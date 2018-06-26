---
title: Az identitás-infrastruktúra az Azure Active Directory biztonságossá öt lépése
description: Ez a dokumentum ismerteti a rendszergazdáknak meg kell valósítania felhasználóinál biztonságos a szervezet az Azure AD-funkciókkal fontos műveletek listája
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: b15fff6e868bfac973f9d2a7277f0fac1e29d845
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938424"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Az identitás-infrastruktúra védelmének biztosítása öt lépése

Ha ez a dokumentum olvasása közben, Ön biztonsági vételének tudomást. Valószínűleg már hajt az ezzel a szervezet biztonságossá tételéhez. Ha kell győzni a felhasználót, mások biztonsági fontosságát, küldje el a olvassa el a legújabb [Microsoft biztonsági Eszközintelligencia-jelentés](https://www.microsoft.com/security/intelligence-report).

Ez a dokumentum segítségével egy biztonságosabb állapotát, a képességek az Azure Active Directory segítségével a szervezet számítógépes-támadások elleni oltsuk öt lépés ellenőrzőlista segítségével.

Az alábbi ellenőrzőlista segítségével gyorsan üzembe helyezhet kritikus javasolt művelet a szervezet azonnal védelmét ismertető útmutató:

* A hitelesítő adatok védelme érdekében.
* Csökkentheti a támadási felületét.
* Fenyegetés-válasz automatizálásához.
* A irányuljon a naplózás és figyelés.
* Engedélyezze a több előre jelezhető és teljes végfelhasználói biztonságának és az önsegítő technikai támogatást kaphat.

> [!NOTE]
> Ebben a dokumentumban szereplő javaslatok érvényesek, csak az alkalmazásra, amely az identitás-szolgáltatóként Azure Active Directory használatára van konfigurálva. Alkalmazások konfigurálása az egyszeri bejelentkezés biztosítja a hitelesítő adatok házirendek, a fenyegetésészlelés, előnyeit naplózási naplózás és egyéb szolgáltatások adja hozzá ezeket az alkalmazásokat. [Egyszeri bejelentkezés Azure Active Directoryn keresztül](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) a foundation - összes ezek az ajánlások van.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Előkészületek: védeni a multi-factor Authentication szolgáltatás a kiemelt jogosultságú fiókok

Az alábbi ellenőrzőlista megkezdése előtt győződjön meg arról, nem feltört közben az alábbi ellenőrzőlista olvasása közben. Először a kiemelt jogosultságú fiókok védelme.

Védje meg kiemelt jogosultságú fiókok támadó rengeteg kárt teheti meg, hogy kritikus védelméhez először ezeket a fiókokat. Engedélyezése és kötelezővé [Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) segítségével a szervezet összes rendszergazdák számára [védelem alapkonfigurációt](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Ha nincs megvalósítva az MFA, most megtenni! Fontos, hogy.

Minden beállítás? Lássunk neki az ellenőrzőlista a.

## <a name="step-1---strengthen-your-credentials"></a>1. lépés – a hitelesítő adatok megerősítése 

A legtöbb vállalati biztonsági problémák egy olyan fiókkal, számos módszer például a jelszó megfelelő, szabálysértésnek minősülő ismétlési vagy adathalász egyike sérült származnak. További tudnivalók az ilyen támadások, ez a videó:
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

Ha az azonosítási rendszer felhasználók gyenge jelszóval, és nem a multi-factor Authentication hitelesítéshez megerősítése, azt nem kell, ha, illetve hogy feltört – csak "milyen gyakran."

### <a name="make-sure-your-organization-use-strong-authentication"></a>Győződjön meg arról, hogy a szervezet erős hitelesítés használata

Jelszavak alatt kitalál, phished, kártevő szoftverrel ellopott, vagy használja fel újra, gyakoriságát megadott kiemelten fontos annak a jelszó erős hitelesítő valamilyen biztonsági – további információ [Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>Kapcsolja ki a hagyományos összetettségét, lejárati szabályok, és általában megtámadott jelszavak helyett tiltó indítása

Számos szervezet használ, a hagyományos összetettségét (például különleges karaktereket) és a jelszó lejárati szabályok. A Microsoft research azt mutatják ezek a házirendek káros, kiválaszthatja, hogy könnyebben kitalálni a felhasználók okozza.

A Microsoft ajánlásokat, konzisztens [NIST útmutatást](https://pages.nist.gov/800-63-3/sp800-63b.html), kell végrehajtaniuk a következő három:

1. Szükséges jelszó legalább 8 karakterből kell állnia. Már nem szükségszerűen jobb, azok oka, hogy a felhasználók előre jelezhető választására, menthetik a jelszavakat a fájlokat, vagy írja le.
2. Tiltsa le a lejárati szabályok, amelyek könnyen becsült jelszavak, például a felhasználók meghajtó **Summer2018!**.
3. Tiltsa le a karakter-összeállítás követelményeknek, és a felhasználók megakadályozása kiválasztása általában megtámadott jelszavak, mivel azok a felhasználók kiválaszthatják, előre jelezhető karakter helyettesítő jelszavak okoznának.

Használhat [PowerShell segítségével megakadályozhatja, hogy a jelszavak lejárjanak](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) felhasználókat, ha közvetlenül az Azure AD-ben identitások hoz létre. A helyszíni használó AD az Azure AD Connect szinkronizálási identitások az Azure AD (más néven a hibrid telepítés), hogy meg kell valósítania a helyszíni [intelligens jelszóházirendek](https://aka.ms/passwordguidance) használatával [tartományi csoportházirend beállítások](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) vagy [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

Az Azure Active Directory [dinamikus tiltott jelszó](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) szolgáltatás jelenlegi támadó viselkedése használva akadályozza meg, amely lehet könnyen kitalálni jelszavak beállítása. Ez a lehetőség mindig be kapcsolva, és a szervezetek hibrid telepítés is kihasználhatja le ez a funkció engedélyezésével [jelszóvisszaírás](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback) vagy telepíthet [az Azure AD jelszavas védelmet a Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Az Azure AD jelszavas védelem megakadályozza a felhasználókat közös jelszót válasszon általánosságban, és egyéni jelszavak konfigurálhatja.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Elleni kiszivárgott hitelesítő adatokat, és adja hozzá a rugalmasság valamilyen okból kimaradás ellen

Ha a szervezet használja a hibrid identitáskezelési megoldás, majd engedélyezze Jelszókivonat-szinkronizálást a következő két okok miatt:

* A [kiszivárgott hitelesítő adatokkal rendelkező felhasználók](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) az Azure AD felügyelet jelentés figyelmezteti felhasználónevet és jelszót tartalmazó, amelyek a weben"sötét." kitett A jelszavak egy hihetetlen kötet kiszivárgott adathalászat, a kártevők és a jelszó újbóli később megsértő külső webhelyeken keresztül. Microsoft megkeresi a hitelesítő adatok szivárgását ezek közül, és jelzi, hogy ebben a jelentésben egyezés hitelesítő adatokat a szervezet –, de csak akkor, ha Ön [engedélyezése a Jelszókivonat-szinkronizálást](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* (Például az egy nevű támadások) helyszíni kimaradás esetén képes vált át lesz [felhőalapú hitelesítés használatával Jelszókivonat-szinkronizálást](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Ez a biztonsági mentési hitelesítési módszer lehetővé teszi az Azure Active Directoryban, beleértve az Office 365-hitelesítésre konfigurálják alkalmazásokhoz fér hozzá a folytatáshoz.

További tudnivalók [Jelszókivonat-szinkronizálást](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) működik.

### <a name="implement-ad-fs-extranet-lockout"></a>Megvalósítása az AD FS extranet kizárás

Konfigurálhatja az alkalmazásokat közvetlenül az Azure AD felé történő hitelesítésre szervezetek kihasználhassa [az Azure AD intelligens kizárás](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Ha az AD FS használatához az AD FS megvalósítására [extranet kizárás](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection). Extranet kizárás találgatásos támadások ellen, melyik céloldali AD FS közben megakadályozza, hogy a felhasználók folyamatban az Active Directoryban történő zárolja véd.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Belsőleg biztonságos, könnyebben hitelesítő adatok használata előnyeinek kihasználása

Használatával [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), jelszavak lecserélheti a számítógépek és mobileszközök erős kéttényezős hitelesítéssel. Ez a hitelesítés felhasználói hitelesítő adatokat, amelyeket egy eszközhöz kötődik, és használja a biometrikus vagy PIN-kód egy új típusú áll.

## <a name="step-2---reduce-your-attack-surface"></a>2. lépés - a támadási felület csökkentése

A megadott jelszó illetéktelen pervasiveness, minimalizálja a támadási felületet a szervezet kritikus. Mivel a kevésbé biztonságos, régebbi protokollok, korlátozza a hozzáférést belépési pontok, és ilyen felügyeleti erőforrásokhoz való hozzáférés jelentősebb ellenőrzést csökkentheti a támadási felületét.

### <a name="block-legacy-authentication"></a>Örökölt hitelesítés letiltása

A hagyományos módszerrel saját hitelesítéséhez az Azure ad-vel vagy fér hozzá vállalati adatokhoz, alkalmazásokat egy másik veszélyt a szervezet számára. A hagyományos hitelesítést használó alkalmazások példák POP3, IMAP4 vagy SMTP ügyfelek. Hitelesítés régebbi alkalmazásokhoz hitelesítéshez a felhasználó nevében, előfordulhat, hogy az Azure AD biztonsági értékelések speciális ezzel. Az alternatív, modern hitelesítést, csökkenti a biztonsági kockázat, mert támogatja a többtényezős hitelesítés és a feltételes hozzáférés. Azt javasoljuk, hogy a következő három műveletet:

1. Blokk [régebbi hitelesítés használatakor az AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. A telepítő [SharePoint Online és Exchange online-hoz a modern hitelesítést használó](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Használjon [feltételes hozzáférési szabályzatokat letilthatja a régebbi hitelesítési](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Blokk érvénytelen hitelesítési belépési pontok

A biztonsági szabályok megsértésére feltételezése mentality használ, csökkentse a felhasználói hitelesítő adatok hatását amikor akkor fordulhat elő. Minden alkalmazás adott környezetben fontolja meg az érvényes használati esetek: mely csoportok, hálózatok, mely eszközök és más elemek jogosult –, akkor a többi letiltása. A használatát, hogy [magas jogosultsági szintű vagy service fiókok](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices). A [Azure AD feltételes hozzáférésével](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), szabályozhatja, hogyan engedélyezett felhasználók hozzáféréssel a alkalmazások és erőforrások adott feltételek alapján határozza meg.

Különösen figyeljen oda szolgáltatási fiókok (a feladatok automatizált módon használt fiókok). A feltételes hozzáférés használatának, biztosíthatja, hogy az ilyen fiókok csak alapján futtathatók a szolgáltatás az IP-címről és nap során, ami megfelelő.

### <a name="implement-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management megvalósítása

Egy másik hatása "feltételezi a biztonsági szabályok megsértésére" hogy annak a valószínűségét, egy sérült biztonságú fiók kapnak egy kiemelt szerepkörhöz. 

[Az Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) jogosultságot segítenek kis méretben nyújt segítséget:

* Határozza meg és rendszergazdai szerepkörök rendelt felhasználók kezeléséhez.
* Ismerje meg, nem használt vagy túlzott jogosultság szerepkörök el kell távolítania.
* Létesíteni, a multi-factor authentication által védett szabályok segítségével győződjön meg arról, hogy kiemelt szerepköröket.
* Győződjön meg arról, hogy a kiemelt szerepköröket csak elegendő ideig kapnak a rendszerjogosultságú feladatnak szabályokat létrehozni.

Azure AD PIM engedélyezése, majd a felhasználók rendszergazdai szerepkört, és távolítsa el a felesleges fiókokat ezeket a szerepköröket a megtekintéséhez. A fennmaradó jogosultsággal rendelkező felhasználók számára helyezze azokat a végleges jogosult. Végezetül létrehozni a megfelelő házirendek, győződjön meg arról, hogy mikor kell azokat kiemelt szerepköröket eléréséhez, akkor teheti biztonságosan.

A magas jogosultsági szintű fiók folyamat történő telepítésének részeként, kövesse a [az ajánlott eljárás az, hogy legalább két vészhelyzeti fiókokat létrehozni](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) ellenőrizze, hogy hozzáférés az Azure AD, ha a kizárhatja magát a.

## <a name="step-3---automate-threat-response"></a>3. lépés - a fenyegetés válasz automatizálásához

Az Azure Active Directory számos lényeges képességét, amely automatikusan intercept támadások, felderítését és a válasz közötti késés eltávolítása rendelkezik. A költségek csökkentése érdekében, és a kockázatok, amikor a idő bűnözők csökkentenie segítségével maguk beágyazása a környezetben. Az alábbiakban a konkrét lépéseket kell elvégeznie.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Az Azure AD Identity Protection használata felhasználói kockázat biztonsági házirend megvalósítása

Felhasználói kockázat azt jelzi, hogy az elágazás a felhasználók személyazonossága feltörték, és kiszámítása a [felhasználói kockázati események](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) , amelyek a felhasználó identitását. Egy felhasználó kockázat házirendnek egy feltételes hozzáférési szabályzatot, amely kiértékeli a kockázati szintjét egy adott felhasználó vagy csoport. Alapján alacsony, közepes, magas kockázatú-, házirend beállítható úgy, hogy engedélyezze a hozzáférést, vagy egy biztonságos jelszó módosítása multi-factor authentication használata szükséges. A Microsoft javasoljuk, hogy biztonságos jelszó előírhatja a felhasználóknak a magas kockázatú.

![Kockázatosként megjelölt felhasználók](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Az Azure AD Identity Protection használatával megvalósítása bejelentkezési kockázat házirendnek

Bejelentkezési kockázat annak a valószínűségét, a fiók tulajdonosa nem próbál bejelentkezés azonosítójának használatával. A [bejelentkezési kockázat házirendnek](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) egy feltételes hozzáférési házirend, amely kiértékeli a kockázati szintjét egy adott felhasználó vagy csoport. A kockázati szintjét (magas/közepes vagy alacsony) alapján, a házirend beállítható úgy, hogy engedélyezze a hozzáférést, vagy a többtényezős hitelesítés kényszerítése. Győződjön meg arról, hogy a többtényezős hitelesítés kényszerítése, közepes vagy újabb kockázat bejelentkezéseket.

![Jelentkezzen be a névtelen IP-címekről](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>4. lépés - a irányuljon

Naplózás és a biztonsági események és a kapcsolódó riasztásokat naplózása egy hatékony védelme stratégia alapvető összetevői. A biztonsági naplók és jelentések elektronikus rekord gyanús tevékenységek és észlelni a lehetséges, hogy sikeres vagy megkísérelt külső behatolást vagy a biztonság a hálózat és a belső támadásokkal jelző minták segítséget nyújtanak. Naplózás segítségével felhasználói tevékenység, a dokumentum előírásoknak való megfelelés figyelése, hajtsa végre a törvényszéki elemzések végrehajtásakor, és több. Riasztások adja meg a biztonsági események értesítések.

### <a name="monitor-azure-ad"></a>A figyelő az Azure AD

A Microsoft Azure-szolgáltatások és funkciók konfigurálható biztonsági naplózás és a naplózási beállítások a biztonsági házirendeknek és mechanizmusoknak a réseinek azonosításában és kezelésében ezen problémák elkerülése érdekében hézagok segítséget nyújtanak. Használható [Azure-naplózás és a naplózási](https://docs.microsoft.com/azure/security/azure-log-audit) és [az Azure Active Directory portálon Tevékenységjelentések naplózási](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Hibrid környezetekben az Azure AD Connect Health figyelése

[Az Azure AD Connect Health AD FS monitoring](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) a lehetséges problémákat és látható-e az AD FS infrastruktúra elleni támadások nagyobb betekintést nyújt. Az Azure AD Connect Health kézbesíti a riasztás részleteit, a megoldási lépések és a hivatkozások kapcsolódó dokumentáció; Használatelemzés az több hitelesítési forgalom; kapcsolódó metrikák teljesítmény- és jelentések.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Az Azure AD Identity Protection események figyelése

[Az Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) értesítés, figyelési és jelentéskészítési eszköz segítségével a szervezet identitásait érintő lehetséges biztonsági rések észlelése. Kockázati eseményekről, például a kiszivárgott hitelesítő adatok, lehetetlen odautazás észlel, és bejelentkezések fertőzött eszközök, névtelen IP-címek, a gyanús tevékenységet, és az ismeretlen helyekhez társított IP-címek. Engedélyezi az értesítési értesítéseket e-mailek veszélyben felhasználók és/vagy heti kivonatoló e-mailek fogadásához.

![Kockázatosként megjelölt felhasználók](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>Enable végfelhasználói önsegítő, 5 -. lépés

Lehetőség szerint érdemes biztonsági elosztása a termelékenység. A témakörgyűjtemény azonos hamarosan eléri a használatában az a, hosszú távon határozza meg a biztonság alaprendszert alaposan eltávolíthat súrlódás a szervezet által közben vigilant biztosítani szeretné a felhasználók. 

### <a name="implement-self-service-password-reset"></a>Önkiszolgáló jelszóváltoztatás megvalósítása

Azure [önkiszolgáló jelszó-változtatási (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) nyújt egy egyszerű azt jelenti, hogy a rendszergazdák számára a felhasználók visszaállíthassák vagy a jelszavak és a rendszergazda beavatkozása nélkül fiókok zárolásának feloldásához. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhozzáféréséről, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre. 

### <a name="implement-self-service-group-management"></a>Önkiszolgáló csoportfelügyelet megvalósítása

Az Azure AD lehetővé teszi biztonsági és Office 365-csoportok használata erőforrásokhoz való hozzáférés kezelése. Ezek a csoportok csoport tulajdonosainak helyett a rendszergazdák által kezelhető. Néven [önkiszolgáló csoportkezelési](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), ez a funkció lehetővé teszi, hogy a csoportházirend-tulajdonosok, nem hozzárendelt felügyeleti csoportok létrehozása és kezelése a kérelmek kezeléséhez a rendszergazdák segítsége nélkül szerepet.

### <a name="implement-azure-ad-access-reviews"></a>Alkalmazzon az Azure AD hozzáférési értékelést

A [ellenőrzi, hogy az Azure AD hozzáférési](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), kezelheti a hozzáférést a vállalati alkalmazásokhoz és a csoporttagságokat, valamint ellenőrizze, hogy egy biztonsági szabvány, amely nem hozzáférést adhat a felhasználók a megmaradjanak kiemelt szerepkör-hozzárendelések kiterjesztett időszakokra, Ha ezeket nem kell az időt.

## <a name="summary"></a>Összegzés

Számos szempontot biztonságos identitás-infrastruktúrát, de ez öt lépés ellenőrzőlista segítségével gyorsan elérheti a biztonságosabb, biztonságos identitás-infrastruktúra:

* A hitelesítő adatok védelme érdekében.
* Csökkentheti a támadási felületét.
* Fenyegetés-válasz automatizálásához.
* A irányuljon a naplózás és figyelés.
* Engedélyezze a több előre jelezhető és teljes végfelhasználói biztonságának és az önsegítő technikai támogatást kaphat.

Köszönjük, hogy hogyan súlyosan identitás biztonsági igénybe vehet, és legyen ez a dokumentum egy biztonságosabb állapotát a szervezet egy hasznos programba.

## <a name="next-steps"></a>További lépések
Ha tervezéséhez és telepítéséhez a javaslatok segítségre van szüksége, tekintse meg a [az Azure AD-projekt központi telepítési csomagok](http://aka.ms/deploymentplans) segítségét.
