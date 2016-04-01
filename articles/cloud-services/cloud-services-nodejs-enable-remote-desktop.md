<properties 
    pageTitle="對雲端服務啟用遠端桌面 (Node.js)" 
    description="了解如何對裝載 Azure Node.js 應用程式的虛擬機器啟用遠端桌面存取。" 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/20/2015" 
    ms.author="robmcm"/>


# 在 Azure 中啟用遠端桌面

遠端桌面可讓您存取 Azure 中執行的角色執行個體的桌面
。 您可以使用遠端桌面連線
設定虛擬機器或疑難排解
重新接收。

> [AZURE.NOTE] 本文適用於裝載成 Azure 雲端服務的 Node.js 應用程式。


## 必要條件

- 安裝和設定 [Powershell](../install-configure-powershell.md)。
- 將 Node.js 應用程式部署到 Azure 雲端服務。 如需詳細資訊，請參閱 [建置和部署 Node.js 應用程式至 Azure 雲端服務](cloud-services-nodejs-develop-deploy-app.md)。


## 步驟 1：使用 Azure PowerShell 設定服務的遠端桌面存取

若要使用遠端桌面，您需要更新 Azure 服務的定義，
使用使用者名稱、 密碼和憑證設定。 

使用包含您應用程式的來源檔案的電腦執行下列步驟。

1. 執行 **Azure PowerShell** 系統管理員身分。 (從 **[開始] 功能表** 或 **[開始] 畫面**, ，搜尋 **PowerShell**。)

2.  巡覽至目錄，其中包含服務定義 (.csdef) 和
服務組態 (.cscfg) 檔案。

3. 輸入下列 PowerShell cmdlet：

        Enable-AzureServiceProjectRemoteDesktop

4. 出現提示時，請輸入使用者名稱和密碼。

    ![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  輸入下列 PowerShell cmdlet，以發佈變更：

        Publish-AzureServiceProject

    ![publish-azureserviceproject][publish-project]

## 步驟 2：連接到角色執行個體

發佈更新的服務定義後，您可以連接到
角色執行個體。

1.  在 [Azure classic portal], ，請選取 **雲端服務** ，然後選取您的服務。

    ![Azure 傳統入口網站][cloud-services]

2.  按一下 [ **執行個體**, ，然後按一下 [ **生產** 或 **預備** 以檢視您的服務執行個體。 選取執行個體，然後按一下 [ **連接** 頁面的底部。

    ![The instances page][3]

2.  當您按一下 **連接**, ，網頁瀏覽器會提示您儲存
    .rdp 檔。 開啟這個檔案。 (例如，如果您使用 Internet Explorer，按一下 [ **開啟**。)

    ![prompt to open or save the .rdp file][4]

3.  檔案開啟時，會出現下列安全性提示：

    ![Windows security prompt][5]

4.  按一下 [ **連接**, ，並會出現安全性提示，要求輸入
    認證以存取執行個體。 輸入您
    在 [步驟 1] [步驟 1 ︰ 將服務設定為使用 Azure PowerShell 的遠端桌面存取]，然後按一下 [ **確定**。

    ![username/password prompt][6]

連接完成時，遠端桌面連線會顯示
Azure 中執行個體的桌面。 

![Remote desktop session][7]

## 步驟 3：將服務設定成停用遠端桌面存取 

當您不再需要雲端中角色執行個體的遠端桌面連線時，
在雲端中的執行個體停用遠端桌面存取使用 [Azure PowerShell]。

1.  輸入下列 PowerShell cmdlet：

        Disable-AzureServiceProjectRemoteDesktop

2.  輸入下列 PowerShell cmdlet，以發佈變更：

        Publish-AzureServiceProject

## 其他資源

- [從遠端存取 Azure 中的角色執行個體] 
- [搭配使用遠端桌面與 Azure 角色]
- [Node.js 開發人員中心](/develop/nodejs/)

  [Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Azure classic portal]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Remotely Accessing Role Instances in Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Using Remote Desktop with Azure Roles]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx
 

