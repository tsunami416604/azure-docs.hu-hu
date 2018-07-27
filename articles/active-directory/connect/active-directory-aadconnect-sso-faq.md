---
title: 'Az Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – gyakori kérdések |} A Microsoft Docs'
description: Tudnivalók az Azure Active Directory zökkenőmentes egyszeri bejelentkezés gyakran feltett kérdésekre adott válaszokat.
services: active-directory
keywords: Mi az Azure AD Connect, Active Directory telepítése szükséges összetevők SSO, Azure AD egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 9c59db56ad78818d9b6165d27fd2e64f0bfd902c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283223"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés: gyakori kérdések

Ebben a cikkben azt cím kapcsolatos Azure Active Directory zökkenőmentes egyszeri bejelentkezés (közvetlen egyszeri bejelentkezés) kapcsolatos gyakori kérdésekre. Vissza az új tartalom megtartása ellenőrzése.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Milyen bejelentkezési módszerek közvetlen egyszeri bejelentkezés működnek?

Közvetlen egyszeri bejelentkezés kombinálva, vagy a [Jelszókivonat-szinkronizálás](active-directory-aadconnectsync-implement-password-hash-synchronization.md) vagy [átmenő hitelesítés](active-directory-aadconnect-pass-through-authentication.md) bejelentkezési módszereket. Azonban az Active Directory összevonási szolgáltatások (ADFS) Ez a funkció nem használható.

## <a name="is-seamless-sso-a-free-feature"></a>Közvetlen egyszeri bejelentkezés egy olyan ingyenes szolgáltatás?

