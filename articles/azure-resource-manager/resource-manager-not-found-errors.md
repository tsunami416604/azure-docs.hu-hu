---
title: "Azure-erőforrás nem található a hibák |} Microsoft Docs"
description: "Ismerteti, hogyan javítsa a hibákat, ha egy erőforrás nem található."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Nem található az Azure-erőforrások hibák megoldása

Ez a cikk ismerteti a hibák jelentkezhetnek, ha egy erőforrás nem található a telepítés során. 

## <a name="symptom"></a>Jelenség

Ha a sablont, amely nem oldható fel egy erőforrás nevét tartalmazza, hasonló hibaüzenetet kap:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Ha úgy próbálja használni a [hivatkozás](resource-group-template-functions-resource.md#reference) vagy [listKeys](resource-group-template-functions-resource.md#listkeys) működik együtt a erőforrása, amely nem oldható fel, a következő hibaüzenetet kapja:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Ok

Erőforrás-kezelő kell beolvasni az erőforrás tulajdonságait, de nem tudja azonosítani az erőforrást az előfizetésében.

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1 megoldás

Ha megpróbálja telepíteni a hiányzó erőforrást a sablonban, ellenőrizze, hogy szükséges-e hozzáadjon egy függőséget. Erőforrás-kezelő optimalizálja a központi telepítési erőforrások létrehozásával párhuzamosan, amikor lehetséges. Ha egy erőforrást egy másik erőforrás után kell telepíteni, kell használnia a **dependsOn** elem az egyéb erőforrás-függőség létrehozása a sablonban. Például a webes alkalmazás telepítésekor az App Service-csomag léteznie kell. Ha nem adott meg, hogy a webalkalmazás az App Service-csomag függ-e, erőforrás-kezelő mindkét erőforrás egyszerre hoz létre. Hibaüzenet jelenik meg, amely meghatározza, hogy, hogy az App Service-csomag erőforrás nem található, mert nem létezik még a egyik tulajdonságának beállítása a webalkalmazás. Megakadályozható, hogy ezt a hibát úgy, hogy a függőséget az web app alkalmazásban.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Függőségi hiba elhárításával kapcsolatos javaslatok, lásd: [ellenőrizze a telepítési sorrendjét](resource-manager-troubleshoot-tips.md#check-deployment-sequence).

### <a name="solution-2"></a>Megoldás 2

Amikor az erőforrás létezik-e egy másik erőforráscsoportban található, mint a telepítendő, használja a [resourceId függvény](resource-group-template-functions-resource.md#resourceid) el az erőforrás teljesen minősített neve.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>Megoldás 3

Keressen egy kifejezés, amely magában foglalja a [hivatkozás](resource-group-template-functions-resource.md#reference) függvény. Megadja az értékeket e az erőforrás van ugyanazon a sablonban, erőforráscsoport és előfizetés függően változhat. A ellenőrizze, hogy meg van adva a szükséges paraméterértékeket a forgatókönyvéhez. Ha az erőforrás egy másik erőforráscsoportban található, adja meg a teljes erőforrás-azonosító. Például hivatkozhat egy másik erőforráscsoportban található a tárfiók, használja:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```