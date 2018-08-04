---
title: Azure szabályzatdefiníciók struktúrája
description: 'Ismerteti, hogy a szabályzatdefiníció erőforrás az Azure Policy által létesíteni az erőforrások konvenciók a szervezetben: Ha a házirend érvényesítve van, és milyen érvénybe léptetéséhez.'
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/03/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 48a33d8ead3a68f4702638fcc8db3d99a5ad626c
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502984"
---
# <a name="azure-policy-definition-structure"></a>Azure szabályzatdefiníciók struktúrája

Az Azure Policy által használt erőforrás szabályzatdefiníció lehetővé teszi, hogy az erőforrások konvenciók a szervezet által: Ha a házirend érvényesítve van, és milyen érvénybe léptetéséhez. Egyezmények definiálásával is csökkenthetők a költségek, és további könnyen kezelheti az erőforrásokat. Megadhatja például, hogy csak bizonyos típusú virtuális gépek használata engedélyezett. Másik lehetőségként megkövetelheti, hogy az összes erőforrásnak rendelkeznie kell egy adott címkét. Összes gyermekerőforrás örökölt házirendek. Tehát ha egy szabályzatot alkalmazott egy erőforráscsoportba, alkalmazható az adott erőforráscsoportba tartozó összes erőforrást.

Az Azure Policy által használt séma itt található: [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

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
                }
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

Az összes Azure Policy minta vannak [házirend minták](json-samples.md).

## <a name="mode"></a>Mód

A **mód** meghatározza, hogy mely erőforrástípusokat szabályzat lesz kiértékelve. A támogatott módok a következők:

- `all`: erőforráscsoportok és az összes erőforrástípus kiértékelése
- `indexed`: erőforrástípusok, amelyek támogatják a címkék és a hely csak kiértékelése

Azt javasoljuk, hogy állítsa **mód** való `all` a legtöbb esetben. A portál használatával létrehozott összes szabályzat-definíciókat a `all` mód. Ha a PowerShell vagy az Azure CLI-vel, megadhatja a **mód** paraméter manuálisan. Ha a szabályzat-definíció nem tartalmaz egy **mód** érték azt az alapértelmezett érték `all` az Azure PowerShell, az `null` az Azure CLI-ben, amely megegyezik `indexed`, a visszamenőleges kompatibilitás.

`indexed` kell használni, amikor a címkék vagy a házirendek létrehozásával, amely kényszeríti. Ez nem szükséges, de megakadályozza, hogy címkéket és a helyek való jelenik meg, nem kompatibilis a megfelelőségi eredmények nem támogató erőforrások. Ez az egyetlen kivétel a **erőforráscsoportok**. Kell beállítania a házirendekben, amelyek a helyet vagy egy erőforráscsoportba tartozó címkéket kényszerítése próbál **mód** való `all` és a kifejezetten a cél a `Microsoft.Resources/subscriptions/resourceGroup` típusa. Egy vonatkozó példáért lásd: [erőforráscímkék csoport kényszerítése](scripts/enforce-tag-rg.md).

## <a name="parameters"></a>Paraméterek

Paraméterek segítségével leegyszerűsítik a szabályzatok kezelését szabályzatdefiníciók számának csökkentésével. Gondoljon a mezőket az űrlap – például a paraméterek `name`, `address`, `city`, `state`. Ezek a paraméterek változnak, azonban azok értékeit módosíthatja az űrlap egyes kitöltése alapján. Paraméterek ugyanúgy működnek, szabályzatok készítése során. Paraméterekkel együtt egy szabályzat-definícióban, újból felhasználhatja az adott házirendnek a különböző helyzetekhez különböző értékek alapján.

Ha például meghatározhat egy házirendet az erőforrás-tulajdonság korlátozni a helyeken, ahol erőforrásokat is üzembe helyezhetők. Ebben az esetben a következő paramétereket szeretne deklarálja a házirend létrehozásakor:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

A paraméter típusa karakterláncként vagy a tömb lehet. A metaadat-tulajdonságot felhasználóbarát információk megjelenítésére szolgál eszközökkel, mint például az Azure Portalon.

A metaadat-tulajdonságot belül használhatja **strongType** biztosít az Azure Portalon lehetőségek többszörös kijelöléses listája.  Megengedett értékek a **strongType** jelenleg tartalmaz:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

A szabályzatbeli szabályban hivatkozási paraméterek a következő szintaxissal:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Definíció helye

Egy kezdeményezést vagy szabályzat-definíció létrehozásakor fontos, hogy Ön adja meg a definíció helye.

A definíció helye határozza meg, hogy a hatókör, amelyhez az kezdeményezést vagy szabályzat-definíció is hozzárendelhető. A hely egy felügyeleti csoportot vagy egy előfizetés adható meg.

> [!NOTE]
> Ha a alkalmazni ezt a szabályzatdefiníciót több előfizetést is tervez, a hely egy felügyeleti csoportot, amely tartalmazza az előfizetések a kezdeményezés, vagy a szabályzatot rendeli kell lennie.

## <a name="display-name-and-description"></a>Megjelenítendő név és leírás

Használhat **displayName** és **leírás** azonosíthatja a szabályzat-definíció, és kontextusának esetén.

## <a name="policy-rule"></a>Szabályzatbeli szabály

A szabály áll **Ha** és **majd** blokkokat. Az a **Ha** letiltása, megadhat egy vagy több feltételt, adja meg, ha a házirend érvényesítve van-e. Ezek a feltételek, pontosan meghatározni a forgatókönyv egy házirend logikai operátorokat alkalmazhat.

