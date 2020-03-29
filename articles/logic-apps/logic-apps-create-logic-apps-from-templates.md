---
title: Gyorsabban hozhat létre logikai alkalmazás-munkafolyamatokat előre összeállított sablonok használatával
description: Az Azure Logic Apps előre összeállított sablonjainak használatával gyorsan készíthet logikai alkalmazás-munkafolyamatokat
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: aac0060527af7b7d880f971e7608be3fa44a2d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905116"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Logikai alkalmazás munkafolyamatok létrehozása előre összeállított sablonokból

A munkafolyamatok gyorsabb létrehozásához a Logic Apps sablonokat biztosít, amelyek előre összeállított logikai alkalmazások, amelyek követik a gyakran használt mintákat. Használja ezeket a sablonokat a megadott formában, vagy szerkesztheti őket, hogy illeszkedjen ek a forgatókönyvhöz.

Íme néhány sablonkategória:

| Sablon típusa | Leírás | 
| ------------- | ----------- | 
| Vállalati felhősablonok | Az Azure Blob, a Dynamics CRM, a Salesforce, a Box integrálásához és a vállalati felhőbeli igényekhez szükséges egyéb összekötők beépítéséhez. Ezekkel a sablonokkal például rendszerezheti az üzleti érdeklődőket, vagy biztonsági másolatot kaphat a vállalati fájladatokról. | 
| Személyes hatékonysági sablonok | A személyes hatékonyság ot a napi emlékeztetők beállításával, a fontos munkaelemek feladatlistákká alakításával és a hosszadalmas feladatok automatizálásával egyetlen felhasználói jóváhagyási lépéssel javíthatja. | 
| Fogyasztói felhősablonok | A közösségi média szolgáltatások, például a Twitter, a Slack és az e-mail integrálásáért. Hasznos a közösségi média marketing kezdeményezéseinek megerősítéséhez. Ezek a sablonok olyan feladatokat is tartalmaznak, mint például a felhőalapú másolás, amely növeli a termelékenységet azáltal, hogy időt takarít meg a hagyományosan ismétlődő feladatokra. | 
| Vállalati integrációs csomagsablonok | Veter (veter-kód, kibontás, átalakítás, gazdagítás, útvonal) konfigurálásához, X12 EDI-dokumentum FOGADÁSához AS2-en keresztül és XML-vé alakításhoz, valamint X12, EDIFACT és AS2 üzenetek kezeléséhez. | 
| Protokollmintasablonok | Protokollminták, például HTTP-n keresztüli kérelem-válasz megvalósításához és FTP- és SFTP-alapú integrációkhoz. Használja ezeket a sablonokat a megadott a képpen, vagy építsen rájuk összetett protokollmintákhoz. | 
||| 

Ha nem rendelkezik Azure-előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/) az eljárás megkezdése előtt. A logikai alkalmazások létrehozásáról további információt a [Logikai alkalmazás létrehozása című témakörben](../logic-apps/quickstart-create-first-logic-app-workflow.md)talál.

