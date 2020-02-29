---
title: Közösségi eszközök – klasszikus erőforrások áthelyezése a Azure Resource Managerba
description: Ez a cikk a IaaS-erőforrások klasszikusról a Azure Resource Manager üzemi modellbe való áttelepítését segítő eszközöket katalogizálja a Közösség által biztosított eszközökkel.
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915401"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Közösségi eszközök IaaS-erőforrások áttelepítéséhez klasszikusról Azure Resource Manager

> [!IMPORTANT]
> Napjainkban a IaaS virtuális gépek 90%-a [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)használ. 2020. február 28-án a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen megszűnnek. [További]( https://aka.ms/classicvmretirement) információ erről az elavult szolgáltatásról, valamint arról, [hogy Ön hogyan befolyásolja Önt](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Ez a cikk a IaaS-erőforrások klasszikusról a Azure Resource Manager üzemi modellbe való áttelepítését segítő, a Közösség által biztosított eszközöket katalogizálja.

> [!NOTE]
> Ezeket az eszközöket Microsoft ügyfélszolgálata nem támogatja hivatalosan. Ezért nyílt forráskódú a GitHubon, és örömmel fogadjuk el a hibákat és a további forgatókönyveket. A probléma jelentéséhez használja a GitHub-problémák funkciót.
> 
> Ha ezekkel az eszközökkel végez áttelepítést, a klasszikus virtuális gép leállását okozhatja. Ha a platform támogatott áttelepítését keresi, látogasson el ide: 
> 
>   * [A platform támogatja a IaaS-erőforrások Klasszikusból Azure Resource Manager verembe való áttelepítését](migration-classic-resource-manager-overview.md)
>   * [A platform által támogatott, Klasszikusról Azure Resource Managerre való áttérés technikai részletes bemutatása](migration-classic-resource-manager-deep-dive.md)
>   * [IaaS-erőforrások migrálása Klasszikusról Azure Resource Managerra Azure PowerShell használatával](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Ez az Azure Service Management szolgáltatásból a Azure Resource Managerba való nagyvállalati Migrálás részeként létrehozott Segéd-eszközök gyűjteménye. Ez az eszköz lehetővé teszi, hogy az infrastruktúrát egy másik előfizetésbe replikálja, amely az áttelepítés teszteléséhez és az esetleges problémák kiszámításához használatos az éles előfizetése előtt.

[Az eszköz dokumentációjának hivatkozása](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
a migAz egy további lehetőség a klasszikus IaaS erőforrások teljes készletének áttelepítésére Azure Resource Manager IaaS-erőforrásokra. Az áttelepítés ugyanazon az előfizetésen belül vagy a különböző előfizetések és előfizetési típusok (pl.: CSP-előfizetések) között történhet.

[Az eszköz dokumentációjának hivatkozása](https://github.com/Azure/migAz)

## <a name="next-steps"></a>További lépések

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

