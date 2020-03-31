---
title: Webalkalmazás üzembe helyezése sablonnal – Azure Cosmos DB
description: Megtudhatja, hogyan telepíthet egy Azure Cosmos DB-fiókot, az Azure App Service Web Apps-t és egy minta webalkalmazást egy Azure Resource Manager-sablon használatával.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128371"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Az Azure Cosmos DB és az Azure App Service Web Apps telepítése egy Azure Resource Manager-sablon használatával
Ez az oktatóanyag bemutatja, hogyan használhat egy Azure Resource Manager-sablont a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), az Azure App [Service](https://go.microsoft.com/fwlink/?LinkId=529714) webalkalmazás és egy minta webalkalmazás üzembe helyezéséhez és integrálásához.

Az Azure Resource Manager-sablonok használatával egyszerűen automatizálhatja az Azure-erőforrások üzembe helyezését és konfigurálását.  Ez az oktatóanyag bemutatja, hogyan telepíthet egy webalkalmazást, és hogyan konfigurálhatja automatikusan az Azure Cosmos DB-fiók kapcsolati adatait.

Befejezése után ez a bemutató, akkor képes lesz arra, hogy válaszoljon a következő kérdésekre:  

* Hogyan használhatok Egy Azure Resource Manager-sablont egy Azure Cosmos DB-fiók és egy webalkalmazás üzembe helyezéséhez és integrálásához az Azure App Service-ben?
* Hogyan használhatok Egy Azure Resource Manager-sablont egy Azure Cosmos DB-fiók, egy webalkalmazás app service-webalkalmazásokban és egy webdeploy-alkalmazás üzembe helyezéséhez és integrálásához?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Előfeltételek
> [!TIP]
> Bár ez az oktatóanyag nem feltételezi az Azure Resource Manager-sablonok vagy jSON előzetes tapasztalatait, ha módosítani szeretné a hivatkozott sablonokat vagy üzembe helyezési beállításokat, akkor mindegyik terület ismerete szükséges.
> 
> 

Az oktatóanyagban található utasításokat követve győződjön meg arról, hogy rendelkezik az Azure-előfizetéssel. Az Azure egy előfizetés-alapú platform.  Az előfizetés beszerzéséről további információt a [Vásárlási lehetőségek](https://azure.microsoft.com/pricing/purchase-options/), [a Tagajánlatok](https://azure.microsoft.com/pricing/member-offers/)vagy [az Ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/)című témakörben talál.

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>1. lépés: A sablonfájlok letöltése
Kezdjük az oktatóanyagáltal igényelt sablonfájlok letöltésével.

1. Töltsön [le egy Azure Cosmos DB-fiókot, webalkalmazásokat, és telepítsen egy bemutatóalkalmazás-mintasablont](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) egy helyi mappába (például C:\Azure Cosmos DBTemplates). Ez a sablon egy Azure Cosmos DB-fiókot, egy App Service-webalkalmazást és egy webalkalmazást telepít.  Emellett automatikusan konfigurálja a webalkalmazást az Azure Cosmos DB-fiókhoz való csatlakozáshoz.
2. Az [Azure Cosmos DB-fiók létrehozása és a Web Apps mintasablon letöltése](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) egy helyi mappába (például C:\Azure Cosmos DBTemplates). Ez a sablon üzembe helyez egy Azure Cosmos DB-fiókot, egy App Service-webalkalmazást, és módosítja a webhely alkalmazásbeállításait az Azure Cosmos DB-kapcsolat adatainak egyszerű felszínre helyezése érdekében, de nem tartalmaz egy webalkalmazást.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>2. lépés: Az Azure Cosmos DB-fiók, az App Service webalkalmazás és a bemutatóalkalmazás-minta üzembe helyezése
Most telepítse az első sablont.

> [!TIP]
> A sablon nem ellenőrzi, hogy a webalkalmazás neve és az Azure Cosmos DB-fiók neve a következő sablonban megadott a) érvényes és b) elérhető.  Erősen ajánlott, hogy ellenőrizze a rendelkezésre álló nevek azt tervezi, hogy a központi telepítés előtt.
> 
> 

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)kattintson az Új gombra, és keressen a "Sablon üzembe helyezése" kifejezésre.
    ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment1.png)
