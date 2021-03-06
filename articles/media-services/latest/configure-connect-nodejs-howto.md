---
title: Kapcsolódás Azure Media Services V3 API-hoz – Node.js
description: Ez a cikk bemutatja, hogyan csatlakozhat a Media Services V3 API-hoz a Node.js használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: c6ea238edd68413646dda59b22d1c0dc2557d57e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916831"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Kapcsolódás Media Services V3 API-hoz – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan csatlakozhat a Azure Media Services v3 node.js SDK-hoz az egyszerű szolgáltatás bejelentkezési metódusának használatával.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse a [Node.js](https://nodejs.org/en/download/).
- [Hozzon létre egy Media Services fiókot](./create-account-howto.md). Ügyeljen arra, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét.

> [!IMPORTANT]
> Tekintse át az [elnevezési konvenciókat](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>package.jslétrehozása a következőn

1. Hozzon létre egy package.jsfájlt a kedvenc szerkesztője használatával.
1. Nyissa meg a fájlt, és illessze be a következő kódot:

   Győződjön meg arról, hogy a [javascripthez készült AZUREMEDIASERVICES SDK](https://www.npmjs.com/package/@azure/arm-mediaservices)legújabb verzióját kapja meg.

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^8.0.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

A következő csomagokat kell megadni:

|Csomag|Description|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Győződjön meg arról, hogy a legújabb Azure Media Services csomagot használja, és [telepítse az Azure-ARM-Mediaservices NPM](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Storage SDK. Fájlok eszközökre való feltöltésekor használatos.|
|`ms-rest-azure`| A bejelentkezéshez használatos.|

A következő parancs futtatásával gondoskodhat arról, hogy a legújabb csomagot használja:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Kapcsolódás Node.js-ügyfélhez

1. Hozzon létre egy. js-fájlt a kedvenc szerkesztője segítségével.
1. Nyissa meg a fájlt, és illessze be a következő kódot.
1. Állítsa be az "Endpoint config" (végpont konfigurációja) szakaszban található értékeket az [Access API](./access-api-howto.md)-k által kapott értékekre.

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

## <a name="run-your-app"></a>Futtassa az alkalmazást

Nyisson meg egy parancssort. Keresse meg a minta címtárát, és hajtsa végre a következő parancsokat:

```
npm install 
node index.js
```

## <a name="see-also"></a>További információ

- [Media Services fogalmak](concepts-overview.md)
- [NPM-telepítés, azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a Media Services [Node.js ref](/javascript/api/overview/azure/mediaservices/management) dokumentációval, és tekintse meg a Media Services API node.js használatával történő használatát bemutató [mintákat](https://github.com/Azure-Samples/media-services-v3-node-tutorials) .
