---
title: Az erőforrás nem található hibák
description: Ismerteti, Hogyan oldhatók meg a hibák, ha egy Azure Resource Manager sablonnal való üzembe helyezéskor nem található erőforrás.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76773252"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Nem található az Azure-erőforrások hibáinak elhárítása

Ez a cikk azokat a hibákat ismerteti, amelyeket akkor láthat, ha egy erőforrás nem található az üzembe helyezés során.

## <a name="symptom"></a>Hibajelenség

Ha a sablon egy nem feloldható erőforrás nevét tartalmazza, a következőhöz hasonló hibaüzenet jelenik meg:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Ha a [Reference](template-functions-resource.md#reference) vagy a [listkeys műveletének beolvasása](template-functions-resource.md#listkeys) függvényt olyan erőforrással használja, amely nem oldható fel, a következő hibaüzenet jelenik meg:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Ok

A Resource Managernek le kell kérnie egy erőforrás tulajdonságait, de nem tudja azonosítani az erőforrást az előfizetésében.

## <a name="solution-1---set-dependencies"></a>1. megoldás – függőségek beállítása

Ha a hiányzó erőforrást a sablonban próbálja telepíteni, ellenőrizze, hogy hozzá kell-e adnia egy függőséget. A Resource Manager optimalizálja az üzembe helyezést az erőforrások párhuzamos létrehozásával, ha lehetséges. Ha egy erőforrást egy másik erőforrás után kell telepíteni, akkor a sablon **dependsOn** elemét kell használnia. Egy webalkalmazás telepítésekor például a App Service tervnek léteznie kell. Ha nem adta meg, hogy a webalkalmazás a App Service-csomagtól függ, a Resource Manager mindkét erőforrást egyszerre hozza létre. Hibaüzenet jelenik meg arról, hogy a App Service-csomag erőforrása nem található, mert még nem létezik, amikor a webalkalmazásban tulajdonságot próbál beállítani. Ezt a hibát a webalkalmazás függőségének beállításával megakadályozhatja.

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

Azonban érdemes elkerülni a nem szükséges függőségek beállítását. Ha szükségtelen függőségekkel rendelkezik, akkor meghosszabbítja a telepítés időtartamát azáltal, hogy megakadályozza, hogy azok az erőforrások, amelyek nem függnek egymástól, párhuzamosan legyenek telepítve. Emellett körkörös függőségeket is létrehozhat, amelyek letiltják az üzemelő példányt. A [hivatkozási](template-functions-resource.md#reference) függvény és a [lista *](template-functions-resource.md#list) függvények implicit függőséget hoznak létre a hivatkozott erőforráson, ha az adott erőforrás ugyanabban a sablonban van telepítve, és a neve (nem erőforrás-azonosító) hivatkozik rá. Ezért előfordulhat, hogy több függősége van, mint a **dependsOn** tulajdonságban megadott függőségek. A [resourceId](template-functions-resource.md#resourceid) függvény nem hoz létre implicit függőséget, vagy ellenőrzi, hogy az erőforrás létezik-e. A [hivatkozási](template-functions-resource.md#reference) függvény és a [lista *](template-functions-resource.md#list) függvények nem hoznak létre implicit függőséget, ha az erőforrást az erőforrás-azonosítója hivatkozik rá. Implicit függőség létrehozásához adja meg az ugyanabban a sablonban üzembe helyezett erőforrás nevét.

Ha megtekinti a függőségi problémákat, meg kell ismernie az erőforrás-telepítés sorrendjét. Az üzembe helyezési műveletek sorrendjének megtekintése:

1. Válassza ki az erőforráscsoport üzembe helyezési előzményeit.

   ![telepítési előzmények kiválasztása](./media/error-not-found/select-deployment.png)

2. Válasszon ki egy központi telepítést az előzmények közül, és válassza az **események**lehetőséget.

   ![telepítési események kiválasztása](./media/error-not-found/select-deployment-events.png)

3. Vizsgálja meg az egyes erőforrások eseményeinek sorozatot. Ügyeljen az egyes műveletek állapotára. Az alábbi képen például három, párhuzamosan telepített Storage-fiók látható. Figyelje meg, hogy a három Storage-fiók egyszerre van elindítva.

   ![párhuzamos üzembe helyezés](./media/error-not-found/deployment-events-parallel.png)

   A következő képen három olyan Storage-fiók látható, amelyek nincsenek párhuzamosan telepítve. A második Storage-fiók az első Storage-fióktól függ, és a harmadik Storage-fiók a második Storage-fióktól függ. Az első Storage-fiók elindítva, elfogadva és befejeződött a következő elindítása előtt.

   ![szekvenciális üzembe helyezés](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>2. megoldás – erőforrás beolvasása különböző erőforráscsoporthoz

Ha az erőforrás létezik egy másik erőforráscsoporthoz, mint amelyet a központilag telepített, akkor a [resourceId függvénnyel](template-functions-resource.md#resourceid) kérheti le az erőforrás teljes nevét.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>3. megoldás – a hivatkozás-ellenőrzési funkció

Keresse meg a [hivatkozási](template-functions-resource.md#reference) függvényt tartalmazó kifejezést. Az Ön által megadott értékek attól függően változnak, hogy az erőforrás ugyanabban a sablonban, erőforráscsoporthoz és előfizetésben található-e. Ellenőrizze, hogy az adott forgatókönyvhöz szükséges paramétereket adja meg. Ha az erőforrás egy másik erőforráscsoporthoz esik, adja meg a teljes erőforrás-azonosítót. Ha például egy másik erőforráscsoport Storage-fiókjára szeretne hivatkozni, használja a következőt:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>4. megoldás – felügyelt identitás beszerzése az erőforrásból

Ha olyan erőforrást helyez üzembe, amely implicit módon létrehoz egy [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md), meg kell várnia, amíg az erőforrás telepítve lesz, mielőtt beolvassa az értékeket a felügyelt identitáson. Ha átadja a felügyelt identitás nevét a [hivatkozási](template-functions-resource.md#reference) függvénynek, a Resource Manager megkísérli a hivatkozás feloldását az erőforrás és az identitás üzembe helyezése előtt. Ehelyett adja meg annak az erőforrásnak a nevét, amelyre az identitás vonatkozik. Ez a megközelítés biztosítja az erőforrás és a felügyelt identitás üzembe helyezését, mielőtt a Resource Manager feloldja a hivatkozási funkciót.

A Reference függvényben használja `Full` az parancsot az összes tulajdonság beolvasásához, beleértve a felügyelt identitást is.

Ha például egy virtuálisgép-méretezési csoportra alkalmazott felügyelt identitás bérlői AZONOSÍTÓját szeretné lekérni, használja a következőt:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```