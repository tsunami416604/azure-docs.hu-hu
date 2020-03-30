---
title: 'Végfelhasználói hitelesítés: Python az Azure Data Lake Storage Gen1 szolgáltatással az Azure Active Directory használatával | Microsoft dokumentumok'
description: Megtudhatja, hogyan érhet el végfelhasználói hitelesítést az Azure Data Lake Storage Gen1 használatával az Azure Active Directory és a Python használatával
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
ms.openlocfilehash: c69f6c1f587285c5c52280c4c49008764d5b20d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265596"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 használatával a Python használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Ebben a cikkben megtudhatja, hogyan használhatja a Python SDK-t az Azure Data Lake Storage Gen1 végfelhasználói hitelesítéséhez. A végfelhasználói hitelesítés tovább osztható két kategóriába:

* Végfelhasználói hitelesítés többtényezős hitelesítés nélkül
* Végfelhasználói hitelesítés többtényezős hitelesítéssel

Mindkét lehetőség ebben a cikkben található. A Data Lake Storage Gen1 Python használatával történő szolgáltatás-szolgáltatás hitelesítése a [Szolgáltatás-szolgáltatás hitelesítésa a Data Lake Storage Gen1](data-lake-store-service-to-service-authenticate-python.md)használatával python használatával című témakörben található.

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "natív" alkalmazást.** Az Azure Active Directory használatával el kell végeznie a végfelhasználói hitelesítés lépéseit a [Data Lake Storage Gen1 szolgáltatással.](data-lake-store-end-user-authenticate-using-active-directory.md)

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 Python használatával való munkához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modul, amely tartalmazza az Azure Data Lake Storage Gen1 fiókkezelési műveleteket. Erről a modulról az [Azure Data Lake Storage Gen1 Management modul hivatkozása](/python/api/azure-mgmt-datalake-store/)című témakörben talál további információt.
* A `azure-datalake-store` modul, amely tartalmazza az Azure Data Lake Storage Gen1 fájlrendszer-műveleteket. Erről a modulról az [azure-datalake-store Filesystem modul hivatkozása](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)című témakörben talál további információt.

A modulok telepítéséhez használja a következő parancsokat.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. Az Ön által választott IDE-ben hozzon létre egy új Python-alkalmazást, például **mysample.py**.

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

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Végfelhasználói hitelesítés többtényezős hitelesítéssel

### <a name="for-account-management"></a>Számlakezeléshez

Az alábbi kódrészlet használatával hitelesítheti magát az Azure AD-vel a Data Lake Storage Gen1-fiók fiókkezelési műveleteihez. A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Adja meg az alábbi értékeket egy meglévő Azure AD **natív** alkalmazás.

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

### <a name="for-filesystem-operations"></a>Fájlrendszer-műveletekhez

Ezzel hitelesítheti magát az Azure AD-vel a Data Lake Storage Gen1-fiók fájlrendszer-műveleteihez. A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Adja meg az alábbi értékeket egy meglévő Azure AD **natív** alkalmazás.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Végfelhasználói hitelesítés többtényezős hitelesítés nélkül

Ez elavult. További információ: [Azure-hitelesítés Python SDK használatával.](/azure/python/python-sdk-azure-authenticate)
   
## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhatja a végfelhasználói hitelesítést az Azure Data Lake Storage Gen1 python használatával történő hitelesítéshez. Most már tekintse meg az alábbi cikkeket, amelyek arról beszélnek, hogyan használhatja a Python t az Azure Data Lake Storage Gen1 használatával.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 python használatával](data-lake-store-get-started-python.md)
* [Adatműveletek a Data Lake Storage Gen1 python használatával](data-lake-store-data-operations-python.md)

