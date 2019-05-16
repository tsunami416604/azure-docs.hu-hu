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
ms.date: 05/14/2019
ms.openlocfilehash: 33634773b436114f4a5f2942028710ae50e0e703
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65737120"
---
# <a name="tutorial-monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Oktatóanyag: Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével

Az Azure-beli vagy külső erőforrásokban bekövetkező adott események esetén elindíthat automatizált [logikaialkalmazás-munkafolyamatokat](../logic-apps/logic-apps-overview.md). Ezek az erőforrások közzétehetik az eseményeket az [Azure Event Griden](../event-grid/overview.md). Az eseményrács ezután leküldi az eseményeket a végpontként üzenetsorokkal, webhookokkal vagy [eseményközpontokkal](../event-hubs/event-hubs-what-is-event-hubs.md) rendelkező feliratkozókra. Feliratkozóként logikai alkalmazása fogadhatja az eseményeket az eseményrácsról, és azok alapján automatizált munkafolyamatokat hajthat végre – mindehhez nem szükséges programkódot írnia.

Például íme néhány esemény, amelyeket a közzétevők az Azure Event Grid szolgáltatáson keresztül küldhetnek a feliratkozóknak:

* Erőforrás létrehozása, olvasása, frissítése vagy törlése. Például monitorozhatja az olyan változásokat, amelyek költségekkel terhelhetik Azure-előfizetését, és befolyásolhatják az egyenlegét. 
* Személy hozzáadása vagy eltávolítása egy Azure-előfizetésben.
* Az alkalmazás végrehajt egy bizonyos műveletet.
* Egy új üzenet jelenik meg egy üzenetsorban.

Ebben az oktatóanyagban létrehoz egy logikai alkalmazást, amely figyeli a módosítások egy virtuális géphez, és ezeket a módosításokat kapcsolatos e-mailt küld. Amikor létrehoz egy logikai alkalmazást egy eseményfeliratkozással egy Azure-erőforrásra, az erőforrás eseményei az eseményrácson keresztül adódnak át a logikai alkalmazásnak. Az oktatóanyag ennek a logikai alkalmazásnak a létrehozását mutatja be:

![Áttekintés – virtuális gép monitorozása eseményrács és logikai alkalmazás használatával](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eseményeket egy eseményrácson keresztül monitorozó logikai alkalmazás létrehozása.
> * Feltétel hozzáadása, amely kifejezetten a virtuális gépek változásait figyeli.
> * E-mailek küldése a virtuális gép változása esetén.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* E-mail-fiók e-mail-értesítések küldését, például az Office 365 Outlook, Outlook.com vagy Gmail a Logic Apps által támogatott szolgáltató. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/). 

  Ebben az oktatóanyagban az Office 365 Outlook-fiókot használja. Ha más e-mail-fiókot használ, az általános lépések ugyanazok, a felhasználói felület azonban némiképp eltérhet.

