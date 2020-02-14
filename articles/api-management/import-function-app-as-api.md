---
title: Azure-függvényalkalmazás importálása API-ként API Management
titleSuffix: Azure API Management
description: Ez az oktatóanyag bemutatja, hogyan lehet az Azure API Management használatával Azure-függvényalkalmazásokat API-ként importálni.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: apimpm
ms.openlocfilehash: cec1d3e07800dd3093ca79a87cafcf5fceafbf2f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209188"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Azure-függvényalkalmazás importálása API-ként az Azure API Management használatával

Az Azure API Management az Azure-függvényalkalmazások új API-ként való importálását vagy a létező API-khoz való hozzáfűzését támogatja. A folyamat automatikusan létrehoz egy gazdagépkulcsot az Azure-függvényalkalmazásban, amelyet aztán az Azure API Management hozzárendel egy névvel ellátott értékhez.

Ez a cikk bemutatja az Azure-függvényalkalmazás importálását API-ként az Azure API Management használatával. Ezen kívül a tesztelési folyamatot is ismerteti.

Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> * Azure-függvényalkalmazás importálása API-ként
> * Azure-függvényalkalmazás hozzáfűzése egy API-hoz
> * Az új Azure-függvényalkalmazás gazdagépkulcsának és az Azure API Management névvel ellátott értékének megtekintése
> * Az API tesztelése az Azure Portalon
> * Az API tesztelése a fejlesztői portálon

## <a name="prerequisites"></a>Előfeltételek

* Tekintse át az [Azure API Management-példány létrehozását](get-started-create-service-instance.md) ismertető rövid útmutatót.
* Győződjön meg arról, hogy az előfizetése tartalmaz egy Azure Functions-alkalmazást. További információkért lásd az [Azure-függvényalkalmazás létrehozását](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) ismertető cikket. Tartalmaznia kell egy HTTP-triggerrel ellátott függvényt, és az engedélyszintnek *Névtelenre* vagy *Függvényre* állítva kell lennie.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-new-api-from-azure-function-app"></a> Azure-függvényalkalmazás importálása új API-ként

Kövesse az alábbi lépéseket egy új API létrehozásához egy Azure-függvényalkalmazásból.

1. Az **Azure API Management** szolgáltatáspéldányban válassza az **API-k** lehetőséget a bal oldali menüből.

2. Az **Új API hozzáadása** listából válassza ki a **Függvényalkalmazást**.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-01.png)

3. Kattintson a **Tallózás** gombra az importálni kívánt függvények kiválasztásához.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-02.png)

4. Kattintson a **Függvényalkalmazás** szakaszra, hogy választhasson az elérhető függvényalkalmazások közül.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-03.png)

5. Keresse meg azt a függvényalkalmazást, amelyből függvényeket szeretne importálni, kattintson rá, majd nyomja le a **Kiválasztás** gombot.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-04.png)

6. Válassza ki a függvényeket, amelyeket importálni szeretne, majd kattintson a **Kiválasztás** gombra.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Kizárólag olyan függvényeket importálhat, amelyek rendelkeznek HTTP-triggerrel, és az engedélyszintjük *Névtelenre* vagy *Függvényre* van állítva.

7. Váltson **teljes** nézetre, és rendelje hozzá a **terméket** az új API-hoz. Szerkessze az előre kitöltött mezőket, ha szükséges.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-06.png)

8. Kattintson a  **Create** (Létrehozás) gombra.

## <a name="append-azure-function-app-to-api"></a> Azure-függvényalkalmazás hozzáfűzése egy már létező API-hoz

Kövesse az alábbi lépéseket, hogy egy Azure-függvényalkalmazást hozzáfűzzön egy már létező API-hoz.

1. Az **Azure API Management** szolgáltatáspéldányban válassza az **API-k** lehetőséget a bal oldali menüből.

2. Válassza ki az API-t, amelyet az Azure-függvényalkalmazásba szeretne importálni. Kattintson a **...** elemre, majd válassza ki az **Importálás** elemet a helyi menüből.

    ![Hozzáfűzés függvényalkalmazásból](./media/import-function-app-as-api/append-01.png)

