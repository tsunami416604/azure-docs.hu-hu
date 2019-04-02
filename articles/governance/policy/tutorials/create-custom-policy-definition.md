---
title: Egyéni szabályzatdefiníció létrehozása
description: Egyéni szabályzat-definíció egyéni üzleti szabályok érvényesítése az Azure Policy írhat.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 79b99532f5fb38123b03d2a39b7c9c6364e9f636
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802269"
---
# <a name="create-a-custom-policy-definition"></a>Egyéni szabályzatdefiníció létrehozása

Egyéni szabályzat-definíció lehetővé teszi a felhasználóknak a saját Azure használatára vonatkozó szabályok meghatározásához. Ezek a szabályok gyakran kényszerítése:

- Biztonsági eljárások
- Cost Management
- Szervezetekre vonatkozó szabályok (például elnevezési vagy helyek)

Függetlenül a stratégiai egyéni házirend létrehozásának a lépései megegyeznek az új egyéni szabályzat definiálása.

Egyéni házirend létrehozása előtt ellenőrizze a [házirend minták](../samples/index.md) megtekintéséhez, hogy létezik-e egy szabályzatot, amely már megfelel az igényeinek.

Az egyéni házirend létrehozása megközelítése kövesse az alábbi lépéseket:

> [!div class="checklist"]
> - Az üzleti követelményeinek azonosítása
> - Egyes követelmények leképezése egy Azure-erőforrás-tulajdonság
> - A tulajdonság leképezése egy aliast
> - Határozza meg, milyen hatást használata
> - A szabályzatdefiníció Compose

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="identify-requirements"></a>Követelmények azonosítása

Mielőtt létrehozná a szabályzatdefiníció, fontos tudni, hogy a szabályzat célja. Ebben az oktatóanyagban egy általános vállalati biztonsági követelményeknek használjuk, a cél a lépéseit mutatja be:

- Minden tárfióknak engedélyezni kell a HTTPS
- Minden tárfióknak le kell tiltani a HTTP-hez

A követelmények egyértelműen azonosítani mind a "" és a "nem az" erőforrás-állapotok.

