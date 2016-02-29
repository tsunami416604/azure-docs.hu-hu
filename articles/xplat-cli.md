<properties
    pageTitle="適用於 Mac、Linux 和 Windows 的 Azure CLI"
    description="安裝和設定適用於 Mac、Linux 和 Windows 的 Azure CLI 來管理 Azure 服務"
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services=""/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/10/2015"
    ms.author="rasquill"/>

# 安裝和設定 Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure CLI](xplat-cli-install.md)

Azure CLI 提供您一組開放原始碼的跨平台命令集合，供您運用在 Azure 平台上。 Azure CLI 與 Azure 入口網站上提供的功能大多相同，例如管理網站、虛擬機器、行動服務、SQL Database 與 Azure 平台提供的其他各項服務等。

Azure CLI 是以 JavaScript 所撰寫，而且需要 Node.js。 它是使用 Azure SDK for Node.js 實作，並在 Apache 2.0 授權下發佈。 專案儲存機制位於 [https://github.com/azure/azure-xplat-cli](https://github.com/azure/azure-xplat-cli)。

本文件說明如何安裝與設定適用於 Mac、Linux 和 Windows 的 Azure CLI，以及如何使用它在 Azure 平台上執行基本工作。

<a id="install"></a>
## 如何安裝 Azure CLI

若要了解 Azure CLI 的安裝步驟，請參閱 [安裝 Azure CLI](xplat-cli-install.md) 頁面。


<a id="configure"></a>
## 如何連線至您的 Azure 訂用帳戶

雖然 Azure CLI 提供的某些命令在沒有 Azure 訂用帳戶的情況下仍可運作，們大多數命令仍需要訂用帳戶。 若要了解如何設定 Azure CLI 以搭配您的訂閱，請造訪 [從 Azure CLI 連接至 Azure 訂用帳戶](xplat-cli-connect.md)。


<a id="use"></a>
## 如何使用 Azure CLI

使用 `azure` 命令存取 Azure CLI。 如果要檢視可用的命令清單，請使用 `azure` 命令並且不指定任何參數。 您應該會看到類似以下的說明資訊：

    info:             _    _____   _ ___ ___
    info:            /_\  |_  / | | | _ \ __|
    info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
    info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
    info:       (_______ _ _)         _ ______ _)_ _
    info:              (______________ _ )   (___ _ _)
    info:
    info:    Microsoft Azure: Microsoft's Cloud Platform
    info:
    info:    Tool version 0.8.10
    help:
    help:    Display help for a given command
    help:      help [options] [command]
    help:
    help:    Opens the portal in a browser
    help:      portal [options]
    help:
    help:    Commands:
    help:      account        Commands to manage your account information and publish settings
    help:      config         Commands to manage your local settings
    help:      hdinsight      Commands to manage your HDInsight accounts
    help:      mobile         Commands to manage your Mobile Services
    help:      network        Commands to manage your Networks
    help:      sb             Commands to manage your Service Bus configuration
    help:      service        Commands to manage your Cloud Services
    help:      site           Commands to manage your Web Sites
    help:      sql            Commands to manage your SQL Server accounts
    help:      storage        Commands to manage your Storage objects
    help:      vm             Commands to manage your Virtual Machines
    help:
    help:    Options:
    help:      -h, --help     output usage information
    help:      -v, --version  output the application version

以上所列的頂層命令包含可與特定 Azure 區域搭配使用的命令。 例如，`azure account` 命令包含與您的 Azure 訂用帳戶相關的命令，像是先前使用的 `download` 和 `import` 設定。 如需可用的命令和選項的詳細資訊，請參閱 [使用適用於 Mac、Linux 和 Windows 的 Azure CLI]。

大多數命令都採用 `azure <command> <operation> [parameters]` 的格式，並對服務或物件 (例如您的帳戶組態) 執行作業。 其他命令提供子命令，並採用 `azure <command> <subcommand> <operation> [parameters]` 格式。 以下列舉可與您的帳戶組態搭配運作的命令範例：

* 若要檢視您所匯入的訂用帳戶，請使用：

        azure account list

* 如果您已匯入訂用帳戶，請使用以下命令，將其中一個訂用帳戶設為預設值：

        azure account set <subscription>

`--help` 或 `-h` 參數可用於檢視特定命令的說明。 或者，`azure help [command] [options]` 格式也可以用來傳回相同的資訊。 例如，以下命令全部都會傳回相同的資訊：

    azure account set --help

    azure account set -h

    azure help account set

當您不確定命令所需的參數時，請使用 `--help`、`-h` 或 `azure help [command]` 來查閱說明。

### 設定組態模式

