---
title: Adatok átvitele Azure Files-re a AzCopy v10 használatával | Microsoft Docs
description: Adatok átvitele a AzCopy és a file Storage szolgáltatással.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: eeac35cb96f5001e9ad318d8fe03927d0cd9394e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844812"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Adatok átvitele a AzCopy és a file Storage szolgáltatással 

A AzCopy olyan parancssori segédprogram, amellyel blobokat vagy fájlokat másolhat a Storage-fiókba, vagy átmásolhatja azokat. Ez a cikk a Azure Filesekkel használható példákat tartalmaz.

Mielőtt elkezdené, tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és az eszköz megismeréséhez.

## <a name="create-file-shares"></a>Fájlmegosztás létrehozása

A AzCopy `make` parancs használatával fájlmegosztás hozható létre. Az ebben a szakaszban szereplő példa egy nevű `myfileshare`fájlmegosztást hoz létre.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Példa** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Fájlok feltöltése

A AzCopy `copy` parancs használatával fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Fájlok feltöltése helyettesítő karakterek használatával

> [!NOTE]
> A AzCopy nem számítja ki automatikusan a fájl MD5 kivonatoló kódját. Ha azt szeretné, hogy a AzCopy ezt a lehetőséget, `--put-md5` fűzze hozzá a jelölőt az egyes másolási parancsokhoz. Így a fájl letöltésekor a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a fájl `Content-md5` tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Példa** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ez a példa egy könyvtárat (és az abban a könyvtárban található összes fájlt) másol egy fájlmegosztásba. Ennek eredményeképpen a fájlmegosztás egyik könyvtára azonos néven szerepel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Példa** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Ha a fájlmegosztást tartalmazó könyvtárba szeretne másolni, csak adja meg a könyvtár nevét a parancs karakterláncában.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Ha egy olyan könyvtár nevét adja meg, amely nem szerepel a fájlmegosztást, a AzCopy létrehoz egy új könyvtárat az adott névvel.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát feltöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Példa** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Fűzze hozzá `--recursive` a jelölőt az összes alkönyvtárban található fájlok feltöltéséhez.

## <a name="download-files"></a>Fájlok letöltése

A AzCopy `copy` parancs használatával fájlokat, címtárakat és fájlmegosztást tölthet le a helyi számítógépre.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl letöltése
> * Könyvtár letöltése
> * Fájlok letöltése helyettesítő karakterek használatával

> [!NOTE]
> Ha egy fájl `Content-md5` tulajdonságértékkivonatottartalmaz,aAzCopykiszámítjaaletöltöttadatokattartalmazóMD5-kivonatot,ésellenőrzi,hogyafájltulajdonságábantároltMD5-kivonatmegegyezik-ea`Content-md5` számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés meghiúsul, ha a hozzáfűzéssel `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a másolási paranccsal felülbírálja ezt a viselkedést.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Példa** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Könyvtár letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Példa** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Ez a példa egy nevű `C:\myDirectory\myFileShareDirectory` könyvtárat eredményez, amely az összes letöltött fájlt tartalmazza.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

A könyvtár tartalmát letöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Példa** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Az összes `--recursive` alkönyvtárban lévő fájlok letöltéséhez fűzze hozzá a jelölőt.

## <a name="next-steps"></a>További lépések

További példákat a következő cikkekben talál:

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)

- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)

- [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)