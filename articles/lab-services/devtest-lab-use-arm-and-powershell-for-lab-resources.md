---
title: Laborok létrehozása vagy módosítása az Azure Resource Manager-sablonokkal
description: Megtudhatja, hogy miként hozhat létre vagy módosíthat automatikusan tesztteszteket a PowerShell használatával az Azure Resource Manager-sablonokkal a DevTest-környezetben
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 180081829387afeb7e63defe7a22378aa8d417d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170312"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Laborok automatikus létrehozása vagy módosítása az Azure Resource Manager-sablonok és a PowerShell használatával

A DevTest Labs számos Azure Resource Manager-sablont és PowerShell-parancsfájlt biztosít, amelyek segítségével gyorsan és automatikusan létrehozhat új laborokat, vagy módosíthatja a meglévő laborokat, majd üzembe helyezheti ezeket az erőforrásokat.

Ez a cikk segítséget nyújt a sablonok és parancsfájlok használatának folyamatán a laborok létrehozásának, módosításának és üzembe helyezésének automatizálásához. Ez a cikk azt is bemutatja, hogy hol talál további információt arról, hogyan használhatja a PowerShellt néhány gyakori feladat végrehajtására a DevTest Labs-ben.

## <a name="step-1-gather-your-templates-and-scripts"></a>1. lépés: Gyűjtse össze a sablonokat és szkripteket
Az előre elkészített [Azure Resource Manager-sablonokat](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) és [PowerShell-parancsfájlokat](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) a nyilvános [GitHub-tárházban](https://github.com/Azure/azure-devtestlab)találhatja meg. Használja őket, ahogy van, vagy testre szabhatja őket az Ön igényeinek, és tárolja őket a saját [privát Git repo](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>2. lépés: Az Azure Resource Manager-sablon módosítása
Kövesse az első [Azure Resource Manager-sablon létrehozása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) című lépéseit, ha még soha nem hozott létre sablont.

Emellett [az Azure Resource Manager-sablonok létrehozásához ajánlott eljárások](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) számos irányelvet és javaslatot kínál, amelyek segítségével megbízható és könnyen használható Azure Resource Manager-sablonokat hozhat létre. Általában a megadott megközelítések vagy példák egyikének egy változatát fogja használni, és az igényeinek megfelelően módosítani fogja a sablont.

## <a name="step-3-deploy-resources-with-powershell"></a>3. lépés: Erőforrások üzembe helyezése a PowerShell segítségével
Miután testreszabta a sablonokat és a parancsfájlokat, kövesse az [erőforrások Erőforrás-kezelő-sablonokkal és az Azure PowerShell-lel való üzembe helyezéséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)szükséges lépéseket. A cikk általános információkat tartalmaz az Azure PowerShell azure Resource Manager-sablonokkal való használatáról az erőforrások Azure-ba való üzembe helyezéséhez.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>A PowerShell használatával a DevTest-laborokban végrehajtható gyakori feladatok
Számos más gyakori feladatok, amelyek segítségével automatizálható a PowerShell használatával. A dokumentáció következő szakaszai ismertetik a feladatok végrehajtásához szükséges lépéseket.

* [Egyéni lemezkép létrehozása Virtuális merevlemez-fájlból a PowerShell használatával](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [VHD-fájl feltöltése a labor tárfiókjába a PowerShell használatával](devtest-lab-upload-vhd-using-powershell.md)
* [Külső felhasználó hozzáadása laborhoz a PowerShell használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Labor egyéni szerepkör létrehozása a PowerShell használatával](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan hozhat létre [egy privát Git-tárházat,](devtest-lab-add-artifact-repo.md) ahol a testreszabott sablonokat vagy parancsfájlokat tárolja.
* Fedezze fel az [Azure Resource Manager-sablonokat az Azure gyorsútmutató sablongyűjteményéből.](https://github.com/Azure/azure-quickstart-templates)
