<properties
    pageTitle="從命令列管理行動服務 | Microsoft Azure"
    description="了解如何使用命令列工具建立、部署和管理 Azure 行動服務。"
    services="mobile-services"
    documentationCenter="Mobile"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="NA"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="11/02/2015"
    ms.author="glenga"/>


# 使用命令列工具將行動服務自動化

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


## 概觀

本主題將示範如何使用 Azure 命令列工具將 Azure 行動服務的建立和管理自動化。 本主題將示範如何安裝並開始使用命令列工具，來執行重要的行動服務。

這些個別的命令結合為一個指令碼或批次檔後，即可將行動服務的建立、確認和刪除程序自動化。

本主題涵蓋 Azure 命令列工具支援的一些常用管理工作。 如需詳細資訊，請參閱 [Azure 命令列工具文件 ][reference-docs]。

## 安裝 Azure 命令列工具

下列清單包含安裝命令列工具的相關資訊，視作業系統而定：

* **Windows**: 下載 [Azure 命令列工具安裝程式 ][windows-installer]。 開啟已下載的 .msi 檔案，並依照提示完成安裝步驟。

* **Mac**: 下載 [Azure SDK 安裝程式 ][mac-installer]。 開啟已下載的 .pkg 檔案，並依照提示完成安裝步驟。

* **Linux**: 安裝最新版 [Node.js ][nodejs-org] (請參閱 [透過封裝管理員 ][install-node-linux])，然後執行下列命令:

    npm install azure-cli -g

若要測試安裝，請在命令提示字元中輸入 `azure`。 安裝成功時，您會看到所有可用的 `azure` 命令的清單。

## 如何下載和匯入發行設定

若要開始進行，您必須先下載並匯入發行設定。 接著您可以使用工具來建立和管理 Azure 服務。 若要下載發行設定，請使用 `account download` 命令：

    azure account download

這會開啟您的預設瀏覽器，提示您登入 Azure 傳統入口網站。 登入之後，就會下載您的 `.publishsettings` 檔案。 請記下此儲存的檔案位置。

接下來，匯入 `.publishsettings` 檔案執行下列命令，來取代 `< 路徑-至-設定-檔案 >` 的路徑與您 `.publishsettings` 檔案:

    azure account import <path-to-settings-file>

您可以移除所有儲存的資訊 <code>匯入</code> 命令，以使用 <code>帳戶清除</code> 命令:

    azure account clear

若要查看 `account` 命令的選項清單，請使用 `-help` 選項：

    azure account -help

匯入發行設定之後，基於安全的考量，請刪除 `.publishsettings` 檔案。 如需詳細資訊，請參閱 [如何安裝適用於 Mac 和 Linux 的 Azure 命令列工具]。 您現在即可開始從命令列或批次檔建立和管理 Azure 行動服務。

## 如何建立行動服務

您可以使用命令列工具建立新的行動服務執行個體。 建立行動服務時，您也可以在新的伺服器中建立 SQL Database 執行個體。

下列命令建立新的行動服務執行個體，在您的訂閱，其中 `< 服務名稱 >` 是新的行動服務名稱 `< 伺服器管理員 >` 是新的伺服器登入名稱和 `< 伺服器密碼 >` 是新的登入的密碼:

    azure mobile create <service-name> <server-admin> <server-password>

當指定的行動服務存在時，`mobile create` 命令將失敗。 在您的自動化指令碼中，您應該嘗試先刪除再重新建立行動服務。

## 如何列出訂閱中現有的行動服務

> [AZURE.NOTE] 在 CLI 中與 "list" 和 "script" 相關的命令只能用於 JavaScript 後端。

下列命令將傳回 Azure 訂閱中所有行動服務的清單：

    azure mobile list

此命令也將顯示每個行動服務的目前狀態和 URL。

## 如何刪除現有行動服務

您可以使用命令列工具刪除現有行動服務以及相關的 SQL Database 和 SQL Server。下列命令會刪除行動服務，其中 `< 服務名稱 >` 是要刪除之行動服務名稱:

    azure mobile delete <service-name> -a -q

