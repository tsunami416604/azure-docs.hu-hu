---
title: "Napló riasztások tevékenység létrehozása |} Microsoft Docs"
description: "Értesítést SMS, webhook és e-mailt a műveletnaplóban bizonyos események megtörténtekor."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: johnkem
ms.openlocfilehash: 3885469ec0e1fcc31386dd0ad7fe6cb5d03ab28e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-activity-log-alerts"></a>Napló riasztások tevékenység létrehozása

## <a name="overview"></a>Áttekintés
Tevékenység napló riasztások az éppen aktiválása, ha egy új tevékenység napló esemény történik a riasztás megadott feltételeknek megfelelő riasztásokat. Azure-erőforrások, így azok hozhat létre Azure Resource Manager-sablonnal. Akkor is is létrehozása, frissítése, vagy törölve az Azure portálon. Ez a cikk bemutatja a napló tevékenységriasztásokat mögött. Azt ezután bemutatja, hogyan használható az Azure-portálon naplózási eseményeket a riasztás beállításához.

Általában létrehozhat tevékenység napló riasztásokat, értesítéseket során:

* Erőforrások az Azure-előfizetése, gyakran hatókörű adott forrás-és erőforrások adott változás. Például előfordulhat, hogy kívánt értesíti, ha bármely myProductionResourceGroup a virtuális gép törlődik. Vagy előfordulhat, hogy szeretne értesítést kapni, ha új szerepköröket a felhasználó az előfizetéshez vannak rendelve.
* Egy szolgáltatás állapotát az esemény akkor következik be. Szolgáltatás állapotával kapcsolatos események tartalmazzák az incidensek és karbantartási események erőforrást az előfizetésében vonatkozó értesítés.

Mindkét esetben egy figyelmeztetés a napló csak az eseményeket az előfizetés, amelyben a riasztást hoz létre figyeli.

A JSON-objektumokat az tevékenység naplóesemény legfelső szintű tulajdonságnak sem alapján tevékenység napló riasztásokat lehet beállítani. A portál azonban a leggyakoribb beállítások láthatók:

