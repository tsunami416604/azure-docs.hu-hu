---
title: Sablonokban található paraméterek
description: Ismerteti, hogyan lehet paramétereket definiálni egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "76122423"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Paraméterek használata az Azure Resource Manager-sablonokban

Ez a cikk azt ismerteti, hogyan lehet paramétereket definiálni és használni a Azure Resource Manager-sablonban. A paraméterek eltérő értékeinek megadásával más környezetekhez is felhasználhat sablont.

A Resource Manager a telepítési műveletek megkezdése előtt oldja fel a paramétereket. Ha a paramétert használja a sablonban, a Resource Manager lecseréli azt a megoldott értékre.

## <a name="define-parameter"></a>Paraméter definiálása

Az alábbi példa egy egyszerű paraméter-definíciót mutat be. Definiál egy **storageSKU**nevű paramétert. A paraméter egy karakterlánc-érték, és csak a rendeltetésszerű használatra érvényes értékeket fogad el. A paraméter alapértelmezett értéket használ, ha nincs megadva érték az üzembe helyezés során.

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

## <a name="use-parameter"></a>Paraméter használata

A sablonban a [Paraméterek](template-functions-deployment.md#parameters) függvény használatával hivatkozhat a paraméter értékére. A következő példában a paraméter értéke a Storage-fiókhoz tartozó SKU beállítására szolgál.

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

## <a name="template-functions"></a>Sablonfüggvények

Egy paraméter alapértelmezett értékének megadásakor használhatja a legtöbb sablon-függvényt. Az alapértelmezett érték kiépítéséhez használhat egy másik paraméter értékét is. A következő sablon a függvények használatát mutatja be az alapértelmezett értékben. Ha a helyhez nincs megadva név, akkor egy egyedi karakterláncot hoz létre, és hozzáfűzi a **helyhez**. Ha a gazdagép-csomaghoz nincs megadva név, a rendszer a hely értékét és a **-tervet**fűzi hozzá.

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

A paraméterek szakaszban nem használható a [Reference](template-functions-resource.md#reference) függvény vagy a [List](template-functions-resource.md#list) függvény. Ezek a függvények egy erőforrás futásidejű állapotát kapják meg, és az üzembe helyezés előtt nem hajthatók végre a paraméterek feloldásakor.

## <a name="objects-as-parameters"></a>Objektumok paraméterként

A kapcsolódó értékek könnyebben rendezhetők úgy, hogy egy objektumként adják át őket. Ez a módszer a sablonban található paraméterek számát is csökkenti.

A következő példa egy olyan paramétert mutat be, amely egy objektum. Az alapértelmezett érték az objektum várt tulajdonságait jeleníti meg.

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

Az objektum tulajdonságaira az dot operátor használatával hivatkozhat.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
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

## <a name="example-templates"></a>Példák sablonokra

Az alábbi példák a paraméterek használatának forgatókönyveit mutatják be.

|Sablon  |Leírás  |
|---------|---------|
|[az alapértelmezett értékek függvényeit tartalmazó paraméterek](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Bemutatja, hogyan használható a Template functions a paraméterek alapértelmezett értékeinek definiálásához. A sablon nem telepít semmilyen erőforrást. Paraméterek értékeit állítja össze, és visszaadja ezeket az értékeket. |
|[paraméter objektum](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Egy paraméter objektumának használatát mutatja be. A sablon nem telepít semmilyen erőforrást. Paraméterek értékeit állítja össze, és visszaadja ezeket az értékeket. |


## <a name="next-steps"></a>További lépések

* A paraméterek elérhető tulajdonságainak megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md)című témakört.
* A paraméterek értékeinek fájlként való átadásáról a [Resource Manager-paraméterérték létrehozása](parameter-files.md)című témakörben olvashat bővebben.
* A paraméterek létrehozásával kapcsolatos javaslatokért lásd: [ajánlott eljárások – paraméterek](template-best-practices.md#parameters).
