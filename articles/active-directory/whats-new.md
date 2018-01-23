---
title: "Újdonságok Azure Active Directory kibocsátási megjegyzései |} Microsoft Docs"
description: "Ismerje meg, milyen újdonságokat Azure Active Directory (Azure AD), például a legújabb kibocsátási megjegyzései, az ismert problémák, hibajavításokat tartalmaz, az elavult funkciók és a jövőbeni változtatásokról."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: c7aab313e6c848c97447cde22752cfed945442df
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Újdonságok az Azure Active Directoryban?




> Friss tudnivalók what's new in Azure Active Directory (Azure AD) való feliratkozással együtt a [ ![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [hírcsatorna](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Az Azure AD fogadja folyamatos fejlesztéseket. Maradjon naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

-   A legfrissebb verziókban
-   Ismert problémák
-   Hibajavítások
-   Elavult funkciók
-   Módosítások tervek

Ezen a lapon a havi frissül, ezért le újra rendszeresen.


## <a name="december-2017"></a>2017. december
 

### <a name="terms-of-use-in-the-access-panel"></a>A hozzáférési Panel a használati feltételek

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** Cégirányítási/megfelelőségi
 
Most már a hozzáférési panel megnyithatja és megtekintheti a használati feltételeket, amelyek korábban elfogadott.

Kövesse az alábbi lépéseket:

