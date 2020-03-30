---
title: Csatlakozás Windows Server virtuális géphez
description: Megtudhatja, hogyan csatlakozhat és jelentkezz be egy Windows virtuális gépre az Azure Portalon és a Resource Manager üzembe helyezési modelljén keresztül.
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
ms.openlocfilehash: 152df830f11cd5a73235559c5c5d65ced44f22fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266766"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Csatlakozás és bejelentkezés Windows rendszert futtató Azure-alapú virtuális gépre
Használja az Azure Portal **Csatlakozás** gombját egy távoli asztali (RDP) munkamenet elindításához egy Windows asztali rendszerről. Először csatlakozik a virtuális géphez, majd bejelentkezik.

Ha Macről szeretne Windows virtuális géphez csatlakozni, telepítenie kell egy Mac-alapú RDP-ügyfelet, például a [Microsoft Remote Desktop programot.](https://aka.ms/rdmac)

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez
1. Lépjen az [Azure Portalra,](https://portal.azure.com/) és csatlakozzon egy virtuális géphez. Keressen és válasszon **virtuális gépek**lehetőséget.
2. Válassza ki a virtuális gépet a listából.
3. A virtuális gép lapjának elején válassza a **Csatlakozás**lehetőséget.
4. A Csatlakozás a **virtuális géphez** lapon válassza az **RDP**lehetőséget, majd válassza ki a megfelelő **IP-címet** és **portszámot.** A legtöbb esetben az alapértelmezett IP-címet és portot kell használni. Válassza **az RDP-fájl letöltése lehetőséget.** Ha a virtuális gép rendelkezik egy just-in-time házirend-készlet, először ki kell **választania** a hozzáférés kérése gombra, hogy hozzáférést kérjen, mielőtt letöltheti az RDP-fájlt. A just-in-time szabályzatról a [Virtuálisgép-hozzáférés kezelése az just in time házirend használatával című témakörben](../../security-center/security-center-just-in-time.md)talál további információt.
5. Nyissa meg a letöltött RDP-fájlt, és amikor a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Figyelmeztetést fog kapni, `.rdp` hogy a fájl ismeretlen közzétevőtől származik. Ez a várható eredmény. A **Távoli asztali kapcsolat** ablakban válassza a **Csatlakozás** lehetőséget a folytatáshoz.
   
    ![Képernyőkép az ismeretlen közzétevőre vonatkozó figyelmeztetésről.](./media/connect-logon/rdp-warn.png)
3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Adja meg egy fiók hitelesítő adatait a virtuális gépen, majd kattintson az **OK gombra.**
   
     **Helyi fiók**: Ez általában a helyi fiók felhasználóneve és jelszava, amelyet a virtuális gép létrehozásakor megadott. Ebben az esetben a tartomány a virtuális gép neve, és a következő módon van megadva: *vmname*&#92;*username*.  
   
    **Tartományhoz csatlakozott virtuális gép**: Ha a virtuális gép tartományhoz tartozik, írja be a felhasználónevet *tartomány*&#92;*felhasználónév*formátumban. A fióknak szerepelnie kell a Rendszergazdák csoportban, vagy távoli hozzáférési jogosultságokkal kell rendelkeznie a virtuális géphez.
   
    **Tartományvezérlő**: Ha a virtuális gép tartományvezérlő, adja meg az adott tartomány tartományi rendszergazdai fiókjának felhasználónevét és jelszavát.
4. Válassza az **Igen** lehetőséget a virtuális gép identitásának ellenőrzéséhez és a bejelentkezés befejezéséhez.
   
   ![Képernyőkép a virtuális gép identitásának ellenőrzési üzenetéről.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Ha a **portálon** a Csatlakozás gomb szürkén jelenik meg, és nem csatlakozik az Azure-hoz [expressz útvonalon](../../expressroute/expressroute-introduction.md) vagy [helyek közötti VPN-kapcsolaton](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) keresztül, az RDP használata előtt létre kell hoznia és hozzá kell rendelnie a virtuális géphez egy nyilvános IP-címet. További információ: [Public IP addresses in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Csatlakozás a virtuális géphez a PowerShell használatával

 

Ha PowerShellt használ, és telepítette az Azure PowerShell-modult, a parancsmag használatával is csatlakozhat, az `Get-AzRemoteDesktopFile` alábbiak szerint.

Ez a példa azonnal elindítja az RDP-kapcsolatot, és a fentiekhez hasonló utasításokat ad.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Az RDP-fájlt későbbi használatra is mentheti.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>További lépések
Ha nehezen tud csatlakozni, olvassa el a Távoli asztali kapcsolatok – Problémamegoldás című [témakört.](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 

