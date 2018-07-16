---
title: A Microsoft Azure multi-factor Authentication felhasználói állapotok
description: Ismerje meg a felhasználói állapotok az Azure multi-factor Authentication hitelesítéshez.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 6945966d4a701ea6e2684b7da766c8b6c9f9a283
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049048"
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Kötelezővé tétele a kétlépéses ellenőrzés egy felhasználó vagy csoport

A kétlépéses ellenőrzést igénylő két módszer valamelyikét hajthatja végre. Az első lehetőség, hogy minden felhasználó számára az Azure multi-factor Authentication (MFA) engedélyezéséhez. Amikor a felhasználók külön-külön vannak engedélyezve, a kétlépéses ellenőrzés minden alkalommal, amikor bejelentkeznek az általuk (néhány kivételtől eltekintve, például amikor bejelentkeznek a megbízható IP-címek, vagy ha a _megjegyzett eszközök_ funkció be van kapcsolva). A második lehetőség, hogy bizonyos körülmények között a kétlépéses ellenőrzést igénylő feltételes hozzáférési szabályzat beállítása.

>[!TIP] 
>Válasszon egyet ezek a metódusok szükséges a kétlépéses ellenőrzést, nem mindkettőt. Bármely feltételes hozzáférési szabályzatokat az Azure multi-factor Authentication engedélyezése a felhasználó felülbírálja.

## <a name="which-option-is-right-for-you"></a>Melyik lehetőség az Önnek való?

**Az Azure többtényezős hitelesítés engedélyezése felhasználói állapotok módosításával** a hagyományos megközelítés a kétlépéses ellenőrzést igénylő. Mind a felhőbeli Azure MFA és az Azure MFA-kiszolgáló működik. Minden olyan felhasználó engedélyezte a kétlépéses hitelesítés végrehajtására, minden alkalommal, amikor bejelentkeznek. Minden olyan feltételes hozzáférési szabályzatokat, amelyek hatással lehetnek, hogy a felhasználó engedélyezése a felhasználó felülbírálja. 

