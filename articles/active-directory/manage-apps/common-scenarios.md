---
title: Az Azure Active Directory gyakori alkalmazáskezelési forgatókönyvei | Microsoft dokumentumok
description: Az alkalmazások kezelésének központosítása az Azure AD-vel
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ff820470765b82e397e56a2458603b8e5a7c7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657948"
---
# <a name="centralize-application-management-with-azure-ad"></a>Az alkalmazások kezelésének központosítása az Azure AD-vel

Jelszavak, mind egy informatikai rémálom, mind a fájdalom az alkalmazottak számára szerte a világon. Ez az oka annak, hogy egyre több vállalat fordul az Azure Active Directoryhoz, a Microsoft identitás- és hozzáférés-kezelési megoldásához a felhőhöz és az összes többi erőforráshoz. Ugrás alkalmazásról alkalmazásra anélkül, hogy adja meg a jelszót mindegyik. Ugrás az Outlook, a Workday, a ADP, amilyen gyorsan meg tudja nyitni őket, gyorsan és biztonságosan. Ezután működjön együtt a partnerekkel, és még a szervezeten kívüli másokkal is, anélkül, hogy meg kellene hívnia az IT-t. Mi több, az Azure AD segít kezelni a kockázatot azáltal, hogy biztosítja a többtényezős hitelesítéssel használt alkalmazásokat, hogy ellenőrizze, ki ön, folyamatosan adaptív gépi tanulás és a biztonsági intelligencia használatával a gyanús bejelentkezések észleléséhez, így biztonságos hozzáférést biztosít a szükséges alkalmazásokhoz, bárhol is van. Ez nem csak a felhasználók számára, hanem az IT is. A just-in-time hozzáférési felülvizsgálatok és a teljes körű cégirányítási csomag, az Azure AD segít a megfelelőség iés kényszerítése szabályzatok is. És ezt kap, akkor is automatizálhatja kiépítése felhasználói fiókok, így a hozzáférés-kezelés a szél. Tekintse meg azokat a gyakori forgatókönyveket, amelyekhez az ügyfél az Azure Active Directory alkalmazáskezelési képességeit használja.

**Gyakori forgatókönyvek**


> [!div class="checklist"]
> * SSO az összes alkalmazáshoz
> * A kiépítés és a megszüntetés automatizálása 
> * Az alkalmazások védelme
> * Az alkalmazásokhoz való hozzáférés szabályozása
> * Hibrid biztonságos hozzáférés

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>1. forgatókönyv: Az sso beállítása az összes alkalmazáshoz

Nincs több jelszókezelés. A vállalati hitelesítő adatokkal biztonságosan elérheti az összes szükséges erőforrást. 

|Szolgáltatás  | Leírás | Ajánlás |
|---------|---------|---------|
|SSO|Szabványokon alapuló, összevont egyszeri szolgáltatások megbízható iparági szabványokat alkalmazva.|Mindig [saml / OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) használatával engedélyezze az SSO-t, ha az alkalmazás támogatja azt.|
|Hozzáférési panel|A felhasználók számára egyszerű központot kínálhat az összes alkalmazás felfedezéséhez és eléréséhez. Lehetővé teszi számukra, hogy hatékonyabban önkiszolgáló funkciókkal, például az alkalmazásokhoz és csoportokhoz való hozzáférés kérésével, vagy az erőforrásokhoz való hozzáférés más nevében történő kezelésével hatékonyabbak legyenek.| Üzembe helyezheti a [hozzáférési panelt](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) a szervezetben, miután integrálta az alkalmazásokat az Azure AD egyszeri bejelentkezéssel.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>2. forgatókönyv: A kiépítés és a megszüntetés automatizálása 


A legtöbb alkalmazás megköveteli, hogy a felhasználó ki kell építeni az alkalmazásba, mielőtt elérne a szükséges erőforrásokat. A CSV-fájlok vagy összetett parancsfájlok használata költséges és nehezen kezelhető. Továbbá az ügyfeleknek gondoskodniuk kell arról, hogy a fiókokat eltávolítsák, ha valakinek már nem kellene hozzáférnie. Használja ki az alábbi eszközöket a kiépítés és a megszüntetés automatizálásához. 