Hogy meghatároztuk a várható állapotát az erőforrást, miközben még nincs definiálva, amit szeretnénk végrehajtott rendelkező nem megfelelő erőforrások. A házirend szélesebbé váló skáláját támogatja [hatások](../concepts/effects.md). Ebben az oktatóanyagban az üzleti követelményt fogunk meghatározni, ha azok nem felelnek meg az üzleti szabályok megakadályozza, hogy az erőforrások létrehozását. E cél elérése érdekében használjuk a [Megtagadás](../concepts/effects.md#deny) érvénybe. Szeretnénk továbbá biztosítani arra, hogy a szabályzat az egyes hozzárendelések felfüggesztése. Ezért használjuk a [letiltott](../concepts/effects.md#disabled) életbe lépjenek, és adja meg azt a hatást egy [paraméter](../concepts/definition-structure.md#parameters) a szabályzat-definícióban.

## <a name="determine-resource-properties"></a>Erőforrás-tulajdonságok meghatározása

Alapján az üzleti követelményt, az Azure-erőforráshoz naplózási házirend-e.
A tulajdonságait a szabályzat-definícióban nem tudjuk. Szabályzat a JSON-reprezentációja az erőforráshoz értékeli, ezért meg kell tudni, hogy az erőforráson elérhető tulajdonságok.

Számos módon az Azure-beli erőforráshoz tulajdonságok meghatározásához. Megnézzük, minden egyes, ebben az oktatóanyagban:

- Resource Manager-sablonok
  - Meglévő erőforrás exportálása
  - Létrehozási folyamatának
  - Gyorsindítási sablonok (GitHub)
  - Sablon útmutató dokumentumok
- Azure Resource Explorer

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

Többféleképpen is meg egy [Resource Manager-sablon](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) , amely tartalmazza a tulajdonság szeretne kezelni.

#### <a name="existing-resource-in-the-portal"></a>Meglévő erőforrást a portálon

A legegyszerűbben úgy tulajdonságait, hogy tekintse meg az azonos típusú meglévő erőforrást. Erőforrások már be van állítva a beállítás kényszeríteni szeretné a érték is biztosítanak.
Tekintse meg a **Automation-szkript** lap (alatt **beállítások**) az Azure Portalon, hogy az adott erőforráshoz.

![Meglévő erőforrás sablon oldal exportálása](../media/create-custom-policy-definition/automation-script.png)

Így a storage-fiókok tárja fel a sablonból példához hasonlóak:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

A **tulajdonságok** nevű érték **supportsHttpsTrafficOnly** beállítása **hamis**. Ez a tulajdonság az látszik, hogy elképzelhető, hogy számíthatunk a tulajdonságot. Emellett a **típus** , de az erőforrás **Microsoft.Storage/storageAccounts**. A típus teszi lehetővé az ilyen típusú erőforrások kizárólag a házirend korlátozza.

#### <a name="create-a-resource-in-the-portal"></a>Erőforrás létrehozása a portálon

A portálon keresztül egy másik módja az erőforrás-létrehozási folyamatának. A portálon, a menüben található egy storage-fiók létrehozása során a **speciális** lap **biztonsági átvitelre van szükség**.
Ez a tulajdonság _letiltott_ és _engedélyezve_ beállítások. Az információs ikon, amely megerősíti, hogy ez a beállítás valószínűleg a tulajdonság azt szeretnénk, hogy a rendszer további szöveget rendelkezik. Azonban a portál nem ossza meg velünk a tulajdonságnév ezen a képernyőn.

Az a **felülvizsgálat + létrehozása** lap hivatkozása a lap alján **automatizálási sablon letöltése**. A hivatkozásra kattintva megnyílik a sablont, amely létrehozza az erőforrás konfiguráltuk. Ebben az esetben két kulcsfontosságú adatokat fogjuk látni:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Ez az információ tudatja velünk, a tulajdonság típusa, és szintén megerősíti, hogy **supportsHttpsTrafficOnly** számíthatunk a tulajdonság.

#### <a name="quickstart-templates-on-github"></a>Gyorsindítási sablonok github

A [Azure gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates) a Githubon rendelkezik a különböző erőforrások számára készült Resource Manager-sablonokkal több száz. Ezek a sablonok rejlő lehetőségeket találja a keresett erőforrás-tulajdonságot is lehet. Egyes tulajdonságok a tűnnek, amit keres, de más szabályozhatja.

#### <a name="resource-reference-docs"></a>Erőforrás útmutató dokumentumok

Ellenőrzése **supportsHttpsTrafficOnly** , javítsa ki a tulajdonságot, akkor ellenőrizze a Resource Manager sablonreferenciája a [tárfiók típusú erőforrást](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) a tárolási szolgáltatót.
A Tulajdonságok objektumnak érvényes paramétereinek listáját. Válassza a [StorageAccountPropertiesCreateParameters-objektum](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) hivatkozás elfogadható tulajdonságok táblázatát jeleníti meg. **supportsHttpsTrafficOnly** megtalálható és a leírás megegyezik, amit mi keres az üzleti szükségletek kielégítése céljából.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Ismerkedés az Azure-erőforrások egy másik módja, keresztül a [Azure erőforrás-kezelő](https://resources.azure.com) (előzetes verzió). Ez az eszköz az előfizetés keretében használ, így a kell elvégeznie a hitelesítést, az Azure-beli hitelesítő adataival. A hitelesítést követően megkeresheti a szolgáltatók, előfizetések, erőforráscsoportok és erőforrások.

Keresse meg a tárfiók típusú erőforrást, és tekintse meg a tulajdonságait. Láthatjuk, a **supportsHttpsTrafficOnly** tulajdonság itt is. Válassza a **dokumentáció** lapon látható, hogy a tulajdonság leírása megegyezik-e a mi találtunk az útmutató dokumentumok a korábban.

## <a name="find-the-property-alias"></a>Keresse meg a tulajdonság alias

Az erőforrás-tulajdonsághoz azonosítottunk, de képezze le az adott tulajdonságot kell egy [alias](../concepts/definition-structure.md#aliases).

Néhány módon határozza meg az aliasok az Azure-beli erőforráshoz. Megnézzük, minden egyes, ebben az oktatóanyagban:

- Azure CLI
- Azure PowerShell
- Azure Resource Graph

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben a `az provider` parancscsoport erőforrás aliasok kereséséhez használja. A szűrjük a **Microsoft.Storage** névtér kaptunk az Azure-erőforrás kapcsolatos korábbi adatok alapján.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Az eredmények között, láthatjuk, nevű storage-fiókok által támogatott alias **supportsHttpsTrafficOnly**. Ez az alias megléte azt jelenti, hogy a szabályzat kényszerítéséhez az üzleti követelmények is írunk!

### <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell a `Get-AzPolicyAlias` parancsmag segítségével keresse meg az erőforrás-aliasok.
A szűrjük a **Microsoft.Storage** névtér kaptunk az Azure-erőforrás kapcsolatos korábbi adatok alapján.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Azure CLI-vel, például az eredmények megjelenítése nevű storage-fiókok által támogatott alias **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Az Azure Erőforrás-grafikon](../../resource-graph/overview.md) egy új szolgáltatás előzetes verzióban érhető el. Keresse meg az Azure-erőforrások tulajdonságait is lehetővé teszi. Itt látható egy minta-lekérdezést az egy tárfiókban megnézzük az Erőforrás-grafikon:

```Query
where type=~'microsoft.storage/storageaccounts' | limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Az eredmények mi látható az a Resource Manager-sablonokkal és az Azure erőforrás-kezelőben hasonlóan néz ki. Azonban az Azure Erőforrás-grafikon is kiterjed [alias](../concepts/definition-structure.md#aliases) részleteit. Íme egy példa kimenet aliasok storage-fiókból:

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Az Azure Erőforrás-grafikon (előzetes verzió) segítségével keresztül [Cloud Shell](https://shell.azure.com), így egy gyors és egyszerű módja az erőforrások tulajdonságait.

## <a name="determine-the-effect-to-use"></a>Határozza meg a hatást használata

Annak eldöntése, hogy mi történjen a nem megfelelő erőforrások a szinte olyan fontos, mint annak eldöntésében, hogy mit kell kiértékelni az elsőként. Minden lehetséges válasz nem kompatibilis erőforrás neve egy [érvénybe](../concepts/effects.md). A hatás szabályozza a nem megfelelő erőforrást a rendszer naplózza, le van tiltva, ha rendelkezik adatokat hozzáfűzi, vagy rendelkezik egy központi telepítési társítva van hozzá tartozó erőforrás vissza és a egy megfelelő állapotba kerülnek.

A példánkban a Megtagadás a hatása, mivel nem szeretnénk létrehozása Azure környezetben a nem megfelelő erőforrások szeretnénk. Naplózási megfelelő első választás az olyan meghatározásához a házirend hatásának előtt beállítása a letiltva a szabályzat hatása. Egyik módja, hogy a hatás könnyebben hozzárendelés kiszolgálónként módosítása paraméterezni hatással. Lásd: [paraméterek](#parameters) alább a részleteket az.

## <a name="compose-the-definition"></a>A definíció Compose

Most már a tulajdonságlapon és alias mi tervezzük kezeléséhez. Ezután azt fogja magát a szabály compose. Ha még nem ismeri a házirend-nyelve, hivatkozhat [szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) hogyan építse fel a szabályzat-definíció. Íme egy üres sablonnal, egy szabályzatdefiníciót néz ki:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metaadatok

Az első három összetevői a szabályzat-metaadatai. Ezek az összetevők is egyszerűen adja meg az értékeket, mivel tudjuk, hogy milyen hozunk létre a szabályt. [Mód](../concepts/definition-structure.md#mode) elsősorban a címkék és erőforrás helyének kapcsolatban van. Korlátozza az értékelést címkék támogató erőforrások nincs szükségünk, mivel fogjuk használni a _összes_ értékét **mód**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Paraméterek

Paraméter nem használjuk a kiértékelés változó, bár szeretnénk egy paraméter használatával lehet módosítani a **érvénybe** hibaelhárításhoz. Fogunk meghatározni egy **effectType** paramétert, és korlátozza azt egyetlen **Megtagadás** és **letiltott**. A két lehetőség felel meg az üzleti követelmények. A befejezett paraméterek letiltása a példához hasonlóan néz ki:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Szabályzatbeli szabály

Összeállítása a [felügyeletiházirend-szabálya](../concepts/definition-structure.md#policy-rule) az utolsó lépés az egyéni szabályzat-definíció épületben. Ennek teszteléséhez két utasítással azonosítottunk:

- Hogy a tárfiók **típus** van **Microsoft.Storage/storageAccounts**
- Hogy a tárfiók **supportsHttpsTrafficOnly** nem **igaz**

Mindkettő igaz utasítások szükségünk, mivel fogjuk használni a **allOf** [logikai operátor](../concepts/definition-structure.md#logical-operators). Adjuk át fogjuk a **effectType** már nem kell egy statikus deklarace hatása paramétert. A befejezett szabály példához hasonlóan néz ki:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Befejezett definíciója

A megadott házirend három része az itt látható a befejezett definíciója:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

A befejezett-definíció segítségével hozzon létre egy új szabályzatot. Fogadja el a definíció különböző módon, ezért tekintse át a megfelelő használati ellenőrzése minden egyes parancsok portál és minden egyes SDK-t (Azure CLI, Azure PowerShell és REST API-t). Ezután rendelje hozzá, a paraméteres hatással, a megfelelő erőforrások kezelése a storage-fiókok biztonságát.

## <a name="review"></a>Áttekintés

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> - Azonosítani az üzleti igényeknek
> - Egyes követelmények leképezve egy Azure-erőforrás-tulajdonság
> - A tulajdonság rendelve egy aliast
> - Határozza meg a hatást használata
> - Mikroszolgáltatásokból álló a szabályzat-definíció

## <a name="next-steps"></a>További lépések

Ezután használja az egyéni szabályzatdefiníció a szabályzat létrehozása és hozzárendelése:

> [!div class="nextstepaction"]
> [Egy szabályzat-definíció létrehozása és hozzárendelése](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)