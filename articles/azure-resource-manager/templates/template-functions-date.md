---
title: Sablon functions – dátum
description: A Azure Resource Manager-sablonban a dátumokkal végzett munkához használandó függvényeket ismerteti.
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: abdc88ce15279b90f8f9dc05a38a2ae236498f12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86058044"
---
# <a name="date-functions-for-arm-templates"></a>Az ARM-sablonokhoz tartozó Date functions

A Resource Manager a következő függvényeket biztosítja a dátumoknak a Azure Resource Manager (ARM) sablonokban való használatához:

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Az időtartamot adja hozzá egy alapértékhez. Az ISO 8601 formátuma várható.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| base | Igen | sztring | A Hozzáadás kezdő datetime értéke. Az [ISO 8601 timestamp formátumot](https://en.wikipedia.org/wiki/ISO_8601)használja. |
| duration | Igen | sztring | Az alaphoz hozzáadandó idő érték. Ez lehet negatív érték. Az [ISO 8601 időtartam formátuma](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| formátumban | Nem | sztring | A dátum és idő eredményének kimeneti formátuma Ha nincs megadva, a rendszer az alapérték formátumát használja. Használjon [szabványos formázó karakterláncokat](/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [Egyéni formázó karakterláncokat](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Visszatérési érték

A DateTime érték, amely az időtartam értékének az alapértékhez való hozzáadását eredményezi.

### <a name="examples"></a>Példák

Az alábbi példában az időértékek hozzáadásának különböző módjai láthatók.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

Ha az előző sablon üzembe helyezése alapidővel történik `2020-04-07 14:53:14Z` , a kimenet a következőket eredményezi:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| add3Years | Sztring | 4/7/2023 2:53:14 PM |
| subtract9Days | Sztring | 3/29/2020 2:53:14 PM |
| add1Hour | Sztring | 4/7/2020 3:53:14 PM |

A következő példa azt mutatja be, hogyan állíthatja be az automatizálási ütemterv kezdési idejét.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Az aktuális (UTC) dátum és idő értéket adja vissza a megadott formátumban. Ha nincs megadva formátum, a rendszer az ISO 8601 (yyyyMMddTHHmmssZ) formátumot használja. **Ez a függvény csak a paraméter alapértelmezett értékében használható.**

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| formátumban |Nem |sztring |A karakterláncra konvertálandó URI-kódolású érték. Használjon [szabványos formázó karakterláncokat](/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [Egyéni formázó karakterláncokat](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt csak egy paraméter alapértelmezett értékére használhatja egy kifejezésen belül. Ha ezt a funkciót a sablonban bárhol máshol használja, hibaüzenetet ad vissza. A függvény nem engedélyezett a sablon más részeiben, mert minden egyes híváskor más értéket ad vissza. Ugyanazon sablon ugyanazon paraméterekkel való üzembe helyezése nem eredményezi megbízhatóan ugyanazt az eredményt.

Ha a hibát egy korábbi sikeres központi telepítésre történő [visszaállításra](rollback-on-error.md) használja, és a korábbi központi telepítés tartalmaz egy utcNow-t használó paramétert, a paraméter nem lett újraértékelve. Ehelyett a korábbi központi telepítés paraméterének értékét a rendszer automatikusan újra felhasználja a visszaállítási telepítésben.

Ügyeljen arra, hogy egy alapértelmezett érték esetén a utcNow függvényre támaszkodó sablont telepítse újra. Ha újratelepíti, és nem ad meg értéket a paraméterhez, a függvény újraértékelése megtörténik. Ha egy meglévő erőforrást nem új létrehozása helyett szeretne frissíteni, adja át a paraméter értékét a korábbi telepítésből.

### <a name="return-value"></a>Visszatérési érték

Az aktuális UTC datetime érték.

### <a name="examples"></a>Példák

A következő példa sablon a DateTime érték különböző formátumait jeleníti meg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Az előző példa kimenete az egyes központi telepítések esetében változik, de a következőhöz hasonló lesz:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| utcOutput | sztring | 20190305T175318Z |
| utcShortOutput | sztring | 03/05/2019 |
| utcCustomOutput | sztring | 3 5 |

A következő példa azt szemlélteti, hogyan használható a függvény értéke a címke értékének beállításakor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>További lépések

* Egy Azure Resource Manager sablonban található részekről az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk nyújt tájékoztatást.
