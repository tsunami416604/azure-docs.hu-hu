---
title: Támadások megelőzése intelligens zárolással – Azure Active Directory
description: Az Azure Active Directory intelligens zárolása segít megvédeni a szervezetet a jelszavak at kitalálni próbáló találgatásos támadásokkal szemben
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61ae942ed189dc4245a9a0b282daf4cad5323536
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652584"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory intelligens zárolás

Az intelligens zárolás segít a rossz szereplők kizárásában, akik megpróbálják kitalálni a felhasználók jelszavait, vagy brute-force módszereket használnak a bejutáshoz. Felismeri az érvényes felhasználóktól érkező bejelentkezéseket, és eltérően kezeli őket, mint a támadók és más ismeretlen források. Az intelligens zárolás kizárja a támadókat, miközben lehetővé teszi a felhasználók számára, hogy továbbra is hozzáférjenek a fiókjaikhoz, és hatékonyak legyenek.

Alapértelmezés szerint az intelligens zárolás 10 sikertelen kísérlet után egy percig zárolja a fiók bejelentkezési kísérleteit. A fiók minden további sikertelen bejelentkezési kísérlet után ismét zárolódik, először egy percig, és a későbbi kísérletekben hosszabb ideig.

Az intelligens zárolás nyomon követi az utolsó három rossz jelszókimondottakat, hogy elkerülje a zárolási számláló növekményét ugyanahhoz a jelszóhoz. Ha valaki többször is beírja ugyanazt a hibás jelszót, ez a viselkedés nem okozza a fiók zárolását.

 > [!NOTE]
 > A kivonatnyomkövetési funkció nem érhető el az átmenő hitelesítést engedélyező ügyfelek számára, mivel a hitelesítés a helyszíni, nem a felhőben történik.

Az AD FS 2016 és az AF FS 2019 használatával végrehajtott összevont telepítések hasonló előnyöket tesznek lehetővé [az AD FS Extranet zárolás és az Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)használatával.

Az intelligens zárolás mindig be van kapcsolva az összes Azure AD-ügyfél számára ezekkel az alapértelmezett beállításokkal, amelyek a biztonság és a használhatóság megfelelő kombinációját kínálják. Az intelligens zárolási beállítások testreszabása a szervezetre jellemző értékekkel fizetős Azure AD-licenceket igényel a felhasználók számára.

Az intelligens zárolás használata nem garantálja, hogy egy valódi felhasználó soha nem lesz zárolva. Amikor az intelligens zárolás zárol egy felhasználói fiókot, mindent megteszünk annak érdekében, hogy ne zárjuk ki az eredeti felhasználót. A zárolási szolgáltatás megpróbálja biztosítani, hogy a hibás szereplők ne férhessenek hozzá egy valódi felhasználói fiókhoz.  

* Minden Egyes Azure Active Directory-adatközpont egymástól függetlenül követi a zárolást. A felhasználónak (threshold_limit * datacenter_count) meg kell kísérelje, ha a felhasználó minden adatközpontot megkörnyékez.
* A Smart Lockout ismerős helyet használ, és ismeretlen helyet használ a rossz szereplő és az eredeti felhasználó megkülönböztetésére. Az ismeretlen és ismerős helyeken külön zárolási számlálók lesznek.

Az intelligens zárolás integrálható a hibrid központi telepítésekkel, jelszókivonat-szinkronizálással vagy átmenő hitelesítéssel megvédheti a helyszíni Active Directory-fiókokat a támadók elől. Intelligens zárolási szabályzatok beállítása az Azure AD megfelelően, támadások kiszűrhető, mielőtt azok elérnék a helyszíni Active Directory.

Az [áteresdéses hitelesítés](../hybrid/how-to-connect-pta.md)használatakor meg kell győződnie arról, hogy:

