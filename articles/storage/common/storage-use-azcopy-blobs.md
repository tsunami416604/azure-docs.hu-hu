---
title: Adatok átvitele az Azure Blob Storage-ba vagy onnan az AzCopy v10 használatával | Microsoft Docs
description: Ez a cikk AzCopy-példákat tartalmaz, amelyek segítenek a tárolók létrehozásában, a fájlok másolásában és a könyvtárak szinkronizálásában a helyi fájlrendszerek és tárolók között.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b676c2647fbf7c93d271e1d7f68653452125e39b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137195"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Adatok átvitele a AzCopy és a blob Storage szolgáltatással

A AzCopy egy parancssori segédprogram, amellyel az adatok átmásolhatók a, a-ból vagy a Storage-fiókok között. Ez a cikk a blob Storage-hoz használható példaként szolgáló parancsokat tartalmaz.

> [!TIP]
> A cikkben szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

## <a name="get-started"></a>Bevezetés

Tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és a tárolási szolgáltatás engedélyezési hitelesítő adatainak megadásához szükséges módszerekről.

> [!NOTE]
> A cikkben szereplő példák azt feltételezik, hogy a `AzCopy login` parancs használatával hitelesítette a személyazonosságát. A AzCopy ezután az Azure AD-fiók használatával engedélyezi a blob Storage-beli adathozzáférését.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban.
>
> Például: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához használhatja a [azcopy make](storage-ref-azcopy-make.md) parancsot. Az ebben a szakaszban szereplő példák egy nevű `mycontainer`tárolót hoznak létre.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Például** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Példa** (hierarchikus névtér) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

