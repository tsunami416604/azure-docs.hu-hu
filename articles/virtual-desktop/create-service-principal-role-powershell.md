---
title: Windows rendszerű virtuális asztali előzetes verziójú egyszerű szolgáltatások és szerepkör-hozzárendelések létrehozása a PowerShell használatával – Azure
description: Egyszerű szolgáltatások létrehozása és szerepkörök kiosztása a PowerShell használatával a Windows virtuális asztal előzetes verziójában.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 3e9ee3f5dd04ef838f78b9731885b7ea48e6c99d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811323"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Oktatóanyag: Szolgáltatásnevek és szerepkör-hozzárendelések létrehozása a PowerShell-lel

Az egyszerű szolgáltatások olyan identitások, amelyeket a Azure Active Directory létrehozhat a szerepkörök és engedélyek adott célra való hozzárendeléséhez. A Windows rendszerű virtuális asztali verzió előzetes verziójában létrehozhat egy egyszerű szolgáltatásnevet a következőhöz:

- Adott Windowsos virtuális asztali felügyeleti feladatok automatizálása.
- Használjon hitelesítő adatokat az MFA-kötelező felhasználók helyett, amikor a Windows rendszerű virtuális asztali számítógépeken bármilyen Azure Resource Manager sablont futtat.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Egyszerű szolgáltatásnév létrehozása Azure Active Directoryban.
> * Szerepkör-hozzárendelés létrehozása a Windows rendszerű virtuális asztalon.
> * Jelentkezzen be a Windows rendszerű virtuális asztalra az egyszerű szolgáltatásnév használatával.

## <a name="prerequisites"></a>Előfeltételek

Az egyszerű szolgáltatások és a szerepkör-hozzárendelések létrehozása előtt három dolgot kell tennie:

1. Telepítse a AzureAD modult. A modul telepítéséhez futtassa a PowerShellt rendszergazdaként, és futtassa a következő parancsmagot:

    ```powershell
    Install-Module AzureAD
    ```

2. [A Windows rendszerű virtuális asztali PowerShell-modul letöltése és importálása](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)

3. Kövesse a cikkben szereplő összes útmutatást ugyanabban a PowerShell-munkamenetben. Előfordulhat, hogy nem működik, ha bezárta az ablakot, és később visszatér rá.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Egyszerű szolgáltatás létrehozása az Azure Active Directory-ban

Miután teljesítette az előfeltételeket a PowerShell-munkamenetben, futtassa a következő PowerShell-parancsmagokat egy több-bérlős szolgáltatásnév létrehozásához az Azure-ban.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="view-your-credentials-in-powershell"></a>Hitelesítő adatok megtekintése a PowerShellben

A PowerShell-munkamenet befejezése előtt tekintse meg a hitelesítő adatait, és jegyezze fel őket későbbi használatra. A jelszó különösen azért fontos, mert a PowerShell-munkamenet lezárása után nem fogja tudni lekérni azt.

A következő három hitelesítő adatot kell leírnia, és a futtatni kívánt parancsmagokat kell lekérnie:

- Jelszó

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Bérlő azonosítója:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Alkalmazás azonosítója:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Szerepkör-hozzárendelés létrehozása a Windows rendszerű virtuális asztali verzióban – előzetes verzió

Ezután létre fog hozni egy RDS szerepkör-hozzárendelést a Windows rendszerű virtuális asztalon a szolgáltatásnév számára, amely lehetővé teszi, hogy az egyszerű szolgáltatás bejelentkezzen a Windows rendszerű virtuális asztalra. Ügyeljen arra, hogy olyan fiókot használjon, amely rendelkezik az RDS szerepkör-hozzárendelések létrehozásához szükséges engedélyekkel.

Futtassa a következő PowerShell-parancsmagokat a Windows rendszerű virtuális asztalhoz való kapcsolódáshoz és az RDS-bérlők megjelenítéséhez.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant | FL
```

Használja a megfelelő bérlő TenantName, és futtassa a következő PowerShell-parancsmagokat az egyszerű szolgáltatáshoz tartozó szerepkör-hozzárendelés létrehozásához a megadott bérlőn.

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName "<my-rds-tenantname>"
```

## <a name="sign-in-with-the-service-principal"></a>Bejelentkezés az egyszerű szolgáltatással

Miután létrehozta az egyszerű szolgáltatáshoz tartozó szerepkör-hozzárendelést, győződjön meg arról, hogy a szolgáltatásnév a következő parancsmag futtatásával tud bejelentkezni a Windows rendszerű virtuális asztalra:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Miután bejelentkezett, győződjön meg róla, hogy minden működik, ha tesztel néhány Windowsos virtuális asztali PowerShell-parancsmagot az egyszerű szolgáltatással.

## <a name="next-steps"></a>További lépések

Miután létrehozta a szolgáltatásnevet, és hozzárendelte azt egy szerepkörhöz a Windows rendszerű virtuális asztali bérlőben, a használatával létrehozhat egy gazdagépet. Ha többet szeretne megtudni a gazdagép-készletekről, folytassa a gazdagépek Windows rendszerű virtuális asztali gépen való létrehozásának oktatóanyagával.

 > [!div class="nextstepaction"]
 > [A Windows rendszerű virtuális asztali gazdagép készletének oktatóanyaga](./create-host-pools-azure-marketplace.md)
