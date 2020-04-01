---
title: 'Oktatóanyag: Egyéni házirend-definíció létrehozása'
description: Ebben az oktatóanyagban egyéni szabályzatdefiníciót hoz létre az Azure-szabályzathoz, amely egyéni üzleti szabályokat kényszerít az Azure-erőforrásokra.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: f7c303956b209b88ce3c697b5b66243e37071c83
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238943"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Oktatóanyag: Egyéni házirend-definíció létrehozása

Az egyéni szabályzat-definíció lehetővé teszi az ügyfelek számára, hogy saját szabályokat határozzanak meg az Azure használatával. Ezek a szabályok gyakran érvényt szereznek:

- Biztonsági eljárások
- Cost Management
- Szervezetspecifikus szabályok (például elnevezés vagy helyek)

Az egyéni házirend létrehozásához az üzleti illesztőprogramtól függetlenül a lépések megegyeznek az új egyéni házirend meghatározásához.

Egyéni házirend létrehozása előtt ellenőrizze a [szabályzatmintákat,](../samples/index.md) hogy létezik-e már az igényeinek megfelelő házirend.

Az egyéni házirendek létrehozásának az alábbi lépéseket követi:

> [!div class="checklist"]
> - Azonosítsa üzleti igényeit
> - Minden követelmény leképezése egy Azure-erőforrás-tulajdonsághoz
> - A tulajdonság leképezése aliashoz
> - Határozza meg, hogy melyik hatást használja
> - A házirend-definíció megírása

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="identify-requirements"></a>Követelmények azonosítása

A szabályzatdefiníció létrehozása előtt fontos megérteni a házirend szándékát. Ebben az oktatóanyagban egy közös vállalati biztonsági követelményt fogunk használni a következő lépések szemléltetésére:

- Minden tárfiókot engedélyezni kell a HTTPS-hez
- Minden tárfiókot le kell tiltani a HTTP-hez

A követelményeknek egyértelműen meg kell határozniuk mind a "lenni" és a "nem lehet" erőforrás-állapotokat.

