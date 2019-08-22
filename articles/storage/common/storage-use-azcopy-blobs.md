---
title: Adatok átvitele az Azure Blob Storage-ba vagy onnan az AzCopy v10 használatával | Microsoft Docs
description: Ez a cikk AzCopy-példákat tartalmaz, amelyek segítenek a tárolók létrehozásában, a fájlok másolásában és a könyvtárak szinkronizálásában a helyi fájlrendszerek és tárolók között.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: eead4436a7135f6f74528cde52883ea247360fc6
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648776"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Adatok átvitele a AzCopy és a blob Storage szolgáltatással

A AzCopy egy parancssori segédprogram, amellyel az adatok átmásolhatók a, a-ból vagy a Storage-fiókok között. Ez a cikk a blob Storage-hoz használható példaként szolgáló parancsokat tartalmaz.

## <a name="get-started"></a>Bevezetés

Tekintse meg az első [lépések a AzCopy](storage-use-azcopy-v10.md) című cikket a AzCopy letöltéséhez és a tárolási szolgáltatás engedélyezési hitelesítő adatainak megadásához szükséges módszerekről.

> [!NOTE]
> A cikkben szereplő példák azt feltételezik, hogy a `AzCopy login` parancs használatával hitelesítette a személyazonosságát. A AzCopy ezután az Azure AD-fiók használatával engedélyezi a blob Storage-beli adathozzáférését.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban.
>
> Például: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Tároló létrehozása

A AzCopy `make` parancs használatával létrehozhat egy tárolót. Az ebben a szakaszban szereplő példák egy nevű `mycontainer`tárolót hoznak létre.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Példa** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Példa** (hierarchikus névtér) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Fájlok feltöltése

A AzCopy `copy` parancs használatával fájlokat és könyvtárakat tölthet fel a helyi számítógépről.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl feltöltése
> * Könyvtár feltöltése
> * Fájlok feltöltése helyettesítő karakterek használatával

> [!NOTE]
> A AzCopy nem számítja ki automatikusan a fájl MD5 kivonatoló kódját. Ha azt szeretné, hogy a AzCopy ezt a lehetőséget, `--put-md5` fűzze hozzá a jelölőt az egyes másolási parancsokhoz. Így a blob letöltése után a AzCopy kiszámítja a letöltött adatokat tartalmazó MD5-kivonatot, és ellenőrzi, hogy a blob `Content-md5` tulajdonságában tárolt MD5-kivonat megegyezik-e a számított kivonattal.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Példa** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> A AzCopy alapértelmezés szerint feltölti az adatblokk-blobokat. A fájlok hozzáfűzési Blobként való feltöltéséhez vagy az oldal Blobok a jelzőt `--blob-type=[BlockBlob|PageBlob|AppendBlob]`használják.

### <a name="upload-a-directory"></a>Könyvtár feltöltése

Ez a példa egy könyvtárat (és az abban a könyvtárban található összes fájlt) egy blob-tárolóba másol. Ennek eredményeképpen a tároló egyik könyvtára azonos néven szerepel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

A tárolóban lévő könyvtárba való másoláshoz csak adja meg a könyvtár nevét a parancs karakterláncában.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Ha egy olyan könyvtár nevét adja meg, amely nem szerepel a tárolóban, a AzCopy létrehoz egy új könyvtárat a név alapján.

### <a name="upload-the-contents-of-a-directory"></a>Könyvtár tartalmának feltöltése

A könyvtár tartalmát feltöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Példa** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Fűzze hozzá `--recursive` a jelölőt az összes alkönyvtárban található fájlok feltöltéséhez.

## <a name="download-files"></a>Fájlok letöltése

A AzCopy `copy` parancs használatával blobokat, címtárakat és tárolókat tölthet le a helyi számítógépre.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl letöltése
> * Könyvtár letöltése
> * Fájlok letöltése helyettesítő karakterek használatával

