---
title: Több DRM-tartalomvédelmi rendszer – Azure Media Services v3
description: Ez a cikk részletes leírást ad arról, hogyan tervezz meg egy több DRM-tartalomvédelmi rendszert az Azure Media Services szolgáltatással.
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
ms.openlocfilehash: fbc6d6fa8f9a3b424eaec1f04a61b5ca24fe14fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161783"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése 

A digitális jogkezelési (DRM) alrendszer tervezése és építése egy felülről felépített (OTT) vagy online streamelési megoldáshoz összetett feladat. Az operátorok/online videoszolgáltatók általában kiszervezik ezt a feladatot speciális DRM-szolgáltatóknak. A dokumentum célja, hogy egy referenciatervet és egy végpontok között DRM-alrendszer referencia-implementációját mutassa be ott vagy online streamelési megoldásban.

A dokumentum célzott olvasói olyan mérnökök, akik az OTT DRM alrendszereiben vagy az online streamelési/többképernyős megoldásokban dolgoznak, vagy olyan olvasók, akik érdeklődnek a DRM-alrendszerek iránt. A feltételezés az, hogy az olvasók ismerik legalább az egyik DRM-technológiát a piacon, például a PlayReady, a Widevine, a FairPlay vagy az Adobe Access.

Ebben a vitában a több DRM-en keresztül az Azure Media Services által támogatott 3 DRM-et is felvesszük: Common Encryption (CENC) for PlayReady és Widevine, FairPlay, valamint AES-128 titkosítás. Az online streamelés és az OTT-iparág egyik fő trendje a natív DRM-ek használata különböző ügyfélplatformokon. Ez a tendencia az előzőhez képest, amely egyetlen DRM-et és ügyfélSDK-t használt különböző ügyfélplatformokhoz. Ha a CENC-t többnatív DRM-mel használja, a PlayReady és a Widevine egyaránt titkosítva van a [Közös Titkosítás (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikáció szerint.

A tartalomvédelemhez a natív multi-DRM használatának előnyei a következők:

* Csökkenti a titkosítási költségeket, mivel egyetlen titkosítási folyamatot használnak a különböző platformok megcélzására a natív DRM-ekkel.
* Csökkenti az eszközök kezelésének költségét, mivel a tárolóban csak az eszköz egyetlen példányára van szükség.
* Kiküszöböli a DRM-ügyfél licencelési költségeit, mivel a natív DRM-ügyfél általában ingyenes a natív platformon.

### <a name="goals-of-the-article"></a>A cikk céljai

A cikk céljai a következők:

* Adjon meg egy referenciatervet egy DRM-alrendszerhez, amely mind a 3 DRM-et használja (CENC a DASH-hez, FairPlay a HLS-hez és PlayReady a zökkenőmentes streameléshez).
* Referenciaimplementáció biztosítása az Azure és az Azure Media Services platformon.
* Vitasson meg néhány tervezési és megvalósítási témát.

Az alábbi táblázat összefoglalja a natív DRM-támogatást a különböző platformokon és az EME-támogatást a különböző böngészőkben.

| **Ügyfélplatform** | **Natív DRM** | **Eme** |
| --- | --- | --- |
| **Smart TV-k, STB-k** | PlayReady, Widevine és/vagy egyéb | Beágyazott böngésző/EME PlayReady és/vagy Widevine esetén|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 a PlayReady-hez|
| **Android-eszközök (telefon, táblagép, TV)** |Widevine |Chrome a Widevine-hoz |
| **iOS** | FairPlay | Safari for FairPlay (az iOS 11.2 óta) |
| **Macos** | FairPlay | Safari for FairPlay (a Safari 9+ óta Mac OS X 10.11+ El Capitan rendszeren)|
| **tvOS** | FairPlay | |

Figyelembe véve az egyes DRM-ek üzembe helyezésének jelenlegi állapotát, a szolgáltatás általában két vagy három DRM-et szeretne megvalósítani, hogy a legjobb módon kezelje a végpontok összes típusát.

A szolgáltatáslogika összetettsége és az ügyféloldalon a felhasználói élmény egy bizonyos szintjének elérése érdekében a különböző ügyfeleken való felhasználói élmény elérése között kompromisszumot kell kötni.

A kiválasztáshoz tartsa szem előtt a következőket:

* A PlayReady natív módon valósul meg minden Windows-eszközön, egyes Android-eszközökön, és gyakorlatilag bármilyen platformon elérhető szoftversdk-eken keresztül.
* A Widevine natív módon valósul meg minden Android-eszközön, a Chrome-ban és néhány más eszközön. Widevine is támogatja a Firefox és az Opera böngészők több mint DASH.
* A FairPlay iOS, macOS és tvOS rendszeren érhető el.


## <a name="a-reference-design"></a>Referenciaterv
Ez a szakasz egy olyan referenciatervet mutat be, amely független az annak megvalósításához használt technológiákhoz.

A DRM-alrendszer a következő összetevőket tartalmazhatja:

* Kulcskezelés
* DRM titkosítási csomagolás
* DRM-licenckézbesítés
* Jogosultság-ellenőrzés/hozzáférés-ellenőrzés
* Felhasználói hitelesítés/engedélyezés
* Player alkalmazás
* Origin/content delivery network (CDN)

Az alábbi ábra a DRM-alrendszer összetevői közötti magas szintű kölcsönhatást mutatja be:

![DRM alrendszer CENC-vel](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

A design három alapvető rétegből áll:

* A háttérirodai réteg (fekete) nincs kitéve külsőleg.
* A DMZ-réteg (sötétkék) tartalmazza az összes olyan végpontot, amely a nyilvánosság elé kerül.
* A nyilvános internetes réteg (világoskék) tartalmazza a CDN és a játékosok a forgalom a nyilvános interneten.

A DRM-védelem szabályozására tartalomkezelő eszköznek is kell lennie, függetlenül attól, hogy statikus vagy dinamikus titkosításról van-e szó. A DRM-titkosítás bemenetei a következők:

* MBR videótartalom
* Tartalomkulcs
* Licencbeszerzési URL-címek

Itt van a magas szintű áramlás lejátszás közben:

* A felhasználó hitelesítve van.
* A felhasználó számára létrejön egy engedélyezési jogkivonat.
* DRM védett tartalom (manifeszt) letöltődik a lejátszóra.
* A játékos licencbeszerzési kérelmet nyújt be a licenckiszolgálóknak egy kulcsazonosítóval és egy engedélyezési tokentel együtt.

A következő szakasz a kulcskezelés tervezését ismerteti.

| **ContentKey-to-asset** | **Forgatókönyv** |
| --- | --- |
| 1 az 1-hez |A legegyszerűbb eset. Ez biztosítja a legjobb irányítást. De ez elrendezés általában eredmények -ban a legmagasabb engedély felszabadítás ár. Legalább egy licenckérelem szükséges minden védett eszközhöz. |
| 1-a-sokhoz |Ugyanazt a tartalomkulcsot több eszközhöz is használhatja. Egy logikai csoport összes eszközéhez, például egy műfajhoz vagy egy műfaj (vagy filmgén) részhalmazához például egyetlen tartalomkulcsot használhat. |
| Több az egyhez |Az egyes eszközökhöz több tartalomkulcs szükséges. <br/><br/>Ha például dinamikus CENC-védelmet kell alkalmaznia több DRM-mel az MPEG-DASH és a dinamikus AES-128 titkosítással a HLS-hez, két külön tartalomkulcsra van szükség. Minden tartalomkulcsnak saját ContentKeyType típusra van szüksége. (A dinamikus CENC-védelemhez használt tartalomkulcshoz használja a ContentKeyType.CommonEncryption titkosítást. A dinamikus AES-128 titkosításhoz használt tartalomkulcshoz használja a ContentKeyType.EnvelopeEncryption titkosítást.)<br/><br/>Egy másik példa, a CENC védelme DASH tartalom, elméletileg, akkor egy tartalom kulcs védelme a video stream és egy másik tartalom kulcs védelme a hangstream. |
| Több-a-többhöz |Az előző két forgatókönyv kombinációja. Egy tartalomkulcsok egy-egy készletét használja az azonos eszközcsoportban lévő több eszköz mindegyikéhez. |

Egy másik fontos tényező, hogy fontolja meg a tartós és nem állandó licencek.

Miért fontosak ezek a megfontolások?

Ha nyilvános felhőt használ a licenckézbesítéshez, az állandó és nem állandó licencek közvetlen hatással vannak a licenc kézbesítési költségére. A következő két különböző tervezési eset szemlélteti:

* Havi előfizetés: Használjon állandó licencet és egy-a-többhöz tartalomkulcs-eszköz leképezést. Például az összes gyerekfilmhez egyetlen tartalomkulcsot használunk a titkosításhoz. Ebben az esetben:

    Az összes gyermekfilmjére/eszközére kért licencek száma összesen = 1

* Havi előfizetés: Használjon nem állandó licencet és 1:1-es hozzárendelést a tartalomkulcs és az eszköz között. Ebben az esetben:

    Az összes gyermekfilmjére/eszközére kért licencek száma összesen = [megnézett filmek száma] x [munkamenetek száma]

A két különböző formatervezési minta nagyon eltérő licenckérelem-mintákat eredményez. A különböző minták eltérő licenckézbesítési költséget eredményeznek, ha a licenckézbesítési szolgáltatást egy nyilvános felhő, például a Media Services biztosítja.

## <a name="map-design-to-technology-for-implementation"></a>Térképtervezés a technológiához a megvalósításhoz
Ezután az általános kialakítás le van képezve az Azure/Media Services platformon lévő technológiákhoz, és adja meg, hogy melyik technológiát használja az egyes építőelemhez.

Az alábbi táblázat a leképezést mutatja be.

| **Építőelem** | **Technológia** |
| --- | --- |
| **Lejátszó** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Identitásszolgáltató (IDP)** |Azure Active Directory (Azure AD) |
| **Biztonságos jogkivonat-szolgáltatás (STS)** |Azure AD |
| **DRM védelmi munkafolyamat** |Az Azure Media Services dinamikus védelme |
| **DRM-licenckézbesítés** |* Media Services licenc szállítás (PlayReady, Widevine, FairPlay) <br/>* Axinom licenc szerver <br/>* Egyéni PlayReady licenc szerver |
| **Forrás** |Az Azure Media Services streamelési végpontja |
| **Kulcskezelés** |Nem szükséges a referencia-megvalósításhoz |
| **Tartalomkezelés** |C# konzolalkalmazás |

Más szóval az IDP és az STS az Azure AD biztosítja. Az [Azure Media Player API-t](https://amp.azure.net/libs/amp/latest/docs/) a lejátszó használja. Az Azure Media Services és az Azure Media Player egyaránt támogatja a CENC-t dash-en, fairplay-t HLS-en keresztül, playready-t a zökkenőmentes streameléshez és AES-128 titkosítást a DASH, HLS és smooth esetén.

Az alábbi ábra a teljes szerkezetet és folyamatot mutatja be az előző technológialeképezéssel:

![CENC a médiaszolgáltatásokról](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

A DRM-tartalomvédelem beállításához a tartalomkezelő eszköz a következő bemeneteket használja:

* Tartalom megnyitása
* Tartalomkulcs a kulcskezelésből
* Licencbeszerzési URL-címek
* Az Azure AD-ből származó információk, például a közönség, a kibocsátó és a tokenjogcímek listája

Itt van a kimenet a tartalomkezelő eszköz:

* A ContentKeyPolicy a DRM licencsablonját ismerteti az egyes használt DRM-típusokhoz;
* A ContentKeyPolicyRestriction a DRM-licenc kiadása előtt ismerteti a hozzáférés-vezérlést
* A streamelési politika a DRM - titkosítási mód - streamelési protokoll - tárolóformátum különböző kombinációit írja le a streameléshez
* A StreamingLocator a titkosításhoz és az URL-ek streameléséhez használt tartalomkulcsot/IV-et írja le 

A folyamat futásközben:

* A felhasználói hitelesítéskor egy JWT jön létre.
* A JWT-ben található jogcímek egyike egy csoportjogcím, amely a EntitledUserGroup csoportazonosítót tartalmazza. Ez a jogcím a jogosultsági ellenőrzésen való megfeleltetésre szolgál.
* A játékos letölti a CENC által védett tartalom ügyfélmanitkóját, és azonosítja a következőket:
   * Kulcsazonosító.
   * A tartalom DRM-védelemmel van eltorpant.
   * Licencbeszerzési URL-címek.
* A játékos licencbeszerzési kérelmet nyújt be a támogatott böngésző/DRM alapján. A licencbeszerzési kérelemben a kulcsazonosító és a JWT is beérkezik. A licenckézbesítési szolgáltatás ellenőrzi a JWT-t és a követeléseket, mielőtt kiadja a szükséges licencet.

## <a name="implementation"></a>Megvalósítás
### <a name="implementation-procedures"></a>Végrehajtási eljárások
A megvalósítás a következő lépéseket tartalmazza:

1. Teszteszközök előkészítése. Tesztvideó kódolása/csomagolása többbitráta-töredezett MP4-re a Media Services szolgáltatásban. Ez az eszköz *nem* DRM védett. A DRM-védelem később dinamikus védelemmel történik.

2. Hozzon létre egy kulcsazonosítót és egy tartalomkulcsot (opcionálisan egy kulcsmagból). Ebben az esetben a kulcskezelő rendszer nem szükséges, mert csak egy kulcsazonosító és a tartalomkulcs szükséges egy pár teszt eszközök.

3. A Media Services API-val konfigurálhatja a több DRM-licenckézbesítési szolgáltatásokat a teszteszközhöz. Ha a Media Services licencszolgáltatások helyett a vállalat vagy a vállalat szállítói egyéni licenckiszolgálókat használ, ezt a lépést kihagyhatja. A licenckézbesítés konfigurálásakor a csomagbeszerzési URL-címeket a lépésben adhatja meg. A Media Services API-ra van szükség néhány részletes konfiguráció, például az engedélyezési házirend korlátozása és a licencválasz-sablonok megadásához a különböző DRM-licencszolgáltatásokhoz. Jelenleg az Azure Portal nem biztosítja a szükséges felhasználói felületet ehhez a konfigurációhoz. Az API-szintű információkat és a mintakódot a [PlayReady és/vagy a Widevine dinamikus közös titkosításának használata című témakörben talál.](protect-with-drm.md)

4. A Media Services API-val konfigurálhatja a teszteszköz eszközkézbesítési szabályzatát. Az API-szintű információkat és a mintakódot a [PlayReady és/vagy a Widevine dinamikus közös titkosításának használata című témakörben talál.](protect-with-drm.md)

5. Hozzon létre és konfiguráljon egy Azure AD-bérlőt az Azure-ban.

6. Hozzon létre néhány felhasználói fiókot és csoportot az Azure AD-bérlőben. Hozzon létre legalább egy "Jogosult felhasználó" csoportot, és adjon hozzá egy felhasználót a csoporthoz. A csoport felhasználói megfelelnek a jogosultság-ellenőrzés licencbeszerzés. Az ebbe a csoportba nem ebbe a csoportba nem álló felhasználók nem felelnek meg a hitelesítési ellenőrzésen, és nem tudnak licencet szerezni. Ebben a "Jogosult felhasználó" csoportban egy szükséges csoport jogcím az Azure AD által kiadott JWT.Membership in this "Entitled User" group is a required groups claim in the JWT issued by Azure AD. Ezt a jogcímigényt a lépésben adja meg a több DRM-licenckézbesítési szolgáltatások konfigurálásakor.

7. Hozzon létre egy ASP.NET MVC alkalmazást a videólejátszó üzemeltetéséhez. Ez a ASP.NET alkalmazás az Azure AD-bérlővel szembeni felhasználói hitelesítéssel védett. A megfelelő jogcímeket a felhasználói hitelesítés után kapott hozzáférési jogkivonatok tartalmazzák. Ehhez a lépéshez az OpenID Connect API-t javasoljuk. Telepítse az alábbi NuGet-csomagokat:

   * Microsoft.Azure.ActiveDirectory.GraphClient telepítése-csomag
   * Telepítőcsomag Microsoft.Owin.Security.OpenIdConnect
   * Telepítőcsomag Microsoft.Owin.Security.Cookies
   * Telepítő csomag Microsoft.Owin.Host.SystemWeb
   * Telepítőcsomag Microsoft.IdentityModel.Clients.ActiveDirectory

8. Hozzon létre egy lejátszót az [Azure Media Player API használatával.](https://amp.azure.net/libs/amp/latest/docs/) Az [Azure Media Player ProtectionInfo API-val](https://amp.azure.net/libs/amp/latest/docs/) megadhatja, hogy melyik DRM-technológiát használja a különböző DRM-platformokon.

9. Az alábbi táblázat a tesztmátrixot mutatja be.

    | **Drm** | **Böngésző** | **A jogosult felhasználó találatának eredménye** | **Nem jogosult felhasználó eredménye** |
    | --- | --- | --- | --- |
    | **PlayReady** |Microsoft Edge vagy Internet Explorer 11 Windows 10 rendszeren |Sikerül |Sikertelen |
    | **Widevine** |Króm, Firefox, Opera |Sikerül |Sikertelen |
    | **FairPlay** |Safari macOS rendszeren      |Sikerül |Sikertelen |
    | **AES-128** |A legtöbb modern böngészők  |Sikerül |Sikertelen |

Az Azure AD ASP.NET MVC-lejátszóalkalmazáshoz való beállításáról az [Azure Media Services OWIN MVC-alapú alkalmazás integrálása az Azure Active Directoryval című témakörben talál, és a JWT-jogcímek alapján korlátozhatja a tartalomkulcs kézbesítését.](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

További információ: [JWT token hitelesítés az Azure Media Services és a dinamikus titkosítás.](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)  

Az Azure AD-vel kapcsolatos információkért:

* A fejlesztői információkat az [Azure Active Directory fejlesztői útmutatójában](../../active-directory/develop/v2-overview.md)találja.
* A rendszergazdai adatokat [az Azure AD-bérlői könyvtár felügyelete című dokumentumban](../../active-directory/fundamentals/active-directory-administer.md)találja.

### <a name="some-issues-in-implementation"></a>Néhány kérdés a végrehajtásban

A megvalósítási problémákkal kapcsolatos segítségért használja az alábbi hibaelhárítási információkat.

* A kibocsátó URL-címének "/" végződéssel kell végződnie. A közönségnek a lejátszóalkalmazás ügyfélazonosítójának kell lennie. Is, add "/" végén a kibocsátó URL-t.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    A [JWT dekóder](http://jwt.calebb.net/), látod **aud** és **iss**, ahogy az a JWT:

    ![Jwt](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* Engedélyek hozzáadása az alkalmazáshoz az Azure AD-ben az alkalmazás **Konfigurálás** lapján. Az engedélyek szükségesek minden alkalmazáshoz, mind a helyi, mind a telepített verziókhoz.

    ![Engedélyek](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* A dinamikus CENC-védelem beállításakor használja a megfelelő kibocsátót.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    A következő nem működik:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    A GUID az Azure AD-bérlőazonosító. A GUID az Azure Portal **Végpontok** legördülő menüjében található.

* Csoporttagsági jogosultságok megadása. Győződjön meg arról, hogy az alábbiak az Azure AD-alkalmazás jegyzékfájljában vannak: 

    "groupMembershipClaims": "All" (az alapértelmezett érték null)

* Állítsa be a megfelelő TokenType korlátozási követelmények létrehozásakor.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Mivel az SWT (ACS) mellett a JWT (Azure AD) támogatását is hozzáadja, az alapértelmezett TokenType a TokenType.JWT. Ha SWT/ACS-et használ, a token et TokenType.SWT értékre kell állítania.

## <a name="the-completed-system-and-test"></a>A befejezett rendszer és teszt

Ez a szakasz végigvezeti a következő forgatókönyveken a befejezett végpontok rendszerében, így a bejelentkezési fiók betöltése előtt alapvető képet kaphat a viselkedésről:

* Ha nem integrált forgatókönyvre van szüksége:

    * A Media Services ben tárolt, vagy nem védett vagy DRM-védelemmel ellátott, de jogkivonatos hitelesítés nélküli videoeszközök esetében (licenc kiadása annak, aki kérte), bejelentkezés nélkül tesztelheti azt. Váltson HTTP-re, ha a videó streamelése HTTP-n keresztül történik.

* Ha végpontok között integrált forgatókönyvre van szüksége:

    * A Media Services dinamikus DRM-védelem alatt álló videoeszközök esetében az Azure AD által létrehozott jogkivonat-hitelesítéssel és JWT-vel be kell jelentkeznie.

A lejátszó webes alkalmazását és bejelentkezését [ezen a webhelyen](https://openidconnectweb.azurewebsites.net/)találja.

### <a name="user-sign-in"></a>Felhasználói bejelentkezés
A végpontok között integrált DRM-rendszer teszteléséhez létre kell hoznia vagy hozzá kell adnia egy fiókot.

Milyen számlára?

Bár az Azure eredetileg csak a Microsoft-fiók felhasználói számára engedélyezett hozzáférést, a hozzáférést mostantól mindkét rendszer felhasználói engedélyezik. Az Összes Azure-tulajdonság most már megbízik az Azure AD-ben a hitelesítéshez, és az Azure AD hitelesíti a szervezeti felhasználókat. Egy összevonási kapcsolat jött létre, amelyben az Azure AD megbízik a Microsoft-fiók fogyasztói identitásrendszer ében a fogyasztói felhasználók hitelesítéséhez. Ennek eredményeképpen az Azure AD hitelesítheti a vendég Microsoft-fiókokat, valamint a natív Azure AD-fiókokat.

Mivel az Azure AD megbízik a Microsoft-fióktartományban, az alábbi tartományok bármelyikét hozzáadhatja az egyéni Azure AD-bérlőhöz, és a fiókkal bejelentkezhet:

| **Tartománynév** | **Tartomány** |
| --- | --- |
| **Egyéni Azure AD-bérlői tartomány** |somename.onmicrosoft.com |
| **Vállalati tartomány** |microsoft.com |
| **Microsoft-fiók tartománya** |live.com hotmail.com outlook.com |

Bármelyik szerzővel kapcsolatba léphet, hogy létrehozhass vagy hozzáadhass egy fiókot.

A következő képernyőképeken a különböző tartományi fiókok által használt különböző bejelentkezési oldalak láthatók:

**Egyéni Azure AD-bérlői tartományi fiók**: Az egyéni Azure AD-bérlői tartomány testreszabott bejelentkezési lapja.

![Egyéni Azure AD-s bérlői tartományi fiók](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**Microsoft tartományi fiók intelligens kártyával**: A Microsoft vállalati informatikai informatikai vállalata által kétfaktoros hitelesítéssel testre szabott bejelentkezési lap.

![Egyéni Azure AD-bérlői tartományi fiók 2](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-fiók**: A Microsoft-fiók bejelentkezési lapja a fogyasztók számára.

![Egyéni Azure AD-bérlői tartományi fiók három](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Titkosított médiabővítmények használata a PlayReady-hez

A PlayReady-támogatáshoz szükséges titkosított médiabővítményekkel (EME) rendelkező modern böngészőkben, például a Windows 8.1-es vagy újabb rendszeren futó Internet Explorer 11-ben és a Windows 10-es Microsoft Edge böngészőben a PlayReady az EME drm-je.

![EME használata a PlayReady-hez](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

A sötét lejátszó terület azért van, mert a PlayReady védelem megakadályozza, hogy képernyőfelvételt készítsen a védett videókról.

Az alábbi képernyőképen a lejátszó beépülő moduljai és a Microsoft Security Essentials (MSE)/EME-támogatás látható:

![Lejátszó bővítmények a PlayReady-hez](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

Az EME a Windows 10-es Microsoft Edge-ben és az Internet Explorer 11-ben lehetővé teszi a [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) meghívását az azt támogató Windows 10-es eszközökön. A PlayReady SL3000 feloldja a továbbfejlesztett prémium tartalmak (4K, HDR) és az új tartalomkézbesítési modellek áramlását (a továbbfejlesztett tartalom érdekében).

A Windows-eszközökre való összpontosításhoz a PlayReady az egyetlen DRM a Windows-eszközökön elérhető hardverben (PlayReady SL3000). A streamelési szolgáltatások a PlayReady-t eME-n vagy univerzális Windows Platform-alkalmazáson keresztül használhatják, és jobb videominőséget kínálnak a PlayReady SL3000 használatával, mint egy másik DRM. Általában a 2K-ig futó tartalom a Chrome-on vagy a Firefoxon keresztül áramlik, és a 4K-ig futó tartalom a Microsoft Edge/Internet Explorer 11-en vagy egy univerzális Windows Platform alkalmazáson keresztül ugyanazon az eszközön. Az összeg a szolgáltatás beállításaitól és megvalósításától függ.

#### <a name="use-eme-for-widevine"></a>EME használata a Widevine-hoz

Az EME/Widevine támogatással rendelkező modern böngészőkben, mint például a Chrome 41+ Windows 10-en, Windows 8.1-en, Mac OSX Yosemite-en és Chrome-on Android 4.4.4-en, a Google Widevine a DRM az EME mögött.

![EME használata a Widevine-hoz](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

A Widevine nem akadályozza meg abban, hogy képernyőfelvételt készítsen a védett videókról.

![Lejátszó plug-inek a Widevine-hoz](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>EME használata a FairPlay-hez

Hasonlóképpen tesztelheti a FairPlay által védett tartalmakat ebben a tesztlejátszóban a Safariban macOS vagy iOS 11.2-es és újabb rendszeren.

Győződjön meg róla, hogy a "FairPlay" -t tette fel protectionInfo.type néven, és az alkalmazástanúsítvány megfelelő URL-címét az FPS AC path (FairPlay Streaming alkalmazástanúsítvány elérési útja) címére.

### <a name="unentitled-users"></a>Nem jogosult felhasználók

Ha egy felhasználó nem tagja a "Jogosult felhasználók" csoportnak, a felhasználó nem felel meg a jogosultsági ellenőrzésen. A multi-DRM licencszolgáltatás ezután megtagadja a kért licenc kiadását az ábrán látható módon. A részletes leírás a "Licenc beszerzése nem sikerült", amely a tervezett.

![Nem jogosult felhasználók](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Egyéni biztonsági jogkivonat-szolgáltatás futtatása

Ha egy egyéni STS-t futtat, a JWT-t az egyéni STS szimmetrikus vagy aszimmetrikus kulccsal bocsátja ki.

A következő képernyőképen egy szimmetrikus kulcsot használó forgatókönyv látható (a Chrome használatával):

![Egyedi STS szimmetrikus kulccsal](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

A következő képernyőképen egy olyan forgatókönyv látható, amely egy Aszimmetrikus kulcsot használ X509-es tanúsítvánnyal (microsoftos modern böngészővel):

![Egyedi STS aszimmetrikus kulccsal](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

Mindkét korábbi esetben a felhasználói hitelesítés ugyanaz marad. Az Azure AD-n keresztül történik. Az egyetlen különbség az, hogy a JWT-ket az egyéni STS bocsátja ki az Azure AD helyett. A dinamikus CENC-védelem konfigurálásakor a licenckézbesítési szolgáltatás korlátozása meghatározza a JWT típusát, akár szimmetrikus, akár aszimmetrikus kulcsot.

## <a name="next-steps"></a>További lépések

* [Gyakori kérdések](frequently-asked-questions.md)
* [Tartalomvédelem – áttekintés](content-protection-overview.md)
* [A tartalom védelme drm-mel](protect-with-drm.md)
