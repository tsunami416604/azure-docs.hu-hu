---
title: Az Azure Active Directory access panel telepítésének megtervezése
description: Útmutató az Azure Active Directory hozzáférési panel telepítéséhez
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897077"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Az Azure Active Directory access panel telepítésének megtervezése

Az Azure Active Directory (Azure AD) Access Panel egy webalapú portál, amely segít csökkenteni a támogatási költségeket, növeli a termelékenységet és a biztonságot, és csökkenti a felhasználói frusztrációt. A rendszer részletes jelentéseket tartalmaz, amelyek nyomon követik a rendszer elérését, és értesítik a rendszergazdákat a visszaélésről vagy a visszaélésről.

Az Azure AD access panel használatával a következőket teheti:

* Fedezze fel és férjen hozzá a vállalat összes Azure AD-hez kapcsolódó erőforrásához, például az alkalmazásokhoz
* Hozzáférés kérése új alkalmazásokhoz és csoportokhoz
* Az erőforrásokhoz való hozzáférés kezelése mások számára
* Önkiszolgáló jelszó-visszaállítások és az Azure többtényezős hitelesítési beállításainak kezelése
* Az eszközök kezelése

Azt is lehetővé teszi a rendszergazdák számára, hogy kezelni:

* Szolgáltatási feltételek
* Cégek
* Hozzáférési felülvizsgálatok


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Az Azure AD Access Panel integrációjának előnyei

Az Azure AD hozzáférési panel a következő módokon szolgálja a vállalkozások javát:

**Intuitív felhasználói élményt nyújt:** A hozzáférési panel egyetlen platformot biztosít az összes egyszeri bejelentkezéses (Egyszeri bejelentkezés) kapcsolatban álló alkalmazáshoz. Egységes portállal rendelkezik a meglévő beállítások és új képességek, például a csoportkezelés és az önkiszolgáló jelszó-visszaállítás kereséséhez, ahogy azok hozzáadódnak. Az intuitív élmény lehetővé teszi a felhasználók számára, hogy gyorsabban térjenek vissza a munkába, és termelékenyebbek legyenek, miközben csökkentik a frusztrációt.

**Növeli a termelékenységet:** A hozzáférési panelen az SSO engedélyezése engedélyezve van. Ha engedélyezi az egyszeri bejelentkezést a nagyvállalati alkalmazásokban és az Office 365-ben, az további bejelentkezési kérések csökkentésével vagy megszüntetésével kiváló bejelentkezési élményt nyújt. A Hozzáférési panel önkiszolgáló és dinamikus tagságot használ, és javítja az identitásrendszer általános biztonságát. Ezt úgy éri el, hogy biztosítja, hogy a megfelelő személyek kezeljék az alkalmazásokhoz való hozzáférést. A hozzáférési panel összefüggő céloldalként szolgál az erőforrások gyors megtalálásához és a munkafeladatok folytatásához.

**Költség kezelése:** A hozzáférési panel engedélyezése az Azure AD-vel segíthet a helyszíni infrastruktúrák értékesítésében. Csökkenti a támogatási költségeket azáltal, hogy egységes portált biztosít az összes alkalmazás megkereséséhez, az erőforrásokhoz való hozzáférés kéréséhez és a fiókok kezeléséhez.

**Növeli a rugalmasságot és**a biztonságot: A hozzáférési panel hozzáférést biztosít a felhőplatform által biztosított biztonsághoz és rugalmassághoz. A rendszergazdák egyszerűen módosíthatják az alkalmazások és erőforrások beállításait, és a felhasználók befolyásolása nélkül is figyelembe vehetik az új biztonsági követelményeket.

**Megbízható naplózási és használati nyomon követést tesz lehetővé:** A naplózás és a használat nyomon követése minden felhasználói képességhez tudatja, hogy a felhasználók mikor használják az erőforrásaikat, és biztosítja, hogy felmérje a biztonságot.

### <a name="licensing-considerations"></a>Engedélyezési szempontok

A hozzáférési panel ingyenes, és nem igényel licencet az alapszintű használathoz. A címtárban lévő objektumok száma és a telepíteni kívánt további szolgáltatások azonban további licenceket igényelhetnek. Néhány gyakori Azure AD-forgatókönyv, amelyek licencelési követelmények közé tartozik a következő biztonsági funkciók:

