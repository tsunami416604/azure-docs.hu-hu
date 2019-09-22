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
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176663"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory zökkenőmentes egyszeri bejelentkezés: Gyakori kérdések

Ebben a cikkben a zökkenőmentes egyszeri bejelentkezéssel (a zökkenőmentes SSO-val) kapcsolatos gyakori kérdéseket tárgyaljuk Azure Active Directory. Az új tartalom ellenőrzésének visszatartása.

**K: A bejelentkezési módszerek használata zökkenőmentes egyszeri bejelentkezéssel**

A zökkenőmentes egyszeri bejelentkezést a jelszó- [kivonatoló szinkronizálással](how-to-connect-password-hash-synchronization.md) vagy az [átmenő hitelesítési](how-to-connect-pta.md) bejelentkezési módszerekkel kombinálhatja. Ez a funkció azonban nem használható Active Directory összevonási szolgáltatások (AD FS) (ADFS) használatával.

**K: A zökkenőmentes egyszeri bejelentkezés egy ingyenes szolgáltatás?**

A zökkenőmentes egyszeri bejelentkezés ingyenes szolgáltatás, és nincs szüksége az Azure AD fizetős kiadásaira a használatához.

**K: Elérhető-e zökkenőmentes egyszeri bejelentkezés a [Microsoft Azure Germany felhőben](https://www.microsoft.de/cloud-deutschland) és a [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/)?**

Nem. A zökkenőmentes egyszeri bejelentkezés csak az Azure AD világméretű példányában érhető el.

**K: Milyen alkalmazások használhatják a `domain_hint` zökkenőmentes egyszeri bejelentkezést vagy `login_hint` a paramétert?**

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

**K: Támogatja `Alternate ID` a zökkenőmentes egyszeri bejelentkezést a Felhasználónév `userPrincipalName`helyett?**

Igen. A zökkenőmentes egyszeri `Alternate ID` bejelentkezés támogatja a felhasználónevet, ha a Azure ad Connect az [itt](how-to-connect-install-custom.md)látható módon van konfigurálva. Nem minden Office 365-alkalmazás `Alternate ID`támogatja. Tekintse meg az adott alkalmazás dokumentációját a támogatási nyilatkozathoz.

**K: Mi a különbség az [Azure ad JOIN](../active-directory-azureadjoin-overview.md) és a zökkenőmentes egyszeri bejelentkezés szolgáltatás által biztosított egyszeri bejelentkezési élmény között?**

Az [Azure ad JOIN](../active-directory-azureadjoin-overview.md) egyszeri bejelentkezést biztosít a felhasználók számára, ha az eszközük regisztrálva van az Azure ad-ben. Ezeket az eszközöket nem feltétlenül kell tartományhoz csatlakoztatni. Az egyszeri bejelentkezés az *elsődleges frissítési tokenekkel* vagy a *PRT*-vel van megadva, és nem Kerberos. A Windows 10-es eszközökön a felhasználói élmény a legoptimálisabb. Az egyszeri bejelentkezés automatikusan megtörténik a Microsoft Edge böngészőben. Emellett a Chrome-ban is működik egy böngésző-bővítmény használatával.

Az Azure AD Joint és a zökkenőmentes egyszeri bejelentkezést is használhatja a bérlőn. Ez a két funkció kiegészíti egymást. Ha mindkét funkció be van kapcsolva, az Azure AD JOIN szolgáltatásból származó egyszeri bejelentkezés elsőbbséget élvez a zökkenőmentes egyszeri bejelentkezéssel szemben.

**K: Nem Windows 10 rendszerű eszközöket szeretnék regisztrálni az Azure AD-vel, AD FS használata nélkül. Használhatom a zökkenőmentes egyszeri bejelentkezést?**

Igen, ehhez a forgatókönyvhöz a [munkahelyi csatlakoztatási ügyfél 2,1-](https://www.microsoft.com/download/details.aspx?id=53554)es vagy újabb verziójára van szükség.

**K: Hogyan lehet átvenni a `AZUREADSSOACC` számítógépfiók Kerberos-visszafejtési kulcsát?**

Fontos a helyszíni ad-erdőben létrehozott `AZUREADSSOACC` számítógépfiók (amely az Azure ad-t jelképező) Kerberos-visszafejtési kulcsának gyakori átadása.

>[!IMPORTANT]
>Javasoljuk, hogy legalább 30 naponként átadja a Kerberos-visszafejtési kulcsot.

Kövesse az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen a Azure AD Connect futtatja:

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. Először töltse le és telepítse az [Azure ad PowerShellt](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
    3. Importálja a zökkenőmentes SSO PowerShell-modult a `Import-Module .\AzureADSSO.psd1`következő parancs használatával:.
    4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja `New-AzureADSSOAuthenticationContext`meg a t. Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
    5. Hívás `Get-AzureADSSOStatus | ConvertFrom-Json`. Ez a parancs az AD-erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

    **2. lépés. Frissítse a Kerberos-visszafejtési kulcsot minden olyan AD-erdőben, amelyen be lett állítva**

    1. Hívás `$creds = Get-Credential`. Ha a rendszer kéri, adja meg a kívánt Active Directory-erdő tartományi rendszergazdai hitelesítő adatait.

    > [!NOTE]
    > A tartományi rendszergazda felhasználónevet használjuk, amelyet az egyszerű felhasználónév (UPN)johndoe@contoso.comformátum vagy a tartomány minősített Sam-fiók neve (contoso\johndoe vagy contoso. com\johndoe) formátumban biztosítanak a kívánt ad-erdő megtalálásához. Ha tartományi minősített Sam-fióknevet használ, a Felhasználónév tartomány részével [Keresse meg a tartományi rendszergazda](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx)tartományvezérlőjét a DNS használatával. Ha ehelyett az UPN-t használja, a megfelelő tartományvezérlő megkeresése előtt [lefordítjuk egy tartományi minősített Sam-fiók nevére](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) .

    2. Hívás `Update-AzureADSSOForest -OnPremCredentials $creds`. Ez a parancs frissíti az adott ad-erdőben lévő `AZUREADSSOACC` számítógépfiók Kerberos-visszafejtési kulcsát, és frissíti azt az Azure ad-ben.
    3. Ismételje meg a fenti lépéseket minden olyan AD-erdőnél, amelyre beállította a szolgáltatást.

    >[!IMPORTANT]
    >Győződjön meg arról , hogy nem `Update-AzureADSSOForest` futtatja többször a parancsot. Ellenkező esetben a funkció addig leáll, amíg a felhasználó Kerberos-jegye lejár, és a helyszíni Active Directory újra kiadja.

**K: Hogyan lehet letiltani a zökkenőmentes egyszeri bejelentkezést?**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. Először töltse le és telepítse az [Azure ad PowerShellt](https://docs.microsoft.com/powershell/azure/active-directory/overview).
    2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
    3. Importálja a zökkenőmentes SSO PowerShell-modult a `Import-Module .\AzureADSSO.psd1`következő parancs használatával:.
    4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja `New-AzureADSSOAuthenticationContext`meg a t. Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
    5. Hívás `Get-AzureADSSOStatus | ConvertFrom-Json`. Ez a parancs az AD-erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

    **3. lépés. Törölje kézzel a `AZUREADSSOACCT` számítógépfiókot a felsorolt ad-erdőkből.**

## <a name="next-steps"></a>További lépések

- [**Gyorskonfigurálás**](how-to-connect-sso-quick-start.md) – az Azure ad zökkenőmentes egyszeri bejelentkezésének megkezdése és futtatása.
- [**Technikai**](how-to-connect-sso-how-it-works.md) részletes útmutató – a funkció működésének megismerése.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új szolgáltatásokra vonatkozó kérelmek bejelentése.
