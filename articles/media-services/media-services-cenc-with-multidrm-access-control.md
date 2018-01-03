---
title: "CENC multi-DRM- és hozzáférés-vezérléssel: egy hivatkozás tervezési és megvalósítási Azure és az Azure Media Services |} Microsoft Docs"
description: "További tudnivalók a Microsoft Smooth Streaming ügyfél eljárás Kit módjával."
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
ms.openlocfilehash: cac1513d805e01f2c9633b3b318ad6808027904e
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC multi-DRM- és hozzáférés-vezérléssel: egy hivatkozás tervezési és megvalósítási Azure és az Azure Media Services
 
## <a name="introduction"></a>Bevezetés
Kialakítása és felépítése egy over-az-felső (OTT) digitális tartalomvédelmi (DRM) felügyeleti alrendszere vagy online streamelési megoldások egy összetett feladat. Operátorok/online videó szolgáltatók általában erőforrás kihelyezése speciális DRM-szolgáltatók ezt a feladatot. Ez a dokumentum célja van a referencia-Tervező és egy végpontok közötti DRM alrendszer OTT vagy online adatfolyam megoldás megvalósítását.

A dokumentum a célzott olvasók DRM alrendszereket OTT vagy online streaming/grafikus; megoldások vagy olvasók, akik DRM alrendszereket dolgozó szakemberek számára. A feltételezi, hogy olvasók jártas a DRM technológiák, például a PlayReady, Widevine, FairPlay vagy Adobe hozzáférés a piacon közül legalább egy.

