---
title: 'Oktatóanyag: VuePress-webhely közzététele az Azure statikus Web Apps'
description: Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe egy VuePress-alkalmazást az Azure statikus Web Apps.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: bd9eaad0c141eda815da159e3c13d6c51f5e6200
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599738"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Oktatóanyag: VuePress-webhely közzététele az Azure statikus Web Apps előzetes verziójában

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe [VuePress](https://vuepress.vuejs.org/) -webalkalmazásokat az [azure Azure statikus Web Apps](overview.md). Az utolsó eredmény egy új Azure-beli statikus Web Apps alkalmazás, amely a kapcsolódó GitHub-műveletekkel szabályozza az alkalmazás felépítésének és közzétételének módját.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
>
> - VuePress-alkalmazás létrehozása
> - Azure statikus Web Apps beállítása
> - A VuePress alkalmazás üzembe helyezése az Azure-ban

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://azure.microsoft.com/free/).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://github.com/join).
- Telepített [Node.js](https://nodejs.org).

## <a name="create-a-vuepress-app"></a>VuePress-alkalmazás létrehozása

VuePress-alkalmazás létrehozása a parancssori felületen (CLI):

1. Hozzon létre egy új mappát a VuePress alkalmazáshoz.

   ```bash
   mkdir static-site
   ```

1. Adjon hozzá egy _readme.MD_ -fájlt a mappához.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Inicializálja a _Package. JSON_ fájlt.

   ```bash
   npm init -y
   ```

1. Adja hozzá a VuePress-t `devDependency` .

   ```bash
   npm install --save-dev vuepress
   ```

1. Nyissa meg a _Package. JSON_ fájlt egy szövegszerkesztőben, és adjon hozzá egy Build parancsot a [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) szakaszhoz.

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Hozzon létre egy _. gitignore_ fájlt a _csomóponti \_ modulok_ mappa kizárásához.

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Egy git-Tárház inicializálása.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Az alkalmazás leküldése a GitHubra

Az Azure statikus Web Appshoz való csatlakozáshoz a GitHubon egy adattárra van szükség. A következő lépések bemutatják, hogyan hozhat létre tárházat a webhelyhez.

1. Hozzon létre egy üres GitHub-tárházat (ne hozzon létre README) a [https://github.com/new](https://github.com/new) named **vuepress-static-app**névvel.

1. Adja hozzá a GitHub-adattárat a helyi tárházhoz. Ügyeljen arra, hogy a GitHub-felhasználónevet a helyőrző helyén adja hozzá `<YOUR_USER_NAME>` a következő parancsban.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Küldje le a helyi tárházat a GitHubra.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>A webalkalmazás üzembe helyezése

A következő lépések bemutatják, hogyan hozhat létre új statikus Web Apps alkalmazást, és hogyan helyezheti üzembe azt éles környezetben.

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. Lépjen az [Azure Portalra](https://portal.azure.com)
1. Kattintson **az erőforrás létrehozása** elemre.
1. **Statikus Web Apps** keresése
1. Kattintson a **statikus Web Apps (előzetes verzió)** elemre.
1. Kattintson a **Létrehozás** gombra

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Statikus Web Apps (előzetes verzió) létrehozása a portálon":::

1. Az **előfizetés**mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.

1. Az _erőforráscsoport_területen válassza az **új**lehetőséget. Az _Új erőforráscsoport neve_mezőbe írja be a **vuepress-static-app** nevet, majd kattintson **az OK gombra**.

1. Ezután adjon meg egy globálisan egyedi nevet az alkalmazásnak a **név** mezőben. Érvényes karakterek a következők:, `a-z` `A-Z` `0-9` és `-` . Ezt az értéket használja a statikus alkalmazás URL-előtagja a következő formátumban: `https://<APP_NAME>.azurestaticapps.net` .

1. A _régió_területen válasszon ki egy elérhető régiót.

1. Az _SKU_esetében válassza az **ingyenes**lehetőséget.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Részletek kitöltve":::

1. Kattintson a **Bejelentkezés a GitHub** gombbal gombra.

1. Válassza ki azt a **szervezetet** , amelyben létrehozta a tárházat.

1. Válassza ki a **vuepress-static-app** _adattárat_ .

1. A _ág_ válassza a **Master**elemet.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Elkészült GitHub-információ":::

### <a name="build"></a>Felépítés

Ezután adja hozzá azokat a konfigurációs beállításokat, amelyeket a létrehozási folyamat az alkalmazás létrehozásához használ. Az alábbi beállítások a GitHub-művelet munkafolyamat-fájlját konfigurálja.

1. Kattintson a **következőre: build >** gombra a Build konfigurációjának szerkesztéséhez

1. Az _alkalmazás helyének_ beállítása a következőre: **/** .

1. Az _alkalmazás-összetevő helyének_ beállítása **. vuepress/dist**.

Az API-k _helyének_ értéke nem szükséges, mivel jelenleg nem TELEPÍTenek API-t.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Összeállítási beállítások":::

### <a name="review-and-create"></a>Áttekintés és létrehozás

1. A részletek helyességének ellenőrzéséhez kattintson a **felülvizsgálat + létrehozás** gombra.

1. Kattintson a Létrehozás gombra az Azure statikus Web Apps létrehozásához, és **hozzon létre** egy GitHub-műveletet az üzembe helyezéshez.

1. Miután az üzembe helyezés befejeződött, kattintson az **erőforrás**elemre.

1. Az erőforrás képernyőn kattintson az _URL_ hivatkozásra az üzembe helyezett alkalmazás megnyitásához. Előfordulhat, hogy várnia kell egy percet vagy kettőt a GitHub-művelet befejeződésére.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Üzembe helyezett alkalmazás":::

### <a name="clean-up-resources"></a>Erőforrások felszabadítása

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](custom-domain.md)
