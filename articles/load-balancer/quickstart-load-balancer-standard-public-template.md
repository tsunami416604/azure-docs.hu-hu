---
title: 'Rövid útmutató: Terheléselosztó létrehozása – Azure-sablon'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy terheléselosztót az Azure Resource Manager sablon használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 175c5a36c873d16d50d5192a489133a01018e335
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474596"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Rövid útmutató: Hozzon létre egy terheléselosztót a terheléselosztási virtuális gépekhez az Azure Resource Manager sablon használatával

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ez a rövid útmutató bemutatja, hogyan telepíthet egy Azure Resource Manager-sablont, amely szabványos terheléselosztót hoz létre a terheléselosztási virtuális gépek hez. Az Erőforrás-kezelő sablon használata kevesebb lépést tesz lefordítva más telepítési módszerekhez.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-a-load-balancer"></a>Load Balancer-terheléselosztó létrehozása

A terheléselosztónak és a nyilvános IP-sus-oknak egyezniük kell. Standard terheléselosztó létrehozásakor létre kell hoznia egy új szabványos nyilvános IP-címet is, amely a Standard terheléselosztó előtétjeként van konfigurálva. Ha alapszintű terheléselosztót szeretne létrehozni, használja [ezt a sablont.](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/) A Microsoft azt javasolja, hogy a standard termékváltozat éles számítási feladatokhoz.

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure gyorsindítási sablonjaiból származik.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="58-122":::

A sablonban több Azure-erőforrás is definiálva van:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): a terheléselosztóhoz és a három virtuális gép mindegyikéhez.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 közülük)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 közülük)
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 közülük): az IIS és a weblapok konfigurálásával

Az Azure Load Balancerhez kapcsolódó további sablonokat az [Azure gyorsindítási sablonjai](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)ban találja.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza a **Próbálja ki** a következő kódblokkból az Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Várjon, amíg megjelenik a kérdés a konzolról.

1. Válassza a **Másolás** az előző kódblokkból a PowerShell-parancsfájl másolásához.

1. Kattintson a jobb gombbal a rendszerhéj konzolablaktáblájára, és válassza **a Beillesztés parancsot.**

1. Adja meg az értékeket.

   A sablon központi telepítése három rendelkezésre állási zónát hoz létre. A rendelkezésre állási zónák csak [bizonyos régiókban](../availability-zones/az-overview.md)támogatottak. Használja a támogatott régiók egyikét. Ha nem biztos benne, írja be **a centralus**.

   Az erőforráscsoport neve a projekt neve **rg** csatolt. Az erőforráscsoport nevére a következő szakaszban van szükség.

A sablon üzembe helyezése körülbelül 10 percet vesz igénybe. Ha elkészült, a kimenet hasonló a következőkhöz:

![Az Azure Standard Load Balancer Resource Manager sablon PowerShell-telepítési kimenet](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Az Azure PowerShell a sablon üzembe helyezéséhez szolgál. Az Azure PowerShell mellett használhatja az Azure Portalt, az Azure CLI-t és a REST API-t is. További telepítési módszerekről a [Sablonok telepítése ..](../azure-resource-manager/templates/deploy-portal.md)

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza **az Erőforráscsoportok lehetőséget** a bal oldali ablaktáblából.

1. Jelölje ki az előző szakaszban létrehozott erőforráscsoportot. Az alapértelmezett erőforráscsoport neve a projekt neve **rg** csatolt.

1. Válassza ki a terheléselosztót. Az alapértelmezett neve a projekt neve **-lb** hozzáfűzve.

1. Másolja csak a nyilvános IP-cím IP-címrészét, majd illessze be a böngésző címsorába.

   ![Az Azure szabványos terheléselosztó erőforrás-kezelősablonnyilvános IP-címe](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    A böngésző megjeleníti az Internet Information Services (IIS) webkiszolgáló alapértelmezett lapját.

   ![IIS-webkiszolgáló](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

A terheléselosztó mindhárom virtuális gép közötti elosztási forgalom megtekintéséhez kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szüksége, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Ehhez nyissa meg az Azure Portalon, válassza ki a terheléselosztót tartalmazó erőforráscsoportot, majd válassza az **Erőforráscsoport törlése lehetőséget.**

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy standard terheléselosztót, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmi szabályt, elvégzett egy állapotmintát, majd tesztelte a terheléselosztót.

További információért folytassa a Terheléselosztó oktatóanyagát.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
