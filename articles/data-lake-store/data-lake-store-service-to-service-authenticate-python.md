---
title: 'Szolgáltatás-szolgáltatás hitelesítés: Python az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával | Microsoft dokumentumok'
description: Megtudhatja, hogyan valósítható meg a szolgáltatás-szolgáltatás hitelesítés az Azure Data Lake Storage Gen1 használatával az Azure Active Directory használatával a Python használatával
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
ms.openlocfilehash: 009aff2703829e6d30f93b3c8e3696724594f29b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260292"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Szolgáltatás-szolgáltatás hitelesítés az Azure Data Lake Storage Gen1 használatával Python
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Ebben a cikkben megtudhatja, hogyan használhatja a Python SDK szolgáltatás-szolgáltatás hitelesítést az Azure Data Lake Storage Gen1 használatával. A Data Lake Storage Gen1 Python használatával történő végfelhasználói hitelesítésről a [Data Lake Storage Gen1 használatával a Python használatával című végfelhasználói hitelesítés című](data-lake-store-end-user-authenticate-python.md)témakörben található.


## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "web" alkalmazást.** Az Azure Active Directory használatával el kell végeznie a Szolgáltatás-szolgáltatás hitelesítés lépéseit a [Data Lake Storage Gen1 szolgáltatással.](data-lake-store-service-to-service-authenticate-using-active-directory.md)

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 Python használatával való munkához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modul, amely magában foglalja a Data Lake Storage Gen1 fiókkezelési műveleteket. Erről a modulról az [Azure Data Lake Storage Gen1 Management modul hivatkozása](/python/api/azure-mgmt-datalake-store/)című témakörben talál további információt.
* A `azure-datalake-store` modul, amely magában foglalja a Data Lake Storage Gen1 fájlrendszer műveleteket. Erről a modulról az [azure-datalake-store Filesystem modul hivatkozása](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)című témakörben talál további információt.

A modulok telepítéséhez használja a következő parancsokat.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. A választott IDE-ben hozzon létre egy új Python-alkalmazást, például **mysample.py** néven.

2. Adja hozzá a következő kódrészletet a szükséges modulok importálásához

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Mentse a mysample.py módosításait.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Szolgáltatások közötti, titkos ügyfélkulccsal történő hitelesítés a fiókkezeléshez

Ezzel a kódrészlettel hitelesítheti magát az Azure AD-vel a Data Lake Storage Gen1 fiókkezelési műveleteihez, például hozzon létre egy Data Lake Storage Gen1 fiókot, törölje a Data Lake Storage Gen1-fiókot stb. A következő kódrészlet az alkalmazás nem interaktív hitelesítéséhez használható, egy meglévő Azure AD "Web App" alkalmazás alkalmazás/ egyszerű szolgáltatás ügyféltitkos fájljának használatával.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Szolgáltatások közötti, titkos ügyfélkulccsal történő hitelesítés a fájlrendszerműveletekhez

A következő kódrészlet használatával hitelesítheti magát az Azure AD-vel a Data Lake Storage Gen1 fájlrendszer-műveleteihez, például a mappa létrehozásához, a feltöltési fájlhoz stb. A következő kódrészlet az alkalmazás nem interaktív hitelesítésére használható, egy alkalmazás/ egyszerű szolgáltatás ügyféltitkos adatának használatával. Ezt meglévő „webes” Azure AD-alkalmazással használhatja.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használhatja a szolgáltatás-szolgáltatás hitelesítést a Data Lake Storage Gen1 python használatával. Most már tekintse meg a következő cikkeket, amelyek arról beszélnek, hogyan használhatja a Python t a Data Lake Storage Gen1 használatával.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 python használatával](data-lake-store-get-started-python.md)
* [Adatműveletek a Data Lake Storage Gen1 python használatával](data-lake-store-data-operations-python.md)


