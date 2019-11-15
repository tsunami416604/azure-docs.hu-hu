---
title: Rövid útmutató – profil és végpont létrehozása Resource Manager-sablonok használatával
titleSuffix: Azure Content Delivery Network
description: Ismerje meg, hogyan hozhat létre Azure-tartalmat hálózati profil és végpont Resource Manager-sablonok használatával
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
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: b711a12161bc134bdcbb8c1f3e74f2e5ae06e701
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083135"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Gyors útmutató: Azure CDN profil és végpont létrehozása Resource Manager-sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablont helyez üzembe a parancssori felület használatával. A létrehozott sablon egy CDN-profilt és egy CDN-végpontot helyez üzembe a webalkalmazás előtt.
A lépések végrehajtása körülbelül tíz percet vesz igénybe.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Információt

Ebben a rövid útmutatóban a forrásként használandó webalkalmazást kell használnia. Az ebben a rövid útmutatóban használt példa webalkalmazás üzembe helyezése https://cdndemo.azurewebsites.net

További információ: [statikus HTML-Webalkalmazás létrehozása az Azure-ban](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az összes erőforrást ugyanabban az erőforráscsoporthoz kell telepíteni.

Hozza létre az erőforráscsoportot a kiválasztott helyen. Ez a példa egy CDN nevű erőforráscsoport létrehozását mutatja be az USA keleti régiójában.

```bash
az group create --name cdn --location eastus
```

![Új erőforráscsoport](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

Ebben a lépésben létrehoz egy sablonfájlt, amely telepíti az erőforrásokat.

Habár ez a példa egy általános webhely-gyorsítási forgatókönyvet mutat be, számos más beállítás is konfigurálható. Ezek a beállítások a Azure Resource Manager sablon hivatkozásában érhetők el. Tekintse meg a [CDN-profil](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) és a [CDN-profil végpontjának](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)hivatkozásait.

Vegye figyelembe, hogy a Microsoft CDN nem támogatja a tartalomtípus-lista módosítását.

Mentse a sablont **Resource-Manager-CDN. JSON**néven.

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

## <a name="create-the-resources"></a>Erőforrások létrehozása

A sablon üzembe helyezése az Azure CLI használatával. A rendszer a következő két bemenetet fogja kérni:

**cdnProfileSku** – a használni kívánt CDN-szolgáltató. A következő lehetőségek közül választhat:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** – az a végpont, amely a CDN-en keresztül fog kiszolgálni, például cdndemo.azurewebsites.net.

```bash
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Resource Manager-sablon üzembe helyezése](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>A CDN-Profil megtekintése

```bash
az cdn profile list --resource-group cdn -o table
```

![CDN-Profil megtekintése](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>A profil szabványának CDN-végpontjának megtekintése – Microsoft

```bash
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![CDN-végpont megtekintése](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Az állomásnév használatával megtekintheti a tartalmat. Például a böngésző használatával férhet hozzá https://cdndemo-azurewebsites-net.azureedge.nethoz.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Az erőforráscsoport törlése automatikusan eltávolítja az abban telepített összes erőforrást.

```bash
az group delete --name cdn
```

![Erőforráscsoport törlése](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Referencia

* CDN-profil – [Azure Resource Manager sablon referenciája](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* CDN-végpont – [Azure Resource Manager sablon-referenciák dokumentációja](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Következő lépések

Az alábbi útmutatóból megtudhatja, hogyan adhat hozzá egyéni tartományt CDN-végpontjához:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni tartomány hozzáadása az Azure CDN-végponthoz](cdn-map-content-to-custom-domain.md)
