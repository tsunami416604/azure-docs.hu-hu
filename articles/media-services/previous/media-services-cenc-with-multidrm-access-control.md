---
title: Tartalomvédelmi rendszer kialakítása hozzáférés-vezérléssel az Azure Media Services használatával | Microsoft dokumentumok
description: További információ a Microsoft Smooth Streaming ügyfélportingkészlet licenceléséről.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.openlocfilehash: 68f42aa13288c2416257f3ba6c0b6072c1572977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162990"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Tartalomvédelmi rendszer tervezése hozzáférés-vezérléssel az Azure Media Services használatával 

## <a name="overview"></a>Áttekintés

A digitális jogkezelési (DRM) alrendszer tervezése és építése egy felülről felépített (OTT) vagy online streamelési megoldáshoz összetett feladat. Az operátorok/online videoszolgáltatók általában kiszervezik ezt a feladatot speciális DRM-szolgáltatóknak. A dokumentum célja, hogy egy end-to-end DRM alrendszer referenciatervét és megvalósítását bemutassa ott vagy online streamelési megoldásban.

A dokumentum célzott olvasói olyan mérnökök, akik az OTT DRM alrendszereiben vagy az online streamelési/többképernyős megoldásokban dolgoznak, vagy olyan olvasók, akik érdeklődnek a DRM-alrendszerek iránt. A feltételezés az, hogy az olvasók ismerik legalább az egyik DRM-technológiát a piacon, például a PlayReady, a Widevine, a FairPlay vagy az Adobe Access.

