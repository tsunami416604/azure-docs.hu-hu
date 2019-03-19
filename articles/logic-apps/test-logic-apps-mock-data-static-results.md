---
title: A logic apps utánzatként funkcionáló adatokkal – Azure Logic Apps tesztelése
description: Állítsa be a statikus eredmények próbaadatokat rendelkező logikai alkalmazások működésének megzavarása nélkül megtesztelheti az éles környezetben való teszteléshez
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 03/18/2019
ms.openlocfilehash: 0fbe56ceeeba71bcbb5ef358cd66de15e36508fc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165096"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Statikus eredmények beállításával próbaadatokat rendelkező logikai alkalmazások tesztelése

Ha a logic apps teszteli, nem feltétlenül készen áll a ténylegesen hívás és az alkalmazások, szolgáltatások és rendszerek el különböző okok miatt. Általában ezekben az esetekben előfordulhat, hogy kell futtatni különböző feltétel elérési utak, kényszerítheti a hibák, adja meg az adott válasz szövegtörzsében vagy meg sem próbál kihagyása néhány lépést. A logikai alkalmazás a művelet eredményei statikus beállításával, a művelet a kimeneti adatokat is utánzása. Statikus eredmények művelet engedélyezése a művelet nem fut le, de ehelyett utánzatként funkcionáló adatait adja vissza.

Például ha úgy állítja be az Outlook 365-höz készült statikus eredmények küldése e-mail-művelet, a Logic Apps-motor csak és adja vissza a megadott statikus eredményként utánzatként funkcionáló adatok helyett hívja az Outlook e-mail küldése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazást, ha szeretne statikus eredmények beállítása

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Állítsa be a statikus eredmények

1. Ha még nem tette, az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic Apps Designerben.

1. A művelet, ha szeretne statikus eredmények beállítása kövesse az alábbi lépéseket: 

   1. A művelet jobb felső sarokban, válassza a három pontra (*...* ) gombra, és válassza **statikus eredmény**, például:

      ![Válassza ki a "Statikus eredménye" > "Statikus eredmény engedélyezése"](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Válasszon **statikus eredmény engedélyezése**. A szükséges (*) tulajdonságait adja meg a be szeretné olvasni a műveleti válasz utánzatként funkcionáló kimeneti értékeket.

      Ha például az alábbiakban a HTTP-művelet a szükséges tulajdonságokat:

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Állapot** | Vissza a művelet állapota |
      | **Állapotkód** | A konkrét kódot ad vissza |
      | **Fejlécek** | A visszaadandó fejléctartalom |
      |||

      ![Válassza a "Statikus eredmény engedélyezése"](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Adja meg a utánzatként funkcionáló adatok JavaScript Object Notation (JSON) formátumú, válassza a **váltson át a JSON üzemmódot** (![válassza "A JSON üzemmód átkapcsolása"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Nem kötelező tulajdonságai között nyissa meg a **válassza ki a kötelező mezők** listában, és jelölje ki a utánzása kívánt tulajdonságokat.

      ![Nem kötelező tulajdonságok kiválasztása](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Amikor elkészült, mentse, válassza ki a **kész**.

   A művelet jobb felső sarokban a címsor most már a teszt főzőpohárba ikon látható (![ikonra a statikus eredmények](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), ami azt jelenti, hogy engedélyezte-e statikus eredményeket.

   ![Ábrázoló ikon engedélyezve van a statikus eredmények](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Korábbi futtatásokból próbaadatokat használó talál [statikus eredményeket használó futtatások keresése](#find-runs-mock-data) jelen témakör későbbi részében.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Újból felhasználhatja az előző kimenetek

Ha a logikai alkalmazás egy korábbi futtassa a kimenetek utánzatként funkcionáló kimenetként felhasználhatja is másolása és beillesztése futtató kimenetei.

1. Ha még nem tette, az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic Apps Designerben.

1. A logikai alkalmazás fő menüjéből válassza **áttekintése**.

1. Az a **futtatási előzmények** szakaszban jelölje be a logikai alkalmazás futtatási Ön szeretné.

1. A logikai alkalmazás munkafolyamat keresse meg és bontsa ki a művelet, amely rendelkezik a kimenetei.

1. Válassza ki a **nyers kimenetek megjelenítése** hivatkozásra.

1. Másolja a teljes JavaScript Object Notation (JSON) objektum vagy a megadott részt szeretne használni, például, a kimeneti szakasz, vagy csak a fejlécek szakaszban.

1. Kövesse a lépéseket a nyitó a **statikus eredmény** a művelet be [állítsa be a statikus eredmények](#set-up-static-results).

1. Miután a **statikus eredmény** megnyílik mezőben válassza ki bármelyik. lépés:

   * Illessze be a teljes JSON-objektum, válassza a **váltson át a JSON üzemmódot** (![válassza "A JSON üzemmód átkapcsolása"](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Válassza ki a "Kapcsoló a JSON üzemmód" teljes objektum](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Illessze be csak egy JSON szakaszban mellett a szakasz címke, válassza a **váltson át a JSON üzemmódot** a részt, például:

     ![Válassza ki a "Kapcsoló a JSON üzemmód" a kimenetek](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. A JSON-szerkesztő illessze be a korábban másolt JSON.

   ![JSON-mód](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Ha elkészült, válassza a **Kész** lehetőséget. Vagy, térjen vissza a tervezőben, válassza a **kapcsoló szerkesztő módra** (![válassza "A szerkesztő mód váltása"](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Statikus eredményeket használó futtatások keresése

A logikai alkalmazás futtatási előzmények azonosítja azokat a futtatásokat, ahol a műveletek használata a statikus eredményeket. Ezek a futtatások megkereséséhez kövesse az alábbi lépéseket:

1. A logikai alkalmazás fő menüjéből válassza **áttekintése**. 

1. A jobb oldali ablaktáblában alatt **futtatási előzmények**, keresse meg a **statikus eredmények** oszlop. 

   Bármely, amely tartalmazza az eredmények műveletek futtatásához tartozik a **statikus eredmények** oszlopban **engedélyezve**, például:

   ![Előzmények – statikus eredmények oszlop](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Műveletek által használt statikus eredmények megtekintéséhez válassza a Futtatás, hol szeretné a **statikus eredmények** oszlop értéke **engedélyezve**.

   Műveletek által használt statikus eredmények megjelenítése a teszt főzőpoharat (![ikonra a statikus eredmények](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) ikonra, például:

   ![Futtatási előzmények - műveletek által használt statikus eredmények](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Tiltsa le a statikus eredmények

Statikus eredmények kikapcsolása nem dobja az értékek az utolsó telepítőfájlból. Így bekapcsolja a statikus eredmények a következő alkalommal, amikor is a korábbi értékeinek használatával.

1. A művelet található, ahol szeretné letiltani a statikus kimenetek. A művelet jobb felső sarokban, válassza ki a teszt főzőpohárba ikon (![ikonra a statikus eredmények](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Tiltsa le a statikus eredmények](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Válasszon **tiltsa le a statikus eredmény** > **kész**.

   ![Tiltsa le a statikus eredmények](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Logic Apps](../logic-apps/logic-apps-overview.md)