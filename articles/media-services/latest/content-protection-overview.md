---
title: A tartalmak Media Services dinamikus titkosítással történő ellátása – Azure | Microsoft Docs
description: Ez a cikk áttekintést nyújt a tartalom védelméről a dinamikus titkosítással. Emellett a streaming protokollokat és a titkosítási típusokat is tárgyalja.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5d31e4a523fdedf9907e33c70638f07a08461ed1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310307"
---
# <a name="content-protection-with-dynamic-encryption"></a>Tartalomvédelem dinamikus titkosítással

Az Azure Media Services segítségével az az idő, akkor hagyja, hogy a számítógép tárolási, feldolgozási és kézbesítési a médiatartalmak védelmét. A Media Services használatával dinamikusan titkosíthatja az élő és igény szerinti tartalmat Advanced Encryption Standard (AES-128) vagy a három jelentős digitális jogkezelési (DRM) rendszerből: Microsoft PlayReady, Google Widevine és Apple FairPlay. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek. 

A Media Services v3-as verziójában a tartalmi kulcs a streaming Lokátorhoz van társítva (lásd [a példát](protect-with-aes128.md)). Ha a Media Services Key Delivery Service-t használja, lehetővé teheti, hogy Azure Media Services létrehozza a tartalmi kulcsot. Ha a saját kulcsú kézbesítési szolgáltatást használja, vagy ha magas rendelkezésre állást szeretne kezelni, akkor a tartalmi kulcsnak két adatközpontban kell lennie.

Adatfolyam-lejátszó kér, amikor a Media Services a megadott kulcs használatával dinamikusan titkosítani a titkosítatlan AES vagy a DRM-titkosítás segítségével. Visszafejteni a streamet, a Windows Media player kér a kulcsot a Media Services kulcstovábbítást vagy a kulcstovábbítást megadott. Annak eldöntéséhez, hogy-e a felhasználó jogosult-e a kulcs lekérése, a szolgáltatás kiértékeli a tartalom a kulcshoz megadott kulcs házirend.

A REST API vagy egy Media Services ügyféloldali kódtárának használatával konfigurálhat a licencekkel és a kulcsok a hitelesítési és engedélyezési házirendeket.

Az alábbi ábrán a Media Services content protection munkafolyamat: 

![Tartalom védelme](./media/content-protection/content-protection.svg)

&#42;*a dinamikus titkosítás AES-128 "tiszta kulcsot" CBCS és CENC támogatja. További részletekért lásd: a támogatási mátrix [Itt](#streaming-protocols-and-encryption-types).*

Ez a cikk ismerteti az alapelvek és fogalmak ismertetése a Media Services content protection kapcsolódik.

## <a name="main-components-of-a-content-protection-system"></a>A Content Protection rendszer fő összetevői

Fejezze be a "content protection" rendszer vagy alkalmazás-tervezés, teljes ismertetése az részéről az erőfeszítés hatókörének kell. Az alábbi lista áttekintést ad végrehajtására kellene három részből áll. 

