---
title: "Virtuális gép módosítások - Azure esemény rács & Logic Apps figyelése |} Microsoft Docs"
description: "Ellenőrizze a virtuális gépek (VM) konfigurációs változásokat Azure esemény rács és a Logic Apps segítségével"
keywords: "a Logic apps, az esemény rácsok, a virtuális gép, Virtuálisgép"
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: 
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: article
ms.date: 11/30/2017
ms.author: LADocs; estfan
ms.openlocfilehash: df1e19b772b41064aff1f345dee93813f0c21c73
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Virtuális gép módosítások Azure esemény rács és logikai alkalmazások figyelése

Megkezdheti az automatizált [logic app munkafolyamat](../logic-apps/logic-apps-what-are-logic-apps.md) amikor adott események következnek be, az Azure-erőforrások és a külső erőforrásokhoz. Ezeket az erőforrásokat is közzéteheti ezeket az eseményeket egy [Azure esemény rács](../event-grid/overview.md). Viszont a esemény rács leküldéses értesítések ezeket az eseményeket, amelyek várólistákat, webhookokkal, az előfizetőknek vagy [az event hubs](../event-hubs/event-hubs-what-is-event-hubs.md) neve azonosítja. Az előfizető, mint a Logic Apps alkalmazást várhat ezeket az eseményeket az Eseménynapló rácsban feladatait - programozás nélkül automatizált munkafolyamatok futtatása előtt.

Például az alábbiakban néhány eseményeket, amelyek a közzétevők küldhet előfizetők a Azure esemény rács szolgáltatáson keresztül:

* Létrehozása, olvasása, frissítése vagy erőforrás törlése. Például módosításokat, előfordulhat, hogy az Azure-előfizetéshez a költségek, és a számlázási hatással lehet figyelni. 
* Hozzáadása vagy eltávolítása egy személy az Azure-előfizetésből.
* Az alkalmazás egy bizonyos művelet végrehajtása.
* A várólista egy új üzenet jelenik meg.

Ebben az oktatóanyagban létrehoz egy logikai alkalmazás, amely a virtuális gépek módosításai figyeli, és ezeket a módosításokat vonatkozó e-mailt küld. Ha egy esemény-előfizetést egy Azure-erőforrás a logikai alkalmazás létrehozásához, események haladjanak egy esemény rácshoz adott erőforráshoz a logikai alkalmazás. Az oktatóanyag végigvezeti a logikai alkalmazás létrehozása:

