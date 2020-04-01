---
title: Munkafolyamat-automatizálás az Azure Security Centerben | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre és automatizálhat munkafolyamatokat az Azure Security Centerben
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5d947cf41e13abdea9a2fd29f8a740d0c101dc6f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397915"
---
# <a name="workflow-automation"></a>Munkafolyamat-automatizálás

Minden biztonsági program több munkafolyamatot tartalmaz az incidensek reválaszolásához. Ezek a folyamatok magukban foglalhatják az érintett érdekelt felek értesítését, a változáskezelési folyamat elindítását és a konkrét javítási lépések alkalmazását. A biztonsági szakértők azt javasolják, hogy a lehető legtöbb lépést automatizálja. Az automatizálás csökkenti a terhelést. A folyamatlépései gyors, következetes és az előre meghatározott követelményeknek megfelelően történő elvégzésének biztosításával is javíthatja a biztonságot.

Ez a cikk az Azure Security Center munkafolyamat-automatizálási funkcióját ismerteti. Ez a funkció a logikai alkalmazások at biztonsági riasztások és javaslatok. Előfordulhat például, hogy a Security Center nek e-mailt kell adnia egy adott felhasználónak, ha riasztás történik. Azt is megtudhatja, hogyan hozhat létre logikai alkalmazásokat [az Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)használatával.

> [!NOTE]
> Ha korábban már használta a Forgatókönyvek (Előzetes verzió) nézetet az oldalsávon, ugyanazokat a funkciókat a kiterjesztett funkciókkal együtt találja az új munkafolyamat-automatizálási lapon.


## <a name="requirements"></a>Követelmények

* Az Azure Logic Apps-munkafolyamatok használatával való munkához a következő Logic Apps-szerepkörökkel/engedélyekkel kell rendelkeznie:

    * [A Logic App Operator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) engedélyei szükségesek, vagy a Logic App olvasási/eseményindító hozzáférése (ez a szerepkör nem hozhat létre és nem szerkeszthet logikai alkalmazásokat; csak meglévőket *futtathat)*

    * [A Logic App Közreműködő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) engedélyei szükségesek a Logikai alkalmazás létrehozásához és módosításához

* Ha Logic App-összekötőket szeretne használni, további hitelesítő adatokra lehet szüksége a megfelelő szolgáltatásokba való bejelentkezéshez (például az Outlook/Teams/Slack-példányokba)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Logikai alkalmazás létrehozása és annak automatikus futtatásának meghatározása 

1. A Biztonsági központ oldalsávján válassza a **Munkafolyamat-automatizálás**lehetőséget.

    [![Munkafolyamat-automatizálások listája](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Ezen a lapon új automatizálási szabályokat hozhat létre, valamint engedélyezheti, letilthatja vagy törölheti a meglévőket.  
1. Új munkafolyamat definiálásához kattintson a **Munkafolyamat-automatizálás hozzáadása**gombra. 

    Megjelenik egy ablaktábla az új automatizálás beállításaival. Itt adhatja meg:
    1. Az automatizálás neve és leírása.
    1. Az automatikus munkafolyamatot kezdeményező eseményindítók. Előfordulhat például, hogy a logikai alkalmazás futtatásához, amikor egy "SQL" biztonsági riasztást hoz létre.
    1. A logikai alkalmazás, amely akkor fog futni, ha az eseményindító feltételek teljesülnek. 

        [![Munkafolyamat-automatizálások listája](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. A Műveletek szakaszban kattintson **a Hozzon létre egy újat** a logikai alkalmazás létrehozási folyamatának megkezdéséhez.

    A szolgáltatás az Azure Logic Apps alkalmazásba kerül.

    [![Új logikai alkalmazás létrehozása](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Adjon meg egy nevet, erőforráscsoportot és helyet, majd kattintson a **Létrehozás gombra.**

1. Az új Logikai alkalmazásban a biztonsági kategóriában beépített, előre definiált sablonok közül választhat. Vagy megadhatja az események egyéni folyamatát, amely a folyamat aktiválásakor következik be.

    A Logic App designer a következő eseményindítók a Security Center összekötők támogatottak:

    * **Az Azure Security Center-javaslat létrehozásakor vagy aktiválásakor**
    * **Az Azure Security Center-riasztás létrehozásakor vagy aktiválásakor** 
    
    > [!TIP]
    > Testreszabhatja az eseményindítót úgy, hogy csak az Önt érdeklő súlyossági szintekkel rendelkező riasztásokra vonatkozzon.
    
    > [!NOTE]
    > Ha az örökölt eseményindító "Amikor egy Azure Security Center-riasztásra adott válasz aktiválódik", a logikai alkalmazások nem indítja el a Munkafolyamat-automatizálás i. Ehelyett használja a fent említett kiváltó okok bármelyikét. 

    [![Minta logikai alkalmazás](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Miután definiálta a logikai alkalmazást, térjen vissza a munkafolyamat-automatizálásdefinícióablaktáblájára ("Munkafolyamat-automatizálás hozzáadása"). Kattintson **a Frissítés** gombra, ha azt szeretné, hogy az új logikai alkalmazás kiválasztható legyen.

    ![Frissítés](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Válassza ki a logikai alkalmazást, és mentse az automatizálást. Vegye figyelembe, hogy a Logic App legördülő csak a logic apps támogató Security Center-összekötők a fent említett.


## <a name="manually-trigger-a-logic-app"></a>Logikai alkalmazás manuális aktiválása

A Logic Apps manuálisan is futtatható, ha biztonsági riasztást vagy bármely olyan javaslatot tekint meg, amely [gyorsjavítás-javítást](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation)kínál.

Logikai alkalmazás manuális futtatásához nyisson meg egy riasztást vagy egy javaslatot, amely támogatja a gyorsjavítás szervizelését, és kattintson **az Eseményindító logikai alkalmazásra:**

[![Logikai alkalmazás manuális aktiválása](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Adattípusok sémák

A logikai alkalmazáspéldánynak átadott biztonsági riasztások vagy javaslatok eseményeinek nyers eseménysémáinak megtekintéséhez látogasson el a [Munkafolyamat-automatizálási adattípusok sémájába.](https://aka.ms/ASCAutomationSchemas) Ez akkor lehet hasznos, ha nem használja a Security Center fent említett beépített Logic App-összekötőit, hanem a Logic App általános HTTP-összekötőjét használja – használhatja az esemény JSON-sémáját manuálisan, ahogy ön megfelelőnek látja.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerte a Logic Apps létrehozása, azok végrehajtásának automatizálása a Security Centerben, és manuálisan futtatja őket. 

Egyéb kapcsolódó anyagokról: 

- [A Microsoft Learn modul arról, hogyan lehet a munkafolyamat-automatizálással automatizálni a biztonsági válaszokat](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Biztonsági javaslatok az Azure Security Centerben](security-center-recommendations.md)
- [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md)
- [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Összekötők a Logic Appsben](https://docs.microsoft.com/connectors/)
- [Munkafolyamat-automatizálási adattípusok sémái](https://aka.ms/ASCAutomationSchemas)
