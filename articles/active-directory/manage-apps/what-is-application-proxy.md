---
title: Az Azure AD-alkalmazásproxy a helyszíni alkalmazások közzététele
description: Ennek megértéséhez kívülről a távoli felhasználók számára a helyszíni webalkalmazások közzétételéhez Application Proxy használatára. Ismerje meg az alkalmazásproxy architektúra, összekötők, hitelesítési módszerek és biztonsági funkcióit.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 05/31/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f23b20d460952ae582c292c8015851b9dc2ea98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108162"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Közzététele az Azure AD-alkalmazásproxy használatával a helyszíni alkalmazások távoli felhasználók számára

Az Azure Active Directory (Azure AD) a védelme érdekében a felhasználók, alkalmazások és adatok felhőbeli és helyszíni számos funkciókat kínál. Elsősorban az Azure AD-alkalmazásproxy szolgáltatás informatikai szakemberek számára, akik kívülről a helyszíni webalkalmazások közzétételéhez implementálható. Távoli felhasználók, akik hozzáférhetnek a belső alkalmazásokat is elérhesse őket biztonságos módon.

A hálózaton kívülről származó belső alkalmazások biztonságos hozzáférést még több kritikus fontosságú lesz a modern munkahelyeken. Forgatókönyvek, például a BYOD (saját eszközök használata) és a mobil eszközök, az informatikai szakemberek számára két célok teljesítéséhez merül fel:

* Lehetővé teheti a végfelhasználók számára, hogy hatékonyan bármikor és bárhol
* Vállalati eszközök védelme mindig

Számos vállalat úgy véli, a felügyeletet, és a védett, amikor a vállalati hálózatok határain belül található erőforrásokat. De a mai digitális munkaterületen, a határ bővített felügyelt mobil eszközök és erőforrások és szolgáltatások a felhőben. Most meg kell felügyelheti a a felhasználói identitások és azok az eszközök és alkalmazások-on tárolt adatok védelme.

Talán már használja az Azure AD a felhőben, hogy az Office 365-höz és más SaaS-alkalmazásokhoz, valamint webes üzemeltetett alkalmazások a helyszíni elérni kívánó felhasználók kezeléséhez. Ha már rendelkezik Azure ad-ben, mint egy vezérlősík zökkenőmentes és biztonságos hozzáférést a helyszíni alkalmazások kihasználhatja. Vagy esetleg továbbra is Ön fontolgató a felhőre. Ha igen, elkezdheti fel Önt a felhőre való megvalósítása alkalmazásproxy és a egy erős identity foundation létrehozásához az első lépést.

Amíg nem átfogó, az alábbi lista mutatja be néhány App Proxy végrehajtási hibrid együttes használata esetén engedélyezheti:

* A helyszíni webalkalmazások külsőleg közzététele egy egyszerűsített módon, anélkül, hogy DMZ-t
* Támogatja az egyszeri bejelentkezés (SSO) eszközök, erőforrások és a felhőbeli és helyszíni alkalmazások
* Többtényezős hitelesítés a felhőben és helyszíni alkalmazások támogatása
* Felhőalapú szolgáltatások biztonsága a Microsoft Cloud használhatja
* Felhasználóifiók-kezelés központosítása
* Központosíthatja az identitás- és biztonsági ellenőrzése
* Automatikusan hozzáad vagy eltávolít a csoport tagsága alapján alkalmazásokhoz való felhasználói hozzáférés

Ez a cikk ismerteti, hogy az Azure AD és az alkalmazásproxy engedélyezheti a távoli felhasználók egy egyszeri bejelentkezéssel (SSO) felhasználói élményt. Felhasználók biztonságosan csatlakozhat a helyszíni alkalmazások VPN vagy kettős többcímes kiszolgálók és -tűzfalszabályok nélkül. Ez a cikk segít megérteni, hogyan alkalmazásproxy biztosít a képességek és a felhő előnyeinek a helyileg üzemeltetett webes alkalmazások. Az architektúra és a lehetséges topológiák is ismerteti.

## <a name="remote-access-in-the-past"></a>Távoli elérés az elmúlt

