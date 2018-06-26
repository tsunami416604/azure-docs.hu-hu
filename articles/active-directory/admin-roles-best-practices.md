---
title: Ajánlott eljárások az Azure AD rendszergazdai hozzáférés biztosítása érdekében |} Microsoft Docs
description: Győződjön meg arról, hogy a szervezet rendszergazdai hozzáférést és rendszergazdai fiókok biztonságos. A rendszer fejlesztők és informatikai szakemberek számára az Azure AD konfigurálása Azure és a Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.date: 06/25/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: 729cb89fbe63dbecb65c6f948052b920bf6e70fe
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937452"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Biztonságossá tétele a felhőalapú és hibrid telepítések privileged access Azure AD-ben

A legtöbb vagy összes üzleti eszközök a modern szervezet biztonsági attól függ, hogy a kiemelt jogosultságú fiókok, felügyelheti és kezelheti az informatikai rendszerek integritását. Például gyakran számítógépes-támadók rosszindulatú szereplője rendszergazdai fiókok és más privilegizált hozzáférést gyorsan hozzáférni az érzékeny adatokat és a használt hitelesítő adatokkal való visszaéléseket támadások rendszerek megpróbálja elemek célként. A felhőalapú szolgáltatások, a megelőzése és a válasz a közös kötelezettségeit, a felhőbeli szolgáltatás szolgáltatója és az ügyfél. A legújabb fenyegetésekről végpontok és a felhő kapcsolatos további információkért tekintse meg a [Microsoft biztonsági az Eszközintelligencia-jelentés](https://www.microsoft.com/security/sir/default.aspx). Ez a cikk segítséget nyújt egy felé az aktuális terv és az itt ismertetett útmutatót közötti hiányosságok terv kidolgozása.

> [!NOTE] 
> A Microsoft elkötelezett a legmagasabb szintű megbízhatóság, átláthatóság, szabványok megfelelési és előírásoknak való megfelelés. Többet hogyan a Microsoft globális incidensválasz-csapat csökkenti a felhőszolgáltatások elleni támadások hatásainak, és hogyan biztonsági be van építve a Microsoft üzleti termékek és a felhőszolgáltatások [Microsoft Trust Center – biztonsági](https://www.microsoft.com/en-us/trustcenter/security)és Microsoft megfelelőségi célkitűzések [Microsoft Trust Center - megfelelőségi](https://www.microsoft.com/en-us/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
A legtöbb szervezet számára az üzleti eszközök biztonsági integritását, a kiemelt jogosultságú fiókok, felügyelheti és kezelheti az informatikai rendszerek függ. Infrastruktúra-rendszereknek (például az Active Directory és az Azure Active Directory) privilegizált hozzáférést ahhoz, hogy a szervezet bizalmas adatokhoz való hozzáférést a támadók számítógépes összpontosítanak. 

Hagyományos megközelítés, amely a be- és pontok az elsődleges biztonsági szegélyhálózati, a hálózat védelme a kevésbé hatékonyak miatt a megnövekedhet a SaaS-alkalmazásokhoz és személyes eszközök az interneten. A hálózati biztonsági szegélyhálózati egy összetett modern vállalati természetes váltja fel a szervezet identitásrétegének hitelesítési és engedélyezési vezérlők. 

Rendszergazdai fiókokhoz hatékonyan maguk irányíthatják a az új "biztonsági szegélyhálózati." Nagyon fontos a rendszerjogosultságú hozzáférés, függetlenül attól, hogy a környezetet a helyszíni, felhőalapú vagy hibrid a helyszíni és felhőalapú üzemeltetett szolgáltatások. Felügyeleti hozzáférés meghatározott ellenfelek elleni védelme szükséges, hogy a teljes és gondos megközelítés igénybe vehet a szervezet rendszerek elkülönítése kockázatoknak. 

Módosítását igényli az emelt szintű hozzáférés biztonságossá tétele
* Folyamatok, a rendszergazdák munkamódszereinek és ismeretgazdálkodás
* Műszaki összetevők, például a gazdagép védelmekkel, fiókja védelmét és Identitáskezelés

Ez a dokumentum elsősorban a létrehozása kezelt identitások biztosításához és a hozzáférés, a terv vagy történtek az Azure AD, Microsoft Azure-ban Office 365 és más felhőalapú szolgáltatásokat foglalkozik. Olyan szervezeteknek, amelyek rendelkeznek a helyi rendszergazdai fiókok, tekintse meg az útmutató a helyszíni és hibrid privilegizált hozzáférési jogosultsága az Active Directory, a felügyelt [emelt szintű hozzáférés biztonságossá tétele](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Az útmutató elsősorban az Azure Active Directory szolgáltatások, Azure Active Directory Premium tervek P1 és P2 szereplő hivatkozik. Az Azure Active Directory Premium P2 az EMS E5 csomag és a Microsoft 365 E5 csomag megtalálható. Ez az útmutató feltételezi, hogy a szervezet már rendelkezik Azure AD Premium P2 ügynöklicenceket, a felhasználók számára. Ha nem rendelkezik a licencek, a útmutatást némelyike nem feltétlenül vonatkozik az a szervezet. Ez a cikk teljes globális rendszergazda kifejezés (vagy globális rendszergazdai) is azonos a "vállalati rendszergazda" vagy "Bérlői rendszergazda."

## <a name="develop-a-roadmap"></a>A terv kidolgozása 

A Microsoft azt javasolja, fejleszthet, és kövesse a rendszerjogosultságú hozzáférés ellen, számítógépes terv. A terv a meglévő képességek és a konkrét követelmények, a szervezeten belüli mindig módosíthatja. A terv minden szakasza kell tennie, a költségek és nehezen az ellenfelek támadható meg a helyszíni, cloud és hibrid eszközök privilegizált hozzáférési jogosultsága. A Microsoft azt javasolja, hogy a következő négy terv fázisból áll: Ez ajánlott terv ütemezések a leghatékonyabb és a leggyorsabb megvalósítások először számítógépes-támadás incidens és válasz megvalósítása a Microsoft élmény alapján. A terv vonatkozóan hozzávetőleges.

![A terv idő vonallal szakaszainak](./media/admin-roles-best-practices/roadmap-timeline.png)

* Szakasz 1 (24-48 óra): javasoljuk, hogy kritikus elemekre tegye azonnal

* (2 – 4 hét) 2. szakaszra: a leggyakrabban használt támadás technikák csökkentése

* (1 – 3 hónapos) 3. szakaszra: hozza létre az látható, és teljes körű hozzáférést engedélyezzenek a felügyeleti tevékenység létrehozása

* 4. fázis (hat hónapos megalapozása): folytatni a biztonsági platform további támogatnia kell a védelmekkel felépítése

A terv keretében maximalizálhatja a már telepített Microsoft-technológiák a célja. Is kulcs jelenlegi és jövőbeli biztonsági technológiák előnyeit, és már telepítették, vagy a telepítése összhangban vannak más gyártóktól származó biztonsági eszközök integrálhatja. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>1. fázis: Javasoljuk, hogy kritikus elemekre tegye azonnal

![– 1. fázis](./media/admin-roles-best-practices/stage-one.png)

A terv 1 szakasza gyors és könnyű, kritikus feladatok összpontosít. Azt javasoljuk, hogy néhány elemekhez rögtön az első 24-48 órán belül egy alapszintű biztonságos privilegizált hozzáféréshez biztosításához belül hajthatja végre. Az emelt szintű hozzáférés biztonságossá terv ezen szakasza tartalmazza a következő műveleteket:

### <a name="general-preparation"></a>Általános előkészítése

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management bekapcsolása

Ha már nincs bekapcsolva az Azure AD Privileged Identity Management (PIM), ehhez az üzemi-bérlőben. A Privileged Identity Management bekapcsolása után értesítést kap e-mailek a privilegizált hozzáférés érdekében szerepkör módosításokat. Ha további felhasználót adnak hozzá magas szintű jogosultságokat igénylő szerepköröket a könyvtárban, ezek az értesítések korai figyelmeztető adja meg.

Az Azure AD Privileged Identity Management Azure AD Premium P2- vagy EMS E5 is tartalmaz. Ezek a megoldások segítségével védelmet alkalmazásokhoz és erőforrásokhoz való hozzáférés teljes a helyszíni környezet és a felhőben. Ha még nem rendelkezik Azure AD Premium P2- vagy EMS E5, és nem szeretné kiértékelni több funkciót, a terv hivatkozik, iratkozzon fel a [Enterprise Mobility + Security 90 napos ingyenes próbaverzió](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Használja a licenc kísérletek Azure AD Privileged Identity Management és az Azure AD Identity Protection figyelése tevékenység speciális biztonsági jelentés, naplózásához és értesítések az Azure AD használatával.

Miután van kapcsolva az Azure AD Privileged Identity Management:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) egy olyan fiókkal, amely a termelési bérlő globális rendszergazdája.

2. A bérlő, ahol szeretné használni a Privileged Identity Management kiválasztásához jelölje be a felhasználónevet a az Azure-portál jobb felső sarkában található.

3. Válassza ki **minden szolgáltatás** és a tulajdonságlista szűréséhez **Azure AD Privileged Identity Management**.

4. Nyissa meg a Privileged Identity Management a **minden szolgáltatás** listán, és rögzítheti az irányítópulton való rögzítéséhez.

Az első, aki Azure AD Privileged Identity Management az Ön bérelt szolgáltatásának használatára lesz automatikusan hozzárendelve a **biztonsági rendszergazda** és **kiemelt szerepkörű rendszergazda** szerepkörök a bérlőben. Csak a kiemelt szerepkörű rendszergazda kezelheti az Azure Active directory szerepkör-hozzárendelések a felhasználók. Azure AD Privileged Identity Management adásakor is, a biztonság varázsló, amely végigvezeti a kezdeti felderítés és a hozzárendelés élmény látható vannak. Ekkor további módosítások nélkül kiléphet a varázslóból. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Azonosítsa és a kiemelt jogosultságokkal rendelkező szerepkörök fiókok kategorizálása 

Azure AD Privileged Identity Management bekapcsolása, után a felhasználók a címtár szerepkörök globális rendszergazdája, a kiemelt szerepkörű rendszergazda, az Exchange Online rendszergazdai és a SharePoint Online felügyeleti megtekintése. Ha a bérlő nem rendelkezik Azure AD PIM, használhatja a [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). A globális rendszergazdai szerepkörrel rendelkező indítható el, mert a szerepkör általános: egy a rendszergazdai szerepkörrel felruházott felhasználó ugyanazokkal az engedélyekkel rendelkezik, amelynek a szervezet kér le, függetlenül attól, hogy azok rendelt Önhöz ezt a szerepkört az Office 365 portál összes felhőszolgáltatás , az Azure portál, vagy Microsoft PowerShell Azure AD modul használatával. 

Távolítsa el a fiókokat, amelyek már nincs szükség a ezeket a szerepköröket, és a megmaradt fiókokat, rendszergazdai szerepkörök rendelt kategorizálása:

* Külön-külön rendelt rendszergazdai felhasználók, és nem felügyeleti célú (például személyes e-mailhez) is használható
* Külön-külön rendelt rendszergazdai felhasználók számára, és kijelölt csak felügyeleti célokra
* Több felhasználó között megosztott
* Skálamegszakítás üveg vészhelyzeti hozzáférés-forgatókönyvek esetén
* Az automatizált parancsfájlokat
* A külső felhasználók számára

#### <a name="define-at-least-two-emergency-access-accounts"></a>Legalább két vészhelyzeti hozzáférés fiók megadása 

Győződjön meg arról, hogy nem jelenik meg az olyan helyzet, ahol azok sikerült véletlenül zárolni a bejelentkezéshez, vagy egy meglévő egyéni felhasználói fiók rendszergazdai aktiválása miatt nem az Azure AD bérlője felügyeleti kívül. Például ha a szervezet össze van vonva egy a helyszíni identitás-szolgáltatóhoz, a identitásszolgáltató esetleg nem érhető el, a felhasználók nem jelentkezhetnek be a helyszíni. Két vagy több vészhelyzeti fiókok tárolása a bérlő mérséklésére vonatkozó útmutatások a rendszergazdai hozzáférés véletlen hiánya hatását.

Vészhelyzeti fiókok segítségével a szervezetek korlátozni a rendszerjogosultságú hozzáférést egy meglévő Azure Active Directory-környezeten belül. Ezek a fiókok magas szintű jogosultságokkal, és konkrét személyek nincsenek hozzárendelve. Vészhelyzeti fiókok korlátozódnak vészhelyzeti "vészhelyzeti" forgatókönyvek, ahol a normál rendszergazdai fiókok nem használható. A szervezetek biztosítania kell vezérlése, és csak amelyek esetében szükséges idő csökkentése a vészhelyzeti fiók használati céljából. 

Értékelje ki a fiókokat, amelyek hozzárendelt vagy abban az esetben jogosult a globális rendszergazdai szerepkörrel. Ha itt nem talál a csak felhőalapú fiókok a *. onmicrosoft.com tartományt (célja, hogy az "vészhelyzeti" vészhelyzeti hozzáférés), hozza létre a címzetteket. További információkért lásd: [vészhelyzeti hozzáférés rendszergazdai fiókok kezelése az Azure AD](active-directory-admin-manage-emergency-access-accounts.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Kapcsolja be a multi-factor authentication és más magas jogosultsági szintű egyfelhasználós nem összevont rendszergazdai fiókok regisztrálása 

Bejelentkezéskor a véglegesen legalább egy, az Azure AD rendszergazdai szerepkörök rendelt minden egyes felhasználók a Azure multi-factor Authentication (MFA) megkövetelése: globális rendszergazda, a kiemelt szerepkörű rendszergazda, az Exchange Online rendszergazdai és a SharePoint Online rendszergazdai. Az útmutató használatával engedélyezhető az [multi-factor Authentication (MFA) a rendszergazdai fiókokhoz](authentication/howto-mfa-userstates.md) , és győződjön meg arról, hogy ezek a felhasználók a regisztrált [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). További információ a 2. lépés és az útmutató 3. lépés [adatokhoz és szolgáltatásokhoz az Office 365-ben való hozzáférés](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>2. fázis: A leggyakrabban használt támadás technikák csökkentése

![2. szakaszra](./media/admin-roles-best-practices/stage-two.png)

A terv 2. szakaszra a leggyakrabban használt támadás technikák hitelesítő adatokkal való visszaéléseket és visszaélés kiküszöböléséhez összpontosít, és arra tervezték, hogy körülbelül 2 – 4 hét kell végrehajtani. Az emelt szintű hozzáférés biztonságossá terv ezen szakasza a következő műveleteket foglalja magában.

### <a name="general-preparation"></a>Általános előkészítése

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>Végezze el a leltárt, szolgáltatások, a tulajdonosok és a rendszergazdák

Bring your-saját eszközök (használata BYOD) és a munka az otthoni házirendekben, mind a vezeték nélküli kapcsolat a vállalkozások növekedésére nő rendkívül fontos, hogy nyomon, hogy ki csatlakozik a hálózathoz. Hatékony biztonsági naplózási gyakran azt jelzi, eszközök, alkalmazások és a hálózaton, amely nem támogatja a futó programok informatikai, ezért potenciálisan nem biztonságos. További információkért lásd: [Azure biztonsági kezelési és figyelési áttekintés](../security/security-management-and-monitoring-overview.md). Győződjön meg arról, hogy megadja a következő feladatokat a leltározási folyamat során. 

* Rendszergazdai szerepkörök és szolgáltatások rendelkezik, ahol azok kezelhetik a felhasználók azonosítása.
* Azure AD PIM segítségével megtudhatja, mely a szervezet elérhetik a rendszergazda az Azure AD, beleértve a további szerepkörök meghaladja az 1. lépés.
* A szerepkörök Azure AD-ben definiált, túl Office 365 tartalmaz egy rendszergazdai szerepkörök, amelyeket hozzárendelhet a felhasználók számára a szervezetében. Minden rendszergazdai szerepkörhöz általános üzleti funkciók van leképezve, és személyek adja meg a szervezet engedélyek elvégezhető feladatokat az Office 365 felügyeleti központban. Az Office felügyeleti központ segítségével megtudhatja, mely a szervezet elérhetik a rendszergazda az Office 365 szolgáltatásra, beleértve az Azure AD-ben nem kezelt szerepkörök keresztül. További információkért lásd: [Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) és [ajánlott biztonsági eljárások az Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Hajtsa végre a készlet más szolgáltatásokkal, a szervezet támaszkodik, például az Azure, az Intune-ban vagy a Dynamics 365.
* Győződjön meg arról, hogy a rendszergazdai fiókok (fiókjait, amelyek felügyeleti célokra, nem csak a felhasználói napi szintű fiókok) működik-e e-mail címek kapcsolódik és az Azure MFA szolgáltatásra regisztrált vagy használjon helyszíni többtényezős Hitelesítést.
* Kérje meg a felhasználókat a saját üzleti indoklásának rendszergazdai hozzáféréshez.
* Távolítsa el a rendszergazdai hozzáférés olyan egyéni felhasználók számára és a szolgáltatásokat, nincs szükség.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>A munkahelyi vagy iskolai fiókok kapcsolható igénylő rendszergazdai szerepkörei Microsoft-fiókok azonosítása 

Egyes esetekben egy szervezet számára a kezdeti globális rendszergazdák felhasználhatja a meglévő Microsoft-fiók hitelesítő, amikor azok megkezdte az Azure AD. Ezek a Microsoft-fiókok a felhőalapú vagy a szinkronizált fiókokat kell helyettesíteni. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Győződjön meg arról, külön felhasználói fiókok és a globális rendszergazdai fiókok továbbítás e-maileket 

Globális rendszergazdai fiók nem rendelkezhet személyes e-mail-címeket, amennyi személyes e-mail fiókok rendszeresen számítógépes támadók phished. Külön internet kockázatok segítségével (adathalász támadások, szándékos webböngészés) rendszergazdai jogosultságokkal, hozzon létre minden felhasználóhoz külön fiókok rendszergazdai jogosultságokkal. 

Ha még nem tette meg, a felhasználók számára a globális rendszergazdai feladatok végrehajtása, annak ellenőrzése, akaratlanul nem nyissa meg az e-maileket vagy futtathatja a programokat, a rendszergazdai fiókokhoz tartozó külön fiókokat létrehozni. Győződjön meg arról, hogy ezek a fiókok rendelkezik működő postafiók továbbítani az e-maileket.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Győződjön meg arról, rendszergazdai fiókok jelszavait nemrég módosította

Győződjön meg arról, hogy minden felhasználó a rendszergazdai fiókok be van jelentkezve és az utolsó 90 napban legalább egyszer a jelszavukat módosítani. Emellett győződjön meg arról, hogy mely több felhasználók olyan megosztott fiók a jelszó ismerete futtatta a nemrég módosította jelszavát.

#### <a name="turn-on-password-hash-synchronization"></a>Kapcsolja be a Jelszókivonat-szinkronizálást

Jelszókivonat-szinkronizálást egy olyan funkció, lehet szinkronizálni a kivonatokat a felhasználói jelszó-kivonatok a helyszíni Active Directory-példányról egy felhőalapú Azure AD-példányban. Még akkor is, ha az Active Directory összevonási szolgáltatások (AD FS) vagy az egyéb identitás-szolgáltatóktól összevonási használata mellett dönt, akkor opcionálisan beállítása Jelszókivonat-szinkronizálást biztonsági esetében a helyszíni infrastruktúrát, például AD vagy AD FS-kiszolgáló sikertelen vagy válik átmenetileg nem érhető el. Ez lehetővé teszi, hogy a felhasználók jelentkezhetnek be a szolgáltatás által ugyanazzal a jelszóval, amelyekkel jelentkezzen be a helyszíni AD-példányban. Emellett lehetővé teszi Identity Protection hitelesítő adatokkal való visszaélés észlelése ezen jelszókivonatait összehasonlítva az ismert megsértik, ha a felhasználó rendelkezik kihasználhatók a ugyanazt az e-mail címet és más szolgáltatások nem csatlakozott az Azure AD-jelszó jelszavakat.  További információkért lásd: [Jelszókivonat-szinkronizálást és az Azure AD Connect-szinkronizálás megvalósítása](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Többtényezős hitelesítés (MFA) igényelnek a felhasználók minden kiemelt szerepkört és a kitett felhasználók

Az Azure AD azt javasolja, hogy szükséges-e többtényezős hitelesítés (MFA) minden felhasználó, többek között a rendszergazdák és az összes többi felhasználója jelentős hatással lenne, ha a fiók biztonsági szempontból sérült (például pénzügyi tisztviselő). Ez csökkenti a sérült biztonságú jelszó miatt a támadás kockázatát.

Kapcsolja be:

* [Magas-közzétételi fiók MFA](authentication/multi-factor-authentication-security-best-practices.md) például egy szervezet vezetői tisztviselő tartozó fiókok 
* [Minden rendszergazdai fiókot az MFA egy adott felhasználóhoz társított](authentication/howto-mfa-userstates.md) az SaaS-alkalmazásokhoz kapcsolódó egyéb 
* Többtényezős hitelesítés az összes rendszergazda a Microsoft SaaS-alkalmazások, többek között a Rendszergazdák szerepkör felügyelt az Exchange Online és az Office portálon

Használatakor a Windows Hello for Business, a többtényezős hitelesítés követelménye érheti el, használja a Windows Hello bejelentkezési élményt nyújt. További információkért lásd: [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Az Identity Protection konfigurálása 

Az Azure AD Identity Protection, az algoritmus-alapú figyelési és jelentéskészítési eszköz, amely segítségével a szervezet identitásait érintő lehetséges biztonsági rések észlelése. Automatikus válaszok e észlelt gyanús tevékenységek konfigurálja, és a megfelelő művelettel hárítsa el őket. További információkért lásd: [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Szerezze be az Office 365-biztonságos pontszám (ha az Office 365-tel)

Biztonságos pontszám adatok milyen Office 365-szolgáltatásokhoz (például a OneDrive, SharePoint és Exchange) használ, akkor ellenőrzi, hogy a beállításokat és a tevékenységek és az összehasonlítja azokat a Microsoft által létrehozott egy alapkonfigurációt ki. Hogyan igazított áll az ajánlott biztonsági eljárásokkal alapján pontszámot kap. Bárki, aki rendszergazdai jogosultságokkal (globális rendszergazda vagy egy egyéni rendszergazda szerepkör) rendelkezik egy olyan Office 365 vállalati prémium verzió vagy nagyvállalati előfizetéssel férhetnek hozzá a következő biztonságos pontszám a [ https://securescore.office.com ](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Olvassa el az Office 365 biztonsági és megfelelőségi útmutatást (ha az Office 365-tel)

A [biztonsági és megfelelőségi tervezése](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) arra, hogy Office 365-felhasználó miként kell konfigurálni az Office 365 és más EMS lehetőségeket kihasználhatja a megközelítést ismerteti. Ezután tekintse át lépések 3-6 bemutatja, hogyan [adatokhoz és szolgáltatásokhoz az Office 365-ben való hozzáférés](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) és az Útmutató arról, hogyan [figyelheti a biztonsági és megfelelőségi az Office 365-ben](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurálja az Office 365 figyelése (ha az Office 365-tel)

Megfigyelheti, hogyan a szervezet dolgozói használják Office 365-szolgáltatásokhoz, amely lehetővé teszi a felhasználók rendszergazdai fiókokkal rendelkező, és akik nem szükséges Office 365 elérhessék miatt nem jelentkezik be azokat a kapukat azonosítása. További információkért lásd: [tevékenység jelentéseit az Office 365 felügyeleti központjának](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Incidens/sürgős válasz terv tulajdonosok létrehozása

Incidensválasz hatékonyan végez egy összetett vállalkozás. Ezért egy sikeres incidensválasz képesség létrehozó szükséges jelentős tervezési és erőforrásokat. Fontos, hogy folyamatosan figyelje a számítógépes-támadások, és az incidensek kezelése rangsorolása eljárásokat. Hatékony gyűjtése, elemzése, és az adatok módszereket nélkülözhetetlen hozhat létre kapcsolatokat, és létrehozza a kommunikációt a más belső csoportokkal, és tervezze meg tulajdonosai. További információkért lásd: [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Biztonságos helyszíni emelt szintű rendszergazdai fiókok, ha még nincs kész

Ha az Azure Active Directory-bérlő szinkronizálni kell a helyszíni Active Directory szolgáltatással, majd kövessék [biztonsági Rendszerjogosultságú hozzáférés terv](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): 1. lépés. Ebbe beletartozik a felhasználók, akik a helyi felügyeleti feladatok elvégzésére jogosultsági szintű hozzáféréssel rendelkező munkaállomások telepítése az Active Directory rendszergazdák, és a munkaállomások egyedi helyi rendszergazdai jelszó létrehozása a külön rendszergazdai fiókok létrehozása és kiszolgálók.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>A szervezet Azure történő hozzáférés felügyeletéhez további lépései

#### <a name="complete-an-inventory-of-subscriptions"></a>Végezze el az előfizetések leltár

A vállalati portál és az Azure-portál használatával azonosíthatja az előfizetéseket a szervezet éles alkalmazásokat. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Távolítsa el a Microsoft-fiókok a rendszergazdai szerepkörök

Microsoft-fiókok más programoktól, például az Xbox Live és az Outlook nem használható rendszergazda fiókok szervezeti előfizetések. Felügyeleti állapot eltávolítása az összes Microsoft-fiókkal, és cserélje le az Active Directory (például chris@contoso.com) munkahelyi vagy iskolai fiókok.

#### <a name="monitor-azure-activity"></a>Az Azure figyelése

Az Azure tevékenységnapló Azure előfizetés-szintű események előzményeit biztosítja. Aki létrehozása, frissítése, és törölni milyen erőforrásokat, és ezek az események előfordulásakor kínál. További információkért lásd: [naplózási és az Azure-előfizetéssel kapcsolatos fontos műveletek értesítéseket](../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépéseket a szervezet más Azure AD felhőszolgáltatásokhoz való hozzáférés kezelése 

#### <a name="configure-conditional-access-policies"></a>Feltételes hozzáférési házirendek konfigurálása

A helyszíni és felhőben futtatott alkalmazások feltételes hozzáférési házirendjeinek az előkészítéséhez. A felhasználók munkahelyhez csatlakoztatott eszközök van, ha további információért [beállítása a helyszíni feltételes hozzáférés használata az Azure Active Directory eszközregisztrációs szerint](active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>3. fázis: Build látható, és teljes mértékben vezérelhetik a felügyeleti tevékenység

![3. szakaszra](./media/admin-roles-best-practices/stage-three.png)

3. szakaszra szakasz 2 a megoldást épül, és arra tervezték, hogy körülbelül 1 – 3 hónapon belül kell végrehajtani. Az emelt szintű hozzáférés biztonságossá terv ezen szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítése

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Fejezze be a rendszergazdai szerepkörök a felhasználók egy áttekintése

Több vállalati felhasználók kiemelt hozzáférjenek vannak felhőszolgáltatások, ami egy növekvő nem felügyelt platformon keresztül. Ez magában foglalja a felhasználók Office 365, Azure-előfizetés rendszergazdák és a virtuális gépek vagy SaaS-alkalmazások segítségével rendszergazdai hozzáféréssel rendelkező felhasználók számára a globális rendszergazdák váljon. Ehelyett a szervezetek kell rendelkeznie minden alkalmazott, különösen a rendszergazdák magas jogosultságszinttel nem rendelkező felhasználóként napi üzleti tranzakciók kezelése, és csak végrehajtása a rendszergazdai jogosultságokkal, igény szerint. Rendszergazdai szerepkörök a felhasználók száma a kezdeti bevezetési óta száma értékük kiadásról, mivel teljes hozzáférést ellenőrzi, hogy azonosítható, és erősítse meg a minden felhasználó jogosult-e rendszergazda jogosultságokkal aktiválásához. 

Tegye a következőket:

* Meghatározására, hogy mely felhasználók az Azure AD rendszergazdák, engedélyezze az igény, közvetlenül az időponthoz kötött rendszergazdai hozzáférés és szerepköralapú biztonsági vezérlők.
* Konvertálás nem egyértelmű indokolt, egy másik szerepkör emelt szintű rendszergazdai hozzáféréssel rendelkező felhasználók (Ha nem jogosult szerepkört, távolítsa el őket).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Bevezetés az összes felhasználó erősebb hitelesítési folytatása 

Szükséges C-csomag vezetők, magas szintű kezelők, a kritikus informatikai és biztonsági csoporthoz, és más magas kitett felhasználók a modern, erős hitelesítés, például az Azure MFA vagy a Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Dedikált munkaállomások használja az Azure AD felügyeleti célokra

A támadók megkísérli ahhoz, hogy hozzáférjenek egy szervezet adatokhoz és rendszerek, így azok megszakíthatja az integritásra és a rosszindulatú kódot, amely megváltoztatja a program programot, vagy a rendszergazdai hitelesítő adatok megadása snoops, az adatok hitelességét kiemelt jogosultságú fiókok célként. Jogosultsági szintű hozzáféréssel rendelkező munkaállomások (láb) kényes feladatokhoz, amely védett az Internet támadások és fenyegetési vektoroknak enged utat adjon meg egy dedikált operációs rendszert. Ezekről a feladatokról bizalmas és fiókok mappától a napi használja a munkaállomások és eszközök nagyon erős védelmet nyújt az adathalász támadások, alkalmazás és az operációs rendszer biztonsági rések, különböző megszemélyesítési támadásokkal szemben, és hitelesítő adatokkal való visszaéléseket támadások ellen, mint a billentyűlenyomást a Pass-the-Hash és Pass-The-Ticket naplózása. Szintű hozzáféréssel rendelkező munkaállomások üzembe helyezésével, csökkentheti annak kockázatát, hogy rendszergazdák meg rendszergazdai hitelesítő adatokat, kivéve egy asztali környezetben, amely rendelkezik lettek megerősítve. További információkért lásd: [jogosultsági szintű hozzáféréssel rendelkező munkaállomások](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Tekintse át a nemzeti szabványügyi és technológiai javaslatok incidensek kezelése 

A National Institute of Standards and Technology meg (NIST) útmutatást nyújt a incidensek kezelése, különösen az incidens kapcsolatos adatok elemzése és minden incidens megfelelő válasz meghatározása. További információkért lásd: [(NIST) a számítógép biztonsági incidens kezelése útmutató (SP 800-61, változat 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Privileged Identity Management (PIM) valósítja meg az igény szerinti további felügyeleti szerepkörökhöz

Az Azure Active Directory használata [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) funkció. Időben korlátozott aktiválásának kiemelt szerepköröket működik, mivel lehetővé teszi, hogy:

* Egy adott feladat végrehajtásához a rendszergazda jogosultságokkal aktiválása
* Többtényezős hitelesítés kényszerítése az aktiválási folyamat során
* Rendszergazdák tájékoztatja a sávon kívüli változásaira vonatkozó értesítések segítségével
* Szeretné megőrizni a előre beállított időn néhány jogosultsággal, a felhasználók engedélyezése
* Lehetővé teszi a biztonsági rendszergazdák számára, hogy az összes kiemelt jogosultságú identitások ismerje meg, naplózási jelentések megtekintése és hozzáférési értékelést minden felhasználó jogosult-e rendszergazda jogosultságokkal aktiválásához azonosítására létrehozása

Ha már használja az Azure AD Privileged Identity Management, úgy dönt, az időhöz kötött jogosultságokkal korlátozta, szükség esetén (például a karbantartási időszakok).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Határozza meg a lehetősége, hogy a jelszó alapú bejelentkezési protokollok (ha az Exchange Online használata)

A múltban protokollok feltételezi, hogy a felhasználónév/jelszó kombináció beágyazott volt az eszközök, az e-mail fiókok, a telefonok és a stb. De most a felhőben számítógépes-támadások kockázatát, javasolt minden lehetséges felhasználó, aki, ha a hitelesítő adataik feltörték, is katasztrofális a szervezet számára, és nem tudnak bejelentkezni az e-mailek felhasználónév keresztül kizárja őket azonosítása / a jelszó erős hitelesítési követelmények és a feltételes hozzáférés alkalmazásával. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Végezze el a szerepkörök felülvizsgálati értékelése az Office 365 szerepkörök (ha az Office 365-tel)

Minden rendszergazda felhasználók vannak a megfelelő szerepkör értékeléséhez (törlése és újbóli hozzárendelése az értékelés szerint).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Tekintse át a biztonsági incidenskezelés megközelítésben az Office 365-ben, és hasonlítsa össze a saját szervezethez

Ez a jelentés letöltheti [biztonsági incidens kezelése a Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Továbbra is a helyszíni emelt szintű rendszergazdai fiókok biztosításában

Ha az Azure Active Directory a helyszíni Active Directoryhoz van csatlakoztatva, majd kövessék a [biztonsági Rendszerjogosultságú hozzáférés terv](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): folyamat 2. Ez magában foglalja a jogosultsági szintű hozzáféréssel rendelkező munkaállomások telepítése az összes rendszergazda, többtényezős hitelesítés megkövetelése, csak elég Admin használ tartományvezérlő karbantartása, így csökkenti a támadási felületet, tartományok, támadások észlelését az ATA üzembe helyezésének.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>A szervezet Azure történő hozzáférés felügyeletéhez további lépései

#### <a name="establish-integrated-monitoring"></a>Integrált figyelő létrehozása

A [az Azure Security Center](../security-center/security-center-intro.md) biztosít integrált biztonsági monitorozást és az Azure-előfizetések, segítséget nyújt az egyébként lépjen észrevétlenül fenyegetéseket, és számos biztonsági együttműködik megoldások.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Készítsen leltárt a futtatott virtuális gépek belül a kiemelt jogosultságú fiókok

A legtöbb esetben nem kell megadnia a felhasználók korlátlan engedélyeket az Azure-előfizetések és erőforrásokhoz. Használhatja az Azure AD rendszergazdai szerepkörök feladataik elkülönítse a szervezeten belül, és csak olyan mértékű hozzáférést biztosítania a felhasználók, akik adott feladatok elvégzéséhez. Például az Azure AD rendszergazdai szerepkörök használatával lehetővé teszik egy rendszergazda egy előfizetéshez csak virtuális gépek kezeléséhez, amíg egy másik kezelheti az SQL-adatbázisok egyazon előfizetésen belül. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure-portálon az első lépései](../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>A rendszergazda szerepkörhöz az Azure AD PIM megvalósítása

Rendszergazdai szerepkörök az Azure AD Privileged identity Management használatával kezelése, szabályozása és figyelése az Azure-erőforrások eléréséhez. A PIM használatával védi kiemelt jogosultságú fiókok számítógépes-támadások jogosultságokat a kitettség idő csökkentése és a használatukat keresztül jelentéseket és riasztásokat láthatósága növelését. További információkért lásd: [Privileged Identity Management Azure-erőforrások kezelése Szerepalapú hozzáférés](../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure naplóelemzés integrációja a is elküldhetik a megfelelő Azure naplóit a SIEM-rendszerekről 

Azure naplóelemzés integrációja lehetővé teszi a szervezet meglévő biztonsági információk és esemény felügyeleti SIEM-rendszereket az Azure-erőforrások származó nyers naplók integrálhatja. [Azure naplóelemzés integrációs](../security/security-azure-log-integration-overview.md) Windows-eseményeket gyűjti össze a Windows Eseménynapló naplókat, és az Azure-erőforrások az Azure tevékenységi naplóit, az Azure Security Center riasztásait és az Azure diagnosztikai naplókat. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépéseket a szervezet más Azure AD felhőszolgáltatásokhoz való hozzáférés kezelése

#### <a name="implement-user-provisioning-for-connected-apps"></a>A felhasználók átadása a csatlakoztatott alkalmazásoknál megvalósítása

Az Azure AD lehetővé teszi a létrehozása, a karbantartási és a felhasználói identitások felhőalapú (SaaS) alkalmazások, például a Dropbox, Salesforce, ServiceNow eltávolítása, és így tovább. További információkért lásd: [felhasználói kiépítésének és megszüntetésének biztosítása az SaaS-alkalmazásokhoz az Azure ad szolgáltatással automatizálhatja](active-directory-saas-app-provisioning.md).

#### <a name="integrate-information-protection"></a>Adatvédelem integrálása

MCAS lehetővé teszi fájlok vizsgálata, és állítsa be a házirendek alapján az Azure Information Protection besorolási címkék, engedélyezése, és nagyobb szabályozhatja az adatok a felhőben. Vizsgálat és a felhőben található fájlokat besorolhatja, és alkalmazza az Azure information protection címkék. További információkért lásd: [Azure Information Protection integrációs](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

Konfigurálja a feltételes hozzáférést egy csoport, helyét és az alkalmazás érzékenysége alapján [SaaS-alkalmazások](https://azure.microsoft.com/overview/what-is-saas/) és az Azure AD csatlakoztatott alkalmazásokat. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>A csatlakoztatott felhőalkalmazásban figyelése

Győződjön meg arról, egymáshoz kapcsolódó alkalmazások, valamint a védett hozzáférést, javasoljuk, hogy kihasználja [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Ez biztosítja, hogy a felhőalapú alkalmazásokba, azáltal, hogy a rendszergazdai fiókok védelme mellett a vállalati hozzáférés:

* Figyelemmel kísérelhetik és vezérelhetik a felhőalapú alkalmazások kiterjesztése
* A hozzáférés, tevékenységek és az adatok megosztása házirendek létrehozása
* Automatikus azonosítása a kockázatos tevékenységek, a rendellenes viselkedés és a fenyegetések
* Az adatszivárgás megakadályozása
* Kockázati és az automatizált fenyegetés megelőzése és a házirendek betartatását minimalizálása érdekében

A Cloud App Security SIEM-ügynök a Cloud App Security integrálható a SIEM-kiszolgáló engedélyezéséhez az Office 365-riasztások és a tevékenységek központi figyelését. Ez fut a kiszolgálón és riasztásokat és a tevékenység lekéri a Cloud App Security, és az adatfolyamokat, azokat be a SIEM-kiszolgáló. További információkért lásd: [SIEM-integráció](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>4. fázis: Folytatják a megelőző biztonságot védelmekkel


![4. fázis](./media/admin-roles-best-practices/stage-four.png)

A terv 4 szakasza a 3. lépés látható épül, és arra tervezték, hogy hat hónapban, vagy azon kívül kell végrehajtani. A terv segít strong fejlesztése befejezése emelt szintű hozzáférés védelmét a potenciális támadások ellen, manapság jelenleg ismert és érhető el. Biztonsági fenyegetések sajnos folyamatosan fejlődnek, és az eltolás mértékét megadó, ezért azt javasoljuk, hogy a biztonsági tárgyalt kiváltása a költség, és csökkenti a környezet célzó ellenfelek sikerességi aránya a folyamatban lévő folyamatként megtekintése.

Biztonságossá tétele a kiemelt hozzáférési üzleti eszközök biztonsági garanciák létrehozó modern szervezet kritikus első lépésként, de nincs a csak egy része egy teljes biztonsági programot, amely tartalmazza a elemeket, például a házirend, a műveletek, az adatokat biztonsági, kiszolgálók, alkalmazások, számítógépek, eszközök, felhőháló és egyéb összetevők adja meg a biztonság folyamatos biztosítékok. 

A privilegizált hozzáférési fiókok kezelése mellett javasoljuk, hogy tekintse át a folyamatos a következőket:

* Győződjön meg arról, hogy a rendszergazdák végzi a napi üzleti magas jogosultságszinttel nem rendelkező felhasználóként.
* Csak a rendszerjogosultságú hozzáférést, ha szükséges, és távolítsa el ezt követően (just-in-time).
* Megőrzése mellett, és tekintse át a kiemelt jogosultságú fiókok naplózási tevékenység.

Egy teljes biztonsági terv felépítésével további információkért lásd: [Microsoft cloud architektúra nagy mennyiségű informatikai erőforrásra](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Módon vegye fel a Microsoft-szolgáltatások segítik ezek a témakörök egyik bővebben a Microsoft-képviselőjére, vagy tekintse meg [összeállítása a vállalat védelme érdekében kritikus fontosságú számítógépes védelmekkel](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Ez a Rendszerjogosultságú hozzáférés biztonságossá terv folyamatban lévő utolsó szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítése

#### <a name="review-admin-roles-in-azure-active-directory"></a>Tekintse át a rendszergazdai szerepkörök az Azure Active Directoryban 

Annak eldöntése, hogy a jelenlegi beépített az Azure AD rendszergazdai szerepkörök még mindig naprakész, és biztosítsa, hogy felhasználók csak a szerepkörök és delegálásokat szükséges megfelelő engedélyek. Az Azure ad-vel a különböző funkciók kiszolgálására külön rendszergazdák is kijelölhet. További információkért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Tekintse át az Azure AD felügyeleti rendelkező felhasználók csatlakoztatott eszközök

További információkért lásd: [hogyan hibrid konfigurálása az Azure Active Directoryhoz csatlakoztatott eszközök](device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Tekintse át a tagjai [beépített Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Office 365 használatakor.
‎
#### <a name="validate-incident-response-plan"></a>Incidensválasz-terv ellenőrzése

A terv alapján javítása érdekében a Microsoft azt javasolja, rendszeresen ellenőrzi, hogy a tervben a kívánt módon működnek:

* Nyissa meg a meglévő ütemterv mi kimaradt megjelenítéséhez
* A postmortem elemzésén alapul, vizsgálja felül a meglévő vagy új ajánlott eljárások megadása
* Győződjön meg arról, hogy a frissített incidensválasz-tervnek és ajánlott eljárások terjesztése a szervezeten belül


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>A szervezet Azure történő hozzáférés felügyeletéhez további lépései 

Határozza meg, ha szükséges [egy másik fiókot az Azure-előfizetés tulajdonjogának átruházása](../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Vészhelyzeti": Mi a teendő, vészhelyzet esetén

![Vészhelyzet](./media/admin-roles-best-practices/emergency.jpeg)

1. Értesítse a kulcs kezelők és biztonsági tisztviselő vonatkozó információkkal kapcsolatban az incidens.

2. Tekintse át a támadás forgatókönyv. 

3. A "vészhelyzeti" fiók felhasználónév/jelszó kombináció bejelentkezni az Azure AD hozzáférési. 

4. Kérjen segítséget a Microsoft által [megnyitása az Azure támogatási kérelmet](../azure-supportability/how-to-create-azure-support-request.md).

5. Tekintse meg a [az Azure AD-bejelentkezés jelentések](active-directory-reporting-azure-portal.md). Előfordulhat, hogy a késés közötti esemény lépett fel, és a jelentésben szerepel.

6. A hibrid környezetek, ha összevont és az AD FS-kiszolgáló nem érhető el, szükség lehet az összevont hitelesítés használatára a Jelszókivonat-szinkronizálás ideiglenesen váltani. Ez visszaállítja a tartomány-összevonás vissza a felügyelt hitelesítési mindaddig, amíg az AD FS-kiszolgáló elérhetővé válik.

7. E-mailek kiemelt jogosultságokkal rendelkező fiókok figyelése.

8. Ellenőrizze, hogy a potenciális Törvényszéki és jogi vizsgálat vonatkozó naplóinak biztonsági mentések menti.

Hogyan kezeli a Microsoft Office 365 a biztonsági eseményekre vonatkozó további információkért lásd: [biztonsági incidens kezelése a Microsoft Office 365](http://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Gyakran ismételt kérdések: Gyakori kérdések érkező vonatkozó emelt szintű hozzáférés biztonságossá tétele  


**K:** Mit tegyek, ha még I még nem használható a biztonságos hozzáférést összetevők?

**Válasz:** MFA adjon meg a rendszergazdai jogosultsággal rendelkező fiókokhoz, és külön felhasználói fiókok globális rendszergazdai fiókok, legalább két break-vészhelyzeti fiók megadása.


**K:** után a biztonsági szabályok megsértésére, mi az a felső problémát kell először látott?

**Válasz:** mindenképpen magas kitett egyéni felhasználók számára a legerősebb hitelesítés most megkövetelése.


**K:** mi történik, ha a jogosultsággal rendelkező rendszergazdák inaktiválták?

**Válasz:** hozzon létre egy globális rendszergazdai fiókot, amely a rendszer mindig naprakészek.


**K:** mi történik, ha csak egy globális rendszergazdai balra, és azok nem érhető el? 

**Válasz:** azonnali privilegizált hozzáférést a break-vészhelyzeti fiókok valamelyikével.


**K:** miként védhetők rendszergazdák a szervezeten belül?

**Válasz:** mindig a normál "jogosultságú" felhasználóként napi üzleti tegye rendszergazdák rendelkeznek.
 

**K:** Mik a felügyeleti fiókok létrehozása az Azure AD belül ajánlott eljárásai?

**Válasz:** tartalék emelt szintű hozzáférés meghatározott felügyeleti feladatokhoz.


**K:** milyen eszköz létezik a csökkentése állandó rendszergazdai hozzáférés?

**Válasz:** Privileged Identity Management (PIM) és az Azure AD rendszergazdai szerepkörök.


**K:** Mi az az Azure AD rendszergazdai fiókok szinkronizálása a Microsoft pozíciója?

**Válasz:** 0. rétegbeli rendszergazdai fiókok (beleértve a fiókok, csoportok és más eszközök közvetlen vagy közvetett felügyeletet az Active Directory-erdőben, a tartományok és a tartományvezérlők és az összes eszköz) ki van használva, csak a helyszíni AD-fiókok és a általában nem szinkronizált az Azure AD a felhőben. 


**K:** hogyan tegye azt megtartása rendszergazdák a portálon véletlenszerű rendszergazdai hozzáférés hozzárendelése?

**Válasz:** rendszerjogosultsággal nem rendelkező fiókok használata a minden felhasználó és a legtöbb rendszergazda. Indítsa el a szükséges a szervezet számára, hogy melyik néhány rendszergazdai fiókot kell privilegizált egy erőforrást. És figyelje az újonnan létrehozott felügyeleti felhasználók.


## <a name="next-steps"></a>További lépések

* [A Microsoft Trust Center termék biztonsági](https://www.microsoft.com/en-us/trustcenter/security) – biztonsági funkciói a Microsoft cloud termékei és szolgáltatásai

* [A Microsoft Trust Center - megfelelőségi](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) – a Microsoft felhőszolgáltatásai számára megfelelőségi ajánlatok átfogó gyűjteményét

* [Útmutatás kockázatbecslés](https://www.microsoft.com/en-us/trustcenter/guidance/risk-assessment) -kezelése a Microsoft biztonsági és megfelelőségi követelményei

### <a name="other-ms-online-services"></a>Más MS Online szolgáltatások 

* [A Microsoft Intune biztonsági](https://www.microsoft.com/en-us/trustcenter/security/intune-security) – az Intune mobileszköz-kezelés, a mobilalkalmazás-felügyelet és a számítógépek felügyeletére szolgáló képességei a felhőből kínál.

* [A Microsoft Dynamics 365 biztonsági](https://www.microsoft.com/en-us/trustcenter/security/dynamics365-security) – Dynamics 365 a Microsoft felhőalapú megoldás egyesíti, Ügyfélkapcsolat-kezelés (CRM) és a vállalati erőforrás-tervező (ERP) képességeket.

 
