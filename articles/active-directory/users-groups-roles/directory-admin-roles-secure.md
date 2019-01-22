---
title: Ajánlott eljárások az Azure AD felügyeleti hozzáférés biztonságossá tétele |} A Microsoft Docs
description: Győződjön meg arról, hogy a szervezet felügyeleti hozzáférés és a rendszergazdai fiókok legyenek. A rendszer tervezők és informatikusok, akik konfigurálása az Azure AD-ben az Azure és a Microsoft Online Services.
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
ms.openlocfilehash: c2ee0834b0c6872d96bebe71231f1a9424ff261a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438150"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Az Azure AD felhőalapú és hibrid telepítések emelt szintű hozzáférés biztonságossá tétele

A legtöbb vagy az összes üzleti eszköz biztonsága az a modern szervezet kiemelt jogosultságú fiókok felügyelheti és kezelheti az informatikai rendszerek biztonságának függ. Gyakran többek között az internetes támadók rosszindulatú actors célozhat meg rendszergazdai fiókok, és gyorsan hozzáférhessenek a bizalmas adatok és rendszerek a hitelesítő adatok ellopására irányuló támadásokkal próbál emelt szintű hozzáférés más elemeit. Cloud services, a megelőzési és a válaszok a közös felelősséget a felhőbeli szolgáltató és az ügyfél a. A végpontok és a felhő legújabb fenyegetések kapcsolatos további információkért lásd: a [a Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report). Ez a cikk segítséget nyújt a fejlesztés egy ütemtervet felé bezárása között az aktuális csomagok és az itt ismertetett útmutatást a hiányosságok pótlásában.

