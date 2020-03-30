---
title: Azure Active Directory alkalmazásproxy-telepítés megtervezése
description: Végpontok között útmutató az alkalmazásproxy szervezeten belüli telepítésének megtervezéséhez
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330911"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Azure AD alkalmazásproxy-telepítés megtervezése

Az Azure Active Directory (Azure AD) alkalmazásproxy egy biztonságos és költséghatékony távelérési megoldás a helyszíni alkalmazásokhoz. Azonnali átmeneti útvonalat biztosít a "Cloud First" szervezetek számára a modern protokollok használatára még nem képes örökölt helyszíni alkalmazásokhoz való hozzáférés kezeléséhez. További bevezető információkért lásd: [Mi az alkalmazásproxy.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)

Alkalmazásproxy ajánlott, hogy a távoli felhasználók hozzáférjenek a belső erőforrásokhoz. Az alkalmazásproxy felváltja a távoli elérésű használati esetekhez szükséges VPN- vagy fordított proxyt. Nem a vállalati hálózaton lévő felhasználók számára készült. Ezek a felhasználók, akik az alkalmazásproxyintranetes hozzáférést használják, nemkívánatos teljesítményproblémákat tapasztalhatnak.

Ez a cikk tartalmazza az Azure AD alkalmazásproxy megtervezéséhez, működtetéséhez és kezeléséhez szükséges erőforrásokat. 

## <a name="plan-your-implementation"></a>Tervezze meg a megvalósítást

A következő szakasz átfogó képet nyújt a legfontosabb tervezési elemekről, amelyek hatékony üzembe helyezési élményt biztosítanak. 

### <a name="prerequisites"></a>Előfeltételek

A megvalósítás megkezdése előtt meg kell felelnie az alábbi előfeltételeknek. Ebben az [oktatóanyagban](application-proxy-add-on-premises-application.md)további információkat talál a környezet beállításáról, beleértve ezeket az előfeltételeket is.

* **Összekötők**: Az összekötők könnyű ügynökök, amelyeket a következőkre helyezhet üzembe:
   * Fizikai hardver a helyszínen
   * Bármely hipervizor megoldáson belül üzemeltetett virtuális gép
   * Az Azure-ban üzemeltetett virtuális gép az alkalmazásproxy-szolgáltatáshoz való kimenő kapcsolat engedélyezéséhez.

* További részletes áttekintésért [tekintse meg az Azure AD-alkalmazásproxy-összekötők](application-proxy-connectors.md) ismertetése című témakört.

     * Az összekötőgépeket az összekötők telepítése előtt [engedélyezni kell a TLS 1.2-höz.](application-proxy-add-on-premises-application.md)

     * Ha lehetséges, telepítse az összekötőket ugyanabban a [hálózatban](application-proxy-network-topology.md) és szegmensben, mint a háttér-webalkalmazás-kiszolgálók. A legjobb, ha az alkalmazások felderítése után telepíti az összekötőket.
     * Azt javasoljuk, hogy minden összekötő csoport rendelkezik legalább két összekötők magas rendelkezésre állás és a méretezés. A három csatlakozó optimális abban az esetben, ha bármikor szervizelnie kell egy gépet. Tekintse át az [összekötő kapacitástábláját,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) hogy segítsen eldönteni, hogy milyen típusú gépre telepítse az összekötőket. Minél nagyobb a gép, annál több puffer és teljesítmény lesz a csatlakozó.

* **Hálózati hozzáférési beállítások:** Az Azure AD alkalmazásproxy-összekötők [HTTPS-en (TCP-port 443) és HTTP-n (TCP-port 80) keresztül csatlakoznak az Azure-hoz.](application-proxy-add-on-premises-application.md) 

   * Az összekötő TLS-forgalom befejezése nem támogatott, és megakadályozza, hogy az összekötők biztonságos csatornát hozzanak létre a megfelelő Azure App Proxy-végpontokkal.

   * Kerülje a szövegközi ellenőrzés minden formáját az összekötők és az Azure közötti kimenő TLS-kommunikációsorán. Az összekötő és a háttéralkalmazások közötti belső vizsgálat lehetséges, de csökkentheti a felhasználói élményt, és mint ilyen, nem ajánlott.

   * A csatlakozók terheléselosztása szintén nem támogatott, sőt nem is szükséges.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Fontos szempontok az Azure AD alkalmazásproxy konfigurálása előtt

Az Azure AD alkalmazásproxy konfigurálásához és megvalósításához a következő alapvető követelményeknek kell megfelelni.

*  **Azure-bevezetés:** Az alkalmazásproxy üzembe helyezése előtt a felhasználói identitásokat szinkronizálni kell egy helyszíni címtárból, vagy közvetlenül az Azure AD-bérlőkön belül kell létrehozni. Az identitás-szinkronizálás lehetővé teszi az Azure AD számára, hogy előzetesen hitelesítse a felhasználókat, mielőtt hozzáférést biztosítana számukra az App Proxy által közzétett alkalmazásokhoz, és hogy rendelkezzenek az egyszeri bejelentkezéshez szükséges felhasználói azonosító adatokkal.

