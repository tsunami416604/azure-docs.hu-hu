---
title: Webalkalmazás üzembe helyezése az Azure Cosmos DB - sablonnal
description: Ismerje meg, hogyan helyezhet üzembe az Azure Cosmos DB-fiók, az Azure App Service Web Apps és a egy minta-webalkalmazáshoz, egy Azure Resource Manager-sablon használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128371"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Azure Cosmos DB és az Azure App Service Web Apps használatával egy Azure Resource Manager-sablon üzembe helyezése
Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe és integrálhat [Microsoft Azure Cosmos DBT](https://azure.microsoft.com/services/cosmos-db/), [Azure app Service](https://go.microsoft.com/fwlink/?LinkId=529714) webalkalmazást és egy minta webalkalmazást egy Azure Resource Manager sablonnal.

Azure Resource Manager-sablonok használatával, könnyedén automatizálható a telepítését és konfigurációját az Azure-erőforrások.  Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe egy webalkalmazást, és automatikusan konfigurálja az Azure Cosmos DB-fiók kapcsolati adatait.

Az oktatóanyag elvégzését követően lesz a következő kérdések megválaszolásához:  

* Hogyan használhatom az Azure Resource Manager-sablonnal történő üzembe helyezése és integrálása az Azure Cosmos DB-fiók és -webalkalmazás létrehozása az Azure App Service?
* Hogyan használhatom az Azure Resource Manager-sablonnal történő üzembe helyezése és integrálása az Azure Cosmos DB-fiókot, -webalkalmazás létrehozása az App Service Web Apps és egy Webdeploy-alkalmazás?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Előfeltételek
> [!TIP]
> Ez az oktatóanyag korábbi tapasztalata az Azure Resource Manager-sablonok vagy JSON nem feltételezi azt, miközben kell szeretné módosítani a hivatkozott sablonok vagy a központi telepítési beállításokat, majd Tudásbázis ezeknek a területeknek az egyes szükség.
> 
> 

Ez az oktatóanyag utasításait követve, előtt gondoskodjon arról, hogy az Azure-előfizetéssel. Az Azure az előfizetés-alapú platform.  Az előfizetés beszerzésével kapcsolatos további információkért lásd a [vásárlási lehetőségeket](https://azure.microsoft.com/pricing/purchase-options/), a [tagok ajánlatait](https://azure.microsoft.com/pricing/member-offers/)vagy az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>1. lépés: a sablonfájlok letöltése
Először töltse le a sablonfájlokat, amely ehhez az oktatóanyaghoz.

1. Töltse le az [Azure Cosmos db fiók létrehozása, Web Apps és a bemutató alkalmazás sablonjának központi telepítését](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) egy helyi mappába (például C:\Azure Cosmos DBTemplates). Ez a sablon üzembe helyezi az Azure Cosmos DB-fiók, egy App Service-webalkalmazást és egy webalkalmazást.  Azt is automatikusan konfigurálja a webalkalmazás az Azure Cosmos DB-fiókhoz való csatlakozáshoz.
2. Töltse le az [Azure Cosmos db fiók létrehozása és a Web Apps](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) sablon helyi mappába (például C:\Azure Cosmos DBTemplates). Ez a sablon üzembe helyez egy Azure Cosmos DB-fiókot, egy App Service-webalkalmazást, és módosítja a hely alkalmazás beállításait, hogy könnyen felszínre hozni azokat az Azure Cosmos DB kapcsolati adatokat, de nem tartalmazza a webalkalmazás.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>2\. lépés: Az Azure Cosmos DB-fiók, az App Service web app és bemutató példa alkalmazás telepítése
Most már az első saját sablon üzembe helyezni.

> [!TIP]
> A sablon nem ellenőrzi, hogy a webalkalmazás neve és az Azure Cosmos DB-fiók neve a következő sablonban megadott a) érvényes, és (b) érhető el.  Azt javasoljuk, hogy ellenőrizze a rendelkezésre állási azt tervezi, hogy az üzembe helyezés beküldése előtt adja meg a neveket.
> 
> 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), kattintson az új elemre, és keressen rá a "template Deployment" kifejezésre.
    ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment1.png)
