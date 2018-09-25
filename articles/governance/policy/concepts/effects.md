---
title: Az Azure Policy hatások ismertetése
description: Azure szabályzat-definíció rendelkezik, amelyek meghatározzák, hogyan megfelelőségét, és jelentett különböző hatásokkal.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 54562401c830232d0a4bf90405cc5a2dbedcd8bc
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055968"
---
# <a name="understand-policy-effects"></a>A házirend hatások ismertetése

Az Azure Policy mindegyik szabályzatdefiníció, amely meghatározza, hogy mi történik, ha ellenőrzése közben egyetlen hatása van a **Ha** szegmense, amely a szabály kiértékelése történik az éppen beolvasott erőforrás megfelelően. A hatásokat is is eltérően viselkednek, ha azok egy új erőforrást, egy frissíteni az erőforrás vagy egy meglévő erőforrást.

Jelenleg a szabályzat-definíció által támogatott öt hatások:

- Hozzáfűzés
- Naplózás
- AuditIfNotExists
- Megtagadás
- DeployIfNotExists

## <a name="order-of-evaluation"></a>Kiértékelési sorrend

Egy kérelmet létrehozni vagy frissíteni egy erőforrást az Azure Resource Manageren keresztül történik, ha a házirend dolgozza fel a hatások előtt a kérés átadja a megfelelő erőforrás-szolgáltató több.
Ami felesleges feldolgozási erőforrás-szolgáltató által ezzel megakadályozza, ha egy erőforrás nem felel meg a házirend tervezett cégirányítási vezérlőket. Szabályzat létrehoz egy listát az összes szabályzat-meghatározást, egy házirend vagy a kezdeményezés-hozzárendelést, által hozzárendelt, amelyek az erőforrás (mínusz kizárások) hatókör szerint érvényesek, és előkészíti az erőforrás minden definíció alapján kiértékelheti, hogy.

- **Hozzáfűzés** abban az esetben először. Azóta hozzáfűzése módosíthatta a kérést, módosítását, a hozzáfűző megelőzhetik a naplózási vagy a hatás megtagadása elindítása.
- **Megtagadási** Ezután kiértékeli. Kiértékelésével megtagadása naplózás, mielőtt egy nemkívánatos erőforrás dupla naplózása a rendszer letiltja.
- **Naplózási** Ezután kiértékeli a kérést az erőforrás-szolgáltató fog előtt.

A kérelemben megadott erőforrás-szolgáltató, és az erőforrás-szolgáltató egy sikeres állapotkódot adja vissza **AuditIfNotExists** és **DeployIfNotExists** annak meghatározásához, hogy követő kiértékelése a naplózás megfelelőségi vagy művelet szükség.

## <a name="append"></a>Hozzáfűzés

Hozzáfűzés további mezőket hozzáadni a kért erőforrás létrehozása vagy módosítása során használatos. Címkék hozzáadásához erőforrások, például a costCenter hasznos lehet, vagy tárolási erőforrás IP-címek megadása engedélyezett.

### <a name="append-evaluation"></a>Értékelés hozzáfűzése

Ahogy említettük, fűzze hozzá a kérelem első által feldolgozott erőforrás-szolgáltató létrehozása vagy frissítése egy adott erőforrás előtt kiértékeli. Fűzze hozzá a mezőket ad hozzá az erőforrás során a **Ha** a szabály a feltétel teljesül. A Hozzáfűzés hatása lenne felülbírálása egy értéket az eredeti kérelmet egy másik érték, ha egy megtagadási hatás funkcionál, és elutasítja a kérelmet.

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

2. példa: Több **mező/érték** párok fűzze hozzá a címkéket egy készletét.

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

Megtagadási szolgál, hogy egy erőforrás-kérelmet, amely nem felel meg a kívánt szabályzat-definíció a szabványokra, és a kérelem sikertelen.

### <a name="deny-evaluation"></a>Értékelés megtagadása

Ha megtagadja a létrehozása vagy frissítése egy erőforrást, megakadályozza, hogy a kérelmet küld az erőforrás-szolgáltató előtt. A kérelem, 403 (tiltott) adja vissza. A portálon a tiltott állapot, amely a szabályzat-hozzárendelés miatt nem sikerült elindítani az üzemelő példányon, tekinthet meg.

