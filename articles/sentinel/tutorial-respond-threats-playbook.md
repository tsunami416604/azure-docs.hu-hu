---
title: 'Oktatóanyag: Forgatókönyv futtatása az Azure Sentinelben'
description: 'Oktatóanyag: Ez a cikk ismerteti, hogyan futtathatok egy forgatókönyvet az Azure Sentinelben.'
services: sentinel
documentationcenter: na
author: yelevin
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
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: f6adcb978dbe540d3bdd352089d4dde407d0fb4c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77585084"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Oktatóanyag: Automatikus fenyegetésre adott válaszok beállítása az Azure Sentinelben



Ez az oktatóanyag segít az Azure Sentinel biztonsági forgatókönyveinek használatával az Azure Sentinel által észlelt, biztonsággal kapcsolatos problémákra adott automatikus fenyegetési válaszok beállításához.


> [!div class="checklist"]
> * A forgatókönyvek megértése
> * Forgatókönyv létrehozása
> * Forgatókönyv futtatása
> * A fenyegetésekre adott válaszok automatizálása


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Mi az azure Sentinel biztonsági forgatókönyve?

A biztonsági forgatókönyv eljárások gyűjteménye, amelyek futtathatók az Azure Sentinel riasztásválaszként. A biztonsági forgatókönyv segítségével automatizálhatja és koordinálhatja a választ, és manuálisan futtatható, vagy beállíthatja, hogy automatikusan fusson, ha bizonyos riasztások aktiválódnak. Az Azure Sentinel biztonsági forgatókönyvei [az Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)szolgáltatáson alapulnak, ami azt jelenti, hogy a Logic Apps minden erejét, testreszabhatóságát és beépített sablonját megkapja. Minden forgatókönyv a kiválasztott előfizetéshez jön létre, de ha megnézed a Forgatókönyvek oldalt, látni fogod az összes forgatókönyvet a kiválasztott előfizetések között.

> [!NOTE]
> A játékkönyvek az Azure Logic Apps alkalmazást használják, ezért díjak at kell fizetniük. További részletekért látogasson el az [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) árképzését ismertető oldalra.

Ha például aggódik amiatt, hogy rosszindulatú támadók férnek hozzá a hálózati erőforrásokhoz, beállíthat egy riasztást, amely a hálózathoz hozzáférő rosszindulatú IP-címeket keresi. Ezután létrehozhat egy forgatókönyvet, amely a következőket teszi:
1. A riasztás aktiválásakor nyisson meg egy jegyet a ServiceNow vagy bármely más informatikai jegyrendszerben.
2. Küldjön üzenetet a Microsoft Teams vagy a Slack biztonsági műveleti csatornájának, hogy a biztonsági elemzők tisztában legyenek az esettel.
3. Küldje el a riasztásban szereplő összes információt a vezető hálózati rendszergazdának és biztonsági rendszergazdának. Az e-mail két felhasználói választógombot is **tartalmaz: Blokkolva** vagy **figyelmen kívül hagyva.**
4. A forgatókönyv továbbra is fut, miután a válasz érkezett az adminoktól.
5. Ha a rendszergazdák a **Blokkolás**lehetőséget választják, az IP-cím le van tiltva a tűzfalon, és a felhasználó le van tiltva az Azure AD-ben.
6. Ha a rendszergazdák a **Figyelmen kívül hagyása**lehetőséget választják, a riasztás az Azure Sentinelben van bezárva, és az incidens a ServiceNow-ban van bezárva.

A biztonsági forgatókönyvek manuálisan vagy automatikusan futtathatók. Ha manuálisan futtatja őket, azt jelenti, hogy amikor riasztást kap, beállíthatja, hogy a kiválasztott riasztásra adott válaszként igény szerint futtasson egy forgatókönyvet. Automatikusan futtatja azt, hogy a korrelációs szabály szerkesztése közben úgy állítja be, hogy automatikusan fusson egy vagy több forgatókönyvet, amikor a riasztás aktiválódik.


## <a name="create-a-security-playbook"></a>Biztonsági forgatókönyv létrehozása

Az alábbi lépésekkel új biztonsági forgatókönyvet hozhat létre az Azure Sentinelben:

1. Nyissa meg az **Azure Sentinel** irányítópultját.
2. A **Kezelés csoportban**válassza **a Forgatókönyvek**lehetőséget.

   ![Logikai alkalmazás](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Az **Azure Sentinel – Playbooks** lapon kattintson a **Hozzáadás** gombra.

   ![Logikai alkalmazás létrehozása](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. A **Logikai alkalmazás létrehozása** lapon írja be a kért adatokat az új logikai alkalmazás létrehozásához, majd kattintson a **Létrehozás gombra.** 

5. A [**Logic App Designer**](../logic-apps/logic-apps-overview.md)alkalmazásban jelölje ki a használni kívánt sablont. Ha olyan sablont választ, amely hitelesítő adatokat tesz szükségessé, meg kell adnia azokat. Másik lehetőségként létrehozhat egy új üres forgatókönyvet a semmiből. Válassza az **Üres logikai alkalmazás lehetőséget.** 

   ![Logikaialkalmazás-tervező](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. A logikai alkalmazástervezőbe kerül, ahol új at készíthet, vagy szerkesztheti a sablont. További információ a forgatókönyv létrehozásáról a [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md)segítségével.

7. Ha üres forgatókönyvet hoz létre, az **Összes összekötő és eseményindító keresése** mezőben írja be az Azure *Sentinel*parancsot, és válassza az **Azure Sentinel-riasztásra adott válasz aktiválásakor lehetőséget.** <br>Létrehozása után az új forgatókönyv megjelenik a **Forgatókönyvek** listában. Ha nem jelenik meg, kattintson a **Frissítés gombra.**

1. Használja az **Entitások begyűjtése** függvényt, amely lehetővé teszi, hogy a megfelelő entitásokat az **Entitások** listából, például számlákból, IP-címekből és állomásokból kapja meg. Ez lehetővé teszi, hogy adott entitásokon műveleteket futtasson.

7. Most meghatározhatja, mi történjen a forgatókönyv aktiválásakor. Hozzáadhat egy műveletet, logikai feltételt, kapcsolóeseti feltételeket vagy hurkokat.

   ![Logikaialkalmazás-tervező](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Hogyan kell futtatni a biztonsági ötletekbõl

Igény szerint futtathat egy forgatókönyvet.

Forgatókönyv igény szerinti futtatása:

1. Az **incidensek** lapon válasszon ki egy incidenst, és kattintson a **Részletek megtekintése gombra.**

2. A **Riasztások** lapon kattintson arra a figyelmeztetésre, amelyen futtatni szeretné a forgatókönyvet, és görgessen egészen jobbra, és kattintson a **Forgatókönyvek megtekintése** elemre, és válasszon ki egy forgatókönyvet az előfizetésben elérhető forgatókönyvek **listájából.** 



## <a name="automate-threat-responses"></a>A fenyegetésekre adott válaszok automatizálása

A SIEM/SOC csapatokat rendszeresen el lehet árasztani biztonsági riasztásokkal. A létrehozott riasztások mennyisége olyan hatalmas, hogy a rendelkezésre álló biztonsági rendszergazdák túlterheltek. Ez túl gyakran eredményez olyan helyzetekben, amikor sok riasztás nem vizsgálható ki, így a szervezet ki van téve a észrevétlen támadásoknak. 

Ezek a riasztások közül sok, ha nem a legtöbb, megfelel az ismétlődő mintáknak, amelyek et meghatározott és meghatározott javítási műveletekkel lehet kezelni. Az Azure Sentinel már lehetővé teszi, hogy meghatározza a szervizelést a forgatókönyvekben. Lehetőség van a valós idejű automatizálás beállítására is a forgatókönyv-definíció részeként, hogy teljes mértékben automatizálhassa az adott biztonsági riasztásokra adott meghatározott választ. A valós idejű automatizálás használatával a válaszcsapatok jelentősen csökkenthetik munkaterhelésüket azáltal, hogy teljes mértékben automatizálják az ismétlődő riasztásokra adott rutinválaszokat, lehetővé téve, hogy jobban összpontosítson az egyedi riasztásokra, a minták elemzésére, a fenyegetésvadászatra és egyebekre.

A válaszok automatizálása:

1. Válassza ki azt a riasztást, amelynek a válaszát automatizálni szeretné.
1. A **Riasztási szabály szerkesztése** lap **Valós idejű automatizálás**csoportban válassza ki azt az Aktivált **forgatókönyvet,** amelyet futtatni szeretne, amikor ez a riasztási szabály egyeztetve van.
1. Kattintson a **Mentés** gombra.

   ![valós idejű automatizálás](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan futtathatja a forgatókönyvet az Azure Sentinelben. Folytassa a [fenyegetések proaktív keresését](hunting.md) az Azure Sentinel használatával.