3. Kattintson a **Függvényalkalmazás** csempére.

    ![Hozzáfűzés függvényalkalmazásból](./media/import-function-app-as-api/append-02.png)

4. Az előugró ablakban kattintson a **Tallózás** gombra.

    ![Hozzáfűzés függvényalkalmazásból](./media/import-function-app-as-api/append-03.png)

5. Kattintson a **Függvényalkalmazás** szakaszra, hogy választhasson az elérhető függvényalkalmazások közül.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-03.png)

6. Keresse meg azt a függvényalkalmazást, amelyből függvényeket szeretne importálni, kattintson rá, majd nyomja le a **Kiválasztás** gombot.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-04.png)

7. Válassza ki a függvényeket, amelyeket importálni szeretne, majd kattintson a **Kiválasztás** gombra.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/add-05.png)

8. Kattintson az **Importálás** gombra.

    ![Hozzáfűzés függvényalkalmazásból](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a>Engedély

Az Azure-függvényalkalmazás importáláskor automatikusan létrehozza a következőket:

* A függvényalkalmazáson belüli APIM-{az*Azure API Management szolgáltatási példányának neve*} nevű gazdagép kulcsa,
* Megnevezett érték az Azure API Management példányban, amelynek neve {az*azure függvényalkalmazás példánynév*} – kulcs, amely a létrehozott gazdagép kulcsát tartalmazza.

A 2019. április 4. után létrehozott API-k esetében a gazdagép kulcsát a rendszer a API Management lévő HTTP-kérésekben továbbítja a fejlécben található függvényalkalmazás. A régebbi API-k [lekérdezési paraméterként](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization)adják át a gazdagép kulcsát. Ez a viselkedés a függvényalkalmazáshoz társított *háttér* -entitás `PATCH Backend` [REST API hívásával](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend/update#backendcredentialscontract) módosítható.

> [!WARNING]
> Az új Azure-függvényalkalmazás gazdagépkulcsának vagy az Azure API Management névvel ellátott értékének eltávolítása vagy megváltoztatása megszakítja a szolgáltatások közti kommunikációt. Az értékek nem szinkronizálódnak automatikusan.
>
> Ha rotálnia kell a gazdagépkulcsot, győződjön meg arról, hogy az Azure API Management névvel ellátott értéke is megváltozik.

### <a name="access-azure-function-app-host-key"></a>Hozzáférés az Azure-függvényalkalmazás gazdagépkulcshoz

1. Nyissa meg az Azure-függvényalkalmazás példányát.

2. Válassza ki a **Függvényalkalmazás-beállítások** lehetőséget az áttekintésből.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/keys-02-a.png)

3. A kulcs a **Gazdagépkulcsok** szakaszban található.

    ![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Hozzáférés az Azure API Management névvel ellátott értékéhez

Az Azure API Management példányában válassza a **Névvel ellátott értékek** lehetőséget a bal oldali menüből. Az Azure-függvényalkalmazás kulcsa itt található.

![Hozzáadás függvényalkalmazásból](./media/import-function-app-as-api/keys-01.png)

## <a name="test-in-azure-portal"></a>Az új API tesztelése a Azure Portal

Az Azure Portalról közvetlenül meghívhat műveleteket. Az Azure Portalon kényelmesen megtekintheti és tesztelheti az API-k műveleteit.  

1. Válassza ki az előző szakaszban létrehozott API-t.

2. Kattintson a **Teszt** fülre.

3. Válasszon ki egy művelet.

    Az oldalon megjelennek a lekérdezési paraméterek és a fejlécek mezői. Az egyik fejléc az **Ocp-Apim-Subscription-Key**, amely az ehhez az API-hoz társított termék előfizetési kulcsára vonatkozik. Ha Ön hozta létre az API Management-példányt, akkor már eleve rendszergazdának számít, így a kulcsot automatikusan kitölti a rendszer. 

4. Kattintson a **Küldés** gombra.

    A háttérrendszer a **200 OK** üzenetet és néhány adatot küld válaszként.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
