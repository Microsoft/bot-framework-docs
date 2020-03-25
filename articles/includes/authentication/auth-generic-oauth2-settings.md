<!-- Generic Oauth2 provider settings -->
<!-- Fixed ID -->

| **Property** | **Description** | **Value** |
|---|---|---|
|**Name** | The name of your connection | \<Your name for the connection\> <img width="300px">|
| **Service Provider**| Identity provider | From the drop-down list, select **Generic Oauth 2** |
|**Client ID** | Identity provider app ID| \<provider ID\> |
|**Client secret** | Identity provider app secret| <provider secret\> |
|**Authorization URL** | | https://login.microsoftonline.com/common/oauth2/v2.0/authorize |
|*Authorization URL Query String* | | *?client_id={ClientId}&response_type=code&redirect_uri={RedirectUrl}&scope={Scopes}&state={State}* |
|**Token URL** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Token Body* | Body to send for the token exchange | *code={Code}&grant_type=authorization_code&redirect_uri={RedirectUrl}&client_id={ClientId}&client_secret={ClientSecret}* |
|**Refresh URL** | | https://login.microsoftonline.com/common/oauth2/v2.0/token |
|*Refresh Body Template* | Body to send with the token refresh | *refresh_token={RefreshToken}&redirect_uri={RedirectUrl}&grant_type=refresh_token&client_id={ClientId}&client_secret={ClientSecret}* |
|**Scopes** | Comma separated list of the API permissions you granted earlier to the Azure AD authentication app | Values such as `openid` `profile` `Mail.Read` `Mail.Send` `User.Read` `User.ReadBasic.All`|
