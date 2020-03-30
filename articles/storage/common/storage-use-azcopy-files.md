---
title: Adatok átvitele az Azure Files-ba vagy onnan az AzCopy v10 használatával | Microsoft dokumentumok
description: Adatok átvitele az AzCopy programmal és a fájltárolással.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 8aa0e5304825b3f016694a40b3fc1e176518237a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526688"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Adatátvitel átvitele az AzCopy programmal és a fájltárolással 

Az AzCopy egy parancssori segédprogram, amely segítségével blobok vagy fájlok másolása egy tárfiókba. Ez a cikk az Azure Files szolgáltatással dolgozó példaparancsokat tartalmazza.

Mielőtt elkezdené, tekintse meg az Első lépések az [AzCopy-t](storage-use-azcopy-v10.md) az AzCopy letöltéséhez és az eszköz megismeréséhez című cikkben.

## <a name="create-file-shares"></a>Fájlmegosztások létrehozása

Az [azcopy make](storage-ref-azcopy-make.md) paranccsal fájlmegosztást hozhat létre. Az ebben a szakaszban található `myfileshare`példa létrehoz egy fájlmegosztást.

> [!TIP]
> Az ebben a szakaszban szereplő példák az elérési út argumentumait egyszeres idézőjelekkel ('') mellékelik. A Windows parancshéj (cmd.exe) kivételével minden parancshéjban használjon egyszeres idézőjeleket. Ha Windows parancshéjat (cmd.exe) használ, az elérési út argumentumait idézőjelek ("") helyett idézőjelek (') közé kell mellékelni.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>'` |
| **Példa** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

A részletes referencia-dokumentumokról az [azcopy make című dokumentumban lehet.](storage-ref-azcopy-make.md)

## <a name="upload-files"></a>Fájlok feltöltése

Az [azcopy copy](storage-ref-azcopy-copy.md) paranccsal fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Könyvtár tartalmának feltöltése
> * Adott fájl feltöltése

> [!NOTE]
> AzACopy nem számítja ki és tárolja automatikusan a fájl md5 kivonatkódját. Ha azt szeretné, hogy az AzCopy `--put-md5` ezt tegye, akkor fűzze hozzá a jelzőt minden egyes másolási parancshoz. Így a fájl letöltésekor az AzCopy kiszámítja a letöltött adatok MD5 kivonatát, és ellenőrzi, hogy `Content-md5` a fájl tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal.

A részletes referenciadokumentumokról az [azcopy copy című dokumentumban](storage-ref-azcopy-copy.md)lehet.

> [!TIP]
> Az ebben a szakaszban szereplő példák az elérési út argumentumait egyszeres idézőjelekkel ('') mellékelik. A Windows parancshéj (cmd.exe) kivételével minden parancshéjban használjon egyszeres idézőjeleket. Ha Windows parancshéjat (cmd.exe) használ, az elérési út argumentumait idézőjelek ("") helyett idézőjelek (') közé kell mellékelni.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

A fájlt helyettesítő szimbólummal (*) is feltöltheti a fájl elérési útján a fájl elérési útján vagy nevében. Például: `'C:\myDirectory\*.txt'`vagy `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ez a példa egy könyvtárat (és a könyvtárösszes fájlt) egy fájlmegosztásba másol. Az eredmény egy könyvtár a fájlmegosztásban ugyanazzal a névvel.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Ha a fájlmegosztáson belüli könyvtárba szeretne másolni, csak adja meg a könyvtár nevét a parancskarakterláncban.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Ha olyan könyvtár nevét adja meg, amely nem létezik a fájlmegosztásban, az AzCopy ezzel a névvel hoz létre új könyvtárat.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát a helyettesítő karakter (*) szimbólum mal történő másolása nélkül is feltöltheti.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> A jelző `--recursive` hozzáfűzése a fájlok feltöltéséhez az összes alkönyvtárba.

### <a name="upload-specific-files"></a>Adott fájlok feltöltése

Megadhatja a teljes fájlneveket, vagy használhat helyettesítő karakterekkel (*) rendelkező részleges neveket.

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnév megadása

Használja az [azcopy](storage-ref-azcopy-copy.md) copy `--include-path` parancsot a beállítással. Az egyes fájlneveket pontosvesszővel (`;`) válassza el.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

Ebben a példában az `C:\myDirectory\photos` AzCopy `C:\myDirectory\documents\myFile.txt` átviszi a könyvtárat és a fájlt. Meg kell adnia a `--recursive` lehetőséget, `C:\myDirectory\photos` hogy át az összes fájlt a könyvtárban.

A `--exclude-path` beállításokkal fájlokat is kizárhat. További információ: [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy](storage-ref-azcopy-copy.md) copy `--include-pattern` parancsot a beállítással. Adja meg a helyettesítő karaktereket tartalmazó részleges neveket. Külön nevek segítségével egy`;`semicolin ( ).

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

A `--exclude-pattern` beállításokkal fájlokat is kizárhat. További információ: [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

A `--include-pattern` `--exclude-pattern` és a beállítások csak a fájlnevekre vonatkoznak, az elérési útra nem.  Ha egy könyvtárfában található összes szövegfájlt át szeretné `–recursive` másolni, használja a teljes könyvtárfa `–include-pattern` leválasztását, majd a és adja meg a lehetőséget `*.txt` az összes szövegfájl bemásolásához.

## <a name="download-files"></a>Fájlok letöltése

Az [azcopy copy](storage-ref-azcopy-copy.md) paranccsal fájlokat, könyvtárakat és fájlmegosztásokat tölthet le a helyi számítógépre.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl letöltése
> * Könyvtár letöltése
> * Könyvtár tartalmának letöltése
> * Adott fájlok letöltése

> [!NOTE]
> Ha `Content-md5` egy fájl tulajdonságértéke kivonatot tartalmaz, az AzCopy kiszámítja a letöltött adatok MD5-kivonatát, és ellenőrzi, `Content-md5` hogy a fájl tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés sikertelen `--check-md5=NoCheck` `--check-md5=LogOnly` lesz, hacsak nem bírálja felül ezt a viselkedést a hozzáfűzéssel vagy a másolási paranccsal.

A részletes referenciadokumentumokról az [azcopy copy című dokumentumban](storage-ref-azcopy-copy.md)lehet.

> [!TIP]
> Az ebben a szakaszban szereplő példák az elérési út argumentumait egyszeres idézőjelekkel ('') mellékelik. A Windows parancshéj (cmd.exe) kivételével minden parancshéjban használjon egyszeres idézőjeleket. Ha Windows parancshéjat (cmd.exe) használ, az elérési út argumentumait idézőjelek ("") helyett idézőjelek (') közé kell mellékelni.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Könyvtár letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Ez a példa egy `C:\myDirectory\myFileShareDirectory` könyvtár nevét, amely tartalmazza az összes letöltött fájlokat.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

A könyvtár tartalmát a helyettesítő karakter (*) szimbólum mal történő másolása nélkül is letöltheti.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> A jelző `--recursive` hozzáfűzése a fájlok letöltéséhez az összes alkönyvtárba.

### <a name="download-specific-files"></a>Adott fájlok letöltése

Megadhatja a teljes fájlneveket, vagy használhat helyettesítő karakterekkel (*) rendelkező részleges neveket.

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnév megadása

Használja az [azcopy](storage-ref-azcopy-copy.md) copy `--include-path` parancsot a beállítással. Külön egyedi fájlnevek segítségével semicolin (`;`).

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Ebben a példában az `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` AzCopy `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` átviszi a könyvtárat és a fájlt. Meg kell adnia a `--recursive` lehetőséget, `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` hogy át az összes fájlt a könyvtárban.

A `--exclude-path` beállításokkal fájlokat is kizárhat. További információ: [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy](storage-ref-azcopy-copy.md) copy `--include-pattern` parancsot a beállítással. Adja meg a helyettesítő karaktereket tartalmazó részleges neveket. Külön nevek segítségével egy`;`semicolin ( ).

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

A `--exclude-pattern` beállításokkal fájlokat is kizárhat. További információ: [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

A `--include-pattern` `--exclude-pattern` és a beállítások csak a fájlnevekre vonatkoznak, az elérési útra nem.  Ha egy könyvtárfában található összes szövegfájlt át szeretné `–recursive` másolni, használja a teljes könyvtárfa `–include-pattern` leválasztását, majd a és adja meg a lehetőséget `*.txt` az összes szövegfájl bemásolásához.

## <a name="copy-files-between-storage-accounts"></a>Fájlok másolása tárfiókok között

Az AzCopy segítségével fájlokat más tárfiókokba másolhat. A másolási művelet szinkron, így amikor a parancs visszatér, az azt jelzi, hogy az összes fájl másolása meglett.

Az AzCopy [kiszolgáló-kiszolgáló](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API-kat](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)használ, így az adatok közvetlenül a tárolókiszolgálók között lesznek másolva. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. Ezeknek a műveleteknek az átviteli teljesítményét a `AZCOPY_CONCURRENCY_VALUE` környezeti változó értékének beállításával növelheti. További információ: [Az átviteli átatól optimalizálása](storage-use-azcopy-configure.md#optimize-throughput).

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl másolása másik tárfiókba
> * Könyvtár másolása másik tárfiókba
> * Fájlmegosztás másolása másik tárfiókba
> * Az összes fájlmegosztás, könyvtár és fájl másolása másik tárfiókba

A részletes referencia-dokumentumokról az [azcopy copy című dokumentumban](storage-ref-azcopy-copy.md)lehet.

> [!TIP]
> Az ebben a szakaszban szereplő példák az elérési út argumentumait egyszeres idézőjelekkel ('') mellékelik. A Windows parancshéj (cmd.exe) kivételével minden parancshéjban használjon egyszeres idézőjeleket. Ha Windows parancshéjat (cmd.exe) használ, az elérési út argumentumait idézőjelek ("") helyett idézőjelek (') közé kell mellékelni.

### <a name="copy-a-file-to-another-storage-account"></a>Fájl másolása másik tárfiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Könyvtár másolása másik tárfiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Fájlmegosztás másolása másik tárfiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Az összes fájlmegosztás, könyvtár és fájl másolása másik tárfiókba

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

A fájlmegosztás tartalmát szinkronizálhatja egy másik fájlmegosztással. A fájlmegosztásban lévő könyvtár tartalmát szinkronizálhatja egy másik fájlmegosztásban található könyvtár tartalmával is. A szinkronizálás egyirányú. Más szóval kiválaszthatja, hogy a két végpont közül melyik a forrás, és melyik a cél. A szinkronizálás kiszolgálói API-kat is használ.

> [!NOTE]
> Jelenleg ez a forgatókönyv csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel. Az AzCopy jelenlegi kiadása nem szinkronizálja az Azure Files és a Blob Storage között.

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és a utoljára módosított időbélyegeket. Állítsa `--delete-destination` be a választható `true` jelzőt a célkönyvtárban lévő fájlok értékére vagy `prompt` törlésére, ha ezek a fájlok már nem léteznek a forráskönyvtárban.

Ha a `--delete-destination` jelzőt `true` AzCopy -re állítja, akkor kérdés megadása nélkül törli a fájlokat. Ha azt szeretné, hogy az AzCopy töröljön `--delete-destination` egy `prompt`fájlt, állítsa a jelzőt a beállításra.

A részletes referencia-dokumentumokról az [azcopy sync című témakörben lehet.](storage-ref-azcopy-sync.md)

> [!TIP]
> Az ebben a szakaszban szereplő példák az elérési út argumentumait egyszeres idézőjelekkel ('') mellékelik. A Windows parancshéj (cmd.exe) kivételével minden parancshéjban használjon egyszeres idézőjeleket. Ha Windows parancshéjat (cmd.exe) használ, az elérési út argumentumait idézőjelek ("") helyett idézőjelek (') közé kell mellékelni.

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Fájlmegosztás frissítése másik fájlmegosztás módosításaival

A parancsban megjelenő első fájlmegosztás a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Könyvtár frissítése egy másik fájlmegosztás könyvtárának módosításaival

A parancsban megjelenő első könyvtár a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name>?<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="next-steps"></a>További lépések

További példák a következő cikkek bármelyikében:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Adatok átvitele az AzCopy és blob tárházával](storage-use-azcopy-blobs.md)

- [Adatátvitel az AzCopy és az Amazon S3 gyűjtőkkel](storage-use-azcopy-s3.md)

- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)
