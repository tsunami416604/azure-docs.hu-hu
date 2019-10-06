---
title: Erőforrás-módosítások lekérése
description: Megtudhatja, hogyan keresheti meg az erőforrást, és hogyan kérheti le a megváltoztatott tulajdonságok listáját.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980282"
---
# <a name="get-resource-changes"></a>Erőforrás-módosítások lekérése

Az erőforrások a napi használat, az újrakonfigurálás és még az újratelepítés során változnak.
A módosítás egy egyedi vagy egy automatizált folyamat alapján is elvégezhető. A legtöbb változás a tervezés szerint történik, de néha nem. Az elmúlt 14 napban az Azure Resource Graph a következőket teszi lehetővé:

- Megtudhatja, mikor történtek módosítások az Azure Resource Manager-tulajdonságokban.
- Megnézheti, mely tulajdonságok változtak meg a módosítási esemény részeként.

Az észlelés és a részletek változása az alábbi példák esetében hasznos:

- Az incidensek kezelése során a _potenciálisan_ kapcsolódó változások megismeréséhez. Egy adott időintervallumban megjelenő változási események lekérdezése és a módosítás részleteinek kiértékelése.
- A CMDB néven ismert Configuration Management-adatbázis naprakészen tartása. Ahelyett, hogy az összes erőforrást és a teljes tulajdonságokat egy ütemezett gyakoriságon frissítse, csak a megváltoztatott értéket adja meg.
- Annak megértése, hogy milyen egyéb tulajdonságok változtak, amikor egy erőforrás megváltoztatta a megfelelőségi állapotot. Ezeknek a további tulajdonságoknak a kiértékelése további, Azure Policy-definíción keresztül felügyelhető tulajdonságokkal is rendelkezhet.

Ez a cikk bemutatja, hogyan gyűjtheti ezeket az információkat az erőforrás-gráf SDK-n keresztül. Ha szeretné megtekinteni ezeket az információkat a Azure Portalban, tekintse meg a Azure Policy [változási előzményeit](../../policy/how-to/determine-non-compliance.md#change-history-preview) vagy az Azure-tevékenység naplójának [módosításait](../../../azure-monitor/platform/activity-log-view.md#azure-portal).

> [!NOTE]
> Az erőforrás-diagram adatainak módosítása a Resource Manager-tulajdonságok. A virtuális gépek változásainak nyomon követéséhez tekintse meg a Azure Automation [változások követése](../../../automation/automation-change-tracking.md) vagy a Azure Policy a virtuális gépekhez [tartozó vendég konfigurációját](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Az Azure Resource Graph változási előzményei nyilvános előzetes verzióban érhetők el.

## <a name="find-when-changes-were-detected"></a>A változások észlelésének megkeresése

Az erőforrás változásának első lépése, hogy megkeresse az adott erőforráshoz kapcsolódó módosítási eseményeket az adott időintervallumon belül. Ez a lépés a **resourceChanges** Rest-végponton keresztül történik.

A **resourceChanges** végpontjának két paramétert kell megadnia a kérelem törzsében:

- **resourceId**: Az Azure-erőforrás, melyen változásokat keres.
- **időköz**: Egy olyan tulajdonság, amely _kezdő_ és _befejező_ dátummal rendelkezik, hogy mikor érdemes megkeresni egy változási eseményt a **Zulu Time Zone (Z)** használatával.

A példában a kérés törzse:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

A fenti kérelem törzsével a **resourceChanges** REST API URI-ja a következő:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A válasz a következőhöz hasonlóan néz ki:

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

A **resourceId** minden észlelt változási eseményéhez tartozik egy **changeId** , amely egyedi az adott erőforráshoz. Habár a **changeId** karakterlánc esetenként más tulajdonságokat is tartalmazhat, csak egyedinek kell lennie. A módosítási rekord tartalmazza azokat az időpontokat, amikor az előtte és utána Pillanatképek készültek.
A változási esemény ezen az időablakon belül egy adott időpontban fordult elő.

## <a name="see-what-properties-changed"></a>A tulajdonságok változásának megtalálása

A **resourceChanges** végpont **changeId** a **resourceChangeDetails** Rest-végpontot használja a változási esemény részletes adatainak beolvasásához.

A **resourceChangeDetails** végpontjának két paramétert kell megadnia a kérelem törzsében:

- **resourceId**: Az Azure-erőforrás, melyen változásokat keres.
- **changeId**: A **resourceChanges**-ből összegyűjtött **resourceId** egyedi változási eseménye.

A példában a kérés törzse:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

A fenti kérelem törzsével a **resourceChangeDetails** REST API URI-ja a következő:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

A válasz a következőhöz hasonlóan néz ki:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
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
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

a **beforeSnapshot** és a **afterSnapshot** mindegyike megadja a pillanatkép készítésének időpontját és a tulajdonságokat. A módosítás néhány ponton történt a pillanatképek között. A fenti példában látható, hogy a módosított tulajdonság **supportsHttpsTrafficOnly**volt.

Ha programozott módon szeretné összehasonlítani az eredményeket, hasonlítsa össze az egyes Pillanatképek **tartalmi** részét a különbség megállapításához. Ha összehasonlítja a teljes pillanatképet, az **időbélyeg** mindig különbséget mutat a vártnál.

## <a name="next-steps"></a>További lépések

- Tekintse meg az [alapszintű lekérdezésekben](../samples/starter.md)használt nyelvet.
- Lásd: speciális alkalmazások a [speciális lekérdezésekben](../samples/advanced.md).
- Ismerje meg az [erőforrások feltárását](../concepts/explore-resources.md).