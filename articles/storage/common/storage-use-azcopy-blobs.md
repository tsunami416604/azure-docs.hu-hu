---
title: Adatok átvitele az Azure Blob-tárhelyre vagy az Azure Blob-tárhelyről az AzCopy v10 használatával | Microsoft dokumentumok
description: Ez a cikk az AzCopy példaparancsok gyűjteményét tartalmazza, amelyek segítségével tárolókat hozhat létre, fájlokat másolhat, és könyvtárakat szinkronizálhat a helyi fájlrendszerek és tárolók között.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 73685f124f93bb541f33b3b70727d90ce22b3cdd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263437"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Adatok átvitele az AzCopy és blob tárhellyel

Az AzCopy egy parancssori segédprogram, amely segítségével adatokat másolhat, onnan vagy a tárfiókok között. Ez a cikk a Blob storage szolgáltatással dolgozó példaparancsokat tartalmazza.

> [!TIP]
> Az ebben a cikkben szereplő példák az elérési út argumentumait egyszeres idézőjelekkel (') mellékelik. A Windows parancshéj (cmd.exe) kivételével minden parancshéjban használjon egyszeres idézőjeleket. Ha Windows parancshéjat (cmd.exe) használ, az elérési út argumentumait idézőjelek ("") helyett idézőjelek (') közé kell mellékelni.

## <a name="get-started"></a>Bevezetés

Tekintse meg az AzCopy letöltéséhez az [AzCopy](storage-use-azcopy-v10.md) letöltéséhez és a storage-szolgáltatás engedélyezési hitelesítő adatainak megadásához szükséges útmutatót.

> [!NOTE]
> A cikkben szereplő példák feltételezik, hogy a `AzCopy login` parancs használatával hitelesítette az identitását. AzAzCopy ezután az Azure AD-fiók használatával engedélyezi az adatokhoz való hozzáférést a Blob storage-ban.
>
> Ha inkább egy SAS-jogkivonatot használ a blobadatokhoz való hozzáférés engedélyezéséhez, akkor hozzáfűzheti a jogkivonatot az erőforrás URL-címéhez az egyes AzCopy-parancsokban.
>
> Például: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tároló létrehozása

Az [azcopy make](storage-ref-azcopy-make.md) parancs segítségével tárolót hozhat létre. Az ebben a szakaszban `mycontainer`szereplő példák egy tárolónevű tárolót hoznak létre.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Példa** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Példa** (hierarchikus névtér) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

A részletes referencia-dokumentumokról az [azcopy make című dokumentumban lehet.](storage-ref-azcopy-make.md)

## <a name="upload-files"></a>Fájlok feltöltése

Az [azcopy copy](storage-ref-azcopy-copy.md) paranccsal fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Könyvtár tartalmának feltöltése 
> * Adott fájlok feltöltése

