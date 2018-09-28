---
title: Csatlakozás Windows Server-rendszerű virtuális géphez | Microsoft Docs
description: Megtudhatja, hogyan csatlakozhat és jelentkezhet be egy Windows virtuális gépre az Azure Portal és a Resource Manager-alapú üzemi modell használatával.
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
ms.date: 09/13/2018
ms.author: cynthn
ms.openlocfilehash: b9cce5658b705e9d3255d2662b2a0157a2e548c3
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409031"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Csatlakozás és bejelentkezés Windows rendszert futtató Azure virtuális gépre
Használja az Azure Portal **Csatlakozás** gombját egy távoli asztali (RDP) munkamenet elindításához egy Windows asztali rendszerről. Először csatlakozzon a virtuális gépet, és jelentkezzen be.

Egy Windows virtuális Géphez való csatlakozáshoz Mac rendszeren, akkor telepítenie kell egy RDP-ügyfelet Mac például [Microsoft távoli asztal](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben válassza ki a **virtuális gépek**.
3. Válassza ki a virtuális gépet a listából.
4. A virtuális gép az oldal felső részén válassza ki a **Connect**.
2. Az a **csatlakozhat a virtuális gép** lapon válassza ki a megfelelő beállításokat, és válassza **töltse le az RDP-fájl**.
2. Nyissa meg a letöltött RDP-fájlt, és válassza ki **Connect** amikor a rendszer kéri. 
2. Megjelenik egy figyelmeztetés, amely az .rdp fájl közzétevője ismeretlen. Ez a várható eredmény. Az a **távoli asztali kapcsolat** ablakban válassza **Connect** folytatásához.
   
    ![Képernyőkép az ismeretlen közzétevőre vonatkozó figyelmeztetésről.](./media/connect-logon/rdp-warn.png)
3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Adja meg a hitelesítő adatokat egy fiók a virtuális gépen, majd **OK**.
   
     **Helyi fiók**: Ez általában a helyi fiók felhasználói nevét és a virtuális gép létrehozásakor megadott jelszót. Ebben az esetben a tartomány a virtuális gép neve, és a következő módon van megadva: *vmname*&#92;*username*.  
   
    **Tartományhoz csatlakoztatott virtuális gép**: Ha a virtuális Gépet egy tartományhoz tartozik, adja meg a felhasználónevet a következő formátumban *tartomány*&#92;*felhasználónév*. A fióknak szerepelnie kell a Rendszergazdák csoportban, vagy távoli hozzáférési jogosultságokkal kell rendelkeznie a virtuális géphez.
   
    **Tartományvezérlő**: Ha a virtuális gép egy tartományvezérlő, írja be a felhasználónevet és a egy tartományi rendszergazdai fiók jelszavát ahhoz a tartományhoz.
4. Válassza ki **Igen** a virtuális gép identitásának ellenőrzéséhez és a bejelentkezés befejezéséhez.
   
   ![Képernyőkép a virtuális gép identitásának ellenőrzésére vonatkozó üzenetről.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Ha a **Connect** gomb a portálon érhető el, és nem csatlakozik az Azure- [Express Route](../../expressroute/expressroute-introduction.md) vagy [Site-to-Site VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) -kapcsolatot kell létrehozni és a virtuális gép nyilvános IP-címet rendelni RDP használata előtt. További információkért lásd: [nyilvános IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 


## <a name="next-steps"></a>További lépések
Ha nehézségekbe csatlakozás, lásd: [távoli asztali kapcsolatainak hibaelhárításával](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

