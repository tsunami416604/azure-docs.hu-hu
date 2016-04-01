<properties 
    pageTitle="Web 應用程式與 Express (Node.js) | Microsoft Azure" 
    description="以雲端服務教學課程為基礎的教學課程，示範如何使用 Express 模組。" 
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






# 在 Azure 雲端服務上使用 Express 建立 Node.js Web 應用程式

Node.js 包含核心執行時期的一組最低功能。
開發人員經常使用協力廠商模組來提供其他
開發 Node.js 應用程式時的功能。 本教學課程內容
您將建立新的應用程式使用 [Express][] 模組，提供建立 Node.js web 應用程式的 MVC 架構。

完成之應用程式的螢幕擷取畫面如下：

![A web browser displaying Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##建立雲端服務專案

請執行下列步驟來建立名為 'expressapp' 的新雲端服務專案：

1. 從 **開始功能表** 或 **[開始] 畫面**, ，搜尋 **PowerShell**。 最後，以滑鼠右鍵按一下 **PowerShell** ，然後選取 **系統管理員身分執行**。

    ![Azure PowerShell icon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

    [AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

2. 將目錄變更至 **c:\\node** 目錄，然後輸入下列命令來建立名為的新方案 **expressapp** 和名為 web 角色 **WebRole1**:

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21

    > [AZURE.NOTE] 根據預設， **Add-azurenodewebrole** 使用舊版的 Node.js。  **Set-azureserviceprojectrole** 上述陳述式會指示 Azure 使用 0.10.21 版本的節點。  請注意這些參數會區分大小寫。  您可以確認已選取正確的 Node.js 版本檢查 **引擎** 屬性 **WebRole1\package.json**。

##安裝 Express

1. 發出下列命令來安裝 Express 產生器：

        PS C:\node\expressapp> npm install express-generator -g

    npm 命令的輸出類似下列結果。 

    ![Windows PowerShell displaying the output of the npm install express command.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. 將目錄變更至 **WebRole1** 目錄，然後使用 express 命令來產生新的應用程式 ︰

        PS C:\node\expressapp\WebRole1> express

    系統會提示您覆寫先前的應用程式。 輸入 **y** 或 **是** 以繼續。 Express 會產生 app.js 檔案和資料夾結構來準備建立應用程式。

    ![The output of the express command](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  若要安裝 package.json 檔案中定義的其他相依性，
    請輸入下列命令：

        PS C:\node\expressapp\WebRole1> npm install

    ![The output of the npm install command](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  使用下列命令來複製 **bin/www** 檔案重新命名為 **server.js**。 正因如此，雲端服務便能找到此應用程式的進入點。

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    此命令完成之後，您應該有 **server.js** WebRole1 目錄中的檔案。

7.  修改 **server.js** 移除其中一個 '。 ' 字元，將下列那一行。

        var app = require('../app');

    進行這個修改之後，程式碼行應會以如下方式出現。

        var app = require('./app');

    這項變更有必要，因為我們已將檔案移 (先前稱為 **bin/www**,，) 來為應用程式檔案所需的相同目錄。 完成此變更之後，儲存 **server.js** 檔案。

8.  使用下列命令，在 Azure 模擬器中執行應用程式：

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![A web page containing welcome to express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## 修改檢視

現在，修改檢視以顯示 "Welcome to Express in
Azure" 訊息。

1.  輸入下列命令來開啟 index.jade 檔案：

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![The contents of the index.jade file.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade 是 Express 應用程式使用的預設檢視引擎。 如需
    Jade 檢視引擎的詳細資訊請參閱 [http://jade-lang.com][]。

2.  修改最後一行文字，加上 **azure**。

    ![Index.jade 檔案最後一行顯示 ︰ p 歡迎 \#{title} 在 Azure 中](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  儲存檔案並結束 [記事本]。

4.  重新整理瀏覽器，您將會看到變更。

    ![A browser window, the page contains Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

測試應用程式之後, 使用 **Stop-azureemulator** 指令程式來停止模擬器。

##將應用程式發行至 Azure

在 Azure PowerShell 視窗中，使用 **Publish-azureserviceproject** 指令程式來部署至雲端服務應用程式

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

部署作業完成時，瀏覽器會開啟並顯示網頁。

![A web browser displaying the Express page. The URL indicates it is now hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## 後續步驟

如需詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

  [Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com

 


