---
title: Linuxos virtuális gépek közös megkeresése
description: Megtudhatja, hogy az Azure virtuálisgép-erőforrások közös helytkeresése hogyan javíthatja a késést.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250282"
---
# <a name="co-locate-resources-for-improved-latency"></a>Az erőforrások közös megkeresése a jobb késés érdekében

Amikor az alkalmazást az Azure-ban telepíti, a példányok régiók vagy rendelkezésre állási zónák közötti szétosztása hálózati késést hoz létre, ami hatással lehet az alkalmazás általános teljesítményére. 

## <a name="proximity-placement-groups"></a>Közelségi elhelyezési csoportok

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>További lépések

Üzembe helyezhet egy virtuális gép egy [közelségelhelyezési csoport](proximity-placement-groups.md) az Azure CLI használatával.

További információ a [hálózati késés teszteléséről.](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

További információ a [hálózati átviteli hang optimalizálásáról.](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

Ismerje meg, hogyan használhatja a [közelségelhelyezési csoportokat az SAP-alkalmazásokkal.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
