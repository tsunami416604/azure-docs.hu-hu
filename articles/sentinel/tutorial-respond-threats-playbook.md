---
title: 'Oktatóanyag: forgatókönyv futtatása az Azure Sentinelben'
description: 'Oktatóanyag: Ez a cikk bemutatja, hogyan futtathat forgatókönyveket az Azure Sentinelben.'
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2019
ms.author: rkarlin
ms.openlocfilehash: 7e26e901fdee8cac79cfb55aa0999487e87b8a78
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113922"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Oktatóanyag: automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure Sentinelben



Ez az oktatóanyag segítséget nyújt a biztonsági forgatókönyvek az Azure Sentinelben való használatához az Azure Sentinel által észlelt biztonsági problémákra vonatkozó automatizált veszélyforrási válaszok beállításához.


> [!div class="checklist"]
> * Forgatókönyvek ismertetése
> * Forgatókönyv létrehozása
> * Forgatókönyv futtatása
> * Fenyegetési válaszok automatizálása


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Mi az az Azure Sentinel biztonsági forgatókönyve?

A biztonsági forgatókönyvek olyan eljárások gyűjteményei, amelyek az Azure Sentinelből egy riasztásra reagálva futtathatók. A biztonsági forgatókönyvek segítségével automatizálhatja és összehangolhatja a válaszát, és az adott riasztások aktiválásakor manuálisan is futtatható, vagy beállíthatja, hogy automatikusan fusson. Az Azure Sentinelben a biztonsági forgatókönyvek a [Azure Logic Appson](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)alapulnak, ami azt jelenti, hogy a Logic apps összes energiagazdálkodási, testreszabható és beépített sablonja elérhető. Az egyes forgatókönyvek a kiválasztott előfizetéshez jönnek létre, de ha megtekinti a forgatókönyvek lapot, megjelenik az összes forgatókönyv a kijelölt előfizetések között.

> [!NOTE]
> A forgatókönyvek kihasználják Azure Logic Apps, ezért a díjak érvényesek. További részletekért látogasson el az [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) árképzését ismertető oldalra.

Ha például a hálózati erőforrásokhoz hozzáférő rosszindulatú támadók miatt aggódik, beállíthat egy riasztást, amely a hálózathoz hozzáférő kártékony IP-címeket keresi. Ezután létrehozhat egy forgatókönyvet, amely a következő műveleteket végzi el:
1. Ha a riasztás aktiválva van, nyisson meg egy jegyet a ServiceNow-ben vagy más IT-szolgáltatói rendszeren.
2. Küldjön üzenetet a biztonsági operatív csatornának a Microsoft Teams vagy a Slack szolgáltatásban, és győződjön meg arról, hogy a biztonsági elemzők tisztában vannak az incidenssel.
3. Küldje el a riasztásban található összes információt a vezető hálózati rendszergazdai és biztonsági rendszergazdának. Az e-mail-üzenetben két felhasználói gomb is **blokkolható** vagy **figyelmen kívül hagyható**.
4. A forgatókönyv továbbra is fut a rendszergazdáktól kapott válasz után.
5. Ha a rendszergazdák a **Letiltás**lehetőséget választják, az IP-cím blokkolva lesz a tűzfalon, és a felhasználó le van tiltva az Azure ad-ben.
6. Ha a rendszergazdák úgy döntenek, hogy **figyelmen kívül hagyják**, a riasztás bezárult az Azure sentinelben, és az incidens bezárult a ServiceNow.

A biztonsági forgatókönyvek manuálisan vagy automatikusan is futtathatók. A manuális Futtatás azt jelenti, hogy ha riasztást kap, dönthet úgy, hogy a kiválasztott riasztásra adott válaszként futtat egy igény szerinti forgatókönyvet. Az automatikus futtatás azt jelenti, hogy a korrelációs szabály létrehozásakor a rendszer automatikusan futtat egy vagy több forgatókönyvet a riasztás indításakor.


## <a name="create-a-security-playbook"></a>Biztonsági forgatókönyv létrehozása

Kövesse az alábbi lépéseket egy új biztonsági forgatókönyv létrehozásához az Azure Sentinelben:

