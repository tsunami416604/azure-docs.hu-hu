---
title: "Az első automatizált munkafolyamat létrehozása rendszerek és felhőszolgáltatások között – Azure Logic Apps | Microsoft Docs"
description: "Üzleti folyamatok és munkafolyamatok automatizálása a rendszer-integrációs és vállalati alkalmazásintegrációs (EAI) forgatókönyvekhez logikai alkalmazások létrehozásával és futtatásával"
author: ecfan
manager: anneta
editor: 
services: logic-apps
keywords: "munkafolyamat, felhőalapú alkalmazások, felhőszolgáltatások, üzleti folyamatok, rendszerintegráció, vállalati alkalmazásintegráció, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>Az első logikai alkalmazás létrehozása munkafolyamatok és folyamatok automatizálására az Azure Portalon keresztül

Rendszereit és szolgáltatásait programkód írása nélkül is integrálhatja, ha automatizált munkafolyamatokat létesít és futtat az [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md) segítségével. Annak bemutatására, hogy milyen egyszerűen automatizálhatók a feladatok munkafolyamatokkal, az oktatóanyagban létrehozunk egy alapszintű logikai alkalmazást, amely egy webhely RSS-hírcsatornájában keres új tartalmakat, és e-mailt küld a csatornában talált minden egyes új elemről. 

