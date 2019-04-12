---
title: Az Azure Resource Manager-sablonfüggvények – logikai |} A Microsoft Docs
description: A functions az Azure Resource Manager-sablon használatával határozza meg a logikai értékeket ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: tomfitz
ms.openlocfilehash: 9065c6bc71a153ae94ddc20d5b41a152094fc111
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492175"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok logikai függvények

Resource Manager összehasonlítások végzett a sablonok számos funkciót biztosít.

* [és](#and)
* [logikai](#bool)
* [Ha](#if)
* [nem](#not)
* [vagy](#or)

## <a name="and"></a>és

`and(arg1, arg2, ...)`

Ellenőrzi, hogy minden paraméter értéket Igaz.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Ellenőrizze, hogy az első értéket e igaz. |
| arg2 |Igen |logikai |A második érték, ellenőrizze, hogy igaz. |
| További argumentumok |Nem |logikai |További argumentumok ellenőrizze, hogy igaz. |

### <a name="return-value"></a>Vrácená hodnota

Értéket ad vissza **igaz** Ha minden érték igaz; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) logikai függvények használatát ismerteti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False (Hamis) |
| orExampleOutput | Bool | True (Igaz) |
| notExampleOutput | Bool | False (Hamis) |

## <a name="bool"></a>logikai

`bool(arg1)`

A paraméter alakítja át egy logikai érték.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc- vagy int |Az érték egy logikai típusra konvertál. |

### <a name="return-value"></a>Vrácená hodnota
Az átalakított érték logikai érték.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) bool használata egy string vagy integer jeleníti meg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| trueString | Bool | True (Igaz) |
| falseString | Bool | False (Hamis) |
| trueInt | Bool | True (Igaz) |
| falseInt | Bool | False (Hamis) |

## <a name="if"></a>Ha

`if(condition, trueValue, falseValue)`

Hogy értéket adja vissza egy feltétele igaz vagy hamis.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| feltétel |Igen |logikai |Ellenőrizze, hogy azt true vagy FALSE (hamis) érték. |
| trueValue |Igen | karakterlánc, int, objektumot vagy tömböt |A visszatérési érték, amikor a feltétel teljesül. |
| falseValue |Igen | karakterlánc, int, objektumot vagy tömböt |A visszatérési érték, amikor a feltétel nem teljesül. |

### <a name="return-value"></a>Vrácená hodnota

Második paraméterként adja vissza, amikor az első paraméter **igaz**; ellenkező esetben, harmadik paraméterként adja vissza.

### <a name="remarks"></a>Megjegyzések

Ha a feltétel nem **igaz**, a true érték csak abban az esetben. Ha a feltétel nem **hamis**, a False (hamis) érték csak abban az esetben. Az a **Ha** függvény, hozzáadhatja a kifejezések, amelyek csak feltételesen érvényes. Ha például egy erőforrást, hogy létezik egy feltétel alapján, de a más feltétel alapján nem hivatkozhat. Feltételesen a kifejezések kiértékelése példát a következő szakaszban látható.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) bemutatja, hogyan használható a `if` függvény.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| yesOutput | String | igen |
| noOutput | String | nem |
| objectOutput | Objektum | {"teszt": "érték1"} |

A következő [példasablonja](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) kifejezésekkel, amelyek csak feltételesen érvényes ez a funkció használatát ismerteti.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[greaterOrEquals(parameters('logAnalytics'), '0')]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(greaterOrEquals(parameters('logAnalytics'), '0'), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(greaterOrEquals(parameters('logAnalytics'), '0'), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(greaterOrEquals(parameters('logAnalytics'), '0'), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>nem

`not(arg1)`

Logikai érték a ellentétes értékké alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Az átalakítandó érték. |

### <a name="return-value"></a>Vrácená hodnota

Értéket ad vissza **igaz** paraméter esetén **hamis**. Értéket ad vissza **hamis** paraméter esetén **igaz**.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) logikai függvények használatát ismerteti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False (Hamis) |
| orExampleOutput | Bool | True (Igaz) |
| notExampleOutput | Bool | False (Hamis) |

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) használ **nem** a [egyenlő](resource-group-template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True (Igaz) |

## <a name="or"></a>vagy

`or(arg1, arg2, ...)`

Ellenőrzi, hogy minden paraméter értéke igaz.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Ellenőrizze, hogy az első értéket e igaz. |
| arg2 |Igen |logikai |A második érték, ellenőrizze, hogy igaz. |
| További argumentumok |Nem |logikai |További argumentumok ellenőrizze, hogy igaz. |

### <a name="return-value"></a>Vrácená hodnota

Értéket ad vissza **igaz** Ha bármely érték igaz; ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) logikai függvények használatát ismerteti.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

Az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False (Hamis) |
| orExampleOutput | Bool | True (Igaz) |
| notExampleOutput | Bool | False (Hamis) |

## <a name="next-steps"></a>További lépések

* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* A megadott számú alkalommal újrafuttathatja egy adott típusú erőforrás létrehozásakor, lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A létrehozott sablon üzembe helyezése, olvassa el [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).

