---
title: Tevékenység napló figyelmeztetések létrehozása, és kezelheti azokat az új riasztások (előzetes verzió) felületet használó Azure figyelőben |} Microsoft Docs
description: Ez a cikk tájékoztatást ad azokról a riasztások (előzetes verzió) lapon, az Azure-figyelő napló riasztások tevékenység létrehozása. Ez a cikk az új felhasználói felületet a szolgáltatáshoz tartozó adatokat.
author: JYOTHIRMAISURI
manager: vvithal
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: ''
ms.openlocfilehash: 740edfd158d56fcb224cf93d1720a2330779bfd6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-preview-experience"></a>Az új figyelmeztetések (előzetes verzió) használatával riasztások élmény tevékenységnapló létrehozása

Tevékenység napló riasztások jelezhetik az beszerzése aktiválni, ha egy új tevékenység napló esemény történik a riasztás megadott feltételeknek megfelelő riasztásokat.

Ezek a riasztások jelezhetik az Azure-erőforrások, az Azure Resource Manager-sablon használatával hozhatók létre. Akkor is is létrehozása, frissítése, vagy törölve az Azure portálon. Ez a cikk bemutatja a napló tevékenységriasztásokat mögött. Ezután bemutatja, hogyan használható az Azure-portálon tevékenység alkalmazásnapló-események az új felület használata a riasztás beállítása [(előzetes verzió) Azure riasztások](monitoring-overview-unified-alerts.md).

Általában létrehozhat tevékenység napló riasztásokat, ha értesítést szeretne kapni az Azure-előfizetéshez, gyakran hatókörű adott forrás-és erőforrás erőforrásainak adott változása esetén. Például érdemes értesíti, ha a virtuális gép (minta erőforráscsoportban) **myProductionResourceGroup** törölték, vagy előfordulhat, hogy szeretne értesítést kaphat, ha új szerepköröket a felhasználó az előfizetéshez vannak rendelve.

A JSON-objektumokat az tevékenység naplóesemény legfelső szintű tulajdonságnak sem alapján tevékenység napló riasztásokat lehet beállítani. Azonban a portál az alábbi szakaszok a leggyakrabban használt beállítások részletes jeleníti meg:

>[!NOTE]

> "Rendszergazda" kategória esetén meg kell adnia legalább egy a fenti feltételek közül a riasztásban. Nem hozható létre egy riasztást, amely minden alkalommal, amikor egy esemény jön létre a tevékenységi naplóit aktiválja.
>

Amikor egy tevékenység napló riasztás aktív, az egy műveletek vagy értesítések. Egy olyan újrafelhasználható értesítési fogadók, például az e-mail címeket, a webhook URL-címek vagy SMS telefonszámokat. A fogadók központosítása és az értesítési csatornák csoport több riasztás lehet hivatkozni. Ha a napló figyelmeztetés, két választási lehetősége van. A következőket teheti:

* A napló figyelmeztetés művelet meglévő csoport használata
* Hozzon létre egy új művelet.

Művelet csoportokkal kapcsolatos további tudnivalókért lásd: [létrehozása és kezelése az Azure portálon művelet csoportok](monitoring-action-groups.md).

A szolgáltatás állapotával kapcsolatos értesítésekre kapcsolatos további információkért lásd: [tevékenység napló értesítést a szolgáltatás állapotával kapcsolatos értesítésekre](monitoring-activity-log-alerts-on-service-notifications.md).


## <a name="whats-new-in-alerts-preview-for-activity-logs"></a>Újdonságok a riasztások Preview tevékenységi naplóit?

[Az Azure riasztások (előzetes verzió)](monitoring-overview-unified-alerts.md) most jobb felhasználói élményt biztosít a napló tevékenységriasztásokat. Az a [felhasználói élmény a riasztások fokozott](monitoring-overview-unified-alerts.md), most is:

