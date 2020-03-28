---
title: Virtuális gépek változásainak figyelése – Azure Event Grid & logikai alkalmazások
description: A virtuális gépek változásainak ellenőrzése az Azure Event Grid és a Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.date: 10/11/2019
ms.openlocfilehash: f5aac7fe63b2afc997ff69e5d976c755440c1bea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75982576"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Oktatóanyag: A virtuális gépek változásainak figyelése az Azure Event Grid és a Logic Apps használatával

Az Azure-erőforrásokban vagy harmadik felek erőforrásaiban bekövetkező konkrét események figyeléséhez és megválaszolásához automatizálhatja és futtathatja a feladatokat munkafolyamatként egy minimális kódot használó [logikai alkalmazás](../logic-apps/logic-apps-overview.md) létrehozásával. Ezek az erőforrások eseményeket tehetnek közzé egy [Azure-eseményrácson.](../event-grid/overview.md) Az eseményrács ezután leküldi az eseményeket a végpontként üzenetsorokkal, webhookokkal vagy [eseményközpontokkal](../event-hubs/event-hubs-what-is-event-hubs.md) rendelkező feliratkozókra. Előfizetőként a logikai alkalmazás megvárhatja ezeket az eseményeket az eseményrácsból, mielőtt automatikus munkafolyamatokat futtatna a feladatok végrehajtásához.

Például íme néhány esemény, amelyeket a közzétevők az Azure Event Grid szolgáltatáson keresztül küldhetnek a feliratkozóknak:

* Erőforrás létrehozása, olvasása, frissítése vagy törlése. Például monitorozhatja az olyan változásokat, amelyek költségekkel terhelhetik Azure-előfizetését, és befolyásolhatják az egyenlegét.

* Személy hozzáadása vagy eltávolítása egy Azure-előfizetésben.

* Az alkalmazás végrehajt egy bizonyos műveletet.

* Egy új üzenet jelenik meg egy üzenetsorban.

Ez az oktatóanyag létrehoz egy logikai alkalmazást, amely figyeli a virtuális gép módosításait, és e-maileket küld ezekről a változásokról. Amikor létrehoz egy logikai alkalmazást egy eseményfeliratkozással egy Azure-erőforrásra, az erőforrás eseményei az eseményrácson keresztül adódnak át a logikai alkalmazásnak. Az oktatóanyag ennek a logikai alkalmazásnak a létrehozását mutatja be:

![Áttekintés – virtuális gép monitorozása eseményrács és logikai alkalmazás használatával](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eseményeket egy eseményrácson keresztül monitorozó logikai alkalmazás létrehozása.
> * Feltétel hozzáadása, amely kifejezetten a virtuális gépek változásait figyeli.
> * E-mailek küldése a virtuális gép változása esetén.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* E-mail fiók egy e-mail szolgáltatótól, amelyet a Logic Apps támogat értesítések küldéséhez, például az Office 365 Outlook, a Outlook.com vagy a Gmail. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/).

  Ez az oktatóanyag Office 365 Outlook-fiókot használ. Ha más e-mail-fiókot használ, az általános lépések ugyanazok, a felhasználói felület azonban némiképp eltérhet.

