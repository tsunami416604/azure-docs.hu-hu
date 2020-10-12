---
title: Szolgáltatói tevékenység megtekintése
description: Az ügyfelek megtekinthetik a naplózott tevékenységeket a szolgáltatók által az Azure-beli delegált erőforrás-kezelésen keresztül végrehajtott műveletek megtekintéséhez.
ms.date: 07/07/2020
ms.topic: how-to
ms.openlocfilehash: 0c92fc9b45d17e37fb3721d9cf087c5e7a62f6d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86131467"
---
# <a name="view-service-provider-activity"></a>Szolgáltatói tevékenység megtekintése

Azok az ügyfelek, akik az [Azure világítótoronyhoz](../overview.md) delegált előfizetésekkel rendelkeznek, [megtekinthetik az Azure-tevékenység naplójának](../../azure-monitor/platform/platform-logs-overview.md) adatait az összes végrehajtott művelet megtekintéséhez. Ez teljes körű láthatóságot biztosít az ügyfeleknek a szolgáltatók számára az Azure-beli [delegált erőforrás-kezelésen](../concepts/azure-delegated-resource-management.md)keresztül, valamint az ügyfél saját Azure Active Directory (Azure ad) bérlője által végzett műveletek végrehajtásával.

> [!TIP]
> Egy Azure Policy beépített szabályzat-definíciót is biztosítunk a hatókörök delegálásának naplózására egy felügyeleti bérlő számára. További információ: [a delegálások naplózása a környezetben](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Tevékenységnapló adatainak megtekintése

A [tevékenység naplóját](../../azure-monitor/platform/activity-log.md#view-the-activity-log) a Azure Portal **figyelő** menüjéből tekintheti meg. Ha az eredményeket egy adott előfizetésre szeretné korlátozni, használja a szűrőket egy adott előfizetés kiválasztásához. [A tevékenységek naplózási eseményeit programozott módon is megtekintheti és lekérheti](../../azure-monitor/platform/activity-log.md#view-the-activity-log) .

> [!NOTE]
> A szolgáltató bérlője által biztosított felhasználók megtekinthetik a tevékenység naplójának eredményét egy ügyfél-bérlőben lévő delegált előfizetésre vonatkozóan, ha az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört (vagy egy másik beépített szerepkört is tartalmaznak, amely olvasói hozzáférést is tartalmaz), amikor az előfizetés bekerült az Azure Lighthouse szolgáltatásba.

A tevékenység naplójában látni fogja a művelet nevét és állapotát, valamint a végrehajtás dátumát és időpontját. Az oszlop **által kezdeményezett esemény** azt jeleníti meg, hogy melyik felhasználó végezte el a műveletet, függetlenül attól, hogy a szolgáltató bérlője az Azure világítótoronyon vagy az ügyfél saját bérlője által elindított felhasználó. Vegye figyelembe, hogy a felhasználó neve jelenik meg, nem pedig a bérlő vagy a felhasználó által az előfizetéshez hozzárendelt szerepkör.

A naplózott tevékenység a Azure Portal az elmúlt 90 napban érhető el. Az adatok 90 napnál hosszabb ideig történő tárolásával kapcsolatban tekintse meg [Az Azure-beli tevékenység-naplók gyűjtése és elemzése log Analytics munkaterületen](../../azure-monitor/platform/activity-log.md)című témakört.

> [!NOTE]
> A szolgáltató felhasználói a tevékenység naplójában jelennek meg, de a felhasználók és a hozzájuk tartozó szerepkör-hozzárendelések nem jelennek meg **Access Control (iam)** vagy a szerepkör-hozzárendelési adatok API-kon keresztüli lekérdezése során.

## <a name="set-alerts-for-critical-operations"></a>Riasztások beállítása kritikus műveletekhez

Annak érdekében, hogy a szolgáltatók (vagy a saját bérlők felhasználói) által végzett kritikus műveletekkel tisztában legyenek, javasoljuk, hogy hozzon létre [műveletnapló-riasztásokat](../../azure-monitor/platform/activity-log-alerts.md). Előfordulhat például, hogy nyomon szeretné követni az előfizetések összes felügyeleti műveletét, vagy értesítést kap, ha egy adott erőforráscsoport valamelyik virtuális gépe törölve van. Ha riasztásokat hoz létre, azok magukban foglalják a felhasználók által az ügyfél saját bérlője által végrehajtott műveleteket, valamint a bérlők felügyeletét is.

További információ: [műveletnapló riasztások létrehozása és kezelése](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Naplóbeli lekérdezések létrehozása

Létrehozhat lekérdezéseket a naplózott tevékenység elemzéséhez, vagy adott elemekre koncentrálhat. Előfordulhat például, hogy egy naplózáshoz jelentést kell készítenie az előfizetésen végrehajtott összes felügyeleti szintű műveletről. Létrehozhat egy lekérdezést, amely csak ezeket a műveleteket szűri, és az eredményeket felhasználó, dátum vagy más érték szerint rendezi.

További információ: [Azure monitorban található naplók áttekintése](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>További lépések

- További információ a [Azure monitorról](../../azure-monitor/index.yml).
- Megtudhatja, hogyan [tekintheti meg és kezelheti](view-manage-service-providers.md) a szolgáltatói ajánlatokat a Azure Portalban.