這個命令也可以加上 `-a` 和 `-q` 參數，以直接刪除行動服務使用的 SQL Database 和 SQL Server，而不顯示提示。
> [AZURE.NOTE] 如果您未指定 <code>-q</code> 參數以及 <code>-</code> 或 <code>-d</code>, ，暫停執行，而且系統會提示您對 SQL Database 選取刪除選項。 只使用 <code>-</code> 在沒有其他服務使用資料庫或伺服器的參數; 否則請使用 <code>-d</code> 參數僅刪除屬於所要刪除行動服務的資料。

## 如何在行動服務中建立資料表

下列命令建立資料表中指定的行動服務，其中 `< 服務名稱 >` 是行動服務的名稱和 `< 資料表名稱 >` 是要建立之資料表的名稱:

    azure mobile table create <service-name> <table-name>

這會建立新的資料表的預設權限， `應用程式`, ，資料表作業: `插入`, ，`讀取`, ，`更新`, ，和 `刪除`。

下列命令將建立新資料表，並使該資料表具有公用 `read` 權限和僅授予管理員的 `delete` 權限：

    azure mobile table create <service-name> <table-name> -p read=public,delete=admin

下表顯示指令碼權限值的權限中的值 [Azure 傳統入口網站]。

|指令碼值 |入口網站值 |
|========|========|
|`公用`|Everyone|
|`應用程式`(預設值) |應用程式 key| 具有的任何人
|`user`|只有已驗證的使用者 |
|`admin`|僅指令碼和管理員 |

當指定的資料表已經存在時，`mobile create` 命令將失敗。 在您的自動化指令碼中，您應該嘗試先刪除再重新建立資料表。

## 如何列出行動服務中現有的資料表

下列命令會傳回所有資料表的清單中的行動服務，其中 `< 服務名稱 >` 是行動服務的名稱:

    azure mobile table list <service-name>

此命令也顯示每個資料表上的索引數，以及目前在資料表中的資料列數。

## 如何刪除行動服務中的現有資料表

下列命令會刪除資料表，從行動服務，其中 `< 服務名稱 >` 是行動服務的名稱和 `< 資料表名稱 >` 是要刪除的資料表名稱:

    azure mobile table delete <service-name> <table-name> -q

在自動化指令碼中，使用 `-q` 參數以刪除資料表而不顯示阻礙執行的確認提示。

## 如何將指令碼註冊到資料表作業

下列命令會上傳並註冊至資料表作業的函式， `< 服務名稱 >` 的行動服務中，名稱 `< 資料表名稱 >` 是資料表的名稱和 `< 作業 >` 是資料表作業，它可以是 `讀取`, ，`插入`, ，`更新`, ，或 `刪除`:

    azure mobile script upload <service-name> table/<table-name>.<operation>.js

請注意，此作業將從本機電腦上傳 JavaScript (.js) 檔案。 必須由資料表和作業名稱組成的檔案名稱和檔案必須位在 `資料表` 相對於執行命令所在位置的子資料夾。 例如，下列作業將上傳並註冊屬於 `TodoItems` 資料表的新 `insert` 指令碼。

    azure mobile script upload todolist table/todoitems.insert.js

指令碼檔案中的函數宣告也必須符合註冊的資料表作業。 這表示，對於 `insert` 指令碼而言，上傳的指令碼要包含具有下列特徵碼的函數：

    function insert(item, user, request) {
        ...
    }

如需關於註冊指令碼的詳細資訊，請參閱 [行動服務伺服器指令碼參考]。
















[download and install the command-line tools]: #install 
[download and import publish settings]: #import 
[create a new mobile service]: #create-service 
[get the master key]: #get-master-key 
[create a new table]: #create-table 
[register a new table script]: #register-script 
[delete an existing table]: #delete-table 
[delete an existing mobile service]: #delete-service 
[test the mobile service]: #test-service 
[list mobile services]: #list-services 
[list tables]: #list-tables 
[next steps]: #next-steps 
[mobile services server script reference]: http://go.microsoft.com/fwlink/p?LinkId=262293 
[azure classic portal]: https://manage.windowsazure.com/ 
[nodejs-org]: http://nodejs.org/ 
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager 
[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249 
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464 
[reference-docs]: http://azure.microsoft.com/documentation/articles/virtual-machines-command-line-tools/#Commands_to_manage_mobile_services 
[how to install the azure command-line tools for mac and linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795 

