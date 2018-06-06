---
title: Az Azure házirend hatások ismertetése
description: Az Azure házirend-definíció járhat különböző, amelyek meghatározzák, hogyan kezeli, és jelentett.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 23bbbe9cf86268f93ae1f8fcec9303efa8a673de
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796716"
---
# <a name="understanding-policy-effects"></a>Házirend által okozott hatások ismertetése

Minden egyes házirend-definíció Azure házirend határozza meg, hogy mi történik, ha ellenőrzése közben egyetlen hatással van a **Ha** házirendszabály szegmens ki lesz értékelve az erőforrás a beolvasandó kereséséhez. A hatás is is eltérően viselkednek, ha azok egy új erőforrást, egy frissített erőforrás vagy egy meglévő erőforrást.

Jelenleg a házirend-definíció támogatott öt hatások:

- Hozzáfűzés
- Naplózás
- AuditIfNotExists
- Megtagadás
- DeployIfNotExists

## <a name="order-of-evaluation"></a>Kiértékelési sorrend

Sikerült létrehozni vagy frissíteni az Azure Resource Manageren keresztül erőforrás a kérelem elküldésekor házirend dolgozza fel a kérelem a megfelelő erőforrás-szolgáltató való átadás előtt hatások számos.
Ezzel megakadályozhatja felesleges feldolgozási egy erőforrás-szolgáltató egy erőforrás nem felel meg a házirend tervezett irányítás vezérlők. Házirend hoz létre, amelyek az erőforrás által (a kizárások) mínusz hatókörében érvényesek, és előkészíti a kiértékelése minden definition szemben az erőforrás egy házirend vagy kezdeményezésére hozzárendelés által hozzárendelt házirend definíciók listája.

- **Hozzáfűzendő** először ki lesz értékelve. Mivel hozzáfűzése módosíthatta a kérelmet, a módosítások az hozzáfűzése előfordulhat, hogy megakadályozza a naplózási vagy hatás megtagadása időt.
- **Megtagadási** majd ki lesz értékelve. Kiértékelésével megtagadása naplózási, mielőtt egy kívánt erőforrás dupla naplózása a rendszer letiltja.
- **Naplózási** majd értékeli ki a kérelem, az erőforrás-szolgáltató Ugrás előtt.

Amennyiben a kérelemben megadott erőforrás-szolgáltató, és az erőforrás-szolgáltató adja vissza egy sikeres állapotkód **AuditIfNotExists** és **DeployIfNotExists** annak meghatározásához, hogy követési kiértékelése szükség a megfelelőség naplózása vagy a műveletet.

## <a name="append"></a>Hozzáfűzés

Hozzáfűzendő további mezőt hozzá a kért erőforrás létrehozása vagy módosítása során használatos. Címkék hozzáadása az erőforrások, például a costCenter hasznos lehet, vagy a tároló egyik erőforrásához tartozó IP-címek megadása engedélyezett.

### <a name="append-evaluation"></a>Kiértékelési hozzáfűzése

Ahogy azt korábban említettük, hozzáfűzése egy erőforrás-szolgáltató kérelmének feldolgozása során a létrehozása vagy frissítése egy erőforrást a kérés előtt kiértékeli. Hozzáfűzendő mezőből ad hozzá az erőforrás amikor a **Ha** házirendszabály feltétel teljesül. Append hatással lenne bírálja felül az eredeti kérést egy másik érték megadásával értéket, ha effektus a Megtagadás funkcionál, és elutasítja a kérelmet.

A Hozzáfűzés hatással házirend-definíció futtatásakor egy kiértékelési ciklusa részeként, nem módosíthatja erőforrásokat, amelyek már létezik. Ehelyett azt jelöli meg az összes erőforrást, amely megfelel a **Ha** feltétel nem megfelelő.

### <a name="append-properties"></a>Tulajdonságok hozzáfűzése

