---
title: Az Azure Media Services eszközök |} Microsoft Docs
description: Ez a cikk lehetőséget ad olyan eszközök vannak, és megismerkedhet használatukkal Azure Media Services által ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 61555eb6cca6995215ce43051abbda9aa43539ec
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284838"
---
# <a name="assets"></a>Objektumok

Egy **eszköz** digitális fájlok (például a videó, hang, képeket, miniatűröket, szöveges nyomon követi és feliratfájlokat fájlok) és a mindezen fájlok metaadatait tartalmazza. Miután a digitális fájlok feltöltése az objektumba, akkor a Media Services kódolási és adatfolyam-munkafolyamatok is használható.

Egy eszköz egy blob-tároló az van leképezve a [Azure Storage-fiók](storage-account-concept.md) és az adott tároló blokkblobként tárolja a fájlokat az eszköz. Az adategység-fájloknak a tárolókban, a Storage szolgáltatás SDK-ügyfél használatával kommunikálhat.

Az Azure Media Services támogatja a Blob rétegek, ha a fiók használ az általános célú v2 (GPv2) tároló. A GPv2 áthelyezheti hűtésével fájlok vagy a cold tároló. Cold tárolási alkalmas archiválás forrásfájlokat, ha már nem szükséges (például után azok van kódolva.)

A Media Services v3 a feladat bemeneti is létrehozható eszközök vagy a http (s) URL-címeket. Egy eszköz, a feladat használható bemenetként létrehozásához lásd: [létrehozni a feladat bemeneti helyi fájlból](job-input-from-local-file-how-to.md).

Emellett olvassa [storage-fiókok a Media Services](storage-account-concept.md) és [átalakítások, a feladatok](transform-concept.md).

## <a name="asset-definition"></a>Eszköz meghatározása

A következő táblázat az eszköz tulajdonságok láthatók, és lehetőséget ad a definíciójukat.

|Name (Név)|Típus|Leírás|
|---|---|---|
|Azonosító|sztring|Az erőforrás teljes erőforrás-azonosító.|
|név|sztring|Az erőforrás neve.|
|properties.alternateId |sztring|Az eszköz a másik azonosító.|
|properties.assetId |sztring|Az eszköz azonosítója.|
|Properties.Container |sztring|Az eszköz blob tároló nevét.|
|Properties.created |sztring|Az eszköz létrehozásának dátuma.|
|properties.description |sztring|Az eszköz leírása.|
|properties.lastModified |sztring|Az utolsó módosításának dátuma vagy az eszköz.|
|properties.storageAccountName |sztring|A tárfiók neve.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |Az eszköz titkosítási formátumban. Nincs egyikét vagy MediaStorageEncryption.|
|type|sztring|Az erőforrás típusa.|

A teljes meghatározása: [eszközök](https://docs.microsoft.com/rest/api/media/assets).

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, lapozás

A Media Services a következő OData-lekérdezés beállításai eszközöket támogatja: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>Szűrés/rendezés

Az alábbi táblázat bemutatja, hogyan lehet alkalmazni ezeket a beállításokat az eszköz tulajdonságok: 

|Name (Név)|Szűrés|Sorrend|
|---|---|---|
|Azonosító|A következőket támogatja:<br/>Egyenlő<br/>Nagyobb mint<br/>Kisebb, mint|A következőket támogatja:<br/>Növekvő<br/>Csökkenő|
|név|||
|properties.alternateId |A következőket támogatja:<br/>Egyenlő||
|properties.assetId |A következőket támogatja:<br/>Egyenlő||
|Properties.Container |||
|Properties.created|A következőket támogatja:<br/>Egyenlő<br/>Nagyobb mint<br/>Kisebb, mint|A következőket támogatja:<br/>Növekvő<br/>Csökkenő|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

A következő C# példa szűrő a létrehozás dátuma:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Tördelés

A négy engedélyezve van a rendezési sorrend mindegyikének tördelési támogatott. 

Ha a lekérdezési válasz tartalmaz számos (jelenleg keresztül 1000) elem, a szolgáltatás adja vissza egy "\@odata.nextLink" tulajdonság használatával beolvassa a következő oldalra. Ez használható a teljes eredménykészlet keresztül lapra. Az oldalméret nincs a felhasználó által konfigurálható. 

Ha az eszközök létrehozása vagy törlése során a gyűjtemény lapozást, a módosítások megjelennek a visszaadott eredmények (ha ezeket a módosításokat a részében a gyűjteményt, amely nem töltődött le.) 

A következő C# példa bemutatja, hogyan keresztül az eszközök a fiók enumerálása.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

További példákért lásd [eszközök – lista](https://docs.microsoft.com/rest/api/media/assets/list)


## <a name="storage-side-encryption"></a>Tárolási kiszolgálóoldali titkosítás

Az inaktív eszközök védelme érdekében az eszközöket a storage ügyféloldali titkosítása titkosítani kell. Az alábbi táblázat a storage ügyféloldali titkosítás a Media Services működése:

|a titkosítási beállítás|Leírás|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-tárolás titkosítása|AES-256 titkosítás, kulcs Media Services által felügyelt|Támogatott<sup>(1)</sup>|Nem támogatott<sup>(2)</sup>|
|[Storage szolgáltatás titkosítási az inaktív adatok](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kiszolgálóoldali titkosítás Azure Storage által kínált kulcsát kezeli az Azure-ban vagy az ügyfél által|Támogatott|Támogatott|
|[Storage ügyféloldali titkosítása](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure-tárolót, a Key Vault ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|Nem támogatott|

<sup>1</sup> közben a Media Services kezelni a tartalom szövegként/bármilyen titkosítás nélkül, ez nem ajánlott.

<sup>2</sup> v3 a Media Services, storage encryption (az AES-256 titkosítás) van csak a visszamenőleges kompatibilitás létrehozásakor támogatott az eszközök volt a Media Services v2. Tehát a meglévő tárhely v3 együttműködik eszközök titkosítottak, de nem teszi lehetővé a újakat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fájl streamelése](stream-files-dotnet-quickstart.md)
