---
title: Egy belső terheléselosztó létrehozása – Azure-sablonnal
titlesuffix: Azure Load Balancer
description: Ismerje meg, hogyan hozható létre belső terheléselosztó sablon használatával a Resource Managerben
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: d029de11c7101e961506f1fa46f5a71c6efb103e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044008"
---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Belső terheléselosztó létrehozása sablon használatával

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Sablon](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>A sablon üzembe helyezése kattintással végrehajtható üzembe helyezéssel

A nyilvános tárházban elérhető mintasablon a fent leírt forgatókönyv létrehozásához használt alapértelmezett értékeket tartalmazó paraméterfájlt használja. Ha a sablon üzembe helyezését kattintással végrehajtható üzembe helyezéssel szeretné elvégezni, kövesse [ezt a hivatkozást](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), kattintson az **Üzembe helyezés az Azure-on** lehetőségre, cserélje ki az alapértelmezett paraméterértékeket, ha szükséges, majd kövesse a portálon megjelenő utasításokat.

## <a name="deploy-the-template-by-using-powershell"></a>A sablon üzembe helyezése a PowerShell használatával

A letöltött sablon PowerShell használatával történő üzembe helyezéséhez kövesse az alábbi lépéseket.

1. Ha még nem használta az Azure PowerShellt, tekintse meg [How to Install and Configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című részt, majd kövesse az utasításokat egészen az utolsó lépésig az Azure-ba való bejelentkezéshez és az előfizetése kiválasztásához.
2. Töltse le a paramétereket tartalmazó fájlt a helyi lemezre.
3. Szerkessze a fájlt, majd mentse el.
4. A **New-AzureRmResourceGroupDeployment** parancsmag futtatásával és a sablon használatával hozzon létre egy erőforráscsoportot.

    ```azurecli
    New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
        -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
        -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>A sablon üzembe helyezése az Azure parancssori felület használatával

Az alábbi lépéseket követve hozhatja létre a sablont az Azure parancssori felület használatával.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **azure config mode** parancs futtatásával váltson az Erőforrás-kezelő módra, a lent látható módon.

    ```azurecli
    azure config mode arm
    ```

    A fenti parancs várható kimenete:

        info:    New mode is arm

3. Nyissa meg a paraméterfájlt, jelölje ki a tartalmát, és mentse el a számítógépén található egyik fájlba. Ennél a példánál a paramétereket tartalmazó fájlt a *parameters.json* fájlba mentettük.
4. Futtassa az **azure group deployment create** parancsot, hogy a fent letöltött és módosított sablonnal és paraméterfájlokkal üzembe helyezhesse az új belső terheléselosztót. A kimenet után látható lista ismerteti a használt paramétereket.

    ```azurecli
    azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json
    ```

## <a name="next-steps"></a>További lépések

[A terheléselosztó elosztási módjának konfigurálása forrás IP-affinitás használatával](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)

A JSON-szintaxist és a egy terheléselosztó-sablon tulajdonságainak [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
