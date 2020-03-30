---
title: A tartalom védelme a Media Services v3 dinamikus titkosításával
titleSuffix: Azure Media Services
description: Ismerje meg a tartalomvédelmet dinamikus titkosítással, streamelési protokollokkal és titkosítási típusokkal az Azure Media Servicesben.
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
ms.date: 03/17/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c1c9440f7ec70cea98f270f04c3030c800dd0fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461112"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>A tartalom védelme a Media Services dinamikus titkosításával

Az Azure Media Services használatával biztosíthatja az adathordozó kontitúdulását attól az időponttól kezdve, amikor elhagyja a számítógépet a tároláson, a feldolgozáson és a kézbesítésen keresztül. A Media Services segítségével az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (DRM) rendszer bármelyikével dinamikusan titkosított élő és igény szerinti tartalmakat is kézbesíthet: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek hivatalos ügyfelek nek történő kézbesítéséhez. Ha a tartalom titkosítva van egy AES egyértelmű kulccsal, és HTTPS-en keresztül küldi el, akkor nem egyértelmű, amíg el nem éri az ügyfelet. 

A Media Services 3-as v3-as eszközében egy tartalomkulcs van társítva a streamelési lokátorhoz (lásd [ezt a példát).](protect-with-aes128.md) Ha a Media Services kulcskézbesítési szolgáltatását használja, lehetővé teheti, hogy az Azure Media Services hozza létre a tartalomkulcsot. A tartalomkulcsot saját magának kell létrehoznia, ha saját kulcskézbesítési szolgáltatást használ, vagy ha magas rendelkezésre állású forgatókönyvet kell kezelnie, ahol ugyanazt a tartalomkulcsot kell használnia két adatközpontban.

Amikor egy lejátszó adatfolyamot kér, a Media Services a megadott kulccsal dinamikusan titkosítja a tartalmat AES titkosítása iaes törlési kulccsal vagy DRM-titkosítással. Az adatfolyam visszafejtéséhez a lejátszó kéri a kulcsot a Media Services kulcskézbesítési szolgáltatásától vagy a megadott kulcskézbesítési szolgáltatástól. Annak eldöntéséhez, hogy a felhasználó jogosult-e a kulcs betöltődésére, a szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-házirendet.

A REST API-val vagy a Media Services-ügyféltárral engedélyezési és hitelesítési házirendeket konfigurálhat a licencekhez és kulcsokhoz.

Az alábbi kép a Media Services tartalomvédelmi munkafolyamatát mutatja be:

![A Media Services tartalomvédelmének munkafolyamata](./media/content-protection/content-protection.svg)
  
&#42; *Dynamic titkosítás támogatja az AES-128 clear key, CBCS és CENC titkosítást. További részletek a [támogatási mátrixban](#streaming-protocols-and-encryption-types)találhatók.*

Ez a cikk ismerteti azokat a fogalmakat és terminológiákat, amelyek segítenek a Tartalomvédelem megértésében a Media Services szolgáltatással.

## <a name="main-components-of-a-content-protection-system"></a>A tartalomvédelmi rendszer fő összetevői

A tartalomvédelmi rendszer sikeres befejezéséhez teljes mértékben meg kell értenie az erőfeszítés hatókörét. A következő szakaszok áttekintést nyújtanak a három részről, amelyeket végre kell hajtania.

> [!NOTE]
> Javasoljuk, hogy a következő részek rekettlisés teljes körű tesztelése előtt összpontosítson és tesztelje az alábbi szakaszok egyes részeire. A tartalomvédelmi rendszer teszteléséhez használja a szakaszokban megadott eszközöket.

### <a name="media-services-code"></a>Media Services-kód
  
