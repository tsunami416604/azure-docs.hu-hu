<properties
    pageTitle="Azure 備份 - 使用 PowerShell 部署和管理 DPM 的備份 | Microsoft Azure"
    description="了解如何使用 PowerShell 部署和管理 Data Protection Manager (DPM) 的 Azure 備份"
    services="backup"
    documentationCenter=""
    authors="AnuragMehrotra"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/26/2015"
    ms.author="jimpark"; "aashishr"; "sammehta"; "anuragm"/>


# 使用 PowerShell 部署和管理 Data Protection Manager (DPM) 伺服器的 Azure 備份
本文說明如何使用 PowerShell 來設定 DPM 伺服器上的 Azure 備份以及管理備份和復原。

## 設定 PowerShell 環境
在可以使用 PowerShell 來管理 Data Protection Manager 的 Azure 備份之前，您必須在 PowerShell 中具備適當的環境。 在 PowerShell 工作階段開始時，請確定您執行下列命令來匯入正確的模組並可讓您正確地參考 DPM Cmdlet：

```
PS C:> & "C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin\DpmCliInitScript.ps1"

Welcome to the DPM Management Shell!

Full list of cmdlets: Get-Command
Only DPM cmdlets: Get-DPMCommand
Get general help: help
Get help for a cmdlet: help <cmdlet-name> or <cmdlet-name> -?
Get definition of a cmdlet: Get-Command <cmdlet-name> -Syntax
Sample DPM scripts: Get-DPMSampleScript
```

## 設定和註冊
開始：

