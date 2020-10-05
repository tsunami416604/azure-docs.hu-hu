---
title: 'Gyors útmutató: Azure API üzembe helyezése FHIR az Azure CLI használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe Azure API-t az Azure FHIR az Azure CLI használatával.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: e1aa3362217a4edcfcf547c69b9effde00459f7e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87850559"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-cli"></a>Gyors útmutató: Azure API üzembe helyezése FHIR az Azure CLI használatával

Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe Azure API-t az Azure FHIR az Azure CLI használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-healthcareapis-extension"></a>HealthcareAPIs-bővítmény hozzáadása

```azurecli-interactive
az extension add --name healthcareapis
```

HealthcareAPIs-parancsok listájának lekérése:

```azurecli-interactive
az healthcareapis --help
```

## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

Válassza ki az erőforráscsoport nevét, amely a FHIR készült Azure API-t fogja tartalmazni, majd hozza létre:

```azurecli-interactive
az group create --name "myResourceGroup" --location westus2
```

## <a name="deploy-the-azure-api-for-fhir"></a>A FHIR készült Azure API üzembe helyezése

```azurecli-interactive
az healthcareapis create --resource-group myResourceGroup --name nameoffhiraccount --kind fhir-r4 --location westus2 
```

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API-képesség utasításának beolvasása

Szerezzen be egy képesség-utasítást a FHIR API-val a következővel:

```azurecli-interactive
curl --url "https://nameoffhiraccount.azurehealthcareapis.com/metadata"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforráscsoportot az alábbi lépésekkel:

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezte a FHIR készült Azure API-t az előfizetésében. Ha további beállításokat szeretne megadni a FHIR készült Azure API-ban, folytassa a további beállítások útmutatóval.

>[!div class="nextstepaction"]
>[További beállítások a FHIR készült Azure API-ban](azure-api-for-fhir-additional-settings.md)
