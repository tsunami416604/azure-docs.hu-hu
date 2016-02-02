<properties
    pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
    description="使用 Azure AD 的 OpenID Connect 驗證通訊協定實作來建置 Web 應用程式。"
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2015"
    ms.author="dastrock"/>


# Azure AD B2C 預覽：使用 OpenID Connect 的 Web 登入

OpenID Connect 是建置在 OAuth 2.0 的驗證通訊協定之上，可用來將使用者安全地登入 Web 應用程式。 使用 OpenID connect 的 Azure AD B2C 的實作，您可以將外包註冊、 登入
和 web 應用程式至 Azure AD 中的其他身分識別管理體驗。 本指南以不考慮語言的方式來示範如何這樣做，描述在不使用我們的任何開放原始碼程式庫的情況下，如何傳送和接收 HTTP 訊息。


[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) 擴充 OAuth 2.0 *授權* 作為通訊協定 *驗證* 通訊協定，可讓您執行單一登入使用 OAuth。 它會介紹的概念 `id_token`, ，這是可讓用戶端驗證之使用者的身分識別並取得使用者的基本設定檔資訊的安全性權杖。 由於它會擴充 OAuth 2.0，它也可讓應用程式安全地取得 **access_tokens** 這可以用來存取受保護的資源 [授權伺服器](active-directory-b2c-reference-protocols.md#the-basics)。 如果您要建置的 Web 應用程式是裝載於伺服器且透過瀏覽器存取，建議使用 OpenID Connect。 如果您想要新增至您的行動裝置的身分識別管理，或使用 Azure AD B2C 桌面應用程式，您應該使用 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) 而不是 OpenID Connect。

Azure AD B2C 擴充標準的 OpenID Connect 通訊協定，功能更強大，而不僅止於簡單的驗證和授權。 它引入了 [* * 原則參數 * *](active-directory-b2c-reference-poliices.md),， 
可讓您將使用 OpenID Connect 新增使用者，例如發生您的應用程式註冊，登入和管理設定檔。 這裡我們將說明如何使用 OpenID Connect 和原則來實作每個這些經驗 
在您的 web 應用程式和 get access_tokens 來存取 web Api。

將以下的範例 HTTP 要求使用我們的範例 B2C 目錄 **fabrikamb2c.onmicrosoft.com**, ，我們的範例應用程式以及 **https://aadb2cplayground.azurewebsites.net** 和原則。 您可以隨意使用這些值來自行嘗試要求，也可以換成您自己的值。
了解如何 [取得自己的 B2C 目錄、 應用程式，以及原則](#use-your-own-b2c-directory)。

## 傳送驗證要求

當您的 web 應用程式需要驗證使用者，並執行原則時，它可以將使用者導向至 `/ 授權` 端點。 這是流程的互動部分，使用者將會根據原則而實際地採取動作。  
在此要求，用戶端表示所需權限的方式從使用者取得 `範圍` 參數，以執行原則 `p` 參數。 以下提供三個範例 (具有分行符號的可讀性)，
每個使用不同的原則。 為了瞭解每個要求的運作方式，請試著將要求貼到瀏覽器來執行。

#### 使用登入原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### 使用註冊原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### 使用編輯設定檔原則

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 參數| | 說明|
| ----------------------- | ------------------------------- | ----------------------- |
| client_id| 必要| 應用程式識別碼， [Azure 入口網站](https://portal.azure.com) 指派您的應用程式。|
| response_type| 必要| 必須包含 `id_token` OpenID connect。如果您的 web 應用程式也會需要將語彙基元來呼叫 web API，您可以使用 `碼 + id_token`, ，因為我們的做法。|
| redirect_uri| 必要| 您的應用程式可在應用程式的 redirect_uri 傳送及接收驗證回應。其必須完全符合您在入口網站中註冊的其中一個 redirect_uris，不然就必須得是編碼的 url。|
| scope| 必要| 範圍的空格分隔清單。單一範圍值向 Azure AD 表示同時要求的兩個權限。 `Openid` 範圍表示與的權限登入的使用者，並取得使用者的相關資料的形式 **id_tokens** (敬請期待此)。 `Offline_access` 範圍都是選擇性的 web 應用程式。它表示您的應用程式將需要 **refresh_token**，才能長久存取資源。|
| response_mode| 建議使用| 指定將產生的 authorization_code 傳回到應用程式所應該使用的方法。可以是 'query'、'form_post' 或 'fragment' 其中一種。|
| state| 建議使用| 同樣會隨權杖回應傳回之要求中所包含的值。其可以是您想要之任何內容的字串。隨機產生的唯一值通常用於防止跨站台要求偽造攻擊。驗證要求出現前，也會先使用此狀態為使用者在應用程式中的狀態資訊編碼，例如他們先前所在的網頁。|
| nonce| 必要| 由應用程式產生且包含在要求中的值，會以宣告方式包含在產生的 id_token 中。應用程式接著便可確認此值，以減少權杖重新執行攻擊。此值通常是隨機的唯一字串，可用以識別要求的來源。|
| p| 必要| 指出要執行的原則。它是 B2C 目錄中建立的原則名稱，值應該以 "b2c_1_" 開頭。深入了解原則 [這裡](active-directory-b2c-reference-policies.md)。|
| prompt| 選用| 表示需要的使用者互動類型。此時唯一有效的值是 'login'，強制使用者針對該要求輸入其認證。單一登入不會生效。|

此時會要求使用者完成原則的工作流程。 這可能需要使用者輸入其使用者名稱和密碼、以社交身分識別登入、註冊目錄，或任意數量的步驟 (視原則如何定義而定)。
當使用者完成原則時，Azure AD 會傳回在指定的應用程式的回應 `redirect_uri`, ，在指定的方法 `response_mode` 參數。 在上述各種情況下，回應完全相同，與已執行的原則無關。

成功的回應，使用 `response_mode = 查詢` 應該像這樣:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數| 說明|
| ----------------------- | ------------------------------- |
| id_token| 應用程式要求的 id_token。您可以使用 id_token 確認使用者的身分識別，並以使用者開始工作階段。需 id_tokens 和其內容的詳細資訊包含在 [Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md)。|
| code| 應用程式要求，如果您使用 authorization_code `response_type = code + id_token`。應用程式可以使用授權碼要求目標資源的存取權杖。Authorization_code 的有效期很短，通常約 10 分鐘後即到期。|
| state| 如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該確認要求和回應中的狀態值完全相同。|

錯誤回應可能也會傳送至 `redirect_uri` ，應用程式可以適當地處理:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 參數| 說明|
| ----------------------- | ------------------------------- |
| 錯誤| 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。|
| error_description| 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。|
| state| 如果要求中包含狀態參數，回應中就應該出現相同的值。應用程式應該檢查要求和回應中的狀態值相同。|


## 驗證 id_token

僅接收 id_token 不足以驗證使用者，您必須驗證 id_token 簽章，並依照應用程式的需求確認權杖中的宣告。 使用 azure AD B2C [JSON Web 權杖 (Jwt)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公開金鑰密碼編譯來簽署權杖，並確認它們是否有效。 視您的語言喜好設定而定，有許多開放原始碼程式庫可用來驗證 JWT。 我們建議您探索這些選項，而不是實作您自己的驗證邏輯。 這裡的資訊有助於了解如何適當使用這些程式庫。

Azure AD B2C 具有 OpenID Connect 中繼資料端點，可讓應用程式在執行階段提取 Azure AD B2C 的相關資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 您的 B2C 目錄中有每個原則的 JSON 中繼資料文件。 比方說的中繼資料文件 `b2c_1_sign_in` 中的原則 `fabrikamb2c.onmicrosoft.com` 位於:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

此設定文件屬性的其中一個是 `jwks_uri`, ，其值會是相同的原則:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`。

若要判斷哪個原則用來簽署 id_token (以及何處可擷取中繼資料)，您有兩個選項。 首先，將原則名稱包含在 `acr` id_token 中宣告。 如需如何剖析來自 id_token 的宣告資訊，請參閱 [Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md)。 另一個選擇是要編碼的值中的原則 `狀態` 時發出要求，然後將它判斷使用哪些原則已解碼的參數。 任一種方法都絕對有效。

當您從 OpenID Connect 中繼資料端點取得中繼資料文件後，您可以使用位於此端點的 RSA256 公用金鑰驗證 id_token 的簽章。 可能有多個索引鍵，列出在此端點，在任何給定時間點的時間、 每個由 `kid`。 Id_token 標頭也包含 `kid` 宣告，表示其中一個這些金鑰用來簽署 id_token。 請參閱 [Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md) 如需詳細資訊，包括 [驗證語彙基元](active-directory-b2c-reference-tokens.md#validating-tokens) 和 [重要簽署金鑰變換的相關資訊](active-directory-b2c-reference-tokens.md#validating-tokens)。


一旦驗證了 id_token 的簽章，就會有數項宣告需要驗證：

- 您應該驗證 `nonce` 宣告以防止權杖重新執行攻擊。 其值應該是您在登入要求中所指定的內容。
- 您應該驗證 `aud` 宣告以確保您的應用程式發出的 id_token。 值應該是應用程式的應用程式識別碼。
- 您應該驗證 `iat` 和 `exp` 宣告以確保 id_token 尚未過期。

您可能也希望根據自己的案例驗證其他宣告。 一些常見的驗證包括：

- 確保使用者/組織已註冊應用程式。
- 確保使用者擁有正確的授權/權限
- 確保驗證具有特定強度，例如多重要素驗證。

如需有關 id_token 中宣告的詳細資訊，請參閱 [Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md)。

一旦驗證完畢 id_token，即可利用使用者開始工作階段，並使用 id_token 中的宣告來取得應用程式中的使用者相關資訊。 這項資訊可以用於顯示、記錄、授權等等。

## 取得權杖

如果您的 Web 應用程式只需要執行原則，您可以略過接下來的幾節。 這幾節只適用於某些 Web 應用程式，這些應用程式需要經過驗證來呼叫 Web API，而該 Web API 也受到 Azure AD B2C 保護。

您可以兌換您取得 authorization_code (使用 `response_type = code + id_token`) 權杖所需的資源，藉由傳送 `POST` 要求 `/語彙基元` 端點。 在 Azure AD B2C 預覽中，是唯一的資源，您可以要求的語彙基元
您應用程式本身的後端的 web API。 用於要求的語彙基元給您自己的慣例是使用範圍 `openid`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
    "scope": "openid offline_access",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
    "client_secret": "<your-application-secret>"
}
```

| 參數| | 說明|
| ----------------------- | ------------------------------- | --------------------- |
| p| 必要| 用來取得授權碼的原則。您不可在此要求中使用不同的原則。**請注意，此參數會加入至查詢字串**，而不是在 POST 本文中。|
| client_id| 必要| 應用程式識別碼， [Azure 入口網站](https://portal.azure.com) 指派您的應用程式。|
| grant_type| 必要| 必須是 `authorization_code` 授權程式碼流程。|
| scope| 必要| 範圍的空格分隔清單。單一範圍值向 Azure AD 表示同時要求的兩個權限。 `Openid` 範圍表示與的權限登入的使用者，並取得使用者的相關資料的形式 **id_tokens**。它可以用來為應用程式本身的後端 Web API 取得權杖，由與用戶端相同的應用程式識別碼代表。 `Offline_access` 範圍表示您的應用程式將需要 **refresh_token** 的長時間執行資源的存取權。|
| code| 必要| 您在流程的第一個階段中取得的 authorization_code。|
| redirect_uri| 必要| 應用程式的 redirect_uri，指出您在此處收到 authorization_code。|
| client_secret| 必要| 您在中產生的應用程式密碼 [Azure 入口網站](https://portal.azure.com)。這個應用程式密碼是重要的安全性構件，應該安全地儲存在伺服器上。您也應該注意定期輪換此用戶端密碼。|

成功的權杖回應如下：

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "openid offline_access",
    "id_token_expires_in": "3600",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
    "refresh_token_expires_in": "1209600"
}
```
| 參數| 說明|
| ----------------------- | ------------------------------- |
| not_before| 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。|
| token_type| 表示權杖類型值。Azure AD 唯一支援的類型是 Bearer。|
| id_token| 您所要求已簽署的 JWT 權杖。|
| scope| 權杖有效的範圍，可用於快取權杖供以後使用。|
| id_token_expires_in| id_token 的有效期 (以秒為單位)。|
| profile_info| Base 64 編碼的 JSON 字串，可能包含有關使用者的有用資訊，可顯示在原生應用程式中。確切內容取決於您在原則中設定的應用程式宣告|
| refresh_token| OAuth 2.0 重新整理權杖。應用程式可以使用此權杖取得目前的權杖到期後的其他權杖。Refresh_token 的有效期很長，而且可以用來延長保留資源存取權的時間。如需詳細資訊，請參閱 [B2C 權杖參照](active-directory-b2c-reference-tokens.md)。請注意，您必須擁有使用範圍 `offline_access` 授權和以便接收重新整理權杖的權杖要求中。|
| refresh_token_expires_in| 重新整理權杖的最長有效時間 (以秒為單位)。不過，重新整理權杖隨時都可能失效。|

> [AZURE.NOTE]
    如果您現在問：「access_token 在哪裡？」，請考慮下列問題。 當您要求 `openid` 範圍，Azure AD 會發出 JWT `id_token` 在回應中。 雖然這 `id_token` 不是技術 OAuth 2.0 access_token，才能使用這類 communcating 與您的應用程式本身的後端服務，由相同 client_id 與用戶端時。  `Id_token` 仍然是帶正負號的 JWT 持有人權杖可以用來驗證要求並傳送至 HTTP 授權標頭中的資源。 其差異在於， `id_token` 沒有一種機制，可能會有特定的用戶端應用程式的存取範圍。 不過，當用戶端應用程式是唯一能夠與後端服務進行通訊的用戶端時 (如同目前的 Azure AD B2C 預覽一樣)，則不需要這種範圍設定機制。 當 Azure AD B2C 預覽增加功能讓用戶端與第一方和第三方資源通訊時，將會引進 access_token。 不過，即使是在該階段中，使用 `id_tokens` 與您的應用程式本身的後端通訊服務仍會 reccomended 模式。 如需詳細資訊類型的應用程式可以使用 Azure AD B2C 預覽來建置，請參閱 [這篇文章](active-directory-b2c-apps.md)。

錯誤回應格式如下：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數| 說明|
| ----------------------- | ------------------------------- |
| 錯誤| 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。|
| error_description| 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。|

## 使用權杖

既然您已成功取得 `id_token`, ，您可以使用語彙基元給您後端 Web Api 的要求中包含在 `授權` 標頭:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 重新整理權杖

Id_token 有效期很短，到期後必須重新整理，才能繼續存取資源。 您可以這樣來提交另一個 `POST` 要求 `/語彙基元` 端點，但這次提供 `refresh_token` 而不是 `程式碼`:

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "refresh_token",
    "client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
    "scope": "openid offline_access",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
    "client_secret": "<your-application-secret>"    
}
```

| 參數| | 說明|
| ----------------------- | ------------------------------- | -------- |
| p| 必要| 用來取得原始重新整理權杖的原則。您不可在此要求中使用不同的原則。**請注意，此參數會加入至查詢字串**，而不是在 POST 本文中。|
| client_id| 必要| 應用程式識別碼， [Azure 入口網站](https://portal.azure.com) 指派您的應用程式。|
| grant_type| 必要| 必須是 `refresh_token` 個授權碼流程的這個階段。|
| scope| 必要| 範圍的空格分隔清單。單一範圍值向 Azure AD 表示同時要求的兩個權限。 `Openid` 範圍表示與的權限登入的使用者，並取得使用者的相關資料的形式 **id_tokens**。它可以用來為應用程式本身的後端 Web API 取得權杖，由與用戶端相同的應用程式識別碼代表。 `Offline_access` 範圍表示您的應用程式將需要 **refresh_token** 的長時間執行資源的存取權。|
| redirect_uri| 必要| 應用程式的 redirect_uri，指出您在此處收到 authorization_code。|
| refresh_token| 必要| 您在流程的第二個階段中取得的原始 refresh_token。請注意，您必須擁有使用範圍 `offline_access` 授權和以便接收重新整理權杖的權杖要求中。|
| client_secret| 必要| 您在中產生的應用程式密碼 [Azure 入口網站](https://portal.azure.com)。這個應用程式密碼是重要的安全性構件，應該安全地儲存在伺服器上。您也應該注意定期輪換此用戶端密碼。|

成功的權杖回應如下：

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "openid offline_access",
    "id_token_expires_in": "3600",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
    "refresh_token_expires_in": "1209600"
}
```
| 參數| 說明|
| ----------------------- | ------------------------------- |
| not_before| 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。|
| token_type| 表示權杖類型值。Azure AD 唯一支援的類型是 Bearer。|
| id_token| 您所要求已簽署的 JWT 權杖。|
| scope| 權杖有效的範圍，可用於快取權杖供以後使用。|
| id_token_expires_in| id_token 的有效期 (以秒為單位)。|
| profile_info| Base 64 編碼的 JSON 字串，可能包含有關使用者的有用資訊，可顯示在原生應用程式中。確切內容取決於您在原則中設定的應用程式宣告|
| refresh_token| OAuth 2.0 重新整理權杖。應用程式可以使用此權杖取得目前的權杖到期後的其他權杖。Refresh_token 的有效期很長，而且可以用來延長保留資源存取權的時間。如需詳細資訊，請參閱 [B2C 權杖參照](active-directory-b2c-reference-tokens.md)。|
| refresh_token_expires_in| 重新整理權杖的最長有效時間 (以秒為單位)。不過，重新整理權杖隨時都可能失效。|

錯誤回應格式如下：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 參數| 說明|
| ----------------------- | ------------------------------- |
| 錯誤| 用以分類發生的錯誤類型與回應錯誤的錯誤碼字串。|
| error_description| 協助開發人員識別驗證錯誤根本原因的特定錯誤訊息。|




## 傳送登出要求

當您想要將使用者登出應用程式時，就不足夠清除您的應用程式 cookie 或其他方式結束使用者工作階段。 您也必須將使用者重新導向至 Azure AD 來完成登出。 如果不這樣做，使用者可能不需要再次輸入認證就能重新通過應用程式的驗證，因為他們與 Azure AD 之間仍然存在一個有效的登入工作階段。

您可以只是將使用者重新導向至 `end_session_endpoint` 列出相同 OpenID Connect 中繼資料文件中所述 [上方](#validate-the-id-token):

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 參數| | 說明|
| ----------------------- | ------------------------------- | ------------ |
| p| 必要| 使用者最近用來登入應用程式的原則。|
| post_logout_redirect_uri| 建議使用| 使用者在成功登出後應該重新導向的 URL。如果未包含，Azure AD B2C 會向使用者顯示一般訊息。|

> [AZURE.NOTE]
    同時將使用者導向 `end_session_endpoint` 將會清除某些使用者單一登入狀態與 Azure AD，它將目前未有效地登出使用者。反之，使用者將選取他們要用來登入的 IDP，然後重新經過驗證，而不需要輸入認證。如果是使用社交 IDP，這是可預期的行為。如果使用者想要登出 B2C 目錄，並不一定表示他們想要完全登出 Facebook 帳戶。不過，如果是使用本機帳戶，則應該可以正確地結束使用者工作階段。它是已知 [限制](active-directory-b2c-limitations.md) Azure AD 的預覽，登出的本機帳戶的運作不正常。立即詞彙的因應措施是將傳送 `提示 & = 登入` 中每個驗證要求，同時也會有想要的行為的外觀，但會中斷單一登入 B2C 目錄中的應用程式之間的參數。

## 使用您自己 B2C 目錄

如果您想要親自嘗試這些要求，您必須先執行這三個步驟，然後以您自己值取代上面的範例值：

- [建立 B2C 目錄](active-directory-b2c-get-started.md), ，並在要求中使用您目錄的名稱。
- [建立應用程式](active-directory-b2c-app-registration.md) 來取得應用程式識別碼和 redirect_uri。 您可以在應用程式中加入 **Web 應用程式/Web API**，並選擇性地建立**應用程式密碼**。
- [建立您的原則](active-directory-b2c-reference-policies.md) 取得原則名稱。