A belső erőforrások védelme a támadók miközben elősegíti a távoli felhasználók hozzáférjenek a vezérlősík korábban az összes a DMZ-t vagy a peremhálózaton. De a VPN- és a külső ügyfelek által a vállalati erőforrások eléréséhez használt DMZ-ben üzembe helyezett fordított proxy megoldások nem alkalmas a felhőalapú világban. Ezek általában a következő hátrányokkal tapasztalható:

* Hardverköltségek
* Fenntartása a biztonsági (javítása, a figyelési portokat, stb.)
* A peremhálózaton felhasználók hitelesítése
* A peremhálózat között, felhasználók hitelesítése
* A távoli felhasználók számára a telepítési és a VPN-ügyfélszoftverének konfigurálásához VPN-hozzáférés fenntartása. Ezenkívül karbantartása tartományhoz csatlakoztatott kiszolgálók a DMZ-t, amely érinti a külső támadásoknak lehet.

Napjaink felhő-és felhőközpontú világában a Azure AD egy szabályozni, és mi lekérdezi a hálózatban a legmegfelelőbb. Az Azure AD-alkalmazásproxy integrálható a modern hitelesítést és a felhőalapú technológiák, például a SaaS-alkalmazások és az identitás-szolgáltatóktól. Ez az integráció lehetővé teszi, hogy a felhasználók számára az alkalmazások bárhonnan elérheti. Nem csak az alkalmazásproxy több olyan mai digitális munkahelyi, biztonságos, mint a VPN- és a fordított proxy megoldásokat, és könnyebben. A távoli felhasználók férnek hozzá Office 365 és más SaaS-alkalmazások az Azure AD-vel integrált ugyanúgy hozzáférhet a helyszíni alkalmazások. Nem kell módosítani vagy frissíteni az alkalmazások a proxyval működjenek. Ezenkívül azt App Proxy nem igényel, meg kell nyitni bejövő kapcsolatok a tűzfalon keresztül. Az App proxyval egyszerűen állítsa be, és felejtse el azt.

## <a name="the-future-of-remote-access"></a>A távelérés a jövőben

