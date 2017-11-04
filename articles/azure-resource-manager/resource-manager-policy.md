---
title: "Az Azure erőforrás-házirendekkel |} Microsoft Docs"
description: "Azure Resource Manager-házirendek használata a központi telepítése során beállításának egységes erőforrás-tulajdonságokat ismerteti. Házirendek: az előfizetés vagy az erőforrás-csoportok alkalmazhatók."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: tomfitz
ms.openlocfilehash: 2e0d2e9830639209a22e9b62b0679d31854150e4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="resource-policy-overview"></a>Erőforrás-házirendek – áttekintés
Erőforrás-házirendek lehetővé teszik a szervezet egyezmények erőforrások létrehozásához. Egyezmények meghatározásával szabályozhatja költségeit, és több könnyen kezelheti az erőforrásokat. Megadhatja például, hogy engedélyezve legyenek-e a virtuális gépek csak bizonyos típusú. Vagy megkövetelheti, hogy minden erőforrás egy bizonyos címkével rendelkezik. Összes gyermek-erőforrás által örökölt házirendek. Igen a házirend vonatkozik egy erőforráscsoport, esetén alkalmazandó az erőforráscsoport összes erőforrást.

Nincsenek két fogalom megértéséhez házirendek:

* házirend-definíció - ismerteti Ha a házirend érvényesítve van-e, és milyen műveletet kell végrehajtani
* házirend-hozzárendelés - alkalmazza a házirend-definíció a hatókör (előfizetés vagy az erőforrás csoport)

Ez a témakör ismerteti a házirend-definíció. További információ a házirend-hozzárendelést: [hozzárendelésére és kezelésére erőforrás-házirendek használata Azure-portálon](resource-manager-policy-portal.md) vagy [meg és kezelheti a parancsfájl-házirendeket](resource-manager-policy-create-assign.md).

Házirendek létrehozása, és az erőforrások (PUT és műveletek javítás) frissítésekor kiértékelése.

## <a name="how-is-it-different-from-rbac"></a>Miben különbözik az RBAC?
Házirend és a szerepköralapú hozzáférés-vezérlést (RBAC) közötti néhány fontos különbség van. Az RBAC-ra összpontosít **felhasználói** különböző hatóköröket műveletek. Például akkor kerülnek a közreműködő szerepkört az egy erőforráscsoport, a kívánt hatókörben, erőforrás csoporthoz módosításokat végezheti el. Házirend összpontosít **erőforrás** tulajdonságok üzembe helyezése során. Például szabályzatokkal, szabályozhatja a telepíthető erőforrások típusú. Vagy a helyek, amelyben az erőforrások kiépítése korlátozhatja. Szerepalapú, eltérően házirend egy alapértelmezett engedélyezése és explicit megtagadja a rendszer. 

Házirendek használatára kell hitelesíteni RBAC keresztül. Pontosabban, a fiókot kell-e a:

* `Microsoft.Authorization/policydefinitions/write`egy házirend meghatározásához engedély
* `Microsoft.Authorization/policyassignments/write`egy házirend hozzárendelése engedély 

Ezek az engedélyek nem szerepelnek a **közreműködő** szerepkör.

## <a name="built-in-policies"></a>Beépített házirendek

Azure biztosít néhány beépített házirend-definíciókat tartalmazzon, előfordulhat, hogy kevesebb házirendek meg kell adnia. Házirend-definíciók a folytatás előtt érdemes lehet akár egy beépített házirend már használatával kell definition. A beépített házirend-definíciók száma a következők:

* Engedélyezett helyek
* Engedélyezett erőforrástípusok
* A tárfiók termékváltozatok engedélyezett
* Virtuális gép termékváltozatok engedélyezett
* Címke és az alapértelmezett érték alkalmazása
* Címke és érték
* Nem engedélyezett típusú erőforrások
* SQL Server verziója 12.0 megkövetelése
* Tárolási fiók titkosításának kötelezővé tétele

