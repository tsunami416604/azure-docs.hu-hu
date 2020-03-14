---
title: A házirend-definíciós struktúra részletei
description: Leírja, hogyan használhatók a szabályzat-definíciók a szervezeten belüli Azure-erőforrásokra vonatkozó konvenciók létrehozásához.
ms.date: 02/26/2020
ms.topic: conceptual
ms.openlocfilehash: 95625894d0eb603ae9a37c96c91d01f3720346b1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280728"
---
# <a name="azure-policy-definition-structure"></a>Azure szabályzatdefiníciók struktúrája

Azure Policy az erőforrásokra vonatkozó konvenciókat hoz létre. A szabályzat-definíciók írják le az erőforrás-megfelelőségi [feltételeket](#conditions) , valamint azt, hogy egy adott feltétel teljesül-e. A feltétel egy erőforrás-tulajdonság [mezőjét](#fields) hasonlítja össze egy szükséges értékkel. Az erőforrás-tulajdonságok mezői [aliasok](#aliases)használatával érhetők el. Az erőforrás-tulajdonság mező egy egyértékű mező vagy több értékből álló [tömb](#understanding-the--alias) . A feltétel kiértékelése eltér a tömböknél.
További információ a [feltételekről](#conditions).

Egyezmények definiálásával is csökkenthetők a költségek, és további könnyen kezelheti az erőforrásokat. Megadhatja például, hogy csak bizonyos típusú virtuális gépek használata engedélyezett. Másik lehetőségként megkövetelheti, hogy az összes erőforrásnak rendelkeznie kell egy adott címkét. Összes gyermekerőforrás örökölt házirendek. Ha egy szabályzatot alkalmazott egy erőforráscsoportba, alkalmazható az adott erőforráscsoportba tartozó összes erőforrást.

A szabályzat-definíciós séma itt található: [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

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

## <a name="mode"></a>Mód

A **mód** attól függően van konfigurálva, hogy a házirend Azure Resource Manager tulajdonságot vagy erőforrás-szolgáltatói tulajdonságot céloz meg.

### <a name="resource-manager-modes"></a>Resource Manager-módok

A **mód** határozza meg, hogy mely erőforrástípusok lesznek kiértékelve egy házirendhez. A támogatott módok a következők:

- `all`: erőforráscsoportok és minden erőforrástípus kiértékelése
- `indexed`: csak a címkéket és helyet támogató erőforrástípusok kiértékelése

Például az erőforrás-`Microsoft.Network/routeTables` támogatja a címkéket és a helyet, és mindkét módban kiértékelésre kerül. A nem címkézhető erőforrás-`Microsoft.Network/routeTables/routes` azonban `Indexed` módban nem lehet kiértékelni.

Javasoljuk, hogy a legtöbb esetben állítsa be a **módot** `all`. A portálon keresztül létrehozott összes házirend-definíció a `all` módot használja. Ha a PowerShellt vagy az Azure CLI-t használja, manuálisan is megadhatja a **Mode** paramétert. Ha a házirend-definíció nem tartalmaz **Mode** értéket, a rendszer alapértelmezés szerint `all` Azure PowerShell és az Azure CLI-ben `null`. A visszafelé kompatibilitás támogatásához a `null` mód megegyezik a `indexed` használatával.

a címkéket vagy helyszíneket kikényszerítő házirendek létrehozásakor `indexed`t kell használni. Habár nem kötelező, megakadályozza, hogy a címkék és a hely nem támogatja az olyan erőforrásokat, amelyek nem felelnek meg a megfelelőségi eredményeknek. A kivétel az **erőforráscsoportok**. Az erőforráscsoportok helyét vagy címkéit kényszerítő házirendeknek úgy kell beállítaniuk a **módot** , hogy `all` és kifejezetten a `Microsoft.Resources/subscriptions/resourceGroups` típust célozzák meg. Példaként tekintse meg az [erőforráscsoport-címkék betartatása](../samples/enforce-tag-rg.md)című témakört. A címkéket támogató erőforrások listáját lásd: az Azure- [erőforrások támogatásának címkézése](../../../azure-resource-manager/management/tag-support.md).

### <a name="a-nameresource-provider-modes-resource-provider-modes-preview"></a><a name="resource-provider-modes" />erőforrás-szolgáltatói módok (előzetes verzió)

Az előzetes verzióban jelenleg a következő erőforrás-szolgáltatói módok támogatottak:

- `Microsoft.ContainerService.Data` a belépésvezérlés szabályainak kezeléséhez az [Azure Kubernetes szolgáltatásban](../../../aks/intro-kubernetes.md). Az ezt az erőforrás-szolgáltatói módot használó házirendeknek a [EnforceRegoPolicy](./effects.md#enforceregopolicy) hatást **kell** használniuk.
- `Microsoft.Kubernetes.Data` az Azure-beli önfelügyelt Kubernetes-fürtök kezeléséhez.
  Az ezt az erőforrás-szolgáltatói módot használó házirendeknek a [EnforceOPAConstraint](./effects.md#enforceopaconstraint) hatást **kell** használniuk.
- `Microsoft.KeyVault.Data` [Azure Key Vault](../../../key-vault/key-vault-overview.md)-tárolók és-tanúsítványok kezeléséhez.

> [!NOTE]
> Az erőforrás-szolgáltatói módok csak a beépített szabályzat-definíciókat támogatják, és az előzetes verzióban nem támogatottak a kezdeményezések.

## <a name="parameters"></a>Paraméterek

Paraméterek segítségével leegyszerűsítik a szabályzatok kezelését szabályzatdefiníciók számának csökkentésével. Gondoljon olyan paraméterekre, mint például az űrlap mezői – `name`, `address`, `city`, `state`. Ezek a paraméterek változnak, azonban azok értékeit módosíthatja az űrlap egyes kitöltése alapján.
Paraméterek ugyanúgy működnek, szabályzatok készítése során. Paraméterekkel együtt egy szabályzat-definícióban, újból felhasználhatja az adott házirendnek a különböző helyzetekhez különböző értékek alapján.

> [!NOTE]
> A paraméterek hozzáadhatók meglévő és hozzárendelt definícióhoz is. Az új paraméternek tartalmaznia kell a **defaultValue** tulajdonságot. Ez megakadályozza, hogy meglévő hozzárendelését a szabályzatot vagy kezdeményezést közvetve érvénytelen kerül sor.

### <a name="parameter-properties"></a>Paraméter tulajdonságai

A paraméter a következő tulajdonságokkal rendelkezik, amelyek a szabályzat-definícióban használatosak:

- **Name (név**): a paraméter neve. A házirend-szabályon belül a `parameters` Deployment függvény használja. További információ: [paraméter értékének használata](#using-a-parameter-value).
- `type`: meghatározza, hogy a paraméter **karakterlánc**, **tömb**, **objektum**, **logikai**, **egész**, **lebegőpontos**vagy **datetime**.
- `metadata`: a Azure Portal által elsődlegesen használt altulajdonságok meghatározása a felhasználóbarát információk megjelenítéséhez:
  - `description`: a paraméter használatának magyarázata. A használható az elfogadható értékek példáinak megadására.
  - `displayName`: a (z) paraméterhez tartozó portálon megjelenő rövid név.
  - `version`: (nem kötelező) a szabályzat-definíció tartalmának részletes adatait követi nyomon.

    > [!NOTE]
    > A Azure Policy szolgáltatás `version`, `preview`és `deprecated` tulajdonságokat használ a beépített szabályzatok definíciójának vagy kezdeményezésének és állapotának továbbítására. `version` formátuma: `{Major}.{Minor}.{Patch}`. Bizonyos állapotokat, például az _elavult_ vagy az _előzetes_verziót, a rendszer a `version` tulajdonsághoz vagy egy másik tulajdonsághoz ( **Boolean**) fűzi hozzá.

  - `category`: (nem kötelező) meghatározza, hogy a házirend-definíció milyen kategóriába tartozik Azure Portalban.
  - `strongType`: (nem kötelező) a házirend-definíciónak a portálon való hozzárendeléséhez használatos. Környezetfüggő listát biztosít. További információ: [strongType](#strongtype).
  - `assignPermissions`: (nem kötelező) állítsa _igaz_ értékre, hogy Azure Portal szerepkör-hozzárendeléseket hozzon létre a házirend-hozzárendelés során. Ez a tulajdonság akkor hasznos, ha az engedélyeket a hozzárendelési hatókörön kívül szeretné hozzárendelni. Szerepkör-definícióban egy szerepkör-hozzárendelés van a házirendben (vagy a szerepkör-definícióban a kezdeményezés összes házirendje esetében). A paraméter értékének érvényes erőforrásnak vagy hatókörnek kell lennie.
- `defaultValue`: (nem kötelező) megadja a paraméter értékét egy hozzárendelésben, ha nincs megadva érték.
  Egy meglévő, hozzárendelt szabályzat-definíció frissítésekor szükséges.
- `allowedValues`: (nem kötelező) az értékek tömbjét adja meg, amelyet a paraméter elfogad a hozzárendelés során.

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

A házirend szabályban a paramétereket a következő `parameters` függvény szintaxisával hivatkozhat:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

Ez a példa a **allowedLocations** paraméterre hivatkozik, amely a [paraméter tulajdonságainál](#parameter-properties)látható.

### <a name="strongtype"></a>strongType

A `metadata` tulajdonságon belül a **strongType** használatával több választási lehetőséget is megadhat a Azure Portalon belül. a **strongType** lehet egy támogatott _erőforrástípus_ vagy egy megengedett érték. Annak megállapításához, hogy az _erőforrástípus_ érvényes-e a **strongType**, használja a [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Bizonyos, a **Get-AzResourceProvider** által nem visszaadott _erőforrástípusok_ támogatottak. Ezek a következők:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

A **strongType** által engedélyezett nem _erőforrástípus_ -érték a következő:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Definíció helye

Egy kezdeményezést vagy létrehozásakor meg kell határozni a definíció helye. A definíció helye egy felügyeleti csoportot vagy egy előfizetésben kell lennie. Ezen a helyen, amelyhez a kezdeményezést vagy rendelhetők a hatókör határozza meg. Erőforrások közvetlen tagjai vagy a gyermekek a hierarchiában a definíció helye, hogy a szabályzat-hozzárendelés kell lennie.

Ha a definíció helye v:

- Az előfizetésen belül csak **az előfizetés** erőforrásait lehet hozzárendelni.
- **Felügyeleti csoport** – csak a gyermek-felügyeleti csoportokon belüli erőforrások és a gyermek előfizetések rendelhetők hozzá a szabályzathoz. Ha azt tervezi, a szabályzat-definíció alkalmazni a különböző előfizetések, a hely ezen előfizetések tartalmazó felügyeleti csoportot kell lennie.

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

A **DisplayName** és a **Leírás** használatával azonosíthatja a házirend-definíciót, és megadhatja a környezetét a használathoz. a **DisplayName** maximális hossza _128_ karakter, és a **Leírás** legfeljebb _512_ karakter hosszúságú lehet.

> [!NOTE]
> A házirend-definíció létrehozása vagy frissítése során a JSON-on kívüli tulajdonságok definiálják az **azonosítót**, a **típust**és a **nevet** , és nem szükségesek a JSON-fájlban. Az SDK-n keresztüli szabályzat-definíció beolvasása az **azonosító**, a **típus**és a **név** tulajdonságokat adja vissza a JSON részeként, de mindegyik írásvédett információ a házirend-definícióhoz kapcsolódik.

## <a name="policy-rule"></a>Szabályzatbeli szabály

A **házirend-szabály** az **IF** és a blokkból áll. Az **IF** blokkban meg kell adnia egy vagy több olyan feltételt, amely megadja, hogy a rendszer mikor kényszerítse ki a házirendet. Ezek a feltételek, pontosan meghatározni a forgatókönyv egy házirend logikai operátorokat alkalmazhat.

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

Támogatott logikai operátorok a következők:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

A **nem** szintaxis invertálja a feltétel eredményét. A **allOf** szintaxisa (a logikai **és** a művelethez hasonlóan) minden feltételnek igaznak kell lennie. A **anyOf** szintaxisa (a logikai **vagy** a művelethez hasonlóan) egy vagy több feltétel teljesülése szükséges.

Logikai operátorok ágyazhatja be. A következő példa egy **nem** műveletet mutat be, amely egy **allOf** -műveletbe van beágyazva.

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
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

A **hasonló** és **notLike** feltételek használatakor helyettesítő karaktert kell megadni `*` az értékben.
Az érték legfeljebb egy helyettesítő karakterből állhat `*`.

A **egyezési** és **notMatch** feltételek használatakor `#` a számjegyek egyeztetéséhez, `?` egy betűhöz, `.` a karaktereknek való megfeleléshez, illetve bármely más karakterhez, amely megfelel a tényleges karakternek. Míg a **Match** és a **notMatch** megkülönbözteti a kis-és nagybetűket, a _stringValue_ kiértékelésére szolgáló összes egyéb feltétel kis-és nagybetűket nem jelent. Kis-és nagybetűket megkülönböztető alternatívák a **matchInsensitively** és a **notMatchInsensitively**szolgáltatásban érhetők el.

Egy **\[\*\] alias** tömb mezőjének értékeként a tömb minden elemét egyedileg értékeli ki a rendszer a logikai **és** az elemek között. További információ: [a \[\*\] alias kiértékelése](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

### <a name="fields"></a>Mezők

Feltételek alkotta mezőkkel. Egy mező megegyezik a tulajdonság az erőforrás-kérések forgalma, és az erőforrás állapotának leírása.

A következő mezők támogatottak:

- `name`
- `fullName`
  - Az erőforrás teljes nevét adja vissza. Erőforrás teljes név bármelyik szülő erőforrás nevét (például "myServer/myDatabase") által kiegészített az erőforrás nevét.
- `kind`
- `type`
- `location`
  - **Globálisan** használhatja az olyan erőforrásokat, amelyek a helytől függetlenek.
- `identity.type`
  - Az erőforráson engedélyezett [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) típusát adja vissza.
- `tags`
- `tags['<tagName>']`
  - Ez a zárójel-szintaxis támogatja a írásjeleket, például kötőjelet, pontot vagy szóközt.
  - Ahol a **\<tagName\>** annak a címkének a neve, amely a feltételt érvényesíti.
  - Példák: `tags['Acct.CostCenter']`, ahol az **acct. CostCenter** a címke neve.
- `tags['''<tagName>''']`
  - Ez a zárójel-szintaxis támogatja az aposztrófokat tartalmazó címkéket dupla aposztrófokkal.
  - Ahol a **"\<tagName\>"** a címke neve, amely ellenőrzi a feltételt.
  - Példa: `tags['''My.Apostrophe.Tag''']`, ahol a **"My. aposztróf. tag"** a címke neve.
- tulajdonság-aliasok – lista esetén lásd: [aliasok](#aliases).

> [!NOTE]
> a `tags.<tagName>`, a `tags[tagName]`és a `tags[tag.with.dots]` továbbra is elfogadható módon deklarálja a címkék mezőt. Az előnyben részesített kifejezések azonban a fentiekben láthatók.

#### <a name="use-tags-with-parameters"></a>Címkék használata paraméterekkel

Egy paraméter értéke adható át egy címke mezőnek. Ha egy paramétert egy címke mezőre továbbít, a házirend-hozzárendelés során növeli a házirend-definíció rugalmasságát.

A következő `concat` példában a rendszer a **TagName** paraméter értékének megadásával létrehoz egy címkéket a címke mezőinek kereséséhez. Ha ez a címke nem létezik, a **módosítás** hatására a rendszer hozzáadja a címkét a naplózott erőforrások szülő erőforráscsoporthoz tartozó megnevezett címke értékével a `resourcegroup()` lookup függvénnyel.

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

A feltételek az **érték**használatával is létrehozhatók. az **érték** a [paraméterekkel](#parameters), a [támogatott sablon-funkciókkal](#policy-functions)vagy a literálokkal kapcsolatos feltételeket ellenőrzi.
az **érték** a támogatott [feltételekkel](#conditions)párosítva van.

> [!WARNING]
> Ha egy _sablon függvény_ eredménye hibát jelez, a szabályzat kiértékelése sikertelen lesz. A sikertelen értékelés implicit **Megtagadás**. További információ: a [sablon meghibásodásának elkerülése](#avoiding-template-failures). A [enforcementMode](./assignment-structure.md#enforcement-mode) használatával megakadályozhatja az új vagy frissített erőforrások sikertelen értékelésének hatását az új házirend-definíció tesztelése és érvényesítése során.

#### <a name="value-examples"></a>Példák az értékekre

Ez a házirend-szabály például a `resourceGroup()` függvény eredményének és a visszaadott **név** tulajdonságnak a `*netrg`**hasonló** feltételéhez való összehasonlítását **használja.** A szabály minden olyan erőforrást megtagad, amely nem a `Microsoft.Network/*` **típust** tartalmaz bármely olyan erőforráscsoport esetében, amelynek a neve `*netrg`ben végződik.

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

Ez a házirend-szabály például az **értéket** használja annak ellenőrzéséhez, hogy több **beágyazott függvény eredménye** `true`-e. A szabály minden olyan erőforrást megtagad, amely nem rendelkezik legalább három címkével.

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

A módosított szabályzattal rendelkező szabály `if()` ellenőrzi a **név** hosszát, mielőtt a rendszer a háromnál kevesebb karakternél rövidebb értékre próbálja beolvasni a `substring()`. Ha a **név** túl rövid, a "nem az ABC-től kezdődően" értéket adja vissza, és az **ABC**-hez képest. Az **ABC** -vel nem kezdődő rövid névvel rendelkező erőforrás továbbra is meghiúsul a házirend-szabályban, de az értékelés során már nem okoz hibát.

### <a name="count"></a>Darabszám

Azok a feltételek, amelyek megszámolják, hogy az erőforrás-adattartalomban lévő tömb tagjai közül hányan felelnek meg egy feltétel kifejezésének a **Count** kifejezés használatával. A gyakori forgatókönyvek azt ellenőrzik, hogy a tömb tagjai megfelelnek-e a feltételnek: "legalább az egyike", "a" minden "vagy" nincs ". a **Count** kiértékeli az egyes [\[\*\] alias](#understanding-the--alias) -tömb tagjait a feltétel kifejezéséhez, és a _valódi_ eredményeket összegzi, amely a kifejezés operátorával összehasonlítva történik.

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
- **Count** (nem kötelező): a feltétel kifejezése, hogy egyenként kiértékelje az egyes [\[\*\] alias](#understanding-the--alias) tömb tagjának száma **. mező**. Ha ez a tulajdonság nincs megadva, a "Field" elérési úttal rendelkező összes tömb-tag _igaz_értékre van kiértékelve. Ezen a tulajdonságon belül bármely [feltétel](../concepts/definition-structure.md#conditions) használható.
  A tulajdonságon belül a [logikai operátorok](#logical-operators) összetett értékelési követelmények létrehozására használhatók.
- **\<feltétel\>** (kötelező): a rendszer összehasonlítja az értéket a darabszámnak megfelelő elemek számával **. where** feltétel kifejezése. Numerikus [feltételt](../concepts/definition-structure.md#conditions) kell használni.

#### <a name="count-examples"></a>Példák száma

1\. példa: Ellenőrizze, hogy egy tömb üres-e

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

2\. példa: csak egy tömb tag keresése a feltétel kifejezésének teljesítéséhez

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

3\. példa: legalább egy tömbhöz tartozó tag keresése a feltétel kifejezésének teljesítéséhez

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

4\. példa: annak megjelölése, hogy az összes objektum összes tagja megfelel-e a feltétel kifejezésének

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

5\. példa: annak megjelölése, hogy az összes karakterlánc-tömb tagjai megfelelnek-e a feltétel kifejezésének

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

6\. példa: az **értéken** belüli **mező** használata annak ellenőrzését, hogy az összes tömb tagjai megfelelnek-e a feltétel kifejezésének.

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

7\. példa: Győződjön meg arról, hogy legalább egy tömb tagja megegyezik a feltétel kifejezésben szereplő több tulajdonsággal

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

### <a name="policy-functions"></a>A házirend-funkciók

Az összes [Resource Manager-sablon funkció](../../../azure-resource-manager/templates/template-functions.md) egy házirend-szabályon belül használható, az alábbi függvények és a felhasználó által definiált függvények kivételével:

- copyIndex ()
- üzembe helyezés ()
- list*
- newGuid()
- pickZones()
- szolgáltatók ()
- hivatkozás ()
- resourceId()
- változók ()

A következő függvény használható egy házirend-szabályban, de eltér a használattól egy Azure Resource Manager sablonban:

- `utcNow()` – a Resource Manager-sablonoktól eltérően ez a defaultValue-n kívül is használható.
  - Egy olyan karakterláncot ad vissza, amely az univerzális ISO 8601 DateTime formátumban van beállítva az aktuális dátumra és időpontra vonatkozóan (éééé-hh-NNTóó: PP: SS. fffffffZ).

A következő függvények csak a házirend-szabályokban érhetők el:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **datetime**: [Required] String-String in the Universal ISO 8601 datetime Format éééé-hh-NNTóó: PP: SS. fffffffZ '
  - **numberOfDaysToAdd**: [kötelező] egész szám – hozzáadandó napok száma
- `field(fieldName)`
  - **Mezőnév**: [kötelező] karakterlánc – a beolvasandó [mező](#fields) neve
  - Annak az erőforrásnak az értékét adja vissza, amelyet az IF feltétel kiértékel.
  - a `field` elsődlegesen a **AuditIfNotExists** és a **DeployIfNotExists** használja a kiértékelt erőforráson található hivatkozási mezőkre. Erre a használatra példa látható az [DeployIfNotExists példában](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - A házirend kiértékelését kiváltó kérelem API-verzióját adja vissza (például: `2019-09-01`). Ez lesz az az API-verzió, amelyet a PUT/PATCH kérelemben használt az erőforrás-létrehozási/frissítési kérelmekre vonatkozó értékelésekhez. A meglévő erőforrásokon a megfelelőségi értékelés során mindig a legújabb API-verziót használja a rendszer.
  


#### <a name="policy-function-example"></a>Példa a házirend-függvényre

Ez a házirend-szabály példa a `resourceGroup` Resource függvényt használja a **Name** tulajdonság beolvasásához, kombinálva az `concat` Array és Object függvénnyel, hogy olyan `like` feltételt hozzon létre, amely kikényszeríti az erőforrás nevét, hogy az erőforráscsoport nevével kezdődjön.

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

- Azure Policy-bővítmény a Visual Studio Code-hoz (ajánlott)

  A [Visual Studio Code](../how-to/extension-for-vscode.md) -hoz készült Azure Policy-bővítmény használatával megtekintheti és derítheti fel az erőforrás-tulajdonságok aliasait.

  ![Azure Policy-bővítmény a Visual Studio Code-hoz](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure Resource Graph

  Az erőforrás **aliasnevének** megjelenítéséhez használja a `project` operátort.

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

- REST API-t / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>A [*] alias ismertetése

A rendelkezésre álló aliasok közül több olyan verzióval rendelkezik, amely "normál" néven jelenik meg, és egy másik, **\[\*\]** csatolva. Például:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

A "normál" alias a mezőt egyetlen értékként jelöli. Ez a mező az összehasonlítási forgatókönyvek pontos egyeztetésére szolgál, ha az értékek teljes halmazának pontosan meghatározottnak kell lennie, nem több és nem kevesebb.

A **\[\*\]** alias lehetővé teszi, hogy összehasonlítsa a tömbben lévő egyes elemek értékével és az egyes elemek adott tulajdonságaival. Ez a módszer lehetővé teszi, hogy összehasonlítsa a "Ha nincs", "," vagy "Ha az összes" forgatókönyvhöz tartozó elem tulajdonságait. Összetettebb forgatókönyvek esetén használja a [Count](#count) feltétel kifejezést. A **ipRules\[\*\]** használatával egy példa érvényesíti, hogy minden _művelet_ _megtagadva_van, de nem kell aggódnia, hogy hány szabály létezik, vagy hogy mi az IP- _érték_ .
Ez a minta-szabály a **ipRules\[\*a \]. Value értéket** ellenőrzi **, és csak** akkor alkalmazza a **effectType** , ha nem talál legalább egy egyezést:

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



További információ: [[\*] alias kiértékelése](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Kezdeményezések

Kezdeményezések lehetővé teszi több kapcsolódó szabályzatdefiníciót, mert dolgozik egy csoport egyetlen elemként hozzárendelések és a felügyeleti egyszerűsítése érdekében. Ha például be egy egyetlen-kezdeményezéshez kapcsolódó címkézési szabályzatdefiníciók csoportosíthatók. Ahelyett, hogy az egyes házirendek külön-külön rendeli, a kezdeményezés vonatkoznak.

> [!NOTE]
> A kezdeményezés kiosztása után a kezdeményezésére szintjének paraméterei nem módosíthatók. Ennek oka, hogy a paraméter megadásakor a rendszer a következőt adja meg: **defaultValue** .

Az alábbi példa bemutatja, hogyan hozhat létre egy kezdeményezést két címke kezeléséhez: `costCenter` és `productName`. Két beépített szabályzatokat használ a alkalmazni az alapértelmezett címke értéke.

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

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
