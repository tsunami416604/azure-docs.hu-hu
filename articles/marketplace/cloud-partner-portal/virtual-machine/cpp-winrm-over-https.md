---
title: Windows távkezelés HTTPS-kapcsolaton keresztül az Azure-hoz | Azure Piactér
description: Bemutatja, hogyan konfigurálhat egy Azure-üzemeltetett, Windows-alapú virtuális gép, hogy távolról kezelhető a PowerShell használatával.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: 7c799c4a56ee5fc2074e4d644bdbcbc6d2b1ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288750"
---
# <a name="windows-remote-management-over-https"></a>Rendszerfelügyeleti webszolgáltatások HTTPS protokollon

Ez a szakasz bemutatja, hogyan konfigurálhat egy Azure-üzemeltetett, Windows-alapú virtuális gép, hogy a PowerShell segítségével távolról kezelhető és telepíthető legyen.  A PowerShell távoli kapcsolatszolgáltatás engedélyezéséhez a cél virtuális gépnek meg kell adnia egy Windows remote Management (WinRM) HTTPS-végpontot.  A PowerShell távoli kapcsolatáttekintésről a [Távoli parancsok futtatása](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands)című témakörben talál.  A WinRM szolgáltatásról további információt a [Windows távkezelés című témakörben talál.](https://docs.microsoft.com/windows/desktop/WinRM/portal)

Ha virtuális gép létrehozása a "klasszikus" Azure-megközelítések egyikével – vagy az Azure Service Manager Portal vagy az elavult [Azure Service Management API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))használatával – létrehozott, akkor az automatikusan winrm-végpontdal lesz konfigurálva.  Azonban ha egy virtuális gép a következő "modern" Azure-megközelítések használatával, majd a virtuális gép *nem* lesz konfigurálva a WinRM HTTPS-en keresztül.

- Az [Azure Portal](https://portal.azure.com/)használata , általában egy jóváhagyott bázisról, az [Azure-kompatibilis virtuális merevlemez létrehozása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd) című szakaszban leírtak szerint
- [Az Azure Resource Manager-sablonok használata](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Az Azure PowerShell vagy az Azure CLI parancshéj használatával.  Példák: [Rövid útmutató: Hozzon létre egy Windows virtuális gépet az Azure-ban a PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) és [a gyorsútmutató: Hozzon létre egy Linux virtuális gépet az Azure CLI használatával.](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)

Ez a WinRM-végpont a virtuális gép bevezetéséhez szükséges minősítési eszközkészlet futtatásához is szükséges, [a virtuális gép lemezképének hitelesítése című részben leírtak szerint.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm)

Ezzel szemben általában a Linux virtuális gépek távolról kezelik az [Azure CLI](https://docs.microsoft.com/cli/azure) vagy Linux-parancsok egy SSH konzolról.  Az Azure számos alternatív módszert is kínál a [linuxos virtuális gép parancsfájljainak futtatásához.](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm)  Összetettebb forgatókönyvek esetén számos automatizálási és integrációs megoldás érhető el windowsos vagy Linux alapú virtuális gépekhez.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurálás és telepítés a WinRM-tel

A Windows-alapú virtuális gép WinRM-végpontja a fejlesztés két különböző szakaszában konfigurálható:

- Létrehozása során - a virtuális gép telepítése során egy meglévő virtuális merevlemez.  Ez az új ajánlatok előnyben részesített megközelítése.  Ehhez a megközelítéshez létre kell adni egy Azure-tanúsítványt a mellékelt Azure Resource Manager-sablonok használatával, és testre szabott PowerShell-parancsfájlokat kell futtatni.
- Üzembe helyezés után – egy azure-ban üzemeltetett meglévő virtuális gépen.  Akkor használja ezt a módszert, ha már rendelkezik az Azure-ban üzembe helyezett virtuálisgép-megoldással, és engedélyeznie kell a Windows remote Management szolgáltatást.  Ez a megközelítés manuális módosításokat igényel az Azure Portalon, és egy parancsfájl végrehajtása a cél virtuális gép.


## <a name="next-steps"></a>További lépések
Ha új virtuális gép létrehozása, engedélyezheti a WinRM a virtuális gép telepítése során [a virtuális gép a virtuális gép.](./cpp-deploy-vm-vhd.md)  Ellenkező esetben a Rendszerfelügyeleti webszolgáltatások engedélyezhetők egy meglévő virtuális gépben
