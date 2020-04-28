---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67178870"
---
**1. Hogyan fogja értesíteni az ügyfeleket a kivonási SDK-ról?**

A Microsoft 12 hónapos előzetes értesítést küld a kivonási SDK támogatásának végére, így megkönnyítve a zökkenőmentes áttérést egy támogatott SDK-ra. További ügyfeleink a különböző kommunikációs csatornákon keresztül kapnak értesítést – Azure felügyeleti portál, fejlesztői központ, blogbejegyzés és közvetlen kommunikáció a hozzárendelt szolgáltatás-rendszergazdák számára.

**2. a felhasználók a 12 hónapos időszak alatt "a" kivont Azure Cosmos DB SDK-t használó alkalmazásokat hozhatnak létre?** 

Igen, az ügyfelek teljes hozzáférést kapnak az alkalmazások létrehozásához, telepítéséhez és módosításához a "to-have" kivont Azure Cosmos DB SDK-val a 12 hónapos türelmi időszak alatt. A 12 hónapos türelmi időszak alatt az ügyfeleknek javasoljuk, hogy a megfelelő módon telepítse át az Azure Cosmos DB SDK újabb támogatott verziójára.

**3. az ügyfelek a 12 hónapos értesítési időszak után kivont Azure Cosmos DB SDK-val hozhatnak létre és módosíthatnak alkalmazásokat?**

A 12 hónapos értesítési időszak után a rendszer kivonja az SDK-t. A Azure Cosmos DB platform nem engedélyezi a kivont SDK-t használó alkalmazások Azure Cosmos DBhoz való hozzáférését. Továbbá a Microsoft nem nyújt támogatást a kivont SDK-hoz.

**4. mi történik az ügyfél olyan futó alkalmazásaival, amelyek nem támogatott Azure Cosmos DB SDK-verziót használnak?**

A Azure Cosmos DB szolgáltatáshoz a kivont SDK-verzióval való kapcsolódásra tett kísérletek el lesznek utasítva. 

**5. a rendszer az új funkciókat és funkciókat alkalmazza az összes nem kivont SDK-ra?**

Új szolgáltatások és funkciók csak új verziókhoz lesznek hozzáadva. Ha az SDK régi, nem kivont verzióját használja, az Azure Cosmos DB kérelmei továbbra is az előzővel fognak működni, de nem fog tudni hozzáférni az új funkciókhoz.  

**6. Mi a teendő, ha nem tudom frissíteni az alkalmazást a kivágási dátum előtt?**

Javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-ra. Ha az SDK-t már megcímkézték a nyugdíjazáshoz, 12 hónapig kell frissítenie az alkalmazást. Ha bármilyen okból kifolyólag nem tudja befejezni az alkalmazás frissítését ezen az időkereten belül, vegye fel a kapcsolatot a [Cosmos db csapatával](mailto:askcosmosdb@microsoft.com) , és kérjen segítséget a kivágási dátum előtt.

