---
title: Sablon functions – logikai
description: A Azure Resource Manager-sablonban a logikai értékek meghatározásához használandó függvények leírása.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: aef520a26124a85f414c4f4aa1a3e307d383c29b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357362"
---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok logikai funkciói

A Resource Manager számos funkciót biztosít a sablonokban való összehasonlításhoz.

* [és](#and)
* [bool](#bool)
* [Ha](#if)
* [nem](#not)
* [vagy](#or)

## <a name="and"></a>és

`and(arg1, arg2, ...)`

Ellenőrzi, hogy az összes paraméter értéke igaz-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Az első érték, amely alapján ellenőrizhető, hogy igaz-e. |
| arg2 |Igen |logikai |A második érték, amely alapján ellenőrizhető, hogy igaz-e. |
| További argumentumok |Nem |logikai |További argumentumok, amelyek alapján ellenőrizhető, hogy igazak-e. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** értéket ad vissza, ha az összes érték igaz; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a logikai függvények használatát mutatja be.

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

Az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False (Hamis) |
| orExampleOutput | Bool | True (Igaz) |
| notExampleOutput | Bool | False (Hamis) |

## <a name="bool"></a>bool

`bool(arg1)`

A paramétert logikai értékre alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc vagy int |A logikai értékké konvertálandó érték. |

### <a name="return-value"></a>Vrácená hodnota
Az átalakított érték logikai értéke.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) azt szemlélteti, hogyan használható a bool karakterlánc vagy egész számmal.

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

Egy értéket ad vissza, attól függően, hogy a feltétel igaz vagy hamis.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| condition |Igen |logikai |Az érték, amely alapján ellenőrizhető, hogy igaz vagy hamis. |
| trueValue |Igen | karakterlánc, int, Object vagy Array |A feltétel teljesülésekor visszaadott érték. |
| falseValue |Igen | karakterlánc, int, Object vagy Array |A feltétel hamis állapotának visszaadására szolgáló érték. |

### <a name="return-value"></a>Vrácená hodnota

A második paramétert adja vissza, ha az első paraméter **igaz**. Ellenkező esetben a harmadik paramétert adja vissza.

### <a name="remarks"></a>Megjegyzések

Ha a feltétel **igaz**, csak a True érték lesz kiértékelve. Ha a feltétel **hamis**, csak a hamis értéket értékeli ki a rendszer. Az **IF** függvénnyel olyan kifejezéseket is tartalmazhat, amelyek csak feltételes érvényességgel rendelkeznek. Hivatkozhat például egy olyan erőforrásra, amely egy feltételben található, de nem a másik feltételben. A következő szakaszban látható egy példa a feltételes kiértékelésre kifejezésekre.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) azt szemlélteti, hogyan használható a `if` függvény.

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

Az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| yesOutput | Sztring | igen |
| nincs kimenet | Sztring | nem |
| objectOutput | Objektum | {"test": "érték1"} |

A következő [példa](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) azt mutatja be, hogyan használható ez a függvény olyan kifejezésekkel, amelyek csak feltételes érvényességgel rendelkeznek.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "condition": "[not(empty(parameters('logAnalytics')))]",
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
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>nem

`not(arg1)`

Logikai érték konvertálása az ellenkező értékre.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Az átalakítandó érték. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** értéket ad eredményül, ha a paraméter **hamis**. **Hamis** értéket ad vissza, ha a paraméter **igaz**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a logikai függvények használatát mutatja be.

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

Az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False (Hamis) |
| orExampleOutput | Bool | True (Igaz) |
| notExampleOutput | Bool | False (Hamis) |

A következő [példában](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a sablon **nem** [egyenlő](template-functions-comparison.md#equals).

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

Az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True (Igaz) |

## <a name="or"></a>vagy

`or(arg1, arg2, ...)`

Ellenőrzi, hogy a paraméter értéke igaz-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Az első érték, amely alapján ellenőrizhető, hogy igaz-e. |
| arg2 |Igen |logikai |A második érték, amely alapján ellenőrizhető, hogy igaz-e. |
| További argumentumok |Nem |logikai |További argumentumok, amelyek alapján ellenőrizhető, hogy igazak-e. |

### <a name="return-value"></a>Vrácená hodnota

**Igaz** értéket ad vissza, ha bármelyik érték igaz; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a logikai függvények használatát mutatja be.

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

Az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False (Hamis) |
| orExampleOutput | Bool | True (Igaz) |
| notExampleOutput | Bool | False (Hamis) |

## <a name="next-steps"></a>Következő lépések

* Egy Azure Resource Manager sablonban található részekkel kapcsolatos leírást a következő témakörben talál: [Azure Resource Manager sablonok készítése](template-syntax.md).
* Több sablon egyesítéséhez tekintse meg [a csatolt sablonok használata Azure Resource Manager használatával](linked-templates.md)című témakört.
* Ha egy adott típusú erőforrás létrehozásakor meghatározott számú alkalommal szeretné megismételni a problémát, tekintse meg a [Azure Resource Manager erőforrások több példányának létrehozása](copy-resources.md)című témakört.
* A létrehozott sablon üzembe helyezésével kapcsolatban lásd: [alkalmazások központi telepítése Azure Resource Manager sablonnal](deploy-powershell.md).

