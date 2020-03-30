---
title: Erőforrások több példányának telepítése
description: Az Azure Resource Manager-sablonban lévő másolási művelet és tömbök használatával többször üzembe helyezheti az erőforrástípust.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e65ab93c21daffa0053e53d953fe95fa9f28e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153318"
---
# <a name="resource-iteration-in-arm-templates"></a>Erőforrás-ismétlés ARM-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egy nél több erőforrás-példányt az Azure Resource Manager (ARM) sablonban. Ha hozzáadja a **másolási** elemet a sablon erőforrásszakaszában, dinamikusan beállíthatja az üzembe helyezandó erőforrások számát. Azt is elkerülheti, hogy meg kell ismételnie a sablon szintaxisát.

A [másolástulajdonságokkal](copy-properties.md), [változókkal](copy-variables.md) és [kimenetekkel](copy-outputs.md)is használható.

Ha meg kell adnia, hogy egy erőforrás egyáltalán telepítve van-e, olvassa el a [feltételelemet.](conditional-resource-deployment.md)

## <a name="resource-iteration"></a>Erőforrás-ismétlés

A másolási elem általános formátuma a következő:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

A **name** tulajdonság bármely olyan érték, amely azonosítja a hurkot. A **Count** tulajdonság az erőforrástípushoz kívánt ismétlések számát adja meg.

A **mode** és **a batchSize** tulajdonságok segítségével megadhatja, hogy az erőforrások párhuzamosan vagy egymás után legyenek telepítve. Ezeket a tulajdonságokat a [Soros vagy a Párhuzamos](#serial-or-parallel)című részben ismertetik.

A következő példa a **storageCount** paraméterben megadott tárfiókok számát hozza létre.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

Figyelje meg, hogy az `copyIndex()` egyes erőforrások neve tartalmazza a függvényt, amely a ciklus aktuális iterációját adja vissza. A `copyIndex()` nulla alapú. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex())]",
```

A következő neveket hozza létre:

* tárolás0
* tárolás1
* tároló2.

Az indexérték eltolásához megadhat egy értéket a copyIndex() függvényben. Az ismétlések száma még mindig meg van adva a másolási elemben, de a copyIndex értékét a megadott érték ellensúlyozza. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex(1))]",
```

A következő neveket hozza létre:

* tárolás1
* tárolás2
* tárolás3

A másolási művelet akkor hasznos, ha tömbökkel dolgozik, mert a tömb minden elemén végighaladhat. A `length` tömb függvényével adja meg az ismétlések számát, és `copyIndex` olvassa el a tömb aktuális indexét.

A következő példa egy tárfiókot hoz létre a paraméterben megadott minden névhez.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

Ha az üzembe helyezett erőforrásokból szeretne értékeket visszaadni, a [kimenetek szakaszban használhatja a másolást.](copy-outputs.md)

## <a name="serial-or-parallel"></a>Soros vagy párhuzamos

Alapértelmezés szerint az Erőforrás-kezelő párhuzamosan hozza létre az erőforrásokat. Nem korlátozza a párhuzamosan üzembe helyezett erőforrások számát, kivéve a sablonban lévő 800 erőforrás teljes korlátját. A létrehozásuk sorrendje nem garantált.

Előfordulhat azonban, hogy meg szeretné adni, hogy az erőforrások egymás után legyenek telepítve. Éles környezet frissítésekor például érdemes lehet a frissítéseket eltántorítása, hogy egyszerre csak egy bizonyos szám frissüljön. Egy erőforrás több példányának sorozatos üzembe `mode` helyezéséhez `batchSize` állítsa be **sorosra** és az egyszerre telepítandó példányok számára. Soros módban az Erőforrás-kezelő függőséget hoz létre a ciklus korábbi példányaitól, így nem indít el egy köteget, amíg az előző köteg be nem fejeződik.

Ha például egyszerre kettővel szeretné üzembe helyezni a tárfiókokat, használja a következőket:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

A mode tulajdonság **párhuzamos**, az alapértelmezett érték is elfogadja.

## <a name="depend-on-resources-in-a-loop"></a>Függ a hurok erőforrásaitól

