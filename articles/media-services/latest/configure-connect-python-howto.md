---
title: Csatlakozás az Azure Media Services v3 API – Python
description: Ismerje meg, hogyan csatlakozhat a Media Services v3 API a Pythonnal.
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
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700200"
---
# <a name="connect-to-media-services-v3-api---python"></a>Csatlakozás a Media Services v3 API – Python

Ez a cikk bemutatja, hogyan csatlakozhat az Azure Media Services v3 Python SDK-t a szolgáltatás egyszerű bejelentkezési módszer használatával.

## <a name="prerequisites"></a>Előfeltételek

- Töltse le a Python [python.org](https://www.python.org/downloads/)
- Állítsa be a `PATH` környezeti változó
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md). Ne felejtse el az erőforráscsoport nevét és a Media Services-fiók nevét.
- Kövesse a [hozzáférés API-k](access-api-cli-how-to.md) témakör. Jegyezze fel az előfizetés-azonosító, Alkalmazásazonosítót (ügyfél-azonosító), a hitelesítési kulcs (titkos) és a bérlő Azonosítóját kell a későbbi lépésben.

## <a name="install-the-modules"></a>A modulok telepítése

Működik az Azure Media Services-be a Python, ezeket a modulokat telepíteni kell.

* A `azure-mgmt-resource` modult, amely az Active Directory Azure-modulokat tartalmaz.
* A `azure-mgmt-media` modult, amely a Media Services entitásokat is tartalmaz.

Nyisson meg egy parancssori eszköz, és használja az alábbi parancsokat a modulok telepítéséhez.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>A Python-kliens csatlakozni

1. Hozzon létre egy fájlt egy `.py` bővítmény
1. Nyissa meg a fájlt egy tetszőleges szerkesztőben
1. Adja hozzá a kódot a fájl a következő. A kód importálja a szükséges modulokat, és a Media Services-csatlakozáshoz kell az Active Directory hitelesítő objektumot hoz létre.

      Állítsa be a kapott értékeket, a változók értékeit [hozzáférés API-k](access-api-cli-how-to.md)

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

1. Futtassa a fájlt

## <a name="next-steps"></a>További lépések

- Használat [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Tekintse át a Media Services [Python ref](https://aka.ms/ams-v3-python-ref) dokumentációját.
