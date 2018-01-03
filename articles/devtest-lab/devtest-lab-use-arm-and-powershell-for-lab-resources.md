---
title: "Létrehozhat vagy módosíthat labs Azure Resource Manager-sablonok használatával automatikusan a PowerShell használatával |} Microsoft Docs"
description: "Útmutató: Azure Resource Manager-sablonok létrehozására vagy módosítására automatikusan labor DevTest labs PowerShell segítségével"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: v-craic
ms.openlocfilehash: 0aa55d1bc1f459e6757ecb27de043d4742a77c9f
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Létrehozhat vagy módosíthat labs automatikusan az Azure Resource Manager-sablonok és a PowerShell használatával

DevTest Labs biztosít sok Azure Resource Manager-sablonok és a PowerShell-parancsfájlok, amely gyorsan és automatikusan hozzon létre új labs vagy módosítsa a meglévő labs és majd telepítenie kell ezeket az erőforrásokat.

Ez a cikk segít ismerteti a folyamatot, ezeket a sablonokat és parancsfájlok használatával automatizálhatja a létrehozását, módosítását és a tesztkörnyezetek központi telepítését. Ez a cikk azt is bemutatja, hol találhatók a DevTest Labs szolgáltatásban néhány gyakori feladatok elvégzése PowerShell használatával kapcsolatos további információk.

## <a name="step-1-gather-your-templates-and-scripts"></a>1. lépés: A sablonok és a parancsfájlok összegyűjtése
Előre végrehajtott található [Azure Resource Manager-sablonok](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) és [PowerShell-parancsfájlok](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) , a nyilvános [Github-tárházban](https://github.com/Azure/azure-devtestlab). Használja őket-van, vagy testre is szabhatja őket az igényeinek, és tárolja őket a saját [privát Git-tárház](devtest-lab-add-artifact-repo.md). 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>2. lépés: Az Azure Resource Manager-sablon módosítása
A következő lépések követésével [az első Azure Resource Manager-sablon létrehozása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) Ha soha nem hozott létre egy sablont előtt.

Emellett [ajánlott eljárások Azure Resource Manager-sablonok létrehozásához](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) kínál számos irányelvek és hozhat létre Azure Resource Manager-sablonok, amely javaslatokat is megbízható és könnyen használható. Általában fogja használni a megközelítésekre, vagy a megadott példákat egyik változata, a sablon módosításához az igényeinek.

## <a name="step-3-deploy-resources-with-powershell"></a>3. lépés: Erőforrások az PowerShell telepítése
Miután a sablonok és a parancsfájlok testreszabott, szükséges lépéseit követve [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). A cikk az Azure PowerShell használata Azure Resource Manager-sablonok az erőforrások telepítése az Azure-bA általános információkat nyújt.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Gyakori feladatok elvégzése a DevTest Labs szolgáltatásban a PowerShell használatával
Nincsenek sok kapcsolatos további általános feladatok PowerShell segítségével automatizálható. A dokumentáció az alábbi szakaszok felsorolják a feladatok végrehajtásához szükséges lépéseket.

* [Egyéni lemezkép létrehozása a PowerShell használatával VHD-fájl](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Töltse fel a VHD-fájlt a tesztlabor a tárfiók PowerShell használatával](devtest-lab-upload-vhd-using-powershell.md)
* [Külső felhasználó hozzáadása egy laborhoz PowerShell használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [PowerShell-lel labor egyéni szerepkör létrehozása](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan hozzon létre egy [privát Git-tárház](devtest-lab-add-artifact-repo.md) tárolására az egyéni sablonok vagy parancsfájlok.
* Megismerkedhet a [Azure Resource Manager sablonok Azure gyors üzembe helyezési sablon gyűjteményből](https://github.com/Azure/azure-quickstart-templates).
