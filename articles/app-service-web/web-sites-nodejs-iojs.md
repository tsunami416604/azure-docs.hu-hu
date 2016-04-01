<properties 
    pageTitle="如何搭配使用 io.js 和 Azure App Service Web Apps" 
    description="了解如何搭配使用 Azure App Service 中的 Web 應用程式和 io.js。" 
    services="app-service\web" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/20/2015"
    ms.author="robmcm" />

# 如何搭配使用 io.js 和 Azure App Service Web Apps

受歡迎的節點分岔 [io.js] 功能的各種差異分散至 Joyent 的 Node.js 專案，包括更開放的控管模型、 更快速的發行週期，以及更快速地採用全新和實驗性的 JavaScript 功能。

雖然 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web 應用程式有許多預先安裝的 Node.js 版本，它也允許使用者提供的 Node.js 二進位檔。 本文討論兩種方法可用來啟用 App Service Web Apps 上的 io.js：使用擴充的部署指令碼(其會自動設定 Azure 來使用最新的可用 io.js 版本)，以及手動上傳 io.js 二進位檔。 

<a id="deploymentscript"></a>
## 使用部署指令碼

部署 Node.js 應用程式時，App Service Web Apps 會執行一些小型命令，以確保會正確設定環境。 使用部署指令碼，可自訂此程序來加入 io.js 的下載和組態。

 [Io.js 部署指令碼](https://github.com/felixrieseberg/iojs-azure) 可在 GitHub 上取得。 Web 應用程式上啟用 io.js，只要複製 **.deployment**, ，**deploy.cmd** 和 **IISNode.yml** 應用程式資料夾的根目錄，以及部署至 Web 應用程式。  

第一個檔案 **.deployment**, ，指示 Web Apps 執行 **deploy.cmd** 部署時。 此指令碼會針對 Node.js 應用程式執行所有一般步驟，但也會下載最新版的 io.js。 最後， **IISNode.yml** 設定 Web Apps 使用剛才下載的 io.js 二進位，而不是預先安裝的 Node.js 二進位檔。

> [AZURE.NOTE] 若要更新使用的 io.js 二進位檔，只要重新部署您的應用程式的指令碼都會下載新版 io.js 應用程式部署的每一次。

<a id="manualinstallation"></a>
## 使用手動安裝

手動安裝的自訂 io.js 版本只包括兩個步驟。 首先，下載 **win x64** 直接從二進位 [io.js distribution]。 需要兩個檔案- **iojs.exe** 和 **iojs.lib**。 將這兩個檔案至您的 web 應用程式內的資料夾，例如儲存在 **bin/iojs**。

若要設定 Web 應用程式以使用 **iojs.exe** 而不是預先安裝的 Node 版本，建立 **IISNode.yml** 檔案在您的應用程式的根目錄，並加入下列這一行。

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## 後續步驟

在本文中，您學到如何搭配使用 io.js 與 App Service Web Apps、使用這兩個提供的部署指令碼，以及手動安裝。 

> [AZURE.NOTE] io.js 正在密集的開發中，而且比 Node.js 更頻繁地更新。 許多 Node.js 模組可能不適用於 io.js，請參閱 [io.js on GitHub] 進行疑難排解。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

[io.js]: https://iojs.org
[io.js distribution]: https://iojs.org/dist/
[io.js on GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 

