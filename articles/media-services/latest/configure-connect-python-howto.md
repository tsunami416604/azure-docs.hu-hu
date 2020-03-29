---
title: Csatlakozás az Azure Media Services v3 API-hoz – Python
description: Ez a cikk bemutatja, hogyan csatlakozhat a Media Services v3 API-hoz a Python.
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
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888461"
---
# <a name="connect-to-media-services-v3-api---python"></a>Csatlakozás a Media Services v3 API-hoz – Python

Ez a cikk bemutatja, hogyan csatlakozhat az Azure Media Services v3 Python SDK-hoz az egyszerű szolgáltatás bejelentkezési módszer használatával.

## <a name="prerequisites"></a>Előfeltételek

- Python letöltése [python.org](https://www.python.org/downloads/)
- Győződjön meg `PATH` arról, hogy a környezeti változó
- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md). Ne felejtse el megjegyezni az erőforráscsoport nevét és a Media Services-fiók nevét.
- Kövesse az [Access API-k témakörlépéseit.](access-api-cli-how-to.md) Rögzítse az előfizetés-azonosítót, az alkalmazásazonosítót (ügyfélazonosító), a hitelesítési kulcsot (titkos) és a bérlői azonosítót, amelya későbbi lépésben szüksége van rá.

> [!IMPORTANT]
> Tekintse át [az elnevezési konvenciókat.](media-services-apis-overview.md#naming-conventions)

## <a name="install-the-modules"></a>A modulok telepítése

Az Azure Media Services Python használatával való munkához telepítenie kell ezeket a modulokat.

* A `azure-mgmt-resource` modul, amely tartalmazza az Azure-modulok az Active Directoryhoz.
* A `azure-mgmt-media` modul, amely magában foglalja a Media Services entitásokat.

Nyisson meg egy parancssori eszközt, és a következő parancsokkal telepítse a modulokat.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Csatlakozás a Python-ügyfélhez

1. `.py` Kiterjesztésű fájl létrehozása
1. A fájl megnyitása a kedvenc szerkesztőjében
1. Adja hozzá a következő kódot a fájlhoz. A kód importálja a szükséges modulokat, és létrehozza a Media Services szolgáltatáshoz való csatlakozáshoz szükséges Active Directory hitelesítő adatok objektumot.

      A változók értékeinek beállítása az [Access API-kból](access-api-cli-how-to.md) kapott értékekre

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. A fájl futtatása

## <a name="next-steps"></a>További lépések

- Használja [python SDK](https://aka.ms/ams-v3-python-sdk).
- Tekintse át a Media Services [Python ref](https://aka.ms/ams-v3-python-ref) dokumentációját.
