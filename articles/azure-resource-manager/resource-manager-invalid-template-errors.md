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
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 87bc6e4def624785c5052a9a25f579b022c940ec
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
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

## <a name="solution"></a>Megoldás

### <a name="solution-1---syntax-error"></a>1 - szintaktikai hiba megoldás

Ha a sablont nem sikerült érvényesítési jelző hibaüzenetet kapja, előfordulhat, hogy szintaktikai hiba a sablonban.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Ez a hiba akkor létrehozását, mert sablont kifejezések bonyolult lehet. Például a következő hozzárendelést egy tárfiók zárójelek közé egy készletét, három funkció, három különböző kerek zárójeleket tartalmazhatnak, szimpla idézőjelben egy készletét, és tartalmaz egy tulajdonság:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Ha nem adja meg a megfelelő szintaxist, az a sablon egy érték, amely eltér attól a levelezéshez hoz létre.

Hiba az ilyen típusú megjelenésekor gondosan tekintse át a kifejezési szintaxist. Érdemes lehet például egy JSON-szerkesztővel [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) vagy [Visual Studio Code](resource-manager-vs-code.md), amely képes figyelmeztessen a szintaktikai hibákat.

### <a name="solution-2---incorrect-segment-lengths"></a>2 - helytelen szegmens hosszának megoldás

Egy másik érvénytelen a sablon hiba akkor fordul elő, ha az erőforrás neve nem megfelelő formátumú.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Egy legfelső szintű erőforrás egy kisebb szegmens kell rendelkeznie a neve, mint az erőforrástípus. Minden szegmensben perjellel különbözteti meg van. A következő példában a típus két szegmenst tartalmaz, és a neve egy szegmens rendelkezik, akkor egy **érvényes**.

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

### <a name="solution-3---parameter-is-not-valid"></a>3 - megoldás paraméter érvénytelen

Ha a sablon meghatározza a paraméter megengedett értékei, és megad egy értéket, amely nem egy ezeket az értékeket, az alábbihoz hasonló üzenet jelenhet meg:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Kettős ellenőrzi az engedélyezett értékek a sablonban, és egy központi telepítése során.

### <a name="solution-4---too-many-target-resource-groups"></a>Túl sok cél-erőforráscsoport 4 - megoldás

Ha ötnél több cél-erőforráscsoport egy központi telepítésnél ad meg, ez a hibaüzenet jelenik meg. Vegye figyelembe, egyesítse a központi telepítésben lévő erőforrás-csoportok száma, vagy külön központi telepítéseket, sablonokat némelyike telepítése. További információkért lásd: [telepítése Azure erőforrások több mint egy előfizetéshez vagy erőforráscsoporthoz](resource-manager-cross-resource-group-deployment.md).

### <a name="solution-5---circular-dependency-detected"></a>Megoldás 5 - körkörös függőséget észlelt

Hibaüzenet az erőforrások egymástól függenek, hogy megakadályozza, hogy a telepítés indítását. Egy egymástól függő szolgáltatásainak összevonás a két vagy több erőforrás, várjon, amíg más erőforrások, amelyek is várnak. Például resource1 resource3 függ, resource2 resource1 függ, és resource3 resource2 függ. Eltávolítja a szükségtelen függőségek általában megoldhatja a problémát.