1. Az Azure Media Services-kódot
  
   A [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) -minta bemutatja, hogyan valósítható meg több DRM rendszer a Media Services v3-vel a .NET használatával. Azt is bemutatja, hogyan használható Media Services licenc/kulcs kézbesítési szolgáltatás. Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). A [streamelési protokollokkal és a titkosítási típusokkal](#streaming-protocols-and-encryption-types) kapcsolatos szakaszban megtekintheti, hogy mit mivel érdemes kombinálni.
  
   A példa bemutatja, hogyan:

   1. Hozzon létre és konfiguráljon egy [tartalmi kulcs házirendjeit](content-key-policy-concept.md). Hozzon létre egy **tartalmi kulcsra vonatkozó házirendet** , amely azt adja meg, hogy a rendszer hogyan továbbítsa a tartalom kulcsát (amely biztonságos hozzáférést biztosít az eszközeihez) a végfelhasználók számára    

      * Adja meg a licenc-kézbesítési engedélyt, amely meghatározza az engedélyezési ellenőrzések logikáját a JWT lévő jogcímek alapján.
      * [PlayReady](playready-license-template-overview.md)-, [Widevine](widevine-license-template-overview.md)-és/vagy [Fairplay](fairplay-license-overview.md) -licencek konfigurálása. A sablonok lehetővé teszik az egyes használt DRMs vonatkozó jogosultságok és engedélyek konfigurálását.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Hozzon létre egy [streaming](streaming-locators-concept.md) -keresőt, amely a titkosított eszköz továbbítására van konfigurálva. 
  
      Az **adatfolyam** -keresőt egy [folyamatos átviteli házirenddel](streaming-policy-concept.md)kell társítani. A példában a StreamingLocator. StreamingPolicyName a "Predefined_MultiDrmCencStreaming" szabályzatra van beállítva. A rendszer a PlayReady és a Widevine titkosítást alkalmazza, a kulcsot a beállított DRM-licencek alapján továbbítja a rendszer a lejátszási ügyfélnek. Ha CBCS (FairPlay) licenccel is titkosítani szeretné a streamet, használja a következőt: „Predefined_MultiDrmStreaming”.
      
      A folyamatos átviteli lokátor a definiált **tartalmi kulcs házirendjéhez** is társítva van.
    
   3. A test-token létrehozásához.

      A **GetTokenAsync** módszer bemutatja, hogyan hozzon létre egy tesztet token.
   4. A streamelési URL-cím összeállítását.

      A **GetDASHStreamingUrlAsync** metódus azt mutatja be, hogyan hozhat létre a streamelési URL-CÍMÉT. Ebben az esetben az URL-cím Streamek a **DASH** tartalmat.

2. A Player AES vagy DRM-ügyféllel. Egy videolejátszó alkalmazást, a lejátszó SDK-t (natív vagy böngészőalapú) alapján kell az alábbi követelményeknek:
   * A lejátszó SDK támogatja a szükséges DRM-ügyfelek
   * A Player SDK támogatja a szükséges folyamatos átviteli protokollokat: Smooth, DASH és/vagy HLS
   * A lejátszó SDK képesnek kell lennie a JWT jogkivonat passing licenc beszerzési kérés kezelésére
  
     A player használatával hozhat létre a [az Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Használja a [az Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) , adja meg, melyik DRM-technológiával használandó DRM különböző platformokon.

     A tesztelési AES vagy CENC (Widevine és/vagy a PlayReady) titkosított tartalmat használhatja [Azure Media Player](https://aka.ms/azuremediaplayer). Győződjön meg arról, kattintson a "Speciális beállítások", és ellenőrizze a titkosítási beállításokat.

     Ha titkosított FairPlay teszttartalmat szeretne, használja a [ezen teszt player](https://aka.ms/amtest). A Windows Media player támogatja, a PlayReady, Widevine és FairPlay DRMs, valamint az AES-128 titkosítatlan kulcs titkosítás. 
    
     A különböző DRMs teszteléséhez ki kell választania a megfelelő böngészőt: Chrome/Opera/Firefox a Widevine, Microsoft Edge/IE11 for PlayReady, Safari on macOS for FairPlay.

3. A biztonságos jogkivonat-szolgáltatás (STS), amely JSON webes jogkivonat (JWT), a hozzáférési jogkivonatot az háttérbeli erőforrások eléréséhez. Az AMS licenctovábbítási szolgáltatások a háttérerőforrásra is használhatja. Az STS szolgáltatással rendelkezik, az alábbiak megadásához:

   * Kibocsátó és a célközönség (vagy hatókör)
   * A jogcímek, ami függ a content protection az üzleti követelmények
   * A szimmetrikus vagy aszimmetrikus ellenőrzési aláírás-ellenőrzés
   * Kulcsváltás támogatás (ha szükséges)

     [Ezt az STS-eszközt](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) használhatja az STS teszteléséhez, amely a következő 3 típusú ellenőrző kulcs használatát támogatja: szimmetrikus, aszimmetrikus vagy Azure ad a Key rollover használatával. 

> [!NOTE]
> Azt javasoljuk hogy teljes körű tesztelését az egyes részek (lásd fent), és mielőtt a következő részére. A "content protection" rendszer teszteléséhez használja a fenti listában megadott eszközök.  

## <a name="streaming-protocols-and-encryption-types"></a>Adatfolyam-továbbítási protokollok és a titkosítási típusok

A Media Services segítségével dinamikusan az AES-kulcsok vagy titkosított DRM-titkosítást a PlayReady, Widevine vagy FairPlay használatával a tartalmat. Jelenleg hogy titkosítsa a HTTP Live Streaming (HLS), MPEG DASH és Smooth Streaming formátumban. Minden egyes protokollhoz a következő titkosítási módszereket támogatja:

### <a name="hls"></a>HLS

A HLS protokoll a következő tároló-formátumokat és titkosítási sémákat támogatja.

|Tároló-formátum|Titkosítási sémával|URL-példa|
|---|---|---|
|Összes|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2 – TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2 – TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

A HLS/CMAF + FairPlay (beleértve a HEVC/H. 265-et) a következő eszközökön támogatott:

* iOS v11 vagy újabb 
* iPhone 8 vagy újabb verzió
* MacOS magas Sierra az Intel hetedik Gen CPU-val

### <a name="mpeg-dash"></a>MPEG-DASH

Az MPEG-DASH protokoll a következő tároló-formátumokat és titkosítási sémákat támogatja.

|Tároló-formátum|Titkosítási sémával|URL-példák
|---|---|---|
|Összes|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

A Smooth Streaming protokoll a következő tároló-formátumokat és titkosítási sémákat támogatja.

|Protocol|Tároló-formátum|Titkosítási sémával|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Böngészők

Az általános böngészők a következő DRM-ügyfeleket támogatják:

|Browser|Encryption|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="control-content-access"></a>Tartalom-hozzáférés vezérlése

Szabályozhatja, hogy ki férhet hozzá a tartalom a content key házirend konfigurálásával. A Media Services szolgáltatásban több különböző módot is beállíthat, amelyek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalom fő házirendet kell konfigurálnia. Az ügyfélnek (lejátszó) meg kell felelnie a szabályzat, a kulcs letöltéséhez az ügyfél előtt. A tartalom kulcs házirendhez **nyissa meg a** vagy **token** korlátozás. 

A jogkivonattal korlátozott tartalom kulcs szabályzattal a tartalomkulcs van csak ügyfélnek küldött, amely egy érvényes JSON webes jogkivonat (JWT) vagy simple web Tokens (SWT) jeleníti meg a kulcs kérésben. Ez a token biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) kell kiállítaniuk. Használja az Azure Active Directory, az STS szolgáltatással, vagy egy egyéni STS üzembe helyezése. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcstovábbítást adja vissza a kért kulcs licenc az ügyfélnek, ha a jogkivonat érvényes, és a jogkivonat jogcímeiben megfelelnek a kulcs/licenc.

A tokennel korlátozott szabályzatokhoz konfigurálásakor adjon meg, hogy az elsődleges ellenőrzőkulcs, a kibocsátó és a célközönség paramétereket. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e. A kibocsátó a biztonságos jogkivonat-szolgáltatás, amely a jogkivonatot. A célközönség, más néven a hatókör, ismerteti a token szándéka, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcstovábbítást ellenőrzi, hogy ezeket az értékeket a jogkivonat egyezik a sablonban szereplő értékeket.

Az ügyfelek gyakran egyéni STS-t használnak a jogkivonatban lévő egyéni jogcímek kiválasztására a különböző DRM-licenccel rendelkező különböző ContentKeyPolicyOptions (előfizetési licencek és bérleti licencek), vagy a tartalmi kulcsot képviselő jogcímek között. annak a kulcsnak az azonosítója, amelyhez a jogkivonat hozzáférést biztosít.

### <a name="token-replay-prevention"></a>Jogkivonat-újrajátszás megelőzése

A *jogkivonat* -Visszajátszások megelőzési funkciója lehetővé teszi, hogy Media Services ügyfelek megszabják, hogy egy adott jogkivonat hányszor használható kulcs vagy licenc igénylésére. Az ügyfél hozzáadhat egy típusú `urn:microsoft:azure:mediaservices:maxuses` jogcímet a jogkivonathoz, ahol az érték az a szám, ahányszor a jogkivonat használható licenc vagy kulcs beszerzéséhez. Az ugyanazzal a jogkivonattal rendelkező összes további kérelem nem engedélyezett választ ad vissza. Lásd: a jogcím hozzáadása a DRM- [mintában](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Megfontolandó szempontok

* Az ügyfeleknek meg kell határoznia a jogkivonat-generálást. A jogcímet maga a jogkivonatban kell elhelyezni.
* A szolgáltatás használatakor a rendszer nem engedélyezett válaszként utasítja el azokat a jogkivonatokat, amelyek lejárati ideje meghaladja a kérés fogadásának idejét.
* A jogkivonatokat az aláírásuk egyedileg azonosítja. A hasznos adatok (például a lejárati idő vagy a jogcím frissítése) változása megváltoztatja a jogkivonat aláírását, és új tokenként fog megjelenni, amelyet a kulcs kézbesítése még nem észlelt.
* A lejátszás meghiúsul, ha a jogkivonat túllépte `maxuses` az ügyfél által beállított értéket.
* Ez a szolgáltatás az összes meglévő védett tartalomhoz használható (csak a kiállított jogkivonat módosítására van szükség).
* Ez a szolgáltatás a JWT és a SWT egyaránt működik.

## <a name="custom-key-and-license-acquisition-url"></a>Egyéni kulcs-és licenc-beszerzési URL-cím

Ha más kulcs-és licenc-kézbesítési szolgáltatást szeretne megadni (nem Media Services), használja a következő sablonokat. A sablonok két cserélhető mezője van, így megoszthatja az adatfolyam-szabályzatot számos eszközön, nem pedig adatátviteli szabályzatot létrehozni az eszközönként. 

* EnvelopeEncryption. CustomKeyAcquisitionUrlTemplate – az egyéni szolgáltatás URL-címének megadása a végfelhasználók számára a kulcsokat kézbesítő felhasználók számára. Nem szükséges, ha Azure Media Servicest használ a kulcsok kiadásához. A sablon támogatja a visszahelyezhető jogkivonatokat, amelyeket a szolgáltatás a kérelemhez megadott értékkel fog frissíteni futásidőben.  A jelenleg támogatott jogkivonat-értékek a következők: {AlternativeMediaId}, amelyet a rendszer a StreamingLocatorId. AlternativeMediaId és a ({ContentKeyId}) értékkel cserél, amelyet a rendszer a kért kulcs azonosítójának értékével helyettesít.
* StreamingPolicyPlayReadyConfiguration. CustomLicenseAcquisitionUrlTemplate – az egyéni szolgáltatás URL-címének kiadása a végfelhasználók számára. A licencek kiadásához Azure Media Services használata nem kötelező. A sablon támogatja a visszahelyezhető jogkivonatokat, amelyeket a szolgáltatás a kérelemhez megadott értékkel fog frissíteni futásidőben. A jelenleg támogatott jogkivonat-értékek a következők: {AlternativeMediaId}, amelyet a rendszer a StreamingLocatorId. AlternativeMediaId és a ({ContentKeyId}) értékkel cserél, amelyet a rendszer a kért kulcs azonosítójának értékével helyettesít. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate - Same as above, only for Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate - Same as above, only for FairPlay.  

Példa:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

A `ContentKeyId` (z) értékének értéke a kért kulcs, `AlternativeMediaId` a pedig akkor használható, ha a kérést az Ön oldalán lévő entitáshoz szeretné rendelni. A használható például az `AlternativeMediaId` engedélyek megkeresésének megkönnyítésére.

Az egyéni kulcs-és licenc-beszerzési URL-címeket használó REST-Példákért lásd: streaming policys [– create](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` hibaüzenet jelenik meg, győződjön meg arról, hogy a megfelelő folyamatos átviteli házirendet adta meg.

Ha a végén `_NOT_SPECIFIED_IN_URL`hibákat kap, ügyeljen arra, hogy a titkosítási formátumot az URL-címben megadja. Például: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Lásd: [adatfolyam-protokollok és titkosítási típusok](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

* [Az AES-titkosítással védeni](protect-with-aes128.md)
* [DRM védelme](protect-with-drm.md)
* [Többplatformos DRM-védelemmel ellátott tartalomkezelő rendszerek tervezése hozzáférés-vezérléssel](design-multi-drm-system-with-access-control.md)
* [Tárolási oldal titkosítása](storage-account-concept.md#storage-side-encryption)
* [Gyakori kérdések](frequently-asked-questions.md)

