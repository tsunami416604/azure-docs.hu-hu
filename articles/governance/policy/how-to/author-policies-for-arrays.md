---
title: Tömbtulajdonságok házirendjeinek készítése erőforrásokon
description: Ismerje meg, hogyan dolgozhat tömbparaméterekkel és tömbnyelvi kifejezésekkel, értékelje ki a [*] aliast, és fűzhet hozzá elemeket az Azure Policy definíciós szabályaihoz.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280663"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Az Azure-erőforrások tömbtulajdonságainak házirendjeinek szerkesztése

Az Azure Resource Manager tulajdonságai általában karakterláncok és logikai értékként vannak definiálva. Ha egy-a-többhöz kapcsolat létezik, az összetett tulajdonságok at tömbként definiálják. Az Azure-szabályzatban a tömbök et többféleképpen használják:

- A [definíciós paraméter](../concepts/definition-structure.md#parameters)típusa , amely több lehetőséget biztosít
- A [házirendszabály](../concepts/definition-structure.md#policy-rule) egy része, **notIn** amely a feltételeket használja **a**
- Egy házirendszabály része, amely [ \[ \* \] ](../concepts/definition-structure.md#understanding-the--alias) kiértékeli a kiértékelt aliast:
  - Például a **Nincs,** **a Vagy**vagy az **Összes**
  - Összetett forgatókönyvek **a számmal**
- Meglévő tömb lecseréléséhez vagy hozzáadásához a [hozzáfűző effektusban](../concepts/effects.md#append)

Ez a cikk az Azure Policy minden egyes használatát ismerteti, és több példadefiníciót tartalmaz.

## <a name="parameter-arrays"></a>Paramétertömbök

### <a name="define-a-parameter-array"></a>Paramétertömb definiálása

Egy paraméter tömbként történő definiálása lehetővé teszi a házirend rugalmasságát, ha egynél több értékre van szükség.
Ez a házirend-definíció lehetővé teszi, hogy az **allowedLocations** paraméter és az _alapértelmezett érték az eastus2_paraméterhez egyetlen helyet engedélyezve:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Mivel **a típus** _karakterlánc_volt, a házirend hozzárendelésekénknél csak egy érték állítható be. Ha ez a szabályzat van hozzárendelve, a hatókörben lévő erőforrások csak egyetlen Azure-régióban engedélyezettek. A legtöbb házirend-definíciónak lehetővé kell tennie a jóváhagyott lehetőségek listáját, például _az eastus2_, _eastus_és _westus2_engedélyezését.

Ha a házirend-definíciót több beállítás engedélyezéséhez szeretné létrehozni, használja a _array_ **tömbtípusát.** Ugyanez a házirend a következőképpen írható át:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> A házirend-definíció mentése után a paraméter **típustulajdonsága** nem módosítható.

Ez az új paraméterdefiníció egynél több értéket vesz igénybe a házirend-hozzárendelés során. Az **allowedValues** tömbtulajdonság definiálásával a hozzárendelés során elérhető értékek tovább korlátozódnak az előre meghatározott választási lehetőségek listájára. A **megengedett értékek** használata nem kötelező.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Értékek áthárítása paramétertömbbe hozzárendelés közben

Amikor a szabályzatot az Azure Portalon keresztül rendeli hozzá, egy **típusú** _tömb_ paraméter jelenik meg egyetlen szövegdobozként. A tipp azt mondja: "Használja; az értékek elkülönítéséhez. (pl. London; New York)". Az _eastus2_, _eastus_és _westus2_ megengedett helyértékeinek paraméternek való átadására használja a következő karakterláncot:

`eastus2;eastus;westus2`

A paraméterérték formátuma eltérő az Azure CLI, az Azure PowerShell vagy a REST API használatakor. Az értékek egy JSON-karakterláncon keresztül kerülnek át, amely a paraméter nevét is tartalmazza.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Ha ezt a karakterláncot minden SDK-val használni szeretné, kövesse a következő parancsokat:

- Azure CLI: Command [az policy hozzárendelés létrehozása](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) paraméter **params**
- Azure PowerShell: [Új-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) parancsmag **policyparameter** paraméterrel
- REST API: A _PUT_ [create](/rest/api/resources/policyassignments/create) művelet részeként a kérelem törzs, mint a **properties.parameters** tulajdonság értéke

## <a name="policy-rules-and-arrays"></a>Házirendszabályok és -tömbök

### <a name="array-conditions"></a>Tömbfeltételek

A házirendszabály [azon feltételei,](../concepts/definition-structure.md#conditions) amelyekkel egy
**tömbtípusú** paraméter használható, a- `in` és `notIn`a. _array_ Vegyük példaként a `equals` következő házirend-definíciót a feltétellel:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

A szabályzatdefiníció nak az Azure Portalon keresztül történő létrehozása olyan hibát eredményez, mint például ez a hibaüzenet:

- "A(z) "{GUID}" házirend et érvényesítési hibák miatt nem lehet paraméterezve. Ellenőrizze, hogy a házirend paraméterei megfelelően vannak-e definiálva. A "[parameters('allowedLocations')]" nyelvi kifejezés belső kivétele "Tömb" típusú, a várt típus "Karakterlánc".".

A feltétel várható `equals` **típusa** a _karakterlánc_. Mivel **az allowedLocations** _típustömbként_van definiálva, a házirendmotor kiértékeli a nyelvi kifejezést, és eldobja a hibát. **type** A `in` és `notIn` a feltétel, a házirend-motor elvárja a **típus** _tömb_ a nyelvi kifejezésben. A hibaüzenet feloldásához `equals` módosítsa `in` a `notIn`vagy a .

### <a name="evaluating-the--alias"></a>A[*] alias kiértékelése

A ** \[ \* ** nevükhöz csatolt aliasok azt jelzik, hogy a **típus** _tömb._ Ahelyett, hogy kiértékelen a ** \[ \* ** teljes tömb értékét, lehetővé teszi a tömb egyes elemeinek egyenkénti kiértékelését, logikai ÉS-vel a kettő között. Három szabványos forgatókönyv van, ha ez a cikkenkénti kiértékelés a következőkben hasznos: _Nincs,_ _Bármely_vagy _Minden_ elem egyezik. Összetett esetekben használja [a count .](../concepts/definition-structure.md#count)

A házirendmotor csak akkor indítja el a **hatást,** ha az **if** szabály igazként értékeli ki. **then**
Ez a tény fontos megérteni összefüggésben az utat ** \[ \* ** értékeli minden egyes eleme a tömb.

A példa házirendszabály az alábbi forgatókönyvtáblázathoz:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Az **ipRules** tömb az alábbi forgatókönyvtáblázatban a következő:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Az alábbi példákban `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`cserélje ki a helyére a következőt:

A következő eredmények a feltétel és a példaházirend-szabály és a fenti értékek tömbjének kombinációjából erednek:

|Állapot |Eredmény | Forgatókönyv |Magyarázat |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Semmit |Nincs egyezés |Egy tömbelem értéke hamis (127.0.0.1 != 127.0.0.1) és egy igaz (127.0.0.1 != 192.168.1.1), így a **notEquals** feltétel _hamis,_ és a hatás nem aktiválódik. |
|`{<field>,"notEquals":"10.0.4.1"}` |Politikai hatás |Nincs egyezés |Mindkét tömbelem igazként értékeli ki (10.0.4.1 != 127.0.0.1 és 10.0.4.1 != 192.168.1.1), így a **notEquals** feltétel _igaz,_ és a hatás aktiválódik. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Politikai hatás |Egy vagy több egyezés |Egy tömbelem értéke hamis (127.0.0.1 != 127.0.0.1) és egy igaz (127.0.0.1 != 192.168.1.1), így a **notEquals** feltétel _hamis_. A logikai operátor igaz **(nem** _hamis)_ értéket ad ki, így a hatás aktiválódik. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Semmit |Egy vagy több egyezés |Mindkét tömbelem igazként értékelhető (10.0.4.1 != 127.0.0.1 és 10.0.4.1 != 192.168.1.1), így a **notEquals** feltétel _igaz_. A logikai operátor kiértékeli a hamis **(nem** _igaz_), így a hatás nem aktiválódik. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Politikai hatás |Nem minden egyezés |Egy tömbelem igazként (127.0.0.1 == 127.0.0.1) és egy hamis (127.0.0.1 == 192.168.1.1), így az **Egyenlő** feltétel _hamis_. A logikai operátor igaz **(nem** _hamis)_ értéket ad ki, így a hatás aktiválódik. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Politikai hatás |Nem minden egyezés |Mindkét tömbelem értéke hamis (10.0.4.1 == 127.0.0.1 és 10.0.4.1 == 192.168.1.1), így az **Egyenlő** feltétel _hamis_. A logikai operátor igaz **(nem** _hamis)_ értéket ad ki, így a hatás aktiválódik. |
|`{<field>,"Equals":"127.0.0.1"}` |Semmit |Minden egyezés |Egy tömbelem igazként (127.0.0.1 == 127.0.0.1) és egy hamis (127.0.0.1 == 192.168.1.1), így az **Egyenlő** feltétel _hamis,_ és a hatás nem aktiválódik. |
|`{<field>,"Equals":"10.0.4.1"}` |Semmit |Minden egyezés |Mindkét tömbelem kiértékelése hamis (10.0.4.1 == 127.0.0.1 és 10.0.4.1 == 192.168.1.1), így az **Equals** feltétel _hamis,_ és a hatás nem aktiválódik. |

## <a name="the-append-effect-and-arrays"></a>A hozzáfűző hatás és a tömbök

A [hozzáfűző hatás](../concepts/effects.md#append) eltérően viselkedik attól függően, ** \[ \* ** hogy a **details.field** alias-e vagy sem.

- Ha nem ** \[ \* ** alias, a hozzáfűzés a teljes tömböt az **értéktulajdonságra** cseréli.
- Amikor ** \[ \* ** egy alias, hozzáfűzés hozzáadja az **érték** tulajdonságot a meglévő tömbhöz, vagy létrehozza az új tömböt

További információt a [hozzáfűzési példákban](../concepts/effects.md#append-examples)talál.

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](programmatically-create.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)
