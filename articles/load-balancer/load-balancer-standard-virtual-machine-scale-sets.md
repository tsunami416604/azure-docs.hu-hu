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
ms.openlocfilehash: f5a8453f84854108facb4da4616a7d362e0fb38c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531724"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer Azure-beli virtuálisgép-méretezési csoportokkal

A virtuálisgép-méretezési csoportok és a terheléselosztó használatakor figyelembe kell venni a következő irányelveket:

## <a name="multiple-virtual-machine-scale-sets-cant-use-the-same-load-balancer"></a>Több virtuálisgép-méretezési csoport nem használhatja ugyanazt a terheléselosztó
## <a name="port-forwarding-and-inbound-nat-rules"></a>A port továbbítása és a bejövő NAT-szabályok:
  * A méretezési csoport létrehozása után a háttér-port nem módosítható a terheléselosztó állapot-mintavételi eljárása által használt terheléselosztási szabályhoz. A port módosításához távolítsa el az állapot-mintavételt az Azure virtuálisgép-méretezési csoport frissítésével, frissítse a portot, majd konfigurálja újra az állapotot.
  * A terheléselosztó backend-készletében lévő virtuálisgép-méretezési csoport használatakor a rendszer automatikusan létrehozza az alapértelmezett bejövő NAT-szabályokat.
## <a name="inbound-nat-pool"></a>Bejövő NAT-készlet:
  * A virtuálisgép-méretezési csoportoknak legalább egy bejövő NAT-készlettel kell rendelkezniük. 
  * A bejövő NAT-készlet a bejövő NAT-szabályok gyűjteménye. Egy bejövő NAT-készlet nem támogatja több virtuálisgép-méretezési csoport használatát.
  
## <a name="load-balancing-rules"></a>Terheléselosztási szabályok:
  * A terheléselosztó backend-készletében lévő virtuálisgép-méretezési csoport használatakor az alapértelmezett terheléselosztási szabály automatikusan létrejön.
## <a name="outbound-rules"></a>Kimenő szabályok:
  *  Egy terheléselosztási szabály által már hivatkozott **háttér-** készletre vonatkozó kimenő szabály létrehozásához először a **"implicit kimenő szabályok létrehozása"** lehetőséget kell megadnia a portálon a bejövő terheléselosztási szabály létrehozásakor.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Terheléselosztási szabály létrehozása" border="true":::

A következő módszerek használhatók a virtuálisgép-méretezési csoport meglévő Azure Load balancerrel való üzembe helyezéséhez.

* [Egy virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer a Azure Portal használatával](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal).
* [Egy virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer Azure PowerShell használatával](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell).
* [Egy virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer az Azure CLI használatával](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli).
