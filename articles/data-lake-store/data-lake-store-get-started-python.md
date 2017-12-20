---
title: "Python: Fiókkezelési műveletek az Azure Data Lake Store-ban | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja a Python SDK-t a Data Lake Store-fiókkezelési műveletekhez."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 76e84687815ca6f4b031e5f7143ba0079fb053db
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Fiókkezelési műveletek az Azure Data Lake Store-ban a Python használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

A cikkből megtudhatja, hogyan végezhet el olyan alapszintű fiókkezelési műveleteket az Azure Data Lake Store-hoz készült Python SDK segítségével, mint például a Data Lake Store-fiókok létrehozása vagy a Data Lake Store-fiókok listázása. A Data Lake Store fájlrendszerműveleteinek Python használatával való végrehajtásával kapcsolatban lásd: [Fájlrendszerműveletek a Data Lake Store-on a Python használatával](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure-erőforráscsoport**. További útmutatásért lásd: [Azure-erőforráscsoport létrehozása](../azure-resource-manager/resource-group-portal.md).

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Store a Pythonnal való használatához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* Az `azure-mgmt-datalake-store` modult, amely az Azure Data Lake Store fiókkezelési műveleteit tartalmazza. További információkat erről a modulról [az Azure Data Lake Store kezelési moduljához készült referenciaanyagban](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html) talál.
* Az `azure-datalake-store` modult, amely az Azure Data Lake Store fájlrendszerműveleteit tartalmazza. További információkat erről a modulról [az Azure Data Lake Store fájlrendszermoduljához készült referenciaanyagban](http://azure-datalake-store.readthedocs.io/en/latest/) talál.

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

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
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

* Az alkalmazás végfelhasználói hitelesítésével kapcsolatban lásd: [Végfelhasználói hitelesítés a Data Lake Store-ban a Python használatával](data-lake-store-end-user-authenticate-python.md).
* Az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban lásd: [Szolgáltatások közötti hitelesítés a Data Lake Store-ban a Python használatával](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-store-account"></a>Ügyfél és Data Lake Store-fiók létrehozása

Az alábbi kódrészlet először a Data Lake Store-fiókügyfelet hozza létre. Az ügyfélobjektum használatával hoz majd létre egy Data Lake Store-fiókot. Végül pedig létrehoz egy fájlrendszerügyfél-objektumot.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>A Data Lake Store-fiókok kilistázása

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>A Data Lake Store-fiók törlése

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Következő lépések
* [Fájlrendszerműveletek a Data Lake Store-on a Python használatával](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Lásd még:
* [Azure Data Lake Store – Python (fiókkezelés) referencia](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Azure Data Lake Store – Python (fájlrendszer) referencia](http://azure-datalake-store.readthedocs.io/en/latest)
* [Az Azure Data Lake Store-ral kompatibilis nyílt forráskódú big data-alkalmazások](data-lake-store-compatible-oss-other-applications.md)
