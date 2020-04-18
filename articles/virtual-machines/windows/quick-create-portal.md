---
title: Rövid útmutató – Windows virtuális gép létrehozása az Azure Portalon
description: Ez a rövid útmutató a Windows rendszerű virtuális gépek az Azure Portallal történő létrehozását ismerteti.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a304e400d193eb4b2d0c2e8ec30ea03ea41977f8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606637"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Rövid útmutató: Windows rendszerű virtuális gép létrehozása az Azure Portalon

Az Azure-beli virtuális gépek (VM-ek) létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a virtuális gépek és a társított erőforrások létrehozásához. Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Portalon egy virtuális gép (VM) üzembe helyezéséhez az Azure-ban, amely windows Server 2019-et futtat. Ha látni szeretné a virtuális gépet feladatvégzés közben, hozzon létre RDP-kapcsolatot a virtuális géphez, és telepítse az IIS webkiszolgálót.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Írja be a **virtuális gépeket** a keresésbe.
1. A **Szolgáltatások csoportban**válassza a **Virtuális gépek**lehetőséget.
1. A **Virtuális gépek** lapon válassza a **Hozzáadás**lehetőséget. 
1. Az **Alapok** fül **Projektadatok** részén győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, és válassza az **Új létrehozása** lehetőséget az Erőforráscsoport részen. Írja be a nevet a *myResourceGroup* névmezőbe. 

    ![Új erőforráscsoport létrehozása saját virtuális géphez](./media/quick-create-portal/project-details.png)

1. A **Példány részletei**csoportban írja be a *myVM* parancsot a virtuális gép **nevéhez,** és válassza az USA keleti *régióját* a **régióhoz,** majd válassza a Windows *Server 2019 Datacenter* lehetőséget a **lemezképhez.** Hagyja meg a többi alapértelmezett értéket.

    ![Példány részletei szakasz](./media/quick-create-portal/instance-details.png)

1. A **Rendszergazdai fiók**, területen adjon meg egy felhasználónevet (pl. *azureuser*) és egy jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Felhasználónév és jelszó megadása](./media/quick-create-portal/administrator-account.png)

1. A **Bejövő portszabályok csoportban**válassza **a Kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** és a **HTTP (80)** elemet a legördülő menüből.

    ![Az RDP- és a HTTP-portok megnyitása](./media/quick-create-portal/inbound-port-rules.png)

1. Hagyja meg a többi alapértelmezett beállítást, és kattintson a **Felülvizsgálat + létrehozás** gombra a lap alján.

    ![Áttekintés és létrehozás](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel. Ezek az utasítások ismertetik, hogyan csatlakozhat a virtuális gépéhez egy Windows rendszerű gépről. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12).

1. Kattintson a virtuális gép áttekintő lapján a **Csatlakozás** gombra. 

    ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/quick-create-portal/portal-quick-start-9.png)
    
2. A Csatlakozás a **virtuális géphez** lapon tartsa meg az alapértelmezett beállításokat az IP-cím szerint való csatlakozáshoz a 3389-es porton keresztül, majd kattintson az **RDP-fájl letöltése gombra**.

2. Nyissa meg a letöltött RDP-fájlt, és kattintson a **Csatlakozás** gombra, amikor a rendszer erre kéri. 

3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet **localhost**\\*felhasználónévként,* írja be a virtuális géphez létrehozott jelszót, majd kattintson az **OK**gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolat létrehozásához kattintson az **Igen** vagy a **Folytatás** gombra.

## <a name="install-web-server"></a>A webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az IIS-webkiszolgálót. Nyisson meg egy PowerShell-parancssort a virtuális gépen, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha befejezte, zárja be a virtuális gép RDP-kapcsolatát.


## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

A portálon válassza ki a virtuális gép, és a virtuális gép áttekintésében a **Kattintásra másolása** gombra az IP-cím jobbra másolja, és illessze be egy böngésző lapra. Megnyílik az alapértelmezett IIS üdvözlőlap, amely a következőképp fog kinézni:

![Alapértelmezett IIS-webhely](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. 

Jelölje ki a virtuális gép erőforráscsoportját, majd válassza a **Törlés**lehetőséget. Erősítse meg az erőforráscsoport nevét az erőforrások törlésének befejezéséhez.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a követésével üzembe helyezett egy egyszerű virtuális gépet, megnyitott egy hálózati portot a webes forgalomnak, és telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)