* Egy [virtuális gép](https://azure.microsoft.com/services/virtual-machines). Ha ezt még nem tette meg, hozzon létre egy virtuális gép a [hozzon létre egy virtuális gép oktatóanyag](../virtual-machines/windows/quick-create-portal.md). Ahhoz, hogy a virtuális gép eseményeket tegyen közzé, [semmi egyebet nem kell tennie](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Üres logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival. 

1. Az Azure fő menüjéből válassza **erőforrás létrehozása** > **integrációs** > **logikai alkalmazás**.

   ![Logikai alkalmazás létrehozása](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. A **logikai alkalmazás**, adja meg a logikai alkalmazással kapcsolatos információkat. Ha elkészült, kattintson a **Létrehozás** gombra.

   ![A logikai alkalmazás részleteinek megadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Tulajdonság | Ajánlott érték | Leírás |
   | -------- | --------------- | ----------- |
   | **Name (Név)** | <*logic-app-name*> | Adjon meg egy egyedi nevet a logikai alkalmazás. |
   | **Előfizetés** | <*Azure-előfizetés-neve*> | Válassza ugyanazt az Azure-előfizetést az oktatóanyagban szereplő minden szolgáltatáshoz. |
   | **Erőforráscsoport** | <*Azure-resource-group*> | Válassza ugyanazt az Azure-erőforráscsoportot az oktatóanyagban szereplő minden szolgáltatáshoz. |
   | **Hely** | <*Azure-datacenter-region*> | Válassza ugyanazt a régiót az oktatóanyagban szereplő minden szolgáltatáshoz. |
   |||

   Ezzel létrehozott egy Azure-erőforrást a logikai alkalmazás számára. 

1. Miután az Azure üzembe helyezte a logikai alkalmazás, a Logic Apps Designerben videó a bevezetés oldalt jelenít meg, és a gyakran használt triggereket. Görgessen át a videón és a triggereken. 

1. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Logikaialkalmazás-sablon kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   A Logic Apps Designerben most már megtekintheti a [ *eseményindítók* ](../logic-apps/logic-apps-overview.md#logic-app-concepts) , hogy a logikai alkalmazás indítására használhatja. Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. 
   Minden alkalommal, amikor az eseményindító elindulása, az Azure Logic Apps létrehoz egy munkafolyamat-példányt, amely a logikai alkalmazás fut.

## <a name="add-event-grid-trigger"></a>Event Grid-trigger hozzáadása 

Ezután adja hozzá az Event Grid eseményindító, amely figyeli a virtuális gép erőforráscsoportját. 

1. A tervezőben a keresőmezőbe írja be szűrőként "event grid". Az eseményindítók listában jelölje ki az eseményindító: **Erőforrás-esemény bekövetkezésekor – Azure Event Griddel**

   ![Ez az eseményindító kiválasztása: "A"erőforrás-esemény](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Amikor a rendszer kéri, jelentkezzen be az Azure Event Grid az Azure-fiók hitelesítő adataival. Az a **bérlői** listát, amely bemutatja az Azure Active Directory-bérlőhöz társított Azure-előfizetése, ellenőrizze, hogy a megfelelő bérlő jelenik meg.

   ![Bejelentkezés az Azure-beli hitelesítő adatokkal](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Ha személyes Microsoft-fiókkal jelentkezett be (például @outlook.com vagy @hotmail.com), az Event Grid-trigger esetleg nem megfelelően jelenik meg. Kerülő megoldásként válassza a [Csatlakozás szolgáltatásnévvel](../active-directory/develop/howto-create-service-principal-portal.md) lehetőséget, vagy jelentkezzen be az Azure-előfizetéséhez tartozó Azure Active Directory tagjaként (például *felhasználónév*@emailoutlook.onmicrosoft.com).

1. Most iratkoztassa fel a logikai alkalmazást a közzétevők eseményeire. Az eseményfeliratkozáshoz az alábbi táblázatban megadott adatokat adja meg:

   ![Eseményfeliratkozás adatainak megadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Tulajdonság | Szükséges | Value | Leírás |
   | -------- | -------- | ----- | ----------- |
   | **Előfizetés** | Igen | <*event-publisher-Azure-subscription-name*> | Válassza ki az Azure-előfizetéséhez társított az esemény-közzétevő nevét. A jelen oktatóanyag esetében válassza ki a virtuális gép Azure-előfizetés nevét. |
   | **Erőforrás típusa** | Igen | <*event-publisher-Azure-resource-type*> | Válassza ki az Azure-erőforrás típusa az esemény-közzétevő. A jelen oktatóanyag esetében válassza ki ezt az értéket figyelése az Azure-erőforráscsoportok: <p><p>**Microsoft.Resources.ResourceGroups** |
   | **Erőforrás neve** |  Igen | <*event-publisher-Azure-resource-name*> | Válassza ki az Azure-erőforrás nevét az esemény-közzétevő esetében. A kiválasztott erőforrás típusa attól függően változik, a listában. A jelen oktatóanyag esetében válassza ki az Azure-erőforráscsoportot a virtuális gép nevét. |
   | **Esemény típusú elem** |  Nem | <*event-types*> | Válassza ki egy vagy több adott eseménytípusok szűrése és az event grid küldhet. Ha például azt is megteheti e eseménytípusok megállapítani, ha az erőforrások módosítva vagy törölve: <p><p>- **Microsoft.Resources.ResourceActionSuccess** <br>- **Microsoft.Resources.ResourceDeleteSuccess** <br>- **Microsoft.Resources.ResourceWriteSuccess** <p>További információkért lásd az alábbi témakörök: <p><p>- [Megismerheti az események szűrése](../event-grid/event-filtering.md) <br>- [Az Event Griddel kapcsolatos események szűrése](../event-grid/how-to-filter-events.md) <br>- [Az erőforráscsoportok az Azure Event Grid eseménysémája](../event-grid/event-schema-resource-groups.md) |
   | **Előfizetés neve** | Nem | <*event-subscription-name*> | Adjon egy egyedi nevet az esemény-előfizetésnek. |
   | Választható beállítások kiválasztása **új paraméter hozzáadása**. | Nem | {a leírásokat lásd:} | * **Előtagszűrő**: Ebben az oktatóanyagban hagyja üresen ezt a tulajdonságot. Az alapértelmezett beállítás minden értéket megenged. Azonban megadhat szűrőként egy előtagsztringet is, például egy elérési útvonalat és egy adott erőforrás paraméterét. <p>* **Szűrő utótag**: Ebben az oktatóanyagban hagyja üresen ezt a tulajdonságot. Az alapértelmezett beállítás minden értéket megenged. Azonban megadhat szűrőként egy utótagsztringet is, például egy fájlnévkiterjesztést, ha csak adott fájltípusokra kíváncsi. |
   |||

   Ha elkészült, az Event Grid eseményindító ebben a példában látható:

   ![Példa Event Grid-trigger részletei](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot. A logikai alkalmazásban lévő egyes műveletek részleteinek összecsukásához és elrejtéséhez kattintson az adott művelet címsorára.

   ![A logikai alkalmazás mentése](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Amikor ment egy eseményrács-triggerrel rendelkező logikai alkalmazást, az Azure automatikusan létrehoz egy eseményfeliratkozást a logikai alkalmazás számára a kiválasztott erőforráshoz. Így amikor az erőforrás közzétesz egy eseményt az eseményrácson, az eseményrács automatikusan leküldi az eseményt a logikai alkalmazásnak. Az esemény aktiválja a logikai alkalmazást, majd létrehozza és futtatja a következő lépésekben meghatározott munkafolyamat egy példányát.

A logikai alkalmazás most már működőképes, és figyeli az eseményrácsról érkező eseményeket, de amíg fel nem vesz a munkafolyamatba műveleteket, semmit nem csinál. 

## <a name="add-condition"></a>Feltétel hozzáadása

Ahhoz, hogy a logikai alkalmazás munkafolyamata csak akkor fusson, ha egy adott esemény bekövetkezik, adjon hozzá egy feltételt, amely a virtuális gép „írás” műveleteit figyeli. Ha ez a feltétel teljesül, a logikai alkalmazás e-mailben elküldi a frissített virtuális gép adatait.

1. A Logikaialkalmazás-Tervező, az event grid-trigger alatt válassza a **új lépés**.

   ![Válassza az "Új lépés"](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. A Keresés mezőbe írja be a "feltétel" szűrőként. A műveletek listából válassza a következő műveletet: **A feltétel**

   ![Feltétel hozzáadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   A Logic App Designer hozzáad egy üres feltételt a munkafolyamathoz, beleértve a feltétel igaz vagy hamis értékei esetén követendő műveleti útvonalakat.

   ![Üres feltétel](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Nevezze át a feltételt cím `If a virtual machine in your resource group has changed`. A feltétel címsorán válassza a három pontra (**...** ) gombra, és válassza **átnevezése**.

   ![Feltétel átnevezése](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Hozzon létre egy feltételt, amely ellenőrzi az esemény `body` számára egy `data` objektum, a `operationName` tulajdonság értéke a `Microsoft.Compute/virtualMachines/write` műveletet. További tudnivalók az [Event Grid eseménysémáról](../event-grid/event-schema.md).

   1. Az első sorban az **And** (És) alatt kattintson a bal oldali mezőbe. A dinamikus tartalmak listájában jelenik meg, válassza ki a **kifejezés**.

      ![Válassza az "Expression"](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. A kifejezésszerkesztőbe írja be a kifejezést, és válassza a **OK**: 

      `triggerBody()?['data']['operationName']`

      Példa:

      ![Válassza az "Expression"](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. A középső mezőben tartsa meg az **is equal to** (egyenlő) operátort.

   1. A jobb oldali mezőbe írja be ezt az értéket:

      `Microsoft.Compute/virtualMachines/write`

   A befejezett feltétel most példához hasonlóan néz ki:

   ![Befejezett feltétel](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Átállás a tervezés nézetbe, és a kód nézet és a Tervező nézetre vissza, ha a kifejezés, amely a feltételben megadott feloldja a **data.operationName** token:

   ![Megoldott feltétel](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Mentse a logikai alkalmazást.

## <a name="send-email-notifications"></a>E-mail-értesítések küldése

Most adjon hozzá egy [*műveletet*](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely egy e-mailt küld a megadott feltétel teljesülésekor.

1. A feltétel **Ha igaz** mezőjében válassza a **Művelet hozzáadása** lehetőséget.

   ![Művelet hozzáadása a feltétel igaz értéke esetére](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. A Keresés mezőbe írja be a "e-mail küldése" szűrőként. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ezután válassza ki az „e-mail küldése” műveletet az összekötőn. Példa: 

   * Azure munkahelyi vagy iskolai fiókok esetében válassza az Office 365 Outlook-összekötőt. 

   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt. 

   * Gmail-fiókok esetében válassza a Gmail-összekötőt. 

   Ebben az oktatóanyagban az Office 365 Outlook-összekötővel továbbra is. 
   Ha egy másik szolgáltatót használ, a lépések ugyanazok, de a felhasználói felület némiképp eltérhet. 

   ![Az „e-mail küldése” művelet kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Ha még nem rendelkezik kapcsolattal az e-mail-szolgáltatójához, amikor a rendszer kéri, hogy hitelesítse magát, jelentkezzen be az e-mail-fiókjába.

1. Nevezze át a cím a küldési e-mail cím: `Send email when virtual machine updated`

1. Az e-mailre vonatkozóan az alábbi táblázatban megadott adatokat adja meg:

   ![Üres e-mail-művelet](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > A munkafolyamat előző lépésekből származó eredmények közül válassza ki, kattintson a szerkesztőmezőkhöz, a dinamikus tartalmak listája jelenik meg, vagy válasszon **dinamikus tartalom hozzáadása**. Válassza a további találatok **Továbbiak** a lista minden egyes szakaszában. Zárja be a dinamikus tartalmú listából, válassza a **dinamikus tartalom hozzáadása** újra.

   | Tulajdonság | Szükséges | Value | Leírás |
   | -------- | -------- | ----- | ----------- |
   | **Címzett** | Igen | <*címzett\@tartomány*> | Adja meg a címzett e-mail-címét. Tesztelési célokra használhatja a saját e-mail-címét. |
   | **Tárgy** | Igen | Erőforrás frissítése: **Tárgy** | Adja meg az e-mail tárgymezőjének tartalmát. Ebben az oktatóanyagban adja meg a megadott szöveg, és válassza ki az esemény **tulajdonos** mező. Itt az e-mail tárgya a frissített erőforrás (virtuális gép) nevét tartalmazza. |
   | **Törzs** | Igen | Erőforrás: **A témakör** <p>Eseménytípus: **Esemény típusa**<p>Eseményazonosító: **Azonosító**<p>Idő: **Esemény időpontja** | Adja meg az e-mail törzsének tartalmát. A jelen oktatóanyag esetében írja be a megadott szöveget, és válassza ki az esemény **témakör**, **eseménytípus**, **azonosító**, és **esemény időpontja** mezőket, hogy a e-mailt az, hogy az aktivált az esemény, eseménytípus, esemény időbélyegzője és a frissítés eseményazonosító erőforrást tartalmaz. Ebben az oktatóanyagban az erőforrás a az eseményindító a kiválasztott Azure-erőforráscsoportot. <p>Ha üres sorokat kíván beszúrni a tartalomba, nyomja le a Shift + Enter billentyűkombinációt. |
   ||||

   > [!NOTE]
   > Ha olyan mezőt választ ki, amely egy tömböt jelöl, a tervező automatikusan hozzáad egy **Mindegyikre** hurkot a tömbre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja az adott műveletet.

   Most az e-mail-művelet a következő példához hasonló lehet:

   ![Az e-mailben szerepeltetni kívánt kimenetek kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   A befejezett logikai alkalmazás pedig a következő példához hasonló lehet:

   ![Befejezett logikai alkalmazás](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Mentse a logikai alkalmazást. A logikai alkalmazásban lévő egyes műveletek részleteinek összecsukásához és elrejtéséhez kattintson az adott művelet címsorára.

   A logikai alkalmazás most már működőképes, de mielőtt bármit is tenne, vár a virtuális gép változásaira. 
   A logikai alkalmazás teszteléséhez folytassa a következő szakasszal.

## <a name="test-your-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának tesztelése

1. Annak ellenőrzéséhez, hogy a logikai alkalmazás megkapja-e a megadott eseményeket, frissítse a virtuális gépet.

   Például átméretezheti a virtuális gépet az Azure Portalon vagy az [Azure PowerShell használatával](../virtual-machines/windows/resize-vm.md).

   Pár pillanat múlva egy e-mailt kell kapnia. Példa:

   ![A virtuális gép módosításáról tájékoztató e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Tekintse át a futtatások és eseményindítási előzményeinek a logikai alkalmazás, a logikai alkalmazás menüjében válassza **áttekintése**. Futtatás részleteinek megtekintéséhez jelölje ki a sorát.

   ![Logikai alkalmazás futtatási előzményei](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Az egyes lépések be- és kimeneteinek megtekintéséhez bontsa ki az áttekinteni kívánt egyes lépéseket. Ezek az információk segítenek diagnosztizálni és elhárítani a logikai alkalmazás hibáit.

   ![Logikai alkalmazás futtatási előzményeinek részletei](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Gratulálunk, sikeresen létrehozott és futtatott egy logikai alkalmazást, amely erőforrás-eseményeket monitoroz egy eseményrácson keresztül, és e-mailt küld az ilyen események bekövetkeztekor. Azt is megtanulta, milyen egyszerű a folyamatokat automatizáló, valamint a rendszereket és felhőalapú szolgáltatásokat integráló munkafolyamatok létrehozása.

Más konfigurációváltozásokat is monitorozhat eseményrácsok és logikai alkalmazások segítségével, például:

* Valamely virtuális gép szerepköralapú hozzáférés-vezérlési (RBAC) jogokat kap.
* Egy hálózati adapter (NIC) egy hálózati biztonsági csoportja (NSG) módosul.
* Lemezeket adnak hozzá vagy törölnek egy virtuális gépen.
* Egy nyilvános IP-címet rendelnek egy virtuális gép hálózati adapteréhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóprogram olyan erőforrásokat és műveleteket alkalmaz, amelyek költségekkel terhelik Azure-előfizetését. Ezért miután végzett az oktatóanyaggal és a teszteléssel, mindenképp tiltsa le vagy inaktiválja azokat az erőforrásokat, amelyek költségeit nem szeretné fizetni.

* Ha a munkája törlése nélkül kívánja leállítani a logikai alkalmazást, akkor tiltsa le. A logikai alkalmazás menüjében válassza **áttekintése**. Az eszköztáron válassza a **Letiltás** parancsot.

  ![A logikai alkalmazás kikapcsolása](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

* Véglegesen törli a logikai alkalmazást, a logikai alkalmazás menüjében válassza **áttekintése**. Az eszköztáron válassza a **Törlés** parancsot. Győződjön meg arról, hogy szeretné-e a logikai alkalmazás törlése, és válassza a **törlése**.

## <a name="next-steps"></a>További lépések

* [Egyéni események létrehozása és átirányítása az Event Griddel](../event-grid/custom-event-quickstart.md)
