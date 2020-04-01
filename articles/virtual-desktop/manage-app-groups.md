---
title: Alkalmazáscsoportok kezelése a Windows virtuális asztalhoz – Azure
description: A Windows virtuális asztal bérlőinek beállítása az Azure Active Directoryban című témakört ismerteti.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9a9d92ea525c6b5a64fdf7cc74babdce6a97f923
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127808"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Oktatóanyag: A Windows virtuális asztal alkalmazáscsoportjainak kezelése

Az új Windows virtuális asztal gazdakészlethez létrehozott alapértelmezett alkalmazáscsoport a teljes asztalt is közzéteszi. Emellett létrehozhat egy vagy több RemoteApp-alkalmazáscsoportot a gazdakészlethez. Az oktatóanyag ból létrehozhat egy RemoteApp-alkalmazáscsoportot, és közzétehet egyedi **Start** menüalkalmazásokat.

Az oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy RemoteApp-csoportot.
> * Hozzáférés a RemoteApp-programokhoz.

Mielőtt elkezdené, [töltse le és importálja a Windows Virtual Desktop PowerShell modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használatra, ha még nem tette meg. Ezután futtassa a következő parancsmast a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>RemoteApp-csoport létrehozása

1. Futtassa a következő PowerShell-parancsmast egy új üres RemoteApp-alkalmazáscsoport létrehozásához.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Nem kötelező) Annak ellenőrzéséhez, hogy az alkalmazáscsoport létrejött-e, a következő parancsmag futtatásával megtekintheti a gazdagépkészlet összes alkalmazáscsoportjának listáját.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. A következő parancsmag futtatásával leszeretné jelenlennie a **gazdakészlet** virtuálisgép-lemezképén található Start menüalkalmazások listájának legördülő listájáról. Írja le a **közzétenni**kívánt alkalmazás FilePath , **IconPath**, **IconIndex**és egyéb fontos információinak értékeit.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Futtassa a következő parancsmalapot `AppAlias`az alkalmazás telepítéséhez a alapján. `AppAlias`a 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Nem kötelező) Futtassa a következő parancsmalapot egy új RemoteApp-program közzétételéhez az 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Az alkalmazás közzétételének ellenőrzéséhez futtassa a következő parancsmast.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Ismételje meg az 1–5.
8. Futtassa a következő parancsmast, hogy a felhasználók hozzáférjenek az alkalmazáscsoport RemoteApp-programjaihoz.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy alkalmazáscsoportot, hogyan népesítheti fel a RemoteApp-programokkal, és hogyan rendelhethozzá felhasználókat az alkalmazáscsoporthoz. Az érvényesítési gazdakészlet létrehozásáról az alábbi oktatóanyagból olvashat. Az ellenőrzési gazdakészlet segítségével figyelheti a szolgáltatásfrissítéseit, mielőtt az éles környezetbe való kihelyezésüket.

> [!div class="nextstepaction"]
> [Gazdagépcsoport létrehozása a szolgáltatásfrissítések érvényesítéséhez](./create-validation-host-pool.md)
