<properties 
    pageTitle = 「 使用流量管理員的 Azure App Service 中設定 web 應用程式的自訂網域名稱 」 wpickett 」 使用的自訂網域名稱中包含的流量管理員負載平衡的 Azure App Service web 應用程式。 」 
    描述 = 「 使用的自訂網域名稱中包含的流量管理員負載平衡的 Azure App Service web 應用程式。 」 
    services="app-service\web" 
    documentationCenter="" 
    作者 ="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/18/2015" 
    ms.author="robmcm"/>

#在使用流量管理員的 Azure App Service 中設定 Web 應用程式的自訂網域名稱

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供對 Azure App Service (使用流量管理員進行負載平衡的網站) 使用自訂網域名稱的一般指示。

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## 了解 DNS 記錄

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## 將 Web 應用程式設定為標準模式

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## 新增自訂網域的 DNS 記錄

> [AZURE.NOTE] 如果您已透過 Azure App Service Web Apps 購買網域則略過下列步驟，而且最後一個步驟，請參閱 [購買 Web Apps 網域](custom-dns-web-site-buydomains-web-app.md) 文件。 

若要將您的自訂網域與 Azure App Service 的 Web 應用程式建立關聯，必須在 DNS 表格中為您的自訂網域新增項目，方法是使用您向其購買網域名稱之網域註冊機構所提供的工具。 使用下列步驟來尋找並使用 DNS 工具。

1. 在網域註冊機構登入您的帳戶，並尋找用於管理 DNS 記錄的頁面。 尋找標示為網站的連結或區域 **網域名稱**, ，**DNS**, ，或 **Name Server Management**。 通常可以找到此網頁的連結，檢視您的帳戶資訊，例如尋找連結 **我的網域**。

4. 一旦找到網域名稱的管理頁面，請尋找可讓您編輯 DNS 記錄的連結。 這可能會列為 **區域檔案**, ，**DNS 記錄**, ，或為 **進階** 設定連結。

    * 頁面會很有可能有幾個已建立記錄，例如建立關聯的項目 '**@**' 或 ' \ *' 與 'domain parking' 頁面。 例如，它可能還包含常見子網域記錄 **www**。
    * 頁面上會提及 **CNAME 記錄**, ，或提供選取記錄類型的下拉式清單。 它也可能提及其他記錄這類如 **記錄** 和 **MX 記錄**。 在某些情況下，CNAME 記錄將會以名稱來稱呼其他例如 **別名記錄**。
    * 頁面也會有可讓您將欄位 **對應** 從 **主機名稱** 或 **網域名稱** 到另一個網域名稱。

5. 由於每個註冊機構的特殊要求可能有所不同，一般來說，對應 *從* 自訂網域名稱 (例如 **contoso.com**,，) *至* 流量管理員網域名稱 (**contoso.trafficmanager.net**) 所用的 web 應用程式。

6. 在註冊機構處將新增或修改 DNS 記錄的作業完成後，請儲存變更。

<a name="enabledomain"></a>
## 啟用流量管理員

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

