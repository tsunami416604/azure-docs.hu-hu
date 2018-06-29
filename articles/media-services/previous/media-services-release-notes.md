---
title: A Media Services kibocsátási megjegyzései |} Microsoft Docs
description: A Media Services kibocsátási megjegyzései
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako
ms.openlocfilehash: 1a874e31c05d287522810782f743d389c86dbe27
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098923"
---
# <a name="azure-media-services-release-notes"></a>Az Azure Media Services kibocsátási megjegyzései
Ezek a kibocsátási megjegyzések az Azure Media Services összesítse a módosításokat a korábbi kiadásokban és ismert problémákat.

> [!NOTE]
> Azt szeretnénk, hogy hallhassa kapunk az ügyfelektől, hogy összpontosíthatunk előforduló problémák kijavítása. A problémákat, vagy kérdései vannak, a közlemény elküldése a [Az Azure Media Services MSDN fórum].
> 
> 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Ismert problémák
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>A Media Services általános problémák

| Probléma | Leírás |
| --- | --- |
| Több közös HTTP-fejlécek nem találhatók a REST API-t. |Media Services-alkalmazások fejlesztése a REST API használatával, ha úgy gondolja, hogy egyes közös HTTP-fejléc mezőit (beleértve CLIENT-REQUEST-ID-azonosító, és VISSZATÉRÉSI-CLIENT-REQUEST-ID) nem támogatottak. A fejlécek az egy későbbi kiadásban lesz hozzáadva. |
| Százalék-kódolás nem engedélyezett. |Media Services a IAssetFile.Name tulajdonság értékét használja, az adatfolyam-tartalmak (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters) URL-címek fejlesztéskor. Emiatt százalék-kódolás nem engedélyezett. A Name tulajdonság értéke nem lehet a következő [százalék kódolás-fenntartott karakterek](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Emellett lehetnek csak egy "." a fájlnévkiterjesztés. |
| A ListBlobs metódus, amely része az Azure Storage szolgáltatás SDK verzió 3.x sikertelen lesz. |A Media Services SAS URL-címek alapján hoz létre a [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) verziója. Ha a lista blobot, amely egy blob tároló a Storage szolgáltatás SDK használni kívánt, használja a [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) módszer, amelynek része a Storage szolgáltatás SDK verzió 2.x. |
| A Media Services mechanizmus szabályozás korlátozza az alkalmazásokat, amelyek a szolgáltatás túl sok kéréseket erőforrás-felhasználása. A szolgáltatás a "Szolgáltatás nem érhető el" 503-as HTTP-állapotkód: Előfordulhat, hogy vissza. |További információkért lásd a 503-as HTTP-állapotkód: a leírását [Media Services hibakódok](media-services-encoding-error-codes.md). |
| Ha entitások, legfeljebb 1000 entitások vissza van egy időben, mert a nyilvános REST 2-es korlátozza a lekérdezési eredmények 1000 eredmények. |Használja a Skip és érvénybe (.NET) / (REST) top, a [.NET példában](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [a REST API-példa](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Egyes ügyfelek között egy ismétlődő címke probléma a Smooth Streaming jegyzékben származhatnak. |További információkért lásd: [ebben a szakaszban](media-services-deliver-content-overview.md#known-issues). |
| Media Services .NET SDK-objektumok nem szerializálható, és ennek eredményeképpen az Azure Redis Cache nem működnek. |Ha az SDK AssetCollection objektum hozzáadása az Azure Redis Cache szerializálható kísérli meg, a rendszer kivételt hoz létre. |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>REST API verziójának előzményei
A Media Services REST API verziójának előzményei kapcsolatos információkért tekintse meg a [Az Azure Media Services REST API-referencia].

## <a name="may-2018"></a>2018. május 

2018. május 12., az élő csatornák indítása rendszer már nem támogatja a RTP/MPEG-2 transport stream betöltési protokollt. Végezzen áttelepítést az RTP/MPEG-2 RTMP vagy töredezett MP4) (Smooth Streaming) betöltési protokollok.

## <a name="october-2017-release"></a>2017. októberi kiadás
> [!IMPORTANT] 
> A Media Services ból kivezettük való hozzáférés-vezérlési szolgáltatásban Azure hitelesítési kulcsokat támogatása. 2018. június 22. a már nem hitelesítheti a Media Services háttérrendszer működésében kód hozzáférés-vezérlési szolgáltatásban kulcsok használatával. Frissítse a kódot az Azure Active Directoryt (Azure AD) a [az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). Ez a változás az Azure portálon kapcsolatos figyelmeztetések figyelje.

### <a name="updates-for-october-2017"></a>2017. októberi frissítései
#### <a name="sdks"></a>SDK-k
* A .NET SDK frissült az Azure AD-alapú hitelesítés támogatásához. Hozzáférés-vezérlési szolgáltatásban hitelesítés támogatása el lett távolítva a legújabb .NET SDK Nuget.org bátorítva gyorsabb áttelepítést az Azure AD meg. 
* A JAVA SDK frissült az Azure AD-alapú hitelesítés támogatásához. Az Azure AD-alapú hitelesítés támogatása a Java SDK lett adva. A Media Services a Java SDK használatáról információkért lásd: [Ismerkedés a Java-ügyfél SDK az Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Fájl alapú kódolás
* Most már használhatja a prémium szintű kódolás (HEVC) videó kodek kódolási H.265 nagy hatékonyságú videó tartalom kódolása. Ha úgy dönt, H.265 keresztül más kodekek, például a H.264, nincs árképzési hatással. HEVC szabadalmi licencek kapcsolatos információkért lásd: [Online szolgáltatási feltételek](https://azure.microsoft.com/support/legal/).
* Forrás videót a H.265 (HEVC) videó kodek, például a videó iOS11 vagy GoPro Hero 6, használatával rögzíteni a kódolt most már használhatja a prémium szintű kódolás vagy a Standard Encoder ezeket a videók kódolásához. Szabadalmi licencek kapcsolatos információkért lásd: [Online szolgáltatási feltételek](https://azure.microsoft.com/support/legal/).
* Több nyelv zeneszámok tartalmazó tartalomhoz a nyelvi értékek megfelelően jelölni kell a megfelelő fájlformátum specifikációjának (például ISO MP4) megfelelően. A Standard Encoder használja majd az adatfolyamként történő tartalom kódolása. Az eredő streamelési locator elérhető hang nyelvek listája.
* Standard Encoder mostantól támogatja a két új csak rendszer készletek, "AAC hang" és "AAC jó minőségű hang." Speciális (AAC) kimeneti 128 kb/s és 192 Kbit/s átviteli sebességét, illetve kódolási hang sztereó is eredményez.
* A prémium szintű kódolás mostantól támogatja a QuickTime/MOV fájlformátumok bemeneti adatként. A videó kodek egyikének kell lennie a [Apple ProRes típusok a Githubon cikkben szereplő](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). A hang vagy AAC vagy impulzusos kód modulációs. A prémium szintű kódolás nem támogatja, például DVC/DVCPro videó QuickTime/MOV fájlok csomagolni bemeneti adatként. Standard Encoder a Videó kodekek támogatja.
* A következő hibajavítások került sor kódolók képesek:

    * Feladatok egy bemeneti eszköz használatával most elküldéséhez. Ezek a feladatok befejezése után módosíthatja az eszköz (például hozzáadásához, törléséhez vagy nevezze át fájlokat az eszköz belül), valamint további feladatok elküldéséhez.
    * A szabványos kódoló által előállított JPEG miniatűrök minőségének tovább lett fejlesztve.
    * Standard Encoder bemeneti metaadatok és a nagyon rövid időtartam videók jobb miniatűr generációs kezeli.
    * A használt a adás H.264 dekóder fejlesztései egyes ritka összetevők megszüntetéséhez. 

#### <a name="media-analytics"></a>Media Analytics
Általános rendelkezésre állását az Azure Media Redactor: A media processzor fellazítja a kijelölt személyeket felületei anonymization végez, és nyilvános biztonsági és hírek media olyan esetekben ideális. 

Az új processzorokon áttekintéséért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/azure-media-redactor/). Dokumentáció és a beállítások információkért lásd: [kivonása az Azure Media Analytics lapok](media-services-face-redaction.md).



## <a name="june-2017-release"></a>2017. június kiadás

Most már támogatja a Media Services [az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Jelenleg a Media Services a hozzáférés-vezérlési szolgáltatásban hitelesítési modellt támogatja. 2018. június 1. a hozzáférés-vezérlési szolgáltatásban engedélyezési elavulttá válik. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="march-2017-release"></a>2017. március kiadás

Ezután már használhatja a szabványos kódoló [automatikus létrehozása egy sávszélességű létra](media-services-autogen-bitrate-ladder-with-mes.md) egy kódolási feladat létrehozásakor a karakterlánc a "adaptív Streameléshez" beállított megadásával. A Media Services streaming videó kódolására, használja a "Adaptív Streameléshez" készlet. Az adott forgatókönyv szerint az adott néven beállítás kódolással testreszabásához megkezdheti a [a készleteket](media-services-mes-presets-overview.md).

Ezután már használhatja Media Encoder Standard vagy a Media Encoder prémium munkafolyamat [állít elő, fMP4 adattömbök kódolási feladat létrehozása](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>2017. február kiadás

2017. április 1., kezdési 90 napnál régebbi fiókjában feladat rekordot automatikusan törlődik, és a kapcsolódó rekordok. Törlés következik be, akkor is, ha a rekordok száma nem éri el a kvóta felső határát. A feladat vagy tevékenység adatai archiválására, az ismertetett kódot használhatja [kezelése az eszközök és a kapcsolódó entitásokból a Media Services .NET SDK-val](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>2017. január kiadás

A Media Services szolgáltatásban a streamvégpontján egy adatfolyam-szolgáltatás által biztosított tartalom közvetlenül egy ügyfélalkalmazás player vagy későbbi terjesztés tartalomkézbesítési hálózat (CDN) jelöli. A Media Services is biztosít az Azure Content Delivery Network való zökkenőmentes integrációt. A kimenő adatfolyam egy StreamingEndpoint szolgáltatásból lehet élő stream, az igény szerinti videó vagy az eszköz a Media Services-fiók a progresszív letöltés. Minden egyes Media Services-fiók egy alapértelmezett streamvégpontból tartalmazza. A fiók alatt további streamvégpontok hozhatók létre. 

Adatfolyam-végpontok, két verziója van 1.0-s és 2.0-s. 2017. január 10., kezdési egyetlen újonnan létrehozott Media Services-fiókot a 2.0-s verziójának alapértelmezett streamvégpontból tartalmazza. Ehhez a fiókhoz hozzáadni kívánt további streamvégpontok egyaránt 2.0-s verziójában. Ez a változás nincs hatással a meglévő fiókokat. Meglévő streamvégpontok 1.0-s verziója és a 2.0-s verziójának frissítése. Nincsenek viselkedés, a számlázással és a szolgáltatás módosításokat az módosítását. További információk: [Streamvégpontok áttekintése](media-services-streaming-endpoints-overview.md).

Media Services a 2.15 verziójától kezdve, az adatfolyam-továbbítási végpont entitásban hozzáadva a következő tulajdonságokkal:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Ezeket a tulajdonságokat a további információkért lásd: [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>2016. december kiadás

 Most segítségével Media Services access telemetriai/metrikai adatok a szolgáltatásokhoz. Adatfolyam-továbbítási végpontra élő csatorna, telemetriai adatokat gyűjthet a Media Services aktuális verzióját használja, és archiválja entitások. További információkért lásd: [Media Services telemetriai](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>2016. július kiadás
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Frissítések az Alkalmazásjegyzék-fájl (*. ISM) generálja a kódolási feladatokhoz
Amikor egy kódolási feladat Media Encoder Standard vagy a Media Encoder Premium, a kódolási feladat hoz létre egy [adatfolyam jegyzékfájl](media-services-deliver-content-overview.md) (* .ism) található a kimeneti adategységen. A legújabb szolgáltatás a kiadástól kezdve a szintaxist, az adatfolyam-továbbítási jegyzékfájl frissült.

> [!NOTE]
> A jegyzékfájl (.ism) adatfolyamfájl szintaxisa a következő belső használatra van fenntartva. Van a jövőbeni kiadásokban változhat. Nem módosíthatók, ez a fájl tartalmának módosítására.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Egy új ügyfél jegyzékfájl (*. ISMC) fájl található a kimeneti adategységen jön létre, amikor egy kódolási feladatok kimenete egy vagy több MP4-fájlok
A szolgáltatás legújabb kiadása kezdődően egy vagy több MP4-fájlokat generál egy kódolási feladat befejezése után a kimeneti adategységen is tartalmaz egy adatfolyam-továbbítási ügyfél jegyzékfájl (*.ismc) fájlt. A .ismc fájl javítja a teljesítményt, a dinamikus adatfolyamként való továbbítására. 

> [!NOTE]
> Az ügyfél-jegyzékfájl (.ismc) fájl szintaxisa a következő belső használatra van fenntartva. Van a jövőbeni kiadásokban változhat. Nem módosíthatók, ez a fájl tartalmának módosítására.
> 
> 

További információkért lásd: [ebben a blogban](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Ismert problémák
Egyes ügyfelek között egy ismétlődő címke probléma a Smooth Streaming jegyzékben származhatnak. További információkért lásd: [ebben a szakaszban](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>2016. április kiadás
### <a name="media-analytics"></a>Media Analytics
 A Media Services Médiaelemzés hatékony videó eszközintelligencia bevezetni. További információkért lásd: [Media Services elemző áttekintése](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (előzetes verzió)
Most már használhatja a Media Services dinamikus titkosítást a HTTP Live Streaming (HLS) az Apple FairPlay tartalom. Is használhatja a Media Services licenctovábbítási szolgáltatása képes biztosítani a FairPlay-licenc ügyfelek számára. További információkért olvassa el az "Azure Media Services a HLS-tartalmak védelme a Apple FairPlay."

## <a id="feb_changes16"></a>2016. februári kiadás
A Media Services SDK for .NET (3.5.3) legújabb verzióját a Google Widevine-kapcsolódó hibajavítás tartalmazza. Több eszköz szolgáltatással végzett Widevine titkosítása AssetDeliveryPolicy újból lehetetlen volt. A hibajavítás részeként a következő tulajdonság hozzá lett adva az SDK-val: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>2016. január kiadás
Kódolási fenntartott egységek lettek átnevezve kódoló nevek összetéveszthető csökkentése érdekében.

A Basic, Standard és Premium kódolási fenntartott egységek S1, S2, átnevezésre volt, és S3 szolgáltatás számára fenntartott egység, illetve. Az ügyfelek, akik alapszintű kódolási fenntartott egységek ma használható S1 a címke az Azure portálon (és a számlázási) jelenik meg. Standard és prémium szintű felhasználók lásd: a címkék S2 és S3, illetve. 

## <a id="dec_changes_15"></a>2015. decemberi kiadása

### <a name="media-encoder-deprecation-announcement"></a>Media Encoder érvénytelenítése bejelentés

 Media Encoder indítása a Media Encoder Standard kiadása körülbelül 12 hónapon belül megszűnnek.

### <a name="azure-sdk-for-php"></a>Azure SDK a PHP-hoz
Az Azure SDK csapat közzé az új verziót a [Azure SDK for PHP](http://github.com/Azure/azure-sdk-for-php) csomagot, amely a Media Services frissítéseket és új szolgáltatásokat tartalmazza. Különösen a Media Services SDK php mostantól támogatja a legújabb [védelmi tartalom](media-services-content-protection-overview.md) szolgáltatásokat. Ezek a szolgáltatások akkor a dinamikus titkosítás AES és DRM (PlayReady és Widevine), és a token korlátozás nélkül. Azt is támogatja a skálázás [kódoláshoz egység](media-services-dotnet-encoding-units.md).

További információkért lásd:

* A következő [Kódminták](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) gyors megkezdéséhez segítséget:
  * **vodworkflow_aes.php**: fájl a PHP dinamikus AES-128 titkosítást és a kulcs kézbesítési szolgáltatás használatát mutatja. A .NET-mintát, tekintse meg a alapul [a dinamikus titkosítás használata AES-128 és a kulcs kézbesítési szolgáltatás](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: A PHP-fájl mutatja be dinamikus titkosítást a PlayReady és a kézbesítési szolgáltatás használatát. A .NET-mintát, tekintse meg a alapul [használata PlayReady és/vagy Widevine a dynamic common encryption](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: A PHP fájl kódolási fenntartott egységek méretezése jeleníti meg.

## <a id="nov_changes_15"></a>2015. november kiadás
 A Media Services kínál a felhőben a Widevine licenctovábbítási szolgáltatásra. További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Lásd még [ebben az oktatóanyagban](media-services-protect-with-playready-widevine.md) és a [GitHub-tárházban](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

A Media Services által biztosított Widevine licenctovábbítási szolgáltatások még csak előzetes verziójúak. További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>2015 október kiadás
A Media Services mostantól élő a következő adatközpontokban: Dél-Brazília, Nyugat-India, Dél-India és közép-India. Ezután már használhatja az Azure portálon [Media Services-fiókok létrehozása](media-services-portal-create-account.md) leírt különböző feladatok végrehajtására és a [Media Services dokumentációs weblap](https://azure.microsoft.com/documentation/services/media-services/). Ezekben az adatközpontokban élő kódolás nincs engedélyezve. További nem minden alkalmazástípus kódoláshoz fenntartott egység is elérhetők ezekben az adatközpontokban.

* Dél-Brazília: Csak a Standard és alapszintű kódoláshoz fenntartott egység érhető el.
* Nyugat-India, Dél-India és közép-India: csak alap kódoláshoz fenntartott egységek érhetők el.

## <a id="september_changes_15"></a>2015. szeptemberi kiadás
A Media Services mostantól lehetővé teszi igény szerinti és élő adatfolyamok Widevine moduláris DRM technológiával is védeni. A következő kézbesítési szolgáltatások partnerek segítségével Widevine-licencek segítségével:
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Az AssetDeliveryConfiguration Widevine használatára történő beállításához használja a [Media Services .NET SDK-t](https://www.nuget.org/packages/windowsazure.mediaservices/) (a 3.5.1-es vagy egy újabb verziót), vagy a REST API-t. 
* A Media Services Apple ProRes videók támogatása. A forrás QuickTime-videók Apple ProRes vagy más használó fájlok most már feltöltheti. További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Most már használhatja Media Encoder Standard subclipping és élő archív kiolvasásához elvégzéséhez. További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Az alábbi szűrési frissítések történtek: 
  
  * Most már használhatja az Apple HLS formátum csak hang-szűrőt. A frissítés segítségével távolítsa el az csak követése megadásával (csak = false) URL-címét.
  * Amikor fájlszűrők definiálása a eszközöket, most kombinálhatja több (legfeljebb három) szűrőt a egyetlen URL-címében.
    
    További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* A Media Services I-keretek mostantól támogatja a HLS 4-es verzióját. I-keret támogatási előretekerés és visszatekerés műveletek optimalizálja. Alapértelmezés szerint az összes HLS 4-es verziójú kimenetének tartalmazza az I-keret lista (EXT-X-I-FRAME-STREAM-INF).
További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>2015. augusztus kiadás
* A Media Services SDK a Java verzióját 0.8.0 kiadás és az új mintát is elérhető. További információkért lásd:
    
* Az Azure Media Player több hangadatfolyam-támogatással rendelkező frissült. További információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>2015. július kiadás
* Az általános rendelkezésre állását Media Encoder Standard jelentették. További információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard használ készletek, a [ebben a szakaszban](http://go.microsoft.com/fwlink/?LinkId=618336). Ha egy készletet használ az 4 KB-os kódolja, a Premium szolgáltatás számára fenntartott egység típus beolvasása. További információkért lásd: [méretezési kódolás](media-services-scale-media-processing-overview.md).
* Élő valós idejű feliratok Media Services és a Media Player volt használva. További információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
A Media Services .NET SDK verziója most 3.4.0.0. A következő frissítéseket történtek: 

* Támogatási élő archiváláshoz megtörtént. Egy élő archív tartalmazó objektumot nem lehet letölteni.
* Támogatja a dinamikus szűrők megtörtént.
* Funkcióit, hogy a felhasználók megtarthatja a tárolót, amíg azok az eszköz törlése megtörtént.
* Hibajavításokat tartalmaz, majd ismételje meg a házirendek segítségével a csatornák kapcsolódó került sor.
* Media Encoder prémium munkafolyamat volt engedélyezve.

## <a id="june_changes_15"></a>2015. június kiadás
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
A Media Services .NET SDK verziója most 3.3.0.0. A következő frissítéseket történtek: 

* Az OpenId Connect felderítési spec támogatást kaptak.
* Támogatást kaptak a identity provider oldalon kulcsok helyettesítő kezelése.

Ha használja az identitásszolgáltató, amely elérhetővé teszi az OpenID Connect felderítési dokumentum (az Azure ad-vel, mint a Google, és a Salesforce végre), Media Services JSON Web Tokens (JWTs) érvényesítése aláírási kulcsokat beszerezni az OpenID Connect felderítési spec találhatók. 

További információkért lásd: [használata JSON webes kulcsokat és a Media Services JWT hitelesítés együttműködésének az OpenID Connect felderítési spec](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>2015. május kiadás
Az alábbi új szolgáltatások bejelentette volt:

* [A Media Services élő kódolás előnézete](media-services-manage-live-encoder-enabled-channels.md)
* [Dinamikus jegyzék](media-services-dynamic-manifest-overview.md)
* [Az Azure Media Hyperlapse media processzor előnézete](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>2015. áprilisi kiadás
### <a name="general-media-services-updates"></a>Általános Media Services frissítése
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) jelentették.
* A Media Services REST 2.10 verziótól kezdődően a valós idejű üzenetküldési protokoll (RTMP) betöltési konfigurált csatornák jönnek létre az elsődleges és másodlagos betöltési URL-címeket. További információkért lásd: [csatorna betöltési konfigurációk](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Az Azure Media Indexer frissítve lett.
* Spanyol nyelvi támogatás hozzá lett adva.
* Egy új konfigurációt az XML-formátuma hozzá lett adva.

További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
A Media Services .NET SDK verziója most 3.2.0.0. A következő frissítéseket történtek:

* Változás megtörje: TokenRestrictionTemplate.Issuer és TokenRestrictionTemplate.Audience módosított egy karakterlánc típusúnak kell lennie.
* Frissítés kapcsolatos egyéni újrapróbálkozási házirendek létrehozása megtörtént.
* Hibajavítások végzett kapcsolódó feltöltése és a fájlok letöltése.
* A MediaServicesCredentials osztály most elsődleges és másodlagos access control végpontok hitelesítése fogad el.

## <a id="march_changes_15"></a>2015. márciusi kiadás
### <a name="general-media-services-updates"></a>Általános Media Services frissítése
* A Media Services most Content Delivery Network integrációt biztosít. Az integráció támogatása érdekében a CdnEnabled tulajdonság StreamingEndpoint lett adva. REST API-k 2.9 verziójától kezdve CdnEnabled használható. További információkért lásd: [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled 3.1.0.2 verziójától kezdve a .NET SDK-t is használható. További információkért lásd: [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* A Media Encoder prémium munkafolyamat jelentették. További információkért lásd: [bevezeti a prémium szintű Azure Media Services kódolási](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>2015. február kiadás
### <a name="general-media-services-updates"></a>Általános Media Services frissítése
A Media Services REST API verziója most 2.9. Ezen verziójával kezdődően a Content Delivery Network integrációja adatfolyam-végpontok is engedélyezheti. További információkért lásd: [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>2015. januári kiadás
### <a name="general-media-services-updates"></a>Általános Media Services frissítése
A dinamikus titkosítás tartalomvédelem általános rendelkezésre állását jelentették. További információkért lásd: [Media Services adatfolyam biztonsági javítja az általános rendelkezésre állását DRM technológia](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
A Media Services .NET SDK verziója most 3.1.0.1.

Ebben a kiadásban az alapértelmezett Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate konstruktor elavultak jelölésű. Az új konstruktor készít TokenType argumentumként.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>2014. decemberi kiadása
### <a name="general-media-services-updates"></a>Általános Media Services frissítése
* A Media Indexer hozzáadott néhány frissítéseket és új szolgáltatásokat. További információkért lásd: [Azure Media Indexer verzió 1.1.6.7 kibocsátási megjegyzések](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Egy új REST API lett hozzáadva, használható kódolási fenntartott egységek frissítéséhez. További információkért lásd: [EncodingReservedUnitType többi](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* A CORS támogatást kaptak a kulcs kézbesítési szolgáltatás.
* Teljesítménnyel kapcsolatos fejlesztések történtek engedélyezési házirend-beállítások lekérdezése.
* A kínai adatközpontban a [kézbesítési URL-cím kulcs](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) már egy ügyfél (akárcsak más adatközpontokban).
* HLS automatikus cél időtartama hozzá lett adva. Ennek az élő Stream továbbítása során, amikor HLS mindig dinamikusan csomagolni. A Media Services alapértelmezés szerint automatikusan HLS szegmens csomagolás aránya (FragmentsPerSegment) a keyframe időköz (KeyFrameInterval) alapján számítja ki. Ez a módszer is nevezzük képek (GOP) csoportja, amely az élő kódoló érkezik. További információkért lásd: [a Media Services használata élő adatfolyam](http://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
A [Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) verziója most 3.1.0.0-s. A következő frissítéseket történtek:

* A .NET-keretrendszer 4.5-ös verziójának a .NET SDK-függőség lett frissítve.
* Egy olyan új API kódolási fenntartott egységek frissítésére használható hozzá lett adva. További információkért lásd: [frissítés szolgáltatás számára fenntartott egység típusát és a kódoláshoz fenntartott egység .NET használatával növelje](media-services-dotnet-encoding-units.md).
* JWT jogkivonat hitelesítés támogatása hozzá lett adva. További információkért lásd: [JWT jogkivonat hitelesítési a Media Services és a dinamikus titkosítás](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* A PlayReady licencsablon a BeginDate és ExpirationDate relatív eltolások lettek hozzáadva.

## <a id="november_changes_14"></a>2014. novemberi kiadásban
* Most már használhatja a Media Services élő Smooth Streaming (fMP4) tartalmat tölti be SSL-kapcsolaton keresztül. Betöltési SSL-en keresztül, győződjön meg arról, hogy a bemeneti URL-címet frissíteni HTTPS. Jelenleg az Media Services nem támogatja az SSL az egyéni tartomány. További információ az élő Stream továbbítása: [együttműködik az Azure Media Services élő adatfolyam-továbbítási](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* Jelenleg egy RTMP élő adatfolyam nem betöltési SSL-kapcsolaton keresztül.
* SSL-en keresztül adatfolyam csak akkor, ha a streamvégpontján, amelyről a tartalmat továbbít a 2014. szeptember 10 után lett létrehozva. Ha a streamelési URL-címek a 2014. szeptember 10 után létrehozott streamvégpontok alapuló "streaming.mediaservices.windows.net" (a új formátum) tartalmaz. Adatfolyam-továbbítási URL-címek, amelyek tartalmazzák a "origin.mediaservices.windows.net" (a régi formátumot) nem támogatja az SSL. Ha a régi formátumban kell megadni az URL-cím és adatfolyamként küldje el SSL,-en keresztül szeretné [hozzon létre egy új streamvégpont](media-services-portal-manage-streaming-endpoints.md). Az adatfolyamként SSL-en keresztül, használja az URL-címek alapján új streamvégpontra.

## <a id="october_changes_14"></a>2014. októberi kiadás
### <a id="new_encoder_release"></a>Media Services kódoló kiadás
 A Media Services Azure Media Encoder új kiadásának jelentették. A legújabb Media Encoder meg van szó, csak a kimeneti GB-ban. Ellenkező esetben az új kódoló egy olyan szolgáltatás, amely kompatibilis a korábbi kódoló. További információkért lásd: [A Media Services díjszabásával kapcsolatos részletek].

### <a id="oct_sdk"></a>Media Services .NET SDK-val
A Media Services SDK .NET-bővítmények verziója most 2.0.0.3.

A Media Services SDK for .NET verziója most 3.0.0.8. A következő frissítéseket történtek:

* Újrapróbálkozási házirend osztályok újrabontása lett megvalósítva.
* A felhasználói ügynök karakterlánca HTTP-kérelmek fejléceinek lett adva.
* A NuGet visszaállítási összeállítása lépés jelent meg.
* Forgatókönyv tesztek javítva lett x509 használandó tanúsítvány számára a tárházban.
* Érvényesítési beállítások hozzá voltak adva a során a csatorna és a streamelési célból.

### <a name="new-github-repository-to-host-media-services-samples"></a>Új GitHub tárház host Media Services-minták
A mintákat a [Media Services minták GitHub-tárházban](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>2014. szeptember kiadás
A Media Services REST metaadatok verziója most 2.7. A többi legújabb kapcsolatos további információkért tekintse meg a [Media Services REST API-referenciában](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

A Media Services SDK for .NET verziója most 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Módosítások megszakítása
* Forrás nevet viseli [StreamingEndpoint].
* A módosítás az alapértelmezett viselkedés kódolására, és tegye közzé az MP4-fájlok az Azure-portálon használatakor.

### <a id="sept_14_GA_changes"></a>Új funkciók/forgatókönyvek, amelyek általánosan rendelkezésre álló kiadásában
* A Media Indexer media processzor jelent meg. További információkért lásd: [médiafájlok a Media Indexer az Index](http://msdn.microsoft.com/library/azure/dn783455.aspx).
* Használhatja a [StreamingEndpoint] entitás hozzáadása egyéni tartománynevek (gazda).
  
    Egy egyéni tartománynevét használja, a Media Services adatfolyam végpont nevét, adja hozzá egyéni állomásnevet a streamvégpontra. A Media Services REST API-k vagy a .NET SDK használatával adja hozzá az egyéni állomásnevet.
  
    A következők érvényesek:
  
  * Az egyéni tartománynév tulajdonjogát kell rendelkeznie.
  * Media Services által is ellenőrizni kell a tartománynév tulajdonjogát. A tartomány ellenőrzéséhez hozzon létre egy CNAME rekordot, amely leképezhető a MediaServicesAccountId szülőtartomány DNS-mediaservices-dns-zóna ellenőrzése.
  * Létre kell hoznia egy másik olyan CNAME rekordot, amely az egyéni állomásnevet (például sports.contoso.com) van leképezve a Media Services StreamingEndpoint gazdagép nevét (például amstest.streaming.mediaservices.windows.net).

    További információkért lásd: a CustomHostNames tulajdonságot a [StreamingEndpoint](http://msdn.microsoft.com/library/azure/dn783468.aspx) cikk.

### <a id="sept_14_preview_changes"></a>Új funkciók/forgatókönyvek a nyilvános előzetes kiadás részét képező
* Élő adatfolyam-továbbítási minta. További információkért lásd: [a Media Services használata élő adatfolyam](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* Kulcs kézbesítési szolgáltatás. További információkért lásd: [a dinamikus titkosítás használata AES-128 és a kulcs kézbesítési szolgáltatás](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* A dinamikus titkosítás AES. További információkért lásd: [a dinamikus titkosítás használata AES-128 és a kulcs kézbesítési szolgáltatás](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* PlayReady-licenctovábbítási szolgáltatásra. 
* PlayReady-titkosítás dinamikus. 
* Media Services PlayReady licenc sablonja. További információkért lásd: a [A Media Services PlayReady licencsablon áttekintése].
* Adatfolyam tárolási titkosított eszközökre. További információkért lásd: [adatfolyam formájában a tartalmat tároló titkosított](http://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Augusztus 2014 kiadásban
Amikor kódolása egy eszköz, a kódolási feladat befejezésekor egy kimeneti eszköz hozott létre. Ebben a kiadásban, amíg a Media Services kódoló előállított kimeneti eszközökre vonatkozó metaadatok. Jelen kiadástól kezdve a kódoló is eredményez bemeneti eszközökre vonatkozó metaadatok. További információkért lásd: [Bemeneti metaadatok] és [kimeneti metaadatok].

## <a id="july_changes_14"></a>2014. július kiadás
A következő hibajavításokat tartalmaz az Azure Media Services csomagoló és titkosító történtek:

* Amikor egy élő archív eszköz átkerülnek a HLS, csak hang lejátssza: Ez a hiba kijavítása volt, és most hang- és a videó lejátszása is.
* Ha egy eszköz HLS és 128 bites AES envelope titkosítási van csomagolva, a csomagolt adatfolyamok nem játssza le az Android-eszközökön: A hiba volt rögzített, és a csomagolt adatfolyam lejátssza Android-eszközökön, amelyek támogatják a HLS.

## <a id="may_changes_14"></a>Előfordulhat, hogy a 2014 kiadásban
### <a id="may_14_changes"></a>Általános Media Services frissítése
Ezután már használhatja [dinamikus becsomagolás] adatfolyam HLS 3-as verziójú. Az adatfolyam HLS 3-as verzió, vegye fel a következő formátumban a lokátor elérési útját: * .ism/manifest(format=m3u8-aapl-v3). További információkért lásd: [ezen a fórumon](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

A dinamikus csomagolás most is támogatja kézbesítéséhez Smooth Streaming PlayReady statikusan titkosított alapján PlayReady titkosított HLS (3-as verziójú és 4-es verzió). A PlayReady Smooth Streaming titkosításának információkért lásd: [Smooth Streaming védelme a Playreadyvel](http://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK frissítések
A Media Services .NET SDK verziója most 3.0.0.5. A következő frissítéseket történtek:

* Sebesség és a rugalmasság is jobb, ha a feltöltés, és töltse le az adathordozó eszközök.
* Újrapróbálkozási logika és átmeneti kivételkezelést fejlődésének volt: 
  
  * Átmeneti hiba felderítését és újrapróbálkozási logika kifejlesztett lekérdezni, mentse a módosításokat, és töltse fel vagy fájlok letöltése okozott kivételekhez. 
  * Amikor webes kivételek (például egy hozzáférés-vezérlési szolgáltatásban jogkivonatkérelem) során, végzetes hibák sikertelen gyorsabban most.

További információkért lásd: [újrapróbálkozási logika a Media Services SDK for .NET].

## <a id="april_changes_14"></a>2014. április kódoló kiadás
### <a name="april_14_enocer_changes"></a>Media Services kódoló frissítések
* Támogatást kaptak a fű Valley EDIUS lineáris szerkesztő használatával készült AVI fájlokat tölti be. A folyamat a videó enyhén tömörített a fű Valley HQ/HQX kodek használatával. További információkért lásd: [fű Valley időről a felhőn keresztül streaming EDIUS 7].
*  Támogatást kaptak a Media Services-kódoló által a fájlok elnevezési megadásához. További információkért lásd: [vezérlő Media Services kódoló kimeneti fájl nevének](http://msdn.microsoft.com/library/azure/dn303341.aspx).
*  Videó és/vagy a hang átfedések támogatást kaptak. További információkért lásd: [átfedések létrehozása](http://msdn.microsoft.com/library/azure/dn640496.aspx).
*  Támogatást kaptak a több egyszerre több video-szegmens. További információkért lásd: [videó szegmensek több](http://msdn.microsoft.com/library/azure/dn640504.aspx).
* Programhiba, volt az átkódolás MPEG-1 hang 3. rétegbeli (más néven MP3), ahol a hang kódolt MP4 lett rögzített.

## <a id="jan_feb_changes_14"></a>Feloldja a 2014. januári/február
### <a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 és 3.0.0.3
A módosítások 3.0.0.1 és 3.0.0.2 a következők:

* Az OrderBy utasítások a LINQ-lekérdezések használatát kapcsolatos problémák javítva lett.
* Tesztelheti a megoldások [GitHubon] egység alapján tesztek és a forgatókönyv-alapú tesztek felosztása volt.

A módosításokkal kapcsolatos további információkért lásd: a [feloldja a Media Services .NET SDK 3.0.0.1 és 3.0.0.2](http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/).

A következő változtak 3.0.0.3 verzióban:

* Azure storage-függőségek frissített 3.0.3.0 verzióját használja.
* A visszamenőleges kompatibilitás érdekében hiba 3.0 javítását. *.* kiadását.

## <a id="december_changes_13"></a>2013 decembere kiadás
### <a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> A 3.0.x.x kiadásokban nincsenek 2.4.x.x kiadásainak visszamenőlegesen kompatibilis.
> 
> 

A Media Services SDK legújabb verziójára már 3.0.0.0. Töltse le a legfrissebb csomagot a Nugetből, vagy a bits az beszerzése [GitHubon].

A Media Services SDK verzió 3.0.0.0 verziótól kezdődően felhasználhatja a [Azure AD-hozzáférés-vezérlési szolgáltatásban](http://msdn.microsoft.com/library/hh147631.aspx) jogkivonatokat. További információkért lásd: "Újbóli hozzáférés-vezérlési szolgáltatásban tokenek" szakasz a [kapcsolódás a Media Services a .NET-keretrendszerhez készült Media Services SDK-t](http://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK-bővítmények 2.0.0.0-s
 A Media Services .NET SDK-bővítmények olyan kiegészítő módszerek és segédfüggvények találhatók, amelyek egyszerűbbé teszik a kódolást és megkönnyítik a Media Services fejlesztéséhez. A legújabb bits kaphat [Media Services .NET SDK-bővítmények](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>A 2013. novemberi kiadásban
### <a name="nov_13_donnet_changes"></a>Media Services .NET SDK változások
Ebben a verzióban a Media Services SDK .NET leírók átmeneti hiba hibáit, amely jelentkezhet, ha a Media Services REST API-réteghez hívások indítása.

## <a id="august_changes_13"></a>Augusztus 2013 kibocsátási
### <a name="aug_13_powershell_changes"></a>Azure SDK tools Media Services PowerShell parancsmagok
Most már szerepel a következő Media Services PowerShell-parancsmagok [Azure SDK tools](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Például:`Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Például:`New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Például:`New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Például:`Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>2013. június kiadás
### <a name="june_13_general_changes"></a>A Media Services változások
A 2013. június Media Services kiadásokban szereplő frissítések érhetők el ebben a szakaszban említett a következő módosításokat:

* Lehetővé teszi több storage-fiókok összekapcsolása egy Media Services-fiókját. 
    * Tárfiók
    * Asset.StorageAccountName és Asset.StorageAccount
* Job.Priority frissítésének lehetőségét. 
* Értesítési kapcsolatos entitásokat és tulajdonságok: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Feladat
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Media Services .NET SDK változások
A következő módosításokat-e adva a júniusi 2013 a Media Services SDK kiadását. A legújabb Media Services SDK a Githubon érhető el.

* A Media Services SDK-t támogatja, több tároló linking fiókok 2.3.0.0 verziójától kezdve, a Media Services-fiók. A következő API-kat támogatja ezt a szolgáltatást:
  
    * IStorageAccount típusa
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts property
    * StorageAccount tulajdonság
    * StorageAccountName tulajdonság
  
    További információkért lásd: [Media Services kezelése eszközök több tárfiókok között](http://msdn.microsoft.com/library/azure/dn271889.aspx).
* Értesítési kapcsolatos API-k. 2.2.0.0 verziójától kezdve, hogy figyelheti az Azure Queue storage értesítésekre. További információkért lásd: [kezelni a Media Services feladat értesítések](http://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions tulajdonság
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint típusa
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription típusa
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection típusa
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType típusa
* A tárolási ügyfél SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll) függőség
* Függőség OData 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>2012. decemberi kiadása
### <a name="dec_12_dotnet_changes"></a>Media Services .NET SDK változások
* IntelliSense: Számos különböző hiányzik az IntelliSense dokumentáció kaptak.
* Microsoft.Practices.TransientFaultHandling.Core: Probléma volt rögzített ahol az SDK-t továbbra is rendelkezett egy régi verziója ezt a szerelvényt függőség. Az SDK-val most már hivatkozik a szerelvény 5.1.1209.1 verzióját.

A 2012. November SDK található probléma javítását:

* IAsset.Locators.Count: Ez a szám most már megfelelően jelentenek új IAsset felületek összes keresők törlését követően.
* IAssetFile.ContentFileSize: A most már megfelelően értéke feltöltés után IAssetFile.Upload(filepath) által.
* IAssetFile.ContentFileSize: Ez a tulajdonság most már beállítható egy eszköz-fájl létrehozásakor. Korábban csak olvasás.
* IAssetFile.Upload(filepath): Probléma volt rögzített ahol a szinkron feltöltés metódus lett kivétel a következő hiba Ha több fájl feltöltése az eszközhöz. A következő hiba történt a "kiszolgáló nem tudta hitelesíteni a kérelmet. Győződjön meg arról, hogy az engedélyezési fejléc értékének formátuma helyes többek között az aláírás."
* IAssetFile.UploadAsync: Probléma volt rögzített fájlok öt fájlok egyidejű feltöltésének korlátozott.
* IAssetFile.UploadProgressChanged: Az SDK-val most már biztosítja ezt az eseményt.
* (Karakterlánc, BlobTransferClient, ILocator, CancellationToken) IAssetFile.DownloadAsync: Ez most valósul meg.
* IAssetFile.DownloadAsync: Probléma volt rögzített korlátozott öt fájl a fájlok az egyidejű letöltését.
* IAssetFile.Delete(): Probléma volt rögzített ahol hívó delete előfordulhat, hogy kivételt jelez, ha nincs fájl feltöltése a IAssetFile a.
* Feladatok: Probléma volt rögzített ahol feladat sablonnal egy "MP4 zökkenőmentes adatfolyamok tevékenységhez" "PlayReady védelmi feladatokkal" láncolás nem hozott létre minden feladatot minden.
* EncryptionUtils.GetCertificateFromStore(): Ez a módszer nullhivatkozás okozta kivétel alapuló tanúsítvány konfigurációs problémák keresésében egy hiba miatt már nem jelez.

## <a id="november_changes_12"></a>2012. november kiadás
Ebben a szakaszban említett módosításra a 2012. November (verzió: 2.0.0.0-s) szereplő frissítés SDK. Ezeket a módosításokat a 2012. júniusi Preview SDK módosulnak vagy írni kibocsátási írásbeli kódok lehet szükség.

* Objektumok
  
    * IAsset.Create(assetName) van a *csak* eszköz létrehozása funkciót. IAsset.Create már nem része a metódushívás fel a fájlokat. Használjon IAssetFile feltöltése.
    * A IAsset.Publish és a AssetState.Publish számbavételi érték az SDK-t eltávolítottak. Ez az érték a ügyfélkulcshoz tartozó kódok kell írni.
* FileInfo
  
    * Ez az osztály eltávolították, majd IAssetFile cserélve.
  
* IAssetFiles
  
    * IAssetFile FileInfo váltja fel, és egy másik viselkedik. A használatához objektumpéldányt a IAssetFiles keresztül a Media Services SDK vagy a Storage szolgáltatás SDK fájlfeltöltés követ. A következő IAssetFile.Upload túlterhelések használhatók:
  
        * IAssetFile.Upload(filePath): A szinkron módszer letiltja a szál, és ajánlott csak egyetlen fájl feltöltésekor.
        * IAssetFile.UploadAsync (fájl elérési útja, blobTransferClient, lokátor, cancellationToken): az aszinkron metódus az előnyben részesített feltöltési módot. 
    
            Ismert hiba: a megszakítási jogkivonatot használja, ha a feltöltés megszakadt. A feladatok cancellation hány állapota lehet. Meg kell megfelelően dolgozza fel, és kivételek tud kezelni.
* Keresők
  
    * A forrás-specifikus verziójával eltávolítva. A SAS-specifikus környezetben. (Eszköz, accessPolicy) Locators.CreateSasLocator általánosan rendelkezésre álló által eltávolított vagy elavult lesznek megjelölve. Az "Új funkciókat" frissített viselkedését a "Keresők" című szakaszában talál.

## <a id="june_changes_12"></a>2012. júniusi előzetes kiadás
Az alábbi funkciókat lett az SDK. novemberi kiadásában új:

* Entitások törlése
  
    * IAsset, IAssetFile, ILocator, IAccessPolicy és IContentKey objektumok objektumszinten, ez azt jelenti, hogy IObject.Delete() ahelyett, hogy a gyűjteményben, ez azt jelenti, hogy cloudMediaContext.ObjCollection.Delete(objInstance) törlési most törlődnek.
* Keresők
  
    * Keresők most már léteznie kell a CreateLocator módszer használatával. Argumentumként a lokátor típusát szeretne létrehozni a LocatorType.SAS vagy LocatorType.OnDemandOrigin számbavételi értéket kell használniuk.
    * Új tulajdonságok használatával könnyebben használható URI-azonosítók a tartalmat az beszerzése keresők hozzáadott. A keresők újratervezése nagyobb rugalmasságot biztosít a későbbi külső bővítési, és növeli a könnyen használható a media ügyfélalkalmazások.
* Aszinkron metódus támogatása
  
    * Aszinkron támogatást kaptak a módszerek.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Az Azure Media Services MSDN fórum]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Az Azure Media Services REST API-referencia]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[A Media Services díjszabásával kapcsolatos részletek]: http://azure.microsoft.com/pricing/details/media-services/
[Bemeneti metaadatok]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Kimeneti metaadatok]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[A Media Services PlayReady licencsablon áttekintése]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[dinamikus becsomagolás]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Újrapróbálkozási logika a Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Fű Valley időről a felhőn keresztül streaming EDIUS 7]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: http://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHubon]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx

