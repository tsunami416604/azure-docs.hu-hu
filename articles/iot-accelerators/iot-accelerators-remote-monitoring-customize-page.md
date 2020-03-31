---
title: Lap hozzáadása a távfigyelési megoldás felhasználói felületéhez – Azure | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan vehet fel új lapot a távfigyelési megoldásgyorsító webes felhasználói felületére.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 0228f317e2d3380f2387dd557a27203eb3abc4ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240264"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni lap hozzáadása a távfigyelési megoldásgyorsító webes felhasználói felületéhez

Ez a cikk bemutatja, hogyan vehet fel új lapot a távfigyelési megoldásgyorsító webes felhasználói felületére. A cikk a következőket írja le:

- Hogyan készítsünk egy helyi fejlesztési környezetben.
- Új lap hozzáadása a webes felhasználói felülethez?

Az egyéb útmutatók kiterjesztik ezt a forgatókönyvet, hogy további funkciókat adjanak a hozzáadott laphoz.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek végrehajtásához a következő szoftverre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Helyi fejlesztési környezet előkészítése a felhasználói felületszámára

A Távoli figyelési megoldásgyorsító felhasználói felületi kódja a [React](https://reactjs.org/) JavaScript keretrendszer használatával valósul meg. A forráskódot a [Távoli figyelés WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-tárházban találja.

A felhasználói felület módosításait a helyi fejlesztői gépen futtathatja. Szükség esetén a helyi másolat csatlakozhat a megoldásgyorsító egy üzembe helyezett példányához, hogy az kommunikáljon a valós vagy szimulált eszközökkel.

A helyi fejlesztői környezet előkészítéséhez a Git segítségével klónozza a [távoli figyelési webui](https://github.com/Azure/pcs-remote-monitoring-webui) tárházat a helyi számítógépre:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Oldal hozzáadása

Ha lapot szeretne hozzáadni a webes felhasználói felülethez, hozzá kell adnia a lapot meghatározó forrásfájlokat, és módosítania kell néhány meglévő fájlt, hogy a webes felhasználói felület tisztában legyen az új lappal.

### <a name="add-the-new-files-that-define-the-page"></a>A lapot meghatározó új fájlok hozzáadása

A kezdéshez az **src/walkthrough/components/pages/basicPage** mappa négy fájlt tartalmaz, amelyek egy egyszerű oldalt határoznak meg:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Hozzon létre egy új mappát **src / alkatrészek / oldalak / példa,** és másolja ezt a négy fájlt bele.

### <a name="add-the-new-page-to-the-web-ui"></a>Az új lap hozzáadása a webes felhasználói felülethez

Ha az új lapot fel szeretné felvenni a webes felhasználói felületre, hajtsa végre a következő módosításokat a meglévő fájlokon:

1. Adja hozzá az új laptárolót az **src/components/pages/index.js** fájlhoz:

    ```js
    export * from './example/basicPage.container';
    ```

1. (Nem kötelező)  SVG ikon hozzáadása az új oldalhoz. További információ: [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Meglévő SVG-fájl is használható.

1. Adja hozzá a lap nevét a fordítási fájlhoz, **nyilvános/területi/en/translations.json**. A webes felhasználói felület az [i18next-et](https://www.i18next.com/) használja a nemzetközivé váláshoz.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Nyissa meg a legfelső szintű alkalmazáslapot meghatározó **src/components/app.js** fájlt. Adja hozzá az új lapot az importlistához:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Ugyanebben a fájlban adja hozzá `pagesConfig` az új lapot a tömbhöz. Állítsa `to` be az útvonal címét, hivatkozzon az SVG ikonra és fordításokra, és állítsa be az `component` oldal tárolóját:

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

1. Adjon hozzá új zsemlemorzsát a `crumbsConfig` tömbhöz:

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

    Ebben a példában az oldal csak egy morzsával rendelkezik, de néhány oldal nak több is lehet.

Mentse az összes módosítást. Készen áll a webes felhasználói felület futtatására az új oldal hozzáadásával.

### <a name="test-the-new-page"></a>Az új oldal tesztelése

A parancssorban keresse meg a tárház helyi példányának gyökerét, és futtassa a következő parancsokat a szükséges könyvtárak telepítéséhez és a webes felhasználói felület helyi futtatásához:

```cmd/sh
npm install
npm start
```

Az előző parancs helyileg futtatja a felhasználói felületet a ban. [http://localhost:3000/dashboard](http://localhost:3000/dashboard)

Anélkül, hogy a webes felhasználói felület helyi példányát a megoldásgyorsító egy telepített példányához csatlakoztatja, hibák jelennek meg az irányítópulton. Ezek a hibák nincsenek hatással az új oldal tesztelésére.

Most már szerkesztheti a kódot, miközben a hely helyileg fut, és dinamikusan láthatja a webes felhasználói felület frissítését.

## <a name="optional-connect-to-deployed-instance"></a>[Nem kötelező] Csatlakozás telepített példányhoz

Opcionálisan csatlakoztathatja a webes felhasználói felület helyi futó példányát a felhőbeli távfigyelési megoldásgyorsítóhoz:

1. Telepítse a megoldásgyorsító **alappéldányát** a **CLI pc-k** használatával. Jegyezze fel a központi telepítés nevét és a virtuális géphez megadott hitelesítő adatokat. További információ: [Deploy using the CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Az Azure Portalon vagy az [az CLI-ben](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ssh-hozzáférést engedélyezhet a megoldásban a mikroszolgáltatásokat üzemeltető virtuális géphez. Példa:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Az SSH-hozzáférés csak tesztelés és fejlesztés során engedélyezze. Ha engedélyezi az [SSH-t, a lehető leghamarabb tiltsa le újra](../security/fundamentals/network-best-practices.md).

1. Az Azure Portalon vagy az [az CLI-ben](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) keresse meg a virtuális gép nevét és nyilvános IP-címét. Példa:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Az SSH használatával az előző lépésben szereplő IP-cím és a rendszer a megoldás üzembe helyezéséhez a **pc-k** telepítésekor megadott hitelesítő adatok használatával csatlakozhat a virtuális géphez.

1. A helyi felhasználói élmény csatlakoztatásához futtassa a következő parancsokat a virtuális gép bash rendszerhéjában:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Miután látta, hogy a parancs befejeződik, és a webhely elindul, leválaszthatja a virtuális gépet.

1. A [Távoli figyelési webui](https://github.com/Azure/pcs-remote-monitoring-webui) tárház helyi példányában az **.env** fájl szerkesztésével adja hozzá a telepített megoldás URL-címét:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>További lépések

Ebben a cikkben a távoli figyelési megoldás gyorsítójában a webes felhasználói felület testreszabásához rendelkezésre álló erőforrásokról szerzett tudomást.

Most, hogy definiált egy lapot, a következő lépés az, hogy [egyéni szolgáltatást ad hozzá a távoli figyelési megoldásgyorsító webes felhasználói felületéhez,](iot-accelerators-remote-monitoring-customize-service.md) amely beolvassa a felhasználói felületen megjelenítendő adatokat.

A távfigyelési megoldásgyorsítóról a [Távoli figyelési architektúra című témakörben](iot-accelerators-remote-monitoring-sample-walkthrough.md)talál további általános tudnivalókat.
