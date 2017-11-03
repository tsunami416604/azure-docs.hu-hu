---
title: "Azure gyors üzembe helyezés – Virtuális gép létrehozása – Portal | Microsoft Docs"
description: "Azure gyors üzembe helyezés – Virtuális gép létrehozása – Portal"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 15d4f81f8a7a835ea61a9056d83d8337d529c7cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Linux virtuális gép létrehozása az Azure Portal használatával

Az Azure virtuális gépek létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a virtuális gépek, valamint az összes kapcsolódó erőforrás létrehozásához és konfigurálásához. Ez a rövid útmutató részletesen bemutatja egy virtuális gép létrehozását és egy webkiszolgáló telepítését a virtuális gépen.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

A gyors üzembe helyezés elvégzéséhez egy SSH-kulcspárra lesz szüksége. Ha már rendelkezik SSH-kulcspárral, kihagyhatja ezt a lépést.

Futtassa ezt a parancsot egy Bash rendszerhéjból, és kövesse a képernyőn látható utasításokat. A parancs kimenete tartalmazza a nyilvános kulcsfájl fájlnevét. Másolja a nyilvános kulcsfájl tartalmát a vágólapra.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet. 

3. Adja meg a virtuális gép adatait. A **Hitelesítés típusa** résznél válassza az **SSH nyilvános kulcs** lehetőséget. Az SSH nyilvános kulcs beillesztése közben ügyeljen rá, hogy eltávolítsa a kezdő vagy záró térközt. Amikor végzett, kattintson az **OK** gombra.

    ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/quick-create-portal/create-vm-portal-basic-blade.png)

4. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. 

    ![Képernyőkép a virtuális gépek méreteivel](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. A **Beállítások** területen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

6. Az összegzés lapon kattintson az **OK** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

7. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejeztével a virtuális gép összefoglalás panelje automatikusan megnyílik.


## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Hozzon léte egy SSH-kapcsolatot a virtuális géppel.

1. Kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságai között. A Csatlakozás gomb megjelenít egy SSH-kapcsolati karakterláncot, amely a virtuális géphez való csatlakozásra használható.

    ![Portál – 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. Futtassa az alábbi parancsot egy SSH-munkamenet létrehozásához. Cserélje le a kapcsolati karakterláncot azzal, amelyet az Azure Portalról másolt ki.

```bash 
ssh azureuser@40.112.21.50
```

## <a name="install-nginx"></a>Az NGINX telepítése

Az alábbi bash-parancsfájl segítségével frissítse a csomagforrásokat, és telepítse a legújabb NGINX-csomagot. 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Ha ezzel elkészült, lépjen ki az SSH-munkamenetből, és térjen vissza a virtuális gép tulajdonságaihoz az Azure Portalon.


## <a name="open-port-80-for-web-traffic"></a>A 80-as port megnyitása a webes adatforgalom számára 

A hálózati biztonsági csoport (NSG) feladata a bejövő és kimenő forgalom védelme. Ha létrehoz egy virtuális gépet az Azure Portalon, a 22-es porton létrejön egy bejövő szabály az SSH-kapcsolatok számára. Mivel ezen a virtuális gépen egy webkiszolgáló üzemel, a 80-as porthoz létre kell hoznia egy NSG-szabályt.

1. A virtuális gépen kattintson az **Erőforráscsoport** nevére.
2. Válassza ki a **hálózati biztonsági csoportot**. A hálózati biztonsági csoport a **Típus** oszlop segítségével azonosítható. 
3. A bal oldali menü Beállítások területén kattintson a **Bejövő biztonsági szabályok** elemre.
4. Kattintson a **Hozzáadás** gombra.
5. A **Név** mezőbe írja be a **http** karakterláncot. Ügyeljen rá, hogy a **Porttartomány** értéke 80, a **Művelet** értéke pedig **Engedélyezés** legyen. 
6. Kattintson az **OK** gombra.


## <a name="view-the-nginx-welcome-page"></a>Az NGINX kezdőlapjának megtekintése

Most, hogy az NGINX telepítve van, és a 80-as port meg van nyitva a virtuális gép felé, a webkiszolgáló elérhető az internetről. Nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános IP-címét. A nyilvános IP-címet a virtuális gép tulajdonságai, az Azure portálon található.

![Alapértelmezett NGINX-webhely](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez jelölje ki az erőforráscsoportot, a virtuális géphez, majd kattintson **törlése**.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, egy hálózati biztonsági csoport szabályát, valamint telepített egy webkiszolgálót. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)
