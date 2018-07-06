---
title: Az Azure CLI 2.0 használatával Linux virtuális gép lemezképének |} A Microsoft Docs
description: Készítsen lemezképet arról az olyan tömeges telepítésekhez az Azure CLI 2.0 használatával egy Azure virtuális Gépen.
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
ms.date: 03/22/2018
ms.author: cynthn
ms.openlocfilehash: ea202cad06130cfaaa134cad94ac08bede2f41a9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867701"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Hogyan hozhat létre virtuális gépet vagy virtuális merevlemez képe

<!-- generalize, image - extended version of the tutorial-->

Egy virtuális gépet (VM) az Azure-ban több példányának létrehozása, a virtuális gép vagy a rendszer virtuális Merevlemeze lemezkép rögzítése. Hozzon létre egy rendszerképet, a személyes fiók adatait, amely biztonságosabbá teszi az üzembe helyezése több alkalommal kell eltávolításakor. A következő lépések azt egy meglévő virtuális gép megszüntetése, felszabadítása és hozzon létre egy rendszerképet. Ez a rendszerkép használatával létrehozni a virtuális gépek keresztül bármely erőforráscsoport az előfizetésben.

Ha azt szeretné, hozzon létre egy másolatot a meglévő Linux virtuális gép biztonsági mentése és a hibakeresés, vagy a helyszíni virtuális gépről egyéni Linux VHD feltöltése, lásd: [feltöltése és a egy Linux virtuális gép létrehozása egyéni rendszerképből](upload-vhd.md).  

Is **Packer** az egyéni konfiguráció létrehozásához. Packer használatával kapcsolatos további információkért lásd: [Packer használatával Linux virtuálisgép-rendszerképek létrehozása az Azure-ban](build-image-with-packer.md).


## <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy megfelel-e a következő előfeltételek vonatkoznak:

* Szüksége lesz egy Azure virtuális gép létrehozása a Resource Manager-alapú üzemi modellben, felügyelt lemezeket használ. Ha még nem hozott létre egy Linux rendszerű virtuális gép, használhatja a [portál](quick-create-portal.md), a [Azure CLI-vel](quick-create-cli.md), vagy [Resource Manager-sablonok](create-ssh-secured-vm-from-template.md). Konfigurálja a virtuális Gépet igény szerint. Ha például [adatlemezek hozzáadása](add-disk.md), alkalmazza a frissítéseket és az alkalmazások telepítésére. 

