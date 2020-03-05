---
title: Sablonok csatolása az üzembe helyezéshez
description: Ismerteti, hogyan lehet kapcsolt sablonok használata az Azure Resource Manager-sablon moduláris sablon megoldást hozhat létre. Bemutatja, hogyan a paraméterek értékek továbbítása alkalmazásparaméter-fájlt, és dinamikusan létrehozott URL-címeket adjon meg.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e26b795a645ab9128dd738ba6a54b66ac0b7da2a
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272576"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Kapcsolt és beágyazott sablonok, az Azure-erőforrások üzembe helyezésekor

Összetett megoldások üzembe helyezéséhez számos kapcsolódó sablonban megszakíthatja a sablont, majd egy fő sablonon keresztül is üzembe helyezheti azokat. A kapcsolódó sablonok a fő sablonba ágyazott fájlok vagy sablon-szintaxisok lehetnek. Ez a cikk a **társított sablon** kifejezését használja egy külön sablonfájl használatára, amely a fő sablonhoz van csatolva. A **beágyazott sablon kifejezést használja** a beágyazott sablon szintaxisára a fő sablonon belül.

Kis és közepes méretű megoldások egyetlen sablon egyszerűbb átlátni és fenntartani. Megtekintheti az erőforrások és értékek egyetlen fájlban. A speciális forgatókönyvek esetében a csatolt sablonok lehetővé teszik a megoldás megkeresését a célként megadott összetevőkre. Ezeket a sablonokat könnyedén újra felhasználhatja más forgatókönyvek esetében is.

Oktatóanyagért lásd [: csatolt Azure Resource Manager sablonok létrehozása](template-tutorial-create-linked-templates.md).

> [!NOTE]
> Csatolt vagy beágyazott sablonok esetében csak [növekményes](deployment-modes.md) telepítési módot használhat.
>

## <a name="nested-template"></a>Beágyazott sablont

Sablon beágyazásához vegyen fel egy [központi telepítési erőforrást](/azure/templates/microsoft.resources/deployments) a fő sablonba. A **sablon** tulajdonságban határozza meg a sablon szintaxisát.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

A következő példa egy Storage-fiókot helyez üzembe egy beágyazott sablonon keresztül.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2017-05-10",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="scope-for-expressions-in-nested-templates"></a>Beágyazott sablonokban található kifejezések hatóköre

Beágyazott sablon használatakor megadhatja, hogy a rendszer kiértékelje-e a sablon kifejezéseit a fölérendelt sablon vagy a beágyazott sablon hatókörén belül. A hatókör határozza meg, Hogyan oldhatók meg a paraméterek, a változók és a függvények, például a [resourceGroup](template-functions-resource.md#resourcegroup) és az [előfizetés](template-functions-resource.md#subscription) .

A hatókör a `expressionEvaluationOptions` tulajdonsággal állítható be. Alapértelmezés szerint a `expressionEvaluationOptions` tulajdonság értéke `outer`, ami azt jelenti, hogy a szülő sablon hatókörét használja. Állítsa `inner` értékre a beágyazott sablonra a hatókör kifejezéseit.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2017-05-10",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

A következő sablon azt mutatja be, Hogyan oldhatók fel a sablon kifejezései a hatókörnek megfelelően. Tartalmaz egy `exampleVar` nevű változót, amely a fölérendelt sablonban és a beágyazott sablonban is definiálva van. A változó értékét adja vissza.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

A változó értéke a hatókör alapján változik. Az alábbi táblázat mindkét hatókör eredményét tartalmazza.

| Hatókör | Kimenet |
| ----- | ------ |
| belső | beágyazott sablonból |
| külső (vagy alapértelmezett) | fölérendelt sablonból |

Az alábbi példa egy SQL Servert telepít, és lekéri a jelszóhoz használandó Key Vault titkos kulcsot. A hatókör úgy van beállítva, hogy `inner`, mert dinamikusan létrehozza a Key Vault-azonosítót, és paraméterként továbbítja azt a beágyazott sablonhoz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> Ha a hatókör értéke `outer`, akkor a beágyazott sablonban üzembe helyezett erőforráshoz tartozó beágyazott sablon kimenetek szakaszában nem használhatja a `reference` függvényt. Egy beágyazott sablonban lévő üzembe helyezett erőforrás értékeinek visszaküldéséhez használja a belső hatókört, vagy alakítsa át a beágyazott sablont egy csatolt sablonra.

## <a name="linked-template"></a>A csatolt sablon

Sablon csatolásához vegyen fel egy [központi telepítési erőforrást](/azure/templates/microsoft.resources/deployments) a fő sablonba. A **templateLink** tulajdonságban adja meg a felvenni kívánt sablon URI-ját. A következő példa egy olyan sablonra mutat, amely egy új Storage-fiókot telepít.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Egy helyi fájlból vagy egy fájlt, amely csak a helyi hálózaton elérhető nem adható meg. Csak olyan URI-értéket adhat meg, amely **http** vagy **HTTPS protokollt**is tartalmaz. A Resource Managernek képesnek kell lennie hozzáférni a sablonhoz. Az egyik lehetőség, hogy a hivatkozott sablonnak helyezze a storage-fiókban, és az URI-t használja, a cikk.

A sablonhoz vagy paraméterekhez nem kell megadnia a `contentVersion` tulajdonságot. Tartalomverzió értéket nem ad meg, ha a sablon aktuális verziója van telepítve. Ha megad egy értéket a tartalom verziója, akkor a verziószámnak egyeznie kell a társított sablonban; Ellenkező esetben az üzembe helyezés egy hibaüzenettel meghiúsul.

### <a name="parameters-for-linked-template"></a>Csatolt sablon paraméterei

A csatolt sablon paramétereit külső fájlban vagy beágyazottan is megadhatja. Külső paraméterérték megadásakor használja a **parametersLink** tulajdonságot:

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

A paraméterek értékének megadásához használja a **Parameters** tulajdonságot.

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

A beágyazott paraméterek és a egy hivatkozást az alkalmazásparaméter-fájlt nem használható. Az üzemelő példány hibát jelez, ha `parametersLink` és `parameters` is meg van adva.

## <a name="using-copy"></a>Másolás használata

Ha egy erőforrás több példányát szeretné létrehozni beágyazott sablonnal, adja hozzá a másolás elemet a **Microsoft. Resources/Deployments** erőforrás szintjén. Ha a hatókör belső, a másolást a beágyazott sablonon belül is hozzáadhatja.

Az alábbi példa bemutatja, hogyan használható a másolás beágyazott sablonnal.

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2018-05-01",
  "name": "[concat('nestedTemplate', copyIndex())]",
  // yes, copy works here
  "copy":{
    "name": "storagecopy",
    "count": 2
  },
  "properties": {
    "mode": "Incremental",
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(variables('storageName'), copyIndex())]",
      "location": "West US",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
      // Copy works here when scope is inner
      // But, when scope is default or outer, you get an error
      //"copy":{
      //  "name": "storagecopy",
      //  "count": 2
      //}
      }
    ]
    }
  }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Változók használata a sablonok összekapcsolása