> [!NOTE] 
> A Microsoft elkötelezett a legmagasabb szintű megbízhatóság, átláthatóság, szabványoknak és előírásoknak való megfelelés. További információ hogyan csökkenti az a Microsoft globális incidenskezelő csapata a felhőszolgáltatások elleni támadások hatásainak, és hogyan biztonsági beépített Microsoft üzleti termékeivel, és a cloud services [Microsoft Trust Center – biztonság](https://www.microsoft.com/trustcenter/security)és a Microsoft megfelelőségi célkitűzések [Microsoft Trust Center – megfelelőségi](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
A legtöbb szervezet számára az üzleti eszköz biztonsága az integritását, felügyelheti és kezelheti az informatikai rendszerek kiemelt jogosultságú fiókok függ. Internetes támadók koncentrálhat infrastruktúra rendszereket (ilyen például az Active Directory és az Azure Active Directory) privilegizált hozzáférést a szervezet bizalmas adatokhoz való hozzáférést. 

Hagyományos megközelítés, amely a bemeneti és kimeneti pontjait, az elsődleges biztonsági határ egy hálózati biztonságossá tétele a kevésbé hatékonyak miatt a SaaS-alkalmazások és a személyes eszközök használatának megnövekedhet az interneten. Egy komplex, modern vállalaton a hálózati biztonsági határ természetes helyettesítői a szervezet identitáskezelő rétegét a hitelesítési és engedélyezési vezérlők. 

Kiemelt jogosultságú rendszergazdai fiókok irányítják, az új "biztonsági határt." Rendkívül fontos, függetlenül attól, hogy a környezetben a helyszíni, felhőbeli vagy helyszíni hibrid emelt szintű hozzáférés védelme és a felhőbeli üzemeltetett szolgáltatások. Rendszergazdai hozzáférés elszánt támadókkal szembeni védelméhez szükséges kell tennie a teljes és átgondolt megközelítést saját intézménye rendszerei elkülönítése a kockázatokat. 

Módosítását igényli az emelt szintű hozzáférés biztonságossá tétele
* Folyamatok, felügyeleti eljárások és ismeretek felügyeleti
* Technikai összetevők, például a gazdagép védelem, a fiók védelmet és Identitáskezelés

Ez a dokumentum elsősorban felügyelt identitások biztonságát, és eléréséhez, amely egy ütemtervet a létrehozásakor vagy az Azure AD-ben a Microsoft Azure, Office 365 és más cloud services foglalkozik. Olyan szervezeteknek, amelyek rendelkeznek a helyi rendszergazdai fiókok, lásd az útmutató a helyszíni és hibrid, emelt szintű hozzáférés felügyelete az Active Directory Directory [emelt szintű hozzáférés biztonságossá tétele](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Ez a cikk az útmutató elsősorban az P1 és P2 szintű Azure Active Directory Premium-csomagokban elérhető szolgáltatások az Azure Active Directory hivatkozik. Az Azure Active Directory Premium P2 az EMS E5 csomag és a Microsoft 365 E5 csomag része. Ez az útmutató feltételezi, hogy a szervezet már rendelkezik Azure AD Premium P2 ügynöklicenceket, a felhasználók számára. Ha nem rendelkezik ezeket a licenceket, útmutatást némelyike nem feltétlenül vonatkozik az a szervezet. Ez a cikk teljes globális rendszergazdai kifejezés (vagy globális rendszergazdaként) is azonos a "Céges" vagy "bérlői rendszergazdája."

## <a name="develop-a-roadmap"></a>A terv kidolgozása 

A Microsoft javasolja, hogy fejleszthet, és hajtsa végre az internetes támadók emelt szintű hozzáférés biztonságossá tételéhez egy ütemtervet. Az ütemterv a már meglévő képességek és a szervezeten belül egyedi igényei mindig módosíthatja. Az ütemterv minden egyes fázisa kell támadásának költségét és nehézségét a támadók való emelt szintű hozzáférés a helyszíni, felhőbeli és hibrid eszközök. A Microsoft javasolja a következő négy ütemterv fázisokra: Ez a javasolt ütemterv ütemezések a leghatékonyabb és leggyorsabb először a Microsoft-elemeket a kibertámadás incidensek és a válasz végrehajtására alapján. Ütemterv megvizsgáltuk hozzávetőleges.

![Az ütemterv részét képezi a határidőinek fázisa](./media/directory-admin-roles-secure/roadmap-timeline.png)

* (24-48 óra) 1. fázis: Azt javasoljuk, hogy kritikus elemek nincs azonnal

* (2 – 4 hét) 2. fázis: A leggyakrabban alkalmazott támadási taktikák kockázatának csökkentése

* (1 – 3 hónapos) 3. fázis: Láthatóság hozhat létre, és állítsa össze a felügyeleti tevékenység teljes hozzáférés

* 4. fázis (hat hónapos és azt követően): Proaktívabb tovább erősíti a biztonsági platform

Az ütemterv keretrendszer célja maximalizálja a már telepített Microsoft-technológiák használatát. Jelenlegi és jövőbeli kulcsfontosságú technológiák előnyeit, és integrálhatja a már üzembe helyezte, vagy a releváns üzembe helyezése más gyártóktól származó biztonsági eszközöket is. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>1. fázis: Azt javasoljuk, hogy kritikus elemek nincs azonnal

![1. fázis](./media/directory-admin-roles-secure/stage-one.png)

1. fázisa az ütemterv részét képezi, amely gyorsan és könnyen megvalósítható kritikus feladatok összpontosít. Azt javasoljuk, hogy végrehajtja-e ezek néhány elemet azonnal belül a biztonságos emelt szintű hozzáférés alapvető szintjének biztosítása érdekében első 24-48 óra. Ebben a szakaszban az emelt szintű hozzáférés biztonságossá ütemterv az alábbi műveleteket tartalmazza:

### <a name="general-preparation"></a>Általános előkészítése

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Kapcsolja be az Azure AD Privileged Identity Management

Ha már nincs bekapcsolva az Azure AD Privileged Identity Management (PIM), ehhez az éles környezetbeli bérlőhöz. Privileged Identity Management bekapcsolása után értesítést kap e-mailek a privilegizált hozzáférés érdekében szerepkörök változása. Ezek az értesítések magas jogosultsági szintű szerepkörökhöz a címtárban további felhasználók hozzáadásakor adja meg a korai figyelmeztetéseket.

Az Azure AD Privileged Identity Management Azure AD Premium P2 vagy az EMS E5 tartalmazza. Ezek a megoldások segítségével alkalmazásokhoz és erőforrásokhoz való hozzáférés védelme, a helyszíni környezetben és a felhőben. Ha nem már rendelkezik Azure AD Premium P2 vagy az EMS E5, és szeretné kiértékelni az ütemterv hivatkozott szolgáltatásokat több, Regisztráljon a [Enterprise Mobility + Security ingyenes 90 napos próbaidőszak](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). E licenc kísérletek használatával próbálja ki az Azure AD Privileged Identity Management és az Azure AD Identity Protection a következőket figyelése tevékenység speciális biztonsági jelentések, a naplózás és a riasztások az Azure AD-vel.

Miután bekapcsolta az Azure AD Privileged Identity Management:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy olyan fiókkal, amely az éles-bérlő globális rendszergazdája.

2. Válassza ki a bérlő, ahol a Privileged Identity Management használni kívánt, válassza ki a felhasználónevet a az Azure portal jobb felső sarkában.

3. Válassza ki **minden szolgáltatás** és szűrje a listát a **Azure AD Privileged Identity Management**.

4. A Privileged Identity Management megnyitásához az **minden szolgáltatás** listában, és rögzítheti az irányítópulton.

Az első, aki a bérlő Azure AD Privileged Identity Management használatához a rendszer automatikusan hozzárendel a **biztonsági rendszergazda** és **kiemelt szerepkörű rendszergazda** szerepkörök a bérlőben. Csak a kiemelt szerepkörű rendszergazdák kezelhetik a az Azure AD directory szerepkör-hozzárendeléseket felhasználók. Emellett az Azure AD Privileged Identity Management felvett jelenik meg a biztonsági varázsló, amely végigvezeti az első felderítési és hozzárendelési élményen. A varázsló most további módosítások nélkül kiléphet. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Azonosíthatja és szerepkörök magas jogosultsági szintű fiókok kategorizálása 

Után az Azure AD Privileged Identity Management bekapcsolását, a felhasználók a címtár szerepkörök globális rendszergazdája, a kiemelt szerepkörű rendszergazda, a Exchange Online rendszergazdai és a SharePoint Online rendszergazdai megtekintése. Ha a bérlő nem rendelkezik Azure AD PIM-ben, akkor használhatja a [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Indítsa el a globális rendszergazdai szerepkörrel rendelkező, mivel ez a szerepkör általános: a rendszergazda szerepkörrel felruházott felhasználó ugyanazokkal az engedélyekkel rendelkezik, amelynek a szervezet fizetett elő, függetlenül attól, hogy van rendelve ezt a szerepkört az Office 365-portál a felhőszolgáltatásokon átívelő , az Azure portal vagy az Azure AD-modul a Microsoft PowerShell használatával. 

Távolítsa el a fiókokat, amelyek már nincs szükség az ezeket a szerepköröket. Ezt követően kategorizálása a megmaradt fiókokat hozzárendelt rendszergazdai szerepkörök:

* Felügyeleti felhasználók hozzárendelése különállóan, és nem felügyeleti célú (például személyes e-mail-cím) is használható
* Külön-külön hozzárendelt rendszergazdai jogosultságokkal rendelkező felhasználókhoz és a kijelölt csak felügyeleti célokra
* Több felhasználó között megosztott
* Szünet üvegből vészelérési forgatókönyvek
* Automatizált szkriptek esetében
* Külső felhasználók számára

#### <a name="define-at-least-two-emergency-access-accounts"></a>Legalább két vészelérési fiókok megadása 

Győződjön meg arról, hogy nem kap egy olyan helyzetet, ahol azok sikerült kell véletlenül kizárva felügyeletének részeként az Azure AD-bérlő miatt nem tud a bejelentkezéshez, vagy egy meglévő egyedi felhasználói fiók rendszergazdai aktiválása be. Például ha a szervezet egy helyszíni identitásszolgáltatót való összevonást használ, adott identitásszolgáltató nem érhető el, a felhasználók nem jelentkezhetnek be a helyszíni. Két vagy több vészelérési fiókok a bérlőben való tárolásával csökkentheti a rendszergazdai hozzáférés véletlen hiánya hatását.

A vészelérési fiókok segítségével a szervezetek korlátozni a rendszerjogosultságú hozzáférést egy meglévő Azure Active Directory-környezeten belül. Ezek a fiókok magas szintű jogosultságokkal a rendelkeznek, és nincsenek hozzárendelve a megadott személyeknek. A vészelérési fiókok korlátozva, vészhelyzeti "vészhelyzeti" forgatókönyvekhez, ahol a szokásos rendszergazdai fiókok nem használható. Szervezetek kell, hogy szabályozása, és csak az az időpont, amelyekre szükség a vészelérési fiókhoz használat csökkentése céljából. 

Értékelje ki a fiókokat, amelyek hozzárendelt vagy jogosult a globális rendszergazdai szerepkörhöz. Ha itt nem talál bármely csak felhőalapú fiókok a *. onmicrosoft.com tartomány ("vészhelyzeti" vészelérési szánt), hozza létre őket. További információkért lásd: [rendszergazdai fiókok vészhelyzeti hozzáférésének kezelése az Azure ad-ben](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>A multi-factor authentication bekapcsolása, és minden egyéb magas jogosultsági szintű egyfelhasználós nem összevont felügyeleti fiókok regisztrálása 

Az Azure multi-factor Authentication (MFA) megkövetelése bejelentkezéskor véglegesen egy vagy több Azure AD felügyeleti szerepkörök rendelt minden egyes felhasználók számára: Globális rendszergazda, kiemelt szerepkörű rendszergazda, az Exchange Online rendszergazdai és a rendszergazda a SharePoint online-hoz. Az útmutató használatával engedélyezhető az [multi-factor Authentication (MFA) a rendszergazdai fiókok](../authentication/howto-mfa-userstates.md) , és győződjön meg arról, hogy az összes számukra regisztrálták-e a [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup). További információ található a 2. lépés és az útmutató 3. lépés [adatait és szolgáltatásait az Office 365-ben való hozzáférés védelme](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>2. fázis: A leggyakrabban alkalmazott támadási taktikák kockázatának csökkentése

![2. szakasz](./media/directory-admin-roles-secure/stage-two.png)

A legtöbb gyakori problémák enyhítését célzó ütemterv összpontosít 2. fázis alkalmazott támadási taktikák kockázatának hitelesítő adatok ellopásának és visszaélések és megközelítőleg 2 – 4 héten belül végrehajtható. Ebben a szakaszban az emelt szintű hozzáférés biztonságossá ütemterv az alábbi műveleteket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítése

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Magatartási szolgáltatások tulajdonosai és rendszergazdái leltára

– A saját-eszközök használata (BYOD) és otthonról munkahelyi házirendekben, mind a vezeték nélküli kapcsolat cégek növekedésének száma rendkívül fontos, hogy nyomon, hogy ki csatlakozik a hálózathoz. Hatékony biztonsági naplózási gyakran felfedi, eszközök, alkalmazások és a hálózat, amely nem támogatja a futó programok informatikai, ezért potenciálisan nem biztonságos. További információkért lásd: [az Azure security management és a teljesítményfigyelés áttekintése](../../security/security-management-and-monitoring-overview.md). Győződjön meg arról, hogy Ön tartalmazzák az összes alábbi feladatot a leltározási folyamat során. 

* Rendszergazdai szerepkörök és szolgáltatások rendelkezik, ahol felügyelheti a felhasználók azonosítása.
* Azure AD PIM segítségével ismerje meg, hogy mely felhasználók a szervezet rendelkezik rendszergazdai hozzáféréssel az Azure AD, beleértve a további szerepkörök meghaladja az 1. fázis.
* Kívül a szerepköröket, az Azure ad-ben definiált Office 365 tartalmaz egy rendszergazdai szerepköröket, amelyeket hozzárendelhet a szervezetben a felhasználók számára. Minden rendszergazdai szerepkörhöz gyakori üzleti funkciók vannak leképezve, és lehetővé teszi személyek az adott feladatok az Office 365 felügyeleti központban a szervezet engedélyek. Az Office felügyeleti központja segítségével ismerje meg, hogy mely felhasználók a szervezet rendelkezik rendszergazdai hozzáféréssel az Office 365 szolgáltatásra, beleértve a szerepkörök nem kezeli az Azure AD-n keresztül. További információkért lásd: [Office 365 rendszergazdai szerepköreinek](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) és [ajánlott biztonsági eljárások az Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3).
* Hajtsa végre a készlet más szervezet támaszkodik, például Azure, az Intune-ban vagy a Dynamics 365-szolgáltatások.
* Győződjön meg arról, hogy a rendszergazdai fiókok (felügyeleti célokra, nem csak a felhasználók napi szintű fiókok által használt fiókok) rendelkezik csatlakoztatott adatlemezekkel e-mail-címek használata és Azure MFA-kiszolgáló van regisztrálva, vagy használja a helyszíni MFA.
* Kérje meg a felhasználók számára az üzleti indoklás rendszergazdai hozzáféréshez.
* Távolítsa el a rendszergazdai hozzáférés ezen személyek és szolgáltatások, amelyek nem szükséges.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Microsoft-fiókokat kíván állítani a munkahelyi vagy iskolai fiókok rendszergazdai szerepkör azonosítása 

Egyes esetekben egy szervezet számára a kezdeti globális rendszergazdák esetén újra felhasználhatja a meglévő Microsoft-fiók hitelesítő azok kezdődött, az Azure AD. Ezeket a Microsoft-fiókkal a felhőalapú és szinkronizált egyénit kell helyettesíteni. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Győződjön meg, hogy külön felhasználói fiókokat és a globális rendszergazdai fiókok levéltovábbítási 

Globális rendszergazdai fiókok nem rendelkezhet személyes e-mail-címeket, mivel személyes e-mail-fiókokat rendszeresen phished internetes támadók. Az internetes kockázatok (adathalász támadások, webböngészés véletlen) rendszergazdai jogosultságokkal, hozzon létre dedikált fiókokra, az egyes felhasználók rendszergazdai jogosultságokkal. 

Ha még nem tette meg, hozzon létre külön fiókokat globális rendszergazdai feladatokat, ellenőrizze, hogy véletlenül nem nyissa meg az e-mailek vagy a rendszergazdai fiókokhoz tartozó programok futtatását, hogy a felhasználók számára. Győződjön meg arról, hogy azok a fiókok rendelkezik egy működő postaláda továbbítani az e-mailjeikhez.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Győződjön meg arról, rendszergazdai fiókok jelszava nemrég megváltozott.

Győződjön meg arról, hogy minden felhasználó jelentkezett be a rendszergazdai fiókok és az utolsó 90 napban legalább egyszer módosította a jelszavukat. Arra is ügyeljen, hogy a felhasználók több olyan megosztott fiókok ismernie kell a jelszót nemrég módosította a jelszavukat volt.

#### <a name="turn-on-password-hash-synchronization"></a>Kapcsolja be a Jelszókivonat-szinkronizálás

A Jelszókivonat-szinkronizálás funkciója felhasználói jelszókivonatok egy helyszíni Active Directory-példányból egy felhőbeli Azure-bA a kivonatok szinkronizálását használt AD-példányt. Még akkor is, ha úgy dönt, hogy összevonási használata az Active Directory összevonási szolgáltatások (AD FS) vagy az egyéb identitás-szolgáltatóktól, akkor igény szerint állítsa be a Jelszókivonat-szinkronizálás biztonsági esetben a helyszíni infrastruktúrát, például az AD, vagy az ADFS-kiszolgálók sikertelen, vagy válik átmenetileg nem érhető el. Ez lehetővé teszi a felhasználók számára, hogy jelentkezzen be a szolgáltatás ugyanazt a jelszót, amely azok használatával jelentkezzen be a helyszíni AD-példányt. Ezenkívül lehetővé teszi Identity Protection hitelesítő adatokkal való visszaélés észlelése ezen jelszókivonatokat összehasonlításával ismert, hogy sérült, ha egy felhasználó múlttal rendelkezik a saját e-mail címet és az egyéb szolgáltatásaira nincs csatlakoztatva az Azure AD-jelszó jelszavakat.  További információkért lásd: [Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Többtényezős hitelesítés (MFA) szükséges az összes kiemelt szerepkörű felhasználók, valamint a kitett felhasználók

Az Azure AD használatát javasolja, hogy a többtényezős hitelesítés (MFA) minden felhasználó számára, beleértve a rendszergazdák és minden más felhasználók, akik jelentős hatással lenne, ha a fiókját feltörték (például a pénzügyi igazgatók) szükséges. Ez csökkenti a biztonsági kockázatot jelentő jelszó miatt a támadás kockázatát.

Kapcsolja be:

* [Nagy-kitettség fiókok MFA](../authentication/multi-factor-authentication-security-best-practices.md) például ügyvezető tisztviselők olyan szervezeti fiókok 
* [Többtényezős hitelesítés minden rendszergazdai fiók egy adott felhasználóhoz társított](../authentication/howto-mfa-userstates.md) a kapcsolódó egyéb SaaS-alkalmazások 
* Többtényezős hitelesítés minden rendszergazda számára a Microsoft SaaS-alkalmazásokhoz, többek között a Rendszergazdák szerepkör felügyelt az Exchange Online és az Office portálon

Ha használ Windows Hello for Business, az MFA úgy érheti el használata a Windows Hello bejelentkezési élményt nyújt. További információkért lásd: [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Az Identity Protection konfigurálása 

Az Azure AD Identity Protection, az algoritmus-alapú figyelési és jelentéskészítési eszköz, amellyel a szervezet identitásait érintő esetleges biztonsági rések észlelését. Automatikus válaszok e észlelt gyanús tevékenységek konfigurálhatja, és hajtsa végre a problémák megoldásához a megfelelő műveletet. További információkért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Szerezze be az Office 365-biztonságos pontszám (Ha használja az Office 365)

Biztonságos pontszám kitalálja, hogy mely Office 365-szolgáltatásokhoz (például a onedrive vállalati verzió, SharePoint és Exchange) használ, majd a beállításokat és a tevékenységek és az összehasonlítja azokat a Microsoft által létrehozott alapterv. Hogyan igazított a van ajánlott biztonsági eljárások alapján pontszámot kap. Bárki, aki rendszergazdai engedélyek (globális rendszergazda vagy egy egyéni rendszergazda szerepkör) számára az Office 365 vállalati prémium vagy nagyvállalati előfizetői férhet hozzá, a biztonságos pontszám [ https://securescore.office.com ](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Olvassa el az Office 365 biztonsági és megfelelőségi útmutatást (Ha használja az Office 365)

A [biztonsági és megfelelőségi terv](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) felvázolja a megközelítés arról, hogy a Office 365-felhasználó hogyan kell konfigurálnia az Office 365-höz és más EMS-képességeket használhatja. Ezt követően a felülvizsgálati lépések 3 – 6 bemutatja, hogyan [adatait és szolgáltatásait az Office 365-ben való hozzáférés védelme](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) és útmutató a hogyan [figyelése, biztonsági és megfelelőségi az Office 365-ben](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Konfigurálja az Office 365 figyelését (Ha használja az Office 365)

Megfigyelheti, hogyan a szervezet felhasználói használják az Office 365-szolgáltatásokhoz, lehetővé téve a felhasználók egy rendszergazdai fiókkal rendelkező, és akik előfordulhat, hogy nem kell az Office 365 hozzáférési miatt nem jelentkezik be azokat a kapukat azonosítása. További információkért lásd: [tevékenységre vonatkozó jelentések az Office 365 felügyeleti központban](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Incidens/válságkezelési válasz terv tulajdonosok létrehozása

Az incidensmegoldás hatékonyan végez egy összetett feladat. Ezért egy sikeres incidensmegoldási képesség létrehozó jelentős tervezés és szükséges erőforrásokat. Ez elengedhetetlen, hogy folyamatosan figyelje a kibertámadások ellen, és eljárásokat priorizálása az incidensek kezelését. Hatékony gyűjtése, elemzése és az adatok jelentéskészítés módszereket létfontosságú kapcsolatokat hozhat létre és más belső csoportok kommunikációt, és tervezze meg tulajdonosai. További információkért lásd: [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Biztonságos helyszíni emelt szintű rendszergazdai fiókok, ha még nem

Ha az Azure Active Directory-bérlő a helyszíni Active Directory szinkronizálva van, majd kövesse az útmutató [biztonsági emelt szintű hozzáférés védelmének ütemterve](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): 1. fázis. Ez magában foglalja a felhasználók számára, akiknek szükség van a helyszíni felügyeleti tevékenységek elvégzésére, emelt szintű hozzáféréssel rendelkező munkaállomások telepítése az Active Directory-rendszergazdák számára, majd hozza létre egyedi helyi rendszergazdai jelszavak a munkaállomásokhoz külön rendszergazdai fiókok létrehozása és a kiszolgálók.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépéseket a szervezet számára az Azure-bA a hozzáférés-kezelés

#### <a name="complete-an-inventory-of-subscriptions"></a>Végezze el az előfizetések leltára

A vállalati portál és az Azure portal használatával azonosítsa az előfizetéseket, amelyek éles üzemi alkalmazások pedig a szervezetben. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Távolítsa el a Microsoft-fiókok a rendszergazdai szerepkörök

Microsoft-fiókjait az egyéb programok, például az Xbox Live és az Outlook nem használandó rendszergazdai fiókok a szervezeti előfizetésekhez. Felügyeleti állapot eltávolítása az összes Microsoft-fiókok, és cserélje le az Active Directory (például chris@contoso.com) munkahelyi vagy iskolai fiókokat.

#### <a name="monitor-azure-activity"></a>Az Azure figyelése

Az Azure-tevékenységnapló az Azure előfizetés-szintű eseményeit tartalmazza. Aki létre, frissíthetők és törölhetők milyen erőforrásokat, és ezek az események előfordulásakor információkat biztosít. További információkért lásd: [naplózási és az Azure-előfizetés fontosabb műveleteivel kapcsolatos értesítések](../../azure-monitor/platform/quick-audit-notify-action-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépéseket a szervezet számára az Azure AD-n keresztül más felhőalapú alkalmazáshoz való hozzáférés kezelése 

#### <a name="configure-conditional-access-policies"></a>Feltételes hozzáférési szabályzatok konfigurálása

Készítse elő a feltételes hozzáférési szabályzatok a helyszíni és felhőben üzemeltetett alkalmazásokban. Ha a felhasználói munkahelyhez csatlakoztatott eszközök, részletes tájékoztatást a [beállítása a helyszíni feltételes hozzáférés által az Azure Active Directory eszközregisztrációjával](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>3. fázis: Láthatóság hozhat létre, és teljes mértékben vezérelhetik a felügyeleti tevékenység

![3. szakasz](./media/directory-admin-roles-secure/stage-three.png)

3. fázis a 2. fázis kockázatcsökkentő épül, és célja, hogy megközelítőleg 1 – 3 hónapon belül kell végrehajtani. A biztonságos emelt szintű hozzáférés védelmének ütemterve ezen szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítése

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>A felhasználók rendszergazdai szerepköröket a hozzáférési felülvizsgálat befejezése

Több vállalati felhasználók emelt szintű hozzáférjenek vannak, felhőszolgáltatások, ami egy növekvő nem felügyelt platform használatával. Ez magában foglalja a felhasználók Office 365, Azure-előfizetések rendszergazdái és virtuális gépekhez vagy SaaS-alkalmazások rendszergazdai hozzáféréssel rendelkező felhasználó számára a globális rendszergazdák váljon. Ehelyett a szervezetek minden alkalmazott, különösen a rendszergazdák kezelni a napi üzleti tranzakciók jogosultságokkal nem rendelkező felhasználóként, és csak végrehajtása a rendszergazdai jogosultságokkal, igény szerint kell rendelkeznie. Mivel a kezdeti bevezetési óta előfordulhat, hogy rendelkezik megnövelte a rendszergazdák számát, teljes hozzáférési felülvizsgálat azonosításához, és erősítse meg a minden felhasználó, aki rendszergazdai jogosultságokkal aktiválására jogosulttá. 

Tegye a következőket:

* Állapítsa meg, hogy mely felhasználók Azure ad-ben rendszergazdák, engedélyezze az igény szerinti, just-in-time rendszergazdai hozzáférés és szerepköralapú biztonsági vezérlők.
* Átalakítás egyértelmű indok arra, hogy egy másik szerepkör az emelt szintű rendszergazdai hozzáféréssel rendelkező felhasználók számára (Ha nem jogosult szerepkör, távolítsa el őket).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Bevezetés az erősebb hitelesítést minden felhasználó számára továbbra is 

C-suite vezetők, kritikus fontosságú magas szintű kezelők megkövetelése informatikai és biztonsági csoporthoz, valamint egyéb magas kitett felhasználók a modern, erős hitelesítést, például az Azure MFA vagy a Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Dedikált munkaállomásokat használja az Azure ad-felügyelethez

Előfordulhat, hogy a támadók próbál cél eléréséhez a szervezet adatokhoz és rendszerekhez, akkor megszakíthatja az integritásra és a kártékony kód, amely megváltoztatja a program logikai vagy a rendszergazdai hitelesítő adatok megadása snoops, az adatok hitelességének kiemelt jogosultságú fiókok. Emelt szintű hozzáféréssel rendelkező munkaállomások (Paw) dedikált operációs rendszert biztosítanak a védett internetes támadások és fenyegetési vektoroknak a bizalmas feladatokhoz. Megadhat, az ezeket a bizalmas feladatoknak és fiókoknak a napi használatú munkaállomások és eszközök igen erős védelmet nyújt az adathalász támadások, az alkalmazás és az operációs rendszer biztonsági réseket, különböző megszemélyesítési támadások és hitelesítő adatok ellopására irányuló támadásokkal, például billentyűleütés naplózás, a Pass-the-Hash és Pass-The-Ticket. Emelt hozzáférési szintű munkaállomások üzembe, csökkentheti a kockázatokat, ha a rendszergazdák rendszergazdai hitelesítő adataival, kivéve, adjon meg egy asztali környezetben, amely rendelkezik megerősített lett. További információkért lásd: [emelt szintű hozzáféréssel rendelkező munkaállomások](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Tekintse át a nemzeti szabványügyi és technológiai javaslatok incidenskezelés 

A nemzeti szabványügyi és technológiai a (NIST) incidensek kezelésére, különösen az incidens kapcsolódó adatok elemzése, és hogy a megfelelő választ az egyes incidensek nyújt útmutatást. További információkért lásd: [(NIST) a számítógép biztonsági incidensek kezelése útmutató (SP 800-61, 2. változat)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Privileged Identity Management (PIM) megvalósítása az igény szerinti további rendszergazdai szerepkörökhöz

Az Azure Active Directory használata [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) képesség. Időkorlátos aktiválását a kiemelt szerepkörökhöz úgy, hogy lehetővé teszi működik:

* Egy adott feladat végrehajtásához rendszergazdai jogosultságokat aktiválása
* MFA kényszerítése az aktiválási folyamat során
* Rendszergazdák tájékoztatja a sávon kívüli változásokkal kapcsolatos riasztások használata
* Engedélyezése a felhasználók számára egy előre konfigurált időtartamot adjon meg egy bizonyos jogosultságai
* Fedezze fel az összes emelt jogosultsági szintű identitásait, naplózási jelentések megtekintése és létrehozása a hozzáférési felülvizsgálatok minden felhasználót, aki rendszergazdai jogosultságokkal aktiválására jogosulttá azonosíthatja a biztonsági rendszergazdák engedélyezése

Ha már használja az Azure AD Privileged Identity Management, módosítsa az időhöz kötött jogosultságok időkeretének meghatározása szükséges (például a karbantartási időszakok).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Határozza meg a jelszóalapú bejelentkezés protokollok való kitettség (ha az Exchange Online-nal)

Múltbeli időpont protokollok feltételezi, hogy felhasználónév/jelszó kombináció is ágyazva eszközök, e-mail-fiókokat, -telefonokat és így tovább. Most már a kibertámadások ellen a felhőben kockázatát, javasoljuk, hogy azonosította a minden lehetséges felhasználói ki, ha hitelesítő adataikkal feltörték, sikerült katasztrofális a szervezet számára, és kizárja őket, hogy jelentkezzen be az e-mail-címét a felhasználónév-n keresztül, de / a jelszó erős hitelesítési követelmények és a feltételes hozzáférést megvalósításával. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Végezze el a szerepkörök felülvizsgálata értékelés Office 365-szerepkörök (Ha használja az Office 365)

Mérje fel, hogy minden rendszergazda felhasználó a megfelelő szerepkörök (törlése és újbóli hozzárendelése az értékelés szerint).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Tekintse át az Office 365-ben használt biztonsági incidenskezelés módszert, és a saját szervezeténél összehasonlítani

Ez a jelentés letölthető [biztonsági incidensek kezelése a Microsoft Office 365-ben](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Továbbra is a helyszíni emelt szintű rendszergazdai fiókok védelme

Ha az Azure Active Directory a helyszíni Active Directoryhoz van csatlakoztatva, majd kövesse az útmutató a [biztonsági emelt szintű hozzáférés védelmének ütemterve](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): 2. fázis. Ez magában foglalja az emelt szintű hozzáféréssel rendelkező munkaállomások telepítése az összes rendszergazda számára, többtényezős hitelesítés megkövetelése, csak Enough Admin használatával a tartományvezérlő karbantartásához, csökkenteni a támadási felületet a tartományok, támadások észlelésével az ATA üzembe helyezésének.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépéseket a szervezet számára az Azure-bA a hozzáférés-kezelés

#### <a name="establish-integrated-monitoring"></a>Integrált figyelés létrehozása

A [az Azure Security Center](../../security-center/security-center-intro.md) integrált biztonsági monitorozást és felügyeletet biztosít az Azure-előfizetésekre, észlelni tudja a fenyegetéseket, előfordulhat, hogy észrevétlenül, és számos biztonsági együttműködik megoldások.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Szoftverleltár-lévő üzemeltetett virtuális gépek a kiemelt jogosultságú fiókok

A legtöbb esetben nem kell megadnia a felhasználók korlátlan engedélyeket az Azure-előfizetések vagy erőforrásokat. Az Azure AD rendszergazdai szerepköreinek segítségével feladatköröket a szervezeten belül, és csak olyan mértékű hozzáférést biztosítson a felhasználók számára az adott feladatok elvégzéséhez szükséges. Például használja az Azure AD-rendszergazdai szerepköröket ahhoz, hogy egy rendszergazda, csak egy előfizetésben található virtuális gépek kezelése, míg egy másik kezelheti az SQL-adatbázisok ugyanazon az előfizetésen belül. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Portalon – első lépések](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Az Azure AD-rendszergazdai szerepköröket a PIM megvalósítása

Privileged identity Management használata az Azure AD-rendszergazdai szerepkörök kezelése, szabályozása és figyelése az Azure-erőforrásokhoz való hozzáférést. A PIM használatával védi a kiemelt jogosultságú fiókok kibertámadások ellen a jogosultságok elérhetőségi idejének csökkentését, és a jelentéseket és riasztásokat keresztül használatuk átláthatóvá növelésével. További információkért lásd: [Privileged Identity Management Azure erőforrások hozzáférésének kezelése RBAC](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure-naplók integrációja segítségével megfelelő elküldése az Azure naplózza a SIEM rendszerekhez 

Azure-naplók integrációja lehetővé teszi a szervezet meglévő biztonsági információk és eseménykezelés (SIEM) rendszereket az Azure-erőforrások nyers naplóinak integrálása. [Az Azure log integration](../../security/security-azure-log-integration-overview.md) Windows-eseménynaplók, és az Azure-erőforrások Azure-tevékenységnaplóinak, az Azure Security Center riasztásainak és az Azure diagnosztikai naplók a Windows-eseményeket gyűjti. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépéseket a szervezet számára az Azure AD-n keresztül más felhőalapú alkalmazáshoz való hozzáférés kezelése

#### <a name="implement-user-provisioning-for-connected-apps"></a>Csatlakoztatott alkalmazások felhasználókiépítése megvalósítása

Az Azure AD lehetővé teszi a létrehozása, a karbantartással és a felhasználói identitásokat felhőalapú (SaaS) alkalmazások, például a Dropbox vagy a Salesforce, ServiceNow eltávolításának automatizálása, és így tovább. További információkért lásd: [automatizálhatja a felhasználókiépítés és -megszüntetés SaaS-alkalmazásokhoz az Azure ad-vel](../manage-apps/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Information protection integrálása

MCAS lehetővé teszi fájlokat vizsgálhat és szabályzatokat állíthat be az Azure Information Protection-besorolási címkék, alapján engedélyezése áttekinthetőbbé és az adatok a felhőben. Vizsgálat és a felhőben lévő fájl besorolása, és a alkalmazni az Azure information protection-címkék. További információkért lásd: [Azure Information Protection-integráció](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

Egy csoport, helyen és az alkalmazás bizalmassági szintje alapján feltételes hozzáférési szabályzatokat [SaaS-alkalmazások](https://azure.microsoft.com/overview/what-is-saas/) és az Azure AD-csatlakoztatott alkalmazások. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Csatlakoztatott felhőalkalmazások tevékenység figyelése

Győződjön meg arról, egymáshoz kapcsolódó alkalmazások, valamint a védett felhasználók hozzáférését, azt javasoljuk, hogy kihasználhatja [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Ez biztosítja a felhőalapú alkalmazásokba engedélyezi, hogy a rendszergazdai fiókok védelme mellett a vállalati hozzáférés:

* Átláthatóságot és irányítást a felhőalapú alkalmazások kiterjesztése
* A hozzáférés, tevékenységek és az adatok megosztása házirendek létrehozása
* Automatikus azonosítása a kockázatos tevékenység, rendellenes viselkedések és fenyegetések
* Adatszivárgás megakadályozása
* Minimalizálja a kockázatot és automatizált fenyegetések megelőzése és a házirend betartatása

A Cloud App Security SIEM-ügynök a Cloud App Security integrálható az Office 365-riasztások és tevékenységek centralizált figyelését engedélyezéséhez az SIEM-kiszolgálóra. A kiszolgálón fut, és lekéri a riasztásokat és tevékenységeket a Cloud App Security és továbbítja azokat a SIEM-kiszolgálóra. További információkért lásd: [SIEM-integráció](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>4. fázis: Proaktívabb egy további biztonsági rendszer kialakításához


![4. fázis](./media/directory-admin-roles-secure/stage-four.png)

Az ütemterv – 4. fázis a 3. fázis látható-e épül, és célja, hogy hat hónapon belül és kívül kell végrehajtani. Befejezése strong fejleszt terv segít az emelt szintű hozzáférési védelmet jelenleg ismertek és elérhetők még ma a lehetséges támadások ellen. Biztonsági fenyegetések sajnos folyamatosan fejlődnek, és változnak, ezért azt javasoljuk, hogy megtekintheti a biztonsági olyan folyamatként összpontosított költségeinek növelése és csökkentése sikerének célja a környezetet.

Biztonságossá tétele a privileged access egy első kritikus fontosságú lépés egy modern szervezet az üzleti eszközök biztonsági intézkedésekkel létrehozó, de azt nem része a csak a teljes biztonsági programnak, amely tartalmazza a elemeket, például a szabályzat, a üzemeltetési és információk biztonsági, kiszolgálók, alkalmazások, számítógépek, eszközök, felhőháló és más összetevők adja meg a folyamatban lévő biztonsági intézkedésekkel. 

A privilegizált hozzáférési fiókok kezelése, mellett azt javasoljuk, tekintse át a következő folyamatosan:

* Győződjön meg arról, hogy a rendszergazdák végeznek a napi üzleti jogosultságokkal nem rendelkező felhasználóként.
* Csak emelt szintű hozzáférést, amikor szükséges, és távolítsa el ezt követően (just-in-time).
* Megőrzi, és tekintse át a kiemelt jogosultságú fiókok kapcsolatos naplózási tevékenység.

Az alkalmazásfejlesztés egy teljes körű biztonsági ütemterv további információkért lásd: [a Microsoft felhőalapú informatikai architektúra erőforrásaihoz](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). További információ a Microsoft-szolgáltatások, amelyek segítik a következő témakörökhöz, forduljon a Microsoft helyi képviselőjéhez, vagy tekintse meg [hozhat létre a vállalati védelme érdekében a kritikus fontosságú számítógépes védelem](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

Ez a biztonságos emelt szintű hozzáférés védelmének ütemterve folyamatban lévő utolsó szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítése

#### <a name="review-admin-roles-in-azure-active-directory"></a>Tekintse át az Azure Active Directory rendszergazdai szerepkörök 

Határozza meg, ha a jelenlegi beépített az Azure AD felügyeleti szerepkörök még mindig naprakészek, és győződjön meg, hogy a felhasználók csak a szerepkörök és találhatók, hogy a megfelelő engedélyeket kell delegálásokat. Az Azure AD-kijelölheti az külön rendszergazdák számára, hogy különböző célokat szolgál. További információkért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Tekintse át a felhasználók, akik rendelkeznek a felügyelet az Azure AD-hez csatlakoztatott eszközök

További információkért lásd: [hogyan hibrid konfigurálása Azure Active Directoryhoz csatlakoztatott eszközök](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Tekintse át a tagjai [beépített Office 365 rendszergazdai szerepkörei](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Ha Office 365-höz használ.
‎
#### <a name="validate-incident-response-plan"></a>Incidensmegoldási terv ellenőrzése

A terv alapján javítása érdekében a Microsoft azt javasolja, rendszeresen ellenőrzi, hogy a csomag a várt módon működik-e:

* Haladjon végig a meglévő, megtekintheti, milyen kimaradt közúti térképes
* A postmortem elemzés alapján vizsgálja felül a meglévő vagy új ajánlott eljárások definiálása
* Győződjön meg arról, hogy a frissített incidenskezelési tervet és ajánlott eljárások legyenek elosztva a szervezeten belül


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépéseket a szervezet számára az Azure-bA a hozzáférés-kezelés 

Ha szeretné meghatározni [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Vészhelyzeti": Mi a teendő vészhelyzet esetén

![Vészhelyzet](./media/directory-admin-roles-secure/emergency.jpeg)

1. Kulcs kezelők és a profiljával kapcsolatos információkat az incidens security officer értesíti.

2. Tekintse át a támadási forgatókönyvek. 

3. A "vészhelyzeti" fiók felhasználónév/jelszó kombináció, jelentkezzen be Azure ad-ben elérhető. 

4. Kérjen segítséget a Microsoft által [megnyitása egy Azure-támogatáskérést](../../azure-supportability/how-to-create-azure-support-request.md).

5. Tekintse meg a [Azure AD-be jelentések](../reports-monitoring/overview-reports.md). Előfordulhat, hogy késéssel között egy esemény bekövetkezett, és a jelentésben szerepel.

6. A hibrid környezetek, ha összevont és az AD FS-kiszolgáló nem érhető el, előfordulhat, hogy újra kell ideiglenesen állítsa át az összevont hitelesítés használata a Jelszókivonat-szinkronizálás. Ez a rendszer visszaállítja a tartomány összevonási vissza a felügyelt hitelesítésre mindaddig, amíg elérhetővé válik az AD FS-kiszolgálón.

7. Figyelheti a kiemelt jogosultságú fiókok e-mailben.

8. Ellenőrizze, hogy lehetséges jogi és a törvényszéki vizsgálati vonatkozó naplóinak biztonsági mentése.

Hogyan kezeli a Microsoft Office 365-höz a biztonsági incidensek kapcsolatos további információkért lásd: [biztonsági incidensek kezelése a Microsoft Office 365-ben](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>FAQ: Gyakori kérdések léphetünk emelt szintű hozzáférés biztonságossá tétele  


**KÉRDÉS:** Mit tegyek, ha még I még nincs megvalósítva a biztonságos hozzáférést összetevők?

**Válasz:** Legalább két break-vészhelyzeti fiók megadása, rendelje az MFA a kiemelt jogosultságú rendszergazdai fiókok, és külön felhasználói fiókokat globális rendszergazdai fiókkal.


**KÉRDÉS:** Egy illetéktelen behatolás után mit jelent a leggyakoribb probléma, amelyet elsőként?

**Válasz:** Győződjön meg arról, hogy Ön még a legerősebb hitelesítést igénylő magas kitett felhasználók számára.


**KÉRDÉS:** Mi történik, ha az emelt szintű rendszergazdák inaktiválták?

**Válasz:** Hozzon létre egy globális rendszergazdai fiókot, amely mindig naprakész.


**KÉRDÉS:** Mi történik, ha csak egy globális rendszergazdai balra, és nem érhető el? 

**Válasz:** A szünet üvegből fiókok valamelyikével azonnali emelt szintű hozzáférés.


**KÉRDÉS:** Hogyan tudja megvédeni a szervezeten belül rendszergazdák?

**Válasz:** Vannak olyan rendszergazdák mindig tegye a napi üzleti standard "jogosultságokkal nem rendelkező" felhasználóként.
 

**KÉRDÉS:** Mik az Azure AD-ben rendszergazdai fiókok létrehozásának ajánlott eljárásai?

**Válasz:** Tartalék emelt szintű hozzáférés adott felügyeleti feladatokhoz.


**KÉRDÉS:** Milyen eszközöket léteznek csökkentése állandó adminisztrátori hozzáférést?

**Válasz:** Privileged Identity Management (PIM) és az Azure AD felügyeleti szerepkörök.


**KÉRDÉS:** Mi a Microsoft álláspontja rendszergazdai fiókok Azure AD szinkronizálása?

**Válasz:** 0. rétegbeli rendszergazdai fiókok (beleértve a fiókok, csoportok, és más eszközök, amelyek közvetlen vagy közvetett ellenőrzést az AD-erdőhöz, tartományok és tartományvezérlők és az összes eszköz) felhasználtuk csak a helyszíni AD-fiókok alatt, és általában nem szinkronizálva az Azure ad-ben a felhőhöz. 


**KÉRDÉS:** Hogyan tudjuk megtartja a rendszergazdák a portálon véletlenszerű felügyeleti hozzáférés hozzárendelése a?

**Válasz:** Minden felhasználó és a legtöbb rendszergazdák nem kiemelt jogosultságú fiókokat kell használni. Első lépésként egy erőforrás-igényű, a szervezet, hogy mely néhány rendszergazdai fiókot kell lennie az emelt szintű fejlesztéséhez. És figyelje az újonnan létrehozott rendszergazda felhasználók.


## <a name="next-steps"></a>További lépések

* [Microsoft Trust Center termék biztonsági](https://www.microsoft.com/trustcenter/security) – biztonsági funkciói a Microsoft felhőalapú termékek és szolgáltatások

* [A Microsoft Trust Center – megfelelőségi](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – a Microsoft átfogó megfelelőségi ajánlattal, a cloud services

* [Hogyan kockázatbecslés](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -biztonsági és megfelelőségi követelmények a Microsoft cloud services kezelése

### <a name="other-ms-online-services"></a>Other MS Online Services 

* [A Microsoft Intune-ban biztonsági](https://www.microsoft.com/trustcenter/security/intune-security) – az Intune mobileszköz-kezelés, mobilalkalmazás-felügyelet és a felhőalapú számítógép-felügyeleti képességek biztosít.

* [A Microsoft Dynamics 365 security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 a Microsoft felhőalapú megoldása, amely egyesíti az Ügyfélkapcsolat-kezelés (CRM) és a vállalatierőforrás-tervezési (ERP) képességeit.

 
