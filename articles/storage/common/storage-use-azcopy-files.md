---
title: Adatok átvitele Azure Files-re a AzCopy v10 használatával | Microsoft Docs
description: Adatok átvitele a AzCopy és a file Storage szolgáltatással.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 7f47dd05035772744fb212ef8914b25979af61e2
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137161"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Adatok átvitele a AzCopy és a file Storage szolgáltatással 

A AzCopy olyan parancssori segédprogram, amellyel blobokat vagy fájlokat másolhat a Storage-fiókba, vagy átmásolhatja azokat. Ez a cikk a Azure Filesekkel használható példákat tartalmaz.

Mielőtt elkezdené, tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és az eszköz megismeréséhez.

> [!TIP]
> A cikkben szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

## <a name="create-file-shares"></a>Fájlmegosztás létrehozása

A [azcopy make](storage-ref-azcopy-make.md) paranccsal fájlmegosztást hozhat létre. Az ebben a szakaszban szereplő példa egy nevű `myfileshare`fájlmegosztást hoz létre.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Például** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

A részletes dokumentációt a következő témakörben tekintheti meg: [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Fájlok feltöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Könyvtár tartalmának feltöltése
> * Adott fájl feltöltése

> [!TIP]
> A feltöltési műveletet opcionális jelzők használatával is megteheti. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |A hozzáférés-vezérlési listák (ACL-ek) másolása a fájlokkal együtt.|**--megőrzése-SMB-engedélyek**=\[True\|false\]|
> |Az SMB-tulajdonságok adatainak másolása a fájlokkal együtt.|**--megőrzése-SMB-info**=\[True\|false\]|
> |Fájlok feltöltése hozzáfűzési Blobként vagy Blobként.|**--BLOB típusú**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Feltöltés egy adott hozzáférési szintre (például az archiválási szintre).|**--Block-blob-réteg**=\[nincs\|gyors\|elérésű\|Archívum\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> A AzCopy nem számítja ki automatikusan a fájl MD5 kivonatoló kódját. Ha azt szeretné, hogy a AzCopy ezt a lehetőséget, `--put-md5` fűzze hozzá a jelölőt az egyes másolási parancsokhoz. Így a fájl letöltésekor a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a fájl `Content-md5` tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Például** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

A fájl elérési útja vagy fájlneve tetszőleges helyettesítő karakter (*) használatával is feltölthet egy fájlt. Például: `'C:\myDirectory\*.txt'`, vagy `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ez a példa egy könyvtárat (és az abban a könyvtárban található összes fájlt) másol egy fájlmegosztásba. Ennek eredményeképpen a fájlmegosztás egyik könyvtára azonos néven szerepel.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Például** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Ha a fájlmegosztást tartalmazó könyvtárba szeretne másolni, csak adja meg a könyvtár nevét a parancs karakterláncában.

|    |     |
|--------|-----------|
| **Például** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Ha egy olyan könyvtár nevét adja meg, amely nem szerepel a fájlmegosztást, a AzCopy létrehoz egy új könyvtárat az adott névvel.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát feltöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Például** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Fűzze hozzá `--recursive` a jelölőt az összes alkönyvtárban található fájlok feltöltéséhez.

### <a name="upload-specific-files"></a>Adott fájlok feltöltése

Megadhatja a teljes fájlnevet, vagy használhat részleges neveket helyettesítő karakterekkel (*).

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Az egyes fájlneveket pontosvesszővel (`;`) válassza el egymástól.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Például** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

Ebben a példában a AzCopy átviszi a `C:\myDirectory\photos` könyvtárat `C:\myDirectory\documents\myFile.txt` és a fájlt. Meg kell adnia a `--recursive` `C:\myDirectory\photos` könyvtárban található összes fájl átvitelének lehetőségét.

A fájlokat a `--exclude-path` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket a semicolin (`;`) használatával válassza el.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Például** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a `--exclude-pattern` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és `--exclude-pattern` a beállítások csak a fájlnevekre érvényesek, és nem az elérési útra.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` teljes könyvtár fájának beolvasására szolgáló lehetőséget, majd a és a `–include-pattern` érték megadásával `*.txt` töltse le az összes szövegfájlt.

## <a name="download-files"></a>Fájlok letöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával fájlokat, címtárakat és fájlmegosztást tölthet le a helyi számítógépre.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl letöltése
> * Könyvtár letöltése
> * Könyvtár tartalmának letöltése
> * Adott fájlok letöltése

> [!TIP]
> A letöltési műveletet opcionális jelzők használatával is megteheti. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |A hozzáférés-vezérlési listák (ACL-ek) másolása a fájlokkal együtt.|**--megőrzése-SMB-engedélyek**=\[True\|false\]|
> |Az SMB-tulajdonságok adatainak másolása a fájlokkal együtt.|**--megőrzése-SMB-info**=\[True\|false\]|
> |Fájlok automatikus kibontása.|**– Kibontás**|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Ha egy `Content-md5` fájl tulajdonságérték kivonatot tartalmaz, a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a fájl `Content-md5` tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés meghiúsul, ha a hozzáfűzéssel `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a másolási paranccsal felülbírálja ezt a viselkedést.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Például** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Könyvtár letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Például** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Ez a példa egy nevű `C:\myDirectory\myFileShareDirectory` könyvtárat eredményez, amely az összes letöltött fájlt tartalmazza.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

A könyvtár tartalmát letöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Például** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Az összes `--recursive` alkönyvtárban lévő fájlok letöltéséhez fűzze hozzá a jelölőt.

### <a name="download-specific-files"></a>Adott fájlok letöltése

Megadhatja a teljes fájlnevet, vagy használhat részleges neveket helyettesítő karakterekkel (*).

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Különálló fájlnevek elkülönítése semicolin (`;`) használatával.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Például** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Ebben a példában a AzCopy átviszi a `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` könyvtárat `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` és a fájlt. Meg kell adnia a `--recursive` `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` könyvtárban található összes fájl átvitelének lehetőségét.

A fájlokat a `--exclude-path` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket a semicolin (`;`) használatával válassza el.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Például** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a `--exclude-pattern` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és `--exclude-pattern` a beállítások csak a fájlnevekre érvényesek, és nem az elérési útra.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` teljes könyvtár fájának beolvasására szolgáló lehetőséget, majd a és a `–include-pattern` érték megadásával `*.txt` töltse le az összes szövegfájlt.

## <a name="copy-files-between-storage-accounts"></a>Fájlok másolása tárfiókok között

A AzCopy használatával fájlokat másolhat más Storage-fiókokba. A másolási művelet szinkronban van, így amikor a parancs visszatér, ez azt jelzi, hogy az összes fájl másolása megtörtént.

A AzCopy [kiszolgálók](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) közötti [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)-kat használ, így az Adatmásolás közvetlenül a Storage-kiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. A `AZCOPY_CONCURRENCY_VALUE` környezeti változó értékének megadásával növelheti a műveletek átviteli sebességét. További információ: az [átviteli sebesség optimalizálása](storage-use-azcopy-configure.md#optimize-throughput).

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl másolása másik Storage-fiókba
> * Könyvtár másolása másik Storage-fiókba
> * Fájlmegosztás másolása másik Storage-fiókba
> * Minden fájlmegosztás, könyvtár és fájl másolása egy másik Storage-fiókba

> [!TIP]
> A másolási műveletet opcionális jelzők használatával is megadhatja. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |A hozzáférés-vezérlési listák (ACL-ek) másolása a fájlokkal együtt.|**--megőrzése-SMB-engedélyek**=\[True\|false\]|
> |Az SMB-tulajdonságok adatainak másolása a fájlokkal együtt.|**--megőrzése-SMB-info**=\[True\|false\]|
> |Fájlok másolása hozzáfűzési Blobként vagy Blobként.|**--BLOB típusú**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Másolás adott hozzáférési szintre (például az archív szintre).|**--Block-blob-réteg**=\[nincs\|gyors\|elérésű\|Archívum\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Fájl másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Például** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Könyvtár másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Például** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Fájlmegosztás másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Például** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Minden fájlmegosztás, könyvtár és fájl másolása egy másik Storage-fiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Például** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

Egy fájlmegosztás tartalmát egy másik fájlmegosztást is szinkronizálhatja. Egy fájlmegosztás könyvtárának tartalmát is szinkronizálhatja egy olyan könyvtár tartalmával, amely egy másik fájlmegosztást tartalmaz. A szinkronizálás egyirányú. Más szóval kiválaszthatja, hogy a két végpont melyik a forrás, és melyik a cél. A szinkronizálás a kiszolgálót kiszolgáló API-kra is használja.

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel. A AzCopy jelenlegi kiadása nem szinkronizál Azure Files és Blob Storage között.

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és az utolsó módosítás időbélyegét. Állítsa a `--delete-destination` választható jelzőt értékre, `true` vagy `prompt` törölje a fájlokat a célhely könyvtárában, ha ezek a fájlok már nem léteznek a forrás-címtárban.

Ha a jelzőt `--delete-destination` úgy állítja `true` be, hogy a AzCopy törölje a fájlokat, a kérés megadása nélkül. Ha azt szeretné, hogy a AzCopy törlése előtt megjelenjen egy üzenet, állítsa a `--delete-destination` jelölőt `prompt`a következőre:.

> [!TIP]
> A szinkronizálási műveletet opcionális jelzők használatával is megadhatja. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |Itt adhatja meg, hogy a letöltéskor a szigorúan MD5-kivonatokat kell-e érvényesíteni.|**--pipa**=\[\|bejelentkezéses\|FailIfDifferent\|FailIfDifferentOrMissing\]|
> |Fájlok kizárása mintázat alapján.|**--kizárás – elérési út**|
> |Adja meg, hogy a szinkronizálással kapcsolatos naplóbejegyzések milyen részletesek legyenek.|**– a naplózási szintű**=\[\|figyelmeztetési\|hiba\|információja nincs\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-sync.md#options).

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Fájlmegosztás frissítése egy másik fájlmegosztás módosításaival

Az ebben a parancsban megjelenő első fájlmegosztás a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Például** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Könyvtár frissítése egy másik fájlmegosztás könyvtárának módosításaival

A parancsban megjelenő első könyvtár a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Például** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Fájlmegosztás frissítése a megosztási pillanatkép tartalmának megfelelően

Az ebben a parancsban megjelenő első fájlmegosztás a forrás. Az URI végén fűzze hozzá a karakterláncot `&sharesnapshot=` , majd a pillanatkép **datetime** értékét. 

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Például** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

További információ a megosztási pillanatképekről: [Azure Files-megosztási Pillanatképek áttekintése](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files).

## <a name="next-steps"></a>További lépések

További példákat a következő cikkekben talál:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)

- [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