> [!TIP]
> A feltöltési műveletet opcionális jelzőkkel módosíthatja. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |Fájlok feltöltése blobok hozzáfűzése ként vagy lapblobként.|**--blob-típusú**=\[BlockBlob\|\|PageBlob AppendBlob\]|
> |Töltsön fel egy adott hozzáférési szintre (például az archív szintre).|**--block-blob-tier**=\[\|Nincs\|\|Hot Cool Archívum\]|
> |A fájlok automatikus kibontása.|**--decompress**=\[gzip\|leereszt\]|
> 
> A teljes listát a [Beállítások menüben láthatja.](storage-ref-azcopy-copy.md#options)

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

A fájlt helyettesítő szimbólummal (*) is feltöltheti a fájl elérési útján a fájl elérési útján vagy nevében. Például: `'C:\myDirectory\*.txt'`vagy `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ez a példa egy könyvtárat (és a könyvtárban lévő összes fájlt) egy blobtárolóba másolja. Az eredmény egy könyvtár a tárolóban az azonos nevű.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Ha a tárolón belüli könyvtárba szeretne másolni, csak adja meg a könyvtár nevét a parancskarakterláncban.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Ha olyan könyvtár nevét adja meg, amely nem létezik a tárolóban, az AzCopy ezzel a névvel hoz létre új könyvtárat.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát a helyettesítő karakter (*) szimbólum mal történő másolása nélkül is feltöltheti.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> A jelző `--recursive` hozzáfűzése a fájlok feltöltéséhez az összes alkönyvtárba.

### <a name="upload-specific-files"></a>Adott fájlok feltöltése

Megadhatja a teljes fájlneveket, vagy használhat helyettesítő karakterekkel (*) rendelkező részleges neveket.

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnév megadása

Használja az [azcopy](storage-ref-azcopy-copy.md) copy `--include-path` parancsot a beállítással. Az egyes fájlneveket pontosvesszővel (`;`) válassza el.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Ebben a példában az `C:\myDirectory\photos` AzCopy `C:\myDirectory\documents\myFile.txt` átviszi a könyvtárat és a fájlt. Meg kell adnia a `--recursive` lehetőséget, `C:\myDirectory\photos` hogy át az összes fájlt a könyvtárban.

A `--exclude-path` beállításokkal fájlokat is kizárhat. További információ: [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy](storage-ref-azcopy-copy.md) copy `--include-pattern` parancsot a beállítással. Adja meg a helyettesítő karaktereket tartalmazó részleges neveket. Külön nevek segítségével egy`;`semicolin ( ). 

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

A `--exclude-pattern` beállításokkal fájlokat is kizárhat. További információ: [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

A `--include-pattern` `--exclude-pattern` és a beállítások csak a fájlnevekre vonatkoznak, az elérési útra nem.  Ha egy könyvtárfában található összes szövegfájlt át szeretné `–recursive` másolni, használja a teljes könyvtárfa `–include-pattern` leválasztását, majd a és adja meg a lehetőséget `*.txt` az összes szövegfájl bemásolásához.

## <a name="download-files"></a>Fájlok letöltése

Az [azcopy copy](storage-ref-azcopy-copy.md) paranccsal blobokat, könyvtárakat és tárolókat tölthet le a helyi számítógépre.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl letöltése
> * Könyvtár letöltése
> * Könyvtár tartalmának letöltése
> * Adott fájlok letöltése

> [!TIP]
> A letöltési műveletet opcionális jelzőkkel módosíthatja. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |A fájlok automatikus kibontása.|**--decompress**=\[gzip\|leereszt\]|
> |Adja meg, hogy milyen részletesen kell megadnia a másolással kapcsolatos naplóbejegyzéseket.|**--log-level**=\[\|FIGYELMEZTETÉS\|\|HIBA INFORMÁCIÓ NINCS\]|
> |Adja meg, hogy felülírhatja-e az ütköző fájlokat és blobokat a célhelyen.|**--a**=\[true\|\|false ifSourceNewer\|parancssor imitomára történő felülírása\]|
> 
> A teljes listát a [Beállítások menüben láthatja.](storage-ref-azcopy-copy.md#options)

> [!NOTE]
> Ha `Content-md5` egy blob tulajdonságértéke kivonatot tartalmaz, az AzCopy kiszámítja a letöltött adatok MD5-kivonatát, és ellenőrzi, `Content-md5` hogy a blob tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés sikertelen `--check-md5=NoCheck` `--check-md5=LogOnly` lesz, hacsak nem bírálja felül ezt a viselkedést a hozzáfűzéssel vagy a másolási paranccsal.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Könyvtár letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Ez a példa egy `C:\myDirectory\myBlobDirectory` könyvtár nevét, amely tartalmazza az összes letöltött fájlokat.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

A könyvtár tartalmát a helyettesítő karakter (*) szimbólum mal történő másolása nélkül is letöltheti.

> [!NOTE]
> Jelenleg ez a forgatókönyv csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> A jelző `--recursive` hozzáfűzése a fájlok letöltéséhez az összes alkönyvtárba.

### <a name="download-specific-files"></a>Adott fájlok letöltése

Megadhatja a teljes fájlneveket, vagy használhat helyettesítő karakterekkel (*) rendelkező részleges neveket.

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnév megadása

Használja az [azcopy](storage-ref-azcopy-copy.md) copy `--include-path` parancsot a beállítással. Külön egyedi fájlnevek segítségével semicolin (`;`).

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Ebben a példában az `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` AzCopy `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` átviszi a könyvtárat és a fájlt. Meg kell adnia a `--recursive` lehetőséget, `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` hogy át az összes fájlt a könyvtárban.

A `--exclude-path` beállításokkal fájlokat is kizárhat. További információ: [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy](storage-ref-azcopy-copy.md) copy `--include-pattern` parancsot a beállítással. Adja meg a helyettesítő karaktereket tartalmazó részleges neveket. Külön nevek segítségével egy`;`semicolin ( ).

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

A `--exclude-pattern` beállításokkal fájlokat is kizárhat. További információ: [azcopy copy reference](storage-ref-azcopy-copy.md) docs.

A `--include-pattern` `--exclude-pattern` és a beállítások csak a fájlnevekre vonatkoznak, az elérési útra nem.  Ha egy könyvtárfában található összes szövegfájlt át szeretné `–recursive` másolni, használja a teljes könyvtárfa `–include-pattern` leválasztását, majd a és adja meg a lehetőséget `*.txt` az összes szövegfájl bemásolásához.

## <a name="copy-blobs-between-storage-accounts"></a>Blobok másolása tárfiókok között

Az AzCopy segítségével blobokat más tárfiókokba másolhat. A másolási művelet szinkron, így amikor a parancs visszatér, az azt jelzi, hogy az összes fájl másolása meglett. 

Az AzCopy [kiszolgáló-kiszolgáló](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API-kat](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)használ, így az adatok közvetlenül a tárolókiszolgálók között lesznek másolva. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. Ezeknek a műveleteknek az átviteli teljesítményét a `AZCOPY_CONCURRENCY_VALUE` környezeti változó értékének beállításával növelheti. További információ: [Az átviteli átatól optimalizálása](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ebben a forgatókönyvben a következő korlátozások az aktuális kiadás.
>
> - Minden forrás URL-címhez hozzá kell fűznie egy SAS-jogkivonatot. Ha az Azure Active Directory (AD) használatával adja meg az engedélyezési hitelesítő adatokat, a SAS-jogkivonatot csak a cél URL-címről hagyhatja ki.
>-  Prémium szintű blokkblob-tárfiókok nem támogatják a hozzáférési szinteket. A blob hozzáférési szintjének kihagyása a `s2s-preserve-access-tier` `false` másolási műveletből a (például: `--s2s-preserve-access-tier=false`) beállításával.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Blob másolása másik tárfiókba
> * Könyvtár másolása másik tárfiókba
> * Tároló másolása másik tárfiókba
> * Az összes tároló, könyvtár és fájl másolása egy másik tárfiókba

Ezek a példák hierarchikus névtérrel rendelkező fiókokkal is működnek. [A Data Lake Storage többprotokollos hozzáférése](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé`blob.core.windows.net`teszi, hogy ugyanazokat az URL-szintaxist ( ) használja ezeken a fiókokon.

> [!TIP]
> A másolási műveletet opcionális jelzőkkel módosíthatja. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |Fájlok másolása blobok hozzáfűzése ként vagy lapblobként.|**--blob-típusú**=\[BlockBlob\|\|PageBlob AppendBlob\]|
> |Másolás egy adott hozzáférési szintre (például az archív szintre).|**--block-blob-tier**=\[\|Nincs\|\|Hot Cool Archívum\]|
> |A fájlok automatikus kibontása.|**--decompress**=\[gzip\|leereszt\]|
> 
> A teljes listát a [Beállítások menüben láthatja.](storage-ref-azcopy-copy.md#options)

### <a name="copy-a-blob-to-another-storage-account"></a>Blob másolása másik tárfiókba

Ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja hierarchikus névtérrel rendelkező fiókokhoz.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Könyvtár másolása másik tárfiókba

Ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja hierarchikus névtérrel rendelkező fiókokhoz.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Tároló másolása másik tárfiókba

Ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja hierarchikus névtérrel rendelkező fiókokhoz.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Az összes tároló, könyvtár és blob másolása egy másik tárfiókba

Ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja hierarchikus névtérrel rendelkező fiókokhoz.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

Szinkronizálhatja a helyi fájlrendszer tartalmát egy blob tárolóval. A tárolókat és a virtuális könyvtárakat is szinkronizálhatja egymással. A szinkronizálás egyirányú. Más szóval kiválaszthatja, hogy a két végpont közül melyik a forrás, és melyik a cél. A szinkronizálás kiszolgálói API-kat is használ. Az ebben a szakaszban bemutatott példák hierarchikus névtérrel rendelkező fiókokkal is működnek. 

> [!NOTE]
> Az AzCopy jelenlegi kiadása nem szinkronizálmás források és célállomások között (például: Fájltárolás vagy Amazon Web Services (AWS) S3 gyűjtők).

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és a utoljára módosított időbélyegeket. Állítsa `--delete-destination` be a választható `true` jelzőt a célkönyvtárban lévő fájlok értékére vagy `prompt` törlésére, ha ezek a fájlok már nem léteznek a forráskönyvtárban.

Ha a `--delete-destination` jelzőt `true` AzCopy -re állítja, akkor kérdés megadása nélkül törli a fájlokat. Ha azt szeretné, hogy az AzCopy töröljön `--delete-destination` egy `prompt`fájlt, állítsa a jelzőt a beállításra.

> [!NOTE]
> A véletlen törlés elkerülése érdekében a `--delete-destination=prompt|true` jelző használata előtt engedélyezze a [törlési](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkciót.

> [!TIP]
> A szinkronizálási műveletet választható jelzők használatával módosíthatja. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |Adja meg, hogy a szigorúan MD5-kihálat hogyan kell érvényesíteni a letöltés során.|**--check-md5**=\[NoCheck\|\|LogOnly\|FailIfDifferent FailIfDifferentOrMissing\]|
> |A fájlok kizárása minta alapján.|**--kizárás-útvonal**|
> |Adja meg, hogy milyen részletesen szeretné a szinkronizálással kapcsolatos naplóbejegyzéseket.|**--log-level**=\[\|FIGYELMEZTETÉS\|\|HIBA INFORMÁCIÓ NINCS\]|
> 
> A teljes listát a [Beállítások menüben láthatja.](storage-ref-azcopy-sync.md#options)

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Tároló frissítése helyi fájlrendszer módosításaival

Ebben az esetben a tároló a cél, és a helyi fájlrendszer a forrás. 

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Helyi fájlrendszer frissítése tároló módosításaival

Ebben az esetben a helyi fájlrendszer a cél, a tároló pedig a forrás.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Példa** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Tároló frissítése egy másik tároló ban végrehajtott módosításokkal

A parancsban megjelenő első tároló a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Könyvtár frissítése egy másik fájlmegosztás könyvtárának módosításaival

A parancsban megjelenő első könyvtár a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>További lépések

További példák a következő cikkek bármelyikében:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)

- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)

- [Adatátvitel az AzCopy és az Amazon S3 gyűjtőkkel](storage-use-azcopy-s3.md)

- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)
