---
title: Az Azure Media Services kiadási megjegyzések | Microsoft dokumentumok
description: Ez a cikk a Microsoft Azure Media Services v2-es kiadási megjegyzésekről szól.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: a3893c8d19c89b639e0584f203cbcd1adf7e2dee
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474845"
---
# <a name="azure-media-services-release-notes"></a>Megjegyzések az Azure Media Services-hez

Ezek a kibocsátási megjegyzések az Azure Media Services-hez a korábbi kiadások változásait és az ismert problémákat foglalja össze.

> [!NOTE]
> A Media Services v2 nem fog újabb funkciókkal bővülni. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Szeretnénk hallani ügyfeleinktől, hogy az Önt érintő problémák megoldására összpontosíthassunk. Probléma bejelentéséhez vagy kérdések felkéréséhez küldjön be egy bejegyzést az [Azure Media Services MSDN fórumán.] 

## <a name="known-issues"></a><a id="issues"/>Ismert problémák
### <a name="media-services-general-issues"></a><a id="general_issues"/>A Media Services általános problémái

| Probléma | Leírás |
| --- | --- |
| Számos gyakori HTTP-fejlécek nem biztosított a REST API-ban. |Ha a Media Services-alkalmazásokat a REST API használatával fejleszti, előfordulhat, hogy néhány gyakori HTTP-fejlécmező (beleértve az CLIENT-REQUEST-ID, REQUEST-ID és RETURN-CLIENT-REQUEST-ID) nem támogatott. A fejlécek egy későbbi frissítésben lesznek hozzáadva. |
| A százalékos kódolás nem engedélyezett. |A Media Services a IAssetFile.Name tulajdonság értékét használja a streamelési `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`tartalom URL-címeinek (például) létrehozásakor. Ezért a százalékos kódolás nem engedélyezett. A Name tulajdonság értéke nem tartalmazhatja a következő [százalékkódolást fenntartott karaktereket:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'();:@&=+$/?%#[]". Is, nem lehet csak egy "." a fájlnév kiterjesztését. |
| Az Azure Storage SDK 3.x-es verziójának részét tartalmazó ListBlobs metódus sikertelen. |A Media Services a [2012-02-12-es](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) verzió alapján hoz létre SAS-URL-címeket. Ha a storage SDK-t szeretné használni a blobtárolóban lévő blobok listázásához, használja a [CloudBlobContainer.ListBlobs metódust,](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) amely a Storage SDK 2.x-es verziójának része. |
| A Media Services szabályozási mechanizmusa korlátozza a szolgáltatásra irányuló túlzott kéréseket tartalmazó alkalmazások erőforrás-használatát. Előfordulhat, hogy a szolgáltatás a "Szolgáltatás nem érhető el" 503 HTTP-állapotkódot adja vissza. |További információt a [Media Services hibakódjaiban](media-services-encoding-error-codes.md)található 503-as HTTP-állapotkód leírásában talál. |
| Entitások lekérdezésekénél egyszerre 1000 entitást ad vissza a rendszer, mert a nyilvános REST 2-es verziója 1000 eredményre korlátozza a lekérdezési eredményeket. |Használja a Skip and Take (.NET)/top (REST) (REST) című részt a [.NET példában](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [ebben a REST API-példában leírtak szerint.](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities) |
| Egyes ügyfelek a Smooth Streaming jegyzékben ismétlődő címkeproblémával találkozhatnak. |További információt [ebben a szakaszban](media-services-deliver-content-overview.md#known-issues)talál. |
| A Media Services .NET SDK-objektumai nem szerializálhatók, és ennek következtében nem működnek az Azure Cache for Redis használatával. |Ha megpróbálja szerializálni az SDK AssetCollection objektumot az Azure Cache for Redis hozzáadásához, egy kivétel jelenik meg. |
|A REST API egy hibaüzenettel válaszol: "A szűrő nem érhető el a REST Api ezen verziójával", amikor eszköz- vagy fiókszint-szűrőt próbál leadni.|A szűrő t egy újabb API-verzióval hozták létre vagy módosították, mint amelyet a szűrő lehívásának megkísérléséhez használnak. Ez akkor fordulhat elő, ha két API-verziót használ az ügyfél által használt kód vagy eszközök.  A legjobb megoldás itt az, hogy frissítse a kódot vagy eszközöket az újabb vagy a két API-verzió használatához.|

## <a name="rest-api-version-history"></a><a id="rest_version_history"/>REST API verzióelőzményei
A Media Services REST API verzióelőzményeiről az [Azure Media Services REST API-hivatkozási száma]című témakörben talál további információt.

## <a name="february-2020"></a>2020. február

Egyes elemzési médiaprocesszorok kilesznek vonva. A nyugdíjazási dátumokat lásd az [örökölt összetevők](legacy-components.md) témakörben.

## <a name="september-2019"></a>2019. szeptember

### <a name="deprecation-of-media-processors"></a>Médiaprocesszorok eprecációja

Bejelentjük az *Azure Media Indexer* és az Azure Media *Indexer 2 Preview*evesítését. [Az Azure Media Services videoindexelő](https://docs.microsoft.com/azure/media-services/video-indexer/) jeleváltja ezeket az örökölt médiaprocesszorokat.

A nyugdíjazási dátumokat lásd az [örökölt összetevők](legacy-components.md) témakörben.

Lásd még: [Áttelepítés az Azure Media Indexerről és az Azure Media Indexer 2-ről az Azure Media Services videoindexelőre.](migrate-indexer-v1-v2.md)

## <a name="august-2019"></a>2019. augusztus

### <a name="deprecation-of-media-processors"></a>Médiaprocesszorok eprecációja

A *Windows Azure Media Encoder* (WAME) és az *Azure Media Encoder* (AME) médiaprocesszorok epcét jelentik be. A nyugdíjazási dátumokat lásd az [örökölt összetevők](legacy-components.md) témakörben.

További információt a [WAME áttelepítése a Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) alkalmazásba és [a a a me-k átáttelepítése a Media Encoder Standard alkalmazásba (AME áttelepítése media encoder sztenderdbe) témakörben talál.](https://go.microsoft.com/fwlink/?LinkId=2101335)

## <a name="march-2019"></a>2019. március

Az Azure Media Services Media Hyperlapse Preview szolgáltatása elavult.

## <a name="december-2018"></a>2018. december

Az Azure Media Services Media Hyperlapse Preview funkciója hamarosan megszűnik. 2018. december 19-től a Media Services már nem módosítja vagy javítja a Media Hyperlapse szolgáltatást. 2019. március 29-én nyugdíjba vonul, és már nem érhető el.

## <a name="october-2018"></a>2018. október

### <a name="cmaf-support"></a>CMAF-támogatás

CMAF és "cbcs" titkosítási támogatás a CMAF-ot támogató Apple HLS (iOS 11+) és MPEG-DASH lejátszókhoz.

### <a name="web-vtt-thumbnail-sprites"></a>Web VTT miniatűr sprite-ok

Most már használhatja a Media Services webes VTT miniatűr sprite a v2 API-k használatával. További információ: [Miniatűr sprite létrehozása.](generate-thumbnail-sprite.md)

## <a name="july-2018"></a>2018. július

A legújabb szolgáltatáskiadással a feladat sikertelensssénél a szolgáltatás által visszaadott hibaüzenetek kisebb formázási módosításai vannak, tekintettel arra, hogy hogyan van felosztva két vagy több sorra.

## <a name="may-2018"></a>2018. május 

2018. május 12-től az élő csatornák már nem támogatják az RTP/MPEG-2 átviteli adatfolyam-betöltési protokollt. Az RTP/MPEG-2 protokollról átkell térni az RTMP-re vagy a töredezett MP4 (Smooth Streaming) protokollokra.

## <a name="october-2017-release"></a>2017. októberi kiadás
> [!IMPORTANT] 
> A Media Services elavulttá tesz az Azure Access Control Service hitelesítési kulcsok támogatását. 2018. június 22-én már nem hitelesítheti magát a Media Services háttérkódjával a Hozzáférés-vezérlési szolgáltatás kulcsai használatával. Frissítenie kell a kódot az Azure Active Directory (Azure AD) [használatához Azure AD-alapú hitelesítésenként.](media-services-use-aad-auth-to-access-ams-api.md) Figyelje meg a változással kapcsolatos figyelmeztetéseket az Azure Portalon.

### <a name="updates-for-october-2017"></a>2017. októberi frissítések
#### <a name="sdks"></a>SDK-k
* A .NET SDK frissítve lett az Azure AD-hitelesítés támogatásához. A hozzáférés-vezérlési szolgáltatás hitelesítésének támogatása el lett távolítva a legújabb .NET SDK-ból Nuget.org az Azure AD-re való gyorsabb áttérés elősegítése érdekében. 
* A JAVA SDK frissítve lett az Azure AD-hitelesítés támogatásához. Az Azure AD-hitelesítés támogatása hozzá lett adva a Java SDK-hoz. A Java SDK Media Services szolgáltatással való használatáról az [Azure Media Services java-ügyfél SDK használatának első lépései](media-services-java-how-to-use.md) című témakörben talál további információt.

#### <a name="file-based-encoding"></a>Fájlalapú kódolás
* Most már használhatja a Premium Encoder kódolni a tartalmat a H.265 nagy hatékonyságú video kódolás (HEVC) video codec. Nincs árképzési hatás, ha a H.265-öt választja más kodekek, például a H.264 kodekek közül. A HEVC szabadalmi licencekkel kapcsolatos információkért lásd az [Online szolgáltatásokra vonatkozó feltételeket.](https://azure.microsoft.com/support/legal/)
* A H.265 (HEVC) videokodekgel kódolt forrásvideó, például az iOS11 vagy a GoPro Hero 6 használatával rögzített videó esetén mostantól használhatja a Prémium kódolót vagy a Standard Kódolót a videók kódolásához. A szabadalmi licencekkel kapcsolatos információkért lásd az [Online szolgáltatások feltételei című témakört.](https://azure.microsoft.com/support/legal/)
* A több nyelvű hangsávot tartalmazó tartalom esetében a nyelvi értékeket helyesen kell címkézni a megfelelő fájlformátum-specifikációnak megfelelően (például ISO MP4). Ezután használhatja a standard kódoló kódolni a tartalmat streamelésre. Az eredményül bekövetkező streamelési lokátor felsorolja a rendelkezésre álló hangnyelveket.
* A Standard Encoder mostantól két új, csak hangalapú rendszerkészletet támogat, az "AAC Audio" és az "AAC Good Quality Audio" rendszert. Mindkettő sztereó fejlett hangkódolási (AAC) kimenetet biztosít, 128 Kbps és 192 Kbps átviteli sebességgel.
* A prémium kódoló mostantól támogatja a QuickTime/MOV fájlformátumokat bemenetként. A videokodeknek a [GitHub cikkében felsorolt Apple ProRes típusok](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats)egyikének kell lennie. A hangnak AAC vagy impulzuskód modulációnak (PCM) kell lennie. A Prémium kódoló nem támogatja például a QuickTime/MOV fájlokba csomagolt DVC/DVCPro videót. A Standard Encoder támogatja ezeket a videokodekeket.
* A következő hibajavítások történtek a kódolókban:

    * Most már elküldheti a feladatokat egy bemeneti eszköz használatával. Miután ezek a feladatok befejeződtek, módosíthatja az eszközt (például hozzáadhat, törölhet vagy átnevezhet fájlokat az eszközön belül), és további feladatokat küldhet el.
    * A Standard Encoder által készített JPEG-bélyegképek minősége javul.
    * A Standard Encoder nagyon rövid időtartamú videókban jobban kezeli a bemeneti metaadatokat és a miniatűrök létrehozását.
    * A Standard Encoderben használt H.264 dekóder fejlesztései kiküszöbölnek bizonyos ritka összetevőket. 

#### <a name="media-analytics"></a>Media Analytics
Az Azure Media Redactor általános elérhetősége: Ez a médiafeldolgozó anonimizálást hajt végre a kiválasztott személyek arcainak elmosódásával, és ideális a közbiztonsági és a hírmédia-forgatókönyvekben való használatra. 

Az új processzor áttekintését [ebben a blogbejegyzésben találja.](https://azure.microsoft.com/blog/azure-media-redactor/) A dokumentációról és a beállításokról a [Vörösítés arcok az Azure Media Analytics szolgáltatással](media-services-face-redaction.md)című témakörben olvashat.



## <a name="june-2017-release"></a>2017. júniusi kiadás

A Media Services mostantól támogatja az [Azure AD-alapú hitelesítést.](media-services-use-aad-auth-to-access-ams-api.md)

> [!IMPORTANT]
> A Media Services jelenleg támogatja a hozzáférés-vezérlési szolgáltatás hitelesítési modelljét. 2018. június 1-jén a hozzáférés-vezérlési szolgáltatás engedélyezése elavult. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="march-2017-release"></a>2017. márciusi kiadás

Most már használhatja a Standard Kódolót [a bitráta-létra automatikus létrehozásához,](media-services-autogen-bitrate-ladder-with-mes.md) ha kódolási feladat létrehozásakor megadja az "Adaptive Streaming" előre beállított karakterláncot. A Media Services használatával történő streameléshez videokódoláshoz használja az "Adaptive Streaming" készletet. Az adott forgatókönyvhöz beállított kódolási készlet testreszabásához kezdje [ezekkel a készletekkel.](media-services-mes-presets-overview.md)

Most már használhatja a Media Encoder Standard vagy a Media Encoder Premium [Workflow-t egy fMP4 adattömböket generáló kódolási feladat létrehozásához.](media-services-generate-fmp4-chunks.md) 

## <a name="february-2017-release"></a>2017. februári kiadás

2017. április 1-jétől a fiókban lévő minden 90 napnál régebbi feladatrekord automatikusan törlődik a kapcsolódó feladatrekordokkal együtt. A törlés akkor is megtörténik, ha a rekordok teljes száma nem éri el a maximális kvótát. A feladat-/feladatadatok archiválásához használhatja az Eszközök és a kapcsolódó entitások kezelése a [Media Services .NET SDK szolgáltatással című](media-services-dotnet-manage-entities.md)könyvében leírt kódot.

## <a name="january-2017-release"></a>2017. januári kiadás

A Media Services szolgáltatásban a streamelési végpont olyan adatfolyam-szolgáltatást jelöl, amely további terjesztés céljából közvetlenül képes tartalmat biztosítani egy ügyféllejátszó alkalmazásnak vagy egy tartalomkézbesítési hálózatnak (CDN). A Media Services zökkenőmentes Azure Content Delivery Network integrációt is biztosít. A StreamingEndpoint szolgáltatás kimenő adatfolyama lehet egy élő közvetítés, igény szerinti videó vagy az eszköz fokozatos letöltése a Media Services-fiókban. Minden Media Services-fiók tartalmaz egy alapértelmezett streamelési végpontot. További streamelési végpontok hozhatók létre a fiók alatt. 

A streamelési végpontok két verziója van, az 1.0 és a 2.0. 2017. január 10-től minden újonnan létrehozott Media Services-fiók tartalmazza a 2.0-s verziójú alapértelmezett streamelési végpontot. A fiókhoz hozzáadott további streamelési végpontok szintén a 2.0-s verziójúak. Ez a módosítás nincs hatással a meglévő fiókokra. A meglévő streamelési végpontok 1.0-s verziójúak, és frissíthetők a 2.0-s verzióra. Ezzel a módosítással viselkedési, számlázási és funkciómódosítások történtek. További információk: [Streamvégpontok áttekintése](media-services-streaming-endpoints-overview.md).

A 2.15-ös verziótól kezdve a Media Services a következő tulajdonságokat adta hozzá a streamelési végpont entitáshoz:

* CdnProvider szolgáltató 
* CdnProfil
* FreeTrialEndTime 
* StreamingEndpointVersion 

Ezekről a tulajdonságokról további információt a [StreamingEndpoint című](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)témakörben talál. 

## <a name="december-2016-release"></a>2016. decemberi kiadás

 Most már használhatja a Media Services telemetriai adatok eléréséhez a szolgáltatások. A Media Services aktuális verziójával telemetriai adatokat gyűjthet élő csatornához, a streamelési végponthoz és az archív entitásokhoz. További információt a [Media Services telemetriai adatai ban](media-services-telemetry-overview.md)talál.

## <a name="july-2016-release"></a><a id="july_changes16"/>2016. júliusi kiadás
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>A jegyzékfájl frissítései (*. ISM) kódolási feladatok által generált
Amikor egy kódolási feladatot küld a Media Encoder Standard vagy a Media Encoder Premium, a kódolási feladat létrehoz egy [adatfolyam-jegyzékfájl](media-services-deliver-content-overview.md) (*.ism) a kimeneti eszköz. A legújabb szolgáltatáskiadással a streamelési jegyzékfájl szintaxisa frissült.

> [!NOTE]
> A streamelési jegyzékfájl (.ism) fájl szintaxisa belső használatra van fenntartva. Ez változhat a jövőbeli kiadásokban. Ne módosítsa és ne módosítsa a fájl tartalmát.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Új ügyféljegyzékfájl (*. ISMC) fájl jön létre a kimeneti eszközben, amikor egy kódolási feladat egy vagy több MP4-fájlt
A legújabb szolgáltatáskiadással kezdve, egy vagy több MP4-fájlt generáló kódolási feladat befejezése után a kimeneti eszköz egy streamelési ügyféljegyzékfájlt (*.ismc) is tartalmaz. Az .ismc fájl segít a dinamikus streamelés teljesítményének javításában. 

> [!NOTE]
> Az ügyféljegyzékfájl (.ismc) szintaxisa belső használatra van fenntartva. Ez változhat a jövőbeli kiadásokban. Ne módosítsa és ne módosítsa a fájl tartalmát.
> 
> 

További információ: [ez a blog](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Ismert problémák
Egyes ügyfelek a Smooth Streaming jegyzékben ismétlődő címkeproblémával találkozhatnak. További információt [ebben a szakaszban](media-services-deliver-content-overview.md#known-issues)talál.

## <a name="april-2016-release"></a><a id="apr_changes16"></a>2016. áprilisi kiadás
### <a name="media-analytics"></a>Media Analytics
 A Media Services bevezette a Media Analytics szolgáltatást a hatékony videointelligenciáért. További információt a [Media Services Analytics – áttekintés című témakörben talál.](media-services-analytics-overview.md)

### <a name="apple-fairplay-preview"></a>Apple FairPlay (előzetes verzió)
Most már használhatja a Media Services dinamikusan titkosítja a HTTP Live Streaming (HLS) tartalom apple FairPlay. A Media Services licenckézbesítési szolgáltatásával fairplay-licenceket is kézbesíthet az ügyfeleknek. További információt "Az Azure Media Services használata az Apple FairPlay által védett HLS-tartalmak streameléséhez" című témakörben talál.

## <a name="february-2016-release"></a><a id="feb_changes16"></a>2016. februári kiadás
A Media Services SDK for . Lehetetlen volt újra felhasználni assetdeliverypolicy több, Widevine-nal titkosított eszközök. A hibajavítás részeként a következő tulajdonság került be az SDK-ba: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>2016. januári kiadás
A kódolásra fenntartott egységeket átnevezték, hogy csökkentsék a kódolónevekkel való összetévesztést.

Az alapszintű, standard és prémium kódolási fenntartott egységek átnevezése S1, S2 és S3 fenntartott egységekre lett átnevezve. Az alapszintű kódolási fenntartott egységeket használó ügyfelek ma az S1-et tekintik címkeként az Azure Portalon (és a számlán). A Standard és Premium-ot használó ügyfelek az S2, illetve az S3 címkéket látják. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>2015. decemberi kiadás

### <a name="media-encoder-deprecation-announcement"></a>A Médiakódoló gyengítésének bejelentése

 A Media Encoder a Media Encoder Standard kiadásától számított körülbelül 12 hónapon belül elavult.

### <a name="azure-sdk-for-php"></a>PHP-hoz készült Azure SDK
Az Azure SDK csapata közzétette az [Azure SDK PHP csomag](https://github.com/Azure/azure-sdk-for-php) új kiadását, amely frissítéseket és új funkciókat tartalmaz a Media Services számára. Különösen a Media Services SDK for PHP támogatja a legújabb [tartalomvédelmi](media-services-content-protection-overview.md) funkciókat. Ezek a funkciók dinamikus titkosítása AES és DRM (PlayReady és Widevine) és token korlátozások nélkül. Támogatja a méretezési [kódolási egységeket](media-services-dotnet-encoding-units.md)is.

További információkért lásd:

* A következő [kódminták](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) segítenek a gyors kezdésben:
  * **vodworkflow_aes.php**: Ez a PHP fájl bemutatja, hogyan kell használni AES-128 dinamikus titkosítás és a kulcs szállítási szolgáltatás. Ez az [AES-128 dinamikus titkosítás és a kulcskézbesítési szolgáltatás](media-services-protect-with-aes128.md)ban ismertetett .NET mintán alapul.
  * **vodworkflow_aes.php**: Ez a PHP fájl bemutatja, hogyan kell használni PlayReady dinamikus titkosítás és a licenc kézbesítési szolgáltatás. A [PlayReady és/vagy a Widevine dinamikus közös titkosításában](media-services-protect-with-playready-widevine.md)ismertetett .NET mintán alapul.
  * **scale_encoding_units.php**: Ez a PHP fájl bemutatja, hogyan kell skálázni kódolás fenntartott egységek.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>2015. novemberi kiadás
 A Media Services mostantól a Widevine licenckézbesítési szolgáltatást kínálja a felhőben. További információ: [ez a blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Tekintse meg [ezt az oktatóanyagot](media-services-protect-with-playready-widevine.md) és a [GitHub-tárházat](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)is. 

A Media Services által nyújtott Widevine licenckézbesítési szolgáltatások előzetes verzióban érhető el. További információ: [ez a blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>2015. októberi kiadás
A Media Services jelenleg a következő adatközpontokban él: Brazília Dél- és India Nyugat-, India Déli és India Central. Most már használhatja az Azure Portalon, hogy [hozzon létre Media Service-fiókok](media-services-portal-create-account.md) és különböző feladatokat leírt a [Media Services dokumentációs weblapon.](https://azure.microsoft.com/documentation/services/media-services/) Az élő kódolás nincs engedélyezve ezekben az adatközpontokban. Továbbá nem minden kódolási típusú fenntartott egység érhető el ezekben az adatközpontokban.

* Brazília déli: Csak standard és basic kódolás fenntartott egységek állnak rendelkezésre.
* India Nyugat, India Déli és India Central: Csak alapvető kódolási fenntartott egységek állnak rendelkezésre.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>2015. szeptemberi kiadás
A Media Services mostantól lehetővé teszi a videoigény és az élő közvetítések védelmét a Widevine moduláris DRM technológiával. A widevine-licencek kézbesítéséhez a következő kézbesítési szolgáltatásokkal segíti:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

További információ: [ez a blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Az AssetDeliveryConfiguration Widevine használatára történő beállításához használja a [Media Services .NET SDK-t](https://www.nuget.org/packages/windowsazure.mediaservices/) (a 3.5.1-es vagy egy újabb verziót), vagy a REST API-t. 
* A Media Services támogatja az Apple ProRes-videókat. Most már feltöltheti a QuickTime forrásvideó fájlokat, amelyek Apple ProRes vagy más kodekeket használnak. További információ: [ez a blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Most már használhatja a Media Encoder Standard csinálni subclipping és élő archív kinyerése. További információ: [ez a blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* A következő szűrési frissítések történtek: 
  
  * Most már használhatja az Apple HLS formátumot csak hangszűrővel. Ezzel a frissítéssel eltávolíthatja a csak hangsávot, ha megadja (csak hang=hamis) az URL-címben.
  * Amikor szűrőket ad meg az eszközökhöz, mostantól több (legfeljebb három) szűrőt egyesíthet egyetlen URL-ben.
    
    További információ: [ez a blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* A Media Services mostantól támogatja az I-kereteket a HLS 4-es verziójában. Az I-frame támogatás optimalizálja a gyors előre- és visszatekerési műveleteket. Alapértelmezés szerint minden HLS 4-es verziójú kimenet tartalmazza az I-frame lejátszási listát (EXT-X-I-FRAME-STREAM-INF).
További információ: [ez a blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>2015. augusztusi kiadás
* A Media Services SDK a Java 0.8.0-s verziójához és az új mintákhoz már elérhető. További információkért lásd:
    
* Az Azure Media Player többhangú adatfolyam-támogatással lett frissítve. További információt [ebben a blogbejegyzésben talál.](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

## <a name="july-2015-release"></a><a id="july_changes_15"></a>2015. júliusi kiadás
* Bejelentették a Media Encoder Standard általános elérhetőségét. További információt [ebben a blogbejegyzésben talál.](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
  
    A Media Encoder Standard előre beállított beállításokat használ az ebben a szakaszban leírtak [szerint.](https://go.microsoft.com/fwlink/?LinkId=618336) Ha 4K kódoláshoz egy készletet használ, a Premium lefoglalt egység típusát kapja meg. További információt a [Kódolás méretezése](media-services-scale-media-processing-overview.md)című témakörben talál.
* Élő valós idejű feliratokat használtak a Media Services és a Media Player szolgáltatásban. További információt [ebben a blogbejegyzésben talál.](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK mostantól a 3.4.0.0-s verzió. A következő frissítések történtek: 

* Támogatás valósult meg az élő archívum. Élő archívumot tartalmazó eszköz nem tölthető le.
* A támogatás a dinamikus szűrőkhöz lett megvalósítva.
* A funkció úgy lett megvalósítva, hogy a felhasználók megtarthassák a tárolótárolót, miközben törölnek egy eszközt.
* A hibajavítások a csatornák újrapróbálkozási irányelveivel kapcsolatosak.
* Media Encoder Prémium munkafolyamat engedélyezve van.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>2015. júniusi kiadás
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK mostantól a 3.3.0.0-s verzió. A következő frissítések történtek: 

* Az OpenId Connect felderítési specifikációja támogatást kapott.
* Támogatás lett hozzáadva a kulcsok váltásának kezeléséhez az identitásszolgáltató oldalán.

Ha olyan identitásszolgáltatót használ, amely openId connect-felderítési dokumentumot tesz elérhetővé (azure AD, Google és Salesforce esetén), utasíthatja a Media Servicest, hogy a JSON webtokenek (JWT) érvényesítéséhez aláírási kulcsokat szerezzen be az OpenID Connect felderítési specifikációból. 

További információt a [JSON webkulcsok használata az OpenID Connect felderítési specifikációjában a JWT-hitelesítés használatához a Media Services szolgáltatásban című témakörben talál.](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)

## <a name="may-2015-release"></a><a id="may_changes_15"></a>2015. májusi kiadás
A következő új funkciókat jelentették be:

* [Az élő kódolás előnézete a Media Services szolgáltatással](media-services-manage-live-encoder-enabled-channels.md)
* [Dinamikus jegyzékfájl](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>2015. áprilisi kiadás
### <a name="general-media-services-updates"></a>Általános Médiaszolgáltatások frissítései
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) bejelentették.
* A Media Services REST 2.10-es rendszertől kezdve a valós idejű üzenetkezelési protokoll (RTMP) betöltésére konfigurált csatornák elsődleges és másodlagos betöltési URL-címekkel jönnek létre. További információ: [Channel beingest configurations](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Az Azure Media Indexer frissítve lett.
* Támogatás a spanyol nyelv bővült.
* Új konfiguráció került hozzáadásra az XML-formátumhoz.

További információ: [ez a blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK mostantól a 3.2.0.0-s verzió. A következő frissítések történtek:

* Változás megszakadása: A TokenRestrictionTemplate.Issuer és a TokenRestrictionTemplate.Audience karakterlánctípusúvá változott.
* Az egyéni újrapróbálkozási házirendek létrehozásával kapcsolatos frissítések történtek.
* Hibajavítások történtek kapcsolatos fájlok feltöltése és letöltése.
* A MediaServicesCredentials osztály most már elfogadja az elsődleges és másodlagos hozzáférés-vezérlési végpontokat, amelyeken hitelesíteni kell.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>2015. márciusi kiadás
### <a name="general-media-services-updates"></a>Általános Médiaszolgáltatások frissítései
* A Media Services mostantól biztosítja a Content Delivery Network integrációját. Az integráció támogatásához a CdnEnabled tulajdonság hozzá lett adva a StreamingEndpointhoz. A CdnEnabled a 2.9-es verziótól kezdődő REST API-kkal használható. További információ: [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). A CdnEnabled a .NET SDK-val használható a 3.1.0.2-es verziótól kezdve. További információ: [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* A Media Encoder Premium munkafolyamat ot jelentették be. További információ: [A prémium szintű kódolás bevezetése az Azure Media Servicesszolgáltatásban.](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

## <a name="february-2015-release"></a><a id="february_changes_15"></a>2015. februári kiadás
### <a name="general-media-services-updates"></a>Általános Médiaszolgáltatások frissítései
A Media Services REST API mostantól 2.9-es verziójú. Ezzel a verzióval kezdve engedélyezheti a Content Delivery Network integrációt a streamelési végpontokkal. További információ: [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>2015. januári kiadás
### <a name="general-media-services-updates"></a>Általános Médiaszolgáltatások frissítései
A tartalomvédelem általános elérhetőségét dinamikus titkosítással jelentették be. További információ: [Media Services enhances streaming security with general availability of DRM technology](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK mostantól a 3.1.0.1-es verzió.

Ez a kiadás elavultnak nevezte az alapértelmezett Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate konstruktort. Az új konstruktor a TokenType-ot argumentumként veszi fel.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a name="december-2014-release"></a><a id="december_changes_14"></a>2014. decemberi kiadás
### <a name="general-media-services-updates"></a>Általános Médiaszolgáltatások frissítései
* Néhány frissítés és új funkció került be a Médiaindexelőbe. További információ: [Azure Media Indexer version 1.1.6.7 release notes](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Egy új REST API került hozzáadásra, amely segítségével frissítheti a kódolás fenntartott egységek. További információ: [EncodingReservedUnitType with REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Cors-támogatás lett hozzáadva a kulcskézbesítési szolgáltatáshoz.
* Az engedélyezési házirend-beállítások lekérdezése teljesítménybeli javulást eredményezett.
* A kínai adatközpontban a [kulcskézbesítési URL-cím](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) mostantól ügyfélenként van (csakúgy, mint más adatközpontokban).
* HLS automatikus cél időtartama lett hozzáadva. Élő közvetítés közben a HLS mindig dinamikusan van csomagolva. Alapértelmezés szerint a Media Services automatikusan kiszámítja a HLS szegmens csomagolási arányát (FragmentsPerSegment) a kulcsképkocka-időköz (KeyFrameInterval) alapján. Ezt a módszert képcsoportnak (GOP) is nevezik, amely az élő kódolótól érkezik. További információt a [Media Services élő közvetítése című témakörben](https://msdn.microsoft.com/library/azure/dn783466.aspx)talál.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) mostantól a 3.1.0.0-s verzió. A következő frissítések történtek:

* A .NET SDK-függőség frissítve lett a .NET 4.5 Keretrendszerre.
* Egy új API-t, amely segítségével frissítheti a kódolás fenntartott egységek bővült. További információt a [Fenntartott egységtípus frissítése és a kódolásra fenntartott egységek növelése a .NET használatával című témakörben talál.](media-services-dotnet-encoding-units.md)
* JWT-támogatás a token hitelesítéshez hozzáadva. További információ: [JWT token hitelesítés a Media Services és a dinamikus titkosítás](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* A PlayReady licencsablon begindate és expirationdate relatív eltolásai kerültek hozzáadásra.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>2014. novemberi kiadás
* Most már használhatja a Media Services élő Smooth Streaming (fMP4) tartalom tls-kapcsolaton keresztül történő betöltéséhez. A TLS-en keresztül történő betöltéshez frissítse a betöltési URL-címet HTTPS-re. Jelenleg a Media Services nem támogatja a TLS egyéni tartományok. Az élő közvetítésről további információt az [Azure Media Services élő közvetítése](https://msdn.microsoft.com/library/azure/dn783466.aspx)című témakörben talál.
* Jelenleg nem lehet betöltése RTMP élő közvetítés tls-kapcsolaton keresztül.
* 2014. szeptember 10-e után csak akkor streamelhet TLS-en keresztül, ha az a streamelési végpont, amelyből a tartalmat kézbesíti, létrejött. Ha a streamelési URL-címek a 201 streaming.mediaservices.windows.net4. A "origin.mediaservices.windows.net" (a régi formátum) tartalmazó streamelési URL-ek nem támogatják a TLS-t. Ha az URL-cím a régi formátumban van, és TLS-en szeretne streamelni, [hozzon létre egy új streamelési végpontot.](media-services-portal-manage-streaming-endpoints.md) A tartalom TLS-en keresztül történő streameléséhez használja az új streamelési végponton alapuló URL-címeket.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Media Services .NET SDK
A Media Services SDK a .NET kiterjesztések most verzió 2.0.0.3.

A.NET Media Services SDK-ja mostantól a 3.0.0.8-as verzió. A következő frissítések történtek:

* Az újrafactorálás az újrapróbálkozási házirend-osztályokban lett megvalósítva.
* Egy felhasználói ügynökkarakterlánc ot adott hozzá a HTTP-kérelem fejléceihez.
* Egy NuGet-visszaállítási buildlépés került hozzáadásra.
* A forgatókönyv-teszteket az x509-es tanúsítvány tárolóból való használatára rögzítették.
* A rendszer ellenőrzési beállításokat adott hozzá a csatorna és a streamelés frissítésének befejezéséhez.

### <a name="new-github-repository-to-host-media-services-samples"></a>Új GitHub-tárház a Media Services-minták üzemeltetéséhez
A minták a [Media Services mintáig, a GitHub-tárházban](https://github.com/Azure/Azure-Media-Services-Samples)találhatók.

## <a name="september-2014-release"></a><a id="september_changes_14"></a>2014. szeptemberi kiadás
A Media Services REST metaadatai mostantól 2.7-es verziójúak. A rest-frissítésekről további információt a [Media Services REST API-hivatkozási útmutatójában talál.](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)

A Media Services SDK a .NET most verzió 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Változások megbontása
* Az Origin neve [StreamingEndpoint]lett.
* Az alapértelmezett viselkedés módosítása történt, amikor az Azure Portalon mp4-fájlok kódolása és közzététele történik.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Az általános rendelkezésre állási kiadás részét vevő új funkciók/forgatókönyvek
* Bevezetésre került a Media Indexer médiaprocesszor. További információt a [Médiaindexelő indexelése](https://msdn.microsoft.com/library/azure/dn783455.aspx)című témakörben talál.
* A [StreamingEndpoint] entitás segítségével egyéni tartomány (állomás) neveket adhat hozzá.
  
    Ha egyéni tartománynevet szeretne használni a Media Services streamelési végpontneveként, adjon egyéni állomásneveket a streamelési végponthoz. Egyéni állomásnevek hozzáadásához használja a Media Services REST API-jait vagy a .NET SDK-t.
  
    A következő szempontokat kell figyelembe venni:
  
  * Rendelkeznie kell az egyéni tartománynév tulajdonjogával.
  * A tartománynév tulajdonjogát a Media Servicesnek ellenőriznie kell. A tartomány érvényesítéséhez hozzon létre egy CName nevet, amely leképezi a MediaServicesAccountId szülőtartományt a DNS mediaservices-dns-zóna ellenőrzéséhez.
  * Létre kell hoznia egy másik CName nevet, amely leképezi az egyéni állomásnevet (például sports.contoso.com) a Media Services StreamingEndpoint állomásnevére (például amstest.streaming.mediaservices.windows.net).

    További információt a [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx) cikk CustomHostNames tulajdonságában talál.

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>A nyilvános előzetes verzióban megjelenő új funkciók/forgatókönyvek
* Élő közvetítés előnézete. További információt a [Media Services élő közvetítése című témakörben](https://msdn.microsoft.com/library/azure/dn783466.aspx)talál.
* Kulcs kézbesítési szolgáltatás. További információ: [AES-128 dinamikus titkosítás használata és a kulcskézbesítési szolgáltatás.](https://msdn.microsoft.com/library/azure/dn783457.aspx)
* AES dinamikus titkosítás. További információ: [AES-128 dinamikus titkosítás használata és a kulcskézbesítési szolgáltatás.](https://msdn.microsoft.com/library/azure/dn783457.aspx)
* PlayReady licenckézbesítési szolgáltatás. 
* PlayReady dinamikus titkosítás. 
* Media Services PlayReady licencsablon. További információt a [Media Services PlayReady licencsablon – áttekintés című témakörben talál.]
* Tárolóval titkosított eszközök streamelése. További információ: [Stream storage-encrypted content](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>2014. augusztusi kiadás
Eszköz kódolásakor egy kimeneti eszköz keletkezik, amikor a kódolási feladat befejeződött. A jelen kiadásig a Media Services Kódoló metaadatokat készített a kimeneti eszközökről. Ettől a kiadástól kezdve a kódoló metaadatokat is készít a bemeneti eszközökről. További információ: [Input metadata] and [Output metadata].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>2014. júliusi kiadás
Az Azure Media Services packager és encryptor a következő hibajavításokat hajtotta végre:

* Amikor egy élő archív eszköz továbbításra kerül a HLS-nek, csak a hang lejátszása történik: Ez a probléma megoldódott, és most mind a hang, mind a videó lejátszható.
* Ha egy eszköz HLS- és AES 128 bites borítéktitkosításba van csomagolva, a csomagolt adatfolyamok nem játszanak le androidos eszközökön: Ez a hiba ki lett javítva, és a csomagolt adatfolyam lejátssza a HLS-t támogató Android-eszközökön.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>2014. májusi kiadás
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Általános Médiaszolgáltatások frissítései
Most már [használhatja a dinamikus csomagolást] a HLS 3-as verziójának streameléséhez. A HLS 3-as verziójának streameléséhez adja hozzá a következő formátumot az eredeti helymeghatározó elérési úthoz: * .ism/manifest(format=m3u8-aapl-v3). További információ, lásd [ezen a fórumon](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

A dinamikus csomagolás most antól támogatja a PlayReady-vel titkosított HLS (3-as és 4-es verzió) szállítását is, amely a PlayReady-vel statikusan titkosított Smooth Streaming-en alapul. A Smooth Streaming PlayReady segítségével való titkosításáról a [Smooth Streaming védelme playready-rel című](https://msdn.microsoft.com/library/azure/dn189154.aspx)témakörben talál további információt.

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK mostantól a 3.0.0.5-ös verzió. A következő frissítések történtek:

* Médiaeszközök feltöltésekése és letöltése esetén a sebesség és a rugalmasság jobb.
* Fejlesztések történtek az újrapróbálkozási logikában és az átmeneti kivételkezelésben: 
  
  * Az átmeneti hibaészlelési és újrapróbálkozási logika továbbfejlesztésre került a lekérdezés, a módosítások mentése, valamint a fájlok feltöltése vagy letöltése során okozott kivételek esetében. 
  * Amikor webes kivételeket kap (például egy hozzáférés-vezérlési szolgáltatás jogkivonat-kérése során), a végzetes hibák most gyorsabban sikertelenek.

További információt [a Media Services SDK for .]

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>2014. januári/februári kiadások
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 és 3.0.0.3
A 3.0.0.1.

* Az OrderBy utasításokkal kapcsolatos LINQ-lekérdezések használatával kapcsolatos problémák at megoldották.
* A [GitHub] tesztmegoldásai egységalapú tesztekre és forgatókönyv-alapú tesztekre vannak felosztva.

A változásokról további információt a [Media Services .NET SDK 3.0.0.1 és 3.0.0.2 kiadásban talál.](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html)

A 3.0.0.3-as verzióban a következő módosítások történtek:

* Az Azure storage-függőségek a 3.0.3.0-s verzió használatára frissítve voltak.
* A visszamenőleges kompatibilitási problémát a 3.0-s verzióhoz javították. *.* Kiadások.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>2013. decemberi kiadás
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Médiaszolgáltatások .NET SDK 3.0.0.0
> [!NOTE]
> A 3.0.x.x kiadások nem kompatibilisek visszafelé a 2.4.x.x kiadásokkal.
> 
> 

A Media Services SDK legújabb verziója most 3.0.0.0. Letöltheti a legújabb csomagot a NuGet-ből, vagy beszerezheti a biteket a [GitHubról.]

A Media Services SDK 3.0.0.0-s verziójától kezdve újra felhasználhatja az [Azure AD Access Control Service](https://msdn.microsoft.com/library/hh147631.aspx) tokeneket. További információt a Csatlakozás a [Médiaszolgáltatásokhoz a Media Services SDK for .](https://msdn.microsoft.com/library/azure/jj129571.aspx)

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK-bővítmények 2.0.0.0
 A Media Services .NET SDK-bővítmények olyan bővítménymetódusok és segítő funkciók, amelyek leegyszerűsítik a kódot, és megkönnyítik a Media Services szolgáltatással való fejlesztést. A legfrissebb biteket a [Media Services .NET SDK-bővítményekből szerezheti](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev)be.

## <a name="november-2013-release"></a><a id="november_changes_13"></a>2013. novemberi kiadás
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>A Media Services .NET SDK módosításai
Ezzel a verzióval kezdve a Media Services SDK for .

## <a name="august-2013-release"></a><a id="august_changes_13"></a>2013. augusztusi kiadás
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Media Services PowerShell-parancsmagok az Azure SDK-eszközökben
Az [Azure SDK-eszközök](https://github.com/Azure/azure-sdk-tools)a következő Media Services PowerShell-parancsmagok:

* Get-AzureMediaServices 

    Például:`Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Például:`New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Például:`New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Például:`Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>2013. júniusi kiadás
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>A Media Services változásai
Az ebben a szakaszban említett következő módosítások a Media Services 2013 júniusi kiadásaiban szereplő frissítések:

* Több tárfiók összekapcsolása egy Media Service-fiókkal. 
    * StorageAccount
    * Asset.StorageAccountName és Asset.StorageAccount
* A Feladat.Prioritás frissítésének lehetősége. 
* Értesítéssel kapcsolatos entitások és tulajdonságok: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Feladat
* Eszköz.Uri 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>A Media Services .NET SDK módosításai
A következő módosításokat a Media Services SDK 2013 júniusi kiadásai tartalmazzák. A legújabb Media Services SDK elérhető a GitHubon.

* A 2.3.0.0-s verziótól kezdve a Media Services SDK támogatja több tárfiók összekapcsolását egy Media Services-fiókkal. A következő API-k támogatják ezt a funkciót:
  
    * IStorageAccount típusa
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts tulajdonság
    * StorageAccount tulajdonság
    * StorageAccountName tulajdonság
  
      További információt a [Media Services-eszközök kezelése több tárfiókban című témakörben talál.](https://msdn.microsoft.com/library/azure/dn271889.aspx)
* Értesítéssel kapcsolatos API-k. A 2.2.0.0-s verzióval kezdődően meghallgathatja az Azure Queue storage-értesítéseit. További információt a Media Services feladatértesítések kezelése című [témakörben talál.](https://msdn.microsoft.com/library/azure/dn261241.aspx)
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions tulajdonság
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint típus
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription típus
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection típusú
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType típus
* Függőség a Storage ügyfél SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Az OData 5.5-től való függőség (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>2012. decemberi kiadás
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>A Media Services .NET SDK módosításai
* IntelliSense: Hiányzó IntelliSense dokumentáció egészül ki sok típus.
* Microsoft.Practices.TransientFaultHandling.Core: Egy olyan hiba történt, amely miatt az SDK-nak még mindig volt függősége a szerelvény egy régi verziójától. Az SDK most a szerelvény 5.1.1209.1-es verziójára hivatkozik.

A 2012 novemberében található SDK-ban található problémák javításai:

* IAsset.Locators.Count: Ez a szám most már helyesen jelentett az új IAsset-felületeken, miután az összes lokátor ttörölte.
* IAssetFile.ContentFileSize: Ez az érték most már megfelelően van beállítva az IAssetFile.Upload(filepath) feltöltése után.
* IAssetFile.ContentFileSize: Ez a tulajdonság most már beállítható, amikor létrehoz egy eszközfájlt. Korábban csak olvasható volt.
* IAssetFile.Upload(filepath): Hiba történt, amikor ez a szinkron feltöltési módszer a következő hibát okozta, amikor több fájlt töltöttek fel az eszközre. A hiba a következő volt: "A kiszolgáló nem tudta hitelesíteni a kérelmet. Győződjön meg arról, hogy az Engedélyezési fejléc értéke megfelelően van kialakítva az aláírással együtt."
* IAssetFile.UploadAsync: Megoldottuk azt a problémát, amely a fájlok egyidejű feltöltését öt fájlra korlátozta.
* IAssetFile.UploadProgressChanged: Ezt az eseményt most az SDK biztosítja.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Ez a metódus túlterhelése most már biztosított.
* IAssetFile.DownloadAsync: Megoldottuk azt a problémát, amely a fájlok egyidejű letöltését öt fájlra korlátozta.
* IAssetFile.Delete(): Kijavítottunk egy problémát, amely miatt a delete hívása kivételt eredményezhet, ha nem töltött fel fájlt az IAssetFile fájlhoz.
* Feladatok: Ki lett javítva egy hiba, amely miatt az "MP4–sima adatfolyamok" feladat "PlayReady Protection Task" feladatsablon használatával történő láncolása egyfeladatsablon használatával egyáltalán nem hoz létre feladatokat.
* EncryptionUtils.GetCertificateFromStore(): Ez a módszer már nem okoz null hivatkozási kivételt, mivel a tanúsítvány konfigurációs problémái alapján nem találták meg a tanúsítványt.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>2012. novemberi kiadás
Az ebben a szakaszban említett módosítások a 2012 novemberi (2.0.0.0) SDK-ban szereplő frissítések voltak. Ezek a módosítások szükségessé tehetik a 2012 júniusi előzetes SDK-kiadáshoz írt kódok módosítását vagy újraírását.

* Objektumok
  
    * Az IAsset.Create(assetName) az *egyetlen* eszközlétrehozási függvény. Az IAsset.Create már nem tölt fel fájlokat a metódushívás részeként. Az IAssetFile fájl használata feltöltéshez.
    * Az IAsset.Publish metódus és az AssetState.Publish enumerálási érték el került a Services SDK-ból. Minden olyan kódot, amely ezen az értéken alapul, újra kell írni.
* Fájlinformáció
  
    * Ezt az osztályt eltávolították, és az IAssetFile váltotta fel.
  
* IAssetFájlok
  
    * Az IAssetFile lecseréli a FileInfo fájlt, és más a viselkedése. A használathoz példányosítsa ki az IAssetFiles objektumot, majd töltse fel a fájlokat a Media Services SDK vagy a Storage SDK használatával. A következő IAssetFile.Upload túlterhelések használhatók:
  
        * IAssetFile.Upload(filePath): Ez a szinkron metódus blokkolja a szálat, és csak akkor javasoljuk, ha egyetlen fájlt tölt fel.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): Ez az aszinkron módszer az előnyben részesített feltöltési mechanizmus. 
    
            Ismert hiba: Ha a törlési jogkivonatot használja, a feltöltés megszakad. A feladatoknak számos lemondási állapotuk lehet. Megfelelően el kell fognia és kezelnie kell a kivételeket.
* Lokátorok
  
    * Az eredetspecifikus verziókat eltávolították. A SAS-specifikus környezetben. Locators.CreateSasLocator (eszköz, accessPolicy) lesz megjelölve elavult, vagy el kell távolítani az általános rendelkezésre állás. A frissített működést lásd a "Lokátorok" című részben az "Új funkciók" részben.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>2012. júniusi előzetes kiadás
Az SDK novemberi kiadásában a következő funkciók újak lehetnek:

* Entitások törlése
  
    * Az IAsset, iAssetFile, ILocator, IAccessPolicy és IContentKey objektumok mostantól törlődnek az objektum szintjén, azaz iObject.Delete(), ahelyett, hogy törölni kellene a gyűjteményben, azaz a cloudMediaContext.ObjCollection.Delete(objInstance).
* Lokátorok
  
    * A lokátorokat most a CreateLocator metódussal kell létrehozni. A LocatorType.SAS vagy a LocatorType.OnDemandOrigin felsorakértékeket kell használniuk a létrehozni kívánt helymeghatározó adott típusának argumentumaként.
    * Új tulajdonságokkerültek a lokátorokhoz, hogy megkönnyítsék a tartalom használható URI-inak beszerzését. A lokátorok újratervezése nagyobb rugalmasságot biztosít a harmadik felek jövőbeli bővíthetősége számára, és növeli a médiaügyfél-alkalmazások egyszerű használatát.
* Aszinkron metódus támogatása
  
    * Az összes metódus aszinkron támogatást kapott.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Az Azure Media Services MSDN fóruma]: https://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Az Azure Media Services REST API-hivatkozása]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Bemeneti metaadatok]: https://msdn.microsoft.com/library/azure/dn783120.aspx
[Kimeneti metaadatok]: https://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: https://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: https://msdn.microsoft.com/library/azure/dn783455.aspx
[Streamelésvégpont]: https://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: https://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: https://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: https://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: https://azure.microsoft.com/services/preview/
[Media Services PlayReady licencsablon – áttekintés]: https://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: https://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Dinamikus csomagolás]: https://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: https://msdn.microsoft.com/library/azure/dn189154.aspx
[A .NET Media Services SDK-ban az újrapróbálkozási logika]: https://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: https://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: https://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: https://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: https://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: https://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: https://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: https://msdn.microsoft.com/library/azure/dn261241.aspx