Közvetlen egyszeri bejelentkezés egy olyan ingyenes szolgáltatás, és nem kell minden fizetős kiadásban az Azure AD használatát.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Közvetlen egyszeri bejelentkezés érhető el a [Microsoft Azure Germany cloud](http://www.microsoft.de/cloud-deutschland) és a [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/)?

Nem. Közvetlen egyszeri bejelentkezés csak akkor használható az Azure AD világszerte elérhető példányával.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Milyen alkalmazásokat előnyeit `domain_hint` vagy `login_hint` paraméter funkció zökkenőmentes SSO-t?

Az alábbiakban az alkalmazásokat, amelyek ezeket a paramétereket küldése az Azure ad-hez, és ezért felhasználók csendes bejelentkezési élményt nyújt közvetlen egyszeri bejelentkezés (azaz nincs szükség a felhasználók saját felhasználónevek bemeneti) használatával nem teljes listája:

| Alkalmazásnév | Alkalmazás URL-cím használható |
| -- | -- |
| Hozzáférési panel | myapps.microsoft.com/contoso.com |
| Webes Outlook | outlook.office365.com/contoso.com |

Emellett a felhasználók kapnak-e egy csendes bejelentkezési felület Ha kérelmet küld a bejelentkezési kérelmek ábráját végpontok az Azure AD -, https://login.microsoftonline.com/contoso.com/<..> vagy https://login.microsoftonline.com/<tenant_ID>/<..> – helyett az Azure AD közös végpont – vagyis https://login.microsoftonline.com/common/<...>. Az alábbiakban az alkalmazásokat, amelyek az ilyen típusú bejelentkezési kérelmek nem teljes listája.

| Alkalmazásnév | Alkalmazás URL-cím használható |
| -- | -- |
| SharePoint Online | contoso.sharepoint.com |
| Azure Portal | portal.azure.com/contoso.com |

A fenti táblák cserélje le a "contoso.com" a bérlő számára a megfelelő alkalmazás URL-címeinek elérése a tartomány nevét.

Ha azt szeretné, hogy más alkalmazások, amelyek a beavatkozás nélküli bejelentkezést, tudassa velünk, a visszajelzési szakaszban.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Nem támogatja a közvetlen egyszeri bejelentkezés `Alternate ID` felhasználóneve, hanem `userPrincipalName`?

Igen. Támogatja a közvetlen egyszeri bejelentkezés `Alternate ID` konfigurálásakor az Azure AD Connectben látható módon felhasználóneveként [Itt](active-directory-aadconnect-get-started-custom.md). Office 365-höz nem mindegyik alkalmazás támogatja `Alternate ID`. Tekintse meg a támogatási nyilatkozattal az adott alkalmazás dokumentációját.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Mi a különbség a között az egyszeri bejelentkezéses élményben beállítója [az Azure AD Join](../active-directory-azureadjoin-overview.md) és közvetlen egyszeri bejelentkezés?

[Az Azure AD Join](../active-directory-azureadjoin-overview.md) egyszeri Bejelentkezést biztosít a felhasználók számára, ha az eszközeik regisztrálva vannak az Azure AD. Ezek az eszközök nem feltétlenül kell a tartományhoz. Egyszeri bejelentkezés használatával biztosított *elsődleges frissítési biztonsági jogkivonat* vagy *PRTs*, és a Kerberos-nem. A felhasználói élmény a Windows 10 rendszerű eszközökön optimális. Egyszeri bejelentkezés automatikusan megtörténik a Microsoft Edge böngészőt. Azt is működik a Chrome webböngésző-bővítmény használatával.

Használhatja az Azure AD Join és közvetlen egyszeri bejelentkezés a bérlő. Ezen két funkció egymást egészítik ki. Ha mindkét funkciók be vannak kapcsolva, majd egyszeri Bejelentkezést az Azure AD Join elsőbbséget élvez közvetlen egyszeri bejelentkezés.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Szeretnék a Windows 10-eszközök regisztrálása az Azure ad-vel, az AD FS használata nélkül. Használható a közvetlen egyszeri bejelentkezés helyett?

Ebben a forgatókönyvben Igen, van szüksége a verzió a 2.1-es és újabb verzióiban a [munkahelyhez való csatlakozás ügyfél](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Hogyan visszaválthatok keresztül, Kerberos visszafejtési kulcsot a `AZUREADSSOACC` számítógépfiók?

Fontos, hogy milyen gyakran vihetők át a Kerberos visszafejtési kulcs a `AZUREADSSOACC` számítógépfiók (amely az Azure AD) létrehozott a helyszíni AD-erdőben.

>[!IMPORTANT]
>Kifejezetten ajánljuk, hogy hosszabbítsa a Kerberos-visszafejtési kulcs legalább 30 nap.

Kövesse az alábbi lépéseket a helyszíni kiszolgálón hol futnak az Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>1. lépés Ha közvetlen egyszeri bejelentkezés engedélyezve van az AD-erdőkkel listájának beolvasása

1. Először töltse le és telepítse a [Microsoft Online Services bejelentkezési segéd](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Ezután töltse le és telepítse a [64 bites Azure Active Directory-modul Windows Powershellhez készült](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).
3. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
4. Ezzel a paranccsal a zökkenőmentes egyszeri bejelentkezési PowerShell-modul importálása: `Import-Module .\AzureADSSO.psd1`.
5. Futtassa a Powershellt rendszergazdaként. A PowerShellben hívás `New-AzureADSSOAuthenticationContext`. Ez a parancs egy előugró ablak, írja be a bérlő globális rendszergazdai hitelesítő adatokat adjon meg.
6. Hívás `Get-AzureADSSOStatus`. Ez a parancs listáját jeleníti meg, AD-erdőkkel, (tekintse meg a "Tartományok" listája), amelyre ez a funkció engedélyezve van a.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>2. lépés A minden AD-erdőben, amely úgy lett beállítva, a Kerberos-visszafejtési kulcs frissítése

1. Hívás `$creds = Get-Credential`. Amikor a rendszer kéri, adja meg a tartományi rendszergazda hitelesítő adatait a kívánt AD-erdőben.

    >[!NOTE]
    >A tartományi rendszergazda felhasználónév, a felhasználó egyszerű neve (UPN) a megadott használjuk (johndoe@contoso.com) vagy a tartomány minősített sam-fiók neve (contoso\budaipeter vagy contoso.com\johndoe) formátumban, a megfelelő AD-erdőben található. Ha minősített sam-fiók tartománynevet használ, a felhasználónevet, tartományt jelölő része használjuk [keresse meg a tartományvezérlő, a tartományi rendszergazda DNS-sel](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Ha Ehelyett használjon egyszerű felhasználónév azt [lefordíthatja minősített sam-fiók tartománynevét](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) előtt a megfelelő tartományvezérlő keresésekor.

2. Hívás `Update-AzureADSSOForest -OnPremCredentials $creds`. Ez a parancs frissíti a Kerberos visszafejtési kulcs a `AZUREADSSOACC` számítógépfiókját az adott AD-erdő, és frissíti, az Azure ad-ben.
3. Ismételje meg a fenti lépéseket minden beállította a szolgáltatást az AD-erdőben.

>[!IMPORTANT]
>Győződjön meg arról, hogy Ön _nem_ futtassa a `Update-AzureADSSOForest` parancs egynél többször. Ellenkező esetben a funkció nem működik a felhasználók Kerberos-jegyekhez jár le, és a rendszer újból ki a helyszíni Active Directory által a időpontig.

## <a name="how-can-i-disable-seamless-sso"></a>Hogyan tilthatom közvetlen egyszeri bejelentkezés?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>1. lépés A bérlő a funkció letiltásához

#### <a name="option-a-disable-using-azure-ad-connect"></a>"A" lehetőség Tiltsa le az Azure AD Connect használatával

1. Futtassa az Azure AD Connectet, válassza a **módosítása felhasználói bejelentkezési oldal** kattintson **tovább**.
2. Törölje a jelet a **egyszeri bejelentkezés engedélyezése** lehetőséget. Kövesse a varázsló lépéseit.

A varázsló befejezése után a bérlő közvetlen egyszeri bejelentkezés letiltásra kerül. Azonban megjelenik egy üzenet, amely a következő képernyőn:

"Egyszeri bejelentkezés letiltva, de további manuális lépések szükségesek a teljes karbantartás. További információk"

A karbantartási folyamat befejezéséhez hajtsa végre a 2. és 3 a helyszíni kiszolgálón hol futnak az Azure AD Connect.

#### <a name="option-b-disable-using-powershell"></a>"B" lehetőség Tiltsa le a PowerShell használatával

Futtassa az alábbi lépéseket a helyszíni kiszolgálón, ahol futtatja az Azure AD Connect:

1. Először töltse le és telepítse a [Microsoft Online Services bejelentkezési segéd](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Ezután töltse le és telepítse a [64 bites Azure Active Directory-modul Windows Powershellhez készült](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
4. Ezzel a paranccsal a zökkenőmentes egyszeri bejelentkezési PowerShell-modul importálása: `Import-Module .\AzureADSSO.psd1`.
5. Futtassa a Powershellt rendszergazdaként. A PowerShellben hívás `New-AzureADSSOAuthenticationContext`. Ez a parancs egy előugró ablak, írja be a bérlő globális rendszergazdai hitelesítő adatokat adjon meg.
6. Hívás `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>Közvetlen egyszeri bejelentkezés letiltása PowerShell-lel nem változik az állapota, az Azure AD Connectben. Engedélyezve van a közvetlen egyszeri bejelentkezés fog megjelenni a **felhasználói bejelentkezés módosítása** lapot.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>2. lépés Ha közvetlen egyszeri bejelentkezés engedélyezve van az AD-erdőkkel listájának beolvasása

Ha le van tiltva a közvetlen egyszeri bejelentkezés az Azure AD Connect használatával, kövesse a lépéseket: 1 – az alábbi 5. Közvetlen egyszeri bejelentkezés használata esetén inkább a PowerShell le van tiltva, ha ugorhat alábbi 6. lépés.

1. Először töltse le és telepítse a [Microsoft Online Services bejelentkezési segéd](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Ezután töltse le és telepítse a [64 bites Azure Active Directory-modul Windows Powershellhez készült](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
4. Ezzel a paranccsal a zökkenőmentes egyszeri bejelentkezési PowerShell-modul importálása: `Import-Module .\AzureADSSO.psd1`.
5. Futtassa a Powershellt rendszergazdaként. A PowerShellben hívás `New-AzureADSSOAuthenticationContext`. Ez a parancs egy előugró ablak, írja be a bérlő globális rendszergazdai hitelesítő adatokat adjon meg.
6. Hívás `Get-AzureADSSOStatus`. Ez a parancs listáját jeleníti meg, AD-erdőkkel, (tekintse meg a "Tartományok" listája), amelyre ez a funkció engedélyezve van a.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>3. lépés Törölje kézzel a `AZUREADSSOACCT` számítógépfiókkal minden AD-erdőben, amely megjelenik a listában.

## <a name="next-steps"></a>További lépések

- [**Gyors üzembe helyezési** ](active-directory-aadconnect-sso-quick-start.md) – és az Azure AD közvetlen egyszeri bejelentkezés futtatása.
- [**Részletes technikai** ](active-directory-aadconnect-sso-how-it-works.md) – Ez a funkció működésének megismerése.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-sso.md) – ismerje meg, a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkcióra vonatkozó javaslata tárolásához.
