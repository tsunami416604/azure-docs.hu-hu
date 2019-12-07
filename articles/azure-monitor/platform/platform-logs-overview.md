---
title: Az Azure platform naplói – áttekintés | Microsoft Docs
description: A Azure Monitor naplófájljainak áttekintése, amelyek gazdag és gyakori információkat biztosítanak az Azure-erőforrások működéséről.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 480c028f11de9a7c44168b217ad3553d721d01e1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894551"
---
# <a name="overview-of-azure-platform-logs"></a>Az Azure platform naplófájljainak áttekintése
A platform naplói részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokhoz és az Azure-platformtól függenek. Ezek automatikusan létrejönnek, bár bizonyos platform-naplókat egy vagy több megőrzött célhelyre kell továbbítani. Ez a cikk áttekintést nyújt a platform naplóiról, beleértve az általuk biztosított információkat, valamint azt, hogy hogyan konfigurálhatja őket gyűjteményekhez és elemzésekhez.

## <a name="types-of-platform-logs"></a>A platform naplófájljainak típusai
A következő táblázat felsorolja az Azure különböző rétegeiben elérhető platform-naplókat.

| Réteg | Naplók | Leírás |
|:---|:---|:---|
| Azure-erőforrások | [Erőforrás-naplók](resource-logs-overview.md) | Betekintést nyerhet az Azure-erőforrásokon (az *adatsíkon*) végrehajtott műveletekre, például a titkos kulcs beszerzése egy Key Vault vagy egy adatbázisra vonatkozó kérelem elkészítése. Az erőforrás-naplók tartalma az Azure-szolgáltatás és az erőforrás típusa szerint változik.<br>*Az erőforrás-naplókat korábban diagnosztikai naplóknak nevezzük.*  |
| Azure-előfizetés | [Tevékenységnapló](activity-logs-overview.md) | Betekintést nyújt a műveletekre az előfizetésben lévő egyes Azure-erőforrásokon kívülről (*a felügyeleti síkon*), valamint a Service Health események frissítései mellett. Minden egyes Azure-előfizetéshez egyetlen tevékenységi napló van.   |
| Azure-bérlő | [Naplók Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)  | A bejelentkezési tevékenység előzményeit és a Azure Active Directory egy adott bérlő esetében végrehajtott módosítások naplózási nyomvonalát tartalmazza.   |


![Platformnaplók áttekintése](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Platform naplófájljainak megtekintése
A Azure Portalban megtekintheti a [műveletnapló](activity-log-view.md) és a [Azure Active Directory naplókat](../../active-directory/reports-monitoring/overview-reports.md) . Az erőforrás-naplókat a [célhelyre](#destinations) kell küldenie a megtekintéséhez.


## <a name="destinations"></a>Célhelyek
A figyelési követelményektől függően a következő táblázatban szereplő célok közül egy vagy több célhelyre is küldhet platform-naplókat. 

| Cél | Alkalmazási helyzet | Tudástár |
|:---|:---|:---|:---|
| Log Analytics-munkaterület | Elemezze a naplókat más figyelési adattal, és használja ki Azure Monitor funkciókat, például a naplózási lekérdezéseket és a riasztásokat. | [Erőforrás-naplók](resource-logs-collect-storage.md)<br>[Tevékenységnapló](activity-log-collect.md)<br>[Azure-tevékenység könyvtárának naplói](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Archiválja a naplókat a naplózáshoz, a statikus elemzéshez vagy a biztonsági mentéshez. |[Erőforrás-naplók](archive-diagnostic-logs.md)<br>[Tevékenységnapló](activity-log-export.md)<br>[Azure-tevékenység könyvtárának naplói](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Eseményközpont | A naplók továbbítása harmadik féltől származó naplózási és telemetria rendszerekre.  |[Erőforrás-naplók](resource-logs-stream-event-hubs.md)<br>[Tevékenységnapló](activity-log-export.md)<br>[Azure-tevékenység könyvtárának naplói](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Diagnosztikai beállítások és bejelentkezési profilok
[Diagnosztikai beállítások létrehozásával](diagnostic-settings.md)konfigurálja az erőforrás-naplók és Azure Active Directory-naplók célhelyeit. Konfigurálja a tevékenység naplójának célhelyeit [egy log-profil létrehozásával](activity-log-export.md) vagy [egy log Analytics munkaterülethez való csatlakoztatásával](activity-log-collect.md).

A diagnosztikai beállítás és a napló profilja az alábbiakat határozza meg:

- Egy vagy több célhely a kiválasztott naplók és metrikák elküldéséhez.
- Az erőforrásból származó naplózási kategóriákat és mérőszámokat a rendszer elküldi a célhelyekre.
- Ha egy Storage-fiók van kiválasztva célként, mennyi ideig kell megőrizni az egyes naplók kategóriáit.



## <a name="next-steps"></a>Következő lépések

* [További információ a tevékenység naplóról](activity-logs-overview.md)
* [További információ az erőforrás-naplókról](resource-logs-overview.md)
* [A különböző Azure-szolgáltatások erőforrás-naplózási sémájának megtekintése](diagnostic-logs-schema.md)
