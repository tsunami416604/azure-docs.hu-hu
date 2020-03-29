---
title: 'Python: Fájlrendszer-műveletek az Azure Data Lake Storage Gen1 szolgáltatáson | Microsoft dokumentumok'
description: Ismerje meg, hogyan használhatja a Python SDK-t a Data Lake Storage Gen1 fájlrendszerrel való együttműködésre.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 17b53c508f0c4c5ba8fa257fcdc692cdaa45d470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294218"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Fájlrendszer-műveletek az Azure Data Lake Storage Gen1 python használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Ebben a cikkben megtudhatja, hogyan használhatja a Python SDK-t az Azure Data Lake Storage Gen1 fájlrendszer-műveletek végrehajtásához. A Data Lake Storage Gen1 Python használatával végzett fiókkezelési műveletekről a Data Lake Storage Gen1 python használatával végzett [fiókkezelési műveletekben](data-lake-store-get-started-python.md)talál útmutatást.

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.6.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1 fiók.** Kövesse az [Azure Data Lake Storage Gen1 használatának első lépéseit az Azure Portal használatával című útmutatóban.](data-lake-store-get-started-portal.md)

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Storage Gen1 Python használatával való munkához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult, amely további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* A `azure-mgmt-datalake-store` modul, amely tartalmazza az Azure Data Lake Storage Gen1 fiókkezelési műveleteket. A modulról további információt az [azure-mgmt-datalake-store modul hivatkozása tartalmaz.](/python/api/azure-mgmt-datalake-store/)
* A `azure-datalake-store` modul, amely tartalmazza az Azure Data Lake Storage Gen1 fájlrendszer-műveleteket. Erről a modulról további információt az [azure-datalake-store fájlrendszer modulhivatkozása tartalmaz.](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/)

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

   ## Common Azure imports
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

## <a name="create-filesystem-client"></a>Fájlrendszerügyfél létrehozása

A következő kódrészlet először létrehozza a Data Lake Storage Gen1 fiókügyfelet. Az ügyfélobjektum segítségével hozzon létre egy Data Lake Storage Gen1 fiókot. Végül pedig létrehoz egy fájlrendszerügyfél-objektumot.

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
* [Fiókkezelési műveletek a Data Lake Storage Gen1](data-lake-store-get-started-python.md)python használatával.

## <a name="see-also"></a>Lásd még

* [Azure Data Lake Storage Gen1 Python (fájlrendszer) – referencia](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Az Azure Data Lake Storage Gen1-tal kompatibilis nyílt forráskódú Big Data-alkalmazások](data-lake-store-compatible-oss-other-applications.md)
