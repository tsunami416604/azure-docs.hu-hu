---
title: Azure Active Directory saját alkalmazások üzembe helyezésének megtervezése
description: Útmutatás Azure Active Directory saját alkalmazások üzembe helyezéséhez
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.openlocfilehash: 0bff283b8e9c0c753100c635ecd4451b467c206d
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146623"
---
# <a name="plan-an-azure-active-directory-my-apps-deployment"></a>Azure Active Directory saját alkalmazások üzembe helyezésének megtervezése

Azure Active Directory (Azure AD) az alkalmazásaim egy webalapú portál, amely segít csökkenteni a támogatási költségeket, növelheti a hatékonyságot és a biztonságot, és csökkentheti a felhasználói frusztrációt. A rendszer részletes jelentéseket tartalmaz, amelyek nyomon követik a rendszer elérését, és értesítik a rendszergazdákat a visszaélésről vagy a visszaélésről. Ha szeretne többet megtudni az alkalmazások végfelhasználói perspektívából való használatáról, tekintse meg [az alkalmazások portál súgóját](../user-help/my-apps-portal-end-user-access.md).

Az Azure AD saját alkalmazások használatával a következőket teheti:

* Felderítheti és elérheti vállalata Azure AD-hez kapcsolódó erőforrásait, például az alkalmazásokat
* Hozzáférés kérése új alkalmazásokhoz és csoportokhoz
* Ezekhez az erőforrásokhoz való hozzáférés kezelése mások számára
* Önkiszolgáló jelszavak alaphelyzetbe állítása és az Azure Multi-Factor Authentication beállításainak kezelése
* Eszközök kezelése

Emellett a rendszergazdák számára is lehetővé teszi a felügyeletet:

* Szolgáltatási feltételek
* Cégek
* Hozzáférési felülvizsgálatok


## <a name="benefits-of-azure-ad-my-apps-integration"></a>Az Azure AD alkalmazás-integráció előnyei

Az Azure AD saját alkalmazásai a következő módokon nyújtják a vállalatokat:

**Intuitív felhasználói élményt nyújt**: az alkalmazásaink egyetlen platformot biztosítanak az összes Azure-beli egyszeri bejelentkezéshez (SSO) csatlakoztatott alkalmazáshoz. A meglévő beállításokat és új képességeket (például a csoportok kezelését és az önkiszolgáló jelszó-visszaállítást) egy egységes portálon találja, ahogy hozzájuk lettek adva. Az intuitív élmény lehetővé teszi, hogy a felhasználók gyorsabban és hatékonyabban térjenek vissza a munkába, miközben csökkentik a frusztrációt.

**Növeli a termelékenységet**: az alkalmazásokban lévő összes felhasználói alkalmazáshoz SSO engedélyezve van. A nagyvállalati alkalmazások egyszeri bejelentkezésének engedélyezése és a Microsoft 365 a további bejelentkezési kérések csökkentése vagy kiküszöbölése révén kiváló bejelentkezési élményt hozhat létre. Az alkalmazások önkiszolgáló és dinamikus tagságot használnak, és növelik az Ön Identity rendszerének általános biztonságát. A saját alkalmazások biztosítják, hogy a megfelelő személyek kezeljék az alkalmazásokhoz való hozzáférést. A saját alkalmazások egy koherens kezdőlapként szolgálnak, amellyel gyorsan megtalálhatja az erőforrásokat, és folytathatja a munkahelyi feladatokat.

A szolgáltatás **kezelése**: a saját alkalmazások Azure ad-vel való engedélyezése segíthet a helyszíni infrastruktúrák kiválasztásában. A támogatási költségeket csökkenti azáltal, hogy egységes portált biztosít az összes alkalmazás megkereséséhez, az erőforrásokhoz való hozzáférés kéréséhez és a fiókok kezeléséhez.

