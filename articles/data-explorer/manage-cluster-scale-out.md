---
title: Változó igényeket elégíthet méretezési Azure adatkezelő fürt
description: Ez a cikk ismerteti a lépéseket a horizontális felskálázást és a méretezési csoport-egy fürtben az adatkezelőt az Azure igény szerinti rendelheti.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 15ef5282e0a073e870f2ac12b5fc442407535770
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408442"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Fürt horizontális felskálázás változó igényeket elégíthet kezelése

Fürt méretezése megfelelően, kritikus fontosságú a teljesítmény az Azure Data Explorer. De igény szerint egy fürtön nem becsülhető 100 %-os pontossággal. Egy statikus fürtméret vezethet korrigáljuk kihasználtsági vagy a túlzott használat esetén ezek egyike sem nem ideális. Az, hogy esetleg jobb megközelítés *méretezési* egy fürthöz, hozzáadása és eltávolítása a kapacitás igény szerinti módosítását. Nincsenek két munkafolyamatokat a skálázás, vertikális és horizontális felskálázást. Ez a cikk ismerteti a kibővített munkafolyamat.

Ez a cikk bemutatja, hogyan kezelheti a fürt kibővített, más néven az automatikus méretezés. Az automatikus skálázás lehetővé teszi, hogy a horizontális felskálázás példányszám automatikus alapján előre meghatározott szabályok és ütemezések. Határozza meg a fürt az Azure Portalon, az automatikus skálázási beállítások, az alább ismertetett.

Keresse meg a fürthöz, majd a **beállítások** kiválasztása **horizontális felskálázása**. A **konfigurálása**válassza **automatikus skálázás engedélyezése**.

![Automatikus skálázás engedélyezése](media/manage-cluster-scaling/enable-autoscale.png)

A következő ábrán látható a folyamat a következő néhány lépést. További részleteket a kép alatt biztosítunk.

![Szabály skálázása](media/manage-cluster-scaling/scale-rule.png)

1. A **automatikus skálázási beállítás neve**, adjon meg egy nevet, például: *horizontális felskálázás: kihasználtsági gyorsítótár*.

1. A **skálázási mód**válassza **skálázás metrika alapján**. Ezt a módot biztosít a dinamikus méretezés; lehetőség kiválasztásával **skálázás adott példányszámra**.

1. Válassza ki **egy szabály hozzáadásához**.

1. Az a **skálázási szabályhoz** a jobb oldali területén adja meg az értékeket a beállításokhoz.

    **Feltételek**

    | Beállítás | Leírás és érték |
    | --- | --- | --- |
    | **Idő összesítése** | Válassza ki például egy összesítő feltételek **átlagos**. |
    | **Metrika neve** | Válassza ki a kívánt kell alapulnia, például a skálázási művelet **gyorsítótár-kihasználtság**. |
    | **Időfelbontási szint statisztikája** | Választhat **átlagos**, **minimális**, **maximális**, és **Sum**. |
    | **Operátor** | Válassza ki a megfelelő beállítást, például **nagyobb vagy egyenlő**. |
    | **Küszöbérték** | Válasszon egy megfelelő értéket. Gyorsítótár-kihasználtság, például a 80 %-os egy jó kiindulási pont. |
    | **Időtartam (perc)** | Válassza ki a megfelelő összeget, és tekintse meg a vissza mérőszámok kiszámításakor a rendszer. Indítsa el az alapértelmezett 10 perc. |
    |  |  |

    **Művelet**

    | Beállítás | Leírás és érték |
    | --- | --- | --- |
    | **Művelet** | Válassza ki a megfelelő beállítást, vagy horizontális leskálázási. |
    | **Példányok száma** | Válassza ki a csomópontok vagy hozzáadása vagy eltávolítása metrikai feltétel teljesülésekor kívánt példányok számát. |
    | **Lehűlés (percben)** | Válasszon egy megfelelő várakozási időköz a skálázási műveletek között. Indítsa el az alapértelmezett öt perc alatt. |
    |  |  |

1. Válassza a **Hozzáadás** lehetőséget.

1. Az a **Példányszámkorlátoknál** szakaszban a bal oldalon található, adja meg az értékeket a beállításokhoz.

    | Beállítás | Leírás és érték |
    | --- | --- | --- |
    | *Minimum* | A fürt nem méretezhető alább függetlenül kihasználtsági példányok száma. |
    | *Maximum* | A fürt nem méretezhető felett, függetlenül a kihasználtság példányok száma. |
    | *Alapértelmezett* | Az alapértelmezett száma, ha hiba lépett fel, erőforrás-metrikák olvasása. |
    |  |  |

1. Kattintson a **Mentés** gombra.

Most már konfigurálta az Azure Data Explorer fürt egy horizontális felskálázási művelet. Adjon meg egy másik szabályt a horizontális leskálázási művelet. Ez lehetővé teszi a fürt méretezése dinamikusan megadott metrikák alapján.

Azt is megteheti [méretezési fel a fürt](manage-cluster-scale-up.md) a fürt megfelelő méretezéshez.

Ha segítségre van szüksége a fürtméretezés problémák, nyissa meg a támogatási kérelmet a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