2. Jelölje ki a Sablon telepítési elemét, és kattintson a Képernyőkép **létrehozása** ![gombra a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment2.png)
3. Kattintson a **Sablon szerkesztése**gombra, illessze be a DocDBWebsiteTodo.json sablonfájl tartalmát, majd kattintson a **Mentés gombra.**
   ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment3.png)
4. Kattintson **a Paraméterek szerkesztése**gombra, adja meg az egyes kötelező paraméterek értékeit, majd kattintson az **OK**gombra.  A paraméterek a következők:
   
   1. SITENAME: Megadja az App Service webalkalmazás nevét, és a webalkalmazás eléréséhez használt URL-cím létrehozásához használatos (például ha megadja a "mydemodocdbwebapp"-ot, akkor az URL-cím, amellyel a webalkalmazást `mydemodocdbwebapp.azurewebsites.net`elérheti).
   2. HOSTINGPLANNAME: Megadja a létrehozandó App Service-üzemeltetési csomag nevét.
   3. HELY: Megadja az Azure helyét, ahol az Azure Cosmos DB és a webalkalmazás-erőforrások létrehozásához.
   4. DATABASEACCOUNTNAME: Megadja a létrehozandó Azure Cosmos DB-fiók nevét.   
      
      ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment4.png)
5. Válasszon egy meglévő erőforráscsoportot, vagy adjon meg egy nevet új erőforráscsoport létrehozása érdekében, és válassza ki az erőforráscsoport helyét.

    ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment5.png)
6. Kattintson **a Jogi kifejezések áttekintése**, **a Beszerzés**parancsra, majd a **Létrehozás** gombra a központi telepítés megkezdéséhez.  Válassza **a Rögzítés az irányítópultra** lehetőséget, hogy az eredményül kapott központi telepítés könnyen látható legyen az Azure Portal kezdőlapján.
   ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment6.png)
7. Amikor a központi telepítés befejeződik, megnyílik az Erőforráscsoport ablaktábla.
   ![Képernyőkép az erőforráscsoport ablaktáblájáról](./media/create-website/TemplateDeployment7.png)  
8. Az alkalmazás használatához keresse meg a webalkalmazás URL-címét (a `http://mydemodocdbwebapp.azurewebsites.net`fenti példában az URL-cím lesz).  A következő webes alkalmazás jelenik meg:
   
   ![Példa Todo alkalmazás](./media/create-website/image2.png)
9. Hozzon létre néhány feladatot a webalkalmazásban, majd térjen vissza az Azure Portal erőforráscsoport-ablaktáblájára. Kattintson az Azure Cosmos DB-fiók erőforrására az Erőforrások listában, majd kattintson az **Adatkezelő**elemre.
10. Futtassa az alapértelmezett lekérdezést, a "SELECT * FROM c" lekérdezést, és vizsgálja meg az eredményeket.  Figyelje meg, hogy a lekérdezés lekérte a fenti 7.  Nyugodtan kísérletezzen lekérdezésekkel; próbálja meg például a SELECT * FROM c WHERE c.isComplete = true parancsot, hogy visszaadja a készként megjelölt összes teendőt.
11. Nyugodtan fedezze fel az Azure Cosmos DB portál élményét, vagy módosítsa a minta-Todo-alkalmazást.  Ha készen áll, telepítsen egy másik sablont.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>3. lépés: A dokumentumfiók és a webalkalmazás minta telepítése
Most telepítse a második sablont.  Ez a sablon hasznos, amely bemutatja, hogyan adhat be az Azure Cosmos DB-kapcsolatadatait, például a fiókvégpontot és a főkulcsot egy webalkalmazásba alkalmazásbeállításokként vagy egyéni kapcsolati karakterláncként. Például előfordulhat, hogy saját webalkalmazással rendelkezik, amelyet egy Azure Cosmos DB-fiókkal szeretne üzembe helyezni, és a kapcsolatadatait automatikusan feltölti az üzembe helyezés során.

> [!TIP]
> A sablon nem ellenőrzi, hogy a webalkalmazás neve és az Azure Cosmos DB-fiók alább megadott neve a) érvényes és b) elérhető.  Erősen ajánlott, hogy ellenőrizze a rendelkezésre álló nevek azt tervezi, hogy a központi telepítés előtt.
> 
> 

