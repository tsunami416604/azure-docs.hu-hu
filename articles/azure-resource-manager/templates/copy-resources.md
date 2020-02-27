---
title: Több erőforrás-példány üzembe helyezése
description: A másolási művelet és tömbök használata Azure Resource Manager sablonban az erőforrástípus többszöri üzembe helyezéséhez.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e90673504ceaccdc25a477e856defa77eed37d86
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620221"
---
# <a name="resource-iteration-in-azure-resource-manager-templates"></a>Erőforrás-iteráció Azure Resource Manager-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egy erőforrás több példányát a Azure Resource Manager-sablonban. Ha hozzáadja a **Másolás** elemet a sablon erőforrások szakaszához, akkor dinamikusan beállíthatja a telepítendő erőforrások számát. Emellett ne kelljen megismételni a sablon szintaxisát.

A másolást a [Tulajdonságok](copy-properties.md), a [változók](copy-variables.md) és a [kimenetek](copy-outputs.md)segítségével is használhatja.

Ha meg kell adnia, hogy az erőforrás telepítve van-e, tekintse meg a [feltétel elemet](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Erőforrás-iteráció

A másolási elem a következő általános formátumú:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

A **Name** tulajdonság bármely olyan érték, amely a hurok azonosítására szolgál. A **Count** tulajdonság határozza meg az erőforrástípus kívánt ismétlések számát.

A **Mode** és a **batchSize** tulajdonsággal adhatja meg, hogy az erőforrások párhuzamosan vagy sorba vannak-e telepítve. Ezeket a tulajdonságokat a [soros vagy párhuzamosan](#serial-or-parallel)kell ismertetni.

A következő példa a **storageCount** paraméterben megadott Storage-fiókok számát hozza létre.

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

A másolási művelet hasznos lehet a tömbök használatakor, mert a tömb minden elemén megismételhető. Használja a tömb `length` függvényét az iterációk számának megadásához, és `copyIndex` a tömb aktuális indexének lekéréséhez.

A következő példa egy Storage-fiókot hoz létre a paraméterben megadott minden névhez.

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

Ha a központilag telepített erőforrások értékeit szeretné visszaadni, a [kimenetek szakaszban a másolás](copy-outputs.md)lehetőséget használhatja.

## <a name="serial-or-parallel"></a>Soros vagy párhuzamos

Alapértelmezés szerint a Resource Manager párhuzamosan hozza létre az erőforrásokat. Nem korlátozza a párhuzamosan üzembe helyezett erőforrások számát, kivéve a sablonban lévő 800-erőforrások teljes korlátját. A létrehozásuk sorrendje nem garantált.

Azonban érdemes megadnia, hogy az erőforrások sorba legyenek telepítve. Ha például éles környezetet frissít, érdemes lehet megosztani a frissítéseket, hogy csak egy adott szám legyen frissítve egyszerre. Egy erőforrás egynél több példányának soros üzembe helyezéséhez állítsa a `mode` értéket **sorosra** , és `batchSize` az egyszerre telepítendő példányok számára. A soros módban a Resource Manager egy függőséget hoz létre a hurok korábbi példányain, így nem indít el egy köteget, amíg az előző köteg be nem fejeződik.

Ha például a Storage-fiókokat egyszerre két sorba szeretné telepíteni, használja a következőt:

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

A Mode (mód) tulajdonság szintén **párhuzamosan**fogadja el az alapértelmezett értéket.

## <a name="depend-on-resources-in-a-loop"></a>Egy hurok erőforrásaitól függ

Azt határozza meg, hogy egy erőforrás központi telepítése egy másik erőforrás után történik a `dependsOn` elem használatával. Ha olyan erőforrást szeretne üzembe helyezni, amely egy hurokban lévő erőforrások gyűjteményéből függ, adja meg a másolási hurok nevét a dependsOn elemben. Az alábbi példa bemutatja, hogyan helyezhet üzembe három Storage-fiókot a virtuális gép telepítése előtt. A virtuális gép teljes definíciója nem jelenik meg. Figyelje meg, hogy a copy elem neve `storagecopy`, és a virtuális gép dependsOn eleme is `storagecopy`.

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

## <a name="copy-limits"></a>Másolási korlátok

A szám nem lehet nagyobb, mint 800.

A darabszám nem lehet negatív szám. Ha Azure PowerShell 2,6-as vagy újabb, Azure CLI-2.0.74 vagy újabb verzióval, REST API vagy a **2019-05-10** -es vagy újabb verziójával rendelkező sablont telepít, akkor a Count értéket nullára állíthatja. A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nulla értéket a darabszámhoz.

Ügyeljen arra, hogy a [teljes telepítési mód](deployment-modes.md) legyen a másolással. Ha egy erőforráscsoport esetében újratelepíti a teljes módot, a másolási hurok feloldása után a sablonban nem megadott erőforrások törlődnek.

## <a name="example-templates"></a>Példa sablonok

Az alábbi példák egy erőforrás vagy tulajdonság egynél több példányának létrehozására vonatkozó gyakori forgatókönyveket mutatnak be.

|Sablon  |Leírás  |
|---------|---------|
|[Tárterület másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Egynél több Storage-fiókot telepít a névben. |
|[Soros másolási tár](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Egyszerre több Storage-fiókot helyez üzembe. A név tartalmazza az index számát. |
|[Tároló másolása tömbvel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Több Storage-fiók üzembe helyezése. A név egy tömbből származó értéket tartalmaz. |
|[VM-telepítés változó számú adatlemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Több adatlemez üzembe helyezése virtuális géppel. |
|[Több biztonsági szabály](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Több biztonsági szabályt telepít egy hálózati biztonsági csoportra. A biztonsági szabályokat egy paraméter alapján hozza létre. A paraméternél tekintse meg a [több NSG-paramétert tartalmazó fájlt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Következő lépések

* Az oktatóanyag lépéseinek megismeréséhez lásd [: oktatóanyag: több erőforrás-példány létrehozása Resource Manager-sablonok használatával](template-tutorial-create-multiple-instances.md).
* A másolási elem egyéb felhasználási módjaiért lásd:
  * [Tulajdonság-iteráció Azure Resource Manager-sablonokban](copy-properties.md)
  * [Változó iteráció Azure Resource Manager-sablonokban](copy-variables.md)
  * [Kimeneti iteráció Azure Resource Manager-sablonokban](copy-outputs.md)
* További információ a másolás beágyazott sablonokkal történő használatáról: [a másolás használata](linked-templates.md#using-copy).
* Ha szeretné megismerni a sablonok egyes részeit, olvassa el a [Azure Resource Manager sablonok készítése](template-syntax.md)című szakaszt.
* A sablon üzembe helyezésével kapcsolatos további információkért lásd: [alkalmazások központi telepítése Azure Resource Manager sablonnal](deploy-powershell.md).

