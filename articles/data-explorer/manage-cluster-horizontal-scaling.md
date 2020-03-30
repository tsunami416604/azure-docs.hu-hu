---
title: Fürt vízszintes skálázásának kezelése (horizontális felskálázás) az Azure Data Explorer igényének megfelelően
description: Ez a cikk az Azure Data Explorer-fürtben az igények módosítása alapján történő horizontális felskálázási és méretezési lépéseket ismerteti.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: ff7420619cffc2287ab8ff6332df605d56329549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664133"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Fürt vízszintes skálázásának kezelése (horizontális felskálázás) az Azure Data Explorerben a változó igényeknek megfelelően

A fürt megfelelő méretezése kritikus fontosságú az Azure Data Explorer teljesítménye szempontjából. A statikus fürtméret kihasználatlan vagy túlkihasználtsághoz vezethet, amelyek közül egyik sem ideális. Mivel a fürt igénye nem előre jelezhető abszolút pontossággal, jobb, ha *egy fürtméretet skáláz,* kapacitást és CPU-erőforrásokat a változó igényekkel. 

Az Azure Data Explorer-fürt méretezéséhez két munkafolyamat létezik: 
* Horizontális skálázás, más néven méretezés be- és kiméretezés.
* [Vertikális skálázás](manage-cluster-vertical-scaling.md), más néven fel- és leskálázás.
Ez a cikk ismerteti a horizontális méretezési munkafolyamatot.

## <a name="configure-horizontal-scaling"></a>Vízszintes méretezés konfigurálása

Horizontális skálázás használatával a példányok száma automatikusan méretezhető, előre definiált szabályok és ütemezések alapján. A fürt automatikus skálázási beállításainak megadása:

1. Az Azure Portalon nyissa meg az Azure Data Explorer fürterőforrás. A **Beállítások csoportban**válassza a **Kiskálázás**lehetőséget. 

2. A **Kirendelés** ablakban válassza ki a kívánt automatikus **skálázási**módszert: **Kézi méretezés,** **Optimalizált automatikus skálázás**vagy Egyéni automatikus skálázás .

### <a name="manual-scale"></a>Kézi skála

