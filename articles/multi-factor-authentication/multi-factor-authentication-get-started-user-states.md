---
title: "A Microsoft Azure multi-factor Authentication felhasználói állapotok"
description: "További tudnivalók az Azure MFA felhasználói állapotok."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
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
ms.openlocfilehash: c10374adf9b1438e087aa5fdba670f6d0234ee6f
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Egy felhasználó vagy csoport kétlépéses ellenőrzés megkövetelése

Kétféleképpen megkövetelő a kétlépéses ellenőrzést. Az első lehetőség ahhoz, hogy egyes felhasználók Azure multi-factor Authentication (MFA). Amikor a felhasználók külön-külön vannak engedélyezve, ezek mindig elvégzik a kétlépéses ellenőrzést (néhány kivétellel, például amikor bejelentkeznek megbízható IP-címekről, vagy ha a korábban megjegyzett eszközökön funkció be van kapcsolva). A második lehetőség egy feltételes hozzáférési szabályzatot, amely szükséges a kétlépéses ellenőrzést, bizonyos feltételek beállításához.

>[!TIP] 
>Válasszon egyet az alábbi módszerek szükséges a kétlépéses ellenőrzést, mindkettő nem. A felhasználó engedélyezése az Azure MFA felülbírálja a feltételes hozzáférési házirendekben.

## <a name="which-option-is-right-for-you"></a>Melyik lehetőség az Ön számára legmegfelelőbb

**Azure többtényezős hitelesítés engedélyezése felhasználói állapotok módosításával** a hagyományos megközelítés megkövetelő a kétlépéses ellenőrzést. Mindkét Azure MFA felhőben való és az Azure MFA kiszolgáló működik. Összes olyan felhasználót, engedélyeznie kell ugyanazt a felhasználói élményt, amely minden alkalommal, amikor bejelentkeznek kétlépéses ellenőrzés végrehajtásához. A feltételes hozzáférési szabályzatok, amelyek befolyásolhatják, hogy a felhasználó engedélyezése egy felhasználó felülbírálja. 

