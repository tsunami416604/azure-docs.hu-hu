---
title: 'Gyors útmutató: az első statikus webalkalmazás felépítése az Azure-beli statikus Web Apps az Azure CLI használatával'
description: Ismerje meg, hogyan hozhat létre Azure statikus Web Apps példányt az Azure statikus Web Apps parancssori felülettel.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 7e0fdbc50dd36e4ea23903a5929735c1c83bd394
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88752907"
---
# <a name="quickstart-building-your-first-static-web-app-using-the-azure-cli"></a>Rövid útmutató: az első statikus webalkalmazás felépítése az Azure CLI használatával

Az Azure statikus Web Apps egy GitHub-tárházból származó alkalmazások létrehozásával tesz közzé webhelyeket az éles környezetben. Ebben a rövid útmutatóban egy webalkalmazást helyez üzembe az Azure statikus Web Apps szolgáltatásban az Azure CLI használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com)-fiók
- [GitHubos személyes hozzáférési jogkivonat](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com) -fiók
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) telepítve (2.8.0 és újabb verzió)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ezután váltson az új mappára az alábbi parancs használatával.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Statikus Webalkalmazás létrehozása

Most, hogy létrejött a tárház, létrehozhat egy statikus webalkalmazást az Azure CLI-ből.

> [!IMPORTANT]
> Győződjön meg arról, hogy a _My-First-static-Web-App_ mappában van a terminálon.

1. Jelentkezzen be az Azure CLI-be az alábbi parancs használatával.

    ```bash
    az login
    ```

1. Hozzon létre egy új statikus webalkalmazást az adattárból.

    # <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`: Cserélje le ezt az értéket egy meglévő Azure-erőforráscsoport nevére.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Cserélje le ezt az értéket a GitHub-felhasználónevére.

    - `<LOCATION>`: Cserélje le ezt az értéket a legközelebbi helyre. A lehetőségek a következők: _CentralUS_, _EastAsia_, _EastUS2_, _WestEurope_és _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Cserélje le ezt az értéket a korábban létrehozott [GitHub személyes hozzáférési tokenre](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) .

    Most már megtekintheti a létrehozott alkalmazást az Azure-ban.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. Keresse meg a **saját-első-web-static-app** elemet a felső keresési sávon.

1. Válassza **a saját-első-web-static-app**elemet.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, az alábbi parancs futtatásával törölheti az Azure statikus Web Apps példányát:

```bash
az staticwebapp delete \
    --name my-first-static-web-app
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
