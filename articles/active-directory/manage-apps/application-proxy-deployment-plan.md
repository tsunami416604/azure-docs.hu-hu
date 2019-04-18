---
title: Az Azure Active Directory Application Proxy központi telepítésének megtervezése
description: Egy teljes körű útmutatójában a szervezeten belüli Application proxy telepítésének megtervezése
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04-04-2019
ms.author: barbaraselden
ms.reviewer: ''
ms.openlocfilehash: fe8f9f271599d688878d61aee64273690d02c2b8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685839"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Az Azure AD-alkalmazásproxy központi telepítésének megtervezése

Azure Active Directory (Azure AD) alkalmazásproxy a helyszíni alkalmazások biztonságos és költséghatékony távoli hozzáférési megoldás is. Biztosít egy azonnali átmenet elérési út "Cloud First" szervezeteknek örökölt való hozzáférés kezelése a helyszíni alkalmazásokat, amelyek még nem képes a modern protokollok használatával. További bevezető jellegű információkért lásd: [Mi az Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) és [Application Proxy működése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Ez a cikk a szükséges tervezéséhez, üzemeltetése és kezelése az Azure AD Application Proxy erőforrásokat tartalmaz. 

## <a name="plan-your-implementation"></a>A megvalósítás tervezése

A következő szakasz a tervezési elemek, amelyek lesz beállítva, felületet nyújt a hatékony üzembe saját kulcsot széles körű nézetét jeleníti meg. 

### <a name="prerequisites"></a>Előfeltételek

Felel meg a következő előfeltételek vonatkoznak a megvalósítás megkezdése előtt kell. A környezetben, ezeket az előfeltételeket, beleértve a jelen beállításával kapcsolatos további információkat tekintheti meg [oktatóanyag](application-proxy-add-on-premises-application.md).

* **Összekötők**: Az összekötők olyan egyszerűsített ügynökök, az alakzatot üzembe helyezhető:
   * Helyszíni fizikai hardver
   * Hipervizor megoldással-ban üzemeltetett virtuális gépek
   * Kimenő kapcsolat az alkalmazásproxy engedélyezése az Azure-ban üzemeltetett virtuális gépek.

Lásd: [megismerheti az Azure AD alkalmazásproxy-összekötők](application-proxy-connectors.md) részletesebb áttekintése.

   * Összekötő gazdagépek kell [engedélyezni kell a TLS 1.2](application-proxy-add-on-premises-application.md) az összekötők telepítése előtt.

   * Ha lehetséges, üzembe helyezése az összekötők a [ugyanazon a hálózaton](application-proxy-network-topology.md) és a háttér-alkalmazáskiszolgálókat, szegmens. Célszerű az alkalmazások felderítés végeztével összekötő-gazdagépként használandó számítógépek telepítéséhez.

* **Hálózati hozzáférés beállításai**: Az Azure AD-alkalmazásproxy összekötőit [megpróbál csatlakozni az Azure HTTPS (443-as TCP Port) és a HTTP (80-as TCP Port)](application-proxy-add-on-premises-application.md). 

   * Megszakítást összekötő TLS-forgalom nem támogatott, és megakadályozza, hogy az összekötők a saját Azure App Proxy-végponttal rendelkező biztonságos csatornát létrehozó.

   * Ne használjon minden űrlap beágyazott ellenőrzési kimenő TLS kommunikáció összekötők és az Azure között. Belső hálózatfelügyeleti egy összekötő és a háttérkiszolgáló alkalmazások között lehetőség azonban ronthatja a felhasználói élmény, és mint ilyen, nem ajánlott.

   * Az alkalmazásproxy-összekötők magukat a terheléselosztás akkor is nem támogatott, vagy még akkor is szükséges.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Az Azure AD-alkalmazásproxy konfigurálása előtt fontos szempontok

Az alábbi alapvető követelményeknek teljesülniük kell ahhoz, hogy konfigurálnia és alkalmaznia az Azure AD-alkalmazásproxy.

*  **Előkészítési**: Application proxy üzembe helyezése előtt felhasználói identitásokat a helyszíni címtárból szinkronizált vagy közvetlenül az Azure AD-bérlőt belül létrehozni. Identitásszinkronizálási lehetővé teszi, hogy az Azure ad-ben előre hitelesíti a felhasználókat, mielőtt őket hozzáférés engedélyezése az alkalmazásproxyval közzétett alkalmazások és az egyszeri bejelentkezés (SSO) végrehajtásához szükséges felhasználói információk.

* **Nyilvános tanúsítvány**: Egyéni tartománynevek használatakor meg kell szerzik be a nyilvános tanúsítvány nem a Microsofttól megbízható hitelesítésszolgáltató által kibocsátott. Szervezeti követelményektől függően egy tanúsítvány eltarthat egy ideig, és azt javasoljuk, hogy a lehető leghamarabb folyamat. Az Azure Application Proxy támogatja a standard, [helyettesítő](application-proxy-wildcard.md), vagy SAN-alapú tanúsítványokat.

* **Tartományra vonatkozó követelmények**: Egyszeri bejelentkezéshez a Kerberos által korlátozott delegálás (KCD) használatával közzétett alkalmazásokban kell lennie, hogy egy összekötő gazdagép a tartományhoz a azonos AD-tartományhoz, mint az alkalmazások közzététele folyamatban van. A témakör részletes információkért lásd: [az egyszeri bejelentkezés KCD](application-proxy-configure-single-sign-on-with-kcd.md) az alkalmazásproxy használatával. Az összekötő-szolgáltatás a helyi rendszer környezetében fut, és nem az egyéni identitás használatára kell beállítani.

* **DNS-rekordok URL-címek**

   * Egyéni tartományok az alkalmazásproxy használata előtt létre kell hoznia egy CNAME rekordot a nyilvános DNS-ben, így az ügyfelek oldja fel az egyéni megadott külső URL-CÍMÉT az előre definiált Application Proxy cím. Hozzon létre egy CNAME rekordot, az egyéni tartományt használó alkalmazások nem megakadályozza a távoli felhasználók számára az alkalmazás csatlakozik. Meg kell adnia a CNAME-rekordokat a DNS szolgáltató a szolgáltató eltérhet lépéseket így megtudhatja, hogyan [DNS-rekordok és -rekordhalmazok kezelése az Azure portal segítségével](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Összekötő gazdagépek hasonlóan tudja feloldani az alkalmazások közzétételét belső URL-Címének kell lennie.

* **Rendszergazdai jogosultságokkal, és szerepkörök**

   * **Összekötő telepítési** megköveteli a Windows-kiszolgálóra, amely azt telepítése a helyi rendszergazdai jogosultságokkal. Legalább egy alkalmazás rendszergazdai szerepkör és az összekötő-példányt az Azure AD-bérlő regisztrálásához hitelesítéséhez is igényel. 

   * **Alkalmazás közzététele és felügyeleti** megkövetelése a *alkalmazás-rendszergazda* szerepkör. Alkalmazás-rendszergazdák kezelhetik a címtárban, beleértve a regisztrációk, egyszeri bejelentkezési beállításainak, felhasználó és csoport-hozzárendelések és licencelési, alkalmazást proxybeállításokat és jóváhagyás minden alkalmazás. Nem adja meg azt a feltételes hozzáférés kezelését. A *Felhőalkalmazás-rendszergazda* szerepkör rendelkezik a képességek az alkalmazás-rendszergazda, azzal a különbséggel, hogy nem teszi lehetővé az alkalmazásproxy-beállítások kezelését.

* **Licencelési**: Az alkalmazásproxy az alapszintű Azure AD-előfizetés keretében érhető el. Tekintse meg a [Azure Active Directory díjszabás oldala](https://azure.microsoft.com/pricing/details/active-directory/) licencelési, beállítások és funkciók teljes listáját. 

* Szerezze be az alkalmazás-rendszergazda jogosultságokkal keresztül szükség lehet egy szerepkör jogosultságszint-emelési [Privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) (PIM), ezért győződjön meg arról, hogy a fiók nem jogosult. 

### <a name="application-discovery"></a>Alkalmazásdetektálás

Fordítsa le egy készlet az összes releváns alkalmazás folyamatban keresztül az alkalmazásproxy által közzétett gyűjtése az alábbi adatokat:

| Információ típusa| A gyűjtendő információ |
|---|---|
| Szolgáltatás típusa| Példa: SharePoint, SAP, CRM, Custom Web Application, API |
| Alkalmazásplatform | Példa: Windows IIS, Apache on Linux, Tomcat, NGINX |
| Tartományi tagság| Webalkalmazás-kiszolgáló teljesen minősített tartománynevét (FQDN) |
| Application helye | A webalkalmazás-kiszolgáló vagy a farm helyét az infrastruktúra |
| Belső hozzáférés | A pontos URL-címet használja, ha az alkalmazás belső elérésének. <br> Ha a farm, hogy milyen típusú terheléselosztási használatban van? <br> E az alkalmazás önmagától eltérő forrásokból rajzol tartalmat.<br> Határozza meg, ha az alkalmazás működik a websockets protokoll. |
| Külső hozzáférés | Az alkalmazás már elérhető, külsőleg gyártói megoldást. <br> Külső hozzáférés céljából használni kívánt URL-címe. Ha a SharePoint, győződjön meg, hogy a másodlagos címek leképezése egy konfigurált [Ez az útmutató](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Ha nem, adja meg a külső URL-címeket kell. |
| Nyilvános tanúsítvány | Ha egy egyéni tartományt használ, be kell szereznie egy megfelelő tulajdonosnévvel rendelkező tanúsítványt. Ha egy tanúsítvány létezik-e, vegye figyelembe a sorozatszámot és a hely, amennyiben szerezhető be. |
| Hitelesítés típusa| Az alkalmazás támogatási, például az alapszintű, a Windows integrációs hitelesítés űrlapalapú, a fejlécalapú, és a jogcímek által támogatott hitelesítési típust. <br>Ha az alkalmazás egy adott tartományi fiók alatt futtatandó van konfigurálva, vegye figyelembe a teljesen minősített tartomány nevét (FQDN) a szolgáltatás fiók.<br> Ha a SAML-alapú, az azonosítóját, valamint a válasz URL-címeket. <br> Ha fejlécalapú, adja a gyártói megoldást és hitelesítés kezeléséhez követelményei. |
| Összekötő neve | A csoport ki kell jelölni a továbbítás és egyszeri Bejelentkezést biztosít ezen háttérrendszer alkalmazás-összekötők a logikai neve. |
| Felhasználók/csoportok hozzáférés | A felhasználókat vagy felhasználócsoportokat, amelyek az alkalmazás külső hozzáférést kapnak. |
| További követelmények | Megjegyzés: minden olyan további távelérési vagy a biztonsági követelmények, amelyek az alkalmazás közzététele be kell figyelembe venni. |

Letöltheti ezt [application inventory számolótábla](https://aka.ms/appdiscovery) leltározhatók az alkalmazásait.

### <a name="define-organizational-requirements"></a>Szervezeti követelmények meghatározása

Az alábbiakban a területek, amelyhez be kell állítania az a szervezet üzleti követelményeinek. Minden egyes területen található példák a követelményekre

 **Access (Hozzáférés)**

* Tartomány és az Azure AD-felhasználók a Ha bármely tartományhoz csatlakoztatott és az Azure AD-hez csatlakoztatott eszközök hozzáférhetnek a közzétett alkalmazások biztonságos és zökkenőmentes egyszeri bejelentkezés (SSO).

* A saját eszközt jóváhagyott felhasználók biztonságosan érhessék el a közzétett alkalmazások, az MFA-ban regisztrált, és a hitelesítési módszerként a Microsoft Authenticator alkalmazást a mobiltelefonon regisztrált.

**Cégirányítási** 

* A rendszergazdák meghatározására és figyelésére a felhasználó-hozzárendeléseket alkalmazásproxyn keresztül közzétett alkalmazások életciklusát.

**Biztonság**

* Csak a felhasználók hozzárendelve a csoporttagság-alkalmazásba, és külön-külön érhessék el ezeket az alkalmazásokat.

**Teljesítmény**

* Nincs nem a belső hálózati alkalmazás elérése képest alkalmazásteljesítmény romlást okoz.

**Felhasználói élmény**

* Felhasználók hogyan érik el alkalmazásokat bármelyik eszközplatformra a jól ismert vállalati URL-címek használatával ismerik.

**Naplózás**
* A rendszergazdák képesek tevékenység a felhasználói hozzáférés naplózása.


### <a name="best-practices-for-a-pilot"></a>Ajánlott eljárások a próbaüzem

Idő és erőfeszítés szükséges teljes mértékben a távelérés egyszeri bejelentkezéssel (SSO) egyetlen alkalmazás Bizottság mennyisége határozza meg. Ehhez egy próbaprogram, amely úgy ítéli meg, a kezdeti felderítés, közzététel és általános tesztelés futtatásával. Egyszerű IIS-alapú webes alkalmazás, amely már előre konfigurált az integrált Windows hitelesítés (IWA) használatával segít az alapértékeket, ahogy ezt a beállítást igényel a sikeres próbaüzem távoli hozzáférést és egyszeri Bejelentkezést, minimális erőfeszítéssel.

Az alábbi tervezési elemek növelje a próbaüzem végrehajtását közvetlenül az éles üzemi bérlők sikeres.  

**Összekötő-felügyeleti**:  

* Az összekötők, amely a helyszíni átjáró az alkalmazások kulcsfontosságú szerepet játszanak. Használatával a **alapértelmezett** összekötőcsoport verziója megfelelő, a kezdeti kísérleti vizsgálati közzétett alkalmazások Mielőtt éles környezetben való üzembe helyezés őket. Sikeresen tesztelt alkalmazások éles összekötőcsoportok majd helyezheti át.

**Az Alkalmazáskezelés**:

* A munkatársak megjegyezni egy külső URL-cím ismer, vagy amelyek nagy valószínűséggel. Ne tegye közzé alkalmazását az előre definiált msappproxy.net vagy onmicrosoft.com utótag használatával. Ehelyett adja meg egy jól ismert legfelső szintű ellenőrzött tartomány, például a következő előtaggal logikai állomásnévvel rendelkező *intranetes. < customers_domain > .com*.

* Korlátozza a próbaüzem alkalmazás ikonja látható-e egy próbacsoportban elrejti a indítása ikon formájában az Azure MyApps portálról. Ha készen áll az éles gazdagépcsoportjaira az alkalmazásnak, hogy a megfelelő célközönség, vagy éles üzem előtti ugyanabban a bérlőben, vagy közzétételével is az alkalmazást az éles környezetbeli bérlőhöz.

**Egyszeri bejelentkezés beállításai**: Néhány egyszeri bejelentkezési beállításainak állított be, ezért ne kelljen függőségek biztosításával az késleltetések szolgálhatók változáskezelésre időt is igénybe vehet bizonyos függőségekkel rendelkezik. Ez magában foglalja a tartománycsatlakozási összekötő gazdagépek egyszeri Bejelentkezést, a Kerberos által korlátozott delegálás (KCD) használatával, és gondoskodik a más időigényes tevékenységek végrehajtásához. Például PING Access-példány beállítása, ha a fejléc-alapú egyszeri bejelentkezés kellene.

**Összekötő gazdagép és a cél alkalmazás közötti SSL**: Biztonsági létfontosságú, így az összekötő gazdagép és a cél az alkalmazások közötti TLS mindig kell használni. Különösen akkor, ha a webalkalmazás Űrlapalapú hitelesítés űrlapalapú van konfigurálva, felhasználói hitelesítő adatok majd hatékonyan továbbított szövegként.

**Növekményes megvalósítása, és az egyes lépések teszteléséhez**. Magatartási alapvető funkcionális tesztelés után győződjön meg arról, hogy minden felhasználó és az üzleti követelmények teljesülnek-e az alábbi utasításokat az alkalmazás közzététele:

1. Tesztelje, és a webes alkalmazásba való általános elérésének ellenőrzése az üzem előtti hitelesítés le van tiltva.
2. Ha sikeres engedélyezze az előhitelesítést, és rendelje hozzá a felhasználókat és csoportokat. És hitelesíti a hozzáférést.
3. Ezután adja hozzá a az alkalmazás egyszeri bejelentkezési metódust, és tesztelje újból hozzáférés ellenőrzése.
4. Alkalmazza a feltételes hozzáférés és a többtényezős hitelesítés szabályzatokat szükség szerint. És hitelesíti a hozzáférést.

**Hibaelhárítási eszközök**: Esetén végzett hibaelhárításhoz, mindig első lépésként hozzáférés ellenőrzése a közzétett alkalmazás az összekötő-gazdagépen a böngészőből, és győződjön meg arról, hogy az alkalmazás a várt módon működik. Az egyszerűbb a telepítőt, a könnyebb megállapítani okát, ezért érdemes próbál reprodukálnia minimális konfigurációval például csak egy összekötőt, és nincs egyszeri Bejelentkezéssel kapcsolatos problémák. Bizonyos esetekben webes hibaelhárító eszközök, például a Telerik Fiddler bizonyíthatja nélkülözhetetlen hozzáférést vagy a tartalom hibaelhárítás proxyn keresztül érhetők el alkalmazások. A fiddler is működhet-e a nyomkövetési és hibakeresési forgalom mobil platformokhoz, például az iOS és Android-proxyként, és szinte bármely, amely konfigurálható proxyn keresztül. Tekintse meg a [hibaelhárítási útmutató](/application-proxy-troubleshoot.md) további információt.

## <a name="implement-your-solution"></a>A megoldás megvalósítása

### <a name="deploy-application-proxy"></a>Application Proxy üzembe helyezése

A lépéseket az Application Proxy üzembe helyezése terjed ki a jelen [oktatóanyag távoli hozzáférés a helyszíni alkalmazás hozzáadására szolgáló](application-proxy-add-on-premises-application.md). Ha a telepítés nem sikeres, válassza ki a **alkalmazásproxy hibaelhárítása** a portálon, vagy használja a hibaelhárítási útmutató[az alkalmazásproxy-ügynök Összekötőjével telepítésével járó problémák](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Tegye közzé az alkalmazásokat az alkalmazásproxy-n keresztül

Alkalmazás-közzététel feltételezi, hogy, hogy teljesült-e a teljesüljenek és, hogy rendelkezik néhány regisztrált megjelenítő összekötők és aktív az alkalmazásproxy-oldalon.

Alkalmazások közzététele a [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Az alábbiakban néhány gyakorlati tanácsot, kövesse az alkalmazás közzététele:

* **Összekötő-csoportok használata**: Rendeljen hozzá egy összekötőcsoporthoz, amely minden egyes alkalmazás-közzététel van kijelölve.

* **Állítsa be a háttéralkalmazás túllépte az időkorlátot**: Ez a beállítás akkor hasznos, forgatókönyvekben, ahol az alkalmazás egy ügyfél tranzakció feldolgozása több mint 75 másodperc lehet szükség. Például amikor egy ügyfél egy lekérdezést küld egy webalkalmazást, amely működik az előtér-adatbázishoz. Az előtér ezt a lekérdezést küld a háttérbeli adatbázis-kiszolgáló és a válaszra vár, de a ideje választ kap, a témakör az ügyféloldalon túllépi az időkorlátot. Az időtúllépés beállítás 180 másodperc hosszabb tranzakciók elvégzéséhez hosszú biztosít.

* **Használjon megfelelő cookie-k**

   * **Csak HTTP Cookie**: Biztosít további biztonsági kellene felvenni a HTTPOnly jelző HTTP-válaszfejlécek beállítása – cookie-k alkalmazásproxy. Ez a beállítás segít mérsékelni az azokat kihasználó támadások ellen, például a webhelyek közötti parancsfájl-kezelési (XSS). Ne módosítsa a munkamenet cookie-k hozzáférést igénylő ügyfelek és a felhasználók ügynökök esetében nem. Például az RDP/MTSC ügyfél csatlakozik a távoli asztali átjáró alkalmazásproxyn keresztül közzétett.

   * **Cookie-k biztonságos**: A cookie-k biztonságos attribútum van beállítva, ha a felhasználói ügynök (ügyféloldali alkalmazás) csak tartalmazza a cookie-t a HTTP-kérelmekre, ha a kérelem eljut a TLS biztonságos csatornán keresztül. Ez segít a kockázatát, hogy a cookie-k feltörését keresztül tiszta szöveges csatornák, ezért engedélyezni kell.

   * **Állandó Cookie**: Lehetővé teszi a fennmaradó érvényes, amíg a, vagy lejárt, vagy törölték a böngésző szünnapok közötti megőrzéséhez a Application Proxy munkamenetcookie-t. Használt forgatókönyvekhez, ahol egy gazdag például az office-alkalmazás fér hozzá egy dokumentumot a közzétett webalkalmazásokhoz, anélkül, hogy a felhasználó hitelesítési újra kérni. Legyen körültekintő engedélyezése azonban maradandó cookie-k, végső soron hagyhatja egy szolgáltatás, fennáll a kockázata, illetéktelen hozzáférés ellen, ha nem használ más kompenzáló vezérlők együtt. Ez a beállítás csak a régebbi alkalmazásokat, amelyek nem oszthat meg a cookie-k folyamatok közötti használandó. Érdemes frissíteni az alkalmazás megosztási cookie-k helyett ezzel a beállítással a folyamatok közötti kezeléséhez.

* **A fejlécek URL-címek lefordítása**: Ez engedélyezi forgatókönyvekhez, ahol a belső DNS nem konfigurálható, a szervezet nyilvános névtér (más néven Split DNS) megfelelően. Kivéve, ha az alkalmazás az eredeti állomásfejlécet az ügyfél kérésében igényel, hagyja ezt az Igen értéket. A tulajdonos alternatív, hogy az összekötő, használja a teljes Tartománynevet a belső URL-útválasztás, a tényleges forgalmat, és a teljes külső URL-címét, a gazdagép-fejléc. A legtöbb esetben ez a megoldás lehetővé teszi az alkalmazás működéséhez szokásos módon, amikor távolról elérhető, de a felhasználók elveszítik a megfelelő belül és azon URL-cím kívül járó előnyöket.

* **A kérelem törzsében URL-címek lefordítása**: Ha azt szeretné, hogy a hivatkozások az alkalmazásból, a válaszokat az ügyfélnek küldött lekérdezésekké, kapcsolja be a Alkalmazástörzs hivatkozás fordítási egy alkalmazáshoz. Ha engedélyezve van, ezt a funkciót biztosít a legjobb erőfeszítés kísérlet a alkalmazásproxy talál az ügyfelek számára visszaadott HTML és CSS-válaszok összes belső hivatkozások fordítása. Ez akkor hasznos, ha a szokott abszolút vagy NetBIOS shortname hivatkozásai a tartalmat tartalmazó alkalmazásokat, vagy a tartalom, amely más alkalmazások közzététele a helyszíni alkalmazásokat.

Forgatókönyvek, ahol a egy közzétett alkalmazás hivatkozások más közzétett alkalmazásokat engedélyezze hivatkozás fordítási vagy egyes alkalmazásokhoz, hogy a felhasználói élmény felett az alkalmazásonkénti szintjén.

Tegyük fel például, hogy három olyan alkalmazásokkal rendelkezik, hogy az összes hivatkozás egymáshoz alkalmazásproxyn keresztül közzétett: Előnyök, költségek, és utazási, valamint egy negyedik alkalmazást, visszajelzés, alkalmazásproxyn keresztül közzétett nem.

![1. kép](media/App-proxy-deployment-plan/link-translation.png)

Hivatkozás fordítási az előnyök alkalmazás engedélyezésekor a rendszer a költségek és utazási mutató hivatkozásokat tartalmaz átirányítja ezen alkalmazások esetén a külső URL-címeket úgy, hogy az alkalmazások a vállalati hálózaton kívül hozzáférő felhasználók érhetik el azokat. Vissza előnyeit a költségek és utazás hivatkozások nem működnek, mert azokat az alkalmazásokat két hivatkozás fordítási még nem lett engedélyezve. A hivatkozásra kattintva visszajelzés nem irányítja át, mert nincsenek külső URL-cím, így az előnyök alkalmazást használó felhasználók nem tudják a vállalati hálózaton kívül a visszajelzés-alkalmazás elérésére. Részletes információk megjelenítéséhez a [fordítási és egyéb átirányítási lehetőségek](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Az alkalmazás eléréséhez

Több lehetőség is létezik a hozzáférés-kezelés az alkalmazásproxyval közzétett erőforrásait, így válassza a legmegfelelőbb az adott forgatókönyv és a méretezhetőség igényeinek. Általános megközelítéseket tartalmazza: helyi csoportok az Azure AD Connect használatával szinkronizált használata felhasználói attribútumok, önkiszolgáló erőforrás tulajdonosa, vagy a kettő kombinációjára irányításával ezek által kezelt csoportok használata a dinamikus csoportok létrehozása az Azure AD alapján. Tekintse meg az egyes megoldások előnyeiről a kapcsolt erőforrásokban.

Alkalmazásokhoz való hozzáférés hozzárendelése a felhasználók a legtöbb nagyon egyszerű módja van átnézi a **felhasználók és csoportok** lehetőség a bal oldali panelen a közzétett alkalmazás és a közvetlen hozzárendelését a csoportoknak vagy személyeknek.

![Kép 24](media/App-proxy-deployment-plan/add-user.png)

Hozzáférés az önkiszolgáló felhasználók az alkalmazás is engedélyezheti, egy csoportot, amely jelenleg nem tagja, és az önkiszolgáló lehetőségek konfigurálása.

![Kép 25](media/App-proxy-deployment-plan/allow-access.png)

Ha engedélyezve van, felhasználók tudnak bejelentkezni a MyApps portál és hozzáférés kérése, és automatikus jóváhagyása és a egy kijelölt jóváhagyó az önkiszolgáló csoportkezelési már engedélyezett, vagy a jóváhagyandó hozzáadott kell lennie.

Vendég felhasználók is lehet [alkalmazásproxy keresztül az Azure AD B2B közzé belső alkalmazás-hozzáférési meghívót](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

A helyi alkalmazások, amelyek általában elérhető-e névtelenül, hitelesítés nélkül, hogy érdemesebb lehet letiltja a beállítást, az alkalmazás a **tulajdonságok**.

![Kép 26.](media/App-proxy-deployment-plan/assignment-required.png)


Ha ez a beállítás a nem értékre állítva lehetővé teszi a felhasználóknak engedélyek nélkül az Azure AD-alkalmazásproxyn keresztül a helyszíni alkalmazás eléréséhez, ezért körültekintően.

Az alkalmazás közzététele után kell elérhető írja be a külső URL-címet egy böngészőben vagy található ikonjukra [ https://myapps.microsoft.com ](https://myapps.microsoft.com/).

### <a name="enable-pre-authentication"></a>Üzem előtti hitelesítés engedélyezése

Ellenőrizze, hogy az alkalmazás az alkalmazásproxyn keresztül érhető el. 

1. Navigáljon a **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás** , és válassza ki a kezelni kívánt alkalmazást.

2. Válassza ki **alkalmazásproxy**.

3. Az a **előhitelesítés** mezőhöz, használja a legördülő listából válassza ki a **Azure Active Directory**, és válassza ki **mentése**.

Az üzem előtti hitelesítés engedélyezve van az Azure AD fog mondd csak a hitelesítéshez, és majd a háttéralkalmazás kell is próbára teszi hitelesítést igényel, ha. Az Azure AD-csatlakoztatott előhitelesítéséhez korlátlanról is konfigurálja a külső URL-cím a HTTPS, így bármely kezdetben a HTTP-hez konfigurált alkalmazás már biztonságát a HTTPS.

### <a name="enable-single-sign-on"></a>Egyszeri bejelentkezés engedélyezése

Egyszeri Bejelentkezést biztosít a legjobb lehetséges felhasználói élmény és a biztonsági, mert a felhasználók csak be kell jelentkeznie egyszer Azure ad-ben való hozzáféréskor. Miután a felhasználó már megtörtént, egyszeri Bejelentkezést hajt végre az Application Proxy connector való hitelesítése a helyszíni alkalmazás a felhasználó nevében. A háttéralkalmazás dolgozza fel a bejelentkezés, mintha a felhasználó saját magukat. 

Választás a **csatlakoztatott** beállítás lehetővé teszi, hogy a felhasználók hozzáférhessenek a közzétett alkalmazást anélkül hogy az Azure AD-hitelesítést.

Egyszeri bejelentkezés elvégzése csak akkor lehetséges, ha az Azure ad-ben a felhasználói hozzáférés kérése az erőforrás, így az alkalmazás konfigurálni kell, hogy előre a felhasználók hitelesítése után hozzáférést az egyszeri bejelentkezés függvény azonosíthatja, ellenkező esetben az egyszeri bejelentkezés beállításai le lesz tiltva.

Olvasási [egyszeri bejelentkezés az Azure AD-alkalmazások](what-is-single-sign-on.md) segítségével válassza ki a legmegfelelőbb egyszeri bejelentkezési módszer, az alkalmazások konfigurálásakor.

###  <a name="working-with-other-types-of-applications"></a>Más típusú alkalmazások használata

Az Azure AD-alkalmazásproxy is támogat, amelyek használatához az Azure AD Authentication Library fejlesztettek alkalmazásokat ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) vagy Microsoft-hitelesítési tár ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)). Natív ügyfélalkalmazások által használó Azure ad-ben kiadott a fejléc-információkat az ügyfél kérése a felhasználók nevében előtti hitelesítés elvégzéséhez a visszakapott jogkivonatokat támogatja.

Olvasási [ügyfél natív és mobil alkalmazások közzétételéhez](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) és [jogcímalapú alkalmazásokat](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) az Application Proxy rendelkezésre álló beállításokkal kapcsolatos további.

### <a name="use-conditional-access-to-strengthen-security"></a>Feltételes hozzáférés használata a biztonság megszilárdítása

Alkalmazás biztonsági fejlett biztonsági funkciókat, amely képes megvédeni a, és reagálhat a komplex veszélyforrások helyszíni és felhőbeli igényel. A támadók leggyakrabban hozzáférni a vállalati hálózaton keresztül gyenge, alapértelmezett vagy lopott felhasználói hitelesítő adatokat.  A Microsoft identitásalapú biztonsági kezelése és védelme az emelt szintű és a nem kiemelt jogosultságú identitások csökkenti ellopott hitelesítő adatok használatát.

Az alábbi képességeket támogatja az Azure AD-alkalmazásproxy használható:

* Felhasználó- és helyalapú feltételes hozzáférés: A földrajzi hely vagy egy IP-cím alapján, a felhasználói hozzáférés korlátozása által védett bizalmas adatok megtartása [helyalapú feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Eszközalapú feltételes hozzáférés: Győződjön meg arról, csak a regisztrált, jóváhagyott és megfelelő eszközök férhessenek hozzá a vállalati adatok [eszközalapú feltételes hozzáférési](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Alkalmazásalapú feltételes hozzáférés: Munkahelyi leállítása, ha a felhasználó nem a vállalati hálózaton nem kell. [Biztonságos hozzáférés a vállalati felhő- és a helyszíni alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) és a feltételes hozzáférés-vezérlés kezelése.

* Kockázatalapú feltételes hozzáférés: Megvédheti adatait a rosszindulatú támadók egy [kockázatalapú feltételes hozzáférési szabályzat](https://www.microsoft.com/cloud-platform/conditional-access) , amelyek alkalmazhatók minden alkalmazás és az összes felhasználóra, hogy a helyszíni vagy a felhőben.

* Az Azure AD-alkalmazás panelen: Az alkalmazásproxy-szolgáltatás telepítve, és biztonságosan közzétett alkalmazások, az ajánlat a felhasználók egy egyszerű központ felderítése és érik el a alkalmazásokat. Önkiszolgáló képességek, például a kérhet hozzáférést az új alkalmazások és a csoportok vagy keresztül, mások nevében ezekhez az erőforrásokhoz való hozzáférés kezelése a termelékenység növelése a [hozzáférési Panel](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>A megvalósítás kezelése

### <a name="required-roles"></a>Szükséges szerepkörök

A Microsoft a lehető legkevesebb jogosultsággal az Azure AD szükséges feladatok végrehajtásához megadásának elve szakemberekkel. [Tekintse át a különböző Azure-szerepkörök elérhető](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) , és válassza ki a megfelelőt, minden egyes személy igényeinek kielégítésére. Egyes szerepkörök ideiglenesen a alkalmazni, és eltávolítja a központi telepítés befejezése után szükség lehet.

| Üzleti szerepkör| Üzleti feladatok| Azure AD-szerepkörök |
|---|---|---|
| Ügyfélszolgálati rendszergazdai | Általában legfeljebb feltételeknek megfelelő felhasználó jelentett problémák, és korlátozott műveleteket, mint a felhasználói jelszavak módosítása, a frissítési biztonsági jogkivonat érvénytelenítése és a szolgáltatás állapotának monitorozása. | Ügyfélszolgálati adminisztrátor |
| Identitás-rendszergazda| Olvassa el az Azure AD bejelentkezési a jelentésekben és -naplók hibakeresése az alkalmazásproxyval kapcsolatos problémák.| Biztonsági olvasó |
| Alkalmazás tulajdonosa| Hozzon létre, és a vállalati alkalmazásokat, alkalmazásregisztrációkat és alkalmazásproxy-beállítások minden szempontjának kezeléséhez.| Alkalmazás-rendszergazda |
| Infrastruktúra-rendszergazda | Tanúsítvány helyettesítő tulajdonosa | Alkalmazás-rendszergazda |

Biztonságos adatokat és erőforrásokat hozzáférő felhasználók számának minimalizálása segít csökkenti az esélyét, hogy egy rosszindulatú aktor beszerzése az illetéktelen hozzáféréstől és a egy jogosult felhasználó véletlenül érintő az erőforrás-és nagybetűket. 
 
Azonban felhasználók továbbra is el kell végezniük, a mindennapos privilegizált műveleteket, ezért kényszerítése – igény (szerinti JIT) alapú [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) szabályzatokat biztosít az igény szerinti, emelt szintű hozzáférés az Azure-erőforrásokhoz, és az Azure AD meg ajánlott megközelítést hatékonyan rendszergazdai hozzáférés-kezelés és naplózás.

### <a name="reporting-and-monitoring"></a>Jelentéskészítés és a monitorozás

Azure ad-ben megadhat további betekintést nyerjen a szervezet felhasználói kiépítési használat és a vizsgálati naplók és jelentések működési állapotát. 

#### <a name="application-audit-logs"></a>Alkalmazások auditnaplói

Ezek a naplók részletes információkat talál az alkalmazásproxyt, valamint az eszköz és a felhasználó az alkalmazás elérésének konfigurált alkalmazások a bejelentkezéseket. Azok az Azure Portalon, és a naplózási API-ban található.

#### <a name="windows-event-logs-and-performance-counters"></a>Windows biztonságiesemény-naplóinak és teljesítményszámlálók

Összekötők rendelkezik rendszergazdai és a munkamenet naplókat. A felügyeleti naplók a fontos eseményeket és azok hibákat tartalmaznak. A munkamenet-naplók tartalmazzák a tranzakciók és a feldolgozási adataikat. Naplók és -számlálók a Windows-eseménynaplók találhatók, és kövesse ezt [Eseménynapló adatforrások konfigurálása az Azure Monitor az oktatóanyag](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Hibaelhárítási útmutató és a lépések

További információ a gyakori problémák és a problémák megoldásához útmutatónk bemutatja, hogyan [hibaelhárítási](application-proxy-troubleshoot.md) hibaüzenetek. 

Ezek a cikkek gyakori forgatókönyveket fednek le, de a saját hibaelhárítási útmutatókat is létrehozhat támogatási szervezete számára. 

* [Probléma az alkalmazáslap megjelenítésekor](application-proxy-page-appearance-broken-problem.md)
* [Az alkalmazás betöltési ideje túl hosszú](application-proxy-page-load-speed-problem.md)
* [Az alkalmazáslap hivatkozásai nem működnek](application-proxy-page-links-broken-problem.md)
* [Milyen portokat nyissak meg az alkalmazáshoz](application-proxy-connectivity-ports-how-to.md)
* [Az alkalmazás összekötőcsoportjában nem volt működő összekötő](application-proxy-connectivity-no-working-connector.md)
* [Konfigurálás a felügyeleti portálon](application-proxy-config-how-to.md)
* [Egyszeri bejelentkezés beállítása az alkalmazáshoz](application-proxy-config-sso-how-to.md)
* [Probléma egy alkalmazás felügyeleti portálon való létrehozásakor](application-proxy-config-problem.md)
* [A Kerberos által korlátozott delegálás konfigurálása](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurálás a PingAccess segítségével](application-proxy-back-end-ping-access-how-to.md)
* [Nem lehet hozzáférni a vállalati alkalmazás hiba](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor](application-proxy-connector-installation-problem.md)
* [Bejelentkezési hiba](application-sign-in-problem-on-premises-application-proxy.md)
