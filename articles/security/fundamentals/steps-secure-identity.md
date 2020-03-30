---
title: Az Azure AD identitásinfrastruktúrájának védelme
titleSuffix: Azure Active Directory
description: Ez a dokumentum ismerteti azon fontos műveletek listáját, amelyeket a rendszergazdáknak végre kell hajtaniuk, hogy az Azure AD-képességek használatával biztonságban legyenek a szervezetük.
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: e0db8edfdfa380697a1d8d7e262a7a84da2fb7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565536"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Öt lépés az identitás-infrastruktúra biztosításához

Ha ezt a dokumentumot olvassa, tisztában van a biztonság jelentőségével. Valószínűleg már ön maga viseli a szervezet védelméért való felelősséget. Ha másokat is meg kell győznie a biztonság fontosságáról, küldje el őket a Microsoft security intelligence legfrissebb [jelentésének elolvasására.](https://go.microsoft.com/fwlink/p/?linkid=2073747)

Ez a dokumentum segít biztonságosabb testtartást az Azure Active Directory képességeinek használatával egy ötlépéses ellenőrzőlista segítségével beoltani a szervezet kibertámadások ellen.

Ez az ellenőrzőlista segítségével gyorsan üzembe helyezheti a szervezet védelmét szolgáló kritikus fontosságú műveleteket a következők magyarázatával:

* Erősítse meg a megbízólevelét.
* Csökkentse a támadási felületet.
* A fenyegetésre adott válasz automatizálása.
* Használja a felhőintelligenciát.
* Engedélyezze a végfelhasználói önkiszolgáló szolgáltatást.

Az ellenőrzőlista olvasása közben folyamatosan nyomon követheti, hogy mely funkciók és lépések teljesek.

> [!NOTE]
> A jelen dokumentumban szereplő javaslatok közül sok csak azokra az alkalmazásokra vonatkozik, amelyek az Azure Active Directory identitásszolgáltatóként való használatára vannak konfigurálva. Az alkalmazások egyszeri bejelentkezéshez való konfigurálása biztosítja a hitelesítő adatokkal kapcsolatos házirendek, a fenyegetésészlelés, a naplózás, a naplózás és az alkalmazásokhoz hozzáadott egyéb funkciók előnyeit. [Egyszeri bejelentkezés az Azure Active Directoryn keresztül](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md) az alapja, amelyen ezek a javaslatok alapulnak.

A jelen dokumentumban szereplő javaslatok összhangban vannak az [identitásbiztonságos pontszámmal,](../../active-directory/fundamentals/identity-secure-score.md)az Azure AD-bérlő identitásbiztonsági konfigurációjának automatikus értékelésével. A szervezetek az Azure AD-portálon található biztonságos identitáspontszám-lapon találhatnak réseket a jelenlegi biztonsági konfigurációjukban, így biztosíthatják, hogy a Microsoft nak a biztonsággal kapcsolatos aktuális [gyakorlati tanácsait kövessék.](identity-management-best-practices.md) A Biztonságos pontszám oldalon végrehajtott minden egyes ajánlás növeli a pontszámot, és lehetővé teszi a haladás nyomon követését, valamint segít összehasonlítani a megvalósítást más hasonló méretű szervezetekkel vagy az iparágával.

![Identitás biztonságos pontszáma](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Az itt leírt funkciók közül sok hoz létre Egy Azure AD Premium-előfizetést, míg néhány ingyenes. További információkért tekintse át [az Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/) és az Azure [AD Deployment ellenőrzőlistáját.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Mielőtt elkezdené: A kiemelt jogosultságú fiókok védelme az MFA-val

Az ellenőrzőlista megkezdése előtt győződjön meg arról, hogy nem kerül veszélybe, miközben ezt az ellenőrzőlistát olvassa. Először meg kell védenie a kiemelt jogosultságú fiókokat.

A kiemelt jogosultságú fiókok vezérlését kapó támadók hatalmas károkat okozhatnak, ezért először fontos a fiókok védelme. Engedélyezze és megkövetelje az [Azure többtényezős hitelesítést](../../active-directory/authentication/multi-factor-authentication.md) (MFA) a szervezet összes rendszergazdája számára az [Azure AD biztonsági alapértelmezéseinek](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) vagy [feltételes hozzáférésének használatával.](../../active-directory/conditional-access/plan-conditional-access.md) Ha még nem hajtotta végre MFA, csináld most! Ez olyan fontos.

Minden készen áll? Kezdjük az ellenőrzőlistával.

## <a name="step-1---strengthen-your-credentials"></a>1. lépés - Erősítse meg hitelesítő adatait

A legtöbb vállalati biztonsági incidens olyan fiókból származik, amelyet számos módszer, például jelszóspray, adatszivárgás vagy adathalászat feltör. Tudjon meg többet ezekről a támadásokról ebben a videóban (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Győződjön meg arról, hogy a szervezet erős hitelesítést használ

Tekintettel a kitalálható, adathalász, rosszindulatú programokkal való lopás vagy újrafelhasznált jelszavak gyakoriságára, rendkívül fontos, hogy a jelszót valamilyen erős hitelesítő adattal támogassuk – tudjon meg többet az [Azure többtényezős hitelesítéséről.](../../active-directory/authentication/multi-factor-authentication.md)

Az identitásbiztonság alapvető szintjének egyszerű engedélyezéséhez használhatja az egykattintásos engedélyezést az [Azure AD biztonsági alapértékeinek](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)használatával. A biztonsági alapértelmezések az Azure MFA-t kényszerítik ki a bérlő összes felhasználója számára, és letiltja az örökölt protokollok bejelentkezéseit bérlői szintű.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Kezdje el kitiltani a gyakran megtámadott jelszavakat, és kapcsolja ki a hagyományos összetettséget és a lejárati szabályokat.

Számos szervezet a hagyományos összetettséget (speciális karaktereket, számokat, nagybetűket és kisbetűs) és jelszólejárati szabályokat használ. [A Microsoft kutatásai](https://aka.ms/passwordguidance) azt mutatják, hogy ezek a házirendek miatt a felhasználók könnyebben kitalálható jelszavakat választanak.

Az Azure AD [dinamikus tiltott jelszó](../../active-directory/authentication/concept-password-ban-bad.md) szolgáltatás a jelenlegi támadó viselkedését használja, hogy megakadályozza a felhasználókat a könnyen kitalálható jelszavak beállításában. Ez a funkció mindig be van kapcsolva, amikor a felhasználók a felhőben jönnek létre, de mostantól a hibrid szervezetek számára is elérhető, amikor telepítik az [Azure AD jelszavas védelmét a Windows Server Active Directory hoz.](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) Az Azure AD jelszavas védelme megakadályozza, hogy a felhasználók ezeket a gyakori jelszavakat válasszák, és kiterjeszthető a megadott egyéni kulcsszavakat tartalmazó jelszavak letiltásához. Megakadályozhatja például, hogy a felhasználók a vállalat termékneveit vagy egy helyi sportcsapatot tartalmazó jelszavakat válasszanak.

A Microsoft a következő modern jelszóházirend elfogadását javasolja a [NIST útmutatása](https://pages.nist.gov/800-63-3/sp800-63b.html)alapján:

1. A szükséges jelszavak legalább 8 karakterből állnak. A hosszabb idő nem feltétlenül jobb, mivel a felhasználók kiszámítható jelszavakat választanak, fájlokba mentik a jelszavakat, vagy leírják őket.
2. Tiltsa le a lejárati szabályokat, amelyek arra késztetik a felhasználókat, hogy könnyen kitalálhatják az olyan jelszavakat, mint a **Spring2019!**
3. Tiltsa le a karakterkompozíciós követelményeket, és akadályozza meg, hogy a felhasználók gyakran támadott jelszavakat válasszanak, mivel ezek hatására a felhasználók kiszámítható karakterhelyettesítéseket választanak a jelszavakban.

A PowerShell használatával [megakadályozhatja, hogy a jelszavak lejárjanak](../../active-directory/authentication/concept-sspr-policy.md) a felhasználók számára, ha közvetlenül hoz létre identitásokat az Azure AD-ben. A hibrid szervezeteknek ezeket a házirendeket [tartományi csoportházirend-beállításokkal](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) vagy a Windows PowerShell használatával kell [megvalósítaniuk.](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy)

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Védje meg a kiszivárgott hitelesítő adatokat, és adjon rugalmasságot a kimaradások ellen

Ha a szervezet egy hibrid identitáskezelési megoldást használ átadó hitelesítéssel vagy összevonással, akkor a jelszókivonat-szinkronizálást a következő két okból kell engedélyeznie:

* A [felhasználók a kiszivárgott hitelesítő adatok](../../active-directory/reports-monitoring/concept-risk-events.md) jelentés az Azure AD-kezelés figyelmezteti Önt a felhasználónév és jelszó párok, amelyek a "sötét web" elérhetővé váltak. Hihetetlen mennyiségű jelszó szivárog ki az adathalászat, a rosszindulatú programok és a jelszó újrafelhasználása révén a később feltört harmadik fél webhelyein. A Microsoft megkeresi a kiszivárgott hitelesítő adatok nagy részét, és ebben a jelentésben megmondja, hogy egyeznek-e a szervezet hitelesítő adataival – de csak akkor, ha [engedélyezi a jelszókivonat-szinkronizálást](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)!
* Helyszíni kimaradás esetén (például zsarolóprogramok elleni támadás esetén) átválthat a [felhőalapú hitelesítéshasználatára a jelszókivonat-szinkronizálás használatával.](choose-ad-authn.md) Ez a biztonsági mentési hitelesítési módszer lehetővé teszi az Azure Active Directoryval való hitelesítésre konfigurált alkalmazások, köztük az Office 365 további elérését. Ebben az esetben az informatikai személyzetnek nem kell személyes e-mail fiókokhoz folyamodnia az adatok megosztásához, amíg a helyszíni kimaradás meg nem oldódik.

További információ a [jelszókivonat-szinkronizálás](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) működéséről.

> [!NOTE]
> Ha engedélyezi a jelszókivonat-szinkronizálást, és az Azure AD tartományi szolgáltatásokat használja, a Kerberos (AES 256) kivonatai és adott esetben ntlm (RC4, só nélkül) kivonatai is titkosítva lesznek, és szinkronizálódnak az Azure AD-vel.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>AD FS extranetes intelligens zárolás megvalósítása

Szervezetek, amelyek konfigurálják az alkalmazásokat, hogy közvetlenül az Azure AD-n való hitelesítésre részesüljenek, az [Azure AD intelligens zárolásának](../../active-directory/authentication/concept-sspr-howitworks.md)előnyeit élvezik. Ha Az AD FS rendszert Windows Server 2012R2 rendszerben használja, valósítsa meg az AD FS [extranet zároláselleni védelmét.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) Ha Az AD FS-t Windows Server 2016 rendszeren használja, valósítsa meg az [extranetes intelligens zárolást.](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016) Az AD FS Smart Extranet zárolása védelmet nyújt az AD FS-t célzó találgatásos támadások ellen, miközben megakadályozza, hogy a felhasználók kilegyennek zárva az Active Directoryból.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Használja ki a gyújtószikramentes, könnyebben használható hitelesítő adatokelőnyeit

A [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)használatával a jelszavakat erős kétfaktoros hitelesítéssel helyettesítheti pc-ken és mobileszközökön. Ez a hitelesítés egy új típusú felhasználói hitelesítő adatból áll, amely biztonságosan kapcsolódik egy eszközhöz, és biometrikus vagy PIN-kódot használ.

## <a name="step-2---reduce-your-attack-surface"></a>2. lépés - Csökkentse a támadási felületet

Tekintettel a jelszófeltörés elterjedtségére, a szervezet támadási felületének minimalizálása kritikus fontosságú. A régebbi, kevésbé biztonságos protokollok használatának megszüntetése, a belépési pontok korlátozása és az erőforrásokhoz való rendszergazdai hozzáférés jelentősebb szabályozása segíthet csökkenteni a támadási felületet.

### <a name="block-legacy-authentication"></a>Régi hitelesítési folyamat letiltása

A saját örökölt módszereiket használó alkalmazások az Azure AD-vel való hitelesítéshez és a vállalati adatok eléréséhez további kockázatot jelentenek a szervezetek számára. Az örökölt hitelesítést használó alkalmazások közé tartoznak például a POP3, az IMAP4 vagy az SMTP-ügyfelek. A régi hitelesítési alkalmazások hitelesítik magukat a felhasználó nevében, és megakadályozzák, hogy az Azure AD speciális biztonsági értékeléseket végez. Az alternatív, modern hitelesítés csökkenti a biztonsági kockázatot, mivel támogatja a többtényezős hitelesítést és a feltételes hozzáférést. A következő három műveletet javasoljuk:

1. Az [AD FS használata esetén blokkolja az örökölt hitelesítést.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)
2. A [SharePoint Online és az Exchange Online](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md)beállítása a modern hitelesítés használatához.
3. Ha rendelkezik Azure AD Premium szolgáltatással, [feltételes hozzáférési szabályzatokkal](../../active-directory/conditional-access/overview.md) blokkolja az örökölt hitelesítést, egyébként használja az [Azure AD biztonsági alapértelmezett beállításait.](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)

### <a name="block-invalid-authentication-entry-points"></a>Érvénytelen hitelesítési belépési pontok blokkolása

A assume megsértése mentalitás használatával csökkentenie kell a sérült felhasználói hitelesítő adatok hatását, amikor azok megtörténnek. A környezetében lévő minden egyes alkalmazás esetében vegye figyelembe az érvényes használati eseteket: mely csoportok, mely hálózatok, mely eszközök és egyéb elemek engedélyezettek – majd blokkolja a többit. Az [Azure AD feltételes hozzáféréssel](../../active-directory/conditional-access/overview.md)szabályozhatja, hogy a jogosult felhasználók a megadott feltételek alapján hogyan férnek hozzá az alkalmazásaikhoz és az erőforrásaikhoz.

### <a name="restrict-user-consent-operations"></a>Felhasználói hozzájárulási műveletek korlátozása

Fontos, hogy megismerjék a különböző [Azure AD-alkalmazások jóváhagyási tapasztalatait,](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)az [engedélyek és a hozzájárulás típusait,](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)valamint a szervezet biztonsági helyzetére gyakorolt hatásukat. Alapértelmezés szerint az Azure AD minden felhasználója olyan alkalmazásokat adhat, amelyek a Microsoft identitásplatformot használják a szervezet adatainak eléréséhez. Bár lehetővé teszi a felhasználók beleegyezését önmagukban lehetővé teszi, hogy a felhasználók könnyen beszerezni hasznos alkalmazások, amelyek integrálhatók a Microsoft 365, az Azure és más szolgáltatások, ez kockázatot jelenthet, ha nem használják, és gondosan figyelt.

A Microsoft azt [javasolja, hogy tiltsa le a jövőbeli felhasználói hozzájárulási műveleteket](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application) a felület csökkentése és a kockázat csökkentése érdekében. Ha a végfelhasználói hozzájárulás le van tiltva, a korábbi hozzájárulási támogatás továbbra is tiszteletben marad, de minden jövőbeli hozzájárulási műveletet egy rendszergazdának kell végrehajtania. A rendszergazdai jóváhagyást a felhasználók egy integrált [rendszergazdai hozzájárulási kérelem munkafolyamaton](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) vagy a saját támogatási folyamatokon keresztül kérhetik. A végfelhasználói hozzájárulás letiltása előtt [recommendations](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests) a javaslataink segítségével tervezze meg ezt a változást a szervezetben. Az on-k nál, amelyekhez minden felhasználónak hozzá szeretne férni, fontolja meg a [beleegyezés megadását az összes felhasználó nevében,](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)ügyelve arra, hogy azok a felhasználók, akik még nem járultak hozzá egyénileg, hozzáférhetnek az alkalmazáshoz. Ha nem szeretné, hogy ezek az alkalmazások minden esetben minden felhasználó számára elérhetők legyenek, használja [az alkalmazás-hozzárendelést](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) és [a feltételes hozzáférést](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) az alkalmazásokhoz való felhasználói hozzáférés korlátozásához.

Győződjön meg arról, hogy a felhasználók rendszergazdai jóváhagyást kérhetnek az új alkalmazásokhoz a felhasználói súrlódás csökkentése, a támogatási mennyiség minimalizálása és annak megakadályozása érdekében, hogy a felhasználók nem Azure-beli AD-hitelesítő adatokkal regisztráljanak az alkalmazásokra. A hozzájárulási műveletek szabályozása után a rendszergazdáknak rendszeresen naplózniuk kell az alkalmazást és a beleegyezési engedélyeket.


### <a name="implement-azure-ad-privileged-identity-management"></a>Valósítsa meg az Azure AD kiemelt identitáskezelés

A "assume breach" egy másik hatása annak szükségessége, hogy minimálisra csökkentse annak valószínűségét, hogy egy feltört fiók emelt szintű szerepkörrel működhet.

[Az Azure AD kiemelt identitáskezelés (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) segít minimalizálni a fiók jogosultságait azáltal, hogy segít Önnek:

* A felügyeleti szerepkörökhöz rendelt felhasználók azonosítása és kezelése.
* Ismerje meg a nem használt vagy túlzott jogosultsági szerepköröket, amelyeket el kell távolítania.
* Hozzon létre szabályokat, amelyek biztosítják, hogy a kiemelt szerepköröket többtényezős hitelesítés védi.
* Hozzon létre szabályokat, hogy a kiemelt szerepkörök csak a kiemelt feladat végrehajtásához elegendő ideig legyenek megadva.

Engedélyezze az Azure AD PIM-et, majd tekintse meg a felügyeleti szerepkörökhöz rendelt felhasználókat, és távolítsa el a szükségtelen fiókokat ezekben a szerepkörökben. A fennmaradó kiemelt felhasználók számára helyezze át őket állandóról jogosultra. Végül hozzon létre megfelelő házirendeket annak biztosítására, hogy amikor hozzá kell férniük ezekhez a kiemelt szerepkörökhöz, ezt biztonságosan megtehetik, a szükséges módosításvezérléssel.

A kiemelt jogosultságú fiókfolyamat üzembe helyezése, kövesse az [ajánlott eljárást, hogy hozzon létre legalább két vészhelyzeti fiókok](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) győződjön meg arról, hogy továbbra is hozzáférhet az Azure AD, ha zárolja magát.

## <a name="step-3---automate-threat-response"></a>3. lépés - A fenyegetésekre adott válaszok automatizálása

Az Azure Active Directory számos olyan képességgel rendelkezik, amelyek automatikusan elfogják a támadásokat, hogy eltávolítsák az észlelés és a válasz közötti késést. Csökkentheti a költségeket és a kockázatokat, ha csökkenti azt az időt, amelyet a bűnözők arra használnak, hogy beágyazzják magukat a környezetébe. Itt vannak a konkrét lépéseket, amit tehetünk.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Felhasználói kockázat biztonsági házirendjének megvalósítása az Azure AD Identity Protection használatával

A felhasználói kockázat azt jelzi, hogy a felhasználó identitása sérült,és a felhasználó identitásával társított [felhasználói kockázatészlelések](../../active-directory/identity-protection/overview.md) alapján kerül kiszámításra. A felhasználói kockázati házirend egy feltételes hozzáférési szabályzat, amely kiértékeli a kockázati szintet egy adott felhasználó vagy csoport. Alacsony, közepes, magas kockázati szintű, a házirend beállítható a hozzáférés letiltására, vagy szükség van egy biztonságos jelszómódosítás többtényezős hitelesítéssel. A Microsoft javaslata az, hogy a magas kockázatú felhasználók számára biztonságos jelszómódosítást követeljenek meg.

![Kockázatosként megjelölt felhasználók](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Bejelentkezési kockázati szabályzat megvalósítása az Azure AD Identity Protection használatával

A bejelentkezési kockázat annak a valószínűsége, hogy a fiók tulajdonosátnem más próbál bejelentkezni az identitás használatával. A [bejelentkezési kockázati házirend](../../active-directory/identity-protection/overview.md) egy feltételes hozzáférési szabályzat, amely kiértékeli a kockázati szintet egy adott felhasználó vagy csoport. A kockázati szint (magas/közepes/alacsony) alapján a házirend beállítható a hozzáférés blokkolására vagy a többtényezős hitelesítés kényszerítésére. Győződjön meg arról, hogy kényszeríti a többtényezős hitelesítést közepes vagy a fenti kockázati bejelentkezések.

![Bejelentkezés névtelen IP-kből](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>4. lépés - Felhőalapú intelligencia kihasználása

A biztonsággal kapcsolatos események és a kapcsolódó riasztások naplózása és naplózása a hatékony védelmi stratégia alapvető elemei. A biztonsági naplók és jelentések elektronikus nyilvántartást nyújtanak a gyanús tevékenységekről, és segítenek észlelni azokat a mintákat, amelyek a hálózat külső behatolására vagy sikeres behatolására utalhatnak, valamint a belső támadásokat. A naplózás segítségével figyelheti a felhasználói tevékenységet, dokumentálhatja a jogszabályok nak való megfelelést, törvényszéki elemzést és egyebeket. A riasztások biztonsági eseményekről adnak értesítést.

### <a name="monitor-azure-ad"></a>Az Azure AD figyelése

A Microsoft Azure-szolgáltatások és -szolgáltatások konfigurálható biztonsági naplózási és naplózási lehetőségeket biztosítanak a biztonsági házirendek és -mechanizmusok hiányosságainak azonosításához, valamint a hiányosságok megszüntetéséhez a szabálysértések megelőzése érdekében. Használhatja [az Azure naplózása és naplózása](log-audit.md) és naplózása és [naplózási tevékenység jelentések az Azure Active Directory portálon.](../../active-directory/reports-monitoring/concept-audit-logs.md)

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Az Azure AD Connect állapotának figyelése hibrid környezetekben

[Az AD FS figyelése az Azure AD Connect Health szolgáltatással](../../active-directory/hybrid/how-to-connect-health-adfs.md) nagyobb betekintést nyújt az AD FS-infrastruktúrával kapcsolatos lehetséges problémákba és láthatóságba. Az Azure AD Connect Health riasztásokat küld a részletekkel, a megoldási lépésekkel és a kapcsolódó dokumentációra mutató hivatkozásokkal; a hitelesítési forgalomhoz kapcsolódó számos mutató használati elemzése; teljesítmény-ellenőrzés és a jelentések.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Az Azure AD-identitásvédelem eseményeinek figyelése

[Az Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) egy értesítési, figyelési és jelentéskészítési eszköz, amellyel észlelheti a szervezet identitásait érintő potenciális biztonsági réseket. Észleli a kockázatészlelések, például a kiszivárgott hitelesítő adatok, lehetetlen utazás és bejelentkezések a fertőzött eszközök, névtelen IP-címek, IP-címek társított gyanús tevékenység, és ismeretlen helyeken. Engedélyezze az értesítési riasztásokat, hogy e-maileket kapjon a veszélyeztetett felhasználóktól és/vagy heti kivonatoló e-maileket.

Az Azure AD Identity Protection két fontos jelentést biztosít, amelyeket naponta figyelnie kell:
1. Kockázatos bejelentkezési jelentések felszínre felhasználói bejelentkezési tevékenységeket kell vizsgálnia, a jogos tulajdonos nem hajtotta végre a bejelentkezést.
2. A kockázatos felhasználói jelentések olyan felhasználói fiókokat hoznak felszínre, amelyek feltörték őket, például az észlelt kiszivárgott hitelesítő adatokat, vagy a különböző helyekről bejelentkezett felhasználót, ami egy lehetetlen utazási eseményt okoz.

![Kockázatosként megjelölt felhasználók](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Alkalmazások és beleegyezési engedélyek naplózása

A felhasználókat rálehet venni, hogy egy feltört webhelyre vagy olyan alkalmazásokra navigáljanak, amelyek hozzáférhetnek a profiladataikhoz és a felhasználói adataikhoz, például az e-mailjükhöz. A rosszindulatú aktor a kapott engedélyeket a postaláda tartalmának titkosítására használhatja, és váltságdíjat követelhet a postaláda adatainak visszaszerzéséért. [A rendszergazdáknak át kell tekinteniük és auditálniuk kell](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) a felhasználók által megadott engedélyeket, vagy le kell tiltaniuk a felhasználók alapértelmezett beleegyezési képességét.

A felhasználók által adott engedélyek naplózása mellett [kockázatos vagy nem kívánt OAuth-alkalmazásokat](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth) is megkereshet prémium szintű környezetekben.

## <a name="step-5---enable-end-user-self-service"></a>5. lépés - Végfelhasználói önkiszolgáló szolgáltatás engedélyezése

Amennyire csak lehetséges, érdemes egyensúlyt teremteni a biztonság és a termelékenység között. Ugyanebben az irányban közeledik az utazás a gondolkodásmódot, hogy te egy alapot a biztonság hosszú távon, akkor távolítsa el a súrlódást a szervezet felhatalmazza a felhasználók, miközben éber.

### <a name="implement-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás megvalósítása

Az Azure AD [önkiszolgáló jelszó-visszaállítási (SSPR)](../../active-directory/authentication/quickstart-sspr.md) egyszerű eszközt kínál a rendszergazdák számára, hogy a felhasználók alaphelyzetbe állíthassák vagy feloldják jelszavukat vagy fiókjaikat ügyfélszolgálat ivagy rendszergazdai beavatkozás nélkül. A rendszer részletes jelentéseket tartalmaz, amelyek nyomon követik, hogy a felhasználók mikor állították alaphelyzetbe a jelszavukat, valamint értesítéseket, amelyek figyelmeztetik önt a visszaélésre vagy a visszaélésre.

### <a name="implement-self-service-group-and-application-access"></a>Önkiszolgáló csoport- és alkalmazáshozzáférés megvalósítása

Az Azure AD lehetővé teszi, hogy a nem rendszergazdák az erőforrásokhoz való hozzáférés kezelését, a biztonsági csoportok, az Office 365 csoportok, alkalmazásszerepkörök és a hozzáférési csomag katalógusok használatával.  [Az önkiszolgáló csoportkezelés](../../active-directory/users-groups-roles/groups-self-service-management.md) lehetővé teszi a csoporttulajdonosok számára, hogy felügyeleti szerepkör nélkül kezeljék saját csoportjukat. A felhasználók anélkül is hozhatnak létre és kezelhetnek Office 365-csoportokat, hogy a rendszergazdákra támaszkodnának a kéréseik kezeléséhez, és a fel nem használt csoportok automatikusan lejárnak.  [Az Azure AD jogosultságkezelés](../../active-directory/governance/entitlement-management-overview.md) továbbá lehetővé teszi a delegálást és a láthatóságot, átfogó hozzáférés-kérelem-munkafolyamatokkal és automatikus lejárati idővel.  Delegálhatja a nem rendszergazdáknak a saját csoportjaikhoz, teamsekhez, alkalmazásokhoz és SharePoint Online-webhelyekhez való saját hozzáférési csomagjainak konfigurálását, egyéni házirendekkel arra vonatkozóan, hogy kinek kell jóváhagynia a hozzáférést, beleértve az alkalmazottak konfigurálását is. a vezetők és az üzleti partnerek szponzorai.

### <a name="implement-azure-ad-access-reviews"></a>Valósítsa meg az Azure AD-hozzáférés-felülvizsgálatokat

Az [Azure AD-hozzáférési felülvizsgálatok](../../active-directory/governance/access-reviews-overview.md)segítségével kezelheti a hozzáférési csomag- és csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a kiemelt szerepkör-hozzárendeléseket, hogy biztosítsa a biztonsági szabvány fenntartását.  A felhasználók, az erőforrás-tulajdonosok és más véleményezők rendszeres felügyelete biztosítja, hogy a felhasználók hosszabb ideig ne őrizzék meg a hozzáférést, amikor már nincs rá szükségük.

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúrának számos szempontja van, de ez az ötlépéses ellenőrzőlista segít a biztonságosabb és biztonságos identitás-infrastruktúrágyors megvalósításában:

* Erősítse meg a megbízólevelét.
* Csökkentse a támadási felületet.
* A fenyegetésre adott válasz automatizálása.
* Használja a felhőintelligenciát.
* Önsegítő vel kiszámíthatóbb és teljesebb végfelhasználói biztonságot biztosít.

Nagyra értékeljük, hogy milyen komolyan veszi identity security, és reméljük, hogy ez a dokumentum hasznos ütemtervet egy biztonságosabb testtartás a szervezet számára.

## <a name="next-steps"></a>További lépések

Ha segítségre van szüksége a javaslatok megtervezéséhez és üzembe helyezéséhez, kérjen segítséget az [Azure AD-projekt telepítési terveiben.](https://aka.ms/deploymentplans)

Ha biztos benne, hogy ezek a lépések befejeződtek, használja a Microsoft [Identity Secure Score pontszámát,](../../active-directory/fundamentals/identity-secure-score.md)amely naprakészen tartja a [legújabb ajánlott eljárásokat](identity-management-best-practices.md) és biztonsági fenyegetéseket.
