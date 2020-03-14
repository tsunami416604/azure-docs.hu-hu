---
title: Ajánlott eljárások a biztonságos rendszergazdai hozzáféréshez – Azure AD | Microsoft Docs
description: Győződjön meg arról, hogy a szervezet rendszergazdai hozzáférési és rendszergazdai fiókjai biztonságosak. Olyan rendszerfejlesztők és informatikai szakemberek számára, akik az Azure AD-t, az Azure-t és a Microsoft Online Servicest konfigurálják.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266272"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Az Azure AD-ben a hibrid és a Felhőbeli üzemelő példányokhoz való emelt szintű hozzáférés biztonságossá tétele

A modern szervezetben a legtöbb vagy az összes üzleti eszköz biztonsága az informatikai rendszereket kezelő és kezelő Kiemelt jogosultságú fiókok integritásának függvénye. A rosszindulatú szereplők, például a Cyber-támadók gyakran a rendszergazdai fiókokat és az emelt szintű hozzáférés más elemeit használják, hogy gyorsan hozzáférjenek a bizalmas adatokhoz és rendszerekhez a hitelesítőadat-lopás elleni támadásokkal. A Cloud Services esetében a megelőzés és a reagálás a felhőalapú szolgáltató és az ügyfél együttes feladatai közé tartozik. A végpontokra és a felhőre vonatkozó legújabb fenyegetésekkel kapcsolatos további információkért tekintse meg a [Microsoft biztonsági intelligencia jelentését](https://www.microsoft.com/security/operations/security-intelligence-report). Ez a cikk segítséget nyújt egy olyan ütemterv kidolgozásához, amely a jelenlegi csomagok és az itt ismertetett útmutató közötti hézagok lezárását célozza.

> [!NOTE]
> A Microsoft elkötelezte magát a megbízhatóság, az átláthatóság, a szabványok és a jogszabályi megfelelőség legmagasabb szintjére. Tudjon meg többet arról, hogy a Microsoft globális incidensek csapatának milyen hatása van a Cloud Services elleni támadások következményeire, és hogy a Microsoft adatvédelmi központ – a Microsoft adatvédelmi [központjában –](https://www.microsoft.com/trustcenter/compliance) [biztonsági](https://www.microsoft.com/trustcenter/security) és Microsoft-megfelelőségi célokból hogyan építhető be a biztonság a Microsoft üzleti termékeibe és a felhőalapú szolgáltatásokba.

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
A legtöbb szervezet esetében az üzleti eszközök biztonsága az informatikai rendszereket kezelő és kezelő Kiemelt jogosultságú fiókok integritásának függvénye. A Cyber-támadók az infrastruktúra-rendszerek (például Active Directory és Azure Active Directory) privilegizált hozzáférésére összpontosítanak ahhoz, hogy hozzáférjenek a szervezet bizalmas adatokhoz. 

Azok a hagyományos megközelítések, amelyek a hálózat beléptetési és kilépési pontjainak védelmére összpontosítanak, mivel az elsődleges biztonsági peremhálózat kevésbé hatékony, mivel az SaaS-alkalmazások és a személyes eszközök használata az interneten. Egy összetett modern vállalat hálózati biztonsági területének természetes cseréje a szervezet identitási rétegének hitelesítési és engedélyezési vezérlői.

A Kiemelt jogosultságú rendszergazdai fiókok hatékonyan felügyelik az új "biztonsági területet". Kritikus fontosságú a Kiemelt hozzáférés biztosítása, függetlenül attól, hogy a környezet helyszíni, Felhőbeli vagy hibrid helyszíni és felhőben üzemeltetett szolgáltatás-e. A rendszergazdai hozzáférés a meghatározott ellenfelekkel szembeni védelme megköveteli, hogy teljes és átgondolt megközelítést nyújtson a szervezet rendszereinek kockázatokból való elkülönítéséhez. 

Az emelt szintű hozzáférés biztonságossá tétele a következő módosításokat igényli

* Folyamatok, felügyeleti gyakorlatok és tudásmenedzsment
* Technikai összetevők, mint például a gazdagépek védelme, a fiókok védelme és az Identitáskezelés

Ez a dokumentum elsősorban az Azure AD-ben, az Microsoft Azure-ban, az Office 365-ben és más felhőalapú szolgáltatásokban kezelt vagy jelentett identitások és hozzáférések védelmének ütemtervét ismerteti. A helyszíni rendszergazdai fiókokkal rendelkező szervezetek esetében tekintse meg a helyi és a hibrid rendszerjogosultságú hozzáféréshez felügyelt, Active Directory a [privilegizált hozzáférés biztonságossá tétele](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)című témakör útmutatását. 

> [!NOTE] 
> A jelen cikkben található útmutatás elsősorban a P1 és P2 csomagokban található Azure Active Directory prémium szintű Azure Active Directory-funkciókra vonatkozik. A P2 prémium szintű Azure Active Directory az EMS E5 Suite és a Microsoft 365 E5 csomag része. Ez az útmutató feltételezi, hogy a szervezete már rendelkezik a felhasználók számára megvásárolt prémium szintű Azure AD P2-licencekkel. Ha nem rendelkezik ezekkel a licencekkel, előfordulhat, hogy néhány útmutató nem vonatkozik a szervezetére. Emellett a jelen cikkben a globális rendszergazda kifejezés (vagy globális rendszergazda) szinonimája a "vállalati rendszergazda" vagy a "bérlői rendszergazda".

## <a name="develop-a-roadmap"></a>Ütemterv kidolgozása 

A Microsoft azt javasolja, hogy fejlesszen és kövessen el egy ütemtervet, amely biztonságos hozzáférést biztosít a Cyber-támadók ellen. Az ütemtervet bármikor módosíthatja, hogy megfeleljen a meglévő képességeinek és a szervezeten belül meghatározott követelményeknek. Az ütemterv minden szakaszának meg kell növelnie a költségeket és a nehézségeket, hogy a támadók a helyi, a Felhőbeli és a hibrid eszközökre emelt szintű hozzáférést kapjanak. A Microsoft a következő négy ütemtervi szakaszt javasolja: ez az ajánlott ütemterv a leghatékonyabb és a leggyorsabb megvalósításokat először a Microsoft által a Cyber-támadási incidensek és a reagálások megvalósításával kapcsolatos tapasztalatok alapján ütemezheti. Az ütemterv ütemterve hozzávetőleges.

![Az ütemterv szakaszai a Time Lines szolgáltatással](./media/directory-admin-roles-secure/roadmap-timeline.png)

* 1\. fázis (24-48 óra): a kritikus fontosságú elemek, amelyekről javasoljuk, hogy azonnal elvégezze

* 2\. fázis (2-4 hét): a leggyakrabban használt támadási technikák enyhítése

* 3\. fázis (1-3 hónap): a felügyeleti tevékenység kiépítése és teljes körű vezérlése

* 4\. fázis (hat hónap és újabb): a védelmi platform további megerősítése érdekében folytassa a fejlesztést.

Ez az ütemterv-keretrendszer úgy lett kialakítva, hogy maximalizálja a már üzembe helyezett Microsoft-technológiák használatát. Emellett kihasználhatja a jelenlegi és a közelgő biztonsági technológiák előnyeit, és integrálhatja azokat a más gyártóktól származó biztonsági eszközöket, amelyeket már üzembe helyezett vagy üzembe helyez. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>1\. fázis: a kritikus fontosságú elemek, melyeket ajánlott azonnal elvégeznie

![1\. lépés a kritikus elemek első lépése](./media/directory-admin-roles-secure/stage-one.png)

Az ütemterv 1. fázisa a gyors és könnyen megvalósítható kritikus fontosságú feladatokra összpontosít. Azt javasoljuk, hogy ezeket a néhány elemet az első 24-48 órán belül el kell végeznie, hogy biztosítsa az alapszintű biztonságos jogosultságok elérését. A védett privilegizált hozzáférési ütemterv ezen szakasza a következő műveleteket tartalmazza:

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management bekapcsolása

Ha még nincs bekapcsolva a Azure AD Privileged Identity Management (PIM), ezt az üzemi bérlőben teheti meg. A Privileged Identity Management bekapcsolását követően értesítési e-mail-üzeneteket fog kapni az emelt szintű hozzáférési szerepkör változásairól. Ezek az értesítések korai figyelmeztetést nyújtanak, ha további felhasználókat adnak hozzá a címtár magas jogosultsági szintű szerepköreihez.

Azure AD Privileged Identity Management a P2 vagy az EMS E5 prémium szintű Azure AD része. Ezek a megoldások segítenek a helyszíni környezetben és a felhőben lévő alkalmazásokhoz és erőforrásokhoz való hozzáférés védelmében. Ha még nem rendelkezik prémium szintű Azure AD P2 vagy EMS E5-vel, és szeretné kiértékelni az ebben az ütemtervben hivatkozott funkciókat, regisztráljon a [Enterprise Mobility + Security ingyenes 90 napos próbaverzióra](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Ezekkel a licencelési kísérletekkel kipróbálhatja Azure AD Privileged Identity Management és Azure AD Identity Protection, hogy figyelje a tevékenységeket az Azure AD speciális biztonsági jelentéskészítési, naplózási és riasztási szolgáltatásával.

A Azure AD Privileged Identity Management bekapcsolása után:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely az éles bérlő globális rendszergazdája.

2. A Privileged Identity Management használni kívánt bérlő kiválasztásához válassza ki a felhasználónevet a Azure Portal jobb felső sarkában.

3. A Azure Portal menüben válassza a **minden szolgáltatás** lehetőséget, és szűrje a **Azure ad Privileged Identity Management**listáját.

4. Nyissa meg Privileged Identity Management a **minden szolgáltatás** listából, és rögzítse az irányítópulton.

A bérlőben a Azure AD Privileged Identity Management használatának első személye automatikusan hozzá lesz rendelve a **biztonsági rendszergazda** és a **Kiemelt szerepkörű rendszergazda** szerepkörhöz a bérlőben. Csak a Kiemelt szerepkörű rendszergazdák kezelhetik a felhasználók Azure AD-címtárbeli szerepkör-hozzárendeléseit. Emellett Azure AD Privileged Identity Management hozzáadása után megjelenik a biztonsági varázsló, amely végigvezeti Önt a kezdeti felderítési és hozzárendelési élményen. A varázslóból a további módosítások végrehajtása nélkül is kiléphet. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Magas jogosultsági szintű szerepkörökben lévő fiókok azonosítása és kategorizálása 

Azure AD Privileged Identity Management bekapcsolását követően tekintse meg a címtárbeli szerepkörök globális rendszergazdája, a Kiemelt szerepkörű rendszergazda, az Exchange Online-rendszergazda és a SharePoint Online-rendszergazda felhasználói számára. Ha nem rendelkezik az Azure AD PIM-vel a bérlőben, használhatja a [POWERSHELL API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)-t. Kezdje a globális rendszergazdai szerepkörrel, mivel ez a szerepkör általános: a rendszergazdai szerepkörhöz rendelt felhasználó ugyanazokkal az engedélyekkel rendelkezik az összes olyan felhőalapú szolgáltatáshoz, amelyhez a szervezet előfizetett, függetlenül attól, hogy a szerepkör hozzá lett-e rendelve a Microsoft 365 felügyeleti központ, a Azure Portal, vagy a Microsoft PowerShellhez készült Azure AD-modul használatával. 

Távolítsa el azokat a fiókokat, amelyekre már nincs szükség ezekben a szerepkörökben. Ezután kategorizálja a rendszergazdai szerepkörökhöz rendelt többi fiókot:

* Egyénileg rendelhető hozzá a rendszergazda felhasználókhoz, és nem rendszergazdai célokra is használható (például személyes e-mail-cím)
* Egyénileg rendelhető hozzá a rendszergazda felhasználókhoz, és csak felügyeleti célokra van kijelölve
* Több felhasználó között megosztott
* Az eseti vészhelyzetek eléréséhez
* Automatikus parancsfájlok esetén
* Külső felhasználók számára

#### <a name="define-at-least-two-emergency-access-accounts"></a>Adjon meg legalább két vészhelyzeti hozzáférési fiókot 

Győződjön meg arról, hogy nem kap olyan helyzetet, amelyben véletlenül kizárják az Azure AD-bérlő felügyeletét, mert nem tud bejelentkezni vagy aktiválni egy meglévő egyéni felhasználói fiókot rendszergazdaként. Ha például a szervezetet egy helyszíni identitás-szolgáltatóhoz összevonták, akkor előfordulhat, hogy az identitás szolgáltatója nem érhető el, így a felhasználók nem jelentkezhetnek be a helyszínen. A rendszergazdai hozzáférés véletlen hiányának hatására úgy csökkenthetők, hogy a bérlőn két vagy több vészhelyzeti hozzáférési fiókot is tárol.

A vészhelyzeti hozzáférési fiókok segítenek a szervezeteknek a meglévő Azure Active Directory-környezetekben korlátozni a rendszerjogosultságú hozzáférést. Ezek a fiókok Kiemelt jogosultságokkal rendelkeznek, és nem adott személyekhez vannak rendelve. A vészhelyzeti hozzáférési fiókok esetében a "break Glass" olyan helyzetekben van korlátozva, ahol a normál rendszergazdai fiókok nem használhatók. A szervezeteknek biztosítaniuk kell a vészhelyzeti fiók használatának szabályozását és csökkentését arra az időre, amelyre szükség van.

Értékelje ki azokat a fiókokat, amelyek a globális rendszergazdai szerepkörhöz vannak rendelve vagy jogosultak. Ha nem lát kizárólag Felhőbeli fiókokat a *. onmicrosoft.com tartomány használatával (a "break Glass" vészhelyzeti hozzáféréshez készült), hozza létre őket. További információ: [a sürgősségi hozzáférés felügyeleti fiókjainak kezelése az Azure ad-ben](directory-emergency-access.md).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>A többtényezős hitelesítés bekapcsolása és az összes többi magas jogosultságú, egyfelhasználós, nem összevont rendszergazdai fiók regisztrálása

Azure Multi-Factor Authentication (MFA) megkövetelése bejelentkezéskor az összes olyan felhasználó számára, aki véglegesen hozzá van rendelve egy vagy több Azure AD-rendszergazdai szerepkörhöz: globális rendszergazda, Kiemelt szerepkörű rendszergazda, Exchange Online-rendszergazda és SharePoint Online rendszergazda. Az útmutató segítségével engedélyezheti a [többtényezős hitelesítés (MFA) használatát a rendszergazdai fiókokhoz](../authentication/howto-mfa-userstates.md) , és biztosíthatja, hogy minden felhasználó regisztrálva legyen [https://aka.ms/mfasetup](https://aka.ms/mfasetup). További információt a 2. lépés és a útmutató 3. lépése tartalmaz az [Office 365 adatokhoz és szolgáltatásokhoz való hozzáférésének védelme](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)című témakörben. 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>2\. fázis: a leggyakrabban használt támadási technikák enyhítése

![2\. fázis – a gyakran használt támadások enyhítése](./media/directory-admin-roles-secure/stage-two.png)

Az ütemterv 2. szakasza arra összpontosít, hogy enyhítse a hitelesítő adatok ellopásának és visszaélésének leggyakrabban használt támadási módszereit, és körülbelül 2-4 hétig valósítható meg. A védett privilegizált hozzáférési ütemterv ezen szakasza a következő műveleteket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Szolgáltatások, tulajdonosok és rendszergazdák leltárának elvégzése

A saját eszközökön (BYOD) és a munkahelyen belüli szabályzatok növekedésével, valamint a vállalatok vezeték nélküli kapcsolatainak növekedésével a hálózattal való kapcsolat figyelése rendkívül fontos. A hatékony biztonsági naplózás gyakran mutatja a hálózaton futó, az általa nem támogatott eszközöket, alkalmazásokat és programokat, így nem biztos, hogy nem biztonságos. További információ: az [Azure biztonsági felügyelete és monitorozása – áttekintés](../../security/fundamentals/management-monitoring-overview.md). Győződjön meg arról, hogy a leltári folyamat az alábbi feladatokat tartalmazza. 

* Azonosítsa azokat a felhasználókat, akik rendszergazdai szerepkörökkel rendelkeznek, és azokat a szolgáltatásokat, amelyeken kezelhető.
* Az Azure AD PIM segítségével megtudhatja, hogy a szervezeten belül mely felhasználók rendelkeznek rendszergazdai hozzáféréssel az Azure AD-hez, beleértve az 1. szakaszban felsorolt további szerepköröket is.
* Az Azure AD-ben definiált szerepkörökön túl az Office 365 olyan rendszergazdai szerepköröket tartalmaz, amelyeket a szervezet felhasználói számára hozzárendelhet. Az egyes rendszergazdai szerepkörök közös üzleti funkciókra mutatnak, és lehetővé teszi, hogy a szervezeten belül a [Microsoft 365 felügyeleti központban](https://admin.microsoft.com)meghatározott feladatokat végezzenek. A Microsoft 365 felügyeleti központ segítségével megtudhatja, hogy a szervezete mely felhasználói rendelkeznek rendszergazdai hozzáféréssel az Office 365-hez, beleértve az Azure AD-ben nem felügyelt szerepkörökön keresztül. További információkért lásd: [az office 365 rendszergazdai szerepkörei](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) és [az Office 365-hez készült ajánlott biztonsági eljárások](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Hajtsa végre a leltárt a szervezet más szolgáltatásaiban, például az Azure-ban, az Intune-ban vagy a Dynamics 365-ben.
* Győződjön meg arról, hogy a rendszergazdai fiókok (az adminisztrációs célokra használt fiókok, nem csak a felhasználók napi fiókjai) rendelkeznek a hozzájuk társított e-mail-címekkel, és regisztráltak az Azure MFA használatára, vagy használják az MFA-t a helyszínen.
* Kérje meg a felhasználókat, hogy üzleti szempontból indokolják meg a rendszergazdai hozzáférést.
* Távolítsa el a rendszergazdai hozzáférést azon személyek és szolgáltatások számára, akiknek nincs rá szükségük.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Azonosítsa a Microsoft-fiókokat olyan rendszergazdai szerepkörökben, amelyeket munkahelyi vagy iskolai fiókokra kell váltani

Előfordulhat, hogy a szervezet kezdeti globális rendszergazdája újra felhasználja a meglévő Microsoft-fiók hitelesítő adatait, amikor elkezdték használni az Azure AD-t. Ezeket a Microsoft-fiókokat különálló felhőalapú vagy szinkronizált fiókokkal kell helyettesíteni. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Különálló felhasználói fiókok és a levelezés továbbítása globális rendszergazdai fiókokhoz 

A globális rendszergazdai fiókok nem rendelkezhetnek személyes e-mail-címekkel, mivel a személyes e-mail-fiókokat rendszeresen a Cyber-támadók végzik. Az internetes kockázatok (adathalászat-támadások, szándékos webes böngészés) rendszergazdai jogosultságokkal való elkülönítésének elősegítése érdekében hozzon létre dedikált fiókokat minden rendszergazdai jogosultságokkal rendelkező felhasználó számára. 

Ha még nem tette meg, hozzon létre külön fiókokat a felhasználók számára a globális rendszergazdai feladatok végrehajtásához, hogy biztosan ne nyissa meg véletlenül az e-maileket, vagy futtassa a rendszergazdai fiókokhoz társított programokat. Győződjön meg arról, hogy ezek a fiókok rendelkeznek e-mail-címmel egy működő postaládába.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Győződjön meg arról, hogy a rendszergazdai fiókok jelszavai nemrég módosultak

Győződjön meg arról, hogy minden felhasználó bejelentkezett a rendszergazdai fiókjaiba, és legalább egyszer módosította a jelszavukat az elmúlt 90 napban. Győződjön meg arról is, hogy minden olyan megosztott fióknál, amelyben több felhasználó is ismeri a jelszót, nemrég módosították a jelszavukat.

#### <a name="turn-on-password-hash-synchronization"></a>Jelszó-kivonat szinkronizálásának bekapcsolása

A jelszó-kivonatolási szinkronizálás egy helyszíni Active Directory-példányról egy felhőalapú Azure AD-példányra történő felhasználói jelszó-kivonatok kivonatának szinkronizálására szolgáló szolgáltatás. Még ha úgy dönt, hogy összevonást használ Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy más identitás-szolgáltatókkal, beállíthatja a jelszó-kivonat szinkronizálását biztonsági mentésként, ha a helyszíni infrastruktúra, például az AD-vagy ADFS-kiszolgálók meghibásodnak vagy válik átmenetileg nem érhető el. Ez lehetővé teszi a felhasználók számára, hogy ugyanazzal a jelszóval jelentkezzenek be a szolgáltatásba, amelyet a helyszíni AD-példányba való bejelentkezéshez használnak. Emellett lehetővé teszi az Identity Protection számára a feltört hitelesítő adatok észlelését is, ha összehasonlítja ezeket a jelszó-kivonatokat a biztonsági rések számára ismert jelszavakkal, ha a felhasználó ugyanazt az e-mail-címet és jelszót használja az Azure AD-hez nem csatlakozó más szolgáltatásokhoz.  További információ: jelszó- [kivonatolási szinkronizálás implementálása Azure ad Connect szinkronizálással](../hybrid/how-to-connect-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Többtényezős hitelesítés (MFA) megkövetelése a felhasználók számára az összes Kiemelt szerepkörben és a felhasználók számára

Az Azure AD azt javasolja, hogy a többtényezős hitelesítés (MFA) megkövetelése az összes felhasználó számára, beleértve a rendszergazdákat és az összes többi felhasználót, akik jelentős hatással lennének a fiók biztonságára (például pénzügyi tisztviselők). Ez csökkenti a támadás kockázatát egy sérült jelszó miatt.

Bekapcsolás:

* [MFA feltételes hozzáférési szabályzatok használatával](../authentication/howto-mfa-getstarted.md) a szervezet összes felhasználója számára.

Ha a vállalati Windows Hello-t használja, akkor az MFA-követelmény a Windows Hello bejelentkezési felületének használatával teljesíthető. További információ: [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Identity Protection konfigurálása 

A Azure AD Identity Protection egy algoritmus-alapú figyelési és jelentéskészítési eszköz, amellyel a szervezet identitásait érintő lehetséges sebezhetőségeket derítheti fel. A gyanús tevékenységekre vonatkozó automatizált válaszokat is konfigurálhat, és megteheti a szükséges lépéseket a megoldásához. További információ: [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Az Office 365 biztonságos pontszámának beszerzése (az Office 365 használata esetén)

A biztonságos pontszám alapján megtekintheti, hogy milyen Office 365-szolgáltatásokat használ (például OneDrive, SharePoint és Exchange), majd a beállításokat és tevékenységeket a Microsoft által létrehozott alaptervhez hasonlítja. A legjobb biztonsági eljárások alapján kapsz egy pontszámot. Bárki, aki rendszergazdai jogosultságokkal rendelkezik (globális rendszergazda vagy egyéni rendszergazdai szerepkör) az Office 365 üzleti prémium vagy nagyvállalati előfizetéshez, elérheti [https://securescore.office.com](https://securescore.office.com/)biztonságos pontszámát.

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Tekintse át az Office 365 biztonsági és megfelelőségi útmutatóját (ha az Office 365-at használja)

A [biztonsági és megfelelőségi terv](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) ismerteti azt a megközelítést, hogy az Office 365-ügyfeleknek hogyan kell konfigurálniuk az Office 365-et, és más EMS-képességeket használni. Ezt követően tekintse át a 3-6-es lépéseket, amelyekkel [biztosíthatja az office 365 adataihoz és szolgáltatásaihoz való hozzáférést](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) , valamint a [Biztonság és megfelelőség figyelését az Office 365-ban](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)című útmutatóban.

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Office 365-tevékenységek figyelése (az Office 365 használata esetén)

Nyomon követheti, hogy a szervezeten belüli felhasználók hogyan használják az Office 365-szolgáltatásokat, így azonosíthatja azokat a felhasználókat, akik rendszergazdai fiókkal rendelkeznek, és akiknek nincs szükségük az Office 365-hozzáférésre, mert nem jelentkezik be a portálra. További információ: [tevékenységi jelentések a Microsoft 365 felügyeleti központban](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Incidens/vészhelyzeti válasz terv tulajdonosainak létrehozása

Az incidensek megválaszolásának hatékony végrehajtása egy összetett vállalkozás. Ezért az incidensek sikeres kiépítésének kialakítása jelentős tervezést és erőforrásokat igényel. Elengedhetetlen, hogy folyamatosan figyelje a számítógépes támadásokat, és eljárásokat hozzon létre az incidensek kezelésének rangsorolásához. Az adatok gyűjtésének, elemzésének és jelentéskészítésének hatékony módszerei elengedhetetlenek a kapcsolatok létrehozásához és a más belső csoportokkal való kommunikáció létrehozásához, valamint a tulajdonosok megtervezéséhez. További információ: [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Biztonságos helyszíni privilegizált rendszergazdai fiókok, ha még nem tette meg

Ha a Azure Active Directory-bérlőt a helyszíni Active Directory szinkronizálja, kövesse a [biztonsági privilegizált hozzáférési ütemterv](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)útmutatását: 1. fázis. Ebbe beletartozik a különálló rendszergazdai fiókok létrehozása azon felhasználók számára, akiknek helyszíni rendszergazdai feladatokat kell elvégezniük, a rendszerjogosultságú hozzáférési munkaállomások üzembe helyezését Active Directory rendszergazdák számára, valamint egyedi helyi rendszergazdai jelszavakat kell létrehozni a munkaállomásokhoz és kiszolgálók.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépések az Azure-hoz való hozzáférést kezelő szervezetek számára

#### <a name="complete-an-inventory-of-subscriptions"></a>Előfizetések leltárának befejezése

Az Enterprise Portal és a Azure Portal segítségével azonosíthatja a szervezetében az üzemi alkalmazásokat futtató előfizetéseket.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Microsoft-fiókok eltávolítása rendszergazdai szerepkörökből

A más programokból (például Xbox, Live és Outlook) származó Microsoft-fiókok nem használhatók a szervezeti előfizetések rendszergazdai fiókjaiként. Távolítsa el a rendszergazdai állapotot az összes Microsoft-fiókból, és cserélje le Azure Active Directory (például chris@contoso.com) munkahelyi vagy iskolai fiókokra.

#### <a name="monitor-azure-activity"></a>Azure-tevékenység figyelése

Az Azure-tevékenység naplója az Azure-beli előfizetési szintű események előzményeit jeleníti meg. Információt nyújt arról, hogy ki hozta létre, frissítette és törölte azokat az erőforrásokat, és hogy mikor történtek ezek az események. További információt az [Azure-előfizetésében található fontos műveletekkel kapcsolatos értesítések naplózása és fogadása](../../azure-monitor/platform/quick-audit-notify-action-subscription.md)című témakörben talál.

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépések a más felhőalapú alkalmazásokhoz való hozzáférést kezelő szervezetek számára az Azure AD-n keresztül

#### <a name="configure-conditional-access-policies"></a>Feltételes hozzáférési szabályzatok konfigurálása

Feltételes hozzáférési szabályzatok előkészítése a helyszíni és a felhőben üzemeltetett alkalmazásokhoz. Ha a felhasználók munkahelyhez csatlakoztatott eszközöket használnak, további információkat kaphat a helyszíni [feltételes hozzáférés beállításáról Azure Active Directory eszköz regisztrációjának használatával](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>3\. fázis: láthatóság kialakítása és a rendszergazdai tevékenység teljes körű felügyelete

![3\. fázis – a rendszergazdai tevékenység vezérlése](./media/directory-admin-roles-secure/stage-three.png)

A 3. fázis a 2. fázis enyhítésére épül, és körülbelül 1-3 hónapban valósítható meg. A védett privilegizált hozzáférési ütemterv ezen szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Rendszergazdai szerepkörrel rendelkező felhasználók hozzáférési felülvizsgálatának befejezése

A vállalati felhasználók egyre magasabb szintű hozzáférést kapnak a Cloud Services szolgáltatáshoz, ami egyre nagyobb, nem felügyelt platformot eredményezhet. Ide tartoznak azok a felhasználók, akik az Office 365, az Azure-előfizetések rendszergazdái és rendszergazdai hozzáférésű virtuális gépekhez vagy SaaS-alkalmazásokon keresztül rendszergazdai hozzáféréssel rendelkező felhasználók. Ehelyett a szervezeteknek minden alkalmazotthoz, különösen rendszergazdákhoz kell tartoznia, a napi üzleti tranzakciókat pedig nem Kiemelt felhasználóként kell kezelniük, és csak a szükséges rendszergazdai jogosultságokat kell igénybe venniük. Mivel a rendszergazdai szerepkörökben lévő felhasználók száma a kezdeti elfogadás óta megnőtt, a hozzáférési felülvizsgálatok végrehajtásával azonosíthatja és megerősítheti a rendszergazdai jogosultságok aktiválásához jogosult összes felhasználót. 

Tegye a következőket:

* Határozza meg, hogy mely felhasználók az Azure AD-rendszergazdák, hogyan engedélyezheti az igény szerinti rendszergazdai hozzáférést, valamint a szerepkör alapú biztonsági vezérlőket.
* Olyan felhasználók konvertálása, akik nem rendelkeznek egyértelmű indoklással a rendszergazdai jogosultsági szintű hozzáféréshez egy másik szerepkörhöz (ha nincs erre jogosult szerepkör, távolítsa el őket).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Az erősebb hitelesítés bevezetésének folytatása minden felhasználó számára 

A C-Suite vezetők, a magas szintű vezetők, a kritikus informatikai és biztonsági személyzet, valamint más, magas rendelkezésre állású felhasználók számára a modern, erős hitelesítés, például az Azure MFA vagy a Windows Hello használata szükséges. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Dedikált munkaállomások használata az Azure AD felügyeletéhez

A támadók megkísérelheti a Kiemelt fiókok megcélzását arra, hogy hozzáférjenek a szervezet adataihoz és rendszereihez, hogy megzavarják az adatok integritását és hitelességét olyan kártékony kód révén, amely megváltoztatja a program logikáját, vagy Snoops a rendszergazdának a hitelesítő adatok megadásával. Az emelt szintű hozzáférésű munkaállomások (mancsok) dedikált operációs rendszert biztosítanak az internetes támadásokkal és a veszélyforrási vektorokkal védett bizalmas feladatokhoz. Az érzékeny feladatok és fiókok a napi használatú munkaállomásokon és eszközökön való elkülönítése rendkívül erős védelmet biztosít az adathalász támadások, az alkalmazások és az operációs rendszer sebezhetőségei, a különböző megszemélyesítési támadások és a hitelesítő adatok ellopása elleni támadások, például a billentyűleütés naplózás, pass-The-hash és Pass-The-Ticket. Az emelt szintű hozzáférésű munkaállomások üzembe helyezésével csökkentheti annak a kockázatát, hogy a rendszergazdák rendszergazdai hitelesítő adatokat adjanak meg, kivéve a megerősített asztali környezetet. További információ: [privilegizált hozzáférésű munkaállomások](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>A National Institute of Standards and Technology ajánlásainak áttekintése az incidensek kezeléséhez 

A National Institute of Standards and Technology (NIST) útmutatást nyújt az incidensek kezeléséhez, különösen az incidensekkel kapcsolatos adatok elemzéséhez és az egyes incidensekre vonatkozó megfelelő válaszok meghatározásához. További információ: [(NIST) számítógép biztonsági incidensek kezelési útmutatója (SP 800-61, 2. változat)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Privileged Identity Management (PIM) megvalósítása a JIT számára a további rendszergazdai szerepkörökhöz

Azure Active Directory esetén használja a [Azure ad Privileged Identity Management](../privileged-identity-management/pim-configure.md) képességet. A Kiemelt szerepkörök időkorlátos aktiválása a következőket teszi lehetővé:

* Rendszergazdai jogosultságok aktiválása adott feladat végrehajtásához
* Az MFA betartatása az aktiválási folyamat során
* Értesítéseket használhat a rendszergazdáknak a sávon kívüli változásokról
* Annak engedélyezése, hogy a felhasználók bizonyos jogosultságokat őrizzenek meg előre konfigurált időtartamra
* Engedélyezze a biztonsági rendszergazdáknak az összes privilegizált identitás felderítését, a naplózási jelentések megtekintését és a hozzáférési felülvizsgálatok létrehozását a rendszergazdai jogosultságok aktiválásához jogosult összes felhasználó azonosításához

Ha már használja a Azure AD Privileged Identity Management-t, módosítsa a szükséges időkereteket (például karbantartási időszakok).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Jelszó alapú bejelentkezési protokollok (az Exchange Online használata esetén) való kitettség meghatározása

A múltban a protokollok azt feltételezik, hogy a Felhasználónév/jelszó kombinációk be lettek ágyazva az eszközökbe, e-mail-fiókokba, telefonokra stb. A Felhőbeli számítógépes támadások kockázata azonban azt javasoljuk, hogy azonosítsa az összes olyan felhasználót, aki, ha a hitelesítő adatai illetéktelenek voltak, katasztrofális lehet a szervezet számára, és kizárhatja őket az e-mail-címére a Felhasználónév/jelszó segítségével, erős hitelesítési követelmények és feltételes hozzáférés bevezetésével. [A feltételes hozzáférés használatával blokkolhatja az örökölt hitelesítést](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Tekintse át az [alapszintű hitelesítés](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) az Exchange Online-on keresztül történő blokkolásának részleteit. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Töltse ki az Office 365-szerepkörök felülvizsgálatának értékelését (az Office 365 használata esetén)

Értékelje ki, hogy az összes rendszergazda felhasználó a megfelelő szerepkörökben van-e (törölje és rendelje hozzá újra az értékelés alapján).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Tekintse át az Office 365-ben használt biztonsági incidensek kezelési megközelítését, és hasonlítsa össze a saját szervezetével

Ezt a jelentést a [biztonsági incidensek felügyeletéről töltheti le Microsoft Office 365-es](https://www.microsoft.com/download/details.aspx?id=54302)verzióban.

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Folytassa a helyszíni privilegizált rendszergazdai fiókok biztonságossá tételét

Ha a Azure Active Directory a helyszíni Active Directoryhoz csatlakozik, kövesse a [biztonsági privilegizált hozzáférési ütemterv](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)útmutatását: 2. fázis. Ez magában foglalja a rendszerjogosultságú hozzáférési munkaállomások üzembe helyezését minden rendszergazda számára, a többtényezős hitelesítés megkövetelésével, a TARTOMÁNYVEZÉRLŐk támadási felületének csökkentésével, a tartományok támadási felületének csökkentésével, a támadások észlelésére.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépések az Azure-hoz való hozzáférést kezelő szervezetek számára

#### <a name="establish-integrated-monitoring"></a>Integrált figyelés létrehozása

A [Azure Security Center](../../security-center/security-center-intro.md) az Azure-előfizetések között integrált biztonsági monitorozást és házirend-kezelést biztosít, segít felderíteni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémáját is felhasználhatják.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Kiemelt jogosultságú fiókok leltárba vétele az üzemeltetett Virtual Machines

A legtöbb esetben nem szükséges korlátlan engedélyeket adni a felhasználóknak az Azure-előfizetésekhez vagy-erőforrásokhoz. Az Azure AD-rendszergazdai szerepkörök segítségével elkülönítheti a szervezeten belüli feladatokat, és csak azon felhasználók számára biztosíthatja a hozzáférést, akiknek szükségük van bizonyos feladatok elvégzésére. Például az Azure AD rendszergazdai szerepköreivel engedélyezheti, hogy egy rendszergazda csak az előfizetésben lévő virtuális gépeket kezelhesse, míg egy másik az adott előfizetésben található SQL-adatbázisokat is képes kezelni. További információ: Bevezetés a [szerepköralapú Access Control használatába a Azure Portal](../../role-based-access-control/overview.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>A PIM megvalósítása az Azure AD rendszergazdai szerepköreihez

Az Azure AD rendszergazdai szerepköreivel felügyelheti, felügyelheti és figyelheti az Azure-erőforrásokhoz való hozzáférést. A PIM használata védi a Kiemelt fiókokat a Cyber-támadásoktól a jogosultságok expozíciós idejének csökkentése és a jelentésekkel és riasztásokkal való használatának növelésével. További információ: az [Azure-erőforrásokhoz való RBAC-hozzáférés kezelése Privileged Identity Management használatával](../../role-based-access-control/pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Az Azure log-integrációk használata a kapcsolódó Azure-naplók a SIEM-rendszerbe való küldéséhez 

Az Azure log-integráció lehetővé teszi, hogy az Azure-erőforrásokból származó nyers naplókat integrálja a szervezet meglévő biztonsági információi és eseménykezelő (SIEM) rendszereibe. Az [Azure log Integration](../../security/fundamentals/azure-log-integration-overview.md) Windows-eseményeket gyűjt a Windows Eseménynapló naplóiból, valamint az Azure-beli adatnaplók, Azure Security Center riasztások és az Azure-beli diagnosztikai naplók Azure-erőforrásairól. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>További lépések a más felhőalapú alkalmazásokhoz való hozzáférést kezelő szervezetek számára az Azure AD-n keresztül

#### <a name="implement-user-provisioning-for-connected-apps"></a>Felhasználók üzembe helyezésének megvalósítása a csatlakoztatott alkalmazásokhoz

Az Azure AD segítségével automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását a Felhőbeli (SaaS) alkalmazásokban, például a Dropbox, a Salesforce, a ServiceNow és így tovább. További információ: a [felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások kiépítése az Azure ad-vel](../app-provisioning/user-provisioning.md).

#### <a name="integrate-information-protection"></a>Adatvédelem integrálása

A MCAS lehetővé teszi a fájlok vizsgálatát és a szabályzatok Azure Information Protection besorolási címkék alapján történő beállítását, amely lehetővé teszi a felhőben tárolt adatai jobb láthatóságát és felügyeletét. Megvizsgálhatja és osztályozhatja a fájlokat a felhőben, és alkalmazhatja az Azure Information Protection feliratait. További információ: [Azure Information Protection integráció](https://docs.microsoft.com/cloud-app-security/azip-integration).

#### <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

Konfigurálhatja a feltételes hozzáférést a csoport, a hely és az alkalmazás érzékenysége alapján az [SaaS-alkalmazások](https://azure.microsoft.com/overview/what-is-saas/) és az Azure ad-hez csatlakoztatott alkalmazások számára. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Tevékenységek figyelése a csatlakoztatott felhőalapú alkalmazásokban

Ha biztosítani szeretné, hogy a felhasználók hozzáférése a csatlakoztatott alkalmazásokban is védve legyen, javasoljuk, hogy használja a [Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Ezzel biztosíthatja a vállalati hozzáférését a felhőalapú alkalmazásokhoz a rendszergazdai fiókok biztonságossá tétele mellett azáltal, hogy lehetővé teszi a következőket:

* A Felhőbeli alkalmazások láthatóságának és vezérlésének kiterjesztése
* Szabályzatok létrehozása a hozzáféréshez, a tevékenységekhez és az adatmegosztáshoz
* A kockázatos tevékenységek, a rendellenes viselkedések és a fenyegetések automatikus azonosítása
* Az adatszivárgás megakadályozása
* Csökkentse a kockázatkezelést és az automatizált fenyegetések megelőzését és a házirendek betartatását

Az Cloud App Security SIEM-ügynök integrálja a Cloud App Securityt az SIEM-kiszolgálóval, hogy lehetővé tegye az Office 365-riasztások és-tevékenységek központosított figyelését. Ez a kiszolgálón fut, és lekéri a riasztásokat és tevékenységeket a Cloud App Securityről, és továbbítja azokat az SIEM-kiszolgálóra. További információ: Siem- [integráció](https://docs.microsoft.com/cloud-app-security/siem).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>4\. fázis: a védelem folytatása proaktív biztonsági helyzetbe

![A 4. fázis egy proaktív biztonsági állapotot fogad el](./media/directory-admin-roles-secure/stage-four.png)

Az ütemterv 4. szakasza a 3. szakasz láthatóságát eredményezi, és úgy van kialakítva, hogy hat hónapon belül és azon kívül is megvalósítható legyen. A menetrendek elvégzése révén erős, emelt szintű hozzáférésű védelmet fejleszthet a jelenleg ismert és elérhető potenciális támadásoktól. Sajnos a biztonsági fenyegetések folyamatosan fejlődnek és váltanak át, ezért javasoljuk, hogy a biztonsági mentést a folyamatban lévő folyamatnak megfelelően tekintse át, és csökkentse a környezetét célzó ellenfelek sikerességi arányát.

Az emelt szintű hozzáférés biztonságossá tétele kulcsfontosságú első lépés egy modern szervezet biztonsági garanciáinak létrehozásához, de ez nem az egyetlen olyan teljes biztonsági program része, amely elemeket is tartalmaz, például a szabályzatot, a műveleteket, az információkat a biztonság, a kiszolgálók, az alkalmazások, a számítógépek, az eszközök, a felhőalapú háló és más összetevők biztosítják a folyamatos biztonsági garanciákat. 

A Kiemelt jogosultságú hozzáférési fiókok kezelése mellett azt javasoljuk, hogy az alábbiakat folyamatosan tekintse át:

* Győződjön meg arról, hogy a rendszergazdák végzik a mindennapi üzleti tevékenységet a nem Kiemelt felhasználók számára.
* Szükség esetén csak emelt szintű jogosultsággal rendelkező hozzáférést adjon meg, majd távolítsa el később (igény szerint).
* A Kiemelt fiókokkal kapcsolatos naplózási tevékenység megőrzése és áttekintése.

A teljes biztonsági ütemterv létrehozásával kapcsolatos további információkért lásd: [Microsoft Cloud it Architecture Resources](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Ha többet szeretne megtudni arról, hogy a Microsoft-szolgáltatások hogyan segítik a témakörök bármelyikét, lépjen kapcsolatba Microsoft-képviselőjével, vagy tekintse meg a [vállalata védelme érdekében a kritikus számítógépes védelem létrehozása](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)című témakört.

A védett privilegizált hozzáférési ütemterv ezen végső szakasza a következő összetevőket tartalmazza.

### <a name="general-preparation"></a>Általános előkészítés

#### <a name="review-admin-roles-in-azure-active-directory"></a>Rendszergazdai szerepkörök áttekintése Azure Active Directory 

Annak megállapítása, hogy az aktuális beépített Azure AD-rendszergazdai szerepkörök naprakészek-e, és hogy a felhasználók csak a megfelelő engedélyekhez szükséges szerepkörökben és delegálásokban vannak. Az Azure AD-vel külön rendszergazdákat jelölhet ki a különböző függvények kiszolgálásához. További információ: [rendszergazdai szerepkörök kiosztása a Azure Active Directoryban](directory-assign-admin-roles.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Az Azure AD-hez csatlakoztatott eszközök felügyeletével rendelkező felhasználók áttekintése

További információ: [hibrid Azure Active Directory csatlakoztatott eszközök konfigurálása](../device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>A [beépített Office 365 rendszergazdai szerepkörök](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) tagjainak áttekintése
Ha az Office 365-et használja.
‎
#### <a name="validate-incident-response-plan"></a>Incidens-válasz tervének ellenőrzése

A terv tökéletesítése érdekében a Microsoft azt javasolja, hogy rendszeresen ellenőrizze, hogy a csomag a várt módon működik-e:

* A meglévő útiterv segítségével megtekintheti, hogy mi volt a kihagyva
* A futási elemzéstől függően módosítsa a meglévőket, vagy adjon meg új ajánlott eljárásokat
* Győződjön meg arról, hogy a frissített incidens-válasz terv és az ajánlott eljárások a szervezeten belül vannak elosztva


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>További lépések az Azure-hoz való hozzáférést kezelő szervezetek számára 

Állapítsa meg, hogy [egy Azure-előfizetés tulajdonjogát át kell-e vinni egy másik fiókba](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Break Glass": mi a teendő vészhelyzet esetén

![Fiókok a sürgősségi szüneti üvegek eléréséhez](./media/directory-admin-roles-secure/emergency.jpeg)

1. Az incidenssel kapcsolatos releváns információkkal értesíti a legfontosabb vezetők és biztonsági tisztviselők adatait.

2. Tekintse át a támadás forgatókönyvét. 

3. Az Azure AD-be való bejelentkezéshez nyissa meg az "break Glass" fiók Felhasználónév/jelszó kombinációját. 

4. Kérjen segítséget a Microsofttól [egy Azure-támogatási kérelem megnyitásával](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Tekintse meg az [Azure ad bejelentkezési jelentéseit](../reports-monitoring/overview-reports.md). Előfordulhat, hogy a jelentésben szereplő események között késés történik.

6. Hibrid környezetek esetén, ha összevont és a AD FS-kiszolgáló nem érhető el, előfordulhat, hogy átmenetileg át kell váltania az összevont hitelesítésből a jelszó-kivonat szinkronizálásának használatára. Ezzel visszaváltja a tartományi Föderációt a felügyelt hitelesítésre, amíg az AD FS-kiszolgáló elérhetővé nem válik.

7. A Kiemelt fiókokra vonatkozó e-mailek figyelése.

8. Győződjön meg arról, hogy biztonsági másolatot készít a kapcsolódó naplókról a lehetséges törvényszéki és jogi vizsgálatokhoz.

További információ arról, hogyan kezeli a Microsoft Office 365 a biztonsági incidenseket: [a biztonsági incidensek kezelése Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>GYAKORI kérdések: az emelt szintű hozzáférés biztonságossá tételével kapcsolatban kapott gyakori kérdések  

**K:** Mi a teendő, ha még nem hajtottam végre biztonságos hozzáférési összetevőket?

**Válasz:** Adjon meg legalább két, a fiókhoz tartozó, az MFA-t a Kiemelt rendszergazdai fiókokhoz, és válassza el a felhasználói fiókokat a globális rendszergazdai fiókokból.

**K:** Mi az a leggyakoribb probléma, amelyet először kell megoldani?

**Válasz:** Győződjön meg arról, hogy a legszigorúbb hitelesítést igényli a magasan elérhető személyek számára.

**K:** Mi történik, ha a Kiemelt rendszergazdák inaktiválva vannak?

**Válasz:** Hozzon létre egy globális rendszergazdai fiókot, amely mindig naprakész állapotban van.

**K:** Mi történik, ha csak egy globális rendszergazda van hátra, és nem érhető el? 

**Válasz:** Az egyik, az Ön által használt fiókkal azonnali jogosultsági szintű hozzáférést nyerhet.

**K:** Hogyan biztosíthatom a rendszergazdákat a szervezeten belül?

**Válasz:** A rendszergazdáknak mindig a szokásos "nem privilegizált" felhasználóként kell elvégezniük a mindennapi tevékenységet.

**K:** Mik az ajánlott eljárások az Azure AD-ben rendszergazdai fiókok létrehozásához?

**Válasz:** Emelt szintű hozzáférés az adott rendszergazdai feladatokhoz.

**K:** Milyen eszközök léteznek az állandó rendszergazdai hozzáférés csökkentése érdekében?

**Válasz:** Privileged Identity Management (PIM) és az Azure AD rendszergazdai szerepkörei.

**K:** Mi a Microsoft pozíciója a rendszergazdai fiókok Azure AD-vel való szinkronizálásához?

**Válasz:** A 0. rétegbeli rendszergazdai fiókok (beleértve a fiókokat, a csoportokat és az AD-erdő, a tartományok, a tartományvezérlők és az összes eszköz közvetlen vagy közvetett felügyeletét biztosító eszközöket) csak a helyszíni AD-fiókok esetében használhatók, és általában nem szinkronizálhatók az Azure AD-vel a felhőben.

**K:** Hogyan lehet megőrizni a rendszergazdákat, hogy véletlenszerű rendszergazdai hozzáférést rendeljenek a portálon?

**Válasz:** A nem Kiemelt fiókok használata az összes felhasználó és a legtöbb rendszergazda számára. Kezdje azzal, hogy kifejleszti a szervezeti lábnyomot, és meghatározza, hogy mely néhány rendszergazdai fióknak kell jogosultságot biztosítania. És figyelés az újonnan létrehozott rendszergazda felhasználók számára.

## <a name="next-steps"></a>Következő lépések

* [Microsoft adatvédelmi központ a termékbiztonság számára](https://www.microsoft.com/trustcenter/security) – a Microsoft Cloud Products és Services biztonsági funkciói

* [Microsoft adatvédelmi központ – megfelelőség](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – a Microsoft átfogó megfelelőségi kínálata a Cloud Serviceshez

* [Útmutató a kockázatértékelés végrehajtásához](https://www.microsoft.com/trustcenter/guidance/risk-assessment) – a Microsoft Cloud Services biztonsági és megfelelőségi követelményeinek kezelése

### <a name="other-microsoft-online-services"></a>Egyéb Microsoft Online Services

* [Microsoft Intune biztonság](https://www.microsoft.com/trustcenter/security/intune-security) – az Intune mobileszköz-felügyeletet, mobileszköz-kezelést és számítógép-felügyeleti funkciókat biztosít a felhőből.

* [Microsoft dynamics 365 biztonság](https://www.microsoft.com/trustcenter/security/dynamics365-security) – a Dynamics 365 a Microsoft felhőalapú megoldás, amely egységesíti az Ügyfélkapcsolat-kezelési (CRM) és a vállalati erőforrás-tervezési (ERP) képességeket.
