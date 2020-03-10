---
title: Rövid útmutató – Linux rendszerű virtuális gép létrehozása a Azure Portal
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Linux rendszerű virtuális gépet a Azure Portal használatával.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/05/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 894fa2f3cda798a409db3ee8e9761c1702baf955
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372491"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-the-azure-portal"></a>Rövid útmutató: Linux rendszerű virtuális gép létrehozása az Azure Portalon

Az Azure-beli virtuális gépek (VM-ek) létrehozhatók az Azure Portal segítségével. Az Azure Portal egy böngészőalapú felhasználói felület az Azure-erőforrások létrehozásához. Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Portal az Ubuntu 18,04 LTS-t futtató linuxos virtuális gép (VM) üzembe helyezéséhez. A virtuális gép működésének megtekintéséhez hozzon létre SSH-kapcsolatot a virtuális géppel, és telepítse az NGINX-webkiszolgálót.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

A rövid útmutató elvégzéséhez egy SSH-kulcspárra lesz szüksége. Ha már rendelkezésére áll egy SSH-kulcspár, kihagyhatja ezt a lépést.

SSH-kulcspár létrehozásához nyissa meg a Bash-felületet, és használja az [ssh-keygen](https://www.ssh.com/ssh/keygen/) eszközt. Ha nincs Bash-felület a helyi számítógépén, használhatja az [Azure Cloud Shellt](https://shell.azure.com/bash) is.


1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Az oldal tetején lévő menüben válassza a `>_` ikont a Cloud Shell megnyitásához.
1. Győződjön meg arról, hogy a Cloudshellben a bal felső sarokban található **bash** . Ha a PowerShell-t mondja, a legördülő menüből válassza a **bash** elemet, és válassza a **megerősítés** lehetőséget a bash rendszerhéjra való váltáshoz.
1. Az SSH-kulcs létrehozásához írja be a `ssh-keygen -t rsa -b 2048`. 
1. A rendszer kérni fogja, hogy adjon meg egy fájlt, amelybe menteni szeretné a kulcspárt. Csak nyomja le az **ENTER** billentyűt az alapértelmezett helyen való mentéshez, zárójelek között. 
1. A rendszer megkéri, hogy adjon meg egy jelszót. Megadhatja az SSH-kulcshoz tartozó jelszót, vagy megnyomhatja az **ENTER** billentyűt a jelszó nélküli folytatáshoz.
1. Az `ssh-keygen` parancs nyilvános és titkos kulcsokat hoz létre a `~/.ssh directory``id_rsa` alapértelmezett nevével. A parancs visszaadja a nyilvános kulcs teljes útvonalát. A nyilvános kulcs elérési útját használva jelenítse meg a tartalmát a `cat` `cat ~/.ssh/id_rsa.pub`beírásával.
1. Másolja ki a parancs kimenetét, és mentse valahova a cikk későbbi részében való használatra. Ez az Ön nyilvános kulcsa, és szüksége lesz rá, amikor konfigurálja a rendszergazdai fiókot a virtuális gépre való bejelentkezéshez.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha még nem tette meg, jelentkezzen be a [Azure Portalba](https://portal.azure.com) .

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Írja be a **virtuális gépeket** a keresésbe.
1. A **szolgáltatások**területen válassza a **virtuális gépek**lehetőséget.
1. A **virtuális gépek** lapon válassza a **Hozzáadás**lehetőséget. Megnyílik a **virtuális gép létrehozása** lap.
1. Az **Alapok** fül **Projektadatok** részén győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, és válassza az **Új létrehozása** lehetőséget az Erőforráscsoport részen. A név mezőbe írja be a következőt: *myResourceGroup* . *. 

    ![Új erőforráscsoport létrehozása virtuális géphez](./media/quick-create-portal/project-details.png)

1. A **példány részletei**területen írja be a myVM **nevet a virtuális gép neveként**, válassza az *USA keleti* **régiója**lehetőséget, és válassza az *Ubuntu 18,04 LTS* lehetőséget a **rendszerképhez**. Hagyja változatlanul a többi alapértelmezett értéket.

    ![Példány részletei szakasz](./media/quick-create-portal/instance-details.png)

1. A **rendszergazdai fiók**területen válassza a **nyilvános SSH-kulcs**elemet, írja be a felhasználónevét, majd illessze be a nyilvános kulcsát. Távolítsa el a kezdő vagy záró szóközöket a nyilvános kulcsból.

    ![Rendszergazdai fiók](./media/quick-create-portal/administrator-account.png)

1. A **Bejövőport-szabályok** > **Nyilvános bejövő portok** részen válassza a **Kijelölt portok engedélyezése** lehetőséget, majd az **SSH (22)** és a **HTTP (80)** elemet a legördülő listából. 

    ![RDP- és a HTTP-portok megnyitása](./media/quick-create-portal/inbound-port-rules.png)

1. Hagyja változatlanul a többi alapértelmezett beállítást, és kattintson a **Áttekintés + létrehozás** gombra a lap alján.

1. A **Virtuális gép létrehozása** lapon láthatja a létrehozandó virtuális gép részleteit. Ha készen áll, kattintson a **Létrehozás** gombra.

A virtuális gép üzembe helyezése eltarthat néhány percig. Az üzembe helyezés végeztével lépjen tovább a következő szakaszra.

    
## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Hozzon léte egy SSH-kapcsolatot a virtuális géppel.

1. A virtuális gép áttekintő oldalán kattintson a **Csatlakozás** gombra. 

    ![Portál – 9](./media/quick-create-portal/portal-quick-start-9.png)

2. A **Csatlakozás a virtuális géphez** oldalon tartsa meg az alapértelmezett beállításokat az IP-címmel való csatlakozáshoz a 22-es porton keresztül. A **Bejelentkezés a virtuális gép helyi fiókjával** területen egy csatlakozási parancs jelenik meg. A parancs másolásához kattintson a gombra. Az SSH-kapcsolat parancsa az alábbi példához hasonlóan néz ki:

    ```bash
    ssh azureuser@10.111.12.123
    ```

3. Ugyanazzal a bash-rendszerhéjsal, amelyet az SSH-kulcspár létrehozásához használt (újra megnyithatja a Cloud Shell a `>_` újbóli kiválasztásával vagy a https://shell.azure.com/bash)ához, illessze be az SSH-kapcsolati parancsot a rendszerhéjba egy SSH-munkamenet létrehozásához.

## <a name="install-web-server"></a>Webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az NGINX-webkiszolgálót. Frissítse a csomag forrásait, majd telepítse a legújabb NGINX-csomagot az SSH-munkamenetből.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Ha elkészült, az SSH-munkamenetből való kilépéshez írja be a következőt: `exit`.


## <a name="view-the-web-server-in-action"></a>A webkiszolgáló megtekintése működés közben

Egy tetszőleges böngésző használatával megtekintheti az alapértelmezett NGINX-kezdőlapot. Adja meg a virtuális gép nyilvános IP-címét a webes címként. A nyilvános IP-cím a virtuális gép áttekintő oldalán található, vagy a korábban használt SSH-kapcsolati sztring részeként.

![Alapértelmezett NGINX-webhely](./media/quick-create-portal/nginx.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a **Törlés** elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, létrehozott egy hálózati biztonsági csoportot és szabályt, valamint telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)
