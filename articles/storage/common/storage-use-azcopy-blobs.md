---
title: AzCopy v10 használatával vihetők át az adatokat, vagy az Azure Blob storage-ból |} A Microsoft Docs
description: Ez a cikk az AzCopy gyűjteményét tartalmazza például parancsok, amelyek segítségével tárolók létrehozása, fájlok másolása és a helyi fájlrendszerben és a tárolók között mappák szinkronizálásához.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 98e33f838ee9b6f506bf1dc01e1dd61ad587aa05
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299408"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Adatok áthelyezése az AzCopy és a Blob storage

Az AzCopy parancssori segédprogram, amely segítségével történő, a vagy tárfiókok között az adatok másolása. Ez a cikk tartalmazza, amely használható a blobtároló Példaparancsok.

## <a name="get-started"></a>Bevezetés

Tekintse meg a [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md) töltse le az AzCopy és az, hogy megadhat-e az engedélyezési hitelesítő adatok a storage szolgáltatásra módjaival kapcsolatos cikket.

> [!NOTE]
> Ebben a cikkben szereplő példák feltételezik, hogy Ön már hitelesítve az identitás használatával a `AzCopy login` parancsot. Az AzCopy ezután a Blob Storage-adatokhoz való hozzáférés engedélyezéséhez használja az Azure AD-fiókot.
>
> Ha szeretné inkább használni egy SAS-token blobadatokhoz való hozzáférés engedélyezése, majd fűzze hozzá a ezt a jogkivonatot az erőforrás URL-címet minden egyes AzCopy-parancs.
>
> Például: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`.

## <a name="create-a-container"></a>Tároló létrehozása

Az azcopyval `make` paranccsal hozzon létre egy tárolót. Ebben a szakaszban szereplő példák, hozzon létre egy tárolót `mycontainer`.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **Példa** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **Példa** (hierarchikus névtér) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>Fájlok feltöltése

Az azcopyval `copy` paranccsal töltse fel a fájlokat és mappákat a helyi számítógépről.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl feltöltése
> * Mappa feltöltése
> * Fájlok feltöltése a helyettesítő karakterek használatával

> [!NOTE]
> Az AzCopy nem automatikusan kiszámítsa és tárolja a fájl md5 kivonatoló kódot. Ha azt szeretné, hogy ehhez az AzCopy, majd fűzze hozzá a `--put-md5` jelző minden egyes Másolás parancsra. Ezzel a módszerrel, amikor a blobot, az AzCopy kiszámítja egy MD5-kivonat letöltött adatok, és ellenőrzi, hogy az MD5-kivonat tárolása a blob `Content-md5` tulajdonsága egyezést mutat a kiszámított kivonatát.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Példa** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> Alapértelmezés szerint az AzCopy adatait feltölti az a blokkblobok használatát támogatják. A jelző használható hozzáfűző Blobok és Lapblobok fájlok feltöltéséhez `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-folder"></a>Mappa feltöltése

