---
title: A Blob storage adatok áthelyezése az AzCopy - csoportos adatelemzési folyamat
description: Adatok áthelyezése Azure Blob Storage-tárolóba vagy onnan máshová az AzCopy használatával
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3165aad326ad476eb5064f0b99acd8c3f5a036d8
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474700"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Adatok importálására és az Azure Blob Storage, az AzCopy használatával
Az AzCopy parancssori segédprogram az feltöltése, letöltése és másolhatja az adatokat, és a Microsoft Azure blob, fájl és a table storage.

Az AzCopy és a további adatokat telepítésével és az Azure platform az utasításokért lásd: [– első lépések az AzCopy parancssori segédprogrammal](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Ha használja a virtuális Gépet, amely be lett állítva a parancsfájlok által biztosított [Data Science virtuális gépek Azure-ban](virtual-machines.md), majd az AzCopy már telepítve van a virtuális gép.
> 
> [!NOTE]
> Egy teljes körű Bevezetés az Azure blob storage, tekintse meg a [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és [Azure Blob Service-ben](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Jelen dokumentum céljából feltételezzük, hogy rendelkezik Azure-előfizetéssel, egy tárfiókot és a kapcsolódó tárfiók-kulcsot az adott fiók. Adatok feltöltése/letöltése, előtt ismernie kell az Azure storage-fiók tárfióknév és fiókkulcs.

* Azure-előfizetés beállításával kapcsolatban lásd: [ingyenes egy hónapos próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* Storage-fiók létrehozásával és az első fiók és a kulcsadatokat: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Az AzCopy-parancs futtatása
Az AzCopy-parancsokat futtatni, nyisson meg egy parancsablakot, és keresse meg a számítógépen, ahol a végrehajtható AzCopy.exe megtalálható az AzCopy telepítési könyvtárára. 

Az AzCopy-parancsokat alapvető szintaxisa:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Adja hozzá az AzCopy telepítési hely a rendszer elérési útjához, és bármelyik könyvtárból futtassa a parancsokat. Alapértelmezés szerint az AzCopy telepítve van a *% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy* vagy *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Fájlok feltöltése az Azure-blobba
A fájl feltöltéséhez használja a következő parancsot:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Töltse le a fájlokat egy Azure-blobból
Fájlok letöltéséhez egy Azure-blobból, használja a következő parancsot:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Azure-tárolók közötti átvitel blobok
Azure-tárolók közötti átviteléhez a BLOB, a következő paranccsal:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Tippek az AzCopy használatával
> [!TIP]
> 1. Amikor **feltöltése** fájlok */S* feltölti a fájlokat rekurzív módon. Ez a paraméter nélkül alkönyvtárakban lévő fájlok nem lesznek feltöltve.  
> 2. Amikor **letöltése** fájl */S* a tároló rekurzív módon keres a megadott könyvtárban, és annak alkönyvtáraiban található összes fájl, vagy minden fájl, amely megfelel a megadott könyvtárban a megadott mintának és alkönyvtárai, letöltődnek.  
> 3. Nem adható meg egy **blobra fájl** letöltéséhez használja a */Source* paraméter. Egy adott fájl letöltéséhez, a letöltéséhez használja a blob nevét adja meg a */mintát* paraméter. **/S** paraméter segítségével keresse meg a fájl neve a minta rekurzív módon AzCopy rendelkezik. A minta paramétert az AzCopy letölti az összes ebben a könyvtárban.
> 
> 

