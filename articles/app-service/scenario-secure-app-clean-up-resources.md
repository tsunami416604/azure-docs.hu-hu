---
title: Oktatóanyag – erőforrások karbantartása | Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan távolíthatja el a webalkalmazás létrehozásakor lefoglalt Azure-erőforrásokat.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: 64421dba5578b478a5fdf0c657614770baf9d735
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025361"
---
# <a name="tutorial-clean-up-resources"></a>Oktatóanyag: erőforrások törlése

Ha befejezte az ebben a többrészes oktatóanyagban szereplő összes lépést, létrehozott egy app Service-üzemeltetési csomagot és egy erőforráscsoporthoz tartozó Storage-fiókot. A Azure Active Directoryban is létrehozott egy alkalmazás-regisztrációt. Ha már nincs rá szükség, törölje ezeket az erőforrásokat és az alkalmazás regisztrációját, hogy ne folytassa a költségek felmerülésével.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Törölje az oktatóanyag után létrehozott Azure-erőforrásokat.

## <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

A [Azure Portal](https://portal.azure.com)válassza az **erőforráscsoportok** lehetőséget a portál menüjében, és válassza ki az App Service-t és az App Service-csomagot tartalmazó erőforráscsoportot.

Válassza az **erőforráscsoport törlése** lehetőséget az erőforráscsoport és az összes erőforrás törléséhez.

:::image type="content" alt-text="Az erőforráscsoport törlését bemutató képernyőkép." source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

A parancs futtatása több percet is igénybe vehet.

## <a name="delete-the-app-registration"></a>Az alkalmazás regisztrációjának törlése

A portál menüjében válassza a **Azure Active Directory**  >  **Alkalmazásregisztrációk** lehetőséget. Ezután válassza ki a létrehozott alkalmazást.
:::image type="content" alt-text="Az alkalmazás regisztrációjának kiválasztását bemutató képernyőkép." source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Az alkalmazás regisztrációjának áttekintése lapon válassza a **Törlés** lehetőséget.
:::image type="content" alt-text="Az alkalmazás regisztrációjának törlését bemutató képernyőkép." source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
>
> * Törölje az oktatóanyag után létrehozott Azure-erőforrásokat.

Megtudhatja, hogyan csatlakozhat egy [.net Core-alkalmazáshoz](tutorial-dotnetcore-sqldb-app.md), [Python-alkalmazáshoz](tutorial-python-postgresql-app.md), [Java-alkalmazáshoz](tutorial-java-spring-cosmosdb.md)vagy [Node.js-alkalmazáshoz](tutorial-nodejs-mongodb-app.md) egy adatbázishoz.