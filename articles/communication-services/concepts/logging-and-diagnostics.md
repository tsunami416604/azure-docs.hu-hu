---
title: Kommunikációs szolgáltatások naplói
titleSuffix: An Azure Communication Services concept document
description: Az Azure kommunikációs szolgáltatások naplózásának megismerése
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: aad4cdfe38ee9dd7530cb8ebe21cded18cb0a1ec
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128662"
---
# <a name="communication-services-logs"></a>Kommunikációs szolgáltatások naplói

Az Azure kommunikációs szolgáltatás olyan naplózási funkciókat kínál, amelyek segítségével figyelheti és hibakeresést végezhet a kommunikációs szolgáltatások megoldásán. Ezeket a képességeket a Azure Portal konfigurálhatja.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Diagnosztikai naplók engedélyezése az erőforrásban

A naplózás alapértelmezés szerint ki van kapcsolva egy erőforrás létrehozásakor. A naplózás engedélyezéséhez navigáljon a **diagnosztikai beállítások** panelre a **figyelés** szakasz erőforrás menüjében. Ezután kattintson a **diagnosztikai beállítások hozzáadása**lehetőségre.

Ezután válassza ki a kívánt archiválási célt. Jelenleg a Storage-fiókokat és a Log Analytics is támogatja archiválási célként. Miután kiválasztotta a rögzíteni kívánt naplók típusát, mentse a diagnosztikai beállításokat.
 
Az új beállítások körülbelül tíz percet vesznek érvénybe. A naplók a kommunikációs szolgáltatások erőforrásának naplók paneljén a konfigurált archiválási célpontban megjelennek.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="Az ACS diagnosztikai beállításainak beállításai.":::

A diagnosztika konfigurálásával kapcsolatos további információkért tekintse meg az [Azure-erőforrások naplóinak](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)áttekintése című témakört.

## <a name="resource-log-categories"></a>Erőforrás-naplók kategóriái

A kommunikációs szolgáltatások háromféle típusú naplót biztosít:

* **Használati naplók** – az egyes számlázott szolgáltatásokhoz kapcsolódó használati adatokat biztosít
* **Csevegés operatív naplói** – a csevegési szolgáltatással kapcsolatos alapvető adatokat biztosít
* **SMS működési naplók** – az SMS szolgáltatással kapcsolatos alapvető információk

### <a name="usage-logs-schema"></a>Használati naplók sémája

| Tulajdonság | Leírás |
| -------- | ---------------|
| Timestamp | A napló létrehozásának időbélyegzője (UTC). |
| Művelet neve | A naplózási rekordhoz társított művelet. |
| Művelet verziója | A `api-version` művelethez társított, ha a OPERATIONNAME API használatával lett elvégezve. Ha nincs olyan API, amely megfelel a műveletnek, akkor a verzió a művelet azon verzióját jelöli, amely a művelethez kapcsolódó tulajdonságok jövőbeli változásakor következik be. |
| Kategória | Az esemény naplózási kategóriája. A kategória a részletesség, amelyen engedélyezheti vagy letilthatja a naplókat egy adott erőforráson. Az események tulajdonságok blobjában megjelenő tulajdonságok egy adott napló kategóriáján és erőforrás-típusán belül megegyeznek. |
| Korrelációs azonosító | A korrelált események azonosítója. A több tábla közötti korrelációs események azonosítására használható. |
| Tulajdonságok | A kommunikációs szolgáltatások különféle módjaira vonatkozó egyéb adat. |
| Rekord azonosítója | Egy adott használati rekord egyedi azonosítója. |
| Használati típus | A használati mód. (például csevegés, PSTN, NAT stb.) |
| Egység típusa | Az adott használati mód alapján a használaton kívüli egység típusa. (például perc, megabájt, üzenet stb.). |
| Mennyiség | A rekordhoz felhasznált vagy felhasznált egységek száma. |

### <a name="chat-operational-logs"></a>Csevegés operatív naplói

