---
title: A tartalmat a Media Services – Azure |} A Microsoft Docs
description: Ez a cikk áttekintést nyújt a Media Services content Protection.
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
ms.date: 02/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6fc7da067e5680af0d4e3f07c5e87be2e9592f3f
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56986039"
---
# <a name="content-protection-overview"></a>A Content protection áttekintése

Az Azure Media Services segítségével az az idő, akkor hagyja, hogy a számítógép tárolási, feldolgozási és kézbesítési a médiatartalmak védelmét. A Media Services élő és igény szerinti tartalmait az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét dinamikusan titkosított juttathat el: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek. 

Az alábbi ábrán a Media Services content protection munkafolyamat: 

![Tartalom védelme](./media/content-protection/content-protection.png)

&#42;*a dinamikus titkosítás AES-128 "tiszta kulcsot" CBCS és CENC támogatja. További részletekért lásd: a támogatási mátrix [Itt](#streaming-protocols-and-encryption-types).*

Ez a cikk ismerteti az alapelvek és fogalmak ismertetése a Media Services content protection kapcsolódik.

## <a name="main-components-of-a-content-protection-system"></a>A rendszer a content protection fő összetevőinek

Fejezze be a "content protection" rendszer vagy alkalmazás-tervezés, teljes ismertetése az részéről az erőfeszítés hatókörének kell. Az alábbi lista áttekintést ad végrehajtására kellene három részből áll. 

