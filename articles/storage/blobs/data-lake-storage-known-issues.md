---
title: Ismert problémák az Azure Data Lake Storage Gen2 szolgáltatással | Microsoft dokumentumok
description: Ismerje meg az Azure Data Lake Storage Gen2 korlátait és ismert problémáit.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 204b5dd4661b34aae8b76d65505a65e20f293f0f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115331"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Ismert problémák az Azure Data Lake Storage Gen2 szolgáltatással kapcsolatban

Ez a cikk az Azure Data Lake Storage Gen2 korlátait és ismert problémáit ismerteti.

## <a name="supported-blob-storage-features"></a>Támogatott Blob Storage-funkciók

A Blob storage-szolgáltatások egyre növekvő száma működik hierarchikus névtérrel rendelkező fiókokkal. A teljes listát az [Azure Data Lake Storage Gen2 szolgáltatásában elérhető Blob Storage-funkciók című témakörben tartalmazza.](data-lake-storage-supported-blob-storage-features.md)

## <a name="supported-azure-service-integrations"></a>Támogatott Azure-szolgáltatásintegrációk

Az Azure Data Lake Storage Gen2 számos Azure-szolgáltatást támogat, amelyek segítségével adatokat lehet beadni, elemzéseket végezhet, és vizuális ábrázolásokat hozhat létre. A támogatott Azure-szolgáltatások listáját az [Azure Data Lake Storage Gen2 szolgáltatást támogató Azure-szolgáltatások című](data-lake-storage-supported-azure-services.md)témakörben található.

Tekintse meg [az Azure Data Lake Storage Gen2 szolgáltatást támogató Azure-szolgáltatásokat.](data-lake-storage-supported-azure-services.md)

## <a name="supported-open-source-platforms"></a>Támogatott nyílt forráskódú platformok

Számos nyílt forráskódú platform támogatja a Data Lake Storage Gen2-t. A teljes listát az [Azure Data Lake Storage Gen2-t támogató nyílt forráskódú platformok ról.](data-lake-storage-supported-open-source-platforms.md)

Lásd: [Nyílt forráskódú platformok, amelyek támogatják az Azure Data Lake Storage Gen2-t.](data-lake-storage-supported-open-source-platforms.md)

## <a name="blob-storage-apis"></a>Blob tárolási API-k

A Blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adaton működhetnek.

Ez a szakasz a blob API-k és a Data Lake Storage Gen2 API-k használatával kapcsolatos problémákat és korlátozásokat ismerteti ugyanazon adatok használatával.

* A Blob API-k és a Data Lake Storage API-k nem használhatók a fájl ugyanazon példányába történő íráshoz. Ha a Data Lake Storage Gen2 API-k használatával ír egy fájlba, akkor a fájl blokkjai nem lesznek láthatók a [blokklista-blobba](https://docs.microsoft.com/rest/api/storageservices/get-block-list) érkező hívások számára. Felülírhat egy fájlt a Data Lake Storage Gen2 API-k vagy a Blob API-k használatával. Ez nincs hatással a fájltulajdonságokra.

* Ha a [Listablobok](https://docs.microsoft.com/rest/api/storageservices/list-blobs) műveletet határolójel megadása nélkül használja, az eredmények könyvtárakat és blobokat is tartalmaznak. Ha úgy dönt, hogy határolójelet használ,`/`csak perjel ( ). Ez az egyetlen támogatott határolójel.

* Ha a [Blob törlése](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API-t használja egy könyvtár törléséhez, a könyvtár csak akkor törlődik, ha üres. Ez azt jelenti, hogy nem használhatja a Blob API-törlési könyvtárak rekurzívan.

Ezek a Blob REST API-k nem támogatottak:

* [Blob felrakása (lap)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Oldal berakása](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Oldaltartományok betöltése](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Növekményes másolási blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Oldal feltöltése URL-címből](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Blob felrakása (hozzáfűzés)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Hozzáfűző blokk](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Blokk hozzáfűzése URL-címből](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

A nem felügyelt virtuálisgép-lemezek nem támogatottak a hierarchikus névtérrel rendelkező fiókokban. Ha hierarchikus névteret szeretne engedélyezni egy tárfiókban, helyezzen nem felügyelt virtuálisgép-lemezeket egy olyan tárfiókba, amelynem rendelkezik a hierarchikus névtér funkció engedélyezésével.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Fájlrendszer-támogatás az SDK-kban

Az ACL-műveletek beszerzése és beállítása jelenleg nem rekurzív.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Fájlrendszer-támogatás a PowerShellben és az Azure CLI-ben

- [A PowerShell](data-lake-storage-directory-file-acl-powershell.md) és az [Azure CLI](data-lake-storage-directory-file-acl-cli.md) támogatása nyilvános előzetes verzióban érhető el.
- Az ACL-műveletek beszerzése és beállítása jelenleg nem rekurzív.

## <a name="lifecycle-management-policies"></a>Életciklus-kezelési irányelvek

* A blob pillanatképek törlése még nem támogatott.  

## <a name="archive-tier"></a>Archiválási szint

Jelenleg van egy hiba, amely hatással van az archív hozzáférési szint.


## <a name="blobfuse"></a>Blobfuse (Blobfuse)

A Blobfuse nem támogatott.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Csak az AzCopy ([AzCopy v10)](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)legújabb verzióját használja.Az AzCopy korábbi verziói, például az AzCopy v8.1, nem támogatottak.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Csak verziókat vagy újabb verziókat `1.6.0` használjon.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer az Azure portalon

Az ACL-ok még nem támogatottak.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Harmadik féltől származó alkalmazások

A REST API-kat használó harmadik féltől származó alkalmazások továbbra is működni fognak, ha a Blob API-kat hívó Data Lake Storage Gen2 alkalmazásokkal használja őket, valószínűleg működni fognak.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Hozzáférés-vezérlési listák (ACL) és névtelen olvasási hozzáférés

Ha egy tárolóhoz [névtelen olvasási hozzáférés](storage-manage-access-to-resources.md) biztosított, akkor az ACL-ok nincsenek hatással a tárolóra vagy a tárolóban lévő fájlokra.

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure Storage Blob (WASB) illesztőprogram (nem támogatott a Data Lake Storage Gen2 szolgáltatással)

Jelenleg a WASB-illesztőprogram, amelyet csak a Blob API-val való együttműködésre terveztek, néhány gyakori forgatókönyvben problémákba ütközik. Pontosabban, ha egy hierarchikus névtér-kompatibilis tárfiók ügyfele. A Data Lake Storage többprotokollos hozzáférése nem enyhíti ezeket a problémákat. 

Egyelőre (és valószínűleg a belátható jövőben) nem támogatjuk azokat az ügyfeleket, akik a WASB-illesztőprogramot használják ügyfélként egy hierarchikus névtér-kompatibilis tárfiókhoz. Ehelyett azt javasoljuk, hogy az [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) illesztőprogramját használja a Hadoop-környezetben. Ha a Hadoop branch-3-nál korábbi verzióval próbál kitérni egy helyszíni Hadoop-környezetből, akkor nyisson meg egy Azure-támogatási jegyet, hogy kapcsolatba léphessünk Önnel a helyes úton az Ön és szervezete számára.
