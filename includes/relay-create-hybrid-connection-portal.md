---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: ceb2626a43ed44338bb0faad475ae2333af2de9e
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572229"
---
Ellenőrizze, hogy már [létrehozott-e Relay-névteret][namespace-how-to].

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **Minden erőforrás** elemet.
3. Válassza ki azt a névteret, ahol a hibrid kapcsolatot létre szeretné hozni. Ebben az esetben ez a **mynewns**.  
4. A **Relay-névtér** alatt válassza ki a **Hibrid kapcsolatok** lehetőséget.

    ![Hibrid kapcsolat létrehozása](./media/relay-create-hybrid-connection-portal/create-hc-1.png)

5. A névtér áttekintési ablakában válassza ki a **+ Hibrid kapcsolat** elemet
   
    ![Hibrid kapcsolat kiválasztása](./media/relay-create-hybrid-connection-portal/create-hc-2.png)
6. A **Hibrid kapcsolat létrehozása** alatt adjon meg egy értéket a hibrid kapcsolat neveként. A többi alapértelmezett értéket ne módosítsa.
   
    ![Új kiválasztása](./media/relay-create-hybrid-connection-portal/create-hc-3.png)
7. Kattintson a **Létrehozás** gombra.

[namespace-how-to]: ../articles/service-bus-relay/relay-create-namespace-portal.md 