Az előző példák azt szemléltették, hogy a sablon hivatkozások URL-címértékekre változtatható. Ez a megközelítés egy egyszerű sablon esetében is működik, de nem működik jól, ha rengeteg moduláris sablonok használata. Ehelyett hozzon létre egy statikus változót, amely tárolja a fő sablon alap URL-cím, és majd dinamikusan hozhat létre URL-címek a csatolt sablonok, az alap URL-CÍMRE. Ez a megközelítés előnye, könnyedén áthelyezheti vagy elágaztatása a sablont, mert csak a statikus változó a fő sablonban módosítani kell. A fő sablont adja meg a megfelelő URI-k a pároknak sablonban.

Az alábbi példa bemutatja, hogyan használható egy alap URL-cím két URL-cím létrehozásához csatolt sablonokhoz (**sharedTemplateUrl** és **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Az [üzembe helyezés ()](template-functions-deployment.md#deployment) használatával lekérheti az aktuális sablon alap URL-címét, és ezzel az adott helyen található más sablonok URL-címét is lekérheti. Ez a megközelítés akkor hasznos, ha a sablon helye megváltozik, vagy szeretné kerülni a merevlemez kódolási URL-címek a sablon fájlban. A templateLink tulajdonság csak való távoli sablon URL-címet adja vissza. Ha egy helyi sablont használ, a tulajdonság nem érhető el.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Értékek lekérése összekapcsolt sablonból

Ha a kimeneti értéket egy csatolt sablonból szeretné lekérni, kérje le a tulajdonság értékét a következő szintaxissal: `"[reference('deploymentName').outputs.propertyName.value]"`.

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

Más típusú erőforrásokat, például beállíthatja a hivatkozott sablonnak és más erőforrások közötti függőségek. Ha más erőforrásokhoz szükség van egy kimeneti értékre a csatolt sablonból, győződjön meg róla, hogy a csatolt sablon telepítve van. Vagy ha a hivatkozott sablonnak támaszkodik más erőforrások, ellenőrizze, hogy más erőforrások telepítése előtt a hivatkozott sablonnak.

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

## <a name="deployment-history"></a>Üzembe helyezési előzmények

Erőforrás-kezelő mindegyik sablon egy külön deployment a központi telepítési előzmények, dolgozza fel. Három csatolt vagy beágyazott sablonnal rendelkező fő sablon az üzembe helyezési előzményekben a következőképpen jelenik meg:

![Üzembe helyezési előzmények](./media/linked-templates/deployment-history.png)

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

Jelenleg nem lehet olyan sablonhoz csatolni, amely egy [Azure Storage-tűzfal](../../storage/common/storage-network-security.md)mögött található Storage-fiókban van.

Az alábbi példa bemutatja, hogyan adhatók át a SAS-token való egy sablont:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
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

A PowerShell a tároló egy token beszerzéséhez, és az alábbi parancsokkal a sablonok üzembe helyezése. Figyelje meg, hogy a **containerSasToken** paraméter a sablonban van definiálva. Nem paraméter a **New-AzResourceGroupDeployment** parancsban.

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
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | A csatolt sablonból karakterlánc értéket ad vissza. |
|[Load Balancer nyilvános IP-címmel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Nyilvános IP-címet adja vissza a társított sablonból, és beállítja, hogy a terheléselosztó. |
|[Több IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Több nyilvános IP-címmel hivatkozott sablonnak hoz létre.  |

## <a name="next-steps"></a>Következő lépések

* Az oktatóanyagot a következő témakörben tekintheti meg [: oktatóanyag: csatolt Azure Resource Manager-sablonok létrehozása](template-tutorial-create-linked-templates.md).
* Az erőforrások telepítési sorrendjének definiálásával kapcsolatos további információkért lásd: [függőségek meghatározása Azure Resource Manager sablonokban](define-resource-dependency.md).
* Ha meg szeretné tudni, hogyan határozhat meg egy erőforrást, de több példányt is létrehozhat, tekintse meg az [erőforrások több példányának létrehozása Azure Resource Manager](copy-resources.md).
* A sablonok Storage-fiókban való beállításával és az SAS-token létrehozásával kapcsolatos lépésekért lásd: [erőforrások üzembe helyezése Resource manager Azure PowerShell-sablonokkal és](deploy-powershell.md) [az erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](deploy-cli.md).
