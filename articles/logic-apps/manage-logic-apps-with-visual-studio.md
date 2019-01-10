---
title: A Visual Studio használatával – Azure Logic Apps logikai alkalmazások kezelése |} A Microsoft Docs
description: A logic apps és a Visual Studio Cloud Explorer más Azure-objektumok kezelése
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.openlocfilehash: dbb38336f0521f8f8e526ad3f5e13829075e7cca
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158145"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Logikai alkalmazások Visual studióval kezelése

Is hozhat létre, szerkesztése, kezelése és a logic Apps-alkalmazások telepítése a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, használhatja a Visual Studio is, ha szeretne hozzáadni a források vezérléséhez, a különböző verziók közzétételéhez, és hozhat létre a logic apps [Azure-erőforrás Kezelő](../azure-resource-manager/resource-group-overview.md) sablonok különböző üzembe helyezési környezetekhez. A Visual Studio Cloud Explorer megkeresheti és a logic apps és más Azure-erőforrások kezeléséhez. Például, nyisson meg, töltse le, szerkesztése, futtatása, futtatási előzmények, tiltsa le és engedélyezze a logic apps már telepített megtekintése az Azure Portalon. Ha most ismerkedik, de az Azure Logic Apps a Visual Studióban, további [logikai alkalmazásokat hozhat létre a Visual Studio használatával hogyan](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Telepítése vagy egy logikai alkalmazást a Visual Studióból közzétételi felülírja az alkalmazás az Azure Portalon. Ezért ha módosítja az Azure Portalon, amelyeket meg szeretne tartani, ellenőrizze, hogy Ön [frissítse a logikai alkalmazás a Visual Studióban](#refresh) előtt legközelebb telepíteni, vagy a közzététel a Visual Studióból az Azure Portalról.

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>.

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 vagy Visual Studio 2015 – Community Edition vagy újabb</a>. 
  Ez a rövid útmutató a Visual Studio Community 2017 kiadást használja, amely ingyenes.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Azure SDK (2.9.1 vagy újabb)</a> és <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Azure Logic Apps Tools for Visual Studio 2017</a> vagy a <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015-ös verziója</a> 
  
    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">hogyan telepítheti a bővítményt a Visual Studio rendszerén belül</a>. 
    Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

* A beágyazott Logic Apps Designerben használatakor a hozzáférést

  A tervezőnek internetkapcsolatra van szüksége, hogy létre tudja hozni az erőforrásokat az Azure-ban, és be tudja olvasni a tulajdonságokat és adatokat a logikai alkalmazásban lévő összekötőkből. 
  Ha például a Dynamics CRM Online-összekötőt használja, a tervező elérhető alapértelmezett és egyéni tulajdonságokat keres a CRM-példányon.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Keresse meg a logic apps

A Visual Studióban található összes a logic Apps alkalmazásokat az Azure-előfizetéséhez társított, és a Cloud Explorer használatával az Azure Portalon vannak telepítve.

1. Nyissa meg a Visual Studiót. Az a **nézet** menüjében válassza **Cloud Explorer**.

2. A Cloud Explorerben válassza **fiókkezelés**. Válassza ki az Azure-előfizetése társítva a logic apps, majd válassza a **alkalmaz**. Példa:

   ![Válassza a "Fiók kezelése"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. E keresett által alapján **erőforráscsoportok** vagy **erőforrástípusok**, kövesse az alábbi lépéseket:

   * **Erőforráscsoportok**: Az Azure-előfizetéshez Cloud Explorer mutatja az adott előfizetéshez tartozó összes erőforráscsoport. 
   Bontsa ki az erőforráscsoportot, amely tartalmazza a logikai alkalmazást, majd válassza ki a logikai alkalmazást.

   * **Erőforrástípusok**: Bontsa ki az Azure-előfizetésben **Logic Apps**. Miután Cloud Explorer azt mutatja, az előfizetéséhez tartozó összes üzembe helyezett logikai alkalmazáshoz, válassza ki a logikai alkalmazás.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Megnyitás Visual Studio-ban

A Visual Studióban nyissa meg a logic apps a korábban létrehozott és telepített, közvetlenül az Azure Portalon vagy Azure Resource Manager-projekteket a Visual Studio használatával.

1. Nyissa meg a Cloud Explorer, és keresse meg a logikai alkalmazást. 

2. Válassza ki a logikai alkalmazás menüjén **nyissa meg a logikai alkalmazás szerkesztő**.

   Ez a példa bemutatja a logic apps erőforrástípusok szerint, így a logic apps meg fog jelenni a **Logic Apps** szakaszban.

  ![Az Azure Portalról nyissa meg üzembe helyezett logikai alkalmazás](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   A logikai alkalmazás a Logic Apps Designerben alján, a tervező megnyitása után kiválaszthatja **Kódnézet** úgy, hogy az alapul szolgáló logikai alkalmazás szabályzatdefiníciók struktúrája áttekintheti. 
   Ha azt szeretné, a logikai alkalmazás központi telepítési sablont létrehozni, ismerje meg, [Azure Resource Manager-sablon letöltése](#download-logic-app) , hogy a logikai alkalmazáshoz. Tudjon meg többet [Resource Manager-sablonok](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Töltse le az Azure-ból

A logikai alkalmazások letöltheti a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> , és mentse őket [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) sablonok. Ezután helyben a Visual Studio-sablonok szerkesztése és testre szabhatja a logic apps különböző üzembe helyezési környezetekhez. Logikai alkalmazások automatikus letöltésének *felparaméterezi* belül a definíciójukat [Resource Manager-sablonok](../azure-resource-manager/resource-group-overview.md#template-deployment), amely JavaScript Object Notation (JSON) is használhatja.

1. A Visual Studióban nyissa meg a Cloud Explorer, majd keresse meg és válassza ki a logikai alkalmazást, amely letölti az Azure-ból.

2. Az alkalmazás helyi menüjén válassza **nyissa meg a logikai alkalmazás szerkesztő**.

   A Logic App Designerben megnyílik, és megjeleníti a logikai alkalmazást. 
   Tekintse át a logikai alkalmazás alapul szolgáló definíciójának és struktúra, a tervező alján válassza **Kódnézet**. 

3. A Tervező eszköztárán válassza **letöltése**.

   ![Válassza a "Letöltés"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Kér egy adott helyre vonatkozóan, amikor keresse meg a helyet, és mentse a logikai alkalmazás definíciójában a Resource Manager-sablon (.json) JSON-fájlok formátuma. 

A logic app-definíció jelenik meg a `resources` alszakasz a Resource Manager-sablon belül. Most már szerkesztheti a logikai alkalmazás definíciójában és a Resource Manager-sablon a Visual Studio használatával. A sablon egy Azure Resource Manager-projektként, egy Visual Studio-megoldás is hozzáadhat. Ismerje meg [Resource Manager-projektek, a logic apps a Visual Studióban](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Frissítse az Azure-ból

Ha a logikai alkalmazás az Azure Portalon szerkesztheti, és szeretné megtartani ezeket a módosításokat, ellenőrizze, hogy frissíti az alkalmazás verziója a Visual Studióban az ezeket a módosításokat. 

* A Visual Studio, a Logikaialkalmazás-Tervező eszköztáron válassza **frissítése**.

  – vagy –

* A Visual Studio Cloud Explorer, a logikai alkalmazás helyi menü megnyitásához, és válassza **frissítése**. 

![Frissítse a logikai alkalmazás frissítései](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Logic app frissítések közzétételéhez

Ha készen áll a logic app-frissítések központi telepítésének a Visual Studióból az Azure-bA Logikaialkalmazás-Tervező eszköztárán válassza a **közzététel**.

![Frissített logikai alkalmazás közzététele](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>A logikai alkalmazás manuális futtatása

Manuálisan is aktiválhatja a Visual Studióból az Azure-ban üzembe helyezett logikai alkalmazás. Logikaialkalmazás-Tervező eszköztárán válassza a **Trigger futtatása**.

![Manuálisan a logikai alkalmazás futtatása](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Futtatási előzmények áttekintése

Ellenőrizze az állapotát, és diagnosztizálhatja a problémákat a logikaialkalmazás-futtatások, tekintse át a részleteket, például bemenetek és kimenetek, azok számára, futtatja a Visual Studióban.

1. A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menü, és válassza ki **nyílt futtatási előzmények**.

   ![Nyissa meg a futtatási előzmények](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Egy adott Futtatás részleteinek megtekintéséhez kattintson duplán egy Futtatás. Példa:

   ![Futtatási előzmények részletes](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Tulajdonságban a táblázat rendezéséhez válassza ki az oszlop fejlécére, az adott tulajdonságnál. 

3. Bontsa ki a lépéseket, amelyek lépések bemeneteit és kimeneteit meg szeretné tekinteni. Példa:

   ![Bemenetek és kimenetek egyes lépéseinek megtekintése](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Logikai alkalmazás engedélyezése vagy letiltása

A logikai alkalmazás törlése, nélkül is leállíthatja az eseményindító aktiválja a következő alkalommal, amikor a megadott feltétel teljesülésekor a. A logikai alkalmazás letiltása megakadályozza, hogy a Logic Apps-motor a létrehozásának és jövőbeli munkafolyamatait a logikai alkalmazás futtatásának.
A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menü, és válassza ki **letiltása**.

![A logikai alkalmazás letiltása](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

> [!NOTE]
> Ha letilt egy logikai alkalmazást, nincsenek új futtatások példányosítása. Az összes folyamatban lévő és a függőben lévő futtatásának továbbra is csak akkor fejeződik be, amely időt is igénybe vehet. 

Ha készen áll a logikai alkalmazás, folytassa a működést, a logikai alkalmazás újraaktiválhatja. A Cloud Explorerben nyissa meg a logikai alkalmazás helyi menü, és válassza ki **engedélyezése**.

![A logikai alkalmazás engedélyezése](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>A logikai alkalmazás törlése

A logikai alkalmazás törlése az Azure Portalról, a Cloud Explorerben nyissa meg a logikai alkalmazás helyi menüt, és válassza **törlése**.

![A logikai alkalmazás törlése](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

> [!NOTE]
> Amikor törli a logikai alkalmazást, a rendszer nem kezdeményez új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat. 

## <a name="troubleshooting"></a>Hibaelhárítás

A logic app projektet a Logic Apps Designerben nyit meg, előfordulhat, hogy nem kap a az Azure-előfizetés kiválasztására szolgáló lehetőség. Ehelyett a logikai alkalmazás megnyílik, amely nem egy használni kívánt Azure-előfizetéssel. Ez a viselkedés akkor fordul elő, mert Miután megnyitotta a logikai alkalmazás .JSON kiterjesztésű fájlt, a Visual Studio gyorsítótárazza az első kijelölt előfizetést későbbi használatra. Ez a probléma megoldása érdekében próbálkozzon az alábbi lépéseket:

* Nevezze át a logikai alkalmazás .JSON kiterjesztésű fájlt. Az előfizetés gyorsítótár attól függ, hogy a fájl nevét. 
* Eltávolítja a korábban kiválasztott előfizetések *összes* a logic apps, a megoldás törlése a *rejtett* .vs mappát a megoldás könyvtárban. Ezen a helyen az előfizetés adatait tárolja. 

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan kezelheti az üzembe helyezett logikai alkalmazások Visual studióval. Következő lépésként megtanulhatja a központi telepítés logikaialkalmazás-definíciók testreszabása:

> [!div class="nextstepaction"]
> [Logikaialkalmazás-definíciók készítése JSON-ban](../logic-apps/logic-apps-author-definitions.md)
