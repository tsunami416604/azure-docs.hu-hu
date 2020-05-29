---
title: A Storage-fiók feladatátvételének kezdeményezése
titleSuffix: Azure Storage
description: Megtudhatja, hogyan kezdeményezheti a fiók feladatátvételét abban az esetben, ha a Storage-fiók elsődleges végpontja elérhetetlenné válik. A feladatátvétel frissíti a másodlagos régiót, hogy az elsődleges régió legyen a Storage-fiók számára.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 905554d1763bdd3c5990a43c5c8d98f336e1c442
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171208"
---
# <a name="initiate-a-storage-account-failover"></a>A Storage-fiók feladatátvételének kezdeményezése

Ha a Geo-redundáns tárolási fiók elsődleges végpontja bármilyen okból elérhetetlenné válik, elindíthatja a fiók feladatátvételét. A fiók feladatátvétele frissíti a másodlagos végpontot, hogy az a Storage-fiók elsődleges végpontja legyen. A feladatátvétel befejeződése után az ügyfelek megkezdhetik az új elsődleges régióba való írást. A kényszerített feladatátvétel lehetővé teszi az alkalmazások magas rendelkezésre állásának fenntartását.

Ez a cikk bemutatja, hogyan kezdeményezheti a fiók feladatátvételét a Storage-fiókhoz a Azure Portal, a PowerShell vagy az Azure CLI használatával. A fiók feladatátvételével kapcsolatos további tudnivalókért tekintse meg a vész [-helyreállítási és a Storage-fiók feladatátvételét](storage-disaster-recovery-guidance.md)ismertető témakört.

> [!WARNING]
> A fiók feladatátvétele általában valamilyen adatvesztést eredményez. A fiók feladatátvételi következményeinek megismeréséhez és az adatvesztés előkészítéséhez tekintse át [a fiók feladatátvételi folyamatának megismerése](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)című témakört.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégzi a fiók feladatátvételét a Storage-fiókjában, ellenőrizze, hogy a Storage-fiók konfigurálva van-e a Geo-replikáláshoz. A Storage-fiók a következő redundancia-beállítások bármelyikét használhatja:

- Geo-redundáns tárolás (GRS) vagy olvasási hozzáférés geo-redundáns tárolás (RA-GRS)
- Geo-Zone-redundáns tárolás (GZRS) vagy olvasási hozzáférésű földrajzi zóna – redundáns tárolás (RA-GZRS)

Az Azure Storage redundanciával kapcsolatos további információkért lásd: [Azure Storage redundancia](storage-redundancy.md).

## <a name="initiate-the-failover"></a>A feladatátvétel kezdeményezése

## <a name="portal"></a>[Portál](#tab/azure-portal)

A fiók feladatátvételének elindításához a Azure Portal hajtsa végre az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
2. A **Beállítások**területen válassza a **geo-replikáció**lehetőséget. Az alábbi képen egy Storage-fiók geo-replikálási és feladatátvételi állapota látható.

    ![A földrajzi replikálást és a feladatátvételi állapotot ábrázoló képernyőkép](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Ellenőrizze, hogy a Storage-fiókja a Geo-redundáns tároláshoz (GRS) van-e konfigurálva, vagy hogy van-e olvasási hozzáférésű geo-redundáns tárolás (RA-GRS). Ha nem, akkor a **Beállítások** területen válassza a **Konfigurálás** lehetőséget, hogy a fiókját a Geo-redundáns értékre frissítse.
4. A **Legutóbbi szinkronizálási idő** tulajdonság azt jelzi, hogy a másodlagos állapot meddig marad az elsődlegesnél. A **legutóbbi szinkronizálás ideje** azt az adatvesztés mértékét adja meg, amelyet a feladatátvétel befejezése után fog tapasztalni. A **legutóbbi szinkronizálás időpontjának** ellenőrzésével kapcsolatos további információkért tekintse [meg a Storage-fiók utolsó szinkronizálási idejének tulajdonságát](last-sync-time-get.md).
5. Válassza **a feladatátvétel előkészítése**lehetőséget.
6. Tekintse át a megerősítő párbeszédpanelt. Ha elkészült, az **Igen** gombra kattintva erősítse meg és kezdeményezheti a feladatátvételt.

    ![A fiók feladatátvételének megerősítési párbeszédpanelét bemutató képernyőkép](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A fiók feladatátvételi funkciója általánosan elérhető, de továbbra is a PowerShell előzetes verziójú moduljára támaszkodik. Ha a PowerShellt a fiók feladatátvételének elindításához szeretné használni, először telepítenie kell az az. Storage [1.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) modult. A modul telepítéséhez kövesse az alábbi lépéseket:

1. Távolítsa el a Azure PowerShell összes korábbi telepítését:

    - Távolítsa el a Azure PowerShell korábbi telepítését a Windows rendszerből a **Beállítások**területen található **alkalmazások & szolgáltatások** beállítással.
    - Távolítsa el az összes **Azure** -modult `%Program Files%\WindowsPowerShell\Modules` .

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

A következő parancs végrehajtásával kezdeményezheti a fiók feladatátvételét a PowerShellből:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI-t szeretné használni a fiók feladatátvételének elindításához, hajtsa végre a következő parancsokat:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>A fiók feladatátvételének fontos következményei

Amikor elindít egy fiókot a Storage-fiókhoz, a másodlagos végpont DNS-rekordjai frissülnek, így a másodlagos végpont lesz az elsődleges végpont. A feladatátvétel kezdeményezése előtt győződjön meg arról, hogy tisztában van a Storage-fiók lehetséges hatásával.

Ha a feladatátvétel elindítása előtt szeretné megbecsülni a várható adatvesztés mértékét, tekintse meg a **legutóbbi szinkronizálás időpontja** tulajdonságot. A **legutóbbi szinkronizálás időpontjának** ellenőrzésével kapcsolatos további információkért tekintse [meg a Storage-fiók utolsó szinkronizálási idejének tulajdonságát](last-sync-time-get.md).

A feladatátvételt követően a rendszer automatikusan átalakítja a Storage-fiók típusát a helyileg redundáns tárterületre (LRS) az új elsődleges régióban. Újra engedélyezheti a Geo-redundáns tárolást (GRS), vagy a fiókhoz tartozó olvasási hozzáférésű geo-redundáns tárolást (RA-GRS). Vegye figyelembe, hogy a LRS-ről GRS-re vagy RA-GRS-re való átalakítás további költségekkel jár. További információ: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/).

Miután újraengedélyezte a GRS a Storage-fiókjához, a Microsoft elkezdi replikálni a fiókjában lévő adatait az új másodlagos régióba. A replikálási idő a replikált adatmennyiségtől függ.  

## <a name="next-steps"></a>Következő lépések

- [Vész-helyreállítási és Storage-fiók feladatátvétele](storage-disaster-recovery-guidance.md)
- [A Storage-fiók utolsó szinkronizálási ideje tulajdonságának megtekintése](last-sync-time-get.md)
- [A Geo-redundancia használata a magasan elérhető alkalmazások kialakításához](geo-redundant-design.md)
- [Oktatóanyag: kiválóan elérhető alkalmazás létrehozása blob Storage-val](../blobs/storage-create-geo-redundant-storage.md)
