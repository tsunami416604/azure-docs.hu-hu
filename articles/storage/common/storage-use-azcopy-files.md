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
ms.openlocfilehash: 69d7136396c3d989e63b8956d3e703cc7f9666c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66687930"
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

Az azcopyval `copy` paranccsal töltse fel a fájlokat és könyvtárakat a helyi számítógépről.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl feltöltése
> * Egy könyvtárat feltöltése
> * Fájlok feltöltése a helyettesítő karakterek használatával

> [!NOTE]
> Az AzCopy nem automatikusan kiszámítsa és tárolja a fájl md5 kivonatoló kódot. Ha azt szeretné, hogy ehhez az AzCopy, majd fűzze hozzá a `--put-md5` jelző minden egyes Másolás parancsra. Ezzel a módszerrel a fájl letöltése, ha az AzCopy kiszámítja egy MD5-kivonat letöltött adatok, és ellenőrzi, hogy az MD5-kivonat a fájl tárolása `Content-md5` tulajdonsága egyezést mutat a kiszámított kivonatát.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **Példa** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-directory"></a>Egy könyvtárat feltöltése

Ebben a példában egy könyvtárat (és az összes fájl ebben a könyvtárban) másolja át azt a fájlmegosztást. Ez egy könyvtárat a fájlmegosztásban ugyanazzal a névvel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **Példa** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Másolja a fájlmegosztás a címtárhoz, csak adja meg annak a könyvtárnak a nevét a parancs karakterláncban.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

Ha egy nem létező könyvtár neve a fájlmegosztást ad meg, az AzCopy ilyen nevű hoz létre egy új könyvtárat.

### <a name="upload-the-contents-of-a-directory"></a>Töltse fel a tartalmát a címtár

Feltölthet egy könyvtár tartalmának másolása a tartalmazó könyvtár magát a (*) helyettesítő karakter és szimbólum nélkül.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Példa** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelző összes alárendelt könyvtárakban található fájlok feltöltéséhez.

## <a name="download-files"></a>Fájlok letöltése

Az azcopyval `copy` paranccsal letöltheti a fájlokat, könyvtárak és fájlok oszt meg a helyi számítógépen.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl letöltése
> * Letöltési könyvtár
> * Fájlok letöltése a helyettesítő karakterek használatával

> [!NOTE]
> Ha a `Content-md5` tulajdonságának értéke egy fájl tartalmaz egy kivonatot, az AzCopy számítja ki a letöltött adatok MD5-kivonatot, és ellenőrzi, hogy a fájl tárolása az MD5-kivonat `Content-md5` tulajdonsága egyezést mutat a kiszámított kivonatát. Ezek az értékek nem egyeznek meg, ha a letöltés meghiúsul, ha felülbírálja ezt a viselkedést úgy, `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a Másolás parancsra.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **Példa** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Letöltési könyvtár

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>" "<local-directory-path>" --recursive` |
| **Példa** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"  --recursive` |

Ebben a példában eredményez nevű könyvtárat `C:\myDirectory\myFileShareDirectory` , amely tartalmazza az összes letöltött fájl.

### <a name="download-the-contents-of-a-directory"></a>Egy könyvtár tartalmának letöltése

Egy könyvtár tartalmának másolása a tartalmazó könyvtár magát a (*) helyettesítő karakter és szimbólum nélkül töltheti le.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-directory-path>/"` |
| **Példa** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myDirectory"` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelző összes alárendelt könyvtárakban található fájlok letöltéséhez.

## <a name="next-steps"></a>További lépések

További példa található a következő cikkeket:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Adatok áthelyezése az AzCopy és a blob storage](storage-use-azcopy-blobs.md)

- [Adatok áthelyezése az AzCopy és az Amazon S3 gyűjtő](storage-use-azcopy-s3.md)

- [Konfigurálja, optimalizálhat és AzCopy hibaelhárítása](storage-use-azcopy-configure.md)