2. Jelölje ki a Template deployment elemet, majd kattintson a sablon üzembe helyezése felhasználói felület ![képernyőkép **létrehozása** elemre](./media/create-website/TemplateDeployment2.png)
3. Kattintson a **Sablon szerkesztése**elemre, illessze be a DocDBWebsiteTodo. JSON sablonfájl tartalmát, majd kattintson a **Mentés**gombra.
   ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment3.png)
4. Kattintson a **Paraméterek szerkesztése**elemre, adja meg az egyes kötelező paraméterek értékeit, majd kattintson **az OK**gombra.  A paraméterek a következők:
   
   1. SITENAME: megadja a App Service webalkalmazás nevét, és a webalkalmazás eléréséhez használt URL-cím létrehozásához használatos (például ha a "mydemodocdbwebapp" nevet adja meg, akkor a webalkalmazás elérésére szolgáló URL-cím `mydemodocdbwebapp.azurewebsites.net`).
   2. HOSTINGPLANNAME: A neve, az App Service szolgáltatási csomag létrehozása.
   3. HELYE: Adja meg az Azure-beli hely, az Azure Cosmos DB és a webes alkalmazás-erőforrások létrehozásához.
   4. DATABASEACCOUNTNAME: Itt adhatja meg, hozhat létre az Azure Cosmos DB-fiók nevére.   
      
      ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment4.png)
5. Válasszon ki egy meglévő erőforráscsoportot, vagy adjon meg egy nevet, hogy egy új erőforráscsoportot, és válassza ki az erőforráscsoport helyét.

    ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment5.png)
6. Kattintson a **jogi feltételek áttekintése**, a **vásárlás**elemre, majd a telepítés megkezdéséhez kattintson a **Létrehozás** gombra.  Válassza a **rögzítés az irányítópulton** lehetőséget, hogy az eredményül kapott központi telepítés könnyen látható legyen a Azure Portal kezdőlapján.
   ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment6.png)
7. Az üzembe helyezést követően a erőforrás-csoport panel nyílik meg.
   ![képernyőkép az erőforráscsoport panelről](./media/create-website/TemplateDeployment7.png)  
8. Az alkalmazás használatához navigáljon a webalkalmazás URL-címére (a fenti példában az URL-cím `http://mydemodocdbwebapp.azurewebsites.net`).  A következő webes alkalmazás jelenik meg:
   
   ![Minta Teendőkezelő alkalmazás](./media/create-website/image2.png)
9. Lépjen tovább, és hozzon létre néhány feladatot az web app alkalmazásban, és térjen vissza a erőforrás-csoport panelen az Azure Portalon. Kattintson a Azure Cosmos DB fiók erőforrására az erőforrások listában, majd kattintson az **adatkezelő**elemre.
10. Az alapértelmezett lekérdezés futtatása "VÁLASSZA * FROM c", és vizsgálja meg az eredményeket.  Figyelje meg, hogy a lekérdezés beolvasta a teendőket, a fenti 7. lépésben létrehozott JSON-ábrázolását.  Nyugodtan kísérletezhet a lekérdezések; például, próbálja meg futtatni a SELECT * FROM c WHERE c.isComplete = true értéket ad vissza az összes teendőlista elemeinek jelölt kész.
11. Nyugodtan Fedezze fel az Azure Cosmos DB portál nyújt lehetőséget, vagy módosítsa a minta Teendőkezelő alkalmazás.  Amikor készen áll, üzembe helyezni egy másik sablonnal.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>3\. lépés: A dokumentum-fiók és a web app minta üzembe helyezése
Most már a második sablon üzembe helyezni.  Ez a sablon hasznos bemutatják, hogyan lehet szúr be az Azure Cosmos DB kapcsolati információkat, például a fiók végpontját és a főkulcs egy webalkalmazásba Alkalmazásbeállítások vagy egyéni kapcsolati karakterláncként. Például például hogy saját webes alkalmazás üzembe helyezése az Azure Cosmos DB-fiókot, és a kapcsolati adatok automatikusan kitölti az üzembe helyezés során szeretné.

> [!TIP]
> A sablon nem ellenőrzi, hogy a webalkalmazás neve és az Azure Cosmos DB-fiók neve alatt megadott a) érvényes, és (b) érhető el.  Azt javasoljuk, hogy ellenőrizze a rendelkezésre állási azt tervezi, hogy az üzembe helyezés beküldése előtt adja meg a neveket.
> 
> 

