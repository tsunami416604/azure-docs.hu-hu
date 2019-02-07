---
title: Nyílt forráskódú FHIR kiszolgáló központi telepítése az Azure PowerShell - Microsoft Healthcare API-k használatával
description: Ez a rövid útmutató bemutatja, hogyan telepíti a PowerShell használatával a Microsoft nyílt forráskódú FHIR-kiszolgálót.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 6118c9371df7aa0af685390dae28bceb496145d4
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823507"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Gyors útmutató: Telepítse a nyílt forráskódú FHIR server PowerShell-lel

Ebben a rövid útmutatóban fog elsajátíthatja a nyílt forráskódú Microsoft FHIR-kiszolgáló telepítése az Azure PowerShell használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Adjon meg egy nevet az erőforráscsoport, amely tartalmazni fogja a kiépített erőforrások létrehozásához:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzureRmResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>A kiszolgáló FHIR-sablon üzembe helyezése

Az Azure-hoz a Microsoft FHIR kiszolgáló [GitHub-adattár](https://github.com/Microsoft/fhir-server) egy sablont, amely telepíti az összes szükséges erőforrást tartalmaz. Helyezze üzembe azt:

```azurepowershell-interactive
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName `
```

## <a name="verify-fhir-server-is-running"></a>FHIR kiszolgáló fut-e

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Egy percet vesz a kiszolgáló válaszára először vesz igénybe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatához, törölje az erőforráscsoportot, az alábbi lépéseket követve:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Microsoft Open FHIR forráskiszolgálón Azure helyezte az előfizetésben helyezi. Megtudhatja, hogyan érheti el az FHIR API Postman használatával, folytassa a Postman-oktatóanyaggal.
 
>[!div class="nextstepaction"]
>[Hozzáférés FHIR API Postman használatával](access-fhir-postman-tutorial.md)