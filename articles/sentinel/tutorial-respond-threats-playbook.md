---
title: Forgatókönyv futtatása az Azure-Sentinel-Előzetesében |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Sentinel-forgatókönyv futtatása.
services: sentinel
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: db6751d6d43a9af31e157ffaeb8c8d3725982dd4
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992789"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel-preview"></a>Oktatóanyag: Automatizált threat válaszok az Azure-on Előzetesben Sentinel-beállítása

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az oktatóanyag segít az biztonsági forgatókönyvek az Azure-Sentinel beállítása Azure-Sentinel által észlelt biztonsági problémák automatikus threat válaszokat.


> [!div class="checklist"]
> * Forgatókönyvek ismertetése
> * Egy forgatókönyv létrehozása
> * Forgatókönyv futtatása


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Mi az a biztonsági forgatókönyvek az Azure-Sentinel?

Egy biztonsági forgatókönyv eljárások egy riasztásra adott válaszként az Azure-Sentinel futtatható gyűjteménye. A biztonsági forgatókönyvek segítségével automatizálható és összehangolható a válaszadás és futtatható manuálisan, illetve beállítása automatikusan futnak, amikor adott figyelmeztetéseket. Biztonsági forgatókönyvek az Azure-Sentinel alapuló [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), ami azt jelenti, hogy a teljesítmény, testreszabhatóság és Logic Apps beépített sablonokkal beolvasása. Minden egyes forgatókönyv jön létre az adott előfizetés mellett dönt, de ha megnézi a forgatókönyvek oldalon, látni fogja az összes forgatókönyvek bármely kiválasztott előfizetések között.

> [!NOTE]
> Forgatókönyvek kihasználhatja az Azure Logic Apps, amiért díjat kell fizetni. További részletekért látogasson el az [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) árképzését ismertető oldalra.

Például Ha aggódik a rosszindulatú támadók a hálózati erőforrások eléréséhez, beállíthat egy riasztást, mely figyeli a nem kártékony IP-címek, a hálózat eléréséhez. Ezután hozhat létre egy forgatókönyv, amely a következőket hajtja végre:
1. A riasztás akkor aktiválódik, ha a ServiceNow vagy bármely más informatikai jegykiadó rendszer nyissa meg egy jegyet.
2. Üzenet küldése a biztonsági műveletek csatorna a Microsoft Teams vagy a Slack, hogy az incidens tisztában a biztonsági elemzőknek.
3. A riasztásban szereplő összes információt küld a vezető hálózati rendszergazda és biztonsági rendszergazdával. Az e-mail-üzenetet is magában foglalja a két felhasználói választógomb **blokk** vagy **figyelmen kívül hagyása**.
4. A forgatókönyv futtatása után a válasz érkezik a rendszergazdák továbbra is.
5. Ha a rendszergazdák **blokk**, az IP-cím le van tiltva, a tűzfal és a felhasználó le van tiltva, az Azure ad-ben.
6. Ha a rendszergazdák **figyelmen kívül hagyása**, a riasztás le van zárva, az Azure-Sentinel, és az incidens le van zárva, a ServiceNow.

Biztonsági forgatókönyvek futtathatja manuálisan vagy automatikusan. Manuális futtatása, az azt jelenti, hogy amikor riasztást kap, választhat a forgatókönyv igény szerinti futtatásához a kiválasztott riasztásra adott válaszként. Automatikusan rendszert futtató, az azt jelenti, hogy a korrelációs szabály meggondolni beállíthatja, hogy automatikusan futtasson egy vagy több forgatókönyvek, a riasztás akkor aktiválódik, amikor.


## <a name="create-a-security-playbook"></a>A biztonsági forgatókönyvek létrehozása

Kövesse az alábbi lépéseket egy új biztonsági forgatókönyv létrehozása az Azure-Sentinel:

1.  Nyissa meg a **Azure Sentinel-** irányítópultot.
2.  A **felügyeleti**válassza **forgatókönyvek**.

   ![Logikai alkalmazás](./media/tutorial-respond-threats-playbook/playbook.png)

3. Az a **Sentinel-Azure - forgatókönyvek (előzetes verzió)** kattintson **Hozzáadás** gombra.

   ![Logikai alkalmazás létrehozása](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Az a **logikai alkalmazás létrehozása** írja be az új logikai alkalmazás létrehozásához, majd kattintson a kért információ **létrehozás**. 

5. Az a [ **Logikaialkalmazás-Tervező**](../logic-apps/logic-apps-overview.md), válassza ki a használni kívánt sablont. Ha egy sablon, amely hitelesítő adatokat, akkor adja meg azokat. Másik megoldásként létrehozhat egy új üres forgatókönyv előzmények. Válassza ki **üres logikai alkalmazás**. 

   ![Logikaialkalmazás-tervező](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Ekkor megnyílik a Logikaialkalmazás-Tervező, vagy új hozhat létre és szerkesztheti a sablont. Egy forgatókönyv-létrehozásával kapcsolatos további információkért a [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Létrehozásakor egy üres forgatókönyv a a **keresés az összes összekötő és trigger között** mezőbe írja be a *Azure Sentinel-*, és válassza ki **aktiváltegyAzureSentinel-riasztásraadottválaszesetén**. <br>Miután elkészült, az új forgatókönyv megjelenik a **forgatókönyvek** listája. Ha nem jelenik meg, kattintson a **frissítése**. 

7. Most meghatározhatja, mi történjen a forgatókönyv aktiválásakor. Hozzáadhat egy műveletet, logikai feltételeket, kapcsolóeset-feltételeket vagy hurkokat.

   ![Logikaialkalmazás-tervező](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>A biztonsági forgatókönyvek futtatása

Egy forgatókönyv igény szerint futtathatja.

A forgatókönyv igény szerinti futtatása:

1. Az a **esetek** lapon válasszon egy esetet, és kattintson a **részletes információk megtekintéséhez**.

2. Az a **riasztások** lapra, kattintson a futtatni a forgatókönyvet a kívánt riasztást, és görgessen egészen a jobb oldalon, majd kattintson a **forgatókönyvek megtekintése** , és válassza ki a forgatókönyv a **futtassa** a a Az előfizetésben elérhető forgatókönyvek listája. 




## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedhetett az Azure Sentinel-forgatókönyv futtatása. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket: Ebben az oktatóanyagban megtanulta, hogyan forgatókönyv futtatása az Azure-Sentinel. Továbbra is a [hogyan fenyegetések proaktív módon hunt](hunting.md) Azure Sentinel-használatával.
> [!div class="nextstepaction"]
> [A fenyegetések Hunt](hunting.md) proaktív módon a hálózaton a fenyegetéseket található.

