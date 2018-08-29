---
title: Munkafolyamatok létrehozása sablonokból – Azure Logic Apps |} A Microsoft Docs
description: Gyorsabb munkafolyamatok létrehozása az Azure Logic Appsben logic app-sablonok használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.date: 10/15/2017
ms.openlocfilehash: 10191a4fbab325dcd5134b082f050188c6798079
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122120"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Logikai alkalmazás munkafolyamatok létrehozása előre összeállított sablonokból

Ahhoz, hogy gyorsabban létre munkafolyamatokat, a Logic Apps biztosít a sablonokat, amelyek előre összeállított logic Apps, az alábbi gyakran használt minták. Megadott használja ezeket a sablonokat, vagy módosíthatja őket saját forgatókönyvéhez igazítva.

Az alábbiakban néhány alkalmazássablon-kategóriák:

| Sablon típusa | Leírás | 
| ------------- | ----------- | 
| Vállalati felhő-sablonok | Használható az Azure Blob, a Dynamics CRM, a Salesforce, Box, és egyéb összekötőkhöz tartalmaz a vállalati felhőhöz van szüksége. Ezek a sablonok használatával például rendezheti az érdeklődők, vagy a vállalati adatok biztonsági mentése. | 
| Személyes hatékonysági sablonok | Személyes termelékenység növelése a napi emlékeztetőket beállításával, fontos bekapcsolásával a munkaelemek teendőlistáit, valamint automatizálhatja hosszadalmas feladatok le egy egyetlen felhasználó jóváhagyási lépést. | 
| Fogyasztói felhőalapú sablonok | Például a Twitter és a közösségi médiába Slack integrálása, és e-mailt. Hasznos meg a kezdeményezések marketing, a közösségi médiában. Ezeket a sablonokat is tartalmaznak a feladatokat, köztük a másolja, növeli a hatékonyságot a időmegtakarítás hagyományosan ismétlődő feladatokat a felhőben. | 
| Vállalati integráció felügyeleticsomag-sablonok | VETER konfigurálásához (érvényesítéséhez, kinyerése, átalakítása, bővítését, irányíthatja a) a folyamatok X12 fogadása AS2 és az XML-átalakítás és kezelési X12, EDIFACT, EDI-dokumentum és az AS2-üzenetek. | 
| Protokoll minta sablonok | Végrehajtási protokoll mintákat, például a kérés-válasz HTTP-és integrációk FTP és SFTP. Használja ezeket a sablonokat, a megadott, vagy azokat az összetett protokoll mintákat hozhat létre. | 
||| 

Ha nem rendelkezik Azure-előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/) az eljárás megkezdése előtt. Logikai alkalmazás létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Logikai alkalmazások létrehozása sablonból

