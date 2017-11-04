---
title: "Regisztrálása vagy a kiszolgáló regisztrációját az Azure fájlszinkronizálás (előzetes verzió) |} Microsoft Docs"
description: "Megtudhatja, hogyan regisztrálásához és a Windows Server egy Azure szinkronizálás szinkronizálási tárhelyre."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 13a75d5cafd94435346660614721399f2d77919b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>Regisztrálása vagy a kiszolgáló regisztrációját az Azure fájlszinkronizálás (előzetes verzió)
Az Azure File Sync (előzetes verzió) lehetővé teszi a szervezet Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ezt úgy éri el, hogy átalakítja a Windows-kiszolgálókat az Azure-fájlmegosztás gyors gyorsítótáraivá. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A következő cikk bemutatja, hogyan regisztrálásához és a kiszolgáló egy tároló szinkronizálási szolgáltatás. Ez is szükséges, ha egy kiszolgáló leszerelt, vagy ha új kiszolgálót végpont egy szinkronizálási csoportban van szükség. Lásd: [Azure fájlszinkronizálás (előzetes verzió) telepítése](storage-sync-files-deployment-guide.md) fájlszinkronizálás Azure-végpontok telepítéséről információk.

## <a name="prerequisites"></a>Előfeltételek
Egy tároló szinkronizálási szolgáltatással a Windows Server regisztrálásához elő kell készítenie a Windows Server, a szükséges előfeltételek:

