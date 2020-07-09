---
title: 'Azure CLI: nyílt forráskódú FHIR-kiszolgáló üzembe helyezése az Azure-hoz – Azure API az Azure-hoz'
description: Ez a rövid útmutató ismerteti, hogyan helyezheti üzembe a nyílt forráskódú Microsoft FHIR-kiszolgálót az Azure-hoz.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f8047ebeb8e47f609db79e3ac1235b5cd65a4fd4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "84820227"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Gyors útmutató: nyílt forráskódú FHIR-kiszolgáló üzembe helyezése az Azure CLI-vel

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy nyílt forráskódú FHIR &reg; -kiszolgálót az Azure-ban az Azure CLI használatával.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

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

```console
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Egy percet vesz igénybe, hogy a kiszolgáló első alkalommal válaszoljon.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforráscsoportot az alábbi lépésekkel:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezte az Azure-hoz készült Microsoft nyílt forráskódú FHIR-kiszolgálót az előfizetésében. Ha szeretné megtudni, hogyan érheti el a FHIR API-t a Poster használatával, folytassa a Poster-oktatóanyaggal.
 
>[!div class="nextstepaction"]
>[FHIR API elérése a Poster használatával](access-fhir-postman-tutorial.md)