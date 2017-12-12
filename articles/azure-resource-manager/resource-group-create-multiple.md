---
title: "Azure-erőforrások több példányának telepítése |} Microsoft Docs"
description: "Másolási művelet és a tömbök használata Azure Resource Manager sablon felépítésének több alkalommal, amikor erőforrásokat üzembe helyezi."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: tomfitz
ms.openlocfilehash: ac72190ddf01301eba595995d2167904ba4b0c05
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Egy erőforrás vagy egy tulajdonság az Azure Resource Manager sablonokban több példányának telepítése
Ez a cikk bemutatja, hogyan feltételesen központi telepítése egy erőforrást, és több példánya erőforrás létrehozása az Azure Resource Manager sablonban felépítésének módját.

## <a name="conditionally-deploy-resource"></a>Erőforrás feltételesen telepítése

Döntse el, központi telepítése során egy példány vagy erőforrás nincs példány létrehozásához, használja a `condition` elemet. Ez az elem értéke IGAZ vagy hamis oldja fel. Értéke true, ha az erőforrás van telepítve. Ha értéke HAMIS, az erőforrás nincs telepítve. Például adja meg, hogy egy új tárfiókot telepítve van, vagy egy meglévő tárfiókot használja, használja:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

## <a name="resource-iteration"></a>Erőforrás iterációs
Ha el kell döntenie központi telepítése során egy erőforrást egy vagy több példány létrehozásához, adjon hozzá egy `copy` elemben, amely az erőforrástípus. A másolási elemben adja meg a számát ismétlési és ez a ciklus nevét. A count értékének pozitív egész számnak kell lennie, és nem haladhatja meg a 800. 

Az erőforrás létrehozása több alkalommal hajtja végre a következő formátumban:

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

Figyelje meg, hogy mindegyik erőforrás nevét tartalmazza a `copyIndex()` függvénynek, amely az aktuális iteráció adja meg a hurok. `copyIndex()`értéke nulla. Így, az alábbi példa:

```json
"name": "[concat('storage', copyIndex())]",
```

Hozza létre ezeket a neveket:

* storage0
* storage1
* storage2.

Eltolás az értéket, adjon át egy értéket a copyIndex() függvény. Végrehajtásához az ismétlések száma továbbra is a másolási elem van megadva, de copyIndex értékének ellensúlyozza a megadott érték. Így, az alábbi példa:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Hozza létre ezeket a neveket:

* storage1
* storage2
* storage3

A másolási művelet akkor hasznos, ha olyan tömb, mert az Ön is iterációt a tömb egyes elemei. Használja a `length` a tömbben, az ismétlések számát adja meg a függvény és `copyIndex` az aktuális index a tömb beolvasása. Így, az alábbi példa:

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

Alapértelmezés szerint a Resource Manager párhuzamosan létrehoz az erőforrások. A sorrend, amelyben létre, ezért nem garantált. Azonban érdemes lehet adja meg, hogy az erőforrások telepítése feladatütemezési. Például egy éles környezetben frissítésekor érdemes szakaszosan, a frissítések csak egy adott értéket egyszerre frissülnek.

Egy erőforrás több példánya Feladattervek telepítéséhez állítsa be `mode` való **soros** és `batchSize` egyszerre telepítendő példányok száma. A soros üzemmódban erőforrás-kezelő függőséget hoz létre a korábbi példánya a hurok, nem indul el egy kötegben csak az előző köteg befejeződése után.

Például Feladattervek telepítéséhez két tárfiókok egyszerre használja:

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

A mód tulajdonságot is fogad **párhuzamos**, az alapértelmezett érték.

## <a name="property-iteration"></a>Tulajdonság iterációs

A tulajdonsághoz több érték létrehozására egy erőforrás hozzáadása egy `copy` tömb a Tulajdonságok elemben. A tömb olyan objektumokat tartalmaz, és az egyes objektumok tulajdonságai a következők:

* név - létrehozása több értéket a tulajdonság neve
* szám - létrehozásához értékek száma
* bemenet – olyan objektum, amely a tulajdonság hozzárendelése értékeket tartalmazza.  

A következő példa bemutatja, hogyan alkalmazandó `copy` dataDisks tulajdonság egy virtuális gépen:

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

Figyelje meg, hogy használatakor `copyIndex` egy tulajdonság iterációs belül meg kell adnia a iterációs nevét. Nem kell adnia a nevét, és erőforrás-ismétlés használatakor.

Erőforrás-kezelő bontja ki a `copy` tömb üzembe helyezése során. A tömb neve lesz a tulajdonságnevet kell megadni. A bemeneti értékek lesz az objektum tulajdonságait. A telepített sablon válik:

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
      }],
      ...
