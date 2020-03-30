---
title: Érvénytelen sablonhibák
description: Ez a témakör azt ismerteti, hogy az Azure Resource Manager-sablonok telepítésekor hogyan lehet feloldani az érvénytelen sablonhibákat.
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.openlocfilehash: 65cd69d67933d117b51f37b587b276aec2bd635a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154057"
---
# <a name="resolve-errors-for-invalid-template"></a>Érvénytelen sablonnal kapcsolatos hibák elhárítása

Ez a cikk az érvénytelen sablonhibák elhárítását ismerteti.

## <a name="symptom"></a>Hibajelenség

Sablon telepítésekor hibaüzenet jelenik meg, amely a következőket jelzi:

```
Code=InvalidTemplate
Message=<varies>
```

A hibaüzenet a hiba típusától függ.

## <a name="cause"></a>Ok

Ez a hiba többféle típusú hibából eredhet. Általában szintaktikai vagy szerkezeti hibát tartalmaznak a sablonban.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>1. megoldás – szintaktikai hiba

Ha olyan hibaüzenet jelenik meg, amely azt jelzi, hogy a sablon érvényesítése sikertelen volt, akkor lehet, hogy szintaktikai probléma lép fel a sablonban.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Ez a hiba könnyen eladható, mert a sablonkifejezések bonyolultak lehetnek. Például egy tárfiók következő névhozzárendelése egy zárójelből, három függvényből, három zárójelből, egy egyidézőjelű ből és egy tulajdonságból áll:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Ha nem adja meg a megfelelő szintaxist, a sablon olyan értéket hoz létre, amely eltér a szándékától.

Ilyen típusú hiba esetén alaposan tekintse át a kifejezés szintaxisát. Fontolja meg egy JSON-szerkesztő, például [a Visual Studio](create-visual-studio-deployment-project.md) vagy a Visual Studio Code [használatát,](use-vs-code-to-create-template.md)amely figyelmezteti a szintaktikai hibákra.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>2. megoldás - helytelen szegmenshosszok

Egy másik érvénytelen sablonhiba akkor fordul elő, ha az erőforrás neve nem a megfelelő formátumban van.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

A gyökérszintű erőforrás nak eggyel kevesebb szegmenssel kell rendelkeznie a névben, mint az erőforrástípusban. Minden szegmenst perjel különböztet meg. A következő példában a típus nak két szegmense van, a neve pedig egy szegmens, így ez egy **érvényes név.**

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

De a következő példa **nem érvényes név,** mert ugyanannyi szegmenssel rendelkezik, mint a típus.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

A gyermekerőforrások esetében a típus és a név azonos számú szegmenssel rendelkezik. Ennek a szegmensszámnak van értelme, mert a gyermek teljes neve és típusa tartalmazza a szülő nevét és típusát. Ezért a teljes név még mindig eggyel kevesebb szegmenssel rendelkezik, mint a teljes típus.

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

A szegmensek helyes beszerzése bonyolult lehet az erőforrás-kezelői erőforrások-szolgáltatók között alkalmazott típusokkal. Például egy erőforrászárolás alkalmazása egy webhelyre négy szegmensből áll. Ezért a név három szegmensből áll:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>3. megoldás - a paraméter érvénytelen

Ha olyan paraméterértéket ad meg, amely nem az engedélyezett értékek egyike, a következőhöz hasonló hibaüzenet jelenik meg:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Ellenőrizze a sablonban engedélyezett értékeket, és adjon meg egyet az üzembe helyezés során. Az engedélyezett paraméterértékekről az [Azure Resource Manager-sablonok Paraméterek szakaszában olvashat bővebben.](template-syntax.md#parameters)

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>4. megoldás – Túl sok célerőforrás-csoport

Ha egy központi telepítésben ötnél több célerőforrás-csoportot ad meg, ez a hibaüzenet jelenik meg. Fontolja meg az erőforráscsoportok számának konszolidálását a központi telepítésben, vagy telepítse a sablonok egy részét külön központi telepítésként. További információ: [Azure-erőforrások telepítése több előfizetésre vagy erőforráscsoportra.](cross-resource-group-deployment.md)

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>5. megoldás – körkörös függőség észlelve

Ez a hibaüzenet akkor jelenik meg, ha az erőforrások egymástól függenek oly módon, hogy megakadályozzák a központi telepítés elindítását. A kölcsönös függőségek kombinációja miatt két vagy több erőforrás várakozik más erőforrásokra, amelyek szintén várakoznak. Például az erőforrás1 az erőforrástól3, az erőforrás2 az erőforrástól1, az erőforrás3 pedig az erőforrástólfügg2. Ezt a problémát általában a szükségtelen függőségek eltávolításával oldhatja meg.

Körkörös függőség megoldása:

1. A sablonban keresse meg a körkörös függőségben azonosított erőforrást.
2. Az adott erőforrás esetében vizsgálja meg a **dependsOn** tulajdonságot és a **referenciafüggvény** bármely felhasználását, hogy lássa, mely erőforrásoktól függ.
3. Vizsgálja meg ezeket az erőforrásokat, hogy mely erőforrásoktól függenek. Kövesse a függőségeket, amíg észre nem veszi az eredeti erőforrástól függő erőforrást.
5. A körkörös függőségben részt vevő erőforrások esetében alaposan vizsgálja meg a **dependsOn** tulajdonság összes felhasználását a szükségtelen függőségek azonosításához. Távolítsa el ezeket a függőségeket. Ha nem biztos abban, hogy függőségre van szükség, próbálja meg eltávolítani.
6. Telepítse újra a sablont.

Értékek eltávolítása a **dependsOn** tulajdonságból hibákat okozhat a sablon telepítésekor. Ha hibaüzenetet kap, adja hozzá a függőséget a sablonhoz.

Ha ez a megközelítés nem oldja meg a körkörös függőséget, fontolja meg a központi telepítési logika egy részének gyermekerőforrásokba (például bővítmények vagy konfigurációs beállítások) történő áthelyezését. Konfigurálja azokat a gyermekerőforrásokat a körkörös függőségben részt vevő erőforrások üzembe helyezéséhez. Tegyük fel például, hogy két virtuális gépet telepít, de mindegyiken be kell állítania a tulajdonságokat, amelyek a másikra hivatkoznak. Ezeket a következő sorrendben telepítheti:

1. vm1
2. vm2
3. A vm1-es bővítmény a vm1 és a vm2-től függ. A bővítmény a vm1-es vm1-es értékeit állítja be, amelyeket a vm2-ből kap.
4. A vm2-es bővítmény a vm1 és a vm2-től függ. A bővítmény a vm1-ből leadott vm2-értékeket állít be.

Ugyanez a megközelítés működik az App Service-alkalmazások. Fontolja meg a konfigurációs értékek áthelyezése az alkalmazás erőforrás gyermekerőforrás. Két webalkalmazást a következő sorrendben telepíthet:

1. webapp1
2. webapp2
3. a webapp1 konfigurációja a webapp1-től és a webapp2-től függ. Ez tartalmazza az alkalmazás beállításait értékek webapp2.
4. a webapp2 config a webapp1-től és a webapp2-től függ. Ez tartalmazza az alkalmazás beállításait értékek webapp1.
