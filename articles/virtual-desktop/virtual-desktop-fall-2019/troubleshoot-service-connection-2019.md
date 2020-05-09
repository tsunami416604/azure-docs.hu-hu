---
title: A Windows rendszerű virtuális asztali kapcsolat hibáinak megoldása – Azure
description: Az ügyfélkapcsolatok Windows virtuális asztali bérlői környezetben való beállításakor felmerülő problémák megoldása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 01aff34839cc7385834468a08f30696efe84561f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614771"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows rendszerű virtuális asztali szolgáltatások kapcsolatai

>[!IMPORTANT]
>Ez a tartalom a Fall 2019 kiadásra vonatkozik, amely nem támogatja a Windows rendszerű virtuális asztali objektumokat Azure Resource Manager. Ha a Spring 2020 Update szolgáltatásban bevezetett Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../troubleshoot-service-connection.md).

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

Ha a gazdagép állapota `NoHeartBeat`, az azt jelenti, hogy a virtuális gép nem válaszol, és az ügynök nem tud kommunikálni a Windows virtuális asztali szolgáltatással.

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

## <a name="next-steps"></a>További lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview-2019.md).
- A bérlők és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [bérlői és az alkalmazáskészletek létrehozását](troubleshoot-set-up-issues-2019.md)ismertető részt.
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration-2019.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell-2019.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
