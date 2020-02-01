---
title: Webalkalmazás üzembe helyezése sablonnal – Azure Cosmos DB
description: Megtudhatja, hogyan helyezhet üzembe egy Azure Cosmos DB fiókot, Azure App Service Web Appst és egy minta webalkalmazást egy Azure Resource Manager sablon használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: sngun
ms.openlocfilehash: 11b84877171fae8e788d3298973fd74645b35480
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901920"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Azure Cosmos DB-és Azure App Service-Web Apps üzembe helyezése Azure Resource Manager sablon használatával
Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe és integrálhat [Microsoft Azure Cosmos DBT](https://azure.microsoft.com/services/cosmos-db/), [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714) webalkalmazást és egy minta webalkalmazást egy Azure Resource Manager sablon használatával.

Azure Resource Manager sablonok használatával egyszerűen automatizálhatja az Azure-erőforrások üzembe helyezését és konfigurálását.  Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe egy webalkalmazást, és hogyan konfigurálhatja automatikusan Azure Cosmos DB fiók kapcsolati adatait.

Az oktatóanyag elvégzése után a következő kérdésekre tud válaszolni:  

* Hogyan használhatok Azure Resource Manager sablont egy Azure Cosmos DB-fiók és egy webalkalmazás üzembe helyezéséhez és integrálásához a Azure App Service-ben?
* Hogyan használhatok egy Azure Resource Manager sablont egy Azure Cosmos DB-fiók, egy webalkalmazás App Service Web Apps és egy Webtelepítő alkalmazás üzembe helyezéséhez és integrálásához?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Előfeltételek
> [!TIP]
> Bár ez az oktatóanyag nem feltételezi az Azure Resource Manager-sablonokkal vagy JSON-vel kapcsolatos korábbi tapasztalatokat, érdemes módosítania a hivatkozott sablonokat vagy telepítési beállításokat, majd az egyes területek ismereteit kötelező megadni.
> 
> 

Az oktatóanyagban szereplő utasítások követése előtt győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Az Azure egy előfizetés-alapú platform.  Az előfizetés beszerzésével kapcsolatos további információkért lásd a [vásárlási lehetőségeket](https://azure.microsoft.com/pricing/purchase-options/), a [tagok ajánlatait](https://azure.microsoft.com/pricing/member-offers/)vagy az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>1. lépés: a sablonfájlok letöltése
Kezdjük az oktatóanyaghoz szükséges sablonfájlok letöltésével.

1. Töltse le az [Azure Cosmos db fiók létrehozása, Web Apps és a bemutató alkalmazás sablonjának központi telepítését](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) egy helyi mappába (például C:\Azure Cosmos DBTemplates). Ez a sablon egy Azure Cosmos DB fiókot, egy App Service webalkalmazást és egy webes alkalmazást telepít.  Emellett automatikusan konfigurálja a webalkalmazást az Azure Cosmos DB-fiókhoz való kapcsolódásra.
2. Töltse le az [Azure Cosmos db fiók létrehozása és a Web Apps](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) sablon helyi mappába (például C:\Azure Cosmos DBTemplates). Ez a sablon egy Azure Cosmos DB fiókot telepít, egy App Service webalkalmazást, és módosítja a hely alkalmazás-beállításait, hogy könnyen felszínre Azure Cosmos DB a kapcsolati adatokat, de nem tartalmaz webalkalmazást.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>2\. lépés: a Azure Cosmos DB fiók üzembe helyezése, App Service a webalkalmazás és a bemutató alkalmazás mintája
Most végezze el az első sablon üzembe helyezését.

> [!TIP]
> A sablon nem ellenőrzi, hogy a webalkalmazás neve és a Azure Cosmos DB fiók neve a következő sablonban van-e megadva) érvényes és b) érhető el.  Erősen ajánlott ellenőrizni, hogy a telepítés elküldése előtt milyen nevekkel kell megadnia a rendelkezésre állást.
> 
> 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), kattintson az új elemre, és keressen rá a "template Deployment" kifejezésre.
    ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment1.png)