Bár meghatároztuk az erőforrás várható állapotát, még nem határoztuk meg, hogy mit akarunk tenni a nem megfelelő erőforrásokkal. Az Azure Policy számos [effektust](../concepts/effects.md)támogat. Ebben az oktatóanyagban az üzleti követelményt úgy határozzuk meg, hogy megakadályozzuk az erőforrások létrehozását, ha azok nem felelnek meg az üzleti szabályoknak. E cél elérése érdekében a [Megtagadás](../concepts/effects.md#deny) hatást használjuk. Azt is szeretnénk, hogy a lehetőséget, hogy függessze fel a politika az egyes feladatok. Mint ilyen, a [Letiltott effektust használjuk,](../concepts/effects.md#disabled) és a hatást a házirend-definíció [ban paraméterként](../concepts/definition-structure.md#parameters) használjuk.

## <a name="determine-resource-properties"></a>Erőforrás-tulajdonságok meghatározása

Az üzleti követelmény alapján az Azure-erőforrás naplózása az Azure Policy egy tárfiók. Azonban nem ismerjük a házirend-definícióban használandó tulajdonságokat. Az Azure Policy kiértékeli az erőforrás JSON-ábrázolását, ezért meg kell értenünk az adott erőforráson elérhető tulajdonságokat.

Egy Azure-erőforrás tulajdonságainak meghatározásához számos módon határozható meg. Majd nézd meg az egyes ez a bemutató:

- Azure Policy-bővítmény VSCode-hoz
- Resource Manager-sablonok
  - Meglévő erőforrás exportálása
  - Létrehozási élmény
  - Gyorsútmutató-sablonok (GitHub)
  - Sablonreferencia-dokumentumok
- Azure Resource Explorer

### <a name="view-resources-in-vs-code-extension"></a>Erőforrások megtekintése a VS-kód bővítményben

A [VS-kód bővítmény](../how-to/extension-for-vscode.md#search-for-and-view-resources) segítségével tallózhat a környezetben lévő erőforrások között, és megtekintheti az erőforrás-kezelő tulajdonságait az egyes erőforrásokon.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

Az [Erőforrás-kezelő sablonja](../../../azure-resource-manager/templates/template-tutorial-create-encrypted-storage-accounts.md) többféleképpen is megtekinthet, amely tartalmazza a kezelni kívánt tulajdonságot.

#### <a name="existing-resource-in-the-portal"></a>Meglévő erőforrás a portálon

A tulajdonságok keresésének legegyszerűbb módja egy azonos típusú meglévő erőforrás vizsgálata. Az érvényesíteni kívánt beállítással már konfigurált erőforrások is biztosítják az összehasonlítandó értéket.
Tekintse meg a **sablon exportálása** lapot **(a Beállítások)** az Azure Portalon az adott erőforráshoz.

![Sablon exportálása lap meglévő erőforráson](../media/create-custom-policy-definition/export-template.png)

Ha egy tárfiókot szeretne, az a példához hasonló sablont tár fel:

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

A **tulajdonságok alatt** egy **supportsHttpsTrafficOnly** nevű érték **hamis.** Ez az ingatlan úgy néz ki, mintha ez lenne az ingatlan, amit keresünk. Az erőforrás **típusa** a **Microsoft.Storage/storageAccounts .** A típus lehetővé teszi, hogy a házirendet csak az ilyen típusú erőforrásokra korlátozzuk.

#### <a name="create-a-resource-in-the-portal"></a>Erőforrás létrehozása a portálon

A portálon keresztül egy másik út az erőforrás-létrehozási élmény. A portálon keresztül itárfiók létrehozásakor a **Speciális** lapon található **a biztonsági átvitel szükséges.** Ez a tulajdonság _letiltott_ és engedélyezett beállításokkal _rendelkezik._ Az információs ikon további szöveget tartalmaz, amely megerősíti, hogy ez a beállítás valószínűleg a kívánt tulajdonság. A portál azonban nem adja meg a képernyőn megjelenő tulajdonság nevét.

A **Véleményezés + létrehozás** lapon egy hivatkozás található a lap alján, **amelyen egy sablon letöltése automatizálási sablonhoz**. A hivatkozás kiválasztása megnyitja a sablont, amely létrehozza az általunk konfigurált erőforrást. Ebben az esetben két kulcsfontosságú információt látunk:

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

Ez az információ közli velünk a tulajdonság típusát, és megerősíti **támogatjaHttpsTrafficOnly** az a tulajdonság, amit keresünk.

#### <a name="quickstart-templates-on-github"></a>Gyorsútmutató sablonok a GitHubon

Az [Azure gyorsindítási sablonjai](https://github.com/Azure/azure-quickstart-templates) a GitHubon több száz Erőforrás-kezelő sablont hoznak fel különböző erőforrásokhoz. Ezek a sablonok nagyszerű módja lehet annak, hogy megtalálja a keresett erőforrás-tulajdonságot. Egyes tulajdonságok úgy tűnhet, hogy mit keres, de ellenőrzés valami mást.

#### <a name="resource-reference-docs"></a>Erőforrás-referencia-dokumentumok

A **támogatottak érvényesítéséhezAHttpsTrafficOnly** megfelelő tulajdonság, ellenőrizze az Erőforrás-kezelő sablon hivatkozási a [tárfiók erőforrás](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) a storage-szolgáltató.
A tulajdonságobjektum érvényes paraméterek et tartalmazó listával rendelkezik. A [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) hivatkozás kiválasztásával megjelenik az elfogadható tulajdonságok táblázata. **supportsHttpsTrafficOnly** jelen van, és a leírás megegyezik azzal, amit keresünk, hogy megfeleljen az üzleti követelményeknek.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Az Azure-erőforrások feltárásának másik módja az [Azure Resource Explorer](https://resources.azure.com) (előzetes verzió). Ez az eszköz az előfizetés környezetét használja, ezért az Azure-hitelesítő adatokkal hitelesítenie kell magát a webhelyre. A hitelesítést követően szolgáltatók, előfizetések, erőforráscsoportok és erőforrások szerint tallózhat.

Keresse meg a tárfiók-erőforrást, és tekintse meg a tulajdonságokat. Itt is látjuk a **támogatjaHttpsTrafficOnly** tulajdonságot. A **Dokumentáció** lapon azt látjuk, hogy a tulajdonság leírása megegyezik a korábbi hivatkozási dokumentumokban találttal.

## <a name="find-the-property-alias"></a>A tulajdonság aliasának megkeresése

Azonosítottuk az erőforrás tulajdonságot, de le kell képeznünk a tulajdonságot egy [aliasra.](../concepts/definition-structure.md#aliases)

Egy Azure-erőforrás aliasai többféleképpen is meghatározhatók. Majd nézd meg az egyes ez a bemutató:

- Azure Policy-bővítmény VSCode-hoz
- Azure CLI
- Azure PowerShell
- Azure Resource Graph

### <a name="get-aliases-in-vs-code-extension"></a>Aliasok bekéselése a VS Code bővítményben

Az Azure Policy bővítmény vs kód bővítmény megkönnyíti az erőforrások böngészését és [az aliasok felderítését.](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties)

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI,a `az provider` parancscsoport erőforrás-aliasok keresésére szolgál. A **Microsoft.Storage** névtér szűrése az Azure-erőforrással kapcsolatos korábbi adatok alapján.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Az eredmények egy aliast látunk, amelyet a **supportshttpsTrafficOnly**nevű tárfiókok támogatnak. Ez a létezése ez az alias azt jelenti, tudjuk írni a politika érvényesítésére üzleti követelmények!

### <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShellben a `Get-AzPolicyAlias` parancsmag erőforrás-aliasok keresésére szolgál. A **Microsoft.Storage** névtér szűrése az Azure-erőforrással kapcsolatos korábbi adatok alapján.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Az Azure CLI-hez hasonlóan az eredmények egy, a **supportsHttpsTrafficOnly**nevű tárfiókok által támogatott aliast jelenítenek meg.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Az Azure Resource Graph](../../resource-graph/overview.md) egy olyan szolgáltatás, amely egy másik módszert biztosít az Azure-erőforrások tulajdonságainak megkereséséhez. Az alábbiakban egy mintalekérdezést olvashat egyetlen tárfiókból a Resource Graph segítségével:

```kusto
Resources
| where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Az eredmények hasonlítanak az Erőforrás-kezelő sablonokban és az Azure Resource Exploreren keresztül láthatóhoz. Az Azure Resource Graph eredményei azonban [aliasrészleteket](../concepts/definition-structure.md#aliases) is tartalmazhatnak az _aliastömb_ _kivetítésével:_

```kusto
Resources
| where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Íme egy példa kimenet egy tárfiók aliasok:

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

Az Azure Resource Graph a [Cloud Shell](https://shell.azure.com)segítségével is használható, így gyorsan és egyszerűen megismerheti az erőforrások tulajdonságait.

## <a name="determine-the-effect-to-use"></a>A használandó hatás meghatározása

Annak eldöntése, hogy mit tegyen a nem megfelelő erőforrásokkal, majdnem olyan fontos, mint annak eldöntése, hogy mit értékeljen. A nem megfelelő erőforrásokra adott minden lehetséges választ [hatásnak](../concepts/effects.md)nevezzük. A hatás szabályozza, ha a nem megfelelő erőforrás naplózott, blokkolt, adatokat csatolt, vagy egy központi telepítés takar, hogy az erőforrás visszaad egy megfelelő állapotba.

Példánkban a Megtagadás az a hatás, amit szeretnénk, mivel nem szeretnénk, ha nem megfelelő erőforrásokat hoznánk létre az Azure-környezetben. Naplózás egy jó első választás a házirend-hatás határozza meg, milyen hatása van a házirend, mielőtt azt a megtagadása. Az egyik módja annak, hogy a hozzárendelésenkénti hatás módosítása könnyebb legyen, a hatás paraméterezése. Az alábbi [paramétereket](#parameters) a részletekért lásd.

## <a name="compose-the-definition"></a>A definíció megírása

Most már a tulajdon adatait és alias, amit tervezünk kezelni. Ezután maga a politikai szabály alkotja. Ha még nem ismeri a házirend nyelvét, a [házirend-definíciós struktúra](../concepts/definition-structure.md) hivatkozási at, hogyan strukturálhatja a házirend-definíció. Íme egy üres sablon arról, hogyan néz ki egy házirend-definíció:

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

Az első három összetevő a házirend metaadatai. Ezek az összetevők könnyen biztosítható értékek, mivel tudjuk, mit hozunk létre a szabály. [A mód](../concepts/definition-structure.md#mode) elsősorban a címkékről és az erőforrás helyéről szól. Mivel nem kell a kiértékelést a címkéket támogató erőforrásokra korlátoznunk, az _összes_ értéket használjuk a **módhoz.**

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Paraméterek

Bár nem használtunk paramétert a kiértékelés módosítására, szeretnénk egy paramétert használni, amely lehetővé teszi a hibaelhárítás **idohatásának** módosítását. Definiálunk egy **effectType** paramétert, és csak **a Deny** and Disabled paraméterre **korlátozzuk.** Ez a két lehetőség megfelel üzleti követelményeinknek. A kész paraméterek blokk néz ki, mint ez a példa:

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

### <a name="policy-rule"></a>Házirendszabály

A [házirendszabály](../concepts/definition-structure.md#policy-rule) összeállítása az utolsó lépés az egyéni szabályzatdefiníció létrehozásában. Két kijelentést azonosítottunk, hogy teszteljük:

- A tárfiók **típusa** **Microsoft.Storage/storageAccounts**
- A tárfiók **által támogatottHttpsTrafficOnly** nem **igaz**

Mivel mindkét állításnak igaznak kell lennie, az **allOf** [logikai operátort](../concepts/definition-structure.md#logical-operators)használjuk. A **effectType** paramétert statikus deklaráció helyett adja át a hatásnak. A befejezett szabály így néz ki:

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

A politika mindhárom részével, itt van a kitöltött meghatározás:

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

A kitöltött definíció új házirend létrehozásához használható. A Portál és az egyes SDK-k (Azure CLI, Azure PowerShell és REST API) különböző módokon fogadják el a definíciót, ezért tekintse át a parancsokat az egyes a helyes használat érvényesítéséhez. Ezután rendelje hozzá a paraméterezett hatás használatával a tárfiókok biztonságának kezeléséhez szükséges megfelelő erőforrásokhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az oktatóanyagból származó erőforrásokkal, az alábbi lépésekkel törölheti a fent létrehozott hozzárendelések vagy definíciók bármelyikét:

1. Válassza **a Definíciók** (vagy **hozzárendelések,** ha egy hozzárendelést próbál törölni) lehetőséget az Azure Policy lap bal oldalán található **Szerzői** műveletek területen.

1. Keresse meg az eltávolítani kívánt új kezdeményezést vagy szabályzatdefiníciót (vagy hozzárendelést).

1. Kattintson a jobb gombbal a sorra, vagy a bal gombbal a definíció (vagy a hozzárendelés) mellett található három pontra, majd a **Definíció törlése** (vagy a **Hozzárendelés törlése**) parancsra.

## <a name="review"></a>Áttekintés

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> - Az üzleti követelmények azonosítása
> - Minden követelmény tappált egy Azure-erőforrás-tulajdonsághoz
> - A tulajdonság leképezése aliashoz
> - Meghatározta a hatás használatát
> - A házirend-definíció összeáll

## <a name="next-steps"></a>További lépések

Ezután az egyéni házirend-definíció segítségével hozzon létre és rendeljen hozzá egy házirendet:

> [!div class="nextstepaction"]
> [Házirend-definíció létrehozása és hozzárendelése](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)