---
title: Terheléselosztás több IP-konfiguráción az Azure CLI használatával
titleSuffix: Azure Load Balancer
description: Megtudhatja, hogyan rendelhet több IP-címet egy virtuális géphez az Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 69d324647af014a5122c404929c104a9077d5f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225306"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Terheléselosztás több IP-konfiguráción az Azure CLI használatával

Ez a cikk ismerteti, hogyan használhatja az Azure Load Balancer több IP-címegy másodlagos hálózati adapter (NIC). Ebben a forgatókönyvben két Windows-t futtató virtuális gépvan, mindegyik elsődleges és másodlagos hálózati adapterrel. A másodlagos hálózati adapterek mindegyike két IP-konfigurációval rendelkezik. Minden virtuális gép contoso.com és fabrikam.com webhelyeket is üzemeltet. Minden webhely a másodlagos hálózati adapter egyik IP-konfigurációjához van kötve. Az Azure Load Balancer segítségével két előtér-IP-címet, egyet-egyet a webhelyeken, a forgalom a megfelelő IP-konfiguráció a webhely reked. Ebben a forgatókönyvben ugyanazt a portszámot használja mindkét előtér-rendszerek között, valamint mindkét háttérkészlet IP-címét.

![LB-forgatókönyv képe](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Több IP-konfiguráció terhelésének lépései

A cikkben ismertetett forgatókönyv eléréséhez hajtsa végre az alábbi lépéseket:

1. [Telepítse és konfigurálja az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a csatolt cikkben leírt lépések végrehajtásával, és jelentkezzen be az Azure-fiókjába.
2. [Hozzon létre egy](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) *contosofabrikam* nevű erőforráscsoportot az alábbiak szerint:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Hozzon létre egy rendelkezésre állási készletet](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) a két virtuális géphez. Ebben az esetben használja a következő parancsot:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Hozzon létre egy](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) *myVNet* nevű virtuális hálózatot és egy *mySubnet*nevű alhálózatot:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. Hozza létre a *mylb*nevű [terheléselosztót:](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Hozzon létre két dinamikus nyilvános IP-címet a terheléselosztó előtér-IP-konfigurációihoz:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Hozza létre a két előtér-IP-konfigurációt, a *contosofe-t* és a *fabrikamfe-t:*

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Hozza létre háttércímkészleteit - *contosopool* és *fabrikampool*, egy -  [http-mintavétel,](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json)valamint a terheléselosztási szabályok - *HTTPc* és *HTTPf*:*HTTP*

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Ellenőrizze a kimenetet, és ellenőrizze, hogy [a terheléselosztó](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) megfelelően lett-e létrehozva a következő parancs futtatásával:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Hozzon létre egy nyilvános IP,](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address) *myPublicIp*, és [tárfiók](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* az első virtuális gép VM1 az alábbiak szerint:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Hozza létre a virtuális gép1 hálózati csatolóit,](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) és adjon hozzá egy második *IP-konfigurációt, a VM1-ipconfig2-t,* és [hozza létre a virtuális gép](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) létrehozását az alábbiak szerint:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Ismételje meg a 10-11.

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Végül be kell állítania a DNS-erőforrásrekordokat úgy, hogy a terheléselosztó megfelelő előtér-IP-címére mutassanak. A tartományokat az Azure DNS-ben üzemeltetheti. Az Azure DNS és a terheléselosztó használatával kapcsolatos további tudnivalókért olvassa el [az Azure DNS használata más Azure-szolgáltatásokkal című témakört.](../dns/dns-for-azure-services.md)

## <a name="next-steps"></a>További lépések
- További információ a terheléselosztási szolgáltatások Azure-beli kombinálásáról [az Azure terheléselosztási szolgáltatásainak használata során.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- Megtudhatja, hogy miként használhat különböző típusú naplókat az Azure-ban a terheléselosztó kezeléséhez és hibaelhárításához [az Azure Load Balancer naplóelemzésében.](../load-balancer/load-balancer-monitor-log.md)
