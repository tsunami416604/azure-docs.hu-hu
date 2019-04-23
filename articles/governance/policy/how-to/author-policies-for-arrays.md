---
title: Az Azure-erőforrások tárolótömb tulajdonságainak Szerző házirendek
description: Ismerje meg a tömb paramétereinek létrehozása, Sablonnyelv-kifejezéseinek tömb szabályokat hozhat létre, a [*] alias kiértékelése és elemek hozzáfűzése egy meglévő Azure Policy definition szabályok tömböt.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 38cf6decb8e61768faa9680058f6366e1550ba40
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793272"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Az Azure-erőforrások tárolótömb tulajdonságainak Szerző házirendek

Az Azure Resource Manager-tulajdonságok gyakran vannak meghatározva karakterláncok és logikai értékekből. Ha egy-a-többhöz kapcsolat áll fenn, összetett tulajdonságok inkább vannak meghatározva tömbök. Az Azure Policy tömbök használhatók a különféle módokon:

- Milyen típusú egy [definíciós paraméter](../concepts/definition-structure.md#parameters), több-beállításokat biztosítson
- Része egy [felügyeletiházirend-szabálya](../concepts/definition-structure.md#policy-rule) feltételek használatával **a** vagy **notIn**
- Része egy házirend szabályt, amely kiértékeli a [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) meghatározott forgatókönyvek például kiértékelheti, hogy **nincs**, **bármely**, vagy  **Az összes**
- Az a [fűzze hozzá a hatás](../concepts/effects.md#append) cserélje le, vagy egy meglévő tömb hozzáadása

Ez a cikk ismerteti az egyes használja az Azure Policy, és néhány példa definícióit tartalmazza.

## <a name="parameter-arrays"></a>A paraméter-tömbök

### <a name="define-a-parameter-array"></a>A paramétertömböt definiálása

Egy paraméter meghatározása tömbként lehetővé teszi a szabályzat rugalmasságot, ha egynél több érték van szükség.
Ez a szabályzat-definíció lehetővé teszi, hogy a paraméter, minden egy helyen **allowedLocations** és az alapértelmezett _eastus2_:

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

Mint **típus** volt _karakterlánc_, csak egy értéket is állítható be, ha a szabályzat hozzárendelése. Ez a szabályzat van hozzárendelve, ha egy adott Azure-régión belül csak engedélyezett a hatókörbe eső erőforrásokhoz. A legtöbb szabályzatok definíciói által jóváhagyott beállítások, például engedélyezheti a listáját engedélyezni kell az _eastus2_, _eastus_, és _westus2_.

Több beállítás engedélyezéséhez a szabályzatdefiníció létrehozásához használja a _tömb_ **típus**. Ugyanaz a szabályzat kell írni a következő:

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
> Szabályzat-definíció a mentés után a **típus** paraméter tulajdonsága nem módosítható.

Ez új paraméter a definíció egynél több értéket vesz fel a szabályzat-hozzárendelés során. A tömb tulajdonsággal **allowedValues** definiált, a hozzárendelés során rendelkezésre álló értékek további lehetőségek az előre meghatározott értéklistából korlátozódik. Felhasználása **allowedValues** nem kötelező.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Hozzárendelés során a paramétertömböt értékeket adja át

Az Azure Portalon, egy paraméter, a házirend hozzárendelésekor **típus** _tömb_ egyetlen szövegmezőként jelenik meg. A mutató szerint a "használni; értékek elválasztásához. (Példa: London; New York) ". Az engedélyezett hely értékek _eastus2_, _eastus_, és _westus2_ a paramétert, használja a következő karakterláncot:

`eastus2;eastus;westus2`

A paraméter értéke formátuma különböző Azure CLI, Azure PowerShell-lel vagy a REST API használata esetén. Az értékeket továbbítja a rendszer egy JSON-karakterlánc, amely a paraméter nevét is tartalmazza.

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

Ezt a karakterláncot használni minden SDK-val, használja a következő parancsokat:

- Azure CLI: A parancs [az szabályzat-hozzárendelés létrehozására](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) paraméterrel **paraméterei**
- Azure PowerShell: A parancsmag [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) paraméterrel **PolicyParameter**
- REST API: Az a _PUT_ [létrehozása](/rest/api/resources/policyassignments/create) műveletet, mert a kérelem törzse részét értékeként a **properties.parameters** tulajdonság

## <a name="policy-rules-and-arrays"></a>Szabályok és tömbök

### <a name="array-conditions"></a>Tömb feltételek

A szabály [feltételek](../concepts/definition-structure.md#conditions) , amely egy _tömb_
**típusa** paraméter is használhatók a korlátozódik `in` és `notIn`. A következő szabályzatdefiníció feltétellel igénybe `equals` példaként:

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

A szabályzat-definíció keresztül az Azure portal érdeklődők, például a hibaüzenet hiba létrehozására tett kísérlet:

- "A szabályzatot {GUID} nem paraméterezhető érvényesítési hibák miatt. Ellenőrizze, ha szabályzatparaméterek megfelelően vannak definiálva. A belső kivétel "Sablonnyelv-kifejezés"[parameters('allowedLocations')]"eredménye"Array", típus értékelési várt típus"String""."

A várt **típus** feltétel `equals` van _karakterlánc_. Mivel **allowedLocations** típusúként van definiálva **típus** _tömb_, a házirendmotor kiértékeli a Sablonnyelv-kifejezés, és a hibát jelez. Az a `in` és `notIn` feltételt, a házirendkezelő összetevő vár az **típus** _tömb_ a nyelvi kifejezés. Ez a hibaüzenet megoldásához módosítsa `equals` egyaránt `in` vagy `notIn`.

### <a name="evaluating-the--alias"></a>A [*] alias kiértékelése

Aliasról, amelyek rendelkeznek **[\*]** nevük csatolt jelzi a **típus** van egy _tömb_. A tömb teljes értékét kiértékelése helyett **[\*]** a tömb egyes elemei kiértékelheti, hogy lehetővé teszi. Nincsenek háromféle esetben ez egy jelentéselem kiértékelése hasznos: Nincs bármely és minden.

A házirend motor eseményindítók a **érvénybe** a **majd** csak akkor, ha a **Ha** szabály igaz értékként ad vissza.
Emiatt azért fontos, hogy a módon összefüggésben **[\*]** kiértékeli a tömb minden egyes elemén.

A példa az alábbi táblázat a forgatókönyvhöz tartozó házirendszabály:

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

A **ipRules** tömb a következőképpen történik a forgatókönyv az alábbi tábla esetében:

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

Minden egyes feltétel az alábbi példában cserélje `<field>` a `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Az alábbi eredmények a következők a feltétel és a példa szabály és a meglévő a fenti értékek tömbje kombinációja eredménye:

|Állapot |Eredmény |Magyarázat |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Semmi |Egy tömbelem hamisnak (127.0.0.1! = 127.0.0.1) és a egy igaz (127.0.0.1! = 192.168.1.1), így a **notEquals** feltétel _false (hamis)_ és nem indul el attól a hatást. |
|`{<field>,"notEquals":"10.0.4.1"}` |Szabályzat hatása |Mindkét tömbelemek rekordsémáját kiértékelése igaz (10.0.4.1! = 127.0.0.1 és 10.0.4.1! = 192.168.1.1), így a **notEquals** feltétel _igaz_ és akkor indul el, a hatás. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Szabályzat hatása |Egy tömbelem kiértékeli a true (127.0.0.1 == 127.0.0.1) és a egy hamis (127.0.0.1 == 192.168.1.1), így a **egyenlő** feltétel _hamis_. A logikai operátor kiértékeli a true (**nem** _false (hamis)_), így a hatás aktiválódik. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Szabályzat hatása |Mindkét tömbelemek rekordsémáját használja inkább a false (10.0.4.1 127.0.0.1 és 10.0.4.1 == == 192.168.1.1), így a **egyenlő** feltétel _hamis_. A logikai operátor kiértékeli a true (**nem** _false (hamis)_), így a hatás aktiválódik. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Szabályzat hatása |Egy tömbelem hamisnak (127.0.0.1! = 127.0.0.1) és a egy igaz (127.0.0.1! = 192.168.1.1), így a **notEquals** feltétel _hamis_. A logikai operátor kiértékeli a true (**nem** _false (hamis)_), így a hatás aktiválódik. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Semmi |Mindkét tömbelemek rekordsémáját kiértékelése igaz (10.0.4.1! = 127.0.0.1 és 10.0.4.1! = 192.168.1.1), így a **notEquals** feltétel _igaz_. A logikai operátor hamisnak (**nem** _igaz_), ezért nem indul el attól a hatást. |
|`{<field>,"Equals":"127.0.0.1"}` |Semmi |Egy tömbelem kiértékeli a true (127.0.0.1 == 127.0.0.1) és a egy hamis (127.0.0.1 == 192.168.1.1), így a **egyenlő** feltétel _hamis_ és a hatás nem indul el attól. |
|`{<field>,"Equals":"10.0.4.1"}` |Semmi |Mindkét tömbelemek rekordsémáját kiértékelni a hamis értéket (10.0.4.1 127.0.0.1 és 10.0.4.1 == == 192.168.1.1), így a **egyenlő** feltétel _hamis_ és nem indul el attól a hatás. |

## <a name="the-append-effect-and-arrays"></a>A Hozzáfűzés hatás és a tömbök

A [fűzze hozzá a hatás](../concepts/effects.md#append) működését eltér attól függően, hogy a **details.field** van egy **[\*]** alias vagy sem.

- Ha nem egy **[\*]** alias, Hozzáfűzés váltja fel a teljes tömb a a **érték** tulajdonság
- Ha egy **[\*]** alias, Hozzáfűzés hozzáadja a **érték** tulajdonságot a meglévő tömböt, vagy hozza létre az új tömb

További információkért lásd: a [példák hozzáfűzése](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy-minták](../samples/index.md)
- Tekintse át a [szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)
- Felülvizsgálat [házirend hatások ismertetése](../concepts/effects.md)
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](programmatically-create.md)
- Ismerje meg, hogyan [javítani a nem megfelelő erőforrások](remediate-resources.md)
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../../management-groups/overview.md) ismertető részt.