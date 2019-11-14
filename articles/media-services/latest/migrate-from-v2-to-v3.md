---
title: Áttelepítés Azure Media Services v2-ről v3-re | Microsoft Docs
description: Ez a cikk a Azure Media Services v3 verzióban bevezetett változásokat ismerteti, és két verzió közötti különbségeket mutatja. A cikk áttelepítési útmutatót is biztosít a Media Services v2-ről a v3-re való áthelyezéshez.
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
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: fb36387764efbdaa1ad3d164ba419bee49770871
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049001"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez

Ez a cikk a Azure Media Services v3 verzióban bevezetett változásokat ismerteti, és megjeleníti a két verzió közötti különbségeket, és áttelepítési útmutatót biztosít.

Ha az [örökölt Media Services v2 API](../previous/media-services-overview.md)-kra épülő videó-szolgáltatást már ma fejlesztette ki, tekintse át a következő irányelveket és szempontokat a V3 API-kra való Migrálás előtt. A V3 API számos előnnyel és új funkcióval rendelkezik, amelyek javítják a Media Services fejlesztői élményét és képességeit. Azonban a jelen cikk [ismert problémák](#known-issues) című szakaszában leírtak szerint az API-verziók változásai miatt bizonyos korlátozások is fennállnak. Ez az oldal továbbra is érvényben marad, mivel a Media Services csapat tovább fejleszti a V3 API-kat, és megjavítja a verziók közötti hézagokat. 

> [!NOTE]
> Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

## <a name="benefits-of-media-services-v3"></a>A Media Services v3 előnyei
  
### <a name="api-is-more-approachable"></a>Az API jobban megközelíthető

*  A v3 egy egységes API felületen alapul, amely az Azure Resource Manager szolgáltatásra épülő felügyeleti és műveleti funkciókat is biztosít. Azure Resource Manager sablonok használatával átalakításokat, folyamatos átviteli végpontokat, élő eseményeket és egyéb műveleteket hozhat létre és helyezhet üzembe.
* [OpenAPI-specifikáció (korábbi nevén henceg)](https://aka.ms/ams-v3-rest-sdk) dokumentum.
    Minden szolgáltatás-összetevő számára elérhetővé teszi a sémát, beleértve a fájl alapú kódolást is.
* A [.net](https://aka.ms/ams-v3-dotnet-ref), a .net Core, a [Node. js](/javascript/api/overview/azure/mediaservices/management), a [Python](https://aka.ms/ams-v3-python-ref), a [Java](https://aka.ms/ams-v3-java-ref), a [Go](https://aka.ms/ams-v3-go-ref)és a Ruby esetében elérhető SDK-k.
* [Azure CLI](https://aka.ms/ams-v3-cli-ref) -integráció az egyszerű parancsfájlok támogatásához.

### <a name="new-features"></a>ÚJ funkciók

* A fájl alapú feladatok feldolgozásához a HTTP (S) URL-címet használhatja bemenetként.<br/>Nem kell az Azure-ban tárolt tartalmakat használnia, és nem kell eszközöket létrehoznia.
* Bevezeti a fájl alapú feladatok feldolgozásának [átalakítási](transforms-jobs-concept.md) koncepcióját. Az átalakító használatával újrafelhasználható konfigurációk hozhatók létre, Azure Resource Manager sablonok hozhatók létre, és a feldolgozási beállítások elkülöníthetők több ügyfél vagy bérlő között.
* Egy eszközhöz több [folyamatos átviteli lokátor](streaming-locators-concept.md) is tartozhat, amelyek különböző [dinamikus csomagolási](dynamic-packaging-overview.md) és dinamikus titkosítási beállításokkal rendelkeznek.
* A [Content Protection](content-key-policy-concept.md) támogatja a többkulcsos funkciókat.
* Akár 24 órán át tartó élő eseményeket is továbbíthat, ha Media Services használ egy sávszélesség-összefoglalást egy olyan kimeneti adatfolyamba, amely több bitrátát tartalmaz.
* Új, kis késleltetésű élő adatfolyam-támogatás élő eseményeken. További információ: [késés](live-event-latency.md).
* Az élő esemény előzetes verziója támogatja a [dinamikus csomagolást](dynamic-packaging-overview.md) és a dinamikus titkosítást. Ez az előzetes verzióban, valamint a DASH és a HLS csomagoláson is lehetővé teszi a tartalomvédelem használatát.
* Az élő kimenet használata egyszerűbb, mint a program entitás a v2 API-kon. 
* Továbbfejlesztett RTMP-támogatás (nagyobb stabilitás és több forrás-kódoló támogatása).
* A RTMP biztonságos betöltést biztosít.<br/>Élő esemény létrehozásakor 4 betöltési URL-címet kap. A 4 betöltési URL-cím majdnem azonos, ugyanazzal a folyamatos átviteli jogkivonattal (AppId) rendelkezik, csak a portszám rész különbözik. A két URL-cím elsődleges és biztonsági másolat az RTMP-hez.   
* Szerepköralapú hozzáférés-vezérléssel (RBAC) rendelkezik az entitások felett. 

## <a name="changes-from-v2"></a>Változások a v2-ből

* A v3-vel létrehozott eszközök esetében a Media Services csak az [Azure Storage kiszolgálóoldali tároló-titkosítását](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)támogatja.
    * A V3 API-kat a Media Services által biztosított Storage encryption (AES 256) [tárolóval](../previous/media-services-rest-storage-encryption.md) rendelkező v2 API-kkal rendelkező eszközökhöz lehet használni.
    * V3 API-kkal nem hozhatók létre új adategységek a régi AES 256 [Storage encryption](../previous/media-services-rest-storage-encryption.md) használatával.
* Az [eszköznek](assets-concept.md)a v3-as tulajdonságai eltérnek a v2-től, lásd: [a tulajdonságok leképezése](assets-concept.md#map-v3-asset-properties-to-v2).
* A v3 SDK-k mostantól le vannak választva a Storage SDK-ból, így nagyobb mértékben szabályozható a használni kívánt Storage SDK verziója, és elkerülheti a verziószámozási problémákat. 
* A V3 API-kon a kódolási átviteli sebességek másodpercenkénti száma. Ez eltér a v2 Media Encoder Standard előkészlettől. Például a v2-es bitrátát 128 (kbps) értékűre kell megadni, a v3-as verzióban azonban 128000 (BITS/másodperc). 
* A AssetFiles, a AccessPolicies és a IngestManifests entitások nem léteznek a v3-as verzióban.
* A IAsset. ParentAssets tulajdonság nem létezik a v3-as verzióban.
* A Tartalomkulcsok már nem entitás, most a streaming lokátor tulajdonsága.
* Event Grid támogatás helyettesíti a NotificationEndpoints.
* A következő entitások lettek átnevezve
    * A feladat kimenete lecseréli a feladatot, és most egy feladat része.
    * A folyamatos átviteli lokátor helyettesíti a lokátort.
    * Az élő esemény lecseréli a csatornát.<br/>Az élő események számlázása az élő csatorna mérőszámán alapul. További információt a [számlázással](live-event-states-billing.md) és a [díjszabással](https://azure.microsoft.com/pricing/details/media-services/)foglalkozó témakörben talál.
    * Az élő kimenet lecseréli a programot.
* Az élő kimenetek a létrehozás után kezdődnek, és a törléskor leállnak. A programok a v2 API-kkal eltérően működnek, amelyeket a létrehozás után kell elindítani.
* A feladattal kapcsolatos információk lekéréséhez ismernie kell az átalakítás nevét, amely alatt a feladatot létrehozták. 
* A v2-ben az XML [bemeneti](../previous/media-services-input-metadata-schema.md) és [kimeneti](../previous/media-services-output-metadata-schema.md) metaadat-fájljai a kódolási feladatok eredményeképpen jönnek létre. A v3-as verzióban a metaadatok formátuma XML-ből JSON-ra változott. 

> [!NOTE]
> Tekintse át [Media Services v3 erőforrásokra](media-services-apis-overview.md#naming-conventions)alkalmazott elnevezési konvenciókat. Tekintse át a [Blobok elnevezését](assets-concept.md#naming-blobs)is.

## <a name="feature-gaps-with-respect-to-v2-apis"></a>Hiányosságok a v2 API-k tekintetében

A V3 API a v2 API-val kapcsolatos következő szolgáltatásbeli réseket tartalmaz. A hiányosságok lezárása folyamatban van.

* A [prémium szintű kódoló](../previous/media-services-premium-workflow-encoder-formats.md) és az örökölt [Media Analytics-processzorok](../previous/media-services-analytics-overview.md) (Azure Media Services indexelő 2 előzetes, Face redactor stb.) nem érhetők el a v3-n keresztül.<br/>Azok az ügyfelek, akik a Media Indexer 1 vagy 2 előzetes verzióról kívánnak áttérni, azonnal használhatják a AudioAnalyzer-készletet a V3 API-ban.  Ez az új beállításkészlet több funkciót tartalmaz, mint a régebbi Media Indexer 1 vagy 2. 
* A v2 API-k [Media Encoder standard számos speciális funkciója](../previous/media-services-advanced-encoding-with-mes.md) jelenleg nem érhető el a v3 verzióban, például:
  
    * Eszközök összefűzése
    * Átfedések
    * Körbevágási
    * Miniatűr sprite
    * Csendes hangsávok beszúrása, ha a bemenet nem rendelkezik hanggal
    * Videós műsorszám beszúrása, ha a bemenet nem tartalmaz videót
* Az átkódolást használó élő események jelenleg nem támogatják az adatfolyam-beszúrási közép-és az ad-jelölő beszúrását API-hívás használatával. 

> [!NOTE]
> Kérjük, könyvjelzőként ezt a cikket, és ellenőrizze a frissítéseket.
 
## <a name="code-differences"></a>Kód eltérései

Az alábbi táblázat a v2 és v3 kód közötti különbségeket mutatja be a gyakori forgatókönyvek esetében.

|Forgatókönyv|V2 API|V3 API|
|---|---|---|
|Eszköz létrehozása és fájl feltöltése |[v2 .NET-példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET – példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Feladatok elküldése|[v2 .NET-példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET – példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Bemutatja, hogyan hozhat létre először egy átalakítót, majd küldhet el egy feladatot.|
|Eszköz közzététele AES-titkosítással |1. ContentKeyAuthorizationPolicyOption létrehozása<br/>2. ContentKeyAuthorizationPolicy létrehozása<br/>3. AssetDeliveryPolicy létrehozása<br/>4. eszköz létrehozása és tartalom feltöltése vagy feladatok elküldése és kimeneti eszköz használata<br/>5. AssetDeliveryPolicy hozzárendelése az objektumhoz<br/>6. ContentKey létrehozása<br/>7. ContentKey csatolása az eszközhöz<br/>8. AccessPolicy létrehozása<br/>9. lokátor létrehozása<br/><br/>[v2 .NET-példa](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. tartalom-kulcs házirend létrehozása<br/>2. eszköz létrehozása<br/>3. tartalom feltöltése vagy eszköz használata JobOutput<br/>4. adatfolyam-kereső létrehozása<br/><br/>[v3 .NET – példa](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Feladat részleteinek beolvasása és feladatok kezelése |[Feladatok kezelése a v2-sel](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[Feladatok kezelése a v3-vel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Ismert problémák

* Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [Rest APIt](https://aka.ms/ams-v3-rest-sdk), a CLI-t vagy az egyik támogatott SDK-t.
* A Media szolgáltatás számára fenntartott egységeket (MRUs-ket) a fiókjában kell kiépíteni a feladatok párhuzamosságának és teljesítményének szabályozása érdekében, különösen a videó-vagy hangelemzést is beleértve. További információért lásd a [médiafeldolgozás skálázását](../previous/media-services-scale-media-processing-overview.md) ismertető cikket. A MRUs a parancssori felület 2,0-es verziójával kezelheti [Media Services v3](media-reserved-units-cli-how-to.md)esetén, a [Azure Portal](../previous/media-services-portal-scale-media-processing.md)vagy a [v2 API](../previous/media-services-dotnet-encoding-units.md)-k használatával. MRUs kell kiépíteni, függetlenül attól, hogy Media Services v2 vagy V3 API-t használ.
* Media Services a V3 API-val létrehozott entitásokat nem lehet a v2 API-val felügyelni.  
* A v2 API-kkal létrehozott entitásokat a V3 API-kon keresztül nem ajánlott felügyelni. Az alábbi példák olyan különbségekre mutatnak, amelyek két verzióban nem kompatibilisek az entitásokkal:   
    * A v2-ben létrehozott feladatok és feladatok nem jelennek meg a v3-as verzióban, mivel nincsenek átalakítók társítva. A javaslat a v3 átalakításokra és feladatokra vált. Viszonylag rövid időszakra van szükség, amely a fedélzeti v2-feladatok figyelését teszi szükségessé az átállás során.
    * A v2-vel létrehozott csatornák és programok (amelyek az élő eseményekhez és a v3-as élő kimenetekhez vannak leképezve) nem folytathatják a v3-vel való felügyeletet. Javasoljuk, hogy térjen át a v3 élő eseményekre, és az élő kimeneteket egy kényelmes csatornán állítsa le.<br/>Jelenleg nem telepíthet át folyamatosan futó csatornákat.  

> [!NOTE]
> Ez az oldal továbbra is érvényben marad, mivel a Media Services csapat tovább fejleszti a V3 API-kat, és megjavítja a verziók közötti hézagokat.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

A [fájlok streamelésével](stream-files-dotnet-quickstart.md) foglalkozó témakörben tekintheti meg, hogy milyen egyszerűen kezdheti meg a videofájlok kódolását és streamelését. 

