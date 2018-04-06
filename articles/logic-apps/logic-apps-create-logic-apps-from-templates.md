---
title: Munkafolyamatok létrehozása sablonból - Azure Logic Apps |} Microsoft Docs
description: Készíthetnek munkafolyamatokat gyorsabb logic app-sablonok segítségével
author: kevinlam1
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; klam
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a2e939143d7181722df17ea7837658d96272e3a3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Munkafolyamatokat logic app előre elkészített sablonok alapján

Az első lépésekhez, hogy gyorsabban figyeléséből, Logic Apps sablonok, amelyek gyakran használt minták az alábbi előre elkészített logic Apps biztosít. Használja ezeket a sablonokat, meghatározott, vagy szerkesztheti azokat a forgatókönyvnek megfelelően.

Az alábbiakban néhány sablon kategóriák:

| Sablon típusa | Leírás | 
| ------------- | ----------- | 
| Vállalati felhő sablonok | Használható az Azure Blob, a Dynamics CRM-hez, a Salesforce, mezőbe, és más összekötők tartalmazza a vállalati felhő kell. Ezek a sablonok segítségével például érdeklődők rendszerezése vagy készítsen biztonsági másolatot az vállalati adatokat. | 
| Egyéni hatékonyságnövelő sablonok | Személyes termelékenység növelése úgy, hogy napi emlékeztetők, feladatlistájukban, és egy egyetlen felhasználói jóváhagyás lépés automatizálása hosszadalmas feladatcsoporton munkaelemek bekapcsolása esetén fontos. | 
| Fogyasztó felhő sablonok | A közösségi szolgáltatások, például a Twitter, a tartalékidő integrálása és e-mailt. Akkor hasznos, ha a közösségi média kezdeményezések marketing megerősítése. Ezeket a sablonokat is feladatokat, mint a felhő másolja, amely növeli a hatékonyságot azáltal, hogy így időt takarít meg a hagyományosan ismétlődő feladatok. | 
| Vállalati integrációs felügyeleticsomag-sablonok | VETER konfigurálásához (ellenőrzése, kinyerési, átalakítási, kiegészítése, útvonal-) folyamatok, a fogadó egy X12 EDI-dokumentum AS2 és XML-átalakítás és X12, EDIFACT, kezelése és AS2-üzeneteket. | 
| Protokoll mintát sablonok | Például a kérés-válasz protokoll minták megvalósításához HTTP és a Integrációk keresztüli FTP és az SFTP. Megadott használja ezeket a sablonokat, vagy összetett protokoll mintára használni ezeket. | 
||| 

Ha nem rendelkezik Azure-előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/) az eljárás megkezdése előtt. Logikai alkalmazás létrehozásával kapcsolatos további információkért lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>A logic apps létrehozása sablonból

