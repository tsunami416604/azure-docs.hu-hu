---
title: Felhasználónkénti Multi-Factor Authentication engedélyezése – Azure Active Directory
description: Megtudhatja, hogyan engedélyezheti a felhasználónkénti Azure-Multi-Factor Authentication a felhasználói állapot módosításával
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ea97d7a2aa5fdc18d11e952eafe65b167b3397
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2020
ms.locfileid: "84483926"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Az Azure Multi-Factor Authentication felhasználónkénti engedélyezése a bejelentkezési események biztonságossá tételéhez

A többtényezős hitelesítés az Azure AD-ben kétféleképpen biztosítható a felhasználói bejelentkezési események biztonságossá tételéhez. Az első és az előnyben részesített lehetőség egy olyan feltételes hozzáférési szabályzat beállítása, amely bizonyos feltételekkel megköveteli a többtényezős hitelesítést. A második lehetőség az, hogy minden felhasználó számára engedélyezze az Azure Multi-Factor Authentication. Ha a felhasználók külön engedélyezve vannak, a többtényezős hitelesítést a bejelentkezéskor minden alkalommal végrehajtják (bizonyos kivételekkel, például amikor bejelentkeznek a megbízható IP-címekről, vagy ha a _megjegyezett eszközök_ funkció be van kapcsolva).

