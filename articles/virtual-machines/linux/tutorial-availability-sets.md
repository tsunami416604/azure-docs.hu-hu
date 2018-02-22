---
title: "Rendelkezésre állási csoportokra vonatkozó oktatóanyag Azure-beli Linux rendszerű virtuális gépekhez | Microsoft Docs"
description: "Ismerje meg az Azure-beli Linux rendszerű virtuális gépek rendelkezésre állási csoportjait."
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
ms.openlocfilehash: 504c4a666d1abd7a495d6759d62815f53f0b54fa
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-use-availability-sets"></a>A rendelkezésre állási csoportok használata


Ebben az oktatóanyagban megtanulhatja, hogyan növelheti Azure-beli virtuálisgép-megoldásai rendelkezésre állását és megbízhatóságát a rendelkezésre állási csoportok elnevezésű képesség használatával. A rendelkezésre állási csoportok biztosítják, hogy az Azure-ban üzembe helyezett virtuális gépek több elkülönített hardverfürt között legyenek elosztva. Ezáltal biztosítható, hogy ha hardveres vagy Azure-beli szoftveres hiba fordul elő, az a virtuális gépeknek csak egy részhalmazát érintse, és a teljes megoldás továbbra is elérhető és működőképes maradjon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="availability-set-overview"></a>Rendelkezésre állási csoport – áttekintés

A rendelkezésre állási csoport egy logikai csoportosítási funkció, amelyet az Azure-ban használva biztosíthatja, hogy a csoportba helyezett virtuálisgép-erőforrások egymástól elkülönítve legyenek üzembe helyezve az Azure-adatközpontokban. Az Azure biztosítja, hogy a rendelkezésre állási csoportba helyezett virtuális gépek több fizikai kiszolgálón, számítási rackszekrényen, tárolási egységben és hálózati kapcsolón fussanak. Ha hardveres vagy Azure-beli szoftveres hiba lép fel, az a virtuális gépeknek csak egy részhalmazát érinti, a teljes alkalmazás azonban tovább üzemel, és továbbra is elérhető marad az ügyfelek számára. A rendelkezésre állási csoport egy alapvető funkció, ha megbízható felhőalapú megoldásokat kíván létrehozni.

Vegyünk például egy tipikus virtuálisgép-alapú megoldást négy előtérbeli webkiszolgálóval és két háttérbeli virtuális géppel, amelyek egy adatbázist futtatnak. Az Azure-ban két rendelkezésre állási csoportot érdemes meghatározni a virtuális gépek üzembe helyezése előtt: egy rendelkezésre állási csoportot a webes szint, egyet pedig az adatbázisszint számára. Amikor létrehoz egy új virtuális gépet, megadhatja a rendelkezésre állási csoportot paraméterként az „az vm create” parancsban. Ekkor az Azure automatikusan biztosítja, hogy a rendelkezésre állási csoportban létrehozott virtuális gépek el legyenek különítve több fizikai hardvererőforráson. Ha a fizikai hardver, amelyen az egyik webes vagy adatbázis-kiszolgáló virtuális gép üzemel, meghibásodik, biztos lehet abban, hogy a webes és az adatbázis-kiszolgáló virtuális gép többi példánya tovább üzemel, mivel azok más hardveren találhatók.

Használjon rendelkezésre állási csoportokat, ha megbízható, virtuálisgép-alapú megoldásokat szeretne üzembe helyezni az Azure-ban.


## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Rendelkezésre állási csoportot az [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create) paranccsal hozhat létre. Ebben a példában a frissítési és a tartalék tartományok számát egyaránt *2*-re állítjuk a *myResourceGroupAvailability* erőforráscsoport *myAvailabilitySet* nevű rendelkezésre állási csoportja esetében.

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

A rendelkezésre állási csoportok segítségével elkülönítheti az erőforrásokat a tartalék tartományok és a frissítési tartományok között. A **tartalék tartomány** kiszolgáló-, hálózati és tárolási erőforrások egy elkülönített gyűjteményét képviseli. Az előző példában jeleztük, hogy a rendelkezésre állási csoportot legalább két tartalék tartományon szeretnénk elosztani a virtuális gépek üzembe helyezésekor. Azt is jelezzük, hogy a rendelkezésre állási csoportot két **frissítési tartomány** között szeretnénk elosztani.  A két frissítési tartomány biztosítja, hogy amikor az Azure frissíti a szoftvereket, a virtuális gépeink erőforrásai el legyenek különítve. Ezzel megakadályozhatjuk, hogy minden, a virtuális gépen futó szoftver egyszerre frissüljön.


## <a name="create-vms-inside-an-availability-set"></a>Virtuális gépek létrehozása rendelkezésre állási csoportban

A virtuális gépeket a rendelkezésre állási csoportban kell létrehozni, hogy azok biztosan megfelelően legyenek elosztva a hardveren. Meglévő virtuális gépet nem adhat hozzá rendelkezésre állási csoporthoz annak létrejötte után. 

Ha az [az vm create](/cli/azure/vm#az_vm_create) paranccsal hoz létre virtuális gépet, az `--availability-set` paraméterrel adhatja meg a rendelkezésre állási csoport nevét.

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

Most már van két virtuális gépünk az újonnan létrehozott rendelkezésre állási csoportunkban. Minthogy ugyanabban a rendelkezésre állási csoportban vannak, az Azure biztosítja, hogy a virtuális gépek és az erőforrásaik (az adatlemezeket is beleértve) el legyenek osztva az elkülönített fizikai hardverek között. Ez az elosztás magasabb rendelkezésre állást biztosít az egész virtuálisgép-megoldásunkban.

Ha megtekinti a rendelkezésre állási csoportot a portálon az Erőforráscsoportok > myResourceGroupAvailability > myAvailabilitySet pont alatt, láthatja, hogy a virtuális gépek hogyan oszlanak el a két tartalék és frissítési tartományban.

![Rendelkezésre állási csoport a portálon](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Elérhető virtuálisgép-méretek ellenőrzése 

A rendelkezésre állási csoportot később további virtuális gépekkel bővítheti, azonban tudnia kell, milyen virtuálisgép-méretek érhetők el a hardveren.  Az [az vm availability-set list-sizes](/cli/azure/availability-set#az_availability_set_list_sizes) paranccsal listázhatja a rendelkezésre állási csoport hardverfürtjén elérhető összes méretet.

```azurecli-interactive 
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése

Folytassa a következő oktatóanyaggal, amely a virtuálisgép-méretezési csoportokat ismerteti.

> [!div class="nextstepaction"]
> [Virtuálisgép-méretezési csoport létrehozása](tutorial-create-vmss.md)

