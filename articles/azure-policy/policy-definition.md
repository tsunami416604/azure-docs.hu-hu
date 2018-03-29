---
title: Házirend-definíció Azure szerkezetet |} Microsoft Docs
description: Ismerteti, hogyan erőforrás házirend-definíció Azure házirend létrehozásához használt erőforrásokra vonatkozó konvenciók a szervezet ismertetésével, ha a házirend érvényesítve van-e, és a végrehajtandó műveletet.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: article
ms.service: azure-policy
ms.custom: ''
ms.openlocfilehash: 50965010d821d4edf94e2f5727546cb56f61f5db
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-policy-definition-structure"></a>Azure szabályzatdefiníciók struktúrája

Erőforrás házirend-definíció Azure házirend által használt lehetővé teszi a szervezet erőforrások egyezmények létrehozása: Ha a házirend érvényesítve van-e, és a végrehajtandó műveletet. Egyezmények meghatározásával szabályozhatja költségeit, és több könnyen kezelheti az erőforrásokat. Megadhatja például, hogy engedélyezve legyenek-e a virtuális gépek csak bizonyos típusú. Vagy megkövetelheti, hogy minden erőforrás egy bizonyos címkével rendelkezik. Összes gyermek-erőforrás által örökölt házirendek. Igen a házirend vonatkozik egy erőforráscsoport, esetén alkalmazandó az erőforráscsoport összes erőforrást.

JSON házirend-definíció létrehozására használhatja. A házirend-definíció a elemeket tartalmazza:

* mód
* paraméterek
* Megjelenített név
* leírás
* Házirend szabályai
  * logikai kiértékelése
  * hatása

Például a következő JSON mutatja egy házirendet, amely korlátozza, amelyekre központilag telepítették erőforrások:

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

Minden Azure házirend sablon minták erővel [sablonok Azure házirend](json-samples.md).

## <a name="mode"></a>Mód

A **mód** határozza meg, milyen típusú erőforrások kiértékelendő táblakifejezés házirend. A támogatott módok a következők:
* `all`: erőforráscsoportok és az összes erőforrástípus kiértékelése 
* `indexed`: csak értékelje ki, amely támogatja a címkék és a hely típusú erőforrások

Azt javasoljuk, hogy állítsa **mód** való `all` a legtöbb esetben. Az összes házirend-definíciók létrehozása a portál használata révén a `all` mód. Ha a PowerShell vagy Azure CLI-t használ, meg kell adnia a **mód** paraméter manuálisan.

`indexed` kell használni, amikor a házirendek létrehozásával kényszeríti ki a címkéket és a helyek. Ez azonban nem kötelező, de megakadályozza, hogy erőforrásokat, amelyek nem támogatják a címkék és a helyek jelennek meg, nem kompatibilis a megfelelőségi eredmények a. Az egyetlen kivétel ez alól **erőforráscsoportok**. Házirendek kényszerítése, hely vagy egy erőforráscsoportot a címkék megkísérlő-et kell beállítania **mód** való `all` és kifejezetten célja a `Microsoft.Resources/subscriptions/resourceGroup` típusa. Egy vonatkozó példáért lásd: [erőforráscímkék csoport kényszerítése](scripts/enforce-tag-rg.md).

## <a name="parameters"></a>Paraméterek

Paraméterek leegyszerűsíti a Csoportházirend kezelése a házirend-definíciók számának csökkentésével. A mezőket az űrlap – például paraméterek gondoljon `name`, `address`, `city`, `state`. Ezek a paraméterek ugyanaz mindig maradjon, azonban azok az értékek módosítása az űrlap egyes kitöltése alapján. Paraméterek ugyanúgy működnek, szabályzatok létrehozásakor. Paraméterek belefoglalja házirend-definíció, felhasználhatja különböző helyzetek kezelésére házirend különböző értékek használatával.

Például egy erőforrás-tulajdonságok korlátozza a hely, ahol erőforrásokat is telepíthető a házirendet sikerült határozza meg. Ebben az esetben a következő paraméterek volna deklarál, a házirend létrehozásakor:


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

