---
title: "Internetkapcsolattal rendelkező terheléselosztó létrehozása – Azure-sablon | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó a Resource Managerben sablon használatával"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: b24f4729-4559-4458-8527-71009d242647
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: f30a450c7d3aa73bb2fb196ca7de94436916bed0
ms.contentlocale: hu-hu
ms.lasthandoff: 04/27/2017

---

# <a name="creating-an-internet-facing-load-balancer-using-a-template"></a>Internetkapcsolattal rendelkező terheléselosztó létrehozása sablon használatával

> [!div class="op_single_selector"]
> * [Portál](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Sablon](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [azt is megismerheti, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó a klasszikus üzemi modell használatával](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>A sablon üzembe helyezése kattintással végrehajtható üzembe helyezéssel

A nyilvános tárházban elérhető mintasablon a fent leírt forgatókönyv létrehozásához használt alapértelmezett értékeket tartalmazó paraméterfájlt használja. Ha a sablon üzembe helyezését kattintással végrehajtható üzembe helyezéssel szeretné elvégezni, kövesse [ezt a hivatkozást](http://go.microsoft.com/fwlink/?LinkId=544801), kattintson az **Üzembe helyezés az Azure-on** lehetőségre, cserélje ki az alapértelmezett paraméterértékeket, ha szükséges, majd kövesse a portálon megjelenő utasításokat.

## <a name="deploy-the-template-by-using-powershell"></a>A sablon üzembe helyezése a PowerShell használatával

A letöltött sablon PowerShell használatával történő üzembe helyezéséhez kövesse az alábbi lépéseket.

1. Ha még nem használta az Azure PowerShellt, tekintse meg [How to Install and Configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című részt, majd kövesse az utasításokat egészen az utolsó lépésig az Azure-ba való bejelentkezéshez és az előfizetése kiválasztásához.
2. A **New-AzureRmResourceGroupDeployment** parancsmag futtatásával és a sablon használatával hozzon létre egy erőforráscsoportot.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
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

3. Lépjen a böngészőből a [Gyorssablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) lapra, másolja át és illessze be a json-fájl tartalmát egy új fájlba a számítógépén. Ehhez a forgatókönyvhöz az alábbi értékeket át kell másolnia egy **c:\lb\azuredeploy.parameters.json** nevű fájlba.
4. Futtassa az **azure group deployment create** parancsmagot, hogy a fent letöltött és módosított sablonnal és paraméterfájlokkal üzembe helyezhesse az új terheléselosztót. A kimenet után látható lista ismerteti a használt paramétereket.

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>Következő lépések

[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)

