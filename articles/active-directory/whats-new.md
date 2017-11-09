---
title: "Újdonságok Azure Active Directory kibocsátási megjegyzései |} Microsoft Docs"
description: "Ismerje meg, mi az az Azure Active Directoryval (Azure AD) többek között a legújabb kibocsátási megjegyzései, ismert problémák, hibajavításokat tartalmaz, az elavult funkciók és a jövőbeni változtatásokról új."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 766ab10c853eb56a1203686d21d0e131c44a41b5
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Újdonságok az Azure Active Directoryban?




> Naprakész legyen what's new in Azure Active Directoryban ez feliratkozással [hírcsatorna](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us) az RSS-hírcsatorna olvasó meg kedvenc.



Azure Active Directory javítása azt vannak a folyamatos. Ahhoz, hogy naprakész legutóbbi fejlesztésekkel, ez a témakör adatokkal kapcsolatban:

-   A legfrissebb verziókban 
-   Ismert problémák 
-   Hibajavítások 
-   Elavult funkciók 
-   Módosítások tervek 

Írja le az ezen a lapon újra rendszeresen havonta is frissítjük azt.

## <a name="november-2017"></a>2017. november

**Típus:** elavult funkciók  
**Szolgáltatás kategória:** ACS  
**A termék képesség:** vezérlő szolgáltatás 

<a name="acs-retirement"></a>

A Microsoft Azure Active Directory hozzáférés-vezérlés (más néven a hozzáférés-vezérlési szolgáltatásban vagy az ACS) késői 2018 a használatból van.  További információkat, például egy részletes ütemezés és a magas szintű áttelepítési útmutató a következő néhány hét lesz megadva. Időközben hagyja megjegyzések ezen az oldalon az ACS kapcsolatos kérdéseivel, és a csapat tagjai lesznek elérhetők a megválaszolására.

---


## <a name="october-2017"></a>2017. október

**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** Reporting  
**A termék funkció:** Identitáskezelés életciklusa  


**Az Azure AD-jelentések (beta version) elavulttá API-k alapján a `https://graph.windows.net/<tenant-name>/reports/` csomópont**

Az Azure-portálon biztosítja:

- Egy új Azure Active Directory felügyeleti konzol 
- Új API-k, tevékenységek és biztonsági jelentések
 
Az új lehetőségekhez, a jelentés API-k miatt alatt a **/reports** végpont 2017. December 10. a rendszerből. 

---

**Típus:** rögzített   
**Szolgáltatás kategória:** alkalmazásaimat  
**A termék képesség:** egyszeri bejelentkezés  