Azure CLI 可讓您執行個別的管理作業 _資源_, ，這是使用者管理的實體，例如資料庫伺服器、 資料庫或網站。 這是預設的 Azure CLI 中的作業模式，稱為 **Azure 服務管理**。 不過，當您使用內含多個資源的複雜解決方案時，能夠將整個解決方案當成同一個單元來管理會讓您輕鬆不少。

若要支援管理資源的一組以單一邏輯單元或 _資源群組_, ，我們推出了預覽 **資源管理員** 做為管理 Azure 資源的全新方式。

>[AZURE.NOTE] 資源管理員目前為預覽狀態，並不提供與 Azure 服務管理相同等級的管理功能。

為了支援新的資源管理員，Azure CLI 可讓您使用 `azure config mode` 命令切換這些管理「模式」。

Azure CLI 預設為 Azure 服務管理模式。 若要切換至資源管理員模式，請使用下列方式來啟用命令：

    azure config mode arm

若要切換回 Azure 服務管理模式，請使用下列命令：

    azure config mode asm

>[AZURE.NOTE] 資源管理員模式與 Azure 服務管理模式是互斥的。 亦即，任一模式所建立的資源，將無法由另一種模式來管理。

如需有關如何使用 Azure CLI 使用資源管理員的詳細資訊，請參閱 [使用 Azure CLI 與資源管理員][cliarm]。

### 在 Azure 服務管理模式中使用服務

Azure CLI 可讓您輕鬆地管理 Azure 服務。 在下列範例中，您將學習如何使用 Azure CLI 管理 Azure 網站。

1. 使用以下命令建立新的 Azure 網站。 取代 **mywebsite** 具有唯一名稱。

        azure site create mywebsite

    系統會提示您指定將會建立網站的區域。 請選取在地理上靠近您的區域。 此命令完成後，網站將會在 http://mywebsite.azurewebsites.net (取代 **mywebsite** 與您指定的名稱。)

    > [AZURE.NOTE] 如果您使用 Git 控制專案來源，您可以指定 `--git` 參數，以在 Azure 上建立 Git 儲存機制，此網站。 這麼做也會在執行該命令的目錄中初始化 Git 儲存機制 (如果還沒有儲存機制的話)。 它也會建立名為的 Git 遠端 __azure__, ，這可以用來將部署推送至 Azure 網站使用 `git push azure master` 命令。

    > [AZURE.NOTE] 如果您收到錯誤指出 'site' 不是 azure 命令，Azure CLI 是很可能處於資源群組模式。 如果要切換回資源模式，請使用 `azure config mode asm` 命令。

2. 使用下列命令可列出您所訂用帳戶的網站：

        azure site list

    該清單應該會包含上一個步驟中所建立的網站。

2. 使用下列命令可停止該網站。 取代 **mywebsite** 加上網站名稱。

        azure site stop mywebsite

    在命令完成後，您可以重新整理瀏覽器以確認網站已經停止。

3. 使用下列命令可啟動該網站。 取代 **mywebsite** 加上網站名稱。

        azure site start mywebsite

    在命令完成後，您可以重新整理瀏覽器以確認網站已經啟動。

4. 使用下列命令可刪除該網站。 取代 **mywebsite** 加上網站名稱。

        azure site delete mywebsite

    當此命令完成之後，請使用 `azure site list` 命令確認該網站不再存在。

<a id="script"></a>
## 如何將適用於 Mac、Linux 和 Windows 的 Azure CLI 編寫成指令碼

您不但可以使用 Azure CLI 手動發出命令，還可以運用您的命令列轉譯器與系統提供的其他命令列公用程式等各項功能，建立複雜的自動化工作流程。 例如下列命令可停止所有執行中的 Azure 網站：

    azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

此範例會輸送一份網站清單給 `grep` 命令，由其檢查每一行有無字串 "Running"。 所有符合的字行都會輸送給 `awk` 命令，由其呼叫 `azure site stop` 並使用傳遞給它的第二個資料行 (執行中之網站的名稱)，做為要停止的網站名稱。

雖然此範例示範了如何將命令串連在一起，您也可以使用由您的命令列轉譯器所提供的指令碼撰寫功能，建立更精細的指令碼。 不同的命令列轉譯器具有不同的指令碼撰寫功能與語法。 Bash 可能是最廣泛運用在 UNIX 架構系統 (包括 Linux 與 OS X) 上的命令列轉譯器。

如需使用 Bash 撰寫指令碼的詳細資訊，請參閱 [進階的 Bash 指令碼指南][advanced-bash]。

一般指令碼 OS X 或 Linux 型系統的詳細資訊，請參閱 [殼層指令碼][script]。

