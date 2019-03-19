---
title: Sablonok az Azure-beli hivatkozás |} A Microsoft Docs
description: Ismerteti, hogyan lehet kapcsolt sablonok használata az Azure Resource Manager-sablon moduláris sablon megoldást hozhat létre. Bemutatja, hogyan a paraméterek értékek továbbítása alkalmazásparaméter-fájlt, és dinamikusan létrehozott URL-címeket adjon meg.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/18/2019
ms.author: tomfitz
ms.openlocfilehash: d4ecccf8787e369b9a3270eab2d01a01ce7ae0c7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58174307"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Kapcsolt és beágyazott sablonok, az Azure-erőforrások üzembe helyezésekor

A megoldás üzembe helyezéséhez használhatja egyetlen sablon és a egy fő sablont számos kapcsolódó sablonok. A kapcsolódó sablon lehet egy külön fájlt, amely kapcsolódik a fő sablonból, vagy egy sablont, amely a fő sablon van beágyazva.

Kis és közepes méretű megoldások egyetlen sablon egyszerűbb átlátni és fenntartani. Megtekintheti az erőforrások és értékek egyetlen fájlban. A speciális alkalmazási a hivatkozott sablonok lehetővé teszik a megoldás célzott összetevőből felosztania, és újra felhasználhatja a sablonokat.

Kapcsolt sablonok használata esetén hozzon létre egy fő sablont, amely megkapja a paraméterértékek üzembe helyezés során. A fő sablon tartalmazza az összes társított sablon, és értékeket továbbítja ezeket a sablonokat, igény szerint.

