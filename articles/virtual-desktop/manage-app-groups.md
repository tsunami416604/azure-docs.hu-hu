---
title: Alkalmazáscsoportok kezelése a Windows virtuális asztal előzetes – Azure
description: Ismerteti, hogyan állíthatja be Windows virtuális asztal előzetes bérlők számára az Azure Active Directoryban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 73425df1f0cfedd2a681650fc2b536a652b621d5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206679"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Oktatóanyag: Alkalmazáscsoportok kezelése Windows virtuális asztal előzetes verzió

Létrehozott egy új Windows virtuális asztal előzetes gazdagép-készlet alapértelmezett alkalmazáscsoportnak is közzéteszi a teljes asztalhoz. Emellett a gazdagép-készlet egy vagy több RemoteApp alkalmazáscsoportok is létrehozhat. Ez az oktatóanyag egy RemoteApp-app csoport létrehozása és közzététele az egyes **Start** menü alkalmazásokat.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * RemoteApp-programok hozzáférést biztosít.

Mielőtt elkezdené, [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

## <a name="create-a-remoteapp-group"></a>A RemoteApp-csoport létrehozása

1. Futtassa a következő PowerShell-parancsmag egy új RemoteApp üres app csoport létrehozásához.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Nem kötelező) Győződjön meg arról, hogy az alkalmazás csoport létrejött, futtathatja a következő parancsmagot a gazdagép-készlet az összes alkalmazás csoportok listájának megtekintéséhez.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Listáját a következő parancsmag futtatásával **Start** virtuálisgép-lemezkép a gazdagép-készlet alkalmazások menüben. Jegyezze fel a tartozó értékeket **FilePath**, **IconPath**, **IconIndex**, és más fontos információkat a közzétenni kívánt alkalmazáshoz.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Futtassa az alábbi parancsmagot, az alkalmazás alapján való `AppAlias`. `AppAlias` láthatóvá válik a 3. lépésében a kimeneti futtatásakor.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Nem kötelező) Futtassa a következő parancsmag egy új RemoteApp-program közzététele az 1. lépésben létrehozott csoporthoz.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Győződjön meg arról, hogy az alkalmazás úgy lett közzétéve, futtassa a következő parancsmagot.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Az alkalmazás csoport közzétenni kívánt minden egyes alkalmazáshoz ismételje meg az 1. és 5.
8. Futtassa a következő parancsmagot a RemoteApp-programok, az alkalmazás csoport hozzáférés adható a felhasználóknak.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan hozzon létre egy app-csoportot, RemoteApp-programok való feltöltéséhez és felhasználók hozzárendelése az alkalmazás csoport. Megtudhatja, hogyan hoz létre egy érvényesítési gazdagép készletet, lásd a következő oktatóanyagot. Érvényesítési gazdagép készlet használatával figyelheti a szolgáltatásfrissítések, mielőtt megvalósítaná azokat az éles környezetben.

> [!div class="nextstepaction"]
> [Szolgáltatásfrissítések ellenőrzése gazdagép-készlet létrehozása](./create-validation-host-pool.md)