如需使用批次檔的 Windows 架構系統指令碼，請參閱 [命令列參照 A-Z][batch]。

### 了解結果

建立指令碼時，通常您需要擷取命令輸出並將其傳遞至另一個命令，或是對輸出執行操作，例如檢查特定值。 Azure CLI 會將輸出產生至 STDOUT 與 STDERR。 標以前置詞 `info:` 的字行為資訊狀態訊息；標以 `data:` 的字行為服務相關的傳回資料；您也可使用 `--verbose` 或 `-v` 參數，指示 Azure CLI 傳回更多的詳細資訊。 這會傳回更多的資訊，並標以前置詞字串 `verbose:`。

例如，下列輸出由 `azure site list` 命令傳回：

    info:    Executing command site list
    + Enumerating sites
    data:    Name           Status   Mode  Host names
    data:    -------------  -------  ----  -------------------------------
    data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
    info:    site list command OK

如有指定 `--verbose` 或 `-v` 參數，將會傳回類似如下的資訊：

    info:    Executing command site list
    verbose: Subscription ####################################
    verbose: Enumerating sites
    verbose: [
    verbose:     {
    verbose:         ComputeMode: 'Shared',
    verbose:         HostNameSslStates: {
    verbose:             HostNameSslState: [
    verbose:                 {
    verbose:                     IpBasedSslResult: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     SslState: 'Disabled',
    verbose:                     ToUpdateIpBasedSsl: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     VirtualIP: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     Thumbprint: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     ToUpdate: {
    verbose:                         $: { i:nil: 'true' }
    verbose:                     },
    verbose:                     Name: 'myawesomesite.azurewebsites.net'
    verbose:                 },
    ...
    verbose:     }
    verbose: ]
    data:    Name           Status   Mode  Host names
    data:    -------------  -------  ----  -------------------------------
    data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
    info:    site list command OK

請注意，`verbose:` 資訊的外觀與 JSON 格式的資料類似。 您可以使用 `--json` 參數傳回 JSON 格式的資訊，如果您正在使用的公用程式原本就了解 JSON，例如 [jsawk](https://github.com/micha/jsawk) 或 [jq](http://stedolan.github.io/jq/)。 例如：

    azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q

上列命令會擷取如 JSON 的網站清單，接著使用 jsawk 擷取網站名稱，最後再使用 xargs 將網站名稱傳遞為參數，對每一個網站執行 site delete 命令。

>[AZURE.NOTE]  `--json` 參數會造成產生狀態或資料資訊 (前置詞為 `info:` 和 `data:`)。 例如，如果搭配使用 `--json` 參數與 `azure site create`，將不會傳回任何輸出，因此此命令只會傳回 `info:` 的資料。

### 處理錯誤

雖然 Azure CLI 確實將錯誤資訊記錄到 STDERR，可能也要記錄之錯誤的其他資訊 **azure.err** 指令碼的目錄中的檔案執行。 如果資訊記錄到這個檔案，則會將下列字行寫入 STDOUT：

    info:    Error information has been recorded to azure.err

### 結束狀態

如果必要的參數遺失的話，某些 Azure CLI 命令就不會傳回非零的結束狀態。 反之，它們將提示使用者輸入。 例如，使用 `azure site create` 命令建立新網站時，如果未指定任何網站名稱或 `--location` 參數，將會提示您提供這些值。

如果您正在撰寫仰賴結束狀態的指令碼，請確認您所使用的 Azure CLI 命令不會提示使用者輸入。

<a id="additional-resources"></a>

## 其他資源

* [搭配使用 Azure CLI 與服務管理][Using the Azure CLI]

* [將 Azure CLI 和資源管理員搭配使用][cliarm]

* 如需有關 Azure CLI，若要下載來源程式碼、 報告問題，或是對專案發表意見，請造訪 [Azure CLI 的 GitHub 儲存機制](https://github.com/azure/azure-xplat-cli)。

* 如果您遇到問題，使用 Azure CLI 或 Azure，請造訪 [Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home)。

* 如需 Azure 的詳細資訊，請參閱 [http://azure.microsoft.com/](http://azure.microsoft.com)。




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert

[Azure Web Site]: ../media/freetrial.png
[select a preview feature]: ../media/antares-iaas-preview-02.png
[select subscription]: ../media/antares-iaas-preview-03.png
[free-trial]: http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script]: http://en.wikipedia.org/wiki/Shell_script
[batch]: http://technet.microsoft.com/library/bb490890.aspx
[cliarm]: xplat-cli-azure-resource-manager.md
[portal]: https://manage.windowsazure.com
[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[Using the Azure CLI]: virtual-machines-command-line-tools.md

