---
title: Oldal hozzáadása a távoli figyelési megoldás felhasználói felület – Azure |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá egy új lapot a távoli figyelési megoldás gyorsító webes felhasználói felületen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 95830cdffb232e16f9fbae51cfa11fbd18172c3c
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094570"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni oldal hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen

Ez a cikk bemutatja, hogyan adhat hozzá egy új lapot a távoli figyelési megoldás gyorsító webes felhasználói felületen. A cikk ismerteti:

- Hogyan készítheti elő a helyi fejlesztési környezetet.
- Hogyan lehet új oldal hozzáadása a webes felhasználói Felületére.

Egyéb útmutatók kiterjesztése ebben a forgatókönyvben a további funkciók hozzáadása az oldalhoz ad hozzá.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések végrehajtásához szüksége van a következő szoftvereknek telepítve a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>A helyi fejlesztési környezet előkészítése a felhasználói felületen

A távoli figyelési megoldásgyorsító UI kód használatával lett megvalósítva a [React](https://reactjs.org/) JavaScript-keretrendszert. Annak a forráskódot a [távoli figyelés mire](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-adattárban.

Győződjön meg arról, és a felhasználói felület módosítások teszteléséhez helyi fejlesztői gépen futtathatja azt. Szükség esetén a helyi példány csatlakozhat ahhoz, hogy kommunikálhasson a valós vagy szimulált eszközök a megoldásgyorsító üzembe helyezett példánya.

Készítse elő a helyi fejlesztési környezetét, a Git használatával klónozással készítsen a [távoli figyelés mire](https://github.com/Azure/pcs-remote-monitoring-webui) -adattár helyi számítógépre:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Oldal hozzáadása

Oldal hozzáadása a webes felhasználói felületen, szüksége adja hozzá a forrásfájlokat, amelyek meghatározzák az oldal, és néhány meglévő fájlokat, hogy a webes felhasználói felületen ismeri az új oldal módosítása.

### <a name="add-the-new-files-that-define-the-page"></a>Adja hozzá az új fájlokat, amelyek meghatározzák az oldal

Az első lépésekhez, a **src/forgatókönyv/components/oldalak/basicPage** mappa tartalmazza, amelyek meghatározzák egy egyszerű lap négy fájlok:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Hozzon létre egy új mappát **példa src/components/oldalak** , és ezeket a fájlokat másolja a fájlba.

### <a name="add-the-new-page-to-the-web-ui"></a>Az új oldal hozzáadása a webes felhasználói felületen

Az új oldal hozzáadása a webes felhasználói felületen, a következő módosításokat a meglévő fájlokat:

1. Adja hozzá az új oldal tárolót a **src/components/pages/index.js** fájlt:

    ```js
    export * from './example/basicPage.container';
    ```

1. (Nem kötelező)  Adja hozzá az új oldal SVG ikon. További információkért lásd: [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Egy meglévő SVG-fájlt is használhat.

1. Adja hozzá a fordítások fájlhoz, a lap neve **public/locales/en/translations.json**. A webes felhasználói Felületet használja [i18next](https://www.i18next.com/) a nemzetközivé.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Nyissa meg a **src/components/app.js** fájlt, amely meghatározza a legfelső szintű alkalmazás lapján. Az új oldal hozzáadása a listához import:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. Ugyanebben a fájlban adja hozzá az új oldalt a `pagesConfig` tömb. Állítsa be a `to` az útvonal-cím, az SVG ikon és a korábban hozzáadott fordítások beállításához, és állítsa be a `component` az oldal tárolóba:

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

1. Adja hozzá a bármely új webhely a `crumbsConfig` tömb:

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

    Ez a lap csak akkor van egy webhely-navigációs, de egyes oldalak is rendelkezhet.

Mentse az összes módosítást. Készen áll az új oldal hozzáadása a webes felhasználói felületének futtatásához.

### <a name="test-the-new-page"></a>Az új lap tesztelése

Egy parancssorban keresse meg a tárház helyi példányának gyökérmappájában, és futtassa az alábbi parancsokat a szükséges kódtárak telepítése és a webes felhasználói felület helyileg történő futtatása:

```cmd/sh
npm install
npm start
```

Az előző parancs futtatása helyileg, a felhasználói felület [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard).

Anélkül, hogy a megoldásgyorsító üzembe helyezett példánya a webes felhasználói felületének a helyi példányát csatlakozna, hibába ütközik, az irányítópulton. Ezek a hibák nincs hatással arra, hogy tesztelje az új oldal.

Most a kód szerkesztése közben a hely helyben fut, és tekintse meg a webes felhasználói felület dinamikusan frissülnek.

## <a name="optional-connect-to-deployed-instance"></a>[Opcionális] Csatlakozhat az üzemelő példány

Szükség esetén a webes felhasználói felületen futó helyi példánya is kapcsolódni a távoli figyelési megoldásgyorsító a felhőben:

1. Üzembe helyezése egy **alapszintű** példányának a megoldás gyorsító a **számítógépek** CLI. Jegyezze fel a nevét, valamint az üzembe helyezés, a virtuális gép a megadott hitelesítő adatok. További információkért lásd: [üzembe helyezés parancssori felületről](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Az Azure Portal vagy a [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ahhoz, hogy az a megoldás a mikroszolgáltatásokat üzemeltető virtuális gép SSH-hozzáférését. Példa:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    SSH-hozzáférés csak engedélyezze a fejlesztés és tesztelés során. Ha engedélyezi az SSH- [újra minél hamarabb tiltsa le,](../security/azure-security-network-security-best-practices.md).

1. Az Azure Portal vagy a [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nevét és a virtuális gép nyilvános IP-cím megkereséséhez. Példa:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. SSH-val csatlakozzon a virtuális géphez az IP-címet az előző lépést, és a futtatásakor a megadott hitelesítő adatok használatával **számítógépek** a megoldás üzembe helyezéséhez.

1. Ahhoz, hogy a helyi UX csatlakozni, a virtuális gépen a bash felületen futtassa az alábbi parancsokat:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Miután látja, hogy a parancs végrehajtása befejeződött, és a webhely elindul, leválaszthatja a virtuális gépről.

1. A helyi példányának a [távoli figyelés mire](https://github.com/Azure/pcs-remote-monitoring-webui) adattárban szerkesztése a **.env** fájlját, hogy a telepített megoldás URL-címe:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az erőforrások segítenek testre szabni a webes felhasználói felület a távoli figyelési megoldásgyorsító a rendelkezésére.

Most már beállított egy oldal, a következő lépés az, hogy [adjon hozzá egy egyéni szolgáltatás a távoli figyelési megoldás gyorsító webes felhasználói Felületére](iot-accelerators-remote-monitoring-customize-service.md) a felhasználói felületen megjelenítendő adatokat.

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md).
