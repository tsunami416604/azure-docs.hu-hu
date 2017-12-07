---
title: "A Media Services kibocsátási megjegyzései |} Microsoft Docs"
description: "Médiaszolgáltatások kibocsátási megjegyzései"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako
ms.openlocfilehash: 358b3701773e6cd61b4a3dfddf4bb092741ff713
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="azure-media-services-release-notes"></a>Az Azure Media Services kibocsátási megjegyzései
A kibocsátási megjegyzések összesítse a módosításokat a korábbi kiadásokban és ismert problémákat.

> [!NOTE]
> Szeretnénk megosztaná velünk a felhasználók pedig a előforduló problémák elhárításához. A problémákat, vagy kérdései vannak, tegye a a [Azure Media Services MSDN fórumon].
> 
> 

## <a id="issues"></a>Ismert problémák
### <a id="general_issues"></a>Media Services kapcsolatos általános problémák
| Probléma | Leírás |
| --- | --- |
| Több, közös HTTP-fejlécek nem szerepelnek a REST API-t. |A REST API használatával Media Services-alkalmazások fejlesztése, ha úgy gondolja, hogy néhány gyakori HTTP-fejlécmezők (CLIENT-REQUEST-ID, beleértve-azonosító, és VISSZATÉRÉSI-CLIENT-REQUEST-ID) használata nem támogatott. A fejlécek az egy későbbi kiadásban lesz hozzáadva. |
| Százalék-kódolás nem engedélyezett. |A Media Services a IAssetFile.Name tulajdonság értékét használja, amikor az adatfolyam-tartalmak (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) a URL-címek kiépítéséhez Emiatt százalék-kódolás nem engedélyezett. Értékét a **neve** tulajdonság nem lehet a következő [százalék kódolás-fenntartott karakterek](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Emellett csak lehet egy "." a fájlnévkiterjesztés. |
| A ListBlobs metódus, amely része az Azure Storage szolgáltatás SDK verzió 3.x sikertelen lesz. |A Media Services SAS URL-címek alapján hoz létre a [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) verziója. Azure Storage szolgáltatás SDK segítségével lista blobot, amely egy blob-tároló, használja a [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) módszer, amelynek része az Azure Storage szolgáltatás SDK verzió 2.x. A szolgáltatás részét képező ListBlobs metódust az Azure Storage szolgáltatás SDK verzió 3.x sikertelen lesz. |
| Media Services mechanizmus szabályozás korlátozza az alkalmazások, amelyek a szolgáltatás túl sok kérelem erőforrás-felhasználása. A szolgáltatás a szolgáltatás nem érhető el (503-as) HTTP-állapotkód adhatnak vissza. |További információkért lásd: a 503-as HTTP-állapotkód: a leírása a [Azure Media Services hibakódok](media-services-encoding-error-codes.md) cikk. |
| Entitások lekérdezésekor korlátozás van adja vissza egy időben, mert a nyilvános REST v2 korlátozza a lekérdezési eredmények 1000 eredmények 1000 entitások. |Kell használnia **kihagyása** és **érvénybe** (.NET) / **felső** (REST) leírtak szerint [.NET példában](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) és [a REST API-példa](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Egyes ügyfelek között egy ismétlődő címke probléma a Smooth Streaming jegyzékben származhatnak. |További információ [itt](media-services-deliver-content-overview.md#known-issues) érhető el. |
| Az Azure Media Services .NET SDK-objektum nem szerializálható, és emiatt sem működik együtt az Azure-gyorsítótárazás. |Ha a gyorsítótár Azure hozzáadása SDK AssetCollection objektum szerializálása, a rendszer kivételt hoz létre. |


## <a id="rest_version_history"></a>REST API verziójának előzményei
A Media Services REST API verziójának előzményei kapcsolatos információkért lásd: [Azure Media Services REST API-referencia].

## <a name="october-2017-release"></a>2017. októberi kiadás
> [!IMPORTANT] 
> Emlékeztető: Az Azure Media Services az ACS-hitelesítési kulcsokat támogatása kell elavulttá lesz.  A 2018. június 1. akkor nem lesznek többé az AMS-háttérrendszerrel ACS kulcsokkal kód hitelesítéshez. Frissítse a kódot az Azure Active Directory (AAD) használja a cikkenként [Azure Active Directory (Azure AD)-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). Ezt a módosítást az Azure-portálon is figyelmeztetést fog kapni.

### <a name="updates-for-october-2017-include"></a>2017. októberi frissítések a következők:
#### <a name="sdks"></a>SDK-k
* .NET SDK frissítése az AAD-hitelesítés támogatásához.  Az ACS-hitelesítés támogatásának eltávolítottuk a Nuget.org bátorítva gyorsabb áttelepítést az aad-be a legújabb .NET SDK-ból. 
* JAVA SDK frissítése az AAD-hitelesítés támogatásához.  AAD-hitelesítés támogatása a Java SDK jelentek meg. A Java SDK használata az AMS-cikkben található részletes olvasható [Ismerkedés a Java-ügyfél SDK az Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Fájl alapú kódolás
1.  A prémium szintű kódolás használatával most H.265(HEVC) videó kodek tartalom kódolása. Nincs a H.265 keresztül más hasonló H.264 kodekek árképzési hatással. Tekintse meg a [Online szolgáltatási feltételek](https://azure.microsoft.com/support/legal/) HEVC egy fontos megjegyzés a szabadalom a licenc száma.
2.  Ha a videó kodek H.265(HEVC), például rögzített iOS11 vagy GoPro Hero 6, videó kódolt forrás videó használhatja vagy a prémium szintű kódolás, vagy a adás ezeket a videók kódolásához. Tekintse meg a [Online szolgáltatási feltételek](https://azure.microsoft.com/support/legal/) a szabadalmi licenc egy fontos megjegyzés.
3.  Ha tartalmazó több nyelv zeneszámok, majd mindaddig, amíg a nyelvi értékek megfelelően a megfelelő fájlformátum specifikációjának (pl. ISO MP4) megfelelően címkével ellátott tartalom, majd segítségével a adás, hogy a tartalom kódolása adatfolyam. Az eredő streamelési locator felsorolja az elérhető hang nyelveket.
4.  Standard Encoder mostantól támogatja a két új csak rendszer készletek, "AAC hang" és "AAC jó minőségű hang". Mindkét sztereó AAC kimeneti, 128 kb/s és 192 Kbit/s átviteli sebességgel rendre eredményez.
5.  A prémium szintű kódolás mostantól támogatja az QuickTime/MOV fájlformátumok bemeneti adatként, mindaddig, amíg a videó kodek, vagy egy olyan a [Apple ProRes változatban is elkészíti az itt felsorolt](https://docs.microsoft.com/en-us/azure/media-services/media-services-media-encoder-standard-formats), illetve a hangfelvételen hallható AAC vagy PCM.

> [!NOTE]
> A prémium szintű kódolás nem támogatja, például DVC/DVCPro videó QuickTime/MOV fájlok csomagolni bemeneti adatként.  Standard Encoder azonban a Videó kodekek támogatja.
>
>

6.  Hibajavítások kódolók képesek:
    * Ezután egy bemeneti eszköz használatával feladatok elküldéséhez és ezek befejezése után módosítsa az eszköznek (például az eszköz hozzáadása/törlés/átnevezése fájljához), és további feladatok elküldéséhez. 
    * A szabványos kódoló által előállított JPEG miniatűrök minőségének javítása
    * Standard Encoder fejlesztései videók rövid időtartamra. Jobb bemeneti metaadatok és a nagyon rövid időtartam videók miniatűr generációs kezelése.
    * A használt adás, H.264 dekóder fejlesztései megszünteti az egyes ritka összetevők. 

#### <a name="media-analytics"></a>Médiaelemzés
* Az Azure Media Redactor - GA a Media processzor (MP) által kiválasztott személyek lapok fellazítja anonymization hajt végre, és ideális nyilvános biztonsági és hírek media olyan esetekben. Az új processzorokon olvashat további tudnivalókat a következő blogbejegyzésben található [Itt](https://azure.microsoft.com/blog/azure-media-redactor/). Részletes dokumentációt és beállítások: [kivonása az Azure Media Analytics lapok](media-services-face-redaction.md).



## <a name="june-2017-release"></a>2017. június kiadás

Most már támogatja a Media Services [Azure Active Directory (Azure AD)-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> A Media Services jelenleg az Azure hozzáférés-vezérlés szolgáltatásmodell-hitelesítés. Azonban a 2018. június 1 elavulttá válik hozzáférés-vezérlés engedélyt. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="march-2017-release"></a>2017. március kiadás

Ezután már használhatja Azure Media Standard [automatikus létrehozása egy sávszélességű létra](media-services-autogen-bitrate-ladder-with-mes.md) egy kódolási feladat létrehozásakor a karakterlánc a "adaptív Streameléshez" beállított megadásával. "Adaptív adatfolyam" a javasolt készlet használata, ha azt szeretné, a Media Services streaming videó kódolására. Ha testre szeretné szabni, az adott forgatókönyv szerint az adott néven beállítás kódolással, elkezdheti a [ezek](media-services-mes-presets-overview.md) készletek.

Ezután már használhatja Azure Media Standard vagy a Media Encoder prémium munkafolyamat [állít elő, fMP4 adattömbök kódolási feladat létrehozása](media-services-generate-fmp4-chunks.md). 


## <a name="february-2017-release"></a>2017. február kiadás

2017. április 1-től kezdődően a fiókokban a 90 napnál régebbi Feladat rekordok automatikusan törölve lesznek, a kapcsolódó Művelet rekordokkal egyetemben, még ha a rekordok összesített száma nem is éri el a maximális kvótát. A feladatok/műveletek adatainak archiválásához használhatja az [itt](media-services-dotnet-manage-entities.md) ismertetett programkódot.

## <a name="january-2017-release"></a>2017. január kiadás

A Microsoft Azure Media Services (AMS), egy **Streamvégponton** egy adatfolyam-szolgáltatás által biztosított tartalom közvetlenül player ügyfélalkalmazást, vagy az a Content Delivery Network (CDN) későbbi terjesztés jelöli. A Media Services is biztosít az Azure CDN való zökkenőmentes integrációt. A kimenő adatfolyam egy StreamingEndpoint szolgáltatásból lehet élő stream, igény szerint, vagy az eszköz a Media Services-fiók a progresszív letöltés videó. Minden Azure Media Services-fiók egy alapértelmezett StreamingEndpoint tartalmazza. A fiók alatt további Streamvégpontok hozhatók létre. Streamvégpontok, 1.0-s és 2.0 két verziója van. 2017. január 10., kezdve minden újonnan létrehozott AMS fiókokat tartalmazza 2.0-s verziójának **alapértelmezett** StreamingEndpoint. Ehhez a fiókhoz hozzáadni kívánt további streamvégpontok is 2.0-s verziójában. Ez a változás nem befolyásolja a meglévő fiókokat; meglévő Streamvégpontok 1.0-s verziója és a 2.0-s verziójának frissítése. A módosítás nem lesznek viselkedés, a számlázással és a szolgáltatás módosításokat (további információkért lásd: [ez](media-services-streaming-endpoints-overview.md) cikk).

Emellett a 2.15 verziójától kezdve Azure Media Services felvenni a következő tulajdonságok a Streamvégponton entitás: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Részletes megtudhatja, ezeket a tulajdonságokat, [ez](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>2016. december kiadás

Az Azure Media Services most lehetővé teszi a hozzá tartozó szolgáltatások telemetriai/metrikai adatok eléréséhez. AMS jelenlegi verziója lehetővé teszi az élő csatorna, StreamingEndpoint, telemetriai adatokat gyűjt, és a működés közbeni archív entitásokat. További információkért lásd: [ez](media-services-telemetry-overview.md) cikk.

## <a id="july_changes16"></a>2016. július kiadás
### <a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Frissítések manifest fájl (*. ISM) generálja a kódolási feladatokhoz
Amikor egy kódolási feladat Media Encoder Standard vagy az Azure Media Encoder, a kódolási feladat létrehoz egy [adatfolyam jegyzékfájl](media-services-deliver-content-overview.md) (* .ism) a kimeneti fájl eszköz. A legújabb szolgáltatás a kiadástól kezdve a szintaxist, az adatfolyam-továbbítási jegyzékfájl frissítve lett.

> [!NOTE]
> A jegyzékfájl (.ism) adatfolyamfájl szintaxisa a következő belső használatra fenntartva, és a későbbi kiadásokban változhat. Nem módosíthatók, ez a fájl tartalmának módosítására.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Egy új ügyfél jegyzékfájl (*. ISMC) fájl jön létre, a kimenetben eszköz, amikor egy kódolási feladatok kimenete egy vagy több MP4-fájlok
További MP4-fájlokat, a kimenetet állít elő egy egy kódolási feladat befejezése után, az a legújabb szolgáltatás kiadástól kezdve eszköz is egy adatfolyam-továbbítási ügyfél jegyzékfájl (*.ismc)-fájlt fogja tartalmazni. A .ismc fájl javítja a teljesítményt, a dinamikus adatfolyamként való továbbítására. 

> [!NOTE]
> Az ügyfél-jegyzékfájl (.ismc) fájl szintaxisa a következő belső használatra fenntartva, és a későbbi kiadásokban változhat. Nem módosíthatók, ez a fájl tartalmának módosítására.
> 
> 

További információkért lásd: [ez](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blog.

### <a name="known-issues"></a>Ismert problémák
Egyes ügyfelek között egy ismétlődő címke probléma a Smooth Streaming jegyzékben származhatnak. További információ [itt](media-services-deliver-content-overview.md#known-issues) érhető el.

## <a id="apr_changes16"></a>2016. április kiadás
### <a name="azure-media-analytics"></a>Az Azure Médiaelemzés használatával
Az Azure Media Services Azure Médiaelemzés használatával hatékony videó eszközintelligencia bevezetni. Részletes információkért lásd: [Azure Media Services elemző áttekintése](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (előzetes verzió)
Az Azure Media Services lehetővé teszi a HTTP Live Streaming (HLS) az Apple FairPlay tartalom dinamikusan titkosítani most. AMS licenctovábbítási szolgáltatásra való kézbesítésére FairPlay-licenc ügyfelek is használhatja. További részletes információkért lásd: [használata Azure Media Services a HLS tartalom védett Apple FairPlay az adatfolyamként történő küldéséhez.

## <a id="feb_changes16"></a>2016. februári kiadás
A legújabb Azure Media Services SDK for .NET (3.5.3) Widevine kapcsolódó hibajavítás tartalmazza. A problémát: AssetDeliveryPolicy nem használható fel újra, több eszköz szolgáltatással végzett Widevine titkosítása. A hibajavítás részeként a következő tulajdonság hozzá lett adva az SDK-val: **WidevineBaseLicenseAcquisitionUrl**.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>2016. január kiadás
A kódoló nevű félreértések elkerülése kódoláshoz fenntartott egység átnevezni.

A Basic, Standard és Premium kódolási fenntartott egységek S1, S2, a rendszer átnevezi, és S3 szolgáltatás számára fenntartott egység, illetve.  Alapszintű kódolás RUs ma használó ügyfelek látni fogják S1 a címke az Azure portálon (és a számlázási), közben Standard és Premium rendre megjelenik a címkék S2 és S3. 

## <a id="dec_changes_15"></a>2015. decemberi kiadása

### <a name="azure-media-encoder-deprecation-announcement"></a>Az Azure Media Encoder érvénytelenítése bejelentés

Az Azure Media Encoder indítása a Media Encoder Standard kiadása körülbelül 12 hónapon belül megszűnnek.

### <a name="azure-sdk-for-php"></a>Azure SDK a PHP-hoz
Az Azure SDK csapat közzé az új verziót a [Azure SDK for PHP](http://github.com/Azure/azure-sdk-for-php) csomagot, amely tartalmazza a frissítéseket és új funkciók a Microsoft Azure Media Services szolgáltatásokhoz. Elsősorban az Azure Media Services SDK php mostantól támogatja a legújabb [védelmi tartalom](media-services-content-protection-overview.md) szolgáltatások: a dinamikus titkosítás AES és DRM (PlayReady és Widevine) és a Token korlátozás nélkül. Azt is támogatja a skálázás [kódoláshoz egységek](media-services-dotnet-encoding-units.md).

További információkért lásd:

* A [Microsoft Azure Media Services SDK for PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) blog.
* A következő [Kódminták](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) az első lépések megtételéhez gyorsan:
  * **vodworkflow_aes.php**: a PHP-fájl, amely dinamikus AES-128 titkosítást és kulcs kézbesítési szolgáltatás használatát ismerteti. A .NET-mintát, tekintse meg a alapul [ez](media-services-protect-with-aes128.md) cikk.
  * **vodworkflow_aes.php**: a PHP-fájl, amely bemutatja, hogyan használja a dinamikus titkosítás PlayReady és Licenctovábbítási szolgáltatásra. A .NET-mintát, tekintse meg a alapul [ez](media-services-protect-with-playready-widevine.md) cikk.
  * **scale_encoding_units.php**: a PHP-fájl, amely bemutatja, hogyan kódolási fenntartott egységek méretezése.

## <a id="nov_changes_15"></a>2015. november kiadás
Az Azure Media Services licenctovábbítási szolgáltatása Google Widevine a felhőben kínál. További információkért lásd: [a közlemény blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Lásd még [ebben az oktatóanyagban](media-services-protect-with-playready-widevine.md) és [GitHub-tárházban](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Azure Media Sevices által biztosított Widevine licenctovábbítási szolgáltatások még csak előzetes verziójúak. További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>2015 október kiadás
Az Azure Media Services (AMS) mostantól élő a következő adatközpontokban: Dél-Brazília, Nyugat-India, Dél-India és közép-India. Ezután már használhatja az Azure portálon [Media Services-fiókok létrehozása](media-services-portal-create-account.md) és leírt különböző feladatok elvégzésére [Itt](https://azure.microsoft.com/documentation/services/media-services/). Azonban ezekben az adatközpontokban a Live Encoding nem támogatott. Emellett ezekben az adatközpontokban a kódoláshoz fenntartott egységeknek sem minden típusa érhető el.

* Dél-Brazília: Csak a standard és alapszintű kódoláshoz fenntartott egységek érhetők el
* Nyugat-India, Dél-India és közép-India: csak alapszintű kódoláshoz fenntartott egységek érhetők el

## <a id="september_changes_15"></a>2015. szeptemberi kiadás
* AMS védelme Video-On-Demand (VOD) és az élő adatfolyamok moduláris Widevine DRM technológiával is kínál. A következő kézbesítési szolgáltatások partnerek segítségével Widevine-licencek segítségével: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
    Az AssetDeliveryConfiguration Widevine használatára történő beállításához használja az [AMS .NET SDK-t](https://www.nuget.org/packages/windowsazure.mediaservices/) (a 3.5.1-es vagy újabb verziót), vagy a REST API-t.  
* AMS Apple ProRes videók támogatása. A forrás QuickTime-videók Apple ProRes vagy más használó fájlok most már feltöltheti. További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Most már használhatja Media Encoder Standard subclipping és élő archív kiolvasásához elvégzéséhez. További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Az alábbi szűrési frissítések történtek: 
  
  * Csak szűrővel most már használhatja az Apple HTTP Live Streaming (HLS) formátumban. A frissítés lehetővé teszi a eltávolítása csak követése megadásával (csak = false) URL-címét.
  * Az eszközök szűrők meghatározásakor most már rendelkezik több előrelépésre szolgáló egyetlen URL-címet (3) szűrőt.
    
    További információkért lásd: [ez](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.
* AMS I-keretek mostantól támogatja a HLS v4. I-keret támogatási előretekerés és visszatekerés műveletek optimalizálja. Alapértelmezés szerint az összes HLS v4 kimenetének I-keret lista (EXT-X-I-FRAME-STREAM-INF) tartalmazza.
  
    További információkért lásd: [ez](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a id="august_changes_15"></a>2015. augusztus kiadás
* Az Azure Media Services SDK Java V0.8.0 kiadás és az új mintát már elérhető. További információkért lásd:
  
  * [Blogbejegyzés](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [Java-tárház minták](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Az Azure Media Player frissítés több hangadatfolyam támogatásával. További információkért lásd:
  * [Blogbejegyzés](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

## <a id="july_changes_15"></a>2015. július kiadás
* Az általános rendelkezésre állását Media Encoder Standard bejelentése. További információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard használ ismertetett készletek [ez](http://go.microsoft.com/fwlink/?LinkId=618336) szakasz. Használatakor a készletet a 4 KB-os kódolja szerezheti be a **prémium** szolgáltatás számára fenntartott egység típusát. További információkért lásd: [hogyan méretezési kódolás](media-services-scale-media-processing-overview.md).
* Élő és az Azure Media Services Player valós idejű feliratok. További információkért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
Az Azure Media Services .NET SDK verziója most 3.4.0.0. A következő funkciók ebben a kiadásban lett hozzáadva:  

* Élő archív megvalósított támogatása. Egy élő archív tartalmazó objektumot nem lehet letölteni.
* Dinamikus szűrők megvalósított támogatása.
* Megvalósított funkció, amely lehetővé teszi a felhasználóknak a tároló tartsa eszköz törlése közben.
* Ismételje meg a csatornák házirendekkel kapcsolatos hibajavítások.
* Engedélyezett **Media Encoder prémium munkafolyamat**.

## <a id="june_changes_15"></a>2015. június kiadás
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
Az Azure Media Services .NET SDK verziója most 3.3.0.0. A következő funkciók ebben a kiadásban lett hozzáadva:  

* OpenId Connect felderítési spec támogatása
* támogatott kezelése az identity provider oldalán kulcsok kulcsváltás. 

Ha az OpenID Connect felderítési dokumentum elérhetővé tévő identitásszolgáltató (, mint a következő szolgáltatók: Azure Active Directoryban, Google, Salesforce), Azure Media Services aláírási kulcsokat érvényesítése JWT jogkivonat beszerzése az OpenID találhatók felderítési spec csatlakozzon. 

További információkért lásd: [használatával Json webes kulcsokat OpenID Connect felderítési spec JWT dolgozni a token hitelesítés az Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>2015. május kiadás
Az alábbi új szolgáltatások bejelentése:

* [A Media Services élő kódolás előnézete](media-services-manage-live-encoder-enabled-channels.md)
* [Dinamikus jegyzék](media-services-dynamic-manifest-overview.md)
* [Azure Media Hyperlapse media processzor előnézete](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>2015. áprilisi kiadás
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
* [Az Azure Media Player bejelentése](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
* Media Services REST 2.10, amely a betöltési egy RTMP protokollt csatornák kezdve jönnek létre, az elsődleges és másodlagos betöltési URL-címeket. További információkért lásd: [csatorna betöltési konfigurációk](media-services-live-streaming-with-onprem-encoders.md#channel_input)
* Az Azure Media Indexer frissítések
* Spanyol nyelvi támogatása
* Új konfigurációs XML-formátuma

További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
Az Azure Media Services .NET SDK verziója most 3.2.0.0.

Az alábbiakban az ügyfelek által használt a frissítések:

* **Változás megtörje**: megváltozott **TokenRestrictionTemplate.Issuer** és **TokenRestrictionTemplate.Audience** egy karakterlánc típusúnak kell lennie.
* Frissítések egyéni létrehozásával kapcsolatos házirendek próbálkozzon újra.
* Fájlok feltöltése/letöltésével kapcsolatos hibajavítások.
* A **MediaServicesCredentials** osztály most elsődleges és másodlagos hozzáférés-vezérlési végponthoz hitelesítése fogad el.

## <a id="march_changes_15"></a>2015. márciusi kiadás
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
* A Media Services most Azure CDN integrációt biztosít. Az integráció támogatásához a **CdnEnabled** tulajdonság hozzá lett adva **StreamingEndpoint**.  **CdnEnabled** együtt 2.9 verziójától kezdve REST API-k (további információkért lásd: [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)).  **CdnEnabled** 3.1.0.2 verziójától kezdve a .NET SDK-val használható (további információkért lásd: [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx)).
* A közlemény **Media Encoder prémium munkafolyamat**. További információkért lásd: [prémium szintű kódolás Introducing Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>2015. február kiadás
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
Media Services REST API verziója most 2.9. Ezen verziójával kezdődően az Azure CDN integrációja adatfolyam-végpontok engedélyezheti. További információkért lásd: [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>2015. januári kiadás
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
Az általános rendelkezésre állási (GA) a dinamikus titkosítás tartalom védelmi közlemény. További információkért lásd: [Azure Media Services javítja a folyamatos átviteli biztonsági DRM általános rendelkezésre állási technológiával](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
Az Azure Media Services .NET SDK verziója most 3.1.0.1.

Ebben a kiadásban az alapértelmezett Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate konstruktor elavultak jelölésű. Az új konstruktor készít TokenType argumentumként.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>2014. decemberi kiadása
### <a name="general-media-services-updates"></a>Általános Media Services-frissítések
* Egyes frissítések és az új szolgáltatások lett felvéve a Azure indexelő Media processzor. További információkért lásd: [Azure Media Indexer verzió 1.1.6.7 kibocsátási megjegyzések](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Egy új REST API, amely lehetővé teszi a kódolás frissítése szolgáltatás számára fenntartott egység hozzáadott: [EncodingReservedUnitType többi](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* A hozzáadott CORS-támogatás kulcs kézbesítési szolgáltatás.
* Teljesítménnyel kapcsolatos fejlesztések az engedélyezési házirend-beállítások lekérdezése volt történik.
* Kínai adatközpontban a [kulcs kézbesítési URL-cím](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) már egy ügyfél (akárcsak más adatközpontokban).
* A hozzáadott HLS automatikus cél időtartama. Ennek az élő Stream továbbítása során, amikor HLS mindig dinamikusan csomagolni. Alapértelmezés szerint a Media Services automatikusan kiszámítja a keyframe időköz (KeyFrameInterval) alapján, más néven képek csoport – GOP, az élő kódoló érkezett HLS szegmens csomagolás arány (FragmentsPerSegment). További információkért lásd: [működik-e az Azure Media Services élő adatfolyam].

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK frissítések
* [Az Azure Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) verziója most 3.1.0.0-s.
* .NET-keretrendszer 4.5-ös verziójának a .net SDK-függőség frissíteni.
* Hozzá egy új API, lehetővé teszi kódolási fenntartott egységek frissítését. További információkért lásd: [fenntartott egységtípus frissítése és növelése kódolás RUs .NET használatával](media-services-dotnet-encoding-units.md).
* (JSON Web Token) hozzáadott JWT jogkivonat hitelesítés támogatása. További információkért lásd: [JWT jogkivonat hitelesítés az Azure Media Services és a dinamikus titkosítás](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* A hozzáadott relatív eltolások BeginDate és ExpirationDate a PlayReady licenc sablonban.

## <a id="november_changes_14"></a>2014. novemberi kiadásban
* A Media Services most már lehetővé teszi egy élő Smooth Streaming (FMP4) tartalmak feldolgozása SSL-kapcsolaton keresztül. Betöltési SSL-en keresztül, győződjön meg arról, hogy a bemeneti URL-címet frissíteni HTTPS.  Jelenleg az AMS nem támogatja az SSL az egyéni tartomány.  További információ az élő Stream továbbítása: [működik-e az Azure Media Services élő adatfolyam].
* Jelenleg egy RTMP élő adatfolyam nem betöltési SSL-kapcsolaton keresztül.
* Akkor is csak adatfolyam SSL-en keresztül, ha a streamvégpontján, amelyről a tartalmat továbbít a 2014. szeptember 10 után lett létrehozva. Ha a streamelési URL-címek létrehozása után. szeptember 10 streamvégpontok alapul, "streaming.mediaservices.windows.net" (a új formátum) tartalmaz. Adatfolyam-továbbítási URL-címek, amelyek tartalmazzák a "origin.mediaservices.windows.net" (a régi formátumot) nem támogatja az SSL. Ha a régi formátumban kell megadni az URL-címet, és képesek lesznek streamelni SSL,-en keresztül szeretné [hozzon létre egy új streamvégpont](media-services-portal-manage-streaming-endpoints.md). Az új streamvégpont alapján létre URL-címek használatával a adatfolyamként SSL-en keresztül.

## <a id="october_changes_14"></a>2014. októberi kiadás
### <a id="new_encoder_release"></a>Media Services-kódoló kiadás
A Media Services Azure Media Encoder új kiadása bejelentése. A legújabb Azure Media Encoder van csak szó, a kimenet GB-ban, de egyébként az új kódoló az előző kódoló kompatibilis szolgáltatás. További információ [Media Services díjszabása]).

### <a id="oct_sdk"></a>Media Services .NET SDK-val
Media Services SDK for .NET-bővítmények verziója most 2.0.0.3.

Media Services SDK for .NET verziója most 3.0.0.8.

A következő módosítások lettek bevezetve:

* Újrabontása újrapróbálkozási házirend osztályokat.
* Felhasználói ügynök karakterlánca felvétele a http-kérelemfejlécekben.
* NuGet visszaállítási összeállítása lépés felvételéhez.
* Forgatókönyv tesztek x509 használandó kijavítása cert tárházból.
* Beállítások ellenőrzése csatorna frissítésekor, és adatfolyam-célból.

### <a name="new-github-repository-to-host-media-services-samples"></a>Új GitHub tárház host Media Services-minták
Minták találhatók [Azure Media Services minták GitHub-tárházban](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>2014. szeptember kiadás
Media Services REST metaadatok verziója most 2.7. A többi legújabb kapcsolatos további információkért lásd: [Azure Media Services REST API-referencia].

Media Services SDK for .NET verziója most 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Módosítások megszakítása
* **Forrás** át lett nevezve a(z) [StreamingEndpoint].
* A módosítás az alapértelmezett viselkedés használata esetén a **Azure-portálon** kódolásához, és tegye közzé az MP4-fájlokat.

Korábban, ha közzététele egy egyfájlos MP4 videó SAS URL-címet a klasszikus Azure portál segítségével hozhatók létre (SAS URL-címek lehetővé teszik a videó letöltése a blob-tároló). Jelenleg a klasszikus Azure portál használatával kódolására, és tegye közzé a egyfájlos MP4 video asset, a létrehozott URL mutat, egy Azure Media Services adatfolyam-továbbítási végpontra.  Ez a változás nincs hatással, amelyek közvetlenül Media Services feltöltött és Azure Media Services által kódolás nélkül közzétett MP4-videók.

Jelenleg a következő két lehetősége megoldani a problémát.

* Engedélyezze a folyamatos átviteli egységeket, és a dinamikus becsomagolás segítségével adatfolyamként küldje el a .mp4 eszköz zavartalan adatfolyam bemutatóként.
* Hozzon létre egy SAS URL-címet a .mp4 letöltése (vagy fokozatosan lejátszása). Az SAS-kereső létrehozásával kapcsolatos további információkért lásd: [tartalom továbbítása].

### <a id="sept_14_GA_changes"></a>Új funkciók/forgatókönyvek GA kiadás részét képező
* **Az indexelő Media processzor**. További információkért lásd: [médiafájlok indexelése az Azure Media Indexer].
* A [StreamingEndpoint] entitás mostantól lehetővé teszi egyéni tartománynevek (gazda).
  
    Egyéni tartománynév, a Media Services adatfolyam végpontnév használható kell hozzáadnia egyéni állomásnevet a adatfolyam-továbbítási végpontra. A Media Services REST API-k vagy a .NET SDK használatával adja hozzá az egyéni állomásnevet.
  
    A következők érvényesek:
  
  * Az egyéni tartománynév tulajdonjogát kell rendelkeznie.
  * Azure Media Services által is ellenőrizni kell a tartománynév tulajdonjogát. A tartomány ellenőrzéséhez hozzon létre egy CNAME rekordot, amely leképezhető <MediaServicesAccountId> <parent domain> ellenőrzése DNS. < mediaservices-dns-zóna >. 
  * Létre kell hoznia egy másik CNAME rekordot, amely az egyéni állomásnevet (pl.: sports.contoso.com) a Media Services StreamingEndpont állomásnévhez (pl.: amstest.streaming.mediaservices.windows.net).

    További információkért lásd: a **CustomHostNames** tulajdonságot a [StreamingEndpoint] cikk.

### <a id="sept_14_preview_changes"></a>Új funkciók/forgatókönyvek a nyilvános előzetes kiadás részét képező
* Élő adatfolyam-továbbítási minta. További információkért lásd: [működik-e az Azure Media Services élő adatfolyam].
* Kulcs kézbesítési szolgáltatás. További információkért lásd: [AES-128 a dinamikus titkosítás segítségével és a kulcs kézbesítési szolgáltatás].
* A dinamikus titkosítás AES. További információkért lásd: [AES-128 a dinamikus titkosítás segítségével és a kulcs kézbesítési szolgáltatás].
* PlayReady Licenctovábbítási szolgáltatásra. További információkért lásd: [használatával dinamikus PlayReady-titkosítás és Licenctovábbítási szolgáltatása].
* PlayReady dinamikus titkosítást. További információkért lásd: [használatával dinamikus PlayReady-titkosítás és Licenctovábbítási szolgáltatása].
* Media Services-PlayReady licenc sablonja. További információkért lásd: [Media Services PlayReady licenc sablon áttekintése].
* Adatfolyam-tároló titkosított eszközök. További információkért lásd: [Streaming tárolási titkosított tartalom].

## <a id="august_changes_14"></a>Augusztus 2014 kiadásban
Kódolása egy eszköz, amikor egy kimeneti eszköz elő a kódolási feladat befejezése után. Ebben a kiadásban, amíg az Azure Media Services kódoló kimeneti eszközökre vonatkozó metaadatok hozott létre. Ettől a kiadástól kezdve a kódoló is bemeneti eszközökre vonatkozó metaadatok hoz létre. További információkért lásd: a [bemeneti metaadatok] és [kimeneti metaadatok] cikkeket.

## <a id="july_changes_14"></a>2014. július kiadás
A következő hibajavításokat tartalmaz az Azure Media Services csomagoló és titkosító történtek:

* Csak hang lejátszása vissza, ha transmuxing egy élő archív eszköz HTTP Live Streaming – ez rögzített, és most hang- és a videó lejátszása közben.
* Ha egy eszköz HTTP Live Streaming és a 128 bites AES envelope titkosítás csomagolás, csomagolt adatfolyamokat nem játssza le az Android-eszközökön – a hiba kijavítása, és a csomagolt adatfolyam lejátssza Android-eszközökön, amelyek támogatják a HTTP Live Streaming.

## <a id="may_changes_14"></a>Előfordulhat, hogy a 2014 kiadásban
### <a id="may_14_changes"></a>Általános Media Services-frissítések
Ezután már használhatja [dinamikus becsomagolás] adatfolyam HTTP Live Streaming (HLS) v3. Az adatfolyam-HLS v3, a lokátor elérési útját adja hozzá a következő formátumban: * .ism/manifest(format=m3u8-aapl-v3). További információkért lásd: [Nick Drouin Blog].

A dinamikus csomagolás most is támogatja a PlayReady titkosított HLS (a v3 és a v4) kézbesítéséhez Smooth Streaming PlayReady statikusan titkosított alapján. A PlayReady Smooth Streaming titkosításának információkért lásd: [védelme Smooth Stream a Playreadyvel].

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK frissítések
A következő fejlesztéseket-e adva a Media Services .NET SDK 3.0.0.5 kiadásban:

* Nagyobb sebesség és a rugalmasság letöltésre feltöltése/media eszközök.
* Újrapróbálkozási logika és átmeneti kivételkezelést fejlesztései: 
  
  * Átmeneti hiba felderítését és újrapróbálkozási logika fejlesztettek tovább a lekérdezése, módosításainak mentése folyamatban van, vagy feltöltése a fájlok által okozott kivételeket. 
  * Webes kivételek (például során egy ACS-jogkivonat kérelem) beolvasásakor bizonyára észrevette, hogy végzetes hibák sikertelenek gyorsabban most.

További információkért lásd: [ismételje meg a logikai a Media Services SDK for .NET].

## <a id="april_changes_14"></a>2014. április kódoló kiadás
### <a name="april_14_enocer_changes"></a>Media Services-kódoló frissítések
* A tömörített választásával dolgozhat fel AVI fájlok lett létrehozva. a szerkesztővel fű Valley EDIUS nem lineáris, ahol a videó enyhén támogatása fű Valley HQ/HQX kodek. További információkért lásd: [fű Valley időről EDIUS 7 Streaming keresztül a felhő].
* A Media Encoder által a fájlok elnevezési megadásának támogatja. További információkért lásd: [Media Service kódoló kimeneti fájlnév szabályozása].
* Videó és/vagy a hang átfedések támogatása. További információkért lásd: [létrehozása átfedések].
* Szeretne összefűzni több videó szegmenseknek támogatja. További információkért lásd: [varrással videó szegmensek].
* Rögzített, ahol a hang kódolású MPEG-1 hang réteg 3 (más néven MP3) MP4 átkódolás kapcsolatos hiba.

## <a id="jan_feb_changes_14"></a>Január/február 2014 kiadások
### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1, 3.0.0.2 és 3.0.0.3
A módosítások 3.0.0.1 és 3.0.0.2 a következők:

* Az OrderBy utasítások LINQ-lekérdezések használata kapcsolatos problémák rögzített.
* Teszt megoldások vágási [GitHub] egység alapján tesztek és a forgatókönyv-alapú teszteket.

A módosításokkal kapcsolatos további információkért lásd: [3.0.0.1 és 3.0.0.2 Azure Media Services .NET SDK kiadott].

A következő változtatás történt 3.0.0.3:

* Frissített Azure storage-függőségek 3.0.3.0 verzióját használja. 
* Előző verziókkal való kompatibilitás probléma 3.0 rögzített. *.* kiadását. 

## <a id="december_changes_13"></a>2013 decembere kiadás
### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3.0.x.x kiadásokban nincsenek 2.4.x.x kiadásainak visszamenőlegesen kompatibilis.
> 
> 

A Media Services SDK legújabb verziójára már 3.0.0.0. Töltse le a legfrissebb csomagot a Nugetből, vagy a bits az beszerzése [GitHub].

A Media Services SDK verzió 3.0.0.0 verziótól kezdődően felhasználhatja a [Azure Active Directory Access Control Service (ACS)] jogkivonatokat. A "Újból felhasználja a Access Control szolgáltatás tokenek" szakaszában talál további információt a [.NET-keretrendszerhez készült Media Services a Media Services SDK-val való kapcsolódás] cikk.

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK-bővítmények 2.0.0.0-s
Az Azure Media Services .NET SDK-bővítmények olyan kiegészítő módszerek és segédfüggvények találhatók, amelyek egyszerűbbé teszik a kódolást és megkönnyítik az Azure Media Services fejlesztéséhez. A legújabb bits kaphat [Azure Media Services .NET SDK-bővítmények].

## <a id="november_changes_13"></a>A 2013. novemberi kiadásban
### <a name="nov_13_donnet_changes"></a>Azure Media Services .NET SDK változások
Ezen verziójával kezdődően a Media Services SDK for .NET hibakezelési átmeneti hiba, amely akkor fordulhat elő, amikor a Media Services REST API-réteghez.

## <a id="august_changes_13"></a>Augusztus 2013 kibocsátási
### <a name="aug_13_powershell_changes"></a>Media Services PowerShell parancsmagok Azure SDK Tools
Most már szerepel a következő Media Services PowerShell-parancsmagok [azure-sdk-eszközök].

* Get-AzureMediaServices 
  
    Például: `Get-AzureMediaServicesAccount`.
* New-AzureMediaServicesAccount 
  
    Például: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.
* New-AzureMediaServicesKey 
  
    Például: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.
* Remove-AzureMediaServicesAccount 
  
    Például: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <a id="june_changes_13"></a>2013. június kiadás
### <a name="june_13_general_changes"></a>Az Azure Media Services változások
Ebben a szakaszban említett módosítások a 2013. június Media Services kiadásokban szereplő frissítések érhetők el.

* Lehetővé teszi több storage-fiókok összekapcsolása egy Media Services-fiókját. 
  
    Tárfiók
  
    Asset.StorageAccountName és Asset.StorageAccount
* Job.Priority frissítésének lehetőségét. 
* Értesítési kapcsolatos entitásokat és tulajdonságok: 
  
    JobNotificationSubscription
  
    NotificationEndPoint
  
    Feladat
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Az Azure Media Services .NET SDK-változások
A következő módosításokat-e adva a 2013. június Media Services SDK kiadásai. A legújabb Media Services SDK a Githubon érhető el.

* A Media Services SDK-t támogatja, több tároló linking fiókok 2.3.0.0 verziójával kezdődően a Media Services-fiók. A következő API-kat támogatja ezt a szolgáltatást:
  
    A IStorageAccount típusa.
  
    A Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts tulajdonság.
  
    A StorageAccount tulajdonság.
  
    A StorageAccountName tulajdonság.
  
    További információkért lásd: [Media Services eszközök kezelése több Tárfiókok között].
* Értesítési kapcsolatos API-k. A verzió 2.2.0.0, hogy a számítógép az Azure Queue storage értesítésekre figyelésére kezdve. További információkért tekintse meg, [kezelése Media Services feladat értesítések].
  
    A Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions tulajdonság.
  
    A Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint típusa.
  
    A Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription típusa.
  
    A Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection típusa.
  
    A Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType típusa.
  
    A Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState típusa.
* Az Azure Storage-ügyfél SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll) függ.
* OData 5.5 (Microsoft.Data.OData.dll) függ.

## <a id="december_changes_12"></a>2012. decemberi kiadása
### <a name="dec_12_dotnet_changes"></a>Az Azure Media Services .NET SDK-változások
* IntelliSense: Hiányzó Intellisense dokumentációjában számos különböző hozzá.
* Microsoft.Practices.TransientFaultHandling.Core: Rögzített problémát, ha az SDK-t továbbra is rendelkezett egy régi verziója ezt a szerelvényt függőség. Az SDK-val most már hivatkozik a szerelvény 5.1.1209.1 verzióját.

A 2012. November SDK található probléma javítását:

* IAsset.Locators.Count: Ez a szám most már megfelelően jelentenek új IAsset felületek után minden keresők törölve lett.
* IAssetFile.ContentFileSize: A most már megfelelően értéke feltöltés után által IAssetFile.Upload (az elérési út).
* IAssetFile.ContentFileSize: Ez a tulajdonság most már beállítható egy eszköz-fájl létrehozásakor. Korábban csak olvasható volt.
* (Az elérési út) IAssetFile.Upload: rögzített problémát, ahol a szinkron feltöltés metódus lett kivétel a következő hiba feltöltésekor a rendszer több fájlt az eszközre. A következő hiba történt a "kiszolgáló nem tudta hitelesíteni a kérelmet. Győződjön meg arról, hogy az engedélyezési fejléc értékének formátuma helyes többek között az aláírás."
* IAssetFile.UploadAsync: Rögzített problémát, amelyben legfeljebb öt fájl tölthető fel egy időben.
* IAssetFile.UploadProgressChanged: Az SDK-val most már biztosítja ezt az eseményt.
* (Karakterlánc, BlobTransferClient, ILocator, CancellationToken) IAssetFile.DownloadAsync: Ez most valósul meg.
* IAssetFile.DownloadAsync: Rögzített problémát, amelyben legfeljebb öt fájlok letöltése sikerült egyidejűleg.
* IAssetFile.Delete(): Rögzített ahol hívó delete előfordulhat, hogy kivételt jelez, ha nincs fájl feltöltése a IAssetFile a problémát.
* Feladatok: Megtörtént egy probléma javítása ahol egy "MP4 zökkenőmentes adatfolyamok tevékenységhez" "PlayReady védelmi feladatokkal" feladat sablon használatával láncolás nem létrehoznia minden feladatot minden.
* EncryptionUtils.GetCertificateFromStore(): Ez a módszer nullhivatkozás okozta kivétel alapuló tanúsítvány konfigurációs problémák keresése egy hiba miatt már nem jelez.

## <a id="november_changes_12"></a>2012. november kiadás
Ebben a szakaszban említett módosításra a 2012. November (verzió: 2.0.0.0-s) szereplő frissítés SDK. Ezeket a módosításokat a 2012. júniusi Preview SDK módosulnak vagy írni kibocsátási írásbeli kódok lehet szükség.

* Objektumok
  
    IAsset.Create(assetName), akkor a csak eszköz létrehozása függvény. IAsset.Create már nem része a metódushívás fel a fájlokat. Használjon IAssetFile feltöltése.
  
    A IAsset.Publish és a AssetState.Publish számbavételi érték eltávolítottak belőle az SDK-t. Ez az érték a ügyfélkulcshoz tartozó kódok kell írni.
* FileInfo
  
    Ez az osztály eltávolítottak és IAssetFile cserélve.
  
    IAssetFiles
  
    IAssetFile FileInfo váltja fel, és egy másik viselkedik. A használatához az objektumpéldány IAssetFiles, majd a fájl feltöltése a Media Services SDK vagy az Azure Storage szolgáltatás SDK segítségével. A következő IAssetFile.Upload túlterhelések használhatók:
  
  * IAssetFile.Upload(filePath): Szinkron módszer, amely blokkolja a szál, és akkor javasolt, ha egy fájl feltöltése.
  * IAssetFile.UploadAsync (fájl elérési útja, blobTransferClient, lokátor, cancellationToken): egy aszinkron metódus. Ez az előnyben részesített feltöltés mechanizmusa. 
    
    Ismert hiba: a cancellationToken használatával valóban megszakítani a feltöltést; azonban a feladat megszakításának állapotának bármelyike lehet állapotok számát. Meg kell megfelelően dolgozza fel, és kivételek tud kezelni.
* Keresők
  
    A forrás-specifikus verziójával el lettek távolítva. A SAS-specifikus környezetben. Elavult, vagy eltávolította GA Locators.CreateSasLocator (eszköz, accessPolicy) lesz megjelölve. Című rész a Lokátorokat új funkciókat a frissített viselkedését.

## <a id="june_changes_12"></a>2012. júniusi előzetes kiadás
Az alábbi funkciókat lett az SDK. novemberi kiadásában új.

* Entitások törlése
  
    IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey objektumok objektumszinten, ez azt jelenti, hogy IObject.Delete() ahelyett, hogy a gyűjteményben, amely cloudMediaContext.ObjCollection.Delete(objInstance) törlési most törlődnek.
* Keresők
  
    Keresők most már léteznie kell a CreateLocator metódussal, és a LocatorType.SAS vagy LocatorType.OnDemandOrigin számbavételi értékek használja argumentumként a lokátor típusát szeretne létrehozni.
  
    Új tulajdonságok használatával könnyebben használható URI-azonosítók a tartalmat az beszerzése keresők hozzáadott. A Lokátorokat újratervezése lett kialakítva, hogy nagyobb rugalmasságot biztosítanak a jövőbeli külső bővítést és növelje a könnyebb használat media ügyfélalkalmazások esetében.
* Aszinkron metódus támogatása
  
    Aszinkron már támogatja az összes módszert.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN fórumon]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API-referencia]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services díjszabása]: http://azure.microsoft.com/pricing/details/media-services/
[bemeneti metaadatok]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[kimeneti metaadatok]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[tartalom továbbítása]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[médiafájlok indexelése az Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[működik-e az Azure Media Services élő adatfolyam]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[AES-128 a dinamikus titkosítás segítségével és a kulcs kézbesítési szolgáltatás]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[használatával dinamikus PlayReady-titkosítás és Licenctovábbítási szolgáltatása]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady licenc sablon áttekintése]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming tárolási titkosított tartalom]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://manage.windowsazure.com
[dinamikus becsomagolás]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin Blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[védelme Smooth Stream a Playreadyvel]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[ismételje meg a logikai a Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[fű Valley időről EDIUS 7 Streaming keresztül a felhő]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Media Service kódoló kimeneti fájlnév szabályozása]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[létrehozása átfedések]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[varrással videó szegmensek]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[3.0.0.1 és 3.0.0.2 Azure Media Services .NET SDK kiadott]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[.NET-keretrendszerhez készült Media Services a Media Services SDK-val való kapcsolódás]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK-bővítmények]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-eszközök]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Media Services eszközök kezelése több Tárfiókok között]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[kezelése Media Services feladat értesítések]: http://msdn.microsoft.com/library/azure/dn261241.aspx