A mai digitális munkaterület, a felhasználók dolgozhatnak, bárhol több alkalmazások és eszközök. Csak állandó felhasználói identitást. Ezért az első lépése egy biztonságos hálózati még ma, hogy használja [az Azure AD identitáskezelési](https://docs.microsoft.com/azure/security/security-identity-management-overview) lehetőségeket, mint a biztonsági vezérlősík. Egy olyan modell, az identitást használja, a vezérlősík általában az alábbi összetevőkből áll:

* Egy identitásszolgáltatóhoz való nyomon követheti, felhasználók és a felhasználóval kapcsolatos információkat.
* Eszköz könyvtár fenntartásához a vállalati erőforrásokhoz való hozzáféréssel rendelkező eszközök listáját. Ez a könyvtár tartalmazza a megfelelő eszköz adatai (írja be például a eszköz, integritás stb.).
* Házirend kiértékelése szolgáltatás határozza meg, hogy egy felhasználó és eszköz megfelel-e a biztonsági rendszergazda által beállított szabályzat.
* Engedélyezheti vagy megtagadhatja a szervezeti erőforrásokhoz való hozzáférés lehetővé teszi.

A proxyval az Azure AD biztosítja, hogy nyomon követése a webes alkalmazások közzétett helyszíni elérni kívánó felhasználók és a felhőben. Központi felügyeleti pontot biztosítja azokat az alkalmazásokat. Bár nem kötelező, ajánlott is engedélyezheti az Azure AD feltételes hozzáférés. Hogyan felhasználók hitelesítve szereznek hozzáférést feltételeinek meghatározásával, további rendelkezzenek a megfelelő személyeknek alkalmazásokhoz való hozzáférést.

**Megjegyzés:** Fontos megérteni, hogy az Azure AD-alkalmazásproxy célja az, egy VPN- vagy fordított proxy helyettesítő központi (vagy távoli) felhasználók, akik hozzáférhetnek a belső erőforrásokat. Nem célja a belső felhasználók számára a vállalati hálózaton. Belső felhasználók, akik szükségtelenül használja az Application Proxy bevezethet váratlan és nem kívánatos teljesítménybeli problémák.

![Az Azure Active Directory és az összes alkalmazás](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Alkalmazásproxy működésének áttekintése

Az alkalmazásproxy egy Azure AD szolgáltatás konfigurálása az Azure Portalon. Lehetővé teszi, hogy egy külső nyilvános HTTP/HTTPS URL-végpontjának közzététele az Azure felhőben, amely csatlakozik egy belső alkalmazás URL-címe a szervezetben. Ezek a helyszíni webalkalmazások integrálhatók az Azure AD egyszeri bejelentkezés támogatására. A végfelhasználók számára hozzáférést a helyszíni webalkalmazásokhoz férnek hozzá az Office 365 és más SaaS-alkalmazások azonos módon.

Ez a szolgáltatás-összetevők közé tartozik az alkalmazásproxy-szolgáltatás, amely a felhőben, az Application Proxy connector, amely egy egyszerűsített ügynököt futtat egy helyszíni kiszolgáló és az Azure AD, amely az identitásszolgáltató. Mindhárom összetevő együttműködve biztosítják az egyszeri bejelentkezés használatát a felhasználók férhetnek hozzá a helyszíni webalkalmazásokhoz.

A bejelentkezést követően a külső felhasználók férhetnek hozzá a helyszíni webalkalmazások egy jól ismert URL-cím használatával, vagy a [MyApps hozzáférési panel](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) asztali vagy iOS és MAC-eszközök. Például App Proxy távoli hozzáférést biztosíthat, és egyszeri bejelentkezést az távoli asztal, a SharePoint helyek, a Tableau, Qlik, Outlook, a webes és az üzletági (LOB) alkalmazások.

![Az Azure AD-alkalmazásproxy architektúrája](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Hitelesítés

Többféleképpen is konfigurálhatja az egyszeri bejelentkezés egy alkalmazás, és az itt választott függ a hitelesítést az alkalmazás használja. Az alkalmazásproxy támogatja a következő típusú alkalmazásokat:

* Webalkalmazások
* Webes API-kat, hogy a különböző eszközökön részletgazdag alkalmazásokat közzétenni kívánt
* Távoli asztali átjáró mögött üzemeltetett alkalmazások
* Gazdag ügyfél alkalmazások integrált a az Active Directory Authentication Library (ADAL)

Alkalmazásproxy működik együtt a következő hitelesítési protokollt használó alkalmazások:

* **[Integrált Windows-hitelesítés (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** IWA az alkalmazásproxy-összekötők használatával a Kerberos által korlátozott delegálás (KCD) hitelesítheti a felhasználókat a Kerberos-alkalmazáshoz.

Alkalmazásproxy is támogatja az alábbi hitelesítési protokollokat, harmadik féltől származó integrációs vagy az adott konfigurációs forgatókönyvek:

* [**Fejlécalapú hitelesítéskor**](application-proxy-configure-single-sign-on-with-ping-access.md). A bejelentkezési módszer PingAccess nevű harmadik fél hitelesítési szolgáltatást használ, és használatos, ha az alkalmazás fejléceket használja a hitelesítéshez. Ebben a forgatókönyvben a PingAccess authentication kezeli.
* [**Forms - és jelszóalapú hitelesítés**](application-proxy-configure-single-sign-on-password-vaulting.md). Ez a hitelesítési módszer a felhasználók jelentkezzen be az alkalmazást az első használatakor a felhasználónév és jelszó. Miután az első bejelentkezés Azure ad-ben a felhasználónevet és jelszót, hogy az alkalmazás választékát kínálja. Ebben a forgatókönyvben az Azure AD authentication kezeli.
* [**SAML-hitelesítés**](application-proxy-configure-single-sign-on-on-premises-apps.md). SAML-alapú egyszeri bejelentkezés az SAML 2.0 vagy WS-Federation protokollt használó alkalmazások esetében támogatott. SAML egyszeri bejelentkezéshez, az Azure AD akkor hitelesíti az alkalmazásnak a felhasználó Azure AD-fiók használatával.

Támogatott módszerekkel kapcsolatos további információkért lásd: [egyszeri bejelentkezési módszer kiválasztása](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Biztonsági előnyeit

Az Application Proxy és az Azure AD által kínált távelérési megoldás támogatja az ügyfelek előfordulhat, hogy kihasználásához, beleértve az olyan biztonsági előnyöket:

* **Hitelesített hozzáférés**. Proxy-alkalmazások közzététele a legmegfelelőbb egy [előhitelesítés](application-proxy-security.md#authenticated-access) annak érdekében, hogy csak a hitelesített kapcsolatokhoz eléri-e a hálózaton. Az előhitelesítést közzétett alkalmazások esetében az alkalmazásproxy-szolgáltatás a helyszíni környezetben, érvényes token nélkül továbbítja a forgalom nem engedélyezett. Előhitelesítés, rendkívül tárhelyigényes, letiltja a célzott támadások elhárításához, jelentős mennyiségű, csak hitelesített identitást is hozzáférni a háttéralkalmazás.
* **Feltételes hozzáférés**. Gazdagabb szabályzati vezérlőket is alkalmazható, előtt a hálózati kapcsolatot létesít. A feltételes hozzáférés, korlátozásokat definiálhat a forgalmat, amely engedélyezi a a háttéralkalmazás megérintve. Létrehozhat házirendeket, amelyek korlátozzák a bejelentkezések alapján a hely, a hitelesítés és felhasználói kockázatú profil erősségét. Módon alakul ki a feltételes hozzáférés, további vezérlők jelennek meg a Microsoft Cloud App Security (MCAS) ilyen az integráció a fokozott biztonság érdekében. MCAS integrációja lehetővé teszi, hogy a helyszíni alkalmazás konfigurálása [valós idejű figyelés](application-proxy-integrate-with-microsoft-cloud-application-security.md) monitorozását és ellenőrzését feltételes hozzáférés használatával valós idejű munkamenetek feltételes hozzáférési szabályzatok alapján.
* **Forgalom-lezárást**. A háttéralkalmazás felé irányuló összes forgalma, a felhőben az alkalmazásproxy-szolgáltatás leállítása közben a munkamenet újra létrejön a háttérkiszolgálón a. Ez a kapcsolat stratégia azt jelenti, hogy a kiszolgálók nem jelennek meg a közvetlen HTTP-forgalmat a háttérbeli. Jobban szembeni védelem érdekében célzott DoS (--szolgáltatásmegtagadásos) támadásokat, mert a tűzfal nem támadás alatt áll.
* **Az összes kimenő csak**. Az alkalmazásproxy-összekötők csak akkor használja a kimenő kapcsolatokat az alkalmazásproxy-szolgáltatás a felhőben 80-as és 443-as portokon keresztül. A kimenő kapcsolatot sem hiba esetén nem kell tűzfalportokat a bejövő kapcsolatok vagy összetevők megnyitásához a DMZ-ben. Az összes kapcsolat kimenő és a egy biztonságos csatornán keresztül is.
* **Biztonsági elemzés és a Machine Learning (gépi tanulás) alapú intelligencia**. Azure Active Directory része, mert az alkalmazásproxy kihasználhatják [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview) (igényel [prémium P2 szintű licencelési](https://azure.microsoft.com/pricing/details/active-directory/)). Az Azure AD Identity Protection biztonsági intelligencia gépi tanulási ötvözi a Microsoft az adatcsatornák [digitális bűnözés elleni Egységünkkel](https://news.microsoft.com/stories/cybercrime/index.html) és [Microsoft Security Response Center](https://www.microsoft.com/msrc) proaktív azonosításához sérült biztonságú fiókok. Identity Protection magas kockázatú bejelentkezések valós idejű védelmet biztosít. Vesz figyelembe tényezőket, például a fertőzött eszközökről névtelenítését hálózatokon keresztül vagy szokatlan, és nem valószínű helyekről hozzáfér növelheti a kockázatú profil, a munkamenet. Valós idejű védelem a kockázatú profil használható. Ezeket a jelentéseket és események számos már az SIEM-rendszerekkel való integráció az API-n keresztül érhető el.

* **A távelérési szolgáltatás**. Nem kell aggódnia a karbantartásáért, valamint távelérés engedélyezése a helyszíni kiszolgálók javítása. Proxy egy olyan internetes szolgáltatás méretezése, amely a Microsoft tulajdonában van, így mindig a legújabb biztonsági javításokat és frissítéseket. Veszéllyel szoftver továbbra is nagyszámú támadások számlák. Megfelelően a részleg haza védelmi, ahány [célzott támadások elhárításához 85 %-os rendszer vállalata](https://www.us-cert.gov/ncas/alerts/TA15-119A). A szolgáltatás-modell nem kell többé az edge-kiszolgálók kezelése nagy terheket, és javítására őket igény szerint kódolásához.

* **Intune-integráció**. Az Intune-nal vállalati adatforgalmat külön a személyes forgalmat. Az alkalmazásproxy biztosítja, hogy a vállalati forgalom hitelesítése. [Proxy és az Intune Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) képesség is használható együtt engedélyezze a távoli felhasználók biztonságosan férhetnek hozzá az iOS és Android-eszközök belső webhelyeket.

### <a name="roadmap-to-the-cloud"></a>Ütemterv a felhőbe

Application Proxy végrehajtási egy másik fő előnye kiterjeszti az Azure AD a helyszíni környezetbe. Alkalmazásproxy végrehajtási valójában egy kulcsfontosságú lépés a szervezet és az alkalmazások áthelyezése a felhőbe. A felhőben és a helyszíni hitelesítési váltással csökkenti a helyszíni adatmennyiséget, és használja az Azure AD identitáskezelési képességeket kínál a vezérlősík. A minimális vagy a meglévő alkalmazások frissítések, rendelkezik hozzáféréssel a felhőalapú képességek, például az egyszeri bejelentkezést, többtényezős hitelesítést, és a központi felügyeleti. A szükséges összetevők telepítése az App proxyval való létrehozásához szükséges egy távelérési keretrendszer egyszerű folyamat során a rendszer. És a felhőbe való áthelyezésével elérheti a legújabb Azure AD-funkciók, a frissítések és a funkciókat, például a magas rendelkezésre állás és a vészhelyreállítás.

Az alkalmazások Azure ad-ben történő áttelepítéssel kapcsolatos további tudnivalókért tekintse meg a [az alkalmazások áttelepítése az Azure Active Directoryhoz](https://aka.ms/migrateapps/whitepaper) tanulmányt.

## <a name="architecture"></a>Architektúra

A következő ábra szemlélteti az általános Azure AD-hitelesítési szolgáltatások és az alkalmazásproxy munkahelyi együtt történő egyszeri bejelentkezéshez, adja meg a helyi alkalmazásokat a végfelhasználók számára.

![Az Azure AD-alkalmazásproxy hitelesítési folyamat](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Miután a felhasználó az alkalmazás érhető el egy végponton keresztül, a rendszer átirányítja a felhasználót az Azure AD bejelentkezési oldal. Ha feltételes hozzáférési szabályzatok konfigurálta, az egyes esetekben ellenőrzi annak érdekében, hogy megfelelnek a szervezet biztonsági követelményeinek a jelenleg.
2. A sikeres bejelentkezést követően az Azure AD elküldi egy jogkivonatot a felhasználó ügyféleszközön.
3. Az ügyfél elküldi az alkalmazásproxy-szolgáltatás, amely lekéri az egyszerű felhasználónév (UPN) és a rendszerbiztonsági tag nevének (SPN) a jogkivonatot.
4. Az alkalmazásproxy továbbítja a kérést, amely a Proxy átveszi [összekötő](application-proxy-connectors.md).
5. Az összekötő a felhasználó nevében szükséges további hitelesítést végez (*hitelesítési módszertől függően nem kötelező*), a belső végpont az alkalmazáskiszolgáló kér, és elküldi a kérelmet a helyszíni az alkalmazás.
6. A kiszolgáló válasza az alkalmazásproxy-szolgáltatás, az összekötőn keresztül zajlik.
7. A válasz az alkalmazásproxy-szolgáltatás is küld a felhasználónak.

|**Összetevő**|**Leírás**|
|:-|:-|
|Végpont|A végpont URL-címe, vagy egy [végfelhasználói portál](end-user-experiences.md). Felhasználók által egy külső URL-cím elérése a hálózatán kívüli alkalmazások érhető el. A hálózaton belüli felhasználók elérhetik az alkalmazást egy URL-címet vagy egy végfelhasználói portálon keresztül. Amikor a felhasználók nyissa meg a végpontok egyikéhez, hitelesítéséhez az Azure ad-ben, és majd az összekötőt a helyszíni alkalmazások kerülnek.|
|Azure AD|Az Azure AD végrehajtja a hitelesítést, a bérlő címtárát a felhőben tárolt használatával.|
|Application Proxy szolgáltatás|Az alkalmazásproxy-szolgáltatás fut a felhőben az Azure AD részeként. Adja át, a bejelentkezési token a felhasználótól az alkalmazásproxy-összekötő. Proxy bármely elérhető-e a kérelem fejlécei továbbítja, és beállítja a fejlécek alapján a protokoll, az ügyfél IP-címre. Ha a bejövő kéréseket a proxy már, hogy a fejléc, az ügyfél IP-cím hozzáadódik a vesszővel tagolt lista, amely a fejléc értéke végére.|
|Alkalmazásproxy-összekötő|Az összekötő egy egyszerűsített, a hálózaton belüli Windows Serveren futó ügynök. Az összekötő a felhőben az alkalmazásproxy-szolgáltatás és a helyszíni alkalmazások közötti kommunikációt kezeli. Az összekötő kimenő kapcsolatok csak akkor használja, így nem kell minden bemenő portokat nyitni, vagy helyezze semmit a DMZ-t. Az összekötők állapot nélküli, és kérje le adatokat a felhőben, igény szerint. További információ az összekötők, többek között azok terheléselosztás és hitelesítéséhez, lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](application-proxy-connectors.md).|
|Az Active Directory (AD)|Az Active Directory fut, a helyszíni tartományi fiókok esetében a hitelesítés végrehajtásához. Ha az egyszeri bejelentkezés konfigurálva van, az összekötő végrehajtásához szükséges további hitelesítést AD kommunikál.|
|Helyszíni alkalmazás|Végül a felhasználó nem érhetik el a helyszíni alkalmazások.|

Az Azure AD-alkalmazásproxy a felhő alapú alkalmazásproxy-szolgáltatás és a egy a helyszíni összekötő áll. Az összekötő az alkalmazásproxy-szolgáltatás kéréseit figyeli, és kezeli a kapcsolatokat a belső alkalmazások. Fontos megjegyezni, hogy minden kommunikáció SSL-en keresztül történik, és mindig a az alkalmazásproxy-összekötő származnak. Ez azt jelenti, hogy kommunikációit a kimenő csak. Az összekötő egy ügyfél-tanúsítványt használ az alkalmazásproxy-szolgáltatás összes híváshoz a hitelesítéséhez. A kapcsolat biztonsági az egyetlen kivétel, a kezdeti beállítás lépés, ahol az ügyféltanúsítvány létrejött. Tekintse meg a Proxy [technikai részletek](application-proxy-security.md#under-the-hood) további részletekért.

### <a name="application-proxy-connectors"></a>Alkalmazásproxy-összekötők

[Alkalmazásproxy-összekötők](application-proxy-connectors.md) egyszerűsített telepített ügynökök a helyszíni, amelyek elősegítik a kimenő kapcsolatot a felhőben az alkalmazásproxy-szolgáltatás. Az összekötők egy olyan Windows Serveren, amely hozzáfér a háttéralkalmazás telepítve kell lennie. Felhasználók kapcsolódhatnak az alkalmazásproxy felhőalapú szolgáltatás, hogy azok az alkalmazások keresztül használható összekötők forgalmát az alábbi ábra szemlélteti útvonalakat.

![Az Azure AD-alkalmazásproxy hálózati kapcsolatok](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

A telepítő és a egy összekötőt és az alkalmazásproxy szolgáltatás közötti regisztrációs módon történik:

1. A rendszergazda megnyitja a 80-as és 443-as kimenő forgalmat, és lehetővé teszi több URL-címek, amelyek az összekötőt, az alkalmazásproxy-szolgáltatás és az Azure AD számára szükséges hozzáférést.
2. A rendszergazda jelentkezik be az Azure Portalon, és telepítse az összekötőt egy helyszíni Windows server egy végrehajtható fájl futtatása.
3. Az összekötő kezd el "figyelő", az alkalmazásproxy-szolgáltatás.
4. A rendszergazda ad hozzá a helyszíni alkalmazások az Azure ad-hez, és azokat a beállításokat konfigurálja, mint például az alkalmazások csatlakoztatni kell az URL-címek felhasználók.

További információkért lásd: [Azure AD-alkalmazásproxy központi telepítésének megtervezése](application-proxy-deployment-plan.md).

Javasoljuk, hogy mindig telepít a redundancia és a méretezési csoport több összekötőt. Az összekötők, a szolgáltatással együtt gondoskodik a magas rendelkezésre állású-feladatokat, és képes hozzáadásának vagy eltávolításának dinamikusan. Minden alkalommal, amikor új kérelem érkezik, irányítja a rendszer az elérhető összekötők egyike. Amikor fut egy összekötőt, addig marad aktív, csatlakozik a szolgáltatáshoz. Ha egy összekötő átmenetileg nem érhető el, ezt a forgalmat nem válaszol. A fel nem használt összekötők vannak megjelölve inaktívként, és 10 nap inaktivitás után eltávolítja.

Összekötők is lekérdezi a kiszolgálót, hogy ismerje meg, hogy van-e az összekötő újabb verziója. Manuális frissítés teheti meg, bár a összekötők automatikusan frissül mindaddig, amíg a Alkalmazásproxyösszekötő szolgáltatást futtató. A bérlők számára a több összekötőt az automatikus frissítések minden csoport a környezetében az állásidő elkerülése érdekében egyszerre egy összekötő célként.

**Megjegyzés**: Nyomon követheti a Proxy [verzió Előzmények lapon](application-proxy-release-version-history.md) értesítést frissítések jelentek meg az RSS-hírcsatornában való feliratkozással.

Minden Application Proxy connector hozzá van rendelve egy [összekötőcsoport](application-proxy-connector-groups.md). Az összekötő ugyanabban a csoportban összekötők jár el a magas rendelkezésre álláshoz egyetlen egységként, és a terheléselosztás. Létrehozhat új csoportokat, összekötők hozzájuk rendelhet az Azure Portalon, majd rendelje hozzá az egyedi összekötők adott alkalmazások kiszolgálására. Azt javasoljuk, hogy minden egyes összekötőcsoport magas rendelkezésre állás érdekében legalább kettő összekötőt rendelkezik.

Összekötőcsoportok akkor hasznos, ha szüksége támogatásra a következő esetekben:

* Földrajzi alkalmazások közzététele
* Szegmentálás/alkalmazáselszigetelés
* A felhőben vagy a helyszínen futó webes alkalmazások közzététele

További információ az összekötők telepítése és a hálózat optimalizálása helyének megválasztásához [Azure Active Directory Application Proxy használata esetén a hálózati topológiai szempontok a](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Más használati eseteket

Eddig a pontig éppen alkalmazásproxy használatával a helyszíni alkalmazások külsőleg közzétételéhez egyszeri bejelentkezés a felhőbeli és helyszíni alkalmazásokra engedélyezése közben. Azonban nincsenek más használati eseteket, az alkalmazás Proxy, amely érdemes megemlíteni. Ezek a következők:

* **REST API-k biztonságos közzétételét**. Üzleti logika vagy rendelkezik futó API-k a helyszíni vagy virtuális gépeken a felhőben üzemeltetett, az alkalmazásproxy nyilvános végpontot biztosít API-hozzáférés. API-végpont hozzáférés lehetővé teszi vezérlő hitelesítési és engedélyezési anélkül, hogy a bejövő portokat. További biztonsági, például a többtényezős hitelesítés és az eszközalapú feltételes hozzáférés prémium szintű Azure AD-funkciók révén biztosítja a asztali számítógépek, iOS, MAC és Android-eszközök Intune-nal. További tudnivalókért lásd: [kommunikál a proxy-alkalmazások natív ügyfélalkalmazások engedélyezése](application-proxy-configure-native-client-application.md) és [OAuth 2.0 segítségével az Azure Active Directory és az API Management API-k védelme](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad).
* **A távoli asztali szolgáltatások** **(RDS)** . Standard RDS központi telepítések igénylik nyissa meg a bejövő kapcsolatokat. Azonban a [az alkalmazásproxy használatával a távoli asztali szolgáltatások telepítési](application-proxy-integrate-with-remote-desktop-services.md) állandó kimenő kapcsolat az összekötő szolgáltatást futtató kiszolgáló tartozik. Ezzel a módszerrel elérhetővé teheti a végfelhasználók több alkalmazás közzététele a helyszíni alkalmazások távoli asztali szolgáltatások használatával. Csökkentheti a támadási felületének az üzembe helyezés a kétlépéses ellenőrzés és a feltételes hozzáférés-vezérlés korlátozott számú RDS
* **Tegye közzé az alkalmazásokat, amelyek a websockets protokoll használatával csatlakoznak**. A támogatási [Qlik Sense](application-proxy-qlik.md) nyilvános előzetes verzióban érhető el, és a jövőben kibomlik más alkalmazások.
* **Együttműködhet a proxy-alkalmazások natív ügyfélalkalmazások engedélyezése**. Az Azure AD-alkalmazásproxy használatával webalkalmazásokat tesz közzé, de azt is használható közzététele [natív ügyfélalkalmazások](application-proxy-configure-native-client-application.md) konfigurált és az Azure AD Authentication Library (ADAL). Natív ügyfélalkalmazások eltérnek a web apps, mert vannak telepítve az eszközön, amíg a böngészőn keresztül elért webalkalmazások.

## <a name="conclusion"></a>Összegzés

A módszer munka- és az általunk használt eszközök gyorsan változnak. További a dolgozók saját eszközöket és -as-szoftverszolgáltatások (SaaS) alkalmazások általánosan elterjedt használatát a módon szervezetek kezelése és biztonságos adataikat kell is fejlesztheti tovább. Vállalatok már nem kizárólag a saját falak egy jele körüli azok szegély moat által védett belüli működésre. Adatok keresztben választ minden eddiginél – további helyeket, a helyszíni és felhőalapú környezetekről. A fejlődést szem előtt tartva segített felhasználók hatékonyságot és az együttműködés lehetőségét, de azt is lehetővé teszi történő védelmét bizalmas adatok nehéz lehet.

Az Azure AD jelenleg használt hibrid együttes használata esetén a felhasználók kezeléséhez, vagy fel Önt a felhőre való kezdődően érdeklik, megvalósítása az Azure AD-alkalmazásproxy segít minimálisra csökkenteni a helyszíni erőforrásigényét azáltal, hogy távoli mérete szolgáltatásként elérhető.

Szervezetek kell kezdődnie, kihasználhatja a alkalmazásproxyval még ma kihasználásához a következő előnyökkel jár:

* A helyszíni alkalmazások külsőleg közzététele nélkül fenntartja a hagyományos VPN- vagy más helyszíni webes közzététel megoldásokat és szegélyhálózat (DMZ) megközelítés a kapcsolódó terhelés
* Egyszeri bejelentkezés minden alkalmazásra, legyenek azok az Office 365 vagy más SaaS-alkalmazások és többek között a helyszíni alkalmazások
* Ahol kihasználja az Azure ad-ben a Office 365 telemetriai adatokat a jogosulatlan hozzáférés megakadályozása érdekében méretezési felhőbiztonság
* Az Intune integrációjának biztosítása a vállalati forgalom hitelesítése
* Felhasználói fiók felügyeleti központosítás
* Ügyeljen arra, hogy az automatikus frissítések rendelkezik a legújabb biztonsági javítások
* Új funkciók azok kiadott; a legutóbbi, folyamatban van a SAML egyszeri bejelentkezés támogatása és a részletes felügyeleti alkalmazás cookie-k

## <a name="next-steps"></a>További lépések

* Tervezésével kapcsolatos információkat, üzemeltetési és kezelése az Azure AD-alkalmazásproxy lásd [Azure AD-alkalmazásproxy központi telepítésének megtervezése](application-proxy-deployment-plan.md).
* Egy élő bemutatót ütemezni, vagy a tesztelési 90 napos ingyenes próbaverzió beszerzése, lásd: [Enterprise Mobility + Security – első lépések](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
