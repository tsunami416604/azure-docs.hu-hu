---
title: Változó igényeket elégíthet méretezési Azure adatkezelő fürt
description: Ez a cikk ismerteti a lépéseket felfelé és lefelé egy igény szerinti rendelheti az adatkezelőt az Azure-fürtön.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: bc3f97c798f5e040908e8103c00d3f015f8c824d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415334"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Fürt vertikális felskálázása változó igényeket elégíthet kezelése

Fürt méretezése megfelelően, kritikus fontosságú a teljesítmény az Azure Data Explorer. De igény szerint egy fürtön nem becsülhető 100 %-os pontossággal. Egy statikus fürtméret vezethet korrigáljuk kihasználtsági vagy a túlzott használat esetén ezek egyike sem nem ideális. Az, hogy esetleg jobb megközelítés *méretezési* egy fürthöz, hozzáadása és eltávolítása a kapacitás- és CPU igény szerinti módosítását. Nincsenek két munkafolyamatokat a skálázás, vertikális és horizontális felskálázást. Ez a cikk bemutatja, hogyan kezelheti a fürt vertikális felskálázása.

1. Keresse meg a fürthöz, majd a **beállítások** kiválasztása **vertikális felskálázás**.

    A rendelkezésre álló termékváltozatok listáját felhőszolgáltatására. Például az alábbi ábrán nincs Termékváltozat csak egy érhető el: D14_V2.

    ![Vertikális felskálázás](media/manage-cluster-scale-up/scale-up.png)

    D13_V2 le van tiltva, mert ez az aktuális Termékváltozat a fürt. 8. és L16 miatt le vannak tiltva, a fürt a régió nem érhető el.

1. A Termékváltozat, jelölje ki a Termékváltozat szeretne, majd nyomja le az **kiválasztása** gombra.

> [!NOTE]
> A vertikális felskálázási folyamat néhány percet is igénybe vehet, és ebben az időszakban a fürt felfüggesztjük. Vegye figyelembe, hogy a vertikális leskálázást károsíthatják a fürt teljesítményét.

Most már az Azure Data Explorer fürt egy felfelé vagy lefelé műveletet végzett el. Azt is megteheti [fürt kibővített](manage-cluster-scale-out.md), más néven az automatikus méretezés, dinamikusan alapján Ön által megadott mérőszámok méretezését.

Ha segítségre van szüksége a fürtméretezés problémák, nyissa meg a támogatási kérelmet a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
