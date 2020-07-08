---
title: Azure Active Directory-hozzáférési panel üzembe helyezésének megtervezése
description: Útmutató a Azure Active Directory-hozzáférési panel üzembe helyezéséhez
services: active-directory
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b8a74c85b63e3a532e49136ba3b33437d907694
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555306"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Azure Active Directory-hozzáférési panel üzembe helyezésének megtervezése

A Azure Active Directory (Azure AD) hozzáférési panel egy webalapú portál, amely a támogatási költségek csökkentését, a termelékenység és a biztonság növelését, valamint a felhasználói frusztráció csökkentését segíti elő. A rendszer részletes jelentéseket tartalmaz, amelyek nyomon követik a rendszer elérését, és értesítik a rendszergazdákat a visszaélésről vagy a visszaélésről.

Az Azure AD hozzáférési paneljének használatával a következőket teheti:

* Felderítheti és elérheti vállalata Azure AD-hez kapcsolódó erőforrásait, például az alkalmazásokat
* Hozzáférés kérése új alkalmazásokhoz és csoportokhoz
* Ezekhez az erőforrásokhoz való hozzáférés kezelése mások számára
* Önkiszolgáló jelszavak alaphelyzetbe állítása és az Azure Multi-Factor Authentication beállításainak kezelése
* Eszközök kezelése

Emellett a rendszergazdák számára is lehetővé teszi a felügyeletet:

* Szolgáltatási feltételek
* Cégek
* Hozzáférési felülvizsgálatok


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Az Azure AD hozzáférési panel-integráció előnyei

Az Azure AD hozzáférési panelje a következő módokon nyújt előnyöket a vállalatoknak:

**Intuitív felhasználói élményt nyújt**: a hozzáférési panel egyetlen platformot biztosít az összes Azure-beli egyszeri bejelentkezéshez (SSO) csatlakoztatott alkalmazáshoz. A meglévő beállításokat és új képességeket (például a csoportok kezelését és az önkiszolgáló jelszó-visszaállítást) egy egységes portálon találja, ahogy hozzájuk lettek adva. Az intuitív élmény lehetővé teszi, hogy a felhasználók gyorsabban és hatékonyabban térjenek vissza a munkába, miközben csökkentik a frusztrációt.

**Növeli a termelékenységet**: a hozzáférési panelen lévő összes felhasználói alkalmazás egyszeri bejelentkezéses engedélyezve van. A vállalati alkalmazások és az Office 365 egyszeri bejelentkezésének engedélyezése kiváló bejelentkezési élményt nyújt a további bejelentkezési kérések csökkentésével vagy megszüntetésével. A hozzáférési panel önkiszolgáló és dinamikus tagságot használ, és javítja az Identity rendszer általános biztonságát. Ezzel biztosíthatja, hogy a megfelelő személyek kezelhesse az alkalmazásokhoz való hozzáférést. A hozzáférési panel egy koherens kezdőlapként szolgál, amellyel gyorsan megtalálhatja az erőforrásokat, és folytathatja a munkahelyi feladatokat.

A **Cost**: a hozzáférési panel Azure ad-vel való engedélyezése segíthet a helyszíni infrastruktúrák kiválasztásában. A támogatási költségeket csökkenti azáltal, hogy egységes portált biztosít az összes alkalmazás megkereséséhez, az erőforrásokhoz való hozzáférés kéréséhez és a fiókok kezeléséhez.

**Növeli a rugalmasságot és a biztonságot**: a hozzáférési panel hozzáférést biztosít a felhőalapú platform által biztosított biztonsághoz és rugalmassághoz. A rendszergazdák egyszerűen módosíthatják a beállításokat az alkalmazásokra és az erőforrásokra, és az új biztonsági követelményekkel is rendelkezhetnek anélkül, hogy ez befolyásolná a felhasználókat

A **robusztus naplózás és használat nyomon követését teszi lehetővé**: az összes felhasználói funkció naplózási és használati nyomon követésével megtudhatja, hogy mikor használják a felhasználók az erőforrásaikat, és biztosítja, hogy értékelni tudja a biztonságot.

