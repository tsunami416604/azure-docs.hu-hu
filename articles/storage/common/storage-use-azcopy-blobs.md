---
title: AzCopy v10 használatával vihetők át az adatokat, vagy az Azure Blob storage-ból |} A Microsoft Docs
description: Ez a cikk az AzCopy gyűjteményét tartalmazza például parancsok, amelyek segítségével tárolók létrehozása, fájlok másolása és szinkronizálhatja a címtárakat, a helyi fájlrendszerben és a tárolók között.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: fea9e79986e45127ad4918ed62bd8bf8dc782133
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67125804"
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

Az azcopyval `copy` paranccsal töltse fel a fájlokat és könyvtárakat a helyi számítógépről.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl feltöltése
> * Egy könyvtárat feltöltése
> * Fájlok feltöltése a helyettesítő karakterek használatával

> [!NOTE]
> Az AzCopy nem automatikusan kiszámítsa és tárolja a fájl md5 kivonatoló kódot. Ha azt szeretné, hogy ehhez az AzCopy, majd fűzze hozzá a `--put-md5` jelző minden egyes Másolás parancsra. Ezzel a módszerrel, amikor a blobot, az AzCopy kiszámítja egy MD5-kivonat letöltött adatok, és ellenőrzi, hogy az MD5-kivonat tárolása a blob `Content-md5` tulajdonsága egyezést mutat a kiszámított kivonatát.

### <a name="upload-a-file"></a>Fájl feltöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **Példa** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> Alapértelmezés szerint az AzCopy adatait feltölti az a blokkblobok használatát támogatják. A jelző használható hozzáfűző Blobok és Lapblobok fájlok feltöltéséhez `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

### <a name="upload-a-directory"></a>Egy könyvtárat feltöltése

Ebben a példában a blob-tárolóba másolja át egy könyvtárat (és az összes fájl ebben a könyvtárban). Ez a tárolóban, azonos nevű könyvtár.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

Másolja a tároló a címtárhoz, csak adja meg annak a könyvtárnak a nevét a parancs karakterláncban.

|    |     |
|--------|-----------|
| **Példa** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

Ha megad egy címtár, amely nem szerepel a tároló nevére, az AzCopy ilyen nevű hoz létre egy új könyvtárat.

### <a name="upload-the-contents-of-a-directory"></a>Töltse fel a tartalmát a címtár

Feltölthet egy könyvtár tartalmának másolása a tartalmazó könyvtár magát a (*) helyettesítő karakter és szimbólum nélkül.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **Példa** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **Példa** (hierarchikus névtér) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelző összes alárendelt könyvtárakban található fájlok feltöltéséhez.

## <a name="download-files"></a>Fájlok letöltése

Az azcopyval `copy` paranccsal letöltheti a blobok, könyvtárak és tárolók a helyi számítógépen.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Fájl letöltése
> * Letöltési könyvtár
> * Fájlok letöltése a helyettesítő karakterek használatával

> [!NOTE]
> Ha a `Content-md5` tulajdonságának értéke egy blobot tartalmaz egy kivonatot, az AzCopy számítja ki a letöltött adatok MD5-kivonatot, és ellenőrzi, hogy az MD5-kivonat tárolása a blob `Content-md5` tulajdonsága egyezést mutat a kiszámított kivonatát. Ezek az értékek nem egyeznek meg, ha a letöltés meghiúsul, ha felülbírálja ezt a viselkedést úgy, `--check-md5=NoCheck` vagy `--check-md5=LogOnly` a Másolás parancsra.

### <a name="download-a-file"></a>Fájl letöltése

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **Példa** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **Példa** (hierarchikus névtér) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>Letöltési könyvtár

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **Példa** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **Példa** (hierarchikus névtér) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

Ebben a példában eredményez nevű könyvtárat `C:\myDirectory\myBlobDirectory` , amely tartalmazza az összes letöltött fájl.

### <a name="download-the-contents-of-a-directory"></a>Egy könyvtár tartalmának letöltése

