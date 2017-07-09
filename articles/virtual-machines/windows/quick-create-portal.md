---
title: "Azure gyors üzembe helyezés – Windows virtuális gép létrehozása a Portalon | Microsoft Docs"
description: "Azure gyors üzembe helyezés – Windows virtuális gép létrehozása a Portalon"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/03/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: fac46780cfd591a00ce73ea1bb4ce73893d22967
ms.contentlocale: hu-hu
ms.lasthandoff: 05/31/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Windows virtuális gép létrehozása az Azure Portallal

Az Azure virtuális gépek létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a virtuális gépek, valamint az összes kapcsolódó erőforrás létrehozásához és konfigurálásához. Ez a rövid útmutató részletesen bemutatja egy virtuális gép létrehozását és egy webkiszolgáló telepítését a virtuális gépen.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Válassza a **Számítás** elemet, majd a **Windows Server 2016 Datacenter** lehetőséget, és győződjön meg arról, hogy a kiválasztott üzemi modell az **Erőforrás-kezelő**. Kattintson a **Létrehozás** gombra. 

3. Adja meg a virtuális gép adatait. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. Amikor végzett, kattintson az **OK** gombra.

    ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

4. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. 

    ![Képernyőkép a virtuális gépek méreteivel](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

5. A beállítások panelen válassza a **Felügyelt lemezek használata** területen az **Igen** lehetőséget, tartsa meg az alapértelmezéseket a többi beállításnál, majd kattintson az **OK** elemre.

6. Az összegzés lapon kattintson az **OK** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

7. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejeztével a virtuális gép összefoglalás panelje automatikusan megnyílik.


## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

1. Kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságai között. A rendszer létrehoz és letölt egy Remote Desktop Protocol-fájlt (.rdp-fájlt).

    ![Portál – 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

3. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.


## <a name="install-iis-using-powershell"></a>Az IIS telepítése a PowerShell-lel

A virtuális gépen indítson el egy PowerShell-munkamenetet, majd futtassa a következő parancsot az IIS telepítéséhez.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha ezzel elkészült, lépjen ki az RDP-munkamenetből, és térjen vissza a virtuális gép tulajdonságaihoz az Azure Portalon.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára 

A hálózati biztonsági csoport (NSG) feladata a bejövő és kimenő forgalom védelme. Ha létrehoz egy virtuális gépet az Azure Portalon, a 3389-es porton létrejön egy bejövő szabály az RDP-kapcsolatok számára. Mivel ezen a virtuális gépen egy webkiszolgáló üzemel, a 80-as porthoz létre kell hoznia egy NSG-szabályt.

1. A virtuális gépen kattintson az **Erőforráscsoport** nevére.
2. Válassza ki a **hálózati biztonsági csoportot**. A hálózati biztonsági csoport a **Típus** oszlop segítségével azonosítható. 
3. A bal oldali menü Beállítások területén kattintson a **Bejövő biztonsági szabályok** elemre.
4. Kattintson a **Hozzáadás** gombra.
5. A **Név** mezőbe írja be a **http** karakterláncot. Ügyeljen rá, hogy a **Porttartomány** értéke 80, a **Művelet** értéke pedig **Engedélyezés** legyen. 
6. Kattintson az **OK** gombra.


## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

Most, hogy az IIS telepítve van, és a 80-as port meg van nyitva a virtuális gép felé, a webkiszolgáló elérhető az internetről. Nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. A nyilvános IP-cím az Azure Portal VM panelén található.

![Alapértelmezett IIS-webhely](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki az erőforráscsoportot a virtuális gép paneljéről és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, egy hálózati biztonsági csoport szabályát, valamint telepített egy webkiszolgálót. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)

