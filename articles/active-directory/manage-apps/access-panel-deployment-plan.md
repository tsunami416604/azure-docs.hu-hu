---
title: Azure Active Directory-hozzáférési panel üzembe helyezésének megtervezése
description: Útmutató az Azure AD hozzáférési panel funkcióinak üzembe helyezéséhez
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
ms.date: 08/16/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448af7075297c2b18df1eceaeaac34d5da762489
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576523"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>Azure Active Directory-hozzáférési panel üzembe helyezésének megtervezése

A Azure Active Directory hozzáférési panel egy webalapú portál, amely lehetővé teszi a támogatási költségek csökkentését, a termelékenység és a biztonság növelését, valamint a felhasználói frusztráció csökkentését. A rendszer részletes jelentéseket tartalmaz, amelyek nyomon követik, hogy a felhasználók Mikor férhetnek hozzá a rendszerhez, és értesítik a rendszergazdákat a visszaélésekről

A Azure Active Directory hozzáférési panel lehetővé teszi a felhasználók számára a következőket:

* Felderítheti és elérheti vállalata Azure Active Directory kapcsolódó erőforrásait, például az alkalmazásokat.
* Hozzáférés kérése új alkalmazásokhoz és csoportokhoz, illetve az ezekhez az erőforrásokhoz való hozzáférés kezelése mások számára.
* Az önkiszolgáló jelszó-visszaállítási és a többtényezős hitelesítési beállítások kezelése.
* Az eszközök kezelése.

Emellett a rendszergazdák számára is lehetővé teszi a felügyeletet:

* Szolgáltatási feltételek
* Munkahelyek
* Hozzáférési felülvizsgálatok


## <a name="benefits-of-azure-active-directory-access-panel-integration"></a>A Azure Active Directory Access panel integrációjának előnyei

A Microsoft Azure Active Directory (Azure AD) hozzáférési panelje a következő módokon nyújt előnyöket a vállalatoknak:

**Intuitív felhasználói élményt nyújt**: A hozzáférési panel engedélyezése lehetővé teszi a felhasználók számára, hogy egyetlen indítási pad legyenek az összes Azure-beli egyszeri bejelentkezéshez kapcsolódó alkalmazáshoz. Az olyan képességek, mint a csoportok kezelése és az önkiszolgáló jelszó-visszaállítás, a felhasználók továbbra is rendelkeznek egy egyesített portálral, hogy megtalálják ezeket a beállításokat. Az intuitív élmény lehetővé teszi a felhasználók számára, hogy gyorsabban és hatékonyabban működjenek, miközben csökkentik a frusztrációt.

**Növeli**a termelékenységet: A hozzáférési panelen lévő összes felhasználói alkalmazásnál engedélyezve van az egyszeri bejelentkezés (SSO). A vállalati alkalmazások és az Office 365 egyszeri bejelentkezésének engedélyezése kiváló bejelentkezési élményt biztosít a meglévő felhasználók számára, így csökkenthető vagy kiküszöbölhető a további bejelentkezési kérések. A hozzáférési panel lehetővé teszi az önkiszolgáló és a dinamikus tagságot, és javítja az identitásrendszer általános biztonságát azáltal, hogy a megfelelő személyek kezelik az alkalmazásokhoz való hozzáférést. A hozzáférési panel egy koherens kezdőlapként szolgál a felhasználók számára az erőforrások gyors megtalálásához és a munkahelyi feladatok folytatásához.

A költségeket a következőket **kezeli**: A hozzáférési panel Azure Active Directory való engedélyezése lehetővé teheti a helyszíni infrastruktúrák kiválasztását. Ez csökkenti a támogatási költségeket azáltal, hogy lehetővé teszi, hogy a felhasználók konzisztens portálon megtalálják az összes alkalmazást, hozzáférést kérjenek az erőforrásokhoz, és kezeljék a fiókját.

**Növeli a rugalmasságot és a biztonságot**: A hozzáférési panel lehetővé teszi a felhőalapú platform által biztosított biztonság és rugalmasság elérését. A rendszergazdák egyszerűen módosíthatják az alkalmazásokra és az erőforrásokra vonatkozó beállításokat, és az új biztonsági követelményekkel rendelkeznek anélkül, hogy befolyásolnák a felhasználókat.

