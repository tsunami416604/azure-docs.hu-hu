---
title: Az Azure HPC cache kezelése és frissítése
description: Az Azure HPC cache kezelése és frissítése a Azure Portal használatával
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: da260074fc69fac9e98d3698bb2d40fdf80d7118
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252042"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>A gyorsítótár kezelése a Azure Portal

A gyorsítótár Áttekintés lapja a Azure Portal megjeleníti a projekt részleteit, a gyorsítótár állapotát és a gyorsítótár alapszintű statisztikáit. Emellett a gyorsítótár leállításához vagy elindításához, a gyorsítótár törléséhez, az adatürítéshez és a szoftver frissítéséhez szükséges vezérlőkkel is rendelkezik.

Az Áttekintés lap megnyitásához válassza ki a gyorsítótár-erőforrást a Azure Portal. Töltse be például a **minden erőforrás** lapot, és kattintson a gyorsítótár nevére.

![Az Azure HPC cache-példány áttekintési oldalának képernyőképe](media/hpc-cache-overview.png)

A lap tetején található gombok segítenek a gyorsítótár kezelésében:

* **Indítás** és [**Leállítás**](#stop-the-cache) – gyorsítótári művelet felfüggesztése
* [**Ürítés**](#flush-cached-data) – módosított adatot ír a tárolási célokba
* [**Frissítés**](#upgrade-cache-software) – frissíti a gyorsítótár szoftverét
* **Frissítés** – újratölti az Áttekintés oldalt
* [**Delete (Törlés**](#delete-the-cache) ) – véglegesen elpusztítja a gyorsítótárat

Az alábbi lehetőségekről itt olvashat bővebben.

## <a name="stop-the-cache"></a>A gyorsítótár leállítása

A gyorsítótár leállításával csökkentheti a költségeket az inaktív időszakokban. Nem számítunk fel időt a gyorsítótár leállítási idejére, de a gyorsítótár lefoglalt lemezes tárterületét kell fizetnie. (A részletekért tekintse meg a [díjszabási](https://aka.ms/hpc-cache-pricing) oldalt.)

A leállított gyorsítótár nem válaszol az ügyfelek kéréseire. A gyorsítótár leállítása előtt le kell választania az ügyfeleket.

A **Leállítás** gomb felfüggeszti az aktív gyorsítótárat. A **Leállítás** gomb akkor érhető el, ha a gyorsítótár állapota **kifogástalan** vagy **csökkentett teljesítményű**.

![képernyőkép a leállítást lefedő gombokról és egy előugró üzenetről, amely leírja a leállítási műveletet, és azt kérdezi, hogy folytatja? Igen (alapértelmezett) és nincs gomb](media/stop-cache.png)

Miután rákattintott az Igen gombra a gyorsítótár leállításának megerősítéséhez, a gyorsítótár automatikusan kiüríti a tartalmát a tárolási célokhoz. Ez a folyamat hosszabb időt is igénybe vehet, de gondoskodik az adatkonzisztenciaről. Végül a gyorsítótár állapota **Leállítva**értékre változik.

A leállított gyorsítótár újraaktiválásához kattintson a **Start** gombra. Nincs szükség jóváhagyásra.

![képernyőkép a legfontosabb gombokról a Start Kiemelt](media/start-cache.png)

## <a name="flush-cached-data"></a>Gyorsítótárazott adattárolás ürítése

Az áttekintő lap **kiürítés** gombja azt jelzi, hogy a gyorsítótár azonnal írni tudja a gyorsítótárban tárolt összes módosult, a háttérbeli tárolási célokat. A gyorsítótár rendszeres módon menti az adatok tárolási célhelyeit, ezért ezt manuálisan nem kell elvégezni, ha nem szeretné, hogy a háttérrendszer naprakész legyen. Előfordulhat például, hogy a **kiürítést** a tárolási pillanatkép elkészítése vagy az adathalmaz méretének ellenőrzése előtt használja.

> [!NOTE]
> A kiürítési folyamat során a gyorsítótár nem tudja kiszolgálni az ügyfelek kérelmeit. A gyorsítótár-hozzáférés fel van függesztve, és a művelet befejeződése után folytatódik.

![képernyőkép a felső gombokról és a flush kiemeléséről, valamint egy előugró üzenet, amely leírja a kiürítési műveletet, és megkérdezi, hogy szeretné-e folytatni? Igen (alapértelmezett) és nincs gomb](media/hpc-cache-flush.png)

Ha elindítja a gyorsítótár kiürítési műveletét, a gyorsítótár leáll az ügyfelek kéréseinek fogadása után, és a gyorsítótár állapota az Áttekintés oldalon a **Kiürítésre**változik.

A gyorsítótárban tárolt adattárolók a megfelelő tárolási célokba lesznek mentve. Attól függően, hogy mennyi adatra van szükség a kiürítéshez, a folyamat eltarthat néhány percig, vagy akár egy óráig is.

Miután az összes adatmentést a tárolási célokba menti, a gyorsítótár automatikusan elindul az ügyfelek kéréseinek megkezdése után. A gyorsítótár állapota **kifogástalanra**vált.

## <a name="upgrade-cache-software"></a>A cache szoftver frissítése

Ha elérhetővé vált egy új szoftververzió, a **frissítés** gomb aktívvá válik. A szoftver frissítésével kapcsolatos üzenet a lap tetején is megjelenik.

![képernyőfelvétel a gombok felső soráról a frissítés gomb engedélyezésével](media/hpc-cache-upgrade-button.png)

A szoftverfrissítés során az ügyfél-hozzáférés nem szakad meg, a gyorsítótár teljesítménye azonban lelassul. Tervezze meg a szoftver frissítését a használaton kívüli órákban vagy tervezett karbantartási időszakban.

A szoftverfrissítés több órát is igénybe vehet. A magasabb átviteli sebességgel konfigurált gyorsítótárak hosszabb ideig tartanak, mint a kisebb csúcsérték-értékekkel rendelkező gyorsítótárak.

Ha egy szoftverfrissítés elérhető, akkor a rendszer hetente vagy manuálisan alkalmazza azt. A befejezési dátum a frissítési üzenetben jelenik meg. Ha ez idő alatt nem végez frissítést, az Azure automatikusan alkalmazza a frissítést a gyorsítótárba. Az automatikus frissítés ütemezése nem konfigurálható. Ha aggódik a gyorsítótár teljesítményére gyakorolt hatás miatt, a szoftvert saját kezűleg kell frissítenie az időtartam lejárta előtt.

Ha a rendszer leállítja a gyorsítótárat a befejezési dátum után, a gyorsítótár a következő indításakor automatikusan frissíti a szoftvert. (Előfordulhat, hogy a frissítés nem indul el azonnal, de az első órában indul el.)

A szoftverfrissítés megkezdéséhez kattintson a **frissítés** gombra. A gyorsítótár állapota a **frissítésig** változik, amíg a művelet be nem fejeződik.

## <a name="delete-the-cache"></a>A gyorsítótár törlése

A **Törlés** gomb megsemmisíti a gyorsítótárat. Ha töröl egy gyorsítótárat, a rendszer minden erőforrását megsemmisíti, és többé nem számít fel fiókra vonatkozó díjat.

A tárolási célokként használt háttérbeli tárolási kötetek nem érintik a gyorsítótár törlésekor. Később hozzáadhatja őket egy későbbi gyorsítótárhoz, vagy elvégezheti őket külön leszereléssel.

> [!NOTE]
> Az Azure HPC-gyorsítótár nem ír automatikusan módosított adatokból a gyorsítótárból a háttér-tárolási rendszerbe a gyorsítótár törlése előtt.
>
> Annak érdekében, hogy a gyorsítótárban lévő összes adattal a hosszú távú tárolásra legyen írva, [a törlés előtt állítsa le a gyorsítótárat](#stop-the-cache) . Győződjön meg arról, hogy az állapot **leállt** , mielőtt a Törlés gombra kattintana.
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>Gyorsítótár-metrikák és-figyelés

Az Áttekintés oldalon néhány alapszintű gyorsítótár-statisztika – a gyorsítótárazási sebesség, a másodpercenkénti műveletek és a késés – grafikonok láthatók.

![képernyőfelvétel a fent említett statisztikai adatokat bemutató három vonalas gráfról](media/hpc-cache-overview-stats.png)

Ezek a diagramok az Azure beépített monitorozási és elemzési eszközeinek részét képezik. A portál oldalsávjának **figyelés** fejléce alatt további eszközök és riasztások érhetők el. További információt az [Azure monitoring dokumentációjának](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)portál szakasza tartalmaz.

## <a name="next-steps"></a>Következő lépések

<!-- * Learn more about metrics and statistics for hpc cache -->
* További információ az [Azure mérőszámok és statisztikai eszközökről](../azure-monitor/index.yml)
* Segítség kérése [Az Azure HPC cache](hpc-cache-support-ticket.md) -hez
