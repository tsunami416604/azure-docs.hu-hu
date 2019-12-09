---
title: Szolgáltatói tevékenység megtekintése
description: Az ügyfelek megtekinthetik a naplózott tevékenységeket a szolgáltatók által az Azure-beli delegált erőforrás-kezelésen keresztül végrehajtott műveletek megtekintéséhez.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: 69517e942aa9f82be16fa3d0e7d6f9252de44d4c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932683"
---
# <a name="view-service-provider-activity"></a>Szolgáltatói tevékenység megtekintése

Azok az ügyfelek, akik az Azure-beli delegált erőforrás-kezeléshez delegált előfizetésekkel rendelkeznek, [megtekinthetik az Azure-tevékenység naplójának](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) adatait az összes művelet megtekintéséhez Ez teljes körű láthatóságot biztosít az ügyfeleknek a szolgáltatók számára az Azure-beli delegált erőforrás-kezelésen keresztül, valamint azokat, amelyeket az ügyfél saját Azure Active Directory (Azure AD) bérlője végez.

## <a name="view-activity-log-data"></a>Tevékenység naplójának adatnézete

A [tevékenység naplóját](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview#view-the-activity-log) a Azure Portal **figyelő** menüjéből tekintheti meg. Az eredmények egy adott előfizetésre való korlátozásához használhatja a szűrőket egy adott előfizetés kiválasztásához. [A tevékenységek naplózási eseményeit programozott módon is megtekintheti és lekérheti](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) .

> [!NOTE]
> A szolgáltató bérlője által biztosított felhasználók megtekinthetik a tevékenység naplójának eredményét egy ügyfél-bérlőben lévő delegált előfizetésre vonatkozóan, ha az [olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) szerepkört (vagy egy másik beépített szerepkört is tartalmaznak, amely olvasói hozzáférést is tartalmaz) az Azure-beli delegált erőforrás-kezeléshez.

A tevékenység naplójában látni fogja a művelet nevét és állapotát, valamint a végrehajtás dátumát és időpontját. Az oszlop **által kezdeményezett esemény** azt jeleníti meg, hogy melyik felhasználó végezte el a műveletet, függetlenül attól, hogy a szolgáltató bérlője egy Azure-beli delegált erőforrás-kezelést vagy egy, az ügyfél saját bérlője által elindított felhasználó. Vegye figyelembe, hogy a felhasználó neve jelenik meg, nem pedig a bérlő vagy a felhasználó által az előfizetéshez hozzárendelt szerepkör.

A naplózott tevékenység a Azure Portal az elmúlt 90 napban érhető el. Az adatok 90 napnál hosszabb ideig történő tárolásával kapcsolatban tekintse meg az [Azure-beli tevékenységek naplóinak összegyűjtése és elemzése log Analytics munkaterületen Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

## <a name="set-alerts-for-critical-operations"></a>Riasztások beállítása kritikus műveletekhez

Annak érdekében, hogy a szolgáltatók (vagy a saját bérlők felhasználói) által végzett kritikus műveletekkel tisztában legyenek, javasoljuk, hogy hozzon létre [műveletnapló-riasztásokat](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts). Előfordulhat például, hogy nyomon szeretné követni az előfizetések összes felügyeleti műveletét, vagy értesítést kap, ha egy adott erőforráscsoport valamelyik virtuális gépe törölve van. Ha riasztásokat hoz létre, azok a felhasználók által az ügyfél saját bérlője által végrehajtott műveleteket, valamint a bérlők felügyeletét is magukban foglalják.

További információ: [műveletnapló riasztások létrehozása és kezelése](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

## <a name="create-log-queries"></a>Naplóbeli lekérdezések létrehozása

Létrehozhat lekérdezéseket a naplózott tevékenység elemzéséhez, vagy adott elemekre koncentrálhat. Előfordulhat például, hogy egy naplózáshoz jelentést kell készítenie az előfizetésen végrehajtott összes felügyeleti szintű műveletről. Létrehozhat egy lekérdezést, amely csak ezeket a műveleteket szűri, és az eredményeket felhasználó, dátum vagy más érték szerint rendezi.

További információ: [Azure monitorban található naplók áttekintése](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitorról](https://docs.microsoft.com/azure/azure-monitor/).
- Megtudhatja, hogyan [tekintheti meg és kezelheti](view-manage-service-providers.md) a szolgáltatói ajánlatokat a Azure Portalban.