---
title: Notification Hubs névtér árképzési szintjeinek módosítása | Microsoft Docs
description: Megtudhatja, hogyan módosíthatja egy Azure Notification Hubs-névtér díjszabási szintjét.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 51d0880b7f56d523a01fbc993993b3caf7328134
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261075"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Azure Notification hub-névtér árképzési szintjeinek módosítása
A Notification Hubs három szinten érhető el: **ingyenes**, **alapszintű**és **standard**. Ez a cikk bemutatja, hogyan módosíthatja az Azure Notification Hubs-névtér díjszabási szintjét. 

## <a name="overview"></a>Áttekintés
Az Azure Notification Hubsban a **hub** a legkisebb erőforrás/entitás. Általában egy alkalmazásra mutat, és minden olyan Platform Notification System rendelkezhet, amely az alkalmazáshoz támogatott. Az alkalmazás lehet hibrid vagy natív, valamint platformfüggetlen alkalmazás is.

A **névtér** az értesítési központok gyűjteménye. Az egyes névterek általában a kapcsolódó és adott célra használt hubokból állnak. Előfordulhat például, hogy három különböző névteret használ a fejlesztési, tesztelési és termelési célokra. 

Egy árképzési szintet a névtér szintjén rendelhet hozzá. A Notification Hubs három szintet támogat: **ingyenes**, **alapszintű**és **standard**. Használhatja a szintet az igényeinek megfelelő névtérhez. A következő részekben bemutatjuk, hogyan módosíthatja egy Notification Hubs névtér díjszabási szintjét. 

## <a name="use-azure-portal"></a>Az Azure-portál használata 
Azure Portal használatakor megváltoztathatja a névtér díjszabási szintjét a névtér oldalon vagy a hub lapon.  Ha egy központi lapon módosítja azt, akkor a névtér szintjén kell megváltoztatnia. Megváltoztatja a névtér és a névtér összes csomópontjának árképzési szintjét. 

### <a name="change-tier-on-the-namespace-page"></a>A névtér oldalának változási szintje
Az alábbi eljárás lépéseit követve módosíthatja a névtér díjszabási szintjét a névtér lapon. Amikor megváltoztatja egy névtérhez tartozó szintet, az a névtér összes hubhoz érvényes lesz.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. 
3. Válassza az **értesítési központ névterek** lehetőséget a **eszközök internetes hálózata** szakaszban. Ha a csillag (`*`) lehetőséget választja a szöveg mellett, a rendszer hozzáadja a bal oldali navigációs sávon a **Kedvencek**területen. A következő időpontokban segít a névterek oldal gyorsabb elérésében. Miután hozzáadta a Kedvencek közé, válassza az **értesítési központ névterek**lehetőséget. 

    ![Minden szolgáltatás – > értesítési központ névterei](./media/change-pricing-tier/all-services-nhub.png)
1. Az **értesítési központ névterei** lapon válassza ki azt a névteret, amelynek az árképzési szintjét módosítani kívánja. 
2. A névtérhez tartozó **értesítési központ névtér** lapján megtekintheti a névtér aktuális díjszabási szintjét az **alapvető** erőforrások szakaszban. A következő képen láthatja, hogy a névtér díjszabási szintje **ingyenes**. 

    ![Aktuális díjszabási csomag a névtér oldalon](./media/change-pricing-tier/pricing-tier-before.png)
1. A névtérhez tartozó **értesítési központ névtér** lapján válassza az **árképzési szintet** a **kezelés** szakaszban. 

    ![Válassza ki az árképzési szintet a névtér oldalon](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Módosítsa az árképzési szintet, majd kattintson a **kiválasztás** gombra.    
7. A **riasztások**között megjelenik a szintek módosítása művelet állapota. 
8. Váltson át az **Áttekintés** lapra. Ellenőrizze, hogy megjelenik-e az új **csomag az alapvető** erőforrások szakasz **díjszabási szintje** mezőjében.     
1. Ez a lépés nem kötelező. Válasszon ki egy hubot a névtérben. Győződjön meg róla, hogy ugyanazt az árképzési szintet látja az **alapvető** erőforrások szakaszban. A névtérben lévő összes hubok esetében ugyanazt az árképzési szintet kell megtekinteni. 

### <a name="change-tier-on-the-hub-page"></a>A központ oldalának változási szintje
Az alábbi eljárás lépéseit követve módosíthatja a névtér díjszabási szintjét a központ lapon. Annak ellenére, hogy ezek a lépések a központ lapról kezdődnek, a névtér és a névtérben lévő összes központ díjszabási szintjét ténylegesen megváltoztathatja. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben.
3. A **eszközök internetes hálózata** szakaszban válassza a **Notification Hubs** lehetőséget. 
4. Válassza ki az **értesítési**központot. 
5. Válassza ki a bal oldali menüben a **díjszabási szintet** . 
6. Módosítsa az árképzési szintet, majd kattintson a **kiválasztás** gombra. Ez a művelet megváltoztatja a hubot tartalmazó névtér díjszabási szintje beállítását. Így az új díjszabási szintet láthatja a névtér és az összes központ oldalain. 

## <a name="use-rest-api"></a>A REST API használata
A következő erőforrás-szolgáltató REST API-k segítségével lekérheti a jelenlegi árképzési szintet, és frissítheti azt. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Névtér aktuális díjszabási szintjeinek beolvasása
Az **aktuális névtér-rétegek**beszerzéséhez küldjön egy Get parancsot az alábbi példában látható módon: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Névtér frissítésének díjszabási szintje
**A névtér-rétegek frissítéséhez**küldjön egy Put parancsot az alábbi példában látható módon: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>Következő lépések
További információ ezekről a csomagokról és díjszabásról: [Notification Hubs díjszabása](https://azure.microsoft.com/pricing/details/notification-hubs/).
