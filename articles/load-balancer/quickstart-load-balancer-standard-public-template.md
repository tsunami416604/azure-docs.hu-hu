---
title: 'Gyors útmutató: A Standard load balancer létrehozása – Azure Resource Manager-sablon'
titlesuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Standard load balancer az Azure Resource Manager-sablon használatával.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 00dbb29cac30f2a3bbecf71727c79617e24af3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441302"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-an-azure-resource-manager-template"></a>Gyors útmutató: Virtuális gépek terhelésének elosztásához az Azure Resource Manager-sablon használatával egy Standard load balancer létrehozása

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe Azure Resource Manager-sablon, amely létrehoz egy Standard terheléselosztót, virtuális gépek terhelésének elosztásához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-standard-load-balancer"></a>Standard load balancer létrehozása

A standard Load Balancer támogatja a csak a Standard nyilvános IP-címet. Amikor létrehoz egy Standard load balancert, is létre kell hoznia egy új Standard nyilvános IP-cím, a Standard load Balancer az előtér konfigurálva van.

Számos módszer használatával egy Standard load balancer létrehozása. Ebben a rövid útmutatóban az Azure PowerShell használatával üzembe helyezése egy [Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg.

Üzembe helyezése és kezelése az Azure-megoldások kapcsolatos fogalmak megismeréséhez tekintse meg [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/). Az Azure Load Balancer kapcsolódó további sablonokat talál [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Válassza ki **kipróbálás** az Azure Cloud Shellt, és kövesse az utasításokat, hogy jelentkezzen be az Azure-bA az alábbi kódblokkot.

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

   Várjon, amíg megjelenik a rendszer kéri a konzolról.

1. Válassza ki **másolási** az előző kódblokkot, amellyel a PowerShell-parancsfájl másolása a.

1. Kattintson a jobb gombbal a rendszerhéj konzol ablaktáblában, majd **beillesztési**.

1. Adja meg az értékeket.

   A sablon üzembe helyezéséhez létrehoz három rendelkezésre állási zónák. A rendelkezésre állási zónák csak a támogatott [bizonyos régiókban](../availability-zones/az-overview.md). A támogatott régiók egyikét használhatja. Ha nem biztos, adja meg a **centralus**.

   Az erőforráscsoport neve a projekt nevére a **rg** hozzáfűzve. Az erőforráscsoport nevét a következő szakaszban van szüksége.

Nagyjából 10 percet a sablon üzembe helyezéséhez vesz igénybe.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **erőforráscsoportok** a bal oldali ablaktáblán.

1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot. Az alapértelmezett erőforráscsoport neve a projekt nevére a **rg** hozzáfűzve.

1. Válassza ki a terheléselosztóhoz. Az alapértelmezés szerint ez a projekt nevére a **-lb** hozzáfűzve.

1. Csak a nyilvános IP-cím IP cím részét másolja, majd illessze be a böngésző címsorába. A böngésző megjeleníti az alapértelmezett oldalt a Internet Information Services (IIS) webkiszolgálót.

   ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Szeretné látni a terheléselosztót három virtuális gép között osztja el a forgalmat, kényszerítheti a webböngésző frissítését áramlását az ügyfélszámítógépen.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség rájuk, törölje az erőforráscsoportot, a terheléselosztó és minden kapcsolódó erőforrás. Ehhez nyissa meg az Azure Portalra, válassza ki az erőforráscsoportot, amely tartalmazza a terheléselosztóhoz, és válassza **erőforráscsoport törlése**.

## <a name="next-steps"></a>További lépések

Ebben a rövid, Standard load balancer létrehozása, virtuális gépeket csatolt hozzá, konfigurálta a terheléselosztó forgalmi szabályát, az állapotfigyelő mintavételező fejeződött és majd tesztelte a terheléselosztót.

További információkért lépjen tovább az oktatóanyagok Load Balancer.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
 