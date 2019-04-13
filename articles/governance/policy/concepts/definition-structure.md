---
title: A szabályzatdefiníciók struktúrája részletei
description: 'Ismerteti, hogy a szabályzatdefiníció erőforrás az Azure Policy által létesíteni az erőforrások konvenciók a szervezetben: Ha a házirend érvényesítve van, és milyen érvénybe léptetéséhez.'
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 7bb25aa1f77a49363fe2e08d1430282b9b33caae
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549354"
---
# <a name="azure-policy-definition-structure"></a>Azure szabályzatdefiníciók struktúrája

Erőforrás szabályzatdefiníciók használják az Azure Policy konvenciók az erőforrások létrehozásához. Minden egyes definíció erőforrás megfelelőségét, és milyen hatása, ha egy erőforráshoz nem megfelelő ismerteti.
Egyezmények definiálásával is csökkenthetők a költségek, és további könnyen kezelheti az erőforrásokat. Megadhatja például, hogy csak bizonyos típusú virtuális gépek használata engedélyezett. Másik lehetőségként megkövetelheti, hogy az összes erőforrásnak rendelkeznie kell egy adott címkét. Összes gyermekerőforrás örökölt házirendek. Ha egy szabályzatot alkalmazott egy erőforráscsoportba, alkalmazható az adott erőforráscsoportba tartozó összes erőforrást.

Az Azure Policy által használt séma itt található: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

JSON használatával létrehozhat egy szabályzatdefiníciót. A szabályzatdefiníció az elemeket tartalmazza:

- mód
- paraméterek
- Megjelenített név
- leírás
- felügyeletiházirend-szabálya
  - logikai kiértékelése
  - hatás

