---
title: A hatások működésének megismerése
description: Azure Policy definíciók különböző effektusokkal rendelkeznek, amelyek meghatározzák a megfelelőség felügyeletének és jelentésének módját.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 9a21242cbb16466ed4c12746ff64bd7352925fed
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592795"
---
# <a name="understand-azure-policy-effects"></a>Azure Policy effektusok ismertetése

Azure Policy minden házirend-definíciója egyetlen hatással van. Ez a hatás határozza meg, hogy mi történik, ha a házirend-szabály kiértékelése megfelel a feltételeknek. A hatások másképp viselkednek, ha egy új erőforráshoz, egy frissített erőforráshoz vagy egy meglévő erőforráshoz tartoznak.

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

- A **Letiltva** érték megadásával megállapíthatja, hogy ki kell-e értékelni a házirend-szabályt.
- Ezután kiértékeli a **hozzáfűzést** és a **módosítást** . Mivel bármelyik megváltoztathatja a kérést, a módosítás miatt előfordulhat, hogy egy naplózási vagy megtagadási hatás megakadályozza az indítást.
- A **Megtagadás** ekkor kiértékelésre kerül. A Megtagadás kiértékelésével a nem kívánt erőforrások kettős naplózása megakadályozható.
- A rendszer ezt követően kiértékeli a **naplózást** , mielőtt az erőforrás-szolgáltatóra felveszi a kérelmet.

Miután az erőforrás-szolgáltató egy sikerességi kódot ad vissza, a **AuditIfNotExists** és a **DeployIfNotExists** kiértékelésével megállapíthatja, hogy szükséges-e további megfelelőségi naplózás vagy művelet.

Jelenleg nincs kiértékelési sorrend a **EnforceRegoPolicy** hatáshoz.

## <a name="disabled"></a>Letiltva

Ez a hatás akkor hasznos, ha tesztelési helyzetekben vagy ha a házirend-definícióban a hatás szerepel. Ez a rugalmasság lehetővé teszi egyetlen hozzárendelés letiltását ahelyett, hogy letiltja a szabályzat összes hozzárendelését.

