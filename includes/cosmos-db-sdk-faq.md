---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178870"
---
**1. Hogyan kapnak értesítést az ügyfelek a nyugdíjba vonuló SDK-ról?**

A Microsoft 12 hónapos előzetes értesítést küld a nyugdíjba vonuló SDK támogatásának végéhez, hogy megkönnyítse a támogatott SDK-ra való zökkenőmentes áttérést. Továbbá az ügyfelek különböző kommunikációs csatornákon – az Azure Management Portalon, a Fejlesztői központban, a blogbejegyzésen és a hozzárendelt szolgáltatás-rendszergazdákkal való közvetlen kommunikáción – keresztül kapnak értesítést.

**2. Az ügyfelek a 12 hónapos időszakban egy "leendő" kinyugdíjas Azure Cosmos DB SDK használatával hozhatnak alkalmazásokat?** 

Igen, az ügyfelek teljes hozzáféréssel rendelkeznek a szerzői, üzembe helyezése és módosítása alkalmazások a "leendő" kinyugdíjas Azure Cosmos DB SDK a 12 hónapos türelmi időszak alatt. A 12 hónapos türelmi időszak alatt az ügyfelek azt tanácsolják, hogy térjen ek át az Azure Cosmos DB SDK egy újabb támogatott verziójára.

**3. Az ügyfelek a 12 hónapos értesítési időszak után egy kivisszavonult Azure Cosmos DB SDK használatával hozhatnak alkalmazásokat?**

A 12 hónapos értesítési időszak után az SDK megszűnik. Az Azure Cosmos DB-hez való hozzáférés egy kivett SDK-t használó alkalmazással nem engedélyezett az Azure Cosmos DB platformon. Továbbá a Microsoft nem nyújt ügyfélszolgálatot a kimondott SDK-hoz.

**4. Mi történik az Ügyfél olyan futó alkalmazásokkal, amelyek nem támogatott Azure Cosmos DB SDK-verziót használnak?**

Minden olyan kísérlet, amely az Azure Cosmos DB szolgáltatáshoz egy kivisszavonult SDK-verzióval való csatlakozásra tett kísérleteket a rendszer elutasítja. 

**5. Az új funkciók és funkciók minden nem nyugdíjas SDK-ra vonatkoznak?**

Az új funkciók és funkciók csak az új verziókhoz lesznek hozzáadva. Ha egy régi, nem kirendelt, az SDK-t használja, az Azure Cosmos DB-nek érkező kérések továbbra is az előzőnek megfelelően fognak működni, de nem lesz hozzáférése az új képességekhez.  

**6. Mit tegyek, ha nem tudom frissíteni a jelentkezésemet a határidő lejárta előtt?**

Javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-ra. Miután egy SDK-t megjelöltek a nyugdíjazási rendszerhez, 12 hónapáll rendelkezésére az alkalmazás frissítésére. Ha bármilyen okból nem tudja befejezni az alkalmazás frissítését ezen az időkereten belül, akkor kérjük, forduljon a [Cosmos DB csapatához,](mailto:askcosmosdb@microsoft.com) és kérje a segítségüket a határidő előtt.

