---
title: Webalkalmazás üzembe helyezése az Azure Cosmos DB - sablonnal |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe az Azure Cosmos DB-fiók, az Azure App Service Web Apps és a egy minta-webalkalmazáshoz, egy Azure Resource Manager-sablon használatával.
services: cosmos-db, app-service\web
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 29a2335a3a4077866b71e4303c240ad8352371ba
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243777"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Azure Cosmos DB és az Azure App Service Web Apps használatával egy Azure Resource Manager-sablon üzembe helyezése
Az oktatóanyag bemutatja, hogyan használhatja az Azure Resource Manager-sablon üzembe helyezése és integrálása [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), egy [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) web app és a egy minta-webalkalmazáshoz.

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

Ez az oktatóanyag utasításait követve, előtt gondoskodjon arról, hogy az Azure-előfizetéssel. Az Azure az előfizetés-alapú platform.  Előfizetés beszerzésével kapcsolatos további információkért lásd: [vásárlási lehetőségek](https://azure.microsoft.com/pricing/purchase-options/), [ajánlatok tagoknak](https://azure.microsoft.com/pricing/member-offers/), vagy [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>1. lépés: Töltse le a sablonfájlokat
Először töltse le a sablonfájlokat, amely ehhez az oktatóanyaghoz.

1. Töltse le a [hozzon létre egy Azure Cosmos DB-fiók, a Web Apps, és a egy bemutató alkalmazást minta üzembe helyezése](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) egy helyi mappába (például C:\Azure Cosmos DBTemplates) sablont. Ez a sablon üzembe helyezi az Azure Cosmos DB-fiók, egy App Service-webalkalmazást és egy webalkalmazást.  Azt is automatikusan konfigurálja a webalkalmazás az Azure Cosmos DB-fiókhoz való csatlakozáshoz.
2. Töltse le a [hozzon létre egy Azure Cosmos DB-fiókot és a Web Apps minta](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) egy helyi mappába (például C:\Azure Cosmos DBTemplates) sablont. Ez a sablon üzembe helyez egy Azure Cosmos DB-fiókot, egy App Service-webalkalmazást, és módosítja a hely alkalmazás beállításait, hogy könnyen felszínre hozni azokat az Azure Cosmos DB kapcsolati adatokat, de nem tartalmazza a webalkalmazás.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>2. lépés: Az Azure Cosmos DB-fiók, az App Service web app és bemutató példa alkalmazás telepítése
Most már az első saját sablon üzembe helyezni.

> [!TIP]
> A sablon nem ellenőrzi, hogy a webalkalmazás neve és az Azure Cosmos DB-fiók neve a következő sablonban megadott a) érvényes, és (b) érhető el.  Azt javasoljuk, hogy ellenőrizze a rendelkezésre állási azt tervezi, hogy az üzembe helyezés beküldése előtt adja meg a neveket.
> 
> 

1. Jelentkezzen be a [az Azure Portal](https://portal.azure.com), az új gombra, és keressen rá a "Sablon telepítése".
    ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment1.png)
2. Válassza ki a sablon üzembe helyezési elemet, és kattintson a **létrehozás** ![a sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment2.png)
3. Kattintson a **szerkesztési sablon**illessze be a DocDBWebsiteTodo.json sablon fájl tartalmát, és kattintson a **mentése**.
   ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment3.png)
4. Kattintson a **paraméterek szerkesztése**, adja meg a kötelező paraméterek értékeit, és kattintson a **OK**.  A paraméterek a következők:
   
   1. HELYNÉV: Nevét adja meg az App Service web app és az URL-cím, amely a web app (például, ha a megadott "mydemodocdbwebapp", majd az URL-címet, amely szerint a webalkalmazás eléréséhez mydemodocdbwebapp.azurewebsites.net) eléréséhez használt létrehozására használható.
   2. HOSTINGPLANNAME: A neve, az App Service szolgáltatási csomag létrehozása.
   3. HELYE: Adja meg az Azure-beli hely, az Azure Cosmos DB és a webes alkalmazás-erőforrások létrehozásához.
   4. DATABASEACCOUNTNAME: Itt adhatja meg, hozhat létre az Azure Cosmos DB-fiók nevére.   
      
      ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment4.png)
5. Válasszon ki egy meglévő erőforráscsoportot, vagy adjon meg egy nevet, hogy egy új erőforráscsoportot, és válassza ki az erőforráscsoport helyét.

    ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment5.png)
6. Kattintson a **jogi feltételek áttekintése**, **beszerzési**, és kattintson a **létrehozás** a telepítés megkezdéséhez.  Válassza ki **rögzítés az irányítópulton** így az eredményül kapott üzemelő példány könnyen látható, az Azure portál kezdőlapján.
   ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment6.png)
7. Az üzembe helyezést követően a erőforrás-csoport panel nyílik meg.
   ![Az erőforrás-csoport panel képernyőképe](./media/create-website/TemplateDeployment7.png)  
