---
title: Változó igényeket elégíthet méretezési Azure adatkezelő fürt
description: Ez a cikk ismerteti a lépéseket horizontális felskálázása és skálázhatja az adatkezelőt az Azure-fürtben alapú igény szerinti módosítására.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 8d369800bdc9817856eeb7039fe47e5b870735ba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107372"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Fürt horizontális felskálázás változó igényeket elégíthet kezelése

Fürt méretezése megfelelően, kritikus fontosságú a teljesítmény az Azure Data Explorer. De igény szerint egy fürtön nem becsülhető abszolút pontossággal. Egy statikus fürtméret vezethet alulkihasználtságának vagy overutilization, ezek egyike sem nem ideális.

Az, hogy esetleg jobb megközelítés *méretezési* egy fürthöz, hozzáadása és eltávolítása a kapacitás igény szerinti módosítását. Két munkafolyamatok skálázásához: vertikális és horizontális felskálázást. Ez a cikk ismerteti a kibővített munkafolyamat.

Ez a cikk bemutatja, hogyan kezelheti a fürt kibővített, más néven az automatikus méretezés. Az automatikus skálázás lehetővé teszi a horizontális felskálázás a példányok száma automatikusan alapján előre meghatározott szabályok és ütemezések. Adja meg a fürt számára az automatikus skálázási beállítások az Azure Portalon, ebben a cikkben leírtak szerint.

Nyissa meg a fürt. A **beállítások**válassza **horizontális felskálázása**. A **konfigurálása**válassza **automatikus skálázás engedélyezése**.

![Automatikus skálázás engedélyezése](media/manage-cluster-scaling/enable-autoscale.png)

A következő ábrán látható a folyamat a következő néhány lépést. További részleteket az alábbiakban a képet.

![Szabály skálázása](media/manage-cluster-scaling/scale-rule.png)

1. Az a **automatikus skálázási beállítás neve** mezőben adjon meg egy nevet, például: *horizontális felskálázás: kihasználtsági gyorsítótár*.

1. A **skálázási mód**válassza **skálázás metrika alapján**. Ebben a módban a dinamikus méretezést biztosít. Lehetőség kiválasztásával **skálázás adott példányszámra**.

1. Válassza ki **és a egy szabály hozzáadásához**.

1. Az a **skálázási szabályhoz** a jobb oldali területén adja meg az értékeket a beállításokhoz.

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

1. Válassza a **Hozzáadás** lehetőséget.

1. Az a **Példányszámkorlátoknál** szakaszban a bal oldalon található, adja meg az értékeket a beállításokhoz.

    | Beállítás | Leírás és érték |
    | --- | --- |
    | **Minimum** | A fürt nem méretezhető alább függetlenül kihasználtsági példányok száma. |
    | **Maximum** | A fürt nem méretezhető felett, függetlenül a kihasználtság példányok száma. |
    | **Alapértelmezett** | Példányok alapértelmezett száma. Ezt a beállítást használja, ha problémák adódnak az erőforrás-metrikák olvasása. |
    |  |  |

1. Kattintson a **Mentés** gombra.

Most már konfigurálta az Azure Data Explorer fürt egy horizontális felskálázási művelet. Adjon meg egy másik szabályt a horizontális leskálázási művelet. Ez a konfiguráció lehetővé teszi, hogy a fürt méretezése dinamikusan megadott metrikák alapján.

Emellett [kezelheti a fürt vertikális felskálázása](manage-cluster-scale-up.md) a fürt megfelelő méretezéshez.

Ha a fürt méretezése problémák segítségre van szüksége [nyisson egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon.
