---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: mikben
ms.openlocfilehash: 90bc9def9c1a78799f4931b58ad0a661602985e3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013192"
---
## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir calling-quickstart && cd calling-quickstart
```

A futtatásával `npm init -y` **package.js** hozhat létre az alapértelmezett beállításokkal rendelkező fájlon.

```console
npm init -y
```

### <a name="install-the-package"></a>A csomag telepítése

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatásokat a JavaScript-hez készült ügyféloldali kódtár meghívásával.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Ehhez a rövid útmutatóhoz a következő webpack-verziók ajánlottak:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Ez a rövid útmutató a webpack használatával csomagolja ki az alkalmazási eszközöket. Futtassa a következő parancsot a webpack, a webpack-CLI és a webpack-dev-Server NPM csomagok telepítéséhez, és sorolja fel azokat fejlesztési függőségeiként a **package.jsban**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Hozzon létre egy **index.html** fájlt a projekt gyökérkönyvtárában. Ezt a fájlt fogjuk használni egy alapszintű elrendezés konfigurálásához, amely lehetővé teszi, hogy a felhasználó hívást helyezzen.