Csak akkor append hatása van a **részletek** tömb, amely pedig szükséges. Mint **részletek** tömb, vagy egyetlen is igénybe vehet **mező/érték** pár vagy Többszörösök. Tekintse meg [házirend-definíció](policy-definition.md#fields) elfogadható mezők listája.

### <a name="append-examples"></a>Példák hozzáfűzése

1. példa: Az egyszeri **mező/érték** pár hozzáfűzése egy címkét.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

2. példa: Több **mező/érték** párok hozzáfűzendő címkék készlete.

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

3. példa: Az egyszeri **mező/érték** párosítsa a használatával egy [alias](policy-definition.md#aliases) a tömb **érték** IP-szabályok beállítása egy tárfiókot.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

## <a name="deny"></a>Megtagadás

Megtagadási megakadályozható a erőforrás kérelmeket, amelyek nem felel meg a kívánt szabványok a házirend-definíció keresztül, és a kérelem sikertelen lesz.

### <a name="deny-evaluation"></a>Kiértékelési megtagadása

Ha megtagadja a létrehozásakor vagy frissítésekor. egy erőforrást, megakadályozza, hogy a kérelem előtt küldi el az erőforrás-szolgáltató. A kérelem egy 403 (tiltott) adja vissza a rendszer. A portálon a tiltott tekinthetők meg, hogy a házirend-hozzárendelés miatt nem sikerült elindítani a telepítési állapota.

Egy értékelési ciklus során a házirend-definíciók Megtagadás hatású erőforrások egyező fel van tüntetve nem megfelelő, de semmilyen műveletet hajtja végre, hogy az erőforrás.

### <a name="deny-properties"></a>Tulajdonságok megtagadása

A megtagadási hatása nincs használható minden egyéb tulajdonságot a **majd** feltétele a házirend-definíció.

### <a name="deny-example"></a>Példa megtagadása

Példa: A Megtagadás hatás használatával.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Naplózás

Naplózási hatás figyelmeztetési esemény létrehozása a napló nem kompatibilis erőforrás van kiértékelve, de nem állítja le a kérelem szolgál.

### <a name="audit-evaluation"></a>Naplózási kiértékelése

A naplózási hatással az utolsó futtatása közben a létrehozása vagy frissítése előtt az erőforrás erőforrás kap az erőforrás-szolgáltató. Naplózási egy erőforrás-kérelem és egy kiértékelési ciklusa ugyanúgy működik, és végrehajtja a `Microsoft.Authorization/policies/audit/action` a műveletnapló művelet. Mindkét esetben az erőforrás eltérését van megjelölve.

### <a name="audit-properties"></a>Naplózási tulajdonságai

A naplózási hatása nincs használható minden egyéb tulajdonságot a **majd** feltétele a házirend-definíció.

### <a name="audit-example"></a>Naplózási – példa

Példa: A naplózási hatás használatával.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists lehetővé teszi, hogy egy erőforrást, amely megfelel a naplózás a **Ha** feltétel, de nem rendelkezik a megadott összetevők a **részletek** , a **majd** feltétel.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists kiértékelése

AuditIfNotExists futtatása után egy erőforrás-szolgáltató egy erőforrás létrehozási vagy frissítési kérelem kezelt és sikeres állapotkódot adott vissza. A hatás akkor váltódik ki, ha nincsenek kapcsolódó erőforrások, vagy ha határozzák meg az erőforrások **ExistenceCondition** nem igaz értéked ad vissza. A hatás kiváltásakor a `Microsoft.Authorization/policies/audit/action` a műveletnapló művelet végrehajtása a naplózási hatást ugyanúgy. Amikor elindul, az erőforrást, amely megfelelne a **Ha** feltétele az erőforrást, amely a jelölése szerint nem megfelelő.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists tulajdonságai

A **részletek** AuditIfNotExists által okozott hatások tulajdonságnak a altulajdonságokat, amelyek meghatározzák a kapcsolódó erőforrások kereséséhez.

- **Típus** [szükséges]
  - Adja meg a megfelelő kapcsolódó erőforrás típusát.
  - Úgy, hogy alá erőforrás beolvasása elindítja a **Ha** feltétel erőforrás, akkor ugyanabban az erőforráscsoportban, lévő lekérdezéseket a **Ha** erőforrás feltétel.
- **Név** (nem kötelező)
  - Egyező erőforrás pontos nevét adja meg, és leállítja a házirendet egy meghatározott erőforrás helyett minden erőforrás a megadott típus beolvasása.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a kapcsolódó erőforrás egy másik erőforráscsoportban található származnia megfelelő.
  - Alól kivételt képez **típus** erőforrása, amely alatt lenne a **Ha** erőforrás feltétel.
  - Alapértelmezett érték a **Ha** erőforrás erőforráscsoport feltétel.
- **ExistenceScope** (nem kötelező)
  - Két érték engedélyezett _előfizetés_ és _ResourceGroup_.
  - Beállítja a hatókört, ahol a megfelelő kapcsolódó erőforrás beolvasása.
  - Alól kivételt képez **típus** erőforrása, amely alatt lenne a **Ha** erőforrás feltétel.
  - A _ResourceGroup_, hogy korlátozza a **Ha** feltétel erőforrás erőforráscsoport vagy a megadott erőforráscsoport **ResourceGroupName**.
  - A _előfizetés_, lekérdezi az egész előfizetésre, a kapcsolódó erőforrás.
  - Alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, az esetleges kapcsolódó erőforrása **típus** eleget tesz a hatás, és ne indítsa el a naplózás.
  - Ugyanebben a nyelvben használja, mint a házirend-szabálya az **Ha** feltétel, de képest értékeli ki minden kapcsolódó erőforrás külön-külön.
  - Bármely megfelelő kapcsolódó erőforrás értéke igaz, ha a hatás meggyőződött, és ne indítsa el a naplózás.
  - Használhatja [field()] való értékek ellenőrizze a **Ha** feltétel.
  - Például használható ellenőrzése a szülő erőforrás (a a **Ha** feltétel) a megfelelő kapcsolódó erőforrás helyen erőforrás található.

### <a name="auditifnotexists-example"></a>AuditIfNotExists – példa

Példa: Értékeli ki a virtuális gépek számára az határozza meg, ha a kártevőirtó-kiterjesztés létezik-e, majd a naplózás, ha valóban hiányzik.

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

AuditIfNotExists hasonló, DeployIfNotExists végrehajtja a sablon-üzembehelyezés a feltétel teljesülése esetén.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists kiértékelése

DeployIfNotExists is futtat egy erőforrás-szolgáltató kezelt a létrehozás vagy frissítés erőforrás elküldeni a kérelmet, és sikeres állapotkódot adott vissza után. A hatás akkor váltódik ki, ha nincsenek kapcsolódó erőforrások, vagy ha határozzák meg az erőforrások **ExistenceCondition** nem igaz értéked ad vissza. Amikor elindul a hatás, egy sablon telepítésének végrehajtása.

Egy értékelési ciklus során a házirend-definíciók DeployIfNotExists hatású erőforrások egyező fel van tüntetve nem megfelelő, de semmilyen műveletet hajtja végre, hogy az erőforrás.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists tulajdonságai

A **részletek** DeployIfNotExists hatások tulajdonságnak a altulajdonságokat, amelyek meghatározzák a kapcsolódó egyezést erőforrásokat és a sablon-üzembehelyezés végrehajtásához.

- **Típus** [szükséges]
  - Adja meg a megfelelő kapcsolódó erőforrás típusát.
  - Úgy, hogy alá erőforrás beolvasása elindítja a **Ha** feltétel erőforrás, akkor ugyanabban az erőforráscsoportban, lévő lekérdezéseket a **Ha** erőforrás feltétel.
- **Név** (nem kötelező)
  - Egyező erőforrás pontos nevét adja meg, és leállítja a házirendet egy meghatározott erőforrás helyett minden erőforrás a megadott típus beolvasása.
- **ResourceGroupName** (nem kötelező)
  - Lehetővé teszi, hogy a kapcsolódó erőforrás egy másik erőforráscsoportban található származnia megfelelő.
  - Alól kivételt képez **típus** erőforrása, amely alatt lenne a **Ha** erőforrás feltétel.
  - Alapértelmezett érték a **Ha** erőforrás erőforráscsoport feltétel.
  - Ha egy sablon-üzembehelyezés, ennek az értéknek az erőforráscsoportban telepítették.
- **ExistenceScope** (nem kötelező)
  - Két érték engedélyezett _előfizetés_ és _ResourceGroup_.
  - Beállítja a hatókört, ahol a megfelelő kapcsolódó erőforrás beolvasása.
  - Alól kivételt képez **típus** erőforrása, amely alatt lenne a **Ha** erőforrás feltétel.
  - A _ResourceGroup_, hogy korlátozza a **Ha** feltétel erőforrás erőforráscsoport vagy a megadott erőforráscsoport **ResourceGroupName**.
  - A _előfizetés_, lekérdezi az egész előfizetésre, a kapcsolódó erőforrás.
  - Alapértelmezett érték a _ResourceGroup_.
- **ExistenceCondition** (nem kötelező)
  - Ha nincs megadva, az esetleges kapcsolódó erőforrása **típus** eleget tesz a hatás, és ne indítsa el a központi telepítést.
  - Ugyanebben a nyelvben használja, mint a házirend-szabálya az **Ha** feltétel, de képest értékeli ki minden kapcsolódó erőforrás külön-külön.
  - Bármely megfelelő kapcsolódó erőforrás értéke igaz, ha a hatás meggyőződött, és ne indítsa el a központi telepítés.
  - Használhatja [field()] való értékek ellenőrizze a **Ha** feltétel.
  - Például használható ellenőrzése a szülő erőforrás (a a **Ha** feltétel) a megfelelő kapcsolódó erőforrás helyen erőforrás található.
- **Központi telepítés** [szükséges]
  - Ezt a tulajdonságot kell tartalmaznia a teljes sablon-üzembehelyezés, mivel azt átadandó a `Microsoft.Resources/deployments` PUT API. További információkért lásd: a [központi telepítések REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Belüli összes funkciók a **telepítési** tulajdonság összetevőként a sablon, a házirend nem értékeli ki. A kivétel: a **paraméterek** kapott értékeket a szabályzatot a sablon tulajdonság. A **érték** ebben a szakaszban a sablon alapján paraméter neve használatával ezt az értéket átadja végez (lásd: _fullDbName_ DeployIfNotExists példában).

### <a name="deployifnotexists-example"></a>DeployIfNotExists – példa

Példa: SQL Server-adatbázisokat határozza meg, ha engedélyezve van-e a transparentDataEncryption értékeli ki. Ha nem, majd engedélyezi a központi telepítés végrehajtása.

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

## <a name="layering-policies"></a>A réteges házirendek

Egy erőforrás negatív hatással lehet több hozzárendeléseket. A hozzárendelések lehet ugyanabban a hatókörben (adott erőforrás, erőforráscsoport, előfizetés vagy felügyeleti csoportnak), vagy a különböző hatóköröket. A hozzárendelések egy is egy másik gyakorolna definiálva. Függetlenül attól a feltétellel és az egyes házirendje (közvetlenül vagy egy kezdeményezés részeként) hatását egymástól függetlenül történik. Például ha 1-házirendben engedélyezve van egy feltételt, amely korlátozza a való létrehozása folyamatban "westus" a Megtagadás hatás és a házirend 2, amely korlátozza az erőforrások az erőforrás-előfizetést A hely a B mértékcsoporton (amely A előfizetés) létrehozását az "eastus" és a naplózás hatással mindkettő hozzárendelt, az eredményül kapott eredmény lenne:

- Valamilyen erőforrás erőforráscsoportja B "eastus" már megfelelő házirendhez 2, de nem megfelelő házirendhez 1 megjelölt.
- Valamilyen erőforrás már nem a "eastus" erőforráscsoport B nem megfelelő házirendhez 2 lesz megjelölve, és akkor be kell jelölni nem kompatibilis az 1-házirendet, ha nem "westus".
- Bármely új erőforrás előfizetés A nem a "westus" megtagadná a rendszer 1 házirend.
- Bármely új erőforrás előfizetés A / B erőforráscsoport "westus" lesz megjelölve eltérését házirend 2, de hozhatók létre (kompatibilis házirend 1 és 2 házirend naplózni, és tiltsa le).

Ha a házirend 1 és 2 házirend is hatással volt a Megtagadás, a helyzet vált át:

- Nem megfelelő házirendhez 2 valamilyen erőforrás már nem a "eastus" a B erőforráscsoport lesz megjelölve.
- Nem megfelelő házirendhez 1 valamilyen erőforrás már nem a "westus" a B erőforráscsoport lesz megjelölve.
- Bármely új erőforrás előfizetés A nem a "westus" megtagadná a rendszer 1 házirend.
- Bármely új erőforrás előfizetés A / B erőforráscsoport megtagadná a rendszer (mivel az helyére soha nem elégíti ki, az 1 és szabályzat 2).

Egyes hozzárendelések külön-külön értékeli ki, mert nem áll rendelkezésre egy erőforrás slip hatókörében eltérések miatt hiány keresztül lehetőséget. Ezért a réteges házirendek és házirend átfedés jár tekinthető **összegző szigorúbb**. Más szóval létrehozni kívánt erőforrás sikerült tiltható le egymást átfedő és az ütköző házirendek, például a fenti példa miatt Ha házirend 1 és a házirend 2 hatást Megtagadás. Ha továbbra is szükséges az erőforrást a célhatókört hozhatók létre, tekintse át a minden hozzárendelés annak érdekében, hogy a megfelelő házirendek érintenek, a jobb oldali hatókörök kizárásokat.

## <a name="next-steps"></a>További lépések

Most, hogy a házirend-definíció hatások bemutatják, tekintse meg a házirend-minták:

- Tekintse át a következő további példákat [Azure házirend minták](json-samples.md).
