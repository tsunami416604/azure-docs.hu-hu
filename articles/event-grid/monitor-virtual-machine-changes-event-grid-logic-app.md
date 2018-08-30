---
title: Virtuális gépek módosításainak monitorozása – Azure Event Grid és Logic Apps | Microsoft Docs
description: Virtuális gépek (VM-ek) konfigurációváltozásainak ellenőrzése az Azure Event Grid és a Logic Apps segítségével
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 11/30/2017
ms.openlocfilehash: 29b28b0d81314d062c1b334092979cc9bccbeb31
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127659"
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével

Az Azure-beli vagy külső erőforrásokban bekövetkező adott események esetén elindíthat automatizált [logikaialkalmazás-munkafolyamatokat](../logic-apps/logic-apps-overview.md). Ezek az erőforrások közzétehetik az eseményeket az [Azure Event Griden](../event-grid/overview.md). Az eseményrács ezután leküldi az eseményeket a végpontként üzenetsorokkal, webhookokkal vagy [eseményközpontokkal](../event-hubs/event-hubs-what-is-event-hubs.md) rendelkező feliratkozókra. Feliratkozóként logikai alkalmazása fogadhatja az eseményeket az eseményrácsról, és azok alapján automatizált munkafolyamatokat hajthat végre – mindehhez nem szükséges programkódot írnia.

Például íme néhány esemény, amelyeket a közzétevők az Azure Event Grid szolgáltatáson keresztül küldhetnek a feliratkozóknak:

* Erőforrás létrehozása, olvasása, frissítése vagy törlése. Például monitorozhatja az olyan változásokat, amelyek költségekkel terhelhetik Azure-előfizetését, és befolyásolhatják az egyenlegét. 
* Személy hozzáadása vagy eltávolítása egy Azure-előfizetésben.
* Az alkalmazás végrehajt egy bizonyos műveletet.
* Egy új üzenet jelenik meg egy üzenetsorban.

Ez az oktatóanyag egy logikai alkalmazást hoz létre, amely egy virtuális gép változásait monitorozza, és a változásokról e-mail-értesítéseket küld. Amikor létrehoz egy logikai alkalmazást egy eseményfeliratkozással egy Azure-erőforrásra, az erőforrás eseményei az eseményrácson keresztül adódnak át a logikai alkalmazásnak. Az oktatóanyag ennek a logikai alkalmazásnak a létrehozását mutatja be:

![Áttekintés – virtuális gép monitorozása eseményrács és logikai alkalmazás használatával](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eseményeket egy eseményrácson keresztül monitorozó logikai alkalmazás létrehozása.
> * Feltétel hozzáadása, amely kifejezetten a virtuális gépek változásait figyeli.
> * E-mailek küldése a virtuális gép változása esetén.

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure Logic Apps által támogatott bármely e-mail-szolgáltató](../connectors/apis-list.md) (például Office 365 Outlook, Outlook.com vagy Gmail) által üzemeltetett e-mail-fiók az értesítések küldéséhez. Ebben az oktatóanyagban az Office 365 Outlookot használjuk.