* **Feltételes hozzáférési követelmények:** Nem javasoljuk az alkalmazásproxy intranetes hozzáféréshez való használatát, mert ez olyan késleltetést ad hozzá, amely hatással lesz a felhasználókra. Azt javasoljuk, hogy az alkalmazásproxy t használja az előhitelesítéshez és a feltételes hozzáférés házirendjeihez az internetről való táveléréshez.  Az intranetes használatra feltételes hozzáférést biztosító módszer az alkalmazások modernizálása, hogy azok közvetlenül hitelesíthessék magukat az AAD-vel. További információt [az alkalmazások AAD-ba való áttelepítéséhez szükséges erőforrások](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) című dokumentumban talál. 

* **Szolgáltatáskorlátok**: Az erőforrások egyes bérlők általi túlfogyasztása elleni védelem érdekében alkalmazásonként és bérlőnként szabályozási korlátok vannak beállítva. Ezek a korlátok az [Azure AD szolgáltatás korlátait és korlátozásait](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)olvassa el. Ezek a sávszélesség-szabályozási korlátok a szokásos használati mennyiség nél jóval magasabb teljesítményteszten alapulnak, és bőséges puffert biztosítanak a központi telepítések többségéhez.

* **Nyilvános tanúsítvány:** Ha egyéni tartományneveket használ, Be kell szereznie egy TLS/SSL tanúsítványt. A szervezeti követelményektől függően a tanúsítvány beszerzése eltarthat egy ideig, és azt javasoljuk, hogy a folyamatot a lehető leghamarabb kezdje el. Az Azure Application Proxy támogatja a szabványos, [helyettesítő vagy](application-proxy-wildcard.md)SAN-alapú tanúsítványokat. További információt az [Egyéni tartományok konfigurálása az Azure AD alkalmazásproxyval](application-proxy-configure-custom-domain.md)című témakörben talál.

* **Tartományi követelmények:** A Kerberos korlátozott delegálás (KCD) használatával a közzétett alkalmazásokba való egyszeri bejelentkezéshez az összekötőt futtató kiszolgálónak és az alkalmazást futtató kiszolgálónak tartományhoz kell csatlakoznia, és ugyanahhoz a tartományhoz vagy megbízó tartományhoz kell csatlakoznia.
A témával kapcsolatos részletes információkért lásd: [KCD az alkalmazásproxyval történő egyszeri bejelentkezéshez.](application-proxy-configure-single-sign-on-with-kcd.md) Az összekötő szolgáltatás a helyi rendszer környezetében fut, és nem kell beállítani, hogy egy egyéni identitás használata.

* **AZ URL-címek DNS-rekordjai**

   * Az egyéni tartományok alkalmazásproxyban való használata előtt létre kell hoznia egy CNAME rekordot a nyilvános DNS-ben, amely lehetővé teszi az ügyfelek számára, hogy az egyénileg definiált külső URL-címet az előre meghatározott alkalmazásproxy-címre oldják fel. Ha nem sikerül CNAME rekordot létrehozni egy egyéni tartományt használó alkalmazáshoz, az megakadályozza, hogy a távoli felhasználók csatlakozzanak az alkalmazáshoz. A CNAME rekordok hozzáadásához szükséges lépések DNS-szolgáltatónként eltérőek lehetnek, így [megtudhatja, hogyan kezelheti a DNS-rekordokat és rekordkészleteket az Azure Portal használatával.](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)

   * Hasonlóképpen az összekötő állomásoknak képesnek kell lenniük a közzétett alkalmazások belső URL-címének feloldására.

* **Rendszergazdai jogok és szerepek**

   * **Az összekötő telepítéséhez** helyi rendszergazdai jogokra van szükség ahhoz a Windows-kiszolgálóhoz, amelyen telepítve van. Emellett legalább egy *alkalmazás-rendszergazdai* szerepkört igényel a hitelesítéshez, és regisztrálja az összekötő példányt az Azure AD-bérlő. 

   * **Az alkalmazások közzétételéhez és felügyeletéhez** *az alkalmazás-rendszergazdai* szerepkör szükséges. Az alkalmazás-rendszergazdák kezelhetik a címtárban lévő összes alkalmazást, beleértve a regisztrációkat, az SSO-beállításokat, a felhasználói és csoport-hozzárendeléseket és a licencelést, az alkalmazásproxy-beállításokat és a hozzájárulást. Nem teszi lehetővé a feltételes hozzáférés kezelését. A *Felhőalkalmazás-rendszergazda* szerepkör az alkalmazás-rendszergazda összes képességével rendelkezik, azzal a különbséggel, hogy nem teszi lehetővé az alkalmazásproxy-beállítások kezelését.

