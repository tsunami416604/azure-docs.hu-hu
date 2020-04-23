---
title: Virtuális gépek közös megkeresése a jobb késés érdekében
description: Megtudhatja, hogy az Azure virtuálisgép-erőforrások közös helytkeresése hogyan javíthatja a késést.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083172"
---
# <a name="co-locate-resource-for-improved-latency"></a>Az erőforrás közös megkeresése a jobb késés érdekében

Amikor az alkalmazást az Azure-ban telepíti, a példányok régiók vagy rendelkezésre állási zónák közötti szétosztása hálózati késést hoz létre, ami hatással lehet az alkalmazás általános teljesítményére. 


## <a name="proximity-placement-groups"></a>Közelségi elhelyezési csoportok 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>További lépések

Üzembe helyezhet egy virtuális gép egy [közelségelhelyezési csoport](proximity-placement-groups.md) az Azure PowerShell használatával.

További információ a [hálózati késés teszteléséről.](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

További információ a [hálózati átviteli hang optimalizálásáról.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)  

Ismerje meg, hogyan használhatja a [közelségelhelyezési csoportokat az SAP-alkalmazásokkal.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)