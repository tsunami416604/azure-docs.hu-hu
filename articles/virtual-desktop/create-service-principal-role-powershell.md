---
title: Windows virtuális asztal előzetes egyszerű szolgáltatásnevekről és a szerepkör-hozzárendelések létrehozása a PowerShell használatával – Azure
description: Hogyan lehet az egyszerű szolgáltatások létrehozása és hozzárendelése a szerepkörök a PowerShell használatával a Windows Virtual Desktop előzetes verziójához.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 1bbe89484d72a21c4432d452d4ddae83ea2d2553
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400031"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell"></a>Oktatóanyag: Szolgáltatásnevek és szerepkör-hozzárendelések létrehozása a PowerShell használatával

Szolgáltatásnevek használata az identitások szerepköröket és engedélyeket rendelhet a meghatározott célra az Azure Active Directoryban is létrehozhat. Windows virtuális asztal előzetes verzióban elérhető szolgáltatás hozhat létre, az egyszerű szolgáltatásnév:

- Adott virtuális asztali Windows-felügyeleti feladatok automatizálása
- Bármely Windows virtuális asztal Azure Resource Manager-sablon futtatásakor helyett MFA szükséges felhasználói hitelesítő adatokat használja

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Egyszerű szolgáltatás létrehozása az Azure Active Directory-ban
> * Szerepkör-hozzárendelés létrehozása a virtuális asztali Windows
> * Jelentkezzen be Windows virtuális asztal egyszerű szolgáltatással

## <a name="prerequisites"></a>Előfeltételek

Szolgáltatásnevek és szerepkör-hozzárendeléseket hozhat létre, mielőtt szüksége három dolgot tennie:

1. Az Azure ad-modul telepítése. A modul telepítéséhez futtassa a Powershellt rendszergazdaként, és futtassa a következő parancsmagot:

    ```powershell
    Install-Module AzureAD
    ```

2. Futtassa a következő parancsmagokat váltotta fel a munkamenethez kapcsolódó értékeket idézőjelek között szereplő értékeket.

    ```powershell
    $myTenantGroupName = "<my-tenant-group-name>"
    $myTenantName = "<my-tenant-name>"
    ```

3. Ebben a cikkben minden utasításait követve ugyanebben a PowerShell-munkamenetben. Ez nem működik, ha bezárja az ablakot, és később még visszatérünk rá.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Egyszerű szolgáltatás létrehozása az Azure Active Directory-ban

Egyszer, hogy teljesülnek az Előfeltételek a PowerShell-munkamenetben futtassa a következő PowerShell-parancsmagok az Azure-beli szolgáltatásnév létrehozása több-bérlős szolgáltatás.

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
Set-RdsContext -TenantGroupName $myTenantGroupName
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantGroupName $myTenantGroupName -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Jelentkezzen be a szolgáltatásnévvel

Miután létrehozta a szerepkör-hozzárendelés, a szolgáltatás egyszerű, most ellenőrizzük, hogy a szolgáltatásnév jelentkezhetnek be Windows virtuális asztal a következő parancsmag futtatásával:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Miután bejelentkezett, ellenőrizze, hogy minden működik, az egyszerű szolgáltatás néhány Windows virtuális asztal PowerShell-parancsmagok tesztelését.

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

- Alkalmazás azonosítója:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan egyszerű szolgáltatás létrehozása és bejelentkezés Windows virtuális asztal vele. Bejelentkezés Windows virtuális asztal kapcsolatos további információkért folytassa a csatlakozás a Windows virtuális asztal útmutatók.

- [Csatlakozás a távoli asztali ügyfél Windows 7 és Windows 10 rendszeren](connect-windows-7-and-10.md)
- [Csatlakozás a Windows virtuális asztal előzetes webes ügyféllel](connect-web.md)