A paraméter típusa karakterlánc vagy tömb lehet. A metaadat-tulajdonságnak szolgál például az Azure-portálon felhasználóbarát információk megjelenítéséhez.

A metaadat-tulajdonságnak belül használható **strongType** arra, hogy az Azure-portálon belül beállítások többszörös kiválasztási listája.  Engedélyezett értékek a **strongType** jelenleg tartalmaz:

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`

A házirend szabályban hivatkozási paraméter a következő szintaxissal:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Megjelenített név és leírás

Használhat **displayName** és **leírás** azonosíthatja a házirend-definíció, és adja meg a környezetben használat esetén.

## <a name="policy-rule"></a>Szabályzatbeli szabály

A házirendszabály áll **Ha** és **majd** blokkolja. Az a **Ha** blokk, megadhatja, hogy egy vagy több feltételt, adja meg, ha a házirend érvényesítve van-e. Logikai operátorok ezek a feltételek pontosan meghatározni a forgatókönyvhöz, amelyben egy házirendet alkalmazhat.

Az a **majd** blokk, megadhatja a hatást, amelyet akkor fordul elő, amikor a **Ha** feltételek teljesülnek.

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

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

A **nem** szintaxis invertálja a feltétel eredménye. A **allOf** szintaxis (a logikai hasonló **és** művelet) igényel minden feltétel igaz értékű lesz. A **anyOf** szintaxis (a logikai hasonló **vagy** művelet) szükséges egy vagy több feltétel igaz értékű lesz.

Logikai operátorok ágyazhatja be. Az alábbi példa mutatja egy **nem** művelet, amelynek van beágyazva egy **allOf** műveletet.

```json
"if": {
  "allOf": [
    {
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

A feltétel e egy **mező** meghatározott feltételeknek eleget. A támogatott feltételek esetén:

* `"equals": "value"`
* `"notEquals": "value"`
* `"like": "value"`
* `"notLike": "value"`
* `"match": "value"`
* `"notMatch": "value"`
* `"contains": "value"`
* `"notContains": "value"`
* `"in": ["value1","value2"]`
* `"notIn": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"notContainsKey": "keyName"`
* `"exists": "bool"`

Használatakor a **például** és **notLike** feltételek, megadhatja a helyettesítő karakter (*) értékét.

Használata esetén a **megfelelő** és **notMatch** feltételek, `#` képviselő számjegy, `?` betűvel, és bármely más karaktert adott tényleges karakter helyettesítéséhez. Tekintse meg a [jóváhagyott Virtuálisgép-rendszerképek](scripts/allowed-custom-images.md).

### <a name="fields"></a>Mezők
Feltételek mezőkkel jöttek létre. A mező képviseli tulajdonság az erőforrás-kérések forgalma, amellyel ismertetik az erőforrás állapotát.  

A következő mezők támogatottak:

* `name`
* `fullName`
  * Az erőforrást, beleértve a szülője (pl. "myServer/adatbázis") teljes nevét adja vissza
* `kind`
* `type`
* `location`
* `tags`
* `tags.tagName`
* `tags[tagName]`
  * Ez a zárójel szintaxis támogatja pontot tartalmazó címke neve
* tulajdonságának aliasokat - listájáért lásd: [aliasok](#aliases).

### <a name="alternative-accessors"></a>Alternatív leíró.
**A mező** a házirend szabályokban használt elsődleges elérővel. Azt közvetlenül megvizsgálja, hogy az erőforrás. Azonban a házirend támogatja egy más elérőnek **forrás**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Forrás** csak a több érték használatát támogatja **művelet**. A művelet az engedélyezési művelet, amely kiértékelése megtörténik a kérelem adja vissza. Engedélyezési műveletek érhetők el az engedélyezési szakaszában a [tevékenységnapló](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Ha házirend értékeli a háttérben állítja a meglévő erőforrásokat **művelet** való egy `/write` engedélyezési műveletet az erőforrás-típus.

### <a name="effect"></a>Következmény
A házirend hatása a következő típusú támogatja:

* **Megtagadási**: a biztonsági naplóban generál egy eseményt, és a kérelem sikertelen
* **Naplózási**: naplót hoz létre a figyelmeztető üzenet, de nem tudja teljesíteni a kérést
* **Hozzáfűzendő**: a definiált mezők halmaza alapján ad a kéréshez
* **AuditIfNotExists**: lehetővé teszi a naplózást, ha egy erőforrás nem létezik.
* **DeployIfNotExists**: erőforrás telepíti, ha még nem létezik. Ebből a célból beépített házirendeken keresztül jelenleg csak támogatott.

A **hozzáfűzése**, meg kell adnia a következő adatokat:

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

Az érték lehet egy karakterlánc vagy egy JSON-formátumú objektum.

A **AuditIfNotExists** és **DeployIfNotExists** értékelje ki a kapcsolódó erőforrások meglétét, és alkalmazza a szabályt, és a megfelelő hatása, ha adott erőforrás nem létezik. Például megkövetelheti, hogy egy hálózati figyelőt az összes virtuális hálózathoz van-e telepítve.
Példa a naplózást, ha egy virtuálisgép-bővítmény nincs telepítve, tekintse meg [kiterjesztés nem található naplózási](scripts/audit-ext-not-exist.md).


## <a name="aliases"></a>Aliasnevek

Egy erőforrás típusára vonatkozó tulajdonságokat eléréséhez használt tulajdonságának aliasokat. Aliasok engedélyezi, hogy milyen értékeket, vagy a feltételek engedélyezve van a erőforrás-tulajdonságok korlátozása. Mindegyik aliasnak elérési utak a különböző API-verziók egy adott erőforrástípusra van leképezve. Házirend kiértékelése közben a házirendmotor lekérdezi az adott API-verzió útvonal.

**Microsoft.Cache/Redis**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Állítsa e a nem ssl-Redis-kiszolgáló port (6379) engedélyezve van. |
| Microsoft.Cache/Redis/shardCount | Adjon meg a szilánkok prémium fürt gyorsítótárat létrehozni.  |
| Microsoft.Cache/Redis/sku.capacity | A telepítendő Redis gyorsítótár méretének beállítása.  |
| Microsoft.Cache/Redis/sku.family | Állítsa be az SKU-család használható. |
| Microsoft.Cache/Redis/sku.name | Állítsa be a Redis Cache telepítendő típusú. |

**Microsoft.Cdn/profiles**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Állítsa be az árképzési szint nevét. |

**Microsoft.Compute/disks**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Állítsa be az ajánlat platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/imagePublisher | Állítsa be a közzétevő platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/imageSku | Állítsa be a Termékváltozat platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/imageVersion | Állítsa be a platformlemezképet vagy a Piactéri lemezképhez a virtuális gép létrehozásához használt verzióját. |


**Microsoft.Compute/virtualMachines**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Compute/imageId | Állítsa be a virtuális gép létrehozásához használt kép azonosítója. |
| Microsoft.Compute/imageOffer | Állítsa be az ajánlat platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/imagePublisher | Állítsa be a közzétevő platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/imageSku | Állítsa be a Termékváltozat platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/imageVersion | Állítsa be a platformlemezképet vagy a Piactéri lemezképhez a virtuális gép létrehozásához használt verzióját. |
| Microsoft.Compute/licenseType | Állítsa be, hogy a lemezkép vagy lemez-e a licencelt helyszíni. Ez az érték csak a Windows Server operációs rendszert tartalmazó lemezképek használatos.  |
| Microsoft.Compute/virtualMachines/imageOffer | Állítsa be az ajánlat platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/virtualMachines/imagePublisher | Állítsa be a közzétevő platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/virtualMachines/imageSku | Állítsa be a Termékváltozat platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/virtualMachines/imageVersion | Állítsa be a platformlemezképet vagy a Piactéri lemezképhez a virtuális gép létrehozásához használt verzióját. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Állítsa be a virtuális merevlemez URI azonosítója. |
| Microsoft.Compute/virtualMachines/sku.name | A virtuális gép méretének beállítása. |
| Microsoft.Compute/virtualMachines/availabilitySet.id | Beállítja a rendelkezésre állási csoport a virtuális gép azonosítója. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Állítsa be a kiadó nevét: a bővítményt. |
| Microsoft.Compute/virtualMachines/extensions/type | Állítsa be a kiterjesztés típusú. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | A verzió megadása |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Compute/imageId | Állítsa be a virtuális gép létrehozásához használt kép azonosítója. |
| Microsoft.Compute/imageOffer | Állítsa be az ajánlat platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/imagePublisher | Állítsa be a közzétevő platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/imageSku | Állítsa be a Termékváltozat platformlemezképet vagy a virtuális gép létrehozásához használt Piactéri lemezképhez. |
| Microsoft.Compute/imageVersion | Állítsa be a platformlemezképet vagy a Piactéri lemezképhez a virtuális gép létrehozásához használt verzióját. |
| Microsoft.Compute/licenseType | Állítsa be, hogy a lemezkép vagy lemez-e a licencelt helyszíni. Ez az érték csak a Windows Server operációs rendszert tartalmazó lemezképek használatos. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | A méretezési csoportban lévő összes virtuális gépet a számítógép nevének előtagját beállítása |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Állítsa be a blob URI felhasználói lemezkép. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | A méretezési az operációs rendszer lemezek tárolására használt tároló URL-címeinek beállítása. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Méretezési csoportban lévő virtuális gépek méretének beállítása. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Méretezési csoportban lévő virtuális gépek szintjének beállítása |

**Microsoft.Network/applicationGateways**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Az átjáró méretének beállítása. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Állítsa be a virtuális hálózati átjáró típusát. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Állítsa be az átjáró-termékváltozat. |

**Microsoft.Sql/servers**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Állítsa be a kiszolgáló verziója. |

**Microsoft.Sql/databases**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Állítsa be az adatbázis-kiadás. |
| Microsoft.Sql/servers/databases/elasticPoolName | Állítsa be a rugalmas készlet az adatbázis nevét. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Állítsa be az adatbázis konfigurált szolgáltatási szint célkitűzésének azonosítója. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Állítsa be a konfigurált szolgáltatásiszint-célkitűzés az adatbázis nevét.  |

**Microsoft.Sql/elasticpools**

| Alias | Leírás |
| ----- | ----------- |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Állítsa be a rugalmas adatbáziskészlet az összes megosztott DTU. |
| servers/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Állítsa be a rugalmas készlet kiadása. |

**Microsoft.Storage/storageAccounts**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Állítsa be a hozzáférési réteg számlázási használatos. |
| Microsoft.Storage/storageAccounts/accountType | Állítsa be a termékváltozat. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Állítsa be, hogy a szolgáltatás titkosítja az adatokat a blob storage szolgáltatásban tárolt. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Állítsa be, hogy a szolgáltatás titkosítja az adatokat, a fájl storage szolgáltatásban tárolt. |
| Microsoft.Storage/storageAccounts/sku.name | Állítsa be a termékváltozat. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Beállítása, hogy a csak https-forgalom tároló szolgáltatást. |
| Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id | Ellenőrizze, hogy engedélyezve van-e a virtuális hálózati szolgáltatási végpont. |

## <a name="initiatives"></a>Kezdeményezések

Csoportosítása több kezdeményezések engedélyezése vonatkozó házirend-definíciók egyszerűsítése érdekében hozzárendelések és a felügyeleti, mert a csoport az egyetlen elemet használata. Például egy egyetlen kezdeményezésére minden kapcsolódó címkézési házirend definíciói csoportosíthatja. Ahelyett, hogy minden egyes házirend hozzárendelése egyenként, a kezdeményező alkalmaz.

A következő példa bemutatja, hogyan létrehozása kezelési két címkék kezdeményezés: `costCenter` és `productName`. Azt a két beépített házirendek segítségével az alapértelmezett címke.


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
        "policyDefinitions": [
            {
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

- Tekintse át az Azure házirend sablon minták [sablonok Azure házirend](json-samples.md).
