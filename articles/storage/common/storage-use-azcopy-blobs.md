---
title: Adatok átvitele az Azure Blob Storage-ba vagy onnan az AzCopy v10 használatával | Microsoft Docs
description: Ez a cikk AzCopy-példákat tartalmaz, amelyek segítenek a tárolók létrehozásában, a fájlok másolásában és a könyvtárak szinkronizálásában a helyi fájlrendszerek és tárolók között.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: fbdb447905ae43fe92693dfe45c1add710f76355
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933582"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Adatok átvitele a AzCopy és a blob Storage szolgáltatással

A AzCopy egy parancssori segédprogram, amellyel az adatok átmásolhatók a, a-ból vagy a Storage-fiókok között. Ez a cikk a blob Storage-hoz használható példaként szolgáló parancsokat tartalmaz.

## <a name="get-started"></a>Bevezetés

Tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és a tárolási szolgáltatás engedélyezési hitelesítő adatainak megadásához szükséges módszerekről.

> [!NOTE]
> A cikkben szereplő példák azt feltételezik, hogy az `AzCopy login` parancs használatával hitelesítette az identitását. A AzCopy ezután az Azure AD-fiók használatával engedélyezi a blob Storage-beli adathozzáférését.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban.
>
> Például: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Tároló létrehozása

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

Tároló létrehozásához használhatja a [azcopy make](storage-ref-azcopy-make.md) parancsot. Az ebben a szakaszban szereplő példák egy `mycontainer`nevű tárolót hoznak létre.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Példa** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Példa** (hierarchikus névtér) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