### <a name="licensing-considerations"></a>Licencelési megfontolások

A hozzáférési panel ingyenes, és nincs szükség alapszintű licencek használatára. A címtárban lévő objektumok száma és a telepíteni kívánt további funkciók azonban további licenceket igényelhetnek. A licencelési követelményekkel rendelkező gyakori Azure AD-forgatókönyvek a következő biztonsági funkciókat tartalmazzák:

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Csoport alapú tagság](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Új jelszó önkiszolgáló kérése](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Tekintse [meg az Azure ad teljes licencelési útmutatóját](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Az Azure AD hozzáférési panel üzembe helyezésének előfeltételei

A projekt megkezdése előtt végezze el a következő előfeltételeket:

* [Alkalmazás egyszeri bejelentkezésének integrálása](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Azure AD-felhasználó és-csoport infrastruktúrájának kezelése](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Az Azure AD hozzáférési panelének megtervezése

A következő táblázat a hozzáférési panel központi telepítésének legfontosabb használati eseteit ismerteti:

| Terület| Description |
| - | - |
| Access| A hozzáférési panel portál a vállalati hálózaton belüli vállalati és személyes eszközökről érhető el. |
|Access | A hozzáférési panel portál a vállalati hálózaton kívüli vállalati eszközökről érhető el. |
| Naplózás| A használati adatok legalább 29 naponta letöltődnek a vállalati rendszerbe. |
| Szabályozás| Az Azure AD-hez csatlakoztatott alkalmazások és csoportok felhasználói hozzárendeléseinek életciklusa definiálva és figyelve van. |
| Biztonság| Az erőforrásokhoz való hozzáférést felhasználói és csoportos hozzárendelések segítségével szabályozhatja. Csak a jogosult felhasználók kezelhetik az erőforrás-hozzáférést. |
| Teljesítmény| A hozzáférési hozzárendelések terjesztési ütemterveit dokumentálják és figyelik. |
| Felhasználó felület| A felhasználók tisztában vannak a hozzáférési panel képességeivel és azok használatának módjával.|
| Felhasználó felület| A felhasználók kezelhetik az alkalmazások és a csoportok hozzáférését.|
| Felhasználó felület| A felhasználók kezelhetik a fiókjaikat. |
| Felhasználó felület| A felhasználók tisztában vannak a böngészők kompatibilitásával. |
| Támogatás| A felhasználók a hozzáférési Panelrel kapcsolatos problémákat is megtalálják. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Ajánlott eljárások az Azure AD hozzáférési paneljének üzembe helyezéséhez

A hozzáférési panel működésének fokozatos engedélyezése is lehetséges. A következő üzembe helyezési sorrendet javasoljuk:

1. Saját alkalmazások
   * App Launcher
   * Önkiszolgáló alkalmazások kezelése
   * Microsoft Office 365 integráció

1. Önkiszolgáló alkalmazások felderítése
   * Új jelszó önkiszolgáló kérése
   * Multi-Factor Authentication beállítások
   * Eszközfelügyelet
   * Használati feltételek
   * Szervezetek kezelése

1. Saját csoportok
   * Önkiszolgáló csoportkezelés
1. Hozzáférési felülvizsgálatok
   * Hozzáférés-felülvizsgálati felügyelet

Az alkalmazásoktól kezdve a felhasználók a portálra is bemutatják az erőforrások elérésének közös helyét. Az önkiszolgáló alkalmazás-felderítés hozzáadását a saját alkalmazások felületén kell megalkotni. A saját csoportok és hozzáférési felülvizsgálatok az önkiszolgáló képességekre épülnek.

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Konfigurációk tervezése az Azure AD hozzáférési paneljén

A következő táblázat a hozzáférési panel több fontos konfigurációját és az esetlegesen használt jellemző értékeket sorolja fel:

| Konfiguráció| Jellemző értékek |
| - | - |
| A kísérleti csoportok meghatározása| Azonosítsa a használni kívánt Azure AD biztonsági csoportot, és győződjön meg arról, hogy az összes kísérleti tag a csoport részét képezi. |
| Határozza meg az éles környezetben engedélyezni kívánt csoportokat vagy csoportokat.| Azonosítsa a használni kívánt Azure AD-beli biztonsági csoportokat, illetve az Azure AD-vel szinkronizált Active Directory csoportokat. Győződjön meg arról, hogy az összes kísérleti tag a csoport részét képezi. |
| Egyszeri bejelentkezés használatának engedélyezése a felhasználók számára bizonyos típusú alkalmazásokhoz| Összevont egyszeri bejelentkezés, OAuth, jelszó egyszeri bejelentkezés, alkalmazásproxy |
| Önkiszolgáló jelszó-visszaállítás használatának engedélyezése a felhasználók számára | Yes |
| Multi-Factor Authentication használatának engedélyezése a felhasználók számára| Yes |
| Lehetővé teszi a felhasználók számára az önkiszolgáló csoportok felügyeletének használatát bizonyos típusú csoportok esetében| Biztonsági csoportok, Office 365-csoportok |
| Az önkiszolgáló alkalmazások felügyeletének engedélyezése a felhasználók számára| Yes |
| Hozzáférési felülvizsgálatok használatának engedélyezése a felhasználók számára| Yes |

### <a name="plan-consent-strategy"></a>A beleegyező stratégia megtervezése

A felhasználóknak vagy a rendszergazdáknak hozzá kell járulniuk bármely alkalmazás használati feltételeihez és adatvédelmi szabályzatához. Ha lehetséges, az üzleti szabályok alapján használja a rendszergazdai jogosultságot, amely jobb felhasználói élményt nyújt a felhasználóknak.

A rendszergazdai engedély használatához a szervezet globális rendszergazdájának kell lennie, és az alkalmazásoknak a következőknek kell lenniük:

* Regisztrálva van a szervezetben

* Regisztrálva van egy másik Azure AD-szervezetben, és korábban legalább egy felhasználó beleegyezett

További információkért lásd: [a végfelhasználók beleegyezett az alkalmazásba Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

Ha a technológiai projektek sikertelenek, általában azért teszik ezt meg, mert a hatás, az eredmények és a felelősségek nem egyeznek. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md) , és hogy a projektben szereplő, az érintett felek szerepkörei jól megértettek.

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon tájékoztathatja a felhasználókat arról, hogy hogyan és Mikor változnak, és hogyan szerezhet támogatást, ha szükséges.

Bár a hozzáférési panel általában nem hoz létre felhasználói problémákat, fontos, hogy előkészítse őket. Hozzon létre útmutatókat és a támogatási személyzet összes erőforrását az elindítása előtt.

#### <a name="communications-templates"></a>Kommunikációs sablonok

A Microsoft testreszabható sablonokat biztosít a hozzáférési panel [e-mailekhez és egyéb kommunikációhoz](https://aka.ms/APTemplates) . Ezeket az eszközöket más kommunikációs csatornákon is használhatja a vállalati kulturális környezetnek megfelelően.

## <a name="plan-your-sso-configuration"></a>Az egyszeri bejelentkezés konfigurációjának megtervezése

Amikor egy felhasználó bejelentkezik egy alkalmazásba, egy hitelesítési folyamaton keresztül történik, és bizonyítania kell, hogy kik azok. Az SSO nélkül a rendszer jelszót tárol az alkalmazásban, és a felhasználónak ismernie kell ezt a jelszót. Az SSO-val a felhasználók hitelesítő adatai átkerülnek az alkalmazásba, így nem kell újból megadniuk a jelszavakat az egyes alkalmazásokhoz.

Ha alkalmazásokat szeretne elindítani az alkalmazásokban, engedélyezni kell az egyszeri bejelentkezést.

Az Azure AD három különböző módon engedélyezheti az [egyszeri bejelentkezést az alkalmazásokban](what-is-single-sign-on.md):

* **Összevont egyszeri bejelentkezés** 
    * Lehetővé teszi egy alkalmazás számára, hogy felhasználói hitelesítésre átirányítsa az Azure AD-be a jelszó kérése helyett. 
    * A protokollt használó alkalmazások esetében támogatott, például az SAML 2,0, a WS-Federation vagy az OpenID Connect, és az egyszeri bejelentkezés legszélesebb módja.

* **Jelszó alapú egyszeri bejelentkezés** 
    * Lehetővé teszi a biztonságos alkalmazás jelszavainak tárolását és visszajátszását webböngésző-bővítmény vagy Mobile-alkalmazás használatával. 
    * Kihasználja az alkalmazás által biztosított meglévő bejelentkezési folyamat előnyeit, de lehetővé teszi a rendszergazdák számára a jelszavak kezelését. A felhasználónak nem kell tudnia a jelszót.

* **Meglévő egyszeri bejelentkezés** 
    * Lehetővé teszi az Azure AD számára, hogy kihasználhassa az alkalmazáshoz konfigurált meglévő egyszeri bejelentkezés előnyeit.
    * Lehetővé teszi, hogy ezek az alkalmazások az Office 365 vagy az Azure AD hozzáférési panel portálokhoz legyenek csatolva. 
    * További jelentéskészítés engedélyezése az Azure AD-ben az alkalmazások indításakor. 
    * Az Azure alkalmazásproxy és a csatolt egyszeri bejelentkezési mód használatát foglalja magában.

Itt megtudhatja, hogyan konfigurálhat egy alkalmazás egyszeri bejelentkezéses módját: [egyszeri bejelentkezés a Azure Active Directory lévő alkalmazásokba](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

A saját alkalmazások oldalával kapcsolatos legjobb megoldásként kezdje az összevont egyszeri bejelentkezéshez elérhető felhőalapú alkalmazások integrálásával. Az összevont egyszeri bejelentkezés lehetővé teszi a felhasználók számára, hogy egységes, egy kattintással elérhető felületet nyújtsanak az alkalmazásban a felületek elindítása előtt, és inkább robusztusak legyenek a konfigurációban.

Használjon összevont egyszeri bejelentkezést az Azure AD-vel (OpenID Connect/SAML), ha egy alkalmazás támogatja azt a jelszó-alapú egyszeri bejelentkezés és az ADFS helyett.

Az SaaS-alkalmazások üzembe helyezésével és konfigurálásával kapcsolatos további információkért lásd a [SaaS SSO telepítési tervét](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>A My apps Browser bővítmény üzembe helyezésének megtervezése

Amikor a felhasználók bejelentkeznek a jelszó-alapú SSO-alkalmazásokba, telepíteniük és használniuk kell a saját alkalmazások biztonságos bejelentkezési bővítményét. A bővítmény végrehajt egy parancsfájlt, amely továbbítja a jelszót az alkalmazás bejelentkezési űrlapjára. A rendszer felkéri a felhasználókat, hogy telepítse a bővítményt, amikor először elindítja a jelszó-alapú SSO-alkalmazást. A bővítménysel kapcsolatos további információkért tekintse meg ezt a dokumentációt a [hozzáférési panel böngésző bővítményének telepítéséhez](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav).

Ha a jelszó-alapú SSO-alkalmazásokat integrálni kell, meg kell határoznia azt a mechanizmust, amellyel a bővítményt a [támogatott böngészőkkel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)nagy méretben helyezheti üzembe. A lehetőségek a következők:

* [Csoportházirend az Internet Explorerben](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Configuration Manager az Internet Explorerben](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [A Chrome, a Firefox, a Microsoft Edge vagy az IE felhasználó által vezérelt letöltése és konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

További információ: [a jelszó egyszeri bejelentkezésének konfigurálása](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

Azok a felhasználók, akik nem használnak jelszó-alapú SSO-alkalmazásokat, a bővítmény előnyeit is kihasználhatják. Ezek az előnyök többek között lehetővé teszi bármely alkalmazás indítását a keresősáv használatával, a legutóbb használt alkalmazások elérésének megkeresését, valamint a saját alkalmazások lapra mutató hivatkozást.

A felhasználó a jelszó-alapú egyszeri bejelentkezési alkalmazás első indításakor a következőt fogja látni:

![Képernyőkép az alkalmazások böngésző bővítményének telepítési képernyőjéről ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Mobil hozzáférés tervezése

Az Intune-szabályzattal (Microsoft Edge vagy Intune Managed Browser) védett böngésző szükséges a jelszó-alapú egyszeri bejelentkezést használó mobil felhasználók számára. A szabályzattal védett böngészők lehetővé teszik az alkalmazás számára mentett jelszó átvitelét. A Microsoft Edge vagy a Managed Browser webes adatvédelmi funkciókat biztosít. Az iOS-és Android-eszközökön használhatja a Microsoft Edge vállalati forgatókönyveket is. A Microsoft Edge ugyanazokat a felügyeleti forgatókönyveket támogatja, mint a Intune Managed Browser és javítja a felhasználói élményt. További információ: [webes elérés kezelése Microsoft Intune szabályzattal védett böngésző használatával](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Saját alkalmazások üzembe helyezésének megtervezése

A hozzáférési panel alapja az Application Launcher saját alkalmazások, amelyekhez a felhasználók férhetnek hozzá [https://myapps.microsoft.com](https://myapps.microsoft.com/) . A saját alkalmazások oldalain egyetlen helyet biztosítanak a felhasználóknak a munkájuk elindításához és a szükséges alkalmazások eléréséhez. Itt a felhasználók az összes olyan alkalmazás listáját megtalálják, amelyhez egyszeri bejelentkezés hozzáférése van. 

![Az alkalmazások panel képernyőképe](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Ugyanazok az alkalmazások jelennek meg az Office 365 app launcherben, amikor a felhasználók az Office 365 portált használják.

Tervezze meg, hogy milyen sorrendben adja hozzá az alkalmazásokat a saját alkalmazások indítóhoz, és döntse el, hogy fokozatosan vagy egyszerre fogja-e őket bevezetni. Ehhez létre kell hoznia egy alkalmazás-leltárt, amely felsorolja a hitelesítés típusát és az egyes alkalmazásokhoz tartozó összes meglévő SSO-integrációt.

#### <a name="add-applications-to-the-my-apps-panel"></a>Alkalmazások hozzáadása a saját alkalmazások panelhez

Minden Azure AD SSO-kompatibilis alkalmazást hozzáadhat a saját alkalmazások indítóhoz. A csatolt egyszeri bejelentkezés lehetőséggel további alkalmazások is hozzáadódnak a szolgáltatáshoz. Beállíthat egy olyan alkalmazás-csempét, amely a meglévő webalkalmazás URL-címére hivatkozik. A csatolt egyszeri bejelentkezés lehetővé teszi, hogy a felhasználókat a saját alkalmazások portálra irányítsa anélkül, hogy az összes alkalmazást áttelepíti az Azure AD SSO-ba. Fokozatosan áthelyezheti az Azure AD SSO-ra konfigurált alkalmazásokat a felhasználói élmény megszakítása nélkül.

#### <a name="use-my-apps-collections"></a>Saját alkalmazások gyűjtemény használata

Alapértelmezés szerint az összes alkalmazás egyetlen oldalon jelenik meg. Azonban a gyűjtemények segítségével csoportosíthatja a kapcsolódó alkalmazásokat, és egy külön lapon is bemutathatja őket, így könnyebben megtalálhatja őket. A gyűjtemények segítségével például létrehozhat logikai csoportosításokat az alkalmazások számára adott feladatok, feladatok, projektek és így tovább. További információkért lásd: [az alkalmazások gyűjteményének használata a felhasználói hozzáférési panelek testreszabásához](access-panel-collections.md). 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Saját alkalmazások vagy meglévő portál használatának megtervezése

Előfordulhat, hogy a felhasználóknak már van alkalmazásuk vagy portálom a saját alkalmazásaitól. Ha igen, döntse el, hogy mindkét portált támogatni kívánja-e, vagy csak egyet használ.

Ha egy meglévő portál már használatban van a felhasználók számára kiindulási pontként, a felhasználói hozzáférési URL-címek használatával integrálhatja az alkalmazások funkcióit. A felhasználói hozzáférési URL-címek a saját alkalmazások portálon elérhető alkalmazásokra mutató közvetlen hivatkozásként működnek. Ezek az URL-címek beágyazható bármely meglévő webhelyre. Amikor a felhasználó kiválasztja a hivatkozást, megnyílik az alkalmazás a saját alkalmazások portálon.

A felhasználói hozzáférés URL-tulajdonsága az alkalmazás **Tulajdonságok** területén található a Azure Portalban.

![Az alkalmazások panel képernyőképe](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Önkiszolgáló alkalmazások felderítésének és hozzáférésének megtervezése

Miután telepítette az alkalmazások alapkészletét egy felhasználó saját alkalmazások lapján, engedélyeznie kell az önkiszolgáló alkalmazás-felügyeleti funkciókat. Az önkiszolgáló alkalmazás-felderítés lehetővé teszi a felhasználók számára a következőket:

* Megtalálhatja a saját alkalmazásaihoz hozzáadandó új alkalmazásokat. 
* Olyan opcionális alkalmazásokat adhat hozzá, amelyek esetleg nem tudják, hogy a telepítés során szükségük van rá.

A jóváhagyási munkafolyamatok az alkalmazások eléréséhez használhatók explicit jóváhagyásra. A jóváhagyó felhasználók a saját alkalmazások portálon kapnak értesítéseket, amikor az alkalmazáshoz való hozzáférésre vonatkozó kérelem van folyamatban.

## <a name="plan-self-service-group-membership"></a>Önkiszolgáló csoporttagság megtervezése 

Lehetővé teheti a felhasználók számára, hogy saját biztonsági Csoportjaikat vagy Office 365-csoportokat hozzanak létre és kezeljenek az Azure AD-ben. A csoport tulajdonosa jóváhagyhatja vagy megtagadhatja a tagsági kérelmeket, és delegálhatja a csoporttagság vezérlését. Az önkiszolgáló csoport felügyeleti funkciói nem érhetők el levelezési biztonsági csoportokhoz vagy terjesztési listához.

Az önkiszolgáló csoporttagság megtervezéséhez döntse el, hogy engedélyezi-e a szervezet összes felhasználója számára, hogy csoportokat hozzon létre és kezeljen, vagy csak a felhasználók egy részhalmazát. Ha engedélyezi a felhasználók egy részhalmazát, be kell állítania egy csoportot, amelyhez hozzá kívánja adni a felhasználókat. A forgatókönyvek engedélyezésével kapcsolatban lásd: [az önkiszolgáló csoport felügyeletének beállítása a Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) .

## <a name="plan-reporting-and-auditing"></a>Jelentéskészítés és naplózás tervezése

Az Azure AD olyan [jelentéseket biztosít, amelyek technikai és üzleti ismereteket kínálnak](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Az üzleti és műszaki alkalmazások tulajdonosai együttműködve vehetik igénybe ezeket a jelentéseket, és rendszeres időközönként használhatják azokat. A következő táblázat néhány példát mutat be a tipikus jelentéskészítési forgatókönyvekre.

| Példa | A kockázatok kezelése| Termelékenység növelése| Cégirányítás és megfelelőség |
|  - |- | - | - |
| Jelentéstípusok|  Alkalmazás-engedélyek és-használat| Fiók létesítési tevékenysége| Tekintse át, ki fér hozzá az alkalmazásokhoz |
| Lehetséges műveletek| Hozzáférés naplózása; engedélyek visszavonása| Kiépítési hibák szervizelése| Hozzáférés visszavonása |

Az Azure AD a legtöbb naplózási adattal 30 napig tart. Az adatai az Azure felügyeleti portálon vagy API-n keresztül érhetők el az elemzési rendszerbe való letöltéshez.

#### <a name="auditing"></a>Naplózás

Az alkalmazás-hozzáférés naplófájljai 30 napig érhetők el. Ha a vállalaton belül a biztonsági naplózás nagyobb adatmegőrzést igényel, a naplókat egy biztonsági információs esemény és felügyelet (SIEM) eszközbe kell exportálni, például splunk vagy ArcSight.

Naplózási, jelentéskészítési és vész-helyreállítási biztonsági másolatok esetén dokumentálja a letöltés szükséges gyakoriságát, a megcélzott rendszerét, valamint az egyes biztonsági másolatok kezeléséért felelős személyeket. Előfordulhat, hogy nincs szüksége külön naplózási és jelentéskészítési biztonsági másolatokra. A vész-helyreállítási biztonsági mentésnek különálló entitásnak kell lennie.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Alkalmazások üzembe helyezése a felhasználók saját alkalmazások paneljén

Miután az alkalmazás be lett állítva az SSO-ra, a csoportok hozzá vannak rendelve. A hozzárendelt csoportokban lévő felhasználók hozzáférhetnek majd az alkalmazáshoz, és az Office 365 app launcherben jelennek meg.

Lásd: [felhasználók és csoportok társítása egy alkalmazáshoz Active Directory-ben](methods-for-assigning-users-and-groups.md).

Ha tesztelés vagy üzembe helyezés során szeretné felvenni a csoportokat, de még nem engedélyezi az alkalmazások használatát a saját alkalmazásokban, tekintse meg az [alkalmazás elrejtése a felhasználói felületről Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)című témakört.

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Microsoft Office 365-alkalmazások üzembe helyezése az alkalmazásokban

Office 365-alkalmazások esetén a felhasználók a hozzájuk rendelt licencek alapján kapják meg az Office-példányt. Az Office-alkalmazásokhoz való hozzáférés előfeltétele, hogy a felhasználók az Office-alkalmazásokhoz kötött megfelelő licenceket kapjanak. Amikor licencet rendel egy felhasználóhoz, a saját alkalmazások lapján és az Office 365 app launcherben automatikusan megjelennek a licenccel társított alkalmazások.

Ha az Office-alkalmazások egy készletét szeretné elrejteni a felhasználóktól, lehetősége van az alkalmazások elrejtésére a saját alkalmazások portálján, miközben továbbra is engedélyezheti a hozzáférést az Office 365-portálról. Ezeket a beállításokat az alkalmazás felhasználói beállítások részében találja meg. További információ: [alkalmazás elrejtése a felhasználók Azure Active Directoryban való felhasználói felületéről](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Képernyőkép az alkalmazások elrejtésének konfigurálásáról](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Alkalmazás önkiszolgáló képességeinek üzembe helyezése

Az önkiszolgáló alkalmazás-hozzáférés lehetővé teszi a felhasználók számára, hogy felfedezzék és hozzáférjenek az alkalmazásokhoz. A felhasználók minden alkalommal hozzáférhetnek a szükséges alkalmazásokhoz anélkül, hogy hozzáférést kellene kérniük az IT-csoportokhoz. Amikor egy felhasználó hozzáférést kér, és a jóváhagyása automatikusan vagy manuálisan történik az alkalmazás tulajdonosa által, egy csoportba kerülnek a háttérbe. A jelentéskészítés engedélyezve van a (z) szolgáltatásban, hogy ki kérte, jóváhagyta vagy eltávolította a hozzáférést, és lehetővé teszi a hozzárendelt szerepkörök felügyeletét.

Delegálhatja az alkalmazás hozzáférési kéréseinek jóváhagyását az üzleti jóváhagyóknak. Az üzleti jóváhagyó az alkalmazás-hozzáférési jelszavakat az üzleti jóváhagyó saját alkalmazások oldaláról állíthatja be.

További információ: [az önkiszolgáló alkalmazás-hozzáférés használata](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to).

![Képernyőkép az önkiszolgáló alkalmazások kezelésének konfigurálásáról](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Az üzemelő példány ellenőrzése

Győződjön meg arról, hogy a hozzáférési panel üzembe helyezése alaposan ki van tesztelve, és a visszaállítási terv van érvényben.

A következő teszteket a vállalati tulajdonú eszközökkel és a személyes eszközökkel kell elvégezni. Ezeknek a tesztelési eseteknek az üzleti használati eseteit is tükröznie kell. A következő néhány eset az ebben a dokumentumban és a tipikus technikai forgatókönyvekben szereplő üzleti követelmények alapján történik. Adja hozzá az igényeinek megfelelő egyéb szolgáltatásokat.

#### <a name="application-sso-access-test-case-examples"></a>Alkalmazás egyszeri bejelentkezéses hozzáférésének tesztelésére vonatkozó példák:


| Üzleti eset| Várt eredmény |
| - | -|
| A felhasználó bejelentkezik a saját alkalmazások portálra| A felhasználó bejelentkezhet, és megtekintheti az alkalmazásaikat |
| A felhasználó egy összevont SSO-alkalmazást indít el| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó első alkalommal indítja el a jelszó-SSO-alkalmazást| A felhasználónak telepítenie kell a saját alkalmazások bővítményt |
| A felhasználó a következő alkalommal indít el egy jelszó-SSO-alkalmazást| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó az Office 365 portálról indít el egy alkalmazást| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó elindít egy alkalmazást a Managed Browser| A felhasználó automatikusan bejelentkezik az alkalmazásba |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Alkalmazások önkiszolgáló képességei – tesztelési példák


| Üzleti eset| Várt eredmény |
| - | - |
| A felhasználó kezelheti a tagságot az alkalmazásban| A felhasználó hozzáadhat vagy eltávolíthat olyan tagokat, akik hozzáféréssel rendelkeznek az alkalmazáshoz |
| A felhasználó szerkesztheti az alkalmazást| A felhasználó szerkesztheti az alkalmazás leírását és a jelszó egyszeri bejelentkezéses alkalmazásaihoz tartozó hitelesítő adatokat. |

### <a name="rollback-steps"></a>Visszaállítási lépések

Fontos megtervezni, hogy mi a teendő, ha a központi telepítés nem a tervezett módon történik. Ha az egyszeri bejelentkezés konfigurálása nem sikerül az üzembe helyezés során, meg kell ismernie az [egyszeri bejelentkezés hibáinak elhárítását](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) és a felhasználókra gyakorolt hatás csökkentését. Szélsőséges körülmények között előfordulhat, hogy vissza kell [állítania az egyszeri bejelentkezést](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>A megvalósítás kezelése

A legkevesebb Kiemelt szerepkört kell használnia a szükséges feladatok végrehajtásához Azure Active Directoryon belül. [Tekintse át az elérhető különböző szerepköröket](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) , és válassza ki a megfelelőt az alkalmazáshoz tartozó egyes personák igényeinek megoldásához. Előfordulhat, hogy bizonyos szerepköröket átmenetileg kell alkalmazni, és el kell távolítani az üzembe helyezés befejeződése után.

| Szerepek| Szerepkörök| Azure AD-szerepkör  |
| - | -| -|
| Segélyszolgálat rendszergazdája| 1. szintű támogatás| None |
| Identity admin| Konfigurálás és hibakeresés az Azure AD-t érintő problémák esetén| Globális rendszergazda |
| Alkalmazás-rendszergazda| Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja| None |
| Infrastruktúra-rendszergazdák| Tanúsítvány-átváltási tulajdonos| Globális rendszergazda |
| Üzleti tulajdonos/érintett fél| Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja| None |

A [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) segítségével kezelheti a szerepköröket, hogy további naplózási, vezérlési és hozzáférési felülvizsgálatot biztosítson a címtár-jogosultságokkal rendelkező felhasználók számára.

### <a name="troubleshoot-access-panel-issues"></a>Hozzáférési panel problémáinak elhárítása

Hozzon létre hibaelhárítási útmutatót a támogatási szervezete számára a gyakori forgatókönyvekkel, amelyek a Microsoft-dokumentációra mutatnak a felbontásban. Előfordulhat, hogy olyan segédvonalakat szeretne létrehozni, amelyek a szervezet által használt rétegekbe bontják a támogatást.

Lásd a következő hibaelhárítási útmutatókat:

[Nem megjelenő alkalmazások](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Váratlan alkalmazások jelennek meg](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[A felhasználó nem tud bejelentkezni a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Az önkiszolgáló alkalmazás-hozzáférés használatával kapcsolatos problémák](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[A böngésző kiterjesztésével kapcsolatos problémák](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>További lépések

[Az Azure Multi-Factor Authentication üzembe helyezésének megtervezése](https://aka.ms/deploymentplans/mfa)
