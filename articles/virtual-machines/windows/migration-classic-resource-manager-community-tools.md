---
title: Közösségi eszközök – klasszikus erőforrások áthelyezése a Azure Resource Managerba | Microsoft Docs
description: Ez a cikk a IaaS-erőforrások klasszikusról a Azure Resource Manager üzemi modellbe való áttelepítését segítő eszközöket katalogizálja a Közösség által biztosított eszközökkel.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 2b5d218812878db6fb110c043afffd2f9a2d344d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102677"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Közösségi eszközök az IaaS-erőforrások migrálásához a klasszikusból az Azure Resource Manager-alapú üzemi modellbe
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
a migAz egy további lehetőség a klasszikus IaaS erőforrások teljes készletének áttelepítésére Azure Resource Manager IaaS-erőforrásokra. Az áttelepítés ugyanazon az előfizetésen belül vagy a különböző előfizetések és előfizetési típusok között történhet (pl.: CSP-előfizetések).

[Az eszköz dokumentációjának hivatkozása](https://github.com/Azure/migAz)

## <a name="next-steps"></a>További lépések

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

