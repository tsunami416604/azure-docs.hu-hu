---
title: Ismert problémák a Azure Data Lake Storage Gen2kal | Microsoft Docs
description: Ismerje meg a korlátozásokat és a Azure Data Lake Storage Gen2 ismert problémáit
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 951d707c898ad0efa1f21480c12f0c733f5218ee
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834940"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Ismert problémák a Azure Data Lake Storage Gen2

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

<a id="api-scope-data-lake-client-library" />

## <a name="filesystem-support-in-sdks"></a>Fájlrendszer-támogatás SDK-k esetén

- A [.net](data-lake-storage-directory-file-acl-dotnet.md), a [Java](data-lake-storage-directory-file-acl-java.md) és a [Python](data-lake-storage-directory-file-acl-python.md) támogatás nyilvános előzetes verzióban érhető el. Más SDK-k jelenleg nem támogatottak.
- Az ACL-műveletek beolvasása és beállítása jelenleg nem rekurzív.

## <a name="filesystem-support-in-powershell-and-azure-cli"></a>Fájlrendszer-támogatás a PowerShellben és az Azure CLI-ben

- A [PowerShell](data-lake-storage-directory-file-acl-powershell.md) és az [Azure CLI](data-lake-storage-directory-file-acl-cli.md) támogatása nyilvános előzetes verzióban érhető el.
- Az ACL-műveletek beolvasása és beállítása jelenleg nem rekurzív.

## <a name="support-for-other-blob-storage-features"></a>Más Blob Storage-funkciók támogatása

A következő táblázat felsorolja az összes olyan funkciót és eszközt, amely még nem támogatott, vagy csak részben támogatott olyan Storage-fiókokkal, amelyek hierarchikus névtérrel rendelkeznek (Azure Data Lake Storage Gen2).

| Szolgáltatás/eszköz    | További információ    |
|--------|-----------|
| **Fiók feladatátvétele** |Még nem támogatott|
| **AzCopy** | Verzió-specifikus támogatás <br><br>Csak a AzCopy legújabb verzióját használja ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). A AzCopy korábbi verziói (például a AzCopy v 8.1) nem támogatottak.|
| **Azure Blob Storage életciklus-kezelési szabályzatok** | Az életciklus-kezelési házirendek támogatottak (előzetes verzió).  Regisztráljon az életciklus-kezelési szabályzatok előzetes verziójára, és archiválja a hozzáférési szintet [itt](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).   <br><br>Minden hozzáférési szintet támogat. Az archív hozzáférési szint jelenleg előzetes verzióban érhető el. A blob-Pillanatképek törlése még nem támogatott.  Jelenleg vannak olyan hibák, amelyek hatással vannak az életciklus-kezelési házirendekre és az archív hozzáférési szintre.  |
| **Azure Content Delivery Network (CDN)** | Még nem támogatott|
| **Azure-keresés** |Támogatott (előzetes verzió)|
| **Azure Storage Explorer** | Verzió-specifikus támogatás. <br><br>Csak `1.6.0` vagy újabb verziót használjon. <br> Jelenleg van olyan tárolási hiba, amely hatással van a `1.11.0` verzióra, ami bizonyos helyzetekben hitelesítési hibákhoz vezethet. A tárolási hiba elhárítása folyamatban van, de megkerülő megoldásként javasoljuk, hogy a `1.10.x` verzióját használja, amely [ingyenes letöltésként](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)érhető el. a tárolási hiba nem érinti a `1.10.x`.|
| **BLOB Container ACL-ek** |Még nem támogatott|
| **Blobfuse** |Még nem támogatott|
| **Egyéni tartományok** |Még nem támogatott|
| **Storage Explorer a Azure Portal** | Korlátozott támogatás. Az ACL-ek még nem támogatottak. |
| **Diagnosztikai naplózás** |A diagnosztikai naplók támogatottak (előzetes verzió). <br><br>Azure Storage Explorer 1.10. x nem használható a diagnosztikai naplók megtekintéséhez. A naplók megtekintéséhez használja a AzCopy vagy SDK-kat.
| **Megváltoztathatatlan tároló** |Még nem támogatott <br><br>A nem módosítható tárterület lehetővé teszi az adattárolást egy [féregben (egyszer írható, olvasható)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) állapottal.|
| **Objektum szintű szintek** |A ritka elérésű és az archív rétegek támogatottak. Az archiválási szint előzetes verzióban érhető el. Az összes többi hozzáférési szintet még nem támogatja a rendszer. <br><br> Jelenleg vannak olyan hibák, amelyek hatással vannak az archív hozzáférési szintre.  Regisztráljon az archív [hozzáférési szint előzetes](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u)verziójára.|
| **Statikus webhelyek** |Még nem támogatott <br><br>Konkrétan a fájlok [statikus webhelyekre](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)való kiszolgálásának lehetősége.|
| **Harmadik féltől származó alkalmazások** | Korlátozott támogatás <br><br>A REST API-kat használó harmadik féltől származó alkalmazások továbbra is működni fognak, ha Data Lake Storage Gen2 használatával használják őket. <br>A blob API-kat meghívó alkalmazások valószínűleg működni fognak.|
|**Helyreállítható törlés** |Még nem támogatott|
| **Verziószámozási funkciók** |Még nem támogatott <br><br>Ez magában foglalja a [Soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)és más verziószámozási funkciókat, például a [pillanatképeket](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


