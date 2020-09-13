---
title: A házirend-definíciós struktúra részletei
description: Leírja, hogyan használhatók a szabályzat-definíciók a szervezeten belüli Azure-erőforrásokra vonatkozó konvenciók létrehozásához.
ms.date: 08/27/2020
ms.topic: conceptual
ms.openlocfilehash: 81e08e07236d445a4ca351a7d93e7851cad69ace
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648718"
---
# <a name="azure-policy-definition-structure"></a>Azure szabályzatdefiníciók struktúrája

Azure Policy az erőforrásokra vonatkozó konvenciókat hoz létre. A szabályzat-definíciók írják le az erőforrás-megfelelőségi [feltételeket](#conditions) , valamint azt, hogy egy adott feltétel teljesül-e. A feltétel egy erőforrás-tulajdonság [mezőjét](#fields) hasonlítja össze egy szükséges értékkel. Az erőforrás-tulajdonságok mezői [aliasok](#aliases)használatával érhetők el. Az erőforrás-tulajdonság mező egy egyértékű mező vagy több értékből álló [tömb](#understanding-the--alias) . A feltétel kiértékelése eltér a tömböknél.
További információ a [feltételekről](#conditions).

Az egyezmények meghatározásával szabályozhatja a költségeket, és könnyebben kezelheti az erőforrásokat. Megadhatja például, hogy csak bizonyos típusú virtuális gépek engedélyezettek legyenek. Azt is megkövetelheti, hogy az erőforrások egy adott címkével rendelkezzenek. A házirend-hozzárendeléseket a gyermek erőforrások öröklik. Ha a szabályzat-hozzárendelést egy erőforráscsoporthoz alkalmazza, az az adott erőforráscsoport összes erőforrására érvényes lesz.

A szabályzat-definíció _' policyrule osztály_ sémája itt található: [https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

A JSON használatával hozhat létre szabályzat-definíciót. A házirend-definíció a következő elemeit tartalmazza:

- megjelenítendő név
- leírás
- mód
- metaadatok
- parameters
- házirend-szabály
  - logikai Értékelés
  - érvénybe

A következő JSON például egy olyan szabályzatot mutat be, amely korlátozza az erőforrások központi telepítését:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
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

Azure Policy a beépített és a minták a [Azure Policy mintákban](../samples/index.md)találhatók.

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

A **DisplayName** és a **Leírás** használatával azonosíthatja a házirend-definíciót, és megadhatja a környezetét a használathoz. a **DisplayName** maximális hossza _128_ karakter, és a **Leírás** legfeljebb _512_ karakter hosszúságú lehet.

> [!NOTE]
> A házirend-definíció létrehozása vagy frissítése során a JSON-on kívüli tulajdonságok definiálják az **azonosítót**, a **típust**és a **nevet** , és nem szükségesek a JSON-fájlban. Az SDK-n keresztüli szabályzat-definíció beolvasása az **azonosító**, a **típus**és a **név** tulajdonságokat adja vissza a JSON részeként, de mindegyik írásvédett információ a házirend-definícióhoz kapcsolódik.

## <a name="type"></a>Típus

Amíg a **Type (típus** ) tulajdonság nem állítható be, az SDK által visszaadott három érték jelenik meg a portálon:

- `Builtin`: Ezeket a szabályzat-definíciókat a Microsoft biztosította és tartja karban.
- `Custom`: Az ügyfelek által létrehozott összes házirend-definíció rendelkezik ezzel az értékkel.
- `Static`: A Microsoft **tulajdonában**lévő [szabályozási megfelelőségi](./regulatory-compliance.md) szabályzat definícióját jelzi. Ezeknek a szabályzat-definícióknak a megfelelőségi eredményei a Microsoft-infrastruktúrával kapcsolatos harmadik féltől származó ellenőrzések eredményei. A Azure Portalban ez az érték időnként **Microsoft által felügyelt**jelenik meg. További információ: [megosztott felelősség a felhőben](../../../security/fundamentals/shared-responsibility.md).

## <a name="mode"></a>Mód

A **mód** attól függően van konfigurálva, hogy a házirend Azure Resource Manager tulajdonságot vagy erőforrás-szolgáltatói tulajdonságot céloz meg.

### <a name="resource-manager-modes"></a>Resource Manager-módok

A **mód** határozza meg, hogy mely erőforrástípusok legyenek kiértékelve egy házirend-definícióhoz. A támogatott módok a következők:

- `all`: erőforráscsoportok, előfizetések és minden erőforrástípus kiértékelése
- `indexed`: csak a címkéket és helyet támogató erőforrástípusok kiértékelése

Például az erőforrás `Microsoft.Network/routeTables` támogatja a címkéket és a helyet, és mindkét módban kiértékelésre kerül. Az erőforrás azonban `Microsoft.Network/routeTables/routes` nem címkézhető, és nincs kiértékelve a `Indexed` módban.

Javasoljuk, hogy a legtöbb esetben állítsa be a **módot** `all` . A portálon keresztül létrehozott összes házirend-definíció a `all` módot használja. Ha a PowerShellt vagy az Azure CLI-t használja, manuálisan is megadhatja a **Mode** paramétert. Ha a házirend-definíció nem tartalmaz **Mode** értéket, az alapértelmezett értéke `all` Azure PowerShell és az `null` Azure CLI-ben. A `null` `indexed` visszafelé való kompatibilitás támogatásához egy mód ugyanaz, mint a használatával.

`indexed` a címkéket vagy helyszíneket kényszerítő házirendek létrehozásakor kell használni. Habár nem kötelező, megakadályozza, hogy a címkék és a hely nem támogatja az olyan erőforrásokat, amelyek nem felelnek meg a megfelelőségi eredményeknek. A kivétel az **erőforráscsoportok** és az **előfizetések**. Az erőforráscsoport vagy előfizetés helyét vagy címkéit kényszerítő szabályzat-definíciók **módot** kell beállítani a (z `all` ) és a (z) és a (z `Microsoft.Resources/subscriptions/resourceGroups` `Microsoft.Resources/subscriptions` ) típusra. Példaként tekintse meg a [minta: címkék – minta #1](../samples/pattern-tags.md). A címkéket támogató erőforrások listáját lásd: az Azure- [erőforrások támogatásának címkézése](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes-preview"></a><a name="resource-provider-modes"></a>Erőforrás-szolgáltatói módok (előzetes verzió)

Az előzetes verzióban jelenleg a következő erőforrás-szolgáltatói módok támogatottak:

- `Microsoft.ContainerService.Data` a belépésvezérlés szabályainak kezeléséhez az [Azure Kubernetes szolgáltatásban](../../../aks/intro-kubernetes.md). Az ezt az erőforrás-szolgáltatói módot használó definícióknak a [EnforceRegoPolicy](./effects.md#enforceregopolicy) effektust **kell** használniuk. Ez a mód _elavult_.
- `Microsoft.Kubernetes.Data` Az Azure-beli Kubernetes-fürtök kezeléséhez. Az erőforrás-szolgáltatói üzemmódot használó definíciók a következő hatásokat használják: _naplózás_, _Megtagadás_és _Letiltva_. A [EnforceOPAConstraint](./effects.md#enforceopaconstraint) hatás használatának _elavultnak_kell lennie.
- `Microsoft.KeyVault.Data` a [Azure Key Vault](../../../key-vault/general/overview.md)tárolók és tanúsítványok kezeléséhez.

> [!NOTE]
> Az erőforrás-szolgáltatói módok csak a beépített szabályzat-definíciókat támogatják, és az előzetes verzióban nem támogatottak a kezdeményezések.

## <a name="metadata"></a>Metaadatok

A választható `metadata` tulajdonság a házirend-definícióval kapcsolatos adatokat tárolja. Az ügyfelek a szervezete számára hasznos tulajdonságokat és értékeket adhatnak meg `metadata` . Vannak azonban olyan _általános_ tulajdonságok, amelyeket a Azure Policy és a beépített modulok használnak.

### <a name="common-metadata-properties"></a>Gyakori metaadatok tulajdonságai

- `version` (karakterlánc): nyomon követi a szabályzat-definíció tartalmának verziószámát.
- `category` (karakterlánc): meghatározza, hogy a házirend-definíció milyen kategóriában jelenjen meg Azure Portal.
- `preview` (Boolean): igaz vagy hamis jelző, ha a házirend-definíció _előzetes_verzió.
- `deprecated` (Boolean): igaz vagy hamis jelző, ha a házirend-definíció _elavultként_van megjelölve.

> [!NOTE]
> A Azure Policy szolgáltatás a, a `version` `preview` és a tulajdonságot használja a `deprecated` beépített szabályzat-definícióra vagy kezdeményezésre és állapotra való váltáshoz. A formátuma `version` : `{Major}.{Minor}.{Patch}` . Bizonyos állapotokat (például az _elavult_ vagy az _előnézet_) a `version` tulajdonsághoz vagy egy másik tulajdonsághoz ( **Boolean**) kell hozzáfűzni. További információ a Azure Policy-verziókról: [beépített verziószámozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Paraméterek

A paraméterek segítségével egyszerűsítheti a házirendek kezelését a házirend-definíciók számának csökkentésével. Gondoljon olyan paraméterekre, mint például az űrlap mezői – `name` , `address` , `city` , `state` . Ezek a paraméterek mindig azonosak maradnak, de az értékek az űrlap kitöltése alapján változnak.
A paraméterek ugyanúgy működnek, mint a házirendek létrehozásakor. A házirend-definícióban szereplő paraméterekkel együtt más értékekkel is felhasználhatja a szabályzatot különböző forgatókönyvekhez.

> [!NOTE]
> A paraméterek hozzáadhatók meglévő és hozzárendelt definícióhoz is. Az új paraméternek tartalmaznia kell a **defaultValue** tulajdonságot. Ez megakadályozza, hogy a házirend vagy kezdeményezés meglévő hozzárendelései érvénytelenek legyenek.

### <a name="parameter-properties"></a>Paraméter tulajdonságai

A paraméter a következő tulajdonságokkal rendelkezik, amelyek a szabályzat-definícióban használatosak:

- `name`: A paraméter neve. A házirend- `parameters` szabályon belüli központi telepítési függvény használja. További információ: [paraméter értékének használata](#using-a-parameter-value).
- `type`: Meghatározza, hogy a paraméter **karakterlánc**, **tömb**, **objektum**, **logikai**, **egész**, **lebegőpontos**vagy **datetime**.
- `metadata`: Meghatározza a Azure Portal által elsődlegesen használt altulajdonságokat a felhasználóbarát információk megjelenítéséhez:
  - `description`: Annak magyarázata, hogy mit használ a paraméter. A használható az elfogadható értékek példáinak megadására.
  - `displayName`: A (z) paraméterben a portálon megjelenő rövid név.
  - `strongType`: (Nem kötelező) a szabályzat definíciójának a portálon való hozzárendeléséhez használatos. Környezetfüggő listát biztosít. További információ: [strongType](#strongtype).
  - `assignPermissions`: (Nem kötelező) állítsa _igaz_ értékre, hogy Azure Portal hozzon létre szerepkör-hozzárendeléseket a házirend-hozzárendelés során. Ez a tulajdonság akkor hasznos, ha az engedélyeket a hozzárendelési hatókörön kívül szeretné hozzárendelni. Szerepkör-definícióban egy szerepkör-hozzárendelés van a házirendben (vagy a szerepkör-definícióban a kezdeményezés összes házirendje esetében). A paraméter értékének érvényes erőforrásnak vagy hatókörnek kell lennie.
- `defaultValue`: (Nem kötelező) megadja a paraméter értékét egy hozzárendelésben, ha nincs megadva érték.
  Egy meglévő, hozzárendelt szabályzat-definíció frissítésekor szükséges.
- `allowedValues`: (Nem kötelező) az értékek egy tömbjét adja meg, amelyet a paraméter elfogad a hozzárendelés során.

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

A házirend-szabályban a paramétereket a következő `parameters` függvény szintaxisával hivatkozhat:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ez a példa a **allowedLocations** paraméterre hivatkozik, amely a [paraméter tulajdonságainál](#parameter-properties)látható.

### <a name="strongtype"></a>strongType

A `metadata` tulajdonságon belül a **strongType** használatával több választási lehetőséget is megadhat a Azure Portalon belül. a **strongType** lehet egy támogatott _erőforrástípus_ vagy egy megengedett érték. Annak megállapításához, hogy az _erőforrástípus_ érvényes-e a **strongType**, használja a [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider). A **strongType** _erőforrástípus_ formátuma `<Resource Provider>/<Resource Type>` . Például: `Microsoft.Network/virtualNetworks/subnets`.

Bizonyos, a **Get-AzResourceProvider** által nem visszaadott _erőforrástípusok_ támogatottak. Ezek a típusok a következők:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

A **strongType** által engedélyezett nem _erőforrástípus_ -érték a következő:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Definíció helye

Kezdeményezés vagy szabályzat létrehozásakor meg kell adnia a definíció helyét. A definíció helyének felügyeleti csoportnak vagy előfizetésnek kell lennie. Ez a hely határozza meg azt a hatókört, amelyhez a kezdeményezést vagy házirendet hozzá lehet rendelni. Az erőforrásoknak a definíció helyének hierarchiájában kell közvetlen tagoknak vagy gyermekeknek lenniük a hozzárendelés céljára.

Ha a definíció helye:

- Az előfizetésen belül csak **az előfizetés** erőforrásait lehet hozzárendelni.
- **Felügyeleti csoport** – csak a gyermek-felügyeleti csoportokon belüli erőforrások és a gyermek előfizetések rendelhetők hozzá a szabályzathoz. Ha azt tervezi, hogy a házirend-definíciót több előfizetésre is alkalmazza, akkor a helynek az előfizetést tartalmazó felügyeleti csoportnak kell lennie.

## <a name="policy-rule"></a>Házirend-szabály

A **házirend-szabály** az **IF** és a blokkból áll. Az **IF** blokkban meg kell adnia egy vagy több olyan feltételt, amely megadja, hogy a rendszer mikor kényszerítse ki a házirendet. Ezekhez a feltételekhez logikai operátorokat alkalmazhat, így pontosan meghatározhatja a házirend forgatókönyvét.

Az **Ezután** blokkban definiálhatja azt a hatást, amely az **IF** feltételek teljesülése esetén történik.

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

A **nem** szintaxis invertálja a feltétel eredményét. A **allOf** szintaxisa (a logikai **és** a művelethez hasonlóan) minden feltételnek igaznak kell lennie. A **anyOf** szintaxisa (a logikai **vagy** a művelethez hasonlóan) egy vagy több feltétel teljesülése szükséges.

A logikai operátorok beágyazására is lehetőség van. A következő példa egy **nem** műveletet mutat be, amely egy **allOf** -műveletbe van beágyazva.

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
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

**Kevesebb**, **lessOrEquals**, **nagyobb**és **greaterOrEquals**esetén, ha a tulajdonság típusa nem egyezik a feltétel típusával, a rendszer hibát jelez. A karakterlánc-összehasonlítások használata a használatával történik `InvariantCultureIgnoreCase` .

A **hasonló** és **notLike** feltételek használatakor helyettesítő karaktert kell megadni `*` az értékben.
Az érték legfeljebb egy helyettesítő karakterből állhat `*` .

A **egyezési** és **notMatch** feltételek használatakor az adott `#` számjegyre, `?` betűre, `.` bármilyen karakterre és bármely más karakterre illeszkedik, amely megfelel a tényleges karakternek. Ha a **egyezés** és a **notMatch** is megkülönbözteti a kis-és nagybetűket, a _stringValue_ kiértékelésére szolgáló összes egyéb feltétel nem tesz különbséget Kis-és nagybetűket megkülönböztető alternatívák a **matchInsensitively** és a **notMatchInsensitively**szolgáltatásban érhetők el.

Az ** \[ \* \] alias** tömb mezőjének értékeként a tömb minden elemét külön kell kiértékelni a logikai **és** az elemek között. További információ: [az \[ \* \] alias kiértékelése](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Mezők

A feltételek mezők használatával jönnek létre. Egy mező megfelel az erőforrás-kérelem hasznos adatainak, és leírja az erőforrás állapotát.

A következő mezők támogatottak:

- `name`
- `fullName`
  - Az erőforrás teljes nevét adja vissza. Az erőforrás teljes neve az erőforrás neve előtagértéke (például "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - **Globálisan** használhatja az olyan erőforrásokat, amelyek a helytől függetlenek.
- `identity.type`
  - Az erőforráson engedélyezett [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) típusát adja vissza.
- `tags`
- `tags['<tagName>']`
  - Ez a zárójel-szintaxis támogatja a írásjeleket, például kötőjelet, pontot vagy szóközt.
  - Ahol a a **\<tagName\>** címke neve, amely ellenőrzi a feltételt.
  - Példák: `tags['Acct.CostCenter']` az **acct. CostCenter** a címke neve.
- `tags['''<tagName>''']`
  - Ez a zárójel-szintaxis támogatja az aposztrófokat tartalmazó címkéket dupla aposztrófokkal.
  - Ahol a **" \<tagName\> "** annak a címkének a neve, amely a feltételt érvényesíti.
  - Példa: `tags['''My.Apostrophe.Tag''']` ahol a **"My. aposztróf. tag"** a címke neve.
- tulajdonság-aliasok – lista esetén lásd: [aliasok](#aliases).

> [!NOTE]
> `tags.<tagName>`a, `tags[tagName]` , és `tags[tag.with.dots]` továbbra is elfogadható módon deklarálhatja a címkék mezőt. Az előnyben részesített kifejezések azonban a fentiekben láthatók.

#### <a name="use-tags-with-parameters"></a>Címkék használata paraméterekkel

Egy paraméter értéke adható át egy címke mezőnek. Ha egy paramétert egy címke mezőre továbbít, a házirend-hozzárendelés során növeli a házirend-definíció rugalmasságát.

A következő példában a `concat` **TagName** paraméter értékének megadásához a címkék mezőhöz tartozó keresőmezőt kell létrehozni. Ha ez a címke nem létezik, a **módosítás** hatására a rendszer hozzáadja a címkét a naplózott erőforrások szülő erőforráscsoporthoz tartozó megnevezett címke értékével a `resourcegroup()` keresési függvénnyel.

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

A feltételek az **érték**használatával is létrehozhatók. az **érték** a [paraméterekkel](#parameters), a [támogatott sablon-funkciókkal](#policy-functions)vagy a literálokkal kapcsolatos feltételeket ellenőrzi. az **érték** a támogatott [feltételekkel](#conditions)párosítva van.

> [!WARNING]
> Ha egy _sablon függvény_ eredménye hibát jelez, a szabályzat kiértékelése sikertelen lesz. A sikertelen értékelés implicit **Megtagadás**. További információ: a [sablon meghibásodásának elkerülése](#avoiding-template-failures). A [enforcementMode](./assignment-structure.md#enforcement-mode) használatával **DoNotEnforce** megakadályozhatja az új vagy frissített erőforrások sikertelen értékelésének hatását az új házirend-definíció tesztelése és érvényesítése során.

#### <a name="value-examples"></a>Példák az értékekre

Ez a házirend-szabály például az **érték** használatával hasonlítja össze a függvény eredményét `resourceGroup()` és a visszaadott **Name** tulajdonságot a **hasonló** feltétellel `*netrg` . A szabály minden olyan erőforrást megtagad, `Microsoft.Network/*` amely **nem egy** olyan erőforráscsoport, amelynek a neve véget ér `*netrg` .

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

Ez a házirend-szabály például az **érték** használatával ellenőrizze, hogy a több beágyazott függvény eredménye **egyenlő** -e `true` . A szabály minden olyan erőforrást megtagad, amely nem rendelkezik legalább három címkével.

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

A fenti példában az [alsztring ()](../../../azure-resource-manager/templates/template-functions-string.md#substring) érték a **név** első három karakterének **ABC**-re való összevetését használja. Ha a **név** rövidebb, mint három karakter, a `substring()` függvény hibát eredményez. Ez a hiba azt eredményezi, hogy a házirend **megtagadási** hatást vált ki.

Ehelyett a [IF ()](../../../azure-resource-manager/templates/template-functions-logical.md#if) függvény használatával ellenőrizze, hogy az első három **karakter egyenlő-e** az **ABC** -vel anélkül, hogy a **név** három karakternél rövidebb legyen, ami hibát okozhat:

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

A módosított szabályzattal rendelkező szabály a `if()` **név** hosszát ellenőrzi, mielőtt egy olyan értéket próbál meg elérni, `substring()` amely kevesebb mint három karakterből áll. Ha a **név** túl rövid, a "nem az ABC-től kezdődően" értéket adja vissza, és az **ABC**-hez képest. Az **ABC** -vel nem kezdődő rövid névvel rendelkező erőforrás továbbra is meghiúsul a házirend-szabályban, de az értékelés során már nem okoz hibát.

### <a name="count"></a>Darabszám

Azok a feltételek, amelyek megszámolják, hogy az erőforrás-adattartalomban lévő tömb tagjai közül hányan felelnek meg egy feltétel kifejezésének a **Count** kifejezés használatával. A gyakori forgatókönyvek azt ellenőrzik, hogy a tömb tagjai megfelelnek-e a feltételnek: "legalább az egyike", "a" minden "vagy" nincs ". a **Count** minden [ \[ \* \] alias](#understanding-the--alias) -tömböt kiértékel egy feltétel kifejezéséhez, és összegzi a _valódi_ eredményeket, amelyeket aztán a kifejezés operátorhoz hasonlít. A **Count** kifejezések három alkalommal is hozzáadhatók egyetlen **' policyrule osztály** -definícióhoz.

A **Count** kifejezés szerkezete:

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

A **Count**a következő tulajdonságokat használja:

- **Count. Field** (kötelező): a tömb elérési útját tartalmazza, és tömb aliasnak kell lennie. Ha a tömb hiányzik, a kifejezés kiértékelése _hamis_ értékre történik a feltétel kifejezésének mérlegelése nélkül.
- **darabszám. where** (nem kötelező): a feltétel kifejezése, hogy egyenként kiértékelje az egyes [ \[ \* \] aliasok](#understanding-the--alias) tömbbeli tagjainak száma **. mező értékét**. Ha ez a tulajdonság nincs megadva, a "Field" elérési úttal rendelkező összes tömb-tag _igaz_értékre van kiértékelve. Ezen a tulajdonságon belül bármely [feltétel](../concepts/definition-structure.md#conditions) használható.
  A tulajdonságon belül a [logikai operátorok](#logical-operators) összetett értékelési követelmények létrehozására használhatók.
- **\<condition\>** (kötelező): a rendszer összehasonlítja az értéket a darabszámban teljesített elemek számával **. where** feltétel kifejezése. Numerikus [feltételt](../concepts/definition-structure.md#conditions) kell használni.

#### <a name="count-examples"></a>Példák száma

1. példa: Ellenőrizze, hogy egy tömb üres-e

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

2. példa: csak egy tömb tag keresése a feltétel kifejezésének teljesítéséhez

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

3. példa: legalább egy tömbhöz tartozó tag keresése a feltétel kifejezésének teljesítéséhez

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

4. példa: annak megjelölése, hogy az összes objektum összes tagja megfelel-e a feltétel kifejezésének

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

5. példa: Győződjön meg arról, hogy legalább egy tömb tagja megegyezik a feltétel kifejezésben szereplő több tulajdonsággal

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

Azure Policy a következő típusú hatásokat támogatja:

- **Hozzáfűzés**: hozzáadja a mezők meghatározott készletét a kéréshez.
- **Naplózás**: figyelmeztetési esemény generálása a tevékenység naplójában, de a kérelem nem sikerül
- **AuditIfNotExists**: figyelmeztetési eseményt állít elő a tevékenység naplójában, ha nem létezik kapcsolódó erőforrás
- **Megtagadás**: eseményt hoz létre a tevékenység naplójában, és sikertelenül kéri a kérést.
- **DeployIfNotExists**: egy kapcsolódó erőforrás üzembe helyezése, ha még nem létezik
- **Letiltva**: nem értékeli ki a házirend-szabálynak való megfeleléshez szükséges erőforrásokat
- **EnforceOPAConstraint** (előzetes verzió): az Azure-beli önfelügyelt Kubernetes-fürtökhöz az Open Policy Agent beléptetési vezérlőt konfigurálja az Azure-ban (előzetes verzió)
- **EnforceRegoPolicy** (előzetes verzió): az Azure Kubernetes Service-ben a (z)
- **Módosítás**: a definiált címkék hozzáadását, frissítését vagy eltávolítását egy erőforrásból

Az egyes effektusok, a kiértékelési sorrend, a tulajdonságok és a példák részletes ismertetését lásd: a [Azure Policy effektusok ismertetése](effects.md).

### <a name="policy-functions"></a>Házirend-függvények

Az összes [Resource Manager-sablon funkció](../../../azure-resource-manager/templates/template-functions.md) egy házirend-szabályon belül használható, az alábbi függvények és a felhasználó által definiált függvények kivételével:

- copyIndex ()
- üzembe helyezés ()
- listáját
- newGuid()
- pickZones()
- szolgáltatók ()
- hivatkozás ()
- resourceId ()
- változók ()

> [!NOTE]
> Ezek a függvények továbbra is elérhetők a `details.deployment.properties.template` sablon központi telepítésének részeként egy **deployIfNotExists** házirend-definícióban.

A következő függvény használható egy házirend-szabályban, de eltér a használattól egy Azure Resource Manager sablonban (ARM-sablon):

- `utcNow()` – Az ARM-sablonoktól eltérően ez a tulajdonság a _defaultValue_-n kívül is használható.
  - Egy olyan karakterláncot ad vissza, amely az univerzális ISO 8601 DateTime formátumban van beállítva az aktuális dátumra és időpontra vonatkozóan (éééé-hh-NNTóó: PP: SS. fffffffZ).

A következő függvények csak a házirend-szabályokban érhetők el:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **datetime**: [Required] String-String in the Universal ISO 8601 datetime Format éééé-hh-NNTóó: PP: SS. fffffffZ '
  - **numberOfDaysToAdd**: [kötelező] egész szám – hozzáadandó napok száma
- `field(fieldName)`
  - **Mezőnév**: [kötelező] karakterlánc – a beolvasandó [mező](#fields) neve
  - Annak az erőforrásnak az értékét adja vissza, amelyet az IF feltétel kiértékel.
  - `field` elsődlegesen a **AuditIfNotExists** és a **DeployIfNotExists** használja a kiértékelt erőforráson található hivatkozási mezőkre. Erre a használatra példa látható az [DeployIfNotExists példában](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - A szabályzat kiértékelését kiváltó kérelem API-verzióját adja vissza (például: `2019-09-01` ).
    Ez az érték az a API-verzió, amelyet a PUT/PATCH kérelemben használt az erőforrás-létrehozási/frissítési kérelmekre vonatkozó értékelésekhez. A meglévő erőforrásokon a megfelelőségi értékelés során mindig a legújabb API-verziót használja a rendszer.
  
#### <a name="policy-function-example"></a>Példa a házirend-függvényre

Ez a házirend-szabály például az erőforrás-függvénnyel kéri le `resourceGroup` a **Name (név** ) tulajdonságot, `concat` amely a tömb és objektum függvénnyel együtt egy olyan `like` feltételt hoz létre, amely kikényszeríti az erőforrás nevét, hogy az erőforráscsoport nevével kezdődjön.

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

Tulajdonság-Aliasok használatával férhet hozzá az erőforrástípus adott tulajdonságaihoz. Az aliasok lehetővé teszik annak korlátozását, hogy az adott erőforrás egy tulajdonsága milyen értékeket vagy feltételeket engedélyezzen. Az egyes aliasok egy adott erőforrástípus különböző API-verzióinak elérési útjaira mutatnak. A házirend kiértékelése során a házirend-kezelő beolvassa az adott API-verzióhoz tartozó tulajdonság elérési útját.

Az aliasok listája mindig növekszik. A Azure Policy által jelenleg támogatott aliasok megkereséséhez használja az alábbi módszerek egyikét:

- Azure Policy-bővítmény a Visual Studio Code-hoz (ajánlott)

  A [Visual Studio Code](../how-to/extension-for-vscode.md) -hoz készült Azure Policy-bővítmény használatával megtekintheti és derítheti fel az erőforrás-tulajdonságok aliasait.

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Képernyőkép a Visual Studio Code-hoz készült Azure Policy-bővítményről, amely egy tulajdonsággal jeleníti meg az aliasok nevét." border="false":::

- Azure Resource Graph

  Az `project` operátor használatával jelenítheti meg az erőforrások **aliasát** .

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

  > [!NOTE]
  > A [módosítás](./effects.md#modify) hatással használható aliasok megkereséséhez használja a következő parancsot Azure PowerShell **4.6.0** vagy magasabb értékben:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>A [*] alias ismertetése

A rendelkezésre álló aliasok közül több olyan verzióval rendelkezik, amely "normál" néven jelenik meg, és egy másik, amely hozzá van **\[\*\]** csatolva. Például:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

A "normál" alias a mezőt egyetlen értékként jelöli. Ez a mező az összehasonlítási forgatókönyvek pontos egyeztetésére szolgál, ha az értékek teljes halmazának pontosan meghatározottnak kell lennie, nem több és nem kevesebb.

Az **\[\*\]** alias lehetővé teszi, hogy összehasonlítsa az egyes elemek értékét a tömbben és az egyes elemek adott tulajdonságaiban. Ez a módszer lehetővé teszi, hogy összehasonlítsa a "Ha nincs", "," vagy "Ha az összes" forgatókönyvhöz tartozó elem tulajdonságait. Összetettebb forgatókönyvek esetén használja a [Count](#count) feltétel kifejezést. A ** \[ \* ipRules \] **használatával egy példa azt ellenőrzi, hogy minden _művelet_ _megtagadva_van-e, de nem kell aggódnia, hogy hány szabály létezik, vagy hogy mi az IP- _érték_ .
Ez a minta-szabály a **ipRules \[ \* \] . Value** összes egyezését ellenőrzi a **10.0.4.1** , és csak akkor alkalmazza a **effectType** , ha nem talál legalább egy egyezést:

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

További információ: [[ \* ] alias kiértékelése](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [kezdeményezési definíció szerkezetét](./initiative-definition-structure.md)
- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
