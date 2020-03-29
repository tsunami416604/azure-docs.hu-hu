---
title: Az Azure Active Directory egyszeri bejelentkezésének megtervezése
description: Útmutató az SSO megtervezéséhez, üzembe helyezéséhez és kezeléséhez a szervezetben.
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
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512827"
---
# <a name="plan-a-single-sign-on-deployment"></a>Egyszeri bejelentkezés üzembe helyezésének tervezése

Egyszeri bejelentkezés (SSO) azt jelenti, hogy a felhasználó által igényelt összes alkalmazás és erőforrás elérése, ha csak egyszer jelentkezik be egyetlen felhasználói fiók használatával. Az Egyszeri rendelés sel a felhasználók hozzáférhetnek az összes szükséges alkalmazáshoz anélkül, hogy másodszor is hitelesíteniük kell őket.

## <a name="benefits-of-sso"></a>Az SSO előnyei

Egyszeri bejelentkezés (SSO) növeli a biztonságot és a kényelmet, amikor a felhasználók bejelentkeznek az alkalmazások az Azure Active Directoryban (Azure AD). 

Számos szervezet támaszkodik a szoftverek szolgáltatásként (SaaS) alkalmazásokra, például az Office 365, a Box és a Salesforce alkalmazásokra a végfelhasználók hatékonyságának növelése érdekében. Korábban az informatikai személyzetnek egyénileg kellett létrehoznia és frissítenie a felhasználói fiókokat az egyes SaaS-alkalmazásokban, és a felhasználóknak mindegyikhez emlékezniük kellett egy jelszóra.

Az Azure Marketplace több mint 3000 alkalmazással rendelkezik, amelyek előre integrált egyszeri bejelentkezési kapcsolatokkal rendelkeznek, így egyszerűen integrálhatja őket a bérlőbe.

## <a name="licensing"></a>Licencek

- **Azure AD-licencelés** – Egyszeri szolgáltató az előre integrált SaaS-alkalmazások ingyenes. A címtárban lévő objektumok száma és a telepíteni kívánt szolgáltatások azonban további licenceket igényelhetnek. A licenckövetelmények teljes listáját az [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/).
- **Alkalmazáslicencelés** – Az üzleti igényekkielégítéséhez szüksége lesz a Megfelelő licencre a SaaS-alkalmazásokhoz. Az alkalmazás tulajdonosával együttműködve állapítsa meg, hogy az alkalmazáshoz rendelt felhasználók rendelkeznek-e az alkalmazáson belüli szerepköreikhez szükséges licencekkel. Ha az Azure AD kezeli az automatikus kiépítés szerepkörök alapján, az Azure AD-ben hozzárendelt szerepköröknek igazodniuk kell az alkalmazáson belül birtokolt licencek számához. Az alkalmazásban található licencek nem megfelelő száma hibákhoz vezethet a felhasználó kiépítése/frissítése során.

## <a name="plan-your-sso-team"></a>Tervezze meg az SSO csapatát