1. Nyissa meg az **Azure Sentinel** irányítópultját.
2. A **kezelés** **területen válassza a**forgatókönyvek lehetőséget.

   ![Logikai alkalmazás](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Az **Azure Sentinel-forgatókönyvek (előzetes verzió)** lapon kattintson a **Hozzáadás** gombra.

   ![Logikai alkalmazás létrehozása](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. A **logikai alkalmazás létrehozása** lapon írja be a kért adatokat az új logikai alkalmazás létrehozásához, majd kattintson a **Létrehozás**gombra. 

5. A [**Logic app Designerben**](../logic-apps/logic-apps-overview.md)válassza ki a használni kívánt sablont. Ha olyan sablont választ, amelynek a hitelesítő adatokat kell megadnia, meg kell adnia őket. Alternatív megoldásként létrehozhat egy új, üres forgatókönyvet is. Válassza az **üres logikai alkalmazás**lehetőséget. 

   ![Logikaialkalmazás-tervező](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. A Logic app Designerben hozhat létre új vagy szerkeszthető sablont. További információ a forgatókönyvek [Logic apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md)-vel való létrehozásáról.

7. Ha üres forgatókönyvet hoz létre, a Keresés az **összes összekötő és eseményindító** mezőben írja be az *Azure Sentinel*kifejezést, és válassza ki, **hogy mikor aktiválódik az Azure Sentinel-riasztásra adott válasz**. <br>A létrehozást követően az új forgatókönyv **megjelenik a forgatókönyvek listájában** . Ha nem jelenik meg, kattintson a **frissítés**gombra.

1. Az **entitások beolvasása** függvények segítségével lekérheti az érintett entitásokat az **entitások** listájában, például a FIÓKOKAT, az IP-címeket és a gazdagépeket. Ez lehetővé teszi, hogy műveleteket futtasson bizonyos entitásokon.

7. Most meghatározhatja, mi történjen a forgatókönyv aktiválásakor. Hozzáadhat egy műveletet, egy logikai feltételt, a váltási eset feltételeit vagy a hurkokat.

   ![Logikaialkalmazás-tervező](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Biztonsági forgatókönyv futtatása

Igény szerint futtathat forgatókönyveket.

Igény szerinti forgatókönyv futtatása:

1. Az **incidensek** lapon válasszon ki egy incidenst, és kattintson a **teljes részletek megtekintése**elemre.

2. A **riasztások** lapon kattintson arra a riasztásra, amelyen futtatni szeretné a forgatókönyvét, és görgessen végig a jobb oldalon, majd kattintson a forgatókönyvek **megtekintése** lehetőségre, és válassza ki az előfizetésben elérhető forgatókönyvek listájából **futtatandó** forgatókönyvet. 



## <a name="automate-threat-responses"></a>Fenyegetési válaszok automatizálása

SIEM/SOC-csapatok rendszeres időközönként biztonsági riasztásokkal is elárasztható. A generált riasztások mennyisége annyira nagy, hogy a rendelkezésre álló biztonsági rendszergazdák túlterheltek. Ez az eredmény túl gyakran olyan helyzetekben, amikor sok riasztást nem lehet megvizsgálni, így a szervezet sebezhetővé válik a nem felmerülő támadásokkal szemben. 

A riasztások többsége – ha nem a legtöbb esetben – megfelel az ismétlődő mintázatoknak, amelyeket meghatározott és meghatározott szervizelési műveletek kezelhetnek. Az Azure Sentinel már lehetővé teszi a szervizelés megadását a forgatókönyvekben. Azt is megteheti, hogy valós idejű automatizálást állít be a forgatókönyv-definíció részeként, így lehetővé teszi, hogy a meghatározott biztonsági riasztásokra adott válasz teljes mértékben automatizálható legyen. A valós idejű automatizálással a válaszokkal rendelkező csapatok jelentősen csökkenthetik a számítási feladatokat azáltal, hogy teljes mértékben automatizálják a rendszeres válaszokat a riasztások ismétlődő típusaira, így többek között az egyedi riasztásokra koncentrálhat, elemezheti a mintákat, a veszélyforrások elleni vadászatot és egyebeket.

Válaszok automatizálása:

1. Válassza ki azt a riasztást, amelynek a válaszát automatizálni szeretné.
1. A **riasztási szabály szerkesztése** lap **valós idejű automatizálás**területén válassza ki azt az **aktivált** forgatókönyvet, amelyet futtatni szeretne a riasztási szabály egyeztetése során.
1. Kattintson a **Mentés** gombra.

   ![valós idejű automatizálás](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan futtathat egy forgatókönyvet az Azure Sentinelben. Folytassa a [fenyegetések proaktív módon történő vadászatát](hunting.md) az Azure Sentinel használatával.


