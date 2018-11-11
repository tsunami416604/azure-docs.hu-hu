---
title: Megtekintheti, és a egy virtuális gépet az Azure Resource Manager-sablonnal |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre más virtuális gépek a virtuális gépről az Azure Resource Manager-sablon használatával
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
ms.openlocfilehash: 99d835d1d0e18bf62d6e64b7e5af45b6c53d809e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239508"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Egy Azure Resource Manager-sablon használatával virtuális gépek létrehozása 

Amikor hoz létre egy virtuális gépet (VM) DevTest Labs-környezetben keresztül a [az Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), megtekintheti az Azure Resource Manager-sablon, a virtuális gép mentése előtt. A sablon majd használható alapjaként ugyanazokkal a beállításokkal további tesztlabor virtuális gépek létrehozásához.

Ez a cikk ismerteti a virtuális gépre kiterjedő és a Resource Manager-sablonokkal egyetlen virtuális, és bemutatja, hogyan megtekintéséhez és a egy sablon mentése a virtuális gép létrehozásakor.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Több virtuális gépre kiterjedő és a egy virtuális Resource Manager-sablonok
Virtuális gépek létrehozásához a DevTest Labs szolgáltatásban létrehozott Resource Manager-sablon használatával két módja van: az Microsoft.DevTestLab/labs/virtualmachines erőforrás kiépítéséhez, vagy az Microsoft.Commpute/virtualmachines erőforrás kiépítéséhez. Minden egyes különböző helyzetekben szolgál, és különböző engedélyekkel kell rendelkeznie.

- Resource Manager-sablonok, amelyek Microsoft.DevTestLab/labs/virtualmachines erőforrás típusa (mivel a "erőforrás" tulajdonságban a sablonban) egyes tesztlabor virtuális gépeket helyezhet üzembe. Minden egyes virtuális Géphez, majd a DevTest Labs szolgáltatásban virtuális gépek listában egyetlen elemként jelenik meg:

   ![Virtuális gépek, mint a virtuális gépek DevTest Labs listában egyetlen elemek listája](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Resource Manager-sablon az ilyen típusú keresztül az Azure PowerShell-paranccsal helyezhetők **New-AzureRmResourceGroupDeployment** vagy az Azure CLI-paranccsal **az csoport központi telepítésének létrehozása**. Rendszergazdai engedélyekkel, akkor szükséges, ezért a DevTest Labs felhasználói szerepkörrel felruházott felhasználók nem hajtható végre a központi telepítés. 

- Resource Manager-sablonok, amelyek Microsoft.Compute/virtualmachines erőforrás típusa, a virtuális gépek DevTest Labs listában egyetlen környezethez több virtuális gép építhető ki:

   ![Virtuális gépek, mint a virtuális gépek DevTest Labs listában egyetlen elemek listája](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuális gépek ugyanabban a környezetben együtt is kezelhető, és ugyanaz az életciklusuk. DevTest Labs felhasználói szerepkörrel felruházott felhasználók használja ezeket a sablonokat, mindaddig, amíg a rendszergazda úgy állította be a labor létrehozása ezzel a módszerrel környezetet hozhat létre.

Ez a cikk további részében Mirosoft.DevTestLab/labs/virtualmachines használó Resource Manager-sablonok ismerteti. Ezek segítségével tesztlabor rendszergazdák automatizálhatja labor a virtuális gép létrehozása (például igényelhető virtuális gépek) vagy a mesterlemez. generációs (például kép gyári).

[Ajánlott eljárások az Azure Resource Manager-sablonok létrehozására](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) kínál számos irányelvek és javaslatok létrehozása Azure Resource Manager-sablonok, amelyek segítségével a rendszer megbízható és könnyen használható.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Megtekintheti, és a virtuális gépek Resource Manager-sablon mentése
1. Kövesse a lépéseket [az első virtuális gép létrehozása tesztkörnyezetben](devtest-lab-create-first-vm.md) egy virtuális gép létrehozásának megkezdéséhez.
1. Adja meg a szükséges információkat a virtuális gép, és adja hozzá az összes összetevőt, ez a virtuális gép használni szeretne.
1. A konfigurálás beállításai ablak alján válassza **nézet ARM-sablon**.

   ![ARM-sablon gomb megtekintése](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Másolja és mentse a Resource Manager-sablon létrehozása egy másik virtuális gép későbbi használat céljából.

   ![Resource Manager-sablon mentése későbbi használat céljából](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

A Resource Manager-sablon mentése után azt használata előtt frissítenie kell a sablon a paraméterek szakaszához. Létrehozhat egy parameter.json, amely csak a paraméterek kívül maga a Resource Manager sablon testreszabása. 

![JSON-fájlok használatával paraméterek testreszabása](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

A Resource Manager-sablon most már készen áll a használatra [hozzon létre egy virtuális Gépet](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [több virtuális gépes környezet létrehozása a Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md).
* [Virtuális gép létrehozása Resource Manager-sablon üzembe helyezése](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm)
* Ismerje meg a további a rövid útmutató Resource Manager-sablonokkal a DevTest Labs automation a [nyilvános DevTest Labs GitHub-adattárat](https://github.com/Azure/azure-quickstart-templates).
