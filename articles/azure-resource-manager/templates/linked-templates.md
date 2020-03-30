---
title: Hivatkozássablonok telepítéshez
description: Bemutatja, hogyan használhatja a csatolt sablonokat egy Azure Resource Manager-sablonban egy moduláris sablonmegoldás létrehozásához. Bemutatja, hogyan lehet átadni a paraméterértékeket, megadni egy paraméterfájlt, és dinamikusan létrehozott URL-eket.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 322797383ee865ceb66c44793387da827aeb8879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131929"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Kapcsolt és beágyazott sablonok használata Azure-erőforrások üzembe helyezésekor

Összetett megoldások üzembe helyezéséhez a sablont számos kapcsolódó sablonra bonthatja, majd egy fő sablonon keresztül együtt telepítheti őket. A kapcsolódó sablonok lehetnek különálló fájlok vagy sablonszintaxis, amely a fő sablonba van ágyazva. Ez a cikk a **csatolt sablon** kifejezést használja egy külön sablonfájlra való hivatkozáshoz, amelyre a fő sablonból származó hivatkozás hivatkozik. A beágyazott **sablon** kifejezést használja a fő sablonon belüli beágyazott sablonszintaxisra való hivatkozáshoz.

Kis és közepes méretű megoldások esetén egyetlen sablon könnyebben érthető és karbantartható. Az összes erőforrást és értéket egyetlen fájlban láthatja. Speciális forgatókönyvek esetén a csatolt sablonok lehetővé teszik a megoldás célzott összetevőkre bontását. Ezeket a sablonokat egyszerűen újra felhasználhatja más esetekben.

Oktatóanyag: [Kapcsolódó Azure Resource Manager-sablonok létrehozása című oktatóanyag.](template-tutorial-create-linked-templates.md)

> [!NOTE]
> Csatolt vagy beágyazott sablonok esetén csak [növekményes](deployment-modes.md) telepítési mód használható.
>

## <a name="nested-template"></a>Beágyazott sablon

Sablon beágyazásához adjon hozzá egy [központi telepítési erőforrást](/azure/templates/microsoft.resources/deployments) a fő sablonhoz. A **sablon** tulajdonságban adja meg a sablon szintaxisát.

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

A következő példa egy beágyazott sablonon keresztül telepíti a tárfiókot.

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

### <a name="expression-evaluation-scope-in-nested-templates"></a>Kifejezésértékelési hatókör beágyazott sablonokban

