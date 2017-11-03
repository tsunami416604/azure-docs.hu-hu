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
ms.date: 06/14/2017
ms.author: tomfitz
ms.openlocfilehash: dc9b64062d7f68c83aa090eec96744819a5ca423
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>A struktúra és az Azure Resource Manager-sablonok szintaxisát ismertetése
Ez a témakör ismerteti az Azure Resource Manager sablon szerkezete. Azt mutatja be a különböző szakaszokat, egy sablon és az elérhető tulajdonságok köre szakaszt. A sablon JSON és összeállítani az üzemelő példány értékeit használó kifejezéseket tartalmaz. A sablonok létrehozásának részletes oktatóanyaga, lásd: [az első Azure Resource Manager-sablon létrehozása](resource-manager-create-first-template.md).

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
| Erőforrások |Igen |Telepített vagy frissített erőforráscsoportban erőforrástípusok esetében. |
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
        "<variable-name>": { 
            <variable-complex-type-value> 
        }
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

Azt vizsgálja meg a sablon a témakör későbbi részében részletesebben részeit.

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

Megadhatja az alábbi szerkezettel paramétereket:

```json
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
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| parameterName |Igen |A paraméter neve. Egy érvényes JavaScript-azonosítónak kell lennie. |
| type |Igen |A paraméter értékének típusa. A táblázat után engedélyezett típusokkal listája látható. |
| DefaultValue érték |Nem |A paraméternek, ha nincs érték megadva, a paraméter alapértelmezett értéke. |
| Storageaccount_accounttype |Nem |Győződjön meg arról, hogy a megfelelő érték van megadva a paraméter megengedett értékei tömbje. |
| A MinValue |Nem |A minimális int típusú paraméterekhez, ez az érték értéke között lehet. |
| MaxValue |Nem |A maximális int típusú paraméterekhez, ez az érték értéke között lehet. |
| a minLength |Nem |A minimális string, secureString és array típusú paraméterekhez, ez az érték hossza határokat is beleértve. |
| MaxLength |Nem |A string, secureString és array típusú paraméterekhez, ez az érték hossza legfeljebb határokat is beleértve. |
| leírás |Nem |A paraméter, akkor jelenik meg, a felhasználók számára a portálon keresztül leírása. |

Az engedélyezett típusokkal és az értékek a következők:

* **karakterlánc**
* **secureString**
* **int**
* **logikai érték**
* **objektum** 
* **secureObject**
* **a tömb**

Nem kötelező paraméter adja meg, adja meg a DefaultValue érték (üres is lehet). 

A paraméter neve a sablon, amely megfelel a paramétert a parancsba a sablon telepítéséhez ad meg, ha nincs megadta kapcsolatos lehetséges egyértelműek. Erőforrás-kezelő Ez zavart megszünteti a utótag hozzáadásával **FromTemplate** a sablon paraméterhez. Például, ha nevű paraméter **ResourceGroupName** a sablonban ütközik a **ResourceGroupName** paramétere a [új-AzureRmResourceGroupDeployment ](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) parancsmag. A telepítés során kéri adjon meg egy értéket a **ResourceGroupNameFromTemplate**. Ez zavart ne általában a nem paraméterek telepítési műveleteihez használt paraméterek megegyező névvel.

> [!NOTE]
> A jelszó, kulcsokat és más titkos adatokat kell használni a **secureString** típusa. A JSON-objektumból átadni a bizalmas adatokat, ha a **secureObject** típusa. Sablonparaméterek secureString vagy secureObject típusú erőforrások telepítése után nem lehet olvasni. 
> 
> Az üzembe helyezési előzményeket a következő bejegyzést például egy karakterláncot és objektumot, de nem a secureString és secureObject értékét jeleníti meg.
>
> ![központi telepítés értékek megjelenítése](./media/resource-group-authoring-templates/show-parameters.png)  
>

A következő példa bemutatja, hogyan paraméterek megadásához:

```json
"parameters": {
    "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
    },
    "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
    },
    "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
    },
    "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
    }
}
```

A telepítés során az értékek a bemeneti tudnivalókat [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md). 

## <a name="variables"></a>Változók
A változók szakaszban, a sablon egész érték, amely használható hozhat létre. Nem kell meghatároznia a változót, de azok gyakran leegyszerűsítheti a sablon csökkentésével összetett kifejezések.

Megadhatja a változókat és az alábbi szerkezettel:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    }
}
```

A következő példa bemutatja, hogyan adhat meg egy változót összeállított paraméter két érték közül:

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