1. [下載最新的 PowerShell](https://github.com/Azure/azure-powershell/releases) (所需的最低版本是 ︰ 1.0.0)
2. 啟用 Azure 備份指令程式，藉由切換至 *AzureResourceManager* 模式使用 **Switch-azuremode** 指令程式 ︰

```
PS C:\> Switch-AzureMode AzureResourceManager
```

PowerShell 可以自動化下列設定和註冊工作：

- 建立備份保存庫
- 安裝 Azure 備份代理程式
- 向 Azure 備份服務進行註冊
- 網路設定
- 加密設定

### 建立備份保存庫

> [AZURE.WARNING] 第一次使用 Azure Backup 的客戶，您需要註冊 Azure 備份提供者來搭配您的訂閱。 這可以透過執行下列命令來完成：Register-AzureProvider -ProviderNamespace "Microsoft.Backup"

您可以建立新的備份保存庫使用 **新增 AzureRMBackupVault** 指令程式。 備份保存庫是 ARM 資源，因此您必須將它放在資源群組內。 在提高權限的 Azure PowerShell 主控台中，執行下列命令：

```
PS C:\> New-AzureResourceGroup –Name “test-rg” -Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GRS
```

您可以取得一份在給定的訂閱使用的所有備份保存庫 **Get AzureRMBackupVault** 指令程式。


### 在 DPM 伺服器上安裝 Azure 備份代理程式
在安裝 Azure 備份代理程式之前，您必須在 Windows Server 上下載並提供安裝程式。 您可以取得最新版本的安裝程式從 [Microsoft 下載中心](http://aka.ms/azurebackup_agent) 或從備份保存庫儀表板] 頁面。 安裝程式儲存至容易存取的位置，例如 * C:\Downloads\*。

若要安裝代理程式，請執行下列命令在提高權限的 PowerShell 主控台 **DPM 伺服器上**:

```
PS C:\> MARSAgentInstaller.exe /q
```

這會以所有預設選項安裝代理程式。 安裝作業會在背景中進行幾分鐘。 如果您未指定 */nu* 選項 **Windows Update** 檢查是否有任何更新安裝結束時會開啟視窗。

代理程式會顯示在已安裝的程式清單中。 若要查看已安裝的程式清單，請移至 **控制台** > **程式** > **程式和功能**。

![已安裝代理程式](./media/backup-dpm-automation/installed-agent-listing.png)

#### 安裝選項
若要查看所有可透過命令列執行的選項，請使用下列命令：

```
PS C:\> MARSAgentInstaller.exe /?
```

可用的選項包括：

| 選項 | 詳細資料 | 預設值 |
| ---- | ----- | ----- |
| /q | 無訊息安裝 | - |
| /p: 「 位置 」 | Azure 備份代理程式的安裝資料夾的路徑。 | C:\Program Files\Microsoft Azure Recovery Services 代理程式 |
| /s: 「 位置 」 | Azure 備份代理程式的快取資料夾的路徑。 | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m | 參加 Microsoft update | - |
| /nu | 不檢查更新安裝完成之後 | - |
| /d | 解除安裝 Microsoft Azure 復原服務代理程式 | - |
| /ph | Proxy 主機位址 | - |
| /po | Proxy 主機連接埠號碼 | - |
| /pu | Proxy 主機使用者名稱 | - |
| /pw | Proxy 密碼 | - |

### 向 Azure 備份服務進行註冊
您可以向 Azure 備份服務之前，您必須確定 [必要條件](backup-azure-dpm-introduction.md) 符合。 您必須：

- 具備有效的 Azure 訂用帳戶
- 具備備份保存庫

若要下載的保存庫認證，請執行 **Get AzureBackupVaultCredentials** 在方便的位置在像是 Azure PowerShell 主控台和存放區中的指令程式 * C:\Downloads\*。

```
PS C:\> $credspath = "C:\"
PS C:\> $credsfilename = Get-AzureRMBackupVaultCredentials -Vault $backupvault -TargetLocation $credspath
PS C:\> $credsfilename
f5303a0b-fae4-4cdb-b44d-0e4c032dde26_backuprg_backuprn_2015-08-11--06-22-35.VaultCredentials
```

向保存庫註冊電腦是使用 [Start-dpmcloudregistration](https://technet.microsoft.com/library/jj612787) 指令程式 ︰

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath $cred
```

此命令會使用指定的保存庫認證向 Microsoft Azure 保存庫註冊名為 “TestingServer” 的 DPM 伺服器。

> [AZURE.IMPORTANT] 請勿使用相對路徑來指定保存庫認證檔。 您必須提供絕對路徑做為 Cmdlet 的輸入。

### 初始組態設定
一旦向 Azure 備份保存庫註冊 DPM 伺服器，就會使用預設的訂用帳戶設定開始。 這些訂閱設定包括網路、加密和臨時區域。 若要開始變更訂用帳戶設定，您需要先取得現有 （預設） 設定使用的控制代碼 [Get-dpmcloudsubscriptionsetting](https://technet.microsoft.com/library/jj612793) 指令程式 ︰

```
$setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

所有修改都會都對此本機 PowerShell 物件 ```$setting```  完整物件認可到 DPM 和 Azure 備份以加以儲存是使用 [Set-dpmcloudsubscriptionsetting](https://technet.microsoft.com/library/jj612791) 指令程式。 您必須使用 ```–Commit``` 旗標，以確保會保存所做的變更。 除非已認可，否則 Azure 備份將不會套用並使用設定。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

### 網路
如果 DPM 機器對在網際網路上的 Azure 備份服務的連線是透過 Proxy 伺服器，則應該提供 Proxy 伺服器設定，備份才能成功。 這由使用 ```-ProxyServer```, ，```-ProxyPort```, ，```-ProxyUsername``` 和 ```ProxyPassword``` 參數 [Set-dpmcloudsubscriptionsetting](https://technet.microsoft.com/library/jj612791) 指令程式。 本範例未使用 Proxy 伺服器，因此會明確地清除任何 Proxy 相關資訊。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoProxy
```

您也可以針對給定的一組當週天數，使用 ```-WorkHourBandwidth``` 和 ```-NonWorkHourBandwidth``` 的選項來控制頻寬使用情形。 本範例未設定任何節流。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -NoThrottle
```

### 設定臨時區域
在 DPM 伺服器上執行的 Azure 備份代理程式需要暫存儲存體，以供存放從雲端還原的資料 (本機臨時區域)。 設定臨時區域使用 [Set-dpmcloudsubscriptionsetting](https://technet.microsoft.com/library/jj612791) cmdlet 和 ```-StagingAreaPath``` 參數。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -StagingAreaPath "C:\StagingArea"
```

在上述範例中，將設定臨時區域以 *C:\StagingArea* PowerShell 物件中 ```$setting```。 請確保指定的資料夾已經存在，否則訂閱設定的最終認可將會失敗。


### 加密設定
傳送至 Azure 備份的備份資料會進行加密來保護資料的機密性。 加密複雜密碼是在還原時用來解密資料的「密碼」。 一旦設定，就請務必保管好這項資訊。

在下列範例中，第一個命令會將字串轉換 ```passphrase123456789``` 安全字串，並將指派給變數的安全字串名為 ```$Passphrase```。 第二個命令設定安全字串 ```$Passphrase``` 做為加密備份的密碼。

```
PS C:\> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force

PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -EncryptionPassphrase $Passphrase
```

> [AZURE.IMPORTANT] 安全一旦設定之後，保留複雜密碼資訊。 若沒有此複雜密碼，您將無法從 Azure 還原資料。

此時，您應該已對 ```$setting``` 物件進行所有必要的變更。 記得要認可變更。

```
PS C:\> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $setting -Commit
```

## 保護 Azure 備份的資料
在本節中，您會將實際執行伺服器加入至 DPM，然後保護資料到本機 DPM 存放區，然後到 Azure 備份。 在範例中，我們將示範如何備份檔案和資料夾。 您可以輕鬆地運用同樣的觀念，來備份任何 DPM 支援的資料來源。 所有 DPM 備份皆受到保護群組 (PG) 所控管，並且由四個部分構成：

1. **群組成員** 是一份所有可保護物件 (也稱為 *Datasources* 在 DPM 中) 您想要保護相同的保護群組中。 比方說，您可能想要保護一個保護群組的實際執行 VM 與另一個保護群組中的 SQL Server 資料庫，因為它們可能會有不同的備份需求。 在可以備份實際執行伺服器上的資料來源之前，您必須先確定 DPM 代理程式已安裝在伺服器上並受到 DPM 管理。 請遵循的步驟 [安裝 DPM 代理程式](https://technet.microsoft.com/library/bb870935.aspx) 並將它連結至適當的 DPM 伺服器。
2. **資料保護方式** 指定目標備份位置-磁帶、 磁碟和雲端。 在我們的範例中，我們將保護資料至本機磁碟以及雲端。
3. A **備份排程** 可指定何時進行備份，以及資料應該同步處理頻率 DPM 伺服器和實際執行伺服器之間。
4. A **保留排程** 可指定多久保留在 Azure 中的復原點。

### 建立保護群組
開始建立新保護群組使用 [Add-dpmprotectiongroup](https://technet.microsoft.com/library/hh881722) 指令程式。

```
PS C:\> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

在上述 cmdlet 會建立名為保護群組 *ProtectGroup01*。 也可以修改稍後現有的保護群組，以加入備份至 Azure 雲端。 不過，變更保護群組-新的或現有的-我們要取得的控制代碼 *可修改* 物件使用 [Modifiable](https://technet.microsoft.com/library/hh881713) 指令程式。

```
PS C:\> $MPG = Get-ModifiableProtectionGroup $PG
```

### 將群組成員加入至保護群組
每個 DPM 代理程式會知道它安裝所在伺服器上資料來源的清單。 若要將資料來源加入至保護群組，DPM 代理程式必須先將資料來源清單傳回到 DPM 伺服器。 然後會選取一或多個資料來源，並加入至保護群組。 達成此動作所需的 PowerShell 步驟包括：

1. 擷取透過 DPM 代理程式由 DPM 管理的所有伺服器清單。
2. 選擇特定伺服器。
3. 擷取伺服器上所有資料來源的清單。
4. 選擇一或多個資料來源，並將它們加入至保護群組

DPM 代理程式已安裝且受 DPM 伺服器所在的伺服器清單，將會取得與 [Get-dpmproductionserver](https://technet.microsoft.com/library/hh881600) 指令程式。 在此範例中我們將篩選並只設定名稱 PS *productionserver01* 進行備份。

```
PS C:\> $server = Get-ProductionServer -DPMServerName "TestingServer" | where {($_.servername) –contains “productionserver01”
```

現在上擷取的資料來源清單 ```$server``` 使用 [Get-dpmdatasource](https://technet.microsoft.com/library/hh881605) 指令程式。 在此範例中，我們要篩選的磁碟區 * D:\* 我們要設定備份。 此資料來源接著會新增至保護群組使用 [Add-dpmchilddatasource](https://technet.microsoft.com/library/hh881732) 指令程式。 請記得使用 *modifable* 保護群組物件 ```$MPG``` 以進行新增。

```
PS C:\> $DS = Get-Datasource -ProductionServer $server -Inquire | where { $_.Name -contains “D:\” }

PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

視需要重複此步驟，直到您已加入所有選取的資料來源至保護群組中為止。 您也可以從只有一個資料來源開始，並完成建立保護群組的工作流程，然後稍後將更多的資料來源加入至保護群組。

### 選取資料保護方式
資料來源加入至保護群組之後, 的下一個步驟是指定保護方法使用 [Set-dpmprotectiontype](https://technet.microsoft.com/library/hh881725) 指令程式。 此範例中，將為本機磁碟和雲端備份設定保護群組。 您也必須指定您想要保護雲端使用的資料來源 [Add-dpmchilddatasource](https://technet.microsoft.com/library/hh881732.aspx) 指令程式搭配-線上旗標。

```
PS C:\> Set-DPMProtectionType -ProtectionGroup $MPG -ShortTerm Disk –LongTerm Online
PS C:\> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS –Online
```

### 設定保留範圍
設定備份點使用的保留 [Set-dpmpolicyobjective](https://technet.microsoft.com/library/hh881762) 指令程式。 雖然在定義備份排程之前設定保留點看起來有點奇怪，使用 ```Set-DPMPolicyObjective``` Cmdlet 會自動設定預設的備份排程，之後便可加以修改。 您總是可以先設定備份排程，之後再設定保留原則。

在下面的範例中，此 Cmdlet 會設定磁碟備份的保留參數。 這將會保留 10 天備份，並每隔 6 小時同步處理實際執行伺服器和 DPM 伺服器之間的資料。 ```SynchronizationFrequencyMinutes``` 不會定義建立備份點的頻率，只會定有資料複製到 DPM 伺服器的頻率；這可防止備份變得太大。

```
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequencyMinutes 360
```

移至 Azure 的備份 （DPM 參照這些稱為線上備份），保留範圍可設定為 [長期使用祖父位父親的兒子配置 (GFS) 保留](backup-azure-backup-cloud-as-tape.md)。 也就是說，您可以定義結合的保留原則，包含每日、每週、每月和每年保留原則。 在此範例中，我們建立陣列，表示我們所需的複雜保留配置，然後設定 [保留範圍使用 [Set-dpmpolicyobjective](https://technet.microsoft.com/library/hh881762) 指令程式。

```
PS C:\> $RRlist = @()
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:\> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:\> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

### 設定備份排程
如果您使用 ```Set-DPMPolicyObjective``` Cmdlet 指定保護目標，DPM 會自動設定預設的備份排程。 若要變更預設排程，請使用 [Get-dpmpolicyschedule](https://technet.microsoft.com/library/hh881749) 指令程式，後面加上 [Set-dpmpolicyschedule](https://technet.microsoft.com/library/hh881723) 指令程式。

```
PS C:\> $onlineSch = Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTerm Online
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:\> Set-DPMPolicySchedule -ProtectionGroup $MPG -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

在上述範例中，```$onlineSch``` 是具有四個元素的陣列，其中包含 GFS 配置中保護群組的現有線上保護排程：

1. ```$onlineSch[0]``` 將包含每日排程
2. ```$onlineSch[1]``` 將包含每週排程
3. ```$onlineSch[2]``` 將包含每月排程
4. ```$onlineSch[3]``` 將包含每年排程

因此，如果您需要修改每週排程，您需要參考 ```$onlineSch[1]```。

### 初始備份
第一次備份資料來源時，DPM 就需要建立初始複本，以建立要在 DPM 複本磁碟區上保護的資料來源複本。 此活動可以排定在特定時間，或可以手動觸發使用 [Set-dpmreplicacreationmethod](https://technet.microsoft.com/library/hh881715) cmdlet 搭配參數 ```-NOW```。

```
PS C:\> Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```
### 變更 DPM 複本和復原點磁碟區的大小
您也可以變更 DPM 複本磁碟區，以及陰影複製磁碟區使用的大小 [組 DPMDatasourceDiskAllocation](https://technet.microsoft.com/library/hh881618.aspx) 指令程式，如以下範例 ︰
Get-datasourcediskallocation Datasource $DS
Set-datasourcediskallocation Get-datasource $DS Get-protectiongroup $MPG-手動 ReplicaArea (2 gb) ShadowCopyArea (2 gb)

### 將變更認可到保護群組
最後，需要先認可變更，DPM 才可以根據每個新保護群組組態進行備份。 這是使用 [Set-dpmprotectiongroup](https://technet.microsoft.com/library/hh881758) 指令程式。

```
PS C:\> Set-DPMProtectionGroup -ProtectionGroup $MPG
```
## 檢視備份點
您可以使用 [Get-dpmrecoverypoint](https://technet.microsoft.com/library/hh881746) cmdlet 來取得資料來源所有復原點的清單。 在此範例中，我們將 ︰
- 這會儲存在陣列中的 DPM 伺服器上擷取所有的 Pg ```$PG```
- 取得對應至資料來源 ```$PG[0]```
- 取得資料來源所有復原點。

```
PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## 還原在 Azure 上保護的資料
還原資料是 ```RecoverableItem``` 物件和 ```RecoveryOption``` 物件的組合。 在上一個節中，我們已取得資料來源的備份點清單。

在下列範例中，我們將示範如何透過結合備份點與復原目標從 Azure 備份還原 Hyper-V 虛擬機器。 其中包括：

- 建立復原選項，使用  [New-dpmrecoveryoption](https://technet.microsoft.com/library/hh881592) 指令程式。
- 使用 ```Get-DPMRecoveryPoint``` Cmdlet 擷取備份點的陣列。
- 選擇要從中還原的備份點。

```
PS C:\> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “C:\VMRecovery”

PS C:\> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:\> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:\> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online

PS C:\> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints[0] -RecoveryOption $RecoveryOption
```

命令可以很容易地針對任何資料來源類型擴充。

## 後續步驟

- 關於 Azure 備份 dpm 的詳細資訊請參閱 [DPM 備份簡介](backup-azure-dpm-introduction.md)


