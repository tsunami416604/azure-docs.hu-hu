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
ms.date: 02/11/2019
ms.author: tomfitz
ms.openlocfilehash: cdc48cf278fdd68bacf74c2d6d762c8d731949e0
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56111561"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Megismerheti a szerkezetének és szintaxisának az Azure Resource Manager-sablonok

Ez a cikk ismerteti az Azure Resource Manager-sablonok szerkezetének. Egy sablon és az elérhető tulajdonságok köre szakaszt az eltérő szakaszok tükrözze. A sablon JSON-t és kifejezések, amelyek segítségével kialakíthatja az üzemelő példány értékeit áll. Sablonok létrehozásának részletes útmutató: [az első Azure Resource Manager-sablon létrehozása](resource-manager-create-first-template.md).

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
| erőforrások |Igen |Erőforrástípusok, telepített vagy frissített egy erőforráscsoportban. |
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
                    "input": {
                        <properties-to-repeat>
                    }
                }
            ]
        },
        "copy": [
            {
                "name": "<variable-array-name>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
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

Az alábbi példa bemutatja egy egyszerű paraméterdefiníció:

```json
"parameters": {
  "siteNamePrefix": {
    "type": "string",
    "metadata": {
      "description": "The name prefix of the web app that you wish to create."
    }
  },
},
```

További információ a paraméterek megadása: [paraméterek szakaszban az Azure Resource Manager-sablonok](resource-manager-templates-parameters.md).

## <a name="variables"></a>Változók

A változók szakaszban, a sablon egész értékek, amelyek segítségével hozhatnak létre. Nem kell definiálnia a változókat, de azok összetett kifejezések csökkentésével gyakran egyszerűbb a sablont.

Az alábbi példa bemutatja egy egyszerű változódefinícióra:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

További információ a változók meghatározása: [változók szakaszban az Azure Resource Manager-sablonok](resource-manager-templates-variables.md).

## <a name="functions"></a>Functions

A sablonon belül a saját funkciókat is létrehozhat. Ezek a függvények használhatók a sablonban. Általában megadhat bonyolult kifejezés, amelyet szeretne, ismételje meg a sablon során. A felhasználó által definiált függvények készítése kifejezések és [funkciók](resource-group-template-functions.md) , amelyek használata támogatott.

A user függvény definiálásakor bizonyos korlátozások vonatkoznak:

* A függvény nem tud hozzáférni a változókat.
* A függvény csak használhatja a függvény a definiált paraméterek. Használatakor a [paraméterek függvény](resource-group-template-functions-deployment.md#parameters) belül a felhasználó által definiált függvény korlátozódnak, a függvény paramétereit.
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
A kimeneti szakaszban adjon meg értékeket, amelyek a központi telepítés rendszer adja vissza. Visszaadhatja például az URI-t üzembe helyezett erőforrások eléréséhez.

```json
"outputs": {
  "newHostName": {
    "type": "string",
    "value": "[reference(variables('webSiteName')).defaultHostName]"
  }
}
```

További információkért lásd: [kimenete az Azure Resource Manager-sablonok szakaszában](resource-manager-templates-outputs.md).

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
