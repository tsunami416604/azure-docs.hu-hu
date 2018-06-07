---
title: Hozzon létre egy Azure event hubs |} Microsoft Docs
description: Hozzon létre egy Azure Event Hubs névtér és egy eseményközpontot, az Azure portál használatával
services: event-hubs
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: sethm
ms.openlocfilehash: 9b466d4e727c1511ca2318c0da3ec2807a965a5d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625542"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Hozzon létre egy Event Hubs névtér és egy eseményközpontot, az Azure portál használatával

## <a name="create-an-event-hubs-namespace"></a>Az Event Hubs-névtér létrehozása

1. Jelentkezzen be a [Azure-portálon][Azure portal], és kattintson a **hozzon létre egy erőforrást** , a képernyő bal felső.
2. Kattintson a **az eszközök internetes hálózatát**, és kattintson a **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. A **névtér létrehozása**, adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.  

4. Miután ellenőrizte, hogy a névtér neve elérhető-e, válassza ki a tarifacsomagot (Basic vagy Standard). Valamint válassza ki azt az Azure-előfizetést, erőforráscsoportot és helyet, amellyel az erőforrást létre kívánja hozni.
 
5. A névtér létrehozásához kattintson a **Létrehozás** parancsra. Lehet, hogy néhány percet várnia kell, amíg a rendszer teljes mértékben kiépíti az erőforrásokat.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. A portál névterek listája kattintson az újonnan létrehozott névtérre.

7. Kattintson a **megosztott elérési házirendek**, és kattintson a **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. Kattintson a másolás gombra, hogy a **RootManageSharedAccessKey** kapcsolati sztringet a vágólapra másolja. Mentse ezt a kapcsolati karakterláncot egy ideiglenes helyre, például a Jegyzettömbben későbbi használat céljából.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Az Event Hubs névtér, kattintson az újonnan létrehozott névtér.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. A névtér panelen kattintson az **Event Hubs** elemre.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. Kattintson a panel tetején **+ Eseményközpont**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. Adja meg az eseményközpont nevét, majd kattintson az **létrehozása**. 

Az eseményközpont megtörtént, és az események küldéséhez és fogadásához szükséges kapcsolati karakterlánccal rendelkezik.

## <a name="next-steps"></a>További lépések

Az Event Hubs kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hubs API overview](event-hubs-api-overview.md) (Event Hubs API – áttekintés)

[Azure portal]: https://portal.azure.com/