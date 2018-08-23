---
title: Az Azure event hub létrehozása |} A Microsoft Docs
description: Hozzon létre egy Azure Event Hubs-névtér és a egy eseményközpontot, az Azure portal használatával
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 5b468e1758d752cd3001c85b328d064369429499
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055316"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Hozzon létre egy Event Hubs-névtér és a egy eseményközpontot, az Azure portal használatával

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

1. Jelentkezzen be a [az Azure portal][Azure portal], és kattintson a **erőforrás létrehozása** , a képernyő bal felső.
2. Kattintson az **Eszközök internetes hálózata**, majd az **Event Hubs** lehetőségre.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. A **Névtér létrehozása** területen adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.  

4. Miután ellenőrizte, hogy a névtér neve elérhető-e, válassza ki a tarifacsomagot (Basic vagy Standard). Valamint válassza ki azt az Azure-előfizetést, erőforráscsoportot és helyet, amellyel az erőforrást létre kívánja hozni.
 
5. A névtér létrehozásához kattintson a **Létrehozás** parancsra. Lehet, hogy néhány percet várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. A névterek listájában a Portalon kattintson az újonnan létrehozott névtérre.

7. Kattintson a **Megosztott elérési szabályzatok** lehetőségre, majd a **RootManageSharedAccessKey** elemre.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Kattintson a másolás gombra, hogy a **RootManageSharedAccessKey** kapcsolati sztringet a vágólapra másolja. Mentse ezt a kapcsolati sztringet egy átmeneti helyre, például a Jegyzettömbbe, mert később szüksége lesz rá.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Az Event Hubs névtérlistájában kattintson az újonnan létrehozott névtérre.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. A névtér panelen kattintson az **Event Hubs** elemre.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. Kattintson a panel tetején **+ Event Hub**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Adjon egy nevet az eseményközpontnak, majd kattintson a **Létrehozás** elemre. 

Az eseményközpont létrehozása megtörtént, és a az események küldéséhez és fogadásához szükséges kapcsolati karakterlánccal rendelkezik.

## <a name="next-steps"></a>További lépések

Az Event Hubs kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hubs API overview](event-hubs-api-overview.md) (Event Hubs API – áttekintés)

[Azure portal]: https://portal.azure.com/