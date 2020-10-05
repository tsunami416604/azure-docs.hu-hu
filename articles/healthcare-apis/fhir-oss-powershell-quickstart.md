---
title: 'PowerShell: FHIR-kiszolgáló üzembe helyezése az Azure-ban – Azure API a FHIR-hez'
description: Ez a rövid útmutató azt ismerteti, hogyan helyezhető üzembe a Microsoft nyílt forráskódú FHIR-kiszolgáló a PowerShell használatával.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c133a309cc85ffcfb69be2ae7bbb614cbb540f2e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87847126"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Gyors útmutató: nyílt forráskódú FHIR-kiszolgáló üzembe helyezése a PowerShell használatával

Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe a nyílt forráskódú Microsoft FHIR-kiszolgálót az Azure-hoz a PowerShell használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Válassza ki az erőforráscsoport nevét, amely a kiépített erőforrásokat fogja tartalmazni, és hozza létre:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>A FHIR-kiszolgáló sablonjának üzembe helyezése

Az Azure [GitHub-tárház](https://github.com/Microsoft/fhir-server) Microsoft FHIR-kiszolgálója olyan sablont tartalmaz, amely az összes szükséges erőforrást üzembe helyezi. Üzembe helyezés az alábbiakkal:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Ellenőrizze, hogy fut-e a FHIR-kiszolgáló

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Egy percet vesz igénybe, hogy a kiszolgáló első alkalommal válaszoljon.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforráscsoportot az alábbi lépésekkel:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezte az Azure-hoz készült Microsoft nyílt forráskódú FHIR-kiszolgálót az előfizetésében. Ha szeretné megtudni, hogyan érheti el a FHIR API-t a Poster használatával, folytassa a Poster-oktatóanyaggal.
 
>[!div class="nextstepaction"]
>[FHIR API elérése a Poster használatával](access-fhir-postman-tutorial.md)