Ebben a vitában a DRM, mi is közös titkosítás (CENC) a multi-DRM. Az online streaming és az OTT iparág egyik fő trendje, hogy a CENC-t több natív DRM-mel használja különböző ügyfélplatformokon. Ez a tendencia az előzőhez képest, amely egyetlen DRM-et és ügyfélSDK-t használt különböző ügyfélplatformokhoz. Ha a CENC-t többnatív DRM-mel használja, a PlayReady és a Widevine egyaránt titkosítva van a [Közös Titkosítás (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) specifikáció szerint.

A CENC több DRM-mel járó előnyei az, hogy:

* Csökkenti a titkosítási költségeket, mivel egyetlen titkosítási folyamatot használnak a különböző platformok megcélzására a natív DRM-ekkel.
* Csökkenti a titkosított eszközök kezelésének költségeit, mivel a titkosított eszközöknek csak egy példányára van szükség.
* Kiküszöböli a DRM-ügyfél licencelési költségeit, mivel a natív DRM-ügyfél általában ingyenes a natív platformon.

A Microsoft a DASH és a CENC aktív támogatója néhány jelentős iparági szereplővel együtt. Az Azure Media Services támogatja a DASH-t és a CENC-et. A legutóbbi bejelentések, lásd a következő blogok:

*  [Announcing Google Widevine license delivery services in Azure Media Services (A Google Widevine-licenctovábbítási szolgáltatás megjelenése az Azure Media Services szolgáltatásban)](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Az Azure Media Services hozzáadja a Google Widevine csomagolását a több DRM-adatfolyam](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>A cikk céljai

A cikk céljai a következők:

* Adjon meg egy referenciatervet egy DRM-alrendszerhez, amely cenc-t használ több DRM-mel.
* Referenciaimplementáció biztosítása egy Azure/Media Services platformon.
* Vitasson meg néhány tervezési és megvalósítási témát.

A cikkben a "multi-DRM" kifejezés a következő termékekre vonatkozik:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

Az alábbi táblázat összefoglalja az egyes DRM-ek által támogatott natív platformot/natív alkalmazást és böngészőket.

| **Ügyfélplatform** | **Natív DRM-támogatás** | **Böngésző/alkalmazás** | **Streamelési formátumok** |
| --- | --- | --- | --- |
| **Smart TV-k, kezelő STB-k, OTT STB-k** |PlayReady elsősorban, és / vagy Widevine, és / vagy más |Linux, Opera, WebKit, egyéb |Különböző formátumok |
| **Windows 10-es eszközök (Windows PC, Windows táblagépek, Windows Phone, Xbox)** |PlayReady |Microsoft Edge/IE11/EME<br/><br/><br/>Univerzális Windows-platform |DASH (HLS esetén a PlayReady nem támogatott)<br/><br/>DASH, Smooth Streaming (HLS esetén a PlayReady nem támogatott) |
| **Android-eszközök (telefon, táblagép, TV)** |Widevine |Króm/EME |KÖTŐJEL, HLS |
| **iOS (iPhone, iPad), OS X kliensek és Apple TV** |FairPlay |Safari 8+/EME |HLS |

Figyelembe véve az egyes DRM-ek üzembe helyezésének jelenlegi állapotát, a szolgáltatás általában két vagy három DRM-et szeretne megvalósítani, hogy a legjobb módon kezelje a végpontok összes típusát.

A szolgáltatáslogika összetettsége és az ügyféloldalon a felhasználói élmény egy bizonyos szintjének elérése érdekében a különböző ügyfeleken való felhasználói élmény elérése között kompromisszumot kell kötni.

A kiválasztáshoz tartsa szem előtt a következőket:

* A PlayReady natív módon valósul meg minden Windows-eszközön, egyes Android-eszközökön, és gyakorlatilag bármilyen platformon elérhető szoftversdk-eken keresztül.
* A Widevine natív módon valósul meg minden Android-eszközön, a Chrome-ban és néhány más eszközön.
* A FairPlay csak iOS és Mac OS klienseken vagy az iTunes-on keresztül érhető el.

Egy tipikus több DRM-re két lehetőség van:

* PlayReady és Widevine
* PlayReady, Widevine és FairPlay

## <a name="a-reference-design"></a>Referenciaterv
Ez a szakasz egy olyan referenciatervet mutat be, amely független az annak megvalósításához használt technológiákhoz.

A DRM-alrendszer a következő összetevőket tartalmazhatja:

* Kulcskezelés
* DRM csomagolás
* DRM-licenckézbesítés
* Jogosultság ellenőrzése
* Hitelesítés/engedélyezés
* Lejátszó
* Origin/content delivery network (CDN)

Az alábbi ábra a DRM-alrendszer összetevői közötti magas szintű kölcsönhatást mutatja be:

![DRM alrendszer CENC-vel](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Biztonsági jogkivonat-szolgáltatás (STS)** |Azure AD |
| **DRM védelmi munkafolyamat** |A Media Services dinamikus védelme |
| **DRM-licenckézbesítés** |* Media Services licenc szállítás (PlayReady, Widevine, FairPlay) <br/>* Axinom licenc szerver <br/>* Egyéni PlayReady licenc szerver |
| **Forrás** |Media Services streamelési végpontja |
| **Kulcskezelés** |Nem szükséges a referencia-megvalósításhoz |
| **Tartalomkezelés** |C# konzolalkalmazás |

Más szóval az IDP és az STS az Azure AD-vel is használható. Az [Azure Media Player API-t](https://amp.azure.net/libs/amp/latest/docs/) a lejátszó használja. A Media Services és a Media Player egyaránt támogatja a DASH-t és a CENC-t a több DRM-mel.

Az alábbi ábra a teljes szerkezetet és folyamatot mutatja be az előző technológialeképezéssel:

![CENC a médiaszolgáltatásokról](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

A dinamikus CENC titkosítás beállításához a tartalomkezelő eszköz a következő bemeneteket használja:

* Tartalom megnyitása
* Tartalomkulcs a kulcsgenerálásból/-kezelésből
* Licencbeszerzési URL-címek
* Az Azure AD információinak listája

Itt van a kimenet a tartalomkezelő eszköz:

* A ContentKeyAuthorizationPolicy tartalmazza a JSON webtoken (JWT) és a DRM-licencspecifikációk ellenőrzésének specifikációját.
* AssetDeliveryPolicy tartalmaz specifikációk streamelési formátum, DRM-védelem, és a licencbeszerzés URL-eket.

A folyamat futásközben:

* A felhasználói hitelesítéskor egy JWT jön létre.
* A JWT-ben található jogcímek egyike egy csoportjogcím, amely a EntitledUserGroup csoportazonosítót tartalmazza. Ez a jogcím a jogosultsági ellenőrzésen való megfeleltetésre szolgál.
* A játékos letölti a CENC által védett tartalom ügyfélmanitkóját, és azonosítja a következőket:
   * Kulcsazonosító.
   * A tartalom CENC védelem alatt áll.
   * Licencbeszerzési URL-címek.
* A játékos licencbeszerzési kérelmet nyújt be a támogatott böngésző/DRM alapján. A licencbeszerzési kérelemben a kulcsazonosító és a JWT is beérkezik. A licenckézbesítési szolgáltatás ellenőrzi a JWT-t és a követeléseket, mielőtt kiadja a szükséges licencet.

## <a name="implementation"></a>Megvalósítás
### <a name="implementation-procedures"></a>Végrehajtási eljárások
A megvalósítás a következő lépéseket tartalmazza:

1. Teszteszközök előkészítése. Tesztvideó kódolása/csomagolása többbitráta-töredezett MP4-re a Media Services szolgáltatásban. Ez az eszköz *nem* DRM védett. A DRM-védelem később dinamikus védelemmel történik.

2. Hozzon létre egy kulcsazonosítót és egy tartalomkulcsot (opcionálisan egy kulcsmagból). Ebben az esetben a kulcskezelő rendszer nem szükséges, mert csak egy kulcsazonosító és a tartalomkulcs szükséges egy pár teszt eszközök.

3. A Media Services API-val konfigurálhatja a több DRM-licenckézbesítési szolgáltatásokat a teszteszközhöz. Ha a Media Services licencszolgáltatások helyett a vállalat vagy a vállalat szállítói egyéni licenckiszolgálókat használ, ezt a lépést kihagyhatja. A licenckézbesítés konfigurálásakor a csomagbeszerzési URL-címeket a lépésben adhatja meg. A Media Services API-ra van szükség néhány részletes konfiguráció, például az engedélyezési házirend korlátozása és a licencválasz-sablonok megadásához a különböző DRM-licencszolgáltatásokhoz. Jelenleg az Azure Portal nem biztosítja a szükséges felhasználói felületet ehhez a konfigurációhoz. Az API-szintű információkat és a mintakódot a [PlayReady és/vagy a Widevine dinamikus közös titkosításának használata című témakörben talál.](media-services-protect-with-playready-widevine.md)

4. A Media Services API-val konfigurálhatja a teszteszköz eszközkézbesítési szabályzatát. Az API-szintű információkat és a mintakódot a [PlayReady és/vagy a Widevine dinamikus közös titkosításának használata című témakörben talál.](media-services-protect-with-playready-widevine.md)

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

* A fejlesztői információkat az [Azure Active Directory fejlesztői útmutatójában](../../active-directory/azuread-dev/v1-overview.md)találja.
* A rendszergazdai adatokat [az Azure AD-bérlői könyvtár felügyelete című dokumentumban](../../active-directory/fundamentals/active-directory-administer.md)találja.

### <a name="some-issues-in-implementation"></a>Néhány kérdés a végrehajtásban
A megvalósítási problémákkal kapcsolatos segítségért használja az alábbi hibaelhárítási információkat.

* A kibocsátó URL-címének "/" végződéssel kell végződnie. A közönségnek a lejátszóalkalmazás ügyfélazonosítójának kell lennie. Is, add "/" végén a kibocsátó URL-t.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    A [JWT dekóder](http://jwt.calebb.net/), látod **aud** és **iss**, ahogy az a JWT:

    ![Jwt](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Engedélyek hozzáadása az alkalmazáshoz az Azure AD-ben az alkalmazás **Konfigurálás** lapján. Az engedélyek szükségesek minden alkalmazáshoz, mind a helyi, mind a telepített verziókhoz.

    ![Engedélyek](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

## <a name="additional-topics-for-implementation"></a>További végrehajtási témák
Ez a szakasz a tervezés és a megvalósítás néhány további témakörét ismerteti.

### <a name="http-or-https"></a>HTTP vagy HTTPS?
A ASP.NET MVC lejátszó alkalmazásnak a következőket kell támogatnia:

* Felhasználói hitelesítés az Azure AD-n keresztül, amely HTTPS alatt van.
* JWT csere az ügyfél és az Azure AD, amely HTTPS alatt.
* DRM-licencbeszerzés az ügyfél által, amelynek HTTPS alatt kell lennie, ha a Media Services biztosítja a licenckézbesítést. A PlayReady termékcsomag nem írja elő a HTTPS-t a licenc kézbesítéséhez. Ha a PlayReady licenckiszolgálója a Media Services szolgáltatáson kívül található, http vagy HTTPS protokollt is használhat.

A ASP.NET lejátszó alkalmazás https protokollt használ a legjobb gyakorlatként, így a Media Player https alatt található. A HTTP azonban előnyben részesítésre szolgál a streameléshez, ezért figyelembe kell vennie a vegyes tartalom kérdését.

* A böngésző nem engedélyezi a vegyes tartalmat. De plug-inek, mint a Silverlight és az OSMF plug-in a sima és DASH nem teszik lehetővé. A vegyes tartalom biztonsági szempont, mivel fennáll a veszélye a rosszindulatú JavaScript-írás befecskendezésének, ami az ügyféladatok veszélyének teheti ki. A böngészők alapértelmezés szerint blokkolják ezt a funkciót. Az egyetlen módja annak, hogy a munka körül van a szerver (forrás) oldalon, amely lehetővé teszi az összes domain (függetlenül a TTÓL, HOGY HTTPS vagy HTTP). Ez valószínűleg nem is jó ötlet.
* Kerülje a vegyes tartalmat. Mind a lejátszóalkalmazásnak, mind a Media Playernek HTTP-t vagy HTTPS-t kell használnia. Vegyes tartalom lejátszásakor a silverlightSS tech-nek törölnie kell a vegyes tartalomra vonatkozó figyelmeztetést. A flashSS tech kezeli a vegyes tartalom nélkül vegyes tartalom figyelmeztetés.
* Ha a streamelési végpont 2014 augusztusa előtt jött létre, nem támogatja a HTTPS-t. Ebben az esetben hozzon létre és használjon egy új streamelési végpontot https.In this case, create and use a new stream endpoint for HTTPS.

A DRM-védelemmel ellátott tartalmak referenciaimplementációjában mind az alkalmazás, mind a streamelés HTTPS alatt van. A megnyitott tartalmakhoz a lejátszónak nincs szüksége hitelesítésre vagy licencre, így http-t vagy HTTPS-t is használhat.

### <a name="azure-active-directory-signing-key-rollover"></a>Az Azure Active Directory aláírókulcs-váltás
A kulcsváltás aláírása fontos szempont a megvalósítás során. Ha figyelmen kívül hagyja, a kész rendszer végül leáll teljesen, hat héten belül legkorábban.

Az Azure AD iparági szabványokat használ a bizalom megteremtéséhez saját maga és az Azure AD-t használó alkalmazások között. Pontosabban az Azure AD egy aláíró kulcsot használ, amely egy nyilvános és személyes kulcspárból áll. Amikor az Azure AD létrehoz egy biztonsági jogkivonatot, amely információkat tartalmaz a felhasználóról, az Azure AD egy személyes kulccsal alá, mielőtt visszaküldi az alkalmazásnak. Annak ellenőrzéséhez, hogy a jogkivonat érvényes-e, és az Azure AD-től származik, az alkalmazásnak érvényesítenie kell a jogkivonat aláírását. Az alkalmazás az Azure AD által elérhetővé tett nyilvános kulcsot használja, amely a bérlő összevonási metaadat-dokumentumában található. Ez a nyilvános kulcs, és az aláíró kulcs, amelyből származik, ugyanaz, amelyet az Azure AD összes bérlője használt.

Az Azure AD-kulcsváltásról az [Azure AD-ben való aláírásával kapcsolatos fontos tudnivalók című](../../active-directory/active-directory-signing-key-rollover.md)témakörben talál további információt.

A [nyilvános-titkos kulcspár](https://login.microsoftonline.com/common/discovery/keys/)között:

* A személyes kulcsot az Azure AD jwt létrehozásához használja.
* A nyilvános kulcsot egy alkalmazás, például a Media Services DRM licenckézbesítési szolgáltatásai használják a JWT ellenőrzéséhez.

Biztonsági okokból az Azure AD rendszeresen (hathetente) elforgatja a tanúsítványt. A biztonság megsértése esetén a kulcsváltás bármikor előfordulhat. Ezért a Media Services licenckézbesítési szolgáltatásainak frissítenie kell a nyilvános kulcsot, mivel az Azure AD elforgatja a kulcspárt. Ellenkező esetben a Médiaszolgáltatások ban a jogkivonat-hitelesítés sikertelen, és nem ad ki licencet.

A szolgáltatás beállításához a TOKENRestrictionTemplate.OpenIdConnectDiscoveryDocument beállítást a DRM licenckézbesítési szolgáltatások konfigurálásakor kell beállítani.

Itt a JWT áramlás:

* Az Azure AD kiadja a JWT-t egy hitelesített felhasználó aktuális titkos kulcsával.
* Amikor egy játékos több DRM-védelemmel ellátott tartalommal rendelkező CENC-t lát, először megkeresi az Azure AD által kiadott JWT-t.
* A játékos licencbeszerzési kérelmet küld a JWT-nek a Media Services kézbesítési szolgáltatásainak licencelésére.
* A Media Services licenckézbesítési szolgáltatásai az Azure AD aktuális/érvényes nyilvános kulcsát használják a JWT ellenőrzéséhez a licencek kiadása előtt.

A DRM-licenckézbesítési szolgáltatások mindig ellenőrzik az aktuális/érvényes nyilvános kulcsot az Azure AD-ből. Az Azure AD által bemutatott nyilvános kulcs az Azure AD által kiadott JWT ellenőrzéséhez használt kulcs.

Mi a teendő, ha a kulcsváltás után történik, miután az Azure AD létrehoz egy JWT-t, de mielőtt a JWT-t a játékosok elküldik a DRM licenckézbesítési szolgáltatásainak a Media Services-ben ellenőrzés céljából?

Mivel egy kulcs bármikor átállítható, az összevonási metaadat-dokumentumban mindig több érvényes nyilvános kulcs érhető el. A Media Services licenckézbesítése a dokumentumban megadott kulcsok bármelyikét használhatja. Mivel az egyik kulcs lehet hengerelt hamarosan, egy másik lehet a csere, és így tovább.

### <a name="where-is-the-access-token"></a>Hol van a hozzáférési jogkivonat?
Ha azt vizsgálja, hogy egy webalkalmazás hogyan hívja meg az API-alkalmazásokat [az Alkalmazásidentitás oAuth 2.0 ügyfélhitelesítő adatokkal biztosítva,](../../active-directory/azuread-dev/web-api.md)a hitelesítési folyamat a következő:

* A felhasználó bejelentkezik az Azure AD-be a webalkalmazásban. További információt a [Webböngészőből webalkalmazásba](../../active-directory/azuread-dev/web-app.md)című témakörben talál.
* Az Azure AD engedélyezési végpont átirányítja a felhasználói ügynököt vissza az ügyfélalkalmazás egy engedélyezési kódot. A felhasználói ügynök visszaadja az engedélyezési kódot az ügyfélalkalmazás átirányítási URI-jának.
* A webalkalmazásnak be kell szereznie egy hozzáférési jogkivonatot, hogy hitelesíthesse magát a webes API-ban, és lekérdezhesse a kívánt erőforrást. Kérést küld az Azure AD-jogkivonat-végpontnak, és biztosítja a hitelesítő adatokat, az ügyfélazonosítót és a webes API alkalmazásazonosító URI-ját. Bemutatja az engedélyezési kódot annak igazolására, hogy a felhasználó hozzájárult.
* Az Azure AD hitelesíti az alkalmazást, és egy JWT-hozzáférési jogkivonatot ad vissza, amely a webes API-hívásához használatos.
* HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT-hozzáférési jogkivonatot használja a JWT-karakterlánc hozzáadásához a "Bemutató" megjelöléssel a kérelem "Engedélyezés" fejlécében a webes API-hoz. The web API then validates the JWT. Ha az ellenőrzés sikeres, a kívánt erőforrást adja vissza.

Ebben az alkalmazásidentitás-folyamatban a webes API megbízik abban, hogy a webalkalmazás hitelesíti a felhasználót. Emiatt ezt a mintát megbízható alrendszernek nevezzük. Az [engedélyezési folyamatábrán](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) bemutatja, hogyan működik az engedélyezési kód-engedélyezési folyamat.

A jogkivonat-korlátozással történő licencbeszerzés ugyanazt a megbízható alrendszermintát követi. A Media Services licenckézbesítési szolgáltatása a webes API-erőforrás, vagy a "háttér-erőforrás", amelyhez egy webalkalmazásnak szüksége van. Hol van a hozzáférési jogkivonat?

A hozzáférési jogkivonat az Azure AD-től származik. A sikeres felhasználói hitelesítés után a rendszer egy engedélyezési kódot ad vissza. Az engedélyezési kódot ezután az ügyfélazonosítóval és az alkalmazáskulccsal együtt a hozzáférési jogkivonat cseréjére használják. A hozzáférési jogkivonat egy "pointer" alkalmazás elérésére szolgál, amely a Media Services licenckézbesítési szolgáltatására mutat vagy képviseli.

A mutatóalkalmazás azure AD-ben való regisztrálásához és konfigurálásához tegye a következő lépéseket:

1. Az Azure AD-bérlőben:

   * Adjon hozzá egy alkalmazást (erőforrást) a https://[resource_name.azurewebsites.net/ bejelentkezési URL-címmel. 
   * Adjon hozzá egy alkalmazásazonosítót a https://[aad_tenant_name].onmicrosoft.com/[resource_name] URL-címmel.

2. Adjon hozzá egy új kulcsot az erőforrásalkalmazáshoz.

3. Frissítse az alkalmazásjegyzék-fájlt úgy, hogy a groupMembershipClaims tulajdonság értéke "groupMembershipClaims": "All".

4. Az Azure AD-alkalmazásban, amely a lejátszó webalkalmazásra mutat, az **Engedélyek más alkalmazásokhoz**című szakaszban adja hozzá az 1. A **Delegált engedély csoportban**válassza **az Access [resource_name]** lehetőséget. Ez a beállítás engedélyt ad a webalkalmazásnak az erőforrásalkalmazáshoz hozzáférő hozzáférési jogkivonatok létrehozására. Ezt a webalkalmazás helyi és üzembe helyezett verziójával is végezze el, ha a Visual Studio és az Azure webalkalmazás használatával fejleszt.

Az Azure AD által kiadott JWT a mutató erőforrás eléréséhez használt hozzáférési jogkivonat.

### <a name="what-about-live-streaming"></a>Mi a helyzet az élő közvetítés?
Az előző vita az igény szerinti eszközökre összpontosított. Mi a helyzet az élő közvetítés?

Pontosan ugyanazt a kialakítást és megvalósítást használhatja az élő közvetítés védelmére a Media Services szolgáltatásban, ha a programhoz társított eszközt VOD-eszközként kezeli.

Pontosabban, hogy nem élő közvetítés a Media Services, létre kell hoznia egy csatornát, majd hozzon létre egy programot a csatorna alatt. A program létrehozásához létre kell hoznia egy eszközt, amely tartalmazza a program élő archívumát. Ahhoz, hogy a CENC több DRM-védelmet nyújtson az élő tartalomnak, alkalmazza ugyanazt a beállítást/feldolgozást az eszközre, mintha VOD eszköz lenne a program indítása előtt.

### <a name="what-about-license-servers-outside-media-services"></a>Mi a helyzet a Media Servicesen kívüli licenckiszolgálókkal?
Az ügyfelek gyakran saját adatközpontjukban vagy drm-szolgáltatók által üzemeltetett licenckiszolgáló-farmba fektettek be. A Media Services Content Protection segítségével hibrid módban is működhet. A tartalom üzemeltethető és dinamikusan védhető a Media Services szolgáltatásban, míg a DRM-licenceket a Media Services szolgáltatáson kívüli kiszolgálók szállítják. Ebben az esetben vegye figyelembe a következő módosításokat:

* Az STS-nek elfogadható és a licenckiszolgáló-farm által ellenőrizhető jogkivonatokat kell kiadnia. Például az Axinom által biztosított Widevine licenckiszolgálókhoz egy jogosultsági üzenetet tartalmazó adott JWT szükséges. Ezért egy Ilyen JWT kibocsátásához sts-re van szükség. Az ilyen típusú implementációról az [Azure Dokumentációs központban](https://azure.microsoft.com/documentation/) tájékozódhat, és az [Axinom használata widevine-licenceket biztosít az Azure Media Services szolgáltatáshoz](media-services-axinom-integration.md)című témakörben talál.
* A továbbiakban nem kell konfigurálnia a licenckézbesítési szolgáltatást (ContentKeyAuthorizationPolicy) a Media Services szolgáltatásban. A licencbeszerzési URL-címeket (PlayReady, Widevine és FairPlay esetén) kell megadnia, amikor az AssetDeliveryPolicy-et úgy konfigurálja, hogy a CENC-et több DRM-mel állítsa be.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Mi a teendő, ha egyéni STS-t szeretnék használni?
Az ügyfél dönthet úgy, hogy egy egyéni STS-t használ a JWT-k biztosításához. Ennek okai a következők:

* Az ügyfél által használt IDP nem támogatja az STS-t. Ebben az esetben egy egyéni STS lehet egy lehetőség.
* Az ügyfélnek rugalmasabb vagy szigorúbb vezérlésre lehet szüksége ahhoz, hogy integrálja az STS-t az ügyfél előfizetői számlázási rendszerébe. Egy MVPD-operátor például több OTT-előfizetői csomagot is kínálhat, például prémium, alapszintű és sportverziókat. Előfordulhat, hogy az operátor szeretné egyeztetni a jogcímeket egy jogkivonatban egy előfizetői csomaggal, hogy csak egy adott csomag tartalma legyen elérhető. Ebben az esetben egy egyéni STS biztosítja a szükséges rugalmasságot és vezérlést.

Egyéni STS használata esetén két módosítást kell végrehajtani:

* Amikor konfigurálja a licenckézbesítési szolgáltatás egy eszköz, meg kell adnia a biztonsági kulcsot, amelyet az egyéni STS ellenőrzése helyett az aktuális kulcsot az Azure AD.When you configure license delivery service for an asset, you need to specify the security key used for verification by the custom STS instead of the current key from Azure AD. (További részletek következnek.) 
* JTW-jogkivonat létrehozásakor az Azure AD-ben az aktuális X509-tanúsítvány titkos kulcsa helyett egy biztonsági kulcs van megadva.

A biztonsági kulcsoknak két típusa van:

* Szimmetrikus kulcs: Ugyanaz a kulcs a JWT létrehozásához és ellenőrzéséhez használható.
* Aszimmetrikus kulcs: Az X509-tanúsítvány nyilvános és titkos kulcspárja egy titkos kulccsal titkos kulccsal titkos ítja a JWT titkosítását/generálását, a nyilvános kulccsal pedig a jogkivonat ellenőrzéséhez.

> [!NOTE]
> Ha fejlesztési platformként a .NET Framework/C# kapcsolót használja, az aszimmetrikus biztonsági kulcshoz használt X509-tanúsítvány kulcsának legalább 2048-nak kell lennie. Ez a system.IdentityModel.Tokens.X509AsymmetricSecurityKey osztály követelménye a . Ellenkező esetben a következő kivétel jelenik meg:
> 
> IDX10630: A 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' aláíráshoz nem lehet kisebb, mint a "2048" bit.

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

![Egyéni Azure AD-bérlői tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft tartományi fiók intelligens kártyával**: A Microsoft vállalati informatikai informatikai vállalata által kétfaktoros hitelesítéssel testre szabott bejelentkezési lap.

![Egyéni Azure AD-bérlői tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-fiók**: A Microsoft-fiók bejelentkezési lapja a fogyasztók számára.

![Egyéni Azure AD-bérlői tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Titkosított médiabővítmények használata a PlayReady-hez
A PlayReady-támogatáshoz szükséges titkosított médiabővítményekkel (EME) rendelkező modern böngészőkben, például a Windows 8.1-es vagy újabb rendszeren futó Internet Explorer 11-ben és a Windows 10-es Microsoft Edge böngészőben a PlayReady az EME drm-je.

![EME használata a PlayReady-hez](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A sötét lejátszó terület azért van, mert a PlayReady védelem megakadályozza, hogy képernyőfelvételt készítsen a védett videókról.

Az alábbi képernyőképen a lejátszó beépülő moduljai és a Microsoft Security Essentials (MSE)/EME-támogatás látható:

![Lejátszó bővítmények a PlayReady-hez](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Az EME a Windows 10-es Microsoft Edge-ben és az Internet Explorer 11-ben lehetővé teszi a [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) meghívását az azt támogató Windows 10-es eszközökön. A PlayReady SL3000 feloldja a továbbfejlesztett prémium tartalmak (4K, HDR) és az új tartalomkézbesítési modellek áramlását (a továbbfejlesztett tartalom érdekében).

A Windows-eszközökre való összpontosításhoz a PlayReady az egyetlen DRM a Windows-eszközökön elérhető hardverben (PlayReady SL3000). A streamelési szolgáltatások a PlayReady-t eME-n vagy univerzális Windows Platform-alkalmazáson keresztül használhatják, és jobb videominőséget kínálnak a PlayReady SL3000 használatával, mint egy másik DRM. Általában a 2K-ig futó tartalom a Chrome-on vagy a Firefoxon keresztül áramlik, és a 4K-ig futó tartalom a Microsoft Edge/Internet Explorer 11-en vagy egy univerzális Windows Platform alkalmazáson keresztül ugyanazon az eszközön. Az összeg a szolgáltatás beállításaitól és megvalósításától függ.

#### <a name="use-eme-for-widevine"></a>EME használata a Widevine-hoz
Az EME/Widevine támogatással rendelkező modern böngészőkben, mint például a Chrome 41+ Windows 10-en, Windows 8.1-en, Mac OSX Yosemite-en és Chrome-on Android 4.4.4-en, a Google Widevine a DRM az EME mögött.

![EME használata a Widevine-hoz](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

A Widevine nem akadályozza meg abban, hogy képernyőfelvételt készítsen a védett videókról.

![Lejátszó plug-inek a Widevine-hoz](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Nem jogosult felhasználók
Ha egy felhasználó nem tagja a "Jogosult felhasználók" csoportnak, a felhasználó nem felel meg a jogosultsági ellenőrzésen. A multi-DRM licencszolgáltatás ezután megtagadja a kért licenc kiadását az ábrán látható módon. A részletes leírás a "Licenc beszerzése nem sikerült", amely a tervezett.

![Nem jogosult felhasználók](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Egyéni biztonsági jogkivonat-szolgáltatás futtatása
Ha egy egyéni STS-t futtat, a JWT-t az egyéni STS szimmetrikus vagy aszimmetrikus kulccsal bocsátja ki.

A következő képernyőképen egy szimmetrikus kulcsot használó forgatókönyv látható (a Chrome használatával):

![Egyedi STS szimmetrikus kulccsal](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

A következő képernyőképen egy olyan forgatókönyv látható, amely egy Aszimmetrikus kulcsot használ X509-es tanúsítvánnyal (microsoftos modern böngészővel):

![Egyedi STS aszimmetrikus kulccsal](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Mindkét korábbi esetben a felhasználói hitelesítés ugyanaz marad. Az Azure AD-n keresztül történik. Az egyetlen különbség az, hogy a JWT-ket az egyéni STS bocsátja ki az Azure AD helyett. A dinamikus CENC-védelem konfigurálásakor a licenckézbesítési szolgáltatás korlátozása meghatározza a JWT típusát, akár szimmetrikus, akár aszimmetrikus kulcsot.

## <a name="summary"></a>Összefoglalás

Ez a dokumentum a CENC-et több natív DRM-mel és hozzáférés-vezérléssel tárgyalta meg a tokenhitelesítés, a tervezés és annak megvalósítása révén az Azure, a Media Services és a Media Player használatával.

* Bemutattak egy referenciatervet, amely tartalmazza a DRM/CENC alrendszer összes szükséges alkatrészét.
* Az Azure, a Media Services és a Media Player referenciaimplementációja bemutatásra került.
* A tervezésben és a megvalósításban közvetlenül részt vevő egyes témákat is megvitatták.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
