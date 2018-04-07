---
title: Közösségi eszközei - hagyományos erőforrás áthelyezése az Azure Resource Manager |} Microsoft Docs
description: Ez a cikk katalogizálása adtak át IaaS-erőforrásokra klasszikus az Azure Resource Manager telepítési modell segítségével a Közösség által-eszközöket.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: cce1906e75646f2fb9ea30842e968d14830f3497
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Közösségi eszközök az IaaS-erőforrások migrálásához a klasszikusból az Azure Resource Manager-alapú üzemi modellbe
Ez a cikk katalogizálása a IaaS-erőforrások az Azure Resource Manager telepítési modell a klasszikus áttelepítéssel segít a Közösség által biztosított eszközöket.

> [!NOTE]
> Ezek az eszközök hivatalosan nem támogatottak az Microsoft Support. Ezért nyissa meg a Githubon forrása, és örömmel fogadja el a PRs javítások és további forgatókönyvekhez. Jelentheti a problémát, a GitHub-problémák szolgáltatást használja.
> 
> Ezekkel az eszközökkel áttelepítése, akkor a klasszikus virtuális gép állásidő. Ha az áttelepítéshez támogatott platform van szüksége, látogasson el 
> 
>   * [IaaS-erőforrásokra a klasszikus Azure Resource Manager-készletben való áttelepítésének támogatott platform](migration-classic-resource-manager-overview.md)
>   * [Műszaki részletes bemutatója a Platform támogatja az áttelepítést a klasszikus Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [IaaS-erőforrások áttelepítése a klasszikus Azure Resource Managerbe Azure PowerShell használatával](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Ez a vállalati rendszerre végzett áttelepítésekre Azure szolgáltatásfelügyelet az Azure Resource Manager részeként létrehozott segítő eszközök gyűjteménye. Ez az eszköz lehetővé teszi az infrastruktúra replikálásához áttelepítés és a problémák megoldása során vas tesztelése előtt az áttelepítés futó éles előfizetése használható egy másik előfizetéssé.

[Az eszköz dokumentációja csatolása](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz további lehetősége arra a teljes adatkészletet klasszikus IaaS-erőforrásokra át Azure Resource Manager IaaS-erőforrásokra. Az áttelepítés akkor fordulhat elő, egyazon előfizetésen belül vagy között különböző előfizetésekhez és előfizetéstípusok (pl.: CSP előfizetések).

[Az eszköz dokumentációja csatolása](https://github.com/Azure/migAz)

## <a name="next-steps"></a>További lépések

* [IaaS-erőforrásokra a klasszikus Azure Resource Manager platform által támogatott áttelepítésének áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Műszaki részletes bemutatója a platform által támogatott áttelepítési a klasszikus Azure Resource Managerbe](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése a klasszikus Azure Resource Manager PowerShell használatával](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése a klasszikus Azure Resource Manager parancssori felület használatával](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakrabban feltett kérdésekre áttelepítése IaaS-erőforrásokra a klasszikus Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

