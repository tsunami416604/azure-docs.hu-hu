---
title: Felkészülés az Azure Monitor erőforrásnaplóinak formátumváltására
description: 2018. november 1-jén ismerteti az eszközhasználat hatását és az eszközhasználat frissítését az új Azure-erőforrásnaplók kezeléséhez, amelyeket a hozzáfűző blobok használatára módosítottak.
author: johnkemnetz
services: monitoring
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d30652d4e068cbceb79e6da60b48176b9de64647
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670440"
---
# <a name="prepare-for-format-change-to-azure-monitor-resource-logs-archived-to-a-storage-account"></a>Felkészülés a formátumváltásra az Azure Monitor tárfiókba archivált erőforrásnaplóiban

> [!WARNING]
> Ha az [Azure-erőforrás-naplók vagy metrikák egy tárfiókba erőforrás diagnosztikai beállítások](./../../azure-monitor/platform/archive-diagnostic-logs.md) vagy tevékenységnaplók használatával egy [tárfiók ba naplóprofilok,](./../../azure-monitor/platform/archive-activity-log.md)az adatok formátuma a tárfiókban fog változni JSON-sorok november 1, 2018. Az alábbi utasítások ismertetik a hatását, és hogyan kell frissíteni a szerszámot az új formátum kezeléséhez. 
>
> 

## <a name="what-is-changing"></a>Mi változik?

Az Azure Monitor olyan lehetőséget kínál, amely lehetővé teszi az erőforrás-diagnosztikai adatok és a tevékenységnapló-adatok küldését egy Azure-tárfiókba, az Event Hubs névtérbe vagy az Azure Monitor Log Analytics-munkaterületére. A rendszer teljesítményével kapcsolatos probléma megoldása érdekében **2018.** Ha olyan eszközmegkészítő eszközzel rendelkezik, amely adatokat olvas fel a blob storage-ból, frissítenie kell az eszközmegkészítőt az új adatformátum megértéséhez.

