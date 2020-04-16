---
title: Logikai alkalmazások kezelése az Azure Portalon
description: Az Azure Portal használatával szerkesztheti, engedélyezheti, letilthatja vagy törölheti a logikai alkalmazásokat.
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415994"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>Logikai alkalmazások kezelése az Azure Portalon

A logikai alkalmazásokat az [Azure Portalon](https://portal.azure.com) vagy a [Visual Studio-ban](manage-logic-apps-with-visual-studio.md)kezelheti. Ez a cikk bemutatja, hogyan szerkesztheti, letilthatja, engedélyezheti vagy törölheti a logikai alkalmazásokat az Azure Portalon. Ha most jön az Azure Logic Apps, olvassa el [a Mi az Azure Logic Apps?](logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy meglévő logikai alkalmazás. Ha meg szeretné tudni, hogyan hozhat létre logikai alkalmazást az Azure Portalon, olvassa el a [rövid útmutató: Az első munkafolyamat létrehozása az Azure Logic Apps – Azure Portal használatával című témakört.](quickstart-create-first-logic-app-workflow.md)

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>A logikai alkalmazások megkeresése

A logikai alkalmazás megkereséséhez és megnyitásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

1. Az Azure keresősávjában `logic apps`írja be a t, és válassza a **Logic Apps**lehetőséget.

   ![A "Logic Apps" megkeresése és kiválasztása](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. A **Logic Apps** lapon keresse meg és jelölje ki a kezelni kívánt logikai alkalmazást.

   A logikai alkalmazás **áttekintő** ablaktáblájának megnyitása után a következő módokon szűrheti a **Logikai alkalmazások** lapon megjelenő listát:

   * Logikai alkalmazások keresése név szerint
   * Logikai alkalmazások szűrése előfizetés, erőforráscsoport, hely és címkék szerint
   * Logikai alkalmazások csoportosítása erőforráscsoport, típus, előfizetés és hely szerint

## <a name="view-logic-app-properties"></a>Logikai alkalmazás tulajdonságainak megtekintése

1. Az Azure Portalon [keresse meg és nyissa meg a logikai alkalmazást.](#find-logic-app)

1. A logikai alkalmazás menüjében a **Beállítások**csoportban válassza a **Tulajdonságok lehetőséget.**

1. A **Tulajdonságok** ablaktáblán megtekintheti és átmásolhatja a logikai alkalmazással kapcsolatos alábbi információkat:

   * **Név**
   * **Erőforrás azonosítója**
   * **Erőforráscsoport**
   * **Helyen**
   * **Típus** 
   * **Előfizetés neve**
   * **Előfizetés azonosítója**
   * **Hozzáférési végpont**
   * **Futásidejű kimenő IP-címek**
   * **Végpont IP-címei elérése**
   * **Kimenő összekötő IP-címei**

## <a name="disable-or-enable-logic-apps"></a>Logikai alkalmazások letiltása vagy engedélyezése

Az Azure Portalon engedélyezheti vagy letilthatja az azure-portálon [egy-egy logikai alkalmazást](#disable-enable-single-logic-app) vagy több [logikai alkalmazást.](#disable-or-enable-multiple-logic-apps) [A Visual Studio alkalmazásban is engedélyezheti vagy letilthatja a logikai alkalmazásokat.](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)

A logikai alkalmazás letiltása hatással van a munkafolyamat-példányokra, és az alábbi módokon fut:

* Minden folyamatban lévő és függőben lévő fut folytatódik, amíg be nem fejeződnek. A futtatások számától függően ez a folyamat eltarthat egy ideig.

* A Logic Apps motor nem hoz létre és nem futtat új munkafolyamat-példányokat.

* A ravasz nem aktiválódik, amikor a következő feltételek teljesülnek.

* Az eseményindító állapota megjegyzi azt a pontot, ahol a logikai alkalmazás leállt. Így ha újra engedélyezi a logikai alkalmazást, az eseményindító az utolsó futtatás óta az összes feldolgozatlan elemhez aktiválódik.

  Ha le szeretné állítani, hogy a logikai alkalmazás az utolsó futtatás óta ne indítson el feldolgozatlan elemeket, törölje az eseményindító állapotát, mielőtt újra engedélyezne a logikai alkalmazást:

  1. Az Azure Portalon [keresse meg és nyissa meg a logikai alkalmazást.](#find-logic-app)

  1. A logikai alkalmazás eseményindítójának bármely részét szerkesztheti.

  1. Mentse a módosításokat. Ez a lépés visszaállítja az eseményindító aktuális állapotát.

  1. [Engedélyezze újra a logikai alkalmazást.](#disable-enable-single-logic-app)

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>Egyetlen logikai alkalmazás letiltása vagy engedélyezése

1. Az Azure Portalon [keresse meg és nyissa meg a logikai alkalmazást.](#find-logic-app)

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget. Válasszon az alábbi lehetőségek közül:

   * Az eszköztáron válassza a **Letiltás gombot.**

     ![Egyetlen logikai alkalmazás letiltása az Azure Portalon](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     Ha a logikai alkalmazás már le van tiltva, csak az **Engedélyezés** lehetőség jelenik meg.

   * Az eszköztáron válassza az **Engedélyezés**lehetőséget.

     ![Egyetlen logikai alkalmazás engedélyezése az Azure Portalon](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     Ha a logikai alkalmazás már engedélyezve van, csak a **Letiltás** lehetőség jelenik meg. 

   Az Azure Portalon megjelenik egy értesítés a fő Azure-eszköztáron, amely megerősíti, hogy a művelet sikeres vagy sikertelen volt.

   ![Értesítés a művelet állapotának megerősítéséhez](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>Több logikai alkalmazás letiltása vagy engedélyezése

1. Az Azure Portalon keresse meg a letiltani vagy engedélyezni kívánt [logikai alkalmazásokat.](#find-logic-app)

1. Annak ellenőrzéséhez, hogy egy logikai alkalmazás jelenleg engedélyezve van-e vagy le van tiltva, a **Logic Apps** lapon tekintse át az adott logikai alkalmazás **Állapot** oszlopát. 

   ![Logic Apps állapotoszlop](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   Ha az **Állapot** oszlop nem látható, a **Logic Apps** eszköztáron válassza az Előzetes **verzió kipróbálásával**lehetőséget.

   ![Az előnézet bekapcsolása](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. A jelölőnégyzet oszlopban jelölje ki a letiltani vagy engedélyezni kívánt logikai alkalmazásokat. Az eszköztáron válassza a **Letiltás** vagy **az Engedélyezés**lehetőséget.

   ![Több logikai alkalmazás engedélyezése vagy letiltása az Azure Portalon](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. Amikor megjelenik a megerősítő mező, a folytatáshoz válassza az **Igen** lehetőséget.

   Az Azure Portalon megjelenik egy értesítés a fő Azure-eszköztáron, amely megerősíti, hogy a művelet sikeres vagy sikertelen volt.

## <a name="delete-logic-apps"></a>Logikai alkalmazások törlése

Törölhet [egy logikai alkalmazást,](#delete-single-logic-app) vagy [egyszerre több logikai alkalmazást is törölhet az](#delete-multiple-logic-apps) Azure Portalon. A logikai alkalmazást a Visual Studio alkalmazásban is [törölheti.](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)

A logikai alkalmazás törlése a következő módokon befolyásolja a munkafolyamat-példányokat:

* Minden folyamatban lévő és függőben lévő fut folytatódik, amíg be nem fejeződnek. A futtatások számától függően ez a folyamat eltarthat egy ideig.

* A Logic Apps motor nem hoz létre és nem futtat új munkafolyamat-példányokat.

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>Egyetlen logikai alkalmazás törlése

1. Az Azure Portalon [keresse meg és nyissa meg a logikai alkalmazást.](#find-logic-app)

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget. A logikai alkalmazás eszköztárán válassza a **Törlés**lehetőséget.

   ![A logikai alkalmazás eszköztárán válassza a "Törlés" lehetőséget](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. Amikor megjelenik a megerősítő mező, adja meg a logikai alkalmazás nevét, és válassza a **Törlés gombot.**

   ![A logikai alkalmazás törlésének megerősítése](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Az Azure Portalon megjelenik egy értesítés a fő Azure-eszköztáron, amely megerősíti, hogy a művelet sikeres vagy sikertelen volt.

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>Több logikai alkalmazás törlése

1. Az Azure Portalon [keresse meg a törölni kívánt logikai alkalmazásokat.](#find-logic-app)

1. A jelölőnégyzet oszlopban jelölje ki a törölni kívánt logikai alkalmazásokat. Az eszköztáron válassza a **Törlés**gombot.

   ![Több logikai alkalmazás törlése](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. Amikor megjelenik a `yes`megerősítő mező, írja be a be parancsot, és válassza a **Törlés**lehetőséget.

   ![A logikai alkalmazások törlésének megerősítése](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Az Azure Portalon megjelenik egy értesítés a fő Azure-eszköztáron, amely megerősíti, hogy a művelet sikeres vagy sikertelen volt.

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>Logikai alkalmazásverziók kezelése

Használhatja az Azure Portalon a logikai alkalmazások verziószabályozásához. Megtalálhatja a logikai alkalmazás verzióelőzményeit, és előléptetheti a korábbi verziókat.

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>Korábbi verziók keresése és megtekintése

1. Az Azure Portalon [keresse meg a kezelni kívánt logikai alkalmazást.](#find-logic-app)

1. A logikai alkalmazás menüjében, a **Fejlesztőeszközök**csoportban válassza a **Verziók lehetőséget.**

   ![A logikai alkalmazás menüjében válassza a "Verziók" lehetőséget a "Fejlesztői eszközök" területen.](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. Válassza ki a logikai alkalmazás a listából kezelni kívánt **verzióját.** A lista szűréséhez **megadhatja** a verzióazonosítót a keresősávban.

1. Az **Előzmények verziólapján** az előző verzió részletei olvasható képpen jelennek meg. A Logic Apps **Designer** és a **Code nézet** módok közül választhat.

   ![A logikai alkalmazás kódnézettel és logikai alkalmazások tervezői nézetével rendelkező előzményverziói lapja](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>Korábbi verziók népszerűsítése

1. A logikai alkalmazás verzióelőzményeiközött [keresse meg és válassza ki az előléptetni kívánt verziót.](#find-version-history)

1. Az **Előzmények verziólapon** válassza **a Reklám lehetőséget.**

   ![Előléptetés gomb a logikai alkalmazás verzióelőzményeiközött](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. A **megnyíló Logic Apps Designer** lapon szükség szerint szerkesztheti a előléptetett verziót. Válthat a **Tervező** és a **Kód nézet** mód között. **A paramétereket,** **sablonokat**és **összekötőket**is frissítheti.

   ![Logic Apps Designer lap egy korábbi verzió népszerűsítéséhez](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. A frissítések mentéséhez és az előző verzió előléptetésének befejezéséhez válassza a **Mentés gombot.** (Vagy a módosítások megszakításához válassza az **Elvetés**lehetőséget.) 

   Amikor ismét [megtekinti a logikai alkalmazás verzióelőzményeit,](#find-version-history) az előléptetett verzió a lista tetején jelenik meg, és új azonosítóval rendelkezik.

## <a name="next-steps"></a>További lépések

* [Logikai alkalmazások figyelése](monitor-logic-apps.md)
