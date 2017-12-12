---
title: "Hivatkozás a sablonokat az Azure-telepítés |} Microsoft Docs"
description: "Ismerteti az Azure Resource Manager sablon kapcsolt sablonok segítségével moduláris sablon megoldás létrehozása. Bemutatja, hogyan továbbítsa a paraméterértéket, adja meg a paraméter fájlt, és dinamikusan létrehozott URL-címeket."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: tomfitz
ms.openlocfilehash: 7d6e8f123dd04b98df10b5a941396d7140bcc023
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Azure-erőforrások telepítésekor kapcsolt sablonok használata

A megoldás üzembe helyezéséhez, használhatja ugyanazt a sablont, vagy egy fő sablont több csatolt sablonokkal. A kis-és közepes méretű megoldások ugyanazt a sablont, ezért könnyebben ismertetése és karbantartása. Megtörténik az erőforrások és a értékek egyetlen fájlban. Speciális forgatókönyvek esetén a kapcsolt sablonok lehetővé teszik a célzott összetevők megoldás lebontva, és újra felhasználhatja a sablonokat.

Csatolt sablon használata esetén létrehozhat egy fő sablont, amely megkapja a paraméterértékek üzembe helyezése során. A fő sablon tartalmazza az összes csatolt sablonokat, és továbbadja értékek ezeket a sablonokat, igény szerint.

![csatolt sablonok](./media/resource-group-linked-templates/nestedTemplateDesign.png)

## <a name="link-to-a-template"></a>A sablon csatolása

Kapcsolódik egy másik sablont, vegye fel a **központi telepítések** erőforrás a fő sablont.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <inline-template-or-external-template>
      }
  }
]
```

Megadja a központi telepítési erőforrás tulajdonságainak e vannak csatolása külső sablont, vagy egy beágyazott sablon beágyazása a fő sablon függően változhat.

### <a name="inline-template"></a>Beágyazott sablon

A csatolt sablon beágyazásához használjon a **sablon** tulajdonság és a sablon tartalmazza.

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      },
      "parameters": {}
    }
  }
]
```

### <a name="external-template-and-external-parameters"></a>Külső sablon és a külső paraméterek

Egy külső sablont és paraméterfájlt csatolásához használható **templateLink** és **parametersLink**. A sablonok csatoláskor az erőforrás-kezelő szolgáltatás férhet hozzá kell lennie. Nem adhat meg egy helyi fájl vagy a fájl, amely csak akkor érhető el a helyi hálózaton. Csak adja meg, amely tartalmazza az vagy URI érték **http** vagy **https**. Egy lehetőség a csatolt sablon helyez egy tárfiókot, és használja az URI, hogy az elem egy.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parametersLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

### <a name="external-template-and-inline-parameters"></a>Külső sablon és a beágyazott paraméterek

Vagy megadhatja, hogy a paraméter beágyazott. Egy érték a fő sablonból átadása a csatolt sablon, használja a **paraméterek**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Változók használata sablonok

Az előző példák azt szemléltették, hogy a sablon hivatkozások kódolt URL-cím értékeket. Ez a módszer egy egyszerű sablon esetében is működik, de nem működik jól, ha nagy számú moduláris sablonok használata. Ehelyett hozzon létre egy statikus változó, amely tárolja a fő sablon alap URL-címet, és majd hozható létre dinamikusan URL-címeket az alap URL-címet a kapcsolt sablonok. Ez a megközelítés előnye, egyszerűen áthelyezheti vagy oszthatja ketté a sablont, mert csak módosítani szeretné a statikus változó a fő sablonban. A fő sablont a megfelelő URI-k teljes lebontott sablon továbbítja.

