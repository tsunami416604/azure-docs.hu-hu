---
title: Virtuális gép Azure Resource Manager sablonjának megtekintése és használata | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Resource Manager sablont egy virtuális gépről más virtuális gépek létrehozásához
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
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: c14abf3acce0084507a03f3d34fdd59566d88c28
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854275"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Virtuális gépek létrehozása Azure Resource Manager sablon használatával 

Ha virtuális gépet (VM) hoz létre a DevTest Labs szolgáltatásban a [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)keresztül, megtekintheti a Azure Resource Manager sablont a virtuális gép mentése előtt. A sablon ezután felhasználható úgy, hogy több, azonos beállításokkal rendelkező laboratóriumi virtuális gépet hozzon létre.

Ez a cikk a több virtuális gépre kiterjedő és a különálló VM Resource Manager-sablonokat ismerteti, és bemutatja, hogyan tekintheti meg és mentheti a sablonokat a virtuális gépek létrehozásakor.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Multi-VM és Single-VM Resource Manager-sablonok
A DevTest Labs-ben kétféleképpen hozhatók létre virtuális gépek a Resource Manager-sablonok használatával: a Microsoft. segédösszetevője/Labs/virtualmachines erőforrás kiépítése, vagy a Microsoft. számítási/virtualmachines erőforrás kiépítése. Mindegyiket különböző forgatókönyvekben használják, és eltérő engedélyekkel kell rendelkezniük.

- A Microsoft. segédösszetevője/Labs/virtualmachines típusú erőforrást használó Resource Manager-sablonok (ahogy a sablon "Resource" tulajdonságában vannak deklarálva) egyéni Tesztkörnyezet-virtuális gépeket is létrehozhatnak. Minden virtuális gép ekkor egyetlen elemként jelenik meg a DevTest Labs virtuális gépek listájában:

   ![A virtuális gépek listája egyetlen elemként a DevTest Labs Virtual Machines listában](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ezt a típusú Resource Manager-sablont a **New-AzResourceGroupDeployment Azure PowerShell-** paranccsal vagy az Azure CLI-parancs az az **Group Deployment Create**parancsával lehet kiépíteni. Rendszergazdai engedélyekre van szükség, így a DevTest Labs felhasználói szerepkörhöz hozzárendelt felhasználók nem hajthatják végre a telepítést. 

- A Microsoft. számítási/virtualmachines erőforrástípus használó Resource Manager-sablonok több virtuális gépet is kiépíthetnek egyetlen környezetként a DevTest Labs virtuális gépek listájában:

   ![A virtuális gépek listája egyetlen elemként a DevTest Labs Virtual Machines listában](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Az ugyanabban a környezetben lévő virtuális gépeket együtt lehet kezelni, és ugyanazt az életciklust használják. A DevTest Labs-felhasználói szerepkörrel rendelkező felhasználók létrehozhatnak környezeteket a sablonok használatával, feltéve, hogy a rendszergazda úgy konfigurálta a labort, hogy a módszert használja.

A cikk további része a Microsoft. segédösszetevője/Labs/virtualmachines szolgáltatást használó Resource Manager-sablonokat ismerteti. Ezeket a laboratóriumi rendszergazdák használják a laboratóriumi virtuális gépek létrehozásának automatizálására (például a megkövetelhető virtuális gépekre) vagy a aranyszínű képek generálására (például a rendszerkép-előállítóra).

[Azure Resource Manager sablonok létrehozásával kapcsolatos ajánlott eljárások](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) számos útmutatást és javaslatot nyújtanak a megbízható és könnyen használható Azure Resource Manager sablonok létrehozásához.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Virtuális gép Resource Manager-sablonjának megtekintése és mentése
1. A virtuális gép létrehozásának megkezdéséhez kövesse az első virtuális gép [tesztkörnyezetben](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) való létrehozásához szükséges lépéseket.
1. Adja meg a virtuális gép szükséges adatait, és adja hozzá az ehhez a virtuális GÉPHEZ használni kívánt összetevőket.
1. Swtich a **Speciális beállítások** lapra. 
1. A beállítások konfigurálása ablak alján kattintson az ARM- **sablon megtekintése**elemre.
1. Másolja ki és mentse el a Resource Manager-sablont később egy másik virtuális gép létrehozásához.

   ![A későbbi használatra menteni kívánt Resource Manager-sablon](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

A Resource Manager-sablon mentése után frissítenie kell a sablon paraméterek szakaszát, mielőtt használni lehetne. Létrehozhat egy paraméter. JSON fájlt, amely a tényleges Resource Manager-sablonon kívül csak a paramétereket szabja testre. 

![Paraméterek testreszabása JSON-fájl használatával](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

A Resource Manager-sablon most már készen áll a [virtuális gép létrehozására](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Lejárati dátum beállítása
Olyan forgatókönyvek esetén, mint például a képzés, a bemutatók és a próbaverziók, érdemes lehet virtuális gépeket létrehozni, és azokat automatikusan törölni rögzített időtartam után, hogy ne kelljen szükségtelen költségeket fizetnie. A virtuális gép **expirationDate** tulajdonságának megadásával létrehozhat egy lejárati dátummal rendelkező Tesztkörnyezet-virtuális gépet. Tekintse meg ugyanezt a Resource Manager-sablont a [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration)-tárházban.



### <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [több virtuális gépes környezet létrehozása a Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md).
* [Resource Manager-sablon üzembe helyezése virtuális gép létrehozásához](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Ismerje meg a további a rövid útmutató Resource Manager-sablonokkal a DevTest Labs automation a [nyilvános DevTest Labs GitHub-adattárat](https://github.com/Azure/azure-quickstart-templates).
