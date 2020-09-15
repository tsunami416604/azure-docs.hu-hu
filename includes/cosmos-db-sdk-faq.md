---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068735"
---
**Hogyan értesítjük a kivonási SDK-t?**

A Microsoft 12 hónapos előzetes értesítést küld a kivonási SDK támogatásának vége előtt, így megkönnyítve a zökkenőmentes áttérést egy támogatott SDK-ra. A következő kommunikációs csatornákon keresztül értesítjük Önt: az Azure Portal, az Azure-frissítések és a közvetlen kommunikáció a hozzárendelt szolgáltatás-rendszergazdák számára.

**Készíthetek alkalmazásokat egy kivont Azure Cosmos DB SDK használatával a 12 hónapos időszakban?** 

Igen, az alkalmazások létrehozásához, üzembe helyezéséhez és módosításához a 12 hónapos felmondási időszakban a to-off Azure Cosmos DB SDK-t használhatja. Javasoljuk, hogy a szükséges módon telepítse át a Azure Cosmos DB SDK újabb támogatott verziójára a 12 hónapos értesítési időszak alatt. 

**A lejárati dátum után mi történik a nem támogatott Azure Cosmos DB SDK-t használó alkalmazásokkal?** 

A lejárati dátum után Azure Cosmos DB megszűnik a hibajavítások, új funkciók hozzáadása, vagy támogatás nyújtása a kivont SDK-verziókhoz. Ha nem szeretné frissíteni, az SDK kivont verzióiból küldött kéréseket továbbra is a Azure Cosmos DB szolgáltatás fogja kiszolgálni. 

**Mely SDK-verziók lesznek a legújabb szolgáltatásai és frissítései?**

Az új funkciók és frissítések csak a legújabb támogatott fő SDK-verzió legújabb alverziójában lesznek hozzáadva. Javasoljuk, hogy mindig a legújabb verziót használja, hogy kihasználhassa az új funkciókat, a teljesítménnyel kapcsolatos javításokat és a hibajavításokat. Ha az SDK egy régi, nem kivont verzióját használja, akkor a Azure Cosmos DBre küldött kérések továbbra is működni fognak, de nem lesz hozzáférése az új funkciókhoz.  

**Mi a teendő, ha nem tudom frissíteni az alkalmazást a kivágási dátum előtt?**

Javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-ra. Miután az SDK-t kicímkézték a nyugdíjazáshoz, 12 hónapig kell frissítenie az alkalmazást. Ha nem tudja frissíteni a lejárati dátummal, az SDK kivont verzióiból küldött kéréseket továbbra is Azure Cosmos DB fogja kiszolgálni, így a futó alkalmazások továbbra is működni fognak. A Azure Cosmos DB azonban többé nem javít, nem vehet fel új funkciókat, és nem nyújt támogatást a kivont SDK-verziókhoz. 

Ha támogatási csomaggal rendelkezik, és technikai támogatásra van szüksége, [vegye fel velünk a kapcsolatot](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) egy támogatási jegy bejelentésével.
    


