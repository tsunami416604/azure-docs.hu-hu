---
title: Alkalmazáscsoportok kezelése a Windows virtuális asztal előzetes – Azure
description: Ismerteti, hogyan állíthatja be Windows virtuális asztal előzetes bérlők számára az Azure Active Directoryban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: cba13012bf165a097bd1382da8ef9897b0584d28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066876"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Oktatóanyag: Alkalmazáscsoportok kezelése Windows virtuális asztal előzetes verzió

Létrehozott egy új Windows virtuális asztal előzetes gazdagép-készlet alapértelmezett alkalmazáscsoportnak is közzéteszi a teljes asztalhoz. Emellett a gazdagép-készlet egy vagy több RemoteApp alkalmazáscsoportok is létrehozhat. Ez az oktatóanyag egy RemoteApp-app csoport létrehozása és közzététele a Start menü egyéni alkalmazások.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * Távoli alkalmazások hozzáférést biztosít.

Mielőtt elkezdené, [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a Windows virtuális asztal modul használatához a PowerShell-munkamenetben, ha még nem tette.

## <a name="create-a-remoteapp-group"></a>A RemoteApp-csoport létrehozása

1. Futtassa a következő PowerShell-parancsmag egy új RemoteApp üres app csoport létrehozásához.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Nem kötelező) Az alkalmazás csoport létrejöttének ellenőrzéséhez futtathatja a következő parancsmagot a gazdagép-készlet az összes alkalmazás csoportok listájának megtekintéséhez.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Virtuálisgép-lemezkép a gazdagép-készlet kezdő listáját menü alkalmazások beolvasása a következő parancsmag futtatásával. Jegyezze fel a tartozó értékeket **FilePath**, **IconPath**, **IconIndex**, és más fontos információkat az alkalmazás közzétenni kívánt.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Futtassa a következő parancsmagot a appalias alapján az alkalmazás telepítéséhez. a 3. lépés futtatásakor a kimeneti appalias láthatóvá válik.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Nem kötelező) Futtassa a következő parancsmag egy új RemoteApp közzététele az 1. lépésben létrehozott csoporthoz.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Győződjön meg arról, hogy az alkalmazás úgy lett közzétéve, futtassa a következő parancsmagot.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Az alkalmazás csoport közzétenni kívánt minden alkalmazáshoz ismételje meg az 1. és 5.
8. Futtassa a következő parancsmagot a távoli alkalmazások, az alkalmazás csoport hozzáférés adható a felhasználóknak.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan hozzon létre az alkalmazás, távoli alkalmazások való feltöltéséhez és hozzárendelhet felhasználókat az alkalmazáscsoportnak. Megtudhatja, hogyan hozhat létre egy érvényesítési gazdagép készletet, amely lehetővé teszi a szolgáltatásfrissítések, mielőtt megvalósítaná azokat az éles környezetben figyelése, tekintse meg a gazdagép készlet létrehozása a service-oktatóanyag frissítések ellenőrzése.

> [!div class="nextstepaction"]
> [Szolgáltatás frissítések oktatóanyag ellenőrzése gazdagép-készlet létrehozása](./create-validation-host-pool.md)
