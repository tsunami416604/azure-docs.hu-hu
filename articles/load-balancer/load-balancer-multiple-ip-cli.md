---
title: "Az Azure parancssori felület használatával több IP-konfigurációk terheléselosztási |} Microsoft Docs"
description: "Megtudhatja, hogyan több IP-címek hozzárendelése a virtuális gép Azure parancssori felületével |} Erőforrás-kezelő."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 23dd3bf6a50a30477014081a955c3c154b765c5d
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="load-balancing-on-multiple-ip-configurations"></a>Terheléselosztás több IP-konfigurációk

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [Parancssori felület](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Ez a cikk ismerteti az Azure Load Balancer használata a másodlagos hálózati adapteren (NIC) több IP-címmel. Ebben a forgatókönyvben két virtuális gépeken futó Windows, az elsődleges és másodlagos hálózati tudunk A másodlagos hálózati adapterek mindegyike rendelkezik, két IP-konfigurációk. Minden virtuális gép webhelyeket a contoso.com és fabrikam.com üzemelteti. Minden webhelyre van kötve egy IP-konfigurációk másodlagos hálózati adapteren Azure Load Balancer használatával teszi közzé a két előtérbeli IP-cím, egy, a megfelelő IP-konfiguráció a webhelyre irányuló forgalom terjeszteni minden webhelyre vonatkozóan. Ebben a példában ugyanazt a portszámot is frontends, valamint mindkét háttér címkészletet IP-címek között.

![Terheléselosztó forgatókönyv kép](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>A több IP-konfigurációk terheléselosztásához lépései

A cikkben ismertetett forgatókönyvben elérése érdekében kövesse az alábbi lépéseket:

1. [Telepítse és konfigurálja az Azure parancssori felület](../cli-install-nodejs.md) lépésekkel a csatolt cikk, majd jelentkezzen be Azure-fiókjába.
2. [Hozzon létre egy erőforráscsoportot](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) nevű *contosofabrikam* az alábbiak szerint:

    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. [Egy rendelkezésre állási csoport létrehozása](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) való lévő két virtuális géphez. A jelen esetben használja a következő parancsot:

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Hozzon létre egy virtuális hálózatot](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) nevű *myVNet* és alhálózat nevű *mySubnet*:

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. [A terheléselosztó létrehozása](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nevű *mylb*:

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Hozzon létre két dinamikus nyilvános IP-címet a terheléselosztó előtérbeli IP-konfigurációk:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. A két előtérbeli IP-konfigurációk létrehozása *contosofe* és *fabrikamfe* osztályban:

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. A háttéralkalmazás - címkészletek létrehozása *contosopool* és *fabrikampool*, egy [mintavételi](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) - *HTTP*, és a terheléselosztási szabályok - *HTTPc* és *HTTPf*:

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. Ellenőrizze a kimenet [ellenőrizze a terheléselosztó](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) megfelelően lett létrehozva a következő parancs futtatásával:

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Hozzon létre egy nyilvános IP-cím](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *myPublicIp*, és [tárfiók](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json), *mystorageaccont1* az első virtuális gépen VM1 az alábbiak szerint:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [A hálózati adapterek létrehozása](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) VM1 számára, és adja hozzá a második IP-konfiguráció *VM1-ipconfig2*, és [a virtuális gép létrehozása](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) az alábbiak szerint:

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. A második virtuális gép ismételje meg a 10-11:

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Végül konfigurálnia kell DNS-erőforrásrekordok a terheléselosztó megfelelő előtérbeli IP-címére mutasson. You may host your domains in Azure DNS. Az Azure DNS-sel terheléselosztással kapcsolatos további információkért lásd: [Azure DNS használata más Azure-szolgáltatásokkal](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>További lépések
- További tudnivalók az Azure a terheléselosztási egyesítése [terheléselosztás szolgáltatások használata az Azure-ban](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Ismerje meg, hogyan használhatja különféle naplók az Azure-ban kezelésére és hibaelhárítására terheléselosztó [analytics keresse meg a Azure terheléselosztó](../load-balancer/load-balancer-monitor-log.md).
