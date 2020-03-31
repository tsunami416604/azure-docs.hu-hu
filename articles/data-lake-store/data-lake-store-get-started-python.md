---
title: 'Python: Fiókkezelési műveletek az Azure Data Lake Storage Gen1 webhelyen | Microsoft dokumentumok'
description: Ismerje meg, hogyan használhatja a Python SDK-t az Azure Data Lake Storage Gen1 fiókkezelési műveletekhez.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c71136ecb57fac460514b5f4815ba19cc22d86cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76290614"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Fiókkezelési műveletek az Azure Data Lake Storage Gen1 python használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Ismerje meg, hogyan használhatja a Python SDK for Azure Data Lake Storage Gen1 alapvető fiókkezelési műveletek, például hozzon létre egy Data Lake Storage Gen1 fiókot, listával a Data Lake Storage Gen1 fiókok, stb. A Data Lake Storage Gen1 Python használatával végzett fájlrendszer-műveletek végrehajtásáról a [Data Lake Storage Gen1 fájlrendszer-műveleteinek Python használatával történő végrehajtásáról.](data-lake-store-data-operations-python.md)

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Egy Azure-erőforráscsoport.** További útmutatásért lásd: [Azure-erőforráscsoport létrehozása](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 Python használatával való munkához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modul, amely tartalmazza az Azure Data Lake Storage Gen1 fiókkezelési műveleteket. Erről a modulról az [Azure Data Lake Storage Gen1 Management modul hivatkozása](/python/api/azure-mgmt-datalake-store/)című témakörben talál további információt.
* A `azure-datalake-store` modul, amely tartalmazza az Azure Data Lake Storage Gen1 fájlrendszer-műveleteket. Erről a modulról az [azure-datalake-store fájlrendszer modulhivatkozáscímű témakörben](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)talál további információt.

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

* Az alkalmazás végfelhasználói hitelesítése a [Data Lake Storage Gen1 használatával a Python használatával című végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-python.md)című témakörben található.
* Az alkalmazás szolgáltatás-szolgáltatás hitelesítése a [Szolgáltatás-szolgáltatás hitelesítés a Data Lake Storage Gen1 python használatával című](data-lake-store-service-to-service-authenticate-python.md)témakörben található.

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Ügyfél- és Data Lake Storage Gen1-fiók létrehozása

A következő kódrészlet először létrehozza a Data Lake Storage Gen1 fiókügyfelet. Az ügyfélobjektum segítségével hozzon létre egy Data Lake Storage Gen1 fiókot. Végül pedig létrehoz egy fájlrendszerügyfél-objektumot.

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
* [Fájlrendszer-műveletek a Data Lake Storage Gen1](data-lake-store-data-operations-python.md)python használatával.

## <a name="see-also"></a>Lásd még

* [azure-datalake-store Python (fájlrendszer) hivatkozás](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Az Azure Data Lake Storage Gen1-tal kompatibilis nyílt forráskódú Big Data-alkalmazások](data-lake-store-compatible-oss-other-applications.md)
