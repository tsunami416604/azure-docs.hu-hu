---
title: Kezelheti az Azure CLI-vel a Virtual Machine Scale Sets |} A Microsoft Docs
description: Gyakori Azure CLI-parancsok kezelése a Virtual Machine Scale Sets, például a következőkkel elindíthatja és leállíthatja a példány, vagy módosítsa a méretezési csoport kapacitásának beállítása.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 014e6ff2bc769675e47743cfdd4fbf7fc6234974
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999834"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Állítsa be az Azure CLI-vel egy virtuálisgép-méretezési csoport kezelése
A virtuálisgép-méretezési csoport életciklusa során egy vagy több felügyeleti feladat futtatására lehet szükség. Emellett előfordulhat, hogy különféle szkripteket is érdemes létrehozni az életciklus-feladatok automatizálására. Ez a cikk részletesen néhány gyakori Azure CLI-parancsok, amelyek lehetővé teszik, hogy ezeket a feladatokat.

Felügyeleti feladatok elvégzéséhez szüksége van az Azure CLI legújabb. További információ: [az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha szeretne létrehozni egy virtuálisgép-méretezési csoportot, akkor az [hozzon létre egy méretezési csoportot az Azure CLI-vel](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Egy méretezési csoportot adatainak megtekintése
Egy méretezési csoportot vonatkozó általános információk megtekintéséhez használja [az vmss show](/cli/azure/vmss#az_vmss_show). Az alábbi példa lekéri a nevű méretezési információ *myScaleSet* a a *myResourceGroup* erőforráscsoportot. Adja meg a saját nevek a következők szerint:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Virtuális gépek megtekintése egy méretezési csoportban
Egy méretezési csoportban lévő Virtuálisgép-példányok listájának megtekintéséhez használja [az vmss list-instances](/cli/azure/vmss#list-instances). Az alábbi példa felsorolja az összes Virtuálisgép-példányt a méretezési elnevezett *myScaleSet* a a *myResourceGroup* erőforráscsoportot. Adja meg ezeket a neveket a saját értékeit:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Egy adott Virtuálisgép-példánnyal kapcsolatos további információk megtekintéséhez adja hozzá a `--instance-id` paramétert [az vmss get-instance-view](/cli/azure/vmss#get-instance-view) és a egy példány megtekintéséhez adja meg. Az alábbi példa megjeleníti a Virtuálisgép-példánnyal kapcsolatos információk *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg a saját nevek a következők szerint:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>A virtuális gépek kapcsolatadatok listázása
A virtuális géphez való kapcsolódásra egy méretezési csoportban, Ön SSH vagy RDP-vel egy hozzárendelt nyilvános IP-cím és port számát. Hálózati címfordítás (NAT) szabályait alapértelmezés szerint az Azure load balancerben, amely továbbítja a távoli kapcsolati forgalmat az egyes virtuális kerülnek. A cím és a egy méretezési csoportban lévő Virtuálisgép-példányokhoz való kapcsolódáshoz portok listázásához használja [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). Az alábbi példa felsorolja a méretezési nevű Virtuálisgép-példányok-kapcsolódási információt *myScaleSet* és a *myResourceGroup* erőforráscsoportot. Adja meg ezeket a neveket a saját értékeit:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Méretezési csoport kapacitásának módosítása
Az előző parancsokban bemutatta a méretezési csoport és a Virtuálisgép-példányok kapcsolatos információkat. Növelheti vagy csökkentheti a méretezési csoportban lévő példányok számát, módosíthatja a kapacitást. A méretezési csoportot hoz létre vagy eltávolítja a szükséges virtuális gépek számát, majd konfigurálja a virtuális gépek alkalmazás forgalom fogadására.

A méretezési csoportban jelenleg futó példányok számának megtekintéséhez használja az [az vmss show](/cli/azure/vmss#az_vmss_show) parancsot, és állítsa be az *sku.capacity* lekérdezést:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Ezt követően az [az vmss scale](/cli/azure/vmss#az_vmss_scale) parancs használatával manuálisan növelheti vagy csökkentheti a méretezési csoportban futó virtuális gépek számát. Az alábbi példa beállítja a virtuális gépek számát a méretezési *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

A méretezési csoport kapacitásának frissítése néhány percet vesz igénybe. Ha csökkenti a méretezési csoport kapacitását beállítása, a virtuális gépek azonosítók rendszer távolítja el először a legmagasabb példánnyal.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Állítsa le, és a egy méretezési csoportban lévő virtuális gépek indítása
Egy vagy több méretezési csoportban lévő virtuális gépek leállításához használja [az vmss stop](/cli/azure/vmss#az-vmss-stop). Az `--instance-ids` paraméter segítségével megadhat egy vagy több leállítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép le lesz állítva. Több virtuális gép leállításához külön mindegyik Példányazonosítója egy-egy szóközzel.

A következő példa leállítja a példányt *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. A saját értékeit adja meg a következőképpen:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

A leállított virtuális gépek lefoglalva maradnak, és továbbra is számítási költségeket generálnak. Ha inkább felszabadítani fel kell szabadítani a virtuális gépeket, és csak tárolási költségeket fizetni, akkor [az vmss deallocate](/cli/azure/vmss#az_vmss_deallocate). Több virtuális gép felszabadítása, külön mindegyik Példányazonosítója egy-egy szóközzel. A következő példa leállítja és felszabadítja a példányt *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. A saját értékeit adja meg a következőképpen:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Egy méretezési csoportban lévő virtuális gépek indítása
Egy méretezési csoportban lévő egy vagy több virtuális gép indításához használja [az vmss start](/cli/azure/vmss#az_vmss_start). Az `--instance-ids` paraméter segítségével megadhat egy vagy több indítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép el lesz indítva. Több virtuális gép indításához külön mindegyik Példányazonosítója egy-egy szóközzel.

A következő példa elindítja a példány *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. A saját értékeit adja meg a következőképpen:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Egy méretezési csoportban lévő virtuális gépek újraindítása
Egy méretezési csoportban lévő egy vagy több virtuális gép újraindításához használja [az vmss újraindítása](/cli/azure/vmss#az_vmss_restart). Az `--instance-ids` paraméter segítségével megadhat egy vagy több újraindítandó virtuális gépet. Ha nem ad meg példányazonosítót, a méretezési csoportban lévő összes virtuális gép újra lesz indítva. Több virtuális gép újraindításához külön mindegyik Példányazonosítója egy-egy szóközzel.

A következő példa újraindítja a példányt *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. A saját értékeit adja meg a következőképpen:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Távolítsa el a virtuális gépek méretezési csoportból
Egy méretezési csoportban lévő egy vagy több virtuális gép eltávolításához használja [az vmss delete-instances](/cli/azure/vmss#delete-instances). A `--instance-ids` paraméter lehetővé teszi, hogy adjon meg egy vagy több virtuális gépet eltávolítja. Ha megad * a példány Azonosítóját, a méretezési csoportban lévő összes virtuális gép el lesznek távolítva. Több virtuális gép eltávolításához egy-egy szóközzel válassza el mindegyik Példányazonosítója.

A következő példa eltávolítja az előfordulást *0* elnevezett méretezési *myScaleSet* és a *myResourceGroup* erőforráscsoportot. A saját értékeit adja meg a következőképpen:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>További lépések
A méretezési csoportokkal kapcsolatos egyéb gyakori feladatok közé tartozik annak [alkalmazás üzembe helyezése](virtual-machine-scale-sets-deploy-app.md), és [Virtuálisgép-példányok frissítése](virtual-machine-scale-sets-upgrade-scale-set.md). Az Azure CLI-vel is használható [konfigurálása az automatikus skálázási szabályok](virtual-machine-scale-sets-autoscale-overview.md).