* Virtuális [gép,](https://azure.microsoft.com/services/virtual-machines) amely egyedül a saját Azure-erőforrás-csoportban. Ha még nem tette meg, hozzon létre egy virtuális gépet a [Virtuálisgép létrehozása oktatóanyagon](../virtual-machines/windows/quick-create-portal.md)keresztül. Ahhoz, hogy a virtuális gép eseményeket tegyen közzé, [semmi egyebet nem kell tennie](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Üres logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. Az Azure főmenüjében válassza az > **Erőforrás-integrációs** > **logikai alkalmazás** **létrehozása**lehetőséget.

   ![Logikai alkalmazás létrehozása](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. A **Logic App**csoportban adja meg a logikai alkalmazás erőforrásadatait. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

   ![A logikai alkalmazás részleteinek megadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Név** | Igen | <*logikai alkalmazás neve*> | Adjon meg egy egyedi nevet a logikai alkalmazás. |
   | **Előfizetés** | Igen | <*Azure-előfizetés-név*> | Válassza ki ugyanazt az Azure-előfizetést az oktatóanyag összes szolgáltatásához. |
   | **Erőforráscsoport** | Igen | <*Azure-erőforrás-csoport*> | Az Azure-erőforráscsoport neve a logikai alkalmazás, amely kiválaszthatja az összes szolgáltatást ebben az oktatóanyagban. |
   | **Helyen** | Igen | <*Azure-régió*> | Válassza ugyanazt a régiót az oktatóanyagban szereplő minden szolgáltatáshoz. |
   |||

1. Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer egy bevezető videót és általánosan használt eseményindítókat tartalmazó lapot jelenít meg. Görgessen át a videón és a triggereken.

1. A **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet.

   ![Logikai alkalmazássablon kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   A Logic Apps Designer most megmutatja az [*eseményindítók,*](../logic-apps/logic-apps-overview.md#logic-app-concepts) amelyek segítségével elindíthatja a logikai alkalmazást. Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. Minden alkalommal, amikor az eseményindító aktiválódik, az Azure Logic Apps létrehoz egy munkafolyamat-példányt, amely futtatja a logikai alkalmazást.

## <a name="add-an-event-grid-trigger"></a>Eseményrács eseményindítójának hozzáadása

Most adja hozzá az Event Grid eseményindítót, amellyel figyelheti a virtuális gép erőforráscsoportját.

1. A tervező keresőmezőjébe írja `event grid` be szűrőként. Az eseményindítók listájában válassza a **Mikor történik egy erőforrásesemény** eseményindító.

   ![Válassza ki ezt az eseményindítót: "Erőforrás-eseményen"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Amikor a rendszer kéri, jelentkezzen be az Azure Event Grid be az Azure-fiók hitelesítő adataival. A **Bérlő** listában, amely az Azure-előfizetéshez társított Azure Active Directory-bérlőt jeleníti meg, ellenőrizze, hogy a megfelelő bérlő jelenik-e meg, például:

   ![Bejelentkezés az Azure-beli hitelesítő adatokkal](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Ha személyes Microsoft-fiókkal jelentkezett be (például @outlook.com vagy @hotmail.com), az Event Grid-trigger esetleg nem megfelelően jelenik meg. Kerülő megoldásként válassza a [Csatlakozás egyszerű szolgáltatáshoz](../active-directory/develop/howto-create-service-principal-portal.md)lehetőséget, vagy hitelesítse magát az Azure-előfizetéséhez társított Azure Active Directory tagjaként, például *a felhasználónév*@emailoutlook.onmicrosoft.comlehetőséget.

1. Most iratkozz on logikai app az eseményeka közzétevőtől. Adja meg az esemény-előfizetés részleteit az alábbi táblázatban leírtak szerint, például:

   ![Eseményfeliratkozás adatainak megadása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   | -------- | -------- | ----- | ----------- |
   | **Előfizetés** | Igen | <*esemény-közzétevő-Azure-előfizetés-név*> | Válassza ki az *esemény közzétevőjéhez*társított Azure-előfizetés nevét. Ebben az oktatóanyagban válassza ki az Azure-előfizetés nevét a virtuális géphez. |
   | **Erőforrás típusa** | Igen | <*esemény-közzétevő-Azure-erőforrás-típus*> | Válassza ki az esemény közzétevőjének Azure-erőforrástípusát. Az Azure-erőforrástípusokról az [Azure-erőforrás-szolgáltatók és -típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakörben talál további információt. Ebben az oktatóanyagban válassza ki az `Microsoft.Resources.ResourceGroups` Azure-erőforráscsoportok figyeléséhez az értéket. |
   | **Erőforrás neve** |  Igen | <*esemény-közzétevő-Azure-erőforrás-név*> | Válassza ki az esemény közzétevőjének Azure-erőforrásnevét. Ez a lista a kiválasztott erőforrástípustól függ. Ebben az oktatóanyagban válassza ki a virtuális gépet tartalmazó Azure-erőforráscsoport nevét. |
   | **Eseménytípus-elem** |  Nem | <*eseménytípusok*> | Jelöljön ki egy vagy több konkrét eseménytípust, amelyet szűrni szeretne, és el szeretne küldeni az eseményrácsra. Tetszés szerint hozzáadhatja például ezeket az eseménytípusokat, hogy észlelje, mikor változnak vagy törlődnek az erőforrások: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>További információt az alábbi témakörökben talál: <p><p>- [Azure Event Grid eseménysémája erőforráscsoportokhoz](../event-grid/event-schema-resource-groups.md) <br>- [Az eseményszűrés ismertetése](../event-grid/event-filtering.md) <br>- [Eseményrács eseményeinek szűrése](../event-grid/how-to-filter-events.md) |
   | Választható tulajdonságok hozzáadásához válassza **az Új paraméter hozzáadása**lehetőséget, majd jelölje ki a kívánt tulajdonságokat. | Nem | {lásd a leírásokat} | * **Előtagszűrő:** Ebben az oktatóanyagban hagyja üresen ezt a tulajdonságot. Az alapértelmezett beállítás minden értéket megenged. Azonban megadhat szűrőként egy előtagsztringet is, például egy elérési útvonalat és egy adott erőforrás paraméterét. <p>* **Utótagszűrő:** Ebben az oktatóanyagban hagyja üresen ezt a tulajdonságot. Az alapértelmezett beállítás minden értéket megenged. Azonban megadhat szűrőként egy utótagsztringet is, például egy fájlnévkiterjesztést, ha csak adott fájltípusokra kíváncsi. <p>* **Előfizetés neve**: Ebben az oktatóanyagban egyedi nevet adhat meg az esemény-előfizetéshez. |
   |||

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.** Ha össze szeretne csukni és el szeretné rejteni egy művelet részleteit a logikai alkalmazásban, jelölje ki a művelet címsorát.

   ![A logikai alkalmazás mentése](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Amikor ment egy eseményrács-triggerrel rendelkező logikai alkalmazást, az Azure automatikusan létrehoz egy eseményfeliratkozást a logikai alkalmazás számára a kiválasztott erőforráshoz. Így amikor az erőforrás közzétesz egy eseményt az eseményrácson, az eseményrács automatikusan leküldi az eseményt a logikai alkalmazásnak. Az esemény aktiválja a logikai alkalmazást, majd létrehozza és futtatja a következő lépésekben meghatározott munkafolyamat egy példányát.

A logikai alkalmazás most már működőképes, és figyeli az eseményrácsról érkező eseményeket, de amíg fel nem vesz a munkafolyamatba műveleteket, semmit nem csinál.

## <a name="add-a-condition"></a>Feltétel felvétele

Ha azt szeretné, hogy a logikai alkalmazás csak akkor fusson, amikor `Microsoft.Compute/virtualMachines/write` egy adott esemény vagy művelet történik, adjon hozzá egy feltételt, amely ellenőrzi a műveletet. Ha ez a feltétel teljesül, a logikai alkalmazás e-mailben elküldi a frissített virtuális gép adatait.

1. A Logic App Designer eseményindítója alatt válassza az **Új lépés**lehetőséget.

   ![Válassza az "Új lépés" lehetőséget](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. A **Művelet kiválasztása csoport**keresőmezőjében `condition` írja be szűrőként jelölőnégyzetet. A műveletek listájában válassza a **Feltétel** műveletet.

   ![Feltétel felvétele](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   A Logic App Designer hozzáad egy üres feltételt a munkafolyamathoz, beleértve a feltétel igaz vagy hamis értékei esetén követendő műveleti útvonalakat.

   ![Üres feltétel jelenik meg](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Nevezze át a `If a virtual machine in your resource group has changed`feltétel címét . A feltétel címsorán jelölje ki a három pont (**...**) gombot, majd az **Átnevezés gombot.**

   ![A feltétel átnevezése](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Hozzon létre egy `body` feltételt, amely ellenőrzi az eseményt egy `data` olyan objektumra, amelyben a `operationName` tulajdonság megegyezik a `Microsoft.Compute/virtualMachines/write` művelettel. További tudnivalók az [Event Grid eseménysémáról](../event-grid/event-schema.md).

   1. Az első sorban az **And** (És) alatt kattintson a bal oldali mezőbe. A megjelenő dinamikus tartalomlistában válassza a **Kifejezés**lehetőséget.

      ![A kifejezésszerkesztő megnyitásához válassza a "Kifejezés" lehetőséget](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. A kifejezésszerkesztőbe írja be ezt a kifejezést, amely a művelet nevét adja vissza az eseményindítóból, és válassza az **OK**gombot:

      `triggerBody()?['data']['operationName']`

      Példa:

      ![A művelet nevének kinyeréséhez írjon be kifejezést](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. A középső mezőben tartsa meg az **is equal to** (egyenlő) operátort.

   1. A jobb oldali mezőbe írja be ezt az értéket, amely a figyelni kívánt művelet:

      `Microsoft.Compute/virtualMachines/write`

   A kész állapot most így néz ki:

   ![A műveletet összehasonlító befejezett feltétel](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Ha tervező nézetről kódnézetre, majd vissza a tervező nézetre vált, a feltételben megadott kifejezés a **data.operationName** tokenre lesz:

   ![Megoldott jogkivonatok állapotban](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Mentse a logikai alkalmazást.

## <a name="send-email-notifications"></a>E-mail-értesítések küldése

Most adjon hozzá egy [*műveletet,*](../logic-apps/logic-apps-overview.md#logic-app-concepts) hogy e-mailt kaphasson, ha a megadott feltétel teljesül.

1. A **Feltétel Ha igaz** mezőben válassza a Művelet **hozzáadása**lehetőséget.

   ![Művelet hozzáadása a feltétel igaz értéke esetére](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. A **Művelet kiválasztása csoport**keresőmezőjében `send an email` írja be szűrőként jelölőnégyzetet. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ezután válassza ki az „e-mail küldése” műveletet az összekötőn. Példa:

   * Azure munkahelyi vagy iskolai fiókok esetében válassza az Office 365 Outlook-összekötőt.

   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

   * Gmail-fiókok esetében válassza a Gmail-összekötőt.

   Ez az oktatóanyag az Office 365 Outlook-összekötővel folytatódik. Ha másik szolgáltatót használ, a lépések változatlanok maradnak, de a felhasználói felület kissé eltérőnek tűnhet.

   ![Az „e-mail küldése” művelet kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Ha még nem rendelkezik kapcsolattal az e-mail-szolgáltatójához, amikor a rendszer kéri, hogy hitelesítse magát, jelentkezzen be az e-mail-fiókjába.

1. Nevezze át az e-mail küldése műveletet erre a címre:`Send email when virtual machine updated`

1. Adja meg az e-mailről az alábbi táblázatban megadott információkat:

   ![Az e-mailművelettel kapcsolatos információk biztosítása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Ha a munkafolyamat előző lépéseiben szeretné kiválasztani a kimenetet, kattintson egy szerkesztési mezőben, hogy megjelenjen a dinamikus tartalomlista, vagy válassza a **Dinamikus tartalom hozzáadása**lehetőséget. További eredményekért válassza a **További információk** a lista egyes szakaszaihoz lehetőséget. A dinamikus tartalomlista bezárásához válassza ismét a **Dinamikus tartalom hozzáadása** lehetőséget.

   | Tulajdonság | Kötelező | Érték | Leírás |
   | -------- | -------- | ----- | ----------- |
   | **Címzett** | Igen | <*címzett\@tartománya*> | Adja meg a címzett e-mail-címét. Tesztelési célokra használhatja a saját e-mail-címét. |
   | **Tárgy** | Igen | `Resource updated:`**Tárgy** | Adja meg az e-mail tárgymezőjének tartalmát. Ebben az oktatóanyagban adja meg a megadott szöveget, és jelölje ki az esemény **Tárgy mezőjét.** Itt az e-mail tárgya a frissített erőforrás (virtuális gép) nevét tartalmazza. |
   | **Törzs** | Igen | `Resource:`**Téma** <p>`Event type:` **Eseménytípus**<p>`Event ID:`**Azonosító**<p>`Time:`**Esemény ideje** | Adja meg az e-mail törzsének tartalmát. Ebben az oktatóanyagban adja meg a megadott szöveget, és válassza ki az esemény **Témakör**, **Eseménytípusa**, **ID**és **Eseményidő** mezőket, hogy az e-mail tartalmazza az eseményt, eseménytípust, eseményidőbélyeget és eseményazonosítót a frissítéshez. Ebben az oktatóanyagban az erőforrás az eseményindítóban kiválasztott Azure-erőforráscsoport. <p>Ha üres sorokat kíván beszúrni a tartalomba, nyomja le a Shift + Enter billentyűkombinációt. |
   ||||

   > [!NOTE]
   > Ha olyan mezőt választ ki, amely egy tömböt jelöl, a tervező automatikusan hozzáad egy **Mindegyikre** hurkot a tömbre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja az adott műveletet.

   Most az e-mail-művelet a következő példához hasonló lehet:

   ![Az e-mailben szerepeltetni kívánt kimenetek kiválasztása](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   A befejezett logikai alkalmazás pedig a következő példához hasonló lehet:

   ![Befejezett logikai alkalmazás](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Mentse a logikai alkalmazást. Ha össze szeretné csukni és el szeretné rejteni az egyes műveletek részleteit a logikai alkalmazásban, jelölje ki a művelet címsorát.

   A logikai alkalmazás most már működőképes, de mielőtt bármit is tenne, vár a virtuális gép változásaira. A logikai alkalmazás teszteléséhez folytassa a következő szakasszal.

## <a name="test-your-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának tesztelése

1. Annak ellenőrzéséhez, hogy a logikai alkalmazás megkapja-e a megadott eseményeket, frissítse a virtuális gépet.

   Például átméretezheti a virtuális gépet az Azure Portalon vagy az [Azure PowerShell használatával](../virtual-machines/windows/resize-vm.md).

   Pár pillanat múlva egy e-mailt kell kapnia. Példa:

   ![A virtuális gép módosításáról tájékoztató e-mail](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. A logikai alkalmazás futtatási és eseményindító előzményeinek áttekintéséhez a logikai alkalmazás menüjében válassza **az Áttekintés lehetőséget.** A futtatással kapcsolatos további részletek megtekintéséhez jelölje ki a futtatás sorát.

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

* Ha a munkája törlése nélkül kívánja leállítani a logikai alkalmazást, akkor tiltsa le. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget. Az eszköztáron válassza a **Letiltás gombot.**

  ![A logikai alkalmazás kikapcsolása](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

* A logikai alkalmazás végleges törléséhez válassza a logikai alkalmazás **menüjének Áttekintés parancsát.** Az eszköztáron válassza a **Törlés**gombot. Erősítse meg, hogy törölni szeretné a logikai alkalmazást, és válassza a **Törlés**gombot.

## <a name="next-steps"></a>További lépések

* [Egyéni események létrehozása és átirányítása az Event Griddel](../event-grid/custom-event-quickstart.md)
