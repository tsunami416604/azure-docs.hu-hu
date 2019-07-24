---
title: A fürt horizontális skálázásának kezelése (horizontális felskálázás) az Azure Adatkezelő a változó igények kielégítése érdekében
description: Ez a cikk az Azure Adatkezelő-fürt skálázásának és méretezésének lépéseit ismerteti a változó igények alapján.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 70e6bdfcf9718244632ad02e09d3ddadee71a617
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311571"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>A fürt horizontális skálázásának kezelése (horizontális felskálázás) az Azure Adatkezelő a változó igények kielégítése érdekében

A fürt megfelelő méretezése az Azure-Adatkezelő teljesítményének szempontjából kritikus fontosságú. A statikus fürt mérete a használaton kívüli vagy túlzott kihasználtságot eredményezhet, ami egyik ideális megoldás.

Mivel a fürtök iránti igényt nem lehet abszolút pontossággal előre jelezni, jobb megoldás  a fürt méretezése, a kapacitás és a CPU-erőforrások hozzáadása és eltávolítása a változó igényekkel. 

Az Azure Adatkezelő-fürtök méretezésére két munkafolyamat áll rendelkezésre: 

* Horizontális skálázás, más néven skálázás be-és kifelé.
* [Vertikális skálázás](manage-cluster-vertical-scaling.md), más néven felfelé és lefelé skálázás.

Ez a cikk a horizontális skálázási munkafolyamatot ismerteti.

## <a name="configure-horizontal-scaling"></a>Vízszintes skálázás konfigurálása

A horizontális skálázás használatával az előre meghatározott szabályok és ütemtervek alapján automatikusan méretezheti a példányszámot. A fürthöz tartozó autoskálázási beállítások megadása:

1. A Azure Portal lépjen az Azure Adatkezelő fürterőforrás elemre. A **Beállítások**területen válassza a felskálázás lehetőséget. 

2. A kibővítő ablakban válassza ki a kívánt autoskálázási módszert:  **Manuális méretezés**, **optimalizált automatikus méretezés**vagy **Egyéni automatikus méretezés**.

### <a name="manual-scale"></a>Manuális méretezés

A fürt létrehozása során az alapértelmezett beállítás a manuális skálázás. A fürt statikus kapacitása nem változik automatikusan. A statikus kapacitást a **Példányszám** sáv használatával választhatja ki. A fürt skálázása ebben a beállításban marad, amíg egy másik módosítást nem végez.

   ![Manuális méretezési módszer](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>Optimalizált autoskálázás

Az optimalizált autoskálázás az ajánlott autoskálázási módszer. Ez a módszer optimalizálja a fürt teljesítményét és költségeit. Ha a fürt a használaton kívüli állapotot közelíti meg, a rendszer a-ben méretezi a-t. Ez a művelet csökkenti a költségeket, de megtartja a teljesítményszint szintjét. Ha a fürt túlzott kihasználtságú állapotot közelít, az optimális teljesítmény érdekében a rendszer kibővíti az adatmennyiséget. Az optimalizált autoskálázás konfigurálása:

1. Válassza az **optimalizált méretezés**lehetőséget. 

1. Válassza ki a példányok minimális száma és a példányok maximális száma értéket. A fürt automatikus skálázási tartománya a két szám között a terhelés alapján.

1. Kattintson a **Mentés** gombra.

   ![Optimalizált autoskálázási módszer](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Az optimalizált autoscale megkezdi a munkát. A műveletei már láthatók a fürt Azure-tevékenység naplójában.

### <a name="custom-autoscale"></a>Egyéni méretezés

Az egyéni autoscale használatával dinamikusan méretezheti a fürtöt a megadott mérőszámok alapján. Az alábbi ábrán a folyamat és az egyéni autoskálázás konfigurálásának lépései láthatók. További részletekért kövesse a grafikát.

1. Az autoskálázási **beállítás neve** mezőbe írjon be egy nevet, például *: kibővíthető: gyorsítótár kihasználtsága*. 

   ![Skálázási szabály](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. A **méretezési mód**beállításnál válassza a **skála mérőszám alapján**lehetőséget. Ez a mód dinamikus skálázást biztosít. Kiválaszthatja **a méretezés adott példányszámot**is.

3. Válassza **a + szabály hozzáadása**lehetőséget.

4. A jobb oldali **skálázási szabály** szakaszban adja meg az egyes beállítások értékeit.

    **Feltételek**

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Idő összesítése** | Válasszon ki egy összesítési feltételt , például átlagot. |
    | **Metrika neve** | Válassza ki azt a mérőszámot, amelynek alapján a skálázási művelet alapulni fog, például a **gyorsítótár kihasználtsága**. |
    | **Időbeli gabona statisztikája** | Az **átlag**, a **minimum**, a **maximum**és az **összeg**közül választhat. |
    | **Operátor** | Válassza ki a megfelelő beállítást, például **nagyobb vagy egyenlő**. |
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
    | **Minimum** | Azon példányok száma, amelyeken a fürt nem méretezhető, a kihasználtságtól függetlenül. |
    | **Maximális** | Azon példányok száma, amelyeken a fürt nem méretezhető, a kihasználtságtól függetlenül. |
    | **Alapértelmezett** | A példányok alapértelmezett száma. Ez a beállítás akkor használatos, ha problémák merülnek fel az erőforrás-metrikák olvasásakor. |
    |  |  |

7. Kattintson a **Mentés** gombra.

Ezzel konfigurálta az Azure Adatkezelő-fürt horizontális skálázását. Adjon hozzá egy másik szabályt a vertikális skálázáshoz. Ha segítségre van szüksége a fürtök skálázásával kapcsolatos problémák megoldásához, [Nyisson meg egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) a Azure Portal.

## <a name="next-steps"></a>További lépések

* [Az Azure Adatkezelő teljesítményének, állapotának és használatának monitorozása metrikákkal](using-metrics.md)

* Fürt [vertikális skálázásának kezelése](manage-cluster-vertical-scaling.md) a fürt megfelelő méretezéséhez.