A következő példa bemutatja, hogyan két URL-címéből kapcsolt sablonok létrehozásához használja az alap URL-cím (**sharedTemplateUrl** és **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Is [deployment()](resource-group-template-functions-deployment.md#deployment) az alap URL-CÍMÉT az aktuális sablon, és azt használja az URL-cím lekérésére más sablonok ugyanazon a helyen. Ez a módszer akkor hasznos, ha a sablon helye megváltozik (lehet, hogy miatt versioning), vagy el szeretné kerülni a merevlemez kódolási URL-címek a sablon fájlban.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Csatolt sablonból értékek lekérése

Ahhoz, hogy egy kimeneti értéket egy csatolt sablonból, lekérdezni a tulajdonság szintaxissal: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Az alábbi példák bemutatják, hogyan lehet egy csatolt sablon hivatkoznak, és egy kimeneti értéket beolvasása. A csatolt sablon egyszerű üzenetet adja vissza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

A szülő-sablon a csatolt sablon telepíti, és lekérdezi a visszaadott érték. Figyelje meg, hogy a név szerint a központi telepítés erőforrásra hivatkozik, és azt a tulajdonságot a csatolt sablon által visszaadott nevét használja.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Más típusú erőforrások, például a csatolt sablont és egyéb erőforrások közti függőségeket is beállíthatja. Ezért ha más erőforrásokhoz szükséges egy kimeneti értéket, a csatolt sablonból, biztosíthatja, a csatolt sablon előtt történik. Vagy a csatolt sablon más erőforrások támaszkodik, ha biztos lehet benne, más erőforrások telepítése előtt a csatolt sablont.

A következő példa bemutatja a sablont, amely telepít egy nyilvános IP-címet, és az erőforrás-Azonosítót adja vissza:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Terheléselosztó telepítésekor a fenti sablon a nyilvános IP-cím használatához a sablon csatolása és a függőség hozzáadása a központi telepítési erőforrás. A terheléselosztó a nyilvános IP-címnek a csatolt sablonból a kimeneti értékre van beállítva.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "name": "[parameters('loadBalancers_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-templates-in-deployment-history"></a>A központi telepítés előzményei kapcsolt sablonok

Erőforrás-kezelő minden csatolt sablon ennek egy külön központi telepítés központi telepítés előzményei dolgozza fel. Ezért az üzembe helyezési előzményeket, mint a szülő sablon három csatolt sablonnal jelenik meg:

![Üzembe helyezési előzmények](./media/resource-group-linked-templates/deployment-history.png)

Az előzmények ezeket külön bejegyzések segítségével kimeneti értékek lekérését, a telepítés után. Az alábbi sablont hoz létre egy nyilvános IP-címet, és kiírja az IP-cím:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

A következő sablon mutató hivatkozásokat tartalmaz az előző sablont. Az alkalmazás létrehozza a három nyilvános IP-címeket.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            },
            "copy": {
                "count": 3,
                "name": "ip-loop"
            }
        }
    ]
}
```

A telepítés után a kimeneti értékek a következő PowerShell-parancsfájllal kérheti le:

```powershell
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Vagy Azure CLI-parancsfájlt:

```azurecli
for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Egy külső sablon biztonságossá tétele

Bár a csatolt sablon külsőleg elérhetőnek kell lennie, nem kell lennie a nyilvános általánosan elérhető. A sablon a személyes storage-fiók, amely csak a fiók tulajdonosa számára hozzáférhető is hozzáadhat. Ezután hozzon létre egy közös hozzáférésű jogosultságkód (SAS) token hozzáférés engedélyezése a telepítés során. A SAS-token hozzáadása az URI a csatolt sablon. Annak ellenére, hogy a jogkivonat érték az átadott egy biztonságos karakterláncot, URI-azonosítója a csatolt sablon, beleértve a SAS-jogkivonat a telepítési műveleteket rögzíti. Korlátozható a támadóknak, beállíthatja a egy lejárati idejét, a jogkivonat esetében.

A paraméterfájl is lehet korlátozni a SAS-jogkivonat-en keresztüli hozzáférés.

A következő példa bemutatja, hogyan egy SAS-jogkivonat felelt meg a sablonok csatoláskor:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

PowerShell, a szolgáltatáshitelesítést egy token ahhoz a tárolóhoz, és telepítse központilag a sablon is van:

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Azure parancssori felületen szolgáltatáshitelesítést egy token ahhoz a tárolóhoz, és léptethet érvénybe a sablonok a következő kóddal:

```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Példa sablonok

### <a name="hello-world-from-linked-template"></a>Hello World csatolt sablonból

Központi telepítése a [szülő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) és [csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json), PowerShell használata:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/helloworldparent.json
```

Vagy az Azure parancssori felület:

```azurecli-interactive
az group deployment create \
  -g examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/helloworldparent.json
```

### <a name="load-balancer-with-public-ip-address-in-linked-template"></a>Terheléselosztó csatolt sablon a nyilvános IP-címmel

Központi telepítése a [szülő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) és [csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json), PowerShell használata:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json
```

Vagy az Azure parancssori felület:

```azurecli-interactive
az group deployment create \
  -g examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json
```

### <a name="multiple-public-ip-addresses-in-linked-template"></a>A csatolt sablon több nyilvános IP-cím

Központi telepítése a [szülő sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) és [csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json), PowerShell használata:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json
```

Vagy az Azure parancssori felület:

```azurecli-interactive
az group deployment create \
  -g examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json
```

## <a name="next-steps"></a>Következő lépések

* A telepítési sorrendet, az erőforrások meghatározása, lásd: [függőségek meghatározása az Azure Resource Manager-sablonok](resource-group-define-dependencies.md).
* Adja meg egy erőforrást, de több példányát létrehozni, lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A sablont a storage-fiók beállítása és SAS-token létrehozása lépéseiért lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](resource-group-template-deploy.md) vagy [erőforrások a Resource Manager-sablonok és az Azure parancssori felület telepítése](resource-group-template-deploy-cli.md).