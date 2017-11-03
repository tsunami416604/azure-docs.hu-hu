---
title: "Kiosztását és telepítését mikroszolgáltatások kiszámítható módon tudja az Azure-ban"
description: "Útmutató az Azure App Service egy egységként és kiszámítható módon JSON erőforrás csoport sablonok és a PowerShell-parancsprogramok használatával mikroszolgáltatások álló alkalmazás központi telepítése."
services: app-service
documentationcenter: 
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
ms.openlocfilehash: 3719e037f1564411a8f94d1ca962ba1ef6b5d435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Kiosztását és telepítését mikroszolgáltatások kiszámítható módon tudja az Azure-ban
Ez az oktatóanyag bemutatja, hogyan kiosztását és telepítését, az alkalmazás összetevői [mikroszolgáltatások](https://en.wikipedia.org/wiki/Microservices) a [Azure App Service](/services/app-service/) egyetlen egységként és kiszámítható módon JSON erőforrás csoport sablonok és a PowerShell-parancsfájlok használatával. 

Kiépítés és magas szintű alkalmazásokat, amelyek álló magas le mikroszolgáltatások, ismételhetőség és kiszámíthatóságot fontosságúak sikeres. [Az Azure App Service](/services/app-service/) lehetővé teszi a webalkalmazások, mobilalkalmazások, az API apps és a logic apps tartalmazó mikroszolgáltatások létrehozására. [Az Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) lehetővé teszi a mikroszolgáltatások kezelése egységként, például az adatbázis erőforrás-függőségek együtt, és a forrás-vezérlési beállításokkal. Most ilyen alkalmazás JSON sablonok és egyszerű PowerShell-parancsfájlok használatával is telepítheti. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>Mit fog
Az oktatóanyagban a, amely tartalmazza az alkalmazás központi telepítése:

* Két webes alkalmazások (pl. két mikroszolgáltatások)
* A háttérrendszer SQL-adatbázis
* Alkalmazásbeállítások kapcsolati karakterláncok és adatforrás-vezérlő
* Az Application insights, riasztások, automatikus skálázás beállításai

## <a name="tools-you-will-use"></a>Eszközök fogja használni
Ebben az oktatóanyagban a következő eszközöket fogja használni. Mivel ez nem az eszközök átfogó leírást, fogom igazodnak-e a végpont forgatókönyv, és csak Önnek egy rövid bevezető minden, és hol találhatók további információk. 

### <a name="azure-resource-manager-templates-json"></a>Az Azure Resource Manager-sablonokat (JSON)
Minden alkalommal, amikor a webalkalmazás létrehozása az Azure App Service-ben, például Azure Resource Manager használ a JSON-sablon létrehozásához a teljes erőforráscsoport az összetevő-erőforrások. Egy összetett sablon, a [Azure piactér](/marketplace) hasonlóan a [méretezhető WordPress](/marketplace/partners/wordpress/scalablewordpress/) app lehetnek a MySQL-adatbázis, storage-fiókok, az App Service-csomag, a webes alkalmazás maga, a riasztási szabályok, beállítások, automatikus skálázási beállításokat, és több, és ezek a sablonok Powershellen keresztül rendelkezésre álló. További információkért töltse le és használja ezeket a sablonokat: [az Azure PowerShell használata Azure Resource Managerrel](../powershell-azure-resource-manager.md).

Az Azure Resource Manager-sablonok további információkért lásd: [Azure Resource Manager sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Az Azure SDK 2.6 Visual Studio
A legújabb SDK tartalmazza a Resource Manager sablon támogatja a JSON-szerkesztőben fejlesztései. Ennek használatával gyorsan hozzon létre egy erőforrás-sablon a teljesen, vagy nyisson meg egy meglévő JSON-sablon (például egy gyűjtemény letöltött sablon) módosításra, a paraméterek fájl feltöltéséhez, és az erőforráscsoport közvetlenül egy Azure-erőforráscsoport megoldás még akkor is telepíteni.

További információkért lásd: [Azure SDK 2.6 a Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Az Azure PowerShell 0.8.0 vagy újabb verzió
0.8.0 verziójától kezdve az Azure PowerShell telepítése az Azure Resource Manager modul mellett az Azure-moduljának tartalmazza. Ez a modul parancsfájllal történő lehetővé teszi az erőforráscsoportok telepítését.

További információkért lásd: [az Azure PowerShell használata Azure Resource Managerrel](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Ez [preview eszköz](https://resources.azure.com) lehetővé teszi a JSON-definíciókat az előfizetését és az egyes erőforrások erőforráscsoportok vizsgálatát. Az eszköz szerkessze a JSON-definíciók egy erőforrást, az egész hierarchiát erőforrások törlése és létrehozni az új erőforrásokat.  Ez az eszköz könnyen elérhető információkat nagyon hasznos sablon létrehozásához, mert jeleníti meg, milyen tulajdonságok, meg kell adnia egy adott típusú erőforrás, a helyes az értékük, stb. Az erőforráscsoport a is létrehozhat a [Azure Portal](https://portal.azure.com/), majd vizsgálja meg a JSON-definíciói segítséget nyújtanak az erőforráscsoport templatize explorer eszközben.

### <a name="deploy-to-azure-button"></a>Telepítse az Azure gomb
Ha GitHub a verziókövetési rendszerrel használja, akkor lehet helyezni egy [az Azure gomb telepítéséhez](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) azokat a fontos. MD, amely lehetővé teszi a kulcsrakész központi telepítés felhasználói felület az Azure-bA. Ehhez minden egyszerű webalkalmazás, amíg egy teljes erőforráscsoport telepítése egy azuredeploy.json fájlt az adattár gyökérkönyvtárában elhelyezett engedélyezheti bővítheti. A JSON-fájl, amely tartalmazza az erőforrás-csoport sablon, használják az Azure gomb telepítéséhez az erőforráscsoport létrehozásához. Egy vonatkozó példáért lásd: a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) mintát, amely ebben az oktatóanyagban használhatja.

## <a name="get-the-sample-resource-group-template"></a>A minta erőforrás csoport sablon
Most folytassuk a jobb oldali rá.

1. Keresse meg a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service-mintát.
2. A readme.md, kattintson **az Azure telepítéséhez**.
3. Meg van-e a [telepítése-a-azure](https://deploy.azure.com) helyről, és kéri a felhasználótól, üzembe helyezéshez megadott paraméterek. Figyelje meg, hogy a mezők meg a tárház nevét és az egyes véletlenszerű karakterláncokat feltöltött. Ha azt szeretné, de a csak dolog, meg kell adni az SQL Server-rendszergazdai bejelentkezés és a jelszót, majd kattintson a mezők módosíthatja **következő**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Ezután kattintson **telepítés** a telepítés megkezdéséhez. Ha a folyamat befejezését fut, kattintson a http://todoapp*XXXX*. azurewebsites.net hivatkozás a telepített alkalmazás tallózással. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   A felhasználói felületen szeretné kissé lassú lehet, amikor először megnyitja a, mert az alkalmazások csak indítása, de meggyőzni a felhasználót saját kezűleg, hogy a rendszer egy teljesen működőképes alkalmazást.
5. A telepítés lapon kattintson a **kezelése** hivatkozásra kattintva megtekintheti az új alkalmazás az Azure portálon.
6. Az a **Essentials** legördülő menüben kattintson az erőforrás-csoport hivatkozásra. Ügyeljen arra is, hogy a webalkalmazás már kapcsolódik a GitHub-tárházban alatt **külső projekt**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Az erőforráscsoport panelen jegyezze fel, hogy nincsenek már két webes alkalmazások és az erőforráscsoport egy SQL-adatbázis.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Minden, ami az imént látott néhány rövid percben egy teljes körűen rendszerbe állított két-mikroszolgáltatási alkalmazást, az összes összetevőt, függőségek, beállítások, adatbázis, és folyamatos közzétételét, a beállítási egy automatizált vezénylési az Azure Resource Manager. Összes Ez két dolgot végezhető el:

* Az Azure gomb telepítéséhez
* a tárház gyökérkönyvtárában azuredeploy.JSON

Ez az alkalmazás telepítése több tíz, száz vagy ezer hányszor és minden alkalommal pontos megegyező konfigurációval rendelkezik. Az ismételhetőség és a hátránya kiszámíthatóságot lehetővé teszi nagy méretű alkalmazások központi telepítése az egyszerű és az vetett bizalmat.

## <a name="examine-or-edit-azuredeployjson"></a>Vizsgálja meg (vagy szerkesztése) AZUREDEPLOY. JSON-BAN
Most nézzük hogyan a GitHub-tárházban be lett állítva. Az Azure .NET SDK-ban a JSON-szerkesztő használ, ha még nem telepítette a [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/), most megtenni.

1. Klónozott a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) tárházat a kedvenc git eszköz használatával. Az alábbi képernyőképen I vagyok ezzel a csapat Explorerben a Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Az adattár gyökérkönyvtárában nyissa meg a Visual Studio azuredeploy.json. Ha nem látható a JSON-vázlat ablak, Azure .NET SDK-t telepíteni szeretné.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nem fogom JSON formátumban minden részletét leírására, de a [több erőforrások](#resources) szakasz az erőforrás csoport sablon nyelv tanulási mutató hivatkozásokat tartalmaz. Itt csak kattintok mutatjuk be, melyek segíthetnek az érdekes szolgáltatások érdekében, hogy saját egyéni sablon az alkalmazások telepítésének első lépései.

### <a name="parameters"></a>Paraméterek
Tekintse meg a Paraméterek szakaszban, hogy ezek a paraméterek a legtöbb Mi a **az Azure telepítéséhez** gomb kéri a felhasználótól. A hely mögött a **az Azure telepítéséhez** gomb tölti fel a bemeneti felhasználói felület azuredeploy.json definiált paraméterek használatával. Ezek a paraméterek kifejezés egyaránt használatban az erőforrás-definíciókban, például erőforrásnevek, tulajdonságértékek stb.

### <a name="resources"></a>Erőforrások
Az erőforrások csomópontjában tekintheti meg, hogy 4 legfelső szintű erőforrások vannak megadva, például egy SQL Server-példányt, az App Service-csomag és két webalkalmazások. 

#### <a name="app-service-plan"></a>App Service-csomag
Kezdjük a JSON egyszerű gyökér szintű az erőforráshoz. A JSON-vázlat, kattintson az App Service-csomag nevű **[hostingPlanName]** jelölje ki a megfelelő JSON-kódot. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Vegye figyelembe, hogy a `type` elem meghatározza azt a karakterláncot, az App Service-csomagot (meghívta a kiszolgálófarm egy hosszú, hosszú idő telt el), és más elemek és tulajdonságok kitölti a JSON-fájlban definiált paraméterek használatával, és ehhez az erőforráshoz nincsenek beágyazott erőforrásai.

> [!NOTE]
> Azt is fontos megjegyezni, hogy értékének `apiVersion` közli az Azure melyik verzióját a REST API-t használja a JSON az erőforrás-definícióban, és hatással lehet a módját a erőforrás kell formázni belül a `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Ezután kattintson az SQL Server erőforrás-nevű **SQLServer** a JSON-Vázlat.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

A kiemelt JSON-kódot kapcsolatos megjegyzések:

* Paraméterek használata azt biztosítja, hogy a létrehozott erőforrások neve, konfigurálható úgy, hogy egymással konzisztenssé teszi őket.
* Az SQL Server erőforrás két beágyazott erőforrások, akkor mindegyik rendelkezik-e meg más értéket `type`.
* A beágyazott erőforrások belül `“resources”: […]`, ahol definiálva vannak az adatbázis és a tűzfalszabályokat, hogy egy `dependsOn` elem, amely meghatározza az erőforrás-azonosítója a gyökér szintű SQLServer erőforrás. Ez alapján Azure Resource Manager "ehhez az erőforráshoz, amely már léteznie kell, más erőforrás; létrehozása előtt Ha más erőforrás van definiálva a sablonban, majd hozza létre, hogy egy először".
  
  > [!NOTE]
  > Részletes információk használatáról a `resourceId()` működik, lásd: [Azure Resource Manager Sablonfüggvényei](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* A hatását, hogy a `dependsOn` elem, hogy Azure Resource Manager tudhatja, mely erőforrásokat párhuzamosan lehet létrehozni és erőforrások egymás után létre kell hozni. 

#### <a name="web-app"></a>Webalkalmazás
Most tegyük lépjen tovább a tényleges webes alkalmazásokat, amelyek bonyolultabb. Kattintson a [variables('apiSiteName')] webalkalmazás a JSON-vázlat jelölje ki a JSON-kódot. Láthatja, hogy dolgot első sokkal ennél is érdekesebb megoldást. Erre a célra I fog kommunikálni egyenként szolgáltatásairól:

##### <a name="root-resource"></a>Legfelső szintű erőforrás
A webalkalmazás két különböző erőforrások függ. Ez azt jelenti, hogy az Azure Resource Manager a webalkalmazás létrehozása, csak az App Service-csomag, mind az SQL Server-példány létrehozása után.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Alkalmazásbeállítások
Az alkalmazás beállításait is meg van határozva egy beágyazott erőforrásként.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

Az a `properties` eleme `config/appsettings`, két Alkalmazásbeállítások formátumban van `“<name>” : “<value>”`.

* `PROJECT`van egy [KUDU beállítás](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , amely közli az Azure-telepítés mely projektek több projektet a Visual Studio-megoldásban. I bemutatja, hogyan később verziókezelő van konfigurálva, de mivel a ToDoApp kód több projektet a Visual Studio-megoldásban, igazolnia kell-e ezt a beállítást.
* `clientUrl`rendszer egyszerűen egy beállítás, amely az alkalmazáskódban alkalmazás használ.

##### <a name="connection-strings"></a>Kapcsolati karakterláncok
A kapcsolati karakterláncok egy beágyazott erőforrást is vannak meghatározva.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

Az a `properties` eleme `config/connectionstrings`, minden egyes kapcsolati karakterláncot is nevezünk név: érték pár, meghatározott formátumban `“<name>” : {“value”: “…”, “type”: “…”}`. Az a `type` elem, a lehetséges értékek: `MySql`, `SQLServer`, `SQLAzure`, és `Custom`.

> [!TIP]
> A karakterláncot kapcsolattípusokat végleges listájának megtekintéséhez futtassa a következő parancsot az Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>A verziókövetési rendszerrel
A verziókövetési beállítások is meg van határozva egy beágyazott erőforrásként. Az Azure Resource Manager használja ezt az erőforrást folyamatos közzétételének konfigurálása (szerint tekintse meg `IsManualIntegration` később) és is indítsa a telepítés automatikusan a JSON-fájl feldolgozása során az alkalmazás kódját.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl`és `branch` közérthető intuitív kell lennie, és a Git-tárház és a közzététel az ág nevét kell mutatnia. Ebben az esetben ezek határozzák meg a bemeneti paraméterek. 

Vegye figyelembe, hogy a `dependsOn` elem, hogy a webes alkalmazás-erőforrást, mellett `sourcecontrols/web` is függ, `config/appsettings` és `config/connectionstrings`. Ennek az az oka után `sourcecontrols/web` van konfigurálva, az Azure telepítési folyamat automatikusan megpróbálja telepíteni, elkészítéséhez és indítsa el az alkalmazás kódját. Ezért a függőség beszúrása segítségével győződjön meg arról, hogy az alkalmazás fér hozzá a szükséges alkalmazás beállításait és a kapcsolati karakterláncokat az alkalmazás kódjának futtatása előtt. 

> [!NOTE]
> Azt is fontos megjegyezni, hogy `IsManualIntegration` értéke `true`. Ez a tulajdonság oka szükség ebben az oktatóanyagban ténylegesen nem tulajdonosa a GitHub-tárházban, és így nem ténylegesen engedélyt ad a folyamatos közzétételének konfigurálása Azure [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (pl. leküldéses Automatikus adattár a frissítések az Azure-bA). Használhatja az alapértelmezett érték `false` csak akkor, ha konfigurálta a tulajdonos GitHub-felhasználó hitelesítő adatait a megadott tárház a [Azure-portálon](https://portal.azure.com/) előtt. Ez azt jelenti Ha meg van adva verziókövetésének GitHub vagy BitBucket szereplő bármely alkalmazásra a [Azure Portal](https://portal.azure.com/) korábban, a felhasználó hitelesítő adatait, akkor Azure fogja a hitelesítő adatok megjegyzése és használata a jövőben telepít a GitHub vagy BitBucket bármely alkalmazás. Azonban ha ezt még nem végezte, telepítés JSON-sablon meghiúsul, ha Azure Resource Manager megpróbálja a webes alkalmazás verziókövetési beállítások konfigurálása, mert azt nem lehet bejelentkezni a GitHub vagy bitbucket szolgáltatásokkal a tárház tulajdonosát hitelesítő adatokkal.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>A JSON-sablon üzembe helyezett erőforrás csoporttal összehasonlítása
Itt, lépjen a a webes alkalmazás összes paneleken keresztül a [Azure Portal](https://portal.azure.com/), azonban egy másik eszköz, amely nincs csak, akkor hasznos, ha több. Lépjen a [Azure erőforrás-kezelő](https://resources.azure.com) preview eszközt, amely lehetővé teszi az erőforráscsoportok JSON-ábrázolását előfizetése, a ténylegesen az Azure-háttérrendszernek léteznek. Megtekintheti, milyen az Azure-ban az erőforráscsoport JSON hierarchia megfelel-e a hierarchiában lévő a létrehozásához használt sablonfájl.

Például ha ugrani a [Azure erőforrás-kezelő](https://resources.azure.com) eszköz, és bontsa ki a Explorer csomópontokat, láthatom valahol az erőforráscsoport és a megfelelő erőforrástípusok szerint gyűjtött gyökér szintű erőforrások.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Ha a webes alkalmazás lebontva, webes alkalmazás konfigurációs adatait hasonló látni kell az alábbi képernyőfelvétel:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Ebben az esetben a beágyazott erőforrások kell rendelkeznie a nagyon hasonlít a JSON-sablon fájl a hierarchia, és az alkalmazásbeállítások, kapcsolati karakterláncok, stb., megfelelően a JSON ablaktáblán megjelennek kell megjelennie. Itt beállítások hiányában esetleg utalhatnak a JSON-fájl, és a JSON-sablon fájl hibaelhárításához nyújt segítséget.

## <a name="deploy-the-resource-group-template-yourself"></a>Az erőforrás-csoport sablon saját kezűleg üzembe helyezése
A **az Azure telepítéséhez** gomb nagy, de lehetővé teszi az erőforrás-csoport sablon azuredeploy.json telepítése csak akkor, ha már leküldött azuredeploy.json GitHub. Az Azure .NET SDK olyan bármely JSON-sablon fájl közvetlenül a helyi számítógépről telepíthető eszközöket is biztosít. Ehhez kövesse az alábbi lépéseket:

1. A Visual Studióban kattintson **fájl** > **új** > **projekt**.
2. Kattintson a **Visual C#** > **felhő** > **Azure erőforráscsoport**, majd kattintson a **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. A **Azure-sablon kiválasztása**, jelölje be **üres sablont** kattintson **OK**.
4. Húzza az azuredeploy.json a **sablon** az új projekt mappájából.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. A Solution Explorerben nyissa meg a másolt azuredeploy.json.
6. Csak az alábbiakban bemutatjuk, adjuk hozzá néhány szokásos Application Insights-erőforrást a JSON-fájlt, hogy kattintva **erőforrás hozzáadása**. Ha csak szeretné használni a JSON-fájl központi telepítése, ugorjon a telepítés lépéseit.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Válassza ki **Application Insights Web Apps**, majd ellenőrizze, hogy egy meglévő App Service csomag és a webes alkalmazás van kiválasztva, és kattintson a **Hozzáadás**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Most már képes lesz számos új forrásanyagokban talál, attól függően, hogy az erőforrás, és hogyan kezeli, vagy az App Service-csomagot, vagy a web app függőségekkel rendelkeznek. Ezeket az erőforrásokat a meglévő definíciót szerint nincsenek engedélyezve, és módosíthatja, hogy fog.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Kattintson a JSON-vázlat **appinsights által biztosított automatikus skálázás** jelölje ki a JSON-kódot. Ez az App Service-csomag méretezési beállítása.
9. A kiemelt JSON-kódot, keresse meg a `location` és `enabled` tulajdonságok és -e meg őket alább látható módon.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Kattintson a JSON-vázlat **CPUHigh appinsights által biztosított** jelölje ki a JSON-kódot. Ez az értesítés.
11. Keresse meg a `location` és `isEnabled` tulajdonságok és -e meg őket alább látható módon. Tegye meg ugyanezt a másik három riasztások (lila hagymák).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Most már készen áll központi telepítéséhez. Kattintson jobb gombbal a projektre, és válassza ki **telepítés** > **új központi telepítési**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Ha még nem tette meg, jelentkezzen be az Azure-fiókjával.
14. Jelöljön ki egy meglévő erőforráscsoportot az előfizetés, vagy hozzon létre egy új egy, jelöljön ki **azuredeploy.json**, és kattintson a **paraméterek szerkesztése**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Most már képes lesz a sablonfájl töltött táblázatban definiált összes paraméterek szerkesztése. Alapértelmezett értékeket meghatározó paraméterek már rendelkezik az alapértelmezett értékekre, és a paraméterek, melyek meghatározzák az engedélyezett értékek listájának jelennek meg a legördülő lista.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Töltse ki a üres paramétereket, és a [ToDoApp GitHub-tárház címe](https://github.com/azure-appservice-samples/ToDoApp.git) a **repoUrl**. Kattintson a **mentése**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Automatikus skálázás egy olyan funkció érhető el **szabványos** réteg vagy magasabb és a terv szintű riasztások a nyújtott szolgáltatások **alapvető** szintjüket, vagy újabb rendszerre, akkor be kell állítania a **sku** paramétert **szabványos** vagy **prémium** láthatók a új App Insights erőforrások könnyű fel.
    > 
    > 
16. Kattintson a **telepítése**. Ha a kiválasztott **menthetik a jelszavakat**, a jelszót a rendszer menti a paraméter fájlban **egyszerű szöveges**. Ellenkező esetben kéri a jelszót a felhasználótól, a telepítési folyamat során.

Készen is van. Most csatlakoznia kell a [Azure Portal](https://portal.azure.com/) és a [Azure erőforrás-kezelő](https://resources.azure.com) eszközt, amely az új riasztások és a JSON új automatikus skálázási beállításokat központilag telepített alkalmazás.

A jelen szakaszban szereplő lépéseket általában úgy hajthatja végre a következő:

1. A sablonfájl előkészítése
2. A paraméterfájl használata a sablonfájl létrehozása
3. A sablonfájl paraméter fájllal telepített

Az utolsó lépés egyszerű PowerShell parancsmag végezhető el. Mi a Visual Studio az az alkalmazás telepítésekor nyerte megtekintéséhez nyissa meg a Scripts\Deploy-AzureResourceGroup.ps1. A kódot van, de csak fogom jelölje ki a megfelelő kódot kell telepítenie a sablonfájl paraméter fájllal.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Az utolsó parancsmag `New-AzureResourceGroup`, van, amely végrehajtja a műveletet. Ez bizonyítania kell Önnek, hogy, tooling segítségével, a rendszer viszonylag egyszerű kiszámítható módon tudja a felhő alkalmazás központi telepítése. Minden alkalommal, amikor az ugyanazon paraméter fájllal ugyanazt a sablont a futtatta a parancsmagot, ugyanazt az eredményt kapja fog.

## <a name="summary"></a>Összefoglalás
DevOps ismételhetőség és kiszámíthatóságot a sikeres telepítést mikroszolgáltatások álló nagy méretű kérelem kulccsal. Ebben az oktatóanyagban az Azure Resource Manager sablon használatával egyetlen erőforráscsoportként működnek az Azure két-mikroszolgáltatási alkalmazást telepített. Remélhetőleg az adott ahhoz, hogy indítsa el az alkalmazás az Azure-ban konvertálása a sablonba és kioszthatja és kiszámítható módon tudja központilag telepítenie kell a Tudásbázis. 

<a name="resources"></a>

## <a name="more-resources"></a>További erőforrások
* [Az Azure Resource Manager sablon nyelve](../azure-resource-manager/resource-group-authoring-templates.md)
* [Az Azure Resource Manager sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md)
* [Az Azure Resource Manager Sablonfüggvényei](../azure-resource-manager/resource-group-template-functions.md)
* [Alkalmazás üzembe helyezése az Azure Resource Manager-sablon](../azure-resource-manager/resource-group-template-deploy.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Az Azure erőforráscsoport-telepítések hibaelhárítása](../azure-resource-manager/resource-manager-common-deployment-errors.md)

