<properties
   pageTitle="部署內部部署 StorSimple 裝置 | Microsoft Azure"
   description="描述部署 StorSimple 裝置和服務的步驟與最佳做法。 (適用於 Microsoft Azure StorSimple .3 版本或更早版本。)"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/01/2015"
   ms.author="alkohli" />

# 部署內部部署 StorSimple 裝置

[AZURE.INCLUDE [storsimple-version-selector](../../includes/storsimple-version-selector.md)]

## 概觀

歡迎使用 Microsoft Azure StorSimple 裝置部署。 這些部署教學課程適用於 StorSimple 8000 系列發行版本、StorSimple 8000 系列 Update 0.1、StorSimple 8000 系列 Update 0.2 與 StorSimple 8000 系列 Update 0.3。 這一系列的教學課程說明如何設定 StorSimple 裝置，並包含設定檢查清單、設定必要條件以及詳細的設定步驟。


這些教學課程中的資訊均假設您已經檢閱安全性預防措施，並已打開 StorSimple 裝置包裝、裝上機架並接好纜線。 如果您仍然需要執行這些工作，開始檢閱 [安全性預防措施](storsimple-safety.md)。 視您的裝置型號而定，您接著可以依照下列指示打開包裝、掛接機架及連接纜線：

- [打開封裝、掛接機架，並將纜線接上 8100](storsimple-8100-hardware-installation.md)
- [打開封裝、掛接機架，並將纜線接上 8600](storsimple-8600-hardware-installation.md)

您必須需要有系統管理員權限，才能完成安裝和設定程序。 建議您在開始之前，檢閱設定檢查清單。 部署與設定程序可能需要一些時間才能完成。

