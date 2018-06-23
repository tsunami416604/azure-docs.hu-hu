---
title: Kerberos által korlátozott delegálás konfigurációi alkalmazásproxy hibaelhárítása |} Microsoft Docs
description: Kerberos által korlátozott delegálás konfigurációi alkalmazásproxy hibaelhárítása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: f318c53de073c8f1fa6c3ae11cb335a4a91e137d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334961"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Kerberos által korlátozott delegálás konfigurációi alkalmazásproxy hibaelhárítása

Az SSO megvalósításához a közzétett alkalmazásokhoz elérhető módszerek eltérőek lehetnek, egyik alkalmazásból a másikba. Egy Azure Active Directory (Azure AD) alkalmazásproxy által alapértelmezés szerint akkor Kerberos által korlátozott delegálás (KCD). Is konfigurál egy összekötőt, a felhasználók számára, korlátozott Kerberos-hitelesítés háttér-alkalmazások futtatásához.

A Kerberos által korlátozott Delegálás engedélyezéséhez a művelet be nem egyértelmű. Legfeljebb egy általános ismeretekkel rendelkezik a különböző összetevőkkel és hitelesítési folyamat, amely támogatja az egyszeri Bejelentkezést igényel. De a Kerberos által korlátozott Delegálás SSO néha, nem fognak megfelelően működni. Ezek a forgatókönyvek hibaelhárításához információkat van szüksége.

A cikkben, amely segít a hibaelhárításához és önálló néhány a leggyakoribb problémák megoldásáról a hivatkozás egyetlen pont. Összetettebb megvalósítási problémák diagnosztizálása is magában foglalja.

Ez a cikk lehetővé teszi a következő előfeltételek:

-   Egy Azure AD alkalmazásproxy telepítését [az alkalmazásproxy első lépései](manage-apps/application-proxy-enable.md) és általános alkalmazás-hozzáférés nem Kerberos által korlátozott Delegálás a várt módon működik.

-   A közzétett célalkalmazás Internet Information Services (IIS) és a Microsoft általi implementációja Kerberos alapul.

