---
title: Az Azure platform naplói – áttekintés | Microsoft Docs
description: A Azure Monitor naplófájljainak áttekintése, amelyek gazdag és gyakori információkat biztosítanak az Azure-erőforrások működéséről.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 5f02368bfb0c084691376300980d4cdee0d9b3be
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530884"
---
# <a name="overview-of-azure-platform-logs"></a>Az Azure platform naplófájljainak áttekintése
A platform naplói részletes diagnosztikai és naplózási információkat biztosítanak az Azure-erőforrásokhoz és az Azure-platformtól függenek. Ezek automatikusan létrejönnek, bár bizonyos platform-naplókat egy vagy több megőrzött célhelyre kell továbbítani. Ez a cikk áttekintést nyújt a platform naplóiról, beleértve az általuk biztosított információkat, valamint azt, hogy hogyan konfigurálhatja őket gyűjteményekhez és elemzésekhez.

## <a name="types-of-platform-logs"></a>A platform naplófájljainak típusai
A következő táblázat felsorolja az Azure különböző rétegeiben elérhető platform-naplókat.

| Napló | Réteg | Leírás |
|:---|:---|:---|
| Erőforrásnaplók | Azure-erőforrások | Betekintést nyerhet az Azure-erőforrásokon (az *adatsíkon*) végrehajtott műveletekre, például a titkos kulcs beszerzése egy Key Vault vagy egy adatbázisra vonatkozó kérelem elkészítése. Az erőforrás-naplók tartalma az Azure-szolgáltatás és az erőforrás típusa szerint változik.<br><br>*Az erőforrás-naplókat korábban diagnosztikai naplóknak nevezzük.*  |
| Tevékenységnapló | Azure-előfizetés | Betekintést nyújt a műveletekre az előfizetésben lévő egyes Azure-erőforrásokon kívülről (*a felügyeleti síkon*), valamint a Service Health események frissítései mellett. A tevékenység naplójának használatával meghatározhatja a _mi_, _ki_és _Mikor_ az előfizetésben lévő erőforrásokra vonatkozó írási műveletek (Put, post, DELETE) esetében. Emellett megismerheti a művelet állapotát és az egyéb kapcsolódó tulajdonságokat is.  Minden egyes Azure-előfizetéshez egyetlen tevékenységi napló van. |
| Naplók Azure Active Directory | Azure-bérlő |  A bejelentkezési tevékenység előzményeit és a Azure Active Directory egy adott bérlő esetében végrehajtott módosítások naplózási nyomvonalát tartalmazza. Lásd: [Mi a Azure Active Directory-jelentés?](../../active-directory/reports-monitoring/overview-reports.md) a Azure Active Directory naplók teljes leírását.   |

> [!NOTE]
> Az Azure-tevékenység naplója elsősorban a Azure Resource Managerban bekövetkező tevékenységek esetében fordul elő. A klasszikus/RDFE modellt használó erőforrásokat nem követi nyomon. Néhány klasszikus erőforrástípus rendelkezik egy proxy erőforrás-szolgáltatóval Azure Resource Managerban (például: Microsoft. ClassicCompute). Ha egy klasszikus erőforrástípust használ a Azure Resource Manager ezen proxy erőforrás-szolgáltatók használatával, a műveletek megjelennek a tevékenység naplójában. Ha a Azure Resource Manager proxyn kívüli klasszikus erőforrástípus is működik, a műveletek csak a műveleti naplóban lesznek rögzítve. A műveleti naplót a portál egy külön szakasza is megkeresheti.

![Platformnaplók áttekintése](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Platform naplófájljainak megtekintése
A különböző Azure platform-naplók megtekintésére és elemzésére különböző lehetőségek állnak rendelkezésre.

- Tekintse meg a Azure Portal és a PowerShell és a parancssori felület eseményeinek elérését ismertető naplót. Részletekért lásd: az [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](activity-log-view.md) . 
- Azure Active Directory biztonsági és tevékenységi jelentések megtekintése a Azure Portalban. Lásd: [Mik azok a Azure Active Directory-jelentések?](../../active-directory/reports-monitoring/overview-reports.md)  a részletekért.
- Az erőforrás-naplókat a támogatott Azure-erőforrások automatikusan létrehozzák, de csak akkor érhetők el, ha nem küldi őket a [célhelyre](#destinations). 

## <a name="destinations"></a>Célhelyek
A figyelési követelményektől függően a következő táblázatban szereplő célok közül egy vagy több célhelyre is küldhet platform-naplókat. [Diagnosztikai beállítások létrehozásával](diagnostic-settings.md)konfigurálja a platform-naplók célhelyeit.

| Cél | Alkalmazási helyzet | Tudástár |
|:---|:---|:---|:---|
| Log Analytics-munkaterület | Elemezze a naplókat más figyelési adattal, és használja ki Azure Monitor funkciókat, például a naplózási lekérdezéseket és a riasztásokat. | [Műveletnapló és erőforrás-naplók](resource-logs-collect-workspace.md)<br>[Azure-tevékenység könyvtárának naplói](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Archiválja a naplókat a naplózáshoz, a statikus elemzéshez vagy a biztonsági mentéshez. |[Műveletnapló és erőforrás-naplók](archive-diagnostic-logs.md)<br>[Azure-tevékenység könyvtárának naplói](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Eseményközpont | A naplók továbbítása harmadik féltől származó naplózási és telemetria rendszerekre.  |[Műveletnapló és erőforrás-naplók](resource-logs-stream-event-hubs.md)<br>[Azure-tevékenység könyvtárának naplói](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Következő lépések

* [A tevékenységi napló sémájának megtekintése különböző kategóriákhoz](activity-log-schema.md)
* [A különböző Azure-szolgáltatások erőforrás-naplózási sémájának megtekintése](diagnostic-logs-schema.md)