**Azure MFA engedélyezésével a feltételes hozzáférési házirenddel** egy olyan rugalmasabb megközelítés megkövetelő a kétlépéses ellenőrzést. Ez csak működnek az Azure MFA felhőben való, azonban, és a feltételes hozzáférés egy [az Azure Active Directory szolgáltatás fizetős](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Csoportokat, valamint az egyes felhasználókra vonatkozó feltételes hozzáférési házirendeket is létrehozhat. Magas kockázatú csoportok adható meg több korlátozás mint alacsony kockázat csoportok, vagy a kétlépéses ellenőrzés csak magas kockázatú felhőalkalmazások szükséges, és kihagyja a alacsony kockázat néhányat a meglévők közül. 

Mindkét lehetőség jeleníti meg az Azure multi-factor Authentication regisztrálása az első bejelentkezéskor a követelményeinek bekapcsolása után. Mindkét lehetőség is dolgozhat a konfigurálható [Azure multi-factor Authentication beállításait](multi-factor-authentication-whats-next.md)

## <a name="enable-azure-mfa-by-changing-user-status"></a>Az Azure MFA engedélyezése a felhasználó állapotának módosítása

Azure multi-factor Authentication felhasználói fiókokat az alábbi három jól elkülöníthető állapottal rendelkeznek:

| status | Leírás | Érintett böngészőn kívüli alkalmazások | Érintett böngészőben megjelenő alkalmazásokba | Érintett modern hitelesítéssel |
|:---:|:---:|:---:|:--:|:--:|
| Letiltva |Új felhasználó alapértelmezett állapota nincs regisztrálva az Azure multi-factor Authentication (MFA). |Nem |Nem |Nem |
| Engedélyezve |A felhasználó előfizetett a Azure MFA, de nincs regisztrálva. Regisztrálja a következő bejelentkezéskor kéri. |Nem.  Ezek továbbra is működnek a regisztrációs folyamat befejezéséig. | Igen. Ha a munkamenet lejár, MFA regisztrációs lesz szükség.| Igen. Ha a hozzáférési jogkivonat lejár, MFA-regisztrációs lesz szükség. |
| Kényszerítve |A felhasználó előfizetett, és a regisztrációs folyamat során az Azure MFA számára befejeződött. |Igen.  Az alkalmazásokhoz alkalmazásjelszókat. |Igen. Multi-factor Authentication a bejelentkezési azonosító szükséges. | Igen. Multi-factor Authentication a bejelentkezési azonosító szükséges. |

A felhasználói állapot tükrözi, hogy egy rendszergazda regisztrálta őket az Azure MFA, és hogy a regisztrációs folyamat befejeződött-e azokat.

Minden felhasználó indulnak *le van tiltva*. Amikor regisztrál az Azure MFA, azok állapotváltozások felhasználók *engedélyezett*. Az engedélyezett felhasználók jelentkezzen be, és végezze el a regisztrációs folyamat során, az állapot változik *kényszerített*.  

### <a name="view-the-status-for-a-user"></a>A felhasználó állapotának megjelenítése

A lap, ahol megtekintheti és kezelheti a felhasználói állapotok eléréséhez használja az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Ugrás a **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **a multi-factor Authentication**.
   ![Válassza ki a multi-factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. Megnyílik egy új lapot, amely megjeleníti a felhasználói állapotok.
   ![a multi-factor authentication felhasználói állapota – képernyőkép](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>A felhasználó állapotának módosítása

1. Az előző lépések segítségével a többtényezős hitelesítést a felhasználók lap.
2. Keresse meg azt a felhasználót, amely az Azure MFA számára engedélyezni szeretné. Lehet, hogy módosítania kell felül a nézetet. 
   ![Keresse meg azt a felhasználót – képernyőkép](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. A név melletti négyzetet.
4. Az első lépéseket, a jobb oldalon válassza ki a **engedélyezése** vagy **letiltása**.
   ![Engedélyezi a kiválasztott felhasználó – képernyőkép](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >*Engedélyezett* felhasználók automatikusan váltani *kényszerített* amikor regisztrálják az Azure MFA számára. Manuálisan nem módosítja a felhasználó állapotának kényszerítettre. 

5. Ellenőrizze a választást, a megjelenő előugró ablakban. 

Miután engedélyezte a felhasználókat, e-mailben értesítse őket. Mondja el neki, hogy azok felkéri, hogy regisztrálja a következő bejelentkezéskor. Is ha a szervezet a böngészőn kívüli alkalmazásokat, amelyek nem támogatják a modern hitelesítést használ, akkor kell létrehoznia az alkalmazásjelszók. Mutató hivatkozást is használható a [Azure MFA végfelhasználói útmutató](./end-user/multi-factor-authentication-end-user.md) felhasználóinál a kezdéshez.

### <a name="use-powershell"></a>A PowerShell használata
Módosíthatja a felhasználói állapot használatával [Azure AD PowerShell](/powershell/azure/overview), módosítsa `$st.State`. Három lehetséges állapota van:

* Engedélyezve
* Kényszerítve
* Letiltva  

Közvetlenül nem kerülnek a felhasználók a *kényszerített* állapotát. A nem a böngészőben megjelenő alkalmazások nem fognak tovább működni, mert a felhasználó nem ment keresztül MFA-regisztráción, illetve nem kapott [alkalmazásjelszót](multi-factor-authentication-whats-next.md#app-passwords). 

PowerShell használatával akkor jó választás, ha a felhasználók tömeges kell. Hozzon létre egy PowerShell-parancsfájlt, amely végighalad a felhasználók listában, és lehetővé teszik, hogy:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Például:

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

Feltételes hozzáférés lehetővé teszi az fizetős az Azure Active Directory, számos konfigurációs beállításokkal. Ezeket a lépéseket bízná egyik módja a szabályzat létrehozásához. További információkért olvassa el [feltételes hozzáférés az Azure Active Directoryban](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Ugrás a **Azure Active Directory** > **feltételes hozzáférés**.
3. Válassza ki **új házirend**.
4. A **hozzárendelések**, jelölje be **felhasználók és csoportok**. Használja a **Include** és **kizárása** lapra, adja meg a házirend mely felhasználók és csoportok fogja kezelni.
5. A **hozzárendelések**, jelölje be **felhőalapú alkalmazásokba**. Foglaljon bele **összes felhőalapú alkalmazások**.
6. A **hozzáférés-szabályozási**, jelölje be **Grant**. Válasszon **többtényezős hitelesítést**.
7. Kapcsolja be **házirend engedélyezése** való **a** majd **mentése**.

A többi beállítást, a feltételes hozzáférési házirend engedélyezi, hogy adjon meg pontosan, amikor a kétlépéses ellenőrzést kell. Például egy házirendet, amely szerint az teheti: alvállalkozói próbál meg elérni a beszerzési alkalmazást a nem megbízható hálózatokhoz, amelyek nincsenek tartományhoz csatlakoztatott eszközön, ha szükséges a kétlépéses ellenőrzést. 

## <a name="next-steps"></a>Következő lépések

- Tippek a [ajánlott eljárások a feltételes hozzáférés](../active-directory/active-directory-conditional-access-best-practices.md)

- A multi-factor Authentication szolgáltatás beállításainak kezelése [a felhasználók és az eszközeik](multi-factor-authentication-manage-users-and-devices.md)
