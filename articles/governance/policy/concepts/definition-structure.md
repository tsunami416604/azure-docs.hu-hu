---
title: A politikadefiníciós struktúra részletei
description: Bemutatja, hogyan használják a szabályzatdefiníciók at a szervezetben az Azure-erőforrásokra vonatkozó konvenciók létrehozásához.
ms.date: 04/03/2020
ms.topic: conceptual
ms.openlocfilehash: 0a7c4e05270ff242fa97b253b27a5de92895368a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461004"
---
# <a name="azure-policy-definition-structure"></a>Azure szabályzatdefiníciók struktúrája

Az Azure Policy erőforrásokra vonatkozó konvenciókat hoz létre. A házirend-definíciók leírják az erőforrás-megfelelőségi [feltételeket](#conditions) és a feltétel teljesülése esetén végrehajtandó hatást. Egy feltétel egy [erőforrástulajdonság-mezőt](#fields) hasonlít össze a szükséges értékkel. Az erőforrástulajdonság-mezők [aliasok](#aliases)használatával érhetők el. Az erőforrástulajdonság-mező egy értékű mező vagy több értékből álló [tömb.](#understanding-the--alias) Az állapotkiértékelése tömbökön eltérő.
További információ a [feltételekről.](#conditions)

A konvenciók meghatározásával szabályozhatja a költségeket, és könnyebben kezelheti az erőforrásokat. Megadhatja például, hogy csak bizonyos típusú virtuális gépek engedélyezettek. Vagy megkövetelheti, hogy minden erőforrásnak legyen egy adott címkéje. A házirendeket az összes gyermekerőforrás örökli. Ha egy házirendet alkalmaz egy erőforráscsoportra, akkor az az adott erőforráscsoport összes erőforrására vonatkozik.

A házirend-definíciós séma itt található:[https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

A JSON használatával hozzon létre egy házirend-definíciót. A házirend-definíció a következő elemeket tartalmazza:

- mód
- paraméterek
- megjelenítendő név
- leírás
- házirend szabály
  - logikai értékelés
  - Hatás

A következő JSON például egy házirendet jelenít meg, amely korlátozza az erőforrások üzembe helyezésének helyeit:

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

Az Azure Policy-minták az [Azure Policy-mintákban](../samples/index.md)találhatók.

## <a name="mode"></a>Mód

**A mód** attól függően van konfigurálva, hogy a szabályzat egy Azure Resource Manager-tulajdonságot vagy egy Erőforrás-szolgáltató tulajdonságot céloz-e meg.

### <a name="resource-manager-modes"></a>Erőforrás-kezelő isztikai

A **mód** határozza meg, hogy mely erőforrástípusokat kell kiértékelni egy házirendhez. A támogatott módok a következők:

- `all`: erőforráscsoportok és az összes erőforrástípus kiértékelése
- `indexed`: csak a címkéket és a helyet támogató erőforrástípusokat

Például az `Microsoft.Network/routeTables` erőforrás támogatja a címkéket és a helyet, és mindkét módban kiértékeli. Az erőforrás `Microsoft.Network/routeTables/routes` azonban nem címkézhető, és `Indexed` nem értékelhető ki módban.

Azt javasoljuk, hogy `all` a legtöbb esetben állítsa be a **módot.** A portálon keresztül létrehozott összes `all` házirend-definíció a módot használja. PowerShell vagy Azure CLI használata esetén **mode** manuálisan is megadhatja a módparamétert. Ha a szabályzatdefiníció nem tartalmaz **módi** értéket, `all` alapértelmezés szerint az `null` Azure PowerShellben és az Azure CLI-ben. A `null` mód megegyezik `indexed` a visszamenőleges kompatibilitás támogatásával használt móddal.

`indexed`címkéket vagy helyeket érvényesítő házirendek létrehozásakor kell használni. Bár nem szükséges, megakadályozza, hogy a címkéket és helyeket nem támogató erőforrások nem megfelelőként jelenjenek meg a megfelelőségi eredményekben. Kivételt képeznek az **erőforráscsoportok.** Az erőforráscsoporton helyeket vagy címkéket `all` kikényszerítő házirendeknek a **módot** a típusra kell beállítaniuk, és kifejezetten a `Microsoft.Resources/subscriptions/resourceGroups` kívánt típust kell megcélozniuk. Például az [Erőforráscsoport-címkék kényszerítése](../samples/enforce-tag-rg.md)című témakörben található. A címkéket támogató erőforrások listáját az [Azure-erőforrások támogatásának címkézése (Tag for Azure) (Címke) témakörben](../../../azure-resource-manager/management/tag-support.md)található.

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes" />Erőforrás-szolgáltató isznak (előzetes verzió)

Az előzetes verzióban jelenleg a következő erőforrás-szolgáltató módok támogatottak:

- `Microsoft.ContainerService.Data`az [Azure Kubernetes-szolgáltatás](../../../aks/intro-kubernetes.md)felvételi vezérlőszabályainak kezeléséhez. Az erőforrás-szolgáltató módot használó **házirendeknek** az [EnforceRegoPolicy](./effects.md#enforceregopolicy) effektust kell használniuk.
- `Microsoft.Kubernetes.Data`az Azure-beli Önfelügyelt AKS Engine Kubernetes-fürtök kezeléséhez.
  Az erőforrás-szolgáltató módot használó **házirendeknek** az [EnforceOPAConstraint effektust](./effects.md#enforceopaconstraint) kell használniuk.
- `Microsoft.KeyVault.Data`az [Azure Key Vault](../../../key-vault/general/overview.md)ban lévő tárolók és tanúsítványok kezeléséhez.

> [!NOTE]
> Az erőforrás-szolgáltató imáta csak a beépített házirend-definíciókat támogatja, és nem támogatja a kezdeményezéseket előzetes verzióban.

## <a name="parameters"></a>Paraméterek

A paraméterek a házirend-definíciók számának csökkentésével megkönnyítik a házirendek kezelését. Gondoljon olyan paraméterekre, mint `name`az `address` `city`űrlap `state`mezői – , , , . Ezek a paraméterek mindig változatlanok maradnak, azonban értékeik az űrlap kitöltésétől függően változnak.
A paraméterek ugyanúgy működnek a házirendek létrehozásakor. Azáltal, hogy paramétereket a házirend-definíció, akkor újra, hogy a szabályzat a különböző forgatókönyvek különböző értékek használatával.

> [!NOTE]
> Paraméterek adhatók hozzá egy meglévő és hozzárendelt definícióhoz. Az új paraméternek tartalmaznia kell a **defaultValue** tulajdonságot. Ez megakadályozza, hogy a házirend vagy kezdeményezés meglévő hozzárendelései közvetetten érvénytelenné tévődjenek.

### <a name="parameter-properties"></a>Paraméter tulajdonságai

Egy paraméter a következő tulajdonságokkal rendelkezik, amelyek a házirend-definícióban használatosak:

- **name**: A paraméter neve. A központi `parameters` telepítési függvény használja a házirendszabályon belül. További információ: [Paraméterérték használata](#using-a-parameter-value).
- `type`: Meghatározza, hogy a paraméter **karakterlánc,** **tömb,** **objektum,** **logikai,** **egész**szám, **lebegtetés**vagy **datetime.**
- `metadata`: Az Azure Portal által elsősorban felhasználóbarát információk megjelenítésére használt altulajdonságokat határozza meg:
  - `description`: Annak magyarázata, hogy mire használják a paramétert. Elfogadható értékek példáinak megadására használható.
  - `displayName`: A paraméter portálján megjelenő rövid név.
  - `version`: (Nem kötelező) nyomon követi a házirend-definíció tartalmának részleteit.

    > [!NOTE]
    > Az Azure Policy `version` `preview`szolgáltatás `deprecated` a , és a tulajdonságok segítségével közvetíti a változás szintjét egy beépített szabályzat definíciójának vagy kezdeményezésének és állapotának. A formátum `version` a `{Major}.{Minor}.{Patch}`következő: . Bizonyos állapotok, például _elavult_ vagy _előnézet_, `version` **logikai értékként**kerülnek a tulajdonsághoz vagy egy másik tulajdonsághoz.

  - `category`: (Nem kötelező) Határozza meg, hogy az Azure Portalon melyik kategória alatt jelenik meg a szabályzatdefiníció.
  - `strongType`: (Nem kötelező) A házirend-definíció portálon keresztüli hozzárendelésekénként használatos. Környezettudatos listát tartalmaz. További információt a [strongType című témakörben](#strongtype)talál.
  - `assignPermissions`: (Nem kötelező) Állítsa be _true_ beállítását, hogy az Azure Portal szerepkör-hozzárendeléseket hozzon létre a házirend-hozzárendelés során. Ez a tulajdonság akkor hasznos, ha a hozzárendeléshatókörön kívül kíván engedélyeket hozzárendelni. A házirendben szerepkör-definíciónként egy szerepkör-hozzárendelés van (vagy szerepkör-definíciónként a kezdeményezés összes házirendjében). A paraméterértéknek érvényes erőforrásnak vagy hatókörnek kell lennie.
- `defaultValue`: (Nem kötelező) A hozzárendelés paraméterének értékét állítja be, ha nincs megadva érték.
  Meglévő házirend-definíció konkretizálásakor szükséges.
- `allowedValues`: (Nem kötelező) Olyan értékek tömbjét adja meg, amelyeket a paraméter a hozzárendelés során elfogad.

Például definiálhat egy szabályzatdefiníciót, amely korlátozza azokat a helyeket, ahol az erőforrások telepíthetők. A házirend-definíció egyik paramétere **lehet engedélyezettLocations**. Ezt a paramétert a házirend-definíció minden hozzárendelése az elfogadott értékek korlátozására használná. A **strongType** használata továbbfejlesztett élményt nyújt a hozzárendelés portálon keresztültörténő befejezésekor:

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

### <a name="using-a-parameter-value"></a>Paraméterérték használata

A házirendszabályban a következő `parameters` függvény szintaxissal rendelkező paraméterekre hivatkozik:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ez a minta a [paramétertulajdonságaiban](#parameter-properties)bemutatott **allowedLocations** paraméterre hivatkozik.

### <a name="strongtype"></a>strongType típus

A `metadata` tulajdonságon belül **használhatja strongType** az Azure Portalon belüli több-select lehetőségek listájának megadásához. **A strongType** lehet támogatott _erőforrástípus_ vagy engedélyezett érték. Annak megállapításához, hogy egy _erőforrástípus_ érvényes-e a strongType típusra, használja a [Get-AzResourceProvider .to](/powershell/module/az.resources/get-azresourceprovider)determine if a resource type is valid for **strongType**, use Get-AzResourceProvider .

A **Get-AzResourceProvider** által vissza nem adott _egyes erőforrástípusok_ támogatottak. Ezek a következők:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

A **strongType** nem _erőforrástípus_ által engedélyezett értékei a következők:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Definíció helye

Kezdeményezés vagy házirend létrehozása közben meg kell adni a definíció helyét. A definíció helyének felügyeleti csoportnak vagy előfizetésnek kell lennie. Ez a hely határozza meg azt a hatókört, amelyhez a kezdeményezés vagy a házirend hozzárendelhető. Az erőforrásoknak a definícióhely hierarchiáján belül közvetlen tagjainak vagy gyermekeinek kell lenniük a hozzárendeléshez.

Ha a definíció helye a következő:

- **Előfizetés** – Csak az adott előfizetésen belüli erőforrások rendelhetők hozzá a szabályzathoz.
- **Felügyeleti csoport** – csak a gyermekkezelési csoportokon belüli erőforrások és a gyermek-előfizetések rendelhetők hozzá a szabályzathoz. Ha azt tervezi, hogy a szabályzat definícióját több előfizetésre is alkalmazza, a helynek olyan felügyeleti csoportnak kell lennie, amely ezeket az előfizetéseket tartalmazza.

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

A **displayName** és a **description** segítségével azonosíthatja a házirend-definíciót, és megadhat kontextust a használathoz. **A displayName** maximális hossza _128_ karakter, **a leírása** pedig _legfeljebb 512_ karakter.

> [!NOTE]
> A házirend-definíció létrehozása vagy frissítése során az **id**, **type**és **name** a JSON-on kívüli tulajdonságok határozzák meg, és nem szükségesek a JSON-fájlban. A házirend-definíció SDK-n keresztüli lekérése a JSON részeként az **azonosító**, **a típus**és a **név** tulajdonságokat adja vissza, de mindegyik csak olvasható információ a házirend-definícióhoz.

## <a name="policy-rule"></a>Házirendszabály

A házirendszabály az **If** és **majd** a blokkokból áll. Az **Ha** blokkban meg kell adniegy vagy több feltételt, amelyek meghatározzák, hogy a házirend mikor van érvényben. Logikai operátorokat alkalmazhat ezekre a feltételekre, hogy pontosan meghatározza a házirend forgatókönyvét.

Az **Ezután** blokkban megadhatja azt a hatást, amely akkor következik be, amikor az **If** feltételek teljesülnek.

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

A támogatott logikai operátorok a következők:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

A **nem** szintaxis megfordítja a feltétel eredményét. Az **allOf** szintaxis (hasonlóan a logikai **és** művelethez) megköveteli, hogy minden feltétel igaz legyen. Az **anyOf** szintaxis (hasonlóan a logikai **vagy** művelethez) egy vagy több feltétel takarásához szükséges.

Logikai operátorokat egymásba ágyazhat. A következő példa egy **nem** műveletet mutat be, amely egy **allOf** műveletbe van ágyazva.

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

A feltétel azt vizsgálja, hogy egy **mező** vagy az **érték-elérő** megfelel-e bizonyos feltételeknek. A támogatott feltételek a következők:

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` | `"greaterOrEquals": intValue`
- `"exists": "bool"`

**Kevesebb**, **lessOrEquals**, **nagyobb**és **nagyobbOrEquals**esetén, ha a tulajdonságtípus nem felel meg a feltétel típusának, a rendszer hibát jelez. A karakterlánc-összehasonlítások `InvariantCultureIgnoreCase`a használatával történnek.

A **hasonló** és **nemhasonló** feltételek használatakor `*` helyettesítő karaktert kell megadnia az értékben.
Az értéknek nem lehet több `*`helyettesítő karaktere.

Ha az **egyezési** és `#` **nemMatch** feltételeket használja, adja meg, hogy egy ez egyszámjegy, `?` egy betű, `.` hogy megfeleljen minden karakter, és minden más karakter, hogy megfeleljen a tényleges karakter. Míg az **egyezés** és **a notMatch** a kis- és nagybetűket, a _stringValue-t_ kiértékelő összes többi feltétel nem szokja ki a kis- és nagybetűket. A kis- és nagybetűket nem megkülönböztető alternatívák a **matchInsensitively** és **notMatchInsensitively**egyezésben érhetők el.

Az aliastömb mezőértékében a tömb minden eleme külön-külön kiértékelődött logikai **és** elemek között. ** \[ \* \] ** További információt [az \[ \* \] Alias kiértékelése](../how-to/author-policies-for-arrays.md#evaluating-the--alias)című témakörben talál.

### <a name="fields"></a>Mezők

A feltételek mezők használatával jönnek létre. Egy mező megfelel az erőforráskérelem hasznos adatának tulajdonságainak, és leírja az erőforrás állapotát.

A következő mezők támogatottak:

- `name`
- `fullName`
  - Az erőforrás teljes nevét adja eredményül. Az erőforrás teljes neve a szülőerőforrás-nevek (például "myServer/myDatabase") által előkészített erőforrásnév.
- `kind`
- `type`
- `location`
  - Globális **global** használata a helyfüggetlen erőforrásokhoz.
- `identity.type`
  - Az erőforráson engedélyezett [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) típusát adja vissza.
- `tags`
- `tags['<tagName>']`
  - Ez a zárójelszintaxis támogatja az írásjeleket , például kötőjelet, pontot vagy szóközt tartalmazó címkeneveket.
  - Ahol ** \<tagName\> ** a feltétel érvényesítéséhez szükséges címke neve.
  - Példák: `tags['Acct.CostCenter']` ahol az **Acct.CostCenter** a címke neve.
- `tags['''<tagName>''']`
  - Ez a zárójel esszinaxis támogatja a címke neveket, amelyek aposztróf benne a menekülés kettős aposztróf.
  - Ahol **\<a\>" tagName "** a feltétel érvényesítéséhez szükséges címke neve.
  - Példa: `tags['''My.Apostrophe.Tag''']` ahol a **"My.Apostrophe.Tag"** a címke neve.
- tulajdonságaliasok - listához lásd: [Aliases](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]`és `tags[tag.with.dots]` továbbra is elfogadható módon deklarálegy címkemezőt. Az előnyben részesített kifejezések azonban a fent felsoroltak.

#### <a name="use-tags-with-parameters"></a>Címkék használata paraméterekkel

Egy paraméterérték átadható egy címkemezőnek. Ha egy paramétert átad egy címkemezőnek, az növeli a házirend-definíció rugalmasságát a házirend-hozzárendelés során.

A következő példában `concat` a **tagName** paraméter értékének nevezett címke címkemező-lekérdezésének létrehozására szolgál. Ha ez a címke nem létezik, a **módosítási** hatás segítségével adja hozzá a címkét az azonos nevű `resourcegroup()` címke értéke a naplózott erőforrások szülő erőforráscsoport segítségével a lookup függvény.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Érték

Feltételek is kialakíthatók **az érték**használatával. **az érték** a paraméterekkel, [a támogatott sablonfüggvényekkel](#policy-functions)vagy a konstansokkal szemben ellenőrzi a feltételeket. [parameters](#parameters)
**az érték** bármely támogatott [feltétellel](#conditions)párosítva van.

> [!WARNING]
> Ha egy _sablonfüggvény_ eredménye hiba, a házirend kiértékelése sikertelen lesz. A sikertelen kiértékelés implicit **megtagadás.** További információt a [sablonhibák elkerülése](#avoiding-template-failures)című témakörben talál. A **DoNotEnforce** [kényszerítési móddal](./assignment-structure.md#enforcement-mode) megelőzheti a sikertelen kiértékelés új vagy frissített erőforrásokra gyakorolt hatását az új házirend-definíció tesztelése és érvényesítése közben.

#### <a name="value-examples"></a>Példák értékre

Ez a házirendszabály-példa **a** függvény `resourceGroup()` és a visszaadott **név** tulajdonság `*netrg`eredményét hasonlítja össze a **hasonló** feltételével. A szabály minden olyan `Microsoft.Network/*` erőforrást megtagad, amely `*netrg`nem olyan **típusú,** mint bármely erőforráscsoport, amelynek neve a végén.

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

Ez a házirendszabály-példa **értékkel** ellenőrzi, hogy a több beágyazott függvény eredménye **egyenlő-e.** `true` A szabály minden olyan erőforrást megtagad, amely nem rendelkezik legalább három címkével.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Sablonhibák elkerülése

A _sablonfüggvények_ **értékben** való használata számos összetett beágyazott függvényt tesz lehetővé. Ha egy _sablonfüggvény_ eredménye hiba, a házirend kiértékelése sikertelen lesz. A sikertelen kiértékelés implicit **megtagadás.** Példa olyan **értékre,** amely bizonyos esetekben sikertelen:

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

A fenti példaházirend-szabály [a név](../../../azure-resource-manager/templates/template-functions-string.md#substring) első három **karakterét** hasonlítja össze az **abc-vel.** Ha a **név** három karakternél rövidebb, a `substring()` függvény hibát eredményez. Ez a hiba hatására a házirend **megtagadási** hatással bír.

Ehelyett használja az [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if) függvénnyel annak ellenőrzését, hogy a **név** első három karaktere **megegyezik-e az abc-vel** anélkül, hogy három karakternél rövidebb **nevet** engedélyezne a hiba:

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

A módosított házirend-szabály `if()` segítségével ellenőrzi a **név** hosszát, mielőtt megpróbálna egy `substring()` három karakternél rövidebb értéket beszerezni. Ha a **név** túl rövid, a "nem az abc-vel kezdődő" értéket adja vissza, és összehasonlítja az **abc-vel.** Az **abc-vel** nem kezdődő rövid nevű erőforrás továbbra is meghiúsul a házirend-szabályon, de a kiértékelés során már nem okoz hibát.

### <a name="count"></a>Darabszám

Feltételek, amelyek megszámolják, hogy egy tömb hány tagja az erőforrás hasznos terhelés megfelel egy feltétel kifejezés segítségével **count** expression. A gyakori forgatókönyvek annak ellenőrzése, hogy a tömbtagok "legalább egy", "pontosan egy az", "all of" vagy "none of" a tömbtagok megfelelnek-e a feltételnek. **A count** kiértékeli az [ \[ \* \] aliastömbök](#understanding-the--alias) minden tagját egy feltételkifejezéshez, és összegzi a _valódi_ eredményeket, amelyet ezután a kifejezésoperátorral hasonlít.

A **count** kifejezés szerkezete:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

A következő tulajdonságok at használjuk **a darabszámmal:**

- **count.field** (kötelező): A tömb elérési útját tartalmazza, és tömbaliasnak kell lennie. Ha a tömb hiányzik, a kifejezés kiértékelése _hamis,_ a feltételkifejezés figyelembe nem hagyása nélkül.
- **count.where** (nem kötelező): A **count.field**minden [ \[ \* \] aliastömb-tagjának](#understanding-the--alias) egyedi kiértékelésére használt feltételkifejezés. Ha ez a tulajdonság nincs megadva, a program a "mező" elérési úttal rendelkező összes tömbtagot _igaz_értékre értékeli ki. A tulajdonságon belül bármilyen [feltétel](../concepts/definition-structure.md#conditions) használható.
  [A logikai operátorok](#logical-operators) ezen a tulajdonságon belül összetett értékelési követelmények létrehozására használhatók.
- feltétel (kötelező): Az értéket a **count.where** feltételkifejezésnek megtaszó elemek számával hasonlítja össze. ** \<\> ** Numerikus [feltételt](../concepts/definition-structure.md#conditions) kell használni.

#### <a name="count-examples"></a>Példák számlálása

1. példa: Ellenőrizze, hogy üres-e egy tömb

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

2. példa: Csak egy tömbtag ellenőrzése a feltételkifejezés nek való megfeleltetéshez

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

3. példa: Ellenőrizze, hogy legalább egy tömbtag megfelel-e a feltételkifejezésnek

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

4. példa: Ellenőrizze, hogy az objektumtömb összes tagja megfelel-e a feltételkifejezésnek

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

5. példa: Ellenőrizze, hogy minden karakterlánctömb-tag megfelel-e a feltételkifejezésnek

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

6. példa: **Mező** belső **értékének** használata annak ellenőrzéséhez, hogy a tömb összes tagja megfelel-e a feltételkifejezésnek

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

7. példa: Ellenőrizze, hogy legalább egy tömbtag megfelel-e a feltételkifejezés több tulajdonságának.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Hatás

Az Azure Policy a következő típusú effektusokat támogatja:

- **Hozzáfűzés:** hozzáadja a megadott mezőkészletet a kéréshez
- **Naplózás**: figyelmeztető eseményt hoz létre a tevékenységnaplóban, de nem mulasztja el a kérést
- **AuditIfNotExists**: figyelmeztető eseményt hoz létre a tevékenységnaplóban, ha nem létezik kapcsolódó erőforrás
- **Megtagadás**: eseményt hoz létre a tevékenységnaplóban, és nem teljesíti a kérést
- **DeployIfNotExists**: egy kapcsolódó erőforrást telepít, ha az még nem létezik
- **Letiltva:** nem értékeli ki az erőforrásokat az irányelvszabálynak való megfelelés miatt
- **EnforceOPAConstraint** (előzetes verzió): konfigurálja a Nyílt házirend-ügynök felvételi vezérlőjét a Gatekeeper v3-mal az Azure-beli, saját felügyelt Kubernetes-fürtökhöz (előzetes verzió)
- **EnforceRegoPolicy** (előzetes verzió): konfigurálja a Nyílt házirend-ügynök felvételi vezérlőgatekeeper v2 az Azure Kubernetes szolgáltatás
- **Módosítás**: hozzáadja, frissíti vagy eltávolítja a definiált címkéket egy erőforrásból

Az egyes hatásokról, a kiértékelés sorrendjéről, a tulajdonságokról és a példákról az [Azure policy effects ismertetése](effects.md)című témakörben talál részletesinformációt.

### <a name="policy-functions"></a>Házirend-funkciók

Az [Erőforrás-kezelő sablonfunkciói](../../../azure-resource-manager/templates/template-functions.md) egy házirendszabályon belül használhatók, kivéve a következő függvényeket és a felhasználó által definiált függvényeket:

- copyIndex()
- telepítés()
- lista*
- newGuid()
- pickZones()
- szolgáltatók()
- hivatkozás()
- resourceId()
- változók()

> [!NOTE]
> Ezek a függvények `details.deployment.properties.template` továbbra is elérhetők a sablon központi telepítésének egy **deployIfNotExists** házirend-definícióban.

A következő függvény használható egy házirendszabályban, de eltér az Azure Resource Manager-sablonban való használattól:

- `utcNow()`- Az Erőforrás-kezelő sablonnal ellentétben ez a defaultValue értéken kívül is használható.
  - Az aktuális dátumra és időre beállított karakterláncot ad eredményül az "yyyy-MM-ddTHH:mm:ss.fffffffZ" univerzális ISO 8601 DateTime formátumban.

A következő függvények csak a házirendszabályokban érhetők el:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime**: [Kötelező] karakterlánc - String az Univerzális ISO 8601 DateTime formátumban "yyy-MM-ddTHH:mm:ss.fffffffZ"
  - **numberOfDaysToAdd**: [Kötelező] egész szám - A hozzáadni kívánt napok száma
- `field(fieldName)`
  - **fieldName**: [Kötelező] karakterlánc - A beolvasandó [mező](#fields) neve
  - Ennek a mezőnek az értékét adja eredményül az If feltétel által kiértékelt erőforrásból.
  - `field`elsősorban **az AuditIfNotExists** és **a DeployIfNotExists** segítségével hivatkozik a kiértékelt erőforrás mezőire. Erre a használatra példa látható a [DeployIfNotExists például.](effects.md#deployifnotexists-example)
- `requestContext().apiVersion`
  - A házirend-kiértékelést kiváltó kérelem API-verzióját `2019-09-01`adja vissza (példa: ).
    Ez lesz az az API-verzió, amelyet a PUT/PATCH kérelemben az erőforrás-létrehozás/-frissítés kiértékelésére használtak. A legújabb API-verzió mindig a meglévő erőforrások megfelelőségi kiértékelése során használatos.
  
#### <a name="policy-function-example"></a>Példa házirend-függvényre

Ez a házirendszabály-példa az erőforrásfüggvény t `resourceGroup` használja a **névtulajdonság** leéséhez a `concat` tömb- és objektumfüggvénnyel kombinálva egy `like` olyan feltétel létrehozásához, amely az erőforrásnevét az erőforráscsoport nevével kezdi.

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

Tulajdonságaliasok használatával férhet hozzá egy erőforrástípus bizonyos tulajdonságaihoz. Az aliasok lehetővé teszik, hogy korlátozza, milyen értékek vagy feltételek engedélyezettek egy erőforrás egy tulajdonságához. Minden alias leképezi az adott erőforrástípus különböző API-verziókelérési útjait. A házirend-kiértékelés során a házirend-motor lekéri az API-verzió tulajdonságelérési útját.

Az álnevek listája folyamatosan növekszik. Ha meg szeretné tudni, hogy az Azure Policy jelenleg milyen aliasokat támogat, használja az alábbi módszerek egyikét:

- Azure Policy bővítmény a Visual Studio-kódhoz (ajánlott)

  Használja az [Azure Policy bővítmény t a Visual Studio-kód](../how-to/extension-for-vscode.md) az erőforrás-tulajdonságok aliasok megtekintéséhez és felderítéséhez.

  ![Azure-szabályzat bővítmény a Visual Studio-kódhoz](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Az `project` operátor segítségével jelenítse meg egy erőforrás **aliasát.**

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

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

- REST API / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>A[*] alias ismertetése

A rendelkezésre álló aliasok közül többnek van egy "normál" ** \[ \* ** névként megjelenő verziója, egy másik pedig hozzá csatolva. Például:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

A "normál" alias a mezőt egyetlen értékként jelöli. Ez a mező a pontos egyezéses összehasonlítási esetekre szolgál, amikor a teljes értékhalmaznak pontosan a meghatározottnak kell lennie, nem többnek és nem kevesebbnek.

** \[ Az \* ** alias lehetővé teszi az összehasonlítást a tömb egyes elemeinek értékével és az egyes elemek adott tulajdonságaival. Ez a megközelítés lehetővé teszi az elemtulajdonságok összehasonlítását a "ha egyik sem", "ha egyáltalán" vagy "ha az összes" forgatókönyvek. Összetettebb esetekben használja a [count](#count) feltétel kifejezést. Az **\[\*ipRules**használatával egy példa az lenne, ha minden _művelet_ _Megtagadás_, de nem kell aggódnia, hogy hány szabály létezik, vagy mi az _IP-érték._
Ez a mintaszabály az **\[\*\]ipRules .value** **10.0.4.1-re** való egyeztetését ellenőrzi, és csak akkor alkalmazza a **effectType függvényt,** ha nem talál legalább egy egyezést:

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

További információt [a [\*] alias kiértékelése](../how-to/author-policies-for-arrays.md#evaluating-the--alias)című témakörben talál.

## <a name="initiatives"></a>Kezdeményezések

A kezdeményezések lehetővé teszik több kapcsolódó házirend-definíció csoportosítását a hozzárendelések és a felügyelet egyszerűsítése érdekében, mivel egy csoporttal egyetlen elemként dolgozik. A kapcsolódó címkézési házirend-definíciókat például egyetlen kezdeményezésbe csoportosíthatja. Ahelyett, hogy minden egyes házirendet külön-külön rendelne hozzá, a kezdeményezést alkalmazza.

> [!NOTE]
> A kezdeményezés hozzárendelése után a kezdeményezésszintű paraméterek nem módosíthatók. Ennek köszönhetően a javaslat az, hogy a paraméter definiálásakor **alapértelmezett értéket** állítson be.

A következő példa bemutatja, hogyan hozhat létre `costCenter` `productName`kezdeményezést két címke kezelésére: és a. Két beépített házirendet használ az alapértelmezett címkeérték alkalmazásához.

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
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
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
    }
}
```

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](../how-to/programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](../how-to/get-compliance-data.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](../how-to/remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)
