---
title: Storage-fiók feladatátvételének kezdeményezése (előzetes verzió) – Azure Storage
description: Megtudhatja, hogyan kezdeményezheti a fiók feladatátvételét abban az esetben, ha a Storage-fiók elsődleges végpontja elérhetetlenné válik. A feladatátvétel frissíti a másodlagos régiót, hogy az elsődleges régió legyen a Storage-fiók számára.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 76e34736238273f2af3fccae0ac2b5ed0ff491f0
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128343"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Storage-fiók feladatátvételének kezdeményezése (előzetes verzió)

Ha a Geo-redundáns tárolási fiók elsődleges végpontja bármilyen okból elérhetetlenné válik, elindíthat egy fiók feladatátvételét (előzetes verzió). A fiók feladatátvétele frissíti a másodlagos végpontot, hogy az a Storage-fiók elsődleges végpontja legyen. A feladatátvétel befejeződése után az ügyfelek megkezdhetik az új elsődleges régióba való írást. A kényszerített feladatátvétel lehetővé teszi az alkalmazások magas rendelkezésre állásának fenntartását.

Ez a cikk bemutatja, hogyan kezdeményezheti a fiók feladatátvételét a Storage-fiókhoz a Azure Portal, a PowerShell vagy az Azure CLI használatával. A fiók feladatátvételével kapcsolatos további tudnivalókért lásd: vész- [helyreállítás és fiók feladatátvétele (előzetes verzió) az Azure Storage-ban](storage-disaster-recovery-guidance.md).

> [!WARNING]
> A fiók feladatátvétele általában valamilyen adatvesztést eredményez. A fiók feladatátvételi következményeinek megismeréséhez és az adatvesztés előkészítéséhez tekintse át [a fiók feladatátvételi folyamatának megismerése](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)című témakört.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégzi a fiók feladatátvételét a Storage-fiókjában, győződjön meg arról, hogy végrehajtotta a következő lépést:

- Győződjön meg arról, hogy a Storage-fiókja a Geo-redundáns tárolás (GRS) vagy a Read-Access geo-redundáns tárolás (RA-GRS) használatára van konfigurálva. További információ a földrajzilag redundáns tárolásról: [Azure Storage redundancia](storage-redundancy.md).

## <a name="important-implications-of-account-failover"></a>A fiók feladatátvételének fontos következményei

Amikor elindít egy fiókot a Storage-fiókhoz, a másodlagos végpont DNS-rekordjai frissülnek, így a másodlagos végpont lesz az elsődleges végpont. A feladatátvétel kezdeményezése előtt győződjön meg arról, hogy tisztában van a Storage-fiók lehetséges hatásával.

Ha a feladatátvétel elindítása előtt szeretné megbecsülni a várható adatvesztés mértékét, akkor a `Get-AzStorageAccount` PowerShell-parancsmag használatával tekintse meg a **Legutóbbi szinkronizálási idő** tulajdonságot, és adja meg a `-IncludeGeoReplicationStats` paramétert. Ezután jelölje be a fiókja `GeoReplicationStats` tulajdonságát. \

A feladatátvételt követően a rendszer automatikusan átalakítja a Storage-fiók típusát a helyileg redundáns tárterületre (LRS) az új elsődleges régióban. Újra engedélyezheti a Geo-redundáns tárolást (GRS), vagy a fiókhoz tartozó olvasási hozzáférésű geo-redundáns tárolást (RA-GRS). Vegye figyelembe, hogy a LRS-ről GRS-re vagy RA-GRS-re való átalakítás további költségekkel jár. További információ: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/).

Miután újraengedélyezte a GRS a Storage-fiókjához, a Microsoft elkezdi replikálni a fiókjában lévő adatait az új másodlagos régióba. A replikálási idő a replikált adatmennyiségtől függ.  

## <a name="portal"></a>[Portal](#tab/azure-portal)

A fiók feladatátvételének elindításához a Azure Portal hajtsa végre az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
2. A **Beállítások**területen válassza a **geo-replikáció**lehetőséget. Az alábbi képen egy Storage-fiók geo-replikálási és feladatátvételi állapota látható.

    ![A földrajzi replikálást és a feladatátvételi állapotot ábrázoló képernyőkép](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Ellenőrizze, hogy a Storage-fiókja a Geo-redundáns tároláshoz (GRS) van-e konfigurálva, vagy hogy van-e olvasási hozzáférésű geo-redundáns tárolás (RA-GRS). Ha nem, akkor a **Beállítások** területen válassza a **Konfigurálás** lehetőséget, hogy a fiókját a Geo-redundáns értékre frissítse. 
4. A **Legutóbbi szinkronizálási idő** tulajdonság azt jelzi, hogy a másodlagos állapot meddig marad az elsődlegesnél. A **legutóbbi szinkronizálás ideje** azt az adatvesztés mértékét adja meg, amelyet a feladatátvétel befejezése után fog tapasztalni.
5. Válassza **a feladatátvétel előkészítése (előzetes verzió)** lehetőséget. 
6. Tekintse át a megerősítő párbeszédpanelt. Ha elkészült, az **Igen** gombra kattintva erősítse meg és kezdeményezheti a feladatátvételt.

    ![A fiók feladatátvételének megerősítési párbeszédpanelét bemutató képernyőkép](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha a PowerShellt a fiók feladatátvételének elindításához szeretné használni, először telepítenie kell a 6.0.1 előzetes verziójának modulját. A modul telepítéséhez kövesse az alábbi lépéseket:

1. Távolítsa el a Azure PowerShell összes korábbi telepítését:

    - Távolítsa el a Azure PowerShell korábbi telepítését a Windows rendszerből a **Beállítások**területen található **alkalmazások & szolgáltatások** beállítással.
    - Távolítsa el a `%Program Files%\WindowsPowerShell\Modules`összes **Azure** -modulját.

1. Győződjön meg arról, hogy a PowerShellGet legújabb verziója van telepítve. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot a legújabb verzió telepítéséhez:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. A PowerShellGet telepítése után zárjuk be és nyissa meg újra a PowerShell ablakot. 

1. Telepítse a Azure PowerShell legújabb verzióját:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Telepítsen egy Azure Storage Preview-modult, amely támogatja a fiók feladatátvételét:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Zárjuk be és nyissa meg újra a PowerShell ablakot.
 
A következő parancs végrehajtásával kezdeményezheti a fiók feladatátvételét a PowerShellből:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI-t szeretné használni a fiók feladatátvételének elindításához, hajtsa végre a következő parancsokat:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Következő lépések

- [Vész-helyreállítási és fiók-feladatátvétel (előzetes verzió) az Azure Storage-ban](storage-disaster-recovery-guidance.md)
- [Magas rendelkezésre állású alkalmazások tervezése az RA-GRS használatával](storage-designing-ha-apps-with-ragrs.md)
- [Oktatóanyag: kiválóan elérhető alkalmazás létrehozása blob Storage-val](../blobs/storage-create-geo-redundant-storage.md) 
