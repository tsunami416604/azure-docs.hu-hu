---
title: Fölérendelt erőforráshibák
description: Ez a témakör azt ismerteti, hogy miként oldható meg a hibák, amikor egy Azure Resource Manager-sablonban egy szülőerőforrás-erőforrással dolgozik.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: f1847389d60ddf3c6abc70bc3309940c2246084e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154040"
---
# <a name="resolve-errors-for-parent-resources"></a>Szülőerőforrások hibáinak elhárítása

Ez a cikk azokat a hibákat ismerteti, amelyek a szülőerőforrástól függő erőforrás telepítésekor fordulhatnak elő.

## <a name="symptom"></a>Hibajelenség

Ha egy másik erőforráshoz gyermekerőforrást telepít, a következő hibaüzenet jelenhet meg:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Ok

Ha az egyik erőforrás egy másik erőforrás gyermeke, a szülő erőforrásnak léteznie kell a gyermekerőforrás létrehozása előtt. A gyermekerőforrás neve határozza meg a szülő erőforrással való kapcsolatot. A gyermekerőforrás neve formátumú. `<parent-resource-name>/<child-resource-name>` Például egy SQL-adatbázis a következőképpen definiálható:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Ha a kiszolgálót és az adatbázist is ugyanabban a sablonban telepíti, de nem ad meg függőséget a kiszolgálón, előfordulhat, hogy az adatbázis telepítése a kiszolgáló telepítése előtt elindul.

Ha a szülőerőforrás már létezik, és nincs telepítve ugyanabban a sablonban, ez a hibaüzenet jelenik meg, amikor az Erőforrás-kezelő nem tudja társítani a gyermekerőforrást a szülővel. Ez a hiba akkor fordulhat elő, ha a gyermekerőforrás nem a megfelelő formátumú, vagy a gyermek erőforrás egy olyan erőforráscsoportba van telepítve, amely eltér a fölérendelt erőforrás erőforráscsoportjától.

## <a name="solution"></a>Megoldás

Ha a szülő- és gyermekerőforrások ugyanabban a sablonban vannak telepítve, a probléma megoldásához vegyen fel függőséget.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Ha a probléma megoldásához, ha a szülő erőforrás korábban telepített egy másik sablonban, ne állítson be függőséget. Ehelyett telepítse a gyermeket ugyanarra az erőforráscsoportra, és adja meg a szülő erőforrás nevét.

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
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

További [információ: Az erőforrások azure Resource Manager-sablonokban való üzembe helyezéséhez szükséges sorrend meghatározása](define-resource-dependency.md)című témakörben olvashat.