2. Jelölje ki a Template deployment elemet, majd kattintson a sablon üzembe helyezése felhasználói felület ![képernyőkép **létrehozása** elemre](./media/create-website/TemplateDeployment2.png)
3. Kattintson a **Sablon szerkesztése**elemre, illessze be a DocDBWebsiteTodo. JSON sablonfájl tartalmát, majd kattintson a **Mentés**gombra.
   ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment3.png)
4. Kattintson a **Paraméterek szerkesztése**elemre, adja meg az egyes kötelező paraméterek értékeit, majd kattintson **az OK**gombra.  A paraméterek a következők:
   
   1. SITENAME: megadja a App Service webalkalmazás nevét, és a webalkalmazás eléréséhez használt URL-cím létrehozásához használatos (például ha a "mydemodocdbwebapp" nevet adja meg, akkor a webalkalmazás elérésére szolgáló URL-cím `mydemodocdbwebapp.azurewebsites.net`).
   2. HOSTINGPLANNAME: a létrehozandó App Service üzemeltetési terv nevét adja meg.
   3. HELY: a Azure Cosmos DB és a webalkalmazás-erőforrások létrehozásához használt Azure-beli hely.
   4. DATABASEACCOUNTNAME: a létrehozandó Azure Cosmos DB fiók nevét adja meg.   
      
      ![Képernyőfelvétel a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment4.png)
5. Válasszon egy meglévő erőforráscsoportot, vagy adjon meg egy nevet az új erőforráscsoport létrehozásához, és válassza ki az erőforráscsoport helyét.

    ![Képernyőfelvétel a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment5.png)
6. Kattintson a **jogi feltételek áttekintése**, a **vásárlás**elemre, majd a telepítés megkezdéséhez kattintson a **Létrehozás** gombra.  Válassza a **rögzítés az irányítópulton** lehetőséget, hogy az eredményül kapott központi telepítés könnyen látható legyen a Azure Portal kezdőlapján.
   ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment6.png)
7. Az üzembe helyezés befejezésekor megnyílik az erőforráscsoport panel.
   ![képernyőkép az erőforráscsoport panelről](./media/create-website/TemplateDeployment7.png)  
8. Az alkalmazás használatához navigáljon a webalkalmazás URL-címére (a fenti példában az URL-cím `http://mydemodocdbwebapp.azurewebsites.net`).  A következő webalkalmazás jelenik meg:
   
   ![Példa Todo-alkalmazásra](./media/create-website/image2.png)
9. Lépjen tovább, és hozzon létre néhány feladatot a webalkalmazásban, majd térjen vissza a Azure Portal erőforráscsoport ablaktáblájához. Kattintson a Azure Cosmos DB fiók erőforrására az erőforrások listában, majd kattintson az **adatkezelő**elemre.
10. Futtassa az alapértelmezett lekérdezést, a "SELECT * FROM c" parancsot, és vizsgálja meg az eredményeket.  Figyelje meg, hogy a lekérdezés lekérte a fenti 7. lépésben létrehozott teendők JSON-ábrázolását.  Nyugodtan kísérletezhet a lekérdezésekkel; Próbálkozzon például a SELECT * utasítás futtatásával, ahol a c. isComplete = True érték az összes olyan tennivaló visszaadására szolgál, amely készként van megjelölve.
11. Nyugodtan megismerheti a Azure Cosmos DB portál felületét, vagy módosíthatja a minta Todo alkalmazást.  Ha elkészült, hozzon üzembe egy másik sablont.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>3\. lépés: a dokumentum-fiók és a webalkalmazás-minta üzembe helyezése
Most Helyezzük üzembe a második sablont.  Ez a sablon hasznos lehet bemutatni, hogyan lehet beszúrni Azure Cosmos DB kapcsolati adatokat, például a fiók végpontját és a főkulcsot egy webalkalmazásba Alkalmazásbeállítások vagy egyéni kapcsolati karakterláncként. Tegyük fel például, hogy van egy saját webalkalmazása, amelyet egy Azure Cosmos DB-fiókkal szeretne üzembe helyezni, és a kapcsolati adatok automatikusan fel lesznek töltve az üzembe helyezés során.

