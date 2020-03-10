---
title: Media Services v3 dinamikus titkosítással védett tartalma
titleSuffix: Azure Media Services
description: További információ a dinamikus titkosítással, a folyamatos átviteli protokollokkal és a titkosítási típusokkal kapcsolatos tartalomvédelem Azure Media Services.
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
ms.date: 10/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18e80383bfcbebc6a442663c141100faa56fd061
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393472"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>A tartalmak Media Services dinamikus titkosítással védhetők

A Azure Media Services segítségével biztonságossá teheti az adathordozót, amikor a számítógépet a tárolás, a feldolgozás és a kézbesítés teljes egészében elhagyja. A Media Services élő és igény szerinti tartalmait az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek dinamikusan titkosított juttathat el: a Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek.  

A Media Services v3-as verziójában a tartalmi kulcs a streaming Lokátorhoz van társítva (lásd [a példát](protect-with-aes128.md)). Ha a Media Services Key Delivery Service-t használja, lehetővé teheti, hogy Azure Media Services létrehozza a tartalmi kulcsot. Ha saját Key Delivery Service-t használ, vagy ha magas rendelkezésre állási forgatókönyvet szeretne kezelni, akkor a tartalmi kulcsot is létre kell hoznia, ha a két adatközpontban ugyanazzal a tartalmi kulccsal kell rendelkeznie.

Adatfolyam-lejátszó kér, amikor a Media Services a megadott kulcs használatával dinamikusan titkosítani a titkosítatlan AES vagy a DRM-titkosítás segítségével. Visszafejteni a streamet, a Windows Media player kér a kulcsot a Media Services kulcstovábbítást vagy a kulcstovábbítást megadott. Annak eldöntéséhez, hogy a felhasználó rendelkezik-e a kulcs lekéréséhez szükséges jogosultsággal, a szolgáltatás kiértékeli a kulcshoz megadott tartalmi kulcs házirendjét.

A REST API vagy egy Media Services ügyféloldali kódtárának használatával konfigurálhat a licencekkel és a kulcsok a hitelesítési és engedélyezési házirendeket.

Az alábbi ábra a Media Services a tartalomvédelem munkafolyamatát szemlélteti:

![Media Services tartalomvédelem munkafolyamata](./media/content-protection/content-protection.svg)
  
&#42;A *dinamikus titkosítás támogatja az AES-128 Clear Key, CBCS és CENC használatát. Részletekért tekintse meg a [támogatási mátrixot](#streaming-protocols-and-encryption-types).*

Ez a cikk azokat a fogalmakat és terminológiát ismerteti, amelyek segítenek megérteni a tartalomvédelem Media Services.

## <a name="main-components-of-a-content-protection-system"></a>A Content Protection rendszer fő összetevői

A tartalomkezelő rendszerek sikeres befejezéséhez teljes mértékben ismernie kell a tevékenység hatókörét. A következő részek áttekintést nyújtanak a megvalósításához szükséges három részről.

> [!NOTE]
> Javasoljuk, hogy a következő részre való áttérés előtt koncentráljon és teljes mértékben tesztelje az alábbi szakaszokban szereplő részeket. A Content Protection-rendszerek teszteléséhez használja a szakaszban megadott eszközöket.

### <a name="media-services-code"></a>Media Services kód
  
