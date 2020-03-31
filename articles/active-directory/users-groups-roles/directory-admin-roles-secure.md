---
title: Gyakorlati tanácsok a biztonságos rendszergazdai hozzáféréshez - Azure AD | Microsoft dokumentumok
description: Győződjön meg arról, hogy a szervezet rendszergazdai hozzáférési és rendszergazdai fiókjai biztonságosak. Az Azure AD, az Azure és a Microsoft Online Services konfiguráló rendszertervezői és informatikai szakemberek számára.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/13/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c46facb2d43137175730bf04fea0efec9c1ecbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266272"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez

A modern szervezet legtöbb vagy összes üzleti eszközének biztonsága az informatikai rendszereket kezelő és kezelő kiemelt fiókok integritásátétól függ. A rosszindulatú szereplők, köztük a kibertámadók gyakran rendszergazdai fiókokat és a kiemelt hozzáférés egyéb elemeit célozzák meg, hogy gyorsan hozzáférjenek a bizalmas adatokhoz és rendszerekhez a hitelesítő adatok ellopásával. A felhőszolgáltatások esetében a megelőzés és a válaszadás a felhőszolgáltató és az ügyfél közös felelőssége. A végpontokat és a felhőt fenyegető legújabb fenyegetésekről a [Microsoft security intelligence jelentésben](https://www.microsoft.com/security/operations/security-intelligence-report)talál további információt. Ez a cikk segítséget nyújt a jelenlegi tervek és az itt leírt útmutató közötti hézagok megszüntetésére vonatkozó ütemterv kidolgozásában.

> [!NOTE]
> A Microsoft elkötelezett a legmagasabb szintű bizalom, az átláthatóság, a szabványoknak való megfelelés és a jogszabályi megfelelőség mellett. További információ arról, hogy a Microsoft globális incidensreagálási csapata hogyan enyhíti a felhőszolgáltatások elleni támadások hatásait, és hogy a Microsoft üzleti termékeibe és felhőszolgáltatásaiba hogyan épül be a biztonság [a Microsoft Adatvédelmi központban – Biztonsági](https://www.microsoft.com/trustcenter/security) és Microsoft megfelelőségi célok a Microsoft [Adatvédelmi központban – Megfelelőség](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
A legtöbb szervezet számára az üzleti eszközök biztonsága az informatikai rendszereket felügyeleti és kezelő kiemelt fiókok integritásáttól függ. A kibertámadók az infrastruktúra-rendszerekhez (például az Active Directoryhoz és az Azure Active Directoryhoz) való kiemelt hozzáférésre összpontosítanak, hogy hozzáférjenek a szervezet bizalmas adataihoz. 

A hagyományos megközelítések, amelyek a hálózat bejárati és kilépési pontjainak elsődleges biztonsági határaként való biztosítására összpontosítanak, kevésbé hatékonyak a SaaS-alkalmazások és a személyes eszközök használatának növekedése miatt az interneten. Egy komplex, modern vállalaton belül a hálózati biztonsági határ természetes helyettesítői a szervezet identitáskezelő rétegében található hitelesítés- és engedélyezésvezérlők.

A kiemelt felügyeleti fiókok hatékonyan kézben tartják ezt az új "biztonsági területet". A kiemelt hozzáférés védelme fontos, függetlenül attól, hogy a környezet helyszíni, felhőbeli vagy hibrid helyszíni és felhőalapú szolgáltatások. A felügyeleti hozzáférés meghatározott ellenfelekkel szembeni védelme megköveteli, hogy teljes körű és átgondolt megközelítést alkalmazzon a szervezet rendszereinek kockázatoktól való elkülönítése érdekében. 

A kiemelt hozzáférés biztosításához módosítani kell a

* Folyamatok, adminisztratív gyakorlatok és tudásmenedzsment
* Technikai összetevők, például a gazdagépvédelem, a fiókvédelem és az identitáskezelés

Ez a dokumentum elsősorban az Azure AD, a Microsoft Azure, az Office 365 és más felhőszolgáltatások által felügyelt vagy jelentett identitások és hozzáférések biztonságossá tétele ütemtervének létrehozására összpontosít. A helyszíni rendszergazdai fiókkal rendelkező szervezetek ről a helyszíni és hibrid kiemelt hozzáférésről szóló útmutatóban található [a Bizalmas hozzáférés biztonságossá tétele.](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access) 

> [!NOTE] 
> Ebben a cikkben található útmutatás t, elsősorban az Azure Active Directory p1-es és P2-csomagjaiban található funkciókra hivatkozik. Az Azure Active Directory Premium P2 az EMS E5 csomag és a Microsoft 365 E5 csomag része. Ez az útmutató feltételezi, hogy a szervezet már rendelkezik a felhasználók számára vásárolt Azure AD Premium P2 licencekkel. Ha nem rendelkezik ezekkel a licencekkel, előfordulhat, hogy az útmutatás egy része nem vonatkozik a szervezetre. A jelen cikkben a globális rendszergazda (vagy globális rendszergazda) kifejezés a "vállalati rendszergazda" vagy a "bérlői rendszergazda" szinonimája.

## <a name="develop-a-roadmap"></a>Ütemterv kidolgozása 

A Microsoft azt javasolja, hogy dolgozzon ki és kövesse az ütemtervet a kiemelt hozzáférés biztosítása a számítógépes támadók ellen. Az ütemtervet bármikor módosíthatja a szervezeten belüli meglévő képességek és speciális követelmények megfelelően. Az ütemterv minden egyes szakaszában meg kell növelnie a költségek et és nehézséget az ellenfelek számára, hogy a helyszíni, felhőbeli és hibrid eszközök kiemelt hozzáféréssel való támadása. A Microsoft a következő négy ütemtervszakaszt ajánlja: Ez az ajánlott ütemterv először a leghatékonyabb és leggyorsabb megvalósításokat ütemezi a Microsoft kibertámadási incidensekkel és a válaszlépések megvalósításával kapcsolatos tapasztalatai alapján. Az ütemterv ütemterve hozzávetőleges.

![Az ütemterv szakaszai az idővonalakkal](./media/directory-admin-roles-secure/roadmap-timeline.png)

* 1. szakasz (24-48 óra): Az általunk javasolt kritikus elemeket azonnal

* 2. szakasz (2-4 hét): A leggyakrabban használt támadási technikák mérséklése

* 3. szakasz (1-3 hónap): Build láthatóság és épít teljes ellenőrzése admin tevékenység

* 4. szakasz (hat hónap és azon túl): További védelem a biztonsági platform további megkeményítése érdekében

Ez az ütemtervkeretrendszer célja, hogy maximalizálja a Microsoft által már telepített Microsoft-technológiák használatát. Kihasználhatja a legfontosabb jelenlegi és a közelgő biztonsági technológiák előnyeit is, és integrálhatja a már üzembe helyezett vagy üzembe helyezett más szállítók biztonsági eszközeit. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>1. szakasz: Az általunk javasolt kritikus elemek et azonnal meg kell tennie

![1. szakasz Kritikus elemek, amelyeket először el kell végezni](./media/directory-admin-roles-secure/stage-one.png)

Az ütemterv első szakasza a gyors és könnyen végrehajtható kritikus feladatokra összpontosít. Azt javasoljuk, hogy ezt a néhány elemet azonnal az első 24-48 órán belül, hogy biztosítsa a biztonságos kiemelt hozzáférés alapvető szintjét. A védett jogosultságú hozzáférésű útiterv ezen szakasza a következő műveleteket tartalmazza:

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Az Azure AD kiemelt identitáskezelésének bekapcsolása

Ha még nem kapcsolta be az Azure AD kiemelt identitáskezelés (PIM) szolgáltatást, tegye meg az éles környezetben. Miután bekapcsolta a kiemelt identitáskezelés, értesítést kap e-mail üzeneteket a kiemelt hozzáférési szerepkör módosításai. Ezek az értesítések korai figyelmeztetést biztosítanak, ha további felhasználókat adnak hozzá a címtár magas szintű jogosultsággal rendelkező szerepköreihez.

Az Azure AD privilegizált identitáskezelés az Azure AD Premium P2 vagy EMS E5 tartalmazza. Ezek a megoldások segítenek megvédeni az alkalmazásokhoz és erőforrásokhoz való hozzáférést a helyszíni környezetben és a felhőben. Ha még nem rendelkezik Azure AD Premium P2 vagy EMS E5 szolgáltatással, és szeretné kiértékelni az ebben az ütemtervben hivatkozott funkciókat, regisztráljon az [Enterprise Mobility + Security ingyenes 90 napos próbaverzióra.](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial) Ezekkel a licencpróbaverziókkal kipróbálhatod az Azure AD kiemelt identitáskezelési és az Azure AD Identity Protection szolgáltatást, így figyelheti a tevékenységeket az Azure AD speciális biztonsági jelentéskészítési, naplózási és riasztási használatával.

Miután bekapcsolta az Azure AD kiemelt identitáskezelést:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) egy olyan fiókkal, amely az éles környezetben lévő bérlő globális rendszergazdája.

2. Válassza ki a bérlő, ahol szeretné használni a kiemelt identitáskezelés, válassza ki a felhasználónevét az Azure Portal jobb felső sarkában.

3. Az Azure Portal menüben válassza a **Minden szolgáltatás** lehetőséget, és szűrje az **Azure AD kiemelt identitáskezelés**listájának szűrését.

4. Nyissa meg a Kiemelt identitáskezelés a **Minden szolgáltatás** listából, és rögzítse az irányítópulton.

Az első személy, aki az Azure AD kiemelt identitáskezelés a bérlőben automatikusan hozzávan rendelve a **biztonsági rendszergazda** és a **kiemelt szerepkör-rendszergazdai** szerepkörök a bérlőben. Csak a kiemelt szerep-rendszergazdák kezelhetik az Azure AD címtárszerepkör-hozzárendelések a felhasználók. Emellett az Azure AD jogosultsági szintű identitáskezelés hozzáadása után megjelenik a biztonsági varázsló, amely végigvezeti a kezdeti felderítési és hozzárendelési élményen. A varázslóból további módosítások nélkül is kiléphet. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Magas szintű jogosultsággal rendelkező szerepkörökben lévő fiókok azonosítása és kategorizálása 

Az Azure AD kiemelt identitáskezelés bekapcsolása után tekintse meg azokat a felhasználókat, akik a globális rendszergazda, a kiemelt szerepmegosztási rendszergazda, az Exchange Online-rendszergazda és a SharePoint Online-rendszergazda címtárszerepköreibe tartoznak. Ha nem rendelkezik az Azure AD PIM a bérlőben, használhatja a [PowerShell API-t.](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0) Kezdje a globális rendszergazdai szerepkörrel, mivel ez a szerepkör általános: a rendszergazdai szerepkörhöz rendelt felhasználó azonos engedélyekkel rendelkezik az összes olyan felhőszolgáltatásban, amelyre a szervezet előfizetett, függetlenül attól, hogy a Microsoft 365-ben kapta-e hozzá ezt a szerepkört. felügyeleti központ, az Azure Portalon, vagy az Azure AD modul a Microsoft PowerShell használatával. 

Távolítsa el azokat a fiókokat, amelyekre már nincs szükség ezekben a szerepkörökben. Ezután kategorizálja a rendszergazdai szerepkörökhöz rendelt többi fiókot:

* Egyénileg hozzárendelve a rendszergazdai felhasználókhoz, és nem adminisztratív célokra is használható (például személyes e-mail)
* Egyénileg hozzárendelt adminisztratív felhasználók és kijelölt adminisztratív célokra csak
* Több felhasználó között megosztva
* A törésüveg vészelérési forgatókönyvekhez
* Automatikus parancsfájlok esetén
* Külső felhasználók számára

#### <a name="define-at-least-two-emergency-access-accounts"></a>Legalább két vészelérési fiók definiálása 

Győződjön meg arról, hogy nem kap olyan helyzetben, ahol véletlenül lehet kizárni az Azure AD-bérlő felügyeletéből, mivel nem tud bejelentkezni vagy aktiválni egy meglévő egyéni felhasználó fiókját rendszergazdaként. Ha például a szervezet egy helyszíni identitásszolgáltatóhoz van összeállítva, előfordulhat, hogy az identitásszolgáltató nem érhető el, így a felhasználók nem tudnak bejelentkezni a helyszíni bejelentkezéshez. A felügyeleti hozzáférés véletlen hiánya hatását két vagy több vészelérési fiók bérlőben való tárolásával csökkentheti.

A vészelérési fiókok segítségével a szervezetek korlátozhatják a kiemelt hozzáférést egy meglévő Azure Active Directory-környezetben. Ezek a fiókok magas szintű jogosultsággal rendelkeznek, és nem vannak hozzárendelve bizonyos személyekhez. A vészhelyzeti hozzáférési fiókok vészhelyzetre korlátozódnak olyan esetekben, amikor a szokásos felügyeleti fiókok nem használhatók. A szervezeteknek biztosítaniuk kell a vészhelyzeti fiók használatának ellenőrzését és csökkentését arra az időre, amelyre szükség van.

Értékelje ki a globális rendszergazdai szerepkörhöz rendelt vagy arra jogosult fiókokat. Ha nem látott csak felhőalapú fiókokat a *.onmicrosoft.com tartomány használatával (a "törésüveg" vészeléréshez, hozza létre őket. További információ: [A segélyhívó rendszergazdai fiókok kezelése az Azure AD-ben.](directory-emergency-access.md)

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>A többtényezős hitelesítés bekapcsolása és az összes többi, nagy jogosultságú, egyfelhasználós, nem összevont rendszergazdai fiók regisztrálása

Az Azure többtényezős hitelesítésének megkövetelése bejelentkezéskor minden olyan egyéni felhasználó számára, aki tartósan hozzá van rendelve egy vagy több Azure AD-rendszergazdai szerepkörhöz: globális rendszergazda, kiemelt szerepkör-rendszergazda, Exchange Online-rendszergazda és SharePoint Online rendszergazda. Az útmutató segítségével engedélyezheti a [többtényezős hitelesítést (MFA) a rendszergazdai fiókokhoz,](../authentication/howto-mfa-userstates.md) és biztosíthatja, hogy az összes felhasználó regisztrált a rendszerben. [https://aka.ms/mfasetup](https://aka.ms/mfasetup) További információ az [Office 365-ben az adatokhoz és szolgáltatásokhoz való hozzáférés védelme](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)című útmutató 2. 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>2. szakasz: A leggyakrabban használt támadási technikák mérséklése

![2. szakasz A gyakran használt támadások enyhítése](./media/directory-admin-roles-secure/stage-two.png)

Az ütemterv 2. A védett jogosultságú hozzáférésű útiterv ezen szakasza a következő műveleteket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Szolgáltatások, tulajdonosok és rendszergazdák leltárának lebonyolítása

A bring-your-own-device (BYOD) és az otthoni munka házirendek számának növekedésével, valamint a vezeték nélküli kapcsolat növekedésével a vállalkozásoknál rendkívül fontos, hogy figyelemmel kísérje, ki csatlakozik a hálózathoz. A hatékony biztonsági naplózás gyakran olyan, a hálózaton futó eszközöket, alkalmazásokat és programokat tár fel, amelyeket az informatikai szervezet nem támogat, és ezért potenciálisan nem biztonságos. További információ: [Azure security management and monitoring overview](../../security/fundamentals/management-monitoring-overview.md). Győződjön meg arról, hogy az alábbi feladatok mindegyikét bevonja a készletfolyamatba. 

* Azonosítsa azokat a felhasználókat, akik rendszergazdai szerepkörrel rendelkeznek, és azokat a szolgáltatásokat, ahol kezelhetők.
* Az Azure AD PIM használatával megtudhatja, hogy a szervezet mely felhasználói rendelkeznek rendszergazdai hozzáféréssel az Azure AD-hez, beleértve az 1.
* Az Azure AD-ben definiált szerepkörökön túl az Office 365 rendszergazdai szerepköröket is bemutat, amelyeket a szervezet felhasználóihoz rendelhet. Minden rendszergazdai szerepkör leképezi a gyakori üzleti funkciókat, és a szervezet ben lévő személyeknek engedélyt ad a [Microsoft 365 Felügyeleti központ](https://admin.microsoft.com)bizonyos feladatainak elvégzésére. A Microsoft 365 Felügyeleti központ segítségével megtudhatja, hogy a szervezet mely felhasználói rendelkeznek rendszergazdai hozzáféréssel az Office 365-höz, beleértve az Azure AD-ben nem felügyelt szerepköröket is. További információt Az [Office 365 rendszergazdai szerepkörei](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) és [az Office 365 biztonsági gyakorlati tanácsai](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)című témakörben talál.
* Végezze el a készletet más szolgáltatásokban, amelyekre a szervezet támaszkodik, például az Azure, az Intune vagy a Dynamics 365.
* Győződjön meg arról, hogy a rendszergazdai fiókok (felügyeleti célokra használt fiókok, nem csak a felhasználók napi fiókjai) rendelkeznek működő e-mail-címekkel, és regisztráltak az Azure MFA-ra, vagy helyszíni MFA-t használnak.
* Kérje meg a felhasználókat, hogy indokolják az adminisztratív hozzáférést.
* Távolítsa el a rendszergazdai hozzáférést azon személyek és szolgáltatások számára, akiknek nincs rá szükségük.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Azonosítsa azokat a Microsoft-fiókokat a felügyeleti szerepkörökben, amelyeket át kell váltani a munkahelyi vagy iskolai fiókokra

Néha a szervezet kezdeti globális rendszergazdái újrafelhasználják a meglévő Microsoft-fiók hitelesítő adatait, amikor elkezdték használni az Azure AD-t. Ezeket a Microsoft-fiókokat egyedi felhőalapú vagy szinkronizált fiókoknak kell helyettesíteniük. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Külön felhasználói fiókok és levéltovábbítás biztosítása globális rendszergazdai fiókok hoz 

A globális rendszergazdai fiókoknem rendelkeznek személyes e-mail címekkel, mivel a kibertámadók rendszeresen halásznak személyes e-mail fiókokat. Az internetes kockázatok (adathalász támadások, nem szándékos webböngészés) és a rendszergazdai jogosultságok elkülönítése érdekében hozzon létre külön fiókokat minden rendszergazdai jogosultsággal rendelkező felhasználó számára. 

Ha még nem tette meg, hozzon létre külön fiókokat a felhasználók számára a globális rendszergazdai feladatok elvégzéséhez, hogy megbizonyosodjon arról, hogy véletlenül nem nyitják meg az e-maileket, és nem futtatnak a rendszergazdai fiókjaikhoz társított programokat. Győződjön meg arról, hogy ezek a fiókok e-mailjeit egy működő postaládába továbbítják.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>A felügyeleti fiókok jelszavának ellenőrzése a közelmúltban

Győződjön meg arról, hogy minden felhasználó bejelentkezett a felügyeleti fiókjába, és legalább egyszer megváltoztatta a jelszavát az elmúlt 90 napban. Győződjön meg arról is, hogy minden olyan megosztott fiók, amelyben több felhasználó tudja, hogy a jelszó a közelmúltban módosította a jelszavát.

#### <a name="turn-on-password-hash-synchronization"></a>A jelszókivonat-szinkronizálás bekapcsolása

A jelszókivonat-szinkronizálás olyan szolgáltatás, amellyel szinkronizálhatja a felhasználói jelszókivonatokat egy helyszíni Active Directory-példányból egy felhőalapú Azure AD-példányba. Még ha úgy is dönt, hogy összevonást használ az Active Directory összevonási szolgáltatásokkal (AD FS) vagy más identitásszolgáltatókkal, szükség esetén beállíthatja a jelszókivonat-szinkronizálást biztonsági másolatként arra az esetre, ha a helyszíni infrastruktúra, például az AD vagy az ADFS-kiszolgálók meghibásodnának vagy meghibásodnának, átmenetileg nem érhető el. Ez lehetővé teszi a felhasználók számára, hogy jelentkezzen be a szolgáltatásba ugyanazt a jelszót, hogy az általuk használt bejelentkezni a helyszíni AD-példány. Emellett lehetővé teszi, hogy az Identity Protection észlelje a feltört hitelesítő adatokat azáltal, hogy összehasonlítja ezeket a jelszókivéteket az ismerten feltört jelszavakkal, ha a felhasználó ugyanazt az e-mail-címet és jelszót használta ki az Azure AD-hez nem kapcsolódó más szolgáltatásokon.  További információ: [Jelszókivonat-szinkronizálás megvalósítása az Azure AD Connect-szinkronizálással.](../hybrid/how-to-connect-password-hash-synchronization.md)

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Többtényezős hitelesítés (MFA) megkövetelése az összes kiemelt szerepkörben lévő felhasználók, valamint a kitett felhasználók számára

Az Azure AD azt javasolja, hogy többtényezős hitelesítést (MFA) kell megkövetelnie az összes felhasználószámára, beleértve a rendszergazdákat és az összes többi olyan felhasználót, akik jelentős hatást gyakorolnának, ha a fiókjuk at feltörték (például pénzügyi tisztviselők). Ez csökkenti a sérült jelszó miatti támadások kockázatát.

Bekapcsolás:

* [Az MFA feltételes hozzáférési házirendeket használ](../authentication/howto-mfa-getstarted.md) a szervezet összes felhasználója számára.

Ha a Windows Hello for Business alkalmazást használja, az MFA-követelmény a Windows Hello bejelentkezési felületével teljesíthető. További információt a Windows Hello című [témakörben talál.](https://docs.microsoft.com/windows/uwp/security/microsoft-passport) 

#### <a name="configure-identity-protection"></a>Identitásvédelem konfigurálása 

Az Azure AD Identity Protection egy algoritmus-alapú figyelési és jelentéskészítési eszköz, amely segítségével észlelheti a szervezet identitásait érintő potenciális biztonsági réseket. Beállíthatja az észlelt gyanús tevékenységekre adott automatikus válaszokat, és megoldhatja a megfelelő lépéseket azok megoldásához. További információkért lásd: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Az Office 365 biztonságos pontszámának beszerzése (az Office 365 használata esetén)

A Biztonságos pontszám megállapítja, hogy milyen Office 365-szolgáltatásokat használ (például A OneDrive, a SharePoint és az Exchange), majd megvizsgálja a beállításokat és tevékenységeket, és összehasonlítja őket a Microsoft által létrehozott alaptervvel. Kapsz egy pontszámot alapján, hogy mennyire igazodik a legjobb biztonsági gyakorlat. Bárki, aki rendszergazdai engedélyekkel (globális rendszergazdai vagy egyéni rendszergazdai szerepkörrel) rendelkezik [https://securescore.office.com](https://securescore.office.com/)egy Office 365 Vállalati Prémium verziós vagy Nagyvállalati előfizetéshez, hozzáférhet a biztonságos pontszámhoz a következő helyen:

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Tekintse át az Office 365 biztonsági és megfelelőségi útmutatóját (az Office 365 használata esetén)

A [biztonsági és megfelelőségi terv](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) felvázolja, hogyan konfigurálja az Office 365-ös ügyfelek az Office 365-öt, és hogyan használja ki az EGYÉB EMS-funkciókat. Ezt követően tekintse át az [Adatokhoz és szolgáltatásokhoz való hozzáférés védelme az Office 365-ben](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) című 3–6. [monitor security and compliance in Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Az Office 365 tevékenységfigyelésének konfigurálása (az Office 365 használata esetén)

Figyelemmel kísérheti, hogy a szervezetben lévő személyek hogyan használják az Office 365-szolgáltatásokat, így azonosíthatja azokat a felhasználókat, akik rendszergazdai fiókkal rendelkeznek, és akiknek esetleg nincs szükségük Office 365-hozzáférésre, mivel nem jelentkeznek be ezekbe a portálokba. További információt a [Tevékenységjelentések a Microsoft 365 Felügyeleti központban című témakörben talál.](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)

#### <a name="establish-incidentemergency-response-plan-owners"></a>Esemény- és veszélyhelyzet-reagálási terv tulajdonosok létrehozása

Az incidensekre adott hatékony reagálás összetett vállalkozás. Ezért a sikeres incidensreagálási képesség létrehozása jelentős tervezést és erőforrásokat igényel. Alapvető fontosságú, hogy folyamatosan figyelje a kibertámadásokat, és eljárásokat dolgozzon ki az incidensek kezelésének rangsorolására. Az adatok gyűjtésének, elemzésének és jelentésének hatékony módszerei létfontosságúak a kapcsolatok kiépítéséhez, valamint a más belső csoportokkal és a tervtulajdonosokkal való kommunikáció létrehozásához. További információt a [Microsoft Biztonsági válaszközpont című témakörben talál.](https://technet.microsoft.com/security/dn440717) 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>A helyszíni kiemelt rendszergazdai fiókok biztonságossá tétele, ha még nem történt meg

Ha az Azure Active Directory-bérlő szinkronizálva van a helyszíni Active Directoryval, kövesse a [biztonsági jogosultsággal rendelkező hozzáférési ütemterv](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): 1. Ez magában foglalja a helyszíni felügyeleti feladatok elvégzéséhez szükséges felhasználók számára külön rendszergazdai fiókok létrehozását, az Active Directory-rendszergazdák számára a kiemelt hozzáférésű munkaállomások üzembe helyezését, valamint egyedi helyi rendszergazdai jelszavak létrehozását a munkaállomások és a munkaállomások számára és Szerverek.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépések az Azure-hoz való hozzáférést kezelő szervezetek számára

#### <a name="complete-an-inventory-of-subscriptions"></a>Előfizetések leltárának elkészítése

Az Enterprise portal és az Azure Portal segítségével azonosíthatja az éles alkalmazásokat üzemeltető szervezet előfizetéseit.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Microsoft-fiókok eltávolítása rendszergazdai szerepkörökből

Más programokból, például az Xbox, a Live és az Outlook programból származó Microsoft-fiókok nem használhatók szervezeti előfizetések rendszergazdai fiókjaként. Távolítsa el a rendszergazdai állapotot az összes Microsoft-fiókból, és cserélje le az Azure Active Directory (például) chris@contoso.communkahelyi vagy iskolai fiókokra.

#### <a name="monitor-azure-activity"></a>Azure-tevékenység figyelése

Az Azure-tevékenységnapló az Azure előfizetés-szintű eseményeit tartalmazza. Tájékoztatást nyújt arról, hogy ki hozta létre, frissítette és törölte az erőforrásokat, és mikor történtek ezek az események. További információt az [Azure-előfizetésfontos műveletekről szóló értesítések naplózása és fogadása](../../azure-monitor/platform/quick-audit-notify-action-subscription.md)című témakörben talál.

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépések a más felhőalapú alkalmazásokhoz való hozzáférést kezelő szervezetek számára az Azure AD-n keresztül

#### <a name="configure-conditional-access-policies"></a>Feltételes hozzáférési házirendek konfigurálása

Feltételes hozzáférési szabályzatok előkészítése a helyszíni és a felhőben üzemeltetett alkalmazásokhoz. Ha a felhasználók munkahelyi összekapcsolt eszközök, további információt [a beállítása a helyszíni feltételes hozzáférés segítségével Azure Active Directory eszközregisztráció.](../active-directory-device-registration-on-premises-setup.md)


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>3. szakasz: A láthatóság kiépítése és az adminisztrációs tevékenység teljes körű ellenőrzése

![3. szakasz átveszi az irányítást az admin tevékenység](./media/directory-admin-roles-secure/stage-three.png)

A 3. A védett jogosultságú hozzáférésű ütemterv ezen szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Rendszergazdai szerepkörökben lévő felhasználók hozzáférés-felülvizsgálatának befejezése

Több vállalati felhasználó privilegizált hozzáférést kap a felhőszolgáltatásokon keresztül, ami egyre növekvő nem felügyelt platformhoz vezethet. Ez magában foglalja az Office 365 globális rendszergazdáivá válást, az Azure-előfizetés-rendszergazdákat és a virtuális gépekhez vagy SaaS-alkalmazásokon keresztül rendszergazdai hozzáféréssel rendelkező felhasználókat. Ehelyett a szervezeteknek minden alkalmazottal kell rendelkezniük, különösen a rendszergazdáknak, a napi üzleti tranzakciókat jogosultságnélküli felhasználóként kell kezelniük, és csak szükség szerint kell rendszergazdai jogokat felvállalniuk. Mivel a rendszergazdai szerepkörökben lévő felhasználók száma a kezdeti bevezetés óta növekedhet, teljes körű hozzáférési felülvizsgálatok végrehajtásával azonosíthatja és megerősítheti azokat a felhasználókat, akik jogosultak a rendszergazdai jogosultságok aktiválására. 

Tegye a következőket:

* Határozza meg, hogy mely felhasználók az Azure AD-rendszergazdák, engedélyezze az igény szerinti, just-in-time rendszergazdai hozzáférést és a szerepköralapú biztonsági vezérlőket.
* Konvertálja azokat a felhasználókat, akik nem rendelkeznek egyértelmű indoklással a rendszergazdai jogosultsággal rendelkező hozzáférésre egy másik szerepkörhöz (ha nincs jogosult szerepkör, távolítsa el őket).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Az erősebb hitelesítés bevezetésének folytatása minden felhasználó számára 

A C-suite-vezetők, a magas szintű vezetők, a kritikus informatikai és biztonsági személyzet, valamint más nagy védelmet képviselő felhasználók modern, erős hitelesítéssel, például az Azure MFA vagy a Windows Hello használatával rendelkeznek. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Dedikált munkaállomások használata az Azure AD felügyeletéhez

A támadók megpróbálhatnak kiemelt jogosultságú fiókokat megcélozni, hogy hozzáférjenek a szervezet adataihoz és rendszereihez, hogy megzavarhassák az adatok integritását és hitelességét a program logikáját módosító rosszindulatú kód segítségével, vagy a rendszergazda hitelesítő adatait beírása kortikó snoopon keresztül. Az emelt hozzáférési szintű munkaállomások (Privileged Access Workstation, PAW) az internetről érkező támadások és fenyegetések ellen védett dedikált operációs rendszert biztosítanak a bizalmas feladatokhoz. A bizalmas feladatoknak és fiókoknak a napi szinten használt munkaállomásoktól és eszközöktől való elkülönítése igen erős védelmet nyújt az adathalász támadások, az alkalmazások és az operációs rendszer biztonsági réseit kihasználó támadások, a különböző megszemélyesítési támadások és a hitelesítő adatok ellopására irányuló támadások, például a billentyűleütések naplózása, a pass-the-hash és a pass-the-ticket típusú támadások ellen. A kiemelt hozzáférésű munkaállomások telepítésével csökkentheti annak kockázatát, hogy a rendszergazdák rendszergazdai hitelesítő adatokat adjanak meg, kivéve egy megerősített asztali környezetben. További információt a [Kiemelt hozzáférésű munkaállomások című témakörben talál.](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Tekintse át a Nemzeti Szabványügyi és Technológiai Intézet ajánlásait az incidensek kezelésére 

A National Institute of Standards and Technology (NIST) iránymutatást ad az incidensek kezeléséhez, különösen az incidensekkel kapcsolatos adatok elemzéséhez és az egyes eseményekre adott megfelelő válaszok meghatározásához. További információ: [The (NIST) Computer Security Incident Handling Guide (SP 800-61, Revision 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Emelt szintű identitáskezelés (PIM) megvalósítása a közös nyomozócsoport számára további felügyeleti szerepkörökhöz

Az Azure Active Directory esetében használja [az Azure AD kiemelt identitáskezelési](../privileged-identity-management/pim-configure.md) képességét. A kiemelt szerepkörök korlátozott idejű aktiválása úgy működik, hogy lehetővé teszi a következőket:

* Rendszergazdai jogosultságok aktiválása adott feladat végrehajtásához
* Az MFA kényszerítése az aktiválási folyamat során
* A riasztások használatával tájékoztathato a rendszergazdákat a sávon kívüli változásokról
* Bizonyos jogosultságok megtartására lehetővé tevő felhasználók előre konfigurált ideig
* A rendszergazdai jogosultsággal rendelkező identitások felderítésének, a naplózási jelentések megtekintésének és a hozzáférési felülvizsgálatok létrehozásának engedélyezése a rendszergazdai jogosultságok aktiválására jogosult felhasználók azonosításához

Ha már használja az Azure AD kiemelt identitáskezelés, állítsa be az időkeretek időkötött jogosultságok szükség szerint (például karbantartási időszakok).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Jelszóalapú bejelentkezési protokollok nak való kitettség meghatározása (Exchange Online használata esetén)

A múltban a protokollok azt feltételezték, hogy a felhasználónév/jelszó kombinációk eszközökbe, e-mail fiókokba, telefonokba és így tovább vannak ágyazva. De most a kockázatot a kibertámadások a felhőben, azt javasoljuk, hogy azonosítsa az összes potenciális felhasználó, aki, ha a hitelesítő adatok veszélybe kerültek, katasztrofális lehet a szervezet számára, és zárja ki őket attól, hogy jelentkezzen be az e-mail felhasználónévvel/jelszóval alkalmazásával erős hitelesítési követelmények és feltételes hozzáférés. Az örökölt hitelesítést a [Feltételes hozzáférés használatával tilthatja le.](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication) Ellenőrizze az egyszerű [hitelesítés letiltásának](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) részleteit az Exchange online szolgáltatásán keresztül. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Szerepkör-felülvizsgálati értékelés elvégzése az Office 365-szerepkörökhöz (az Office 365 használata esetén)

Mérje fel, hogy az összes rendszergazda felhasználó a megfelelő szerepkörben van-e (törölje és rendelje hozzá az értékelés nek megfelelően).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Tekintse át az Office 365-ben alkalmazott biztonsági incidensek kezelésének megközelítését, és hasonlítsa össze a saját szervezetével

Ezt a jelentést a [Microsoft Office 365 biztonsági incidenskezelés szolgáltatásából](https://www.microsoft.com/download/details.aspx?id=54302)töltheti le.

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>A helyszíni kiemelt rendszergazdai fiókok biztonságossá tétele

Ha az Azure Active Directory csatlakozik a helyszíni Active Directory, majd kövesse a [biztonsági privilegizált hozzáférési ütemterv:](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)2. Ez magában foglalja a kiemelt hozzáférésű munkaállomások üzembe helyezését az összes rendszergazda számára, az MFA megkövetelését, a csak elég rendszergazda használatát a tartományvezérlő karbantartásához, a tartományok támadási felületének csökkentését, az ATA telepítését a támadások észlelésére.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépések az Azure-hoz való hozzáférést kezelő szervezetek számára

#### <a name="establish-integrated-monitoring"></a>Integrált nyomon követés létrehozása

Az [Azure Security Center](../../security-center/security-center-intro.md) integrált biztonsági figyelést és szabályzatkezelést biztosít az Azure-előfizetések között, segít észlelni az egyébként észrevétlenül megjelenő fenyegetéseket, és a biztonsági megoldások széles ökoszisztémájával működik együtt.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>A kiemelt jogosultságú fiókok leltározása a tárolt virtuális gépeken belül

A legtöbb esetben nem kell korlátlan engedélyeket adnia a felhasználóknak az összes Azure-előfizetéshez vagy -erőforráshoz. Az Azure AD-rendszergazdai szerepkörök használatával elkülönítheti a feladatokat a szervezeten belül, és csak az adott feladatok elvégzéséhez szükséges felhasználók számára biztosíthatja a hozzáférést. Például az Azure AD rendszergazdai szerepkörök használatával lehetővé teheti, hogy egy rendszergazda csak virtuális gépeket kezeljen egy előfizetésben, míg egy másik kezelheti az SQL-adatbázisokat ugyanazon az előfizetésen belül. További információ: [A szerepköralapú hozzáférés-vezérlés az Azure Portalon](../../role-based-access-control/overview.md)című témakörben található.

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>PIM megvalósítása Az Azure AD-rendszergazdai szerepkörökhöz

A Kiemelt identitáskezelés azure AD-rendszergazdai szerepkörökkel az Azure-erőforrások kezeléséhez, vezérléséhez és figyeléséhez használja a kiemelt identitáskezelés használatát. A PIM használatával védi a kiemelt jogosultságú fiókokat a kibertámadások ellen azáltal, hogy csökkenti a jogosultságok expozíciós idejét, és jelentések és riasztások segítségével növeli a használatuk láthatóságát. További információ: [RBAC-hozzáférés kezelése az Azure-erőforrásokhoz emelt szintű identitáskezeléssel.](../../role-based-access-control/pim-azure-resource.md)

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure-naplóintegrációk használatával releváns Azure-naplókat küldhet A SIEM-rendszereknek 

Az Azure-napló integráció lehetővé teszi, hogy integrálja a nyers naplók az Azure-erőforrások a szervezet meglévő biztonsági információk és eseménykezelési (SIEM) rendszerek. [Az Azure-naplóintegráció](../../security/fundamentals/azure-log-integration-overview.md) a Windows Eseménynapló-naplókból, az Azure-erőforrásokpedig az Azure-tevékenységnaplókból, az Azure Security Center-riasztásokból és az Azure diagnosztikai naplókból gyűjti a Windows-eseményeket. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépések a más felhőalapú alkalmazásokhoz való hozzáférést kezelő szervezetek számára az Azure AD-n keresztül

#### <a name="implement-user-provisioning-for-connected-apps"></a>A csatlakoztatott alkalmazások felhasználói kiépítésének megvalósítása

Az Azure AD lehetővé teszi a felhasználói identitások létrehozását, karbantartását és eltávolítását a felhőalapú (SaaS) alkalmazásokban, például a Dropboxban, a Salesforce-ban, a ServiceNow-ban és így tovább. További információ: [A felhasználói kiépítés automatizálása és a SaaS-alkalmazások létesítésének megszüntetése az Azure AD-vel](../app-provisioning/user-provisioning.md)című témakörben.

#### <a name="integrate-information-protection"></a>Információvédelem integrálása

Az MCAS lehetővé teszi a fájlok vizsgálatát és az Azure Information Protection besorolási címkék en alapuló szabályzatok beállítását, lehetővé téve az adatok nagyobb láthatóságát és ellenőrzését a felhőben. A felhőben lévő fájlok bekéselése és besorolása, valamint az Azure-adatok védelmére vonatkozó címkék alkalmazása. További információ: [Azure Information Protection integration.](https://docs.microsoft.com/cloud-app-security/azip-integration)

#### <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

Konfigurálja a feltételes hozzáférést egy csoport, hely és alkalmazásérzékenység alapján a [SaaS-alkalmazások](https://azure.microsoft.com/overview/what-is-saas/) és az Azure AD-hez csatlakoztatott alkalmazások esetében. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Tevékenység figyelése a csatlakoztatott felhőalapú alkalmazásokban

Annak érdekében, hogy a csatlakoztatott alkalmazásokban is védett legyen a felhasználók hozzáférése, javasoljuk, hogy használja ki a [Microsoft Cloud App Security alkalmazást.](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) Ez biztosítja a felhőalapú alkalmazásokhoz való vállalati hozzáférést a rendszergazdai fiókok védelme mellett azáltal, hogy lehetővé teszi a következőket:

* A felhőalapú alkalmazások láthatóságának és vezérlésnek a kiterjesztése
* Hozzáférési, tevékenységi és adatmegosztási házirendek létrehozása
* A kockázatos tevékenységek, a rendellenes viselkedések és a fenyegetések automatikus azonosítása
* Adatszivárgás megelőzése
* A kockázatok és az automatizált veszélymegelőzés és a házirendek kényszerítésének minimalizálása

A Cloud App Security SIEM-ügynök integrálja a Cloud App Security alkalmazást a SIEM-kiszolgálóval, hogy lehetővé tegye az Office 365-riasztások és tevékenységek központosított figyelését. A kiszolgálón fut, és lekéri a riasztásokat és tevékenységeket a Cloud App Security-től, és továbbítja azokat a SIEM-kiszolgálóra. További információ: [SIEM integráció](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>4. szakasz: Folytassa a védelem kiépítését egy proaktívabb biztonsági testtartásra

![4. szakasz elfogadja a proaktív biztonsági testtartás](./media/directory-admin-roles-secure/stage-four.png)

Az ütemterv 4. Az ütemterv kitöltésével erős, kiemelt hozzáférési védelmet fejleszthet ki a jelenleg ismert és ma elérhető lehetséges támadásokkal szemben. Sajnos a biztonsági fenyegetések folyamatosan fejlődnek és eltolódnak, ezért azt javasoljuk, hogy a biztonságot olyan folyamatos folyamatként tekintse meg, amely a költségek növelésére és a környezetet célzó ellenfelek sikerességi arányának csökkentésére összpontosít.

A kiemelt hozzáférés biztosítása kritikus első lépés az üzleti eszközök revonatkozó biztonsági garanciáinak létrehozásához egy modern szervezetben, de nem ez az egyetlen olyan része a teljes biztonsági programnak, amely olyan elemeket tartalmaz, mint például a házirend, a műveletek, az információk a biztonság, a kiszolgálók, az alkalmazások, a számítógépek, az eszközök, a felhőalapú háló és más összetevők folyamatos biztonsági garanciákat nyújtanak. 

A kiemelt hozzáférési fiókok kezelése mellett azt javasoljuk, hogy folyamatosan tekintse át az alábbiakat:

* Győződjön meg arról, hogy a rendszergazdák a napi üzleti, mint jogosultsággal nem rendelkező felhasználók.
* Csak akkor adjon jogosultsággal rendelkező hozzáférést, ha szükséges, és távolítsa el később (just-in-time).
* A kiemelt fiókokhoz kapcsolódó ellenőrzési tevékenység megőrzése és felülvizsgálata.

A teljes biztonsági ütemterv létrehozásáról a [Microsoft felhőinformatikai architektúra erőforrásai](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)című témakörben talál további információt. Ha többet szeretne tudni arról, hogy miként veheti igénybe a Microsoft-szolgáltatásokat a témakörök bármelyikének megsegítésére, forduljon a Microsoft képviselőjéhez, vagy olvassa el [a Kritikus fontosságú kibervédelem készítése a vállalat védelme érdekében című témakört.](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)

A biztonságos jogosultsággal rendelkező hozzáférés ütemtervének ez a végső folyamatban lévő szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="review-admin-roles-in-azure-active-directory"></a>Felügyeleti szerepkörök áttekintése az Azure Active Directoryban 

Határozza meg, hogy a jelenlegi beépített Azure AD-rendszergazdai szerepkörök még mindig naprakészek-e, és győződjön meg arról, hogy a felhasználók csak azokban a szerepkörökben és delegálásokban vannak, amelyekre szükségük van a megfelelő engedélyekhez. Az Azure AD segítségével kijelölhet külön rendszergazdákat a különböző funkciók kiszolgálására. További információt a [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban című témakörben talál.](directory-assign-admin-roles.md)

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Az Azure AD-hez csatlakozott eszközök felügyeletét végző felhasználók áttekintése

További információ: [Hibrid Azure Active Directoryhoz csatlakozó eszközök konfigurálása.](../device-management-hybrid-azuread-joined-devices-setup.md)

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>A [beépített Office 365 rendszergazdai szerepkörök tagjainak áttekintése](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Ha office 365-öt használ.
‎
#### <a name="validate-incident-response-plan"></a>Incidens-választerv ellenőrzése

A csomag továbbfejlesztése érdekében a Microsoft azt javasolja, hogy rendszeresen ellenőrizze, hogy a csomag a várt módon működik-e:

* Tekintse át a meglévő útitervet, és nézze meg, mi hiányzott
* A postmortem elemzés alapján vizsgálja felül a meglévő vagy az új ajánlott eljárásokat
* Annak biztosítása, hogy a frissített incidensválasz-terv és az ajánlott eljárások el legyenek osztva a szervezetben


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépések az Azure-hoz való hozzáférést kezelő szervezetek számára 

Határozza meg, hogy át [kell-e ruháznia egy Azure-előfizetés tulajdonjogát egy másik fiókra.](../../cost-management-billing/manage/billing-subscription-transfer.md)
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Üvegtörés": mi a teendő vészhelyzetben

![Számlák a sürgősségi szünet üveg hozzáférés](./media/directory-admin-roles-secure/emergency.jpeg)

1. Értesítse a kulcsvezetőket és a biztonsági tisztviselőket az incidenssel kapcsolatos releváns információkkal.

2. Tekintse át a támadásforgatókönyvét. 

3. Az Azure AD-be való bejelentkezéshez férjen hozzá a "tördüveg" fiók felhasználónevének/jelszavának a használatával. 

4. Kérjen segítséget a Microsofttól [egy Azure-támogatási kérelem megnyitásával.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

5. Tekintse meg az [Azure AD bejelentkezési jelentéseket.](../reports-monitoring/overview-reports.md) Előfordulhat, hogy egy esemény bekövetkezése és a jelentésben szereplő esemény között késés van.

6. Hibrid környezetekben, ha az összevont és az AD FS-kiszolgáló nem érhető el, előfordulhat, hogy ideiglenesen át kell váltania az összevont hitelesítésről a jelszókivonat-szinkronizálás használatához. Ez visszaállítja a tartományösszevonást felügyelt hitelesítésre, amíg az AD FS-kiszolgáló elérhetővé nem válik.

7. A kiemelt jogosultságú fiókok e-mailjeinek figyelése.

8. Győződjön meg róla, hogy mentse a megfelelő naplók biztonsági mentését a lehetséges törvényszéki és jogi vizsgálathoz.

Arról, hogy a Microsoft Office 365 hogyan kezeli a biztonsági incidenseket, a [Microsoft Office 365 biztonsági incidensek kezelése című témakörben talál](https://aka.ms/Office365SIM)további információt.

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>GYIK: Gyakori kérdések, amelyeket a kiváltságos hozzáférés biztosításával kapcsolatban kapunk  

**K:** Mit tegyek, ha még nem valósítottam meg biztonságos hozzáférési összetevőket?

**Válasz:** Definiáljon legalább két törésüveg-fiókot, rendeljen többfa-t a kiemelt rendszergazdai fiókokhoz, és különítse el a felhasználói fiókokat a globális rendszergazdai fiókoktól.

**K:** A jogsértés után mi a legfontosabb probléma, amellyel először foglalkozni kell?

**Válasz:** Győződjön meg arról, hogy a legszigorúbb hitelesítést igényli a nagy védelmet leplezésnek kitett személyek számára.

**K:** Mi történik, ha a kiemelt jogosultságú adminisztrátorainkat deaktiválták?

**Válasz:** Hozzon létre egy globális rendszergazdai fiókot, amely mindig naprakész.

**K:** Mi történik, ha már csak egy globális admin maradt, és nem érhető el? 

**Válasz:** Használja az egyik törésüveg-fiókját, hogy azonnali, kiváltságos hozzáférést kapjon.

**K:** Hogyan védhetem meg a szervezeten belüli rendszergazdákat?

**Válasz:** A rendszergazdák mindig a napi üzleti, mint standard "jogosultsággal nem rendelkező" felhasználók.

**K:** Melyek az azure-beli rendszergazdai fiókok létrehozásának ajánlott eljárásai?

**Válasz:** Kiemelt hozzáférés lefoglalása adott rendszergazdai feladatokhoz.

**K:** Milyen eszközök léteznek az állandó rendszergazdai hozzáférés csökkentésére?

**Válasz:** Kiemelt identitáskezelés (PIM) és az Azure AD-rendszergazdai szerepkörök.

**K:** Mi a Microsoft álláspontja a rendszergazdai fiókok Azure AD-vel való szinkronizálásával?

**Válasz:** A tier 0 rendszergazdai fiókokat (beleértve a fiókokat, csoportokat és egyéb eszközöket, amelyek közvetlen vagy közvetett felügyeleti vezérléssel rendelkeznek az AD-erdő, a tartományok vagy a tartományvezérlők és az összes eszköz felett) csak helyszíni AD-fiókokhoz használják, és általában nem szinkronizálódnak az Azure AD-vel a felhőben.

**K:** Hogyan tarthatjuk meg a rendszergazdákat attól, hogy véletlenszerű rendszergazdai hozzáférést rendelhessenek a portálon?

**Válasz:** Nem kiemelt jogosultságú fiókok használata minden felhasználó és a legtöbb rendszergazda számára. Kezdje azzal, hogy a szervezet alapigényének fejlesztésével határozza meg, hogy mely néhány rendszergazdai fiók legyen kiemelt jogosultsággal. És figyelje az újonnan létrehozott rendszergazdai felhasználók számára.

## <a name="next-steps"></a>További lépések

* [Microsoft Adatvédelmi központ termékbiztonságért](https://www.microsoft.com/trustcenter/security) – A Microsoft felhőalapú termékeinek és szolgáltatásainak biztonsági szolgáltatásai

* [Microsoft Adatvédelmi központ – Megfelelőség](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – a Microsoft átfogó megfelelőségi ajánlatai a felhőszolgáltatásokhoz

* [Útmutató a kockázatértékelés elvégzéséhez](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – A Microsoft felhőszolgáltatásaira vonatkozó biztonsági és megfelelőségi követelmények kezelése

### <a name="other-microsoft-online-services"></a>Egyéb Microsoft online szolgáltatások

* [Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) – Az Intune mobileszköz-felügyeleti, mobilalkalmazás-kezelési és számítógép-felügyeleti lehetőségeket biztosít a felhőből.

* [Microsoft Dynamics 365 biztonság](https://www.microsoft.com/trustcenter/security/dynamics365-security) – A Dynamics 365 a Microsoft felhőalapú megoldása, amely egyesíti az ügyfélkapcsolat-kezelést (CRM) és a vállalati erőforrás-tervezési (ERP) képességeket.