Foglalkozó oktatóanyagért lásd: [oktatóanyag: a csatolt Azure Resource Manager-sablonok létrehozása](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> Kapcsolt és beágyazott sablonok csak használhat [növekményes](deployment-modes.md) üzembe helyezési mód.
>

## <a name="link-or-nest-a-template"></a>Hivatkozás, vagy egy sablon beágyazása

Egy másik sablonnal, vegyen fel egy **központi telepítések** erőforrás a fő sablont.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

Ad meg a központi telepítési erőforrás tulajdonságainak függ attól, létrehozhatja, ha egy külső sablon akár egy beágyazott sablont, a fő sablont a beágyazási.

### <a name="nested-template"></a>Beágyazott sablont

A sablon ugyanazon a fő sablont beágyazása, használja a **sablon** tulajdonságot, és adja meg a sablon szintaxisáról.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-07-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> Beágyazott sablonok, a paraméterek vagy a beágyazott sablonon belül definiált változókat nem használható. Paraméterek és változók a fő sablonból is használhatja. Az előző példában `[variables('storageName')]` beolvas egy értéket a fő sablonból, nem a beágyazott sablont. Ez a korlátozás nem vonatkozik a külső sablonokat.
>
> A két erőforrás definiált egy beágyazott sablont és a egy erőforrás függ a másik, a függőség értéke csak a függő erőforrás neve:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> Nem használhatja a `reference` függvény egy beágyazott sablont kimenetek szakaszában. Az értékeket egy üzembe helyezett erőforrás visszaadása egy beágyazott sablont, váltson egy hivatkozott sablonnak a beágyazott sablont.

A beágyazott sablonhoz szükséges a [azonos tulajdonságokkal](resource-group-authoring-templates.md) standard sablonként.

### <a name="external-template-and-external-parameters"></a>Külső sablon és a külső paraméterek

Társítson egy külső sablont és paraméterfájlt, használja a **templateLink** és **parametersLink**. Kapcsolja a sablonok, az erőforrás-kezelő szolgáltatás elérheti azt kell lennie. Egy helyi fájlból vagy egy fájlt, amely csak a helyi hálózaton elérhető nem adható meg. Csak adja meg a URI értéket, amely akár **http** vagy **https**. Az egyik lehetőség, hogy a hivatkozott sablonnak helyezze a storage-fiókban, és az URI-t használja, a cikk.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
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

Nem kell adnia a `contentVersion` tulajdonságot a sablonból vagy a paraméterek számára. Tartalomverzió értéket nem ad meg, ha a sablon aktuális verziója van telepítve. Ha megad egy értéket a tartalom verziója, akkor a verziószámnak egyeznie kell a társított sablonban; Ellenkező esetben az üzembe helyezés egy hibaüzenettel meghiúsul.

### <a name="external-template-and-inline-parameters"></a>Külső sablon és a beágyazott paraméterek

Vagy megadhatja a paraméterrel beágyazott. A beágyazott paraméterek és a egy hivatkozást az alkalmazásparaméter-fájlt nem használható. Egy hiba miatt nem sikerül a telepítés során is `parametersLink` és `parameters` vannak megadva.

A fő sablonból értéket adnak át a hivatkozott sablonnak, használja a **paraméterek**.

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2018-05-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "Incremental",
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

## <a name="using-variables-to-link-templates"></a>Változók használata a sablonok összekapcsolása

Az előző példák azt szemléltették, hogy a sablon hivatkozások URL-címértékekre változtatható. Ez a megközelítés egy egyszerű sablon esetében is működik, de nem működik jól, ha rengeteg moduláris sablonok használata. Ehelyett hozzon létre egy statikus változót, amely tárolja a fő sablon alap URL-cím, és majd dinamikusan hozhat létre URL-címek a csatolt sablonok, az alap URL-CÍMRE. Ez a megközelítés előnye, könnyedén áthelyezheti vagy elágaztatása a sablont, mert csak a statikus változó a fő sablonban módosítani kell. A fő sablont adja meg a megfelelő URI-k a pároknak sablonban.

A következő példa bemutatja, hogyan hozhat létre csatolt sablonok két URL-címet a kiindulási URL-cím használatával (**sharedTemplateUrl** és **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Is [deployment()](resource-group-template-functions-deployment.md#deployment) a kiindulási URL-cím beszerzése a jelenlegi sablonhoz, és használja, amely az URL-Címének lekéréséhez más sablonok ugyanazon a helyen. Ez a megközelítés akkor hasznos, ha a sablon helye megváltozik, vagy szeretné kerülni a merevlemez kódolási URL-címek a sablon fájlban. A templateLink tulajdonság csak való távoli sablon URL-címet adja vissza. Ha egy helyi sablont használ, a tulajdonság nem érhető el.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Értékek lekérése összekapcsolt sablonból

Egy kimeneti értéket egy hivatkozott sablonnak a lekéréséhez a szintaxissal tulajdonság értékét a vizualizációhoz: `"[reference('deploymentName').outputs.propertyName.value]"`.

Amikor egy kimeneti tulajdonság lekérése egy hivatkozott sablonnak, a tulajdonság neve nem tartalmazhatja az kötőjellel.

Az alábbi példák bemutatják, hogyan lehet hivatkozni egy hivatkozott sablonnak, és a egy kimeneti értéket lekéréséhez. A hivatkozott sablonnak egyszerű üzenetet adja vissza.

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

A fő sablon üzembe helyezi a hivatkozott sablonnak, és a visszaadott érték beolvasása. Figyelje meg, hogy a név szerint a központi telepítési erőforrásra hivatkozik, és használja a társított sablon által visszaadott tulajdonság nevére.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
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

Más típusú erőforrásokat, például beállíthatja a hivatkozott sablonnak és más erőforrások közötti függőségek. Ezért más erőforrásokhoz egy kimeneti értéket, a társított sablonból van szükség, amikor győződjön meg arról, a társított sablon előtt őket üzembe. Vagy ha a hivatkozott sablonnak támaszkodik más erőforrások, ellenőrizze, hogy más erőforrások telepítése előtt a hivatkozott sablonnak.

Az alábbi példa bemutatja egy sablont, amely üzembe helyez egy nyilvános IP-címet, és visszaadja az erőforrás-azonosító:

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
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
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

A nyilvános IP-címet az előző sablon üzembe helyezésekor használandó egy terheléselosztót, kapcsolja össze a sablont, és hozzáadjon egy függőséget az üzembe helyezés erőforráson. A terheléselosztó nyilvános IP-címet a kimeneti értékre van állítva, a társított sablonból.

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
            "apiVersion": "2018-11-01",
            "name": "[parameters('loadBalancers_name')]",
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
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
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

## <a name="linked-and-nested-templates-in-deployment-history"></a>Az üzembe helyezési előzmények kapcsolt és beágyazott sablonok

Erőforrás-kezelő mindegyik sablon egy külön deployment a központi telepítési előzmények, dolgozza fel. Ezért az üzembe helyezési előzményeket, mint egy fő sablont, amely három kapcsolt és beágyazott sablonok jelenik meg:

![Üzembe helyezési előzmények](./media/resource-group-linked-templates/deployment-history.png)

Az előzményekben található ezeket a tételeket külön használatával lekérheti a kimeneti értékeket az üzembe helyezés után. Az alábbi sablont hoz létre egy nyilvános IP-címet, és megjeleníti az IP-cím:

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
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
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

A következő sablon mutató hivatkozásokat tartalmaz a fenti sablon. Három nyilvános IP-címeket hoz létre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "copy": {
                "count": 3,
                "name": "ip-loop"
            },
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            }
        }
    ]
}
```

A telepítést követően a kimeneti értékeket a következő PowerShell-parancsfájllal kérheti le:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Vagy az Azure CLI-szkript Bash-felületen:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Egy külső sablon biztonságossá tétele

A hivatkozott sablonnak külsőleg elérhetőnek kell lennie, bár az általánosan elérhető a nyilvános nem szükséges. A sablon adhat hozzá egy privát storage-fiókot, amely csak a tárfiók tulajdonosa számára hozzáférhető. Ezután hozzon létre egy közös hozzáférésű jogosultságkód (SAS-) tokenekkel hozzáférés engedélyezése az üzembe helyezés során. A SAS-token hozzáadása az URI-t, a társított sablon. Annak ellenére, hogy a jogkivonat átadott biztonságos karakterláncként, URI-ját a hivatkozott sablonnak, többek között a SAS-jogkivonat kerül a központi telepítési műveleteket. Korlátozhatja a fenyegetéseknek, állítsa be a jogkivonat-lejárati idejét.

A paraméterfájl is lehet korlátozni a hozzáférést keresztül SAS-token.

Az alábbi példa bemutatja, hogyan adhatók át a SAS-token való egy sablont:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
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

A PowerShell a tároló egy token beszerzéséhez, és az alábbi parancsokkal a sablonok üzembe helyezése. Figyelje meg, hogy a **containerSasToken** paraméter van definiálva a sablonban. A paraméter nem fut a **New-AzResourceGroupDeployment** parancsot.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Azure CLI Bash-felületen a tároló egy token beszerzéséhez, és a sablonok a következő kód üzembe helyezése:

```azurecli-interactive
#!/bin/bash

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

