<properties
    pageTitle="如何在 Azure App Service 中偵錯 Node.js Web 應用程式"
    description="了解如何在 Azure App Service 中偵錯 Node.js Web 應用程式。"
    tags="azure-portal"
    services="app-service\web"
    documentationCenter="nodejs"
    authors="TomArcher"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="11/18/2015"
    ms.author="tarcher"/>

# 如何在 Azure App Service 中偵錯 Node.js Web 應用程式

Azure 提供內建的診斷功能，可協助您進行偵錯 Node.js 應用程式裝載於 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web 應用程式。 本文中，您將了解如何啟用 stdout 和 stderr 記錄、在瀏覽器中顯示錯誤資訊，以及如何下載和檢視記錄檔。

在 Azure 上裝載的 Node.js 應用程式的診斷功能由 [IISNode]。 本文只討論最常用來收集診斷資訊的設定，不提供 IISNode 的完整使用參考。 如需有關使用 IISNode 的詳細資訊，請參閱 [IISNode Readme] GitHub 上。

<a id="enablelogging"></a>
## 啟用記錄

依預設，App Service Web 應用程式只擷取關於部署的診斷資訊，例如使用 Git 來部署 Web 應用程式時。 這項資訊是很有用，如果您在部署期間，例如失敗時安裝中參考的模組有問題 **package.json**, ，或如果您使用自訂部署指令碼。

若要啟用 stdout 和 stderr 資料流記錄，您必須建立 **IISNode.yml** 檔案的 Node.js 應用程式根目錄，並加入下列:

    loggingEnabled: true

這會啟用記錄來自 Node.js 應用程式的 stderr 和 stdout。

 **IISNode.yml** 檔案也可用來控制易懂的錯誤還是開發人員錯誤傳回至瀏覽器發生失敗時。 若要讓開發人員錯誤，新增下行以 **IISNode.yml** 檔案:

    devErrorsEnabled: true

啟用此選項之後，IISNode 會將傳送至 stderr 的最後 64K 資訊傳回，而不是易懂的錯誤，例如「發生內部伺服器錯誤」。

> [AZURE.NOTE] 雖然 devErrorsEnabled 在開發期間診斷問題時很有用，但在實際執行環境中啟用可能會導致開發錯誤傳送給使用者。

如果 **IISNode.yml** 檔案並不存在您的應用程式中，您必須發佈更新的應用程式之後重新啟動 web 應用程式。 如果您只要變更在現有的設定 **IISNode.yml** 先前發行的檔案，不需要重新啟動。

> [AZURE.NOTE] 如果您的 web 應用程式使用 Azure 命令列工具或 Azure PowerShell Cmdlet，預設值建立 **IISNode.yml** 檔案會自動建立。

若要重新啟動 web 應用程式，請選取 [web 應用程式中的 [Azure 入口網站](https://portal.azure.com), ，然後按一下 [ **重新啟動** 按鈕:

![restart button][restart-button]

如果開發環境中已安裝 Azure 命令列工具，您可以使用下列命令來重新啟動 Web 應用程式：

    azure site restart [sitename]

> [AZURE.NOTE] 雖然 loggingEnabled 和 devErrorsEnabled 是最常用來擷取診斷資訊的 IISNode.yml 組態選項，IISNode.yml 可以用來設定各種不同的裝載環境的選項。 如需設定選項的完整清單，請參閱 [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml) 檔案。

<a id="viewlogs"></a>
## 存取記錄檔

有三種方式可存取診斷記錄檔：使用檔案傳輸通訊協定 (FTP)、下載 Zip 封存檔，或即使更新的記錄資料流 (又稱為 tail)。 需要有 Azure 命令列工具，才能下載記錄檔的 Zip 封存檔或檢視即時資料流。 請使用下列命令來安裝這些工具：

    npm install azure-cli -g

安裝之後，可使用 'azure' 命令來存取工具。 必須先設定命令列工具來使用您的 Azure 訂閱。 如需如何完成這項工作的資訊，請參閱 **如何下載和匯入發佈設定** 區段 [如何使用 Azure 命令列工具](../xplat-cli-connect) 文件。

###FTP

若要透過 FTP 存取診斷資訊，請瀏覽 [Azure 入口網站](https://portal.azure.com), ，選取您的 web 應用程式，然後選取 **儀表板**。 在 **快速連結** ] 區段中， **FTP 診斷記錄** 和 **FTPS 診斷記錄** 連結提供使用 FTP 通訊協定記錄檔的存取。

> [AZURE.NOTE] 如果您沒有先前設定使用者名稱和密碼為 FTP 或部署，您可以從 **快速入門** 管理頁面中的選取 **設定部署認證**。

在 [儀表板中傳回的 FTP URL 是 **LogFiles** 目錄，將會包含下列子目錄:

* [部署方法](web-sites-deploy) -相同名稱的目錄，如果您使用 Git 之類的部署方法，將會建立，而且會包含與部署相關的資訊。

* nodejs - 從應用程式的所有執行個體擷取的 stdout 和 stderr 資訊 (當 loggingEnabled 為 true 時)。

###Zip 封存檔

若要下載診斷記錄的 Zip 封存檔，請從 Azure 命令列工具中使用下列命令：

    azure site log download [sitename]

這會下載 **diagnostics.zip** 目前目錄中。 此封存檔包含下列目錄結構：

* deployments - 關於應用程式部署的資訊記錄

* LogFiles

    * [部署方法](web-sites-deploy) -相同名稱的目錄，如果您使用 Git 之類的部署方法，將會建立，而且會包含與部署相關的資訊。

    * nodejs - 從應用程式的所有執行個體擷取的 stdout 和 stderr 資訊 (當 loggingEnabled 為 true 時)。

###即時資料流 (tail)

若要檢視診斷記錄資訊的即時資料流，請從 Azure 命令列工具中使用下列命令：

    azure site log tail [sitename]

這會傳回伺服器上最新發生之記錄事件的資料流。 此資料流會傳回部署資訊及 stdout 和 stderr 資訊 (當 loggingEnabled 為 true 時)。

<a id="nextsteps"></a>
## 後續步驟

本文中，您學到如何啟用和存取 Azure 的診斷資訊。 此資訊有助於了解應用程式發生的問題，但也可能指出您使用的模組有問題，或 App Service Web Apps 使用的 Node.js 版本和部署環境中使用的版本不同。

在 Azure 上使用模組的資訊，請參閱 [Azure 應用程式中使用 Node.js 模組](../nodejs-use-node-modules-azure-apps)。

指定 Node.js 版本的應用程式的詳細資訊，請參閱 [Specifying a Node.js version in an Azure application]。

如需詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

[IISNode]: https://github.com/tjanczuk/iisnode
[IISNode Readme]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli-install.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Specifying a Node.js version in an Azure application]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 

