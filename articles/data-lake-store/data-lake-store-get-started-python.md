---
title: 'Python: Fiókkezelési műveletek az Azure Data Lake Storage Gen1 |} A Microsoft Docs'
description: Megtudhatja, hogyan használhatja a Python SDK fiókkezelési műveletek az Azure Data Lake Storage Gen1 dolgozhat.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 0cc5b101e1afb6ea648963188887cf43b65a5afa
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124949"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Fiókkezelési műveletek az Azure Data Lake Storage Gen1 Python használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Ismerje meg, hogyan használhatja a Python SDK esetében az Azure Data Lake Storage Gen1 hajtsa végre az alapszintű fiókkezelési műveletek például hozzon létre egy Data Lake Storage Gen1 fiókot, a fiókok listázása a Data Lake Storage Gen1, stb. Hogyan hajthat végre fájlrendszerműveleteket a Data Lake Storage Gen1 pythonnal kapcsolatos utasításokért lásd: [fájlrendszerműveletek a Data Lake Storage Gen1 pythonnal](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure-erőforráscsoport**. További útmutatásért lásd: [Azure-erőforráscsoport létrehozása](../azure-resource-manager/resource-group-portal.md).

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 használata pythonnal, három modult telepítenie kell.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modult, amely az Azure Data Lake Storage Gen1 fiókkezelési műveleteit tartalmazza. Ez a modul további információkért lásd: [Azure Data Lake Storage Gen1 felügyeleti modul-hivatkozás](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* A `azure-datalake-store` modult, amely az Azure Data Lake Storage Gen1 fájlrendszer-műveleteit tartalmazza. Ez a modul további információkért lásd: [azure-datalake-store fájlrendszermoduljához készült referenciaanyagban](http://azure-datalake-store.readthedocs.io/en/latest/).

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
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Mentse a mysample.py módosításait.

## <a name="authentication"></a>Hitelesítés

Ebben a szakaszban az Azure AD-hitelesítés különböző módjait tárgyaljuk. Az elérhető lehetőségek:

* Az alkalmazás végfelhasználói hitelesítésével kapcsolatban lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 pythonnal](data-lake-store-end-user-authenticate-python.md).
* Az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 pythonnal](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Ügyfél és a Data Lake Storage Gen1 fiók létrehozása

Az alábbi kódrészlet először a Data Lake Storage Gen1 fiók ügyfél hoz létre. Az ügyfélobjektum használatával hozzon létre egy Data Lake Storage Gen1 fiókot. Végül pedig létrehoz egy fájlrendszerügyfél-objektumot.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create Data Lake Storage Gen1 account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Storage Gen1 account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>A Data Lake Storage Gen1 fiókok listázása

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>A Data Lake Storage Gen1 fiók törlése

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>További lépések
* [Fájlrendszerműveletek a Data Lake Storage Gen1 pythonnal](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Lásd még

* [az Azure-datalake-store – Python (fájlrendszer) referencia](http://azure-datalake-store.readthedocs.io/en/latest)
* [Nyílt forráskódú Big Data-alkalmazások kompatibilis az Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
