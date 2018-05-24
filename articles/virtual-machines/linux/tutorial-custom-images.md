---
title: Oktatóanyag – Egyéni virtuálisgép-rendszerképek létrehozása az Azure CLI használatával | Microsoft Docs
description: Ez az oktatóanyag ismerteti, hogyan használja az Azure CLI 2.0-t egyéni virtuálisgép-rendszerképek az Azure-ban történő létrehozásához.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 766e247775e61d7427b658b66948aa6699a7241a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli-20"></a>Oktatóanyag: Azure-beli virtuális gép egyéni rendszerképének létrehozása az Azure CLI 2.0 használatával

Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. Ebben az oktatóanyagban létrehoz egy egyéni rendszerképet egy Azure-beli virtuális gépről. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Virtuális gépek megszüntetése és általánosítása
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egyéni rendszerképből
> * Az előfizetésben lévő összes rendszerkép listázása
> * Rendszerkép törlése

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amely segítségével új virtuálisgép-példányokat hozhat létre.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Amennyiben szükséges, [ezzel a mintaszkripttel](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) létrehozhat egyet. Az oktatóanyag elvégzése során cserélje le az erőforráscsoportok és a virtuális gépek neveit, ahol szükséges.

## <a name="create-a-custom-image"></a>Egyéni lemezkép létrehozása

Egy virtuális gépről készült rendszerkép létrehozásához először elő kell készítenie a virtuális gépet. Ehhez meg kell szüntetni, fel kell szabadítani, majd általánosítottként kell megjelölni a forrásként szolgáló virtuális gépet. Miután előkészítette a virtuális gépet, létrehozhat egy rendszerképet.

### <a name="deprovision-the-vm"></a>A virtuális gép megszüntetése 

A megszüntetés a gépspecifikus információk eltávolításával általánosítja a virtuális gépet. Ez az általánosítás teszi lehetővé, hogy több virtuális gépet helyezzen üzembe egyetlen rendszerképből. A megszüntetés során a gazdanév visszaáll a *localhost.localdomain* értékre. A rendszer törli az SSH-gazdakulcsokat, a névkiszolgáló-konfigurációkat, a rendszergazdai szintű jelszót és a gyorsítótárazott DHCP-bérleteket is.

A virtuális gép megszüntetéséhez használja az azure-os virtuálisgép-ügynököt (waagent). Az azure-os virtuálisgép-ügynök telepítve van a virtuális gépen, és felügyeli a kiépítést, valamint az Azure Fabric Controllerrel való kommunikációt. További információk: [Azure Linux-ügynök – felhasználói útmutató](../extensions/agent-linux.md).

Csatlakozzon SSH-val a virtuális géphez, és futtassa az alábbi parancsot a virtuális gép megszüntetéséhez. A `+user` argumentummal a rendszer az utoljára kiépített felhasználói fiókot és az ahhoz társított adatokat is törli. Cserélje le a példában szereplő IP-címet a virtuális gépe nyilvános IP-címére.

Hozzon létre SSH–kapcsolatot a virtuális géppel.
```bash
ssh azureuser@52.174.34.95
```
Szüntesse meg a virtuális gépet.

```bash
sudo waagent -deprovision+user -force
```
Zárja be az SSH-munkamenetet.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Virtuális gép felszabadítása és megjelölése általánosként

Rendszerkép létrehozásához fel kell szabadítani a virtuális gépet. Szabadítsa fel a virtuális gépet az [az vm deallocate](/cli//azure/vm#deallocate) paranccsal. 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Végezetül állítsa a virtuális gép állapotát általánosítottra az [az vm generalize](/cli//azure/vm#generalize) paranccsal, hogy az Azure platform meg tudja állapítani, hogy a virtuális gép általánosítva lett. Rendszerképet csak általánosított virtuális gépből hozhat létre.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>A rendszerkép létrehozása

Most létrehozhat egy rendszerképet a virtuális gépből az [az image create](/cli//azure/image#create) paranccsal. Az alábbi példa létrehoz egy *myImage* nevű rendszerképet a *myVM* nevű virtuális gépből.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Virtuális gépek létrehozása a rendszerképből

Most, hogy már van egy rendszerképe, az [az vm create](/cli/azure/vm#az_vm_create) paranccsal létrehozhat belőle egy vagy több új virtuális gépet. Az alábbi példa létrehoz egy *myVMfromImage* nevű virtuális gépet a *myImage* rendszerkép alapján.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>Rendszerkép kezelése 

Az alábbiakban felsorolunk néhány gyakori rendszerkép-kezelési feladatot, továbbá ismertetjük, hogyan lehet elvégezni ezeket az Azure CLI használatával.

Az összes rendszerkép felsorolása táblázatos formátumban.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Rendszerkép törlése. Ez a példa törli a *myOldImage* nevű rendszerképet a *myResourceGroup* erőforráscsoportból.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy egyéni virtuálisgép-rendszerképet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális gépek megszüntetése és általánosítása
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egyéni rendszerképből
> * Az előfizetésben lévő összes rendszerkép listázása
> * Rendszerkép törlése

A következő oktatóanyagban a magas rendelkezésre állású virtuális gépeket ismerheti meg.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)