```

Erőforrás- és tulajdonság iterációs együtt használható. Hivatkozás a tulajdonság iterációs név szerint.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2016-06-01",
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

## <a name="variable-iteration"></a>Változó iterációs

Egy változó több példány létrehozásához használja a `copy` elemet a változók szakaszban. Hozzon létre több objektumpéldányok kapcsolódó értékeket, és rendeljen ezeket az értékeket az erőforrás-példányokban kívánja. Másolás tömbtulajdonság vagy tömb vagy objektum létrehozására használhatja. Mindkét megközelítés az alábbi példában látható:

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
        }
      ]
    },
    "copy": [
      {
        "name": "disks-top-level-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks-top-level-array')]"
        }
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
    "exampleArray": {
      "value": "[variables('disks-top-level-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="depend-on-resources-in-a-loop"></a>Ismétlődő erőforrásokat függ
Megadja, hogy egy erőforrás által központilag telepített után egy másik erőforrás használja a `dependsOn` elemet. Egy erőforrást, amelyek elengedhetetlenek az ismétlődő források központi telepítéséhez adja meg a másolási ciklust a dependsOn elem nevét. A következő példa bemutatja, hogyan három storage-fiókok telepítése a virtuális gép üzembe helyezése előtt. A teljes virtuálisgép-definíció nem jelenik meg. Figyelje meg, hogy rendelkezik-e a másolási elem name tulajdonsága `storagecopy` , és a dependsOn elem a virtuális gépek is `storagecopy`.

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

## <a name="iteration-for-a-child-resource"></a>A gyermek-erőforrások esetében iterációs
A másolási ciklust a gyermek-erőforrások esetében nem használható. Hozzon létre egy erőforrást, amely általában határozza meg, mint a beágyazott belül egy másik erőforrás több példánya, ehelyett hozzon létre, hogy erőforrást egy legfelső szintű erőforrás. Megadhatja a kapcsolat a szülő erőforrás típusa és neve tulajdonságai.

Tegyük fel, hogy egy adat-előállító belül gyermek erőforrásként általában meghatározása a DataSet adatkészlet.

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
}]
```

Adatkészletek több példány létrehozásához helyezze az adat-előállítóban kívül. A dataset adat-előállító ugyanazon a szinten kell lennie, de továbbra is az adat-előállítóban gyermek erőforrása. Megőrizheti a adatkészlet és adat-előállító típusa és neve tulajdonságai közötti kapcsolat. Óta már nem lehet következtetni a sablonban helyéről, meg kell adnia a teljesen minősített típus a formátumban: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Az adat-előállítóban példányának a szülő-gyermek kapcsolat létrehozására, nevezze el a készlet, amely a szülő erőforrás nevét tartalmazza. A formátumot használja: `{parent-resource-name}/{child-resource-name}`.  

Az alábbi példa megvalósítását mutatja be:

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
    } 
    ...
}]
```

## <a name="deploy-example-templates"></a>Példa sablonok telepítése

### <a name="resource-iteration"></a>Erőforrás iterációs

A [tárolási másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) sablon telepíti egy index számot a név több tárfiókot.

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/copystorage.json
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment create \
  --resource-group examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/copystorage.json
```

### <a name="serial-resource-iteration"></a>Soros erőforrás iterációs

A [soros tároló](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) sablon telepít több tárfiókot egy időben. A neve tartalmazza a indexszámát.

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/serialcopystorage.json
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment create \
  --resource-group examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/serialcopystorage.json
```

### <a name="resource-iteration-from-array"></a>Egy olyan tömbből, erőforrás iterációs

A [másolja a tömb tárolási](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) sablon telepíti a több tárfiókot. A név egy olyan tömbből értéket tartalmaz.

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/copystoragewitharray.json
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment create \
  --resource-group examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/copystoragewitharray.json
```

### <a name="conditionally-deploy-resources"></a>Feltételesen az erőforrások telepítése

A [virtuális gép és egy új vagy meglévő virtuális hálózat, a tároló és a nyilvános IP-cím](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) sablon telepíti az új vagy meglévő erőforrásokat egy virtuális géppel.

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-new-or-existing-conditions/azuredeploy.json
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment create \
  --resource-group examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-new-or-existing-conditions/azuredeploy.json
```

### <a name="property-iteration"></a>Tulajdonság iterációs

A [adatlemezek változó több Virtuálisgép-telepítéshez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) sablont egy virtuális gép több adatlemezek telepíti.

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-windows-copy-datadisks/azuredeploy.json
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment create \
  --resource-group examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-windows-copy-datadisks/azuredeploy.json
```

### <a name="variable-iteration"></a>Változó iterációs

A [változók másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) sablon a különböző módokat a változók léptetés mutatja be.

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/copyvariables.json
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
az group deployment create \
  --resource-group examplegroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/copyvariables.json
```

### <a name="variable-iteration-to-create-resources"></a>Változó iterációs erőforrások létrehozása

A [több biztonsági szabály](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) sablon több biztonsági szabály telepíti a hálózati biztonsági csoport. Akkor hoz létre a biztonsági szabályok paraméter.

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json
```

## <a name="next-steps"></a>Következő lépések
* Ha azt szeretné, további információt a szakaszok egy sablon, lásd: [Azure Resource Manager sablonok készítése](resource-group-authoring-templates.md).
* A sablon telepítéséhez, lásd: [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).