A letiltott effektus alternatívájaként a szabályzat-hozzárendelés **enforcementMode** van beállítva.
Ha a enforcementMode _le van tiltva_, a rendszer továbbra is kiértékeli az erőforrásokat. A naplózás, például a tevékenységek naplói, és a házirend hatása nem történik meg. További információ: [szabályzat-hozzárendelés – kényszerítési mód](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Hozzáfűzés

A Hozzáfűzés használatával további mezőket adhat hozzá a kért erőforráshoz a létrehozás vagy a frissítés során. Gyakori példa egy tárolási erőforrás engedélyezett IP-címeinek megadására.

> [!IMPORTANT]
> A Hozzáfűzés a nem címkével ellátott tulajdonságokkal való használatra készült. Míg a hozzáfűzéssel hozzáadhat címkéket egy erőforráshoz egy létrehozási vagy frissítési kérelem során, azt javasoljuk, hogy használja inkább a címkék [módosítási](#modify) effektusát.

### <a name="append-evaluation"></a>Kiértékelés hozzáfűzése

A kiértékelések hozzáfűzésével a rendszer az erőforrás létrehozásakor vagy frissítésekor egy erőforrás-szolgáltató dolgozza fel a kérést. A Hozzáfűzés mezők hozzáadása az erőforráshoz, **Ha a házirend-szabály feltételei** teljesülnek. Ha a hozzáfűzési effektus felülbírálja az eredeti kérelemben szereplő értéket eltérő értékkel, akkor megtagadási hatásként viselkedik, és elutasítja a kérelmet. Új érték meglévő tömbhöz való hozzáfűzéséhez használja az alias **[\*]** verzióját.

Ha a hozzáfűzési effektust használó házirend-definíció egy értékelési ciklus részeként fut, akkor nem módosítja a már létező erőforrásokat. Ehelyett olyan erőforrást jelöl, amely megfelel az **IF** feltétel nem megfelelőnek.

### <a name="append-properties"></a>Hozzáfűzési tulajdonságok

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

A módosítás a létrehozás vagy a frissítés során az erőforrásokhoz tartozó címkék hozzáadására, frissítésére és eltávolítására szolgál. Gyakori példa az olyan erőforrásokra vonatkozó címkék frissítése, mint például a costCenter. A módosítási házirendnek mindig `mode` értékűnek _kell lennie, ha_ a célként megadott erőforrás egy erőforráscsoport. A meglévő, nem megfelelő erőforrások szervizelése [szervizelési feladattal](../how-to/remediate-resources.md)javítható. Egyetlen módosítási szabálynak tetszőleges számú művelete lehet.

> [!IMPORTANT]
> A módosítás jelenleg csak címkékkel használható. Ha címkéket kezel, javasolt a hozzáfűzés helyett a módosítás használata, amely további műveleti típusokat és a meglévő erőforrások javítását is lehetővé teszi. A Hozzáfűzés azonban ajánlott, ha nem tud felügyelt identitást létrehozni.

### <a name="modify-evaluation"></a>Értékelés módosítása

A módosítja a kiértékeléseket, mielőtt az erőforrás-szolgáltató feldolgozza a kérést az erőforrás létrehozása vagy frissítése során. Ha a házirend- **szabály feltételei** teljesülnek, módosítja a hozzáadási vagy frissítési címkéket az erőforráson.

Ha a módosítási hatást használó házirend-definíció egy értékelési ciklus részeként fut, akkor nem módosítja a már létező erőforrásokat. Ehelyett olyan erőforrást jelöl, amely megfelel az **IF** feltétel nem megfelelőnek.

### <a name="modify-properties"></a>Tulajdonságok módosítása

A Modify Effect **details** tulajdonsága minden olyan altulajdonsággal rendelkezik, amely meghatározza a szervizeléshez szükséges engedélyeket, valamint a címkék hozzáadásához, frissítéséhez vagy eltávolításához használt **műveleteket** .

- **roleDefinitionIds** [kötelező]
  - Ennek a tulajdonságnak tartalmaznia kell egy olyan karakterlánc-tömböt, amely megfelel az előfizetés által elérhető szerepköralapú hozzáférés-vezérlési szerepkör-AZONOSÍTÓnak. További információ: [szervizelés – házirend-definíció konfigurálása](../how-to/remediate-resources.md#configure-policy-definition).
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

2\. példa: távolítsa el a `env` címkét, adja hozzá a `environment` címkét, vagy cserélje le a meglévő `environment` címkéket paraméteres értékkel:

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

A Megtagadás megakadályozza egy olyan erőforrás-kérelem használatát, amely nem felel meg a meghatározott szabványoknak egy házirend-definíción keresztül, és a kérést nem teljesíti.

### <a name="deny-evaluation"></a>Kiértékelés megtagadása

Egyeztetett erőforrás létrehozásakor vagy frissítésekor a Megtagadás megakadályozza a kérést az erőforrás-szolgáltatónak való küldés előtt. A kérést a rendszer `403 (Forbidden)` értékként adja vissza. A portálon a tiltott megtekinthető állapotként az üzemelő példányon, amelyet a szabályzat-hozzárendelés megakadályozott.

A meglévő erőforrások kiértékelése során a megtagadási szabályzat definíciójának megfelelő erőforrások nem megfelelőként vannak megjelölve.

### <a name="deny-properties"></a>Megtagadási tulajdonságok

A megtagadási effektus nem rendelkezik további tulajdonságokkal a házirend-definíció **feltételében** való használathoz.

### <a name="deny-example"></a>Megtagadási példa

Példa: a Megtagadás effektus használata.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Naplózás

A naplózás egy figyelmeztetési esemény létrehozására szolgál a tevékenység naplójában a nem megfelelő erőforrások kiértékelése során, de nem állítja le a kérést.

### <a name="audit-evaluation"></a>Naplózás kiértékelése

Az erőforrás létrehozása vagy frissítése során az Azure Policy által ellenőrzött legutóbb végrehajtott ellenőrzés. Azure Policy ezután elküldi az erőforrást az erőforrás-szolgáltatónak. A naplózás ugyanúgy működik, mint egy erőforrás-kérelem és egy értékelési ciklus. Azure Policy egy `Microsoft.Authorization/policies/audit/action` műveletet hoz létre a tevékenység naplójába, és nem megfelelőként jelöli meg az erőforrást.

### <a name="audit-properties"></a>Naplózási tulajdonságok

A naplózási effektus nem rendelkezik további tulajdonságokkal a házirend-definíció **feltételében** való használathoz.

### <a name="audit-example"></a>Példa naplózásra

Példa: a naplózási effektus használata.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

A AuditIfNotExists lehetővé teszi a naplózást olyan erőforrásokon, amelyek megfelelnek az **IF** feltételnek, de nem rendelkeznek a **then** feltétel **részleteiben** megadott összetevőkkel.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists kiértékelése

A AuditIfNotExists azt követően fut, hogy egy erőforrás-szolgáltató kezelt egy erőforrás-létrehozási vagy frissítési kérelmet, és sikeres állapotkódot adott vissza. A naplózás akkor fordul elő, ha nincsenek kapcsolódó erőforrások, vagy ha a **ExistenceCondition** által meghatározott erőforrások nem értékelik az igaz értéket. Azure Policy egy `Microsoft.Authorization/policies/audit/action` műveletet hoz létre a tevékenység naplójába, ugyanúgy, mint a naplózási effektus. Az aktiváláskor az az erőforrás, amely megfelel az **IF** feltételnek, nem megfelelőként megjelölt erőforrás.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists tulajdonságai

A AuditIfNotExists-effektusok **részletek** tulajdonsága az összes olyan altulajdonsággal rendelkezik, amely meghatározza a kapcsolódó erőforrásokat.

- **Típus** [kötelező]
  - Meghatározza a kapcsolódó erőforrás típusát.
  - Ha a **details. Type** egy erőforrástípus az **IF** feltétel erőforrása alatt, a szabályzat a kiértékelt erőforrás hatókörén belül lekérdezi az ilyen **típusú** erőforrásokat. Ellenkező esetben a házirend-lekérdezések a kiértékelt erőforrással megegyező erőforráscsoport alatt vannak.
- **Név** (nem kötelező)
  - Megadja az egyező erőforrás pontos nevét, és a megadott típus összes erőforrása helyett egy adott erőforrás beolvasását okozza.
  - Ha a feltétel értéke **if. Field. Type** , **majd. details. Type** egyezés, akkor a **név** _szükséges_ lesz, és `[field('name')]`nak kell lennie. A [naplózási](#audit) hatást azonban figyelembe kell venni.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi a kapcsolódó erőforrás megfeleltetését egy másik erőforráscsoporthoz.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - Az alapértelmezett érték a **IF** Condition erőforrás erőforráscsoport.
- **ExistenceScope** (nem kötelező)
  - Az engedélyezett értékek az _előfizetések_ és a _ResourceGroup_.
  - Azt a hatókört állítja be, ahová a kapcsolódó erőforrást be kell olvasni.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - A _ResourceGroup_esetében korlátozni kellene az **IF** feltétel erőforrásának erőforráscsoport vagy a **ResourceGroupName**megadott erőforráscsoport esetében.
  - Az _előfizetés_a kapcsolódó erőforrás teljes előfizetését kérdezi le.
  - Az alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, az összes kapcsolódó erőforrás **megfelel a** hatásnak, és nem indítja el a naplózást.
  - Ugyanazt a nyelvet használja, mint az **IF** feltétel házirend-szabálya, de a rendszer minden kapcsolódó erőforrásra külön kiértékeli.
  - Ha bármely egyező kapcsolódó erőforrás igaz értéket ad vissza, a hatás teljesül, és nem indítja el a naplózást.
  - A [Field ()] paranccsal az egyenértékűséget az **IF** feltétel értékeivel is megtekintheti.
  - Például arra használható, hogy ellenőrizze, hogy a szülő erőforrás ( **Ha** a feltétel) ugyanabban az erőforrásban van-e, mint a megfelelő kapcsolódó erőforrás.

### <a name="auditifnotexists-example"></a>AuditIfNotExists példa

Példa: kiértékeli a Virtual Machines annak megállapítására, hogy a kártevők bővítménye létezik-e, majd naplózza a hiányzó állapotot.

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
> A [beágyazott sablonok](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) a **deployIfNotExists**-mel támogatottak, de a [csatolt sablonok](../../../azure-resource-manager/resource-group-linked-templates.md) jelenleg nem támogatottak.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists kiértékelése

A DeployIfNotExists azt követően fut, hogy egy erőforrás-szolgáltató kezelt egy erőforrás-létrehozási vagy frissítési kérelmet, és sikeres állapotkódot adott vissza. A sablonok központi telepítése akkor történik, ha nincsenek kapcsolódó erőforrások, vagy ha a **ExistenceCondition** által meghatározott erőforrások nem értékelik az igaz értéket.

A kiértékelési ciklusokban az erőforrásoknak megfelelő DeployIfNotExists-effektussal rendelkező házirend-definíciók nem megfelelőként vannak megjelölve, de az adott erőforráson nem végeznek műveleteket.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists tulajdonságai

A DeployIfNotExists-effektus **részletek** tulajdonsága az összes olyan altulajdonsággal rendelkezik, amely meghatározza a kapcsolódó erőforrásokat, valamint a sablon központi telepítését.

- **Típus** [kötelező]
  - Meghatározza a kapcsolódó erőforrás típusát.
  - A parancs elindítja az erőforrás lekérését az **IF** feltétel erőforrása alatt, majd az adott erőforráscsoporthoz tartozó, az **IF** feltétel erőforrással rendelkező lekérdezéseket.
- **Név** (nem kötelező)
  - Megadja az egyező erőforrás pontos nevét, és a megadott típus összes erőforrása helyett egy adott erőforrás beolvasását okozza.
  - Ha a feltétel értéke **if. Field. Type** , **majd. details. Type** egyezés, akkor a **név** _szükséges_ lesz, és `[field('name')]`nak kell lennie.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi a kapcsolódó erőforrás megfeleltetését egy másik erőforráscsoporthoz.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - Az alapértelmezett érték a **IF** Condition erőforrás erőforráscsoport.
  - Ha egy sablon központi telepítését hajtja végre, a rendszer az adott érték erőforráscsoporthoz helyezi üzembe.
- **ExistenceScope** (nem kötelező)
  - Az engedélyezett értékek az _előfizetések_ és a _ResourceGroup_.
  - Azt a hatókört állítja be, ahová a kapcsolódó erőforrást be kell olvasni.
  - Nem alkalmazható, ha a **típus** olyan erőforrás, amely az **IF** feltétel erőforrása alá esik.
  - A _ResourceGroup_esetében korlátozni kellene az **IF** feltétel erőforrásának erőforráscsoport vagy a **ResourceGroupName**megadott erőforráscsoport esetében.
  - Az _előfizetés_a kapcsolódó erőforrás teljes előfizetését kérdezi le.
  - Az alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, az összes kapcsolódó erőforrás **megfelel a** hatásnak, és nem indítja el a telepítést.
  - Ugyanazt a nyelvet használja, mint az **IF** feltétel házirend-szabálya, de a rendszer minden kapcsolódó erőforrásra külön kiértékeli.
  - Ha bármely egyező kapcsolódó erőforrás igaz értéket ad vissza, a hatás teljesül, és nem indítja el a telepítést.
  - A [Field ()] paranccsal az egyenértékűséget az **IF** feltétel értékeivel is megtekintheti.
  - Például arra használható, hogy ellenőrizze, hogy a szülő erőforrás ( **Ha** a feltétel) ugyanabban az erőforrásban van-e, mint a megfelelő kapcsolódó erőforrás.
- **roleDefinitionIds** [kötelező]
  - Ennek a tulajdonságnak tartalmaznia kell egy olyan karakterlánc-tömböt, amely megfelel az előfizetés által elérhető szerepköralapú hozzáférés-vezérlési szerepkör-AZONOSÍTÓnak. További információ: [szervizelés – házirend-definíció konfigurálása](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (nem kötelező)
  - Az engedélyezett értékek az _előfizetések_ és a _ResourceGroup_.
  - Az aktiválni kívánt központi telepítés típusának beállítása. Az _előfizetés_ az [előfizetési szinten](../../../azure-resource-manager/deploy-to-subscription.md)üzemelő telepítést jelzi, a _ResourceGroup_ pedig egy erőforráscsoporthoz történő központi telepítést jelez.
  - Az előfizetések szintjén üzemelő példányok használatakor meg kell adni egy _Location_ tulajdonságot a _központi telepítésben_ .
  - Az alapértelmezett érték a _ResourceGroup_.
- **Üzembe helyezés** [kötelező]
  - Ennek a tulajdonságnak szerepelnie kell a sablon teljes telepítésének, mivel a `Microsoft.Resources/deployments` PUT API-nak lesz átadva. További információ: [központi telepítések REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > A **központi telepítési** tulajdonságon belüli összes függvény a sablon összetevőiként lesz kiértékelve, nem pedig a házirendnek. A kivétel a **Paraméterek** tulajdonság, amely értékeket továbbít a szabályzatból a sablonba. Ennek az értéknek a megadásához a sablon paraméter neve alatt található **értéket** kell használni (lásd: _fullDbName_ a DeployIfNotExists példában).

### <a name="deployifnotexists-example"></a>DeployIfNotExists példa

Példa: kiértékeli SQL Server adatbázisokat annak megállapítására, hogy engedélyezve van-e a transparentDataEncryption. Ha nem, akkor a rendszer végrehajtja az engedélyezéshez szükséges központi telepítést.

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

## <a name="layering-policies"></a>Rétegbeli házirendek

Egy erőforrásra több hozzárendelés is hatással lehet. Ezek a hozzárendelések lehetnek ugyanazon a hatókörön vagy eltérő hatókörökben. Ezen hozzárendelések mindegyike valószínűleg más effektust is meghatároz. Az egyes szabályzatok feltételeit és hatásait egymástól függetlenül ki kell értékelni. Példa:

- 1\. szabályzat
  - Erőforrás helyének korlátozása a következőre: "westus"
  - Az A előfizetéshez rendelve
  - Megtagadási hatás
- 2\. szabályzat
  - Erőforrás helyének korlátozása a következőre: "eastus"
  - Az A előfizetéshez tartozó B erőforráscsoport számára hozzárendelve
  - Naplózási hatás
  
Ez a beállítás a következő eredményt eredményezheti:

- A (z) "eastus" B erőforráscsoporthoz tartozó összes erőforrás megfelel a 2. házirendnek, és nem felel meg az 1. házirendnek
- A B erőforráscsoport egyik erőforrása, amely nem a "eastus", nem felel meg a 2. házirendnek, és nem felel meg az 1. házirendnek, ha nem a "westus"
- A (z) "westus" nem a következő előfizetésben lévő új erőforrást a szabályzat 1.
- A (z) "westus" nevű és "B" erőforráscsoport összes új erőforrása létrehozva és nem megfelelő a (z) 2. házirendben

Ha az 1. és a 2. házirend mindkét esetben megtagadta a Megtagadás hatását, a helyzet a következőre változik:

- A B erőforráscsoport egyik erőforrása, amely nem a "eastus", nem felel meg a 2. házirendnek
- A B erőforráscsoport egyik erőforrása, amely nem a "westus", nem felel meg az 1. szabályzatnak
- A (z) "westus" nem a következő előfizetésben lévő új erőforrást a szabályzat 1.
- A (z) "A" előfizetéshez tartozó B erőforráscsoport új erőforrásai megtagadva

Minden hozzárendelés külön kiértékelésre kerül. Ilyen esetben nincs lehetőség arra, hogy egy erőforrás a hatókörben lévő eltérések miatt nem csúszik meg. A rétegbeli házirendek vagy a szabályzatok átfedésének nettó eredménye az **összesítő legszigorúbbnak**minősül. Ha például az 1. és a 2. szabályzat is megtagadási hatást gyakorolt, az átfedésben lévő és ütköző házirendek letiltják az erőforrásokat. Ha továbbra is szüksége van az erőforrás létrehozására a célként megadott hatókörben, tekintse át az egyes hozzárendelések kizárásait, hogy a megfelelő házirendek a megfelelő hatókörökre legyenek hatással.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](definition-structure.md) szakaszt.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
