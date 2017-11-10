---
title: "Adatok áthelyezése, és az Azure Blob Storage AzCopy használatával |} Microsoft Docs"
description: "Adatok áthelyezése Azure Blob Storage-tárolóba vagy onnan máshová az AzCopy használatával"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c309ceb2-0e83-4a07-b16d-c997dcd62d5c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 928d579da0cb469fd714b8a456a64917158d8ebc
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Adatok áthelyezése, és az Azure Blob Storage AzCopy használatával
AzCopy egy feltöltés, letöltése, és az adatok másolását a Microsoft Azure blob, a fájl és az a table storage parancssori segédprogram.

Az Azure platformon az AzCopy és további információkat telepítésével kapcsolatos útmutatásért lásd: [első lépések az AzCopy parancssori segédprogram](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Ha virtuális Gépet, amely a parancsfájlok által biztosított be lett állítva az [adatok tudományos virtuális gépek Azure-ban](virtual-machines.md), majd az AzCopy már telepítve van a virtuális gép.
> 
> [!NOTE]
> Az Azure blob storage teljes bevezetéséhez hivatkozik [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és [Azure Blob szolgáltatás](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Jelen dokumentum céljából feltételezzük, hogy az Azure-előfizetéssel, a tárfiók és a megfelelő kulcsot adott fiók rendelkezik. Adatok feltöltése/letöltése, előtt ismernie kell az Azure storage-fiók nevét és a fiók kulcs.

* Állítsa be Azure-előfizetéssel, lásd: [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* A storage-fiók létrehozásával és az első fiók és a fontos információkat lásd: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>AzCopy parancsok futtatása
AzCopy parancsok futtatásához nyisson meg egy parancsablakot, és keresse meg a számítógépen, ahol a végrehajtható AzCopy.exe az AzCopy telepítési könyvtárára. 

Az AzCopy parancs alapvető szintaxisa:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Adja hozzá az AzCopy telepítési hely a rendszerútvonalhoz, és futtassa a parancsokat bármelyik könyvtárból. Alapértelmezés szerint AzCopy telepítés *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* vagy *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Fájlok feltöltése az Azure-blobba
A fájl feltöltéséhez, a következő paranccsal:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Egy Azure-blobot a fájlok letöltése
A fájl letöltését az Azure blob, a következő paranccsal:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Azure tárolók közötti átviteléhez a BLOB
Az Azure tárolók közötti átviteléhez a BLOB, a következő paranccsal:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Tippek az AzCopy segítségével
> [!TIP]
> 1. Ha **feltöltése** fájlok, */S* fájlok rekurzív módon feltölt. Ez a paraméter nélkül nem feltöltése fájlok alkönyvtáraiban találhatóak.  
> 2. Ha **letöltése** fájl, */S* a tároló rekurzív módon keres mindaddig, amíg a megadott könyvtárban és annak alkönyvtáraiban található összes fájl, vagy minden fájl, amely megfelel a megadott könyvtárban a megadott mintának és annak alkönyvtáraiban letöltődnek.  
> 3. Nem adható meg egy **adott blob fájl** letölteni, használja a */Source* paraméter. A megadott fájl letöltésére, adja meg a blob-fájlnevet, letölteni, használja a */mintát* paraméter. **/S** kell rendelkeznie a fájl neve mintát rekurzív módon keres AzCopy paraméter használható. A minta paraméterét nélkül AzCopy letölti az összes fájlt, hogy a címtárban.
> 
> 

