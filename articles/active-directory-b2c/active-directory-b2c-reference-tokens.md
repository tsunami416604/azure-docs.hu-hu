<properties
    pageTitle="Azure AD B2C 預覽 | Microsoft Azure"
    description="在 Azure AD B2C 預覽中簽發的權杖類型。"
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


# Azure AD B2C 預覽︰權杖參考

本文件說明每種權杖的格式、安全性特性和內容。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 權杖的類型

每個權杖都是以「持有人權杖」表示。

持有人權杖是輕巧型的安全性權杖，授權「持有者」存取受保護的資源。 從這個意義上說，「持有者」是可出示權杖的任何一方。 雖然某一方必須先向 Azure AD 驗證以收到持有人權杖，但如果傳輸和儲存時未採取必要的步驟來保護權杖，它可能會被非預期的一方攔截和使用。 雖然某些安全性權杖都有內建的機制，可防止未經授權的人士使用權杖，但持有者權杖沒有這項機制，而必須以安全通道來傳輸，例如傳輸層安全性 (HTTPS)。 如果持有人權杖以純文字傳輸，惡意人士可能使用攔截式攻擊來取得權杖，然後未經授權存取受保護的資源。 儲存或快取持有者權杖供以後使用時，也適用相同的安全性原則。 務必確定您的應用程式以安全的方式傳輸和儲存持有人權杖。

許多由 Azure AD B2C 所簽發的權杖都會實作為 Json Web 權杖或 JWT。 JWT 是一種精簡的 URL 安全方法，可在兩方之間傳輸資訊。 JWT 中包含的資訊也稱為權杖持有人及主體相關資訊的「宣告」或判斷提示。 JWT 中的宣告是為了傳輸而編碼和序列化的 JSON 物件。 因為 Azure AD B2C 所簽發的 JWT 已簽署但未加密，所以您可以輕鬆地檢查 JWT 的內容以便偵錯。

## Id_Token

您可以適時使用 id_token 中的宣告 - 通常用來顯示顯示帳戶資訊或在應用程式中進行存取控制決策。

Id_token 已簽署，但這次不加密。

下面提供 id_token 中宣告的完整詳細資料以及範例 id_token。 請注意，id_token 中的宣告不依任何特定順序傳回。 此外，新的宣告可以隨時引進 id_token 中 - 您的應用程式不會在引進新的宣告時中斷。 以下清單包含您的應用程式在此書寫時能確實地解譯的宣告。

#### 範例 Id_Token

```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA
```

#### Id_Token 中的宣告

有了 Azure AD B2C，您即可細微控制您的權杖內容。   不過，您收到的每個 id_token 將包含一組特定的安全性相關宣告，可供應用程式用來安全地驗證使用者和要求。 以下是您預期會存在於 Azure AD B2C 所簽發的每個 id_token 中的宣告 - 將由原則決定任何其他宣告。

| 名稱| 宣告| 範例值| 說明|
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| 對象| | | 識別權杖的預定接收者。在 id_token 中，對象是在應用程式註冊入口網站中指派給應用程式的應用程式識別碼。您的應用程式應驗證此值並拒絕不相符的權杖。|
| Issuer| | | 識別可建構並傳回權杖的 Security Token Service (STS)，以及用以使用者驗證的 Azure AD 目錄。您的應用程式應驗證簽發者宣告，以確保權杖來自 v2.0 端點。|
| 發出時間| `iat`| `1438535543`| 簽發權杖的時間 (以新紀元 (Epoch) 時間表示)。|
| 到期時間| | `1438539443`| 權杖失效的時間 (以新紀元 (Epoch) 時間表示)。您的應用程式應使用此宣告來驗證權杖存留期的有效性。|
| 生效時間| | `1438535543`| 權杖生效的時間 (以新紀元 (Epoch) 時間表示)。這通常與簽發時間相同。您的應用程式應使用此宣告來驗證權杖存留期的有效性。|
| 版本| | `1.0`| Azure AD 所定義的 id_token 版本。|
| 程式碼雜湊| | | 只有在 id_token 隨著 OAuth 2.0 授權碼一起簽發時，雜湊程式碼才會包含在 id_token 中。它可用來驗證授權碼的真實性。|
| 存取權杖雜湊| | | 只有在 id_token 隨著 OAuth 2.0 存取權杖一起簽發時，存取權杖才會包含在 id_token 中。它可用來驗證存取權杖的真實性。|
| Nonce| `nonce`| `12345`| Nonce 是緩和權杖重新執行攻擊的策略。這可讓您的應用程式根據在要求上指定的值驗證此值，使應用程式的工作階段與給定的 id_token 產生關聯。您的應用程式應在 id_token 驗證程序中執行這項驗證。|
| 主旨| `sub`| | 權杖判斷提示其相關資訊的主體，例如應用程式的使用者。這個值不可變，而且無法重新指派或重複使用，因此可用來安全地執行授權檢查，例如當權杖用於存取資源時。不過，尚未在 Azure AD B2C 預覽中實作主體宣告。|
| 驗證內容類別參考| | | 用來取得 id_token 的原則名稱。|
| 驗證期間| | `1438535543`| 使用者上次輸入其認證的時間 (以新紀元 (Epoch) 時間表示)。|



## 存取權杖

Azure AD B2C 此時不會簽發存取權杖。 在此早期預覽階段不支援驗證兩個不同的合作對象 - 不過，您可以使用 id_token 在相同應用程式的元件之間進行通訊。

## 重新整理權杖

