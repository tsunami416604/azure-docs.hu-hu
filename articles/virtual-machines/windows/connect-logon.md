---
title: Csatlakozás Windows Server-rendszerű virtuális géphez | Microsoft Docs
description: Útmutató a Windows rendszerű virtuális gépekhez való kapcsolódáshoz és a Azure Portal és a Resource Manager-alapú üzemi modell használatával történő bejelentkezéshez.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 1980ee0bdea1316f6117d9deb7d2adb9df941768
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162613"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre
Használja az Azure Portal **Csatlakozás** gombját egy távoli asztali (RDP) munkamenet elindításához egy Windows asztali rendszerről. Először kapcsolódjon a virtuális géphez, majd jelentkezzen be.

Ha Mac számítógépről szeretne csatlakozni egy Windows rendszerű virtuális géphez, telepítenie kell egy RDP-ügyfelet a Mac rendszerhez, például [Microsoft távoli asztal](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben válassza a **Virtual Machines**lehetőséget.
3. Válassza ki a virtuális gépet a listából.
4. A virtuális gép oldalának tetején válassza a **kapcsolat**lehetőséget.
2. A **Csatlakozás virtuális géphez** lapon válassza ki a megfelelő IP-címet és portot. A legtöbb esetben az alapértelmezett IP-címet és portot kell használni. Válassza az **RDP-fájl letöltése** parancsot. Ha a virtuális gépen egy igény szerinti szabályzat van beállítva, először a hozzáférés **kérése** gombra kell kattintania ahhoz, hogy le tudja tölteni az RDP-fájlt. Az igény szerinti szabályzattal kapcsolatos további információkért lásd: [virtuális gépek hozzáférésének kezelése az igény szerinti házirend használatával](../../security-center/security-center-just-in-time.md).
2. Nyissa meg a letöltött RDP-fájlt, és válassza a **Kapcsolódás** lehetőséget, amikor a rendszer kéri. 
2. Figyelmeztetés jelenik meg arról, hogy a `.rdp` fájl ismeretlen közzétevőtől származik. Ez a várható eredmény. A **Távoli asztali kapcsolat** ablakban válassza a **Kapcsolódás** lehetőséget a folytatáshoz.
   
    ![Képernyőkép az ismeretlen közzétevőre vonatkozó figyelmeztetésről.](./media/connect-logon/rdp-warn.png)
3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Adja meg egy fiók hitelesítő adatait a virtuális gépen, majd kattintson az **OK gombra**.
   
     **Helyi fiók**: Ez általában a virtuális gép létrehozásakor megadott helyi fiók felhasználóneve és jelszava. Ebben az esetben a tartomány a virtuális gép neve, és a következő módon van megadva: *vmname*&#92;*username*.  
   
    **Tartományhoz csatlakoztatott virtuális gép**: Ha a virtuális gép tartományhoz tartozik, írja be a felhasználónevet a *tartomány*&#92;*Felhasználónév*formátumban. A fióknak szerepelnie kell a Rendszergazdák csoportban, vagy távoli hozzáférési jogosultságokkal kell rendelkeznie a virtuális géphez.
   
    **Tartományvezérlő**: Ha a virtuális gép tartományvezérlő, adja meg az adott tartományhoz tartozó tartományi rendszergazdai fiók felhasználónevét és jelszavát.
4. Válassza az **Igen** lehetőséget a virtuális gép identitásának ellenőrzéséhez és a bejelentkezés befejezéséhez.
   
   ![Képernyőkép a virtuális gép identitásának ellenőrzésére vonatkozó üzenetről.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Ha a portálon a **Csatlakozás** gomb szürkén jelenik meg, és nem csatlakozik az Azure-hoz egy [expressz útvonalon](../../expressroute/expressroute-introduction.md) vagy [helyek közötti VPN-](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) kapcsolaton keresztül, akkor az RDP használata előtt létre kell hoznia és hozzá kell rendelnie egy nyilvános IP-címet a virtuális géphez. További információ: [nyilvános IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Kapcsolódás a virtuális géphez a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha a PowerShellt használja, és a Azure PowerShell modul telepítve van, akkor a `Get-AzRemoteDesktopFile` parancsmag használatával is kapcsolódhat, ahogy az alább látható.

Ez a példa azonnal elindítja az RDP-kapcsolatokat, így a fentiekhez hasonló kérések is megjelennek.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Az RDP-fájl későbbi használatra is menthető.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>További lépések
Ha problémája van a kapcsolódással, tekintse meg a [Távoli asztal kapcsolatok hibáinak megoldása](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakört 

