---
title: Csatlakozás az Azure Media Services 3-as v3 API-hoz – Node.js
description: Ez a cikk bemutatja, hogyan csatlakozhat a Media Services v3 API-hoz a Node.js.
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
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896104"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Csatlakozás a Media Services 3-as v3 API-jához – Node.js

Ez a cikk bemutatja, hogyan csatlakozhat az Azure Media Services v3-node.js SDK szolgáltatásegyszerű bejelentkezési módszer használatával.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse [a Node.js programot.](https://nodejs.org/en/download/)
- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md). Ne felejtse el megjegyezni az erőforráscsoport nevét és a Media Services-fiók nevét.

> [!IMPORTANT]
> Tekintse át [az elnevezési konvenciókat.](media-services-apis-overview.md#naming-conventions)

## <a name="create-packagejson"></a>Package.json létrehozása

1. Hozzon létre egy package.json fájlt a kedvenc szerkesztőjével.
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

A következő csomagokat kell meghatározni:

|Csomag|Leírás|
|---|---|
|`azure-arm-mediaservices`|Az Azure Media Services SDK-t. <br/>Győződjön meg arról, hogy a legújabb Azure Media Services-csomagot használja, ellenőrizze az [NPM telepítését az azure-arm-mediaservices.To](https://www.npmjs.com/package/azure-arm-mediaservices/)make sure you are using the latest Azure Media Services package, check NPM install azure-arm-mediaservices .|
|`azure-storage`|Tároló SDK. Fájlok eszközökbe való feltöltésekén.|
|`ms-rest-azure`| Régebben bejelentkezett.|

A következő paranccsal ellenőrizheti, hogy a legújabb csomagot használja-e:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Csatlakozás a Node.js ügyfélhez

1. Hozzon létre egy .js fájlt a kedvenc szerkesztőjével.
1. Nyissa meg a fájlt, és illessze be a következő kódot.
1. Állítsa be az "endpoint config" szakaszban a [hozzáférési API-kból](access-api-cli-how-to.md)kapott értékeket.

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

Nyisson meg egy parancssort. Tallózással keresse meg a minta könyvtárát, és hajtsa végre a következő parancsokat:

```
npm install 
node index.js
```

## <a name="see-also"></a>Lásd még

- [A Media Services fogalmai](concepts-overview.md)
- [NPM-telepítés, azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>További lépések

Fedezze fel a Media Services [Node.js ref](/javascript/api/overview/azure/mediaservices/management) dokumentációját, és tekintse meg azokat a [mintákat,](https://github.com/Azure-Samples/media-services-v3-node-tutorials) amelyek bemutatják a Media Services API használatát a node.js használatával.

