---
title: Az Azure HPC cache kezelése és frissítése
description: Az Azure HPC cache kezelése és frissítése a Azure Portal használatával
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: a166a904b2e63419efd5803fd54be1d1b59836fb
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867078"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>A gyorsítótár kezelése a Azure Portal

A gyorsítótár Áttekintés lapja a Azure Portal megjeleníti a projekt részleteit, a gyorsítótár állapotát és a gyorsítótár alapszintű statisztikáit. Emellett a gyorsítótár törlésére, az adatürítések hosszú távú tárolásra vagy a szoftverek frissítésére vonatkozó vezérlőket is tartalmaz.

Az Áttekintés lap megnyitásához válassza ki a gyorsítótár-erőforrást a Azure Portal. Töltse be például a **minden erőforrás** lapot, és kattintson a gyorsítótár nevére.

![Az Azure HPC cache-példány áttekintési oldalának képernyőképe](media/hpc-cache-overview.png)

A lap tetején található gombok segítenek a gyorsítótár kezelésében:

* [**Ürítés**](#flush-cached-data) – módosított adatot ír a tárolási célokba
* [**Frissítés**](#upgrade-cache-software) – frissíti a gyorsítótár szoftverét
* **Frissítés** – újratölti az Áttekintés oldalt
* [**Delete (Törlés**](#delete-the-cache) ) – véglegesen elpusztítja a gyorsítótárat

Az alábbi lehetőségekről itt olvashat bővebben.

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

A szoftverfrissítés megkezdéséhez kattintson a **frissítés** gombra. A gyorsítótár állapota a **frissítésig** változik, amíg a művelet be nem fejeződik.

## <a name="delete-the-cache"></a>A gyorsítótár törlése

A **Törlés** gomb megsemmisíti a gyorsítótárat. Ha töröl egy gyorsítótárat, a rendszer minden erőforrását megsemmisíti, és többé nem számít fel fiókra vonatkozó díjat.

A tárolási célokként használt háttérbeli tárolási kötetek nem érintik a gyorsítótár törlésekor. Később hozzáadhatja őket egy későbbi gyorsítótárhoz, vagy elvégezheti őket külön leszereléssel.

> [!NOTE]
> Az Azure HPC-gyorsítótár nem ír automatikusan módosított adatokból a gyorsítótárból a háttér-tárolási rendszerbe a gyorsítótár törlése előtt.
>
> Az alábbi eljárást követve győződjön meg arról, hogy a gyorsítótárban lévő összes adattal a hosszú távú tárolásra lett írva:
>
> 1. [Távolítsa el](hpc-cache-edit-storage.md#remove-a-storage-target) az egyes tárolási célokat az Azure HPC-gyorsítótárból a tárolási célok lapon található törlés gombbal. A rendszer automatikusan a gyorsítótárból a háttér-tárolási rendszerbe írja a módosult adatot a cél eltávolítása előtt.
> 1. Várjon, amíg a tárolási cél teljesen el lesz távolítva. A folyamat akár egy órát is igénybe vehet, ha sok adattal kell írni a gyorsítótárból. Ha elkészült, a portál értesítése szerint a törlési művelet sikeres volt, és a tárolási cél eltűnik a listából.
> 1. Miután az összes érintett tárolási cél törölve lett, biztonságosan törölheti a gyorsítótárat.
>
> Azt is megteheti, hogy a [kiürítési](#flush-cached-data) lehetőséggel menti a gyorsítótárazott adatot, de az elveszítés kis kockázattal jár, ha az ügyfél a kiürítés befejeződése után módosítja a gyorsítótárat, de megsemmisíti a gyorsítótár-példányt.

## <a name="cache-metrics-and-monitoring"></a>Gyorsítótár-metrikák és-figyelés

Az Áttekintés oldalon néhány alapszintű gyorsítótár-statisztika – a gyorsítótárazási sebesség, a másodpercenkénti műveletek és a késés – grafikonok láthatók.

![képernyőfelvétel a fent említett statisztikai adatokat bemutató három vonalas gráfról](media/hpc-cache-overview-stats.png)

Ezek a diagramok az Azure beépített monitorozási és elemzési eszközeinek részét képezik. A portál oldalsávjának **figyelés** fejléce alatt további eszközök és riasztások érhetők el. További információt az [Azure monitoring dokumentációjának](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)portál szakasza tartalmaz.

## <a name="next-steps"></a>Következő lépések

<!-- * Learn more about metrics and statistics for hpc cache -->
* További információ az [Azure mérőszámok és statisztikai eszközökről](../azure-monitor/index.yml)
* Segítség kérése [Az Azure HPC cache](hpc-cache-support-ticket.md) -hez
