---
title: Biztonsági forgatókönyvek az Azure Security Centerben | Microsoft Docs
description: Ebből a dokumentumból megismerheti, hogyan automatizálható a biztonsági incidensekre történő válaszadás az Azure Security Center biztonsági forgatókönyvei segítségével.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: yurid
ms.openlocfilehash: 05245b2a7a4f7bf61052b13da5ee2a98be721f7c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259955"
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Biztonsági forgatókönyvek az Azure Security Centerben (előzetes verzió)
Ebből a dokumentumból megismerheti, hogyan használhatók az Azure Security Center biztonsági forgatókönyvei a biztonsággal kapcsolatos problémákra való válaszadásra.

## <a name="what-is-security-playbook-in-security-center"></a>Mit jelent a biztonsági forgatókönyv a Security Centerben?
A biztonsági forgatókönyv eljárások olyan gyűjteménye, amelyet végre lehet hajtani a Security Centerből, mihelyt egy riasztás kivált egy adott forgatókönyvet. A biztonsági forgatókönyvek segítségével automatizálható és összehangolható a válaszadás a Security Center által észlelt biztonsági riasztásokra. A Security Center biztonsági forgatókönyvei [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)-alkalmazásokon alapulnak. Ez azt jelenti, hogy használhatja a Logic Apps biztonsági kategóriájú sablonjait, amelyeket saját igényei szerint módosíthat is, vagy létrehozhat új forgatókönyveket az [Azure Logic Apps-munkafolyamat](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) segítségével, amelyekhez a Security Centert használhatja eseményindítóként. 

> [!NOTE]
> A forgatókönyvek az Azure Logic Appst használják, amiért díjat kell fizetni. További részletekért látogasson el az [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) árképzését ismertető oldalra. 

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Hogyan hozható létre egy biztonsági forgatókönyv a Security Centerben?
Az alábbi lépéseket követve hozhat létre egy biztonsági forgatókönyvet a Security Centerben:

1.  Nyissa meg a **Security Center** irányítópultját.
2.  A bal oldali panel **Automatizálás és vezénylés** részén kattintson a **Forgatókönyvek (előzetes verzió)** elemre.

    ![Logikai alkalmazás](./media/security-center-playbooks/security-center-playbooks-fig17.png)
 
3. A **Security Center – Forgatókönyvek (előzetes verzió)** oldalon kattintson a **Hozzáadás** gombra.

    ![Logikai alkalmazás létrehozása](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. A **Logikai alkalmazás létrehozása** oldalon írja be a kért adatokat az új logikai alkalmazás létrehozásához, majd kattintson a **Létrehozás** gombra. Az új forgatókönyv a létrehozást követően megjelenik a listában. Ha nem jelenik meg, kattintson a **Frissítés** gombra. Ha látja, kattintson rá a forgatókönyv szerkesztésének megkezdéséhez.

    ![Logikai alkalmazás létrehozása](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. Megjelenik a **Logikaialkalmazás-tervező**. Kattintson az **Üres logikai alkalmazás** elemre egy új forgatókönyv létrehozásához. Kiválaszthatja a kategóriák alatti **Biztonság** elemet is, és választhat a sablonok közül.
    
    ![Logikaialkalmazás-tervező](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. A **Keresés az összes összekötő és eseményindító között** mezőbe írja be az *Azure Security Center* kifejezést, majd válassza az  **Egy Azure Security Center-riasztásra adott válasz aktiválásakor** lehetőséget.

    ![Eseményindító](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. Most meghatározhatja, mi történjen a forgatókönyv aktiválásakor. Megadhat műveleteket, logikai feltételeket, kapcsolóeset-feltételeket vagy hurkokat.

    ![Logikaialkalmazás-tervező](./media/security-center-playbooks/security-center-playbooks-fig5.png)
     
## <a name="how-to-run-a-security-playbook-in-security-center"></a>Hogyan futtathatók a biztonsági forgatókönyvek a Security Centerben?

Forgatókönyveket akkor futtathat a Security Centerben, ha össze szeretné hangolni az eseményeket, további információkat szeretne szerezni más szolgáltatásoktól, vagy ha problémákat szeretne elhárítani. A forgatókönyvek eléréséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a **Security Center** irányítópultját.
2.  A bal oldali panelen, a **Fenyegetésészlelés** területen kattintson a **Biztonsági incidensek és riasztások** elemre.

    ![Riasztások](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Kattintson a vizsgálni kívánt riasztásra.
4.  A riasztás oldalának tetején kattintson a **Forgatókönyvek futtatása** gombra.

    ![Forgatókönyv futtatása](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. A Forgatókönyvek oldalon válassza ki a futtatni kívánt forgatókönyvet, majd kattintson a **Futtatás** gombra. Ha szeretné az aktiválás előtt megtekinteni a forgatókönyvet, kattintson rá, és megnyílik a tervező.

    ![Forgatókönyvek](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Előzmények

A forgatókönyv futtatása után elérheti az előző futtatásokat is, illetve az előzőleg végrehajtott forgatókönyvek állapotáról további adatokat tartalmazó lépéseket. Az előzmények riasztásonként kontextualizálva vannak, ami azt jelenti, hogy az oldalon látható előzmények az adott forgatókönyvet kiváltó riasztásra vonatkoznak. 

![Előzmények](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Ha további részletekre kíváncsi egy bizonyos forgatókönyv futtatásával kapcsolatban, kattintson magára a forgatókönyvre, és megjelenik a Logikai alkalmazás futtatása ablak, amely a teljes munkafolyamatot tartalmazza.

![Részletek](./media/security-center-playbooks/security-center-playbooks-fig14.png)

Ebben a munkafolyamatban látható, hogy melyik feladat végrehajtása mennyi időt vett igénybe, és az egyes feladatok kibontásával megtekinthetők az eredmények. 

### <a name="changing-an-existing-playbook"></a>Egy meglévő forgatókönyv módosítása

A meglévő forgatókönyveket a Security Centerben módosíthatja, ha műveleteket vagy feltételeket szeretne hozzájuk adni. Ehhez egyszerűen kattintson a módosítani kívánt forgatókönyv nevére a Forgatókönyvek lapon, és megnyílik a Logikaialkalmazás-tervező.

> [!NOTE]
> A saját forgatókönyvek Azure-beli logikai alkalmazások használatával történő létrehozásáról további információt [az első logikai alkalmazás munkafolyamatának a felhőalapú alkalmazások és felhőszolgáltatások közötti folyamatok automatizálásához történő létrehozását](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app#add-an-action-that-responds-to-your-trigger) ismertető cikkben talál.


## <a name="see-also"></a>Lásd még
Ebben a dokumentumban megismerkedhetett az Azure Security Center forgatókönyveinek használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti. 
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

