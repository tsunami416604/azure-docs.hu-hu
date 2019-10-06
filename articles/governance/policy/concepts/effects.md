---
title: A hatások működésének megismerése
description: Azure Policy definíciók különböző effektusokkal rendelkeznek, amelyek meghatározzák a megfelelőség felügyeletének és jelentésének módját.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 991cfb54dc511c284c5f5d0cf1807d5dd42b34ea
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978076"
---
# <a name="understand-azure-policy-effects"></a>Az Azure Policy hatások ismertetése

Minden egyes szabályzatdefiníciót az Azure Policy egy egyetlen hatása. A hatás határozza meg, mi történik, ha a szabály kiértékelése történik az egyeztetéshez. A hatás eltérően viselkednek, ha azok egy új erőforrást, egy frissíteni az erőforrás vagy egy meglévő erőforrást.

Ezek a hatások jelenleg a szabályzatok definíciójában támogatottak:

- [Hozzáfűzése](#append)
- [Naplózás](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Tagadja](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Letiltva](#disabled)
- [EnforceRegoPolicy](#enforceregopolicy) (előzetes verzió)
- [Módosítása](#modify)

## <a name="order-of-evaluation"></a>Kiértékelési sorrend

Az erőforrásoknak a Azure Resource Manager alapján történő létrehozására vagy frissítésére vonatkozó kérelmeket először Azure Policy kiértékeli. Azure Policy létrehoz egy listát az erőforrásra vonatkozó összes hozzárendelésről, majd kiértékeli az erőforrást az egyes definíciók alapján. Azure Policy dolgozza fel a hatásokat, mielőtt átadná a kérést a megfelelő erőforrás-szolgáltatónak. Ez megakadályozza az erőforrás-szolgáltató általi szükségtelen feldolgozást, ha az erőforrás nem teljesíti a Azure Policy tervezett irányítási vezérlőit.

- **Letiltott** határozza meg, ha a szabály kell kiértékelni, először be van jelölve.
- Ezután kiértékeli a **hozzáfűzést** és a **módosítást** . Mivel bármelyik megváltoztathatja a kérést, a módosítás miatt előfordulhat, hogy egy naplózási vagy megtagadási hatás megakadályozza az indítást.
- **Megtagadási** Ezután kiértékeli. Kiértékelésével megtagadása naplózás, mielőtt egy nemkívánatos erőforrás dupla naplózása a rendszer letiltja.
- **Naplózási** Ezután kiértékeli a kérést az erőforrás-szolgáltató fog előtt.

Miután az erőforrás-szolgáltató, sikerkódot küld vissza **AuditIfNotExists** és **DeployIfNotExists** értékeli annak megállapításához, hogy további megfelelőségi naplózás vagy a művelet szükséges.

Jelenleg nincs kiértékelési sorrend a **EnforceRegoPolicy** hatáshoz.

## <a name="disabled"></a>Letiltva

Ez a hatás tesztelési helyzetek, vagy ha a szabályzatdefiníció rendelkezik paraméterezni a hatás hasznos. Ezt a rugalmasságot lehetővé teszi egy egyetlen hozzárendelési helyett letiltása a szabályzat-hozzárendelések mindegyikét letiltása.

## <a name="append"></a>Hozzáfűzés

Hozzáfűzés további mezőket hozzáadni a kért erőforrás létrehozása vagy módosítása során használatos. Gyakori példa egy tárolási erőforrás engedélyezett IP-címeinek megadására.

> [!IMPORTANT]
> A Hozzáfűzés a nem címkével ellátott tulajdonságokkal való használatra készült. Míg a hozzáfűzéssel hozzáadhat címkéket egy erőforráshoz egy létrehozási vagy frissítési kérelem során, azt javasoljuk, hogy használja inkább a címkék [módosítási](#modify) effektusát.

### <a name="append-evaluation"></a>Értékelés hozzáfűzése

Hozzáfűzés kiértékeli, mielőtt a kérést egy erőforrás-szolgáltató feldolgozza a létrehozása vagy egy adott erőforrás frissítése során. Fűzze hozzá mezőket ad hozzá az erőforrás során a **Ha** a szabály a feltétel teljesül. Ha Hozzáfűzés hatása lenne bírálja felül az eredeti kérelmet egy másik érték egy értéket, egy megtagadási hatást funkcionál, és elutasítja a kérelmet. Új érték meglévő tömbhöz való hozzáfűzéséhez használja az alias **[\*]** verzióját.

Ha egy szabályzatdefiníciót a Hozzáfűzés hatással egy kiértékelési ciklusa részeként fut, azt nem módosítások már meglévő erőforrásokat. Ehelyett jelöli meg minden olyan erőforrást, amely megfelel a **Ha** feltétel nem megfelelő.

### <a name="append-properties"></a>Tulajdonságok hozzáfűzése

Csak akkor Hozzáfűzés hatással van egy **részletek** tömb, amely szükséges. Mint **részletek** egy tömb, egyetlen is igénybe vehet **mező/érték** pár vagy többszöröseként jelenik meg. Tekintse meg [szabályzatdefiníciók struktúrája](definition-structure.md#fields) elfogadható mezők listáját.

### <a name="append-examples"></a>Példák hozzáfűzése

1\. példa: Egyetlen **mező/érték** pár, amely egy nem **[\*]** [aliast](definition-structure.md#aliases) használ egy tömb **értékkel** egy Storage-fiók IP-szabályainak beállításához. Ha a nem **[\*]** alias egy tömb, a hatás a teljes tömbként hozzáfűzi az **értéket** . Ha a tömb már létezik, megtagadási esemény következik be az ütközésből.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

2\. példa Egyetlen **mező/érték** pár, amely egy **[\*]** [aliast](definition-structure.md#aliases) használ egy tömb **értékkel** egy Storage-fiók IP-szabályainak beállításához. A **[\*]** alias használatával a hatás hozzáfűzi az **értéket** egy potenciálisan előre létező tömbhöz. Ha a tömb még nem létezik, a rendszer létrehozza.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="modify"></a>Módosítás

A módosítás a létrehozás vagy a frissítés során az erőforrásokhoz tartozó címkék hozzáadására, frissítésére és eltávolítására szolgál. Gyakori példa az olyan erőforrásokra vonatkozó címkék frissítése, mint például a costCenter. A módosítási házirendnek mindig `mode` értékűnek kell lennie _indexelt_értékre. A meglévő, nem megfelelő erőforrások szervizelése [szervizelési feladattal](../how-to/remediate-resources.md)javítható.
Egyetlen módosítási szabálynak tetszőleges számú művelete lehet.

> [!IMPORTANT]
> A módosítás jelenleg csak címkékkel használható. Ha címkéket kezel, javasolt a hozzáfűzés helyett a módosítás használata, amely további műveleti típusokat és a meglévő erőforrások javítását is lehetővé teszi. A Hozzáfűzés azonban ajánlott, ha nem tud felügyelt identitást létrehozni.

### <a name="modify-evaluation"></a>Értékelés módosítása

A módosítja a kiértékeléseket, mielőtt az erőforrás-szolgáltató feldolgozza a kérést az erőforrás létrehozása vagy frissítése során. Ha a házirend- **szabály feltételei** teljesülnek, módosítja a hozzáadási vagy frissítési címkéket az erőforráson.

Ha a módosítási hatást használó házirend-definíció egy értékelési ciklus részeként fut, akkor nem módosítja a már létező erőforrásokat. Ehelyett jelöli meg minden olyan erőforrást, amely megfelel a **Ha** feltétel nem megfelelő.

### <a name="modify-properties"></a>Tulajdonságok módosítása

A Modify Effect **details** tulajdonsága minden olyan altulajdonsággal rendelkezik, amely meghatározza a szervizeléshez szükséges engedélyeket, valamint a címkék hozzáadásához, frissítéséhez vagy eltávolításához használt **műveleteket** .

- **roleDefinitionIds** [kötelező]
  - Ez a tulajdonság karakterláncok, amelyek megfelelnek a szerepköralapú hozzáférés-vezérlési szerepkör azonosítója elérhető-e az előfizetés-tartalmaznia kell. További információkért lásd: [szervizelési – konfigurálja a szabályzat-definíció](../how-to/remediate-resources.md#configure-policy-definition).
  - A definiált szerepkörnek tartalmaznia kell a [közreműködő](../../../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz megadott összes műveletet.
- **műveletek** [kötelező]
  - Az összes címkéző művelet tömbje, amely a megfelelő erőforrásokon fejeződik be.
  - Tulajdonságok
    - **művelet** [kötelező]
      - Meghatározza, hogy milyen műveletet kell végrehajtani a megfelelő erőforráson. A lehetőségek a következők: _addOrReplace_, _Hozzáadás_, _Eltávolítás_. A _Hozzáadás_ a [hozzáfűzési](#append) effektushoz hasonló.
    - **mező** [kötelező]
      - A hozzáadni, cserélni vagy eltávolítani kívánt címke. A címkék nevének meg kell felelnie a többi [mezőhöz](./definition-structure.md#fields)tartozó elnevezési konvenciónak.
    - **érték** (nem kötelező)
      - Az az érték, amellyel a címkét be kell állítani.
      - Ez a tulajdonság kötelező, ha a **művelet** _addOrReplace_ vagy _hozzáadása_.

### <a name="modify-operations"></a>Műveletek módosítása

Az **Operations** Property Array lehetővé teszi több címke különböző módon történő módosítását egyetlen házirend-definícióból. Minden művelet **művelet**, **mező**és **érték** tulajdonságaiból tevődik fel. A művelet meghatározza, hogy a Szervizelési feladat mit tesz a címkék területen, a mező határozza meg, hogy melyik címke módosult, és az érték határozza meg az adott címke új beállítását. Az alábbi példa a következő címke-módosításokat végzi el:

- A `environment` címkét úgy állítja be, hogy "test" legyen, még akkor is, ha már létezik egy másik érték.
- Eltávolítja a `TempResource` címkét.
- Beállítja a `Dept` címkét a szabályzat-hozzárendelésen konfigurált _DeptName_ házirend-paraméterre.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "field": "[parameters('DeptName')]"
        }
    ]
}
```

A **Operation** tulajdonság a következő beállításokkal rendelkezik:

|Művelet |Leírás |
|-|-|
|addOrReplace |Hozzáadja a definiált címkét és értéket az erőforráshoz, még akkor is, ha a címke már létezik egy másik értékkel. |
|Hozzáadás |Hozzáadja a definiált címkét és értéket az erőforráshoz. |
|Eltávolítás |Eltávolítja a definiált címkét az erőforrásból. |

### <a name="modify-examples"></a>Példák módosítása

1\. példa: Adja hozzá a `environment` címkét, és cserélje le a meglévő `environment` címkéket a "test" kifejezésre:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

2\. példa Távolítsa el a `env` címkét, adja hozzá a `environment` címkét, vagy cserélje le a meglévő @no__t 2 címkéket paraméteres értékkel:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>Megtagadás

Megtagadási szolgál, hogy egy erőforrás-kérelmet, amely nem felel meg a szabályzat-definíció segítségével meghatározott előírásoknak, és a kérelem sikertelen.

### <a name="deny-evaluation"></a>Értékelés megtagadása

Ha megtagadja a létrehozása vagy frissítése egy egyező erőforrás, megakadályozza, hogy az a kérelem erőforrás-szolgáltatónál elküldése előtt. A kérelem visszaadott egy `403 (Forbidden)`. A portálon a tiltott állapot, amely szerint a szabályzat-hozzárendelés nem sikerült elindítani az üzemelő példányon, tekinthet meg.

Meglévő erőforrások értékelése során az erőforrások, amelyek megfelelnek egy megtagadási szabályzatdefiníciót nem megfelelőként vannak megjelölve.

### <a name="deny-properties"></a>Megtagadási tulajdonságai

A megtagadási hatás nem rendelkezik használható tulajdonságokat a **majd** szabályzatdefiníció feltétel.

### <a name="deny-example"></a>Példa megtagadása

Példa: A Megtagadás effektus használata.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Naplózás

Naplózási figyelmeztetési esemény létrehozása a tevékenységnaplóban a nem megfelelő erőforrások kiértékelésekor szolgál, de azt nem állítsa le a kérést.

### <a name="audit-evaluation"></a>Értékelés naplózása

Az erőforrás létrehozása vagy frissítése során az Azure Policy által ellenőrzött legutóbb végrehajtott ellenőrzés. Azure Policy ezután elküldi az erőforrást az erőforrás-szolgáltatónak. Naplózási egy erőforrás-kérés és a egy kiértékelési ciklusa ugyanúgy működik. Azure Policy egy `Microsoft.Authorization/policies/audit/action` műveletet hoz létre a tevékenység naplójába, és nem megfelelőként jelöli meg az erőforrást.

### <a name="audit-properties"></a>Naplózási tulajdonságok

A naplózási hatás nem rendelkezik használható tulajdonságokat a **majd** szabályzatdefiníció feltétel.

### <a name="audit-example"></a>Naplózási példa

Példa: A naplózási effektus használata.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists lehetővé teszi a naplózást az erőforrásokat, amelyek megfelelnek a **Ha** feltétel, azonban nem rendelkezik a megadott összetevők a **részletek** , a **majd** feltétel.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists kiértékelése

AuditIfNotExists futtatása után egy erőforrás-szolgáltató rendelkezik kezelt létrehozásának vagy frissítésének erőforrás kérelmet, és sikeres állapotkódot adott vissza. Az ellenőrzés akkor fordul elő, ha nincsenek kapcsolódó erőforrások, vagy ha az erőforrások által meghatározott **ExistenceCondition** nem igaz értéked. Azure Policy egy `Microsoft.Authorization/policies/audit/action` műveletet hoz létre a tevékenység naplójába, ugyanúgy, mint a naplózási effektus. Adatvezérelt, az erőforrást, amely elégedett az eredménnyel a **Ha** feltétel, hogy az erőforrás, amely a nem megfelelő van megjelölve.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists tulajdonságai

A **részletek** a altulajdonságokat, amelyek meghatározzák a kapcsolódó erőforrásokat a tulajdonságnak AuditIfNotExists hatásait.

- **Típus** [kötelező]
  - Határozza meg a megfelelő kapcsolódó erőforrás.
  - Ha a **details. Type** egy erőforrástípus az **IF** feltétel erőforrása alatt, a szabályzat a kiértékelt erőforrás hatókörén belül lekérdezi az ilyen **típusú** erőforrásokat. Ellenkező esetben a házirend-lekérdezések a kiértékelt erőforrással megegyező erőforráscsoport alatt vannak.
- **Név** (nem kötelező)
  - A megfelelő erőforrás pontos nevét adja meg, és a egy adott erőforrás helyett a megadott típusú összes erőforrást beolvasni a szabályzat okoz.
  - Ha a feltétel értéke **if. Field. Type** , **majd. details. Type** egyezés, akkor a **név** _szükséges_ lesz, és `[field('name')]` értéknek kell lennie. A [naplózási](#audit) hatást azonban figyelembe kell venni.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a rendszer a kapcsolódó erőforrás egy másik erőforráscsoportban található származnak.
  - Nem alkalmazható, ha **típus** egy erőforrás, amely alatt a **Ha** erőforrás feltételt.
  - Alapértelmezett érték a **Ha** erőforrás erőforráscsoport feltételt.
- **ExistenceScope** (nem kötelező)
  - Engedélyezett értékek a következők _előfizetés_ és _ResourceGroup_.
  - Beállítja a hatókört, hova felel meg a kapcsolódó erőforrást beolvasni.
  - Nem alkalmazható, ha **típus** egy erőforrás, amely alatt a **Ha** erőforrás feltételt.
  - A _ResourceGroup_, hogy korlátozza a **Ha** feltétel erőforrás erőforráscsoport vagy a megadott erőforráscsoport **ResourceGroupName**.
  - A _előfizetés_, lekérdezi a teljes előfizetés, a kapcsolódó erőforrás.
  - Alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, minden kapcsolódó erőforrás a **típus** eleget tesz a hatás és a naplózási nem aktiválja.
  - Ugyanazt a nyelvet használja, mint az a szabály a **Ha** feltételt, de képest értékeli ki minden kapcsolódó erőforrás külön-külön.
  - Ha minden egyező kapcsolódó erőforrás igaz értéket ad vissza, a hatás teljesült, és nem aktiválja a naplózási.
  - Használhatja a értékekkel egyenértékűség ellenőrzése [field()] a **Ha** feltétel.
  - Például segítségével ellenőrizze, hogy a szülő erőforrás (az a **Ha** feltétel) van ugyanazon a helyen erőforrás egyező kapcsolódó erőforrásként.

### <a name="auditifnotexists-example"></a>AuditIfNotExists példa

Példa: Kiértékeli a Virtual Machines annak megállapításához, hogy a kártevők bővítménye létezik-e, majd naplózza a hiányzó állapotot.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

A AuditIfNotExists hasonlóan a DeployIfNotExists házirend-definíciója is végrehajtja a sablon telepítését a feltétel teljesülése esetén.

> [!NOTE]
> [Beágyazott sablonok](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) támogatottak **deployIfNotExists**, de [kapcsolódó sablonok](../../../azure-resource-manager/resource-group-linked-templates.md) jelenleg nem támogatottak.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists kiértékelése

DeployIfNotExists futtatása után egy erőforrás-szolgáltató rendelkezik kezelt létrehozásának vagy frissítésének erőforrás kérelmet, és sikeres állapotkódot adott vissza. A sablonalapú telepítés akkor fordul elő, ha nincsenek kapcsolódó erőforrások, vagy ha az erőforrások által meghatározott **ExistenceCondition** nem igaz értéked.

Egy értékelési ciklus során a szabályzatdefiníciók egy DeployIfNotExists hatást, amelyek megfelelnek az erőforrások nem megfelelőként lesznek megjelölve, de nem tesz az erőforráson.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists tulajdonságai

A DeployIfNotExists-effektus **részletek** tulajdonsága az összes olyan altulajdonsággal rendelkezik, amely meghatározza a kapcsolódó erőforrásokat, valamint a sablon központi telepítését.

- **Típus** [kötelező]
  - Határozza meg a megfelelő kapcsolódó erőforrás.
  - Úgy, hogy lekérni egy erőforrást alatt elindítja a **Ha** feltétel erőforrás, akkor az azonos erőforráscsoportjában lévő lekérdezéseket a **Ha** erőforrás feltételt.
- **Név** (nem kötelező)
  - A megfelelő erőforrás pontos nevét adja meg, és a egy adott erőforrás helyett a megadott típusú összes erőforrást beolvasni a szabályzat okoz.
  - Ha a feltétel értéke **if. Field. Type** , **majd. details. Type** egyezés, akkor a **név** _szükséges_ lesz, és `[field('name')]` értéknek kell lennie.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a rendszer a kapcsolódó erőforrás egy másik erőforráscsoportban található származnak.
  - Nem alkalmazható, ha **típus** egy erőforrás, amely alatt a **Ha** erőforrás feltételt.
  - Alapértelmezett érték a **Ha** erőforrás erőforráscsoport feltételt.
  - Ha egy sablon telepítésének, ezt az értéket az erőforráscsoportban üzembe helyezett.
- **ExistenceScope** (nem kötelező)
  - Engedélyezett értékek a következők _előfizetés_ és _ResourceGroup_.
  - Beállítja a hatókört, hova felel meg a kapcsolódó erőforrást beolvasni.
  - Nem alkalmazható, ha **típus** egy erőforrás, amely alatt a **Ha** erőforrás feltételt.
  - A _ResourceGroup_, hogy korlátozza a **Ha** feltétel erőforrás erőforráscsoport vagy a megadott erőforráscsoport **ResourceGroupName**.
  - A _előfizetés_, lekérdezi a teljes előfizetés, a kapcsolódó erőforrás.
  - Alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, minden kapcsolódó erőforrás a **típus** eleget tesz a hatás és a központi telepítés nem aktiválja.
  - Ugyanazt a nyelvet használja, mint az a szabály a **Ha** feltételt, de képest értékeli ki minden kapcsolódó erőforrás külön-külön.
  - Minden egyező kapcsolódó erőforrás igaz értéket ad vissza, ha a hatás teljesült, és nem aktiválja a központi telepítést.
  - Használhatja a értékekkel egyenértékűség ellenőrzése [field()] a **Ha** feltétel.
  - Például segítségével ellenőrizze, hogy a szülő erőforrás (az a **Ha** feltétel) van ugyanazon a helyen erőforrás egyező kapcsolódó erőforrásként.
- **roleDefinitionIds** [kötelező]
  - Ez a tulajdonság karakterláncok, amelyek megfelelnek a szerepköralapú hozzáférés-vezérlési szerepkör azonosítója elérhető-e az előfizetés-tartalmaznia kell. További információkért lásd: [szervizelési – konfigurálja a szabályzat-definíció](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (nem kötelező)
  - Engedélyezett értékek a következők _előfizetés_ és _ResourceGroup_.
  - Az aktiválni kívánt központi telepítés típusának beállítása. Az _előfizetés_ az [előfizetési szinten](../../../azure-resource-manager/deploy-to-subscription.md)üzemelő telepítést jelzi, a _ResourceGroup_ pedig egy erőforráscsoporthoz történő központi telepítést jelez.
  - Az előfizetések szintjén üzemelő példányok használatakor meg kell adni egy _Location_ tulajdonságot a _központi telepítésben_ .
  - Alapértelmezett érték a _ResourceGroup_.
- **Üzembe helyezés** [kötelező]
  - Ez a tulajdonság a teljes körű sablonalapú telepítés tartalmaznia kell, hogy adná a `Microsoft.Resources/deployments` PUT API. További információkért lásd: a [központi telepítések REST API-val](/rest/api/resources/deployments).

  > [!NOTE]
  > Belül a függvények a **üzembe helyezési** tulajdonság összetevőként a sablon, a szabályzat nem értékeli ki. A kivétel a **paraméterek** tulajdonságot, amely a szabályzat alól értékeket továbbítja a sablont. A **érték** ebben a szakaszban egy sablon alapján paraméternév használja ezt az értéket átadja végrehajtásához (lásd: _fullDbName_ DeployIfNotExists példában).

### <a name="deployifnotexists-example"></a>DeployIfNotExists példa

Példa: Kiértékeli SQL Server adatbázisokat annak megállapításához, hogy engedélyezve van-e a transparentDataEncryption. Ha nem, akkor a rendszer végrehajtja az engedélyezéshez szükséges központi telepítést.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Ez a hatás a `Microsoft.ContainerService.Data` házirend-definíciós *módjával* használható. A [Rego](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego) által meghatározott belépésvezérlés-szabályok átadására szolgálnak [az](https://www.openpolicyagent.org/) [Azure Kubernetes Service](../../../aks/intro-kubernetes.md)-ben.

> [!NOTE]
> A [Kubernetes Azure Policy](rego-for-aks.md) nyilvános előzetes verzióban érhető el, és csak a beépített szabályzat-definíciókat támogatja.

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy kiértékelése

A nyílt házirend-ügynök beléptetési vezérlője valós időben értékeli ki a fürtön lévő új kéréseket.
5 percenként a fürt teljes vizsgálata befejeződött, és az eredmények Azure Policynak.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy tulajdonságai

A EnforceRegoPolicy-effektus **details** tulajdonsága a Rego belépésvezérlés szabályát leíró altulajdonságokkal rendelkezik.

- **policyId** [kötelező]
  - Egy egyedi név, amelyet paraméterként adott át a Rego belépésvezérlési szabálynak.
- **házirend** [kötelező]
  - Megadja a Rego-belépésvezérlés szabályának URI-JÁT.
- **policyParameters** [nem kötelező]
  - Meghatározza a Rego szabályzatnak átadandó paramétereket és értékeket.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy példa

Példa: Rego-szabály, amely csak a megadott tároló lemezképeit engedélyezi az AK-ban.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>Rétegezett házirendek

Több hozzárendelések hatással lehet egy erőforrást. Ezeket a hozzárendeléseket lehet ugyanabban a hatókörben vagy egy másik hatókört. Ezeket a hozzárendeléseket mindegyike valószínűleg is definiálva másik hatást. Egymástól függetlenül értékeli a feltételt és minden szabályzat érvénybe. Példa:

- 1 házirend
  - Korlátozza a 'westus' erőforrás helye
  - Egy előfizetéshez hozzárendelt
  - Hatás megtagadása
- Szabályzat 2
  - Korlátozza az "eastus" erőforrás helye
  - Előfizetés A B erőforráscsoportban rendelve
  - Naplózási
  
A telepítő a következő eredmény eredményez:

- "Eastus" B erőforráscsoportban már található valamilyen erőforrás a szabályzat 2 megfelelő és nem megfelelő házirendhez 1
- Minden erőforrás már nem az "eastus" B erőforráscsoport a nem megfelelő szabályzat 2 és a nem megfelelő, 1, ha nem található 'westus' házirend
- Bármilyen új erőforrást az előfizetésben A nem a 'westus' 1 házirend által megtagadva
- Bármilyen új erőforrást az előfizetésben A és B erőforráscsoport 'westus' létrehozva, és nem kompatibilis a szabályzat 2

Ha a szabályzat 1 és 2 szabályzat is hatással volt a Megtagadás, a helyzet változik:

- Minden erőforrás már nem az "eastus" B erőforráscsoport a nem megfelelő szabályzat 2
- Minden erőforrás már nem található 'westus' B erőforráscsoport a nem megfelelő házirendhez 1
- Bármilyen új erőforrást az előfizetésben A nem a 'westus' 1 házirend által megtagadva
- Bármilyen új erőforrást erőforráscsoportban B előfizetés, A rendszer megtagadja.

Minden hozzárendelés egyenként értékeli ki. Mint ilyen nem erőforrás lehetőséget bérjegyzékes eseményáramlási kimaradást keresztül, a hatókör közötti különbségek. Az eredmény rétegezett házirendek vagy a szabályzat átfedés minősül **összegző leginkább korlátozó**. Például ha mindkét házirend 1. és 2 hatást megtagadás, erőforrás blokkolná-e az átfedésben lévő és az ütköző szabályzatok. Ha továbbra is kell lennie az erőforrás létrehozott célhatóköre, tekintse át a kivételeket a megfelelő házirendek ellenőrzéséhez minden egyes hozzárendelés hatással vannak a megfelelő hatókörök.

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).