1. Ha még nem tette, jelentkezzen be a [az Azure portal](https://portal.azure.com "az Azure portal").

2. Az Azure fő menüjéből válassza az **Erőforrás létrehozása** > **Enterprise Integration** > **Logic App** elemet.

   ![Azure Portal, Új, Vállalati integráció, Logikai alkalmazás](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Hozza létre a logikai alkalmazást a következő kép alatti táblázatban szereplő beállításokkal:

   ![A logikai alkalmazás részleteinek megadása](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Name (Név)** | *a-logikai-alkalmazása-neve* | Adjon meg egy egyedi nevet a logikai alkalmazás számára. | 
   | **Előfizetés** | *az-Ön-Azure-előfizetésének-neve* | Válassza ki a használni kívánt Azure-előfizetést. | 
   | **Erőforráscsoport** | *az-Ön-Azure-erőforráscsoportjának-neve* | Hozzon létre vagy válasszon egy [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) a logikai alkalmazás és az ehhez az alkalmazáshoz kapcsolódó összes erőforrás rendszerezéséhez. | 
   | **Hely** | *az-Ön-Azure-adatközpont-régiója* | Válassza ki az adatközpont-régiót a logikai alkalmazás üzembe helyezéséhez, például az USA nyugati régióját. | 
   | **Log Analytics** | **Ki** (alapértelmezés) vagy **a** | Kapcsolja be a [diagnosztikai naplózás](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) a logikai alkalmazás keresztül [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Megköveteli, hogy Ön már egy Log Analytics-munkaterületet. | 
   |||| 

4. Amikor végzett, válassza a **Rögzítés az irányítópulton** lehetőséget. Így a logikai alkalmazás automatikusan megjelenik az Azure-irányítópulton, és megnyílik az üzembe helyezés után. Válassza a **Létrehozás** elemet.

   > [!NOTE]
   > Ha nem szeretné rögzíteni a logikai alkalmazást, a folytatáshoz manuálisan kell megkeresnie és megnyitnia a logikai alkalmazást az üzembe helyezés után.

   Miután az Azure üzembe helyezte a logikai alkalmazást, megnyílik a Logic Apps Designer, és egy bemutató videót tartalmazó oldalt jelenít meg. 
   A videó alatt megtalálja a gyakori logikaialkalmazás-minták sablonjait. 

5. Görgessen lefelé a bevezető videó és a gyakori eseményindítókon az elmúlt **sablonok**. Válasszon ki egy előre elkészített sablont. Példa:

   ![A logikaialkalmazás-sablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Válassza ki a logikai alkalmazás létrehozása előzmények, **üres logikai alkalmazás**.

   Amikor kiválaszt egy előre elkészített sablont, megtekintheti, hogy a sablon további információt. 
   Példa:

   ![Válasszon ki egy előre elkészített sablont](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. A kiválasztott sablon alapján folytatásához válasszon **ezzel a sablonnal**. 

7. Az összekötők a sablon alapján, a rendszer felszólítja, hajtsa végre az alábbi lépések bármelyikét:

   * Jelentkezzen be hitelesítő adataival, és a sablon által hivatkozott szolgáltatások vagy -rendszerekkel.

   * A szolgáltatások vagy a sablon által hivatkozott rendszerek kapcsolatok létrehozása. Egy kapcsolat létrehozásához adja meg a kapcsolat nevét, és ha szükséges, válassza ki az erőforrást, amelyet használni szeretne. 

   * Ha már beállította ezeket a kapcsolatokat, válassza a **Folytatás**.

   Példa:

   ![Kapcsolatok létrehozása](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Ha elkészült, a logikai alkalmazás megnyílik, és a Logic Apps Designerben megjelennek.

   > [!TIP]
   > Térjen vissza a sablon megjelenítő, kattintson a **sablonok** a Tervező eszköztárán. Ez a művelet elveti a nem mentett módosítások, így egy figyelmeztető üzenet jelenik meg, ellenőrizze a kérelem.

8. Továbbra is összeállítani saját logikai alkalmazását.

   > [!NOTE] 
   > Számos sablon összekötők, előfordulhat, hogy már rendelkezik előre feltöltve kötelező tulajdonságai közé tartozik. Azonban bizonyos sablonok továbbra is szükség lehet, hogy értékeket ad meg a logikai alkalmazás megfelelően telepítése előtt. Ha megpróbál üzembe helyezése nélkül a hiányzó tulajdonság mezőket, hibaüzenetet kap. 

## <a name="update-logic-apps-with-templates"></a>Frissítés a logic apps-sablonokkal

1. Az a [az Azure portal](https://portal.azure.com "az Azure portal"), keresse meg és nyissa meg a logikai alkalmazás th Logikaialkalmazás-Tervező.

2. A Tervező eszköztárán válassza **sablonok**. Ez a művelet elveti a nem mentett módosítások, így egy figyelmeztető üzenet jelenik meg, így ellenőrizheti, hogy szeretné-e továbbra is. Győződjön meg arról, hogy válasszon **OK**. Példa:

   ![Válassza a "Sablon"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Görgessen lefelé a bevezető videó és a gyakori eseményindítókon az elmúlt **sablonok**. Válasszon ki egy előre elkészített sablont. Példa:

   ![A logikaialkalmazás-sablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Amikor kiválaszt egy előre elkészített sablont, megtekintheti, hogy a sablon további információt. 
   Példa:

   ![Válasszon ki egy előre elkészített sablont](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. A kiválasztott sablon alapján folytatásához válasszon **ezzel a sablonnal**. 

5. Az összekötők a sablon alapján, a rendszer felszólítja, hajtsa végre az alábbi lépések bármelyikét:

   * Jelentkezzen be hitelesítő adataival, és a sablon által hivatkozott szolgáltatások vagy -rendszerekkel.

   * A szolgáltatások vagy a sablon által hivatkozott rendszerek kapcsolatok létrehozása. Egy kapcsolat létrehozásához adja meg a kapcsolat nevét, és ha szükséges, válassza ki az erőforrást, amelyet használni szeretne. 

   * Ha már beállította ezeket a kapcsolatokat, válassza a **Folytatás**.

   ![Kapcsolatok létrehozása](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   A logikai alkalmazás most megnyílik, és a Logic Apps Designerben megjelennek.

8. Továbbra is összeállítani saját logikai alkalmazását. 

   > [!TIP]
   > Ha még nem mentette a módosításokat, elvetheti a munkáját, és térjen vissza az előző logikai alkalmazás. A Tervező eszköztárán válassza **elveti**.

> [!NOTE] 
> Számos sablon összekötők, előfordulhat, hogy rendelkezik már előre fel van töltve kötelező tulajdonságai közé tartozik. Azonban bizonyos sablonok továbbra is szükség lehet, hogy értékeket ad meg a logikai alkalmazás megfelelően telepítése előtt. Ha megpróbál üzembe helyezése nélkül a hiányzó tulajdonság mezőket, hibaüzenetet kap.

## <a name="deploy-logic-apps-built-from-templates"></a>A logic apps beépített sablonokból üzembe helyezése

A módosítása után a sablonhoz, mentheti a módosításokat. Ez a művelet automatikusan közzéteszi a logikai alkalmazást.

A tervező eszköztárán válassza a **Mentés** parancsot.

![Mentés és közzététel a logikai alkalmazás](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

Ismerje meg a logikai alkalmazások példák, forgatókönyvek, ügyfelek beszámolói és útmutatók révén.

> [!div class="nextstepaction"]
> [Tekintse át a logikai alkalmazás példák, forgatókönyvek és útmutatók](../logic-apps/logic-apps-examples-and-scenarios.md)