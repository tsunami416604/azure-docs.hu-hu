---
title: Laborok létrehozása vagy módosítása Azure Resource Manager sablonok használatával
description: Megtudhatja, hogyan használhatja Azure Resource Manager-sablonokat a PowerShell-lel a Labs automatikus létrehozására vagy módosítására egy DevTest-laborban
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
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898541"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Labs automatikus létrehozása vagy módosítása Azure Resource Manager-sablonokkal és a PowerShell-lel

A DevTest Labs számos Azure Resource Manager sablont és PowerShell-parancsfájlt biztosít, amelyek segítségével gyorsan és automatikusan hozhat létre új laborokat, illetve módosíthatja a meglévő laborokat, majd telepítheti ezeket az erőforrásokat.

Ebből a cikkből megtudhatja, hogyan használhatja ezeket a sablonokat és parancsfájlokat a laborok létrehozásának, módosításának és üzembe helyezésének automatizálására. A cikk azt is bemutatja, hol találhat további információt arról, hogyan használható a PowerShell a DevTest Labs gyakori feladatainak végrehajtásához.

## <a name="step-1-gather-your-templates-and-scripts"></a>1. lépés: a sablonok és a parancsfájlok összegyűjtése
Előre elkészített [Azure Resource Manager sablonokat](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) és [PowerShell-parancsfájlokat](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) a nyilvános GitHub- [tárházban](https://github.com/Azure/azure-devtestlab)talál. Használhatja őket a-ként, vagy testre szabhatja őket az igényeinek megfelelően, és tárolhatja őket a saját [privát git](devtest-lab-add-artifact-repo.md)-tárházában.

## <a name="step-2-modify-your-azure-resource-manager-template"></a>2. lépés: a Azure Resource Manager-sablon módosítása
Ha még nem hozott létre sablont, kövesse az [első Azure Resource Manager-sablon létrehozása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) című témakör lépéseit.

Emellett a [Azure Resource Manager sablonok létrehozásával kapcsolatos ajánlott eljárások](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) számos útmutatást és javaslatot nyújtanak a megbízható és könnyen használható Azure Resource Manager sablonok létrehozásához. Általában a megadott megközelítések vagy példák valamelyikének egy változatát fogja használni, és módosítania kell a sablont az igényeinek megfelelően.

## <a name="step-3-deploy-resources-with-powershell"></a>3. lépés: erőforrások üzembe helyezése a PowerShell-lel
Miután testreszabta a sablonokat és a parancsfájlokat, kövesse az [erőforrások Resource Manager-sablonokkal és Azure PowerShell való üzembe helyezéséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)szükséges lépéseket. A cikk általános információkat nyújt a Azure PowerShell használatáról Azure Resource Manager sablonokkal az erőforrások Azure-ba történő telepítéséhez.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>A DevTest Labs-ben elvégezhető gyakori feladatok a PowerShell használatával
A PowerShell használatával számos más gyakori feladat is automatizálható. A dokumentáció következő fejezetei a feladatok végrehajtásához szükséges lépéseket ismertetik.

* [Egyéni rendszerkép létrehozása VHD-fájlból a PowerShell használatával](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [VHD-fájl feltöltése a labor Storage-fiókjába a PowerShell használatával](devtest-lab-upload-vhd-using-powershell.md)
* [Külső felhasználó hozzáadása laborhoz a PowerShell használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Egyéni labor-szerepkör létrehozása a PowerShell használatával](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan hozhat létre egy [privát git-tárházat](devtest-lab-add-artifact-repo.md) , ahol a testreszabott sablonokat vagy parancsfájlokat fogja tárolni.
* Ismerkedjen meg a [Azure Resource Manager-sablonokkal az Azure Gyorsindítás sablon-galériájában](https://github.com/Azure/azure-quickstart-templates).
