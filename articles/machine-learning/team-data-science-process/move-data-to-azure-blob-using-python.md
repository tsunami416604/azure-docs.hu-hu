---
title: "Adatok áthelyezése, és az Azure Blob Storage használatával Python |} Microsoft Docs"
description: "Adatok áthelyezése Azure Blob Storage-tárolóba vagy onnan máshová Python használatával"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 24276252-b3dd-4edf-9e5d-f6803f8ccccc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: d7847f695a77ad469f56a20518cb979c41384d1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Adatok áthelyezése, és az Azure Blob Storage pythonos környezetekben
Ez a témakör ismerteti a listában, töltse fel, és töltse le a blobok a Python API használatával. A Python API-hoz megadott Azure SDK-t a következőket teheti:

* Tároló létrehozása
* Blobok feltöltése a tárolóba
* Blobok letöltése
* A tárolóban lévő blobok listázása
* Blob törlése

A Python API használatával kapcsolatos további információkért lásd: [használata a Blob Storage szolgáltatást Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md).

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

## <a name="upload-data-to-blob"></a>Adatok feltöltése a Blob
Adja hozzá a következő kódrészletet a Python kódját, amelyben programon keresztüli eléréséhez az Azure Storage kívánja felső részén:

    from azure.storage.blob import BlobService

A **BlobService** objektum lehetővé teszi, hogy a tárolók és blobok. Az alábbi kód létrehoz egy BlobService objektumot, a tárfiók nevét és a fiók kulcsot használ. Fiók neve és a fiókkulcsot cserélje le a valódi fiókot és kulcsot.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Az alábbi módszerekkel adatok feltöltése a blob:

1. PUT\_blokk\_blob\_a\_(feltölt egy fájlt a megadott elérési) elérési útja
2. PUT\_block_blob\_a\_(feltölt egy már megnyitott fájl vagy adatfolyam tartalma) fájlt
3. PUT\_blokk\_blob\_a\_bájt (feltöltések egy bájttömb)
4. PUT\_blokk\_blob\_a\_szöveg (feltölti az adott szöveges értéket a megadott kódolás használatával)

Az alábbi példakód egy tároló feltölt egy helyi fájlt:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Az alábbi példakód a blob storage egy helyi könyvtárban (kivéve a könyvtárak) minden fájl feltöltését:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>A Blob adatok letöltése
Az alábbi módszerekkel adatok egy blob töltheti le:

1. első\_blob\_való\_elérési útja
2. első\_blob\_való\_fájl
3. első\_blob\_való\_bájt
4. első\_blob\_való\_szöveg

Ezek a módszerek, hajtsa végre a szükséges adattömbösítő, ha az adatok mérete meghaladja a 64 MB.

Az alábbi példakód a tárolóban lévő blob tartalmát egy helyi fájl tölti le:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Az alábbi példakód összes BLOB tölt le egy tárolót. Lista használ\_elérhető blobok listájának lekérdezése a tárolóban lévő blobok és letölti azokat a helyi könyvtárat.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
