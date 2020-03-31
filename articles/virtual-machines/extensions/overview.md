---
title: Azure-beli virtuálisgép-bővítmények és funkciók
description: Ismerje meg, hogy mik azok az Azure Virtuálisgép-bővítmények, és hogyan használhatók az Azure virtuális gépekkel
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74072981"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure-beli virtuálisgép-bővítmények és funkciók
Az Azure virtuálisgép-bővítmények olyan kis alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure virtuális gépein, használhatja a meglévő lemezképeket, majd testre szabhatja őket az üzembe helyezések részeként, így kiléphet az egyéni képépítés.

Az Azure platform számos bővítményt üzemeltet, amelyek a virtuális gép konfigurációjától, figyelésétől, biztonságától és segédprogram-alkalmazásoktól kezdve számos bővítményt tartalmaznak. A közzétevők vesznek egy alkalmazást, majd csomagolja be egy bővítménybe, és egyszerűsítik a telepítést, így mindössze annyit kell tennie, hogy kötelező paramétereket ad meg. 

 Van egy nagy választéka az első és a harmadik féltől származó bővítmények, ha az alkalmazás a bővítmény tárházban nem létezik, majd használhatja az egyéni parancsfájl-bővítményt, és konfigurálja a virtuális gép a saját parancsfájlok és parancsok.

Példák olyan kulcsfontosságú forgatókönyvekre, amelyekhez a bővítmények et használják:
* Virtuális gép konfiguráció, használhatja a Powershell DSC (kívánt állapotkonfiguráció), Chef, Puppet és egyéni parancsfájl-bővítmények virtuálisgép konfigurációs ügynökök telepítéséhez és konfigurálása a virtuális gép. 
* AV termékek, mint például a Symantec, ESET.
* Virtuális gép biztonsági rés eszköze, például Qualys, Rapid7, HPE.
* Virtuális gép figyelése eszközök, például a DynaTrace, az Azure Network Watcher, Site24x7 és Stackify.

A bővítmények egy új virtuális gép üzembe helyezésével együtt is kötegelhetők. Például lehetnek egy nagyobb központi telepítés, alkalmazások konfigurálása a virtuális gép kiépítése, vagy bármely támogatott bővítmény által működtetett rendszerek üzembe helyezés után futnak.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hogyan találhatom meg a Rendelkezésre álló bővítményeket?
Megtekintheti a rendelkezésre álló bővítmények a virtuális gép panel a portálon, a bővítmények alatt, ez csak egy kis összeget jelent, a teljes listát, használhatja a CLI eszközöket, [lásd: Virtuálisgép-bővítmények felfedezése Linux és](features-linux.md) [felfedezése VM extensions for Windows.](features-windows.md)

## <a name="how-can-i-install-an-extension"></a>Hogyan telepíthetek egy bővítményt?
Az Azure VM-bővítmények az Azure CLI, az Azure PowerShell, az Azure Resource Manager-sablonok és az Azure Portal használatával kezelhetők. Egy bővítmény kipróbálásához nyissa meg az Azure Portalon, válassza ki az egyéni parancsfájl-bővítményt, majd adja át a parancsot / parancsfájlt, és futtassa a bővítményeket.

Ha ugyanazt a bővítményt szeretné hozzáadni a portálon a CLI vagy a Resource Manager sablon, lásd a különböző kiterjesztés dokumentáció, mint például a [Windows Egyéni Script Extension](custom-script-windows.md) és a Linux Custom Script [Extension](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hogyan kezelhetem a bővítményalkalmazás életciklusát?
Nem kell csatlakoznia a virtuális géphez közvetlenül a bővítmény telepítéséhez vagy törléséhez. Mivel az Azure-bővítmény alkalmazás életciklusa a virtuális gépen kívül van kezelve, és integrálva van az Azure platformba, a bővítmény integrált állapotát is megkapja.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Van még valami, amire gondolnom kellene a hosszabbítások miatt?
A bővítmények alkalmazásokat telepítenek, mint minden alkalmazás, vannak bizonyos követelmények, a bővítmények hez van egy lista a támogatott Windows és Linux operációs rendszerek, és telepíteni kell az Azure vm ügynökök telepítve. Egyes egyes virtuálisgép-bővítmény alkalmazások saját környezeti előfeltételek, például egy végponthoz való hozzáférés.

## <a name="troubleshoot-extensions"></a>Bővítmények hibaelhárítása

Az egyes bővítmények hibaelhárítási információi a bővítmény áttekintésének **Hibaelhárítás és támogatás** szakaszában találhatók. Az alábbiakban a rendelkezésre álló hibaelhárítási információkat találja meg:

| Névtér | Hibaelhárítás |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Azure Monitor-függőség Linuxhoz](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Azure Monitor-függőség windowshoz](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Azure lemeztitkosítás Linuxhoz](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Azure lemeztitkosítás Windows hoz](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Egyéni Parancsfájl a Windows rendszerhez](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Kívánt állapotkonfiguráció Linuxhoz](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [A Windows kívánt állapotkonfigurációja](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [NVIDIA GPU illesztőprogram-bővítmény Linuxra](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [NVIDIA GPU illesztőprogram-bővítmény Windowshoz](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Kártevőirtó bővítmény Windowshoz](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Linuxalapú Azure Monitor](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Azure Monitor for Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace Linuxra](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Jelszó alaphelyzetbe állítása (VMAccess) Linuxhoz](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Pillanatkép Linuxhoz](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Pillanatkép a Windows hoz](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>További lépések
* A Linux-ügynök és -bővítmények működéséről további információt az [Azure VM-bővítmények és a Linux-szolgáltatások című témakörben talál.](features-linux.md)
* A Windows vendégügynök és -bővítmények működéséről az [Azure vm-bővítmények és a Windows szolgáltatásai](features-windows.md)című témakörben talál további információt.  
* A Windows vendégügynök telepítéséhez olvassa el az [Azure Windows virtuálisgép-ügynök – áttekintés című témakört.](agent-windows.md)  
* A Linux-ügynök telepítéséhez tekintse meg az [Azure Linux virtuálisgép-ügynök áttekintése című témakört.](agent-linux.md)  