> [!NOTE]
> Ha egy blob `Content-md5` tulajdonságértékekivonatottartalmaz,aAzCopykiszámítjaaletöltöttadatokattartalmazóMD5-kivonatot,ésellenőrzi,hogyablobtulajdonságábantároltMD5-kivonatmegegyezik-ea`Content-md5` számított kivonattal. Ha ezek az értékek nem egyeznek, a letöltés meghiúsul, ha a hozzáfűzéssel `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a másolási paranccsal felülbírálja ezt a viselkedést.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Példa** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Példa** (hierarchikus névtér) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Könyvtár letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Példa** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Ez a példa egy nevű `C:\myDirectory\myBlobDirectory` könyvtárat eredményez, amely az összes letöltött fájlt tartalmazza.

### <a name="download-the-contents-of-a-directory"></a>Könyvtár tartalmának letöltése

A könyvtár tartalmát letöltheti anélkül, hogy a benne foglalt könyvtárat a helyettesítő karakter (*) használatával másolja.

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Példa** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Az összes `--recursive` alkönyvtárban lévő fájlok letöltéséhez fűzze hozzá a jelölőt.

## <a name="copy-blobs-between-storage-accounts"></a>Blobok másolása a Storage-fiókok között

A AzCopy a Blobok más Storage-fiókba való másolására is használható. A másolási művelet szinkronban van, így amikor a parancs visszatér, ez azt jelzi, hogy az összes fájl másolása megtörtént.

A AzCopy [kiszolgálók](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) közötti [API](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)-kat használ, így az Adatmásolás közvetlenül a Storage-kiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét. A `AZCOPY_CONCURRENCY_VALUE` környezeti változó értékének megadásával növelheti a műveletek átviteli sebességét. További információ: az [átviteli sebesség optimalizálása](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Ez a forgatókönyv a jelenlegi kiadásban a következő korlátozásokkal rendelkezik.
>
> - Csak azok a fiókok támogatottak, amelyek nem rendelkeznek hierarchikus névtérrel.
> - Minden forrás URL-címhez hozzá kell fűzni egy SAS-tokent. Ha Azure Active Directory (AD) hitelesítő adatokat ad meg, kihagyhatja az SAS-tokent csak a cél URL-címről.
>-  A prémium szintű blokk blob Storage-fiókok nem támogatják a hozzáférési szinteket. Hagyja ki a blob hozzáférési szintjét a másolási műveletből a (z `s2s-preserve-access-tier` ) `false` értékre való beállításával (például: `--s2s-preserve-access-tier=false`).

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * BLOB másolása másik Storage-fiókba
> * Könyvtár másolása másik Storage-fiókba
> * Tárolók másolása másik Storage-fiókba
> * Minden tároló, könyvtár és fájl másolása egy másik Storage-fiókba

### <a name="copy-a-blob-to-another-storage-account"></a>BLOB másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Példa** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Könyvtár másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>Tárolók másolása másik Storage-fiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy copy "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Minden tároló, könyvtár és blob másolása egy másik Storage-fiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Példa** | `azcopy copy "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

A helyi fájlrendszer tartalmát egy blob-tárolóval szinkronizálhatja. A szinkronizálás egyirányú. Más szóval kiválaszthatja, hogy a két végpont melyik a forrás, és melyik a cél.

> [!NOTE]
> Ez a forgatókönyv jelenleg csak olyan fiókok esetében támogatott, amelyek nem rendelkeznek hierarchikus névtérrel. A AzCopy jelenlegi kiadása nem szinkronizál más források és célhelyek között (például: File Storage vagy Amazon Web Services (AWS) S3 gyűjtők).

A `sync` parancs összehasonlítja a fájlneveket és az utolsó módosítás időbélyegét. Állítsa a `--delete-destination` választható jelzőt értékre, `true` vagy `prompt` törölje a fájlokat a célhely könyvtárában, ha ezek a fájlok már nem léteznek a forrás-címtárban.

Ha a jelzőt `--delete-destination` úgy állítja `true` be, hogy a AzCopy törölje a fájlokat, a kérés megadása nélkül. Ha azt szeretné, hogy a AzCopy törlése előtt megjelenjen egy üzenet, állítsa a `--delete-destination` `prompt`jelölőt a következőre:.

> [!NOTE]
> A véletlen törlés megelőzése érdekében ügyeljen arra, hogy a `--delete-destination=prompt|true` jelző használata előtt engedélyezze a [Soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkciót.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Tároló frissítése egy helyi fájlrendszer módosításaival

Ebben az esetben a tároló a cél, a helyi fájlrendszer pedig a forrás.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Helyi fájlrendszer frissítése egy tároló módosításaival

Ebben az esetben a helyi fájlrendszer a cél, és a tároló a forrás.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Példa** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>További lépések

További példákat a következő cikkekben talál:

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)

- [Oktatóanyag: Helyszíni adatai áttelepíthetők a felhőalapú tárolóba a AzCopy használatával](storage-use-azcopy-migrate-on-premises-data.md)

- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)

- [Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel](storage-use-azcopy-s3.md)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
