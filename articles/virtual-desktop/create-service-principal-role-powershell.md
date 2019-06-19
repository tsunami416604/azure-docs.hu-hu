---
title: Windows virtuális asztal előzetes egyszerű szolgáltatásnevekről és a szerepkör-hozzárendelés létrehozása PowerShell – Azure használatával
description: Útmutató egyszerű szolgáltatások létrehozása és szerepkörök hozzárendelése a PowerShell használatával Windows virtuális asztal előzetes verzióban érhető el.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 44c823653ecbad1c4dd1fd35b676c8a6d8bd1620
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206656"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Oktatóanyag: Egyszerű szolgáltatásnevekről és a szerepkör-hozzárendelés létrehozása PowerShell használatával

Szolgáltatásnevek használata az identitások, amelyek a szerepköröket és engedélyeket rendelhet a meghatározott célra az Azure Active Directoryban is létrehozhat. Windows virtuális asztal előzetes verzióban elérhető szolgáltatás hozhat létre, az egyszerű szolgáltatásnév:

- Adott virtuális asztali Windows-felügyeleti feladatok automatizálása.
- Helyett MFA szükséges felhasználói hitelesítő adatokat használja a Windows virtuális asztal minden olyan Azure Resource Manager-sablon futtatásakor.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Egyszerű szolgáltatás létrehozása az Azure Active Directoryban.
> * Szerepkör-hozzárendelés létrehozása a Windows virtuális asztalához.
> * Jelentkezzen be a Windows virtuális asztal egyszerű szolgáltatás használatával.

## <a name="prerequisites"></a>Előfeltételek

Szolgáltatásnevek és szerepkör-hozzárendeléseket hozhat létre, mielőtt kell tennie a három dolgot:

1. Az Azure ad-modul telepítése. A modul telepítéséhez futtassa a Powershellt rendszergazdaként, és futtassa a következő parancsmagot:

    ```powershell
    Install-Module AzureAD
    ```

2. Futtassa a következő parancsmagokat váltotta fel a munkamenethez kapcsolódó értékeket idézőjelek között szereplő értékeket.

    ```powershell
    $myTenantName = "<my-tenant-name>"
    ```

3. Ebben a cikkben minden utasításait követve ugyanebben a PowerShell-munkamenetben. Ez nem működik, ha bezárja az ablakot, és később még visszatérünk rá.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Egyszerű szolgáltatás létrehozása az Azure Active Directory-ban

Után a PowerShell-munkamenetben az előfeltételeket már teljesülnek, futtassa a következő PowerShell-parancsmagok egy több-bérlős szolgáltatásnév létrehozásához az Azure-ban.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Szerepkör-hozzárendelés létrehozása a Windows virtuális asztal előzetes verzióban érhető el

Most, hogy létrehozott egy szolgáltatás egyszerű, használhatja a Windows virtuális asztal bejelentkezni. Ellenőrizze, hogy jelentkezzen be egy olyan fiókkal, amely rendelkezik engedélyekkel a szerepkör-hozzárendelés létrehozásához.

Először [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

Futtassa a következő PowerShell-parancsmagok a Windows virtuális asztalhoz csatlakozik, és egyszerű szolgáltatás szerepkör-hozzárendelés létrehozása.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Jelentkezzen be a szolgáltatásnévvel

Miután létrehozott egy szerepkör-hozzárendelés a szolgáltatás egyszerű, győződjön meg arról, hogy a szolgáltatásnév jelentkezhetnek be Windows virtuális asztal a következő parancsmagot:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Miután bejelentkezett, ellenőrizze, hogy minden működik, az egyszerű szolgáltatás néhány Windows virtuális asztal PowerShell-parancsmagok tesztelésével.

## <a name="view-your-credentials-in-powershell"></a>A hitelesítő adatok megtekintése a PowerShellben

Mielőtt leállítása a PowerShell-munkamenetet, megtekintheti a hitelesítő adatait, és írja le a későbbiekben. A jelszó különösen fontos, mert nem tudja beolvasni a PowerShell-munkamenet bezárása után.

Az alábbiakban a három hitelesítő adatokat, le kell írni és azok futtatnia kell a parancsmagokat:

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

## <a name="next-steps"></a>További lépések

Miután az egyszerű szolgáltatás létrehozása és hozzárendelte egy szerepkört a Windows virtuális asztal bérlő, a gazdagép-készlet létrehozása használhatja. Gazdagép-készletek kapcsolatos további információkért folytassa az oktatóanyag során egy gazdagép-készlet létrehozása a Windows virtuális asztal.

 > [!div class="nextstepaction"]
 > [Windows virtuális asztali állomás készlet oktatóanyag](./create-host-pools-azure-marketplace.md)
