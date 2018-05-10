---
title: Az Azure virtuálisgép-bővítmények és a szolgáltatások |} Microsoft Docs
description: Milyen Azure Virtuálisgép-bővítmények anre megtudhatja, hogyan használhatja ezeket az Azure virtuális gépekkel
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: 01178995dbf9203082a6250ef256522bc1101e57
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Az Azure virtuálisgép-bővítmények és szolgáltatások
Az Azure virtuális gép (VM) a program kis alkalmazásokat, amelyek telepítés utáni konfigurációs és automatizálási feladatok Azure virtuális gépeken, majd testre is szabhatja őket a központi telepítések részeként kívül az egyéni üzleti első meg és használhatja a meglévő lemezképek lemezkép létrehozása.

Az Azure platformon, amely a Virtuálisgép-konfigurációhoz, figyelés, biztonsági és segédprogram alkalmazások között számos bővítmények üzemelteti. Közzétevők igénybe vehet egy alkalmazást, majd tegye azokat a bővítmény és egyszerűsítse a telepítés, így kell tennie, csak a kötelező paraméterek megadásához. 

 Az első és harmadik fél bővítményei nagy választási lehetőség van, ha az alkalmazás a bővítménytárban nem létezik, majd használja az egyéni parancsprogramok futtatására szolgáló bővítmény, és konfigurálja a virtuális gép saját parancsprogramok és parancsok.

A használt kiterjesztések főbb forgatókönyvek példái:
* Virtuálisgép-konfiguráció segítségével (célállapot-konfiguráció) a Powershell DSC, Chef, Puppet és egyéni parancsfájl-kiterjesztés virtuális gép konfigurációs ügynökök telepítése és konfigurálása a virtuális Gépet. 
* Az AV-termékek, például Symantec, alaphelyzetbe állítása.
* Virtuális gép biztonsági rés eszköz, például Qualys, Rapid7, HPE.
* Virtuális gép és Alkalmazásfigyelési tooling, például DynaTrace Azure hálózati figyelőt, Site24x7 vagy Stackify.

Bővítmények is telepíthet egy új Virtuálisgép-telepítéshez. Például azok kell egy nagyobb üzembe helyezés, az alkalmazások konfigurálása a virtuális gép kiépítését, vagy üzemeltetett támogatott bővítmény rendszerek feladás egy vagy több központi telepítési futtatni.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hogyan találhatók meg, hogy mely bővítmények érhetők el?
A portál kiterjesztések, a virtuális gép paneljén megtekintheti a rendelkezésre álló bővítmények, csak a kis méretű jelképez teljes listáját, a parancssori eszközök, lásd: [Virtuálisgép-bővítmények felderítéséhez Linux](features-linux.md) és [ Virtuálisgép-bővítmények felderítéséhez Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hogyan telepíthető egy bővítmény?
Az Azure Virtuálisgép-bővítmények az Azure CLI 2.0, az Azure PowerShell, Azure Resource Manager-sablonok, és az Azure-portál használatával kezelhetők. Kipróbálásához bővítménye, megnyithatja az Azure portálon, válassza ki az egyéni parancsprogramok futtatására szolgáló bővítmény, majd adjon át egy parancs / script, és futtassa a bővítmények.

Ha szeretné ugyanazokat bővítmény meg hozzáadni a parancssori felületen vagy a Resource Manager-sablon, a portál dokumentációjában másik kiterjesztést, például a [Windows egyéni parancsprogramok futtatására szolgáló bővítmény](custom-script-windows.md) és [Linux egyéni parancsprogramok futtatására szolgáló bővítmény](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hogyan kezelhetők a bővítmény alkalmazás életciklusa?
Nem kell csatlakozni a virtuális gépek közvetlenül telepíteni vagy törölni a bővítményt. Az Azure-bővítményt alkalmazás-életciklus a VM-on kívül felügyelt és az Azure platformon integrálva, a bővítmény állapotának integrált is kap.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Bármi más I kell lennie a gondolat Extensions?
Bővítmények alkalmazásokat telepíteni, például olyan alkalmazást, nincs néhány követelményhez, bővítmények nem támogatott Windows és Linux operációs rendszer, és telepíteni kell az Azure virtuális gép ügynököt telepíteni. Előfordulhat, hogy az egyes különálló virtuális gép bővítmény alkalmazások saját környezeti Előfeltételek, például egy olyan végpont elérésére.

## <a name="next-steps"></a>További lépések
* A Linux-ügynök és a kiterjesztések működése kapcsolatos további információkért lásd: [Azure Virtuálisgép-bővítmények és a Linux funkcióit](features-linux.md).
* A Windows Vendégügynök és bővítmények működése kapcsolatos további információkért lásd: [Azure Virtuálisgép-bővítmények és a Linux funkcióit](features-windows.md).  
* A Windows-Vendégügynök telepítéséhez tekintse át [Azure Windows virtuális gépek ügynök áttekintése ](agent-windows.md).  
* A Linux-ügynök telepítéséhez tekintse át [Azure Linux virtuális gép ügynök – áttekintés ](agent-linux.md).  

