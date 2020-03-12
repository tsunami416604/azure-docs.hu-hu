---
title: A Windows rendszerű virtuális asztali kapcsolat hibáinak megoldása – Azure
description: Az ügyfélkapcsolatok Windows virtuális asztali bérlői környezetben való beállításakor felmerülő problémák megoldása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127462"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows rendszerű virtuális asztali szolgáltatások kapcsolatai

Ez a cikk a Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák megoldásához használható.

## <a name="provide-feedback"></a>Visszajelzés küldése

Küldhet visszajelzést, és megvitathatja a Windows rendszerű virtuális asztali szolgáltatást a termék csapatával és a többi aktív közösségi taggal a [Windows Virtual Desktop technikai közösségében](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>A felhasználó csatlakozik, de semmi nem jelenik meg (nincs hírcsatorna)

A felhasználók elindíthatják Távoli asztal-ügyfeleket, és képesek hitelesíteni magukat, azonban a felhasználó nem lát ikonokat a webes felderítési hírcsatornában.

Győződjön meg arról, hogy a problémát jelentő felhasználó a következő parancssor használatával van hozzárendelve az alkalmazási csoportokhoz:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Győződjön meg arról, hogy a felhasználó a megfelelő hitelesítő adatokkal jelentkezik be.

Ha a webes ügyfél használatban van, ellenőrizze, hogy nincsenek-e gyorsítótárazott hitelesítő adatok.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>A Windows 10 Enterprise több munkamenetet támogató virtuális gépei nem válaszolnak

Ha a virtuális gép nem válaszol, és az RDP-n keresztül nem fér hozzá, akkor a gazdagép állapotának ellenőrzésével kell a diagnosztikai szolgáltatással elhárítani.

A gazdagép állapotának megtekintéséhez futtassa a következő parancsmagot:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Ha a gazdagép állapota `NoHeartBeat`, az azt jelenti, hogy a virtuális gép nem válaszol, és az ügynök nem tud kommunikálni a Windows rendszerű virtuális asztali szolgáltatással.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Van néhány dolog, amit megtehet a szívverési állapot kijavítása érdekében.

### <a name="update-fslogix"></a>FSLogix frissítése

Ha a FSLogix nem naprakész, különösen akkor, ha az frxdrvvt. sys verziója 2.9.7205.27375, a holtpontot okozhat. Győződjön meg arról, hogy [a legújabb verzióra frissíti a FSLogix](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>BgTaskRegistrationMaintenanceTask letiltása

Ha a FSLogix frissítése nem működik, a probléma az lehet, hogy egy BiSrv-összetevő egy heti karbantartási feladat során kimeríti a rendszererőforrásokat. Ideiglenesen tiltsa le a karbantartási feladatot úgy, hogy letiltja a BgTaskRegistrationMaintenanceTask az alábbi két módszer egyikével:

- Lépjen a Start menüre, és keresse **meg a Feladatütemezőt.** Navigáljon a Feladatütemező **könyvtár** > **Microsoft** > **Windows** > **BrokerInfrastructure**. Keresse meg a **BgTaskRegistrationMaintenanceTask**nevű feladatot. Ha megtalálta, kattintson rá a jobb gombbal, és válassza a **Letiltás** lehetőséget a legördülő menüből.
- Nyisson meg egy parancssori menüt rendszergazdaként, és futtassa a következő parancsot:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Következő lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A bérlők és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [bérlői és az alkalmazáskészletek létrehozását](troubleshoot-set-up-issues.md)ismertető részt.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
