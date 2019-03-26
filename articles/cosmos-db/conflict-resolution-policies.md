---
title: Ütközés feloldása típusok és több megoldás szabályzatok írási régiók az Azure Cosmos DB-ben
description: Ez a cikk ismerteti az ütközés kategóriákat és ütközés feloldása házirendek az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 24201cfd657d4f23eb962b7407ed20262d780cf7
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407419"
---
# <a name="conflict-types-and-resolution-policies"></a>Ütközéstípusok és -feloldási szabályzatok

Ütközések és a szabályzatok alkalmazandók, ha az Azure Cosmos DB-fiók van konfigurálva több ütközésfeloldás írási régió.

Az Azure Cosmos-konfigurálva több írási régióval rendelkező fiókok esetében a frissítési ütközések előfordulhatnak, ha írók egyszerre több régióban található azonos elem frissítése. Frissítési ütközések a következő három típusú lehet:

* **Ütközések beszúrása**: Az ütközések akkor fordulhat elő, ha egy alkalmazás egyidejűleg szúr be két vagy több elemet a ugyanazon egyedi indexszel rendelkező két vagy több régióban. Ha például a ütközést okozhat, egy ID tulajdonsággal.

* **Cserélje le az ütközések**: Az ütközések akkor fordulhat elő, amikor egy alkalmazás egyidejűleg két vagy több régióban található azonos elem frissíti.

* **Törlés ütközések**: Az ütközések akkor fordulhat elő, amikor az alkalmazások egyszerre egy adott régióban egy elem törlése, és frissíti azt egy másik régióban.

## <a name="conflict-resolution-policies"></a>Ütközésfeloldási szabályzatok

Az Azure Cosmos DB írási ütközések feloldása egy rugalmas házirendekkel vezérelt mechanizmust biztosít. Az Azure Cosmos-tárolókat két ütközés feloldása házirendek közül választhat:

- **Utolsó írás (LWW) Wins**: A névfeloldási házirend alapértelmezés szerint használja a rendszer által meghatározott időbélyeg-tulajdonság. Az óra időszinkronizálás protokoll alapján. Ha az SQL API-t használja, minden más egyéni numerikus tulajdonság (például a saját fogalma időbélyeg) ütközésfeloldás használandó is megadhat. Egyéni numerikus tulajdonság is nevezzük a *ütközés feloldása elérési út*. 

  Két vagy több elem ütközés a Beszúrás, vagy cserélje le az operations, az elem elérési útja ütközés feloldása a legnagyobb értékű a győztes lesz. A rendszer a győztes határozza meg, ha több elem is rendelkezik, az ütközés feloldása elérési út numerikus ugyanazt az értéket. Minden régió garantáltan szerveződik egy egységes és a záró fel a véglegesített elem ugyanazzal a verzióval. Amikor wins-törlés ütközések van szó, a törölt verziót mindig helyezze be vagy cserélje le az ütközések. Az eredmény akkor fordul elő, függetlenül attól, hogy mi az az érték, az ütközés feloldása útvonal.

  > [!NOTE]
  > Az alapértelmezett ütközésfeloldási házirend utolsó írás Wins. Érhető el a következő API-kat: Az SQL, MongoDB, Cassandra, Gremlin és táblában.

  További tudnivalókért lásd: [példák LWW használó megoldás házirendek ütköznek](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

- **Egyéni**: A névfeloldási házirend készült alkalmazás által meghatározott szemantika ütközések egyeztetéséhez. Ha ezt a szabályzatot állít be az Azure Cosmos-tároló, is regisztrálnia kell egy *tárolt eljárás egyesítése*. Ez az eljárás automatikusan megnyílik, ha ütközést észlel a kiszolgálón az adatbázis-tranzakciók alapján. A rendszer biztosít pontosan egyszer garantálja a kötelezettségvállalás protokoll részeként egy merge eljárás végrehajtására.  

  Ha a egyéni feloldási lehetőséggel konfigurálja a tároló és a egy merge eljárás a tároló regisztrálása nem sikerült, vagy az egyesítési eljárás kivételt jelez, futtatás közben, az ütközések írt a *hírcsatorna ütközések*. Az alkalmazás ezután kell manuálisan oldja fel az ütközést a hírcsatorna-ütközéseket. További tudnivalókért lásd: [példák az egyéni házirend használatával, és hogyan használható a hírcsatorna-ütközések](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy).

  > [!NOTE]
  > Egyéni ütközésfeloldási házirend csak az SQL API-fiókok esetében érhető el.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan ütközés feloldása szabályzatok konfigurálására:

* [Az alkalmazások több főkiszolgálós konfigurálása](how-to-multi-master.md)
* [A LWW ütközésfeloldási házirend használata](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Az egyéni ütközésfeloldási házirend használata](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [Az ütközések csatorna beolvasása](how-to-manage-conflicts.md#read-from-conflict-feed)