> [!TIP]
> A sablon nem ellenőrzi, hogy a webalkalmazás neve és az Azure Cosmos DB fiók neve az alábbiakban megadott név érvényes-e, és b) elérhető-e.  Erősen ajánlott ellenőrizni, hogy a telepítés elküldése előtt milyen nevekkel kell megadnia a rendelkezésre állást.
> 
> 

1. Az [Azure Portalon](https://portal.azure.com)kattintson az új elemre, és keressen rá a "template Deployment" kifejezésre.
    ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment1.png)
2. Jelölje ki a Template deployment elemet, majd kattintson a sablon üzembe helyezése felhasználói felület ![képernyőkép **létrehozása** elemre](./media/create-website/TemplateDeployment2.png)
3. Kattintson a **Sablon szerkesztése**elemre, illessze be a DocDBWebSite. JSON sablonfájl tartalmát, majd kattintson a **Mentés**gombra.
   ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment3.png)
4. Kattintson a **Paraméterek szerkesztése**elemre, adja meg az egyes kötelező paraméterek értékeit, majd kattintson **az OK**gombra.  A paraméterek a következők:
   
   1. SITENAME: megadja a App Service webalkalmazás nevét, és a webalkalmazás eléréséhez használni kívánt URL-cím összeállítására szolgál (például ha a "mydemodocdbwebapp" nevet adja meg, akkor a webalkalmazás eléréséhez használt URL-cím a mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: a létrehozandó App Service üzemeltetési terv nevét adja meg.
   3. HELY: a Azure Cosmos DB és a webalkalmazás-erőforrások létrehozásához használt Azure-beli hely.
   4. DATABASEACCOUNTNAME: a létrehozandó Azure Cosmos DB fiók nevét adja meg.   
      
      ![Képernyőfelvétel a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment4.png)
5. Válasszon egy meglévő erőforráscsoportot, vagy adjon meg egy nevet az új erőforráscsoport létrehozásához, és válassza ki az erőforráscsoport helyét.

    ![Képernyőfelvétel a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment5.png)
6. Kattintson a **jogi feltételek áttekintése**, a **vásárlás**elemre, majd a telepítés megkezdéséhez kattintson a **Létrehozás** gombra.  Válassza a **rögzítés az irányítópulton** lehetőséget, hogy az eredményül kapott központi telepítés könnyen látható legyen a Azure Portal kezdőlapján.
   ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment6.png)
7. Az üzembe helyezés befejezésekor megnyílik az erőforráscsoport panel.
   ![képernyőkép az erőforráscsoport panelről](./media/create-website/TemplateDeployment7.png)  
8. Kattintson a webalkalmazás-erőforrásra az erőforrások listában, majd kattintson az **alkalmazásbeállítások** ![az erőforráscsoport képernyőképére](./media/create-website/TemplateDeployment9.png)  
9. Figyelje meg, hogy a Azure Cosmos DB végpont és a Azure Cosmos DB főkulcsok mindegyike rendelkezik-e Alkalmazásbeállítások.

    ![Az Alkalmazásbeállítások képernyőképe](./media/create-website/TemplateDeployment10.png)  
10. Nyugodtan folytathatja az Azure Portal megismerését, vagy követheti Azure Cosmos DB [mintáit](https://go.microsoft.com/fwlink/?LinkID=402386) , hogy saját Azure Cosmos db alkalmazást hozzon létre.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Következő lépések
Gratulálunk! Üzembe helyezte Azure Cosmos DB, App Service webalkalmazást és egy minta webalkalmazást Azure Resource Manager sablonok használatával.

* Ha többet szeretne megtudni a Azure Cosmos DBről, kattintson [ide](https://azure.microsoft.com/services/cosmos-db/).
* Ha többet szeretne megtudni a Azure App Service Web Apps szolgáltatásról, kattintson [ide](https://go.microsoft.com/fwlink/?LinkId=325362).
* A Azure Resource Manager-sablonokkal kapcsolatos további tudnivalókért kattintson [ide](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Újdonságok
* Információk a Websites szolgáltatásról az App Service-re való váltásról: [Az Azure App Service és a hatása a meglévő Azure-szolgáltatásokra](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://go.microsoft.com/fwlink/?LinkId=523751) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Nincs szükség bankkártyára, és mindez nem jár kötelezettségekkel.
> 
> 

