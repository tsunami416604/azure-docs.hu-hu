---
title: Szűrése, rendezése, lapozófájl-Media Services entitások – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a szűrése, rendezése, az Azure Media Services entitások lapozást.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 28c880e8709074d808a41d9920361eaa2b20ecc4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496573"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>A Media Services entitások szűrési, rendezési, stránkování

A Media Services Media Services v3 entitások támogatja a következő OData-lekérdezés beállításai: 

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

A dátum/idő típusú entitások tulajdonságainak mindig UTC formátumban vannak.

## <a name="page-results"></a>Eredmények lap

A lekérdezési válasz számos elemet tartalmaz, ha a szolgáltatás visszaadja egy "\@odata.nextLink" tulajdonságát a következő lapra az eredmények lekérése. Ez használható a lapozza végig a teljes eredményhalmaz. Az oldal méretét nem lehet konfigurálni. Az oldalméret, entitás típusa szerint változó olvassa el a részleteket az egyes szakaszokban.

Entitások létrehozása vagy a gyűjtemény átlapozva közben, ha a módosítások megjelennek a kapott találatok közül (ha ezek a módosítások a gyűjteményt, amely még nincs letöltve részében). 

> [!TIP]
> A gyűjtemény enumerálása, és nem függ egy adott oldal méretét a következő hivatkozás mindig használjon.

## <a name="assets"></a>Objektumok

### <a name="filteringordering"></a>Szűrés és rendezés

Az alábbi táblázat bemutatja, hogyan a szűrés és rendezés beállítások alkalmazhatók a [eszköz](https://docs.microsoft.com/rest/api/media/assets) tulajdonságai: 

|Name (Név)|Szűrés|Rendelés|
|---|---|---|
|id|||
|név|eq, gt, lt| Növekvő vagy csökkenő sorrendben|
|properties.alternateId |EQ||
|properties.assetId |EQ||
|Properties.Container |||
|Properties.created| eq, gt, lt| Növekvő vagy csökkenő sorrendben|
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

## <a name="content-key-policies"></a>Tartalomkulcsszabályok

### <a name="filteringordering"></a>Szűrés és rendezés

Az alábbi táblázat mutatja, hogy ezek a beállítások alkalmazhatók a [Tartalomszabályzat kulcs](https://docs.microsoft.com/rest/api/media/contentkeypolicies) tulajdonságai: 

|Name (Név)|Szűrés|Rendelés|
|---|---|---|
|id|||
|név|Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|Properties.created |Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.description |Eq, ne, a ge, le, gt, lt||
|properties.lastModified|Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|Properties.Options |||
|properties.policyId|eq, ne||
|type|||

### <a name="pagination"></a>Tördelés

Tördelés a négy engedélyezve van a rendezési sorrend mindegyike támogatott. Az oldalméret jelenleg 10.

A következő C# példa bemutatja, hogyan végig az összes számbavétele **Tartalomszabályzat kulcs** a fiókban.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

További példák: [tartalom kulcs házirendjei – lista](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="jobs"></a>Feladatok

### <a name="filteringordering"></a>Szűrés és rendezés

Az alábbi táblázat mutatja, hogy ezek a beállítások alkalmazhatók a [feladatok](https://docs.microsoft.com/rest/api/media/jobs) tulajdonságai: 

| Name (Név)    | Szűrés                        | Rendelés |
|---------|-------------------------------|-------|
| név                    | EQ            | Növekvő vagy csökkenő sorrendben|
| Properties.state        | eq, ne        |                         |
| Properties.created      | gt, lt, -le, a ge| Növekvő vagy csökkenő sorrendben|
| properties.lastModified | gt, lt, -le, a ge | Növekvő vagy csökkenő sorrendben| 

### <a name="pagination"></a>Tördelés

A Media Services v3 feladatok tördelés használata támogatott.

A következő C# példa bemutatja a feladatok a fiókban lévő számba.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

További példák: [feladatok – lista](https://docs.microsoft.com/rest/api/media/jobs/list)

## <a name="streaming-locators"></a>Streamelési lokátor

### <a name="filteringordering"></a>Szűrés és rendezés

Az alábbi táblázat bemutatja, hogyan lehet alkalmazni ezeket a beállításokat a StreamingLocator tulajdonságai: 

|Name (Név)|Szűrés|Rendelés|
|---|---|---|
|id |||
|név|Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|Properties.created |Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>Tördelés

Tördelés a négy engedélyezve van a rendezési sorrend mindegyike támogatott. Az oldalméret jelenleg 10.

Az alábbi C#-példa bemutatja a fiókban lévő összes StreamingLocators számba.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

További példák: [Streamelési Lokátorok - lista](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

## <a name="streaming-policies"></a>Streamelési szabályok

### <a name="filteringordering"></a>Szűrés és rendezés

Az alábbi táblázat bemutatja, hogyan lehet alkalmazni ezeket a beállításokat a StreamingPolicy tulajdonságai: 

|Name (Név)|Szűrés|Rendelés|
|---|---|---|
|id|||
|név|Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|Properties.created |Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>Tördelés

Tördelés a négy engedélyezve van a rendezési sorrend mindegyike támogatott. Az oldalméret jelenleg 10.

Az alábbi C#-példa bemutatja a fiókban lévő összes StreamingPolicies számba.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

További példák: [Streamelési házirendek – lista](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)

## <a name="transform"></a>Átalakítás

### <a name="filteringordering"></a>Szűrés és rendezés

Az alábbi táblázat mutatja, hogy ezek a beállítások alkalmazhatók a [alakítja át az](https://docs.microsoft.com/rest/api/media/transforms) tulajdonságai: 

| Name (Név)    | Szűrés                        | Rendelés |
|---------|-------------------------------|-------|
| név                    | EQ            | Növekvő vagy csökkenő sorrendben|
| Properties.created      | gt, lt, -le, a ge| Növekvő vagy csökkenő sorrendben|
| properties.lastModified | gt, lt, -le, a ge | Növekvő vagy csökkenő sorrendben|

## <a name="next-steps"></a>További lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)
