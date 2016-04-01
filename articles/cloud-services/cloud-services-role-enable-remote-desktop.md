<properties 
pageTitle="啟用 Azure 雲端服務中角色的遠端桌面連線" 
description="如何設定的 Azure 雲端服務應用程式以允許遠端桌面連線" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="10/14/2015" 
ms.author="saurabh"/>

# 啟用 Azure 雲端服務中角色的遠端桌面連線

>[AZURE.SELECTOR]
- [Azure 傳統入口網站](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](https://msdn.microsoft.com/library/gg443832.aspx)


遠端桌面可讓您存取 Azure 內執行中角色的桌面。 您可以使用遠端桌面連線來疑難排解和診斷執行中應用程式的問題。 

您可以在開發期間藉由在服務定義中包含遠端桌面模組啟用遠端桌面連線，也可以選擇透過遠端桌面延伸模組啟用遠端桌面。 慣用的方法是使用遠端桌面延伸模組，因為即使在部署應用程式之後，您仍然可以啟用遠端桌面，無需重新部署您的應用程式。 


## 從入口網站設定遠端桌面
入口網站會使用遠端桌面延伸模組方法，因此即使在應用程式部署之後，您也可以啟用遠端桌面。  **設定** 的雲端服務] 頁面可讓您啟用遠端桌面，變更用來連接到虛擬機器的本機系統管理員帳戶、 將憑證用於驗證和設定到期日。 


1. 按一下 [ **雲端服務**, ，按一下 [雲端服務的名稱，然後按一下 **設定**。

2. 按一下 [ **遠端**。
    
    ![Cloud services remote](./media/cloud-services-role-enable-remote-desktop/CloudServices_Remote.png)
    
    > [AZURE.WARNING]當您首次啟用遠端桌面並按一下 [確定] (勾選記號) 時，所有角色執行個體都會重新啟動。 若要防止重新啟動，角色上必須安裝用來將密碼加密的憑證。 若要防止重新啟動， [上傳雲端服務的憑證](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service) 然後回到此對話方塊。
    

3. 在 **角色**, ，選取您想要更新，或選取的角色 **所有** 所有角色。

4. 進行下列任一變更：
    
    - 若要啟用遠端桌面，請選取 **啟用遠端桌面** 核取方塊。 若要停用遠端桌面，請清除此核取方塊。
    
    - 建立用來對角色執行個體進行遠端桌面連線的帳戶。
    
    - 更新現有帳戶的密碼。
    
    - 選取要用於驗證上傳的憑證 (憑證使用上傳 **上載** 上 **憑證** 頁面)，或建立新的憑證。 
    
    - 變更遠端桌面組態的到期日。

5. 當您完成組態更新時，按一下 **確定** （勾選記號）。


## 角色執行個體的遠端存取
一旦在角色上啟用遠端桌面，您可以透過各種工具遠端存取角色執行個體。

從入口網站連線到角色執行個體：
    
  1.   按一下 [ **執行個體** 開啟 **執行個體** 頁面。
  2.   選取已設定「遠端桌面」的角色執行個體。
  3.   按一下 [ **連接**, ，並依照指示開啟桌面。 
  4.   按一下 [ **開啟** 然後 **連接** 以啟動遠端桌面連線。 


### 使用 Visual Studio 遠端存取角色執行個體

在 Visual Studio 中，伺服器總管：

1. 展開 **Azure\\Cloud Services\\ [雲端服務名稱]** 節點。
2. 展開 [ **預備** 或 **生產**。
3. 展開個別角色。
4. 以滑鼠右鍵按一下其中一個角色執行個體中，按一下 **...使用遠端桌面連線**, ，然後輸入使用者名稱和密碼。 

![伺服器總管遠端桌面](./media/cloud-services-role-enable-remote-desktop/ServerExplorer_RemoteDesktop.png)


### 使用 PowerShell 取得 RDP 檔案
您可以使用 [Get-azureremotedesktopfile](https://msdn.microsoft.com/library/azure/dn495261.aspx) cmdlet 來擷取 RDP 檔案。 然後，您可以使用 RDP 檔案及遠端桌面連線存取雲端服務。

### 以程式設計的方式透過服務管理 REST API 下載 RDP 檔案
您可以使用 [下載 RDP 檔案](https://msdn.microsoft.com/library/jj157183.aspx) REST 作業下載 RDP 檔案。 



## 在服務定義檔中設定遠端桌面

這個方法可讓您在開發期間啟用應用程式的遠端桌面。 此方法需要加密的密碼儲存在您的服務組態檔中，且遠端桌面組態的任何更新都需要重新部署應用程式。 如果您想要避免這些缺點，您應該使用以上所述之以遠端桌面延伸模組為基礎的方法。  

您可以使用 Visual Studio [啟用遠端桌面連線](https://msdn.microsoft.com/library/gg443832.aspx) 使用服務定義檔案的方法。  
下列步驟說明服務模型檔案啟用遠端桌面所需的變更。 Visual Studio 將會在發佈時自動產生這些變更。

### 設定服務模型中的連線 
使用 **匯入** 匯入的項目 **RemoteAccess** 模組和 **RemoteForwarder** 模組 [ServiceDefinition.csdef](cloud-services-model-and-package.md#csdef) 檔案。

服務定義檔應類似下列已加入 `<Imports>` 元素的範例。

```xml
<ServiceDefinition name="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2013-03.2.0">
    <WebRole name="WebRole1" vmsize="Small">
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="Endpoint1" endpointName="Endpoint1" />
                </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
            <Import moduleName="Diagnostics" />
            <Import moduleName="RemoteAccess" />
            <Import moduleName="RemoteForwarder" />
        </Imports>
    </WebRole>
</ServiceDefinition>
```
 [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#cscfg) 檔案應該類似下列的範例，請注意 `<ConfigurationSettings>` 和 `<Certificates>` 項目。 指定的憑證必須是 [上傳至雲端服務](cloud-services-how-to-create-deploy/#how-to-upload-a-certificate-for-a-cloud-service)。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="<name-of-cloud-service>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2013-03.2.0">
    <Role name="WebRole1">
        <Instances count="2" />
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="[name-of-user-account]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="[base-64-encrypted-user-password]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="[certificate-expiration]" />
            <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
        </ConfigurationSettings>
        <Certificates>
            <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="[certificate-thumbprint]" thumbprintAlgorithm="sha1" />
        </Certificates>
    </Role>
</ServiceConfiguration>
```


## 其他資源

[如何設定雲端服務](cloud-services-how-to-configure.md)

