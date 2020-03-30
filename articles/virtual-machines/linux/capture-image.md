---
title: Linuxos virtuális gép lemezének rögzítése az Azure CLI használatával
description: Az Azure CLI használatával egy Azure virtuális gép lemezképét rögzítése, amelyet tömeges üzembe helyezésekhez használhat.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 77f6244651551763f5460432655d66267775a256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250399"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Virtuális gép vagy virtuális merevlemez rendszerképének létrehozása

Virtuális gép (VM) több példányát az Azure-ban való használatra, készítsen egy képet a virtuális gép vagy az operációs rendszer virtuális merevlemeze. A központi telepítéshez lemezkép létrehozásához el kell távolítania a személyes fiókadatokat. A következő lépésekben egy meglévő virtuális gép kibontása, felszabadítása, és hozzon létre egy lemezképet. Ezzel a lemezképpel virtuális gépeket hozhat létre az előfizetés bármely erőforráscsoportjában.

Ha másolatot szeretne készíteni a meglévő Linux virtuális gépről biztonsági mentéshez vagy hibakereséshez, vagy egy speciális Linux virtuális merevlemezt szeretne feltölteni egy helyszíni virtuális gépről, olvassa el [a Feltöltés és a Linux virtuális gép létrehozása egyéni lemezképből című témakört.](upload-vhd.md)  

Az Azure **VM Image Builder (Public Preview)** szolgáltatás segítségével az egyéni lemezkép létrehozásához, nem kell megtanulnia az eszközöket, vagy a telepítő build folyamatok, egyszerűen egy lemezkép konfigurációját, és a rendszerkép-szerkesztő hozza létre a lemezképet. További információ: [Első lépések az Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview)használatával című témakörben.

A kép létrehozása előtt a következő elemekre lesz szüksége:

* Az Erőforrás-kezelő telepítési modelljében létrehozott Azure-beli virtuális gép, amely felügyelt lemezeket használ. Ha még nem hozott létre Linux virtuális gép, [használhatja](quick-create-portal.md)a portált, az [Azure CLI-](quick-create-cli.md)vagy [Erőforrás-kezelő sablonokat.](create-ssh-secured-vm-from-template.md) Állítsa be a virtuális gép szükség szerint. Például [adatlemezeket adhat hozzá,](add-disk.md)frissítéseket alkalmazhat, és alkalmazásokat telepíthet. 

* A legújabb [Azure CLI](/cli/azure/install-az-cli2) telepítve van, és be van jelentkezve egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index#az-login)

## <a name="prefer-a-tutorial-instead"></a>Inkább a bemutató helyett?

A cikk egyszerűsített verziójáért, valamint az Azure-beli virtuális gépek teszteléséért, kiértékeléséről vagy megismeréséről a [CLI használatával egyéni lemezkép létrehozása című](tutorial-custom-images.md)témakörben olvashat.  Ellenkező esetben, olvass on here to get the teljes képet.


## <a name="step-1-deprovision-the-vm"></a>1. lépés: A virtuális gép kioldása
Először is megszüntetheti a virtuális gépet az Azure virtuálisgép-ügynök használatával a gépspecifikus fájlok és adatok törléséhez. Használja `waagent` a parancsot a `-deprovision+user` linuxos linuxos virtuális gép paraméterével. További információk: [Azure Linux-ügynök – felhasználói útmutató](../extensions/agent-linux.md).

1. Csatlakozzon a Linux virtuális gép egy SSH-ügyfél.
2. Az SSH ablakban írja be a következő parancsot:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Ezt a parancsot csak olyan virtuális számítógépen futtassa, amelyet lemezképként fog rögzíteni. Ez a parancs nem garantálja, hogy a kép törlődik az összes bizalmas információból, vagy alkalmas az újraelosztásra. A `+user` paraméter eltávolítja az utolsó kiépített felhasználói fiókot is. Ha meg szeretné tartani a felhasználói fiók `-deprovision`hitelesítő adatait a virtuális gépben, csak a t használja.
 
3. Adja meg **az y** gombot a folytatáshoz. A megerősítő `-force` lépés elkerülése érdekében hozzáadhatja a paramétert.
4. A parancs befejezése után lépjen be a **kijáratba** az SSH-ügyfél bezárásához.  A virtuális gép továbbra is fut ezen a ponton.

