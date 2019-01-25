---
title: Az Azure Monitor-diagnosztikai naplók formátum módosítás előkészítése
description: Az Azure diagnosztikai naplók használata kerül hozzáfűző blobok 2018. November 1.
author: johnkemnetz
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: f626ef4cfb385a62f68ab611b77dd0c307cd3820
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882379"
---
# <a name="prepare-for-format-change-to-azure-monitor-diagnostic-logs-archived-to-a-storage-account"></a>Az Azure Monitor diagnosztikai naplók tárfiókba archivált formátum módosítás előkészítése

> [!WARNING]
> Ha küld [Azure erőforrás-diagnosztikai naplók vagy a storage-fiók erőforrás diagnosztikai beállításait mérőszámok](./../../azure-monitor/platform/archive-diagnostic-logs.md) vagy [Tevékenységnaplók az a tárfiók tárfiókkulcsait naplóprofilok](./../../azure-monitor/platform/archive-activity-log.md), az adatok formátumát a tárfiók 2018. november 1. a JSON-sorok változik. Az alábbi utasítások írja le, a hatás és az eszközök kezeléséhez az új formátum frissítése. 
>
> 

## <a name="what-is-changing"></a>Mi változik

Az Azure Monitor egy olyan funkció, amely lehetővé teszi, hogy küldjön az erőforrás diagnosztikai adatok és a tevékenységnapló adatainak Azure storage-fiók, az Event Hubs-névteret, vagy a Log Analytics kínál. A rendszer teljesítményprobléma megoldása a **2018. November 1. 12:00 éjfélkor (UTC)** adatokat küldeni a blob storage-naplófájl formátumát változik. Ha eszközkészlet, amely blob tárolóból az adatok olvasása, tudni, hogy az új adatformátum a azokat az eszközöket frissíteni szeretné.

* Csütörtök. November 1, 2018. 12:00 éjfélkor (UTC), a blob formátumát kell változik [JSON sorok](http://jsonlines.org/). Ez azt jelenti, hogy minden rekord egy új sor nem külső rekordok tömb és JSON-bejegyzések között nincs vesszővel kell elválasztani.
* A blob formátuma változásokat a egyszerre az összes előfizetés összes diagnosztikai beállításait. Az első PT1H.json fájlt. November 1 bocsátja ki fogja használni az új formátum. A blob és a tároló neve nem változik.
* Diagnosztikai beállítás módosítása és November 1 továbbra is az aktuális formátumú adatokat küldik. November 1-ig.
* Ez a változás az összes nyilvános régióban egyszerre történik. A módosítás nem történik az Azure China, az Azure Germany és az Azure Government még.
* Ez a módosítás hatással van a következő adattípusokat:
  * [Az Azure erőforrás-diagnosztikai naplók](./../../azure-monitor/platform/archive-diagnostic-logs.md) ([itt erőforrások listájának megtekintéséhez](./../../azure-monitor/platform/diagnostic-logs-schema.md))
  * [Az Azure erőforrás-metrikák diagnosztikai beállítások exportálása folyamatban](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)
  * [Naplóprofilok által exportált Azure tevékenységnapló adatainak](./../../azure-monitor/platform/archive-activity-log.md)
* Ez a változás nem érinti:
  * Hálózati forgalmi naplók
  * Az Azure naplói nem még a Azure Monitor érhetők el (például a diagnosztikai naplók az Azure App Service-ben, a storage analytics naplók)
  * Az Azure diagnosztikai naplók és Tevékenységnaplók más célhelyre irányított (az Event Hubs, a Log Analytics) útválasztás

### <a name="how-to-see-if-you-are-impacted"></a>Ha, Ön érintett megtekintése

Ön csak érinti a változás Ha Ön:
1. Naplóadatok erőforrás diagnosztikai beállításának használata az Azure storage-fiókba küldi, és
2. Vannak olyan eszközök, amelyek ezeket a naplókat tároló a JSON szerkezete függ.
 
Ha erőforrás diagnosztikai beállításait, amelyek adatokat küldenek az Azure storage-fiók azonosításához navigálhat a **figyelő** szakaszban a portál, kattintson a **diagnosztikai beállítások**, és azonosítása minden olyan erőforrásokat, **diagnosztikai állapot** beállítása **engedélyezve**:

![Az Azure Monitor diagnosztikai beállítások panel](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Diagnosztikai beállítás van engedélyezve, ha azok aktív diagnosztikai beállítást az erőforráson. Kattintson az erőforrás megtekintéséhez, ha a diagnosztikai beállítások adatot küldenek egy storage-fiókhoz:

![Storage-fiókban engedélyezve](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Ha rendelkezik ezekkel a beállításokkal erőforrás diagnosztikai tárfiók történő adatküldés erőforrásokat, a storage-fiókban lévő adatok formátumát a változás által érintett. A formátumának módosítása nem érinti, kivéve, ha egyéni azokat az eszközöket, amelyek ki ezeket a storage-fiókok nem működik.

### <a name="details-of-the-format-change"></a>A formátum változás részletei

A jelenlegi formátumát a PT1H.json fájlt az Azure blob storage-ban a rekordok egy JSON-tömböt használ. Itt látható egy példa a KeyVault naplófájlok most:

```json
{
    "records": [
        {
            "time": "2016-01-05T01:32:01.2691226Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "78",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        },
        {
            "time": "2016-01-05T01:33:56.5264523Z",
            "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            "operationName": "VaultGet",
            "operationVersion": "2015-06-01",
            "category": "AuditEvent",
            "resultType": "Success",
            "resultSignature": "OK",
            "resultDescription": "",
            "durationMs": "83",
            "callerIpAddress": "104.40.82.76",
            "correlationId": "",
            "identity": {
                "claim": {
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com",
                    "appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"
                }
            },
            "properties": {
                "clientInfo": "azure-resource-manager/2.0",
                "requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01",
                "id": "https://contosokeyvault.vault.azure.net/",
                "httpStatusCode": 200
            }
        }
    ]
}
```

Az új formátumot használ [JSON sorok](http://jsonlines.org/), ahol minden egyes esemény egy olyan sor, és a soremelés karaktert azt jelzi, hogy egy új esemény. Itt látható a fenti példa fog kinézni a PT1H.json fájlhoz fűzve a váltás után:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Az új formátum lehetővé teszi, hogy az Azure Monitor használatával leküldéses naplófájlok [hozzáfűző blobok](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs), ami még hatékonyabbak folyamatosan lehetőséggel új eseményadatokat.

## <a name="how-to-update"></a>Frissítése

Csak kell, hogy a frissítések, ha egyéni azokat az eszközöket, amelyek fogadnak ezek a naplófájlok további feldolgozás céljából. Ha egy külső a log analytics vagy az SIEM-eszközével használja, javasoljuk, hogy [event hubs használatával, hogy ezeket az adatokat inkább](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). Event hubs-integráción, könnyebben feldolgozása a naplókat számos szolgáltatásból és a könyvjelzőkezelési funkció egy adott napló helyét.

Egyéni eszközök kezeléséhez az aktuális és a JSON-sorok formátumú a fent leírt is frissíteni kell. Ez biztosítja, hogy amikor adatok jelennek meg az új formátum kezd, az eszközök nem hibásodik.

## <a name="next-steps"></a>További lépések

* Ismerje meg [archiválás tárfiókba erőforrás-diagnosztikai naplók](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* Ismerje meg [archiválás tárfiókba tevékenységnapló adatainak](./../../azure-monitor/platform/archive-activity-log.md)

