---
title: Sablon functions – logikai
description: A Azure Resource Manager-sablonban a logikai értékek meghatározásához használandó függvények leírása.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8fe1c00240fc24c3c1454b118f9e0d9a9d54fe4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84677389"
---
# <a name="logical-functions-for-arm-templates"></a>Az ARM-sablonok logikai funkciói

A Resource Manager számos funkciót biztosít a Azure Resource Manager-(ARM-) sablonokban való összehasonlításhoz.

* [and](#and)
* [logikai](#bool)
* [Ha](#if)
* [nem](#not)
* [vagy](#or)

## <a name="and"></a>és

`and(arg1, arg2, ...)`

Ellenőrzi, hogy az összes paraméter értéke igaz-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |boolean |Az első érték, amely alapján ellenőrizhető, hogy igaz-e. |
| arg2 |Igen |boolean |A második érték, amely alapján ellenőrizhető, hogy igaz-e. |
| További argumentumok |Nem |boolean |További argumentumok, amelyek alapján ellenőrizhető, hogy igazak-e. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha az összes érték igaz; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a logikai függvények használatát mutatja be.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Logikai | Hamis |
| orExampleOutput | Logikai | Igaz |
| notExampleOutput | Logikai | Hamis |

## <a name="bool"></a>logikai

`bool(arg1)`

A paramétert logikai értékre alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc vagy int |A logikai értékké konvertálandó érték. |

### <a name="return-value"></a>Visszatérési érték
Az átalakított érték logikai értéke.

### <a name="examples"></a>Példák

Az alábbi [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) azt szemlélteti, hogyan használható a bool karakterlánc vagy egész számmal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Az előző példában az alapértelmezett értékekkel rendelkező kimenet a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| trueString | Logikai | Igaz |
| falseString | Logikai | Hamis |
| trueInt | Logikai | Igaz |
| falseInt | Logikai | Hamis |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Egy értéket ad vissza, attól függően, hogy a feltétel igaz vagy hamis.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| feltétel |Igen |boolean |Az érték, amely alapján ellenőrizhető, hogy igaz vagy hamis. |
| trueValue |Igen | karakterlánc, int, Object vagy Array |A feltétel teljesülésekor visszaadott érték. |
| falseValue |Igen | karakterlánc, int, Object vagy Array |A feltétel hamis állapotának visszaadására szolgáló érték. |

### <a name="return-value"></a>Visszatérési érték

A második paramétert adja vissza, ha az első paraméter **igaz**. Ellenkező esetben a harmadik paramétert adja vissza.

### <a name="remarks"></a>Megjegyzések

Ha a feltétel **igaz**, csak a True érték lesz kiértékelve. Ha a feltétel **hamis**, csak a hamis értéket értékeli ki a rendszer. Az **IF** függvénnyel olyan kifejezéseket is tartalmazhat, amelyek csak feltételes érvényességgel rendelkeznek. Hivatkozhat például egy olyan erőforrásra, amely egy feltételben található, de nem a másik feltételben. A következő szakaszban látható egy példa a feltételes kiértékelésre kifejezésekre.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) a függvény használatát mutatja be `if` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| yesOutput | Sztring | igen |
| nincs kimenet | Sztring | nem |
| objectOutput | Objektum | {"test": "érték1"} |

A következő [példa](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) azt mutatja be, hogyan használható ez a függvény olyan kifejezésekkel, amelyek csak feltételes érvényességgel rendelkeznek.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

## <a name="not"></a>not

`not(arg1)`

Logikai érték konvertálása az ellenkező értékre.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |boolean |Az átalakítandó érték. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad eredményül, ha a paraméter **hamis**. **Hamis** értéket ad vissza, ha a paraméter **igaz**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a logikai függvények használatát mutatja be.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Logikai | Hamis |
| orExampleOutput | Logikai | Igaz |
| notExampleOutput | Logikai | Hamis |

A következő [példában](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a sablon **nem** [egyenlő](template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

Az előző példa kimenete a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkNotEquals | Logikai | Igaz |

## <a name="or"></a>vagy

`or(arg1, arg2, ...)`

Ellenőrzi, hogy a paraméter értéke igaz-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |boolean |Az első érték, amely alapján ellenőrizhető, hogy igaz-e. |
| arg2 |Igen |boolean |A második érték, amely alapján ellenőrizhető, hogy igaz-e. |
| További argumentumok |Nem |boolean |További argumentumok, amelyek alapján ellenőrizhető, hogy igazak-e. |

### <a name="return-value"></a>Visszatérési érték

**Igaz** értéket ad vissza, ha bármelyik érték igaz; Ellenkező esetben **hamis**.

### <a name="examples"></a>Példák

A következő [példa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a logikai függvények használatát mutatja be.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| andExampleOutput | Logikai | Hamis |
| orExampleOutput | Logikai | Igaz |
| notExampleOutput | Logikai | Hamis |

## <a name="next-steps"></a>További lépések

* Egy Azure Resource Manager sablonban található részekről az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk nyújt tájékoztatást.

