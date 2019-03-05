---
title: Az Azure érvénytelen sablon típusú hiba |} A Microsoft Docs
description: Érvénytelen sablon típusú hiba megoldását ismerteti.
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
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: ee4bce38e0fcde93ba0417617ae90dab2eefda67
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316200"
---
# <a name="resolve-errors-for-invalid-template"></a>Érvénytelen a sablonhoz ki a hibákat

Ez a cikk bemutatja, hogyan érvénytelen sablon típusú hiba megoldásához.

## <a name="symptom"></a>Jelenség

Sablon üzembe helyezésekor, hibaüzenetet kapja:

```
Code=InvalidTemplate
Message=<varies>
```

A hibaüzenet hiba típusától függ.

## <a name="cause"></a>Ok

Ez a hiba a különböző típusú hibákat okozhat. Szintaxis- vagy strukturális hiba történt a sablon általában tartalmaznak.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>1 – szintaktikai hiba megoldás

Ha egy hibaüzenet, amely azt jelzi, hogy a sablon nem sikerült érvényesítése kap, előfordulhat, szintaktikai hiba a sablonban.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Ez a hiba akkor létrehozását, mert lehet, hogy a sablon kifejezések bonyolult. Például a következő hozzárendelést egy storage-fiók rendelkezik zárójelek egy készletét, három funkciót, három különböző zárójelben, szimpla idézőjelek között egy készletét és egy tulajdonságot:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Ha nem ad meg a megfelelő szintaxist, az a sablon licenccsomagot eltérő értéket állít elő.

Az ilyen típusú hiba jelenik meg, gondosan tekintse át a kifejezés szintaxisa. Fontolja meg egy JSON-szerkesztő, például [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) vagy [Visual Studio Code](resource-manager-vs-code.md), amely képes figyelmeztessen a szintaktikai hibákat.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>2 – hibás szegmens hosszúságok megoldás

Egy másik érvénytelen sablon-hiba akkor fordul elő, amikor az erőforrás neve nem megfelelő formátumú.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

A legfelső szintű erőforrás az erőforrástípus neve eltér egy kisebb szegmenst kell rendelkeznie. Van olyan törtvonalat különbözteti meg minden szegmensben. A következő példában a típus 2 szegmensből pedig a nevét egy szegmens, így egy **érvényes nevet**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

A következő példában viszont **nem egy érvényes nevet** szegmensek azonos számú típusként, mert.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Gyermek-erőforrás típusa és neve szegmensek azonos számú rendelkezik. A szegmensek száma teljes neve és típusa, a gyermek tartalmazza a szülő neve és típusa van értelme. Ezért a teljes fájlvisszaállítási név még kisebb, mint a teljes típus egy szegmens.

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

A szegmensek megfelelő első nehéz megkülönböztetni a különböző erőforrás-szolgáltatók alkalmazott Resource Manager-típusokkal. Például egy erőforrás-zárolás alkalmazása a webhelyhez szükséges négy szegmensek típus. A név ezért szegmensek:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>3 – megoldás paraméter nem érvényes

Ha egy paraméter értéke, amely nem szerepel az engedélyezett értékeket ad meg, a következő hibához hasonló üzenetet kapja:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Dupla ellenőrzi az engedélyezett értékek a sablonban, és egy üzembe helyezés során. Engedélyezett értékei kapcsolatos további információkért lásd: [paraméterek szakaszban az Azure Resource Manager-sablonok](resource-group-authoring-templates.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Túl sok erőforrás célcsoportok 4 - megoldás

Ha ötnél több cél erőforráscsoportok egyetlen telepítést ad meg, ezt a hibaüzenetet kapja. Fontolja meg a központi telepítésben lévő erőforrás-csoportok száma konszolidálása vagy központi telepítése különálló üzembe helyezéseket, a sablonokat. További információkért lásd: [üzembe helyezése Azure-erőforrásokat az egynél több előfizetésen vagy erőforráscsoporton](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Megoldás 5 – a rendszer körkörös függőséget észlelt

Ezt a hibaüzenetet kapja, amikor az erőforrások egymástól függenek úgy, hogy megakadályozza, hogy a telepítés indítását. Köztük fennálló függőségek kombinációja lehetővé teszi, hogy két vagy több erőforrás is váró más erőforrásokra várnia. Például resource1 resource3 függ, resource2 resource1 függ, és resource3 resource2 függ. Eltávolítja a felesleges függőségek általában is megoldhatja a problémát.

Körkörös függőséget megoldása:

1. A sablonban található az erőforrás, a rendszer körkörös függőséget azonosítja. 
2. Az adott erőforráshoz, vizsgálja meg a **dependsOn** tulajdonság, és bármely használják a **referencia** függvényt, hogy mely erőforrások attól függ. 
3. Tekintse meg ezeket az erőforrásokat, hogy mely erőforrások függenek. Kövesse a függőségeket, amíg a láthatja, hogy egy erőforrás, amely az eredeti erőforrás függ.
5. Részt vesz a rendszer körkörös függőséget az erőforrások, gondosan vizsgálja az összes felhasználását a **dependsOn** tulajdonság nem szükséges függőségek azonosításához. Távolítsa el ezeket a függőségeket. Ha biztos benne, hogy egy függőség van szüksége, próbálja meg eltávolítani azt. 
6. A sablon újbóli telepítése.

Az értékek eltávolítása a **dependsOn** tulajdonság hibákat eredményezhet, amikor telepíti a sablont. Ha hibaüzenetet kap, adja hozzá a függőség be újra a sablont. 

Ha ezt a megközelítést nem oldja meg a rendszer körkörös függőséget, fontolja meg az üzembe helyezés logikája megéri gyermekszintű erőforrása (például a konfigurációs beállításokat és a teljes). Ezek után a rendszer körkörös függőséget erőforrás gyermekerőforrásait konfigurálása. Tegyük fel például, hogy két virtuális gépet telepít, de a tulajdonságai az egyes hivatkozni, a másik be kell. A következő sorrendben telepíthetők:

1. vm1
2. vm2
3. A vm1 futtatására szolgáló bővítmény a vm1, vm2 és függ. A bővítmény a vm1, vm2 olvas, adja meg az értékeket.
4. A vm2 bővítmény a vm1, vm2 és függ. A bővítmény lekéri a vm1, vm2 értékek beállítása.

Ugyanezzel a megközelítéssel az App Service-alkalmazások esetében működik. Fontolja meg a konfigurációs értékeket helyezi át az alkalmazás-erőforrást gyermek erőforrása. Két webes alkalmazásokat telepíthet a következő sorrendben:

1. webapp1
2. webapp2
3. config webapp1 webapp1 és webapp2 függ. Alkalmazásbeállítások webapp2 értékeit tartalmazza.
4. config webapp2 webapp1 és webapp2 függ. Alkalmazásbeállítások webapp1 értékeit tartalmazza.
