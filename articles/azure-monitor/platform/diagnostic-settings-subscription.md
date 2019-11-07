---
title: Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal (előzetes verzió) – Azure Monitor | Microsoft Docs
description: Diagnosztikai beállításokkal továbbíthatja az Azure-tevékenységek naplóit Azure Monitor naplókba, az Azure Storage-ba vagy az Azure Event Hubsba.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 9f8783dc6d3c14b086364639b60273dbae626cee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587978"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal (előzetes verzió)
Az [Azure-tevékenység naplója](activity-logs-overview.md) egy olyan [platform-napló](platform-logs-overview.md) , amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. Eddig létrehozott egy log-profilt, amely a tevékenységi naplók bejegyzéseit [egy Event hub-vagy Storage-fiókba](activity-log-export.md) küldi, és egy összekötőt használ egy [log Analytics munkaterületre](activity-log-collect.md)való begyűjtéséhez.

Mostantól konfigurálhatja az Azure-tevékenység naplójának gyűjteményét az [erőforrás-naplók](resource-logs-overview.md)összegyűjtéséhez használt [diagnosztikai beállítások](diagnostic-settings.md) használatával. A diagnosztikai beállítások használata a következő előnyökkel jár az aktuális módszereknél:

- Konzisztens módszer az összes platform naplójának összegyűjtéséhez.
- Gyűjtsön több előfizetésre és bérlőre kiterjedő tevékenység naplóját.
- A szűrő gyűjtemény csak bizonyos kategóriákhoz tartozó naplókat gyűjt.

## <a name="considerations"></a>Megfontolandó szempontok
A funkció engedélyezése előtt vegye figyelembe a következő részleteket a tevékenység naplóinak gyűjtéséhez a diagnosztikai beállítások használatával.

- A diagnosztikai beállításokkal történő engedélyezés előtt tiltsa le a tevékenység meglévő gyűjteményét. Ha mindkettő engedélyezve van, duplikált adatértéket eredményezhet.
- A tevékenység naplójának az Azure Storage-ba való gyűjtésének megőrzési beállítása el lett távolítva, ami azt jelenti, hogy az adatok határozatlan ideig lesznek tárolva, amíg el nem távolítja.
- A tevékenység naplóját több előfizetésből is elküldheti egyetlen Log Analytics munkaterületre. A műveletnapló bármely előfizetésből akár öt Log Analytics munkaterületre is elküldhető.

## <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása
Az alábbi eljárással hozhat létre diagnosztikai beállítást a Azure Portal az Azure-tevékenység naplójának összegyűjtéséhez. Jelenleg nem hozhat létre előfizetési szintű diagnosztikai beállítást más metódusok használatával.

1. Meglévő Log Analytics munkaterület-gyűjtemény letiltása a tevékenység naplójában:
   1. Nyissa meg a **log Analytics munkaterületek** menüt a Azure Portal, és válassza ki a munkaterületet a tevékenység naplójának összegyűjtéséhez.
   2. A munkaterület menü **munkaterület adatforrásai** területén válassza az **Azure-tevékenység napló**elemet.
   3. Kattintson a leválasztani kívánt előfizetésre.
   4. Kattintson a **Leválasztás** lehetőségre, majd az **Igen** gombra, amikor a rendszer megkérdezi, hogy erősítse meg
2. A Azure Portal **Azure monitor** menüjében válassza a **műveletnapló**elemet.
3. Kattintson a **Diagnosztikai beállítások** elemre.
   
   ![Diagnosztikai beállítások](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Kattintson a régi élményhez tartozó lila szalagcímre, és tiltsa le az aktuális gyűjteményt a Storage vagy az Event hubok számára. 

    ![Korábbi élmény](media/diagnostic-settings-subscription/legacy-experience.png)

5. Diagnosztikai beállítások létrehozásához kövesse a [diagnosztikai beállítások létrehozása a Azure Portalban](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal) című témakör eljárásait. A tevékenység naplójában lévő események szűréséhez használható kategóriák magyarázatát a [következő témakörben](activity-logs-overview.md#categories-in-the-activity-log) találja:. 


## <a name="differences-in-data"></a>Adateltérések
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

## <a name="activity-log-monitoring-solution"></a>Műveletnapló-figyelési megoldás
Az Azure Log Analytics-figyelési megoldás több naplózási lekérdezést és nézetet tartalmaz a Log Analytics munkaterületen található műveletnapló-rekordok elemzéséhez. Ez a megoldás a Log Analytics munkaterületen összegyűjtött naplózási adatokat használja, és a tevékenység naplójának diagnosztikai beállításokkal történő begyűjtése után továbbra is működni fog. A megoldás részleteiért tekintse meg a [Tevékenységnaplók Analytics-figyelési megoldást](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>További lépések

* [További információ a tevékenység naplóról](../../azure-resource-manager/resource-group-audit.md)
* [További információ a diagnosztikai beállításokról](diagnostic-settings.md)
