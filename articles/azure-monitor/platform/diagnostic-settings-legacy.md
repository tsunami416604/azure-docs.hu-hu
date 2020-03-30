---
title: Az Azure-tevékenységnapló gyűjtése diagnosztikai beállításokkal - Azure Monitor | Microsoft dokumentumok
description: Diagnosztikai beállítások használatával továbbítsa az Azure-tevékenységnaplókat az Azure Monitor-naplók, az Azure storage vagy az Azure Event Hubs.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096897"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Frissítés az Azure-tevékenységnapló-gyűjteményhez és -exportáláshoz
Az [Azure-tevékenységnapló](platform-logs-overview.md) egy [platformnapló,](platform-logs-overview.md) amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekbe. A tevékenységnapló-bejegyzések [eseményközpontba vagy tárfiókba,](activity-log-export.md) illetve [a Log Analytics-munkaterületre](activity-log-collect.md) történő küldésének módja [a diagnosztikai beállítások](diagnostic-settings.md)használatára változott. Ez a cikk ismerteti a módszerek közötti különbséget, és azt, hogy hogyan törölheti az örökölt beállításokat a diagnosztikai beállítások módosításának előkészítése során.


## <a name="differences-between-methods"></a>A módszerek közötti különbségek

### <a name="advantages"></a>Előnyök
A diagnosztikai beállítások használata a következő előnyökkel jár a jelenlegi módszerekkel szemben:

- Konzisztens módszer az összes platformnapló összegyűjtésére.
- Tevékenységnapló gyűjtése több előfizetésés bérlő között.
- Szűrőgyűjtés, hogy csak bizonyos kategóriáknaplóit gyűjtse össze.
- Gyűjtse össze az összes tevékenységnapló-kategóriát. Egyes kategóriák nem gyűjtik örökölt módszerrel.
- Gyorsabb késés a naplóbetöltéshez. Az előző módszer körülbelül 15 perces késés, míg a diagnosztikai beállítások hozzáteszi, csak körülbelül 1 perc.

### <a name="considerations"></a>Megfontolandó szempontok
A szolgáltatás engedélyezése előtt vegye figyelembe a tevékenységnapló-gyűjtemény diagnosztikai beállításokkal történő gyűjtésének következő részleteit.

- A tevékenységnapló Azure storage-ba történő gyűjtésére szolgáló adatmegőrzési beállítás el lett távolítva, ami azt jelenti, hogy az adatok határozatlan ideig tárolódnak, amíg el nem távolítja őket.
- Jelenleg csak az Azure Portal használatával hozhat létre előfizetési szint diagnosztikai beállítást. Más módszerek, például a PowerShell vagy a CLI használatához létrehozhat egy Erőforrás-kezelő sablont.


### <a name="differences-in-data"></a>Az adatok eltérései
A diagnosztikai beállítások ugyanazokat az adatokat gyűjtik, mint a tevékenységnapló gyűjtésére használt korábbi módszerek, a következő aktuális különbségekkel:

A következő oszlopok at eltávolították. Ezeknek az oszlopoknak a cseréje más formátumú, ezért előfordulhat, hogy módosítania kell az azokat használó naplólekérdezéseket. Előfordulhat, hogy továbbra is megjelennek az eltávolított oszlopok a sémában, de nem lesznek feltöltve adatokkal.

| Eltávolított oszlop | Csere oszlop |
|:---|:---|
| ActivityStatus (Tevékenységállapota)    | ActivityStatusValue    |
| ActivitySubstatus (Tevékenységsubstatus) | ActivitySubstatusValue érték |
| OperationName     | OperationNameValue érték     |
| ResourceProvider  | ResourceProviderValue érték  |

A következő oszlopmal egészült ki:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> Bizonyos esetekben az oszlopokban lévő értékek nagybetűsek lehetnek. Ha olyan lekérdezéssel rendelkezik, amely ezeket az oszlopokat tartalmazza, az [=~ operátorral](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) végezze el a kis- és nagybetűk megkülönböztetését.

## <a name="work-with-legacy-settings"></a>A régi beállítások használata
A tevékenységnapló gyűjtésére szolgáló örökölt beállítások továbbra is működnek, ha nem választja úgy, hogy diagnosztikai beállítással helyettesíti. Az alábbi módszerrel kezelheti az előfizetés naplóprofilját.

1. Az **Azure-portál Azure Monitor** menüjében válassza a **Tevékenységnapló**lehetőséget.
3. Kattintson a **Diagnosztikai beállítások** elemre.

   ![Diagnosztikai beállítások](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Kattintson a lila banner az örökölt élmény.

    ![Örökölt élmény](media/diagnostic-settings-subscription/legacy-experience.png)


Az örökölt gyűjtési módszerek használatával kapcsolatos részleteket az alábbi cikkekben találja.

- [Azure-tevékenységnaplók gyűjtése és elemzése az Azure-figyelő Log Analytics-munkaterületén](activity-log-collect.md)
- [Azure-tevékenységnaplók gyűjtése az Azure Monitorban az Azure Active Directory-bérlők között](activity-log-collect-tenants.md)
- [Azure-tevékenységnapló exportálása tárhelyre vagy Azure-eseményközpontokba](activity-log-export.md)

## <a name="disable-existing-settings"></a>Meglévő beállítások letiltása
A diagnosztikai beállítások használata előtt le kell tiltania a tevékenység meglévő gyűjteményét. Ha mindkettő engedélyezve van, az ismétlődő adatokat eredményezhet.

### <a name="disable-collection-into-log-analytics-workspace"></a>Gyűjtemény letiltása a Log Analytics-munkaterületen

1. Nyissa meg a **Log Analytics-munkaterületek** menüt az Azure Portalon, és válassza ki a munkaterületet a tevékenységnapló gyűjtéséhez.
2. A munkaterület menüjének **Munkaterület-adatforrások** szakaszában válassza az **Azure-tevékenységnapló t.**
3. Kattintson a kapcsolat bontásához kívánt előfizetésre.
4. Kattintson **a Kapcsolat bontása,** majd az **Igen** gombra, amikor a rendszer a választás megerősítésére kéri.

### <a name="disable-log-profile"></a>Naplóprofil letiltása

1. A korábbi beállítások megnyitásához használja a [Korábbi beállítások használata](#work-with-legacy-settings) című részben ismertetett eljárást.
2. Tiltson le minden aktuális gyűjteményt a tárolókba vagy az eseményközpontokba.



## <a name="activity-log-monitoring-solution"></a>Tevékenységnapló-figyelő megoldás
Az Azure Log Analytics figyelési megoldás több naplólekérdezést és nézetet tartalmaz a Naplóelemzési munkaterületen lévő tevékenységnapló-rekordok elemzéséhez. Ez a megoldás a Log Analytics-munkaterületen gyűjtött naplóadatokat használja, és továbbra is módosítások nélkül fog működni, ha diagnosztikai beállításokkal gyűjti a tevékenységnaplót. A megoldással kapcsolatos részletekért tekintse meg a [Tevékenységnaplók elemzésfigyelési megoldását.](activity-log-collect.md#activity-logs-analytics-monitoring-solution)

## <a name="next-steps"></a>További lépések

* [További információ a tevékenységnaplóról](../../azure-resource-manager/management/view-activity-logs.md)
* [További információ a diagnosztikai beállításokról](diagnostic-settings.md)
