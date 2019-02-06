---
title: Oktatóanyag – Méretezési csoport automatikus skálázása az Azure CLI használatával | Microsoft Docs
description: Ismerje meg, hogyan skálázhat automatikusan virtuálisgép-méretezési csoportokat az Azure CLI használatával a processzorterhelés növekedésének vagy csökkenésének megfelelően
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
ms.topic: tutorial
ms.date: 05/18/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4064816ae932a0f26fd3478420c69f3e8fba8732
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751276"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Oktatóanyag: Virtuálisgép-méretezési csoport automatikus méretezése az Azure CLI használatával

Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Automatikus skálázás használata méretezési csoportokkal
> * Automatikus skálázási szabályok létrehozása és használata
> * Virtuálisgép-példányok és automatikus skálázás-aktiválási szabályok terhelési tesztje
> * Visszaméretezés, ha az igény csökken

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.32-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal az alábbiak szerint:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) paranccsal. A következő példa *2-es* példányszámmal egy méretezési csoportot hoz létre, valamint SSH-kulcsokat is, ha azok még nem léteznének:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="define-an-autoscale-profile"></a>Automatikus skálázási profil meghatározása

Az automatikus skálázás méretezési csoportban történő engedélyezéséhez először határozza meg az automatikus skálázási profilt. Ez a profil határozza meg a méretezési csoport alapértelmezett, minimális és maximális kapacitását. Ezek a korlátok lehetővé teszik a költségek Virtuálisgép-példányok nem folyamatosan létrehozásával, és elosztja a elfogadható teljesítményt, mely egy horizontális leskálázási esemény is példányok minimális száma. Automatikus skálázási profilt az [az monitor autoscale create](/cli/azure/monitor/autoscale#az-monitor-autoscale-create) paranccsal hozhat létre. A következő példa a virtuálisgép-példányok alapértelmezett és egyben minimális (*2*), valamint a maximális (*10*) értékét állítja be:

```azurecli-interactive
az monitor autoscale create \
  --resource-group myResourceGroup \
  --resource myScaleSet \
  --resource-type Microsoft.Compute/virtualMachineScaleSets \
  --name autoscale \
  --min-count 2 \
  --max-count 10 \
  --count 2
```

## <a name="create-a-rule-to-autoscale-out"></a>Automatikus felskálázási szabály létrehozása

Az alkalmazás növekvő igényeivel párhuzamosan a méretezési csoportban lévő virtuálisgép-példányok terhelése is nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

Hozzunk létre egy szabályt az [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create) paranccsal a méretezési csoportban lévő virtuálisgép-példányok növelésére, ha az átlagos processzorterhelés 5 percen keresztül meghaladja a 70%-ot. A szabály aktiválásakor a virtuálisgép-példányok száma hárommal nő.

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 3
```

## <a name="create-a-rule-to-autoscale-in"></a>Automatikus leskálázási szabály létrehozása

Az este vagy a hétvége folyamán az alkalmazás igényei csökkenhetnek. Ha a csökkent terhelés egy adott időtartam alatt állandó, akkor megadhatja, hogy az automatikus skálázási szabály csökkentse a virtuálisgép-példányok számát a méretezési csoportban. A horizontális leskálázási művelet csökkenti a méretezési csoport futtatásának költségeit, mivel csak az aktuális igényt kielégítő számú példányt futtat.

Hozzunk létre egy másik szabályt az [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create) paranccsal a méretezési csoportban lévő virtuálisgép-példányok csökkentésére, ha az átlagos processzorterhelés ezután 5 percen keresztül nem éri el a 30%-ot. Az alábbi példa egy olyan szabályt határoz meg, amely a virtuálisgép-példányok számát eggyel skálázza le horizontálisan:

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1
```

## <a name="generate-cpu-load-on-scale-set"></a>Processzorterhelés létrehozása a méretezési csoportban

Az automatikus skálázási szabályok teszteléséhez hozzon létre némi processzorterhelést a méretezési csoportban lévő virtuálisgép-példányokhoz. Ezen szimulált processzorterhelés hatására az automatikus méretezési szabályok horizontálisan felskáláznak, és megnövelik a virtuálisgép-példányok számát. A szimulált processzorterhelés ezt követő csökkentésével az automatikus méretezési szabály horizontálisan leskáláz, és csökkenti a virtuálisgép-példányok számát.

Először listázza ki a méretezési csoportban lévő virtuálisgép-példányokhoz való csatlakozáshoz használható címet és portokat az [az vmss list-instance-connection-info](/cli/azure/vmss) paranccsal:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Az alábbi kimenetpélda a példány nevét, a terheléselosztó nyilvános IP-címét és azt a portszámot mutatja, amelyre a hálózati címfordítási (NAT) szabályok továbbítják a forgalmat:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH-n keresztül csatlakozzon az első virtuálisgép-példányhoz. Adja meg saját nyilvános IP-címét és portszámát a `-p` paraméterrel az ezt megelőző parancsban látható módon:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

A bejelentkezést követően telepítse a **stress** segédprogramot. Indítson el *10* **terhelési** feldolgozót, amelyek processzorterhelést hoznak létre. Ezek a feldolgozók *420* másodpercig futnak. Ez elegendő idő ahhoz, hogy az automatikus méretezési szabályok alkalmazzák a kívánt műveletet.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Amikor **stress** hasonló eredményeket jelenít meg *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, nyomja le az *Enter* billentyűt a parancssorhoz való visszatéréshez.

Annak megerősítéséhez, hogy a **stress** segédprogram processzorterhelést hoz létre, vizsgálja meg az aktív rendszerterhelést a **top** segédprogram segítségével:

```azurecli-interactive
top
```

Lépjen ki a **top** segédprogramból, majd zárja be a virtuálisgép-példánnyal létesített kapcsolatot. A **stress** segédprogram továbbra is fut a virtuálisgép-példányon.

```azurecli-interactive
Ctrl-c
exit
```

Csatlakozzon a másik virtuálisgép-példányhoz az előző [az vmss list-instance-connection-info](/cli/azure/vmss) paranccsal listázott portszám segítségével:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

Telepítse és futtassa a **stress** segédprogramot, majd indítson el tíz feldolgozót a második virtuálisgép-példányon.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Újra, amikor **stress** hasonló eredményeket jelenít meg *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, nyomja le az *Enter* billentyűt a parancssorhoz való visszatéréshez.

Zárja be a második virtuálisgép-példánnyal létesített kapcsolatot. A **stress** segédprogram továbbra is fut a virtuálisgép-példányon.

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Aktív automatikus skálázási szabályok monitorozása

A méretezési csoportban lévő virtuálisgép-példányok számának monitorozásához használja a **watch** segédprogramot. Az egyes virtuálisgép-példányokon 5 percet vesz igénybe, mire az automatikus skálázási szabályok megkezdik a horizontális felskálázási folyamatot a **stress** segédprogram által létrehozott processzorterhelésre válaszul:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Ha elérte a processzor küszöbértékét, az automatikus méretezési szabályok megnövelik a méretezési csoportban lévő virtuálisgép-példányok számát. Az alábbi kimenet három virtuális gép létrejöttét mutatja, amint a méretezési csoport automatikusan felskálázódik:

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            myResourceGroup  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            myResourceGroup  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             myResourceGroup  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             myResourceGroup  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Amint a **stress** segédprogram leáll a kezdeti virtuálisgép-példányokon, az átlagos processzorterhelés visszatér a normális szintre. Újabb 5 perc elteltével az automatikus méretezési szabályok horizontálisan leskálázzák a virtuálisgép-példányok számát. A horizontális leskálázási műveletek először a legmagasabb azonosítóval rendelkező virtuálisgép-példányokat távolítják el. Ha egy méretezési csoport az Availability Sets vagy az Availability Zones funkciót használja, a horizontális leskálázási műveletek egyenletesen lesznek elosztva a virtuálisgép-példányok között. Az alábbi kimeneti példa egy virtuálisgép-példány törlését mutatja, ahogy a méretezési csoport automatikusan leskálázódik:

```bash
           6  True                  eastus      myScaleSet_6  Deleting             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Lépjen ki a *watch* segédprogramból a `Ctrl-c` paranccsal. A méretezési csoport folytatja az 5 percenkénti horizontális leskálázást, és eltávolít egy virtuálisgép-példányt, amíg el nem éri a minimális két példányos értéket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A méretezési csoport és további erőforrások eltávolításához törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást az [az group delete](/cli/azure/group) paranccsal. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan lehet automatikusan horizontálisan le- illetve felskálázni egy méretezési csoportot az Azure CLI használatával:

> [!div class="checklist"]
> * Automatikus skálázás használata méretezési csoportokkal
> * Automatikus skálázási szabályok létrehozása és használata
> * Virtuálisgép-példányok és automatikus skálázás-aktiválási szabályok terhelési tesztje
> * Visszaméretezés, ha az igény csökken

A virtuálisgép-méretezési csoportok működésével kapcsolatos további példákért tekintse meg az alábbi Azure CLI-mintaszkripteket:

> [!div class="nextstepaction"]
> [Méretezési csoportokhoz tartozó szkriptminták az Azure CLI-hez](cli-samples.md)
