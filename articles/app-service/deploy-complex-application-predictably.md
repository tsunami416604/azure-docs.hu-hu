---
title: Létrehozhatja és üzembe helyezheti a mikroszolgáltatások kiszámítható módon – az Azure App Service-ben
description: Ismerje meg, hogyan helyezhet üzembe egy alkalmazás mikroszolgáltatásokból álló, az Azure App Service egyetlen egységként, és a egy erőforráscsoport-sablonok JSON és a PowerShell-parancsfájlok használatával kiszámítható módon.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 657211378d7b38b88ccd40aa31a175058e1ad67c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015556"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Létrehozhatja és üzembe helyezheti a mikroszolgáltatások kiszámítható módon az Azure-ban
Ez az oktatóanyag bemutatja, hogyan létrehozása és üzembe helyezése egy alkalmazás összetevői [mikroszolgáltatások](https://en.wikipedia.org/wiki/Microservices) a [Azure App Service](https://azure.microsoft.com/services/app-service/) egyetlen egységként, és a egy erőforráscsoport-sablonok JSON használatával kiszámítható módon és PowerShell-parancsprogramok. 

Kiépítés, és üzembe helyezésekor épülnek fel, magas leválasztott nagy méretű alkalmazások mikroszolgáltatások, ismételhetőség és kiszámíthatóságot fontosságúak sikeres. [Az Azure App Service](https://azure.microsoft.com/services/app-service/) hozhatók létre, amelyek tartalmazzák a web apps, mobile háttérrendszerek és API-alkalmazások. [Az Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) lehetővé teszi, hogy mindegyik mikroszolgáltatás kezelését olyan adategység, például az adatbázis erőforrás-függőségek együtt, és a forrás-ellenőrzési beállítások. Most ilyen alkalmazás JSON-sablonok és egyszerű PowerShell-parancsfájlok használatával is telepítheti. 

## <a name="what-you-will-do"></a>Mit fog
Az oktatóanyagban, amely tartalmazza az alkalmazás telepíti:

* Két App Service-alkalmazások (pl. két mikroszolgáltatás-alapú)
* A háttér SQL-adatbázis
* Alkalmazásbeállítások, kapcsolati karakterláncok és verziókövetés
* Az Application insights, riasztások, az automatikus skálázási beállítások

## <a name="tools-you-will-use"></a>Ezzel a eszközök
Ebben az oktatóanyagban a következő eszközöket fogja használni. Nem érhető el az eszközök átfogó leírást, mivel fogom elgondolkodni a teljes körű forgatókönyvre, és csak adhat meg egy rövid bevezető, és ha talál további tájékoztatást. 

### <a name="azure-resource-manager-templates-json"></a>Az Azure Resource Manager-sablonokat (JSON)
Minden alkalommal, amikor az Azure App Service-alkalmazást hoz létre, például az Azure Resource Manager használja egy JSON-sablon létrehozásához a teljes erőforráscsoport az összetevő-erőforrások. A komplex sablonjának a [Azure Marketplace-en](/azure/marketplace) lehetnek az adatbázis, tárfiókok, az App Service-csomag, maga az alkalmazás, riasztási szabályok, beállítások, az automatikus méretezési beállítások, és több, és ezek a sablonok érhetők el, a PowerShell. Töltse le és használja ezeket a sablonokat a további információkért lásd: [az Azure PowerShell az Azure Resource Manager](../powershell-azure-resource-manager.md).

Az Azure Resource Manager-sablonokat a további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>A Visual Studióhoz készült Azure SDK 2.6-os
A legújabb SDK és a Resource Manager sablon támogatja a JSON-szerkesztőben tartalmaz. Ennek használatával gyorsan hozzon létre egy erőforráscsoport sablonjához teljesen új, vagy nyisson meg egy meglévő JSON-sablon (például egy letöltött katalógus sablonjának) módosítását, töltse ki a paramétereket tartalmazó fájlt, és az erőforráscsoport, közvetlenül az Azure-erőforrás is üzembe Csoport megoldás.

További információkért lásd: [Visual Studióhoz készült Azure SDK 2.6](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Az Azure PowerShell 0.8.0 vagy újabb
Kezdve 0.8.0, az Azure PowerShell telepítése mellett az Azure-modul az Azure Resource Manager modult tartalmaz. Ez a modul lehetővé teszi az erőforráscsoportok üzembe helyezési parancsfájlt.

További információkért lásd: [az Azure PowerShell az Azure Resource Managerrel](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Ez [előzetes eszköz](https://resources.azure.com) lehet felfedezni az előfizetés és az egyedi erőforrásokat az erőforráscsoportok JSON-definíciói. Az eszközben szerkesztheti egy erőforrást a JSON-definíciók, az egész hierarchiát az erőforrások törlése és új erőforrásokat hozhat létre.  Ez az eszköz azonnal elérhető információk nagyon hasznos a sablon létrehozásához, mert jeleníti meg, milyen tulajdonságok, be kell állítani egy adott típusú erőforrás, a helyes értékeket, stb. Az erőforráscsoportban is létrehozható a [az Azure Portal](https://portal.azure.com/), majd vizsgálja meg annak a explorer eszköz segítségével sablonok kialakítása az erőforráscsoport a JSON-definíciói.

### <a name="deploy-to-azure-button"></a>Deploy to Azure gombbal
Ha GitHub a verziókezelő használja, akkor lehet helyezni egy [Deploy to Azure gombbal](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) azokat az információs fájlban OLVASHATÓ. MD, amely lehetővé teszi egy kulcsrakész üzembe helyezését az Azure-bA felhasználói felület. Ezt megteheti bármilyen egyszerű alkalmazás, amíg ezt a lehetőséget egy teljes erőforráscsoport üzembe az azuredeploy.json fájlt az adattár gyökérkönyvtárában való központi telepítéséhez, bővítheti. A JSON-fájlt, az erőforráscsoport sablonjának tartalmaz, amelyek használják az üzembe helyezés az Azure-ban gombra az erőforráscsoport létrehozásához. Egy vonatkozó példáért tekintse meg a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) minta, amely ebben az oktatóanyagban használhatja.

## <a name="get-the-sample-resource-group-template"></a>A minta erőforráscsoport sablonjának beolvasása
Tehát most folytassuk a megfelelő rá.

1. Keresse meg a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) minta App Service-ben.
2. A readme.md, kattintson **üzembe helyezés az Azure**.
3. Ekkor átkerül a [üzembe helyezése – azure](https://deploy.azure.com) webhelyről, és ismételt üzembe helyezéshez megadott paraméterek beviteli. Figyelje meg, hogy a mezők fel van töltve a tárház nevének és a egy véletlenszerű karakterlánc az Ön számára. Ha azt szeretné, de a dolog csak meg kell adnia az SQL Server-rendszergazdai bejelentkezés és a jelszót, majd kattintson az összes mezőt módosíthatja **tovább**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Ezután kattintson **telepítés** az üzembe helyezés elindításához. Ha a folyamat befejezéséig fut, kattintson a http://todoapp *XXXX*. azurewebsites.net hivatkozásra a telepített alkalmazás tallózással. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   A felhasználói felület lenne először böngészése során, mivel az alkalmazások most használja először kell egy kicsit lassan, de meggyőzni a felhasználót saját magának, hogy-e egy teljesen működőképes alkalmazást.
5. A telepítés lapon kattintson a **kezelés** hivatkozásra kattintva megtekintheti az új alkalmazás az Azure Portalon.
6. Az a **Essentials** legördülő menüben kattintson az erőforrás-csoport hivatkozásra. Fontos megjegyezni, hogy az alkalmazás már csatlakoztatva van a GitHub-adattár alatt **külső projekt**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Az erőforráscsoport panelen fontos, hogy nincsenek már két alkalmazás és a egy SQL Database az erőforráscsoportban.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Mindent, ami az imént látott néhány rövid perc alatt egy teljes mértékben üzembe helyezett két-mikroszolgáltatás-alkalmazás, az összes összetevői, függőségek, beállítások, adatbázis, és folyamatos közzétételre, állíthatja be egy automatizált vezénylési az Azure Resource Manager. Mind a két dolgokat végezhető el:

* Üzembe helyezés az Azure-ban gombra
* az adattár gyökérkönyvtárában azuredeploy.JSON

Ez az alkalmazás telepíthet több tíz, száz vagy ezer alkalommal, és pontosan ugyanaz a konfiguráció minden alkalommal. Az ismételhetőség és a háttérrendszeren, ez a megközelítés lehetővé teszi nagy léptékben méretezhető alkalmazások egyszerű és megbízható központi telepítése.

## <a name="examine-or-edit-azuredeployjson"></a>Vizsgálja meg (vagy szerkesztése) AZUREDEPLOY. JSON-BAN
Most nézzük, hogyan a GitHub-tárház be lett állítva. Az Azure .NET SDK-ban a JSON-szerkesztőt használni, ha még nem telepítette [Azure .NET SDK 2.6-os](https://azure.microsoft.com/downloads/), most megtenni.

1. Klónozás a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) tárház a kedvenc git-eszközt használ. Az alábbi képernyőképen ezt ennek a Team Explorerben a Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Az adattár gyökérkönyvtárában nyissa meg az azuredeploy.json a Visual Studióban. Ha nem látja a JSON-vázlat ablak, Azure .NET SDK telepítése szeretné.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nem fogom írja le a JSON-formátumban, minden részletét, de a [további erőforrások](#resources) szakasz csoport sablon erőforrásnyelv tanulási hivatkozásokkal rendelkezik. Itt most elindítok mutatni, az érdekes funkciók, amelyek segítségével első lépései a saját, egyéni sablont az alkalmazás üzembe helyezése során.

### <a name="parameters"></a>Paraméterek
Vessen egy pillantást a Paraméterek szakaszban megtekintheti, hogy ezeket a paramétereket a legtöbb, mi a **üzembe helyezés az Azure** gomb felszólítja, hogy adjon meg. A hely mögött a **üzembe helyezés az Azure** gomb tölti fel a bemeneti felhasználói felület azuredeploy.json megadott paraméterek használatával. Ezek a paraméterek előfordulnak az erőforrás-definíciókban, például az erőforrás nevét, a tulajdonságértékek, stb.

### <a name="resources"></a>További források
Az erőforrások csomóponthoz láthatja, hogy 4 legfelső szintű erőforrások meg vannak határozva, például egy SQL Server-példány, egy App Service-csomagot és két alkalmazás. 

#### <a name="app-service-plan"></a>App Service-csomag
Kezdjük a JSON egyszerű gyökér szintű erőforrás. Kattintson a JSON-vázlat nevű App Service-csomag **[hostingPlanName]** jelölje ki a megfelelő JSON-kódot. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Vegye figyelembe, hogy a `type` elem karakterláncot határozza meg az App Service-csomag (azt byla volána kiszolgálófarmot ezelőtt hosszú, hosszú idő), és más elemeket és a Tulajdonságok ki vannak töltve a JSON-fájlban definiált paraméterek használatával, és az erőforrás nem rendelkezik ilyennel a beágyazott az erőforrásokat.

> [!NOTE]
> Figyelje meg azt is, amelyek értékét `apiVersion` jelzi az Azure melyik verzióját a REST API-t használja a JSON-erőforrás-definícióban, és hatással lehet a hogyan az erőforrás kell formázni belül a `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Ezután kattintson az SQL Server-erőforrásnév **SQLServer** a JSON-Vázlat.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Vegye figyelembe a következő információk a kiemelt JSON-kódot:

* Paraméterek használatával biztosítja a létrehozott erőforrásokat nevű és konfigurálni úgy, hogy egymással konzisztenssé teszi őket.
* Az SQL Server erőforrás rendelkezik két beágyazott erőforrások, mindegyiknek eltérő értékeket `type`.
* A beágyazott erőforrások belül `“resources”: […]`, ahol definiálva vannak az adatbázisban és a tűzfalszabályokat, rendelkezik egy `dependsOn` elem, amely megadja a gyökér szintű SQL Server erőforrás erőforrás-Azonosítóját. Ez közli az Azure Resource Manager "ennek az erőforrásnak, amely a többi erőforrás már léteznie kell; létrehozása előtt és ha a többi erőforrás van definiálva a sablonban, majd hozza létre, hogy egy először".
  
  > [!NOTE]
  > További információk a használatát a `resourceId()` működik, tekintse meg [Azure Resource Manager-Sablonfüggvények](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* A hatása a `dependsOn` elem, hogy az Azure Resource Manager tudhatja, mely erőforrásokat párhuzamosan hozhatja létre, és mely erőforrások egymás után létre kell hozni. 

#### <a name="app-service-app"></a>App Service-alkalmazás
Most már továbbvezet a tényleges alkalmazások, melyek bonyolultabbá. Kattintson a [variables('apiSiteName')] alkalmazás a JSON-vázlat jelölje ki a JSON-kódot. Láthatja, hogy dolgot első sokkal több érdekes. Erre a célra a szolgáltatások egyenként fogok beszélni:

##### <a name="root-resource"></a>Legfelső szintű erőforrás
Az alkalmazás két különböző erőforrások függ. Ez azt jelenti, hogy az Azure Resource Manager az alkalmazás létrehozásához, csak az App Service-csomag és az SQL Server-példány létrehozása után.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Alkalmazásbeállítások
A beállítások egy beágyazott erőforrást is vannak meghatározva.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

Az a `properties` eleme `config/appsettings`, a következő formátumban van két alkalmazás beállítását `"<name>" : "<value>"`.

* `PROJECT` van egy [KUDU beállítás](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , amely közli, az Azure-telepítés használata több projektet a Visual Studio-megoldásban projektet. Bemutatom majd, hogyan később verziókövetés van konfigurálva, de mivel a ToDoApp kód több projektet a Visual Studio-megoldásban, ezt a beállítást kell.
* `clientUrl` az egyszerűen egy alkalmazás, beállítás, amely az alkalmazáskód használja.

##### <a name="connection-strings"></a>Kapcsolati sztringek
A kapcsolati karakterláncok egy beágyazott erőforrást is vannak meghatározva.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

Az a `properties` eleme `config/connectionstrings`, minden egyes kapcsolati karakterláncot is definiálva van: név-érték párban, meghatározott formátumban `"<name>" : {"value": "…", "type": "…"}`. Az a `type` elem, a lehetséges értékek: `MySql`, `SQLServer`, `SQLAzure`, és `Custom`.

> [!TIP]
> A kapcsolati karakterlánc típusú végleges listáját a következő parancsot az Azure PowerShell-lel: \[Enum]::GetNames("Microsoft.WindowsAzure.commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Verziókövetés
Az adatforrás-ellenőrzési beállítások egy beágyazott erőforrást is vannak meghatározva. Az Azure Resource Manager használja ezt az erőforrást folyamatos közzététel konfigurálása (lásd: csoportosítani a `IsManualIntegration` később) is automatikusan a JSON-fájl feldolgozása során az alkalmazáskód telepítését indíthat, és.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` és `branch` viszonylag intuitív kell lennie, és a Git-tárházba, és szeretné közzétenni az ág nevét kell mutatnia. Újra ezek határozzák meg a bemeneti paraméterek. 

Vegye figyelembe a a `dependsOn` elem mellett az alkalmazás-erőforrást, amely `sourcecontrols/web` is függ, `config/appsettings` és `config/connectionstrings`. Ennek oka, hogy miután `sourcecontrols/web` van konfigurálva, az Azure üzembe helyezési folyamat automatikusan megpróbálja telepíteni, hozhat létre és indítsa el az alkalmazás kódja. Ezért ezt a függőséget szúr be segítségével győződjön meg arról, hogy az alkalmazás hozzáfér a szükséges alkalmazások beállításai és a kapcsolati karakterláncok az alkalmazás kódjának futtatása előtt. 

> [!NOTE]
> Ügyeljen a következőkre is `IsManualIntegration` értékre van állítva `true`. Ez a tulajdonság nem szükséges ebben az oktatóanyagban, mert a GitHub-adattárból ténylegesen nem tulajdonosa, és így nem ténylegesen engedélyt az Azure-ba való folyamatos közzétételre konfigurálása [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (pl. leküldéses Automatikus adattár frissítés az Azure-bA). Használhatja az alapértelmezett érték `false` csak akkor, ha már konfigurálta a tulajdonos GitHub hitelesítő adatait a megadott adattár a [az Azure portal](https://portal.azure.com/) előtt. Más szóval ha minden olyan alkalmazáshoz, a GitHub vagy bitbucket-alapú Verziókövetés beállítása a [az Azure Portal](https://portal.azure.com/) korábban, a felhasználó hitelesítő adatainak használata, akkor az Azure lesz a hitelesítő adatok megjegyzése, és használja őket, amikor minden olyan alkalmazást telepít GitHub vagy bitbucket-alapú a jövőben. Azonban Ön tette meg, ha a JSON-sablon telepítése sikertelen lesz, ha az alkalmazás forrás vezérlő beállítások konfigurálása, mert az adattár tulajdonosának hitelesítő adatokkal nem jelentkezhet be a GitHub vagy bitbucket-alapú megpróbálja Azure Resource Manager.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Hasonlítsa össze a telepített erőforráscsoportot a JSON-sablon
Itt megnyithatja a az alkalmazás paneleken keresztül a [az Azure Portal](https://portal.azure.com/), azonban egy másik eszköz, amely hasonlóan lehet hasznos, ha nem több. Nyissa meg a [Azure erőforrás-kezelő](https://resources.azure.com) előzetes eszközt, amely lehetővé teszi az erőforráscsoportok JSON-reprezentációja az előfizetésekben, valójában az Azure-beli háttéralkalmazásának léteznek. Az erőforráscsoport JSON hierarchia az Azure-ban hogyan felel meg a hierarchiát a létrehozásához használt sablon fájlban is megjelenik.

Például ha megnyitom a [Azure erőforrás-kezelő](https://resources.azure.com) eszközt, és bontsa ki a csomópontokat a Explorerben, látható, hogy az erőforráscsoport és a gyökérszintű erőforrások a megfelelő erőforrástípusok szerint gyűjtött.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Ha egy alkalmazás részletezéssel, láthatja a hasonló az alkalmazás-konfigurációs adatait kell az alábbi képernyőfelvétel:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Újra a beágyazott erőforrások nagyon hasonlóak a JSON-sablonfájlt hierarchia kell rendelkeznie, és megjelenik az alkalmazásbeállítások, kapcsolati karakterláncok, stb., megfelelően a JSON-panelen megjelennek. Érhető el a beállításokat itt jelezheti a problémát a JSON-fájllal, és a JSON-sablonfájlt hibaelhárításához nyújt segítséget.

## <a name="deploy-the-resource-group-template-yourself"></a>Az erőforráscsoport sablonjának saját kezűleg üzembe helyezése
A **üzembe helyezés az Azure** gomb nagyszerű, de lehetővé teszi az azuredeploy.json erőforrás csoport sablon üzembe helyezése, csak akkor, ha már rendelkezik leküldött azuredeploy.json GitHub. Az Azure .NET SDK-t is biztosít az eszközök üzembe helyezését bármely JSON-sablonfájlt közvetlenül a helyi gépről. Ehhez kövesse az alábbi lépéseket:

1. A Visual Studióban kattintson a **File (Fájl)** > **New (Új)** > **Project (Projekt)** parancsra.
2. Kattintson a **Visual C#** > **felhőalapú** > **Azure-erőforráscsoport**, majd kattintson a **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. A **Azure-sablon kiválasztása**válassza **üres sablon** kattintson **OK**.
4. Húzza az azuredeploy.json a **sablon** mappában található az új projektet.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. A Megoldáskezelőben nyissa meg a másolt azuredeploy.json.
6. Csak a bemutató Szemléltetésül adjunk hozzá néhány standard Application Insights-erőforrást a JSON-fájl kattintva **erőforrás hozzáadása**. Ha csak szeretné használni a JSON-fájl telepítése, ugorjon a telepítés lépéseit.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Válassza ki **Application Insights Pro Web Apps**, majd győződjön meg arról, hogy be van jelölve egy meglévő App Service-csomagot és alkalmazást, és kattintson **Hozzáadás**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Most már fogja tudni több új erőforrás, erőforrások és a működés megtekintéséhez vagy az App Service-csomagot, vagy az alkalmazás függőségekkel rendelkeznek. Ezeket az erőforrásokat a meglévő definíció szerint nincsenek engedélyezve, és módosíthatja, hogy fog.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Kattintson a JSON-vázlat **appInsights automatikus skálázási** jelölje ki a JSON-kódot. Ez az az App Service-csomag a skálázási beállítást.
9. A kiemelt JSON-kódot, keresse meg a `location` és `enabled` tulajdonságait, és állítsa be őket alább látható módon.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Kattintson a JSON-vázlat **CPUHigh appInsights** jelölje ki a JSON-kódot. Ez a riasztás.
11. Keresse meg a `location` és `isEnabled` tulajdonságait, és állítsa be őket alább látható módon. Használja ugyanazt a többi három riasztások (lila hagymák).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Most már készen áll az üzembe helyezése. Kattintson a jobb gombbal a projektre, és válassza ki **telepítés** > **új üzembe helyezési**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Ha ezt még nem tette meg, jelentkezzen be Azure-fiókjába.
14. Válasszon ki egy meglévő erőforráscsoportot az előfizetésében, vagy hozzon létre egy új egyetlen, select **azuredeploy.json**, és kattintson a **paraméterek szerkesztése**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Most már fogja szerkesztheti a már jól néz kis táblában a sablon fájlban meghatározott összes paramétert. Alapértelmezett értékeket meghatározó paraméterek már rendelkezik alapértelmezett értékekre, és a legördülő menük jelennek paraméterei, melyek meghatározzák az engedélyezett értékek listáját.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Adja meg az üres paraméterek, és használja a [ToDoApp GitHub-tárház címet](https://github.com/azure-appservice-samples/ToDoApp.git) a **adattár URL-címe**. Kattintson a **mentése**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatikus méretezési funkciója érhető el **Standard** szint vagy magasabb szintű és a terv szintű riasztások felajánlott szolgáltatások **alapszintű** díjcsomag vagy újabb verziója szükséges, hogy kell beállítani a **termékváltozat** paraméter a **Standard** vagy **prémium** annak érdekében, hogy tekintse meg az új App Insights erőforrások könnyű fel.
    > 
    > 
16. Kattintson a **üzembe helyezése**. Ha a kiválasztott **jelszavak mentése egyszerű**, a jelszó menti a rendszer a paraméterfájlban **szövegként**. Ellenkező esetben kéri, adja a database jelszavát a telepítési folyamat során.

Készen is van. Most csatlakoznia kell a [az Azure Portal](https://portal.azure.com/) és a [Azure erőforrás-kezelő](https://resources.azure.com) eszközt az új riasztások és automatikus méretezési beállítások a JSON az alkalmazás üzembe helyezése.

A jelen szakaszban ismertetett lépések főként elvégezte a következőket:

1. A sablonfájl előkészítése
2. A sablonfájl betarthatja paraméterfájl létrehozása
3. A sablonfájl alkalmazásparaméter-fájlt az üzembe helyezett

Az utolsó lépés egyszerű végzi el egy PowerShell-parancsmagot. Mi a Visual Studio az azt az alkalmazás telepítésekor tette megtekintéséhez nyissa meg a Scripts\Deploy-AzureResourceGroup.ps1. A kódot van, de most elindítok kiemelheti a profiljával kapcsolatos kódot kell üzembe helyeznie a sablonfájlt paraméter-fájllal.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Az utolsó parancsmag `New-AzureResourceGroup`, amelyet az végrehajtja a műveletet. Mindez bizonyítania kell, hogy, eszközök segítségével, azaz viszonylag egyszerű, kiszámítható módon a felhőalapú alkalmazások üzembe helyezése. Minden alkalommal, amikor futtatja a parancsmagot ugyanazt a sablont a paraméter ugyanebben a fájlban, meg fogjuk elérhető ugyanaz az eredmény.

## <a name="summary"></a>Összegzés
Fejlesztés és üzemeltetés ismételhetőség és kiszámíthatóságot kulcsok mikroszolgáltatásokból álló, nagy méretű alkalmazás sikeres környezethez. Ebben az oktatóanyagban telepítette az Azure Resource Manager-sablon használatával egyetlen erőforráscsoportot, az Azure-ban két-mikroszolgáltatás-alkalmazás. Remélhetőleg Ez a példa adott a Tudásbázis van szüksége ahhoz, hogy indítsa el az Azure-ban az alkalmazás átalakítása sablon üzembe helyezése és is kiszámítható módon telepítheti. 

<a name="resources"></a>

## <a name="more-resources"></a>További erőforrások
* [Az Azure Resource Manager-sablon nyelve](../azure-resource-manager/resource-group-authoring-templates.md)
* [Az Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md)
* [Az Azure Resource Manager-Sablonfüggvények](../azure-resource-manager/resource-group-template-functions.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](../azure-resource-manager/resource-group-template-deploy.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Erőforrások üzemelő példányainak hibaelhárítása az Azure-ban](../azure-resource-manager/resource-manager-common-deployment-errors.md)

## <a name="next-steps"></a>További lépések

További információ a JSON-szintaxist és a Tulajdonságok erőforrástípusok telepített ebben a cikkben, lásd:

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)