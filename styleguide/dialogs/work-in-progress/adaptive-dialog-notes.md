# <a id="top"></a>Changes in dialogs: botbuilder-dotnet

**Branch reviewed**: microsoft/botbuilder-dotnet **4.Future**

Apparent changes:

- Dialog containers
- Adaptive dialogs
- Modified memory model (scoped memory, bypassing? memory management objects)
- Dialog events and event bubbling
- Bindings and skills

Namespaces explored:

- [Microsoft.Bot.Builder.AI.TriggerTrees](#ns-ai-triggertrees)
- [Microsoft.Bot.Builder.Dialogs](#ns-dialogs)
  - [Microsoft.Bot.Builder.Dialogs.Adaptive](#ns-dialogs-adaptive)
    - Microsoft.Bot.Builder.Dialogs.Adaptive.Actions
    - Microsoft.Bot.Builder.Dialogs.Adaptive.Conditions
    - Microsoft.Bot.Builder.Dialogs.Adaptive.Input
    - Microsoft.Bot.Builder.Dialogs.Adaptive.Recognizers
    - Microsoft.Bot.Builder.Dialogs.Adaptive.Selectors
  - Microsoft.Bot.Builder.Dialogs.Composition.Recognizers
  - Microsoft.Bot.Builder.Dialogs.Debugging
  - Microsoft.Bot.Builder.Dialogs.Declarative
    - Microsoft.Bot.Builder.Dialogs.Declarative.Converters
    - Microsoft.Bot.Builder.Dialogs.Declarative.Loaders
    - Microsoft.Bot.Builder.Dialogs.Declarative.Parsers
    - Microsoft.Bot.Builder.Dialogs.Declarative.Plugins
    - Microsoft.Bot.Builder.Dialogs.Declarative.Resolvers
    - Microsoft.Bot.Builder.Dialogs.Declarative.Resources
    - Microsoft.Bot.Builder.Dialogs.Declarative.Types
  - [Microsoft.Bot.Builder.Dialogs.Memory](#ns-dialogs-memory)
    - Microsoft.Bot.Builder.Dialogs.Memory.PathResolvers
    - [Microsoft.Bot.Builder.Dialogs.Memory.Scopes](#ns-dialogs-memory-scopes)
- [Microsoft.Bot.Builder.Expressions](#ns-expressions)
  - [Microsoft.Bot.Builder.Expressions.Parser](#ns-expressions-parser)
- Microsoft.Bot.Builder.LanguageGeneration
  - Microsoft.Bot.Builder.LanguageGeneration.Generators
  - Microsoft.Bot.Builder.LanguageGeneration.Templates

## <a id="ns-ai-triggertrees"></a>[new] Microsoft.Bot.Builder.AI.TriggerTrees

### <a id="Clause"></a>public class **Clause** : [Expression](#Expression)

<details><summary>"interesting" members</summary>

**Last checked**: 10/01

```csharp
private Dictionary<string, string> anyBindings = new Dictionary<string, string>();
internal bool Subsumed = false;

internal Clause() : base(ExpressionType.And) { }
internal Clause(Clause fromClause) : base(ExpressionType.And) {…}
internal Clause(Expression expression) : base(ExpressionType.And, expression) { }
internal Clause(IEnumerable<Expression> children) : base(ExpressionType.And, children.ToArray()) { }

public Dictionary<string, string> AnyBindings { get => anyBindings; set => anyBindings = value; }

public override string ToString() {…}
public void ToString(StringBuilder builder, int indent = 0) {…}

public RelationshipType Relationship(Clause other, Dictionary<string, IPredicateComparer> comparers) {…}
```

</details>

back to [top](#top)

### <a id="RelationshipType"></a>public enum **RelationshipType**

**Last checked**: 10/01

```csharp
{ Specializes, Equal, Generalizes, Incomparable }
```

back to [top](#top)

### <a id="Trigger"></a>public class **Trigger**

**Last checked**: 10/01

> A trigger is a combination of a trigger expression and the corresponding action.

<details><summary>"interesting" members</summary>

```csharp
public Expression OriginalExpression;

private readonly TriggerTree _tree;
private readonly IEnumerable<Quantifier> _quantifiers;
private List<Clause> _clauses;

internal Trigger(TriggerTree tree, Expression expression, object action, params Quantifier[] quantifiers) {…}

public object Action { get; }

public IReadOnlyList<Clause> Clauses => _clauses;

public override string ToString() {…}
protected void ToString(StringBuilder builder, int indent = 0) {…}

public RelationshipType Relationship(Trigger other, Dictionary<string, IPredicateComparer> comparers) {…}
```

</details>

The term _Action_ evokes a sub-dialog or step sequence, but is of type **object**. I'll need to ask about this or locate its use in one of the samples.

back to [top](#top)

### <a id="TriggerTree"></a>public class **TriggerTree**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
// Each trigger is normalized to disjunctive normal form and then expanded with quantifiers.
// Each of those clauses is then put into a DAG where the most restrictive clauses are at the bottom.
// When matching the most specific clauses block out any more general clauses.
//
// Disjunctions and quantification do not change the tree construction, but they are used in determing
// what triggers are returned.  For example, from a strictly logical sense A&B v C&D is more general
// then A&B or C.  If we had these rules:
// R1(A)
// R2(A&B)
// R3(A&BvC&D)
// R4(C)
// Then from a strictly logic viewpoint the tree should be:
//               Root
//     |           |       |
// R3(A&B v C&D)   R1(A) R4(C)
//    |                  /
// R2(A&B)
// The problem is that from the developer standpoint R3 is more of a shortcut for two rules, i.e.A&B and another rule for C&D.
// In the tree above if you had C&D you would get both R3 and R4—which does not seem like what you really want.
// Even though R3 is a disjunction, C&D is more specific than just C.
// The fix is build the tree just based on the conjunctions and then filter triggers on a specific clause so that more specific triggers remove more general ones, i.e. disjunctions.  
// This is what the correspoinding tree looks like:
// Root
//    |                                                   |
// A: R1(A)                                           C: R4(C)
//    |                                                    |
// A&B: R2(A&B), R3(A&BvC&D)                        C&D: R3(A&BvC&D)
// If you had A&B you can look at the triggers and return R2 instead of R3—that seems appropriate.
// But, if you also had C&D at the same time you would still get R3 triggering because of C&D,  I think this is the right thing.
// Even though R3 was filtered out of the A&B branch, it is still the most specific answer in the C&D branch.
// If we remove R3 all together then we would end up returning R4 instead which doesn’t seem correct from the standpoint of
// disjunctions being a shortcut for multiple rules.

/// <summary>
/// A trigger tree organizes evaluators according to generalization/specialization in order to make it easier to use rules.
/// </summary>
/// <remarks>
/// A trigger expression generates true if the expression evaluated on a frame is true.
/// The expression itself consists of arbitrary boolean functions ("predicates") combined with &amp;&amp; || !.
/// Most predicates are expressed over the frame passed in, but they can be anything--there are even ways of optimizing or comparing them.
/// By organizing evaluators into a tree (techinically a DAG) it becomes easier to use rules by reducing the coupling between rules.
/// For example if a rule applies if some predicate A is true, then another rule that applies if A &amp;&amp; B are true is
/// more specialized.  If the second expression is true, then because we know of the relationship we can ignore the first
/// rule--even though its expression is true.  Without this kind of capability in order to add the second rule, you would
/// have to change the first to become A &amp;&amp; !B.
/// </remarks>
```

</details>

back to [top](#top)

## <a id="ns-dialogs"></a>[updated] Microsoft.Bot.Builder.Dialogs

### <a id="ComponentDialog"></a>[updated] public class **ComponentDialog** : [DialogContainer](#DialogContainer)

**Last checked**: 10/01

> A [**Dialog**](#Dialog) that is composed of other dialogs.

<details><summary>"interesting" members</summary>

```csharp
public const string PersistedDialogState = "dialogs";

public ComponentDialog(string dialogId = null) : base(dialogId) {…}

public string InitialDialogId { get; set; }
public new IBotTelemetryClient TelemetryClient { get {…} set {…} }

public override async Task<DialogTurnResult> BeginDialogAsync(DialogContext outerDc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public override async Task<DialogTurnResult> ContinueDialogAsync(DialogContext outerDc, CancellationToken cancellationToken = default(CancellationToken)) {…}
public override async Task<DialogTurnResult> ResumeDialogAsync(DialogContext outerDc, DialogReason reason, object result = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public override async Task RepromptDialogAsync(ITurnContext turnContext, DialogInstance instance, CancellationToken cancellationToken = default(CancellationToken)) {…}
public override async Task EndDialogAsync(ITurnContext turnContext, DialogInstance instance, DialogReason reason, CancellationToken cancellationToken = default(CancellationToken)) {…}

public override Dialog AddDialog(IDialog dialog) {…}

protected async Task EnsureInitialized(DialogContext outerDc) {…}

public override DialogContext CreateChildContext(DialogContext dc) {…}
private DialogContext CreateInnerDc(ITurnContext context, DialogInstance instance) {…}

protected virtual Task OnInitialize(DialogContext dc) {…}
protected virtual Task<DialogTurnResult> OnBeginDialogAsync(DialogContext innerDc, object options, CancellationToken cancellationToken = default(CancellationToken)) {…}
protected virtual Task<DialogTurnResult> OnContinueDialogAsync(DialogContext innerDc, CancellationToken cancellationToken = default(CancellationToken)) {…}
protected virtual Task OnEndDialogAsync(ITurnContext context, DialogInstance instance, DialogReason reason, CancellationToken cancellationToken = default(CancellationToken)) {…}
protected virtual Task OnRepromptDialogAsync(ITurnContext turnContext, DialogInstance instance, CancellationToken cancellationToken = default(CancellationToken)) {…}

protected virtual Task<DialogTurnResult> EndComponentAsync(DialogContext outerDc, object result, CancellationToken cancellationToken) {…}
```

</details>

back to [top](#top)

### <a id="Dialog"></a>[updated] public abstract class **Dialog**

**Last checked**: 10/01

> Base class for all dialogs.

<details><summary>"interesting" members</summary>

```csharp
public static readonly DialogTurnResult EndOfTurn = new DialogTurnResult(DialogTurnStatus.Waiting);

private string id;
private IBotTelemetryClient _telemetryClient;

public Dialog(string dialogId = null) {…}

public string Id {get {…} set {…}}
public virtual IBotTelemetryClient TelemetryClient {get {…} set {…}}
public List<string> Tags { get; private set; } = new List<string>();
public Dictionary<string, string> InputBindings { get; set; } = new Dictionary<string, string>();
public string OutputBinding { get; set; }

public abstract Task<DialogTurnResult> BeginDialogAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken));
public virtual async Task<DialogTurnResult> ContinueDialogAsync(DialogContext dc, CancellationToken cancellationToken = default(CancellationToken)) {…}
public virtual async Task<DialogTurnResult> ResumeDialogAsync(DialogContext dc, DialogReason reason, object result = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public virtual Task RepromptDialogAsync(ITurnContext turnContext, DialogInstance instance, CancellationToken cancellationToken = default(CancellationToken)) {…}
public virtual Task EndDialogAsync(ITurnContext turnContext, DialogInstance instance, DialogReason reason, CancellationToken cancellationToken = default(CancellationToken)) {…}

public virtual async Task<bool> OnDialogEventAsync(DialogContext dc, DialogEvent e, CancellationToken cancellationToken) {…}
protected virtual Task<bool> OnPreBubbleEvent(DialogContext dc, DialogEvent e, CancellationToken cancellationToken) {…}
protected virtual Task<bool> OnPostBubbleEvent(DialogContext dc, DialogEvent e, CancellationToken cancellationToken) {…}

protected virtual string OnComputeId() {…}
protected virtual string BindingPath() {…}

protected void RegisterSourceLocation(string path, int lineNumber) {…}
```

</details>

back to [top](#top)

### <a id="DialogContainer"></a>[new] public abstract class **DialogContainer** : [Dialog](#Dialog)

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
protected readonly DialogSet _dialogs = new DialogSet();

public DialogContainer(string dialogId = null) : base(dialogId) { }

public abstract DialogContext CreateChildContext(DialogContext dc);

public IDialog FindDialog(string dialogId) {…}
```

</details>

**DialogContainer** is now a common base class for [component](#ComponentDialog) and [adaptive](#AdaptiveDialog) dialogs.

- It defines an inner dialog set.
- I'm not sure why it doesn't override the **[Dialog](#Dialog).TelemetryClient** setter to apply the telemetry client to its child dialogs. The implementations in **ComponentDialog** and **AdaptiveDialog**, are nigh-identical ([issue 2638](https://github.com/microsoft/botbuilder-dotnet/issues/2638))

back to [top](#top)

### <a id="DialogContext"></a>[updated] public class **DialogContext**

**Last checked**: 10/01

> Provides context for the current state of the dialog stack.

<details><summary>"interesting" members</summary>

```csharp
public DialogContext(DialogSet dialogs, ITurnContext turnContext, DialogState state) {…}
public DialogContext(DialogSet dialogs, DialogContext parentDialogContext, DialogState state)
    : this(dialogs, parentDialogContext.Context, state) {…}

public DialogSet Dialogs { get; private set; }
public ITurnContext Context { get; private set; }
public IList<DialogInstance> Stack { get; private set; }
public DialogStateManager State { get; private set; }
public DialogContext Parent { get; set; }
public DialogContext Child { get {…} }
public DialogInstance ActiveDialog { get {…} }

public async Task<DialogTurnResult> BeginDialogAsync(string dialogId, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public async Task<DialogTurnResult> PromptAsync(string dialogId, PromptOptions options, CancellationToken cancellationToken = default(CancellationToken)) {…}
public async Task<DialogTurnResult> ContinueDialogAsync(CancellationToken cancellationToken = default(CancellationToken)) {…}
public async Task<DialogTurnResult> EndDialogAsync(object result = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public async Task<DialogTurnResult> CancelAllDialogsAsync(CancellationToken cancellationToken = default(CancellationToken)) {…}
public async Task<DialogTurnResult> CancelAllDialogsAsync(string eventName, object eventValue = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public async Task<DialogTurnResult> ReplaceDialogAsync(string dialogId, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public async Task RepromptDialogAsync(CancellationToken cancellationToken = default(CancellationToken)) {…}

public Dialog FindDialog(string dialogId) {…}
public async Task<bool> EmitEventAsync(string name, object value = null, bool bubble = true, bool fromLeaf = false, CancellationToken cancellationToken = default(CancellationToken)) {…}
private async Task EndActiveDialogAsync(DialogReason reason, object result = null, CancellationToken cancellationToken = default(CancellationToken)) {…}

private IDictionary<string, object> GetActiveDialogState(DialogContext dialogContext, IDictionary<string, object> state = null, int? stackIdx = null) {…}
```

</details>

back to [top](#top)

### <a id="DialogEvent"></a>[new] public class **DialogEvent**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public bool Bubble { get; set; }  // Whether to propagate events to parent contexts.
public string Name { get; set; }  // Event name.
public object Value { get; set; } // Optional. Event value.
```

</details>

### <a id="DialogEvents"></a>[new] public class **DialogEvents**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public const string BeginDialog = "beginDialog";
public const string RepromptDialog = "repromptDialog";
public const string CancelDialog = "cancelDialog";
public const string ActivityReceived = "activityReceived";
public const string Error = "error";
```

</details>

### <a id="DialogInstance"></a>[updated] public class **DialogInstance**

**Last checked**: 10/01

> Contains state information associated with a [**Dialog**](#Dialog) on a dialog stack.

<details><summary>"interesting" members</summary>

```csharp
public string Id { get; set; } // Gets or sets the ID of the dialog.
public IDictionary<string, object> State { get; set; } // Gets or sets the instance's persisted state.
public int? StackIndex { get; set; } // Positive values are indexes within the current DC and negative values are indexes in the parent DC.
```

</details>

- Not yet sure how the **StackIndex** property is being used.

back to [top](#top)

### <a id="DialogManager"></a>[new] public class **DialogManager**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
private DialogSet dialogSet;
private string rootDialogId;

public DialogManager(Dialog rootDialog = null) {…}

public Dialog RootDialog { get {…} set {…} }

public int? ExpireAfter { get; set; }
public IStorage Storage { get; set; }

public static async Task<PersistedState> LoadState(IStorage storage, PersistedStateKeys keys) {…}
public static async Task SaveState(IStorage storage, PersistedStateKeys keys, PersistedState newState, PersistedState oldState = null, string eTag = null) {…}

public static PersistedStateKeys GetKeys(ITurnContext context) {…}
public static PersistedStateKeys GetKeysForReference(ConversationReference reference, string @namespace = null) {…}

public async Task<DialogManagerResult> RunAsync(Activity activity, PersistedState state = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public async Task<DialogManagerResult> OnTurnAsync(ITurnContext context, PersistedState state = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
```

</details>

This stands in for an **IBot**, except that the **OnTurnAsync** method has an additional default argument, `storedState`.

- This re-introduces the concept of a _root dialog_ that was a feature of the v3 library.
- This optionally has a storage layer object it can use for memory management.
  - Uses its own memory schema (unclear how the user and conversation scopes overlap with the user and conversation state management objects).
  - Uses ETags to manage state concurrency.
- Use **OnTurnAsync** "processes" the activity.
  - This takes a turn context and an optional [**PersistedState**](#PersistedState) as input.
  - Gets the storage layer object, if one is set.
  - If `state` is **null**, loads state from the storage layer (else throws).
  - Clones state (to preserve original values).
  - Checks whether the conversation has _expired_. If so, clear conversation state.
  - Creates [**DialogState**](#DialogState) from state and _defines_ these scopes: user, conversation, turn, settings.
  - Creates a **DialogContext** for the turn.
  - _Continues_ the active dialog; else it starts the "root dialog".
  - Sends a **TraceActivity**.
  - If state was loaded from storage (instead of passed in), save state.
  - Returns a [**DialogManagerResult**](#DialogManagerResult) object.
- Use **RunAsync** to "just" _run_ the root dialog and get a result.
  - This takes an **Activity** and an optional [**PersistedState**](#PersistedState) as input.
  - Creates internal and transient [**DialogManagerAdapter**](#DialogManagerAdapter) and **TurnContext** objects.
  - Calls **OnTurnAsync**, adds the array of activities to send (if any) to the [**DialogManagerResult**](#DialogManagerResult) result, and returns the updated result.

back to [top](#top)

### <a id="DialogManagerAdapter"></a>[new] class **DialogManagerAdapter** : BotAdapter

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public DialogManagerAdapter() { }

public readonly List<Activity> Activities = new List<Activity>();

public override Task<ResourceResponse[]> SendActivitiesAsync(ITurnContext turnContext, Activity[] activities, CancellationToken cancellationToken) {…}

// Both of these throw a NotImplementedException.
public override Task<ResourceResponse> UpdateActivityAsync(ITurnContext turnContext, Activity activity, CancellationToken cancellationToken) {…}
public override Task DeleteActivityAsync(ITurnContext turnContext, ConversationReference reference, CancellationToken cancellationToken) {…}
```

</details>

- This appears to be a transient, internal adapter used by the **[DialogManager](#DialogManager).RunAsync** method.
- On _send_ activities operations, caches the activities to send and returns their IDs in the standard **ResourceResponse** array.
- On _update_ and _delete_ activity operations, throws.

back to [top](#top)

### <a id="DialogManagerResult"></a>[new] public class **DialogManagerResult**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public DialogTurnResult TurnResult { get; set; }
public Activity[] Activities { get; set; }
public PersistedState NewState { get; set; }
```

</details>

back to [top](#top)

### <a id="DialogPath"></a>[new] public class **DialogPath**

**Last checked**: 10/01

No members, doesn't appear to be used?!

back to [top](#top)

### <a id="DialogSet"></a>[updated] public class **DialogSet**

**Last checked**: 10/01

> A collection of [**Dialog**](#Dialog) objects that can all call each other.

<details><summary>"interesting" members</summary>

```csharp
public DialogSet(IStatePropertyAccessor<DialogState> dialogState) {…}
public DialogSet() {…}

public IBotTelemetryClient TelemetryClient { get {…} set {…} }

public DialogSet Add(Dialog dialog) {…}

public async Task<DialogContext> CreateContextAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken)) {…}

public Dialog Find(string dialogId) {…}
public IEnumerable<Dialog> GetDialogs() {…}
```

</details>

- An adaptive dialog has both a UserState dictionary and a BotState object (which has both UserState and ConversationState dictionaries). Why?
- An adaptive dialog has a Recognizer "for processing incoming user input".

back to [top](#top)

### <a id="BotState"></a>public class **BotState** : [DialogState](#DialogState)

<details><summary>"interesting" members</summary>

```csharp
public string LastAccess { get; set; }
```

</details>

- This appears to be a stub, as **LastAccess** is not referenced anywhere in the 4.6-Preview SDK.

back to [top](#top)

### <a id="DialogState"></a>[updated] public class **DialogState**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public DialogState() : this(null) { }
public DialogState(IList<DialogInstance> stack) {…}

public IList<DialogInstance> DialogStack { get; set; } = new List<DialogInstance>();
public IDictionary<string, object> ConversationState { get; set; } = new Dictionary<string, object>();
public IDictionary<string, object> UserState { get; set; } = new Dictionary<string, object>();
```

</details>

- The additon of the **ConversationState** and **UserState** properties looks ugly. I'll try to get them to change the names so they don't actively collide with the state management classes of the same name.

### <a id="DialogTurnResult"></a>[updated] public class **DialogTurnResult**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public DialogTurnResult(DialogTurnStatus status, object result = null) {…}

public DialogTurnStatus Status { get; set; }
public object Result { get; set; }
public bool ParentEnded { get; set; }
```

</details>

back to [top](#top)

### <a id="IDialogDependencies"></a>[new] public interface **IDialogDependencies**

**Last checked**: 10/01

> Enumerates child [**Dialog**](#Dialog) dependencies so they can be added to the container's [**DialogSet**](#DialogSet).

<details><summary>"interesting" members</summary>

```csharp
List<Dialog> GetDependencies();
```

</details>

Implementing classes and interfaces:

- **Microsoft.Bot.Builder.Dialogs.Adaptive.Actions.[BaseInvokeDialog](#BaseInvokeDialog)**
- **Microsoft.Bot.Builder.Dialogs.Adaptive.Actions.[EditActions](#EditActions)**
- **Microsoft.Bot.Builder.Dialogs.Adaptive.Actions.[Foreach](#Foreach)**
- **Microsoft.Bot.Builder.Dialogs.Adaptive.Actions.[ForeachPage](#ForeachPage)**
- **Microsoft.Bot.Builder.Dialogs.Adaptive.Actions.[IfCondition](#IfCondition)**
- **Microsoft.Bot.Builder.Dialogs.Adaptive.Actions.[SwitchCondition](#SwitchCondition)**
- **Microsoft.Bot.Builder.Dialogs.Adaptive.Conditions.[OnCondition](#OnCondition)**

back to [top](#top)

### <a id="IItemIdentity"></a>[new] public interface **IItemIdentity**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
string GetIdentity();
```

</details>

Implementing classes:

- **Microsoft.Bot.Builder.Dialogs.Adaptive.Conditions.[OnCondition](#OnCondition)**

back to [top](#top)

### <a id="ObjectPath"></a>[new] public static class **ObjectPath**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
private static JsonSerializerSettings cloneSettings = new JsonSerializerSettings() {…};
private static JsonSerializerSettings expressionCaseSettings = new JsonSerializerSettings {…};

public static bool HasValue(object obj, string pathExpression) {…}
public static T GetPathValue<T>(object obj, string pathExpression) {…}
public static T GetPathValue<T>(object obj, string path, T defaultValue) {…}
public static bool TryGetPathValue<T>(object obj, string pathExpression, out T value) {…}
public static void SetPathValue(object o, string pathExpression, object value, bool json = true) {…}
public static void RemovePathValue(object o, string pathExpression) {…}

public static T Clone<T>(T obj) {…}
public static T Merge<T>(T startObject, T overlayObject) where T : class {…}
public static T Assign<T>(object startObject, object overlayObject) where T : class {…}
public static object Assign(object startObject, object overlayObject, Type type) {…}
```

</details>

- Appears to play a part in reading from and writing to persistable state objects.

back to [top](#top)

### <a id="PersistedState"></a>[new] public class **PersistedState**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public PersistedState() {…}
public PersistedState(PersistedStateKeys keys, IDictionary<string, object> data) {…}

public IDictionary<string, object> UserState { get; set; }
public IDictionary<string, object> ConversationState { get; set; }
```

</details>

back to [top](#top)

### <a id="PersistedStateKeys"></a>[new] public class **PersistedStateKeys** : IEnumerable<string>

**Last checked**: 10/01

> These are the keys which are persisted.

<details><summary>"interesting" members</summary>

```csharp
public string UserState { get; set; }
public string ConversationState { get; set; }

public IEnumerator<string> GetEnumerator() {…}
IEnumerator IEnumerable.GetEnumerator() {…}
```

</details>

See also

- [**DialogManager**](DialogManager): **LoadState**, **SaveState**, **GetKeys**, and **GetKeysForReference** methods.

back to [top](#top)

### <a id="ScopePath"></a>[new] public class **ScopePath**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public const string USER = "user";
public const string CONVERSATION = "conversation";
public const string DIALOG = "dialog";
public const string THIS = "this";
public const string SETTINGS = "settings";
public const string TURN = "turn";
```

</details>

back to [top](#top)

### <a id="ThisPath"></a>[new] public class **ThisPath**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public const string OPTIONS = "this.options";
```

</details>

back to [top](#top)

### <a id="TurnPath"></a>[new] public class **TurnPath**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
public const string LASTRESULT = "turn.lastresult";
public const string ACTIVITY = "turn.activity";
public const string RECOGNIZED = "turn.recognized";
public const string TOPINTENT = "turn.recognized.intent";
public const string TOPSCORE = "turn.recognized.score";
public const string INTERRUPTED = "turn.interrupted";
public const string DIALOGEVENT = "turn.dialogEvent";
public const string REPEATEDIDS = "turn.repeatedIds";
```

</details>

back to [top](#top)

## <a id="ns-dialogs-adaptive"></a>[new] Microsoft.Bot.Builder.Dialogs.Adaptive

### <a id="AdaptiveDialog"></a>public class **AdaptiveDialog** : [DialogContainer](#DialogContainer)

**Last checked**: 10/01

> Models conversation using events to adapt dynamically to changing conversation flow.

<details><summary>"interesting" members</summary>

```csharp
public AdaptiveDialog(string dialogId = null, [CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0)
    : base(dialogId) {…}

public IStatePropertyAccessor<BotState> BotState { get; set; }
public IStatePropertyAccessor<Dictionary<string, object>> UserState { get; set; }

public IRecognizer Recognizer { get; set; }
public ILanguageGenerator Generator { get; set; }

public virtual List<OnCondition> Triggers { get; set; } = new List<OnCondition>();
public bool AutoEndDialog { get; set; } = true;
public ITriggerSelector Selector { get; set; }
public string DefaultResultProperty { get; set; } = "dialog.result";

public DialogSet Dialogs => this._dialogs;

public override IBotTelemetryClient TelemetryClient { get {…} set {…} }

public override async Task<DialogTurnResult> BeginDialogAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public override async Task<DialogTurnResult> ContinueDialogAsync(DialogContext dc, CancellationToken cancellationToken = default(CancellationToken)) {…}
public override async Task<DialogTurnResult> ResumeDialogAsync(DialogContext dc, DialogReason reason, object result = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
public override async Task RepromptDialogAsync(ITurnContext turnContext, DialogInstance instance, CancellationToken cancellationToken = default(CancellationToken)) {…}

public override DialogContext CreateChildContext(DialogContext dc) {…}

protected override async Task<bool> OnPreBubbleEvent(DialogContext dc, DialogEvent dialogEvent, CancellationToken cancellationToken = default(CancellationToken)) {…}
protected override async Task<bool> OnPostBubbleEvent(DialogContext dc, DialogEvent dialogEvent, CancellationToken cancellationToken = default(CancellationToken)) {…}
protected async Task<bool> ProcessEventAsync(SequenceContext sequenceContext, DialogEvent dialogEvent, bool preBubble, CancellationToken cancellationToken = default(CancellationToken)) {…}

protected override string OnComputeId() {…}

protected async Task<DialogTurnResult> ContinueActionsAsync(DialogContext dc, object options, CancellationToken cancellationToken) {…}
protected Task<bool> EndCurrentActionAsync(SequenceContext sequenceContext, CancellationToken cancellationToken = default(CancellationToken)) {…}
protected async Task<DialogTurnResult> OnEndOfActionsAsync(SequenceContext sequenceContext, CancellationToken cancellationToken = default(CancellationToken)) {…}
protected async Task<RecognizerResult> OnRecognize(SequenceContext sequenceContext, CancellationToken cancellationToken = default(CancellationToken)) {…}
```

</details>

- An adaptive dialog has properties for **UserState** and **BotState** (which itself has **UserState** and **ConversationState** properties). Why?
- An adaptive dialog has a Recognizer ([IRecognizer](#IRecognizer)) "for processing incoming user input".

back to [top](#top)

### <a id="AdaptiveEvents"></a>public class **AdaptiveEvents** : [DialogContext](#DialogContext).DialogEvents

<details><summary>"interesting" members</summary>

```csharp
public const string RecognizedIntent = "recognizedIntent";
public const string UnknownIntent = "unknownIntent";
public const string SequenceStarted = "stepsStarted";
public const string SequenceEnded = "stepsEnded";
```

</details>

back to [top](#top)

### <a id="BotState"></a>public class **BotState** : [DialogState](#DialogState)

<details><summary>"interesting" members</summary>

```csharp
public string LastAccess { get; set; }
```

</details>

- This appears to be a stub, as **LastAccess** is not referenced anywhere in the 4.6-Preview SDK.

back to [top](#top)

### <a id="SequenceContext"></a>public class **SequenceContext** : [DialogContext](#DialogContext)

<details><summary>"interesting" members</summary>

```csharp
public AdaptiveDialogState Plans { get; private set; }

public List<StepState> Steps { get; set; }

public List<StepChangeList> Changes { get {…} private set {…} }

public SequenceContext(DialogSet dialogs, DialogContext dc, DialogState state, List<StepState> steps, string changeKey, DialogSet stepDialogs)
    : base(dialogs, dc.Context, state, conversationState: dc.State.Conversation, userState: dc.State.User, settings: dc.State.Settings) {…}

public void QueueChanges(StepChangeList changes) {…}

public async Task<bool> ApplyChangesAsync(CancellationToken cancellationToken = default(CancellationToken)) {…}

public SequenceContext InsertSteps(List<StepState> steps) {…}
public SequenceContext InsertStepsBeforeTags(List<string> tags, List<StepState> steps) {…}
public SequenceContext AppendSteps(List<StepState> steps) {…}

public SequenceContext EndSequence(List<StepState> steps) {…}
public SequenceContext ReplaceSequence(List<StepState> steps) {…}

protected override bool ShouldInheritState(IDialog dialog) {…}
```

</details>

back to [top](#top)

### <a id="StepChangeTypes"></a>public enum **StepChangeTypes**

<details><summary>"interesting" members</summary>

```csharp
public enum StepChangeTypes
{
    InsertSteps,
    InsertStepsBeforeTags,
    AppendSteps,
    EndSequence,
    ReplaceSequence,
}
```

</details>

back to [top](#top)

### <a id="StepChangeList"></a>public class **StepChangeList**

<details><summary>"interesting" members</summary>

```csharp
public StepChangeTypes ChangeType { get; set; } = StepChangeTypes.InsertSteps;
public List<StepState> Steps { get; set; } = new List<StepState>();
public List<string> Tags { get; set; } = new List<string>();
public Dictionary<string, object> Turn { get; set; }
```

</details>

back to [top](#top)

### <a id="StepState"></a>public class **StepState** : [DialogState](#DialogState)

<details><summary>"interesting" members</summary>

```csharp
public AdaptiveDialogState() { }

public dynamic Options { get; set; }
public List<StepState> Steps { get; set; } = new List<StepState>();
public object Result { get; set; }
```

</details>

- Not sure if the use of `dynamic` here is a bug. The SDK has actively avoided the use of `dynamic` up to this point.

back to [top](#top)

## <a id="ns-dialogs-adaptive-steps"></a>~~[new] Microsoft.Bot.Builder.Dialogs.Adaptive.Steps~~

Looks like this namespace was renamed to **Microsoft.Bot.Builder.Dialogs.Adaptive.Actions**.

### <a id="BaseInvokeDialog"></a>public abstract class **BaseInvokeDialog** : [DialogCommand](#DialogCommand)

<details><summary>"interesting" members</summary>

```csharp
protected string dialogIdToCall;

public object Options { get; set; }

public IDialog Dialog { get; set; }
public string Property { get {…} set {…} }

public BaseInvokeDialog(string dialogIdToCall = null, string property = null, object options = null) : base() {…}

public override List<IDialog> ListDependencies() {…}

protected override string OnComputeId() {…}

protected IDialog ResolveDialog(DialogContext dc) {…}

protected void BindOptions(DialogContext dc) {…}
```

</details>

- Represents a step that calls/invokes another dialog.
- The **Property** property sets up a binding between the parent and child contexts. It is used to set the initial/input value in the child, and the final/output value in the parent.

Derived classes:

- **Microsoft.Bot.Builder.Dialogs.Adaptive.Steps.[BeginDialog](#BeginDialog)**
- **Microsoft.Bot.Builder.Dialogs.Adaptive.Steps.[ReplaceDialog](#ReplaceDialog)**

back to [top](#top)

### <a id="BeginDialog"></a>public class **BeginDialog** : [BaseInvokeDialog](#BaseInvokeDialog)

<details><summary>"interesting" members</summary>

```csharp
public BeginDialog(string dialogIdToCall = null, string property = null, object options = null, [CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0)
    : base(dialogIdToCall, property, options) {…}

protected async override Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
```

</details>

back to [top](#top)

### <a id="CancelAllDialogs"></a>public class **CancelAllDialogs** : [DialogCommand](#DialogCommand)

<details><summary>"interesting" members</summary>

```csharp
public CancelAllDialogs([CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0) : base() {…}

public string EventName { get; set; }
public string EventValue { get; set; }

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}

protected override string OnComputeId() {…}
```

</details>

- When _run_, calls **[DialogCommand](#DialogCommand).CancelAllParentDialogsAsync** with `eventName: EventName ?? "cancelDialog"` and `eventValue: EventValue`.

back to [top](#top)

### <a id="CodeStep"></a>public class **CodeStep** : [DialogCommand](#DialogCommand)

<details><summary>"interesting" members</summary>

```csharp
public CodeStep(CodeStepHandler codeHandler, [CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0) : base() {…}

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}

protected override string OnComputeId() {…}
```

</details>

- When _run_, runs a _code handler_.

    ```csharp
    using CodeStepHandler = Func<DialogContext, object, Task<DialogTurnResult>>;
    ```

back to [top](#top)

### <a id="DebugBreak"></a>public class **DebugBreak** : [DialogCommand](#DialogCommand)

<details><summary>"interesting" members</summary>

```csharp
public DebugBreak([CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0) {…}

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
```

</details>

- When _run_ and there's an attached **System.Diagnostics.Debugger**, calls **Debugger.Break**.

back to [top](#top)

### <a id="DeleteProperty"></a>public class **DeleteProperty** : [DialogCommand](#DialogCommand)

<details><summary>"interesting" members</summary>

```csharp
public string Property { get; set; }

public DeleteProperty([CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0) : base() {…}
public DeleteProperty(string property, [CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0) : base() {…}

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
```

</details>

- When _run_ and our dialog context is a [**SequenceContext**](#SequenceContext), deletes a property from DialogManager-style state.

back to [top](#top)

### <a id="EditArray"></a>public class **EditArray** : [DialogCommand](#DialogCommand)

<details><summary>"interesting" members</summary>

```csharp
public enum ArrayChangeType { Push, Pop, Take, Remove, Clear }

public EditArray([CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0) : base() {…}

protected override string OnComputeId() {…}

public ArrayChangeType ChangeType { get; set; }
public string ArrayProperty { get {…} set {…} }
public string ResultProperty { get {…} set {…} }
public string Value { get {…} set {…} }

public EditArray(ArrayChangeType changeType, string arrayProperty = null, string value = null, string resultProperty = null) : base() {…}

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
```

</details>

The _array_ [property] we're working with is a **Newtonsoft.Json.Linq.JArray**.

- Pop removes and returns a value from the end.
- Push adds a value to the end.
- Take removes and returns a value from the start.
- Remove finds the first instance of a value in the array and removes it. Returns true if an item was removed.
- Clear empties the array. Returns true if any items were removed.

Push and pop describe a stack, and push and take describe a queue. Not sure why more array operations are not implemented, but they are easy to add.

back to [top](#top)

### <a id="EditSteps"></a>public class **EditSteps** : [DialogCommand](#DialogCommand), [IDialogDependencies](#IDialogDependencies)

<details><summary>"interesting" members</summary>

```csharp
public List<IDialog> Steps { get; set; } = new List<IDialog>();

public StepChangeTypes ChangeType { get; set; }

public EditSteps([CallerFilePath] string sourceFilePath = "", [CallerLineNumber] int sourceLineNumber = 0) : base() {…}

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}

protected override string OnComputeId() {…}

public override List<IDialog> ListDependencies() {…}
```

</details>

- When _run_ and our dialog context is a [**SequenceContext**](#SequenceContext), queues the changes [on the sequence context] and then ends itself.
- Not sure when these queued changes get applied.

back to [top](#top)

### <a id="EmitEvent"></a>public class **EmitEvent** : [DialogCommand](#DialogCommand)

<details><summary>"interesting" members</summary>

```csharp
public string EventName { get; set; }
public object EventValue { get; set; }
public bool BubbleEvent { get; set; }
public string EventValueProperty { get {…} set {…} }

public EmitEvent(string eventName = null, object eventValue = null, bool bubble = true, [CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0)
    : base() {…}

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}

protected override string OnComputeId() {…}
```

</details>

- When _run_, "emits" the event. I guess this lets a child dialog notify ancestors.

back to [top](#top)

### <a id="EndDialog"></a>public class **EndDialog** : [DialogCommand](#DialogCommand)

<details><summary>"interesting" members</summary>

```csharp
public string ResultProperty { get; set; } = "dialog.result";

public EndDialog(string resultProperty = null, [CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0) : base() {…}

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}

protected override string OnComputeId() {…}
```

</details>

- When _run_, ends the nearest ancestor dialog that is not a command dialog and returns whatever result is currently saved to the _dialog.result_ property.

back to [top](#top)

### <a id="EndTurn"></a>public class **EndTurn** : [DialogCommand](#DialogCommand)

<details><summary>"interesting" members</summary>

```csharp
public EndTurn([CallerFilePath] string callerPath = "", [CallerLineNumber] int callerLine = 0) : base() {…}

protected override string OnComputeId() {…}

protected async override Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}
```

</details>

- When _run_, simply ends the turn without ending the dialog. Has no side effects. Equivalent to a old-school waterfall step returning **[Dialog](#Dialog).EndOfTurn**.

back to [top](#top)

### <a id="Foreach"></a>public class **Foreach** : [DialogCommand](#DialogCommand), [IDialogDependencies](#IDialogDependencies)

<details><summary>"interesting" members</summary>

```csharp
public string ListProperty { get {…} set {…} }
public string IndexProperty { get; set; } = "dialog.index";
public string ValueProperty { get; set; } = DialogContextState.DIALOG_VALUE;
public List<IDialog> Steps { get; set; } = new List<IDialog>();

public Foreach([CallerFilePath] string sourceFilePath = "", [CallerLineNumber] int sourceLineNumber = 0) : base() {…}

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}

protected override string OnComputeId() {…}

public override List<IDialog> ListDependencies() {…}

public class ForeachOptions
{
    public Expression list { get; set; }
    public int offset { get; set; }
}
```

</details>

- When _run_ and our dialog context is a [**SequenceContext**](#SequenceContext):
  - Appears to copy the steps, add one to increment the loop offset, "queue" the steps to be added, and then end itself.
  - Not sure where the missing logic is that makes this an actual loop or checks whether to continue.
  - The **ListProperty** seems to contain some meaningful expression the [**ExpressionEngine**](#ExpressionEngine) can parse.

back to [top](#top)

### <a id="ForeachPage"></a>public class **ForeachPage** : [DialogCommand](#DialogCommand), [IDialogDependencies](#IDialogDependencies)

<details><summary>"interesting" members</summary>

```csharp
public string ListProperty { get {…} set {…} }
public int PageSize { get; set; } = 10;
public string ValueProperty { get; set; } = DialogContextState.DIALOG_VALUE;
public List<IDialog> Steps { get; set; } = new List<IDialog>();

public ForeachPage([CallerFilePath] string sourceFilePath = "", [CallerLineNumber] int sourceLineNumber = 0) : base() {…}

protected override async Task<DialogTurnResult> OnRunCommandAsync(DialogContext dc, object options = null, CancellationToken cancellationToken = default(CancellationToken)) {…}

protected override string OnComputeId() {…}

public override List<IDialog> ListDependencies() {…}

public class ForeachPageOptions
{
    public Expression list { get; set; }
    public int offset { get; set; }
    public int pageSize { get; set; }
}
```

</details>

- When _run_ and our dialog context is a [**SequenceContext**](#SequenceContext):
  - Appears to copy the steps, add **pageSize** to increment the loop offset, "queue" the steps to be added, and then end itself.
  - Appears to work on a _page_ of values at a time?
  - Not sure where the missing logic is that makes this an actual loop or checks whether to continue.
  - The **ListProperty** seems to contain some meaningful expression the [**ExpressionEngine**](#ExpressionEngine) can parse.

back to [top](#top)

## <a id="ns-dialogs-memory"></a>[new] Microsoft.Bot.Builder.Dialogs.Memory

### <a id="DialogStateManager"></a>public class **DialogStateManager** : IDictionary<string, object>

**Last checked**: 10/01

> The DialogStateManager manages memory scopes and path resolvers.
> Memory scopes are named root level objects, which can exist either in the [**Dialogcontext**](#Dialogcontext) or off of turn state.
> Path resolvers allow for shortcut behavior for mapping things like `$foo -> dialog.foo`.

<details><summary>"interesting" members</summary>

```csharp
private readonly DialogContext dialogContext;

public DialogStateManager(DialogContext dc) {…}

public static List<IPathResolver> PathResolvers { get; private set; } = new List<IPathResolver>() {…};
public static List<MemoryScope> MemoryScopes { get; private set; } = new List<MemoryScope>() {…};

public ICollection<string> Keys => MemoryScopes.Select(ms => ms.Name).ToList();
public ICollection<object> Values => MemoryScopes.Select(ms => ms.GetMemory(this.dialogContext)).ToList();
public int Count => MemoryScopes.Count;
public bool IsReadOnly => true;
public object this[string key] { get {…} set {…} }

public static MemoryScope GetMemoryScope(string name) {…}
public virtual MemoryScope ResolveMemoryScope(ref string path) {…}

public virtual string TransformPath(string path) {…}

public bool TryGetValue<T>(string path, out T value) {…}
public T GetValue<T>(string pathExpression, Func<T> defaultValue = null) {…}
public int GetIntValue(string pathExpression, int defaultValue = 0) {…}
public bool GetBoolValue(string pathExpression, bool defaultValue = false) {…}
public void SetValue(string path, object value) {…}
public void RemoveValue(string path) {…}

public JObject GetMemorySnapshot() {…}

public void Add(string key, object value) {…}
public void Add(KeyValuePair<string, object> item) {…}
public bool ContainsKey(string key) {…}
public bool Contains(KeyValuePair<string, object> item) {…}
public bool TryGetValue(string key, out object value) {…}
public bool Remove(string key) {…}
public bool Remove(KeyValuePair<string, object> item) {…}
public void Clear() {…}
public void CopyTo(KeyValuePair<string, object>[] array, int arrayIndex) {…}

public IEnumerator<KeyValuePair<string, object>> GetEnumerator() {…}
IEnumerator IEnumerable.GetEnumerator() {…}
```

</details>

back to [top](#top)

### <a id="IPathResolver"></a>public interface **IPathResolver**

**Last checked**: 10/01

<details><summary>"interesting" members</summary>

```csharp
string TransformPath(string path);
```

</details>

back to [top](#top)

## <a id="ns-dialogs-memory-scopes"></a>[new] Microsoft.Bot.Builder.Dialogs.Memory.Scopes

### <a id="DialogMemoryScope"></a>public class **DialogMemoryScope** : [MemoryScope](#MemoryScope)

**Last checked**: 10/01

> **DialogMemoryScope** maps "dialog" -> `dc.Parent?.ActiveDialog.State ?? ActiveDialog.State`.

<details><summary>"interesting" members</summary>

```csharp
public DialogMemoryScope() : base(ScopePath.DIALOG) {…}

public override object GetMemory(DialogContext dc) {…}
public override void SetMemory(DialogContext dc, object memory) {…}
```

</details>

back to [top](#top)

### <a id="MemoryScope"></a>public class **MemoryScope**

**Last checked**: 10/01

> **MemoryScope** represents a named memory scope stored in turn state under "MemoryScopes".

<details><summary>"interesting" members</summary>

```csharp
private const string MEMORYSCOPESKEY = "MemoryScopes";

public MemoryScope(string name) {…}

public string Name { get; set; }

public virtual object GetMemory(DialogContext dc) {…}
public virtual void SetMemory(DialogContext dc, object memory) {…}

internal static Dictionary<string, object> GetScopesMemory(ITurnContext context) {…}
```

</details>

Derived classes:

- Microsoft.Bot.Builder.Dialogs.Memory.Scopes.[DialogMemoryScope](#DialogMemoryScope)
- Microsoft.Bot.Builder.Dialogs.Memory.Scopes.[ThisMemoryScope](#ThisMemoryScope)

back to [top](#top)

### <a id="ThisMemoryScope"></a>public class **ThisMemoryScope** : [MemoryScope](#MemoryScope)

**Last checked**: 10/01

> **ThisMemoryScope** maps "dialog" -> `dc.ActiveDialog.State`.

<details><summary>"interesting" members</summary>

```csharp
public ThisMemoryScope() : base(ScopePath.THIS) {…}

public override object GetMemory(DialogContext dc) {…}
public override void SetMemory(DialogContext dc, object memory) {…}
```

</details>

back to [top](#top)

## <a id="ns-expressions"></a>[new] Microsoft.Bot.Builder.Expressions

### <a id="Expression"></a>public class **Expression**

<details><summary>"interesting" members</summary>

```csharp
public Expression(string type, params Expression[] children) {…}
public Expression(ExpressionEvaluator evaluator, params Expression[] children) {…}

public string Type => Evaluator.Type;
public ExpressionEvaluator Evaluator { get; }

public Expression[] Children { get; set; }
public ReturnType ReturnType => Evaluator.ReturnType;

public static Expression MakeExpression(string type, params Expression[] children) {…}
public static Expression MakeExpression(ExpressionEvaluator evaluator, params Expression[] children) {…}

public static Expression LambaExpression(EvaluateExpressionDelegate function) => new Expression(new ExpressionEvaluator(ExpressionType.Lambda, function));
public static Expression Lambda(Func<object, object> function) => new Expression(new ExpressionEvaluator(ExpressionType.Lambda, (expression, state) => {…}));
public static Expression SetPathToValue(Expression property, Expression value) => Expression.MakeExpression(ExpressionType.SetPathToValue, property, value);
public static Expression SetPathToValue(Expression property, object value) {…}
public static Expression EqualsExpression(params Expression[] children) => Expression.MakeExpression(ExpressionType.Equal, children);
public static Expression AndExpression(params Expression[] children) {…}
public static Expression OrExpression(params Expression[] children) {…}
public static Expression NotExpression(Expression child) => Expression.MakeExpression(ExpressionType.Not, child);
public static Expression ConstantExpression(object value) => new Constant(value);
public static Expression Accessor(string property, Expression instance = null) => …;

public void Validate() => Evaluator.ValidateExpression(this);
public void ValidateTree() {…}

public (object value, string error) TryEvaluate(object state) => Evaluator.TryEvaluate(this, state);

public override string ToString() {…}
```

</details>

- Relies on an [**ExpressionEvaluator**](#ExpressionEvaluator) to...evaluate the expression.

Derived classes:

- **Microsoft.Bot.Builder.AI.TriggerTrees.[Clause](#Clause)**
- **Microsoft.Bot.Builder.Expressions.[Constant](#Constant)**

back to [top](#top)

### <a id="Constant"></a>public class **Constant** : [Expression](#Expression)

<details><summary>"interesting" members</summary>

```csharp
private object _value;

public Constant(object value = null)
    : base(new ExpressionEvaluator(ExpressionType.Constant, (expression, state) => ((expression as Constant).Value, null))) {…}

public object Value { get {…} set {…} }

public override string ToString() {…}
```

</details>

back to [top](#top)

### <a id="ReturnType"></a>public enum **ReturnType**

<details><summary>"interesting" members</summary>

```csharp
Boolean, Number, Object, String
```

</details>

back to [top](#top)

### <a id="ExpressionEvaluator"></a>public class **ExpressionEvaluator**

<details><summary>"interesting" members</summary>

```csharp
private readonly ValidateExpressionDelegate _validator;
private readonly EvaluateExpressionDelegate _evaluator;

public ExpressionEvaluator(string type, EvaluateExpressionDelegate evaluator, ReturnType returnType = ReturnType.Object, ValidateExpressionDelegate validator = null) {…}

public string Type { get; }
public ReturnType ReturnType { get; set; }

public ExpressionEvaluator Negation { get {…} set {…} }

public override string ToString() => $"{Type} => {ReturnType}";

public (object value, string error) TryEvaluate(Expression expression, object state) => _evaluator(expression, state);
public void ValidateExpression(Expression expression) => _validator(expression);
```

</details>

back to [top](#top)

### <a id="ValidateExpressionDelegate"></a>public delegate void **ValidateExpressionDelegate**([Expression](#Expression) expression)

back to [top](#top)

### <a id="EvaluateExpressionDelegate"></a>public delegate (object value, string error) **EvaluateExpressionDelegate**([Expression](#Expression) expression, object state)

back to [top](#top)

## <a id="ns-expressions-parser"></a>[new] Microsoft.Bot.Builder.Expressions.Parser

### <a id="IExpressionParser"></a>public interface **IExpressionParser**

<details><summary>"interesting" members</summary>

```csharp
Expression Parse(string expression);
```

</details>

Implemented by:

- **Microsoft.Bot.Builder.Expressions.Parser.[ExpressionEngine](#ExpressionEngine)**

back to [top](#top)

### <a id="ExpressionEngine"></a>public class **ExpressionEngine** : [IExpressionParser](#IExpressionParser)

<details><summary>"interesting" members</summary>

```csharp
public ExpressionEngine(EvaluatorLookup lookup = null) {…}

public Expression Parse(string expression) => new ExpressionTransformer(_lookup).Transform(AntlrParse(expression));

protected static IParseTree AntlrParse(string expression) {…}
```

</details>

- **ExpressionTransformer** is a private inner class that encapsulates much of the parsing logic.
- Looks like they used [ANTLR](https://www.antlr.org/) along with the **Expression.g4** file to generate the core parser.

back to [top](#top)
