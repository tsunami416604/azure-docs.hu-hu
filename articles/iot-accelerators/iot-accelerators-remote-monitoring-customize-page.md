---
title: Oldal hozzáadása a távoli figyelési megoldás felhasználói felületéhez – Azure | Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá új lapot a távoli figyelési megoldás webes felhasználói felületéhez.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-javascript
ms.openlocfilehash: 8101c01762749c2ceb085516936fa0c421fdb5f3
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422708"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni lap hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületéhez

Ez a cikk bemutatja, hogyan adhat hozzá új lapot a távoli figyelési megoldás webes felhasználói felületéhez. A cikk a következőket ismerteti:

- Helyi fejlesztési környezet előkészítése.
- Új lap hozzáadása a webes felhasználói felülethez.

További útmutatók: ezt a forgatókönyvet kiterjesztve további funkciókat adhat hozzá a hozzáadott laphoz.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához a következő szoftverekre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Helyi fejlesztési környezet előkészítése a felhasználói felületen

A távoli figyelési megoldás gyorsított felhasználói felületének kódja az [reagáló](https://reactjs.org/) JavaScript-keretrendszer használatával valósul meg. A forráskódot a [távoli figyelés WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-tárházában találja.

A felhasználói felületen végzett módosítások végrehajtásához és teszteléséhez futtassa azt a helyi fejlesztői gépen. Szükség esetén a helyi másolat a megoldás-gyorsító üzembe helyezett példányához is csatlakozhat, hogy az a valódi vagy szimulált eszközökkel is működjön.

A helyi fejlesztési környezet előkészítéséhez használja a git-t a [távoli monitorozási WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) adattár a helyi gépre történő klónozásához:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Oldal hozzáadása

Ha egy oldalt szeretne felvenni a webes felhasználói felületre, fel kell vennie a lapot definiáló forrásfájlokat, és módosítania kell néhány meglévő fájlt, hogy a webes KEZELŐFELÜLET tisztában legyen az új oldallal.

### <a name="add-the-new-files-that-define-the-page"></a>Adja hozzá a lapot definiáló új fájlokat

Az első lépésekhez az **src/walkthrough/Components/Pages/basicPage** mappa négy olyan fájlt tartalmaz, amelyek egy egyszerű lapot határoznak meg:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage. SCSS**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Hozzon létre egy új mappát **src/Components/Pages/example** , és másolja ezt a négy fájlt.

### <a name="add-the-new-page-to-the-web-ui"></a>Az új oldal hozzáadása a webes felhasználói felülethez

Az új oldal webes felhasználói felületen való hozzáadásához hajtsa végre a következő módosításokat a meglévő fájlokon:

1. Adja hozzá az új oldal tárolót a **src/Components/Pages/index.js** fájlhoz:

    ```js
    export * from './example/basicPage.container';
    ```

1. Választható  Adjon hozzá egy SVG-ikont az új laphoz. További információ: [WebUI/src/Utilities/readme. MD](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Meglévő SVG-fájlt is használhat.

1. Adja hozzá az oldal nevét a fordítási fájlhoz, a **nyilvános/területi beállítások/en/translations.jsa**következőn:. A webes felhasználói felület [i18next](https://www.i18next.com/) használ a honosításhoz.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Nyissa meg a legfelső szintű alkalmazás lapját meghatározó **src/Components/app.js** fájlt. Adja hozzá az új lapot az Importálások listájához:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Ugyanebben a fájlban adja hozzá az új lapot a `pagesConfig` tömbhöz. Állítsa be az `to` útvonalhoz tartozó címeket, hivatkozzon az SVG ikonjára és a korábban hozzáadott fordításokra, majd állítsa be a `component` lapot a következő tárolóra:

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Adja hozzá az új navigációs elemeket a `crumbsConfig` tömbhöz:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Ebben a példában csak egy webhely-navigációs elem van, de néhány oldal több is lehet.

Mentse az összes módosítást. Készen áll a webes felhasználói felület futtatására az új oldal hozzáadásával.

### <a name="test-the-new-page"></a>Az új oldal tesztelése

A parancssorban navigáljon a tárház helyi példányának gyökerére, és futtassa a következő parancsokat a szükséges kódtárak telepítéséhez és a webes felhasználói felület helyi futtatásához:

```cmd/sh
npm install
npm start
```

Az előző parancs helyileg futtatja a felhasználói felületet `http://localhost:3000/dashboard` .

A webes felhasználói felület helyi példányának a megoldás-gyorsító üzembe helyezett példányához való csatlakoztatása nélkül az irányítópulton hibaüzenetek jelennek meg. Ezek a hibák nem érintik az új oldal tesztelésének lehetőségét.

Mostantól szerkesztheti a kódot, miközben a hely helyileg fut, és dinamikusan megtekintheti a webes felhasználói felület frissítését.

## <a name="optional-connect-to-deployed-instance"></a>Választható Kapcsolódás központilag telepített példányhoz

Igény szerint a webes felhasználói felület helyi futó példányát a felhőben a távoli figyelési megoldáshoz kapcsolódó gyorssegédtel is összekapcsolhatja:

1. Telepítse a megoldás-gyorsító **alapszintű** példányát a **számítógépek** CLI használatával. Jegyezze fel az üzemelő példány nevét és a virtuális géphez megadott hitelesítő adatokat. További információ: [üzembe helyezés a parancssori felület használatával](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Az Azure Portal vagy az az az [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) használatával engedélyezze az SSH-hozzáférést a megoldásban a-szolgáltatásokat üzemeltető virtuális géphez. Például:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Csak a tesztelés és a fejlesztés során engedélyezze az SSH-hozzáférést. Ha engedélyezi az SSH- [t, a lehető leghamarabb le kell tiltania](../security/fundamentals/network-best-practices.md).

1. A virtuális gép nevének és nyilvános IP-címének megkereséséhez használja a Azure Portal vagy az az az [parancssori](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) felületet. Például:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Az SSH használatával csatlakozhat a virtuális géphez az előző lépésben megadott IP-cím és a **számítógépeken** a megoldás üzembe helyezése során megadott hitelesítő adatok alapján.

1. Ha engedélyezni szeretné a helyi UX-t a kapcsolódáshoz, futtassa a következő parancsokat a virtuális gép bash rendszerhéjában:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Miután a parancs befejeződik, és elindul a webhely, leválaszthatja a virtuális gépet.

1. A [távoli figyelési WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) adattárának helyi példányában szerkessze a **. env** fájlt, és adja hozzá az üzembe helyezett megoldás URL-címét:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan szabhatja testre a webes felhasználói felületet a távoli figyelési megoldás-gyorsító segítségével.

Most már definiált egy oldalt, a következő lépés [egy egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-service.md) , amely a felhasználói felületen megjelenített adatforrásokat kéri le.

A távoli figyelési megoldás-gyorsító részletes ismertetését lásd: [távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md).
