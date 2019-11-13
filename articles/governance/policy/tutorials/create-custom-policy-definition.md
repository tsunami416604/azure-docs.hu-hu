---
title: Egyéni szabályzatdefiníció létrehozása
description: Az egyéni üzleti szabályok betartatásához a Azure Policy egyéni szabályzat-definícióját kell megalkotni.
ms.date: 04/23/2019
ms.topic: tutorial
ms.openlocfilehash: 1a5be5a3e81dec6f4369e6b01dcda3d5de5f6dac
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959271"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Oktatóanyag: egyéni szabályzat-definíció létrehozása

Az egyéni szabályzatok definíciója lehetővé teszi, hogy az ügyfelek definiálják saját szabályaikat az Azure használatához. Ezek a szabályok gyakran kényszerítik a következőket:

- Biztonsági eljárások
- Cost Management
- Szervezetre vonatkozó szabályok (például elnevezés vagy hely)

Függetlenül attól, hogy az üzleti illesztőprogram egyéni házirendet hoz létre, a lépések ugyanazok, mint az új egyéni házirend definiálásához.

Egyéni házirend létrehozása előtt tekintse meg a [szabályzat mintáit](../samples/index.md) , és ellenőrizze, hogy már létezik-e az igényeinek megfelelő szabályzat.

Az egyéni szabályzatok létrehozásának módszere az alábbi lépésekből áll:

> [!div class="checklist"]
> - Az üzleti követelmények meghatározása
> - Minden követelmény hozzárendelése egy Azure Resource tulajdonsághoz
> - A tulajdonság hozzárendelése aliashoz
> - A használni kívánt effektus meghatározása
> - A szabályzat definíciójának összeállítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="identify-requirements"></a>Követelmények azonosítása

A házirend-definíció létrehozása előtt fontos megérteni a szabályzat céljait. Ebben az oktatóanyagban egy közös nagyvállalati biztonsági követelményt használunk, amely a következő lépéseket szemlélteti:

- Minden Storage-fióknak engedélyezve kell lennie a HTTPS-hez
- A Storage-fiókoknak le kell tiltaniuk a HTTP-t

A követelményeknek egyértelműen azonosítaniuk kell mind a "to", mind a "nem lehet" erőforrás-állapotot.

