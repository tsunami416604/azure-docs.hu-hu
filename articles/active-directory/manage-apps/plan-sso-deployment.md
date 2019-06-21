---
title: Azure Active Directory egyszeri bejelentkezés központi telepítésének megtervezése
description: Az útmutató segítséget tervezésére, üzembe helyezése és egyszeri bejelentkezés kezelése a szervezetben.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5278d504c43688bf064b869982938db52b1b1bf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164957"
---
# <a name="plan-a-single-sign-on-deployment"></a>Egy egyszeri bejelentkezés üzembe helyezésének megtervezése

Egyszeri bejelentkezés (SSO): az összes alkalmazások és erőforrások csak egyszer, egyetlen felhasználói fiókkal jelentkezzen be a felhasználó nem fér hozzá. Egyszeri bejelentkezés, a felhasználói bármikor hozzáférhetnek a minden szükséges alkalmazásokat anélkül, hogy másodszor hitelesítéséhez.

## <a name="benefits-of-sso"></a>Egyszeri bejelentkezés előnyei

Egyszeri bejelentkezés (SSO) hozzáadja a biztonsági és kényelmes használatra vonatkozó lehetőséget, amikor a felhasználók jelentkezzen be az Azure Active Directoryban (Azure AD-) alkalmazások. 

Számos szervezet támaszkodik a szoftver, mint a szoftverszolgáltatások (SaaS) alkalmazások, például az Office 365-höz, a Box vagy a Salesforce, a végfelhasználói termelékenység. Hagyományosan informatikai munkatársak külön-külön létrehozásához, és minden egyes SaaS-alkalmazás és a egy jelszó megjegyzése minden szükséges felhasználók a felhasználói fiókok frissítése szükséges.

Az Azure Marketplace-en olyan előre integrált egyszeri Bejelentkezést kapcsolatok, több mint 3000 alkalmazások, így könnyen integrálhatja azokat a bérlőben.

## <a name="licensing"></a>Licencek

- **Az Azure AD licencelése** -előre integrált Szolgáltatottszoftver-alkalmazáshoz az egyszeri bejelentkezés díjmentes. A címtárral és az a funkciók kívánja telepíteni az objektumok száma azonban szükség lehet további licenceket. Licenc követelmények teljes listáját lásd: [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/).
- **Az alkalmazáslicenceket** – az SaaS-alkalmazások az üzleti igényeinek megfelelő licencekkel kell. Működik az alkalmazás tulajdonosa meghatározni, hogy az alkalmazás a felhasználók rendelkeznek-e az alkalmazásban lévő szerepkörökhöz megfelelő licenceivel. Ha az Azure AD kezeli az Automatikus kiépítés szerepkörök alapján, az Azure AD-ben hozzárendelt szerepkörök kell összhangba kerüljenek az alkalmazáson belül birtokolt licencek számát. A kiépítés vagy frissít egy felhasználó során az alkalmazás a birtokolt licencek száma helytelen hibákhoz vezethetnek.

## <a name="plan-your-sso-team"></a>Egyszeri bejelentkezés csapata megtervezése

- **A jobb oldali érdekelt felek léphet** - technológia projektek sikertelenek lesznek, amikor általában a hatás, kimeneteket és felelősségek eltérő elvárások miatt van. Ezek a alkalmazásmegoldásokra elkerülése érdekében [győződjön meg arról, hogy a megfelelő érintettek vagyunk szabályzatra hivatkozó](https://aka.ms/deploymentplans) és, hogy az érdekelt felek tudomásul szerepkörökhöz.
- **A kommunikáció tervezése** -kommunikáció, kritikus fontosságú a sikeres semmilyen új szolgáltatást. Proaktív módon való kommunikáció során a felhasználók hogyan változik a szerzett, mikor változik, és megtudhatja, hogyan támogatási problémák esetén eredményez. Tekintse át a beállításokat a [végfelhasználók hogyan érik el az SSO-kompatibilis alkalmazások](end-user-experiences.md), és a kommunikáció megfelelően a kijelölt írhat. 

## <a name="plan-your-sso-protocol"></a>Az egyszeri bejelentkezés protokollt megtervezése

Összevonási protokollok alapján egyszeri bejelentkezés megvalósítása javítja a biztonságot, megbízhatóságot, és a végfelhasználói élmény, és jóval egyszerűbb implementálni. Számos alkalmazás előre integrált, az Azure AD- [lépés lépésről lépésre végigvezeti a rendelkezésre álló](../saas-apps/tutorial-list.md). Megtalálhatja azokat a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/). A cikkben található minden egyes egyszeri bejelentkezési módszer részletes információkat [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](what-is-single-sign-on.md).