## <a name="create-logic-apps-from-templates"></a>Logikai alkalmazások létrehozása sablonból

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalon.](https://portal.azure.com "Azure portál")

2. Az Azure főmenüjében válassza az Erőforrás > létrehozása**vállalati integrációs** > **logikai alkalmazás** **lehetőséget.**

   ![Azure Portal, Új, Vállalati integráció, Logikai alkalmazás](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Hozza létre a logikai alkalmazást a következő kép alatti táblázatban szereplő beállításokkal:

   ![A logikai alkalmazás részleteinek megadása](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Név** | *a-logikai-alkalmazása-neve* | Adjon meg egy egyedi nevet a logikai alkalmazás számára. | 
   | **Előfizetés** | *az-Ön-Azure-előfizetésének-neve* | Válassza ki a használni kívánt Azure-előfizetést. | 
   | **Erőforráscsoport** | *az-Ön-Azure-erőforráscsoportjának-neve* | Hozzon létre vagy válasszon ki egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) ehhez a logikai alkalmazáshoz, és rendszerezze az alkalmazáshoz társított összes erőforrást. | 
   | **Helyen** | *az-Ön-Azure-adatközpont-régiója* | Válassza ki az adatközpont-régiót a logikai alkalmazás üzembe helyezéséhez, például az USA nyugati régióját. | 
   | **Log Analytics** | **Ki** (alapértelmezett) vagy **Be** | Állítsa be a [diagnosztikai naplózást](../logic-apps/monitor-logic-apps-log-analytics.md) a logikai alkalmazáshoz az [Azure Monitor naplói](../log-analytics/log-analytics-overview.md)nak használatával. Ehhez már rendelkeznie kell egy Log Analytics-munkaterülettel. | 
   |||| 

4. Amikor végzett, válassza a **Rögzítés az irányítópulton** lehetőséget. Így a logikai alkalmazás automatikusan megjelenik az Azure-irányítópulton, és megnyílik az üzembe helyezés után. Válassza a **Létrehozás** elemet.

   > [!NOTE]
   > Ha nem szeretné rögzíteni a logikai alkalmazást, a folytatáshoz manuálisan kell megkeresnie és megnyitnia a logikai alkalmazást az üzembe helyezés után.

   Miután az Azure üzembe helyezte a logikai alkalmazást, megnyílik a Logic Apps Designer, és egy bemutató videót tartalmazó oldalt jelenít meg. 
   A videó alatt megtalálja a gyakori logikaialkalmazás-minták sablonjait. 

5. Görgessen tovább a bevezető videó és a gyakori eseményindítók **a sablonok**. Válasszon egy előre összeállított sablont. Példa:

   ![Logikai alkalmazássablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > A logikai alkalmazás teljesen új, válassza a **Blank Logic App**lehetőséget.

   Amikor előre összeállított sablont választ ki, további információkat tekinthet meg a sablonról. 
   Példa:

   ![Előre összeállított sablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. A kijelölt sablon folytatásához válassza **a Sablon használata**lehetőséget. 

7. A sablonban lévő összekötők alapján a rendszer az alábbi lépések valamelyikének végrehajtására kéri:

   * Jelentkezzen be hitelesítő adataival a sablon által hivatkozott rendszerekbe vagy szolgáltatásokba.

   * Hozzon létre kapcsolatokat a sablon által hivatkozott szolgáltatásokhoz vagy rendszerekhez. Kapcsolat létrehozásához adja meg a kapcsolat nevét, és szükség esetén jelölje ki a használni kívánt erőforrást. 

   * Ha már beállította ezeket a kapcsolatokat, válassza a **Folytatás gombot.**

   Példa:

   ![Kapcsolatok létrehozása](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Ha elkészült, a logikai alkalmazás megnyílik, és megjelenik a Logic Apps Designer.

   > [!TIP]
   > A sablonmegjelenítőhöz való visszatéréshez válassza a tervező **eszköztárának Sablonok parancsát.** Ez a művelet elveti a nem mentett módosításokat, ezért egy figyelmeztető üzenet jelenik meg a kérés megerősítésére.

8. Folytassa a logikai alkalmazás létrehozásának folytatását.

   > [!NOTE] 
   > Számos sablon olyan összekötőt tartalmaz, amely esetleg már előre kitöltött szükséges tulajdonságokkal rendelkezik. Előfordulhat azonban, hogy egyes sablonokhoz továbbra is szükség lehet értékek megadására a logikai alkalmazás megfelelő üzembe helyezése előtt. Ha a hiányzó tulajdonságmezők kitöltése nélkül próbál telepíteni, hibaüzenet jelenik meg. 

## <a name="update-logic-apps-with-templates"></a>Logikai alkalmazások frissítése sablonokkal

1. Az [Azure Portalon](https://portal.azure.com "Azure portál")keresse meg és nyissa meg a logikai alkalmazást a logic app designerben.

2. A tervező eszköztárán válassza a **Sablonok**lehetőséget. Ez a művelet elveti a nem mentett módosításokat, így egy figyelmeztető üzenet jelenik meg, így megerősítheti, hogy folytatni kívánja. A megerősítéshez válassza az **OK gombot.** Példa:

   ![Válassza a "Sablonok" lehetőséget](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Görgessen tovább a bevezető videó és a gyakori eseményindítók **a sablonok**. Válasszon egy előre összeállított sablont. Példa:

   ![Logikai alkalmazássablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Amikor előre összeállított sablont választ ki, további információkat tekinthet meg a sablonról. 
   Példa:

   ![Előre összeállított sablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. A kijelölt sablon folytatásához válassza **a Sablon használata**lehetőséget. 

5. A sablonban lévő összekötők alapján a rendszer az alábbi lépések valamelyikének végrehajtására kéri:

   * Jelentkezzen be hitelesítő adataival a sablon által hivatkozott rendszerekbe vagy szolgáltatásokba.

   * Hozzon létre kapcsolatokat a sablon által hivatkozott szolgáltatásokhoz vagy rendszerekhez. Kapcsolat létrehozásához adja meg a kapcsolat nevét, és szükség esetén jelölje ki a használni kívánt erőforrást. 

   * Ha már beállította ezeket a kapcsolatokat, válassza a **Folytatás gombot.**

   ![Kapcsolatok létrehozása](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   A logikai alkalmazás most megnyílik, és megjelenik a Logic Apps Designer.

8. Folytassa a logikai alkalmazás létrehozásának folytatását. 

   > [!TIP]
   > Ha még nem mentette a módosításokat, elvetheti a munkáját, és visszatérhet az előző logikai alkalmazáshoz. A tervező eszköztárán válassza az **Elvetés gombot.**

> [!NOTE] 
> Számos sablon olyan összekötőt tartalmaz, amely esetleg már előre kitöltött szükséges tulajdonságokkal rendelkezik. Előfordulhat azonban, hogy egyes sablonokhoz továbbra is szükség lehet értékek megadására a logikai alkalmazás megfelelő üzembe helyezése előtt. Ha a hiányzó tulajdonságmezők kitöltése nélkül próbál telepíteni, hibaüzenet jelenik meg.

## <a name="deploy-logic-apps-built-from-templates"></a>Sablonokból készült logikai alkalmazások telepítése

Miután végrehajtotta a módosításokat a sablonon, mentheti a módosításokat. Ez a művelet is automatikusan közzéteszi a logikai alkalmazást.

A tervező eszköztárán válassza a **Mentés** parancsot.

![A logikai alkalmazás mentése és közzététele](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

Ismerje meg a logikai alkalmazások készítését példák, forgatókönyvek, ügyféltörténetek és forgatókönyvek segítségével.

> [!div class="nextstepaction"]
> [Logikai alkalmazáspéldák, forgatókönyvek és forgatókönyvek áttekintése](../logic-apps/logic-apps-examples-and-scenarios.md)