> [AZURE.NOTE] 在 Microsoft Azure 網站上發行的 StorSimple 部署資訊適用於僅適用於 StorSimple 8000 系列裝置。 如需 7000 系列裝置的完整資訊，請移至 ︰ [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com)。 如需 7000 系列部署資訊，請參閱 [StorSimple 系統快速入門指南](http://onlinehelp.storsimple.com/111_Appliance/)。

## 部署步驟

請執行這些必要步驟來設定 StorSimple 裝置，並將它連接到 StorSimple Manager 服務。 除了這些必要步驟外，部署期間也會有一些您可能需要的選擇性步驟和程序。 逐步部署指出您應該執行各選擇性步驟的時機。


| 步驟                                                                                   | 說明                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **必要條件**                                                                      | 這些是針對將要進行的部署而需要完成的準備工作。                                                                                        |
| 部署設定檢查清單。                                                     | 使用此檢查清單來收集並記錄部署之前和部署期間的資訊。                                                                       |
| 部署必要條件。                                                               | 這些驗證環境準備好進行部署。                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **逐步部署**                                                                   | 需要執行這些步驟，才能在生產環境中部署您的 StorSimple 裝置。                                                                                      |
| 步驟 1：建立新的服務。                                                         | 設定雲端管理和 StorSimple 裝置的儲存體。 如果您現在已經有針對其他 StorSimple 裝置的服務，請略過此步驟。                |
| 步驟 2：取得服務註冊金鑰。                                               | 使用此金鑰註冊並將 StorSimple 裝置與管理服務連接。                                                                         |
| 步驟 3：透過 Windows PowerShell for StorSimple 設定和註冊裝置    | 將裝置連接到您的網路，並向 Azure 使用管理服務安裝程式。                                            |
| 步驟 4：完成最小量裝置設定</br>選擇性 ︰ 更新您的 StorSimple 裝置。      | 使用管理服務來完成裝置設定並啟用裝置以提供儲存體。                                                                      |
| 步驟 5：建立磁碟區容器。                                                      | 建立容器以佈建磁碟區。 磁碟區容器的儲存體帳戶、 頻寬和內含的所有磁碟區的加密設定。    |
| 步驟 6：建立磁碟區。                                                                | 在您伺服器的 StorSimple 裝置上佈建儲存體磁碟區。                                                                                        |
| 步驟 7 ︰ 掛接、 初始化及格式化磁碟區。</br>選用 ︰ 設定 MPIO。            | 將您的伺服器連接至裝置提供的 iSCSI 儲存體。 選擇性地設定 MPIO 確保您的伺服器可以容許連結、網路和介面失敗。                                                                                                                                                              |
| 步驟 8：進行備份。                                                                  | 設定備份原則以保護您的資料                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **其他程序**                                                                   | 在您部署解決方案時可能需要參考這些程序。                                                                                        |
| 針對服務設定新的儲存體帳戶。                                      |                                                                                                                                                               |
| 使用 PuTTY 連接到裝置序列主控台。                                    |                                                                                                                                                               |
| 取得 Windows Server 主機的 IQN。                                                   |                                                                                                                                                               |
| 建立手動備份。                                                                 |


## 部署設定檢查清單

下列部署設定檢查清單描述當您在設定 StorSimple 裝置上的軟體之前需要收集的資訊。 事先備妥部分的這些資訊可協助簡化在環境中部署 StorSimple 裝置的程序。 也請您使用此檢查清單記下您部署裝置時的設定詳細資訊。

| 階段                                  | 參數                                         | 詳細資料                                                                                                                                                                | 值 |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **將裝置接上纜線**                      | 序列存取                                     | 初始裝置組態                                                                  | 是/否 |
|   |   |  |  |
| **設定和註冊裝置**          | Data 0 網路設定                           | Data 0 IP 位址 ︰</br>子網路遮罩 ︰</br>閘道 ︰</br>主要 DNS 伺服器 ︰</br>主要 NTP 伺服器 ︰</br>Web proxy 伺服器 IP/FQDN （選用） ︰</br>Web proxy 連接埠 ︰|        |
|                                        | 裝置系統管理員密碼                     | 密碼必須介於 8 到 15 個字元之間，包含小寫字母、大寫字母、數字和特殊字元。 |        |
|                                        | StorSimple Snapshot Manager 密碼              | 密碼必須是 14 或 15 個字元，包含小寫字母、大寫字母、數字和特殊字元。|        |
|                                        | 服務註冊金鑰                          | 此金鑰是從 Azure 傳統入口網站產生。    |        |
|                                        | 服務資料加密金鑰                       | 當裝置透過 Windows PowerShell for StorSimple 註冊管理服務時會建立此金鑰。 複製這個金鑰，並將它儲存在安全的位置。|  |
|   |   |  |  |
| **完成最小裝置設定**          | 裝置的易記名稱                     | 這是裝置的描述性名稱。 |        |
|                                        | 時區                                          | 裝置將針對所有排程的操作使用這個時區。  |        |
|                                        | 次要 DNS 伺服器                              | 這是必要設定。                                  |        |
|                                        | 網路介面：Data 0 控制器固定 IP                                     | 這些 IP 必須可路由傳送到網際網路。</br>控制器 0 固定 IP 位址 ︰</br>控制器 1 固定 IP 位址 ︰|
|   |   |  |  |
| **其他的網路介面設定**  | 網路介面 ︰ Data 1</br>如果已啟用 iSCSI，請勿設定閘道。      | 用途 ︰ 定域機組/iSCSI 無法使用</br>IP 位址 ︰</br>子網路遮罩 ︰</br>閘道 ︰|
|                                        | 網路介面 ︰ Data 2</br>如果已啟用 iSCSI，請勿設定閘道。      | 用途 ︰ 定域機組/iSCSI 無法使用</br>IP 位址 ︰</br>子網路遮罩 ︰</br>閘道 ︰|
|                                        | 網路介面 ︰ 資料 3</br>如果已啟用 iSCSI，請勿設定閘道。      | 用途 ︰ 定域機組/iSCSI 無法使用</br>IP 位址 ︰</br>子網路遮罩 ︰</br>閘道 ︰|
|                                        | 網路介面 ︰ Data 4</br>如果已啟用 iSCSI，請勿設定閘道。      | 用途 ︰ 定域機組/iSCSI 無法使用</br>IP 位址 ︰</br>子網路遮罩 ︰</br>閘道 ︰|
|                                        | 網路介面 ︰ 資料 5</br>如果已啟用 iSCSI，請勿設定閘道。      | 用途 ︰ 定域機組/iSCSI 無法使用</br>IP 位址 ︰</br>子網路遮罩 ︰</br>閘道 ︰|
|   |   |  |  |
| **建立磁碟區容器**                      | 磁碟區容器名稱：                            | 容器名稱                                                                                                                                                 |        |
|                                        | Azure 儲存體帳戶：                            | 與此磁碟區容器相關的儲存體帳戶名稱和存取金鑰                                                                                              |        |
|                                        | 雲端儲存體加密金鑰：                     | 每個容器中儲存體的加密金鑰                                                                                                                           |        |
|   |   |  |  |
| **建立磁碟區**                        | 每個磁碟區的詳細資料                           | 磁碟區名稱：                                                                                                                                                           |        |
|                                        |                                                   | 大小：                                                                                                                                                                  |        |
|                                        |                                                   | 使用類型：                                                                                                                                                            |        |
|                                        |                                                   | ACR 名稱：                                                                                                                                                              |        |
|                                        |                                                   | 預設備份原則：                                                                                                                                                 |        |
|   |   |  |  |
| **掛接、初始化及格式化磁碟區** | 連接至儲存體的每個主機伺服器詳細資料 | Windows Server 名稱：                                                                                                                                                   |        |
|                                        |                                                   | Windows Server IQN：                                                                                                                                                    |        |
|                                        |                                                   | Windows Server 磁碟區名稱：                                                                                                                                                   |        |
|                                        |                                                   | NTFS 掛接點/磁碟機代號：                                                                                                                                      |        |

## 部署必要條件

下列各節說明 StorSimple Manager 服務、StorSimple 裝置，以及您資料中心網路的設定必要條件。

### 對於 StorSimple Manager 服務

在您開始前，請確定：

- 您擁有的 Microsoft 帳戶具有存取認證。

- 您擁有的 Microsoft Azure 儲存體帳戶具有存取認證。

- StorSimple Manager 服務已啟用您的 Microsoft Azure 訂用帳戶。 您應該要透過購買訂閱 [企業合約](http://azure.microsoft.com/pricing/enterprise-agreement/)。

- 您有權限可存取終端機模擬軟體，例如 PuTTY。

### 對於資料中心的裝置

在設定裝置前，請確認：

- 您已完全打開裝置包裝、掛接到機架上，並連接所有的電源、網路及序列存取纜線，如下所述：

    -  [打開封裝、掛接機架，並將纜線接上 8100 裝置](storsimple-8100-hardware-installation.md)
    -  [打開封裝、掛接機架，並將纜線接上 8600 裝置](storsimple-8600-hardware-installation.md)


### 針對資料中心內的網路

在您開始前，請確定：

- 資料中心防火牆中的連接埠已開放，以允許 iSCSI 和雲端流量中所述 [StorSimple 裝置的網路需求](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)。
- 資料中心內的裝置可以連線到外部網路。 執行下列命令 [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) cmdlet （下表顯示） 來驗證外部網路的連線。 在可連線至 Azure 和您將部署 StorSimple 裝置的電腦上 (於資料中心網路內) 執行此驗證。  

| 針對此參數…       | 檢查有效性...                                                                                                                                                                                | 執行這些命令/Cmdlet。                                                                                                                                                             |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **IP**</br>**子網路**</br>**閘道器** | 這是有效的 IPv4 或 IPv6 位址？</br>這是有效的子網路嗎？</br>這是有效的閘道嗎？</br>這是重複的 IP 網路上？                                                                          | `ping ip`</br>`arp -a`</br> `ping` 和 `arp` 命令應該會失敗，表示正在使用此 IP 資料中心網路中沒有任何裝置。
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **DNS**                       | 這是有效的 DNS 且可以解析 Azure URL 嗎？                                                                                                                    | `Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>可以使用替代命令為 ︰</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com`      |
|                           | 檢查連接埠 53 是否開啟。 只有在您為裝置使用外部 DNS 時才適用。 內部 DNS 應該會自動解析外部 URL。  | `Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[此 cmdlet 的詳細資訊](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/)|
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **NTP**                       | 我們會在 NTP 伺服器輸入時觸發時間同步處理。 請在您輸入 `time.windows.com` 或公用時間伺服器時檢查 UDP 連接埠 123 是否開啟)。 | [下載並使用這個指令碼](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca)。                                                                                                                                                           |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Proxy (選用)**          | 這是有效的 Proxy URI 和連接埠嗎？ </br> 是正確的驗證模式？                                                                                                                                | <code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>設定 web proxy 之後立即執行此命令。 如果傳回狀態碼 200，則表示連接成功。                                                                                                                                 |
|                           | 流量是否可透過 Proxy 路由？                                                                                                                                 | 在裝置上設定 Proxy 之後，請執行 DNS 驗證、NTP 檢查 或 HTTP 檢查。 這會清楚的反映流量是否在 Proxy 或其他地方遭到封鎖。                                                                                                                      |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **註冊**              | 檢查輸出 TCP 連接埠 443、80、9354 是否開啟。                                                                                                                |  `Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[測試 NetConnection 指令程式的詳細資訊](https://technet.microsoft.com/library/dn372891.aspx)                                                                           |

## 逐步部署

請在資料中心使用下列逐步指示來部署 StorSimple 裝置。

## 步驟 1：建立新的服務

StorSimple Manager 服務可以管理多個 StorSimple 裝置。 針對第一次的 StorSimple 裝置部署，您會需要建立新的 StorSimple Manager 服務。

> [AZURE.IMPORTANT] 如果您有現有的 StorSimple Manager 服務，而且您想要部署您的 StorSimple 裝置與該服務，請略過此步驟。

請執行下列步驟以建立 StorSimple Manager 服務的新執行個體。

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] 如果您未啟用自動建立儲存體帳戶與您的服務，您必須建立至少一個儲存體帳戶之後您已成功建立服務。 當您建立磁碟區容器時，將會使用此儲存體帳戶。
>
> 如果您未自動建立儲存體帳戶，請移至 [設定新的儲存體帳戶服務](#configure-a-new-storage-account-for-the-service) 如需詳細指示。
> 如果您啟用自動建立儲存體帳戶，請移至 [步驟 2 ︰ 取得服務註冊金鑰](#step-2:-get-the-service-registration-key)。

## 步驟 2：取得服務註冊金鑰

當 StorSimple Manager 服務啟動後處於執行中時，您就必須取得服務註冊金鑰。 這個金鑰是用來註冊和將 StorSimple 裝置與服務連接。

請在 Azure 傳統入口網站中執行下列步驟。

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## 步驟 3：透過 Windows PowerShell for StorSimple 設定和註冊裝置

> [AZURE.IMPORTANT] 之前執行這項設定，請拔除所有網路介面 DATA 0 以外 （主動和被動） 控制器。

您可以使用 Windows PowerShell for StorSimple 來完成 StorSimple 裝置的初始安裝，如下列程序所述。 您必須使用終端機模擬軟體來完成這個步驟。 如需詳細資訊，請參閱 [使用 PuTTY 來連接至裝置序列主控台](#use-putty-to-connect-to-the-device-serial-console)。

[AZURE.INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## 步驟 4：完成最小量裝置設定

為完成 StorSimple 裝置的最小量裝置設定，您必須：

- 設定次要 DNS 伺服器。
- 至少在一個網路介面上啟用 iSCSI。
- 針對兩個控制器指派固定的 IP 位址。

請在 Azure 傳統入口網站中執行下列步驟以完成最小量裝置設定。

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

裝置設定完成之後，您必須掃描是否有可用的更新並安裝。 更新可能需花費數小時才能完成。 依照 [掃描並套用更新](#scan-for-and-apply-updates)。


## 步驟 5：建立磁碟區容器

磁碟區容器具有其中所含之所有磁碟區的儲存體帳戶、頻寬及加密設定。 您必須建立磁碟區容器，才能開始在 StorSimple 裝置上佈建磁碟區。

請在 Azure 傳統入口網站中執行下列步驟以建立磁碟區容器。

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## 步驟 6：建立磁碟區

建立磁碟區容器之後，您就可以為伺服器在 StorSimple 裝置上佈建存放磁碟區。 請在 Azure 傳統入口網站中執行下列步驟以建立磁碟區。

> [AZURE.IMPORTANT] StorSimple Manager 可以建立精簡佈建的磁碟區。  您無法建立完整或部分佈建的磁碟機。

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## 步驟 7：掛接、初始化及格式化磁碟區

> [AZURE.IMPORTANT]

> - 為獲得 StorSimple 解決方案的高可用性，建議您先在 Windows Server 主機 (選用) 上設定 MPIO，再於 Windows Server 主機上設定 iSCSI。 主機伺服器上的 MPIO 設定會確保伺服器可以容許連結、網路，或介面失敗。

> - 如需 MPIO 和 iSCSI 安裝和組態指示，請移至 [為 StorSimple 裝置設定 MPIO](storsimple-configure-mpio-windows-server.md)。 其中也會包括掛接、初始化和格式化 StorSimple 磁碟區的步驟。

如果您決定不設定 MPIO，請執行下列步驟來掛接、初始化及格式化您的 StorSimple 磁碟區。

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## 步驟 8：進行備份

備份可提供磁碟區的時間點保護，並改善復原能力，同時讓還原時間降至最低。 您可以在 StorSimple 裝置上進行兩種備份類型： 本機快照與雲端快照。 每一種備份類型可以是 **排程** 或 **手動**。

請在 Azure 傳統入口網站中執行下列步驟，以建立排程備份。

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

您可以隨時進行手動備份。 如需程序，請移至 [建立手動備份](#Create-a-manual-backup)。

## 針對服務設定新的儲存體帳戶

這是選擇性步驟，只有當您並未啟用服務自動建立儲存體帳戶時才需要執行。 必須要有 Microsoft Azure 儲存體帳戶，才能建立 StorSimple 磁碟區容器。

如果您需要在不同區域建立 Azure 儲存體帳戶，請參閱 [關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md) 如需逐步指示。

上執行下列步驟在 Azure 傳統入口網站， **StorSimple Manager 服務** 頁面。

[AZURE.INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]


## 使用 PuTTY 連接到裝置序列主控台

若要連接到 Windows PowerShell for StorSimple，您需要使用終端機模擬軟體，例如 PuTTY。 您可以在存取裝置時，直接透過序列主控台或從遠端電腦開啟 Telnet 工作階段來使用 PuTTY。

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## 掃描並套用更新

更新裝置可能會花費 1 到 4 小時。 在裝置上執行下列步驟來掃描並套用更新。

> [AZURE.NOTE] 如果您有設定 Data 0 以外的網路介面上的閘道，您必須停用資料 2 和 Data 3 網路介面，然後再安裝更新。 移至 **裝置 > 設定** 和停用資料 2 和 Data 3 的介面。 裝置更新之後，您應該重新啟用這些介面。

#### 若要更新裝置
1.  在裝置上 **快速入門** 頁面上，按一下 **裝置**。 選取實體裝置，按一下 **維護** 然後按一下 [ **掃描更新**。  
2.  系統會建立掃描可用更新的工作。 如果有可用更新， **掃描更新** 變更為 **安裝更新**。 按一下 [ **安裝更新**。 系統可能會要求您在安裝更新前停用 Data 2 和 Data 3。 您必須停用這些網路介面，否則更新會失敗。
3.  更新工作將會建立。 監視的瀏覽至您的更新狀態 **工作**。

    > [AZURE.NOTE] 更新工作啟動時，它立即顯示狀態為 [50%。 只有在更新工作完成之後，狀態才會變更為 100 %。 更新程序沒有即時狀態。

4.  裝置成功更新之後，請啟用 Data 2 和 Data 3 網路介面 (如果已停用)。



## 取得 Windows Server 主機的 IQN

請執行下列步驟，以取得正在執行 Windows Server 2012 之 Windows 主機的 iSCSI 限定名稱 (IQN)。

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]


## 建立手動備份

請在 Azure 傳統入口網站中執行下列步驟，以針對 StorSimple 裝置上的單一磁碟區建立隨選手動備份。

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## 後續步驟

- 設定 [虛擬裝置](storsimple-virtual-device.md)。

- 使用 [StorSimple Manager 服務](https://msdn.microsoft.com/library/azure/dn772396.aspx) 來管理您的 StorSimple 裝置。