|Szolgáltatás  |Leírás|Ajánlás |
|---------|---------|---------|
|SciM-kiépítés|[SciM](https://aka.ms/SICMOverview) egy iparági bevált gyakorlat a felhasználói kiépítés automatizálására. Bármely SCIM-kompatibilis alkalmazás integrálható az Azure AD-vel. A felhasználói fiókok automatikus létrehozása, frissítése és törlése CSV-fájlok, egyéni parancsfájlok vagy helyszíni megoldások karbantartása nélkül.|Tekintse meg az [előintegrált](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) alkalmazások egyre növekvő listáját az Azure AD alkalmazásgalériában|
|Microsoft Graph|Használja ki az Azure AD által az alkalmazás nak a szükséges adatokkal gazdagító adatok at és az adatok kilélegzését és mélységét.|Használja ki a [Microsoft graph-ot,](https://developer.microsoft.com/graph/) hogy adatokat szerezzen be a Microsoft ökoszisztémájából. |


## <a name="scenario-3-secure-your-applications"></a>3. forgatókönyv: Az alkalmazások biztonságossá tétele
A személyazonosság a biztonság kapocsa. Ha egy identitás veszélybe kerül, hihetetlenül nehéz megállítani a dominó hatást, mielőtt túl késő lenne. Átlagosan több mint 100 nap telik el, mielőtt a szervezetek rájönnek, hogy volt egy kompromisszum. Az Azure AD által biztosított eszközök kelti az alkalmazások biztonsági állapotának javítása érdekében. 

|Szolgáltatás  |Leírás| Ajánlás |
|---------|---------| ---------|
|Azure MFA|Az Azure Multi-Factor Authentication (MFA) a Microsoft kétlépéses hitelesítési megoldása. A rendszergazda által jóváhagyott hitelesítési módszerek használatával az Azure MFA segít megőrizni az adatokhoz és alkalmazásokhoz való hozzáférést, miközben kielégíti az egyszerű bejelentkezési folyamat iránti igényt.| [Engedélyezze](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) az MFA-t a felhasználók számára.  |
|Feltételes hozzáférés|A Feltételes hozzáférés segítségével automatizált hozzáférés-vezérlési döntéseket hozhat arra vonatkozóan, hogy ki férhet hozzá a felhőalapú alkalmazásokhoz a feltételek alapján.| Tekintse át a [biztonsági alapértelmezett értékeket](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) és az ügyfelek által használt [általános házirendeket.](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) | 
|Identity Protection|Az Identity Protection azAzure AD-vel, a Microsoft-fiókkal rendelkező fogyasztói térrel és az Xbox-szal való játékokkal kapcsolatos tanulási lehetőségeket használja a Microsoft által a szervezetben szerzett adatokból, hogy megvédje a felhasználókat. A Microsoft naponta 6,5 billió jelet elemez, hogy azonosítsa és megvédje az ügyfeleket a fenyegetésektől.|Engedélyezze a szolgáltatásunk által biztosított [alapértelmezett identitásvédelmi szabályzatokat.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) | 

## <a name="scenario-4-govern-access-to-your-applications"></a>4. forgatókönyv: Az alkalmazásokhoz való hozzáférés szabályozása
Az identitás-szabályozás segítségével a szervezetek egyensúlyt teremtenek a termelékenység között – Milyen gyorsan férhet hozzá egy személy a szükséges alkalmazásokhoz, például amikor csatlakozik a szervezetemhez? És biztonság - Hogyan kell a hozzáférés idővel változik, például a változások miatt az adott személy foglalkoztatási státuszát? 

|Szolgáltatás  |Leírás|Ajánlás |
|---------|---------| ---------|
|Elm|Az Azure AD jogosultságkezelés segítségével a felhasználók a szervezeten belül és kívül is hatékonyabban kezelheti az alkalmazásokhoz való hozzáférést.| A nem rendszergazdák számára lehetővé teszik, hogy [hozzáférési csomagokkal](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)kezeljék az alkalmazásaikat.|
|Hozzáférési felülvizsgálatok|A felhasználók alkalmazásokhoz való hozzáférése rendszeresen ellenőrizhető, hogy csak a megfelelő személyek férhessenek hozzá.| Tekintse át a legérzékenyebb alkalmazásokhoz [való hozzáférést.](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) |
|Log Analytics|Jelentéseket készíthet arról, hogy ki fér hozzá az alkalmazásokhoz, és tárolja őket a választott SIEM eszközben, hogy korrelálja az adatokat az adatforrások között és az idő múlásával.| Engedélyezze [a naplóelemzést,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) és állítson be riasztásokat az alkalmazásokkal kapcsolatos kritikus eseményekhez. |


## <a name="scenario-5-hybrid-secure-access"></a>5. forgatókönyv: Hibrid biztonságos hozzáférés
Az identitás csak akkor lehet a vezérlősík, ha mindent össze tud kapcsolni a felhőbeli és helyszíni alkalmazásokközött. Használja ki az Azure AD és partnerei által biztosított eszközöket az örökölt hitelesítésalapú alkalmazásokhoz való biztonságos hozzáférés biztosításához.

|Szolgáltatás  |Leírás|Ajánlás |
|---------|---------|---------|
|Alkalmazásproxy|Napjainkban a munkavállalók a legkülönfélébb helyeken, időpontokban és eszközökön szeretnek dolgozni. SaaS-alkalmazások eléréséhez a felhőben és a vállalati alkalmazások a helyszínen. Az Azure AD alkalmazásproxy lehetővé teszi ezt a robusztus hozzáférést költséges és összetett virtuális magánhálózatok (VPN) vagy demilitarizált zónák (DMZs) nélkül.|Állítsa be a [távelérést](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) a helyszíni alkalmazásokhoz. |
|F5, Akamai, Zscaler|A meglévő hálózati és kézbesítési vezérlő használatával könnyedén megvédheti azokat az örökölt alkalmazásokat, amelyek még mindig kritikus fontosságúak az üzleti folyamatok szempontjából, de amelyeket az Azure AD-vel korábban nem tudott megvédeni. Valószínű, hogy már mindent megtud, amire szüksége van az alkalmazások védelméhez.| Akamai, Citrix, F5 vagy Zscaler használatával? Tekintse meg [előre elkészített megoldásainkat.](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access) | 

## <a name="related-articles"></a>Kapcsolódó cikkek

- [Alkalmazáskezelés](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Alkalmazások üzembe helyezése](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Hibrid biztonságos hozzáférés]()
- [Identitáskezelés](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft-identitásplatform](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Identitásbiztonság](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
