---
title: Azure HPC-gyorsítótár létrehozása
description: Azure HPC-gyorsítótár-példány létrehozása
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: befbe2435a518b82cf5a3ab12e6129aa3ce5c22b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537974"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC-gyorsítótár létrehozása

Az Azure Portal segítségével hozza létre a gyorsítótárat.

![képernyőkép a gyorsítótár áttekintéséről az Azure Portalon, alul a Létrehozás gombbal](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Alapvető részletek meghatározása

![képernyőkép a projekt részletei lapról az Azure Portalon](media/hpc-cache-create-basics.png)

A **Project Details (Projekt részletei)** területen válassza ki a gyorsítótárat üzemeltető előfizetést és erőforráscsoportot.

A **Szolgáltatás részletei területen**állítsa be a gyorsítótár nevét és az alábbi attribútumokat:

* Hely – Válasszon egyet a [támogatott régiók közül.](hpc-cache-overview.md#region-availability)
* Virtuális hálózat – Kiválaszthat egy meglévőt, vagy létrehozhat egy új virtuális hálózatot.
* Alhálózat – Válasszon vagy hozzon létre egy legalább 64 IP-című (/24) alhálózatot, amely csak ehhez az Azure HPC-gyorsítótár-példányhoz lesz használva.

## <a name="set-cache-capacity"></a>Gyorsítótár-kapacitás beállítása
<!-- referenced from GUI - update aka.ms link if you change this header text -->

A **Gyorsítótár** lapon be kell állítania a gyorsítótár kapacitását. Az itt megadott értékek határozzák meg, hogy a gyorsítótár mennyi adatot tárolhat, és milyen gyorsan tudja kiszolgálni az ügyfélkérelmeket.

A kapacitás a gyorsítótár költségére is hatással van.

A következő két érték beállításával válassza ki a kapacitást:

* A gyorsítótár maximális adatátviteli sebessége (átviteli sebesség) GB/másodpercben
* A gyorsítótárazott adatok számára lefoglalt tárterület mennyisége TB-ben

Válasszon egyet a rendelkezésre álló átviteli értékek és a gyorsítótár tárolási méretek közül.

Ne feledje, hogy a tényleges adatátviteli sebesség a munkaterheléstől, a hálózati sebességtől és a tárolási célok típusától függ. A választott értékek a teljes gyorsítótár-rendszer maximális átviteli értékét határozzák meg, de ezek közül néhány általános feladatokhoz használatos. Ha például egy ügyfél olyan fájlt kér, amely még nincs a gyorsítótárban tárolva, vagy ha a fájl elavultként van megjelölve, a gyorsítótár az átviteli kapacitás egy részét használja a háttér-tárolóból való lekéréshez.

Az Azure HPC cache kezeli, hogy mely fájlok gyorsítótárazott és előre betöltött, hogy maximalizálja a gyorsítótár találati arány. A gyorsítótár tartalmát folyamatosan értékeli, és a fájlokat áthelyezi a hosszú távú tárolóba, amikor ritkábban férnek hozzá. Válasszon olyan gyorsítótár-tárolóméretet, amely kényelmesen elfér a munkafájlok aktív készletében, és további helyet biztosít a metaadatok és egyéb terhelések számára.

![képernyőkép a gyorsítótár méretezési lapjáról](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Az Azure Key Vault titkosításának engedélyezése (nem kötelező)

Ha a gyorsítótár olyan régióban található, amely támogatja az ügyfél által felügyelt titkosítási kulcsokat, a **Lemeztitkosítási kulcsok** lap jelenik meg a **Gyorsítótár** és a **Címkék** lapok között. A közzétételi időtől ez a lehetőség az USA keleti régiója, az USA déli középső régiója és az USA nyugati régiója 2 régióban támogatott.

Ha a gyorsítótár-tárolóhoz használt titkosítási kulcsokat szeretné kezelni, adja meg az Azure Key Vault adatait a **Lemeztitkosítási kulcsok** lapon. A key vault kell ugyanabban a régióban, és ugyanabban az előfizetésben, mint a gyorsítótár.

Ezt a szakaszt kihagyhatja, ha nincs szüksége ügyfél által felügyelt kulcsokra. Az Azure alapértelmezés szerint microsoftáltal kezelt kulcsokkal titkosítja az adatokat. További információért olvassa el az [Azure-tárolótitkosítást.](../storage/common/storage-service-encryption.md)

> [!NOTE]
>
> * A gyorsítótár létrehozása után nem lehet módosítani a Microsoft által kezelt kulcsok és az ügyfél által kezelt kulcsok között.
> * A gyorsítótár létrehozása után engedélyeznie kell, hogy hozzáférjen a key vaulthoz. A titkosítás bekapcsolásához kattintson a **titkosítás engedélyezése** gombra a gyorsítótár **áttekintése** lapon. Ezt a lépést a gyorsítótár létrehozását követő 90 percen belül tegye meg.
> * A gyorsítótárlemezek az engedélyezés után jönnek létre. Ez azt jelenti, hogy a kezdeti gyorsítótár-létrehozási idő rövid, de a gyorsítótár a hozzáférés engedélyezése után már tíz percig vagy tovább nem lesz használatra.

Az ügyfél által felügyelt kulcstitkosítási folyamat teljes magyarázata az [Azure HPC-gyorsítótárügyfél által felügyelt titkosítási kulcsok használata](customer-keys.md)című olvassa el.

![képernyőkép a titkosítási kulcsok lapról, amelyen az "ügyfél által kezelt" beállítás és a kulcstartó mezői láthatók](media/create-encryption.png)

Válassza az Ügyfél által kezelt kulcstitkosítás kiválasztásához **kiválasztott Ügyfél által kezelt** kulcstitkosítást. Megjelennek a key vault specifikációs mezői. Válassza ki a használni kívánt Azure Key Vaultot, majd válassza ki a gyorsítótárhoz használandó kulcsot és verziót. A kulcsnak 2048 bites RSA-kulcsnak kell lennie. Ezen a lapon új kulcstartót, kulcsot vagy kulcsverziót hozhat létre.

A gyorsítótár létrehozása után engedélyeznie kell, hogy használja a key vault szolgáltatás használatát. A részletekért olvassa [el az Azure Key Vault titkosításának engedélyezése a gyorsítótárból](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) című olvasni.

## <a name="add-resource-tags-optional"></a>Erőforráscímkék hozzáadása (nem kötelező)

A **Címkék** lap lehetővé teszi, hogy [erőforrás-címkéket](https://go.microsoft.com/fwlink/?linkid=873112) az Azure HPC cache-példány.

## <a name="finish-creating-the-cache"></a>A gyorsítótár létrehozásának befejezése

Az új gyorsítótár konfigurálása után kattintson a **Véleményezés + létrehozás** fülre. A portál ellenőrzi a beállításokat, és lehetővé teszi a választási lehetőségek áttekintését. Ha minden rendben van, kattintson a **Létrehozás gombra.**

A gyorsítótár létrehozása körülbelül 10 percet vesz igénybe. Az Azure Portal értesítési paneljén nyomon követheti a folyamatot.

![képernyőkép a gyorsítótár létrehozásáról szóló "üzembe helyezés folyamatban" és "értesítések" oldalakról a portálon](media/hpc-cache-deploy-status.png)

A létrehozás befejezéseután megjelenik egy értesítés az új Azure HPC-gyorsítótár-példányra mutató hivatkozással, és a gyorsítótár megjelenik az előfizetés **erőforrások** listájában.

![képernyőkép az Azure HPC cache-példányáról az Azure Portalon](media/hpc-cache-new-overview.png)

> [!NOTE]
> Ha a gyorsítótár ügyfél által felügyelt titkosítási kulcsokat használ, a gyorsítótár megjelenhet az erőforrások listájában, mielőtt a központi telepítés állapota befejeződne. Amint a gyorsítótár állapota **kulcsra vár,** [engedélyezheti, hogy](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) használja a key vault.

## <a name="next-steps"></a>További lépések

Miután a gyorsítótár megjelenik az **Erőforrások** listában, továbbléphet a következő lépésre.

* [Tárolási célok definiálása,](hpc-cache-add-storage.md) hogy a gyorsítótár hozzáférhessen az adatforrásokhoz.
* Ha ügyfél által felügyelt titkosítási kulcsokat használ, engedélyeznie kell az [Azure Key Vault titkosítását](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) a gyorsítótár áttekintő lapjáról a gyorsítótár beállításának befejezéséhez. Ezt a lépést a tárhely hozzáadása előtt kell megtennie. Olvassa [el Az ügyfél által felügyelt titkosítási kulcsok használata](customer-keys.md) a részletekért.
