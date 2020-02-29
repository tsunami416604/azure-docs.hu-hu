---
title: Ismert problémák az Azure Data Lake Storage Gen2 |} A Microsoft Docs
description: Ismerje meg Azure Data Lake Storage Gen2 korlátozásait és ismert problémáit.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7d637c2fb3f4a4d5f8deac9cd99c0a44af6568e6
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919611"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 ismert problémái

Ez a cikk a Azure Data Lake Storage Gen2 korlátozásait és ismert problémáit ismerteti.

## <a name="supported-blob-storage-features"></a>Támogatott blob Storage-funkciók

A blob Storage-funkciók egyre több funkciója működik olyan fiókokkal, amelyek hierarchikus névtérrel rendelkeznek. A teljes listát lásd: [Blob Storage Azure Data Lake Storage Gen2 elérhető szolgáltatásai](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Támogatott Azure-szolgáltatások integrációi

Data Lake Storage a Gen2 számos olyan Azure-szolgáltatást támogat, amelyet az adatok betöltésére, elemzések elvégzésére és vizuális ábrázolások létrehozására használhat. A támogatott Azure-szolgáltatások listáját a [Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)című témakörben tekintheti meg.

Lásd: [Azure Data Lake Storage Gen2 támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Támogatja a nyílt forráskódú platformok

Számos nyílt forráskódú platformra támogatja a Data Lake Storage Gen2. A teljes listát lásd: a [Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md).

Lásd: [a Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>A BLOB storage API-k

A blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adatközponton működhetnek.

Ez a szakasz a blob API-k és a Data Lake Storage Gen2 API-k használatával kapcsolatos problémákat és korlátozásokat ismerteti ugyanazon az adatközponton.

* A blob API-k és a Data Lake Storage API-k nem használhatók a fájlok ugyanazon példányára való íráshoz. Ha Data Lake Storage Gen2 API-k használatával ír fájlba egy fájlt, a rendszer nem fogja látni a fájl blokkokat a [letiltási lista](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API-jának meghívásához. A fájlokat Data Lake Storage Gen2 API-k vagy blob API-k használatával írhatja felül. Ez nem befolyásolja a fájl tulajdonságait.

* Ha a [lista Blobok](https://docs.microsoft.com/rest/api/storageservices/list-blobs) műveletet határolójel megadása nélkül használja, az eredmények a címtárakat és a blobokat is tartalmazzák. Ha elválasztót választ, csak perjelet (`/`) használjon. Ez az egyetlen támogatott elválasztó karakter.

* Ha a [blob törlése](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API-t használja egy könyvtár törléséhez, akkor a rendszer csak akkor törli a könyvtárat, ha üres. Ez azt jelenti, hogy a blob API rekurzív törlése nem végezhető el.

Ezek a blob REST API-k nem támogatottak:

* [BLOB (oldal) elhelyezése](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Oldal elhelyezése](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Oldalak tartományának beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Növekményes másolási blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Oldal elhelyezése az URL-címről](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [BLOB elhelyezése (Hozzáfűzés)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Blokk hozzáfűzése](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Blokk hozzáfűzése URL-címről](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

A nem felügyelt virtuálisgép-lemezek nem támogatottak olyan fiókoknál, amelyek hierarchikus névtérrel rendelkeznek. Ha egy hierarchikus névteret szeretne engedélyezni egy Storage-fiókban, helyezze a nem felügyelt virtuális gépek lemezeit olyan Storage-fiókba, amelyben nincs engedélyezve a hierarchikus névtér funkció.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Fájlrendszer-támogatás SDK-k esetén

- A [.net](data-lake-storage-directory-file-acl-dotnet.md), a [Java](data-lake-storage-directory-file-acl-java.md) és a [Python](data-lake-storage-directory-file-acl-python.md), valamint a [JavaScript](data-lake-storage-directory-file-acl-javascript.md) és a támogatás nyilvános előzetes verzióban érhető el. Más SDK-k jelenleg nem támogatottak.
- Az ACL-műveletek beolvasása és beállítása jelenleg nem rekurzív.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Fájlrendszer-támogatás a PowerShellben és az Azure CLI-ben

- A [PowerShell](data-lake-storage-directory-file-acl-powershell.md) és az [Azure CLI](data-lake-storage-directory-file-acl-cli.md) támogatása nyilvános előzetes verzióban érhető el.
- Az ACL-műveletek beolvasása és beállítása jelenleg nem rekurzív.

## <a name="lifecycle-management-policies"></a>Életciklus-kezelési szabályzatok

* A blob-Pillanatképek törlése még nem támogatott.  

* Jelenleg vannak olyan hibák, amelyek hatással vannak az életciklus-kezelési házirendekre és az archív hozzáférési szintre. 

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Azure Storage Explorer 1.10. x nem használható a diagnosztikai naplók megtekintéséhez. A naplók megtekintéséhez használja a AzCopy vagy SDK-kat.

## <a name="blobfuse"></a>Blobfuse

A Blobfuse nem támogatott.



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Csak a AzCopy legújabb verzióját használja ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). A AzCopy korábbi verziói (például a AzCopy v 8.1) nem támogatottak.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Csak `1.6.0` vagy újabb verziót használjon. Jelenleg van olyan tárolási hiba, amely hatással van a `1.11.0` verzióra, ami bizonyos helyzetekben hitelesítési hibákhoz vezethet. A tárolási hiba elhárítása folyamatban van, de megkerülő megoldásként javasoljuk, hogy a `1.10.x` verzióját használja, amely [ingyenes letöltésként](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)érhető el. a tárolási hiba nem érinti a `1.10.x` t.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer az Azure Portalon

Az ACL-ek még nem támogatottak.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Harmadik féltől származó alkalmazások

A REST API-kat használó harmadik féltől származó alkalmazások továbbra is működni fognak, ha a blob API-kat hívó Data Lake Storage Gen2 alkalmazásokkal valószínűleg működni fognak.





