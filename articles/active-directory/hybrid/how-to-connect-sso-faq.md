---
title: 'Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés – gyakori kérdések | Microsoft dokumentumok'
description: Válaszok az Azure Active Directory zökkenőmentes egyszeri bejelentkezéssel kapcsolatos gyakori kérdésekre.
services: active-directory
keywords: mi az Azure AD Connect, telepítse az Active Directoryt, szükséges összetevőket az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7241c8dfbedb24f95c29ea9e1c3f763218a5668d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025677"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory zökkenőmentes egyszeri bejelentkezés: gyakori kérdések

Ebben a cikkben az Azure Active Directory zökkenőmentes egyszeri bejelentkezéssel (zökkenőmentes egyszeri bejelentkezés) kapcsolatos gyakori kérdéseket tesszük fel. Nézz vissza az új tartalomra.

**K: Milyen bejelentkezési módszerek zökkenőmentes egyszeri bejelentkezési munka**

A zökkenőmentes egyszeri bejelentkezés kombinálható a [jelszókivonat-szinkronizálással](how-to-connect-password-hash-synchronization.md) vagy az [átmenő hitelesítés bejelentkezési](how-to-connect-pta.md) módszerekkel. Ez a szolgáltatás azonban nem használható az Active Directory összevonási szolgáltatásokkal (ADFS).

**K: A seamless sso ingyenes funkció?**

A zökkenőmentes egyszeri bejelentkezés egy ingyenes funkció, és nem kell az Azure AD fizetős kiadásainak használatához.

