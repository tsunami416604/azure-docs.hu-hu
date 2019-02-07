---
title: PowerShell-lel FHIR üzembe helyezése az Azure API
description: A PowerShell-lel FHIR üzembe helyezése az Azure API.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 0cea06da1815fa1128b16f1427690141823e82d8
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823512"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>Gyors útmutató: PowerShell-lel FHIR üzembe helyezése az Azure API

Ebben a rövid útmutatóban üzembe helyezése az Azure API-hoz a PowerShell-lel FHIR is elsajátíthatja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Az Azure API FHIR erőforrás-szolgáltató regisztrálása

Ha a `Microsoft.HealthcareApis` erőforrás-szolgáltató még nincs regisztrálva az előfizetéshez tartozó, az regisztrálhatja:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása

Hozzon létre egy Azure Resource Manager-sablon a következő tartalommal:

[!code-json[](samples/azuredeploy.json)]

Mentse a neve `azuredeploy.json`

## <a name="create-azure-resource-manager-parameter-file"></a>Azure Resource Manager-paraméterfájl létrehozása

Hozzon létre egy Azure Resource Manager sablon alkalmazásparaméter-fájlt az alábbi tartalommal:

[!code-json[](samples/azuredeploy.parameters.json)]

Mentse a neve `azuredeploy.parameters.json`

Az objektum azonosítója értékek `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` és `yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy` felelnek meg az egyes Azure Active Directory felhasználói azonosítók objektumot, vagy a könyvtárban, az előfizetéshez tartozó egyszerű szolgáltatások. Ha szeretné tudni, hogy egy adott felhasználó Objektumazonosítója, megtalálhatja a hasonló parancsot:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

Olvassa el az útmutató a [keresése identitás objektumazonosítójának](find-identity-object-ids.md) további részletekért.

## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

```azurepowershell-interactive
$rg = New-AzureRmResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-template"></a>Sablon üzembe helyezése

```azurepowershell-interactive
New-AzureRmResourceGroupDeployment -ResourceGroup $rg.ResourceGroupName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

## <a name="fetch-capability-statement"></a>Képesség utasítás beolvasása

Láthatja az Azure API FHIR fiók által beolvasása egy FHIR képesség utasítás futásának ellenőrzéséhez:

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri $metadataUrl "https://nameOfFhirAccount.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatához, törölje az erőforráscsoportot, az alábbi lépéseket követve:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure API helyezte a FHIR az előfizetésben helyezi. Megtudhatja, hogyan érheti el az FHIR API Postman használatával, folytassa a Postman-oktatóanyaggal.

>[!div class="nextstepaction"]
>[Hozzáférés FHIR API Postman használatával](access-fhir-postman-tutorial.md)