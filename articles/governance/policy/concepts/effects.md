---
title: Az Azure Policy hatások ismertetése
description: Azure szabályzat-definíció rendelkezik, amelyek meghatározzák, hogyan megfelelőségét, és jelentett különböző hatásokkal.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 2bed2f52f29d5c97ab576fae73498b60fb7ecc30
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079800"
---
# <a name="understand-policy-effects"></a>A szabályzat hatásainak megismerése

Minden egyes szabályzatdefiníciót az Azure Policy egy egyetlen hatása. A hatás határozza meg, mi történik, ha a szabály kiértékelése történik az egyeztetéshez. A hatás eltérően viselkednek, ha azok egy új erőforrást, egy frissíteni az erőforrás vagy egy meglévő erőforrást.

Jelenleg a szabályzat-definíció által támogatott hat hatások:

- Hozzáfűzés
- Naplózás
- AuditIfNotExists
- Megtagadás
- DeployIfNotExists
- Letiltva

## <a name="order-of-evaluation"></a>Kiértékelési sorrend

Kérelmek létrehozni vagy frissíteni egy erőforrást az Azure Resource Manager először házirend szerint értékeli. A szabályzat létrehoz egy listát az összes, az erőforrásra alkalmazni kívánt és összeveti az erőforrás minden definíció ellen. Házirend dolgozza fel a hatások számos előtt a kérés átadja a megfelelő erőforrás-szolgáltatónál. Ami felesleges feldolgozási erőforrás-szolgáltató által ezzel megakadályozza, ha egy erőforrás nem felel meg a házirend tervezett cégirányítási vezérlőket.

- **Letiltott** határozza meg, ha a szabály kell kiértékelni, először be van jelölve.
- **Hozzáfűzés** Ezután kiértékeli. Azóta hozzáfűzése módosíthatta a kérést, módosítását, a hozzáfűző megelőzhetik a naplózási vagy a hatás megtagadása elindítása.
- **Megtagadási** Ezután kiértékeli. Kiértékelésével megtagadása naplózás, mielőtt egy nemkívánatos erőforrás dupla naplózása a rendszer letiltja.
- **Naplózási** Ezután kiértékeli a kérést az erőforrás-szolgáltató fog előtt.

Miután az erőforrás-szolgáltató, sikerkódot küld vissza **AuditIfNotExists** és **DeployIfNotExists** értékeli annak megállapításához, hogy további megfelelőségi naplózás vagy a művelet szükséges.

## <a name="disabled"></a>Letiltva

Ez a hatás tesztelési helyzetek, vagy ha a szabályzatdefiníció rendelkezik paraméterezni a hatás hasznos. Ezt a rugalmasságot lehetővé teszi egy egyetlen hozzárendelési helyett letiltása a szabályzat-hozzárendelések mindegyikét letiltása.

## <a name="append"></a>Hozzáfűzés

Hozzáfűzés további mezőket hozzáadni a kért erőforrás létrehozása vagy módosítása során használatos. Gyakori például címkék hozzáadása erőforrások, például a costCenter, vagy egy tárolási erőforrás IP-címek megadása engedélyezett.

### <a name="append-evaluation"></a>Értékelés hozzáfűzése

Hozzáfűzés kiértékeli, mielőtt a kérést egy erőforrás-szolgáltató feldolgozza a létrehozása vagy egy adott erőforrás frissítése során. Fűzze hozzá mezőket ad hozzá az erőforrás során a **Ha** a szabály a feltétel teljesül. Ha Hozzáfűzés hatása lenne bírálja felül az eredeti kérelmet egy másik érték egy értéket, egy megtagadási hatást funkcionál, és elutasítja a kérelmet.

Ha egy szabályzatdefiníciót a Hozzáfűzés hatással egy kiértékelési ciklusa részeként fut, azt nem módosítások már meglévő erőforrásokat. Ehelyett jelöli meg minden olyan erőforrást, amely megfelel a **Ha** feltétel nem megfelelő.