1. Az Azure Media Services-kódot
  
  A [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) minta bemutatja, hogyan multi-DRM-rendszer a Media Services v3 megvalósítása, és a Media Services/licenckulcs-továbbítási szolgáltatást is használhatja. Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). A [streamelési protokollokkal és a titkosítási típusokkal](#streaming-protocols-and-encryption-types) kapcsolatos szakaszban megtekintheti, hogy mit mivel érdemes kombinálni.
  
  A példa bemutatja, hogyan:

  1. Létrehozhat és konfigurálhat [Tartalomszabályzat kulcs](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

    * Adja meg a licenc kézbesítési engedélyezési, adja meg a JWT jogcímszolgáltatói alapján jogosultsági ellenőrzés logikáját.
    * DRM-titkosítás konfigurálja a tartalomkulcs megadásával.
    * Konfigurálása [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), és/vagy [FairPlay](fairplay-license-overview.md) licenceket. A sablonok segítségével beállíthatja, hogy jogosultságai és engedélyei a használt DRMs mindegyikéhez.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
  2. Hozzon létre egy [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators) megfelelően van konfigurálva, a titkosított adategység továbbításához. 
  
    A **Streamelési lokátor** kell lennie egy [Streaming-szabályzattal társított] (https://docs.microsoft.com/rest/api/media/streamingpolicies). A példában a StreamingLocator.StreamingPolicyName állítjuk a "Predefined_MultiDrmCencStreaming" szabályzat. Ez a szabályzat azt jelzi, hogy használni kívánt két tartalomkulcs (boríték és CENC) jön létre, és állítsa be a lokátor. Így az envelope, a PlayReady és a Widevine titkosítások lesznek alkalmazva (a kulcsot a konfigurált DRM-licencek alapján továbbítja a rendszer a lejátszást végző ügyfelének). Ha CBCS (FairPlay) licenccel is titkosítani szeretné a streamet, használja a következőt: „Predefined_MultiDrmStreaming”.
    
    Titkosíthatja a videót, mivel a **tartalom kulcs házirend** , hogy korábban beállított is van társítva, a **Streamelési lokátor**. 
    
  3. A test-token létrehozásához.

    A **GetTokenAsync** módszer bemutatja, hogyan hozzon létre egy tesztet token.
  4. A streamelési URL-cím összeállítását.

    A **GetDASHStreamingUrlAsync** metódus azt mutatja be, hogyan hozhat létre a streamelési URL-CÍMÉT. Ebben az esetben az URL-cím Streamek a **DASH** tartalmat.

2. A Player AES vagy DRM-ügyféllel. Egy videolejátszó alkalmazást, a lejátszó SDK-t (natív vagy böngészőalapú) alapján kell az alábbi követelményeknek:
  * A lejátszó SDK támogatja a szükséges DRM-ügyfelek
  * A lejátszó SDK-t a szükséges streamelési protokollokat támogatja: A Smooth, DASH és/vagy a HLS
  * A lejátszó SDK képesnek kell lennie a JWT jogkivonat passing licenc beszerzési kérés kezelésére
  
    A player használatával hozhat létre a [az Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). Használja a [az Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) , adja meg, melyik DRM-technológiával használandó DRM különböző platformokon.

    A tesztelési AES vagy CENC (Widevine és/vagy a PlayReady) titkosított tartalmat használhatja [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Győződjön meg arról, kattintson a "Speciális beállítások", és ellenőrizze a titkosítási beállításokat.

    Ha titkosított FairPlay teszttartalmat szeretne, használja a [ezen teszt player](https://aka.ms/amtest). A Windows Media player támogatja, a PlayReady, Widevine és FairPlay DRMs, valamint az AES-128 titkosítatlan kulcs titkosítás. 
    
    Válassza ki a megfelelő böngészőt másik DRMs teszteléséhez szüksége: Chrome/Opera/Firefox Widevine, a Microsoft Edge/IE11 a PlayReady, a Safari a fairplay rendszerhez macOS rendszeren.

3. A biztonságos jogkivonat-szolgáltatás (STS), amely JSON webes jogkivonat (JWT), a hozzáférési jogkivonatot az háttérbeli erőforrások eléréséhez. Az AMS licenctovábbítási szolgáltatások a háttérerőforrásra is használhatja. Az STS szolgáltatással rendelkezik, az alábbiak megadásához:

  * Kibocsátó és a célközönség (vagy hatókör)
  * A jogcímek, ami függ a content protection az üzleti követelmények
  * A szimmetrikus vagy aszimmetrikus ellenőrzési aláírás-ellenőrzés
  * Kulcsváltás támogatás (ha szükséges)

    Használhat [az STS eszköz](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) tesztelési STS, amely támogatja az ellenőrzőkulcs 3 típusú: szimmetrikus, aszimmetrikus vagy az Azure AD-kulcshoz kapcsolódó kulcsváltás. 

> [!NOTE]
> Azt javasoljuk hogy teljes körű tesztelését az egyes részek (lásd fent), és mielőtt a következő részére. A "content protection" rendszer teszteléséhez használja a fenti listában megadott eszközök.  

## <a name="streaming-protocols-and-encryption-types"></a>Adatfolyam-továbbítási protokollok és a titkosítási típusok

A Media Services segítségével dinamikusan az AES-kulcsok vagy titkosított DRM-titkosítást a PlayReady, Widevine vagy FairPlay használatával a tartalmat. Jelenleg hogy titkosítsa a HTTP Live Streaming (HLS), MPEG DASH és Smooth Streaming formátumban. Minden egyes protokollhoz a következő titkosítási módszereket támogatja:

|Protokoll|Tároló-formátum|Titkosítási sémával|
|---|---|---|---|
|MPEG-DASH|Összes|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Összes|AES|
||MPG2 – TS |CBCS (Fairplay) |
||MPG2 – TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="aes-128-clear-key-vs-drm"></a>AES-128-kulcsok vs. DRM

Ügyfeleink gyakran wonder, hogy használják inkább a AES-titkosítás vagy a DRM-rendszer. Az a két rendszer közötti elsődleges különbség az, hogy az AES-titkosítás a tartalomkulcs az ügyfél a hálózaton keresztül továbbított TLS, hogy a kulcs titkosítva van az átvitel során, de minden további titkosítás nélkül ("a titkosítatlan"). Ennek eredményeképpen a kulcs használatával fejti vissza a tartalom elérhető ügyfél Windows Media player számára, és tekintheti meg a hálózati nyomkövetés egyszerű szöveges az ügyfélen. AES-128-kulcsok titkosítási ideális használati esetekben, amikor a jelentésmegjelenítő egy megbízható entitás (például titkosított vállalati videók megtekintését a az alkalmazottak által a vállalaton belül terjesztett).

DRM-rendszerek, például a PlayReady, Widevine és FairPlay összes egy további titkosítási szint adja meg a kulcs használatával fejti vissza a tartalom AES-128 titkosítatlan kulcs képest. A tartalomkulcsot a DRM-modul az által védett kulcs van titkosítva, bármely átviteli fájlszintű titkosítás TLS által biztosított további. Ezenkívül a visszafejtési az operációs rendszer szintjén, ahol nehezebb a támadásokkal szemben egy rosszindulatú felhasználó biztonságos környezetben történik. DRM ajánlott használati esetek, ahol a megjelenítő lehetséges, hogy nem egy megbízható entitás, és Ön a legmagasabb szintű biztonságot követelhetnek meg.

## <a name="dynamic-encryption-and-key-delivery-service"></a>A dinamikus titkosítás és kulcstovábbítást

A Media Services v3, tartalomkulcs Streamelési lokátor társítva (lásd: [ebben a példában](protect-with-aes128.md)). Ha használja a Media Services kulcstovábbítást, hagyhatja, hogy az Ön számára a tartalomkulcs létrehozása az Azure Media Services. Kell létrehoznia a tartalomkulcs saját kezűleg használatakor, saját kulcstovábbítást, vagy ha egy magas rendelkezésre állású forgatókönyv kezel, amelyekben két adatközpontban tartalom ugyanazzal a kulccsal van szükségük.

Adatfolyam-lejátszó kér, amikor a Media Services a megadott kulcs használatával dinamikusan titkosítani a titkosítatlan AES vagy a DRM-titkosítás segítségével. Visszafejteni a streamet, a Windows Media player kér a kulcsot a Media Services kulcstovábbítást vagy a kulcstovábbítást megadott. Annak eldöntéséhez, hogy-e a felhasználó jogosult-e a kulcs lekérése, a szolgáltatás kiértékeli a tartalom a kulcshoz megadott kulcs házirend.

A Media Services DRM (PlayReady, Widevine, FairPlay) licencek és az AES-kulcsok kézbesítéséhez a hitelesített ügyfelek kulcskézbesítési szolgáltatást biztosít. A REST API vagy egy Media Services ügyféloldali kódtárának használatával konfigurálhat a licencekkel és a kulcsok a hitelesítési és engedélyezési házirendeket.

### <a name="custom-key-and-license-acquisition-url"></a>Egyéni kulcs és a licenc licenckérési URL-cím

Ha meg szeretné határozni a más kulcs és a licenc kézbesítési szolgáltatás (nem a Media Services), használja az alábbi sablonok. A sablonok két cserélhető mezőinek vannak-e, hogy a Streamelési szabályzat megosztása számos eszközök eszközönként egy adatfolyam-szabályzat létrehozása helyett. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate - sablon az egyéni szolgáltatás, amely a kulcsok végfelhasználói lejátszók URL-címe. Az Azure Media Services számára kibocsátott kulcsok használata esetén nem szükséges. A sablon támogatja a cserélhető jogkivonatok, amelyek a szolgáltatás futásidőben a kérelemre adott értékkel frissíti.  A jelenleg támogatott jogkivonat értékek: {AlternativeMediaId}, amelyet a rendszer felhasználónévre StreamingLocatorId.AlternativeMediaId és {ContentKeyId} értékét, amely helyébe a következő értékkel: a kért kulcs azonosítója.
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate - sablon az egyéni szolgáltatás, amely a licencek végfelhasználói lejátszók URL-címe. Licencek kiadása az Azure Media Services használata esetén nem szükséges. A sablon támogatja a cserélhető jogkivonatok, amelyek a szolgáltatás futásidőben a kérelemre adott értékkel frissíti. A jelenleg támogatott jogkivonat értékek: {AlternativeMediaId}, amelyet a rendszer felhasználónévre StreamingLocatorId.AlternativeMediaId és {ContentKeyId} értékét, amely helyébe a következő értékkel: a kért kulcs azonosítója. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate - Same as above, only for Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate - Same as above, only for FairPlay.  

Példa:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

A `ContentKeyId` -kérelem vonatkozik a kulcs értéke és a `AlternativeMediaId` is használható, ha azt szeretné, a kérelem leképezése egy entitás az oldalon. Ha például a `AlternativeMediaId` segítségével megkeresheti a engedélyeket is használható.

Egyéni használó REST-példák kulcs, és a licenc-licenckérési URL-címek, lásd: [Streamelési házirendek – létrehozása](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="control-content-access"></a>Tartalom-hozzáférés vezérlése

Szabályozhatja, hogy ki férhet hozzá a tartalom a content key házirend konfigurálásával. A Media Services szolgáltatásban több különböző módot is beállíthat, amelyek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalom fő házirendet kell konfigurálnia. Az ügyfélnek (lejátszó) meg kell felelnie a szabályzat, a kulcs letöltéséhez az ügyfél előtt. A tartalom kulcs házirendhez **nyissa meg a** vagy **token** korlátozás. 

A jogkivonattal korlátozott tartalom kulcs szabályzattal a tartalomkulcs van csak ügyfélnek küldött, amely egy érvényes JSON webes jogkivonat (JWT) vagy simple web Tokens (SWT) jeleníti meg a kulcs kérésben. Ez a token biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) kell kiállítaniuk. Használja az Azure Active Directory, az STS szolgáltatással, vagy egy egyéni STS üzembe helyezése. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcstovábbítást adja vissza a kért kulcs licenc az ügyfélnek, ha a jogkivonat érvényes, és a jogkivonat jogcímeiben megfelelnek a kulcs/licenc.

A tokennel korlátozott szabályzatokhoz konfigurálásakor adjon meg, hogy az elsődleges ellenőrzőkulcs, a kibocsátó és a célközönség paramétereket. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e. A kibocsátó a biztonságos jogkivonat-szolgáltatás, amely a jogkivonatot. A célközönség, más néven a hatókör, ismerteti a token szándéka, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcstovábbítást ellenőrzi, hogy ezeket az értékeket a jogkivonat egyezik a sablonban szereplő értékeket.

Ügyfeleink gyakran használják egyéni STS egyéni jogcímek foglalandó választhat másik ContentKeyPolicyOptions különböző paraméterekkel DRM licenc (egy előfizetési licenc és a egy bérleti licencet) vagy a tartalomkulcs jelölő jogcím tartalmazza a tokent a token engedélyt biztosít a kulcs azonosítója.
 
## <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

Az inaktív eszközök védelmére, titkosítani kell az eszközök által a storage ügyféloldali titkosítása. Az alábbi táblázat a storage ügyféloldali titkosítása működését mutatja a Media Services v3:

|Titkosítási beállítás|Leírás|Media Services v3|
|---|---|---|---|
|Media Services, tárolás titkosítása| AES-256 titkosítással, kulcsfontosságú a Media Services által felügyelt|Nem támogatott<sup>(1)</sup>|
|[A Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|A kiszolgálóoldali titkosítást az Azure Storage által kínált kulcs felügyelt ügyfél vagy Azure által|Támogatott|
|[Storage ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage, a Key Vault az ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|

<sup>1</sup> a Media Services v3, tárolás titkosítása (AES-256 titkosítás) van csak támogatott a visszamenőleges kompatibilitás a Media Services v2 létrehozásakor a rendszer az eszközöket. Tehát v3 együttműködik a meglévő tárhely eszközök titkosított, de nem teszi meg újakat.

## <a name="next-steps"></a>További lépések

* [Az AES-titkosítással védeni](protect-with-aes128.md)
* [DRM védelme](protect-with-drm.md)
* [A hozzáférés-vezérléssel drm-mel a content protection rendszer tervezése](design-multi-drm-system-with-access-control.md)
* [Gyakori kérdések](frequently-asked-questions.md)

