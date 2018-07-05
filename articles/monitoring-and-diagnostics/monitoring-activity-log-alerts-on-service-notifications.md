---
title: Az Azure szolgáltatási értesítésekhez tevékenységnapló-riasztások fogadása
description: Értesítés SMS, e-mailben vagy webhook Azure-szolgáltatás esetén.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/09/2018
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 1e79fee75b2361dd7da8c46c175a5a6532089ad6
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437239"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Tevékenységnapló-riasztások létrehozása szolgáltatási értesítésekhez
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan szolgáltatás állapotára vonatkozó értesítések a tevékenységnapló-riasztások beállítása az Azure portal használatával.  

Riasztást is küld, ha az Azure service health értesítéseket küld az Azure-előfizetéshez. A riasztás alapján konfigurálhatja:

- A szolgáltatás állapotával kapcsolatos értesítés (szolgáltatási problémák, tervezett karbantartás, állapot-tanácsadási információk) osztályát.
- Az érintett előfizetés.
- Az érintett szolgáltatásokat.
- Az érintett régió(k).

> [!NOTE]
> Szolgáltatás állapotára vonatkozó értesítések nem küld riasztást erőforrásról hálózatállapot-események.

Is lehet konfigurálni kell a riasztást küldő:

- Válasszon ki egy meglévő művelet.
- Hozzon létre egy új műveletcsoportot (amely a jövőbeni riasztásokhoz használható).

A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](monitoring-action-groups.md) szóló cikkben talál.

Health értesítési szolgáltatásriasztások konfigurálása Azure Resource Manager-sablonok használatával kapcsolatos információkért lásd: [Resource Manager-sablonok](monitoring-create-activity-log-alerts-with-resource-manager-template.md).

## <a name="create-an-alert-on-a-service-health-notification-for-a-new-action-group-by-using-the-azure-portal"></a>Az Azure portal segítségével hozzon létre egy riasztást a szolgáltatás állapotával kapcsolatos értesítés az új műveletcsoport
1. Az a [portál](https://portal.azure.com)válassza **Service Health**.

    ![A "Service" szolgáltatása](./media/monitoring-activity-log-alerts-on-service-notifications/home-servicehealth.png)

2. Az a **riasztások** szakaszban jelölje be **állapotriasztások**.

    ![A "Állapotriasztások" lap](./media/monitoring-activity-log-alerts-on-service-notifications/alerts-blades-sh.png)

3. Válassza ki **szolgáltatásállapot-riasztás létrehozása** , és töltse ki a mezőket.

    ![A "Létrehozás szolgáltatásállapot-riasztás" parancs](./media/monitoring-activity-log-alerts-on-service-notifications/service-health-alert.png)

4. Válassza ki a **előfizetés**, **szolgáltatások**, és **régiók** szeretne riasztást létrehozni.

    ![A "Tevékenységnapló-riasztás hozzáadása" párbeszédpanel](./media/monitoring-activity-log-alerts-on-service-notifications/activity-log-alert-new-ux.png)

> [!NOTE]
> Ebben az előfizetésben a tevékenységnapló-riasztás mentéséhez használatos. A riasztási erőforrás ehhez az előfizetéshez van telepítve, és a tevékenységnaplóban eseményeket figyeli.

5. Válassza ki a **eseménytípusok** szeretne riasztást létrehozni: *szolgáltatási probléma*, *tervezett karbantartás*, és *állapottanácsadási információk* 

6. Írja be a riasztás részleteinek megadása egy **riasztásiszabály-névnek** és **leírás**.

7. Válassza ki a **erőforráscsoport** hol szeretné menteni a riasztást.

8. Hozzon létre egy új műveletcsoportot kiválasztásával **új műveletcsoport**. Adjon meg egy nevet a a **műveletcsoport neve** mezőbe, majd adjon meg egy nevet a a **rövid, nevet** mezőbe. Ez a riasztás aktiválódásakor küldött értesítések hivatkozik rövid nevét.

    ![Új műveletcsoport létrehozása](./media/monitoring-activity-log-alerts-on-service-notifications/action-group-creation.png)

9. Adja meg a fogadók listáját azáltal, hogy a fogadó:

    a. **Név**: Adja meg a fogadó nevét, alias vagy azonosítója.

    b. **Művelet típusa**: válassza ki az SMS, e-mailt, webhookot, az Azure app és több.

    c. **Részletek**: a választott művelet típusa alapján, adja meg, egy telefonszám, e-mail címét, webhook URI-t, stb.

10. Válassza ki **OK** a műveletcsoport létrehozásához, majd **riasztási szabály létrehozása** a riasztás végrehajtásához.

Néhány percen belül a riasztás aktív, és megkezdi aktiválása a létrehozása során megadott feltételek alapján.

Ismerje meg, hogyan [konfigurálása webhook-értesítésekkel meglévő probléma felügyeleti rendszerek](../service-health/service-health-alert-webhook-guide.md). A tevékenységnapló-riasztások a webhook sémáról kapcsolatos tudnivalókat lásd: [Webhookok az Azure-tevékenységi naplóriasztások](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>A műveletcsoport meghatározott ezeket a lépéseket az újrafelhasználható, az összes jövőbeli riasztásdefiníciók meglévő műveletcsoport.
>
>

## <a name="create-an-alert-on-a-service-health-notification-for-an-existing-action-group-by-using-the-azure-portal"></a>Riasztás létrehozása a meglévő műveletcsoport számára a szolgáltatás állapotával kapcsolatos értesítés az Azure portal használatával

1. Végezze el az 1 – 7 hozhat létre a szolgáltatás állapotával kapcsolatos értesítés az előző szakaszban. 

2. Alatt **definiálása műveletcsoport**, kattintson a **válassza műveletcsoport** gombra. Válassza ki a megfelelő műveletet.

3. Válassza ki **Hozzáadás** hozzáadása a műveletcsoport, majd **riasztási szabály létrehozása** a riasztás végrehajtásához.

Néhány percen belül a riasztás aktív, és megkezdi aktiválása a létrehozása során megadott feltételek alapján.

## <a name="manage-your-alerts"></a>A riasztások kezelése

Miután létrehozta a riasztást, legyen látható a **riasztások** szakaszában **figyelő**. Válassza ki a kezelni kívánt riasztás:

* Szerkesztheti.
* Törölje azt.
* Letiltani vagy engedélyezni, ha szeretné ideiglenesen leállítani, vagy folytathatja a riasztás-mailjeire.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [konfigurálása webhook-értesítésekkel meglévő probléma felügyeleti rendszerek](../service-health/service-health-alert-webhook-guide.md).
- Ismerje meg [szolgáltatás állapotára vonatkozó értesítések](monitoring-service-notifications.md).
- Ismerje meg [értesítési sebességkorlátozással](monitoring-alerts-rate-limiting.md).
- Tekintse át a [tevékenység log riasztási webhookséma](monitoring-activity-log-alerts-webhook.md).
- Get- [tevékenységnapló-riasztások áttekintése](monitoring-overview-alerts.md), és a riasztások fogadása. 
- Tudjon meg többet [Műveletcsoportok](monitoring-action-groups.md).
