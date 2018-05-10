---
title: 'Végfelhasználói hitelesítési: Azure Active Directory használatával a Data Lake Store Python |} Microsoft Docs'
description: Megtudhatja, hogyan végfelhasználói hitelesítés az Azure Active Directory használatával Python Data Lake Store elérése
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 7f582683245505fe1ff161e4461dd34690a1ab0a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>Végfelhasználói hitelesítési a Data Lake Store pythonos környezetekben
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Ebből a cikkből megismerheti az Azure Data Lake Store végfelhasználói hitelesítés elvégzéséhez a Python SDK használatáról. Végfelhasználói hitelesítés további két kategóriába oszthatók:

* Végfelhasználói hitelesítést a multi-factor authentication nélkül
* Végfelhasználói hitelesítési többtényezős hitelesítéssel

Ez a cikk mindkét ezeket a beállításokat ismerteti. A szolgáltatások közötti hitelesítéshez a Data Lake Store pythonos környezetekben, lásd: [szolgáltatások közötti hitelesítést a Data Lake Store használatának Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy "Natív" Azure Active Directory-alkalmazás**. Végrehajtotta a lépéseket [végfelhasználói hitelesítés az Azure Active Directory használatával a Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Store a Pythonnal való használatához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* Az `azure-mgmt-datalake-store` modult, amely az Azure Data Lake Store fiókkezelési műveleteit tartalmazza. További információkat erről a modulról [az Azure Data Lake Store kezelési moduljához készült referenciaanyagban](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python) talál.
* Az `azure-datalake-store` modult, amely az Azure Data Lake Store fájlrendszerműveleteit tartalmazza. További információkat erről a modulról [az Azure Data Lake Store fájlrendszermoduljához készült referenciaanyagban](http://azure-datalake-store.readthedocs.io/en/latest/) talál.

A modulok telepítéséhez használja a következő parancsokat.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. A ide az Ön által választott, hozzon létre egy új Python alkalmazást, például **mysample.py**.

2. Adja hozzá a következő kódrészletet a szükséges modulok importálásához

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Mentse a mysample.py módosításait.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Végfelhasználói hitelesítési többtényezős hitelesítéssel

### <a name="for-account-management"></a>A fiókok kezelése

A következő kódrészletet használja a Data Lake Store-fiók a fiókkezelési műveletekhez az Azure AD szolgáltatással való hitelesítésre. A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Adja meg az alábbiakban egy meglévő Azure AD **natív** alkalmazás.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>A fájlrendszer műveletek

Ezzel a fájlrendszer-műveleteket a Data Lake Store-fiók Azure ad-val való hitelesítéshez szükséges. A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Adja meg az alábbiakban egy meglévő Azure AD **natív** alkalmazás.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Végfelhasználói hitelesítést a multi-factor authentication nélkül

Ez elavult. További információkért lásd: [hitelesítés Azure Python SDK](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta végfelhasználói hitelesítés használata az Azure Data Lake Store való hitelesítéshez szükséges Python használatával. Most már megtekintheti a következő cikkekben használatáról Python működéséhez az Azure Data Lake Store kommunikálhatnak.

* [Fiókkezelési műveletekhez a Data Lake Store pythonos környezetekben](data-lake-store-get-started-python.md)
* [Adatok műveletek a Data Lake Store pythonos környezetekben](data-lake-store-data-operations-python.md)

