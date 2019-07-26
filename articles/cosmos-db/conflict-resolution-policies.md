---
title: Ütközés-feloldási típusok és megoldási házirendek több írási régióval Azure Cosmos DB
description: Ez a cikk a Azure Cosmos DB ütközési kategóriáit és ütközés-feloldási házirendjeit ismerteti.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 45b7257f67be8ba5c134717d73488916056b7a7d
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384214"
---
# <a name="conflict-types-and-resolution-policies"></a>Ütközéstípusok és -feloldási szabályzatok

Az ütközések és az ütközések feloldására szolgáló házirendek akkor alkalmazhatók, ha a Azure Cosmos DB-fiók több írási régióval van konfigurálva.

Több írási régióval konfigurált Azure Cosmos-fiókok esetén a frissítési ütközések akkor fordulhatnak elő, ha az írók egyidejűleg frissítik ugyanazt az elemeket több régióban. A frissítési ütközések a következő három típusból lehetnek:

* **Ütközések beszúrása**: Ezek az ütközések akkor fordulnak elő, ha egy alkalmazás egyidejűleg két vagy több olyan elemet szúr be, amelyek ugyanazzal az egyedi indexszel rendelkeznek. Előfordulhat például, hogy ez az ütközés egy ID tulajdonsággal együtt fordul elő.

* **Ütközések cseréje**: Ezek az ütközések akkor fordulhatnak elő, ha egy alkalmazás két vagy több régióban egyszerre frissíti ugyanazt az elemeket.

* **Ütközések törlése**: Ezek az ütközések akkor fordulhatnak elő, ha egy alkalmazás egyidejűleg törli az egyik régióban lévő elemeket, és egy másik régióban frissíti azt.

## <a name="conflict-resolution-policies"></a>Ütközésfeloldási szabályzatok

Azure Cosmos DB rugalmas, házirend által vezérelt mechanizmust kínál az írási ütközések feloldásához. Az Azure Cosmos-tárolóban két ütközés-feloldási szabályzat közül választhat:

- **Utolsó írási WINS (LWW)** : Ez a megoldási szabályzat alapértelmezés szerint rendszer által definiált időbélyeg-tulajdonságot használ. Ez a Time-Sync Clock protokollon alapul. Ha az SQL API-t használja, megadhat bármilyen más egyéni numerikus tulajdonságot (például egy időbélyeg saját fogalmát) az ütközés feloldásához. Az egyéni numerikus tulajdonságokat az *ütközés feloldási útvonalnak*is nevezzük. 

  Ha két vagy több elem ütközik az INSERT vagy a Replace művelettel, az ütközés feloldási útvonalának legmagasabb értékét tartalmazó elem lesz a nyertes. A rendszer meghatározza a nyertest, ha több elemnek ugyanaz a numerikus értéke az ütközési feloldási útvonalhoz. Minden régió garantáltan egyetlen nyertesnek kell lennie, és a véglegesített elem azonos verziójával kell végződnie. Ha törlési ütközések merülnek fel, a törölt verzió mindig az INSERT vagy a Replace ütközések között nyer. Ennek az eredménynek az az oka, hogy az ütközés feloldási elérési útjának értéke nem számít.

  > [!NOTE]
  > Az utolsó írási WINS az alapértelmezett ütközés-feloldási házirend. A következő API-khoz érhető el: SQL, MongoDB, Cassandra, Gremlin és Table.

  További információért lásd: [LWW ütközési megoldási szabályzatokat használó példák](how-to-manage-conflicts.md).

- **Egyéni**: Ez a megoldási szabályzat az alkalmazás által definiált szemantikai szabályokhoz készült az ütközések egyeztetéséhez. Amikor beállítja ezt a házirendet az Azure Cosmos-tárolón, regisztrálnia kell egy *egyesítéssel tárolt eljárást*is. Ezt az eljárást automatikusan meghívja a rendszer, ha ütközések észlelhetők a kiszolgálón található adatbázis-tranzakció alatt. A rendszer biztosít pontosan egyszer garantálja a kötelezettségvállalás protokoll részeként egy merge eljárás végrehajtására.  

  Ha a tárolót az egyéni feloldási lehetőséggel konfigurálja, és nem tud regisztrálni egy egyesítési eljárást a tárolón, vagy az egyesítési eljárás futásidejű kivételt jelez, az ütközések az *ütközések hírcsatornába*íródnak. Az alkalmazásnak ezután manuálisan kell feloldania az ütközéseket a hírcsatornában. További információ: példák az [Egyéni megoldási szabályzat használatára és az ütközések hírcsatornájának használatára](how-to-manage-conflicts.md).

  > [!NOTE]
  > Az egyéni ütközés-feloldási szabályzat csak az SQL API-fiókok esetében érhető el.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan konfigurálhat ütközés-feloldási házirendeket:

* [Több főkiszolgáló konfigurálása az alkalmazásokban](how-to-multi-master.md)
* [Az ütközés-feloldási házirendek kezelése](how-to-manage-conflicts.md)
* [Az ütközések hírcsatornájának beolvasása](how-to-manage-conflicts.md#read-from-conflict-feed)