Ebben a példában a blob-tárolóba másolja át egy mappát (és a mappában lévő fájlok). Ez egy mappát a tárolóban, ugyanazzal a névvel.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Másolása egy mappába a tárolóban, csak a parancs karakterláncot adja meg, hogy a mappa nevét.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myFolder" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder" --recursive` |

Ha megad egy mappát, amely nem szerepel a tároló nevére, az AzCopy ilyen nevű új mappát hoz létre.

### <a name="upload-the-contents-of-a-folder"></a>A mappa tartalmának feltöltése

Maga a tartalmazó mappa másolása a (*) helyettesítő karakter és szimbólum nélkül feltöltheti egy mappa tartalmát.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-folder-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>` |
| **Példa** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder"` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder"` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelző összes almappákban lévő fájlok feltöltéséhez.

## <a name="download-files"></a>Fájlok letöltése

Az azcopyval `copy` paranccsal letöltheti a tárolók, blobok és mappákat a helyi számítógépen.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl letöltése
> * Töltse le a mappa
> * Fájlok letöltése a helyettesítő karakterek használatával

> [!NOTE]
> Ha a `Content-md5` tulajdonságának értéke egy blobot tartalmaz egy kivonatot, az AzCopy számítja ki a letöltött adatok MD5-kivonatot, és ellenőrzi, hogy az MD5-kivonat tárolása a blob `Content-md5` tulajdonsága egyezést mutat a kiszámított kivonatát. Ezek az értékek nem egyeznek meg, ha a letöltés meghiúsul, ha felülbírálja ezt a viselkedést úgy, `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a Másolás parancsra.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Példa** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |
| **Példa** (hierarchikus névtér) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>Töltse le a mappa

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<folder-path>" "<local-folder-path>" --recursive` |
| **Példa** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobFolder "C:\myFolder"  --recursive` |

Ebben a példában lévő szkriptfájlokat eredmények `C:\myFolder\myBlobFolder` , amely tartalmazza az összes letöltött fájl.

### <a name="download-the-contents-of-a-folder"></a>A mappa tartalmának letöltése

Egy mappa tartalmát anélkül, hogy maga a tartalmazó mappa másolása (*) helyettesítő karakter és szimbólum használatával töltheti le.

> [!NOTE]
> Ebben a forgatókönyvben jelenleg csak egy hierarchikus névtér nem rendelkező fiókok esetében támogatott.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-folder-path>/"` |
| **Példa** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobFolder/*" "C:\myFolder"` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelző összes almappákban lévő fájlok letöltéséhez.

## <a name="copy-blobs-between-storage-accounts"></a>Másolás BLOB storage-fiókok között

Az AzCopy segítségével más tárfiókok blobok másolása. A másolási művelet szinkron, így ha a parancs, amely jelzi, hogy minden fájl erőforrástármegosztásba megtörtént-e.

> [!NOTE]
> Ebben a forgatókönyvben jelenleg csak egy hierarchikus névtér nem rendelkező fiókok esetében támogatott.

Az AzCopy használja a [URL blokk Put](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API-t, így közvetlenül a tároló kiszolgálók közötti adatokat másolja. Ezek a másolási műveletek ne használja a hálózati sávszélesség számítógép.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Blob másolása egy másik tárfiókba
> * Másolja a mappát egy másik tárfiókba
> * A tárolók másolja egy másik tárfiókba
> * Az összes tárolók, mappák és fájlok másolása egy másik tárfiókba

### <a name="copy-a-blob-to-another-storage-account"></a>Blob másolása egy másik tárfiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Példa** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-folder-to-another-storage-account"></a>Másolja a mappát egy másik tárfiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<folder-path>" --recursive` |
| **Példa** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobFolder" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobFolder" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>A tárolók másolja egy másik tárfiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-folders-and-files-to-another-storage-account"></a>Az összes tárolók, mappák és fájlok másolása egy másik tárfiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Példa** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

Szinkronizálhatja a tartalmát a helyi fájlrendszerben; egy blob-tárolóba. Egy blob-tárolóba a helyi fájlrendszerbe is szinkronizálhatja a számítógépen. Szinkronizálás még csak egyirányú. Más szóval válassza ki ezen két végpontot, azaz a forrás, és melyik az a hely.

> [!NOTE]
> A jelenlegi kiadásban az AzCopy nem szinkronizálja más források és célok között (például: A File storage vagy az Amazon Web Services (AWS) S3 gyűjtő).

A `sync` parancs összehasonlítja fájlneveket, valamint utolsó módosítás az időbélyegek. Állítsa be a `--delete-destination` értékét kötelező jelző `true` vagy `prompt` törli a fájlokat a célmappában, ha ezeket a fájlokat már nem létezik a forrásmappában található.

Ha a `--delete-destination` jelzőt `true` AzCopy kérdés megadása nélkül törli a fájlokat. Ha azt szeretné, hogy egy kérés AzCopy törlése előtt meg egy fájlt, állítsa be a `--delete-destination` jelzőt `prompt`.

> [!NOTE]
> Véletlen törlések megakadályozása érdekében ügyeljen arra, hogy engedélyezze a [helyreállítható törlési](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkció használata előtt a `--delete-destination=prompt|true` jelzőt.

### <a name="synchronize-a-container-to-a-local-file-system"></a>Egy tárolót a helyi fájlrendszerben szinkronizálása

Ebben az esetben a helyi fájlrendszerben lesz a forrás és a tároló a célhelyen.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy sync "C:\myFolder" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>Szinkronizálja a helyi fájlrendszer egy tárolóba

Ebben az esetben a tároló válik a forrás és a helyi fájlrendszer az a cél.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myFolder" --recursive` |
| **Példa** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myFolder" --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myFolder" --recursive` |

## <a name="next-steps"></a>További lépések

További példa található a következő cikkeket:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Oktatóanyag: Felhőtárhelybe az Azcopyval a helyszíni adatok migrálása](storage-use-azcopy-migrate-on-premises-data.md)

- [Adatok áthelyezése az AzCopy és a file storage](storage-use-azcopy-files.md)

- [Adatok áthelyezése az AzCopy és az Amazon S3 gyűjtő](storage-use-azcopy-s3.md)

- [Konfigurálja, optimalizálhat és AzCopy hibaelhárítása](storage-use-azcopy-configure.md)