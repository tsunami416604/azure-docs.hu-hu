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
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752391"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Rövid útmutató: az első statikus webalkalmazás felépítése

Az Azure statikus Web Apps egy GitHub-tárházból származó alkalmazások létrehozásával tesz közzé webhelyeket az éles környezetben. Ebben a rövid útmutatóban egy webalkalmazást helyez üzembe az Azure statikus Web Apps szolgáltatásban a Visual Studio Code bővítménnyel.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com) -fiók
- [Azure](https://portal.azure.com) -fiók
- [Visual Studio Code](https://code.visualstudio.com)
- [Azure statikus Web Apps-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ezután nyissa meg a Visual Studio Code-ot, és nyissa meg a **fájl > megnyitása mappát** , ahol megnyithatja azt a tárházat, amelyet az imént klónozott a gépre a szerkesztőben.

## <a name="create-a-static-web-app"></a>Statikus Webalkalmazás létrehozása

1. A Visual Studio Code-ban válassza ki az Azure-emblémát a tevékenység sávján az Azure-bővítmények ablak megnyitásához.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-embléma":::

    > [!NOTE]
    > Az Azure-és a GitHub-bejelentkezés szükséges. Ha még nem jelentkezett be az Azure-ba és a GitHubot a Visual Studio Code-ból, a bővítmény arra kéri, hogy mindkét esetben jelentkezzen be a folyamatba.

1. Vigye az egérmutatót a _statikus Web Apps_ címkéjére, és válassza ki a **plusz jelet**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Alkalmazás neve":::

1. A szájpadlás parancs a szerkesztő tetején nyílik meg, és rákérdez az alkalmazás nevének megadására.

    Írja be **az én-First-static-Web-App** értéket, és nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Statikus Webalkalmazás létrehozása":::

1. Válassza ki a **fő** ágat, majd nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Ág neve":::

1. Válassza ki az **/** alkalmazás kódjának helyét, majd nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Alkalmazás kódjának helye":::

1. A bővítmény az API helyét keresi az alkalmazásban. Ez a cikk nem valósít meg API-t.

    Kattintson **a Kihagyás gombra** , majd nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API-hely":::

1. Válassza ki azt a helyet, ahol az alkalmazásban a fájlok az éles környezethez készültek.

    # <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

    Törölje a mezőt, majd nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Alkalmazás fájljainak elérési útja":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Írja be a **dist/szögletes-Basic** értéket, majd nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="A szögletes alkalmazás fájljainak elérési útja":::

    # <a name="react"></a>[React](#tab/react)

    Írja be a **Build** és az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Alkalmazás-fájlok elérési útjának megválaszolása":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Írja be a **dist** értéket, majd nyomja le az **ENTER**billentyűt

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Vue-alkalmazás fájljainak elérési útja":::

    ---

1. Válasszon ki egy legközelebbi helyet, és nyomja le az **ENTER**billentyűt.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Erőforrás helye":::

1. Az alkalmazás létrehozása után a rendszer megerősítő értesítést jelenít meg a Visual Studio Code-ban.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Megerősítés létrehozva":::

1. A Visual Studio Code Explorer ablakában térjen vissza a _statikus Web Apps_ szakaszra, majd kattintson a jobb gombbal az **éles** környezetben, és válassza a **Megnyitás a portálon** lehetőséget a Azure Portal alkalmazás megtekintéséhez.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Portál megnyitása":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölheti az Azure statikus Web Apps példányát a bővítmény használatával.

A Visual Studio Code Explorer ablakban térjen vissza a _statikus Web Apps_ szakaszhoz, és kattintson a jobb gombbal a **My-First-static-Web-App** elemre, és válassza a **Törlés**lehetőséget.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Alkalmazás törlése":::

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
