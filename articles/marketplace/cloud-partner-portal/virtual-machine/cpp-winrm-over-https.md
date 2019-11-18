---
title: Rendszerfelügyeleti webszolgáltatások HTTPS protokollon keresztül az Azure-hoz | Azure piactér
description: Ez a cikk azt ismerteti, hogyan konfigurálható egy Azure-ban üzemeltetett, Windows-alapú virtuális gép, hogy távolról felügyelhető legyen a PowerShell használatával.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pabutler
ms.openlocfilehash: e39f83b2ed715afbfff69770c151cfc4d527105d
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132256"
---
# <a name="windows-remote-management-over-https"></a>Rendszerfelügyeleti webszolgáltatások HTTPS protokollon keresztül

Ez a szakasz azt ismerteti, hogyan konfigurálható egy Azure-ban üzemeltetett, Windows-alapú virtuális gép, hogy a PowerShell használatával távolról felügyelhető és telepíthető legyen.  A PowerShell távelérésének engedélyezéséhez a célként megadott virtuális gépnek elérhetőnek kell lennie a Rendszerfelügyeleti webszolgáltatások (WinRM) HTTPS-végpontjának.  További információ a PowerShell táveléréséről: [távoli parancsok futtatása](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  A WinRM szolgáltatással kapcsolatos további információkért [lásd: Rendszerfelügyeleti](https://docs.microsoft.com/windows/desktop/WinRM/portal)webszolgáltatások.

Ha létrehozott egy virtuális gépet a "klasszikus" Azure-megközelítések egyikével – vagy az Azure Service Manager-portálon vagy az elavult [Azure-Service Management API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)), akkor automatikusan egy WinRM-végponttal lesz konfigurálva.  Ha azonban a következő "modern" Azure-megközelítések bármelyikével létrehoz egy virtuális gépet, akkor a virtuális gép *nem* lesz konfigurálva a WinRM HTTPS protokollon keresztül.

- Az [Azure-kompatibilis virtuális merevlemez létrehozása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd) című szakaszban leírtaknak megfelelően a [Azure Portal](https://portal.azure.com/)használata, jellemzően egy jóváhagyott alapból
- [A Azure Resource Manager-sablonok használata](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- A Azure PowerShell vagy az Azure CLI parancs-rendszerhéj használatával.  Példa: gyors útmutató [: Windows rendszerű virtuális gép létrehozása az Azure-ban a PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) -lel és a gyors üzembe helyezéssel [: linuxos virtuális gép létrehozása az Azure CLI-vel](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Ezt a WinRM-végpontot a virtuálisgép- [rendszerkép](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm)tanúsítása című témakörben leírtak szerint is futtatni kell a Certificate Tool Kit a virtuális gép előkészítéséhez.

Ezzel szemben általában a Linux rendszerű virtuális gépek távolról felügyelhetők az SSH-konzolról származó [Azure CLI](https://docs.microsoft.com/cli/azure) -vagy Linux-parancsok használatával.  Az Azure számos alternatív módszert is biztosít a [parancsfájlok Linux rendszerű virtuális gépen való futtatásához](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Összetettebb forgatókönyvek esetén számos automatizálási és integrációs megoldás érhető el Windows-vagy Linux-alapú virtuális gépekhez.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurálás és üzembe helyezés a WinRM szolgáltatással

A Windows-alapú virtuális gépek WinRM-végpontja a fejlesztés két különböző fázisában konfigurálható:

- A létrehozás során – egy virtuális gép egy meglévő virtuális merevlemezre történő telepítése során.  Ez az új ajánlatok előnyben részesített megközelítése.  Ennek a megközelítésnek szüksége van egy Azure-tanúsítvány létrehozására, amelyet a megadott Azure Resource Manager sablonok használatával és a testreszabott PowerShell-parancsfájlok futtatásával kell létrehozni.
- Üzembe helyezés után – az Azure-ban üzemeltetett meglévő virtuális gépen.  Akkor használja ezt a módszert, ha már van üzembe helyezett virtuálisgép-megoldás az Azure-ban, és engedélyeznie kell az ablak távoli felügyeletét.  Ehhez a megközelítéshez manuális módosításokat kell végrehajtani a Azure Portalban, valamint egy parancsfájl végrehajtását a célként megadott virtuális gépen.


## <a name="next-steps"></a>További lépések
Ha új virtuális gépet hoz létre, akkor [a virtuális gép virtuális merevlemezről történő üzembe helyezése](./cpp-deploy-vm-vhd.md)során engedélyezheti a WinRM szolgáltatást.  Ellenkező esetben a WinRM egy meglévő virtuális gépen is engedélyezhető
