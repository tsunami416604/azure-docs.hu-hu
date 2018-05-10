---
title: Rögzítsen egy rendszerképet a Linux virtuális gépek az Azure-ban a parancssori felület 2.0 |} Microsoft Docs
description: Rögzítsen egy rendszerképet az Azure virtuális gép tömeges üzembe helyezése az Azure CLI 2.0 használandó.
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
ms.openlocfilehash: bb70b3ff84392797ce0d93b8cf5d4018ff8ebdd8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Egy képet egy virtuális géphez vagy virtuális merevlemez létrehozása

<!-- generalize, image - extended version of the tutorial-->

Szeretne létrehozni egy virtuális gép (VM) Azure-ban történő használatáról több példányát, a virtuális Gépet vagy virtuális merevlemez operációs rendszer lemezképének rögzítése. A képfájl létrehozásához személyes fiók adatait, amely biztonságosabbá teszi telepítése többször kell eltávolítani. A következő lépések, meglévő virtuális kiosztásának megszüntetése, felszabadítani és lemezkép létrehozása. Ez a rendszerkép hozhat létre a virtuális gépek közötti bármely erőforráscsoport az előfizetésen belül.

Ha azt szeretné, hozzon létre egy másolatot a meglévő Linux virtuális gép biztonsági mentését vagy hibakeresés, vagy egy helyszíni virtuális gép speciális Linux virtuális merevlemez feltöltése, lásd: [feltöltése és a Linux virtuális gép létrehozása az egyéni lemezképet](upload-vhd.md).  

Is **csomagoló** az egyéni konfiguráció létrehozásához. Csomagoló használatával kapcsolatos további információkért lásd: [csomagoló használata a Linux virtuális gép képek létrehozása az Azure-ban](build-image-with-packer.md).


## <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy teljesülnek-e a következő előfeltételek teljesülését:

* Egy Azure virtuális gép létrehozása a Resource Manager üzembe helyezési modellel felügyelt lemezekkel van szüksége. Ha még nem hozott létre egy Linux virtuális Gépet, használhatja a [portal](quick-create-portal.md), a [Azure CLI](quick-create-cli.md), vagy [Resource Manager-sablonok](create-ssh-secured-vm-from-template.md). Igény szerint konfigurálhatja a virtuális Gépet. Például [adatok lemezek hozzáadása a](add-disk.md), frissítések és alkalmazások telepítéséhez. 

* Szükség van a legújabb [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és az Azure-fiók használatával kell bejelentkezett [az bejelentkezési](/cli/azure/reference-index#az_login).

## <a name="quick-commands"></a>Gyors parancsok

Egy egyszerűsített verziója, tesztelési, ez a témakör értékelése és megismerése az virtuális gépeket az Azure-ban, olvassa el [hozzon létre egy Azure virtuális gép a parancssori felület használatával egyéni lemezkép](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>1. lépés: A virtuális gép kiosztásának megszüntetése
Ön kiosztásának megszüntetése a virtuális gépet az Azure Virtuálisgép-ügynök, a gép adott fájlokat és adatokat törli. Használja a `waagent` parancsot a *-deprovision + felhasználói* paraméter a Linux virtuális gép – forrásként. További információk: [Azure Linux-ügynök – felhasználói útmutató](../extensions/agent-linux.md).

1. Csatlakoztassa a Linux virtuális gép SSH-ügyfél.
2. Az SSH-ablakban írja be a következő parancsot:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Ez a parancs csak egy virtuális gép rögzítése képként kívánt futtatható. Nem biztos, hogy a kép minden a bizalmas adatok megszűnik, vagy alkalmas terjesztését. A *+ felhasználói* paraméter is eltávolítja az utolsó kiépített felhasználói fiókot. Ha meg szeretné tartani a virtuális gép fiók hitelesítő adatait, használja *-deprovision* a felhasználói fiók kilép a helyen.
 
3. Típus **y** folytatja. Hozzáadhat a **-force** paraméter a megerősítési lépés elkerülése érdekében.
4. Miután befejezte a parancsot, írja be a **kilépéshez**. Ez a lépés az SSH-ügyfél bezárása után.

## <a name="step-2-create-vm-image"></a>2. lépés: A Virtuálisgép-lemezkép létrehozása
Az Azure CLI 2.0 segítségével a virtuális gép megjelölése általánosítva van, és a lemezképének rögzítése. A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevek a következők *myResourceGroup*, *myVnet*, és *myVM*.

1. A virtuális Gépet, amely az Ön platformelőfizetés felszabadítani [az virtuális gép felszabadítása](/cli//azure/vm#deallocate). Az alábbi példa felszabadítja a nevű virtuális gép *myVM* az erőforráscsoport neve *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. A virtuális gép megjelölése rendelkező általánosított [az vm generalize](/cli//azure/vm#generalize). A következő példa jelek a nevű virtuális gép *myVM* az erőforráscsoport neve *myResourceGroup* , általánosítva van:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Most létrehozza a virtuális gép erőforrásához a kép [az lemezkép létrehozása](/cli/azure/image#az_image_create). Az alábbi példakód létrehozza nevű kép *myImage* az erőforráscsoport neve *myResourceGroup* nevű VM erőforrás használatával *myVM*:
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A kép mint a forrás virtuális gép ugyanabban az erőforráscsoportban jön létre. Bármely erőforráscsoport virtuális gépeket hozhat létre a lemezképből az előfizetésen belül. Felügyeleti szempontból Kezdésként hozzon létre egy adott erőforráscsoportot a Virtuálisgép-erőforrások és a képeket.
   >
   > Ha szeretné a zóna rugalmas tárolás tárolják, egy régióban található, amely támogatja a létrehozásához szükséges [rendelkezésre állási zónák](../../availability-zones/az-overview.md) , és tartalmazzák a `--zone-resilient true` paraméter.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3. lépés: Virtuális gép létrehozása a rögzített lemezképből
A lemezkép segítségével létrehozott virtuális gép létrehozása [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create). Az alábbi példakód létrehozza a virtuális gépek nevű *myVMDeployed* nevű lemezkép alapján *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>A virtuális gép létrehozása egy másik erőforráscsoportban található 

Bármely erőforráscsoport lemezkép virtuális gépeket hozhat létre az előfizetésen belül. A virtuális gép egy másik erőforráscsoportban található, mint a lemezkép létrehozásához adja meg a teljes erőforrás-azonosítója a képhez. Használjon [az képlistában](/cli/azure/image#az_image_list) képek listájának megtekintéséhez. A kimenet a következő példához hasonló:

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

A virtuális géphez, ellenőrizze a központi telepítés és a használatával az új virtuális gép indítási létrehozott most SSH. SSH-kapcsolaton keresztül csatlakozni az IP-cím vagy FQDN-jét az a virtuális gép található [az vm megjelenítése](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>További lépések
A forrás Virtuálisgép-lemezkép létrehozhat több virtuális géphez. Ha módosítania kell a lemezkép: 

- Hozzon létre egy virtuális Gépet a lemezképből.
- Győződjön meg a szükséges frissítések vagy a konfigurációs módosításokat.
- Kövesse a lépéseket újra kiosztásának megszüntetése, felszabadítani, generalize és lemezkép létrehozása.
- Az új kép használata a jövőben a központi telepítésekre. Ha szükséges, az eredeti lemezkép törlése.

A parancssori felület a virtuális gépek kezelésével kapcsolatban további információkért lásd: [Azure CLI 2.0](/cli/azure).