1. Lépjen a [MyApps portal](https://myapps.microsoft.com), és jelentkezzen be.

2. A jobb felső sarokban válassza ki a nevét, majd válassza ki **profil** a listából. 

3. Az a **profil**, jelölje be **tekintse át a használati feltételek**. 

4. Most már megtekintheti a használati feltételeket elfogadott. 

További információkért lásd: a [(előzetes verzió) szolgáltatás az Azure AD feltételeinek](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Új Azure AD bejelentkezés során tapasztal élmény

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** az Azure AD  
**A termék képesség:** felhasználói hitelesítés
 
Az Azure AD és a Microsoft-fiók identitásrendszere UI volt újratervezett úgy, hogy a konzisztens Megjelenés és működés. Emellett az Azure AD bejelentkezési oldal gyűjti a felhasználónév először, a második képernyőn a hitelesítő adatok követ.

További információkért lásd: [az új Azure AD bejelentkezés során tapasztal élmény mostantól nyilvános előzetes verziójában](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Kevesebb bejelentkezési kérdésben: egy új "bejelentkezve szeretnék maradni" tapasztalattal az Azure AD-bejelentkezés

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** az Azure AD  
**A termék képesség:** felhasználói hitelesítés
 
A **bejelentkezve szeretnék maradni** jelölőnégyzetet az Azure AD bejelentkezési oldal váltotta fel egy új értesítés megjelenő után sikeresen hitelesíteni. 

Ha **Igen** kell a figyelmeztetésre, a szolgáltatás lehetővé teszi egy állandó frissítési jogkivonat. Ez a viselkedés megegyezik a kiválasztott amikor a **bejelentkezve szeretnék maradni** jelölőnégyzetet a régi élményt nyújt. Összevont bérlők esetén ez a kérdés az összevont szolgáltatás sikeres hitelesítést követően jeleníti meg.

További információkért lásd: [kevesebb bejelentkezési kérdésben: az új "bejelentkezve szeretnék maradni" az Azure AD szolgáltatás Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>A használati feltételek elfogadása előtt kell bővíteni szükséges konfiguráció hozzáadása

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** irányítás
 
A beállítás a rendszergazdák a felhasználónak kell bontsa ki a használati feltételeket a feltételek elfogadása előtt.

Válassza ki vagy **a** vagy **ki** a felhasználók bontsa ki a használati feltételeket. A **a** beállítás használatakor a felhasználóknak a megtekintése előtt őket a használati feltételek.

További információkért lásd: a [(előzetes verzió) szolgáltatás az Azure AD feltételeinek](active-directory-tou.md).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>A megfelelő szerepkör-hozzárendelések hatókörön belüli aktiválás

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** Privileged Identity Management  
**A termék képesség:** Privileged Identity Management
 
Hatókörön belüli aktiválási jogosult Azure-erőforrás szerepkör-hozzárendelések kisebb, mint az eredeti hozzárendelés alapértelmezett önállóan aktiválásához használhatja. Például akkor, ha a kapott előfizetés tulajdonosaként az Ön bérelt szolgáltatásának. A hatókörbe tartozó aktiválási aktiválhatja a tulajdonosi szerepkört az előfizetés (például a csoportok és a virtuális gépek) lévő öt erőforrások. Az aktiválás hatókörének előfordulhat, hogy az esélye, a kritikus fontosságú Azure-erőforrások nemkívánatos módosítások végrehajtásakor.

További információkért lásd: [Mi az Azure AD Privileged Identity Management?](active-directory-privileged-identity-management-configure.md).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Új összevont alkalmazások az Azure AD-alkalmazásgyűjtemény

**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** vállalati alkalmazások  
**A termék képesség:** külső integráció
 
A December 2017 a következő új alkalmazások összevonási támogatással rendelkező hozzáadott a gyűjtemény.

|Name (Név)|Integráció típusa|Leírás|
|:-- |----------------|:----------|
|EFI digitális kirakat|SAML 2.0|[Webes 2 nyomtatás](https://go.microsoft.com/fwlink/?linkid=861685) alkalmazás.|
|Vodeclic|SAML 2.0|Az Azure AD segítségével felügyelheti a felhasználók hozzáférését és az egyszeri bejelentkezés engedélyezése a [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522). Meglévő Vodeclic fiókra van szükség.|
|Accredible|SAML 2.0|Használjon [Accredible](https://go.microsoft.com/fwlink/?linkid=863523) létrehozása, kezelése és a tanúsítványok, jelvények és blockchain hitelesítő adatokat.|
|FactSet|SAML 2.0|Egyszeri bejelentkezés a [FactSet FDSWeb alkalmazás](https://go.microsoft.com/fwlink/?linkid=863525).|
|MobileIron Azure AD-integrációs|SAML 2.0|Modern vállalatok használható [MobileIron](https://go.microsoft.com/fwlink/?linkid=858027) eszköz a információk kezelésére, az átvitel során mobile és a felhő míg azok az adatvédelmi és megbízhatósági megőrzése.|
|KÉP MŰKÖDIK|SAML 2.0|Az Azure AD segítségével felügyelheti a felhasználók hozzáférését, felhasználói fiókok kiépítése és egyszeri bejelentkezés engedélyezése a [kép WORKS](https://go.microsoft.com/fwlink/?linkid=863517). Egy meglévő LEMEZKÉPET WORKS előfizetést igényel.|
|Bitbucket felbontása GmbH a SAML SSO|SAML 2.0|[Egyszeri bejelentkezés Bitbucket](https://go.microsoft.com/fwlink/?linkid=863519) delegáltak hitelesítés az Azure ad Szolgáltatásba. Már bejelentkezett az Azure AD-felhasználók férhetnek hozzá közvetlenül bitbucket szolgáltatásokhoz. Felhasználók létrehozása, és az SAML-attribútumok adatokkal parancsprogramok frissítése.|
|A felbontása GmbH bambusz SAML SSO|SAML 2.0|[Egyszeri bejelentkezés bambusz](https://go.microsoft.com/fwlink/?linkid=863520) delegáltak hitelesítés az Azure ad Szolgáltatásba. Már bejelentkezett az Azure AD-felhasználók bambusz közvetlenül.|
|Communifire|SAML 2.0|[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) olyan modern, teljes funkcionalitású közösségi intranetes szoftver, amely támogatja az alkalmazottak és üzleti.|
|MOBI|SAML 2.0|Használjon [MOBI](https://go.microsoft.com/fwlink/?linkid=863521) központosítása, érthető, és szabályozhatja a teljes eszköz ökoszisztéma.|
|Reflektive|SAML 2.0|[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518) egy modern platform teljesítménykezelés, a valós idejű visszajelzését és a cél beállítás. |
|CybSafe|OpenID Connect & OAuth|A számítógépes-tájékoztatási GCHQ hitelesített platform speciális technológia és az adatok analytics használja az emberi aspektus számítógépes biztonsági és a data protection kockázat csökkentése érdekében.|
|WebHR|OpenID Connect & OAuth|A közösségi emberi erőforrások szoftver több mint 20 000 vállalatok 197 országokban megbízható.|
 |Zenegy Azure AD-integrációs|OpenID Connect & OAuth|Ezzel az alkalmazással bejelentkezni Zenegy használhatja a vállalat az Azure AD hitelesítő adatait.|
|Az Adobe élmény Manager|SAML 2.0|A tartalom átfogó felügyeleti platform megoldás segítségével build webhelyek, mobilalkalmazások és űrlapok a marketing tartalom és az eszközök kezelését.|

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Az Azure Active directory szerepkörök a jóváhagyási munkafolyamatai

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** Privileged Identity Management  
**A termék képesség:** Privileged Identity Management
 
Az Azure Active directory szerepkörök jóváhagyási munkafolyamata az általánosan elérhető.

A jóváhagyási munkafolyamata kiemelt szerepkör rendszergazdák megkövetelheti szerepkör aktiválás kéréséhez jogosult-szerepkör tagjai az emelt szintű szerepkör használatához. Több felhasználók és csoportok delegált jóváhagyási feladatkörök lehet. Jogosult szerepkör tagjai értesítések jóváhagyási befejeződött, és a szerepkör aktív.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Áteresztő hitelesítés: a Skype vállalati verziójának ügyfélszolgálatával

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** hitelesítések (Bejelentkezések)  
**A termék képesség:** felhasználói hitelesítés


Áteresztő hitelesítés támogatja a felhasználói bejelentkezések Skype üzleti ügyfélalkalmazások, amely támogatja a modern hitelesítést, amely tartalmazza az online és a hibrid topológiák. 

További információkért lásd: [a modern hitelesítést használó támogatott üzleti topológiák Skype](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Az Azure RBAC (előzetes verzió) Azure AD Privileged Identity Management frissítések

**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** Privileged Identity Management  
**A termék képesség:** Privileged Identity Management
 
A nyilvános előzetes verzió frissítése az Azure AD Privileged Identity Management (PIM) a átruházásához hozzáférés-vezérlés (RBAC) az alábbi műveleteket hajthatja végre:

* Éppen elég felügyelettel használja.
* Erőforrás-szerepkörök aktiválása jóváhagyást igényel.
* Egy szerepkör, amely jóváhagyásra van szüksége a két Azure AD és az Azure RBAC-szerepkörök a jövőbeli aktiválás ütemezni.

 
További információkért lásd: [az Azure-erőforrások (előzetes verzió) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>2017. november
 
### <a name="access-control-service-retirement"></a>Hozzáférés-vezérlés szolgáltatás kivonása



**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** hozzáférés-vezérlés szolgáltatás  
**A termék képesség:** hozzáférés-vezérlés szolgáltatás 


 Az Azure Active Directory hozzáférés-vezérlés (más néven a hozzáférés-vezérlés szolgáltatás) késői 2018 a rendszerből. A részletes ütemezés és a magas szintű áttelepítési útmutató tartalmaz további információt nyújtanak a következő néhány hétben. Hagyhatja megjegyzések ezen a lapon, a hozzáférés-vezérlés szolgáltatás kapcsolatos kérdéseket, és a-csoport tagja lesz válaszol rájuk.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Az Intune Managed Browser böngésző-hozzáférés korlátozása 


**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem




Office 365 és más Azure AD-csatlakoztatott felhőalkalmazások böngészőalapú hozzáférés korlátozhatja az Intune Managed Browser használják, mint az engedélyezett alkalmazások. 

Most már konfigurálhatja az alkalmazás-alapú feltételes hozzáférés a következő feltételt:

**Ügyfélalkalmazások:** böngésző

**Mi az a változás hatását?**

Jelenleg nincs hozzáférése a probléma használatakor. A kép nem érhető el, minden hozzáférés szükséges a használja a felügyelt böngésző alkalmazást. 

Keresse meg a képességre és a közelgő blogok és a kibocsátási megjegyzések a további információk. 

További információkért lásd: [feltételes hozzáférés az Azure AD](active-directory-conditional-access-azure-portal.md).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott alkalmazások az Azure AD alkalmazás-alapú feltételes hozzáférés

 
**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem




A következő alkalmazások listájának hozzáadni tervezett [ügyfélalkalmazások jóváhagyott](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


További információkért lásd:

- [Jóváhagyott app követelmény](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)
- [Azure AD, alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Használati feltételek több nyelven támogatása



**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** Cégirányítási/megfelelőségi





A rendszergazdák mostantól hozhat létre új használati feltételek több PDF-dokumentumot tartalmazó. Jelölheti meg a megfelelő nyelvű verzióját PDF dokumentumokhoz. A felhasználóknak megjelenik a PDF-fájl a megfelelő nyelvű verzióját preferenciáik alapján. Nincs egyezés, ha az alapértelmezett nyelv jelenik meg.


---
 

### <a name="real-time-password-writeback-client-status"></a>Valós idejű jelszó visszaírási ügyfél állapota



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** önkiszolgáló jelszóváltoztatás  
**A termék képesség:** felhasználói hitelesítés


 

Most már a helyszíni jelszó visszaírási ügyfél állapotát tekintheti meg. Ez a beállítás érhető el a **helyszíni integráció** szakasza a [jelszó-átállítási](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) lap. 

Ha a kapcsolat a helyszíni visszaírási ügyfél problémák vannak, egy hibaüzenet, amely tartalmazza a jelenik meg:

- Miért nem tud csatlakozni a helyszíni visszaírási ügyfél információkat.
- Dokumentáció, és segítséget nyújt a probléma megoldását mutató hivatkozást. 


További információkért lásd: [helyszíni integráció](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD, alkalmazás-alapú feltételes hozzáférés 



 
**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** az Azure AD  
**A termék képesség:** identitás biztonság és védelem





Most korlátozzuk Office 365 és más Azure AD-csatlakoztatott felhőalapú alkalmazások [ügyfélalkalmazások jóváhagyott](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) , amely támogatja az Intune app adatvédelmi szabályzatok használatával [az Azure AD alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md). Intune app adatvédelmi szabályzatok segítségével konfigurálhatja, és ezeket az ügyfél alkalmazásokat a vállalati adatok védelme.

Kombinálásával [alkalmazás-alapú](active-directory-conditional-access-mam.md) rendelkező [eszközalapú](active-directory-conditional-access-policy-connected-applications.md) feltételes hozzáférési házirendeket, így rugalmasan adatok a személyes és vállalati eszközök védelme érdekében.

A következő feltételek és szabályozza a való használathoz az alkalmazás-alapú feltételes hozzáférés mostantól érhetők el:

**Támogatott platformonként feltétel**

- iOS
- Android

**Ügyfél alkalmazások feltétel**

- Mobilalkalmazások és asztali ügyfelek

**Hozzáférés-vezérlés**

- Jóváhagyott ügyfélalkalmazás megkövetelése


További információkért lásd: [az Azure AD alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Az Azure-portálon az Azure AD-eszközök kezelése



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** eszközök regisztrációja és kezelése  
**A termék képesség:** identitás biztonság és védelem

 



Most már található az Azure ad Szolgáltatásba csatlakoztatott összes eszközre és az eszközzel kapcsolatos tevékenységek egy helyen. Nincs olyan új felügyeleti élmény kezeli az eszköz identitások és beállításokat az Azure portálon. Ebben a kiadásban a következőket teheti:

- Megtekintheti az eszközök által biztosított a feltételes hozzáférés az Azure ad-ben.
- Megtekintheti a tulajdonságait, többek között a hybrid Azure AD-csatlakoztatott eszközökhöz.
- BitLocker-kulcsok a Azure AD-csatlakoztatott eszközök keresése, az eszköz Intune-nal, és több kezelését.
- Az Azure AD eszközzel kapcsolatos beállítások kezelése.

További információkért lásd: [eszközök kezelése az Azure portál használatával](device-management-azure-portal.md).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>A feltételes hozzáférés az Azure AD eszköz platformként macOS támogatása 



**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem 
 

Most belefoglalható (vagy kizárása) macOS eszköz platform feltétele az Azure AD feltételes hozzáférési házirendben. MacOS a támogatott eszközplatformok való hozzáadásával a következőket teheti:

- **Eszközök regisztrálására és kezelésére macOS Intune-nal.** Más platformokon, például az iOS és Android rendszerhez hasonló, a vállalati portál alkalmazás érhető el macOS egyesített regisztrációkat elvégzéséhez. Az új vállalati portál alkalmazás macOS segítségével regisztrálhasson egy eszközt az Intune-nal, és regisztrálhatja azt az Azure ad-val.
- **Győződjön meg arról, macOS eszközök igazodnia kell a szervezet megfelelőségi szabályzatok az Intune-ban megadva.** Az Intune-ban az Azure portálon most már beállíthat macOS eszközök megfelelőségi szabályzatainak. 
- **Alkalmazások való hozzáférés korlátozása csak a megfelelő macOS eszközökre az Azure AD-ben.** MacOS feltételes hozzáférési házirend szerzői rendelkezik egy különálló eszköz platform lehetőséget. Most a célzott alkalmazás beállítása az Azure-ban macOS vonatkozó feltételes hozzáférési házirendjei is létrehozhat.

További információkért lásd:

- [MacOS eszközök megfelelőségi szabályzat létrehozása az Intune-nal](https://aka.ms/macoscompliancepolicy)
- [Feltételes hozzáférés az Azure ad-ben](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Hálózati házirend-kiszolgáló bővítmény Azure multi-factor Authentication 


**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** többtényezős hitelesítés  
**A termék képesség:** felhasználói hitelesítés




A hálózati házirend-kiszolgáló bővítmény, az Azure multi-factor Authentication a hitelesítési infrastruktúra felhőalapú többtényezős hitelesítés lényeges képességét biztosítja a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló kiterjesztésű adhat hozzá a telefonhívás, szöveges üzenetet vagy telefonszám alkalmazás ellenőrzése a meglévő hitelesítési folyamatot. Nincs telepítése, konfigurálása és karbantartása az új kiszolgálók. 

Ezt a bővítményt a szervezeteknek, amelyek a virtuális magánhálózati kapcsolatok védelme az Azure multi-factor Authentication kiszolgáló telepítés nélkül kívánja lett létrehozva. A hálózati házirend-kiszolgáló között RADIUS és a felhőalapú Azure multi-factor Authentication második tényezőként hitelesítésének biztosításához adapterként működik bővítmény összevont, vagy a szinkronizált felhasználók.


További információkért lásd: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication-nps-extension.md).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Állítsa vissza vagy véglegesen eltávolítja a törölt felhasználók


**Típus:** új szolgáltatás    
**Szolgáltatás kategória:** felhasználók kezelése  
**A termék képesség:** könyvtár 



Az Azure AD felügyeleti központban az alábbi műveleteket hajthatja végre:

- Állítsa vissza a felhasználóhoz. 
- A felhasználó véglegesen törli.


**Próbálja:**

1. Válassza ki az Azure AD felügyeleti központban [minden felhasználó](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) a a **kezelése** szakasz. 

2. Az a **megjelenítése** listáról válassza ki **nemrég törölt felhasználók**. 

3. Válasszon legalább egy nemrég törölt felhasználók, és majd állíthatja vissza azokat, vagy véglegesen törli.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott alkalmazások az Azure AD alkalmazás-alapú feltételes hozzáférés

 
**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem


A következő alkalmazások listáját a rendszer adott [ügyfélalkalmazások jóváhagyott](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 


További információkért lásd:

- [Jóváhagyott app követelmény](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)
- [Azure AD, alkalmazás-alapú feltételes hozzáférés](active-directory-conditional-access-mam.md)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Használja a feltételes hozzáférési házirend vezérlőelemek között a "vagy" 


**Típus:** módosulás a szolgáltatás    
**Szolgáltatás kategória:** feltételes hozzáférés  
**A termék képesség:** identitás biztonság és védelem

 
Most már használhatja "vagy" (van szükség a kijelölt vezérlők egyik) a feltételes hozzáférés-vezérlést. E szolgáltatás használatával házirendek létrehozása, a "vagy" közötti hozzáférés-vezérlést. Például használhatja ezt a szolgáltatást, amelyhez a felhasználó a multi-factor authentication használatával bejelentkezni "vagy" kell lennie a megfelelő eszközökre házirend létrehozása.

További információkért lásd: [vezérlők az Azure AD feltételes hozzáférésével](active-directory-conditional-access-controls.md).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Valós idejű kockázati események összesítése


**Típus:** módosulás a szolgáltatás    
**Szolgáltatás kategória:** Identity protection  
**A termék képesség:** identitás biztonság és védelem


Az Azure AD Identity Protection összes valós idejű kockázat származó eseményt az azonos IP-cím egy adott napon most összesítése az egyes kockázat esemény. Ez a változás a felhasználó biztonsági változás nélkül látható kockázati események mennyiségének korlátozza.

Az alapul szolgáló valós idejű észlelés működik minden alkalommal, amikor a felhasználó bejelentkezik. Ha egy bejelentkezési kockázat biztonsági házirend beállítása a multi-factor authentication blokkolja a hozzáférést, továbbra is indított minden kockázatos bejelentkezés során.

 
---
 




## <a name="october-2017"></a>2017. október


### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések érvényteleníthető


**Típus:** tervezett változtatás  
**Szolgáltatás kategória:** jelentéskészítés  
**A termék képesség:** Identitáskezelés életciklusa  



Az Azure-portálon biztosítja:

- Egy új Azure AD felügyeleti konzolon.
- Új API-khoz, tevékenységek és biztonsági jelentések.
 
Az új lehetőségekhez, mert a jelentés API-k alapján a/Reports végpont 2017. December 10. volt használatból. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatikus bejelentkezés mező észlelése


**Típus:** rögzített   
**Szolgáltatás kategória:** saját alkalmazások  
**A termék képesség:** egyszeri bejelentkezés  



Az Azure AD egy HTML-felhasználó nevét és jelszavát mező leképező alkalmazások automatikus bejelentkezési mező észlelését támogatja. Ezeket a lépéseket ismertetett [automatikusan rögzítése az alkalmazás bejelentkezési mezők](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Ez a funkció található hozzáadásával egy *nem-gyűjtemény* alkalmazás a a **vállalati alkalmazások** lapját a [Azure-portálon](http://aad.portal.azure.com). Emellett konfigurálhatja a **egyszeri bejelentkezés** módra az új alkalmazást a **jelszó-alapú egyszeri bejelentkezést**, adja meg a webszolgáltatás URL-címet, és mentse a a lap.
 
Ez a funkció egy szolgáltatási probléma miatt ideiglenesen letiltotta. A probléma, és az automatikus bejelentkezési mező észlelési érhető el újra.

---

### <a name="new-multi-factor-authentication-features"></a>Új többtényezős hitelesítési szolgáltatások


**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** többtényezős hitelesítés  
**A termék képesség:** identitás biztonság és védelem  



Többtényezős hitelesítés (MFA) a szervezet védelmének fontos részét képezi. Csak azok több adaptív hitelesítő adatokat és a felhasználói élmény zökkenőmentesebb, a következő funkciók lettek hozzáadva: 

- Multi-factor Authentication ellenőrző eredmények közvetlenül integrálva vannak az Azure AD-bejelentkezés jelentést, amely magában foglalja a többtényezős hitelesítés eredmények programozott hozzáférést.
- A többtényezős hitelesítés konfigurációs mélyebb integrálva van az Azure Active Directory beállítása az Azure portálon tapasztalható.

Ebben az nyilvános előzetes MFA felügyeleti és jelentéskészítési konfigurációs tapasztalatok core az Azure AD integrált részét képezik. Most már a többtényezős hitelesítés felügyeleti portál funkció az Azure AD-élmény belül kezelheti.

További információkért lásd: [a többtényezős hitelesítés az Azure portálon jelentési hivatkozás](active-directory-reporting-activity-sign-ins-mfa.md). 


---

### <a name="terms-of-use"></a>Használati feltételek



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** használati feltételek  
**A termék képesség:** irányítás  



Használhatja az Azure AD használati feltételeket, például a jogi vagy megfelelőségi követelményeknek megfelelő nyilatkozatok van a felhasználók számára.

Az Azure AD használati feltételek a következő helyzetekben használhatja:

- Általános használati feltételeket a szervezete összes felhasználója számára
- Egy felhasználói attribútum (például ápolók és orvosi) vagy belföldi és nemzetközi alkalmazottai, a dinamikus csoportok által megadott használati feltételek
- Konkrét használati feltételek a nagy jelentőségű üzleti alkalmazások, például a Salesforce elérése

További információkért lásd: [az Azure AD használati feltételek](active-directory-tou.md).


---

### <a name="enhancements-to-privileged-identity-management"></a>A Privileged Identity Management fejlesztései


**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** Privileged Identity Management  
**A termék képesség:** Privileged Identity Management  


Az Azure AD Privileged Identity Management kezelése, szabályozása, és figyelje az Azure-erőforrások (előzetes verzió) hozzáférést a szervezeten belül:

- Előfizetések
- Erőforráscsoportok
- Virtual machines (Virtuális gépek) 

Erőforrások az Azure portálon az Azure RBAC-funkciók használatára kihasználhatja a biztonsági és életciklus-kezelési képességei, amelyre az Azure AD Privileged Identity Management kínálnak.

További információkért lásd: [Privileged Identity Management az Azure-erőforrások](privileged-identity-management/azure-pim-resource-rbac.md).


---

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok


**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** értékelést eléréséhez  
**A termék képesség:** irányítás  



Szervezet hozzáférés értékelést (előzetes verzió) segítségével hatékonyan tudja kezelni a csoporttagságokat és a vállalati alkalmazásokhoz való hozzáférés: 

- Az alkalmazásokhoz való hozzáférések és a csoporttagságok felülvizsgálatával újrahitelesítheti a vendégfelhasználói hozzáférést. Felülvizsgálók hatékonyan eldöntheti, hogy a vendégek továbbra is biztosítja a hozzáférés értékelést feltárása alapján.
- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információkért lásd: [ellenőrzi, hogy az Azure AD hozzáférési](active-directory-azure-ad-controls-access-reviews-overview.md).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Külső felek alkalmazásainak a személyes alkalmazások és az Office 365 alkalmazás indító elrejtése



**Típus:** új szolgáltatás  
**Szolgáltatás kategória:** saját alkalmazások  
**A termék képesség:** egyszeri bejelentkezés  



Most jobban kezelhető jelenik meg a felhasználói portálon keresztül egy új alkalmazások **app elrejtése** tulajdonság. Azokban az esetekben, ahol alkalmazások ikonjaihoz jelenik meg a háttér-szolgáltatásaihoz vagy ismétlődő csempék és zsúfoltságát felhasználók app kilövők alkalmazások elrejthetők. A váltógomb van a **tulajdonságok** szakasz a külső alkalmazás és lett címkézve **látható a felhasználó számára?** Egy alkalmazás programozott módon a PowerShell segítségével is elrejthetők. 

További információkért lásd: [egy külső alkalmazások az Azure AD-ben a felhasználói élmény elrejtése](active-directory-coreapps-hide-third-party-app.md). 


**Mi az az elérhető?**

 Az új felügyeleti konzol, a két új API-k történő áttelepítés részeként az Azure Active Directory érhetők el naplók. Az új API-készlet gazdagabb szűrési és rendezési gazdagabb vizsgálati és bejelentkezési tevékenységek nyújtása mellett funkciókat biztosít. Korábban a biztonsági jelentések most keresztül elérhető adatok Identity Protection kockázati események API Microsoft Graph is elérhetőek.


## <a name="september-2017"></a>2017. szeptember

### <a name="hotfix-for-identity-manager"></a>A gyorsjavítás az Identity Manager


**Típus:** módosulás a szolgáltatás  
**Szolgáltatás kategória:** Identity Manager  
**A termék képesség:** Identitáskezelés életciklusa  



A kumulatív gyorsjavítás (build 4.4.1642.0) szeptember 25, 2017, Identity Manager 2016 Service Pack 1-től érhető el. A csomag:

- Oldja fel a problémákat, és hozzáadja a fejlesztései.
- Az összesítő frissítés, amely kiváltja a build 4.4.1459.0 Identity Manager 2016 legfeljebb frissítéseinek Identity Manager 2016 Service Pack 1. 
- Szükséges hozzá Identity Manager 2016 4.4.1302.0 felépítéséhez. 

További információkért lásd: [kumulatív gyorsjavítás (build 4.4.1642.0) érhető el az Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