### <a name="append-properties"></a>Tulajdonságok hozzáfűzése

Csak akkor Hozzáfűzés hatással van egy **részletek** tömb, amely szükséges. Mint **részletek** egy tömb, egyetlen is igénybe vehet **mező/érték** pár vagy többszöröseként jelenik meg. Tekintse meg [szabályzatdefiníciók struktúrája](definition-structure.md#fields) elfogadható mezők listáját.

### <a name="append-examples"></a>Példák hozzáfűzése

1. példa: Egyetlen **mező/érték** pár hozzáfűzni egy címkét.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

2. példa: Két **mező/érték** párok fűzze hozzá a címkéket egy készletét.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

3. példa: Egyetlen **mező/érték** használatával párosítsa az [alias](definition-structure.md#aliases) -tömbbel rendelkező **érték** IP-szabályok beállítása a storage-fiók.

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

## <a name="deny"></a>Megtagadás

Megtagadási szolgál, hogy egy erőforrás-kérelmet, amely nem felel meg a szabályzat-definíció segítségével meghatározott előírásoknak, és a kérelem sikertelen.

### <a name="deny-evaluation"></a>Értékelés megtagadása

Ha megtagadja a létrehozása vagy frissítése egy egyező erőforrás, megakadályozza, hogy az a kérelem erőforrás-szolgáltatónál elküldése előtt. A kérelem visszaadott egy `403 (Forbidden)`. A portálon a tiltott állapot, amely szerint a szabályzat-hozzárendelés nem sikerült elindítani az üzemelő példányon, tekinthet meg.

Meglévő erőforrások értékelése során az erőforrások, amelyek megfelelnek egy megtagadási szabályzatdefiníciót nem megfelelőként vannak megjelölve.

### <a name="deny-properties"></a>Megtagadási tulajdonságai

A megtagadási hatás nem rendelkezik használható tulajdonságokat a **majd** szabályzatdefiníció feltétel.

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

Naplózási házirend a létrehozás vagy frissítés egy adott erőforrás alatt be van jelölve utolsó hatása. A házirend ezután elküldi az erőforrás az erőforrás-szolgáltató. Naplózási egy erőforrás-kérés és a egy kiértékelési ciklusa ugyanúgy működik. Szabályzat hozzáadása egy `Microsoft.Authorization/policies/audit/action` a tevékenységnaplóban a művelet és az erőforrás nem megfelelőként jelöli meg.

### <a name="audit-properties"></a>Naplózási tulajdonságok

A naplózási hatás nem rendelkezik használható tulajdonságokat a **majd** szabályzatdefiníció feltétel.

### <a name="audit-example"></a>Naplózási példa

Példa: A naplózási hatás használatával.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists lehetővé teszi a naplózást az erőforrásokat, amelyek megfelelnek a **Ha** feltétel, azonban nem rendelkezik a megadott összetevők a **részletek** , a **majd** feltétel.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists kiértékelése

AuditIfNotExists futtatása után egy erőforrás-szolgáltató rendelkezik kezelt létrehozásának vagy frissítésének erőforrás kérelmet, és sikeres állapotkódot adott vissza. Az ellenőrzés akkor fordul elő, ha nincsenek kapcsolódó erőforrások, vagy ha az erőforrások által meghatározott **ExistenceCondition** nem igaz értéked. Szabályzat hozzáadása egy `Microsoft.Authorization/policies/audit/action` művelet a tevékenységnek jelentkezzen ugyanúgy, mint a naplózási hatást. Adatvezérelt, az erőforrást, amely elégedett az eredménnyel a **Ha** feltétel, hogy az erőforrás, amely a nem megfelelő van megjelölve.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists tulajdonságai

A **részletek** a altulajdonságokat, amelyek meghatározzák a kapcsolódó erőforrásokat a tulajdonságnak AuditIfNotExists hatásait.

- **Típus** [kötelező]
  - Határozza meg a megfelelő kapcsolódó erőforrás.
  - Úgy, hogy lekérni egy erőforrást alatt elindítja a **Ha** feltétel erőforrás, akkor az azonos erőforráscsoportjában lévő lekérdezéseket a **Ha** erőforrás feltételt.
- **Név** (nem kötelező)
  - A megfelelő erőforrás pontos nevét adja meg, és a egy adott erőforrás helyett a megadott típusú összes erőforrást beolvasni a szabályzat okoz.
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

AuditIfNotExists hasonlóan DeployIfNotExists végrehajt egy sablon telepítése a feltétel teljesülése esetén.

> [!NOTE]
> [Beágyazott sablonok](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) támogatottak **deployIfNotExists**, de [kapcsolódó sablonok](../../../azure-resource-manager/resource-group-linked-templates.md) jelenleg nem támogatottak.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists kiértékelése

DeployIfNotExists futtatása után egy erőforrás-szolgáltató rendelkezik kezelt létrehozásának vagy frissítésének erőforrás kérelmet, és sikeres állapotkódot adott vissza. A sablonalapú telepítés akkor fordul elő, ha nincsenek kapcsolódó erőforrások, vagy ha az erőforrások által meghatározott **ExistenceCondition** nem igaz értéked.

Egy értékelési ciklus során a szabályzatdefiníciók egy DeployIfNotExists hatást, amelyek megfelelnek az erőforrások nem megfelelőként lesznek megjelölve, de nem tesz az erőforráson.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists tulajdonságai

A **részletek** a DeployIfNotExists hatást tulajdonsága egyezik a kapcsolódó erőforrásokat határoz meg az összes altulajdonságot és a sablon telepítésének végrehajtásához.

- **Típus** [kötelező]
  - Határozza meg a megfelelő kapcsolódó erőforrás.
  - Úgy, hogy lekérni egy erőforrást alatt elindítja a **Ha** feltétel erőforrás, akkor az azonos erőforráscsoportjában lévő lekérdezéseket a **Ha** erőforrás feltételt.
- **Név** (nem kötelező)
  - A megfelelő erőforrás pontos nevét adja meg, és a egy adott erőforrás helyett a megadott típusú összes erőforrást beolvasni a szabályzat okoz.
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
- **Üzembe helyezés** [kötelező]
  - Ez a tulajdonság a teljes körű sablonalapú telepítés tartalmaznia kell, hogy adná a `Microsoft.Resources/deployments` PUT API. További információkért lásd: a [központi telepítések REST API-val](/rest/api/resources/deployments).

  > [!NOTE]
  > Belül a függvények a **üzembe helyezési** tulajdonság összetevőként a sablon, a szabályzat nem értékeli ki. A kivétel a **paraméterek** tulajdonságot, amely a szabályzat alól értékeket továbbítja a sablont. A **érték** ebben a szakaszban egy sablon alapján paraméternév használja ezt az értéket átadja végrehajtásához (lásd: _fullDbName_ DeployIfNotExists példában).

### <a name="deployifnotexists-example"></a>DeployIfNotExists példa

Példa: SQL Server-adatbázisok, hogy ha engedélyezve van-e a transparentDataEncryption értékeli ki. Ha nem, akkor hajtja végre a központi telepítést, az engedélyezéshez.

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
            "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
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
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

- Tekintse át a következő példák [Azure Policy-minták](../samples/index.md)
- Tekintse át a [szabályzatdefiníciók struktúrája](definition-structure.md)
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](../how-to/programmatically-create.md)
- Ismerje meg, hogyan [megfelelőségi adatok lekérése](../how-to/getting-compliance-data.md)
- Ismerje meg, hogyan [javítani a nem megfelelő erőforrások](../how-to/remediate-resources.md)
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../../management-groups/overview.md) ismertető részt.