1. Ha még nem tette meg, jelentkezzen be a [Azure-portálon](https://portal.azure.com "Azure-portálon").

2. Az Azure fő menüjéből válassza az **Erőforrás létrehozása** > **Enterprise Integration** > **Logic App** elemet.

   ![Azure Portal, Új, Vállalati integráció, Logikai alkalmazás](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Hozza létre a logikai alkalmazást a következő kép alatti táblázatban szereplő beállításokkal:

   ![A logikai alkalmazás részleteinek megadása](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Name (Név)** | *a-logikai-alkalmazása-neve* | Adjon meg egy egyedi nevet a logikai alkalmazás számára. | 
   | **Előfizetés** | *az-Ön-Azure-előfizetésének-neve* | Válassza ki a használni kívánt Azure-előfizetést. | 
   | **Erőforráscsoport** | *az-Ön-Azure-erőforráscsoportjának-neve* | Hozzon létre vagy válasszon egy [Azure erőforráscsoport](../azure-resource-manager/resource-group-overview.md) logikai alkalmazás és az alkalmazáshoz tartozó összes erőforrást rendezése. | 
   | **Hely** | *az-Ön-Azure-adatközpont-régiója* | Válassza ki az adatközpont-régiót a logikai alkalmazás üzembe helyezéséhez, például az USA nyugati régióját. | 
   | **Log Analytics** | **Ki** (alapértelmezés) vagy **a** | Kapcsolja be a [diagnosztikai naplózás](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) a logikai alkalmazásnak keresztül [Azure Naplóelemzés](../log-analytics/log-analytics-overview.md). Megköveteli, hogy Ön már a Naplóelemzési munkaterület. | 
   |||| 

4. Amikor végzett, válassza a **Rögzítés az irányítópulton** lehetőséget. Így a logikai alkalmazás automatikusan megjelenik az Azure-irányítópulton, és megnyílik az üzembe helyezés után. Válassza a **Létrehozás** elemet.

   > [!NOTE]
   > Ha nem szeretné rögzíteni a logikai alkalmazást, a folytatáshoz manuálisan kell megkeresnie és megnyitnia a logikai alkalmazást az üzembe helyezés után.

   Miután az Azure üzembe helyezte a logikai alkalmazást, megnyílik a Logic Apps Designer, és egy bemutató videót tartalmazó oldalt jelenít meg. 
   A videó alatt megtalálja a gyakori logikaialkalmazás-minták sablonjait. 

5. A bevezető videó és közös eseményindítók túli görgetési **sablonok**. Válasszon egy előre elkészített sablont. Példa:

   ![A logic app-sablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > A Logic Apps alkalmazást teljesen új létrehozásához válassza a **üres logikai alkalmazás**.

   Amikor kiválaszt egy előre elkészített sablont, megtekintheti, hogy a sablon további információt. 
   Példa:

   ![Válasszon egy előre elkészített sablon](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. A kiválasztott sablont folytatásához válasszon **ezzel a sablonnal**. 

7. Az összekötők a sablon alapján, kéri hajtsa végre ezeket a lépéseket:

   * Jelentkezzen be a rendszer vagy a sablon által hivatkozott szolgáltatások hitelesítő adatait.

   * Szolgáltatások és a sablon által hivatkozott rendszerek kapcsolatok létrehozása. VPN-kapcsolat létrehozásához adja meg a kapcsolat nevét, és ha szükséges, válassza ki a használni kívánt erőforrás. 

   * Ha korábban már beállított ezeket a kapcsolatokat, válassza a **Folytatás**.

   Példa:

   ![Kapcsolatok létrehozása](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Amikor elkészült, a logikai alkalmazás nyílik meg és a Logic Apps-tervezőben.

   > [!TIP]
   > Válassza ki a sablon megjelenítő visszaállításához **sablonok** a designer eszköztáron. Ez a művelet elveti a nem mentett módosításokat, így egy figyelmeztető üzenet jelenik meg a kérést a.

8. Továbbra is összeállítani saját logikai alkalmazását.

   > [!NOTE] 
   > Sok-sablonjai tartalmazzák az összekötőt, előfordulhat, hogy már rendelkezik előre feltöltve szükséges tulajdonságai. Azonban néhány sablonok előfordulhat, hogy továbbra is meg kell adnia értéket a logikai alkalmazás megfelelően telepítése előtt. Ha kísérli meg telepíteni a hiányzó tulajdonság mezőket befejeződés nélkül, hibaüzenetet kap. 

## <a name="update-logic-apps-with-templates"></a>A logic apps-sablonok frissítése

1. Az a [Azure-portálon](https://portal.azure.com "Azure-portálon"), található, és nyissa meg a Logic Apps alkalmazást th Logic App Tervező.

2. A Tervező eszköztáron válassza **sablonok**. Ez a művelet elveti a nem mentett módosításokat, így ellenőrizheti, hogy szeretné-e továbbra is megjelenik egy figyelmeztető üzenet. Győződjön meg arról, hogy válasszon **OK**. Példa:

   ![Válassza ki a "Sablon"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. A bevezető videó és közös eseményindítók túli görgetési **sablonok**. Válasszon egy előre elkészített sablont. Példa:

   ![A logic app-sablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Amikor kiválaszt egy előre elkészített sablont, megtekintheti, hogy a sablon további információt. 
   Példa:

   ![Válasszon egy előre elkészített sablon](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. A kiválasztott sablont folytatásához válasszon **ezzel a sablonnal**. 

5. Az összekötők a sablon alapján, kéri hajtsa végre ezeket a lépéseket:

   * Jelentkezzen be a rendszer vagy a sablon által hivatkozott szolgáltatások hitelesítő adatait.

   * Szolgáltatások és a sablon által hivatkozott rendszerek kapcsolatok létrehozása. VPN-kapcsolat létrehozásához adja meg a kapcsolat nevét, és ha szükséges, válassza ki a használni kívánt erőforrás. 

   * Ha korábban már beállított ezeket a kapcsolatokat, válassza a **Folytatás**.

   ![Kapcsolatok létrehozása](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   A Logic Apps alkalmazást most nyílik meg és a Logic Apps-tervezőben.

8. Továbbra is összeállítani saját logikai alkalmazását. 

   > [!TIP]
   > Ha még nem mentette a módosításokat, elveti a munkáját, és térjen vissza a korábbi Logic Apps alkalmazást. A Tervező eszköztáron válassza **elvetése**.

> [!NOTE] 
> Sok-sablonjai tartalmazzák az összekötőt, előfordulhat, hogy rendelkezik már előre feltöltve szükséges tulajdonságai. Azonban néhány sablonok előfordulhat, hogy továbbra is meg kell adnia értéket a logikai alkalmazás megfelelően telepítése előtt. Ha kísérli meg telepíteni a hiányzó tulajdonság mezőket befejeződés nélkül, hibaüzenetet kap.

## <a name="deploy-logic-apps-built-from-templates"></a>Sablonból létrehozott logikai alkalmazások központi telepítése

Miután a módosításokat a sablont, mentheti a módosításokat. Ez a művelet automatikusan közzéteszi a Logic Apps alkalmazást.

A tervező eszköztárán válassza a **Mentés** parancsot.

![Mentse és a logikai alkalmazás közzététele](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

Ismerje meg a logic apps segítségével példák, a konfigurációkat, az ügyfél-szövegek és a forgatókönyvek létrehozása.

> [!div class="nextstepaction"]
> [Tekintse át a logic app példák forgatókönyvek és forgatókönyvek](../logic-apps/logic-apps-examples-and-scenarios.md)