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
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 3cb94144addabf2371c8a058cebff25def4b6338
ms.lasthandoff: 04/04/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Windows virtuális gép létrehozása az Azure Portallal

Az Azure virtuális gépek létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a virtuális gépek, valamint az összes kapcsolódó erőforrás létrehozásához és konfigurálásához. Ez a rövid útmutató részletesen bemutatja egy virtuális gép Azure Portalon történő létrehozását. Az üzembe helyezés végeztével csatlakozunk a kiszolgálóhoz, és telepítjük az IIS-t.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

2. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

3. Válassza a **Számítás** elemet az **Új** panelen, majd válassza a **Windows Server 2016 Datacenter** lehetőséget a **Számítás** panelről, és kattintson a **Létrehozás** gombra.

4. Töltse ki a virtuális gép **Alapvető beállítások** űrlapját. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. Hozzon létre egy új **Erőforráscsoportot**. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer létrehozza és együttesen kezeli az Azure-erőforrásokat. Amikor végzett, kattintson az **OK** gombra.

    ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

5. Válasszon méretet a virtuális gépnek, majd kattintson a **Kiválasztás** elemre.

6. A beállítások panelen válassza a **Felügyelt lemezek használata** területen az **Igen** lehetőséget, tartsa meg az alapértelmezéseket a többi beállításnál, majd kattintson az **OK** elemre.

7. Az összegzés lapon kattintson az **OK** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

8. Az üzembe helyezési állapot figyeléséhez kattintson a virtuális gépre. A virtuális gép megtalálható az Azure Portal irányítópultján, illetve a bal oldali menüben a **Virtuális gépek** elemre kattintva. A virtuális gép létrehozása után az állapot **Üzembe helyezés** értékről **Fut** értékre módosul.

## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára 

Az IIS-adatforgalom engedélyezéséhez meg kell nyitnia a 80-as portot a webes forgalom számára. Ez a lépés végigvezeti a hálózati biztonsági csoportokra (NSG) vonatkozó szabály létrehozásának folyamatán, amellyel engedélyezheti a bejövő kapcsolatokat a 80-as porton.

1. A virtuális gép paneljén, az **Alapvető erőforrások** szakaszban kattintson az **Erőforráscsoport** nevére.
2. Az erőforráscsoport paneljén kattintson a **Hálózati biztonsági csoport** elemre az erőforrások listájában. Az NSG neve a virtuális gép -nsg utótaggal bővített neve legyen.
3. Kattintson a **Bejövő biztonsági szabály** fejlécre a bejövő szabályok listájának megnyitásához. A listában látnia kell egy, az RDP-re vonatkozó szabályt.
4. Kattintson a **+ Hozzáadás** gombra a **Bejövő biztonsági szabály felvétele** panel megnyitásához.
5. A **Név** mezőbe írja be az **IIS** karakterláncot, gondoskodjon róla, hogy a **Porttartomány** értéke 80, a **Művelet** értéke pedig **Engedélyezés** legyen, majd kattintson az **OK** gombra.


## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

1. Kattintson a **Csatlakozás** gombra a virtuális gép paneljén. A rendszer létrehoz és letölt egy Remote Desktop Protocol-fájlt (.rdp-fájlt).

    ![Portál – 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

3. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.


## <a name="install-iis-using-powershell"></a>Az IIS telepítése a PowerShell-lel

Miután bejelentkezett az Azure-beli virtuális gépre, egyetlen PowerShell-utasítással telepítheti az IIS-t, és engedélyezheti, hogy a helyi tűzfalszabály átengedje a webforgalmat.  Nyisson meg egy PowerShell-parancssort, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

Miután az IIS telepítve lett, és a 80-as port meg van nyitva a virtuális gépen az internet irányából, egy tetszőleges böngésző használatával megtekintheti az alapértelmezett IIS-kezdőlapot. Kérje le a **nyilvános IP-címet** a virtuális gép paneljéről, majd annak használatával nyissa meg az alapértelmezett weblapot. 

![Alapértelmezett IIS-webhely](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Virtuális gép törlése

Ha már nincs rá szükség, az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="delete-virtual-machine"></a>Virtuális gép törlése

Ha már nincs rá szükség, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki az erőforráscsoportot a virtuális gép paneljéről és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>Következő lépések

[Szerepkörök telepítését és a tűzfal konfigurálását ismertető oktatóanyag](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[A virtuális gépek parancssori felületen való üzembe helyezését ismertető minták megismerése](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

