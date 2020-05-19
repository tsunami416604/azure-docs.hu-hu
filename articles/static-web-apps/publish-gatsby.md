---
title: 'Oktatóanyag: Gatsby-webhely közzététele az Azure statikus Web Apps'
description: Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe egy Gatsby-alkalmazást az Azure statikus Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 14706b623c6973c09b499e4fcd905e3eed430898
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599618"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Oktatóanyag: Gatsby-webhely közzététele az Azure statikus Web Apps előzetes verziójában

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe [Gatsby](https://gatsbyjs.org) -webalkalmazásokat az [Azure statikus Web Apps](overview.md). Az utolsó eredmény egy új statikus Web Apps-hely (a kapcsolódó GitHub-műveletekkel), amelyek segítségével szabályozhatja az alkalmazás felépítésének és közzétételének módját.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
>
> - Gatsby-alkalmazás létrehozása
> - Azure statikus Web Apps-hely beállítása
> - A Gatsby alkalmazás üzembe helyezése az Azure-ban

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://azure.microsoft.com/free/).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://github.com/join).
- Telepített [Node.js](https://nodejs.org).

## <a name="create-a-gatsby-app"></a>Gatsby-alkalmazás létrehozása

Gatsby-alkalmazás létrehozása a Gatsby parancssori felülettel (CLI):

1. Terminál megnyitása
1. Hozzon létre egy új alkalmazást a [npx](https://www.npmjs.com/package/npx) eszközzel a Gatsby CLI használatával. Ez eltarthat néhány percig.

   ```bash
   npx gatsby new static-web-app
   ```

1. Navigáljon az újonnan létrehozott alkalmazáshoz

   ```bash
   cd static-web-app
   ```

1. Git-Tárház inicializálása

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Az alkalmazás leküldése a GitHubra

Egy új Azure statikus Web Apps-erőforrás létrehozásához a GitHubon egy adattárral kell rendelkeznie.

1. Hozzon létre egy üres GitHub-tárházat (ne hozzon létre README) a [https://github.com/new](https://github.com/new) named **Gatsby-static-Web-App**paranccsal.

1. Ezután adja hozzá az imént létrehozott GitHub-tárházat a helyi tárházhoz. Ügyeljen arra, hogy a GitHub-felhasználónevet a helyőrző helyén adja hozzá `<YOUR_USER_NAME>` a következő parancsban.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. A helyi tárházat a GitHubra leküldheti.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>A webalkalmazás üzembe helyezése

A következő lépések bemutatják, hogyan hozhat létre egy új statikus webhely-alkalmazást, és hogyan telepítheti azt éles környezetben.

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. Lépjen az [Azure Portalra](https://portal.azure.com)
1. Kattintson **az erőforrás létrehozása** elemre.
1. **Statikus Web Apps** keresése
1. Kattintson a **statikus Web Apps (előzetes verzió)** elemre.
1. Kattintson a **Létrehozás** gombra

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Statikus Web Apps (előzetes verzió) létrehozása a portálon":::

1. Az _előfizetés_mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.

1. Az _erőforráscsoport_területen válassza az **új**lehetőséget. Az _Új erőforráscsoport neve_mezőbe írja be a **Gatsby-static-Web-App** nevet, majd kattintson **az OK gombra**.

1. Ezután adjon meg egy globálisan egyedi nevet az alkalmazásnak a **név** mezőben. Érvényes karakterek a következők:, `a-z` `A-Z` `0-9` és `-` . Ezt az értéket használja a statikus webalkalmazás URL-előtagja a következő formátumban: `https://<YOUR_APP_NAME>.azurestaticapps.net` .

1. A _régió_területen válasszon ki egy elérhető régiót.

1. Az _SKU_esetében válassza az **ingyenes**lehetőséget.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Részletek kitöltve":::

1. Kattintson a **Bejelentkezés a GitHub** gombbal gombra.

1. Válassza ki azt a **szervezetet** , amelyben létrehozta a tárházat.

1. Válassza ki a **Gatsby-static-Web-App** _tárat adattárként_ .

1. A _ág_ válassza a **Master**elemet.

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Elkészült GitHub-információ":::

### <a name="build"></a>Felépítés

Ezután adja hozzá a konfigurációs beállításokat, amelyeket a létrehozási folyamat az alkalmazás létrehozásához használ.

1. Kattintson a **következőre: build >** gombra a Build konfigurációjának szerkesztéséhez

1. A GitHub-műveletekben a lépés beállításainak konfigurálásához állítsa az _alkalmazás helyét_ a következőre: **/** .

1. Az _alkalmazás-összetevő helyének_ beállítása **nyilvánosra**.

   Az API-k _helyének_ értéke nem szükséges, mivel jelenleg nem TELEPÍTenek API-t.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Összeállítási beállítások":::

### <a name="review-and-create"></a>Áttekintés és létrehozás

1. A részletek helyességének ellenőrzéséhez kattintson a **felülvizsgálat + létrehozás** gombra.

1. Kattintson a Létrehozás elemre a App Service statikus webalkalmazás létrehozásának elindításához, és **hozzon létre** egy GitHub-műveletet az üzembe helyezéshez.

1. Miután az üzembe helyezés befejeződött, kattintson az **erőforrás**elemre.

1. Az erőforrás képernyőn kattintson az _URL_ hivatkozásra az üzembe helyezett alkalmazás megnyitásához. Előfordulhat, hogy várnia kell egy percet vagy kettőt a GitHub-művelet befejeződésére.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Üzembe helyezett alkalmazás":::

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](custom-domain.md)
