---
title: Használja az Azure AD intelligens kizárás találgatásos támadások megelőzése
description: Az Azure Active Directory intelligens zárolás megvédi a szervezet a találgatásos támadások ismételt próbálkozással kitalálják jelszavát
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: d5beb5ce6e167cd100bec2ed54dc6ea0e78ba37b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036001"
---
# <a name="azure-active-directory-smart-lockout"></a>Az Azure Active Directory intelligens zárolás

Intelligens zárolás felhő eszközintelligencia használja ki a felhasználói jelszavak kitalálni vagy találgatásos módszerrel lehet beolvasni a próbált ezeken zárolására. Adott eszközintelligencia ismeri fel érvényes felhasználók érkező bejelentkezéseket, és eltérően azokat, a támadók, és más ismeretlen források kezeli őket. Intelligens zárolás is zárolására azzal kapcsolatban, a felhasználók továbbra is hozzáférhet a fiókjukat, és a hatékony során a támadók.

Intelligens zárolás mindig be van kapcsolva az összes Azure AD ügyfelek által biztosított biztonsági és a használhatóság jobb vegyesen alapértelmezett beállításokkal. Testreszabási intelligens zárolási beállítás értékekkel szervezetben, a felhasználók Azure AD alapszintű vagy magasabb licencek van szükség.

Intelligens zárolás integrálható hibrid telepítések, és a helyszíni Active Directory-fiókok védelme alatt álló zárolása a támadók a Jelszókivonat-szinkronizálás vagy átmenő hitelesítést használ. Intelligens fiókzárolási házirendek beállításával az Azure AD megfelelően támadások is kiszűri a helyszíni Active Directory elérése előtti.

Használata esetén [átmenő hitelesítés](../connect/active-directory-aadconnect-pass-through-authentication.md), és győződjön meg arról, hogy szüksége:

   * Az Azure AD bejelentkezési próbálkozásra van lehetőségük van **kevesebb** mint az Active Directory számítógépfiókok zárolási küszöbértéke. Állítsa be az értékeket, úgy, hogy az Active Directory számítógépfiókok zárolási küszöbértéke legalább két vagy három alkalommal hosszabb, mint az Azure AD bejelentkezési próbálkozásra van lehetőségük. 
   * Az Azure AD kizárás időtartama **másodpercben** van **hosszabb** , mint az Active Directory Fiókzárolás időtartama után **perc**.

> [!IMPORTANT]
> Jelenleg a rendszergazda nem fiókok zárolásának feloldása a felhasználók felhőalapú ha azok zárolva van az intelligens zárolás funkció. A rendszergazdának meg kell várnia a kizárás időtartama lejár.

## <a name="verify-on-premises-account-lockout-policy"></a>A helyszíni fiókzárolási házirend ellenőrzése

A helyszíni Active Directory fiókzárolási házirendet ellenőrzéséhez használja az alábbi utasításokat:

1. Nyissa meg a Csoportházirend kezelése eszközt.
2. Szerkessze a csoportházirendet, amelyet a szervezet fiókzárolási házirendet, például tartalmazza a **alapértelmezett tartományházirend**.
3. Keresse meg a **számítógép konfigurációja** > **házirendek** > **Windows-beállítások** > **biztonsági beállítások**   >  **Fiókházirendek** > **fiókzárolási házirend**.
4. Ellenőrizze a **fiókzárolás küszöbértékénél** és **fiókzárolás számlálójának visszaállítása után** értékeket.

![Módosítsa a helyszíni Active Directory fiókzárolási házirendet egy csoportházirend-objektum használatával](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Az Azure AD intelligens zárolás értékek kezelése

A szervezeti követelmények alapján, az intelligens zárolás értékeket kell szabható testre. Testreszabási intelligens zárolási beállítás értékekkel szervezetben, a felhasználók Azure AD alapszintű vagy magasabb licencek van szükség.

Ellenőrizze, vagy módosíthatja az intelligens zárolás értékeket a szervezet számára, tegye a következőket:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd kattintson a **Azure Active Directory**, majd **hitelesítési módszerek**.
1. Állítsa be a **bejelentkezési próbálkozásra van lehetőségük**alapján hány sikertelen bejelentkezések engedélyezettek a fiókhoz az első zárolása előtt. Az alapértelmezett érték 10.
1. Állítsa be a **kizárás időtartama másodpercben**, az egyes fiókzárolási hosszát másodpercben.

> [!NOTE]
> Ha az első bejelentkezés után a rendszer a kizárás is sikertelen, a fiók megakadályozza, hogy az újra. Ha egy fiók ismételten zárolja, a kizárás időtartama növeli.

![Szabja testre a Azure AD intelligens fiókzárolási házirendet az Azure-portálon](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>További lépések

[Megtudhatja, hogyan rossz jelszavak, a szervezet az Azure AD bA.](howto-password-ban-bad.md)

[Konfigurálja az önkiszolgáló jelszó-visszaállítást a felhasználók saját fiókokkal feloldásához.](quickstart-sspr.md)