Az a **majd** letiltása, definiálhat a hatás, amely akkor fordul elő, amikor a **Ha** feltételek teljesülnek.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
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

A feltétel-e egy **mező** megfelel bizonyos feltételeknek. A támogatott feltételek a következők:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

Használatakor a **például** és **notLike** feltételek, megadhat egy helyettesítő karaktert tartalmazó `*` értéke.
Az érték nem tartalmazhat egynél több helyettesítő `*`.

Használatakor a **megfelelő** és **notMatch** adja meg a feltételeket, `#` , amelyek egy számjegyet `?` betűvel, és minden más karaktert a tényleges karakter helyettesítéséhez. Példák: [lehetővé teszik több név minták](scripts/allow-multiple-name-patterns.md).

### <a name="fields"></a>Mezők

Feltételek alkotta mezőkkel. Egy mezőt az erőforrás-kérések forgalma, amely az erőforrás állapotának leírására szolgáló tulajdonság jelöli.  

A következő mezők támogatottak:

- `name`
- `fullName`
  - Az erőforrás teljes nevét adja vissza. Erőforrás teljes név bármelyik szülő erőforrás nevét (például "myServer/myDatabase") által kiegészített az erőforrás nevét.
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - Ahol **\<tagName\>** feltételét ellenőrzése a címke neve.
  - Példa: `tags.CostCenter` ahol **CostCenter** a címke neve.
- `tags[<tagName>]`
  - A szögletes zárójelet szintaxist tartalmaznak a címkenevek pontot tartalmazó támogatja.
  - Ahol **\<tagName\>** feltételét ellenőrzése a címke neve.
  - Példa: `tags.[Acct.CostCenter]` ahol **Acct.CostCenter** a címke neve.
- vlastnost aliasok -, lásd: [aliasok](#aliases).

### <a name="alternative-accessors"></a>Alternatív leíró.

**A mező** a házirend-szabályokban használt elsődleges leírójához. Közvetlenül átvizsgálja a kiértékelt erőforrás. Azonban a házirend támogatja-e egy másik hozzáférő **forrás**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Forrás** csak támogatja a több értéket **művelet**. A művelet az engedélyezési művelet a kérelem a kiértékelt adja vissza. Engedélyezési műveletek engedélyezési szakaszában érhetők el a [tevékenységnapló](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Esetén a szabályzat az a célja, a háttérben a meglévő erőforrások, **művelet** , egy `/write` engedélyezési művelet az erőforrás-típus.

### <a name="effect"></a>Következmény

A házirend hatása a következő típusú támogatja:

- **Megtagadási**: eseményt hoz létre a felügyeleti napló és a kérelem meghiúsul
- **Naplózási**: hoz létre egy figyelmeztető esemény auditálási naplóba kerülnek, de a kérelem végrehajtása nem sikerül
- **Hozzáfűzés**: a meghatározott készletével mezőket ad hozzá a kérelem
- **AuditIfNotExists**: lehetővé teszi a naplózást, ha egy erőforrás nem létezik.
- **DeployIfNotExists**: üzembe helyezi egy erőforrást, ha azt nem létezik. Ennek a hatásnak jelenleg csak támogatott beépített szabályzatokon keresztül.

A **hozzáfűzése**, meg kell adnia a következő adatokat:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Az érték lehet egy karakterlánc- vagy JSON-formátumú objektum.

A **AuditIfNotExists** és **DeployIfNotExists** is létezik-e a kapcsolódó erőforráshoz értékeli és alkalmazza a szabályt, és a egy megfelelő hatása, ha adott erőforrás nem létezik. Például megkövetelheti, hogy a network watchert az összes virtuális hálózat üzemel.
Példa a naplózást, ha egy virtuálisgép-bővítmény nincs telepítve, tekintse meg a [naplózása nem létezik olyan bővítmény](scripts/audit-ext-not-exist.md).

Minden egyes hatás, értékelési, tulajdonságokat és példákat sorrendje a részleteket lásd: [ismertetése házirend hatások](policy-effects.md).

## <a name="aliases"></a>Aliasok

Aliasok tulajdonság használatával az erőforrástípushoz konkrét tulajdonságok eléréséhez. Aliasok korlátozhatja, hogy milyen értékeket vagy feltételek használata engedélyezett az erőforrás-tulajdonságok engedélyezése. Minden egyes alias képez le egy adott erőforrás típusát különböző API-verzióit szereplő elérési utakat. Szabályzat-kiértékelés során a házirendmotor lekérdezi, hogy API-verzió tulajdonság elérési útját.

Az aliasok listája folyamatosan bővül. Fedezze fel, milyen aliasok az Azure Policy által jelenleg támogatott, használja a következő módszerek egyikét:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  $azContext = Get-AzureRmContext
  $azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
  $profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
  $token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
  $authHeader = @{
      'Content-Type'='application/json'
      'Authorization'='Bearer ' + $token.AccessToken
  }

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API-t / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

## <a name="initiatives"></a>Kezdeményezések

Kezdeményezések lehetővé teszi több kapcsolódó szabályzatdefiníciót, mert dolgozik egy csoport egyetlen elemként hozzárendelések és a felügyeleti egyszerűsítése érdekében. Ha például egyetlen kezdeményezés címkézési minden kapcsolódó szabályzatdefiníciók csoportosíthatók. Ahelyett, hogy az egyes házirendek külön-külön rendeli, a kezdeményezés vonatkoznak.

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

- További példák [Az Azure Policy sablonjai](json-samples.md) oldalon.
