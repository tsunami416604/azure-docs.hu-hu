---
title: Az Azure Resource Manager-sablonok használatával automatikusan a PowerShell-lel tesztkörnyezetek létrehozása vagy módosítása |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Resource Manager-sablonok a PowerShell-lel tesztkörnyezetek automatikusan a fejlesztési és tesztelési labor létrehozása vagy módosítása
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2c15cac7897e25018057aa47cc2043050ed0ae5b
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413200"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Az Azure Resource Manager-sablonok és PowerShell használatával automatikusan tesztkörnyezetek létrehozása vagy módosítása

DevTest Labs biztosít sok Azure Resource Manager-sablonok és PowerShell-parancsfájlok is gyorsan és új tesztkörnyezetek automatikus létrehozása vagy meglévő labs módosítása és a majd ezeket az erőforrásokat.

Ez a cikk segít a telepítési folyamat, ezek a sablonok és parancsfájlok használatával automatizálhatja a létrehozását, módosítását és központi telepítését a gyakorlatot. Ez a cikk is bemutatja, hol találhatók az egyes gyakori feladatok elvégzéséhez a DevTest Labs szolgáltatásban létrehozott PowerShell használatával kapcsolatos további információk.

## <a name="step-1-gather-your-templates-and-scripts"></a>1. lépés: Gyűjtse össze a sablonok és parancsfájlok
Annak előre elkészített [Azure Resource Manager-sablonok](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) és [PowerShell-parancsfájlok](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) , nyilvános [GitHub-adattár](https://github.com/Azure/azure-devtestlab). Használja őket-van, vagy testre is szabhatja őket az igényeinek, és tárolja őket a saját [privát Git-tárház](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>2. lépés: Az Azure Resource Manager-sablon módosítása
A lépéseket követheti [az első Azure Resource Manager-sablon létrehozása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) Ha soha nem hozott létre sablon előtt.

Emellett [ajánlott eljárások az Azure Resource Manager-sablonok létrehozására](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) kínál számos irányelvek és javaslatok létrehozása Azure Resource Manager-sablonok, amelyek segítségével a rendszer megbízható és könnyen használható. Általában fog használni egy változata, a megközelítések vagy a megadott példák egyike, és módosítsa a sablon az igényeinek.

## <a name="step-3-deploy-resources-with-powershell"></a>3. lépés: A PowerShell erőforrások üzembe helyezése
A sablonok és a parancsfájlok testreszabott, miután hajtsa végre a szükséges lépések [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). A cikk az Azure Resource Manager-sablonok az Azure PowerShell használata az erőforrások üzembe helyezése az Azure általános információkat nyújt.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Gyakori feladatok elvégzése PowerShell-lel DevTest labs szolgáltatásban
Nincsenek számos egyéb gyakori feladatok, amelyek a PowerShell használatával automatizálható. Az alábbi szakaszok a dokumentáció szerkezeti a feladatok végrehajtásához szükséges lépéseket.

* [Egy egyéni lemezkép készítése VHD-fájlból PowerShell-lel](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [VHD-fájl feltöltése PowerShell használatával tesztkörnyezet tárfiókba](devtest-lab-upload-vhd-using-powershell.md)
* [Külső felhasználó hozzáadása egy laborhoz PowerShell-lel](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [PowerShell-lel labor egyéni szerepkör létrehozása](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan hozhat létre egy [privát Git-tárház](devtest-lab-add-artifact-repo.md) tárolására a testre szabott sablonok vagy parancsfájlok.
* Fedezze fel a [Azure Resource Manager-sablonok Azure gyorsindítási sablon katalógusból](https://github.com/Azure/azure-quickstart-templates).
