---
title: Áttelepítés Azure Media Services v2-ről v3-re
description: Ez a cikk a Azure Media Services v3 verzióban bevezetett változásokat ismerteti, és két verzió közötti különbségeket mutatja.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/01/2020
ms.author: inhenkel
ms.openlocfilehash: 14544f58bcda56a55cef33de8fe0a70d5859b589
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510947"
---
# <a name="media-services-v2-vs-v3"></a>Media Services v2 és v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk a Azure Media Services v3 verzióban bevezetett változásokat ismerteti, és két verzió közötti különbségeket mutatja.

## <a name="general-changes-from-v2"></a>Általános változások a v2-ből

* Az eszközhöz kapcsolódó változásokért tekintse meg az alábbi, az [eszközre vonatkozó módosításokat](#asset-specific-changes) ismertető szakaszt.
* A v3 SDK-k mostantól le vannak választva a Storage SDK-ból, így nagyobb mértékben szabályozható a használni kívánt Storage SDK verziója, és elkerülheti a verziószámozási problémákat. 
* A V3 API-kon a kódolási átviteli sebességek másodpercenkénti száma. Ez eltér a v2 Media Encoder Standard előkészlettől. Például a v2-es bitrátát 128 (kbps) értékűre kell megadni, a v3-as verzióban azonban 128000 (BITS/másodperc). 
* A AssetFiles, a AccessPolicies és a IngestManifests entitások nem léteznek a v3-as verzióban.
* A Tartalomkulcsok már nem entitás, most a streaming lokátor tulajdonsága.
* Event Grid támogatás helyettesíti a NotificationEndpoints.
* A következő entitások lettek átnevezve:

   * a v3 JobOutput lecseréli a v2 feladatot, és már egy feladat része. A bemenetek és kimenetek jelenleg a feladatok szintjén vannak. További információ: [Job input létrehozása helyi fájlból](job-input-from-local-file-how-to.md). 

       A feladatok előrehaladási előzményeinek megtekintéséhez figyelje a EventGrid eseményeket. További információ: [Event Grid események feldolgozása](reacting-to-media-services-events.md).
    * A folyamatos átviteli lokátor helyettesíti a lokátort.
    * Az élő esemény lecseréli a csatornát.<br/>Az élő események számlázása az élő csatorna mérőszámán alapul. További információt a [számlázással](live-event-states-billing.md) és a [díjszabással](https://azure.microsoft.com/pricing/details/media-services/)foglalkozó témakörben talál.
    * Az élő kimenet lecseréli a programot.
* Az élő kimenetek a létrehozás után kezdődnek, és a törléskor leállnak. A programok a v2 API-kkal eltérően működnek, amelyeket a létrehozás után kell elindítani.
* A feladattal kapcsolatos információk lekéréséhez ismernie kell az átalakítás nevét, amely alatt a feladatot létrehozták. 
* A v2-ben az XML [bemeneti](../previous/media-services-input-metadata-schema.md) és [kimeneti](../previous/media-services-output-metadata-schema.md) metaadat-fájljai a kódolási feladatok eredményeképpen jönnek létre. A v3-as verzióban a metaadatok formátuma XML-ből JSON-ra változott. 
* Media Services v2-ben megadható az inicializálási vektor (IV). Media Services v3-as verzióban nem adható meg a FairPlay IV. Noha nem befolyásolja az ügyfeleket a csomagok és a licencek kézbesítésének Media Services használatával, ez akkor lehet probléma, ha egy külső gyártótól származó DRM-rendszer használatával továbbítja a FairPlay-licenceket (hibrid mód). Ebben az esetben fontos tudni, hogy a FairPlay IV a CBCS kulcs-AZONOSÍTÓból származik, és a következő képlet használatával kérhető le:

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

    További információkért tekintse meg a [Azure functions C#-kódot Media Services v3 hibrid módban az élő és a VOD műveletekhez](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3).
 
> [!NOTE]
> Tekintse át [Media Services v3 erőforrásokra](media-services-apis-overview.md#naming-conventions)alkalmazott elnevezési konvenciókat. Tekintse át a [Blobok elnevezését](assets-concept.md#naming)is.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>A v2 API-k hiányzó funkciói

A V3 API a v2 API-val kapcsolatos következő szolgáltatásbeli réseket tartalmaz. A hiányosságok lezárása folyamatban van.

* A [prémium szintű kódoló](../previous/media-services-encode-asset.md) és az örökölt [Media Analytics-processzorok](../previous/legacy-components.md) (Azure Media Services indexelő 2 előzetes, Face redactor stb.) nem érhetők el a v3-n keresztül.<br/>Azok az ügyfelek, akik a Media Indexer 1 vagy 2 előzetes verzióról kívánnak áttérni, azonnal használhatják a AudioAnalyzer-készletet a V3 API-ban.  Ez az új beállításkészlet több funkciót tartalmaz, mint a régebbi Media Indexer 1 vagy 2. 
* A v2 API-k [Media Encoder standard számos speciális funkciója](../previous/media-services-advanced-encoding-with-mes.md) jelenleg nem érhető el a v3 verzióban, például:
  
    * Eszközök összefűzése
    * Átfedések
    * Körbevágási
    * Csendes hangsávok beszúrása, ha a bemenet nem rendelkezik hanggal
    * Videós műsorszám beszúrása, ha a bemenet nem tartalmaz videót
* Az átkódolást használó élő események jelenleg nem támogatják az adatfolyam-beszúrási közép-és az ad-jelölő beszúrását API-hívás használatával. 
* A `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git` V2 REST API on használatával kapcsolatos ajánlott eljárásokért és mintákért tekintse meg a mintakód című témakört. NETCore SDK.

## <a name="asset-specific-changes"></a>Eszköz-specifikus változások

* A v3-vel létrehozott eszközök esetében a Media Services csak az [Azure Storage kiszolgálóoldali tároló-titkosítását](../../storage/common/storage-service-encryption.md)támogatja.
    * A V3 API-kat a Media Services által biztosított Storage encryption (AES 256) [tárolóval](../previous/media-services-rest-storage-encryption.md) rendelkező v2 API-kkal rendelkező eszközökhöz lehet használni.
    * V3 API-kkal nem hozhatók létre új adategységek a régi AES 256 [Storage encryption](../previous/media-services-rest-storage-encryption.md) használatával.
* Az [eszköznek](assets-concept.md)a v3-as tulajdonságai eltérnek a v2-től, lásd: [a tulajdonságok leképezése](#map-v3-asset-properties-to-v2).
* A IAsset. ParentAssets tulajdonság nem létezik a v3-as verzióban.

### <a name="map-v3-asset-properties-to-v2"></a>V3-eszköz tulajdonságainak hozzárendelése v2-re

A következő táblázat azt mutatja be, hogy az adategység tulajdonságai hogyan jelennek meg az objektumnak [a v2](/rest/api/media/assets/createorupdate#asset)-ben lévő tulajdonságok tulajdonságában.

|v3-tulajdonságok|v2 tulajdonságai|
|---|---|
|`id` -(egyedi) a teljes Azure Resource Manager elérési út, lásd: példák az [eszközön](/rest/api/media/assets/createorupdate)||
|`name` – (egyedi) lásd: [elnevezési konvenciók](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` -(egyedi) érték az `nb:cid:UUID:` előtaggal kezdődik.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (létrehozási beállítások)|
|`type`||

### <a name="storage-side-encryption"></a>Tárolási oldal titkosítása

Az adategységek védelméhez az eszközöket a tárolási oldal titkosításával kell titkosítani. A következő táblázat bemutatja, hogyan működik a tárolási oldal titkosítása Media Servicesban:

|Titkosítási beállítás|Leírás|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services Storage-titkosítás|AES-256 titkosítás, Media Services által felügyelt kulcs.|Támogatott<sup>(1)</sup>|Nem támogatott<sup>(2)</sup>|
|[Inaktív adatok Storage Service Encryption](../../storage/common/storage-service-encryption.md)|Az Azure Storage által kínált kiszolgálóoldali titkosítás, amelyet az Azure vagy az ügyfél kezel.|Támogatott|Támogatott|
|[Storage Client-Side titkosítás](../../storage/common/storage-client-side-encryption.md)|Az Azure Storage által kínált ügyféloldali titkosítás, amelyet az ügyfél felügyel Key Vaultban.|Nem támogatott|Nem támogatott|

<sup>1</sup> míg a Media Services támogatja a tartalom tiszta/titkosítás nélküli kezelését, ezért nem ajánlott.

<sup>2</sup> Media Services v3-as verzióban a Storage encryption (AES-256 encryption) csak akkor támogatott a visszamenőleges kompatibilitás érdekében, ha az eszközök Media Services v2-mel lettek létrehozva. A v3 azt jelenti, hogy a meglévő tárolók titkosított eszközeivel működik együtt, de nem engedélyezi újak létrehozását.

## <a name="code-differences"></a>Kód eltérései

Az alábbi táblázat a v2 és v3 kód közötti különbségeket mutatja be a gyakori forgatókönyvek esetében.

|Használati példa|v2 API|V3 API|
|---|---|---|
|Eszköz létrehozása és fájl feltöltése |[v2 .NET-példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET – példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Feladatok elküldése|[v2 .NET-példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET – példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Bemutatja, hogyan hozhat létre először egy átalakítót, majd küldhet el egy feladatot.|
|Eszköz közzététele AES-titkosítással |1. ContentKeyAuthorizationPolicyOption létrehozása<br/>2. ContentKeyAuthorizationPolicy létrehozása<br/>3. AssetDeliveryPolicy létrehozása<br/>4. eszköz létrehozása és tartalom feltöltése vagy feladatok elküldése és kimeneti eszköz használata<br/>5. AssetDeliveryPolicy hozzárendelése az objektumhoz<br/>6. ContentKey létrehozása<br/>7. ContentKey csatolása az eszközhöz<br/>8. AccessPolicy létrehozása<br/>9. lokátor létrehozása<br/><br/>[v2 .NET-példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. tartalom-kulcs házirend létrehozása<br/>2. eszköz létrehozása<br/>3. tartalom feltöltése vagy eszköz használata JobOutput<br/>4. adatfolyam-kereső létrehozása<br/><br/>[v3 .NET – példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Feladat részleteinek beolvasása és feladatok kezelése |[Feladatok kezelése a v2-sel](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Feladatok kezelése a v3-vel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> Kérjük, könyvjelzőként ezt a cikket, és ellenőrizze a frissítéseket.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

[Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez](migrate-from-v2-to-v3.md)