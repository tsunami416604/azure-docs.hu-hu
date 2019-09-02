---
title: 'Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – gyakori kérdések | Microsoft Docs'
description: Válaszok az Azure Active Directory zökkenőmentes egyszeri bejelentkezéssel kapcsolatos gyakori kérdésekre.
services: active-directory
keywords: Mi az Azure AD Connect, az Azure AD, az egyszeri bejelentkezéshez szükséges összetevők telepítése Active Directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 709fb3be37850be37d6378652921ce26f4ff15fe
ms.sourcegitcommit: dcea3c1ab715a79ebecd913885fbf9bbee61606a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2019
ms.locfileid: "60242226"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Gyakori kérdések

Ebben a cikkben a zökkenőmentes egyszeri bejelentkezéssel (a zökkenőmentes SSO-val) kapcsolatos gyakori kérdéseket tárgyaljuk Azure Active Directory. Az új tartalom ellenőrzésének visszatartása.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Milyen bejelentkezési módszerekkel működik a zökkenőmentes egyszeri bejelentkezés?

A zökkenőmentes egyszeri bejelentkezést a jelszó- [kivonatoló szinkronizálással](how-to-connect-password-hash-synchronization.md) vagy az [átmenő hitelesítési](how-to-connect-pta.md) bejelentkezési módszerekkel kombinálhatja. Ez a funkció azonban nem használható Active Directory összevonási szolgáltatások (AD FS) (ADFS) használatával.

## <a name="is-seamless-sso-a-free-feature"></a>A zökkenőmentes egyszeri bejelentkezés egy ingyenes szolgáltatás?

