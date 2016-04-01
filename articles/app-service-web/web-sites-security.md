<properties
    pageTitle="保護 Azure App Service 中的 Web 應用程式"
    description="了解如何保護 Azure Web 應用程式的安全。"
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="09/16/2015"
    ms.author="cephalin"/>


#保護 Azure App Service 中的 Web 應用程式

開發 Web 應用程式的其中一個挑戰是如何提供客戶安全無虞的服務。 在本文中，您將了解功能 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 可保護您的 web 應用程式。

> [AZURE.NOTE] Web 應用程式的安全性考量的完整討論已超出本文的範圍。 做為起點的進一步保護 web 應用程式的指導方針，請參閱 [開啟 Web 應用程式安全性專案 (OWASP)]( https://www.owasp.org/index.php/Main_Page), ，尤其是 [前 10 個專案。](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), ，其中會列出目前的前 10 個 web 應用程式安全性錯誤，由 OWASP 成員所決定。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a name="https"></a> 保護通訊的安全

如果您使用 ***。 azurewebsites.net** 網域名稱建立 web 應用程式的所有提供的 SSL 憑證，您可以立即使用 HTTPS，***。 azurewebsites.net** 網域名稱。 如果您的網站使用 [自訂網域名稱](web-sites-custom-domain-name.md), ，您可以上傳 SSL 憑證來 [啟用 HTTPS](web-sites-configure-ssl-certificate.md) 為自訂網域。

##<a name="develop"></a> 保護開發的安全

### 發行設定檔和發行設定

在開發應用程式時，執行管理工作或自動化工作使用公用程式，例如 **Visual Studio**, ，**Web Matrix**, ，**PowerShell** 或 **Azure 命令列介面 (Azure CLI)**, ，您可以使用 *發行設定* 檔案或 *發行設定檔*。 這兩個選項皆會向 Azure 驗證您的身分，且應已設定安全性防止未經授權的存取。

* A **發行設定** 檔案包含

    * 您的 Azure 訂閱識別碼

    * 可讓您執行訂閱管理工作的管理憑證 *而無需提供帳戶名稱或密碼*。

* A **發行設定檔** 檔案包含

    * 發行至 Web 應用程式的資訊

如果您使用發行設定或發行設定檔的公用程式，匯入到公用程式包含發行設定檔的檔案，然後 **刪除** 檔案。 如果您必須保留此檔案，以分享給其他人參與專案，例如將它儲存在安全的位置例如 **加密** 目錄具有限制權限。

另外，您應確保匯入的憑證已受到保護。 例如， **PowerShell** 和 **Azure 命令列介面 (Azure CLI)** 將匯入的資訊儲存於您 **主目錄** (*~* Linux 或 OS X 系統上和 */使用者//users/yourusername* Windows 系統上。)為了加強安全性，您可能想要 **加密** 使用適用於您作業系統的加密工具可以使用這些位置。

### 組態設定和連接字串
將連接字串、驗證憑證和其他敏感資訊儲存於組態檔中是一種常見的做法。 然而，這些檔案可能會暴露在您的網站上，或簽入公開的儲存機制，因而公開此資訊。

Azure 應用程式服務可讓您將組態資訊儲存為 Web 應用程式執行階段環境的一部分 **應用程式設定** 和 **連接字串**。 這些值會公開應用程式在執行階段，透過 *環境變數* 大部分的程式設計語言。 若是 .NET 應用程式，則這些值會在執行階段加入您的 .NET 組態。

**應用程式設定** 和 **連接字串** 可使用 [Azure 入口網站](http://portal.azure.com) 或公用程式，例如 PowerShell 或 Azure CLI。

如需應用程式設定和連接字串的詳細資訊，請參閱 [設定 web 應用程式](web-sites-configure.md)。

### FTPS

Azure 提供安全 FTP 存取權限的檔案系統 web 應用程式透過 **FTPS**。 這可讓您以安全的方式存取 Web 應用程式上的應用程式程式碼及診斷記錄。 您 Web 應用程式的 FTPS 連結具有下列步驟：

1. 開啟 [Azure 入口網站](http://portal.azure.com)。
2. 選取 **全部瀏覽**。
3. 從 **瀏覽** 分頁中，選取 **Web 應用程式**。
4. 從 **Web 應用程式** 刀鋒視窗中，選取所需的 web 應用程式。
5. 從 web 應用程式的刀鋒視窗中，選取 **所有設定**。
6. 從 **設定** 分頁中，選取 **屬性**。
7. 上提供 FTP 和 FTPS 連結 **設定** 刀鋒視窗。 

如需 FTPS 的詳細資訊，請參閱 [檔案傳輸通訊協定](http://en.wikipedia.org/wiki/File_Transfer_Protocol)。

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## 後續步驟

如需詳細資訊的安全性時，Azure 平台上報告資訊 **安全性事件或不當使用**, ，或通知 Microsoft 您即將執行 **入侵測試** 您的網站，請參閱 [安全性] 區段的 [Microsoft Azure 信任中心](http://azure.microsoft.com/support/trust-center/security/)。

如需有關 **web.config** 或 **applicationhost.config** 檔案在 web 應用程式，請參閱 [組態選項，在 Azure App Service web apps 中解除鎖定](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)。

如需記錄資訊的 web 應用程式，可能會偵測攻擊很有用的請參閱 [啟用診斷記錄](web-sites-enable-diagnostic-log.md)。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)


