<!-- Oauth 2 generic provider settings -->
<!-- Fixed ID -->

| **Property** | **Description** | **Value** |
|---|---|---|
|**Name** | The name of your connection | \<Your name for the connection\> <img width="300px">|
| **Service Provider**| Identity provider | From the drop-down list, select **Oauth 2 Generic Provider** |
|**Client ID** | Identity provider app ID| \<provider ID\> |
|**Client secret** | Identity provider app secret| <provider secret\> |
|*Scope List Delimiter*|The character to use between scope values (often a space or comma) | *,* \<enter comma\> |
|**Authorization URL Template** || https://login.microsoftonline.com/common/oauth2/v2.0/authorize |
|*Authorization URL Query String* |The query string to append to the authorization URL,templated with any wanted parameters: {ClientId} {ClientSecret} {RedirectUrl} {Scopes} {State}| *?client_id={ClientId}&response_type=code&redirect_uri={RedirectUrl}&scope={Scopes}&state={State}* |
|**Token URL Template** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Token URL Query String Template* | Body to send for the token exchange |*?* \<enter question mark\>|
|*Token Body Template* | Body to send for the token exchange | *code={Code}&grant_type=authorization_code&redirect_uri={RedirectUrl}&client_id={ClientId}&client_secret={ClientSecret}* |
|**Refresh URL Template** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Refresh URL Query String Template* |The query string to append to the refresh URL,templated with any wanted parameters: {ClientId} {ClientSecret} {RedirectUrl} {Scopes} {State} |*?* \<enter question mark\>|
|*Refresh Body Template* | Body to send with the token refresh | *refresh_token={RefreshToken}&redirect_uri={RedirectUrl}&grant_type=refresh_token&client_id={ClientId}&client_secret={ClientSecret}* |
|**Scopes** | Comma separated list of the API permissions you granted earlier to the Azure AD authentication app | Values such as `openid` `profile` `Mail.Read` `Mail.Send` `User.Read` `User.ReadBasic.All`|
