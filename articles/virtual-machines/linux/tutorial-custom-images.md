---
title: "Egyéni Virtuálisgép-lemezképek létrehozása az Azure parancssori felülettel |} Microsoft Docs"
description: "Útmutató – hozzon létre egy egyéni Virtuálisgép-lemezkép az Azure parancssori felület használatával."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e73494ff4827b74cbb42b2b0f1f9738c78960e23
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Hozzon létre egy egyéni rendszerképet az Azure virtuális gép a parancssori felület használatával

Egyéni lemezképek piactéren elérhető rendszerkép hasonló, de Ön hozza létre őket. Egyéni lemezképek a rendszerindítási beállításokat, például alkalmazások, alkalmazás, és más operációs rendszer konfigurációjában kerüli használható. Ebben az oktatóanyagban létrehoz egy Azure virtuális gép saját egyéni rendszerképét. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kiosztásának megszüntetése és virtuális gépek generalize
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egy egyéni lemezképből
> * Az előfizetésben a képek felsorolása
> * Lemezkép törlése


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépéseket a meglévő virtuális igénybe vehet, és kapcsolja be, amelyek segítségével hozzon létre új Virtuálisgép-példányok újra felhasználható egyéni lemezképként adatok találhatók.

A példa az oktatóanyag elvégzéséhez rendelkeznie kell egy meglévő virtuális gépet. Ha szükséges, ez [parancsfájl minta](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) hozhat létre egyet. Az oktatóanyag lépéseinek működő cseréje esetén a az erőforráscsoportot és a virtuális gép nevét, amennyiben szükséges.

## <a name="create-a-custom-image"></a>Egyéni lemezkép létrehozása

Hozzon létre egy virtuális gép lemezképét, meg kell készíteni a virtuális gép megszüntetés, felszabadítása, és majd a forrás virtuális Gépen, mivel általánosítva. Miután a virtuális gép előkészített, kép hozhatók létre.

### <a name="deprovision-the-vm"></a>A virtuális gép kiosztásának megszüntetése 

Megszüntetés használatúvá a virtuális gép-specifikus adatok eltávolításával. Ez általánosítása lehetővé teszi egyetlen lemezképéről sok virtuális gép központi telepítéséhez. Megszüntetés, során az állomás neve lesz visszaállítva *localhost.localdomain*. SSH-állomások kulcsait, a névkiszolgáló-konfigurációk, a gyökér szintű jelszó és a gyorsítótárazott DHCP-bérletek is törlődnek.

A virtuális gép kiosztásának megszüntetése, használja az Azure Virtuálisgép-ügynök (waagent). Az Azure Virtuálisgép-ügynök telepítve van a virtuális Gépre, és kezeli az üzembe helyezési és az Azure Fabric Controller való interakció. További információkért lásd: a [Azure Linux ügynök felhasználói útmutató](agent-user-guide.md).

A virtuális gép SSH használatával csatlakozhat, és futtassa a parancsot a virtuális gép kiosztásának megszüntetése. Az a `+user` argumentum, a legutóbbi kiépített felhasználói fiók és minden egyéb vonatkozó adatok is törlődnek. A példa IP-cím cserélje le a virtuális gép nyilvános IP-címét.

SSH-kapcsolatot a virtuális Gépet.
```bash
ssh azureuser@52.174.34.95
```
A virtuális gép kiosztásának megszüntetése.

```bash
sudo waagent -deprovision+user -force
```
Zárja be az SSH-munkamenetet.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Felszabadítani, és a virtuális gép megjelölése általánosítva

A képfájl létrehozásához a virtuális gép felszabadítása kell. A virtuális gép használatával felszabadítani [az virtuális gép felszabadítása](/cli//azure/vm#deallocate). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Végezetül beállítani a virtuális gép állapotát a általánosítva [az vm generalize](/cli//azure/vm#generalize) , az Azure platformon tudja, hogy a virtuális gép már általánosítva lett. Kép csak létrehozhat egy általánosított virtuális Gépet.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>A lemezkép létrehozása

Most a virtuális gép lemezképét segítségével létrehozható [az lemezkép létrehozása](/cli//azure/image#create). Az alábbi példakód létrehozza nevű kép *myImage* nevű VM *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Virtuális gépek létrehozása lemezkép alapján

Most, hogy egy lemezképet, létrehozhat egy vagy több új virtuális gépek a lemezkép használatával [az virtuális gép létrehozása](/cli/azure/vm#create). Az alábbi példakód létrehozza a virtuális gépek nevű *myVMfromImage* nevű lemezkép alapján *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>Lemezkép-kezelési 

Az alábbiakban néhány olyan gyakori lemezkép-kezelési feladatok és, hogyan lehet elvégezni őket az Azure parancssori felület használatával.

Lista összes lemezkép nevű táblázatos formátumban.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Lemezkép törlése. Ebben a példában a nevű rendszerkép törlése *myOldImage* a a *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létre egyéni Virtuálisgép-lemezképet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Kiosztásának megszüntetése és virtuális gépek generalize
> * Egyéni lemezkép létrehozása
> * Virtuális gép létrehozása egy egyéni lemezképből
> * Az előfizetésben a képek felsorolása
> * Lemezkép törlése

További információt a magas rendelkezésre állású virtuális gépek a következő oktatóanyag továbblépés.

> [!div class="nextstepaction"]
> [Hozzon létre magas rendelkezésre állású virtuális gépek](tutorial-availability-sets.md).

