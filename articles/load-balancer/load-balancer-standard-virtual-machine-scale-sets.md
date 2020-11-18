---
title: Azure standard Load Balancer és Virtual Machine Scale Sets
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: Ezzel a képzési útvonallal megismerheti az Azure standard Load Balancer és a Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: fdca40d5113f06d185255be2e237cb52b47e9793
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697441"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer Azure-beli virtuálisgép-méretezési csoportokkal

A virtuálisgép-méretezési csoportok és a terheléselosztó használatakor figyelembe kell venni a következő irányelveket:

## <a name="port-forwarding-and-inbound-nat-rules"></a>A port továbbítása és a bejövő NAT-szabályok:
  * A méretezési csoport létrehozása után a háttér-port nem módosítható a terheléselosztó állapot-mintavételi eljárása által használt terheléselosztási szabályhoz. A port módosításához távolítsa el az állapot-mintavételt az Azure virtuálisgép-méretezési csoport frissítésével, frissítse a portot, majd konfigurálja újra az állapotot.
  * A terheléselosztó backend-készletében lévő virtuálisgép-méretezési csoport használatakor a rendszer automatikusan létrehozza az alapértelmezett bejövő NAT-szabályokat.
## <a name="inbound-nat-pool"></a>Bejövő NAT-készlet:
  * A virtuálisgép-méretezési csoportoknak legalább egy bejövő NAT-készlettel kell rendelkezniük. 
  * A bejövő NAT-készlet a bejövő NAT-szabályok gyűjteménye. Egy bejövő NAT-készlet nem támogatja több virtuálisgép-méretezési csoport használatát.
  * NAT-készlet meglévő virtuálisgép-méretezési csoportból való törléséhez először el kell távolítania a NAT-készletet a méretezési csoportból. A CLI-t használó teljes példa alább látható:
```azurecli-interactive
  az vmss update
     --resource-group MyResourceGroup
     --name MyVMSS
     --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
  az vmss update-instances
     -–instance-ids *
     --resource-group MyResourceGroup
     --name MyVMSS
  az network lb inbound-nat-pool delete
     --resource-group MyResourceGroup
     -–lb-name MyLoadBalancer
     --name MyNatPool
```
## <a name="load-balancing-rules"></a>Terheléselosztási szabályok:
  * A terheléselosztó backend-készletében lévő virtuálisgép-méretezési csoport használatakor az alapértelmezett terheléselosztási szabály automatikusan létrejön.
## <a name="outbound-rules"></a>Kimenő szabályok:
  *  Egy terheléselosztási szabály által már hivatkozott **háttér-** készletre vonatkozó kimenő szabály létrehozásához először a **"implicit kimenő szabályok létrehozása"** lehetőséget kell megadnia a portálon a bejövő terheléselosztási szabály létrehozásakor.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Terheléselosztási szabály létrehozása" border="true":::

A következő módszerek használhatók a virtuálisgép-méretezési csoport meglévő Azure Load balancerrel való üzembe helyezéséhez.

* [Egy virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer a Azure Portal használatával](./configure-vm-scale-set-portal.md).
* [Egy virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer Azure PowerShell használatával](./configure-vm-scale-set-powershell.md).
* [Egy virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer az Azure CLI használatával](./configure-vm-scale-set-cli.md).