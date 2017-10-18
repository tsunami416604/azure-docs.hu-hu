---
title: "Python: Fájlrendszerműveletek az Azure Data Lake Store-ban | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja a Python SDK-t a Data Lake Store-fájlrendszer kezeléséhez."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 0c3bda65bd40a5d24e4c4ab3dcbbbf27fbbb87c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>Fájlrendszerműveletek az Azure Data Lake Store-ban a REST API használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Ebből a cikkből megtudhatja, hogyan hajthat végre fájlrendszerműveleteket a Python SDK használatával. A Data Lake Store fiókkezelési műveleteinek a Python használatával való végrehajtásával kapcsolatban lásd: [Az Azure Data Lake Store használatának első lépései a Python használatával](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Store-fiók**. Kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md) című témakör utasításait.

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

2. Adja hozzá a következő sorokat a szükséges modulok importálásához.

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

## <a name="create-filesystem-client"></a>Fájlrendszerügyfél létrehozása

Az alábbi kódrészlet először a Data Lake Store-fiókügyfelet hozza létre. Az ügyfélobjektum használatával hoz majd létre egy Data Lake Store-fiókot. Végül pedig létrehoz egy fájlrendszerügyfél-objektumot.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Könyvtár létrehozása

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Fájl feltöltése


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Fájl letöltése

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Könyvtár törlése

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Következő lépések
* [Fiókkezelési műveletek a Data Lake Store-ban a Python használatával](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Lásd még:
* [Azure Data Lake Store – Python (fiókkezelés) referencia](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Azure Data Lake Store – Python (fájlrendszer) referencia](http://azure-datalake-store.readthedocs.io/en/latest)
* [Az Azure Data Lake Store-ral kompatibilis nyílt forráskódú big data-alkalmazások](data-lake-store-compatible-oss-other-applications.md)
