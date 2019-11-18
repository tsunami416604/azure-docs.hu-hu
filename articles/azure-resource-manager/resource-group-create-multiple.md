---
title: Több erőforrás-példány üzembe helyezése
description: A másolási művelet és tömbök használata Azure Resource Manager sablonban több alkalommal is megismételhető az erőforrások telepítésekor.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 3ee4b47dd6cb9043a4100d114c483d1feadbde38
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150802"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Erőforrás, tulajdonság vagy változó iteráció Azure Resource Manager-sablonokban

Ebből a cikkből megtudhatja, hogyan hozhat létre egy erőforrás, változó vagy tulajdonság egynél több példányát a Azure Resource Manager-sablonban. Több példány létrehozásához adja hozzá a `copy` objektumot a sablonhoz.

Ha erőforrással használja, a másolási objektum formátuma a következő:

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

Ha változót vagy tulajdonságot használ, a másolási objektum formátuma a következő:

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

A jelen cikk részletesebben ismerteti mindkét felhasználási leírást. Oktatóanyagért lásd [: oktatóanyag: több erőforrás-példány létrehozása Resource Manager-sablonok használatával](./resource-manager-tutorial-create-multiple-instances.md).

Ha meg kell adnia, hogy az erőforrás telepítve van-e, tekintse meg a [feltétel elemet](conditional-resource-deployment.md).

## <a name="copy-limits"></a>Másolási korlátok

Az ismétlések számának megadásához adja meg a Count tulajdonság értékét. A szám nem lehet nagyobb, mint 800.

A darabszám nem lehet negatív szám. Ha Azure PowerShell 2,6-as vagy újabb, Azure CLI-2.0.74 vagy újabb verzióval, REST API vagy a **2019-05-10** -es vagy újabb verziójával rendelkező sablont telepít, akkor a Count értéket nullára állíthatja. A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nulla értéket a darabszámhoz.

Ügyeljen arra, hogy a [teljes telepítési mód](deployment-modes.md) legyen a másolással. Ha egy erőforráscsoport esetében újratelepíti a teljes módot, a másolási hurok feloldása után a sablonban nem megadott erőforrások törlődnek.

A darabszám korlátja azonos, ha erőforrással, változóval vagy tulajdonsággal van használatban.

## <a name="resource-iteration"></a>Erőforrás-iteráció

Ha egy központi telepítésben egynél több példányt szeretne létrehozni egy adott erőforráshoz, adjon hozzá egy `copy` elemet az erőforrás típushoz. A másolás elemnél adja meg az ismétlések számát és a hurok nevét.

A több alkalommal létrehozandó erőforrás a következő formátumot veszi figyelembe:

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

Figyelje meg, hogy az egyes erőforrások neve tartalmazza a `copyIndex()` függvényt, amely az aktuális iterációt adja vissza a hurokban. A `copyIndex()` nulla alapú. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex())]",
```

A következő neveket hozza létre:

* storage0
* storage1
* storage2.

Az indexérték eltolásához megadhat egy értéket a copyIndex() függvényben. Az ismétlések száma továbbra is meg van adva a másolási elemben, de a copyIndex értéke a megadott értékkel van ellensúlyozva. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex(1))]",
```

A következő neveket hozza létre:

* storage1
* storage2
* storage3

A másolási művelet hasznos lehet a tömbök használatakor, mert a tömb minden elemén megismételhető. Használja a tömb `length` függvényét az iterációk számának megadásához, és `copyIndex` a tömb aktuális indexének lekéréséhez. Tehát a következő példa:

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

A következő neveket hozza létre:

* storagecontoso
* storagefabrikam
* storagecoho

Alapértelmezés szerint a Resource Manager párhuzamosan hozza létre az erőforrásokat. Nem korlátozza a párhuzamosan üzembe helyezett erőforrások számát, kivéve a sablonban lévő 800-erőforrások teljes korlátját. A létrehozásuk sorrendje nem garantált.

Azonban érdemes megadnia, hogy az erőforrások sorba legyenek telepítve. Ha például éles környezetet frissít, érdemes lehet megosztani a frissítéseket, hogy csak egy adott szám legyen frissítve egyszerre. Egy erőforrás egynél több példányának soros üzembe helyezéséhez állítsa a `mode` értéket **sorosra** , és `batchSize` az egyszerre telepítendő példányok számára. A soros módban a Resource Manager egy függőséget hoz létre a hurok korábbi példányain, így nem indít el egy köteget, amíg az előző köteg be nem fejeződik.

Ha például a Storage-fiókokat egyszerre két sorba szeretné telepíteni, használja a következőt:

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

A Mode (mód) tulajdonság szintén **párhuzamosan**fogadja el az alapértelmezett értéket.

