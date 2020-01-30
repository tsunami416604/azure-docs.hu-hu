---
title: A fürt horizontális skálázásának kezelése (horizontális felskálázás) az Azure Adatkezelő a változó igények kielégítése érdekében
description: Ez a cikk az Azure Adatkezelő-fürt skálázásának és méretezésének lépéseit ismerteti a változó igények alapján.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 8ab192957ead806b4bb3ae8e7395589f3b1ecbbe
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76833294"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>A fürt horizontális skálázásának kezelése (horizontális felskálázás) az Azure Adatkezelő a változó igények kielégítése érdekében

A fürt megfelelő méretezése az Azure-Adatkezelő teljesítményének szempontjából kritikus fontosságú. A statikus fürt mérete a használaton kívüli vagy túlzott kihasználtságot eredményezhet, ami egyik ideális megoldás. Mivel a fürtök iránti igényt nem lehet abszolút pontossággal előre jelezni, jobb megoldás a fürt *méretezése* , a kapacitás és a CPU-erőforrások hozzáadása és eltávolítása a változó igényekkel. 

Az Azure Adatkezelő-fürtök méretezésére két munkafolyamat áll rendelkezésre: 
* Horizontális skálázás, más néven skálázás be-és kifelé.
* [Vertikális skálázás](manage-cluster-vertical-scaling.md), más néven felfelé és lefelé skálázás.
Ez a cikk a horizontális skálázási munkafolyamatot ismerteti.

## <a name="configure-horizontal-scaling"></a>Vízszintes skálázás konfigurálása

A horizontális skálázás használatával az előre meghatározott szabályok és ütemtervek alapján automatikusan méretezheti a példányszámot. A fürthöz tartozó autoskálázási beállítások megadása:

1. A Azure Portal lépjen az Azure Adatkezelő fürterőforrás elemre. A **Beállítások**területen válassza a **felskálázás**lehetőséget. 

2. A **kibővítő** ablakban válassza ki a kívánt automatikus méretezési módszert: **manuális méretezés**, **optimalizált automatikus méretezés**vagy egyéni automatikus **Méretezés**.

### <a name="manual-scale"></a>Manuális méretezés

