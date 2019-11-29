---
title: Adatok átvitele Azure Files-re a AzCopy v10 használatával | Microsoft Docs
description: Adatok átvitele a AzCopy és a file Storage szolgáltatással.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: fe2a6cb7943875732e6ce2fad630b2af69e6197b
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559147"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Adatok átvitele a AzCopy és a file Storage szolgáltatással 

A AzCopy olyan parancssori segédprogram, amellyel blobokat vagy fájlokat másolhat a Storage-fiókba, vagy átmásolhatja azokat. Ez a cikk a Azure Filesekkel használható példákat tartalmaz.

Mielőtt elkezdené, tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és az eszköz megismeréséhez.

## <a name="create-file-shares"></a>Fájlmegosztás létrehozása

A [azcopy make](storage-ref-azcopy-make.md) paranccsal fájlmegosztást hozhat létre. Az ebben a szakaszban szereplő példa egy `myfileshare`nevű fájlmegosztást hoz létre.

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Példa** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

A részletes dokumentációt a következő témakörben tekintheti meg: [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Fájlok feltöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Könyvtár tartalmának feltöltése
> * Adott fájl feltöltése

> [!NOTE]
> A AzCopy nem számítja ki automatikusan a fájl MD5 kivonatoló kódját. Ha azt szeretné, hogy a AzCopy ezt tegye, fűzze hozzá a `--put-md5` jelzőt az egyes másolási parancsokhoz. Így a fájl letöltésekor a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a fájl `Content-md5` tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal.

Részletes dokumentációs dokumentáció: [azcopy másolás](storage-ref-azcopy-copy.md).

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

A fájl elérési útja vagy fájlneve tetszőleges helyettesítő karakter (*) használatával is feltölthet egy fájlt. Például: `'C:\myDirectory\*.txt'`vagy `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ez a példa egy könyvtárat (és az abban a könyvtárban található összes fájlt) másol egy fájlmegosztásba. Ennek eredményeképpen a fájlmegosztás egyik könyvtára azonos néven szerepel.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Ha a fájlmegosztást tartalmazó könyvtárba szeretne másolni, csak adja meg a könyvtár nevét a parancs karakterláncában.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Ha egy olyan könyvtár nevét adja meg, amely nem szerepel a fájlmegosztást, a AzCopy létrehoz egy új könyvtárat az adott névvel.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát feltöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Az összes alkönyvtárban lévő fájlok feltöltéséhez fűzze hozzá a `--recursive` jelzőt.

### <a name="upload-specific-files"></a>Adott fájlok feltöltése

Megadhatja a teljes fájlnevet, vagy használhat részleges neveket helyettesítő karakterekkel (*).

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Pontosvesszővel (`;`) válassza el az egyes fájlneveket.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

Ebben a példában a AzCopy átviszi a `C:\myDirectory\photos` könyvtárat és a `C:\myDirectory\documents\myFile.txt` fájlt. A `C:\myDirectory\photos` könyvtárban lévő összes fájl átviteléhez meg kell adnia a `--recursive` lehetőséget.

A fájlokat a `--exclude-path` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket semicolin (`;`) használva válassza el.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a `--exclude-pattern` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és `--exclude-pattern` beállítások csak a fájlnevekre érvényesek, az elérési útra nem.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` kapcsolót a teljes könyvtár faszerkezetének beolvasásához, majd használja a `–include-pattern`, és adja meg `*.txt` az összes szövegfájl beolvasásához.

## <a name="download-files"></a>Fájlok letöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával fájlokat, címtárakat és fájlmegosztást tölthet le a helyi számítógépre.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl letöltése
> * Könyvtár letöltése
> * Könyvtár tartalmának letöltése
> * Adott fájlok letöltése

> [!NOTE]
> Ha egy fájl `Content-md5` tulajdonságának értéke kivonatot tartalmaz, a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a fájl `Content-md5` tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés meghiúsul, hacsak nem bírálja felül ezt a viselkedést `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a másolási parancshoz való hozzáfűzésével.

Részletes dokumentációs dokumentáció: [azcopy másolás](storage-ref-azcopy-copy.md).

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' '<local-file-path>'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Könyvtár letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' '<local-directory-path>' --recursive` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Ez a példa egy `C:\myDirectory\myFileShareDirectory` nevű könyvtárat eredményez, amely az összes letöltött fájlt tartalmazza.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

A könyvtár tartalmát letöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>' '<local-directory-path>/'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Az összes alkönyvtárban lévő fájlok letöltéséhez fűzze hozzá a `--recursive` jelzőt.

### <a name="download-specific-files"></a>Adott fájlok letöltése

Megadhatja a teljes fájlnevet, vagy használhat részleges neveket helyettesítő karakterekkel (*).

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Különálló fájlnevek elkülönítése semicolin (`;`) használatával.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name>?<SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Ebben a példában a AzCopy átviszi a `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` könyvtárat és a `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` fájlt. A `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` könyvtárban lévő összes fájl átviteléhez meg kell adnia a `--recursive` lehetőséget.

A fájlokat a `--exclude-path` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket semicolin (`;`) használva válassza el.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>?<SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a `--exclude-pattern` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és `--exclude-pattern` beállítások csak a fájlnevekre érvényesek, az elérési útra nem.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` kapcsolót a teljes könyvtár faszerkezetének beolvasásához, majd használja a `–include-pattern`, és adja meg `*.txt` az összes szövegfájl beolvasásához.

## <a name="copy-files-between-storage-accounts"></a>Fájlok másolása a Storage-fiókok között

A AzCopy használatával fájlokat másolhat más Storage-fiókokba. A másolási művelet szinkronban van, így amikor a parancs visszatér, ez azt jelzi, hogy az összes fájl másolása megtörtént.

A AzCopy [kiszolgálók](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) közötti [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)-kat használ, így az Adatmásolás közvetlenül a Storage-kiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. Az `AZCOPY_CONCURRENCY_VALUE` környezeti változó értékének megadásával növelheti a műveletek átviteli sebességét. További információ: az [átviteli sebesség optimalizálása](storage-use-azcopy-configure.md#optimize-throughput).

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl másolása másik Storage-fiókba
> * Könyvtár másolása másik Storage-fiókba
> * Fájlmegosztás másolása másik Storage-fiókba
> * Minden fájlmegosztás, könyvtár és fájl másolása egy másik Storage-fiókba

A részletes dokumentációt lásd: [azcopy másolás](storage-ref-azcopy-copy.md).

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

### <a name="copy-a-file-to-another-storage-account"></a>Fájl másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Könyvtár másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Fájlmegosztás másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Minden fájlmegosztás, könyvtár és fájl másolása egy másik Storage-fiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/?<SAS-token>' --recursive'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

Egy fájlmegosztás tartalmát egy másik fájlmegosztást is szinkronizálhatja. Egy fájlmegosztás könyvtárának tartalmát is szinkronizálhatja egy olyan könyvtár tartalmával, amely egy másik fájlmegosztást tartalmaz. A szinkronizálás egyirányú. Más szóval kiválaszthatja, hogy a két végpont melyik a forrás, és melyik a cél. A szinkronizálás a kiszolgálót kiszolgáló API-kra is használja.

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel. A AzCopy jelenlegi kiadása nem szinkronizál Azure Files és Blob Storage között.

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és az utolsó módosítás időbélyegét. Adja meg a `--delete-destination` opcionális jelzőt `true` vagy `prompt` értékre, hogy törölje a fájlokat a célhely könyvtárában, ha ezek a fájlok már nem léteznek a forrás-címtárban.

Ha a `--delete-destination` jelzőt úgy állítja be, hogy `true` AzCopy, a fájlok megadása nélkül törölje a fájlokat. Ha azt szeretné, hogy a AzCopy törlése előtt megjelenjen egy üzenet, állítsa a `--delete-destination` jelzőt `prompt`re.

A részletes dokumentációs dokumentáció: [azcopy Sync](storage-ref-azcopy-sync.md).

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Fájlmegosztás frissítése egy másik fájlmegosztás módosításaival

Az ebben a parancsban megjelenő első fájlmegosztás a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Könyvtár frissítése egy másik fájlmegosztás könyvtárának módosításaival

A parancsban megjelenő első könyvtár a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>Következő lépések

További példákat a következő cikkekben talál:

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)

- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)

- [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
