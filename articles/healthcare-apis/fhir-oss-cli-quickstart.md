---
title: 'Azure CLI: nyílt forráskódú FHIR-kiszolgáló üzembe helyezése az Azure-hoz – Azure API az Azure-hoz'
description: Ez a rövid útmutató ismerteti, hogyan helyezheti üzembe a nyílt forráskódú Microsoft FHIR-kiszolgálót az Azure-hoz.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10af71afd8843e75d5df3be57c909c56a7abca01
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843572"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Gyors útmutató: nyílt forráskódú FHIR-kiszolgáló üzembe helyezése az Azure CLI-vel

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy nyílt forráskódú FHIR &reg; -kiszolgálót az Azure-ban az Azure CLI használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Válassza ki az erőforráscsoport nevét, amely a kiépített erőforrásokat fogja tartalmazni, és hozza létre:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Sablon üzembe helyezése

Az Azure [GitHub-tárház](https://github.com/Microsoft/fhir-server) Microsoft FHIR-kiszolgálója olyan sablont tartalmaz, amely az összes szükséges erőforrást üzembe helyezi. Üzembe helyezés az alábbiakkal:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Ellenőrizze, hogy fut-e a FHIR-kiszolgáló

Szerezzen be egy képesség-utasítást a FHIR-kiszolgálóról a következővel:

```azurecli-interactive
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Egy percet vesz igénybe, hogy a kiszolgáló első alkalommal válaszoljon.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforráscsoportot az alábbi lépésekkel:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezte az Azure-hoz készült Microsoft nyílt forráskódú FHIR-kiszolgálót az előfizetésében. Ha szeretné megtudni, hogyan érheti el a FHIR API-t a Poster használatával, folytassa a Poster-oktatóanyaggal.
 
>[!div class="nextstepaction"]
>[FHIR API elérése a Poster használatával](access-fhir-postman-tutorial.md)
