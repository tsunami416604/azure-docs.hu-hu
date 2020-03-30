---
title: Az Azure platformnaplók áttekintése | Microsoft dokumentumok
description: Az Azure Monitor naplóinak áttekintése, amelyek gazdag, gyakori adatokat nyújtanak egy Azure-erőforrás működéséről.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659320"
---
# <a name="overview-of-azure-platform-logs"></a>Az Azure platformnaplók áttekintése
A platformnaplók részletes diagnosztikai és naplózási információkat nyújtanak az Azure-erőforrásokhoz és az általuk függő Azure platformhoz. Ezek automatikusan létrejönnek, bár be kell állítania bizonyos platformnaplókat, hogy egy vagy több helyre továbbítsa őket, hogy megmaradjanak. Ez a cikk áttekintést nyújt a platformnaplókról, beleértve az általuk megadott információkat, valamint azt, hogy hogyan konfigurálhatja őket gyűjtésre és elemzésre.

## <a name="types-of-platform-logs"></a>A platformnaplók típusai
Az alábbi táblázat az Azure különböző rétegein elérhető platformnaplókat sorolja fel.

| Napló | Réteg | Leírás |
|:---|:---|:---|
| Erőforrásnaplók | Azure-erőforrások | Betekintést nyújt az Azure-erőforráson (az *adatsíkon)* belül végrehajtott műveletekbe, például egy Key Vault titkos kulcsának beszerzése vagy egy adatbázisba irányuló kérés. Az erőforrásnaplók tartalma az Azure-szolgáltatás tól és az erőforrástípustól függően változik.<br><br>*Az erőforrásnaplókat korábban diagnosztikai naplóknak nevezték.*  |
| Tevékenységnapló | Azure-előfizetés | Betekintést nyújt az egyes Azure-erőforrások műveleteibe az előfizetésben kívülről *(a felügyeleti síkból)* a Service Health-események frissítései mellett. A tevékenységnapló segítségével határozza meg, hogy _ki,_ és _mikor_ milyen írási műveleteket (PUT, POST, DELETE) vett az előfizetés erőforrásaira. _who_ Azt is megismerheti a művelet állapotát és más releváns tulajdonságokat.  Minden Azure-előfizetéshez egyetlen tevékenységnapló található. |
| Az Azure Active Directory naplói | Azure-bérlő |  A bejelentkezési tevékenység előzményeit és az Azure Active Directoryban egy adott bérlőhöz végrehajtott módosítások naplózási nyomvonalát tartalmazza. [Tekintse meg: Mik azok az Azure Active Directory-jelentések?](../../active-directory/reports-monitoring/overview-reports.md) Az Azure Active Directory-naplók teljes leírását.   |

> [!NOTE]
> Az Azure-tevékenységnapló elsősorban az Azure Resource Managerben előforduló tevékenységekhez. Nem követi nyomon az erőforrásokat a Klasszikus/RDFE modell használatával. Egyes klasszikus erőforrástípusok proxyerőforrás-szolgáltatóval rendelkeznek az Azure Resource Managerben (például Microsoft.ClassicCompute). Ha az Azure Resource Manageren keresztül egy klasszikus erőforrástípussal kommunikál ezekkel a proxyerőforrás-szolgáltatókkal, a műveletek megjelennek a tevékenységnaplóban. Ha az Azure Resource Manager proxykon kívül egy klasszikus erőforrástípussal kommunikál, a műveletek csak a műveleti naplóban kerülnek rögzítésre. A műveleti napló a portál egy külön részében is megtekinthető.

![Platformnaplók áttekintése](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Platformnaplók megtekintése
A különböző Azure platformnaplók megtekintésére és elemzésére különböző lehetőségek állnak rendelkezésre.

- Tekintse meg a tevékenységnaplót az Azure Portalon, és a PowerShell és a CLI eseményeinek elérése. A részleteket az [Azure-tevékenységnapló-események megtekintése és beolvasása.](activity-log-view.md) 
- Tekintse meg az Azure Active Directory biztonsági és tevékenységi jelentéseit az Azure Portalon. Lásd: [Mik azok az Azure Active Directory-jelentések?](../../active-directory/reports-monitoring/overview-reports.md)  a részletekért.
- Az erőforrásnaplókat a támogatott Azure-erőforrások automatikusan generálják, de csak akkor tekinthetők meg, ha elküldi őket egy [célhelyre.](#destinations) 

## <a name="destinations"></a>Célhelyek
A figyelési követelményektől függően az alábbi táblázatban szereplő egy vagy több célhelyre küldhet platformnaplókat. Konfigurálja a platformnaplók célhelyeit [diagnosztikai beállítás létrehozásával.](diagnostic-settings.md)

| Cél | Forgatókönyv | Referencia |
|:---|:---|:---|:---|
| Log Analytics-munkaterület | Elemezze a naplókat más figyelési adatokkal, és használja ki az Azure Monitor funkcióit, például a naplólekérdezéseket és riasztásokat. | [Tevékenységnapló és erőforrásnaplók](resource-logs-collect-workspace.md)<br>[Az Azure-tevékenységcímtár naplói](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage-tárterület | Archiválja a naplókat naplózáshoz, statikus elemzéshez vagy biztonsági mentéshez. |[Tevékenységnapló és erőforrásnaplók](archive-diagnostic-logs.md)<br>[Az Azure-tevékenységcímtár naplói](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Eseményközpont | A naplók streamelése harmadik fél naplózási és telemetriai rendszerekre.  |[Tevékenységnapló és erőforrásnaplók](resource-logs-stream-event-hubs.md)<br>[Az Azure-tevékenységcímtár naplói](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>További lépések

* [A különböző kategóriák tevékenységnapló-sémájának megtekintése](activity-log-schema.md)
* [A különböző Azure-szolgáltatások erőforrásnapló-sémájának megtekintése](diagnostic-logs-schema.md)
