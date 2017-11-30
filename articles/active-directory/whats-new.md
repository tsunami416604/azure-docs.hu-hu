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
ms.date: 11/28/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f1538e1c26cfe658c7f42ccdd57d8bf5aca0b1fb
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
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
 
### <a name="retiring-acs"></a>ACS kivonása



**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** ACS  
**A termék képesség:** vezérlő szolgáltatás 


A Microsoft Azure Active Directory hozzáférés-vezérlés (más néven a hozzáférés-vezérlési szolgáltatásban vagy az ACS) késői 2018 a rendszerből.  További információk, például egy részletes ütemezés és a magas szintű áttelepítési útmutató fognak adni a következő néhány hétben. Időközben ezen az oldalon hagyja megjegyzések ACS kapcsolatos kérdéseivel, és a csapat tagjai segít választ.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Az Intune managed browser böngésző-hozzáférés korlátozása 


**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonsági és védelmi




Ez a viselkedés az lesz az Office 365 és más Azure AD-csatlakoztatott felhőalkalmazások jóváhagyott alkalmazások használata az Intune Managed Browser böngésző-hozzáférés korlátozása. 

Ez a módosítás lehetővé teszi, hogy konfigurálja az alkalmazás-alapú feltételes hozzáférés a következő feltételt:

**Ügyfélalkalmazások:** böngésző

**Mi az a változás hatását?**

Jelenleg nincs hozzáférése a probléma használatakor. Ez a viselkedés előnézete nem érhető el, minden hozzáférés szükséges a használja a felügyelt böngésző alkalmazást. 

Tekintse meg ezt a funkciót, és több a jövőbeli blogok és a kibocsátási megjegyzésekben. 