A fürt létrehozása során az alapértelmezett beállítás a manuális skálázás. A fürt statikus kapacitása nem változik automatikusan. A statikus kapacitást a **Példányszám** sáv használatával választhatja ki. A fürt skálázása ebben a beállításban marad, amíg egy másik módosítást nem végez.

   ![Manuális méretezési módszer](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Optimalizált autoscale (előzetes verzió)

Az optimalizált autoskálázás az ajánlott autoskálázási módszer. Ez a módszer optimalizálja a fürt teljesítményét és költségeit. Ha a fürt a használaton kívüli állapotot közelíti meg, a rendszer a-ben méretezi a-t. Ez a művelet csökkenti a költségeket, de megtartja a teljesítményszint szintjét. Ha a fürt túlzott kihasználtságú állapotot közelít, az optimális teljesítmény érdekében a rendszer kibővíti az adatmennyiséget. Az optimalizált autoskálázás konfigurálása:

1. Válassza az **optimalizált méretezés**lehetőséget. 

1. Válassza ki a példányok minimális száma és a példányok maximális száma értéket. A fürt automatikus méretezési tartománya a két szám között a terhelés alapján.

1. Kattintson a **Mentés** gombra.

   ![Optimalizált autoskálázási módszer](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Az optimalizált autoscale megkezdi a munkát. A műveletei már láthatók a fürt Azure-tevékenység naplójában.

#### <a name="logic-of-optimized-autoscale"></a>Optimalizált autoskálázás logikája 

**Horizontális felskálázás**

Ha a fürt túlzott kihasználtságú állapotot közelít, az optimális teljesítmény érdekében felskálázást biztosít. A vertikális felskálázás a következő esetekben fog történni:
* A fürtözött példányok száma nem éri el a felhasználó által definiált példányok maximális számát.
* A gyorsítótár kihasználtsága több mint egy óra alatt magas.
* A CPU több mint egy óráig magas.

> [!NOTE]
> A Felskálázási logika jelenleg nem veszi figyelembe a betöltés kihasználtságának mérőszámát. Ha ez a metrika a használati eset szempontjából fontos, használja az [Egyéni autoskálázást](#custom-autoscale).

**Skálázás**

Ha a fürt a használaton kívüli állapotot közelíti meg, az alacsonyabb költségekre, de a teljesítmény fenntartására is kiterjed. A rendszer több mérőszámot használ annak ellenőrzéséhez, hogy biztonságos-e a fürt méretezése. A következő szabályok naponta kiértékelésre kerülnek a méretezés előtt 7 napig:
* A példányok száma meghaladja a 2 értéket, és meghaladja a definiált példányok minimális számát.
* Annak biztosítása érdekében, hogy az erőforrások ne legyenek túlterhelve, a következő metrikákat ellenőrizni kell a skálázás végrehajtása előtt: 
    * A gyorsítótár kihasználtsága nem magas
    * A CPU átlag alatt van 
    * A betöltés kihasználtsága átlag alatt van 
    * A streaming betöltési kihasználtsága (ha a folyamatos átvitel használatban van) nem magas
    * Az életben lévő események megtartása egy meghatározott minimális, megfelelően feldolgozott és időben történik.
    * Nincs lekérdezés-szabályozás 
    * A sikertelen lekérdezések száma nem éri el a megadott minimális értéket.

> [!NOTE]
> A méretezés a logikában jelenleg 7 napos kiértékelést igényel az optimalizált skálázás megvalósítása előtt. A kiértékelés 24 óránként történik. Ha gyors módosításra van szükség, használja a [manuális skálázást](#manual-scale).

### <a name="custom-autoscale"></a>Egyéni méretezés

Az egyéni autoscale használatával dinamikusan méretezheti a fürtöt a megadott mérőszámok alapján. Az alábbi ábrán a folyamat és az egyéni autoskálázás konfigurálásának lépései láthatók. További részletekért kövesse a grafikát.

1. Az **autoskálázási beállítás neve** mezőbe írjon be egy nevet, például *: kibővíthető: gyorsítótár kihasználtsága*. 

   ![Skálázási szabály](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. A **méretezési mód**beállításnál válassza a **skála mérőszám alapján**lehetőséget. Ez a mód dinamikus skálázást biztosít. Kiválaszthatja **a méretezés adott példányszámot**is.

3. Válassza **a + szabály hozzáadása**lehetőséget.

4. A jobb oldali **skálázási szabály** szakaszban adja meg az egyes beállítások értékeit.

    **Feltételek**

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Idő összesítése** | Válasszon ki egy összesítési feltételt, például **átlagot**. |
    | **Metrika neve** | Válassza ki azt a mérőszámot, amelynek alapján a skálázási művelet alapulni fog, például a **gyorsítótár kihasználtsága**. |
    | **Időbeli gabona statisztikája** | Az **átlag**, a **minimum**, a **maximum**és az **összeg**közül választhat. |
    | **Üzemeltető** | Válassza ki a megfelelő beállítást, például **nagyobb vagy egyenlő**. |
    | **Küszöb** | Válasszon ki egy megfelelő értéket. A gyorsítótár kihasználtsága például a 80 százalék jó kiindulási pont. |
    | **Időtartam (perc)** | Válasszon ki egy megfelelő időtartamot a rendszer számára a metrikák kiszámításakor. Kezdje az alapértelmezett 10 percet. |
    |  |  |

    **Művelet**

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Művelet** | Válassza ki a megfelelő lehetőséget a méretezéshez vagy a vertikális felskálázáshoz. |
    | **Példányok száma** | Válassza ki a felvenni vagy eltávolítani kívánt csomópontok vagy példányok számát egy metrikai feltétel teljesülése esetén. |
    | **Lehűlni (perc)** | Válasszon ki egy megfelelő időintervallumot a skálázási műveletek közötti várakozáshoz. Kezdje az alapértelmezett öt percet. |
    |  |  |

5. Válassza a **Hozzáadás** lehetőséget.

6. A bal oldali **példány korlátai** szakaszban adja meg az egyes beállítások értékeit.

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Minimális** | Azon példányok száma, amelyeken a fürt nem méretezhető, a kihasználtságtól függetlenül. |
    | **Maximális** | Azon példányok száma, amelyeken a fürt nem méretezhető, a kihasználtságtól függetlenül. |
    | **Alapértelmezett** | A példányok alapértelmezett száma. Ez a beállítás akkor használatos, ha problémák merülnek fel az erőforrás-metrikák olvasásakor. |
    |  |  |

7. Kattintson a **Mentés** gombra.

Ezzel konfigurálta az Azure Adatkezelő-fürt horizontális skálázását. Adjon hozzá egy másik szabályt a vertikális skálázáshoz. Ha segítségre van szüksége a fürtök skálázásával kapcsolatos problémák megoldásához, [Nyisson meg egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) a Azure Portal.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Adatkezelő teljesítményének, állapotának és használatának monitorozása metrikákkal](using-metrics.md)
* Fürt [vertikális skálázásának kezelése](manage-cluster-vertical-scaling.md) a fürt megfelelő méretezéséhez.
