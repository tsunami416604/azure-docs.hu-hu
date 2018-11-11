---
title: Öt lépést Ön identitáskezelési infrastruktúráját az Azure Active Directory biztonságossá tételéhez
description: Ez a dokumentum ismerteti a rendszergazdáknak meg kell valósítania, amelyekkel biztonságos használatával az Azure AD-képességekhez szervezet fontos műveletek listája
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: cb1c525527f7261c10b502a25b3cab3db89dd85f
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515137"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Öt lépést a személyazonosság-infrastruktúra védelme

Ez a dokumentum acélja épp az, ha biztosan tisztában biztonsági jelentőségét van. Valószínűleg már sokat biztonságossá tétele a vállalata a felelősséget. Ha szeretné a biztonsági fontosságát, másokat meggyőzni a felhasználót, küldje el a olvassa el a legújabb [Microsoft biztonsági információs jelentései](https://www.microsoft.com/security/intelligence-report).

Ez a dokumentum segít egy biztonságosabb állapotáról, az Azure Active Directory képességeit használja ahhoz a kibertámadások ellen a szervezet egy 5-lépés ellenőrzőlista használatával beolvasása.

Az alábbi ellenőrzőlista segít gyorsan üzembe helyezhet a kritikus fontosságú műveleteket a szervezet védelmében azonnal elmagyarázza, hogyan:

* Erősítse meg a hitelesítő adatait.
* A támadási felület csökkentése.
* Veszélyforrások elleni válasz automatizálása.
* Növelje a ismertségét, naplózás és figyelés.
* Lehetővé teszi a több kiszámítható és teljes körű végfelhasználói biztonsági önsegítő együtt.

> [!NOTE]
> Ebben a dokumentumban szereplő javaslatok a alkalmazni csak azon alkalmazásoknak, amelyek az identitás-szolgáltatóként az Azure Active Directory használatára vannak konfigurálva. Alkalmazások konfigurálása az egyszeri bejelentkezés hitelesítőadat-szabályzatok, a fenyegetésészlelés, előnyeinek további naplózási, naplózást és egyéb funkciók hozzá ezeket az alkalmazásokat. [Egyszeri bejelentkezés az Azure Active Directoryn keresztül](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) az alapja – összes ezekkel az ajánlásokkal alapulnak.

Az ajánlások a dokumentum összhangban legyenek a [identitás biztonságos pontszám](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score), egy automatikus az Azure AD-bérlő identitás biztonsági konfigurációfelméréssel. Szervezetek használhatják az identitás biztonságos pontszám oldal az Azure AD portálon való azok aktuális biztonsági beállításait, akkor hajtsa végre az aktuális Microsoft gyakorlati tanácsok a biztonsági a lefedettségi hézagok meghatározása. Minden javaslat megvalósítása a biztonságos pontszám oldalon növelheti a pontszám, és segítségével nyomon követheti az előrehaladást, valamint segítséget nyújtanak az implementáció más hasonló méretű szervezetek vagy az iparág összehasonlítása.

![Biztonságos identitás-pontszám](media/azure-ad/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Előkészületek: védelme MFA hitelesítéssel kiemelt jogosultságú fiókok

Ezzel az ellenőrzőlistával Kezdés előtt győződjön meg arról, miközben acélja épp az ezzel az ellenőrzőlistával, nem feltört. Először a kiemelt jogosultságú fiókok védelméhez.

Kiemelt jogosultságú fiókok feletti beolvasása, akik teheti áttekintse károkat, ezért fontos, hogy ezek a fiókok védelméhez először. Engedélyezze és szükséges [Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) az összes rendszergazda számára a szervezeti használatával [alapvető védelmet](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Ha az MFA még nincs megvalósítva, megteheti most! Fontos, hogy.

Minden állítható be? Első lépések az ellenőrzőlista a.

## <a name="step-1---strengthen-your-credentials"></a>1. lépés – a hitelesítő adatok megerősítése 

A legtöbb vállalati biztonsági résekkel szemben származnak, egy olyan fiókkal, az egyik módszer, például a jelszó megfelelő, illetéktelen behatolás visszajátszását vagy adathalász néhány biztonsága sérült. További információ a ezeket a támadásokat, ez a videó (45 perc):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

Ha a felhasználók az azonosítási rendszer gyenge jelszót, és nem a multi-factor Authentication hitelesítéshez megerősítése, nem csupán néhány Ha, vagy hogy feltört – csak "milyen gyakran."

### <a name="make-sure-your-organization-use-strong-authentication"></a>Győződjön meg arról, hogy a szervezet erős hitelesítés használata

Adott folyamatban kitalálni, jelszavak phished, újra, vagy a kártevő szoftverrel ellopják gyakoriság rendkívül fontos a jelszó erős hitelesítő adatainak valamilyen biztonsági – további információk [Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Gyakran tiltásának kezdő megtámadott jelszavakat, és kapcsolja ki a hagyományos összetettséget és a lejárati szabályokat.

Számos vállalat használja a hagyományos összetettségét (igénylő speciális karaktereket, számokat, nagy,- és kisbetűk) és a jelszó lejárati szabályok. [A Microsoft research](https://aka.ms/passwordguidance) azt mutatják, ezek a szabályzatok a felhasználóktól az olyan könnyebben kitalálja jelszó.

Az Azure AD [dinamikus letiltott jelszavak](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) a szolgáltatás jelenlegi támadó viselkedése segítségével megakadályozhatja a felhasználókat, hogy könnyen lehet kitalálni beállítása. Ez a lehetőség mindig be van kapcsolva, amikor a felhasználók jönnek létre a felhőben, de most is elérhető hibrid cégekhez és vállalkozásokhoz azokat üzembe [jelszavas védelem az Azure AD a Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Az Azure AD jelszóvédelem megakadályozza a felhasználókat abban, hogy ezek a közös jelszavak kiválasztása, és a jelszavát adja meg, hogy egyéni kulcsszavakat tartalmazó blokkolására is kiterjeszthető. Például a felhasználók megakadályozása a vállalat terméknevekhez vagy egy helyi sport csapat tartalmazó jelszavak kiválasztása.

A Microsoft javasolja, hogy a következő alapján modern jelszóházirend bevezetése [NIST útmutatást](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Szükséges jelszavak legalább 8 karakterből kell állnia. Már nem szükségszerűen jobb, mivel azok kárt felhasználókat előre jelezhető jelszó, menthetik a jelszavakat a fájlokat, vagy írja le.
2. Tiltsa le a lejárati szabályok, amelyek hozzásegítheti a felhasználókat a könnyen kitalálható jelszavakat, mint **Summer2018!**
3. Tiltsa le a karakter-összeállítás követelményeknek, és megakadályozhatja a felhasználókat gyakran megtámadott jelszavak, kiválasztása, azok kárt a felhasználók kiválaszthatják a kiszámítható karakter helyettesítések hozzáadása a jelszavakat.

Használhat [PowerShell használatával megakadályozhatja, hogy a jelszavak lejárjanak](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) a felhasználók számára, ha közvetlenül az Azure ad-identitások hoz létre. Ezek a házirendek használatával [tartományi csoportházirend-beállítások] hibrid szervezeteknek meg kell valósítania (https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10) vagy [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Kiszivárgott hitelesítő adatok ellen, és adja hozzá a leállások ellen rugalmasságának biztosításával

Ha a szervezet hibrid identitáskezelési megoldás használ az átmenő hitelesítés vagy összevonás, majd engedélyezze Jelszókivonat-szinkronizálás a következő két okok miatt:

* A [kiszivárgott hitelesítő adatokkal rendelkező felhasználók](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) jelentés az Azure AD felügyeleti figyelmeztetést jelenít meg felhasználónevet és jelszót párok, amelyeket megnyílt a weben"sötét." Egy tetszik mennyiségi jelszavak újbóli használatának kiszivárgott adathalász, a kártevők és a jelszó ismételt később megsértő külső webhelyeken keresztül. A Microsoft megállapítja, hogy ezek közül számos kiszivárgott hitelesítő adatok, és megtudhatja, hogy ebben a jelentésben, ha azok megfelelnek hitelesítő adatok a szervezet –, de csak akkor, ha [Jelszókivonat-szinkronizálás engedélyezése](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* (Például a zsarolóprogram-támadásra utal) helyszíni kimaradás esetén is elérheti, váltson [felhőalapú hitelesítés használatával a Jelszókivonat-szinkronizálás](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Ez a biztonsági mentési hitelesítési módszer lehetővé teszi, hogy továbbra is elérhesse az Azure Active Directoryval, beleértve az Office 365-hitelesítéshez konfigurált alkalmazásokhoz. INFORMATIKAI részleg ebben az esetben nem kell, hogy személyes e-mail-fiók oszthat meg adatokat mindaddig, amíg a helyszíni újraindulása után.

Tudjon meg többet [Jelszókivonat-szinkronizálás](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) működik.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Az AD FS extranet az intelligens zárolás megvalósítása

Konfigurálhatja az alkalmazásokat közvetlenül az Azure AD-hitelesítést a szervezet számára előnyös [Azure ad-ben az intelligens zárolási](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Ha az AD FS a Windows Server 2012 R2 használata esetén az AD FS megvalósítására [extranetes fiókzárolási védelem](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Az AD FS a Windows Server 2016 használatakor megvalósítása [intelligens extranetes fiókzárolás](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Az AD FS intelligens extranetes fiókzárolási találgatásos tranzakciómentes támadások, melyik céloldali az AD FS megakadályozza, hogy a felhasználók az Active Directory kizárásuk.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Belsőleg biztonságos, könnyebben használható hitelesítő adatok kihasználása

Használatával [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), lecserélheti ezen jelszavak erős, kétfaktoros hitelesítés a számítógépek és mobileszközök felügyeletét. Ez a hitelesítési módszer új típusú a felhasználói hitelesítő adatok, amelyek biztonságosan eszközhöz kötődik, és a egy biometrikus adatokat vagy PIN-kódot használja.

## <a name="step-2---reduce-your-attack-surface"></a>2. lépés – a támadási felület csökkentése

Adja meg a jelszót biztonsági sérülés elterjedtségével, minimalizálja a támadási felületet a szervezetben, kritikus fontosságú. Távolítsa el a régebbi, kevésbé biztonságos protokollok, korlátozza az access belépési pontok, és ilyen erőforrásokhoz való rendszergazdai hozzáférés jelentősebb ellenőrzést csökkentheti a támadási felület.

### <a name="block-legacy-authentication"></a>Az örökölt hitelesítés letiltása

Saját örökölt módszerekkel fér hozzá vállalati adatokhoz, és az Azure AD-vel hitelesítéséhez alkalmazásokat egy másik veszélyt a szervezet számára. Örökölt hitelesítést használó alkalmazások példái olyan POP3, IMAP4 vagy SMTP-ügyfelek. Az örökölt hitelesítési alkalmazások hitelesítéshez a felhasználó nevében, és a speciális biztonsági értékelések ezzel megakadályozza, hogy az Azure AD. Alternatív, modern hitelesítést, csökkenti a biztonsági kockázatot jelent, mivel támogatja a többtényezős hitelesítés és feltételes hozzáférési. Azt javasoljuk, hogy a következő három műveletek:

1. Blokk [örökölt hitelesítés használatakor az AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. A telepítő [SharePoint Online és Exchange online-hoz a modern hitelesítést használó](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Használat [feltételes hozzáférési szabályzatok letiltása az örökölt hitelesítési](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Blokk érvénytelen hitelesítési belépési pontok

A feltételezése megsértésére védekezőknek használ, meg kell csökkentésében feltört felhasználói hitelesítő adatok esetén azokat. A környezetében lévő minden egyes alkalmazás fontolja meg az érvényes használati esetek: mely csoportokat, hálózatok, mely eszközök és az egyéb elemek jogosult –, majd a többi letiltása. A [Azure AD feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), szabályozhatja, hogyan jogosult felhasználók hozzáférésének a alkalmazások és erőforrások adott feltételek alapján határozza meg.

### <a name="block-end-user-consent"></a>A végfelhasználói jóváhagyási letiltása

Alapértelmezés szerint az Azure ad-ben minden felhasználók jogosultak biztosítanak az alkalmazások, az OAuth 2.0 és a Microsoft identity [hozzájárulási keretrendszer](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) fér hozzá vállalati adatokhoz való engedélyekkel. Közben jóváhagyják teszi lehetővé a felhasználók könnyedén beszerezni a hasznos olyan alkalmazások, amelyek integrálhatók a Microsoft 365 és az Azure, azt egy kockázati hozhat létre, ha nem használt és figyelünk. [Az összes jövőbeli felhasználói jóváhagyás műveletek letiltása](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access) segíthet csökkenteni a támadási és a kockázat csökkentése érdekében. Ha végfelhasználói jóváhagyási le van tiltva előző jóváhagyás biztosít továbbra is figyelembe veszi, de minden további jóváhagyása műveletet kell végrehajtani egy rendszergazda. Ez a funkció letiltása előtt javasoljuk, hogy győződjön meg arról, hogy felhasználók fog megtudhatja, hogyan rendszergazdai jóváhagyást kérnie az új alkalmazások; Ennek kell segítenek csökkenteni a felhasználó fennakadások nélkül használható, minimalizálása érdekében a támogatási kötet, és győződjön meg arról, hogy a felhasználók ne iratkozzon fel nem Azure AD hitelesítő adatait használó alkalmazások számára.

### <a name="implement-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management megvalósítása

"Sérülések feltételezésének" egy másik hatása szükség, azzal csökkenthető a kiemelt szerepkörök kapnak egy feltört fiókhoz. 

[Az Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) fiókjogosultságot segít kis méretben segítségével:

* Azonosítsa és felügyeleti szerepkörökhöz rendelt felhasználók kezelése.
* Ismerje meg, távolítsa el a használaton kívüli vagy túl sok jogosultsággal szerepköröket.
* Létrehozza a multi-factor authentication által védett szabályokat, győződjön meg arról, hogy a kiemelt szerepkörökhöz.
* Szabályokat, hogy a kiemelt szerepkörökhöz csak elég hosszú kapnak a feladatnak a rendszerjogosultságú létesíteni.

Engedélyezze az Azure AD PIM-ben, majd a hozzárendelt rendszergazdai szerepkörök, és távolítsa el a felesleges fiókot ezeket a szerepköröket a felhasználók megtekintéséhez. A fennmaradó kiemelt jogosultságú felhasználók át azokat állandó jogosulttá. Végül létrehozni a megfelelő házirendek, győződjön meg arról, amikor szükségük van ezen a kiemelt szerepkörökhöz való hozzáférést, azokat is biztonságosan ehhez, és a szükséges módosítani a vezérlő.

Telepítésének részeként a rendszerjogosultságú fiókot folyamat, kövesse a [ajánlott eljárás az, hogy hozzon létre legalább két vészhelyzeti fiókokat](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) , hogy az Azure AD hozzáférési rendelkezik, ha Ön zárolja önmagát.

## <a name="step-3---automate-threat-response"></a>3. lépés – reagáláshoz automatizálása

Az Azure Active Directory számos képesség, amely automatikusan a támadások, az észlelés és válasz a késés eltávolítása intercept rendelkezik. Csökkentheti a költségeket és a kockázatok, ha csökkenti az idő bűnözők beágyazása saját magukat a környezet használatával. Az alábbiakban a konkrét lépéseket is igénybe vehet.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Felhasználói kockázati biztonsági házirend használata az Azure AD Identity Protection bevezetése

Felhasználói kockázat azt jelzi, hogy a felhasználói identitás biztonsága sérült, és az annak a valószínűségét alapján a [felhasználói kockázati események](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) társított a felhasználó identitását. Felhasználói kockázati házirendet feltételes hozzáférési szabályzatot, amely kiértékeli a kockázati szintjét egy adott felhasználónak vagy csoportnak. Alapján alacsony, közepes, magas kockázati szintű, a szabályzat letiltja a hozzáférést, vagy a multi-factor authentication szolgáltatás használatával biztonságos jelszómódosítás megkövetelése konfigurálható. Microsoft azt javasolja, hogy a magas kockázatú felhasználók számára egy biztonságos jelszómódosítás megkövetelése.

![Kockázatosként megjelölt felhasználók](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Az Azure AD Identity Protection alkalmazzon bejelentkezési kockázati házirend

Bejelentkezési kockázati annak a valószínűségét, a fiók tulajdonosa nem próbál jelentkezzen be az identitást használja. A [bejelentkezési kockázati házirend](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) egy feltételes hozzáférési házirend, amely kiértékeli a kockázati szintjét egy adott felhasználó vagy csoport. Kockázati szint (magas és közepes vagy alacsony) alapján, a szabályzat letiltja a hozzáférést, vagy többtényezős hitelesítés konfigurálható. Ellenőrizze, hogy a többtényezős hitelesítés kényszerítése, közepes vagy újabb kockázati bejelentkezések.

![Bejelentkezés névtelen IP-címekről](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>4. lépés – a irányuljon

Naplózás és a biztonsággal kapcsolatos eseményeket és a kapcsolódó riasztások naplózása egy hatékony védelme stratégia alapvető összetevői. A biztonsági naplók és jelentések biztosítja a gyanús tevékenységek és a mintákat, amelyek a hálózat és a belső támadásokkal külső behatolás történt kísérlet, vagy sikeres lehet, hogy észlelni a Súgó elektronikus rekord. Naplózás segítségével figyelheti a felhasználói tevékenység, a dokumentum a jogszabályoknak való megfelelőség, hajtsa végre a nyomkereső elemzésekkel és több. Riasztások adja meg a biztonsági események értesítések.

### <a name="monitor-azure-ad"></a>A figyelő Azure ad-ben

A Microsoft Azure-szolgáltatásokat és funkciókat biztosít a vizsgálati és naplózási beállítások segítenek e hiányosságok a adatszivárgás megakadályozása érdekében a biztonsági házirendeket és mechanizmusokat megalkotni hiányosságok azonosítása és konfigurálható biztonsági. Használhat [Azure-naplózás és a naplózási](https://docs.microsoft.com/azure/security/azure-log-audit) és [naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Figyelheti a hibrid környezetben az Azure AD Connect Health

[Az Azure AD Connect Health AD FS monitoring](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) lehetséges problémákat és az AD FS-infrastruktúra elleni támadások látható-e nagyobb betekintést nyújt. Az Azure AD Connect Health riasztások részletei, a megoldási lépések, valamint hivatkozásokat biztosít a kapcsolódó dokumentációra; Használatelemzés vonatkozó kapcsolatos hitelesítési forgalom; több metrikát teljesítmény figyelése és jelentések.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Az Azure AD Identity Protection-események figyelése

[Az Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) egy értesítési, figyelési és jelentéskészítési eszköz, amellyel a szervezet identitásait érintő esetleges biztonsági rések észlelését. Azt észleli, kockázati eseményekről, például lehetetlen utazásokat, kiszivárgott hitelesítő adatok, és történő bejelentkezések fertőzött eszközök, névtelen IP-címeket a gyanús tevékenységeket, és az ismeretlen helyekkel társított IP-címeket. E-mail-címe kockázatos felhasználók és/vagy egy heti összefoglaló e-mail fogadása értesítési riasztások engedélyezése.

Az Azure AD Identity Protection a célszerű figyelemmel kísérni napi két fontos jelentéseket biztosítja:
1. Felhasználói bejelentkezési tevékenységek kell vizsgálni – a kockázatos bejelentkezési jelentések felületen, a jogos tulajdonosa a bejelentkezés nem végeztek.
2. Felhasználói fiókokat, előfordulhat, hogy sérült a biztonsága, mint a kiszivárgott hitelesítő adatok észlelt a kockázatos felhasználó jelentések felületen vagy a felhasználó bejelentkezett egy lehetetlen utazási események okozó különböző helyekről. 

![Kockázatosként megjelölt felhasználók](media/azure-ad/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Naplózási alkalmazások és a jóváhagyott engedélyek

Felhasználók is lehet címeket ágyaznak ellenőrizheti, hogy a feltört webhelyek vagy alkalmazások, amely hozzáfér a saját profil és a felhasználó adatok, például az e-mailjeikhez. Egy rosszindulatú aktor használhatja a postaláda tartalmak titkosításához, és kereslet-postaláda adatait elhárította a ransom kapott jóváhagyott engedélyeket. [A rendszergazdák ekkor a felülvizsgálandó és naplózási](https://blogs.technet.microsoft.com/office365security/defending-against-illicit-consent-grants/) a felhasználó által megadott engedélyekkel.

## <a name="step-5---enable-end-user-self-help"></a>5. lépés - a végfelhasználói önsegítő engedélyezése

Lehetőség szerint érdemes hatékonyságú biztonsági elosztása érdekében. A témakörgyűjtemény azonos hamarosan eléri a, hosszú távon határozza meg a biztonság alaprendszert így juthat fennakadások nélkül használható eltávolíthatja a szervezet úgy a felhasználók fennmaradó vigilant közben. 

### <a name="implement-self-service-password-reset"></a>Az önkiszolgáló jelszó-visszaállítás végrehajtása

Az Azure [önkiszolgáló jelszó-visszaállítási (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) egyszerű módot kínál a rendszergazdáknak, hogy a felhasználók visszaállítására vagy zárolásának feloldására jelszavuk és fiókjuk rendszergazda beavatkozása nélkül. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhozzáféréséről, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre. 

### <a name="implement-self-service-group-management"></a>Önkiszolgáló csoportkezelés megvalósítása

Az Azure AD lehetővé teszi a biztonsági és Office 365-csoportok használatával erőforrásokhoz való hozzáférés kezelésére. Ezek a csoportok csoporttulajdonosok helyett a rendszergazdák által kezelhető. Más néven [önkiszolgáló csoportkezelési](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), ez a funkció lehetővé teszi a csoporttulajdonosok számára a rendszer nem rendeli egy rendszergazdai szerepkör a csoportok létrehozásához és kezeléséhez a kérelmek kezeléséhez a rendszergazdák segítsége nélkül.

### <a name="implement-azure-ad-access-reviews"></a>Megvalósítása az Azure AD hozzáférési felülvizsgálatok

A [az Azure AD hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), akkor is kezelhetik a csoporttagságokat, hozzáférés a vállalati alkalmazásokhoz, és ellenőrizze, hogy egy biztonsági szabvány, amely a hozzáférést a felhasználóknak a megmaradjanak a kiemelt szerepkörök hozzárendeléseit kiterjesztett időszakok idő, amikor nincs szükségük van rá.

## <a name="summary"></a>Összegzés

Sok szempontot, biztonságos identitás-infrastruktúrát, de az 5. lépés – ellenőrzőlista segít gyorsan elvégezni egy biztonságosabb és biztonságos identitás-infrastruktúrát:

* Erősítse meg a hitelesítő adatait.
* A támadási felület csökkentése.
* Veszélyforrások elleni válasz automatizálása.
* Növelje a ismertségét, naplózás és figyelés.
* Lehetővé teszi a több kiszámítható és teljes körű végfelhasználói biztonsági önsegítő együtt.

Nagyra értékeljük, hogyan súlyosan Identitásbiztonság igénybe vehet, és Reméljük, ez a dokumentum egy hasznos ütemterv egy biztonságosabb rendszer kialakításához, a szervezet számára.

## <a name="next-steps"></a>További lépések
Ha tervezéséhez és üzembe helyezése a javaslatok segítségre van szüksége, tekintse meg a [Azure ad-ben a projekt központi telepítési csomagok](https://aka.ms/deploymentplans) segítséget.
