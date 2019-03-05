---
title: Az Azure Resource Manager-sablon szerkezetének és szintaxisának |} A Microsoft Docs
description: Ismerteti, és a tulajdonságait az Azure Resource Manager-sablonok deklaratív JSON-szintaxis használatával.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2019
ms.author: tomfitz
ms.openlocfilehash: 024a622484a83957c9ab5f4a684a346a55787ccf
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313361"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Megismerheti a szerkezetének és szintaxisának az Azure Resource Manager-sablonok

Ez a cikk ismerteti az Azure Resource Manager-sablonok szerkezetének. Egy sablon és az elérhető tulajdonságok köre szakaszt az eltérő szakaszok tükrözze. A sablon JSON-t és kifejezések, amelyek segítségével kialakíthatja az üzemelő példány értékeit áll.

Ez a cikk szól, akik rendelkeznek a Resource Manager-sablonok bizonyos fokú ismeretét. Ez a struktúra és a sablon szintaxisát részletes információkat tartalmaz. Ha azt szeretné, hogy a sablonok létrehozásának bemutatása, [az első Azure Resource Manager-sablon létrehozása](resource-manager-create-first-template.md).

## <a name="template-format"></a>Sablon formátuma

A legegyszerűbb szerkezetét, a sablon a következő elemekből áll:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| $schema |Igen |A JSON-fájl, amely leírja a sablon nyelvének verziója helye.<br><br> Erőforráscsoportok üzemelő példányainak használja: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Előfizetések üzemelő példányai használja: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Igen |A sablon (például 1.0.0.0) verziója. Bármilyen értéket megadhat ehhez az elemhez. A dokumentum jelentős változásokat ezt az értéket használja a sablonban. A sablon használatával erőforrások üzembe helyezésekor, ezt az értéket segítségével győződjön meg arról, hogy a megfelelő sablon használatban van-e. |
| paraméterek |Nem |Üzembe helyezés testreszabásához erőforrások üzembe helyezésének végrehajtásakor biztosított értékeket. |
| Változók |Nem |Egyszerűsítése érdekében a Sablonnyelv-kifejezések, JSON-töredék a sablonban használt értékeket. |
| functions |Nem |Felhasználó által megadott funkciók érhetők el a sablonon belül. |
| erőforrások |Igen |Erőforrástípusok, telepített vagy egy erőforráscsoport vagy előfizetés frissített. |
| kimenetek |Nem |Üzembe helyezés után visszaadott értékek. |

