---
title: Csatlakozás Windows Server-rendszerű virtuális géphez | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat, és jelentkezzen be Windows virtuális gép az Azure portal és a Resource Manager üzemi modell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: c14b6e16a16f325681722203eaa05a435b141208
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990628"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Hogyan csatlakozhat, és jelentkezzen be az Azure Windows rendszerű virtuális gép
Használja az Azure Portal **Csatlakozás** gombját egy távoli asztali (RDP) munkamenet elindításához egy Windows asztali rendszerről. Először csatlakozzon a virtuális gépet, és jelentkezzen.

Egy Windows virtuális Géphez való csatlakozáshoz Mac rendszeren, akkor telepítenie kell egy RDP-ügyfelet Mac például [Microsoft távoli asztal](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben válassza ki a **virtuális gépek**.
3. Válassza ki a virtuális gépet a listából.
4. A virtuális gép az oldal felső részén válassza ki a **Connect**.
2. Az a **csatlakozhat a virtuális gép** lapon, válassza ki a megfelelő IP-címet és portot. A legtöbb esetben az alapértelmezett IP-címet és portot kell használni. Válassza az **RDP-fájl letöltése** parancsot. Ha a virtuális gépen egy just-in-time-szabályzat beállítása, először válassza ki a **hozzáférés kérése** gombra kattintva kérhet hozzáférést az RDP-fájljának letöltése előtt. A just-in-time házirenddel kapcsolatos további információkért lásd: [kezelése virtuálisgép-hozzáférés az igény szerinti szabályzat](../../security-center/security-center-just-in-time.md).
2. Nyissa meg a letöltött RDP-fájlt, és válassza ki **Connect** amikor a rendszer kéri. 
2. Egy figyelmeztetés fog megjelenni, amely a `.rdp` fájl közzétevője ismeretlen. Ez a várható eredmény. Az a **távoli asztali kapcsolat** ablakban válassza **Connect** folytatásához.
   
    ![Képernyőkép az ismeretlen közzétevőre vonatkozó figyelmeztetésről.](./media/connect-logon/rdp-warn.png)
3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Adja meg a hitelesítő adatokat egy fiók a virtuális gépen, majd **OK**.
   
     **Helyi fiók**: Ez általában a helyi fiók felhasználói nevét és a virtuális gép létrehozásakor megadott jelszót. Ebben az esetben a tartomány a virtuális gép neve, és a következő módon van megadva: *vmname*&#92;*username*.  
   
    **Tartományhoz csatlakoztatott virtuális gép**: Ha a virtuális Gépet egy tartományhoz tartozik, adja meg a felhasználónevet a következő formátumban *tartomány*&#92;*felhasználónév*. A fióknak szerepelnie kell a Rendszergazdák csoportban, vagy távoli hozzáférési jogosultságokkal kell rendelkeznie a virtuális géphez.
   
    **Tartományvezérlő**: Ha a virtuális gép egy tartományvezérlő, adja meg a felhasználónevet és a egy tartományi rendszergazdai fiók jelszavát ahhoz a tartományhoz.
4. Válassza ki **Igen** a virtuális gép identitásának ellenőrzéséhez és a bejelentkezés befejezéséhez.
   
   ![Képernyőkép a virtuális gép identitásának ellenőrzésére vonatkozó üzenetről.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Ha a **Connect** gomb a portálon érhető el, és nem csatlakozik az Azure- [Express Route](../../expressroute/expressroute-introduction.md) vagy [Site-to-Site VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) -kapcsolatot kell létrehozni és a virtuális gép nyilvános IP-címet rendelni RDP használata előtt. További információkért lásd: [nyilvános IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Csatlakozzon a virtuális géphez PowerShell használatával

Ha a Powershellt, és az AzureRM-modul telepítve van, is kapcsolódhatnak használatával a `Get-AzRemoteDesktopFile` parancsmag, az alább látható módon.

Ebben a példában azonnal elindul az RDP-kapcsolat, tart, mint a fenti hasonló kérdések keresztül.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Mentheti későbbi használatra az RDP-fájlt.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>További lépések
Ha nehézségekbe csatlakozás, lásd: [távoli asztali kapcsolatainak hibaelhárításával](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

