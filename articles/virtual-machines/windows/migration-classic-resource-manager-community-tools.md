---
title: Közösségi eszközök – klasszikus erőforrások áthelyezése az Azure Resource Manager |} A Microsoft Docs
description: Ez a cikk összegyűjti az IaaS-erőforrások migrálásához a klasszikusból az Azure Resource Manager-alapú üzemi modellbe segítségével a Közösség által biztosított eszközöket.
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
ms.openlocfilehash: 085a0ff0eee260069d693b339521a9489df78e40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848231"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Közösségi eszközök az IaaS-erőforrások migrálásához a klasszikusból az Azure Resource Manager-alapú üzemi modellbe
Ez a cikk összegyűjti az IaaS-erőforrások klasszikusból az Azure Resource Manager-alapú üzemi modellbe való migrálásának, amelyek segítik a Közösség által biztosított eszközöket.

> [!NOTE]
> Ezek az eszközök hivatalosan nem támogatottak az Support. Ezért ezek-, nyílt forráskódú a Githubon, és forduljon hozzánk, és fogadja el a lekéréses kérelmek javítások és további forgatókönyvek esetében. Jelentéséhez, használja a GitHub-problémák szolgáltatást.
> 
> Ezekkel az eszközökkel való migrálás a klasszikus virtuális gép miatt állásidőt. Ha az áttelepítéshez támogatott platform keres, keresse fel 
> 
>   * [IaaS-erőforrások klasszikusból Azure Resource Manager verembe történő migrálása támogatott platform](migration-classic-resource-manager-overview.md)
>   * [Technikai bemutatásán túl a platformon támogatott áttelepítési klasszikusból Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [IaaS-erőforrások migrálásához klasszikusból Azure Resource Manager Azure PowerShell-lel](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Ez a vállalati futtató fürtökre történő áttelepítést az Azure Service Management az Azure Resource Manager részeként létrehozott segítő eszközök gyűjteményét. Ez az eszköz lehetővé teszi, hogy az infrastruktúra replikálása egy másik előfizetést, amely használható az áttelepítés és a problémák megoldása során vas teszteléséhez az áttelepítés futtatása termelési előfizetésében előtt be.

[Az eszköz dokumentációja mutató hivatkozás](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz egy további lehetőség áttelepítése az Azure Resource Manager IaaS-erőforrások klasszikus IaaS-erőforrások teljes körét. Az áttelepítés akkor fordulhat elő, ugyanazon az előfizetésen belül vagy eltérő előfizetésekben és előfizetés-típusok között (például: CSP-előfizetések).

[Az eszköz dokumentációja mutató hivatkozás](https://github.com/Azure/migAz)

## <a name="next-steps"></a>További lépések

* [A platform által támogatott áttelepítés IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése klasszikusból Azure Resource Manager PowerShell-lel](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [IaaS-erőforrások áttelepítése klasszikusból Azure Resource Manager parancssori felület használatával](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [A legtöbb – gyakori kérdések áttelepítése IaaS-erőforrások klasszikusból Azure Resource Manager áttekintése](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

