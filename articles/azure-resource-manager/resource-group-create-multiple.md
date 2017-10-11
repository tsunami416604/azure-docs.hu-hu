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
ms.date: 06/26/2017
ms.author: tomfitz
ms.openlocfilehash: ed8e3081d2b2e07938d7cf3aa5f95f6dde81bc66
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Egy erőforrás vagy egy tulajdonság az Azure Resource Manager sablonokban több példányának telepítése
Ez a témakör bemutatja, hogyan felépítésének erőforrás több példányát, vagy egy tulajdonság több példányát erőforrás létrehozása az Azure Resource Manager sablonban.

Ha szeretné logika hozzáadása a sablont, amely lehetővé teszi, hogy adja meg, hogy telepítve van-e a erőforrás című [feltételesen telepíteni az erőforrás](#conditionally-deploy-resource).

## <a name="resource-iteration"></a>Erőforrás iterációs
Az erőforrástípus több példányt létrehozni, vegye fel a `copy` elemben, amely az erőforrástípus. A másolási elemben adja meg a számát ismétlési és ez a ciklus nevét. A count értékének pozitív egész számnak kell lennie, és nem haladhatja meg a 800. Erőforrás-kezelő párhuzamosan hoz létre az erőforrásokat. A sorrend, amelyben létre, ezért nem garantált. Feladatütemezési többször is erőforrások létrehozásához lásd: [soros másolási](#serial-copy). 

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

## <a name="serial-copy"></a>Soros másolása

Használatakor a másolási elem az erőforrástípus, erőforrás-kezelő, alapértelmezés szerint több példány létrehozásához telepíti azokat a példányokat párhuzamosan. Azonban érdemes lehet adja meg, hogy az erőforrások telepítése feladatütemezési. Például egy éles környezetben frissítésekor érdemes szakaszosan, a frissítések csak egy adott értéket egyszerre frissülnek.

A Resource Manager tulajdonságok biztosít, amelyek lehetővé teszik több példány Feladattervek telepítendő példány elemen. A másolási elem beállítása `mode` való **soros** és `batchSize` egyszerre telepítendő példányok száma. A soros üzemmódban erőforrás-kezelő függőséget hoz létre a korábbi példánya a hurok, nem indul el egy kötegben csak az előző köteg befejeződése után.

```json
"copy": {
    "name": "iterator",
    "count": "[parameters('numberToDeploy')]",
    "mode": "serial",
    "batchSize": 2
},
```

A mód tulajdonságot is fogad **párhuzamos**, az alapértelmezett érték.

Tényleges erőforrások létrehozása nélkül soros példány teszteléséhez a következő sablon használata, amely üres beágyazott sablonok telepíti:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex())]",
      "copy": {
        "name": "iterator",
        "count": "[parameters('numberToDeploy')]",
        "mode": "serial",
        "batchSize": 1
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

A központi telepítés előzményei figyelje meg, hogy a beágyazott központi telepítések feldolgozása sorrendben.

![soros központi telepítés](./media/resource-group-create-multiple/serial-copy.png)

Modell forgatókönyvek esetében a következő példa egy beágyazott sablonból Linux virtuális gép egyszerre két példányt központilag telepíti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "User name for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "dnsLabelPrefix": {
            "type": "string",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "16.04.0-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "15.10",
                "16.04.0-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        }
    },
    "variables": {
        "templatelink": "https://raw.githubusercontent.com/rjmax/Build2017/master/Act1.TemplateEnhancements/Chapter03.LinuxVM.json"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "[concat('nestedDeployment',copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "copy": {
                "name": "myCopySet",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            },
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "adminUsername": {
                        "value": "[parameters('adminUsername')]"
                    },
                    "adminPassword": {
                        "value": "[parameters('adminPassword')]"
                    },
                    "dnsLabelPrefix": {
                        "value": "[parameters('dnsLabelPrefix')]"
                    },
                    "ubuntuOSVersion": {
                        "value": "[parameters('ubuntuOSVersion')]"
                    },
                    "index":{
                        "value": "[copyIndex()]"
                    }
                }
            }
        }
    ]
}
```

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

Csak akkor szerepelhet egy másolás elem tulajdonságai között az egyes erőforrások. Egy iteráció hurok egynél több tulajdonság megadásához ad meg a másolási tömb több objektum. Az egyes objektumok külön-külön többször is van. Ahhoz például, hogy hozzon létre több példányát is a `frontendIPConfigurations` tulajdonság és a `loadBalancingRules` tulajdonság a terheléselosztóhoz, egyetlen másolatának elemben adja meg mindkét objektumok: 

```json
{
    "name": "[variables('loadBalancerName')]",
    "type": "Microsoft.Network/loadBalancers",
    "properties": {
        "copy": [
          {
              "name": "frontendIPConfigurations",
              "count": 2,
              "input": {
                  "name": "[concat('loadBalancerFrontEnd', copyIndex('frontendIPConfigurations', 1))]",
                  "properties": {
                      "publicIPAddress": {
                          "id": "[variables(concat('publicIPAddressID', copyIndex('frontendIPConfigurations', 1)))]"
                      }
                  }
              }
          },
          {
              "name": "loadBalancingRules",
              "count": 2,
              "input": {
                  "name": "[concat('LBRuleForVIP', copyIndex('loadBalancingRules', 1))]",
                  "properties": {
                      "frontendIPConfiguration": {
                          "id": "[variables(concat('frontEndIPConfigID', copyIndex('loadBalancingRules', 1)))]"
                      },
                      "backendAddressPool": {
                          "id": "[variables('lbBackendPoolID')]"
                      },
                      "protocol": "tcp",
                      "frontendPort": "[variables(concat('frontEndPort' copyIndex('loadBalancingRules', 1))]",
                      "backendPort": "[variables(concat('backEndPort' copyIndex('loadBalancingRules', 1))]",
                      "probe": {
                          "id": "[variables('lbProbeID')]"
                      }
                  }
              }
          }
        ],
        ...
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

## <a name="create-multiple-instances-of-a-child-resource"></a>Hozzon létre egy gyermek erőforrás több példánya
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

## <a name="conditionally-deploy-resource"></a>Erőforrás feltételesen telepítése

Adja meg, hogy telepítve van-e a erőforrás, a `condition` elemet. Ez az elem értéke IGAZ vagy hamis oldja fel. Értéke true, ha az erőforrás van telepítve. Ha értéke HAMIS, az erőforrás nincs telepítve. Például adja meg, hogy egy új tárfiókot telepítve van, vagy egy meglévő tárfiókot használja, használja:

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

Például egy új vagy meglévő erőforrást használ, tekintse meg a [új vagy meglévő feltétel sablon](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json).

Például egy jelszó vagy SSH-kulcs használatával a virtuális gép telepítése, lásd: [felhasználónév és SSH feltétel sablon](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json).

## <a name="next-steps"></a>Következő lépések
* Ha azt szeretné, további információt a szakaszok egy sablon, lásd: [Azure Resource Manager sablonok készítése](resource-group-authoring-templates.md).
* A sablon telepítéséhez, lásd: [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).