- [Hozzon létre](#create-an-alert-rule-for-an-activity-log) és [kezelése](#view-and-manage-activity-log-alert-rules) tevékenységét naplózni riasztási szabályok **figyelő** > **riasztások (előzetes verzió)** panelen. További információ [tevékenységi naplóit](monitoring-overview-activity-logs.md).

- **A riasztások cél új beállítások**: új tevékenység napló riasztási szabály létrehozásakor mostantól kiválaszthatja a cél erőforráson vagy egy erőforráscsoport vagy egy előfizetést.


## <a name="create-an-alert-rule-for-an-activity-log"></a>Hozzon létre egy tevékenységnapló riasztási szabály

> [!NOTE]

>  A riasztási szabályok létrehozásához, ellenőrizze a következőket:

> - Előfizetés a hatókör nincs eltér az előfizetés ahol a riasztás jön létre.
- Feltételek kell szint/állapot/hívó / erőforráscsoportot / erőforrás-azonosító erőforrás típusa vagy eseménykategória, amelyen a riasztás konfigurálva van.
- Nincs "anyOf" feltétel vagy a riasztási konfigurációban JSON beágyazott feltételek (alapvetően csak egy allOf esetén megengedett nincs további allOf anyOf).


Kövesse az alábbi eljárást:

1. Azure-portálon, válassza ki a **figyelő** > **riasztások (előzetes verzió).**
2. Kattintson a **Új riasztási szabály** tetején a **riasztások (előzetes verzió)** ablak.

     ![Új riasztási szabályt](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     A **létrehozás szabály** ablak jelenik meg.

      ![Új riasztási szabály beállítások](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **Riasztási meghatározása feltétel alapján** adja meg a következő adatokat, és kattintson a **végzett**.

    - **Riasztási cél:** megtekintéséhez, és jelölje ki a cél az új riasztás, használja **előfizetés szűrés** / **erőforrástípus szűrés** , és válassza ki az erőforrás vagy erőforráscsoportban, mint a a lista jelenik meg.

    > [!NOTE]

    > kiválaszthatja, hogy egy erőforrás, erőforráscsoport vagy egy egész előfizetésre.

    **Riasztási célnézet minta**

     ![Válassza ki a cél](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - A **cél feltételek**, kattintson a **adja meg a feltételeket** válassza ki a jel típusú **tevékenységnapló**.

    - Válassza ki a jel jelenik meg a listából.

    A naplózási előzmények ütemterv és a cél jel megfelelő riasztási logikáját választhat:

    **Adja hozzá a feltételek képernyő**

    ![Adja meg a feltételeket](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Előzmények idő**: az utolsó 6/12 vagy 24 óra, a múlt héten keresztül.

    **Riasztási logika**:

     - **Eseményszint**-az esemény súlyosságának. **Részletes, a tájékoztató, figyelmeztetés, hiba**, vagy **kritikus**.
     - **Állapot**: az esemény állapotát. **Sikerült elindítani,**, vagy **sikeres**.
     - **Az esemény által kezdeményezett**: más néven a hívó; Az e-mail cím vagy a műveletet a felhasználó Azure Active Directory azonosítója.

        **Minta jel graph alkalmazott riasztási logikával** :

        ![ kiválasztott feltételekhez](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. A **riasztási szabályok adatainak**, adja meg a következő adatokat:

    - **Riasztási szabály neve** – az Új riasztási szabály neve
    - **Leírás** – az Új riasztási szabály leírása
    - **Riasztás mentése erőforráscsoporthoz** – válassza ki az erőforráscsoportot, ahol az új szabály menteni szeretné.

5. A **művelet csoport**, a legördülő menüből, adja meg a műveletet, amelyet szeretne hozzárendelni az Új riasztási szabály. Másik lehetőségként [hozzon létre egy új művelet csoportot](monitoring-action-groups.md) és rendelje hozzá az új szabályt. Új csoport létrehozásához kattintson a **+ új csoport**.

6. A szabályok létrehozása után engedélyezéséhez kattintson **Igen** a **engedélyezése a szabály létrehozásakor** lehetőséget.
7. Kattintson a **riasztási szabály létrehozása**.

    Az Új riasztási szabály a műveletnapló jön létre, és egy megerősítő üzenet jelenik meg a bal felső sarkában az ablak.

    ![ riasztási szabály létrehozása](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    Engedélyezése, letiltása, szerkesztheti és törölheti a szabályt. [További](#view-and-manage-activity-log-alert-rules) tevékenység napló szabályok kezelése.

## <a name="view-and-manage-activity-log-alert-rules"></a>Megtekintése és tevékenység napló riasztási szabályok kezelése

1. Azure-portálon, kattintson a **figyelő** > **riasztások (előzetes verzió)** kattintson **szabályok kezelése** a az ablak bal felső.

    ![ riasztási szabályok kezelése](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    Elérhető szabályok listája jelenik meg.

2. Keresse meg a tevékenység napló szabály módosítása.

    ![ tevékenység napló riasztási szabályok keresése](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    A rendelkezésre álló szűrők - **előfizetés**, **erőforráscsoport**, vagy **erőforrás** a szerkeszteni kívánt tevékenység szabály található.

    > [!NOTE]

    > Csak akkor szerkeszthető **leírás** , **céloz feltételek** és **művelet csoportok**.

3.  Jelölje ki a szabályt, és kattintson duplán a szabály beállításainak módosítása. Végezze el a szükséges módosításokat, és kattintson a **mentése**.

    ![ riasztási szabályok kezelése](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  Letiltása, engedélyezése vagy szabály törlése. Válassza ki a megfelelő beállítást az ablak tetején kijelölése a szabály a 2. lépés után.


## <a name="next-steps"></a>További lépések

- [Napló tevékenységriasztásokat archiválása](monitoring-archive-activity-log.md)
- [Az adatfolyam tevékenységi naplóit Event hubs](monitoring-stream-activity-logs-event-hubs.md)
- [Tevékenységi naplóit áttelepítése](monitoring-migrate-management-alerts.md)
