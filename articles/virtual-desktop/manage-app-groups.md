---
title: Alkalmazás-csoportok kezelése a Windows rendszerű virtuális asztali előzetes verzióhoz – Azure
description: Leírja, hogyan kell beállítani a Windows rendszerű virtuális asztali előnézeti bérlőket a Azure Active Directoryban.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 2bec7e490443727fa294e7be9412bb20ae66e691
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163251"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Oktatóanyag: Alkalmazás-csoportok kezelése a Windows rendszerű virtuális asztal előzetes verziójához

Az új Windows virtuális asztali előzetes verziójú címkészlet számára létrehozott alapértelmezett alkalmazáscsoport a teljes asztalt is közzéteszi. Emellett létrehozhat egy vagy több RemoteApp-alkalmazáscsoport is a gazdagéphez. Ezt az oktatóanyagot követve hozzon létre egy RemoteApp-alkalmazáscsoport alkalmazást, és tegye közzé az egyes **Start** menüket.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * Hozzáférés biztosítása RemoteApp-programokhoz.

Mielőtt elkezdené, [töltse le és importálja a](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

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
   
4. A következő parancsmag futtatásával telepítheti az alkalmazást a `AppAlias`alapján. `AppAlias`a 3. lépés kimenetének futtatásakor láthatóvá válik.

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

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy alkalmazáscsoport, hogyan tölthető be a RemoteApp-programokkal, és hogyan rendelhet hozzá felhasználókat az alkalmazás-csoporthoz. Az érvényesítési gazdagépek létrehozásáról a következő oktatóanyagban olvashat bővebben. Az ellenőrzési gazdagépek segítségével figyelheti a szolgáltatás frissítéseit, mielőtt az éles környezetbe helyezné őket.

> [!div class="nextstepaction"]
> [Gazdagép-készlet létrehozása a szolgáltatás frissítéseinek ellenőrzéséhez](./create-validation-host-pool.md)
