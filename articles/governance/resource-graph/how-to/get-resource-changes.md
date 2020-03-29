---
title: Erőforrás-módosítások lekérése
description: Ismerje meg, hogyan keresheti meg az erőforrás módosításának módját, hogyan kaphatja meg a módosított tulajdonságok listáját, és hogyan értékelheti ki a különbözetet.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873029"
---
# <a name="get-resource-changes"></a>Erőforrás-módosítások lekérése

Az erőforrások a napi használat, az újrakonfigurálás, sőt az újratelepítés során módosulnak.
A változás származhat egyéntől vagy automatizált folyamattól. A legtöbb változás a tervezés, de néha nem. A módosítási előzmények elmúlt 14 napjával az Azure Resource Graph lehetővé teszi a következőket:

- Az Azure Resource Manager-tulajdonság változásainak észlelésekor
- Az egyes erőforrás-változásokról lásd: Tulajdonságmódosítási részletek
- Az észlelt változás előtti és utáni erőforrás teljes összehasonlításának megtekintése

A változások észlelése és részletei a következő példaesetekben értékesek:

- Az incidenskezelés során a _potenciálisan_ kapcsolódó változások megértéséhez. Változásesemények lekérdezése egy adott időablakban, és a módosítás részleteinek kiértékelése.
- A CMDB néven ismert konfigurációkezelő adatbázis naprakészen tartása. Ahelyett, hogy az összes erőforrást és a teljes tulajdonságkészleteket ütemezett gyakorisággal frissítenék, csak azt kapja meg, ami megváltozott.
- Annak ismertetése, hogy milyen egyéb tulajdonságok módosulhattak, amikor egy erőforrás módosította a megfelelőségi állapotot. Ezeka további tulajdonságok kiértékelése betekintést nyújthat más tulajdonságok, amelyek egy Azure Policy-definíción keresztül kell kezelni.