Kétféleképpen elsődleges, amelyben engedélyezheti a felhasználók egyszeri bejelentkezés az alkalmazásokhoz:

- **Az összevont egyszeri bejelentkezés** Azure AD akkor hitelesíti a felhasználót, hogy az alkalmazás Azure AD-fiókját használva. Ez a módszer támogatott alkalmazásokat, amelyek támogatási protokollok, például a SAML 2.0, WS-Federation, vagy az OpenID Connect, és az egyszeri bejelentkezés az richest mód. Ha egy alkalmazás ezt támogatja, helyett a jelszóalapú egyszeri bejelentkezés és az ADFS Azure AD-vel összevont egyszeri bejelentkezés használatát javasoljuk.

- **A jelszóalapú egyszeri bejelentkezés** bejelentkeznek az alkalmazásba a felhasználónévvel és jelszóval először, azok-e férni. Miután az első bejelentkezés Azure ad-ben a felhasználónevet és jelszót, hogy az alkalmazás választékát kínálja. Jelszavas egyszeri bejelentkezés lehetővé teszi a biztonságos tárolását és ismétlését egy webböngésző-bővítmény vagy mobilalkalmazás segítségével. Ezt a beállítást használja a meglévő bejelentkezési folyamat az alkalmazás által biztosított, lehetővé teszi a rendszergazdák a jelszavak kezeléséhez, és nincs szükség a felhasználót, hogy ismeri a jelszavát.

### <a name="considerations-for-federation-based-sso"></a>Összevonás-alapú egyszeri bejelentkezés szempontjai

