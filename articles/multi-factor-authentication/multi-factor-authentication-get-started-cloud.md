---
title: "Bevezetés az Azure MFA üzemeltetésébe a felhőben |Microsoft Docs"
description: "Ez a Microsoft Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA használatát a felhőben."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 79c6ef627d20be52b6067d1f6dfea2a53505c590
ms.lasthandoff: 03/17/2017


---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Azure Multi-Factor Authentication a felhőben – első lépések
A cikk végigkalauzolja az Azure Multi-Factor Authentication a felhőben való használatának kezdeti lépésein.

> [!NOTE]
> A következő dokumentáció arról nyújt tájékoztatást, hogyan engedélyezhet felhasználókat a **klasszikus Azure portállal**. Az Azure Multi-Factor Authentication az O365-felhasználók számára való beállításával kapcsolatos információk: [Többtényezős hitelesítés beállítása az Office 365-höz.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA a felhőben](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Előfeltétel
[Regisztráció Azure-előfizetésre](https://azure.microsoft.com/pricing/free-trial/) – Ha még nincs Azure-előfizetése, regisztrálnia kell rá. Ha csak most kezdi és az Azure MFA-t használja, használhat próbaelőfizetést

## <a name="enable-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication engedélyezése
Ha a felhasználói rendelkeznek az Azure Multi-Factor Authenticationt magában foglaló licenccel, semmit nem kell tennie az Azure MFA bekapcsolásához. Felhasználói alapon, egyenként kezdeményezheti a kétlépéses ellenőrzés igénylését. Az Azure MFA használatát lehetővé tévő licencek:
- Azure Multi-Factor Authentication
- Prémium szintű Azure Active Directory
- Enterprise Mobility + Security

Ha nem rendelkezik ilyen licencekkel, vagy nincs elég licence az összes felhasználó lefedéséhez, az sem gond. Csak egy plusz lépésre van szüksége, hogy [létrehozzon egy többtényezős hitelesítési szolgáltatót](multi-factor-authentication-get-started-auth-provider.md) a könyvtárában.

## <a name="turn-on-two-step-verification-for-users"></a>A kétlépéses ellenőrzés bekapcsolása a felhasználók számára
A két kezdeti ellenőrzés a felhasználóktól való megköveteléséhez változtassa meg a felhasználó állapotát letiltottról engedélyezettre.  A felhasználói állapotokkal kapcsolatos további információ: [Felhasználói állapotok az Azure Multi-Factor Authenticationben](multi-factor-authentication-get-started-user-states.md)

A következő eljárással engedélyezheti az MFA-t a felhasználók számára.

### <a name="to-turn-on-multi-factor-authentication"></a>A Multi-Factor Authentication bekapcsolása
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) rendszergazdaként.
2. A bal oldalon kattintson az **Active Directory** elemre.
3. A Címtár részben jelölje ki azt a címtárat, amelyben az engedélyezni kívánt felhasználó található.
   ![Kattintson a címtár lehetőségre](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. A lap tetején kattintson a **Felhasználók** elemre.
5. Kattintson a lap alján található **Multi-Factor Auth kezelése** gombra. Ekkor egy új böngészőlap jelenik meg.
   ![Kattintson a címtár lehetőségre](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Keresse meg a felhasználót, akit a kétlépéses ellenőrzéshez engedélyezni szeretne. Lehet, hogy módosítania kell felül a nézetet. Győződjön meg arról, hogy az állapot **letiltva** értékű.
   ![Felhasználó engedélyezése](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. **Jelölje be** a neve melletti jelölőnégyzetet.
8. A jobb oldalon kattintson az **Engedélyezés** lehetőségre.
   ![Felhasználó engedélyezése](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Kattintson a **Multi-Factor Auth engedélyezése** gombra.
   ![Felhasználó engedélyezése](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Figyelje meg, hogy a felhasználó állapota **letiltottról** **engedélyezettre** változott.
    ![Felhasználók engedélyezése](./media/multi-factor-authentication-get-started-cloud/user.png)

Miután engedélyezte a felhasználókat, e-mailben értesítenie kell őket. Amikor legközelebb megpróbálnak bejelentkezni, a rendszer kérni fogja őket, hogy regisztrálják fiókjukat a kétlépéses ellenőrzéshez. Miután elkezdik használni a kétlépéses ellenőrzést, alkalmazásjelszavakat is be kell állítaniuk a nem a böngészőben megjelenő alkalmazásokból való kizárásuk elkerülése érdekében.

## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>A kétlépéses ellenőrzés bekapcsolásának automatizálása a PowerShell segítségével
Ha az [állapot](multi-factor-authentication-whats-next.md) módosításához az [Azure AD PowerShellt](/powershell/azureps-cmdlets-docs) szeretné használni, tegye a következőket.  Módosítsa úgy a `$st.State` elemet, hogy a következő állapotok valamelyikével rendelkezzen:

* Engedélyezve
* Kényszerítve
* Letiltva  

> [!IMPORTANT]
> A Microsoft nem javasolja a felhasználók közvetlen áthelyezését a Letiltva állapotból a Kényszerítve állapotba. A nem a böngészőben megjelenő alkalmazások nem fognak tovább működni, mert a felhasználó nem ment keresztül MFA-regisztráción, illetve nem kapott [alkalmazásjelszót](multi-factor-authentication-whats-next.md#app-passwords). Ha nem a böngészőben megjelenő alkalmazásokat használ, és alkalmazásjelszavakra van szüksége, ajánlott a Letiltva állapotot Engedélyezve állapotra váltani. Így a felhasználók regisztrálhatnak és beszerezhetik az alkalmazásjelszavaikat. Ezt követően már áthelyezheti őket a Kényszerítve állapotba.

A PowerShell használata a felhasználók kötegelt engedélyezésének egy módszere. Jelenleg nincs kötegelt engedélyezési funkció az Azure portálon, és a felhasználókat egyenként kell kiválasztania. Ez nehéz feladat, ha sok felhasználója van. Ha a következő eljárással PowerShell-parancsprogramot hoz létre, hurkot készíthet a felhasználók listáján, és engedélyezheti őket.

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


További információ: [Felhasználói állapotok az Azure Multi-Factor Authenticationben](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Következő lépések
Most, hogy beállította az Azure Multi-Factor Authenticationt a felhőben, konfigurálhatja és beállíthatja az üzemelő példányt. A részletekről lásd: [Az Azure Multi-Factor Authentication konfigurálása](multi-factor-authentication-whats-next.md).


