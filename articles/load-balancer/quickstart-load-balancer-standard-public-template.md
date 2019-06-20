---
title: 'Gyors útmutató: A Standard Load Balancer létrehozása – Azure Resource Manager-sablon'
titlesuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Standard Load Balancer az Azure Resource Manager-sablon használatával.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a95bedc8b2b395f856512ec49bae630666fe36da
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272884"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Gyors útmutató: Hozzon létre egy Standard Load Balancer virtuális gépek terhelésének elosztásához az Azure Resource Manager-sablon használatával

A terheléselosztás magasabb szintű rendelkezésre állást és méretezést biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy Azure Resource Manager-sablont (VM) virtuális gépek terheléselosztása a standard load balancer létrehozása a.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-standard-load-balancer"></a>Standard terheléselosztó létrehozása

A Standard Load Balancer csak a standard nyilvános IP-címeket támogatja. Amikor létrehoz egy Standard Load Balancer, is létre kell hoznia egy új Standard nyilvános IP-cím az előtéri, a Standard Load Balancer konfigurálva van. Számos módon használható standard load balancer létrehozása. Ebben a rövid útmutatóban az Azure PowerShell használatával üzembe helyezése egy [Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json). A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Üzembe helyezése és kezelése az Azure-megoldások kapcsolatos fogalmak megismeréséhez tekintse meg [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/). További kapcsolódó Azure Load Balancer-sablonnal kapcsolatban [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

1. Válassza ki **kipróbálás** , a következő kódrészletet az Azure Cloud shellt, és kövesse az utasításokat, hogy jelentkezzen be az Azure-bA.

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
2. Válassza ki **másolási** az előző kódblokkot, amellyel a PowerShell-parancsfájl másolása a.
3. Kattintson a jobb gombbal a rendszerhéj konzol ablaktáblában, majd **beillesztési**.
4. Adja meg az értékeket.

   A sablon üzembe helyezéséhez létrehoz három rendelkezésre állási zónák.  A rendelkezésre állási zónák csak a támogatott [bizonyos régiókban](../availability-zones/az-overview.md). A támogatott régiók egyikét használhatja. Ha nem biztos benne, adja meg a **centralus**.

   Az erőforráscsoport neve a projekt nevére a **rg** hozzáfűzve. Az erőforráscsoport nevét a következő szakaszban van szüksége.

Nagyjából 10 percet a sablon üzembe helyezéséhez vesz igénybe.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **erőforráscsoportok** a bal oldali ablaktáblán.
1. Válassza ki az előző szakaszban létrehozott erőforráscsoportot.  Az alapértelmezett erőforráscsoport neve a projekt nevére a **rg** hozzáfűzve.
1. Válassza ki a terheléselosztóhoz.  Nincs csak egy terheléselosztó. Alapértelmezés szerint ez a projekt nevére a **-lb** hozzáfűzve.
1. Másolja a nyilvános IP-cím (csak az IP-cím-rész), majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

   ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

A három virtuális gép közötti terheléselosztó megtekintéséhez, kényszerített frissítését a webböngésző áramlását az ügyfélszámítógépen.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, terheléselosztó és minden kapcsolódó erőforrás. Ehhez válassza ki az erőforráscsoportot, amely tartalmazza a terheléselosztó az Azure Portalról, és válassza **erőforráscsoport törlése**.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Standard Load Balancer, virtuális gépeket csatolt hozzá, a terheléselosztó forgalmának szabályát, az állapotadat-mintavétel konfigurálva, majd tesztelte a terheléselosztót. Ha bővebb információra van szüksége az Azure Load Balancerrel kapcsolatban, folytassa az Azure Load Balancerről szóló oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Azure Load Balancer-oktatóanyagok](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