## <a name="step-2-create-vm-image"></a>2. lépés: Virtuálisgép-lemezkép létrehozása
Az Azure CLI használatával megjelölheti a virtuális gép általánosítva, és a lemezkép rögzítése. A következő példákban cserélje le a példaparaméterneveket a saját értékeire. Példa paraméter nevek közé *tartozik a myResourceGroup*, *myVnet*, és *myVM*.

1. Az [az vm-tel](/cli/azure/vm)megszüntetett virtuális gép felszabadításának felszabadítása. A következő példa felszabadítja a *myVM* nevű virtuális gép a *myResourceGroup*nevű erőforráscsoportban.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Várja meg, amíg a virtuális gép teljesen felszabadítja, mielőtt továbblépne. Ez eltarthat néhány percig.  A virtuális gép leáll a feloldás során.

2. Jelölje meg a virtuális gép általánosítva [az az vm generalize](/cli/azure/vm). A következő példa a *myVm* nevű virtuális gép az erőforráscsoportban *a myResourceGroup* általánosítva.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Az általánossá vált virtuális gép már nem indítható újra.

3. Hozzon létre egy képet a virtuális gép erőforrás [az rendszerkép létrehozása.](/cli/azure/image#az-image-create) A következő példa létrehoz egy *myImage* nevű képet a *myResourceGroup* nevű erőforráscsoportban a *myVM*nevű virtuálisgép-erőforrás használatával.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A rendszerkép ugyanabban az erőforráscsoportban jön létre, mint a forrás virtuális gép. Ebből a lemezképből az előfizetésbármely erőforráscsoportjában létrehozhat virtuális gépeket. Felügyeleti szempontból érdemes lehet létrehozni egy adott erőforráscsoportot a virtuális gép erőforrásaihoz és a rendszerképekhez.
   >
   > Ha zónarugalmas tárolóban szeretné tárolni a lemezképet, létre kell hoznia egy olyan régióban, amely támogatja a [rendelkezésre állási zónákat,](../../availability-zones/az-overview.md) és tartalmazza a `--zone-resilient true` paramétert.
   
Ez a parancs a virtuális gép lemezképét leíró JSON-t adja vissza. Mentse ezt a kimenetet későbbi használatra.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3. lépés: Virtuális gép létrehozása a rögzített képből
Hozzon létre egy virtuális gép segítségével létrehozott az [az vm create](/cli/azure/vm). A következő példa létrehoz egy *myVMDeployed* nevű virtuális gép a *myImage*nevű lemezképből.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>A virtuális gép létrehozása egy másik erőforráscsoportban 

Virtuális gépeket hozhat létre egy lemezképből az előfizetés bármely erőforráscsoportjában. Ha a lemezképnél eltérő erőforráscsoportban szeretne virtuális gépet létrehozni, adja meg a lemezkép teljes erőforrás-azonosítóját. Az [képlista segítségével](/cli/azure/image#az-image-list) megtekintheti a képek listáját. A kimenet a következő példához hasonló.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

A következő példa az [az vm létrehozása](/cli/azure/vm#az-vm-create) segítségével hozzon létre egy virtuális gép egy erőforráscsoportban nem a forráskép, a rendszerkép erőforrás-azonosító megadásával.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>4. lépés: A telepítés ellenőrzése

SSH-ba a virtuális gép a központi telepítés ellenőrzéséhez és az új virtuális gép használatának megkezdéséhez létrehozott. Az SSH-n keresztüli csatlakozáshoz keresse meg a virtuális gép IP-címét vagy teljes tartománynát [az az vm show-val.](/cli/azure/vm#az-vm-show)

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>További lépések
Több virtuális gépet is létrehozhat a forrás virtuálisgép-lemezképből. A kép módosítása: 

- Hozzon létre egy virtuális gép a lemezképből.
- Módosítások vagy konfigurációs módosítások végrehajtása.
- Kövesse ismét a lépéseket a lemezbontás, a felszabadítás, az általánosítás és a lemezkép létrehozásához.
- Használja ezt az új lemezképet a jövőbeli központi telepítésekhez. Törölheti az eredeti képet.

A virtuális gépek CLI-vel való kezeléséről az [Azure CLI](/cli/azure)című témakörben talál további információt.
