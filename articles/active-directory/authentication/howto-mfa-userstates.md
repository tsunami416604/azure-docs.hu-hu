---
title: Felhasználónkénti többtényezős hitelesítés engedélyezése – Azure Active Directory
description: Ismerje meg, hogyan engedélyezheti a felhasználónkénti Azure többtényezős hitelesítést a felhasználói állapot módosításával
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
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309781"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Felhasználónkénti Azure többtényezős hitelesítés engedélyezése a bejelentkezési események biztonságossá tétele érdekében

Az Azure AD-ben többtényezős hitelesítést igénylő felhasználók bejelentkezési eseményeinek védelme két módon biztosítható. Az első és előnyben részesített beállítás egy feltételes hozzáférési házirend beállítása, amely bizonyos feltételek mellett többtényezős hitelesítést igényel. A második lehetőség az, hogy minden egyes felhasználó számára az Azure többtényezős hitelesítés engedélyezéséhez. Ha a felhasználók külön-külön engedélyezve vannak, többtényezős hitelesítést hajtanak végre minden bejelentkezéskor (néhány kivételtől eltekintve, például amikor megbízható IP-címekről jelentkeznek be, vagy amikor a _megjegyzett eszközök_ funkció be van kapcsolva).

> [!NOTE]
> Az ajánlott megközelítés az Azure többtényezős hitelesítés feltételes hozzáférési szabályzatok használatával történő engedélyezése. A felhasználói állapotok módosítása már nem ajánlott, kivéve, ha a licencek nem tartalmaznak feltételes hozzáférést, mivel a felhasználóknak minden bejelentkezéskor többkori többkori becsmérlést kell végrehajtaniuk.
>
> A feltételes hozzáférés használatának első [lépései: Oktatóanyag: Biztonságos felhasználói bejelentkezési események az Azure többtényezős hitelesítésével című témakörben.](tutorial-enable-azure-mfa.md)

## <a name="azure-multi-factor-authentication-user-states"></a>Az Azure többtényezős hitelesítés felhasználói állapotai

Az Azure többtényezős hitelesítésfelhasználói fiókjainak állapota a következő három különböző állapot:

> [!IMPORTANT]
> Az Azure többtényezős hitelesítés feltételes hozzáférési szabályzaton keresztüli engedélyezése nem változtatja meg a felhasználó állapotát. Ne ijedjen meg, ha a felhasználók letiltva jelennek meg. A feltételes hozzáférés nem módosítja az állapotot.
>
> **Feltételes hozzáférési házirendek használata esetén nem engedélyezheti és nem kényszerítheti a felhasználókat.**

| status | Leírás | Az érintett nem böngészőalkalmazások | Az érintett böngészőalkalmazások | A modern hitelesítésérintett |
|:---:| --- |:---:|:--:|:--:|
| Letiltva | Az Azure többtényezős hitelesítésében nem regisztrált új felhasználó alapértelmezett állapota. | Nem | Nem | Nem |
| Engedélyezve | A felhasználó regisztrálva van az Azure többtényezős hitelesítés, de még nem regisztrált. A következő bejelentkezéskor figyelmeztetést kapnak a regisztrációról. | Nem.  A regisztrációs folyamat befejezéséig folytatják a munkát. | Igen. A munkamenet lejárta után az Azure többtényezős hitelesítés regisztrációja szükséges.| Igen. A hozzáférési jogkivonat lejárta után az Azure többtényezős hitelesítés regisztrációja szükséges. |
| Kényszerítve | A felhasználó regisztrálva van, és befejezte az Azure többtényezős hitelesítés regisztrációs folyamatát. | Igen. Az alkalmazásokhoz alkalmazásjelszavakra van szükség. | Igen. Az Azure többtényezős hitelesítése szükséges a bejelentkezéskor. | Igen. Az Azure többtényezős hitelesítése szükséges a bejelentkezéskor. |

A felhasználó állapota azt tükrözi, hogy egy rendszergazda regisztrálta-e őket az Azure többtényezős hitelesítésében, és hogy befejezték-e a regisztrációs folyamatot.