重新整理權杖是您的應用程式可用來在 OAuth 2.0 流程中取得新的 id_token 和 access_token 的安全性權杖。 它可讓您的應用程式代表使用者長期存取資源，而不需使用者互動。



重新整理權杖永遠對您的應用程式完全不透明。它們是由 Azure AD 所簽發，只能由 Azure AD 檢查和解譯。它們屬於長效權杖，但不得將您的應用程式撰寫成預期重新整理權杖將持續任何一段時間。重新整理權杖可能會因為各種原因而隨時失效。讓您的應用程式知道重新整理權杖是否有效的唯一方式，就是對 Azure AD 提出權杖要求以嘗試兌換。

這將保證您的重新整理權杖盡可能長期保持有效。

## 驗證權杖

此時，您的應用程式必須執行的唯一權杖驗證就是驗證 id_token。 若要驗證 id_token，您的應用程式應該驗證 id_token 簽章和 id_token 中的宣告。


視您的語言喜好設定而定，有許多開放原始碼程式庫可用來驗證 JWT。 我們建議您探索這些選項，而不是實作您自己的驗證邏輯。 這裡的資訊有助於了解如何適當使用這些程式庫。

#### 驗證簽章

第一個區段稱為**標頭**、第二個稱為**主體**，而第三個稱為**簽章**。 簽章區段可用來驗證 id_token 的真實性，您的應用程式才得以信任。

Id_Token 會使用業界標準非對稱式加密演算法 (例如 RSA 256) 進行簽署。 Id_token 標頭包含用來簽署權杖的金鑰和加密方法相關資訊：

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```



在任何指定的時間點，Azure AD 可能會使用一組特定公開-私密金鑰組的其中一個金鑰組來簽署 id_token。 Azure AD 會定期替換一組可能的金鑰，所以應將您的應用程式撰寫成自動處理這些金鑰變更。 檢查 Azure AD 所用公開金鑰的更新的合理頻率大約為 24 小時。

Azure AD B2C 具有 OpenID Connect 中繼資料端點，可讓應用程式在執行階段提取 Azure AD B2C 的相關資訊。 這項資訊包括端點、權杖內容和權杖簽署金鑰。 您的 B2C 目錄中有每個原則的 JSON 中繼資料文件。



您可以使用位於下列位置的 OpenID Connect 中繼資料文件來取得驗證簽章所需的簽署金鑰資料：

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

若要判斷哪個原則用來簽署 id_token (以及何處可擷取中繼資料)，您有兩個選項。  您可將主體進行 base 64 解碼以及將結果產生的 JSON 字串還原序列化，以剖析 JWT 主體中的宣告。  `Acr` 宣告將會用來發出 id_token 原則的名稱。 另一個選擇是要編碼的值中的原則 `狀態` 時發出要求，然後將它判斷使用哪些原則已解碼的參數。 任一種方法都絕對有效。

此中繼資料文件是 JSON 物件，內含幾項實用的資訊，例如執行 OpenID Connect 驗證所需的各種端點的位置。 它也包含 `jwks_uri`, ，可讓用來簽署權杖公開金鑰組的位置。 下面提供位置，但最好是使用中繼資料文件並剖析出動態擷取該位置 `jwks_uri`:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

位於此 url 的 JSON 文件包含在該特定時間點使用的所有公開金鑰資訊。 您的應用程式可以使用 `kid` 或 `x5t` JWT 標頭中選取 [此文件中的公用金鑰已用來簽署特定權杖的宣告。 接著可以使用正確的公開金鑰和指定的演算法來執行簽章驗證。

執行簽章驗證已超出本文件的範圍 - 有許多開放原始碼程式庫可協助您這麼做 (如有必要)。

#### 驗證宣告

當您的應用程式或 API 收到 id_token 時，還應該對 id_token 中的宣告執行一些檢查。 其中包含：

- **對象**宣告 - 確認 id_token 預定要提供給您的應用程式。
- **生效時間**和**到期時間**宣告 - 確認 id_token 尚未過期。
- **簽發者**宣告 - 確認權杖確實是由 Azure AD 簽發給您的應用程式。
- **Nonce** -做為權杖重新執行攻擊防護功能。

這些宣告的預期值的詳細資料中包含上述 [id_token 區段](#id_tokens)。

## 權杖存留期

下列權杖存留期有助於開發及偵錯應用程式，完全是為了讓您了解而提供的。 不得將您的應用程式撰寫成預期任何一個存留期會維持不變 - 它們可以並將在任何指定的時間點變更。

| 權杖| 存留期| 說明|
| ----------------------- | ------------------------------- | ------------ |
| Id_Token| 1 小時| Id_Token 的有效期通常為 1 小時。您的 Web 應用程式可將此相同的存留期使用於維護自己與使用者的工作階段 (建議)，或選擇完全不同的工作階段存留期。如果您的應用程式需要取得新的 id_token，它只需要對 Azure AD 提出新的登入要求。如果使用者有 Azure AD 的有效瀏覽器工作階段，則可能不需要再次輸入其認證。|
| 重新整理權杖| 最多 14 天| 單一重新整理權杖的有效期最多 14 天。不過，重新整理權杖可能會因為任何原因而隨時失效，所以您的應用程式應該繼續嘗試並使用重新整理權杖直到失敗，或直到您的應用程式以新的重新整理權杖取代它為止。如果使用者輸入其認證已 90 天，則重新整理權杖也會失效。|
| 授權碼| 5 分鐘| 授權碼的存留期特意較短，且應在收到時立即兌換 access_token、 id_token 和 refresh_token。|





