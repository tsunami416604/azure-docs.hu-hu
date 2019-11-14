---
title: Ismert problémák az Azure Data Lake Storage Gen2 |} A Microsoft Docs
description: További információ a korlátozások és az Azure Data Lake Storage Gen2 ismert problémái
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 78693dceaac119279b1c1d06a6c3a18cc4fdb485
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033944"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 ismert problémái

Ez a cikk azokat a funkciókat és eszközöket sorolja fel, amelyek még nem támogatottak, vagy csak részlegesen támogatottak olyan Storage-fiókokkal, amelyek hierarchikus névtérrel rendelkeznek (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>A blob API-k használatával kapcsolatos problémák és korlátozások

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

## <a name="support-for-other-blob-storage-features"></a>Más Blob Storage-funkciók támogatása

A következő táblázat felsorolja az összes olyan funkciót és eszközt, amely még nem támogatott, vagy csak részben támogatott olyan Storage-fiókokkal, amelyek hierarchikus névtérrel rendelkeznek (Azure Data Lake Storage Gen2).

| Szolgáltatás/eszköz    | További információ    |
|--------|-----------|
| **Data Lake Storage Gen2 API-k** | Részlegesen támogatott <br><br>A jelenlegi kiadásban a Data Lake Storage Gen2 **Rest** API-k használatával kommunikálhat a címtárakkal, és beállíthatja a hozzáférés-vezérlési listákat (ACL-eket), de nincs más SDK (például: .net, Java vagy Python) a feladatok végrehajtásához. Más feladatok (például fájlok feltöltése és letöltése) elvégzéséhez használhatja a blob SDK-kat.  |
| **AzCopy** | Verzió-specifikus támogatás <br><br>Csak a AzCopy legújabb verzióját használja ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). A AzCopy korábbi verziói (például a AzCopy v 8.1) nem támogatottak.|
| **Azure Blob Storage életciklus-kezelési szabályzatok** | Minden hozzáférési szintet támogat. Az archív hozzáférési szint jelenleg előzetes verzióban érhető el. A blob-Pillanatképek törlése még nem támogatott. |
| **Azure Content Delivery Network (CDN)** | Még nem támogatott|
| **Azure-keresés** |Támogatott (előzetes verzió)|
| **Azure Storage Explorer** | Verzió-specifikus támogatás <br><br>Csak a `1.10.0``1.6.0` verziókat használja. <br> A verzió `1.10.0` [ingyenes letöltésként](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)érhető el. A `1.11.0` verziója még nem támogatott.|
| **BLOB Container ACL-ek** |Még nem támogatott|
| **Blobfuse** |Még nem támogatott|
| **Egyéni tartományok** |Még nem támogatott|
| **Storage Explorer a Azure Portal** | Korlátozott támogatás. Az ACL-ek még nem támogatottak. |
| **Diagnosztikai naplózás** |A diagnosztikai naplók támogatottak (előzetes verzió).<br><br>A naplók engedélyezése a Azure Portal jelenleg nem támogatott. Az alábbi példa bemutatja, hogyan engedélyezheti a naplókat a PowerShell használatával. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Győződjön meg arról, hogy a `-ServiceType` paraméter értékeként `Blob` értéket kell megadnia, ahogy az ebben a példában is látható. <br><br>A Azure Storage Explorer jelenleg nem használható a diagnosztikai naplók megtekintéséhez. A naplók megtekintéséhez használja a AzCopy vagy SDK-kat.
| **Megváltoztathatatlan tároló** |Még nem támogatott <br><br>A nem módosítható tárterület lehetővé teszi az adattárolást egy [féregben (egyszer írható, olvasható)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) állapottal.|
| **Objektum szintű szintek** |A ritka elérésű és az archív rétegek támogatottak. Az archiválási szint előzetes verzióban érhető el. Az összes többi hozzáférési szintet még nem támogatja a rendszer.|
| **A PowerShell és a parancssori felület támogatása** | Korlátozott funkcionalitás <br><br>A blob-műveletek támogatottak. A címtárak használata és a hozzáférés-vezérlési listák (ACL-ek) beállítása még nem támogatott. |
| **Statikus webhelyek** |Még nem támogatott <br><br>Konkrétan a fájlok [statikus webhelyekre](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)való kiszolgálásának lehetősége.|
| **Harmadik féltől származó alkalmazások** | Korlátozott támogatás <br><br>A REST API-kat használó harmadik féltől származó alkalmazások továbbra is működni fognak, ha Data Lake Storage Gen2 használatával használják őket. <br>A blob API-kat meghívó alkalmazások valószínűleg működni fognak.|
|**Helyreállítható törlés** |Még nem támogatott|
| **Verziószámozási funkciók** |Még nem támogatott <br><br>Ez magában foglalja a [Soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)és más verziószámozási funkciókat, például a [pillanatképeket](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


