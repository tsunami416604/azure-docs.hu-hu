---
title: Ismert problémák az Azure Data Lake Storage Gen2 |} A Microsoft Docs
description: További információ a korlátozások és az Azure Data Lake Storage Gen2 ismert problémái
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: 27adc0eeeabed2b1f2e86f301a60604a3d358b82
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464723"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 ismert problémái

Ez a cikk felsorolja a szolgáltatásokat és eszközöket, amelyek még nem támogatott, illetve a storage-fiókok (az Azure Data Lake Storage Gen2) hierarchikus névtérrel rendelkező csak részlegesen támogatott.

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>A BLOB storage API-k

A BLOB storage API-k sikerült merülnek fel, mert a Blob Storage API-k még nem működik együtt az Azure Data Lake Gen2 API-k nem szándékos adatok hozzáférési problémák elkerülése érdekében le vannak tiltva.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Mit kell tenni a meglévő eszközökkel, alkalmazásokkal és szolgáltatásokkal

Ha ezeket használja a Blob API-kat, és ezek segítségével dolgozhat a fiókjába feltöltött tartalom mindegyikét, majd nem engedélyezi a Blob storage-fiókjában hierarchikus névtér mindaddig, amíg a Blob API-k válnak együttműködésre képes az Azure Data Lake Gen2 API-k.

Hierarchikus névtér nélkül-tárfiókok használata azt jelenti, akkor nem rendelkezik hozzáféréssel a Data Lake Storage Gen2 funkciók, például a fájl és könyvtár rendszer hozzáférés-vezérlési listák.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>Mit kell tenni a virtuális gép (VM) a nem felügyelt lemezek

Ezek a letiltott Blob Storage API-k attól függenek, ezért ha szeretné engedélyezni a storage-fiók, hierarchikus névtér, érdemes helyezi őket a storage-fiókra, amely nem rendelkezik a hierarchikus névtér szolgáltatás engedélyezve van.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Mi a teendő, ha Blob API-k segítségével adatok betöltése előtt Blob API-k letiltottuk

Ha ezekkel az API-adatok betöltése előtt letiltottuk azokat, és a, termelési adatokhoz való hozzáférést, majd lépjen kapcsolatba Microsoft Support a következő információkat:

> [!div class="checklist"]
> * Előfizetés-azonosító (GUID, nem a nevét).
> * Storage-fiók neve.
> * Éles környezetben aktívan érinti-e, és ha igen, melyik storage-fiókok?.
> * Akkor is, ha az aktívan nem érintett éles környezetben, akkor mondja el, hogy ezeket az adatokat a valamilyen okból egy másik tárfiókba másolni kell, és ha igen, hogy miért?

Ilyen körülmények azt is visszaállíthatók hozzáférés a Blob API-t egy korlátozott ideig, hogy a storage-fiókra, amely nem rendelkezik a hierarchikus névtér szolgáltatás engedélyezve van az adatokat másolja.

## <a name="all-other-features-and-tools"></a>Más funkciók és eszközök

A következő táblázat sorolja fel, minden más szolgáltatásokat és eszközöket, amelyek még nem támogatott, illetve a storage-fiókok (az Azure Data Lake Storage Gen2) hierarchikus névtérrel rendelkező csak részlegesen támogatott.

| Szolgáltatás / eszköz    | További információ    |
|--------|-----------|
| **Data Lake Storage Gen2 storage-fiókok API-k** | Részben támogatott <br><br>Használhatja a Data Lake Storage Gen2 **REST** API-kat, de más Blob SDK-k, például a .NET, Java, Python SDK-k API-k még nem érhető el.|
| **AzCopy** | Verzió-specifikus támogatása <br><br>Csak az AzCopy legújabb verzióját használja ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Az AzCopy AzCopy v8.1, mint a korábbi verziók nem támogatottak.|
| **Az Azure Blob storage életciklus felügyeleti házirendek** | Még nem támogatott |
| **Az Azure Content Delivery Network (CDN)** | Még nem támogatott|
| **Azure Event Grid** | Még nem támogatott |
| **Az Azure search** |Még nem támogatott|
| **Azure Storage Explorer** | Verzió-specifikus támogatása <br><br>Csak verziót használja `1.6.0` vagy újabb verziója. <br>Verzió `1.6.0` elérhető egy [ingyenesen letölthető](https://azure.microsoft.com/features/storage-explorer/).|
| **BLOB-tároló hozzáférés-vezérlési listák** |Még nem támogatott|
| **Blobfuse** |Még nem támogatott|
| **Egyéni tartományok** |Még nem támogatott|
| **Diagnosztikai naplók** |Még nem támogatott|
| **Fájlkezelő rendszer** | Korlátozott támogatás |
| **A tároló nem módosítható** |Még nem támogatott <br><br>Nem módosítható storage lehetővé teszi az adatok tárolása egy [FÉREG (egyszer írható, olvassa el számos)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) állapota.|
| **Az Objektumszintű rétegek** |Még nem támogatott <br><br>Példa: Prémium szintű, gyors Elérésűre, ritka elérésű és az archív szint.|
| **Támogatja a PowerShell és CLI** | Korlátozott funkciók <br><br>A Powershell vagy a parancssori felület használatával létrehozhat egy fiókot. Műveletek végrehajtására nem vagy hozzáférés-vezérlési listák beállítása a fájlrendszerek, könyvtárak és fájlok.|
| **Statikus webhely** |Még nem támogatott <br><br>Pontosabban, lehetővé teszi a fájlok kiszolgálása [statikus webhelyek kiszolgálására](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Harmadik féltől származó alkalmazások** | Korlátozott támogatás <br><br>REST API-kat használják a harmadik féltől származó alkalmazások továbbra is működik, ha a Data Lake Storage Gen2 használja azokat. <br>Ha Blob API-kat használó alkalmazás, az alkalmazás valószínűleg rendelkezik problémák a Data Lake Storage Gen2 adott alkalmazás használatakor. További tudnivalókért tekintse meg a [a Blob storage API-k le vannak tiltva, a Data Lake Storage Gen2 tárfiókban](#blob-apis-disabled) című szakaszát.|
| **Verziókezelés funkciók** |Még nem támogatott <br><br>Ez magában foglalja [pillanatképek](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) és [helyreállítható törlési](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|
|

