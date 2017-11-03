---
title: "Rendelkezésre állási készletek oktatóanyag Linux virtuális gépek Azure-ban |} Microsoft Docs"
description: "További tudnivalók a rendelkezésre állási készletek Linux virtuális gépek Azure-ban."
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e7780a29f6633b444608d96012fabe67b9b6d924
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-availability-sets"></a>Rendelkezésre állási készletek használata


Ebben az oktatóanyagban elsajátíthatja a rendelkezésre állása és megbízhatósága szempontjából a virtuális gép megoldások Azure-ban egy rendelkezésre állási készletek nevű képesség növelésére. Rendelkezésre állási készletek győződjön meg arról, hogy a telepített Azure virtuális gépek több elkülönített hardver fürt különböző pontjain. Ez biztosítja, hogy az Azure hardveres vagy szoftveres hiba akkor fordul elő, ha a virtuális gépek csak egy részhalmazát érintett ezzel és, hogy a teljes megoldás továbbra is elérhető, és működik.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Hozzon létre egy virtuális gép rendelkezésre állási csoportba
> * Ellenőrizze a rendelkezésre álló Virtuálisgép-méretek


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="availability-set-overview"></a>Rendelkezésre állási csoport – áttekintés

Rendelkezésre állási csoport egy olyan logikai jellegű csoportosítását képességgel, győződjön meg arról, hogy a Virtuálisgép-erőforrások helyezi-e benne el különítve egymástól belül egy Azure-adatközpontban telepítésekor használhatja az Azure-ban. Azure biztosítja, hogy a virtuális gépek elhelyezésekor belül egy rendelkezésre állási készlet több fizikai kiszolgálón futtassa, például rackszekrények, a tárolási egység és a hálózati kapcsolók számítási. A hardver- vagy Azure szoftver hiba akkor fordul elő, ha a virtuális gépek csak egy részhalmazát érintett, és az alkalmazás általános mentése marad, és továbbra is fennáll, az ügyfelek számára elérhető legyen. Rendelkezésre állási csoportok egy alapvető funkció is, ha a használni kívánt megbízható felhőalapú megoldásokat.

Mérlegeljük, ahol például 4 előtér-webkiszolgáló, és 2 háttér-virtuális gép egy adatbázist az tipikus Virtuálisgép-alapú megoldás. Az Azure-szeretné a virtuális gépek telepítése előtt határozza meg a két rendelkezésre állási csoportok: egy rendelkezésre állási készletét, a "web" réteg és a rendelkezésre állási csoporthoz az "adatbázis" szinthez. Amikor létrehoz egy új virtuális Gépet, majd megadhatja a rendelkezésre állási csoportot paraméterként az VM létrehozása parancs, és az Azure automatikusan biztosítja, hogy a virtuális gépeket hoz létre belül a rendelkezésre álló készlet több fizikai hardver-erőforrások között vannak elszigetelt. Ha a fizikai hardverrel, az egyik webkiszolgálón vagy adatbázis-kiszolgáló virtuális gépeken futó probléma van, akkor tudja, hogy a webkiszolgáló és az adatbázis virtuális gépek más példánya futhat, mert másik hardveren.

Rendelkezésre állási készletek használata során megbízható, VM-alapú megoldások Azure-ban telepíteni szeretné.


## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Rendelkezésre állási készlet használatával hozhat létre [az virtuális gép rendelkezésre állási-csoport létrehozása](/cli/azure/vm/availability-set#create). Ebben a példában mindkét, frissítés és a tartalék tartományok számának hivatott *2* esetében a rendelkezésre állási csoportot elnevezett *myAvailabilitySet* a a *myResourceGroupAvailability* erőforráscsoportot.

Hozzon létre egy erőforráscsoportot.

```azurecli-interactive 
az group create --name myResourceGroupAvailability --location eastus
```


```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Rendelkezésre állási készletek lehetővé teszik erőforrások elkülöníteni a tartalék tartományok közötti és tartományok frissítése. A **tartalék tartomány** kiszolgáló + hálózati, tárolási elkülönített gyűjteményét képviseli erőforrásokat. Az előző példában azt jelzi, hogy azt szeretné, hogy a rendelkezésre állási csoportban, legalább két tartalék tartományok legyen elosztva, ha a virtuális gépek vannak telepítve. Azt is jelzi, hogy azt szeretné, hogy a rendelkezésre állási készlet elosztott két **tartományok frissítése**.  Két frissítési tartomány arról, hogy amikor a szoftverfrissítések az Azure-ban a Virtuálisgép-erőforrások elkülönített, akadályozza meg, hogy a szoftver összes alatt a virtuális gép fut egyszerre frissítendő.


## <a name="create-vms-inside-an-availability-set"></a>Hozzon létre virtuális gépek rendelkezésre állási csoportok belül

Virtuális gépek a rendelkezésre állási készletét, győződjön meg arról, hogy a hardver megfelelően vannak elosztva a belül kell létrehoznia. Rendelkezésre állási készlet létrehozása után nem lehet hozzáadni egy meglévő virtuális Gépre. 

Amikor hoz létre egy virtuális gép használatával [az virtuális gép létrehozása](/cli/azure/vm#create) a rendelkezésre állási csoportot a használatával megadhatja a `--availability-set` paraméterrel adhatja meg a rendelkezésre állási készlet nevét.

```azurecli-interactive 
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Most már tudunk két virtuális gép belül az újonnan létrehozott rendelkezésre állási csoportot. Az azonos rendelkezésre állási készlet vannak, mert a Azure biztosítja, hogy a virtuális gépek és az összes az erőforrások (beleértve az adatlemezek) különítve fizikai hardver elosztott. Ehhez a terjesztéshez biztosíthatja, hogy mekkora a teljes méretű megoldás magas rendelkezésre állás érdekében.

Ha megnézi a rendelkezésre állási csoportot a portálon a erőforráscsoportok > myResourceGroupAvailability > myAvailabilitySet, tekintse meg a virtuális gépeket a 2 tartalék elosztott hogyan és tartományok frissíteni kell.

![Rendelkezésre állási csoportban, a portálon](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Ellenőrizze az elérhető Virtuálisgép-méretek 

A rendelkezésre állási csoportot később adhat hozzá további virtuális gépek, de milyen Virtuálisgép-méretek állnak rendelkezésre a hardver tudnia kell.  Használjon [az virtuális gép rendelkezésre állási-készlet lista-méretek](/cli/azure/availability-set#list-sizes) listázza az összes hardveren elérhető méretek fürtön a rendelkezésre állási csoport számára.

```azurecli-interactive 
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Hozzon létre egy virtuális gép rendelkezésre állási csoportba
> * Ellenőrizze a rendelkezésre álló Virtuálisgép-méretek

Virtuálisgép-méretezési csoportok olvashat a következő oktatóanyag továbblépés.

> [!div class="nextstepaction"]
> [Virtuálisgép-méretezési csoport létrehozása](tutorial-create-vmss.md)

