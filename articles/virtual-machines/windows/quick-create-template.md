---
title: 'Gyors útmutató: Windows rendszerű virtuális gép létrehozása Resource Manager-sablonnal'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy Windows rendszerű virtuális gépet egy Resource Manager-sablon használatával
author: cynthn
ms.service: virtual-machines-windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: 6ef929a2934d8480ce6d1eca8bb7ba3b70580110
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84551998"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-a-resource-manager-template"></a>Gyors útmutató: Windows rendszerű virtuális gép létrehozása Resource Manager-sablonnal

Ez a rövid útmutató bemutatja, hogyan használható egy Resource Manager-sablon egy Windows rendszerű virtuális gép (VM) üzembe helyezéséhez az Azure-ban. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Nincsenek.

## <a name="create-a-windows-virtual-machine"></a>Windows rendszerű virtuális gép létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)származik.

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json" range="1-225" highlight="67-224":::


Több erőforrás van definiálva a sablonban:

- [**Microsoft. Network/virtualNetworks/Subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): hozzon létre egy alhálózatot.
- [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): hozzon létre egy Storage-fiókot.
- [**Microsoft. Network/nyilvános IP**](/azure/templates/Microsoft.Network/publicIPAddresses): hozzon létre egy nyilvános IP-címet.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): hozzon létre egy hálózati biztonsági csoportot.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): hozzon létre egy virtuális hálózatot.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): hozzon létre egy hálózati adaptert.
- [**Microsoft. számítási/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): hozzon létre egy virtuális gépet.



### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket. Használja az alapértelmezett értékeket, ha vannak ilyenek.

    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása**lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK**gombra.
    - **Hely**: válasszon ki egy helyet.  Például: **USA középső régiója**.
    - **Rendszergazdai Felhasználónév**: adjon meg egy felhasználónevet, például az *Azureus*nevet.
    - **Rendszergazdai jelszó**: adja meg a rendszergazdai fiókhoz használni kívánt jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](faq.md#what-are-the-password-requirements-when-creating-a-vm).
    - **DNS-címke előtagja**: adjon meg egy egyedi azonosítót, amelyet a DNS-címke részeként kíván használni.
    - **Windows operációs rendszer verziója**: válassza ki a virtuális gépen futtatni kívánt Windows-verziót.
    - **Virtuális gép mérete**: válassza ki a virtuális géphez használandó [méretet](sizes.md) .
    - **Hely**: az alapértelmezett érték ugyanaz a hely, mint az erőforráscsoport, ha már létezik.
1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után a **Létrehozás** gombra kattintva hozhatja létre és helyezheti üzembe a virtuális gépet.


Az Azure Portalon helyezhető üzembe a sablon. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal segítségével megkeresheti a virtuális gépet és a létrehozott más erőforrásokat. Az üzembe helyezés befejezése után válassza az **Ugrás az erőforráscsoporthoz** lehetőséget a virtuális gép és egyéb erőforrások megtekintéséhez.


## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, amely törli a virtuális gépet és az erőforráscsoport összes erőforrását. 

1. Válassza ki az **erőforráscsoportot**.
1. Az erőforráscsoport lapján válassza a **Törlés**lehetőséget.
1. Ha a rendszer kéri, írja be az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy egyszerű virtuális gépet telepített egy Resource Manager-sablon használatával. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.


> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
