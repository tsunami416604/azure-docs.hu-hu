---
title: Azure-szülő erőforrás-hibák | Microsoft Docs
description: Ismerteti, Hogyan oldhatók meg a hibák, amikor egy Azure Resource Manager sablonban lévő szülő erőforrással dolgozik.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 197554e16e28b4928cab351838f00e1631c269fd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390238"
---
# <a name="resolve-errors-for-parent-resources"></a>A szülő erőforrások hibáinak elhárítása

Ez a cikk azokat a hibákat ismerteti, amelyekkel a szülő erőforrástól függő erőforrások telepítése végezhető el.

## <a name="symptom"></a>Hibajelenség

Ha olyan erőforrást telepít, amely egy gyermek egy másik erőforráshoz, a következő hibaüzenetet kaphatja:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Ok

Ha az egyik erőforrás gyermek egy másik erőforráshoz tartozik, a szülő erőforrásnak léteznie kell a gyermek erőforrás létrehozása előtt. A gyermek erőforrás neve határozza meg a fölérendelt erőforrással létesített kapcsolatokat. A gyermek erőforrás nevének `<parent-resource-name>/<child-resource-name>` formátumúnak kell lennie. Előfordulhat például, hogy egy SQL Database a következőként van meghatározva:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Ha a kiszolgálót és az adatbázist is ugyanabban a sablonban telepíti, de nem ad meg függőséget a kiszolgálón, előfordulhat, hogy az adatbázis központi telepítése a kiszolgáló üzembe helyezése előtt elindul. 

Ha a fölérendelt erőforrás már létezik, és nem ugyanabban a sablonban van telepítve, akkor ez a hiba akkor jelenik meg, ha a Resource Manager nem tudja hozzárendelni a gyermek erőforrást szülővel. Ez a hiba akkor fordulhat elő, ha a gyermek erőforrás formátuma nem megfelelő, vagy a gyermek erőforrás olyan erőforráscsoporthoz van telepítve, amely eltér a szülő erőforrás erőforráscsoport-csoportjához.

## <a name="solution"></a>Megoldás

Ha meg szeretné oldani ezt a hibát, ha a szülő-és alárendelt erőforrások ugyanabban a sablonban vannak telepítve, akkor vegyen fel egy függőséget.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Ha ezt a hibát szeretné megoldani, ha a fölérendelt erőforrást korábban egy másik sablonban telepítették, akkor nem kell függőséget beállítania. Ehelyett helyezze üzembe a gyermeket ugyanarra az erőforrás-csoportba, és adja meg a szülő erőforrás nevét.

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

További információ: [erőforrások üzembe helyezési sorrendjének meghatározása Azure Resource Manager-sablonokban](resource-group-define-dependencies.md).