Az alábbi példák bemutatják a gyakori használati hivatkozott sablonok.

|Fő sablon  |A csatolt sablon |Leírás  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[A csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | A csatolt sablonból karakterlánc értéket ad vissza. |
|[A terheléselosztó nyilvános IP-címmel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[A csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Nyilvános IP-címet adja vissza a társított sablonból, és beállítja, hogy a terheléselosztó. |
|[Több IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [A csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Több nyilvános IP-címmel hivatkozott sablonnak hoz létre.  |

## <a name="next-steps"></a>További lépések

* Go-oktatóanyagot, tekintse meg [oktatóanyag: a csatolt Azure Resource Manager-sablonok létrehozása](./resource-manager-tutorial-create-linked-templates.md).
* A telepítési sorrendet, az erőforrások definiálása kapcsolatos további információkért lásd: [függőségek meghatározása az Azure Resource Manager-sablonok](resource-group-define-dependencies.md).
* Adja meg egy erőforrást, de számos példányának létrehozása kezelésével kapcsolatos információkért lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A storage-fiókban lévő sablon beállítása és SAS-token létrehozása lépéseiért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](resource-group-template-deploy.md) vagy [erőforrások Resource Manager-sablonok üzembe helyezése és Az Azure CLI](resource-group-template-deploy-cli.md).
