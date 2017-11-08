---
title: "CENC Multi-DRM-és hozzáférés-vezérlés: egy hivatkozás tervezési és megvalósítási Azure és az Azure Media Services |} Microsoft Docs"
description: "További tudnivalók a Microsoft® Smooth Streaming ügyfél eljárás Kit licencelésre."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.openlocfilehash: e4a53d053a4c792f54e215c19a8f0c4064815839
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC többplatformos DRM és hozzáférés-vezérlés használatával: Egy referenciaterv és megvalósítás az Azure-on és az Azure Media Services szolgáltatásban
 
## <a name="introduction"></a>Bevezetés
Jól ismert, hogy a rendszer egy összetett feladat, tervezése és az OTT DRM alrendszer építése vagy online streamelési megoldások. És egy általános gyakorlat, ez a kijelző speciális DRM-szolgáltatók kihelyező operátorok/online videó szolgáltatók. Ez a dokumentum célja van a referencia-Tervező és végpont DRM alrendszer OTT vagy online adatfolyam megoldás megvalósítása.

A jelen dokumentum a célzott olvasók működik-e OTT online streaming/több-screen megoldások és bármely DRM alrendszer iránt érdeklődik olvasók DRM alrendszer a mérnökök. A feltételezi, hogy olvasók jártas a DRM technológiák, például a PlayReady, Widevine, FairPlay vagy Adobe hozzáférés a piacon közül legalább egy.

