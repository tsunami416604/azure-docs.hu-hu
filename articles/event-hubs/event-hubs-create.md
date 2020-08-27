---
title: Azure rövid útmutató – Event hub létrehozása a Azure Portal használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Azure-eseményközpontot az Azure Portallal, majd hogyan küldhet és fogadhat eseményeket a .NET Standard SDK használatával.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 84cafcc86142cb9b97639c023971e7d290fc79fc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927884"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Rövid útmutató: Eseményközpont létrehozása az Azure Portallal
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebben a rövid útmutatóban az [Azure Portal](https://portal.azure.com) használatával hoz létre eseményközpontot.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport Azure-erőforrások logikai gyűjteménye. Minden erőforrás üzembe helyezése és kezelése erőforráscsoportban történik. Erőforráscsoport létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **Erőforráscsoportok** lehetőséget. Ezután kattintson az **Add** (Hozzáadás) gombra.

   ![Erőforráscsoportok – Hozzáadás gomb](./media/event-hubs-quickstart-portal/resource-groups1.png)

1. Az **előfizetés**mezőben válassza ki annak az Azure-előfizetésnek a nevét, amelyben létre kívánja hozni az erőforráscsoportot.
1. Adja **meg az erőforráscsoport egyedi nevét**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e az aktuálisan kiválasztott Azure-előfizetésben.
1. Válassza ki az erőforráscsoport **régióját** .
1. Válassza a **felülvizsgálat + létrehozás**lehetőséget.

   ![Erőforráscsoport – létrehozás](./media/event-hubs-quickstart-portal/resource-groups2.png)
1. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget. 

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Egy Event Hubs-névtér egyedi hatókörkezelési tárolót biztosít, amelyre a teljes tartománynevével lehet hivatkozni, és ebben a tárolóban egy vagy több eseményközpontot is létrehozhat. Ha létre szeretne hozni egy névteret az erőforráscsoportban a portál használatával, végezze el az alábbi műveleteket:

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra a képernyő bal felső részén.
1. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget, majd válassza a **csillag ( `*` )** lehetőséget a **Event Hubs** elem mellett az **elemzési** kategóriában. Győződjön meg arról, hogy a bal oldali navigációs menüben a **Event Hubs** a **Kedvencek közé** kerül. 
    
   ![Event Hubs keresése](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
1. A bal oldali navigációs menüben kattintson a **Kedvencek** elemre, majd a **Hozzáadás** elemre az eszköztáron válassza a **Event Hubs** lehetőséget.

   ![Hozzáadás gomb](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
1. A **névtér létrehozása** oldalon hajtsa végre a következő lépéseket:  
   1. Válassza ki azt az **előfizetést** , amelyben létre kívánja hozni a névteret.  
   1. Válassza ki az előző lépésben létrehozott **erőforráscsoportot** .   
   1. Adja meg a névtér **nevét** . A rendszer azonnal ellenőrzi, hogy a név elérhető-e.  
   1. Válassza ki a névtér **helyét** .      
   1. Válassza ki az **árképzési szintet** (alapszintű vagy standard).    
   1. Hagyja meg az **átviteli egységek** beállításait. Az átviteli egységekkel kapcsolatos további tudnivalókért tekintse meg a [Event Hubs méretezhetőségét](event-hubs-scalability.md#throughput-units)ismertető témakört.  
   1. Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre.
      
      ![Eseményközponti névtér létrehozása](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   1. A **felülvizsgálat + létrehozás** oldalon tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra. Várjon, amíg az üzembe helyezés befejeződik. 
      
      ![Felülvizsgálat + Létrehozás lap](./media/event-hubs-quickstart-portal/review-create.png)
      
   1. A **központi telepítés** lapon válassza az **erőforrás keresése** lehetőséget a névtér oldalának megnyitásához. 
      
      ![Az üzembe helyezés befejeződött – ugrás az erőforráshoz](./media/event-hubs-quickstart-portal/deployment-complete.png)  
   1. Ellenőrizze, hogy megjelenik-e a **Event Hubs névtér** oldal a következő példához hasonlóan:   
      
      ![A névtér kezdőlapja](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

      > [!NOTE]
      > Az Azure Event Hubs egy Kafka-végpontot biztosít. Ez a végpont lehetővé teszi, hogy a Event Hubs névteret natív módon tudja értelmezni [Apache Kafka](https://kafka.apache.org/intro) üzenetküldési protokoll és API-k. Ezzel a képességgel a Kafka-témakörökhöz hasonlóan tud kommunikálni az Event hubokkal a protokoll-ügyfelek módosítása vagy a saját fürtök futtatása nélkül. A Event Hubs a Apache Kafka 1,0-es és újabb [verzióit](https://kafka.apache.org/10/documentation.html) támogatja. További információ: [Event Hubs használata Apache Kafka alkalmazásokból](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ha eseményközpontot szeretne létrehozni egy névtérben, végezze el a következő műveleteket:

1. A Event Hubs névtér lapon válassza a bal oldali menüben a **Event Hubs** lehetőséget.
1. Az ablak tetején kattintson a **+ Eseményközpont** elemre.
   
    ![Eseményközpont hozzáadása – gomb](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Adja meg az Event hub nevét, majd kattintson a **Létrehozás**gombra.
   
    ![Eseményközpont létrehozása](./media/event-hubs-quickstart-portal/create-event-hub5.png)
1. Az Event hub létrehozásának állapotát a riasztásokban tekintheti meg. Az Event hub létrehozása után az az alábbi képen látható módon jelenik meg az Event hubok listájában:

    ![Az Event hub létrehozva](./media/event-hubs-quickstart-portal/event-hub-created.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy erőforráscsoportot, egy Event Hubs-névteret és egy eseményközpontot hozott létre. Az események küldése az Event hub-tól (vagy) események fogadására vonatkozó részletes utasításokért lásd a **küldési és fogadási események** oktatóanyagokat: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Ugrás](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak fogadás)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
