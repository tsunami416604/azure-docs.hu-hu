<properties pageTitle="使用 Node.js 模組" description="了解如何在使用 Azure 網站或雲端服務時使用 Node.js 模組。" services="" documentationCenter="nodejs" authors="rmcmurray" manager="wpickett" editor=""/>

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="11/20/2015" ms.author="robmcm"/>






# 使用 Node.js 模組與 Azure 應用程式搭配

本文提供有關使用 Node.js 模組與 Azure 上代管之應用程式搭配的指引。 它提供有關確保應用程式使用模組特定版本，以及搭配原生模組與 Azure 使用的指引。

如果您已熟悉使用 Node.js 模組、**package.json** 和 **npm-shrinkwrap.json** 檔案，則下列是本文中討論內容的快速摘要：

* Azure 網站熟悉 **package.json** 和 **npm-shrinkwrap.json** 檔案，並可根據這些檔案中的項目安裝模組。
* Azure 雲端服務期望在開發環境上安裝所有模組和 **node\_modules** 目錄會將部署套件的一部分。

> [AZURE.NOTE] 本文中將不會討論 Azure 虛擬機器，因為 VM 中的部署經驗會視虛擬機器所代管的作業系統而定。

> [AZURE.NOTE] 提供支援使用 Azure 上的 **package.json** 或 **npm-shrinkwrap.json** 檔案來安裝模組是有可能的，不過，這會需要自訂雲端服務專案所使用的預設指令碼。 如需如何完成這項作業的範例，請參閱 [Azure 啟動工作執行 npm 安裝以避免部署節點模組](http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure)

## Node.js 模組

模組是指可載入的 JavaScript 封裝，可為您的應用程式提供特定功能。 模組的安裝方式通常是使用 **npm** 命令列工具，不過，也有一些模組 (例如 http 模組) 會以核心 Node.js 封裝的一部分提供。

當安裝模組時，它們會儲存在 **node\_modules** 目錄應用程式目錄結構的根目錄。 每個模組內 **node\_modules** 目錄都會維護它自己 **node\_modules** 包含任何模組而定，且這會一直重複一次針對每個模組相依性鏈結中一路向下的目錄。 這可讓每個已安裝模組都有它自己所相依的模組版本需求，不過，它會產生相當大的目錄結構。

部署時 **node\_modules** 目錄作為應用程式的一部分，它會增加部署相較於使用的大小 **package.json** 或 **npm-shrinkwrap.json** ; 不過，它可以確實保證用於生產環境中的模組版本與開發中所用相同的檔案。

### 原生模組

雖然大多數的模組是簡單的純文字 JavaScript 檔案，有些模組卻是平台特定的二進位影像。 這些模組會在安裝時編譯，通常是採用 Python 和 node-gyp。 由於 Azure 雲端服務仰賴 **node\_modules** 資料夾當作應用程式的任何原生模組包括為安裝模組一部分的一部分進行部署應該使用雲端服務中，只要它是安裝與編譯的 Windows 開發系統上。

Azure 網站不支援所有的原生模組，而且在編譯具有非常特定必要元件的原生模組時可能會失敗。 雖然某些熱門模組 (如 MongoDB) 具有選擇性原生相依性，而且沒有這些相依性仍照常運作，但兩種因應措施成功證明目前可使用幾乎所有的原生模組：

* 在已安裝所有原生模組之必要元件的 Windows 電腦上執行 **npm install**。 接著，將部署建立 **node\_modules** 資料夾作為應用程式至 Azure 網站的一部分。
* Azure 網站可以設定為在部署期間執行自訂 bash 或 shell 指令碼，讓您有機會執行自訂命令以及精確第設定 **npm install** 的執行方式。 示範如何執行這項操作的影片，請參閱 [自訂網站部署指令碼使用 Kudu]。

### 使用 package.json 檔案

**Package.json** 檔案就是如何指定應用程式，以便主控平台可安裝相依性，而不需要您將包含所需的最上層相依性 **node\_packages** 資料夾作為部署的一部分。 在部署應用程式之後，您可使用 **npm install** 命令，來剖析 **package.json** 檔案並安裝所有列出的相依性。

開發期間，當安裝模組將模組項目自動新增至 **package.json** 檔案時，您可以使用 **--save**、**--save-dev** 或 **--save-optional** 參數。 如需詳細資訊，請參閱 [npm 安裝](https://npmjs.org/doc/install.html)。

有關 **package.json** 檔案的一個潛在問題是它只指定最上層相依性的版本。 每個已安裝模組不一定會指定它所相依的模組版本，而且您最終得到的相依性鏈結可能與在開發中所用的不同。
> [AZURE.NOTE]
> 當部署到 Azure 網站時，如果您 <b>package.json</b> 檔案參考原生模組，以發行應用程式使用 Git 時，您會看到類似下面的錯誤:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
>    
>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
>


### 使用 npm-shrinkwrap.json 檔案

**npm-shrinkwrap.json** 檔案嘗試解決 **package.json** 檔案的模組版本設定限制。 **package.json** 檔案只包含最上層模組的版本，而 **npm-shrinkwrap.json** 檔案包含完整模組相依性鏈結的版本需求。

當您的應用程式準備好開始生產時，您可以鎖定版本需求，並使用 **npm shrinkwrap** 命令建立 **npm-shrinkwrap.json** 檔案。 這會使用目前安裝中的版本 **node\_modules** 資料夾，並記錄到 **npm-shrinkwrap.json** 檔案。 在部署應用程式到主控環境之後，使用 **npm install** 命令來剖析 **npm-shrinkwrap.json** 檔案，並安裝所有列出的相依性。 如需詳細資訊，請參閱 [npm 安裝](https://npmjs.org/doc/install.html)。
> [AZURE.NOTE]
>當部署到 Azure 網站時，如果您 <b>npm-shrinkwrap.json</b> 檔案參考原生模組，以發行應用程式使用 Git 時，您會看到類似下面的錯誤:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'
>    
>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1
>


## 後續步驟

既然您了解如何搭配 Node.js 模組與 Azure，學習如何在 [指定 Node.js 版本]，[建置和部署 Node.js 網站] 和 [如何使用適用於 Mac 和 Linux 的 Azure 命令列介面]。

如需詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。


[specify the node.js version]: nodejs-specify-node-version-azure-apps.md 
[how to use the azure command-line interface for mac and linux]: xplat-cli-install.md 
[build and deploy a node.js web site]: web-sites-nodejs-develop-deploy-mac.md 
[node.js web application with storage on mongodb (mongolab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md 
[publishing with git]: web-sites-publish-source-control.md 
[build and deploy a node.js application to an azure cloud service]: cloud-services-nodejs-develop-deploy-app.md 
[custom website deployment scripts with kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/ 

