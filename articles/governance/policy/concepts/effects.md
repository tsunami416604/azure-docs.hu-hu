---
title: A hatások működésének megismerése
description: Azure Policy definíciók különböző effektusokkal rendelkeznek, amelyek meghatározzák a megfelelőség felügyeletének és jelentésének módját.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 502c8a87c4e915ebd1fd764915daa9c89a307097
ms.sourcegitcommit: 78f367310e243380b591ff10f2500feca93f5d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77544130"
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
- [EnforceOPAConstraint](#enforceopaconstraint) (előzetes verzió)
- [EnforceRegoPolicy](#enforceregopolicy) (előzetes verzió)
- [Módosítása](#modify)

## <a name="order-of-evaluation"></a>Kiértékelési sorrend

Az erőforrásoknak a Azure Resource Manager alapján történő létrehozására vagy frissítésére vonatkozó kérelmeket először Azure Policy kiértékeli. Azure Policy létrehoz egy listát az erőforrásra vonatkozó összes hozzárendelésről, majd kiértékeli az erőforrást az egyes definíciók alapján. Azure Policy dolgozza fel a hatásokat, mielőtt átadná a kérést a megfelelő erőforrás-szolgáltatónak. Ez megakadályozza az erőforrás-szolgáltató általi szükségtelen feldolgozást, ha az erőforrás nem teljesíti a Azure Policy tervezett irányítási vezérlőit.

- A **Letiltva** érték megadásával megállapíthatja, hogy ki kell-e értékelni a házirend-szabályt.
- Ezután kiértékeli a **hozzáfűzést** és a **módosítást** . Mivel bármelyik megváltoztathatja a kérést, a módosítás miatt előfordulhat, hogy egy naplózási vagy megtagadási hatás megakadályozza az indítást.
- A **Megtagadás** ekkor kiértékelésre kerül. Kiértékelésével megtagadása naplózás, mielőtt egy nemkívánatos erőforrás dupla naplózása a rendszer letiltja.
- A rendszer ezt követően kiértékeli a **naplózást** , mielőtt az erőforrás-szolgáltatóra felveszi a kérelmet.

Miután az erőforrás-szolgáltató egy sikerességi kódot ad vissza, a **AuditIfNotExists** és a **DeployIfNotExists** kiértékelésével megállapíthatja, hogy szükséges-e további megfelelőségi naplózás vagy művelet.

Jelenleg nincs kiértékelési sorrend a **EnforceOPAConstraint** vagy a **EnforceRegoPolicy** effektushoz.

## <a name="disabled"></a>Letiltva

Ez a hatás tesztelési helyzetek, vagy ha a szabályzatdefiníció rendelkezik paraméterezni a hatás hasznos. Ezt a rugalmasságot lehetővé teszi egy egyetlen hozzárendelési helyett letiltása a szabályzat-hozzárendelések mindegyikét letiltása.

A letiltott effektus alternatívájaként a szabályzat-hozzárendelés **enforcementMode** van beállítva.
Ha a enforcementMode _le van tiltva_, a rendszer továbbra is kiértékeli az erőforrásokat. A naplózás, például a tevékenységek naplói, és a házirend hatása nem történik meg. További információ: [szabályzat-hozzárendelés – kényszerítési mód](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Hozzáfűzés

Hozzáfűzés további mezőket hozzáadni a kért erőforrás létrehozása vagy módosítása során használatos. Gyakori példa egy tárolási erőforrás engedélyezett IP-címeinek megadására.

> [!IMPORTANT]
> A Hozzáfűzés a nem címkével ellátott tulajdonságokkal való használatra készült. Míg a hozzáfűzéssel hozzáadhat címkéket egy erőforráshoz egy létrehozási vagy frissítési kérelem során, azt javasoljuk, hogy használja inkább a címkék [módosítási](#modify) effektusát.

### <a name="append-evaluation"></a>Értékelés hozzáfűzése

Hozzáfűzés kiértékeli, mielőtt a kérést egy erőforrás-szolgáltató feldolgozza a létrehozása vagy egy adott erőforrás frissítése során. A Hozzáfűzés mezők hozzáadása az erőforráshoz, **Ha a házirend-szabály feltételei** teljesülnek. Ha Hozzáfűzés hatása lenne bírálja felül az eredeti kérelmet egy másik érték egy értéket, egy megtagadási hatást funkcionál, és elutasítja a kérelmet. Új érték meglévő tömbhöz való hozzáfűzéséhez használja az alias **[\*]** verzióját.

Ha egy szabályzatdefiníciót a Hozzáfűzés hatással egy kiértékelési ciklusa részeként fut, azt nem módosítások már meglévő erőforrásokat. Ehelyett olyan erőforrást jelöl, amely megfelel az **IF** feltétel nem megfelelőnek.

### <a name="append-properties"></a>Tulajdonságok hozzáfűzése

A hozzáfűzési effektushoz csak a **részletek** tömbje szükséges. A **részletek** egy tömb, amely egyetlen **mező/érték** párokat vagy többszöröseket is igénybe vehet. Tekintse meg az elfogadható mezők listáját a [definíciós struktúrában](definition-structure.md#fields) .

### <a name="append-examples"></a>Példák hozzáfűzése

1\. példa: egy **mező/érték** pár egy nem **[\*]** [aliast](definition-structure.md#aliases) használ egy tömb **értékkel** az IP-szabályok tárolási fiókra való beállításához. Ha a nem **[\*]** alias egy tömb, a hatás a teljes tömbként hozzáfűzi az **értéket** . Ha a tömb már létezik, megtagadási esemény következik be az ütközésből.

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

2\. példa: egyetlen **mező/érték** pár, amely egy **[\*]** [aliast](definition-structure.md#aliases) használ egy tömb **értékkel** egy Storage-fiók IP-szabályainak beállításához. A **[\*]** alias használatával a hatás hozzáfűzi az **értéket** egy potenciálisan előre létező tömbhöz. Ha a tömb még nem létezik, a rendszer létrehozza.

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

A módosítás a létrehozás vagy a frissítés során az erőforrásokhoz tartozó címkék hozzáadására, frissítésére és eltávolítására szolgál. Gyakori példa az olyan erőforrásokra vonatkozó címkék frissítése, mint például a costCenter. A módosítási házirendnek mindig _`mode` kell lennie, ha_ a célként megadott erőforrás egy erőforráscsoport. A meglévő, nem megfelelő erőforrások szervizelése [szervizelési feladattal](../how-to/remediate-resources.md)javítható. Egyetlen módosítási szabálynak tetszőleges számú művelete lehet.

> [!IMPORTANT]
> A módosítás jelenleg csak címkékkel használható. Ha címkéket kezel, javasolt a hozzáfűzés helyett a módosítás használata, amely további műveleti típusokat és a meglévő erőforrások javítását is lehetővé teszi. A Hozzáfűzés azonban ajánlott, ha nem tud felügyelt identitást létrehozni.

### <a name="modify-evaluation"></a>Értékelés módosítása

A módosítja a kiértékeléseket, mielőtt az erőforrás-szolgáltató feldolgozza a kérést az erőforrás létrehozása vagy frissítése során. Ha a házirend- **szabály feltételei** teljesülnek, módosítja a hozzáadási vagy frissítési címkéket az erőforráson.

Ha a módosítási hatást használó házirend-definíció egy értékelési ciklus részeként fut, akkor nem módosítja a már létező erőforrásokat. Ehelyett olyan erőforrást jelöl, amely megfelel az **IF** feltétel nem megfelelőnek.

### <a name="modify-properties"></a>Tulajdonságok módosítása

A Modify Effect **details** tulajdonsága minden olyan altulajdonsággal rendelkezik, amely meghatározza a szervizeléshez szükséges engedélyeket, valamint a címkék hozzáadásához, frissítéséhez vagy eltávolításához használt **műveleteket** .

- **roleDefinitionIds** [kötelező]
  - Ez a tulajdonság karakterláncok, amelyek megfelelnek a szerepköralapú hozzáférés-vezérlési szerepkör azonosítója elérhető-e az előfizetés-tartalmaznia kell. További információ: [szervizelés – házirend-definíció konfigurálása](../how-to/remediate-resources.md#configure-policy-definition).
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

- A `environment` címkét "teszt" értékre állítja, még akkor is, ha már létezik egy másik érték.
- Eltávolítja a `TempResource`címkét.
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
            "value": "[parameters('DeptName')]"
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

1\. példa: adja hozzá a `environment` címkét, és cserélje le a meglévő `environment` címkéket a "test" kifejezésre:

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

2\. példa: távolítsa el a `env` címkét, és adja hozzá a `environment` címkét, vagy cserélje le a meglévő `environment` címkéket paraméteres értékre:

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

Ha megtagadja a létrehozása vagy frissítése egy egyező erőforrás, megakadályozza, hogy az a kérelem erőforrás-szolgáltatónál elküldése előtt. A kérést a rendszer `403 (Forbidden)`ként adja vissza. A portálon a tiltott állapot, amely szerint a szabályzat-hozzárendelés nem sikerült elindítani az üzemelő példányon, tekinthet meg.

Meglévő erőforrások értékelése során az erőforrások, amelyek megfelelnek egy megtagadási szabályzatdefiníciót nem megfelelőként vannak megjelölve.

### <a name="deny-properties"></a>Megtagadási tulajdonságai

A megtagadási effektus nem rendelkezik további tulajdonságokkal a házirend-definíció **feltételében** való használathoz.

### <a name="deny-example"></a>Példa megtagadása

Példa: A Megtagadás hatás használatával.

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

A naplózási effektus nem rendelkezik további tulajdonságokkal a házirend-definíció **feltételében** való használathoz.

### <a name="audit-example"></a>Naplózási példa

Példa: A naplózási hatás használatával.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

A AuditIfNotExists lehetővé teszi a naplózást olyan erőforrásokon, amelyek megfelelnek az **IF** feltételnek, de nem rendelkeznek a **then** feltétel **részleteiben** megadott összetevőkkel.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists kiértékelése

AuditIfNotExists futtatása után egy erőforrás-szolgáltató rendelkezik kezelt létrehozásának vagy frissítésének erőforrás kérelmet, és sikeres állapotkódot adott vissza. A naplózás akkor fordul elő, ha nincsenek kapcsolódó erőforrások, vagy ha a **ExistenceCondition** által meghatározott erőforrások nem értékelik az igaz értéket. Azure Policy egy `Microsoft.Authorization/policies/audit/action` műveletet hoz létre a tevékenység naplójába, ugyanúgy, mint a naplózási effektus. Az aktiváláskor az az erőforrás, amely megfelel az **IF** feltételnek, nem megfelelőként megjelölt erőforrás.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists tulajdonságai

A AuditIfNotExists-effektusok **részletek** tulajdonsága az összes olyan altulajdonsággal rendelkezik, amely meghatározza a kapcsolódó erőforrásokat.

- **Típus** [kötelező]
  - Határozza meg a megfelelő kapcsolódó erőforrás.
  - Ha a **details. Type** egy erőforrástípus az **IF** feltétel erőforrása alatt, a szabályzat a kiértékelt erőforrás hatókörén belül lekérdezi az ilyen **típusú** erőforrásokat. Ellenkező esetben a házirend-lekérdezések a kiértékelt erőforrással megegyező erőforráscsoport alatt vannak.
- **Név** (nem kötelező)
  - A megfelelő erőforrás pontos nevét adja meg, és a egy adott erőforrás helyett a megadott típusú összes erőforrást beolvasni a szabályzat okoz.
  - Ha a feltétel értéke **if. Field. Type** , **majd. details. Type** egyezés, akkor a **név** _szükséges_ lesz, és `[field('name')]`nak kell lennie. A [naplózási](#audit) hatást azonban figyelembe kell venni.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a rendszer a kapcsolódó erőforrás egy másik erőforráscsoportban található származnak.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - Az alapértelmezett érték a **IF** Condition erőforrás erőforráscsoport.
- **ExistenceScope** (nem kötelező)
  - Az engedélyezett értékek az _előfizetések_ és a _ResourceGroup_.
  - Beállítja a hatókört, hova felel meg a kapcsolódó erőforrást beolvasni.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - A _ResourceGroup_esetében korlátozni kellene az **IF** feltétel erőforrásának erőforráscsoport vagy a **ResourceGroupName**megadott erőforráscsoport esetében.
  - Az _előfizetés_a kapcsolódó erőforrás teljes előfizetését kérdezi le.
  - Az alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, az összes kapcsolódó erőforrás **megfelel a** hatásnak, és nem indítja el a naplózást.
  - Ugyanazt a nyelvet használja, mint az **IF** feltétel házirend-szabálya, de a rendszer minden kapcsolódó erőforrásra külön kiértékeli.
  - Ha minden egyező kapcsolódó erőforrás igaz értéket ad vissza, a hatás teljesült, és nem aktiválja a naplózási.
  - A [Field ()] paranccsal az egyenértékűséget az **IF** feltétel értékeivel is megtekintheti.
  - Például arra használható, hogy ellenőrizze, hogy a szülő erőforrás ( **Ha** a feltétel) ugyanabban az erőforrásban van-e, mint a megfelelő kapcsolódó erőforrás.

### <a name="auditifnotexists-example"></a>AuditIfNotExists példa

Példa: Kiértékeli a virtuális gépek határozza meg, ha a kártevőirtó bővítmény létezik, akkor naplózza, ha valóban hiányzik.

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
> A [beágyazott sablonok](../../../azure-resource-manager/templates/linked-templates.md#nested-template) a **deployIfNotExists**-mel támogatottak, de a [csatolt sablonok](../../../azure-resource-manager/templates/linked-templates.md#linked-template) jelenleg nem támogatottak.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists kiértékelése

Az DeployIfNotExists körülbelül 15 perccel azután fut le, hogy egy erőforrás-szolgáltató kezelte az erőforrás-létrehozási vagy-frissítési kérelmet, és sikeres állapotkódot adott vissza. A sablonok központi telepítése akkor történik, ha nincsenek kapcsolódó erőforrások, vagy ha a **ExistenceCondition** által meghatározott erőforrások nem értékelik az igaz értéket.
A központi telepítés időtartama a sablonban található erőforrások bonyolultságával függ.

Egy értékelési ciklus során a szabályzatdefiníciók egy DeployIfNotExists hatást, amelyek megfelelnek az erőforrások nem megfelelőként lesznek megjelölve, de nem tesz az erőforráson.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists tulajdonságai

A DeployIfNotExists-effektus **részletek** tulajdonsága az összes olyan altulajdonsággal rendelkezik, amely meghatározza a kapcsolódó erőforrásokat, valamint a sablon központi telepítését.

- **Típus** [kötelező]
  - Határozza meg a megfelelő kapcsolódó erőforrás.
  - A parancs elindítja az erőforrás lekérését az **IF** feltétel erőforrása alatt, majd az adott erőforráscsoporthoz tartozó, az **IF** feltétel erőforrással rendelkező lekérdezéseket.
- **Név** (nem kötelező)
  - A megfelelő erőforrás pontos nevét adja meg, és a egy adott erőforrás helyett a megadott típusú összes erőforrást beolvasni a szabályzat okoz.
  - Ha a feltétel értéke **if. Field. Type** , **majd. details. Type** egyezés, akkor a **név** _szükséges_ lesz, és `[field('name')]`nak kell lennie.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a rendszer a kapcsolódó erőforrás egy másik erőforráscsoportban található származnak.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - Az alapértelmezett érték a **IF** Condition erőforrás erőforráscsoport.
  - Ha egy sablon telepítésének, ezt az értéket az erőforráscsoportban üzembe helyezett.
- **ExistenceScope** (nem kötelező)
  - Az engedélyezett értékek az _előfizetések_ és a _ResourceGroup_.
  - Beállítja a hatókört, hova felel meg a kapcsolódó erőforrást beolvasni.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - A _ResourceGroup_esetében korlátozni kellene az **IF** feltétel erőforrásának erőforráscsoport vagy a **ResourceGroupName**megadott erőforráscsoport esetében.
  - Az _előfizetés_a kapcsolódó erőforrás teljes előfizetését kérdezi le.
  - Az alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, az összes kapcsolódó erőforrás **megfelel a** hatásnak, és nem indítja el a telepítést.
  - Ugyanazt a nyelvet használja, mint az **IF** feltétel házirend-szabálya, de a rendszer minden kapcsolódó erőforrásra külön kiértékeli.
  - Minden egyező kapcsolódó erőforrás igaz értéket ad vissza, ha a hatás teljesült, és nem aktiválja a központi telepítést.
  - A [Field ()] paranccsal az egyenértékűséget az **IF** feltétel értékeivel is megtekintheti.
  - Például arra használható, hogy ellenőrizze, hogy a szülő erőforrás ( **Ha** a feltétel) ugyanabban az erőforrásban van-e, mint a megfelelő kapcsolódó erőforrás.
- **roleDefinitionIds** [kötelező]
  - Ez a tulajdonság karakterláncok, amelyek megfelelnek a szerepköralapú hozzáférés-vezérlési szerepkör azonosítója elérhető-e az előfizetés-tartalmaznia kell. További információ: [szervizelés – házirend-definíció konfigurálása](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (nem kötelező)
  - Az engedélyezett értékek az _előfizetések_ és a _ResourceGroup_.
  - Az aktiválni kívánt központi telepítés típusának beállítása. Az _előfizetés_ az [előfizetési szinten](../../../azure-resource-manager/templates/deploy-to-subscription.md)üzemelő telepítést jelzi, a _ResourceGroup_ pedig egy erőforráscsoporthoz történő központi telepítést jelez.
  - Az előfizetések szintjén üzemelő példányok használatakor meg kell adni egy _Location_ tulajdonságot a _központi telepítésben_ .
  - Az alapértelmezett érték a _ResourceGroup_.
- **Üzembe helyezés** [kötelező]
  - Ennek a tulajdonságnak szerepelnie kell a sablon teljes telepítésének, mivel a `Microsoft.Resources/deployments` PUT API-nak lesz átadva. További információ: [központi telepítések REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > A **központi telepítési** tulajdonságon belüli összes függvény a sablon összetevőiként lesz kiértékelve, nem pedig a házirendnek. A kivétel a **Paraméterek** tulajdonság, amely értékeket továbbít a szabályzatból a sablonba. Ennek az értéknek a megadásához a sablon paraméter neve alatt található **értéket** kell használni (lásd: _fullDbName_ a DeployIfNotExists példában).

### <a name="deployifnotexists-example"></a>DeployIfNotExists példa

Példa: SQL Server-adatbázisok, hogy ha engedélyezve van-e a transparentDataEncryption értékeli ki. Ha nem, akkor a rendszer végrehajtja az engedélyezéshez szükséges központi telepítést.

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

Ez a hatás `Microsoft.Kubernetes.Data`házirend-definíciós *módjával* van használatban. A rendszer az [Opa-korlátozási keretrendszerben](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) definiált forgalomirányító v3 belépésvezérlés-szabályok átadására szolgál a házirendügynök (OPA) és az Azure-beli önálló felügyelt Kubernetes-fürtök [megnyitásához](https://www.openpolicyagent.org/) .

> [!NOTE]
> Az [AK-motor Azure Policy](aks-engine.md) nyilvános előzetes verzióban érhető el, és csak a beépített szabályzat-definíciókat támogatja.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint kiértékelése

A nyílt házirend-ügynök beléptetési vezérlője valós időben értékeli ki a fürtön lévő új kéréseket.
5 percenként a fürt teljes vizsgálata befejeződött, és az eredmények Azure Policynak.

### <a name="enforceopaconstraint-properties"></a>EnforceOPAConstraint tulajdonságai

A EnforceOPAConstraint Effect **details** tulajdonsága a forgalomirányító v3 belépésvezérlés szabályának altulajdonságaival rendelkezik.

- **constraintTemplate** [kötelező]
  - A korlátozási sablon CustomResourceDefinition (CRD), amely új korlátozásokat határoz meg. A sablon meghatározza a Rego logikát, a megkötési sémát és a megkötési paramétereket, amelyek a Azure Policy **értékein** keresztül lesznek átadva.
- **korlátozás** [kötelező]
  - A korlátozási sablon CRD-implementációja. Az **értékeken** keresztül átadott paramétereket `{{ .Values.<valuename> }}`ként használja. Az alábbi példában ez `{{ .Values.cpuLimit }}` és `{{ .Values.memoryLimit }}`.
- **értékek** [nem kötelező]
  - Meghatározza a korlátozásnak átadandó paramétereket és értékeket. Minden értéknek léteznie kell a korlátozási sablon CRD-ben.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy példa

Példa: forgalomirányító v3 belépésvezérlés-szabály a tároló CPU-és memória-erőforrás-korlátainak beállításához az AK-motorban.

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

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Ez a hatás `Microsoft.ContainerService.Data`házirend-definíciós *módjával* van használatban. A [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) által meghatározott forgalomirányító v2 belépésvezérlés-szabályok átadására szolgálnak [az](https://www.openpolicyagent.org/) [Azure Kubernetes Service](../../../aks/intro-kubernetes.md)-ben.

> [!NOTE]
> Az AK-hoz készült [Azure Policy](rego-for-aks.md) korlátozott előzetes verzióban érhető el, és csak a beépített szabályzat-definíciókat támogatja

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy kiértékelése

A nyílt házirend-ügynök beléptetési vezérlője valós időben értékeli ki a fürtön lévő új kéréseket.
5 percenként a fürt teljes vizsgálata befejeződött, és az eredmények Azure Policynak.

### <a name="enforceregopolicy-properties"></a>EnforceRegoPolicy tulajdonságai

A EnforceRegoPolicy effektus **details** tulajdonsága a forgalomirányító v2 belépésvezérlés szabályát leíró altulajdonságokkal rendelkezik.

- **policyId** [kötelező]
  - Egy egyedi név, amelyet paraméterként adott át a Rego belépésvezérlési szabálynak.
- **házirend** [kötelező]
  - Megadja a Rego-belépésvezérlés szabályának URI-JÁT.
- **policyParameters** [nem kötelező]
  - Meghatározza a Rego szabályzatnak átadandó paramétereket és értékeket.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy példa

Példa: forgalomirányító v2 belépésvezérlési szabály, amely csak a megadott tároló lemezképeit engedélyezi az AK-ban.

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

Több hozzárendelések hatással lehet egy erőforrást. Ezeket a hozzárendeléseket lehet ugyanabban a hatókörben vagy egy másik hatókört. Ezeket a hozzárendeléseket mindegyike valószínűleg is definiálva másik hatást. Egymástól függetlenül értékeli a feltételt és minden szabályzat érvénybe. Például:

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

Minden hozzárendelés egyenként értékeli ki. Mint ilyen nem erőforrás lehetőséget bérjegyzékes eseményáramlási kimaradást keresztül, a hatókör közötti különbségek. A rétegbeli házirendek vagy a szabályzatok átfedésének nettó eredménye az **összesítő legszigorúbbnak**minősül. Például ha mindkét házirend 1. és 2 hatást megtagadás, erőforrás blokkolná-e az átfedésben lévő és az ütköző szabályzatok. Ha továbbra is kell lennie az erőforrás létrehozott célhatóköre, tekintse át a kivételeket a megfelelő házirendek ellenőrzéséhez minden egyes hozzárendelés hatással vannak a megfelelő hatókörök.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