* **Licencelés:** Az alkalmazásproxy egy Azure AD Premium-előfizetésen keresztül érhető el. Tekintse meg az [Azure Active Directory díjszabási lap](https://azure.microsoft.com/pricing/details/active-directory/) a licencelési lehetőségek és szolgáltatások teljes listáját.  

### <a name="application-discovery"></a>Alkalmazásfelderítés

Állítson össze leltárt az alkalmazásproxyn keresztül közzétett összes hatókörbe tartozó alkalmazásról a következő információk összegyűjtésével:

| Információ típusa| Összegyűjtésre szolgáló információ |
|---|---|
| Szolgáltatástípus| Például: SharePoint, SAP, CRM, Egyéni webalkalmazás, API |
| Alkalmazási platform | Például: Windows IIS, Apache Linuxon, Tomcat, NGINX |
| Tartományi tagság| A webkiszolgáló teljesen minősített tartományneve (FQDN) |
| Alkalmazás helye | Ha a webkiszolgáló vagy a farm az infrastruktúrában található |
| Belső hozzáférés | Az alkalmazás belső elérésekor használt pontos URL-cím. <br> Farm használata esetén milyen típusú terheléselosztás van használatban? <br> Azt jelzi, hogy az alkalmazás nem saját magából merít-e tartalmat.<br> Határozza meg, hogy az alkalmazás websocketeken keresztül működik-e. |
| Külső hozzáférés | A szállítói megoldás, amely az alkalmazás már ki van téve a külső. <br> A külső hozzáféréshez használni kívánt URL-cím. Ha a SharePoint, győződjön meg arról, hogy az alternatív hozzáférés-hozzárendelések konfigurálva vannak [ezen útmutató szerint.](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings) Ha nem, akkor meg kell határoznia a külső URL-eket. |
| Nyilvános tanúsítvány | Ha egyéni tartományt használ, szerezzen be egy megfelelő tulajdonosnevet. ha a tanúsítvány létezik, jegyezze fel a sorozatszámot és a helyet, ahonnan beszerezhető. |
| Hitelesítés típusa| Az alkalmazás támogatás által támogatott hitelesítés típusa, például alapszintű, Windows-integrációs hitelesítés, űrlapalapú, fejlécalapú és jogcímek. <br>Ha az alkalmazás úgy van beállítva, hogy egy adott tartományfiók alatt fusson, jegyezze fel a szolgáltatásfiók teljesen minősített tartománynevét (FQDN).<br> Ha SAML-alapú, az azonosító és a válasz URL-címei. <br> Ha fejlécalapú, a szállítói megoldás és a hitelesítéstípus kezeléséhez szükséges konkrét követelmény. |
| Összekötő csoport neve | A logikai neve az összekötők, hogy ki lesz jelölve, hogy a csatorna és az SSO a háttéralkalmazás. |
| Felhasználók/csoportok elérése | Azok a felhasználók vagy felhasználói csoportok, amelyek külső hozzáférést kapnak az alkalmazáshoz. |
| További követelmények | Vegye figyelembe az alkalmazás közzétételéhez figyelembe veendő további távelérési vagy biztonsági követelményeket. |

Letöltheti ezt az [alkalmazásleltár-táblázatot](https://aka.ms/appdiscovery) az alkalmazások leltározásához.

### <a name="define-organizational-requirements"></a>Szervezeti követelmények meghatározása

Az alábbiakban azokat a területeket kell meghatároznia, amelyekhez meg kell határoznia a szervezet üzleti követelményeit. Minden terület tartalmaz példákat a követelményekre

 **Hozzáférés**

* Távoli felhasználók tartományhoz csatlakozott, vagy az Azure AD-hez csatlakozó eszközök felhasználók biztonságosan hozzáférhetnek a közzétett alkalmazások zökkenőmentes egyszeri bejelentkezéssel (SSO).

* A jóváhagyott személyes eszközökkel rendelkező távoli felhasználók biztonságosan hozzáférhetnek a közzétett alkalmazásokhoz, feltéve, hogy regisztrálva vannak az MFA-ban, és a Microsoft Authenticator alkalmazást a mobiltelefonjukon hitelesítési módszerként regisztrálták.

**Kormányzás** 

* A rendszergazdák meghatározhatják és figyelhetik az alkalmazásproxyn keresztül közzétett alkalmazások felhasználói hozzárendeléseinek életciklusát.

**Biztonság**

* Csak a csoporttagságon vagy egyénileg az alkalmazásokhoz rendelt felhasználók férhetnek hozzá ezekhez az alkalmazásokhoz.

**Teljesítmény**

* Az alkalmazás teljesítménye nem csökkent a belső hálózatról való hozzáféréshez képest.

**Felhasználói élmény**

* A felhasználók tisztában vannak azzal, hogyan érhetik el alkalmazásaikat a jól ismert vállalati URL-ek használatával bármely eszközplatformon.

**Naplózás**
* A rendszergazdák naplózhatják a felhasználói hozzáférési tevékenységeket.


### <a name="best-practices-for-a-pilot"></a>Legjobb gyakorlatok egy kísérleti

Határozza meg, hogy mennyi idő és erőfeszítés szükséges a távelérés egyetlen alkalmazás egyszeri bejelentkezéssel történő teljes körű üzembe töltéséhez. Ehhez fut egy próba, amely figyelembe veszi a kezdeti felderítés, közzététel, és az általános tesztelés. Egy egyszerű IIS-alapú webalkalmazás használata, amely már előre konfigurált integrált Windows-hitelesítés (IWA) segítene létrehozni az alapkonfigurációt, mivel ez a beállítás minimális erőfeszítést igényel a távelérés és az egyszeri szolgáltató sikeres pályáztatásához.

A következő tervezési elemek nek növelniük kell a kísérleti implementáció sikerét közvetlenül az éles bérlőben.  

**Összekötő kezelése:**  

* Az összekötők kulcsszerepet játszanak az alkalmazások helyszíni csatornájának biztosításában. Az **alapértelmezett** összekötő csoport használata megfelelő a közzétett alkalmazások kezdeti próbateszteléséhez, mielőtt üzembe rendelne őket az éles környezetbe. A sikeresen tesztelt alkalmazások ezután áthelyezhetők az éles összekötő csoportokba.

**Alkalmazáskezelés**:

* A munkaerő valószínűleg megjegyzi, hogy egy külső URL ismerős és releváns. Ne tegye közzé alkalmazását az előre meghatározott msappproxy.net vagy onmicrosoft.com utótagokkal. Ehelyett adjon meg egy ismerős felső szintű ellenőrzött tartományt, amely előtaggal rendelkezik egy logikai állomásnévvel, például *intranet.<customers_domain>.com*.

* A próbaalkalmazás ikonjának láthatóságát egy próbacsoportra korlátozhatja az Azure MyApps portálon található indítási ikon elrejtésével. Amikor készen áll az éles használatra, az alkalmazást a megfelelő megcélzott közönségre is lefedheti, akár ugyanabban az üzem előtti bérlőben, akár az alkalmazás éles környezetben való közzétételével.

**Egyszeri bejelentkezési beállítások:** Egyes egyszeri bejelentkezési beállítások speciális függőségekkel rendelkeznek, amelyek beállítása időbe telhet, ezért a függőségek idő előtti kezelésével elkerülheti a változásszabályozás késleltetését. Ez magában foglalja a tartománycsatlakozó-állomások at, hogy a Kerberos korlátozott delegálás (KCD) használatával egyszeri szolgáltatást hajtson végre, és más időigényes tevékenységeket is elvégezzen. Például ping access példány beállítása, ha fejlécalapú egyszeri vevőre van szüksége.

**TLS összekötő állomás és a célalkalmazás között:** A biztonság a legfontosabb, ezért a TLS-t az összekötő gazdagép és a célalkalmazások között mindig használni kell. Különösen akkor, ha a webalkalmazás űrlapalapú hitelesítésre (FBA) van konfigurálva, mivel a felhasználói hitelesítő adatok at hatékonyan, tiszta szövegként továbbítják.

**Fokozatosan hajtsa végre, és tesztelje az egyes lépcsőkt.** Az alkalmazás közzététele után végezzen alapvető funkcionális tesztelést annak biztosítása érdekében, hogy az alábbi utasítások szerint minden felhasználói és üzleti követelmény teljesüljön:

1. Tesztelje és ellenőrizze a webes alkalmazáshoz való általános hozzáférést, ha az előhitelesítés le van tiltva.
2. Ha sikeresen engedélyezze az előhitelesítést, és rendeljen hozzá felhasználókat és csoportokat. Tesztelje és érvényesítse a hozzáférést.
3. Ezután adja hozzá az SSO-módszert az alkalmazáshoz, és tesztelje újra a hozzáférés érvényesítéséhez.
4. Szükség szerint alkalmazza a feltételes hozzáférés re vonatkozó és mfa-házirendeket. Tesztelje és érvényesítse a hozzáférést.

**Hibaelhárítási eszközök:** Hibaelhárításkor mindig kezdje azzal, hogy érvényesíti a közzétett alkalmazáshoz való hozzáférést az összekötő gazdagépen lévő böngészőből, és ellenőrizze, hogy az alkalmazás a várt módon működik-e. Minél egyszerűbb a beállítás, annál könnyebben meghatározható a kiváltó ok, ezért fontolja meg a problémák minimális konfigurációval történő reprodukálását, például csak egyetlen összekötő használatával, és nincs egyszeri biztonsági ár.the simple the setup, the easier to determine root cause, so consider to reprodukálás issues with a minimal configuration such as using only a single connector and no SSO. Bizonyos esetekben a webes hibakereső eszközök, mint például a Telerik Fiddler-e nélkülözhetetlennek bizonyulhatnak a proxyn keresztül elérhető alkalmazások hozzáférési vagy tartalmi problémáinak elhárításához. Fiddler is jár, mint egy proxy, hogy segítsen nyomon követni és debug forgalom mobil platformok, mint például az iOS és az Android, és gyakorlatilag bármi, ami lehet beállítani, hogy útvonalon keresztül proxy. További információt a [hibaelhárítási útmutatóban](application-proxy-troubleshoot.md) talál.

## <a name="implement-your-solution"></a>Valósítsa meg a megoldást

### <a name="deploy-application-proxy"></a>Alkalmazásproxy telepítése

Az alkalmazásproxy üzembe helyezésének lépéseit ez az oktatóanyag ismerteti a [távoli eléréshez szükséges helyszíni alkalmazások hozzáadásához.](application-proxy-add-on-premises-application.md) Ha a telepítés nem sikerült, válassza az **Alkalmazásproxy hibaelhárítása lehetőséget** a portálon, vagy használja az [Alkalmazásproxy-ügynök-összekötő telepítésével kapcsolatos problémák hibaelhárítási útmutatóját.](application-proxy-connector-installation-problem.md)

### <a name="publish-applications-via-application-proxy"></a>Alkalmazások közzététele alkalmazásproxyn keresztül

A közzétételi alkalmazások feltételezik, hogy minden előfeltételnek megfelelt, és hogy több összekötővel rendelkezik, amelyek regisztráltként és aktívként jelennek meg az alkalmazásproxy lapon.

Az alkalmazásokat a [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)használatával is közzéteheti.

Az alábbiakban néhány, az alkalmazás közzétételekor követendő gyakorlati tanács olvasható:

* **Összekötőcsoportok használata:** Rendeljen hozzá egy összekötőcsoportot, amely et kijelöltek az egyes alkalmazások közzétételére. Azt javasoljuk, hogy minden összekötő csoport rendelkezik legalább két összekötők magas rendelkezésre állás és a méretezés. A három csatlakozó optimális abban az esetben, ha bármikor szervizelnie kell egy gépet. Ezenkívül az [Alkalmazások közzététele külön hálózatokon és helyeken összekötőcsoportok használatával](application-proxy-connector-groups.md) című témakörben ismerteti, hogyan használhatja az összekötőcsoportokat az összekötők hálózat vagy hely szerinti szegmentálására.

* **Háttérrendszer-alkalmazás időhelyesbítésének beállítása:** Ez a beállítás olyan esetekben hasznos, amikor az alkalmazás nak 75 másodpercnél többre van szüksége az ügyféltranzakció feldolgozásához. Például amikor egy ügyfél lekérdezést küld egy olyan webalkalmazásnak, amely előtérként működik egy adatbázisba. Az előtér elküldi ezt a lekérdezést a háttéradatbázis-kiszolgálónak, és megvárja a választ, de mire választ kap, a beszélgetés ügyféloldala időtúljár. Az időhosszabbítás hosszúra állítása 180 másodpercet biztosít a hosszabb tranzakciók befejezéséhez.

* **Megfelelő cookie-típusok használata**

   * **CSAK HTTP-cookie**: További biztonságot nyújt azáltal, hogy az alkalmazásproxy tartalmazza a HTTPOnly jelzőt a set-cookie HTTP válaszfejlécekben. Ez a beállítás segít a biztonsági rések, például a webhelyek közötti parancsfájlok (XSS) csökkentésében. Hagyja ezt a készletet Nem-re azon ügyfelek/felhasználói ügynökök esetében, akiknek hozzáférésre van szükségük a munkamenet-cookie-hoz. Például rdp/MTSC ügyfél csatlakozik egy távoli asztali átjáró közzétett app proxy.

   * **Biztonságos cookie:** Ha egy cookie secure attribútummal van beállítva, a felhasználói ügynök (ügyféloldali alkalmazás) csak akkor veszi fel a cookie-t a HTTP-kérelmekbe, ha a kérést TLS-alapú biztonságos csatornán keresztül továbbítják. Ez segít csökkenteni annak kockázatát, hogy a cookie-k at egyértelmű szöveges csatornákon keresztül veszélyeztessék, ezért engedélyezni kell.

   * **Állandó cookie**: Lehetővé teszi, hogy az alkalmazásproxy munkamenet-cookie-ja megmaradjon a böngészőbezárásai között, ha érvényes marad, amíg le nem jár vagy nem törlődik. Olyan esetekben használható, amikor egy gazdag alkalmazás, például az office hozzáfér egy dokumentumhoz egy közzétett webalkalmazáson belül, anélkül, hogy a felhasználó újra hitelesítést kérne. Engedélyezze azonban óvatosan, mivel az állandó cookie-k végső soron a szolgáltatás jogosulatlan hozzáférés veszélyének vannak kitéve, ha nem használják más kompenzáló vezérlőkkel együtt. Ez a beállítás csak olyan régebbi alkalmazásokesetében használható, amelyek nem tudják megosztani a cookie-kat a folyamatok között. A beállítás használata helyett jobb, ha frissíti az alkalmazást, hogy kezelje a cookie-k folyamatok közötti megosztását.

* **URL-címek fordítása a fejlécekben:** Ezt olyan esetekben engedélyezi, amikor a belső DNS nem konfigurálható úgy, hogy megfeleljen a szervezet nyilvános névterének (más néven a Split DNS). Ha csak akkor, ha az alkalmazás az ügyfélkérelemben az eredeti állomásfejlécet igényli, hagyja ezt az értéket Igen értékre. A másik lehetőség az, hogy az összekötő használja a teljes tartományn a belső URL-cím a tényleges forgalom útválasztása, és a teljes tartományna a külső URL-cím, mint a gazdagép-fejléc. A legtöbb esetben ez az alternatíva lehetővé kell tennie az alkalmazás működését a szokásos módon, ha távolról érhető el, de a felhasználók elveszítik az előnyeit, amelyek a megfelelő belül & külső URL-t.

* **URL-ek fordítása az alkalmazástörzsben:** Kapcsolja be az alkalmazástörzs hivatkozásfordítását egy alkalmazáshoz, ha azt szeretné, hogy az adott alkalmazáshivatkozásait az ügyfélnek adott válaszokban fordítsa le. Ha engedélyezve van, ez a funkció biztosítja a legjobb erőfeszítést az összes belső hivatkozás fordítására, amelyet az App Proxy html- és CSS-válaszokban talál vissza az ügyfeleknek. Ez akkor hasznos, ha olyan alkalmazásokat tesz közzé, amelyek a tartalomban hard-coded abszolút vagy NetBIOS rövidnév hivatkozásokat tartalmaznak, vagy olyan alkalmazásokat, amelyek más helyszíni alkalmazásokra mutató tartalommal rendelkeznek.

Olyan esetekben, amikor egy közzétett alkalmazás más közzétett alkalmazásokra hivatkozik, engedélyezze a hivatkozások fordítását az egyes alkalmazásokhoz, hogy alkalmazásonként szabályozhassa a felhasználói élményt.

Tegyük fel például, hogy három alkalmazás van közzétéve az alkalmazásproxyn keresztül, amelyek mindegyike egymásra mutató hivatkozás: Előnyök, Költségek és Utazás, valamint egy negyedik alkalmazás, a Visszajelzés, amely nem az alkalmazásproxyn keresztül jelenik meg.

![1. kép](media/App-proxy-deployment-plan/link-translation.png)

Ha engedélyezi a kapcsolatok fordítását a Benefits alkalmazáshoz, a költségekre és az utazásra mutató hivatkozásokat a rendszer átirányítja az alkalmazások külső URL-jeire, így a vállalati hálózaton kívülről érkező alkalmazásokat elérő felhasználók hozzáférhetnek. A költségekés az utazások vissza az előnyökre mutató hivatkozások nem működnek, mert a két alkalmazás esetében nincs engedélyezve a hivatkozásfordítás. A visszajelzésre mutató hivatkozás nem lesz átirányítva, mert nincs külső URL, így a Előnyök alkalmazást használó felhasználók nem férhetnek hozzá a visszajelzési alkalmazáshoz a vállalati hálózaton kívülről. Részletes információk a [hivatkozásfordításról és az egyéb átirányítási lehetőségekről](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Az alkalmazás elérése

Számos lehetőség létezik az App Proxy közzétett erőforrásokhoz való hozzáférés kezelésére, ezért válassza ki az adott forgatókönyvnek és méretezhetőségi igényeknek leginkább megfelelőt. A gyakori megközelítések a következők: az Azure AD Connecten keresztül szinkronizált helyszíni csoportok használata, dinamikus csoportok létrehozása az Azure AD-ben felhasználói attribútumok alapján, önkiszolgáló csoportok használatával, amelyeket egy erőforrás-tulajdonos kezel, vagy ezek kombinációja. Tekintse meg a kapcsolódó erőforrások előnyeit az egyes.

Az alkalmazáshoz való hozzáférés hozzárendelésének legközvetlenebb módja a **Felhasználók és csoportok** beállításainak a közzétett alkalmazás bal oldali ablaktáblájából, valamint a csoportok vagy egyének közvetlen hozzárendelése.

![Kép 24](media/App-proxy-deployment-plan/add-user.png)

Azt is engedélyezheti a felhasználóknak, hogy önkiszolgáló hozzáférést az alkalmazáshoz hozzárendelésével egy csoport, amely jelenleg nem tagja, és konfigurálja az önkiszolgáló lehetőségeket.

![25. kép](media/App-proxy-deployment-plan/allow-access.png)

Ha engedélyezve van, a felhasználók majd bejelentkezhetnek a MyApps portálra, és hozzáférést kérhetnek, és automatikusan jóváhagyhatók, és hozzáadódnak a már engedélyezett önkiszolgáló csoporthoz, vagy egy kijelölt jóváhagyó jóváhagyására van szükségük.

Vendég felhasználók is [fel lehet kérni, hogy hozzáférjenek a belső alkalmazások közzétett alkalmazásproxy keresztül Azure AD B2B.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)

Olyan helyszíni alkalmazások esetén, amelyek általában névtelenül érhetők el, hitelesítés nélkül, célszerű lehet letiltani az alkalmazás **tulajdonságai**ban található beállítást.

![Kép 26](media/App-proxy-deployment-plan/assignment-required.png)


Ha ezt a beállítást nem re állítja, a felhasználók engedély nélkül férhetnek hozzá a helyszíni alkalmazáshoz az Azure AD App Proxy használatával, ezért körültekintően járjon el.

Az alkalmazás közzététele után elérhetőnek kell lennie, ha beírja a külső [https://myapps.microsoft.com](https://myapps.microsoft.com/)URL-címét a böngészőbe, vagy ikonját a alkalmazásban.

### <a name="enable-pre-authentication"></a>Előhitelesítés engedélyezése

Ellenőrizze, hogy az alkalmazás elérhető-e a külső URL-címen keresztül elérő alkalmazásproxyn keresztül. 

1. Keresse meg az **Azure Active Directory** > **Enterprise alkalmazások** > **minden alkalmazást,** és válassza ki a kezelni kívánt alkalmazást.

2. Válassza **az Alkalmazásproxy**lehetőséget .

3. A **Hitelesítés előtti mezőben** a legördülő lista segítségével válassza az **Azure Active Directory**t, majd a **Mentés**lehetőséget.

Ha az előhitelesítés engedélyezve van, az Azure AD először a hitelesítéshez hívja ki a felhasználókat, és ha egyszer esikol van konfigurálva, akkor a háttéralkalmazás is ellenőrzi a felhasználót, mielőtt az alkalmazáshoz való hozzáférés megadható. Az előhitelesítési mód átlépésről az Azure AD-be történő módosítása a külső URL-címet https-lel is konfigurálja, így a HTTP-hez eredetileg konfigurált alkalmazások mostantól https-lel lesznek biztosítva.

### <a name="enable-single-sign-on"></a>Egyszeri bejelentkezés engedélyezése

Egyszeri bejelentkezés a lehető legjobb felhasználói élményt és biztonságot nyújtja, mert a felhasználóknak csak egyszer kell bejelentkezni, amikor az Azure AD-t éri el. Miután a felhasználó előre hitelesítette, az Egyszeri rendelést a helyszíni alkalmazásnak hitelesítő alkalmazásproxy-összekötő hajtja végre a felhasználó nevében. A háttéralkalmazás úgy dolgozza fel a bejelentkezést, mintha maguk a felhasználó lennének. 

Az **Átlépési** lehetőség kiválasztásával a felhasználók anélkül férhetnek hozzá a közzétett alkalmazáshoz, hogy az Azure AD-ben hitelesíteniük kellene magukat.

Egyszeri telepítési szolgáltatás végrehajtása csak akkor lehetséges, ha az Azure AD képes azonosítani a felhasználó hozzáférést kér egy erőforráshoz, ezért az alkalmazást úgy kell konfigurálni, hogy előzetesen hitelesítse a felhasználókat az Azure AD-vel az egyszeri szolgáltatás működéséhez való hozzáférés után, ellenkező esetben az egyszeri szolgáltatás beállításai le lesznek tiltva.

Olvassa [el az Azure AD-ben az alkalmazásokra való egyszeri bejelentkezést,](what-is-single-sign-on.md) hogy az alkalmazások konfigurálásakor a legmegfelelőbb egyszeri bejelentkezési módszert választhassa ki.

###  <a name="working-with-other-types-of-applications"></a>Más típusú alkalmazásokkal való együttműködés

Az Azure AD alkalmazásproxy támogatja az Azure AD hitelesítési könyvtár[(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)vagy a Microsoft Authentication Library[(MSAL)](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/)használatára kifejlesztett alkalmazásokat is. Támogatja a natív ügyfélalkalmazások az Azure AD által kibocsátott jogkivonatokat kapott a fejléc adatait az ügyfélkérelem a felhasználók nevében végzett előzetes hitelesítés végrehajtásához.

Olvassa el [a natív és mobil ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) és [jogcímalapú alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) közzétételét, és ismerje meg az Alkalmazásproxy elérhető konfigurációit.

### <a name="use-conditional-access-to-strengthen-security"></a>Feltételes hozzáférés használata a biztonság megerősítéséhez

Az alkalmazásbiztonság hoz egy speciális biztonsági képességek, amelyek védelmet nyújtanak a helyszíni és a felhőben lévő összetett fenyegetések elleni védelemre és az azokra való reagálásra. A támadók leggyakrabban gyenge, alapértelmezett vagy ellopott felhasználói hitelesítő adatokkal férnek hozzá a vállalati hálózati hozzáféréshez.  A Microsoft identitásvezérelt biztonsága csökkenti az ellopott hitelesítő adatok használatát a kiemelt és nem kiemelt identitások kezelésével és védelmével.

Az Azure AD alkalmazásproxy támogatásához a következő képességek használhatók:

* Felhasználó- és helyalapú feltételes hozzáférés: A bizalmas adatok védelme a földrajzi hely vagy a [helyalapú feltételes hozzáférési házirendekkel rendelkező IP-cím](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)alapján történő korlátozásával biztosíthatja a bizalmas adatok védelmét.

* Eszközalapú feltételes hozzáférés: Győződjön meg arról, hogy csak a regisztrált, jóváhagyott és megfelelő eszközök férhetnek hozzá a vállalati adatokhoz [az eszközalapú feltételes hozzáféréssel.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)

* Alkalmazásalapú feltételes hozzáférés: A munkának nem kell leállnia, ha a felhasználó nem a vállalati hálózaton van. [Biztonságos hozzáférés a vállalati felhőalapú és helyszíni alkalmazásokhoz,](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) valamint az irányítás fenntartása a feltételes hozzáféréssel.

* Kockázatalapú feltételes hozzáférés: A [kockázatalapú feltételes hozzáférésre vonatkozó szabályzattal](https://www.microsoft.com/cloud-platform/conditional-access) megvédheti adatait a rosszindulatú támadóktól, amely minden alkalmazásra és minden felhasználóra alkalmazható, akár a helyszínen, akár a felhőben.

* Azure AD access panel: Az alkalmazásproxy-szolgáltatás üzembe helyezésével és a biztonságosan közzétett alkalmazásokkal egyszerű központot kínálhat a felhasználóknak az összes alkalmazás felderítéséhez és eléréséhez. Növelje a termelékenységet az önkiszolgáló funkciókkal, például az új alkalmazásokhoz és csoportokhoz való hozzáférés kérésével, vagy az erőforrásokhoz való hozzáférés kezelésével mások nevében a [Hozzáférési panelen](https://aka.ms/AccessPanelDPDownload)keresztül.

## <a name="manage-your-implementation"></a>A megvalósítás kezelése

### <a name="required-roles"></a>Szükséges szerepkörök

A Microsoft támogatja azt az elvet, hogy a lehető legkevesebb jogosultságot adja a szükséges feladatok azure AD-vel való elvégzéséhez. [Tekintse át a rendelkezésre álló különböző Azure-szerepköröket,](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) és válassza ki az egyes személyek igényeinek kielégítéséhez megfelelőt. Előfordulhat, hogy egyes szerepköröket ideiglenesen kell alkalmazni, és a központi telepítés befejezése után el kell távolítani.

| Üzleti szerepkör| Üzleti feladatok| Azure AD-szerepkörök |
|---|---|---|
| Ügyfélszolgálati rendszergazda | Általában csak a jogosult végfelhasználói jelentett problémák és a feladatok végrehajtása, mint például a felhasználók jelszavainak módosítása, érvénytelenítése frissítési jogkivonatok, és a szolgáltatás állapotának figyelése. | Ügyfélszolgálati rendszergazda |
| Identitás-rendszergazda| Olvassa el az Azure AD-bejelentkezési jelentésekés naplónaplók hibakeresés app proxy kapcsolatos problémák.| Biztonsági olvasó |
| Alkalmazás tulajdonosa| A vállalati alkalmazások, az alkalmazásregisztrációk és az alkalmazásproxy-beállítások minden aspektusát létrehozhatja és kezelheti.| Alkalmazás adminisztrátora |
| Infrastruktúra rendszergazdája | Tanúsítványváltás tulajdonosa | Alkalmazás adminisztrátora |

A biztonságos adatokhoz vagy erőforrásokhoz hozzáféréssel rendelkező személyek számának minimalizálása segít csökkenteni annak esélyét, hogy egy rosszindulatú szereplő jogosulatlan hozzáférést szerezzen, vagy ha egy jogosult felhasználó véletlenül hatással van egy bizalmas erőforrásra. 
 
A felhasználóknak azonban továbbra is napi kiemelt fontosságú műveleteket kell végrehajtaniuk, így a just-in-time (JIT) alapú [kiemelt identitáskezelési](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) szabályzatok kényszerítése az Azure-erőforrásokhoz való igény szerinti kiemelt hozzáférés biztosítása érdekében, és az Azure AD az ajánlott megközelítésünk a felügyeleti hozzáférés és a naplózás hatékony kezeléséhez.

### <a name="reporting-and-monitoring"></a>Jelentés és monitorozás

Az Azure AD a naplók és jelentések segítségével további betekintést nyújt a szervezet alkalmazáshasználatába és működési [állapotába.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) Alkalmazásproxy is nagyon egyszerűvé teszi az összekötők figyelése az Azure AD portálról és a Windows eseménynaplók.

#### <a name="application-audit-logs"></a>Alkalmazások auditnaplói

Ezek a naplók részletes információkat nyújtanak az alkalmazásproxyval konfigurált alkalmazásokba, valamint az alkalmazáshoz és az alkalmazáshoz hozzáférő felhasználóhoz való bejelentkezésekről. [A naplónaplók](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) az Azure Portalon és az [exportálási API-naplózási API-ban](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) találhatók. Emellett [a használati és elemzési jelentések](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) is rendelkezésre állnak az alkalmazáshoz.

#### <a name="application-proxy-connector-monitoring"></a>Alkalmazásproxy-összekötő figyelése

Az összekötők és a szolgáltatás gondoskodik a magas rendelkezésre állási feladatok. Az összekötők állapotát az Azure AD Portal alkalmazásproxy lapjáról figyelheti. Az összekötő karbantartásáról az [Azure AD alkalmazásproxy-összekötők ismertetése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance)című témakörben talál további információt.

![Példa: Azure AD alkalmazásproxy-összekötők](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows eseménynaplók és teljesítményszámlálók

Az összekötők rendszergazdai és munkamenet-naplókkal is rendelkeznek. A rendszergazdai naplók tartalmazzák a legfontosabb eseményeket és azok hibáit. A munkamenetnaplók tartalmazzák az összes tranzakciót és azok feldolgozási adatait. A naplók és számlálók a Windows eseménynaplókban találhatók további információt az [Azure AD alkalmazásproxy-összekötők ismertetése című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood) Az [azure-figyelőeseménynapló-adatforrások konfigurálásához](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)kövesse ezt az oktatóanyagot.

### <a name="troubleshooting-guide-and-steps"></a>Hibaelhárítási útmutató és lépések

A [hibaüzenetek hibaelhárításáról](application-proxy-troubleshoot.md) szóló útmutatónkban további információ a gyakori problémákról és azok megoldásáról. 

Az alábbi cikkek olyan gyakori forgatókönyveket tartalmaznak, amelyek hibaelhárítási útmutatók at hozhatnak létre a támogatási szervezet számára. 

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
* [„Can't Access this Corporate Application” (Nem lehet hozzáférni ehhez a céges alkalmazáshoz) hibaüzenet](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Probléma az alkalmazásproxy-ügynök összekötőjének telepítésekor](application-proxy-connector-installation-problem.md)
* [Bejelentkezéssel kapcsolatos probléma](application-sign-in-problem-on-premises-application-proxy.md)
