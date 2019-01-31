---
title: Ütközés feloldása típusok és több megoldás szabályzatok írási régiók az Azure Cosmos DB-ben
description: Ez a cikk ismerteti az ütközés kategóriákat és ütközés feloldása házirendek az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 31e6fa596bb053ee8cd19f641349f02ee169b6eb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472337"
---
# <a name="conflict-types-and-resolution-policies"></a>Ütközéstípusok és -feloldási szabályzatok

Ütközések és a szabályzatok alkalmazandók, ha az Azure Cosmos DB-fiók van konfigurálva több ütközésfeloldás írási régió.

Konfigurált több írási régióval rendelkező Azure Cosmos DB-fiókok esetében a frissítések ütközésének előfordulhatnak, ha írók egyszerre több régióban található azonos elem frissítése. A következő három típusú frissítések ütközésének sorolhatók:

* **Ütközések beszúrása**: Az ütközések akkor fordulhat elő, amikor egy alkalmazás egyidejűleg a ugyanazon egyedi indexszel rendelkező két vagy több elemet beszúrja két vagy több régióban. Tegyük fel az ütközés fordulhat elő, egy ID tulajdonsággal együtt. Az összes írási művelet sikeres kezdetben lehet a megfelelő helyi régióban. Azonban úgy dönt, az ütközésfeloldási házirend alapján, csak egy elemet az eredeti azonosítójú elkötelezett végül.

* **Cserélje le az ütközések**: Az ütközések akkor fordulhat elő, amikor egy alkalmazás frissíti egy elem egyidejűleg két vagy több régió közül.

* **Törlés ütközések**: Az ütközések akkor fordulhat elő, amikor egy alkalmazás egyidejűleg egy elem törlése egy adott régióban található, és frissíti azt egy másik régióban.

## <a name="conflict-resolution-policies"></a>Ütközésfeloldási szabályzatok

Az Azure Cosmos DB frissítési ütközések feloldása egy rugalmas házirendekkel vezérelt mechanizmust biztosít. Egy Azure Cosmos DB-tárolók két ütközés feloldása házirendek közül választhat:

- **Utolsó írás (LWW) Wins**: A névfeloldási házirend alapértelmezés szerint használja a rendszer által meghatározott időbélyeg-tulajdonság. Az óra időszinkronizálás protokoll alapján. Ha az Azure Cosmos DB SQL API használja, minden más egyéni numerikus tulajdonság ütközésfeloldás használandó is megadhat. Egyéni numerikus tulajdonság is nevezzük az ütközés feloldása elérési utat. 

  Két vagy több elem ütközés a Beszúrás, vagy cserélje le az operations, az elem elérési útja ütközés feloldása a legnagyobb értékű a győztes lesz. A rendszer a győztes határozza meg, ha több elem is rendelkezik, az ütközés feloldása elérési út numerikus ugyanazt az értéket. Minden régió garantáltan szerveződik egy egységes és a záró fel a véglegesített elem ugyanazzal a verzióval. Amikor wins-törlés ütközések van szó, a törölt verziót mindig helyezze be vagy cserélje le az ütközések. Az eredmény akkor fordul elő, függetlenül attól, hogy az érték, az ütközés feloldása útvonal.

  > [!NOTE]
  > Az alapértelmezett ütközésfeloldási házirend utolsó írás Wins. SQL, az Azure Cosmos DB Table, a MongoDB, a Cassandra és a Gremlin API-fiókok érhető el.

  További tudnivalókért lásd: [példák LWW használó megoldás házirendek ütköznek](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Egyéni**: A névfeloldási házirend készült alkalmazás által meghatározott szemantika ütközések egyeztetéséhez. Ez a szabályzat az Azure Cosmos DB-tároló állít be, amikor is egy merge tárolt eljárás regisztrálnia kell. Ez az eljárás automatikusan megnyílik, ha a kiszolgáló, adatbázis-tranzakció alatt ütközések észlelése. A rendszer biztosít pontosan egyszer garantálja a kötelezettségvállalás protokoll részeként egy merge eljárás végrehajtására.  

  Vannak, ne felejtse el, ha az egyéni feloldási kapcsolóval a tároló konfigurálása két pont. Ha nem sikerül regisztrálni egy merge eljárás a tároló, vagy az egyesítéses eljárás kivételt jelez, futtatás közben, az ütközések a hírcsatorna-ütközések írt. Az alkalmazás ezután kell manuálisan oldja fel az ütközést a hírcsatorna-ütközéseket. További tudnivalókért lásd: [példák az egyéni házirend használatával, és hogyan használható a hírcsatorna-ütközések](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Egyéni ütközésfeloldási házirend csak az SQL API-fiókok esetében érhető el.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan ütközés feloldása szabályzatok konfigurálása. Lásd az alábbi cikkeket:

* [Használja az LWW ütközésfeloldási házirend](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Az egyéni ütközésfeloldási házirend használata](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Használja a hírcsatorna-ütközés](how-to-manage-conflicts.md#read-from-conflict-feed)