| Tulajdonság | Leírás |
| -------- | ---------------|
| TimeGenerated | A napló létrehozásának időbélyegzője (UTC). |
| OperationName | A naplózási rekordhoz társított művelet. |
| CorrelationID | A korrelált események azonosítója. A több tábla közötti korrelációs események azonosítására használható. |
| OperationVersion | A művelethez társított API-verzió, ha a operationName API használatával lett elvégezve. Ha nincs olyan API, amely megfelel a műveletnek, akkor a verzió a művelet azon verzióját jelöli, amely a művelethez kapcsolódó tulajdonságok jövőbeli változásakor következik be. |
| Kategória | Az esemény naplózási kategóriája. A kategória a részletesség, amelyen engedélyezheti vagy letilthatja a naplókat egy adott erőforráson. Az események tulajdonságok blobjában megjelenő tulajdonságok egy adott napló kategóriáján és erőforrás-típusán belül megegyeznek. |
| ResultType | A művelet állapota. |
| ResultSignature | A művelet alállapota. Ha ez a művelet egy REST API hívásnak felel meg, ez a mező a megfelelő REST-hívás HTTP-állapotkód. |
| ResultDescription | A művelet statikus szöveges leírása. |
| DurationMs | A művelet időtartama ezredmásodpercben. |
| CallerIpAddress | A hívó IP-címe, ha a művelet olyan API-hívásnak felel meg, amely nyilvánosan elérhető IP-címmel rendelkező entitásból származik. |
| Szint | Az esemény súlyossági szintje. |
| URI | A kérelem URI-ja. |
| UserId (Felhasználóazonosító) | A kérelem feladójának felhasználói azonosítója. |
| ChatThreadId | A kérelemhez társított csevegési szál azonosítója. |
| ChatMessageId | A kérelemhez társított csevegési üzenet azonosítója. |
| SdkType | A kérelemben használt SDK-típus. |
| PlatformType | A kérelemben használt platform típusa. |

### <a name="sms-operational-logs"></a>SMS működési naplók

| Tulajdonság | Leírás |
| -------- | ---------------|
| TimeGenerated | A napló létrehozásának időbélyegzője (UTC). |
| OperationName | A naplózási rekordhoz társított művelet. |
| CorrelationID | A korrelált események azonosítója. A több tábla közötti korrelációs események azonosítására használható. |
| OperationVersion | A művelethez társított API-verzió, ha a operationName API használatával lett elvégezve. Ha nincs olyan API, amely megfelel a műveletnek, akkor a verzió a művelet azon verzióját jelöli, amely a művelethez kapcsolódó tulajdonságok jövőbeli változásakor következik be. |
| Kategória | Az esemény naplózási kategóriája. A kategória a részletesség, amelyen engedélyezheti vagy letilthatja a naplókat egy adott erőforráson. Az események tulajdonságok blobjában megjelenő tulajdonságok egy adott napló kategóriáján és erőforrás-típusán belül megegyeznek. |
| ResultType | A művelet állapota. |
| ResultSignature | A művelet alállapota. Ha ez a művelet egy REST API hívásnak felel meg, ez a mező a megfelelő REST-hívás HTTP-állapotkód. |
| ResultDescription | A művelet statikus szöveges leírása. |
| DurationMs | A művelet időtartama ezredmásodpercben. |
| CallerIpAddress | A hívó IP-címe, ha a művelet olyan API-hívásnak felel meg, amely nyilvánosan elérhető IP-címmel rendelkező entitásból származik. |
| Szint | Az esemény súlyossági szintje. |
| URI | A kérelem URI-ja. |
| OutgoingMessageLength | A kimenő üzenetben szereplő karakterek száma. |
| IncomingMessageLength | A bejövő üzenetben szereplő karakterek száma. |
| DeliveryAttempts | Az üzenet kézbesítésére tett kísérletek száma. |
| PhoneNumber | Az SMS-üzenet küldési telefonszáma. |
| SdkType | A kérelemben használt SDK-típus. |
| PlatformType | A kérelemben használt platform típusa. |
| Metódus | A kérelemben használt metódus. |