Említett házirendjeinek keresztül rendelhet a [portal](resource-manager-policy-portal.md), [PowerShell](resource-manager-policy-create-assign.md#powershell), vagy [Azure CLI](resource-manager-policy-create-assign.md#azure-cli).

## <a name="policy-definition-structure"></a>Házirend szerkezete
JSON házirend-definíció létrehozására használhatja. A házirend-definíció a elemeket tartalmazza:

* Mód
* paraméterek
* Megjelenített név
* leírás
* Házirend szabályai
  * logikai kiértékelése
  * hatása

A következő példa bemutatja egy házirendet, amely korlátozza, amelyekre központilag telepítették erőforrások:

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

## <a name="mode"></a>Mód

Azt javasoljuk, hogy állítsa `mode` való `all`. Ha beállította azt **összes**, csoportok és az összes erőforrástípus kiértékeli a házirendet. A portál alkalmaz **összes** az összes házirendhez. Ha a PowerShell vagy Azure CLI-t használ, meg kell adnia a `mode` paramétert, majd állítsa be **összes**.
 
Korábban a házirend kiértékelés csak a támogatott címkék és a hely erőforrás típusokon. A `indexed` mód továbbra is ezt a viselkedést. Ha használja a **összes** módban házirendeket is, amelyek nem támogatják a címkék és a hely erőforrástípusok alapján értékeli ki. [Virtuális hálózati alhálózat](https://github.com/Azure/azure-policy-samples/tree/master/samples/Network/enforce-nsg-on-subnet) egy példa egy újonnan hozzáadott típusú. Ezenkívül erőforráscsoportok értékelésének módja van beállítva **összes**. Például végezheti [címkéket az erőforráscsoport kényszerítése](https://github.com/Azure/azure-policy-samples/tree/master/samples/ResourceGroup/enforce-resourceGroup-tags). 

## <a name="parameters"></a>Paraméterek
Paraméterek használatával egyszerűbbé teszi a Csoportházirend kezelése a házirend-definíciók számának csökkentésével. Adja meg a házirendet (például korlátozza a hely, ahol erőforrásokat is telepíthető) erőforrás-tulajdonságot, és paraméterek bele. Ezt követően többször használ, hogy a házirend-definíció különböző helyzetek kezelésére történő különböző értékeket (például megadják az előfizetéshez tartozó helyek egy set) Ha a házirend hozzárendelése.

Ha a házirend-meghatározások létrehozása deklarálhatja paraméterek.

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

A paraméter típusa karakterlánc vagy tömb lehet. A metaadat-tulajdonságnak felhasználóbarát információk megjelenítésére szolgál például az Azure-portálon. 

A házirend szabályban hivatkozási paraméter a következő szintaxissal: 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Megjelenített név és leírás

Használja a **displayName** és **leírás** azonosíthatja a házirend-definíció, és adja meg a környezetben használat esetén.

## <a name="policy-rule"></a>Házirend szabályai

A házirendszabály áll **Ha** és **majd** blokkolja. Az a **Ha** blokk, megadhatja, hogy egy vagy több feltételt, adja meg, ha a házirend érvényesítve van-e. Logikai operátorok ezek a feltételek pontosan meghatározni a forgatókönyvhöz, amelyben egy házirendet alkalmazhat. Az a **majd** blokk, megadhatja a hatást, amelyet akkor fordul elő, amikor a **Ha** feltételek teljesülnek.

```json
{
  "if": {
    <condition> | <logical operator>
  },
  "then": {
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Logikai operátorok
A támogatott logikai operátorok a következők:

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
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Használatakor a **például** feltétel, megadhatja a helyettesítő karakter (*) értékét.

Használatakor a **megfelelő** feltétel, adja meg `#` képviselő számjegy, `?` betűvel, és bármely más karaktert adott tényleges karakter helyettesítéséhez. Tekintse meg a [nevét és az erőforrás-szabályzatok alkalmazása](resource-manager-policy-naming-convention.md).

### <a name="fields"></a>Mezők
Feltételek mezőkkel jöttek létre. A mező képviseli tulajdonság az erőforrás-kérések forgalma, amellyel ismertetik az erőforrás állapotát.  

A következő mezők támogatottak:

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*` 
* tulajdonságának aliasokat - listájáért lásd: [aliasok](#aliases).

### <a name="effect"></a>Következmény
Házirend érvénybe - három típusú támogat `deny`, `audit`, `append`, `AuditIfNotExists`, és `DeployIfNotExists`. 

* **Megtagadási** generál egy eseményt, a biztonsági naplóban, és a kérelem sikertelen
* **Naplózási** naplót hoz létre a figyelmeztető üzenet, de nem tudja teljesíteni a kérést
* **Hozzáfűzendő** a definiált mezők halmaza alapján ad a kéréshez 
* **AuditIfNotExists** -naplózást, ha egy erőforrás nem létezik.
* **DeployIfNotExists** -erőforrás telepíteni, ha még nem létezik. Ebből a célból beépített házirendeken keresztül jelenleg csak támogatott.

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

A **AuditIfNotExists** és **DeployIfNotExists**, értékelje ki a gyermek-erőforrás meglétét, és olyan szabályt alkalmaz, ha az adott erőforrás nem létezik. Például megkövetelheti, hogy egy hálózati figyelőt az összes virtuális hálózathoz van-e telepítve.

Példa a naplózást, ha egy virtuálisgép-bővítmény nincs telepítve, tekintse meg [naplózási Virtuálisgép-bővítmények](https://github.com/Azure/azure-policy-samples/blob/master/samples/Compute/audit-vm-extension/azurepolicy.json).

## <a name="aliases"></a>Aliasok

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
| kiszolgálók/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Állítsa be a rugalmas adatbáziskészlet az összes megosztott DTU. |
| kiszolgálók/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Állítsa be a rugalmas készlet kiadása. |

**Microsoft.Storage/storageAccounts**

| Alias | Leírás |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Állítsa be a hozzáférési réteg számlázási használatos. |
| Microsoft.Storage/storageAccounts/accountType | Állítsa be a termékváltozat. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Állítsa be, hogy a szolgáltatás titkosítja az adatokat a blob storage szolgáltatásban tárolt. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Állítsa be, hogy a szolgáltatás titkosítja az adatokat, a fájl storage szolgáltatásban tárolt. |
| Microsoft.Storage/storageAccounts/sku.name | Állítsa be a termékváltozat. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Beállítása, hogy a csak https-forgalom tároló szolgáltatást. |

## <a name="policy-sets"></a>Házirend beállítása

Házirend beállítása lehetővé teszi több kapcsolódó házirend-definíciók csoportban. A házirend-készlet egyszerűbbé teszi hozzárendelése és felügyeleti, mivel használata csak egy elemet egy csoportba. Például egyetlen házirend csoportban lévő összes kapcsolódó címkézési házirendek csoportosíthatja. Ahelyett, hogy minden egyes házirend hozzárendelése egyenként, alkalmazza a házirend beállítása.
 
A következő példa bemutatja, hogyan két címkét (costCenter és productName) kezelése a beállított házirend létrehozásához. Az alapértelmezett címke alkalmazza, és a címke értékének a két beépített házirendek használ. A házirend-készlet két paramétert, costCenterValue és a újrahasznosításának productNameValue deklarálja. A két beépített házirend-definíciók eltérő paraméterekkel rendelkező többször hivatkozik. Mindegyik paraméterhez vagy megadhatja egy rögzített érték a tagName ahogy, vagy egy paraméter, a házirend beállítása, a tagValue látható módon.

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

Állítsa be a szabályzat hozzáadása a **New-AzureRMPolicySetDefinition** PowerShell-parancsot.

REST-műveletek, használja a **2017 06-01. dátumú előnézeti** API-verzió, az alábbi példában látható módon:

```
PUT /subscriptions/<subId>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicySet?api-version=2017-06-01-preview
```

## <a name="next-steps"></a>Következő lépések
* Házirend szabály megadása után rendelje hozzá hatókör. A portálon keresztül házirendek rendeléséhez lásd: [hozzárendelésére és kezelésére erőforrás-házirendek használata Azure-portálon](resource-manager-policy-portal.md). REST API-t, a PowerShell vagy az Azure CLI-házirendeket rendeléséhez lásd: [meg és kezelheti a parancsfájl-házirendeket](resource-manager-policy-create-assign.md).
* Például a házirendek, lásd: [Azure-erőforrás házirend GitHub-tárházban](https://github.com/Azure/azure-policy-samples).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
* A házirend séma közzé van téve a [http://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](http://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json). 

