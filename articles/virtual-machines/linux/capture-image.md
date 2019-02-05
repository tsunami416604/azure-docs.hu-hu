---
title: Az Azure CLI használatával Azure-beli Linuxos virtuális gép lemezképének |} A Microsoft Docs
description: Készítsen lemezképet arról az olyan tömeges telepítésekhez az Azure CLI használatával egy Azure virtuális Gépen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 5022d765b5dfa4f1f973b7fb4370d5314bb887b8
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731937"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Hogyan hozhat létre virtuális gépet vagy virtuális merevlemez képe

<!-- generalize, image - extended version of the tutorial-->

Szeretne létrehozni egy virtuális gépet (VM) több példányát használja az Azure-ban, a rendszert tartalmazó virtuális Merevlemezt, vagy a virtuális gép lemezképének rögzítése. A központi telepítési lemezképek létrehozására kell személyes fiókadatot eltávolítása. A következő lépésekben egy meglévő virtuális gép megszüntetése, szabadítsa fel, és hozzon létre egy rendszerképet. Ez a rendszerkép használatával létrehozni a virtuális gépek keresztül bármely erőforráscsoport az előfizetésben.

Hozzon létre egy másolatot a meglévő Linuxos virtuális gép vagy hibakeresés céljából, vagy a helyszíni virtuális gépről egyéni Linux VHD feltöltése, lásd: [feltöltése és a egy Linux virtuális gép létrehozása egyéni rendszerképből](upload-vhd.md).  

Is **Packer** az egyéni konfiguráció létrehozásához. További információkért lásd: [Packer használatával Linux virtuálisgép-rendszerképek létrehozása az Azure-ban](build-image-with-packer.md).

Mielőtt létrehozná a lemezképet a következőkre lesz szüksége:

* A Resource Manager-alapú üzemi modellben létrehozott egy Azure virtuális Gépen, hogy használja a managed disks. Ha Ön még nem hozta létre Linux rendszerű virtuális gép, használhatja a [portál](quick-create-portal.md), a [Azure CLI-vel](quick-create-cli.md), vagy [Resource Manager-sablonok](create-ssh-secured-vm-from-template.md). Konfigurálja a virtuális Gépet igény szerint. Ha például [adatlemezek hozzáadása](add-disk.md), alkalmazza a frissítéseket és az alkalmazások telepítésére. 

* A legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és az Azure-fiókkal kell bejelentkeznie [az bejelentkezési](/cli/azure/reference-index#az-login).

## <a name="quick-commands"></a>Gyors parancsok

Ez a cikk egy egyszerűsített verziója, valamint a teszteléshez, kiértékelése, vagy az Azure-beli virtuális gépek megismerésére, lásd: [egy Azure virtuális gép egyéni rendszerképének létrehozása a parancssori felület használatával](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>1. lépés: A virtuális gép megszüntetése
A virtuális gép első számítógép-specifikus fájlok és adatok törlése az Azure-beli Virtuálisgép-ügynök használatával fogjuk megszüntetéséhez. Használja a `waagent` parancsot a `-deprovision+user` paraméter a forrás Linux rendszerű virtuális gép. További információk: [Azure Linux-ügynök – felhasználói útmutató](../extensions/agent-linux.md).

1. A Linux rendszerű virtuális egy SSH-ügyfél csatlakozhat.
2. Az SSH ablakban írja be a következő parancsot:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Csak egy virtuális Gépet, amely akkor lesz képként rögzítse a következő parancs futtatásával. Ez a parancs nem garantálja, hogy a lemezkép törlődik a bizalmas adatok, vagy alkalmas terjesztési. A `+user` paramétert is eltávolítja az utoljára kiépített felhasználói fiókot. Ne a virtuális gép felhasználói fiókjának hitelesítő adatait, használja csak `-deprovision`.
 
3. Adja meg **y** folytatásához. Hozzáadhat a `-force` paraméter a megerősítési lépés elkerülése érdekében.
4. A parancs befejeződése után adja meg a **kilépéshez** gombra kattintva zárja be az SSH-ügyfél.

## <a name="step-2-create-vm-image"></a>2. lépés: Virtuális gép rendszerképének létrehozása
Az Azure CLI segítségével jelölje meg a virtuális gép általánosként és a lemezképének rögzítése. A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők *myResourceGroup*, *myVnet*, és *myVM*.

1. Szabadítsa fel a virtuális Gépet, amely a – figyelmeztetés megszüntetésről [az vm deallocate](/cli/azure/vm). Az alábbi példa felszabadítja a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup*.
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Jelölje meg a virtuális gép általánosítottra az [az vm generalize](/cli/azure/vm). Az alábbi példa jelöli meg a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup* általánosként.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Hozzon létre egy rendszerképet a virtuális gép erőforrás- [az lemezkép létrehozása](/cli/azure/image#az-image-create). A következő példában létrehozunk egy nevű rendszerképet *myImage* az erőforráscsoport neve *myResourceGroup* nevű Virtuálisgép-erőforrást használ *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A kép a forrásoldali virtuális Géppel azonos erőforráscsoportban jön létre. A lemezkép alapján az adott előfizetéshez tartozó virtuális gépeket hozhat létre bármely erőforráscsoport. Felügyeleti szempontból érdemes létrehozni egy adott erőforráscsoporthoz a Virtuálisgép-erőforrások és a képek.
   >
   > Ha szeretné tárolni a rendszerkép zóna rugalmas tárolás, egy régióban, amely támogatja a létrehozásához szükséges [rendelkezésre állási zónák](../../availability-zones/az-overview.md) , és tartalmazzák a `--zone-resilient true` paraméter.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3. lépés: A rögzített lemezképet a virtuális gép létrehozása
Virtuális gép létrehozása a létrehozott lemezképpel [az virtuális gép létrehozása](/cli/azure/vm). A következő példában létrehozunk egy nevű virtuális Gépet *myVMDeployed* rendszerkép *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>A virtuális gép létrehozása egy másik erőforráscsoportban található 

Az adott előfizetéshez tartozó virtuális gépeket hozhat létre bármely erőforráscsoport-lemezképről. Virtuális gép létrehozása egy másik erőforráscsoportban található, mint a kép, adja meg a képre a teljes erőforrás-azonosítója. Használat [az képlista](/cli/azure/image#az-image-list) rendszerképek listájának megtekintéséhez. A kimenet a következő példához hasonló.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Az alábbi példában [az virtuális gép létrehozása](/cli/azure/vm#az-vm-create) a forrás lemezkép erőforráscsoportokban található virtuális gép létrehozásához adja meg a lemezkép-erőforrás azonosítója.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>4. lépés: Az üzemelő példány ellenőrzése

SSH-t a virtuális géphez, ellenőrizze a telepítési és elkezdheti az új virtuális Géphez létrehozott. SSH-n keresztül csatlakozni, keresse meg az IP-címét vagy teljes Tartománynevét, a virtuális gép [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>További lépések
A forrás Virtuálisgép-rendszerképről több virtuális gépet hozhat létre. A lemezkép módosíthatja: 

- Hozzon létre egy virtuális gép a lemezkép alapján.
- Győződjön meg a szükséges frissítések vagy konfigurációs módosítások.
- Kövesse a lépéseket újra megszüntetése, felszabadítása, generalize és hozzon létre egy rendszerképet.
- Az új rendszerkép használata a későbbiekben. Előfordulhat, hogy törli az eredeti képet.

A CLI-vel a virtuális gépek kezeléséről további információkért lásd: [Azure CLI-vel](/cli/azure).
