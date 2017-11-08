---
title: "Hozzon létre egy Azure event hubs |} Microsoft Docs"
description: "Hozzon létre egy Azure Event Hubs névtér és egy eseményközpontot, az Azure portál használatával"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: e444c4505d4744c95e08c4ef0d33566356785c81
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Hozzon létre egy Event Hubs névtér és egy eseményközpontot, az Azure portál használatával

## <a name="create-an-event-hubs-namespace"></a>Az Event Hubs-névtér létrehozása
1. Jelentkezzen be a [Azure-portálon][Azure portal], és kattintson a **hozzon létre egy erőforrást** , a képernyő bal felső.
1. Kattintson a **az eszközök internetes hálózatát**, és kattintson a **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. A **névtér létrehozása**, adja meg a névtér nevét. A rendszer azonnal ellenőrzi, hogy a név elérhető-e.
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. Miután ellenőrizte, hogy a névtér neve elérhető-e, válassza ki a tarifacsomagot (Basic vagy Standard). Valamint válassza ki azt az Azure-előfizetést, erőforráscsoportot és helyet, amellyel az erőforrást létre kívánja hozni. 
1. A névtér létrehozásához kattintson a **Létrehozás** parancsra. Előfordulhat, hogy Várjon néhány percet, hogy a rendszer teljesen kiépíteni az erőforrásokat.
2. A portál névterek listája kattintson az újonnan létrehozott névtérre.
2. Kattintson a **megosztott elérési házirendek**, és kattintson a **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

3. Kattintson a másolás gombra, hogy a **RootManageSharedAccessKey** kapcsolati karakterláncot a vágólapra másolja. Mentse ezt a kapcsolati karakterláncot egy ideiglenes helyre, például a Jegyzettömbben későbbi használat céljából.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

1. Az Event Hubs névtér, kattintson az újonnan létrehozott névtér.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. A névtér panelen kattintson az **Event Hubs** elemre.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

1. A panel tetején kattintson az **Eseményközpont felvétele** parancsra.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. Adja meg az eseményközpont nevét, majd kattintson az **létrehozása**.
   
    ![](./media/event-hubs-create/create-event-hub5.png)

Az eseményközpont megtörtént, és az események küldéséhez és fogadásához szükséges kapcsolati karakterlánccal rendelkezik.

## <a name="next-steps"></a>Következő lépések
Az Event Hubs kapcsolatos további információkért látogasson el ezeket a hivatkozásokat:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hubs API overview](event-hubs-api-overview.md) (Event Hubs API – áttekintés)

[Azure portal]: https://portal.azure.com/