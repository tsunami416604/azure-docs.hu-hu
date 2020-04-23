---
title: Azure HPC-gyorsítótár létrehozása
description: Azure HPC cache-példány létrehozása
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: efa9037b345cdfc5f165e9c5e0c1831ea97b52ed
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106491"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC-gyorsítótár létrehozása

A gyorsítótár létrehozásához használja a Azure Portal.

![képernyőkép a gyorsítótár áttekintéséről Azure Portalban, a létrehozás gombbal alul](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Alapszintű részletek meghatározása

![képernyőkép a Project details lapról Azure Portal](media/hpc-cache-create-basics.png)

A **projekt részletei**területen válassza ki azt az előfizetést és erőforráscsoportot, amely a gyorsítótárat fogja tárolni. Győződjön meg arról, hogy az előfizetés szerepel a [hozzáférési](hpc-cache-prereqs.md#azure-subscription) listán.

A **szolgáltatás részletei**területen adja meg a gyorsítótár nevét és a többi attribútumot:

* Hely – válasszon egy [támogatott régiót](hpc-cache-overview.md#region-availability).
* Virtuális hálózat – választhat egy meglévőt, vagy létrehozhat egy új virtuális hálózatot.
* Alhálózat – válasszon ki vagy hozzon létre legalább 64 IP-címmel rendelkező alhálózatot (/24), amelyet csak ehhez az Azure HPC cache-példányhoz fog használni.

## <a name="set-cache-capacity"></a>Gyorsítótár kapacitásának beállítása
<!-- referenced from GUI - update aka.ms link if you change this header text -->

A **gyorsítótár** lapon be kell állítania a gyorsítótár kapacitását. Az itt megadott értékek határozzák meg, hogy a gyorsítótár milyen mennyiségű adattal rendelkezhet, és hogy milyen gyorsan lehet az ügyfelek kéréseinek kiszolgálására.

A kapacitás a gyorsítótár költségeit is befolyásolja.

Válassza ki a kapacitást a következő két érték beállításával:

* A gyorsítótár (átviteli sebesség) maximális adatátviteli sebessége GB/másodpercben
* A gyorsítótárazott adathoz lefoglalt tárterület mennyisége (TB)

Válassza ki az elérhető átviteli sebesség és a gyorsítótár tárolási méretének egyikét.

Ne feledje, hogy a tényleges adatátviteli sebesség a munkaterhelés, a hálózati sebesség és a tárolási célok típusától függ. A megadott értékek a teljes gyorsítótárrendszer maximális átviteli sebességét határozzák meg, de ezek közül néhányat a rendszer a terhelési feladatokhoz használ. Ha például egy ügyfél olyan fájlt kér, amely még nem található meg a gyorsítótárban, vagy ha a fájl elavultként van megjelölve, a gyorsítótár a háttérbeli tárolóból beolvassa az átviteli sebességét.

Az Azure HPC gyorsítótára felügyeli, hogy mely fájlok vannak gyorsítótárazva és előre betöltve a gyorsítótár találati arányának maximalizálása érdekében. A gyorsítótár tartalmának folyamatos ellenőrzése megtörténik, és a fájlok átkerülnek a hosszú távú tárolásba, ha ritkábban férnek hozzá. Válasszon egy olyan gyorsítótár-tárolási méretet, amely kényelmesen tárolhatja a munkafájlok aktív készletét, és további helyet biztosít a metaadatok és egyéb terhelések számára.

![a gyorsítótár-méretezés oldalának képernyőképe](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Azure Key Vault titkosítás engedélyezése (nem kötelező)

Ha a gyorsítótár olyan régióban található, amely támogatja az ügyfél által felügyelt titkosítási kulcsokat, a **lemez titkosítási kulcsainak** lapja a **gyorsítótár** és a **címkék** lapok között jelenik meg. A közzététel időpontjában ez a lehetőség az USA keleti régiójában, az USA déli középső régiójában és az USA 2. nyugati régiójában támogatott.

Ha szeretné kezelni a gyorsítótár-tárolóhoz használt titkosítási kulcsokat, adja meg a Azure Key Vault adatait a **lemez titkosítási kulcsainak** oldalán. A kulcstárolónak ugyanabban a régióban és ugyanabban az előfizetésben kell lennie, mint a gyorsítótárnak.

Ezt a szakaszt kihagyhatja, ha nincs szüksége az ügyfél által felügyelt kulcsokra. Alapértelmezés szerint az Azure a Microsoft által felügyelt kulcsokkal titkosítja az adataikat. További információért olvassa el az [Azure Storage-titkosítást](../storage/common/storage-service-encryption.md) ismertető témakört.

> [!NOTE]
>
> * A gyorsítótár létrehozása után nem válthat a Microsoft által felügyelt kulcsok és az ügyfél által felügyelt kulcsok között.
> * A gyorsítótár létrehozása után engedélyeznie kell, hogy hozzáférjen a kulcstartóhoz. Kattintson a titkosítás **engedélyezése** gombra a gyorsítótár **Áttekintés** lapján a titkosítás bekapcsolásához. Ezt a lépést a gyorsítótár létrehozása 90 percen belül elvégezheti.
> * A gyorsítótár-lemezek az engedélyezés után jönnek létre. Ez azt jelenti, hogy a kezdeti gyorsítótár-létrehozási idő rövid, de a gyorsítótár nem áll készen a hozzáférés engedélyezése után tíz vagy több percig.

Az ügyfél által felügyelt kulcs titkosítási folyamat teljes körű ismertetését az [ügyfél által felügyelt titkosítási kulcsok használata az Azure HPC cache-hez című cikk](customer-keys.md)ismerteti.

![a titkosítási kulcsok oldalának képernyőképe a "felhasználó által felügyelt" beállítással és a Key Vault mezőinek megjelenítésével](media/create-encryption.png)

Válassza a **felügyelt ügyfél** lehetőséget az ügyfél által felügyelt kulcs titkosításának kiválasztásához. Megjelenik a Key Vault specifikáció mezői. Válassza ki a használni kívánt Azure Key Vault, majd válassza ki a gyorsítótárhoz használni kívánt kulcsot és verziót. A kulcsnak 2048 bites RSA-kulcsnak kell lennie. Ezen a lapon létrehozhat egy új kulcstartót, kulcsot vagy kulcsot tartalmazó verziót.

A gyorsítótár létrehozása után engedélyeznie kell azt a Key Vault szolgáltatás használatához. További részletek: [Azure Key Vault titkosítás engedélyezése a gyorsítótárból](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) .

## <a name="add-resource-tags-optional"></a>Erőforrás-Címkék hozzáadása (nem kötelező)

A **címkék** lapon hozzáadhat erőforrás- [CÍMKÉKET](https://go.microsoft.com/fwlink/?linkid=873112) az Azure HPC cache-példányhoz.

## <a name="finish-creating-the-cache"></a>A gyorsítótár létrehozásának befejezése

Az új gyorsítótár konfigurálása után kattintson a **felülvizsgálat + létrehozás** fülre. A portál ellenőrzi a beállításokat, és lehetővé teszi a lehetőségek áttekintését. Ha minden helyes, kattintson a **Létrehozás**gombra.

A gyorsítótár létrehozása körülbelül 10 percet vesz igénybe. A folyamat nyomon követhető a Azure Portal értesítések paneljén.

![képernyőkép a gyorsítótár létrehozásáról "üzembe helyezés folyamatban" és "értesítések" oldalain a portálon](media/hpc-cache-deploy-status.png)

A létrehozás befejeződése után egy értesítés jelenik meg az új Azure HPC cache-példányra mutató hivatkozással, a gyorsítótár pedig megjelenik az előfizetés **erőforrások** listájában.

![képernyőkép az Azure HPC cache-példányról Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Ha a gyorsítótár ügyfél által felügyelt titkosítási kulcsokat használ, előfordulhat, hogy a gyorsítótár megjelenik az erőforrások listájában, mielőtt a központi telepítés állapota befejeződik. Amint a gyorsítótár állapota a **kulcsra vár** , [engedélyezheti](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a kulcstároló használatát.

## <a name="next-steps"></a>További lépések

Miután a gyorsítótár megjelenik az **erőforrások** listájában, átléphet a következő lépésre.

* [Adja meg a tárolási célokat](hpc-cache-add-storage.md) , hogy a gyorsítótár hozzáférjen az adatforrásokhoz.
* Ha ügyfél által felügyelt titkosítási kulcsokat használ, engedélyeznie kell [Azure Key Vault titkosítást](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a gyorsítótár áttekintés lapján a gyorsítótár telepítésének befejezéséhez. Ezt a lépést a tárterület hozzáadása előtt kell végrehajtania. A részletekért olvassa el az [ügyfél által felügyelt titkosítási kulcsok használata](customer-keys.md) című leírást.
