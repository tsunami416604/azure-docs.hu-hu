---
title: Erőforrás-módosítások lekérése
description: Megismerheti, hogyan találhatja meg, amikor egy erőforrás változott, és a módosított tulajdonságok listája.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/20/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 0ae85b45dfcd80056316ed5f2099aab4057d24c8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720342"
---
# <a name="get-resource-changes"></a>Erőforrás-módosítások lekérése

Erőforrások használ napi újrakonfigurálása és még akkor is, újbóli üzembe helyezés során módosul.
Változás bármikor visszatérhet, egyéni vagy egy automatikus folyamat alapján. A legtöbb módosítása a rendszer kialakításából fakad, de néha nem. Az elmúlt 14 napban a változások nyomon követése, az Azure-erőforrás Graph teszi lehetővé:

- Megtudhatja, mikor történtek módosítások az Azure Resource Manager-tulajdonságokban.
- Megnézheti, mely tulajdonságok változtak meg a módosítási esemény részeként.

Címváltozásának felderítését és a részletek hasznosak az alábbi példák a következők:

- Incidenskezelés ismertetése során _potenciálisan_ kapcsolódó változásairól. Állapotváltozási események idő adott időszak alatt lekérdezheti, és kiértékelheti a változás részleteit.
- Konfigurációkezelési adatbázis tartja, más néven a cmdb-JÉBEN, naprakész. Frissítés helyett az összes erőforrás és a egy ütemezett gyakoriságát, a teljes tulajdonság beállítása csak a get mi változott.
- Milyen egyéb tulajdonságok Módosíthatott erőforrás megfelelőségi állapotok megváltozásakor ismertetése. Ezek a további tulajdonságok kiértékelése is szükség lehet egy Azure szabályzat-definíció kezelendő egyéb tulajdonságok betekintést nyújtson.

Ez a cikk bemutatja, hogyan erőforrás Graph SDK-n keresztül az információk gyűjtésére. Ez az információ az Azure Portalon, olvassa el az Azure Policy [módosítási előzmények](../../policy/how-to/determine-non-compliance.md#change-history-preview).

> [!NOTE]
> Változások részleteinek Erőforrás-grafikon a Resource Manager-tulajdonságok vannak. A virtuális gépen belüli változásainak követése, tekintse meg az Azure Automation [Change tracking](../../../automation/automation-change-tracking.md) vagy az Azure Policy [Vendég virtuális gépek konfigurációjának](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Változások nyomon követése az Azure-erőforrás Graph jelenleg nyilvános előzetes verzióban.

## <a name="find-when-changes-were-detected"></a>Keresse meg, ha módosításokat észlelt

Az első lépés jelenik meg, mi változott, egy erőforráson, hogy az idő időtartamon belül erőforráshoz kapcsolódó események megtalálja. Ebben a lépésben keresztül történik a **resourceChanges** REST-végpont.

A **resourceChanges** végpont a kérelem törzsében szereplő két paraméter szükséges:

- **resourceId**: Az Azure-erőforráshoz módosításait keressen.
- **Intervallum**: Tulajdonságot _start_ és _záró_ mikor ellenőrizze a módosítási esemény a dátumokat a **Zulu időzóna (Z)**.

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

A fenti kérés törzse, a REST API URI-azonosítóját az **resourceChanges** van:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A válasz a példához hasonlóan néz ki:

```json
{
    "changes": [{
            "changeId": "2db0ad2d-f6f0-4f46-b529-5c4e8c494648",
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

Esemény a rendszer minden egyes észlelt a **resourceId** rendelkezik egy **changeId** Ez az egyedi ennek az erőforrásnak. Bár a **changeId** karakterlánc időnként előfordulhat, hogy tartalmazzák a többi tulajdonság, csak garantálta egyedinek kell lennie. A módosító rekord tartalmazza az időpontokat, amely a előtt és után pillanatképek származnak.
Az esemény történt olyan pont, ebben az ablakban idő.

## <a name="see-what-properties-changed"></a>Tulajdonságok változott

Az a **changeId** származó a **resourceChanges** végpont, a **resourceChangeDetails** REST-végpont majd kéri le az esemény tulajdonságairól.

A **resourceChangeDetails** végpont a kérelem törzsében szereplő két paraméter szükséges:

- **resourceId**: Az Azure-erőforráshoz módosításait keressen.
- **changeId**: Az egyedi esemény esetében a **resourceId** összeállításunkat **resourceChanges**.

A példában a kérés törzse:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556"
}
```

A fenti kérés törzse, a REST API URI-azonosítóját az **resourceChangeDetails** van:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

A válasz a példához hasonlóan néz ki:

```json
{
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556",
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

**beforeSnapshot** és **afterSnapshot** egyes meg kell adni az idő a pillanatkép és a Tulajdonságok időpontjában. A módosítás történt a pillanatképek közé. A fenti példa megnézzük, láthatjuk, hogy a megváltozott tulajdonság volt **supportsHttpsTrafficOnly**.

Az eredmények összehasonlítása a programozott módon, hasonlítsa össze a **tartalom** minden pillanatkép különbség meghatározására része. Ha a teljes pillanatkép összehasonlítja a **időbélyeg** mindig jeleníti meg, annak ellenére, hogy folyamatban van a várt számít.

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelv [alapszintű lekérdezéseket](../samples/starter.md).
- Tekintse meg a speciális használ [összetettebb lekérdezésekhez](../samples/advanced.md).
- Ismerje meg, hogyan [források](../concepts/explore-resources.md).
