---
title: "Az Azure Resource Manager sablon struktúra és szintaxis |} Microsoft Docs"
description: "A struktúra és az Azure Resource Manager-sablonok deklaratív JSON-szintaxis használatával tulajdonságait ismerteti."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: c0ec888dbe94229701391f1aed79a78d3cb90d77
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>A struktúra és az Azure Resource Manager-sablonok szintaxisát ismertetése
Ez a cikk ismerteti az Azure Resource Manager sablon szerkezete. Azt mutatja be a különböző szakaszokat, egy sablon és az elérhető tulajdonságok köre szakaszt. A sablon JSON és összeállítani az üzemelő példány értékeit használó kifejezéseket tartalmaz. A sablonok létrehozásának részletes oktatóanyaga, lásd: [az első Azure Resource Manager-sablon létrehozása](resource-manager-create-first-template.md).

## <a name="template-format"></a>Sablon formátumban
A legegyszerűbb struktúra, a sablon a következő elemeket tartalmazza:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| $schema |Igen |A JSON-fájl, amely leírja a sablon nyelvi verzióját helye. Az előző példában is látható URL-CÍMÉT használja. |
| contentVersion |Igen |A sablon (például 1.0.0.0) verzióját. Bármely érték biztosíthat ehhez az elemhez. A sablon eszközzel való telepítésekor ez az érték segítségével győződjön meg arról, hogy a megfelelő sablon használatban van-e. |
| paraméterek |Nem |Amikor központi telepítés végrehajtása testre szabhatja az erőforrások telepítése által biztosított értéket. |
| változók |Nem |A sablon JSON-töredék, egyszerűbbé teheti a sablonnyelvi kifejezéseket használt értékek. |
| erőforrások |Igen |Telepített vagy frissített erőforráscsoportban erőforrástípusok esetében. |
| kimenetek |Nem |A telepítés utáni visszaadott értékek. |

Minden elem beállítható tulajdonságokat tartalmaz. A következő példa a teljes szintaxissal sablon tartalmazza:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Ez a cikk ismerteti a szakaszok részletesebben sablon.

## <a name="expressions-and-functions"></a>Kifejezések és funkciók
A sablon alapvető szintaxisa a JSON-NÁ. Azonban kifejezések és a funkciók bővíthetik a JSON a sablonon belül elérhető értékek.  Kifejezések íródtak belül JSON szövegkonstansok amelynek első és utolsó karaktere a szögletes: `[` és `]`, illetve. A kifejezés értéke legyen kiértékelve a sablon telepítésekor. Megírva egy szöveges karakterlánc, amíg értékeli a kifejezés eredménye lehet eltérő típusú JSON, például a tömb vagy egész, attól függően, hogy a tényleges kifejezést.  Indítsa el a zárójel szövegkonstansnak rendelkeznie `[`, de nem rendelkezik azt kifejezésként értelmezi, adja hozzá a karakterlánc elindítani egy extra zárójel `[[`.

Általában használ kifejezések funkciók konfigurálása a központi telepítési műveleteinek elvégzéséhez. Csak a például a JavaScript, függvényhívások-ként formázott `functionName(arg1,arg2,arg3)`. A pont és a [index] operátorok használatával tulajdonságok hivatkozik.

A következő példa bemutatja, hogyan hozhat létre értékek több funkciók használandó:

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

Sablon funkciók teljes listáját lásd: [Azure Resource Manager sablonfüggvényei](resource-group-template-functions.md). 

## <a name="parameters"></a>Paraméterek
A sablon a Paraméterek szakaszban adja meg az erőforrások telepítése során is bemeneti értékeket. A paraméterértékek lehetővé teszik a központi telepítés testreszabása egy adott környezetben (például a fejlesztői, tesztelési és éles) is lefednek értékek megadásával. A sablon a paraméterek megadása nem szükséges, de a paraméterek nélkül a sablon mindig központilag ugyanazon a nevét, helyét és tulajdonságok ugyanazokat az erőforrásokat.

Az alábbi példában egy egyszerű paraméterek definícióját:

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
A változók szakaszban, a sablon egész érték, amely használható hozhat létre. Nem kell meghatároznia a változót, de azok gyakran leegyszerűsítheti a sablon csökkentésével összetett kifejezések.

Az alábbi példában egy egyszerű változó definíciója:

```json
"variables": {
  "webSiteName": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
},
```

További információ a változók meghatározása: [változók szakaszban az Azure Resource Manager-sablonok](resource-manager-templates-variables.md).

## <a name="resources"></a>Erőforrások
Erőforrások területen adja meg az erőforrások telepítése vagy frissítése. Ez a szakasz kérheti le bonyolult, mert ismernie kell a típusok esetében helyez üzembe adja meg a megfelelő értékeket.

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

További információkért lásd: [Azure Resource Manager-sablonok források szakaszában](resource-manager-templates-resources.md).

## <a name="outputs"></a>Kimenetek
A kimenetek szakaszban adja meg központi telepítéséből a visszaadott érték. Visszaadhatja például az URI telepített erőforrások eléréséhez.

A következő példa egy kimeneti definíciója szerkezetét mutatja:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| outputName |Igen |A kimeneti érték nevét. Egy érvényes JavaScript-azonosítónak kell lennie. |
| type |Igen |A kimeneti érték típusa. Kimeneti értékek támogatásához sablon bemeneti paraméterként. |
| érték |Igen |Amely értékeli ki, és kimeneti értéket adja vissza a sablonnyelvi kifejezés. |

A következő példa bemutatja a kimenetek szakaszban visszaadott érték.

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

A kimeneti használatáról további információk: [állapotát az Azure Resource Manager sablonokban megosztása](best-practices-resource-manager-state.md).

## <a name="template-limits"></a>Sablon korlátok

A sablon 1 MB-nál, és minden paraméterfájl 64 KB méretének korlátozására. 1 MB-os korlát vonatkozik a sablon a végső állapot után kiterjedt ismétlődő erőforrás-definíciókban és változók és a paraméterek értékeit. 

Emellett korlátozva:

* 256 paraméterek
* 256 változók
* 800 erőforrások (például a példányszám)
* 64 kimeneti értékek
* egy sablon kifejezés 24,576 karakter

Néhány sablon korlátot azért lépheti túl a beágyazott sablon használatával. További információkért lásd: [kapcsolt sablonok használata az Azure-erőforrások telepítésekor](resource-group-linked-templates.md). A paraméterek, változók vagy kimenetek számának csökkentése érdekében kombinálható egy objektum több értéket. További információkért lásd: [paraméterekként objektumok](resource-manager-objects-as-parameters.md).

## <a name="next-steps"></a>Következő lépések
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A sablonon belül használhatja a functions szolgáltatással kapcsolatos részletekért lásd: [Azure Resource Manager Sablonfüggvényei](resource-group-template-functions.md).
* Egyesítenie több sablon üzembe helyezése során, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Szükség lehet egy másik erőforráscsoportban található erőforrások használatával. Ez a forgatókönyv nem közös, ha a storage-fiókok vagy több erőforrás csoporttal megosztott virtuális hálózatok. További információkért lásd: a [resourceId függvény](resource-group-template-functions-resource.md#resourceid).
