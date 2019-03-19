---
title: Az Azure Application Gateway - sablonok létrehozása |} A Microsoft Docs
description: Ez az oldal utasításokat tartalmaz egy Azure Application Gateway Azure Resource Manager-sablonnal történő létrehozásához
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: victorh
ms.openlocfilehash: 4cfe8b02697fe8234c29995a611cb99a89e2e54b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080981"
---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Application Gateway létrehozása az Azure Resource Manager-sablonokkal

Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen találhatóak. Az Application Gateway számos alkalmazáskézbesítési vezérlőszolgáltatást (ADC) biztosít, beleértve a HTTP-terheléselosztást, a cookie-alapú munkamenet-affinitást, a Secure Sockets Layer- (SSL-) alapú kiszervezést, az egyéni állapotmintákat, a többhelyes támogatást és még sok mást. Keresse meg a támogatott szolgáltatások teljes listáját, keresse fel [Application Gateway áttekintése](overview.md)

Ebben a cikkben bemutatjuk, hogyan letöltése és a egy meglévő módosítása [Azure Resource Manager-sablon](../azure-resource-manager/resource-group-authoring-templates.md) a Githubról, majd a sablont a Githubból, Powershellből és az Azure CLI telepítése.

Ha egyszerűen üzembe a sablont, közvetlenül a Githubból módosítása nélkül, ugorjon a sablont a GitHub.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben az alábbiakat fogja tenni:

* Hozzon létre egy application gateway webalkalmazási tűzfal.
* Létrehoz egy VirtualNetwork1 nevű virtuális hálózatot a 10.0.0.0/16 egy fenntartott CIDR-blokkjával.
* Létrehoz egy Appgatewaysubnet nevű alhálózatot, amelynek a CIDR-blokkja 10.0.0.0/28 lesz.
* Beállít két korábban konfigurált háttér IP-címet a webkiszolgálóknak, amelyek között el szeretné osztani a forgalom terhelését. Ebben a példasablonban a háttér IP-cím a 10.0.1.10 és a 10.0.1.11.

> [!NOTE]
> Ezek a beállítások a sablon paraméterei. A sablon testreszabásához módosíthatja a szabályokat, a figyelőt, SSL és egyéb lehetőségek az azuredeploy.json fájlban.