**Növeli a rugalmasságot és a biztonságot**: az alkalmazások lehetővé teszi a felhőalapú platform által biztosított biztonság és rugalmasság elérését. A rendszergazdák egyszerűen módosíthatják a beállításokat az alkalmazásokra és az erőforrásokra, és az új biztonsági követelményekkel is rendelkezhetnek anélkül, hogy ez befolyásolná a felhasználókat

A **robusztus naplózás és használat nyomon követését teszi lehetővé**: az összes felhasználói funkció naplózási és használati nyomon követésével megtudhatja, hogy mikor használják a felhasználók az erőforrásaikat, és biztosítja, hogy értékelni tudja a biztonságot.

### <a name="licensing-considerations"></a>Licencelési megfontolások

Az alkalmazások ingyenesek, és nem igényelnek alapszintű licencet. A címtárban lévő objektumok száma és a telepíteni kívánt további funkciók azonban további licenceket igényelhetnek. A licencelési követelményekkel rendelkező gyakori Azure AD-forgatókönyvek a következő biztonsági funkciókat tartalmazzák:

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)
* [Csoport alapú tagság](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
* [Új jelszó önkiszolgáló kérése](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
* [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Tekintse [meg az Azure ad teljes licencelési útmutatóját](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-azure-ad-my-apps"></a>Az Azure AD saját alkalmazásainak üzembe helyezésének előfeltételei

A projekt megkezdése előtt végezze el a következő előfeltételeket:

* [Alkalmazás egyszeri bejelentkezésének integrálása](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)
* [Azure AD-felhasználó és-csoport infrastruktúrájának kezelése](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-azure-ad-my-apps-deployment"></a>Az Azure AD saját alkalmazások üzembe helyezésének megtervezése

Az alábbi táblázat a saját alkalmazások központi telepítésének legfontosabb használati eseteit ismerteti:

| Terület| Leírás |
| - | - |
| Access| A saját alkalmazások portál a vállalati hálózaton belüli vállalati és személyes eszközökről érhető el. |
|Access | A saját alkalmazások portál a vállalati hálózaton kívüli vállalati eszközökről is elérhető. |
| Naplózás| A használati adatok legalább 29 naponta letöltődnek a vállalati rendszerbe. |
| Szabályozás| Az Azure AD-hez csatlakoztatott alkalmazások és csoportok felhasználói hozzárendeléseinek életciklusa definiálva és figyelve van. |
| Biztonság| Az erőforrásokhoz való hozzáférést felhasználói és csoportos hozzárendelések segítségével szabályozhatja. Csak a jogosult felhasználók kezelhetik az erőforrás-hozzáférést. |
| Teljesítmény| A hozzáférési hozzárendelések terjesztési ütemterveit dokumentálják és figyelik. |
| Felhasználó felület| A felhasználók tisztában vannak az alkalmazások képességeivel, és hogyan használhatók.|
| Felhasználó felület| A felhasználók kezelhetik az alkalmazások és a csoportok hozzáférését.|
| Felhasználó felület| A felhasználók kezelhetik a fiókjaikat. |
| Felhasználó felület| A felhasználók tisztában vannak a böngészők kompatibilitásával. |
| Támogatás| A felhasználók megtalálhatják az alkalmazások problémáinak támogatását. |


> [!TIP]
> A saját alkalmazások a belső vállalati URL-címekkel is használhatók az alkalmazásproxy használatával. További információ: [oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez az alkalmazásproxy használatával Azure Active Directoryban](application-proxy-add-on-premises-application.md).

### <a name="best-practices-for-deploying-azure-ad-my-apps"></a>Ajánlott eljárások az Azure AD-alkalmazások üzembe helyezéséhez

A saját alkalmazások funkcióit fokozatosan lehet engedélyezni. A következő üzembe helyezési sorrendet javasoljuk:

1. Saját alkalmazások
   * App Launcher
   * Önkiszolgáló alkalmazások kezelése
   * Microsoft 365 integráció

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

### <a name="plan-configurations-for-azure-my-apps"></a>Az Azure-beli alkalmazások konfigurációjának megtervezése

A következő táblázat a saját alkalmazások számos fontos konfigurációját és az esetlegesen használt jellemző értékeket sorolja fel:

| Konfiguráció| Jellemző értékek |
| - | - |
| A kísérleti csoportok meghatározása| Azonosítsa a használni kívánt Azure AD biztonsági csoportot, és győződjön meg arról, hogy az összes kísérleti tag a csoport részét képezi. |
| Határozza meg az éles környezetben engedélyezni kívánt csoportokat vagy csoportokat.| Azonosítsa a használni kívánt Azure AD-beli biztonsági csoportokat, illetve az Azure AD-vel szinkronizált Active Directory csoportokat. Győződjön meg arról, hogy az összes kísérleti tag a csoport részét képezi. |
| Egyszeri bejelentkezés használatának engedélyezése a felhasználók számára bizonyos típusú alkalmazásokhoz| Összevont egyszeri bejelentkezés, OAuth, jelszó egyszeri bejelentkezés, alkalmazásproxy |
| Önkiszolgáló jelszó-visszaállítás használatának engedélyezése a felhasználók számára | Igen |
| Multi-Factor Authentication használatának engedélyezése a felhasználók számára| Igen |
| Lehetővé teszi a felhasználók számára az önkiszolgáló csoportok felügyeletének használatát bizonyos típusú csoportok esetében| Biztonsági csoportok, Microsoft 365 csoportok |
| Az önkiszolgáló alkalmazások felügyeletének engedélyezése a felhasználók számára| Igen |
| Hozzáférési felülvizsgálatok használatának engedélyezése a felhasználók számára| Igen |

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

Bár az alkalmazások általában nem hoznak létre felhasználói problémákat, fontos, hogy előkészítse őket. Hozzon létre útmutatókat és a támogatási személyzet összes erőforrását az elindítása előtt.

#### <a name="communications-templates"></a>Kommunikációs sablonok

A Microsoft [testreszabható sablonokat biztosít az e-mailekhez és](https://aka.ms/APTemplates) az alkalmazásokhoz kapcsolódó egyéb kommunikációhoz. Ezeket az eszközöket más kommunikációs csatornákon is használhatja a vállalati kulturális környezetnek megfelelően.

## <a name="plan-your-sso-configuration"></a>Az egyszeri bejelentkezés konfigurációjának megtervezése

Amikor egy felhasználó bejelentkezik egy alkalmazásba, egy hitelesítési folyamaton keresztül történik, és bizonyítania kell, hogy kik azok. Az SSO nélkül a rendszer jelszót tárol az alkalmazásban, és a felhasználónak ismernie kell ezt a jelszót. Az SSO-val a felhasználók hitelesítő adatai átkerülnek az alkalmazásba, így nem kell újból megadniuk a jelszavakat az egyes alkalmazásokhoz.

Ha alkalmazásokat szeretne elindítani az alkalmazásokban, engedélyezni kell az egyszeri bejelentkezést. Az Azure AD több SSO-beállítást is támogat. További információ: [az egyszeri bejelentkezés lehetőségei az Azure ad-ben](sso-options.md).

> [!NOTE]
> Ha többet szeretne megtudni az Azure AD-alkalmazás identitás-szolgáltatóként való használatáról az alkalmazáshoz, tekintse meg a gyors üzembe helyezési [sorozatot az alkalmazások kezelésében](view-applications-portal.md).

A saját alkalmazások oldalával kapcsolatos legjobb megoldásként kezdje az összevont egyszeri bejelentkezéshez elérhető felhőalapú alkalmazások integrálásával. Az összevont egyszeri bejelentkezés lehetővé teszi a felhasználók számára, hogy egységes, egy kattintással elérhető felületet nyújtsanak az alkalmazásban a felületek elindítása előtt, és inkább robusztusak legyenek a konfigurációban.

Használjon összevont egyszeri bejelentkezést az Azure AD-vel (OpenID Connect/SAML), ha egy alkalmazás támogatja azt a jelszó-alapú egyszeri bejelentkezés és az ADFS helyett.

Az SaaS-alkalmazások üzembe helyezésével és konfigurálásával kapcsolatos további információkért lásd a [SaaS SSO telepítési tervét](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>A My apps Browser bővítmény üzembe helyezésének megtervezése

Amikor a felhasználók bejelentkeznek a jelszó-alapú SSO-alkalmazásokba, telepíteniük és használniuk kell a saját alkalmazások biztonságos bejelentkezési bővítményét. A bővítmény végrehajt egy parancsfájlt, amely továbbítja a jelszót az alkalmazás bejelentkezési űrlapjára. A rendszer felkéri a felhasználókat, hogy telepítse a bővítményt, amikor először elindítja a jelszó-alapú SSO-alkalmazást. A bővítménysel kapcsolatos további információkért tekintse meg a jelen dokumentációban az [alkalmazások böngésző bővítményének telepítését](access-panel-extension-problem-installing.md)ismertető témakört.

Ha a jelszó-alapú SSO-alkalmazásokat integrálni kell, meg kell határoznia azt a mechanizmust, amellyel a bővítményt a [támogatott böngészőkkel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)nagy méretben helyezheti üzembe. A lehetőségek a következők:

* [Csoportházirend az Internet Explorerben](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
* [Configuration Manager az Internet Explorerben](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
* [A Chrome, a Firefox, a Microsoft Edge vagy az IE felhasználó által vezérelt letöltése és konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Azok a felhasználók, akik nem használnak jelszó-alapú SSO-alkalmazásokat, a bővítmény előnyeit is kihasználhatják. Ezek az előnyök többek között lehetővé teszi bármely alkalmazás indítását a keresősáv használatával, a legutóbb használt alkalmazások elérésének megkeresését, valamint a saját alkalmazások lapra mutató hivatkozást.

#### <a name="plan-for-mobile-access"></a>Mobil hozzáférés tervezése

Az Intune-szabályzattal (Microsoft Edge vagy Intune Managed Browser) védett böngésző szükséges a jelszó-alapú egyszeri bejelentkezést használó mobil felhasználók számára. A szabályzattal védett böngészők lehetővé teszik az alkalmazás számára mentett jelszó átvitelét. A Microsoft Edge vagy a Managed Browser webes adatvédelmi funkciókat biztosít. Az iOS-és Android-eszközökön használhatja a Microsoft Edge vállalati forgatókönyveket is. A Microsoft Edge ugyanazokat a felügyeleti forgatókönyveket támogatja, mint a Intune Managed Browser és javítja a felhasználói élményt. További információ: [webes elérés kezelése Microsoft Intune szabályzattal védett böngésző használatával](https://docs.microsoft.com/intune/app-configuration-managed-browser).

## <a name="plan-your-my-apps-deployment"></a>Saját alkalmazások üzembe helyezésének megtervezése

Az alkalmazások alapja az Application Launcher portál, amely a felhasználók számára érhető el [https://myapps.microsoft.com](https://myapps.microsoft.com/) . A saját alkalmazások lap egyetlen helyet biztosít a felhasználóknak a munkájuk elindításához és a szükséges alkalmazások eléréséhez. Itt a felhasználók az összes olyan alkalmazás listáját megtalálják, amelyhez egyszeri bejelentkezés hozzáférése van. 

> [!NOTE]
> Ugyanazok az alkalmazások jelennek meg a Microsoft 365 app launcherben.

Tervezze meg, hogy milyen sorrendben adja hozzá az alkalmazásokat a saját alkalmazások indítóhoz, és döntse el, hogy fokozatosan vagy egyszerre fogja-e őket bevezetni. Ehhez létre kell hoznia egy alkalmazás-leltárt, amely felsorolja a hitelesítés típusát és az egyes alkalmazásokhoz tartozó összes meglévő SSO-integrációt.

#### <a name="add-applications-to-the-my-apps-panel"></a>Alkalmazások hozzáadása a saját alkalmazások panelhez

Minden Azure AD SSO-kompatibilis alkalmazást hozzáadhat a saját alkalmazások indítóhoz. A csatolt egyszeri bejelentkezés lehetőséggel további alkalmazások is hozzáadódnak a szolgáltatáshoz. Beállíthat egy olyan alkalmazás-csempét, amely a meglévő webalkalmazás URL-címére hivatkozik. A csatolt egyszeri bejelentkezés lehetővé teszi, hogy a felhasználókat a saját alkalmazások portálra irányítsa anélkül, hogy az összes alkalmazást áttelepíti az Azure AD SSO-ba. Fokozatosan áthelyezheti az Azure AD SSO-ra konfigurált alkalmazásokat a felhasználói élmény megszakítása nélkül.

#### <a name="use-my-apps-collections"></a>Saját alkalmazások gyűjtemény használata

Alapértelmezés szerint az összes alkalmazás egyetlen oldalon jelenik meg. Azonban a gyűjtemények segítségével csoportosíthatja a kapcsolódó alkalmazásokat, és egy külön lapon is bemutathatja őket, így könnyebben megtalálhatja őket. A gyűjtemények segítségével például létrehozhat logikai csoportosításokat az alkalmazások számára adott feladatok, feladatok, projektek és így tovább. További információ: [How to use My apps Collections](access-panel-collections.md). 

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

Lehetővé teheti a felhasználók számára, hogy saját biztonsági csoportokat vagy Microsoft 365 csoportokat hozzanak létre és kezeljenek az Azure AD-ben. A csoport tulajdonosa jóváhagyhatja vagy megtagadhatja a tagsági kérelmeket, és delegálhatja a csoporttagság vezérlését. Az önkiszolgáló csoport felügyeleti funkciói nem érhetők el levelezési biztonsági csoportokhoz vagy terjesztési listához.

Az önkiszolgáló csoporttagság megtervezéséhez döntse el, hogy engedélyezi-e a szervezet összes felhasználója számára, hogy csoportokat hozzon létre és kezeljen, vagy csak a felhasználók egy részhalmazát. Ha engedélyezi a felhasználók egy részhalmazát, be kell állítania egy csoportot, amelyhez hozzá kívánja adni a felhasználókat. A forgatókönyvek engedélyezésével kapcsolatban lásd: [az önkiszolgáló csoport felügyeletének beállítása a Azure Active Directoryban](../users-groups-roles/groups-self-service-management.md) .

## <a name="plan-reporting-and-auditing"></a>Jelentéskészítés és naplózás tervezése

Az Azure AD olyan [jelentéseket biztosít, amelyek technikai és üzleti ismereteket kínálnak](../reports-monitoring/overview-reports.md). Az üzleti és műszaki alkalmazások tulajdonosai együttműködve vehetik igénybe ezeket a jelentéseket, és rendszeres időközönként használhatják azokat. A következő táblázat néhány példát mutat be a tipikus jelentéskészítési forgatókönyvekre.

| Példa | A kockázatok kezelése| Termelékenység növelése| Cégirányítás és megfelelőség |
|  - |- | - | - |
| Jelentéstípusok|  Alkalmazás-engedélyek és-használat| Fiók létesítési tevékenysége| Tekintse át, ki fér hozzá az alkalmazásokhoz |
| Lehetséges műveletek| Hozzáférés naplózása; engedélyek visszavonása| Kiépítési hibák szervizelése| Hozzáférés visszavonása |

Az Azure AD a legtöbb naplózási adattal 30 napig tart. Az adatai az Azure felügyeleti portálon vagy API-n keresztül érhetők el az elemzési rendszerbe való letöltéshez.

#### <a name="auditing"></a>Naplózás

Az alkalmazás-hozzáférés naplófájljai 30 napig érhetők el. Ha a vállalaton belül a biztonsági naplózás nagyobb adatmegőrzést igényel, a naplókat egy biztonsági információs esemény és felügyelet (SIEM) eszközbe kell exportálni, például splunk vagy ArcSight.

Naplózási, jelentéskészítési és vész-helyreállítási biztonsági másolatok esetén dokumentálja a letöltés szükséges gyakoriságát, a megcélzott rendszerét, valamint az egyes biztonsági másolatok kezeléséért felelős személyeket. Előfordulhat, hogy nincs szüksége külön naplózási és jelentéskészítési biztonsági másolatokra. A vész-helyreállítási biztonsági mentésnek különálló entitásnak kell lennie.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Alkalmazások üzembe helyezése a felhasználók saját alkalmazások paneljén

Miután az alkalmazás be lett állítva az SSO-ra, a csoportok hozzá vannak rendelve. A hozzárendelt csoportokban lévő felhasználók hozzáférhetnek majd az alkalmazáshoz a saját alkalmazások és a Microsoft 365 app Launcher szolgáltatásban.

Lásd: [felhasználók és csoportok társítása egy alkalmazáshoz Active Directory-ben](methods-for-assigning-users-and-groups.md).

Ha tesztelés vagy üzembe helyezés során szeretné felvenni a csoportokat, de még nem engedélyezi az alkalmazások használatát a saját alkalmazásokban, tekintse meg az [alkalmazás elrejtése a felhasználói felületről Azure Active Directoryban](hide-application-from-user-portal.md)című témakört.

### <a name="deploy-microsoft-365-applications-to-my-apps"></a>Microsoft 365 alkalmazások üzembe helyezése az alkalmazásokban

Microsoft 365 alkalmazások esetében a felhasználók az Office egy példányát kapják meg a hozzájuk rendelt licencek alapján. Az Office-alkalmazásokhoz való hozzáférés előfeltétele, hogy a felhasználók az Office-alkalmazásokhoz kötött megfelelő licenceket kapjanak. Ha hozzárendel egy licencet a felhasználóhoz, a rendszer automatikusan megtekinti a licenccel társított alkalmazásokat a saját alkalmazások lapján és a Microsoft 365 app launcherben.

Ha az Office-alkalmazások egy készletét szeretné elrejteni a felhasználóktól, lehetőség van az alkalmazások elrejtésére a saját alkalmazások portálról, miközben továbbra is engedélyezi a hozzáférést a Microsoft 365 portálról. További információ: [alkalmazás elrejtése a felhasználók Azure Active Directoryban való felhasználói felületéről](hide-application-from-user-portal.md).

### <a name="deploy-application-self-service-capabilities"></a>Alkalmazás önkiszolgáló képességeinek üzembe helyezése

Az önkiszolgáló alkalmazás-hozzáférés lehetővé teszi a felhasználók számára, hogy felfedezzék és hozzáférjenek az alkalmazásokhoz. A felhasználók minden alkalommal hozzáférhetnek a szükséges alkalmazásokhoz anélkül, hogy hozzáférést kellene kérniük az IT-csoportokhoz. Amikor egy felhasználó hozzáférést kér, és a jóváhagyása automatikusan vagy manuálisan történik az alkalmazás tulajdonosa által, egy csoportba kerülnek a háttérbe. A jelentéskészítés engedélyezve van a (z) szolgáltatásban, hogy ki kérte, jóváhagyta vagy eltávolította a hozzáférést, és lehetővé teszi a hozzárendelt szerepkörök felügyeletét.

Delegálhatja az alkalmazás hozzáférési kéréseinek jóváhagyását az üzleti jóváhagyóknak. Az üzleti jóváhagyó az alkalmazás-hozzáférési jelszavakat az üzleti jóváhagyó saját alkalmazások oldaláról állíthatja be.

További információ: [az önkiszolgáló alkalmazás-hozzáférés használata](access-panel-manage-self-service-access.md).

## <a name="validate-your-deployment"></a>Az üzemelő példány ellenőrzése

Győződjön meg arról, hogy a saját alkalmazások üzembe helyezését alaposan tesztelték, és a visszaállítási terv van érvényben.

A következő teszteket a vállalati tulajdonú eszközökkel és a személyes eszközökkel kell elvégezni. Ezeknek a tesztelési eseteknek az üzleti használati eseteit is tükröznie kell. A következő néhány eset az ebben a dokumentumban és a tipikus technikai forgatókönyvekben szereplő üzleti követelmények alapján történik. Adja hozzá az igényeinek megfelelő egyéb szolgáltatásokat.

#### <a name="application-sso-access-test-case-examples"></a>Alkalmazás egyszeri bejelentkezéses hozzáférésének tesztelésére vonatkozó példák:


| Üzleti eset| Várt eredmény |
| - | -|
| A felhasználó bejelentkezik a saját alkalmazások portálra| A felhasználó bejelentkezhet, és megtekintheti az alkalmazásaikat |
| A felhasználó egy összevont SSO-alkalmazást indít el| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó első alkalommal indítja el a jelszó-SSO-alkalmazást| A felhasználónak telepítenie kell a saját alkalmazások bővítményt |
| A felhasználó a következő alkalommal indít el egy jelszó-SSO-alkalmazást| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó elindít egy alkalmazást Microsoft 365 portálról| A felhasználó automatikusan bejelentkezik az alkalmazásba |
| A felhasználó elindít egy alkalmazást a Managed Browser| A felhasználó automatikusan bejelentkezik az alkalmazásba |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Alkalmazások önkiszolgáló képességei – tesztelési példák

| Üzleti eset| Várt eredmény |
| - | - |
| A felhasználó kezelheti a tagságot az alkalmazásban| A felhasználó hozzáadhat vagy eltávolíthat olyan tagokat, akik hozzáféréssel rendelkeznek az alkalmazáshoz |
| A felhasználó szerkesztheti az alkalmazást| A felhasználó szerkesztheti az alkalmazás leírását és a jelszó egyszeri bejelentkezéses alkalmazásaihoz tartozó hitelesítő adatokat. |

### <a name="rollback-steps"></a>Visszaállítási lépések

Fontos megtervezni, hogy mi a teendő, ha a központi telepítés nem a tervezett módon történik. Ha az egyszeri bejelentkezés konfigurálása nem sikerül az üzembe helyezés során, meg kell ismernie az [egyszeri bejelentkezés hibáinak elhárítását](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) és a felhasználókra gyakorolt hatás csökkentését. Szélsőséges körülmények között előfordulhat, hogy vissza kell [állítania az egyszeri bejelentkezést](../manage-apps/plan-sso-deployment.md#rollback-process).


## <a name="manage-your-implementation"></a>A megvalósítás kezelése

A legkevesebb Kiemelt szerepkörrel hajtson végre egy szükséges feladatot a Azure Active Directoryon belül. [Tekintse át az elérhető különböző szerepköröket](../users-groups-roles/directory-assign-admin-roles.md) , és válassza ki a megfelelőt az alkalmazáshoz tartozó egyes personák igényeinek megoldásához. Előfordulhat, hogy bizonyos szerepköröket átmenetileg kell alkalmazni, és el kell távolítani az üzembe helyezés befejeződése után.

| Szerepek| Szerepkörök| Azure AD-szerepkör  |
| - | -| -|
| Segélyszolgálat rendszergazdája| 1. szintű támogatás| Nincs |
| Identity admin| Konfigurálás és hibakeresés az Azure AD-t érintő problémák esetén| Globális rendszergazda |
| Alkalmazás-rendszergazda| Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja| Nincs |
| Infrastruktúra-rendszergazdák| Tanúsítvány-átváltási tulajdonos| Globális rendszergazda |
| Üzleti tulajdonos/érintett fél| Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja| Nincs |

A [Privileged Identity Management](../privileged-identity-management/pim-configure.md) segítségével kezelheti a szerepköröket, hogy további naplózási, vezérlési és hozzáférési felülvizsgálatot biztosítson a címtár-jogosultságokkal rendelkező felhasználók számára.

## <a name="next-steps"></a>Következő lépések
[Az Azure Multi-Factor Authentication üzembe helyezésének megtervezése](https://aka.ms/deploymentplans/mfa)
