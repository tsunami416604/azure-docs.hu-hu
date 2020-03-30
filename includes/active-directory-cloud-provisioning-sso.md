---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504275"
---
## <a name="steps-to-enable-single-sign-on"></a>Az egyszeri bejelentkezés engedélyezésének lépései
A felhőkiépítés az egyszeri bejelentkezéssel működik.  Jelenleg nincs lehetőség az SSO engedélyezésére, ha az ügynök telepítve van, azonban az alábbi lépésekkel engedélyezheti és használhatja az SSO-t. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>1. lépés: Azure AD Connect-fájlok letöltése és kibontása
1.  Először töltse le az [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) legújabb verzióját
2.  Nyisson meg egy parancssort rendszergazdai jogosultságokkal, és keresse meg az imént letöltött msi-t.
3.  Futtassa a következőket:`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Módosítsa a fájlelérési utat és a extractfolder mappát a fájl elérési útjának és az extrakciós mappa nevének megfelelően.  A tartalomnak most már a kivonási mappában kell lennie.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>2. lépés: A zökkenőmentes SSO PowerShell modul importálása

1. Töltse le és telepítse az [Azure AD PowerShellt.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
2. Tallózással keresse meg a `%programfiles%\Microsoft Azure Active Directory Connect` mappát.
3. Importálja a zökkenőmentes SSO PowerShell `Import-Module .\AzureADSSO.psd1`modult a következő paranccsal: .

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>3. lépés: Azon Active Directory-erdők listájának beszereznie, amelyeken a zökkenőmentes egyszeri bejelentkezés engedélyezve van

1. Futtassa a PowerShellt rendszergazdaként. A PowerShellben `New-AzureADSSOAuthenticationContext`hívja a . Amikor a rendszer kéri, adja meg a bérlő globális rendszergazdai hitelesítő adatait.
2. Hívja `Get-AzureADSSOStatus`a hívást. Ez a parancs tartalmazza az Active Directory-erdők listáját (nézd meg a "Tartományok" listát), amelyen ez a szolgáltatás engedélyezve van.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4. lépés: Zökkenőmentes egyszeri bejelentkezés engedélyezése minden Active Directory-erdőhöz

1. Hívja `Enable-AzureADSSOForest`a hívást. Amikor a rendszer kéri, adja meg a kívánt Active Directory-erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   >A tartományi rendszergazda hitelesítő adatainak felhasználónevét a SAM-fiók névformátumában kell megadni (contoso\johndoe vagy contoso.com\johndoe). A felhasználónév tartományrészét használjuk a tartományvezérlő DNS-t használó tartományvezérlőjének megkeresésére.

   >[!NOTE]
   >A használt tartományi rendszergazdai fiók nem lehet a Védett felhasználók csoport tagja. Ha igen, a művelet sikertelen lesz.

2. Ismételje meg az előző lépést minden olyan Active Directory-erdőesetében, ahol be szeretné állítani a szolgáltatást.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5. lépés A szolgáltatás engedélyezése a bérlőn

A szolgáltatás bérlőn való bekapcsolásához hívja a hívást. `Enable-AzureADSSO -Enable $true`