- **Kategória**: felügyeleti, a szolgáltatás állapotát, automatikus skálázás és javaslat. További információkért lásd: [az Azure tevékenységnapló áttekintése](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). A szolgáltatás állapotával kapcsolatos események kapcsolatos további információkért lásd: [szolgáltatáshoz értesítést tevékenység napló riasztásokat fogadhat](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Erőforráscsoport**
- **Erőforrás**
- **Erőforrástípus**
- **A művelet neve**: hozzáférés-vezérlés The Resource Manager Role-Based művelet neve.
- **Szint**: A súlyossági szintet az esemény (részletes, tájékoztatás, figyelmeztetés, hiba vagy kritikus).
- **Állapot**: az esemény, általában elindult, sikertelen vagy sikeres állapotát.
- **Az esemény által kezdeményezett**: más néven a "hívó." Az e-mail cím vagy a műveletet a felhasználó Azure Active Directory azonosítója.

>[!NOTE]
>Meg kell adnia a fenti feltételek közül legalább kettő kattintson a riasztásra, egy a kategória alatt. Nem hozható létre egy riasztást, amely minden alkalommal, amikor egy esemény jön létre a tevékenységi naplóit aktiválja.
>
>

Amikor egy tevékenység napló riasztás aktív, az egy műveletek vagy értesítések. Egy olyan újrafelhasználható értesítési fogadók, például az e-mail címeket, a webhook URL-címek vagy SMS telefonszámokat. A fogadók központosítása és az értesítési csatornák csoport több riasztás lehet hivatkozni. Ha a napló figyelmeztetés, két választási lehetősége van. A következőket teheti:

* A napló figyelmeztetés művelet meglévő csoport használata 
* Hozzon létre egy új művelet. 

Művelet csoportokkal kapcsolatos további tudnivalókért lásd: [létrehozása és kezelése az Azure portálon művelet csoportok](monitoring-action-groups.md).

A szolgáltatás állapotával kapcsolatos értesítésekre kapcsolatos további információkért lásd: [tevékenység napló értesítést a szolgáltatás állapotával kapcsolatos értesítésekre](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>A művelet új csoport tevékenység napló esemény a riasztás létrehozása az Azure portál használatával
1. Az a [portal](https://portal.azure.com), jelölje be **figyelő**.

    ![A "Figyelés" szolgáltatás](./media/monitoring-activity-log-alerts/home-monitor.png)
2. Az a **tevékenységnapló** szakaszban jelölje be **riasztások**.

    ![Az "Értesítések" lapon](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. Válassza ki **Hozzáadás figyelmeztetés a napló**, és töltse ki a mezőket.

4. Írjon be egy nevet a **tevékenység napló riasztás neve** mezőbe, majd válassza ki a **leírás**.

    ![A "Hozzáadás napló figyelmeztetés" parancs](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. A **előfizetés** a jelenlegi előfizetés autofills mezőben. Ez az előfizetés, amelyben a művelet csoport mentett lesz. A riasztási erőforrás ebbe az előfizetésbe telepítve van, és figyeli a naplózási eseményeket belőle.

    ![A "Napló figyelmeztetés hozzáadása" párbeszédpanel](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. Válassza ki a **erőforráscsoport** a riasztási erőforrás létrehozása. Ez nem az erőforráscsoport, amelyet a riasztást. Ehelyett az erőforráscsoportot, ahol a riasztás erőforrás.

7. Ha kijelöl egy **eseménykategória** módosítása a további szűrőket látható. A felügyeleti események, a szűrők a következők **erőforráscsoport**, **erőforrás**, **erőforrástípus**, **műveletnév**, **Szint**, **állapot**, és **esemény által kezdeményezett**. Ezeket az értékeket a riasztás célszerű figyelemmel kísérni események azonosítása.

    >[!NOTE]
    >Meg kell adnia legalább egy a fenti feltételek közül a riasztásban. Nem hozható létre egy riasztást, amely minden alkalommal, amikor egy esemény jön létre a tevékenységi naplóit aktiválja.
    >
    >

8. Adjon meg egy nevet a a **művelet csoportnév** mezőbe, majd írjon be egy nevet a **rövid név** mezőbe. A rövid nevét használja a teljes műveletet csoport neve helyett amikor ez a csoport értesítések küldése.

9.  A művelet megadásával határozza meg azon műveletek listáját:

    a. **Név**: Adja meg a művelet neve, az alias vagy a azonosítója.

    b. **Művelet típusa**: válassza ki az SMS, e-mailek vagy webhook.

    c. **Részletek**: a művelet típusa alapján, adjon meg egy telefonszám, e-mail címét vagy webhook URI.

10. Válassza ki **OK** a riasztás létrehozása.

A riasztás teljesen propagálása és válik aktívvá néhány percet vesz igénybe. Amikor új események feltételeknek a riasztás elindítja.

További információkért lásd: [megérteni a webhook séma napló tevékenységriasztásokat használt](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>A művelet csoportnak, az alábbi lépéseket az használható fel újra egy meglévő művelet csoportként jövőbeli riasztási-definíciók.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Az Azure portál segítségével hozzon létre egy riasztást az tevékenység naplóesemény meglévő művelet csoportok
1. Végezze el az 1 – 7 a napló figyelmeztetés létrehozásához az előző szakaszban.

2. A **keresztül értesíti**, jelölje be a **meglévő** művelet csoport gombra. Válasszon egy meglévő művelet csoportot a listából.

3. Válassza ki **OK** a riasztás létrehozása.

A riasztás teljesen propagálása és válik aktívvá néhány percet vesz igénybe. Amikor új események feltételeknek a riasztás elindítja.

## <a name="manage-your-alerts"></a>A riasztások kezelése

Riasztás létrehozása után már látható a riasztások szakaszban, a figyelő panelről. Jelölje ki a kezelni kívánt riasztást:

* Szerkesztheti.
* Törölje a parancsikont.
* Tiltsa le, vagy engedélyezheti, ha azt szeretné, ideiglenesen leállítani, vagy folytassa a riasztás értesítések fogadásának.

## <a name="next-steps"></a>Következő lépések
- Első egy [riasztások áttekintése](monitoring-overview-alerts.md).
- További tudnivalók [értesítési sebessége korlátozza az](monitoring-alerts-rate-limiting.md).
- Tekintse át a [műveletnapló riasztási webhook séma](monitoring-activity-log-alerts-webhook.md).
- További információ [művelet csoportok](monitoring-action-groups.md).  
- További tudnivalók [szolgáltatás állapotával kapcsolatos értesítésekre](monitoring-service-notifications.md).
- Hozzon létre egy [napló figyelmeztetés a figyelheti az előfizetés minden automatikus skálázási motor műveletek](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Hozzon létre egy [napló figyelmeztetés a figyelheti az előfizetés összes sikertelen automatikus skálázás méretezési-a/kibővített művelete](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
