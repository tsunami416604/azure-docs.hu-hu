---
title: "A Microsoft Azure multi-factor Authentication felhasználói állapotok"
description: "További tudnivalók az Azure multi-factor Authentication felhasználói állapotok."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: ad8d531d633eb65fe90404fdab0499b8e5332db6
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Egy felhasználó vagy csoport kétlépéses ellenőrzés megkövetelése

A kétlépéses ellenőrzést igénylő két megközelítés valamelyikét hajthatja végre. Az első lehetőség, minden felhasználóhoz az Azure multi-factor Authentication (MFA) engedélyezéséhez. Amikor a felhasználók külön-külön vannak engedélyezve, kétlépéses ellenőrzés minden alkalommal, amikor bejelentkeznek az általuk (néhány kivétel, például amikor bejelentkeznek a megbízható IP-címekkel rendelkező, vagy ha a _megjegyzett eszközökön_ funkció be van kapcsolva). A második lehetőség egy feltételes hozzáférési szabályzatot, amely szükséges a kétlépéses ellenőrzést, bizonyos feltételek beállításához.

>[!TIP] 
>Válasszon egyet az alábbi módszerek szükséges a kétlépéses ellenőrzést, mindkettő nem. A feltételes hozzáférési házirendek engedélyezése a felhasználók a Azure multi-factor Authentication felülbírálja.

## <a name="which-option-is-right-for-you"></a>Melyik lehetőség az Ön számára legmegfelelőbb?

**Azure többtényezős hitelesítés engedélyezése felhasználói állapotok módosításával** a hagyományos megközelítés megkövetelő a kétlépéses ellenőrzést. Mindkét Azure MFA felhőben való és az Azure MFA kiszolgáló működik. Összes felhasználó számára engedélyezi a kétlépéses ellenőrzés végrehajtása, minden alkalommal, amikor bejelentkeznek az. A feltételes hozzáférési szabályzatok, amelyek hatással lehetnek, hogy a felhasználó engedélyezése egy felhasználó felülbírálja. 

