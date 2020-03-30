---
title: Sablonfüggvények - logikai
description: Az Azure Resource Manager-sablonban a logikai értékek meghatározásához használandó függvények ismertetése.
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f058baa32e5f93a4177913287a5e9873fa7a9acb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156310"
---
# <a name="logical-functions-for-arm-templates"></a>Logikai függvények ARM sablonokhoz

A Resource Manager számos funkciót biztosít az Azure Resource Manager (ARM) sablonjaiban végzett összehasonlításhoz.

* [és](#and)
* [Bool](#bool)
* [Ha](#if)
* [Nem](#not)
* [Vagy](#or)

## <a name="and"></a>és

`and(arg1, arg2, ...)`

Ellenőrzi, hogy minden paraméterérték igaz-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Az első érték, amely ellenőrzi, hogy igaz-e. |
| arg2 |Igen |logikai |A második érték, amely ellenőrzi, hogy igaz-e. |
| további argumentumok |Nem |logikai |További argumentumok annak ellenőrzésére, hogy igaz-e. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz értéket** ad vissza, ha minden érték igaz; ellenkező **esetben, Hamis**.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) bemutatja a logikai függvények használatát.

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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| és ExampleOutput | Logikai | False (Hamis) |
| orExampleOutput | Logikai | True (Igaz) |
| notExampleOutput | Logikai | False (Hamis) |

## <a name="bool"></a>Bool

`bool(arg1)`

A paramétert logikai változóvá alakítja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |karakterlánc vagy int |Logikai változóvá konvertálandó érték. |

### <a name="return-value"></a>Visszatérítési érték
A konvertált érték logikai értéke.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) bemutatja, hogyan használható a bool karakterlánccal vagy egész számmal.

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| trueString | Logikai | True (Igaz) |
| falseString | Logikai | False (Hamis) |
| trueInt | Logikai | True (Igaz) |
| falseInt | Logikai | False (Hamis) |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Értéket ad vissza attól függően, hogy egy feltétel igaz vagy hamis.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| Feltétel |Igen |logikai |Az érték, hogy ellenőrizze, hogy igaz vagy hamis. |
| trueValue érték |Igen | karakterlánc, int, objektum vagy tömb |A feltétel teljesülése esetén visszaadandó érték. |
| falseValue (hamis érték) |Igen | karakterlánc, int, objektum vagy tömb |A feltétel hamis állapotában visszaadandó érték. |

### <a name="return-value"></a>Visszatérítési érték

A második paramétert adja eredményül, ha az első paraméter **Igaz**; ellenkező esetben a harmadik paramétert adja vissza.

### <a name="remarks"></a>Megjegyzések

Ha a feltétel **Igaz,** csak a valós értéket értékeli ki a program. Ha a feltétel **Hamis,** csak a hamis értéket értékeli ki a rendszer. Az **if** függvénnyel olyan kifejezéseket is felvehet, amelyek csak feltételesen érvényesek. Hivatkozhat például egy olyan erőforrásra, amely az egyik feltétel alatt létezik, de a másik feltétel alatt nem. A kifejezések feltételes kiértékelésére a következő szakaszban látható példa.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) bemutatja `if` a függvény használatát.

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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| yesKimenet | Sztring | igen |
| noOutput | Sztring | nem |
| objectOutput | Objektum | { "teszt": "érték1" } |

A következő [példasablon](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) bemutatja, hogyan használható ez a függvény olyan kifejezésekkel, amelyek csak feltételesen érvényesek.

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

## <a name="not"></a>not

`not(arg1)`

A logikai értéket az ellenkező értékre konvertálja.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |A konvertálandó érték. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz** értéket ad vissza, ha a paraméter **Hamis**. **Hamis** értéket ad vissza, ha a paraméter **Igaz**.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) bemutatja a logikai függvények használatát.

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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| és ExampleOutput | Logikai | False (Hamis) |
| orExampleOutput | Logikai | True (Igaz) |
| notExampleOutput | Logikai | False (Hamis) |

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) **nem** [egyenlő](template-functions-comparison.md#equals).

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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| checkNotEquals | Logikai | True (Igaz) |

## <a name="or"></a>vagy

`or(arg1, arg2, ...)`

Ellenőrzi, hogy a paraméterérték igaz-e.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| arg1 |Igen |logikai |Az első érték, amely ellenőrzi, hogy igaz-e. |
| arg2 |Igen |logikai |A második érték, amely ellenőrzi, hogy igaz-e. |
| további argumentumok |Nem |logikai |További argumentumok annak ellenőrzésére, hogy igaz-e. |

### <a name="return-value"></a>Visszatérítési érték

**Igaz értéket** ad vissza, ha bármely érték igaz; ellenkező **esetben, Hamis**.

### <a name="examples"></a>Példák

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) bemutatja a logikai függvények használatát.

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

| Név | Típus | Érték |
| ---- | ---- | ----- |
| és ExampleOutput | Logikai | False (Hamis) |
| orExampleOutput | Logikai | True (Igaz) |
| notExampleOutput | Logikai | False (Hamis) |

## <a name="next-steps"></a>További lépések

* Az Azure Resource Manager-sablon szakaszainak leírását az [Azure Resource Manager-sablonok készítése című témakörben találja.](template-syntax.md)
* Több sablon egyesítéséhez olvassa el [a Csatolt sablonok használata az Azure Resource Manager rel.](linked-templates.md)
* Ha egy erőforrástípus létrehozásakor meghatározott számú alkalommal szeretne meghaladni, olvassa [el az Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakört.](copy-resources.md)
* A létrehozott sablon központi telepítéséről az Alkalmazás üzembe helyezése az [Azure Resource Manager sablonnal című témakörben](deploy-powershell.md)olvashat.

