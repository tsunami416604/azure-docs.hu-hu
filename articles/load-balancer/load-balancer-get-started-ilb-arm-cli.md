---
title: "Belső terheléselosztó létrehozása – Azure CLI | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre belső terheléselosztó az Azure parancssori felület használatával a Resource Managerben"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: c7a24e92-b4da-43c0-90f2-841c1b7ce489
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: bd1f5e497bcf39a0d8848cc63c718e693f775d63

---

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Belső terheléselosztó létrehozása az Azure parancssori felület használatával

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Sablon](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti, amelyet a Microsoft a legtöbb új telepítéshez a [klasszikus üzemi modell](load-balancer-get-started-ilb-classic-cli.md) helyett javasol.

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>A megoldás üzembe helyezése az Azure parancssori felület használatával

A következő lépések bemutatják, hogyan lehet internetes elérésű terheléselosztót létrehozni az Azure Resource Manager és a parancssori felület használatával. Az Azure Resource Manager lehetővé teszi, hogy az egyes erőforrások konfigurálása egyenként történjen, majd az összerakásukkal jöjjön létre egy erőforrás.

A terheléselosztó üzembe helyezéséhez a következő objektumokat kell létrehozni és konfigurálni:

* **Előtérbeli IP-konfiguráció**: a nyilvános IP-címeket tartalmazza a bejövő hálózati forgalomhoz
* **Háttércímkészlet**: hálózati adaptereket (NIC) tartalmaz, amelyek lehetővé teszik a virtuális gépek számára a terheléselosztóról érkező hálózati forgalom fogadását
* **Terheléselosztási szabályok**: olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját rendelik hozzá a háttér címkészletben levő porthoz
* **Bejövő NAT-szabályok**: olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá egy adott virtuális gép portjához a háttércímkészletben
* **Mintavételezők**: állapotfigyelő mintavételezőket tartalmaz, amelyek a virtuálisgép-példányok rendelkezésre állását ellenőrzik a háttércímkészletben

A további információkat az [Azure Resource Manager support for Load Balancer](load-balancer-arm.md) (Azure Resource Manager-támogatás a terheléselosztóhoz) című rész tartalmazza.

## <a name="set-up-cli-to-use-resource-manager"></a>A parancssori felület beállítása a Resource Manager használatához

1. Ha még soha nem használta az Azure parancssori felületét, lásd: [Install and configure the Azure CLI](../xplat-cli-install.md) (Azure parancssori felület (CLI) telepítése és konfigurálása). Kövesse az utasításokat az Azure-fiók és -előfizetés kiválasztásáig.
2. Az **azure config mode** parancs futtatásával váltson Resource Manager módra, a következők szerint:

    ```azurecli
    azure config mode arm
    ```

    Várt kimenet:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Belső terheléselosztó létrehozásának lépései

1. Jelentkezzen be az Azure-ba.

    ```azurecli
    azure login
    ```

    Amikor a rendszer erre kéri, adja meg Azure rendszerbeli hitelesítő adatait.

2. Módosítsa a parancssori eszközöket az Azure Resource Manager módra.

    ```azurecli
    azure config mode arm
    ```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Resource Managerben minden erőforrás egy erőforráscsoporthoz van társítva. Ha még nem tette meg, hozzon létre egy erőforráscsoportot.

```azurecli
azure group create <resource group name> <location>
```

## <a name="create-an-internal-load-balancer-set"></a>Hozzon létre egy belső terheléselosztó készletet

1. Hozzon létre egy belső terheléselosztót

    A következő forgatókönyv esetében az nrprg nevű erőforráscsoportot hozzuk létre az Egyesült Államok keleti régiójában.

    ```azurecli
    azure network lb create --name nrprg --location eastus
    ```

   > [!NOTE]
   > A belső terheléselosztók összes erőforrásának (például virtuális hálózatok és virtuális alhálózatok) ugyanabban az erőforráscsoportban és ugyanabban a régióban kell lennie.

2. Hozzon létre egy előtérbeli IP-címet a belső terheléselosztó számára.

    Az IP-címnek kötelező a virtuális hálózat alhálózati tartományában lennie.

    ```azurecli
    azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet
    ```

3. Hozza létre a háttércímkészletet.

    ```azurecli
    azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb
    ```

    Miután megtörtént az előtérbeli IP-cím, valamint a háttércímkészlet definiálása, létre lehet hozni a terheléselosztási szabályokat, a bejövő NAT-szabályokat, valamint a testre szabott állapotfigyelő mintavételezőket.

4. Hozzon létre egy terheléselosztási szabályt a belső terheléselosztóhoz.

    Az előző lépések végrehajtásakor a parancs olyan terheléselosztási szabályt hoz létre, amelyik az 1433-as portot figyeli az előtérkészletben, és a háttér címkészletre szintén az 1433-as port használatával küldi az elosztott terhelésű hálózati forgalmat.

    ```azurecli
    azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb
    ```

5. Hozza létre a bejövő NAT-szabályokat.

    A bejövő NAT-szabályok olyan végpontok létrehozásához használhatók a terheléselosztóban, amelyek egy adott virtuálisgép-példányhoz tartoznak. Az előző lépések két NAT-szabályt hoztak létre a távoli asztalhoz.

    ```azurecli
    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389
    ```

6. Hozzon létre állapotfigyelő mintavételezőket a terheléselosztóhoz.

    Az állapotfigyelő mintavételező az összes virtuálisgép-példányt ellenőrzi, hogy biztosan képesek legyenek hálózati forgalom küldésére. A mintavételező tesztjén elbukó virtuálisgép-példányokat a rendszer eltávolítja a terheléselosztóból, és így is maradnak, amíg ismét online állapotúak nem lesznek, és a mintavételező tesztje azt nem jelzi, hogy megfelelő az állapotuk.

    ```azurecli
    azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4
    ```

    > [!NOTE]
    > A Microsoft Azure platform egy statikus, nyilvánosan irányítható IPv4-címet használ számos különböző felügyeleti forgatókönyvhöz. Az IP-cím a 168.63.129.16. Ezt az IP-címet nem blokkolhatja egy tűzfal sem, mert ez nem várt viselkedést okozhat.
    > Az Azure belső terheléselosztás esetében ezt az IP-címet használják a terheléselosztó figyelési mintavételezői az elosztott terhelésű készlet virtuális gépeinek állapotmeghatározásához. Ha egy belső elosztott terhelésű készletben hálózati biztonsági csoportot használnak az Azure virtuális gépekre irányuló forgalom korlátozásához, vagy egy virtuális alhálózaton alkalmazzák, győződjön meg arról, hogy a 168.63.129.16 címről érkező adatforgalom engedélyezéséhez felvettek egy hálózati biztonsági szabályt.

## <a name="create-nics"></a>Hálózati adapterek létrehozása

Hálózati adaptereket kell létrehoznia (vagy a meglévőket is módosíthatja), és hozzá kell rendelnie őket a NAT-szabályokhoz, a terheléselosztási szabályokhoz és a mintavételezőkhöz.

1. Hozzon létre egy hálózati adaptert *lb-nic1-be* néven, majd társítsa az *rdp1* NAT-szabályhoz és a *beilb* háttércímkészlethez.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus
    ```

    Várt kimenet:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Hozzon létre egy hálózati adaptert *lb-nic2-be* néven, majd társítsa az *rdp2* NAT-szabályhoz és a *beilb* háttércímkészlethez.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus
    ```

3. Hozzon létre egy virtuális gépet *DB1* néven, és társítsa az *lb-nic1-be* nevű hálózati adapterrel. A következő parancs futtatása előtt létrejön a *web1nrp* nevű tárfiók:

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```
    > [!IMPORTANT]
    > A terheléselosztó virtuális gépeinek ugyanabban a rendelkezésre állási készletben kell lenniük. Használja az `azure availset create` parancsot a rendelkezésre állási készlet létrehozásához.

4. Hozzon létre egy virtuális gépet (VM) *DB2* néven, és társítsa az *lb-nic2-be* nevű hálózati adapterhez. A következő parancs futtatása előtt létrejön a *web1nrp* nevű tárfiók.

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="delete-a-load-balancer"></a>Terheléselosztó törlése

A terheléselosztó eltávolításához használja a következő parancsot:

```azurecli
azure network lb delete --resource-group nrprg --name ilbset
```

## <a name="next-steps"></a>Következő lépések

[A terheléselosztó elosztási módjának konfigurálása forrás IP-affinitás használatával](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Jan17_HO4-->


