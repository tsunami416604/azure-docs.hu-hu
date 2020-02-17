---
title: Windows rendszerű virtuális asztali alkalmazás-csoportok kezelése – Azure
description: Leírja, hogyan kell beállítani a Windows rendszerű virtuális asztali bérlőket a Azure Active Directoryban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 8469b54afe01d9ee42dda8cf99f2f0125a4a1982
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367316"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Oktatóanyag: alkalmazás-csoportok kezelése a Windows rendszerű virtuális asztali gépeken

Az új Windows rendszerű virtuális asztali készlethez létrehozott alapértelmezett alkalmazáscsoport a teljes asztalt is közzéteszi. Emellett létrehozhat egy vagy több RemoteApp-alkalmazáscsoport is a gazdagéphez. Ezt az oktatóanyagot követve hozzon létre egy RemoteApp-alkalmazáscsoport alkalmazást, és tegye közzé az egyes **Start** menüket.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * Hozzáférés biztosítása RemoteApp-programokhoz.

Mielőtt elkezdené, [töltse le és importálja a](/powershell/windows-virtual-desktop/overview/) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>RemoteApp-csoport létrehozása

1. A következő PowerShell-parancsmag futtatásával hozzon létre egy új, üres RemoteApp-alkalmazás csoportot.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Választható Annak ellenőrzéséhez, hogy az alkalmazáscsoport létrejött-e, a következő parancsmag futtatásával megtekintheti az alkalmazáskészlethez tartozó összes alkalmazás-csoportot.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Futtassa a következő parancsmagot a **Start** menü alkalmazások listájának lekéréséhez a gazdaszámítógép virtuálisgép-rendszerképén. Jegyezze fel a közzétenni kívánt alkalmazás **filepath**, **IconPath**, **IconIndex**és egyéb fontos információinak értékeit.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. A következő parancsmag futtatásával telepítheti az alkalmazást `AppAlias`alapján. Ha a 3. lépésben a kimenetet futtatja, a `AppAlias` látható lesz.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Választható Futtassa a következő parancsmagot egy új RemoteApp-program közzétételéhez az 1. lépésben létrehozott alkalmazáscsoport számára.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Az alkalmazás közzétételének ellenőrzéséhez futtassa a következő parancsmagot.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Ismételje meg az 1 – 5. lépést minden olyan alkalmazás esetében, amelyet közzé szeretne tenni ehhez az alkalmazási csoporthoz.
8. Futtassa a következő parancsmagot, hogy hozzáférést biztosítson a felhasználóknak a RemoteApp-programokhoz az alkalmazás csoportban.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy alkalmazáscsoport, hogyan tölthető be a RemoteApp-programokkal, és hogyan rendelhet hozzá felhasználókat az alkalmazás-csoporthoz. Az érvényesítési gazdagépek létrehozásáról a következő oktatóanyagban olvashat bővebben. Az ellenőrzési gazdagépek segítségével figyelheti a szolgáltatás frissítéseit, mielőtt az éles környezetbe helyezné őket.

> [!div class="nextstepaction"]
> [Gazdagép-készlet létrehozása a szolgáltatás frissítéseinek ellenőrzéséhez](./create-validation-host-pool.md)