Egy értékelési ciklus során szabályzatdefiníciók egy megtagadási kezdve a megfelelő erőforrások nem megfelelőként lesznek megjelölve, de az erőforráson nincs művelet történik.

### <a name="deny-properties"></a>Megtagadási tulajdonságai

A megtagadási hatása nincs használatra tulajdonságokat a **majd** szabályzatdefiníció feltétel.

### <a name="deny-example"></a>Példa megtagadása

Példa: A Megtagadás hatás használatával.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Naplózás

Naplózási figyelmeztetési esemény létrehozása a tevékenységnaplóban egy nem megfelelő erőforráshoz értékeli ki, de nem állítja le a kérelem szolgál.

### <a name="audit-evaluation"></a>Értékelés naplózása

Naplózási hatással az utolsó futtatása a létrehozás során vagy a frissítés előtt az erőforrás erőforrás van az erőforrás-szolgáltató. Naplózási egy erőforrás-kérés és a egy kiértékelési ciklusa esetén ugyanúgy működik, és végrehajt egy `Microsoft.Authorization/policies/audit/action` műveletet a tevékenységnaplóhoz. Mindkét esetben az erőforrás van megjelölve, nem megfelelő.

### <a name="audit-properties"></a>Naplózási tulajdonságok

A naplózási hatása nincs használatra tulajdonságokat a **majd** szabályzatdefiníció feltétel.

### <a name="audit-example"></a>Naplózási példa

Példa: A naplózási hatás használatával.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists lehetővé teszi a naplózást az erőforrás, amely megfelel a **Ha** feltétel, de nem rendelkezik a megadott összetevők a **részletek** , a **majd** feltétel.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists kiértékelése

AuditIfNotExists futtatása után egy erőforrás-szolgáltató erőforrás létrehozásának vagy frissítésének kérést rendelkezik kezelnek, és sikeres állapotkódot adott vissza. A hatás akkor aktiválódik, ha nincsenek kapcsolódó erőforrások, vagy ha az erőforrások által meghatározott **ExistenceCondition** nem értékelik ki a true értékre. A hatás akkor aktiválódik, amikor egy `Microsoft.Authorization/policies/audit/action` a tevékenységnaplóhoz művelet végrehajtása a ugyanúgy, mint a naplózási hatást. Adatvezérelt, az erőforrást, amely elégedett az eredménnyel a **Ha** feltétel, hogy az erőforrás, amely a nem megfelelő van megjelölve.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists tulajdonságai

A **részletek** a altulajdonságokat, amelyek meghatározzák a kapcsolódó erőforrásokat a tulajdonságnak AuditIfNotExists hatásait.

- **Típus** [kötelező]
  - Határozza meg a megfelelő kapcsolódó erőforrás.
  - Úgy, hogy lekérni egy erőforrást alatt elindítja a **Ha** feltétel erőforrás, akkor az azonos erőforráscsoportjában lévő lekérdezéseket a **Ha** erőforrás feltételt.
- **Név** (nem kötelező)
  - A megfelelő erőforrás pontos nevét adja meg, és a egy adott erőforrás helyett a megadott típusú összes erőforrást beolvasni a szabályzat okoz.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a rendszer a kapcsolódó erőforrás egy másik erőforráscsoportban található származnak.
  - Nem vonatkozik, ha **típus** egy erőforrás, amely alatt a **Ha** erőforrás feltételt.
  - Alapértelmezett érték a **Ha** erőforrás erőforráscsoport feltételt.
