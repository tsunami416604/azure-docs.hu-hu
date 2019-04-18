---
title: 'Végfelhasználói hitelesítés: Az Azure Active Directoryval Python és az Azure Data Lake Storage Gen1 |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 a Python használatával Azure Active Directory használatával
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8b72604d7e736230911d0a0987b88d372be4ddf3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881279"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 a Python használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Ebben a cikkben megismerkedhet a Python SDK használatával hajtsa végre a végfelhasználói hitelesítés az Azure Data Lake Storage Gen1. Végfelhasználói hitelesítés további két kategóriába oszthatók:

* Végfelhasználói hitelesítés a multi-factor authentication használata nélkül
* Végfelhasználói hitelesítés a multi-factor authentication szolgáltatás

Ebben a cikkben mindkét ezek a beállítások ismertetése. Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 Python használatával, lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 pythonnal](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre a "Natív" Azure Active Directory-alkalmazás**. El kell végeznie a lépések [végfelhasználói hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 használata pythonnal, három modult telepítenie kell.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modult, amely az Azure Data Lake Storage Gen1 fiókkezelési műveleteit tartalmazza. Ez a modul további információkért lásd: [Azure Data Lake Storage Gen1 felügyeleti modul-hivatkozás](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* A `azure-datalake-store` modult, amely az Azure Data Lake Storage Gen1 fájlrendszer-műveleteit tartalmazza. Ez a modul további információkért lásd: [azure-datalake-store Fájlrendszermoduljához készült referenciaanyagban](https://azure-datalake-store.readthedocs.io/en/latest/).

A modulok telepítéséhez használja a következő parancsokat.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. A választott IDE-ben hozzon létre egy új Python-alkalmazás, például **mysample.py**.

2. Adja hozzá a következő kódrészletet a szükséges modulok importálásához

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Mentse a mysample.py módosításait.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Végfelhasználói hitelesítés a multi-factor authentication szolgáltatás

### <a name="for-account-management"></a>A fiókok kezelése

A következő kódrészletet használja az Azure AD-fiókkezelési műveletek a Data Lake Storage Gen1 fiók hitelesítéséhez. A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Adja meg az alábbi értékeket egy meglévő Azure AD-hez **natív** alkalmazás.

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

### <a name="for-filesystem-operations"></a>A fájlrendszer-műveletekhez

Ezzel az Azure AD-fájlrendszerműveletek a Data Lake Storage Gen1 fiók hitelesítéséhez. A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Adja meg az alábbi értékeket egy meglévő Azure AD-hez **natív** alkalmazás.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Végfelhasználói hitelesítés a multi-factor authentication használata nélkül

Elavult. További információkért lásd: [Python SDK-val az Azure hitelesítési](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan végfelhasználói hitelesítés használata az Azure Data Lake Storage Gen1 hitelesítést, a Python használatával. Most már megtekintheti a következő cikkeket, hogy hogyan használható a Python használata Azure Data Lake Storage Gen1 beszélni.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 Python használatával](data-lake-store-get-started-python.md)
* [A Data Lake Storage Gen1 Adatműveletek Python használatával](data-lake-store-data-operations-python.md)

