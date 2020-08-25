---
title: 'Oktatóanyag: Jekyll-hely közzététele az Azure statikus Web Apps'
description: Megtudhatja, hogyan helyezhet üzembe egy Jekyll-alkalmazást az Azure statikus Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 06/08/2020
ms.author: cshoe
ms.openlocfilehash: bf1664a35562b888f9dd7aacd3b1112058bed664
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797700"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>Oktatóanyag: Jekyll-hely közzététele az Azure statikus Web Apps előzetes verziójában

Ez a cikk bemutatja, hogyan hozhat létre és helyezhet üzembe egy [Jekyll](https://jekyllrb.com/) -webalkalmazást az [azure Azure statikus Web Apps](overview.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> - Jekyll-webhely létrehozása
> - Azure statikus Web Apps beállítása
> - A Jekyll-alkalmazás üzembe helyezése az Azure-ban

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- A [Jekyll](https://jekyllrb.com/docs/installation/) telepítése
  - Használhatja a Linux Windows alrendszerét, és szükség esetén az Ubuntu-utasításokat is követheti.
- Aktív előfizetéssel rendelkező Azure-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://azure.microsoft.com/free/).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, [ingyenes fiókot hozhat létre](https://github.com/join).

## <a name="create-jekyll-app"></a>Jekyll-alkalmazás létrehozása

Jekyll-alkalmazás létrehozása a Jekyll parancssori felület (CLI) használatával:

1. A terminálon futtassa a Jekyll CLI-t egy új alkalmazás létrehozásához.

   ```bash
   jekyll new static-app
   ```

1. Navigáljon az újonnan létrehozott alkalmazáshoz.

   ```bash
   cd static-app
   ```

1. Új git-Tárház inicializálása.

   ```bash
    git init
   ```

1. Véglegesítse a módosításokat.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Az alkalmazás leküldése a GitHubra

Az Azure statikus Web Apps a GitHub használatával teszi közzé webhelyét. A következő lépések bemutatják, hogyan hozhat létre GitHub-tárházat.

1. Hozzon létre egy üres GitHub-tárházat (ne hozzon létre egy README-t) a következő [https://github.com/new](https://github.com/new) névvel: **Jekyll-Azure-static**.

1. Adja hozzá a GitHub-adattárat a helyi tárházhoz. Ügyeljen arra, hogy a GitHub-felhasználónevet a helyőrző helyén adja hozzá `<YOUR_USER_NAME>` a következő parancsban.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
   ```

1. Küldje le a helyi tárházat a GitHubra.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>A webalkalmazás üzembe helyezése

A következő lépések bemutatják, hogyan hozhat létre egy új statikus webhely-alkalmazást, és hogyan telepítheti azt éles környezetben.

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. Navigáljon a [Azure Portal](https://portal.azure.com).

1. Kattintson **az erőforrás létrehozása**elemre.

1. **Statikus Web Apps**keresése.

1. Kattintson a **statikus Web Apps (előzetes verzió)** elemre.

1. Kattintson a **Create** (Létrehozás) gombra.

1. Az **előfizetés**mezőben fogadja el a felsorolt előfizetést, vagy válasszon ki egy újat a legördülő listából.

1. Az _erőforráscsoport_területen válassza az **új**lehetőséget. Az _Új erőforráscsoport neve_mezőbe írja be a **Jekyll-static-app** nevet, majd kattintson **az OK gombra**.

1. Ezután adja meg az alkalmazás nevét a _név_ mezőben. Érvényes karakterek a következők:, `a-z` `A-Z` `0-9` és `-` .

1. A _régió_területen válasszon ki egy elérhető régiót.

1. Az _SKU_esetében válassza az **ingyenes**lehetőséget.

    :::image type="content" source="./media/publish-jekyll/basic-app-details.png" alt-text="Részletek kitöltve":::

1. Kattintson a **Bejelentkezés a GitHub** gombbal gombra.

1. Válassza ki azt a **szervezetet** , amelyben létrehozta a tárházat.

1. Válassza ki a **Jekyll-static-app** _adattárat_.

1. A _ág_ válassza a **Master**elemet.

    :::image type="content" source="./media/publish-jekyll/completed-github-info.png" alt-text="Elkészült GitHub-információ":::

### <a name="build"></a>Létrehozás

Ezután adja hozzá azokat a konfigurációs beállításokat, amelyeket a létrehozási folyamat az alkalmazás létrehozásához használ. Az alábbi beállítások a GitHub-művelet munkafolyamat-fájlját konfigurálja.

1. Kattintson a **következőre: build >** gombra a Build konfigurációjának szerkesztéséhez.

1. _Alkalmazás helyének_ beállítása a következőre: **/_site**.

1. Hagyja üresen az alkalmazás-összetevő _helyét_ .

   Az API-k _helyének_ értéke nem szükséges, mivel jelenleg nem TELEPÍTenek API-t.

### <a name="review-and-create"></a>Áttekintés és létrehozás

1. A részletek helyességének ellenőrzéséhez kattintson a **felülvizsgálat + létrehozás** gombra.

1. Kattintson a Létrehozás gombra az Azure statikus Web Apps létrehozásához, és **hozzon létre** egy GitHub-műveletet az üzembe helyezéshez.

1. A központi telepítés először is sikertelen lesz, mert a munkafolyamat-fájlnak bizonyos Jekyll-specifikus beállításokra van szüksége. A beállítások hozzáadásához navigáljon a terminálhoz, és kérje le a commit a GitHub művelettel a gépre.

   ```bash
   git pull
   ```

1. Nyissa meg a Jekyll alkalmazást egy szövegszerkesztőben, és nyissa meg a _. GitHub/munkafolyamatok/Azure-Pages-<WORKFLOW_NAME>. YML_ fájlt.

1. Sorok hozzáadása a blokk után `- uses: actions/checkout@v2` a következő konfigurációs blokkhoz.

    ```yml
    - uses: actions/checkout@v2
      with:
        submodules: true
    - name: Set up Ruby
      uses: ruby/setup-ruby@ec106b438a1ff6ff109590de34ddc62c540232e0
      with:
        ruby-version: 2.6
    - name: Install dependencies
      run: bundle install
    - name: Jekyll build
      run: jekyll build
    ```

1. Véglegesítse a frissített munkafolyamatot, és küldje el a GitHubnak.

    ```bash
    git add -A
    git commit -m "Updating GitHub Actions workflow"
    git push
    ```

1. Várjon, amíg a GitHub-művelet befejeződik.

1. A Azure Portal _Áttekintés_ ablakában kattintson az _URL_ hivatkozásra az üzembe helyezett alkalmazás megnyitásához.

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="Üzembe helyezett alkalmazás":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](custom-domain.md)
