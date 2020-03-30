---
title: 'Szolgáltatáskapcsolat – Problémamegoldás: Windows Virtual Desktop – Azure'
description: Az ügyfélkapcsolatok Windows virtuális asztal bérlői környezetben történő beállításakor felmerülő problémák megoldása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127462"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtuális asztal szolgáltatás kapcsolatai

Ebből a cikkből megoldhatja a Windows virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémákat.

## <a name="provide-feedback"></a>Visszajelzés küldése

Visszajelzést adhat nekünk, és megvitathatja a Windows virtuális asztali szolgáltatást a termékcsapattal és a Közösség más aktív tagjaival a [Windows Virtual Desktop Tech Community webhelyén.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>A felhasználó csatlakozik, de semmi sem jelenik meg (nincs hírcsatorna)

A felhasználó elindíthatja a Távoli asztali ügyfeleket, és képes a hitelesítésre, azonban a felhasználó nem lát ikonokat a webes felderítési hírcsatornában.

A következő parancssor használatával ellenőrizze, hogy a problémákat bejelentő felhasználó az alkalmazáscsoportokhoz van-e rendelve:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Ellenőrizze, hogy a felhasználó a megfelelő hitelesítő adatokkal jelentkezik-e be.

Ha a webes ügyfél használatban van, ellenőrizze, hogy nincsenek-e gyorsítótárazott hitelesítő adatokkal kapcsolatos problémák.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>A Windows 10 Enterprise többmunkamenetes virtuális gépei nem válaszolnak

Ha egy virtuális gép nem válaszol, és nem tudja elérni az RDP-n keresztül, akkor a diagnosztikai funkcióval kell elhárítania a diagnosztikai szolgáltatással az állomás állapotának ellenőrzésével.

Az állomás állapotának ellenőrzéséhez futtassa a parancsmast:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Ha az állomás `NoHeartBeat`állapota , az azt jelenti, hogy a virtuális gép nem válaszol, és az ügynök nem tud kommunikálni a Windows virtuális asztal szolgáltatással.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Van néhány dolog, amit tehetünk, hogy rögzítse a NoHeartBeat állapotát.

### <a name="update-fslogix"></a>FSLogix frissítése

Ha a FSLogix nem naprakész, különösen, ha ez a 2.9.7205.27375-ös frxdrvvt.sys verziója, ez holtpontot okozhat. Győződjön meg arról, hogy [az FSLogix frissítése a legújabb verzióra történt.](https://go.microsoft.com/fwlink/?linkid=2084562)

### <a name="disable-bgtaskregistrationmaintenancetask"></a>A BgTaskRegistrationMaintenanceTask letiltása

Ha az FSLogix frissítése nem működik, a probléma az lehet, hogy egy BiSrv összetevő kimeríti a rendszer erőforrásait egy heti karbantartási feladat során. Ideiglenesen tiltsa le a karbantartási feladatot a BgTaskRegistrationMaintenanceTask letiltásával a következő két módszer egyikével:

- Nyissa meg a Start menüt, és keresse meg a **Feladatütemező kifejezést.** Keresse meg a **Feladatütemező könyvtárat** > **Microsoft** > **Windows** > **BrokerInfrastructure**. Keressen egy **BgTaskRegistrationMaintenanceTask**nevű feladatot. Ha megtalálta, kattintson rá a jobb gombbal, és válassza a legördülő menü **Letiltás parancsát.**
- Nyisson meg egy parancssori menüt rendszergazdaként, és futtassa a következő parancsot:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>További lépések

- A Windows virtuális asztal hibáinak és az eszkalációs pályáknak a [hibaelhárítás – áttekintés– áttekintés– visszajelzés és támogatás – áttekintést.](troubleshoot-set-up-overview.md)
- A bérlői és gazdagépkészlet Windows virtuális asztali környezetben való létrehozása során felmerülő problémák elhárításához olvassa el a [Bérlő és a gazdakészlet létrehozása című témakört.](troubleshoot-set-up-issues.md)
- A Windows virtuális asztal virtuális gépének konfigurálása során felmerülő problémák elhárításához olvassa el a [Munkamenetgazda virtuálisgép-konfiguráció című témakört.](troubleshoot-vm-configuration.md)
- A PowerShell Windows virtuális asztallal való használatakor felmerülő problémák elhárításáról a [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)című témakörben található.
- Ha hibaelhárítási oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Erőforrás-kezelő sablontelepítésekkel kapcsolatos hibaelhárítása című témakört.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
