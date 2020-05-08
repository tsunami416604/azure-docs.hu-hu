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
ms.openlocfilehash: a6298b3a9c5769b1d82f89956736b451935b2c5d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612639"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows rendszerű virtuális asztali szolgáltatások kapcsolatai

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk a Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák megoldásához használható.

## <a name="provide-feedback"></a>Visszajelzés küldése

Küldhet visszajelzést, és megvitathatja a Windows rendszerű virtuális asztali szolgáltatást a termék csapatával és a többi aktív közösségi taggal a [Windows Virtual Desktop technikai közösségében](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>A felhasználó csatlakozik, de semmi nem jelenik meg (nincs hírcsatorna)

A felhasználók elindíthatják Távoli asztal-ügyfeleket, és képesek hitelesíteni magukat, azonban a felhasználó nem lát ikonokat a webes felderítési hírcsatornában.

Győződjön meg arról, hogy a problémát jelentő felhasználó a következő parancssor használatával van hozzárendelve az alkalmazási csoportokhoz:

```PowerShell
Get-AzRoleAssignment -SignInName <userupn>
```

Győződjön meg arról, hogy a felhasználó a megfelelő hitelesítő adatokkal jelentkezik be.

Ha a webes ügyfél használatban van, ellenőrizze, hogy nincsenek-e gyorsítótárazott hitelesítő adatok.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>A Windows 10 Enterprise több munkamenetet támogató virtuális gépei nem válaszolnak

Ha a virtuális gép nem válaszol, és az RDP-n keresztül nem fér hozzá, akkor a gazdagép állapotának ellenőrzésével kell a diagnosztikai szolgáltatással elhárítani.

A gazdagép állapotának megtekintéséhez futtassa a következő parancsmagot:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -ResourceGroupName <resourcegroupname>| Format-List Name, LastHeartBeat, AllowNewSession, Status
```

Ha a gazdagép állapota `NoHeartBeat`, az azt jelenti, hogy a virtuális gép nem válaszol, és az ügynök nem tud kommunikálni a Windows virtuális asztali szolgáltatással.

```powershell
Name            : 0301HP/win10pd-0.contoso.com 
LastHeartBeat   : 4/8/2020 1:48:35 AM 
AllowNewSession : True 
Status          : Available 

Name            : 0301HP/win10pd-1.contoso.com 
LastHeartBeat   : 4/8/2020 1:45:44 AM 
AllowNewSession : True 
Status          : NoHeartBeat
```

Van néhány dolog, amit megtehet a szívverési állapot kijavítása érdekében.

### <a name="update-fslogix"></a>FSLogix frissítése

Ha a FSLogix nem naprakész, különösen akkor, ha az frxdrvvt. sys verziója 2.9.7205.27375, a holtpontot okozhat. Győződjön meg arról, hogy [a legújabb verzióra frissíti a FSLogix](https://go.microsoft.com/fwlink/?linkid=2084562).

## <a name="next-steps"></a>További lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A Windows rendszerű virtuális asztali környezetek és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [környezet és az alkalmazáskészlet létrehozása](troubleshoot-set-up-issues.md)című témakört
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
