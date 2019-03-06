---
title: Több példány Azure-erőforrások üzembe helyezése |} A Microsoft Docs
description: A másolási műveletek és tömbök használata az Azure Resource Manager-sablon újrafuttathatja többször erőforrások üzembe helyezésekor.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: c60983dbbe72515fd8f0f4860e169ce1ba69ed45
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407085"
---
# <a name="deploy-more-than-one-instance-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Egynél több példányát egy erőforrást vagy tulajdonság frissítése az Azure Resource Manager-sablonok üzembe helyezése

Ez a cikk bemutatja, hogyan pedig ismétlést állítunk be az Azure Resource Manager-sablon egynél több példányának erőforrás létrehozásához. Ha meg kell adnia e egy erőforrás üzembe van helyezve egyáltalán, lásd: [feltétel elem](resource-group-authoring-templates.md#condition).

Foglalkozó oktatóanyagért lásd: [oktatóanyag: létrehozása a Resource Manager-sablonokkal több erőforráspéldány létrehozásával](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="resource-iteration"></a>Erőforrás iteráció

Ha egy vagy több erőforrás-példányok létrehozásához, el kell döntenie üzembe helyezés során, adjon hozzá egy `copy` elem az erőforrás típusát. A másolási elemben adja meg a ciklus nevét és iterációinak számát. A hibaszám értéke pozitív egész számnak kell lennie, és nem lehet több mint 800. 

Az erőforrás létrehozandó többször fogadja a következő formátumban:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ],
  "outputs": {}
}
```

Figyelje meg, hogy az egyes erőforrások a neve tartalmazza a `copyIndex()` függvény, amely az aktuális iteráció a hurok adja vissza. A `copyIndex()` nulla alapú. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex())]",
```

Hozza létre ezeket a neveket:

* storage0
* storage1
* storage2.

Az indexérték eltolásához megadhat egy értéket a copyIndex() függvényben. Végrehajtásához az ismétlések száma továbbra is a másolási eleme van megadva, de copyIndex értékét ellensúlyozza a megadott értéket. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Hozza létre ezeket a neveket:

* storage1
* storage2
* storage3

A másolási művelet akkor hasznos, ha tömbök dolgozik, mert Ön is iterálódnak a tömb egyes elemei. Használja a `length` a tömbben, az ismétlések számát adja meg a függvény és `copyIndex` lekérni az aktuális index a tömb. Tehát a következő példa:

```json
"parameters": { 
  "org": { 
    "type": "array", 
    "defaultValue": [ 
      "contoso", 
      "fabrikam", 
      "coho" 
    ] 
  }
}, 
"resources": [ 
  { 
    "name": "[concat('storage', parameters('org')[copyIndex()])]", 
    "copy": { 
      "name": "storagecopy", 
      "count": "[length(parameters('org'))]" 
    }, 
    ...
  } 
]
```

Hozza létre ezeket a neveket:

* storagecontoso
* storagefabrikam
* storagecoho

Alapértelmezés szerint a Resource Manager párhuzamosan létrehozza az erőforrásokat. A sorrend, amelyben jönnek létre nem garantált. Azonban érdemes adja meg, hogy az erőforrások telepítése feladatütemezés. Például amikor frissíti a termelési környezetben, érdemes tehát ütemtervét a frissítések egyszerre csak egy bizonyos számú frissülnek.

Sorosan helyezze üzembe az erőforrás egynél több példányát, állítsa `mode` való **soros** és `batchSize` egyszerre telepítendő példányok száma. Soros üzemmódot erőforrás-kezelő függőséget hoz létre a hurok korábbi példányain, az egy kötegben nem indul el addig, amíg befejeződik az előző köteg.

