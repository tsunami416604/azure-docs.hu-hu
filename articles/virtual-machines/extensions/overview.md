---
title: Az Azure virtuális gépi bővítmények és szolgáltatások |} A Microsoft Docs
description: Ismerje meg az Azure Virtuálisgép-bővítmények és azok használatát az Azure-beli virtuális gépekkel
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: 1d092f03013d98106138f35734d960567121a5bf
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692721"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Az Azure virtuális gépi bővítmények és szolgáltatások
Az Azure virtuális gép (VM) a bővítmények olyan kisebb alkalmazásoknál, amelyek az üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosít az Azure virtuális gépeken, majd testre szabhatja őket a központi telepítések részeként lekérdezése a business, az egyéni tartományon kívül és használhatja a meglévő rendszerképek rendszerkép létrehozása.

Az Azure platform számos olyan bővítmény, a Virtuálisgép-konfiguráció, biztonsági és megfigyelési segédprogram alkalmazásokat üzemelteti. A kiadók igénybe vehet egy alkalmazást, majd tegye azokat egy bővítmény be, és egyszerűsítése érdekében a telepítés, így ehhez mindössze meg a kötelező paramétereket. 

 Az első és harmadik féltől származó bővítményeket, nagy választási lehetőség van, ha az alkalmazás a bővítménytárban nem létezik, majd használja az egyéni szkriptek bővítménye, és konfigurálja a virtuális gép saját parancsprogramok és parancsok.

Főbb alkalmazási helyzetek bővítmények használt példák:
* Virtuálisgép-konfiguráció segítségével (a Desired State Configuration) Powershell DSC, Chef, Puppet és egyéni parancsfájl-kiterjesztés Virtuálisgép-konfiguráció ügynökök telepítése és konfigurálása a virtuális gép. 
* AV-termékek, például a Symantec, ESET.
* Virtuális gép biztonsági rés eszközre, például a Qualys, Rapid7, HPE.
* Virtuális gép és alkalmazás figyelési eszközök, például a dynatrace-szel, az Azure Network Watcher, Site24x7 és Stackify.

Egy új virtuális gép üzembe helyezésének bővítményeket is telepíthet. Például ezek lehet része egy nagyobb üzembe helyezés, a virtuális gép kiépítéséhez, alkalmazások konfigurálása, vagy bármely támogatott bővítmény üzemeltetett rendszerek post üzemelő futtassa.

## <a name="how-can-i-find-what-extensions-are-available"></a>Hogyan találhatja meg, hogy mely bővítmények érhetők el?
Extensions alatt a portálon a virtuális gép paneljén megtekintheti az elérhető bővítmények, ez jelenti, hogy csak egy kis összeget, a teljes listát a CLI-eszközök használata, hogy [Virtuálisgép-bővítmények felfedezése Linux](features-linux.md) és [ A Windows Virtuálisgép-bővítmények felderítése](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Hogyan telepíthetem egy bővítmény?
Az Azure Virtuálisgép-bővítmények használatával vagy az Azure CLI, Azure PowerShell-lel, az Azure Resource Manager-sablonokkal és az Azure Portalon kezelhetők. Megismétli a bővítményekhez, is nyissa meg az Azure Portalon, válassza az egyéni szkriptek futtatására szolgáló bővítmény, ezután adja át a parancs / script és futtathat a bővítmények.

Ha szeretné a CLI vagy a Resource Manager-sablon által hozzáadott a portálon kiterjesztése, lásd másik kiterjesztést dokumentációját, például [Windows egyéni szkriptek futtatására szolgáló bővítmény](custom-script-windows.md) és [Linux egyéni szkriptek futtatására szolgáló bővítmény](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Hogyan kezelhetem a bővítmény alkalmazás-életciklus során?
Nem kell közvetlenül telepíteni vagy törölni a bővítményt a virtuális Géphez való csatlakozáshoz. Az Azure-bővítmény alkalmazás-életciklus a VM-en kívül kezeli, és az Azure platform integrált, érhet el a bővítményt integrált állapotát.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Bármi más I kell lennie virtualizációról szeretne tájékozódni-bővítmények
Bővítmények telepíthet alkalmazásokat, például nincsenek alkalmazások néhány követelményt, ott a bővítmények támogatott Windows és Linux rendszerű OSE-kre listáját, és kell rendelkeznie az Azure-beli Virtuálisgép-ügynököt telepíteni. Előfordulhat, hogy az egyes egyes VM-bővítmény alkalmazások saját környezeti Előfeltételek, például az a végpont a hozzáférést.

## <a name="next-steps"></a>További lépések
* Hogyan működik a Linux-ügynök és a bővítmények kapcsolatos további információkért lásd: [Azure-beli Virtuálisgép-bővítmények és szolgáltatások Linux](features-linux.md).
* Hogyan működik a Windows Vendégügynöke és bővítmények kapcsolatos további információkért lásd: [Azure-beli Virtuálisgép-bővítmények és szolgáltatások Windows](features-windows.md).  
* A Windows-Vendégügynök telepítése: [Azure Windows virtuális gép Agent áttekintése ](agent-windows.md).  
* A Linux-ügynök telepítése: [Azure Linux virtuálisgép-ügynök áttekintése ](agent-linux.md).  