* Szükség van a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve van, és a egy Azure-fiók használatával kell jelentkeznie [az bejelentkezési](/cli/azure/reference-index#az_login).

## <a name="quick-commands"></a>Gyors parancsok

Ez a témakör a vizsgálat, egyszerűsített változata kiértékelése vagy megismerése az Azure-beli virtuális gépek esetén lásd: [hozzon létre egy egyéni rendszerképet egy Azure virtuális gépek, a parancssori felületről](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>1. lépés: A virtuális gép megszüntetése
A virtuális gép adott fájlokat és adatokat törli az Azure-beli Virtuálisgép-ügynök használatával, megszüntetése. Használja a `waagent` parancsot a *-megszüntetési + felhasználó* paraméter a forrás Linux rendszerű virtuális gép. További információk: [Azure Linux-ügynök – felhasználói útmutató](../extensions/agent-linux.md).

1. A Linux virtuális gép egy SSH-ügyfél csatlakozhat.
2. Az SSH ablakban írja be a következő parancsot:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Csak egy virtuális gép kívánt képként rögzítse a következő parancs futtatásával. Nem garantálja, hogy a lemezkép törlődik a bizalmas adatok, vagy alkalmas terjesztési. A *+ felhasználó* paramétert is eltávolítja az utoljára kiépített felhasználói fiókot. Ha azt szeretné, hogy a fiók hitelesítő adatait a virtuális gépen, használja *-megszüntetési* hagyja a felhasználói fiók helye.
 
3. Típus **y** folytatásához. Adhat hozzá a **-force** paraméter a megerősítési lépés elkerülése érdekében.
4. A parancs befejeződése után írja be a **kilépéshez**. Ebben a lépésben az SSH-ügyfél bezárása.

## <a name="step-2-create-vm-image"></a>2. lépés: A virtuális gép rendszerképének létrehozása
Az Azure CLI 2.0 használatával a virtuális gép megjelölése általánosként és a lemezképének rögzítése. A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa a paraméter nevek a következők *myResourceGroup*, *myVnet*, és *myVM*.

1. Szabadítsa fel a virtuális Gépet, amely a – figyelmeztetés megszüntetésről [az vm deallocate](/cli//azure/vm#deallocate). Az alábbi példa felszabadítja a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Jelölje meg a virtuális gép általánosítottra az [az vm generalize](/cli//azure/vm#generalize). Az alábbi példa jelöli meg a virtuális gép nevű *myVM* az erőforráscsoport neve *myResourceGroup* általánosként:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Most hozzon létre egy rendszerképet a virtuális gép erőforrás- [az lemezkép létrehozása](/cli/azure/image#az_image_create). A következő példában létrehozunk egy nevű rendszerképet *myImage* az erőforráscsoport neve *myResourceGroup* nevű Virtuálisgép-erőforrást használ *myVM*:
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A kép a forrásoldali virtuális Géppel azonos erőforráscsoportban jön létre. A lemezkép alapján az adott előfizetéshez tartozó virtuális gépeket hozhat létre bármely erőforráscsoport. Felügyeleti szempontból érdemes létrehozni egy adott erőforráscsoporthoz a Virtuálisgép-erőforrások és a képek.
   >
   > Ha szeretné tárolni a rendszerkép zóna rugalmas tárolás, egy régióban, amely támogatja a létrehozásához szükséges [rendelkezésre állási zónák](../../availability-zones/az-overview.md) , és tartalmazzák a `--zone-resilient true` paraméter.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3. lépés: Virtuális gép létrehozása a rögzített lemezképből
Hozzon létre egy virtuális Gépet a létrehozott rendszerkép használatával [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). A következő példában létrehozunk egy nevű virtuális Gépet *myVMDeployed* rendszerkép *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>A virtuális gép létrehozása egy másik erőforráscsoportban található 

Az adott előfizetéshez tartozó virtuális gépeket hozhat létre bármely erőforráscsoport-lemezképről. Virtuális gép létrehozása egy másik erőforráscsoportban található, mint a kép, adja meg a képre a teljes erőforrás-azonosítója. Használat [az képlista](/cli/azure/image#az_image_list) rendszerképek listájának megtekintéséhez. A kimenet a következő példához hasonló:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Az alábbi példában [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) egy másik erőforráscsoportban található, mint a forrás lemezkép virtuális gép létrehozása a képerőforrás-azonosító megadásával:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>4. lépés: A telepítés ellenőrzése

Most már ssh-KAPCSOLATOT a virtuális géphez, ellenőrizze a telepítési és elkezdheti az új virtuális Géphez létrehozott. SSH-n keresztül csatlakozni, keresse meg az IP-címét vagy teljes Tartománynevét, a virtuális gép [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>További lépések
A forrás Virtuálisgép-rendszerképről több virtuális gépet hozhat létre. Ha módosítania kell a lemezkép: 

- Hozzon létre egy virtuális gép a lemezkép alapján.
- Győződjön meg a szükséges frissítések vagy konfigurációs módosítások.
- Kövesse a lépéseket újra megszüntetése, felszabadítása, generalize és hozzon létre egy rendszerképet.
- Az új rendszerkép használata a későbbiekben. Ha szükséges, törölje az eredeti képet.

A CLI-vel a virtuális gépek kezeléséről további információkért lásd: [Azure CLI 2.0](/cli/azure).
