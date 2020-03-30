---
title: Alkalmazások telepítése kiszámíthatóan az ARM-fel
description: Ismerje meg, hogyan telepíthet több Azure App Service-alkalmazást egyetlen egységként és kiszámítható módon az Azure Resource Management-sablonok és a PowerShell-parancsfájlok használatával.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 62d0bf776b2d0c97d95b992ed6a1fd2a356e467a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75967382"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Mikroszolgáltatások biztosítása és üzembe helyezése kiszámíthatóan az Azure-ban
Ez az oktatóanyag bemutatja, hogyan lehet kiépíteni és telepíteni egy [mikroszolgáltatásokból](https://en.wikipedia.org/wiki/Microservices) álló alkalmazást az [Azure App Service-ben](https://azure.microsoft.com/services/app-service/) egyetlen egységként és kiszámítható módon json erőforráscsoport-sablonok és PowerShell-parancsfájlok használatával. 

Nagy méretű alkalmazások kiépítése és üzembe helyezése, amelyek nagy mértékben függetlenített mikroszolgáltatások, ismételhetőség és kiszámíthatóság elengedhetetlen a sikerhez. [Az Azure App Service](https://azure.microsoft.com/services/app-service/) lehetővé teszi, hogy olyan mikroszolgáltatásokat hozzon létre, amelyek magukban foglalják a webalkalmazásokat, a mobil háttérrendszereket és az API-alkalmazásokat. [Az Azure Resource Manager](../azure-resource-manager/management/overview.md) lehetővé teszi, hogy az összes mikroszolgáltatás egy egységként, valamint az erőforrás-függőségek, például az adatbázis és a forrásvezérlő beállításokat. Most egy ilyen alkalmazást is üzembe helyezhet JSON-sablonok és egyszerű PowerShell-parancsfájlok használatával. 

## <a name="what-you-will-do"></a>Mit fog tenni?
Az oktatóanyagban egy olyan alkalmazást telepít, amely a következőket tartalmazza:

* Két App Service-alkalmazás (azaz két mikroszolgáltatás)
* Háttérrendszer-SQL-adatbázis
* Alkalmazásbeállítások, kapcsolati karakterláncok és forrásvezérlő
* Alkalmazáselemzési adatok, riasztások, automatikus skálázási beállítások

## <a name="tools-you-will-use"></a>A használni kívánt eszközök
Ebben az oktatóanyagban a következő eszközöket fogja használni. Mivel ez nem átfogó vitát eszközök, fogok ragaszkodni a end-to-end forgatókönyv, és csak kapsz egy rövid intro minden, és ahol megtalálható további információt róla. 

### <a name="azure-resource-manager-templates-json"></a>Azure Resource Manager-sablonok (JSON)
Minden alkalommal, amikor létrehoz egy alkalmazást az Azure App Service-ben, például az Azure Resource Manager egy JSON-sablont használ a teljes erőforráscsoport létrehozásához az összetevő-erőforrásokkal. Az [Azure Marketplace-ről](/azure/marketplace) származó összetett sablon oka lehet az adatbázis, a tárfiókok, az App Service-csomag, maga az alkalmazás, a riasztási szabályok, az alkalmazásbeállítások, az automatikus skálázási beállítások és egyebek, és ezek a sablonok a PowerShellen keresztül érhetők el. Az Azure Resource Manager-sablonokról az [Azure Resource Manager-sablonok készítése című témakörben olvashat bővebben.](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 a Visual Studio számára
A legújabb SDK a JSON-szerkesztő Erőforrás-kezelő sablontámogatásának fejlesztéseit tartalmazza. Ezzel gyorsan létrehozhat egy teljesen új erőforráscsoport-sablont, vagy megnyithat egy meglévő JSON-sablont (például egy letöltött katalógussablont) módosításra, feltöltheti a paraméterfájlt, és akár közvetlenül az Azure Resource-ból is telepítheti az erőforráscsoportot. Csoportmegoldás.

További információ: [Azure SDK 2.6 for Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 vagy újabb
A 0.8.0-s verziótól kezdve az Azure PowerShell-telepítés az Azure Resource Manager modult is tartalmazza az Azure modul mellett. Ez az új modul lehetővé teszi az erőforráscsoportok központi telepítésének parancsfájlfuttatását.

További információ: [Az Azure PowerShell használata az Azure Resource Managerrel című témakörben talál.](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Ez [az előzetes verziós eszköz](https://resources.azure.com) lehetővé teszi, hogy az előfizetésben és az egyes erőforrásokban szereplő összes erőforráscsoport JSON-definícióit tárja fel. Az eszközben szerkesztheti egy erőforrás JSON-definícióit, törölheti az erőforrások teljes hierarchiáját, és új erőforrásokat hozhat létre.  Az eszközben könnyen elérhető információk nagyon hasznosak a sablonkészítéshez, mert megmutatják, hogy milyen tulajdonságokat kell beállítani egy adott típusú erőforráshoz, a helyes értékeket stb. Az erőforráscsoportot még az [Azure Portalon](https://portal.azure.com/)is létrehozhatja, majd a JSON-definícióit az intéző eszközben is megtekintheti, hogy segítsen az erőforráscsoport megbontásában.

### <a name="deploy-to-azure-button"></a>Üzembe helyezés az Azure-ban gomb
Ha a GitHubot használja a forrásvezérléshez, az [Azure-ba való üzembe helyezés gombot](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) helyezheti el a README-ben. MD, amely lehetővé teszi a kulcsrakész üzembe helyezési felhasználói felületet az Azure-ba. Bár ezt bármely egyszerű alkalmazás, kiterjesztheti ezt a teljes erőforráscsoport üzembe helyezésének engedélyezéséhez egy azuredeploy.json fájl elhelyezésével a tárház gyökér. Ez a JSON-fájl, amely tartalmazza az erőforráscsoport sablon, fogja használni a Deploy az Azure-ba gombot az erőforráscsoport létrehozásához. Például tekintse meg a [ToDoApp-mintát,](https://github.com/azure-appservice-samples/ToDoApp) amelyet ebben az oktatóanyagban fog használni.

## <a name="get-the-sample-resource-group-template"></a>A mintaerőforrás-csoport sablonjának beszereznie
Szóval térjünk a térjünk a sántára.

1. Keresse meg a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service minta.
2. A readme.md kattintson **a Telepítés az Azure-ba gombra.**
3. A rendszer az [azure-ba üzembe helyezési](https://deploy.azure.com) helyre kerül, és megkéri a központi telepítési paraméterek bevitelét. Figyelje meg, hogy a legtöbb mező a tárház nevével és néhány véletlenszerű karakterláncgal van feltöltve. Az összes mezőt módosíthatja, ha szeretné, de csak az SQL Server felügyeleti bejelentkezési adatait és a jelszót kell megadnia, majd kattintson a **Tovább**gombra.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Ezután kattintson **a Telepítés gombra** a telepítési folyamat elindításához. Miután a folyamat befejeződött, kattintson az http://todoapp *XXXX*.azurewebsites.net hivatkozásra az üzembe helyezett alkalmazás tallózásához. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   A felhasználói felület egy kicsit lassú lenne, amikor először böngészik, mert az alkalmazások csak most indulnak el, de meggyőzni magukat, hogy ez egy teljesen működőképes alkalmazás.
5. A Központi telepítés lapon kattintson a **Kezelés** hivatkozásra az új alkalmazás megtekintéséhez az Azure Portalon.
6. Az **Essentials** legördülő menüben kattintson az erőforráscsoport hivatkozására. Vegye figyelembe azt is, hogy az alkalmazás már csatlakozik a GitHub-tárházhoz a **Külső projekt**csoportban. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Az erőforráscsoport panelen vegye figyelembe, hogy már két alkalmazás és egy SQL-adatbázis van az erőforráscsoportban.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Minden, amit az imént látott néhány rövid perc alatt egy teljesen üzembe helyezett két mikroszolgáltatású alkalmazás, az összes összetevőt, függőséget, beállításokat, adatbázist és folyamatos közzétételt, amelyet az Azure Resource Manager automatikus vezénylése állít be. Mindez két dologmiatt történt:

* Az Üzembe helyezés az Azure-ba gomb
* azuredeploy.json a tárcsagyökérben

Ezt az alkalmazást több tíz, száz vagy ezer alkalommal is telepítheti, és minden alkalommal pontosan ugyanazt a konfigurációt használhatja. A megközelítés ismételhetősége és kiszámíthatósága lehetővé teszi, hogy könnyedén és magabiztosan telepítsen nagy méretű alkalmazásokat.

## <a name="examine-or-edit-azuredeployjson"></a>Vizsgálja meg (vagy szerkesztheti) AZ AZUREDEPLOY-t. Json
Most nézzük meg, hogyan lett beállítva a GitHub-tárház. A JSON-szerkesztőt az Azure .NET SDK-ban fogja használni, így ha még nem telepítette az [Azure .NET SDK 2.6-ot,](https://azure.microsoft.com/downloads/)tegye meg most.

1. Klónozza a [ToDoApp-tárházat](https://github.com/azure-appservice-samples/ToDoApp) a kedvenc git eszközével. Az alábbi képernyőképen ezt a Visual Studio 2013 Csapatkezelőjében csinálom.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. A tárház gyökér, nyissa azuredeploy.json a Visual Studio.From the repository root, open azuredeploy.json in Visual Studio. Ha nem látja a JSON Tagolás ablaktáblát, telepítenie kell az Azure .NET SDK-t.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nem fogom leírni a JSON formátum minden részletét, de a [További erőforrások](#resources) szakasz hivatkozásokat tartalmaz az erőforráscsoport sablonnyelvének megismerésére. Itt csak megmutatom azérdekes funkciókat, amelyek segíthetnek a saját egyéni sablon készítésében az alkalmazás telepítéséhez.

### <a name="parameters"></a>Paraméterek
Tekintse meg a paraméterek szakaszban, hogy a legtöbb ilyen paraméterek, amit a **Deploy to Azure** gomb kéri, hogy adja meg. Az **Azure-ba való üzembe helyezés** mögötti hely feltölti a bemeneti felhasználói felületet az azuredeploy.json ban meghatározott paraméterek használatával. Ezek a paraméterek az erőforrás-definíciók ban használatosak, például erőforrásnevek, tulajdonságértékek stb.

### <a name="resources"></a>Források
Az erőforrás-csomópontban láthatja, hogy 4 legfelső szintű erőforrás van definiálva, beleértve az SQL Server-példányt, az App Service-csomagot és két alkalmazást. 

#### <a name="app-service-plan"></a>App Service-csomag
Kezdjük egy egyszerű gyökérszintű erőforrással a JSON-ban. A JSON tagolásban kattintson a **[hostingPlanName]** nevű App Service-csomagra a megfelelő JSON-kód kiemeléséhez. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Vegye figyelembe, hogy az `type` elem egy App Service-csomag karakterláncát adja meg (ezt már régóta kiszolgálófarmnak hívták), és más elemek és tulajdonságok a JSON-fájlban meghatározott paraméterek kel vannak kitöltve, és ez az erőforrás nem rendelkezik beágyazott erőforrásokkal.

> [!NOTE]
> Vegye figyelembe azt `apiVersion` is, hogy az Azure-nak a REST API melyik verziójával kell használnia a JSON-erőforrás-definíciót, és hatással lehet arra, hogy az erőforrást hogyan kell formázni a. `{}` 
> 
> 

#### <a name="sql-server"></a>SQL Server
Ezután kattintson az SQLServer nevű **SQLServer** erőforrásra a JSON tagolásban.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Vegye figyelembe a következőt a kiemelt JSON-kóddal kapcsolatban:

* A paraméterek használata biztosítja, hogy a létrehozott erőforrások neve és konfigurálása oly módon, hogy azok összhangban vannak egymással.
* Az SQLServer erőforrás két beágyazott erőforrással rendelkezik, mindegyiknek más az `type`értéke.
* A beágyazott erőforrások `“resources”: […]`belül , ahol az adatbázis és a `dependsOn` tűzfalszabályok vannak definiálva, van egy elem, amely meghatározza a gyökérszintű SQLServer erőforrás azonosítóját. Ez közli az Azure Resource Manager, "mielőtt létrehozza ezt az erőforrást, hogy más erőforrás már léteznie kell; és ha a másik erőforrás definiálva van a sablonban, akkor először hozza létre azt".
  
  > [!NOTE]
  > A függvény használatáról az `resourceId()` Azure [Resource Manager sablonfüggvényei](../azure-resource-manager/templates/template-functions-resource.md#resourceid)című témakörben olvashat részletesen.
  > 
  > 
* Az elem `dependsOn` hatása az, hogy az Azure Resource Manager tudja, hogy mely erőforrások hozhatók létre párhuzamosan, és mely erőforrásokat kell egymás után létrehozni. 

#### <a name="app-service-app"></a>App Service-alkalmazás
Most térjünk át maguka tényleges alkalmazásokra, amelyek bonyolultabbak. Kattintson a JSON tagolás [variables('apiSiteName')] alkalmazásra a JSON tagolásban a JSON-kód kiemeléséhez. Észre fogod venni, hogy a dolgok egyre érdekesebbek. Erre a célra, fogok beszélni a funkciók egyenként:

##### <a name="root-resource"></a>Gyökérerőforrás
Az alkalmazás két különböző erőforrástól függ. Ez azt jelenti, hogy az Azure Resource Manager csak az App Service-csomag és az SQL Server-példány létrehozása után hozza létre az alkalmazást.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Alkalmazásbeállítások
Az alkalmazás beállításai beágyazott erőforrásként is definiálva vannak.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

A `properties` alkalmazásban `config/appsettings`két alkalmazásbeállítás van a `"<name>" : "<value>"`formátumban.

* `PROJECT`EGY [KUDU beállítás,](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) amely közli az Azure üzembe helyezését, hogy melyik projektet használja egy többprojektes Visual Studio-megoldásban. Később megmutatom, hogyan van beállítva a forrásvezérlés, de mivel a ToDoApp kód egy többprojektes Visual Studio megoldásban van, szükségünk van erre a beállításra.
* `clientUrl`egyszerűen egy alkalmazásbeállítás, amelyet az alkalmazáskód használ.

##### <a name="connection-strings"></a>Kapcsolati sztringek
A kapcsolati karakterláncok beágyazott erőforrásként is definiálva vannak.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

A `properties` elemben `config/connectionstrings`minden kapcsolati karakterlánc name:value pair néven is definiálva `"<name>" : {"value": "…", "type": "…"}`van, a megadott formátumú . Az `type` elem esetében a `MySql` `SQLServer`lehetséges `SQLAzure`értékek `Custom`a , , , és .

> [!TIP]
> A kapcsolati karakterlánc-típusok végleges listájához futtassa a \[következő parancsot az Azure PowerShellben: Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Verziókövetés
A forrásvezérlő beállításai beágyazott erőforrásként is definiálva vannak. Az Azure Resource Manager ezt az erőforrást `IsManualIntegration` használja a folyamatos közzététel konfigurálásához (lásd később a kikötést), valamint az alkalmazáskód automatikus telepítésének indításához a JSON-fájl feldolgozása során.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`és `branch` kell elég intuitív, és meg kell mutatnia, hogy a Git repository és a nevét, hogy a fióktelep közzétenni. Ezeket ismét bemeneti paraméterek határozzák meg. 

Megjegyzés az `dependsOn` elemben, hogy az alkalmazás-erőforrás on `config/appsettings` `config/connectionstrings`kívül atól is függ, `sourcecontrols/web` és . Ennek az `sourcecontrols/web` az oka, hogy ha egyszer van konfigurálva, az Azure központi telepítési folyamat automatikusan megpróbálja üzembe helyezni, építeni, és indítsa el az alkalmazáskódot. Ezért a függőség beszúrásával meggyőződheti, hogy az alkalmazás hozzáfér-e a szükséges alkalmazásbeállításokhoz és kapcsolati karakterláncokhoz az alkalmazáskód futtatása előtt. 

> [!NOTE]
> Vegye figyelembe `IsManualIntegration` azt `true`is, hogy a beállítása . Ez a tulajdonság azért szükséges ebben az oktatóanyagban, mert valójában nem rendelkezik a GitHub-tárház, és így valójában nem adhat engedélyt az Azure-nak a folyamatos közzététel konfigurálására [a ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) -ból (azaz az automatikus tárház-frissítések leküldése az Azure-ba). A megadott tárház `false` alapértelmezett értékét csak akkor használhatja, ha már korábban konfigurálta a tulajdonos GitHub-hitelesítő adatait az [Azure Portalon.](https://portal.azure.com/) Más szóval, ha korábban beállította a GitHub vagy a BitBucket forrásvezérlését az [Azure Portalbármely](https://portal.azure.com/) alkalmazásához a felhasználói hitelesítő adatok használatával, akkor az Azure megjegyzi a hitelesítő adatokat, és használja őket, amikor a jövőben bármilyen alkalmazást telepít a GitHubról vagy a BitBucket-ből. Ha azonban ezt még nem tette meg, a JSON-sablon telepítése sikertelen lesz, amikor az Azure Resource Manager megpróbálja konfigurálni az alkalmazás forrásvezérlő beállításait, mert nem tud bejelentkezni a GitHubra vagy a BitBucket-be a tárház tulajdonosának hitelesítő adataival.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>A JSON-sablon összehasonlítása az üzembe helyezett erőforráscsoporttal
Itt végighaladhat az alkalmazás összes paneljén az [Azure Portalon,](https://portal.azure.com/)de van egy másik eszköz, amely ugyanolyan hasznos, ha nem több. Nyissa meg az [Azure Resource Explorer](https://resources.azure.com) előzetes verzióeszközt, amely az előfizetésekben lévő összes erőforráscsoport JSON-ábrázolását biztosítja, mivel azok az Azure-háttérrendszerben ténylegesen léteznek. Azt is láthatja, hogy az erőforráscsoport JSON-hierarchiája az Azure-ban hogyan felel meg a sablonfájlban lévő hierarchiának, amely létrehozásához szolgál.

Ha például az [Azure Resource Explorer](https://resources.azure.com) eszközt, és bontsa ki a csomópontokat a felfedező, látom az erőforráscsoport és a gyökérszintű erőforrások, amelyek a megfelelő erőforrástípusok alatt gyűjtött.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Ha leás egy alkalmazásba, az alábbi képernyőképhez hasonló módon láthatja az alkalmazás konfigurációjának részleteit:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

A beágyazott erőforrásoknak is nagyon hasonló hierarchiával kell rendelkezniük a JSON-sablonfájlban lévőkhöz, és a JSON-ablaktáblában megfelelően tükröződő alkalmazásbeállításokat, kapcsolati karakterláncokat stb. A beállítások hiánya a JSON-fájllal kapcsolatos problémát jelezhet, és segíthet a JSON-sablonfájl hibaelhárításában.

## <a name="deploy-the-resource-group-template-yourself"></a>Az erőforráscsoport-sablon telepítése saját kezűleg
Az **Azure üzembe helyezése** gomb nagyszerű, de lehetővé teszi, hogy az erőforráscsoport-sablon t akar azuredeploy.json csak akkor, ha már leküldte azuredeploy.json a GitHub. Az Azure .NET SDK is biztosítja az eszközöket, hogy bármilyen JSON-sablonfájlt közvetlenül a helyi gépről telepíthet. Ehhez kövesse az alábbi lépéseket:

1. A Visual Studio programban kattintson az**Új** > **projekt** **fájlja** > gombra.
2. Kattintson **a Visual C#** > **Cloud** > **Azure Resource Group**elemre, majd az **OK**gombra.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. Az **Azure-sablon kiválasztása**csoportban válassza az **Üres sablon lehetőséget,** és kattintson **az OK**gombra.
4. Húzza az azuredeploy.json fájlt az új projekt **Sablon** mappájába.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. A Solution Explorer ből nyissa meg a másolt azuredeploy.json.
6. Csak a bemutató kedvéért adjunk hozzá néhány szabványos Application Insight erőforrást a JSON fájlhoz, kattintson az **Erőforrás hozzáadása**gombra. Ha csak a JSON-fájl üzembe helyezése érdekli, ugorjon a üzembe helyezési lépésekre.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Válassza **az Application Insights for Web Apps lehetőséget,** majd győződjön meg arról, hogy egy meglévő App Service-csomag és alkalmazás ki van jelölve, majd kattintson a **Hozzáadás gombra.**
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Mostantól számos új erőforrást láthat, amelyek az erőforrástól és annak tőlük függően függőséget tartalmaznak az App Service-csomagtól vagy az alkalmazástól. Ezeket az erőforrásokat a meglévő definíciójuk nem teszi lehetővé, és ezt meg fogja változtatni.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. A JSON vázlat, kattintson **appInsights Automatikus skálázás** kiemelni a JSON-kódot. Ez az App Service-csomag méretezési beállítása.
9. A kiemelt JSON-kódban `location` keresse `enabled` meg a és a tulajdonságokat, és állítsa be őket az alábbi módon.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. A JSON vázlat, kattintson **a CPUHigh appInsights** kiemelni a JSON-kódot. Ez egy riadó.
11. Keresse `location` meg `isEnabled` a és a tulajdonságokat, és állítsa be őket az alábbi módon. Ugyanezt a másik három riasztások (lila izzók).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Most már készen áll a bevetésre. Kattintson a jobb gombbal a projektre, és válassza az Új telepítés **telepítése parancsot.** > **New Deployment**
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Ha még nem tette meg, jelentkezzen be Azure-fiókjába.
14. Jelöljön ki egy meglévő erőforráscsoportot az előfizetésben, vagy hozzon létre egy újat, válassza **az azuredeploy.json**lehetőséget, majd kattintson **a Paraméterek szerkesztése gombra.**
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Most már szerkesztheti a sablonfájlban definiált összes paramétert egy szép táblában. Az alapértelmezett értékeket definiáló paraméterek nek már meg lesznek az alapértelmezett értékei, és az engedélyezett értékek listáját meghatározó paraméterek legördülő menükként jelennek meg.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Töltse ki az összes üres paramétert, és használja a [GitHub repo címét a ToDoApp-hoz](https://github.com/azure-appservice-samples/ToDoApp.git) a **repoUrl-ben.** Ezután kattintson a **Mentés gombra.**
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Az automatikus skálázás a **Standard** szint vagy magasabb szinten érhető el, és a csomagszintű riasztások **alapszintű** vagy magasabb szintű funkciók, a **sku** paramétert **Standard** vagy **Prémium** szintre kell állítania ahhoz, hogy az összes új App Insights-erőforrás kigyulladjon.
    > 
    > 
16. Kattintson **a Telepítés gombra.** Ha a **Jelszavak mentése**lehetőséget választotta, a jelszó egyszerű szövegként kerül a paraméterfájlba. **in plain text** Ellenkező esetben a rendszer megkéri, hogy adja meg az adatbázis jelszavát a központi telepítési folyamat során.

Készen is van. Most már csak meg kell mennie az [Azure Portalon](https://portal.azure.com/) és az [Azure Resource Explorer](https://resources.azure.com) eszköza az új riasztások és automatikus skálázási beállítások a JSON üzembe helyezett alkalmazáshoz hozzáadott.

Az ebben a szakaszban ismertetett lépések főként a következőket tették:

1. Előkészítette a sablonfájlt
2. A sablonfájlhoz kapcsolódó paraméterfájl létrehozása
3. A sablonfájl telepítése a paraméterfájllal

Az utolsó lépés könnyen elvégezhető egy PowerShell-parancsmag. Ha meg szeretné tekinteni, hogy a Visual Studio mit tett az alkalmazás üzembe helyezésekor, nyissa meg a Parancsfájlok\Deploy-AzureResourceGroup.ps1 webhelyet. Van egy csomó kódot, de én csak megyek, hogy kiemelje az összes vonatkozó kódot kell telepíteni a sablon fájlt a paraméter fájlt.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Az utolsó parancsmag `New-AzureResourceGroup`, az, amely ténylegesen végrehajtja a műveletet. Mindez azt bizonyítja, hogy a segítségével a szerszámozás, viszonylag egyszerű a felhőalapú alkalmazás telepítése kiszámíthatóan. Minden alkalommal, amikor a parancsmag ugyanazon a sablonon fut ugyanazon a paraméterfájlon, ugyanazt az eredményt kapja.

## <a name="summary"></a>Összefoglalás
A DevOps-ban az ismételhetőség és a kiszámíthatóság a mikroszolgáltatásokból álló nagy méretű alkalmazások sikeres üzembe helyezésének kulcsai. Ebben az oktatóanyagban egy két mikroszolgáltatású alkalmazást telepített az Azure-ba egyetlen erőforráscsoportként az Azure Resource Manager sablon használatával. Remélhetőleg megadta önnek a szükséges ismereteket ahhoz, hogy megkezdhesse az alkalmazás átalakítását az Azure-ban egy sablonná, és kiszámíthatóan üzembe helyezheti azt. 

<a name="resources"></a>

## <a name="more-resources"></a>További erőforrások
* [Az Azure Resource Manager sablonnyelve](../azure-resource-manager/templates/template-syntax.md)
* [Az Azure Resource Manager-sablonok készítése](../azure-resource-manager/templates/template-syntax.md)
* [Az Azure Resource Manager sablonfüggvényei](../azure-resource-manager/templates/template-functions.md)
* [Alkalmazás üzembe helyezése az Azure Resource Manager-sablonnal](../azure-resource-manager/templates/deploy-powershell.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Erőforráscsoport-telepítések hibaelhárítása az Azure-ban](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>További lépések

A json szintaxisáról és a cikkben üzembe helyezett erőforrástípusok tulajdonságairól az alábbi témakörökben olvashat:

* [Microsoft.Sql/kiszolgálók](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/webhelyek](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slot](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)