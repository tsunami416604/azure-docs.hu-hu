---
title: Az erőforrás nem található hibát
description: Ez a témakör ismerteti, hogyan háríthatók el a hibák, ha egy erőforrás nem található, amikor egy Azure Resource Manager-sablonnal telepíti.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773252"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Az Azure-erőforrások nem talált hibáinak elhárítása

Ez a cikk ismerteti a hibákat, amelyek akkor jelenhetnek meg, ha egy erőforrás nem található a központi telepítés során.

## <a name="symptom"></a>Hibajelenség

Ha a sablon olyan erőforrás nevét tartalmazza, amely nem oldható fel, a következőhöz hasonló hibaüzenet jelenik meg:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Ha olyan [erőforrással](template-functions-resource.md#reference) használja a referencia- vagy [listabillentyűket,](template-functions-resource.md#listkeys) amely nem oldható fel, a következő hibaüzenet jelenik meg:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Ok

Az Erőforrás-kezelőnek le kell kérnie egy erőforrás tulajdonságait, de nem tudja azonosítani az erőforrást az előfizetésben.

## <a name="solution-1---set-dependencies"></a>1. megoldás - függőségek beállítása

Ha a hiányzó erőforrást próbálja telepíteni a sablonban, ellenőrizze, hogy hozzá kell-e adnia egy függőséget. Az Erőforrás-kezelő optimalizálja a telepítést az erőforrások párhuzamos létrehozásával, ha lehetséges. Ha egy erőforrást egy másik erőforrás után kell telepíteni, akkor a **dependsOn** elemet kell használnia a sablonban. Például egy webalkalmazás telepítésekor az App Service-csomag léteznie kell. Ha még nem adta meg, hogy a webalkalmazás az App Service-csomagtól függ, az Erőforrás-kezelő mindkét erőforrást egyszerre hozza létre. Hibaüzenetet kap arról, hogy az App Service-csomag erőforrás nem található, mert még nem létezik, amikor megpróbál beállítani egy tulajdonságot a webalkalmazásban. Ezt a hibát a webalkalmazás függőségének beállításával előzheti meg.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

De szeretné elkerülni a függőségek beállítását, amelyek nincsenek szükségesek. Ha szükségtelen függőségek, meghosszabbítja a központi telepítés időtartamát azáltal, hogy megakadályozza, hogy az erőforrások, amelyek nem függnek egymástól párhuzamosan üzembe helyezése. Emellett körkörös függőségeket is létrehozhat, amelyek blokkolják a központi telepítést. A [referencia](template-functions-resource.md#reference) függvény és [a lista*](template-functions-resource.md#list) függvény implicit függőséget hoz létre a hivatkozott erőforrástól, ha az erőforrás ugyanabban a sablonban van telepítve, és a neve (nem erőforrás-azonosító) hivatkozik rá. Ezért előfordulhat, hogy több függősége van, mint a **dependsOn** tulajdonságban megadott függőségeknek. Az [erőforrásazonosító](template-functions-resource.md#resourceid) függvény nem hoz létre implicit függőséget, és nem ellenőrzi, hogy az erőforrás létezik-e. A [referenciafüggvény](template-functions-resource.md#reference) és [a lista*](template-functions-resource.md#list) függvények nem hoznak létre implicit függőséget, ha az erőforrásra az erőforrásazonosítója hivatkozik. Implicit függőség létrehozásához adja át az ugyanabban a sablonban üzembe helyezett erőforrás nevét.

Ha függőségi problémákat lát, betekintést kell nyernie az erőforrások központi telepítésének sorrendjébe. A telepítési műveletek sorrendjének megtekintése:

1. Válassza ki az erőforráscsoport telepítési előzményeit.

   ![telepítési előzmények kiválasztása](./media/error-not-found/select-deployment.png)

2. Válasszon ki egy központi telepítést az előzmények közül, és válassza az **Események**lehetőséget.

   ![telepítési események kiválasztása](./media/error-not-found/select-deployment-events.png)

3. Vizsgálja meg az egyes erőforrások eseményeinek sorrendjét. Ügyeljen az egyes műveletek állapotára. Az alábbi képen például három párhuzamosan üzembe helyezett tárfiók látható. Figyelje meg, hogy a három tárfiókok egyszerre indulnak el.

   ![párhuzamos telepítés](./media/error-not-found/deployment-events-parallel.png)

   A következő képen három tárfiók látható, amelyek nincsenek párhuzamosan telepítve. A második tárfiók az első tárfióktól függ, a harmadik pedig a második tárfióktól. Az első tárfiók elindul, elfogadott és befejeződött, mielőtt a következő elindul.

   ![szekvenciális telepítés](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>2. megoldás – erőforrás bekésezése különböző erőforráscsoportból

Ha az erőforrás egy másik erőforráscsoportban található, mint amelybe telepítve van, használja az [erőforrásazonosító függvényt](template-functions-resource.md#resourceid) az erőforrás teljesen minősített nevének leéséhez.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>3. megoldás - referenciafunkció ellenőrzése

Keressen egy olyan kifejezést, amely tartalmazza a [referencia](template-functions-resource.md#reference) függvényt. A megadott értékek attól függően változnak, hogy az erőforrás ugyanabban a sablonban, erőforráscsoportban és előfizetésben található-e. Ellenőrizze, hogy megadja-e a forgatókönyvhöz szükséges paraméterértékeket. Ha az erőforrás egy másik erőforráscsoportban van, adja meg a teljes erőforrás-azonosítót. Ha például egy másik erőforráscsoport ban lévő tárfiókra szeretne hivatkozni, használja a következőket:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>4. megoldás – felügyelt identitás beszereznie az erőforrásból

Ha olyan erőforrást telepít, amely implicit módon létrehoz egy [felügyelt identitást,](../../active-directory/managed-identities-azure-resources/overview.md)meg kell várnia, amíg az erőforrás üzembe kerül, mielőtt behívná a felügyelt identitás értékeit. Ha átadja a felügyelt identitás nevet a [referencia](template-functions-resource.md#reference) függvénynek, az Erőforrás-kezelő megpróbálja feloldani a hivatkozást az erőforrás és az identitás üzembe helyezése előtt. Ehelyett adja át annak az erőforrásnak a nevét, amelyre az identitás vonatkozik. Ez a megközelítés biztosítja az erőforrás és a felügyelt identitás üzembe helyezése előtt Erőforrás-kezelő feloldja a referencia függvényt.

A referencia függvényben `Full` az összes tulajdonság leéséhez használható, beleértve a felügyelt identitást is.

Például a bérlői azonosító lekérni egy felügyelt identitás, amely egy virtuálisgép méretezési csoport, használja:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```