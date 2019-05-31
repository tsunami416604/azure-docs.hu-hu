---
title: Az AzCopy v10-adatok az Azure Files átvitelét |} A Microsoft Docs
description: Adatok áthelyezése az AzCopy és a file storage.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247109"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Adatok áthelyezése az AzCopy és a file storage 

Az AzCopy parancssori segédprogram, amely a blobok és a fájlok másolása, illetve a storage-fiók használatával. Ez a cikk tartalmazza, amelyek együttműködnek az Azure Files Példaparancsok.

Mielőtt elkezdené, tekintse meg a [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md) AzCopy letöltéséhez, és ismerkedjen meg az eszköz a cikk.

## <a name="create-file-shares"></a>Fájlmegosztások létrehozása

Az azcopyval `make` parancs használatával hozzon létre fájlmegosztást. A jelen szakaszban ismertetett példa egy nevű fájlmegosztást hoz létre `myfileshare`.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **Példa** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>Fájlok feltöltése

Az azcopyval `copy` paranccsal töltse fel a fájlokat és mappákat a helyi számítógépről.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl feltöltése
> * Mappa feltöltése
> * Fájlok feltöltése a helyettesítő karakterek használatával

> [!NOTE]
> Az AzCopy nem automatikusan kiszámítsa és tárolja a fájl md5 kivonatoló kódot. Ha azt szeretné, hogy ehhez az AzCopy, majd fűzze hozzá a `--put-md5` jelző minden egyes Másolás parancsra. Ezzel a módszerrel a fájl letöltése, ha az AzCopy kiszámítja egy MD5-kivonat letöltött adatok, és ellenőrzi, hogy az MD5-kivonat a fájl tárolása `Content-md5` tulajdonsága egyezést mutat a kiszámított kivonatát.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Példa** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>Mappa feltöltése

Ebben a példában egy mappát (és a mappában lévő fájlok) másolja át azt a fájlmegosztást. Ez egy mappát a fájlmegosztásban ugyanazzal a névvel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Példa** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Másolása egy mappába a fájlmegosztás belül, csak a parancs karakterláncot adja meg, hogy a mappa nevét.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Ha a fájlmegosztást ad meg, amely nem létezik a mappa nevét, az AzCopy ilyen nevű hoz létre egy új mappát.

### <a name="upload-the-contents-of-a-folder"></a>A mappa tartalmának feltöltése

Maga a tartalmazó mappa másolása a (*) helyettesítő karakter és szimbólum nélkül feltöltheti egy mappa tartalmát.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **Példa** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelző összes almappákban lévő fájlok feltöltéséhez.

## <a name="download-files"></a>Fájlok letöltése

Az azcopyval `copy` paranccsal letöltheti a fájlokat, mappákat és fájlt oszt meg a helyi számítógépen.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl letöltése
> * Töltse le a mappa
> * Fájlok letöltése a helyettesítő karakterek használatával

> [!NOTE]
> Ha a `Content-md5` tulajdonságának értéke egy fájl tartalmaz egy kivonatot, az AzCopy számítja ki a letöltött adatok MD5-kivonatot, és ellenőrzi, hogy a fájl tárolása az MD5-kivonat `Content-md5` tulajdonsága egyezést mutat a kiszámított kivonatát. Ezek az értékek nem egyeznek meg, ha a letöltés meghiúsul, ha felülbírálja ezt a viselkedést úgy, `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a Másolás parancsra.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Példa** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Töltse le a mappa

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **Példa** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

Ebben a példában lévő szkriptfájlokat eredmények `C:\myFolder\myFileShareFolder` , amely tartalmazza az összes letöltött fájl.

### <a name="download-the-contents-of-a-folder"></a>A mappa tartalmának letöltése

Egy mappa tartalmát anélkül, hogy maga a tartalmazó mappa másolása (*) helyettesítő karakter és szimbólum használatával töltheti le.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **Példa** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelző összes almappákban lévő fájlok letöltéséhez.

## <a name="next-steps"></a>További lépések

További példa található a következő cikkeket:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Adatok áthelyezése az AzCopy és a blob storage](storage-use-azcopy-blobs.md)

- [Adatok áthelyezése az AzCopy és az Amazon S3 gyűjtő](storage-use-azcopy-s3.md)

- [Konfigurálja, optimalizálhat és AzCopy hibaelhárítása](storage-use-azcopy-configure.md)