**Azure multi-factor Authentication engedélyezése egy feltételes hozzáférési házirenddel** egy olyan rugalmasabb megközelítés megkövetelő a kétlépéses ellenőrzést. Ez csak akkor működik az Azure MFA felhőben való, azonban és _feltételes hozzáférés_ van egy [az Azure Active Directory szolgáltatás fizetős](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Csoportokat, valamint az egyes felhasználókra vonatkozó feltételes hozzáférési házirendeket is létrehozhat. Magas kockázatú csoportok adható meg több korlátozás mint alacsony kockázat csoportok, vagy a kétlépéses ellenőrzés csak magas kockázatú felhőalkalmazások szükséges, és kihagyja a alacsony kockázat néhányat a meglévők közül. 

Mindkét lehetőség jeleníti meg az Azure multi-factor Authentication regisztrálása az első bejelentkezéskor a követelmények bekapcsolása után. Mindkét lehetőség is dolgozhat a konfigurálható [Azure multi-factor Authentication beállításainak](multi-factor-authentication-whats-next.md).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Az Azure MFA engedélyezése a felhasználó állapotának módosítása

Azure multi-factor Authentication felhasználói fiókokat az alábbi három jól elkülöníthető állapottal rendelkeznek:

| status | Leírás | Érintett böngészőn kívüli alkalmazások | Érintett böngészőben megjelenő alkalmazásokba | Érintett modern hitelesítést |
|:---:|:---:|:---:|:--:|:--:|
| Letiltva |Az Azure MFA-ban nem regisztrált új felhasználó alapértelmezett állapota. |Nem |Nem |Nem |
| Engedélyezve |A felhasználó előfizetett a Azure MFA, de nincs regisztrálva. A következő bejelentkezéskor a regisztrációs kapnak. |Nem.  Ezek továbbra is működnek a regisztrációs folyamat befejezéséig. | Igen. A munkamenet lejárata után az Azure MFA-regisztráció szükség.| Igen. A hozzáférési jogkivonat lejárata után az Azure MFA-regisztráció szükség. |
| Kényszerítve |A felhasználó előfizetett, és a regisztrációs folyamat során az Azure MFA számára befejeződött. |Igen.  Az alkalmazásokhoz alkalmazásjelszókat. |Igen. Az Azure MFA szükség, a bejelentkezés. | Igen. Az Azure MFA szükség, a bejelentkezés. |

A felhasználói állapot tükrözi, hogy egy rendszergazda regisztrálta őket az Azure MFA, és hogy a regisztrációs folyamat befejeződött-e azokat.

Minden felhasználó indulnak *letiltott*. Ha regisztrál az Azure MFA a felhasználók, az állapot változik *engedélyezve*. Az engedélyezett felhasználók jelentkezzen be, és végezze el a regisztrációs folyamat során, az állapot változik *kényszerített*.  

### <a name="view-the-status-for-a-user"></a>A felhasználó állapotának megjelenítése

A lap, ahol megtekintheti és kezelheti a felhasználói állapotok eléréséhez használja az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Ugrás a **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**.
   ![Válassza ki a multi-factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. Megnyílik egy új lap, amely megjeleníti a felhasználói állapotok.
   ![a multi-factor authentication felhasználói állapota – képernyőkép](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>A felhasználó állapotának módosítása

1. Az előző lépések segítségével beolvasása az Azure multi-factor Authentication **felhasználók** lap.
2. Keresse meg azt a felhasználót az Azure MFA számára engedélyezni szeretné. Előfordulhat, hogy módosítani szeretné a nézetet a lap tetején. 
   ![Keresse meg azt a felhasználót – képernyőkép](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. A név melletti négyzetet.
4. A jobb oldalon a **Gyorsműveletek**, válassza a **engedélyezése** vagy **tiltsa le a**.
   ![Engedélyezi a kiválasztott felhasználó – képernyőkép](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >*Engedélyezett* felhasználók vannak automatikusan át lett váltva *kényszerített* amikor regisztrálják az Azure MFA számára. A felhasználói állapot módosítása nem manuálisan tegye *kényszerített*. 

5. Ellenőrizze a választást, a megjelenő előugró ablakban. 

Miután engedélyezte a felhasználókat, e-mailben értesítést küldhet nekik. Mondja el neki, hogy azok felkéri, hogy regisztrálja a következő bejelentkezéskor. Is ha a szervezet a böngészőn kívüli alkalmazásokat, amelyek nem támogatják a modern hitelesítést használ, szükségük alkalmazásjelszók létrehozásához. Mutató hivatkozást is használható a [Azure MFA végfelhasználói útmutató](./end-user/multi-factor-authentication-end-user.md) felhasználóinál a kezdéshez.

### <a name="use-powershell"></a>A PowerShell használata
A felhasználói állapot módosítása használatával [Azure AD PowerShell](/powershell/azure/overview), módosítsa `$st.State`. Három lehetséges állapota van:

* Engedélyezve
* Kényszerítve
* Letiltva  

Közvetlenül nem kerülnek a felhasználók a *kényszerített* állapotát. Ha így tesz, nem a webböngésző-alapú alkalmazások tovább működni, mert a felhasználó rendelkezik már meg az Azure MFA regisztrációs keresztül, és nem kapott egy [alkalmazásjelszót](multi-factor-authentication-whats-next.md#app-passwords). 

PowerShell használatával akkor jó választás, ha a felhasználók tömeges kell. Hozzon létre egy PowerShell-parancsfájlt, amely végighalad a felhasználók listában, és lehetővé teszik, hogy:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

A következő parancsfájl példája:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Feltételes hozzáférési házirenddel Azure többtényezős hitelesítés engedélyezése

_Feltételes hozzáférés_ egy fizetős szolgáltatás az Azure Active Directory, számos konfigurációs beállításokkal. Ezeket a lépéseket bízná egyik módja a szabályzat létrehozásához. További információkért olvassa el [feltételes hozzáférés az Azure Active Directoryban](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Ugrás a **Azure Active Directory** > **feltételes hozzáférés**.
3. Válassza ki **új házirend**.
4. A **hozzárendelések**, jelölje be **felhasználók és csoportok**. Használja a **Include** és **kizárása** lapra, adja meg, hogy mely felhasználók és csoportok kezeli-e a házirend.
5. A **hozzárendelések**, jelölje be **felhőalapú alkalmazásokba**. Foglaljon bele **összes felhőalapú alkalmazások**.
6. A **hozzáférés-szabályozási**, jelölje be **Grant**. Válasszon **többtényezős hitelesítést**.
7. Kapcsolja be **házirend engedélyezése** való **a**, majd válassza ki **mentése**.

A feltételes hozzáférési házirendben az egyéb beállítások segítségével meghatározhatja, hogy pontosan akkor, ha a kétlépéses ellenőrzés szükség. Például, hogy egy házirendet, például az egyik: alvállalkozói próbál meg elérni a beszerzési alkalmazást a nem megbízható hálózatokhoz, amelyek nincsenek tartományhoz csatlakoztatott eszközön, ha szükséges a kétlépéses ellenőrzést. 

## <a name="next-steps"></a>További lépések

- Tippek a [ajánlott eljárások a feltételes hozzáférés](../active-directory/active-directory-conditional-access-best-practices.md).

- Az Azure multi-factor Authentication beállításainak kezelése [a felhasználók és az eszközeik](multi-factor-authentication-manage-users-and-devices.md).
