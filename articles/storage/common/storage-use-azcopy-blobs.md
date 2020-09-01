---
title: Adatok átvitele az Azure Blob Storage-ba vagy onnan az AzCopy v10 használatával | Microsoft Docs
description: Ez a cikk AzCopy-példákat tartalmaz, amelyek segítenek a tárolók létrehozásában, a fájlok másolásában és a könyvtárak szinkronizálásában a helyi fájlrendszerek és tárolók között.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 7ff8f3d18564140b4654b1591eec5c0e1f40b7cf
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077908"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Adatok átvitele a AzCopy és a blob Storage szolgáltatással

A AzCopy egy parancssori segédprogram, amellyel az adatok átmásolhatók a, a-ból vagy a Storage-fiókok között. Ez a cikk a blob Storage-hoz használható példaként szolgáló parancsokat tartalmaz.

> [!TIP]
> A cikkben szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

## <a name="get-started"></a>Bevezetés

Tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és a tárolási szolgáltatás engedélyezési hitelesítő adatainak megadásához szükséges módszerekről.

> [!NOTE]
> A cikkben szereplő példák azt feltételezik, hogy a parancs használatával hitelesítette a személyazonosságát `AzCopy login` . A AzCopy ezután az Azure AD-fiók használatával engedélyezi a blob Storage-beli adathozzáférését.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban.
>
> Például: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához használhatja a [azcopy make](storage-ref-azcopy-make.md) parancsot. Az ebben a szakaszban szereplő példák egy nevű tárolót hoznak létre `mycontainer` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Példa** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
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
> |Forgatókönyv|Jelölő|
> |---|---|
> |Fájlok feltöltése hozzáfűző blobként vagy lapblobként.|**– blob típusú** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
> |Feltöltés egy adott hozzáférési szintre (például az archív szintre).|**--Block-blob-réteg** = \[ Nincs \| gyors elérésű \| \| Archívum\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

A fájl elérési útja vagy fájlneve tetszőleges helyettesítő karakter (*) használatával is feltölthet egy fájlt. Például: `'C:\myDirectory\*.txt'` , vagy `C:\my*\*.txt` .

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ez a példa egy könyvtárat (és az abban a könyvtárban található összes fájlt) egy blob-tárolóba másol. Ennek eredményeképpen a tároló egyik könyvtára azonos néven szerepel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

A tárolóban lévő könyvtárba való másoláshoz csak adja meg a könyvtár nevét a parancs karakterláncában.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Ha egy olyan könyvtár nevét adja meg, amely nem szerepel a tárolóban, a AzCopy létrehoz egy új könyvtárat a név alapján.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát feltöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelölőt az összes alkönyvtárban található fájlok feltöltéséhez.

### <a name="upload-specific-files"></a>Adott fájlok feltöltése

Az adott fájlokat feltöltheti a teljes fájlnevekkel, a részleges nevekkel, helyettesítő karakterekkel (*), vagy dátum és idő használatával.

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Az egyes fájlneveket pontosvesszővel () válassza el egymástól `;` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Ebben a példában a AzCopy átviszi a `C:\myDirectory\photos` könyvtárat és a `C:\myDirectory\documents\myFile.txt` fájlt. Meg kell adnia a `--recursive` könyvtárban található összes fájl átvitelének lehetőségét `C:\myDirectory\photos` .

A fájlokat a lehetőség használatával is kizárhatja `--exclude-path` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket a semicolin () használatával válassza el `;` . 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a lehetőség használatával is kizárhatja `--exclude-pattern` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és a `--exclude-pattern` beállítások csak a fájlnevekre érvényesek, és nem az elérési útra.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` teljes könyvtár fájának beolvasására szolgáló lehetőséget, majd a `–include-pattern` és a érték megadásával töltse le az `*.txt` összes szövegfájlt.

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>A dátum és idő után módosított fájlok feltöltése 

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-after` kapcsolóval. A dátumot és az időt ISO-8601 formátumban kell megadni (például: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

Részletes információk: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

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
> |Forgatókönyv|Jelölő|
> |---|---|
> |Fájlok automatikus kibontása.|**– Kibontás**|
> |Adja meg, hogy a másolással kapcsolatos naplóbejegyzések milyen részletesek legyenek.|**– naplózási szint** = \[ FIGYELMEZTETÉSi \| hiba – \| \| nincs\]|
> |Itt adhatja meg, hogy miként írja felül az ütköző fájlokat és blobokat a célhelyen.|**– felülírás** = \[ igaz \| hamis \| ifSourceNewer- \| kérés\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Ha `Content-md5` egy blob tulajdonság értéke kivonatot tartalmaz, a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a blob tulajdonságában tárolt MD5-kivonat megegyezik-e `Content-md5` a számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés meghiúsul, ha a hozzáfűzéssel `--check-md5=NoCheck` vagy a másolási paranccsal felülbírálja ezt a viselkedést `--check-md5=LogOnly` .

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Könyvtár letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Ez a példa egy nevű könyvtárat eredményez, `C:\myDirectory\myBlobDirectory` amely az összes letöltött fájlt tartalmazza.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

A könyvtár tartalmát letöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Az `--recursive` összes alkönyvtárban lévő fájlok letöltéséhez fűzze hozzá a jelölőt.

### <a name="download-specific-files"></a>Adott fájlok letöltése

Az adott fájlokat letöltheti a teljes fájlnevekkel, a részleges nevekkel, helyettesítő karakterekkel (*), vagy dátum és idő használatával. 

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Különálló fájlnevek elkülönítése semicolin () használatával `;` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Ebben a példában a AzCopy átviszi a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` könyvtárat és a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` fájlt. Meg kell adnia a `--recursive` könyvtárban található összes fájl átvitelének lehetőségét `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` .

A fájlokat a lehetőség használatával is kizárhatja `--exclude-path` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket a semicolin () használatával válassza el `;` .

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a lehetőség használatával is kizárhatja `--exclude-pattern` . További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és a `--exclude-pattern` beállítások csak a fájlnevekre érvényesek, és nem az elérési útra.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` teljes könyvtár fájának beolvasására szolgáló lehetőséget, majd a `–include-pattern` és a érték megadásával töltse le az `*.txt` összes szövegfájlt.

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>A dátum és idő után módosított fájlok letöltése 

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-after` kapcsolóval. A dátumot és az időt ISO-8601 formátumban kell megadni (például: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

Részletes információk: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="download-previous-versions-of-a-blob"></a>BLOB korábbi verzióinak letöltése

Ha engedélyezte a [Blobok verziószámozását](../blobs/versioning-enable.md), letöltheti a Blobok egy vagy több korábbi verzióját is. 

Először hozzon létre egy szövegfájlt, amely tartalmazza a [verzió-azonosítók](../blobs/versioning-overview.md)listáját. Minden verzióazonosítónak külön sorban kell szerepelnie. Például: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

Ezután használja a [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--list-of-versions` kapcsolóval. Itt adhatja meg a verziók listáját tartalmazó szövegfájl helyét (például: `D:\\list-of-versions.txt` ).  

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

Az egyes letöltött fájlok neve a verzióazonosító nevével kezdődik. 

## <a name="copy-blobs-between-storage-accounts"></a>Blobok másolása tárfiókok között

Az AzCopyval blobokat másolhat át más tárfiókokba. A másolási művelet szinkron módon megy végbe, ezért a parancs visszatérése azt jelzi, hogy a fájlok másolása megtörtént. 

A AzCopy [kiszolgálók](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) közötti [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)-kat használ, így az Adatmásolás közvetlenül a Storage-kiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. A környezeti változó értékének megadásával növelheti a műveletek átviteli sebességét `AZCOPY_CONCURRENCY_VALUE` . További információ: az [átviteli sebesség optimalizálása](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ez a forgatókönyv a jelenlegi kiadásban a következő korlátozásokkal rendelkezik.
>
> - Minden forrás URL-címhez hozzá kell fűzni egy SAS-tokent. Ha Azure Active Directory (AD) hitelesítő adatokat ad meg, kihagyhatja az SAS-tokent csak a cél URL-címről. Győződjön meg arról, hogy beállította a megfelelő szerepköröket a célkiszolgálón. Lásd [az 1. lehetőséget: Azure Active Directory használata](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory).
>-  A prémium szintű blokk blob Storage-fiókok nem támogatják a hozzáférési szinteket. Hagyja ki a blob hozzáférési szintjét a másolási műveletből a (z `s2s-preserve-access-tier` ) értékre való beállításával `false` (például: `--s2s-preserve-access-tier=false` ).

Ez a szakasz a következő példákat tartalmazza:

> [!div class="checklist"]
> * BLOB másolása másik Storage-fiókba
> * Könyvtár másolása másik Storage-fiókba
> * Tároló másolása másik Storage-fiókba
> * Minden tároló, könyvtár és fájl másolása egy másik Storage-fiókba

Ezek a példák olyan fiókokkal is működnek, amelyek hierarchikus névtérrel rendelkeznek. A [több protokollon keresztüli hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé teszi, hogy ugyanazt az URL-szintaxist () használja a `blob.core.windows.net` fiókokon.

> [!TIP]
> A másolási műveletet opcionális jelzők használatával is megadhatja. Íme néhány példa.
>
> |Forgatókönyv|Jelölő|
> |---|---|
> |Blobok másolása blokk, oldal vagy Hozzáfűzés Blobként.|**– blob típusú** = \[ BlockBlob \| PageBlob \| AppendBlob\]|
> |Másolás adott hozzáférési szintre (például az archív szintre).|**--Block-blob-réteg** = \[ Nincs \| gyors elérésű \| \| Archívum\]|
> |Fájlok automatikus kibontása.|**– Kibontás** = \[ gzip- \| kiengedés\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>BLOB másolása másik Storage-fiókba

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Könyvtár másolása másik Storage-fiókba

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Tároló másolása másik Storage-fiókba

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Minden tároló, könyvtár és blob másolása egy másik Storage-fiókba

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

A helyi fájlrendszer tartalmát egy blob-tárolóval szinkronizálhatja. A tárolókat és a virtuális könyvtárakat is szinkronizálhatja egymással. A szinkronizálás egyirányú. Más szóval kiválaszthatja, hogy a két végpont melyik a forrás, és melyik a cél. A szinkronizálás a kiszolgálót kiszolgáló API-kra is használja. Az ebben a szakaszban bemutatott példák a hierarchikus névtérrel rendelkező fiókokkal is működnek. 

> [!NOTE]
> A AzCopy jelenlegi kiadása nem szinkronizál más források és célhelyek között (például: file Storage vagy Amazon Web Services (AWS) S3 gyűjtők).

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és az utolsó módosítás időbélyegét. Állítsa a `--delete-destination` választható jelzőt értékre, `true` vagy `prompt` törölje a fájlokat a célhely könyvtárában, ha ezek a fájlok már nem léteznek a forrás-címtárban.

Ha a `--delete-destination` jelzőt úgy állítja be, hogy a `true` AzCopy törölje a fájlokat, a kérés megadása nélkül. Ha azt szeretné, hogy a AzCopy törlése előtt megjelenjen egy üzenet, állítsa a jelölőt a következőre: `--delete-destination` `prompt` .

> [!NOTE]
> A véletlen törlés megelőzése érdekében ügyeljen arra, hogy a jelző használata előtt engedélyezze a [Soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkciót `--delete-destination=prompt|true` .

> [!TIP]
> A szinkronizálási műveletet opcionális jelzők használatával is megadhatja. Íme néhány példa.
>
> |Forgatókönyv|Jelölő|
> |---|---|
> |Itt adhatja meg, hogy a letöltéskor a szigorúan MD5-kivonatokat kell-e érvényesíteni.|**--ellenőrzési-MD5** = \[ Nincs \| bejelentkezett bejelentkezési \| FailIfDifferent \| FailIfDifferentOrMissing\]|
> |Fájlok kizárása mintázat alapján.|**--kizárás – elérési út**|
> |Adja meg, hogy a szinkronizálással kapcsolatos naplóbejegyzések milyen részletesek legyenek.|**– naplózási szint** = \[ FIGYELMEZTETÉSi \| hiba – \| \| nincs\]|
> 
> A teljes listát itt tekintheti meg: [Beállítások](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Tároló frissítése egy helyi fájlrendszer módosításaival

Ebben az esetben a cél a tároló, a forrás pedig a helyi fájlrendszer. 

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Helyi fájlrendszer frissítése egy tároló módosításaival

Ebben az esetben a helyi fájlrendszer a cél, és a tároló a forrás.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Példa** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Tároló frissítése egy másik tároló módosításaival

A parancsban megjelenő első tároló a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Könyvtár frissítése egy másik fájlmegosztás könyvtárának módosításaival

A parancsban megjelenő első könyvtár a forrás. A második a cél.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Példa** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Következő lépések

További példákat a következő cikkekben talál:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Oktatóanyag: Helyszíni adatok migrálása felhőtárhelybe az AzCopyval](storage-use-azcopy-migrate-on-premises-data.md)

- [Adatok átvitele az AzCopy használatával és fájltárolás](storage-use-azcopy-files.md)

- [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