* 2018. november 1-jén, csütörtökön 12:00-kor az UTC éjfélkor a blob formátuma [JSON Lines](http://jsonlines.org/)lesz. Ez azt jelenti, hogy minden rekordot egy új sor fog elválasztani, külső rekordtömb és json-rekordok között vesszők nélkül.
* A blob formátuma egyszerre változik az összes diagnosztikai beállításhoz az összes előfizetésben. Az első PT1H.json fájl kibocsátott november 1 fogja használni ezt az új formátumot. A blob és a tároló nevek változatlanok maradnak.
* A diagnosztikai beállítás beállítása mostantól november 1-ig továbbra is az aktuális formátumban történik.
* Ez a változás egyszerre fog bekövetkezni az összes nyilvános felhőbeli régióban. A változás még nem fog bekövetkezni a 21Vianet, az Azure Germany vagy az Azure Government felhők által üzemeltetett Microsoft Azure-ban.
* Ez a módosítás a következő adattípusokra van hatással:
  * [Az Azure erőforrás-naplói](archive-diagnostic-logs.md) [(lásd az erőforrások listáját itt)](diagnostic-logs-schema.md)
  * [A diagnosztikai beállítások kal exportált Azure-erőforrás-metrikák](diagnostic-settings.md)
  * [A naplóprofilok által exportált Azure-tevékenységnapló-adatok](archive-activity-log.md)
* Ez a változás nincs hatással:
  * Hálózati folyamatnaplók
  * Az Azure-szolgáltatásnaplók még nem érhetők el az Azure Monitoron keresztül (például az Azure App Service erőforrás-naplói, a tárolási elemzési naplók)
  * Az Azure-erőforrásnaplók és tevékenységnaplók útválasztása más célhelyekre (Eseményközpontok, Log Analytics)

### <a name="how-to-see-if-you-are-impacted"></a>Hogyan lehet látni, ha tással van

Önre csak akkor lesz hatással ez a változás, ha:
1. Naplóadatokat küld egy Azure-tárfiókba erőforrás-diagnosztikai beállítással, és
2. A tárolóban lévő naplók JSON-struktúrájátantól függő eszközelművel.
 
Annak megállapításához, hogy vannak-e olyan erőforrás-diagnosztikai beállítások, amelyek adatokat küldenek egy Azure-tárfiókba, keresse meg a portál **Figyelő** szakaszát, kattintson a **Diagnosztikai beállítások**elemre, és azonosítsa azokat az erőforrásokat, amelyek **diagnosztikai állapota** **engedélyezve**van:

![Az Azure Monitor diagnosztikai beállításai panel](./media/diagnostic-logs-append-blobs/portal-diag-settings.png)

Ha a Diagnosztikai állapot beállítás engedélyezve van, akkor aktív diagnosztikai beállítással rendelkezik az adott erőforráson. Kattintson az erőforrásra, és nézze meg, hogy a diagnosztikai beállítások küldenek-e adatokat egy tárfiókba:

![Tárfiók engedélyezve](./media/diagnostic-logs-append-blobs/portal-storage-enabled.png)

Ha rendelkezik erőforrásokkal, amelyek adatokat küldenek egy tárfiókba ezekkel az erőforrás-diagnosztikai beállításokkal, a rendszer hatással lesz az adott tárfiókban lévő adatok formátumára. Ha nem rendelkezik olyan egyéni eszközökkel, amelyek ezeken a tárfiókokon kívül működnek, a formátummódosítása nem lesz hatással Önre.

### <a name="details-of-the-format-change"></a>A formátumváltozás részletei

A PT1H.json fájl jelenlegi formátuma az Azure blob storage-ban egy JSON-rekordtömböt használ. Itt van egy példa a KeyVault naplófájl most:

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

Az új formátum [JSON-sorokat](http://jsonlines.org/)használ, ahol minden esemény egy sor, az új sor karakter pedig egy új eseményt jelöl. Itt van, amit a fenti minta fog kinézni a PT1H.json fájl a változás után:

```json
{"time": "2016-01-05T01:32:01.2691226Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "78","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
{"time": "2016-01-05T01:33:56.5264523Z","resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT","operationName": "VaultGet","operationVersion": "2015-06-01","category": "AuditEvent","resultType": "Success","resultSignature": "OK","resultDescription": "","durationMs": "83","callerIpAddress": "104.40.82.76","correlationId": "","identity": {"claim": {"http://schemas.microsoft.com/identity/claims/objectidentifier": "d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "live.com#username@outlook.com","appid": "1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},"properties": {"clientInfo": "azure-resource-manager/2.0","requestUri": "https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id": "https://contosokeyvault.vault.azure.net/","httpStatusCode": 200}}
```

Ez az új formátum lehetővé teszi, hogy az Azure Monitor leküldéses naplófájlok [hozzáfűző blobok,](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)amelyek hatékonyabbak az új eseményadatok folyamatos hozzáfűzése.

## <a name="how-to-update"></a>Hogyan lehet frissíteni

Csak akkor kell frissítéseket készítenie, ha egyéni eszközkezeléssel rendelkezik, amely befelé betöltése a naplófájlokat további feldolgozásra. Ha külső naplóelemzési vagy SIEM-eszközt használ, azt javasoljuk, [hogy inkább az eseményközpontok at használja az adatok betöltéséhez.](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) Az eseményközpontok integrációja egyszerűbb a számos szolgáltatás naplóinak feldolgozása és egy adott napló könyvjelzői helyének feldolgozása szempontjából.

Az egyéni eszközöket frissíteni kell az aktuális formátum és a fent leírt JSON Lines formátum kezeléséhez. Ez biztosítja, hogy amikor az adatok az új formátumban kezdenek megjelenni, az eszközök ne szakadjanak meg.

## <a name="next-steps"></a>További lépések

* Információ az [erőforrás-erőforrások naplóinak tárfiókba történő archiválásáról](./../../azure-monitor/platform/archive-diagnostic-logs.md)
* További információ a [tevékenységnapló-adatok tárfiókba történő archiválásáról](./../../azure-monitor/platform/archive-activity-log.md)

