---
title: Virtuálisgép-méretezési készletek kezelése az Azure CLI-vel
description: Általános Azure CLI-parancsok a virtuális gép méretezési készletei kezeléséhez, például egy példány indításához és leállításához, illetve a méretezési csoport kapacitásának módosításához.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: d8f40708cf5a9989146a72e2093de7bad47ffde9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275747"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Virtuálisgép-méretezési készlet kezelése az Azure CLI-vel
A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Ez a cikk néhány gyakori Azure CLI-parancsokat részletez, amelyek lehetővé teszik a feladatok elvégzését.

A felügyeleti feladatok elvégzéséhez a legújabb Azure CLI-re van szükség. További információt [az Azure CLI telepítése című](/cli/azure/install-azure-cli)témakörben talál. Ha virtuálisgép-méretezési csoportot kell létrehoznia, [létrehozhat egy méretezési csoportot az Azure CLI-vel.](quick-create-cli.md)


## <a name="view-information-about-a-scale-set"></a>Méretezési csoport adatainak megtekintése
A méretezési csoport általános információinak megtekintéséhez használja [az vmss show](/cli/azure/vmss). A következő példa a myResourceGroup erőforráscsoport *myResourceGroup* erőforráscsoportban található *myScaleSet* nevű méretezési csoportadatait kapja meg. Írja be saját nevét az alábbiak szerint:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
A virtuálisgép-példányok listájának megtekintéséhez használja [az vmss list-instances.](/cli/azure/vmss) A következő példa felsorolja az összes virtuálisgép-példányt a *myResourceGroup* erőforráscsoport *myResourceGroup* erőforráscsoportban nevű méretezési csoportban. Adja meg saját értékeit a következő nevekhez:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Egy adott virtuálisgép-példány további adatainak `--instance-id` megtekintéséhez adja hozzá a paramétert az [az vmss get-instance-view nézethez,](/cli/azure/vmss) és adjon meg egy megtekinthető példányt. A következő példa a 0 *0* virtuálisgép-példány ra vonatkozó információkat tekinti meg a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Írja be saját nevét az alábbiak szerint:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>A virtuális gépek kapcsolati adatainak listázása
A méretezési csoportban lévő virtuális gépekhez való csatlakozáshoz SSH vagy RDP egy hozzárendelt nyilvános IP-címhez és portszámhoz kell csatlakoznia. Alapértelmezés szerint a hálózati címfordítási (NAT) szabályok hozzáadódnak az Azure terheléselosztóhoz, amely továbbítja a távoli kapcsolatforgalmát az egyes virtuális gépeknek. A virtuálisgép-példányokhoz való csatlakozás címének és portjainak listázásához használja [az az vmss list-instance-connection-info parancsot.](/cli/azure/vmss) A következő példa a virtuálisgép-példányok kapcsolati adatait sorolja fel a *myScaleSet* nevű méretezési csoportban és a *myResourceGroup* erőforráscsoportban. Adja meg saját értékeit a következő nevekhez:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
Az előző parancsok a méretezési csoportés a virtuálisgép-példányok adatait mutatták. A méretezési csoport példányainak számának növeléséhez vagy csökkentéséhez módosíthatja a kapacitást. A méretezési készlet létrehozza vagy eltávolítja a szükséges számú virtuális gépet, majd konfigurálja a virtuális gépeket az alkalmazásforgalom fogadására.

A méretezési csoportban jelenleg futó példányok számának megtekintéséhez használja az [az vmss show](/cli/azure/vmss) parancsot, és állítsa be az *sku.capacity* lekérdezést:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Ezt követően az [az vmss scale](/cli/azure/vmss) parancs használatával manuálisan növelheti vagy csökkentheti a méretezési csoportban futó virtuális gépek számát. A következő példa a méretezési csoportban lévő virtuális gépek számát *5-re*állítja:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. Ha csökkenti a méretezési csoport kapacitását, a legmagasabb példányazonosítóval rendelkező virtuális gépek et először eltávolítja.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Virtuális gépek leállítása és elindítása egy méretezési csoportban
Egy méretezési készlet egy vagy több virtuális gép leállításához használja [a virtuális gépek leállítása.](/cli/azure/vmss#az-vmss-stop) Az `--instance-ids` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. Több virtuális gép leállításához válassza el az egyes példányazonosítókat egy szóközvel.

A következő példa leállítja a *0* példányt a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg saját értékeit az alábbiak szerint:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

A leállított virtuális gépek továbbra is le vannak foglalva, és továbbra is számítási díjakat vonnak maga után. Ha ehelyett azt szeretné, hogy a virtuális gépek fellegyenek szabadítva, és csak tárolási költségekmerülnek fel, használja [az az vmss felszabadítását.](/cli/azure/vmss) Több virtuális gép felszabadításához válassza el az egyes példányazonosítókat egy szóközvel. A következő példa leállítja és felszabadítja a *0* példányt a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg saját értékeit az alábbiak szerint:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Virtuális gépek indítása méretezési készletben
Egy vagy több virtuális gép egy méretezési készletben történő indításához használja [a virtuális gépek indítását.](/cli/azure/vmss) Az `--instance-ids` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. Több virtuális gép indításához válassza el az egyes példányazonosítókat egy szóközvel.

A következő példa elindítja a *0-as* példányt a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg saját értékeit az alábbiak szerint:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Virtuális gépek újraindítása egy méretezési csoportban
Egy méretezési készlet egy vagy több virtuális gép újraindításához használja [az virtuális gépek újraindítását.](/cli/azure/vmss) Az `--instance-ids` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. Több virtuális gép újraindításához válassza el az egyes példányazonosítókat egy szóközvel.

A következő példa *0* újraindítja a 0.példányt a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportban. Adja meg saját értékeit az alábbiak szerint:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Virtuális gépek eltávolítása méretezési készletből
Egy méretezési csoport egy vagy több virtuális gép eltávolításához használja [a virtuális gépdelete-példányokat.](/cli/azure/vmss) A `--instance-ids` paraméter lehetővé teszi, hogy egy vagy több virtuális gépet távolítson el. Ha a példányazonosítót adja meg, a méretezési csoportban lévő összes virtuális gép törlődik. Több virtuális gép eltávolításához válassza el az egyes példányazonosítókat egy szóközvel.

A következő példa eltávolítja a *0-as* példányt a *myScaleSet* és a *myResourceGroup* erőforráscsoport nevű méretezési csoportból. Adja meg saját értékeit az alábbiak szerint:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>További lépések
A méretezési csoportok egyéb gyakori feladatai közé tartozik [az alkalmazás telepítése](virtual-machine-scale-sets-deploy-app.md)és a [virtuálisgép-példányok frissítése.](virtual-machine-scale-sets-upgrade-scale-set.md) Az Azure CLI segítségével is konfigurálhatja [az automatikus méretezési szabályokat.](virtual-machine-scale-sets-autoscale-overview.md)
