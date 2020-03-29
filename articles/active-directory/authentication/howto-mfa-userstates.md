---
title: Felhasználónkénti többtényezős hitelesítés – Azure Active Directory
description: Engedélyezze az MFA-t az Azure többtényezős hitelesítésfelhasználói állapotának módosításával.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61d7227c57422cfe2228002750ec29bffa385d44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756766"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Kétlépéses ellenőrzés megkövetelése egy felhasználó számára

A kétlépéses ellenőrzés megköveteléséhez két módszert alkalmazhat, amelyek mindegyike globális rendszergazdai fiókot igényel. Az első lehetőség az, hogy minden egyes felhasználó számára az Azure többtényezős hitelesítés (MFA) engedélyezése. Ha a felhasználók külön-külön vannak engedélyezve, minden bejelentkezéskor kétlépéses ellenőrzést végeznek (néhány kivételtől eltekintve, például amikor megbízható IP-címekről jelentkeznek be, vagy amikor a _megjegyzett eszközök_ funkció be van kapcsolva). A második lehetőség egy feltételes hozzáférési házirend beállítása, amely bizonyos feltételek mellett kétlépéses ellenőrzést igényel.

> [!TIP]
> Az ajánlott megközelítés az Azure többtényezős hitelesítés feltételes hozzáférési szabályzatok használatával történő engedélyezése. A felhasználói állapotok módosítása már nem ajánlott, kivéve, ha a licencek nem tartalmaznak feltételes hozzáférést, mivel a felhasználóknak minden bejelentkezéskor többkori többkori betöltést kell végrehajtaniuk.

## <a name="choose-how-to-enable"></a>Válassza ki, hogyan szeretné engedélyezni

**Felhasználóállapot ának módosításával engedélyezve** – Ez a hagyományos módszer a kétlépéses ellenőrzés megkövetelésére, és ebben a cikkben tárgyalja. Az Azure MFA-val a felhőben és az Azure MFA-kiszolgálóval is működik. Ezzel a módszerrel a felhasználóknak kétlépéses ellenőrzést kell végrehajtaniuk **minden bejelentkezéskor,** és felülbírálják a feltételes hozzáférési házirendeket.

**Feltételes hozzáférési házirend által engedélyezve** – Ez a legrugalmasabb eszköz a kétlépéses ellenőrzés engedélyezéséhez a felhasználók számára. Feltételes hozzáférés házirend használatával történő engedélyezés csak akkor működik, az Azure MFA a felhőben, és az Azure AD prémium szintű szolgáltatása. Erről a módszerről további információ a [felhőalapú Azure többtényezős hitelesítés telepítése](howto-mfa-getstarted.md)című részben található.