Beágyazott sablon használata esetén megadhatja, hogy a sablonkifejezések kiértékelése a szülősablon vagy a beágyazott sablon hatókörén belül legyen.When using a nested template, you can specify whether template expressions are kiértékelés within the scope of the parent template or the nested template. A hatókör határozza meg, hogyan paraméterek, változók és függvények, például [resourceGroup](template-functions-resource.md#resourcegroup) és [előfizetés](template-functions-resource.md#subscription) feloldódnak.

A hatókört a `expressionEvaluationOptions` tulajdonságon keresztül állíthatja be. Alapértelmezés szerint `expressionEvaluationOptions` a tulajdonság `outer`beállítása , ami azt jelenti, hogy a szülősablon hatókörét használja. Állítsa be `inner` úgy az értéket, hogy a kifejezések kiértékelése a beágyazott sablon hatókörén belül legyen.

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

A következő sablon bemutatja, hogyan oldódnak fel a sablonkifejezések a hatókörnek megfelelően. Olyan nevű változót `exampleVar` tartalmaz, amely mind a szülősablonban, mind a beágyazott sablonban definiálva van. A változó értékét adja eredményül.

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

Az `exampleVar` `scope` értéke a tulajdonság értékétől függően `expressionEvaluationOptions`változik. Az alábbi táblázat mindkét hatókör eredményeit mutatja be.

| `expressionEvaluationOptions` `scope` | Kimenet |
| ----- | ------ |
| Belső | beágyazott sablonból |
| külső (vagy alapértelmezett) | szülősablonból |

A következő példa telepíti az SQL-kiszolgálót, és beolvassa a jelszóhoz használandó kulcstartót. A hatókör van `inner` beállítva, mert dinamikusan létrehozza `adminPassword.reference.keyVault` a key vault-azonosító (lásd a külső sablonok) `parameters`és átadja azt paraméterként a beágyazott sablont.

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
> Ha a hatókör `outer`a beállítás , `reference` nem használhatja a függvényt a beágyazott sablon kimenetek szakaszában a beágyazott sablonban üzembe helyezett erőforráshoz. Ha egy beágyazott sablonban lévő telepített erőforrás értékeit `inner` szeretné visszaadni, használja a hatókört, vagy konvertálja a beágyazott sablont csatolt sablonná.

## <a name="linked-template"></a>Csatolt sablon

Sablon csatolásához adjon hozzá egy [központi telepítési erőforrást](/azure/templates/microsoft.resources/deployments) a fő sablonhoz. A **templateLink** tulajdonságban adja meg a felvenni kívánt sablon URI-ját. A következő példa egy új tárfiókot üzembe helyező sablonra hivatkozik.

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

Csatolt sablonra való hivatkozáskor az `uri` érték nem lehet helyi fájl vagy olyan fájl, amely csak a helyi hálózaton érhető el. Meg kell adnia egy OLYAN URI-értéket, amely **http** vagy **https**néven letölthető. 

> [!NOTE]
>
> Olyan paramétereket használó sablonokra hivatkozhat, amelyek végül **a http** vagy `_artifactsLocation` **https**protokollt használó elemre oldanak fel, például a következő paraméter használatával:`"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]",`



Az Erőforrás-kezelőnek képesnek kell lennie a sablon elérésére. Az egyik lehetőség az, hogy helyezze el a csatolt sablont egy tárfiókban, és használja az adott elem URI-ját.

### <a name="parameters-for-linked-template"></a>Csatolt sablon paraméterei

A csatolt sablon paramétereit külső fájlban vagy szövegközi fájlban is megadhatja. Külső paraméterfájl megadásakor használja a **parametersLink tulajdonságot:**

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

A paraméterértékek szövegközi átadására használja a **parameters tulajdonságot.**

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

Nem használható mind a szövegközi paraméterek, sem a paraméterfájlra mutató hivatkozás. A központi telepítés hiba `parametersLink` esetén `parameters` sikertelen, ha mindkettő meg van adva.

## `contentVersion`

Önnek nem kell biztosítania `contentVersion` a `templateLink` tulajdon `parametersLink` t vagy a tulajdon. Ha nem ad `contentVersion`meg egy, a sablon aktuális verziója telepítve van. Ha értéket ad meg a tartalomverzióhoz, annak meg kell egyeznie a csatolt sablon verziójával; ellenkező esetben a központi telepítés hiba miatt sikertelen.

## <a name="using-variables-to-link-templates"></a>Változók használata sablonok csatolásához

Az előző példák a sablonhivatkozások kódolt URL-értékeit mutatták be. Ez a megközelítés működhet egy egyszerű sablon, de nem működik jól egy nagy sor moduláris sablonok. Ehelyett létrehozhat egy statikus változót, amely a fő sablon alap URL-címét tárolja, majd dinamikusan létrehozhat URL-címeket a csatolt sablonokhoz az adott alap URL-címből. Ennek a megközelítésnek az az előnye, hogy könnyedén áthelyezheti vagy elágazhatja a sablont, mert csak a statikus változót kell módosítania a fő sablonban. A fő sablon a megfelelő URI-kat adja át a lebomlott sablonon keresztül.

A következő példa bemutatja, hogyan lehet egy alap URL-t használni két URL-cím létrehozásához csatolt sablonokhoz (**sharedTemplateUrl** és **vmTemplate**).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

A [deployment()](template-functions-deployment.md#deployment) segítségével leiskaphatja az aktuális sablon alap URL-címét, és ezzel lekéri más sablonok URL-címét ugyanazon a helyen. Ez a megközelítés akkor hasznos, ha a sablon helye megváltozik, vagy el szeretné kerülni a sablonfájlban lévő URL-címek szigorú kódolását. A templateLink tulajdonság csak akkor kerül visszaadásra, ha egy URL-címmel rendelkező távoli sablonhoz kapcsolódik. Ha helyi sablont használ, az a tulajdonság nem érhető el.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Végső soron a változót `uri` egy `templateLink` tulajdonság tulajdonságában kell használni.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Másolás használata

Ha egy erőforrástöbb példányát szeretné beágyazott sablonnal létrehozni, adja hozzá a másolási elemet a **Microsoft.Resources/deployments** erőforrás szintjén. Vagy ha a hatókör belső, hozzáadhatja a másolatot a beágyazott sablonon belül.

A következő példasablon bemutatja, hogyan használható a másolás beágyazott sablonnal.

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

## <a name="get-values-from-linked-template"></a>Értékek lekérése összekapcsolt sablonból

Ha egy csatolt sablonból szeretne kimeneti értéket lekérni, `"[reference('deploymentName').outputs.propertyName.value]"`olvassa be a tulajdonság értékét a következő szintaxissal: .

Amikor egy kimeneti tulajdonságot csatolt sablonból kap, a tulajdonság neve nem tartalmazhat kötőjelet.

Az alábbi példák bemutatják, hogyan hivatkozzon egy csatolt sablonra, és hogyan olvasson le egy kimeneti értéket. A csatolt sablon egy egyszerű üzenetet ad vissza.  Először is, a csatolt sablon:

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

A fő sablon telepíti a csatolt sablont, és megkapja a visszaadott értéket. Figyelje meg, hogy név szerint hivatkozik a központi telepítési erőforrásra, és a csatolt sablon által visszaadott tulajdonság nevét használja.

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

Más erőforrástípusokhoz is ugyanúgy függőségeket állíthat be a csatolt sablon és más erőforrások között. Ha más erőforrások kimeneti értéket igényelnek a csatolt sablonból, győződjön meg arról, hogy a csatolt sablon telepítve van előttük. Ha a csatolt sablon más erőforrásokra támaszkodik, győződjön meg arról, hogy a csatolt sablon előtt más erőforrások vannak telepítve.

A következő példa egy nyilvános IP-címet üzembe helyező sablont mutat be, és az adott nyilvános IP-címhez az Azure-erőforrás-azonosítót adja vissza:

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

Ha az előző sablon nyilvános IP-címét szeretné használni egy terheléselosztó üzembe helyezésekor, `Microsoft.Resources/deployments` csatoljon a sablonhoz, és deklarálja az erőforrástól való függőséget. A terheléselosztó nyilvános IP-címe a csatolt sablon kimeneti értékére van beállítva.

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
                // this is where the output value from linkedTemplate is used
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
      // This is where the dependency is declared
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

## <a name="deployment-history"></a>Telepítési előzmények

Az Erőforrás-kezelő minden sablont külön központi telepítésként dolgoz fel a központi telepítés előzményeiben. A három összekapcsolt vagy beágyazott sablonnal rendelkező fő sablon a következő ként jelenik meg a telepítési előzményekben:

![Telepítési előzmények](./media/linked-templates/deployment-history.png)

Ezeket a különálló bejegyzéseket az előzményekben a kimeneti értékek lekéréséhez használhatja a központi telepítés után. A következő sablon létrehoz egy nyilvános IP-címet, és adja ki az IP-címet:

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

A következő sablon az előző sablonra mutat. Három nyilvános IP-címet hoz létre.

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

A telepítés után a következő PowerShell-parancsfájllal kérheti le a kimeneti értékeket:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Vagy az Azure CLI-parancsfájl bash rendszerhéjban:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Külső sablon biztonságossá tétele

Bár a csatolt sablonnak külsőleg elérhetőnek kell lennie, nem kell általánosan elérhetőnek lennie a nyilvánosság számára. A sablont hozzáadhatja egy privát tárfiókhoz, amely csak a tárfiók tulajdonosa számára érhető el. Ezután hozzon létre egy közös hozzáférésű aláírás (SAS) jogkivonatot a hozzáférés engedélyezéséhez az üzembe helyezés során. Hozzáadja ezt a SAS-jogkivonatot a csatolt sablon URI-jéhez. Annak ellenére, hogy a jogkivonat biztonságos karakterláncként kerül átadásra, a csatolt sablon URI-ja, beleértve a SAS-jogkivonatot is, be van jelentkezve a központi telepítési műveletekben. Az expozíció korlátozásához állítson be egy lejárati jogkivonatot.

A paraméterfájl sas-jogkivonaton keresztüli hozzáférésre is korlátozható.

Jelenleg nem csatolhat sablont egy [Azure Storage-tűzfal](../../storage/common/storage-network-security.md)mögötti tárfiókban lévő sablonhoz.

A következő példa bemutatja, hogyan lehet átadni egy SAS-jogkivonatot, amikor sablonhoz kapcsolódik:

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

A PowerShellben egy jogkivonatot kap a tárolóhoz, és a következő parancsokkal telepíti a sablonokat. Figyelje meg, hogy a **containerSasToken** paraméter definiálva van a sablonban. Ez nem egy paraméter a **New-AzResourceGroupDeployment** parancsban.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Az Azure CLI egy Bash rendszerhéjban, kap egy jogkivonatot a tárolóhoz, és telepítse a sablonokat a következő kóddal:

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
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák a csatolt sablonok gyakori felhasználási céljait mutatják be.

|Fő sablon  |Csatolt sablon |Leírás  |
|---------|---------| ---------|
|[helló világ](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | A csatolt sablonból származó karakterláncot ad eredményül. |
|[Nyilvános IP-címmel rendelkező terheléselosztó](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Nyilvános IP-címet ad vissza a csatolt sablonból, és beállítja ezt az értéket a terheléselosztóban. |
|[Több IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [csatolt sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Több nyilvános IP-címet hoz létre a csatolt sablonban.  |

## <a name="next-steps"></a>További lépések

* Az oktatóanyag megtekintéséhez olvassa el [az Oktatóanyag: Csatolt Azure Resource Manager-sablonok létrehozása című témakört.](template-tutorial-create-linked-templates.md)
* Az erőforrások telepítési sorrendjének meghatározásáról a [Függőségek definiálása az Azure Resource Manager-sablonokban](define-resource-dependency.md)című témakörben olvashat.
* Ha meg szeretné tudni, hogyan definiálhat egy erőforrást, de több példányt hozhat létre, olvassa [el az Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakört.](copy-resources.md)
* A tárfiókban lévő sablon beállításának és a SAS-jogkivonat létrehozásának lépéseit az [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure PowerShell használatával,](deploy-powershell.md) illetve [erőforrások üzembe helyezése Erőforrás-kezelő sablonokkal és az Azure CLI használatával részben](deploy-cli.md)olvashatja el.