- **ExistenceScope** (nem kötelező)
  - Engedélyezett értékek a következők _előfizetés_ és _ResourceGroup_.
  - Beállítja a hatókört, hova felel meg a kapcsolódó erőforrást beolvasni.
  - Nem vonatkozik, ha **típus** egy erőforrás, amely alatt a **Ha** erőforrás feltételt.
  - A _ResourceGroup_, hogy korlátozza a **Ha** feltétel erőforrás erőforráscsoport vagy a megadott erőforráscsoport **ResourceGroupName**.
  - A _előfizetés_, lekérdezi a teljes előfizetés, a kapcsolódó erőforrás.
  - Alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, minden kapcsolódó erőforrás a **típus** eleget tesz a hatást, és nem indítja el az ellenőrzést.
  - Ugyanazt a nyelvet használja, mint az a szabály a **Ha** feltételt, de képest értékeli ki minden kapcsolódó erőforrás külön-külön.
  - Ha minden egyező kapcsolódó erőforrás igaz értéket ad vissza, a hatás teljesült, és nem indítja el a naplózási.
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

DeployIfNotExists is fut, miután az erőforrás-szolgáltató van kezelve a létrehozás vagy frissítés kérése az erőforrás és a sikeres állapotkódot adott vissza. A hatás akkor aktiválódik, ha nincsenek kapcsolódó erőforrások, vagy ha az erőforrások által meghatározott **ExistenceCondition** nem értékelik ki a true értékre. A hatás akkor aktiválódik, amikor egy sablon telepítésének hajtja végre.

Egy értékelési ciklus során a szabályzatdefiníciók egy DeployIfNotExists hatást, amelyek megfelelnek az erőforrások nem megfelelőként lesznek megjelölve, de az erőforráson nincs művelet történik.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists tulajdonságai

A **részletek** a DeployIfNotExists hatást tulajdonsága egyezik a kapcsolódó erőforrásokat határoz meg az összes altulajdonságot és a sablon telepítésének végrehajtásához.

- **Típus** [kötelező]
  - Határozza meg a megfelelő kapcsolódó erőforrás.
  - Úgy, hogy lekérni egy erőforrást alatt elindítja a **Ha** feltétel erőforrás, akkor az azonos erőforráscsoportjában lévő lekérdezéseket a **Ha** erőforrás feltételt.
- **Név** (nem kötelező)
  - A megfelelő erőforrás pontos nevét adja meg, és a egy adott erőforrás helyett a megadott típusú összes erőforrást beolvasni a szabályzat okoz.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a rendszer a kapcsolódó erőforrás egy másik erőforráscsoportban található származnak.
  - Nem vonatkozik, ha **típus** egy erőforrás, amely alatt a **Ha** erőforrás feltételt.
  - Alapértelmezett érték a **Ha** erőforrás erőforráscsoport feltételt.
  - Ha egy sablon telepítésének, ezt az értéket az erőforráscsoportban üzembe helyezett.
- **ExistenceScope** (nem kötelező)
  - Engedélyezett értékek a következők _előfizetés_ és _ResourceGroup_.
  - Beállítja a hatókört, hova felel meg a kapcsolódó erőforrást beolvasni.
  - Nem vonatkozik, ha **típus** egy erőforrás, amely alatt a **Ha** erőforrás feltételt.
  - A _ResourceGroup_, hogy korlátozza a **Ha** feltétel erőforrás erőforráscsoport vagy a megadott erőforráscsoport **ResourceGroupName**.
  - A _előfizetés_, lekérdezi a teljes előfizetés, a kapcsolódó erőforrás.
  - Alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, minden kapcsolódó erőforrás a **típus** eleget tesz a hatást, és ne indítsa el az üzembe helyezés.
  - Ugyanazt a nyelvet használja, mint az a szabály a **Ha** feltételt, de képest értékeli ki minden kapcsolódó erőforrás külön-külön.
  - Minden egyező kapcsolódó erőforrás igaz értéket ad vissza, ha a hatás teljesült, és ne indítsa el az üzembe helyezés.
  - Használhatja a értékekkel egyenértékűség ellenőrzése [field()] a **Ha** feltétel.
  - Például segítségével ellenőrizze, hogy a szülő erőforrás (az a **Ha** feltétel) van ugyanazon a helyen erőforrás egyező kapcsolódó erőforrásként.