A zökkenőmentes egyszeri bejelentkezés ingyenes szolgáltatás, és nincs szüksége az Azure AD fizetős kiadásaira a használatához.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Elérhető-e zökkenőmentes egyszeri bejelentkezés a [Microsoft Azure Germany felhőben](https://www.microsoft.de/cloud-deutschland) és a [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/)?

Nem. A zökkenőmentes egyszeri bejelentkezés csak az Azure AD világméretű példányában érhető el.

## <a name="what-applications-take-advantage-of-domain_hint-or-login_hint-parameter-capability-of-seamless-sso"></a>Milyen alkalmazások használhatják a `domain_hint` zökkenőmentes egyszeri bejelentkezést vagy `login_hint` a paramétert?

Az alábbiakban felsoroljuk azokat az alkalmazásokat, amelyek elküldhetik ezeket a paramétereket az Azure AD-be, így a felhasználók csendes bejelentkezési élményt biztosítanak a zökkenőmentes egyszeri bejelentkezés (azaz nem szükséges, hogy a felhasználók be tudják írni a felhasználóneveket vagy a jelszavukat):

| Alkalmazás neve | Használandó alkalmazás-URL-cím |
| -- | -- |
| Hozzáférési panel | https:\//MyApps.microsoft.com/contoso.com |
| Webes Outlook | https:\//outlook.office365.com/contoso.com |
| Office 365 portálok | https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

Emellett a felhasználók csendes bejelentkezési élményt kapnak, ha egy alkalmazás a beérkező kéréseket az Azure ad-ba bérlőként beállított végpontokra küldi, vagyis https:\//login.microsoftonline.com/contoso.com/<. > vagy https:\//login.microsoftonline.com/<tenant_ID>/<.. > – Az Azure ad közös végpontja helyett – vagyis https:\//login.microsoftonline.com/common/<... >. Az alábbi lista az ilyen típusú bejelentkezési kérelmeket elvégező alkalmazások nem teljes listáját tartalmazza.

| Alkalmazás neve | Használandó alkalmazás-URL-cím |
| -- | -- |
| SharePoint Online | https:\//contoso.SharePoint.com |
| Azure Portal | https:\//Portal.Azure.com/contoso.com |

A fenti táblázatokban cserélje le a "contoso.com" kifejezést a tartománynevére, hogy a bérlőnek megfelelő alkalmazás URL-címei legyenek.

Ha azt szeretné, hogy más alkalmazások is csendes bejelentkezési élményt használjanak, tudassa velünk a visszajelzések szakaszban.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Támogatja `Alternate ID` a zökkenőmentes egyszeri bejelentkezést a Felhasználónév `userPrincipalName`helyett?

Igen. A zökkenőmentes egyszeri `Alternate ID` bejelentkezés támogatja a felhasználónevet, ha a Azure ad Connect az [itt](how-to-connect-install-custom.md)látható módon van konfigurálva. Nem minden Office 365-alkalmazás `Alternate ID`támogatja. Tekintse meg az adott alkalmazás dokumentációját a támogatási nyilatkozathoz.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Mi a különbség az [Azure ad JOIN](../active-directory-azureadjoin-overview.md) és a zökkenőmentes egyszeri bejelentkezés szolgáltatás által biztosított egyszeri bejelentkezési élmény között?

Az [Azure ad JOIN](../active-directory-azureadjoin-overview.md) egyszeri bejelentkezést biztosít a felhasználók számára, ha az eszközük regisztrálva van az Azure ad-ben. Ezeket az eszközöket nem feltétlenül kell tartományhoz csatlakoztatni. Az egyszeri bejelentkezés az *elsődleges frissítési tokenekkel* vagy a *PRT*-vel van megadva, és nem Kerberos. A Windows 10-es eszközökön a felhasználói élmény a legoptimálisabb. Az egyszeri bejelentkezés automatikusan megtörténik a Microsoft Edge böngészőben. Emellett a Chrome-ban is működik egy böngésző-bővítmény használatával.

Az Azure AD Joint és a zökkenőmentes egyszeri bejelentkezést is használhatja a bérlőn. Ez a két funkció kiegészíti egymást. Ha mindkét funkció be van kapcsolva, az Azure AD JOIN szolgáltatásból származó egyszeri bejelentkezés elsőbbséget élvez a zökkenőmentes egyszeri bejelentkezéssel szemben.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Nem Windows 10 rendszerű eszközöket szeretnék regisztrálni az Azure AD-vel, AD FS használata nélkül. Használhatom a zökkenőmentes egyszeri bejelentkezést?

Igen, ehhez a forgatókönyvhöz a [munkahelyi csatlakoztatási ügyfél 2,1-](https://www.microsoft.com/download/details.aspx?id=53554)es vagy újabb verziójára van szükség.

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Hogyan lehet átvenni a `AZUREADSSOACC` számítógépfiók Kerberos-visszafejtési kulcsát?

Fontos a helyszíni ad-erdőben létrehozott `AZUREADSSOACC` számítógépfiók (amely az Azure ad-t jelképező) Kerberos-visszafejtési kulcsának gyakori átadása.

>[!IMPORTANT]
>Javasoljuk, hogy legalább 30 naponként átadja a Kerberos-visszafejtési kulcsot.

Kövesse az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen a Azure AD Connect futtatja:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>1\.lépés Azon AD-erdők listájának beolvasása, amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés

1. Először töltse le és telepítse az [Azure ad PowerShellt](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
3. Importálja a zökkenőmentes SSO PowerShell-modult a `Import-Module .\AzureADSSO.psd1`következő parancs használatával:.
4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja `New-AzureADSSOAuthenticationContext`meg a t. Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
5. Hívás `Get-AzureADSSOStatus | ConvertFrom-Json`. Ez a parancs az AD-erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>2\.lépés Frissítse a Kerberos-visszafejtési kulcsot minden olyan AD-erdőben, amelyen be lett állítva

1. Hívás `$creds = Get-Credential`. Ha a rendszer kéri, adja meg a kívánt Active Directory-erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   > A tartományi rendszergazda felhasználónevet használjuk, amelyet az egyszerű felhasználónév (UPN)johndoe@contoso.comformátum vagy a tartomány minősített Sam-fiók neve (contoso\johndoe vagy contoso. com\johndoe) formátumban biztosítanak a kívánt ad-erdő megtalálásához. Ha tartományi minősített Sam-fióknevet használ, a Felhasználónév tartomány részével [Keresse meg a tartományi rendszergazda](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx)tartományvezérlőjét a DNS használatával. Ha ehelyett az UPN-t használja, a megfelelő tartományvezérlő megkeresése előtt lefordítjuk [egy tartományi minősített Sam-fiók nevére](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) .

2. Hívás `Update-AzureADSSOForest -OnPremCredentials $creds`. Ez a parancs frissíti az adott ad-erdőben lévő `AZUREADSSOACC` számítógépfiók Kerberos-visszafejtési kulcsát, és frissíti azt az Azure ad-ben.
3. Ismételje meg a fenti lépéseket minden olyan AD-erdőnél, amelyre beállította a szolgáltatást.

>[!IMPORTANT]
>Győződjön meg arról , hogy nem `Update-AzureADSSOForest` futtatja többször a parancsot. Ellenkező esetben a funkció addig leáll, amíg a felhasználó Kerberos-jegye lejár, és a helyszíni Active Directory újra kiadja.

## <a name="how-can-i-disable-seamless-sso"></a>Hogyan lehet letiltani a zökkenőmentes egyszeri bejelentkezést?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>1\.lépés A szolgáltatás letiltása a bérlőn

#### <a name="option-a-disable-using-azure-ad-connect"></a>A. lehetőség: Letiltás a Azure AD Connect használatával

1. Futtassa Azure AD Connect, válassza a **felhasználói bejelentkezés módosítása lapot** , és kattintson a **tovább**gombra.
2. Törölje az **egyszeri bejelentkezés engedélyezése** lehetőséget. Folytassa a varázslót.

A varázsló befejezése után a rendszer letiltja a zökkenőmentes egyszeri bejelentkezést a bérlőn. A képernyőn megjelenő üzenet azonban a következőképpen jelenik meg:

"Az egyszeri bejelentkezés le van tiltva, de további manuális lépéseket is végre kell hajtani a tisztítás befejezéséhez. További információ "

A tisztítási folyamat befejezéséhez hajtsa végre a 2. és a 3. lépést azon a helyszíni kiszolgálón, amelyen a Azure AD Connect fut.

#### <a name="option-b-disable-using-powershell"></a>B. lehetőség: Letiltás a PowerShell használatával

Futtassa az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen a Azure AD Connect futtatja:

1. Először töltse le és telepítse az [Azure ad PowerShellt](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
3. Importálja a zökkenőmentes SSO PowerShell-modult a `Import-Module .\AzureADSSO.psd1`következő parancs használatával:.
4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja `New-AzureADSSOAuthenticationContext`meg a t. Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
5. Hívás `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>A zökkenőmentes egyszeri bejelentkezés letiltása a PowerShell használatával nem változtatja meg Azure AD Connect állapotát. A zökkenőmentes egyszeri bejelentkezés a **felhasználó bejelentkezési** oldalának módosítása lapon engedélyezettként jelenik meg.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>2\.lépés Azon AD-erdők listájának beolvasása, amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés

Ha a Azure AD Connect használatával letiltotta a zökkenőmentes egyszeri bejelentkezést, kövesse az alábbi 1 – 4. feladatot. Ha letiltotta a zökkenőmentes egyszeri bejelentkezést a PowerShell használatával, ugorjon az alábbi 5. feladatra.

1. Először töltse le és telepítse az [Azure ad PowerShellt](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
3. Importálja a zökkenőmentes SSO PowerShell-modult a `Import-Module .\AzureADSSO.psd1`következő parancs használatával:.
4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja `New-AzureADSSOAuthenticationContext`meg a t. Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
5. Hívás `Get-AzureADSSOStatus | ConvertFrom-Json`. Ez a parancs az AD-erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>3\. lépés. Törölje kézzel a `AZUREADSSOACCT` számítógépfiókot a felsorolt ad-erdőkből.

## <a name="next-steps"></a>További lépések

- [**Gyorskonfigurálás**](how-to-connect-sso-quick-start.md) – az Azure ad zökkenőmentes egyszeri bejelentkezésének megkezdése és futtatása.
- [**Technikai**](how-to-connect-sso-how-it-works.md) részletes útmutató – a funkció működésének megismerése.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új szolgáltatásokra vonatkozó kérelmek bejelentése.
