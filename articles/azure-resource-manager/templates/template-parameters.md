---
title: Paraméterek a sablonokban
description: Ez a témakör azt ismerteti, hogy miként definiálható paraméterek egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 89c6984c587e8dae59c1825a99d4f8da1c06dafb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122423"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Paraméterek az Azure Resource Manager-sablonokban

Ez a cikk ismerteti, hogyan definiálhatja és használhatja a paramétereket az Azure Resource Manager sablonban. A paraméterek különböző értékeinek megadásával újra felhasználhatja a sablont a különböző környezetekben.

Az Erőforrás-kezelő a telepítési műveletek megkezdése előtt feloldja a paraméterértékeket. Ahol a paramétert használja a sablonban, az Erőforrás-kezelő lecseréli azt a feloldott értékre.

## <a name="define-parameter"></a>Paraméter definiálása

A következő példa egy egyszerű paraméterdefiníciót mutat be. Ez határozza meg a paraméter nevű **storageSKU.** A paraméter karakterlánc-érték, és csak a tervezett használatra érvényes értékeket fogadja el. A paraméter alapértelmezett értéket használ, ha a központi telepítés során nem ad meg értéket.

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

A sablonban a paraméter értékére hivatkozik a [paraméterek](template-functions-deployment.md#parameters) függvény használatával. A következő példában a paraméter érték a tárfiók termékváltozatának beállításához használatos.

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

Egy paraméter alapértelmezett értékének megadásakor a legtöbb sablonfüggvényt használhatja. Az alapértelmezett érték létrehozásához használhat másik paraméterértéket. A következő sablon bemutatja a függvények használatát az alapértelmezett értékben. Ha nincs megadva név a webhelyhez, egyedi karakterlánc-értéket hoz létre, és hozzáfűzi a **webhelyhez.** Ha nincs megadva név a gazdacsomaghoz, akkor a webhely értékét veszi fel, és hozzáfűzi **a -plan**értéket.

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

A [hivatkozási](template-functions-resource.md#reference) függvény vagy a paraméterek szakaszban lévő [listafüggvények](template-functions-resource.md#list) nem használhatók. Ezek a függvények egy erőforrás futásidejű állapotát kapják, és nem hajthatók végre a telepítés előtt, ha a paraméterek feloldásakor.

## <a name="objects-as-parameters"></a>Objektumok paraméterként

A kapcsolódó értékeket könnyebb enként rendszerezni lehet, ha átadjuk őket objektumként. Ez a megközelítés a sablonban lévő paraméterek számát is csökkenti.

A következő példa egy objektumparamétert mutat be. Az alapértelmezett érték az objektum várt tulajdonságait jeleníti meg.

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

Az objektum tulajdonságaira a pontoperátor használatával hivatkozik.

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

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák bemutatják a paraméterek használatának forgatókönyveit.

|Sablon  |Leírás  |
|---------|---------|
|[paraméterek az alapértelmezett értékek függvényeivel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Bemutatja, hogyan használható sablonfüggvények a paraméterek alapértelmezett értékeinek definiálásakor. A sablon nem telepít erőforrásokat. Paraméterértékeket hoz létre, és ezeket az értékeket adja vissza. |
|[paraméterobjektum](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Egy paraméter objektumának használatát mutatja be. A sablon nem telepít erőforrásokat. Paraméterértékeket hoz létre, és ezeket az értékeket adja vissza. |


## <a name="next-steps"></a>További lépések

* A paraméterek elérhető tulajdonságairól az [Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](template-syntax.md)
* A paraméterértékek fájlként való átadásáról az [Erőforrás-kezelő paraméterfájl létrehozása című](parameter-files.md)témakörben olvashat.
* A paraméterek létrehozásával kapcsolatos javaslatokról az Ajánlott eljárások – paraméterek című [témakörben van.](template-best-practices.md#parameters)