* [Azure többtényezős hitelesítés](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Csoportalapú tagság](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Új jelszó önkiszolgáló kérése](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory identitásvédelem](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Tekintse meg az [Azure AD teljes licencelési útmutatóját.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Az Azure AD hozzáférési panel üzembe helyezésének előfeltételei

A projekt megkezdése előtt hajtsa végre az alábbi előfeltételeket:

* [Alkalmazás-sso integrálása](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Az Azure AD felhasználói és csoportinfrastruktúrájának kezelése](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Az Azure AD Access Panel telepítésének megtervezése

Az alábbi táblázat a hozzáférési panel központi telepítésének legfontosabb használati eseteit ismerteti:

| Terület| Leírás |
| - | - |
| Hozzáférés| Az Access Panel portál a vállalati hálózaton belüli vállalati és személyes eszközökről érhető el. |
|Hozzáférés | Az Access Panel portál a vállalati hálózaton kívüli vállalati eszközökről érhető el. |
| Naplózás| A használati adatok legalább 29 naponta letöltődnek a vállalati rendszerekbe. |
| Szabályozás| Az Azure AD-hez csatlakoztatott alkalmazásokhoz és csoportokhoz való felhasználói hozzárendelések életciklusa definiálva és figyelve történik. |
| Biztonság| Az erőforrásokhoz való hozzáférés felhasználói és csoport-hozzárendeléseken keresztül történik. Csak a jogosult felhasználók kezelhetik az erőforrás-hozzáférést. |
| Teljesítmény| Az access-hozzárendelések propagálási ütemterveit dokumentálja és figyeli. |
| Felhasználó felület| A felhasználók tisztában vannak a Hozzáférési panel funkcióival és azok használatával.|
| Felhasználó felület| A felhasználók kezelhetik az alkalmazásokhoz és csoportokhoz való hozzáférésüket.|
| Felhasználó felület| A felhasználók kezelhetik a fiókjukat. |
| Felhasználó felület| A felhasználók tisztában vannak a böngésző kompatibilitásával. |
| Támogatás| A felhasználók támogatást kaphatnak a hozzáférési panellel kapcsolatos problémákhoz. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Gyakorlati tanácsok az Azure AD access panel üzembe helyezéséhez

A hozzáférési panel funkciói fokozatosan engedélyezhetők. A következő telepítési sorrendet javasoljuk:

1. Saját alkalmazások
   * Alkalmazásindító
   * Önkiszolgáló alkalmazások kezelése
   * Microsoft Office 365 integráció

1. Önkiszolgáló alkalmazások felderítése
   * Új jelszó önkiszolgáló kérése
   * Többtényezős hitelesítés beállításai
   * Eszközfelügyelet
   * Használati feltételek
   * Szervezetek kezelése

1. Saját csoportok
   * Önkiszolgáló csoportkezelés
1. Hozzáférési felülvizsgálatok
   * Hozzáférés-felülvizsgálat kezelése

A My Apps kezdetével a felhasználók bevezetik a portált az erőforrások elérésének általános helyeként. Az önkiszolgáló alkalmazások felderítésének hozzáadása a Saját alkalmazások élményre épül. A Saját csoportok és a hozzáférési felülvizsgálatok az önkiszolgáló képességekre épülnek.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Az Azure AD hozzáférési panel konfigurációinak megtervezése

Az alábbi táblázat a Hozzáférési panel számos fontos konfigurációját és a jellemző értékeket sorolja fel:

| Konfiguráció| Tipikus értékek |
| - | - |
| A kísérleti csoportok meghatározása| Azonosítsa a használandó Azure AD biztonsági csoportot, és győződjön meg arról, hogy minden kísérleti tag a csoport része. |
| Határozza meg a termeléshez engedélyezni fogandó csoportot vagy csoportokat.| Azonosítsa az Azure AD biztonsági csoportok, vagy az Azure AD-vel szinkronizált Active Directory-csoportok, használni kell. Győződjön meg arról, hogy minden pilóta a csoport tagja. |
| Lehetővé teszi a felhasználók számára, hogy az SSO-t bizonyos típusú alkalmazásokhoz használják| Összevont SSO, OAuth, Jelszó SSO, App Proxy |
| Önkiszolgáló jelszó-visszaállítás használatának engedélyezése a felhasználóknak | Igen |
| Többtényezős hitelesítés használatának engedélyezése a felhasználóknak| Igen |
| Önkiszolgáló csoportkezelés használatának engedélyezése a felhasználók nak bizonyos típusú csoportokhoz| Biztonsági csoportok, Office 365-csoportok |
| Önkiszolgáló alkalmazáskezelés használatának engedélyezése a felhasználóknak| Igen |
| Hozzáférés-felülvizsgálatok használatának engedélyezése a felhasználóknak| Igen |

### <a name="plan-consent-strategy"></a>Hozzájárulási stratégia tervezése

A felhasználóknak vagy rendszergazdáknak hozzá kell járulniuk az alkalmazások használati feltételeihez és adatvédelmi irányelveihez. Ha lehetséges, az üzleti szabályok alapján használja a rendszergazdai beleegyezést, ami jobb felhasználói élményt nyújt a felhasználóknak.

A rendszergazdai hozzájárulás használatához a szervezet globális rendszergazdájának kell lennie, és az alkalmazásoknak vagy a következőknek kell lenniük:

* Regisztrálva a szervezetben

* Regisztrált egy másik Azure AD-szervezetben, és korábban legalább egy felhasználó hozzájárult hozzá

További információ: [A végfelhasználók hozzájárulásának konfigurálása az Azure Active Directoryban](configure-user-consent.md)című témakörben talál.

### <a name="engage-the-right-stakeholders"></a>Vonja be a megfelelő érdekelt feleket

Ha a technológiai projektek kudarcot vallanak, általában a hatásra, az eredményekre és a felelősségre vonatkozó, nem megfelelő elvárások miatt teszik ezt. A buktatókat elkerülve [győződjön meg arról, hogy a megfelelő érdekelt feleket vonja be,](../fundamentals/active-directory-deployment-plans.md) és hogy az érdekelt felek szerepe idomulanak a projekthez.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeréhez elengedhetetlen. Proaktív módon tájékoztassa a felhasználókat arról, hogyan és mikor változik meg a felhasználói élményük, és szükség esetén hogyan szerezhetnek támogatást.

Bár a Hozzáférési panel általában nem okoz felhasználói problémákat, fontos felkészülni. Útmutatókat és az összes erőforrás listáját a kisegítő személyzet számára a bevezetés előtt hozhat létre.

#### <a name="communications-templates"></a>Kommunikációs sablonok

A Microsoft [testre szabható sablonokat biztosít az e-mailekhez és az egyéb kommunikációhoz](https://aka.ms/APTemplates) a Hozzáférési panelszámára. Ezeket az eszközöket a vállalati kultúrának megfelelően más kommunikációs csatornákon való használatra is módosíthatja.

## <a name="plan-your-sso-configuration"></a>Az sso konfigurációjának megtervezése

Amikor egy felhasználó bejelentkezik egy alkalmazásba, hitelesítési folyamaton megy keresztül, és bizonyítania kell, hogy kik ők. SSO nélkül a jelszó az alkalmazásban tárolódik, és a felhasználónak ismernie kell ezt a jelszót. Az SSO használatával a felhasználók hitelesítő adatait a rendszer átadja az alkalmazásnak, így nem kell újra megadniuk az egyes alkalmazások jelszavait.

Az alkalmazások elindításához a Saját alkalmazásokban engedélyezni kell az SSO-t.

Az Azure AD három különböző módot támogat az [alkalmazásokban való egyszeri bejelentkezés](what-is-single-sign-on.md)engedélyezésére:

* **Összevont egyszeri bejelentkezés** 
    * Lehetővé teszi, hogy egy alkalmazás átirányítsa az Azure AD-be a felhasználói hitelesítés, jelszó kérése helyett. 
    * Protokollokat használó alkalmazások, például az SAML 2.0, a WS-Federation vagy az OpenID Connect, és az egyszeri bejelentkezés leggazdagabb módja.

* **Jelszóalapú egyszeri bejelentkezés** 
    * Lehetővé teszi az alkalmazásjelszó biztonságos tárolását és visszajátszását egy webböngésző-bővítmény vagy mobilalkalmazás használatával. 
    * Kihasználja az alkalmazás által biztosított meglévő bejelentkezési folyamatot, de lehetővé teszi a rendszergazda számára a jelszavak kezelését. A felhasználónak nem kell ismernie a jelszót.

* **Meglévő egyszeri bejelentkezés** 
    * Lehetővé teszi, hogy az Azure AD kihasználja az alkalmazáshoz konfigurált meglévő egyszeri bejelentkezéselőnyeit.
    * Lehetővé teszi, hogy ezek az alkalmazások az Office 365-höz vagy az Azure AD Access Panel portálokhoz kapcsolódjanak. 
    * További jelentéskészítést tesz lehetővé az Azure AD-ben, amikor az alkalmazások ott elindulnak. 
    * Tartalmazza az Azure Application Proxy és a csatolt egyszeri bejelentkezési mód használatát.

Az alkalmazások Egyszeri bejelentkezési módjának konfigurálása itt található: [Egyszeri bejelentkezés az Azure Active Directoryban lévő alkalmazásokba.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

A My Apps oldal legjobb élménye érdekében kezdje az összevont sso-hoz elérhető felhőalkalmazások integrációjával. Az összevont egyszeri bejelentkezés lehetővé teszi a felhasználók számára, hogy egységes, egy kattintásos élményt nyújtsanak az alkalmazásindítási felületeken, és általában robusztusabbak a konfigurációvezérlésben.

Használja az összevont egyszeri bejelentkezést az Azure AD-vel (OpenID Connect/SAML), ha egy alkalmazás támogatja azt, jelszóalapú egyszeri bejelentkezés és ADFS helyett.

A SaaS-alkalmazások üzembe helyezéséről és konfigurálásáról a [SaaS-sso telepítési tervben](https://aka.ms/deploymentplans/sso)talál további információt.

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>A Saját alkalmazások böngészőbővítmény telepítésének megtervezése

Amikor a felhasználók jelszóalapú egyszeri bejelentkezésű alkalmazásokba jelentkeznek be, telepíteniük kell és használniuk kell a Saját alkalmazások biztonságos bejelentkezési bővítményt. A bővítmény végrehajt egy parancsfájlt, amely továbbítja a jelszót az alkalmazás bejelentkezési űrlapjára. A rendszer a jelszóalapú egyszeri bejelentkezés alkalmazás első indításakor kéri a bővítmény telepítését. A bővítményről további információt ebben a dokumentációban talál [az Access Panel böngészőbővítmény telepítéséről.](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)

Ha jelszóalapú egyszeri bejelentkezésű alkalmazásokat kell integrálnia, meg kell határoznia egy mechanizmust a bővítmény támogatott böngészőkkel történő nagyméretekben történő [telepítéséhez.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) A lehetőségek a következők:

* [Az Internet Explorer csoportházirendje](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Az Internet Explorer konfigurációkezelője](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Felhasználó által vezérelt letöltés és konfiguráció Chrome, Firefox, Microsoft Edge vagy IE esetén](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

További információ: [A jelszó egyszeri bejelentkezésbeállítása](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Azok a felhasználók, akik nem használnak jelszóalapú Egyszeri bejelentkezést használó alkalmazásokat, szintén élvezhetik a bővítmény előnyeit. Ezek az előnyök magukban foglalják azt a lehetőséget, hogy bármilyen alkalmazást elindítson a keresősávjáról, hozzáférést talál a legutóbb használt alkalmazásokhoz, és rendelkezik egy hivatkozással a Saját alkalmazások lapra.

A jelszóalapú Egyszeri bejelentkezés alkalmazás első indításakor a következőket fogja látni:

![Képernyőkép a My Apps böngészőbővítmény telepítési képernyőjéről ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>A mobil hozzáférés megtervezése

Az Intune-házirenddel (Microsoft Edge vagy Intune felügyelt böngészővel védett) böngésző szükséges a jelszóalapú Egyszeri bejelentkezésalkalmazások indításához. A házirenddel védett böngésző lehetővé teszi az alkalmazáshoz mentett jelszó átvitelét. A Microsoft Edge vagy a felügyelt böngésző webes adatvédelmi funkciókat biztosít. A Microsoft Edge-et vállalati forgatókönyvekhez is használhatja iOS és Android rendszerű eszközökön. A Microsoft Edge ugyanazokat a felügyeleti forgatókönyveket támogatja, mint az Intune felügyelt böngészője, és javítja a felhasználói élményt. További információ: [A webes hozzáférés kezelése a Microsoft Intune szabályzattal védett böngészőjével](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>A Saját alkalmazások telepítésének megtervezése

A hozzáférési panel alapja a Saját alkalmazások alkalmazásindító, [https://myapps.microsoft.com](https://myapps.microsoft.com/)amelyet a felhasználók a rendszer hez. A My Apps oldalak egyetlen helyet biztosítanak a felhasználóknak, hogy elkezdjék a munkájukat, és eljussanak a szükséges alkalmazásokhoz. Itt a felhasználók az összes olyan alkalmazás listáját találják meg, amelyekhez egyszeri bejelentkezési hozzáféréssel rendelkeznek. 

![Képernyőkép az alkalmazások panelről](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Ugyanezek az alkalmazások jelennek meg az Office 365 alkalmazásindítójában is, amikor a felhasználók az Office 365 portált használják.

Tervezze meg, hogy milyen sorrendben fogja hozzáadni az alkalmazásokat a Saját alkalmazások indítóhoz, és döntse el, hogy fokozatosan vagy egyszerre fogja-e őket kiterjezni. Ehhez hozzon létre egy alkalmazásleltárt, amely felsorolja a hitelesítés típusát és az egyes alkalmazásokhoz meglévő SSO-integrációkat.

#### <a name="add-applications-to-the-my-apps-panel"></a>Alkalmazások hozzáadása a Saját alkalmazások panelhez

Bármely Azure AD Egyszeri szolgáltatást engedélyező alkalmazás hozzáadható a Saját alkalmazások indítójához. A többi alkalmazás a Csatolt SSO beállítással kerül hozzáadásra. Konfigurálhat egy alkalmazáscsempét, amely a meglévő webalkalmazás URL-címére hivatkozik. A csatolt sso lehetővé teszi, hogy a felhasználókat a Saját alkalmazások portálra irányítsa anélkül, hogy az összes alkalmazást áttelepítené az Azure AD SSO-ba. Fokozatosan áthelyezheti az Azure AD Egyszeri szolgáltatás által konfigurált alkalmazásokat a felhasználói élmény megszakítása nélkül.

#### <a name="use-my-apps-collections"></a>A Saját alkalmazások gyűjtemények használata

Alapértelmezés szerint az összes alkalmazás egy oldalon jelenik meg. A gyűjtemények segítségével azonban csoportosíthatja a kapcsolódó alkalmazásokat, és külön lapon mutathatja be őket, így könnyebben megtalálhatja őket. A gyűjtemények segítségével például logikai csoportokat hozhat létre alkalmazásokból adott szerepkörökhöz, feladatokhoz, projektekhez és így tovább. További információt a [Saját alkalmazások gyűjtemények használata a felhasználói hozzáférési panelek testreszabása című témakörben talál.](access-panel-collections.md) 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Tervezze meg, hogy a Saját alkalmazások vagy egy meglévő portál használata

Előfordulhat, hogy a felhasználók már rendelkeznek a Saját alkalmazásoktól eltérő alkalmazással vagy portállal. Ha igen, döntse el, hogy mindkét portált támogatja-e, vagy csak egyet használ.

Ha egy meglévő portált már használnak kiindulási pontként a felhasználók számára, integrálhatja a Saját alkalmazások funkciót a felhasználói hozzáférési URL-címek használatával. A felhasználói hozzáférési URL-ek közvetlen hivatkozásként működnek a Saját alkalmazások portálon elérhető alkalmazásokhoz. Ezek az URL-címek beágyazhatók bármely meglévő webhelybe. Amikor egy felhasználó kiválasztja a hivatkozást, megnyitja az alkalmazást a Saját alkalmazások portálon.

A felhasználói hozzáférés URL-tulajdonsága az azure-portál **tulajdonságok** területén található.

![Képernyőkép az alkalmazások panelről](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Önkiszolgáló alkalmazások felderítésének és elérésének megtervezése

Ha egy alapvető alkalmazáskészlet telepítve van a felhasználó Saját alkalmazások lapjára, engedélyeznie kell az önkiszolgáló alkalmazáskezelési funkciókat. Az önkiszolgáló alkalmazások felderítése lehetővé teszi a felhasználók számára, hogy:

* Keressen új alkalmazásokat, amelyeket hozzáadhat a Saját alkalmazásokhoz. 
* Adjon hozzá olyan választható alkalmazásokat, amelyekről nem tudják, hogy szükségük van a telepítés során.

A jóváhagyási munkafolyamatok kifejezett jóváhagyásra rendelkezésre állnak az alkalmazások eléréséhez. A jóváhagyó felhasználók értesítést kapnak a Saját alkalmazások portálon, ha függőben lévő hozzáférési kérelem van az alkalmazáshoz.

## <a name="plan-self-service-group-membership"></a>Önkiszolgáló csoporttagság megtervezése 

Engedélyezheti a felhasználóknak, hogy saját biztonsági csoportokat vagy Office 365-csoportokat hozzanak létre és kezeljenek az Azure AD-ben. A csoport tulajdonosa jóváhagyhatja vagy elutasíthatja a tagsági kérelmeket, és delegálhatja a csoporttagság ellenőrzését. Az önkiszolgáló csoportfelügyeleti funkciók nem érhetők el a levelezésre képes biztonsági csoportok vagy terjesztési listák esetében.

Az önkiszolgáló csoporttagság megtervezéséhez határozza meg, hogy engedélyezi-e a szervezet összes felhasználójának csoportok vagy felhasználók csak egy részhalmazát. Ha a felhasználók egy részét engedélyezi, be kell állítania egy csoportot, amelyhez ezeket a személyeket hozzáadja. [Az önkiszolgáló csoportkezelés beállítása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) a forgatókönyvek engedélyezésével kapcsolatos részletekért olvassa el az önkiszolgáló csoportkezelés beállítása.

## <a name="plan-reporting-and-auditing"></a>A tervezés jelentése és naplózása

Az Azure AD [technikai és üzleti elemzéseket kínáló jelentéseket](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)tartalmaz. A jelentések tulajdonjogának átvétele és rendszeres felhasználása érdekében együttműködjön a vállalati és a műszaki alkalmazások tulajdonosaival. Az alábbi táblázat néhány példát mutat be a tipikus jelentéskészítési forgatókönyvekre.

|   | A kockázatok kezelése| Növelje a termelékenységet| Cégirányítás és megfelelőség |
|  - |- | - | - |
| Jelentéstípusok|  Alkalmazásengedélyek és -használat| Fiókkiépítési tevékenység| Az alkalmazások elérésének áttekintése |
| Lehetséges intézkedések| Ellenőrzési hozzáférés; engedélyek visszavonása| A kiépítési hibák elhárítása| Hozzáférés visszavonása |

Az Azure AD 30 napig tartja a legtöbb naplózási adatot. Az adatok az Azure Felügyeleti portálon vagy az API-n keresztül érhetők el, hogy letölthesse az elemző rendszerekbe.

#### <a name="auditing"></a>Naplózás

Az alkalmazáshozzáférés naplózási naplói 30 napig érhetők el. Ha a vállalaton belüli biztonsági naplózás hosszabb megőrzést igényel, a naplókat exportálni kell egy Biztonsági információ esemény- és felügyeleti (SIEM) eszközébe, például a Splunk vagy az ArcSight eszközbe.

A naplózási, jelentési és vész-helyreállítási biztonsági mentések, dokumentálja a szükséges letöltési gyakoriság, mi a célrendszer, és ki felelős az egyes biztonsági mentések kezeléséért. Előfordulhat, hogy nincs szükség külön naplózási és jelentési biztonsági mentések. A vész-helyreállítási biztonsági mentés kell egy külön entitás.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Alkalmazások telepítése a felhasználók Saját alkalmazások panelére

Miután egy alkalmazás konfigurálva van az SSO-hoz, a csoportok hoznak létre hozzáférést. A hozzárendelt csoportok felhasználói hozzáférhetnek, és az alkalmazás a Saját alkalmazások és az Office 365 alkalmazásindítójában jelenik meg.

Lásd: [Felhasználók és csoportok hozzárendelése egy alkalmazáshoz az Active Directoryban](methods-for-assigning-users-and-groups.md).

Ha a tesztelés vagy üzembe helyezés során hozzá szeretné adni a csoportokat, de még nem engedélyezi az alkalmazások megjelenítését a Saját alkalmazásokban, olvassa el [Az alkalmazás elrejtése a felhasználói élmény elől az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)című témakört.

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Microsoft Office 365-alkalmazások telepítése a Saját alkalmazások szolgáltatásba

Office 365-alkalmazások esetén a felhasználók a hozzájuk rendelt licencek alapján kapják meg az Office-példányt. Az Office-alkalmazásokhoz való hozzáférés előfeltétele, hogy a felhasználók az Office-alkalmazásokhoz kötött megfelelő licenceket kapják. Amikor licencet rendel egy felhasználóhoz, azok automatikusan megjelennek a licenchez társított alkalmazások a Saját alkalmazások lapon és az Office 365 alkalmazásindítójában.

Ha el szeretne rejteni egy office-alkalmazást a felhasználók elől, lehetőség van az alkalmazások elrejtésére a Saját alkalmazások portálról, miközben továbbra is engedélyezi a hozzáférést az Office 365 portálról. Keresse meg ezeket a beállításokat az alkalmazás Felhasználói beállítások részében. További információ: [Egy alkalmazás elrejtése a felhasználói élmény elől az Azure Active Directoryban.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)

![Képernyőkép az alkalmazások elrejtésének konfigurálásáról](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Alkalmazásönkiszolgáló képességek üzembe helyezése

Az önkiszolgáló alkalmazások hozzáférése lehetővé teszi a felhasználók számára az alkalmazások önfelderítését és hozzáférésének kérését. A felhasználók szabadon hozzáférhetnek a szükséges alkalmazásokhoz anélkül, hogy minden alkalommal egy informatikai csoporton keresztül kellene hozzáférést kérniük. Amikor egy felhasználó hozzáférést kér, és az alkalmazás tulajdonosa automatikusan vagy manuálisan jóváhagyja, a rendszer hozzáadja őket egy csoporthoz a háttérben. A jelentéskészítés engedélyezve van arról, hogy ki kért, hagyott jóvá vagy távolított el hozzáférést, és így szabályozhatja a hozzárendelt szerepkörök kezelését.

Az alkalmazáshozzáférési kérelmek jóváhagyását delegálhatja az üzleti jóváhagyóknak. A vállalkozás jóváhagyója beállíthatja az alkalmazás hozzáférési jelszavakat a vállalkozás jóváhagyója Saját alkalmazások lapjáról.

További információ: [Az önkiszolgáló alkalmazás-hozzáférés használata.](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![Képernyőkép az önkiszolgáló alkalmazáskezelés konfigurálásáról](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>A telepítés ellenőrzése

Győződjön meg arról, hogy a hozzáférési panel üzembe helyezését alaposan tesztelték, és visszaállítási terv van érvényben.

A következő teszteket mind a vállalati tulajdonú, mind a személyes eszközökkel el kell végezni. Ezeknek a teszteseteknek tükrözniük kell az üzleti használati eseteket is. Az alábbiakban néhány esetben a minta üzleti követelmények ebben a dokumentumban, és a tipikus technikai forgatókönyvek. Adjon hozzá másokat az igényeinek megfelelően.

#### <a name="application-sso-access-test-case-examples"></a>Alkalmazás-sso hozzáférési teszteset példák:


| Üzleti eset| Várt eredmény |
| - | -|
| A felhasználó bejelentkezik a Saját alkalmazások portálra| A felhasználó bejelentkezhet, és megtekintheti az alkalmazásait |
| A felhasználó egy összevont SSO-alkalmazást indít el| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| Felhasználó elindítja a jelszót Egyszeri bejelentkezés alkalmazás először| A felhasználónak telepítenie kell a Saját alkalmazások bővítményt |
| Felhasználó elindítja a jelszót Egyszeri bejelentkezés alkalmazás egy későbbi időpontban| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó elindít egy alkalmazást az Office 365 Portálról| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó alkalmazást indít el a Felügyelt böngészőből| A felhasználó automatikusan bejelentkezik az alkalmazásba |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Alkalmazás önkiszolgáló képességek teszt eset példák


| Üzleti eset| Várt eredmény |
| - | - |
| A felhasználó kezelheti az alkalmazás tagságát| A felhasználó hozzáadhat/eltávolíthat olyan tagokat, akik hozzáférnek az alkalmazáshoz |
| A felhasználó szerkesztheti az alkalmazást| A felhasználó szerkesztheti az alkalmazás leírását és hitelesítő adatait a jelszó-sso alkalmazásokhoz |

### <a name="rollback-steps"></a>Visszaállítási lépések

Fontos megtervezni, hogy mi a teendő, ha a központi telepítés nem a tervek szerint halad. Ha az sso-konfiguráció sikertelen a központi telepítés során, meg kell [értenie, hogyan háríthatja el az SSO-problémákat,](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) és hogyan csökkentheti a felhasználókra gyakorolt hatást. Szélsőséges körülmények között előfordulhat, hogy vissza kell [vonnia az SSO-t.](../manage-apps/plan-sso-deployment.md)


## <a name="manage-your-implementation"></a>A megvalósítás kezelése

A legkevésbé kiemelt szerepkört kell használnia egy szükséges feladat elvégzéséhez az Azure Active Directoryban. [Tekintse át a rendelkezésre álló különböző szerepköröket,](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) és válassza ki a megfelelőt az alkalmazás minden egyes személyiségéhez szükséges hely megoldásához. Előfordulhat, hogy egyes szerepköröket ideiglenesen kell alkalmazni, és a központi telepítés befejezése után el kell távolítani.

| Szerepek| Szerepkörök| Azure AD szerepkör  |
| - | -| -|
| Ügyfélszolgálati rendszergazda| Tier 1 támogatás| None |
| Identitás-rendszergazda| Konfigurálás és hibakeresés, ha a problémák hatással vannak az Azure AD-re| Globális rendszergazda |
| Alkalmazás rendszergazdája| Felhasználói tanúsítvány az alkalmazásban, konfiguráció az engedéllyel rendelkező felhasználókon| None |
| Infrastruktúra-rendszergazdák| Tanúsítvány borulás tulajdonosa| Globális rendszergazda |
| A vállalkozás tulajdonosa/érdekelt fél| Felhasználói tanúsítvány az alkalmazásban, konfiguráció az engedéllyel rendelkező felhasználókon| None |

A [Kiemelt identitáskezelés](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) segítségével kezelheti a szerepköröket, hogy további naplózási, vezérlési és hozzáférés-felülvizsgálatot biztosítson a címtárengedélyekkel rendelkező felhasználók számára.

### <a name="troubleshoot-access-panel-issues"></a>A Hozzáférési panel hibáinak elhárítása

Hibaelhárítási útmutatókat hozhat létre a támogatási szervezet számára, gyakori forgatókönyvekkel, amelyek a Microsoft dokumentációjára mutatnak a megoldásaikban. Érdemes lehet létrehozni a támogatást a szervezet által használt szintekre.

Az alábbi hibaelhárítási útmutatókból itt talál például a következőket:

[Nem megjelenő alkalmazások](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Nem várt alkalmazások jelennek meg](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[A felhasználó nem tud bejelentkezni a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Problémák az önkiszolgáló alkalmazások elérésével](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[A böngészőbővítménylel kapcsolatos problémák](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>További lépések

[Az Azure többtényezős hitelesítésének üzembe helyezésének megtervezése](https://aka.ms/deploymentplans/mfa)