![Áttekintés – esemény rács és a logic app virtuális gép figyelője](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy esemény rácsban eseményeket figyelő logikai alkalmazás létrehozása.
> * Adjon hozzá olyan feltétel, amely kifejezetten ellenőrzi a virtuális gép módosításokat.
> * E-mail küldése, ha a virtuális gép vált.

## <a name="prerequisites"></a>Előfeltételek

* Az e-mail-fiók [bármely Azure Logic Apps által támogatott e-mail szolgáltató](../connectors/apis-list.md), például az Office 365 Outlook, Outlook.com-os vagy Gmailes, az értesítések küldésével. Ebben az oktatóanyagban az Office 365 Outlookot használjuk.

* A [virtuális gép](https://azure.microsoft.com/services/virtual-machines). Ha még nem tette meg, a virtuális gép létrehozása egy [hozzon létre egy virtuális gép oktatóanyag](https://docs.microsoft.com/azure/virtual-machines/). A virtuális gép közzé az eseményeket, hogy Ön [nem kell semmi mást tennie](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Egy esemény rácsban eseményeket figyelő logikai alkalmazás létrehozása

Először logikai alkalmazás létrehozása és hozzáadása, amely figyeli a virtuális géphez tartozó erőforráscsoport rács eseményindító. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. A főmenü Azure bal felső sarokban válassza **új** > **vállalati integrációs** > **logikai alkalmazás**.

   ![Logikai alkalmazás létrehozása](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. A logikai alkalmazás létrehozása az alábbi táblázatban megadott beállításokkal:

   ![A logikai alkalmazás részleteinek megadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Name (Név)** | *{a-logika-alkalmazás-neve}* | Adjon meg egy egyedi nevet a logikai alkalmazás számára. | 
   | **Előfizetés** | *{a-Azure-előfizetés}* | Ebben az oktatóanyagban válassza ki az ugyanazon Azure-előfizetés az összes szolgáltatáshoz. | 
   | **Erőforráscsoport** | *{a-Azure-erőforráscsoport}* | Ebben az oktatóanyagban válassza ki az ugyanazon Azure erőforráscsoport az összes szolgáltatáshoz. | 
   | **Hely** | *{a-Azure-régió}* | Ebben az oktatóanyagban válassza ki a ugyanabban a régióban, az összes szolgáltatáshoz. | 
   | | | 

4. Ha elkészült, válassza ki a **rögzítés az irányítópulton**, és válassza a **létrehozása**.

   Ezzel létrehozott egy Azure-erőforrást a logikai alkalmazás számára. 
   Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer gyakran használt minták sablonjait jeleníti meg, hogy megkönnyítse az első lépéseket.

   > [!NOTE] 
   > Ha bejelöli **rögzítés az irányítópulton**, a logikai alkalmazás automatikusan megnyílik a Logic Apps-tervezőben. Ellenkező esetben manuálisan is található, majd nyissa meg a Logic Apps alkalmazást.

5. Mostantól kiválaszthatja a logic app-sablon. A **sablonok**, válassza a **üres logikai alkalmazás** , hozhat létre a logikai alkalmazás létrehozása.

   ![Logikaialkalmazás-sablon kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   A Logic Apps Designer most bemutatja, [ *összekötők* ](../connectors/apis-list.md) és [ *eseményindítók* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) használható a Logic Apps alkalmazást, és az is, hogy adhat hozzá egy eseményindító feladatok végrehajtása után műveletek. Egy eseményindítót hoz létre egy logic app-példány, és elindítja a logic app munkafolyamatot esemény. 
   A Logic Apps alkalmazást kell egy eseményindító első elemeként.

6. A keresési mezőbe írja be az "esemény rács" szűrőként. Válassza ki az ehhez az eseményindítóhoz: **Azure esemény rács - erőforrás-esemény**

   ![Válassza ki az eseményindító: "Azure esemény rács - erőforrás esemény"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Amikor a rendszer kéri, jelentkezzen be Azure esemény rácshoz a Azure hitelesítő adataival.

   ![Jelentkezzen be Azure hitelesítő adataival](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Ha be van jelentkezve be személyes Microsoft-fiókkal, például a @outlook.com vagy @hotmail.com, az esemény rács eseményindító nem megfelelően jelenik meg. A probléma megoldásához válassza [Connect szolgáltatás egyszerű](../azure-resource-manager/resource-group-create-service-principal-portal.md), vagy a hitelesítés az Azure Active Directory társított Azure-előfizetése, például tagjaként *felhasználónév-*@emailoutlook.onmicrosoft.com.

8. Most már az előfizetés, a Logic Apps alkalmazást publisher eseményekre. Az alábbi táblázatban megadottak az esemény-előfizetés adatainak megadása:

   ![Esemény előfizetés részletesen](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Előfizetés** | *{virtuális-machine-Azure-előfizetés}* | Válassza ki az esemény-közzétevő Azure-előfizetés. A jelen oktatóanyag esetében válassza ki a virtuális gép az Azure-előfizetés. | 
   | **Erőforrás típusa** | Microsoft.Resources.resourceGroups | Válassza ki az esemény-közzétevő erőforrás típusát. A jelen oktatóanyag esetében válassza ki a megadott érték, a Logic Apps alkalmazást csak erőforráscsoportok figyeli. | 
   | **Erőforrás neve** | *{virtuális-erőforrás-csoport-számítógépnév}* | Válassza ki a közzétevő erőforrás nevét. A jelen oktatóanyag esetében válassza ki a virtuális gép az erőforráscsoport nevét. | 
   | Választható beállítások kiválasztása **speciális beállítások megjelenítése**. | *{leírását lásd:}* | * **Előtag-szűrő**: A jelen oktatóanyag esetében ez a beállítás üresen hagyja. Az alapértelmezett viselkedés megegyezik az összes értéket. Azonban megadhat egy előtag-karakterlánc szűrőként, például egy elérési utat és egy adott erőforrás paramétert. <p>* **Szűrő utótag**: A jelen oktatóanyag esetében ez a beállítás üresen hagyja. Az alapértelmezett viselkedés megegyezik az összes értéket. Azonban megadhat egy karakterlánc utótagja szűrőként, például a fájlnév-kiterjesztésűeket, ha azt szeretné, hogy csak bizonyos fájltípusok.<p>* **Előfizetés neve**: Adjon meg egy egyedi nevet az esemény-előfizetéséhez. |
   | | | 

   Amikor elkészült, a rács eseményindító ebben a példában látható:
   
   ![Példa esemény rács eseményindító részletei](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Mentse a Logic Apps alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot. Csukja össze, és egy művelet részleteit a Logic Apps alkalmazást elrejtése, válassza ki a művelet címsorában.

   ![A logikai alkalmazás mentése](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   A Logic Apps alkalmazást a rács eseményindító mentésekor az Azure automatikusan létrehoz egy esemény-előfizetést, a logikai alkalmazásnak, hogy a kiválasztott erőforrás. Ezért az erőforrás a esemény rácshoz tesz közzé egy eseményt, amikor adott esemény rács automatikusan leküldi az esemény a Logic Apps alkalmazást. Ezt az eseményt váltja ki a Logic Apps alkalmazást, akkor hoz létre, és fut a következő lépések meghatározott munkafolyamat példánya.

A Logic Apps alkalmazást élő és az esemény rácsban eseményeket figyeli, de nem semmit műveletek hozzáadnia a munkafolyamat. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Olyan feltétel, amely ellenőrzi a virtuális gép módosításokat hozzáadása

A logic app munkafolyamat futtatásához csak akkor, ha egy adott esemény történik, vegye fel egy feltételt, amely ellenőrzi a virtuális gép "write" műveletek. Ez az állapot értéke igaz, amikor elküldi a Logic Apps alkalmazást, e-mailben elküldi a frissített virtuális gép adatait.

1. Logic App Designer alapján az eseményindító rács válasszon **új lépés** > **feltétel hozzáadása**.

   ![A logikai alkalmazáshoz feltétel hozzáadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   A Logic App Designer egy üres feltétel hozzáadása a munkafolyamathoz, beleértve a művelet elérési útjait kövesse alapján, hogy a feltétel igaz vagy hamis.

   ![Üres feltétel](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. Az a **feltétel** válassza **speciális módban szerkesztése**.
Adja meg a kifejezés:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   A feltétel most néz ki ebben a példában:

   ![Üres feltétel](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Ebben a kifejezésben ellenőrzi az esemény `body` a egy `data` objektum ahol a `operationName` tulajdonság a `Microsoft.Compute/virtualMachines/write` műveletet. 
   További információ [esemény rács esemény séma](../event-grid/event-schema.md).

3. Adja meg a feltétel leírását, válassza ki a **folytatást jelző pontokra** (**...** ) gombra kattint, a feltétel alakzat, majd kattintson a **átnevezése**.

   > [!NOTE] 
   > Ez az oktatóanyag későbbi példák lépéseket a logic app munkafolyamat leírását is tartalmazzák.

4. Válasszon **alapvető módban szerkesztése** , hogy a kifejezés automatikusan feloldja a látható módon:

   ![Logic app feltétel](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Mentse a Logic Apps alkalmazást.

## <a name="send-email-when-your-virtual-machine-changes"></a>E-mail küldése, ha a virtuális gép vált

Ezután adja hozzá egy [ *művelet* ](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) , hogy a megadott feltétel teljesülésekor kap egy e-mailt.

1. A feltételben **igaz értéke esetén** válassza **művelet hozzáadása**.

   ![Ha a feltétel teljesül-e a művelet hozzáadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. A keresési mezőbe írja be az "e-mail" szűrőként. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ezután válassza ki az „e-mail küldése” műveletet az összekötőn. Példa: 

   * Azure munkahelyi vagy iskolai fiókok esetében válassza az Office 365 Outlook-összekötőt. 
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt. 
   * Gmail-fiókok esetében válassza a Gmail-összekötőt. 

   Mi most az Office 365 Outlook-összekötőt választjuk ki. 
   Ha egy másik szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület eltérhet. 

   ![Válassza ki a "e-mail küldési" művelet](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Ha még nem rendelkezik a kapcsolat az e-mailt Provider, jelentkezzen be az e-mail fiókjába kérő hitelesítéshez.

4. Az e-mailek, az alábbi táblázatban megadottak részletesen:

   ![Üres e-mail művelet](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > A munkafolyamat elérhető mezők kiválasztásához kattintson a szerkesztőmező úgy, hogy a **dinamikus tartalom** megnyílik listában, vagy válasszon **dinamikus tartalom hozzáadása az**. További mezők kiválasztása **további** minden szakasz a listában. Bezárja a **dinamikus tartalom** menüben válassza ki **dinamikus tartalom hozzáadása az**.

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Címzett** | *{címzett – e-mail-cím}* |Adja meg a címzett e-mail-címét. Tesztelési célokra használhatja a saját e-mail-címét. | 
   | **Tárgy** | Erőforrás frissítése: **tulajdonos**| Adja meg az e-mail tárgymezőjének tartalmát. A jelen oktatóanyag esetében adja meg a javasolt szöveget, és válassza ki az esemény **tulajdonos** mező. Az e-mail tárgyát ide, tartalmazza a frissített erőforrás (virtuális gép) nevét. | 
   | **Törzs** | Erőforráscsoport: **témakör** <p>Eseménytípus: **esemény típusa**<p>Eseményazonosító: **azonosítója**<p>Idő: **esemény időpontja** | Adja meg az e-mail törzsének tartalmát. A jelen oktatóanyag esetében adja meg a javasolt szöveget, és válassza ki az esemény **témakör**, **eseménytípus**, **azonosító**, és **esemény időpontja** mezők, hogy az e-mail tartalmazza az erőforráscsoport-név, eseménytípus, esemény időbélyegzője és a frissítés eseményazonosító. <p>A tartalom üres sorok hozzáadásához nyomja le a Shift + Enter billentyűt. | 
   | | | 

   > [!NOTE] 
   > Ha egy mező tömb jelölő választja, a tervező automatikusan hozzáadja a **minden** hurkot köré a művelet, amely a tömb hivatkozik. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja az adott műveletet.

   Most az e-mailek művelettel ebben a példában látható:

   ![Válassza ki ahhoz, hogy szerepeljen az e-mail kimenetek](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   És a befejezett Logic Apps alkalmazást ebben a példában nézhet ki:

   ![Befejezett logikai alkalmazás](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Mentse a Logic Apps alkalmazást. Csukja össze, és minden művelet részleteit a Logic Apps alkalmazást elrejtése, válassza ki a művelet címsorában.

   ![A logikai alkalmazás mentése](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   A Logic Apps alkalmazást élő, de a virtuális gép módosításainak vár, mielőtt bármi. 
   A logikai alkalmazás teszteléséhez folytassa a következő szakasszal.

## <a name="test-your-logic-app-workflow"></a>Tesztelje a logic app munkafolyamat

1. Ellenőrizze, hogy a logikai alkalmazás a megadott események egyre, frissítse a virtuális gép. 

   Például a virtuális gép az Azure portálon átméretezheti vagy [méretezze át a virtuális Gépet az Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Néhány másodpercen belül egy e-mailt kell kapnia. Példa:

   ![E-mail-virtuális gép frissítése](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Válassza a Logic Apps alkalmazást, a logic app menüjében előzményei indul el, tekintse át a fut **áttekintése**. Az egyes futtatásokkal kapcsolatos részletek megtekintéséhez válassza az adott futtatás sorát.

   ![Logic Apps alkalmazást futtat előzmények](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Az egyes lépések be- és kimeneteinek megtekintéséhez bontsa ki az áttekinteni kívánt egyes lépéseket. Ezek az információk segítenek diagnosztizálni és elhárítani a logikai alkalmazás hibáit.
 
   ![Futtassa az előzmények részletek logikai alkalmazás](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Gratulálunk, elkészítette és erőforrás-események az esemény rács keresztül figyeli, és e-mailt küld, ha ezen események logikai alkalmazás futtatására. Is megtanulta, hogyan könnyen, amely automatizálja a folyamatok és rendszerek integrálható a felhőalapú szolgáltatások munkafolyamatokat hozhat létre.

Egyéb konfigurációs módosítások végrehajtása esemény rácsok és logikai alkalmazások, például figyelhetők:

* A virtuális gép lekérdezi szerepköralapú hozzáférés-vezérlést (RBAC).
* Hálózati biztonsági csoport (NSG) egy adott hálózati csatoló (NIC) a végrehajtott módosítások.
* A virtuális gép lemezeinek hozzáadásakor vagy eltávolításakor.
* A nyilvános IP-cím hozzá van rendelve egy virtuális gép hálózati adaptert.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag erőforrást használ, és költségek az Azure-előfizetéshez a műveletet. Így ha az oktatóanyag nem használja, és a teszteléshez, győződjön meg arról, hogy letiltja vagy semmilyen erőforráshoz, amennyiben nem kívánja költségek törlése.

* Ha a munkája törlése nélkül kívánja leállítani a logikai alkalmazást, akkor tiltsa le. A logikai alkalmazás menüjében válassza az **Áttekintés** elemet. Az eszköztáron válassza a **Letiltás** parancsot.

  ![A logikai alkalmazás kikapcsolása](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

* Végleg törölni kívánja a Logic Apps alkalmazást, a logic app menüben válasszon **áttekintése**. Az eszköztáron válassza a **Törlés** parancsot. Erősítse meg a logikai alkalmazás törlését, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Hozzon létre és útvonal-esemény rácshoz egyéni események](../event-grid/custom-event-quickstart.md)
