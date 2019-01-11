---
title: Windows távoli felügyeleti-HTTPS-kapcsolaton keresztül az Azure-hoz |} A Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pbutlerm
ms.openlocfilehash: b2c0dbfbf474cccdf2d1253b77869c59fd449591
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214555"
---
# <a name="windows-remote-management-over-https"></a>Windows Rendszerfelügyeleti webszolgáltatások HTTPS protokollon

Ez a szakasz ismerteti, hogyan konfigurálhatja egy Azure-ban üzemeltetett, a Windows-alapú virtuális gép, úgy, hogy, kezelhető és a PowerShell használatával távolról üzembe helyezve.  PowerShell távoli eljáráshívás engedélyezéséhez a cél virtuális Gépen a Windows Rendszerfelügyeleti (webszolgáltatások WinRM) HTTPS-végpontokat kell közzétennie.  PowerShell-táveléréssel kapcsolatos további információkért lásd: [távoli parancsok futtatása](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6).  Winrm szolgáltatásokkal kapcsolatos további információkért lásd: [Windows távoli felügyeleti](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Ha létrehozott egy virtuális Gépet, a "klasszikus" Azure megközelítések egyikének használatával – az Azure Service Manager portál vagy az elavult [Azure Service Management API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))–, majd automatikusan van konfigurálva a WinRM-végponthoz.  Azonban egy virtuális Gépet hoz létre "modern" Azure megközelíti a következő használatával, akkor a virtuális gép fog *nem* konfigurálni a winrm HTTPS-kapcsolaton keresztül.  

- Használatával a [az Azure portal](https://portal.azure.com/), általában egy jóváhagyott alap, a szakaszban leírtak szerint a [hozzon létre egy Azure-kompatibilis virtuális Merevlemezt.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Az Azure Resource Manager-sablonok használatával](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Az Azure PowerShell vagy az Azure parancssori felület parancs-rendszerhéj használata.  Példák: [a rövid útmutató: Windows virtuális gép létrehozása az Azure-ban a PowerShell-lel](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) és [a rövid útmutató: Linux rendszerű virtuális gép létrehozása az Azure CLI-vel](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

A WinRM-végpont is futtatásához szükséges a hitelesítő eszköz kit bevezetése a virtuális gép leírtak szerint [igazolja, a Virtuálisgép-lemezkép](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Ezzel szemben a általában Linux rendszerű virtuális gépek távolról felügyelt segítségével [Azure CLI-vel](https://docs.microsoft.com/cli/azure) vagy Linux-parancsok egy SSH-konzolról.  Az Azure emellett több alternatív módszert nyújt [szkriptek futtatása a Linux rendszerű virtuális gép](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Az összetettebb esetekhez tartozó számos automatizálási és integrációs megoldásokat Windows - vagy Linux-alapú virtuális gépeken érhető el.


## <a name="configure-and-deploy-with-winrm"></a>Konfigurálhatja és telepítheti a WinRM-mel

A WinRM-végpont egy windows-alapú virtuális gép két különböző szakaszaiban a fejlesztés során konfigurálhatók:

- Létrehozásakor – a meglévő virtuális Merevlemezek, virtuális gépek üzembe helyezése során.  Ez az új ajánlatokról az előnyben részesített módszere.  E módszerhez szükség van egy Azure-tanúsítvánnyal, a megadott Azure Resource Manager-sablonok használatával létrehozását, és futó testre PowerShell-parancsfájlokat. 
- Miután üzembe helyezés – az Azure-ban tárolt meglévő virtuális Géphez.  Ezt a megközelítést akkor használja, ha már rendelkezik egy Azure-ban üzembe helyezett Virtuálisgép-megoldás, és ablak távoli felügyelet engedélyezéséhez hozzá kell.  Ez a megközelítés szükséges az Azure Portalon és a cél virtuális Gépen a parancsfájl végrehajtása manuális módosítások. 


## <a name="next-steps"></a>További lépések
Új virtuális gép létrehozásakor, engedélyezheti a Rendszerfelügyeleti webszolgáltatások során [a VHD-ből a virtuális gép üzembe helyezési](./cpp-deploy-vm-vhd.md).  Ellenkező esetben a Rendszerfelügyeleti webszolgáltatások is engedélyezhető a meglévő virtuális gép  
