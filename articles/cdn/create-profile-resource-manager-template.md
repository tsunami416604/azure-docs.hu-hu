---
title: Rövid útmutató – Profil és végpont létrehozása Erőforrás-kezelői sablonokkal
titleSuffix: Azure Content Delivery Network
description: Megtudhatja, hogyan hozhat létre Azure Content Deliver Network-profilt és végpontot az Erőforrás-kezelő sablonjai használatával
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: dfd7b933502b96c0952a24dbee563e9b537dcdd8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683476"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Rövid útmutató: Hozzon létre egy Azure CDN-profilt és végpontot az Erőforrás-kezelő sablonnal

Ebben a rövid útmutatóban üzembe helyezhet egy Azure Resource Manager-sablont a CLI használatával. A létrehozott sablon cdn-profilt és CDN-végpontot telepít a webalkalmazás elé.
A lépések végrehajtásához körülbelül tíz percet vesz igénybe.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Előhelyek

Ennek a rövid útmutatónak az alkalmazásában rendelkeznie kell egy webalkalmazással, amelyet Originként használhat. A rövid útmutatóban használt példa webalkalmazást ahttps://cdndemo.azurewebsites.net

További információt a [Statikus HTML-webalkalmazás létrehozása az Azure-ban című témakörben talál.](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Minden erőforrást ugyanabban az erőforráscsoportban kell telepíteni.

Hozza létre az erőforráscsoportot a kiválasztott helyen. Ez a példa egy cdn nevű erőforráscsoport létrehozását mutatja be az USA keleti részén.

```azurecli-interactive
az group create --name cdn --location eastus
```

![Új erőforráscsoport](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

Ebben a lépésben hozzon létre egy sablonfájlt, amely telepíti az erőforrásokat.

Bár ez a példa végigvezeti az általános webhelygyorsítási forgatókönyvet, számos más beállítás konfigurálható. Ezek a beállítások az Azure Resource Manager sablonhivatkozásban érhetők el. Olvassa el a [CDN-profil](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) és a [CDN-profilvégpont hivatkozásait.](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

Ne feledje, hogy a Microsoft CDN nem támogatja a tartalomtípus-lista módosítását.

Mentse a sablont **erőforrás-kezelő-cdn.json néven.**

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "cdnProfileSku": {
            "type": "string",
            "allowedValues": [
                "Standard_Microsoft",
                "Standard_Akamai",
                "Standard_Verizon",
                "Premium_Verizon"
            ]
        },
        "endpointOriginHostName": {
            "type": "string"
        }
    },
    "variables": {
        "profile": {
            "name": "[replace(toLower(parameters('cdnProfileSku')), '_', '-')]"
        },
        "endpoint": {
            "name": "[replace(toLower(parameters('endpointOriginHostName')), '.', '-')]",
            "originHostName": "[parameters('endpointOriginHostName')]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Cdn/profiles",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[variables('profile').name]",
            "sku": {
                "name": "[parameters('cdnProfileSku')]"
            }
        },
        {
            "dependsOn": [
                "[resourceId('Microsoft.Cdn/profiles', variables('profile').name)]"
            ],
            "type": "Microsoft.Cdn/profiles/endpoints",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[concat(variables('profile').name, '/', variables('endpoint').name)]",
            "properties": {
                "hostName": "[concat(variables('endpoint').name, '.azureedge.net')]",
                "originHostHeader": "[variables('endpoint').originHostName]",
                "isHttpAllowed": true,
                "isHttpsAllowed": true,
                "queryStringCachingBehavior": "IgnoreQueryString",
                "origins": [
                    {
                        "name": "[replace(variables('endpoint').originHostName, '.', '-')]",
                        "properties": {
                            "hostName": "[variables('endpoint').originHostName]",
                            "httpPort": 80,
                            "httpsPort": 443
                        }
                    }
                ],
                "contentTypesToCompress": [
                    "application/eot",
                    "application/font",
                    "application/font-sfnt",
                    "application/javascript",
                    "application/json",
                    "application/opentype",
                    "application/otf",
                    "application/pkcs7-mime",
                    "application/truetype",
                    "application/ttf",
                    "application/vnd.ms-fontobject",
                    "application/xhtml+xml",
                    "application/xml",
                    "application/xml+rss",
                    "application/x-font-opentype",
                    "application/x-font-truetype",
                    "application/x-font-ttf",
                    "application/x-httpd-cgi",
                    "application/x-javascript",
                    "application/x-mpegurl",
                    "application/x-opentype",
                    "application/x-otf",
                    "application/x-perl",
                    "application/x-ttf",
                    "font/eot",
                    "font/ttf",
                    "font/otf",
                    "font/opentype",
                    "image/svg+xml",
                    "text/css",
                    "text/csv",
                    "text/html",
                    "text/javascript",
                    "text/js",
                    "text/plain",
                    "text/richtext",
                    "text/tab-separated-values",
                    "text/xml",
                    "text/x-script",
                    "text/x-component",
                    "text/x-java-source"
                ],
                "isCompressionEnabled": true,
                "optimizationType": "GeneralWebDelivery"
            }
        }
    ],
    "outputs": {
        "cdnUrl": {
            "type": "string",
            "value": "[concat('https://', variables('endpoint').name, '.azureedge.net')]"
        }
    }
}
```

## <a name="create-the-resources"></a>Az erőforrások létrehozása

Telepítse a sablont az Azure CLI használatával. A rendszer 2 bemenetet kér:

**cdnProfileSku** - a használni kívánt CDN-szolgáltató. A következő lehetőségek közül választhat:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** - a CDN-en keresztül kiszolgált végpont, például cdndemo.azurewebsites.net.

```azurecli-interactive
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Erőforrás-kezelő sablon telepítése](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>A CDN-profil megtekintése

```azurecli-interactive
az cdn profile list --resource-group cdn -o table
```

![CDN-profil megtekintése](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>A standard-microsoft profil CDN-végpontjának megtekintése

```azurecli-interactive
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![CDN-végpont megtekintése](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

A tartalom megtekintéséhez használja a HostName nevet. Például a https:\//cdndemo-azurewebsites-net.azureedge.net a böngészőhasználatával.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Az erőforráscsoport törlése automatikusan eltávolítja az összes üzembe helyezett erőforrást.

```azurecli-interactive
az group delete --name cdn
```

![Erőforráscsoport törlése](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Referencia

* CDN-profil – [Azure Resource Manager-sablon – hivatkozás](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* CDN-végpont – [Azure Resource Manager-sablon referenciadokumentációja](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>További lépések

Az alábbi útmutatóból megtudhatja, hogyan adhat hozzá egyéni tartományt CDN-végpontjához:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni tartomány hozzáadása az Azure CDN-végponthoz](cdn-map-content-to-custom-domain.md)
