---
title: Megtekintése és használata a virtuális gép Azure Resource Manager-sablonnal |} Microsoft Docs
description: Az Azure Resource Manager-sablon a virtuális gép használata a többi virtuális gép létrehozása
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1b96c801e3c5a43f23d4c63c13a611c24dd6705b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788675"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Egy Azure Resource Manager-sablon használatával virtuális gépek létrehozása 

Ha hoz létre egy virtuális gép (VM) a DevTest Labs szolgáltatásban keresztül a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040), megtekintheti az Azure Resource Manager-sablon, mielőtt mentené a virtuális Gépet. A sablon majd használható alapjaként, ugyanazokkal a beállításokkal további tesztlabor virtuális gépek létrehozásához.

Ez a cikk single-virtuális gép Resource Manager-sablonok és virtuális Gépre kiterjedő ismerteti és bemutatja, hogyan megtekintése, és menti a sablont, virtuális gép létrehozásakor.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Több virtuális Gépre kiterjedő és single-virtuális gép Resource Manager-sablonok
Virtuális gépek létrehozása a DevTest Labs szolgáltatásban Resource Manager-sablon használatával két módja van: kiépíteni a Microsoft.DevTestLab/labs/virtualmachines erőforrás vagy a Microsoft.Commpute/virtualmachines erőforrás kiépítéséhez. Minden olyan más esetekben használható, és különböző engedélyekkel kell rendelkeznie.

- Resource Manager-sablonok, amelyek Microsoft.DevTestLab/labs/virtualmachines erőforrás típusa (a "resource" tulajdonságban a sablon) hozhat létre egyéni labor virtuális gépeken. Minden virtuális gép akkor jön létre csak egy elemet a DevTest Labs szolgáltatásban virtuális gépek listában:

   ![Egyetlen elem DevTest Labs szolgáltatásban virtuális gépek, virtuális gépek listája](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Az ilyen típusú Resource Manager-sablon az Azure PowerShell-paranccsal helyezhetők **New-AzureRmResourceGroupDeployment** vagy az Azure CLI paranccsal **az csoport központi telepítésének létrehozása**. Az rendszergazdai engedélyekkel kell rendelkeznie, így a DevTest Labs szolgáltatásban felhasználói szerepkörrel felruházott felhasználók nem végezhető el a központi telepítés. 

- Resource Manager-sablonok, amelyek Microsoft.Compute/virtualmachines erőforrás típusa, egy egyetlen környezetben listájában DevTest Labs szolgáltatásban virtuális gépek több virtuális gép építhető ki:

   ![Egyetlen elem DevTest Labs szolgáltatásban virtuális gépek, virtuális gépek listája](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuális gépek ugyanabban a környezetben együtt kezelheti, és az azonos életciklussal megosztani. DevTest Labs szolgáltatásban felhasználói szerepkörrel felruházott felhasználók használja ezeket a sablonokat, mindaddig, amíg a rendszergazda úgy állította be a labor ily módon tesztkörnyezetek hozhatók létre.

Ez a cikk fennmaradó Mirosoft.DevTestLab/labs/virtualmachines használó Resource Manager-sablonok ismerteti. Ezek használhatók labor rendszergazdák által automatizálhatja a labor virtuális gép létrehozása (például claimable virtuális gépek) és a mesterlemez (például a lemezkép gyári).

[Ajánlott eljárások Azure Resource Manager-sablonok létrehozásához](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) kínál számos irányelvek és hozhat létre Azure Resource Manager-sablonok, amely javaslatokat is megbízható és könnyen használható.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Megtekintése és a virtuális gép Resource Manager-sablon mentése
1. Kövesse a [az első virtuális gép létrehozása egy tesztkörnyezetben](devtest-lab-create-first-vm.md) egy virtuális gép létrehozásának megkezdéséhez.
1. Adja meg a szükséges adatokat a virtuális gép, és adja hozzá a virtuális gép kívánt összetevőihez.
1. A konfigurálás beállítások ablak alján válassza **nézet ARM-sablon**.

   ![Nézet ARM-sablon gomb](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Másolja ki és mentse a Resource Manager-sablon létrehozni egy másik virtuális gépet későbbi használatra.

   ![Későbbi használatra menti a Resource Manager-sablon](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

A Resource Manager-sablon mentése után frissítenie kell a sablon a Paraméterek szakaszban előtt is használhatja. Létrehozhat egy parameter.json, amely csak a paraméterek kívül a tényleges Resource Manager sablon testreszabása. 

![A JSON-fájl használatával paraméterek testreszabása](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

A Resource Manager-sablon segítségével készen áll [hozzon létre egy virtuális Gépet](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [virtuális Gépre kiterjedő környezetek létrehozása a Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md).
* [Virtuális gép létrehozása egy Resource Manager-sablon üzembe helyezése](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm)
* További gyors üzembe helyezés Resource Manager-sablonok a DevTest Labs automatizálásra megismerkedhet a [nyilvános DevTest Labs GitHub-tárház](https://github.com/Azure/azure-quickstart-templates).
