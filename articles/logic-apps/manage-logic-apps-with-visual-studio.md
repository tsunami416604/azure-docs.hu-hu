---
title: Logikai alkalmazások szerkesztése és kezelése a Visual Studio és a Cloud Explorer használatával
description: A Visual Studio és a Cloud Explorer segítségével szerkesztheti, frissítheti, kezelheti, hozzáadhatja és üzembe helyezheti a logikai alkalmazásokat
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 6319d2d72df69cc9633bd2b2ff8e777c2a48966a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270250"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Logikai alkalmazások kezelése a Visual Studióval

Bár hozhat létre, szerkeszthet, kezelhet és helyezhet üzembe logikai alkalmazásokat az [Azure Portalon,](https://portal.azure.com)a Visual Studio-t is használhatja, ha hozzá szeretné adni a logikai alkalmazásokat a forrásvezérléshez, különböző verziókat tehet közzé, és [Azure Resource Manager-sablonokat](../azure-resource-manager/management/overview.md) hozhat létre a különböző központi telepítési környezetekhez. A Visual Studio Cloud Explorer segítségével megtalálhatja és kezelheti a logikai alkalmazásokat más Azure-erőforrásokkal együtt. Például megnyithatja, letöltheti, szerkesztheti, futtathatja, megtekintheti a futtatási előzményeket, letilthatja és engedélyezheti az Azure Portalon már telepített logikai alkalmazásokat. Ha most ismerkedik az Azure Logic Apps alkalmazásokkal a Visual Studióban, ismerje meg, [hogyan hozhat létre logikai alkalmazásokat a Visual Studióval.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

> [!IMPORTANT]
> A Visual Studio logikai alkalmazás üzembe helyezése vagy közzététele felülírja az alkalmazás verzióját az Azure Portalon. Ha tehát olyan módosításokat hajt végre az Azure Portalon, amelyeket meg szeretne tartani, frissítse [a logikai alkalmazást a Visual Studióban](#refresh) az Azure Portalról, mielőtt legközelebb üzembe helyezne vagy közzétenné a Visual Studióból.

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * [Visual Studio 2019, 2017 vagy 2015 – közösségi kiadás vagy újabb](https://aka.ms/download-visual-studio). Ez a rövid útmutató a Visual Studio Community 2017 kiadást használja, amely ingyenes.

    > [!IMPORTANT]
    > A Visual Studio 2019 vagy 2017 telepítésekor győződjön meg arról, hogy az **Azure fejlesztési** számítási feladatait választja.
    > További információt az [Azure-fiókokhoz társított erőforrások kezelése a Visual Studio Cloud Explorer ben című témakörben](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view)talál.

    A Cloud Explorer for Visual Studio 2015 telepítéséhez [töltse le a Cloud Explorer alkalmazást a Visual Studio Piactérről.](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015) További információt az [Azure-fiókokhoz társított erőforrások kezelése a Visual Studio Cloud Explorerben (2015) című témakörben](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015)talál.

  * [Azure SDK (2.9.1 vagy újabb)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * A visual studio-bővítmény legújabb Azure Logic Apps-eszközei a kívánt verzióhoz:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

* Hozzáférés a webhez a beágyazott Logic Apps Designer használata közben

  A tervezőnek internetkapcsolatra van szüksége, hogy létre tudja hozni az erőforrásokat az Azure-ban, és be tudja olvasni a tulajdonságokat és adatokat a logikai alkalmazásban lévő összekötőkből. Ha például a Dynamics CRM Online-összekötőt használja, a tervező elérhető alapértelmezett és egyéni tulajdonságokat keres a CRM-példányon.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>A logikai alkalmazások megkeresése

A Visual Studio-ban megtalálhatja az Azure-előfizetéséhez társított és az Azure-portálon a Cloud Explorer használatával telepített összes logikai alkalmazást.

1. Nyissa meg a Visual Studiót. A **Nézet** menüben válassza a **Cloud Explorer**parancsot.

1. A Cloud Explorer ben válassza **a Fiókkezelés**lehetőséget. Válassza ki a logikai alkalmazásokkal társított Azure-előfizetést, majd válassza az **Alkalmaz**lehetőséget. Példa:

   ![Válassza a "Fiókkezelés" lehetőséget](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Attól függően, hogy **erőforráscsoportok** vagy **erőforrástípusok**szerint keres, kövesse az alábbi lépéseket:

   * **Erőforráscsoportok:** Az Azure-előfizetés, Cloud Explorer megjeleníti az összes erőforráscsoportok, amelyek az adott előfizetéstársított. Bontsa ki a logikai alkalmazást tartalmazó erőforráscsoportot, majd válassza ki a logikai alkalmazást.

   * **Erőforrástípusok:** Az Azure-előfizetésalatt bontsa ki a **Logic Apps csomópontot.** Miután a Cloud Explorer megjeleníti az összes üzembe helyezett logikai alkalmazások, amelyek az előfizetéshez társított, válassza ki a logikai alkalmazást.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Megnyitás a Visual Studióban

A Visual Studióban megnyithatja a korábban létrehozott és üzembe helyezett logikai alkalmazásokat közvetlenül az Azure Portalon keresztül, vagy a Visual Studio Azure Resource Group-projektekként.

1. Nyissa meg a Cloud Explorert, és keresse meg a logikai alkalmazást.

1. A logikai alkalmazás helyi menüjében válassza a **Megnyitás logikai alkalmazásszerkesztővel**lehetőséget.

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a Visual Studio legújabb frissítéseivel.

   Ebben a példában a logikai alkalmazások at jeleníti meg erőforrástípus szerint, így a logikai alkalmazások a **Logikai alkalmazások** szakaszban jelennek meg.

   ![Telepített logikai alkalmazás megnyitása az Azure Portalról](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Miután a logikai alkalmazás megnyílik a Logic Apps Designerben, a tervező alján kiválaszthatja a **Kódnézet lehetőséget,** hogy áttekinthesse az alapul szolgáló logikai alkalmazásdefiníciós struktúrát. Ha egy központi telepítési sablont szeretne létrehozni a logikai alkalmazáshoz, ismerje meg, [hogyan tölthet le egy Azure Resource Manager-sablont](#download-logic-app) az adott logikai alkalmazáshoz. További információ az [Erőforrás-kezelő sablonjairól](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Letöltés az Azure-ból

A logikai alkalmazásokat letöltheti az [Azure Portalról,](https://portal.azure.com) és mentheti őket [Azure Resource Manager-sablonként.](../azure-resource-manager/management/overview.md) Ezután helyileg szerkesztheti a sablonokat a Visual Studio segítségével, és testreszabhatja a logikai alkalmazásokat a különböző központi telepítési környezetekben.  A logikai alkalmazások letöltése automatikusan *paraméterezi* a definíciókat az [Erőforrás-kezelő sablonokon](../azure-resource-manager/templates/overview.md)belül, amelyek javascript-objektumnotítást (JSON) is használnak.

1. A Visual Studio programban nyissa meg a Cloud Explorer t. Keresse meg és válassza ki az Azure-ból letölteni kívánt logikai alkalmazást.

1. Az alkalmazás helyi menüjében válassza a **Megnyitás logikai alkalmazásszerkesztővel**lehetőséget.

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a Visual Studio legújabb frissítéseivel.

   A Logic App Designer megnyílik, és megjeleníti a logikai alkalmazást. A logikai alkalmazás alapjául szolgáló definíció és struktúra áttekintéséhez a tervező alján válassza a **Kódnézet**lehetőséget.

1. A tervező eszköztárán válassza a **Letöltés**gombot.

   ![Logikai alkalmazás letöltése az Azure Portalról](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Amikor a rendszer rákérdez egy helyre, keresse meg azt a helyet, és mentse az Erőforrás-kezelő sablont a logikai alkalmazás definíciójához JSON (.json) fájlformátumban.

   A logikai alkalmazás `resources` definíciója az Erőforrás-kezelő sablon alszakaszában jelenik meg. Most már szerkesztheti a logikai alkalmazás definícióját és az Erőforrás-kezelő sablont a Visual Studio segítségével. A sablont [Azure Resource Group-projektként](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) is hozzáadhatja egy Visual Studio-megoldáshoz. Ismerje meg az [Azure Resource Group-projekteket a Visual Studióban a logikai alkalmazásokhoz.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Hivatkozás az integrációs fiókhoz

A vállalati integrációs forgatókönyvekhez szükséges logikai alkalmazások létrehozásához csatolhatja a logikai alkalmazást egy korábban létrehozott [integrációs fiókhoz,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) amely ugyanabban a régióban létezik, mint a logikai alkalmazás. Az integrációs fiók B2B-összetevőket tartalmaz, például kereskedelmi partnereket, megállapodásokat, sémákat és leképezéseket, és lehetővé teszi, hogy a logikai alkalmazás B2B-összekötőket használjon xml-érvényesítéshez és egyszenkenel történő kódoláshoz. Bár [ezt a kapcsolatot az Azure Portal használatával](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)is létrehozhatja, az előfeltételek teljesítése után is használhatja a Visual [Studio-t,](#requirements)és a logikai alkalmazás JSON (.json) fájlként létezik egy [Azure Resource Group projektben.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Ismerje meg az [Azure Resource Group-projekteket a Visual Studióban a logikai alkalmazásokhoz.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)

1. A Visual Studióban nyissa meg az Azure Resource Group projektet, amely a logikai alkalmazást tartalmazza.

1. A Megoldáskezelőben nyissa meg a **<logikai alkalmazásnév>.json** fájl helyi menüjét, és válassza **a Megnyitás a Logikai alkalmazástervezővel parancsot.** (Billentyűzet: Ctrl + L)

   ![Logikai alkalmazás .jsonfájljának megnyitása a Logic App Designer alkalmazással](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a Visual Studio és az Azure Logic Apps Tools bővítmény legújabb frissítéseivel.

1. Győződjön meg arról, hogy a Logic App Designer rendelkezik a fókusz kiválasztásával a tervező lap vagy felület, hogy a Tulajdonságok ablakban megjelenik az **integrációs fiók** tulajdonsága a logikai alkalmazás.

   ![Tulajdonságok ablak – "Integrációs számla" tulajdonság](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Ha a Tulajdonságok ablak még nincs megnyitva, a **Nézet** menüben válassza a **Tulajdonságok ablak parancsot.** (Billentyűzet: Nyomja meg az F4 billentyűt)

1. Nyissa meg az **Integrációs fiók** tulajdonságlistát, és válassza ki a logikai alkalmazáshoz kapcsolni kívánt integrációs fiókot, például:

   !["Integrációs fiók" tulajdonságlista megnyitása](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Ha elkészült, ne felejtse el menteni a Visual Studio-megoldást.

Ha beállítja az **integrációs fiók** tulajdonságot a Visual Studióban, és a logikai alkalmazást Azure Resource Manager-sablonként menti, az adott sablon a kijelölt integrációs fiók paraméterdeklarációját is tartalmazza. A sablonparaméterekről és a logikai alkalmazásokról az [Áttekintés: Logikai alkalmazások üzembe helyezésének automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)című témakörben olvashat bővebben.

<a name="change-location"></a>

## <a name="change-deployment-location"></a>A telepítés helyének módosítása

A Visual Studio,ha a logikai alkalmazás létezik JSON (.json) fájlként egy [Azure Resource Group projekt,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) amely a központi telepítés automatizálására használt, hogy a logikai alkalmazás van beállítva, hogy egy helytípusát és egy adott helyen. Ez a hely egy Azure-régió vagy egy meglévő [integrációs szolgáltatási környezet (ISE).](connect-virtual-network-vnet-isolated-environment.md)

A logikai alkalmazás helytípusának vagy helyének módosításához meg kell nyitnia a logikai alkalmazás munkafolyamat-definíciós (.json) fájlját a Solution Explorerből a Logic App Designer használatával. Ezek a tulajdonságok nem módosíthatók a Cloud Explorer használatával.

> [!IMPORTANT]
> A helytípus **régióról** [**integrációs szolgáltatáskörnyezetre**](connect-virtual-network-vnet-isolated-environment-overview.md) történő módosítása hatással van a logikai alkalmazás számlázási, [korlátok](logic-apps-limits-and-config.md#integration-account-limits), [integrációs fiók támogatásés](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)így tovább használt [díjszabási modelljére.](logic-apps-pricing.md#fixed-pricing) Mielőtt másik helytípust választana ki, győződjön meg arról, hogy megértette az ebből eredő hatást a logikai alkalmazásra.

1. A Visual Studióban nyissa meg az Azure Resource Group projektet, amely a logikai alkalmazást tartalmazza.

1. A Megoldáskezelőben `<logic-app-name>.json` nyissa meg a fájl helyi menüjét, és válassza **a Megnyitás a Logikai alkalmazástervezővel**lehetőséget. (Billentyűzet: Ctrl + L)

   ![Logikai alkalmazás .jsonfájljának megnyitása a Logic App Designer alkalmazással](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a Visual Studio és az Azure Logic Apps Tools bővítmény legújabb frissítéseivel.

1. Győződjön meg arról, hogy a Logic App Designer rendelkezik a fókuszkiválasztásával a tervező lapon vagy felületen, hogy a Tulajdonságok ablakban megjelenik a **Helytípus** és **hely** tulajdonságainak kiválasztása a logikai alkalmazáshoz. A projekt helytípusa **Régió** vagy **Integrációs szolgáltatás környezet.**

   ![Tulajdonságok ablak - "Helytípus kiválasztása" & "Hely" tulajdonságai](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Ha a Tulajdonságok ablak még nincs megnyitva, a **Nézet** menüben válassza a **Tulajdonságok ablak parancsot.** (Billentyűzet: Nyomja meg az F4 billentyűt)

1. A helytípus módosításához nyissa meg a **Helytípus kiválasztása** tulajdonságlistát, és válassza ki a kívánt helytípust.

   Ha például a hely típusa **Integrációs szolgáltatáskörnyezet,** válassza a **Régió**lehetőséget.

   !["Helytípus kiválasztása" tulajdonság – helytípus módosítása](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Az adott hely módosításához nyissa meg a **Hely** tulajdonságlistát. A hely típusa alapján válassza ki a kívánt helyet, például:

   * Válasszon másik Azure-régiót:

     ![Nyissa meg a "Location" tulajdonságlistát, válasszon másik Azure-régiót](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Válasszon másik ISE-t:

     ![Nyissa meg a "Location" tulajdonságlistát, válasszon másik ISE-t](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Ha elkészült, ne felejtse el menteni a Visual Studio-megoldást.

Ha módosítja a hely típusát vagy helyét a Visual Studióban, és a logikai alkalmazást Azure Resource Manager-sablonként menti, az adott sablon az adott helytípusra és helyre vonatkozó paraméterdeklarációkat is tartalmaz. A sablonparaméterekről és a logikai alkalmazásokról az [Áttekintés: Logikai alkalmazások üzembe helyezésének automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)című témakörben olvashat bővebben.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Frissítés az Azure-ból

Ha módosítja a logikai alkalmazást az Azure Portalon, és meg szeretné tartani ezeket a módosításokat, győződjön meg arról, hogy frissíti az alkalmazás verzióját a Visual Studióban ezekkel a módosításokkal.

* A Visual Studio Logikai alkalmazástervező eszközsorában válassza a **Frissítés**lehetőséget.

  – vagy –

* A Visual Studio Cloud Explorer ben nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **Frissítés**gombot.

![Logikai alkalmazás frissítése frissítésekkel](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>A logikai alkalmazások frissítéseinek közzététele

Ha készen áll a logic app frissítéseinek üzembe helyezésére a Visual Studio-ból az Azure-ba, a Logic App Designer eszköztáron válassza a **Közzététel**lehetőséget.

![Frissített logikai alkalmazás közzététele az Azure Portalon](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>A logikai alkalmazás manuális futtatása

Manuálisan is elindíthatja az Azure-ban üzembe helyezett logikai alkalmazást a Visual Studióból. A Logic App Designer eszközsorában válassza az **Eseményindító futtatása**lehetőséget.

![Manuálisan futtathatja az eseményindítót a logikai alkalmazáshoz](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Futtatási előzmények áttekintése

Az állapot ellenőrzéséhez és a logikai alkalmazás futtatásokkal kapcsolatos problémák diagnosztizálásához tekintse át a Visual Studióban futtatott adatokat, például a bemeneteket és a kimeneteket.

1. A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza **a Futtatási előzmények megnyitása**lehetőséget.

   ![A logikai alkalmazás futtatási előzményeinek megnyitása](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Egy adott futtatás részleteinek megtekintéséhez kattintson duplán egy futtatásra. Példa:

   ![Adott futtatással kapcsolatos információk megtekintése](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > A táblázat tulajdonság szerint való rendezéséhez jelölje ki a tulajdonság oszlopfejlécét.

1. Bontsa ki azokat a lépéseket, amelyek által áttekinteni kívánt bemeneteket és kimeneteket szeretné áttekinteni, például:

   ![Az egyes lépcsők bemenetének és kimenetének megtekintése](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Logikai alkalmazás letiltása vagy engedélyezése

A logikai alkalmazás törlése nélkül leállíthatja az eseményindító indítását a következő alkalommal, amikor az eseményindító feltétel teljesül. A logikai alkalmazás letiltása megakadályozza, hogy a Logic Apps motor jövőbeli munkafolyamat-példányokat hozzon létre és futtasson a logikai alkalmazáshoz. A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza **a Letiltás gombot.**

![A logikai alkalmazás letiltása a Cloud Explorerben](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> Ha letilt egy logikai alkalmazást, a nem hoz új futtatásokat a példányosítani. Az összes folyamatban lévő és függőben lévő futtatás a befejezésükig folytatódik, ami időbe telhet.

A logikai alkalmazás újraaktiválásához a Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüjét, és válassza **az Engedélyezés parancsot.**

![Logikai alkalmazás engedélyezése a Cloud Explorerben](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>A logikai alkalmazás törlése

Ha törölni szeretné a logikai alkalmazást az Azure Portalról, nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **Törlés parancsot.**

![A logikai alkalmazás törlése az Azure Portalról](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat. 

## <a name="troubleshooting"></a>Hibaelhárítás

Amikor megnyitja a logikai alkalmazás projekt a Logic Apps Designer, előfordulhat, hogy nem kapja meg a lehetőséget az Azure-előfizetés kiválasztásához. Ehelyett a logikai alkalmazás megnyílik egy Azure-előfizetés, amely nem az, amit szeretne használni. Ez a viselkedés azért fordul elő, mert miután megnyitotta a logikai alkalmazás .json fájlját, a Visual Studio gyorsítótárazza az első kiválasztott előfizetést későbbi használatra. A probléma megoldásához próbálkozzon az alábbi lépésekkel:

* Nevezze át a logikai alkalmazás .jsonfájlját. Az előfizetés gyorsítótára a fájlnévtől függ.

* A *megoldásban lévő összes* logikai alkalmazás korábban kiválasztott előfizetésének eltávolításához törölje a megoldás címtárában lévő rejtett Visual Studio-beállítások mappát (.vs). Ez a hely tárolja az előfizetésadatait.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan kezelheti az üzembe helyezett logikai alkalmazásokat a Visual Studióval. Ezután ismerje meg a logikai alkalmazásdefiníciók üzembe helyezéshez való testreszabását:

> [!div class="nextstepaction"]
> [Logikai alkalmazásdefiníciók készítése a JSON-ban](../logic-apps/logic-apps-author-definitions.md)