* Az Azure AD zárolási **küszöbértéke kisebb,** mint az Active Directory-fiók zárolási küszöbértéke. Állítsa be az értékeket úgy, hogy az Active Directory-fiók zárolási küszöbértéklegalább két vagy háromszor hosszabb, mint az Azure AD zárolási küszöbértéket. 
* Az Azure AD zárolási időtartamát hosszabb ideig kell beállítani, mint az Active Directory alaphelyzetbe állítása fiók zárolási számláló időtartama után. Vegye figyelembe, hogy az Azure AD időtartama másodpercben van beállítva, míg az AD időtartama percekben van beállítva. 

Ha például azt szeretné, hogy az Azure AD-számláló magasabb legyen, mint az AD, akkor az Azure AD 120 másodperc (2 perc), míg a helyszíni AD van beállítva 1 perc (60 másodperc).

> [!IMPORTANT]
> Jelenleg a rendszergazda nem tudja feloldani a felhasználók felhőfiókjait, ha azintelligens zárolási funkció zárolta őket. A rendszergazdának meg kell várnia, amíg a zárolás időtartama lejár. A felhasználó azonban feloldhatja a zárolást az önkiszolgáló jelszó-visszaállítás (SSPR) használatával egy megbízható eszközről vagy helyről.

## <a name="verify-on-premises-account-lockout-policy"></a>Helyszíni fiókzárolási házirend ellenőrzése

Az alábbi utasításokat követve ellenőrizze az Active Directory-fiók zárolási házirendjeit:

1. Nyissa meg a Csoportházirend kezelése eszközt.
2. Szerkessze a szervezet fiókzárolási házirendjét tartalmazó csoportházirendet, például az **Alapértelmezett tartományházirendet.**
3. Tallózással keresse meg **a Számítógép konfigurációs** > **házirendjeit** > **A Windows beállításai** > **biztonsági beállítások** > **fiókházirendek** > **fiókzárolási házirendje között.**
4. Ellenőrizze a **fiókzárolási küszöbértéket,** és **állítsa alaphelyzetbe a fiókzárolási számlálót** az értékek után.

![Az Active Directory-fiók zárolási házirendjének módosítása](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Az Azure AD intelligens zárolási értékeinek kezelése

A szervezeti követelmények alapján előfordulhat, hogy testre kell szabni az intelligens zárolási értékeket. Az intelligens zárolási beállítások testreszabása a szervezetre jellemző értékekkel fizetős Azure AD-licenceket igényel a felhasználók számára.

A szervezet intelligens zárolási értékeinek ellenőrzéséhez vagy módosításához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg és válassza ki az *Azure Active Directoryt*. Válassza **a Biztonsági** > **hitelesítés módszerei jelszóvédelem** > **Password protection**lehetőséget.
1. Állítsa be a **zárolási küszöbértéket**annak alapján, hogy hány sikertelen bejelentkezés engedélyezett egy fiókban az első zárolás előtt. Az alapértelmezett érték 10.
1. Állítsa a **zárolás időtartamát másodpercben**az egyes zárolások hosszára másodpercben. Az alapértelmezett érték 60 másodperc (egy perc).

> [!NOTE]
> Ha a zárolás utáni első bejelentkezés is sikertelen, a fiók ismét zárolódik. Ha egy fiók ismételten zárolódik, a zárolás időtartama nő.

![Az Azure AD intelligens zárolási szabályzatának testreszabása az Azure Portalon](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Annak megállapítása, hogy működik-e az intelligens zárolási funkció

Az intelligens zárolási küszöbérték aktiválásakor a következő üzenet jelenik meg, amíg a fiók zárolva van:

**Fiókját ideiglenesen zároljuk a jogosulatlan használat megakadályozása érdekében. Próbálkozzon később, és ha továbbra is problémái vannak, forduljon a rendszergazdához.**

## <a name="next-steps"></a>További lépések

* [Ismerje meg, hogyan tilthatja ki a rossz jelszavakat a szervezetben az Azure AD használatával.](howto-password-ban-bad.md)
* [Konfigurálja az önkiszolgáló jelszó-visszaállítást, hogy a felhasználók feloldhassák saját fiókjuk zárolását.](quickstart-sspr.md)
