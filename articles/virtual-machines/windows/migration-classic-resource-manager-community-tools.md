---
title: Közösségi eszközök – Klasszikus erőforrások áthelyezése az Azure Resource Managerbe
description: Ez a cikk a közösség által biztosított eszközöket katalogizálja az IaaS-erőforrások klasszikusról az Azure Resource Manager telepítési modellre való áttelepítéséhez.
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f4298af05f021f0a9579a9837308be5d15a3c14f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915401"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Közösségi eszközök az IaaS-erőforrások klasszikusról Azure Resource Managerbe való áttelepítéséhez

> [!IMPORTANT]
> Ma az IaaS virtuális gépek mintegy 90%-a használja az [Azure Resource Managert.](https://azure.microsoft.com/features/resource-manager/) 2020. február 28-tól a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen nyugdíjba kerülnek. [Tudjon meg többet]( https://aka.ms/classicvmretirement) erről az eprecációról [és arról, hogy ez milyen hatással van Önre.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Ez a cikk a közösség által az IaaS-erőforrások klasszikusról az Azure Resource Manager üzembe helyezési modellre való áttelepítéséhez biztosított eszközöket katalogizálja.

> [!NOTE]
> Ezeket az eszközöket a Microsoft támogatási szolgálata hivatalosan nem támogatja. Ezért nyílt forráskódúak a GitHubon, és örömmel fogadunk el prs javításokat vagy további forgatókönyveket. Probléma jelentéséhez használja a GitHub-problémák funkciót.
> 
> Az ezekkel az eszközökkel való áttelepítés a klasszikus virtuális gép állásidejét okozza. Ha platformáltal támogatott áttelepítést keres, látogasson el a 
> 
>   * [A platform támogatja az IaaS-erőforrások klasszikusról Azure Resource Manager-veremre való áttelepítését](migration-classic-resource-manager-overview.md)
>   * [Technikai Deep Dive on Platform támogatott áttelepítés klasszikus Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [IaaS-erőforrások áttelepítése klasszikusról az Azure Resource Managerbe az Azure PowerShell használatával](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Ez az Azure Service Management szolgáltatáskezelésből az Azure Resource Managerbe történő vállalati áttelepítések részeként létrehozott segítő eszközök gyűjteménye. Ez az eszköz lehetővé teszi, hogy replikálja az infrastruktúrát egy másik előfizetésbe, amely az áttelepítés tesztelésére használható, és kivasalhatja a problémákat, mielőtt futtatna az áttelepítést az éles előfizetésen.

[Hivatkozás az eszköz dokumentációjára](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
A migAz egy további lehetőség a klasszikus IaaS-erőforrások teljes készletének az Azure Resource Manager IaaS-erőforrásokba való áttelepítéséhez. Az áttelepítés történhet ugyanazon az előfizetésen belül, vagy különböző előfizetések és előfizetéstípusok (pl. CSP-előfizetések) között.

[Hivatkozás az eszköz dokumentációjára](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Következő lépések

* [Az IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Managerre](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusról az Azure Resource Managerbe való áttelepítése a PowerShell használatával](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A CLI használatával áttelepítheti az IaaS-erőforrásokat klasszikusról Azure Resource Managerre](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tekintse át az IaaS-erőforrások klasszikusról Azure Resource Managerbe való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