Megadhatja, hogy egy erőforrás egy másik `dependsOn` erőforrás után kerül-e üzembe helyezésre az elem használatával. Ha olyan erőforrást szeretne üzembe helyezni, amely egy ciklusban lévő erőforrások gyűjtésétől függ, adja meg a másolási hurok nevét a dependsOn elemben. A következő példa bemutatja, hogyan telepíthet három tárfiókot a virtuális gép üzembe helyezése előtt. A teljes virtuálisgép-definíció nem jelenik meg. Figyelje meg, hogy a `storagecopy` másolási elem neve van beállítva, `storagecopy`és a dependsOn elem a virtuális gép is be van állítva .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Gyermekerőforrás ismétlése

Gyermekerőforráshoz nem használhat másolási hurkot. Ha egy erőforrásból egynél több olyan példányt szeretne létrehozni, amelyet általában egy másik erőforrásba ágyazottként definiál, az erőforrást legfelső szintű erőforrásként kell létrehoznia. A szülőerőforrással való kapcsolatot a típus- és névtulajdonságokon keresztül határozhatja meg.

Tegyük fel például, hogy egy adatkészletet általában gyermekerőforrásként definiál egy adat-előállítón belül.

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

Ha egynél több adatkészletet szeretne létrehozni, helyezze át az adat-előállítón kívülre. Az adatkészletnek ugyanazon a szinten kell lennie, mint az adat-előállító, de ez még mindig az adat-előállító gyermekerőforrás. Az adatkészlet és az adat-előállító közötti kapcsolatot a típus- és névtulajdonságokon keresztül őrizheti meg. Mivel a típus már nem következtethető ki a sablonban elfoglalt helyéből, a teljesen minősített típust a következő formátumban kell megadnia: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Ha szülő-gyermek kapcsolatot szeretne létesíteni az adat-előállító egy példányával, adjon meg egy nevet a szülőerőforrás nevét tartalmazó adatkészletnek. Használja a következő formátumot: `{parent-resource-name}/{child-resource-name}`.

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

## <a name="copy-limits"></a>Másolási korlátok

A szám nem haladhatja meg a 800-at.

A szám nem lehet negatív szám. Ha az Azure PowerShell 2.6-os vagy újabb, az Azure CLI 2.0.74-es vagy újabb, illetve a REST API **2019-05-10-es** vagy újabb verziójával telepít egy sablont, beállíthatja a számlálót nullára. A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nullát a számláláshoz.

Legyen óvatos a [teljes mód ú telepítés](deployment-modes.md) másolással. Ha teljes módban újratelepíti egy erőforráscsoportba, a másolási ciklus feloldása után a sablonban nem megadott erőforrások törlődnek.

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák az erőforrás vagy tulajdonság egynél több példányának létrehozására vonatkozó gyakori forgatókönyveket mutatják be.

|Sablon  |Leírás  |
|---------|---------|
|[Tároló másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Több tárfiókot telepít egy indexszámmal a névben. |
|[Soros másolat tárolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Egyszerre több tárfiókot telepít. A név tartalmazza az indexszámot. |
|[Tároló másolása tömbbel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Több tárfiók telepítése. A név egy tömbből származó értéket tartalmaz. |
|[Virtuális gép telepítése változó számú adatlemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Több adatlemez központi telepítése egy virtuális géppel. |
|[Több biztonsági szabály](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Több biztonsági szabályt telepít egy hálózati biztonsági csoportra. A biztonsági szabályokat egy paraméterből építi fel. A paramétert több [NSG paraméterfájlban láthatja.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) |

## <a name="next-steps"></a>További lépések

* Ha egy oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Több erőforráspéldány létrehozása ARM-sablonokkal című témakört.](template-tutorial-create-multiple-instances.md)
* A másolási elem egyéb felhasználási ideje:
  * [Tulajdonság ismétlése ARM sablonokban](copy-properties.md)
  * [Változó iteráció arm sablonokban](copy-variables.md)
  * [Kimeneti ismétlés ARM sablonokban](copy-outputs.md)
* A másolás beágyazott sablonokkal való használatáról a [Másolás használata című témakörben](linked-templates.md#using-copy)olvashat.
* Ha a sablonok szakaszairól szeretne többet megtudni, olvassa el [az ARM-sablonok készítése című témakört.](template-syntax.md)
* A sablon üzembe helyezéséről az [Alkalmazás telepítése ARM sablonnal (Alkalmazás telepítése ARM sablonnal) témakörben](deploy-powershell.md)olvashat.

