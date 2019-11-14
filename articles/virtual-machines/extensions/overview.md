---
title: Azure-beli virtuálisgép-bővítmények és-funkciók
description: Ismerje meg, hogy milyen Azure-beli virtuálisgép-bővítmények és hogyan használhatók az Azure Virtual Machines szolgáltatással
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072981"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure-beli virtuálisgép-bővítmények és-funkciók
Az Azure virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációt és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken, meglévő lemezképeket használhat, majd az üzembe helyezés részeként testreszabhatja azokat, így az egyéni tevékenységek rendszerkép kiépítése.

Az Azure platform számos olyan bővítményt üzemeltet, amely a virtuálisgép-konfigurációtól, a figyelési, a biztonsági és a segédprogram-alkalmazásokból származik. A kiadók egy alkalmazást futtatnak, majd becsomagolják egy bővítménybe, és leegyszerűsítik a telepítést, ezért mindössze annyit kell tennie, hogy megadja a kötelező paramétereket. 

 Az első és a harmadik féltől származó bővítmények széles választéka van, ha a bővítmény-tárházban lévő alkalmazás nem létezik, akkor használhatja az egyéni szkriptek bővítményét, és konfigurálhatja a virtuális gépet a saját parancsfájljaival és parancsaival.

Példák a bővítmények által használt főbb forgatókönyvekre:
* Virtuálisgép-konfiguráció, a PowerShell DSC (a kívánt állapot konfigurációja), a Chef, a Puppet és az egyéni parancsfájlok bővítmények segítségével telepítheti a virtuálisgép-konfigurációs ügynököket, és konfigurálhatja a virtuális gépet. 
* AV-termékek, például Symantec, ESET.
* VM sebezhetőségi eszköz, például Qualys, Rapid7, HPE.
* VIRTUÁLIS gépek és alkalmazások figyelésére szolgáló eszközök, például DynaTrace, Azure Network Watcher, Site24x7 és Stackify.

A bővítmények a virtuális gépek új üzembe helyezésével is elhelyezhetők. Lehetnek például egy nagyobb üzembe helyezések, a virtuális gépek üzembe helyezése, illetve a támogatott bővítmények által a telepítés után futtatott alkalmazások.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hogyan találhatom meg a rendelkezésre álló bővítményeket?
Az elérhető bővítmények a portál virtuálisgép-paneljén tekinthetők meg, a bővítmények alatt ez csupán kis mennyiségű, a teljes listához használhatja a CLI-eszközöket, lásd: a virtuálisgép- [bővítmények felfedése Linuxra](features-linux.md) és a [Windows rendszerhez készült virtuálisgép-bővítmények felfedése](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hogyan telepíthetek bővítményt?
Az Azure virtuálisgép-bővítmények az Azure CLI, Azure PowerShell, Azure Resource Manager sablonok és a Azure Portal használatával kezelhetők. A bővítmény kipróbálásához lépjen a Azure Portalra, válassza ki az egyéni parancsfájl-bővítményt, majd adjon át egy parancsot/parancsfájlt, és futtassa a bővítményeket.

Ha azt szeretné, hogy a CLI-vagy Resource Manager-sablon a portálon is felvette a bővítményt, tekintse meg a különböző bővítmények dokumentációját, például a [Windows egyéni szkriptek bővítményét](custom-script-windows.md) és a [Linux Custom script bővítményt](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hogyan a bővítmény alkalmazási életciklusának kezelése?
A bővítmény telepítéséhez és törléséhez nem kell közvetlenül csatlakoznia a virtuális géphez. Mivel az Azure-bővítmény alkalmazás-életciklusát felügyeli a virtuális gépen kívül, és integrálva van az Azure-platformba, a bővítmény integrált állapotát is megkapja.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Bármi mást érdemes meggondolni a bővítményekhez?
A bővítmények telepítik az alkalmazásokat, például az alkalmazások bizonyos követelményeit, a bővítmények esetében a támogatott Windows-és Linux-alapú operációs rendszerek listája szerepel, és telepítenie kell az Azure virtuálisgép-ügynököket. A virtuálisgép-bővítmények egyes alkalmazásai rendelkezhetnek saját környezeti előfeltételekkel, például egy végponthoz való hozzáféréssel.

## <a name="troubleshoot-extensions"></a>Bővítmények hibaelhárítása

Az egyes bővítményekkel kapcsolatos hibaelhárítási információk a bővítmény áttekintésében, a **Hibaelhárítás és támogatás** szakaszban találhatók. Itt látható az elérhető hibaelhárítási információk listája:

| Névtér | Hibaelhárítás |
|-----------|-----------------|
| Microsoft. Azure. monitoring. dependencyagent. dependencyagentlinux | [Linux-függőség Azure Monitor](agent-dependency-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. monitoring. dependencyagent. dependencyagentwindows | [Windows-függőség Azure Monitor](agent-dependency-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryptionforlinux | [Linux-Azure Disk Encryption](azure-disk-enc-linux.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. azurediskencryption | [Windows Azure Disk Encryption](azure-disk-enc-windows.md#troubleshoot-and-support) |
| Microsoft. számítás. customscriptextension | [Egyéni parancsfájl a Windowshoz](custom-script-windows.md#troubleshoot-and-support) |
| Microsoft. ostcextensions. customscriptforlinux | [A Linux kívánt állapotának konfigurálása](dsc-linux.md#troubleshoot-and-support) |
| Microsoft. PowerShell. DSC | [A Windows kívánt állapotának konfigurálása](dsc-windows.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverlinux | [NVIDIA GPU illesztőprogram-bővítmény Linux rendszerhez](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| Microsoft. hpccompute. nvidiagpudriverwindows | [NVIDIA GPU illesztőprogram-bővítmény a Windowshoz](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| Microsoft. Azure. Security. iaasantimalware | [Antimalware-bővítmény a Windows rendszerhez](iaas-antimalware-windows.md#troubleshoot-and-support) |
| Microsoft. enterprisecloud. monitoring. omsagentforlinux | [Linux-Azure Monitor](oms-linux.md#troubleshoot-and-support)
| Microsoft. enterprisecloud. monitoring. microsoftmonitoringagent | [Windows Azure Monitor](oms-windows.md#troubleshoot-and-support) |
| stackify. linuxagent. Extension. stackifylinuxagentextension | [Stackify újrarajzolása Linux rendszeren](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux. microsoft. ostcextensions | [Jelszó alaphelyzetbe állítása (VMAccess) Linux rendszeren](vmaccess.md#troubleshoot-and-support) |
| Microsoft. recoveryservices szolgáltatónál. vmsnapshot | [Linux-pillanatkép](vmsnapshot-linux.md#troubleshoot-and-support) |
| Microsoft. recoveryservices szolgáltatónál. vmsnapshot | [Windows rendszerhez készült pillanatkép](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>További lépések
* A Linux-ügynök és-bővítmények működésével kapcsolatos további információkért lásd: Azure virtuálisgép- [bővítmények és-szolgáltatások Linux rendszerhez](features-linux.md).
* További információ a Windows Guest Agent és a bővítmények működéséről: Azure-beli virtuálisgép- [bővítmények és-szolgáltatások a Windows rendszerhez](features-windows.md).  
* A Windows Guest Agent telepítéséhez tekintse meg az [Azure Windows rendszerű virtuális gépek ügynökének áttekintése](agent-windows.md)című témakört.  
* A Linux-ügynök telepítéséhez tekintse meg az Azure-beli [linuxos virtuális gépek ügynökének áttekintése](agent-linux.md)című témakört.  

