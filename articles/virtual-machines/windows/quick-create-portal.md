---
title: Rövid útmutató – Windows rendszerű virtuális gép létrehozása az Azure Portalon | Microsoft Docs
description: Ez a rövid útmutató a Windows rendszerű virtuális gépek az Azure Portallal történő létrehozását ismerteti.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/14/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2b9997fb1fe86d8ffdb67e74fc053773997fb113
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816480"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Rövid útmutató: Windows rendszerű virtuális gép létrehozása az Azure Portalon

Az Azure-beli virtuális gépek (VM-ek) létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a virtuális gépek és a társított erőforrások létrehozásához. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe az Azure Portal segítségével Windows Server 2016 rendszerű virtuális gépeket (VM-eket) az Azure-ban. A virtuális gép működésének ellenőrzéséhez ezután RDP-kapcsolaton keresztül csatlakozzon a géphez, és telepítse az IIS-webkiszolgálót.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Válassza az Azure Portal bal felső sarkában az **Erőforrás létrehozása** lehetőséget.

1. Az Azure Marketplace-erőforrások fölött lévő keresési mezőben keressen a **Windows Server 2016 Datacenter** elemre, és válassza ki, majd válassza a **Létrehozás** lehetőséget.

1. Az **Alapok** fül **Projektadatok** részén győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, és válassza az **Új létrehozása** lehetőséget az Erőforráscsoport részen. Nevezze el így: *myResourceGroup*. 

    ![Új erőforráscsoport létrehozása virtuális géphez](./media/quick-create-portal/project-details.png)

1. A **Példány részletei** területen írja a *myVM* nevet a **Virtuális gép neve** mezőbe, majd a **Régió** menüjéből válassza ki az *USA keleti régiója* lehetőséget. Hagyja változatlanul a többi alapértelmezett értéket.

    ![Példány részletei szakasz](./media/quick-create-portal/instance-details.png)

1. A **Rendszergazdai fiók**, területen adjon meg egy felhasználónevet (pl. *azureuser*) és egy jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Felhasználónév és jelszó megadása](./media/quick-create-portal/administrator-account.png)

1. A **Bejövőport-szabályok** területen válassza a **Kijelölt portok engedélyezése**lehetőséget, majd válassza az **RDP (3389)** és a **HTTP** elemeket a legördülő listából.

    ![RDP- és a HTTP-portok megnyitása](./media/quick-create-portal/inbound-port-rules.png)

1. Hagyja változatlanul a többi alapértelmezett beállítást, és kattintson a **Áttekintés + létrehozás** gombra a lap alján.

    ![Áttekintés és létrehozás](./media/quick-create-portal/review-create.png)


## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel. Ezek az utasítások ismertetik, hogyan csatlakozhat a virtuális gépéhez egy Windows rendszerű gépről. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

1. Kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságainak lapján. 

    ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/quick-create-portal/portal-quick-start-9.png)
    
2. A **Csatlakozás virtuális géphez** oldalon tartsa meg az alapértelmezett beállításokat a DNS-név alapján a 3389-es porton keresztül való csatlakozáshoz, és kattintson az **RDP-fájl letöltése** parancsra.

2. Nyissa meg a letöltött RDP-fájlt, és kattintson a **Csatlakozás** gombra, amikor a rendszer erre kéri. 

3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet **localhost**\\*felhasználónév* formátumban, és adja meg a virtuális géphez létrehozott jelszót, majd kattintson az **OK** gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolat létrehozásához kattintson az **Igen** vagy **Folytatás** gombra.

## <a name="install-web-server"></a>Webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az IIS-webkiszolgálót. Nyisson meg egy PowerShell-parancssort a virtuális gépen, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha elkészült, zárja be a virtuális géphez nyitott RDP-kapcsolatot.


## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

A portálon válassza ki a virtuális gépet, majd a virtuális gép áttekintési területén használja a **Kattintson a másoláshoz** gombot az IP-cím jobb oldalán annak másolásához és a böngészőlapra való beillesztéshez. Megnyílik az alapértelmezett IIS-kezdőlap, amelynek így kell kinéznie:

![Alapértelmezett IIS-webhely](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a **Törlés** elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, megnyitott egy hálózati portot a webes forgalom számára, valamint telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
