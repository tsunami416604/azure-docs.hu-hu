---
title: Azure CLI-vel FHIR üzembe helyezése az Azure API
description: Az Azure CLI-vel FHIR üzembe helyezése az Azure API.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 880f9d67f87d32fbc03bc04877267b5b26022381
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823580"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Gyors útmutató: Azure CLI-vel FHIR üzembe helyezése az Azure API

Ebben a rövid útmutatóban üzembe helyezése az Azure API a FHIR az Azure-ban az Azure CLI használatával fog elsajátíthatja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása

Hozzon létre egy Azure Resource Manager-sablon a következő tartalommal:

[!code-json[](samples/azuredeploy.json)]

Mentse a neve `azuredeploy.json`

## <a name="create-azure-resource-manager-parameter-file"></a>Azure Resource Manager-paraméterfájl létrehozása

Hozzon létre egy Azure Resource Manager sablon alkalmazásparaméter-fájlt az alábbi tartalommal:

[!code-json[](samples/azuredeploy.parameters.json)]

Mentse a neve `azuredeploy.parameters.json`

Az objektum azonosítója értékek `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` és `yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy` felelnek meg az egyes Azure Active Directory felhasználói azonosítók objektumot, vagy a könyvtárban, az előfizetéshez tartozó egyszerű szolgáltatások. Ha szeretné tudni, hogy egy adott felhasználó Objektumazonosítója, megtalálhatja a hasonló parancsot:

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

Olvassa el az útmutató a [keresése identitás objektumazonosítójának](find-identity-object-ids.md) további részletekért.

# <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

Adjon meg egy nevet az erőforráscsoport, amely tartalmazni fogja az Azure API-t a FHIR hozza létre:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir-account"></a>Az Azure API FHIR-fiók üzembe helyezése

A sablon használatához (`azuredeploy.json`) és a sablon alkalmazásparaméter-fájlt (`azuredeploy.parameters.json`) való üzembe helyezése az Azure API FHIR:

```azurecli-interactive
az group deployment create -g "myResourceGroup" --template-file azuredeploy.json --parameters @{azuredeploy.parameters.json}
```

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API képesség utasítás beolvasása

Szerezze be az FHIR API-t egy képesség utasítást:

```azurecli-interactive
curl --url "https://nameOfFhirAccount.azurehealthcareapis.com/fhir/metadata"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatához, törölje az erőforráscsoportot, az alábbi lépéseket követve:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure API helyezte a FHIR az előfizetésben helyezi. Megtudhatja, hogyan érheti el az FHIR API Postman használatával, folytassa a Postman-oktatóanyaggal.

>[!div class="nextstepaction"]
>[Hozzáférés FHIR API Postman használatával](access-fhir-postman-tutorial.md)