A következő példa bemutatja egy változó, amely egy összetett JSON-típus, és a változókat, amelyek más változók össze:

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
"variables": {
    "environmentSettings": {
        "test": {
            "instancesSize": "Small",
            "instancesCount": 1
        },
        "prod": {
            "instancesSize": "Large",
            "instancesCount": 4
        }
    },
    "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
    "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
    "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

## <a name="resources"></a>Erőforrások
Erőforrások területen adja meg az erőforrások telepítése vagy frissítése. Ez a szakasz kérheti le bonyolult, mert ismernie kell a típusok esetében helyez üzembe adja meg a megfelelő értékeket. Az erőforrás-specifikus értékeket (apiVersion, típusa és tulajdonságait) meg kell adnia, lásd: [meghatározhatja az erőforrásokat az Azure Resource Manager sablonokban](/azure/templates/). 

Meghatározhatja az erőforrások az alábbi szerkezettel:

```json
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
]
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| Az állapot | Nem | Logikai érték, amely azt jelzi, hogy telepítve van-e az erőforrás. |
| apiVersion |Igen |Az erőforrás létrehozásához használt a REST API verziója. |
| type |Igen |Az erőforrás típusát. Ezt az értéket az erőforrás-szolgáltató és az erőforrástípus névtere kombinációja (például **Microsoft.Storage/storageAccounts**). |
| név |Igen |Az erőforrás nevét. A név URI összetevő korlátozások RFC3986 definiált kell követnie. Emellett Azure-szolgáltatások elérhetővé tenni az erőforrásnév kívül felek ellenőrzése a nevét, győződjön meg arról, hogy nincs egy másik identitás hamisításának kísérlet. |
| location |Változó |Támogatja a megadott erőforráscsoport földrajzi elhelyezkedését. Kiválaszthatja a rendelkezésre álló helyeken, de általában érdemes válasszon egyet, amelynek mérete megközelítőleg a felhasználók. Általában is érdemes helyezendő erőforrásokat, amelyek ugyanabban a régióban kapcsolatba egymással. A legtöbb erőforrás szükséges egy helyre, de néhány típust (például egy szerepkör-hozzárendelés) igényel egy helyre. Lásd: [erőforrás hely beállítása az Azure Resource Manager sablonokban](resource-manager-template-location.md). |
| tags |Nem |Az erőforrás társított címkékkel. Lásd: [címkével olyan erőforrásokat az Azure Resource Manager sablonokban](resource-manager-template-tags.md). |
| Megjegyzések |Nem |A Megjegyzések a a sablonban lévő erőforrások dokumentálása |
| Másolás |Nem |Ha egynél több példány van szükség, az olyan erőforrások száma létrehozásához. Az alapértelmezett mód párhuzamos. Adja meg a soros módban, ha nem szeretné, hogy az összes vagy egy időben üzembe helyezendő erőforrásokat. További információkért lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nem |Ehhez az erőforráshoz központi telepítése előtt telepíteni kell erőforrások. Erőforrás-kezelő kiértékeli az erőforrások közti függőségeket, és telepíti azokat a megfelelő sorrendben. Ha nincsenek függő erőforrások, párhuzamos központi telepítés. Az érték lehet egy vesszővel elválasztott lista erőforrás nevét vagy egyedi erőforrás-azonosítók. Ez a sablon üzembe helyezett erőforrások csak felsorolása Erőforrások, amelyek nincsenek meghatározva a sablonban már léteznie kell. Kerülje a szükségtelen függőségek hozzáadásával még a központi telepítés lassú, és hozzon létre körkörös függőségi viszony. A beállítás függőségek útmutatást lásd: [függőségek meghatározása az Azure Resource Manager-sablonok](resource-group-define-dependencies.md). |
| properties |Nem |Erőforrás-specifikus konfigurációs beállításokat. A tulajdonságok értékeit ugyanazok, mint a REST API művelet (PUT metódust) létrehozni az erőforrást a kérés törzsében meg az értékeket. Egy tulajdonság több példányát létrehozni egy másolatot tömb is megadható. További információkért lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md). |
| Erőforrások |Nem |A múltbeli erőforrástól függő gyermekszintű erőforrása. Csak olyan típusú erőforrások a szülő erőforrás sémája által számukra engedélyezett. A gyermek-erőforrás teljesen minősített típusú tartalmaz szülő erőforrástípusra, például **Microsoft.Web/sites/extensions**. A szülő erőforrás függőség nem utal. Függőséget explicit módon meg kell adni. |

A források szakaszában üzembe helyezendő erőforrásokat tömbjét tartalmazza. Az egyes erőforrások belül is meghatározhat gyermekerőforrásait tömbjét. Ezért a források szakaszában eredményezhet. a struktúra, például:

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

Gyermek erőforrások meghatározása kapcsolatos további információkért lásd: [Resource Manager-sablon nevét és típusát gyermek erőforrás beállított](resource-manager-template-child-resource.md).

A **feltétel** elem határozza meg, hogy telepítve van-e az erőforrás. Ez az elem értéke IGAZ vagy hamis oldja fel. Például adja meg, hogy telepítve van-e a egy új tárfiókot, használja:

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

Adja meg, hogy egy virtuális gép telepítve van-e a jelszó vagy SSH-kulcsot, adja meg a virtuális gép két verziója a sablonban és a használata **feltétel** használati megkülönböztetéséhez. Egy paraméter meghatározza, hogy mely forgatókönyv telepítését továbbítani.

```json
{
    "condition": "[equals(parameters('passwordOrSshKey'),'password')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'password')]",
    "properties": {
        "osProfile": {
            "computerName": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
        },
        ...
    },
    ...
},
{
    "condition": "[equals(parameters('passwordOrSshKey'),'sshKey')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'ssh')]",
    "properties": {
        "osProfile": {
            "linuxConfiguration": {
                "disablePasswordAuthentication": "true",
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "[variables('sshKeyPath')]",
                            "keyData": "[parameters('adminSshKey')]"
                        }
                    ]
                }
            }
        },
        ...
    },
    ...
}
``` 

Például egy jelszó vagy SSH-kulcs használatával a virtuális gép telepítése, lásd: [felhasználónév és SSH feltétel sablon](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json).

## <a name="outputs"></a>kimenetek
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
