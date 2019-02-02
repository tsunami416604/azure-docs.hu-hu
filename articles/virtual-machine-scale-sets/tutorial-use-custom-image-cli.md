---
title: Oktatóanyag – Egyéni virtuálisgép-rendszerkép használata egy méretezési csoportban az Azure CLI használatával | Microsoft Docs
description: Megismerheti, hogyan hozhat létre egyéni virtuálisgép-rendszerképeket az Azure CLI használatával, amelyek használatával virtuálisgép-méretezési csoportokat helyezhet üzembe
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
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8f1556f67948ec2474d713385c291efe3353c723
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663009"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Oktatóanyag: Rendszerkép létrehozása és használata egy egyéni virtuálisgép-méretezési csoportokhoz az Azure CLI-vel
Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. Egyéni virtuálisgép-rendszerkép használatával csökkentheti a feladatok számát a virtuálisgép-példányok üzembe helyezése után. Ez az egyéni virtuálisgép-rendszerkép tartalmaz minden szükséges alkalmazástelepítést és -konfigurációt. A méretezési csoportban létrehozott összes virtuálisgép-példány az egyéni virtuálisgép-rendszerképet használja, és készen állnak az alkalmazás forgalmának kiszolgálására. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Virtuális gép létrehozása és testre szabása
> * A virtuális gép megszüntetése és általánosítása
> * Egyéni virtuális gép rendszerképének létrehozása
> * Az egyéni virtuálisgép-rendszerképet használó méretezési csoport üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.29-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Forrás virtuális gép létrehozása és konfigurálása

>[!NOTE]
> Ez az oktatóanyag bemutatja az általánosított virtuálisgép-rendszerképek létrehozásának és használatának folyamatát. A méretezési csoportok speciális virtuálisgép-rendszerképekből való létrehozása nem támogatott.

Először is hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create), majd egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Ez a virtuális gép szolgál majd az egyéni virtuálisgép-rendszerkép forrásaként. A következő példában létrehozunk egy *myVM* nevű virtuális gépet a *myResourceGroup* nevű erőforráscsoportban:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép nyilvános IP-címe az [az vm create](/cli/azure/vm) parancs kimenetében található. Hozzon létre egy SSH-kapcsolatot a virtuális gép nyilvános IP-címével az alább látható módon:

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

A virtuális gép testre szabásához telepítünk egy alapszintű webkiszolgálót. A méretezési csoport virtuálisgép-példánya az üzembe helyezéskor már rendelkezni fog a webalkalmazás futtatásához szükséges összes csomaggal. Használja az `apt-get` parancsot az *NGINX* telepítéséhez az alábbi módon:

```bash
sudo apt-get install -y nginx
```

A virtuális gépnek az egyéni rendszerkép használatára való előkészítésének utolsó lépése a virtuális gép megszüntetése. Ez a lépés eltávolítja a gépspecifikus információkat a virtuális gépről, és lehetővé teszi több virtuális gép üzembe helyezését egyetlen rendszerképből. A virtuális gép megszüntetése után a gazdagépnév visszaáll a *localhost.localdomain* értékre. A rendszer törli az SSH-gazdakulcsokat, a névkiszolgáló-konfigurációkat, a rendszergazdai szintű jelszót és a gyorsítótárazott DHCP-bérleteket is.

A virtuális gép megszüntetéséhez használja az Azure virtuálisgép-ügynököt (*waagent*). Az Azure virtuálisgép-ügynök minden virtuális gépen telepítve van, és az Azure platformmal való kommunikációhoz használatos. A `-force` paraméter megadja az ügynök számára, hogy elfogadja a gépspecifikus információk visszaállítására vonatkozó kéréseket.

```bash
sudo waagent -deprovision+user -force
```

Zárja be a virtuális géppel létesített SSH-kapcsolatot:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Egyéni virtuálisgép-rendszerkép létrehozása a forrás virtuális gépről
A forrás virtuális gép most már testre van szabva, és az NGINX-webkiszolgáló is telepítve van. Hozzuk létre a méretezési csoporthoz használni kívánt egyéni virtuálisgép-rendszerképet.

Rendszerkép létrehozásához fel kell szabadítani a virtuális gépet. Szabadítsa fel a virtuális gépet az [az vm deallocate](/cli//azure/vm) paranccsal. Ezután állítsa a virtuális gép állapotát általánosított értékre az [az vm generalize](/cli//azure/vm#az_vm_generalize) paranccsal. Így az Azure platform felismeri, hogy a virtuális gép készen áll egy egyéni rendszerkép használatára. Rendszerképet csak általánosított virtuális gépből hozhat létre:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

A virtuális gép felszabadítása és általánosítása eltarthat néhány percig.

Most hozzon létre egy rendszerképet a virtuális gépről az [az image create](/cli//azure/image) paranccsal. Az alábbi példa létrehoz egy *myImage* nevű rendszerképet a virtuális gépéből:

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Méretezési csoport létrehozása az egyéni virtuálisgép-rendszerképből
Hozzon létre egy méretezési csoportot az [az vmss create](/cli/azure/vmss#az-vmss-create) paranccsal. A platform rendszerképe helyett (pl. *UbuntuLTS* vagy *CentOS*) adja meg az egyéni virtuálisgép-rendszerkép nevét. Az alábbi példa egy *myScaleSet* nevű méretezési csoportot hoz létre, amely az előző lépésben szereplő, *myImage* nevű egyéni rendszerképet használja:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
Annak érdekében, hogy a forgalom elérhesse a méretezési csoportot és hogy ellenőrizhesse a webkiszolgáló megfelelő működését, hozzon létre egy terheléselosztó-szabályt az [az network lb rule create](/cli/azure/network/lb/rule#create) paranccsal. Az alábbi példa egy *myLoadBalancerRuleWeb* nevű szabályt hoz létre, amely engedélyezi a forgalmat a *80*-as *TCP*-porton keresztül:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Ha működés közben szeretné megtekinteni a méretezési csoportot, kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip show](/cli/azure/network/public-ip#show) paranccsal. A következő példa a *myScaleSetLBPublicIP* méretezési csoport részeként létrehozott IP-címét kéri le:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Gépelje be a nyilvános IP-címet a webböngészőjébe. Az alapértelmezett NGINX-weboldal jelenik meg, ahogy az az alábbi példában is látható:

![Egyéni virtuálisgép-rendszerképről futtatott NGINX](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és további erőforrások eltávolításához törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást az [az group delete](/cli/azure/group#az_group_delete) paranccsal. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre és használhat egyéni virtuálisgép-rendszerképet a méretezési csoportjai esetében az Azure CLI-vel:

> [!div class="checklist"]
> * Virtuális gép létrehozása és testre szabása
> * A virtuális gép megszüntetése és általánosítása
> * Egyéni virtuális gép rendszerképének létrehozása
> * Az egyéni virtuálisgép-rendszerképet használó méretezési csoport üzembe helyezése

A következő oktatóanyag azt mutatja be, hogyan helyezhet alkalmazásokat üzembe a méretezési csoportban.

> [!div class="nextstepaction"]
> [Alkalmazások üzembe helyezése a méretezési csoportban](tutorial-install-apps-cli.md)