Noha meghatározta az erőforrás várható állapotát, még nem definiálta, hogy mit szeretne tenni a nem megfelelő erőforrásokkal. Azure Policy számos [effektust](../concepts/effects.md)támogat. Ebben az oktatóanyagban definiáljuk az üzleti követelményt, amely megakadályozza az erőforrások létrehozását, ha nem felelnek meg az üzleti szabályoknak. Ennek a célnak a teljesítéséhez a [Megtagadás](../concepts/effects.md#deny) effektust fogjuk használni. Azt is szeretnénk, hogy az adott hozzárendelésekre vonatkozó szabályzatot felfüggessze. Ezért a [letiltott](../concepts/effects.md#disabled) effektust fogjuk használni, és a [paramétereket](../concepts/definition-structure.md#parameters) a házirend-definícióban kell végrehajtani.

## <a name="determine-resource-properties"></a>Erőforrás-tulajdonságok meghatározása

Az üzleti követelménytől függően az Azure-erőforrás, amellyel a Azure Policy naplózni kívánja a Storage-fiókot. Azonban nem tudjuk, hogy milyen tulajdonságokat kell használni a házirend-definícióban. Azure Policy kiértékeli az erőforrás JSON-ábrázolását, ezért meg kell értenünk az adott erőforráshoz elérhető tulajdonságokat.

Az Azure-erőforrások tulajdonságai számos módon meghatározhatók. Ebben az oktatóanyagban a következőket fogjuk megtekinteni:

- Resource Manager-sablonok
  - Meglévő erőforrás exportálása
  - Létrehozási élmény
  - Gyorsindítás sablonok (GitHub)
  - Sablon-dokumentációs dokumentumok
- Azure Resource Explorer

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

A felügyelni kívánt tulajdonságot több módon is megtekintheti egy [Resource Manager-sablonban](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) .

#### <a name="existing-resource-in-the-portal"></a>Meglévő erőforrás a portálon

A tulajdonságok megkeresésének legegyszerűbb módja egy azonos típusú meglévő erőforrás megkeresése. A kényszeríteni kívánt beállítással már konfigurált erőforrások is megadják az összehasonlításhoz használandó értéket.
Tekintse meg a **sablon exportálása** lapot (a **Beállítások**alatt) az adott erőforráshoz tartozó Azure Portalban.

![Sablon exportálása lap a meglévő erőforráson](../media/create-custom-policy-definition/export-template.png)

A Storage-fiók esetében a következőhöz hasonló sablon látható:

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

A **Tulajdonságok** területen a **supportsHttpsTrafficOnly** nevű érték false ( **hamis**) értékűre van állítva. Ez a tulajdonság úgy tűnik, hogy a keresett tulajdonság lehet. Emellett az erőforrás **típusa** **Microsoft. Storage/storageAccounts**. A típus lehetővé teszi, hogy csak az ilyen típusú erőforrásokra korlátozza a szabályzatot.

#### <a name="create-a-resource-in-the-portal"></a>Erőforrás létrehozása a portálon

A portálon keresztül egy másik módszer az erőforrás-létrehozási élmény. Amikor a portálon keresztül hoz létre egy Storage-fiókot, a **speciális** lapon található lehetőség a **biztonságos átvitelre van szükség**. Ez a tulajdonság _letiltott_ és _engedélyezett_ beállításokat tartalmaz. Az info ikon további szöveggel rendelkezik, amely megerősíti, hogy ez a beállítás valószínűleg a kívánt tulajdonság. A portálon azonban nem jelennek meg a tulajdonságok neve ezen a képernyőn.

A **felülvizsgálat + létrehozás** lapon egy hivatkozás található az oldal alján egy **sablon az automatizáláshoz való letöltéséhez**. A hivatkozás kiválasztásával megnyílik a konfigurált erőforrást létrehozó sablon. Ebben az esetben két fő információt látunk:

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

Ez az információ azt jelzi, hogy a tulajdonság típusa és a **supportsHttpsTrafficOnly** is megerősíti a keresett tulajdonságot.

#### <a name="quickstart-templates-on-github"></a>Gyors útmutató sablonok a GitHubon

A GitHubon futó Azure rövid útmutató [sablonjai](https://github.com/Azure/azure-quickstart-templates) több száz Resource Manager-sablonnal rendelkeznek, amelyek különböző erőforrásokhoz készültek. Ezek a sablonok nagyszerű módot biztosítanak a keresett erőforrás-tulajdonság megkeresésére. Előfordulhat, hogy egyes tulajdonságok úgy tűnik, hogy mit keres, de mást is vezérel.

#### <a name="resource-reference-docs"></a>Erőforrás-referenciák dokumentációja

A **supportsHttpsTrafficOnly** helyes tulajdonságának ellenőrzéséhez ellenőrizze a Storage- [fiók erőforrásának](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) Resource Manager-sablonra vonatkozó hivatkozását a tárolási szolgáltatón.
A Properties objektum érvényes paraméterek listáját tartalmazza. Az [StorageAccountPropertiesCreateParameters-Object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) hivatkozás kiválasztásával egy elfogadható tulajdonságokat tartalmazó táblázat látható. **supportsHttpsTrafficOnly** jelennek meg, és a Leírás megfelel az üzleti igényeknek.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Az Azure-erőforrások megismerésének egy másik módja a [Azure erőforrás-kezelő](https://resources.azure.com) (előzetes verzió). Ez az eszköz az előfizetés kontextusát használja, ezért az Azure-beli hitelesítő adataival kell hitelesítenie a webhelyet. A hitelesítés után a szolgáltatók, előfizetések, erőforráscsoportok és erőforrások között böngészhet.

Keresse meg a Storage-fiók erőforrását, és tekintse meg a tulajdonságokat. Itt a **supportsHttpsTrafficOnly** tulajdonság is látható. A **dokumentáció** lapon láthatjuk, hogy a tulajdonság leírása megegyezik a korábban a hivatkozási dokumentációban találhatókkal.

## <a name="find-the-property-alias"></a>A tulajdonság aliasnevének megkeresése

Azonosította az erőforrás-tulajdonságot, de a tulajdonságot egy [aliasra](../concepts/definition-structure.md#aliases)kell leképeznie.

Az Azure-erőforrások aliasait többféleképpen is meghatározhatja. Ebben az oktatóanyagban a következőket fogjuk megtekinteni:

- Azure CLI
- Azure PowerShell
- Azure Resource Graph

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben az `az provider`-parancs az erőforrás-aliasok keresésére szolgál. A **Microsoft. Storage** névterét a korábban az Azure-erőforrással kapcsolatban kapott részletek alapján szűrheti.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Az eredmények között a **supportsHttpsTrafficOnly**nevű Storage-fiókok által támogatott alias látható. Ennek az aliasnak a létezése azt jelenti, hogy megírhatjuk a szabályzatot az üzleti követelmények érvénybe léptetéséhez.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell a `Get-AzPolicyAlias` parancsmag az erőforrás-aliasok keresésére szolgál. A **Microsoft. Storage** névterét a korábban az Azure-erőforrással kapcsolatban kapott részletek alapján szűrheti.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Az Azure CLI-hez hasonlóan az eredmények a **supportsHttpsTrafficOnly**nevű Storage-fiókok által támogatott aliast jelenítik meg.

### <a name="azure-resource-graph"></a>Azure Resource Graph

Az [Azure Resource Graph](../../resource-graph/overview.md) egy új, előzetes verzióban elérhető szolgáltatás. Lehetővé teszi egy másik módszer számára az Azure-erőforrások tulajdonságainak megkeresését. Az alábbi példa egy olyan lekérdezési lekérdezést mutat be, amely egyetlen Storage-fiókot keres az erőforrás-Gráfmal:

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Az eredmények ugyanúgy néznek ki, mint a Resource Manager-sablonokban és a Azure Erőforrás-kezelő. Az Azure Resource Graph eredményei azonban _az aliasok tömb_ _kivetítésével_ is tartalmazhatják az [alias](../concepts/definition-structure.md#aliases) részleteit:

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Íme egy példa az aliasokhoz tartozó Storage-fiók kimenetére:

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

Az Azure Resource Graph (előzetes verzió) [Cloud Shell](https://shell.azure.com)használatával is használható, így gyorsan és egyszerűen feltárhatja az erőforrások tulajdonságait.

## <a name="determine-the-effect-to-use"></a>A használandó effektus meghatározása

Annak eldöntése, hogy mi a teendő a nem megfelelő erőforrásokkal, majdnem olyan fontos, mint az első helyen való kiértékelés eldöntése. A nem megfelelő erőforrásokra adott lehetséges válaszokat a rendszer [effektusnak](../concepts/effects.md)nevezzük.
A hatás azt szabályozza, hogy a nem megfelelő erőforrás van-e naplózva, letiltva, van-e hozzáfűzve vagy van-e hozzárendelve az erőforrás megfelelő állapotba helyezéséhez szükséges központi telepítéshez.

A megtagadási példa a mi az a hatása, ahogy nem szeretnénk, hogy az Azure-környezetben létrehozott nem megfelelő erőforrások ne legyenek. A naplózás jó választás a házirendek hatására, hogy megtudja, milyen hatással van a szabályzat, mielőtt a rendszer megtagadja a beállítást. Az egyes hozzárendelések hatásának megváltoztatásának egyik módja a parametrizálja. A részletekért lásd az alábbi [paramétereket](#parameters) .

## <a name="compose-the-definition"></a>A definíció összeállítása

Most már megtörtént a tulajdonságok részletei és aliasa a felügyelni kívánt tervekhez. Ezután saját maga alkotja a házirend-szabályt. Ha még nem ismeri a házirend nyelvét, a hivatkozási szabályzat [definíciójának struktúrája](../concepts/definition-structure.md) a házirend-definíció strukturálása. Itt látható egy üres sablon arról, hogy a szabályzat definíciója hogyan néz ki:

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

Az első három összetevő a szabályzat metaadatai. Ezek az összetevők egyszerűen biztosítanak értékeket, mert tudjuk, mi hozza létre a szabályt. A [Mode](../concepts/definition-structure.md#mode) elsődlegesen a címkék és az erőforrás helye. Mivel nem kell a címkéket támogató erőforrásokra korlátozni a kiértékelést, a _minden_ értéket használni fogjuk **.**

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Paraméterek

Habár nem használunk paramétert a kiértékelés módosításához, egy paraméterrel szeretnénk engedélyezni a hibaelhárítási **effektus** módosítását. Definiálunk egy **effectType** paramétert, és csak a **Megtagadás** és a **Letiltva**értékre korlátozzuk. Ez a két lehetőség megfelel az üzleti követelményeknek. A befejezett paraméterek blokk a következő példához hasonlít:

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

A házirend- [szabály](../concepts/definition-structure.md#policy-rule) összeállítása az egyéni szabályzat-definíció kiépítése utolsó lépése. A teszteléshez két utasítást azonosítottak:

- A Storage-fiók **típusa** **Microsoft. Storage/storageAccounts**
- A Storage-fiók **supportsHttpsTrafficOnly** nem **igaz**

Mivel mindkét utasításnak igaz értékűnek kell lennie, a **allOf** [logikai operátort](../concepts/definition-structure.md#logical-operators)használjuk. Statikus deklaráció helyett a **effectType** paramétert adjuk át a hatásnak. A befejezett szabály a következő példához hasonlít:

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

### <a name="completed-definition"></a>Befejezett definíció

A szabályzat mindhárom részének meghatározása után itt látható a befejezett definíció:

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

Az elkészült definíció használatával új szabályzat hozható létre. A portál és az egyes SDK-kat (Azure CLI, Azure PowerShell és REST API) különböző módokon fogadja el a definíciót, ezért tekintse át az egyes parancsokat a helyes használat ellenőrzéséhez. Ezután rendelje hozzá a paraméteres hatás használatával a megfelelő erőforrásokhoz a Storage-fiókok biztonságának kezeléséhez.

## <a name="review"></a>Áttekintés

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> - Az üzleti igények azonosítása
> - Az egyes követelmények leképezve egy Azure Resource tulajdonsághoz
> - A tulajdonság hozzárendelése egy aliashoz
> - A használandó effektus meghatározása
> - A szabályzat definíciójának tagjai

## <a name="next-steps"></a>Következő lépések

Ezután az egyéni házirend-definíció használatával hozzon létre és rendeljen hozzá egy házirendet:

> [!div class="nextstepaction"]
> [Szabályzat-definíció létrehozása és társítása](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)