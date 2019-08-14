---
title: Ismert problémák az Azure Data Lake Storage Gen2 |} A Microsoft Docs
description: További információ a korlátozások és az Azure Data Lake Storage Gen2 ismert problémái
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 369069ef9a9c562ef6ba88a46dc0ef82c4debba1
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950685"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 ismert problémái

Ez a cikk azokat a funkciókat és eszközöket sorolja fel, amelyek még nem támogatottak, vagy csak részlegesen támogatottak olyan Storage-fiókokkal, amelyek hierarchikus névtérrel rendelkeznek (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>A BLOB storage API-k

A blob Storage API-k le vannak tiltva, hogy megakadályozzák a funkciók működőképességét, amelyek felmerülhetnek, mivel Blob Storage API-k még nem működnek együtt Azure Data Lake Gen2 API-kkal.

> [!NOTE]
> Ha a többprotokollos hozzáférés nyilvános előzetesét regisztrálja Data Lake Storage, akkor a blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adattárban működhetnek. További információ: [Data Lake Storage több protokollos hozzáférése](data-lake-storage-multi-protocol-access.md).

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Mi a teendő a meglévő eszközökkel, alkalmazásokkal és szolgáltatásokkal

Ha ezek bármelyike blob API-kat használ, és azt szeretné használni, hogy az összes, a fiókjába feltöltött tartalommal működjön, akkor két lehetőség közül választhat.

* **1. lehetőség**: Ne engedélyezzen hierarchikus névteret a blob Storage-fiókban, amíg a blob API-k nem tudnak együttműködni Azure Data Lake Gen2 API-kkal. Ha a Storage-fiókot hierarchikus névtér nélkül szeretné használni, akkor nem férhet hozzá Data Lake Storage Gen2 adott szolgáltatásokhoz, például a címtárhoz és a fájlrendszer hozzáférés-vezérlési listájához.

* **2. lehetőség**: Regisztráljon a [multi-Protocol hozzáférés](data-lake-storage-multi-protocol-access.md)nyilvános előzetes verziójára Data Lake Storageon. A blob API-kat meghívó eszközök és alkalmazások, valamint a blob Storage-funkciók, például a diagnosztikai naplók a hierarchikus névtérrel rendelkező fiókokkal is működhetnek.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Mi a teendő, ha blob API-kat használt az adatgyűjtés előtt a blob API-k letiltásához

Ha ezekkel az API-adatok betöltése előtt letiltottuk azokat, és a, termelési adatokhoz való hozzáférést, majd lépjen kapcsolatba Microsoft Support a következő információkat:

> [!div class="checklist"]
> * Előfizetés-azonosító (a GUID, nem a név).
> * A Storage-fiók neve (i).
> * Függetlenül attól, hogy aktív hatással van-e az éles üzemre, és ha igen, milyen tárolási fiókokhoz?
> * Akkor is, ha az aktívan nem érintett éles környezetben, akkor mondja el, hogy ezeket az adatokat a valamilyen okból egy másik tárfiókba másolni kell, és ha igen, hogy miért?

Ilyen esetben a blob API-hoz csak korlátozott ideig lehet visszaállítani a hozzáférést, így az ilyen típusú Adatmásolás olyan Storage-fiókba is elvégezhető, amelyen nincs engedélyezve a hierarchikus névtér funkció.

### <a name="issues-and-limitations-with-using-blob-apis-on-accounts-that-have-a-hierarchical-namespace"></a>Problémák és korlátozások a blob API-k használatával hierarchikus névtérrel rendelkező fiókokon

Ha a többprotokollos hozzáférés nyilvános előzetesét regisztrálja Data Lake Storage, akkor a blob API-k és a Data Lake Storage Gen2 API-k ugyanazon az adattárban működhetnek.

Ez a szakasz a blob API-k és a Data Lake Storage Gen2 API-k használatával kapcsolatos problémákat és korlátozásokat ismerteti ugyanazon az adatközponton.

* A blob API-k és a Data Lake Storage API-k nem használhatók a fájlok ugyanazon példányára való íráshoz.

* Ha Data Lake Storage Gen2 API-k használatával ír fájlba egy fájlt, a rendszer nem fogja látni a fájl blokkokat a letiltási [lista](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API-jának meghívásához.

* A fájlokat Data Lake Storage Gen2 API-k vagy blob API-k használatával írhatja felül. Ez nem befolyásolja a fájl tulajdonságait.

* Ha a [lista Blobok](https://docs.microsoft.com/rest/api/storageservices/list-blobs) műveletet határolójel megadása nélkül használja, az eredmények a címtárakat és a blobokat is tartalmazzák.

  Ha elválasztót választ, csak egy perjelet (`/`) használjon. Ez az egyetlen támogatott elválasztó karakter.

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


## <a name="support-for-other-blob-storage-features"></a>Egyéb blob Storage-funkciók támogatása

A következő táblázat felsorolja az összes olyan funkciót és eszközt, amely még nem támogatott, vagy csak részben támogatott olyan Storage-fiókokkal, amelyek hierarchikus névtérrel rendelkeznek (Azure Data Lake Storage Gen2).

| Szolgáltatás/eszköz    | További információ    |
|--------|-----------|
| **Data Lake Storage Gen2 Storage-fiókok API-jai** | Részlegesen támogatott <br><br>a multi-Protocol hozzáférés Data Lake Storage jelenleg nyilvános előzetes verzióban érhető el. Ez az előzetes verzió lehetővé teszi a blob API-k használatát a .NET, a Java, a Python SDK-k és a hierarchikus névteret tartalmazó fiókok használatával.  Az SDK-k még nem tartalmaznak olyan API-kat, amelyek lehetővé teszik a címtárak használatát vagy a hozzáférés-vezérlési listák (ACL-ek) beállítását. A függvények végrehajtásához használhatja Data Lake Storage Gen2 **Rest** API-kat. |
| **AzCopy** | Verzió-specifikus támogatás <br><br>Csak a AzCopy legújabb verzióját használja ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). A AzCopy korábbi verziói (például a AzCopy v 8.1) nem támogatottak.|
| **Az Azure Blob Storage életciklus-kezelési házirendjei** | Csak akkor támogatott, ha Data Lake Storage előzetes verzióban regisztrál a [több protokollos hozzáférésre](data-lake-storage-multi-protocol-access.md) . A ritka elérésű és az archív hozzáférési rétegek csak az előzetes verzióban támogatottak. A blob-Pillanatképek törlése még nem támogatott. |
| **Azure Content Delivery Network (CDN)** | Még nem támogatott|
| **Azure-keresés** |Csak akkor támogatott, ha Data Lake Storage előzetes verzióban regisztrál a [több protokollos hozzáférésre](data-lake-storage-multi-protocol-access.md) .|
| **Azure Storage Explorer** | Verzió-specifikus támogatás <br><br>Csak a vagy `1.6.0` újabb verziót használja. <br>A `1.6.0` verzió [ingyenes letöltésként](https://azure.microsoft.com/features/storage-explorer/)érhető el.|
| **BLOB Container ACL-ek** |Még nem támogatott|
| **Blobfuse** |Még nem támogatott|
| **Egyéni tartományok** |Még nem támogatott|
| **Fájlrendszer Explorer** | Korlátozott támogatás |
| **Diagnosztikai naplózás** |A diagnosztikai naplók csak akkor támogatottak, ha Data Lake Storage előzetes verzióban regisztrál a [több protokollos hozzáférésre](data-lake-storage-multi-protocol-access.md) . <br><br>A naplók engedélyezése a Azure Portal jelenleg nem támogatott. Az alábbi példa bemutatja, hogyan engedélyezheti a naplókat a PowerShell használatával. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Ügyeljen arra, hogy `Blob` a `-ServiceType` paraméter értékeként az ebben a példában látható módon legyen megadva. 
| **Megváltoztathatatlan tároló** |Még nem támogatott <br><br>A nem módosítható tárterület lehetővé teszi az adattárolást egy [féregben (egyszer írható, olvasható)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) állapottal.|
| **Objektum szintű szintek** |A ritka elérésű és az archiválási rétegek csak akkor támogatottak, ha Data Lake Storage előzetes verzióban regisztrál a [multi-Protocol hozzáférésre](data-lake-storage-multi-protocol-access.md) . <br><br> Az összes többi hozzáférési szintet még nem támogatja a rendszer.|
| **A PowerShell és a parancssori felület támogatása** | Korlátozott funkcionalitás <br><br>A felügyeleti műveletek, például a fiókok létrehozása is támogatottak. Az adatsík-műveletek, például a fájlok feltöltése és letöltése nyilvános előzetes verzióban érhető el [Data Lake Storage a többprotokollos hozzáférés](data-lake-storage-multi-protocol-access.md)részeként. A címtárak használata és a hozzáférés-vezérlési listák (ACL-ek) beállítása még nem támogatott. |
| **Statikus webhelyek** |Még nem támogatott <br><br>Konkrétan a fájlok [statikus](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)webhelyekre való kiszolgálásának lehetősége.|
| **Harmadik féltől származó alkalmazások** | Korlátozott támogatás <br><br>A REST API-kat használó harmadik féltől származó alkalmazások továbbra is működni fognak, ha Data Lake Storage Gen2 használatával használják őket. <br>A blob API-kat meghívó alkalmazások valószínűleg akkor fognak működni, ha a többprotokollos [hozzáférés](data-lake-storage-multi-protocol-access.md)nyilvános előzetes verzióját regisztrálja Data Lake Storageon. 
| **Verziószámozási funkciók** |Még nem támogatott <br><br>Ide tartoznak [](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) a pillanatképek és a helyreállítható [Törlés](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|


