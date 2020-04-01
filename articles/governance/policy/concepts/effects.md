---
title: A hatások működésének ismertetése
description: Az Azure Policy-definíciók különböző hatásokkal rendelkeznek, amelyek meghatározzák a megfelelőség kezelését és jelentésének módját.
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 0330cb5c732921efda3627dec92e486657097d82
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422460"
---
# <a name="understand-azure-policy-effects"></a>Az Azure-szabályzat hatásainak ismertetése

Az Azure Policyban minden egyes szabályzatdefiníció egyetlen hatással bír. Ez a hatás határozza meg, hogy mi történik, ha a házirendszabály kiértékelése egyezik. A hatások eltérően viselkednek, ha új erőforrásra, frissített erőforrásra vagy meglévő erőforrásra vannak kinevezve.

Ezeket a hatásokat jelenleg egy házirend-definíció támogatja:

- [Hozzáfűzés](#append)
- [Naplózás](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Megtagadás](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Letiltva](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (előzetes verzió)
- [EnforceRegoPolicy](#enforceregopolicy) (előzetes verzió)
- [Módosítás](#modify)

## <a name="order-of-evaluation"></a>Az értékelés sorrendje

Az Azure Resource Manageren keresztül küldött, az erőforrások létrehozására és frissítésére vonatkozó kérelmeket először az Azure Policy értékeli ki. Az Azure Policy létrehoz egy listát az erőforrásra vonatkozó összes hozzárendelésről, majd kiértékeli az erőforrást az egyes definíciókból. Az Azure Policy feldolgozza a hatások több, mielőtt a kérelmet a megfelelő erőforrás-szolgáltatónak átadása előtt. Ezzel megakadályozza az erőforrás-szolgáltató szükségtelen feldolgozását, ha egy erőforrás nem felel meg az Azure Policy tervezett cégirányítási vezérlőinek.

- **Először a Letiltva** ellenőrzés alapján állapítja meg, hogy a házirendszabályt ki kell-e értékelni.
- **A hozzáfűzés** és **a módosítás** ezután kiértékelésre kerül. Mivel bármelyik módosíthatja a kérelmet, a módosítás megakadályozhatja a naplózást vagy a megtagadási hatás indítását.
- **A megtagadást** ezután kiértékeli a rendszer. A megtagadás naplózás előtti kiértékelésével a nem kívánt erőforrások kettős naplózása megakadályozható.
- **A naplózást** ezután kiértékeli a rendszer, mielőtt az erőforrás-szolgáltatóhoz küldő kérés tanuskóba kerülne.

Miután az erőforrás-szolgáltató sikeres ségi kódot ad vissza, **az AuditIfNotExists** és **a DeployIfNotExists** kiértékeli, hogy szükség van-e további megfelelőségi naplózásra vagy műveletre.

Jelenleg nincs kiértékelés isorrendjében a **EnforceOPAConstraint** vagy **EnforceRegoPolicy** hatások.

## <a name="disabled"></a>Letiltva

Ez a hatás akkor hasznos, ha a tesztelési helyzetekben, vagy ha a házirend-definíció paraméterezett a hatás. Ez a rugalmasság lehetővé teszi egyetlen hozzárendelés letiltását ahelyett, hogy letiltaná a házirend összes hozzárendelését.

A Letiltott hatás alternatívája a **kényszerítésMód,** amely a házirend-hozzárendelésen van beállítva.
Ha **a enforcementMode** le van _tiltva,_ az erőforrások kiértékelése továbbra is megtörténik. Naplózás, például a tevékenységnaplók, és a házirend hatása nem fordul elő. További információ: [Házirend-hozzárendelés - kényszerítési mód.](./assignment-structure.md#enforcement-mode)

## <a name="append"></a>Hozzáfűzés

A hozzáfűzés további mezők hozzáadására szolgál a kért erőforráshoz a létrehozás vagy frissítés során. Gyakori példa a tárolási erőforrások engedélyezett IP-k ének megadása.

> [!IMPORTANT]
> A hozzáfűzés nem címketulajdonságokkal való használatra készült. Bár a hozzáfűzés címkéket adhat egy erőforráshoz egy létrehozási vagy frissítési kérelem során, javasoljuk, hogy a [Címkék módosítása effektust](#modify) használja.

### <a name="append-evaluation"></a>Kiértékelés hozzáfűzése

Hozzáfűzés kiértékeli, mielőtt a kérelmet kap egy erőforrás-szolgáltató egy erőforrás létrehozása vagy frissítése során. A hozzáfűzés mezőket ad az erőforráshoz, ha az házirendszabály **ha** feltétele teljesül. Ha a hozzáfűző hatás felülírna egy értéket az eredeti kérelemben egy másik értékkel, akkor megtagadási hatásként működik, és elutasítja a kérelmet. Ha új értéket szeretne hozzáfűzni egy meglévő tömbhöz, használja az alias **[\*]** verzióját.

Ha a hozzáfűző effektust használó házirend-definíció egy értékelési ciklus részeként fut, nem módosítja a már létező erőforrásokat. Ehelyett minden olyan erőforrást, amely megfelel a **ha** feltételnek, nem megfelelőként jelöl meg.

### <a name="append-properties"></a>Hozzáfűzési tulajdonságok

A hozzáfűző effektus csak egy **részlettömböt** tartalmaz, amely szükséges. Mivel a **részletek** tömbök, egyetlen **mező/érték** párt vagy többszöröst is vehet fel. Az elfogadható mezők listájának [definíciós struktúrájában](definition-structure.md#fields) tájékformált.

### <a name="append-examples"></a>Példák hozzáfűzése

1. példa: Egyetlen **mező/érték** pár, amely nem **-\*[ ]** [aliast](definition-structure.md#aliases) használ **tömbértékkel,** hogy IP-szabályokat állítson be egy tárfiókban. Ha a nem **-\*[ ]** alias tömb, a hatás az **értéket** a teljes tömbként fűzi hozzá. Ha a tömb már létezik, megtagadási esemény következik be az ütközésből.

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

2. példa: Egyetlen **mező/érték** pár **egy\*[ ]** [alias](definition-structure.md#aliases) használatával **tömbértékkel** ip-szabályok at állíthat be egy tárfiókban. A **\*[ ]** alias használatával a hatás hozzáfűzi az **értéket** egy potenciálisan már meglévő tömbhöz. Ha a tömb még nem létezik, akkor létre jön.

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

A módosítás az erőforrások címkéinek hozzáadására, frissítésére vagy eltávolítására szolgál a létrehozás vagy frissítés során. Gyakori példa a címkék frissítése az erőforrásokon, például a costCenter. A módosítási házirendnek mindig indexelt értékre kell `mode` _állítania, kivéve,_ ha a célerőforrás erőforráscsoport. A meglévő nem megfelelő erőforrások [szervizelési feladattal](../how-to/remediate-resources.md)orvosolhatók . Egyetlen módosítási szabály tetszőleges számú művelettel rendelkezhet.

> [!IMPORTANT]
> A módosítás jelenleg csak címkékkel használható. Ha címkéket kezel, javasoljuk, hogy a Hozzáfűzés módosítás helyett a Modify lehetőséget használja további művelettípusokat és a meglévő erőforrások kiújítását. Azonban hozzáfűzése ajánlott, ha nem tudja létrehozni a felügyelt identitást.

### <a name="modify-evaluation"></a>Kiértékelés módosítása

A módosítás kiértékelés, mielőtt a kérelmet egy erőforrás-szolgáltató feldolgozná egy erőforrás létrehozása vagy frissítése során. Az erőforrás címkéinek módosítása vagy frissítése, ha a házirendszabály **ha** feltétele teljesül.

Ha a Módosítás effektust használó házirend-definíció egy értékelési ciklus részeként fut, nem módosítja a már létező erőforrásokat. Ehelyett minden olyan erőforrást, amely megfelel a **ha** feltételnek, nem megfelelőként jelöl meg.

### <a name="modify-properties"></a>Tulajdonságok módosítása

A Modify effektus **részletes** tulajdonsága tartalmazza az összes altulajdonságot, amely meghatározza a szervizeléshez szükséges engedélyeket, valamint a címkeértékek hozzáadásához, frissítéséhez vagy eltávolításához használt **műveleteket.**

- **roleDefinitionIds** [kötelező]
  - Ennek a tulajdonságnak tartalmaznia kell egy olyan karakterlánctömböt, amely megfelel az előfizetés által elérhető szerepköralapú hozzáférés-vezérlési szerepkör azonosítójának. További információ: [Szervizelés - házirend-definíció konfigurálása](../how-to/remediate-resources.md#configure-policy-definition).
  - A megadott szerepkörnek tartalmaznia kell a [közreműködői](../../../role-based-access-control/built-in-roles.md#contributor) szerepkörnek biztosított összes műveletet.
- **műveletek** [kötelező]
  - Az összes címkeművelet tömbje, amelyet az egyező erőforrásokon kell végrehajtani.
  - Tulajdonságok:
    - **művelet** [kötelező]
      - Azt határozza meg, hogy milyen műveletet kell végrehajtani egy egyező erőforráson. A következő lehetőségek közül választhat: _addOrReplace_, _Add_, _Remove_. _Add_ viselkedik hasonló a [hozzáfűző](#append) hatás.
    - **mező** [kötelező]
      - A hozzáadni, cserélni vagy eltávolítani a hozzáadni, cserélni vagy eltávolítani a címkét. A címkeneveknek más [mezők](./definition-structure.md#fields)esetében is meg kell felelniük az elnevezési konvenciónak.
    - **érték** (nem kötelező)
      - A címkét beállítani.
      - Ez a tulajdonság akkor szükséges, ha a **művelet** _addOrReplace_ vagy _Add_.

### <a name="modify-operations"></a>Műveletek módosítása

Az **operations** tulajdonságtömb lehetővé teszi több címke különböző módon történő módosítását egyetlen házirend-definícióból. Minden művelet a **művelet,** **a mező**és az **érték** tulajdonságokból áll. A Művelet határozza meg, hogy a szervizelési feladat mit tesz a címkékkel, a mező határozza meg, hogy melyik címkét módosítja, és az érték határozza meg az adott címke új beállítását. Az alábbi példa a következő címkemódosításokat teszi:

- A `environment` címkét "Teszt" értékre állítja, még akkor is, ha már létezik más értékkel.
- Eltávolítja a `TempResource`címkét .
- A `Dept` címkét a házirend-hozzárendelésen konfigurált _DeptName_ házirendparaméterre állítja be.

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
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

A **művelettulajdonság** beállításai a következők:

|Művelet |Leírás |
|-|-|
|addOrReplace |Hozzáadja a megadott címkét és értéket az erőforráshoz, még akkor is, ha a címke már létezik egy másik értékkel. |
|Hozzáadás |Hozzáadja a megadott címkét és értéket az erőforráshoz. |
|Eltávolítás |Eltávolítja a definiált címkét az erőforrásból. |

### <a name="modify-examples"></a>Példák módosítása

1. példa: `environment` Adja hozzá `environment` a címkét, és cserélje le a meglévő címkéket a "Test" kifejezésre:

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

2. példa: `env` Távolítsa el `environment` a címkét, és adja hozzá a címkét, vagy cserélje le a meglévő `environment` címkéket egy paraméterezett értékre:

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

A megtagadás sal megakadályozhatja, hogy egy erőforrás-kérelem ne feleljen meg a megadott szabványoknak egy házirend-definíción keresztül, és sikertelen a kérés.

### <a name="deny-evaluation"></a>Értékelés megtagadása

Egyező erőforrás létrehozásakor vagy frissítésekor a megtagadás megakadályozza a kérést, mielőtt elküldené az erőforrás-szolgáltatónak. A kérelem visszaad egy `403 (Forbidden)`. A portálon a Tiltott lehet tekinteni, mint egy állapot a központi telepítés, amely megakadályozta a házirend-hozzárendelés.

A meglévő erőforrások kiértékelése során a megtagadási házirend-definíciónak megfelelő erőforrások nem megfelelőként vannak megjelölve.

### <a name="deny-properties"></a>Tulajdonságok megtagadása

A megtagadási hatás nem rendelkezik további tulajdonságokkal a házirend-definíció **akkori** feltételében való használatra.

### <a name="deny-example"></a>Példa megtagadása

Példa: A megtagadási hatás használata.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Naplózás

A naplózás egy figyelmeztetési esemény létrehozására szolgál a tevékenységnaplóban egy nem megfelelő erőforrás kiértékelésekor, de nem állítja le a kérelmet.

### <a name="audit-evaluation"></a>Az ellenőrzés értékelése

Naplózás az azure-szabályzat által ellenőrzött utolsó hatás egy erőforrás létrehozása vagy frissítése során. Az Azure-szabályzat ezután elküldi az erőforrást az erőforrás-szolgáltatónak. A naplózás ugyanúgy működik egy erőforrás-kérelem nél és egy értékelési ciklusban. Az Azure `Microsoft.Authorization/policies/audit/action` Policy hozzáad egy műveletet a tevékenységnaplóhoz, és nem megfelelőként jelöli meg az erőforrást.

### <a name="audit-properties"></a>Tulajdonságok naplózása

A naplózási hatás nem rendelkezik további tulajdonságokkal a házirend-definíció **akkori** feltételében való használatra.

### <a name="audit-example"></a>Példa naplózása

Példa: A naplózási hatás használata.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

Az AuditIfNotExists lehetővé teszi az **if** feltételnek megfelelő erőforrások naplózását, de nem rendelkezik az **akkori** feltétel **részleteiben** megadott összetevőkkel.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists kiértékelés

Az AuditIfNotExists futtatása azt követően fut, hogy egy erőforrás-szolgáltató kezelt egy létrehozási vagy frissítési erőforrás-kérelmet, és sikeres állapotkódot adott vissza. A naplózás akkor történik, ha nincsenek kapcsolódó erőforrások, vagy ha a **ExistenceCondition** által definiált erőforrások nem értékelik igazra. Az Azure `Microsoft.Authorization/policies/audit/action` Policy ugyanúgy ad hozzá egy műveletet a tevékenységnaplóhoz, mint a naplózási hatás. Aktiváláskor az az erőforrás, amely megfelelt az **if** feltételnek, az az erőforrás, amely nem megfelelőként van megjelölve.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists tulajdonságok

Az AuditIfNotExists hatások **részletes** tulajdonsága tartalmazza az összes olyan altulajdonságot, amely meghatározza a kapcsolódó erőforrásokat.

- **Típus** [kötelező]
  - Megadja az egyeztetni nek megfelelő erőforrás típusát.
  - Ha **a details.type** egy erőforrástípus az **if** feltétel erőforrás alatt, a szabályzat az ilyen **típusú** erőforrásoklekérdezése a kiértékelt erőforrás hatókörén belül. Ellenkező esetben a házirend-lekérdezések ugyanazon erőforráscsoporton belül, mint a kiértékelt erőforrás.
- **Név** (nem kötelező)
  - Megadja az erőforrás pontos nevét, és a megadott típusú összes erőforrás helyett egy adott erőforrást kell lekérnie.
  - Ha az **if.field.type** és **then.details.type** feltételértékei egyeznek, akkor **a Név** _kötelezővé_ válik, és annak kell lennie. `[field('name')]` Ehelyett azonban [egy ellenőrzési](#audit) hatást kell figyelembe venni.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a kapcsolódó erőforrás egyeztetése egy másik erőforráscsoportból származik.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely a **ha** feltétel erőforrás alatt lenne.
  - Az alapértelmezett az **if** feltétel erőforrás erőforráscsoportja.
- **ExistenceScope** (nem kötelező)
  - Az engedélyezett értékek az _Előfizetés_ és _a ResourceGroup_.
  - Beállítja, hogy a kapcsolódó erőforrás honnan hol jelenjen meg.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely a **ha** feltétel erőforrás alatt lenne.
  - A _ResourceGroup_csoport esetében a **(ha** feltételerőforrás) erőforráscsoportra vagy a **ResourceGroupName**csoportban megadott erőforráscsoportra korlátozódna.
  - _Előfizetés esetén_lekérdezi a teljes előfizetést a kapcsolódó erőforráshoz.
  - Az alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, bármely kapcsolódó **erőforrás megfelel** a hatásnak, és nem indítja el a naplózást.
  - Ugyanazt a nyelvet használja, mint az **if** feltétel házirendszabálya, de minden kapcsolódó erőforrással külön-külön kiértékeli.
  - Ha bármely egyező kapcsolódó erőforrás kiértékeli, hogy igaz, a hatás teljesül, és nem váltja ki a naplózást.
  - A [field()] segítségével ellenőrizheti az egyenértékűséget az **if** feltétel ben lévő értékekkel.
  - Például annak ellenőrzésére használható, hogy a szülő erőforrás (az **if** feltételben) ugyanabban az erőforráshelyen van-e, mint a kapcsolódó erőforrás.

### <a name="auditifnotexists-example"></a>AuditIfNotExists példa

Példa: Kiértékeli a virtuális gépeket annak megállapítására, hogy a kártevőirtó bővítmény létezik-e, majd naplózza, ha hiányzik.

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

Az AuditIfNotExists-hez hasonlóan a DeployIfNotExists házirend-definíció a feltétel teljesülése esetén végrehajtja a sablon központi telepítését.

> [!NOTE]
> A **telepítettIfNotExists**támogatja [a beágyazott sablonokat,](../../../azure-resource-manager/templates/linked-templates.md#nested-template) de a csatolt sablonok jelenleg nem [támogatottak.](../../../azure-resource-manager/templates/linked-templates.md#linked-template)

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists kiértékelés

A DeployIfNotExists körülbelül 15 perccel azt követően fut, hogy egy erőforrás-szolgáltató kezelt egy létrehozási vagy frissítési erőforrás-kérelmet, és sikeres állapotkódot adott vissza. A sablon központi telepítése akkor történik, ha nincsenek kapcsolódó erőforrások, vagy ha a **ExistenceCondition** által definiált erőforrások nem értékelik igazra.
A telepítés időtartama a sablonban szereplő erőforrások összetettségétől függ.

A kiértékelési ciklus során a deployIfNotExists hatással rendelkező házirend-definíciók nem megfelelőként vannak megjelölve, de az erőforráson nem történik művelet. A meglévő nem megfelelő erőforrások [szervizelési feladattal](../how-to/remediate-resources.md)orvosolhatók .

### <a name="deployifnotexists-properties"></a>DeployIfNotExists tulajdonságok

A DeployIfNotExists hatás **részletes** tulajdonsága tartalmazza az összes olyan altulajdonságot, amely meghatározza a kapcsolódó erőforrásokat, és a végrehajtandó sablon központi telepítését.

- **Típus** [kötelező]
  - Megadja az egyeztetni nek megfelelő erőforrás típusát.
  - A ha **feltétel** erőforrás alatti erőforrás beolvasásával kezdődik, majd az **if** feltétel erőforrással azonos erőforráscsoporton belül lekérdezéseket.
- **Név** (nem kötelező)
  - Megadja az erőforrás pontos nevét, és a megadott típusú összes erőforrás helyett egy adott erőforrást kell lekérnie.
  - Ha az **if.field.type** és **then.details.type** feltételértékei egyeznek, akkor **a Név** _kötelezővé_ válik, és annak kell lennie. `[field('name')]`
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a kapcsolódó erőforrás egyeztetése egy másik erőforráscsoportból származik.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely a **ha** feltétel erőforrás alatt lenne.
  - Az alapértelmezett az **if** feltétel erőforrás erőforráscsoportja.
  - Ha egy sablon központi telepítése végrehajtásra kerül, akkor az az érték erőforráscsoportjában van telepítve.
- **ExistenceScope** (nem kötelező)
  - Az engedélyezett értékek az _Előfizetés_ és _a ResourceGroup_.
  - Beállítja, hogy a kapcsolódó erőforrás honnan hol jelenjen meg.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely a **ha** feltétel erőforrás alatt lenne.
  - A _ResourceGroup_csoport esetében a **(ha** feltételerőforrás) erőforráscsoportra vagy a **ResourceGroupName**csoportban megadott erőforráscsoportra korlátozódna.
  - _Előfizetés esetén_lekérdezi a teljes előfizetést a kapcsolódó erőforráshoz.
  - Az alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, bármely kapcsolódó **típusú** erőforrás megfelel a hatásnak, és nem indítja el a központi telepítést.
  - Ugyanazt a nyelvet használja, mint az **if** feltétel házirendszabálya, de minden kapcsolódó erőforrással külön-külön kiértékeli.
  - Ha bármely egyező kapcsolódó erőforrás kiértékeli a true értéket, a hatás teljesül, és nem indítja el a központi telepítést.
  - A [field()] segítségével ellenőrizheti az egyenértékűséget az **if** feltétel ben lévő értékekkel.
  - Például annak ellenőrzésére használható, hogy a szülő erőforrás (az **if** feltételben) ugyanabban az erőforráshelyen van-e, mint a kapcsolódó erőforrás.
- **roleDefinitionIds** [kötelező]
  - Ennek a tulajdonságnak tartalmaznia kell egy olyan karakterlánctömböt, amely megfelel az előfizetés által elérhető szerepköralapú hozzáférés-vezérlési szerepkör azonosítójának. További információ: [Szervizelés - házirend-definíció konfigurálása](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (nem kötelező)
  - Az engedélyezett értékek az _Előfizetés_ és _a ResourceGroup_.
  - Beállítja az aktiválandó központi telepítés típusát. _Az előfizetés_ [előfizetési szintű központi telepítést](../../../azure-resource-manager/templates/deploy-to-subscription.md)jelez, _a ResourceGroup_ egy erőforráscsoportnak történő központi telepítést.
  - Előfizetési szintű központi telepítések használataesetén meg kell adni egy _helytulajdonságot_ a _központi telepítésben._
  - Az alapértelmezett érték a _ResourceGroup_.
- **Központi telepítés** [szükséges]
  - Ez a tulajdonság tartalmazza a teljes sablon üzembe `Microsoft.Resources/deployments` helyezését, mivel a PUT API-nak átkell adni. További információt a [Deployments REST API című témakörben](/rest/api/resources/deployments)talál.

  > [!NOTE]
  > A központi **telepítési** tulajdonságon belüli összes függvény a sablon összetevőiként lesz kiértékelve, nem pedig a házirend. A kivétel a **paraméterek** tulajdonság, amely értékeket ad át a házirendből a sablonba. Ebben a szakaszban a sablon paraméter neve alatt található **érték** az érték átadásának végrehajtására szolgál (lásd a _fullDbName-t_ a DeployIfNotExists példában).

### <a name="deployifnotexists-example"></a>DeployIfNotExists példa

Példa: Kiértékeli az SQL Server-adatbázisokat annak megállapítására, hogy engedélyezve van-e az transparentDataEncryption. Ha nem, akkor az engedélyezésre alkalmas központi telepítés végrehajtása történik.

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

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Ez a hatás a `Microsoft.Kubernetes.Data`házirend-definíciós *módban* használatos. Arra használják, hogy az [OPA-megszorítási keretrendszerrel](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) definiált Gatekeeper v3-es belépésvezérlési szabályokat [az Azure-beli,](https://www.openpolicyagent.org/) saját felügyeletű Kubernetes-fürtöknek adja át.

> [!NOTE]
> [Az Azure Policy for AKS Engine](aks-engine.md) nyilvános előzetes verzióban érhető el, és csak a beépített szabályzatdefiníciókat támogatja.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint értékelése

Az Open Policy Agent felvételi vezérlő kiértékeli az új kérelmeket a fürt valós időben.
5 percenként befejeződik a fürt teljes vizsgálata, és az eredmények et jelentik az Azure Policy-nek.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint tulajdonságai

Az EnforceOPAConstraint hatás **részletes** tulajdonsága a Gatekeeper v3 belépésvezérlési szabályt leíró altulajdonságokkal rendelkezik.

- **megszorításTemplate** [kötelező]
  - Az új kényszereket meghatározó CustomResourceDefinition (CRD) megszorítássablon. A sablon határozza meg a Rego logika, a megszorítássémája és a megszorítás paraméterek, amelyek átkerülnek az Azure Policy **értékein** keresztül.
- **megkötés** [kötelező]
  - A kényszersablon CRD-megvalósítása. Az **értékeken** keresztül `{{ .Values.<valuename> }}`átadott paramétereket használja . Az alábbi példában ez `{{ .Values.cpuLimit }}` `{{ .Values.memoryLimit }}`a és .
- **értékek** [nem kötelező]
  - Meghatározza azokat a paramétereket és értékeket, amelyeket át kell adni a megkötésnek. Minden értéknek léteznie kell a CrD megkötéssablonban.

### <a name="enforceopaconstraint-example"></a>EnforceOPAConstraint példa

Példa: Gatekeeper v3 belépésvezérlési szabály a tároló CPU- és memóriaerőforrás-korlátainak beállításához az AKS-motorban.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>RegoPolicy kényszerítése

Ez a hatás a `Microsoft.ContainerService.Data`házirend-definíciós *módban* használatos. A Gatekeeper v2-es belépésvezérlési szabályok áthaladására szolgál, amelyeket [a Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) to [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) az Azure [Kubernetes szolgáltatásban](../../../aks/intro-kubernetes.md)definiált.

> [!NOTE]
> [Az AKS-hez készült Azure-szabályzat](rego-for-aks.md) korlátozott előzetes verzióban érhető el, és csak a beépített szabályzatdefiníciókat támogatja

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy értékelés

Az Open Policy Agent felvételi vezérlő kiértékeli az új kérelmeket a fürt valós időben.
5 percenként befejeződik a fürt teljes vizsgálata, és az eredmények et jelentik az Azure Policy-nek.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy tulajdonságai

Az EnforceRegoPolicy hatás **részletes** tulajdonsága a Gatekeeper v2 belépésvezérlési szabályt leíró altulajdonságokat tartalmazza.

- **policyId** [kötelező]
  - A Rego beléptetési ellenőrzési szabály paramétereként egy egyedi név került átadásra.
- **házirend** [kötelező]
  - A Rego belépésvezérlési szabály URI-ját adja meg.
- **policyParameters** [nem kötelező]
  - Meghatározza a rego-házirendnek átadandó paramétereket és értékeket.

### <a name="enforceregopolicy-example"></a>Példa a RegoPolicy kényszerítésére

Példa: Gatekeeper v2 belépésvezérlési szabály, amely csak a megadott tárolórendszerképek engedélyezése az AKS-ben.

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

## <a name="layering-policies"></a>Rétegezési házirendek

Egy erőforrásra több hozzárendelés is hatással lehet. Ezek a hozzárendelések lehetnek azonos hatályúak vagy különböző hatókörök. Ezek a hozzárendelések is valószínűleg más hatást határozott meg. Az egyes házirendek feltétele és hatása egymástól függetlenül kiértékelésre kerül. Példa:

- 1. szakpolitika
  - Az erőforrás helyét a "westus" -ra korlátozza
  - Az "A" előfizetéshez rendelve
  - Megtagadási hatás
- 2. szakpolitika
  - Az erőforrás helyét az "eastus" -ra korlátozza
  - A B erőforráscsoporthoz rendelve az A előfizetésben
  - Ellenőrzési hatás
  
Ez a beállítás a következő eredményt eredményezné:

- Az "eastus" B erőforráscsoportban lévő bármely erőforrás megfelel a 2- es házirendnek, és nem felel meg az 1.
- A B erőforráscsoportban már nem az "eastus" erőforráscsoportban lévő erőforrások nem felelnek meg a 2- es házirendnek, és nem felelnek meg az 1- es házirendnek, ha nem a "westus"
- Az "A" előfizetésben lévő minden új erőforrást a házirend 1 tagad meg.
- Az A előfizetésben és a B erőforráscsoportban a "westus" erőforráscsoportban minden új erőforrás jön létre, és nem megfelelő a 2.

Ha mind az 1.

- A B erőforráscsoportban már nem az "eastus" erőforráscsoportban lévő erőforrások nem felelnek meg a 2.
- A B erőforráscsoportban már nem a "westus" erőforráscsoportban lévő erőforrások nem felelnek meg az 1.
- Az "A" előfizetésben lévő minden új erőforrást a házirend 1 tagad meg.
- Az A előfizetés B erőforráscsoportjában lévő minden új erőforrás megtagadva

Minden hozzárendelés külön-külön kiértékelve történik. Mint ilyen, nincs lehetőség arra, hogy egy erőforrás átcsússzon a hatókör beli különbségekből. A rétegezési politikák vagy a szakpolitikai átfedések nettó eredménye **halmozottleg korlátozónak**tekinthető . Ha például az 1- es házirend nek és a 2-nek is volt megtagadási hatása, az erőforrásokat az egymást átfedő és ütköző házirendek blokkolnák. Ha továbbra is szüksége van az erőforrás létrehozására a célhatókörben, tekintse át az egyes hozzárendelések kizárásait, hogy ellenőrizze, hogy a megfelelő házirendek hatással vannak-e a megfelelő hatókörökre.

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](../how-to/programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](../how-to/get-compliance-data.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](../how-to/remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)
