---
title: Egy DRM-mel a content protection rendszert a hozzáférés-vezérléssel – az Azure Media Services |} A Microsoft Docs
description: További információ a licencelésről a Microsoft Smooth Streaming Client portolása Kit.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: ef695d913c73f0a4266b20f21f1008108b85b4d0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57893016"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Egy DRM-mel a content protection rendszert a hozzáférés-vezérléssel 

## <a name="overview"></a>Áttekintés

Tervezése és létrehozása a digitális jogkezelési (DRM) alrendszer egy over-the-top (OTT) vagy online streamelési megoldások a bonyolult feladat. Kezelők vagy online videó szolgáltatók jellemzően kiszervezik ennek a feladatnak a speciális DRM-szolgáltatók. A jelen dokumentum célja, hogy egy referenciaterv és a egy referenciaimplementációt, egy teljes körű DRM-alrendszer OTT vagy online videoszolgáltatási megoldás.

Ebben a dokumentumban a megcélzott olvasók mérnökeitől, akik a DRM-alrendszer OTT vagy online streamelés/grafikus; megoldások, illetve olvasók, akik DRM-alrendszer. Feltételezzük, hogy olvasók ismeri a DRM-technológiák a piacon, például a PlayReady, Widevine, FairPlay vagy az Adobe hozzáférés legalább egyike.

