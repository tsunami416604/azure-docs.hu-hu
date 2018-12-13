---
title: Több IP-konfiguráció az Azure CLI-vel a terheléselosztás
titlesuffix: Azure Load Balancer
description: Ismerje meg, hogy több IP-cím hozzárendelése a virtuális gépek Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: 1e8911847a555e3b6326f15d15a09344a4472f2c
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165506"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Több IP-konfiguráció az Azure CLI-vel a terheléselosztás

Ez a cikk több, a másodlagos hálózati adapter (NIC) IP-címekkel rendelkező Azure Load Balancer használatát ismerteti. Ebben a forgatókönyvben két virtuális gépet futtató Windows, minden elsődleges és a egy másodlagos hálózati adapterre van Mindegyik másodlagos hálózati adapter két IP-konfigurációval rendelkezik. Minden virtuális gép, amelyen webhelyek a contoso.com és fabrikam.com. Minden webhely van kötve, az IP-konfigurációk közül a másodlagos hálózati adapteren. Az Azure Load Balancer használatával két előtérbeli IP-cím, egy minden olyan webhelyhez, a megfelelő IP-konfigurációhoz a webhely forgalom elosztását teszi közzé. Ebben a forgatókönyvben használja ugyanazt a portszámot is előtérrendszer, valamint háttérbeli címkészlet IP-címe között.

![LB-forgatókönyv kép](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Terheléselosztás több IP-konfiguráció lépések

A következő cikkben ismertetett forgatókönyvben eléréséhez kövesse az alábbi lépéseket:

1. [Telepítse és konfigurálja az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a hivatkozott cikk és a lépéseket követve az Azure-fiókba.
2. [Hozzon létre egy erőforráscsoportot](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) nevű *contosofabrikam* módon:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Hozzon létre egy rendelkezésre állási csoport](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) , a két virtuális gép számára. A jelen esetben használja a következő parancsot:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Hozzon létre egy virtuális hálózatot](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) nevű *myVNet* és a egy alhálózat *mySubnet*:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [A load balancer létrehozása](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nevű *mylb*:

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Hozzon létre két dinamikus nyilvános IP-címet a terheléselosztó előtérbeli IP-konfigurációk esetében:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. A két előtérbeli IP-konfigurációk létrehozása *contosofe* és *fabrikamfe* jelölik:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Hozzon létre a háttér-címkészletet - *contosopool* és *fabrikampool*, amely egy [mintavételi](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*, és a betöltés terheléselosztási szabályok – *HTTPc* és *HTTPf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Ellenőrizze a kimenetet [ellenőrizze a terheléselosztó](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) megfelelően lett létrehozva a következő parancs futtatásával:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Hozzon létre egy nyilvános IP-cím](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp*, és [tárfiók](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* az első virtuális gép VM1 az alábbiak szerint:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [A hálózati adapterek létrehozása](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) vm1, és adja hozzá a második IP-konfiguráció *VM1-ipconfig2*, és [a virtuális gép létrehozása](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) módon:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. A második virtuális géphez ismételje meg a 10-11:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Végül konfigurálnia kell DNS-erőforrásrekordok, a terheléselosztó megfelelő előtérbeli IP-címre mutasson. You may host your domains in Azure DNS. Azure DNS használata a Load Balancer kapcsolatos további információkért lásd: [Azure DNS használata más Azure-szolgáltatásokkal](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>További lépések
- Többféle terheléselosztási szolgáltatás az Azure-ban történő használatáról további [terheléselosztási szolgáltatás használata az Azure-ban](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Ismerje meg, hogyan használhatja naplók különböző típusú Azure-beli kezeléséhez, és a load balancer hibaelhárítása [Log analytics az Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
