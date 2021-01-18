---
title: 'Rövid útmutató: belső terheléselosztó létrehozása – Azure CLI'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozható létre belső terheléselosztó az Azure CLI használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: edf893f1f6ba0691da5764420017282d7a8bde84
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562811"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rövid útmutató: belső terheléselosztó létrehozása a virtuális gépek terheléselosztásához az Azure CLI használatával

Ismerkedjen meg Azure Load Balancerekkel az Azure CLI használatával belső terheléselosztó és három virtuális gép létrehozásához.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az_group_create)paranccsal:

* **CreateIntLBQS-RG** névvel ellátott. 
* A **eastus** helyen.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```
---

# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

Ebben a szakaszban létrehoz egy terheléselosztó, amely terheléselosztást végez a virtuális gépeken. 

Belső terheléselosztó létrehozásakor a rendszer a terheléselosztó hálózatként konfigurálja a virtuális hálózatot. 

Az alábbi ábrán az ebben a rövid útmutatóban létrehozott erőforrások láthatók:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="A gyors üzembe helyezéshez létrehozott Standard Load Balancer-erőforrások." border="false":::

## <a name="configure-virtual-network---standard"></a>Virtuális hálózat konfigurálása – standard

A virtuális gépek üzembe helyezése és a terheléselosztó üzembe helyezése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal:

* Elnevezett **myVNet**.
* A **10.1.0.0/16** címnek az előtagja.
* **MyBackendSubnet** nevű alhálózat.
* A **10.1.0.0/24** alhálózati előtag.
* A **CreateIntLBQS-RG** erőforráscsoporthoz.
* A **eastus** helye.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myBastionIP** néven.
* **CreateIntLBQS – RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Megerősített alhálózat létrehozása

Az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) paranccsal hozzon létre egy megerősített alhálózatot:

* Elnevezett **AzureBastionSubnet**.
* A **10.1.1.0/24** címek előtagja.
* A virtuális hálózat **myVNet**.
* Az erőforráscsoport **CreateIntLBQS – RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Megerősített gazdagép létrehozása

Az [az Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) paranccsal hozzon létre egy megerősített gazdagépet:

* Elnevezett **myBastionHost**.
* **CreateIntLBQS – RG**.
* Nyilvános IP- **myBastionIP** társítva.
* Virtuális hálózati **myVNet** társítva.
* A **eastus** helyen.

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Az Azure Bastion-gazdagép üzembe helyezése néhány percet is igénybe vehet.


### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A standard Load Balancer esetében a háttérbeli címen lévő virtuális gépeknek a hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell rendelkezniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)paranccsal:

* Elnevezett **myNSG**.
* Az erőforráscsoport **CreateIntLBQS – RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt [az az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)paranccsal:

* Elnevezett **myNSGRuleHTTP**.
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* Protokoll **(*)**.
* Irány **bejövő**.
* Forrás **(*)**.
* Cél **(*)**.
* Célport **portszáma 80**.
* Hozzáférés **engedélyezése**.
* **200** prioritás.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Háttér-kiszolgálók létrehozása – standard

Ebben a szakaszban a következőket hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* Három virtuális gép, amely háttér-kiszolgálóként használható a terheléselosztó számára.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal:

* Elnevezett **myNicVM1**, **myNicVM2** és **myNicVM3**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal:

* Elnevezett **myVM1**, **myVM2** és **myVM3**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM1**, **myNicVM2** és **myNicVM3**.
* A virtuális gép rendszerképének **win2019datacenter**.
* **1. zóna**, **2. zóna** és **3. zóna**.

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.

## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Hozzon létre egy nyilvános Load balancert az [az Network LB Create](/cli/azure/network/lb#az-network-lb-create)paranccsal:

* Elnevezett **myLoadBalancer**.
* Egy **myFrontEnd** nevű frontend-készlet.
* Egy **myBackEndPool** nevű háttér-készlet.
* A virtuális hálózat **myVNet** társítva.
* A háttérbeli alhálózat **myBackendSubnet** van társítva.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [az Network LB Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create)paranccsal:

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP** protokoll.
* A **80**-es port figyelése.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [az Network LB Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create)paranccsal:

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd**.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton** keresztül. 
* A Health mintavételi **myHealthProbe** használata.
* **TCP** protokoll.
* A **15 perc** üresjárati időkorlátja.
* Engedélyezze a TCP-visszaállítást.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása a terheléselosztó háttérbeli készletéhez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)paranccsal:

* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* A hálózati adapter **myNicVM1**, **myNicVM2** és **myNicVM3** van társítva.
* A terheléselosztó **myLoadBalancer** van társítva.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

Ebben a szakaszban létrehoz egy terheléselosztó, amely terheléselosztást végez a virtuális gépeken. 

Belső terheléselosztó létrehozásakor a rendszer a terheléselosztó hálózatként konfigurálja a virtuális hálózatot. 

Az alábbi ábrán az ebben a rövid útmutatóban létrehozott erőforrások láthatók:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="A rövid útmutatóban létrehozott alapszintű Load Balancer-erőforrások." border="false":::

## <a name="configure-virtual-network---basic"></a>Virtuális hálózat konfigurálása – alapszintű

A virtuális gépek üzembe helyezése és a terheléselosztó üzembe helyezése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-createt)paranccsal:

* Elnevezett **myVNet**.
* A **10.1.0.0/16** címnek az előtagja.
* **MyBackendSubnet** nevű alhálózat.
* A **10.1.0.0/24** alhálózati előtag.
* A **CreateIntLBQS-RG** erőforráscsoporthoz.
* A **eastus** helye.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myBastionIP** néven.
* **CreateIntLBQS – RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Megerősített alhálózat létrehozása

Az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) paranccsal hozzon létre egy megerősített alhálózatot:

* Elnevezett **AzureBastionSubnet**.
* A **10.1.1.0/24** címek előtagja.
* A virtuális hálózat **myVNet**.
* Az erőforráscsoport **CreateIntLBQS – RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Megerősített gazdagép létrehozása

Az [az Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) paranccsal hozzon létre egy megerősített gazdagépet:

* Elnevezett **myBastionHost**.
* **CreateIntLBQS – RG**.
* Nyilvános IP- **myBastionIP** társítva.
* Virtuális hálózati **myVNet** társítva.
* A **eastus** helyen.

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Az Azure Bastion-gazdagép üzembe helyezése néhány percet is igénybe vehet.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A standard Load Balancer esetében a háttérbeli címen lévő virtuális gépeknek a hálózati biztonsági csoporthoz tartozó hálózati adapterekkel kell rendelkezniük. 

Hozzon létre egy hálózati biztonsági csoportot [az az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)paranccsal:

* Elnevezett **myNSG**.
* Az erőforráscsoport **CreateIntLBQS – RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt [az az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)paranccsal:

* Elnevezett **myNSGRuleHTTP**.
* Az előző lépésben létrehozott hálózati biztonsági csoport **myNSG**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* Protokoll **(*)**.
* Irány **bejövő**.
* Forrás **(*)**.
* Cél **(*)**.
* Célport **portszáma 80**.
* Hozzáférés **engedélyezése**.
* **200** prioritás.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Háttér-kiszolgálók létrehozása – alapszintű

Ebben a szakaszban a következőket hozza létre:

* Három hálózati adapter a virtuális gépekhez.
* Rendelkezésre állási csoport a virtuális gépekhez
* Három virtuális gép, amely háttér-kiszolgálóként használható a terheléselosztó számára.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Hálózati adapterek létrehozása a virtuális gépekhez

Hozzon létre három hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal:

* Elnevezett **myNicVM1**, **myNicVM2** és **myNicVM3**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-availability-set-for-virtual-machines"></a>Rendelkezésre állási csoport létrehozása a virtuális gépekhez

Hozza létre a rendelkezésre állási készletet az [az VM rendelkezésre állása-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create)paranccsal:

* Elnevezett **myAvailabilitySet**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* Hely **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozza létre a virtuális gépeket az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal:

* Elnevezett **myVM1**, **myVM2** és **myVM3**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* Csatolva a hálózati adapter **myNicVM1**, **myNicVM2** és **myNicVM3**.
* A virtuális gép rendszerképének **win2019datacenter**.
* A **myAvailabilitySet**.


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
A virtuális gépek üzembe helyezése néhány percet is igénybe vehet.

## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Hozzon létre egy nyilvános Load balancert az [az Network LB Create](/cli/azure/network/lb#az-network-lb-create)paranccsal:

* Elnevezett **myLoadBalancer**.
* Egy **myFrontEnd** nevű frontend-készlet.
* Egy **myBackEndPool** nevű háttér-készlet.
* A virtuális hálózat **myVNet** társítva.
* A háttérbeli alhálózat **myBackendSubnet** van társítva.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [az Network LB Probe Create](/cli/azure/network/lb/probe#az-network-lb-probe-create)paranccsal:

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP** protokoll.
* A **80**-es port figyelése.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [az Network LB Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create)paranccsal:

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd**.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton** keresztül. 
* A Health mintavételi **myHealthProbe** használata.
* **TCP** protokoll.
* A **15 perc** üresjárati időkorlátja.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Virtuális gépek hozzáadása a terheléselosztó háttérbeli készletéhez

Adja hozzá a virtuális gépeket a háttér-készlethez az [az Network NIC IP-config cím-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add)paranccsal:

* A háttérbeli címkészlet **myBackEndPool**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* A hálózati adapter **myNicVM1**, **myNicVM2** és **myNicVM3** van társítva.
* A terheléselosztó **myLoadBalancer** van társítva.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

### <a name="create-test-virtual-machine"></a>Teszt virtuális gép létrehozása

Hozza létre a hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal:

* Elnevezett **myNicTestVM**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Hozza létre a virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal:

* Elnevezett **myTestVM**.
* Az erőforráscsoport **CreateIntLBQS – RG**.
* Csatolva a hálózati adapter **myNicTestVM**.
* A virtuális gép rendszerképének **Win2019Datacenter**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
A virtuális gép üzembe helyezése néhány percet is igénybe vehet.

## <a name="install-iis"></a>Az IIS telepítése

Az [az VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) paranccsal telepítse az IIS-t a virtuális gépekre, és állítsa az alapértelmezett webhelyet a számítógép nevére.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Tesztelés

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A terheléselosztó magánhálózati IP-címének megkeresése az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd válassza a **myLoadBalancer** lehetőséget.

3. Jegyezze fel, vagy másolja a **magánhálózati IP-cím** melletti címet a **myLoadBalancer** **áttekintésében** .

4. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza ki a **myTestVM** , amely a **CreateIntLBQS-RG** erőforráscsoporthoz található.

5. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. Nyissa meg az **Internet Explorert** a **myTestVM**.

8. Az előző lépésben adja meg az IP-címet a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Standard belső terheléselosztó létrehozása" border="true":::
   
Ha meg szeretné tekinteni, hogy a terheléselosztó mindhárom virtuális gépen osztja el a forgalmat, testreszabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett oldalát, majd kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal távolítsa el az erőforráscsoportot, a Load balancert és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban:

* Létrehozta a standard vagy a nyilvános terheléselosztó
* Csatlakoztatott virtuális gépek. 
* Konfigurálta a terheléselosztó forgalmi szabályát és az állapot-mintavételt.
* Tesztelte a terheléselosztó.

Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a következővel:
> [!div class="nextstepaction"]
> [Mi az Azure Load Balancer?](load-balancer-overview.md)