-   A kiszolgáló és az alkalmazás állomások egy egyetlen Azure Active Directory-tartományban található. A tartományok közötti és erdők forgatókönyvek részletes információkért lásd: a [Kerberos által korlátozott Delegálás tanulmány](https://aka.ms/KCDPaper).

-   A figyelt alkalmazást egy Azure-ban van közzétéve bérlői előtti hitelesítés engedélyezve van. Felhasználók-azonosítóknak történő hitelesítésre űrlapalapú hitelesítésen keresztül. Gazdag ügyfél-hitelesítési forgatókönyvei nem fedi le ez a cikk. Előfordulhat, hogy adni őket egy bizonyos ponton a jövőben.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-alkalmazásproxy is telepíthető az számos különböző típusú infrastruktúrák vagy környezetben. A architektúrák eltérőek lehetnek a szervezetben. A Kerberos által korlátozott Delegálás kapcsolatos hibák leggyakoribb oka a környezetben nem. Egyszerű konfigurációs hibák vagy általános hibák miatt a legtöbb probléma.

Emiatt érdemes győződjön meg arról, hogy megfelel az összes előfeltétel [Kerberos által korlátozott Delegálás egyszeri bejelentkezés használatával az alkalmazásproxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) hibaelhárítás megkezdése előtt. Megjegyzés: a Kerberos által korlátozott delegálás konfigurálása 2012R2 című szakaszban. Ez a folyamat a Kerberos által korlátozott Delegálás konfigurálása a Windows előző verzióiban eltérő megközelítést alkalmaz. Is figyelembe véve a felsorolt szempontok:

-   A rendszer nem ritka, hogy az egy tartományi tagkiszolgáló, egy adott tartományvezérlő (DC) a biztonságos csatorna párbeszédpanel megnyitásához. A kiszolgáló előfordulhat, hogy helyezze át egy másik párbeszédpanelt egy adott időpontban. Ezért összekötő állomások nem korlátozódik, és csak bizonyos helyi hely azok a tartományvezérlők közötti kommunikáció.

-   Tartományok közötti forgatókönyvek alapulnak, amely egy összekötő gazdagépet, a tartományvezérlőket, lehet, hogy a helyi hálózati határain kívül közvetlen hivatkozásokat. Ebben az esetben fontos egyaránt meghajtóbetűjeltől forgalmat is küldhet tartományvezérlők, amelyek megfelelnek a megfelelő tartományához. Ha nem, delegálás sikertelen lesz.

-   Ahol lehetséges, helyez be minden aktív IP-cím vagy -Azonosítók eszközök összekötő-állomások és a tartományvezérlők között. Ezek az eszközök néha overintrusive és zavarják a core RPC-forgalmat.

Tesztelje a delegálás egyszerű forgatókönyvekben. A több változót bevezetése, előfordulhat, hogy annál a tartalom kell. Idő megtakarítása érdekében korlátozza, a tesztelés egyetlen összekötőt. Adjon hozzá további összekötők, a probléma megoldása után.

Néhány környezeti tényezők is hozzájárulhat a problémát. Ezek a tényezők elkerülése érdekében minimalizálása érdekében architektúra lehetőség szerint a tesztelés során. Például a helytelenül konfigurált belső tűzfal hozzáférés-vezérlési listák használata gyakori. Ha lehetséges, az összes forgalmat küldeni az összekötő egyenes keresztül a tartományvezérlők és a háttér-alkalmazás.

A legjobb hely összekötők elhelyezése a lehető legközelebb a tárolókra. A tűzfal, amely a beágyazott tesztelése során szükségtelen összetettsége hozzáadja, és a vizsgálatok is meghosszabbítása.

Mi jeleníti meg a Kerberos által korlátozott Delegálás probléma? Több közös arra utaló jelek vannak, amelyek Kerberos által korlátozott Delegálás SSO nem működik. Az első jelentkezik a probléma jelennek meg a böngészőben.

   ![Helytelen a Kerberos által korlátozott konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Hiányzó jogosultságok miatt nem sikerült engedélyezése](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Mindkét ezeket a lemezképeket megjelenítése a azonos jelenség: egyszeri bejelentkezési hiba. Az alkalmazás a felhasználói hozzáférés megtagadva.

## <a name="troubleshooting"></a>Hibaelhárítás

Hogyan háríthatóak attól függ, a problémát és az erőforrásigények tapasztalhatja. Mielőtt továbblépne a távolabb, megismerkedhet a következő cikkekben talál. Ezek nyújtanak hasznos információkat:

-   [Alkalmazásproxy problémák és hibaüzenetek hibaelhárítása](manage-apps/application-proxy-troubleshoot.md)

-   [Kerberos-hibákhoz, és a jelenség](manage-apps/application-proxy-troubleshoot.md#kerberos-errors)

-   [Egyszeri bejelentkezési modellel működik. Ha a helyszíni és felhőalapú Identitások nem azonos](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Ha portáltól erre a pontra, majd a fő probléma áll fenn. Elindításához külön be a következő három fázisból áll, amelyek elháríthatja a folyamatot.

### <a name="client-pre-authentication"></a>Ügyfelek előhitelesítése 
A külső felhasználó hitelesítéséhez az Azure-bA böngésző használatával. Az Azure előre hitelesíthetik Kerberos által korlátozott Delegálás SSO működéséhez szükséges. Tesztelje, és oldja ezt a lehetőséget, ha probléma merül. Az előhitelesítési szakasza nem kapcsolódik a Kerberos által korlátozott vagy a közzétett alkalmazáshoz. Egyszerűen kijavíthatja az esetleges eltéréseket megerősítést ellenőrzésével a tulajdonos fiók létezik-e az Azure-ban is. Ellenőrizze, hogy nem is le van tiltva, vagy letiltva. A böngészőben a hibaválaszba elég leíró lett okát. Ha bizonytalan, ellenőrizze az egyéb Microsoft cikkekben ellenőrzése.

### <a name="delegation-service"></a>Delegálás szolgáltatás 
A Kerberos szolgáltatásjegy lekérdezi a felhasználók számára egy Kerberos-kulcs terjesztési Center (KCD) az Azure Proxy összekötőn.

A külső kommunikáció az ügyfél és az Azure előtér között nincs hatással van a Kerberos által korlátozott Delegálás. Ezek a kommunikációk csak győződjön meg arról, hogy működik-e a Kerberos által korlátozott Delegálás. Az Azure Proxy szolgáltatás megadott felhasználói azonosító érvénytelen a Kerberos jegy eléréséhez használt. Ez az azonosító nélkül Kerberos által korlátozott Delegálás nem lehet, és sikertelen lesz.

Ahogy korábban említettük, a böngésző hibaüzenetek biztosít bizonyos jó tartalom bemutatásához miért művelet sikertelen. Győződjön meg arról, jegyezze fel a tevékenység azonosítója és a válaszban szereplő Timestamp típusú. Ezen információk alapján, hogy az Azure Proxy naplóbeli eseményei a tényleges összefüggéseket.

   ![Helytelen a Kerberos által korlátozott konfigurációs hiba](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

A megfelelő bejegyzéseket az Eseménynaplóban látható 13019 vagy 12027 események megjelenítése. Az összekötő eseménynaplók található **alkalmazási és Szolgáltatásnaplójában** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt;  **Összekötő** &gt; **Admin**.

   ![Alkalmazásproxy eseménynaplójából 13019 esemény](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![Alkalmazásproxy eseménynaplójából 12027 esemény](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1.   Használjon egy **A** a az alkalmazás-cím, a belső DNS-bejegyzés nem a **CName**.

2.   Megerősítése, hogy az összekötő állomás megkapta a delegált jogosultság a kijelölt célfiók egyszerű Szolgáltatásnevet. Megerősítése, hogy **bármely hitelesítési protokoll** van kiválasztva. További információkért lásd: a [SSO konfigurációs cikk](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

3.   Győződjön meg arról, hogy az egyszerű Szolgáltatásnevet, az Azure AD létező csak egy példánya. A probléma `setspn -x` egy parancs parancssori futtatásával bármely tartományi tag gazdagépen.

4.   Ellenőrizze, hogy a tartományi házirend van érvényben, amely korlátozza a [Kerberos kiállított jogkivonatokat maximális méretén](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Ezzel a házirend-leállítja az összekötő egy jogkivonatot fog, ha túl nagy megtalálható.

Egy hálózati nyomkövetést, amely rögzíti az összekötő állomás- és a KDC között cseréjét a következő lépésre ajánlott szerezni alacsony szintű részletesen a problémákat. További információkért lásd: a [mélyreható kapcsolatos problémák elhárítása papír](https://aka.ms/proxytshootpaper).

Ha jegykezelési megfelelőnek tűnik, tekintse meg a naplófájlokban, amely meghatározza, hogy, hogy a hitelesítés sikertelen volt, mert az alkalmazás a következő a 401-es esemény. Az esemény azt jelzi, hogy a cél alkalmazás elutasította a jegyet. Nyissa meg a következő szakaszára.

### <a name="target-application"></a>Célalkalmazás 
A fogyasztó az összekötő által biztosított Kerberos jegy. Ezen a ponton várható az összekötő és a hátteret a Kerberos szolgáltatásjegy küldött. A jegyet az első alkalmazás kérelem fejléc értéke.

1.   Az alkalmazás belső URL-cím segítségével meghatározott a portálon, ellenőrizze, hogy az alkalmazás közvetlenül a böngészőből az összekötő állomáson elérhető. Majd jelentkezhet be sikeresen. Az összekötő a részletek megtalálhatók **kapcsolatos problémák elhárítása** lap.

2.   Továbbra is az összekötő állomás erősítse meg, hogy a böngésző és az alkalmazás közötti hitelesítés Kerberos-t használja. Hajtsa végre az alábbi műveletek egyikét:

3.  Futtatás DevTools (**F12**) a Internet Explorer vagy [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) a connector gazdagépről. Ugrás az alkalmazás a belső URL-cím segítségével. Vizsgálja meg a felajánlott WWW engedélyezési fejléceket, az eredmény abban az alkalmazásból, és győződjön meg arról, hogy a válasz vagy negotiate vagy Kerberos jelen. 

    a. A következő Kerberos blob az alkalmazás a böngészőből érkező válaszban visszaadott kezdődik-e **YII**. Ezek a betűk jelzi, hogy fut-e a Kerberos. Microsoft NT LAN Manager (NTLM), másrészt, mindig kezdődik **TlRMTVNTUAAB**, amelyen NTLM biztonsági támogatás szolgáltató (NTLMSSP) Ha a Base64 kódolású anyag dekódolni olvassa be. Ha látja **TlRMTVNTUAAB** a blob elején Kerberos nem érhető el. Ha nem lát **TlRMTVNTUAAB**, Kerberos valószínű érhető el.

       > [!NOTE]
       > Ha a Fiddler használata esetén ennél a módszernél ideiglenesen letiltani az alkalmazást az IIS-ben a bővített védelmet.

       ![Hálózati hálózatfelügyeleti böngészőablakban](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    b. A blob ezen az ábrán nem kezdődhet **TIRMTVNTUAAB**. Így ebben a példában a Kerberos érhető el, és a Kerberos-blob nem kezdődhet **YII**.

4.  NTLM ideiglenesen eltávolítása a szolgáltatók listája az IIS-webhelyen. Az alkalmazás közvetlenül elérje az Internet Explorer, az összekötő-állomáson. NTLM már nem a szolgáltatók listája. Az alkalmazás csak Kerberos használatával végezheti el. Ha hozzáférés sikertelen, az alkalmazás konfigurációs probléma lehet. A Kerberos-hitelesítés nem működik.

    a. Ha Kerberos nem érhető el, ellenőrizze az alkalmazás hitelesítési beállításait az IIS-ben. Győződjön meg arról, hogy **Negotiate** tetején, az NTLM csak alatta szerepel. Ha látja **nem egyezteti**, **Kerberos vagy Negotiate**, vagy **PKU2U**, csak folytatja, ha Kerberos működőképességét.

       ![Windows-hitelesítésszolgáltatók](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
    b. A Kerberos és NTLM helyen ideiglenesen letilthatja az előhitelesítési az alkalmazás a portálon. Próbálja elérni az internetről a külső URL-cím segítségével. Számítógép hitelesítéséhez. Ehhez ugyanazt a fiókot használja az előző lépésben elvégezheti. Ha nem, a háttér-alkalmazással, nem a Kerberos által korlátozott Delegálás probléma van.

    c. Engedélyezze újra a portálon előhitelesítés során. Az alkalmazás a külső URL-CÍMEN keresztül csatlakozni próbál hitelesítést Azure szolgáltatáson keresztül. Egyszeri bejelentkezés sikertelen, a böngésző és az esemény 13022 a tiltott hibaüzenet jelenik meg:

       *Microsoft AAD alkalmazásproxy-összekötő nem tudja hitelesíteni a felhasználót, mert a háttérkiszolgáló válaszol-e egy HTTP 401 hiba: a Kerberos hitelesítési kísérleteket.*

       ![Tiltott hibaüzenetet HTTTP 401](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

    d. Ellenőrizze az IIS-alkalmazás. Győződjön meg arról, hogy a konfigurált alkalmazáskészlet és az egyszerű szolgáltatásnév használatára van konfigurálva ugyanazt a fiókot az Azure ad-ben. Lépjen az IIS-ben a következő ábrán látható módon:

       ![IIS alkalmazás konfigurációs ablak](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

       Után tudja, hogy az identitás, ellenőrizze, hogy ennek a fióknak az egyszerű szolgáltatásnév van konfigurálva. Például: `setspn –q http/spn.wacketywack.com`. Adja meg a következő szöveget a parancssorba:

       ![SetSPN parancssori ablakban](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

    e. Ellenőrizze az egyszerű Szolgáltatásnevet, az alkalmazás beállításait a portálon képest van meghatározva. Győződjön meg arról, hogy az Azure AD-fiókot a célon konfigurált azonos SPN app alkalmazáskészlet használják.

       ![Az Azure portálon SPN-konfiguráció](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
    f. Nyissa meg az IIS, és válassza a **Konfigurációszerkesztő** lehetőséget az alkalmazás. Navigáljon a **system.webServer/security/authentication/windowsAuthentication**. Győződjön meg arról, hogy az érték **UseAppPoolCredentials** van **igaz**.

       ![Az IIS konfigurációs app készletek hitelesítőadat-beállítás](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

       Módosítsa az értéket **igaz**. A háttér-kiszolgálófiók minden gyorsítótárazott Kerberos-jegyet eltávolítása a következő parancs futtatásával:

       ```powershell
       Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
       ``` 

További információkért lásd: [a Kerberos jegy ügyfélgyorsítótár kiürítése az összes munkamenetek](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).



Ha nem adja a Kernel mód engedélyezve van, az javítja a Kerberos műveletek. De is felvet a jegyet a számítógépfiók használatával fejthetők vissza a kért szolgáltatás. Ennek a fióknak a helyi rendszer néven is ismert. Ez az érték beállítása **igaz** megszünteti a Kerberos által korlátozott, ha az alkalmazás a farmban lévő több kiszolgáló között.

-   Egy további ellenőrzést, letiltásának **kiterjesztett** védelmi túl. Bizonyos esetekben **kiterjesztett** védelmi túllépte a Kerberos által korlátozott Delegálás, amikor az adott konfigurációk lett engedélyezve. Ezekben az esetekben egy alkalmazásnak a gyártója megegyezik az alapértelmezett webhelyhez. Ez az alkalmazás csak névtelen hitelesítésre van konfigurálva. A párbeszédpanelek használhatók, amelyek javasol gyermekobjektumok nem tudnák bármely active-beállítások örökléséhez. Azt javasoljuk, hogy tesztelni, de ne felejtse el ezt az értéket visszaállítása **engedélyezett**, ahol csak lehetséges.

    A további ellenőrzést helyezi a közzétett alkalmazáshoz használandó teljesülnek. Akkor is léptetési további összekötők delegálása is állítottak be. További információkért olvassa el a részletesebb műszaki útmutató [hibaelhárítása az Azure AD-alkalmazásproxy](https://aka.ms/proxytshootpaper).

Ha még nem lehet folyamatban van, a Microsoft támogatási segítséget nyújthatnak. Hozzon létre egy támogatási jegy közvetlenül a portálon belül. Egy mérnököt kapcsolatba lép Önnel.

## <a name="other-scenarios"></a>Egyéb forgatókönyvek

- Azure-alkalmazásproxy Kerberos-jegy kérelmek egy alkalmazás a kérelem elküldése előtt. Bizonyos külső alkalmazások, például Tableau kiszolgáló nem szeretné ezt a módszert a hitelesítésére. Ezek az alkalmazások sor több hagyományos egyeztetések várt. Az első kérésre névtelen, amely lehetővé teszi, hogy az alkalmazás szeretne válaszolni, hogy a 401-es keresztül támogatja a hitelesítési típus.

- Többugrásos hitelesítés gyakran használják forgatókönyvekben ahol a kérelmet rétegzett, egy háttér és előtér, ahol mindkettő igényel a hitelesítéshez, például az SQL Server Reporting Services. A többszörös ugrásos forgatókönyv konfigurálásához lásd: a támogatási cikk [Kerberos által korlátozott delegálás lehet szükséges Protokollátmenet Többugrásos forgatókönyvekben](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>További lépések
[Kerberos által korlátozott Delegálás konfigurálása a felügyelt tartományhoz](../active-directory-domain-services/active-directory-ds-enable-kcd.md).
