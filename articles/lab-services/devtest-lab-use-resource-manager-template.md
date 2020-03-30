---
title: Virtuális gép Azure Resource Manager-sablonjának megtekintése és használata
description: Ismerje meg, hogyan használhatja az Azure Resource Manager sablont egy virtuális gépről más virtuális gépek létrehozásához
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 7064fdeec04f4dc5ae2c73c1a3896cf2d10dd01d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169113"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Virtuális gépek létrehozása Azure Resource Manager-sablon használatával 

Amikor virtuális gépet (VM) hoz létre a DevTest Labs az [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)keresztül, megtekintheti az Azure Resource Manager sablont a virtuális gép mentése előtt. A sablon ezután alapul szolgál, hogy hozzon létre több tesztkörnyezetvirtuális gépek azonos beállításokkal.

Ez a cikk a többvirtuális gép és az egyvirtuálisos erőforrás-kezelő sablonokat ismerteti, és bemutatja, hogyan tekintheti meg és mentheti a sablont virtuális gép létrehozásakor.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Többvirtuális gép és egyvirtuálisos erőforrás-kezelő sablonok
A DevTest Labs alkalmazásban kétféleképpen hozhat létre virtuális gépeket egy Resource Manager-sablon használatával: kiépítheti a Microsoft.DevTestLab/labs/virtualmachines erőforrást, vagy kiépítheti a Microsoft.Compute/virtualmachines erőforrást. Mindegyik különböző forgatókönyvekben használatos, és különböző engedélyeket igényel.

- A Microsoft.DevTestLab/labs/virtualmachines erőforrástípust használó Resource Manager-sablonok (a sablon "erőforrás" tulajdonságában deklarálva) kiépíthetnek egyéni tesztkörnyezetes virtuális gépeket. Minden virtuális gép megjelenik egyetlen elemként a DevTest Labs virtuális gépek listájában:

   ![A virtuális gépek listája egyetlen elemként a DevTest Labs virtuális gépek listájában](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ez a típusú Erőforrás-kezelő sablon az Azure PowerShell **new-azResourceGroupDeployment** parancsán vagy az Azure CLI parancs **az csoport központi telepítésének létrehozásán keresztül építhető ki.** Rendszergazdai engedélyeket igényel, így a DevTest Labs felhasználói szerepkörrel rendelkező felhasználók nem hajthatják végre a központi telepítést. 

- A Microsoft.Compute/virtualmachines erőforrástípust használó Resource Manager-sablonok több virtuális gépet is kiépíthetnek egyetlen környezetként a DevTest Labs virtuális gépek listájában:

   ![A virtuális gépek listája egyetlen elemként a DevTest Labs virtuális gépek listájában](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Az ugyanabban a környezetben lévő virtuális gépek együtt kezelhetők, és ugyanazt az életciklust is megoszthatják. A DevTest Labs felhasználói szerepkörrel rendelkező felhasználók létrehozhatnak környezeteket a sablonok használatával, feltéve, hogy a rendszergazda így konfigurálta a tesztkörnyezetet.

A cikk további ismerteti a Microsoft.DevTestLab/labs/virtualmachines használó Erőforrás-kezelő sablonokat. Ezeket a laboradminisztrátorok használják a lab vm-ek létrehozásának automatizálására (például igény szerint cserélhető virtuális gépek) vagy aranylemezkép-generálás (például lemezkép-gyár).

[Az Azure Resource Manager-sablonok létrehozásának gyakorlati tanácsai](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) számos irányelvet és javaslatot kínálnak az Azure Resource Manager megbízható és könnyen használható sablonjainak létrehozásához.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Virtuálisgép Erőforrás-kezelő sablonjának megtekintése és mentése
1. Kövesse az [első virtuális gép létrehozása egy tesztkörnyezetben](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) a virtuális gép létrehozásának megkezdéséhez.
1. Adja meg a virtuális géphez szükséges adatokat, és adja hozzá a virtuális géphez kívánt összetevőket.
1. Swtich a **Speciális beállítások** lapra. 
1. A Beállítások konfigurálása ablak alján válassza az **ARM nézet sablont.**
1. Másolja és mentse az Erőforrás-kezelő sablont, hogy később egy másik virtuális gép létrehozásához használhassa.

   ![Erőforrás-kezelő sablon későbbi használatra](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Miután mentette az Erőforrás-kezelő sablont, használat előtt frissítenie kell a sablon paramétereit tartalmazó szakaszt. Létrehozhat egy parameter.json t, amely csak a paramétereket szabja testre, a tényleges Erőforrás-kezelő sablonon kívül. 

![Paraméterek testreszabása JSON-fájl használatával](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Az Erőforrás-kezelő sablon készen áll a [virtuális gép létrehozására.](devtest-lab-create-environment-from-arm.md)

## <a name="set-expiration-date"></a>Lejárati dátum beállítása
Például a betanítás, a bemutatók és a próbaverziók esetén érdemes lehet virtuális gépeket létrehozni, és automatikusan törölni őket egy meghatározott időtartam után, hogy ne merüljenek fel szükségtelen költségek. Létrehozhat egy tesztkörnyezet virtuális gép egy lejárati dátum megadásával **expirationDate** tulajdonság a virtuális gép. Nézze meg ugyanazt a Resource Manager sablont a [GitHub-tárházban.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration)



### <a name="next-steps"></a>További lépések
* Megtudhatja, hogy [miként hozhat létre többvirtuális gépes környezeteket az Erőforrás-kezelő sablonokkal.](devtest-lab-create-environment-from-arm.md)
* [Erőforrás-kezelő sablon telepítése virtuális gép létrehozásához](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Fedezzen fel további rövid útmutatóerőforrás-kezelősablonokat a DevTest Labs automatizálásához a [nyilvános DevTest Labs GitHub-tárból.](https://github.com/Azure/azure-quickstart-templates)
