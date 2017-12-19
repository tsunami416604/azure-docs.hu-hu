---
title: "Hozzon létre egy Azure Application Gateway - sablonok |} Microsoft Docs"
description: "Ez az oldal utasításokat tartalmaz egy Azure Application Gateway Azure Resource Manager-sablonnal történő létrehozásához"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 0aa16e9d7472d2d8c3c251e60a506a7f4223ac1d
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Application Gateway létrehozása az Azure Resource Manager-sablonokkal

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager-sablon](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen találhatóak. Az Application Gateway számos alkalmazáskézbesítési vezérlőszolgáltatást (ADC) biztosít, beleértve a HTTP-terheléselosztást, a cookie-alapú munkamenet-affinitást, a Secure Sockets Layer- (SSL-) alapú kiszervezést, az egyéni állapotmintákat, a többhelyes támogatást és még sok mást. Támogatott funkció teljes listájának megkereséséhez látogasson el a [Alkalmazásátjáró áttekintése](application-gateway-introduction.md)

Ez a cikk útmutatást nyújt a letöltés, és módosítja a meglévő [Azure Resource Manager sablon](../azure-resource-manager/resource-group-authoring-templates.md) a Githubról, majd a sablon a Githubon, PowerShell és az Azure parancssori felület telepítése.

Egyszerűen telepít a sablon közvetlenül a Githubból módosítások nélkül, ha a sablont a Githubból telepítendő kihagyása.

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben az alábbiakat fogja tenni:

* Hozzon létre egy alkalmazás webalkalmazási tűzfal.
* Létrehoz egy VirtualNetwork1 nevű virtuális hálózatot a 10.0.0.0/16 egy fenntartott CIDR-blokkjával.
* Létrehoz egy Appgatewaysubnet nevű alhálózatot, amelynek a CIDR-blokkja 10.0.0.0/28 lesz.
* Beállít két korábban konfigurált háttér IP-címet a webkiszolgálóknak, amelyek között el szeretné osztani a forgalom terhelését. Ebben a példasablonban a háttér IP-cím a 10.0.1.10 és a 10.0.1.11.

> [!NOTE]
> Ezek a beállítások a sablon paraméterei. A sablon testreszabása, módosíthatja a szabályokat, a figyelő, SSL és a azuredeploy.json fájl más beállítások.

![Forgatókönyv](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon letöltése és megismerése

A GitHubból letöltheti a meglévő Azure Resource Manager-sablont, amellyel létrehozhat egy virtuális hálózatot két alhálózattal, végrehajthatja a kívánt módosításokat, és újra felhasználhatja azt. Ehhez a következőket kell tennie:

1. Navigáljon a [Alkalmazásátjáró létrehozása webalkalmazási tűzfal engedélyezve van a](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf).
1. Kattintson az **azuredeploy.json**, majd a **RAW** elemre.
1. Mentse a fájlt egy helyi mappába a számítógépén.
1. Ha már ismeri az Azure Resource Manager-sablonokat, akkor ugorjon a 7. lépéshez.
1. Nyissa meg a mentett fájlt, és tekintse meg a tartalom **paraméterek** sorban
1. Az Azure Resource Manager-sablonparaméterek az üzembe helyezés során kitölthető paraméterek helyőrzőiként működnek.

  | Paraméter | Leírás |
  | --- | --- |
  | **subnetPrefix** |Az átjáró-alhálózat CIDR-blokkja. |
  | **applicationGatewaySize** | Az Alkalmazásátjáró mérete.  WAF csak akkor engedélyezett, közepes és nagy méretű. |
  | **backendIpaddress1** |Az első webalkalmazás-kiszolgáló IP-címe. |
  | **backendIpaddress2** |A második webalkalmazás-kiszolgáló IP-címe. |
  | **wafEnabled** | Határozza meg, ha engedélyezve van-e a WAF beállítást.|
  | **wafMode** | A webalkalmazási tűzfal módját.  Elérhető lehetőségek **megelőzési** vagy **észlelési**.|
  | **wafRuleSetType** | WAF szabálykészletben típusa.  OWASP jelenleg az egyetlen támogatott beállítás. |
  | **wafRuleSetVersion** |Szabálykészletben verziója. Program 2.2.9-es és 3.0 OWASP CRS opció jelenleg támogatott. |

1. Ellenőrizze a tartalmat a **erőforrások** , és figyelje meg a következő tulajdonságokkal:

   * **type**. A sablon által létrehozott erőforrástípus. Ebben az esetben a típus `Microsoft.Network/applicationGateways`, amely olyan átjárót jelöli.
   * **Név** Az erőforrás neve. Figyelje meg a `[parameters('applicationGatewayName')]`, ami azt jelenti, a név biztosított bemenetként, vagy egy paraméterfájl üzembe helyezése során.
   * **properties**. Az erőforrás tulajdonságainak listája. A sablon az Application Gateway létrehozása során a virtuális hálózatot és a nyilvános IP-címet használja.

1. Lépjen vissza [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf).
1. Kattintson a **azuredeploy-parameters.json**, és kattintson a **RAW**.
1. Mentse a fájlt egy helyi mappába a számítógépén.
1. Nyissa meg a mentett fájlt, és módosítsa a paraméterek értékeit. A következő értékek használatával helyezze üzembe a forgatókönyvünkben ismertetett Application Gateway-t.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "addressPrefix": {
            "value": "10.0.0.0/16"
            },
            "subnetPrefix": {
            "value": "10.0.0.0/28"
            },
            "applicationGatewaySize": {
            "value": "WAF_Medium"
            },
            "capacity": {
            "value": 2
            },
            "backendIpAddress1": {
            "value": "10.0.1.10"
            },
            "backendIpAddress2": {
            "value": "10.0.1.11"
            },
            "wafEnabled": {
            "value": true
            },
            "wafMode": {
            "value": "Detection"
            },
            "wafRuleSetType": {
            "value": "OWASP"
            },
            "wafRuleSetVersion": {
            "value": "3.0"
            }
        }
    }
    ```

1. Mentse a fájlt. A JSON-sablont és a paramétersablont online JSON érvényesítési eszközök, például a [JSlint.com](http://www.jslint.com/) segítségével tesztelheti.

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Az Azure Resource Manager-sablon üzembe helyezése a PowerShell használatával

Ha még sosem használta az Azure PowerShell, látogasson el: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) és az utasításokat követve jelentkezzen be Azure, és jelölje ki az előfizetését.

1. PowerShell-bejelentkezési

    ```powershell
    Login-AzureRmAccount
    ```

1. Keresse meg a fiókot az előfizetésekben.

    ```powershell
    Get-AzureRmSubscription
    ```

    A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.

1. Válassza ki, hogy melyik Azure előfizetést fogja használni.

    ```powershell
    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
    ```

1. Szükség esetén hozzon létre egy erőforráscsoportot a **New-AzureResourceGroup** parancsmaggal. A következő példában hozzon létre egy erőforráscsoportot AppgatewayRG nevű USA keleti régiója helyen.

    ```powershell
    New-AzureRmResourceGroup -Name AppgatewayRG -Location "West US"
    ```

1. Futtassa a **New-AzureRmResourceGroupDeployment** parancsmagot, hogy az előzőleg letöltött és módosított sablonnal és paraméterfájlokkal üzembe helyezhesse az új virtuális hálózatot.
    
    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Az Azure Resource Manager-sablon üzembe helyezése az Azure CLI használatával

Az Azure Resource Manager sablon Azure CLI használatával történő üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Az Azure CLI telepítése és konfigurálása](/cli/azure/install-azure-cli) című szakaszt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.

1. Szükség esetén a `az group create` parancs futtatásával hozzon létre egy erőforráscsoportot, a következő kódrészletben látható módon. Figyelje meg a parancs kimenetét. A kimenet után látható lista ismerteti a használt paramétereket. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).

    ```azurecli
    az group create --location westus --name appgatewayRG
    ```
    
    **-n (vagy --name)**. Az új erőforráscsoport neve. A mi esetünkben *appgatewayRG*.
    
    **-l (vagy --location)**. Az Azure-régió, ahol az új erőforráscsoport létrejön. A mi esetünkben rendelkezik *westus*.

1. Futtassa a `az group deployment create` parancsmag használatával történő telepítéséről az új virtuális hálózat sablonnal és paraméterfájlokkal fájlok letöltött és módosított az előző lépésben. A kimenet után látható lista ismerteti a használt paramétereket.

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Az Azure Resource Manager-sablon üzembe helyezése kattintással végrehajtható üzembe helyezéssel

Az Azure Resource Manager-sablonok használatának másik módja a kattintással végrehajtható üzembe helyezés. Ez egy egyszerű mód a sablonok Azure portállal történő használatára.

1. Ugrás a [hozzon létre egy alkalmazás webalkalmazási tűzfal](https://azure.microsoft.com/documentation/templates/101-application-gateway-waf/).

1. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.

    ![Üzembe helyezés az Azure-ban](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)
    
1. Töltse ki a központi telepítési sablon paramétereit a portálon, majd kattintson az **OK** gombra.

    ![Paraméterek](./media/application-gateway-create-gateway-arm-template/ibiza1.png)
    
1. Válassza ki **elfogadom a feltételeket és a fenti feltételek** kattintson **beszerzési**.

1. Az Egyéni üzembe helyezés panelen kattintson a **Létrehozás** gombra.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Tanúsítvány adatait a Resource Manager sablonokhoz szolgáltató

Ha SSL-t egy sablont használ, meg kell adni a feltöltendő helyett Base64 kódolású karakterláncnak kell a tanúsítványt. Alakítsa át a .pfx vagy Base64 kódolású karakterlánc .cer használja a következő parancsok egyikét. Az alábbi parancsokat a tanúsítvány Base64 kódolású karakterlánc, amely a sablonhoz megadható alakítsa át. A várt kimeneti karakterlánc, amely egy változó tárolja, és a sablon a beillesztett.

### <a name="macos"></a>macOS
```bash
cert=$( base64 <certificate path and name>.pfx )
echo $cert
```

### <a name="windows"></a>Windows
```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

Ez a cikk létrehozott összes erőforrást törli, végezze el az alábbi lépések egyikét:

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureRmResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>Következő lépések

Ha SSL-alapú kiszervezést szeretne konfigurálni, tekintse meg a következőt: [Application Gateway konfigurálása SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

