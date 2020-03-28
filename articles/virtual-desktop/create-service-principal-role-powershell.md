---
title: Windows Virtual Desktop szolgáltatás egyszerű szerepkör-hozzárendelése – Azure
description: Szolgáltatásnévi tagok létrehozása és szerepkörök hozzárendelése a PowerShell használatával a Windows Virtual Desktop rendszerben használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61b5017609d99f2f0074c67d3838cf351ea38bea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365423"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Oktatóanyag: Szolgáltatástagok és szerepkör-hozzárendelések létrehozása a PowerShell használatával

A szolgáltatásnévi tagok olyan identitások, amelyeket létrehozhat az Azure Active Directoryban szerepkörök és engedélyek hozzárendeléséhez egy adott célra. A Windows Virtuális asztal rendszerben egyszerű szolgáltatáslétrehozása a következőkre:

- Automatizálhatja a Windows virtuális asztal kezelési feladatait.
- Az MFA-ra szükséges felhasználók helyett hitelesítő adatként használható bármely Azure Resource Manager-sablon Windows virtuális asztalhoz való futtatásakor.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy egyszerű szolgáltatás az Azure Active Directoryban.
> * Szerepkör-hozzárendelés létrehozása a Windows virtuális asztalon.
> * Jelentkezzen be a Windows virtuális asztalra az egyszerű szolgáltatás használatával.

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatásnév- és szerepkör-hozzárendelések létrehozása előtt három dolgot kell tennie:

1. Telepítse az AzureAD-modult. A modul telepítéséhez futtassa a PowerShellt rendszergazdaként, és futtassa a következő parancsmast:

    ```powershell
    Install-Module AzureAD
    ```

2. [Töltse le és importálja a Windows Virtual Desktop PowerShell modult.](/powershell/windows-virtual-desktop/overview/)

3. Kövesse a jelen cikkben szereplő összes utasítást ugyanabban a PowerShell-munkamenetben. Előfordulhat, hogy a folyamat nem működik, ha megszakítja a PowerShell-munkamenetet az ablak bezárásával és később újra megnyitja.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Egyszerű szolgáltatás létrehozása az Azure Active Directory-ban

Miután teljesítette az előfeltételeket a PowerShell-munkamenetben, futtassa a következő PowerShell-parancsmagokat egy több-bérlős egyszerű szolgáltatás létrehozásához az Azure-ban.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>A hitelesítő adatok megtekintése a PowerShellben

Mielőtt létrehozna egy szerepkör-hozzárendelést az egyszerű szolgáltatáshoz, tekintse meg a hitelesítő adatait, és írja le őket későbbi használatra. A jelszó különösen fontos, mert a PowerShell-munkamenet bezárása után nem fogja tudni letölteni.

Itt van a három hitelesítő adatokat kell leírni, és a parancsmagok meg kell futtatni, hogy őket:

- Jelszó:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Bérlő azonosítója:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Alkalmazásazonosító:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Szerepkör-hozzárendelés létrehozása a Windows virtuális asztal előzetes verziójában

Ezután létre kell hoznia egy szerepkör-hozzárendelést, hogy az egyszerű szolgáltatás bejelentkezhessen a Windows virtuális asztalra. Ügyeljen arra, hogy olyan fiókkal jelentkezzen be, amely rendelkezik szerepkör-hozzárendelések létrehozásához szükséges engedélyekkel.

Először [töltse le és importálja a Windows Virtual Desktop PowerShell modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használatra, ha még nem tette meg.

Futtassa a következő PowerShell-parancsmagokat a Windows virtuális asztalhoz való csatlakozáshoz és a bérlők megjelenítéséhez.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Ha megtalálja annak a bérlőnek a bérlő nevét, amelyhez szerepkör-hozzárendelést szeretne létrehozni, használja ezt a nevet a következő parancsmagban:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Bejelentkezés az egyszerű szolgáltatással

Miután létrehozott egy szerepkör-hozzárendelést az egyszerű szolgáltatáshoz, győződjön meg arról, hogy a szolgáltatásnév a következő parancsmag futtatásával tud bejelentkezni a Windows virtuális asztalra:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Miután bejelentkezett, győződjön meg arról, hogy minden működik néhány Windows Virtual Desktop PowerShell-parancsmag tesztelésével az egyszerű szolgáltatással.

## <a name="next-steps"></a>További lépések

Miután létrehozta az egyszerű szolgáltatást, és szerepkört rendelt hozzá a Windows virtuális asztal bérlőjéhez, gazdagépkészlet létrehozásához használhatja. Ha többet szeretne megtudni a gazdakészletekről, folytassa a gazdakészlet létrehozásáról a Windows virtuális asztalon című oktatóanyaggal.

 > [!div class="nextstepaction"]
 > [Gazdagépcsoport létrehozása az Azure Marketplace-en](./create-host-pools-azure-marketplace.md)