További információ a másolás beágyazott sablonokkal történő használatáról: [a másolás használata](resource-group-linked-templates.md#using-copy).

## <a name="property-iteration"></a>Tulajdonság iterációja

Ha egynél több értéket szeretne létrehozni egy erőforrás egy tulajdonságához, adjon hozzá egy `copy` tömböt a Properties (Tulajdonságok) elemben. Ez a tömb objektumokat tartalmaz, és minden objektum a következő tulajdonságokkal rendelkezik:

* név – a tulajdonság neve, amelynek több értékét kell létrehoznia
* Darabszám – a létrehozandó értékek száma.
* bemenet – egy objektum, amely a tulajdonsághoz rendelendő értékeket tartalmazza.

Az alábbi példa bemutatja, hogyan alkalmazhatja a `copy`t a virtuális gép dataDisks tulajdonságára:

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

Figyelje meg, hogy amikor `copyIndex` használ a tulajdonságok iterációjában, meg kell adnia az iteráció nevét. A nevet nem kell megadnia az erőforrás-iterációval való használathoz.

A Resource Manager kibővíti a `copy` tömböt az üzembe helyezés során. A tömb neve lesz a tulajdonság neve. A bemeneti értékek az objektum tulajdonságai lesznek. A központilag telepített sablon a következőket válik:

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

A másolási elem egy tömb, így több tulajdonság is megadható az erőforráshoz. Adjon hozzá egy objektumot a létrehozandó egyes tulajdonságokhoz.

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

Az erőforrások és a tulajdonságok ismétlését együtt is használhatja. Hivatkozzon a tulajdonság iterációra név alapján.

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

Egy változó több példányának létrehozásához használja a változók szakaszban található `copy` tulajdonságot. A `input` tulajdonság értéke alapján létrehozott elemek tömbjét hozza létre. Használhatja a `copy` tulajdonságot egy változón belül, vagy a változók szakasz legfelső szintjén. Ha a `copyIndex` változót használ egy iterációban, meg kell adnia az iteráció nevét.

A karakterlánc-értékek tömbje létrehozásának egyszerű példáját lásd: [Array sablon másolása](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

Az alábbi példa több különböző módszert mutat be a dinamikusan kiépített elemekből álló tömbök változók létrehozására. Bemutatja, hogyan használható a másolás egy változóban az objektumok és karakterláncok tömbje létrehozásához. Azt is bemutatja, hogyan használhatók a legfelső szintű másolási objektumok, karakterláncok és egész számok tömbje.

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

A létrehozott változó típusa a bemeneti objektumtól függ. Például az előző példában szereplő **felső szintű objektum-tömb** nevű változó a következőt adja vissza:

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

És a **felső szintű karakterlánc-tömb** nevű változó a következőket adja vissza:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Egy hurok erőforrásaitól függ

Azt határozza meg, hogy egy erőforrás központi telepítése egy másik erőforrás után történik a `dependsOn` elem használatával. Ha olyan erőforrást szeretne üzembe helyezni, amely egy hurokban lévő erőforrások gyűjteményéből függ, adja meg a másolási hurok nevét a dependsOn elemben. Az alábbi példa bemutatja, hogyan helyezhet üzembe három Storage-fiókot a virtuális gép telepítése előtt. A virtuális gép teljes definíciója nem jelenik meg. Figyelje meg, hogy a másolási elem neve `storagecopy`, és a Virtual Machines dependsOn eleme is `storagecopy`értékre van állítva.

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

## <a name="iteration-for-a-child-resource"></a>Gyermek erőforrás iterációja
Alárendelt erőforráshoz nem használhat másolási hurkot. Ha egy erőforrás több példányát szeretné létrehozni, amelyet általában egy másik erőforrásban ágyaznak be, ehelyett legfelső szintű erőforrásként kell létrehoznia az erőforrást. A szülő erőforrással létesített kapcsolatot a típus és a név tulajdonsággal határozhatja meg.

Tegyük fel például, hogy az adatkészletet általában alárendelt erőforrásként definiálja egy adat-előállítón belül.

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

Egynél több adathalmaz létrehozásához helyezze át azt az adatelőállítón kívülre. Az adatkészletnek a adat-előállítóval megegyező szinten kell lennie, de még mindig az adat-előállító alárendelt erőforrása. Az adatkészletek és a adatfeldolgozók közötti kapcsolatot a típus és a név tulajdonságon keresztül megőrizheti. Mivel a típus már nem következtethető ki a sablonban lévő pozícióból, meg kell adnia a teljes típust a következő formátumban: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Ha szülő/gyermek kapcsolatot szeretne létesíteni az adatelőállító egy példányával, adja meg a szülő erőforrás nevét tartalmazó adatkészlet nevét. Használja a formátumot: `{parent-resource-name}/{child-resource-name}`.

A következő példa a megvalósítást mutatja be:

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

Az alábbi példák egy erőforrás vagy tulajdonság egynél több példányának létrehozására vonatkozó gyakori forgatókönyveket mutatnak be.

|Sablon  |Leírás  |
|---------|---------|
|[Tárterület másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Egynél több Storage-fiókot telepít a névben. |
|[Soros másolási tár](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Egyszerre több Storage-fiókot helyez üzembe. A név tartalmazza az index számát. |
|[Tároló másolása tömbvel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Több Storage-fiók üzembe helyezése. A név egy tömbből származó értéket tartalmaz. |
|[VM-telepítés változó számú adatlemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Több adatlemez üzembe helyezése virtuális géppel. |
|[Másolja a változók](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Bemutatja a változók megismétlésének különböző módszereit. |
|[Több biztonsági szabály](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Több biztonsági szabályt telepít egy hálózati biztonsági csoportra. A biztonsági szabályokat egy paraméter alapján hozza létre. A paraméternél tekintse meg a [több NSG-paramétert tartalmazó fájlt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>További lépések

* Az oktatóanyag lépéseinek megismeréséhez lásd [: oktatóanyag: több erőforrás-példány létrehozása Resource Manager-sablonok használatával](./resource-manager-tutorial-create-multiple-instances.md).

* Ha szeretné megismerni a sablonok egyes részeit, olvassa el a [Azure Resource Manager sablonok készítése](resource-group-authoring-templates.md)című szakaszt.
* A sablon üzembe helyezésével kapcsolatos további információkért lásd: [alkalmazások központi telepítése Azure Resource Manager sablonnal](resource-group-template-deploy.md).

