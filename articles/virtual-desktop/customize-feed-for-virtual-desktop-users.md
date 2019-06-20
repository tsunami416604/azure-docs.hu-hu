---
title: Testre szabhatja a virtuális asztali Windows-felhasználók – az Azure-csatornája
description: Hogyan szabhatja testre a PowerShell-parancsmagok a Windows virtuális asztali felhasználók hírcsatorna.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 869760a12089ed7453199ad8a3fa18a6cca87511
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157089"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Csatorna személyre szabása Windows Virtual Desktop-felhasználók számára

A hírcsatorna testre szabhatja, így a felhasználók számára könnyen felismerhető névre úgy jelennek meg a RemoteApp- és távoli asztali erőforrás.

Először [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Testre szabhatja a RemoteApp megjelenített neve

A közzétett RemoteApp megjelenítendő nevét módosíthatja a rövid név beállításával. Alapértelmezés szerint a valódi név megegyezik a RemoteApp-program nevét.

Egy alkalmazás-csoport távoli közzétett alkalmazások listájának lekéréséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![PowerShell-parancsmag képernyőképe a Get-RDSRemoteApp nevét és rövid név kiemelve.](media/get-rdsremoteapp.png)

Egy rövid nevet a RemoteApp rendelni, futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![PowerShell-parancsmag képernyőképe a Set-RDSRemoteApp nevű és új rövid név kiemelve.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Testre szabhatja a távoli asztali megjelenített neve

Egy rövid nevet beállításával módosíthatja a közzétett távoli asztali megjelenítendő nevét. Ha manuálisan hozott létre egy gazdagép-készlet és a egy asztali alkalmazás csoport PowerShell-lel, az alapértelmezett felhasználóbarát név-e a "Desktop munkamenet." Készlet és a egy asztali alkalmazás csoportot a GitHub Azure Resource Manager-sablon vagy az ajánlat az Azure piactéren keresztül létrehozta a gazdagép, az alapértelmezett rövid neve ugyanaz, mint a készlet neve.

A távoli asztali erőforrás lekéréséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![PowerShell-parancsmag képernyőképe a Get-RDSRemoteApp nevét és rövid név kiemelve.](media/get-rdsremotedesktop.png)

Egy rövid nevet a távoli asztali erőforrás rendelni, futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![PowerShell-parancsmag képernyőképe a Set-RDSRemoteApp nevű és új rövid név kiemelve.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>További lépések

Most, hogy testre szabta a hírcsatorna a felhasználók számára, bejelentkezhet egy Windows virtuális asztali ügyfélhez próbálhatja ki. Ehhez továbbra is a csatlakozás a Windows virtuális asztal útmutatók:
    
 * [Csatlakozás a Windows 10-es vagy Windows 7](connect-windows-7-and-10.md)
 * [Egy webböngészőből csatlakozzon](connect-web.md) 