Ha például tárolókonfigurációhoz egyszerre két storage-fiókok üzembe helyezéséhez, használja:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      }
    }
  ],
  "outputs": {}
}
```

A mód tulajdonságot is fogad **párhuzamos**, amelynek alapértelmezett értéke.

## <a name="property-iteration"></a>A tulajdonság az iteráció

Több érték a tulajdonság az erőforrás létrehozásához adja hozzá a `copy` a Tulajdonságok elem a tömbben. Ezt a tömböt tartalmaz objektumokat, és minden objektum rendelkezik a következő tulajdonságokkal:

* név – hozhat létre több értékeit a tulajdonság neve
* szám – a létrehozni kívánt értékek száma. A hibaszám értéke pozitív egész számnak kell lennie, és nem lehet több mint 800.
* bemenet - olyan objektum, amely a tulajdonság hozzárendelése értékeket tartalmazza.  

Az alábbi példa bemutatja, hogyan alkalmazhatja a `copy` dataDisks tulajdonsághoz, a virtuális gépen:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

Figyelje meg, hogy használatakor `copyIndex` egy tulajdonság iteráció belül meg kell adnia az iteráció nevét. Nincs erőforrás iteráció együtt használva a nevét.

Erőforrás-kezelő bontja ki a `copy` array üzembe helyezése során. A tömb neve lesz a tulajdonság nevét. A bemeneti értékek válik az objektum tulajdonságait. Az üzembe helyezett sablon válik:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

A másolási elem egy tömb így megadhatja, hogy egynél több tulajdonság az erőforrás. Adjon hozzá egy objektumot, minden egyes tulajdonság létrehozásához.

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Erőforrás- és az iteráció együtt használhatja. A tulajdonság az iteráció hivatkozni nevét.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="variable-iteration"></a>Változó iteráció

Egy változó több példány létrehozásához használja a `copy` tulajdonság a változók szakaszban. Létrehozhat értéke értékekből összeállított elemek tömbjét a `input` tulajdonság. Használhatja a `copy` tulajdonság egy változóban, vagy a változók szakaszban, a legfelső szinten. Használata esetén `copyIndex` egy változó iteráció belül meg kell adnia az iteráció nevét.

Karakterlánc-értékek tömbje létrehozásának egy egyszerű példa: [tömb sablon másolása](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

Az alábbi példa a tömbváltozók dinamikusan létrehozott elemek létrehozása számos különböző módszert mutat be. Ez bemutatja, hogyan belül egy változó másolási tömbök sztringek és objektumok létrehozásához használja. Azt is bemutatja, hogyan másolás használata a legfelső szinten objektumok, karakterláncok és egész számok tömbök létrehozására.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

Milyen típusú változó, amely jön létre a bemeneti objektum függ. Ha például a következő változót **top-szintű-objektum-tömb** adja vissza az előző példában:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

És a következő változót **top-szint – karakterlánc-tömbben** adja vissza:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Függő erőforrások hurokba került

Megadhatja, hogy egy erőforrás üzembe van helyezve egy másik erőforrás után használatával a `dependsOn` elemet. Egy erőforrás, amely attól függ, a gyűjtemény-erőforrások egy hurokba, és központi telepítéséhez adja meg a másolási ciklust a dependsOn elem nevét. Az alábbi példa bemutatja, hogyan üzembe helyezése három tárfiókot tartalmaz a virtuális gép üzembe helyezése előtt. A teljes virtuális gép definíciója nem jelenik meg. Figyelje meg, hogy a másolási eleme rendelkezik-e a névhez a `storagecopy` és a virtuális gépek a dependsOn elem értéke is `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    },
    {
      "apiVersion": "2015-06-15", 
      "type": "Microsoft.Compute/virtualMachines", 
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Iteráció egy gyermek-erőforrás
A másolási ciklust egy gyermek-erőforrás nem használható. Általában megadhat mint belül egy másik erőforrás beágyazott erőforrás egynél több példányának létrehozása, ennek az erőforrásnak inkább hozzon létre egy legfelső szintű erőforrásként. Meghatározhatja a szülő erőforrás típusa és neve tulajdonságai rendelkező kapcsolatot.

Tegyük fel például, alárendelt erőforrásként adat-előállító belül általában meghatároz egy adatkészletet.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Szeretne létrehozni egynél több adatkészlet, helyezze át a data factory-en kívül. Az adatkészletet a data factory ugyanazon a szinten kell lennie, de továbbra is egy gyermek-erőforrás a data Factory. Akkor őrzi meg az adatkészlet és a data factory típus és név tulajdonságai közötti kapcsolat. Mivel a típus már nem lehet következtetni a sablonban helyéről, meg kell adnia a teljesen minősített írja be a következő formátumban: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Az adat-előállító egy példányát a szülő-gyermek kapcsolat létrehozására, nevezze el az adatkészlet, amely tartalmazza a szülő erőforrás nevét. Használja a következő formátumot: `{parent-resource-name}/{child-resource-name}`.  

Az alábbi példa bemutatja a végrehajtása:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák bemutatják a gyakori helyzetek az erőforrás vagy tulajdonság egynél több példány létrehozása.

|Sablon  |Leírás  |
|---------|---------|
|[Tároló](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Több tárfiók nevében indexszámmal telepíti. |
|[Soros tároló](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Üzembe helyezi a különböző storage-fiókok egy időben. A név tartalmaz indexszámát. |
|[A tömb storage másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Különböző storage-fiókok üzembe helyezi. A név egy tömb értéket tartalmaz. |
|[Az adatlemezek változó számú virtuális gép üzembe helyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Üzembe helyez egy virtuális gép több adatlemezének. |
|[Másolja a változók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |A különböző módszert is változókra léptetés mutatja be. |
|[Több biztonsági szabályok](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |A hálózati biztonsági csoport több biztonsági szabályokat telepíti. Akkor hoz létre a biztonsági szabályok a paraméterből származó. Tekintse meg a paraméter [több NSG-paraméterfájl](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>További lépések

* Go-oktatóanyagot, tekintse meg [oktatóanyag: létrehozása a Resource Manager-sablonokkal több erőforráspéldány létrehozásával](./resource-manager-tutorial-create-multiple-instances.md).

* Ha szeretne további információ a szakaszokat, [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Ismerje meg, hogyan helyezze üzembe a sablont, tekintse meg [alkalmazás üzembe helyezése az Azure Resource Manager-sablon](resource-group-template-deploy.md).