* Győződjön meg arról, hogy a tároló szinkronizálási szolgáltatás van telepítve. Egy tároló szinkronizálási szolgáltatás telepítéséről további információkért lásd: [Azure fájlszinkronizálás (előzetes verzió) telepítése](storage-sync-files-deployment-guide.md).
* Győződjön meg arról, hogy a kiszolgáló csatlakozik-e az internethez, és hogy elérhető Azure.
* Tiltsa le az Internet Explorer fokozott biztonsági beállításai a rendszergazda a Kiszolgálókezelő felhasználói felületén.
    
    ![A Kiszolgálókezelő az Internet Explorer fokozott biztonsági beállításai a kijelölt felhasználói felület](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Győződjön meg arról, hogy a AzureRM PowerShell modul telepítve van-e a kiszolgálón. Ha a kiszolgáló egy feladatátvevő fürt tagja, a fürt minden csomópontja a AzureRM modul szükséges. További részletes információt a AzureRM modul telepítése található meg a [Azure PowerShell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

    > [!Note]  
    > Azt javasoljuk, hogy egy kiszolgáló register/unregister legújabb verzióját, a AzureRM PowerShell-modul segítségével. Ha a AzureRM csomag korábban már telepítve van ezen a kiszolgálón (a PowerShell verziója ezen a kiszolgálón pedig 5.* vagy újabb), használhatja a `Update-Module` parancsmag frissíti a csomagot. 

## <a name="register-a-server-with-storage-sync-service"></a>Regisztrálja a kiszolgálót tároló szinkronizálási szolgáltatás
Előtt a Windows Server használható egy *végpontját* egy Azure fájlszinkronizálás a *szinkronizálású csoport*, regisztrálnia kell azt a egy *tároló szinkronizálási szolgáltatás*. A kiszolgáló csak egy regisztrálható *tároló szinkronizálási szolgáltatás* egyszerre.

### <a name="install-the-azure-file-sync-agent"></a>Az Azure fájl Sync-ügynök telepítése
1. [Töltse le az Azure fájlszinkronizálás ügynököt](https://go.microsoft.com/fwlink/?linkid=858257).
2. Indítsa el az Azure fájlszinkronizálás ügynök telepítőjét.
    
    ![Az Azure fájlszinkronizálás ügynököt telepítő első oldalán](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Győződjön meg arról, lehetővé teszi az Azure fájlszinkronizálás ügynök a Microsoft Update használata frissítések. Ez azért fontos, mert a kritikus fontosságú biztonsági javítások és továbbfejlesztett szolgáltatásokat a kiszolgáló csomaghoz mellékeltük a Microsoft Update.

    ![Győződjön meg arról, a Microsoft Update ablaktábláján az Azure fájlszinkronizálás ügynök telepítőjét engedélyezve van a Microsoft Update](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Ha a kiszolgáló nem korábban regisztrálva, a kiszolgáló regisztrálása felhasználói felület jelenik meg azonnal a telepítés befejezése után.

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, az Azure fájl Sync-ügynök telepítve kell lennie a fürt minden csomópontján.

### <a name="register-the-server-using-the-server-registration-ui"></a>Regisztrálja a kiszolgálót, a kiszolgáló regisztrálása felhasználói felület használatával

> [!Important]  
> Cloud Solution Provider-előfizetések nem használható a kiszolgáló regisztrálása felhasználói felületén. Ehelyett használjon PowerShell (alatt ez a szakasz).

1. Ha a kiszolgáló regisztrálása felhasználói felület nem indult el közvetlenül az Azure fájl Sync-ügynök telepítésének befejezése után, akkor is manuálisan kell elindítani a következő futtatásával `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Kattintson a *bejelentkezési* elérni az Azure-előfizetéshez. 

    ![A kiszolgáló regisztrációs felhasználói felület párbeszédpanel megnyitása](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Válassza ki a megfelelő előfizetés, erőforráscsoport és tároló szinkronizálási szolgáltatás a párbeszédpanelen.

    ![Szinkronizálási szolgáltatás tárolással](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. A képen egy további bejelentkezés szükséges a folyamat befejezéséhez. 

    ![Jelentkezzen be a párbeszédpanelt](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, minden olyan kiszolgálón kell futtatni a kiszolgáló regisztrálása. A regisztrált kiszolgálókat az Azure portálon megtekintésekor Azure fájlszinkronizálás automatikusan minden csomópont felismeri a ugyanazt a feladatátvevő fürt tagja, és a összevonja azokat megfelelően.

### <a name="register-the-server-with-powershell"></a>A kiszolgáló regisztrálása a PowerShell használatával
Kiszolgáló regisztrálása a PowerShell használatával is elvégezheti. Ez az a kiszolgáló regisztrálása a Cloud Solution Provider-előfizetések az egyetlen támogatott mód:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

## <a name="unregister-the-server-with-storage-sync-service"></a>Szüntesse meg a kiszolgáló, a tároló szinkronizálási szolgáltatás
Nincsenek számos lépést, a kiszolgáló regisztrációját az egy tároló szinkronizálási szolgáltatás szükséges. Vessen egy pillantást hogyan megfelelően az a kiszolgáló regisztrációját.

### <a name="optional-recall-all-tiered-data"></a>(Választható) Rétegzett adatainak visszaírásához
Ha engedélyezve van a kiszolgáló végpont, a felhő rétegezési lesz *réteg* fájlok az Azure fájlmegosztások. Ez lehetővé teszi a helyszíni fájlmegosztások a gyorsítótár ahelyett, hogy az adatkészlet, teljes másolata ügyfélgépként hatékonyan használhatják a területet a fájlkiszolgálón. Azonban ha egy kiszolgáló végpont rétegzett fájlok a kiszolgálón továbbra is helyben távolítja el, azokat a fájlokat válnak lehet. Ezért továbbra is fájl hozzáférésre van szükség, ha kell Emlékezzen vissza Azure-fájlokból rétegzett bővítményi regisztráció törléséhez folytatása előtt. 

Ezt megteheti a PowerShell-parancsmaggal alább látható módon:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Ha a helyi köteten, amelyen a kiszolgáló nem rendelkezik elég szabad hely a rétegzett adatainak visszaírásához, a `Invoke-StorageSyncFileRecall` parancsmag sikertelen lesz.  

### <a name="remove-the-server-from-all-sync-groups"></a>A kiszolgáló eltávolítása az összes szinkronizálási csoportok
A tároló szinkronizálási szolgáltatást a kiszolgáló regisztrációját, mielőtt Server végpontjai kiszolgálón el kell távolítani. Ezt megteheti a portálon:

1. Nyissa meg a tároló szinkronizálási szolgáltatást, ha a kiszolgáló regisztrálva van-e.
2. Távolítsa el a kiszolgáló összes Server-végpontokat a tároló szinkronizálási szolgáltatás minden egyes szinkronizálás csoport. Ehhez kattintson a jobb gombbal a megfelelő végpontját a szinkronizálási ablakban.

    ![A kiszolgáló végpont szinkronizálási csoportból történő eltávolítása](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Ez egy egyszerű PowerShell-parancsfájllal is elvégezhető:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>Szüntesse meg a kiszolgáló
Most, hogy minden adat rendelkezik lett visszahívott, és a kiszolgáló összes szinkronizálási csoport el lett távolítva, a kiszolgáló regisztrációjának törlése is lehet. 

1. Az Azure portálon keresse meg a tároló szinkronizálási szolgáltatás a "Regisztrált kiszolgálók" című szakasza.
2. Kattintson a jobb gombbal a kiszolgáló regisztrációját, és kattintson a "Kiszolgáló regisztrációjának törlése".

    ![Kiszolgáló regisztrációjának törlése](media/storage-sync-files-server-registration/unregister-server-1.png)