A részletes dokumentációt a következő témakörben tekintheti meg: [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Fájlok feltöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Könyvtár tartalmának feltöltése 
> * Adott fájlok feltöltése

> [!TIP]
> A feltöltési műveletet opcionális jelzők használatával is megteheti. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |Fájlok feltöltése hozzáfűzési Blobként vagy Blobként.|**--BLOB típusú**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Feltöltés egy adott hozzáférési szintre (például az archiválási szintre).|**--Block-blob-réteg**=\[nincs\|gyors\|elérésű\|Archívum\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Például** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

A fájl elérési útja vagy fájlneve tetszőleges helyettesítő karakter (*) használatával is feltölthet egy fájlt. Például: `'C:\myDirectory\*.txt'`, vagy `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ez a példa egy könyvtárat (és az abban a könyvtárban található összes fájlt) egy blob-tárolóba másol. Ennek eredményeképpen a tároló egyik könyvtára azonos néven szerepel.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Például** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

A tárolóban lévő könyvtárba való másoláshoz csak adja meg a könyvtár nevét a parancs karakterláncában.

|    |     |
|--------|-----------|
| **Például** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Ha egy olyan könyvtár nevét adja meg, amely nem szerepel a tárolóban, a AzCopy létrehoz egy új könyvtárat a név alapján.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát feltöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Például** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Fűzze hozzá `--recursive` a jelölőt az összes alkönyvtárban található fájlok feltöltéséhez.

### <a name="upload-specific-files"></a>Adott fájlok feltöltése

Megadhatja a teljes fájlnevet, vagy használhat részleges neveket helyettesítő karakterekkel (*).

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Az egyes fájlneveket pontosvesszővel (`;`) válassza el egymástól.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Például** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Ebben a példában a AzCopy átviszi a `C:\myDirectory\photos` könyvtárat `C:\myDirectory\documents\myFile.txt` és a fájlt. Meg kell adnia a `--recursive` `C:\myDirectory\photos` könyvtárban található összes fájl átvitelének lehetőségét.

A fájlokat a `--exclude-path` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket a semicolin (`;`) használatával válassza el. 

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Például** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a `--exclude-pattern` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és `--exclude-pattern` a beállítások csak a fájlnevekre érvényesek, és nem az elérési útra.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` teljes könyvtár fájának beolvasására szolgáló lehetőséget, majd a és a `–include-pattern` érték megadásával `*.txt` töltse le az összes szövegfájlt.

## <a name="download-files"></a>Fájlok letöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával blobokat, címtárakat és tárolókat tölthet le a helyi számítógépre.

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
> |Fájlok automatikus kibontása.|**– Kibontás**|
> |Adja meg, hogy a másolással kapcsolatos naplóbejegyzések milyen részletesek legyenek.|**– a naplózási szintű**=\[\|figyelmeztetési\|hiba\|információja nincs\]|
> |Itt adhatja meg, hogy miként írja felül az ütköző fájlokat és blobokat a célhelyen.|**–**=\[igaz\|hamis\|ifSourceNewer\|-kérés felülírása\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Ha egy `Content-md5` blob tulajdonság értéke kivonatot tartalmaz, a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a blob `Content-md5` tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés meghiúsul, ha a hozzáfűzéssel `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a másolási paranccsal felülbírálja ezt a viselkedést.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Például** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Könyvtár letöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Például** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Ez a példa egy nevű `C:\myDirectory\myBlobDirectory` könyvtárat eredményez, amely az összes letöltött fájlt tartalmazza.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

A könyvtár tartalmát letöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Például** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Az összes `--recursive` alkönyvtárban lévő fájlok letöltéséhez fűzze hozzá a jelölőt.

### <a name="download-specific-files"></a>Adott fájlok letöltése

Megadhatja a teljes fájlnevet, vagy használhat részleges neveket helyettesítő karakterekkel (*).

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Különálló fájlnevek elkülönítése semicolin (`;`) használatával.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Például** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Ebben a példában a AzCopy átviszi a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` könyvtárat `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` és a fájlt. Meg kell adnia a `--recursive` `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` könyvtárban található összes fájl átvitelének lehetőségét.

A fájlokat a `--exclude-path` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket a semicolin (`;`) használatával válassza el.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Például** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a `--exclude-pattern` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és `--exclude-pattern` a beállítások csak a fájlnevekre érvényesek, és nem az elérési útra.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` teljes könyvtár fájának beolvasására szolgáló lehetőséget, majd a és a `–include-pattern` érték megadásával `*.txt` töltse le az összes szövegfájlt.

## <a name="copy-blobs-between-storage-accounts"></a>Blobok másolása tárfiókok között

A AzCopy a Blobok más Storage-fiókba való másolására is használható. A másolási művelet szinkronban van, így amikor a parancs visszatér, ez azt jelzi, hogy az összes fájl másolása megtörtént. 

A AzCopy [kiszolgálók](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) közötti [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)-kat használ, így az Adatmásolás közvetlenül a Storage-kiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. A `AZCOPY_CONCURRENCY_VALUE` környezeti változó értékének megadásával növelheti a műveletek átviteli sebességét. További információ: az [átviteli sebesség optimalizálása](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ez a forgatókönyv a jelenlegi kiadásban a következő korlátozásokkal rendelkezik.
>
> - Minden forrás URL-címhez hozzá kell fűzni egy SAS-tokent. Ha Azure Active Directory (AD) hitelesítő adatokat ad meg, kihagyhatja az SAS-tokent csak a cél URL-címről.
>-  A prémium szintű blokk blob Storage-fiókok nem támogatják a hozzáférési szinteket. Hagyja ki a blob hozzáférési szintjét a másolási műveletből a (z `s2s-preserve-access-tier` ) `false` értékre való beállításával (például: `--s2s-preserve-access-tier=false`).

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * BLOB másolása másik Storage-fiókba
> * Könyvtár másolása másik Storage-fiókba
> * Tároló másolása másik Storage-fiókba
> * Minden tároló, könyvtár és fájl másolása egy másik Storage-fiókba

Ezek a példák olyan fiókokkal is működnek, amelyek hierarchikus névtérrel rendelkeznek. A [több protokollon keresztüli hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé teszi, hogy ugyanazt az URL`blob.core.windows.net`-szintaxist () használja a fiókokon.

> [!TIP]
> A másolási műveletet opcionális jelzők használatával is megadhatja. Íme néhány példa.
>
> |Forgatókönyv|Jelző|
> |---|---|
> |Fájlok másolása hozzáfűzési Blobként vagy Blobként.|**--BLOB típusú**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Másolás adott hozzáférési szintre (például az archív szintre).|**--Block-blob-réteg**=\[nincs\|gyors\|elérésű\|Archívum\]|
> |Fájlok automatikus kibontása.|**--**=\[a gzip\|deflate kibontása\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>BLOB másolása másik Storage-fiókba

Használja ugyanazt az URL-szintaxist (`blob.core.windows.net`) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Például** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Könyvtár másolása másik Storage-fiókba

Használja ugyanazt az URL-szintaxist (`blob.core.windows.net`) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Például** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Tároló másolása másik Storage-fiókba

Használja ugyanazt az URL-szintaxist (`blob.core.windows.net`) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Például** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Minden tároló, könyvtár és blob másolása egy másik Storage-fiókba

Használja ugyanazt az URL-szintaxist (`blob.core.windows.net`) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Például** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

A helyi fájlrendszer tartalmát egy blob-tárolóval szinkronizálhatja. A tárolókat és a virtuális könyvtárakat is szinkronizálhatja egymással. A szinkronizálás egyirányú. Más szóval kiválaszthatja, hogy a két végpont melyik a forrás, és melyik a cél. A szinkronizálás a kiszolgálót kiszolgáló API-kra is használja. Az ebben a szakaszban bemutatott példák a hierarchikus névtérrel rendelkező fiókokkal is működnek. 

> [!NOTE]
> A AzCopy jelenlegi kiadása nem szinkronizál más források és célhelyek között (például: file Storage vagy Amazon Web Services (AWS) S3 gyűjtők).

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és az utolsó módosítás időbélyegét. Állítsa a `--delete-destination` választható jelzőt értékre, `true` vagy `prompt` törölje a fájlokat a célhely könyvtárában, ha ezek a fájlok már nem léteznek a forrás-címtárban.

Ha a jelzőt `--delete-destination` úgy állítja `true` be, hogy a AzCopy törölje a fájlokat, a kérés megadása nélkül. Ha azt szeretné, hogy a AzCopy törlése előtt megjelenjen egy üzenet, állítsa a `--delete-destination` jelölőt `prompt`a következőre:.

> [!NOTE]
> A véletlen törlés megelőzése érdekében ügyeljen arra, hogy a `--delete-destination=prompt|true` jelző használata előtt engedélyezze a [Soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkciót.

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

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Tároló frissítése egy helyi fájlrendszer módosításaival

Ebben az esetben a tároló a cél, a helyi fájlrendszer pedig a forrás. 

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Például** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Helyi fájlrendszer frissítése egy tároló módosításaival

Ebben az esetben a helyi fájlrendszer a cél, és a tároló a forrás.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Például** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Tároló frissítése egy másik tároló módosításaival

A parancsban megjelenő első tároló a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Például** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Könyvtár frissítése egy másik fájlmegosztás könyvtárának módosításaival

A parancsban megjelenő első könyvtár a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Például** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>További lépések

További példákat a következő cikkekben talál:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)

- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)

- [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
