---
title: A házirend-definíciós struktúra részletei
description: 'Ismerteti, hogy a szabályzatdefiníció erőforrás az Azure Policy által létesíteni az erőforrások konvenciók a szervezetben: Ha a házirend érvényesítve van, és milyen érvénybe léptetéséhez.'
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 77bf284734428e9257b46d85296796e4051ace26
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494826"
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
                "defaultValue": [ "westus2" ]
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

Az összes Azure Policy minta [Azure Policy minta](../samples/index.md).

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Mód

A **mód** beállítása attól függően történik, hogy a házirend Azure Resource Manager tulajdonságot vagy erőforrás-szolgáltatói tulajdonságot céloz meg.

### <a name="resource-manager-modes"></a>Resource Manager-módok

A **mód** meghatározza, hogy mely erőforrástípusokat szabályzat lesz kiértékelve. A támogatott módok a következők:

- `all`: erőforráscsoportok és az összes erőforrástípus kiértékelése
- `indexed`: erőforrástípusok, amelyek támogatják a címkék és a hely csak kiértékelése

Azt javasoljuk, hogy állítsa **mód** való `all` a legtöbb esetben. A portál használatával létrehozott összes szabályzat-definíciókat a `all` mód. Ha a PowerShell vagy az Azure CLI-vel, megadhatja a **mód** paraméter manuálisan. Ha a szabályzat-definíció nem tartalmaz egy **mód** érték, a rendszer alapértelmezés szerint `all` az Azure PowerShell, az `null` Azure CLI-ben. A `null` módja megegyezik `indexed` visszamenőleges kompatibilitás támogatására.

`indexed` lehet, amelyeket a címkék vagy a szabályzatok létrehozásakor használt. Habár nem kötelező, megakadályozza, hogy a címkék és a hely nem támogatja az olyan erőforrásokat, amelyek nem felelnek meg a megfelelőségi eredményeknek. A kivétel **erőforráscsoportok**. Házirendeket kikényszerítő helyet vagy egy erőforráscsoportba tartozó címkéket kell beállítania **mód** való `all` és a kifejezetten a cél a `Microsoft.Resources/subscriptions/resourceGroups` típusa. Egy vonatkozó példáért lásd: [erőforráscímkék csoport kényszerítése](../samples/enforce-tag-rg.md). A címkéket támogató erőforrások listáját lásd: az Azure- [erőforrások támogatásának címkézése](../../../azure-resource-manager/tag-support.md).

### <a name="resource-provider-modes"></a>Erőforrás-szolgáltatói módok

Jelenleg `Microsoft.ContainerService.Data` csak az erőforrás-szolgáltatói mód támogatott az [Azure Kubernetes szolgáltatásban](../../../aks/intro-kubernetes.md)a belépésvezérlés szabályainak kezeléséhez.

> [!NOTE]
> A [Kubernetes Azure Policy](rego-for-aks.md) nyilvános előzetes verzióban érhető el, és csak a beépített szabályzat-definíciókat támogatja.

## <a name="parameters"></a>Paraméterek

Paraméterek segítségével leegyszerűsítik a szabályzatok kezelését szabályzatdefiníciók számának csökkentésével. Gondoljon a mezőket az űrlap – például a paraméterek `name`, `address`, `city`, `state`. Ezek a paraméterek változnak, azonban azok értékeit módosíthatja az űrlap egyes kitöltése alapján.
Paraméterek ugyanúgy működnek, szabályzatok készítése során. Paraméterekkel együtt egy szabályzat-definícióban, újból felhasználhatja az adott házirendnek a különböző helyzetekhez különböző értékek alapján.

> [!NOTE]
> A paraméterek hozzáadhatók meglévő és hozzárendelt definícióhoz is. Az új paraméternek tartalmaznia kell a **defaultValue** tulajdonságot. Ez megakadályozza, hogy meglévő hozzárendelését a szabályzatot vagy kezdeményezést közvetve érvénytelen kerül sor.

### <a name="parameter-properties"></a>Paraméter tulajdonságai

A paraméter a következő tulajdonságokkal rendelkezik, amelyek a szabályzat-definícióban használatosak:

