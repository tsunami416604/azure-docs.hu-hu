---
title: Multi-DRM Content Protection rendszer tervezése hozzáférés-vezérléssel – Azure Media Services | Microsoft Docs
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
ms.openlocfilehash: ffbf53c0bb0aaf2832afecc2d0df935f04eeff19
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310331"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Egy DRM-mel a content protection rendszert a hozzáférés-vezérléssel 

Tervezése és létrehozása a digitális jogkezelési (DRM) alrendszer egy over-the-top (OTT) vagy online streamelési megoldások a bonyolult feladat. Kezelők vagy online videó szolgáltatók jellemzően kiszervezik ennek a feladatnak a speciális DRM-szolgáltatók. A jelen dokumentum célja, hogy egy referenciaterv és a egy referenciaimplementációt, egy teljes körű DRM-alrendszer OTT vagy online videoszolgáltatási megoldás.

Ebben a dokumentumban a megcélzott olvasók mérnökeitől, akik a DRM-alrendszer OTT vagy online streamelés/grafikus; megoldások, illetve olvasók, akik DRM-alrendszer. Feltételezzük, hogy olvasók ismeri a DRM-technológiák a piacon, például a PlayReady, Widevine, FairPlay vagy az Adobe hozzáférés legalább egyike.

Ebben a vitában a több DRM által támogatott 3 DRMs Azure Media Services: Common Encryption (CENC) a PlayReady és a Widevine, a FairPlay, valamint az AES-128 titkosítatlan kulcs titkosítása. Online streamelés és OTT iparág fő a tendencia, hogy natív DRMs ügyfél különböző platformokon. Erre az irányra egy az előzőre, amelyek egyetlen DRM és az ügyfél-SDK különböző ügyfél-platformokhoz való váltás. CENC használata többszörös natív DRM, ha mind a PlayReady, mind a Widevine titkosítása a [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikációnak.

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
| **Windows 10** | PlayReady | Microsoft Edge/IE11 a PlayReady|
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

* Havi előfizetés: Használjon állandó licencet és egy-a-többhöz a tartalom kulcs – eszköz leképezését. Például a gyermekek filmekhez, használjuk egy egyetlen titkosítási tartalomkulcsot. Ebben az esetben:

    Lekéri az összes gyerek filmek/eszköz licencek teljes száma = 1

* Havi előfizetés: Használjon nem állandó licencet és 1 – 1 leképezést a tartalmi kulcs és az eszköz között. Ebben az esetben:

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

Más szóval Identitásszolgáltató és az STS által biztosított Azure ad-ben. A [az Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/) a lejátszó szolgál. Mind a Azure Media Services, mind a Azure Media Player támogatja a CENC-t a DASH-en keresztül, a HLS, a PlayReady a Smooth streaming és az AES-128 titkosítást a DASH, a HLS és a Smooth FairPlay.

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

    "groupMembershipClaims": "All" (az alapértelmezett érték null)

* Állítsa be a megfelelő TokenType, eszközkorlátozásokra vonatkozó követelmények létrehozásakor.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Mivel a JWT-(Azure AD) mellett SWT (ACS) támogatása, TokenType alapértelmezés szerint TokenType.JWT. SWT/ACS használatakor, TokenType.SWT be kell állítania a jogkivonatot.

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

**Egyéni Azure ad-bérlői tartományi fiók**: Az egyéni Azure AD-bérlői tartomány testreszabott bejelentkezési lapja.

![Egyéni Azure AD bérlő tartományi fiók egyik](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Microsoft tartományi fiók intelligens kártyával**: A Microsoft vállalat által a két faktoros hitelesítéssel testreszabott bejelentkezési oldal.

![Egyéni Azure AD bérlő tartományi fiók két](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-fiók**: A felhasználók Microsoft-fiók bejelentkezési lapja.

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

## <a name="next-steps"></a>További lépések

* [Gyakori kérdések](frequently-asked-questions.md)
* [A tartalomvédelem áttekintése](content-protection-overview.md)
* [A DRM-tartalom védelme](protect-with-drm.md)
