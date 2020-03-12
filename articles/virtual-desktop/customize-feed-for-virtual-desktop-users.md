---
title: A Windows rendszerű virtuális asztali felhasználók hírcsatornáinak testreszabása – Azure
description: A Windows rendszerű virtuális asztali felhasználók hírcsatornáinak testreszabása PowerShell-parancsmagokkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128080"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Csatorna személyre szabása Windows Virtual Desktop-felhasználók számára

Testreszabhatja a hírcsatornát, hogy a RemoteApp-és távoli asztali erőforrások felismerhető módon jelenjenek meg a felhasználók számára.

Először [töltse le és importálja a](/powershell/windows-virtual-desktop/overview/) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>A RemoteApp megjelenítendő nevének testreszabása

A közzétett RemoteApp megjelenített nevét megváltoztathatja a felhasználóbarát név beállításával. Alapértelmezés szerint a felhasználóbarát név megegyezik a RemoteApp program nevével.

Az alkalmazáscsoport közzétett RemoteAppinak listájának lekéréséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Képernyőkép a Get-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és FriendlyName ki van emelve.](media/get-rdsremoteapp.png)

Ha rövid nevet szeretne hozzárendelni egy RemoteApp-hoz, futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Képernyőkép a set-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és új FriendlyName ki van emelve.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Távoli asztal megjelenítendő nevének testreszabása

Egy közzétett távoli asztal megjelenítendő nevét a felhasználóbarát név beállításával módosíthatja. Ha manuálisan hozott létre egy gazdagép-készletet és egy asztali alkalmazást a PowerShell használatával, az alapértelmezett felhasználóbarát név a "munkamenet-asztal". Ha a GitHub Azure Resource Manager sablonnal vagy az Azure Marketplace-ajánlaton keresztül hozott létre egy gazdagép-készletet és egy asztali alkalmazást, az alapértelmezett felhasználóbarát név ugyanaz, mint a gazdagép-készlet neve.

A távoli asztal erőforrásának lekéréséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Képernyőkép a Get-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és FriendlyName ki van emelve.](media/get-rdsremotedesktop.png)

Ha rövid nevet szeretne rendelni a távoli asztali erőforráshoz, futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Képernyőkép a set-RDSRemoteApp PowerShell-parancsmagról, amelynek neve és új FriendlyName ki van emelve.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy testre szabta a hírcsatornát a felhasználók számára, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe a teszteléshez. Ehhez folytassa a Kapcsolódás a Windows rendszerű virtuális asztali környezetekhez:
    
 * [Windows 10 vagy Windows 7 rendszerről való kapcsolat](connect-windows-7-and-10.md)
 * [Webböngészőből való csatlakozási szolgáltatás](connect-web.md) 
