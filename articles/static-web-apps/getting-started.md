---
title: 'Rövid útmutató: az első statikus webalkalmazás felépítése az Azure statikus Web Apps'
description: Ismerje meg, hogyan hozhat létre Azure statikus Web Apps webhelyeket.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: db3836e6171d187539b8615efcb5ab782c368020
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88752391"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Rövid útmutató: az első statikus webalkalmazás felépítése

Az Azure statikus Web Apps egy GitHub-tárházból származó alkalmazások létrehozásával tesz közzé webhelyeket az éles környezetben. Ebben a rövid útmutatóban egy webalkalmazást helyez üzembe az Azure statikus Web Apps szolgáltatásban a Visual Studio Code bővítménnyel.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com)-fiók
- [Azure](https://portal.azure.com) -fiók
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure statikus Web Apps-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ezután nyissa meg a Visual Studio Code-ot, és nyissa meg a **fájl > megnyitása mappát** , ahol megnyithatja azt a tárházat, amelyet az imént klónozott a gépre a szerkesztőben.

## <a name="create-a-static-web-app"></a>Statikus Webalkalmazás létrehozása

1. Az Azure-bővítmények ablak megnyitásához a Visual Studio Code-ban kattintson a Tevékenység sávon az Azure-emblémára.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-embléma":::

    > [!NOTE]
    > Be kell jelentkeznie az Azure-ba és a GitHubra. Ha még nincs bejelentkezve az Azure-ba és a GitHubra a Visual Studio Code-ból, a bővítmény arra fogja kérni, hogy a létrehozás folyamata során mindkét szolgáltatásba jelentkezzen be.

1. Vigye az egeret a _Static Web Apps_ címke fölé, és kattintson a **plusz jelre**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Azure-embléma":::

1. A szájpadlás parancs a szerkesztő tetején nyílik meg, és rákérdez az alkalmazás nevének megadására.

    Írja be **az én-First-static-Web-App** értéket, és nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Azure-embléma":::

1. Válassza ki a **fő** ágat, majd nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Azure-embléma":::

1. Válassza ki az **/** alkalmazás kódjának helyét, majd nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Azure-embléma":::

1. A bővítmény az API helyét keresi az alkalmazásban. Ez a cikk nem valósít meg API-t.

    Válassza a **Kihagyás most** lehetőséget, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Azure-embléma":::

1. Válassza ki azt a helyet, ahol a fájlok elkészülhetnek az alkalmazásbeli üzemeléshez.

    # <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

    Törölje a mezőt, majd nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Azure-embléma":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Írja be a **dist/szögletes-Basic** értéket, majd nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Azure-embléma":::

    # <a name="react"></a>[React](#tab/react)

    Írja be a **build** szót, majd nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Azure-embléma":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Írja be a **dist** szót, majd nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Azure-embléma":::

    ---

1. Válassza ki a legközelebbi helyet, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Azure-embléma":::

1. Az alkalmazás létrehozása után megerősítő értesítés jelenik meg a Visual Studio Code-ban.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Azure-embléma":::

1. A Visual Studio Code Explorer ablakában térjen vissza a _statikus Web Apps_ szakaszra, majd kattintson a jobb gombbal az **éles** környezetben, és válassza a **Megnyitás a portálon** lehetőséget a Azure Portal alkalmazás megtekintéséhez.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Azure-embléma":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölheti az Azure statikus Web Apps példányát a bővítmény használatával.

A Visual Studio Code Explorer ablakban térjen vissza a _statikus Web Apps_ szakaszhoz, és kattintson a jobb gombbal a **My-First-static-Web-App** elemre, és válassza a **Törlés**lehetőséget.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Azure-embléma":::

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