A DRM ismertető multi-DRM-közös titkosítás (CENC) is szerepel. A fő trendet online streamelésre és az OTT iparági CENC használata a ügyfélplatformokon különböző több natív DRM. Erre az irányra a shift az előző egyetlen DRM és az ügyfél-SDK-ügyfél különböző platformokon használt. Ha több natív DRM CENC használ, mind a PlayReady, mind a Widevine titkosítása a [Common Encryption (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) megadását.

A következők, hogy az a multi-DRM CENC előnyei:

* Csökkenti a titkosítási költség, mert egy egyetlen titkosítási folyamat használatos, amelyekre a natív DRMs a különböző platformokon.
* Csökkenti a költségeket a titkosított eszközök kezelésére, mert csak egy példányát a titkosított eszközökre van szükség.
* Megszünteti a licencelési költségek, mivel DRM natív ügyfél általában a natív platformon szabad DRM-ügyfél.

Microsoft egy aktív hatnak DASH és CENC néhány fő iparági lejátszó együtt. Az Azure Media Services DASH és CENC támogatást nyújt. Friss közlemények tekintse meg a következő angol nyelvű blogokban:

*  [Announcing Google Widevine license delivery services in Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/) (A Google Widevine-licenctovábbítási szolgáltatás megjelenése az Azure Media Services-ben)
* [Az Azure Media Services hozzáadja a Google Widevine-csomagolás multi-DRM adatfolyam kézbesítéséhez](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="overview-of-this-article"></a>Ez a cikk áttekintése
Ez a cikk céljai, hogy:

* Adjon meg egy referencia-Tervező multi-DRM CENC használó DRM alrendszer.
* Hivatkozás implementálásához egy Azure/Media Services-platformon.
* Egyes tervezési és megvalósítási témakörei ismertetik.

A cikkben szereplő "multi-DRM" kifejezés tartalmazza a következő termékek:

* Microsoft PlayReady
* Google Widevine
* Apple FairPlay 

A következő táblázat összefoglalja a natív platform/natív alkalmazás és minden DRM által támogatott böngészők.

| **Ügyfélplatform** | **Natív DRM-támogatás** | **Böngészőalkalmazás /** | **Adatfolyam-továbbítási formátumokba** |
| --- | --- | --- | --- |
| **Intelligens televízió, operátor vezérléséhez, OTT vezérléséhez** |PlayReady, és/vagy Widevine, és/vagy más |Linux, az Opera, WebKit, más |Különböző formátumokba |
| **Windows 10 rendszerű eszközökhöz (Windows-Számítógépet, a Windows rendszerű táblagépek, Windows Phone, Xbox)** |PlayReady |Peremhálózati/IE11/EME MS<br/><br/><br/>Az univerzális Windows Platform |KÖTŐJEL (HLS, PlayReady nem támogatott)<br/><br/>DASH, Smooth Streaming (HLS, PlayReady nem támogatott) |
| **Android-eszközök (telefon, táblagép, TV)** |Widevine |Chrome/EME |DASH, HLS |
| **(iPhone-, iPad-) iOS, OS X-ügyfelek és az Apple TV** |FairPlay |Safari 8 / EME |HLS |


Figyelembe véve a központi telepítés minden egyes DRM aktuális állapotát a szolgáltatás általában szeretne megvalósítani két vagy három DRMs meg végpontok összes típusú cím a legmegfelelőbb módon biztosításához.

Nincs a szolgáltatás logika összetettsége és ügyféloldali felhasználói felület a különböző ügyfelek bizonyos szintű eléréséhez összetettségét közötti kompromisszumot.

Ahhoz, hogy a kijelölés, vegye figyelembe:

* PlayReady natív módon van megvalósítva a minden Windows-eszközét, az egyes Android-eszközök és szoftverek SDK-k szinte bármilyen platformon keresztül elérhető.
* Widevine natív módon van megvalósítva, minden Android-eszköz, a Chrome-ban, és néhány más eszközök.
* FairPlay csak iOS és Mac OS ügyfelek vagy iTunes érhető el.

A tipikus multi-DRM két lehetőség áll rendelkezésre:

* PlayReady és Widevine
* FairPlay, PlayReady és Widevine

## <a name="a-reference-design"></a>A referencia-Tervező
Ez a szakasz referencia kialakítás megvalósítása használt technológiák független mutatja be.

A DRM alrendszer a következő összetevőket tartalmazza:

* Kulcskezelés
* DRM-csomag
* DRM-licenckézbesítés
* Jogosultság ellenőrzése
* Hitelesítési/engedélyezési
* Lejátszó
* Forrás/content delivery network (CDN)

A következő ábra szemlélteti a magas szintű interakció az DRM alrendszer összetevői között:

![CENC DRM alrendszer](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

A Tervező három alapvető rétegek rendelkezik:

* Egy office-biztonsági réteg (fekete) kívülről nem lesz közzétéve.
* Egy Szegélyhálózaton réteg (sötét kék) összes végpont kapcsán szembesülhetnek a nyilvános tartalmaz.
* A nyilvános internet réteg (könnyű kék) tartalmazza a CDN és a játékosok forgalommal a nyilvános interneten keresztül.

Is lehetnek ellenőrzés DRM-védelem, függetlenül attól, hogy statikus vagy dinamikus titkosítás tartalomkezelési eszközt. A bemenetek DRM-titkosítás a következők:

* MBR-videót
* Tartalomkulcs
* Licenc-licenckérési URL-címek

A lejátszás idő alatt ez a magas szintű folyamata:

* A felhasználó hitelesítése.
* A felhasználó egy engedélyezési jogkivonatot jön létre.
* A Windows Media player letölti DRM védett tartalmat (jegyzékfájl).
* A Windows Media player kérést licenc beszerzési küld licenckiszolgálókat együtt egy kulcs Azonosítót és egy engedélyezési jogkivonatot.

Az alábbi szakasz ismerteti a kulcskezelési tervét.

| **ContentKey-eszköz** | **A forgatókönyv** |
| --- | --- |
| 1-1 |A legegyszerűbb eset. A Legfinomabb felügyeletét biztosítja azt. De ezzel az elrendezéssel általában a legmagasabb licenc kézbesítési költség eredményez. Legalább egy licenc kérelme, mert minden egyes védett eszköz szükséges. |
| 1-a-többhöz |Több eszköz használhatja ugyanazt a tartalom kulcsot. Például a logikai csoport összes az eszközök például a genre vagy egy genre (vagy movie gén), a részhalmazát használhatja egy tartalomkulcsot. |
| Több-a-1 |Több tartalomkulcs minden eszköz szükséges. <br/><br/>Például ha szeretné alkalmazni a dinamikus CENC-védelem és multi-DRM a MPEG-DASH vagy HLS dinamikus AES-128 titkosítási, kell két külön tartalomkulcs. Minden tartalom kulcsot kell a saját ContentKeyType. (A tartalomkulcsot a dinamikus CENC védelemhez használt ContentKeyType.CommonEncryption használja. A tartalomkulcsot használt dinamikus AES-128 titkosítást használják ContentKeyType.EnvelopeEncryption.)<br/><br/>Másik példaként DASH tartalom elméletileg CENC védelmi segítségével egy tartalomkulcsot a video-adatfolyamot és egy másik tartalomkulcsot a hangadatfolyam védelméhez védelme. |
| Több-a-többhöz |Az előző két forgatókönyv kombinációja. Egy tartalom kulcsok használatos minden eszköz ugyanazon csoportba tartozó több eszközhöz. |

Egy másik fontos szempont az, hogy az állandó, és nem állandó licencek használatát.

Miért fontosak ezeket a szempontokat?

Használatakor a nyilvános felhők licenc kézbesítésre állandó, és nem állandó licencek közvetlen hatást gyakorolnak licenc kézbesítési költség. A következő két különböző kialakítási esetekben kiszolgálására mutatja be:

* Havi előfizetést: állandó licenc és 1-a-többhöz tartalom kulcs eszköz leképezés. Például a gyermekek filmek, a használjuk egy egy titkosítási tartalomkulcsot. Ebben az esetben:

    Minden gyermekek filmek vagy eszköz kért licencek teljes száma = 1

* Havi előfizetést: a nem állandó licenc és 1-1 leképezési tartalomkulcsot és eszköz közötti. Ebben az esetben:

    Minden gyermekek filmek vagy eszköz kért licencek teljes száma = [figyelt filmek száma] x [munkamenetek száma]

A két különböző kialakításokról nagyon különböző licenc kérelem minták eredményez. A különböző minták költségeket, ha a nyilvános felhők, például a Media Services licenctovábbítási szolgáltatása biztosítja különböző licenc kézbesítési eredményez.

## <a name="map-design-to-technology-for-implementation"></a>Tervezési leképezése végrehajtásához technológia
A következő általános tervezési van leképezve az Azure/Media Services platformon technológiák használata minden egyes építőelem technológiákat megadásával.

A következő táblázat a leképezés.

| **Építőelem** | **Technológia** |
| --- | --- |
| **Player** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **Az identitásszolgáltató (IDP)** |Azure Active Directory (Azure AD) |
| **Biztonságijogkivonat-szolgáltatás (STS)** |Azure AD |
| **DRM protection munkafolyamata** |A Media Services dinamikus védelme |
| **DRM-licenckézbesítés** |* A Media Services licenc kézbesítési (PlayReady, Widevine, FairPlay) <br/>* Axinom licenckiszolgáló <br/>* Egyéni PlayReady licenckiszolgáló |
| **Forrás** |A Media Services streamvégpont |
| **Kulcskezelés** |A hivatkozás végrehajtása esetén nem szükséges |
| **Tartalomkezelés** |A C# Konzolalkalmazás |

Ez azt jelenti IDP és STS is használják az Azure ad-val. A [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/) a Windows Media player szolgál. A Media Services és Media Player támogatják a KÖTŐJELET és CENC multi-DRM.

A következő ábrán az látható, a teljes struktúra és technológia előző hozzárendelési folyamat:

![A Media Services CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Dinamikus CENC titkosítási beállításához a Tartalomkezelés eszköz használja az alábbi adatokat:

* Nyissa meg a tartalom
* A következő kulcsból: tartalomkulcs létrehozása/felügyeleti
* Licenc-licenckérési URL-címek
* Azure ad-információk listája

A Tartalomkezelés eszköz kimenete a következő:

* Hogyan licenc kézbesítési ellenőrzi egy JSON webes jogkivonat (JWT) megadását és DRM licenc specifikációk ContentKeyAuthorizationPolicy tartalmazza.
* AssetDeliveryPolicy a streaming formátumban, DRM-védelem és licenc-licenckérési URL-címek meghatározásokat tartalmaznak.

Futtatás során ez a folyamat:

* Felhasználók hitelesítése után a jwt-t jön létre.
* A JWT lévő jogcímek egyik olyan csoportok jogcímet, a csoportházirend-objektum azonosítója EntitledUserGroup tartalmazza. A jogcím használatával adja át a jogosultság ellenőrzése.
* A Windows Media player letölti az ügyfél jegyzék CENC által védett tartalom, és azonosítja a következő:
   * Kulcs azonosítója.
   * Védett CENC tartalma.
   * Licenc-licenckérési URL-címeket.
* A Windows Media player támogatott böngésző/DRM alapján licenc beszerzési kérelmet küld. A kérésben licenc beszerzési, a kulcs azonosítója és a jwt-t is elküldi. A kézbesítési szolgáltatás ellenőrzi a jwt-t és a jogcímeket, előtt a szükséges licenccel kapcsolatos tartalmazott.

## <a name="implementation"></a>Megvalósítás
### <a name="implementation-procedures"></a>Eljárások végrehajtása
Végrehajtása a következő lépésekből áll:

1. Készítse elő a vizsgálati eszközök. Egy teszt videót, amely többszörös sávszélességű töredékes MP4 a Media Services kódolási/csomag. Ez az eszköz *nem* DRM-védelemmel. DRM-védelem később dinamikus védelmi végezhető el.

2. Hozzon létre egy kulcs azonosítója és (opcionálisan az egyik kulcs kezdőérték) egy tartalomkulcsot. Ebben az esetben a kulcskezelő rendszer nincs szükség, mert csak egy kulcs Azonosítóját és a tartalomkulcs is vizsgálati eszközök néhány szükséges.

3. A Media Services API segítségével konfigurálhatja a multi-DRM-licenctovábbítási szolgáltatások a vizsgálati eszköz. Ha a vállalat vagy a vállalat forgalmazók a Media Services szolgáltatások helyett egyéni licenc kiszolgálók, kihagyhatja ezt a lépést. Licenc-licenckérési URL-címeket adhat meg a lépés licenc kézbesítési konfigurálásakor. A Media Services API néhány részletes konfigurációk, például engedélyezési házirend-korlátozás és a licenc válasz sablonok különböző DRM licenc szolgáltatásokhoz van szükség. Ilyenkor az Azure-portálon ehhez a konfigurációhoz nem biztosít a szükséges felhasználói felületén. API-szintű információkat és mintakódját, [használata PlayReady és/vagy Widevine a dynamic common encryption](media-services-protect-with-playready-widevine.md).

4. A Media Services API segítségével konfigurálja az adategység továbbítási házirendjét a vizsgálati eszköz. API-szintű információkat és mintakódját, [használata PlayReady és/vagy Widevine a dynamic common encryption](media-services-protect-with-playready-widevine.md).

5. Hozzon létre, és az Azure AD-bérlő konfigurálása az Azure-ban.

6. Néhány felhasználói fiókok és csoportok létrehozása az Azure AD-bérlőben. Hozzon létre legalább egy "Jogosult felhasználó" csoportot, és a felhasználó hozzáadása ehhez a csoporthoz. A csoportban található felhasználók licenc a beszerzési adja át a jogosultság ellenőrzése. A csoportba nem tartozó felhasználók felelt meg a hitelesítési ellenőrzés sikertelen, és nem lehet engedélyt. A "Jogosult felhasználó" csoporttagság egy megfelelő a jwt-t az Azure AD által kiállított jogcímet. Ez a követelmény jogcím megadnia a lépésben multi-DRM-licenctovábbítási szolgáltatások konfigurálásakor.

7. A videólejátszó üzemeltetésére ASP.NET MVC alkalmazás létrehozása. Az ASP.NET-alkalmazás az Azure AD-bérlő felhasználóhitelesítés védi. Megfelelő jogcímek a felhasználó hitelesítése után kapott hozzáférési jogkivonatok szerepelnek. Ez a lépés ajánlott OpenID Connect API. A következő NuGet-csomagok telepítése:

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. Hozzon létre egy player a [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). Használja a [Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) adhatja meg, melyik DRM technológiát DRM különböző platformokon használatára.

9. Az alábbi táblázat a teszt mátrix.

    | **DRM** | **Böngésző** | **Jogosult felhasználó eredménye** | **Unentitled felhasználói eredménye** |
    | --- | --- | --- | --- |
    | **PlayReady** |A Microsoft Edge vagy az Internet Explorer 11 Windows 10 rendszeren |Sikeres |Sikertelen |
    | **Widevine** |A Windows 10 Chrome |Sikeres |Sikertelen |
    | **FairPlay** |TBD | | |

Állítsa be az ASP.NET MVC player alkalmazásoknak az Azure AD információkért lásd: [az Azure Media Services OWIN MVC-alapú alkalmazások integrálása az Azure Active Directoryban, és korlátozhatja a JWT jogcímei alapján kulcs tartalomkézbesítési](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

További információkért lásd: [JWT jogkivonat hitelesítés az Azure Media Services és a dinamikus titkosítás](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).  

Az Azure AD tájékoztatást:

* A fejlesztői információt a [Azure Active Directory fejlesztői útmutatója](../active-directory/active-directory-developers-guide.md).
* A rendszergazda információt [az Azure AD bérlő címtár felügyelete](../active-directory/active-directory-administer.md).

### <a name="some-issues-in-implementation"></a>Bizonyos problémák végrehajtása
Használja a következő hibaelhárítási információk megvalósítási problémái segítségért.

* A kibocsátó URL-cím végén szerepelnie kell "/". A célközönség kell lennie a player alkalmazás ügyfél-azonosítót. Továbbá adja hozzá "/" a kibocsátó URL-cím végén.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    Az a [JWT dekóder](http://jwt.calebb.net/), látni **és** és **iss**, ahogy az a JWT:

    ![JWT-T](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Az engedélyeket az alkalmazásnak az Azure AD hozzá a **konfigurálása** az alkalmazás lapján. Engedélyek is szükségesek az alkalmazások a helyi és a telepített verzió.

    ![Engedélyek](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

* Használja a megfelelő kibocsátó dinamikus CENC védelem beállítása során.

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    A következő nem működik:

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    A GUID az az Azure AD bérlő azonosítója. A GUID-azonosító található a **végpontok** az Azure portálon előugró menüjét.

* Támogatás csoporttagság jogosultságokat jogcímek. Győződjön meg arról, hogy a következő Azure AD alkalmazás-jegyzékfájl: 

    "groupMembershipClaims": "All" (alapértelmezett értéke null)

* Állítsa be a megfelelő TokenType, korlátozás követelmények létrehozásakor.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Támogatja a jwt-t (az Azure AD) mellett SWT (ACS) adja hozzá, mert az TokenType alapérték TokenType.JWT. Ha SWT/ACS használja, a jogkivonat TokenType.SWT kell beállítania.

## <a name="additional-topics-for-implementation"></a>Kiegészítő témakörök az végrehajtása
Ez a szakasz néhány további tervezési és megvalósítási témaköre ismerteti.

### <a name="http-or-https"></a>HTTP vagy HTTPS?
Az ASP.NET MVC player alkalmazásnak támogatnia kell a következő:

* Felhasználók hitelesítése az Azure AD, amely a HTTPS használatával.
* Az ügyfél és az Azure AD, amely a HTTPS közötti információcsere jwt-t.
* Az ügyfél, amely a HTTPS PROTOKOLLT kell lennie, ha a Media Services által biztosított licenc kézbesítési DRM a licenc beszerzése. A PlayReady termékcsomag nem határozza meg, hogy HTTPS licenc kézbesítésre. Ha a PlayReady licenckiszolgáló kívül a Media Services, HTTP vagy HTTPS is használhatja.

Az ASP.NET lejátszóalkalmazás HTTPS PROTOKOLLT használ, célszerű így Media Player a HTTPS oldalon. Azonban HTTP részesíti előnyben a streaming, ezért figyelembe kell vennie a problémát a vegyes tartalom.

* A böngésző nem engedélyezi a vegyes tartalomról. De beépülő modulok például a Silverlight beépülő modult OSMF sima és kötőjel engedélyezhető. Vegyes tartalom kapcsolatos egyik biztonsági szempont a rosszindulatú JavaScript, ami okozhat ügyféladatok van kitéve, új funkció lehetővé teszi a veszélye miatt. Böngészők blokkolják a alapértelmezés szerint ez a funkció. A csak kerülő, a kiszolgáló (forrás) oldalán (függetlenül attól, HTTPS vagy HTTP) minden tartományban engedélyezi. Ez az valószínűleg nem jó ötlet vagy.
* Kerülje a vegyes tartalomról. A lejátszóalkalmazás és a Media Player HTTP vagy HTTPS PROTOKOLLT kell használnia. Vegyes tartalom lejátszását, amikor a silverlightSS technológia szükséges, vegyes tartalmú figyelmeztetés törlése. A flashSS technikai vegyes tartalom vegyes tartalmú figyelmeztetés nélkül kezeli.
* A streamvégpont augusztus 2014 előtt jöttek létre, ha az nem támogatja a HTTPS. Ebben az esetben, hozzon létre és egy új streamvégpont HTTPS-hez.

A DRM-védelemmel ellátott tartalmak, az alkalmazás és a HTTPS a folyamatos átviteli vannak hivatkozás végrehajtása. Tartalmának megnyitása a Windows Media player nem kell hitelesítést vagy licencet, így is használhatja a HTTP vagy HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Az Azure Active Directory aláírási kulcs váltása
Aláírási kulcs váltása esetében figyelembe kell venni a megvalósításában. Figyelmen kívül hagyja azt, ha befejezte a rendszer végül nem működik teljes mértékben, hat héten belül legfeljebb.

Az Azure AD az ipari szabványok használatával és alkalmazásokat, amelyek használják az Azure AD között megbízhatósági kapcsolat létrehozása. Pontosabban az Azure AD nyilvános és titkos kulcsból álló kulcspárt tartalmazó aláíró kulcsot használ. Az Azure AD egy biztonsági jogkivonatot, amely leírja a felhasználó hoz létre, amikor aláírja azt az Azure AD és a titkos kulcs vissza az alkalmazáshoz való továbbítás előtt. Győződjön meg arról, hogy a jogkivonat érvényes, és az Azure AD kezdeményezésű, az alkalmazás ellenőrizni kell a jogkivonat aláírása. Az alkalmazás a bérlő összevonási metaadat-dokumentum található, az Azure AD által elérhetővé tett nyilvános kulcsot használja. A nyilvános kulcsot, és az aláírási kulcs, amelyből származik, megegyezik egy a egyetlen bérlő számára az Azure ad-ben.

Az Azure AD kulcsváltás további információkért lásd: [fontos információkat a kulcsváltás az Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Között a [nyilvános-titkos kulcsból álló kulcspárt](https://login.microsoftonline.com/common/discovery/keys/):

* A titkos kulcsot az Azure AD jwt-t használt.
* A nyilvános kulcs az egyes alkalmazások, például a DRM-licenctovábbítási szolgáltatások a Media Services ellenőrzésére szolgál a jwt-t.

Biztonsági okokból az Azure AD a tanúsítvány rendszeres időközönként (hat hetente) forog. Biztonsági behatolás esetén a kulcsváltás akkor fordulhat elő, amikor. Ezért a Media Services licenctovábbítási szolgáltatások frissítenie kell a nyilvános kulcsot az Azure AD a kulcspár forog használatos. Ellenkező esetben a Media Services token hitelesítés nem sikerül, és a licenc nem jelenik meg.

Állítsa be ezt a szolgáltatást, akkor állítsa TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument DRM-licenctovábbítási szolgáltatások konfigurálásakor.

A JWT folyamata a következő:

* Az Azure AD kibocsát a jwt-t a hitelesített felhasználók a jelenlegi titkos kulccsal.
* Ha egy player egy CENC tartalmú multi-DRM-védelemmel látja, először megkeresi a jwt-t az Azure AD által kibocsátott.
* A Windows Media player a jwt-t a licenc beszerzési kérést küld a Media Services licenctovábbítási szolgáltatások.
* A Media Services licenctovábbítási szolgáltatások a jelenlegi érvényes nyilvános kulcsot az Azure AD segítségével ellenőrizze a JWT licencek kiadása előtt.

DRM-licenctovábbítási szolgáltatások mindig ellenőrizze, hogy a jelenlegi érvényes nyilvános kulcsot az Azure AD. A nyilvános kulcsot az Azure AD által bemutatott a kulcs ellenőrzése az Azure AD által kibocsátott jwt-t használja.

Mi történik, ha a kulcs átfordulási történik, az Azure AD jwt-t állít elő, de a JWT játékosok által küldött DRM-licenctovábbítási szolgáltatások a Media Services ellenőrzés előtt?

A kulcs is bármikor kell frissítése, mert egynél több érvényes nyilvános kulccsal mindig rendelkezésre áll az összevonási metaadatok dokumentumban. A Media Services licenc kézbesítési a dokumentumban megadott kulcsok bármelyikét használhatja. Egy kulcs előfordulhat, hogy hamarosan állítva, mert egy másik előfordulhat, hogy azok helyettesítéseivel, és így tovább.

### <a name="where-is-the-access-token"></a>Hol található a hozzáférési jogkivonat?
Ha tekinti meg hogyan webalkalmazás hívja az API-alkalmazás [OAuth 2.0 ügyfél hitelesítő adatok megadása az Alkalmazásidentitás](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api), a hitelesítési folyamat a következőképpen történik:

* Egy felhasználó bejelentkezik az Azure AD-t a webalkalmazás. További információkért lásd: [webböngészőt a webes alkalmazás](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application).
* Az Azure AD-engedélyezési végpont átirányítja a felhasználói ügynök az ügyfélalkalmazás engedélyezési kóddal. A felhasználói ügynök engedélyezési kódot az ügyfél-alkalmazás átirányítási URI-t adja vissza.
* A webalkalmazásnak kell szerezni a hozzáférési tokent, hogy a webes API-t a hitelesítéshez és a kívánt erőforrás lekérése. Egy kérést küld az Azure AD-jogkivonat végpontjához, és a hitelesítő adatokat, ügyfél-Azonosítót és webes API-alkalmazás URI azonosítója. Azt mutatja be, hogy a felhasználó átadni kívánt hozzájárult e igazolásához engedélyezési kódot.
* Az Azure AD hitelesíti az alkalmazást, és adja vissza a JWT jogkivonat, amellyel a webes API hívása.
* A HTTPS PROTOKOLLOKON keresztül a webalkalmazás az visszaadott JWT jogkivonat használja a "Tulajdonos" megjelölés JWT karakterlánc a "Engedélyezés" a kérelem fejlécében hozzáadása a webes API-t. A webes API-k majd érvényesíti a jwt-t. Ha az érvényesítés sikeres, a kívánt erőforrás adja vissza.

Az alkalmazás identitását folyamatot a webes API-k megbízhatónak tekinti, hogy a webes alkalmazás hitelesítette a felhasználót. Ezért ez a minta egy megbízható alrendszer neve. A [engedélyezési folyamatábrája](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) ismerteti, hogyan-kód-hitelesítésengedélyezési folyamata működik.

Licenc beszerzési token korlátozás azonos megbízható alrendszer mintát követi. A Media Services licenctovábbítási szolgáltatása a webes API-erőforrás vagy a "háttér-erőforrás", amely egy webalkalmazás hozzáférésre van szüksége. Ezért hol található a hozzáférési jogkivonat?

A hozzáférési tokent az Azure AD származik. A felhasználó sikeres hitelesítés után egy engedélyezési kódot ad vissza. Az engedélyezési kód majd szolgál, az ügyfél-azonosító és az alkalmazás-kulcsban, exchange-a hozzáférési jogkivonat. A hozzáférési jogkivonat mutat, vagy a Media Services licenctovábbítási szolgáltatása jelöli egy "mutató" alkalmazás eléréséhez használt.

Regisztrálja és mutató alkalmazás beállítása az Azure AD, a következő lépéseket:

1. Az Azure AD-bérlő:

   * A bejelentkezési URL-cím https://[resource_name].azurewebsites.net/ az alkalmazás (erőforrás) hozzáadása. 
   * Adja hozzá az URL-cím https://[aad_tenant_name].onmicrosoft.com/[resource_name egy alkalmazás azonosítója].

2. Adjon hozzá egy új kulcsot az erőforrás alkalmazás.

3. Az alkalmazás-jegyzékfájl frissítése az, hogy groupMembershipClaims tulajdonság értéke "groupMembershipClaims": "All".

4. A mutató player webalkalmazás a szakaszban az Azure AD alkalmazás **egyéb alkalmazások engedélyei**, adja hozzá az erőforrás-alkalmazást, amely az 1. lépésben lett hozzáadva. A **jogosultságot**, jelölje be **hozzáférés [resource_name]**. Ez a beállítás biztosítja a web app engedélye az erőforrás alkalmazás elérő hozzáférési jogkivonatok létrehozásához. Erre a webes alkalmazás helyi és a telepített verziójának, ha a Visual Studio és az Azure web app fejleszt.

A jwt-t az Azure AD által kibocsátott az egérmutatót erőforrás eléréséhez használt jogkivonat.

### <a name="what-about-live-streaming"></a>Mi a helyzet live streaming?
Az előző vitafórum igény szerinti eszközök összpontosít. Mi a helyzet live streaming?

Pontosan azonos megtervezését és megvalósítását segítségével védelmére élő Stream továbbítása a Media Services való kezelésével, amelyek eszközként program társított adategységet.

Pontosabban közvetít élő adatfolyamként, a Media Services, kell csatornát létrehozni, és hozzon létre egy programot a csatorna számára. A program létrehozásához létrehozásához szükséges egy eszköz, amely tartalmazza az élő archívum a program. Az élő tartalom multi-DRM-védelemmel rendelkező CENC megadásához alkalmazni azonos telepítő/feldolgozása az eszköz, mintha egy VOD eszköz a program indítása előtt.

### <a name="what-about-license-servers-outside-media-services"></a>Mi a helyzet a Media Services kívül licenckiszolgálókat?
Gyakran az ügyfelek fektetett licenc kiszolgálófarm vagy a saját adatközpont vagy DRM-szolgáltatók által üzemeltetett egy. A Media Services Content Protection hibrid módban is működik. Tartalom birtokolt, és dinamikusan védett a Media Services szolgáltatásban, amíg DRM-licencek kívül a Media Services-kiszolgálók által érkeznek. Ebben az esetben fontolja meg a következő módosításokat:

* STS kell, hogy elfogadható, majd ellenőrizheti a licenc kiszolgálófarm jogkivonatokat kibocsátani. Például a Widevine licenc Axinom által biztosított kiszolgálóknál adott jwt-t, amely tartalmaz egy jogosultság-üzenet. Ezért figyelembe kell rendelkeznie az STS szolgáltatással ki ilyen jwt-t. Megvalósítása az ilyen típusú információkért látogasson el a [Azure dokumentációs központban](https://azure.microsoft.com/documentation/) és [Widevine-licencek kézbesíthet Azure Media Services használata Axinom](media-services-axinom-integration.md).
* Már nincs szüksége a Media Services licenctovábbítási szolgáltatása (ContentKeyAuthorizationPolicy) konfigurálása. Meg kell adnia a licenc licenckérési URL-címek (a PlayReady, Widevine és FairPlay) AssetDeliveryPolicy állítsa be a CENC multi-DRM való konfigurálásakor.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Mi történik, ha használni kívánt egyéni STS?
Az ügyfél kiválaszthat egy egyéni STS JWTs biztosításához. Okai:

* A kiállító terjesztési hely, az ügyfél által használt STS nem támogatja. Ebben az esetben egy egyéni STS lehet egy lehetőséget.
* Az ügyfél rugalmas vagy szorosabb vezérlő STS integrálása az ügyfél előfizető számlázórendszerrel módosítania kell. MVPD operátor kínálhat például több OTT előfizető csomagok, például a prémium alapvető, és sport. Az operátor szeretnénk felel meg a jogcímek, az előfizető csomag jogkivonatot, hogy egy adott csomag csak a tartalom elérhetővé válnak. Ebben az esetben egy egyéni STS biztosítja a szükséges rugalmasságot és.

Egy egyéni STS használata esetén két módosításokat kell végrehajtani:

* Az eszközhöz tartozó licenctovábbítási szolgáltatása konfigurálásakor meg kell adnia a biztonsági kulcsot használja az Azure AD az aktuális kulcs helyett egyéni STS-ellenőrzése. (További részletekért kövesse.) 
* Amikor JTW jogkivonat történik, a biztonsági kulcs helyett az aktuális X509 titkos kulcsának van-e adva az Azure AD-tanúsítványt.

A biztonsági kulcsok két típusa van:

* Szimmetrikus kulcs: A ugyanazzal a kulccsal létrehozásához, és ellenőrizze a jwt-t.
* Aszimmetrikus kulcs: egy nyilvános-titkos kulcsból álló kulcspárt egy X509 tanúsítványt használni fogják és a titkos kulcs létrehozásához titkosítása/jwt-t és a nyilvános kulcsot, jogkivonat megerősítéséhez.

> [!NOTE]
> Ha a .NET-keretrendszer használata / C#, a fejlesztő platform, a X509 az aszimmetrikus kulcs használt tanúsítványnak rendelkeznie kell legalább 2048 bites kulcshosszt. Ez az osztály a .NET-keretrendszer System.IdentityModel.Tokens.X509AsymmetricSecurityKey követelmény. Ellenkező esetben a következő kivétel történt:

> IDX10630: "System.IdentityModel.Tokens.X509AsymmetricSecurityKey" aláírásra nem lehet kisebb, mint '2048' bit.

## <a name="the-completed-system-and-test"></a>A befejezett rendszer és a teszt
Ez a szakasz végigvezeti az alábbi esetekben a befejezett végpont rendszerben, hogy viselkedését alapvető képe előtt, hozzon létre egy bejelentkezési fiókot lehet:

* Ha egy nem integrált forgatókönyv lesz szüksége:

    * A Media Services, amelyek üzemeltetett videó eszközök vagy védelem nélkül, vagy DRM-védelemmel, de (licenc kiadása a személy, aki kérte, hogy) token hitelesítés nélkül tesztelheti, bejelentkezés nélkül. HTTP vált, ha a videoadatfolyam-továbbítás HTTP Protokollon keresztül.

* Ha egy végpontok közötti integrált forgatókönyv lesz szüksége:

    * A jogkivonat a hitelesítés és az Azure AD által generált JWT a Media Services dinamikus DRM védelem alatt videó eszközök, be kell jelentkeznie.

A player webalkalmazás és a bejelentkezés: [ezen a webhelyen](https://openidconnectweb.azurewebsites.net/).

### <a name="user-sign-in"></a>Felhasználói bejelentkezés
A végpontok közötti integrált DRM rendszer teszteléséhez szüksége létrehozni vagy hozzáadni egy fiókot.

Milyen fiókot?

Bár az Azure eredetileg csak a Microsoft-fiókos felhasználó által hozzáférése engedélyezett, most elérését felhasználók mindkét rendszerekből. Az összes Azure-tulajdonság most megbízhatónak Azure AD hitelesítésében, és az Azure AD akkor hitelesíti a szervezeti felhasználók. Összevonási kapcsolat létrejött, ahol az Azure AD megbízik a Microsoft fiók végfelhasználói identitásrendszere identitásrendszerében a felhasználók hitelesítésére. Ennek eredményeképpen az Azure AD hitelesítheti Vendég Microsoft a fiókok, valamint a natív Azure AD-fiókok.

Azure AD megbízik a Microsoft-fiók tartományát, mert a következő tartományokban lévő bármely fiókok a egyéni Azure AD bérlői és a fiók használatával jelentkezzen be is hozzáadhat:

| **Tartománynév** | **Tartomány** |
| --- | --- |
| **Egyéni az Azure AD bérlő tartománya** |somename.onmicrosoft.com |
| **Vállalati tartományhoz** |Microsoft.com |
| **Microsoft-fiók tartománya** |Outlook.com-os, live.com, hotmail.com |

A szerzők egy fiókot a létrehozott új és az Ön is kapcsolódhat.

Az alábbi képek a különböző bejelentkezési oldalai különböző tartományi fiókok által használt megjelenítése:

**Egyéni az Azure AD tartományi fiók bérlői**: A testreszabott bejelentkezési oldal az egyéni Azure ad bérlői tartományhoz.

![Egyéni az Azure AD bérlő tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Microsoft tartományi fiók intelligens kártyával**: A bejelentkezési oldal vállalati Microsoft szerint testre szabott informatikai kéttényezős hitelesítéssel.

![Egyéni az Azure AD bérlő tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-fiók**: A fogyasztók Microsoft-fiókja bejelentkezési oldalára.

![Egyéni az Azure AD bérlő tartományi fiók](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>A PlayReady titkosított Media bővítmények használatát
A modern böngészőt titkosított Media Extensions (EME) rétegen a PlayReady-támogatást, például az Internet Explorer 11 Windows 8.1 vagy újabb rendszeren és Windows 10, a Microsoft Edge böngésző PlayReady EME az alapul szolgáló DRM.

![PlayReady EME használata](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

A sötét player terület az oka, hogy PlayReady védelem megakadályozza, hogy Ön a képernyőfelvétel-készítés védett videó.

Az alábbi képernyőfelvételen látható a player-alapú beépülő modulokat és a Microsoft Security Essentials (MSE) / EME támogatja:

![Player beépülő modulokat a PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Lehetővé teszi, hogy a Microsoft Edge és a Windows 10 rendszeren az Internet Explorer 11 EME [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) Windows 10 rendszerű eszközökön, amelyek támogatják ezt meg. PlayReady SL3000 feloldja a folyamatot a bővített támogatás tartalom (4 KB-os, HDR) és az új tartalom üzemeltetési modell (a bővített tartalom).

Ha csak a Windows-eszközökön, PlayReady a hardver (PlayReady SL3000) Windows-eszközökön elérhető csak DRM. Egy adatfolyam-továbbítási szolgáltatás használhatja a PlayReady vagy egy univerzális Windows Platform alkalmazás keresztül EME és PlayReady SL3000, mint egy másik DRM segítségével egy magasabb videominőséget lehetőséget. Általában a tartalom legfeljebb 2 KB-os áthaladó Chrome vagy a Firefox, és a tartalom mentése 4 KB-os viszonylatában Microsoft Edge/Internet Explorer 11 vagy egy univerzális Windows Platform-alkalmazás az adott eszközön keresztül. A függ szolgáltatás beállításait és a végrehajtása.

#### <a name="use-eme-for-widevine"></a>Widevine EME használata
Olyan modern böngészőt EME vagy Widevine-támogatással rendelkező, például a Chrome 41 + a Windows 10, Windows 8.1-, Mac OS x Yosemite és Chrome Android 4.4.4, a Google Widevine a DRM EME mögött.

![Widevine EME használata](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine nem akadályozza meg a képernyőfelvétel-készítés védett videó elvégzése.

![Player beépülő modulokat Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Unentitled felhasználók
Ha a felhasználó nem a "Jogosult felhasználók" csoport tagja, a felhasználó fennakadt a jogosultság ellenőrzése. A multi-DRM-szolgáltatás megtagadja majd a kért licenc kiadását, látható módon. A részletes leírás: "licenc megszerzése sikertelen volt," amelyhez megfelelően van.

![Unentitled felhasználók](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Egyéni biztonságijogkivonat-szolgáltatás futtatásához
Ha egy egyéni STS futtatja, a jwt-t adja ki az egyéni STS egy szimmetrikus vagy aszimmetrikus kulcsok használatával.

Az alábbi képernyőfelvételen látható olyan forgatókönyvekben, amelyek (Chrome használatával történő) szimmetrikus kulcsot használ:

![A szimmetrikus kulcs egyéni STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

Az alábbi képernyőfelvételen látható olyan forgatókönyvekben, amelyek egy X509 keresztül aszimmetrikus kulccsal tanúsítvány (Microsoft modern böngésző használatával):

![Egyéni STS aszimmetrikus kulccsal rendelkező](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

Mindkét előző esetben a felhasználó hitelesítési ugyanaz marad. Az Azure AD keresztül vesz igénybe. Az egyetlen különbség, hogy JWTs az egyéni STS helyett az Azure AD által kiállított. Dinamikus CENC védelem konfigurálásakor a licenc-kézbesítési szolgáltatás korlátozás határozza meg a jwt-t, a szimmetrikus vagy aszimmetrikus kulcsok.

## <a name="summary"></a>Összegzés
Ez a dokumentum több natív DRM- és hozzáférés-vezérléssel tokent használó hitelesítés, a tervezési és a végrehajtás CENC tárgyalt Azure, a Media Services és a Media Player segítségével.

* Hivatkozás kialakítást, amely tartalmazza a szükséges összetevőkkel a DRM/CENC alrendszerben adtak meg.
* A hivatkozás megvalósításának Azure, a Media Services és a Media Player adtak meg.
* Néhány közvetlenül a tervezési és megvalósítási foglalkozó témakörök volt is ismertet.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 
