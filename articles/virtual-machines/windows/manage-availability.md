---
title: A Windows virtuális gépek azure-beli elérhetőségének kezelése
description: Megtudhatja, hogyan használhat több virtuális gépet a Windows-alkalmazás magas rendelkezésre állásának biztosítására az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02dec02664cc82392ece49d1c5bd08834c3b9af3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451771"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Azure-beli Windows rendszerű virtuális gépek rendelkezésre állásának kezelése 

Ismerje meg, hogyan állíthat be és kezelhet több virtuális gépet, hogy biztosítsa a Windows-alkalmazás magas rendelkezésre állását az Azure-ban. Azt is [kezelheti a rendelkezésre álló Linux virtuális gépek](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>További lépések
A virtuális gépek terheléselosztásáról a [Terheléselosztás virtuális gépek](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ről olvashat bővebben.

Referenciaarchitektúrák megtekintése az N-szintű alkalmazások SQL Server en való futtatásához az IaaS-ben

* [Windows N-tier alkalmazás az Azure-ban az SQL Server rel](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [N szintű alkalmazás futtatása több Azure-régióban a magas rendelkezésre állás érdekében](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