A témakörben ismertetettek szempontjából a többplatformos DRM által tartalmazza az Azure Media Services által támogatott 3 DRMs: Általános titkosítás (CENC) a PlayReady és Widevine FairPlay, valamint az AES-128 titkosítatlan kulcs titkosítás. Online streamelés és OTT iparág fő a tendencia, hogy natív DRMs ügyfél különböző platformokon. Erre az irányra egy az előzőre, amelyek egyetlen DRM és az ügyfél-SDK különböző ügyfél-platformokhoz való váltás. CENC használata többszörös natív DRM, ha mind a PlayReady, mind a Widevine titkosítása a [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikációnak.

A következők, hogy a natív tartalomtovábbítás többplatformos DRM használatával a content protection előnyei:

* Csökkenti a titkosítási költségek, mivel ez egy egyetlen folyamat különböző platformokon a natív DRMs a célként használt.
* Csökkenti a költségeket eszközök kezelésére, mert csak egy példányban kell az eszközintelligencia a storage-ban van szükség.
* Kiküszöböli a licencelési költségek, mivel a natív DRM-ügyfél a natív platformon általában ingyenes DRM-ügyfél.


### <a name="goals-of-the-article"></a>A cikk célja

Ez a cikk célja a következők:

* Adjon meg egy referenciaterv használó összes 3 DRMs (CENC a DASH), a FairPlay a HLS és PlayReady a smooth streaming DRM-alrendszer.
* Adjon meg egy referenciaimplementációt Azure és az Azure Media Services platformon.
* Néhány tervezési és megvalósítási témakörök tárgyalják.

Az alábbi táblázat foglalja össze a különböző platformokon a natív DRM-támogatása és a különböző böngészők EME támogatást.

| **Ügyfélplatform** | **Natív DRM** | **AZ EME** |
| --- | --- | --- |
| **Okostévére vezérléséhez** | A PlayReady, Widevine és/vagy egyéb | Beágyazott böngésző/EME a PlayReady és/vagy Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 for PlayReady|
| **Android-eszközök (telefonon, táblagépen, TV)** |Widevine |A Widevine Chrome |
| **iOS** | FairPlay | A fairplay rendszerhez Safari (óta 11,2 iOS) |
| **macOS** | FairPlay | A fairplay rendszerhez (óta a Safari 9 és a Mac OS X 10.11 El Capitan) Safari|
| **tvOS** | FairPlay | |

Üzembe helyezés minden egyes DRM aktuális állapotát, figyelembe véve egy szolgáltatás általában szeretne megvalósítani, ellenőrizze, hogy a legjobb módszer az összes típusú végpontok meg cím két vagy három DRMs.

A szolgáltatás logika összetettségétől és a különböző ügyfelek részére a felhasználói élmény bizonyos szintű elérni az ügyféloldalon összetettségét magával van.

Ahhoz, hogy a kijelölt, vegye figyelembe:

* PlayReady natív módon valósítja meg a minden Windows-eszköz, bizonyos Android-eszközön, és elérhető szoftverek SDK-k, gyakorlatilag bármely platformra.
* Widevine minden Android-eszközön, a Chrome-ban, és az egyes eszközök natív módon valósul meg. Widevine is támogatott, a Firefox és az Operát böngészők DASH-en keresztül.
* FairPlay iOS, macOS és tvOS érhető el.


## <a name="a-reference-design"></a>A referencia-Tervező
Ez a szakasz egy referenciaterv, amely független a implementálásáról használt technológiákat mutatja be.

Egy DRM-alrendszer a következő összetevőket tartalmazza:

* Kulcskezelés
* DRM-titkosítás csomagolás
* DRM-licenckézbesítés
* Jogosultság ellenőrzése és hozzáférés-vezérlés
* Felhasználói hitelesítés/engedélyezés
* Player alkalmazás
* Forrás/tartalomkézbesítési hálózat (CDN)

A következő ábra szemlélteti a magas szintű interakció egy DRM-alrendszer összetevői között:

![CENC a DRM-alrendszer](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

A Tervező három alapvető réteg van:

* A háttérrendszer webalkalmazása réteg (fekete) nem lesz közzétéve kívülről.
* Szegélyhálózat (DMZ) réteg (sötétkék) között a nyilvános végpontokat tartalmazza.
* A nyilvános interneten réteg (világoskék) tartalmazza a CDN és a játékosok a forgalmat a nyilvános interneten keresztül.

Emellett lehetnek ellenőrzés DRM-védelem, függetlenül attól, hogy statikus vagy dinamikus titkosítás a tartalom felügyeleti eszközt. A DRM-titkosításhoz bemenetei között a következők:

* MBR-videót
* Tartalomkulcs
* Licenc-licenckérési URL-címek

A lejátszás idő alatt a következő magas szintű folyamat:

* A felhasználó hitelesítését.
* A felhasználó egy engedélyezési jogkivonatot jön létre.
* A Windows Media player letöltődik DRM által védett tartalmat (manifest).
* A Windows Media player licenckiszolgálókat együtt egy kulcs licenc beszerzése kérelmet küld a vizsgálatnál Azonosítóját és a egy engedélyezési jogkivonatot.

Az alábbi szakasz ismerteti a kulcskezelés kialakítása.

| **ContentKey-adategység** | **Forgatókönyv** |
| --- | --- |
| 1-1 |A legegyszerűbb eset. A legkiválóbb vezérlő biztosít. De ezzel az elrendezéssel fokozott általában a legmagasabb kézbesítési licencköltség eredményez. Legalább egy licenc kérésünk szükség, az egyes védett eszközök. |
| 1-a-többhöz |Több eszköz használatával tartalom ugyanazzal a kulccsal. Például az összes az eszköz egy logikai csoportba, például a genre vagy a műfaj (vagy film gene), a részhalmazát használhatja egyetlen tartalomkulcs. |
| Több-a-1 |Több tartalomkulcs van szükség az egyes eszközökre. <br/><br/>Ha például dinamikus CENC-védelem és multi-DRM MPEG-dash protokollhoz és a HLS Protokollhoz dinamikus AES-128 titkosítást alkalmazni kell, ha szüksége két külön tartalomkulcs. Minden tartalom kulcsot a saját ContentKeyType kell. (A tartalomkulcsot a dinamikus CENC védelemhez használt ContentKeyType.CommonEncryption használja. A tartalomkulcs konfigurációjának dinamikus AES-128 titkosítást használja használjon ContentKeyType.EnvelopeEncryption.)<br/><br/>Másik példaként DASH-tartalom elméletileg CENC Protection segítségével egy tartalomkulcsot a video-adatfolyamot és a egy másik tartalomkulcs védelme érdekében az audio-adatfolyamot védelme. |
| Több-a-többhöz |Az előző két forgatókönyv kombinációja. Tartalomkulcs egy készletét minden eszköz ugyanabba a csoportba több eszközhöz használatos. |

Egy másik fontos szempont az, hogy az állandó és nem állandó licencek használatát.

Miért fontosak ezeket a szempontokat?

Ha licencekkel történő kézbesítés használ a nyilvános felhő, állandó és nem állandó licencek közvetlen hatást license delivery költséget. A következő két különböző kialakítási esetben műveletek mutatja be:

* Havi előfizetés: Használjon egy állandó licenc és az 1-a-többhöz tartalom kulcs eszköz hozzárendelést. Például a gyermekek filmekhez, használjuk egy egyetlen titkosítási tartalomkulcsot. Ebben az esetben:

    Lekéri az összes gyerek filmek/eszköz licencek teljes száma = 1

* Havi előfizetés: Használja a nonpersistent licenc és 1-1 leképezés tartalomkulcs és eszköz közötti. Ebben az esetben:

    Lekéri az összes gyerek filmek/eszköz licencek teljes száma = [nézte filmek száma] x [munkamenetek száma]

A két különböző műveletekhez különböző licenc kérelem minták eredményez. A különböző minták Ha licenctovábbítási szolgáltatása egy nyilvános felhő, például a Media Services által biztosított különböző licencekkel történő kézbesítés eredményez.

## <a name="map-design-to-technology-for-implementation"></a>Technológiai megvalósításának tervezési leképezése
Ezután az általános tervezési van leképezve az Azure/Media Services platformon technológiák technológiákat használni mindegyik építőelem megadásával.

Az alábbi táblázat a leképezést.

| **Építőelem** | **Technológia** |
| --- | --- |
| **Lejátszó** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identitásszolgáltató (IDP)** |Azure Active Directory (Azure AD) |
| **Biztonságos jogkivonat-szolgáltatás (STS)** |Azure AD |
| **DRM-védelmi munkafolyamat** |Az Azure Media Services dinamikus protection |
| **DRM-licenckézbesítés** |* A Media Services-licencekkel történő kézbesítés (PlayReady, Widevine és FairPlay) <br/>* Az Axinom licenckiszolgáló <br/>* Egyéni PlayReady-licenc kiszolgáló |
| **Forrás** |Az Azure Media Services streaming endpoint |
| **Kulcskezelés** |Nincs szükség referenciaimplementáció |
| **Tartalomkezelés** |C# Konzolalkalmazás |

Más szóval Identitásszolgáltató és az STS által biztosított Azure ad-ben. A [az Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) a lejátszó szolgál. Az Azure Media Services és az Azure Media Player támogathatóak CENC DASH, a FairPlay HLS keresztül, a PlayReady smooth streaming, valamint a DASH, HLS vagy smooth AES-128 titkosítást.

Az alábbi ábrán látható a teljes struktúra és korábbi technológiai hozzárendelési folyamat:

![A Media Services CENC](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

A Tartalomkezelés-eszköz beállítva a content protection DRM, használja az alábbi ráfordítások:

* Nyissa meg a tartalom
* A tartalomkulcsot a kulcskezelés
* Licenc-licenckérési URL-címek
* Az Azure AD-ben például a közönség, a kibocsátó és a jogkivonat adatainak listája

A Tartalomkezelés-eszköz a kimenet itt látható:

* ContentKeyPolicy DRM-licencsablon használni; DRM egyes függőségtípusok ismerteti.
* ContentKeyPolicyRestriction ismerteti a hozzáférés-vezérlést a DRM-licenckiszolgáló kibocsátása előtt
* Streamingpolicy ismerteti a különböző kombinációit DRM - titkosítási mód - streamelési protokoll - tároló formátumát, streaming
* StreamingLocator ismerteti content key/IV használt titkosítási és streamelési URL-címek 

Itt látható a folyamat során:

* Esetén a felhasználók hitelesítését jwt-t jön létre.
* A JWT lévő jogcímek egyik, a csoportházirend-objektum azonosítója EntitledUserGroup tartalmazó csoportok jogcím. Ez a jogcím segítségével adja át a jogosultság-ellenőrzést.
* A Windows Media player letölti az ügyfél jegyzékfájlja CENC által védett tartalom, és azonosítja a következőket:
   * Kulcs azonosítója.
   * A tartalom DRM-védelemmel.
   * Licenc-licenckérési URL-címek.
* A Windows Media player támogatott böngésző/DRM alapján licenc beszerzése kérelmet küld. A licenc beszerzési kérés esetén a kulcs Azonosítóját és a JWT is elküldi. A szolgáltatásra vonatkozó ellenőrzi a JWT és a jogcímek tartalmazott előtt, a szükséges licenccel.

## <a name="implementation"></a>Megvalósítás
### <a name="implementation-procedures"></a>Eljárások végrehajtása
Végrehajtása a következő lépésekből áll:

1. Készítse elő a tesztelési eszközök. A Media Services töredezett többszörös sávszélességű MP4 videókat teszt kódolása/csomag. Ez az eszköz *nem* DRM-védelemmel. DRM-védelem később dinamikus védelmét végzi el.

2. Hozzon létre egy kulcsot Azonosítóját és a egy tartalomkulcsot (szükség esetén az egyik legfontosabb kezdőérték). Ebben a példában a kulcskezelés rendszer nincs szükség, mivel csak egyetlen kulcs azonosítója és a tartalomkulcs tesztelési eszközök néhány szükségesek.

3. A Media Services API segítségével konfigurálhatja a multi-DRM-licenctovábbítási szolgáltatások a vizsgálati eszköz. Ha a vállalat vagy a vállalat forgalmazók helyett a Media Services licencelési szolgáltatások egyéni licenckiszolgálókat használja, kihagyhatja ezt a lépést. Licenc-licenckérési URL-címek megadhatja a lépés licencekkel történő kézbesítés konfigurálásakor. A Media Services API-t adjon meg néhány részletes konfigurációk, például engedélyezési szabályzat korlátozási is megszűnnek, és különböző DRM-szolgáltatások válasz sablonok licenc szükséges. Jelenleg az Azure portal nem biztosít a a szükséges felhasználói felületén ebben a konfigurációban. API-szintű információkat és mintakód: [használja a PlayReady és/vagy Widevine dynamic common Encryption titkosítás](protect-with-drm.md).

4. A Media Services API használatával a vizsgálati eszköz objektumtovábbítási szabályzat konfigurálása. API-szintű információkat és mintakód: [használja a PlayReady és/vagy Widevine dynamic common Encryption titkosítás](protect-with-drm.md).

5. Hozzon létre, és az Azure AD-bérlő konfigurálása az Azure-ban.

6. Hozzon létre néhány felhasználói fiókokat és csoportokat az Azure AD-bérlőben. Hozzon létre legalább egy "Jogosult felhasználó" csoportot, és a felhasználót a csoporthoz. Ez a csoport felhasználóinak a jogosultság-ellenőrzést licenc beszerzése adja át. Nem ebben a csoportban lévő felhasználók átadására a hitelesítés ellenőrzése sikertelen, és nem tudta beolvasni a licencet. A "Jogosult felhasználó" csoporttagság szükséges csoportok jogcím a kiállított Azure AD által a JWT. Ez a jogcím-követelmény az multi-DRM-licenctovábbítási szolgáltatások konfigurálásakor megadhatja a lépést.

7. Hozzon létre egy ASP.NET MVC alkalmazás a videolejátszó üzemeltetéséhez. Az ASP.NET-alkalmazás az Azure AD-bérlő felhasználóhitelesítés védi. Megfelelő jogcím szerepel a felhasználói hitelesítés után kapott hozzáférési jogkivonatok. Javasoljuk, hogy OpenID Connect API-t ehhez a lépéshez. Telepítse az alábbi NuGet-csomagokat:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Hozzon létre egy player használatával a [az Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Használja a [az Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) , adja meg, melyik DRM-technológiával használandó DRM különböző platformokon.

9. Az alábbi táblázat a teszt mátrix.

    | **DRM** | **Böngésző** | **Jogosult felhasználói eredménye** | **Eredmény unentitled felhasználó** |
    | --- | --- | --- | --- |
    | **PlayReady** |A Microsoft Edge vagy az Internet Explorer 11 Windows 10 rendszeren |Sikeres |Sikertelen |
    | **Widevine** |Chrome, Firefox, Opera |Sikeres |Sikertelen |
    | **FairPlay** |Safari macOS rendszeren      |Sikeres |Sikertelen |
    | **AES-128** |A legtöbb modern böngészők  |Sikeres |Sikertelen |

Az Azure AD-lejátszó ASP.NET MVC alkalmazás beállítása a további információkért lásd: [Azure Media Services OWIN MVC-alapú alkalmazás integrálása az Azure Active Directoryval, és korlátozhatja a JWT jogcímszolgáltatói alapján kulcs tartalomkézbesítési](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

További információkért lásd: [JWT jogkivonat-hitelesítés az Azure Media Services és a dinamikus titkosítás](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Az Azure AD tájékoztatást:

* A fejlesztői információkat a [Azure Active Directory fejlesztői útmutatója](../../active-directory/develop/v1-overview.md).
* A rendszergazdai információkért [felügyelheti az Azure AD-bérlő címtárát](../../active-directory/fundamentals/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Bizonyos problémák végrehajtása
Használja a következő hibaelhárítási információk megvalósítási problémái segítség.

* A kibocsátó URL-címet kell végződnie "/". A célközönség kell lennie a lejátszó alkalmazás ügyfél-azonosítóját. Adja hozzá "/" a kibocsátó URL-cím végén található.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    Az a [JWT-dekódoló](http://jwt.calebb.net/), látja **aud** és **iss**, ahogyan az a JWT:

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Adja hozzá az Azure AD az alkalmazásnak a **konfigurálása** az alkalmazás lapján. Engedélyek szükségesek az egyes alkalmazások, a helyi és a telepített verzió.

    ![Engedélyek](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* Használja a megfelelő kiállítótól, dinamikus CENC védelem beállításakor.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    A következő nem működik:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    A GUID azonosító az Azure AD-bérlő azonosítója. A GUID azonosító megtalálható a **végpontok** az Azure Portalon az előugró menüben.

* Támogatási csoport tagsági jogosultságokat jogcímek. Ellenőrizze, hogy az Azure ad-ben Alkalmazásjegyzék-fájl a következő szerepel: 

    "groupMembershipClaims": "All" (az alapértelmezett érték null értékű)

* Állítsa be a megfelelő TokenType, eszközkorlátozásokra vonatkozó követelmények létrehozásakor.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Mivel a JWT-(Azure AD) mellett SWT (ACS) támogatása, TokenType alapértelmezés szerint TokenType.JWT. SWT/ACS használatakor, TokenType.SWT be kell állítania a jogkivonatot.

## <a name="faqs"></a>Gyakori kérdések

Ez a szakasz ismerteti a tervezési és megvalósítási néhány további témaköreiben.

### <a name="http-or-https"></a>A HTTP vagy HTTPS?
Az ASP.NET MVC-lejátszóalkalmazás támogatnia kell a következőket:

* Felhasználó hitelesítése az Azure AD, amely HTTPS alatt található.
* JWT exchange, az ügyfél és a HTTPS alatt álló Azure AD között.
* DRM-licenc beszerzése az ügyfél, amely a HTTPS alá kell tartoznia, ha a Media Services által biztosított licencekkel történő kézbesítés. A PlayReady termékcsomag nem határozza meg, hogy HTTPS licencekkel történő kézbesítés számára. Ha a PlayReady-licenc kiszolgálón kívül a Media Services, HTTP vagy HTTPS is használhatja.

Az ASP.NET-lejátszóalkalmazás a HTTPS, ajánlott eljárásként használja, így a Media Player alatt HTTPS oldalon szerepel. Azonban HTTP, figyelembe kell vennie a probléma a vegyes tartalom nem előnyben részesített streameléshez.

* A böngésző nem engedélyezi a vegyes tartalom. De modulok, a Silverlight beépülő modul OSMF smooth és kötőjel engedélyezhető. A vegyes tartalom egyik biztonsági szempont, a rosszindulatú JavaScript, okozhat a vásárlói adatokat veszélyben új funkció lehetővé teszi a fenyegetés miatt. Böngészők alapértelmezés szerint ez a funkció letiltása. Az egyetlen megoldás módja a kiszolgáló (forrás) oldalán azáltal, hogy minden tartomány (függetlenül a HTTPS vagy HTTP). Ez a valószínűleg nem célszerű vagy.
* A vegyes tartalom elkerülése érdekében. A lejátszóalkalmazás és a Media Player HTTP vagy HTTPS PROTOKOLLT kell használnia. A vegyes tartalom lejátszása, a silverlightSS tech kell rendelkeznie a vegyes tartalom figyelmeztetés törlése. A flashSS tech kezeli a vegyes tartalom vegyes tartalmú figyelmeztetés nélkül.
* Ha a tartalomstreameléshez használt streamvégpont 2014. augusztus előtt jött létre, azt nem támogatja a HTTPS. Ebben az esetben létrehozhat, és egy új streamvégpont használhat HTTPS-hez.

A referenciaimplementációt a DRM-védelemmel ellátott tartalmakat, az alkalmazás és a streamelési vannak HTTPS alatt található. Nyissa meg a tartalmát a Windows Media player nem kell hitelesítést vagy egy licencet, így HTTP vagy HTTPS használatával is.

### <a name="what-is-azure-active-directory-signing-key-rollover"></a>Mi az Azure Active Directory aláírókulcs?
Aláírókulcs-egy fontos pontot a végrehajtása során figyelembe kell venni. Figyelmen kívül ezt, ha befejeződött a rendszer végül leáll, teljesen, hat héten belül a leginkább.

Az Azure AD iparági szabványok használatával magát és alkalmazásokat, amelyek használják az Azure AD közötti megbízhatósági kapcsolatot hoz létre. Pontosabban az Azure AD, amely egy nyilvános és titkos kulcsból álló párként áll aláíró kulcsot használ. Amikor Azure ad-ben létrehoz egy biztonsági jogkivonatot, amely tartalmazza a felhasználó adatai, aláírja azt az Azure AD és a egy titkos kulcs az alkalmazásnak továbbítás előtt. Annak ellenőrzéséhez, hogy a jogkivonat érvényes, és az Azure ad-ből származó, az alkalmazás ellenőrizni kell a jogkivonat aláírása. Az alkalmazás Azure ad-ben, amely tartalmazza a bérlő összevonási metaadatok dokumentuma által elérhetővé tett a nyilvános kulcsot használ. A nyilvános kulcs és az aláírási kulcs, amelyből származik, a rendszer ugyanaz az Azure ad-ben használt összes bérlőre vonatkozóan.

Az Azure AD kulcsváltás további információkért lásd: [fontos információ az Azure Active Directory aláírókulcs](../../active-directory/active-directory-signing-key-rollover.md).

Között a [nyilvános-titkos kulcspárt](https://login.microsoftonline.com/common/discovery/keys/):

* A titkos kulcs létrehozásához a jwt-t használják az Azure AD.
* A nyilvános kulcs ellenőrzése a JWT-alkalmazást például a DRM-licenctovábbítási szolgáltatások a Media Services használják.

Biztonsági okokból az Azure ad-ben elforgatása a tanúsítványt rendszeres időközönként (hat hét). Biztonsági szabályok megsértése esetén alkalmazandók esetén a kulcshoz kapcsolódó kulcsváltás fordulhat elő, bármikor. Ezért a Media Services licenctovábbítási szolgáltatások frissítenie kell a használt Azure ad-ben forog a kulcspár nyilvános kulcsot. Ellenkező esetben a tokent használó hitelesítés biztosítását a Media Services meghiúsul, és a licenc nem jelenik meg.

Ez a szolgáltatás beállításához, beállíthat egy TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument DRM-licenctovábbítási szolgáltatások konfigurálásakor.

A JWT-folyamat a következő:

* Az Azure AD a JWT kibocsát egy hitelesített felhasználó számára a jelenlegi titkos kulccsal.
* A player egy CENC többplatformos DRM által védett tartalom látja, amikor először megkeresi az Azure AD által kiállított JWT.
* A Windows Media player a JWT-licenc beszerzése kérést küld a Media Services licenctovábbítási szolgáltatások.
* A Media Services licenctovábbítási szolgáltatások az Azure ad-ből a jelenlegi érvényes nyilvános kulcs használatával ellenőrizni a JWT licencek kiadása előtt.

DRM-licenctovábbítási szolgáltatások mindig ellenőrizze a jelenlegi érvényes nyilvános kulcsot az Azure ad-ből. A nyilvános kulcsot az Azure AD által a jwt-t, az Azure AD által kiadott ellenőrzéséhez használt kulcs.

Mi történik, ha a kulcshoz kapcsolódó kulcsváltás után az Azure ad-ben a jwt-t hoz létre, de a JWT DRM-licenctovábbítási szolgáltatások a Media Services az ellenőrzéshez a játékosok szerint elküldése előtt történik?

A kulcs bármikor kell leváltani, mert egynél több érvényes nyilvános kulccsal mindig érhető el az összevonási metaadatok dokumentuma. A Media Services-licencekkel történő kézbesítés a dokumentumban szereplő kulcsok bármelyikét használhatja. Előfordulhat, hogy egyetlen kulcs hamarosan fel lesz állítva, mert egy másik előfordulhat, hogy azok helyettesítéseivel, és így tovább.

### <a name="where-is-the-access-token"></a>Hol található a hozzáférési jogkivonat?
Ha tekinti meg hogyan meghívja a webes alkalmazás a API-alkalmazás alatt [alkalmazás identitását az OAuth 2.0 ügyfélhitelesítő adatok](../../active-directory/develop/web-api.md), a hitelesítési folyamat a következőképpen történik:

* Egy felhasználó bejelentkezik a webalkalmazás az Azure ad-ben. További információkért lásd: [webalkalmazást a webböngésző](../../active-directory/develop/web-app.md).
* Az Azure AD engedélyezési végpont átirányítja a felhasználói ügynököt az ügyfélalkalmazásnak az engedélyezési kód. A felhasználói ügynököt az engedélyezési kód és az ügyfél-alkalmazás átirányítási URI-t ad vissza.
* A webalkalmazás kell, hogy a webes API-t hitelesítésre és a kívánt erőforrást beolvasni a hozzáférési jogkivonat beszerzése. Ezt a kérést küld az Azure AD-jogkivonat végpontra, és a hitelesítő adatokat, az ügyfél-Azonosítót és a webes API-k alkalmazás Alkalmazásazonosító URI-t biztosít. Igazolja, hogy a felhasználó hagyta jóvá az engedélyezési kódot tükrözze.
* Az Azure AD hitelesíti magát az alkalmazást, és a webes API meghívásához használt JWT hozzáférési jogkivonatot ad vissza.
* HTTPS-en keresztüli a webalkalmazás a kapott JWT jogkivonat használja az "Engedélyezés" a kérelem fejlécében a JWT-karakterlánc a "Tulajdonos" megnevezéssel hozzáadása a webes API-t. A webes API a JWT ellenőrzi. Ha érvényesítés sikeres, a kívánt erőforrást adja vissza.

Alkalmazás identity folyamatban a webes API megbízik, hogy a webalkalmazás hitelesítette a felhasználót. Ezért ez a minta egy megbízható alrendszer neve. A [engedélyezési folyamatábrája](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) azt ismerteti, hogyan engedélyezési-kód-megadási folyamatában működik.

Licenc beszerzése a token korlátozás ugyanazon a megbízható alrendszer mintát követi. A Media Services licenctovábbítási szolgáltatása a webes API-erőforrás, vagy a "háttér-erőforrás", amely a web application való hozzáférésre van szüksége. Hol vannak a hozzáférési jogkivonat?

A hozzáférési jogkivonatot az Azure AD származik. A felhasználó sikeres hitelesítés után az engedélyezési kódot adja vissza. Az engedélyezési kód majd szolgál, az ügyfél-Azonosítót és az Alkalmazáskulcs exchange-hozzáférési jogkivonat. A hozzáférési jogkivonat segítségével mutat, vagy a Media Services licenctovábbítási szolgáltatása jelöli egy "mutató" alkalmazás eléréséhez.

Regisztrálta és konfigurálta az egérmutatót alkalmazást az Azure ad-ben, az alábbi lépéseket:

1. Az Azure AD-bérlő:

   * Adja hozzá a bejelentkezési URL-cím https://[resource_name].azurewebsites.net/ alkalmazás (erőforrás). 
   * Adja hozzá az URL-cím https://[aad_tenant_name].onmicrosoft.com/[resource_name az Alkalmazásazonosítót].

2. Adjon hozzá egy új kulcsot az erőforrás-alkalmazás számára.

3. Az alkalmazás-jegyzékfájl frissítése, így groupMembershipClaims tulajdonság értéke "groupMembershipClaims": "All".

4. Az Azure AD-alkalmazás, amely a lejátszó web app, a szakaszban szereplő **egyéb alkalmazások engedélyei**, adja hozzá az erőforrás-alkalmazás, amely az 1. lépésben lett hozzáadva. A **delegált engedély**válassza **hozzáférés [resource_name]**. Ez a beállítás hozzáférést biztosít a webes alkalmazás létrehozásához szükséges engedéllyel hozzáférési jogkivonatok, amelyek az erőforrás-alkalmazás elérésére. Erre a webalkalmazás helyi és a telepített verziójához, ha a Visual Studio és az Azure-webalkalmazás fejlesztése.

Az Azure AD által kiállított JWT az egérmutató erőforrás eléréséhez használt jogkivonat.

### <a name="what-about-live-streaming"></a>Mi a helyzet élő adások online közvetítése?
Az előző vitafórum igény szerinti lejátszásra összpontosít. Mi a helyzet élő adások online közvetítése?

Pontosan azonos megtervezését és megvalósítását segítségével megvédheti az élő streamelés a Media Services szolgáltatásban való kezelésével VOD eszközként program társított adategységet.

Pontosabban élő közvetítést végezni a Media Services szolgáltatásban, kell csatornát létrehozni, és hozzon létre egy programot a csatorna alapján. A program létrehozásához, meg kell hozzon létre egy objektumot, amely tartalmazza az élő archívumot a program. CENC többplatformos DRM-védelemmel az élő tartalom megadásához vonatkoznak az ugyanazon a telepítő feldolgozási az eszköz, mintha a VOD-eszköz a program indítása előtt.

### <a name="what-about-license-servers-outside-media-services"></a>Mi a helyzet licenckiszolgálókat kívül a Media Services?

Gyakran ügyfelek befektetni licenc kiszolgálófarm vagy a saját adatközpontban vagy egy DRM szolgáltató felhője. A Media Services content protection esetében működhet a hibrid üzemmódot. Tartalom is üzemeltetett és dinamikusan a Media Services szolgáltatásban védett, míg DRM-licencek kívül a Media Services-kiszolgálók által lépnek. Ebben az esetben fontolja meg a következő módosításokat:

* STS kell elfogadható és a licenc kiszolgálófarm által ellenőrzött jogkivonatokat kibocsátani. A Widevine-licenc Axinom által biztosított kiszolgálók például egy adott JWT-jogosultság üzenetet tartalmazó igényelnek. Ezért szüksége lesz az STS szolgáltatással kiadására ilyen jwt-t. 
* Már nincs szüksége a Media Services licenctovábbítási szolgáltatása konfigurálása. Meg kell adnia a licenc licenckérési URL-címek (a PlayReady, Widevine és FairPlay) ContentKeyPolicies konfigurálása során.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Mi történik, ha szeretnék egy egyéni STS használata?
Egy ügyfél egyéni STS használata JWTs érdemes választania. Okok a következők:

* Az Identitásszolgáltató az ügyfél nem támogatja az STS. Ebben az esetben egy egyéni STS lehet egy lehetőséget.
* Az ügyfél rugalmas vagy szigorúbb vezérlőelem STS integrálhatja az ügyfél előfizető számlázórendszerrel igényelhet. MVPD operátornak kínálhat például több OTT előfizetői csomagok, például a prémium, alapszintű, kereskedelemmel vagy sporttal kapcsolatosak. Az operátor érdemes felel meg a jogcímeket a jogkivonatot az előfizető csomagot, hogy egy adott csomag csak a tartalom elérhetővé válnak. Ebben az esetben egy egyéni STS a szükséges rugalmasságot és irányítást biztosít.

Egy egyéni STS használata esetén két módosításokat kell végrehajtani:

* Az adott eszköz számára szolgáltatásra vonatkozó konfigurálásakor adja meg a biztonsági kulcs ellenőrzése az Azure AD-ből a jelenlegi kulcs helyett egyéni STS által használt kell. (További részletekért kövesse.) 
* JTW jogkivonat jön létre, amikor a biztonsági kulcs helyett az aktuális X509 titkos kulcsa van megadva a tanúsítvány az Azure ad-ben.

Biztonsági kulcsok két típusa van:

* Szimmetrikus kulcs: Ugyanazt a kulcsot létrehozni, és ellenőrizheti a jwt-t használja.
* Az aszimmetrikus kulcs: Egy nyilvános-titkos kulcspárt egy X509 a tanúsítványt használja, a titkos kulcs titkosításához/készítése a jwt-t és ellenőrizni a jogkivonatot a nyilvános kulccsal.

> [!NOTE]
> Ha .NET-keretrendszer / C#, a fejlesztési platform, a X509 az aszimmetrikus kulcs használt tanúsítványnak rendelkeznie kell legalább 2048 bites kulcshosszt használ. Ez az osztály a .NET-keretrendszer System.IdentityModel.Tokens.X509AsymmetricSecurityKey mindenképpen szükséges. Ellenkező esetben a következő kivétel történt:
> 
> IDX10630: Az aláíráshoz "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" nem lehet kisebb, mint "2048" bits.

## <a name="the-completed-system-and-test"></a>A befejezett rendszer és a teszt
Ez a szakasz végigvezeti a következő esetekben a befejezett rendszerben teljes körű, hogy viselkedésének általános képet előtt megjelenik egy bejelentkezési fiókot használhat:

* Ha egy nem integrált forgatókönyv lesz szüksége:

    * Media Services az üzemeltetett videó eszközök vagy védelem nélkül osztanák vagy DRM-védelemmel, de (licenc kibocsátó személy, aki kérte, hogy) tokent használó hitelesítés nélkül tesztelheti bejelentkezés nélkül. Váltson a HTTP-e a video-adatfolyamok HTTP protokollon keresztül.

* Ha egy teljes körű integrált keresztül lesz szüksége:

    * Videó eszközök a jogkivonat-hitelesítés és az Azure AD által generált JWT a Media Services szolgáltatásban a dinamikus DRM védelem alatt kell bejelentkeznie.

A player webalkalmazás és annak bejelentkezési: [ezen a webhelyen](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Felhasználói bejelentkezés
A teljes körű integrált DRM-rendszer teszteléséhez szüksége lesz egy fiók létrehozásakor vagy hozzáadva.

Milyen fiókot?

Bár az Azure eredetileg csak Microsoft-fiókos felhasználó általi elérés engedélyezett, hozzáférés most már mindkét rendszerekből a felhasználók által engedélyezett. Az összes Azure-tulajdonság mostantól az Azure AD-hitelesítés megbízható, és az Azure AD akkor hitelesíti a szervezeti felhasználók. Egy összevonási kapcsolat jött létre, ahol az Azure AD megbízik a Microsoft fiók végfelhasználói identitásrendszer identitásrendszerében a felhasználók hitelesítéséhez. Ennek eredményeképpen az Azure AD hitelesítheti vendégfiókok Microsoft fiókokat, valamint a natív Azure ad-ben.

A Microsoft-fiók tartományát az Azure AD megbízik, mivel az egyéni Azure ad-ben a következő tartományokban lévő bármely fiókok bérlői, és jelentkezzen be a fiók használatával adhat hozzá:

| **Tartománynév** | **Tartomány** |
| --- | --- |
| **Egyéni Azure AD-bérlői tartomány** |somename.onmicrosoft.com |
| **Vállalati tartomány** |Microsoft.com |
| **A Microsoft-fiók tartománya** |Outlook.com-os, live.com, hotmail.com |

Egy fiók létrehozásakor vagy hozzáadva, hogy a szerzők bármelyikét fordulnia.

Az alábbi képernyőfelvételnek megfelelően eltérő bejelentkezési lapok ugyanaz a tartományi fiókok által használt megjelenítése:

**Egyéni Azure AD-bérlő tartományi fiók**: A testre szabott bejelentkezési oldala, az egyéni Azure AD bérlői tartomány.

![Egyéni Azure AD bérlő tartományi fiók egyik](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Tartományi fiókot a Microsoft intelligens kártyás**: A bejelentkezési oldal, testre szabott Microsoft vállalati IT-modernizálás a kétfaktoros hitelesítés.

![Egyéni Azure AD bérlő tartományi fiók két](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-fiók**: A bejelentkezési oldal a Microsoft-fiók a fogyasztók számára.

![Egyéni Azure AD bérlő tartományi fiók három](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>A PlayReady titkosított adathordozó-bővítmények használata
Olyan modern böngészőt a titkosított Media Extensions (eme) technológiával kapcsolatos PlayReady-támogatás, például az Internet Explorer 11 Windows 8.1 vagy újabb rendszeren és Windows 10-es, a Microsoft Edge böngésző PlayReady az alapul szolgáló DRM EME számára.

![PlayReady EME használata](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

A sötét player területén azért, hogy a PlayReady-védelmet megakadályozható, hogy Ön a képernyőfelvétel-készítés védett videó.

Az alábbi képernyőfelvételen a player beépülő modulok és a Microsoft Security Essentials (MSE) / EME támogatja:

![Player beépülő modulok az PlayReady](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

Lehetővé teszi, hogy a Microsoft Edge és az Internet Explorer 11 Windows 10-es EME [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) kell hívni a Windows 10 rendszerű eszközökön, amelyek támogatják ezt. PlayReady SL3000 feloldja a folyamat új és továbbfejlesztett prémium tartalom (4K, HDR) tartalom üzemeltetési modell (a bővített tartalmat).

A Windows-eszközök összpontosíthat, a PlayReady a csak DRM (PlayReady SL3000) Windows-eszközökön elérhető hardver. Streamelési szolgáltatás PlayReady EME vagy egy univerzális Windows Platform-alkalmazásból használhatja, és egy nagyobb videó minősége PlayReady SL3000, mint egy másik DRM segítségével lehetőséget. Általában Chrome vagy Firefox 2K folyamatok be tartalmat, és a tartalom legfeljebb 4K folyamatok keretében a Microsoft Edge és az Internet Explorer 11 vagy egy univerzális Windows-Platformos alkalmazások ugyanazon az eszközön. Az összeg attól függ, szolgáltatás beállításait és a megvalósítás.

#### <a name="use-eme-for-widevine"></a>Az EME használata a Widevine
Olyan modern böngészőt a EME/Widevine támogatásával, például a Chrome 41 + a Windows 10, Windows 8.1-, Mac OS x Yosemite és Chrome Android 4.4.4, a Google Widevine a DRM EME mögött.

![Az EME használata a Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine nem megakadályozhatja a védett videó képernyőfelvételt készíteni.

![Player beépülő modulok az Widevine](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>Az EME használja a fairplay rendszerhez
Ehhez hasonlóan FairPlay által védett tartalom tesztelheti a teszt lejátszóban a Safariban macOS vagy 11.2 és újabb verziók iOS rendszeren.

Ellenőrizze, hogy "FairPlay" protectionInfo.type helyezi, és helyezze a megfelelő URL-cím a Alkalmazástanúsítványát az FPS AC útvonal (FairPlay Streaming alkalmazás tanúsítvány).

### <a name="unentitled-users"></a>Unentitled felhasználók
Ha a felhasználó nem a "Jogosult felhasználók" csoport tagja, a felhasználó fennakadt a jogosultság-ellenőrzést. A DRM-mel szolgáltatás megtagadja majd adja ki a kért licenc látható módon. A részletes leírás "licenc beszerzése sikertelen volt," azaz részletezik.

![Unentitled felhasználók](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Egyéni biztonsági jogkivonatokkal kapcsolatos szolgáltatás futtatása
Ha egy egyéni STS futtatja, a JWT adja ki az egyéni STS egy szimmetrikus vagy aszimmetrikus kulccsal.

Az alábbi képernyőfelvételen egy forgatókönyvet, amely egy szimmetrikus kulcsot, (a Chrome):

![Egyéni STS a szimmetrikus kulcs](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

Az alábbi képernyőfelvételen az aszimmetrikus kulcs egy X509 keresztül használja a forgatókönyv (a Microsoft modern böngésző használata) tanúsítvány:

![Egyéni STS-aszimmetrikus kulccsal](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

Mindkét előző esetben a felhasználói hitelesítés változatlan marad. Ez történik, az Azure AD-n keresztül. Az egyetlen különbség, hogy JWTs adják ki az egyéni STS helyett az Azure ad-ben. Dinamikus CENC védelem konfigurálásakor a licenc-kézbesítési szolgáltatás korlátozás határozza meg a JWT, egy szimmetrikus vagy aszimmetrikus kulccsal.

## <a name="summary"></a>Összegzés
Ebben a dokumentumban tárgyalt a content protection 3 DRMs és révén az eszközjogkivonattal történő hitelesítés, a tervezésről és a hozzáférés-vezérlést használó Azure, az Azure Media Services és az Azure Media Player használatával.

* Egy referenciaterv bemutatott, amely tartalmazza a DRM-alrendszer az összes szükséges összetevőt.
* Egy referenciaimplementációt bemutatott Azure, az Azure Media Services és az Azure Media Player.
* Olyan témakörök, amelyek közvetlenül részt a tervezési és megvalósítási is aktorcsoportot tárgyalt.

## <a name="next-steps"></a>További lépések

[A DRM-tartalom védelme](protect-with-drm.md)
