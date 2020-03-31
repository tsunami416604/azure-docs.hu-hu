---
title: Áttelepítés az Azure Media Services v2-ről a v3-ra
description: Ez a cikk az Azure Media Services v3-ban bevezetett módosításait ismerteti, és két verzió közötti különbségeket mutatja be.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: fd094e35ceaa718ec1b258d74106b39744cbd16f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087817"
---
# <a name="media-services-v2-vs-v3"></a>Media Services v2 vs. v3

Ez a cikk az Azure Media Services v3-ban bevezetett módosításait ismerteti, és két verzió közötti különbségeket mutatja be.

## <a name="general-changes-from-v2"></a>Általános változások a v2-ről

* A v3-mal létrehozott eszközök esetében a Media Services csak az [Azure Storage kiszolgálóoldali tárolási titkosítását](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)támogatja.
    * A v3 API-k at a Media Services által biztosított [tárolótitkosítással](../previous/media-services-rest-storage-encryption.md) rendelkező v2 API-kkal (AES 256) létrehozott eszközökkel használhatja.
    * A régi AES 256-os [tárolótitkosítással](../previous/media-services-rest-storage-encryption.md) nem hozhat létre új eszközöket a v3 API-k használatával.
* Az [eszköz](assets-concept.md)tulajdonságai a v3-ban eltérnek a v2-től, lásd, [hogy a tulajdonságok hogyan felelnek meg.](#map-v3-asset-properties-to-v2)
* A v3 SDK-k most levannak választva a storage SDK-ról, amely nagyobb ellenőrzést biztosít a storage SDK használni kívánt verziójának felett, és elkerüli a verziószámozási problémákat. 
* A v3 API-kban az összes kódolási bitsebesség bitper másodpercben van. Ez eltér a v2 Media Encoder Standard készletek. A v2-ben például a bitráta 128 (kbit/s), de a v3-ban 128000 (bit/másodperc) lenne megadva. 
* Az AssetFiles, az AccessPolicies és a IngestManifests nem létezik a 3-as v3-ban.
* Az IAsset.ParentAssets tulajdonság nem létezik a 3-as eszközben.
* ContentKeys már nem entitás, most már a streaming lokátor tulajdonsága.
* Az Event Grid támogatása lecseréli az Értesítésvégpontokat.
* A következő entitások átnevezésre kerültek
    * A Projekt kimenet lecseréli a feladatot, és most már egy feladat része.
    * A streamelési lokátor helyettesíti a lokátort.
    * Az Élő esemény helyettesíti a csatornát.<br/>Az élő események számlázása az Élő csatorna mérőműszerein alapul. További információt a [számlázás](live-event-states-billing.md) és [az árképzés című témakörben talál.](https://azure.microsoft.com/pricing/details/media-services/)
    * Élő kimenet helyettesíti program.
* Az élő kimenetek létrehozásakor kezdődnek, és törléskor leállnak. A programok másképp működtek a v2 API-kban, a létrehozás után kellett elkezdeni őket.
* Egy feladatról való információhoz ismernie kell azt az Átalakítás nevet, amely alatt a feladatot létrehozták. 
* A v2-ben az [XML-bemeneti](../previous/media-services-input-metadata-schema.md) és [kimeneti](../previous/media-services-output-metadata-schema.md) metaadatfájlok kódolási feladat eredményeként jönnek létre. A v3-ban a metaadat-formátum XML-ről JSON-ra változott. 
* A Media Services v2-ben megadható az inicializálási vektor (IV). A Media Services v3-as ében a FairPlay IV nem adható meg. Bár ez nem érinti a Media Services-t sem a csomagolás, mind a licenckézbesítés során használó ügyfeleket, problémát okozhat, ha harmadik féltől származó DRM-rendszert használnak a FairPlay licencek (hibrid mód) kézbesítésére. Ebben az esetben fontos tudni, hogy a FairPlay IV a cbcs kulcsazonosítóból származik, és ezzel a képlettel olvasható be:

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    a

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    További információ: Az [Azure Functions C# kód a Media Services v3 hibrid módban élő és VOD műveletek.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)
 
> [!NOTE]
> Tekintse át a Media Services [v3-erőforrásokra](media-services-apis-overview.md#naming-conventions)alkalmazott elnevezési konvencióit. Tekintse át [az elnevezési blobokat](assets-concept.md#naming)is.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>A v2 API-k hiányzó funkciói

A v3 API a következő szolgáltatás hiányosságok tekintetében a v2 API-t. A hiányosságok megszüntetése folyamatban van.

* A [prémium kódoló](../previous/media-services-premium-workflow-encoder-formats.md) és a régi [médiaelemzési processzorok](../previous/media-services-analytics-overview.md) (Azure Media Services Indexer 2 Preview, Face Redactor, stb) nem érhetők el a v3-on keresztül.<br/>Azok az ügyfelek, akik a Media Indexer 1 vagy 2 előzetes verzióból szeretnének áttérni, azonnal használhatják a v3 API-ban beállított AudioAnalyzer-készletet.  Ez az új készlet több szolgáltatást tartalmaz, mint a régebbi Media Indexer 1 vagy 2. 
* A v2 API-k [media encoder szabványának számos speciális szolgáltatása](../previous/media-services-advanced-encoding-with-mes.md) jelenleg nem érhető el a v3-ban, például:
  
    * Eszközök összefűzése
    * Matricák
    * Vágás
    * Miniatűr sprite-ok
    * Néma hangsáv beszúrása, ha a bemenet nem rendelkezik hanggal
    * Videosáv beszúrása, ha a bemenet nem rendelkezik videóval
* Az átkódolással rendelkező élő események jelenleg nem támogatják a Pala beillesztését az adatfolyam közepén és a hirdetésjelölők API-híváson keresztüli beszúrását. 
 
## <a name="asset-specific-changes"></a>Eszközspecifikus változások

### <a name="map-v3-asset-properties-to-v2"></a>A v3-as eszköz tulajdonságainak leképezése a v2-hez

Az alábbi táblázat [bemutatja,](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)hogy az eszköz tulajdonságai a v3-ban hogyan felelnek meg az eszköz tulajdonságainak a v2-ben.

|v3 tulajdonságok|v2 tulajdonságok|
|---|---|
|`id`- (egyedi) a teljes Azure Resource Manager elérési út, lásd a példákat [Asset](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`- (egyedi) lásd [elnevezési konvenciók](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`- (egyedi) érték `nb:cid:UUID:` az előtaggal kezdődik.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`(létrehozási lehetőségek)|
|`type`||

### <a name="storage-side-encryption"></a>Tárolási oldali titkosítás

Az eszközök inaktív védelme érdekében az eszközöket a tárolási oldali titkosítással kell titkosítani. Az alábbi táblázat bemutatja, hogyan működik a tárolási oldali titkosítás a Media Services szolgáltatásban:

|Titkosítási beállítás|Leírás|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services storage titkosítása|AES-256 titkosítás, a Media Services által kezelt kulcs.|Támogatott<sup>(1)</sup>|Nem támogatott<sup>(2)</sup>|
|[Tárolószolgáltatás titkosítása inaktív adatokhoz](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Az Azure Storage által kínált kiszolgálóoldali titkosítás, az Azure vagy az ügyfél által kezelt kulcs.|Támogatott|Támogatott|
|[Tároló ügyféloldali titkosítása](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage által kínált ügyféloldali titkosítás, amelykulcst az ügyfél a Key Vaultban kezeli.|Nem támogatott|Nem támogatott|

<sup>1</sup> Bár a Media Services támogatja a tartalom tiszta/titkosítás nélküli kezelését, ez nem ajánlott.

<sup>2</sup> A Media Services v3-as rendszerben a tárolótitkosítás (AES-256 titkosítás) csak akkor támogatott visszamenőleges kompatibilitás esetén, ha az eszközök a Media Services v2-vel jöttek létre. Jelentés: v3 működik a meglévő tároló titkosított eszközök, de nem teszi lehetővé az újak létrehozását.

## <a name="code-differences"></a>Kódkülönbségek

Az alábbi táblázat a v2 és a v3 közötti kódkülönbségeket mutatja be a gyakori forgatókönyvek esetében.

|Forgatókönyv|V2 API|V3 API|
|---|---|---|
|Eszköz létrehozása és fájl feltöltése |[v2 .NET példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Feladat beküldése|[v2 .NET példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Bemutatja, hogyan hozhat létre először egy átalakítást, majd küldhet el egy feladatot.|
|AES-titkosítással rendelkező eszköz közzététele |1. Create ContentKeyAuthorizationPolicyOption<br/>2. ContentKeyAuthorizationPolicy létrehozása<br/>3. AssetDeliveryPolicy létrehozása<br/>4. Eszköz létrehozása és tartalom feltöltése vagy küldési feladat és kimeneti eszköz használata<br/>5. AssetDeliveryPolicy társítása az eszközhöz<br/>6. Create ContentKey<br/>7. A ContentKey csatolása az eszközhöz<br/>8. AccessPolicy létrehozása<br/>9. Lokátor létrehozása<br/><br/>[v2 .NET példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Tartalomkulcs-házirend létrehozása<br/>2. Eszköz létrehozása<br/>3. Tartalom feltöltése vagy eszköz használata JobOutput-ként<br/>4. Streaming lokátor létrehozása<br/><br/>[v3 .NET példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Állásrészleteinek beszereznie és feladatok kezelése |[Feladatok kezelése a v2-vel](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Feladatok kezelése a v3-mal](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Kérjük, könyvjelző ezt a cikket, és folyamatosan ellenőrzi a frissítéseket.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

[Áttelepítési útmutató a Media Services 2-es v3-as ról a 3-as vagy a 3-as](migrate-from-v2-to-v3.md)
