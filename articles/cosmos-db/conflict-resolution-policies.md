---
title: Ütközésfeloldási típusok és megoldási szabályzatok az Azure Cosmos DB-ben
description: Ez a cikk ismerteti az ütközési kategóriák és az ütközésfeloldási szabályzatok az Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a8ee72f46e1789088e779c10a0824262469ffde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441985"
---
# <a name="conflict-types-and-resolution-policies"></a>Ütközéstípusok és -feloldási szabályzatok

Ütközések és ütközésfeloldási szabályzatok alkalmazhatók, ha az Azure Cosmos DB-fiók több írási régióval van konfigurálva.

Több írási régióval konfigurált Azure Cosmos-fiókok esetén frissítési ütközések fordulhatnak elő, ha az írók egyidejűleg több régióban frissítik ugyanazt az elemet. A frissítési ütközések a következő három típusból állnak:

* **Ütközések beszúrása**: Ezek az ütközések akkor fordulhatnak elő, ha egy alkalmazás egyszerre két vagy több, azonos egyedi indexszel rendelkező elemet szúr be két vagy több régióban. Ez az ütközés például egy azonosító tulajdonsággal fordulhat elő.

* **Az ütközések cseréje**: Ezek az ütközések akkor fordulhatnak elő, ha egy alkalmazás egyszerre frissíti ugyanazt az elemet két vagy több régióban.

* **Ütközések törlése**: Ezek az ütközések akkor fordulhatnak elő, ha egy alkalmazás egyidejűleg töröl egy elemet az egyik régióban, és frissíti azt egy másik régióban.

## <a name="conflict-resolution-policies"></a>Ütközésfeloldási szabályzatok

Az Azure Cosmos DB rugalmas, házirend-alapú mechanizmust kínál az írási ütközések feloldásához. Az Azure Cosmos-tárolókét ütközésfeloldási szabályzata közül választhat:

* **Last Write Wins (LWW)**: Ez a megoldási házirend alapértelmezés szerint rendszer által definiált időbélyeg-tulajdonságot használ. Az idő-szinkronizálási óra protokollon alapul. Ha az SQL API-t használja, megadhatja bármely más egyéni numerikus tulajdonságot (például az időbélyeg saját fogalmát), amelyet az ütközések feloldásához használni kell. Az egyéni numerikus tulajdonságot *ütközésfeloldási útvonalnak is nevezik.* 

  Ha két vagy több elem ütközik a beszúrási vagy csereműveleteksorán, az ütközésfeloldási útvonal legnagyobb értékével rendelkező elem lesz a győztes. A rendszer határozza meg a győztest, ha több elem azonos numerikus értékkel rendelkezik az ütközésfeloldási útvonalhoz. Minden régió garantáltan egyetlen győzteshez közelít, és a véglegesített elem azonos verziójával végződik. Törlési ütközések esetén a törölt verzió mindig megnyeri az ütközések beszúrását vagy cseréjét. Ez az eredmény az ütközésfeloldási útvonal értékétől függetlenül bekövetkezik.

  > [!NOTE]
  > A Last Write Wins az alapértelmezett ütközésfeloldási házirend, amely a következő API-k időbélyegét `_ts` használja: SQL, MongoDB, Cassandra, Gremlin és Table. Az egyéni numerikus tulajdonság csak az SQL API-hoz érhető el.

  További információ: [Az LWW ütközésfeloldási házirendjeit használó példák.](how-to-manage-conflicts.md)

* **Egyéni**: Ez a megoldási házirend alkalmazásáltal definiált szemantikára készült az ütközések egyeztetéséhez. Ha ezt a szabályzatot az Azure Cosmos-tárolóban állítja be, regisztrálnia kell egy *egyesítési tárolt eljárást*is. Ez az eljárás automatikusan meghívásra kerül, ha a kiszolgálón lévő adatbázis-tranzakció során ütközéseket észlel. A rendszer pontosan egyszer garantálja az egyesítési eljárás végrehajtását a kötelezettségvállalási protokoll részeként.  

  Ha a tárolót az egyéni feloldási beállítással konfigurálja, és nem sikerül regisztrálnia egy egyesítési eljárást a tárolón, vagy az egyesítési eljárás futásidőben kivételt okoz, az ütközések az *ütközések hírcsatornába*kerülnek. Az alkalmazásnak ezután manuálisan kell feloldania az ütközések hírcsatornájában lévő ütközéseket. További információ: [példák az egyéni megoldási házirend használatára és az ütközések hírcsatornájának használatára.](how-to-manage-conflicts.md)

  > [!NOTE]
  > Az egyéni ütközésfeloldási házirend csak SQL API-fiókok esetén érhető el.

## <a name="next-steps"></a>További lépések

További információ az ütközésfeloldási házirendek konfigurálásáról:

* [Többfőkiszolgáló konfigurálása az alkalmazásokban](how-to-multi-master.md)
* [Az ütközésfeloldási házirendek kezelése](how-to-manage-conflicts.md)
* [Hogyan kell olvasni a konfliktusok feed](how-to-manage-conflicts.md#read-from-conflict-feed)
