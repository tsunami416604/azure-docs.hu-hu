---
title: Rövid útmutató – VOIP-hívás hozzáadása egy webalkalmazáshoz az Azure kommunikációs szolgáltatásokkal
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure kommunikációs szolgáltatásokat az ügyféloldali kódtár meghívásához a Javascripthez
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: d58b4d86936c56a08f27bef59edc1d3cc4ce4617
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947679"
---
Ebből a rövid útmutatóból megtudhatja, hogyan indíthat el egy hívást az Azure kommunikációs szolgáltatásokkal a JavaScript-hez készült ügyféloldali kódtár meghívásával.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
- Aktív kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- Felhasználói hozzáférési jogkivonat a hívási ügyfél létrehozásához. Megtudhatja, hogyan [hozhat létre és kezelhet felhasználói hozzáférési jogkivonatokat](../../access-tokens.md).

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

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Ez a rövid útmutató a webpack használatával csomagolja ki az alkalmazási eszközöket. Futtassa a következő parancsot a webpack, a webpack-CLI és a webpack-dev-Server NPM csomagok telepítéséhez, és sorolja fel azokat fejlesztési függőségeiként a **package.jsban**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Hozzon létre egy **index.html** fájlt a projekt gyökérkönyvtárában. Ezt a fájlt fogjuk használni egy alapszintű elrendezés konfigurálásához, amely lehetővé teszi a felhasználó számára, hogy hívást kezdeményezzen egy Azure kommunikációs robotba.

A kód itt látható:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Hozzon létre egy fájlt **client.js** nevű projekt gyökérkönyvtárában, hogy tartalmazza az alkalmazás logikáját ehhez a rövid útmutatóhoz. Adja hozzá az alábbi kódot a hívó ügyfél importálásához és a DOM-elemekre mutató hivatkozások beszerzéséhez, hogy az üzleti logikánk is csatolható legyen. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");

// quickstart code goes here
```

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek az Azure kommunikációs szolgáltatások ügyfél-függvénytárának főbb funkcióit kezelik:

| Név                             | Leírás                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | A CallClient a hívó ügyféloldali függvénytár fő belépési pontja.                                                                       |
| CallAgent                        | A CallAgent a hívások indításához és kezeléséhez használatos.                                                                                            |
| AzureCommunicationUserCredential | A AzureCommunicationUserCredential osztály az CommunicationUserCredential felületet valósítja meg, amely a CallAgent létrehozásához használatos. |


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az `<USER_ACCESS_TOKEN>` erőforráshoz érvényes felhasználói hozzáférési tokent kell cserélnie. Ha még nem áll rendelkezésre jogkivonat, tekintse meg a [felhasználói hozzáférési jogkivonat](../../access-tokens.md) dokumentációját. A használatával `CallClient` inicializáljon egy `CallAgent` példányt egy példánnyal, `CommunicationUserCredential` amely lehetővé teszi a hívások kezdeményezését és fogadását. Adja hozzá a következő kódot a **client.jshoz **:

```javascript
const callClient = new CallClient();
const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
let callAgent;

callClient.createCallAgent(tokenCredential).then(agent => {
  callAgent = agent;
  callButton.disabled = false;
});
```

## <a name="start-a-call"></a>Hívás indítása

Eseménykezelő hozzáadásával kezdeményezheti a hívását, ha a `callButton` gombra kattint:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Hívás befejezése

Esemény-figyelő hozzáadása az aktuális hívás befejezéséhez, amikor a `hangUpButton` gombra kattint:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

A `forEveryone` tulajdonság véget ér a hívás minden résztvevőnél.

## <a name="run-the-code"></a>A kód futtatása

Használja az `webpack-dev-server` alkalmazást az alkalmazás létrehozásához és futtatásához. Futtassa a következő parancsot az alkalmazás gazdagépének a helyi webkiszolgálón való megadásához:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Nyissa meg a böngészőt, és navigáljon a gombra http://localhost:8080/ . A következőnek kell megjelennie:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Képernyőkép a befejezett JavaScript-alkalmazásról.":::

A kimenő VOIP-hívást úgy teheti meg, hogy egy felhasználói azonosítót biztosít a szövegmezőben, és a **hívás indítása** gombra kattint. A Calling `8:echo123` egy echo-robottal csatlakozik, ez nagyszerű megoldás az első lépésekhez és a hangeszközök ellenőrzéséhez.
