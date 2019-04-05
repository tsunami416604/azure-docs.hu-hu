---
title: Telepítse át az Azure Media Services v3 a v2 |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Media Services v3 bevezetett módosítások és láthatók a két verziója közötti különbségek. A cikk a migrálási útmutató segítséget nyújt a Media Services v2 v3 áthelyezését is tartalmaz.
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
ms.date: 03/27/2019
ms.author: juliako
ms.openlocfilehash: 70e28377b19b682f2191e0a8fb95792101fa8ec7
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045677"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Migrálási útmutató segítséget nyújt a Media Services v2 áthelyezését v3

Ez a cikk ismerteti az Azure Media Services v3 bevezetett módosítások, két verziója közötti különbségek, és megjeleníti a migrálási útmutatást.

Ha rendelkezik egy fejlett még ma a videószolgáltatás a [örökölt Media Services v2 API-k](../previous/media-services-overview.md), az alábbi irányelvek és megfontolandó szempontok a v3 API-k áttelepítése előtt tekintse át. Nincsenek számos előnyei és funkciói a v3 API fejlesztői élményt és Media Services funkcióit. Azonban, mint a hívott a [ismert problémák](#known-issues) szakasz ebben a cikkben, akkor korlátozásokba is miatt az API-verziók között változik. Ezen a lapon, a Media Services-csapat folyamatos fejlesztéseket hajt végre a v3 API-k és a verziók között a hiányosságait kell fenntartani. 

> [!NOTE]
> Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](developers-guide.md).

## <a name="benefits-of-media-services-v3"></a>Előnyei a Media Services v3
  
### <a name="api-is-more-approachable"></a>API több programozása

