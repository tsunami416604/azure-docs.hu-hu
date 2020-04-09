---
title: Sablonfüggvények - dátum
description: Ismerteti az Azure Resource Manager sablonban a dátumok használatához használandó függvényeket.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 75234a142b9f2fbe61c337bfeb378b47534bac79
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986277"
---
# <a name="date-functions-for-arm-templates"></a>Dátumfüggvények ARM sablonokhoz

Az Erőforrás-kezelő a következő funkciókat biztosítja az Azure Resource Manager (ARM) sablonokban a dátumok használatával való munkához:

* [dateTimeAdd](#datetimeadd)
* [utcNow között](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Időidőtartam hozzáadása egy alap datetime értékhez.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| base | Igen | sztring | A hozzáadás kezdő dátumidő-értéke. Az [ISO 8601 időbélyeg formátum használata](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Igen | sztring | Az alaphoz hozzáadandó időérték. Ez lehet negatív érték. Az [ISO 8601 időtartam formátum használata](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| Formátum | Nem | sztring | A dátumidő eredményének kimeneti formátuma. Ha nincs megadva, a program az alapérték formátumát használja. Használjon [szabványos formátumú karakterláncokat](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumú karakterláncokat.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) |

### <a name="return-value"></a>Visszatérítési érték

Az időtartam értéknek az alapértékhez való hozzáadásából eredő datetime érték.

### <a name="examples"></a>Példák

A következő példasablon az időértékek hozzáadásának különböző módjait mutatja be.

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

Ha az előző sablon alapideje a `2020-04-07 14:53:14Z`, a kimenet:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| add3Years | Sztring | 2023.04.7.04.02:53:14 |
| kivonás9Nap | Sztring | 2020.02.29. |
| add1Óra | Sztring | 2020/04/7 03:53:14 |

A következő példasablon bemutatja, hogyan állíthatja be az Automation-ütemezés kezdési idejét.

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

## <a name="utcnow"></a>utcNow között

`utcNow(format)`

Az aktuális (UTC) datetime értéket adja eredményül a megadott formátumban. Ha nincs formátum, akkor az ISO 8601 (yyyyMMddTHHmmssZ) formátumot használja. **Ez a függvény csak egy paraméter alapértelmezett értékében használható.**

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| Formátum |Nem |sztring |Az URI kódolású érték karakterlánclá konvertálása. Használjon [szabványos formátumú karakterláncokat](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [egyéni formátumú karakterláncokat.](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) |

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt csak egy paraméter alapértelmezett értékéhez használhatja egy kifejezésen belül. Ha ezt a függvényt a sablon ban bárhol használja, az hibát ad vissza. A függvény nem engedélyezett a sablon más részein, mert minden egyes megadáskor más értéket ad vissza. Ugyanazt a sablont üzembe helyezése azonos paraméterekkel nem megbízhatóan ugyanazt az eredményt.

Ha egy [korábbi sikeres központi telepítés újratelepítésének lehetőségével rendelkezik,](rollback-on-error.md)és a korábbi központi telepítés tartalmaz egy utcNow paramétert, a paraméter nem lesz újraértékelve. Ehelyett a korábbi központi telepítés paraméterértéke automatikusan újra fellesz használva a visszaállítási központi telepítésben.

Legyen óvatos egy sablon, amely támaszkodik az utcNow függvény egy alapértelmezett érték. Ha újratelepíti, és nem ad meg értéket a paraméter, a függvény újraértékeli. Ha egy meglévő erőforrást szeretne frissíteni ahelyett, hogy újat hozna létre, adja át a paraméter értékét a korábbi központi telepítésből.

### <a name="return-value"></a>Visszatérítési érték

Az aktuális UTC datetime érték.

### <a name="examples"></a>Példák

A következő példasablon a datetime érték különböző formátumait jeleníti meg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Az előző példa kimenete az egyes központi telepítésekhez változik, de hasonló lesz a következőkhöz:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| utcKimenet | sztring | 20190305T175318Z |
| utcShortOutput | sztring | 03/05/2019 |
| utcCustomOutput | sztring | 3 5 |

A következő példa bemutatja, hogyan kell használni egy értéket a függvényből címke értékének beállításakor.

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