**Robusztus naplózási és használati követést tesz lehetővé**: Az összes végfelhasználói funkció naplózása és használatának nyomon követése lehetővé teszi, hogy a felhasználók az erőforrásaikat használják, és lehetővé teszi a biztonság értékelését.

### <a name="licensing-considerations"></a>Licencelési megfontolások

A hozzáférési panel ingyenes, és nem igényel licenceket egy alapszintű minden felhasználó számára. A címtárban lévő objektumok száma és a telepíteni kívánt funkciók azonban további licenceket is igényelhetnek. Az általános Azure AD-forgatókönyvek az alábbi, licencelési követelményekkel rendelkező biztonsági szolgáltatásokat tartalmazzák.

* [Az Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [Csoport alapú tagság](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [Önkiszolgáló jelszó-követelmény](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

Tekintse [meg a Azure Active Directory teljes licencelési útmutatóját](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>Az Azure AD hozzáférési panel üzembe helyezésének előfeltételei

A projekt megkezdése előtt a következő előfeltételeket kell végrehajtania.

* [Application SSO-integráció](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Azure AD-beli felhasználói és csoportos infrastruktúra](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>Az Azure AD hozzáférési panelének megtervezése

A következő táblázat a hozzáférési panel központi telepítésének legfontosabb használati eseteit ismerteti:

| Terület| Leírás |
| - | - |
| Access| A hozzáférési panel portál a vállalati hálózaton belüli vállalati és személyes eszközökről érhető el. |
|Access | A hozzáférési panel portál a vállalati hálózaton kívüli vállalati eszközök számára is elérhető. |
| Naplózás| A használati adatok legalább 29 naponta letöltődnek a vállalati rendszerbe. |
| Cégirányítás| Az Azure AD-hez csatlakoztatott alkalmazások és csoportok felhasználói hozzárendeléseinek életciklusa definiálva és figyelve van. |
| Biztonság| Az erőforrásokhoz való hozzáférést felhasználói és csoportos hozzárendelések segítségével szabályozhatja. Csak a jogosult felhasználók kezelhetik az erőforrás-hozzáférést. |
| Teljesítmény| A hozzáférési hozzárendelések terjesztési ütemterveit dokumentálják és figyelik. |
| Felhasználói élmény| A felhasználók tisztában vannak a hozzáférési panel képességeivel és azok használatának módjával.|
| Felhasználói élmény| A felhasználók saját maguk is kezelhetik az alkalmazásokhoz és csoportokhoz való hozzáférésüket.|
| Felhasználói élmény| A felhasználók kezelhetik a fiókjaikat. |
| Felhasználói élmény| A felhasználók tisztában vannak a böngészők kompatibilitásával. |
| Támogatás| A felhasználók a hozzáférési panelrel kapcsolatos problémákat is megtalálják. |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>Ajánlott eljárások az Azure AD hozzáférési paneljének üzembe helyezéséhez

A hozzáférési panel működésének fokozatos engedélyezése is lehetséges. A következő üzembe helyezési sorrendet javasoljuk:

1. Saját alkalmazások
   * App Launcher
   * Önkiszolgáló alkalmazások kezelése
   * Microsoft Office 365 integráció

1. Önkiszolgáló alkalmazások felderítése
   * Új jelszó önkiszolgáló kérése
   * Multi-Factor Authentication-beállítások
   * Eszközkezelés
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
| A kísérleti csoport (ok) meghatározása| Azonosítsa a használni kívánt Azure AD biztonsági csoportot, és győződjön meg arról, hogy az összes kísérleti tag a csoport részét képezi. |
| Határozza meg az éles környezetben engedélyezni kívánt csoportokat vagy csoportokat.| Azonosítsa az Azure ad-vel szinkronizált Azure AD biztonsági csoport (ok) vagy AD-csoportok használatát. Győződjön meg arról, hogy az összes kísérleti tag a csoport részét képezi. |
| Az egyszeri bejelentkezés használatának engedélyezése a felhasználók számára az alkalmazások típusaihoz| Összevont egyszeri bejelentkezés, OAuth, jelszó egyszeri bejelentkezés, alkalmazásproxy |
| Önkiszolgáló jelszó-visszaállítás használatának engedélyezése a felhasználók számára| Igen |
| Multi-Factor Authentication használatának engedélyezése a felhasználók számára| Igen |
| Az önkiszolgáló csoportok kezelésének engedélyezése a felhasználók számára, hogy milyen típusú csoportokat használ| Biztonsági csoportok, O365-csoportok |
| Az önkiszolgáló alkalmazások felügyeletének engedélyezése a felhasználók számára| Igen |
| Hozzáférési felülvizsgálatok használatának engedélyezése a felhasználók számára| Igen |

### <a name="plan-consent-strategy"></a>A beleegyező stratégia megtervezése

A felhasználóknak vagy a rendszergazdáknak hozzá kell járulniuk bármely alkalmazás használati feltételeihez és adatvédelmi szabályzatához. Ha lehetséges, az üzleti szabályok alapján javasoljuk a rendszergazdai beleszólást, amely jobb felhasználói élményt nyújt a felhasználóknak.

A rendszergazdai engedély használatához a bérlő globális rendszergazdájának kell lennie, és az alkalmazásoknak a következőket kell tartalmazniuk:

* Regisztrálva van a bérlőben, vagy

* Regisztrálva van egy másik Azure AD-bérlőben, és korábban legalább egy végfelhasználó beleegyezett.

További információ: [a végfelhasználók beleegyezett az alkalmazásba Azure Active Directory](configure-user-consent.md).

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

Ha a technológiai projektek sikertelenek, általában azért teszik ezt meg, mert a hatás, az eredmények és a felelősségek nem egyeznek. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md) , és hogy a projektben szereplő, az érintett felek szerepkörei jól megértettek.

### <a name="plan-communications"></a>Kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon tájékoztathatja a felhasználókat arról, hogy a felhasználói élmény mikor és mikor változik, és hogyan szerezhet támogatást, ha szükséges.

Habár a hozzáférési panel általában nem hoz létre felhasználói problémákat, fontos, hogy felkészüljön. Hozzon létre útmutatókat és a támogatási személyzet összes erőforrását az elindítása előtt.

#### <a name="communications-templates"></a>Kommunikációs sablonok

A Microsoft testreszabható sablonokat biztosít a hozzáférési panel [e-mailekhez és egyéb kommunikációhoz](https://aka.ms/APTemplates) . Ezeket az eszközöket más kommunikációs csatornákon is használhatja a vállalati kulturális környezetnek megfelelően.

## <a name="plan-your-sso-configuration"></a>Az egyszeri bejelentkezés konfigurációjának megtervezése

Amikor egy felhasználó bejelentkezik egy alkalmazásba, egy hitelesítési folyamaton keresztül történik, és bizonyítania kell, hogy kik azok. Egyszeri bejelentkezés nélkül az alkalmazásban tárolt jelszó, és a felhasználónak ismernie kell ezt a jelszót. Az egyszeri bejelentkezés (SSO) felhasználói hitelesítő adatai átkerülnek az alkalmazásba, így nem kell újból megadniuk a jelszavakat az egyes alkalmazásokhoz.

Ha alkalmazásokat szeretne elindítani az alkalmazásokban, engedélyezni kell az egyszeri bejelentkezést (SSO) az alkalmazásokhoz.

Az Azure AD három különböző módon teszi lehetővé az [egyszeri bejelentkezést az alkalmazásokba](what-is-single-sign-on.md):

* **Összevont egyszeri bejelentkezés** 
    * Lehetővé teszi az alkalmazások számára, hogy jelszó kérése helyett felhasználói hitelesítésre átirányítsák az Azure AD-t. 
    * Támogatott olyan alkalmazások esetében, mint például az SAML 2,0, a WS-Federation vagy az OpenID Connect, és az egyszeri bejelentkezés legszélesebb módja.

* **Jelszó alapú egyszeri bejelentkezés** 
    * Lehetővé teszi a biztonságos alkalmazások jelszavainak tárolását és visszajátszását webböngészős bővítmény vagy mobil alkalmazás használatával. 
    * Kihasználja az alkalmazás által biztosított meglévő bejelentkezési folyamatot, de lehetővé teszi a rendszergazdák számára a jelszavak kezelését. A felhasználónak nem kell tudnia a jelszót.

* **Meglévő egyszeri bejelentkezés** 
    * Lehetővé teszi az Azure AD számára az alkalmazáshoz konfigurált meglévő egyszeri bejelentkezés kihasználása.
    * Lehetővé teszi, hogy ezek az alkalmazások az Office 365 vagy az Azure AD hozzáférési panel portálokhoz legyenek csatolva. 
    * További jelentéskészítés engedélyezése az Azure AD-ben az alkalmazások indításakor. 
    * Az Azure alkalmazásproxy és a csatolt egyszeri bejelentkezési mód használatát foglalja magában.

Itt megtudhatja, hogyan konfigurálhat egy alkalmazás egyszeri bejelentkezéses módját: [Egyszeri bejelentkezés az Azure Active Directory lévő alkalmazásokba](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

A saját alkalmazások oldalával kapcsolatos legjobb megoldásként javasoljuk, hogy kezdje az összevont egyszeri bejelentkezéshez elérhető felhőalapú alkalmazások integrálásával. Az összevont egyszeri bejelentkezés lehetővé teszi a felhasználók számára, hogy egységes, egy kattintással elérhető felületet nyújtsanak az alkalmazásban a felületek elindítása előtt, és inkább robusztusak legyenek a konfigurációban.

Használjon összevont egyszeri bejelentkezést az Azure AD-vel (OpenID Connect/SAML), ha egy alkalmazás támogatja azt a jelszó-alapú egyszeri bejelentkezés és az ADFS helyett.

Az SaaS-alkalmazások üzembe helyezésével és konfigurálásával kapcsolatos további információkért tekintse meg a [SaaS SSO telepítési tervét](https://aka.ms/deploymentplans/sso).

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>A My apps Browser bővítmény üzembe helyezésének megtervezése

Ha a végfelhasználók jelszó-alapú egyszeri bejelentkezést igénylő alkalmazásokat tesznek elérhetővé, a bejelentkezéshez telepíteniük kell a saját alkalmazások biztonságos bejelentkezési bővítményét. A bővítmény végrehajt egy parancsfájlt, amely továbbítja a jelszót az alkalmazás bejelentkezési űrlapjára. A rendszer felkéri a felhasználókat, hogy telepítse a bővítményt, amikor először elindítja a jelszó-alapú SSO-alkalmazást. A bővítménysel kapcsolatos további információkért tekintse meg a [hozzáférési panel böngésző bővítményének telepítését](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)ismertető dokumentációt.

Ha a jelszó-alapú SSO-alkalmazásokat integrálni kell, akkor meg kell határoznia azt a mechanizmust, amellyel a bővítményt a [támogatott böngészőkkel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)nagy méretben helyezheti üzembe. A lehetőségek a következők:

* [Csoportházirend az Internet Explorerben](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Az Internet Explorer System Center Configuration Manager (SCCM)](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [A Chrome, a Firefox, a Microsoft Edge vagy az IE felhasználó által vezérelt letöltése és konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

További információ: A [jelszó egyszeri bejelentkezésének konfigurálása](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

A jelszó-alapú SSO-alkalmazásokat nem használó felhasználók a bővítmény előnyeit is kihasználhatják. Ezek az előnyök többek között lehetővé teszi bármely alkalmazás indítását a keresősáv használatával, a legutóbb használt alkalmazások elérésének megkeresését, valamint a saját alkalmazások lapra mutató hivatkozást.

A felhasználó a jelszó-alapú egyszeri bejelentkezési alkalmazás első indításakor a következőt fogja látni:

![Képernyőkép az alkalmazások böngésző bővítményének telepítési képernyőjéről ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>Mobil hozzáférés tervezése

Az Intune-szabályzattal (Microsoft Edge vagy Intune Managed Browser) védett böngésző szükséges a jelszó-alapú egyszeri bejelentkezést használó mobil felhasználók számára. A szabályzattal védett böngészők lehetővé teszik az alkalmazás számára mentett jelszó átvitelét. A Microsoft Edge vagy a Managed Browser webes adatvédelmi funkciókat biztosít. Az iOS-és Android-eszközökön használhatja a Microsoft Edge vállalati forgatókönyveket is. A Microsoft Edge ugyanolyan felügyeleti forgatókönyveket támogat, mint a Intune Managed Browser, és javítja a végfelhasználói élményt. További információ: [Webes hozzáférés kezelése Microsoft Intune szabályzattal védett böngésző használatával](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>Saját alkalmazások üzembe helyezésének megtervezése

A hozzáférési panel alapja az Application Launcher saját alkalmazások, amelyekhez a felhasználók férhetnek hozzá [https://myapps.microsoft.com](https://myapps.microsoft.com/). A saját alkalmazások oldalain egyetlen helyet biztosítanak a felhasználóknak a munkájuk elindításához és a szükséges alkalmazások eléréséhez. Itt a felhasználók az összes olyan alkalmazás listáját megtalálják, amelyhez egyszeri bejelentkezés hozzáférése van. 

![Az alkalmazások panel képernyőképe](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

Ugyanazok az alkalmazások jelennek meg az Office 365 app launcherben, amikor a felhasználók az Office 365 portált használják.

Tervezze meg, hogy milyen sorrendben vegyen fel alkalmazásokat a saját alkalmazások indítóba, és hogy fokozatosan vagy egyszerre legyenek-e kivezetve. Ehhez létre kell hoznia egy alkalmazás-leltárt, amely felsorolja a hitelesítés típusát és az egyes alkalmazások meglévő egyszeri bejelentkezéses (SSO) integrációját.

#### <a name="add-applications-to-the-my-apps-panel"></a>Alkalmazások hozzáadása a saját alkalmazások panelhez

Minden Azure AD SSO-kompatibilis alkalmazást hozzáadhat a saját alkalmazások indítóhoz. A csatolt egyszeri bejelentkezés lehetőséggel további alkalmazások is hozzáadódnak a szolgáltatáshoz. Beállíthat egy olyan alkalmazás-csempét, amely a meglévő webalkalmazás URL-címére hivatkozik. A csatolt egyszeri bejelentkezés lehetővé teszi, hogy a felhasználókat a saját alkalmazások portálra irányítsa anélkül, hogy az összes alkalmazást át kellene telepítenie Azure Active Directory egyszeri bejelentkezésre. Fokozatosan áthelyezheti az Azure AD SSO-ra konfigurált alkalmazásokat a felhasználói élmény megszakítása nélkül.

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>Saját alkalmazások vagy meglévő portál használatának megtervezése

Előfordulhat, hogy a felhasználók már rendelkeznek a saját alkalmazásaitól eltérő alkalmazással vagy portálral. Ha igen, döntse el, hogy mindkét portált támogatni kívánja-e, vagy csak egyet fog használni.

Ha egy meglévő portált már használ kiindulási pontként a felhasználók számára, a "felhasználói hozzáférési URL-címek" használatával integrálhatja az alkalmazások funkcióit. A felhasználói hozzáférési URL-címek a saját alkalmazások portálon elérhető alkalmazásokra mutató közvetlen hivatkozásként működnek. Ezek az URL-címek beágyazható bármely meglévő webhelyre. Amikor a felhasználó a hivatkozásra kattint, elindítja az alkalmazást a saját alkalmazások portálján.

A felhasználói hozzáférés URL-tulajdonsága az alkalmazás tulajdonságok területén található a Azure Portalban.

![Az alkalmazások panel képernyőképe](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>Önkiszolgáló alkalmazások felderítésének és hozzáférésének megtervezése

Miután telepítette az alkalmazások alapkészletét egy felhasználó saját alkalmazások lapján, javasoljuk, hogy engedélyezze az önkiszolgáló alkalmazás-felügyeleti funkciókat. Az önkiszolgáló alkalmazások felderítése a következőket teszi lehetővé:
* A felhasználók megkereshetik a saját alkalmazásaikban felvehető új alkalmazásokat. 
* A felhasználók hozzáadhatnak opcionális alkalmazásokat, amelyeket a telepítés során esetleg nem igényelnek.

A jóváhagyási munkafolyamatok az alkalmazások eléréséhez használhatók explicit jóváhagyásra. A jóváhagyó felhasználók a saját alkalmazások portálon kapnak értesítéseket, amikor az alkalmazáshoz való hozzáférésre vonatkozó kérelem van folyamatban.

## <a name="plan-self-service-group-membership"></a>Önkiszolgáló csoporttagság megtervezése 

Lehetővé teheti a felhasználók számára, hogy saját biztonsági csoportokat vagy Office 365-csoportokat hozzanak létre és kezeljenek Azure Active Directoryban (Azure AD). A csoport tulajdonosa jóváhagyhatja vagy megtagadhatja a tagsági kérelmeket, és delegálhatja a csoporttagság vezérlését. Az önkiszolgáló csoport felügyeleti funkciói nem érhetők el levelezési biztonsági csoportokhoz vagy terjesztési listához.

Az önkiszolgáló csoporttagság megtervezéséhez döntse el, hogy engedélyezi-e a szervezet összes felhasználója számára a csoportok létrehozását és kezelését, vagy csak a felhasználók egy részhalmazát. Ha a felhasználók egy részhalmazát szeretné használni, be kell állítania egy csoportot, amelyhez hozzá kívánja adni a felhasználókat. A forgatókönyvek engedélyezésével kapcsolatos további információkért lásd: [az önkiszolgáló csoport kezelésének beállítása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management) .

## <a name="plan-reporting-and-auditing"></a>Jelentéskészítés és naplózás tervezése

Az Azure AD olyan [jelentéseket biztosít, amelyek technikai és üzleti ismereteket kínálnak](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). Az üzleti és a műszaki alkalmazások tulajdonosai együttműködve vehetik igénybe ezeket a jelentéseket rendszeresen. Az alábbi táblázat néhány példát mutat be a tipikus jelentéskészítési forgatókönyvekre.

|   | A kockázatok kezelése| Termelékenység növelése| Cégirányítás és megfelelőség |
|  - |- | - | - |
| Jelentéstípusok|  Alkalmazás engedélyei és használata.| Fiók létesítési tevékenysége| Tekintse át, ki fér hozzá az alkalmazásokhoz |
| Lehetséges műveletek| Hozzáférés naplózása; engedélyek visszavonása| Kiépítési hibák szervizelése| Hozzáférés visszavonása |

Az Azure AD a legtöbb naplózási adattal 30 napig tart. Az adatai az Azure felügyeleti portálon vagy API-n keresztül érhetők el az elemzési rendszerbe való letöltéshez.

#### <a name="auditing"></a>Naplózás

Az alkalmazás-hozzáférés naplófájljai 30 napig érhetők el. Ha a vállalaton belül a biztonsági naplózás nagyobb adatmegőrzést igényel, a naplókat egy biztonsági információs esemény és felügyelet (SIEM) eszközbe kell exportálni, például splunk vagy ArcSight.

A naplózási, jelentéskészítési és vész-helyreállítási biztonsági másolatok esetében dokumentálja a letöltés szükséges gyakoriságát, a célként megadott rendszerét, és felelős az egyes biztonsági másolatok kezeléséért. Előfordulhat, hogy nincs szükség külön naplózási és jelentéskészítési biztonsági mentésre. A vész-helyreállítási biztonsági mentésnek különálló entitásnak kell lennie.

## <a name="deploy-applications-to-users-my-apps-panel"></a>Alkalmazások üzembe helyezése a felhasználók saját alkalmazások paneljén

Miután az alkalmazás be lett állítva az SSO-ra, a csoportok hozzá vannak rendelve. A hozzárendelt csoportokban lévő felhasználók hozzáférhetnek majd az alkalmazáshoz, és az Office 365 app launcherben jelennek meg.

Lásd: [felhasználók és csoportok társítása egy alkalmazáshoz Active Directory-ben](methods-for-assigning-users-and-groups.md).

Ha tesztelés vagy üzembe helyezés során szeretné felvenni a csoportokat, de még nem teszi lehetővé az alkalmazások használatát a saját alkalmazásokban, tekintse [meg az alkalmazás elrejtése a felhasználói felületről Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)című témakört.

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>Microsoft Office 365-alkalmazások üzembe helyezése az alkalmazásokban

Office 365-alkalmazások esetén a felhasználók a hozzájuk rendelt licencek alapján kapják meg az Office-példányt. Az Office-alkalmazásokhoz való hozzáférés előfeltétele, hogy a felhasználók az Office-alkalmazásokhoz kötött megfelelő licencekhez rendeljenek felhasználókat. Ha hozzárendel egy felhasználót a licenchez, a rendszer automatikusan megtekinti a licenccel társított alkalmazásokat a saját alkalmazások lapján és a O365 alkalmazás-Indítóban.

Ha az Office-alkalmazások egy készletét szeretné elrejteni a felhasználóktól, lehetőség van az alkalmazások elrejtésére a saját alkalmazások portálján, miközben továbbra is engedélyezheti a hozzáférést a O365-portálon. Ezeket a beállításokat az alkalmazás felhasználói beállítások részében találja meg. További információ: [Alkalmazás elrejtése a felhasználói felületről Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

![Képernyőkép az alkalmazások elrejtésének konfigurálásáról](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>Alkalmazás önkiszolgáló képességeinek üzembe helyezése

Az önkiszolgáló alkalmazás-hozzáférés lehetővé teszi a felhasználók számára, hogy saját felfedezzék és hozzáférést kérjenek bármely alkalmazáshoz. A felhasználók a hozzáférés kérése nélkül kapnak hozzáférést a szükséges alkalmazásokhoz anélkül, hogy az IT-csoporton keresztül kellene elvégezniük a hozzáférést. Amikor egy felhasználó hozzáférést kér, és az alkalmazás tulajdonosa automatikusan vagy manuálisan jóváhagyja őket, a rendszer hozzáadja őket a háttérbeli csoporthoz. A jelentéskészítés engedélyezve van a felhasználó által kért, jóváhagyott vagy eltávolított hozzáférésre, és lehetővé teszi a hozzárendelt szerepkörök felügyeletét.

Delegálhatja az alkalmazás hozzáférési kéréseinek jóváhagyását az üzleti jóváhagyóknak. Az üzleti jóváhagyó az alkalmazás-hozzáférési jelszavakat közvetlenül az üzleti jóváhagyó saját alkalmazások oldaláról állíthatja be.

További információ: [Önkiszolgáló alkalmazás-hozzáférés használata](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![Képernyőkép az önkiszolgáló alkalmazások kezelésének konfigurálásáról](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>Az üzemelő példány ellenőrzése

Győződjön meg arról, hogy a hozzáférési panel üzembe helyezése alaposan ki van tesztelve, és a visszaállítási terv van érvényben.

A következő teszteket a vállalati tulajdonú eszközökkel és a személyes eszközökkel kell elvégezni. Ezeknek a tesztelési eseteknek az üzleti használati eseteit is tükröznie kell. A következő néhány eset a jelen dokumentumban szereplő üzleti követelményeken és a tipikus technikai forgatókönyveken alapul. Adja hozzá az igényeinek megfelelő egyéb szolgáltatásokat.

#### <a name="application-sso-access-test-case-examples"></a>Alkalmazás egyszeri bejelentkezéses hozzáférésének tesztelésére vonatkozó példák:


| Üzleti eset| Várt eredmény |
| - | -|
| A felhasználó bejelentkezik a saját alkalmazások portálra| A felhasználó bejelentkezhet, és megtekintheti az alkalmazásaikat |
| A felhasználó egy összevont SSO-alkalmazást indít el| A felhasználó automatikusan bejelentkezett az alkalmazásba |
| A felhasználó első alkalommal indítja el a jelszó-SSO-alkalmazást| A felhasználónak telepítenie kell a saját alkalmazások bővítményt |
| A felhasználó a következő alkalommal indít el egy jelszó-SSO-alkalmazást| A felhasználó automatikusan bejelentkezett az alkalmazásba |
| A felhasználó elindítja az alkalmazást a O365-portálról| A felhasználó automatikusan bejelentkezett az alkalmazásba |
| A felhasználó elindít egy alkalmazást a Managed Browser| A felhasználó automatikusan bejelentkezett az alkalmazásba |


#### <a name="application-self-service-capabilities-test-case-examples"></a>Alkalmazások önkiszolgáló képességei – tesztelési példák


| Üzleti eset| Várt eredmény |
| - | - |
| A felhasználó kezelheti a tagságot az alkalmazásban| A felhasználó hozzáadhat vagy eltávolíthat olyan tagokat, akik hozzáféréssel rendelkeznek az alkalmazáshoz |
| A felhasználó szerkesztheti az alkalmazást| A felhasználó szerkesztheti az alkalmazás leírását és a jelszó egyszeri bejelentkezéses alkalmazásaihoz tartozó hitelesítő adatokat. |

### <a name="rollback-steps"></a>Visszaállítási lépések

Fontos megtervezni, hogy mi a teendő abban az esetben, ha az üzemelő példány nem tervezett. Ha az egyszeri bejelentkezés konfigurálása nem sikerül az üzembe helyezés során, meg kell ismernie az [egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) hibáinak elhárítását és a felhasználókra gyakorolt hatás csökkentését. Szélsőséges körülmények között előfordulhat, hogy vissza kell állítania az [egyszeri bejelentkezést](../manage-apps/plan-sso-deployment.md).


## <a name="manage-your-implementation"></a>A megvalósítás kezelése

A Microsoft azt javasolja, hogy a legkevésbé Kiemelt szerepkört használja a szükséges feladatok végrehajtásához Azure Active Directoryon belül. A Microsoft azt javasolja, [hogy tekintse át az elérhető különböző szerepköröket](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) , és válasszon egyet az alkalmazáshoz tartozó personák igényeinek megoldásához. Előfordulhat, hogy bizonyos szerepköröket átmenetileg kell alkalmazni, és el kell távolítani az üzembe helyezés befejeződése után.

| Szerepek| Szerepkörök| Azure AD-szerepkör  |
| - | -| -|
| Ügyfélszolgálati rendszergazda| 1\. szintű támogatás| Nincsenek |
| Identity admin| Konfigurálás és hibakeresés az Azure AD-t érintő problémák esetén| Globális rendszergazda |
| Alkalmazás-rendszergazda| Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja| Nincsenek |
| Infrastruktúra-rendszergazdák| Tanúsítvány-átváltási tulajdonos| Globális rendszergazda |
| Üzleti tulajdonos/érintett fél| Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja| Nincsenek |

Javasoljuk, hogy az [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) használatával kezelje a szerepköröket, hogy további naplózási, vezérlési és hozzáférési felülvizsgálatot biztosítson a címtár-jogosultságokkal rendelkező felhasználók számára.

### <a name="troubleshoot-access-panel-issues"></a>Hozzáférési panel problémáinak elhárítása

Hibaelhárítási útmutatók hozhatók létre a támogatási szervezet számára a gyakori forgatókönyvekkel, és a Microsoft dokumentációra mutatnak a állásfoglalásaira. Érdemes lehet olyan útmutatók létrehozására, amelyek a szervezet által használt rétegekbe bontják a támogatást.

A következő hibaelhárítási útmutatókból tájékozódhat:

[Nem megjelenő alkalmazások](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[Váratlan alkalmazások jelennek meg](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[A felhasználó nem tud bejelentkezni a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[Az önkiszolgáló alkalmazás-hozzáférés használatával kapcsolatos problémák](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[A böngésző kiterjesztésével kapcsolatos problémák](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>További lépések

[Azure Active Directory többtényezős hitelesítés központi telepítésének megtervezése](https://aka.ms/deploymentplans/mfa)
