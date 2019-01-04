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
ms.date: 01/01/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 8507d51f0d4d49d89fc24b38ed73df7488261daa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969575"
---
# <a name="assets"></a>Objektumok

Az Azure Media Services- [eszköz](https://docs.microsoft.com/rest/api/media/assets) mindezen fájlok metaadatait és a digitális fájlok (beleértve a videót, hangot, képeket, miniatűröket, szövegsávok és feliratfájlok) tartalmaz. Miután a digitális fájlok feltöltése egy adategységbe, azok a Media Services encoding, streaming, tartalom munkafolyamatok elemzése használható. További információkért lásd: a [digitális fájlok feltöltése eszközök](#upload-digital-files-into-assets) szakaszt.

Egy eszköz található blob-tárolóra lesz leképezve a [Azure Storage-fiók](storage-account-concept.md) és az adategységben található fájlokat a tárolóban blokkblobként vannak tárolva. A Media Services támogatja a Blob-rétegek, ha a fiók használ az általános célú v2 (GPv2) storage. A GPv2, helyezze át a fájlok [ritka elérésű vagy archív tárolási](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archív** storage alacsony költségű forrásfájlokat, ha már nincs szükség (például, miután azok kódolású) ideális választás.

A **archív** tárolási réteg csak ajánlott nagyon nagy méretű forrásfájlokat, amely már kódolva, és a kódolási feladat kimenetének okot egy kimeneti blob-tárolóban. A blobok a kimeneti tárolóhoz, hogy egy eszköz és -felhasználási streamelésére, vagy a tartalomelemzés társítani kívánt léteznie kell egy **interaktív** vagy **ritkán használt adatok** tárolási szinten.

## <a name="asset-definition"></a>Az eszközintelligencia-definíció

Az alábbi táblázat az objektum tulajdonságait jeleníti meg, és lehetővé teszi a definíciójukat.

|Name (Név)|Leírás|
|---|---|
|id|Az erőforrás teljes erőforrás-azonosítója.|
|név|Az erőforrás neve.|
|properties.alternateId |Az eszköz a másik azonosító.|
|properties.assetId |Az eszköz azonosítója.|
|Properties.Container |Az eszközintelligencia blobtároló neve.|
|Properties.created |A létrehozást, az eszköz.<br/> Dátum és idő, mindig UTC formátumban.|
|properties.description|Az eszköz leírása.|
|properties.lastModified |Az utolsó módosítás dátuma az eszköz. <br/> Dátum és idő, mindig UTC formátumban.|
|properties.storageAccountName |A tárfiók neve.|
|properties.storageEncryptionFormat |Az eszköz titkosításának formátuma. Egyik sem, vagy MediaStorageEncryption.|
|type|Az erőforrás típusát.|

Egy teljes meghatározása: [eszközök](https://docs.microsoft.com/rest/api/media/assets).

## <a name="upload-digital-files-into-assets"></a>Eszközök digitális fájlok feltöltése

A gyakori munkafolyamatokat a Media Services egyik feltöltése, kódolása és streamelése egy fájlt. Ez a szakasz az általános lépéseket ismerteti.

1. A Media Services v3 API használatával hozzon létre egy új "bemeneti" objektumot. Ez a művelet létrehoz egy tárolót a Media Services-fiókjához társított storage-fiókban. Az API-t adja vissza a tároló nevét (például `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Ha már rendelkezik egy adategység társítani kívánt blob-tárolóba, megadhatja a tároló neve, az eszköz létrehozásakor. Media Services jelenleg csak támogatja a blobok a tároló legfelső szintű és nem a fájlnévben szereplő elérési utakat. Így egy tároló, a "input.mp4" fájl nevével fog működni. Azonban az "videos/inputs/input.mp4" nevű tároló nem fog működni.

    Az Azure CLI segítségével feltöltés közvetlenül a tárfiók és tároló, amely megfelelő jogosultságokkal rendelkezik az előfizetésében. <br/>A tároló neve egyedi legyen, és hajtsa végre a tároló elnevezési irányelveinek. A név nem kell hajtsa végre a formázás Media Services eszköz Tárolónév (objektum-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Egy SAS URL-cím olvasási és írási engedélyekkel, hogy a rendszer a digitális fájlok feltöltése az Eszközintelligencia-tároló lekéréséhez. A Media Services API-t is használhatja [eszköz tároló URL-címek listája](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Az Azure Storage API-k és SDK-kkal (például a [Storage REST API-val](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), vagy [.NET SDK-val](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) fájlok feltöltése az Eszközintelligencia-tárolóba. 
4. A Media Services v3 API-k használatával hozzon létre egy-egy átalakítási és a egy feladatot a "bemeneti" Eszközintelligencia feldolgozásához. További információkért lásd: [átalakítások és feladatok](transform-concept.md).
5. A Stream a tartalom a "kimeneti" objektum.

> [!TIP]
> A teljes .NET-példa bemutatja, hogyan: hozható létre az eszköz, írható SAS URL-cím lekérése a storage-tárolóba az eszköz, a fájl feltöltése a tárolóba, a storage, az SAS URL-cím használatával, lásd: [hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Új eszköz létrehozása

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

A REST-példa: a [hozzon létre egy objektumot a REST segítségével](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) példa.

A példa bemutatja, hogyan hozhat létre a **kérelem törzse** , ahol megadható hasznos információkat, például leírása, a tároló neve, a storage-fiók és az egyéb információkat.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Egy teljes példa: [hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md). A Media Services v3-as, a feladat bemenetének is létrehozhatók a HTTPS URL (lásd: [hozzon létre egy feladat bemenete HTTPS URL-címet](job-input-from-http-how-to.md)).

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
|név|A következőket támogatja: Eq, Gt, Lt|A következőket támogatja: Növekvő vagy csökkenő sorrendben|
|properties.alternateId |A következőket támogatja: EQ||
|properties.assetId |A következőket támogatja: EQ||
|Properties.Container |||
|Properties.created|A következőket támogatja: Eq, Gt, Lt| A következőket támogatja: Növekvő vagy csökkenő sorrendben|
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

Eszközök létrehozott vagy a gyűjtemény átlapozva közben, ha a módosítások megjelennek a kapott találatok közül (ha ezek a módosítások a gyűjteményt, amely még nincs letöltve részében). 

#### <a name="c-example"></a>C#-példa

Az alábbi C#-példa bemutatja a fiókban lévő összes eszköz számba.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Példa REST

Tekintse meg a következő példát, amelyben a rendszer használja-e a $skiptoken. Győződjön meg arról, hogy cserélje le *amstestaccount* a fiók nevét és a set-a *api-version* érték a legújabb verzióra.

Ha ehhez hasonló eszközök listájának lekérdezéséhez:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

A jogosultság hiányát vissza választ ehhez hasonló:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

A következő oldalon egy get kérés küldése lenne lekérhetik:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

REST kapcsolatos további példákért lásd [eszközök – lista](https://docs.microsoft.com/rest/api/media/assets/list)

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

[Különbségeit a Media Services v2 és v3](migrate-from-v2-to-v3.md)
