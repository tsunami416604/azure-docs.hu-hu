---
title: Az Azure szülő erőforrás hibák |} A Microsoft Docs
description: Javítsa ki a hibákat, ha egy szülő erőforrás-használata módját ismerteti.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 19642138105f3de0e2bda3f0f80e12cf74a35709
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61059990"
---
# <a name="resolve-errors-for-parent-resources"></a>Szülő erőforrások ki a hibákat

Ez a cikk ismerteti a hibákat, előfordulhat, hogy kap egy erőforrás, amelynek van egy szülő erőforrástól függ-e üzembe helyezésekor.

## <a name="symptom"></a>Jelenség

Egy erőforrás, amely egy másik erőforrás alárendelt üzembe helyezésekor, a következő hiba jelenhet meg:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Ok

Ha egy erőforrás egy másik erőforrás gyermek, a szülő erőforrás a gyermek-erőforrás létrehozása előtt léteznie kell. A gyermek-erőforrás nevét határozza meg, hogy a kapcsolat a szülő erőforrással. A gyermek-erőforrás neve a következő formátumban kell `<parent-resource-name>/<child-resource-name>`. Ha például egy SQL Database előfordulhat, hogy meghatározása:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Ha a kiszolgáló és a ugyanazt a sablont-adatbázis üzembe helyezése, de egy függőség nem adja meg a kiszolgálón, az adatbázis központi telepítése előtt a kiszolgálón telepített is elindíthatják. 

Ha a szülő erőforrás már létezik, és ugyanabban a sablonban nincs telepítve, megjelenik a hibaüzenet, ha a Resource Manager a szülővel nem társítható a gyermek-erőforrás. Ez a hiba akkor fordulhat elő, amikor a gyermek-erőforrás nincs megfelelő formátumú, vagy a gyermek-erőforrás üzembe van helyezve, egy erőforráscsoportba, amely eltér attól az erőforráscsoport, a szülő erőforrás.

## <a name="solution"></a>Megoldás

A probléma megoldásához, ha a szülő és gyermek erőforrások telepítése ugyanazt a sablont, például egy függőséget.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

A probléma megoldásához, ha a szülő erőforrás korábban telepítve lett egy másik sablonba, függőség nem állít be. Ehelyett az alárendelt ugyanabban az erőforráscsoportban üzembe helyezése, és adja meg a szülő erőforrás nevét.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

További információkért lásd: [üzembe erőforrásokat az Azure Resource Manager-sablonokban a sorrendben](resource-group-define-dependencies.md).