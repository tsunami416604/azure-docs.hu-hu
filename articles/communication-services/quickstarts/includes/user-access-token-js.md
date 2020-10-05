---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947219"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../create-communication-resource.md).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

A futtatásával `npm init -y` **package.js** hozhat létre az alapértelmezett beállításokkal rendelkező fájlon.

```console
npm init -y
```

### <a name="install-the-package"></a>A csomag telepítése

A `npm install` paranccsal telepítheti az Azure Communication Services Felügyeleti ügyféloldali kódtárat a javascripthez.

```console

npm install @azure/communication-administration --save

```

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

## <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Új szövegfájl megnyitása a kódszerkesztő programban
1. Vegyen fel egy hívást a következő `require` betöltéséhez `CommunicationIdentityClient`
1. A program struktúrájának létrehozása, beleértve az alapszintű kivételek kezelését

A kezdéshez használja a következő kódot:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. Mentse az új fájlt **issue-token.jsként ** a *User-tokens-Gyorsindítás* könyvtárban.

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A-példány létrehozása a- `CommunicationIdentityClient` val a kapcsolatok karakterláncával. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../create-communication-resource.md#store-your-connection-string).

Adja hozzá a következő kódot a `main` metódushoz:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>Felhasználó létrehozása

Az Azure kommunikációs szolgáltatások egy egyszerűsített identitási könyvtárat tartanak fenn. A `createUser` metódus használatával hozzon létre egy új bejegyzést a címtárban egyedi értékkel `Id` . Az alkalmazás felhasználóinak és a kommunikációs szolgáltatások által generált identitások (például az alkalmazás-kiszolgáló adatbázisában való tárolás) közötti leképezést kell fenntartani.

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>Felhasználói hozzáférési tokenek kiadása

A `issueToken` metódus használatával kiállíthatja a kommunikációs szolgáltatások felhasználójának hozzáférési jogkivonatát. Ha nem adja meg a választható `user` paramétert, a rendszer új felhasználót hoz létre, és visszaadja a tokent.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

A felhasználói hozzáférési tokenek olyan rövid élettartamú hitelesítő adatok, amelyeket újra kell adni ahhoz, hogy a felhasználók megakadályozzák a szolgáltatás megszakadását. A `expiresOn` Response tulajdonság a jogkivonat élettartamát jelzi.

## <a name="revoke-user-access-tokens"></a>Felhasználói hozzáférési tokenek visszavonása

Bizonyos esetekben előfordulhat, hogy explicit módon vissza kell vonnia a felhasználói hozzáférési jogkivonatokat, például amikor egy felhasználó módosítja a szolgáltatásban való hitelesítéshez használt jelszót. Ezzel a `revokeTokens` módszerrel érvénytelenítheti az összes felhasználó hozzáférési jogkivonatát.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>Felhasználó törlése

Egy felhasználó törlése visszavonja az összes aktív jogkivonatot, és megakadályozza, hogy az identitások számára további jogkivonatokat bocsásson ki. Emellett eltávolítja a felhasználóhoz társított összes megőrzött tartalmat is.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>A kód futtatása

A konzol parancssorában navigáljon a *issue-token.js* fájlt tartalmazó könyvtárra, majd hajtsa végre a következő `node` parancsot az alkalmazás futtatásához.

```console
node ./issue-token.js
```