- **roleDefinitionIds** [kötelező]
  - Ez a tulajdonság karakterláncok, amelyek megfelelnek a szerepköralapú hozzáférés-vezérlési szerepkör azonosítója elérhető-e az előfizetésben kell tartalmaznia. További információkért lásd: [szervizelési – konfigurálja a szabályzat-definíció](../how-to/remediate-resources.md#configure-policy-definition).
- **Üzembe helyezés** [kötelező]
  - Ez a tulajdonság kell tartalmaznia a teljes körű sablonalapú telepítés adná mivel a `Microsoft.Resources/deployments` PUT API. További információkért lásd: a [központi telepítések REST API-val](/rest/api/resources/deployments).

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

Több hozzárendelések hatással lehet egy erőforrást. Ezeket a hozzárendeléseket lehet ugyanabban a hatókörben (adott erőforráshoz, erőforráscsoporthoz, előfizetés vagy felügyeleti csoport) vagy egy másik hatókört. Ezeket a hozzárendeléseket mindegyike valószínűleg is definiálva másik hatást. Függetlenül attól a feltétellel és az egyes (közvetlenül vagy egy kezdeményezés részeként hozzárendelt) házirend érvénybe egymástól függetlenül értékeli. Például ha szabályzat 1 egy feltételt, amely korlátozza az egy előfizetéshez csak 'westus', a Megtagadás kezdve a létrehozandó erőforrás helye pedig 2 házirend rendelkezik egy feltételt, amely korlátozza az erőforrás helye az erőforráscsoport B (amely egy előfizetésben), csak lehet a naplózási kezdve "eastus" létrehozott is vannak hozzárendelve, az eredményül kapott eredmény lenne::

- "Eastus" B erőforráscsoportban már található valamilyen erőforrás a szabályzat 2 szabványnak megfelelő, de lett megjelölve nem megfelelő szabályzat 1.
- Már nem "eastus" a B erőforráscsoport összes erőforrását nem megfelelő szabályzat 2 lesz megjelölve, és akkor be kell jelölni nem megfelelő, 1-házirendet, ha nem 'westus'.
- Bármilyen új erőforrást az előfizetésben A nem a 'westus' lenne tagadva szabályzat 1.
- Bármilyen új erőforrást az előfizetésben A / B erőforráscsoport 'westus' lenne jelölhetők meg, nem kompatibilis a szabályzat 2, de hozhatók létre (megfelelő szabályzat 2 és 1-házirendet a naplózása, és nem utasíthatja el).

Ha a szabályzat 1 és 2 szabályzat is hatással volt a Megtagadás, a helyzet módosulnak:

- Nincs "eastus" a B erőforráscsoport összes erőforrását már nem megfelelő szabályzat 2 lesz megjelölve.
- Minden erőforrás már nem található 'westus' B erőforráscsoport a nem megfelelő házirendhez 1 lesz megjelölve.
- Bármilyen új erőforrást az előfizetésben A nem a 'westus' lenne tagadva szabályzat 1.
- Bármilyen új erőforrást egy előfizetés / erőforráscsoport B lenne tagadva, (mivel a hely soha nem tudta teljesíteni a szabályzat 1 és a szabályzat 2).

Minden hozzárendelés külön-külön abban az esetben, mivel nincs lehetőség a jegy keresztül hatókör között fennálló különbségek miatt megszakad egy erőforrást. Így az eredmény rétegezett házirendek vagy a szabályzat átfedés tekinthető **összegző leginkább korlátozó**. Más szóval azt szeretné, hogy a létrehozott erőforrás sikerült blokkolja egymást átfedő és az ütköző házirendek, például a fenti példa, ha a szabályzat 1 és a szabályzat 2 volt Megtagadás hatással. Ha továbbra is az erőforrás célhatóköre kell létrehozni, tekintse át a kivételeket, annak érdekében, hogy a megfelelő szabályzatok hatással vannak a megfelelő hatókörök minden hozzárendelés.

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy-minták](../samples/index.md)
- Tekintse át a [szabályzatdefiníciók struktúrája](definition-structure.md)
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](../how-to/programmatically-create.md)
- Ismerje meg, hogyan [megfelelőségi adatok lekérése](../how-to/getting-compliance-data.md)
- Fedezze fel hogyan [javítani a nem megfelelő erőforrások](../how-to/remediate-resources.md)
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../../management-groups/overview.md) ismertető részt.