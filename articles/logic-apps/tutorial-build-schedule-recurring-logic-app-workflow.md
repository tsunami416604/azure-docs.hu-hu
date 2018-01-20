---
title: "A Feladatütemező-alapú automatizált munkafolyamatok - Azure Logic Apps alkalmazásokat készíthet |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan scheduler-alapú, ismétlődő, automatikus munkafolyamat létrehozása az Azure Logic Apps"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.author: LADocs; estfan
ms.openlocfilehash: deb2572de363ca5d0dec0f78f2e30ad648e9b5f8
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="check-traffic-with-a-scheduler-based-logic-app"></a>A Feladatütemező-alapú logikai alkalmazás forgalom ellenőrzése

Az Azure Logic Apps segítségével automatizálhatja az ütemezés szerint futtatott munkafolyamatok. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy [logikai alkalmazás](../logic-apps/logic-apps-overview.md) az ütemező eseményindító, amely a hét napja reggelente, és ellenőrzi a utazás idő, beleértve a forgalom, két között helyezi. Ha idő elér egy adott korlátot, a logikai alkalmazást e-mailt küld a utazás idő-és a felesleges idő szükséges a cél.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Üres logikai alkalmazás létrehozása. 
> * Adja hozzá egy eseményindító, amely a Feladatütemező a logikai alkalmazásnak.
> * Egy műveletet, amely lekérdezi a utazás idő útvonal hozzáadása.
> * Új változót hoz létre, a utazás idő másodpercben a perc alakítja és eredményező takarít meg a változó művelet.
> * Adjon hozzá egy feltételt, amely összehasonlítja a utazás idő, szemben a megadott korlátnál.
> * Adja hozzá az e-mailt küld, ha a utazás ideje túllépi ezt a műveletet.

Amikor elkészült, a logikai alkalmazás néz ki a munkafolyamat magas szinten:

![Magas szintű logikai alkalmazást](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztrálhat egy ingyenes Azure-fiókra</a> az eljárás megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az e-mail-fiók egy e-mailt provider Logic Apps, például az Office 365 Outlook, Outlook.com-os vagy Gmailes által támogatott. A más szolgáltatók [tekintse át az összekötők listán Itt](https://docs.microsoft.com/connectors/). A gyors üzembe helyezés egy Outlook.com-os fiókot használja. Ha egy másik e-mail fiókot használja, az általános lépéseket ugyanaz maradjon, de a felhasználói felület némileg eltérő jelenhet meg.

* Ahhoz, hogy az útvonal utazás időpontját, a Bing térképek API szükség van egy hozzáférési kulcsot. Ahhoz, hogy ezt a kulcsot, kövesse a lépéseket <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">a Bing Maps kulcs beszerzése</a>. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a> a Azure-fiók hitelesítő adataival.

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Az Azure fő menüjéből válassza az **Új** > **Enterprise Integration** > **Logic App** elemet.

   ![Logikai alkalmazás létrehozása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app.png)

2. A **hozzon létre logikai alkalmazás**, ez a logikai alkalmazás látható és ismertetett ismertetik. Ha elkészült, válassza a **Rögzítés az irányítópulton** > **Létrehozás** lehetőséget.

   ![Adja meg a logic app információk](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Name (Név)** | LA-TravelTime | A logikai alkalmazás nevét | 
   | **Előfizetés** | <*your-Azure-subscription-name*> | Az Azure-előfizetés nevét | 
   | **Erőforráscsoport** | LA-TravelTime-RG | A nevet a [Azure erőforráscsoport](../azure-resource-manager/resource-group-overview.md) használatával rendszerezhetők kapcsolódó erőforrások | 
   | **Hely** | USA 2. keleti régiója | A régió a Logic Apps alkalmazást kapcsolatos információk tárolására szolgáló | 
   | **Log Analytics** | Ki | Tartsa a **ki** diagnosztikai naplózás beállítása. | 
   |||| 

3. Miután Azure telepíti az alkalmazást, a Logic Apps Designer megnyílik, és egy bevezető videó és sablonok közös logic app mintákat egy lapját jeleníti meg. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Üres logikaialkalmazás-sablon kiválasztása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/choose-logic-app-template.png)

Ezután adja hozzá az ismétlődési [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), mely akkor következik be, a megadott ütemezés szerint. Minden logikai alkalmazás egy eseményindítót, amely akkor fordul elő, amikor egy adott esemény következik be, vagy amikor új adatok megfelel-e egy adott feltétel kell kezdődnie. További információkért lásd: [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-scheduler-trigger"></a>A Feladatütemező eseményindító hozzáadása

1. Adja meg a Tervező be a keresőmezőbe "ismétlődési". Válassza ki az ehhez az eseményindítóhoz: **ütemezés - ismétlődési**

   ![Keresse meg és vegye fel a "Ismétlődés ütemezése" eseményindító](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

2. Az a **ismétlődési** alakul, válassza ki a **folytatást jelző pontokra** (**...** ) gombra, és válassza a **átnevezése**. Nevezze át a leírást az eseményindító:```Check travel time every weekday morning```

   ![Nevezze át az eseményindító](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

3. Eseményindító, belső válasszon **speciális beállítások megjelenítése**.

4. Adja meg az ütemezés és ismétlődési adatait az eseményindító látható és ismertetett:

   ![Az ütemezés és ismétlődési részleteinek megadása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/schedule-recurrence-trigger-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Időköz** | 1 | Ellenőrzések között intervallumok száma | 
   | **Gyakoriság** | Hét | A használatára az ismétlődés egysége | 
   | **Időzóna** | Nincs | Csak akkor, ha megadja a kezdő időpont vonatkozik. Akkor hasznos, ha nem-helyi időzónát. | 
   | **Kezdési idő** | Nincs | Késleltetés az ismétlődést, amíg egy adott dátumot és időpontot. További információkért lásd: [feladatok és a munkafolyamatok, amelyek rendszeres futtatásának ütemezése](../connectors/connectors-native-recurrence.md). | 
   | **Napjainkban a** | Hétfő, kedd, szerda, csütörtök, péntek | Csak akkor, ha elérhető **gyakoriság** "Hetente" értékre van állítva | 
   | **Ezek az órák:** | 7,8,9 | Csak akkor, ha elérhető **gyakoriság** "Hét" vagy "Day" értékre van beállítva. Válassza ki az óra, nap az ismétlődés futtatásához. Ebben a példában a 7, 8 és 9 órás jelek lefut. | 
   | **Ezek perccel** | 0,15,30,45 | Csak akkor, ha elérhető **gyakoriság** "Hét" vagy "Day" értékre van beállítva. Válassza ki a perc, a nap az ismétlődés futtatásához. Ebben a példában a nulla órás jelre kezdődő 15 percenként fut. | 
   ||||

   Ehhez az eseményindítóhoz akkor következik be, minden hétköznap, 15 percenként, 7:00 órakor kezdő és záró dátum 9 óra 45 Perckor. 
   A **előzetes** mező az ismétlődés ütemezésének jeleníti meg. 
   További információkért lásd: [ütemezése, feladatok és a munkafolyamatok](../connectors/connectors-native-recurrence.md) és [munkafolyamat-műveleteket és eseményindítók](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

5. Az eseményindító részletek most elrejtéséhez kattintson az alakzat címsorában belül.

   ![Részletek elrejtéséhez alakzat összecsukása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

6. Mentse a Logic Apps alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot. 

A Logic Apps alkalmazást most élő, de nem végez semmilyen más ismétlődés. Igen új művelet, amely válaszol, amikor az eseményindító következik be.

## <a name="get-the-travel-time-for-a-route"></a>Az útvonal utazás időpont kérdezhető le

Most, hogy egy eseményindítót, adja hozzá egy [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) utazás idő, amely lekérdezi a két hely között. A Logic Apps, hogy könnyen kaphat ezeket az információkat biztosít a Bing térképek API összekötőt. Ez a feladat megkezdése előtt győződjön meg arról, hogy rendelkezik a Bing térképek API-kulcs, ez az oktatóanyag Előfeltételek leírtak szerint.

1. Válassza a Logic App az eseményindító Designer **+ új lépés** > **művelet hozzáadása**.

2. "A maps", és adja meg a műveletet: **Bing Maps - Get-útvonal**

3. Ha nem rendelkezik a Bing Maps kapcsolat, megkérdezi, VPN-kapcsolat létrehozásához. Adja meg a kapcsolat adatait, és válassza a **létrehozása**.

   ![Válassza ki a "Bing Maps - útvonal Get" művelet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Beállítás | Érték | Leírás |
   | ------- | ----- | ----------- |
   | **Kapcsolat neve** | BingMapsConnection | Adjon meg egy nevet a kapcsolathoz. | 
   | **API-kulcs** | <*your-Bing-Maps-key*> | Adja meg a korábban a Bing Maps termékkulcsot. Ha nem rendelkezik a Bing Maps kulccsal, <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">egy kulcs beszerzése</a>. | 
   | | | |  

4. Nevezze át a műveletet. Ez a leírás a:```Get route and travel time with traffic```

5. Adja meg az adatokat a **Get útvonal** művelet látható, és itt, például:

   ![Adja meg a "Bing Maps - Get-útvonal" információkat művelet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Beállítás | Érték | Leírás |
   | ------- | ----- | ----------- |
   | **Waypoint 1** | <*Start-helye*> | Az útvonal forrása | 
   | **Waypoint 2** | <*Záró-helye*> | Az útvonal-cél | 
   | **Avoid** | None | Elkerülése érdekében a útvonalon, például autópályákat, autópályadíjak, és egyéb elemeket | 
   | **Optimize** | timeWithTraffic | Egy paraméter optimalizálja a útvonal távolság, például idő az aktuális forgalommal utazik, és így tovább. Válassza ki a paraméter: "timeWithTraffic" | 
   | **Távolság egység** | <*your-preference*> | Az útvonal távolság egység. Ez a cikk ezt egységet használ: "Mérföldes"  | 
   | **Utazó mód** | Befolyásoló tényezők | Az útvonal utazó mód. Válassza ezt a módot: "Befolyásoló tényezők" | 
   | **Átvitel dátuma és időpontja** | Nincs | Átvitel mód csak a következőkre vonatkozik | 
   | **Átvitel dátuma-típusának** | Nincs | Átvitel mód csak a következőkre vonatkozik | 
   |||| 

   Ezek a paraméterek kapcsolatos további információkért lásd: [útvonal kiszámításához](https://msdn.microsoft.com/library/ff701717.aspx).

6. Mentse a Logic Apps alkalmazást.

Következő lépésként hozzon létre egy változót, hogy alakítsa át és tárolására is használható az aktuális utazás idő másodpercben, hanem perc. Ily módon lehet az átalakítás ismétlődésének elkerülése és használni a érték könnyebben szakaszt követő lépésekben olvashatja. 

## <a name="create-variable-to-store-travel-time"></a>Utazás időtartam változó létrehozása

Egyes esetekben érdemes a munkafolyamat adatok műveleteket, és az eredmények használata a későbbi műveletek. Mentse az ezekkel az eredményekkel, így könnyen ismét felhasználni, vagy hivatkozzon őket, az eredmények tárolásához őket feldolgozása után változók is létrehozhat. Csak a legfelső szinten a Logic Apps alkalmazást változókat hozhat létre.

Alapértelmezés szerint az előző **Get útvonal** művelet adja vissza a jelenlegi utazás adatforgalommal és a **utazik időtartama forgalom** mező. Konvertálása, és ez az érték tárolása perc helyett, akkor könnyebben értéke később újra felhasználhatja anélkül konvertálása újra.

1. Az a **Get útvonal** művelet, válassza a **+ új lépés** > **művelet hozzáadása**.

2. "Változó", és adja meg a műveletet: **változók - inicializálási változó**

   ![Válassza ki a "Változók - inicializálási változó" művelet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

3. Nevezze át ezt a műveletet, a Leírás:```Create variable to store travel time```

4. Adja meg a részleteket a változó itt leírt módon:

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Name (Név)** | travelTime | A változó nevét | 
   | **Típus** | Egész szám | A változó adatok típusa | 
   | **Érték** | Egy kifejezés, amely az aktuális utazás idő másodpercben a perc (lásd a táblázat alatti lépéseket) alakítja. | A kezdeti érték, a változó | 
   |||| 

   1. A kifejezésben létrehozásához a **érték** , majd kattintson a mezőbe, hogy a dinamikus tartalom listába jelenik meg. 
   Szükség esetén kiszélesíteni a böngésző, amíg megjelenik a listában. 
   Válassza ki a dinamikus tartalom listába **kifejezés**. 

      ![Adja meg az "Változók - inicializálási változó" információkat művelet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      Belül néhány kattintást szerkesztése jelölőnégyzetéből, vagy a dinamikus tartalom listáját, vagy a lista egy beágyazott paraméter jelenik meg. Ebben a listában láthatók az előző műveleteket a munkafolyamat bemeneteként használható paramétereket. 
      A dinamikus tartalom listába kifejezés szerkesztővé rendelkezik, ahol kiválaszthatja a funkciók a műveletek végrehajtásához. 
      A kifejezés szerkesztő csak dinamikus tartalom listájában jelenik meg.

      A böngésző szélesség határozza meg, melyik listája jelenik meg. 
      Ha a böngésző széles, a dinamikus tartalom listába jelenik meg. 
      Ha a böngésző keskeny, a paraméterlista beágyazott a szerkesztőmező aktuális alatt jelenik meg.

   2. Adja meg a kifejezés-szerkesztő, ebben a kifejezésben:```div(,60)```

      ![Adja meg a kifejezés: "div(,60)"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   3. Helyezze a kurzort a kifejezés a bal oldali kerek zárójelet tartalmazhat közötti belül (**(**) és a vessző (**,**). 
   Válasszon **dinamikus tartalom**.

      ![Vigye a kurzort, válassza a "Dinamikus tartalom"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   4. A dinamikus tartalom listában válassza ki a **utazás időtartama forgalom**.

      ![Jelölje ki "Utazás időtartama forgalom" mezőt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   5. Miután a mező megoldja belül a kifejezést, válassza ki azt **OK**.

      ![Válassza az "OK"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      A **érték** mező megjelenik itt látható módon:

      !["Érték" mezőben a feloldott kifejezéssel](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

5. Mentse a Logic Apps alkalmazást.

Ezután adja hozzá az olyan feltétel, amely ellenőrzi, hogy az aktuális utazás idő nagyobb, mint a megadott határértéket.

## <a name="compare-travel-time-with-limit"></a>Hasonlítsa össze a korlát utazás idő

1. Válassza ki az előző művelet **+ új lépés** > **feltétel hozzáadása**. 

2. Nevezze át a leírást a feltételei:```If travel time exceeds limit```

3. Build olyan feltétel, amely ellenőrzi, hogy **travelTime** meghaladja a megadott leírt és látható itt:

   1. Belül a feltétellel, kattintson a **adjon meg értéket** csoportban, amely a bal oldali (széles böngésző nézet) vagy (keskeny böngésző nézet) felül.

   2. A dinamikus tartalom listába vagy a paraméterlista, válassza ki a **travelTime** eleménél **változók**.

   3. Összehasonlítás, válassza az ezzel a művelettel: **nagyobb, mint**

   4. Az a **adjon meg értéket** jobb (széles nézet) vagy alsó (keskeny nézet), a szövegmezőbe írja be ezt a korlátot:```15```

   Például ha keskeny nézetben dolgozik, ez hogyan hoz létre, hogy ez a feltétel:

   ![Keskeny nézetben feltétel létrehozása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time-narrow.png)

4. Mentse a Logic Apps alkalmazást.

Ezután adja hozzá a műveletet kell végrehajtani, ha a utazás ideje túllépi a határértéket.

## <a name="send-email-when-limit-exceeded"></a>E-mail küldése, amikor korlátja túllépve

Ezután adja hozzá egy műveletet, amely e-mailt küld, ha a utazás ideje túllépi a határértéket. Az e-mail tartalmazza az aktuális utazás és a további szükséges, hogy a megadott útvonal. 

1. A feltételben **igaz értéke esetén** fiókirodai, válassza a **művelet hozzáadása**.

2. "E-mail küldése", és adja meg az e-mailek összekötő és a "küldési e-mail művelet", amely a használni kívánt.

   ![Keresse meg és jelölje ki a "e-mail küldési" művelet](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

   * Személyes Microsoft-fiók, válassza ki a **Outlook.com-os**. 
   * Az Azure-bA a munkahelyi vagy iskolai fiókok, jelölje be **Office 365 Outlook**.

3. Ha még nem rendelkezik olyan kapcsolat, megkérdezi, jelentkezzen be az e-mail fiókjába.

   A Logic Apps létrehoz egy kapcsolatot az e-mail-fiókjával.

4. Nevezze át a műveletet. Ez a leírás a:```Send email with travel time```

5. A **Címzett** mezőben adja meg a címzett e-mail-címét. Tesztelési célokra, az e-mail címét használja.

6. Az a **tulajdonos** mezőben adja meg az e-mail tárgyát, és tartalmazzák a **travelTime** változó.

   1. Adja meg a szöveget ```Current travel time (minutes): ``` végződhet szóközzel. 
   
   2. Válassza ki a paraméterlista vagy a dinamikus tartalom listába, azt **travelTime** alatt **változók**. 
   
      Ha például a böngésző keskeny nézetben van:

      ![Adja meg a tulajdonos szöveg és a utazás idő visszaadó kifejezés](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-subject-settings.png)

7. Az a **törzs** adja meg az e-mail törzsének tartalmát. 

   1. Adja meg a szöveget ```Add extra travel time (minutes): ``` végződhet szóközzel. 
   
   2. Ha szükséges, kiszélesíteni a böngésző csak akkor jelenik meg, a dinamikus tartalom listába. 
   Válassza ki a dinamikus tartalom listába **kifejezés**.

      ![Az e-mail törzsének kifejezésének összeállítása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   3. Kifejezés-szerkesztőben, hogy kiszámíthatja a korláton percek számát adja meg az ebben a kifejezésben:```sub(,15)```

      ![Adja meg a kifejezés kiszámításához percig utazik idő](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   4. Helyezze a kurzort a kifejezés a bal oldali kerek zárójelet tartalmazhat közötti belül (**(**) és a vessző (**,**). Válasszon **dinamikus tartalom**.

      ![Továbbra is percig utazik idő kiszámításához kifejezés összeállítása](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   5. A **változók**, jelölje be **travelTime**.

      ![Válassza ki a kifejezés "travelTime" mezőt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   6. Miután a mező megoldja belül a kifejezést, válassza ki azt **OK**.

      ![Megoldott kifejezésben a "Törzs" mezőt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      A **törzs** mező megjelenik itt látható módon:

      ![Megoldott kifejezésben a "Törzs" mezőt](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

8. Mentse a Logic Apps alkalmazást.

Ezt követően tesztelje a Logic Apps alkalmazást, amely most hasonlít-e ebben a példában:

![Befejezett logikai alkalmazás](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás** elemet. Ha az aktuális idő marad a korlátja alatt változatlan marad, a Logic Apps alkalmazást nincs semmi hatása más, és várjon a következő intervallum, mielőtt az újra.
De ha az aktuális utazás idő meghaladja a korlátot, meghaladja a kap egy e-mailt az aktuális utazás idő-és a percek számát. Íme egy példa e-mailt, amelyet a logikai alkalmazás küld:

![E-mailek utazás ideje nem jut](./media/tutorial-build-scheduled-recurring-logic-app-workflow/email-notification.png)

Ha nem minden e-mailek, ellenőrizze az e-maileket Levélszemét mappát. Az e-mailek szűrőt átirányítási előfordulhat, hogy az ilyen típusú e-mailt. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, mostantól elkészítette és ismétlődő logika ütemezés-alapú alkalmazás futtatására. 

Egyéb használó logikai alkalmazás létrehozása a **ütemezés - ismétlődési** indítható el, tekintse meg ezeket a sablonokat, amelyek akkor után rendelkezésre álló logikai alkalmazás létrehozása:

* Napi emlékeztetők küldött beolvasása.
* Törölje a régebbi Azure-blobokat.
* Adja hozzá az Azure Storage üzenetsorába üzenetet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor már nincs szükség, a csoport, amely tartalmazza a logikai alkalmazás és a kapcsolódó erőforrások törléséhez. Lépjen az Azure főmenü **erőforráscsoportok**, és válassza ki az erőforrás a Logic Apps alkalmazást. Válasszon **erőforrás csoport törlése**. Adja meg az erőforráscsoport neve megerősítő, és válassza a **törlése**.

!["Overview" > "Delete erőforráscsoport"](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott logikai alkalmazás, amely a forgalom a (a hét napja reggelente) megadott ütemezés szerint ellenőrzi, és tart a művelet (küld e-mailben) Ha a utazás ideje meghaladja a megadott korlátnál. Most megtudhatja, hogyan hozhat létre egy logikai alkalmazás által a levelezési lista kérelmek jóváhagyásra integrálja az Azure-szolgáltatásokhoz, Microsoft-szolgáltatások és egyéb SaaS-alkalmazásokhoz.

> [!div class="nextstepaction"]
> [Levelezési lista vonatkozó kérések kezelése](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)