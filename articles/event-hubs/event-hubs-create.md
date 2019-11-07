---
title: Azure rövid útmutató – Event hub létrehozása a Azure Portal használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Azure-eseményközpontot az Azure Portallal, majd hogyan küldhet és fogadhat eseményeket a .NET Standard SDK használatával.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 209bdb919a505e55bdfb8868d556fa1ad08db2fe
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717943"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Rövid útmutató: Eseményközpont létrehozása az Azure Portallal
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebben a rövid útmutatóban az [Azure Portal](https://portal.azure.com) használatával hoz létre eseményközpontot.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
- [Visual Studio 2019)](https://www.visualstudio.com/vs) vagy újabb.
- A [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 2.0-s vagy újabb verziója.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport Azure-erőforrások logikai gyűjteménye. Minden erőforrás üzembe helyezése és kezelése erőforráscsoportban történik. Erőforráscsoport létrehozása:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. A bal oldali menüben válassza az **Erőforráscsoportok** lehetőséget. Ezután kattintson az **Add** (Hozzáadás) gombra.

   ![Erőforráscsoportok – Hozzáadás gomb](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Az **előfizetés**mezőben válassza ki annak az Azure-előfizetésnek a nevét, amelyben létre kívánja hozni az erőforráscsoportot.
3. Adja **meg az erőforráscsoport egyedi nevét**. A rendszer azonnal ellenőrzi, hogy a név elérhető-e az aktuálisan kiválasztott Azure-előfizetésben.
4. Válassza ki az erőforráscsoport **régióját** .
5. Válassza a **felülvizsgálat + létrehozás**lehetőséget.

   ![Erőforráscsoport – létrehozás](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget. 

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Egy Event Hubs-névtér egyedi hatókörkezelési tárolót biztosít, amelyre a teljes tartománynevével lehet hivatkozni, és ebben a tárolóban egy vagy több eseményközpontot is létrehozhat. Ha létre szeretne hozni egy névteret az erőforráscsoportban a portál használatával, végezze el az alábbi műveleteket:

1. Az Azure Portalon kattintson az **Erőforrás létrehozása** gombra a képernyő bal felső részén.
2. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget, majd válassza a **csillag (`*`)** lehetőséget a **Event Hubs** elem mellett az **elemzési** kategóriában. Győződjön meg arról, hogy a bal oldali navigációs menüben a **Event Hubs** a **Kedvencek közé** kerül. 
    
   ![Event Hubs keresése](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. A bal oldali navigációs menüben kattintson a **Kedvencek** elemre, majd a **Hozzáadás** elemre az eszköztáron válassza a **Event Hubs** lehetőséget.

   ![Hozzáadás gomb](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. A **névtér létrehozása** oldalon hajtsa végre a következő lépéseket:
    1. Adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
    2. Válassza ki az árképzési szintet (alapszintű vagy standard).
    3. Válassza ki azt az **előfizetést** , amelyben létre kívánja hozni a névteret.
    4. Válassza ki a névtér **helyét** .
    5. Kattintson a **Létrehozás** gombra. Lehet, hogy néhány percet várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat.

       ![Eseményközpont-névtér létrehozása](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Az Event hub-névtér megjelenítéséhez frissítse a **Event Hubs** lapot. Az Event hub-létrehozás állapotát a riasztások között tekintheti meg. 

    ![Eseményközpont-névtér létrehozása](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Válassza ki a névteret. Ekkor megjelenik a **Event Hubs névtér** kezdőlapja a portálon. 

   ![A névtér kezdőlapja](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Ha eseményközpontot szeretne létrehozni egy névtérben, végezze el a következő műveleteket:

1. A Event Hubs névtér lapon válassza a bal oldali menüben a **Event Hubs** lehetőséget.
1. Az ablak tetején kattintson a **+ Eseményközpont** elemre.
   
    ![Eseményközpont hozzáadása – gomb](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Adjon egy nevet az eseményközpontnak, majd kattintson a **Létrehozás** elemre.
   
    ![Eseményközpont létrehozása](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Az Event hub létrehozásának állapotát a riasztásokban tekintheti meg. Az Event hub létrehozása után az az alábbi képen látható módon jelenik meg az Event hubok listájában:

    ![Az Event hub létrehozva](./media/event-hubs-quickstart-portal/event-hub-created.png)

Gratulálunk! A Portal segítségével létrehozott egy Event Hubs-névteret és egy eseményközpontot a névtéren belül. 

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy erőforráscsoportot, egy Event Hubs-névteret és egy eseményközpontot hozott létre. Az események küldése az Event hub-tól (vagy) események fogadására vonatkozó részletes utasításokért lásd a **küldési és fogadási események** oktatóanyagokat: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET-keretrendszer](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak reecive)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