- **A megfelelő érdekelt felek bevonása** – Ha a technológiai projektek kudarcot vallanak, az általában a hatásra, az eredményekre és a felelősségre vonatkozó, nem megfelelő elvárásoknak köszönhető. A buktatókat elkerülve [győződjön meg arról, hogy a megfelelő érdekelt feleket vonja be,](https://aka.ms/deploymentplans) és hogy az érdekelt felek megértsék a szerepüket.
- **Terv kommunikáció** - Kommunikáció elengedhetetlen a siker minden új szolgáltatás. Proaktív módon tájékoztassa a felhasználókat arról, hogy a felhasználói élményük hogyan változik, mikor fog változni, és hogyan szerezhet támogatást, ha problémákat tapasztalnak. Tekintse át, hogy a [végfelhasználók hogyan férhetnek hozzá az Egyszeri eladósodás-kompatibilis alkalmazásaikhoz,](end-user-experiences.md)és a kiválasztott beállításoknak megfelelően alakítsa ki a kommunikációt. 

## <a name="plan-your-sso-protocol"></a>Az SSO protokoll megtervezése

Az összevonási protokollokon alapuló sso implementáció javítja a biztonságot, a megbízhatóságot és a végfelhasználói élményt, és könnyebben megvalósítható. Számos alkalmazás előre be van építve az Azure [AD-be, és elérhető a lépésről lépésre használható útmutatók.](../saas-apps/tutorial-list.md) Ezeket az [Azure Piactéren](https://azuremarketplace.microsoft.com/marketplace/)találja. Az egyes egyszeri bejelentkezési módszerekrészletes információi az [Azure Active Directoryban lévő alkalmazásokra való egyszeri bejelentkezés](what-is-single-sign-on.md)című cikkben találhatók.

Két elsődleges módja van annak, hogy a felhasználók egyszeri bejelentkezést tegyenek lehetővé az alkalmazásokba:

- **Összevont egyszeri bejelentkezéssel** Az Azure AD hitelesíti a felhasználót az alkalmazás az Azure AD-fiók használatával. Ez a módszer olyan alkalmazások esetében támogatott, amelyek támogatják az SAML 2.0, a WS-Federation vagy az OpenID Connect protokollokat, és az egyszeri bejelentkezés leggazdagabb módja. Azt javasoljuk, hogy az összevont egyszeri bejelentkezés t az Azure AD, ha egy alkalmazás támogatja, jelszóalapú egyszeri bejelentkezés és ADFS helyett.

- **A jelszóalapú egyszeri bejelentkezéssel a** felhasználók először jelentkeznek be az alkalmazásba felhasználónévvel és jelszóval, így hozzáférhetnek. Az első bejelentkezés után az Azure AD biztosítja a felhasználónevet és a jelszót az alkalmazáshoz. A jelszóalapú egyszeri bejelentkezés lehetővé teszi az alkalmazásjelszó biztonságos tárolását és visszajátszását egy webböngésző-bővítmény vagy mobilalkalmazás használatával. Ez a beállítás az alkalmazás által biztosított meglévő bejelentkezési folyamatot használja, lehetővé teszi a rendszergazda számára a jelszavak kezelését, és nem követeli meg a felhasználótól a jelszó ismeretét.

### <a name="considerations-for-federation-based-sso"></a>Az összevonási alapú egyszeri szolgáltatásokkal kapcsolatos szempontok

- **OpenID Connect és OAuth** használatával – Ha az alkalmazás, amelyhez csatlakozik, támogatja, használja az OIDC/OAuth 2.0 metódust az egyszeri használatú felhasználó engedélyezéséhez az adott alkalmazáshoz. Ez a módszer kevesebb konfigurációt igényel, és gazdagabb felhasználói élményt tesz lehetővé. További információ: [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)és [Azure Active Directory fejlesztői útmutató.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
- **Végpontkonfigurációk SAML-alapú egyszeri szolgáltatáshoz** – SAML használata esetén a fejlesztőknek az alkalmazás konfigurálása előtt konkrét információkra lesz szükségük. További információ: [Az egyszerű SAML-konfiguráció szerkesztése.](configure-single-sign-on-non-gallery-applications.md)
- **Tanúsítványkezelés saml-alapú egyszeri szolgáltató** – Ha engedélyezi az összevont egyszeri egyszeri szolgáltató az alkalmazáshoz, az Azure AD létrehoz egy tanúsítványt, amely alapértelmezés szerint három évig érvényes. Szükség esetén testreszabhatja a tanúsítvány lejárati dátumát. Győződjön meg arról, hogy rendelkezik a tanúsítványok lejárat előtti megújítására szolgáló folyamatokkal. További információ: [Azure AD managing certificates](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>A jelszóalapú egyszeri bejelentkezésekkel kapcsolatos szempontok

Az Azure AD használata a jelszóalapú egyszeri bejelentkezéshez olyan böngészőbővítmény telepítését igényel, amely biztonságosan lekéri a hitelesítő adatokat, és kitölti a bejelentkezési űrlapokat. Definiáljon egy mechanizmust a bővítmény nagy méretekben történő üzembe helyezéséhez [a támogatott böngészőkkel.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) A lehetőségek a következők:

- [Az Internet Explorer csoportházirendje](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Az Internet Explorer konfigurációkezelője](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Felhasználó által vezérelt letöltés és konfiguráció Chrome, Firefox, Microsoft Edge vagy IE esetén](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

További információ: [A jelszó egyszeri bejelentkezésbeállítása](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)című témakörben olvashat.

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Bejelentkezési űrlapok metaadatainak rögzítése olyan alkalmazásokhoz, amelyek nem vannak a katalógusban

A Microsoft támogatja a metaadatok rögzítését egy webes alkalmazáson a jelszótárolóhoz (a felhasználónév és a jelszó mezők rögzítését). Keresse meg a bejelentkezési URL-címet az alkalmazás nak az űrlapok metaadatainak rögzítésére történő konfigurálása során. Kérdezze meg az alkalmazás tulajdonosát a pontos bejelentkezési URL-t. Ez eket az adatokat a bejelentkezési folyamat során használja fel, leképezve az Azure AD hitelesítő adatait az alkalmazáshoz bejelentkezés közben.

További információ: [Mi az alkalmazás-hozzáférés és az Egyszeri bejelentkezés az Azure AD-vel? – Jelszóalapú egyszeri bejelentkezés.](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Az űrlapokmetaadatait vissza kell ragadni

Amikor az alkalmazások megváltoztatják a HTML-elrendezésüket, előfordulhat, hogy a módosításokhoz való alkalmazkodáshoz újra rögzítenie kell a metaadatokat. A HTML-elrendezés változását jelző gyakori tünetek a következők:

- Felhasználók jelentés, hogy kattintson az alkalmazás lesz "beragadt" a bejelentkezési oldalon
- Felhasználók, akik azt jelentik, hogy a felhasználónév vagy a jelszó nincs feltöltve

#### <a name="shared-accounts"></a>Közös fiókok

A bejelentkezési szempontból a megosztott fiókkal rendelkező alkalmazások nem különböznek a katalógus alkalmazás, amely jelszó egyszeri bejelentkezést használ az egyes felhasználók számára. A megosztott fiókok használatára szánt alkalmazások tervezése és konfigurálása során azonban további lépésekre van szükség:

1. Az alkalmazás üzleti felhasználóival a következők dokumentálása:
   1. Az alkalmazás használóinak felhasználói nak készlete
   1. A felhasználók készletéhez társított alkalmazás meglévő hitelesítő adatai 
1. A felhasználói készlet és a hitelesítő adatok minden egyes kombinációjához hozzon létre egy biztonsági csoportot a felhőben vagy a helyszíni igényeinek megfelelően.
1. Állítsa alaphelyzetbe a megosztott hitelesítő adatokat. Miután az alkalmazás telepítve van az Azure AD-ben, az egyének nem kell a jelszót a megosztott fiók. Mivel az Azure AD tárolja a jelszót, fontolja meg, hogy nagyon hosszú és összetett. 
1. Konfigurálja a jelszó automatikus átgörgetését, ha az alkalmazás támogatja azt. Így még a kezdeti telepítést végző rendszergazda sem fogja tudni a megosztott fiók jelszavát. 

## <a name="plan-your-authentication-method"></a>A hitelesítési módszer megtervezése

A megfelelő hitelesítési módszer kiválasztása egy fontos első döntés az Azure AD hibrid identitáskezelési megoldás beállítása. Valósítsa meg a hitelesítési módszert, amely az Azure AD Connect használatával konfigurálva van, amely a felhőben lévő felhasználókat is biztosít.

A hitelesítési módszer kiválasztásához figyelembe kell vennie a választás idejét, meglévő infrastruktúráját, összetettségét és költségét. Ezek a tényezők minden szervezetesetében eltérőek, és idővel változhatnak. Válassza ki azt, amelyik a leginkább megfelel az adott forgatókönyvnek. További információ: [Válassza ki a megfelelő hitelesítési módszert az Azure Active Directory hibrid identitáskezelési megoldásához.](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)

## <a name="plan-your-security-and-governance"></a>Tervezze meg biztonságát és irányítását 

Az identitás a biztonsági figyelem és a beruházások új elsődleges fordulója, mivel a hálózat peremvonalai egyre porózusabbá és kevésbé hatékonyakká váltak a BYOD-eszközök és a felhőalapú alkalmazások robbanásszerű robbanásával. 

### <a name="plan-access-reviews"></a>Hozzáférési felülvizsgálatok tervezése

[Az Access-felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) lehetővé teszik a szervezetek számára a csoporttagságok, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések hatékony kezelését. Rendszeresen ellenőrizze a felhasználói hozzáférést, hogy csak a megfelelő személyek férhessenek hozzá.

A hozzáférési felülvizsgálatok beállítása során megtervezésre váró legfontosabb témakörök a következők:

1. A vállalkozásigénynek megfelelő hozzáférési ellenőrzések ütemét azonosítja. Ez lehet olyan gyakori, mint hetente egyszer, havonta, évente, vagy egy on-demand gyakorlat.

1. Hozzon létre olyan csoportokat, amelyek az alkalmazás-hozzáférési jelentések ellenőrzőit képviselik. Biztosítania kell, hogy az alkalmazás és a megcélzott felhasználók és a használati esetek tekintetében leginkább ismert érdekelt felek részt vegyenek a hozzáférési felülvizsgálatokban.

1. A hozzáférési felülvizsgálat elvégzése magában foglalja az alkalmazáshozzáférési engedélyek elvételét azon felhasználók számára, akiknek már nincs szükségük hozzáférésre. Tervezze meg a visszautasított felhasználók potenciális támogatási kérelmeinek kezelését. A törölt felhasználó 30 napig törlődik az Azure AD-ben, amely idő alatt szükség esetén a rendszergazda visszaállíthatja őket. A felhasználók 30 nap után véglegesen törlődnek. Az Azure Active Directory portál használatával a globális rendszergazda explicit módon véglegesen törölheti a nemrég törölt felhasználót, mielőtt ezt az időszakot elérné.

### <a name="plan-auditing"></a>Tervezés naplózása

Az Azure AD [technikai és üzleti elemzéseket tartalmazó jelentéseket](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)tartalmaz. 

Mind a biztonsági, mind a tevékenységjelentések elérhetők. A biztonsági jelentések a kockázatra megjelölt felhasználókat és a kockázatos bejelentkezéseket jelenítik meg. A tevékenységjelentések segítenek megérteni a szervezet felhasználóinak viselkedését a bejelentkezési tevékenység részletezésével és az összes bejelentkezés naplózási nyomvonalának biztosításával. A jelentések segítségével kezelheti a kockázatokat, növelheti a termelékenységet, és figyelheti a megfelelőséget.

| Jelentés típusa | Hozzáférés felülvizsgálata | Biztonsági jelentések | Bejelentkezési jelentés |
|-------------|---------------|------------------|----------------|
| Véleményezésre használható | Alkalmazásengedélyek és -használat. | Potenciálisan feltört fiókok | Ki fér hozzá az alkalmazásokhoz? |
| Lehetséges intézkedések | Ellenőrzési hozzáférés; engedélyek visszavonása | Hozzáférés visszavonása; kényszerítési biztonság visszaállítása | Hozzáférés visszavonása |

Az Azure AD 30 napig megőrzi a legtöbb naplózási adatot, és elérhetővé teszi az adatokat az Azure felügyeleti portálon vagy egy API-n keresztül, amelyet letölthet az elemző rendszerekbe.

### <a name="consider-using-microsoft-cloud-application-security"></a>Fontolja meg a Microsoft Cloud Alkalmazásbiztonság használatát

A Microsoft Cloud App Security (MCAS) egy Cloud Access Security Broker (CASB) megoldás. Betekintést nyújt a felhőalapú alkalmazásokba és -szolgáltatásokba, kifinomult elemzéseket biztosít a kiberfenyegetések azonosításához és leküzdéséhez, és lehetővé teszi az adatok utazásának szabályozását.

Az MCAS telepítése lehetővé teszi a következőket:

- A Cloud Discovery segítségével leképezheti és azonosíthatja a felhőkörnyezetet és a szervezet által használt felhőalkalmazásokat.
- Az alkalmazások szankcionálása és szankcionálásának visszavonása a felhőben
- A szolgáltatói API-k előnyeit kihasználó, könnyen telepíthető alkalmazásösszekötők használata a hozzá kapcsolódó alkalmazások láthatósága és irányítása érdekében
- A feltételes hozzáférés-hozzáférés-vezérlési védelem használatával valós idejű láthatóságot és a felhőalapú alkalmazásokon belüli hozzáférés és tevékenységek vezérléséhez férhet hozzá
- A házirendek beállításával, majd folyamatos finomhangolásával folyamatos vezérlést biztosít.

A Microsoft Cloud Application Security (MCAS) munkamenet-vezérlő bármely böngészőben elérhető bármely operációs rendszer bármely fő platformján. A mobilalkalmazások és az asztali alkalmazások is letilthatók vagy engedélyezhetők. Az Azure AD-vel való natív integrációval az SAML-lel vagy az Open ID Connect alkalmazásokkal konfigurált alkalmazások az Azure AD-ben, köztük [számos kiemelt alkalmazás](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)is támogatott.

Az MCAS-ről a [Microsoft Cloud App Security áttekintése](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)című témakörben olvashat. Az MCAS egy felhasználó-alapú előfizetési szolgáltatás. A licencelés részleteit az [MCAS licencelési adatlapján tekintheti meg.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)

### <a name="use-conditional-access"></a>Feltételes hozzáférés használata

A Feltételes hozzáférés segítségével automatizálhatja a felhőalapú alkalmazások feltételalapú hozzáférés-vezérlési döntéseit.

A feltételes hozzáférési házirendek az első tényezős hitelesítés befejezése után lépnek érvénybe. Ezért a feltételes hozzáférés nem célja, mint az első vonalvédelmi forgatókönyvek, például a szolgáltatásmegtagadási (DoS) támadások, de használhatja az ilyen események ből származó jeleket a hozzáférés meghatározásához. Például a bejelentkezési kockázati szint, a kérelem helye, és így tovább használható. A feltételes hozzáférésről további információt [az áttekintésben](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) és a [telepítési tervben](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)talál.

## <a name="azure-sso-technical-requirements"></a>Az Azure SSO műszaki követelményei

A következő szakasz ismerteti az adott alkalmazás konfigurálásának követelményeit, beleértve a szükséges környezet(ek),végpontok, jogcímleképezés, szükséges attribútumok, tanúsítványok és a használt protokollok. Erre az információra szüksége lesz az SSO konfigurálásához az [Azure AD portálon.](https://portal.azure.com/)

### <a name="authentication-mechanism-details"></a>A hitelesítési mechanizmus részletei

Az összes előre integrált SaaS-alkalmazás esetében a Microsoft oktatóanyagot biztosít, és nem lesz szüksége ezekre az információkra. Ha az alkalmazás nem az alkalmazás piactéren / galériában, akkor lehet, hogy össze kell gyűjtenie a következő adatokat:

- **Az alkalmazás által az Egyszeri használathoz használt jelenlegi identitásszolgáltató, ha alkalmazható** – Például: AD FS, PingFederate, Okta
- **A célalkalmazás által támogatott protokollok** – például SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Az Azure AD-vel konfigurált protokoll** – például SAML 2.0 vagy 1.1, OpenID Connect, OAuth, Forms-based, WS-Fed

### <a name="attribute-requirements"></a>Attribútumkövetelmények

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között egy előre konfigurált attribútum- és attribútum-leképezési készlet található. Egyes alkalmazások más típusú objektumokat, például csoportokat kezelnek. Tervezze meg a felhasználói attribútumok leképezését az Azure AD-től az alkalmazáshoz, és [szabja testre az alapértelmezett attribútum-leképezéseket az](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) üzleti igényeinek megfelelően.

### <a name="certificate-requirements"></a>Tanúsítványkövetelmények

Az alkalmazás tanúsítványának naprakésznek kell lennie, vagy fennáll annak a veszélye, hogy a felhasználók nem férnek hozzá az alkalmazáshoz. A legtöbb SaaS-alkalmazástanúsítvány 36 hónapig jó. Módosítja a tanúsítvány időtartamát az alkalmazáspanelen. Győződjön meg arról, hogy dokumentálja a lejárati, és tudja, hogyan fogja kezelni a tanúsítvány megújítása. 

A tanúsítványok kétféleképpen kezelhetők. 

- **Automatikus tanúsítványváltás** – A Microsoft támogatja [az aláíró kulcsváltást az Azure AD-ben.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover) Bár ez a tanúsítványok kezeléséhez előnyben részesített módszer, nem minden isv támogatja ezt a forgatókönyvet.

- **Manuális frissítés** – Minden alkalmazás saját tanúsítvánnyal rendelkezik, amely a definiálása alapján lejár. Az alkalmazás tanúsítványának lejárta előtt hozzon létre egy új tanúsítványt, és küldje el az isv-nek. Ez az információ lekérése az összevonási metaadatokból. [Az összevonási metaadatokról itt olvashat bővebben.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Eszköz- és egysta

A következő fázisok segítségével tervezze meg és telepítse a megoldást a szervezetben:

### <a name="user-configuration-for-sso"></a>Az SSO felhasználói konfigurációja

- **A tesztfelhasználók azonosítása**

   Lépjen kapcsolatba az alkalmazás tulajdonosával, és kérje meg őket, hogy hozzanak létre legalább három tesztfelhasználót az alkalmazáson belül. Győződjön meg arról, hogy az elsődleges azonosítóként használni kívánt adatok megfelelően vannak feltöltve, és megfelelnek az Azure AD-ben elérhető attribútumnak. A legtöbb esetben ez az SAML-alapú alkalmazások "NameID"-jéhez lesz leképezve. A JWT tokenek, ez a "preferred_username."
   
   Hozza létre a felhasználót az Azure AD-ben manuálisan felhőalapú felhasználóként, vagy szinkronizálja a felhasználót a helyszíni az Azure AD Connect szinkronizálási motor használatával. Győződjön meg arról, hogy az adatok megegyeznek az alkalmazásnak küldött jogcímek.

- **SSO konfigurálása**

   Az [alkalmazások listájából](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)keresse meg és nyissa meg az alkalmazás SSO oktatóanyagát, majd kövesse az oktatóanyag lépéseit a SaaS-alkalmazás sikeres konfigurálásához.

   Ha nem találja az alkalmazást, olvassa el az [Egyéni alkalmazás dokumentációját.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) Ez bemutatja, hogyan vehet fel egy alkalmazást, amely nem található az Azure AD-gyűjteményben.

   A [Microsoft útmutató dokumentációjának](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)használatával az SAML-jogkivonatban kiadott jogcímek a vállalati alkalmazáshoz is használhatók. Győződjön meg arról, hogy ez leképezi, hogy mit vár kapni az SAML választ az alkalmazáshoz. Ha problémákat tapasztal a konfiguráció során, használja az [SSO-integráció hibakeresésével](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)kapcsolatos útmutatónkat.

Egyéni alkalmazás onboarding egy Azure AD Premium P1 vagy P2 licencek funkció.

### <a name="provide-sso-change-communications-to-end-users"></a>Az SSO-módosítási kommunikáció biztosítása a végfelhasználók nak

Valósítsa meg kommunikációs tervét. Győződjön meg arról, hogy tudatja a végfelhasználókkal, hogy változás következik be, mikor érkezik meg, mi a teendő most, és hogyan kérhet segítséget.

### <a name="verify-end-user-scenarios-for-sso"></a>Végfelhasználói forgatókönyvek ellenőrzése az SSO-hoz

A következő tesztesetek segítségével végezhet teszteket a vállalati tulajdonú és személyes eszközökön annak érdekében, hogy az egyszeri szolgáltatás konfigurációi a várt módon működjenek. Az alábbi esetek feltételezik, hogy a felhasználó egy alkalmazás URL-címére navigál, és a szolgáltató által kezdeményezett hitelesítési folyamaton (SP által kezdeményezett hitelesítési folyamaton) halad keresztül.

| Forgatókönyv | Az SP által kezdeményezett hitelesítési folyamat várt eredménye a felhasználó szerint |
|----------|---------------------------------------------------|
| Jelentkezzen be az alkalmazásba az IE-vel a testhálózaton. | Az integrált Windows-hitelesítés (IWA) további kérések nélkül történik. |
| Bejelentkezés az alkalmazásba az IE-vel, miközben ki van kapcsolva a testhálózaton új bejelentkezési kísérlettel. | Űrlapalapú kérdés az AD FS-kiszolgálón. A felhasználó sikeresen bejelentkezik, és a böngésző kéri az MFA-t. |
| Jelentkezzen be az alkalmazásba az IE-vel, miközben nincs testhálózati egy aktuális munkamenettel, és soha nem végzett többfa-t. | A felhasználó nem kap rá figyelmeztetést az első tényezőről. A felhasználó figyelmeztetést kap az MFA-hoz. |
| Jelentkezzen be az alkalmazásba az IE-vel, miközben nincs testhálózati egy aktuális munkamenettel, és már végrehajtotta az MFA-t ebben a munkamenetben. | A felhasználó nem kap rá figyelmeztetést az első tényezőről. A felhasználó nem kap többfa-t. Felhasználói SSO-k az alkalmazásba. |
| Jelentkezzen be az alkalmazás chrome / Firefox / Safari, míg ki corpnet egy aktuális munkamenet, és már végzett MFA ebben a munkamenetben. | A felhasználó nem kap rá figyelmeztetést az első tényezőről. A felhasználó nem kap többfa-t. A felhasználói SSO alkalmazásba van. |
| Jelentkezzen be-ba alkalmazás Chrome / Firefox / Safari, míg ki corpnet új bejelentkezési kísérlet. | Űrlapalapú kérdés az AD FS-kiszolgálón. A felhasználó sikeresen bejelentkezik, és a böngésző kéri az MFA-t. |
| Jelentkezzen be az alkalmazáschrome / Firefox, míg a vállalati hálózat egy aktuális munkamenet. | A felhasználó nem kap rá figyelmeztetést az első tényezőről. A felhasználó nem kap többfa-t. A felhasználói SSO alkalmazásba van. |
| Jelentkezzen be az alkalmazásba az alkalmazás mobilalkalmazással egy új bejelentkezési kísérlettel. | Űrlapalapú kérdés az AD FS-kiszolgálón. A felhasználó sikeresen bejelentkezik, és az ADAL-ügyfél kéri az MFA-t. |
| Jogosulatlan felhasználó próbál bejelentkezni alkalmazás bejelentkezési URL-címet. | Űrlapalapú kérdés az AD FS-kiszolgálón. A felhasználó nem tud bejelentkezni az első tényezővel. |
| A jogosult felhasználó megpróbál bejelentkezni, de helytelen jelszót ad meg. | A felhasználó az alkalmazás URL-címére navigál, és hibás felhasználónevet/jelszót kap. |
| A jogosult felhasználó egy e-mailben lévő hivatkozásra kattint, és már hitelesített. | A felhasználó az URL-re kattint, és további kérések nélkül jelentkezik be az alkalmazásba. |
| A jogosult felhasználó egy e-mailben lévő hivatkozásra kattint, és még nincs hitelesítve. | A felhasználó rákattint az URL-re, és az első tényezővel kell hitelesítenie magát. |
| A jogosult felhasználó új bejelentkezési kísérlettel jelentkezik be az alkalmazásba az alkalmazás mobilalkalmazásával (SP-kezdeményezéssel). | Űrlapalapú kérdés az AD FS-kiszolgálón. A felhasználó sikeresen bejelentkezik, és az ADAL-ügyfél kéri az MFA-t. |
| Jogosulatlan felhasználó próbál bejelentkezni alkalmazás bejelentkezési URL-cím (SP által kezdeményezett). | Űrlapalapú kérdés az AD FS-kiszolgálón. A felhasználó nem tud bejelentkezni az első tényezővel. |
| A jogosult felhasználó megpróbál bejelentkezni, de helytelen jelszót ad meg.| A felhasználó az alkalmazás URL-címére navigál, és hibás felhasználónevet/jelszót kap. |
| A jogosult felhasználó kijelentkezik, majd újra bejelentkezik. | Ha a kijelentkezési URL-cím be van állítva, a felhasználó kijelentkezik az összes szolgáltatásból, és hitelesítésre kér. |
| A jogosult felhasználó kijelentkezik, majd újra bejelentkezik. | Ha a kijelentkezési URL nincs konfigurálva, a felhasználó automatikusan visszajelentkezik a meglévő Azure AD böngészőmunkamenet meglévő tokenhasználatával. |
| A jogosult felhasználó egy e-mailben lévő hivatkozásra kattint, és már hitelesített. | A felhasználó az URL-re kattint, és további kérések nélkül jelentkezik be az alkalmazásba. |
| A jogosult felhasználó egy e-mailben lévő hivatkozásra kattint, és még nincs hitelesítve. | A felhasználó rákattint az URL-re, és az első tényezővel kell hitelesítenie magát. |

## <a name="manage-sso"></a>SSO kezelése

Ez a szakasz ismerteti az SSO sikeres kezeléséhez támasztott követelményeket és javaslatokat.

### <a name="required-administrative-roles"></a>Szükséges felügyeleti szerepkörök

Mindig használja a szerepkört a legkevesebb elérhető engedélyekkel a szükséges feladat elvégzéséhez az Azure Active Directoryban. A Microsoft azt [javasolja, hogy tekintse át a rendelkezésre álló különböző szerepköröket,](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) és válassza ki a megfelelőt az alkalmazás minden egyes személyéhez szükséges hely megoldásához. Előfordulhat, hogy egyes szerepköröket ideiglenesen kell alkalmazni, és a központi telepítés befejezése után el kell távolítani.

| Persona| Szerepkörök | Azure AD szerepkör (ha szükséges) |
|--------|-------|-----------------------------|
| Ügyfélszolgálati rendszergazda | Tier 1 támogatás | None |
| Identitás-rendszergazda | Konfigurálás és hibakeresés, ha a problémák hatással vannak az Azure AD-re | Globális rendszergazda |
| Alkalmazás rendszergazdája | Felhasználói tanúsítvány az alkalmazásban, konfiguráció az engedéllyel rendelkező felhasználókon | None |
| Infrastruktúra-rendszergazdák | Tanúsítvány borulás tulajdonosa | Globális rendszergazda |
| A vállalkozás tulajdonosa/érdekelt fél | Felhasználói tanúsítvány az alkalmazásban, konfiguráció az engedéllyel rendelkező felhasználókon | None |

Javasoljuk, hogy a [kiemelt identitáskezelés](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) használatával kezelje a szerepköröket, hogy további naplózási, vezérlési és hozzáférés-felülvizsgálatot biztosítson a címtárengedélyekkel rendelkező felhasználók számára.

### <a name="sso-certificate-lifecycle-management"></a>SSO tanúsítvány életciklusának kezelése

Fontos, hogy azonosítsa a megfelelő szerepkörök és e-mail terjesztési listák feladata az azure AD és az egyszeri bejelentkezéssel konfigurált alkalmazás közötti aláíró tanúsítvány életciklusának kezelése. Íme néhány kulcsfontosságú szerep, amelyet javasoljuk:

- Tulajdonos a felhasználói tulajdonságok frissítéséhez az alkalmazásban
- Tulajdonos I-Call alkalmazásszünet/javítás támogatásához
- Szorosan figyelemmel kísért e-mail terjesztési lista a tanúsítványokkal kapcsolatos változási értesítésekhez

A tanúsítvány maximális élettartama három év. Azt javasoljuk, hogy hozzon létre egy folyamatot, hogyan fogja kezelni a tanúsítványváltozás az Azure AD és az alkalmazás között. Ez segíthet megelőzni vagy minimalizálni a tanúsítvány lejáró vagy a tanúsítványváltás kényszerítése miatti kimaradást.

### <a name="rollback-process"></a>Visszaállítási folyamat

Miután befejezte a tesztelést a tesztesetek alapján, itt az ideje, hogy éles környezetbe lépjen az alkalmazással. Az éles környezetbe való áttérés azt jelenti, hogy a tervezett és tesztelt konfigurációkat az éles környezetben valósítja meg, és a felhasználók számára is elvezeti. Azonban fontos megtervezni, hogy mi a teendő, ha a központi telepítés nem a tervek szerint halad. Ha az sso-konfiguráció sikertelen a központi telepítés során, meg kell értenie, hogyan lehet csökkenteni a kimaradások és a felhasználókra gyakorolt hatás csökkentése.

A rendelkezésre álló hitelesítési módszerek az alkalmazáson belül határozza meg a legjobb stratégia. Mindig győződjön meg arról, hogy részletes dokumentációt az alkalmazás tulajdonosok pontosan hogyan juthat vissza az eredeti bejelentkezési konfigurációs állapot, ha a központi telepítés problémákba ütközik.

- **Ha az alkalmazás több identitásszolgáltatót is támogat,** például az LDAP-t, az AD FS-t és a Ping-et, ne törölje a meglévő sso-konfigurációt a bevezetés során. Ehelyett tiltsa le az áttelepítés során, ha később vissza kell váltania. 

- **Ha az alkalmazás nem támogatja a több IDPs,** de lehetővé teszi a felhasználók számára, hogy jelentkezzen be az űrlap-alapú hitelesítés (felhasználónév/jelszó), győződjön meg arról, hogy a felhasználók visszatérhetnek ehhez a megközelítéshez, ha az új egyszeri bejelentkezés konfigurációjának bevezetése sikertelen.

### <a name="access-management"></a>Hozzáférés-kezelés

Azt javasoljuk, hogy az erőforrásokhoz való hozzáférés kezelése során válasszon skálázott megközelítést. A gyakori megközelítések közé tartozik a helyszíni csoportok használatával az Azure AD Connect en keresztüli szinkronizálás, [dinamikus csoportok létrehozása az Azure AD-ben felhasználói attribútumok alapján,](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)vagy önkiszolgáló csoportok létrehozása az Azure AD-ben egy erőforrás tulajdonosa által kezelt. [self-service groups](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="monitor-security"></a>A biztonság figyelése

Azt javasoljuk, hogy rendszeres ütemben, amelyben tekintse át a SaaS-alkalmazás biztonságának különböző aspektusait, és végezze el a szükséges javító műveleteket.

### <a name="troubleshooting"></a>Hibaelhárítás

Az alábbi hivatkozások hibaelhárítási forgatókönyveket mutatnak be. Érdemes lehet létrehozni egy speciális útmutatót a kisegítő személyzet, amely magában foglalja ezeket a forgatókönyveket, és a javítási lépéseket.

#### <a name="consent-issues"></a>Hozzájárulási problémák

- [Váratlan hozzájárulási hiba](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Hiba a felhasználói jóváhagyással](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Bejelentkezési problémák

- [Problémák az egyéni portálról való bejelentkezéssel](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Probléma a hozzáférési panelről való bejelentkezésnél](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Hiba az alkalmazás bejelentkezési oldalán](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Probléma microsoftos alkalmazásba való bejelentkezéssel](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Az Azure Alkalmazásgalériában felsorolt alkalmazások sso-problémái

- [Probléma a jelszósultsso használatával az Azure Alkalmazásgalériában felsorolt alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Probléma az Azure Alkalmazásgalériában felsorolt alkalmazások összevont sso-jával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Az Azure Alkalmazásgalériában nem szereplő alkalmazások sso-problémái

- [Probléma a jelszó-sso alkalmazásokkal az Azure Application Gallery-ben nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Probléma az összevont sso alkalmazásokkal az Azure Application Gallery-ben nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>További lépések

[SAML-alapú SSO hibakeresése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Alkalmazások jogcímleképezése a PowerShellen keresztül](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[SamL-tokenben kiállított jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Egyszeri bejelentkezési SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Egyszeri kijelentkezési SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (külső felhasználók, például partnerek és szállítók számára)

[Azure AD-beli feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure-identitásvédelem](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Egyszeri bejelentkezéses hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Alkalmazás-sso oktatóanyag](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
