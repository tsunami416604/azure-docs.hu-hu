---
title: A Notification Hubs-névtér priciing csomagváltás |} A Microsoft Docs
description: Útmutató az Azure Notification Hubs-névtér tarifacsomagjának módosítása.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/28/2019
ms.author: jowargo
ms.openlocfilehash: fb84cc0a6e2fc427727fa0c50583b7f1a37e0160
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55199070"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Az Azure notification hubs-névtér tarifacsomagjának módosítása
A Notification Hubs három szinten érhető el: **ingyenes**, **alapszintű**, és **standard**. Ez a cikk bemutatja, hogyan módosíthatja a tarifacsomagot az Azure Notification Hubs-névterekhez. 

## <a name="overview"></a>Áttekintés
Az Azure Notification Hubs egy **hub** a legkisebb erőforrás entitás. Általában egy alkalmazás és is tartsa egy tanúsítványt minden Platform Notification System támogatjuk az alkalmazás számára. Az alkalmazás egy hibrid vagy egy natív és platformfüggetlen alkalmazást is lehet.

A **névtér** a notification hubs gyűjteménye. Minden névtér kapcsolódó és egy adott célra használt elosztók általában áll. Például illetve lehet fejlesztési, tesztelési és éles üzemi célú három különböző névterekben. 

A névterek szintjén tarifacsomag társíthatja. A Notification Hubs mindhárom szintet támogat: **ingyenes**, **alapszintű**, és **standard**. A réteg használhatja a névtér, amely az igényeinek. A következő szakaszok bemutatják, hogyan módosíthatja a Notification Hubs-névtér tarifacsomagját. 

## <a name="use-azure-portal"></a>Az Azure Portal használata 
Az Azure portál használata esetén a tarifacsomagot a névtér a névtér vagy egy hub lapon módosíthatja.  Ha módosít egy hub oldalon, hogy a tényleges módosítás a névterek szintjén. A névtér és a névtér összes hubs tarifacsomagja változik. 

### <a name="change-tier-on-the-namespace-page"></a>A névtér oldalon csomagváltás
Az alábbi eljárás biztosít a névtér oldalon névtér tarifacsomagjának módosításához szükséges lépések. Amikor módosítja a réteg a névtér, a névtér összes hubs vonatkozik.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali menüben. 
3. Válassza ki **értesítési központok névterei** a a **IOT-** szakaszban. Ha bejelöli a csillag (`*`) a bal oldali navigációs sáv alatt hozzáadott lekérdezi a szöveg mellett **Kedvencek**. Segít a gyorsabb és újabb verziók esetében a következő alkalommal a névterek lap elérése. Miután hozzáadta a Kedvencek közé, válassza ki a **értesítési központok névterei**. 

    ![Az összes szolgáltatás -> értesítési központok névterei](./media/change-pricing-tier/all-services-nhub.png)
1. Az a **értesítési központok névterei** lapon, válassza ki a névteret, amelynek meg szeretné változtatni a tarifacsomagot. 
2. Az a **Notification Hub Namespace** lap névteréhez, tekintse meg a névteret a jelenlegi tarifacsomagja a **Essentials** szakaszban. Az alábbi képen láthatja, hogy a névtér tarifacsomagját **ingyenes**. 

    ![Jelenlegi tarifacsomag a névtér az oldalon](./media/change-pricing-tier/pricing-tier-before.png)
1. Az a **Notification Hub Namespace** névteréhez, válassza a lap **Tarifacsomag** alatt **kezelés** szakaszban. 

    ![Válasszon tarifacsomagot a névtér az oldalon](./media/change-pricing-tier/namespace-select-pricing-menu.png)
6. Módosítsa a tarifacsomagot, majd kattintson a **kiválasztása** gombra.    
7. A réteg módosítása a művelet állapota a **riasztások**. 
8. Váltson a **áttekintése** lapot. Győződjön meg arról, hogy az új csomag jelennek meg a **Tarifacsomag** mezőbe a **Essentials** szakaszban.     
1. Ez a lépés nem kötelező. Jelölje ki bármely eseményközpont a névteret. Győződjön meg arról, hogy az azonos díjszabás díjcsomag a **Essentials** szakaszban. A névtér összes hubs ugyanabba a tarifacsomagba tartozó kell megjelennie. 

### <a name="change-tier-on-the-hub-page"></a>A hub oldalon csomagváltás
Az alábbi eljárás lehetővé teszi a hub oldalon névtér tarifacsomagjának módosításához szükséges lépések. Annak ellenére, hogy mégis ezeket a lépéseket az eseményközpontba oldal kezdve, ténylegesen módosítja a névtér és a névtér összes hubs tarifacsomagja. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali menüben.
3. Válassza ki **Notification Hubs** a a **IOT-** szakaszban. 
4. Válassza ki az értesítési **hub**. 
5. Válassza ki **Tarifacsomag** a bal oldali menüben. 
6. Módosítsa a tarifacsomagot, majd kattintson a **kiválasztása** gombra. Ez a művelet módosítja a díjszabási szint beállítása a névtér, amely tartalmazza a hubhoz. Az új tarifacsomag, így a névtér és összes központ lapon láthatja. 

## <a name="use-rest-api"></a>A REST API használata
A jelenlegi tarifacsomag lekérése, és frissítse a következő erőforrás szolgáltató REST API-k segítségével. 

### <a name="get-current-pricing-tier-for-a-namespace"></a>Jelenlegi tarifacsomag lekérése a névtér
Az első a **aktuális névtér szint**, egy GET parancs küldése, az alábbi példában látható módon: 

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Egy névtér tarifacsomagjának frissítése
A **frissíteni a névtér réteget**, küldjön egy PUT parancsot az alábbi példában látható módon: 

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```



## <a name="next-steps"></a>További lépések
Ezek a rétegek kapcsolatos további részletekért és a díjszabás, lásd: [Notification Hubs-díjszabás](https://azure.microsoft.com/pricing/details/notification-hubs/).
