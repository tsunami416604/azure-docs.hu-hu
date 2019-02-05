---
title: Változó igényeket elégíthet méretezési Azure adatkezelő fürt
description: Ez a cikk ismerteti a lépéseket a horizontális felskálázást és a méretezési csoport-egy fürtben az adatkezelőt az Azure igény szerinti rendelheti.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 38dc7b70630276d51c75ca7e87f0b69ea7fe040a
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735619"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Fürt horizontális felskálázási kezelése változó igényeket elégíthet

Fürt méretezése megfelelően, kritikus fontosságú a teljesítmény az Azure Data Explorer. De igény szerint egy fürtön nem becsülhető 100 %-os pontossággal. Egy statikus fürtméret vezethet korrigáljuk kihasználtsági vagy a túlzott használat esetén ezek egyike sem nem ideális. Az, hogy esetleg jobb megközelítés *méretezési* egy fürthöz, hozzáadása és eltávolítása a kapacitás igény szerinti módosítását. Ez a cikk bemutatja, hogyan kezelheti a fürt horizontális felskálázás.

Keresse meg a fürthöz, majd a **beállítások** kiválasztása **horizontális felskálázása**. A **konfigurálása**válassza **automatikus skálázás engedélyezése**.

![Automatikus skálázás engedélyezése](media/manage-cluster-scaling/enable-autoscale.png)

A következő ábrán látható a folyamat a következő néhány lépést. További részleteket a kép alatt biztosítunk.

![Szabály skálázása](media/manage-cluster-scaling/scale-rule.png)

1. A **automatikus skálázási beállítás neve**, adjon meg egy nevet, például: *horizontális felskálázás: kihasználtsági gyorsítótár*.

1. A **skálázási mód**válassza **skálázás metrika alapján**. Ezt a módot biztosít a dinamikus méretezés; lehetőség kiválasztásával **skálázás adott példányszámra**.

1. Válassza ki **egy szabály hozzáadásához**.

1. Az a **skálázási szabályhoz** a jobb oldali területén adja meg az értékeket a beállításokhoz.

    | Beállítás | Leírás és érték |
    | --- | --- | --- |
    | **Idő összesítése** | Válassza ki például egy összesítő feltételek **átlagos**. |
    | **Metrika neve** | Válassza ki a kívánt kell alapulnia, például a skálázási művelet **gyorsítótár-kihasználtság**. |
    | **Időfelbontási szint statisztikája** | Választhat **átlagos**, **minimális**, **maximális**, és **Sum**. |
    | **Operátor** | Válassza ki a megfelelő beállítást, például **nagyobb vagy egyenlő**. |
    | **Küszöbérték** | Válasszon egy megfelelő értéket. Gyorsítótár-kihasználtság, például a 80 %-os egy jó kiindulási pont. |
    | **Időtartam** | Válassza ki a megfelelő összeget, és tekintse meg a vissza mérőszámok kiszámításakor a rendszer. Indítsa el az alapértelmezett tíz perc. |
    | **Művelet** | Válassza ki a megfelelő beállítást a vertikális vagy horizontális felskálázás. |
    | **Példányok száma** | Válassza ki a csomópontok vagy hozzáadása vagy eltávolítása metrikai feltétel teljesülésekor kívánt példányok számát. |
    | **Lehűlés (percben)** | Válasszon egy megfelelő várakozási időköz a skálázási műveletek között. Indítsa el az alapértelmezett öt perc alatt. |
    |  |  |

1. Válassza a **Hozzáadás** lehetőséget.

1. Az a **Példányszámkorlátoknál** szakaszban a bal oldalon található, adja meg az értékeket a beállításokhoz.

    | Beállítás | Leírás és érték |
    | --- | --- | --- |
    | *Minimum* | Ez a példányok, amelyek a fürt nem skálázza alább függetlenül kihasználtságát. |
    | *Maximum* | Ez a példányok, amelyek a fürt nem skálázza a fenti kihasználtsági függetlenül. |
    | *Alapértelmezett* | Az alapértelmezett száma, használja, ha az erőforrás-metrikák olvasása során. |
    |  |  |

1. Kattintson a **Mentés** gombra.

Most már konfigurálta az Azure Data Explorer fürt egy horizontális felskálázási művelet. Adjon meg egy másik szabályt a horizontális leskálázási művelet. Ez lehetővé teszi a fürt méretezése dinamikusan megadott metrikák alapján.

Ha segítségre van szüksége a fürtméretezés problémák, nyissa meg a támogatási kérelmet a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).