A következő JSON például olyan szabályzatot, amely korlátozza, hogy üzembe helyezett erőforrások látható:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": "westus2"
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Az összes Azure Policy minta vannak [házirend minták](../samples/index.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Mód

A **mód** meghatározza, hogy mely erőforrástípusokat szabályzat lesz kiértékelve. A támogatott módok a következők:

- `all`: erőforráscsoportok és az összes erőforrástípus kiértékelése
- `indexed`: erőforrástípusok, amelyek támogatják a címkék és a hely csak kiértékelése

Azt javasoljuk, hogy állítsa **mód** való `all` a legtöbb esetben. A portál használatával létrehozott összes szabályzat-definíciókat a `all` mód. Ha a PowerShell vagy az Azure CLI-vel, megadhatja a **mód** paraméter manuálisan. Ha a szabályzat-definíció nem tartalmaz egy **mód** érték, a rendszer alapértelmezés szerint `all` az Azure PowerShell, az `null` Azure CLI-ben. A `null` módja megegyezik `indexed` visszamenőleges kompatibilitás támogatására.

`indexed` lehet, amelyeket a címkék vagy a szabályzatok létrehozásakor használt. Bár nem kötelező, megakadályozza, hogy a címkék és a helyek való jelenik meg, nem kompatibilis a megfelelőségi eredmények nem támogató erőforrások. A kivétel **erőforráscsoportok**. Házirendeket kikényszerítő helyet vagy egy erőforráscsoportba tartozó címkéket kell beállítania **mód** való `all` és a kifejezetten a cél a `Microsoft.Resources/subscriptions/resourceGroups` típusa. Egy vonatkozó példáért lásd: [erőforráscímkék csoport kényszerítése](../samples/enforce-tag-rg.md). A címkék támogató erőforrások listáját lásd: [címkézése Azure-erőforrások támogatása](../../../azure-resource-manager/tag-support.md).

## <a name="parameters"></a>Paraméterek

Paraméterek segítségével leegyszerűsítik a szabályzatok kezelését szabályzatdefiníciók számának csökkentésével. Gondoljon a mezőket az űrlap – például a paraméterek `name`, `address`, `city`, `state`. Ezek a paraméterek változnak, azonban azok értékeit módosíthatja az űrlap egyes kitöltése alapján.
Paraméterek ugyanúgy működnek, szabályzatok készítése során. Paraméterekkel együtt egy szabályzat-definícióban, újból felhasználhatja az adott házirendnek a különböző helyzetekhez különböző értékek alapján.

> [!NOTE]
> Paraméterek egy meglévő és hozzárendelt-definíció adható hozzá. Az új paraméternek tartalmaznia kell a **defaultValue** tulajdonság. Ez megakadályozza, hogy meglévő hozzárendelését a szabályzatot vagy kezdeményezést közvetve érvénytelen kerül sor.

### <a name="parameter-properties"></a>Jelentésparaméter tulajdonságai

A paraméter a következő tulajdonságokat a szabályzat-definícióban használt rendelkezik:

- **Név**: A paraméter neve. Használja a `parameters` üzembe helyezési funkció a szabály belül. További információkért lásd: [paraméter értéke](#using-a-parameter-value).
- `type`: Meghatározza, hogy a paraméter egy **karakterlánc** vagy egy **tömb**.
- `metadata`: Elsősorban az Azure Portalon felhasználóbarát információk megjelenítéséhez altulajdonságok határozza meg:
  - `description`: A paraméter mire való a leírását. Példák az elfogadható értékek használható.
  - `displayName`: A rövid név jelenik meg a paraméter a portálon.
  - `strongType`: (Nem kötelező) Használja a portálon keresztül a szabályzatdefiníció hozzárendelésekor. Környezet figyelembe listáját tartalmazza. További információkért lásd: [strongType](#strongtype).
- `defaultValue`: (Nem kötelező) Beállítja a hozzárendelés a paraméter értékét, ha nincs érték megadva. Szükséges, amikor frissíti egy meglévő szabályzat-definíció, amely hozzá van rendelve.
- `allowedValues`: (Nem kötelező) Itt egy olyan értéktömböt, paraméterben hozzárendelés során.

Például meghatározhat szabályzatdefiníció korlátozni a helyeken, ahol erőforrásokat is üzembe helyezhetők. A szabályzat-definíció egy paramétere lehet **allowedLocations**. Ez a paraméter az elfogadott értékek korlátozásának mindegyik szabályzatdefiníció-hozzárendelés volna használható. Használatát **strongType** nyújt hatékonyabb, ha a portálon keresztül a hozzárendelés befejezése:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "westus2",
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>A paraméter értékének használata

A szabályzatbeli szabályban hivatkozik az alábbi paraméterek `parameters` telepítési érték függvény Szintaxis:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ez a minta hivatkozik a **allowedLocations** paraméter, amely a mutattuk [jelentésparaméter tulajdonságai](#parameter-properties).

### <a name="strongtype"></a>strongType

Belül a `metadata` tulajdonság, használhat **strongType** biztosít az Azure Portalon lehetőségek többszörös kijelöléses listája. Megengedett értékek a **strongType** jelenleg tartalmaz:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Definíció helye

Egy kezdeményezést vagy létrehozásakor meg kell határozni a definíció helye. A definíció helye egy felügyeleti csoportot vagy egy előfizetésben kell lennie. Ezen a helyen, amelyhez a kezdeményezést vagy rendelhetők a hatókör határozza meg. Erőforrások közvetlen tagjai vagy a gyermekek a hierarchiában a definíció helye, hogy a szabályzat-hozzárendelés kell lennie.

Ha a definíció helye v:

- **Előfizetés** – csak az adott előfizetésen belüli erőforrások rendelhetők a szabályzat.
- **A felügyeleti csoport** – csak az alárendelt felügyeleti csoportok és a gyermek előfizetések belüli erőforrások rendelhetők a szabályzatot. Ha azt tervezi, a szabályzat-definíció alkalmazni a különböző előfizetések, a hely ezen előfizetések tartalmazó felügyeleti csoportot kell lennie.

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

Használhat **displayName** és **leírás** azonosíthatja a szabályzat-definíció, és kontextusának esetén. **displayName** maximális hossza _128_ karakterek és **leírás** legfeljebb _512_ karakter.

## <a name="policy-rule"></a>Szabályzatbeli szabály

A szabály áll **Ha** és **majd** blokkokat. Az a **Ha** letiltása, megadhat egy vagy több feltételt, adja meg, ha a házirend érvényesítve van-e. Ezek a feltételek, pontosan meghatározni a forgatókönyv egy házirend logikai operátorokat alkalmazhat.

Az a **majd** letiltása, definiálhat a hatás, amely akkor fordul elő, amikor a **Ha** feltételek teljesülnek.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Logikai operátorok

Támogatott logikai operátorok a következők:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

A **nem** szintaxis invertálja a feltétel eredménye. A **allOf** szintaxis (a logikai hasonló **és** művelet) szükséges összes feltétel igaz. A **anyOf** szintaxis (a logikai hasonló **vagy** művelet) van szükség, hogy igaz legyen egy vagy több feltételt.

Logikai operátorok ágyazhatja be. A következő példa bemutatja egy **nem** művelet, amely van beágyazva egy **allOf** műveletet.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Feltételek

A feltétel-e egy **mező** vagy a **érték** hozzáférő megfelel bizonyos feltételeknek. A támogatott feltételek a következők:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

Használatakor a **például** és **notLike** , feltételek meg helyettesítő karakter `*` értéke.
Az érték nem tartalmazhat egynél több helyettesítő `*`.

Használatakor a **megfelelő** és **notMatch** adja meg a feltételeket, `#` számjegy, megfelelő `?` betűvel, a `.` megfelelően az összes karaktert, és bármely más karakter megfelelően a tényleges karakter.
**megfelelő** és **notMatch** kis-és nagybetűket. Kis-és alternatív megoldások érhetők el a **matchInsensitively** és **notMatchInsensitively**. Példák: [lehetővé teszik több név minták](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Mezők

Feltételek alkotta mezőkkel. Egy mező megegyezik a tulajdonság az erőforrás-kérések forgalma, és az erőforrás állapotának leírása.

A következő mezők támogatottak:

- `name`
- `fullName`
  - Az erőforrás teljes nevét adja vissza. Erőforrás teljes név bármelyik szülő erőforrás nevét (például "myServer/myDatabase") által kiegészített az erőforrás nevét.
- `kind`
- `type`
- `location`
  - Használat **globális** az erőforrásra, amely helyet független. Egy vonatkozó példáért lásd: [-minták – engedélyezett helyek](../samples/allowed-locations.md).
- `identity.type`
  - Típusát adja vissza [identitás](../../../active-directory/managed-identities-azure-resources/overview.md) engedélyezve van az erőforráson.
- `tags`
- `tags['<tagName>']`
  - A szögletes zárójelet szintaxist tartalmaznak a címkenevek írásjelek, például egy kötőjel, ponttal vagy szóközzel rendelkező támogatja.
  - Ahol **\<tagName\>** feltételét ellenőrzése a címke neve.
  - Példák: `tags['Acct.CostCenter']` ahol **Acct.CostCenter** a címke neve.
- `tags['''<tagName>''']`
  - A szögletes zárójelet szintaxist tartalmaznak a címkenevek által a dupla aposztrófot escape-karaktersorozat rendelkező aposztrófot támogatja.
  - Ahol **"\<tagName\>"** feltételét ellenőrzése a címke neve.
  - Példa: `tags['''My.Apostrophe.Tag''']` ahol **"\<tagName\>"** a címke neve.
- vlastnost aliasok -, lásd: [aliasok](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`, és `tags[tag.with.dots]` egy címkemező deklaráló továbbra is elfogadható módja.
> Az előnyben részesített kifejezések azonban fent felsoroltak is.

#### <a name="use-tags-with-parameters"></a>Címkék használata paraméterekkel

A paraméter értékét a tag mező adható át. Paraméter átadása egy tag mező növeli a szabályzatdefiníció szabályzat-hozzárendelés során rugalmasságát.

A következő példában `concat` a címkék a mező keresési értékét nevű címke létrehozására szolgáló a **tagName** paraméter. Ha a címke nem létezik, a **hozzáfűzése** érvénybe szolgál a rendszervizsgálati módban levő erőforrások szülő erőforráscsoporthoz használatával állítsa be a azonos nevű címke értékének használatával címke hozzáadása az `resourcegroup()` függvényhez.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Érték

Feltételek is alakítható használatával **érték**. **érték** feltételek alapján ellenőrzi [paraméterek](#parameters), [sablonfüggvények támogatott](#policy-functions), vagy literálok lehetnek.
**érték** van párosítva bármely támogatott [feltétel](#conditions).

> [!WARNING]
> Ha eredménye egy _sablonfüggvény_ hiba történt a házirend kiértékelése sikertelen. Sikertelen kiértékelés egy implicit **megtagadása**. További információkért lásd: [sablon hibák elkerülése](#avoiding-template-failures).

#### <a name="value-examples"></a>Érték példák

Ez a házirend a szabály példa **érték** összehasonlítására eredményét a `resourceGroup()` függvény és a visszaadott **neve** tulajdonságát egy **például** feltétele`*netrg`. A szabály letiltja a nem az összes erőforrást a `Microsoft.Network/*` **típus** bármely kifejezésre végződő nevű erőforráscsoport `*netrg`.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Ez a házirend a szabály példa **érték** ellenőrizheti, ha az eredmény több beágyazott függvények **egyenlő** `true`. A szabály letiltja a bármely erőforrás, amely nem rendelkezik legalább három címkék.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Sablon hibák elkerülése

Használatát _sablonfüggvények_ a **érték** lehetővé teszi, hogy számos összetett beágyazott függvényt. Ha eredménye egy _sablonfüggvény_ hiba történt a házirend kiértékelése sikertelen. Sikertelen kiértékelés egy implicit **megtagadása**. Például egy **érték** , amely bizonyos esetekben meghiúsul:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

A fent használt példa szabály [substring()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) összehasonlítani az első három karakterét **neve** való **abc**. Ha **neve** három karakternél rövidebb a `substring()` függvény hibát eredményez. Ezt a hibát okoz a szabályzat lesz egy **megtagadása** érvénybe.

Ehelyett használja a [if()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) függvény annak ellenőrzéséhez, hogy az első három karakterét **neve** egyenlő **abc** nem engedélyezi egy **neve** rövidebb, mint három karaktert a hiba miatt:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

A módosított szabályzatot szabállyal `if()` hosszát ellenőrzi **neve** beolvasásának megkísérlése előtt egy `substring()` a 3-nál kevesebb karaktert tartalmazó értéket. Ha **neve** túl rövid, a "nem kezdve abc" érték helyett visszaadott és képest **abc**. Egy erőforrás, amelynek rövid neve nem kezdődhet **abc** a szabály továbbra is sikertelen, de már nem hibát okoz a kiértékelés során.

### <a name="effect"></a>Következmény

A házirend hatása a következő típusú támogatja:

- **Megtagadási**: a tevékenységnapló eseményt hoz létre, és a kérelem meghiúsul
- **Naplózási**: állít elő, egy figyelmeztető esemény tevékenységnaplóban, de nem sikertelen a kérelem
- **Hozzáfűzés**: a meghatározott készletével mezőket ad hozzá a kérelem
- **AuditIfNotExists**: lehetővé teszi a naplózást, ha egy erőforrás nem létezik.
- **DeployIfNotExists**: üzembe helyezi egy erőforrást, ha még nem létezik
- **Letiltott**: nem erőforrások megfelelőségét, a szabály kiértékelése

A **hozzáfűzése**, meg kell adnia a következő adatokat:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Az érték lehet egy karakterlánc- vagy JSON-formátumú objektum.

**AuditIfNotExists** és **DeployIfNotExists** létezik-e a kapcsolódó erőforráshoz értékeli, és olyan szabályt alkalmaz. Ha az erőforrás nem felel meg a szabály, milyen hatása van megvalósítva. Például megkövetelheti, hogy a network watchert az összes virtuális hálózat üzemel. További információkért lásd: a [naplózása nem található a bővítmény](../samples/audit-ext-not-exist.md) példa.

A **DeployIfNotExists** hatása van szükség a **roleDefinitionId** tulajdonságot a **részletek** a szabály részét. További információkért lásd: [szervizelési – konfigurálja a szabályzat-definíció](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Minden egyes hatás, értékelési, tulajdonságokat és példákat sorrendje a részleteket lásd: [ismertetése házirend hatások](effects.md).

### <a name="policy-functions"></a>A házirend-funkciók

Az összes [Resource Manager-sablonfüggvények](../../../azure-resource-manager/resource-group-template-functions.md) belül házirendszabály, kivéve a következő függvények használhatók:

- copyIndex()
- Deployment()
- list*
- Providers()
- Reference()
- resourceId()
- variables()

Ezenkívül a `field` funkció érhető el a szabályzat előírásainak. `field` elsősorban az **AuditIfNotExists** és **DeployIfNotExists** referencia mezők, a rendszer kiértékelt erőforrás. Ezt használhatja például látható a [DeployIfNotExists példa](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Függvény szabályzat – példa

Ez a házirend a szabály a példa a `resourceGroup` erőforrás függvény a **neve** tulajdonság, kombinálva a `concat` tömb- és függvény hozhat létre egy `like` feltételt, amely érvényesíti az erőforrás neve indítása az az erőforráscsoport nevét.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Aliasok

Aliasok tulajdonság használatával az erőforrástípushoz konkrét tulajdonságok eléréséhez. Az aliasok lehetővé teszi korlátozása, milyen értékeket, vagy a feltételek az erőforrás-tulajdonságok megengedettek. Minden egyes alias képez le egy adott erőforrás típusát különböző API-verzióit szereplő elérési utakat. Szabályzat-kiértékelés során a házirendmotor lekérdezi, hogy API-verzió tulajdonság elérési útját.

Az aliasok listája folyamatosan bővül. Milyen aliasok az Azure Policy által jelenleg támogatott megkereséséhez használja a következő módszerek egyikét:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzPolicyAlias -NamespaceMatch 'automation'
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API-t / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>A [*] alias ismertetése

Az aliasok rendelkezésre álló számos rendelkezik egy olyanra, amely egy "normál" nevet, és a egy másik néven jelenik meg, amely rendelkezik **[\*]** csatlakozik. Példa:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

A "normál" alias egyetlen értékként a mezőt jelöl. Ez a mező esetén pontosan egyezik összehasonlítást forgatókönyveket értékek teljes készletét kell lenniük pontosan a meghatározott, nincs több nem.

A **[\*]** alias-szal a tömb egyes elemei értékét, és minden eleme megadott tulajdonságainak képest. Ez a megközelítés lehetővé teszi a hasonlítsa össze az elem tulajdonságainak "Ha egyik sem", "Ha", vagy a "Ha az összes," forgatókönyveket. Használatával **ipRules [\*]**, például szeretné érvényesítése, amely minden _művelet_ van _Megtagadás_, azonban nem aggódniuk hány szabályok vagy milyen IP-_érték_ van. A minta a szabály ellenőrzi, minden egyezések **ipRules [\*] .value** való **10.0.4.1** , és alkalmazza a **effectType** csak akkor, ha nem, legalább egy egyezést talál:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

További információkért lásd: [kiértékelése a [\*] alias](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Kezdeményezések

Kezdeményezések lehetővé teszi több kapcsolódó szabályzatdefiníciót, mert dolgozik egy csoport egyetlen elemként hozzárendelések és a felügyeleti egyszerűsítése érdekében. Ha például be egy egyetlen-kezdeményezéshez kapcsolódó címkézési szabályzatdefiníciók csoportosíthatók. Ahelyett, hogy az egyes házirendek külön-külön rendeli, a kezdeményezés vonatkoznak.

A következő példa bemutatja, hogyan hozhat létre a kezdeményezés két címkét kezelése: `costCenter` és `productName`. Két beépített szabályzatokat használ a alkalmazni az alapértelmezett címke értéke.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy-minták](../samples/index.md)
- Felülvizsgálat [házirend hatások ismertetése](effects.md)
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](../how-to/programmatically-create.md)
- Ismerje meg, hogyan [megfelelőségi adatok lekérése](../how-to/getting-compliance-data.md)
- Ismerje meg, hogyan [javítani a nem megfelelő erőforrások](../how-to/remediate-resources.md)
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../../management-groups/overview.md) ismertető részt.
