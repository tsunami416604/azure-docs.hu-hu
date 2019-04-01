---
title: Csatlakozás az Azure Media Services v3 API – Node.js
description: Ismerje meg, hogyan csatlakozhat a Media Services v3 API a node.js használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: fbea3daec3d9ccac81f246d3dc762fc80cb4b18a
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759528"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Csatlakozás a Media Services v3 API – Node.js

Ez a cikk bemutatja, hogyan csatlakozhat az Azure Media Services v3 node.js SDK használatával a szolgáltatás egyszerű bejelentkezési módszert.

## <a name="prerequisites"></a>Előfeltételek

- Telepítés [Node.js](https://nodejs.org/en/download/).
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md). Ne felejtse el az erőforráscsoport nevét és a Media Services-fiók nevét.

## <a name="create-packagejson"></a>Hozzon létre a package.json

1. Hozzon létre egy package.json fájlt a kedvenc szerkesztőjében.
1. Nyissa meg a fájlt, és illessze be a következő kódot:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

A következő csomagok adható meg:

|Csomag|Leírás|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Győződjön meg arról, hogy a legfrissebb Azure Media Services-csomagot használ, ellenőrizze a következőket [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Storage SDK. Fájlokat tölthet fel eszközöket használja.|
|`ms-rest-azure`| Való bejelentkezéshez használt.|

Győződjön meg arról, hogy a legfrissebb csomagot használ, a következő parancsot futtathatja:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Node.js-ügyfél csatlakozhat.

1. Hozzon létre egy tetszőleges szerkesztőben .js fájl.
1. Nyissa meg a fájlt, és illessze be az alábbi kódot.
1. Állítsa az értékeket az "végpont-konfiguráció" szakasz értékek származó [API-k elérése](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Az alkalmazás futtatása

Nyisson meg egy parancssort. Keresse meg a minta könyvtárat, és hajtsa végre a következő parancsokat:

```
npm install 
node index.js
```

## <a name="see-also"></a>Lásd még

[.NET-referencia](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)

## <a name="next-steps"></a>További lépések

- [A Media Services – alapelvek](concepts-overview.md)
- [Gyors útmutató]()
- [Node.js-referencia](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/?view=azure-node-latest)
- [NPM-telepítés, azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)<br>
