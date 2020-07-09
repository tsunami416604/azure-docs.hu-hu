---
title: Webjobs-feladatok fejlesztése és üzembe helyezése a VS használatával
description: Megtudhatja, hogyan fejlesztheti Azure WebJobs a Visual Studióban, és hogyan helyezheti üzembe azokat Azure App Service, beleértve az ütemezett feladatok létrehozását is.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 971ab39ddf4b383f92f0cd81e5258ad357e76e99
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857074"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>WebJobs-feladatok üzembe helyezése Visual Studióval – Azure App Service

Ez a cikk azt ismerteti, hogyan lehet a Visual Studióval üzembe helyezni egy Console Application-projektet egy webalkalmazásban [app Service](overview.md) [Azure-webjobs](https://go.microsoft.com/fwlink/?LinkId=390226). További információ a webjobs [Azure Portal](https://portal.azure.com)használatával történő üzembe helyezéséről: [háttérben futó feladatok futtatása webjobs](webjobs-create.md)-feladatokkal.

Több webfeladat is közzétehető egyetlen webalkalmazásban. Győződjön meg arról, hogy a webalkalmazás minden Webjobs egyedi névvel rendelkezik.

Az [Azure WEBJOBS SDK](webjobs-sdk-how-to.md) 3. x verziója lehetővé teszi, hogy a .net Core-alkalmazásként vagy a .NET-keretrendszer alkalmazásaiként futó webjobs-feladatokat fejlesszen, míg a 2. x verzió csak a .NET-keretrendszert támogatja. A webjobs-projekt üzembe helyezésének módja különbözik a .NET Core-projektek és a .NET-keretrendszer esetében is.

## <a name="webjobs-as-net-core-console-apps"></a>Webjobs-feladatok .NET Core Console-alkalmazásként

A webjobs 3. x verziójának használatakor a webjobs-t .NET Core Console-alkalmazásként hozhatja létre és teheti közzé. A .NET Core Console-alkalmazások Azure-beli Webjobs való létrehozásával és közzétételével kapcsolatos részletes utasításokért lásd: Ismerkedés [a Azure WEBJOBS SDK-val eseményvezérelt háttér-feldolgozáshoz](webjobs-sdk-get-started.md).

> [!NOTE]
> A .NET Core webjobs-feladatok nem csatolhatók webes projektekhez. Ha a Webjobs egy webalkalmazással kell központilag telepítenie, akkor [a webjobs-et .NET Framework Console-alkalmazásként kell létrehoznia](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Üzembe helyezés az Azure App Service-ben

A .NET Core-Webjobs a Visual studióból való App Service való közzététele ugyanazokat az eszközöket használja, mint a ASP.NET Core alkalmazás közzététele.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Webjobs-típusok

Alapértelmezés szerint a .NET Core-konzolon közzétett Webjobs csak aktiváláskor vagy igény szerint fut. Azt is megteheti, hogy a projektet [ütemezett](#scheduled-execution) vagy folyamatos futtatásra frissíti.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Ütemezett végrehajtás

Ha egy .NET Core Console-alkalmazást tesz közzé az Azure-ban, a rendszer új *beállításokat* ad hozzá a projekthez. Ezzel a fájllal állíthatja be a Webjobs végrehajtási ütemtervét. További információ: [aktivált Webjobs ütemezése](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Folyamatos végrehajtás

A Visual Studióval megváltoztathatja a Webjobs, hogy folyamatosan fusson, amikor a mindig be van kapcsolva az Azure-ban.

1. Ha még nem tette meg, [tegye közzé a projektet az Azure](#deploy-to-azure-app-service)-ban.

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel** lapon válassza a **Beállítások**lehetőséget. 

1. A **Profilbeállítások** párbeszédpanelen válassza a **folyamatos** lehetőséget a **webjobs típusnál**, majd kattintson a **Mentés**gombra.

    ![Webjobs közzétételi Beállítások párbeszédpanelje](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Válassza a **Közzététel** lehetőséget a webjobs újbóli közzétételéhez a frissített beállításokkal.

## <a name="webjobs-as-net-framework-console-apps"></a>Webjobs-feladatok .NET-keretrendszerbeli konzol alkalmazásaiként  

Ha a Visual Studio üzembe helyez egy webjobs-kompatibilis .NET-keretrendszerbeli alkalmazás-projektet, a futásidejű fájlokat másolja a webalkalmazás megfelelő mappájába (*App_Data/Jobs/Continuous* a folyamatos webjobs-feladatok számára, és *App_Data/Jobs/Triggered* az ütemezett vagy igény szerinti webjobs-feladatok esetében).

Egy webjobs-kompatibilis projekthez a következő elemek vannak hozzáadva:

* A [Microsoft. Web. webjobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-csomag.
* A központi telepítés és a Feladatütemező beállításait tartalmazó fájl [webjob-publish-settings.js](#publishsettings) . 

![Ábra, amely bemutatja, hogy mit adnak hozzá egy Webjobs az üzemelő példányok engedélyezéséhez](./media/webjobs-dotnet-deploy-vs/convert.png)

Ezeket az elemeket hozzáadhatja egy meglévő Console Application-projekthez, vagy használhat egy sablont egy új webjobs-kompatibilis Console Application-projekt létrehozásához. 

Saját maga is üzembe helyezhet egy projektet Webjobs, vagy összekapcsolhatja azt egy webes projekttel, hogy az automatikusan üzembe kerüljön a webes projekt telepítésekor. A projektek összekapcsolásához a Visual Studio tartalmazza a webjobs-kompatibilis projekt nevét a webes projektben lévő fájl [webjobs-list.js](#webjobslist) .

![A webes projekthez kapcsolódó Webjobs bemutató diagram](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Előfeltételek

Ha a Visual Studio 2015-at használja, telepítse a [.net-hez készült Azure SDK-t (Visual studio 2015)](https://azure.microsoft.com/downloads/).

Ha a Visual Studio 2017-et használja, telepítse az [Azure-fejlesztési](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)számítási feladatot.

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>Webjobs-telepítés engedélyezése meglévő konzolos alkalmazás projekthez

Erre két lehetősége van:

* [Automatikus központi telepítés engedélyezése webes projekttel](#convertlink).

  Konfiguráljon egy meglévő Console Application-projektet úgy, hogy az automatikusan Webjobs, amikor egy webes projektet telepít központilag. Akkor használja ezt a beállítást, ha a Webjobs ugyanabban a webalkalmazásban szeretné futtatni, amelyben a kapcsolódó webalkalmazást futtatja.

* [Az üzembe helyezést webes projekt nélkül is engedélyezheti](#convertnolink).

  Egy meglévő konzolos alkalmazás-projekt konfigurálása a Webjobs való üzembe helyezéshez, a webes projekthez való hivatkozás nélkül. Akkor használja ezt a beállítást, ha saját maga szeretné futtatni a Webjobs egy webalkalmazásban, és a webalkalmazásban nem fut a webes alkalmazás. Ezt úgy teheti meg, hogy a webalkalmazás-erőforrásaitól függetlenül szeretné a Webjobs-erőforrásokat méretezni.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Automatikus webjobs-telepítés engedélyezése webes projekttel

1. Kattintson a jobb gombbal a **megoldáskezelő**található webes projektre, majd **Add**kattintson a  >  **meglévő projekt hozzáadása Azure webjobs**elemre.
   
    ![Meglévő projekt Azure Webjobs](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Megjelenik az [Azure-Webjobs hozzáadása](#configure) párbeszédpanel.
2. A **projekt neve** legördülő listában válassza ki a webjobs hozzáadni kívánt konzol alkalmazás-projektet.
   
    ![A projekt kiválasztása az Azure Webjobs hozzáadása párbeszédpanelen](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Fejezze be az [Azure-Webjobs hozzáadása](#configure) párbeszédpanelt, majd kattintson **az OK**gombra. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>Webjobs-telepítés engedélyezése webes projekt nélkül
1. Kattintson a jobb gombbal a Console Application-projektre **megoldáskezelő**, majd kattintson a **Közzététel Azure-webjobs...** lehetőségre. 
   
    ![Közzététel Azure-Webjobs](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Megjelenik az [Azure-Webjobs hozzáadása](#configure) párbeszédpanel, ahol a projekt **neve** mezőben ki van választva.
2. Fejezze be az [Azure-Webjobs hozzáadása](#configure) párbeszédpanelt, majd kattintson **az OK**gombra.
   
   Megjelenik a **webes közzététel** varázsló.  Ha nem szeretne azonnal közzétenni, a varázsló bezárásával. A megadott beállításokat a rendszer a [projekt központi telepítésekor](#deploy)menti.

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Új webjobs-kompatibilis projekt létrehozása
Új webjobs-kompatibilis projekt létrehozásához használhatja a Console Application Project sablont, és engedélyezheti a webjobs-telepítést [az előző szakaszban](#convert)leírtak szerint. Másik lehetőségként használhatja a webjobs új-projekt sablont:

* [A webjobs új-Project sablonjának használata egy független Webjobs](#createnolink)
  
    Hozzon létre egy projektet, és konfigurálja úgy, hogy magát Webjobs-ként telepítse, és ne legyen webes projektre mutató hivatkozás. Akkor használja ezt a beállítást, ha saját maga szeretné futtatni a Webjobs egy webalkalmazásban, és a webalkalmazásban nem fut a webes alkalmazás. Ezt úgy teheti meg, hogy a webalkalmazás-erőforrásaitól függetlenül szeretné a Webjobs-erőforrásokat méretezni.
* [A webjobs új-Project sablonjának használata webes projekthez kapcsolódó Webjobs](#createlink)
  
    Hozzon létre egy projektet, amely úgy van beállítva, hogy automatikusan Webjobs telepítsen, amikor egy webes projekt ugyanabban a megoldásban van telepítve. Akkor használja ezt a beállítást, ha a Webjobs ugyanabban a webalkalmazásban szeretné futtatni, amelyben a kapcsolódó webalkalmazást futtatja.

> [!NOTE]
> A webjobs új-Project sablonja automatikusan telepíti a NuGet-csomagokat, és tartalmazza a *program.cs* -ben a [webjobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)-hoz tartozó kódot. Ha nem szeretné használni a webjobs SDK-t, távolítsa el vagy módosítsa az `host.RunAndBlock` utasítást a *program.cs*-ben.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>A webjobs új-Project sablonjának használata egy független Webjobs
1. Kattintson a **fájl**  >  **új projekt**elemre, majd az **új projekt** párbeszédpanelen kattintson a **Cloud**  >  **Azure webjobs (.NET-keretrendszer)** elemre.
   
    ![Új projekt párbeszédpanel, amely a Webjobs-sablont mutatja](./media/webjobs-dotnet-deploy-vs/np.png)
2. Kövesse a korábban bemutatott utasításokat, hogy [a konzol alkalmazás-projekthez egy független webjobs-projektet lehessen készíteni](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>A webjobs új-Project sablonjának használata webes projekthez kapcsolódó Webjobs
1. Kattintson a jobb gombbal a **megoldáskezelő**található webes projektre, majd **Add**kattintson az  >  **új Azure webjobs-projekt**hozzáadása lehetőségre.
   
    ![Új Azure Webjobs projekt menü bejegyzés](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Megjelenik az [Azure-Webjobs hozzáadása](#configure) párbeszédpanel.
2. Fejezze be az [Azure-Webjobs hozzáadása](#configure) párbeszédpanelt, majd kattintson **az OK**gombra.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Az Azure-Webjobs hozzáadása párbeszédpanel
Az **Azure-Webjobs hozzáadása** párbeszédpanelen megadhatja a webjobs nevét és a futtatási mód beállítását a webjobs. 

![Azure-Webjobs hozzáadása párbeszédpanel](./media/webjobs-dotnet-deploy-vs/aaw2.png)

A párbeszédpanel mezői a Azure Portal **Webjobs hozzáadása** párbeszédpanel mezőinek felelnek meg. További információ: [háttérben futó feladatok futtatása webjobs](webjobs-create.md)-feladatokkal.

> [!NOTE]
> * További információ a parancssori telepítésről: a [Azure WebJobs parancssori vagy folyamatos kézbesítésének engedélyezése](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Ha központilag telepít egy Webjobs, és eldönti, hogy szeretné-e módosítani a Webjobs típusát és az újbóli üzembe helyezést, törölnie kell a fájlt a *webjobs-publish-settings.js* . Ekkor a Visual Studio ismét megjeleníti a közzétételi beállításokat, így módosíthatja a Webjobs típusát.
> * Ha központilag telepít egy Webjobs, és később a Run (Futtatás) módot folyamatosan, nem folyamatosra módosítja, vagy fordítva, a Visual Studio új Webjobs hoz létre az Azure-ban az újbóli üzembe helyezéskor. Ha más ütemezési beállításokat módosít, de a futtatási mód nem egyezik meg az ütemezett és igény szerinti váltással, akkor a Visual Studio frissíti a meglévő feladatot, és nem hoz létre újat.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.jsbekapcsolva
Ha a webjobs-telepítéshez konfigurál egy konzol alkalmazást, a Visual Studio telepíti a [Microsoft. Web. Webjobss. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet csomagot, és az ütemezési információkat a webjobs-projekt Project *Properties* mappájában található fájl *webjob-publish-settings.js* tárolja. Íme egy példa erre a fájlra:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

Ezt a fájlt közvetlenül szerkesztheti, a Visual Studio pedig IntelliSense-t is biztosít. A fájl sémája a (z) helyen található [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) , és itt tekinthető meg.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.jsbekapcsolva
Amikor webjobs-kompatibilis projektet kapcsol össze egy webes projekthez, a Visual Studio a webes projekt *Tulajdonságok* mappájában tárolja *webjobs-list.jsa* webjobs-projekt nevét. A lista több webjobs-projektet is tartalmazhat, ahogy az alábbi példában is látható:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
  "WebJobs": [
    {
      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
    },
    {
      "filePath": "../WebJob1/WebJob1.csproj"
    }
  ]
}
```

Ezt a fájlt közvetlenül szerkesztheti, a Visual Studio pedig IntelliSense-t is biztosít. A fájl sémája a (z) helyen található [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) , és itt tekinthető meg.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Webjobs-projekt üzembe helyezése
Egy webjobs-projekt, amely webes projekthez van csatolva, automatikusan települ a webes projekttel. További információ a webes projekt központi telepítéséről: **útmutató**az  >  **alkalmazás központi telepítése** a bal oldali navigációs sávon.

Webjobs-projekt saját maga általi üzembe helyezéséhez kattintson a jobb gombbal a projektre **megoldáskezelő** , majd kattintson a **Közzététel Azure-webjobs...** lehetőségre. 

![Közzététel Azure-Webjobs](./media/webjobs-dotnet-deploy-vs/paw.png)

Egy független Webjobs esetében a webes projektekhez használt **közzétételi webes** varázsló jelenik meg, de a módosításhoz kevesebb beállítás áll rendelkezésre.

## <a name="scheduling-a-triggered-webjob"></a>Aktivált WebJobs-feladat ütemezése

A webjobs egy *Settings. job* fájlt használ a webjobs futtatásának megállapításához. Ezzel a fájllal állíthatja be a Webjobs végrehajtási ütemtervét. A következő példa óránként, 9 órától 5 óráig fut le:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Ennek a fájlnak a webjobs-mappa gyökerében kell lennie, amely a Webjobs parancsfájljában, például `wwwroot\app_data\jobs\triggered\{job name}` vagy `wwwroot\app_data\jobs\continuous\{job name}` . Ha a Visual studióból helyez üzembe egy Webjobs, a `settings.job` fájl tulajdonságainak megjelölése **másolással, ha újabb**. 

Amikor [webjobs hoz létre a Azure Portal](webjobs-create.md), a Settings. job fájl jön létre az Ön számára.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON-kifejezések

A webjobs ugyanazokat a CRON-kifejezéseket használja az ütemezéshez, mint az időzítő trigger Azure Functionsban. A CRON-támogatással kapcsolatos további tudnivalókért tekintse meg az [időzítő trigger-referenciát ismertető cikket](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>Settings. job-hivatkozás

A webjobs a következő beállításokat támogatja:

| **Beállítás** | **Típus**  | **Leírás** |
| ----------- | --------- | --------------- |
| `is_in_place` | Mind | Lehetővé teszi a feladatok futtatását anélkül, hogy először egy ideiglenes mappába kellene másolni. További információ: [webjobs Working Directory](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Folyamatos | A felskálázáskor csak egyetlen példányon futtassa a webjobs-feladatokat. További információ: [folyamatos munka beállítása](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)egypéldányos beállítással. |
| `schedule` | Kiváltott | Futtassa a Webjobs egy CRON-alapú ütemterven. További információért lásd az [időzítő trigger-referenciát ismertető cikket](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Mind | Lehetővé teszi a leállítási viselkedés vezérlését. További információ: [kecses leállítás](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a webjobs SDK-ról](webjobs-sdk-how-to.md)
