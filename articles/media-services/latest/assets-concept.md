---
title: Eszközök a Media Services – Azure |} A Microsoft Docs
description: Ez a cikk biztosítja az eszközök vannak, és hogyan használják az Azure Media Services ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f9a6f0963ce8f45da567bb4f6326e9fcc8f435ef
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140132"
---
# <a name="assets"></a>Objektumok

Egy **eszköz** mindezen fájlok metaadatait és a digitális fájlok (beleértve a videókhoz, audiofájlokhoz, képeket, miniatűröket, szövegsávok és a feliratfájlok) tartalmaz. Miután a digitális fájlok feltöltése egy adategységbe, azok a Media Services encoding és adatfolyam-munkafolyamatok használható.

Egy eszköz található blob-tárolóra lesz leképezve a [Azure Storage-fiók](storage-account-concept.md) és az adategységben található fájlokat a tárolóban blokkblobként vannak tárolva. Az adategység-fájlok, a tárolókban, a Storage SDK-ügyfél használatával kezelheti.

Az Azure Media Services támogatja a Blob-rétegek, ha a fiók használ az általános célú v2 (GPv2) storage. A GPv2 továbbléphet a fájlokat a ritka elérésű vagy ritka elérésű tárolási. Ritka elérésű tárolási ideális archiválása a forrásfájlokat, ha már nincs szükség (például, miután azok van kódolva.)

A Media Services v3-as a feladat bemenetére is létrehozható eszközök vagy a http (s) URL-címeket. Hozzon létre egy objektumot, a feladat bemeneteként felhasználható, lásd: [hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md).

Emellett olvassa [storage-fiókok a Media Services](storage-account-concept.md) és [átalakítások és feladatok](transform-concept.md).

## <a name="asset-definition"></a>Az eszközintelligencia-definíció

Az alábbi táblázat az objektum tulajdonságait jeleníti meg, és lehetővé teszi a definíciójukat.

|Name (Név)|Leírás|
|---|---|
|id|Az erőforrás teljes erőforrás-azonosítója.|
|név|Az erőforrás neve.|
|properties.alternateId |Az eszköz a másik azonosító.|
|properties.assetId |Az eszköz azonosítója.|
|Properties.Container |Az eszközintelligencia blobtároló neve.|
|Properties.created |A létrehozást, az eszköz.|
|properties.description|Az eszköz leírása.|
|properties.lastModified |Az utolsó módosítás dátuma az eszköz.|
|properties.storageAccountName |A tárfiók neve.|
|properties.storageEncryptionFormat |Az eszköz titkosításának formátuma. Egyik sem, vagy MediaStorageEncryption.|
|type|Az erőforrás típusát.|

A teljes meghatározása: [eszközök](https://docs.microsoft.com/rest/api/media/assets).

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

A Media Services a következő OData lekérdezési beállítások eszközöket támogatja: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Operátor leírása:

* EQ = egyenlő
* Ú = nem egyenlő
* A GE = nagyobb vagy egyenlő
* Le = kisebb vagy egyenlő
* Gt = nagyobb, mint
* Lt = kisebb, mint

### <a name="filteringordering"></a>Szűrés és rendezés

Az alábbi táblázat bemutatja, hogyan lehet alkalmazni ezeket a beállításokat az eszköz tulajdonságok: 

|Name (Név)|Szűrés|Rendelés|
|---|---|---|
|id|||
|név|Támogatott rendszerek: Eq, Gt, Lt|Támogatja: növekvő vagy csökkenő sorrendben|
|properties.alternateId |Támogatja: Eq||
|properties.assetId |Támogatja: Eq||
|Properties.Container |||
|Properties.created|Támogatott rendszerek: Eq, Gt, Lt| Támogatott rendszerek: Növekvő vagy csökkenő sorrendben|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

Az alábbi C#-példa szűrőit a létrehozás dátuma:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Tördelés

Tördelés a négy engedélyezve van a rendezési sorrend mindegyike támogatott. Az oldalméret jelenleg 1000.

> [!TIP]
> A gyűjtemény enumerálása, és nem függ egy adott oldal méretét a következő hivatkozás mindig használjon.

A lekérdezési válasz számos elemet tartalmaz, ha a szolgáltatás visszaadja egy "\@odata.nextLink" tulajdonságát a következő lapra az eredmények lekérése. Ez használható a lapozza végig a teljes eredményhalmaz. Az oldal méretét nem lehet konfigurálni. 

Eszközök létrehozott vagy a gyűjtemény átlapozva közben, ha a módosítások megjelennek a kapott találatok közül (ha ezek a módosítások a gyűjteményt, amely még nincs letöltve részén.) 

Az alábbi C#-példa bemutatja a fiókban lévő összes eszköz számba.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

További példák: [eszközök – lista](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

Az inaktív eszközök védelmére, titkosítani kell az eszközök által a storage ügyféloldali titkosítása. Az alábbi táblázat a storage ügyféloldali titkosítása működését mutatja a Media Services:

|Titkosítási beállítás|Leírás|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services, tárolás titkosítása|AES-256 titkosítással, kulcsfontosságú a Media Services által felügyelt|Támogatott<sup>(1)</sup>|Nem támogatott<sup>(2)</sup>|
|[A Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|A kiszolgálóoldali titkosítást az Azure Storage által kínált kulcs felügyelt ügyfél vagy Azure által|Támogatott|Támogatott|
|[Storage ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage, a Key Vault az ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|Nem támogatott|

<sup>1</sup> közben a Media Services támogatja a tartalom kezelésére, a titkosítatlan/bármilyen titkosítás nélkül, ez nem ajánlott.

<sup>2</sup> a Media Services v3, tárolás titkosítása (AES-256 titkosítás) van csak támogatott a visszamenőleges kompatibilitás a Media Services v2 létrehozásakor a rendszer az eszközöket. Tehát v3 együttműködik a meglévő tárhely eszközök titkosított, de nem teszi meg újakat.

## <a name="next-steps"></a>További lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)