![Áttekintés – példa az első logikai alkalmazásra](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Üres logikai alkalmazás létrehozása.
> * Eseményindító megadása, hogy a logikai alkalmazás elinduljon, ha egy új elemet tesznek közzé az RSS-hírcsatornában.
> * Művelet megadása, amely elküld egy, az RSS-hírcsatornaelem részleteit tartalmazó e-mailt.
> * A logikai alkalmazás futtatása és tesztelése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [kezdhet egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/). Egyéb esetben [regisztrálhat használatalapú fizetéses előfizetésre](https://azure.microsoft.com/pricing/purchase-options/).

* Egy e-mail-fiók [bármely, az Azure Logic Apps által támogatott e-mail-szolgáltatónál](../connectors/apis-list.md) az értesítések küldéséhez. Használhatja az Office 365 Outlookot, az Outlook.com-ot, a Gmailt vagy bármilyen egyéb támogatott szolgáltatót. Ebben az oktatóanyagban az Office 365 Outlookot használjuk.

  > [!TIP]
  > Ha rendelkezik személyes [Microsoft-fiókkal](https://account.microsoft.com/account), akkor Outlook.com-fiókja van. Ha Azure munkahelyi vagy iskolai fiókkal rendelkezik, akkor Office 365 Outlook-fiókja van.

* Egy webhely RSS-hírcsatornájára mutató hivatkozás. Ez a példa a [CNN.com webhely legfontosabb híreit tartalmazó RSS-hírcsatornáját használja](http://rss.cnn.com/rss/cnn_topstories.rss): `http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="1-create-a-blank-logic-app"></a>1. Üres logikai alkalmazás létrehozása 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal")

2. Az Azure fő menüjéből válassza az **Új** > **Enterprise Integration** > **Logic App** elemet.

   ![Azure Portal, Új, Vállalati integráció, Logikai alkalmazás](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Hozza létre a logikai alkalmazást a táblázatban megadott beállításokkal.

   ![A logikai alkalmazás részleteinek megadása](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Name (Név)** | *a-logikai-alkalmazása-neve* | Adjon meg egy egyedi nevet a logikai alkalmazás számára. | 
   | **Előfizetés** | *az-Ön-Azure-előfizetése* | Válassza ki a használni kívánt Azure-előfizetést. | 
   | **Erőforráscsoport** | *az-Ön-Azure-erőforráscsoportja* | Hozzon létre vagy válasszon ki egy Azure-erőforráscsoportot, amely segít a kapcsolódó Azure-erőforrások rendszerezésében és kezelésében. | 
   | **Hely** | *az-Ön-Azure-régiója* | Válassza ki az adatközpont-régiót a logikai alkalmazás üzembe helyezéséhez. | 
   |||| 

4. Mikor végzett, válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

   Ezzel létrehozott egy Azure-erőforrást a logikai alkalmazás számára. 
   Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer gyakran használt minták sablonjait jeleníti meg, hogy megkönnyítse az első lépéseket.

   > [!NOTE] 
   > Amikor kiválasztja a **Rögzítés az irányítópulton** elemet, a logikai alkalmazás az üzembe helyezés után megjelenik az Azure irányítópulton, és automatikusan megnyílik a Logic Apps Designerben. Ha a logikai alkalmazás nem nyílna meg, manuálisan is megkeresheti és megnyithatja.

5. Jelen esetben a **Sablonok** alatt válassza az **Üres logikai alkalmazás** elemet, így az alapoktól építheti fel saját logikai alkalmazását.

   ![Logikaialkalmazás-sablon kiválasztása](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   A Logic Apps Designerben most az elérhető [*összekötők*](../connectors/apis-list.md) és azok [*eseményindítói*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) jelennek meg, amelyek a logikai alkalmazás indítására használhatók.

   ![A logikai alkalmazások eseményindítói](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2. Eseményindító megadása a munkafolyamat indításához

Mindegyik logikai alkalmazásnak egy [*eseményindítóval*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) kell indulnia. Az eseményindítót egy meghatározott esemény váltja ki, vagy ha egy új adat megfelel a megadott követelményeknek. A Logic Apps-motor ekkor létrehoz egy logikaialkalmazás-példányt a munkafolyamat futtatásához. A motor az eseményindító minden egyes aktiválódása alkalmával egy külön példányt hoz létre a logikai alkalmazás munkafolyamatának futtatásához.

1. A keresőmezőbe írja be szűrőként az „rss” kifejezést. Válassza ki a következő eseményindítót: **RSS – Művelet hírcsatornaelem közzétételekor**. 

   ![Eseményindító kiválasztása: „RSS – Művelet hírcsatornaelem közzétételekor”](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Adja meg be a webhely követni kívánt RSS-hírcsatornájára mutató hivatkozást, például: `http://rss.cnn.com/rss/cnn_topstories.rss`. Adja meg az ismétlés időközét és gyakoriságát. Példánkban úgy állítsa be ezeket a tulajdonságokat, hogy a rendszer naponta ellenőrizze a hírcsatornát. 

   ![Eseményindító beállítása RSS-hírcsatornával, gyakorisággal és időközzel](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. Egyelőre mentse a munkáját. A tervező eszköztárán válassza a **Mentés** parancsot.
Az eseményindító részleteinek összecsukásához és elrejtéséhez kattintson az eseményindító címsorára.

   ![A logikai alkalmazás mentése](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   A logikai alkalmazás most már működőképes, de amíg fel nem vesz a munkafolyamatba műveleteket, mindössze annyit csinál, hogy ellenőrzi az új elemeket az RSS-hírcsatornában. 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3. Az eseményindítóra válaszoló művelet megadása

Most adjon meg egy [*műveletet*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), azaz egy, a logikai alkalmazás munkafolyamata által végrehajtott feladatot. A példában egy olyan műveletet adunk hozzá, amely e-mailt küld, ha új elem jelenik meg az RSS-hírcsatornában.

1. A Logic Apps Designerben az eseményindító területén válassza a **+ Új lépés** > **Művelet hozzáadása** elemet.

   ![Művelet hozzáadása](./media/logic-apps-create-a-logic-app/add-new-action.png)

   A tervező megjeleníti az [elérhető összekötőket](../connectors/apis-list.md), így választhat, hogy milyen művelet legyen végrehajtva az eseményindító aktiválásakor.

   ![Kiválasztás a műveletek listájáról](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. A keresőmezőbe írja be szűrőként az „e-mail küldése” kifejezést. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ezután válassza ki az „e-mail küldése” műveletet az összekötőn. Példa: 

   * Azure munkahelyi vagy iskolai fiókok esetében válassza az Office 365 Outlook-összekötőt. 
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt. 
   * Gmail-fiókok esetében válassza a Gmail-összekötőt. 

   Mi most az Office 365 Outlook-összekötőt választjuk ki. 
   Ha egy másik szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület eltérhet. 

   ![Válassza ezt a műveletet: „Office 365 Outlook – e-mail küldése”](./media/logic-apps-create-a-logic-app/actions.png)

3. Ha a rendszer hitelesítő adatok megadását kéri, jelentkezzen be az e-mail-fiókjához tartozó felhasználónévvel és jelszóval. 

4. Adja meg a táblázatban megadott adatokat, és válassza ki, hogy melyik mezőket tartalmazza az e-mail.

   | Művelet | Lépések | 
   | -- | ----- | 
   | Az elérhető mezők kiválasztása a munkafolyamathoz. | A szerkesztőmezőkbe kattintva nyissa le a **Dinamikus tartalom** listát, vagy válassza a **Dinamikus tartalom hozzáadása** elemet. | 
   | A többi elérhető mező megtekintése. | A **Dinamikus tartalom** listában válassza a **Továbbiak** lehetőséget minden egyes szakaszban.  | 
   | Üres sorok hozzáadása a szerkesztőmezőkben. | Nyomja le a Shift + Enter billentyűkombinációt. | 
   | A **Dinamikus tartalom** lista bezárása. | Kattintson ismét a **Dinamikus tartalom hozzáadása** elemre. | 
   ||| 

   ![Az e-mailben szerepeltetni kívánt adatok kiválasztása](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Címzett** | *címzett-e-mail-címe* | Adja meg a címzett e-mail-címét. Tesztelési célokra használhatja a saját e-mail-címét. | 
   | **Tárgy** | Új CNN-bejegyzés: **Hírcsatorna címe** | Adja meg az e-mail tárgymezőjének tartalmát. <p>Jelen oktatóanyag esetében írja be a javasolt szöveget, és válassza az eseményindító **Hírcsatorna címe** mezőjét, amely a hírcsatornaelem címét jeleníti meg. | 
   | **Törzs** | Cím: **Hírcsatorna címe** <p>Publikálás dátuma: **Hírcsatorna elsődleges hivatkozása** <p>Hivatkozás: **Elsődleges hírcsatorna hivatkozása** | Adja meg az e-mail törzsének tartalmát. <p>Jelen oktatóanyag esetében írja be a javasolt szöveget, és válassza az eseményindító alábbi mezőit: <p>A - **Hírcsatorna címe**, amely ismét megjeleníti a hírcsatornaelem címét </br>A - **Hírcsatorna közzétételi dátuma**, amely az elem közzétételének napját és időpontját jeleníti meg. </br>Az - **Elsődleges hírcsatorna hivatkozása**, amely a hírcsatornaelem URL-címét jeleníti meg | 
   |||| 

   > [!NOTE] 
   > Ha olyan mezőt választ ki, amely egy tömböt tartalmaz, a tervező automatikusan hozzáad egy „Mindegyikre” hurkot a tömbre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja az adott műveletet.

5. Miután végzett, mentse a módosításokat. A tervező eszköztárán válassza a **Mentés** parancsot.

   ![Az elkészült logikai alkalmazás](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   A logikai alkalmazás teszteléséhez folytassa a következő szakasszal.

## <a name="4-run-and-test-your-workflow"></a>4. A munkafolyamat futtatása és tesztelése

1. A logikai alkalmazás tesztelési célú manuálisan futtatásához a tervező eszköztárán válassza a **Futtatás** elemet, vagy engedélyezheti, hogy a logikai alkalmazás egy beállított ütemezés szerint ellenőrizze a megadott RSS-hírcsatornát.

   ![Logikai alkalmazás futtatása](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   Ha a logikai alkalmazás új elemeket talál, a kiválasztott adatokat tartalmazó e-mailt küld, például:

   ![Az új RSS-hírcsatornaelemről küldött e-mail](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   Ha a logikai alkalmazás nem talál új elemeket, kihagyja az e-mail küldésére vonatkozó műveletet, vár a következő esedékes időpontig, és újra elvégzi az ellenőrzést. 

2. A logikai alkalmazás futtatási és eseményindítási előzményeinek áttekintéséhez válassza az **Áttekintés** lehetőséget a logikai alkalmazás menüjében.
Az egyes futtatásokkal kapcsolatos részletek megtekintéséhez válassza az adott futtatás sorát.

   ![A logikai alkalmazás futtatási és eseményindítási előzményeinek figyelése és ellenőrzése](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Ha nem jelennek meg a várt adatok, válassza a **Frissítés** elemet az eszköztáron.

   Akár sikeres, akár sikertelen volt a futtatás, a Futtatás részletei nézeten áttekintheti a sikeres és sikertelen lépéseket. 

   ![Logikai alkalmazás futtatási részleteinek megtekintése](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   A logikai alkalmazás állapotával, illetve futtatási és eseményindítási előzményeivel kapcsolatos további információkért, valamint a logikai alkalmazás diagnosztizáláshoz tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

3. Az egyes lépések be- és kimeneteinek megtekintéséhez bontsa ki az áttekinteni kívánt egyes lépéseket. Ezek az információk segítenek diagnosztizálni és elhárítani a logikai alkalmazás hibáit. Példa:

   ![Lépés részleteinek megtekintése](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   További információkért lásd: [Logikai alkalmazások állapotának megfigyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulálunk, sikeresen létrehozta és futtatta az első logikai alkalmazását. Az itt ismertetett példa bemutatta, milyen egyszerűen, kódok használata nélkül hozhat létre rendszereket és szolgáltatásokat integráló folyamatokat automatizáló munkafolyamatokat.

> [!NOTE]
> A logikai alkalmazás addig fut, amíg ki nem kapcsolja az alkalmazást. Az alkalmazás ideiglenes leállításához folytassa a következő szakasszal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóprogram olyan erőforrásokat és műveleteket alkalmaz, amelyek költségekkel terhelhetik Azure-előfizetését. Miután végzett az oktatóanyaggal és a teszteléssel, mindenképp tiltsa le vagy inaktiválja azokat az erőforrásokat, amelyek költségeit nem szeretné fizetni.

A logikai alkalmazást és az e-mailek küldését az alkalmazás törlése nélkül is leállíthatja. A logikai alkalmazás menüjében válassza az **Áttekintés** elemet. Az eszköztáron válassza a **Letiltás** parancsot.

![A logikai alkalmazás kikapcsolása](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>GYIK

**K:** Milyen egyéb lehetőségeket kínál a logikai alkalmazás? </br>
**V:** Egyéb feladatokat is végrehajthat, például szerkesztheti és megtekintheti a JSON-definíciót, áttekintheti a tevékenységnaplót, vagy törölheti a logikai alkalmazást.

A logikai alkalmazás további felügyeleti tevékenységeiért tekintse át a következő parancsokat a logikai alkalmazás menüjében:

| Tevékenység | Lépések | 
| ---- | ----- | 
| Az alkalmazás állapotának, futtatási és eseményindítási előzményeinek és általános információinak megtekintése | Válassza az **Áttekintés** elemet. | 
| Az alkalmazás szerkesztése | Válassza a **Logikaialkalmazás-tervező** lehetőséget. | 
| Az alkalmazáshoz tartozó munkafolyamat JSON-definíciójának megtekintése | Válassza a **Logikai alkalmazás kódnézete** elemet. | 
| A logikai alkalmazáson végrehajtott műveletek megtekintése | Válassza a **Tevékenységnapló** elemet. | 
| A logikai alkalmazás korábbi verzióinak megtekintése | Válassza a **Verziók** elemet. | 
| Az alkalmazás ideiglenes kikapcsolása | Válassza az **Áttekintés** elemet, majd az eszköztárban válassza a **Letiltás** lehetőséget. | 
| Az alkalmazás törlése | Válassza az **Áttekintés** elemet, majd az eszköztárban válassza a **Törlés** lehetőséget. Adja meg a logikai alkalmazás nevét, majd válassza a **Törlés** elemet. | 
||| 

## <a name="get-support"></a>Támogatás kérése

* Az Azure Logic Apps szolgáltatással kapcsolatos kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Szeretné segíteni az Azure Logic Apps és az összekötők fejlesztését? Szavazzon az ötletekre [az Azure Logic Apps felhasználói visszajelzési oldalán](http://aka.ms/logicapps-wish), vagy küldje be saját javaslatait.

## <a name="next-steps"></a>Következő lépések

* [Logikai alkalmazás létrehozása Visual Studióval](../logic-apps/logic-apps-deploy-from-vs.md)
* [Feltételek hozzáadása és munkafolyamatok futtatása](../logic-apps/logic-apps-use-logic-app-features.md)
*   [A logikai alkalmazások sablonjai](../logic-apps/logic-apps-use-logic-app-templates.md)
* [Logikai alkalmazások létrehozása Azure Resource Manager-sablonokból](../logic-apps/logic-apps-arm-provision.md)
* [A Logic Apps használatának mérése](../logic-apps/logic-apps-pricing.md) 
* [A Logic Apps díjszabása](https://azure.microsoft.com/pricing/details/logic-apps)
