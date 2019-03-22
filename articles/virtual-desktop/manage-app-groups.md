---
title: Alkalmazáscsoportok kezelése a Windows virtuális asztal (előzetes verzió) – Azure
description: Ismerteti, hogyan állíthatja be Windows virtuális asztal bérlők számára az Azure Active Directoryban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: d0b5dede5846b3ff9eac3ee865685fe5b3f773f9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318105"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Oktatóanyag: Alkalmazáscsoportok kezelése a Windows virtuális asztal (előzetes verzió)

Létrehozott egy új gazdagép-készlet alapértelmezett alkalmazáscsoportnak is közzéteszi a teljes asztalhoz. Emellett a gazdagép-készlet egy vagy több (előzetes verzió) RemoteApp alkalmazáscsoportok is létrehozhat. Ez az oktatóanyag egy RemoteApp-app csoport létrehozása és közzététele a Start menü egyéni alkalmazások.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * Távoli alkalmazások hozzáférést biztosít.

Mielőtt elkezdené, [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a Windows virtuális asztal modul használatához a PowerShell-munkamenetben, ha még nem tette.

## <a name="create-a-remoteapp-group"></a>A RemoteApp-csoport létrehozása

1. Futtassa a következő PowerShell-parancsmag egy új üres RemoteApp-csoport létrehozása.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Nem kötelező) Az alkalmazás csoport létrejöttének ellenőrzéséhez futtathatja a következő parancsmagot a gazdagép készlet minden alkalmazáscsoport listájának megtekintéséhez.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Virtuálisgép-lemezkép a gazdagép-készlet kezdő listáját menü alkalmazások beolvasása a következő parancsmag futtatásával. Jegyezze fel a tartozó értékeket **FilePath**, **IconPath**, **IconIndex**, és más fontos információkat az alkalmazás közzétenni kívánt.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Futtassa a következő parancsmag egy új RemoteApp közzététele az 1. lépésben létrehozott csoporthoz.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

5. (Nem kötelező) Futtassa a következő parancsmagot appalias alapján az alkalmazás telepítéséhez. a 3. lépés futtatásakor a kimeneti appalias láthatóvá válik.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -AppAlias <appalias>
   ```

6. Győződjön meg arról, hogy az alkalmazás úgy lett közzétéve, futtassa a következő parancsmagot.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Az alkalmazás csoport közzétenni kívánt minden alkalmazáshoz ismételje meg az 1. és 5.
8. Futtassa a következő parancsmagot a távoli alkalmazások, az alkalmazás csoport hozzáférés adható a felhasználóknak.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalNames <userupn>
   ```

## <a name="next-steps"></a>További lépések

Miután létrehozta az alkalmazáscsoportokhoz, egyszerű szolgáltatások létrehozása, és szerepköröket rendelhet a felhasználók számára. Ennek módjáról, lásd az oktatóanyag az egyszerű szolgáltatásnevekről és a szerepkör-hozzárendelések létrehozása a PowerShell használatával.

> [!div class="nextstepaction"]
> [Szolgáltatásnevek és szerepkör-hozzárendelések létrehozása a PowerShell használatával](create-service-principal-role-powershell.md)