- **OpenID Connect és az OAuth használatával** – Ha az alkalmazást, amelyhez csatlakozik támogatja, a OIDC/OAuth 2.0 metódus használatával, amelyek az egyszeri bejelentkezés engedélyezése. Ez a módszer kevesebb konfigurációs van szükség, és lehetővé teszi, hogy egy gazdagabb felhasználói élmény. További információkért lásd: [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), és [Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **SAML-alapú egyszeri bejelentkezési végpont konfigurációi** – Ha az SAML használata esetén a fejlesztők információkat az alkalmazás konfigurálása előtt kell. További információ: [alapszintű SAML-beállítások konfigurálása](configure-single-sign-on-portal.md).
- **Tanúsítványkezelés az SAML-alapú egyszeri bejelentkezéshez** – az alkalmazás, az Azure AD-hoz létre egy tanúsítványt, amely három évig érvényes alapértelmezés szerint az összevont egyszeri bejelentkezés engedélyezése esetén. Szükség esetén a tanúsítvány lejárati dátuma testre szabhatja. Győződjön meg arról, hogy már működik a megújítja a tanúsítványokat a lejárat előtt folyamatokat. További tudnivalókért lásd: [az Azure AD kezelése tanúsítványok](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Szempontok a jelszóalapú egyszeri bejelentkezés

A jelszóalapú egyszeri bejelentkezés az Azure AD használatával telepíteni kell egy webböngésző-bővítmény, amely biztonságosan betölti a hitelesítő adatokat, és töltse ki a bejelentkezési űrlap. Adja meg egy olyan mechanizmust méretek mellett a bővítmény telepítése [támogatott böngészők](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). A lehetőségek a következők:

- [Az Internet Explorer csoportházirend](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [A System Center Configuration Manager (SCCM) az Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [A felhasználó közreműködésével, töltse le és a Chrome, Firefox, a Microsoft Edge vagy Internet Explorer konfigurációját](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

További tudnivalókért lásd: [hogyan konfigurálása jelszó egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>A rögzítés bejelentkezési képernyők metaadatait az alkalmazásokat, amelyek nem szerepelnek a katalógusban

A Microsoft a jelszó (a felhasználónév és jelszó mezők rögzítése) vaulting webalkalmazás rögzítését metaadatok támogatja. Keresse meg a bejelentkezési URL-cím a képernyők metaadatok rögzítése az alkalmazás konfigurálása során. Kérdezze meg a pontos bejelentkezési URL-címe az alkalmazás tulajdonosa. Ez az információ a bejelentkezési folyamat során a bejelentkezés során az alkalmazás Azure AD hitelesítő adatait leképezése szolgál.

További tudnivalókért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure ad-vel? – jelszóalapú egyszeri bejelentkezés](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Jelzések visszanyerését kell ezekhez a metaadatokhoz űrlapokon

Alkalmazások módosítsa azok HTML-elrendezését, amikor szüksége lehet a változásoknak a metaadatok rendszerképrögzítő. Általános jelenség, amely jelzi, hogy a HTML-elrendezésének módosítása a következők:

- A felhasználók az alkalmazásra kattintva reporting "elakad" a bejelentkezési oldalon
- A felhasználók reporting, hogy a felhasználónév vagy jelszó nincs feltöltve

#### <a name="shared-accounts"></a>Közös fiókok

A bejelentkezési szempontból a megosztott fiókkal rendelkező alkalmazások nem különböző katalógus-alkalmazás, amely jelszavas egyszeri Bejelentkezést használ az egyéni felhasználók számára. Van azonban néhány további lépés szükséges, ha a tervezési és -alkalmazások konfigurálása arra, hogy a megosztott fiókok használata:

1. Működik az alkalmazás üzleti felhasználókkal dokumentálni a következőket:
   1. A szervezetben, akik használni fogják, hogy az alkalmazás felhasználói számára
   1. Hitelesítő adatok az alkalmazásban található felhasználók készlete társított meglévő készletének 
1. Minden felhasználói csoport és a hitelesítő adatok kombinációjának hozzon létre egy biztonsági csoportot a felhőben vagy helyszíni igényei alapján.
1. A megosztott hitelesítő adatok alaphelyzetbe állítása. Telepítse az alkalmazást, az Azure ad-ben egyéni felhasználók számára a megosztott fiók jelszava nem szükséges. Mivel az Azure ad-ben a jelszót fogja tárolni, érdemes lehet értékre állítani, hogy nagyon hosszú és összetett. 
1. Konfigurálja az automatikus átvitel, a jelszót, ha az alkalmazás ezt támogatja. Ezzel a módszerrel is, ha a rendszergazda a kezdeti beállítás végrehajtó tudni fogja a megosztott fiók jelszavát. 

## <a name="plan-your-authentication-method"></a>A hitelesítési módszer megtervezése

A megfelelő hitelesítési módszer választása az beállítása az Azure AD hibrid identitáskezelési megoldás kulcsfontosságú első döntése. A hitelesítési módszer, amely konfigurálva van, amely emellett kiosztja a felhőbeli felhasználók az Azure AD Connect használatával hajtja végre.

Válasszon hitelesítési módszert, fontolja meg a idő, a meglévő infrastruktúra, az összetettséget és a költség végrehajtási választott kell. Ezek a tényezők különböző minden szervezet számára, és idővel változhatnak. Az egy másikkal, amelyik leginkább megfelel az adott kell kiválasztani. További információkért lásd: [válassza ki a megfelelő hitelesítési módszert az Azure Active Directory hibrid identitáskezelési megoldás](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

## <a name="plan-your-security-and-governance"></a>A biztonsági és cégirányítási megtervezése 

Identitás az új elsődleges pivot biztonsági intézkedést és a beruházások azért, mert a hálózati régebben váltak a BYOD-eszközök robbanásszerűen egyre elválasztó és kevésbé hatékony, és a felhőbeli alkalmazásokhoz. 

### <a name="plan-access-reviews"></a>Csomag-hozzáférési felülvizsgálatok

[Hozzáférési felülvizsgálatokkal](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) engedélyezése a cégek hatékonyan kezelhetik a csoporttagságokat, a vállalati alkalmazások és a szerepkör-hozzárendeléseket. Tekintse át a felhasználói hozzáférést, hogy csak a megfelelő emberek számára hozzáférést kaphatnak folyamatos rendszeresen kell terveznie.

A hozzáférési felülvizsgálatok beállítása közben tervezése legfontosabb témakörök többek között:

1. Egy kiadása ütemben történik a hozzáférési felülvizsgálatok az üzleti igényeknek megfelelő azonosítása. Ez lehet például gyakori, hetente, havonta, évente, vagy igény szerinti gyakorlatként.

1. Hozzon létre csoportokat, amelyek megfelelnek a felülvizsgálatot, az alkalmazás hozzáférési jelentések. Ügyeljen arra, hogy legtöbb ismeri az alkalmazás és a cél-felhasználók és a használati esetek résztvevőket a hozzáférési felülvizsgálatok résztvevői kell

1. Hozzáférési felülvizsgálat befejezése magában foglalja a felhasználók számára, akiknek többé nincs szükségük a hozzáférést azonnal alkalmazás-hozzáférési engedélyek véve. Tervezze meg a letiltott felhasználók lehetséges támogatáskérések kezelése. Törölt felhasználó törlése az Azure ad-ben ez idő alatt, vissza tudja állítani a rendszergazda által szükség esetén 30 napig maradnak. A felhasználók 30 nap után véglegesen törlődnek. Az Azure Active Directory portál használata esetén egy globális rendszergazda is explicit módon véglegesen törli egy nemrég törölt felhasználó időszak elérése előtt.

### <a name="plan-auditing"></a>Naplózás tervezése

Az Azure AD biztosít [technikai és üzleti elemzéseket tartalmazó jelentések](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Biztonság és a tevékenységekre vonatkozó jelentések érhetők el. Biztonsági jelentések megjelenítése megjelölt felhasználók kockázat, és a kockázatos bejelentkezések. Tevékenységre vonatkozó jelentések segítenek jobban átláthatja a szervezeti felhasználók viselkedését részletező bejelentkezési tevékenységeket, és az összes bejelentkezés naplók megadásával. Jelentések segítségével kezelni a kockázatokat, növelheti a hatékonyságot és megfelelőségének figyeléséhez.

| Jelentés típusa | Hozzáférési felülvizsgálat | Biztonsági jelentések | Jelentkezzen be a jelentés |
|-------------|---------------|------------------|----------------|
| Áttekintheti a | Alkalmazásengedélyek és használatát. | Vélhetően feltört fiókok | Ki fér hozzá az alkalmazásokat |
| A lehetséges műveletek | Naplózási hozzáférést. engedélyek visszavonása | Visszavonhatja a hozzáférést. biztonság visszaállítása kényszerítése | Hozzáférés visszavonása |

Az Azure AD 30 napig őrzi meg a legtöbb naplózási adatokat, és elérhetővé teszi az adatok az Azure felügyeleti portálján keresztül vagy az analysis rendszerekbe letölthető egy API-n keresztül.

### <a name="consider-using-microsoft-cloud-application-security"></a>Fontolja meg a Microsoft Cloud Application Security

A Microsoft Cloud App Security (MCAS) egy olyan felhőalapú hozzáférés biztonsági közvetítő (CASB) megoldás. Azt a felhőalapú alkalmazások és szolgáltatások betekintést nyújt, azonosíthatja és kibertámadások elleni kifinomult elemzési biztosít, és segítségével szabályozható, hogyan az adatokat fogadja.

Deploying MCAS teszi lehetővé:

- A Cloud Discovery segítségével leképezése és azonosítása a felhőalapú környezet és a szervezetben használt felhőalkalmazásokat.
- Alkalmazások engedélyezése és az ENSZ-felvállalt alkalmazások a felhőben
- Könnyen telepíthető alkalmazás-összekötők, amelyek kihasználják a szolgáltatói API-k, a láthatósága és irányítása érdekében alkalmazások használata
- Valós idejű átláthatja és szabályozhatja a hozzáférést és a felhőalapú alkalmazások tevékenységét a feltételes hozzáférést biztosító alkalmazás-vezérlő protection használatával
- Folyamatos ellenőrzés a beállítást, és rendszeres finomhangolásával való elősegítésével, szabályzatok segítségével.

A Microsoft Cloud Application Security (MCAS) munkamenet-vezérlő bármely böngészőben bármely operációs rendszeren bármilyen jelentősebb platformon érhető el. A mobilalkalmazások és asztali alkalmazásokat is kell letiltott vagy engedélyezett. Natív módon integrálja az Azure AD-vel, azokat az alkalmazásokhoz, amelyek SAML vannak konfigurálva, illetve alkalmazások Open ID Connect, az egyszeri bejelentkezés az Azure ad-ben támogatja, beleértve a [több kiemelt alkalmazások](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

MCAS kapcsolatos információkért tekintse meg a [áttekintése a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS az előfizetés felhasználó-alapú szolgáltatás. A licencelési részleteit megtekintheti a [MCAS licencelési adatlapját](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Feltételes hozzáférés használata

A feltételes hozzáférés segítségével automatizálhatja a felhőbeli alkalmazások feltételek alapján döntést hozhasson a hozzáférésről.

Feltételes hozzáférési szabályzatai érvényben vannak, az első többtényezős hitelesítés befejezése után. Feltételes hozzáférés, ezért nem készült, mint egy első sor védelmi forgatókönyvek-szolgáltatásmegtagadásos (DDoS-) támadások, például, de használhatja ezeket az eseményeket a jelek általi hozzáférés megállapításához. Ha például a bejelentkezési kockázat szintjének helyét a kérelmet, és így tovább is használható. Feltételes hozzáféréssel kapcsolatos további információkért lásd: [az Áttekintés](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) és a [telepítési tervének](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Egyszeri bejelentkezés az Azure technikai követelmények

Az alábbi szakasz részletesen konfigurálhatja az adott alkalmazást, beleértve a szükséges környezete(i), végpontokat, jogcímek leképezése, szükséges attribútumok, tanúsítványok és használt protokollokat a követelményeknek. Ezt az információt az egyszeri bejelentkezés konfigurálásához lesz szüksége a [Azure AD portálon](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Hitelesítési mechanizmus részletei

Az összes előre integrált SaaS-alkalmazások a Microsoft biztosít egy oktatóanyag, és nem kell ezt az információt. Ha az alkalmazás nem szerepel alkalmazás-piacteréről / katalógus, szükség lehet az alábbi adatokra vonatkozó adatok gyűjtése:

- **Aktuális identitásszolgáltató az alkalmazás egyszeri bejelentkezéshez használja, ha van ilyen** – például: Az AD FS-ben a pingfederate-tel, az Okta
- **A cél alkalmazás által támogatott protokollok** – SAML 2.0, OpenID Connect, OAuth, űrlapos hitelesítés, például WS-Fed, WS-Trust
- **Az Azure ad-ben konfigurált protokoll** – például a SAML 2.0-s vagy 1.1, OpenID Connect, OAuth, a űrlapalapú, WS-Fed

### <a name="attribute-requirements"></a>Attribútum-követelmények

Nincs attribútumokat és az Azure AD felhasználói és minden egyes SaaS-alkalmazás felhasználói objektumok között attribútumleképezések előre konfigurált készletével. Néhány alkalmazás más típusú objektumok, például a csoportok kezelése. Az alkalmazás Azure AD-ből származó felhasználói attribútumok hozzárendelését tervezi, és [az alapértelmezett attribútumleképezések testreszabása](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) üzleti megfelelően kell.

### <a name="certificate-requirements"></a>Tanúsítványkövetelmények

A tanúsítvány az alkalmazás naprakész állapotban kell lennie, vagy annak a kockázata, a felhasználók nem tudnak hozzáférni az alkalmazáshoz. A legtöbb SaaS-alkalmazás tanúsítványok jók 36 hónapos. Az alkalmazás paneljén a tanúsítványt töltött módosíthatja. Ügyeljen arra, hogy a lejárati dokumentum, és tudja, hogyan fogja kezelni a tanúsítvány megújításához. 

Két módon kezelheti a tanúsítványait. 

- **Automatikus tanúsítványváltás** – a Microsoft támogatja [az Azure Active Directory Aláírókulcs](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Bár ez az előnyben részesített módszer a tanúsítványok kezeléséhez, nem minden ISV támogatja az ebben a forgatókönyvben.

- **Manuális frissítése** – minden alkalmazás rendelkezik a saját tanúsítvánnyal, amely lejár alapján hogyan van definiálva. Az alkalmazás tanúsítvány lejárta előtt hozzon létre egy új tanúsítványt, és küldje el az ISV-k. Ez az információ tölthetők az összevonási metaadatokat. [További információ az összevonási metaadatok itt.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Egyszeri bejelentkezés megvalósítása

A következő fázisok segítségével megtervezheti és üzembe helyezheti megoldását, a szervezet:

### <a name="user-configuration-for-sso"></a>Egyszeri bejelentkezés a felhasználói beállítások

- **A test-felhasználóinak azonosítása**

   Lépjen kapcsolatba az alkalmazás tulajdonosának, és hozzon létre legalább három tesztfelhasználók az alkalmazáson belül. Győződjön meg arról, az információt fogja használni az elsődleges azonosító megfelelően fel van töltve, és megfelel-e olyan attribútum, amely az Azure ad-ben érhető el. A legtöbb esetben ez rendelje hozzá a "NameID" SAML-alapú alkalmazásokhoz. A JWT-jogkivonatokkal a "preferred_username."
   
   A felhasználó létrehozása az Azure ad-ben vagy felhőalapú felhasználóként manuálisan, vagy a helyszínről az Azure AD Connect szinkronizálási motor használatával a felhasználó szinkronizálása. Győződjön meg arról, az információ megegyezik a jogcímek, az alkalmazás felé.

- **Egyszeri bejelentkezés konfigurálása**

   Az a [alkalmazások listájának](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), keresse meg és nyissa meg az egyszeri bejelentkezés az oktatóanyag az alkalmazáshoz, majd hajtsa végre az oktatóanyag SaaS-alkalmazás sikeres konfigurálása.

   Ha az alkalmazás nem találja, tekintse meg [egyéni alkalmazás dokumentációját](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Ez végigvezeti a nem található, az Azure AD katalógusából származó alkalmazás hozzáadása.

   Igény szerint használhatja a vállalati alkalmazás használatára vonatkozó SAML-jogkivonatban kiadott jogcímek [Microsoft guidance dokumentációja](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Győződjön meg arról, mi várható az alkalmazás SAML-válasz fogadása képez le. Ha problémái vannak a konfiguráció során, használjon ügyfeleinket a [SSO hibakeresése integrációs hogyan](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

Egyéni alkalmazás-előkészítés az Azure AD Premium P1 vagy P2 szintű licencek szolgáltatása.

### <a name="provide-sso-change-communications-to-end-users"></a>Adja meg az egyszeri bejelentkezés módosítása kommunikáció a végfelhasználók számára

A kommunikációs terv megvalósításához. Ellenőrizze, hogy még így tudja, hogy változás várható, ha megérkezik, mi a teendő most, és hogyan kérjen segítséget a végfelhasználóknak.

### <a name="verify-end-user-scenarios-for-sso"></a>Ellenőrizze a végfelhasználó forgatókönyvek egyszeri bejelentkezéshez

A következő esetek segítségével teszteket a vállalat által birtokolt és a saját eszközét, győződjön meg, hogy az egyszeri bejelentkezés konfigurációkat a várt módon működnek. Az alábbi forgatókönyvek feltételezik, hogy a felhasználó ellenőrizheti, hogy egy alkalmazás URL-CÍMÉT és egy hitelesítési folyamat által a szolgáltató (Szolgáltató által kezdeményezett hitelesítési folyamatok) keresztül.

| Forgatókönyv | Felhasználó által a Szolgáltató által kezdeményezett hitelesítési folyamatok a várt eredmény |
|----------|---------------------------------------------------|
| Jelentkezzen be az Internet Explorer, a vállalati hálózaton kívüli alkalmazáshoz. | Integrált Windows-hitelesítés (IWA) további beavatkozás nélküli történik. |
| Jelentkezzen be az Internet Explorer ki az új bejelentkezési kísérlet corpnet alkalmazás. | Űrlapalapú üzenetet, az AD FS-kiszolgálón. A felhasználó sikeresen bejelentkezik, és a böngésző megkéri az MFA-hoz. |
| Jelentkezzen be az Internet Explorer – corpnet az aktuális munkamenet ki az alkalmazást, és soha nem hajtott végre MFA. | Felhasználó nem kap első tényező kérése. Felhasználó kérdezzen rá a multi-factor Authentication kap. |
| Jelentkezzen be az Internet Explorer ki az aktuális munkamenet corpnet alkalmazás és a munkamenet már végrehajtotta az MFA. | Felhasználó nem kap első tényező kérése. Felhasználó nem kap a többtényezős hitelesítés. Felhasználói SSO alkalmazásba. |
| Bejelentkezés alkalmazásba a Chrome és a Firefox/Safari ki az aktuális munkamenet a vállalati hálózat és a munkamenet már végrehajtotta az MFA. | Felhasználó nem kap első tényező kérése. Felhasználó nem kap a többtényezős hitelesítés. Felhasználói egyszeri bejelentkezés az alkalmazásba. |
| Bejelentkezési a Chrome és a Firefox/Safari ki az új bejelentkezési kísérlet a vállalati hálózaton kívüli alkalmazásba. | Űrlapalapú üzenetet, az AD FS-kiszolgálón. A felhasználó sikeresen bejelentkezik, és a böngésző megkéri az MFA-hoz. |
| Bejelentkezés alkalmazásba a Chrome és a Firefox a vállalati hálózaton az aktuális munkamenet. | Felhasználó nem kap első tényező kérése. Felhasználó nem kap a többtényezős hitelesítés. Felhasználói egyszeri bejelentkezés az alkalmazásba. |
| Jelentkezzen be az alkalmazás mobil alkalmazás egy új bejelentkezési kísérlet után az alkalmazás. | Űrlapalapú üzenetet, az AD FS-kiszolgálón. A felhasználó sikeresen bejelentkezik, és ADAL ügyfél bekéri a multi-factor Authentication. |
| Nem jogosult felhasználó próbál bejelentkezni az alkalmazás bejelentkezési URL-címmel. | Űrlapalapú üzenetet, az AD FS-kiszolgálón. Felhasználó nem tud első tényező adatokkal való bejelentkezést. |
| Jogosult felhasználó megpróbál bejelentkezni, de kell írnia egy helytelen jelszót. | Felhasználó alkalmazás URL-Címének navigál, és megkapja a rossz felhasználónév/jelszó hiba. |
| Hitelesített felhasználó egy e-mailben szereplő hivatkozásra kattint, és már hitelesítve van. | Felhasználói URL-cím kattint, és alá van írva, nincs további utasításokat az alkalmazásba. |
| Jogosult felhasználó egy e-mailben szereplő hivatkozásra kattint, és még nem lehet hitelesíteni. | Felhasználó URL-cím kattint, és a hitelesítés első tényezője a parancssorba. |
| Jogosult felhasználói naplók alkalmazás mobilalkalmazással alkalmazásba (SP által kezdeményezett) az új bejelentkezési kísérlet. | Űrlapalapú üzenetet, az AD FS-kiszolgálón. A felhasználó sikeresen bejelentkezik, és ADAL ügyfél bekéri a multi-factor Authentication. |
| Jogosulatlan felhasználó megpróbál bejelentkezni az alkalmazás bejelentkezési URL-címmel (SP által kezdeményezett). | Űrlapalapú üzenetet, az AD FS-kiszolgálón. Felhasználó nem tud első tényező adatokkal való bejelentkezést. |
| Jogosult felhasználó megpróbál bejelentkezni, de kell írnia egy helytelen jelszót.| Felhasználó alkalmazás URL-Címének navigál, és megkapja a rossz felhasználónév/jelszó hiba. |
| Hitelesített felhasználó kijelentkezik, és ezután újra bejelentkezik. | Ha kijelentkezéses URL-cím van beállítva, a felhasználó a tartományon kívül az összes szolgáltatás és a kérés hitelesítéséhez be van jelentkezve. |
| Hitelesített felhasználó kijelentkezik, és ezután újra bejelentkezik. | Kijelentkezéses URL-címe nincs beállítva, ha a felhasználó automatikusan bekerülnek vissza a meglévő Azure ad-ben böngésző-munkamenettel meglévő token használatával. |
| Hitelesített felhasználó egy e-mailben szereplő hivatkozásra kattint, és már hitelesítve van. | Felhasználói URL-cím kattint, és alá van írva, nincs további utasításokat az alkalmazásba. |
| Jogosult felhasználó egy e-mailben szereplő hivatkozásra kattint, és még nem lehet hitelesíteni. | Felhasználó URL-cím kattint, és a hitelesítés első tényezője a parancssorba. |

## <a name="manage-sso"></a>Egyszeri bejelentkezés kezelése

Ez a szakasz ismerteti a követelmények és javaslatok tudják kezelni az egyszeri Bejelentkezést.

### <a name="required-administrative-roles"></a>Szükséges felügyeleti szerepkörök

A szerepkör mindig használata a legkevesebb engedélyek érhetők el a szükséges feladatnak az Azure Active Directoryban. A Microsoft javasolja [tekintse át a különböző szerepkörök elérhető](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) és megoldani az igényeinek, az alkalmazás minden egyes személy számára jobb közül választhat. Egyes szerepkörök ideiglenesen a alkalmazni, és eltávolítja a központi telepítés befejezése után szükség lehet.

| Személy| Szerepkörök | Az Azure AD-szerepkörhöz (ha szükséges) |
|--------|-------|-----------------------------|
| Ügyfélszolgálati rendszergazdai | 1\. rétegbeli támogatása | None |
| Identitás-rendszergazda | Konfigurálja és hibakeresése során problémák hatással van az Azure ad-ben | Globális rendszergazda |
| Alkalmazás-rendszergazda | Felhasználói igazolási alkalmazásban, a konfigurációt a engedélyekkel rendelkező felhasználók | None |
| Infrastruktúra-rendszergazda | Tanúsítvány helyettesítő tulajdonosa | Globális rendszergazda |
| Üzleti tulajdonosa/projektmenedzsment | Felhasználói igazolási alkalmazásban, a konfigurációt a engedélyekkel rendelkező felhasználók | None |

Azt javasoljuk, [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) számára a szerepkörök további naplózást, ellenőrzési és hozzáférési tekintse át a directory engedélyekkel rendelkező felhasználók számára.

### <a name="sso-certificate-lifecycle-management"></a>Tanúsítvány-életciklus felügyelete egyszeri Bejelentkezéssel

Fontos, hogy azonosítsa a megfelelő szerepköreivel és e-mail-terjesztési listák biztosítja az Azure AD között az aláíró tanúsítvány életciklusának kezeléséről és az egyszeri bejelentkezést a konfigurálni kívánt alkalmazás. Íme néhány a kulcsfontosságú szerepköröket, javasoljuk, hogy teljesülnek:

- Az alkalmazás felhasználói tulajdonságok frissítése tulajdonosa
- Tulajdonos telefonos alkalmazás hibajavítási támogatás
- A tanúsítványokkal kapcsolatos értesítések szorosan figyelt e-mail terjesztési lista

A tanúsítvány maximális élettartamát három év meghatározva. Javasoljuk, hogy hogyan fogja kezelni az Azure AD között tanúsítvány módosítása a folyamat létrehozó és az alkalmazás. Ez segít megakadályozza vagy minimalizálása érdekében egy lejárt tanúsítvány miatt kimaradás vagy tanúsítványváltás kényszerítése.

### <a name="rollback-process"></a>Visszaállítás folyamatban

Miután befejezte a tesztelést, a vizsgálati esetek alapján, fontos helyezi át az alkalmazását az éles környezetbe. Éles környezetben való jelenti lesz a tervezett és tesztelt konfigurációk megvalósításához az éles környezetbeli bérlőhöz, és megkezdik a felhasználók számára. Fontos azonban, tervezze meg, mi a teendő abban az esetben a telepítés nem fogadja a tervek szerint. Ha az egyszeri bejelentkezés konfigurálása sikertelen az üzembe helyezés során, ismernie kell a bármilyen kimaradásról csökkentése és a felhasználókra gyakorolt hatást.

Hitelesítési módszerek az alkalmazáson belül rendelkezésre állását a legjobb stratégiáját határozza meg. Mindig biztosítsa az alkalmazástulajdonosok pontosan az, hogy az eredeti bejelentkezési szolgáltatás konfigurációs állapotát abban az esetben, ha a központi telepítés futtatása problémákat hogyan dokumentációját részletes rendelkeznek.

- **Ha az alkalmazás támogatja több identitásszolgáltató**, például LDAP és az AD FS és a Ping, ne törölje a meglévő egyszeri bejelentkezési konfiguráció bevezetés során. Ehelyett tiltsa le a migrálás során abban az esetben át kell váltania, vissza később. 

- **Ha az alkalmazás nem támogatja több identitásszolgáltató használatát** , de lehetővé teszi a felhasználóknak jelentkezzen be az űrlapalapú hitelesítést (felhasználónév és jelszó), győződjön meg arról, hogy felhasználók visszatérhet Ez a megközelítés abban az esetben az új egyszeri bejelentkezési konfiguráció bevezetésének sikertelen lesz.

### <a name="access-management"></a>Hozzáférés-kezelés

Azt javasoljuk, hogy méretezett megközelítés kiválasztása, amikor erőforrásokhoz való hozzáférés kezelése. Általános megközelítéseket tartalmazza a helyi csoportok használó Azure AD connectben való szinkronizálásával [dinamikus csoportok létrehozása az Azure ad-ben a felhasználói attribútumok alapján](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), vagy a létrehozás [önkiszolgáló csoportok](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) az Azure ad-ben kezeli az erőforrás tulajdonosa.

### <a name="monitor-security"></a>A figyelő biztonsági

Azt javasoljuk, amelyben a különböző SaaS-alkalmazásbiztonság aspektusait tekintse át a bevezetésére beállítását, és végezze el a szükséges korrekciós műveleteket.

### <a name="troubleshooting"></a>Hibaelhárítás

Az alábbi hivatkozások a jelen hibaelhárítási forgatókönyveket. Előfordulhat, hogy szeretne létrehozni egy adott útmutató a támogató személyzete számára, amely tartalmazza ezeket a forgatókönyveket, és kijavíthatja azokat a lépéseket.

#### <a name="consent-issues"></a>Jóváhagyás kapcsolatos problémák

- [Váratlan beleegyezést kérő hiba](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Hiba a felhasználói jóváhagyással](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Bejelentkezési problémák

- [Egy egyéni portálról bejelentkezésnél](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Probléma a hozzáférési panelről való bejelentkezésnél](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Hiba az alkalmazás bejelentkezési oldalán](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [A probléma jelentkezik be egy Microsoft-alkalmazásba](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Szerepel az Azure Alkalmazásgyűjteményben alkalmazások egyszeri bejelentkezési problémák

- [A probléma a jelszavas egyszeri Bejelentkezést az alkalmazások az Azure Alkalmazásgyűjteményben szerepel](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [A probléma az összevont egyszeri bejelentkezés az Azure-alkalmazás katalógusában szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Az Azure Alkalmazáskatalógusában nem szereplő alkalmazások egyszeri bejelentkezési problémák

- [A probléma a jelszavas egyszeri Bejelentkezést az Azure Alkalmazáskatalógusában nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [A probléma az összevont egyszeri bejelentkezés az Azure Alkalmazáskatalógusában nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>További lépések

[SAML-alapú SSO hibakeresése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Jogcím a következő PowerShell-alkalmazások hozzárendelése](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[SAML-jogkivonatban kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Egyszeri kijelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Az Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (a külső felhasználók, partnerek és szállítók például)

[Az Azure ad-beli feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Egyszeri bejelentkezéses hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Egyszeri bejelentkezés vonatkozó oktatóanyag](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