*  A v3 egy egységes API felületen alapul, amely az Azure Resource Manager szolgáltatásra épülő felügyeleti és műveleti funkciókat is biztosít. Az Azure Resource Manager-sablonok létrehozása és üzembe helyezése átalakítások, a Streamelési végpontok, az élő események és egyéb használható.
* [Nyissa meg az API-t (más néven Swagger-) specifikáció](https://aka.ms/ams-v3-rest-sdk) dokumentumot.
    Az összes szolgáltatás összetevőivel, például a fájl alapú kódolás sémáját mutatja.
* SDK-k [.NET](https://aka.ms/ams-v3-dotnet-ref), .NET Core, [Node.js](https://aka.ms/ams-v3-nodejs-ref), [Python](https://aka.ms/ams-v3-python-ref), [Java](https://aka.ms/ams-v3-java-ref), [Go](https://aka.ms/ams-v3-go-ref), és a Ruby.
* [Az Azure CLI](https://aka.ms/ams-v3-cli-ref) egyszerű parancsfájl-kezelési támogatás-integráció.

### <a name="new-features"></a>Új funkciók

* Fájlalapú feladat feldolgozása egy HTTP (S) URL-címet a bemenetként is használhatja.<br/>Szeretné, hogy már az Azure-ban tárolt tartalmat nem kell, és nem kell objektumok létrehozásához.
* Bemutatja a [alakítja át az](transforms-jobs-concept.md) fájlalapú feladat feldolgozása. Egy-egy átalakítási segítségével hozhat létre újrahasznosítható konfigurációk esetén az Azure Resource Manager-sablonok létrehozását és feldolgozási beállítások között több, ügyfelek vagy bérlők elkülönítésére.
* Egy eszköz rendelkezhet több [Streamelési Lokátorok](streaming-locators-concept.md) rendelkező különböző [dinamikus csomagolási](dynamic-packaging-overview.md) és a dinamikus titkosítás beállításai.
* [Content protection szolgáltatása](content-key-policy-concept.md) támogatja a többszörös legfontosabb funkcióit.
* Élő események, amelyek akár 24 órát hosszú egy kimeneti adatfolyamba, amely rendelkezik több bitsebességre való átkódolása átkódolása egyféle sávszélességű hozzájárulás a Media Services használatával hírcsatornaelem streamelheti.
* Új alacsony késés élő adatfolyam támogatását a élő események. További információkért lásd: [késés](live-event-latency.md).
* Élő események előzetes támogatja [dinamikus csomagolási](dynamic-packaging-overview.md) és a dinamikus titkosítás. Ez lehetővé teszi a content protection előzetes verzió, valamint a DASH vagy HLS csomagolásra.
* Élő kimenete egyszerűbb, mint a Program entitás az v2 API-k használatához. 
* Továbbfejlesztett RMTP-támogatása (fokozott stabilitás és további forráskód kódoló).
* Biztonságos RTMPS betöltését.<br/>Amikor egy élő eseményt hoz létre, 4 kap betöltési URL-címeket. A 4 betöltési URL-címek olyan majdnem teljesen megegyezik, rendelkezik a azonos streamelési token (alkalmazásazonosító), csak a port száma rész nem egyezik. Az URL-címek kettő elsődleges és tartalék RTMPS számára.   
* Szerepköralapú hozzáférés-vezérlés (RBAC) az entitások felett van. 

## <a name="changes-from-v2"></a>V2-módosítások

* Az eszközök létrehozni a v3-as, a Media Services támogatja a csak a [Azure Storage kiszolgálóoldali tártitkosítás](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).
    * V3 API-k használata a v2 API-kkal, amelyek korábban létrehozott eszközök [tártitkosítás](../previous/media-services-rest-storage-encryption.md) Media Services által kínált (AES-256).
    * Nem hozható létre új eszközök az örökölt AES 256 [tártitkosítás](../previous/media-services-rest-storage-encryption.md) v3 API-k használatával.
* A v3-as SDK-k most vannak választva a a Storage SDK-t biztosít a Storage SDK-t szeretné használni, és elkerülhetők a verziókezelés problémák verzióját jobban szabályozhatja. 
* V3 API-k a kódolási átviteli sebességek összes bit / másodperc. Ez eltér attól a Media Encoder Standard készletek v2. Például az átviteli sebesség a v2-ben ezt kell megadni, mint 128 (kbps), de a v3-as lenne 128000 (bit/másodperc). 
* Entitások AssetFiles AccessPolicies és IngestManifests v3 nem szerepelnek.
* A IAsset.ParentAssets tulajdonság nem létezik a v3-as.
* Tartalomkulcsok már nem egy entitás, most már egy tulajdonságát a Streamelési lokátor.
* Event Grid támogatási NotificationEndpoints váltja fel.
* A következő entitásokat is átnevezése
    * Feladat kimenetének váltja fel a feladat, és a feladat részeként.
    * Lokátort a streamelési lokátor váltja fel.
    * Élő esemény cserél csatorna.<br/>Élő események élő csatorna mérőszámok alapján számoljuk. További információkért lásd: [számlázási](live-event-states-billing.md) és [díjszabás](https://azure.microsoft.com/pricing/details/media-services/).
    * Élő kimeneti Program váltja fel.
* Élő kimenetek nem kell explicit módon kell elindítani, a létrehozás indítása és leállítása, ha törli. Programok dolgoztam eltérően a v2 API-k, kellett arra, hogy létrehozása után indítható.
*  Információt szeretne kapni egy feladatot, akkor ismernie kell a átalakító neve, amelyben a feladat létrehozása. 

## <a name="feature-gaps-with-respect-to-v2-apis"></a>A szolgáltatás hézagok megállapodást v2 API-k

A v3 API rendelkezik a következő szolgáltatás garanciát a v2 API hiányosságok pótlásában. A hiányosságok folyamatban.

* A [prémium szintű kódoló](../previous/media-services-premium-workflow-encoder-formats.md) és az örökölt [elemzési médiafeldolgozók](../previous/media-services-analytics-overview.md) (az Azure Media Services Indexer 2 előzetes verzió, Face Redactor stb.) nem érhetők el a v3-n keresztül.<br/>Ügyfelek, akik a Media Indexer 1 vagy 2 előzetes migrálni szeretne azonnal használható a készletet a v3 API AudioAnalyzer.  Az új beállításkészlet további funkciókat, mint a régebbi Media Indexer 1 vagy 2-es tartalmazza. 
* Számos, a [speciális funkciókat, a Media Encoder Standard a v2-ben](../previous/media-services-advanced-encoding-with-mes.md) API-k jelenleg nem állnak rendelkezésre a v3-as, például:
    * Kivágás (az igény szerinti és élő forgatókönyv)
    * Eszközeinek vágása
    * Átfedések
    * Vágása
    * Miniatűr Sprites
    * Nincs hang Beszúrás, amikor a bevitel csendes hangsáv van
    * Nincs videó Beszúrás, ha a bemeneti videó nyomon rendelkezik.
* Élő események átkódolása jelenleg nem támogatja a lappal beszúrási közepes stream és az ad reklámjelölők beszúrása API-hívás használatával. 

> [!NOTE]
> Lássa el könyvjelzővel ezt a cikket, és tartsa a frissítések keresése.
 
## <a name="code-differences"></a>Kód különbségek

Az alábbi táblázat a v2 és v3 szabhatják kódjának különbségeiről.

|Forgatókönyv|V2 API|V3 API|
|---|---|---|
|Hozzon létre egy objektumot, és a egy fájl feltöltése |[v2 .NET example](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET example](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|Feladat elküldése|[v2 .NET example](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET example](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>Először hozzon létre egy-egy átalakítási és majd a feladat elküldése mutatja.|
|Teszi közzé az objektumot az AES-titkosítás |1. Create ContentKeyAuthorizationPolicyOption<br/>2. Create ContentKeyAuthorizationPolicy<br/>3. Create AssetDeliveryPolicy<br/>4. Eszköz létrehozása és töltse fel a feladat vagy terjeszt tartalmat, és használja a kimeneti adategység<br/>5. Az Eszközintelligencia AssetDeliveryPolicy társítása<br/>6. ContentKey létrehozása<br/>7. Az Eszközintelligencia ContentKey csatolása<br/>8. Create AccessPolicy<br/>9. Kereső létrehozása<br/><br/>[v2 .NET example](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1. Tartalmi kulcs szabályzat létrehozása<br/>2. Eszköz létrehozása<br/>3. Tartalom feltöltése, vagy JobOutput eszköz használata<br/>4. A Streamelési Lokátorok létrehozásához<br/><br/>[v3 .NET example](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|Lekérhet feladatadatokat és feladatok kezelése |[A v2 feladatok kezelése](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[V3-feladatok kezelése](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

## <a name="known-issues"></a>Ismert problémák

* Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-val](https://aka.ms/ams-v3-rest-sdk), CLI-t, vagy valamelyik támogatott SDK-k.
* Kell rendelkezni a Media szolgáltatás számára fenntartott egységek (helyet) a fiókban az egyidejűség és a feladatok, különösen azokról, videó vagy hang Analysis teljesítményének szabályozása érdekében. További információért lásd a [médiafeldolgozás skálázását](../previous/media-services-scale-media-processing-overview.md) ismertető cikket. Helyet használata kezelheti [CLI 2.0-t a Media Services v3](media-reserved-units-cli-how-to.md)révén a [az Azure portal](../previous/media-services-portal-scale-media-processing.md), vagy használja a [v2 API-k](../previous/media-services-dotnet-encoding-units.md). Kiépítés helyet, akár az a Media Services v2 és v3 kell API-k.
* A Media Services entitások API nem tudja kezelni a v2 API v3-as hoztak létre.  
* A v3 API-kon keresztül a v2 API-kkal létrehozott entitások kezelése nem ajánlott. Példák, amelyek az entitásokat a két verziója nem kompatibilis a különbségek a következők:   
    * Feladatok és a v2-ben létrehozott feladatok nem jelennek meg a v3-as, azok nem kapcsolódnak egy-egy átalakítási. A javaslat, hogy váltson át a v3-as átalakítások és feladatok. A viszonylag rövid időtartammal kellene a figyelő a megszakít v2 feladatok a váltás során lesz.
    * Csatornákon és programokon a v2 létrehozott (amelynek vannak leképezve az élő események és élő kimenetek a v3-as) nem tudja folytatni a v3-as felügyelt. A javaslat, hogy egy kényelmes csatorna leállítás v3 élő események és élő kimenetek kapcsolót.<br/>Jelenleg nem tudja áttelepíteni, folyamatosan futó csatornák.  

> [!NOTE]
> Ezen a lapon, a Media Services-csapat folyamatos fejlesztéseket hajt végre a v3 API-k és a verziók között a hiányosságait kell fenntartani.

## <a name="next-steps"></a>További lépések

A [fájlok streamelésével](stream-files-dotnet-quickstart.md) foglalkozó témakörben tekintheti meg, hogy milyen egyszerűen kezdheti meg a videofájlok kódolását és streamelését. 

