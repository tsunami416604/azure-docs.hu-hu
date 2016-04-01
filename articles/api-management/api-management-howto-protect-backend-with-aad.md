<properties
    pageTitle="如何使用 Azure Active Directory 與 API 管理保護 Web API 後端"
    description="了解如何使用 Azure Active Directory 與 API 管理保護 Web API 後端。" 
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="sdanie"/>

# 如何使用 Azure Active Directory 與 API 管理保護 Web API 後端

下列視訊示範如何使用 OAuth 2.0 通訊協定搭配 Azure Active Directory 與 API 管理建置 Web API 後端並加以保護。  本文提供概觀以及視訊中步驟的其他資訊。 這段 24 分鐘的視訊示範如何：

-   使用 AAD 建置 Web API 後端以及保護安全 - 從 1:30 開始
-   將 API 匯入 API 管理 - 從 7:10 開始
-   設定開發人員入口網站呼叫 API - 從 9:09 開始
-   設定桌面應用程式呼叫 API - 從 18:08 開始
-   設定 JWT 驗證原則來預先授權要求 - 從 20:47 開始

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## 建立 Azure AD 目錄

若要使用 Azure Active Directory 保護您的 Web API 後端，您必須先具備 AAD 租用戶。 在這段影片中名為租用戶 **APIMDemo** 用。 若要建立 AAD 租用戶，登入 [Azure 傳統入口網站](https://manage.windowsazure.com) 按一下 **新增**]-> [**應用程式服務**]-> [**Active Directory**]-> [**目錄**]-> [**自訂建立**。 

![Azure Active Directory][api-management-create-aad-menu]

在此範例中名為 **APIMDemo** 建立預設的網域名稱為 **DemoAPIM.onmicrosoft.com**。 在整段視訊中都會使用這個目錄。

![Azure Active Directory][api-management-create-aad]

## 建立由 Azure Active Directory 保護的 Web API 服務

在此步驟中，Web API 後端會使用 Visual Studio 2013 建立。 這個步驟的視訊從 1:30 開始。 若要建立 Web API 後端專案在 Visual Studio 中，按一下 **檔案**]-> [**新增**]-> [**專案**, ，然後選擇 [ **ASP.NET Web 應用程式** 從 **Web** 範本清單中。 在這段影片中專案的名稱為 **APIMAADDemo**。 按一下 [ **確定** 以建立專案。 

![Visual Studio][api-management-new-web-app]

按一下 [ **Web API** 從 **從範本清單中選取** 建立 Web API 專案。 若要設定 Azure Directory 驗證按一下 **變更驗證**。

![新增專案][api-management-new-project]

按一下 [ **組織帳戶**, ，並指定 **網域** 的 AAD 租用戶。 在此範例中的網域是 **DemoAPIM.onmicrosoft.com**。 您可以從取得目錄的網域 **網域** 您目錄的索引標籤。

![網域][api-management-aad-domains]

設定所需的設定中 **變更驗證** ] 對話方塊中，按一下 **確定**。

![變更驗證][api-management-change-authentication]

當您按一下 **確定** Visual Studio 將會嘗試與您的 Azure AD 目錄註冊您的應用程式，您可能會提示您登入 Visual Studio。 使用您目錄的系統管理帳戶登入。

![登入 Visual Studio][api-management-sign-in-vidual-studio]

若要設定此專案為 Azure Web API 核取方塊 **定域機組中的主機** 然後按一下 [ **確定**。

![新增專案][api-management-new-project-cloud]

系統可能會提示您登入 Azure，接著您就可以設定 Web 應用程式。

![設定][api-management-configure-web-app]

在此範例中使用新 **應用程式服務方案** 名為 **APIMAADDemo** 指定。

按一下 [ **確定** 設定 Web 應用程式和建立專案。

## 將程式碼加入 Web API 專案

視訊中的下一個步驟會將程式碼加入 Web API 專案。 這個步驟從 4:35 開始。

此範例中的 Web API 使用模型和控制器實作基本的計算機服務。 若要新增服務的模型，以滑鼠右鍵按一下 **模型** 中 **方案總管] 中** 選擇 **新增**, ，**類別**。 將類別 `CalcInput` 按一下 **新增**。

在 `CalcInput.cs` 檔案的開頭處新增下列 `using` 陳述式。

    using Newtonsoft.Json;

 將產生的類別取代為下列程式碼。

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

以滑鼠右鍵按一下 **控制器** 中 **方案總管] 中** 選擇 **新增**]-> [**控制器**。 選擇 **Web API 2 控制器-空白** 按一下 **新增**。 型別 **CalcController** 做為控制器名稱，然後按一下 **新增**。

![新增控制器][api-management-add-controller]

在 `CalcController.cs` 檔案的開頭處新增下列 `using` 陳述式。

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

將產生的控制器類別取代為下列程式碼。 此程式碼實作基本計算機 API 的 `Add`、`Subtract`、`Multiply` 以及 `Divide` 運算。

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
HttpResponseMessage 回應 = Request.CreateResponse();
回應。內容 = 新 StringContent (xml、 System.Text.Encoding.UTF8，「 應用程式/xml 」);
return response;
}
}


按下 **F6** 建置和驗證解決方案。

## 將專案發佈到 Azure

在此步驟中，Visual Studio 專案會發佈到 Azure。 這個步驟的視訊從 5:45 開始。

若要將專案發行至 Azure，以滑鼠右鍵按一下 **APIMAADDemo** 專案在 Visual Studio 中，選擇 **發行**。 保留預設設定 **發行 Web** ] 對話方塊中，按一下 **發行**。

![Web 發佈][api-management-web-publish]

## 授與權限給 Azure AD 後端服務應用程式

設定和發佈您的 Web API 專案時，您的 Azure AD 目錄中會建立一個新的應用程式用於後端服務。 這個步驟會授與權限給 Web API 後端，從視訊的 6:13 開始。

![應用程式][api-management-aad-backend-app]

按一下要設定必要權限的應用程式名稱。 瀏覽至 **設定** 索引標籤，然後向下捲動至 **其他應用程式的權限** 一節。 按一下 [ **應用程式權限** 旁邊的下拉式清單 **Windows** **Azure Active Directory**, ，核取方塊， **讀取目錄資料**, ，然後按一下 **儲存**。

![新增權限][api-management-aad-add-permissions]

>[AZURE.NOTE] 如果 **Windows** **Azure Active Directory** 是未列出其他應用程式的權限] 底下，按一下 [ **新增應用程式** 並將它加入清單中。

請記下的 **應用程式識別碼 URI** 後續的步驟針對 API 管理開發人員入口網站設定 Azure AD 應用程式時使用。

![App 識別碼 URI][api-management-aad-sso-uri]

## 將 Web API 匯入 API 管理

API 是從 API 發佈者入口網站所設定，您可以透過 Azure 傳統入口網站存取此入口網站。 若要存取發行者入口網站，按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。 如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [管理您的第一個 API][] 教學課程。

![發行者入口網站][api-management-management-console]

作業可能會 [手動加入 Api](api-management-howto-add-operations.md), ，或可匯入。 在這段視訊中從 6:40 開始，運算會以 Swagger 格式匯入。

以下列內容建立名為 `calcapi.json` 的檔案，然後將檔案儲存到您的電腦。 確定 `host` 屬性指向您的 Web API 後端。 在此範例中使用 `"host": "apimaaddemo.azurewebsites.net"`。

{
"swagger": "2.0",
"info": {
"title":"Calculator"，
「 說明 」: 「 Arithmetics 透過 HTTP ！ 」，
「 版本 」: 「 1.0 」
},
「 主機 」: 「 apimaaddemo.azurewebsites.net 」，
「 基本 」 路徑:"/ api 」，
"schemes": [
""http
],
"paths": {
「 新增 /？ = {} & b = {b}": {
          "get": {
            「 說明 」: 「 回應兩個數字的總和。 」，
            「 operationId 」: 「 新增兩個整數 」
            "parameters": [
              {
                "name":"a"，
                「 中 」: 「 查詢 」，
                「 說明 」: 「 第一個運算元。 預設值是 <code>51</code>。"，
                "required": true,
                「 預設 」: 「 51 」，
                「 列舉 」: [
                  "51"
                ]
              },
              {
                "name":"b"，
                「 中 」: 「 查詢 」，
                「 說明 」: 「 第二個運算元。 預設值是 <code>49</code>。"，
                "required": true,
                「 預設 」: 「 49 」，
                「 列舉 」: [
                  "49"
                ]
              }
            ],
            「 回應 」: {}
          }
        },
        "/ sub？ = {} & b = {b}": {
          "get": {
            「 說明 」: 「 回應兩個數字之間的差異。 」，
            「 operationId 」: 「 減去兩個整數 」
            "parameters": [
              {
                "name":"a"，
                「 中 」: 「 查詢 」，
                「 說明 」: 「 第一個運算元。 預設值是 <code>100</code>。"，
                "required": true,
                "default":"100"，
                「 列舉 」: [
                  "100"
                ]
              },
              {
                "name":"b"，
                「 中 」: 「 查詢 」，
                「 說明 」: 「 第二個運算元。 預設值是 <code>50</code>。"，
                "required": true,
                "default":"50"
                「 列舉 」: [
                  "50"
                ]
              }
            ],
            「 回應 」: {}
          }
        },
        "/ div？ = {} & b = {b}": {
          "get": {
            「 說明 」: 「 回應兩個數字的商數。 」，
            「 operationId 」: 「 除號兩個整數 」，
            "parameters": [
              {
                "name":"a"，
                「 中 」: 「 查詢 」，
                「 說明 」: 「 第一個運算元。 預設值是 <code>100</code>。"，
                "required": true,
                "default":"100"，
                「 列舉 」: [
                  "100"
                ]
              },
              {
                "name":"b"，
                「 中 」: 「 查詢 」，
                「 說明 」: 「 第二個運算元。 預設值是 <code>20</code>。"，
                "required": true,
                "default":"20"，
                「 列舉 」: [
                  "20"
                ]
              }
            ],
            「 回應 」: {}
          }
        },
        "/ mul？ = {} & b = {b}": {
          "get": {
            「 說明 」: 「 回應兩個數字的乘積。 」，
            「 operationId 」: 「 乘以兩個整數 」
            "parameters": [
              {
                "name":"a"，
                「 中 」: 「 查詢 」，
                「 說明 」: 「 第一個運算元。 預設值是 <code>20</code>。"，
                "required": true,
                "default":"20"，
                「 列舉 」: [
                  "20"
                ]
              },
              {
                "name":"b"，
                「 中 」: 「 查詢 」，
                「 說明 」: 「 第二個運算元。 預設值是 <code>5</code>。"，
                "required": true,
                "default":"5"
                「 列舉 」: [
                  "5"
                ]
              }
            ],
            「 回應 」: {}
          }
        }
      }
    }

若要匯入計算機 API，請按一下 [ **Api** 從 **API 管理** 左邊的功能表，然後按一下 **匯入 API**。

![匯入 API 按鈕][api-management-import-api]

執行下列步驟以設定計算機 API。

1. 按一下 [ **檔案從**, ，瀏覽至 `calculator.json` 檔案儲存，並按一下 [ **Swagger** 選項按鈕。
2. 型別 **calc** 到 **Web API URL 尾碼** 文字方塊。
3. 按一下 [在 **產品 （選擇性）** 方塊，然後選擇 **入門**。
4. 按一下 [ **儲存** 匯入 API。

![Add new API][api-management-import-new-api]

匯入 API 之後，API 的摘要頁面隨即會顯示在發行者入口網站中。

## 從開發人員入口網站無法成功呼叫 API

現在，API 已經匯入 API 管理，但是還無法從開發人員入口網站成功呼叫，因為使用 Azure AD 驗證保護後端服務。 這會從視訊的 7:40 開始使用下列步驟示範。

按一下 [ **開發人員入口網站** 從發行者入口網站右上方。

![開發人員入口網站][api-management-developer-portal-menu]

按一下 [ **Api** 按一下 **計算機** API。

![開發人員入口網站][api-management-dev-portal-apis]

按一下 [ **試試**。

![試試看][api-management-dev-portal-try-it]

按一下 [ **傳送** ，並記下回應狀態為 **401 未經授權**。

![傳送][api-management-dev-portal-send-401]

因為後端 API 受到 Azure Active Directory 的保護，所以要求未獲授權。 在成功呼叫 API 之前，必須先使用 OAuth 2.0 設定開發人員入口網站以授權開發人員。 下列各節將說明這個程序。

## 將開發人員入口網站註冊為 AAD 應用程式

使用 OAuth 2.0 設定開發人員入口網站來授權開發人員的第一個步驟是將開發人員入口網站註冊為 AAD 應用程式。 這會從視訊的 8:27 開始示範。

瀏覽至 Azure AD 租用戶從這段影片中，在此範例中的第一個步驟 **APIMDemo** 並瀏覽至 **應用程式** ] 索引標籤。

![新增應用程式][api-management-aad-new-application-devportal]

按一下 [ **新增** 按鈕來建立新的 Azure Active Directory 應用程式，並選擇 **加入我的組織正在開發的應用程式**。

![新增應用程式][api-management-new-aad-application-menu]

選擇 **Web 應用程式和/或 Web API**, ，輸入名稱，然後按一下 [下一步箭頭。 在此範例中 **APIMDeveloperPortal** 用。

![新增應用程式][api-management-aad-new-application-devportal-1]

如 **登入 URL** 輸入您 API 管理服務的 URL，並且附加 `/signin`。 在此範例中 * * https://contoso5.portal.azure-api.net/signin * * 使用。

如 **應用程式識別碼 URL** 輸入您 API 管理服務的 URL 並附加一些唯一字元。 這些可以是任何想要的字元，在此範例 **https://contoso5.portal.azure-api.net/dp** 用。 當想要的 **應用程式屬性** 所設定，按一下核取記號以建立應用程式。

![新增應用程式][api-management-aad-new-application-devportal-2]

## 設定 API 管理 OAuth 2.0 授權伺服器

下一步是在 API 管理中設定 OAuth 2.0 授權伺服器。 這個步驟會從視訊的 9:43 開始示範。

按一下 [ **安全性** 從左側 [API 管理] 功能表中，按一下 [ **OAuth 2.0**, ，然後按一下 [ **新增授權** 伺服器。

![新增授權伺服器][api-management-add-authorization-server]

輸入的名稱和選擇性的描述中 **名稱** 和 **描述** 欄位。 這些欄位是用來在 API 管理服務執行個體中識別 OAuth 2.0 授權伺服器。 在此範例中 **授權伺服器示範** 用。 稍後當您指定要用於 API 驗證的 OAuth 2.0 伺服器時，您要選取這個名稱。

如 **用戶端註冊頁面 URL** 輸入預留位置值，例如 `http://localhost`。   **用戶端註冊頁面 URL** 指向使用者可用來建立及設定 OAuth 2.0 提供者支援使用者帳戶管理他們自己的帳戶的頁面。 在此範例中，使用者沒有建立和設定自己的帳戶，因此使用預留位置。

![新增授權伺服器][api-management-add-authorization-server-1]

接下來，指定 **授權端點 URL** 和 **權杖端點 URL**。

![授權伺服器][api-management-add-authorization-server-1a]

這些值可擷取自 **應用程式端點** 您為開發人員入口網站建立的 AAD 應用程式的頁面。 若要存取端點，瀏覽至 **設定** AAD 應用程式] 索引標籤上，按一下 [ **檢視端點**。

![應用程式][api-management-aad-devportal-application]

![檢視端點][api-management-aad-view-endpoints]

複製 **OAuth 2.0 授權端點** 並將它貼到 **授權端點 URL** 文字方塊。

![新增授權伺服器][api-management-add-authorization-server-2]

複製 **OAuth 2.0 權杖端點** 並將它貼到 **權杖端點 URL** 文字方塊。

![新增授權伺服器][api-management-add-authorization-server-2a]

除了貼上權杖端點，新增名為的其他主體參數 **資源** 值使用 **應用程式識別碼 URI** 後端服務發佈的 Visual Studio 專案時所建立的 AAD 應用程式。

![App 識別碼 URI][api-management-aad-sso-uri]

接著，指定用戶端認證。 這些是您想要存取的資源的認證，在此案例中是後端服務。

![用戶端認證][api-management-client-credentials]

若要取得 **用戶端識別碼**, ，瀏覽至 **設定** ] 索引標籤的 [AAD 應用程式後端服務並複製 **用戶端識別碼**。

若要取得 **用戶端密碼** 按一下 **選取持續時間** 下拉式清單中 **金鑰** 區段，並指定的間隔。 在此範例中使用 1 年。

![用戶端識別碼][api-management-aad-client-id]

按一下 [ **儲存** ，儲存設定，並顯示金鑰。 

>[AZURE.IMPORTANT] 記下這個機碼。 關閉 Azure Active Directory 組態視窗之後，即無法再次顯示金鑰。

將金鑰複製到剪貼簿、 切換回發行者入口網站中，貼上金鑰 **用戶端密碼** 文字方塊中，按一下 [ **儲存**。

![新增授權伺服器][api-management-add-authorization-server-3]

緊接在用戶端認證後面的是授權碼授與。 複製此授權碼並切換回 Azure AD 開發人員入口網站應用程式設定] 頁面上，並貼到授權授與 **回覆 URL** 欄位，然後按一下 [ **儲存** 一次。

![回覆 URL][api-management-aad-reply-url]

下一步是設定開發人員入口網站 AAD 應用程式的權限。 按一下 [ **應用程式權限** 和核取方塊， **讀取目錄資料**。 按一下 [ **儲存** 要儲存這項變更，然後再按一下 **新增應用程式**。

![新增權限][api-management-add-devportal-permissions]

按一下搜尋圖示，型別 **APIM** 到 [開頭為] 方塊中，選取 **APIMAADDemo**, ，然後按一下核取記號儲存。

![新增權限][api-management-aad-add-app-permissions]

按一下 [ **委派的權限** 的 **APIMAADDemo** 和核取方塊， **存取 APIMAADDemo**, ，然後按一下 **儲存**。 這樣就允許開發人員入口網站應用程式存取後端服務。

![新增權限][api-management-aad-add-delegated-permissions]

## 啟用計算機 API 的 OAuth 2.0 使用者授權

現在已經設定好 OAuth 2.0 伺服器，您可以在安全性設定中為您的 API 指定此伺服器。 這個步驟會從視訊的 14:30 開始示範。

按一下 [ **Api** 中左的窗格中，按一下  **計算機** 來檢視和設定其設定。

![計算機 API][api-management-calc-api]

瀏覽至 **安全性** ] 索引標籤，核取 **OAuth 2.0** 核取方塊，選取想要的授權伺服器，從 **授權伺服器** 下拉式清單中，按一下 [ **儲存**。

![計算機 API][api-management-enable-aad-calculator]

## 從開發人員入口網站成功呼叫計算機 API

現在已經在 API 上設定好 OAuth 2.0 授權，就可以從開發人員中心成功呼叫其運算。 這個步驟會從視訊的 15:00 開始示範。

瀏覽回到 **新增兩個整數** 計算機服務開發人員入口網站中按一下作業 **試試**。 請注意新的項目中 **授權** 區段對應至您剛才加入的授權伺服器。

![計算機 API][api-management-calc-authorization-server]

選取 **授權碼** 從授權下拉式清單，然後輸入要使用之帳戶的認證。 如果您已經使用帳戶登入，就不會提示您。

![計算機 API][api-management-devportal-authorization-code]

按一下 [ **傳送** ，並記下 **回應狀態** 的 **200 確定** 和回應內容中作業的結果。

![計算機 API][api-management-devportal-response]

## 設定桌面應用程式呼叫 API

視訊中的下一個程序從 16:30 開始，設定簡單的桌面應用程式呼叫 API。 第一個步驟是在 Azure AD 中註冊桌面應用程式，並且讓它能夠存取目錄和後端服務。 在 18:25 處示範桌面應用程式呼叫計算機 API 的運算。

## 設定 JWT 驗證原則來預先授權要求

在此影片中最後的程序開始 20:48，並示範如何使用 [驗證 JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) 原則來預先授權要求驗證的每個傳入要求存取權杖。 如果要求沒有經過「驗證 JWT」原則驗證，要求就會被 API 管理封鎖而不會傳入後端。

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

如需設定和使用此原則的另一個示範，請參閱 [Cloud Cover 第 177 集 ︰ 更多 API 管理功能](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) 並向前快轉到 13:50。 向前快轉到 15:00 可看到在原則編輯器中設定的原則，再到 18:50 可以看到一個示範，從開發人員入口網站 (使用和不使用必要的授權權杖) 呼叫運算。

## 後續步驟
-   查看更多 [影片](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 解 API 管理。
-   如需其他方式來保護後端服務，請參閱 [相互憑證驗證](api-management-howto-mutual-certificates.md) 和 [透過 VPN 或 ExpressRoute 連線](api-management-howto-setup-vpn)。

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Manage your first API]: api-management-get-started.md