**Az Azure AD Identity Protection által engedélyezett** – Ez a módszer az Azure AD Identity Protection kockázati szabályzatát használja a kétlépéses ellenőrzés megköveteléséhez, csak az összes felhőalkalmazás bejelentkezési kockázata alapján. Ehhez a módszerhez Azure Active Directory P2 licencelésszükséges. Erről a módszerről további információ az [Azure Active Directory identitásvédelemben található.](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> A licencekről és a díjszabásról az [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) és a [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) díjszabási oldalakon talál további információt.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Az Azure MFA engedélyezése a felhasználói állapot módosításával

Az Azure többtényezős hitelesítésfelhasználói fiókjainak állapota a következő három különböző állapot:

> [!IMPORTANT]
> Az Azure MFA feltételes hozzáférési szabályzaton keresztül i. feltétel házirend nem változtatja meg a felhasználó állapotát. Ne ijedjen meg, hogy a felhasználók letiltva jelennek meg. A feltételes hozzáférés nem változtatja meg az állapotot. **A szervezetek nem engedélyezhetik és nem kényszeríthetik a felhasználókat, ha feltételes hozzáférési házirendeket használnak.**

| status | Leírás | Az érintett nem böngészőalkalmazások | Az érintett böngészőalkalmazások | A modern hitelesítésérintett |
|:---:| --- |:---:|:--:|:--:|
| Letiltva | Az Azure MFA-ban nem regisztrált új felhasználó alapértelmezett állapota. | Nem | Nem | Nem |
| Engedélyezve | A felhasználó regisztrálva van az Azure MFA-ban, de nem regisztrált. A következő bejelentkezéskor figyelmeztetést kapnak a regisztrációról. | Nem.  A regisztrációs folyamat befejezéséig folytatják a munkát. | Igen. A munkamenet lejárta után az Azure MFA-regisztráció szükséges.| Igen. A hozzáférési jogkivonat lejárta után az Azure MFA-regisztráció szükséges. |
| Kényszerítve | A felhasználó regisztrálva van, és befejezte az Azure MFA regisztrációs folyamatát. | Igen. Az alkalmazásokhoz alkalmazásjelszavakra van szükség. | Igen. Az Azure MFA bejelentkezéskor szükséges. | Igen. Az Azure MFA bejelentkezéskor szükséges. |

A felhasználó állapota azt tükrözi, hogy egy rendszergazda regisztrálta-e őket az Azure MFA-ban, és hogy befejezték-e a regisztrációs folyamatot.

Minden felhasználó elindul *Letiltva*. Amikor felhasználókat regisztrál az Azure MFA-ban, állapotuk *Engedélyezve lesz.* Amikor az engedélyezett felhasználók bejelentkeznek, és befejezik a regisztrációs folyamatot, állapotuk Kényszerített értékre *változik.*

> [!NOTE]
> Ha az MFA újra engedélyezve van egy olyan felhasználói objektumon, amely már rendelkezik regisztrációs adatokkal, például telefonnal vagy e-maillel, akkor a rendszergazdáknak újra regisztrálniuk kell az MFA-t az Azure Portalon vagy a PowerShellen keresztül. Ha a felhasználó nem regisztrálja újra, az MFA-állapot nem vált *át az Engedélyezettről* *az* MFA-kezelési felhasználói felületre.

### <a name="view-the-status-for-a-user"></a>Felhasználó állapotának megtekintése

Az alábbi lépésekkel érheti el azt a lapot, amelyen megtekintheti és kezelheti a felhasználói állapotokat:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
2. Keresse meg és válassza ki az *Azure Active Directoryt*. Válassza a **Felhasználók** > **minden felhasználó lehetőséget.**
3. Válassza a **Többtényezős hitelesítés lehetőséget.** Előfordulhat, hogy a menüpont megtekintéséhez jobbra kell görgetnie. Válassza ki az alábbi példaképernyőképet az Azure Portal teljes ablakának és menühelyének megtekintéséhez:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Válassza a többtényezős hitelesítést az Azure AD Felhasználók ablakából")](media/howto-mfa-userstates/selectmfa.png#lightbox)
4. Megnyílik egy új lap, amely a felhasználói állapotokat jeleníti meg.
   ![többtényezős hitelesítés felhasználói állapota - képernyőkép](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Felhasználó állapotának módosítása

1. Az előző lépésekkel az Azure **többtényezős** hitelesítés felhasználói nak lapra való eléréséhez.
2. Keresse meg az Azure MFA-hoz engedélyezni kívánt felhasználót. Előfordulhat, hogy módosítania kell a felső nézetet.
   ![A felhasználók lapon módosíthatja az állapotot módosító felhasználót](./media/howto-mfa-userstates/enable1.png)
3. Jelölje be a nevük melletti jelölőnégyzetet.
4. A jobb oldalon a **gyorslépések**csoportban válassza az **Engedélyezés** vagy **a Letiltás lehetőséget.**
   ![A kijelölt felhasználó engedélyezése a gyorslépések menü Engedélyezés parancsára kattintva](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Az engedélyezett* felhasználók automatikusan átvált *a Kényszerített,* amikor regisztrálnak az Azure MFA. Ne módosítsa manuálisan a felhasználói állapotot *Kényszerített*állapotra.

5. Erősítse meg a kijelölést a megnyíló előugró ablakban.

Miután engedélyezte a felhasználókat, értesítse őket e-mailben. Mondja meg nekik, hogy a következő bejelentkezéskor regisztrálniuk kell. Továbbá, ha a szervezet nem böngészőalkalmazásokat használ, amelyek nem támogatják a modern hitelesítést, létre kell hozniuk az alkalmazásjelszavakat. Az [Azure MFA végfelhasználói útmutatójára](../user-help/multi-factor-authentication-end-user.md) mutató hivatkozást is megadhat, hogy segítsen nekik az első lépésekhez.

### <a name="use-powershell"></a>A PowerShell használata

Ha módosítani szeretné a felhasználói állapotot az `$st.State` [Azure AD PowerShell](/powershell/azure/overview)használatával, módosítsa a . Három lehetséges állapot létezik:

* Engedélyezve
* Kényszerítve
* Letiltva  

Ne helyezze át a felhasználókat közvetlenül a *Kényszerített* állapotba. Ha így tesz, a nem böngészőalapú alkalmazások nem működnek, mert a felhasználó nem ment át az Azure MFA-regisztráción, és [megszerezte az alkalmazás jelszavát.](howto-mfa-mfasettings.md#app-passwords)

Először telepítse a modult a következő használatával:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Ne felejtsen el először csatlakozni a **Connect-MsolService használatával**

   ```PowerShell
   Connect-MsolService
   ```

Ez a példa powershell-parancsfájl engedélyezi az MFA-t egy adott felhasználó számára:

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

A PowerShell használata jó lehetőség, ha tömegesen engedélyeznie kell a felhasználókat. Például a következő parancsfájl hurkok egy listát a felhasználók, és lehetővé teszi az MFA a fiókjukban:

   ```PowerShell
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

Az MFA letiltásához használja ezt a parancsfájlt:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

amely szintén lerövidíthető:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Felhasználók átalakítása felhasználónkénti többszintű többszintű támogatásból feltételes hozzáférésalapú többszintű hozzáférésű többszintű hozzáférési programra

A következő PowerShell segítséget nyújt a feltételes hozzáférésalapú Azure többtényezős hitelesítésre való átalakításban.

Futtassa ezt a PowerShellt egy ISE-ablakban, vagy mentse . PS1 fájlt helyileg futtatni.

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
> Nemrég módosítottuk a viselkedést és a PowerShell-parancsfájl t ennek megfelelően. Korábban a parancsfájl mentette ki az MFA-metódusok, letiltotta az MFA, és visszaállította a módszereket. Erre már nincs szükség, most, hogy a letiltás alapértelmezett viselkedése nem törli a metódusok at.
>
> Ha az MFA újra engedélyezve van egy olyan felhasználói objektumon, amely már rendelkezik regisztrációs adatokkal, például telefonnal vagy e-maillel, akkor a rendszergazdáknak újra regisztrálniuk kell az MFA-t az Azure Portalon vagy a PowerShellen keresztül. Ha a felhasználó nem regisztrálja újra, az MFA-állapot nem vált *át az Engedélyezettről* *az* MFA-kezelési felhasználói felületre.

## <a name="next-steps"></a>További lépések

* Miért kéri a rendszer a felhasználót az MFA végrehajtására? Tekintse meg az [Azure AD bejelentkezések jelentés az Azure többtényezős hitelesítési dokumentumban a jelentések című szakaszban.](howto-mfa-reporting.md#azure-ad-sign-ins-report)
* További beállítások, például megbízható IP-k, egyéni hangüzenetek és csalási riasztások konfigurálásához olvassa el az [Azure többtényezős hitelesítési beállításainak konfigurálása](howto-mfa-mfasettings.md) című témakört.
* Az Azure többtényezős hitelesítésfelhasználói beállításainak kezeléséről a Felhasználói beállítások kezelése az [Azure többtényezős hitelesítéssel a felhőben](howto-mfa-userdevicesettings.md) című cikkben található.