A [DRM-minta bemutatja,](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) hogyan valósítható meg egy több DRM-rendszer a Media Services v3 használatával a .NET használatával. Azt is bemutatja, hogyan kell használni a Media Services licenc/kulcs kézbesítési szolgáltatás.
  
Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). Ha meg szeretné tudni, hogy mi értelme az összevonásnak, olvassa el a [Streamelési protokollok és titkosítási típusok](#streaming-protocols-and-encryption-types).

A példa bemutatja, hogyan:

1. [Tartalomkulcs-házirend](content-key-policy-concept.md)létrehozása és konfigurálása .

   Hozzon létre egy tartalomkulcs-házirendet annak konfigurálásához, hogy a tartalomkulcs (amely biztonságos hozzáférést biztosít az eszközökhöz) hogyan kerül jön létre a végfelhasználók számára:  

   * Licenckézbesítési engedély definiálása. Adja meg az engedélyezési ellenőrzés logikáját a JSON webtokenben (JWT) lévő jogcímek alapján.
   * Konfigurálja [a PlayReady,](playready-license-template-overview.md) [Widevine](widevine-license-template-overview.md)és/vagy [FairPlay](fairplay-license-overview.md) licenceket. A sablonok segítségével minden egyes DRM-hez engedélyeket és engedélyeket állíthat be.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Hozzon létre egy [streamelési lokátort,](streaming-locators-concept.md) amely a titkosított eszköz streamelésére van konfigurálva.
  
   A streamelési lokátort [egy streamelési házirendhez](streaming-policy-concept.md)kell társozni. A példában a `StreamingLocator.StreamingPolicyName` "Predefined_MultiDrmCencStreaming" politikát határoztuk meg.

   A PlayReady és widevine titkosítások alkalmazása történik, és a kulcs a konfigurált DRM-licencek alapján kerül a lejátszási ügyfélbe. Ha azt is szeretné titkosítani az adatfolyamot cbcs (FairPlay), használja a "Predefined_MultiDrmStreaming" házirendet.

   A streamelési lokátor is társítva van a megadott tartalomkulcs-házirenddel.

3. Hozzon létre egy teszttokent.

   A `GetTokenAsync` módszer bemutatja, hogyan hozhat létre egy tesztjogkivonatot.
4. A streamelési URL-cím létrehozása.

   A `GetDASHStreamingUrlAsync` módszer bemutatja, hogyan hozhat létre a streamelési URL-címet. Ebben az esetben az URL-cím streameli a DASH-tartalmat.

### <a name="player-with-an-aes-or-drm-client"></a>AES vagy DRM ügyféllel rendelkező játékos

A lejátszó SDK-n (natív vagy böngészőalapú) alapuló videolejátszó alkalmazásának az alábbi követelményeknek kell megfelelnie:

* A lejátszó SDK támogatja a szükséges DRM-ügyfeleket.
* A lejátszó SDK támogatja a szükséges streamelési protokollokat: Smooth, DASH és/vagy HTTP Live Streaming (HLS).
* A játékos SDK képes kezelni halad egy JWT token egy licenc megszerzése kérelmet.

Lejátszót az [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)használatával hozhat létre. Az [Azure Media Player ProtectionInfo API-val](https://amp.azure.net/libs/amp/latest/docs/) megadhatja, hogy melyik DRM-technológiát használja a különböző DRM-platformokon.

Az AES vagy a CENC (Widevine és/vagy PlayReady) titkosított tartalom teszteléséhez használhatja az [Azure Media Player t.](https://aka.ms/azuremediaplayer) Győződjön meg arról, hogy a **Speciális beállítások lehetőséget választja,** és ellenőrzi a titkosítási beállításokat.

Ha szeretné tesztelni FairPlay titkosított tartalom, használja [ezt a tesztlejátszót](https://aka.ms/amtest). A játékos támogatja a Widevine, PlayReady és FairPlay DRM-eket, valamint az AES-128 titkosítást.

Válassza ki a megfelelő böngészőt a különböző DRM-ek teszteléséhez:

* Chrome, Opera vagy Firefox for Widevine.
* Microsoft Edge vagy Internet Explorer 11 for PlayReady.
* Safari a FairPlay rendszerhez macOS rendszeren.

### <a name="security-token-service"></a>Biztonsági jogkivonat-szolgáltatás

Egy biztonsági jogkivonat-szolgáltatás (STS) kiadja a JWT-t a háttér-erőforrás-hozzáférés hozzáférési jogkivonataként. Használhatja az Azure Media Services licenc-/kulcskézbesítési szolgáltatás, mint a háttér-erőforrás. Az STS-nek a következő dolgokat kell meghatároznia:

* Kiállító és közönség (vagy hatókör).
* Követelések, amelyek a tartalomvédelem üzleti követelményeitől függenek.
* Szimmetrikus vagy aszimmetrikus ellenőrzés az aláírás ellenőrzéséhez.
* Kulcsváltás támogatása (ha szükséges).

Ezzel [az STS eszközzel](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) tesztelheti az STS-t. Támogatja az ellenőrzési kulcsok mindhárom típusát: szimmetrikus, aszimmetrikus vagy Azure Active Directory (Azure AD) kulcsváltással.

## <a name="streaming-protocols-and-encryption-types"></a>Streamelési protokollok és titkosítási típusok

A Media Services segítségével dinamikusan, AES-titkosítással vagy DRM-titkosítással titkosított tartalmakat vezérelhet a PlayReady, a Widevine vagy a FairPlay használatával. Jelenleg titkosíthatja a HLS, MPEG DASH és Smooth Streaming formátumokat. Minden protokoll a következő titkosítási módszereket támogatja.

### <a name="hls"></a>HLS

A HLS protokoll a következő tárolóformátumokat és titkosítási sémákat támogatja:

|Tároló formátuma|Titkosítási séma|PÉLDA URL-címre|
|---|---|---|
|Összes|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

A HLS/CMAF + FairPlay (beleértve a HEVC/H.265-öt is) a következő eszközökön támogatott:

* iOS 11-es vagy újabb rendszer.
* iPhone 8 vagy újabb készüléken.
* MacOS High Sierra Intel 7th Generation CPU-val.

### <a name="mpeg-dash"></a>MPEG-DASH

Az MPEG-DASH protokoll a következő tárolóformátumokat és titkosítási sémákat támogatja:

|Tároló formátuma|Titkosítási séma|Példák URL-címek
|---|---|---|
|Összes|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

A Smooth Streaming protokoll a következő tárolóformátumokat és titkosítási sémákat támogatja.

|Protocol (Protokoll)|Tároló formátuma|Titkosítási séma|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Böngészők

A gyakori böngészők a következő DRM-ügyfeleket támogatják:

|Böngésző|Titkosítás|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Tartalom-hozzáférés szabályozása

A tartalomkulcs-házirend beállításával szabályozhatja, hogy ki férhet hozzá a tartalomhoz. A Media Services szolgáltatásban több különböző módot is beállíthat, amelyek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. Az ügyfélnek (játékosnak) meg kell felelnie a házirendnek, mielőtt a kulcsot kézbesítené az ügyfélnek. A tartalomkulcs-házirend *nyitott* vagy *jogkivonat-korlátozást* tartalmazhat.

A nyitott, korlátozott tartalmi kulcsra vonatkozó házirend akkor használható, ha engedély nélkül szeretne licencet kiadni bárkinek. Ha például a bevétel hirdetésalapú, és nem előfizetéses.  

A jogkivonat-korlátozott tartalomkulcs-házirend esetén a tartalomkulcsot csak egy olyan ügyfélnek küldi el a rendszer, amely érvényes JWT-jogkivonatot vagy egy egyszerű webes jogkivonatot (SWT) jelenít meg a licenc-/kulcskérelemben. Ezt a jogkivonatot egy STS-nek kell kiállítania.

Használhatja az Azure AD-t STS-ként, vagy egyéni [STS-t telepíthet.](#using-a-custom-sts) Az STS-t úgy kell konfigurálni, hogy hozzon létre egy jogkivonatot a megadott kulccsal, és kiadja a jogkivonat-korlátozási konfigurációban megadott jogcímeket. A Media Services licenc-/kulcskézbesítési szolgáltatás a kért licencet vagy kulcsot adja vissza az ügyfélnek, ha mindkét feltétel fennáll:

* A token érvényes.
* A jogkivonatban szereplő jogcímek megegyeznek a licenchez vagy kulcshoz konfigurált jogcímek.

A jogkivonat-korlátozott házirend konfigurálásakor meg kell adnia az elsődleges ellenőrzési kulcsot, a kibocsátót és a közönség paramétereit. Az elsődleges ellenőrzési kulcs tartalmazza azt a kulcsot, amelyhez a jogkivonat ot aláírták. A kibocsátó az STS, amely kiadja a jogkivonatot. A célközönség, más néven hatókör, leírja a jogkivonat vagy az erőforrás, amelyhez a jogkivonat engedélyezi a hozzáférést. A Media Services licenc-/kulcskézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a jogkivonatban megegyeznek-e a sablonban szereplő értékekkel.

### <a name="token-replay-prevention"></a>Token visszajátszásának megakadályozása

A *Token Replay Prevention* funkció lehetővé teszi a Media Services-ügyfelek számára, hogy korlátozzák, hogy hányszor ugyanazt a jogkivonatot lehet használni egy kulcs vagy licenc igényléséhez. Az ügyfél hozzáadhat egy `urn:microsoft:azure:mediaservices:maxuses` típustípusú jogcímet a jogkivonatban, ahol az érték a jogkivonat licenc vagy kulcs megszerzéséhez használható számának száma. Minden további kérelmek azonos jogkivonatot kulcskézbesítés i jogosulatlan választ ad vissza. Tekintse meg, hogyan adhat hozzá a jogcímet a [DRM-mintába.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)
 
#### <a name="considerations"></a>Megfontolandó szempontok

* Az ügyfeleknek szabályozniuk kell a tokenek létrehozását. A jogcím kell helyezni a jogkivonatot is.
* A funkció használata kor, kérelmek tokenek, amelyek lejárati ideje több mint egy óra távolságra a kérelem fogadásának időpontjától elutasításra kerül egy jogosulatlan válasz.
* A tokeneket egyedileg azonosítja az aláírásuk. A hasznos adat bármilyen módosítása (például a lejárati idő frissítése vagy a jogcím) módosítja a jogkivonat aláírását, és olyan új jogkivonatnak számít, amelyet a kulcskézbesítés még nem ért el.
* A lejátszás sikertelen, ha a `maxuses` token túllépte az ügyfél által beállított értéket.
* Ez a funkció az összes meglévő védett tartalomhoz használható (csak a kibocsátott jogkivonatot kell módosítani).
* Ez a funkció a JWT-vel és az SWT-vel is működik.

## <a name="using-a-custom-sts"></a>Egyéni STS használata

Az ügyfél dönthet úgy, hogy egy egyéni STS-t használ a jogkivonatok biztosításához. Ennek okai a következők:

* Az ügyfél által használt identitásszolgáltató (IDP) nem támogatja az STS-t. Ebben az esetben egy egyéni STS lehet egy lehetőség.
* Az ügyfélnek rugalmasabb vagy szigorúbb vezérlésre lehet szüksége ahhoz, hogy integrálja az STS-t az ügyfél előfizetői számlázási rendszerébe.

   Egy OTT-szolgáltatás-üzemeltető például több előfizetői csomagot is kínálhat, például prémium, alapszintű és sportcsomagokat. [OTT](https://en.wikipedia.org/wiki/Over-the-top_media_services) Előfordulhat, hogy az operátor szeretné egyeztetni a jogcímeket egy jogkivonatban egy előfizetői csomaggal, hogy csak egy adott csomag tartalma legyen elérhető. Ebben az esetben egy egyéni STS biztosítja a szükséges rugalmasságot és vezérlést.

* Egyéni jogcímek felvétele a jogkivonatba a különböző ContentKeyPolicyOptions különböző DRM licencparaméterekkel (előfizetési licenc és a bérleti licenc) közötti választás kiválasztásához.
* A jogkivonat által hozzáférési jogot biztosít a kulcs tartalomkulcs-azonosítóját képviselő jogcím felvétele.

Egyéni STS használata esetén két módosítást kell végrehajtani:

* Amikor konfigurálja a licenckézbesítési szolgáltatás egy eszköz, meg kell adnia a biztonsági kulcsot, amelyet az egyéni STS ellenőrzése helyett az aktuális kulcsot az Azure AD.When you configure license delivery service for an asset, you need to specify the security key used for verification by the custom STS instead of the current key from Azure AD.
* JTW-jogkivonat létrehozásakor az Azure AD-ben az aktuális X509-tanúsítvány titkos kulcsa helyett egy biztonsági kulcs van megadva.

A biztonsági kulcsoknak két típusa van:

* Szimmetrikus kulcs: Ugyanaz a kulcs a JWT létrehozásához és ellenőrzéséhez használható.
* Aszimmetrikus kulcs: Az X509-tanúsítvány nyilvános és titkos kulcspárja egy titkos kulccsal titkos kulccsal titkos ítja a JWT titkosítását/generálását, a nyilvános kulccsal pedig a jogkivonat ellenőrzéséhez.

Ha fejlesztési platformként a .NET Framework/C# kapcsolót használja, az aszimmetrikus biztonsági kulcshoz használt X509-tanúsítvány kulcsának legalább 2048-nak kell lennie. Ez a kulcshossz a System.IdentityModel.Tokens.X509AsymmetricSecurityKey osztály követelménye a . Ellenkező esetben a következő kivétel jelenik meg: IDX10630: A 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' aláíráshoz nem lehet kisebb, mint a "2048" bit.

## <a name="custom-key-and-license-acquisition-url"></a>Egyéni kulcs- és licencszerzési URL

Használja az alábbi sablonokat, ha más licenc-/kulcskézbesítési szolgáltatást szeretne megadni (nem Media Services szolgáltatást). A sablonok két cserélhető mezője ott van, így megoszthatja a streamelési szabályzatot számos eszközön, ahelyett, hogy eszközenként i streamelési szabályzatot hozna létre. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: A kulcsokat a végfelhasználói lejátszóknak kézbesítő egyéni szolgáltatás URL-címének sablonja. Nem szükséges, ha az Azure Media Services kulcsok kiadásához használja. 

   A sablon támogatja a cserélhető jogkivonatokat, amelyeket a szolgáltatás futásidőben frissít a kérésre vonatkozó értékkel.  A jelenleg támogatott tokenértékek a következők:
   * `{AlternativeMediaId}`, amely et a StreamingLocatorId.AlternativeMediaId érték váltja fel.
   * `{ContentKeyId}`, amely et a kért kulcs azonosítójának értéke váltja fel.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: Sablon annak az egyéni szolgáltatásnak az URL-címéhez, amely licenceket ad a végfelhasználói játékosoknak. Nem szükséges, ha az Azure Media Services-t használja licencek kiállításához.

   A sablon támogatja a cserélhető jogkivonatokat, amelyeket a szolgáltatás futásidőben frissít a kérésre vonatkozó értékkel. A jelenleg támogatott tokenértékek a következők:  
   * `{AlternativeMediaId}`, amely et a StreamingLocatorId.AlternativeMediaId érték váltja fel.
   * `{ContentKeyId}`, amely et a kért kulcs azonosítójának értéke váltja fel. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Ugyanaz, mint az előző sablon, csak a Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Ugyanaz, mint az előző sablon, csak a FairPlay esetében.  

Példa:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId`a kért kulcs értéke. Használhatja, `AlternativeMediaId` ha a kérelmet egy entitáshoz szeretné leképezni az Ön oldalán. Például `AlternativeMediaId` az engedélyek felkelőzésének elősegítésére használható.

Az egyéni licenc-/kulcsbeszerzési URL-címeket használó REST-példákért lásd: [Streamelési házirendek – Létrehozás.](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

> [!NOTE]
> A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` hiba, győződjön meg arról, hogy adja meg a megfelelő streamelési házirendet.

Ha olyan hibákat `_NOT_SPECIFIED_IN_URL`kap, amelyek a , győződjön meg arról, hogy megadja a titkosítási formátumot az URL-ben. Például: `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Lásd: [Streamelési protokollok és titkosítási típusok.](#streaming-protocols-and-encryption-types)

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

* [Védelem AES titkosítással](protect-with-aes128.md)
* [Védelem drm-mel](protect-with-drm.md)
* [Több DRM-tartalomvédelmi rendszer kialakítása hozzáférés-vezérléssel](design-multi-drm-system-with-access-control.md)
* [Tárolási oldali titkosítás](storage-account-concept.md#storage-side-encryption)
* [Gyakori kérdések](frequently-asked-questions.md)
* [JSON webes tokenkezelő](https://docs.microsoft.com/dotnet/framework/security/json-web-token-handler)