**Feltételes hozzáférési szabályzatot az Azure multi-factor Authentication engedélyezése** a kétlépéses ellenőrzést igénylő rugalmasabb módszer van. Csak működik az Azure multi-factor Authentication a felhőben, és _feltételes hozzáférési_ van egy [fizetős szolgáltatás az Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Csoportok, valamint az egyéni felhasználók számára vonatkozó feltételes hozzáférési szabályzatokat hozhat létre. Magas kockázatú csoportok kaphatnak a további korlátozások, mint az alacsony kockázatú csoportokat, vagy a kétlépéses ellenőrzés csak magas kockázatú felhőalkalmazások szükséges, és kihagyva alacsony kockázatú azokat a. 

Mindkét lehetőséget kérje a felhasználóktól az, amikor először bejelentkezik a követően kapcsolja be a követelmények az Azure multi-factor Authentication regisztrálása. Mindkét lehetőség együttműködnek a konfigurálható [Azure multi-factor Authentication szolgáltatás beállításainak](howto-mfa-mfasettings.md).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Az Azure MFA engedélyezése a felhasználó állapotának módosítása

Felhasználói fiókok az Azure multi-factor Authentication az alábbi három jól elkülöníthető állapottal rendelkeznek:

| status | Leírás | A böngészőn kívüli alkalmazások érintett | Az érintett alkalmazások | Modern hitelesítés nélküli érintett |
|:---:|:---:|:---:|:--:|:--:|
| Letiltva |Az Azure MFA-ban nem regisztrált új felhasználó alapértelmezett állapota. |Nem |Nem |Nem |
| Engedélyezve |A felhasználó az Azure MFA-ban regisztrált, de nincs regisztrálva. Amikor legközelebb bejelentkeznek a regisztrációs kapnak. |Nem.  Ezek továbbra is használhatók, amíg a regisztrációs folyamat befejezése. | Igen. A munkamenet lejárata után az Azure MFA-regisztráció szükség.| Igen. A hozzáférési jogkivonat lejárata után az Azure MFA-regisztráció szükség. |
| Kényszerítve |A felhasználó előfizetett, és az Azure MFA-hoz a regisztrációs folyamat befejeződött. |Igen.  Alkalmazások alkalmazásjelszavakra. |Igen. Az Azure MFA szükség a bejelentkezéskor. | Igen. Az Azure MFA szükség a bejelentkezéskor. |

A felhasználói állapot tükrözi, hogy egy rendszergazda regisztrálta őket az Azure MFA-ban, és hogy azokat tölteni a regisztrációs folyamat.

Minden felhasználó kezd *letiltott*. Ha regisztrálja az Azure MFA felhasználói, állapotuk változik *engedélyezve*. Ha az engedélyezett felhasználók jelentkezzen be, és a regisztráció befejezéséhez, állapotuk változik *kényszerített*.  

### <a name="view-the-status-for-a-user"></a>A felhasználó állapotának megtekintése

A következő lépéseket követve az oldalt, ahol megtekintheti és kezelheti a felhasználói állapotok:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Lépjen a **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki **multi-factor Authentication**.
   ![Válassza ki a multi-factor Authentication szolgáltatás](./media/howto-mfa-userstates/selectmfa.png)
4. Megnyílik egy új lap, amely a felhasználói állapotok jeleníti meg.
   ![a multi-factor authentication felhasználói állapot – képernyőkép](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>A felhasználó állapotának módosítása

1. A fenti lépések segítségével az Azure multi-factor Authentication beszerzése **felhasználók** lapot.
2. Keresse meg a felhasználót, az Azure MFA számára engedélyezni kívánja. Előfordulhat, hogy módosítani szeretné a nézet tetején. 
   ![Keresse meg a felhasználó – képernyőkép](./media/howto-mfa-userstates/enable1.png)
3. Ellenőrizze a név melletti mezőbe.
4. A jobb oldali alatt **Gyorsműveletek**, válassza a **engedélyezése** vagy **letiltása**.
   ![Engedélyezi a kiválasztott felhasználó – képernyőkép](./media/howto-mfa-userstates/user1.png)

   >[!TIP]
   >*Engedélyezett* felhasználók automatikusan át *kényszerített* amikor regisztrálja az Azure MFA-hoz. A felhasználói állapot módosítása nem manuálisan tegye *kényszerített*. 

5. Erősítse meg választását a megjelenő előugró ablakban. 

Miután engedélyezte a felhasználókat, értesítse őket e-mailen keresztül. Mondja el neki, hogy azok regisztrálása a következő bejelentkezéskor felkéri. Emellett a szervezet a böngészőn kívüli alkalmazásokat, amelyek nem támogatják a modern hitelesítést használja, ha szükségük alkalmazásjelszavakat. Mutató hivatkozást is belefoglalhatja a [végfelhasználói útmutató az Azure MFA](../user-help/multi-factor-authentication-end-user.md) , amelyekkel a kezdéshez.

### <a name="use-powershell"></a>A PowerShell használata
A felhasználói állapot módosítása használatával [az Azure AD PowerShell](/powershell/azure/overview), módosítsa `$st.State`. Nincsenek három lehetséges állapota:

* Engedélyezve
* Kényszerítve
* Letiltva  

Ne helyezze át máshová felhasználók közvetlenül a *kényszerített* állapota. Ha így tesz, nem böngészőalapú alkalmazások tovább működni, mert a felhasználó nem ment keresztül az Azure MFA-regisztráció, és kapott egy [alkalmazásjelszót](howto-mfa-mfasettings.md#app-passwords). 

PowerShell-lel jó választás, ha a felhasználók kötegelt engedélyezésének kell. Hozzon létre egy PowerShell-parancsprogram, amely végighalad a felhasználók listáját, és lehetővé teszi őket:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

A következő parancsfájlt a következő egy példa:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzattal együtt az Azure MFA engedélyezése

_Feltételes hozzáférés_ díjköteles funkció az Azure Active Directory számos konfigurációs beállításokkal. Ezek a lépések bemutatják, a szabályzat létrehozásának egyik módja. További információkért olvassa el [feltételes hozzáférés az Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Lépjen a **Azure Active Directory** > **feltételes hozzáférési**.
3. Válassza ki **új szabályzat**.
4. A **hozzárendelések**válassza **felhasználók és csoportok**. Használja a **Belefoglalás** és **kizárása** lapra, adja meg, milyen felhasználókkal és csoportokkal kezeli-e a házirend.
5. A **hozzárendelések**válassza **Felhőalkalmazások**. Válassza ki a befoglalandó **az összes felhőalapú alkalmazások**.
6. A **hozzáférés-vezérlés**válassza **Grant**. Válasszon **többtényezős hitelesítés megkövetelése**.
7. Kapcsolja be **házirend engedélyezése** való **a**, majd válassza ki **mentése**.

A feltételes hozzáférési szabályzat egyéb beállításai lehetővé teszik, megadnia, pontosan akkor, ha a kétlépéses ellenőrzés szükség. Például, hogy egy házirendet, például az egyik: alvállalkozók próbálja meg elérni a beszerzési alkalmazást a nem megbízható hálózatokhoz, amelyek nem tartományhoz csatlakoztatott eszközökön, ha szükséges a kétlépéses ellenőrzést. 

## <a name="next-steps"></a>További lépések

- Tippek a [gyakorlati tanácsok a feltételes hozzáférés](../active-directory-conditional-access-best-practices.md).

- Az Azure multi-factor Authentication szolgáltatás beállításainak kezelése [a felhasználók és eszközeik](howto-mfa-userdevicesettings.md).
