---
title: Az Azure Standard Load Balancer és a Virtual Machine Scale Sets
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
ms.openlocfilehash: 7e1df754a4a4ca5878d93d53282fd39191313b54
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883163"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer Azure-beli virtuálisgép-méretezési csoportokkal

A virtuálisgép-méretezési csoportok és a terheléselosztó használatakor figyelembe kell venni a következő irányelveket:

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

* [Egy virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer a Azure Portal használatával](./configure-vm-scale-set-portal.md).
* [Egy virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer Azure PowerShell használatával](./configure-vm-scale-set-powershell.md).
* [Egy virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer az Azure CLI használatával](./configure-vm-scale-set-cli.md).
* [A virtuálisgép-méretezési csoport által használt meglévő Azure Load Balancer frissítése vagy törlése](./update-load-balancer-with-vm-scale-set.md)
