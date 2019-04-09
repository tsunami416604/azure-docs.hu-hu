---
title: Horizontális felskálázás az Azure Data Explorer fürt
description: Ez a cikk ismerteti a lépéseket horizontális felskálázása és skálázhatja az adatkezelőt az Azure-fürtben alapú igény szerinti módosítására.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 24bbddd28943adc929fbaea456eeae8165db290c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261598"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Fürt horizontális felskálázás változó igényeket elégíthet kezelése

Fürt méretezése megfelelően, kritikus fontosságú a teljesítmény az Azure Data Explorer. De igény szerint egy fürtön nem becsülhető abszolút pontossággal. Egy statikus fürtméret vezethet alulkihasználtságának vagy overutilization, ezek egyike sem nem ideális.

Az, hogy esetleg jobb megközelítés *méretezési* egy fürthöz, hozzáadása és eltávolítása a kapacitás igény szerinti módosítását. Két munkafolyamatok skálázásához: vertikális és horizontális felskálázást. Ez a cikk ismerteti a kibővített munkafolyamat.

Ez a cikk bemutatja, hogyan kezelheti a fürt kibővített, más néven az automatikus méretezés. Az automatikus skálázás lehetővé teszi a horizontális felskálázás a példányok száma automatikusan alapján előre meghatározott szabályok és ütemezések. Adja meg a fürt számára az automatikus skálázási beállítások az Azure Portalon, ebben a cikkben leírtak szerint.

## <a name="steps-to-configure-autoscale"></a>Automatikus skálázási konfigurálásának lépései

Az Azure Portalon lépjen az adatkezelő fürt-erőforrásra. Alatt a **beállítások** szakaszban kattintson **horizontális felskálázása**. Az a **konfigurálása** lapon jelölje be **automatikus skálázás engedélyezése**.

   ![Automatikus skálázás engedélyezése](media/manage-cluster-scaling/enable-autoscale.png)

A következő ábrán látható a folyamat a következő néhány lépést. További részletekért kövesse a képet.

1. Az a **automatikus skálázási beállítás neve** mezőben adjon meg egy nevet, például: *horizontális felskálázás: kihasználtsági gyorsítótár*. 

   ![Szabály skálázása](media/manage-cluster-scaling/scale-rule.png)

2. A **skálázási mód**válassza **skálázás metrika alapján**. Ebben a módban a dinamikus méretezést biztosít. Lehetőség kiválasztásával **skálázás adott példányszámra**.

3. Válassza ki **és a egy szabály hozzáadásához**.

4. Az a **skálázási szabályhoz** a jobb oldali területén adja meg az értékeket a beállításokhoz.

    **Feltételek**

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Idő összesítése** | Válassza ki például egy összesítő feltételek **átlagos**. |
    | **Metrika neve** | Válassza ki a kívánt kell alapulnia, például a skálázási művelet **gyorsítótár-kihasználtság**. |
    | **Időfelbontási szint statisztikája** | Választhat **átlagos**, **minimális**, **maximális**, és **Sum**. |
    | **Művelet** | Válassza ki a megfelelő beállítást, például **nagyobb vagy egyenlő**. |
    | **Küszöbérték** | Válasszon egy megfelelő értéket. Gyorsítótár-kihasználtság, például a 80 %-a egy jó kiindulási pont. |
    | **Időtartam (perc)** | Válassza ki a megfelelő összeget, és tekintse meg a vissza mérőszámok kiszámításakor a rendszer. Indítsa el az alapértelmezett 10 perc. |
    |  |  |

    **Műveletek**

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Művelet** | Válassza ki a megfelelő beállítást a vertikális vagy horizontális felskálázás. |
    | **Példányszám** | Válassza ki a csomópontok vagy hozzáadása vagy eltávolítása metrikai feltétel teljesülésekor kívánt példányok számát. |
    | **Lehűlés (percben)** | Válasszon egy megfelelő várakozási időköz a skálázási műveletek között. Indítsa el az alapértelmezett öt perc alatt. |
    |  |  |

5. Válassza a **Hozzáadás** lehetőséget.

6. Az a **Példányszámkorlátoknál** szakaszban a bal oldalon található, adja meg az értékeket a beállításokhoz.

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Minimális** | A fürt nem méretezhető alább függetlenül kihasználtsági példányok száma. |
    | **Maximum** | A fürt nem méretezhető felett, függetlenül a kihasználtság példányok száma. |
    | **Alapértelmezett** | Példányok alapértelmezett száma. Ezt a beállítást használja, ha problémák adódnak az erőforrás-metrikák olvasása. |
    |  |  |

7. Kattintson a **Mentés** gombra.

Most már konfigurálta az Azure Data Explorer fürt egy horizontális felskálázási művelet. Adjon meg egy másik szabályt a horizontális leskálázási művelet. Ez a konfiguráció lehetővé teszi, hogy a fürt méretezése dinamikusan megadott metrikák alapján.

Emellett [kezelheti a fürt vertikális felskálázása](manage-cluster-scale-up.md) a fürt megfelelő méretezéshez.

Ha a fürt méretezése problémák segítségre van szüksége [nyisson egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon.

## <a name="next-steps"></a>További lépések

[Azure Data Explorer Teljesítményfigyelő, egészségügyi és használati metrikákkal](using-metrics.md)
