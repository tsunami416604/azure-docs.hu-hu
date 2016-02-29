<properties
    pageTitle="在 PowerApps 中設定 API 連接到 Azure Active Directory 網域中的後端系統 | Microsoft Azure"
    description="在 PowerApps 中設定 API 連接到受 AAD 保護的後端系統"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="dwrede"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# 設定 API 連接到 Azure Active Directory 網域上的後端資源
隨著愈來愈多的使用者在 Azure Active Directory (AAD) 上建立網域，後端資源也被加入這些 AAD 網域中。 您可以建立和設定 API 連接到這些後端資源。 

#### 開始使用的必要條件

- 申請 [PowerApps 企業](powerapps-get-started-azure-portal.md)。
- 建立 [app service 環境](powerapps-get-started-azure-portal.md)。
- 安裝 [Azure PowerShell] [11] 1.0 預覽或更高版本。
- 登錄中的 API 您 [app service 環境](powerapps-register-api-hosted-in-app-service.md)。

## 步驟 1：建立 Active Directory 應用程式並授與權限

若要存取 AAD 網域中的後端系統，請建立 AAD 應用程式並授與其現有後端 (也是 AAD 應用程式) 的適當權限。 步驟：

1. 在 [Azure 傳統入口網站] [13]，請移至您 Azure Active Directory 中，開啟您租用戶 (或目錄)，然後選取 **應用程式** ] 索引標籤:  
![][14]
2. 選取 **新增** 底部的按鈕。 然後：  

    a) 選擇 **加入我的組織正在開發的應用程式**。  
    b) 輸入您的應用程式的名稱，然後選取 **Web 應用程式和/或 web API**。  
    c) 在 **登入 URL** 和 **應用程式識別碼 URI**, ，輸入您的 AAD 內的唯一 Url 和對您的組織有意義的 Url。 例如，您可以輸入 http://powerappssignon.contoso.com 或 http://powerappsappid.contoso.com。  我們建議您使用貴組織 AAD 網域內的 URL。 URL 用做識別碼，沒有任何需求。 沒有人會瀏覽您所輸入的 URL。 您可以輸入 HTTP 或 HTTPS。  

3. 在新建立的 AAD 應用程式頁面，請移至 **設定** ] 索引標籤:  
![][15]
4. 在 **金鑰** 區段中，使用下拉式清單選取持續時間。 請注意當您選取之後，會顯示索引鍵 **儲存**:  
![][16]
5. 在 **單一登入**, ，加入 ``https://<your App Service Environment name>.azure-apim.net:456/redirect`` 為 **回覆 URL**。
6. 在 **其他應用程式的權限**:  

    a) 選取 **新增應用程式**。 在快顯視窗中，選擇 [AAD 應用程式保護您現有的後端:  
    ![][17]  

    b) 使用下拉式清單來新增權限:  
    ![][18]

7. 選取 **儲存** 底部。 
8. 複製 **用戶端識別碼** 和 **金鑰** 並將其儲存。 關閉 Azure 入口網站之後，金鑰就不再出現。 

請參閱 [與 Azure Active Directory 整合的應用程式](../active-directory-integrating-applications.md) 若要深入了解 AAD 應用程式。 

## 步驟 2：使用 Azure PowerShell 設定 API

此時，沒有任何 Azure 入口網站支援可初始化 API 需要的設定。 若要在 Azure 入口網站中設定 API，請使用下列 Azure PowerShell 指令碼： 

> [AZURE.TIP] 若要了解如何安裝、 設定和執行 Azure PowerShell，請參閱 < 如何安裝和設定 Azure PowerShell] [11]。 下列指令碼可搭配 Azure PowerShell 1.0 Preview 或更新版本使用。

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**請注意** ， **語彙基元** 連線參數名稱很重要。 您可以自行選擇名稱，但必須是駝峰式命名法。 稍後在後端程式碼或 API 原則中會使用這個名稱。

接下來，請移至 [Azure 入口網站] [19]，並移至 **一般** 設定] 刀鋒視窗，您的 api。 您應該會看到其他設定選項:  
![][21]


## 立即試用

在 PowerApps 中開啟應用程式。 在 **可用連線**, ，會列出新的 API。 當您選取 **連接**, ，它會顯示 AAD 登入視窗。 輸入貴組織的 AAD 帳戶詳細資訊後，隨即建立連接。

現在您的後端執行階段進行呼叫時從您的應用程式的應用程式開發介面使用這個連線，接收使用者的 AAD 語彙基元中 **x ms apim-權杖** 下列 [Base64 編碼] [20] 格式的 HTTP 標頭:  

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**請注意** 的屬性名稱 **語彙基元** 符合您設定此設定時使用的連接參數名稱。

後端程式碼可以再取得 AAD 語彙基元從 **AccessToken** 屬性並使用它，如有需要。 App Service 環境會自動重新整理權杖。

## 設定 API  原則

您也可以設定的標準 HTTP AAD 語彙基元使用 API 原則的選擇性地 **授權** 標頭。 如此一來，如果後端程式碼需要使用 AAD 權杖，您就可以標準方式取得它，而不是研究自訂的 HTTP 標頭並進行 Base64 解碼。 若要這樣做，請移至 Azure 入口網站，請移至 **原則** 刀鋒視窗中，您的 API，和設定下列原則:  

```xml
<policies>
    <inbound>
        <base/>
        <choose>
            <when condition="@(context.Variables.ContainsKey(&quot;tokens&quot;) &amp;&amp; ((JObject)context.Variables[&quot;tokens&quot;])[&quot;token&quot;] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[&quot;tokens&quot;])[&quot;token&quot;][&quot;AccessToken&quot;]))">
                <set-header exists-action="override" name="Authorization">
                    <value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[&quot;token&quot;]["AccessToken"])</value>
                </set-header>
            </when>
        </choose>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>
```

查看此原則，它基本上可讓您參考中的值 **x ms apim-權杖** 標頭，因為已解碼的 JObject 使用 **語彙基元** 變數。 然後您可以使用 **設定標頭** 原則，以取得實際的 AAD 語彙基元，並將它設定為 **授權** 標頭。 這是所使用的相同原則 [Azure API 管理](https://azure.microsoft.com/services/api-management/)。 若要深入了解，請參閱 [Azure API 管理中的原則](../api-management-howto-policies.md)。

**請注意** 的屬性名稱 **語彙基元** 符合您設定的設定時所使用的連接參數名稱。

## 摘要和後續步驟

在本主題中，您已了解如何設定 API 連接 (和驗證)  Azure Active Directory 網域中的後端資源。 以下列出一些相關主題及資源，可以協助您深入了解 PowerApps：

- [開發適用於 PowerApps 的 API](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

