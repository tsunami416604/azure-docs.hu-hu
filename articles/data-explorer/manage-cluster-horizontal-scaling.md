---
title: Horizontális felskálázás az Azure Data Explorer fürt
description: Ez a cikk ismerteti a lépéseket horizontális felskálázása és skálázhatja az adatkezelőt az Azure-fürtben alapú igény szerinti módosítására.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 29bfcc42462a667850f0b2e1bbda3d29cd1597ab
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571516"
---
# <a name="manage-cluster-horizontal-scaling-to-accommodate-changing-demand"></a>Fürt horizontális skálázással változó igényeket elégíthet kezelése

Fürt méretezése megfelelően, kritikus fontosságú a teljesítmény az Azure Data Explorer. De igény szerint egy fürtön nem becsülhető abszolút pontossággal. Egy statikus fürtméret vezethet alulkihasználtságának vagy overutilization, ezek egyike sem nem ideális.

Az, hogy esetleg jobb megközelítés *méretezési* egy fürthöz, hozzáadása és eltávolítása a kapacitás igény szerinti módosítását. Nincsenek két munkafolyamatok skálázásához: 
* Horizontális skálázás, más néven horizontális fel- és a.
* Függőleges méretezés, más néven felfelé és lefelé skálázást.

Ez a cikk azt ismerteti, hogy a horizontális skálázási munkafolyamat.

Horizontális skálázás lehetővé teszi a példányok száma automatikusan alapján előre meghatározott szabályok és ütemezések méretezése. Adja meg a fürt számára az automatikus skálázási beállítások az Azure Portalon, ebben a cikkben leírtak szerint.

## <a name="steps-to-configure-horizontal-scaling"></a>Horizontális skálázás konfigurálásának lépései

Az Azure Portalon lépjen az adatkezelő fürt-erőforrásra. Alatt a **beállítások** szakaszban kattintson **horizontális felskálázása**. 

Válassza ki a kívánt Automatikus méretezéssel módszert: **Manuális skálázás**, **automatikus skálázási optimalizált** vagy **egyéni automatikus skálázási**.

### <a name="manual-scale"></a>Manuális skálázás

Manuális skálázás a fürt létrehozása az alapértelmezett beállítása. Ez azt jelenti, hogy a fürt rendelkezik egy statikus fürtkapacitás nem fog automatikusan változni. A statikus kapacitás a Keresősáv használatával kiválaszthatja, és nem változik mindaddig, amíg a következő alkalommal fog módosítja a fürt horizontális felskálázási beállítás.

   ![Manuális skálázás metódus](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>Az automatikus méretezés optimalizált

Optimalizált automatikus méretezés a az automatikus méretezés ajánlott módszer. Az automatikus méretezés optimalizált konfigurálásának lépései:

1. Optimalizált automatikus skálázási beállítást, és válassza ki egy alacsonyabb korlátot és a egy felső korlátot, a fürt példányok mennyiségét, az automatikus skálázás ezek korlátai között történik.
2. Kattintson a Save (Mentés) gombra.

   ![Az automatikus méretezés optimalizált metódus](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Munkahelyi optimalizált automatikus skálázási mechanizmus mentése elindul, és ez után műveletek a tevékenységnaplóban a fürt látható lesz. Ez a módszer az automatikus méretezés optimalizálja a fürt teljesítmény és költségek: Ha a fürt első azt fogja kell van ellátva a alulkihasználtságának, amely teljesítmény az azonos és alacsonyabb költségeket hagyja állapotba elindul, és a fürt állapotának bekéréséhez való indul overutilization, horizontálisan felskálázott, hogy jól teljesít lesz

### <a name="custom-autoscale"></a>Egyéni automatikus skálázási

Az automatikus méretezés egyéni módszer lehetővé teszi az Ön által megadott metrikák alapján dinamikusan fürt méretezése. A következő ábrán látható, a folyamat és az egyéni automatikus skálázási konfigurálásához szükséges lépésekről. További részletekért kövesse a képet.

1. Az a **automatikus skálázási beállítás neve** mezőben adjon meg egy nevet, például: *horizontális felskálázás: kihasználtsági gyorsítótár*. 

   ![Skálázási szabályhoz](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. A **skálázási mód**válassza **skálázás metrika alapján**. Ebben a módban a dinamikus méretezést biztosít. Lehetőség kiválasztásával **skálázás adott példányszámra**.

3. Válassza ki **és a egy szabály hozzáadásához**.

4. Az a **skálázási szabályhoz** a jobb oldali területén adja meg az értékeket a beállításokhoz.

    **Feltételek**

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Idő összesítése** | Válassza ki például egy összesítő feltételek **átlagos**. |
    | **Metrika neve** | Válassza ki a kívánt kell alapulnia, például a skálázási művelet **gyorsítótár-kihasználtság**. |
    | **Időfelbontási szint statisztikája** | Választhat **átlagos**, **minimális**, **maximális**, és **Sum**. |
    | **Operátor** | Válassza ki a megfelelő beállítást, például **nagyobb vagy egyenlő**. |
    | **Küszöbérték** | Válasszon egy megfelelő értéket. Gyorsítótár-kihasználtság, például a 80 %-a egy jó kiindulási pont. |
    | **Időtartam (perc)** | Válassza ki a megfelelő összeget, és tekintse meg a vissza mérőszámok kiszámításakor a rendszer. Indítsa el az alapértelmezett 10 perc. |
    |  |  |

    **Művelet**

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Művelet** | Válassza ki a megfelelő beállítást a vertikális vagy horizontális felskálázás. |
    | **Példányok száma** | Válassza ki a csomópontok vagy hozzáadása vagy eltávolítása metrikai feltétel teljesülésekor kívánt példányok számát. |
    | **Lehűlés (percben)** | Válasszon egy megfelelő várakozási időköz a skálázási műveletek között. Indítsa el az alapértelmezett öt perc alatt. |
    |  |  |

5. Válassza a **Hozzáadás** lehetőséget.

6. Az a **Példányszámkorlátoknál** szakaszban a bal oldalon található, adja meg az értékeket a beállításokhoz.

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Minimum** | A fürt nem méretezhető alább függetlenül kihasználtsági példányok száma. |
    | **Maximum** | A fürt nem méretezhető felett, függetlenül a kihasználtság példányok száma. |
    | **Alapértelmezett** | Példányok alapértelmezett száma. Ezt a beállítást használja, ha problémák adódnak az erőforrás-metrikák olvasása. |
    |  |  |

7. Kattintson a **Mentés** gombra.

Most már konfigurálta az Azure Data Explorer fürt egy horizontális felskálázási művelet. Adjon meg egy másik szabályt a horizontális leskálázási művelet. Ha a fürt méretezése problémák segítségre van szüksége [nyisson egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon.

## <a name="next-steps"></a>További lépések

* [Azure Data Explorer Teljesítményfigyelő, egészségügyi és használati metrikákkal](using-metrics.md)
* [Fürt vertikális skálázás kezelése](manage-cluster-vertical-scaling.md) a fürt megfelelő méretezéshez.