A manuális méretezés az alapértelmezett beállítás a fürt létrehozásasorán. A fürt statikus kapacitása nem változik automatikusan. A statikus kapacitást a **Példányszám-sáv** használatával választhatja ki. A fürt skálázása ezen a beállításon marad, amíg egy másik módosítást nem ad.

   ![Kézi méretezési módszer](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Optimalizált automatikus skálázás (előzetes verzió)

Az optimalizált automatikus skálázás az ajánlott automatikus skálázási módszer. Ez a módszer optimalizálja a fürt teljesítményét és költségeit. Ha a fürt megközelíti az alulkihasználtság állapotát, a lesz méretezve. Ez a művelet csökkenti a költségeket, de megtartja a teljesítményszintet. Ha a fürt túlhasználtsági állapothoz közelít, az optimális teljesítmény fenntartása érdekében a rendszer kilesz méretezve. Az optimalizált automatikus skálázás konfigurálása:

1. Válassza **az Optimalizált automatikus skálázás**lehetőséget. 

1. Válassza ki a példányok minimális számát és a maximális példányszámot. A fürt automatikus skálázása a terhelés alapján két szám között mozog.

1. Kattintson a **Mentés** gombra.

   ![Optimalizált automatikus skálázási módszer](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Az optimalizált automatikus skálázás megkezdi működését. A műveletek most már látható a fürt Azure-tevékenységnaplójában.

#### <a name="logic-of-optimized-autoscale"></a>Az optimalizált automatikus skálázás logikája 

**Horizontális felskálázás**

Amikor a fürt megközelíti a túlhasználtság állapotát, horizontális annektálódik az optimális teljesítmény fenntartása érdekében. Horizontális felskálázás akkor történik, ha:
* A fürtpéldányok száma nem éri el a felhasználó által meghatározott példányok maximális számát.
* A gyorsítótár kihasználtsága több mint egy órán keresztül magas.
* A CPU magas több mint egy óra.
* A lenyelés kihasználtsága több mint egy órán át magas.

> [!NOTE]
> A horizontális felskálázási logika jelenleg nem veszi figyelembe a betöltési kihasználtsági metrika. Ha ez a mérőszám fontos a használati esethez, használjon [egyéni automatikus skálázást.](#custom-autoscale)

**Horizontális leskálázás**

Amikor a fürt megközelíti az alulkihasználtság állapotát, skálázhatja az alacsonyabb költségek, de a teljesítmény fenntartása érdekében. Több metrikák segítségével ellenőrizze, hogy biztonságos-e a fürtben skálázható. A következő szabályok at óránként értékeli kitették 6 órán keresztül, mielőtt a skálázás történik:
* A példányok száma meghaladja a 2 és meghaladja a minimális példányok száma meghatározott.
* Annak érdekében, hogy ne legyen túlterhelés az erőforrásokban, a következő mutatókat kell ellenőrizni a méretezés végrehajtása előtt: 
    * A gyorsítótár kihasználtsága nem magas
    * A CPU átlag alatti 
    * A lenyelés kihasználtsága az átlag alatt van 
    * A streamelési kihasználtság (ha streamelési betöltést használ) nem magas
    * Életben tartani események felett meghatározott minimális, megfelelően feldolgozott, és időben.
    * Nincs lekérdezésszabályozás 
    * A sikertelen lekérdezések száma a megadott minimum alatt van.

> [!NOTE]
> A piszkapi-skála jelenleg egy 7 napos értékelést igényel az optimalizált skálázás megvalósítása előtt. Ez az értékelés 24 óránként egyszer történik. Ha gyors módosításra van szükség, használja a [kézi skálázást](#manual-scale).

### <a name="custom-autoscale"></a>Egyéni automatikus skálázás

Egyéni automatikus skálázás használatával dinamikusan skálázhatja a fürtet a megadott metrikák alapján. A következő ábra bemutatja az egyéni automatikus skálázás folyamatát és az egyéni méretezés konfigurálásának lépéseit. További részletek követik a grafikát.

1. Az **Automatikus skálázási beállítás neve** mezőbe írjon be egy nevet, például *kibővített: gyorsítótár-kihasználtság.* 

   ![Szabály méretezése](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. A **Méretezés módban**válassza **a Méretezés egy metrika alapján**lehetőséget. Ez a mód dinamikus méretezést biztosít. A Méretezés **adott példányszámra**lehetőséget is választhatja.

3. Válassza **a + Szabály hozzáadása**lehetőséget.

4. A jobb oldali **Méretezés szabály** szakaszban adja meg az egyes beállítások értékeit.

    **Feltételek**

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Idő összesítése** | Válasszon egy összesítési feltételt, például **átlagot.** |
    | **Metrika neve** | Válassza ki azt a metrikát, amelyen a méretezési művelet alapul, például **a gyorsítótár kihasználtságát.** |
    | **Időfelbontási szint statisztikája** | Válasszon **az Átlag**, a **Minimum**, **a Maximum**és az **Összeg**között. |
    | **Művelet** | Válassza ki a megfelelő beállítást, például **nagyobb vagy egyenlő .** |
    | **Küszöb** | Válassza ki a megfelelő értéket. Például a gyorsítótár kihasználtsága, 80 százalék egy jó kiindulási pont. |
    | **Időtartam (perc)** | Válassza ki a megfelelő időt a rendszer, hogy nézzen vissza, amikor a metrikák kiszámításakor. Kezdje az alapértelmezett 10 perc. |
    |  |  |

    **Művelet**

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Művelet** | Válassza ki a megfelelő beállítást a méretezéshez vagy a horizontális felskálázáshoz. |
    | **Példányok száma** | Válassza ki a metrikafeltétel teljesülése esetén hozzáadni vagy eltávolítani kívánt csomópontok vagy példányok számát. |
    | **Lehűlés (perc)** | Válassza ki a méretezési műveletek közötti várakozáshoz szükséges megfelelő időintervallumot. Kezdje az alapértelmezett öt perc. |
    |  |  |

5. Válassza a **Hozzáadás** lehetőséget.

6. A bal oldali **Példánykorlátok** szakaszban adja meg az egyes beállítások értékeit.

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Minimális** | Azon példányok száma, amelyeket a fürt a kihasználtságtól függetlenül nem méretez. |
    | **Maximum** | Azon példányok száma, amelyek felett a fürt nem skálázható, függetlenül a kihasználtságtól. |
    | **Alapértelmezett** | A példányok alapértelmezett száma. Ez a beállítás akkor használatos, ha problémák merülnek fel az erőforrás-metrikák olvasásával. |
    |  |  |

7. Kattintson a **Mentés** gombra.

Most már konfigurálta a horizontális skálázást az Azure Data Explorer-fürthöz. Adjon hozzá egy másik szabályt a függőleges méretezéshez. Ha segítségre van szüksége a fürtskálázási problémák, [nyisson meg egy támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon.

## <a name="next-steps"></a>További lépések

* [Az Azure Data Explorer teljesítményének, állapotának és használatának figyelése mérőszámokkal](using-metrics.md)
* [Fürt függőleges méretezésének kezelése](manage-cluster-vertical-scaling.md) a fürt megfelelő méretezése esetén.
