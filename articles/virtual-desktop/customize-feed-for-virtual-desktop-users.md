---
title: Hírcsatornák testreszabása a Windows virtuális asztali felhasználók számára - Azure
description: A Windows virtuális asztali felhasználók hírcsatornájának testreszabása PowerShell-parancsmagokkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128080"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Csatorna személyre szabása Windows Virtual Desktop-felhasználók számára

Testreszabhatja a hírcsatornát, hogy a RemoteApp és a távoli asztali erőforrások felismerhető módon jelenjenek meg a felhasználók számára.

Először [töltse le és importálja a Windows Virtual Desktop PowerShell modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használatra, ha még nem tette meg. Ezután futtassa a következő parancsmast a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp megjelenítendő nevének testreszabása

A közzétett RemoteApp megjelenítendő nevét a rövid név beállításával módosíthatja. Alapértelmezés szerint a rövid név megegyezik a RemoteApp program nevével.

Egy alkalmazáscsoport közzétett RemoteApps-listájának lekéréséhez futtassa a következő PowerShell-parancsmackét:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![A Get-RDSRemoteApp Get-RDSRemoteApp parancsmag képernyőképe, amelynek neve és friendlyname jave van kiemelve.](media/get-rdsremoteapp.png)

Ha rövid nevet szeretne hozzárendelni egy RemoteApp-hoz, futtassa a következő PowerShell-parancsmacãdat:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![A Set-RDSRemoteApp PowerShell-parancsmag képernyőképe, amelyen a Név és az Új barátságosnév ki van emelve.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Távoli asztal megjelenítendő nevének testreszabása

A közzétett távoli asztal megjelenítendő nevét egy rövid név beállításával módosíthatja. Ha manuálisan hozott létre egy gazdakészletet és asztali alkalmazáscsoportot a PowerShellen keresztül, az alapértelmezett rövid név a "Session Desktop" név. Ha létrehozott egy gazdakészletet és asztali alkalmazáscsoportot a GitHub Azure Resource Manager sablonon vagy az Azure Marketplace-ajánlaton keresztül, az alapértelmezett rövid név megegyezik a gazdakészlet nevével.

A távoli asztali erőforrás lekéréséhez futtassa a következő PowerShell-parancsmast:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![A Get-RDSRemoteApp Get-RDSRemoteApp parancsmag képernyőképe, amelynek neve és friendlyname jave van kiemelve.](media/get-rdsremotedesktop.png)

Ha rövid nevet szeretne rendelni a távoli asztali erőforráshoz, futtassa a következő PowerShell-parancsmackét:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![A Set-RDSRemoteApp PowerShell-parancsmag képernyőképe, amelyen a Név és az Új barátságosnév ki van emelve.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>További lépések

Most, hogy testre szabta a hírcsatornát a felhasználók számára, bejelentkezhet egy Windows virtuális asztali ügyfélbe, hogy tesztelje azt. Ehhez folytassa a Csatlakozás a Windows virtuális asztalhoz útmutatóhoz:
    
 * [Csatlakozás Windows 10 vagy Windows Server 7 rendszerről](connect-windows-7-and-10.md)
 * [Csatlakozás webböngészőről](connect-web.md) 
