---
title: 'Python: Fájlrendszerműveletek az Azure Data Lake Storage Gen1 |} A Microsoft Docs'
description: Ismerje meg, hogyan dolgozhat a Data Lake Storage Gen1 fájlrendszer Python SDK használatával.
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
ms.openlocfilehash: 57efc718a51398b577a0078ba829d2f6209cab54
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880746"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Fájlrendszerműveletek az Azure Data Lake Storage Gen1 Python használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Ebből a cikkből megismerheti a Python SDK használata fájlrendszerműveletek az Azure Data Lake Storage Gen1 végrehajtásához. Fiókkezelési műveletek végrehajtása a Data Lake Storage Gen1 pythonnal kapcsolatos utasításokért lásd: [fiókkezelési műveletek a Data Lake Storage Gen1 pythonnal](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Az Azure Data Lake Storage Gen1 fiók**. Kövesse az utasításokat, [Azure Data Lake Storage Gen1 használatának első lépései az Azure portal használatával](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 használata pythonnal, három modult telepítenie kell.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modult, amely az Azure Data Lake Storage Gen1 fiókkezelési műveleteit tartalmazza. Ez a modul további információkért lásd: a [azure-mgmt-datalake-store modul-hivatkozás](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* A `azure-datalake-store` modult, amely az Azure Data Lake Storage Gen1 fájlrendszer-műveleteit tartalmazza. Ez a modul további információkért lásd: a [azure-datalake-store-fájlrendszer modulhivatkozás](https://azure-datalake-store.readthedocs.io/en/latest/).

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

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Mentse a mysample.py módosításait.

## <a name="authentication"></a>Authentication

Ebben a szakaszban az Azure AD-hitelesítés különböző módjait tárgyaljuk. Az elérhető lehetőségek:

* Az alkalmazás végfelhasználói hitelesítésével kapcsolatban lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 pythonnal](data-lake-store-end-user-authenticate-python.md).
* Az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 pythonnal](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Fájlrendszerügyfél létrehozása

Az alábbi kódrészlet először a Data Lake Storage Gen1 fiók ügyfél hoz létre. Az ügyfélobjektum használatával hozzon létre egy Data Lake Storage Gen1 fiókot. Végül pedig létrehoz egy fájlrendszerügyfél-objektumot.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

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

## <a name="next-steps"></a>További lépések
* [Fiókkezelési műveletek a Data Lake Storage Gen1 pythonnal](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Lásd még

* [Az Azure Data Lake Storage Gen1 Python (fájlrendszer) referencia](https://azure-datalake-store.readthedocs.io/en/latest)
* [Nyílt forráskódú Big Data-alkalmazások kompatibilis az Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
