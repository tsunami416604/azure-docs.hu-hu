---
title: "Érvénytelen a sablon Azure hibák |} Microsoft Docs"
description: "Érvénytelen a sablon hibák megoldását ismerteti."
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
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 9626b3caaa7188a4e9a37f83d1fbf091951714f4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="resolve-errors-for-invalid-template"></a>Érvénytelen a sablon által jelzett hibákat feloldása

Ez a cikk ismerteti, hogyan érvénytelen a sablon hibák.

## <a name="symptom"></a>Jelenség

A sablon telepítésekor hibaüzenetet kap:

```
Code=InvalidTemplate
Message=<varies>
```

A hibaüzenet a következő hiba típusától függ.

## <a name="cause"></a>Ok

Ez a hiba oka lehet több különböző típusú hibák. Általában magukban szintaxis vagy strukturális hiba történt a sablonban.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>1 - szintaktikai hiba megoldás

Ha a sablont nem sikerült érvényesítési jelző hibaüzenetet kapja, előfordulhat, hogy szintaktikai hiba a sablonban.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Ez a hiba akkor létrehozását, mert sablont kifejezések bonyolult lehet. Például a következő hozzárendelést egy tárfiók tartalmaz, zárójelek közé egy készletét, három funkció, három különböző kerek zárójeleket tartalmazhatnak, szimpla idézőjelben egy készletét, és egy tulajdonság:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Ha nem ad meg a megfelelő szintaxist, az a sablon egy érték, amely eltér attól a levelezéshez hoz létre.

Hiba az ilyen típusú megjelenésekor gondosan tekintse át a kifejezési szintaxist. Érdemes lehet például egy JSON-szerkesztővel [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) vagy [Visual Studio Code](resource-manager-vs-code.md), amely képes figyelmeztessen a szintaktikai hibákat.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>2 - helytelen szegmens hosszának megoldás

Egy másik érvénytelen a sablon-hiba akkor fordul elő, amikor az erőforrás neve nem megfelelő formátumú.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Egy legfelső szintű erőforrás egy kisebb szegmens kell rendelkeznie a neve, mint az erőforrástípus. Minden szegmensben perjellel különbözteti meg van. A következő példában a típus két szegmenst tartalmaz, és a név egy szegmens rendelkezik, ezért a rendelkezik egy **érvényes**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

A következő példában viszont **nem egy érvényes nevet** mert szegmensek azonos számú típusként.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Az alsóbb szintű erőforrásai típusa és neve szegmensek azonos számú rendelkezik. Ez a szám szegmensek szabálykészletében, mert a teljes neve és a gyermek típusú tartalmazza a szülő nevét és típusát. Ezért a teljes név még egy kisebb, mint a teljes típusú szegmens.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

A szegmensek megfelelő első megkapni erőforrás-kezelő típusú erőforrás-szolgáltató között alkalmazott. Például egy erőforrás zárolási alkalmazása a webhelyhez szükséges négy szegmensek típus. A név ezért szegmensek:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>3 - megoldás paraméter érvénytelen

Ha a paraméter értékét, amely nincs a megengedett értékeket ad meg, az alábbihoz hasonló üzenet jelenhet meg:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Kettős ellenőrzi az engedélyezett értékek a sablonban, és egy központi telepítése során. A paraméterértékek engedélyezett kapcsolatos további információkért lásd: [paraméterek szakaszban az Azure Resource Manager-sablonok](resource-manager-templates-parameters.md).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Túl sok cél-erőforráscsoport 4 - megoldás

Ha ötnél több cél-erőforráscsoport egy központi telepítésnél ad meg, ez a hibaüzenet jelenik meg. Vegye figyelembe, egyesítse a központi telepítésben lévő erőforrás-csoportok száma, vagy külön központi telepítéseket, sablonokat némelyike telepítése. További információkért lásd: [telepítése Azure erőforrások több mint egy előfizetéshez vagy erőforráscsoporthoz](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Megoldás 5 - körkörös függőséget észlelt

Hibaüzenet az erőforrások egymástól függenek, hogy megakadályozza, hogy a telepítés indítását. Egy egymástól függő szolgáltatásainak összevonás a két vagy több erőforrás, várjon, amíg más erőforrások, amelyek is várnak. Például resource1 resource3 függ, resource2 resource1 függ, és resource3 resource2 függ. Eltávolítja a szükségtelen függőségek általában megoldhatja a problémát.

Körkörös függőség megoldása:

1. A sablonban a körkörös függőség azonosított erőforrás található. 
2. Az adott erőforráshoz, vizsgálja meg a **dependsOn** tulajdonság és bármely használatát a **hivatkozás** működnek, attól függ, milyen erőforrásokat. 
3. Vizsgálja meg ezeket az erőforrásokat, hogy mely erőforrások függenek. Kövesse a függőségek meg, amikor egy erőforrást, amely az eredeti erőforrás függ.
5. Az erőforrások a körkörös függőség részt, gondosan vizsgálja meg az összes használatát a **dependsOn** tulajdonság esetén nem szükséges összes függőséget azonosításához. Távolítsa el ezeket a függőségeket. Ha biztos abban, hogy a függőség van szükség, távolítsa el azt. 
6. Telepítse újra a sablont.

Az értékek eltávolítása a **dependsOn** tulajdonság hibákat okozhat, ha a sablon telepítéséhez. Ha hibaüzenetet kap, adja hozzá a függőség újra üzembe a sablont. 

Ha ezt a megközelítést nem oldja meg a körkörös függőséget, fontolja meg a központi telepítés logikája a gyermekszintű erőforrása (például a bővítmények vagy konfigurációs beállítások). Gyermek erőforrások telepítése után a körkörös függőség erőforrás konfigurálása. Tegyük fel például, két olyan virtuális gépet telepít, de tulajdonságokat meg kell adni az egyes, amely a másikra hivatkozik. A következő sorrendben telepíthetők:

1. vm1
2. vm2
3. Vm1 kiterjesztés vm1 és vm2 virtuális gépnek függ. A bővítmény beállítása, amely azt lekérése vm2 virtuális gépnek vm1 értékeket.
4. Bővítmény vm2 virtuális gépnek a vm1 és vm2 virtuális gépnek függ. A bővítmény beállítása értékek, amelyek azt lekérése vm1 vm2 virtuális gépnek.

Ugyanezt a megközelítést működik az App Service-alkalmazásokhoz. Fontolja meg a konfigurációs értékek helyezi át az alkalmazás-erőforrást gyermek erőforrása. Két webes alkalmazásokat telepíthet a következő sorrendben:

1. webapp1
2. webapp2
3. config webapp1 webapp1 és webapp2 függ. Alkalmazásbeállítások webapp2 értékeivel tartalmaz.
4. config webapp2 webapp1 és webapp2 függ. Alkalmazásbeállítások webapp1 értékeivel tartalmaz.
