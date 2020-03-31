---
title: Tárfiók feladatátvételének kezdeményezése (előzetes verzió) – Azure Storage
description: Ismerje meg, hogyan kezdeményezhet egy fiók feladatátvételt abban az esetben, ha a tárfiók elsődleges végpontja elérhetetlenné válik. A feladatátvétel frissíti a másodlagos régió lesz a tárfiók elsődleges régió.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0c619224201d6225d5e5c127b342f71f2f7fced9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535352"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Tárfiók feladatátvételének kezdeményezése (előzetes verzió)

Ha a georedundáns tárfiók elsődleges végpontja bármilyen okból elérhetetlenné válik, kezdeményezhet egy fiók feladatátvételt (előzetes verzió). Egy fiók feladatátvételi frissíti a másodlagos végpont lesz az elsődleges végpont a tárfiók. A feladatátvétel befejezése után az ügyfelek megkezdhetik az írást az új elsődleges régióba. A kényszerített feladatátvétel lehetővé teszi az alkalmazások magas rendelkezésre állását.

Ez a cikk bemutatja, hogyan kezdeményezhet egy fiók feladatátvételt a tárfiókhoz az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül. Ha többet szeretne megtudni a fiók feladatátvételről, olvassa el a [Vész-helyreállítási és fiókfeladat-átvételi (előzetes verzió) című témakört az Azure Storage-ban.](storage-disaster-recovery-guidance.md)

> [!WARNING]
> A fiók feladatátvétele általában adatvesztést eredményez. A fiók feladatátvételkövetkezményeinek megértéséhez és az adatvesztésre való felkészüléshez tekintse [át a Fiók feladatátvételi folyamatának ismertetése](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)című részét.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt fiókfeladat-átvételt hajthatna végre a tárfiókon, győződjön meg arról, hogy végrehajtotta a következő lépést:

- Győződjön meg arról, hogy a tárfiók georedundáns tárolás (GRS) vagy olvasási hozzáférésű georedundáns tárolás (RA-GRS) használatára van konfigurálva. A georedundáns tárolásról az [Azure Storage redundanciája](storage-redundancy.md)című témakörben talál további információt.

## <a name="important-implications-of-account-failover"></a>A fiókfeladat-átvétel fontos következményei

Amikor fiókfeladat-átvételt kezdeményez a tárfiókhoz, a másodlagos végpont DNS-rekordjai frissülnek, így a másodlagos végpont lesz az elsődleges végpont. A feladatátvétel kezdeményezése előtt győződjön meg arról, hogy tisztában van a tárfiókra gyakorolt lehetséges hatásával.

A feladatátvétel megkezdése előtt becsült adatok elvesztésének mértékének becsléséhez `Get-AzStorageAccount` ellenőrizze az **Utolsó szinkronizálási idő** tulajdonságot a PowerShell-parancsmag használatával, és adja meg a `-IncludeGeoReplicationStats` paramétert. Ezután `GeoReplicationStats` ellenőrizze a szálláshely számláját. \

A feladatátvétel után a tárfiók típusa automatikusan átalakul helyileg redundáns tároló (LRS) az új elsődleges régióban. Újra engedélyezheti a georedundáns tárolást (GRS) vagy az olvasási hozzáférésű georedundáns tárolást (RA-GRS) a fiókhoz. Vegye figyelembe, hogy az LRS-ről GRS-re vagy RA-GRS-re történő átalakítás többletköltséget von maga után. További információt a [Sávszélesség-díjszabás részletei című témakörben talál.](https://azure.microsoft.com/pricing/details/bandwidth/)

Miután újra engedélyezte a GRS-t a tárfiókhoz, a Microsoft megkezdi a fiókban lévő adatok replikálását az új másodlagos régióba. A replikáció ideje a replikált adatok mennyiségétől függ.  

## <a name="portal"></a>[Portál](#tab/azure-portal)

Ha fiókfeladat-átvételt szeretne kezdeményezni az Azure Portalról, kövesse az alábbi lépéseket:

1. Nyissa meg a tárfiókot.
2. A **Beállítások csoportban**válassza a **Georeplikáció**lehetőséget. Az alábbi képen egy tárfiók georeplikációs és feladatátvételi állapota látható.

    ![A georeplikációt és a feladatátvétel állapotát ábrázoló képernyőkép](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Ellenőrizze, hogy a tárfiók georedundáns tárolásra (GRS) vagy olvasási hozzáférésű georedundáns tárolásra (RA-GRS) van-e konfigurálva. Ha nem, akkor válassza a Beállítások **csoport** **konfigurációja** lehetőséget a fiók georedundánsként való frissítéséhez. 
4. Az **utolsó szinkronizálási idő** tulajdonság azt jelzi, hogy a másodlagos milyen messze van az elsődleges mögött. **Az utolsó szinkronizálási idő** becslést ad az adatvesztés mértékéről, amelyet a feladatátvétel befejezése után fog tapasztalni.
5. Válassza **a Felkészülés feladatátvételre (előzetes verzió)** lehetőséget. 
6. Tekintse át a megerősítést kérő párbeszédpanelt. Ha készen áll, írja be **az Igen** értéket a feladatátvétel megerősítéséhez és elindításához.

    ![Egy fiók feladatátvételének megerősítést kérő párbeszédpanelét bemutató képernyőkép](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A PowerShell használatával kezdeményezzen egy fiók feladatátvételt, először telepítenie kell a 6.0.1-es előzetes modult. A modul telepítéséhez kövesse az alábbi lépéseket:

1. Távolítsa el az Azure PowerShell korábbi telepítéseit:

    - Távolítsa el az Azure PowerShell korábbi telepítéseit a Windowsból az **Alkalmazások & szolgáltatások** **beállításokkal**a Beállítások területen.
    - Az összes **Azure-modul** eltávolítása a ból. `%Program Files%\WindowsPowerShell\Modules`

1. Győződjön meg arról, hogy a PowerShellTelepített legújabb verziójával rendelkezik. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot a legújabb verzió telepítéséhez:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Zárja be, majd nyissa meg újra a PowerShell-ablakot a PowerShellGet telepítése után. 

1. Telepítse az Azure PowerShell legújabb verzióját:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Telepítsen egy Azure Storage-előnézeti modult, amely támogatja a fiók feladatátvételt:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. Zárja be, majd nyissa meg újra a PowerShell-ablakot.
 
Ha a PowerShellből szeretne fiókfeladat-átvételt kezdeményezni, hajtsa végre a következő parancsot:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI használatával kezdeményezegy fiókfeladat-átvételt, hajtsa végre a következő parancsokat:

```azurecli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>További lépések

- [Vészhelyreállítás és fiókfeladat-átvétel (előzetes verzió) az Azure Storage-ban](storage-disaster-recovery-guidance.md)
- [Magas rendelkezésre állású alkalmazások tervezése az RA-GRS használatával](storage-designing-ha-apps-with-ragrs.md)
- [Oktatóanyag: Magas rendelkezésre állású alkalmazás létrehozása a Blob storage segítségével](../blobs/storage-create-geo-redundant-storage.md) 
