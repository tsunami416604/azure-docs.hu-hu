---
title: Változó igényeket elégíthet méretezési Azure adatkezelő fürt
description: Ez a cikk ismerteti a lépéseket a horizontális felskálázást és a méretezési csoport-egy fürtben az adatkezelőt az Azure igény szerinti rendelheti.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735815"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Fürthöz méretezés akár kezelése változó igényeket elégíthet

Fürt méretezése megfelelően, kritikus fontosságú a teljesítmény az Azure Data Explorer. De igény szerint egy fürtön nem becsülhető 100 %-os pontossággal. Egy statikus fürtméret vezethet korrigáljuk kihasználtsági vagy a túlzott használat esetén ezek egyike sem nem ideális. Az, hogy esetleg jobb megközelítés *méretezési* egy fürthöz, hozzáadása és eltávolítása a kapacitás- és CPU igény szerinti módosítását. Ez a cikk bemutatja, hogyan kezelheti a fürthöz méretezés fel.

Keresse meg a fürthöz, majd a **beállítások** kiválasztása **vertikális felskálázás**.

Szeretné majd a rendelkezésre álló termékváltozatok listáját adott. A lista engedélyezett kártyák közül választhat. Például az alábbi ábrán csak egy Termékváltozat, amely D14_vs lehet kiválasztani nincs.

![Vertikális felskálázás](media/manage-cluster-scale-up/scale-up.png)

D13_v2 le van tiltva, mert ez az aktuális Termékváltozat a fürt. 8. és L16 letiltják, mert azok a régióban, ahol a fürt nem érhető el.

A Termékváltozat csak kattintson a Termékváltozat módosításához szeretné használni, és kattintson a **kiválasztása** gombra.

[!NOTE] A vertikális felskálázási folyamat néhány percet is igénybe vehet, és ebben az időszakban a fürt felfüggesztjük. Vegye figyelembe, hogy a vertikális leskálázást károsíthatják a fürt teljesítményét.

Ha segítségre van szüksége a fürtméretezés problémák, nyissa meg a támogatási kérelmet a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).