* Egy [virtuális gép](https://azure.microsoft.com/services/virtual-machines). Ha még nem tette meg, hozzon létre egy virtuális gépet a [virtuális gépek létrehozását](https://docs.microsoft.com/azure/virtual-machines/) ismertető oktatóanyag alapján. Ahhoz, hogy a virtuális gép eseményeket tegyen közzé, [semmi egyebet nem kell tennie](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Az eseményeket egy eseményrácson keresztül monitorozó logikai alkalmazás létrehozása

Először hozzon létre egy logikai alkalmazást, és adjon hozzá egy Event Grid-triggert, amely a virtuális gép erőforráscsoportját monitorozza. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. Az Azure fő menüjének bal felső sarkában válassza az **Erőforrás létrehozása** > **Enterprise Integration** > **Logic App** elemet.

   ![Logikai alkalmazás létrehozása](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Hozza létre a logikai alkalmazást az alábbi táblázatban megadott beállításokkal:

   ![A logikai alkalmazás részleteinek megadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Name (Név)** | *{a-logikai-alkalmazása-neve}* | Adjon meg egy egyedi nevet a logikai alkalmazás számára. | 
   | **Előfizetés** | *{az-Ön-Azure-előfizetése}* | Válassza ugyanazt az Azure-előfizetést az oktatóanyagban szereplő minden szolgáltatáshoz. | 
   | **Erőforráscsoport** | *{az-Ön-Azure-erőforráscsoportja}* | Válassza ugyanazt az Azure-erőforráscsoportot az oktatóanyagban szereplő minden szolgáltatáshoz. | 
   | **Hely** | *{az-Ön-Azure-régiója}* | Válassza ugyanazt a régiót az oktatóanyagban szereplő minden szolgáltatáshoz. | 
   | | | 

4. Mikor végzett, válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

   Ezzel létrehozott egy Azure-erőforrást a logikai alkalmazás számára. 
   Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer gyakran használt minták sablonjait jeleníti meg, hogy megkönnyítse az első lépéseket.

   > [!NOTE] 
   > A **Rögzítés az irányítópulton** elemet kiválasztva a logikai alkalmazás automatikusan megnyílik a Logic Apps Designerben. Ellenkező esetben manuálisan is megkeresheti és megnyithatja a logikai alkalmazást.

5. Most válasszon egy logikaialkalmazás-sablont. A **Sablonok** alatt válassza az **Üres logikai alkalmazás** elemet, így az alapoktól építheti fel saját logikai alkalmazását.

   ![Logikaialkalmazás-sablon kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   A Logic Apps Designerben most megjelennek a logikai alkalmazás indítására használható [*összekötők*](../connectors/apis-list.md) és [*triggerek*](../logic-apps/logic-apps-overview.md#logic-app-concepts), valamint a triggerek mögé befűzhető műveletek, amelyek tevékenységeket hajtanak végre. A trigger egy olyan esemény, amely létrehoz egy logikaialkalmazás-példányt és elindítja a logikai alkalmazás munkafolyamatát. 
   A logikai alkalmazásnak az első elemeként egy triggert kell tartalmaznia.

6. A keresőmezőbe írja be szűrőként az „event grid” kifejezést. Válassza ki a következő triggert: **Azure Event Grid – Erőforrásesemény esetén**

   ![Az „Azure Event Grid – Erőforrásesemény esetén” trigger kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Amikor a rendszer erre kéri, jelentkezzen be Azure Event Gridbe az Azure-beli hitelesítő adataival.

   ![Bejelentkezés az Azure-beli hitelesítő adatokkal](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Ha személyes Microsoft-fiókkal jelentkezett be (például @outlook.com vagy @hotmail.com), az Event Grid-trigger esetleg nem megfelelően jelenik meg. Kerülő megoldásként válassza a [Csatlakozás szolgáltatásnévvel](../azure-resource-manager/resource-group-create-service-principal-portal.md) lehetőséget, vagy jelentkezzen be az Azure-előfizetéséhez tartozó Azure Active Directory tagjaként (például *felhasználónév*@emailoutlook.onmicrosoft.com).

8. Most iratkoztassa fel a logikai alkalmazást a közzétevők eseményeire. Az eseményfeliratkozáshoz az alábbi táblázatban megadott adatokat adja meg:

   ![Eseményfeliratkozás adatainak megadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Előfizetés** | *{a-virtuális-gép-Azure-előfizetése}* | Válassza az esemény-közzétevő Azure-előfizetését. Ebben az oktatóanyagban válassza a virtuális géphez tartozó Azure-előfizetést. | 
   | **Erőforrás típusa** | Microsoft.Resources.resourceGroups | Válassza az esemény-közzétevő erőforrástípusát. A jelen oktatóanyag esetében válassza a megadott értéket, így a logikai alkalmazás csak az erőforráscsoportokat monitorozza majd. | 
   | **Erőforrás neve** | *{a-virtuális-gép-erőforráscsoportjának-neve}* | Válassza a közzétevő erőforrás nevét. Ebben az oktatóanyagban válassza a virtuális géphez tartozó erőforráscsoportot. | 
   | A speciális beállítások megjelenítéséhez kattintson a **Speciális beállítások megjelenítése** gombra. | *{lásd a leírásokat}* | * **Előtagszűrő**: Ebben az oktatóanyagban hagyja üresen ezt a beállítást. Az alapértelmezett beállítás minden értéket megenged. Azonban megadhat szűrőként egy előtagsztringet is, például egy elérési útvonalat és egy adott erőforrás paraméterét. <p>* **Utótagszűrő**: Ebben az oktatóanyagban hagyja üresen ezt a beállítást. Az alapértelmezett beállítás minden értéket megenged. Azonban megadhat szűrőként egy utótagsztringet is, például egy fájlnévkiterjesztést, ha csak adott fájltípusokra kíváncsi.<p>* **Feliratkozás neve**: Adjon egy egyedi nevet az eseményfeliratkozásnak. |
   | | | 

   Ha elkészült, az eseményrács-trigger a következő példához hasonlíthat:
   
   ![Eseményrács-trigger példa](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot. A logikai alkalmazásban lévő egyes műveletek részleteinek összecsukásához és elrejtéséhez kattintson az adott művelet címsorára.

   ![A logikai alkalmazás mentése](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Amikor ment egy eseményrács-triggerrel rendelkező logikai alkalmazást, az Azure automatikusan létrehoz egy eseményfeliratkozást a logikai alkalmazás számára a kiválasztott erőforráshoz. Így amikor az erőforrás közzétesz egy eseményt az eseményrácson, az eseményrács automatikusan leküldi az eseményt a logikai alkalmazásnak. Az esemény aktiválja a logikai alkalmazást, majd létrehozza és futtatja a következő lépésekben meghatározott munkafolyamat egy példányát.

A logikai alkalmazás most már működőképes, és figyeli az eseményrácsról érkező eseményeket, de amíg fel nem vesz a munkafolyamatba műveleteket, semmit nem csinál. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Feltétel hozzáadása, amely a virtuális gépek változásait figyeli

Ahhoz, hogy a logikai alkalmazás munkafolyamata csak akkor fusson, ha egy adott esemény bekövetkezik, adjon hozzá egy feltételt, amely a virtuális gép „írás” műveleteit figyeli. Ha ez a feltétel teljesül, a logikai alkalmazás e-mailben elküldi a frissített virtuális gép adatait.

1. A Logic App Designerben a trigger alatt válassza az **Új lépés** > **Feltétel hozzáadása** elemet.

   ![Feltétel hozzáadása a logikai alkalmazáshoz](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   A Logic App Designer hozzáad egy üres feltételt a munkafolyamathoz, beleértve a feltétel igaz vagy hamis értékei esetén követendő műveleti útvonalakat.

   ![Üres feltétel](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. A **Feltétel** mezőben válassza a **Szerkesztés speciális módban** lehetőséget.
Írja be a következő kifejezést:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   A feltétel most a következő példához hasonlít:

   ![Üres feltétel](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Ez a kifejezés ellenőrzi, hogy az esemény `body` tartalmaz-e olyan `data` objektumot, ahol az `operationName` tulajdonság a `Microsoft.Compute/virtualMachines/write` művelet. 
   További tudnivalók az [Event Grid eseménysémáról](../event-grid/event-schema.md).

3. A feltétel leírásának megadásához válassza a feltételalakzaton a **három pont** (**...**) gombot, majd az **Átnevezés** lehetőséget.

   > [!NOTE] 
   > Az oktatóanyagban szereplő későbbi példák szintén tartalmaznak leírásokat a logikai alkalmazás munkafolyamatának lépéseire.

4. Most válassza a **Szerkesztés egyszerű módban** lehetőséget, így a kifejezés automatikusan feloldódik az alábbiak szerint:

   ![Logikai alkalmazás feltétele](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Mentse a logikai alkalmazást.

## <a name="send-email-when-your-virtual-machine-changes"></a>E-mailek küldése a virtuális gép változása esetén

Most adjon hozzá egy [*műveletet*](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely egy e-mailt küld a megadott feltétel teljesülésekor.

1. A feltétel **Ha igaz** mezőjében válassza a **Művelet hozzáadása** lehetőséget.

   ![Művelet hozzáadása a feltétel igaz értéke esetére](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. A keresőmezőbe írja be szűrőként az „e-mail” kifejezést. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ezután válassza ki az „e-mail küldése” műveletet az összekötőn. Például: 

   * Azure munkahelyi vagy iskolai fiókok esetében válassza az Office 365 Outlook-összekötőt. 
   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt. 
   * Gmail-fiókok esetében válassza a Gmail-összekötőt. 

   Mi most az Office 365 Outlook-összekötőt választjuk ki. 
   Ha egy másik szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület eltérhet. 

   ![Az „e-mail küldése” művelet kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Ha még nem rendelkezik kapcsolattal az e-mail-szolgáltatójához, amikor a rendszer kéri, hogy hitelesítse magát, jelentkezzen be az e-mail-fiókjába.

4. Az e-mailre vonatkozóan az alábbi táblázatban megadott adatokat adja meg:

   ![Üres e-mail-művelet](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > A munkafolyamatban elérhető mezők megjelenítéséhez az egyik szerkesztőmezőbe kattintva nyissa le a **Dinamikus tartalom** listát, vagy válassza a **Dinamikus tartalom hozzáadása** elemet. További mezőkért válassza a **Továbbiak** lehetőséget a lista egyes szakaszaiban. A **Dinamikus tartalom** lista bezárásához válassza a **Dinamikus tartalom hozzáadása** lehetőséget.

   | Beállítás | Ajánlott érték | Leírás | 
   | ------- | --------------- | ----------- | 
   | **Címzett** | *{címzett-e-mail-címe}* |Adja meg a címzett e-mail-címét. Tesztelési célokra használhatja a saját e-mail-címét. | 
   | **Tárgy** | Frissített erőforrás: **Tárgy**| Adja meg az e-mail tárgymezőjének tartalmát. A jelen oktatóanyag esetében írja be a javasolt szöveget, és válassza az esemény **Tárgy** mezőjét. Itt az e-mail tárgya a frissített erőforrás (virtuális gép) nevét tartalmazza. | 
   | **Törzs** | Erőforráscsoport: **Témakör** <p>Eseménytípus: **Eseménytípus**<p>Eseményazonosító: **Azonosító**<p>Esemény ideje: **Esemény ideje** | Adja meg az e-mail törzsének tartalmát. A jelen oktatóanyag esetében írja be a javasolt szöveget, és válassza ki az esemény **Témakör**, **Eseménytípus**, **Azonosító** és **Esemény ideje** mezőit, így az e-mail tartalmazza majd az erőforráscsoport nevét, az esemény típusát, az esemény időbélyegét és a frissítés eseményazonosítóját. <p>Ha üres sorokat kíván beszúrni a tartalomba, nyomja le a Shift + Enter billentyűkombinációt. | 
   | | | 

   > [!NOTE] 
   > Ha olyan mezőt választ ki, amely egy tömböt jelöl, a tervező automatikusan hozzáad egy **Mindegyikre** hurkot a tömbre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja az adott műveletet.

   Most az e-mail-művelet a következő példához hasonló lehet:

   ![Az e-mailben szerepeltetni kívánt kimenetek kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   A befejezett logikai alkalmazás pedig a következő példához hasonló lehet:

   ![Befejezett logikai alkalmazás](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Mentse a logikai alkalmazást. A logikai alkalmazásban lévő egyes műveletek részleteinek összecsukásához és elrejtéséhez kattintson az adott művelet címsorára.

   ![A logikai alkalmazás mentése](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   A logikai alkalmazás most már működőképes, de mielőtt bármit is tenne, vár a virtuális gép változásaira. 
   A logikai alkalmazás teszteléséhez folytassa a következő szakasszal.

## <a name="test-your-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának tesztelése

1. Annak ellenőrzéséhez, hogy a logikai alkalmazás megkapja-e a megadott eseményeket, frissítse a virtuális gépet. 

   Például átméretezheti a virtuális gépet az Azure Portalon vagy az [Azure PowerShell használatával](../virtual-machines/windows/resize-vm.md). 

   Pár pillanat múlva egy e-mailt kell kapnia. Például:

   ![A virtuális gép módosításáról tájékoztató e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. A logikai alkalmazás futtatási és triggerelőzményeinek áttekintéséhez válassza az **Áttekintés** lehetőséget a logikai alkalmazás menüjében. Az egyes futtatásokkal kapcsolatos részletek megtekintéséhez válassza az adott futtatás sorát.

   ![Logikai alkalmazás futtatási előzményei](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Az egyes lépések be- és kimeneteinek megtekintéséhez bontsa ki az áttekinteni kívánt egyes lépéseket. Ezek az információk segítenek diagnosztizálni és elhárítani a logikai alkalmazás hibáit.
 
   ![Logikai alkalmazás futtatási előzményeinek részletei](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Gratulálunk, sikeresen létrehozott és futtatott egy logikai alkalmazást, amely erőforrás-eseményeket monitoroz egy eseményrácson keresztül, és e-mailt küld az ilyen események bekövetkeztekor. Azt is megtanulta, milyen egyszerű a folyamatokat automatizáló, valamint a rendszereket és felhőalapú szolgáltatásokat integráló munkafolyamatok létrehozása.

Más konfigurációváltozásokat is monitorozhat eseményrácsok és logikai alkalmazások segítségével, például:

* Valamely virtuális gép szerepköralapú hozzáférés-vezérlési (RBAC) jogokat kap.
* Egy hálózati adapter (NIC) egy hálózati biztonsági csoportja (NSG) módosul.
* Lemezeket adnak hozzá vagy törölnek egy virtuális gépen.
* Egy nyilvános IP-címet rendelnek egy virtuális gép hálózati adapteréhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóprogram olyan erőforrásokat és műveleteket alkalmaz, amelyek költségekkel terhelik Azure-előfizetését. Ezért miután végzett az oktatóanyaggal és a teszteléssel, mindenképp tiltsa le vagy inaktiválja azokat az erőforrásokat, amelyek költségeit nem szeretné fizetni.

* Ha a munkája törlése nélkül kívánja leállítani a logikai alkalmazást, akkor tiltsa le. A logikai alkalmazás menüjében válassza az **Áttekintés** elemet. Az eszköztáron válassza a **Letiltás** parancsot.

  ![A logikai alkalmazás kikapcsolása](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

* A logikai alkalmazás végleges törléséhez válassza az **Áttekintés** lehetőséget a logikai alkalmazás menüjében. Az eszköztáron válassza a **Törlés** parancsot. Erősítse meg a logikai alkalmazás törlését, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Egyéni események létrehozása és átirányítása az Event Griddel](../event-grid/custom-event-quickstart.md)