Minden felhasználó elindul *Letiltva*. Amikor felhasználókat regisztrál az Azure többtényezős hitelesítésében, állapotuk *Engedélyezve lesz.* Amikor az engedélyezett felhasználók bejelentkeznek, és befejezik a regisztrációs folyamatot, állapotuk Kényszerített értékre *változik.*

> [!NOTE]
> Ha az MFA újra engedélyezve van egy olyan felhasználói objektumon, amely már rendelkezik regisztrációs adatokkal, például telefonnal vagy e-maillel, akkor a rendszergazdáknak újra regisztrálniuk kell az MFA-t az Azure Portalon vagy a PowerShellen keresztül. Ha a felhasználó nem regisztrálja újra, az MFA-állapot nem vált *át az Engedélyezettről* *az* MFA-kezelési felhasználói felületre.

## <a name="view-the-status-for-a-user"></a>Felhasználó állapotának megtekintése

Az alábbi lépésekkel érheti el az Azure Portal lap, ahol megtekintheti és kezelheti a felhasználói állapotok:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
1. Keresse meg és válassza az *Azure Active Directory*t, majd válassza a **Felhasználók** > **minden felhasználója lehetőséget.**
1. Válassza a **Többtényezős hitelesítés lehetőséget.** Előfordulhat, hogy a menüpont megtekintéséhez jobbra kell görgetnie. Válassza ki az alábbi példaképernyőképet az Azure Portal teljes ablakának és menühelyének megtekintéséhez:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Válassza a többtényezős hitelesítést az Azure AD Felhasználók ablakából")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Megnyílik egy új lap, amely a felhasználói állapotot jeleníti meg, ahogy az a következő példában látható.
   ![Képernyőkép, amely az Azure többtényezős hitelesítésének felhasználói állapotadatait jeleníti meg](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Felhasználó állapotának módosítása

Az Azure többtényezős hitelesítési állapotának módosításához hajtsa végre az alábbi lépéseket:

1. Az előző lépésekkel az Azure **többtényezős** hitelesítés felhasználói nak lapra való eléréséhez.
1. Keresse meg az Azure többtényezős hitelesítéséhez engedélyezni kívánt felhasználót. Előfordulhat, hogy a felső nézetet felhasználókra kell **módosítania.**
   ![A felhasználók lapon módosíthatja az állapotot módosító felhasználót](./media/howto-mfa-userstates/enable1.png)
1. Jelölje be a felhasználó(k) neve melletti jelölőnégyzetet a felhasználó(k) állapotának módosításához.
1. A jobb oldalon a **gyorslépések**csoportban válassza az **Engedélyezés** vagy **a Letiltás lehetőséget.** A következő példában a *felhasználó, Smith János* egy pipa mellett ![a nevüket, és engedélyezve van a használatra: A kijelölt felhasználó engedélyezése a gyorslépések menü Engedélyezés parancsára kattintva](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Az engedélyezett* felhasználók automatikusan átváltanak *kényszerítésre,* amikor regisztrálnak az Azure többtényezős hitelesítésre. Ne módosítsa manuálisan a felhasználói állapotot *Kényszerített*állapotra.

1. Erősítse meg a kijelölést a megnyíló előugró ablakban.

Miután engedélyezte a felhasználókat, értesítse őket e-mailben. Mondja meg a felhasználóknak, hogy a következő bejelentkezéskor kérje meg a felhasználókat, hogy regisztráljanak. Továbbá, ha a szervezet nem böngészőalkalmazásokat használ, amelyek nem támogatják a modern hitelesítést, létre kell hozniuk az alkalmazásjelszavakat. További információkért tekintse meg az [Azure többtényezős hitelesítés végfelhasználói útmutató,](../user-help/multi-factor-authentication-end-user.md) hogy segítsen nekik az első lépésekhez.

## <a name="change-state-using-powershell"></a>Állapot módosítása a PowerShell használatával

A felhasználói állapot módosítása az [Azure AD PowerShell](/powershell/azure/overview)használatával, módosítsa a `$st.State` felhasználói fiók paraméterét. Egy felhasználói fióknak három lehetséges állapota van:

* *Engedélyezve*
* *Kényszerítve*
* *Letiltva*  

Ne helyezze át a felhasználókat közvetlenül a *Kényszerített* állapotba. Ha így tesz, a nem böngészőalapú alkalmazások nem működnek, mert a felhasználó nem ment át az Azure többtényezős hitelesítésregisztráción, és nem kapott meg egy [alkalmazásjelszót.](howto-mfa-mfasettings.md#app-passwords)

Első lépésekhez telepítse az *MSOnline* modult az [Install-Module](/powershell/module/powershellget/install-module) használatával az alábbiak szerint:

```PowerShell
Install-Module MSOnline
```

Ezután csatlakozzon a [Connect-MsolService használatával:](/powershell/module/msonline/connect-msolservice)

```PowerShell
Connect-MsolService
```

A következő példa powershell-parancsfájl engedélyezi az *bsimon@contoso.com*MFA-t egy névvel ellátott felhasználó számára:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

A PowerShell használata jó lehetőség, ha tömegesen engedélyeznie kell a felhasználókat. A következő parancsfájl hurkok a felhasználók listáját, és lehetővé teszi az MFA a fiókjukban. Adja meg az első sorban beállított `$users` felhasználói fiókokat az alábbiak szerint:

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

Az MFA letiltásához a következő példa bejut egy felhasználóhoz a [Get-MsolUser](/powershell/module/msonline/get-msoluser)segítségével, majd eltávolítja a megadott felhasználóhoz beállított *StrongAuthenticationRequirements* beállításokat a [Set-MsolUser](/powershell/module/msonline/set-msoluser)használatával:

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

A Set-MsolUser használatával közvetlenül is letilthatja az [MFA-t a Set-MsolUser](/powershell/module/msonline/set-msoluser) használatával az alábbiak szerint:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Felhasználók átalakítása felhasználónkénti többszintű többszintű támogatásból feltételes hozzáférésalapú többszintű hozzáférésű többszintű hozzáférési programra

A következő PowerShell segítséget nyújt a feltételes hozzáférésalapú Azure többtényezős hitelesítésre való átalakításban.

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
> Nemrég módosítottuk a viselkedést és ezt a PowerShell-parancsfájlt. Korábban a parancsfájl mentette ki az MFA-metódusok, letiltotta az MFA, és visszaállította a módszereket. Erre már nincs szükség, most, hogy a letiltás alapértelmezett viselkedése nem törli a metódusok at.
>
> Ha az MFA újra engedélyezve van egy olyan felhasználói objektumon, amely már rendelkezik regisztrációs adatokkal, például telefonnal vagy e-maillel, akkor a rendszergazdáknak újra regisztrálniuk kell az MFA-t az Azure Portalon vagy a PowerShellen keresztül. Ha a felhasználó nem regisztrálja újra, az MFA-állapot nem vált *át az Engedélyezettről* *az* MFA-kezelési felhasználói felületre.

## <a name="next-steps"></a>További lépések

Az Azure többtényezős hitelesítési beállításainak, például a megbízható IP-cím, az egyéni hangüzenetek és a csalási riasztások konfigurálásához olvassa el [az Azure többtényezős hitelesítési beállításainak konfigurálása című](howto-mfa-mfasettings.md)témakört. Az Azure többtényezős hitelesítésfelhasználói beállításainak kezeléséhez olvassa el [a Felhasználói beállítások kezelése az Azure többtényezős hitelesítéssel című témakört.](howto-mfa-userdevicesettings.md)

Ha tudni szeretné, hogy miért kéri a felhasználó az MFA végrehajtására, vagy miért nem, olvassa el az [Azure többtényezős hitelesítési jelentéseit.](howto-mfa-reporting.md#azure-ad-sign-ins-report)
