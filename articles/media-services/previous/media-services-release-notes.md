---
title: A Media Services kibocsátási megjegyzések |} A Microsoft Docs
description: A Media Services kibocsátási megjegyzései
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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 9370cdfc75995f36101804d2a9f7dc9cf275243c
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186310"
---
# <a name="azure-media-services-release-notes"></a>Az Azure Media Services kibocsátási megjegyzései
Ezek a kibocsátási megjegyzések az Azure Media Services összefoglalója változásokat a korábbi kiadások és ismert problémáit.

> [!NOTE]
> Szívesen meghallgatnánk az ügyfeleink, így Ön érintő problémák kijavítása összpontosíthatunk. Probléma jelentéséhez vagy tegyen fel kérdéseket, küldje el a hozzászólás a [Az Azure Media Services MSDN-fórum].
> 
> 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Ismert problémák
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>A Media Services általános problémák

| Probléma | Leírás |
| --- | --- |
| Számos gyakori HTTP-fejlécek nem biztosított a REST API-ban. |A Media Services-alkalmazások fejlesztése a REST API-val, ha úgy gondolja, hogy néhány általános HTTP-fejléc mezők (beleértve a CLIENT-REQUEST-ID REQUEST-ID és a VISSZAADANDÓ-CLIENT-REQUEST-ID) nem támogatottak. A fejlécek, egy következő frissítés fog bővülni. |
| %-Os-kódolást nem engedélyezett. |A Media Services a IAssetFile.Name tulajdonság értékét használja, URL-címek létrehozását, a streamelési tartalom (például `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). Ebből kifolyólag százalék-kódolást nem engedélyezett. A Name tulajdonság értéke nem lehet a következő [százalék-kódolás – fenntartott karakterek](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Ezenkívül lehetnek csak egy "." a fájlnév kiterjesztésével. |
| A ListBlobs metódust, amely része az Azure Storage SDK-verzió 3.x sikertelen lesz. |A Media Services SAS URL-címek alapján hoz létre a [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) verzió. Ha szeretne egy blob-tárolóban lévő blobok listázása a Storage SDK-val, használja a [CloudBlobContainer.ListBlobs](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) módszer, amely része a Storage SDK-verziója 2.x. |
| A Media Services mechanizmus szabályozás korlátozza az alkalmazásokat, amelyek a szolgáltatás túl sok kérést az erőforrás-használati. A szolgáltatás a "Szolgáltatás nem érhető el" 503-as HTTP-állapotkód: Előfordulhat, hogy vissza. |További információkért lásd az 503-as HTTP-állapotkód leírását [Media Services-hibakódok](media-services-encoding-error-codes.md). |
| Ha entitást lekérdezni, legfeljebb 1000 entitások adja vissza egy időben, mert a nyilvános REST 2-es verzió korlátozza az 1000 eredmények lekérdezési eredményeket. |Kihagyás és igénybe (.NET) / (REST) leírtak szerint az első [ebben a példában .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [a REST API-példa](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Egyes ügyfelek között egy ismételje meg a címke a probléma a Smooth Streaming-jegyzékfájlban származhatnak. |További információkért lásd: [ebben a szakaszban](media-services-deliver-content-overview.md#known-issues). |
| Media Services .NET SDK-objektumok nem szerializálható, és emiatt nem működnek az Azure Cache redis. |Ha az SDK AssetCollection objektumot hozzáadása az Azure Cache redis, akkor a program kivételt vált ki. |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>REST API-korábbi verziók
A Media Services REST API-korábbi verziók kapcsolatos információkért tekintse meg a [Az Azure Media Services REST API-referencia].

## <a name="october-2018"></a>2018. október

### <a name="cmaf-support"></a>CMAF támogatása

CMAF és "cbcs" titkosítás támogatása (iOS 11 +) Apple HLS és MPEG-DASH, amelyek támogatják a CMAF lejátszók.

### <a name="web-vtt-thumbnail-sprites"></a>Webes VTT miniatűr sprites

Mostantól használhatja a Media Services webes VTT miniatűr sprites a v2 API-k használatával létrehozni. További információkért lásd: [hozzon létre egy miniatűr sprite](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>2018. július

A szolgáltatás legújabb kiadással nincsenek kisebb a hibaüzenetek, amikor egy feladat sikertelen volt, vonatkozóan, hogyan azt van osztva két vagy több sort adja vissza a szolgáltatás a módosítások formázása.

## <a name="may-2018"></a>2018. május 

2018. május 12., az élő csatornák indítása fogja többé nem támogatja a RTP/MPEG-2 adatátviteli stream betöltési protokollját. Telepítse át a RTP/MPEG-2 RTMP vagy darabolt MP4 (Smooth Streaming) betöltési protokollokra.

## <a name="october-2017-release"></a>2017. októberi kiadás
> [!IMPORTANT] 
> A Media Services az Azure Access Control Service-hitelesítési kulcsok támogatásának kivezetése. 2018. június 22. már nem hitelesítheti a Media Services-háttérrendszer használatával kód Access Control Service kulcsok használatával. Az Azure Active Directory (Azure AD) használja a kód frissítenie kell egy [az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). Tekintse meg a figyelmeztetéseket erről a változásról, az Azure Portalon.

### <a name="updates-for-october-2017"></a>2017. októberi frissítések
#### <a name="sdks"></a>SDK-k
* A .NET SDK frissült az Azure AD-hitelesítés támogatásához. A legújabb .NET SDK gyorsabb áttelepítés az Azure ad-val ösztönzik a Nuget.org webhelyen az Access Control Service-hitelesítés támogatása el lett távolítva. 
* A JAVA SDK frissítve lett az Azure AD-hitelesítés támogatásához. Azure AD-hitelesítés támogatása hozzáadva a Java SDK-t. Információk a Java SDK használatának a Media Services: [a Java ügyfél-SDK használatának első lépései az Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Fájl alapú kódolás
* Most már használhatja a prémium szintű kódoló a tartalmat (HEVC) videó kodek kódolási H.265 nagy hatékonyságú videó kódolásához. Ha úgy dönt, H.265 keresztül más kodekek, például a H.264, nincs nincs ár hatását. HEVC szabadalmi licencekkel kapcsolatos információkért lásd: [Online szolgáltatások használati feltételeit](https://azure.microsoft.com/support/legal/).
* Forrás videó H.265 (HEVC) videó kodekkel, például az ios11-et vagy GoPro Hero 6, rögzített videó kódolt most már használhatja a prémium szintű kódoló vagy a standard szintű Encoder ezek a videók kódolásához. Szabadalmi licencekkel kapcsolatos információkért lásd: [Online szolgáltatások használati feltételeit](https://azure.microsoft.com/support/legal/).
* Több nyelv hangsáv tartalmazó tartalom a nyelv értékeket kell megfelelően neve alapján a megfelelő fájlformátum specifikációjának (például az ISO MP4). Ezután használhatja a standard szintű Encoder streamelési tartalmának kódolása. Az eredő a streamelési lokátorok hang elérhető nyelvek listája.
* A standard szintű Encoder mostantól támogatja a két új csak hangfájlt tartalmazó rendszer készleteket, "AAC hang" és "AAC jó minőségű hang." Speciális kódolási jelölik a (AAC) kimeneti 128 kb/s és 192 Kb/s átviteli díjakat hang sztereó mindkét termék.
* A prémium szintű kódoló mostantól támogatja az QuickTime/MOV fájlformátumok bemenetként. A videó kodek egyikének kell lennie a [Apple ProRes típusok a GitHub-cikkben felsorolt](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). A hang vagy AAC vagy impulzusos kód modulációs. A prémium szintű kódoló nem támogatja, például DVC/DVCPro videó bemenetként QuickTime/MOV fájlok burkolja. A standard szintű Encoder ezek videokodekek támogatja.
* A következő hibajavítások kódolók történtek:

    * Most már küldhet feladatokat egy bemeneti eszköz használatával. Miután ezek a feladatok, módosíthatja az eszköz (például hozzáadásához, törléséhez vagy fájlok belül az eszköz átnevezése), valamint további feladatok elküldéséhez.
    * A standard szintű Encoder által előállított JPEG miniatűrök minőségének fejlesztése
    * A Standard kódoló bemeneti metaadatok és a nagyon rövid időre videókban jobb miniatűrkészítés kezeli.
    * Fejlesztések a standard szintű Encoder használt H.264 dekódolónak történő átadása egyes ritka összetevők kiküszöbölése. 

#### <a name="media-analytics"></a>Media Analytics
Az Azure Media Redactor általános elérhetősége: Ez médiafeldolgozót anonimizálás elvégzi a arcának felismerhetetlenné, és ideális közbiztonsági megfontolásokból és sajtóbeli híradásoknál használható. 

Az új processzor áttekintéséhez lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/azure-media-redactor/). Dokumentáció és a beállítások kapcsolatos tudnivalókat lásd: [szereplők az Azure Media Analytics arcok](media-services-face-redaction.md).



## <a name="june-2017-release"></a>2017 június kiadás

A Media Services már támogatja [az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Jelenleg a Media Services támogatja az Access Control Service-hitelesítési modellre. 2018. június 1. Access Control Service engedélyezési elavulttá válik. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="march-2017-release"></a>2017. márciusi kiadás

Most már használhatja a standard szintű Encoder a [skála automatikus létrehozását](media-services-autogen-bitrate-ladder-with-mes.md) kódolási tevékenység létrehozásakor a karakterlánc a "adaptív Streamelés" előbeállítást megadásával. Kódolás a Media Services használatával folyamatos átviteli videó, használja a "Adaptív Streamelés" beállításkészletet. Testre szabhatja egy kódolási előbeállítást az adott forgatókönyvhöz, megkezdheti az [ezen készletek](media-services-mes-presets-overview.md).

Most már használhatja a Media Encoder Standard vagy a Media Encoder Premium munkafolyamat való [hozzon létre egy kódolási feladat által létrehozott fMP4 adattömbök](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>2017 február kiadás

2017. április 1-én kezdve a fiókjában 90 napnál régebbi feladat rekordot automatikusan törlődik, és a kapcsolódó bejegyzések. Törlés akkor fordul elő, akkor is, ha a rekordok száma nem éri a maximális kvótát. A feladatok/műveletek adatainak archiválásához használhatja a kód ismertetett [kezelése az eszközök és a kapcsolódó entitások a Media Services .NET SDK-val](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>2017 január kiadás

A Media Services szolgáltatásban a tartalomstreameléshez használt streamvégpont jelöli egy adatfolyam-szolgáltatást, amely tartalmat továbbít közvetlenül az ügyfél lejátszóalkalmazásába, vagy a content delivery network (CDN) további terjesztés céljából. Media Services az Azure Content Delivery Network zökkenőmentes integrációt is biztosít. A kimenő adatfolyam a Streamvégpontok szolgáltatás az élő stream, igény szerinti videó vagy egy progresszív letöltés, az eszköz a Media Services-fiók lehet. Minden egyes Media Services-fiók alapértelmezett streamvégpontja tartalmazza. További streamvégpontok a fiók alatt hozható létre. 

A streamvégpontot választanak, két verziója 1.0-s és 2.0-s. 2017. január 10., kezdve minden újonnan létrehozott Media Services-fiókok tartalmazzák a 2.0-s verziójának alapértelmezett streamvégpontot. Adja hozzá az ehhez a fiókhoz további streamvégpontok egyúttal 2.0-s verzióját. Ez a változás nem érinti a meglévő fiókok. Meglévő streamvégpontok 1.0-s és 2.0-s verziójának frissíthetők. Nincsenek viselkedés, a számlázással és az módosítása funkció módosításokat. További információk: [Streamvégpontok áttekintése](media-services-streaming-endpoints-overview.md).

A Media Services 2.15 verziójával kezdődően a streaming endpoint entitáshoz hozzáadva az alábbi tulajdonságokat:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Ezek a tulajdonságok további információkért lásd: [Streamvégpontok](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>2016. december kiadás

 Most már használhatja a Media Services telemetriai adatok és metrikák adatok elérését a szolgáltatásokhoz. Aktuális verzióját használja, a Media Services élő csatorna telemetriai adatainak gyűjtéséhez streamvégpontot, és archiválja az entitások. További információkért lásd: [Media Services telemetriai](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>2016. júliusi kiadás
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Frissítések az Alkalmazásjegyzék-fájl (*. Kódolási feladatok által generált ISM)
Media Encoder Standard vagy prémium szintű Media Encoder egy kódolási feladat elküldésekor a kódolási feladat létrehoz egy [streamelési jegyzékfájlt](media-services-deliver-content-overview.md) (* .ism) a kimeneti adategység. A szolgáltatás legújabb kiadással a streamelési jegyzékfájlt szintaxisa a következő frissítve lett.

> [!NOTE]
> A streamelési jegyzékfájlt (.ism) fájl szintaxisa a következő belső használatra van fenntartva. Fontos a jövőbeni kiadásokban változhat. Ne módosítsa vagy a fájl tartalmának módosítására.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Egy új ügyfél-jegyzékfájlt (*. ISMC) fájl jön létre a kimeneti adategység, ha a kódolási tevékenység kimenete egy vagy több változó bitsebességű MP4-fájlokat
Egy vagy több MP4-fájlokat hoz létre kódolási tevékenység befejezése után a legújabb szolgáltatásverzió kiadása, kezdve a kimeneti objektum is tartalmaz ügyfél streamelési jegyzékfájlt (*.ismc) fájl. A .ismc fájl segítségével dinamikus streamelési teljesítményének növelésében. 

> [!NOTE]
> Az ügyfél-jegyzékfájlt (.ismc) fájl szintaxisa a következő belső használatra van fenntartva. Fontos a jövőbeni kiadásokban változhat. Ne módosítsa vagy a fájl tartalmának módosítására.
> 
> 

További információt [ebben a blogban](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) talál.

### <a name="known-issues"></a>Ismert problémák
Egyes ügyfelek között egy ismételje meg a címke a probléma a Smooth Streaming-jegyzékfájlban származhatnak. További információkért lásd: [ebben a szakaszban](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>2016. április kiadás
### <a name="media-analytics"></a>Media Analytics
 A Media Services Media Analytics hatékony videó intelligens bevezetni. További információkért lásd: [Médiaelemzés-szolgáltatások – áttekintés](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Az Apple FairPlay (előzetes verzió)
Most már használhatja a Media Services dinamikus titkosítást a HTTP Live Streaming (HLS) az Apple fairplay által tartalom. A Media Services licenctovábbítási szolgáltatása FairPlay-licenc továbbítására az ügyfelek is használhatja. További információkért lásd: "Az Azure Media Services Apple fairplay által védett HLS-tartalmak streamelésére használható."

## <a id="feb_changes16"></a>2016. február kiadás
A Media Services SDK for .NET (3.5.3) legújabb verzióját a Google Widevine kapcsolatos hibajavítás tartalmazza. Nem lehet újból felhasználja a Widevine titkosítása adategységek AssetDeliveryPolicy volt. Az SDK-t a hibajavítás részeként a következő tulajdonság lett hozzáadva: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>2016. január kiadás
A kódoláshoz fenntartott egységek lettek átnevezve kódoló nevekkel elkerülése érdekében.

A Basic, Standard és Premium kódoláshoz fenntartott egységek az S1, S2, is átnevezheti, és S3 szintű fenntartott egység, illetve. Ügyfelek, akik alapszintű kódoláshoz fenntartott egységek használatáért S1 látja, mint a címke az Azure Portalon (és a számlán). Standard és prémium szintű felhasználók tekintse meg a címkéket, S2 és S3, jelölik. 

## <a id="dec_changes_15"></a>2015 decemberi kiadása

### <a name="media-encoder-deprecation-announcement"></a>Media Encoder elavulási értesítés

 Media Encoder kezdve a Media Encoder Standard kiadása körülbelül 12 hónapon belül megszűnnek.

### <a name="azure-sdk-for-php"></a>Azure SDK a PHP-hoz
Az Azure SDK csapata közzétett új kiadása a [php-hez készült Azure SDK-t](http://github.com/Azure/azure-sdk-for-php) csomagot, amely a Media Services frissítéseit és új funkcióit tartalmazza. Ilyen például a Media Services php SDK mostantól támogatja a legújabb [content protection szolgáltatása](media-services-content-protection-overview.md) funkciókat. Ezeket a funkciókat a dinamikus titkosítás, az AES és DRM (PlayReady és Widevine), a és a jogkivonat-korlátozások nélkül. Ezenkívül támogatja skálázás [kódolási egységek](media-services-dotnet-encoding-units.md).

További információkért lásd:

* A következő [Kódminták](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) segítenek a gyors kezdéshez:
  * **vodworkflow_aes.php**: Ez a PHP-fájl bemutatja, hogyan használhatja a dinamikus AES-128-titkosítás és a kulcstovábbítást. A .NET-minta leírtak alapján [használata AES-128, a dinamikus titkosítás és a kulcstovábbítást](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Ez a PHP-fájl bemutatja, hogyan használhatja a dinamikus titkosítást a PlayReady és a licenctovábbítási szolgáltatása. A .NET-minta leírtak alapján [használja a PlayReady és/vagy Widevine dynamic common Encryption titkosítás](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Ez a PHP-fájl bemutatja a kódoláshoz fenntartott egységek méretezése.

## <a id="nov_changes_15"></a>2015 novemberi kiadásban
 A Media Services kínál a Widevine license delivery szolgáltatás a felhőben. További információt [ebben a blogban](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/) talál. Lásd még [ebben az oktatóanyagban](media-services-protect-with-playready-widevine.md) és a [GitHub-adattár](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Media Services által kínált Widevine licenctovábbítási szolgáltatások vannak előzetes verzióban érhető el. További információt [ebben a blogban](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/) talál.

## <a id="oct_changes_15"></a>2015. október kiadás
A Media Services jelenleg élő a következő adatközpontokban: Dél-Brazília, Nyugat-India, Dél-India és közép-India. Most már használhatja az Azure Portalon [Media Services-fiókok létrehozása](media-services-portal-create-account.md) leírt különböző feladatok elvégzésére, és a [Media Services dokumentációs weblapon](https://azure.microsoft.com/documentation/services/media-services/). Ezekben az adatközpontokban a Live Encoding nincs engedélyezve. További nem minden típusú kódoláshoz fenntartott egység érhető el ezekben az adatközpontokban.

* Dél-Brazília:                                          Csak a Standard és alapszintű kódoláshoz fenntartott egység érhető el.
* Nyugat-India, Dél-India és közép-India:             Csak alapszintű kódoláshoz fenntartott egységek érhetők el.

## <a id="september_changes_15"></a>2015 szeptemberi kiadás
Media Services mostantól lehetővé teszi az igény szerinti és élő adatfolyamok moduláris Widevine DRM-technológiával is védeni. A következő kézbesítési szolgáltatások partnerek segítségével Widevine-licencek segítségével:
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

További információt [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) talál.
  
Az AssetDeliveryConfiguration Widevine használatára történő beállításához használja a [Media Services .NET SDK-t](https://www.nuget.org/packages/windowsazure.mediaservices/) (a 3.5.1-es vagy egy újabb verziót), vagy a REST API-t. 
* A Media Services Apple ProRes videók támogatása. A forrás QuickTime-videók Apple ProRes vagy más kodekek használó fájlok most már feltöltheti. További információt [ebben a blogban](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/) talál.
* Mostantól használhatja a Media Encoder Standard részklipkészítés és élő archívum kibontása tennie. További információt [ebben a blogban](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/) talál.
* Az alábbi szűrési frissítések történtek: 
  
  * Mostantól használhatja az Apple HLS formátumban csak szűrővel rendelkező. Ez a frissítés segítségével távolítsa el a csak hangfájlt tartalmazó követése megadásával (csak = false) URL-címét.
  * Ha szűrőket definiálhat az eszközök, most kombinálható több (legfeljebb három) szűrőt egy egyetlen URL-címben.
    
    További információt [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) talál.
* A Media Services már támogatják e-keretek HLS 4-es verzióját. E-keret támogatási előretekerés és visszatekerés műveletek optimalizálja. Alapértelmezés szerint minden HLS-4. verziójú kimenetek közé tartozik az e-keret lista (EXT-X-I-FRAME-STREAM-INF).
További információt [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) talál.

## <a id="august_changes_15"></a>2015 augusztus kiadás
* A Media Services SDK a Java 0.8.0. verzió kiadását és az új mintát, mostantól elérhetők. További információkért lásd:
    
* Az Azure Media Player frissítve lett a többszörös hang Streamek támogatását. További információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>2015 júliusi kiadás
* Az általánosan elérhető Media Encoder Standard mutattuk be. További információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Standard szintű Media Encodert használja a készletek, leírtak szerint [ebben a szakaszban](https://go.microsoft.com/fwlink/?LinkId=618336). A készletet használhatja a 4K kódol, a prémium szintű szolgáltatás számára fenntartott egység típusának lekérése. További információkért lásd: [méretezési kódolás](media-services-scale-media-processing-overview.md).
* A Media Services és a Media Player élő valós idejű feliratok használták. További információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK már 3.4.0.0 verzió. Az alábbi frissítések történtek: 

* Az élő archívumot volt implementálva. Az élő archívumot tartalmazó objektumot nem lehet letölteni.
* A dinamikus szűrőt volt implementálva.
* Funkcióit, hogy a felhasználók megtarthatja a tárolót, amíg az eszköz törlését a lett megvalósítva.
* Hibajavítások kapcsolatos újrapróbálkozási szabályzatok csatornákon keresztül történtek.
* Media Encoder Premium munkafolyamat engedélyezve lett.

## <a id="june_changes_15"></a>2015 június kiadás
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK már 3.3.0.0 verzió. Az alábbi frissítések történtek: 

* Az OpenId Connect-felderítési specifikációja váltak.
* Az identity provider oldalán kulcsok váltása váltak.

Ha használhatja, amely közzéteszi az OpenID Connect-felderítési dokumentum Identitásszolgáltatóként (Azure AD-ben, a Google vagy a Salesforce végre), utasíthatja JSON webes jogkivonatainak (JWTs) érvényesítése aláírási kulcsokat lekérését az OpenID Connect-felderítési feldolgozása a Media Services. 

További információkért lásd: [használható JSON webes kulcsok a JWT-hitelesítéssel, a Media Services működéséhez az OpenID Connect felderítési specifikációja](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>2015 május kiadás
A következő új funkciók lettek bejelentett:

* [Valós idejű kódolás a Media Services előnézete](media-services-manage-live-encoder-enabled-channels.md)
* [A dinamikus manifest](media-services-dynamic-manifest-overview.md)
* [Előzetes verziója az Azure Media Hyperlapse médiafeldolgozót.](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>2015 áprilisi kiadás
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
* [A Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) mutattuk be.
* A Media Services REST 2.10 kezdve tárterületeket, hogy a valós idejű üzenetkezelési protokollt (RTMP) jönnek létre az elsődleges és másodlagos betöltési URL-címeket. További információkért lásd: [csatorna betöltési konfigurációk](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Az Azure Media Indexer frissítve lett.
* Spanyol nyelvű támogatása hozzáadva.
* Az XML-formátum egy új konfigurációt jelent meg.

További információt [ebben a blogban](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/) talál.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK már 3.2.0.0 verzió. Az alábbi frissítések történtek:

* Kompatibilitástörő változás: TokenRestrictionTemplate.Issuer és TokenRestrictionTemplate.Audience módosult egy karakterlánc típusú.
* Frissítések kapcsolódó hozhat létre egyéni újrapróbálkozási szabályzatok történtek.
* Hibajavítások történtek kapcsolódó, fel- és fájlok letöltéséhez.
* A MediaServicesCredentials osztály már elfogad hitelesíti a rendszer az elsődleges és másodlagos hozzáférés-vezérlési végpontokról.

## <a id="march_changes_15"></a>2015. márciusi kiadás
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
* A Media Services most már a Content Delivery Network-integrációt biztosít. Streamvégpontok támogatja az integrációt, a CdnEnabled tulajdonság lett hozzáadva. CdnEnabled használható REST API-k 2.9-es verzióval kezdődően. További információkért lásd: [Streamvégpontok](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled a .NET SDK használatával 3.1.0.2 verziótól kezdve használható. További információkért lásd: [Streamvégpontok](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* A Media Encoder Premium munkafolyamat mutattuk be. További információkért lásd: [Introducing Premium encoding, az Azure Media Servicesben](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>2015 február kiadás
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
A Media Services REST API már 2.9-es verzió. Ezen verziójával kezdődően a Content Delivery Network integráció streamvégpontok is engedélyezheti. További információkért lásd: [Streamvégpontok](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>2015 januári kibocsátási
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
Az általánosan elérhető a dinamikus titkosítás segítségével a content protection mutattuk be. További információkért lásd: [Media Services fokozza a streamelési biztonságot az általánosan elérhető a DRM-technológia](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK már 3.1.0.1 verzió.

Ebben a kiadásban az alapértelmezett Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate konstruktor elavultak megjelölve. Az új konstruktort TokenType argumentumként vesz igénybe.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>2014. decemberi kiadása
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
* A Media Indexer hozzáadott néhány frissítéseit és új funkcióit. További információkért lásd: [Azure Media Indexer 1.1.6.7 verzió kibocsátási megjegyzései](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Egy új REST API hozzá lett adva, hogy a kódoláshoz fenntartott egységek frissítéséhez használhatja. További információkért lásd: [REST-tel EncodingReservedUnitType](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* CORS-támogatás a kulcskézbesítési szolgáltatás lett hozzáadva.
* Teljesítménnyel kapcsolatos fejlesztések történtek engedélyezésiházirend-beállítások lekérdezéséhez.
* Kína adatközpontban a [kulcs kézbesítési URL-CÍMÉT](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) már minden ügyfél (hasonlóan más adatközpontokban).
* HLS automatikus cél időtartam lett hozzáadva. Élő streameléskor, HLS mindig dinamikusan csomagolva. A Media Services alapértelmezés szerint automatikusan HLS szegmens csomagolási arány (FragmentsPerSegment) a kulcsképkocka időköz (KeyFrameInterval) alapján számítja ki. Ez a módszer is nevezik képek (Képcsoporttal) csoportja, amely az élő kódoló érkezik. További információkért lásd: [együttműködnek a Media Services élő streamelési](https://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-frissítések
A [Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) már 3.1.0.0-s verzió. Az alábbi frissítések történtek:

* A .NET-keretrendszer 4.5-ös verziója a .NET SDK-függőség lett frissítve.
* Egy új API-t használó frissítse a kódoláshoz fenntartott egységek hozzá lett adva. További információkért lásd: [frissítési szolgáltatás számára fenntartott egység típusának és növelje a kódoláshoz fenntartott egység .NET-keretrendszerrel](media-services-dotnet-encoding-units.md).
* JWT jogkivonat hitelesítés támogatása hozzáadva. További információkért lásd: [JWT jogkivonat-hitelesítés a Media Services és a dinamikus titkosítás](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* A PlayReady-licencsablon BeginDate és ExpirationDate relatív eltolások bővült.

## <a id="november_changes_14"></a>2014. novemberi kiadásában
* Most már használhatja a Media Services élő Smooth Streaming (fMP4) tartalom betöltését a SSL-kapcsolaton keresztül. Hogy SSL-en keresztül, ügyeljen arra, hogy a bemeneti URL-címet frissíteni a HTTPS-re. Jelenleg az a Media Services nem támogatja az SSL egyéni tartománnyal rendelkező. Élő streameléssel kapcsolatos további információkért lásd: [használata az Azure Media Services élő Streamelési](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* SSL-kapcsolaton keresztül jelenleg nem képes feldolgozni a RTMP élő adatfolyamokat.
* SSL-en keresztül adatfolyam csak akkor, ha a streamvégpontra, amelyről a tartalomkézbesítés 2014. szeptember 10-után jött létre. Ha a streamelési URL-címek alapján a 2014. szeptember 10-én után létrehozott streamelési végpontok az URL-címet tartalmaz "streaming.mediaservices.windows.net" (a új formátum). Streamelési URL-címek, amelyek tartalmazzák a "origin.mediaservices.windows.net" (a régi formátumot) nem támogatják az SSL. Ha az URL-cím szerepel a régi formátumot, és SSL,-en keresztül kívánja [hozzon létre egy új streamvégpont](media-services-portal-manage-streaming-endpoints.md). A tartalmak streamelésére SSL-en keresztül, használja az URL-címek az új streamvégpont alapján.

## <a id="october_changes_14"></a>2014. októberi kiadás
### <a id="new_encoder_release"></a>Media Services Encoder kiadás
 A Media Services az Azure Media Encoder új kiadása mutattuk be. A legfrissebb Media Encoder fizetnie csak a kimeneti GB. Ellenkező esetben az új kódoló funkciója kompatibilis a korábbi kódolót. További információkért lásd: [A Media Services díjszabásával kapcsolatos részletek].

### <a id="oct_sdk"></a>A Media Services .NET SDK-val
A Media Services SDK for .NET-bővítmények mostantól 2.0.0.3 verzió.

A Media Services SDK for .NET már 3.0.0.8 verzió. Az alábbi frissítések történtek:

* Újrabontás újrapróbálkozási házirend osztályok lett megvalósítva.
* HTTP-kérelmek fejléceinek lett felvéve a böngészőazonosító sztringet.
* Buildelési lépést NuGet visszaállítási hozzá lett adva.
* Forgatókönyv tesztek x509 használandó javítva lett a tárházból tanúsítvány.
* Ellenőrzési beállítások hozzá voltak adva a Ha a csatorna és a streamelési vége.

### <a name="new-github-repository-to-host-media-services-samples"></a>Új GitHub-adattárban gazdagép Media Services-minták
Minták szerepelnek a [Media Services-minták GitHub-adattár](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>2014. szeptember kiadás
A Media Services REST-metaadatok már 2.7-es verzió. A legújabb REST frissítésekkel kapcsolatos további információkért lásd: a [Media Services REST API-referencia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

A Media Services SDK for .NET már 3.0.0.7 verzió

### <a id="sept_14_breaking_changes"></a>Használhatatlanná tévő változásai
* Forrás átnevezte [Streamvégpontok].
* A módosítás az alapértelmezett viselkedés kódolásához, és tegye közzé az MP4-fájlokat az Azure portal használatakor.

### <a id="sept_14_GA_changes"></a>Új funkciók/forgatókönyveket, amelyek részei az általánosan elérhető kiadások
* A Media Indexer médiafeldolgozót jelent meg. További információkért lásd: [a Media Indexer médiafájlok indexelése](https://msdn.microsoft.com/library/azure/dn783455.aspx).
* Használhatja a [Streamvégpontok] entitás hozzáadása az egyéni tartománynevek (fogadó).
  
    Egyéni tartománynév használata a Media Services streaming endpoint neveként, a streamvégpontnak egyéni állomásnevek hozzá. A Media Services REST API-k vagy a .NET SDK használatával egyéni állomásnevek hozzáadása.
  
    A következő szempontokat kell figyelembe venni:
  
  * Az egyéni tartománynév tulajdonjogának kell rendelkeznie.
  * A Media Services által érvényesíteni kell a tartománynév tulajdonjogát. A tartomány ellenőrzéséhez hozzon létre egy CNAME rekordot, amely leképezi a MediaServicesAccountId szülőtartomány DNS-mediaservices – dns-zóna ellenőrzése.
  * Létre kell hoznia egy másik CName, amely egyéni gazdagépnévvel (például sports.contoso.com) a Media Services Streamvégpontok gazdagép nevét (például amstest.streaming.mediaservices.windows.net) van leképezve.

    További információkért lásd: CustomHostNames tulajdonsága a [Streamvégpontok](https://msdn.microsoft.com/library/azure/dn783468.aspx) cikk.

### <a id="sept_14_preview_changes"></a>Új funkciók/forgatókönyveket, amelyek részei a nyilvános előzetes kiadás
* Élő adatfolyam-továbbítási előzetes verzió. További információkért lásd: [együttműködnek a Media Services élő streamelési](https://msdn.microsoft.com/library/azure/dn783466.aspx).
* Kulcstovábbítást. További információkért lásd: [használata AES-128, a dinamikus titkosítás és a kulcstovábbítást](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* AES-titkosítás dinamikus. További információkért lásd: [használata AES-128, a dinamikus titkosítás és a kulcstovábbítást](https://msdn.microsoft.com/library/azure/dn783457.aspx).
* PlayReady licenctovábbítási szolgáltatása. 
* A dinamikus PlayReady-titkosítást. 
* A Media Services PlayReady licencsablon. További információkért lásd: a [a Media Services PlayReady licencsablon áttekintése].
* Stream storage által titkosított eszközökre. További információkért lásd: [storage által titkosított tartalmat Stream](https://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>A 2014. augusztus kiadás
Adategység kódolása, ha a kimeneti adategység jön létre, a kódolási feladat befejezésekor. Ebben a kiadásban, amíg a Media Services Encoder előállított kimeneti objektumok metaadatait. Ettől a kiadástól kezdve a kódoló is hoz létre bemeneti objektumok metaadatait. További információkért lásd: [bemeneti metaadatok] és [kimeneti metaadatok].

## <a id="july_changes_14"></a>2014. júliusi kiadás
A következő hibajavítások az Azure Media Services Packager és a jelszótitkosító futtatásához történtek:

* Amikor egy élő archívumot eszköz átkerülnek a HLS, csak hang játszik: A probléma rögzített volt, és most audio- és videotartalmak játszhatja le.
* Ha egy eszköz van csomagolva, HLS és 128 bites AES boríték-titkosítást, a csomagolt adatfolyamok nem lejátszani az Android-eszközökön: Ezt a hibát kijavították, és az Android-eszközökön, amelyek támogatják a HLS játszik a csomagolt streamet.

## <a id="may_changes_14"></a>2014. május kiadás
### <a id="may_14_changes"></a>Általános Media Services-frissítések
Most már használhatja [dinamikus csomagolási] Stream HLS 3-as verziójú. A stream HLS 3-as verziójú, adja hozzá a következő formátumban a forrás-lokátor elérési útja: * .ism/manifest(format=m3u8-aapl-v3). További információkért lásd: [ezen a fórumon](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

A dinamikus csomagolás most is támogatja a Smooth Streaming playreadyvel statikusan titkosított alapján PlayReady titkosított HLS (3-as verziójú és 4-es verzió). A Smooth Streaming playreadyvel titkosítása további információkért lásd: [Smooth Streaming védelme playreadyvel](https://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK-frissítések
A Media Services .NET SDK már 3.0.0.5 verzió. Az alábbi frissítések történtek:

* Sebesség és a rugalmasság akkor jobb, ha töltse fel, és töltse le a médiatartalmak ellátásához.
* Fejlesztések történtek az újrapróbálkozási logika és az átmeneti kivételek kezelése: 
  
  * Átmeneti hiba észlelése és újrapróbálkozási logikát is javítottuk a lekérdezése, mentse a módosításokat, és töltse fel a vagy fájlok letöltése okozott kivételeket. 
  * Ha webes kivételek (például egy Access Control Service-jogkivonat kérése) alatt, végzetes hibák sikertelen gyorsabban most.

További információkért lásd: [Újrapróbálkozási logika, a Media Services SDK-ban a .NET-hez].

## <a id="april_changes_14"></a>2014. április kódoló kiadás
### <a name="april_14_enocer_changes"></a>Media Services Encoder frissítések
* Támogatási AVI-fájlokban, hogy a élet Valley EDIUS nemlineáris szerkesztő használatával készült, hogy lett hozzáadva. Ebben a folyamatban a videó enyhén tömörített élet Valley HQ/HQX kodek használatával. További információkért lásd: [élet Valley készült továbbfejlesztésekkel bővült a felhőn keresztül streamelési EDIUS 7].
*  Adja meg a fájlokat, a Media Services Encoder által előállított elnevezési támogatása hozzáadva. További információkért lásd: [vezérlő Media Services Encoder kimeneti fájl nevét](https://msdn.microsoft.com/library/azure/dn303341.aspx).
*  Videó-és/vagy hang átfedések váltak. További információkért lásd: [átfedések létrehozása](https://msdn.microsoft.com/library/azure/dn640496.aspx).
*  Támogatás hozzáadva összefűzheti több videó szegmensek. További információkért lásd: [videó szegmensek több](https://msdn.microsoft.com/library/azure/dn640504.aspx).
* Egy hiba volt átkódolása, ahol a hanganyag kódolt olyan MP4, fájlnak az MPEG-1 hang 3. rétegbeli (más néven MP3) kapcsolódó rendszerriasztások mechanizmusában.

## <a id="jan_feb_changes_14"></a>2014. január/február kiadások
### <a name="jan_fab_14_donnet_changes"></a>A Media Services .NET SDK-val 3.0.0.1, 3.0.0.2 és 3.0.0.3
A 3.0.0.1 és 3.0.0.2 a változások a következők:

* Javítva lett a LINQ-lekérdezések az OrderBy utasítások használatát kapcsolatos problémákat.
* -Megoldások teszteléséhez [GitHubon] egység alapján tesztek és a forgatókönyv-alapú tesztek lett felosztva.

A változásokkal kapcsolatos további információkért lásd: a [3.0.0.1 és 3.0.0.2 Media Services .NET SDK-kiadások](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

A következő változások történtek 3.0.0.3 verzióban:

* Az Azure storage-függőségek frissített verziójának 3.0.3.0.
* Előző verziókkal való kompatibilitási probléma 3.0 a rendszerriasztások mechanizmusában. *.* kiadások.

## <a id="december_changes_13"></a>2013. decemberi kibocsátási
### <a name="dec_13_donnet_changes"></a>A Media Services .NET SDK-val 3.0.0.0
> [!NOTE]
> A 3.0.x.x kiadásokban nem kompatibilisek visszamenőlegesen a 2.4.x.x kiadások.
> 
> 

A Media Services SDK legújabb verzióját már 3.0.0.0. Töltse le a legújabb csomagot a Nugetről, vagy a bits, az első [GitHubon].

A Media Services SDK 3.0.0.0 verziójával kezdődően újból felhasználhatja a [Azure AD Access Control Service](https://msdn.microsoft.com/library/hh147631.aspx) jogkivonatokat. További információkért lásd: "Újbóli Access Control Service tokenek" szakasz a [kapcsolódás a Media Services Media Services SDK-val .NET-keretrendszerhez készült](https://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>A Media Services .NET SDK-bővítmények 2.0.0.0-s
 A Media Services .NET SDK-bővítmények olyan kiegészítő módszerek és segédfüggvények találhatók, amelyek egyszerűbbé teszik a kódolást és megkönnyítik a Media Services fejlesztéséhez. Beszerezheti a legújabb elemeket a [Media Services .NET SDK-bővítmények](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>2013. novemberi kiadásban
### <a name="nov_13_donnet_changes"></a>Media Services .NET SDK-módosítások
Ebben a verzióban a Media Services SDK a .NET leírók átmeneti hibák hibákat, amelyek akkor jelentkeznek, amikor a hívások a Media Services REST API-réteg kezdve.

## <a id="august_changes_13"></a>2013. augusztus kiadás
### <a name="aug_13_powershell_changes"></a>Azure SDK tools tartalmaz a Media Services PowerShell-parancsmagok
A következő Media Services PowerShell-parancsmagok mostantól beletartoznak [Azure SDK tools](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Például:`Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Például:`New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Például:`New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Például:`Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>2013. június kiadás
### <a name="june_13_general_changes"></a>A Media Services módosítások
A következő módosításokat ebben a szakaszban említett a 2013. június Media Services-kiadások-frissítéseket is:

* Lehetővé teszi több tárfiók csatolása egy Media Services-fiókját. 
    * Tárfiók
    * Asset.StorageAccountName és Asset.StorageAccount
* Job.Priority frissítésének lehetőségét. 
* Értesítési kapcsolatos entitásokat és tulajdonságok: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Feladat
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Media Services .NET SDK-módosítások
A következő változások legyenek belefoglalva a június 2013 a Media Services SDK-kiadások. A legfrissebb Media Services SDK a Githubon érhető el.

* A Media Services SDK-t támogatja, több tárolási linking fiókok 2.3.0.0-s kezdve a Media Services-fiókba. A következő API-kat támogatja ezt a szolgáltatást:
  
    * IStorageAccount típusa
    * Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts property
    * StorageAccount tulajdonság
    * StorageAccountName tulajdonság
  
      További információkért lásd: [kezelése Media Services objektumai több tárfiókban](https://msdn.microsoft.com/library/azure/dn271889.aspx).
* Értesítési kapcsolatos API-k. 2.2.0.0 verziójával kezdődően, figyelheti az Azure Queue storage értesítésekre. További információkért lásd: [kezelni a Media Services feladat értesítések](https://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions tulajdonság
    * Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint típusa
    * Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription típusa
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection típusa
    * Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType típusa
* A Storage ügyféloldali SDK 2.0-s (Microsoft.WindowsAzure.StorageClient.dll) függőség
* Függőség OData 5.5-ös (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>2012. december kiadás
### <a name="dec_12_dotnet_changes"></a>Media Services .NET SDK-módosítások
* Az IntelliSense: Hiányzik az IntelliSense dokumentáció számos hozzá lett adva.
* Microsoft.Practices.TransientFaultHandling.Core: Probléma volt rögzített, ahol az SDK továbbra is rendelkezett egy függőséget ezt a szerelvényt, régebbi verzióját. Az SDK-val most már hivatkozik a szerelvény 5.1.1209.1 verzióját.

A 2012. November SDK található hibák javításait:

* IAsset.Locators.Count: Ez a szám minden kereső törlését követően most már jelentett új IAsset adapteren megfelelően.
* IAssetFile.ContentFileSize: Most már megfelelően beállítás után egy feltöltési IAssetFile.Upload(filepath) szerint.
* IAssetFile.ContentFileSize: Ez a tulajdonság most már beállítható egy adategység-fájl létrehozásakor. Korábban csak olvasás.
* IAssetFile.Upload(filepath): Probléma volt rögzített, ahol a szinkron feltöltési módszer történt kivétel a következő hiba az eszközre feltöltésekor a rendszer több fájlt. Hiba: "a kiszolgáló nem tudta hitelesíteni a kérelmet. Győződjön meg arról, hogy az engedélyezési fejléc értékének formátuma megfelelő többek között az aláírás."
* IAssetFile.UploadAsync: Probléma volt rögzített fájlok öt fájl egyidejű feltöltésének korlátozott.
* IAssetFile.UploadProgressChanged: Az SDK-t mostantól megjeleníti ezt az eseményt.
* IAssetFile.DownloadAsync (karakterlánc, BlobTransferClient, ILocator, CancellationToken): Ez a módszer túlterhelés mostantól megjeleníti.
* IAssetFile.DownloadAsync: Rögzített volt a problémát, amely legfeljebb öt fájlt a fájlok az egyidejű letöltését.
* IAssetFile.Delete(): Probléma hol hívó delete előfordulhat, hogy kivételt, ha nem történt fájlfeltöltés a IAssetFile a rendszerriasztások mechanizmusában.
* Feladatok: Probléma volt rögzített, ahol egy "MP4 zökkenőmentes Streamek feladat" a "PlayReady védelmi feladat" feladat a sablonok segítségével-láncolás nem hozott létre tevékenységeket egyáltalán.
* EncryptionUtils.GetCertificateFromStore(): Ez a metódus már nem jelez egy NULL értékű referencia kivételt adja vissza a tanúsítványt a tanúsítvány konfigurációs problémák alapján keres egy hiba miatt.

## <a id="november_changes_12"></a>2012. november kiadás
Ebben a szakaszban említett módosításra-frissítéseket a 2012. November (verzió 2.0.0.0-s) SDK-t. Ezeket a módosításokat a 2012. júniusi Preview, SDK kiadási módosulnak vagy feladatátvitelt írt kód lehet szükség.

* Objektumok
  
    * IAsset.Create(assetName) van a *csak* adategység létrehozása funkciót. IAsset.Create metódushívás részeként már nem tölt fel fájlokat. IAssetFile használja fel.
    * Az SDK a IAsset.Publish módszer és a AssetState.Publish enumerálási érték lett eltávolítva. Hogy ezt az értéket meg kell írni.
* FileInfo
  
    * Ez az osztály lett eltávolítva, és IAssetFile helyébe.
  
* IAssetFiles
  
    * IAssetFile váltja fel a FileInfo és a egy másik működés. A használatához az objektumpéldány IAssetFiles, kiegészítve a feltöltés, a Media Services SDK-t vagy a segítségével a Storage SDK-t. A következő IAssetFile.Upload túlterheléssel használhatók:
  
        * IAssetFile.Upload(filePath): A szinkron módszer blokkolja a hozzászólásláncot, és ajánlott csak egyetlen fájl feltöltésekor.
        * IAssetFile.UploadAsync (fájl elérési útja, blobTransferClient, lokátort, cancellationToken): Ez a metódus aszinkron az előnyben részesített feltöltési mechanizmusa. 
    
            Ismert hiba: Ha a megszakítási tokent használ, a feltöltés meg lett szakítva. A feladatok számos megszakítás állapotok is rendelkezhet. Később kell a tényleges, kivételek kezelése.
* Keresők
  
    * A forrás-specifikus verziók el lettek távolítva. A SAS-specifikus környezet. Elavult vagy eltávolított általános rendelkezésre állás szerint Locators.CreateSasLocator (eszköz, accessPolicy) lesznek megjelölve. A frissített viselkedés "új funkciói" a "Keresők" című szakaszában talál.

## <a id="june_changes_12"></a>2012. júniusi előzetes verzió
Az alábbi funkciókat lett az SDK novemberi kiadásában új:

* Entitások törlése
  
    * Az objektumok szintjén, azaz IObject.Delete(), nem kell megadni célhelyként a gyűjtemény, azaz cloudMediaContext.ObjCollection.Delete(objInstance) törlési most törlődnek a IAsset, IAssetFile, ILocator, IAccessPolicy és IContentKey objektumokat.
* Keresők
  
    * Keresők most már léteznie kell a CreateLocator módszer használatával. A LocatorType.SAS vagy LocatorType.OnDemandOrigin felsorolásértéktől argumentumként a lokátor típusát a létrehozni kívánt kell használniuk.
    * Új tulajdonságok lokátorokat, hogy egyszerűbb legyen a tartalom használható URL-címének beszerzése bővült. A keresők újratervezése nagyobb rugalmasságot biztosít a későbbi külső bővítést, és növeli a könnyű használatra az adathordozó-ügyfélalkalmazások.
* Asynchronní metoda támogatása
  
    * Aszinkron támogatásának lett felvéve az összes módszer.

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Az Azure Media Services MSDN-fórum]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Az Azure Media Services REST API-referencia]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[A Media Services díjszabásával kapcsolatos részletek]: http://azure.microsoft.com/pricing/details/media-services/
[Bemeneti metaadatok]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Kimeneti metaadatok]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[Streamvégpontok]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[A Media Services PlayReady licencsablon áttekintése]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[dinamikus csomagolási]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Újrapróbálkozási logika, a Media Services SDK-ban a .NET-hez]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Élet Valley készült továbbfejlesztésekkel bővült a felhőn keresztül streamelési EDIUS 7]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
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

