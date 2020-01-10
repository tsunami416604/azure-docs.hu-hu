---
title: Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal (előzetes verzió) – Azure Monitor | Microsoft Docs
description: Diagnosztikai beállításokkal továbbíthatja az Azure-tevékenységek naplóit Azure Monitor naplókba, az Azure Storage-ba vagy az Azure Event Hubsba.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 12/20/2019
ms.openlocfilehash: e3a8048d8bcb8bb737c511a8b8bb6b57f9e81403
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750980"
---
# <a name="collect-azure-activity-log-with-legacy-settings"></a>Az Azure-tevékenység naplójának összegyűjtése örökölt beállításokkal
Az [Azure-tevékenység naplója](platform-logs-overview.md) egy olyan [platform-napló](platform-logs-overview.md) , amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. A legutóbbi időpontig létrehozott egy log-profilt, amely a műveletnapló-bejegyzéseket [egy Event hub-vagy Storage-fiókba](activity-log-export.md) küldi, és egy összekötőt használt egy [log Analytics munkaterületre](activity-log-collect.md)való begyűjtéséhez. Ez a cikk a módszerek közötti különbséget ismerteti, a meglévő örökölt beállításokkal való munkavégzést, valamint a korábbi beállítások törlését a diagnosztikai beállítások előkészítése során.


## <a name="differences-between-methods"></a>A módszerek közötti különbségek

### <a name="advantages"></a>Előnyök
A diagnosztikai beállítások használata a következő előnyökkel jár az aktuális módszereknél:

- Konzisztens módszer az összes platform naplójának összegyűjtéséhez.
- Gyűjtsön több előfizetésre és bérlőre kiterjedő tevékenység naplóját.
- A szűrő gyűjtemény csak bizonyos kategóriákhoz tartozó naplókat gyűjt.
- Az összes műveletnapló-kategória összegyűjtése. Egyes kategóriák gyűjtése nem a régi metódussal történik.
- Gyorsabb késés a naplók betöltéséhez. Az előző metódus körülbelül 15 percet vesz igénybe, míg a diagnosztikai beállítások csak 1 percet vesznek fel.
  
### <a name="considerations"></a>Megfontolandó szempontok
A funkció engedélyezése előtt vegye figyelembe a következő részleteket a tevékenység naplóinak gyűjtéséhez a diagnosztikai beállítások használatával.

- A tevékenység naplójának az Azure Storage-ba való gyűjtésének megőrzési beállítása el lett távolítva, ami azt jelenti, hogy az adatok határozatlan ideig lesznek tárolva, amíg el nem távolítja.
- Jelenleg csak az előfizetési szint diagnosztikai beállításait lehet létrehozni a Azure Portal használatával. Más módszerek, például a PowerShell vagy a CLI használatához létrehozhat egy Resource Manager-sablont.


### <a name="differences-in-data"></a>Adateltérések
A diagnosztikai beállítások a tevékenység naplójának összegyűjtéséhez használt korábbi módszerekkel azonos adatokat gyűjtenek a következő jelenlegi eltérésekkel:

A következő tulajdonságok lettek eltávolítva:

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

A következő tulajdonságok lettek hozzáadva:

- Authorization_d
- Claims_d
- Properties_d

## <a name="work-with-legacy-settings"></a>Régi beállítások használata
A tevékenység naplójának összegyűjtéséhez használt örökölt beállítások továbbra is működni fognak, ha nem kívánja helyettesíteni a diagnosztikai beállításokat. A következő módszer használatával kezelheti az előfizetések naplójának profilját.

1. A Azure Portal **Azure monitor** menüjében válassza a **műveletnapló**elemet.
3. Kattintson a **Diagnosztikai beállítások** elemre.
   
   ![Diagnosztikai beállítások](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. A régi élményhez kattintson a lila szalagcímre. 

    ![Korábbi élmény](media/diagnostic-settings-subscription/legacy-experience.png)


A régi gyűjtemény módszereinek használatáról a következő cikkekben talál további információt.

- [Azure-beli tevékenység-naplók gyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](activity-log-collect.md)
- [Azure-beli tevékenységek naplóinak gyűjtése a Azure Monitor Azure Active Directory-bérlők között](activity-log-collect-tenants.md)
- [Azure-Tevékenységnaplók exportálása a Storage-ba vagy az Azure Event Hubsba](activity-log-export.md)

## <a name="disable-existing-settings"></a>Meglévő beállítások letiltása
A diagnosztikai beállításokkal történő engedélyezés előtt tiltsa le a tevékenység meglévő gyűjteményét. Ha mindkettő engedélyezve van, duplikált adatértéket eredményezhet.

### <a name="disable-collection-into-log-analytics-workspace"></a>Gyűjtemény letiltása Log Analytics munkaterületre

1. Nyissa meg a **log Analytics munkaterületek** menüt a Azure Portal, és válassza ki a munkaterületet a tevékenység naplójának összegyűjtéséhez.
2. A munkaterület menü **munkaterület adatforrásai** területén válassza az **Azure-tevékenység napló**elemet.
3. Kattintson a leválasztani kívánt előfizetésre.
4. Kattintson a **Leválasztás** lehetőségre, majd az **Igen** gombra, amikor a rendszer megkérdezi, hogy erősítse meg

### <a name="disable-log-profile"></a>Log-profil letiltása

1. Az örökölt beállítások megnyitásához használja az [örökölt beállításokkal végzett munka](#work-with-legacy-settings) című témakörben leírt eljárást.
2. Tiltsa le az aktuális gyűjteményt a Storage vagy az Event hub szolgáltatásban. 



## <a name="activity-log-monitoring-solution"></a>Műveletnapló-figyelési megoldás
Az Azure Log Analytics-figyelési megoldás több naplózási lekérdezést és nézetet tartalmaz a Log Analytics munkaterületen található műveletnapló-rekordok elemzéséhez. Ez a megoldás a Log Analytics munkaterületen összegyűjtött naplózási adatokat használja, és a tevékenység naplójának diagnosztikai beállításokkal történő begyűjtése után továbbra is működni fog. A megoldás részleteiért tekintse meg a [Tevékenységnaplók Analytics-figyelési megoldást](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Következő lépések

* [További információ a tevékenység naplóról](../../azure-resource-manager/resource-group-audit.md)
* [További információ a diagnosztikai beállításokról](diagnostic-settings.md)
