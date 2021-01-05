---
title: A virtuálisgép-méretezési csoport által használt meglévő Azure Load Balancer frissítése vagy törlése
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: Ezzel a cikkből megtudhatja, hogyan kezdheti el az Azure standard Load Balancer és az Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: f8f664375e53a1cef28b0c7b95207770434f67fa
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97893267"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets által használt Azure Load Balancer frissítése/törlése

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Azure Load Balancer beállítása a horizontális felskálázáshoz Virtual Machine Scale Sets
  * Győződjön meg arról, hogy a Load Balancer be van állítva a [bejövő NAT-készlet](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) , és hogy a virtuálisgép-méretezési csoport be van helyezve a Load Balancer háttér-készletbe. A Azure Load Balancer automatikusan új bejövő NAT-szabályokat hoz létre a bejövő NAT-készletben, amikor új virtuálisgép-példányok kerülnek a virtuálisgép-méretezési csoportba. 
  * Annak ellenőrzését, hogy a bejövő NAT-készlet megfelelően van-e beállítva, 
  1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
  
  1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
  
  1. A **Beállítások** területen válassza a **bejövő NAT-szabályok** elemet.
Ha a jobb oldali ablaktáblán megjelenik a virtuálisgép-méretezési csoport minden egyes példányához létrehozott szabályok listája, akkor az összes olyan korláttal rendelkezik, amelyhez a rendszer bármikor felskálázást végez.

## <a name="how-to-add-inbound-nat-rules"></a>Bejövő NAT-szabályok hozzáadása 
  * Az egyes bejövő NAT-szabályok nem vehetők fel. A virtuálisgép-méretezési csoport összes példányához azonban hozzáadhat egy bejövő NAT-szabályt a megadott előtér-porttartomány és a háttér-port használatával.
  * Ahhoz, hogy a Virtual Machine Scale Setshoz a bejövő NAT-szabályok teljes készletét hozzá lehessen adni, először létre kell hoznia egy bejövő NAT-készletet a Load Balancerban, majd a bejövő NAT-készletet a virtuálisgép-méretezési csoport hálózati profiljában kell hivatkoznia. A CLI-t használó teljes példa alább látható.
  * Az új bejövő NAT-készlet nem rendelkezhet átfedésben lévő előtér-porttartomány meglévő bejövő NAT-készletekkel. Ha meg szeretné tekinteni a meglévő bejövő NAT-készleteket, használhatja ezt a [CLI-parancsot](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list) .
```azurecli-interactive
az network lb inbound-nat-pool create 
        -g MyResourceGroup 
        --lb-name MyLb
        -n MyNatPool 
        --protocol Tcp 
        --frontend-port-range-start 80 
        --frontend-port-range-end 89 
        --backend-port 80 
        --frontend-ip-name MyFrontendIp
az vmss update 
        -g MyResourceGroup 
        -n myVMSS 
        --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
        
az vmss update-instances
        -–instance-ids *
        --resource-group MyResourceGroup
        --name MyVMSS
```
## <a name="how-to-update-inbound-nat-rules"></a>Hogyan lehet frissíteni a bejövő NAT-szabályokat? 
  * Az egyes bejövő NAT-szabályok nem frissíthetők. A virtuálisgép-méretezési csoport összes példánya esetében azonban frissítheti a bejövő NAT-szabályok készletét a megadott előtér-porttartomány és a háttér-port használatával.
  * A Virtual Machine Scale Sets összes bejövő NAT-szabályának frissítéséhez frissítenie kell a bejövő NAT-készletet a Load Balancerban. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Hogyan lehet törölni a bejövő NAT-szabályokat? 
* Az egyes bejövő NAT-szabályok nem törölhetők. Azonban törölheti a bejövő NAT-szabályok teljes készletét.
* A méretezési csoport által használt bejövő NAT-szabályok teljes készletének törléséhez először el kell távolítania a NAT-készletet a méretezési csoportból. A CLI-t használó teljes példa alább látható:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
   az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```

## <a name="how-to-add-multiple-ip-configurations"></a>Több IP-konfiguráció hozzáadása:
1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** területen válassza a előtér **IP-konfigurációk** elemet, majd kattintson a **Hozzáadás** gombra.
   
1. Az előtérbeli **IP-cím hozzáadása** lapon írja be az értékeket, majd kattintson az **OK gombra** .

1. Kövesse az oktatóanyag [5. lépését](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) és [6. lépését](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) , ha új terheléselosztási szabályokra van szükség

1. Szükség esetén hozza létre a bejövő NAT-szabályok új készletét az újonnan létrehozott előtéri IP-konfigurációk használatával. Például itt található az [előző szakasz].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport által használt előtér-IP-konfiguráció törlése: 
 1. A méretezési csoport által használt előtéri IP-konfiguráció törléséhez először törölnie kell a bejövő NAT-készletet (a bejövő NAT-szabályok készletét), amelyek az előtérbeli IP-konfigurációra hivatkoznak. A bejövő szabályok törlésének utasításait az előző szakaszban találja.
 1. Törölje az előtérbeli IP-konfigurációra hivatkozó terheléselosztási szabályt. 
 1. Az előtérbeli IP-konfiguráció törlése.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport által használt Azure Load Balancer törlése: 
 1. A méretezési csoport által használt előtéri IP-konfiguráció törléséhez először törölnie kell a bejövő NAT-készletet (a bejövő NAT-szabályok készletét), amelyek az előtérbeli IP-konfigurációra hivatkoznak. A bejövő szabályok törlésének utasításait az előző szakaszban találja.
 
 1. Törölje a virtuálisgép-méretezési készletet tartalmazó háttér-készletre hivatkozó terheléselosztási szabályt.
 
 1. Távolítsa el a loadBalancerBackendAddressPool hivatkozást a virtuálisgép-méretezési csoport hálózati profiljából. A CLI-t használó teljes példa alább látható:
 ```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
  az vmss update-instances 
     --instance-ids "*" 
     --resource-group MyResourceGroup
     --name MyVMSS
```
Végezetül törölje a Load Balancer erőforrást.
 
## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Load Balancer és a virtuálisgép-méretezési csoportról, olvassa el a fogalmakat ismertető témakört.

> [Azure Load Balancer Azure-beli virtuálisgép-méretezési csoportokkal](load-balancer-standard-virtual-machine-scale-sets.md)