További információkért lásd: [feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott alkalmazások az Azure AD alkalmazás-alapú feltételes hozzáférés

 
**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonsági és védelmi




A következő alkalmazások listájának hozzáadni tervezett [ügyfélalkalmazások jóváhagyott](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


További információkért lásd:

- [Jóváhagyott app követelmény](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory, alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Több nyelv használatát támogatási feltételeit



**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** Cégirányítási/megfelelőségi





A rendszergazdák mostantól hozhat létre új használati feltételek (a használati feltételek), amely több PDF-dokumentumot tartalmaz. Jelölheti meg a megfelelő nyelvű verzióját PDF dokumentumokhoz. A felhasználóknak, amelyek hatókörében megjelenik PDF-fájl a megfelelő nyelvű verzióját preferenciáik alapján. Nincs egyezés, ha az alapértelmezett nyelv jelenik meg.


---
 

### <a name="realtime-password-writeback-client-status"></a>Valós idejű jelszó visszaírási ügyfél állapota



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** SSPR  
**A termék képesség:** felhasználói hitelesítés


 

Most már a helyszíni jelszó visszaírási ügyfél állapotát tekintheti meg. Ez a beállítás érhető el a **helyszíni integráció** szakasza a  **[jelszó-átállítási](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)**  lap. 

Ha a kapcsolat a helyszíni visszaírási ügyfél problémák vannak, egy hibaüzenet, amely tartalmazza a jelenik meg:

- Miért nem tud csatlakozni a helyszíni visszaírási ügyfél információk 
- Dokumentáció, és segítséget nyújt a probléma megoldását mutató hivatkozást. 


További információkért lásd: [helyszíni integráció](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD, alkalmazás-alapú feltételes hozzáférés 



 
**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** az Azure AD  
**A termék képesség:** identitás biztonsági és védelmi





Most korlátozhatja hozzáférést az Office 365 és más Azure AD-csatlakoztatott felhőalapú alkalmazások [ügyfélalkalmazások jóváhagyott](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) , amelyek támogatják az Intune App Protection házirendek [az Azure AD alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md). Intune app adatvédelmi szabályzatok segítségével konfigurálhatja, és ezeket az ügyfél alkalmazásokat a vállalati adatok védelme.

Kombinálásával [alkalmazás-alapú](active-directory-conditional-access-mam.md) rendelkező [eszközalapú](active-directory-conditional-access-policy-connected-applications.md) feltételes hozzáférési házirendeket, így rugalmasan adatok a személyes és vállalati eszközök védelme érdekében.

A következő feltételek és szabályozza a való használathoz az alkalmazás-alapú feltételes hozzáférés mostantól érhetők el:

**Támogatott platformonként feltétel**

- iOS
- Android

**Ügyfél alkalmazások feltétel**

- Mobilalkalmazások és asztali ügyfelek

**Hozzáférés-vezérlés**

- Jóváhagyott ügyfélalkalmazást igényel


További információkért lásd: [Azure Active Directory alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md).

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>Az Azure-portálon az Azure AD-eszközök kezelése



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** eszközök regisztrációja és kezelése  
**A termék képesség:** identitás biztonsági és védelmi

 



Megtalálja az Azure ad Szolgáltatásba csatlakoztatott összes eszközre és az eszközzel kapcsolatos tevékenységek egy helyen. Nincs olyan új felügyeleti élmény kezeli az eszköz identitások és beállításokat az Azure portálon. Ebben a kiadásban a következőket teheti:

- Az eszköz által biztosított a feltételes hozzáférés az Azure AD megtekintése

- Tulajdonságok megtekintése, beleértve az Azure AD hibrid csatlakoztatott eszközök

- BitLocker-kulcsok a Azure AD-csatlakoztatott eszközök keresése, kezelhesse az eszközét az Intune-ban és több.

- Az Azure AD-eszközzel kapcsolatos beállítások kezelése


További információkért lásd: [kezelése az Azure portál használatával eszközök](device-management-azure-portal.md).



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>MacOS, az Azure AD feltételes hozzáférésével eszközplatform támogatása 



**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonsági és védelmi 
 

Ön most belefoglalható (vagy kizárható) macOS eszköz platform feltételként az Azure AD feltételes hozzáférési házirendben. MacOS a támogatott eszközplatformok való hozzáadásával a következőket teheti:

- **Az Intune-ban macOS eszközöket regisztrálhat és kezelhet** -más platformokon, például az iOS és Android rendszerhez hasonló, a vállalati portál alkalmazás érhető el macOS egyesített regisztrációkat elvégzéséhez. Az új vállalati portál alkalmazás macOS regisztrálhasson egy eszközt az Intune-nal, és regisztrálhatja azt az Azure ad-val teszi lehetővé.
 
- **Győződjön meg arról, macOS eszközök igazodnia kell a szervezet megfelelőségi szabályzatok az Intune-ban meghatározott** -a Intune Azure-portál, most már beállíthat macOS eszközök megfelelőségi szabályzatainak. 
  
- **Alkalmazások az Azure AD-csak a megfelelő macOS eszközökre korlátozza a hozzáférést** -feltételes hozzáférési házirend szerzői rendelkezik egy különálló eszköz platform beállításként macOS. Ez a beállítás lehetővé teszi, hogy készíthet macOS adott feltételes hozzáférési házirendjei a célzott alkalmazás beállítása az Azure-ban.

További információkért lásd:

- [MacOS eszközök megfelelőségi szabályzat létrehozása az Intune-nal](https://aka.ms/macoscompliancepolicy)
- [Feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>Az Azure MFA használatára a hálózati házirend-kiszolgáló bővítmény 


**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** MFA  
**A termék képesség:** felhasználói hitelesítés




A hálózati házirend-kiszolgáló (NPS) bővítményt, az Azure MFA MFA felhőalapú képességek hozzáadása a meglévő kiszolgálók használata hitelesítési infrastruktúráját. A hálózati házirend-kiszolgáló kiterjesztésű adhat hozzá a telefonhívás, szöveges üzenetet vagy telefonszám alkalmazás ellenőrzése a meglévő hitelesítési folyamat telepítése, konfigurálása és karbantartása az új kiszolgálók nélkül. 

A bővítmény szervezeteknek, amelyek a VPN-kapcsolatok védendő az Azure MFA kiszolgáló központi telepítése nélkül lett létrehozva. A hálózati házirend-kiszolgáló bővítmény eleget kell egy adaptert, RADIUS és az Azure MFA felhőalapú arra, hogy egy második tényezős hitelesítési közötti összevont, vagy a szinkronizált felhasználók.


További információkért lásd: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Állítsa vissza vagy véglegesen eltávolítja a törölt felhasználók


**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** felhasználók kezelése  
**A termék képesség:** könyvtár 



Az Azure AD felügyeleti központban az alábbi műveleteket hajthatja végre:

- Törölt felhasználó visszaállítása 
- A felhasználó végleges törlése 


**Próbálja:**

1. Válassza ki az Azure AD felügyeleti központban [ **minden felhasználó** ](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) a a **kezelése** szakasz. 

2. Az a **megjelenítése** listáról válassza ki **nemrég törölt felhasználók**. 

4. Válasszon ki legalább egy nemrég törölt felhasználót, majd állíthatja vissza azokat, és véglegesen törli.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott alkalmazások az Azure AD alkalmazás-alapú feltételes hozzáférés

 
**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonsági és védelmi


A következő alkalmazások listájának hozzáadott [ügyfélalkalmazások jóváhagyott](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- Microsoft Planner

- Microsoft Azure Information Protection 


További információkért lásd:

- [Jóváhagyott app követelmény](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory, alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>A "vagy" között a feltételes hozzáférési házirend vezérlőelemek lehetősége 


**Típus:** módosulás a szolgáltatás    
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonsági és védelmi

 
Olyan "Vagy" (van szükség a kijelölt vezérlők egyik) feltételes hozzáférés-vezérlést kiadása. A szolgáltatás lehetővé teszi a szabályzatok létrehozásához egy **vagy** közötti hozzáférés-vezérlést. Például használhatja ezt a szolgáltatást, amelyhez a felhasználó is bejelentkezhet a multi-factor authentication-házirend létrehozása **vagy** levő megfelelő eszközökre.

További információkért lásd: [az Azure Active Directory feltételes hozzáférést vezérlő](active-directory-conditional-access-controls.md).

 
---

### <a name="aggregation-of-realtime-risk-events"></a>Valós idejű kockázati események összesítése


**Típus:** módosulás a szolgáltatás    
**Szolgáltatás kategória:** Identity Protection  
**A termék képesség:** identitás biztonsági és védelmi


Felügyeleti élményét, az Azure AD Identity Protection, hogy az összes valós idejű kockázat volt származó eseményt az azonos IP-cím egy adott napon most összesítése az egyes kockázat esemény. Ez a módosítás nélkül a felhasználók biztonsági megjelenő kockázati események mennyisége korlátozza.

Az alapul szolgáló valós idejű észlelés minden alkalommal, amikor a felhasználó bejelentkezése működik. Ha egy bejelentkezési kockázat biztonsági házirend beállítása az MFA blokkolja a hozzáférést, továbbra is indított minden kockázatos bejelentkezés során.

 
---
 




## <a name="october-2017"></a>2017. október


### <a name="deprecating-azure-ad-reports"></a>Az Azure AD-jelentések elavulttá


**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** Reporting  
**A termék funkció:** Identitáskezelés életciklusa  



Az Azure-portálon biztosítja:

- Egy új Azure Active Directory felügyeleti konzol 
- Új API-k, tevékenységek és biztonsági jelentések
 
Az új lehetőségekhez, a jelentés API-k miatt alatt a **/reports** végpont 2017. December 10. a rendszerből. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatikus bejelentkezés mező észlelése


**Típus:** rögzített   
**Szolgáltatás kategória:** alkalmazásaimat  
**A termék képesség:** egyszeri bejelentkezés  



Az Azure Active Directory egy HTML-felhasználónév és jelszó mező leképező alkalmazások automatikus bejelentkezési mező észlelését támogatja.  Ezeket a lépéseket ismertetett [automatikusan rögzítése az alkalmazás bejelentkezési mezők](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Ez a funkció található hozzáadásával egy *nem-gyűjtemény* alkalmazás a a **vállalati alkalmazások** lapját a [Azure-portálon](http://aad.portal.azure.com). Emellett konfigurálhatja a **egyszeri bejelentkezés** módra az új alkalmazást a **jelszó-alapú egyszeri bejelentkezést**, egy webes URL-cím megadása és a lap majd mentése közben.
 
Egy szolgáltatási probléma miatt ez a funkció ideiglenesen letiltotta egy ideig. A probléma megoldódott, és az automatikus bejelentkezési mező észlelési érhető el újra.

---

### <a name="new-mfa-features"></a>Új MFA-szolgáltatások


**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** MFA  
**A termék képesség:** identitás biztonsági és védelmi  



Többtényezős hitelesítés (MFA) a szervezet védelmének fontos részét képezi. Csak azok több adaptív hitelesítő adatokat és a felhasználói élmény zökkenőmentesebb, a következő funkciók érhetők el: 

- Integrálása a multi-factor Authentication ellenőrző eredmények közvetlenül az Azure AD bejelentkezési jelentés, beleértve az MFA eredmények programozott hozzáférést

- Az Azure Active Directory beállítása az MFA-konfiguráció szorosabb integrációt tapasztalhat az Azure-portálon

Ebben az nyilvános előzetes MFA felügyeleti és jelentéskészítési konfigurációs tapasztalatok core az Azure AD integrált részét képezik. Összesítése mindkét funkció lehetővé teszi a multi-factor Authentication felügyeleti portál funkció az Azure AD-élmény belül kezelését.

További információkért lásd: [hivatkozás a multi-factor authentication jelentéskészítés az Azure-portálon](active-directory-reporting-activity-sign-ins-mfa.md) 


---

### <a name="introducing-terms-of-use"></a>Használati feltételek bemutatása



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** irányítás  



Az Azure AD használati feltételeket adhat egy egyszerű módszer információ van a végfelhasználók számára. Ez biztosítja, hogy a felhasználók látják a jogi vagy megfelelőségi követelményeknek megfelelő nyilatkozatok.

Az Azure AD használati feltételek a következő helyzetekben használhatja:

- A szervezet minden tagjára vonatkozó általános használati feltételek. 

- A felhasználói attribútumok (például) alapján adott használati feltételek orvosi vs ápolók vagy hazai és nemzetközi alkalmazottak, dinamikus csoportok által végzett). 

- Konkrét használati feltételek eléréséhez nagy üzleti hatás alkalmazások, például a Salesforce.

További információkért lásd: [Azure Active Directory használati](active-directory-tou.md).


---

### <a name="enhancements-to-privileged-identity-management"></a>A privileged identity management fejlesztései


**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** PIM  
**A termék képesség:** Privileged Identity Management  


Az Azure Active Directory Privileged Identity Management (PIM), kezelheti, felügyeletére és figyelésére elérhető Azure erőforrások (előzetes verzió) a szervezetben:

- Előfizetések
- Erőforráscsoportok
- Virtuális gépek. 

Erőforrások az Azure portálon az Azure szerepköralapú hozzáférés vezérlés (RBAC) funkcióit használja ki az összes biztonsági és életciklus-kezelési képességei Azure AD PIM nyújtotta előnyöket kihasználhatja.

További információkért lásd: [az Azure-erőforrások PIM](privileged-identity-management/azure-pim-resource-rbac.md).


---

### <a name="introducing-access-reviews"></a>Ellenőrzi a hozzáférési bemutatása


**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** értékelést eléréséhez  
**A termék képesség:** irányítás  



Hozzáférés értékelést (előzetes verzió) lehetővé teszik a szervezetek hatékonyabban csoporttagságok kezelése és vállalati alkalmazások: 

- Vendég felhasználói hozzáférés alkalmazásokhoz való hozzáférés értékelést, és a csoportok tagságát is recertify. A hozzáférés értékelést által biztosított feltárása hatékonyan eldöntheti, hogy vendégek kell továbbra is hozzáférési felülvizsgálók engedélyezése.

- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információkért lásd: [ellenőrzi, hogy az Azure AD hozzáférési](active-directory-azure-ad-controls-access-reviews-overview.md).


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>Külső felek alkalmazásainak a személyes alkalmazások és az Office 365 indítója elrejtése



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** alkalmazásaimat  
**A termék képesség:** egyszeri bejelentkezés  



Hatékonyabban kezelheti az alkalmazásokat, amelyek jelenik meg a felhasználói portálon keresztül egy új **app elrejtése** tulajdonság. Alkalmazások elrejtése segít az esetekben, amikor alkalmazások ikonjaihoz háttér-szolgáltatások vagy ismétlődő csempék láthatók, és végül felhasználó alkalmazás kilövők rendezetlenné tétele. A váltógomb a Tulajdonságok szakaszának a külső alkalmazás található, és lett címkézve **látható a felhasználó számára?** Egy alkalmazás programozott módon a PowerShell segítségével is elrejthetők. 

További információkért lásd: [elrejtése a külső alkalmazás Azure Active Directoryban lévő felhasználói élmény](active-directory-coreapps-hide-third-party-app.md). 


**Mi az az elérhető?**

 Az új felügyeleti konzol történő áttelepítés részeként az Azure AD tevékenységi naplóit 2 új API-k érhetők el. Az új API-készlet gazdagabb szűrési és rendezési gazdagabb vizsgálati és bejelentkezési tevékenységek nyújtása mellett funkciókat biztosít. Korábban a biztonsági jelentések keresztül elérhető adatok most Identity Protection kockázati események API Microsoft Graph keresztül érhető el.


## <a name="september-2017"></a>2017. szeptember

### <a name="hotfix-for-microsoft-identity-manager"></a>A gyorsjavítás a Microsoft Identity Manager


**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** a Microsoft Identity Manager  
**A termék funkció:** Identitáskezelés életciklusa  



A kumulatív gyorsjavítás (build 4.4.1642.0) szeptember 25, 2017, a Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1) től érhető el. A csomag:

- Oldja fel a problémákat, és hozzáadja a fejlesztései
- Az összesítő frissítés, amely kiváltja a Microsoft Identity Manager 2016 4.4.1459.0 buildet legfeljebb minden MIM 2016 SP1 frissítés. 
- Szükséges hozzá **Microsoft Identity Manager 2016 4.4.1302.0 felépítéséhez.** 

További információkért lásd: [kumulatív gyorsjavítás (build 4.4.1642.0) érhető el a Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---