A [DRM-minta](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) bemutatja, hogyan valósítható meg egy több-DRM rendszer a Media Services v3-vel a .NET használatával. Azt is bemutatja, hogyan használhatja a Media Services licenc/kulcs kézbesítési szolgáltatást.
  
Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). Ha szeretné megtekinteni, hogy mit érdemes egyesíteni, tekintse meg a [streaming protokollok és a titkosítási típusok](#streaming-protocols-and-encryption-types)témakört.

A példa bemutatja, hogyan:

1. Hozzon létre és konfiguráljon egy [tartalmi kulcsra vonatkozó házirendet](content-key-policy-concept.md).

   Hozzon létre egy tartalmi kulcsra vonatkozó házirendet, amely azt adja meg, hogy a tartalom kulcsa (amely biztonságos hozzáférést biztosít az eszközeihez) a végfelhasználók számára legyen elérhető:  

   * Adja meg a licenc kézbesítésének engedélyezését. Adja meg az engedélyezési ellenőrzési logikáját JSON Web Token (JWT) jogcímein alapuló jogcímek alapján.
   * [PlayReady](playready-license-template-overview.md)-, [Widevine](widevine-license-template-overview.md)-és/vagy [Fairplay](fairplay-license-overview.md) -licencek konfigurálása. A sablonok lehetővé teszik az egyes DRMs vonatkozó jogosultságok és engedélyek konfigurálását.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Hozzon létre egy [folyamatos átviteli lokátort](streaming-locators-concept.md) , amely a titkosított eszköz továbbítására van konfigurálva.
  
   Az adatfolyam-keresőt egy [folyamatos átviteli házirenddel](streaming-policy-concept.md)kell társítani. A példában a `StreamingLocator.StreamingPolicyName` a "Predefined_MultiDrmCencStreaming" házirendre van beállítva.

   A rendszer alkalmazza a PlayReady és a Widevine titkosítást, és a kulcsot a konfigurált DRM-licencek alapján továbbítja a rendszer a lejátszási ügyfélnek. Ha a streamet a CBCS (FairPlay) használatával is titkosítani szeretné, használja a "Predefined_MultiDrmStreaming" házirendet.

   A folyamatos átviteli lokátor a megadott tartalmi kulcs házirendjéhez is társítva van.

3. A test-token létrehozásához.

   A `GetTokenAsync` metódus azt mutatja be, hogyan lehet létrehozni egy teszt jogkivonatot.
4. A streamelési URL-cím összeállítását.

   A `GetDASHStreamingUrlAsync` metódus a streaming URL-cím összeállítását mutatja be. Ebben az esetben az URL-cím továbbítja a kötőjel tartalmát.

### <a name="player-with-an-aes-or-drm-client"></a>AES-vagy DRM-ügyféllel rendelkező lejátszó

Egy videolejátszó alkalmazást, a lejátszó SDK-t (natív vagy böngészőalapú) alapján kell az alábbi követelményeknek:

* A Player SDK támogatja a szükséges DRM-ügyfeleket.
* A Player SDK támogatja a szükséges folyamatos átviteli protokollokat: Smooth, DASH és/vagy HTTP Live Streaming (HLS).
* A Player SDK képes kezelni egy JWT-token átadását a licenc-beszerzési kérelemben.

A [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)használatával létrehozhat egy lejátszót. A [Azure Media Player PROTECTIONINFO API](https://amp.azure.net/libs/amp/latest/docs/) segítségével meghatározhatja, hogy melyik DRM-technológiát használja a különböző DRM-platformokon.

Az AES-vagy CENC-(Widevine-és/vagy PlayReady-) titkosított tartalom teszteléséhez használhatja a [Azure Media Player](https://aka.ms/azuremediaplayer). Ügyeljen arra, hogy válassza a **Speciális beállítások lehetőséget** , és ellenőrizze a titkosítási beállításokat.

Ha a FairPlay titkosított tartalmat szeretné tesztelni, használja [ezt a teszt-lejátszót](https://aka.ms/amtest). A lejátszó támogatja a Widevine, a PlayReady és a FairPlay DRMs, valamint az AES-128 titkosítatlan kulcs titkosítását.

Válassza ki a megfelelő böngészőt a különböző DRMs teszteléséhez:

* A Widevine Chrome, Opera vagy Firefox.
* Microsoft Edge vagy Internet Explorer 11 a PlayReady.
* Safari macOS-en a FairPlay.

### <a name="security-token-service"></a>Biztonsági jogkivonat szolgáltatás

A biztonságijogkivonat-szolgáltatás (STS) a háttérbeli erőforrás-hozzáférés hozzáférési JWT. A Azure Media Services licenc/kulcs kézbesítési szolgáltatását használhatja háttérként szolgáló erőforrásként. Az STS-nek a következő dolgokat kell megadnia:

* Kiállító és célközönség (vagy hatókör).
* Jogcímek, amelyek a tartalomvédelem üzleti követelményeitől függenek.
* Az aláírás-ellenőrzés szimmetrikus vagy aszimmetrikus ellenőrzése.
* A kulcsok átváltásának támogatása (ha szükséges).

[Ezt az STS-eszközt](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) használhatja az STS teszteléséhez. Az ellenőrző kulcsok mindhárom típusát támogatja: szimmetrikus, aszimmetrikus vagy Azure Active Directory (Azure AD) a kulcsok átváltásával.

## <a name="streaming-protocols-and-encryption-types"></a>Streamelési protokollok és titkosítási típusok

A Media Services segítségével dinamikusan az AES-kulcsok vagy titkosított DRM-titkosítást a PlayReady, Widevine vagy FairPlay használatával a tartalmat. Jelenleg titkosíthatja a HLS, az MPEG DASH és a Smooth Streaming formátumokat. A protokollok a következő titkosítási módszereket támogatják.

### <a name="hls"></a>HLS

A HLS protokoll a következő tároló-formátumokat és titkosítási sémákat támogatja:

|Tároló-formátum|Titkosítási sémával|URL-példa|
|---|---|---|
|Összes|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2 – TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2 – TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

A HLS/CMAF + FairPlay (beleértve a HEVC/H. 265-et) a következő eszközökön támogatott:

* iOS 11 vagy újabb verzió.
* iPhone 8 vagy újabb verzió.
* MacOS magas Sierra az Intel hetedik generációs PROCESSZORával.

### <a name="mpeg-dash"></a>MPEG-DASH

Az MPEG-DASH protokoll a következő tároló-formátumokat és titkosítási sémákat támogatja:

|Tároló-formátum|Titkosítási sémával|URL-példák
|---|---|---|
|Összes|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

A Smooth Streaming protokoll a következő tároló-formátumokat és titkosítási sémákat támogatja.

|Protokoll|Tároló-formátum|Titkosítási sémával|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Böngészők

Az általános böngészők a következő DRM-ügyfeleket támogatják:

|Böngésző|Titkosítás|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Tartalom-hozzáférés szabályozása

Szabályozhatja, hogy ki férhet hozzá a tartalom a content key házirend konfigurálásával. A Media Services szolgáltatásban több különböző módot is beállíthat, amelyek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. Az ügyfélnek (lejátszó) meg kell felelnie a szabályzat, a kulcs letöltéséhez az ügyfél előtt. A tartalmi kulcs szabályzata rendelkezhet *nyitott* vagy *jogkivonat* -korlátozással.

Nyílt hozzáférésű tartalomra vonatkozó szabályzatot akkor lehet használni, ha engedély nélkül szeretne licencet kiadni valakinek. Ha például a bevétele ad-alapú, és nem előfizetés-alapú.  

A jogkivonat-korlátozott tartalmi kulcs házirendje csak olyan ügyfélnek küldi el a tartalmat, amely érvényes JWT jogkivonatot vagy egyszerű webes jogkivonatot (SWT) biztosít a licenc/kulcs kérelmében. Ezt a tokent egy STS-nek kell kiállítania.

Az Azure AD-t STS-ként vagy [Egyéni STS](#using-a-custom-sts)üzembe helyezésével is használhatja. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services licenc/kulcs kézbesítési szolgáltatása visszaadja a kért licencet vagy kulcsot az ügyfélnek, ha mindkét feltétel teljesül:

* A jogkivonat érvényes.
* A jogkivonatban szereplő jogcímek egyeznek a licenccel vagy a kulccsal konfigurált jogcímekkel.

Ha a jogkivonat-korlátozott szabályzatot konfigurálja, meg kell adnia az elsődleges ellenőrző kulcsot, a kiállítót és a célközönség paramétereit. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e. A kibocsátó a jogkivonatot kiállító STS. A célközönség, más néven hatókör, leírja a jogkivonat szándékát vagy azt az erőforrást, amelyhez a jogkivonat hozzáférést engedélyez. A Media Services licenc/kulcs kézbesítési szolgáltatás ellenőrzi, hogy a jogkivonat értékei egyeznek-e a sablon értékeivel.

### <a name="token-replay-prevention"></a>Jogkivonat-újrajátszás megelőzése

A *jogkivonat-Visszajátszások megelőzési* funkciója lehetővé teszi, hogy Media Services ügyfelek megszabják, hogy egy adott jogkivonat hányszor használható kulcs vagy licenc igénylésére. Az ügyfél hozzáadhat egy `urn:microsoft:azure:mediaservices:maxuses` típusú jogcímet a jogkivonatban, ahol az érték az a szám, ahányszor a token használható a licencek vagy kulcsok beszerzéséhez. Az ugyanazzal a jogkivonattal rendelkező összes további kérelem nem engedélyezett választ ad vissza. Lásd: a jogcím hozzáadása a DRM- [mintában](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601).
 
#### <a name="considerations"></a>Megfontolások

* Az ügyfeleknek meg kell határoznia a jogkivonat-generálást. A jogcímet maga a jogkivonatban kell elhelyezni.
* A szolgáltatás használatakor a rendszer nem engedélyezett válaszként utasítja el azokat a jogkivonatokat, amelyek lejárati ideje meghaladja a kérés fogadásának idejét.
* A jogkivonatokat az aláírásuk egyedileg azonosítja. A hasznos adatok (például a lejárati idő vagy a jogcím frissítése) változása megváltoztatja a jogkivonat aláírását, és új tokenként fog megjelenni, amely a kulcs kézbesítése előtt nem érkezett meg.
* A lejátszás meghiúsul, ha a jogkivonat túllépte az ügyfél által beállított `maxuses` értéket.
* Ez a szolgáltatás az összes meglévő védett tartalomhoz használható (csak a kiállított jogkivonat módosítására van szükség).
* Ez a szolgáltatás a JWT és a SWT egyaránt működik.

## <a name="using-a-custom-sts"></a>Egyéni STS használata

Az ügyfél dönthet úgy, hogy egyéni STS-t használ a jogkivonatok biztosításához. Okok a következők:

* Az ügyfél által használt identitás-szolgáltató (IDENTITÁSSZOLGÁLTATÓ) nem támogatja az STS használatát. Ebben az esetben egy egyéni STS lehet egy lehetőséget.
* Az ügyfél rugalmas vagy szigorúbb vezérlőelem STS integrálhatja az ügyfél előfizető számlázórendszerrel igényelhet.

   Előfordulhat például, hogy az [ott](https://en.wikipedia.org/wiki/Over-the-top_media_services) szolgáltatás-kezelő több előfizetői csomagot is kínál, például a prémium, az alapszintű és a sport. Az operátor érdemes felel meg a jogcímeket a jogkivonatot az előfizető csomagot, hogy egy adott csomag csak a tartalom elérhetővé válnak. Ebben az esetben egy egyéni STS a szükséges rugalmasságot és irányítást biztosít.

* Ha egyéni jogcímeket szeretne szerepeltetni a jogkivonatban, válassza a különböző DRM-licenccel rendelkező különböző ContentKeyPolicyOptions (előfizetési licenc és bérleti licenc) közötti választást.
* Annak a kulcsnak a tartalmi kulcs-azonosítóját képviselő jogcím belefoglalása, amelyhez a jogkivonat hozzáférést biztosít.

Egy egyéni STS használata esetén két módosításokat kell végrehajtani:

* Az adott eszköz számára szolgáltatásra vonatkozó konfigurálásakor adja meg a biztonsági kulcs ellenőrzése az Azure AD-ből a jelenlegi kulcs helyett egyéni STS által használt kell.
* JTW jogkivonat jön létre, amikor a biztonsági kulcs helyett az aktuális X509 titkos kulcsa van megadva a tanúsítvány az Azure ad-ben.

Biztonsági kulcsok két típusa van:

* Szimmetrikus kulcs: ugyanazt a kulcsot létrehozni, és ellenőrizheti a jwt-t használja.
* Aszimmetrikus kulcs: egy nyilvános-titkos kulcspárt egy X509 a tanúsítványt használja, a titkos kulcs titkosításához/készítése a jwt-t és ellenőrizni a jogkivonatot a nyilvános kulccsal.

Ha .NET-keretrendszer / C#, a fejlesztési platform, a X509 az aszimmetrikus kulcs használt tanúsítványnak rendelkeznie kell legalább 2048 bites kulcshosszt használ. A kulcs hossza a System. IdentityModel. tokens. X509AsymmetricSecurityKey osztály követelménye a .NET-keretrendszerben. Ellenkező esetben a következő kivételt kell eldobni: IDX10630: a "System. IdentityModel. tokens. X509AsymmetricSecurityKey" az aláíráshoz nem lehet kisebb, mint "2048" bit.

## <a name="custom-key-and-license-acquisition-url"></a>Egyéni kulcs-és licenc-beszerzési URL-cím

Ha másik licenc/kulcs kézbesítési szolgáltatást szeretne megadni (nem Media Services), használja a következő sablonokat. A sablonok két cserélhető mezője van, így megoszthatja az adatfolyam-szabályzatot számos eszközön, nem pedig adatátviteli szabályzatot létrehozni az eszközönként. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: sablon azon egyéni szolgáltatás URL-címéhez, amely kulcsokat biztosít a végfelhasználók számára. A kulcsok kiadásához Azure Media Services használata nem kötelező. 

   A sablon támogatja a visszahelyezhető jogkivonatokat, amelyeket a szolgáltatás a kérelemhez megadott értékkel fog frissíteni futásidőben.  A jelenleg támogatott jogkivonat-értékek a következők:
   * `{AlternativeMediaId}`, amely a StreamingLocatorId. AlternativeMediaId értékével van lecserélve.
   * `{ContentKeyId}`, amely a kért kulcs azonosítójának értékével van lecserélve.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: sablon azon egyéni szolgáltatás URL-címéhez, amely licenceket biztosít a végfelhasználók számára. A licencek kiadásához Azure Media Services használata nem kötelező.

   A sablon támogatja a visszahelyezhető jogkivonatokat, amelyeket a szolgáltatás a kérelemhez megadott értékkel fog frissíteni futásidőben. A jelenleg támogatott jogkivonat-értékek a következők:  
   * `{AlternativeMediaId}`, amely a StreamingLocatorId. AlternativeMediaId értékével van lecserélve.
   * `{ContentKeyId}`, amely a kért kulcs azonosítójának értékével van lecserélve. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: ugyanaz, mint az előző sablon, csak a Widevine esetében. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: ugyanaz, mint az előző sablon, csak a FairPlay esetében.  

Például:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` a kért kulcs értéke. A `AlternativeMediaId` akkor használhatja, ha a kérést a saját oldalán lévő entitáshoz szeretné hozzárendelni. A `AlternativeMediaId` például segítséget nyújthat az engedélyek kereséséhez.

Az egyéni licenc/kulcs beszerzési URL-címeket használó REST-Példákért lásd: streaming policys [– create](https://docs.microsoft.com/rest/api/media/streamingpolicies/create).

> [!NOTE]
> A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` hibaüzenetet kap, győződjön meg arról, hogy a megfelelő folyamatos átviteli házirendet adta meg.

Ha `_NOT_SPECIFIED_IN_URL`kal végződik hibákat kap, ügyeljen arra, hogy a titkosítási formátumot az URL-címben megadja. Például: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Lásd: [adatfolyam-protokollok és titkosítási típusok](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

* [Védelem AES-titkosítással](protect-with-aes128.md)
* [Védelem a DRM-mel](protect-with-drm.md)
* [Többplatformos DRM-védelemmel ellátott tartalomkezelő rendszerek tervezése hozzáférés-vezérléssel](design-multi-drm-system-with-access-control.md)
* [Tárolási oldal titkosítása](storage-account-concept.md#storage-side-encryption)
* [Gyakori kérdések](frequently-asked-questions.md)
* [JSON Web Token kezelő](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
