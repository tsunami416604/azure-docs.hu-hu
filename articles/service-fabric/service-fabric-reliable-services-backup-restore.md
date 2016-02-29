<properties
   pageTitle="Reliable Service 備份與還原 |Microsoft Azure"
   description="Service Fabric Reliable Service 備份與還原的概念文件"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/01/2015"
   ms.author="mcoskun"/>

# 備份與還原 Reliable Services

Service Fabric 是高可用性平台，跨多個節點之間複寫狀態以維護這個高可用性。  因此，即使叢集中的一個節點失敗，服務可以繼續。 雖然此平台所提供的內建備援對於一些特定情況可能已經足夠，但是服務最好能夠備份資料 (到外部存放區)。

例如，服務在下列案例中可能想要備份資料：

* 在整個 Service Fabric 叢集或執行指定資料分割的所有節點永久遺失時。 舉例來說，當您不是地理複寫且您的整個叢集是在一個資料中心，而且整個資料中心中斷，就會發生這個事件。

* 不小心刪除/損毀狀態的系統管理錯誤。 例如，這可能會在具備足夠權限的系統管理員錯誤地刪除服務時發生。

* 服務中造成資料損毀的錯誤。 例如，當服務程式碼升級而開始將錯誤資料寫入「可靠的集合」時，就可能發生此情況。 在這種情況下，可能必須將程式碼和資料還原成先前的狀態。

* 離線資料處理。 對於獨立於服務來產生資料的商業智慧，離線處理資料相當方便。

備份/還原功能可以讓在 Reliable Services API 上建置的服務建立及還原備份。 平台提供的備份 API 可以進行分割狀態的備份，而不會封鎖讀取或寫入作業。 還原 API 可以從所選的備份還原分割狀態。


## 如何備份

服務作者對於進行備份的時機與儲存備份的位置具有完整的控制權。

若要開始備份，服務可以叫用 **IReliableStateManager.BackupAsync**。  備份只可以從主要複本進行，且它們需要授與撰寫狀態。

如下所示的簡單的多載 **BackupAsync** 會在呼叫 Func << BackupInfo，bool >> **backupCallback**。

```C#
await this.StateManager.BackupAsync(this.BackupCallbackAsync);
```

**BackupInfo** 提供備份，包括執行階段儲存的備份 (BackupInfo.Directory) 所在的資料夾位置的相關資訊。 回呼函式預期會將 BackupInfo.Directory 移到外部存放區或另一個位置。  此函式也會傳回 Bool，指出是否能夠順利將備份資料夾移動至目標位置。

下列程式碼示範如何使用 backupCallback，將備份上傳至 Azure 儲存體：

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

    return true;
}
```

在上述範例中， **ExternalBackupStore** 是範例類別，用來與 Azure Blob 儲存體和 **UploadBackupFolderAsync** 是方法壓縮的資料夾，並將它放在 Azure Blob 存放區。

請注意：

- 只能有一個 **BackupAsync** 每個複本傳遞在任何給定的時間點。 多個 **BackupAsync** 一次呼叫會擲回 **FabricBackupInProgressException** 來限制傳遞至其中一個的備份。

- 如果當備份進行中時有複本容錯移轉，備份可能未完成。 因此，完成容錯移轉時，會叫用來重新啟動備份服務的責任 **BackupAsync** 視。

## 如何還原資料

一般而言，您可能需要執行還原作業的情況屬於下列其中一種：


1. 服務資料分割遺失資料。 例如，資料分割的三分之二複本 (包括主要複本) 的磁碟損毀/抹除。 新的主要複本可能需要從備份還原資料。

2. 整個服務遺失。 例如，系統管理員移除整個服務，因此服務和資料需要還原。

3. 服務會複寫損毀的應用程式 (例如，因為應用程式錯誤) 資料。 在此情況下，服務必須升級/還原以移除損毀的原因，並且必須還原未損毀的資料。

雖然有許多種可行的方法，我們會提供使用 RestoreAsync 從上述案例復原的一些範例。

## 資料分割資料遺失

在此情況下，執行階段會自動偵測資料遺失，並叫用 **OnDataLossAsync** API。

服務作者必須執行下列動作來復原：
- 覆寫 **IReliableStateManager** 傳回新的 ReliableStateManager，並提供在資料遺失事件的情況下呼叫回撥功能。
- 尋找最新的備份中的外部位置，其中包含服務的備份。
- 如果新的主要背後最新的備份狀態，則傳回 false。 這可確保，新的主要無法覆寫與較舊的資料。
- 下載最新的備份 (並將已壓縮的備份解壓縮到備份資料夾)。
- 呼叫 **IReliableStateManager.RestoreAsync** 備份資料夾的路徑。
- 如果還原成功，則會傳回 true。

以下是範例實作 **OnDataLossAsync** 連同方法 **IReliableStateManager** 覆寫。

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(new ReliableStateManagerConfiguration(
            onDataLossEvent: this.OnDataLossAsync));
}

protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    await this.StateManager.RestoreAsync(backupFolder);

    return true;
}
```

>[AZURE.NOTE] RestorePolicy 預設設定為安全。  這表示如果 RestoreAsync API 偵測到備份資料夾包含早於或等於這個複本所包含之狀態的狀態，則它將會失敗且具有 ArgumentException。  RestorePolicy.Force 可以用來略過這項安全檢查。

## 已刪除或遺失的服務

