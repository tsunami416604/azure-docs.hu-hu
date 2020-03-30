---
title: Azure HPC-gyorsítótár létrehozása
description: Azure HPC-gyorsítótár-példány létrehozása
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: c6090d19ce530829b79dca69636c2123e2519961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129552"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC-gyorsítótár létrehozása

Az Azure Portal segítségével hozza létre a gyorsítótárat.

![képernyőkép a gyorsítótár áttekintéséről az Azure Portalon, alul a Létrehozás gombbal](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Alapvető részletek meghatározása

![képernyőkép a projekt részletei lapról az Azure Portalon](media/hpc-cache-create-basics.png)

A **Project Details (Projekt részletei)** területen válassza ki a gyorsítótárat üzemeltető előfizetést és erőforráscsoportot. Győződjön meg arról, hogy az előfizetés szerepel a [hozzáférési](hpc-cache-prereqs.md#azure-subscription) listán.

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

## <a name="add-resource-tags-optional"></a>Erőforráscímkék hozzáadása (nem kötelező)

A **Címkék** lap lehetővé teszi, hogy [erőforrás-címkéket](https://go.microsoft.com/fwlink/?linkid=873112) az Azure HPC cache-példány.

## <a name="finish-creating-the-cache"></a>A gyorsítótár létrehozásának befejezése

Az új gyorsítótár konfigurálása után kattintson a **Véleményezés + létrehozás** fülre. A portál ellenőrzi a beállításokat, és lehetővé teszi a választási lehetőségek áttekintését. Ha minden rendben van, kattintson a **Létrehozás gombra.**

A gyorsítótár létrehozása körülbelül 10 percet vesz igénybe. Az Azure Portal értesítési paneljén nyomon követheti a folyamatot.

![képernyőkép a gyorsítótár létrehozásáról szóló "üzembe helyezés folyamatban" és "értesítések" oldalakról a portálon](media/hpc-cache-deploy-status.png)

A létrehozás befejezéseután megjelenik egy értesítés az új Azure HPC-gyorsítótár-példányra mutató hivatkozással, és a gyorsítótár megjelenik az előfizetés **erőforrások** listájában.
<!-- double check on notification -->

![képernyőkép az Azure HPC cache-példányáról az Azure Portalon](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>További lépések

Miután a gyorsítótár megjelenik az **Erőforrások** listában, határozza meg a tárolási célokat, hogy a gyorsítótár hozzáférést biztosítson az adatforrásokhoz.

* [Céltárak hozzáadása](hpc-cache-add-storage.md)