Egy könyvtár tartalmának másolása a tartalmazó könyvtár magát a (*) helyettesítő karakter és szimbólum nélkül töltheti le.

> [!NOTE]
> Ebben a forgatókönyvben jelenleg csak egy hierarchikus névtér nem rendelkező fiókok esetében támogatott.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **Példa** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> Fűzze hozzá a `--recursive` jelző összes alárendelt könyvtárakban található fájlok letöltéséhez.

## <a name="copy-blobs-between-storage-accounts"></a>Másolás BLOB storage-fiókok között

Az AzCopy segítségével más tárfiókok blobok másolása. A másolási művelet szinkron, így ha a parancs, amely jelzi, hogy minden fájl erőforrástármegosztásba megtörtént-e.

> [!NOTE]
> Ebben a forgatókönyvben jelenleg csak egy hierarchikus névtér nem rendelkező fiókok esetében támogatott.

Az AzCopy használja a [URL blokk Put](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API-t, így közvetlenül a tároló kiszolgálók közötti adatokat másolja. Ezek a másolási műveletek ne használja a hálózati sávszélesség számítógép.

Ez a szakasz tartalmazza az alábbi példák:

> [!div class="checklist"]
> * Blob másolása egy másik tárfiókba
> * Másolja egy könyvtár egy másik tárfiókba
> * A tárolók másolja egy másik tárfiókba
> * Az összes olyan tárolók, könyvtárak és fájlok másolása egy másik tárfiókba

### <a name="copy-a-blob-to-another-storage-account"></a>Blob másolása egy másik tárfiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **Példa** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>Másolja egy könyvtár egy másik tárfiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **Példa** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>A tárolók másolja egy másik tárfiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>Az összes olyan tárolók, könyvtárak és fájlok másolása egy másik tárfiókba

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **Példa** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>Fájlok szinkronizálása

Szinkronizálhatja a tartalmát egy helyi fájlrendszer és egy blobtárolót. Szinkronizálás még csak egyirányú. Más szóval válassza ki ezen két végpontot, azaz a forrás, és melyik az a hely.

> [!NOTE]
> Ebben a forgatókönyvben jelenleg csak egy hierarchikus névtér nem rendelkező fiókok esetében támogatott. A jelenlegi kiadásban az AzCopy nem szinkronizálja más források és célok között (például: A File storage vagy az Amazon Web Services (AWS) S3 gyűjtő).

A `sync` parancs összehasonlítja fájlneveket, valamint utolsó módosítás az időbélyegek. Állítsa be a `--delete-destination` értékét kötelező jelző `true` vagy `prompt` a célkönyvtárban duplikátum található fájlok törléséhez, ha ezeket a fájlokat a forráskönyvtárban nem létezik.

Ha a `--delete-destination` jelzőt `true` AzCopy kérdés megadása nélkül törli a fájlokat. Ha azt szeretné, hogy egy kérés AzCopy törlése előtt meg egy fájlt, állítsa be a `--delete-destination` jelzőt `prompt`.

> [!NOTE]
> Véletlen törlések megakadályozása érdekében ügyeljen arra, hogy engedélyezze a [helyreállítható törlési](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) funkció használata előtt a `--delete-destination=prompt|true` jelzőt.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Egy tároló frissítése a változások a helyi fájlrendszerbe

Ebben az esetben a tároló-e a cél, és a helyi fájlrendszer az a forrás.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **Példa** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Frissítse a helyi fájlrendszer egy tároló módosítása

Ebben az esetben a helyi fájlrendszer az a cél, és a tároló a forrás.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **Példa** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>További lépések

További példa található a következő cikkeket:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Oktatóanyag: Felhőtárhelybe az Azcopyval a helyszíni adatok migrálása](storage-use-azcopy-migrate-on-premises-data.md)

- [Adatok áthelyezése az AzCopy és a file storage](storage-use-azcopy-files.md)

- [Adatok áthelyezése az AzCopy és az Amazon S3 gyűjtő](storage-use-azcopy-s3.md)

- [Konfigurálja, optimalizálhat és AzCopy hibaelhárítása](storage-use-azcopy-configure.md)