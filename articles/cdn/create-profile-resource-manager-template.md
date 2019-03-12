---
title: Rövid útmutató – Azure Content Delivery Network-profil és -végpont használatával a Resource Manager-sablonok létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az Azure Content Delivery Network-profil és -végpont használatával a Resource Manager-sablonok
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5c393916bc977f4e0bc51913bdb2dfbbd6677c97
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727539"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Gyors útmutató: Azure CDN-profil és -végpont használatával a Resource Manager-sablon létrehozása

Ebből a gyors üzembe helyezése egy Azure Resource Manager-sablonok parancssori felület használatával. A sablon létrehoz egy CDN-profil és a CDN-végpontot, a webalkalmazás első helyez üzembe.
Ezek a lépések végrehajtása nagyjából tíz percet kell vennie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Byok

Ebben a rövid útmutatóban egy webes alkalmazást használja, mint a forrás kell rendelkeznie. A példában ebben a rövid útmutatóban használt webes alkalmazás üzembe lett helyezve https://cdndemo.azurewebsites.net

További információkért lásd: [egy statikus HTML-webalkalmazás létrehozása az Azure-ban](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Ugyanabban az erőforráscsoportban lévő összes erőforrást kell telepíteni.

Az erőforráscsoport létrehozása a kiválasztott helyen. Ez a példa bemutatja egy erőforráscsoportot az USA keleti régiójában cdn nevű.

```bash
az group create --name cdn --location eastus
```

![Új erőforráscsoport](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>A Resource Manager-sablon létrehozása

Ebben a lépésben hozzon létre egy sablon fájlt, amely üzembe helyezi az erőforrásokat.

Ebben a példában egy általános webhely-gyorsítás forgatókönyv bemutatja, amíg nincsenek számos egyéb beállítást is konfigurálható. Ezek a beállítások az Azure Resource Manager sablonreferenciája érhető el. Tekintse át a hivatkozások [CDN-profil](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles) és [profil CDN-végpont](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Vegye figyelembe, hogy a Microsoft CDN nem támogatja a módosítása a tartalomtípusok listája.

A sablon mentése másként **erőforrás-kezelő – cdn.json**.

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

A sablon Azure CLI-vel üzembe helyezéséhez. 2 bemenet kell megadnia:

**cdnProfileSku** – a CDN-szolgáltató, amelyet használni szeretne. A következő lehetőségek közül választhat:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** -szolgáltató segítségével a CDN-t, például cdndemo.azurewebsites.net végpont.

```bash
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Resource Manager-sablon üzembe helyezése](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>A CDN-profil megtekintése

```bash
az cdn profile list --resource-group cdn -o table
```

![A CDN-profil megtekintése](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Megtekintheti a CDN-végpont a profil standard – Microsoft

```bash
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![CDN-végpont megtekintése](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

A tartalom megtekintéséhez használja az állomásnevet. Például https://cdndemo-azurewebsites-net.azureedge.net a böngészővel.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Az erőforráscsoport törlése automatikusan eltávolítja az összes üzembe helyezett erőforrások benne.

```bash
az group delete --name cdn
```

![Erőforráscsoport törlése](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Referencia

* CDN-profil - [az Azure Resource Manager Sablonreferenciája](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles)
* CDN-végpont - [az Azure Resource Manager Sablonreferenciája dokumentációja](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>További lépések

Az alábbi útmutatóból megtudhatja, hogyan adhat hozzá egyéni tartományt CDN-végpontjához:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni tartomány hozzáadása az Azure CDN-végponthoz](cdn-map-content-to-custom-domain.md)