> [!NOTE]
> Az Azure-Multi-Factor Authentication feltételes hozzáférési szabályzatok használatával történő engedélyezése az ajánlott módszer. A felhasználói állapotok módosítása már nem ajánlott, kivéve, ha a licencek nem tartalmazzák a feltételes hozzáférést, mert a felhasználóknak minden bejelentkezéskor a többtényezős hitelesítést kell végezniük. A feltételes hozzáférés használatának megkezdéséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure multi-Factor Authentication](tutorial-enable-azure-mfa.md)használatával című témakört.
>
> A feltételes hozzáférés nélküli Azure AD-bérlők esetében az [alapértelmezett biztonsági beállítások használatával biztosítható a felhasználók védelme](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Azure Multi-Factor Authentication felhasználói állapotok

Az Azure Multi-Factor Authentication felhasználói fiókjai a következő három különböző állapottal rendelkeznek:

> [!IMPORTANT]
> Az Azure Multi-Factor Authentication feltételes hozzáférési házirenddel való engedélyezése nem változtatja meg a felhasználó állapotát. Nem szabad riasztást adni, ha a felhasználók le vannak tiltva. A feltételes hozzáférés nem változtatja meg az állapotot.
>
> **Ha feltételes hozzáférési szabályzatokat használ, ne engedélyezze és ne kényszerítse ki a felhasználókat.**

| Állapot | Leírás | Érintett nem böngészőbeli alkalmazások | Érintett böngészőalapú alkalmazások | A modern hitelesítés érintett |
|:---:| --- |:---:|:--:|:--:|
| Letiltva | Az Azure Multi-Factor Authenticationban nem regisztrált új felhasználók alapértelmezett állapota. | Nem | Nem | Nem |
| Engedélyezve | A felhasználó regisztrálva lett az Azure Multi-Factor Authenticationban, de nincs regisztrálva. A következő bejelentkezés alkalmával a rendszer felszólítja a regisztrálásra. | Nem.  Továbbra is működnek, amíg a regisztrációs folyamat be nem fejeződik. | Igen. A munkamenet lejárata után az Azure Multi-Factor Authentication regisztrációra van szükség.| Igen. A hozzáférési jogkivonat lejárta után az Azure Multi-Factor Authentication regisztrációra van szükség. |
| Kényszerítve | A felhasználó regisztrálva lett, és befejezte az Azure Multi-Factor Authentication regisztrációs folyamatát. | Igen. Az alkalmazásokhoz alkalmazások jelszava szükséges. | Igen. Bejelentkezéskor Azure Multi-Factor Authentication szükséges. | Igen. Bejelentkezéskor Azure Multi-Factor Authentication szükséges. |

A felhasználó állapota azt jelzi, hogy egy rendszergazda regisztrálta-e őket az Azure Multi-Factor Authenticationban, és hogy elvégezték-e a regisztrációs folyamatot.

Az összes felhasználó *le van tiltva*. Amikor felhasználókat regisztrál az Azure Multi-Factor Authenticationban, az állapotuk *engedélyezve*értékre vált. Ha az engedélyezett felhasználók bejelentkeznek, és elvégzik a regisztrációs folyamatot, az állapotuk *kényszerítve*értékűre vált.

> [!NOTE]
> Ha a többtényezős hitelesítés engedélyezve van egy olyan felhasználói objektumon, amely már rendelkezik regisztrációs adatokkal, például telefonon vagy e-mailben, akkor a rendszergazdáknak Azure Portal vagy PowerShell használatával újra regisztrálniuk kell az MFA-t. Ha a felhasználó nem regisztrálja újra a regisztrációt, az MFA-állapota nem lesz *engedélyezve* az MFA felügyeleti felhasználói felületén való *kikényszerített* állapotra.

## <a name="view-the-status-for-a-user"></a>Felhasználó állapotának megtekintése

A következő lépésekkel érheti el a Azure Portal oldalt, amelyen megtekintheti és kezelheti a felhasználói állapotokat:

1. Jelentkezzen be rendszergazdaként a [Azure Portalba](https://portal.azure.com) .
1. Keresse meg és válassza ki *Azure Active Directory*, majd válassza a **felhasználók**  >  **minden felhasználó**lehetőséget.
1. Válassza a **multi-Factor Authentication**lehetőséget. Előfordulhat, hogy a menüpontra kell görgetni a jobb oldalon. Válassza az alábbi képernyőképet a teljes Azure Portal ablak és menü helyének megtekintéséhez:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Multi-Factor Authentication kiválasztása az Azure AD felhasználók ablakában")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Megnyílik egy új lap, amely megjeleníti a felhasználói állapotot, ahogy az az alábbi példában is látható.
   ![Képernyőkép az Azure-beli felhasználói állapotadatokat bemutató Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Felhasználó állapotának módosítása

Ha módosítani szeretné egy felhasználó Azure Multi-Factor Authentication állapotát, hajtsa végre a következő lépéseket:

1. Az előző lépésekkel érheti el az Azure Multi-Factor Authentication- **felhasználók** lapot.
1. Keresse meg azt a felhasználót, aki számára engedélyezni szeretné az Azure Multi-Factor Authentication. Előfordulhat, hogy a felül kell változtatnia a nézetet a **felhasználók számára**.
   ![Válassza ki a felhasználót az állapot módosításához a felhasználók lapon](./media/howto-mfa-userstates/enable1.png)
1. Jelölje be a felhasználó (k) neve melletti jelölőnégyzetet a (z) állapotának módosításához.
1. A jobb oldalon, a **gyors lépések**területen válassza az **Engedélyezés** vagy a **Letiltás**lehetőséget. A következő példában a *John Smith* felhasználó neve mellett egy pipa van, és engedélyezve van a használatra: a ![ kijelölt felhasználó engedélyezése a gyors lépések menüben az Engedélyezés gombra kattintva](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Az *engedélyezett* felhasználók automatikusan *érvénybe* lettek állítva az Azure-multi-Factor Authentication való regisztráláskor. Ne módosítsa manuálisan a felhasználói állapotot *kényszerített*értékre.

1. Erősítse meg a kijelölést a megnyíló előugró ablakban.

A felhasználók engedélyezése után értesítse őket e-mailben. Tájékoztassa a felhasználókat arról, hogy egy üzenet jelenik meg, hogy regisztrálja őket a következő bejelentkezés alkalmával. Továbbá, ha a szervezete nem böngészőbeli alkalmazásokat használ, amelyek nem támogatják a modern hitelesítést, létre kell hozniuk az alkalmazáshoz tartozó jelszavakat. További információkért tekintse meg az [Azure multi-Factor Authentication végfelhasználói útmutatót](../user-help/multi-factor-authentication-end-user.md) , amely segítséget nyújt az első lépésekhez.

## <a name="change-state-using-powershell"></a>Állapot módosítása a PowerShell használatával

Ha a felhasználói állapotot az [Azure ad PowerShell](/powershell/azure/overview)használatával szeretné módosítani, módosítsa `$st.State` egy felhasználói fiók paraméterét. Egy felhasználói fióknak három lehetséges állapota van:

* *Engedélyezve*
* *Kényszerítve*
* *Letiltva*  

Ne helyezze át a felhasználókat közvetlenül a *kényszerített* állapotba. Ha így tesz, a nem böngészőalapú alkalmazások nem működnek, mert a felhasználó nem ment át az Azure Multi-Factor Authentication-regisztrációval, és nem kapott meg egy [alkalmazás jelszavát](howto-mfa-app-passwords.md).

Első lépésként telepítse a *MSOnline* modult a [install-Module](/powershell/module/powershellget/install-module) paranccsal a következőképpen:

```PowerShell
Install-Module MSOnline
```

Következő lépésként kapcsolódjon a [MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

A következő példa PowerShell-parancsfájl lehetővé teszi az MFA használatát egy nevű egyéni felhasználó számára *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

A PowerShell használata jó megoldás, ha a felhasználók tömeges engedélyezésére van szükség. A következő szkript hurkokat mutat a felhasználók listáján, és lehetővé teszi az MFA használatát a fiókjaik számára. Adja meg a felhasználói fiókokat az első sorban a `$users` következőképpen:

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Az MFA letiltásához a következő példa beolvas egy felhasználót a [Get-MsolUser](/powershell/module/msonline/get-msoluser), majd eltávolítja a [set-MsolUser](/powershell/module/msonline/set-msoluser)használatával a megadott felhasználóhoz tartozó *StrongAuthenticationRequirements* .

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Az MFA-t közvetlenül is letilthatja egy felhasználó számára a [set-MsolUser](/powershell/module/msonline/set-msoluser) használatával, a következőképpen:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Felhasználók konvertálása felhasználónkénti MFA-ből feltételes hozzáférésen alapuló MFA-ra

A következő PowerShell segítséget nyújt a feltételes hozzáférésű Azure-Multi-Factor Authentication átalakításához.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Nemrég módosítottuk a viselkedést és a PowerShell-szkriptet. Korábban a parancsfájl mentve az MFA-metódusokból, letiltotta az MFA-t, és visszaállította a metódusokat. Ez már nem szükséges ahhoz, hogy a Letiltás alapértelmezett viselkedése ne törölje a metódusokat.
>
> Ha a többtényezős hitelesítés engedélyezve van egy olyan felhasználói objektumon, amely már rendelkezik regisztrációs adatokkal, például telefonon vagy e-mailben, akkor a rendszergazdáknak Azure Portal vagy PowerShell használatával újra regisztrálniuk kell az MFA-t. Ha a felhasználó nem regisztrálja újra a regisztrációt, az MFA-állapota nem lesz *engedélyezve* az MFA felügyeleti felhasználói felületén való *kikényszerített* állapotra.

## <a name="next-steps"></a>Következő lépések

Az Azure Multi-Factor Authentication beállításainak, például a megbízható IP-címek, az egyéni hangüzenetek és a csalási riasztások konfigurálásával kapcsolatban lásd: az [azure multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md). Az Azure Multi-Factor Authentication felhasználói beállításainak kezeléséhez lásd: [felhasználói beállítások kezelése az azure multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Ha meg szeretné tudni, hogy egy felhasználó miért kérdezte meg a felhasználót, vagy nem kéri az MFA-t, olvassa el az [Azure multi-Factor Authentication-jelentések](howto-mfa-reporting.md)
