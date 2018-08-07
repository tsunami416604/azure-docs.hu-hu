---
title: Azure-erőforrás nem található a hibák |} A Microsoft Docs
description: Ismerteti, hogyan lehet javítsa ki a hibákat, amikor egy erőforrás nem található.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: 176de6f19274dfd8a6cf0335bb4cf16a8baa874b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525344"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Nem található az Azure-erőforrások hibák megoldása

Ez a cikk ismerteti a hibákat, amikor egy erőforrás nem található üzembe helyezés során látni.

## <a name="symptom"></a>Jelenség

Amikor a sablont is tartalmaz, amely nem oldható fel egy erőforrás nevét, a következőhöz hasonló hibaüzenetet kap:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Ha használja a [referencia](resource-group-template-functions-resource.md#reference) vagy [listkeys műveletének](resource-group-template-functions-resource.md#listkeys) a functions és a egy erőforrás, amely nem oldható fel, a következő hibaüzenetet kapja:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Ok

Erőforrás-kezelő erőforrás tulajdonságainak lekéréséhez szükséges, de nem tudja azonosítani az erőforrást az előfizetésében.

## <a name="solution-1---set-dependencies"></a>Függőségek beállítása 1 - megoldás

Ha a hiányzó erőforrást, a sablon üzembe helyezéséhez, ellenőrizze, hogy szükséges-e hozzáadjon egy függőséget. Resource Manager üzembe helyezési optimalizálja-erőforrások létrehozását, párhuzamos, amikor csak lehetséges. Ha egy erőforrás egy másik erőforrás után kell telepíteni, szeretné-e használni a **dependsOn** elem a sablonban. Például egy webes alkalmazás üzembe helyezésekor, az App Service-csomag léteznie kell. Ha nem adott meg, hogy a webalkalmazás az App Service-csomag függ, a Resource Manager mindkét erőforrás létrehoz egy időben. Hibaüzenetet kap arról, hogy az App Service csomag erőforrás nem található, mert nem létezik még megkísérelte beállítani egy tulajdonságot a webalkalmazásban. Ez a hiba megakadályozása a függőség beállítása a webalkalmazásban.

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

Azonban el szeretné kerülni, amelyek nem szükségesek a függőségek beállítása. A szükségtelen függőségekkel rendelkezik, amikor a központi telepítés tartamára meghosszabbítása szerint megakadályozza az erőforrások, amelyek nem a párhuzamos parancsfájlműveletekkel egymástól függenek. Emellett előfordulhat, hogy hozzon létre, amely az üzembe helyezés letiltása körkörös függőségi. A [referencia](resource-group-template-functions-resource.md#reference) függvény és [lista *](resource-group-template-functions-resource.md#list) funkciók az implicit függ a hivatkozott erőforrás hoz létre, ha ennek az erőforrásnak ugyanabban a sablonban telepítve van, és hivatkozik a nevét (nem erőforrás-azonosító ). Ezért előfordulhat, mint a függőségek megadott további függőségek a **dependsOn** tulajdonság. A [resourceId](resource-group-template-functions-resource.md#resourceid) függvény létrehozása egy implicit függőség nem, vagy ellenőrizze, hogy létezik-e az erőforrás. A [referencia](resource-group-template-functions-resource.md#reference) függvény és [lista *](resource-group-template-functions-resource.md#list) funkciók nem létrehozása egy implicit függőség, amikor az erőforrás hivatkozik az erőforrás-azonosítója. Hozzon létre egy implicit függőség, át kell adnia ugyanazt a sablont az üzembe helyezett erőforrás nevére.

Függőségi problémák megjelenésekor kell betekintést nyerhet az erőforrások üzembe helyezésének sorrendjét. Üzembehelyezési műveletek sorrendjét megtekintése:

1. Válassza ki az erőforráscsoport üzembe helyezési előzményeit.

   ![Válassza ki az üzembe helyezési előzmények](./media/resource-manager-not-found-errors/select-deployment.png)

2. Jelölje ki a telepítést az előzményekben tekintheti át, és válassza ki **események**.

   ![Válassza ki a központi telepítési események](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Vizsgálja meg az egyes erőforrások események sorozatát. Az egyes műveletek állapota figyelmet fordítania. Ha például az alábbi képen látható három tárfiókot tartalmaz, amelyek telepítve párhuzamosan. Figyelje meg, hogy a három tárfiókot tartalmaz egy időben elindulnak.

   ![párhuzamos üzembe helyezés](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   A következő képen három tárfiókot tartalmaz, amelyek nincsenek telepítve párhuzamosan. A második tárfiók függ, hogy az első storage-fiókját, és a harmadik tárfiók attól függ, a második tárfiók. Az első storage-fiók elindult, fogadja, és befejeződött a következő indítása előtt.

   ![egymást követő üzembe helyezési](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>2. megoldás erőforrás le másik erőforráscsoportba

Ha az erőforrás már létezik egy másik erőforráscsoportban található, mint a folyamatban való üzembe helyezése, használja a [resourceId függvény](resource-group-template-functions-resource.md#resourceid) beolvasni az erőforrás a teljes nevet.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Megoldás 3. Ellenőrizze a hivatkozási funkció

Keressen egy kifejezés, amely tartalmazza a [referencia](resource-group-template-functions-resource.md#reference) függvény. Az erőforrás-e ugyanazt a sablont, erőforráscsoportban és előfizetésben található, adja meg az értékeket függ. Ellenőrizze, hogy Ön adja-e a szükséges paraméterértékeket a forgatókönyvhöz. Ha az erőforrás egy másik erőforráscsoportban található, adja meg a teljes erőforrás-azonosítója. Ha például való hivatkozáshoz egy storage-fiók egy másik erőforráscsoportban található, használja:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```