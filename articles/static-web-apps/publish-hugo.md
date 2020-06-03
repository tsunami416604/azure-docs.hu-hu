---
title: 'Oktatóanyag: Hugo-hely közzététele az Azure statikus Web Apps'
description: Megtudhatja, hogyan helyezhet üzembe egy Hugo-alkalmazást az Azure statikus Web Appson.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 85376e1861108089cd7918b3b261f05433b59217
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84298030"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Oktatóanyag: Hugo-hely közzététele az Azure statikus Web Apps előzetes verziójában

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe egy [Hugo](https://gohugo.io/) -webalkalmazást az [azure Azure statikus Web Apps](overview.md). Az utolsó eredmény egy új Azure-beli statikus Web Apps a kapcsolódó GitHub-műveletekkel, amelyek segítségével szabályozhatja az alkalmazás felépítésének és közzétételének módját.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Hugo-alkalmazás létrehozása
> - Azure statikus Web Apps beállítása
> - A Hugo-alkalmazás üzembe helyezése az Azure-ban

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://azure.microsoft.com/free/).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://github.com/join).

## <a name="create-a-hugo-app"></a>Hugo-alkalmazás létrehozása

Hugo-alkalmazás létrehozása a Hugo parancssori felület (CLI) használatával:

1. Kövesse a Hugo [telepítési útmutatóját](https://gohugo.io/getting-started/installing/) az operációs rendszeren.

1. Terminál megnyitása

1. Futtassa a Hugo CLI-t egy új alkalmazás létrehozásához.

   ```bash
   hugo new site static-app
   ```

1. Navigáljon az újonnan létrehozott alkalmazáshoz.

   ```bash
   cd static-app
   ```

1. Egy git-Tárház inicializálása.

   ```bash
    git init
   ```

1. Ezután vegyen fel egy témát a webhelyre egy téma git-almodulként való telepítésével, majd adja meg a Hugo konfigurációs fájlban.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Véglegesítse a módosításokat.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Az alkalmazás leküldése a GitHubra

Az Azure statikus Web Appshoz való csatlakozáshoz a GitHubon egy adattárra van szükség. A következő lépések bemutatják, hogyan hozhat létre tárházat a webhelyhez.

1. Hozzon létre egy üres GitHub-tárházat (ne hozzon létre README) a [https://github.com/new](https://github.com/new) named **Hugo-static-app**névvel.

1. Adja hozzá a GitHub-adattárat a helyi tárházhoz. Ügyeljen arra, hogy a GitHub-felhasználónevet a helyőrző helyén adja hozzá `<YOUR_USER_NAME>` a következő parancsban.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Küldje le a helyi tárházat a GitHubra.

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

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Azure statikus Web Apps-erőforrás létrehozása a portálon":::

1. Az **előfizetés**mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.

1. Az _erőforráscsoport_területen válassza az **új**lehetőséget. Az _Új erőforráscsoport neve_mezőbe írja be a **Hugo-static-app** nevet, majd kattintson **az OK gombra**.

1. Ezután adja meg az alkalmazás nevét a **név** mezőben. Érvényes karakterek a következők:, `a-z` `A-Z` `0-9` és `-` .

1. A _régió_területen válasszon ki egy elérhető régiót.

1. Az _SKU_esetében válassza az **ingyenes**lehetőséget.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Részletek kitöltve":::

1. Kattintson a **Bejelentkezés a GitHub** gombbal gombra.

1. Válassza ki azt a **szervezetet** , amelyben létrehozta a tárházat.

1. Válassza ki a **Hugo-static-app** _adattárat_ .

1. A _ág_ válassza a **Master**elemet.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Elkészült GitHub-információ":::

### <a name="build"></a>Felépítés

Ezután adja hozzá azokat a konfigurációs beállításokat, amelyeket a létrehozási folyamat az alkalmazás létrehozásához használ. Az alábbi beállítások a GitHub-művelet munkafolyamat-fájlját konfigurálja.

1. Kattintson a **következőre: build >** gombra a Build konfigurációjának szerkesztéséhez

1. Az _alkalmazás helyének_ beállítása **nyilvánosra**.

1. Hagyja üresen az alkalmazás-összetevő _helyét_ .

   Az API-k _helyének_ értéke nem szükséges, mivel jelenleg nem TELEPÍTenek API-t.

### <a name="review-and-create"></a>Áttekintés és létrehozás

1. A részletek helyességének ellenőrzéséhez kattintson a **felülvizsgálat + létrehozás** gombra.

1. Kattintson a Létrehozás gombra az Azure statikus Web Apps létrehozásához, és **hozzon létre** egy GitHub-műveletet az üzembe helyezéshez.

1. Miután az üzembe helyezés befejeződött, navigáljon a terminálhoz, és a GitHub művelettel töltse le a commit a gépre.

   ```bash
   git pull
   ```

1. Nyissa meg a Hugo alkalmazást egy szövegszerkesztőben, és nyissa meg a _. GitHub/munkafolyamatok/Azure-Pages-<WORKFLOW_NAME>. YML_ fájlt.

1. A `- uses: actions/checkout@v2` Hugo-alkalmazás létrehozásához cserélje le a sort (18. sor) a következőre. Ha a Hugo Extended, a comment (Megjegyzés) lehetőségre van szüksége `extended: true` .

   ```yml
   - uses: actions/checkout@v2
     with:
       submodules: true

   - name: Setup Hugo
     uses: peaceiris/actions-hugo@v2.4.8
     with:
       hugo-version: "latest"
       # extended: true

   - name: Build
     run: hugo
   ```

1. Véglegesítse a frissített munkafolyamatot, és küldje el a GitHubnak.

   ```bash
   git add -A
   git commit -m "Updating GitHub Actions workflow"
   git push
   ```

1. Várjon, amíg a GitHub-művelet befejeződik.

1. Az újonnan létrehozott Azure statikus Web Apps erőforrás Azure Portal _áttekintése_ ablakában kattintson az _URL-_ hivatkozásra a telepített alkalmazás megnyitásához.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Üzembe helyezett alkalmazás":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](custom-domain.md)
