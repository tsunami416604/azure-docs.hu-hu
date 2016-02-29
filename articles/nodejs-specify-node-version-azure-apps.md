<properties
    pageTitle="指定 Node.js 版本"
    description="了解如何指定 Azure 網站和雲端服務所使用的 Node.js 版本"
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="robmcm"/>

# 在 Azure 應用程式中指定 Node.js 版本

裝載 Node.js 應用程式時，您可能會想要確定應用程式是使用特定版本的 Node.js。 有數種方式可以為 Azure 上裝載的應用程式完成這項動作。

##預設版本

Azure 提供的 Node.js 版本會持續進行更新。 除非另有指定，否則會使用最新的版本。

> [AZURE.NOTE] 如果您要裝載您的應用程式在 Azure 雲端服務 (web 或背景工作角色)，而且它是第一次部署應用程式，Azure 就會嘗試使用的 Node.js 版本符合 Azure 上提供的預設版本，您已安裝在開發環境。

##以 package.json 進行版本設定

您可以指定要新增下列內容來使用的 Node.js 版本您 **package.json** 檔案:

    "engines":{"node":version}

其中 *版本* 是要使用的特定版本號碼。 您可以指定較複雜的條件，如版本，例如:

    "engines":{"node": "0.6.22 || 0.8.x"}

由於 0.6.22 不是主控環境中已提供的版本，因此將改用 0.8 系列可用的最高版本，也就是 0.8.4 版。

##版本控制網站與應用程式設定
如果您主控網站的應用程式，您可以設定環境變數 **WEBSITE_NODE_DEFAULT_VERSION** 所需的版本。 

##以 PowerShell 進行雲端服務版本設定

如果您要裝載在雲端服務中，應用程式，而且要部署應用程式使用 Azure PowerShell，您可以使用覆寫預設 Node.js 版本 **Set-azureserviceprojectrole** PowerShell cmdlet。 例如：

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

請注意上述陳述式的參數會區分大小寫。  您可以確認已選取正確的 Node.js 版本檢查 **引擎** 屬性，在您的角色 **package.json**。

您也可以使用 **Get-azureserviceprojectroleruntime** 擷取可用的雲端服務裝載的應用程式的 Node.js 版本清單。  務必確認您專案所依據的 Node.js 版本列在此清單中。

##對 Azure 網站使用自訂版本

雖然 Azure 提供數個預設 Node.js 版本，不過您可能會想要使用預設未提供的版本。 如果您的應用程式裝載為 Azure 網站，您可以完成這項作業使用 **iisnode.yml** 檔案。 下列步驟將逐步引導您對 Azure 網站使用自訂版本的 Node.Js：

1. 建立新的目錄，然後再建立 **server.js** 目錄內的檔案。  **Server.js** 檔案應該包含下列:

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    這將顯示當您瀏覽網站時會使用的 Node.js 版本。

2. 建立新網站並記下網站的名稱。 例如，以下使用 [Azure Command-line tools] 來建立新的 Azure 網站，名為 **mywebsite**, ，然後啟用網站的 Git 儲存機制。

        azure site create mywebsite --git

3. 建立新目錄 **bin** 為目錄的子系 **server.js** 檔案。

4. 下載的特定版本 **node.exe** (Windows 版)，您想要讓應用程式使用。 例如，以下使用 **curl** 下載 0.8.1 版:

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    儲存 **node.exe** 檔案 **bin** 先前建立的資料夾。

5. 建立 **iisnode.yml** 與相同的目錄中檔案 **server.js** 檔案，，然後加入下列內容 **iisnode.yml** 檔案:

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    這個路徑 **node.exe** 一旦發行至 Azure 網站的應用程式專案中的檔案會位於位置。

6. 發行您的應用程式。 例如，由於我稍早是使用 --git 參數建立新的網站，下列命令會將應用程式檔案新增到我的本機 Git 存放庫，然後將這些檔案推播到網站存放庫：

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    發行應用程式後，使用瀏覽器開啟網站。 您應該會看見 "Hello from Azure running node version: v0.8.1" 訊息。

##後續步驟

現在您已了解如何指定應用程式所使用的 Node.js 版本，請了解如何 [work with modules], ，[build and deploy a Node.js Web Site], ，和 [How to use the Azure Command-Line Tools for Mac and Linux]。

如需詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

[How to use the Azure Command-Line Tools for Mac and Linux]: xplat-cli-install.md
[Azure Command-line tools]: xplat-cli-install.md
[work with modules]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: web-sites-nodejs-develop-deploy-mac.md