DRM is magában foglalja az CENC (Common Encryption) és multi-DRM. Egy online streamelésre és az OTT iparági fő trendet, hogy az CENC a több-native-DRM különböző a ügyfélplatformokon, amely egy előző trendjét egyetlen DRM és az ügyfél SDK használatával a ügyfélplatformokon különböző a shift. Ha több native DRM CENC használ, mind a PlayReady, mind a Widevine titkosítása a [Common Encryption (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) megadását.

A multi-DRM CENC előnyei a következők:

1. Csökkenti a költségeket, mert egy egyetlen titkosítási feldolgozási célzó különböző platformokon a natív DRMs; a használt titkosítási
2. Csökkenti a költségeket a titkosított eszközök kezelésére, mert csak egy példányát a titkosított eszközökre van szükség;
3. Megszünteti a licencelési költségek, mivel a natív DRM-ügyfél általában a natív platformon szabad DRM-ügyfél.

Microsoft lett egy aktív hatnak DASH és CENC néhány fő iparági lejátszó együtt. A Microsoft Azure Media Services DASH és CENC támogatásának biztosítása rendelkezik lett. Friss közlemények talál Mingfei tartozó blogok: [bejelentése Google Widevine licenctovábbítási szolgáltatások az Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/), és [Azure Media Services hozzáadja a Google Widevine-csomagolás multi-DRM adatfolyam kézbesítéséhez](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Ez a cikk áttekintése
Ez a cikk célja az alábbiakat tartalmazza:

1. Biztosít egy referencia-Tervező DRM alrendszer CENC használata multi-DRM;
2. Ez a témakör egy referencia-megvalósításban a Microsoft Azure vagy az Azure Media Services platform;
3. Néhány tervezési és megvalósítási témaköre ismerteti.

A cikk "multi-DRM" a következőket tartalmazza:

1. Microsoft PlayReady
2. Google Widevine
3. Apple FairPlay 

A következő táblázat összefoglalja a natív platform/natív alkalmazás, és minden DRM által támogatott böngészők.

| **Ügyfélplatform** | **Natív DRM-támogatás** | **Böngészőalkalmazás /** | **Formátumban** |
| --- | --- | --- | --- |
| **Intelligens televízió, operátor vezérléséhez, OTT vezérléséhez** |PlayReady, és/vagy Widevine, és/vagy más |Linux, az Opera, WebKit, más |Különböző formátumokba |
| **Windows 10 rendszerű eszközökhöz (Windows-Számítógépet, a Windows rendszerű táblagépek, Windows Phone, Xbox)** |PlayReady |Peremhálózati/IE11/EME MS<br/><br/><br/>UWP |KÖTŐJEL (a HLS, PlayReady nem támogatott)<br/><br/>DASH, Smooth Streaming (HLS a, a PlayReady nem támogatott) |
| **Android-eszközök (telefon, táblagép, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **(iPhone-, iPad-) iOS, OS X-ügyfelek és az Apple TV** |FairPlay |Safari 8 / EME |HLS |


Központi telepítés minden egyes DRM aktuális állapotát, figyelembe véve a szolgáltatás általában akkor érdemes 2 vagy 3 DRMs ellenőrizze, hogy meg végpontok összes típusú cím a legmegfelelőbb módon történő végrehajtásához.

Nincs a szolgáltatás logika összetettsége és ügyféloldali felhasználói felület a különböző ügyfelek bizonyos szintű eléréséhez összetettségét közötti kompromisszumot.

Ahhoz, hogy a kijelölés, tartsa szem előtt ezeket a tények:

* PlayReady natív módon valósul meg minden egyes Android-eszközökön, és a szoftver SDK-k szinte bármilyen platformon keresztül elérhető Windows-eszköz
* Minden Android-eszköz, a Chrome-ban, és néhány egyéb eszközök Widevine natív módon történik.
* FairPlay csak iOS és Mac OS ügyfelek vagy iTunes érhető el.

Egy tipikus multi-DRM így néz ki:

* 1. lehetőség: PlayReady és Widevine
* 2. lehetőség: A PlayReady, Widevine és FairPlay

## <a name="a-reference-design"></a>A referencia-Tervező
Ebben a szakaszban egy hivatkozás tervezési, amely független megvalósítása használt technológiák jelent azt.

A DRM alrendszer a következő összetevőket tartalmazza:

1. Kulcskezelés
2. DRM-csomag
3. DRM-licenckézbesítés
4. Jogosultság ellenőrzése
5. Hitelesítési/engedélyezési
6. Player
7. Forrás/CDN

A következő ábra szemlélteti a magas szintű interakció az DRM alrendszer összetevői között.

![CENC DRM alrendszer](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

Az alábbi három alapvető "réteg" modelljében:

1. Irodai réteghez, amely nem érhetők el külsőleg; (a fekete)
2. Az összes elérhető nyilvános; végpontok "DMZ" réteg (kék)
3. Nyilvános Internet (könnyű kék) tartalmazó réteget CDN és a forgalom lejátszó nyilvános interneten keresztül.

A Tartalomkezelés eszköz DRM-védelem szabályozása kell lennie, függetlenül attól statikus vagy dinamikus titkosítást. A bemenetek DRM titkosításhoz kell tartalmaznia:

1. MBR videotartalom;
2. Tartalomkulcs;
3. Licenc-licenckérési URL-címeket.

A lejátszás idő alatt a magas szintű folyamata a következő:

1. Felhasználó hitelesítése;
2. Engedélyezési jogkivonatot hoz létre a felhasználó;
3. DRM védett tartalmat (jegyzékfájl) a rendszer letölti player;
4. Player elküldi a licenc beszerzési kérelem licenckiszolgálókat kulcs azonosítója és engedélyezési jogkivonat.

Mielőtt továbblép a következő témakörben talál néhány szót kulcskezelést kialakításának kapcsolatos.

| **ContentKey – eszköz** | **A forgatókönyv** |
| --- | --- |
| 1 – 1 |A legegyszerűbb eset. A Legfinomabb felügyeletét biztosítja azt. Azonban ez általában annak az eredménye a legmagasabb licenc kézbesítési költséggel jár. A minimális egy-egy licencet kérelme, mert minden védett eszköz szükséges. |
| 1 – a-többhöz |Több eszköz használhatja ugyanazt a tartalom kulcsot. Például az összes az eszköz a logikai csoport például a genre vagy genre (vagy Movie gén) részhalmazát, használhat egy tartalomkulcsot. |
| Több-a-1 |Több tartalomkulcs minden eszköz szükséges. <br/><br/>Például ha szeretné alkalmazni, dinamikus CENC-védelem és multi-DRM a MPEG-DASH, HLS dinamikus AES-128 titkosítást, kell két külön tartalomkulcs, külön-külön ContentKeyType. (A tartalomkulcsot a dinamikus CENC védelemhez használt ContentKeyType.CommonEncryption kell használni, a dinamikus AES-128 titkosítást használja a tartalomkulcsot, ContentKeyType.EnvelopeEncryption kell használni.)<br/><br/>Egy másik példa CENC védelmi DASH tartalom elméletileg egy tartalomkulcsot a video-adatfolyamot és hangadatfolyam védelmét egy másik tartalomkulcsot védelmére használható. |
| Több-az - sok |A fenti két esetben kombinációja: tartalomkulcs egy készletét használják az egyes az a "csoport" ugyanazon objektumhoz több eszközök. |

Egy másik fontos szempont az, hogy az állandó, és nem állandó licencek használatát.

Miért fontosak ezeket a szempontokat?

Nyilvános felhő licenc kézbesítésre használatakor rendelkeznek közvetlen gyakorolt hatás licenc kézbesítési költség. Mérlegeljük, a következő két különböző kialakítási esetekben mutatja be:

1. Havi előfizetést: állandó licenccel, illetve 1-a-többhöz tartalom kulcs eszköz leképezés. Például a gyermekek filmeket a titkosításhoz egyetlen tartalomkulcs használjuk. Ebben az esetben:

    Minden gyermekek filmek vagy eszköz kért # licencek teljes száma = 1
2. Havi előfizetést: nem állandó licenccel, illetve 1-1 leképezési tartalomkulcsot és eszköz közötti. Ebben az esetben:

    Minden gyermekek filmek vagy eszköz kért # licencek teljes száma = [# filmek figyelt] x [# munkamenetek]

Könnyen látható, a két különböző kialakításokról eredményezheti, ezért nagyon különböző licenc kérelem minták licenc kézbesítési költségeket, ha a nyilvános felhők, például az Azure Media Services licenctovábbítási szolgáltatása biztosítja.

## <a name="mapping-design-to-technology-for-implementation"></a>Végrehajtási technológiák tervezési leképezése
A következő azt rendelje az általános tervezési technológiák a Microsoft Azure vagy az Azure Media Services platform, mely technológia minden építőelem használandó megadásával.

Az alábbi táblázat a leképezés:

| **Építőelem** | **Technológia** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Az identitásszolgáltató (IDP)** |Azure Active Directory |
| **Biztonságos biztonságijogkivonat-szolgáltatás (STS)** |Azure Active Directory |
| **DRM Protection munkafolyamata** |Azure Media Services dinamikus védelme |
| **DRM licenc kézbesítés** |1. Az Azure Media Services-licenc kézbesítési (PlayReady, Widevine, FairPlay), vagy <br/>2. Axinom licenckiszolgáló, <br/>3. Egyéni PlayReady licenckiszolgáló |
| **Forrás** |Az Azure Media Services-Streamvégpont |
| **Kulcskezelés** |A hivatkozás végrehajtása esetén nem szükséges |
| **Tartalomkezelés** |A C# Konzolalkalmazás |

Identity Provider (IDP) és a Secure Token Service (STS) más szóval lesz az Azure AD. Player, használjuk [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). Az Azure Media Services és Azure Media Player támogatják a KÖTŐJELET és CENC multi-DRM.

Az alábbi ábrán látható, az általános struktúra és a fenti technológia hozzárendelési folyamat.

![Az AMS CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Dinamikus CENC titkosítási beállításához a Tartalomkezelés eszköz használja az alábbi adatokat:

1. Nyissa meg a tartalom;
2. A következő kulcsból: tartalomkulcsot generációs/felügyeleti;
3. Licenc-licenckérési URL-címek;
4. Azure ad-információk listáját.

A Tartalomkezelés eszköz kimenete a következő lesz:

1. Hogyan licenc kézbesítési ellenőrzi a JWT jogkivonat és DRM licenc specifikációk; a meghatározás tartalmazó ContentKeyAuthorizationPolicy
2. A AssetDeliveryPolicy tartalmazó beállításokat a streaming formátumban, DRM-védelem és licenc-licenckérési URL-címeket.

Futásidőben, a folyamat van, mint alatt:

1. Felhasználók hitelesítése után a JWT jogkivonat jön létre;
2. A JWT jogkivonat lévő jogcímek egyik tartalmazó "EntitledUserGroup" csoport Objektumazonosítója "csoportok" jogcímet. Ezt az igényt az "jogosultság ellenőrzése" továbbításához használható.
3. Egy CENC Player letöltések ügyfél jegyzékében védett tartalmakat, és "látja" a következő:

   1. kulcs azonosítója,
   2. védett, CENC tartalma
   3. Licenc-licenckérési URL-címeket.
4. Player támogatott böngésző/DRM alapján licenc beszerzési kérelmet küld. A licenc beszerzési kérelemben kulcs azonosítója és a JWT jogkivonat is elküldése megtörtént. Licenctovábbítási szolgáltatása ellenőrzi, hogy a JWT jogkivonat, illetve a szükséges licenccel kiadása előtt lévő jogcímeket.

## <a name="implementation"></a>Megvalósítás
### <a name="implementation-procedures"></a>Eljárások végrehajtása
A végrehajtása a következő lépéseket tartalmazza:

1. Készítse elő a teszt eszköz(ök): kódolása/csomag egy teszt videót, amely többszörös sávszélességű MP4 Azure Media Services töredezett. Ez az eszköz nincs DRM-védelemmel. DRM-védelem később dinamikus védelmi végezhető el.
2. Kulcs azonosítója és a tartalom létrehozása (nem kötelező a kulcs kezdőérték) kulcs. A célra kulcskezelés rendszer nincs szükség, mert jelenleg csak egyetlen halmazába foglalkoznak azonosítója és a vizsgálati eszközök; több tartalomkulcs
3. AMS API használatával multi-DRM-licenctovábbítási szolgáltatások a vizsgálati eszköz konfigurálja. A vállalat vagy a vállalat forgalmazók Azure Media Services szolgáltatások helyett egyéni licenc kiszolgálókat használ, ha kihagyja ezt a lépést, és adja meg a licenc-licenckérési URL-címek konfigurálásához licenc kézbesítési lépésben. AMS API szükséges adja meg az egyes részletes konfigurációkat, például az engedélyezési házirend-korlátozás, a licenc válasz sablonok DRM-licenc különböző szolgáltatások, stb. Ekkor az Azure-portál nem még adja meg a szükséges felhasználói felület ehhez a konfigurációhoz. API-szintű adatainak megkeresése és a mintakódot Ágnes Kornich dokumentumban: [segítségével PlayReady és/vagy Widevine Dynamic Common Encryption](media-services-protect-with-drm.md).
4. AMS API használatával konfigurálja az adategység továbbítási házirendjét a vizsgálati eszköz. API-szintű adatainak megkeresése és a mintakódot Ágnes Kornich dokumentumban: [segítségével PlayReady és/vagy Widevine Dynamic Common Encryption](media-services-protect-with-drm.md).
5. Hozza létre és konfigurálja az Azure Active Directory-bérlő az Azure rendszerben;
6. Néhány felhasználói fiókok és csoportok létrehozása az Azure Active Directory-bérlőhöz: legalább hozzon létre "EntitledUser" csoport és a felhasználó hozzáadása ehhez a csoporthoz. A csoportban található felhasználók licenc a beszerzési fogja továbbítani jogosultság ellenőrzése és a csoportba nem tartozó felhasználók felelt meg a hitelesítési ellenőrzés sikertelen lesz, és nem fogja tudni bármely licenc beszerzésére. A "EntitledUser" csoport tagjaként a szükséges "csoportok" jogcím a JWT jogkivonat Azure ad ki. Ez a követelmény jogcím multi-DRM licenc delivery services lépés konfigurálása kell megadni.
7. Hozzon létre egy ASP.NET MVC alkalmazást, amely a videólejátszó szolgáltató. Az ASP.NET-alkalmazás az Azure Active Directory-bérlő felhasználóhitelesítés védelemmel látja el. Megfelelő jogcímek a felhasználó hitelesítése után kapott hozzáférési jogkivonatok fognak szerepelni. Ez a lépés ajánlott OpenID Connect API. Telepítenie kell a következő NuGet-csomagok:
   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
8. Hozzon létre egy player használt [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). [Az Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) megadhatja a használandó különböző DRM platformon DRM technológiákat.
9. Teszt mátrix:

| **DRM** | **Böngésző** | **Jogosult felhasználó eredménye** | **Nem jogosult felhasználó eredménye** |
| --- | --- | --- | --- |
| **PlayReady** |MS peremhálózati vagy IE11 Windows 10 rendszeren |Sikeres |Sikertelen |
| **Widevine** |A Windows 10 Chrome |Sikeres |Sikertelen |
| **FairPlay** |TBD | | |

Az Azure Media Services csapatától George Trifonov írás biztosít az ASP.NET MVC player alkalmazásoknak az Azure Active Directory beállításának részletes lépéseket blog: [integrálni Azure Media Services OWIN MVC alkalmazás Azure Active Directory-alapú, és korlátozhatja a JWT jogcímei alapján kulcs tartalomkézbesítési](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

George is írás blog a [JWT jogkivonat hitelesítés az Azure Media Services és a dinamikus titkosítás](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Információk az Azure Active Directoryban:

* A fejlesztői információt [Azure Active Directory fejlesztői útmutatója](../active-directory/active-directory-developers-guide.md).
* A rendszergazda információt [felügyelheti az Azure AD-címtár](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Néhány gotchas végrehajtása
A megvalósítása bizonyos "gotchas" szerepelnek. Remélhetőleg az alábbi listán szereplő "gotchas" segítséget hibaelhárítási abban az esetben, ha problémákat tapasztal.

1. **Kibocsátó** URL-címet kell végződnie **"/"**.  

    **A célközönség** kell lennie a player alkalmazás ügyfél-azonosító és is fel kell **"/"** a kibocsátó URL-cím végén.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    A [JWT dekóder](http://jwt.calebb.net/), megtekintheti az **és** és **iss** a JWT jogkivonat az alábbiak szerint:

    ![1. gotcha](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)
2. Engedélyek hozzáadása az alkalmazást az aad-ben (a konfigurálás lap az alkalmazás). Ez azért szükséges, az egyes alkalmazásokhoz (helyi és a telepített verzió).

    ![2. gotcha](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)
3. A dinamikus CENC védelem beállítása a helyes kibocsátói használja:

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    A következő nem működik:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    A GUID az az AAD-bérlő azonosítója. A GUID-azonosító az Azure portálon található végpontok előugró.
4. Támogatás csoporttagság jogosultságokat jogcímek. Ellenőrizze, hogy az aad-ben Alkalmazásjegyzék-fájl, az alábbiakkal

    "groupMembershipClaims": "All", (az alapértelmezett értéke null)
5. A beállítás megfelelő TokenType korlátozás követelmények létrehozásakor.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Támogatja a jwt-t (AAD) mellett SWT (ACS) ad hozzá, mert az TokenType alapérték TokenType.JWT. SWT/ACS használatakor TokenType.SWT kell beállítania.

## <a name="additional-topics-for-implementation"></a>Kiegészítő témakörök az végrehajtása
Ezután azt fogja lemezre uss néhány kiegészítő témakörök a tervezési és megvalósítási.

### <a name="http-or-https"></a>HTTP vagy HTTPS?
A beépített Microsoft ASP.NET MVC lejátszóalkalmazás a következőket kell támogatnia:

1. A HTTPS; kell az Azure AD felhasználói hitelesítést
2. JWT jogkivonat exchange ügyfél és a HTTPS; kell az Azure AD között
3. Az ügyfél, amely azonban szükséges lehet a HTTPS, ha az Azure Media Services által biztosított licenc kézbesítési a DRM a licenc beszerzése. Természetesen PlayReady termékcsomag határozza meg, hogy HTTPS licenc kézbesítésre. Ha a PlayReady licenckiszolgáló Azure Media Services kívül esik, HTTP vagy HTTPS használható.

Ezért az ASP.NET lejátszóalkalmazás fogja használni az HTTPS az ajánlott eljárás. Ez azt jelenti, hogy az Azure Media Player lesz a HTTPS oldalon. Azonban az adatfolyamként történő azt részesítsék előnyben a HTTP, ezért fontolja meg a vegyes tartalom problémát kell.

1. Böngésző vegyes tartalom nem teszi lehetővé. De a beépülő modulok, például a Silverlight- és OSMF beépülő modul a smooth, valamint a kötőjel. Vegyes tartalom kapcsolatos egyik biztonsági szempont,-miatt a fenyegetését, hogy a rosszindulatú JS, ami az ügyféladatok lehetnek kitéve okozhat új funkció lehetővé teszi.  Böngészők blokkolják a ez alapértelmezés szerint, és amennyiben csak kerülő, a kiszolgáló (forrás) oldalán, hogy az összes tartomány (függetlenül attól, hogy https vagy http). Ez az valószínűleg nem jó ötlet vagy.
2. Azt a vegyes tartalom kerülendő: mindkettő használja a HTTP vagy HTTPS használatára is. Vegyes tartalom lejátszását, amikor a silverlightSS technológia szükséges, vegyes tartalom figyelmeztetés törlése. flashSS műszaki vegyes tartalom vegyes tartalom figyelmeztetés nélkül kezeli.
3. Ha a streamvégpontján augusztus 2014 előtt készült, nem fogja támogatni HTTPS. Ebben az esetben hozzon létre és használjon egy új streamvégpont HTTPS-hez.

Hivatkozás megvalósításában DRM védett tartalmakat, az alkalmazás- és adatfolyamként történő továbbítását fogja tudni HTTTPS. Tartalmának megnyitása a Windows Media player nem kell hitelesítési vagy licencelési, így a liberty HTTP vagy HTTPS használatával kell.

### <a name="azure-active-directory-signing-key-rollover"></a>Az Azure Active Directory aláírási kulcs váltása
Ez az egy fontos eleme a megvalósítás figyelembe. Ha nem ez a megvalósítás a, a befejezett rendszer végül leállnak teljesen legfeljebb 6 héten belül.

Az Azure AD iparági szabvány használatával saját magát, és az alkalmazások az Azure AD közötti megbízhatósági kapcsolat létrehozása. Pontosabban az Azure AD nyilvános és titkos kulcsból álló kulcspárt tartalmazó aláíró kulcsot használ. Az Azure AD létrehoz egy biztonsági jogkivonatot, amely a felhasználói információkat tartalmaz, ez a token aláírását Azure ad használatával a titkos kulcsot, az alkalmazásnak elküldés előtt. Győződjön meg arról, hogy a jogkivonat érvényes, és ténylegesen kezdeményezésű az Azure AD, hogy az alkalmazás ellenőrizni kell a jogkivonat aláírása, amely a bérlői összevonási metaadatok dokumentum tartalmazza az Azure AD által elérhetővé tett nyilvános kulccsal. A nyilvános kulcsot – és az aláírási kulcs, amelyből származik – megegyezik egy a egyetlen bérlő számára az Azure ad-ben.

A dokumentumban található részletes adatait az Azure AD kulcsváltás: [aláírási kulcs átfordulási az Azure AD vonatkozó fontos információk](../active-directory/active-directory-signing-key-rollover.md).

Között a [nyilvános-titkos kulcsból álló kulcspárt](https://login.microsoftonline.com/common/discovery/keys/),

* A titkos kulcs segítségével Azure Active Directory JWT jogkivonat; előállítása
* A nyilvános kulcs segítségével egy alkalmazás, például DRM-Licenctovábbítási szolgáltatások AMS a JWT jogkivonat;

Biztonsági célra az Azure Active Directory forog ezt a tanúsítványt rendszeres időközönként (6 hét). Biztonsági problémák esetén a kulcsváltás akkor fordulhat elő, amikor. Ezért a licenctovábbítási szolgáltatások az AMS frissítenie kell az Azure AD forog a kulcspár, ellenkező esetben az AMS tokent használó hitelesítés sikertelen lesz, és a licenc nem lesznek kiállítva használt nyilvános kulcsot.

Ez TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument úgy, hogy DRM-licenctovábbítási szolgáltatások konfigurálása esetén érhető el.

A JWT jogkivonat-folyamata van, mint alatt:

1. Az Azure AD a JWT jogkivonat állít ki, a jelenlegi titkos kulccsal, a hitelesített felhasználók;
2. Amikor egy player egy CENC tartalmú multi-DRM-védelemmel látja, először megkeresi a JWT jogkivonat Azure ad ki.
3. A Windows Media player a JWT jogkivonat licenc beszerzési kérelmet küld licenctovábbítási szolgáltatások az AMS;
4. Az AMS a licenctovábbítási szolgáltatások a jelenlegi érvényes nyilvános kulcsot az Azure AD segítségével ellenőrizze a JWT jogkivonat licencek kiadása előtt.

DRM-licenctovábbítási szolgáltatások mindig keresi a jelenlegi érvényes nyilvános kulcsot az Azure AD. A nyilvános kulcsot az Azure AD által használt Azure AD által kibocsátott JWT jogkivonat ellenőrzése kulcs lesz.

Mi történik, ha a kulcshoz kapcsolódó kulcsváltás után AAD JWT jogkivonat állít elő, de előtt a JWT jogkivonat által küldött játékosok DRM-licenctovábbítási szolgáltatások az AMS ellenőrzési célból történő történik?

A kulcs bármikor előfordulhat, hogy állítva, mert nincs mindig egynél több érvényes nyilvános kulccsal elérhető az összevonási metaadatok dokumentumban. Az Azure Media Services licenc kézbesítési a dokumentumban megadott kulcsok segítségével egy kulcs amint lehet, hogy lesz vonva, mivel egy másik előfordulhat, hogy azok helyettesítéseivel, és így tovább.

### <a name="where-is-the-access-token"></a>Hol található a hozzáférési jogkivonat?
Ha tekinti meg hogyan webalkalmazás hívja az API-alkalmazás [OAuth 2.0 ügyfél hitelesítő adatok megadása az Alkalmazásidentitás](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), a hitelesítési folyamat van, mint alatt:

1. A felhasználó van bejelentkezve az Azure AD-t a webes alkalmazás (lásd a [webböngészőt a webes alkalmazás](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application).
2. Az Azure AD-engedélyezési végpont átirányítja a felhasználói ügynök az ügyfélalkalmazás engedélyezési kóddal. A felhasználói ügynök engedélyezési kódot az ügyfél-alkalmazás átirányítási URI-t adja vissza.
3. A webalkalmazásnak kell szerezni a hozzáférési tokent, hogy a webes API-t a hitelesítéshez és a kívánt erőforrás lekérése. Ez egy kérést küld az Azure AD token-végpont, a hitelesítő adatokat, ügyfél-Azonosítót és webes API-alkalmazás URI azonosítója. Azt mutatja be, hogy a felhasználó hozzájárult igazolásához engedélyezési kódot.
4. Az Azure AD hitelesíti az alkalmazást, és adja vissza a JWT jogkivonat, amellyel a webes API hívása.
5. A HTTPS PROTOKOLLOKON keresztül a webes alkalmazás használatával az visszaadott JWT jogkivonat a kérés hitelesítési fejlécéhez a "Tulajdonos" megjelölés JWT karakterlánc hozzáadása a webes API-t. A webes API-k majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, akkor adja vissza a kívánt erőforrás.

A "Alkalmazásidentitás" folyamatában a webes API-k megbízhatónak tekinti, hogy a webes alkalmazás hitelesítette a felhasználót. Ezért ez a minta egy megbízható alrendszer neve. A [diagram ezen az oldalon](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) ismerteti, hogyan engedélyezési kódot adjon folyamata működik.

Licenc a beszerzési token korlátozás megbízható alrendszer ugyanilyen mintájú következő azt. És az Azure Media Services licenctovábbítási szolgáltatása a webes API erőforrás, a "háttér-erőforrás" webalkalmazás hozzáférésre van szüksége. Ezért hol található a hozzáférési jogkivonat?

Valóban azt beszerzésekor hozzáférési jogkivonatot az Azure AD. A felhasználó sikeres hitelesítést követően engedélyezési kódot ad vissza. Az engedélyezési kód majd szolgál, és azonosító és az alkalmazás ügyfélkulcsot, exchange-hozzáférési jogkivonat. És a hozzáférési jogkivonat van mutató "mutató" alkalmazás eléréséhez, vagy Azure Media Services licenctovábbítási szolgáltatása jelző.

Igazolnia kell regisztrálni, és az Azure ad-ben "mutató" alkalmazás beállítása az alábbi lépéseket követve:

1. Az Azure AD-bérlőben

   * bejelentkezés az URL-címmel (erőforrás) alkalmazás hozzáadása:

   https://[resource_name].azurewebsites.NET/ és

   * App ID URL-címe:

   https://[aad_tenant_name].onmicrosoft.com/[resource_name];
2. Adja hozzá egy új kulcsot az erőforrás alkalmazás;
3. Az alkalmazás-jegyzékfájl frissítése az, hogy a groupMembershipClaims tulajdonság értéke a következő: "groupMembershipClaims": "All",  
4. Az Azure AD-alkalmazáshoz a player webalkalmazás mutató, az "egyéb alkalmazások engedélyei," szakaszában adja meg az erőforrás alkalmazás, amely hozzá lett adva a fenti 1. lépés. A "delegált engedélyek" Ellenőrizze a "Hozzáférés [resource_name]" jelet. Ez biztosítja a web app engedély az erőforrás alkalmazás eléréséhez hozzáférési jogkivonatok létrehozásához. Akkor tegye ezt a webes alkalmazás a helyi és a telepített verziójához a Visual Studio és az Azure web app fejlesztői.

Ezért a JWT jogkivonat az Azure AD által kibocsátott, valóban a hozzáférési jogkivonat az "mutató" erőforrás eléréséhez.

### <a name="what-about-live-streaming"></a>Mi a helyzet élő adatfolyam?
A fenti a vitafórum rendelkezik lett összpontosító igény szerinti eszközök. Mi a helyzet live streaming?

Jó hírünk használható pontosan azonos megtervezését és megvalósítását védelmére élő adatfolyamként történő Azure Media Services, a program "VOD eszközként" társított adategységet kezelésére.

Pontosabban is jól ismert, hogy ehhez élő adatfolyam-Azure Media Services, meg kell létrehoznia egy csatornát, majd egy a csatorna a programot. A program létrehozásához létrehozásához szükséges egy eszköz, amely tartalmazni fogja az élő archívum a program. Ahhoz, hogy az élő tartalom multi-DRM-védelemmel rendelkező CENC megadásához Önnek kell elvégeznie, azonos telepítő/feldolgozása az eszközre, ha a program elindítása előtt volt egy "VOD eszköz" vonatkozik.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>Mi a helyzet licenckiszolgálókat Azure Media Services kívül?
Az ügyfelek gyakran, előfordulhat, hogy befektettek licenc kiszolgálófarm vagy a saját data center vagy üzemeltetett DRM-szolgáltatók. Szerencsére Azure Media Services Content Protection lehetővé teszi a hibrid módban: tartalma birtokolt, és dinamikusan védett Azure Media Services, amíg DRM-licencek kiszolgálók Azure Media Services kívül érkeznek. Ebben az esetben van változások az alábbiakat:

1. A Secure Token Service kell ezek elfogadható, és ellenőrizheti a licenc kiszolgálófarm jogkivonatokat kibocsátani. A Widevine-licenc Axinom által biztosított kiszolgálók például egy adott JWT jogkivonat, amely tartalmazza a "jogosultság üzenet" igényel. Ezért szükség van egy ilyen JWT jogkivonat kiállítására STS. A szerzők egy végrehajtás befejezése és a részletek a következő dokumentumban talál [Azure dokumentációs központban](https://azure.microsoft.com/documentation/): [Widevine-licencek kézbesíthet Azure Media Services használatával Axinom](media-services-axinom-integration.md).
2. Már nincs szüksége az Azure Media Services licenctovábbítási szolgáltatása (ContentKeyAuthorizationPolicy) konfigurálása. Mit kell tennie, hogy adja meg a licenc licenckérési URL-címek (PlayReady és Widevine FairPlay) konfigurálása során AssetDeliveryPolicy és multi-DRM CENC beállításában.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Mi történik, ha használni kívánt egyéni STS?
Néhány, amelyek az ügyfél lehet, hogy egy egyéni STS (Secure Token Service) biztosítani a JWT-jogkivonatokat oka lehet. Ezek a következők:

1. Az Identity Provider (IDP) az ügyfél által használt nem támogatja az STS. Ebben az esetben egy egyéni STS lesz lehetőség.
2. Az ügyfél esetleg rugalmas vagy szorosabb vezérlő az STS integrálása számlázórendszerrel ügyfél előfizető. MVPD operátor kínálhat például több OTT előfizető csomagok, például a prémium alapvető, sport, stb. Az operátor szeretne felel meg a jogcímek, az előfizető csomag jogkivonatot, hogy csak a megfelelő csomag tartalmát szeretné elérhetővé tenni. Ebben az esetben egy egyéni STS biztosítja a szükséges rugalmasságot és.

Két módosításokat kell végezni egy egyéni STS használata esetén:

1. Az eszközhöz tartozó licenctovábbítási szolgáltatása konfigurálásakor meg kell adnia a biztonsági kulcsot használja az egyéni STS (további részleteket az alábbi) helyett az aktuális kulcsot az Azure Active Directory-ellenőrzés.
2. Amikor JTW jogkivonat történik, a biztonsági kulcs helyett az aktuális X509 titkos kulcsának van-e adva tanúsítvány, az Azure Active Directoryban.

A biztonsági kulcsok két típusa van:

1. Szimmetrikus kulcs: a ugyanazzal a kulccsal létrehozása és ellenőrzése a JWT jogkivonat;
2. Aszimmetrikus kulcs: egy nyilvános-titkos kulcspár egy X509 tanúsítvánnyal titkosított/JWT jogkivonat és létrehozásához a token ellenőrzésére szolgáló nyilvános kulcs titkos kulcsával.

#### <a name="tech-note"></a>Műszaki megjegyzés
.NET-keretrendszer használatakor / C#, a fejlesztő platform, a X509 aszimmetrikus kulcs használt tanúsítványnak rendelkeznie kell legalább 2048 bites kulcshosszt. Ez az osztály a .NET-keretrendszer System.IdentityModel.Tokens.X509AsymmetricSecurityKey követelmény. Ellenkező esetben a következő kivételt fog jelezni:

IDX10630: "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" aláírásra nem lehet kisebb, mint '2048' bit.

## <a name="the-completed-system-and-test"></a>A befejezett rendszer és a teszt
Végigvezetjük néhány olyan forgatókönyvet a befejezett végpont rendszer így olvasók "kép" működését alapvető bejelentkezési fiókot beolvasása előtt.

A player webalkalmazás és a bejelentkezési található [Itt](https://openidconnectweb.azurewebsites.net/).

Ha az alábbiakra lesz szüksége a "nem integrált" lehetőséget: Azure Media Services, amely vannak vagy védelem nélkül, vagy DRM, de tokent használó hitelesítés nélkül üzemeltetett videó eszközök (licenc kiállításának személy, aki ezt kéri), tesztelheti, bejelentkezési azonosító nélküli (által átváltása HTTP, ha a videoadatfolyam-továbbítás HTTP Protokollon keresztül).

Ha mire van szüksége a végpontok közötti integrált lehetőséget: lexikális elem a hitelesítés és a rendszer az Azure AD által létrehozott JWT jogkivonat az Azure Media Services dinamikus DRM védelem alatt videó eszközök, akkor kell bejelentkezni.

### <a name="user-login"></a>Felhasználói bejelentkezés
A végpont integrált DRM rendszer teszteléséhez kell rendelkezik egy "fiók" létrehozni vagy hozzáadni.

Milyen fiókot?

Bár az Azure eredetileg csak a Microsoft-fiókos felhasználó által hozzáférése engedélyezett, mostantól engedélyezi mindkét rendszer felhasználóinak hozzáférését. Ez úgy valósul meg, hogy az összes Azure-tulajdonság megbízik az Azure AD hitelesítésében, így az Azure AD hitelesíti a szervezeti felhasználókat, valamint a rendszer összevonási kapcsolatot hoz létre, ahol az Azure AD megbízik a Microsoft-fiókok végfelhasználói identitásrendszerében a felhasználók hitelesítéséhez. Ennek eredményeképpen az Azure AD képes a „vendég” Microsoft-fiókok és a „natív” Azure AD-fiókok hitelesítésére is.

Microsoft-fiók (MSA) tartományhoz az Azure AD megbízik, mivel adhat hozzá az egyéni Azure AD a következő tartományokban lévő bármely fiókok bérlői és a bejelentkezési fiókot használja:

| **Tartománynév** | **Tartomány** |
| --- | --- |
| **Egyéni az Azure AD bérlő tartománya** |somename.onmicrosoft.com |
| **Vállalati tartományhoz** |Microsoft.com |
| **Microsoft-fiók (MSA) tartomány** |Outlook.com-os, live.com, hotmail.com |

A szerzők egy fiókot a létrehozott új és az Ön is kapcsolódhat.

Az alábbiakban a különböző bejelentkezési oldalak ugyanaz a tartományi fiókok által használt példaként bemutató képernyőképeket láthat.

**Egyéni az Azure AD tartományi fiók bérlői**: Ebben az esetben az egyéni testreszabott bejelentkezési oldalán megjelenik az Azure AD bérlő tartománya.

![Egyéni az Azure AD bérlő tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft tartományi fiók intelligens kártyával**: Ebben az esetben tekintse meg a bejelentkezési oldal vállalati Microsoft szerint testre szabott informatikai kéttényezős hitelesítéssel.

![Egyéni az Azure AD bérlő tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-fiók (MSA)**: Ebben az esetben megjelenik a bejelentkezési oldalt a Microsoft Account fogyasztók számára.

![Egyéni az Azure AD bérlő tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>PlayReady titkosított Media bővítményekkel
A modern böngészőt titkosított Media Extensions (EME) rétegen a PlayReady-támogatást, például az Internet Explorer 11 és mentése a Windows 8.1 és Windows 10, a Microsoft Edge böngésző PlayReady EME az alapul szolgáló DRM lesz.

![PlayReady EME használata](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A sötét player területen van annak köszönhető, hogy PlayReady védelem megakadályozza, hogy egy képernyőfelvétel-készítés védett videó hajtsanak végre.

A következő képernyő player beépülő modulok és MSE/EME támogatási jeleníti meg.

![PlayReady EME használata](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

A Microsoft Edge és a Windows 10 Internet Explorer 11 EME lehetővé teszi, hogy meghívása a [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) Windows 10 rendszerű eszközökön, amelyek támogatják azt. PlayReady SL3000 feloldja a folyamatot a bővített támogatás tartalom (4 KB-os, HDR, stb.) és új tartalom üzemeltetési modell (a korai ablakának bővített tartalom).

A Windows-eszközök összpontosítani: PlayReady a Windows-eszközökön (PlayReady SL3000) elérhető Hardveres csak DRM. Egy adatfolyam-továbbítási szolgáltatás használhatja a PlayReady vagy egy UWP-alkalmazás EME keresztül, és segítségével PlayReady SL3000 egy másik DRM-nál nagyobb videominőséget kínálnak. Általában 2K tartalom halad keresztül Chrome vagy a Firefox, és a 4 KB-os tartalom a Microsoft Edge/IE11 vagy egy UWP-alkalmazás (attól függően, hogy szolgáltatás beállításainak és megvalósítása) az adott eszközön keresztül.

#### <a name="using-eme-for-widevine"></a>Widevine EME használata
Olyan modern böngészőt EME vagy Widevine-támogatással rendelkező, például a Chrome 41 + a Windows 10, Windows 8.1-, Mac OS x Yosemite és Chrome Android 4.4.4, a Google Widevine a DRM EME mögött.

![Widevine EME használata](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Figyelje meg, hogy Widevine nem gátolja meg egy védett videó képernyőfelvétel-készítés hajtsanak végre.

![Widevine EME használata](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Nem jogosult felhasználók
Ha a felhasználó nincs "Jogosult felhasználók" csoport tagja, a felhasználó nem fogja tudni adja át a "jogosultság ellenőrzése", és a multi-DRM szolgáltatás nem lesz adja ki a kért licenc alább látható módon. A részletes leírás: "licencet szerezni nem sikerült", amely esetén, mert a tervezett.

![Nem jogosult felhasználók](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Egyéni biztonságos biztonságijogkivonat-szolgáltatás fut
A forgatókönyvhöz a futó egyéni Secure Token Service (STS) a JWT jogkivonat ad ki az egyéni STS szimmetrikus vagy aszimmetrikus kulccsal.

A kis-és szimmetrikus kulcs (Chrome használatával történő) használatával:

![Egyéni STS fut](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

A kis-és egy X509 keresztül aszimmetrikus kulccsal tanúsítvány (Microsoft modern böngészőt használ).

![Egyéni STS fut](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

A fenti esetek mindegyikét, a felhasználó hitelesítési ugyanaz marad – keresztül az Azure AD. Az egyetlen különbség az, hogy az egyéni STS helyett az Azure AD által kiállított JWT-jogkivonatokat. Természetesen dinamikus CENC védelmi konfigurálásakor licenctovábbítási szolgáltatása korlátozásának határozza meg a JWT jogkivonat szimmetrikus vagy aszimmetrikus kulcs.

## <a name="summary"></a>Összefoglalás
Ebben a dokumentumban, amiről beszéltünk CENC token hitelesítéssel több native DRM- és hozzáférés-vezérléssel: a kialakításakor és Azure, az Azure Media Services és az Azure Media Player megvalósítása.

* Egy referencia-Tervező oszlik, amely tartalmazza a szükséges összetevőkkel a DRM/CENC alrendszerben;
* Egy Azure, az Azure Media Services és az Azure Media Player hivatkozás végrehajtására.
* Néhány közvetlenül részt a tervezési és megvalósítási témaköröket is tartalmazza.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