- **név**: A paraméter neve. A házirend-szabályon belüli központitelepítésifüggvényhasználja.`parameters` További információ: [paraméter értékének használata](#using-a-parameter-value).
- `type`: Meghatározza, hogy a paraméter **karakterlánc**, **tömb**, **objektum**, **logikai**, **egész**, **lebegőpontos**vagy **datetime**.
- `metadata`: Meghatározza, hogy a Azure Portal milyen altulajdonságokat használ a felhasználóbarát információk megjelenítéséhez:
  - `description`: Annak magyarázata, hogy mit használ a paraméter. A használható az elfogadható értékek példáinak megadására.
  - `displayName`: A paraméterhez tartozó portálon megjelenő rövid név.
  - `strongType`: Választható A szabályzat definíciójának a portálon való hozzárendeléséhez használatos. Környezetfüggő listát biztosít. További információ: [strongType](#strongtype).
  - `assignPermissions`: Választható Állítsa _igaz_ értékre, hogy Azure Portal hozzon létre szerepkör-hozzárendeléseket a házirend-hozzárendelés során. Ez a tulajdonság akkor hasznos, ha az engedélyeket a hozzárendelési hatókörön kívül szeretné hozzárendelni. Szerepkör-definícióban egy szerepkör-hozzárendelés van a házirendben (vagy a szerepkör-definícióban a kezdeményezés összes házirendje esetében). A paraméter értékének érvényes erőforrásnak vagy hatókörnek kell lennie.
- `defaultValue`: Választható Megadja a paraméter értékét egy hozzárendelésben, ha nincs megadva érték.
  Egy meglévő, hozzárendelt szabályzat-definíció frissítésekor szükséges.
- `allowedValues`: Választható Az értékek tömbjét adja meg, amelyet a paraméter elfogad a hozzárendelés során.

Például meghatározhat egy házirend-definíciót, amely korlátozza az erőforrások üzembe helyezésének helyét. A házirend-definíció paraméterét **allowedLocations**lehet. Ezt a paramétert a házirend-definíció egyes hozzárendelései használják az elfogadott értékek korlátozására. A **strongType** használata fokozott élményt nyújt a hozzárendelésnek a portálon keresztül történő elvégzése során:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Paraméter értékének használata

A szabályzatbeli szabályban hivatkozik az alábbi paraméterek `parameters` telepítési érték függvény Szintaxis:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ez a példa a **allowedLocations** paraméterre hivatkozik, amely a [paraméter tulajdonságainál](#parameter-properties)látható.

### <a name="strongtype"></a>strongType

A `metadata` tulajdonságon belül a **strongType** használatával több választási lehetőséget is megadhat a Azure Portalon belül. Megengedett értékek a **strongType** jelenleg tartalmaz:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Definíció helye

Egy kezdeményezést vagy létrehozásakor meg kell határozni a definíció helye. A definíció helye egy felügyeleti csoportot vagy egy előfizetésben kell lennie. Ezen a helyen, amelyhez a kezdeményezést vagy rendelhetők a hatókör határozza meg. Erőforrások közvetlen tagjai vagy a gyermekek a hierarchiában a definíció helye, hogy a szabályzat-hozzárendelés kell lennie.

Ha a definíció helye v:

- **Előfizetés** – csak az adott előfizetésen belüli erőforrások rendelhetők a szabályzat.
- **A felügyeleti csoport** – csak az alárendelt felügyeleti csoportok és a gyermek előfizetések belüli erőforrások rendelhetők a szabályzatot. Ha azt tervezi, a szabályzat-definíció alkalmazni a különböző előfizetések, a hely ezen előfizetések tartalmazó felügyeleti csoportot kell lennie.

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

Használhat **displayName** és **leírás** azonosíthatja a szabályzat-definíció, és kontextusának esetén. a **DisplayName** maximális hossza _128_ karakter, és a **Leírás** legfeljebb _512_ karakter hosszúságú lehet.

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

A feltétel azt értékeli, hogy egy **mező** vagy az **érték** -hozzáférési megfelel-e bizonyos feltételeknek. A támogatott feltételek a következők:

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
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

Használatakor a **például** és **notLike** , feltételek meg helyettesítő karakter `*` értéke.
Az érték nem tartalmazhat egynél több helyettesítő `*`.

A egyezési  és **notMatch** `#` feltételek használatakor az adott számjegyre `?` , betűre, `.` bármilyen karakterre és bármely más karakterre illeszkedik, amely megfelel a tényleges karakternek.
a **Match** és a **notMatch** megkülönbözteti a kis-és nagybetűket. Kis-és nagybetűket megkülönböztető alternatívák a **matchInsensitively** és a **notMatchInsensitively**szolgáltatásban érhetők el. Példák: [lehetővé teszik több név minták](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Mezők

Feltételek alkotta mezőkkel. Egy mező megegyezik a tulajdonság az erőforrás-kérések forgalma, és az erőforrás állapotának leírása.

A következő mezők támogatottak:

- `name`
- `fullName`
  - Az erőforrás teljes nevét adja vissza. Erőforrás teljes név bármelyik szülő erőforrás nevét (például "myServer/myDatabase") által kiegészített az erőforrás nevét.
- `kind`
- `type`
- `location`
  - **Globálisan** használhatja az olyan erőforrásokat, amelyek a helytől függetlenek. Példaként tekintse meg a [minták – engedélyezett helyszínek](../samples/allowed-locations.md)című témakört.
- `identity.type`
  - Az erőforráson engedélyezett [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) típusát adja vissza.
- `tags`
- `tags['<tagName>']`
  - Ez a zárójel-szintaxis támogatja a írásjeleket, például kötőjelet, pontot vagy szóközt.
  - Ahol **\<tagName\>** feltételét ellenőrzése a címke neve.
  - Példák: `tags['Acct.CostCenter']` az **acct. CostCenter** a címke neve.
- `tags['''<tagName>''']`
  - Ez a zárójel-szintaxis támogatja az aposztrófokat tartalmazó címkéket dupla aposztrófokkal.
  - Ahol **a\<"\>TagName"** a címke neve, amely ellenőrzi a feltételt.
  - Példa: `tags['''My.Apostrophe.Tag''']` ahol **a\<"\>TagName"** a címke neve.
- vlastnost aliasok -, lásd: [aliasok](#aliases).

> [!NOTE]
> `tags.<tagName>`a, `tags[tag.with.dots]` , és továbbra is elfogadható módon deklarálhatja a címkék mezőt. `tags[tagName]` Az előnyben részesített kifejezések azonban a fentiekben láthatók.

#### <a name="use-tags-with-parameters"></a>Címkék használata paraméterekkel

Egy paraméter értéke adható át egy címke mezőnek. Ha egy paramétert egy címke mezőre továbbít, a házirend-hozzárendelés során növeli a házirend-definíció rugalmasságát.

A következő példában `concat` a **TagName** paraméter értékének megadásához a címkék mezőhöz tartozó keresőmezőt kell létrehozni. Ha ez a címke nem létezik, a **Hozzáfűzés** hatására a rendszer hozzáadja a címkét a naplózott erőforrások szülő erőforráscsoporthoz `resourcegroup()` tartozó megnevezett címke értékével a keresési függvénnyel.

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

A feltételek az **érték**használatával is létrehozhatók. az **érték** a [paraméterekkel](#parameters), a [támogatott sablon](#policy-functions)-funkciókkal vagy a literálokkal kapcsolatos feltételeket ellenőrzi.
az **érték** a támogatott [feltételekkel](#conditions)párosítva van.

> [!WARNING]
> Ha egy _sablon függvény_ eredménye hibát jelez, a szabályzat kiértékelése sikertelen lesz. A sikertelen értékelés implicit **Megtagadás**. További információ: a [sablon meghibásodásának elkerülése](#avoiding-template-failures).

#### <a name="value-examples"></a>Példák az értékekre

Ez a házirend-szabály például az **érték** használatával hasonlítja össze `resourceGroup()` a függvény eredményét és a visszaadott  `*netrg` **Name** tulajdonságot a hasonló feltétellel. A szabály minden olyan erőforrást megtagad, `Microsoft.Network/*` amely nem egy olyan erőforráscsoport, amelynek a  `*netrg`neve véget ér.

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

Ez a házirend-szabály például az **érték** használatával ellenőrizze, hogy a több beágyazott  `true`függvény eredménye egyenlő-e. A szabály minden olyan erőforrást megtagad, amely nem rendelkezik legalább három címkével.

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

#### <a name="avoiding-template-failures"></a>Sablon meghibásodásának elkerülése

A _template functions_ in **Value** használata számos összetett beágyazott függvényt tesz lehetővé. Ha egy _sablon függvény_ eredménye hibát jelez, a szabályzat kiértékelése sikertelen lesz. A sikertelen értékelés implicit **Megtagadás**. Egy példa olyan **értékre** , amely bizonyos helyzetekben meghiúsul:

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

A fenti példában az alsztring [()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) érték a **név** első három karakterének **ABC**-re való összevetését használja. Ha a **név** rövidebb, mint három karakter, `substring()` a függvény hibát eredményez. Ez a hiba azt eredményezi, hogy  a házirend megtagadási hatást vált ki.

Ehelyett a [IF ()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if) függvény használatával ellenőrizze, hogy az első három **karakter egyenlő-** e az **ABC** -vel anélkül, hogy a **név** három karakternél rövidebb legyen, ami hibát okozhat:

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

A módosított szabályzattal rendelkező szabály `if()` a **név** hosszát ellenőrzi, mielőtt egy olyan értéket `substring()` próbál meg elérni, amely kevesebb mint három karakterből áll. Ha a **név** túl rövid, a "nem az ABC-től kezdődően" értéket adja vissza, és az **ABC**-hez képest. Az **ABC** -vel nem kezdődő rövid névvel rendelkező erőforrás továbbra is meghiúsul a házirend-szabályban, de az értékelés során már nem okoz hibát.

### <a name="effect"></a>Következmény

Azure Policy a következő típusú hatásokat támogatja:

- **Megtagadási**: a tevékenységnapló eseményt hoz létre, és a kérelem meghiúsul
- **Naplózási**: állít elő, egy figyelmeztető esemény tevékenységnaplóban, de nem sikertelen a kérelem
- **Hozzáfűzés**: a meghatározott készletével mezőket ad hozzá a kérelem
- **AuditIfNotExists**: lehetővé teszi a naplózást, ha egy erőforrás nem létezik.
- **DeployIfNotExists**: üzembe helyezi egy erőforrást, ha még nem létezik
- **Letiltott**: nem erőforrások megfelelőségét, a szabály kiértékelése
- **EnforceRegoPolicy**: az Azure Kubernetes szolgáltatásban (előzetes verzió) megnyitja a házirend-ügynök beléptetési vezérlőjét.

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

Az egyes effektusok, a kiértékelési sorrend, a tulajdonságok és a példák részletes ismertetését lásd: a [Azure Policy effektusok ismertetése](effects.md).

### <a name="policy-functions"></a>A házirend-funkciók

Az összes [Resource Manager-sablon funkció](../../../azure-resource-manager/resource-group-template-functions.md) egy házirend-szabályon belül használható, az alábbi függvények és a felhasználó által definiált függvények kivételével:

- copyIndex ()
- üzembe helyezés ()
- list*
- newGuid()
- pickZones()
- szolgáltatók ()
- hivatkozás ()
- resourceId()
- változók ()

A következő függvények használhatók egy házirend-szabályban, de különböznek a Azure Resource Manager sablonban lévő használattól:

- addDays (dateTime, numberOfDaysToAdd)
  - **datetime**: [Required] String-String in the Universal ISO 8601 datetime Format éééé-hh-NNTóó: PP: SS. fffffffZ '
  - **numberOfDaysToAdd**: [kötelező] egész szám – hozzáadandó napok száma
- utcNow () – a Resource Manager-sablonoktól eltérően ez a defaultValue kívül is használható.
  - Egy olyan karakterláncot ad vissza, amely az univerzális ISO 8601 DateTime formátumban van beállítva az aktuális dátumra és időpontra vonatkozóan (éééé-hh-NNTóó: PP: SS. fffffffZ).

Ezenkívül a `field` funkció érhető el a szabályzat előírásainak. `field` elsősorban az **AuditIfNotExists** és **DeployIfNotExists** referencia mezők, a rendszer kiértékelt erőforrás. Ezt használhatja például látható a [DeployIfNotExists példa](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Példa a házirend-függvényre

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

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API-t / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>A [*] alias ismertetése

Az aliasok rendelkezésre álló számos rendelkezik egy olyanra, amely egy "normál" nevet, és a egy másik néven jelenik meg, amely rendelkezik **[\*]** csatlakozik. Példa:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

A "normál" alias a mezőt egyetlen értékként jelöli. Ez a mező az összehasonlítási forgatókönyvek pontos egyeztetésére szolgál, ha az értékek teljes halmazának pontosan meghatározottnak kell lennie, nem több és nem kevesebb.

A **[\*]** alias lehetővé teszi, hogy összehasonlítsa a tömbben lévő egyes elemek értékével és az egyes elemek adott tulajdonságaival. Ez a módszer lehetővé teszi, hogy összehasonlítsa a "Ha nincs", "," vagy "Ha az összes" forgatókönyvhöz tartozó elem tulajdonságait. A **ipRules [\*]** használatával egy példa azt ellenőrzi, hogy minden _művelet_ megtagadva van-e, de nem kell aggódnia, hogy hány szabály létezik, vagy hogy mi az IP- _érték_ . Ez a minta-szabály a következő **ipRules keresi:\*[]. az érték** a **10.0.4.1** , és csak akkor alkalmazza a **effectType** , ha nem talál legalább egy egyezést:

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

További információ: [[\*] alias](../how-to/author-policies-for-arrays.md#evaluating-the--alias)kiértékelése.

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

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott](../how-to/programmatically-create.md)módon házirendeket.
- Ismerje meg, hogyan kérheti le a [megfelelőségi információkat](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).