---
title: Megakadályozza a találgatásos támadások, az Azure AD intelligens zárolás használata
description: Az Azure Active Directory intelligens zárolás segít a szervezet védelme a találgatásos támadások ismételt próbálkozással kitalálják jelszavát
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.openlocfilehash: 101070dab80c69ea1d24dd11fe5e067947505d19
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55077964"
---
# <a name="azure-active-directory-smart-lockout"></a>Az Azure Active Directory intelligens zárolás

Intelligens zárolás segíti a kártékony elemek számára kitalálni a felhasználók jelszavát, vagy találgatásos módszerekkel úgy szerezheti be a kívánt zárolásának. Ez ismeri fel érvényes felhasználók származó bejelentkezéseket, és eltérően megjelennek a támadók, és az ismeretlen forrásokból való kezelése. Intelligens zárolás bejelentkezzenek a támadók, miközben a felhasználók továbbra is a fiókjaikat eléréséhez, és produktív látni.

Alapértelmezés szerint az intelligens zárolási zárolja a fiókot a bejelentkezési kísérletek 10 sikertelen bejelentkezési kísérletek után egy percig. A fiók zárolása minden ezt követő sikertelen bejelentkezési kísérlet az első és az ezt követő kísérletek hosszabb egy perc múlva ismét.

Intelligens zárolás nyomon követi az utolsó három rossz jelszavak kivonatait a fiókzárolási számláló reincrementing elkerülése érdekében. Ha valaki több alkalommal belép a rossz jelszót, ez a viselkedés nem okoz a fiók zárolása.

 > [!NOTE]
 > Nyomon követéséhez kivonat nem áll rendelkezésre az ügyfelek az átmenő hitelesítés engedélyezett, mint a hitelesítés történik, a felhőben nem helyszíni.

Intelligens zárolás mindig be van kapcsolva az Azure AD összes élvező ezeket az alapértelmezett beállításokat, amelyek a megfelelő biztonsági és a használhatóságot vegyesen kínálnak. Testre szabhatja az intelligens zárolás beállításokat a szervezet specifikus értékeket a felhasználók Azure AD alapszintű vagy magasabb szintű licenccel kell rendelkeznie.

Intelligens zárolás használata nem garantálja, hogy a valódi felhasználók rendszer soha nem zárolható lesz. Intelligens zárolás zárolja egy felhasználói fiókot, ha nem az eredeti felhasználó zárolhat legkedvezőbb megpróbáljuk. A zárolási szolgáltatás megkísérli győződjön meg arról, hogy kártékony elemek nem fér hozzá egy valódi felhasználói fiókot.  

* Mindegyik Azure Active Directory adatközpont nyomon követi a zárolási egymástól függetlenül. A felhasználó (threshold_limit * datacenter_count) kísérletek száma, ha a felhasználó eléri minden adatközpontban.
* Intelligens zárolás ismerős hely vs ismeretlen helyről kiderítsék és az eredeti felhasználó megkülönböztetéséhez használja. Ismeretlen és a jól ismert is külön Fiókzárolási számláló.

Intelligens zárolás integrálható legyen az a hibrid telepítések esetén a helyszíni Active Directory-fiókok védelméhez a támadók kizárásuk Jelszókivonat-szinkronizálás és átmenő hitelesítés használatával. Az intelligens zárolási házirendek beállításával az Azure ad-ben megfelelően támadások is kiszűrte a helyszíni Active Directory elérése előtti.

Használata esetén [átmenő hitelesítés](../hybrid/how-to-connect-pta.md), győződjön meg arról, hogy szüksége:

   * Az Azure ad-ben fiókzárolás küszöbértéke **kevesebb** , mint az Active Directory számítógépfiókok zárolási küszöbértéke. Állítsa be az értékét, úgy, hogy az Active Directory számítógépfiókok zárolási küszöbértéke hosszabb, mint az Azure ad-ben Fiókzárolás küszöbe legalább két-három alkalommal. 
   * Az Azure ad-ben a fiókzárolás időtartama **másodpercek alatt** van **hosszabb** , mint az Active Directory Fiókzárolás időtartama után **perc**.

> [!IMPORTANT]
> Jelenleg a rendszergazda nem fiókok zárolásának feloldása a felhasználók felhőbeli ha azok zárolva van az intelligens zárolás funkció. A rendszergazdának meg kell várnia a fiókzárolás időtartama lejár.

## <a name="verify-on-premises-account-lockout-policy"></a>A helyszíni fiókzárolási házirend ellenőrzése

A helyszíni Active Directory fiókzárolási házirend ellenőrzéséhez használja az alábbi utasításokat:

1. Nyissa meg a Csoportházirend kezelése eszközt.
2. Szerkessze a csoportházirenddel, amely magában foglalja a szervezet fiókzárolási házirend, például a **alapértelmezett tartományi házirend**.
3. Keresse meg a **számítógép konfigurációja** > **házirendek** > **Windows beállítások** > **biztonsági beállítások**   >  **Fiókházirend** > **fiókzárolási házirend**.
4. Ellenőrizze a **számítógépfiókok zárolási küszöbértéke** és **alaphelyzetbe állítása Fiókzárolási számláló nullázása** értékeket.

![Módosítsa a helyszíni Active Directory fiókzárolási házirendet egy csoportházirend-objektum használatával](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Intelligens zárolás értékeket az Azure AD kezelése

A szervezeti követelmények alapján, az intelligens zárolási értékeket szükség lehet szabható testre. Testre szabhatja az intelligens zárolás beállításokat a szervezet specifikus értékeket a felhasználók Azure AD alapszintű vagy magasabb szintű licenccel kell rendelkeznie.

Ellenőrizze, vagy módosítani az intelligens zárolás értékeket a szervezet számára, használja az alábbi lépéseket:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és kattintson a **Azure Active Directory**, majd **hitelesítési módszerek**.
1. Állítsa be a **Fiókzárolás küszöbe**alapján hány sikertelen bejelentkezések engedélyezett fiókonként az első zárolása előtt. Az alapértelmezett érték 10.
1. Állítsa be a **Fiókzárolás időtartama (másodpercben)**, az egyes fiókzárolási hosszát másodpercben. Az alapértelmezett érték 60 másodperc (egy percig).

> [!NOTE]
> Ha az első bejelentkezés után is sikertelen, a zárolás, a fiók bejelentkezzenek újra. Egy fiók ismételten zárolja, ha növeli a Zárolás időtartama.

![Szabja testre az Azure AD az intelligens zárolási házirendet az Azure Portalon](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smartlockout-feature-is-working-or-not"></a>Annak megállapítása, hogy a Smartlockout funkció működik-e, vagy nem

A smartlockout küszöbértéket akkor aktiválódik, amikor a következő üzenetet kap, amíg a fiók zárolva van:

**Fiókja jogosulatlan használatának megakadályozása érdekében ideiglenesen zárolva van. Próbálkozzon újra később, és ha továbbra sem tud bejelentkezni, forduljon a rendszergazdához.**


## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan lehet a szervezet Azure AD-vel rossz jelszavak letiltása.](howto-password-ban-bad.md)

[Konfigurálja az önkiszolgáló jelszó-visszaállítás felhasználók feloldhatják fiókjukat.](quickstart-sspr.md)
