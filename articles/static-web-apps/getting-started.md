---
title: 'Rövid útmutató: az első statikus webalkalmazás felépítése az Azure statikus Web Apps'
description: Ismerje meg, hogyan hozhat létre egy Azure statikus Web Apps példányt az előnyben részesített kezelőfelületi keretrendszerrel.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 6738f598275e91ce8a811c3ef6bcc6d5dc84e0bd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089498"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Rövid útmutató: az első statikus webalkalmazás felépítése

Az Azure Static Web Apps egy GitHub-adattárból származó alkalmazások létrehozásával tesz közzé webhelyeket egy éles környezetben. Ebben a rövid útmutatóban egy GitHub-tárházból egy webalkalmazást hoz létre az előnyben részesített kezelőfelületi keretrendszer használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com) -fiók
- [Azure](https://portal.azure.com) -fiók

## <a name="create-a-repository"></a>Adattár létrehozása

Ez a cikk a GitHub-sablonok tárházait használja, így egyszerűen létrehozhat egy új tárházat. A sablonok olyan alapszintű alkalmazásokat is tartalmaz, amelyek különböző előtér-keretrendszerekkel lettek felépítve.

# <a name="angular"></a>[Angular](#tab/angular)

- Győződjön meg arról, hogy be van jelentkezve a GitHubba, és a következő helyre navigálva hozzon létre egy új tárházat
  - https://github.com/staticwebdev/angular-basic/generate
- Nevezze el az adattárat **az első-statikus-Web-App**

# <a name="react"></a>[React](#tab/react)

- Győződjön meg arról, hogy be van jelentkezve a GitHubba, és a következő helyre navigálva hozzon létre egy új tárházat
  - https://github.com/staticwebdev/react-basic/generate
- Nevezze el az adattárat **az első-statikus-Web-App**

# <a name="vue"></a>[Vue](#tab/vue)

- Győződjön meg arról, hogy be van jelentkezve a GitHubba, és a következő helyre navigálva hozzon létre egy új tárházat
  - https://github.com/staticwebdev/vue-basic/generate
- Nevezze el az adattárat **az első-statikus-Web-App**

# <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

- Győződjön meg arról, hogy be van jelentkezve a GitHubba, és a következő helyre navigálva hozzon létre egy új tárházat
  - https://github.com/staticwebdev/vanilla-basic/generate
- Nevezze el az adattárat **az első-statikus-Web-App**

> [!NOTE]
> Az Azure statikus Web Apps egy webalkalmazás létrehozásához legalább egy HTML-fájl szükséges. Az ebben a lépésben létrehozott tárház egyetlen _index.html_ fájlt tartalmaz.

---

Kattintson a **Tárház létrehozása sablonból** gombra.

:::image type="content" source="media/getting-started/create-template.png" alt-text="Tárház létrehozása sablonból":::

## <a name="create-a-static-web-app"></a>Statikus Webalkalmazás létrehozása

Most, hogy létrejött az adattár, létrehozhat egy statikus webalkalmazást a Azure Portal.

- Navigáljon a [Azure Portal](https://portal.azure.com)
- Kattintson az **Erőforrás létrehozása** gombra
- Keressen rá a **Static Web Apps** kifejezésre
- Kattintson a **Static Web Apps (előzetes verzió)** lehetőségre
- Kattintson a **Létrehozás** gombra

### <a name="basics"></a>Alapbeállítások

Először konfigurálja az új alkalmazást, és csatolja azt egy GitHub-tárházhoz.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Alapbeállítások lap":::

- Azure- _előfizetés_ kiválasztása
- Válasszon ki vagy hozzon létre egy új _erőforráscsoportot_
- Nevezze el az alkalmazás **az első-statikus-Web-App**nevet.
  - Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.
- Válasszon egy Önhöz legközelebb eső _régiót_
- Válassza ki az **ingyenes** _SKU_ -t
- Kattintson a **Bejelentkezés GitHub-fiókkal** gombra, majd végezzen hitelesítést a GitHub-fiókkal

Miután bejelentkezett a GitHubba, adja meg a tárház adatait.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Adattár részletei":::

- Válassza ki az előnyben részesített _szervezetet_
- Válassza a **saját-első-web-static-app** elemet az _adattár_ legördülő menüből.
- Válassza ki a **főkiszolgálót** az _ág_ legördülő menüből
- Kattintson a **Tovább: Létrehozás >** gombra a létrehozási konfiguráció szerkesztéséhez

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Következő Build gomb":::

> [!NOTE]
>  Ha nem lát tárházat, lehetséges, hogy engedélyeznie kell az Azure statikus Web Apps a GitHubon. Keresse meg a GitHub-tárházat, és lépjen a **beállítások > alkalmazások > a OAuth-alkalmazások engedélyezve**lehetőségre, válassza az **Azure statikus Web Apps**lehetőséget, majd válassza a **támogatás**lehetőséget. A szervezeti adattárak esetében a szervezet tulajdonosának kell lennie az engedélyek megadásához.

### <a name="build"></a>Létrehozás

Ezután adja meg az Ön által választott előtér-keretrendszerhez tartozó konfigurációs adatokat.

# <a name="angular"></a>[Angular](#tab/angular)

- Adja meg **/** az _alkalmazás helye_ mezőt
- Az alapértelmezett érték törlése az _API helye_ mezőben
- Az alkalmazás-összetevő _helye_ mezőben adja meg a **dist/szögletes – Basic** értéket.

# <a name="react"></a>[React](#tab/react)

- Adja meg **/** az _alkalmazás helye_ mezőt
- Az alapértelmezett érték törlése az _API helye_ mezőben
- Adja meg a **Build** értéket az alkalmazás-összetevő _helye_ mezőben

# <a name="vue"></a>[Vue](#tab/vue)

- Adja meg **/** az _alkalmazás helye_ mezőt
- Az alapértelmezett érték törlése az _API helye_ mezőben
- Adja meg a **dist** értéket az alkalmazás-összetevő _helye_ mezőben

# <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

- Adja meg **/** az _alkalmazás helye_ mezőt
- Az alapértelmezett érték törlése az _API helye_ mezőben
- Az alapértelmezett érték törlése az _alkalmazás_ -összetevő hely mezőjéből

---

Kattintson az **Ellenőrzés és létrehozás** gombra.

:::image type="content" source="media/getting-started/review-create.png" alt-text="A létrehozás gomb áttekintése":::

Ha módosítani szeretné ezeket az értékeket az alkalmazás létrehozása után, akkor szerkesztheti a [munkafolyamat-fájlt](github-actions-workflow.md).

### <a name="review--create"></a>Ellenőrzés és létrehozás

A kérés érvényesítése után továbbra is létrehozhatja az alkalmazást.

Kattintson a **Létrehozás** gombra

:::image type="content" source="media/getting-started/create-button.png" alt-text="Létrehozás gomb":::

Az erőforrás létrehozása után kattintson az **Ugrás erőforrásra** gombra.

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Erőforrás megnyitása gomb":::

## <a name="view-the-website"></a>Webhely megtekintése

A statikus alkalmazások üzembe helyezésének két aspektusa van. Az első azokat a mögöttes Azure-erőforrásokat építi ki, amelyekből az alkalmazás áll. A második egy GitHub Actions-munkafolyamat, amely létrehozza és közzéteszi az alkalmazást.

Az új statikus helyhez való csatlakozás előtt az üzembe helyezési buildnek először futnia kell.

A statikus Web Apps áttekintése ablak a webalkalmazással való interakciót segítő hivatkozásokat jelenít meg.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Áttekintő ablak":::

1. Ha a szalagcímre kattint, a "kattintson ide a GitHub-műveletek futtatásának állapotának vizsgálatához" gombra kattintva megtekintheti a tárházon futó GitHub-műveleteket. Miután meggyőződött arról, hogy befejeződött a telepítési feladatok befejezése, a generált URL-címen keresztül megnyithatja a webhelyet.

2. Miután befejeződött a GitHub-műveletek munkafolyamata, az _URL_ hivatkozásra kattintva megnyithatja a webhelyet az új lapon.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha nem folytatja az alkalmazás használatát, az alábbi lépésekkel törölheti az Azure statikus Web Apps példányát:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
1. Keresés az **én-első-web-static-app** a felső keresési sávon
1. Kattintson az alkalmazás nevére
1. Kattintson a **Törlés** gombra
1. A törlési művelet megerősítéséhez kattintson az **Igen** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
