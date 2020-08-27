---
title: Erőforrás-események beolvasása a Azure App Serviceban
description: Ismerje meg, hogyan szerezhet be erőforrás-eseményeket tevékenység-naplók és Event Grid használatával a App Service alkalmazásban.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c5c84891187c540c0b24162cf5c8c7f96e9e731a
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962298"
---
# <a name="get-resource-events-in-azure-app-service"></a>Erőforrás-események beolvasása a Azure App Serviceban

A Azure App Service beépített eszközöket biztosít az erőforrások állapotának és állapotának figyeléséhez. Az erőforrás-események segítenek megérteni az alapul szolgáló webalkalmazás-erőforrásokkal kapcsolatos módosításokat, és szükség esetén végrehajtják a szükséges lépéseket. Ilyenek például a példányok skálázása, az Alkalmazásbeállítások frissítése, a webalkalmazás újraindítása és sok más. Ebből a cikkből megtudhatja, hogyan tekintheti meg az [Azure-tevékenységek naplóit](../azure-monitor/platform/activity-log.md#view-the-activity-log) , és hogyan engedélyezheti [Event Grid](../event-grid/index.yml) a app Service webalkalmazáshoz kapcsolódó erőforrás-események figyelését.

> [!NOTE]
> Az Event Grid-integráció App Service **előzetes**verzióban érhető el. [További részletekért tekintse meg a közleményt.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Azure-Tevékenységnaplók megtekintése
Az Azure-tevékenység naplói az előfizetésében lévő erőforrásokon végrehajtott műveletek által kibocsátott erőforrás-eseményeket tartalmazzák. A Azure Portal és Azure Resource Manager sablonok felhasználói műveletei is hozzájárulnak a tevékenység naplójában rögzített eseményekhez. 

Az Azure-beli tevékenység naplói a App Service részleteket, például a következőket:
- milyen műveleteket végeztek az erőforrásokon (pl.: App Service csomagok)
- a művelet elindítása
- a művelet bekövetkeztekor
- a művelet állapota
- a művelet megkutatásához segítséget nyújtó tulajdonságértékek

### <a name="what-can-you-do-with-azure-activity-logs"></a>Mire használhatók az Azure-beli tevékenységek naplói?

Az Azure-tevékenység naplófájljai a Azure Portal, a PowerShell, a REST API vagy a parancssori felület használatával kérhetők le. A naplókat egy Storage-fiókba, az Event Hubbe és a Log Analyticsba is elküldheti. Power BI elemezheti őket, vagy létrehozhat riasztásokat, hogy naprakész maradjon az erőforrás-eseményekről.

[Azure-tevékenységek naplózási eseményeinek megtekintése és beolvasása.](../azure-monitor/platform/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>Tevékenységek naplóinak Event Gridba való szállítása

Míg a tevékenység-naplók felhasználó-alapúak, új [Event Grid](../event-grid/index.yml) integráció a app Service (előzetes verzió) szolgáltatással, amely a felhasználói műveleteket és az automatizált eseményeket is naplózza. A Event Grid segítségével beállíthatja, hogy a kezelő reagáljon az említett eseményekre. Az Event Grid használatával például azonnal aktiválható egy képelemzést futtató, kiszolgáló nélküli függvény, amikor egy új fénykép kerül egy Blob Storage-tárolóba.

Vagy az Event Gridet a Logic Appsszel együtt használhatja tetszőleges helyen, kód írása nélkül végzett adatfeldolgozásra. Az Event Grid összeköti az adatforrásokat az eseménykezelőkkel. Az Event Grid használatával például azonnal aktiválható egy képelemzést futtató, kiszolgáló nélküli függvény, amikor egy új fénykép kerül egy Blob Storage-tárolóba.

[Azure App Service események tulajdonságainak és sémájának megtekintése.](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> További lépések
* [Naplók lekérdezése Azure Monitor](../azure-monitor/log-query/log-query-overview.md)
* [A Azure App Service figyelése](web-sites-monitor.md)
* [Hibaelhárítási Azure App Service a Visual Studióban](troubleshoot-dotnet-visual-studio.md)
* [Alkalmazás-naplók elemzése a HDInsight-ben](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)