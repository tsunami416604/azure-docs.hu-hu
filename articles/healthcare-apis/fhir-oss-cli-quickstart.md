---
title: Nyílt forráskódú FHIR kiszolgáló központi telepítése az Azure CLI használatával
description: Ez a rövid útmutató azt ismerteti, hogyan az Azure a nyílt forráskódú Microsoft FHIR kiszolgáló üzembe helyezése.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 6bb2806aa1ba830f1ef3215a61db4d40f531bdd8
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823492"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Gyors útmutató: Azure CLI-vel nyílt forráskódú FHIR-kiszolgáló központi telepítése

Ebből a gyorsútmutatóból megtudhatja, hogyan üzembe helyezéséhez egy nyílt forráskódú FHIR&reg; server az Azure-ban az Azure CLI használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Adjon meg egy nevet az erőforráscsoport, amely tartalmazni fogja a kiépített erőforrások létrehozásához:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Sablon üzembe helyezése

Az Azure-hoz a Microsoft FHIR kiszolgáló [GitHub-adattár](https://github.com/Microsoft/fhir-server) egy sablont, amely telepíti az összes szükséges erőforrást tartalmaz. Helyezze üzembe azt:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>FHIR kiszolgáló fut-e

Szerezze be az FHIR-kiszolgáló egy képesség utasítást:

```azurecli-interactive
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Egy percet vesz a kiszolgáló válaszára először vesz igénybe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatához, törölje az erőforráscsoportot, az alábbi lépéseket követve:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Microsoft Open FHIR forráskiszolgálón Azure helyezte az előfizetésben helyezi. Megtudhatja, hogyan érheti el az FHIR API Postman használatával, folytassa a Postman-oktatóanyaggal.
 
>[!div class="nextstepaction"]
>[Hozzáférés FHIR API Postman használatával](access-fhir-postman-tutorial.md)