Ez a cikk bemutatja, hogyan gyűjtheti össze ezeket az információkat az Erőforrás-grafikon SDK-n keresztül. Ha meg szeretné tekinteni ezeket az információkat az Azure Portalon, tekintse meg az Azure-szabályzat [változási előzményeit](../../policy/how-to/determine-non-compliance.md#change-history-preview) vagy az Azure-tevékenységnapló [módosítási előzményeit.](../../../azure-monitor/platform/activity-log-view.md#azure-portal)
Az alkalmazások nak az infrastruktúrarétegből az alkalmazás üzembe helyezéséig történő változásairól az Azure Monitor [alkalmazásváltozási elemzés (előzetes verzió) használata című](../../../azure-monitor/app/change-analysis.md) témakörben talál további részleteket.

> [!NOTE]
> Az Erőforrás-diagram ban az Erőforrás-kezelő tulajdonságainak módosítása. A virtuális gépeken belüli változások nyomon követéséről az Azure Automation [változáskövetés](../../../automation/automation-change-tracking.md) vagy az Azure-szabályzat [virtuális gépekhez való vendégkonfigurációja](../../policy/concepts/guest-configuration.md)című témakörben található.

> [!IMPORTANT]
> Az Azure Resource Graph módosítási előzményei nyilvános előzetes verzióban vannak.

## <a name="find-detected-change-events-and-view-change-details"></a>Észlelt változási események keresése és a módosítás részleteinek megtekintése

Az első lépés annak megtekintéséhez, hogy mi változott egy erőforrás, hogy megtalálja a változás események kapcsolódó erőforrás egy ablakon belül. Minden módosítási esemény részletesen ismerteti, hogy mi változott az erőforráson. Ez a lépés a **resourceChanges** REST-végponton keresztül történik.

A **resourceChanges** végpont a következő paramétereket fogadja el a kérelemtörzsben:

- **resourceId** \[\]szükséges: Az Azure-erőforrás a változások at keresni.
- **időköz** \[\]szükséges : Olyan tulajdonság, amelynek _kezdő_ és _záró_ dátuma van arra vonatkozóan, hogy mikor kell változási eseményt keresni a **Zulu időzónával (Z).**
- **fetchPropertyChanges** (nem kötelező): Logikai tulajdonság, amely akkor állítja be, ha a válaszobjektum tulajdonságváltozásokat tartalmaz.

Példa kérelem törzse:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

A fenti kérelemtörzs esetén a REST API URI az **erőforrásváltozásokhoz** a következő:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

A válasz a következő példához hasonlóan néz ki:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Az **erőforrásazonosító** minden észlelt változási eseménye a következő tulajdonságokkal rendelkezik:

- **changeId** – Ez az érték az adott erőforrásra egyedi. Bár a **changeId** karakterlánc néha tartalmazhat más tulajdonságokat, ez csak garantáltan egyedi.
- **beforeSnapshot** - Tartalmazza a **pillanatképazonosítót** és az erőforrás-pillanatkép **időbélyegét,** amely a módosítás észlelése előtt készült.
- **afterSnapshot** – A módosítás észlelése után készített erőforrás-pillanatkép **pillanatképazonosítóját** és **időbélyegét** tartalmazza.
- **changeType** - A teljes változásrekordhoz a **beforeSnapshot** és az **afterSnapshot**között észlelt módosítás típusát írja le. Az értékek a következők: _Létrehozás,_ _Frissítés_és _Törlés_. A **propertyChanges** tulajdonságtömb csak akkor jelenik meg, ha a **changeType** _frissítés_.
- **propertyChanges** - Ez a tulajdonságtömb részletezi az összes olyan erőforrástulajdonságot, amely a snapshot előtt és az **afterSnapshot** között frissült: **afterSnapshot**
  - **propertyName** - A módosított erőforrástulajdonság neve.
  - **changeCategory** - Leírja, hogy mi történt a változás. Értékek: _Rendszer_ és _felhasználó_.
  - **changeType** - Az egyes erőforrás-tulajdonsághoz észlelt módosítás típusát írja le.
    Az értékek a következők: _Beszúrás,_ _Frissítés_, _Eltávolítás_.
  - **beforeValue** - Az erőforrástulajdonság értéke a **beforeSnapshot .** Nem jelenik meg, ha a **changeType** _a Beszúrás_.
  - **afterValue** - az erőforrástulajdonság értéke az **afterSnapshot .** Nem jelenik meg, ha a **changeType** _az Eltávolítás_.

## <a name="compare-resource-changes"></a>Erőforrás-változások összehasonlítása

A **changeId** a **resourceChanges** végpont, a **resourceChangeDetails** REST végpont ezután a módosított erőforrás előtti és utáni pillanatképek lefelvételéhez.

A **resourceChangeDetails** végpontnak két paraméterre van szüksége a kérelemtörzsben:

- **resourceId:** Az Azure-erőforrás a változások összehasonlításához.
- **changeId**: Az **erőforrás-azonosító erőforrás-azonosítójának** egyedi **változáseseménye .**

Példa kérelem törzse:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

A fenti kérelemtörzs esetén a REST API URI a **resourceChangeDetails** a következő:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

A válasz a következő példához hasonlóan néz ki:

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

**beforeSnapshot** és **afterSnapshot** mindegyik megadja a pillanatkép készítésének idejét és az adott időpontban lévő tulajdonságokat. A változás történt egy bizonyos ponton között ezeket a pillanatképeket. A fenti példát nézve láthatjuk, hogy a megváltozott tulajdonság **támogatjaaHttpsTrafficOnly**.

Az eredmények összehasonlításához használja a **changes tulajdonságot** az **erőforrásváltozásokban,** vagy értékelje ki az egyes pillanatképek **tartalomrészét** a **resourceChangeDetails-ban** a különbség meghatározásához. Ha összehasonlítja a pillanatképek, az **időbélyeg** mindig a várt ellenére is különbségként jelenik meg.

## <a name="next-steps"></a>További lépések

- Tekintse meg a használt nyelvet az [Indítólekérdezésekben.](../samples/starter.md)
- Lásd: Speciális használat a [Speciális lekérdezésekben.](../samples/advanced.md)
- További információ az [erőforrások felfedezéséről.](../concepts/explore-resources.md)