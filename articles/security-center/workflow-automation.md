---
title: Munkafolyamat-automatizálás (előzetes verzió) a Azure Security Centerban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és automatizálhat munkafolyamatokat Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 1abfd8af7e0ef18d4e7bcf05b4726c5d0bcb0a84
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151693"
---
# <a name="workflow-automation-preview"></a>Munkafolyamat-automatizálás (előzetes verzió)

Minden biztonsági program több munkafolyamatot tartalmaz az incidensek megválaszolásához. Ezek a folyamatok magukban foglalhatják az érintett érintett felek értesítését, a módosítási felügyeleti folyamat indítását és az adott szervizelési lépések alkalmazását. A biztonsági szakértők azt ajánlják, hogy az eljárások több lépésének automatizálására legyen lehetőség. Az Automation csökkenti a terhelést. Emellett javíthatja a biztonságot, mivel biztosítja, hogy a folyamat lépései gyorsan, következetesen és az előre meghatározott követelményeknek megfelelően történjenek.

Ez a cikk a Azure Security Center munkafolyamat-automatizálási funkcióját (előzetes verzió) ismerteti. Ez az előzetes verziójú funkció a biztonsági riasztások és javaslatok Logic Apps indítását is elvégezheti. Előfordulhat például, hogy egy adott felhasználót szeretne e-mailben elküldeni a Security Center riasztás esetén. Azt is megtudhatja, hogyan hozhat létre Logic Apps a [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)használatával.

> [!NOTE]
> Ha korábban a forgatókönyvek (előzetes verzió) nézetet használta az oldalsávon, akkor ugyanazokat a funkciókat fogja használni, mint az új munkafolyamat-automatizálás (előzetes verzió) lapon található kibővített funkciókkal.


## <a name="requirements"></a>Követelmények

* Azure Logic Apps munkafolyamatok használatához a következő Logic Apps szerepkörökkel/engedélyekkel kell rendelkeznie:

    * A [Logic app-operátor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) engedélyei kötelezőek vagy logikai alkalmazások olvasási/aktiválási hozzáférése (ez a szerepkör nem tud logikai alkalmazásokat létrehozni vagy szerkeszteni, csak a meglévőket *futtathatja* )

    * A logikai alkalmazás létrehozásához és módosításához a [Logic app közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) engedélyei szükségesek

* Ha logikai alkalmazás-összekötőket szeretne használni, további hitelesítő adatokra lehet szüksége a saját szolgáltatásaiba való bejelentkezéshez (például az Outlook/csapatok/Slack-példányok esetében)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Logikai alkalmazás létrehozása és az automatikus futtatásának meghatározása 

1. A Security Center oldalsávján válassza a **munkafolyamat-automatizálás (előzetes verzió)** lehetőséget.

    [a munkafolyamat-automatizálások ![listája](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Ebből a lapról új Automation-szabályokat hozhat létre, valamint engedélyezheti, letilthatja vagy törölheti a meglévőket.  
1. Új munkafolyamat definiálásához kattintson a **munkafolyamat-automatizálás hozzáadása**lehetőségre. 

    Megjelenik egy ablaktábla az új automatizálási beállításokkal. Itt megadhatja a következőket:
    1. Az automatizálás neve és leírása.
    1. Az automatikus munkafolyamatot kezdeményező eseményindítók. Előfordulhat például, hogy azt szeretné, hogy a logikai alkalmazás akkor fusson, amikor az "SQL" kifejezést tartalmazó biztonsági riasztást hoz létre.
    1. Az a logikai alkalmazás, amely az aktiválási feltételek teljesülése esetén fog futni. 

        [a munkafolyamat-automatizálások ![listája](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. A műveletek szakaszban kattintson az **új létrehozása** lehetőségre a logikai alkalmazás létrehozási folyamatának megkezdéséhez.

    Azure Logic Apps.

    [új logikai alkalmazás létrehozása ![](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Adja meg a nevet, az erőforráscsoportot és a helyet, majd kattintson a **Létrehozás**gombra.

1. Az új logikai alkalmazásban a biztonsági kategóriából választhat a beépített, előre definiált sablonok közül. Megadhat egyéni folyamatokat is, amelyek a folyamat indításakor történnek.

    A Logic app Designerben a Security Center összekötők következő eseményindítói támogatottak:

    * **Azure Security Center javaslat létrehozása vagy elindítása (előzetes verzió)**
    * **Azure Security Center riasztás létrehozásakor vagy aktiválásakor (előzetes verzió)**
    
    > [!NOTE]
    > Ha az örökölt triggert használja a Azure Security Center riasztásra adott válasz indításakor, a rendszer nem indítja el a Logic Apps a munkafolyamat-automatizálási szolgáltatás. Ehelyett használja a fent említett eseményindítók egyikét. 

    [![minta logikai alkalmazás](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. A logikai alkalmazás meghatározása után térjen vissza a munkafolyamat-automatizálási definíciós panelre ("Munkafolyamat-automatizálás hozzáadása"). Kattintson a **frissítés** lehetőségre, hogy az új logikai alkalmazás elérhető legyen a kiválasztáshoz.

    ![Frissítés](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Válassza ki a logikai alkalmazást, és mentse az automatizálást. Vegye figyelembe, hogy a logikai alkalmazás legördülő lista csak a fent említett Security Center-összekötőket mutatja Logic Apps.


## <a name="manually-trigger-a-logic-app"></a>Logikai alkalmazás manuális elindítása

Logic Apps manuálisan is futtathatja a biztonsági javaslatok megtekintésekor.

Egy logikai alkalmazás manuális futtatásához nyisson meg egy javaslatot, és kattintson az trigger Logic app (előzetes verzió) elemre:

[Logikai alkalmazás manuális elindítása ![](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan hozhat létre Logic Appsokat, és hogyan futtathatja azokat manuálisan Security Center, és automatizálhatja a végrehajtást. 

Más kapcsolódó anyagok esetében tekintse meg a következő cikkeket: 

- [Biztonsági javaslatok a Azure Security Center](security-center-recommendations.md)
- [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md)
- [Tudnivalók Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Logic Apps összekötők](https://docs.microsoft.com/connectors/)