Az Azure Active Directory egy HTML-felhasználónév és jelszó mező leképező alkalmazások automatikus bejelentkezési mező észlelését támogatja.  Ezeket a lépéseket ismertetett [automatikusan rögzítése az alkalmazás bejelentkezési mezők](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Ez a funkció található hozzáadásával egy *nem-gyűjtemény* alkalmazás a a **vállalati alkalmazások** lapját a [Azure-portálon](http://aad.portal.azure.com). Emellett konfigurálhatja a **egyszeri bejelentkezés** módra az új alkalmazást a **jelszó-alapú egyszeri bejelentkezést**, egy webes URL-cím megadása és a lap majd mentése közben.
 
Egy szolgáltatási probléma miatt ez a funkció ideiglenesen letiltotta egy ideig. A probléma megoldódott, és az automatikus bejelentkezési mező észlelési érhető el újra.

---

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** MFA  
**A termék képesség:** identitás biztonsági és védelmi  


A világ mindig a többtényezős hitelesítés (MFA) nagyon fontos részét képezik a szervezet védelme. A Microsoft identity csapata fejlődik a multi-factor Authentication hitelesítéssel való több adaptív hitelesítő adatokat és a felhasználói élmény zökkenőmentesebb. Ma rendben két fontos lépések az út az értesítés: 

- Integrálása a multi-factor Authentication ellenőrző eredmények közvetlenül az Azure AD bejelentkezési jelentés, beleértve az MFA eredmények programozott hozzáférést

- A core az Azure Active Directory beállítása az MFA-konfiguráció szorosabb integrációt tapasztalhat az Azure-portálon

Ebben az nyilvános előzetes MFA felügyeleti és jelentéskészítési az alapvető az Azure AD konfigurációs élményt, hogy lehetővé teszi a multi-factor Authentication kezelési portál funkcióihoz az Azure AD felhasználói élmény kezeléséhez integrált részét képezik.

További információkért lásd: [hivatkozás a multi-factor authentication jelentéskészítés az Azure-portálon](active-directory-reporting-activity-sign-ins-mfa.md) 


---
**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** irányítás  


**Az Azure AD használati feltételeket** információ van a végfelhasználók számára egyszerű módszert biztosít. Ez biztosítja, hogy a felhasználók látják a jogi vagy megfelelőségi követelményeknek megfelelő nyilatkozatok.

Az Azure AD használati feltételek a következő helyzetekben használhatja:

- A szervezet minden tagjára vonatkozó általános használati feltételek. 

- A felhasználói attribútumok (például) alapján adott használati feltételek orvosi vs ápolók vagy hazai és nemzetközi alkalmazottak, dinamikus csoportok által végzett). 

- Konkrét használati feltételek eléréséhez nagy üzleti hatás alkalmazások, például a Salesforce.

További információkért lásd: [Azure Active Directory használati](active-directory-tou.md).


---
**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** PIM  
**A termék képesség:** Privileged Identity Management  


Az Azure Active Directory Privileged Identity Management (PIM), kezelheti, felügyeletére és figyelésére elérhető Azure erőforrások (előzetes verzió) a szervezetben. Ez magában foglalja az előfizetések, erőforrás-csoportok és még akkor is, virtuális gépek. Összes erőforrást az Azure-portálon belül, amely kihasználja az Azure szerepköralapú hozzáférés vezérlés (RBAC) funkció előnyeit az összes nagy biztonsági és életciklus-kezelési képességei Azure AD PIM nyújtotta előnyöket, és hogy tervezzük néhány nagyszerű új szolgáltatásokat Az Azure AD szerepkörök hamarosan.

További információkért lásd: [az Azure-erőforrások PIM](privileged-identity-management/azure-pim-resource-rbac.md).


---
**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** értékelést eléréséhez  
**A termék képesség:** irányítás  


Hozzáférés értékelést (előzetes verzió) lehetővé teszik a szervezetek hatékonyabban csoporttagságok kezelése és vállalati alkalmazások: 

- Vendég felhasználói hozzáférés alkalmazásokhoz való hozzáférés értékelést, és a csoportok tagságát is recertify. A hozzáférés értékelést által biztosított feltárása hatékonyan eldöntheti, hogy vendégek kell továbbra is hozzáférési felülvizsgálók engedélyezése.

- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információkért lásd: [ellenőrzi, hogy az Azure AD hozzáférési](active-directory-azure-ad-controls-access-reviews-overview.md).


---
**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** alkalmazásaimat  
**A termék képesség:** egyszeri bejelentkezés  


**Hogy külső gyártótól származó alkalmazásokat a saját alkalmazásokat és az Office 365 indítója**

Hatékonyabban kezelheti az alkalmazásokat, amelyek jelenik meg a felhasználói portálon keresztül egy új **app elrejtése** tulajdonság. Ezzel a megoldással az esetekben, amikor alkalmazások ikonjaihoz háttér-szolgáltatások vagy ismétlődő csempék láthatók, és végül felhasználó alkalmazás kilövők rendezetlenné tétele. A váltógomb a Tulajdonságok szakaszának a külső alkalmazás található, és lett címkézve **felhasználó számára látható?**. Egy alkalmazás programozott módon a PowerShell segítségével is elrejthetők. 

További információkért lásd: [elrejtése a külső alkalmazás Azure Active Directoryban lévő felhasználói élmény](active-directory-coreapps-hide-third-party-app.md). 


**Mi az az elérhető?**

 Az új felügyeleti konzol áttérés részeként hajtottunk 2 új API-k elérhető az Azure AD-tevékenység naplók beolvasásakor. Az új API-készlet adja meg a gazdagabb szűrési és rendezési funkció gazdagabb vizsgálati és bejelentkezési tevékenységek nyújtása mellett. Korábban a biztonsági jelentések keresztül elérhető adatok most Identity Protection kockázati események API Microsoft Graph keresztül érhető el.


## <a name="september-2017"></a>2017. szeptember

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** a Microsoft Identity Manager  
**A termék funkció:** Identitáskezelés életciklusa  


A kumulatív gyorsjavítás (build 4.4.1642.0) szeptember 25, 2017, a Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1) től érhető el. A csomag:

- Oldja fel a problémákat, és hozzáadja a fejlesztései
- Az összesítő frissítés, amely kiváltja a Microsoft Identity Manager 2016 4.4.1459.0 buildet legfeljebb minden MIM 2016 SP1 frissítés. 
- Szükséges hozzá **Microsoft Identity Manager 2016 4.4.1302.0 felépítéséhez.** 

További információkért lásd: [kumulatív gyorsjavítás (build 4.4.1642.0) érhető el a Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---
