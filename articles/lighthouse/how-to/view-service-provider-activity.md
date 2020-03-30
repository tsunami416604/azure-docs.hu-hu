---
title: Szolgáltatói tevékenység megtekintése
description: Az ügyfelek megtekinthetik a naplózott tevékenységeket, hogy lássák a szolgáltatók által az Azure delegált erőforrás-kezelésén keresztül végrehajtott műveleteket.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649636"
---
# <a name="view-service-provider-activity"></a>Szolgáltatói tevékenység megtekintése

Azok az ügyfelek, akik delegált előfizetéseket kaptak az Azure delegált erőforrás-kezelésért, [megtekinthetik az Azure-tevékenységnapló](../../azure-monitor/platform/platform-logs-overview.md) adatait, és megtekinthetik az összes végrehajtott műveletet. Ez teljes betekintést nyújt az ügyfelek nek az Azure delegált erőforrás-kezelése révén végrehajtott műveletekre, valamint az ügyfél saját Azure Active Directory (Azure AD) bérlőjének felhasználói által végzett műveletekre.

> [!TIP]
> Az Azure Policy beépített szabályzat-definícióját is biztosítjuk a hatókörök felügyelt bérlőnek való delegálásának naplózásához. További információ: [Delegálások naplózása a környezetben című témakörben.](view-manage-service-providers.md#audit-delegations-in-your-environment)

## <a name="view-activity-log-data"></a>Tevékenységnapló adatainak megtekintése

A [tevékenységnaplót az](../../azure-monitor/platform/activity-log-view.md) Azure Portal **Monitor** menüjében tekintheti meg. Ha az eredményeket egy adott előfizetésre szeretné korlátozni, a szűrők segítségével válasszon ki egy adott előfizetést. A [tevékenységnapló-eseményeket](../../azure-monitor/platform/activity-log-view.md) programozott módon is megtekintheti és bekeresheti.

> [!NOTE]
> A szolgáltató bérlőinek felhasználói megtekinthetik a delegált előfizetés tevékenységnaplójának eredményeit egy ügyfél-bérlőben, ha megkapták az [Olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört (vagy egy másik beépített szerepkört, amely tartalmazza az Olvasó-hozzáférést), amikor az előfizetést az Azure delegált erőforrás-kezeléséhez beszervezték.

A tevékenységnaplóban megjelenik a művelet neve és állapota, valamint a végrehajtás dátuma és időpontja. Az **oszlop által kezdeményezett esemény** megmutatja, hogy melyik felhasználó hajtotta végre a műveletet, függetlenül attól, hogy egy felhasználó egy szolgáltató bérlője az Azure delegált erőforrás-kezelés, vagy a felhasználó az ügyfél saját bérlője. Vegye figyelembe, hogy a felhasználó neve jelenik meg, nem pedig a bérlő vagy a szerepkör, amely a felhasználó rendelt az adott előfizetéshez.

A naplózott tevékenység az elmúlt 90 napban érhető el az Azure Portalon. Ha többet szeretne megtudni arról, hogyan tárolhatja ezeket az adatokat 90 napnál hosszabb ideig, olvassa [el az Azure-tevékenységnaplók gyűjtése és elemzése a Log Analytics-munkaterületen című témakört.](../../azure-monitor/platform/activity-log-collect.md)

> [!NOTE]
> A szolgáltató felhasználói megjelennek a tevékenységnaplóban, de ezek a felhasználók és szerepkör-hozzárendeléseik nem jelennek meg a **hozzáférés-vezérlésben (IAM),** illetve a szerepkör-hozzárendelési adatok API-kon keresztüli beolvasásakor.

## <a name="set-alerts-for-critical-operations"></a>Riasztások beállítása a kritikus műveletekhez

Ha tudni szeretné, hogy a szolgáltatók (vagy a saját bérlője felhasználói) kritikus műveleteket hajtanak végre, javasoljuk, hogy hozzon létre [tevékenységnapló-riasztásokat.](../../azure-monitor/platform/activity-log-alerts.md) Előfordulhat például, hogy egy előfizetés összes felügyeleti műveletét nyomon szeretné követni, vagy értesítést kaphat, ha egy adott erőforráscsoport bármely virtuális gépe törlődik. Amikor riasztásokat hoz létre, azok tartalmazzák a felhasználók által az ügyfél saját bérlőjében, valamint a bérlők kezelése során végrehajtott műveleteket.

További információt a [Tevékenységnapló-riasztások létrehozása és kezelése című témakörben talál.](../../azure-monitor/platform/alerts-activity-log.md)

## <a name="create-log-queries"></a>Naplólekérdezések létrehozása

Lekérdezéseket hozhat létre a naplózott tevékenység elemzéséhez vagy adott elemekre való összpontosításhoz. Például előfordulhat, hogy egy audit megköveteli, hogy jelentést az összes felügyeleti szintű műveletek et egy előfizetés. Létrehozhat egy lekérdezést, amely csak ezekre a műveletekre szűr, és az eredményeket felhasználó, dátum vagy más érték szerint rendezi.

További információt a [Naplólekérdezések áttekintése az Azure Monitorban](../../azure-monitor/log-query/log-query-overview.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

- További információ az [Azure Monitorról.](../../azure-monitor/index.yml)
- Ismerje meg, hogyan tekintheti meg és kezelheti a [szolgáltatói ajánlatokat](view-manage-service-providers.md) az Azure Portalon.