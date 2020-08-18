---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515258"
---
**1. Hogyan fogja értesíteni az ügyfeleket a kivonási SDK-ról?**

A Microsoft 12 hónapos előzetes értesítést küld a kivonási SDK támogatásának végére, így megkönnyítve a zökkenőmentes áttérést egy támogatott SDK-ra. Ezen túlmenően az ügyfelek különböző kommunikációs csatornákon keresztül kapnak értesítést – Azure Portal, Azure-frissítések és közvetlen kommunikáció a hozzárendelt szolgáltatás-rendszergazdák számára.

**2. a felhasználók a 12 hónapos időszak alatt "a" kivont Azure Cosmos DB SDK-t használó alkalmazásokat hozhatnak létre?** 

Igen, az ügyfelek teljes hozzáférést kapnak az alkalmazások létrehozásához, telepítéséhez és módosításához a "to-have" kivont Azure Cosmos DB SDK használatával a 12 hónapos értesítési időszakban. A 12 hónapos értesítési időszakban a felhasználóknak javasoljuk, hogy a megfelelő módon telepítse át az Azure Cosmos DB SDK újabb támogatott verziójára. 

**3. a kivonulási dátum után mi történik a nem támogatott Azure Cosmos DB SDK-t használó alkalmazásokkal?** 

A lejárati dátum után Azure Cosmos DB a továbbiakban nem fog hibajavításokat felvenni, új szolgáltatásokat hozzáadni, és támogatást nyújt a kivont SDK-verziókhoz. Ha nem szeretné frissíteni, az SDK kivont verzióiból küldött kéréseket továbbra is a Azure Cosmos DB szolgáltatás fogja kiszolgálni. 

**4. mely SDK-verziók lesznek a legújabb szolgáltatásai és frissítései?**

Új funkciók és frissítések csak a legújabb támogatott fő SDK-verzió legújabb alverziójában lesznek hozzáadva. Javasoljuk, hogy mindig a legújabb verziót használja, hogy kihasználhassa az új funkciókat, a teljesítménnyel kapcsolatos javításokat és a hibajavításokat. Ha az SDK egy régi, nem kivont verzióját használja, az Azure Cosmos DB kérelmei továbbra is működni fognak, de nem fog tudni hozzáférni az új funkciókhoz.  

**5. Mi a teendő, ha nem tudom frissíteni az alkalmazást a kivágási dátum előtt?**

Javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-ra. Ha az SDK-t már címkézték a nyugdíjazáshoz, 12 hónapig kell frissítenie az alkalmazást. Ha nem tudja frissíteni a lejárati dátummal, az SDK kivont verzióiból küldött kéréseket továbbra is Azure Cosmos DB fogja kiszolgálni, így a futó alkalmazások továbbra is működni fognak. Azure Cosmos DB azonban a továbbiakban nem fog hibajavításokat felvenni, új funkciókat hozzáadni, és támogatást nyújtani a kivont SDK-verziókhoz. 

Ha támogatási csomaggal rendelkezik, és technikai támogatásra van szüksége, vegye [fel velünk a kapcsolatot](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) a támogatási jegy bejelentésével.
    


