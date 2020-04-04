---
title: Értesítési központok névterének tarifacsomagjának módosítása | Microsoft dokumentumok
description: Ismerje meg, hogyan módosíthatja az Azure Notification Hubs névter ének díjcsomagját.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/02/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 855a050afa14144f8963f24398c6b7b3939ef562
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656468"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Az Azure értesítési központok névterének díjszabási szintjének módosítása

Az értesítési központok három szinten érhető el: **ingyenes**, **alapszintű**és **szabványos**. Ez a cikk bemutatja, hogyan módosíthatja az Azure Notification Hubs névtér díjszabási szintjét.

## <a name="overview"></a>Áttekintés

Az Azure Notification Hubs,a hub a legkisebb erőforrás/entitás. Általában egy alkalmazáshoz van rendelve, és minden egyes platformértesítési rendszerhez (PNS) egy tanúsítványt tud tárolni, amelyet támogatunk az alkalmazáshoz. Az alkalmazás lehet egy hibrid, vagy egy natív és egy platformfüggetlen alkalmazás.

A **névtér** értesítési központok gyűjteménye. Minden névtér általában egy adott célra kapcsolódó és használt elosztókból áll. Például három különböző névtér fejlesztési, tesztelési és éles célokra, illetve.

A névteret az **ingyenes,** **alapszintű**vagy **standard** tarifacsomagokkal társíthatja. A réteget olyan névtérhez használhatja, amely megfelel az igényeinek. A következő szakaszok bemutatják, hogyan módosíthatja az értesítési központok névterének tarifacsomagját.

## <a name="use-azure-portal"></a>Az Azure Portal használata

Az Azure Portal használatakor módosíthatja a névtér díjszabási szintjét a névtér lapon vagy egy központi lapon. Ha egy központi lapon módosítja, akkor a névtér szintjén is módosítja. Módosítja a névtér és a névtér összes hubjának tarifacsomagját.

### <a name="change-tier-on-the-namespace-page"></a>Szint módosítása a névtér lapon

Az alábbi eljárás bemutatja, hogyan módosíthatja egy névtér tarifacsomagját a névtér lapon. Ha módosítja egy névtér szintjét, az a névtér összes hubjára vonatkozik.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget.**
3. Válassza **az Értesítési központ névtereit** a **dolgok internete** csoportban. Ha a szöveg melletti`*`csillagot ( ) választja, az a **KEDVENCEK**csoportbal oldali navigációs sávjára kerül. Ez segít elérni a névterek lap gyorsabb megy előre. Miután hozzáadja a KEDVENCEKhez, válassza **az Értesítési központ névterét.**

    ![Minden szolgáltatás -> Értesítési központ névterét](./media/change-pricing-tier/all-services-nhub.png)

4. Az **Értesítési központ névterei** lapon jelölje ki azt a névteret, amelynek a tarifacsomagját módosítani szeretné.
5. A névtér **értesítési központ névtér** lapján az Essentials szakaszban láthatja a névtér aktuális tarifacsomagját. **Essentials** Az alábbi képen láthatja, hogy a névtér tarifaszintje **szabad.**

    ![Aktuális tarifacsomag a névtérlapon](./media/change-pricing-tier/pricing-tier-before.png)

6. A névtér **értesítési központ névtér** lapján válassza a Kezelési csoport Díjszabási szint **csoportban** a **Tarifacsomag** lehetőséget.

    ![Tarifacsomag kiválasztása a névtér lapon](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Módosítsa a tarifacsomagot, majd kattintson a **Kijelölés** gombra.
8. A szintmódosítási művelet állapotát a **riasztásokban láthatja.**
9. Váltás az **Áttekintés** lapra. Ellenőrizze, hogy az új szint megjelenik-e az **Essentials** szakasz **Tarifaszint** mezőjéhez.
10. Ez a lépés nem kötelező. Jelöljön ki egy elosztót a névtérben. Győződjön meg arról, hogy ugyanazt a tarifacsomagot látja az **Essentials** szakaszban. A névtérben lévő összes hubhoz ugyanazt a tarifacsomagot kell látnia.

### <a name="change-tier-on-the-hub-page"></a>Szint módosítása a központi lapon

Az alábbi eljárás bemutatja, hogyan módosíthatja egy névtér tarifacsomagját a központi lapon. Annak ellenére, hogy ezeket a lépéseket a központi lapról kezdve, ténylegesen módosíthatja a névtér és a névtér összes hubja tarifacsomagját.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget.**
3. Válassza az **Értesítési központok lehetőséget** a **dolgok internete** csoportban.
4. Válassza ki az értesítési **központot.**
5. A bal oldali menüben válassza a **Tarifacsomag** lehetőséget.
6. Módosítsa a tarifacsomagot, és kattintson a **Kijelölés** gombra. Ez a művelet módosítja a hubot tartalmazó névtér tarifacsomag-beállítását. Így láthatja az új tarifacsomagot a névtér lapon és az összes központi lapon.

> [!NOTE]
> Minden tarifacsomag-módosítás azonnal hatályba lép.

## <a name="use-rest-api"></a>A REST API használata

A következő erőforrás-szolgáltató REST API-k segítségével lejuthat az aktuális tarifacsomaglevételére és frissítésére.

### <a name="get-current-pricing-tier-for-a-namespace"></a>A névtér aktuális tarifacsomagjának beszereznie

Az aktuális névtérszint levételéhez küldjön egy GET parancsot, ahogy az a következő példában látható:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Névtér tarifacsomagjának frissítése

A névtérszint frissítéséhez küldjön egy PUT parancsot, ahogy az a következő példában látható:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>További lépések

Ezekről a szintekről és az árképzésről az [Értesítési központok díjszabása](https://azure.microsoft.com/pricing/details/notification-hubs/)című témakörben talál további információt.