8. Használja az alkalmazást, nyissa meg a webes alkalmazás URL-címe (a fenti példában az URL-cím lenne http://mydemodocdbwebapp.azurewebsites.net).  A következő webes alkalmazás jelenik meg:
   
   ![Minta Teendőkezelő alkalmazás](./media/create-website/image2.png)
9. Lépjen tovább, és hozzon létre néhány feladatot az web app alkalmazásban, és térjen vissza a erőforrás-csoport panelen az Azure Portalon. Az Azure Cosmos DB-fiók erőforrás az erőforrások listájában kattintson, majd **adatkezelő**.
10. Az alapértelmezett lekérdezés futtatása "VÁLASSZA * FROM c", és vizsgálja meg az eredményeket.  Figyelje meg, hogy a lekérdezés beolvasta a teendőket, a fenti 7. lépésben létrehozott JSON-ábrázolását.  Nyugodtan kísérletezhet a lekérdezések; például, próbálja meg futtatni a SELECT * FROM c WHERE c.isComplete = true értéket ad vissza az összes teendőlista elemeinek jelölt kész.
11. Nyugodtan Fedezze fel az Azure Cosmos DB portál nyújt lehetőséget, vagy módosítsa a minta Teendőkezelő alkalmazás.  Amikor készen áll, üzembe helyezni egy másik sablonnal.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>3. lépés: A dokumentum-fiók és a web app minta üzembe helyezése
Most már a második sablon üzembe helyezni.  Ez a sablon hasznos bemutatják, hogyan lehet szúr be az Azure Cosmos DB kapcsolati információkat, például a fiók végpontját és a főkulcs egy webalkalmazásba Alkalmazásbeállítások vagy egyéni kapcsolati karakterláncként. Például például hogy saját webes alkalmazás üzembe helyezése az Azure Cosmos DB-fiókot, és a kapcsolati adatok automatikusan kitölti az üzembe helyezés során szeretné.

> [!TIP]
> A sablon nem ellenőrzi, hogy a webalkalmazás neve és az Azure Cosmos DB-fiók neve alatt megadott a) érvényes, és (b) érhető el.  Azt javasoljuk, hogy ellenőrizze a rendelkezésre állási azt tervezi, hogy az üzembe helyezés beküldése előtt adja meg a neveket.
> 
> 

1. Az a [az Azure Portal](https://portal.azure.com), az új gombra, és keressen rá a "Sablon telepítése".
    ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment1.png)
2. Válassza ki a sablon üzembe helyezési elemet, és kattintson a **létrehozás** ![a sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment2.png)
3. Kattintson a **szerkesztési sablon**illessze be a DocDBWebSite.json sablon fájl tartalmát, és kattintson a **mentése**.
   ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment3.png)
4. Kattintson a **paraméterek szerkesztése**, adja meg a kötelező paraméterek értékeit, és kattintson a **OK**.  A paraméterek a következők:
   
   1. HELYNÉV: Nevét adja meg az App Service web app és az URL-cím, amely a web app (például, ha a megadott "mydemodocdbwebapp", majd az URL-címet, amely szerint a webalkalmazás eléréséhez mydemodocdbwebapp.azurewebsites.net) eléréséhez használandó létrehozására használható.
   2. HOSTINGPLANNAME: A neve, az App Service szolgáltatási csomag létrehozása.
   3. HELYE: Adja meg az Azure-beli hely, az Azure Cosmos DB és a webes alkalmazás-erőforrások létrehozásához.
   4. DATABASEACCOUNTNAME: Itt adhatja meg, hozhat létre az Azure Cosmos DB-fiók nevére.   
      
      ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment4.png)
5. Válasszon ki egy meglévő erőforráscsoportot, vagy adjon meg egy nevet, hogy egy új erőforráscsoportot, és válassza ki az erőforráscsoport helyét.

    ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment5.png)
6. Kattintson a **jogi feltételek áttekintése**, **beszerzési**, és kattintson a **létrehozás** a telepítés megkezdéséhez.  Válassza ki **rögzítés az irányítópulton** így az eredményül kapott üzemelő példány könnyen látható, az Azure portál kezdőlapján.
   ![A sablon üzembe helyezéséhez felhasználói felület képernyőképe](./media/create-website/TemplateDeployment6.png)
7. Az üzembe helyezést követően a erőforrás-csoport panel nyílik meg.
   ![Az erőforrás-csoport panel képernyőképe](./media/create-website/TemplateDeployment7.png)  
8. Kattintson a webes alkalmazás-erőforrást az erőforrások listájában, majd **Alkalmazásbeállítások** ![képernyőfelvétel az erőforráscsoport](./media/create-website/TemplateDeployment9.png)  
9. Vegye figyelembe, hogyan vannak jelen, az Azure Cosmos DB végpont és az Azure Cosmos DB főkulcsok mindegyike nastavení aplikace.

    ![Képernyőkép a nastavení aplikace](./media/create-website/TemplateDeployment10.png)  
10. Nyugodtan folytassa az ismerkedést az Azure Portalon, vagy kövesse az Azure Cosmos DB az [minták](https://go.microsoft.com/fwlink/?LinkID=402386) a saját Azure Cosmos DB-alkalmazás létrehozásához.

<a name="NextSteps"></a>

## <a name="next-steps"></a>További lépések
Gratulálunk! Üzembe helyezte az Azure Cosmos DB, az App Service web app és a egy minta-webalkalmazás Azure Resource Manager-sablonok használatával.

* Azure Cosmos DB kapcsolatos további tudnivalókért kattintson [Itt](http://azure.com/docdb).
* További információ az Azure App Service Web apps szolgáltatásról, kattintson a [Itt](https://go.microsoft.com/fwlink/?LinkId=325362).
* Az Azure Resource Manager-sablonokkal kapcsolatos további tudnivalókért kattintson [Itt](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>A változások
* Információk a Websites szolgáltatásról az App Service-re való váltásról: [Az Azure App Service és a hatása a meglévő Azure-szolgáltatásokra](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://go.microsoft.com/fwlink/?LinkId=523751) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

