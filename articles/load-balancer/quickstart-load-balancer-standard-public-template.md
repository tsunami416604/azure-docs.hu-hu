---
title: 'Gyors útmutató: Load Balancer létrehozása – Azure-sablon'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre terheléselosztó-t a Azure Resource Manager sablon használatával.
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
ms.date: 01/27/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 69503968ece5e68b6e4777d72713565158009949
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843854"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Gyors útmutató: Load Balancer létrehozása a virtuális gépek terheléselosztásához Azure Resource Manager sablon használatával

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe olyan Azure Resource Manager-sablont, amely létrehoz egy standard Load balancert a virtuális gépek terheléselosztásához. A Resource Manager-sablon használata kevesebb lépést vesz igénybe a többi üzembe helyezési módszer összehasonlításával.

A [Resource Manager-sablon](../azure-resource-manager/templates/overview.md) egy JavaScript Object Notation (JSON) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon deklaratív szintaxist használ, amely lehetővé teszi, hogy a telepítéshez szükséges programozási parancsok sorrendjének megírása nélkül adja meg az üzembe helyezni kívánt adatokat. Ha többet szeretne megtudni a Resource Manager-sablonok fejlesztéséről, tekintse meg a [Resource Manager dokumentációját](/azure/azure-resource-manager/) és a [sablon referenciáját](/azure/templates/microsoft.network/loadbalancers).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása

A Load Balancer és a nyilvános IP-címeknek egyezniük kell. Standard Load Balancer létrehozásakor létre kell hoznia egy új szabványos nyilvános IP-címet is, amely a standard Load Balancer előtérben van konfigurálva. Ha alapszintű Load Balancer szeretne létrehozni, használja [ezt a sablont](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). A Microsoft a standard SKU használatát javasolja üzemi számítási feladatokhoz.

Az ebben a rövid útmutatóban használt sablon egy rövid útmutató [sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

[!code-json[<Azure Resource Manager template create standard load balancer>](~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json)]

Több Azure-erőforrás van definiálva a sablonban:

- **Microsoft. Network/loadBalancers**
- **Microsoft. Network/nyilvános IP**: a terheléselosztó számára.
- **Microsoft. Network/networkSecurityGroups**
- **Microsoft. Network/virtualNetworks**
- **Microsoft. számítási/virutalMachines** (3)
- **Microsoft. Network/nyilvános IP** (3): mindhárom virtuális géphez.
- **Microsoft. Network/networkInterfaces** (3 közülük)
- **Microsoft. számítás/virtualMachine/Extensions** (3): az IIS és a weblapok konfigurálására használható.

A Azure Load Balancer kapcsolódó további sablonok kereséséhez tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Válassza a **kipróbálás** a következő kódrészletből lehetőséget a Azure Cloud Shell megnyitásához, majd kövesse az utasításokat az Azure-ba való bejelentkezéshez.

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

   Várjon, amíg megjelenik a-konzolon megjelenő kérdés.

1. A PowerShell-szkript másolásához válassza a **Másolás** az előző kódrészletből lehetőséget.

1. Kattintson a jobb gombbal a rendszerhéj-konzol ablaktáblára, majd válassza a **Beillesztés**lehetőséget.

1. Adja meg az értékeket.

   A sablon központi telepítése három rendelkezésre állási zónát hoz létre. A rendelkezésre állási zónák csak [bizonyos régiókban](../availability-zones/az-overview.md)támogatottak. Használja az egyik támogatott régiót. Ha nem biztos benne, adja meg a **CentralUS**.

   Az erőforráscsoport neve a projekt neve **RG** hozzáfűzéssel. A következő szakaszban szüksége lesz az erőforráscsoport nevére.

A sablon üzembe helyezése körülbelül 10 percet vesz igénybe. Ha elkészült, a kimenet a következőhöz hasonló:

![Azure standard Load Balancer Resource Manager-sablon PowerShell üzembe helyezési kimenete](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

A Azure PowerShell a sablon üzembe helyezésére szolgál. A Azure PowerShellon kívül használhatja a Azure Portal, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Válassza ki az **erőforráscsoportok** elemet a bal oldali ablaktáblán.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az erőforráscsoport alapértelmezett neve a projekt neve **RG** hozzáfűzéssel.

1. Válassza ki a Load balancert. Az alapértelmezett neve a projekt neve, a **-LB** hozzáfűzéssel.

1. Másolja csak a nyilvános IP-cím IP-címének részét, majd illessze be a böngésző címsorába.

   ![Azure standard Load Balancer Resource Manager-sablon nyilvános IP-címe](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    A böngésző megjeleníti a Internet Information Services (IIS) webkiszolgáló alapértelmezett oldalát.

   ![IIS-webkiszolgáló](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Ha látni szeretné, hogy a terheléselosztó mindhárom virtuális gépen osztja szét a forgalmat, kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölje az erőforráscsoportot, a terheléselosztó és az összes kapcsolódó erőforrást. Ehhez nyissa meg a Azure Portal, válassza ki azt az erőforráscsoportot, amely tartalmazza a terheléselosztó elemet, majd válassza az **erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy standard Load balancert, csatlakoztatott virtuális gépeket, konfigurálta a terheléselosztó forgalmi szabályát, elvégezte az állapot mintavételét, majd tesztelte a terheléselosztó.

További információért folytassa a Load Balancer oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
