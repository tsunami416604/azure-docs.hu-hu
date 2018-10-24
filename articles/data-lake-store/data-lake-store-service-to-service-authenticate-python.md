---
title: 'Szolgáltatások közötti hitelesítés: Azure Active Directory használatával az Azure Data Lake Storage Gen1 Python |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 az Azure Active Directory-ban a Python használatával
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: a51eb8f560d598f86caf57b7b24737b0a3574b68
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957219"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 Python használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Ebben a cikkben megismerkedhet a Python SDK használata a szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 tennie. Végfelhasználói hitelesítés a Data Lake Storage Gen1 Python használatával, lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 pythonnal](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Az Azure Active Directory "" webalkalmazás létrehozásához**. El kell végeznie a lépések [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 használata pythonnal, három modult telepítenie kell.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modult, amely a Data Lake Storage Gen1 fiókkezelési műveleteit tartalmazza. Ez a modul további információkért lásd: [Azure Data Lake Storage Gen1 felügyeleti modul-hivatkozás](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* A `azure-datalake-store` modult, amely a Data Lake Storage Gen1 fájlrendszer-műveleteit tartalmazza. Ez a modul további információkért lásd: [azure-datalake-store Fájlrendszermoduljához készült referenciaanyagban](http://azure-datalake-store.readthedocs.io/en/latest/).

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

Ez a kódrészlet használatával hitelesíti az Azure AD-fiókkezelési műveletek a Data Lake Storage Gen1 például hozzon létre egy Data Lake Storage Gen1 fiókot, törölje a Data Lake Storage Gen1 fiók stb. Az alábbi kódrészlet is használható az alkalmazás nem interaktív hitelesítéséhez az ügyfél titkos kulcsát egy alkalmazás / egyszerű szolgáltatás egy meglévő Azure ad "Webalkalmazás" alkalmazás.

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

Használja az alábbi kódrészlet például a hitelesítést az Azure AD-fájlrendszerműveletek a Data Lake Storage Gen1 hozzon létre a mappa, fájl feltöltése stb. A következő kódrészlet használható az alkalmazás nem interaktív hitelesítéséhez, az alkalmazás/egyszerű szolgáltatás titkos ügyfélkódjának használatával. Ezt meglévő „webes” Azure AD-alkalmazással használhatja.

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
Ebben a cikkben megtanulta, hogyan service-to-service-hitelesítés használatára való hitelesítés a Data Lake Storage Gen1 Python használatával. Most már megtekintheti a következő cikkeket, hogy hogyan használható a Python használata a Data Lake Storage Gen1 beszélni.

* [Fiókkezelési műveletek a Data Lake Storage Gen1 Python használatával](data-lake-store-get-started-python.md)
* [A Data Lake Storage Gen1 Adatműveletek Python használatával](data-lake-store-data-operations-python.md)