1. Az [Azure Portalon](https://portal.azure.com)kattintson az Új gombra, és keressen a "Sablon üzembe helyezése" kifejezésre.
    ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment1.png)
2. Jelölje ki a Sablon telepítési elemét, és kattintson a Képernyőkép **létrehozása** ![gombra a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment2.png)
3. Kattintson a **Sablon szerkesztése**gombra, illessze be a DocDBWebSite.json sablonfájl tartalmát, majd kattintson a **Mentés gombra.**
   ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment3.png)
4. Kattintson **a Paraméterek szerkesztése**gombra, adja meg az egyes kötelező paraméterek értékeit, majd kattintson az **OK**gombra.  A paraméterek a következők:
   
   1. SITENAME: Megadja az App Service webalkalmazás nevét, és a webalkalmazás eléréséhez használt URL-cím létrehozásához használatos (például ha megadja a "mydemodocdbwebapp"-ot, akkor a webalkalmazás elérésének URL-címe mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Megadja a létrehozandó App Service-üzemeltetési csomag nevét.
   3. HELY: Megadja az Azure helyét, ahol az Azure Cosmos DB és a webalkalmazás-erőforrások létrehozásához.
   4. DATABASEACCOUNTNAME: Megadja a létrehozandó Azure Cosmos DB-fiók nevét.   
      
      ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment4.png)
5. Válasszon egy meglévő erőforráscsoportot, vagy adjon meg egy nevet új erőforráscsoport létrehozása érdekében, és válassza ki az erőforráscsoport helyét.

    ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment5.png)
6. Kattintson **a Jogi kifejezések áttekintése**, **a Beszerzés**parancsra, majd a **Létrehozás** gombra a központi telepítés megkezdéséhez.  Válassza **a Rögzítés az irányítópultra** lehetőséget, hogy az eredményül kapott központi telepítés könnyen látható legyen az Azure Portal kezdőlapján.
   ![Képernyőkép a sablon telepítési felhasználói felületéről](./media/create-website/TemplateDeployment6.png)
7. Amikor a központi telepítés befejeződik, megnyílik az Erőforráscsoport ablaktábla.
   ![Képernyőkép az erőforráscsoport ablaktáblájáról](./media/create-website/TemplateDeployment7.png)  
8. Kattintson a Web App erőforrásra az Erőforrások listában, majd az **Alkalmazásbeállítások** ![Képernyőkép parancsra az erőforráscsoportról.](./media/create-website/TemplateDeployment9.png)  
9. Vegye figyelembe, hogy az Azure Cosmos DB-végponthoz és az Azure Cosmos DB főkulcsokhoz alkalmazásbeállítások vannak-e.

    ![Képernyőkép az alkalmazás beállításairól](./media/create-website/TemplateDeployment10.png)  
10. Nyugodtan folytassa az Azure Portal felfedezését, vagy kövesse az Azure Cosmos [DB-mintáink](https://go.microsoft.com/fwlink/?LinkID=402386) egyikét saját Azure Cosmos DB-alkalmazás létrehozásához.

<a name="NextSteps"></a>

## <a name="next-steps"></a>További lépések
Gratulálunk! Telepítette az Azure Cosmos DB-t, az App Service webalkalmazást és egy minta webalkalmazást az Azure Resource Manager-sablonok használatával.

* Ha többet szeretne megtudni az Azure Cosmos DB-ről, kattintson [ide.](https://azure.microsoft.com/services/cosmos-db/)
* Ha többet szeretne megtudni az Azure App Service webappokról, kattintson [ide.](https://go.microsoft.com/fwlink/?LinkId=325362)
* Ha többet szeretne megtudni az Azure Resource Manager-sablonokról, kattintson [ide.](https://msdn.microsoft.com/library/azure/dn790549.aspx)

## <a name="whats-changed"></a>A változások
* A webhelyekről az App Service szolgáltatásra történő módosításról a következő útmutatót talál: [Azure App Service és annak hatása a meglévő Azure-szolgáltatásokra](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, lépjen [Az App Service kipróbálása](https://go.microsoft.com/fwlink/?LinkId=523751) oldalra, ahol azonnal létrehozhat egy rövid élettartamú alapszintű webalkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
> 
> 