如果服務已移除，您必須先重新建立該服務，才可以還原資料。  請務必以相同的組態建立服務，例如資料分割配置，如此才能順暢地還原資料。  一旦服務已啟動還原資料的 API (**OnDataLossAsync** 上方) 必須叫用此服務的每一個磁碟分割。  這使用其中一種方式達成的 **FabricClient.ServiceManager.InvokeDataLossAsync** 每個磁碟分割。  

從這裡開始，實作與上述案例相同。  每個資料分割都需要從外部存放區還原最新的相關備份。 有一點需要注意，資料分割識別碼現在可能已變更，因為執行階段會以動態方式建立資料分割識別碼)。 因此，服務需要存放區和適當的資料分割資訊和服務名稱，來識別要針對每個資料分割還原的正確最新備份。


## 損毀的應用程式資料的複寫

如果新部署的應用程式升級有錯誤，可能會造成資料損毀。例如，應用程式升級可能會開始以無效的區碼更新「可靠的字典」中的每個電話號碼記錄。  在此情況下，因為 Service Fabric 並不知道要儲存的資料的本質，所以會複寫無效的電話號碼。

偵測會造成資料損毀的這類嚴重錯誤之後，要做的第一件事是在應用程式層級凍結服務，並且在可行時升級至沒有錯誤的應用程式程式碼的版本。  不過，即使在修正服務程式碼之後，資料仍可能會損毀並且因此需要還原資料。  在這種情況下，還原最新的備份可能還不足夠，因為最新的備份也可能已損毀。  因此，必須尋找在資料損毀之前所做的最後一個備份。

如果不確定哪些備份正確，您可以部署新的 Service Fabric 叢集，並還原受影響的資料分割備份，就像上述「刪除的服務」案例一樣。  針對每個資料分割，開始還原從最新到最舊的備份。 一旦您找到沒有損毀的備份，就移動/刪除這個資料分割較新 (相較於備份) 的所有備份。 針對每個資料分割重複此程序。 現在，當 **OnDataLossAsync** 稱為生產叢集中的磁碟分割，在外部存放區中的最後一個備份將會執行上述程序所選擇的一個。

現在，可以使用「刪除的服務」中的步驟，將服務備份的狀態還原至不良程式碼已損毀狀態之前的狀態。

請注意：

- 當您還原時，還原的備份很有可能是早於資料遺失之前的資料分割狀態。 因此，還原應該只能當做盡量復原最多資料的最後手段。

- 代表備份資料夾路徑與備份資料夾內的檔案路徑，它們的字串可以大於 255 個字元，視 FabricDataRoot 路徑和應用程式類型名稱的長度而定。 這可能會造成一些類似的.Net 方法 **Directory.Move** 擲回 **PathTooLongException**。 解決方法是直接呼叫 kernel32 Api，像是 **CopyFile**。


## 幕後：備份與還原的詳細資料

### 備份
可靠的狀態管理員能夠建立一致的備份，而不會封鎖任何讀取或寫入作業。 為了達到這個目的，它會利用檢查點和記錄持續性機制。  可靠的狀態管理員會在特定時間點採用模糊 (輕量型) 檢查點，來減輕交易記錄檔的壓力和改善復原時間。  當 IReliableStateManager。**BackupAsync** 呼叫時，可靠的狀態管理員會指示所有可靠的物件將其最新的檢查點檔案複製到本機備份資料夾。  然後，可靠的狀態管理員會從「開始指標」開始，將所有記錄檔記錄複製到備份資料夾中的最新記錄檔記錄。  因為記錄到最新的記錄檔記錄的所有記錄檔記錄會包含在備份中，可靠的狀態管理員會保留預先寫入記錄，可靠的狀態管理員會保證所有已認可的交易 (CommitAsync 已順利傳回) 會包含在備份中。

任何交易認可之後， **BackupAsync** 被呼叫，可能或可能無法在備份中。  一旦由平台填入本機備份資料夾 (亦即執行階段完成的本機備份)，會叫用服務的備份回呼。  此回呼會負責將備份資料夾移到外部位置，例如 Azure 儲存體。

### 還原

可靠的狀態管理員能夠利用 IReliableStateManager.RestoreAsync API，從備份還原。  RestoreAsync 方法只能在內部呼叫 **OnDataLossAsync** 方法。  傳回 bool **OnDataLossAsync** 表示從外部來源還原其狀態，服務。  如果 **OnDataLossAsync** 傳回 true，Service Fabric 將會重建此主要所有其他複本。  Service Fabric 可確保接收的複本， **OnDataLossAsync** 先轉換成主要角色，但不是授與讀取狀態或寫入的狀態。  這表示，如 StatefulService 實作 RunAsync 將不會呼叫直到 **OnDataLossAsync** 順利完成。  然後， **OnDataLossAsync** 就會叫用在新的主要伺服器上。 在服務成功完成此 API (藉由傳回 true 或 false) 並完成相關重新設定之前，將會一次一個地繼續呼叫 API。


RestoreAsync 會先卸除過去呼叫的主要複本中的所有現有狀態。  然後，可靠的狀態管理員會建立存在於備份資料夾中的所有可靠的物件。  接下來，可靠的物件會獲得指示從其備份資料夾中的檢查點還原。  最後，可靠的狀態管理員會從備份資料夾中的記錄檔記錄復原自己的狀態，並執行復原。  做為復原程序的一部分，作業是從「開始點」開始，在備份資料夾中認可記錄檔記錄，並且對可靠的物件重新執行。  這個步驟可確保復原的狀態一致。