1. Az [Azure Portalon](https://portal.azure.com)kattintson az új elemre, és keressen rá a "template Deployment" kifejezésre.
    ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment1.png)
2. Jelölje ki a Template deployment elemet, majd kattintson a sablon üzembe helyezése felhasználói felület ![képernyőkép **létrehozása** elemre](./media/create-website/TemplateDeployment2.png)
3. Kattintson a **Sablon szerkesztése**elemre, illessze be a DocDBWebSite. JSON sablonfájl tartalmát, majd kattintson a **Mentés**gombra.
   ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment3.png)
4. Kattintson a **Paraméterek szerkesztése**elemre, adja meg az egyes kötelező paraméterek értékeit, majd kattintson **az OK**gombra.  A paraméterek a következők:
   
   1. HELYNÉV: Nevét adja meg az App Service web app és az URL-cím, amely a web app (például, ha a megadott "mydemodocdbwebapp", majd az URL-címet, amely szerint a webalkalmazás eléréséhez mydemodocdbwebapp.azurewebsites.net) eléréséhez használandó létrehozására használható.
   2. HOSTINGPLANNAME: A neve, az App Service szolgáltatási csomag létrehozása.
   3. HELYE: Adja meg az Azure-beli hely, az Azure Cosmos DB és a webes alkalmazás-erőforrások létrehozásához.
   4. DATABASEACCOUNTNAME: Itt adhatja meg, hozhat létre az Azure Cosmos DB-fiók nevére.   
      
      ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment4.png)
5. Válasszon ki egy meglévő erőforráscsoportot, vagy adjon meg egy nevet, hogy egy új erőforráscsoportot, és válassza ki az erőforráscsoport helyét.

    ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment5.png)
6. Kattintson a **jogi feltételek áttekintése**, a **vásárlás**elemre, majd a telepítés megkezdéséhez kattintson a **Létrehozás** gombra.  Válassza a **rögzítés az irányítópulton** lehetőséget, hogy az eredményül kapott központi telepítés könnyen látható legyen a Azure Portal kezdőlapján.
   ![képernyőkép a sablon üzembe helyezésének felhasználói felületéről](./media/create-website/TemplateDeployment6.png)
7. Az üzembe helyezést követően a erőforrás-csoport panel nyílik meg.
   ![képernyőkép az erőforráscsoport panelről](./media/create-website/TemplateDeployment7.png)  
8. Kattintson a webalkalmazás-erőforrásra az erőforrások listában, majd kattintson az **alkalmazásbeállítások** ![az erőforráscsoport képernyőképére](./media/create-website/TemplateDeployment9.png)  
9. Vegye figyelembe, hogyan vannak jelen, az Azure Cosmos DB végpont és az Azure Cosmos DB főkulcsok mindegyike nastavení aplikace.

    ![Képernyőkép a nastavení aplikace](./media/create-website/TemplateDeployment10.png)  
10. Nyugodtan folytathatja az Azure Portal megismerését, vagy követheti Azure Cosmos DB [mintáit](https://go.microsoft.com/fwlink/?LinkID=402386) , hogy saját Azure Cosmos db alkalmazást hozzon létre.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Következő lépések
Gratulálunk! Üzembe helyezte az Azure Cosmos DB, az App Service web app és a egy minta-webalkalmazás Azure Resource Manager-sablonok használatával.

* Ha többet szeretne megtudni a Azure Cosmos DBről, kattintson [ide](https://azure.microsoft.com/services/cosmos-db/).
* Ha többet szeretne megtudni a Azure App Service Web Apps szolgáltatásról, kattintson [ide](https://go.microsoft.com/fwlink/?LinkId=325362).
* A Azure Resource Manager-sablonokkal kapcsolatos további tudnivalókért kattintson [ide](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>A változások
* Információk a Websites szolgáltatásról az App Service-re való váltásról: [Az Azure App Service és a hatása a meglévő Azure-szolgáltatásokra](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, lépjen [Az Azure App Service kipróbálása](https://go.microsoft.com/fwlink/?LinkId=523751) oldalra, ahol azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Nincs szükség bankkártyára, és nem jár semmiféle kötelezettséggel.
> 
> 

