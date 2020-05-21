---
title: 'Rövid útmutató: az első statikus webalkalmazás felépítése az Azure statikus Web Apps'
description: Ismerje meg, hogyan hozhat létre egy Azure statikus Web Apps példányt az előnyben részesített kezelőfelületi keretrendszerrel.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 8de6e9cff8149423f4b00b07e3113be6606181b5
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714236"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Rövid útmutató: az első statikus webalkalmazás felépítése

Az Azure statikus Web Apps egy GitHub-tárházból származó alkalmazások létrehozásával tesz közzé webhelyeket az éles környezetben. Ebben a rövid útmutatóban egy GitHub-tárházból egy webalkalmazást hoz létre az előnyben részesített kezelőfelületi keretrendszer használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com) -fiók
- [Azure](https://portal.azure.com) -fiók

## <a name="create-a-repository"></a>Adattár létrehozása

Ez a cikk a GitHub-sablonok tárházait használja, így egyszerűen létrehozhat egy új tárházat. A sablonok olyan alapszintű alkalmazásokat is tartalmaz, amelyek különböző előtér-keretrendszerekkel lettek felépítve.

# <a name="angular"></a>[Angular](#tab/angular)

- Új tárház létrehozásához navigáljon a következő helyre
  - https://github.com/staticwebdev/angular-basic/generate
- Nevezze el az adattárat **az első-statikus-Web-App**

# <a name="react"></a>[Reagálni](#tab/react)

- Új tárház létrehozásához navigáljon a következő helyre
  - https://github.com/staticwebdev/react-basic/generate
- Nevezze el az adattárat **az első-statikus-Web-App**

# <a name="vue"></a>[Vue](#tab/vue)

- Új tárház létrehozásához navigáljon a következő helyre
  - https://github.com/staticwebdev/vue-basic/generate
- Nevezze el az adattárat **az első-statikus-Web-App**

# <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

- Új tárház létrehozásához navigáljon a következő helyre
  - https://github.com/staticwebdev/vanilla-basic/generate
- Nevezze el az adattárat **az első-statikus-Web-App**

> [!NOTE]
> Az Azure statikus Web Apps egy webalkalmazás létrehozásához legalább egy HTML-fájl szükséges. Az ebben a lépésben létrehozott tárház egyetlen _index. html_ fájlt tartalmaz.

---

Kattintson a **Tárház létrehozása sablonból** gombra.

:::image type="content" source="media/getting-started/create-template.png" alt-text="Tárház létrehozása sablonból":::

## <a name="create-a-static-web-app"></a>Statikus Webalkalmazás létrehozása

Most, hogy létrejött az adattár, létrehozhat egy statikus webalkalmazást a Azure Portal.

- Lépjen az [Azure Portalra](https://portal.azure.com)
- Kattintson **az erőforrás létrehozása** elemre.
- **Statikus Web Apps** keresése
- Kattintson a **statikus Web Apps (előzetes verzió)** elemre.
- Kattintson a **Létrehozás** gombra

### <a name="basics"></a>Alapvető beállítások

Először konfigurálja az új alkalmazást, és csatolja azt egy GitHub-tárházhoz.

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="Alapbeállítások lap":::

- Azure- _előfizetés_ kiválasztása
- Válasszon ki vagy hozzon létre egy új _erőforráscsoportot_
- Nevezze el az alkalmazás **az első-statikus-Web-App**nevet.
  - Érvényes karakterek `a-z` (kis-és nagybetűk megkülönböztetése), `0-9` és `-` .
- Válasszon egy Önhöz legközelebb eső _régiót_
- Válassza ki az **ingyenes** _SKU_ -t
- Kattintson a **Bejelentkezés a GitHub** használatával gombra, és hitelesítés a GitHub használatával

Miután bejelentkezett a GitHubba, adja meg a tárház adatait.

:::image type="content" source="media/getting-started/repository-details.png" alt-text="Adattár részletei":::

- Válassza ki az előnyben részesített _szervezetet_
- Válassza a **saját-első-web-static-app** elemet az _adattár_ legördülő menüből.
- Válassza ki a **főkiszolgálót** az _ág_ legördülő menüből
- Kattintson a **következőre: build >** gombra a Build konfigurációjának szerkesztéséhez

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="Következő Build gomb":::

### <a name="build"></a>Felépítés

Ezután adja hozzá az előnyben részesített kezelőfelületi keretrendszerhez tartozó konfigurációs adatokat.

# <a name="angular"></a>[Angular](#tab/angular)

- Adja meg **/** az _alkalmazás helye_ mezőt
- Az alapértelmezett érték törlése az _API helye_ mezőben
- Az alkalmazás-összetevő _helye_ mezőben adja meg a **dist/szögletes – Basic** értéket.

# <a name="react"></a>[Reagálni](#tab/react)

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

Kattintson az **Áttekintés + létrehozás** gombra.

:::image type="content" source="media/getting-started/review-create.png" alt-text="A létrehozás gomb áttekintése":::

Ha módosítani szeretné ezeket az értékeket az alkalmazás létrehozása után, akkor szerkesztheti a [munkafolyamat-fájlt](github-actions-workflow.md).

### <a name="review--create"></a>Felülvizsgálat + létrehozás

A kérés érvényesítése után továbbra is létrehozhatja az alkalmazást.

Kattintson a **Létrehozás** gombra

:::image type="content" source="media/getting-started/create-button.png" alt-text="Létrehozás gomb":::

Az erőforrás létrehozása után kattintson az **Ugrás erőforrásra** gombra.

:::image type="content" source="media/getting-started/resource-button.png" alt-text="Ugrás az erőforrás gombra":::

## <a name="view-the-website"></a>Webhely megtekintése

A statikus alkalmazások üzembe helyezésének két aspektusa van. Az első a mögöttes Azure-erőforrásokat hozza létre az alkalmazásból. A második egy GitHub-műveletek munkafolyamata, amely felépíti és közzéteszi az alkalmazást.

Az új statikus helyhez való csatlakozás előtt az üzembe helyezési buildnek először futnia kell.

A statikus Web Apps áttekintése ablak a webalkalmazással való interakciót segítő hivatkozásokat jelenít meg.

:::image type="content" source="media/getting-started/overview-window.png" alt-text="Áttekintő ablak":::

1. Ha a szalagcímre kattint, a "kattintson ide a GitHub-műveletek futtatásának állapotának vizsgálatához" gombra kattintva megtekintheti a tárházon futó GitHub-műveleteket. Miután meggyőződött arról, hogy befejeződött a telepítési feladatok befejezése, a generált URL-címen keresztül megnyithatja a webhelyet.

2. Miután befejeződött a GitHub-műveletek munkafolyamata, az _URL_ hivatkozásra kattintva megnyithatja a webhelyet az új lapon.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha nem folytatja az alkalmazás használatát, az alábbi lépésekkel törölheti az Azure statikus Web Apps példányát:

1. A [Azure Portal](https://portal.azure.com) megnyitása
1. Keresés az **én-első-web-static-app** a felső keresési sávon
1. Kattintson az alkalmazás nevére
1. Kattintson a **Törlés** gombra
1. A törlési művelet megerősítéséhez kattintson az **Igen** gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
