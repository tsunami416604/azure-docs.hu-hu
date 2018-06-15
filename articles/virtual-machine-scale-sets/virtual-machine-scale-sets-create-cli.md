---
title: Virtuálisgép-méretezési csoport létrehozása az Azure CLI 2.0 használatával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre gyorsan virtuálisgép-méretezési csoportot az Azure PowerShellel
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 8794ea7d998293e7ea88ba780f67ef8a021f2298
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201104"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Virtuálisgép-méretezési csoport létrehozása az Azure CLI 2.0 használatával
A virtuálisgép-méretezési csoportok segítségével azonos, automatikus skálázású virtuális gépek csoportját hozhatja létre és kezelheti. A méretezési csoportban lévő virtuális gépek számát skálázhatja manuálisan, vagy megadhat automatikus skálázási szabályokat is az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Ebben az első lépéseket bemutató cikkben egy virtuálisgép-méretezési csoportot hozunk létre az Azure CLI 2.0 használatával. Méretezési csoportokat az [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) használatával, illetve az [Azure Portalon](virtual-machine-scale-sets-create-portal.md) is létrehozhat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.20-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Mielőtt létrehozhatna egy méretezési csoportot, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss#az_vmss_create) paranccsal. A következő példa egy *myScaleSet* nevű méretezési csoportot, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznének:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="install-nginx-webserver"></a>NGINX-webkiszolgáló telepítése
A méretezési csoport teszteléséhez az egyéni szkriptbővítmény használatával töltsön le és futtasson egy szkriptet, amely az NGINX-et telepíti a VM-példányokon. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. További információ: [Az egyéni szkriptbővítmény áttekintése](../virtual-machines/linux/extensions-customscript.md).

Alkalmazza az NGINX-et telepítő egyéni szkriptbővítményt a következő módon:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>Webforgalom engedélyezése
Annak érdekében, hogy a forgalom elérhesse a webkiszolgálót, hozzon létre egy terheléselosztó-szabályt az [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) paranccsal. Az alábbi példa egy *myLoadBalancerRuleWeb* nevű szabályt hoz létre:

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


## <a name="test-your-web-server"></a>A webkiszolgáló tesztelése
Ha működés közben szeretné megtekinteni a webkiszolgálót, kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) paranccsal. A következő példa a *myScaleSetLBPublicIP* a méretezési csoport részeként létrehozott IP-címét kéri le:

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Alapértelmezett weboldal az NGINX-ben](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás a következők szerint:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>További lépések
Ebben az első lépéseket bemutató cikkben egy alapszintű méretezési csoportot hoztunk létre, valamint az egyéni szkriptbővítménnyel egy alapszintű NGINX-webkiszolgálót telepítettünk a VM-példányokon. A nagyobb méretezhetőség és automatizáció érdekében bővítse méretezési csoportját az alábbi útmutatók segítségével:

- [Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokon](virtual-machine-scale-sets-deploy-app.md)
- Automatikus méretezés az [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), az [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md), illetve az [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md) használatával.
- [Az operációs rendszer automatikus frissítéseinek használata a méretezési csoport virtuálisgép-példányain](virtual-machine-scale-sets-automatic-upgrade.md)