![Forgatókönyv](./media/create-vmss-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon letöltése és megismerése

A GitHubból letöltheti a meglévő Azure Resource Manager-sablont, amellyel létrehozhat egy virtuális hálózatot két alhálózattal, végrehajthatja a kívánt módosításokat, és újra felhasználhatja azt. Ehhez a következőket kell tennie:

1. Navigáljon a [engedélyezett webalkalmazási tűzfallal rendelkező Application Gateway létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf).
1. Kattintson az **azuredeploy.json**, majd a **RAW** elemre.
1. Mentse a fájlt egy helyi mappába a számítógépén.
1. Ha már ismeri az Azure Resource Manager-sablonokat, akkor ugorjon a 7. lépéshez.
1. Nyissa meg a mentett fájlt, és tekintse meg a tartalom alatt **paraméterek** sorban
1. Az Azure Resource Manager-sablonparaméterek az üzembe helyezés során kitölthető paraméterek helyőrzőiként működnek.

   | Paraméter | Leírás |
   | --- | --- |
   | **subnetPrefix** |Az application gateway alhálózatának CIDR-blokkja. |
   | **applicationGatewaySize** | Az application gateway méretét.  WAF csak közepes és nagy méretű teszi lehetővé. |
   | **backendIpaddress1** |Az első webkiszolgáló IP-címe. |
   | **backendIpaddress2** |A második webkiszolgáló IP-címe. |
   | **wafEnabled** | Beállítás határozza meg, ha engedélyezve van-e a WAF.|
   | **wafMode** | A webalkalmazási tűzfal mód.  Elérhető lehetőségek **megelőzési** vagy **észlelési**.|
   | **wafRuleSetType** | WAF szabálykészletben típusa.  OWASP jelenleg az egyetlen támogatott lehetőség. |
   | **wafRuleSetVersion** |Szabálykészletben verziója. OWASP CRS 2.2.9 és 3.0 jelenleg a támogatott kapcsolókat. |

1. Ellenőrizze a tartalom alatt **erőforrások** , és figyelje meg a következő tulajdonságokkal:

   * **type**. A sablon által létrehozott erőforrástípus. Ebben az esetben a típus `Microsoft.Network/applicationGateways`, amely egy application gateway jelöli.
   * **Név** Az erőforrás neve. Figyelje meg a `[parameters('applicationGatewayName')]`, mely azt jelenti, hogy a név bemenetként, vagy egy paraméterfájl üzembe helyezés során.
   * **properties**. Az erőforrás tulajdonságainak listája. A sablon az Application Gateway létrehozása során a virtuális hálózatot és a nyilvános IP-címet használja. A JSON-szintaxist és egy sablon az application gateway tulajdonságainak: [Microsoft.Network/applicationGateways](/azure/templates/microsoft.network/applicationgateways).

1. Lépjen vissza a [ https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf/ ](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf).
1. Kattintson a **azuredeploy-parameters.json**, és kattintson a **RAW**.
1. Mentse a fájlt egy helyi mappába a számítógépén.
1. Nyissa meg a mentett fájlt, és módosítsa a paraméterek értékeit. A következő értékek használatával helyezze üzembe a forgatókönyvünkben ismertetett Application Gateway-t.

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

1. Mentse a fájlt. A JSON-sablont és a paramétersablont online JSON érvényesítési eszközök, például a [JSlint.com](https://www.jslint.com/) segítségével tesztelheti.

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Az Azure Resource Manager-sablon üzembe helyezése a PowerShell használatával

Ha még nem használta az Azure Powershellt, tekintse meg: [Azure PowerShell telepítése és konfigurálása annak](/powershell/azure/overview) és kövesse az utasításokat az Azure-ba való bejelentkezéshez, és válassza ki az előfizetését.

1. Bejelentkezés a Powershellbe

    ```powershell
    Login-AzAccount
    ```

1. Keresse meg a fiókot az előfizetésekben.

    ```powershell
    Get-AzSubscription
    ```

    A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.

1. Válassza ki, hogy melyik Azure előfizetést fogja használni.

    ```powershell
    Select-AzSubscription -Subscriptionid "GUID of subscription"
    ```

1. Szükség esetén hozzon létre egy erőforráscsoportot a **New-AzureResourceGroup** parancsmaggal. A következő példában egy az USA keleti régiójában AppgatewayRG nevű erőforráscsoportot hoz létre.

    ```powershell
    New-AzResourceGroup -Name AppgatewayRG -Location "West US"
    ```

1. Futtassa a **New-AzResourceGroupDeployment** parancsmagot, hogy az új virtuális hálózat az előző sablonnal és paraméterfájlokkal üzembe helyezhesse fájljainak letöltött és módosított.
    
    ```powershell
    New-AzResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Az Azure Resource Manager-sablon üzembe helyezése az Azure CLI használatával

Az Azure parancssori felület használatával a letöltött Azure Resource Manager-sablon üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Az Azure CLI telepítése és konfigurálása](/cli/azure/install-azure-cli) című szakaszt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.

1. Szükség esetén a `az group create` paranccsal hozzon létre egy erőforráscsoportot, az alábbi kódrészletben látható módon. Figyelje meg a parancs kimenetét. A kimenet után látható lista ismerteti a használt paramétereket. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).

    ```azurecli
    az group create --location westus --name appgatewayRG
    ```
    
    **-n (vagy --name)**. Az új erőforráscsoport neve. A mi esetünkben *appgatewayRG*.
    
    **-l (vagy --location)**. Az Azure-régió, ahol az új erőforráscsoport létrejön. Esetünkben az rendelkezik *westus*.

1. Futtassa a `az group deployment create` parancsmagot, hogy az új virtuális hálózat sablonnal és paraméterfájlokkal üzembe helyezhesse fájljainak letöltött és módosított az előző lépésben. A kimenet után látható lista ismerteti a használt paramétereket.

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Az Azure Resource Manager-sablon üzembe helyezése kattintással végrehajtható üzembe helyezéssel

Az Azure Resource Manager-sablonok használatának másik módja a kattintással végrehajtható üzembe helyezés. Ez egy egyszerű mód a sablonok Azure portállal történő használatára.

1. Lépjen a [webalkalmazási tűzfallal rendelkező application gateway létrehozása](https://azure.microsoft.com/documentation/templates/101-application-gateway-waf/).

1. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.

    ![Üzembe helyezés az Azure-ban](./media/create-vmss-template/deploytoazure.png)
    
1. Töltse ki a központi telepítési sablon paramétereit a portálon, majd kattintson az **OK** gombra.

    ![Paraméterek](./media/create-vmss-template/ibiza1.png)
    
1. Válassza ki **elfogadom a feltételeket és a fenti feltételeket** kattintson **beszerzési**.

1. Az Egyéni üzembe helyezés panelen kattintson a **Létrehozás** gombra.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Tanúsítvány adatokat szolgáltat a Resource Manager-sablonok

Amikor az SSL-sablonnal a tanúsítványt kell adni egy base64-karakterlánc helyett feltöltése folyamatban. Konvertálja a .pfx- vagy .cer Base64 kódolású karakterlánc használja a következő parancsok egyikét. A következő parancsokat egy base64-karakterlánc, amely a sablonhoz adható meg, alakítsa át a tanúsítványt. A várható kimenete egy karakterlánc, amely egy változó tárolja, és a sablonban beillesztett.

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

Ebben a cikkben létrehozott összes erőforrás törléséhez hajtsa végre az alábbi lépések egyikét:

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>További lépések

Ha azt szeretné, SSL-alapú kiszervezés konfigurálása, látogassa meg: [Konfigurálja az application gateway SSL-alapú kiszervezéshez](tutorial-ssl-cli.md).

Ha szeretne egy belső terheléselosztó használata application gateway konfigurálása, látogassa meg: [Application gateway létrehozása belső terheléselosztóval (ILB)](redirect-internal-site-cli.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

