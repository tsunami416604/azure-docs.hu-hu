---
title: Az Azure HPC-gyorsítótár kezelése és frissítése
description: Az Azure HPC-gyorsítótár kezelése és frissítése az Azure Portalon
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: da260074fc69fac9e98d3698bb2d40fdf80d7118
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252042"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>A gyorsítótár kezelése az Azure Portalról

Az Azure Portal gyorsítótárát áttekintő lapja a projekt részleteit, a gyorsítótár állapotát és a gyorsítótár alapvető statisztikáit jeleníti meg. Azt is ellenőrzi, hogy állítsa le vagy indítsa el a gyorsítótárat, törölje a gyorsítótárat, flush adatok at hosszú távú tárolás, és a szoftver frissítése.

Az áttekintő lap megnyitásához válassza ki a gyorsítótár-erőforrást az Azure Portalon. Töltse be például a **Minden erőforrás** lapot, és kattintson a gyorsítótár nevére.

![képernyőkép egy Azure HPC-gyorsítótár példány áttekintése lapról](media/hpc-cache-overview.png)

A lap tetején található gombok segítségével kezelheti a gyorsítótárat:

* **Indítás** és [**leállítás**](#stop-the-cache) – Gyorsítótár-művelet felfüggesztése
* [**Kiürítés**](#flush-cached-data) – A módosított adatok at tárolási célokba írása
* [**Frissítés**](#upgrade-cache-software) – Frissíti a gyorsítótár-szoftvert
* **Frissítés** - Az áttekintő lap újratöltése
* [**Törlés**](#delete-the-cache) - Véglegesen megsemmisíti a gyorsítótárat

Tudjon meg többet ezekről a lehetőségekről az alábbiakban.

## <a name="stop-the-cache"></a>A gyorsítótár leállítása

A gyorsítótár leállításával csökkentheti a költségeket egy inaktív időszakban. A gyorsítótár leállítása kor nem számít unk fel díjat az uptime-ért, de a gyorsítótár lefoglalt lemezes tárolásáért kell fizetnie. (A [részleteket](https://aka.ms/hpc-cache-pricing) lásd az árképzési oldalon.)

A leállított gyorsítótár nem válaszol az ügyfélkérésekre. A gyorsítótár leállítása előtt le kell választania az ügyfeleket.

A **Leállítás** gomb felfüggeszti az aktív gyorsítótárat. A **Leállítás** gomb akkor érhető el, ha a gyorsítótár állapota **kifogástalan** vagy **degradálódott.**

![képernyőkép a felső gombokról, kiemelve a Stop gombokkal, és egy előugró üzenet, amely leírja a stop műveletet, és megkérdezi, hogy "folytatja?". igennel (alapértelmezett) és Nincs gombokkal](media/stop-cache.png)

Miután az Igen gombra kattintott a gyorsítótár leállításának megerősítéséhez, a gyorsítótár automatikusan kiüríti annak tartalmát a tárolócélokba. Ez a folyamat eltarthat egy ideig, de biztosítja az adatok konzisztenciáját. Végül a gyorsítótár állapota **Leállítva**állapotra változik.

A leállított gyorsítótár újraaktiválásához kattintson a **Start** gombra. Nincs szükség megerősítésre.

![képernyőkép a felső gombokról, kiemelt Kezdőképernyővel](media/start-cache.png)

## <a name="flush-cached-data"></a>Gyorsítótárazott adatok kiürítése

Az áttekintő lap **Kiürítés** gombja arra utasítja a gyorsítótárat, hogy azonnal írja a gyorsítótárban tárolt összes módosított adatot a háttértároló-célokba. A gyorsítótár rendszeresen menti az adatokat a tárolási célokba, így ezt nem szükséges manuálisan elvégezni, hacsak nem szeretne győződjön meg arról, hogy a háttértároló rendszer naprakész. Használhatja például a **Kiürítést,** mielőtt tárolópillanatképet készítne, vagy ellenőrizné az adatkészlet méretét.

> [!NOTE]
> A kiürítési folyamat során a gyorsítótár nem tudja kiszolgálni az ügyfélkérelmeket. A gyorsítótár-hozzáférés felfüggesztésre kerül, és a művelet befejezése után folytatódik.

![képernyőkép a felső gombokról, kiemelve a Flush gombokkal, és egy előugró üzenet, amely leírja a kiürítési műveletet, és megkérdezi, hogy "folytatja-e?" igennel (alapértelmezett) és Nincs gombokkal](media/hpc-cache-flush.png)

A gyorsítótár kiürítési műveletének indításakor a gyorsítótár nem fogadja az ügyfélkérelmeket, és az áttekintő lapon a gyorsítótár állapota **a Kiürítés**állapotára változik.

A gyorsítótárban lévő adatok a megfelelő tárolási célokba kerülnek. Attól függően, hogy mennyi adatot kell kiüríteni, a folyamat eltarthat néhány percig vagy több mint egy órát.

Miután az összes adatot mentette a tárolási célokba, a gyorsítótár automatikusan elindítja az ügyfélkérelmek ismételt teljesítését. A gyorsítótár állapota kifogástalan **lesz.**

## <a name="upgrade-cache-software"></a>Frissítési gyorsítótár-szoftver

Ha elérhető egy új szoftververzió, a **Frissítés** gomb aktívvá válik. Az oldal tetején is megjelenik egy üzenet a szoftverek frissítéséről.

![képernyőkép a gombok felső soráról, amelyen a Frissítés gomb van engedélyezve](media/hpc-cache-upgrade-button.png)

Az ügyfélhozzáférés nem szakad meg a szoftverfrissítés során, de a gyorsítótár teljesítménye lelassul. Tervezze meg a szoftver frissítését a csúcsidőn kívüli használati időben vagy egy tervezett karbantartási időszakban.

A szoftverfrissítés több órát is igénybe vehet. A nagyobb átviteli értékkel konfigurált gyorsítótárak frissítése hosszabb időt vesz igénybe, mint a kisebb csúcsátviteli értékkel rendelkező gyorsítótárak.

Ha egy szoftverfrissítés elérhető, akkor lesz egy hét múlva alkalmazni kézzel. A befejezési dátum szerepel a frissítési üzenetben. Ha ez idő alatt nem frissít, az Azure automatikusan alkalmazza a frissítést a gyorsítótárra. Az automatikus frissítés időzítése nem konfigurálható. Ha aggódik a gyorsítótár teljesítményére gyakorolt hatás miatt, az időszak lejárta előtt saját maga kell frissítenie a szoftvert.

Ha a gyorsítótár leáll, amikor a befejezési dátum lejár, a gyorsítótár automatikusan frissíti a szoftvert a következő indításkor. (Előfordulhat, hogy a frissítés nem indul el azonnal, de az első órában indul el.)

A **szoftverfrissítés** megkezdéséhez kattintson a Frissítés gombra. A gyorsítótár állapota **frissítésre változik,** amíg a művelet be nem fejeződik.

## <a name="delete-the-cache"></a>A gyorsítótár törlése

A **Törlés** gomb elpusztítja a gyorsítótárat. Gyorsítótár törlésekor az összes erőforrása megsemmisül, és a továbbiakban nem merülnek fel fiókköltségek.

A tárolótárolóként használt háttértároló-kötetek nem változnak a gyorsítótár törlésekor. Később hozzáadhatja őket egy jövőbeli gyorsítótárhoz, vagy külön-külön leszerelheti őket.

> [!NOTE]
> Az Azure HPC-gyorsítótár nem automatikusan írja a módosított adatokat a gyorsítótárból a háttértároló rendszereka gyorsítótár törlése előtt.
>
> Annak érdekében, hogy a gyorsítótárban lévő összes adat hosszú távú tárolóba legyen írva, a törlés előtt [állítsa le a gyorsítótárat.](#stop-the-cache) A törlés gombra kattintás előtt győződjön meg arról, hogy a **Leállítva** állapot látható.
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>Gyorsítótár-mérőszámok és figyelés

Az áttekintő lap grafikonokat jelenít meg néhány alapvető gyorsítótár-statisztikához – gyorsítótár átviteli teljesítmény, másodpercenkénti műveletek és késés.

![képernyőkép három vonaldiagramról, amelyek a fent említett statisztikákat mutatják egy mintagyorsítótárhoz](media/hpc-cache-overview-stats.png)

Ezek a diagramok az Azure beépített figyelési és elemzési eszközeinek részét képezik. További eszközök és riasztások érhetők el a portál **oldalsávfigyelés** fejlécében található oldalakról. További információ az Azure [Monitoring](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)dokumentációportálszakaszában található.

## <a name="next-steps"></a>További lépések

<!-- * Learn more about metrics and statistics for hpc cache -->
* További információ az [Azure-metrikákról és statisztikai eszközökről](../azure-monitor/index.yml)
* [Segítség az Azure HPC-gyorsítótárral kapcsolatban](hpc-cache-support-ticket.md)