**K: Elérhető a zökkenőmentes egyszeri bejelentkezés a [Microsoft Azure Germany felhőben](https://www.microsoft.de/cloud-deutschland) és a [Microsoft Azure Government felhőben?](https://azure.microsoft.com/features/gov/)**

Nem. A zökkenőmentes egyszeri bejelentkezés csak az Azure AD világméretű példányában érhető el.

**K: Milyen alkalmazások `domain_hint` használják `login_hint` ki a zökkenőmentes egyszeri bejelentkezés előnyeit vagy paraméteres képességeit?**

Az alábbiakban felsoroljuk azokat az alkalmazásokat, amelyek ezeket a paramétereket elküldhetik az Azure AD-nek, és ezért csendes bejelentkezési élményt nyújt a felhasználóknak a zökkenőmentes egyszeri bejelentkezés használatával (azaz nincs szükség arra, hogy a felhasználók beírják a felhasználónevüket vagy jelszavukat):

| Alkalmazásnév | A használandó alkalmazás URL-címe |
| -- | -- |
| Hozzáférési panel | https:\//myapps.microsoft.com/contoso.com |
| Webes Outlook | https:\//outlook.office365.com/contoso.com |
| Office 365-portálok | https:\//portal.office.com?domain_hint=contoso.com,\/https: /www.office.com?domain_hint=contoso.com |

Emellett a felhasználók egy csendes bejelentkezési élményt kapnak, ha egy alkalmazás bejelentkezési kérelmeket küld az Azure AD bérlőként beállított végpontjainak – azaz https:\//login.microsoftonline.com/contoso.com/<..> vagy https:\//login.microsoftonline.com/<tenant_ID>/<. > – az Azure AD közös végpontja helyett\/–, azaz https: /login.microsoftonline.com/common/<...>. Az alábbiakban felsorolunk egy nem teljes listát az alkalmazások, amelyek az ilyen típusú bejelentkezési kérelmek.

| Alkalmazásnév | A használandó alkalmazás URL-címe |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Azure portál | https:\//portal.azure.com/contoso.com |

A fenti táblázatokban cserélje le a "contoso.com" a tartománynevét, hogy a megfelelő alkalmazás URL-eket a bérlő.

Ha azt szeretné, hogy más alkalmazások is használják a csendes bejelentkezési élményt, tudassa velünk a visszajelzési szakaszban.

**K: A zökkenőmentes `Alternate ID` egyszeri bejelentkezés támogatja a `userPrincipalName`felhasználónevet, nem pedig?**

Igen. A zökkenőmentes egyszeri `Alternate ID` bejelentkezés felhasználónévként támogatott, ha az Azure AD Connect ben konfigurálva van, ahogy [az itt](how-to-connect-install-custom.md)látható. Nem minden Office 365-alkalmazás támogatja. `Alternate ID` A támogatási nyilatkozatot az adott alkalmazás dokumentációjában találja.

**K: Mi a különbség az [Azure AD Join](../active-directory-azureadjoin-overview.md) és a Seamless SSO által biztosított egyszeri bejelentkezési élmény között?**

[Az Azure AD Join](../active-directory-azureadjoin-overview.md) sso-t biztosít a felhasználóknak, ha eszközeik regisztrálva vannak az Azure AD-ben. Ezek az eszközök nem feltétlenül kell tartományhoz csatlakozni. Egyszeri biztonsági rendszer *elsődleges frissítési jogkivonatok* vagy *PRT-k*használatával érhető el, és nem a Kerberos. A felhasználói élmény a legoptimálisabb a Windows 10-es eszközökön. Az sso automatikusan megtörténik a Microsoft Edge böngészőben. Ez is működik a Chrome használatával a böngésző kiterjesztés.

Használhatja az Azure AD-s és a zökkenőmentes egyszeri bejelentkezés t a bérlőn. Ez a két jellemző kiegészíti egymást. Ha mindkét funkció be van kapcsolva, majd az Azure AD Join egyszeri bejelentkezés e-alapú sso elsőbbséget élvez a zökkenőmentes egyszeri bejelentkezéssel szemben.

**K: Nem Windows 10-es eszközöket szeretnék regisztrálni az Azure AD-vel, az AD FS használata nélkül. Használhatom helyette a zökkenőmentes egyszeri bejelentkezést?**

Igen, ebben a forgatókönyvben a munkahelyi csatlakozás ügyfél 2.1-es vagy újabb [verziójára](https://www.microsoft.com/download/details.aspx?id=53554)van szükség.

**K: Hogyan tudom átfordítani a `AZUREADSSOACC` számítógépfiók Kerberos-visszafejtési kulcsát?**

Fontos, hogy gyakran gördülje át a helyszíni `AZUREADSSOACC` AD-erdőben létrehozott számítógépfiók Kerberos-visszafejtési kulcsát (amely az Azure AD-t jelöli).

>[!IMPORTANT]
>Javasoljuk, hogy legalább 30 naponta döntse át a Kerberos-visszafejtési kulcsot.

Kövesse az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen az Azure AD Connect fut:

   **1. lépés. Azon AD-erdők listájának beszereznie, ahol a zökkenőmentes egyszeri bejelentkezés engedélyezve van**

   1. Először töltse le és telepítse az [Azure AD PowerShellt.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
   2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
   3. Importálja a zökkenőmentes SSO PowerShell `Import-Module .\AzureADSSO.psd1`modult a következő paranccsal: .
   4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben `New-AzureADSSOAuthenticationContext`hívja a . Ezzel a paranccsal előugró ablakban adhatja meg a bérlő globális rendszergazdai hitelesítő adatait.
   5. Hívja `Get-AzureADSSOStatus | ConvertFrom-Json`a hívást. Ez a parancs azoknak az AD-erdőknek a listáját tartalmazza (nézd meg a "Tartományok" listát), amelyen ez a szolgáltatás engedélyezve van.

   **2. lépés. A Kerberos-visszafejtési kulcs frissítése minden olyan AD-erdőben, amelyen beállította**

   1. Hívja `$creds = Get-Credential`a hívást. Amikor a rendszer kéri, adja meg a kívánt AD-erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   >A tartományi rendszergazda hitelesítő adatainak felhasználónevét a SAM-fiók névformátumában kell megadni (contoso\johndoe vagy contoso.com\johndoe). A felhasználónév tartományrészét használjuk a tartományvezérlő DNS-t használó tartományvezérlőjének megkeresésére.

   >[!NOTE]
   >A használt tartományi rendszergazdai fiók nem lehet a Védett felhasználók csoport tagja. Ha igen, a művelet sikertelen lesz.

   2. Hívja `Update-AzureADSSOForest -OnPremCredentials $creds`a hívást. Ez a parancs frissíti a Kerberos `AZUREADSSOACC` visszafejtési kulcsot a számítógépfiók ebben az AD erdőben, és frissíti azt az Azure AD-ben.
   3. Ismételje meg az előző lépéseket minden olyan AD-erdőnél, amelyen beállította a funkciót.

   >[!IMPORTANT]
   >Győződjön _don't_ meg arról, `Update-AzureADSSOForest` hogy a parancsot nem futtatja egynél többször. Ellenkező esetben a szolgáltatás leáll, amíg a felhasználók Kerberos-jegyei le nem járnak, és a helyszíni Active Directory újra ki nem adja.

**K: Hogyan tudom letiltani a zökkenőmentes egyszeri bejelentkezést?**

   **1. lépés. A szolgáltatás letiltása a bérlőn**

   **A. lehetőség: Az Azure AD Connect használatának letiltása**
    
   1. Futtassa az Azure AD Connect et, válassza **a Felhasználó bejelentkezési lapjának módosítása** lehetőséget, és kattintson a **Tovább**gombra.
   2. Törölje a jelet az **Egyszeri bejelentkezés engedélyezése** jelölőnégyzetből. Folytassa a varázslóval.

   A varázsló befejezése után a zökkenőmentes egyszeri bejelentkezés le lesz tiltva a bérlőn. A képernyőn azonban megjelenik egy üzenet, amely a következőképpen szól:

   "Az egyszeri bejelentkezés le van tiltva, de további manuális lépéseket kell végrehajtani a tisztítás befejezéséhez. Tudjon meg többet"

   A tisztítási folyamat befejezéséhez kövesse a 2.

   **B. lehetőség: Letiltás a PowerShell használatával**

   Futtassa az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen az Azure AD Connect fut:

   1. Először töltse le és telepítse az [Azure AD PowerShellt.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
   2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
   3. Importálja a zökkenőmentes SSO PowerShell `Import-Module .\AzureADSSO.psd1`modult a következő paranccsal: .
   4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben `New-AzureADSSOAuthenticationContext`hívja a . Ezzel a paranccsal előugró ablakban adhatja meg a bérlő globális rendszergazdai hitelesítő adatait.
   5. Hívja `Enable-AzureADSSO -Enable $false`a hívást.

   >[!IMPORTANT]
   >A zökkenőmentes egyszeri bejelentkezés letiltása a PowerShell használatával nem változtatja meg az Azure AD Connect állapotát. A zökkenőmentes egyszeri bejelentkezés a **Felhasználó bejelentkezésének módosítása** lapon engedélyezettként jelenik meg.

   **2. lépés. Azon AD-erdők listájának beszereznie, ahol a zökkenőmentes egyszeri bejelentkezés engedélyezve van**

   Kövesse az alábbi 1–4. Ha ehelyett letiltotta a Zökkenőmentes egyszeri bejelentkezést a PowerShell használatával, ugorjon előre az alábbi 5-ös feladatra.

   1. Először töltse le és telepítse az [Azure AD PowerShellt.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
   2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
   3. Importálja a zökkenőmentes SSO PowerShell `Import-Module .\AzureADSSO.psd1`modult a következő paranccsal: .
   4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben `New-AzureADSSOAuthenticationContext`hívja a . Ezzel a paranccsal előugró ablakban adhatja meg a bérlő globális rendszergazdai hitelesítő adatait.
   5. Hívja `Get-AzureADSSOStatus | ConvertFrom-Json`a hívást. Ez a parancs azoknak az AD-erdőknek a listáját tartalmazza (nézd meg a "Tartományok" listát), amelyen ez a szolgáltatás engedélyezve van.

   **3. lépés. Törölje manuálisan a `AZUREADSSOACCT` számítógépfiókot minden felsorolt AD-erdőből.**

## <a name="next-steps"></a>További lépések

- [**Rövid útmutató**](how-to-connect-sso-quick-start.md) – Az Azure AD seamless sso üzembe kerülése.
- [**Műszaki Deep Dive**](how-to-connect-sso-how-it-works.md) - Ismerje meg, hogyan működik ez a funkció.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – Ismerje meg, hogyan oldhatja meg a szolgáltatással kapcsolatos gyakori problémákat.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - A bejelentés új funkció kérelmeket.