A részletes dokumentációt a következő témakörben tekintheti meg: [azcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Fájlok feltöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Könyvtár tartalmának feltöltése 
> * Adott fájlok feltöltése

A részletes dokumentációt lásd: [azcopy másolás](storage-ref-azcopy-copy.md).

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

A fájl elérési útja vagy fájlneve tetszőleges helyettesítő karakter (*) használatával is feltölthet egy fájlt. Például: `'C:\myDirectory\*.txt'`vagy `C:\my*\*.txt`.

> [!NOTE]
> A AzCopy alapértelmezés szerint a blokk blobként tölti fel az adatokkal. A fájlok hozzáfűzési Blobként vagy Blobként való feltöltéséhez használja a jelzőt `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.
> A AzCopy alapértelmezés szerint feltölti az adatait, hogy örökölje a fiók hozzáférési szintjét. Ha fájlokat szeretne feltölteni egy adott [hozzáférési szintjére](../blobs/storage-blob-storage-tiers.md), használja a jelzőt `--block-blob-tier=[Hot|Cool|Archive]`.

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ez a példa egy könyvtárat (és az abban a könyvtárban található összes fájlt) egy blob-tárolóba másol. Ennek eredményeképpen a tároló egyik könyvtára azonos néven szerepel.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
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
| **Szintaxis** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Példa** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Az összes alkönyvtárban lévő fájlok feltöltéséhez fűzze hozzá a `--recursive` jelzőt.

### <a name="upload-specific-files"></a>Adott fájlok feltöltése

Megadhatja a teljes fájlnevet, vagy használhat részleges neveket helyettesítő karakterekkel (*).

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Pontosvesszővel (`;`) válassza el az egyes fájlneveket.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Ebben a példában a AzCopy átviszi a `C:\myDirectory\photos` könyvtárat és a `C:\myDirectory\documents\myFile.txt` fájlt. A `C:\myDirectory\photos` könyvtárban lévő összes fájl átviteléhez meg kell adnia a `--recursive` lehetőséget.

A fájlokat a `--exclude-path` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket semicolin (`;`) használva válassza el. 

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a `--exclude-pattern` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és `--exclude-pattern` beállítások csak a fájlnevekre érvényesek, az elérési útra nem.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` kapcsolót a teljes könyvtár faszerkezetének beolvasásához, majd használja a `–include-pattern`, és adja meg `*.txt` az összes szövegfájl beolvasásához.

## <a name="download-files"></a>Fájlok letöltése

A [azcopy másolás](storage-ref-azcopy-copy.md) parancs használatával blobokat, címtárakat és tárolókat tölthet le a helyi számítógépre.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl letöltése
> * Könyvtár letöltése
> * Könyvtár tartalmának letöltése
> * Adott fájlok letöltése

> [!NOTE]
> Ha egy blob `Content-md5` tulajdonságának értéke kivonatot tartalmaz, a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a blob `Content-md5` tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés meghiúsul, hacsak nem bírálja felül ezt a viselkedést `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a másolási parancshoz való hozzáfűzésével.

A részletes dokumentációt lásd: [azcopy másolás](storage-ref-azcopy-copy.md).

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

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

Ez a példa egy `C:\myDirectory\myBlobDirectory` nevű könyvtárat eredményez, amely az összes letöltött fájlt tartalmazza.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

A könyvtár tartalmát letöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Az összes alkönyvtárban lévő fájlok letöltéséhez fűzze hozzá a `--recursive` jelzőt.

### <a name="download-specific-files"></a>Adott fájlok letöltése

Megadhatja a teljes fájlnevet, vagy használhat részleges neveket helyettesítő karakterekkel (*).

#### <a name="specify-multiple-complete-file-names"></a>Több teljes fájlnevet kell megadni

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-path` kapcsolóval. Különálló fájlnevek elkülönítése semicolin (`;`) használatával.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Ebben a példában a AzCopy átviszi a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` könyvtárat és a `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` fájlt. A `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` könyvtárban lévő összes fájl átviteléhez meg kell adnia a `--recursive` lehetőséget.

A fájlokat a `--exclude-path` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

#### <a name="use-wildcard-characters"></a>Helyettesítő karakterek használata

Használja az [azcopy Copy](storage-ref-azcopy-copy.md) parancsot a `--include-pattern` kapcsolóval. Adjon meg olyan részleges neveket, amelyek tartalmazzák a helyettesítő karaktereket. A neveket semicolin (`;`) használva válassza el.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Példa** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

A fájlokat a `--exclude-pattern` lehetőség használatával is kizárhatja. További információért lásd: [azcopy másolási](storage-ref-azcopy-copy.md) dokumentáció.

A `--include-pattern` és `--exclude-pattern` beállítások csak a fájlnevekre érvényesek, az elérési útra nem.  Ha egy címtár fájában található összes szövegfájlt szeretné átmásolni, használja a `–recursive` kapcsolót a teljes könyvtár faszerkezetének beolvasásához, majd használja a `–include-pattern`, és adja meg `*.txt` az összes szövegfájl beolvasásához.

## <a name="copy-blobs-between-storage-accounts"></a>Blobok másolása a Storage-fiókok között

A AzCopy a Blobok más Storage-fiókba való másolására is használható. A másolási művelet szinkronban van, így amikor a parancs visszatér, ez azt jelzi, hogy az összes fájl másolása megtörtént. 

A AzCopy [kiszolgálók](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) közötti [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)-kat használ, így az Adatmásolás közvetlenül a Storage-kiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. Az `AZCOPY_CONCURRENCY_VALUE` környezeti változó értékének megadásával növelheti a műveletek átviteli sebességét. További információ: az [átviteli sebesség optimalizálása](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ez a forgatókönyv a jelenlegi kiadásban a következő korlátozásokkal rendelkezik.
>
> - Minden forrás URL-címhez hozzá kell fűzni egy SAS-tokent. Ha Azure Active Directory (AD) hitelesítő adatokat ad meg, kihagyhatja az SAS-tokent csak a cél URL-címről.
>-  A prémium szintű blokk blob Storage-fiókok nem támogatják a hozzáférési szinteket. A másolási műveletből kihagyhatja a blob hozzáférési szintjét úgy, hogy a `s2s-preserve-access-tier` `false`re állítja (például: `--s2s-preserve-access-tier=false`).

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * BLOB másolása másik Storage-fiókba
> * Könyvtár másolása másik Storage-fiókba
> * Tároló másolása másik Storage-fiókba
> * Minden tároló, könyvtár és fájl másolása egy másik Storage-fiókba

A részletes dokumentációt lásd: [azcopy másolás](storage-ref-azcopy-copy.md).

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

 Ezek a példák olyan fiókokkal is működnek, amelyek hierarchikus névtérrel rendelkeznek. A [többprotokollos hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé teszi, hogy ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja a fiókokon. 

### <a name="copy-a-blob-to-another-storage-account"></a>BLOB másolása másik Storage-fiókba

Használja ugyanazt az URL-szintaxist (`blob.core.windows.net`) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Könyvtár másolása másik Storage-fiókba

Használja ugyanazt az URL-szintaxist (`blob.core.windows.net`) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Tároló másolása másik Storage-fiókba

Használja ugyanazt az URL-szintaxist (`blob.core.windows.net`) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Minden tároló, könyvtár és blob másolása egy másik Storage-fiókba

Használja ugyanazt az URL-szintaxist (`blob.core.windows.net`) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Példa** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

A helyi fájlrendszer tartalmát egy blob-tárolóval szinkronizálhatja. A tárolókat és a virtuális könyvtárakat is szinkronizálhatja egymással. A szinkronizálás egyirányú. Más szóval kiválaszthatja, hogy a két végpont melyik a forrás, és melyik a cél. A szinkronizálás a kiszolgálót kiszolgáló API-kra is használja. Az ebben a szakaszban bemutatott példák a hierarchikus névtérrel rendelkező fiókokkal is működnek. 

> [!NOTE]
> A AzCopy jelenlegi kiadása nem szinkronizál más források és célhelyek között (például: file Storage vagy Amazon Web Services (AWS) S3 gyűjtők).

A [szinkronizálási](storage-ref-azcopy-sync.md) parancs összehasonlítja a fájlneveket és az utolsó módosítás időbélyegét. Adja meg a `--delete-destination` opcionális jelzőt `true` vagy `prompt` értékre, hogy törölje a fájlokat a célhely könyvtárában, ha ezek a fájlok már nem léteznek a forrás-címtárban.

Ha a `--delete-destination` jelzőt úgy állítja be, hogy `true` AzCopy, a fájlok megadása nélkül törölje a fájlokat. Ha azt szeretné, hogy a AzCopy törlése előtt megjelenjen egy üzenet, állítsa a `--delete-destination` jelzőt `prompt`re.

> [!NOTE]
> A véletlen törlés megelőzése érdekében ügyeljen arra, hogy a `--delete-destination=prompt|true` jelző használata előtt engedélyezze a [Soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkciót.

A részletes dokumentációs dokumentáció: [azcopy Sync](storage-ref-azcopy-sync.md).

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. A Windows parancs-rendszerhéj (Cmd. exe) kivételével használjon szimpla idézőjeleket az összes parancs-rendszerhéjban. Ha Windows parancs-rendszerhéjt (Cmd. exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé kell foglalni az elérésiút-argumentumokat.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Tároló frissítése egy helyi fájlrendszer módosításaival

Ebben az esetben a tároló a cél, a helyi fájlrendszer pedig a forrás. 

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Példa** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Helyi fájlrendszer frissítése egy tároló módosításaival

Ebben az esetben a helyi fájlrendszer a cél, és a tároló a forrás.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Példa** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Tároló frissítése egy másik tároló módosításaival

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

További példákat a következő cikkekben talál:

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)

- [Oktatóanyag: helyszíni adatáttelepítés a Felhőbeli tárhelyre a AzCopy használatával](storage-use-azcopy-migrate-on-premises-data.md)

- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)

- [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
