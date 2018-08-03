---
title: Klasszikus tevékenységnapló-riasztások létrehozása
description: Értesítést küldünk az SMS, webhook és e-mailek bizonyos események történnek a tevékenységnaplóban.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/18/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 120fd3552ad36b3d19179f39ca95ce2b3ee2c2e6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426618"
---
# <a name="create-activity-log-alerts-classic"></a>Tevékenységnapló-riasztások (klasszikus) létrehozása

## <a name="overview"></a>Áttekintés
A tevékenységnapló-riasztások aktiválása, ha egy új tevékenységnapló eseményéhez akkor fordul elő a riasztást a megadott feltételeknek megfelelő riasztásokat is. Azure-erőforrások, így azok az Azure Resource Manager-sablon segítségével hozható létre. Akkor is képes lehet létrehozott, frissített vagy törölt az Azure Portalon. Ez a cikk bemutatja a tevékenységnapló-riasztások mögött. Majd bemutatja, hogyan állítsa be a riasztást a tevékenységnapló-események az Azure portal használatával.

> [!NOTE]

>  Az új [riasztások](monitoring-overview-unified-alerts.md) élmény váltotta fel ezt az eljárást. Ez a cikk a korábbi élmény biztosítunk hivatkozásként van listázva. [További információk](monitoring-activity-log-alerts-new-experience.md).

Általában akkor tevékenységnapló-riasztások létrehozása az értesítések fogadása során:

* Adott változások történnek az erőforrások az Azure-előfizetésben, az adott erőforrás-csoportok vagy az erőforrások gyakran hatóköre. Például érdemes értesíti, ha bármelyik virtuális gépre myProductionResourceGroup törlődik. Vagy előfordulhat, hogy szeretne értesítést kapjon, ha egyetlen új szerepkör hozzá van rendelve egy felhasználó az előfizetésében.
* A service health esemény következik be. A Szolgáltatásállapot-események értesítési incidensek és a karbantartási események, amelyek érvényesek az előfizetése tartalmazza.

Mindkét esetben a tevékenységnapló-riasztás csak az az előfizetés, amelyben a riasztás létrejött eseményeket figyeli.

Egy tevékenységnapló-riasztás alapján a JSON-objektumokat egy tevékenységnapló eseményéhez szereplő összes legfelső szintű tulajdonsággal is beállíthatja. A portálon azonban a leggyakrabban használt beállításai láthatók:

- **Kategória**: rendszergazda, a Service Health, automatikus méretezés és javaslat. További információkért lásd: [áttekintése az Azure-tevékenységnapló](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). A Szolgáltatásállapot-események kapcsolatos további információkért lásd: [szolgáltatási értesítésekhez tevékenységnapló-riasztások fogadása](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Erőforráscsoport**
- **Erőforrás**
- **Erőforrás típusa**
- **Název operace**: hozzáférés-vezérlés The Resource Manager Role-Based művelet neve.
- **Szint**: A súlyossági szintet az esemény (részletes, tájékoztatás, figyelmeztetés, hiba vagy kritikus).
- **Állapot**: az esemény, általában elindult, sikertelen vagy sikeres állapotát.
- **Esemény kezdeményezője**: más néven a "hívó." Az e-mail címet vagy az Azure Active Directory a műveletet végrehajtó felhasználó azonosítója.

> [!NOTE]
> Ha "rendszergazda" kategória, adjon meg legalább egy, a fenti feltételek közül a riasztásban. Nem hozható létre egy riasztást, amely minden alkalommal, amikor létrejön egy esemény a Tevékenységnaplókban aktiválja.

Tevékenységnapló-riasztás aktiválódik, ha a műveletek vagy értesítések műveletcsoportot használ. Műveletcsoport egy újrafelhasználható értesítési címzett e-mail-címeket, például a webhook URL-címek vagy SMS ügyfélszolgálatának telefonszámai. A fogadók központosítása és az értesítési csatornák csoportban több riasztásokból lehet hivatkozni. A tevékenységnapló-riasztás határozza meg, amikor két lehetősége van. A következőket teheti:

* Használja meglévő műveletcsoport a tevékenységnapló-riasztás.
* Hozzon létre egy új műveletcsoportot.

További információ a műveletcsoportokról, lásd: [létrehozása és kezelése az Azure Portalon Műveletcsoportok](monitoring-action-groups.md).

Szolgáltatás állapotára vonatkozó értesítések kapcsolatos további információkért lásd: [tevékenységnapló-riasztások a szolgáltatás állapotára vonatkozó értesítések fogadásához](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-classic-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Riasztás létrehozása (klasszikus) egy tevékenységnapló eseményéhez az új műveletcsoport az Azure portal használatával
1. Az a [portál](https://portal.azure.com)válassza **figyelő**.

    ![A "Figyelés" szolgáltatás](./media/monitoring-activity-log-alerts/home-monitor.png)
1. Az a **tevékenységnapló** szakaszban jelölje be **riasztások (klasszikus)**.

    ![Az "Értesítések" lapon](./media/monitoring-activity-log-alerts/alerts-blades.png)
1. Válassza ki **tevékenységnapló-riasztás hozzáadása**, és töltse ki a mezőket.

1. Írjon be egy nevet a **tevékenységnapló-riasztás neve** mezőbe, majd válassza ki a **leírás**.

    ![A "Tevékenységnapló-riasztás hozzáadása" parancs](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

1. A **előfizetés** az aktuális előfizetéshez autofills mezőbe. Ebben az előfizetésben a műveletcsoport elmentett. A riasztási erőforrás ehhez az előfizetéshez van telepítve, és figyeli, a tevékenységnapló eseményeit.

    ![A "Tevékenységnapló-riasztás hozzáadása" párbeszédpanel](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

1. Válassza ki a **erőforráscsoport** , amelyben a riasztási erőforrás létrejön. Ez a nem az erőforráscsoport, a riasztás által figyelt. Ehelyett érdemes az erőforráscsoport, ahol a riasztás erőforrás megtalálható.

1. Kiválaszthat egy **eseménykategória** jelennek meg a további szűrők módosításához. Felügyeleti események, a szűrők a következők **erőforráscsoport**, **erőforrás**, **erőforrástípus**, **műveletnév**, **Szint**, **állapot**, és **esemény kezdeményezője**. Ezek az értékek azonosítják, mely eseményeket a riasztás figyelni kell.

    >[!NOTE]
    >A fenti feltételek legalább egyikének a riasztásban szereplő adjon meg. Nem hozható létre egy riasztást, amely minden alkalommal, amikor létrejön egy esemény a Tevékenységnaplókban aktiválja.
    >
    >

1. Adjon meg egy nevet a a **műveletcsoport neve** mezőbe, majd adjon meg egy nevet a a **rövid, nevet** mezőbe. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

1.  Adja meg a műveleteknek a listája, azáltal, hogy a művelet:

    a. **Név**: Adja meg a művelet nevét, alias vagy azonosítója.

    b. **Művelet típusa**: válassza ki az SMS, e-mailben vagy webhook.

    c. **Részletek**: a művelet típusa alapján, adja meg, egy telefonszám, e-mail címét vagy webhook URI-t.

1.  Válassza ki **OK** a riasztás létrehozásához.

A riasztás teljes propagálása, és ezután aktiválása néhány percet vesz igénybe. Amikor új események feltételeknek a riasztás elindítja.

További információkért lásd: [megismerni a webhook-sémát, használja a tevékenységnapló-riasztások](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>A műveletcsoport meghatározott ezeket a lépéseket az újrafelhasználható, az összes jövőbeli riasztásdefiníciók meglévő műveletcsoport.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Egy tevékenységnapló eseményéhez meglévő műveletcsoport a riasztás létrehozása az Azure portal használatával
1. Végezze el az 1 – 7 a tevékenységnapló-riasztás létrehozásához az előző szakaszban.

1. A **értesítés ezen keresztül**, jelölje be a **meglévő** műveleti csoport gombra. Válasszon egy meglévő művelet csoportot a listából.

1. Válassza ki **OK** a riasztás létrehozásához.

A riasztás teljes propagálása, és ezután aktiválása néhány percet vesz igénybe. Amikor új események feltételeknek a riasztás elindítja.

## <a name="manage-your-alerts"></a>A riasztások kezelése

Miután létrehozta a riasztást, értéke látható a riasztások szakasz az a Monitor panel. Válassza ki a kezelni kívánt riasztás:

* Szerkesztheti.
* Törölje azt.
* Letiltani vagy engedélyezni, ha szeretné ideiglenesen leállítani, vagy folytathatja a riasztás-mailjeire.

## <a name="next-steps"></a>További lépések
- Get- [riasztások áttekintése](monitoring-overview-alerts.md).
- Ismerje meg [értesítési sebességkorlátozással](monitoring-alerts-rate-limiting.md).
- Tekintse át a [tevékenység log riasztási webhookséma](monitoring-activity-log-alerts-webhook.md).
- Tudjon meg többet [Műveletcsoportok](monitoring-action-groups.md).  
- Ismerje meg [szolgáltatás állapotára vonatkozó értesítések](monitoring-service-notifications.md).
- Hozzon létre egy [figyelése az előfizetés összes automatikus skálázási motor műveletek tevékenységnapló-riasztás](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Hozzon létre egy [figyelése az előfizetés összes sikertelen automatikus skálázás méretezési-a/horizontális felskálázás műveletek tevékenységnapló-riasztás](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