Minden elem is megadhatja a tulajdonságokkal rendelkezik. Az alábbi példa bemutatja a teljes szintaxisra, sablon:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "parameters": {  
    "<parameter-name>" : {
      "type" : "<type-of-parameter-value>",
      "defaultValue": "<default-value-of-parameter>",
      "allowedValues": [ "<array-of-allowed-values>" ],
      "minValue": <minimum-value-for-int>,
      "maxValue": <maximum-value-for-int>,
      "minLength": <minimum-length-for-string-or-array>,
      "maxLength": <maximum-length-for-string-or-array-parameters>,
      "metadata": {
        "description": "<description-of-the parameter>" 
      }
    }
  },
  "variables": {
    "<variable-name>": "<variable-value>",
    "<variable-object-name>": {
      <variable-complex-type-value>
    },
    "<variable-object-name>": {
      "copy": [
        {
          "name": "<name-of-array-property>",
          "count": <number-of-iterations>,
          "input": <object-or-value-to-repeat>
        }
      ]
    },
    "copy": [
      {
        "name": "<variable-array-name>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "functions": [
    {
      "namespace": "<namespace-for-your-function>",
      "members": {
        "<function-name>": {
          "parameters": [
            {
              "name": "<parameter-name>",
              "type": "<type-of-parameter-value>"
            }
          ],
          "output": {
            "type": "<type-of-output-value>",
            "value": "<function-expression>"
          }
        }
      }
    }
  ],
  "resources": [
    {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
        "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
        },
        "comments": "<your-reference-notes>",
        "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
        },
        "dependsOn": [
          "<array-of-related-resource-names>"
        ],
        "properties": {
          "<settings-for-the-resource>",
          "copy": [
            {
              "name": ,
              "count": ,
              "input": {}
            }
          ]
        },
        "resources": [
          "<array-of-child-resources>"
        ]
    }
  ],
  "outputs": {
    "<outputName>" : {
      "condition": "<boolean-value-whether-to-output-value>",
      "type" : "<type-of-output-value>",
      "value": "<output-value-expression>"
    }
  }
}
```

Ez a cikk ismerteti a sablon nagyobb részletességgel szakaszait.

## <a name="syntax"></a>Szintaxis

A sablon szintaxisa alapszintű JSON. Kifejezések és függvények kiterjesztheti a sablonon belül elérhető JSON-értékeit.  A szögletes zárójelek kifejezések JSON-karakterlánc-literálnak belül írt amelynek első és utolsó karakterek: `[` és `]`, illetve. A kifejezés értékét a sablon üzembe helyezésekor lesz kiértékelve. Ír egy szöveges karakterlánc, míg a kifejezés kiértékelésének eredménye lehet egy másik JSON típusú, például egy tömb, vagy az egész szám, attól függően, a tényleges kifejezést.  Kezdje egy zárójelet szövegkonstansnak kell `[`, de nem rendelkezik, azt értelmezni, hogy egy kifejezés, adjon hozzá egy extra szögletes zárójelet, indítsa el a karakterlánc `[[`.

Általában akkor kifejezések használata a functions konfigurálásához az üzembe helyezési műveletek végrehajtásához. Csak, például a JavaScript, függvényhívások formázott `functionName(arg1,arg2,arg3)`. A pont és a [index] operátorok használatával tulajdonságok hivatkozik.

Az alábbi példa bemutatja, hogyan hozhat létre, amely egy értéket számos funkciót használni kívánt:

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
}
```

A sablonokban használható függvények teljes listájáért lásd: [Azure Resource Manager-sablonfüggvények](resource-group-template-functions.md). 

## <a name="parameters"></a>Paraméterek

A sablon a Paraméterek szakaszban megadhatja az erőforrások üzembe helyezésekor mely értékeket kell megadni. Ezek a paraméterértékek szabhatja testre az üzembe helyezés értékek, amelyek azáltal, hogy egy adott környezetben (például fejlesztési, tesztelési és éles környezetben). Nem kell adnia a sablonban szereplő paraméterekkel, de paraméterek nélkül a sablon mindig telepít az azonos nevek, helyek és tulajdonságok ugyanazokhoz az erőforrásokhoz.

Most már legfeljebb 256 paramétereket a sablonban. A paraméterek számát, csökkentheti a több tulajdonságot tartalmazó objektumok használatával, ahogyan az ebben a cikkben.

### <a name="available-properties"></a>Rendelkezésre álló tulajdonságok

A paraméter a rendelkezésre álló tulajdonságok a következők:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>" 
    }
  }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| parameterName |Igen |A paraméter neve. Érvényes JavaScript-azonosítónak kell lennie. |
| type |Igen |A paraméter értékének típusa. Az engedélyezett típusokkal és az értékek **karakterlánc**, **securestring**, **int**, **bool**, **objektum**, **secureObject**, és **tömb**. |
| DefaultValue érték |Nem |A paramétert, ha a nem érték van megadva a paraméter alapértelmezett értéke. |
| allowedValues |Nem |Győződjön meg arról, hogy a megfelelő értéket adott-e, hogy a paraméter megengedett értékek tömbje. |
| minValue |Nem |Int típusú paraméterekhez minimális értéke, ez az érték, amely naptól. |
| maxValue |Nem |A maximális int típusú paraméterekhez, ez az érték értéke is beleértve. |
| a minLength |Nem |A minimális karakterlánc, a biztonságos karakterláncot és array típusú paraméterekhez, ez az érték hossza között lehet. |
| maxLength |Nem |A karakterlánc, a biztonságos karakterláncot és array típusú paraméterekhez, ez az érték hossza legfeljebb között lehet. |
| leírás |Nem |A portálon keresztül a felhasználók számára megjelenő paraméter leírása. További információkért lásd: [sablonok megjegyzéseket](#comments). |

### <a name="define-and-use-a-parameter"></a>Határozza meg, és a egy paraméterrel

Az alábbi példa bemutatja egy egyszerű paraméterdefinícióhoz. A paraméter nevét adja meg, és meghatározza, hogy egy karakterlánc értéket vesz igénybe. A paraméter csak fogad értékeket, amelyek használat bírnak. Azt adja meg az alapértelmezett értéket, amikor nem érték van megadva üzembe helyezés során. Végül a paraméter használatakor leírását tartalmazza.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

A sablon a következő szintaxissal a paraméter értékére hivatkozik:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>A paraméterekkel sablonfüggvények

Egy paraméter alapértelmezett értékének megadása esetén a legtöbb sablonfüggvények is használhatja. Egy másik paraméter értékének használatával hozhat létre egy alapértelmezett értéket. A következő sablon az alapértelmezett érték a functions használatát mutatja be:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Nem használhatja a `reference` függvény a Paraméterek szakaszban. Paraméterek értékeli ki a telepítés előtt úgy a `reference` függvény nem lehet lekérni egy erőforrást futási állapotát. 

### <a name="objects-as-parameters"></a>Objektumok paraméterek

Kapcsolódó értékek rendszerezheti azokat objektumként átadásával könnyebben lehet. Ez a megközelítés a paraméterek számát, a sablonban is csökkenti.

Adja meg a paramétert a sablont, és adja meg a JSON-objektum egyetlen érték helyett üzembe helyezés során. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Ezután hivatkozhat a altulajdonságokat paraméter a pont operátor használatával.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>A paraméter példasablonkészlet

A példa sablonok bemutatják, bizonyos forgatókönyvek paraméterek használatával. Tesztelje a paraméterek kezelésének módját a különböző helyzetekben való telepítéséhez.

|Sablon  |Leírás  |
|---------|---------|
|[a functions alapértelmezett értékeket a paraméterek](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Bemutatja, hogyan használható a sablonokban használható függvények paramétereinek alapértelmezett értékei meghatározásakor. A sablon nem üzembe erőforrásokat. Ez hoz létre a paraméterértékeket, és ezeket az értékeket ad vissza. |
|[A paraméter objektum](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Azt mutatja be, a paraméter egy objektum használatával. A sablon nem üzembe erőforrásokat. Ez hoz létre a paraméterértékeket, és ezeket az értékeket ad vissza. |

## <a name="variables"></a>Változók

A változók szakaszban, a sablon egész értékek, amelyek segítségével hozhatnak létre. Nem kell definiálnia a változókat, de azok összetett kifejezések csökkentésével gyakran egyszerűbb a sablont.

### <a name="available-definitions"></a>Rendelkezésre álló definíciók

Az alábbi példa bemutatja a változó definiálása az elérhető lehetőségek közül:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

További információ `copy` több értéket egy változóhoz létrehozásával kapcsolatban lásd: [változó iteráció](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definiálja és egy változó

Az alábbi példa bemutatja a változó definícióját. Létrehoz egy karakterláncértéket a tárfiók neve. Több sablonokban használható függvények használatával jelenik meg a paraméter értékét, és egy egyedi karakterlánccá fűzi össze azt.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Az erőforrás definiálásakor használja a változót.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Konfigurációs változók

Használhatja a komplex JSON-típusok meghatározása a kapcsolódó értékeket egy adott környezetben.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

A paraméterek hozzon létre egy értéket, amely jelzi, hogy melyik konfigurációs értékeket kell használni.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

A jelenlegi beállítások kérheti le:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Változó példasablonkészlet

A példa sablonok bizonyos változók használatára vonatkozó forgatókönyvek bemutatása. Tesztelje a változók kezelésének módját a különböző helyzetekben való telepítéséhez. 

|Sablon  |Leírás  |
|---------|---------|
| [változó definíciók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Azt ismerteti, hogy a különböző típusú változót. A sablon nem üzembe erőforrásokat. Ez változóértékek entitástörzséhez, és ezeket az értékeket ad vissza. |
| [konfigurációs változó](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Bemutatja, hogy egy változó, amely a konfigurációs értékeket határozza meg. A sablon nem üzembe erőforrásokat. Ez változóértékek entitástörzséhez, és ezeket az értékeket ad vissza. |
| [hálózati biztonsági szabály](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) és [alkalmazásparaméter-fájlt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Létrehoz egy tömböt a biztonsági szabályok rendel egy hálózati biztonsági csoportot a megfelelő formátumban. |


## <a name="functions"></a>Functions

A sablonon belül a saját funkciókat is létrehozhat. Ezek a függvények használhatók a sablonban. Általában megadhat bonyolult kifejezés, amelyet szeretne, ismételje meg a sablon során. A felhasználó által definiált függvények készítése kifejezések és [funkciók](resource-group-template-functions.md) , amelyek használata támogatott.

A user függvény definiálásakor bizonyos korlátozások vonatkoznak:

* A függvény nem tud hozzáférni a változókat.
* A függvény csak használhatja a függvény a definiált paraméterek. Ha a [paraméterek függvény](resource-group-template-functions-deployment.md#parameters) belül a felhasználó által definiált függvény korlátozva van, a függvény paramétereit.
* A függvény nem hívható meg más felhasználó által definiált függvények.
* A függvény nem használható a [függvényre](resource-group-template-functions-resource.md#reference).
* A függvény paramétereit nem lehet alapértelmezett értékük van.

A függvények, így elkerülhetők a névütközések a sablonokban használható függvények névtér értéket kell megadni. Az alábbi példa bemutatja egy függvényt, amely egy storage-fiók nevét adja vissza:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

A függvény hívásakor:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>További források
Az erőforrások szakaszban meghatározhatja az erőforrásokat, amelyek telepítése vagy frissítése. Ez a szakasz is kapott bonyolult, mert ismernie kell a típusok, helyezi üzembe, adja meg a megfelelő értékeket.

```json
"resources": [
  {
    "apiVersion": "2016-08-01",
    "name": "[variables('webSiteName')]",
    "type": "Microsoft.Web/sites",
    "location": "[resourceGroup().location]",
    "properties": {
      "serverFarmId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Web/serverFarms/<plan-name>"
    }
  }
],
```

Feltételesen belefoglalása vagy kizárása erőforrás üzembe helyezés során használja a [feltétel elem](resource-manager-templates-resources.md#condition). Az erőforrás szakasz tartalmával kapcsolatos további információkért lásd: [források szakaszában az Azure Resource Manager-sablonok](resource-manager-templates-resources.md).

## <a name="outputs"></a>Kimenetek

A kimeneti szakaszban adjon meg értékeket, amelyek a központi telepítés rendszer adja vissza. Értékek általában üzembe helyezett erőforrásokból visszaadása.

### <a name="available-properties"></a>Rendelkezésre álló tulajdonságok

A következő példa egy kimeneti definíciót szerkezetét mutatja:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| outputName |Igen |A kimeneti érték neve. Érvényes JavaScript-azonosítónak kell lennie. |
| feltétel |Nem | Logikai érték, amely azt jelzi, hogy ez a kimeneti értéket adja vissza. Amikor `true`, az érték szerepel a kimenet a központi telepítéshez. Amikor `false`, a rendszer kihagyta a kimeneti értéket ehhez a központi telepítéshez. Ha nincs megadva, az alapértelmezett értéke `true`. |
| type |Igen |A kimeneti érték típusát. Sablon bemeneti paraméterként azonos kimeneti értékeket támogatásához. |
| érték |Igen |Sablonnyelv-kifejezés, amely értékeli ki és adja vissza a kimeneti értéket. |

### <a name="define-and-use-output-values"></a>Definiálja és kimeneti értékeket

Az alábbi példa bemutatja, hogyan állítható vissza a nyilvános IP-cím erőforrás-azonosító:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

A következő példa bemutatja, hogyan feltételesen a nyilvános IP-cím erőforrás-azonosító alapján egy új egyik telepítve lett-e vissza:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Egy egyszerű példa feltételes kimenet: [feltételes kimeneti sablon](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Az üzembe helyezés után az érték-parancsfájllal kérheti le. PowerShell esetén használja az alábbi parancsot:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Kérheti le a kimeneti értéket egy hivatkozott sablonnak a használatával a [referencia](resource-group-template-functions-resource.md#reference) függvény. Egy kimeneti értéket egy hivatkozott sablonnak a lekéréséhez a szintaxissal tulajdonság értékét a vizualizációhoz: `"[reference('deploymentName').outputs.propertyName.value]"`.

Amikor egy kimeneti tulajdonság lekérése egy hivatkozott sablonnak, a tulajdonság neve nem tartalmazhatja az kötőjellel.

Az alábbi példa bemutatja, hogyan az IP-cím beállítása az egy terheléselosztóhoz társított sablonból értéket lekérésével.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Nem használhatja a `reference` függvény kimenetek szakaszában egy [beágyazott sablont](resource-group-linked-templates.md#link-or-nest-a-template). Az értékeket egy üzembe helyezett erőforrás visszaadása egy beágyazott sablont, váltson egy hivatkozott sablonnak a beágyazott sablont.

### <a name="output-example-templates"></a>Kimeneti példa sablonok

|Sablon  |Leírás  |
|---------|---------|
|[Másolja a változók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Komplex változók hoz létre, és kiírja ezeket az értékeket. Nem telepíti az erőforrásokat. |
|[Nyilvános IP-cím](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Létrehoz egy nyilvános IP-címet, és kiírja az erőforrás-azonosítója. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Az előző sablon mutató hivatkozásokat tartalmaz. A terheléselosztó létrehozásakor használja a kimenetben az erőforrás-azonosítója. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Megjegyzések és metaadatok

Pár megjegyzéseket és metaadatok hozzáadása a sablonhoz, lehetősége van.

Hozzáadhat egy `metadata` objektum szinte bárhonnan a sablonban. Erőforrás-kezelő figyelmen kívül hagyja az objektum, de a JSON-szerkesztőt is figyelmezteti, hogy a tulajdonság nem érvényes. Az objektum határoz meg a szükséges tulajdonságokat.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

A **paraméterek**, adjon hozzá egy `metadata` rendelkező objektum egy `description` tulajdonság.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

A portálon keresztül a sablon üzembe helyezésekor, a szöveget a leírást meg automatikusan használják tipp: arra a paraméterre vonatkozóan.

![A paraméter tipp megjelenítése](./media/resource-group-authoring-templates/show-parameter-tip.png)

A **erőforrások**, adjon hozzá egy `comments` elem vagy metaadat-objektum. Az alábbi példa bemutatja egy megjegyzések elem és a egy metaadat-objektum.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

A **kimenete**, a kimeneti érték hozzáadása egy metaadat-objektum.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Felhasználó által definiált függvények metaadat-objektum nem lehet hozzáadni.

A beágyazott megjegyzések, használhatja `//` , de ez a szintaxis nem működik az összes eszköz. Azure CLI-vel való helyezze üzembe a sablont a beágyazott megjegyzések nem használható. És nem használható a portál sablon szerkesztő dolgozhat a beágyazott megjegyzések-sablonok. Ha ezen comment stílusát, ügyeljen arra, hogy támogatja a beágyazott JSON megjegyzések eszközeit.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

A VS Code-ban is beállíthatja a nyelvmód JSON megjegyzésekkel. A beágyazott megjegyzések rendszer már nem érvénytelenként jelölte meg. Az üzemmód módosítása:

1. Nyissa meg a nyelv mód kiválasztása (Ctrl + K M)

1. Válassza ki **megjegyzésekkel JSON**.

   ![Nyelvmód kiválasztása](./media/resource-group-authoring-templates/select-json-comments.png)

## <a name="template-limits"></a>Sablon korlátok

A sablon 1 MB-ra, és minden egyes alkalmazásparaméter-fájlt a 64 KB-os mérete korlátozza. Az 1 MB-os korlát vonatkozik a sablon a végállapot után ki lett terjesztve az iteratív erőforrás-definíciók és a változók és paraméterek értékeit. 

Korlátot is jelent a:

* 256 paraméterek
* 256 változók
* 800-erőforrásokat (például a példányszám)
* 64 kimeneti értékeket
* egy kifejezés 24,576 karakter

Néhány sablon korlát beágyazott sablonok segítségével is lehet. További információkért lásd: [kapcsolt sablonok használata az Azure-erőforrások üzembe helyezésekor](resource-group-linked-templates.md). A paraméterek, a változók és a kimenetek számának csökkentése, több értéket is egyesítendő objektum. További információkért lásd: [paraméterekként objektumok](resource-manager-objects-as-parameters.md).

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>További lépések
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használhatja függvényeivel kapcsolatos részletekért lásd: [Azure Resource Manager-Sablonfüggvények](resource-group-template-functions.md).
* Úgy, hogy számos sablon üzembe helyezése során, tekintse meg a [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Sablonok létrehozásával kapcsolatos ajánlások, lásd: [gyakorlati tanácsok az Azure Resource Manager-sablon](template-best-practices.md).
* Javaslatok, amelyet használhat az összes Azure-környezetek és az Azure Stack Resource Manager-sablonok létrehozásával, lásd: [felhőalapú konzisztencia fejlesztése az Azure Resource Manager-sablonokkal](templates-cloud-consistency.md).
