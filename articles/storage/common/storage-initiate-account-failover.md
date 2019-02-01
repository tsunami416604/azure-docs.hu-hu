---
title: A tárolási fiók feladatátvétel (előzetes verzió) – Azure Storage
description: Ismerje meg, hogyan egy fiók feladatátvételt kezdeményezzen, abban az esetben, ha a tárfiók elsődleges végpontjába elérhetetlenné válik. A feladatátvétel a másodlagos régióban a storage-fiók az elsődleges régió válhat frissíti.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2019
ms.author: tamram
ms.component: common
ms.openlocfilehash: 1290a990515cd68f46d137c276e8bb36834ff482
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513490"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Storage-fiók feladatátvétel (előzetes verzió)

A georedundáns tárfiók elsődleges végpontjába valamilyen okból elérhetetlenné válik, ha egy fiók feladatátvételt (előzetes verzió) is kezdeményezhető. Egy fiók feladatátvételt a másodlagos végpontot, hogy a tárfiók elsődleges végpontjába válnak frissíti. A feladatátvétel befejezése után az ügyfelek az új elsődleges régió felé tartalomkészítés elkezdéséhez. Kényszerített feladatátvétel lehetővé teszi az alkalmazások magas rendelkezésre állás fenntartása.

Ez a cikk bemutatja, hogyan, hogy a tárfiók használata az Azure portal, PowerShell vagy az Azure CLI-fiók feladatátvételt kezdeményezzen. Fiók feladatátvételi kapcsolatos további információkért lásd: [katasztrófa utáni helyreállítás és a fiók feladatátvétel (előzetes verzió) az Azure Storage-ban](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Egy fiók feladatátvételt általában kis mértékű adatvesztést eredményez. Egy fiók feladatátvételt megszegéseinek, és az adatvesztést előkészítése, tekintse át a [megismerheti a fiók feladatátvételi folyamatot](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

## <a name="prerequisites"></a>Előfeltételek

Egy fiók feladatátvételt az a tárfiók elvégzése előtt győződjön meg arról, hogy elvégezte-e az alábbi lépéseket:

- A fiók feladatátvétel előzetes regisztráció. Hogyan kell regisztrálni kapcsolatos információkért lásd: [az előzetes verzióra vonatkozó](storage-disaster-recovery-guidance.md#about-the-preview).
- Győződjön meg arról, hogy a tárfiók georedundáns tárolás (GRS) vagy írásvédett georedundáns tárolás (RA-GRS) használatára van konfigurálva. Georedundáns tárolással kapcsolatos további információkért lásd: [georedundáns tárolás (GRS): Az Azure Storage-régiók közti replikációs](storage-redundancy-grs.md). 

## <a name="important-implications-of-account-failover"></a>Fiók feladatátvételi fontos következményei

Amikor egy fiók feladatátvételt kezdeményez a tárfiók, a DNS-rekordokat a másodlagos végpontot, hogy a másodlagos végpontra lesz az elsődleges végpont frissül. Győződjön meg arról, hogy megértette a lehetséges hatás a tárfiókhoz való feladatátvétel elindítása előtt.

Mértékének becsléséhez nagy valószínűséggel adatvesztést előtt feladatátvételt kezdeményezni, ellenőrizze a **utolsó szinkronizálás időpontja** tulajdonság használatával a `Get-AzureRmStorageAccount` PowerShell-parancsmagot, és tartalmazzák a `-IncludeGeoReplicationStats` paraméter. Ezután ellenőrizze a `GeoReplicationStats` tulajdonság a fiókjához. 

A feladatátvétel után a tárfiók típusa automatikusan az új elsődleges régióban helyileg redundáns tárolás (LRS) alakítja át. Georedundáns tárolás (GRS) vagy írásvédett georedundáns tárolás (RA-GRS) a fiók újra engedélyezheti. Vegye figyelembe, hogy egy további költséget konvertáláskor LRS, GRS vagy RA-GRS áll. További információkért lásd: [adatforgalmi díjszabás részletei](https://azure.microsoft.com/pricing/details/bandwidth/). 

Miután újra engedélyezi GRS-tárfiókot, a Microsoft kezdődik, az új másodlagos régióban való replikálásához a fiókban lévő adatok. Replikáció ideje függ a replikált adatok mennyisége.  

## <a name="azure-portal"></a>Azure Portal

Az Azure Portalról egy fiók feladatátvételt kezdeményez, kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
2. A **beállítások**válassza **georeplikációs**. Az alábbi képen egy tárfiókhoz georeplikációs és feladatátvételi állapotát jeleníti meg.

    ![Georeplikáció és feladatátvétel állapotát ábrázoló képernyőfelvétel](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Győződjön meg arról, hogy a tárfiók georedundáns tárolás (GRS) vagy írásvédett georedundáns tárolás (RA-GRS) van konfigurálva. Ha nem érhető el, majd válassza ki **konfigurációs** alatt **beállítások** a fiók georedundáns kell frissíteni. 
4. A **utolsó szinkronizálás időpontja** tulajdonság azt jelzi, hogy milyen távolságban a másodlagos mögött van az elsődleges kiszolgálóról. **Utolsó szinkronizálás ideje** biztosít egy becsült tapasztalható adatveszteség mértékét a feladatátvétel befejezése után.
5. Válassza ki **(előzetes verzió) feladatátvétel előkészítése**. 
6. Tekintse át a megerősítési párbeszédpanelen. Amikor készen áll, adja meg **Igen** erősítse meg, és kezdeményezze a feladatátvételt.

    ![Képernyőkép ábrázoló megerősítő párbeszédpanel egy fiók feladatátvételhez](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>PowerShell

A PowerShell használatával egy fiók feladatátvételt kezdeményezzen, először telepítenie kell a 6.0.1 modul előzetes verziójának. Kövesse az alábbi lépéseket a modul telepítése:

1. Távolítsa el az összes az Azure PowerShell előző telepítés:

    - Távolítsa el az összes korábbi telepítés az Azure PowerShell használatával Windows a **alkalmazások és szolgáltatások** menüpont **beállítások**.
    - Távolítsa el az összes **Azure*** a modulok `%Program Files%\WindowsPowerShell\Modules`.
    
1. Győződjön meg arról, hogy telepítve van a PowerShellGet legújabb verzióját. Nyisson meg egy Windows PowerShell-ablakot, és a legújabb verzió telepítéséhez a következő parancsot:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Zárja be, majd nyissa meg a PowerShell-ablakban a PowerShellGet telepítése után. 

1. Telepítse az Azure PowerShell legújabb verzióját:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Telepítse az Azure Storage előzetes modul, amely támogatja az Azure ad-ben:
   
    ```powershell
    Install-Module -Name AzureRM.Storage -AllowPrerelease
    ```
1. Zárja be és nyissa meg ismét a PowerShell-ablakban.
 

A PowerShell egy fiók feladatátvételt kezdeményez, hajtsa végre a következő parancsot:

```powershell
Invoke-AzureRmStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>Azure CLI

Azure CLI használatával egy fiók feladatátvételt kezdeményezzen, hajtsa végre a következő parancsokat:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

## <a name="next-steps"></a>További lépések

- [Katasztrófa utáni helyreállítás és a fiók feladatátvétel (előzetes verzió) az Azure Storage-ban](storage-disaster-recovery-guidance.md)
- [Magas rendelkezésre állású alkalmazások tervezése az RA-GRS használatával](storage-designing-ha-apps-with-ragrs.md)
- [Oktatóanyag: A Blob storage magas rendelkezésre állású alkalmazás létrehozása](../blobs/storage-create-geo-redundant-storage.md) 
