---
title: A Microsoft Azure multi-factor Authentication felhasználói állapotok
description: Ismerje meg a felhasználói állapotok az Azure multi-factor Authentication hitelesítéshez.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 7a6f6909791fcf60f6c691a830cab85241ae803c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432489"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Egy felhasználó kétlépéses ellenőrzést igénylése

Egy globális rendszergazdai fiók használata szükséges, amelyek a kétlépéses ellenőrzés, hogy a két módszer valamelyikét hajthatja végre. Az első lehetőség, hogy minden felhasználó számára az Azure multi-factor Authentication (MFA) engedélyezéséhez. Amikor a felhasználók külön-külön vannak engedélyezve, a kétlépéses ellenőrzés minden alkalommal, amikor bejelentkeznek az általuk (néhány kivételtől eltekintve, például amikor bejelentkeznek a megbízható IP-címek, vagy ha a _megjegyzett eszközök_ funkció be van kapcsolva). A második lehetőség, hogy bizonyos körülmények között a kétlépéses ellenőrzést igénylő feltételes hozzáférési szabályzat beállítása.

> [!TIP]
> Válasszon egyet ezek a metódusok szükséges a kétlépéses ellenőrzést, nem mindkettőt. Bármely feltételes hozzáférési szabályzatokat az Azure multi-factor Authentication engedélyezése a felhasználó felülbírálja.

## <a name="choose-how-to-enable"></a>Válassza ki a engedélyezése

**Engedélyezve van a felhasználói állapot módosításával** – Ez a hagyományos módszer a kétlépéses ellenőrzést igénylő, és a cikkben leírtak szerint. Mind a felhőbeli Azure MFA és az Azure MFA-kiszolgáló működik. Ezzel a módszerrel megköveteli a felhasználóktól a kétlépéses ellenőrzés végrehajtására **minden alkalommal, amikor** jelentkezzen be, és felülírja a feltételes hozzáférési szabályzatokat. Ez a feltételes hozzáférési funkciók nem tartoznak azok számára, vagy az Office 365-höz, vagy a Microsoft 365 vállalati licenccel rendelkező használt módszer.

Engedélyezte a feltételes hozzáférési szabályzat – Ez a legrugalmasabb, hogy engedélyezze a kétlépéses ellenőrzést, a felhasználók számára. Engedélyezése csak a feltételes hozzáférési szabályzat segítségével az Azure multi-factor Authentication a felhőben működik, és az Azure AD prémium szintű szolgáltatása. Ez a módszer további információ található [üzembe helyezése a felhőalapú Azure multi-factor Authentication](howto-mfa-getstarted.md).

Engedélyezve van az Azure AD Identity Protection - ezt a módszert használja az Azure AD Identity Protection kockázati szabályzat minden felhőalapú alkalmazásra bejelentkezési kockázat alapján a kétlépéses ellenőrzés megkövetelése. Ez a metódus szükséges, Azure Active Directory P2 licencelési. Ez a módszer további információ található [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> További információ a licencekkel és a díjszabás találhatók a [Azure ad-ben](https://azure.microsoft.com/pricing/details/active-directory/
) és [multi-factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) díjszabási lapjait.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Az Azure MFA engedélyezése a felhasználói állapot módosítása

Felhasználói fiókok az Azure multi-factor Authentication az alábbi három jól elkülöníthető állapottal rendelkeznek:

| status | Leírás | A böngészőn kívüli alkalmazások érintett | Az érintett alkalmazások | Modern hitelesítés nélküli érintett |
|:---:|:---:|:---:|:--:|:--:|
| Letiltva |Az Azure MFA-ban nem regisztrált új felhasználó alapértelmezett állapota. |Nem |Nem |Nem |
| Engedélyezve |A felhasználó az Azure MFA-ban regisztrált, de nincs regisztrálva. Amikor legközelebb bejelentkeznek a regisztrációs kapnak. |Nem.  Ezek továbbra is használhatók, amíg a regisztrációs folyamat befejezése. | Igen. A munkamenet lejárata után az Azure MFA-regisztráció szükség.| Igen. A hozzáférési jogkivonat lejárata után az Azure MFA-regisztráció szükség. |
| Kényszerítve |A felhasználó előfizetett, és az Azure MFA-hoz a regisztrációs folyamat befejeződött. |Igen. Alkalmazások alkalmazásjelszavakra. |Igen. Az Azure MFA szükség a bejelentkezéskor. | Igen. Az Azure MFA szükség a bejelentkezéskor. |

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

   > [!TIP]
   > *Engedélyezett* felhasználók automatikusan át *kényszerített* amikor regisztrálja az Azure MFA-hoz. A felhasználói állapot módosítása nem manuálisan tegye *kényszerített*.

5. Erősítse meg választását a megjelenő előugró ablakban.

Miután engedélyezte a felhasználókat, értesítse őket e-mailen keresztül. Mondja el neki, hogy azok regisztrálása a következő bejelentkezéskor felkéri. Emellett a szervezet a böngészőn kívüli alkalmazásokat, amelyek nem támogatják a modern hitelesítést használja, ha szükségük alkalmazásjelszavakat. Mutató hivatkozást is belefoglalhatja a [végfelhasználói útmutató az Azure MFA](../user-help/multi-factor-authentication-end-user.md) , amelyekkel a kezdéshez.

### <a name="use-powershell"></a>A PowerShell használata

A felhasználói állapot módosítása használatával [az Azure AD PowerShell](/powershell/azure/overview), módosítsa `$st.State`. Nincsenek három lehetséges állapota:

* Engedélyezve
* Kényszerítve
* Letiltva  

Ne helyezze át máshová felhasználók közvetlenül a *kényszerített* állapota. Ha így tesz, nem böngészőalapú alkalmazások tovább működni, mert a felhasználó nem ment keresztül az Azure MFA-regisztráció, és kapott egy [alkalmazásjelszót](howto-mfa-mfasettings.md#app-passwords).

A modul telepítéséhez először használatával:

       Install-Module MSOnline
       
> [!TIP]
> Ne felejtse el először létesítsen **Connect-MsolService**


PowerShell-lel jó választás, ha a felhasználók kötegelt engedélyezésének kell. Hozzon létre egy PowerShell-parancsprogram, amely végighalad a felhasználók listáját, és lehetővé teszi őket:

        Import-Module MSOnline
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = "Enabled"
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

A következő parancsfájlt a következő egy példa:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = "Enabled"
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }
    
A letiltott MFA használni ezt a parancsfájlt:

    Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
    
vagy is lehet Rövidítse le:

    Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()

## <a name="next-steps"></a>További lépések

Miért lett egy felhasználó kéri vagy a rendszer nem kéri hajthatok végre MFA? Című témakör [az Azure AD bejelentkezési jelentések a jelentések az Azure multi-factor Authentication dokumentum](howto-mfa-reporting.md#azure-ad-sign-ins-report).

További beállítások, például a megbízható IP-címek, egyedi Hangüzenetek és visszaélési riasztás küldésének, lásd: a cikk [konfigurálása az Azure multi-factor Authentication szolgáltatás beállításainak](howto-mfa-mfasettings.md)

Felhasználói beállítások kezelése az Azure multi-factor Authentication a cikkben található információ [az Azure multi-factor Authentication a felhőben a felhasználói beállítások kezelése](howto-mfa-userdevicesettings.md)
