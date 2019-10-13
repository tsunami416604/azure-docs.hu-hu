---
title: Ismert problémák a Azure Data Lake Storage Gen2kal | Microsoft Docs
description: Ismerje meg a korlátozásokat és a Azure Data Lake Storage Gen2 ismert problémáit
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 300da59aa1a16bb2c4cfeaf8035cbe882ae83358
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300245"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Ismert problémák a Azure Data Lake Storage Gen2

Ez a cikk azokat a funkciókat és eszközöket sorolja fel, amelyek még nem támogatottak, vagy csak részlegesen támogatottak olyan Storage-fiókokkal, amelyek hierarchikus névtérrel rendelkeznek (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Blob Storage API-k

Blob Storage API-k le vannak tiltva, hogy megakadályozzák a szolgáltatásokkal kapcsolatos olyan problémákat, amelyek felmerülhetnek, mert Blob Storage API-k még nem működnek együtt Azure Data Lake Gen2 API-kkal

> [!NOTE]
> A többprotokollos hozzáférés nyilvános előzetes verziója Data Lake Storage, a blob API-k és az Data Lake Storage Gen2 API-k ugyanazon az adatközponton működhetnek. További információ: [Data Lake Storage több protokollos hozzáférése](data-lake-storage-multi-protocol-access.md).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Mi a teendő a meglévő eszközökkel, alkalmazásokkal és szolgáltatásokkal

Ha ezek bármelyike blob API-kat használ, és a fiókban lévő összes tartalommal együtt szeretne dolgozni, akkor két lehetőség közül választhat.

* **1. lehetőség**: Ne engedélyezzen hierarchikus névteret a blob Storage-fiókjában, amíg [Data Lake Storage a többprotokollos hozzáférés](data-lake-storage-multi-protocol-access.md) általánosan elérhető, és a blob API-k teljes mértékben interoperábilisak Azure Data Lake Gen2 API-kkal. [A multi-Protocol hozzáférés Data Lake Storage](data-lake-storage-multi-protocol-access.md) jelenleg nyilvános előzetes verzióban érhető el.  Ha a Storage-fiókot hierarchikus névtér **nélkül** szeretné használni, akkor nem fog hozzáférni Data Lake Storage Gen2 adott szolgáltatásokhoz, például a címtárhoz és a tároló hozzáférés-vezérlési listájához.

* **2. lehetőség**: hierarchikus névterek engedélyezése. A [többprotokollos hozzáférés](data-lake-storage-multi-protocol-access.md)nyilvános előzetese Data Lake Storage, a blob API-kat meghívó eszközökön és alkalmazásokon, valamint blob Storage szolgáltatásokon, például a diagnosztikai naplókban is működhet a hierarchikus névtérrel rendelkező fiókokkal. Az ismert problémákkal és korlátozásokkal kapcsolatban tekintse át ezt a cikket.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Mi a teendő, ha blob API-kat használt az adatgyűjtés előtt a blob API-k letiltásához

Ha ezeket az API-kat használta az adatok betöltéséhez a letiltásuk előtt, és éles környezetben van szükség az adatok elérésére, akkor vegye fel a kapcsolatot Microsoft ügyfélszolgálata a következő információkkal:

> [!div class="checklist"]
> * Előfizetés-azonosító (a GUID, nem a név).
> * A Storage-fiók neve (i).
> * Függetlenül attól, hogy aktív hatással van-e az éles üzemre, és ha igen, milyen tárolási fiókokhoz?
> * Még ha nincs aktívan hatással az éles üzemre, mondja el, hogy szükség van-e arra, hogy más Storage-fiókba másolja ezeket az adatfájlokat valamilyen okból, és ha igen, miért?

Ilyen esetben a blob API-hoz csak korlátozott ideig lehet visszaállítani a hozzáférést, így az ilyen típusú Adatmásolás olyan Storage-fiókba is elvégezhető, amelyen nincs engedélyezve a hierarchikus névtér funkció.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problémák és korlátozások a blob API-k használatával hierarchikus névtérrel rendelkező fiókokon

A többprotokollos hozzáférés nyilvános előzetes verziója Data Lake Storage, a blob API-k és az Data Lake Storage Gen2 API-k ugyanazon az adatközponton működhetnek.

Ez a szakasz a blob API-k és a Data Lake Storage Gen2 API-k használatával kapcsolatos problémákat és korlátozásokat ismerteti ugyanazon az adatközponton.

* A blob API-k és a Data Lake Storage API-k nem használhatók a fájlok ugyanazon példányára való íráshoz.

* Ha Data Lake Storage Gen2 API-k használatával ír fájlba egy fájlt, a rendszer nem fogja látni a fájl blokkokat a [letiltási lista](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API-jának meghívásához.

* A fájlokat Data Lake Storage Gen2 API-k vagy blob API-k használatával írhatja felül. Ez nem befolyásolja a fájl tulajdonságait.

* Ha a [lista Blobok](https://docs.microsoft.com/rest/api/storageservices/list-blobs) műveletet határolójel megadása nélkül használja, az eredmények a címtárakat és a blobokat is tartalmazzák.

  Ha elválasztót választ, csak egy perjelet használjon (`/`). Ez az egyetlen támogatott elválasztó karakter.

* Ha a [blob törlése](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API-t használja egy könyvtár törléséhez, akkor a rendszer csak akkor törli a könyvtárat, ha üres.

  Ez azt jelenti, hogy a blob API rekurzív törlése nem végezhető el.

Ezek a blob REST API-k nem támogatottak:

* [BLOB (oldal) elhelyezése](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Oldal elhelyezése](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Oldalak tartományának beolvasása](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Növekményes másolási blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Oldal elhelyezése az URL-címről](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [BLOB elhelyezése (Hozzáfűzés)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Blokk hozzáfűzése](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Blokk hozzáfűzése URL-címről](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

## <a name="issues-with-unmanaged-virtual-machine-vm-disks"></a>Nem felügyelt virtuálisgép-lemezekkel kapcsolatos problémák

A nem felügyelt virtuálisgép-lemezek nem támogatottak olyan fiókoknál, amelyek hierarchikus névtérrel rendelkeznek. Ha egy hierarchikus névteret szeretne engedélyezni egy Storage-fiókban, helyezze a nem felügyelt virtuális gépek lemezeit olyan Storage-fiókba, amelyben nincs engedélyezve a hierarchikus névtér funkció.


## <a name="support-for-other-blob-storage-features"></a>Más Blob Storage-funkciók támogatása

A következő táblázat felsorolja az összes olyan funkciót és eszközt, amely még nem támogatott, vagy csak részben támogatott olyan Storage-fiókokkal, amelyek hierarchikus névtérrel rendelkeznek (Azure Data Lake Storage Gen2).

| Szolgáltatás/eszköz    | További információ    |
|--------|-----------|
| **Data Lake Storage Gen2 Storage-fiókok API-jai** | Részlegesen támogatott <br><br>A multi-Protocol hozzáférés Data Lake Storage jelenleg nyilvános előzetes verzióban érhető el. Ez az előzetes verzió lehetővé teszi a blob API-k használatát a .NET, a Java, a Python SDK-k és a hierarchikus névteret tartalmazó fiókok használatával.  Az SDK-k még nem tartalmaznak olyan API-kat, amelyek lehetővé teszik a címtárak használatát vagy a hozzáférés-vezérlési listák (ACL-ek) beállítását. A függvények végrehajtásához használhatja Data Lake Storage Gen2 **Rest** API-kat. |
| **AzCopy** | Verzió-specifikus támogatás <br><br>Csak a AzCopy legújabb verzióját használja ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). A AzCopy korábbi verziói (például a AzCopy v 8.1) nem támogatottak.|
| **Azure Blob Storage életciklus-kezelési szabályzatok** | A [több protokollos hozzáférés Data Lake Storage](data-lake-storage-multi-protocol-access.md) előzetes verzióban támogatott. A ritka elérésű és az archív hozzáférési rétegek csak az előzetes verzióban támogatottak. A blob-Pillanatképek törlése még nem támogatott. |
| **Azure Content Delivery Network (CDN)** | Még nem támogatott|
| **Azure-keresés** |A [több protokollos hozzáférés Data Lake Storage](data-lake-storage-multi-protocol-access.md) előzetes verzióban támogatott.|
| **Azure Storage Explorer** | Verzió-specifikus támogatás <br><br>Csak `1.6.0` vagy újabb verziót használjon. <br>A `1.6.0` verzió [ingyenes letöltésként](https://azure.microsoft.com/features/storage-explorer/)érhető el.|
| **BLOB Container ACL-ek** |Még nem támogatott|
| **Blobfuse** |Még nem támogatott|
| **Egyéni tartományok** |Még nem támogatott|
| **Fájlrendszer Explorer** | Korlátozott támogatás |
| **Diagnosztikai naplózás** |A Data Lake Storage előzetes verziójában a [több protokollos hozzáférés](data-lake-storage-multi-protocol-access.md) is támogatja a diagnosztikai naplókat. <br><br>A naplók engedélyezése a Azure Portal jelenleg nem támogatott. Az alábbi példa bemutatja, hogyan engedélyezheti a naplókat a PowerShell használatával. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>` kérdésre adott válaszban foglalt lépéseket. <br><br>Ügyeljen arra, hogy a `Blob` értéket a `-ServiceType` paraméter értékeként határozza meg, ahogy az ebben a példában is látható. <br><br>A Azure Storage Explorer jelenleg nem használható a diagnosztikai naplók megtekintéséhez. A naplók megtekintéséhez használja a AzCopy vagy SDK-kat.
| **Megváltoztathatatlan tároló** |Még nem támogatott <br><br>A nem módosítható tárterület lehetővé teszi az adattárolást egy [féregben (egyszer írható, olvasható)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) állapottal.|
| **Objektum szintű szintek** |A ritka elérésű és az archiválási szint a [több protokollos hozzáférés Data Lake Storage](data-lake-storage-multi-protocol-access.md) előzetes verzióban támogatott. <br><br> Az összes többi hozzáférési szintet még nem támogatja a rendszer.|
| **A PowerShell és a parancssori felület támogatása** | Korlátozott funkcionalitás <br><br>A felügyeleti műveletek, például a fiókok létrehozása is támogatottak. Az adatsík-műveletek, például a fájlok feltöltése és letöltése nyilvános előzetes verzióban érhető el [Data Lake Storage a többprotokollos hozzáférés](data-lake-storage-multi-protocol-access.md)részeként. A címtárak használata és a hozzáférés-vezérlési listák (ACL-ek) beállítása még nem támogatott. |
| **Statikus webhelyek** |Még nem támogatott <br><br>Konkrétan a fájlok [statikus webhelyekre](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)való kiszolgálásának lehetősége.|
| **Harmadik féltől származó alkalmazások** | Korlátozott támogatás <br><br>A REST API-kat használó harmadik féltől származó alkalmazások továbbra is működni fognak, ha Data Lake Storage Gen2 használatával használják őket. <br>A blob API-kat meghívó alkalmazások valószínűleg a [Data Lake Storage több protokollos hozzáférésének](data-lake-storage-multi-protocol-access.md)nyilvános előzetes verziójával fognak működni. 
| **Verziószámozási funkciók** |Még nem támogatott <br><br>Ide tartoznak a [Pillanatképek](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) és a helyreállítható [Törlés](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|


