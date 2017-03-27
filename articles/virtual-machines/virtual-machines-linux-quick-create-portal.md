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
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: bcfd830a5e2f39f36460990cae7e84b04d9a5fbb
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>Linux virtuális gép létrehozása az Azure Portal használatával

Az Azure virtuális gépek létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a virtuális gépek, valamint az összes kapcsolódó erőforrás létrehozásához és konfigurálásához. Ez a rövid útmutató részletesen bemutatja egy virtuális gép Azure Portalon történő létrehozását. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

A gyors üzembe helyezés elvégzéséhez egy SSH-kulcspárra lesz szüksége. Ha már rendelkezik SSH-kulcspárral, kihagyhatja ezt a lépést. Ha Windows-gépet használ, kövesse az [itt](./virtual-machines-linux-ssh-from-windows.md) található utasításokat. 

Futtassa ezt a parancsot egy Bash rendszerhéjból, és kövesse a képernyőn látható utasításokat. A parancs kimenete tartalmazza a nyilvános kulcsfájl fájlnevét. A fájl tartalmára szükség van a virtuális gép létrehozásakor.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Válassza a **Számítás** elemet az **Új** panelen, majd válassza az **Ubuntu Server 16.04 LTS** lehetőséget a **Számítás** panelről, és kattintson a **Létrehozás** gombra.

3. Töltse ki a virtuális gép **Alapvető beállítások** űrlapját. A **Hitelesítés típusa** résznél válassza az **SSH** lehetőséget. Az **SSH nyilvános kulcs** beillesztése közben ügyeljen rá, hogy eltávolítsa a kezdő vagy záró térközt. Hozzon létre egy új **Erőforráscsoportot**. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer létrehozza és együttesen kezeli az Azure-erőforrásokat. Amikor végzett, kattintson az **OK** gombra.

    ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/virtual-machine-quick-start/create-vm-portal-basic-blade.png)  

4. Válasszon méretet a virtuális gépnek, majd kattintson a **Kiválasztás** elemre. 

    ![Válasszon méretet a virtuális gépnek a portálpanelen](./media/virtual-machine-quick-start/create-vm-portal-size-blade.png)

5. A beállítások panelen válassza a **Felügyelt lemezek használata** területen az **Igen** lehetőséget, tartsa meg az alapértelmezéseket a többi beállításnál, majd kattintson az **OK** elemre.

6. Az összegzés lapon kattintson az **OK** elemre a virtuális gép üzembe helyezésének megkezdéséhez.

7. Az üzembe helyezési állapot figyeléséhez kattintson a virtuális gépre. A virtuális gép megtalálható az Azure Portal irányítópultján, illetve a bal oldali menüben a **Virtuális gépek** elemre kattintva. A virtuális gép létrehozása után az állapot **Üzembe helyezés** értékről **Fut** értékre módosul.

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy SSH-kapcsolatot a virtuális géppel.

1. Kattintson a **Csatlakozás** gombra a virtuális gép paneljén. A Csatlakozás gomb megjelenít egy SSH-kapcsolati karakterláncot, amely a virtuális géphez való csatlakozásra használható.

    ![Portál – 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. Futtassa az alábbi parancsot egy SSH-munkamenet létrehozásához. Cserélje le a kapcsolati karakterláncot azzal, amelyet az Azure Portalról másolt ki.

```bash 
ssh <replace with IP address>
```
## <a name="delete-virtual-machine"></a>Virtuális gép törlése

Ha már nincs rá szükség, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki az erőforráscsoportot a virtuális gép paneljéről és kattintson a **Törlés** elemre.

## <a name="next-steps"></a>Következő lépések

[Magas rendelkezésre állású virtuális gép létrehozása oktatóanyag](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[A virtuális gépek parancssori felületen való üzembe helyezését ismertető minták megismerése](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

