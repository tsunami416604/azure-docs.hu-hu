---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: 5e483ecfcbddfcf5aa7f8a41c1ee75136c86b656
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440187"
---
Ha üzembe helyezés közben szeretne címkével ellátni egy erőforrást, adja hozzá a `tags` elemet ahhoz az erőforráshoz, amelyet éppen üzembe helyez. Adja meg a címke nevét és értékét.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Szövegkonstansérték alkalmazása a címkenévre
Az alábbi példában egy tárfiók látható két címkével (`Dept` és `Environment`), amelyek szövegkonstansértékre vannak beállítva:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

### <a name="apply-an-object-to-the-tag-element"></a>Objektum alkalmazása a címkeelemre
Megadhat olyan objektumparamétert, amely több címkét tartalmaz, majd alkalmazhatja azt az objektumot a címkeelemre. Az objektum minden tulajdonsága az erőforrás külön címkéjévé válik. Az alábbi példa egy `tagValues` nevű paramétert tartalmaz, amely a címkeelemre van alkalmazva.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tagValues": {
      "type": "object",
      "defaultValue": {
        "Dept": "Finance",
        "Environment": "Production"
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": "[parameters('tagValues')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    }
  ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>JSON-sztring alkalmazása a címkenévre

Ha több értéket szeretne tárolni egyetlen címkében, alkalmazzon a megfelelő értékeket képviselő JSON-sztringet. A teljes JSON-sztring egyetlen címkeként tárolódik, amelynek hossza nem lépheti túl a 256 karaktert. Az alábbi példában egy `CostCenter` nevű címke szerepel, amely egy JSON-sztring számos értékét tartalmazza:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```