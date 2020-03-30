---
title: WebJobs-alkalmazások fejlesztése és telepítése a VS használatával
description: Megtudhatja, hogyan fejlesztheti az Azure WebJobs-ot a Visual Studióban, és hogyan helyezheti üzembe őket az Azure App Service szolgáltatásban, beleértve egy ütemezett feladat létrehozását is.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: feacd463a10bae66dc8fa88a99b9ea60f399e9ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684173"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>WebJobs-feladatok üzembe helyezése Visual Studióval – Azure App Service

Ebből a cikkből megtudhatja, hogy a Visual Studio segítségével hogyan helyezhet üzembe egy konzolalkalmazás-projektet egy webalkalmazásra az [App](overview.md) [Service-ben, mint egy Azure WebJob.](https://go.microsoft.com/fwlink/?LinkId=390226) A WebJobs Azure [Portal](https://portal.azure.com)használatával történő központi telepítéséről a [Háttérfeladatok futtatása webfeladatokkal](webjobs-create.md)című témakörben talál.

Egyetlen webalkalmazásban több WebJobs-ot is közzétehet. Győződjön meg arról, hogy a webalkalmazás minden webjobusának egyedi neve van.

Az [Azure WebJobs SDK](webjobs-sdk-how-to.md) 3.x-es verziója lehetővé teszi a .NET Core vagy .NET Framework alkalmazásokként futó WebJobs-alkalmazások fejlesztését, míg a 2.x-es verzió csak a . A WebJobs-projektek központi telepítésének módja a .NET Core projektekés a .NET Framework projektek esetében eltérő.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs mint .NET Core konzolalkalmazások

A WebJobs 3.x-es verziójának használatakor webjobs-okat hozhat létre és tehet közzé .NET Core konzolalkalmazásként. A .NET Core konzolalkalmazások webfeladatként történő létrehozásáról és közzétételéről az [Azure WebJobs SDK első lépései az eseményvezérelt háttérfeldolgozáshoz](webjobs-sdk-get-started.md)című témakörben talál részletes útmutatást.

> [!NOTE]
> A .NET Core WebJobs nem kapcsolható webes projektekhez. Ha webalkalmazással kell telepítenie a WebJobot, [a WebJobot .NET Framework konzolalkalmazásként kell létrehoznia.](#webjobs-as-net-framework-console-apps)  

### <a name="deploy-to-azure-app-service"></a>Üzembe helyezés az Azure App Service-ben

A .NET Core WebJob közzététele az App Service szolgáltatásban a Visual Studio-ból ugyanazt az eszközt használja, mint egy ASP.NET Core alkalmazás közzététele.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJob-típusok

Alapértelmezés szerint a .NET Core konzolprojektből közzétett WebJob csak akkor fut, ha aktiválva van, vagy igény szerint. A projektet frissítheti is, hogy [ütemezés szerint fusson,](#scheduled-execution) vagy folyamatosan fusson.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Ütemezett végrehajtás

Amikor közzétesz egy .NET Core konzolalkalmazást az Azure-ban, egy új *settings.job* fájl kerül a projektbe. Ezzel a fájllal állítsa be a webfeladat végrehajtási ütemezését. További információt az [Aktivált webfeladat ütemezése](#scheduling-a-triggered-webjob)című témakörben talál.

#### <a name="continuous-execution"></a>Folyamatos végrehajtás

A Visual Studio segítségével módosíthatja a WebJob folyamatos futtatását, ha az Always On engedélyezve van az Azure-ban.

1. Ha még nem tette meg, [tegye közzé a projektet az Azure-ban.](#deploy-to-azure-app-service)

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel** lapon válassza a **Beállítások lehetőséget.** 

1. A **Profilbeállítások** párbeszédpanelen válassza a **Folyamatos** a **WebFeladat típusához**lehetőséget, majd a **Mentés gombot.**

    ![Webfeladat Beállítások közzététele párbeszédpanelje](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. A **Közzététel gombra** választva tegye közzé újra a webfeladatot a frissített beállításokkal.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs mint .NET Framework konzolalkalmazások  

Amikor a Visual Studio webjobs-kompatibilis .NET Framework Console Application projektet telepít, a futásidejű fájlokat a webalkalmazás megfelelő mappájába másolja *(App_Data/jobs/continuous* folyamatos folyamatos webfeladatokhoz és *App_Data/feladatok/ütemezett* vagy igény szerinti WebJobs esetén).

A WebJobs-kompatibilis projektekhez a következő elemek et adják hozzá:

* A [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet csomag.
* [Webjob-publish-settings.json](#publishsettings) fájl, amely telepítési és ütemezőbeállításokat tartalmaz. 

![A konzolalkalmazáshoz hozzáadott tartalom, amely webfeladatként engedélyezi a telepítést](./media/webjobs-dotnet-deploy-vs/convert.png)

Ezeket az elemeket hozzáadhatja egy meglévő konzolalkalmazás-projekthez, vagy sablonnal új WebJobs-kompatibilis konzolalkalmazás-projektet hozhat létre. 

A projektet önmagában is telepítheti WebJob ként, vagy összekapcsolhatja egy webprojekttel, hogy az automatikusan üzembe helyezhesse a webes projekt telepítésekor. Projektek csatolásához a Visual Studio tartalmazza a WebJobs-kompatibilis projekt nevét egy [webjobs-list.json](#webjobslist) fájlban a webes projektben.

![Webes projekthez való csatolást bemutató diagram](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Előfeltételek

Visual Studio 2015 használata esetén telepítse az [Azure SDK for .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/)alkalmazást.

Visual Studio 2017 használata esetén telepítse az [Azure fejlesztési számítási feladatait.](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>WebJobs-telepítés engedélyezése meglévő konzolalkalmazás-projekthez

Erre két lehetősége van:

* [Engedélyezze az automatikus telepítést egy webes projekttel.](#convertlink)

  Konfiguráljon egy meglévő konzolalkalmazás-projektet úgy, hogy az automatikusan webfeladatként települjön, amikor webprojektet telepít. Akkor használja ezt a beállítást, ha a WebJobot ugyanabban a webalkalmazásban szeretné futtatni, amelyben a kapcsolódó webalkalmazást futtatja.

* [A telepítés engedélyezése webes projekt nélkül](#convertnolink).

  Konfiguráljon úgy egy meglévő konzolalkalmazás-projektet, hogy az önmagában webfeladatként települjen, és ne hivatkozhatna egy webes projektre. Akkor használja ezt a beállítást, ha egy webmunkát egy webalkalmazásban szeretne egyedül futtatni, és nem fut webalkalmazás a webalkalmazásban. Erre érdemes lehet a WebJob-erőforrások méretezése érdekében, függetlenül a webalkalmazás-erőforrásoktól.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Automatikus WebJobs-telepítés engedélyezése webes projekttel

1. Kattintson a jobb gombbal a webes projektre a **Megoldáskezelőben,** majd kattintson a Meglévő projekt **hozzáadása** > **Azure WebJob néven parancsra.**
   
    ![Meglévő projekt Azure WebJob néven](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Megjelenik [az Azure WebJob hozzáadása](#configure) párbeszédpanel.
2. A **Projekt név** legördülő listában válassza ki a WebJobként hozzáadni kívánt konzolalkalmazás-projektet.
   
    ![Projekt kijelölése az Azure WebJob hozzáadása párbeszédpanelen](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Fejezze be az [Azure WebJob hozzáadása](#configure) párbeszédpanelt, majd kattintson az **OK**gombra. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>WebJobs-telepítés engedélyezése webes projekt nélkül
1. Kattintson a jobb gombbal a Konzolalkalmazás projektre a **Megoldáskezelőben,** majd kattintson a **Közzététel Azure WebJobként parancsra.** 
   
    ![Közzététel Azure WebJob néven](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Megjelenik [az Azure WebJob hozzáadása](#configure) párbeszédpanel, amelyen a projekt a Projekt **neve** mezőben van kijelölve.
2. Töltse ki az [Azure WebJob hozzáadása](#configure) párbeszédpanelt, majd kattintson az **OK**gombra.
   
   Megjelenik **a Webhely közzététele** varázsló.  Ha nem kíván azonnal közzétenni, zárja be a varázslót. A megadott beállításokat a program menti, amikor telepíteni szeretné [a projektet.](#deploy)

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Új WebJobs-kompatibilis projekt létrehozása
Új WebJobs-kompatibilis projekt létrehozásához használja a Konzolalkalmazás projektsablont, és engedélyezheti a WebJobs központi telepítését [az előző szakaszban](#convert)leírtak szerint. Másik lehetőségként használhatja a WebJobs új projekt sablont:

* [A WebJobs új projektsablon használata független WebJob hoz](#createnolink)
  
    Hozzon létre egy projektet, és konfigurálja úgy, hogy saját maga webfeladatként telepítse, és nincs hivatkozás egy webes projektre. Akkor használja ezt a beállítást, ha egy webmunkát egy webalkalmazásban szeretne egyedül futtatni, és nem fut webalkalmazás a webalkalmazásban. Erre érdemes lehet a WebJob-erőforrások méretezése érdekében, függetlenül a webalkalmazás-erőforrásoktól.
* [WebJobs új projekt sablon használata webes projekthez kapcsolódó WebJob esetén](#createlink)
  
    Hozzon létre egy olyan projektet, amely úgy van beállítva, hogy automatikusan webfeladatként települjön, ha egy webprojekt ugyanabban a megoldásban van telepítve. Akkor használja ezt a beállítást, ha a WebJobot ugyanabban a webalkalmazásban szeretné futtatni, amelyben a kapcsolódó webalkalmazást futtatja.

> [!NOTE]
> A WebJobs új projektsablon automatikusan telepíti a NuGet csomagokat, és a [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) *Program.cs* tartalmaz kódot. Ha nem szeretné használni a WebJobs SDK-t, `host.RunAndBlock` távolítsa el vagy módosítsa az utasítást *a Program.cs.*
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>A WebJobs új projektsablon használata független WebJob hoz
1. Kattintson az**Új projekt** **fájljára** > , majd az **Új projekt** párbeszédpanelen kattintson a **Cloud** > **Azure WebJob (.NET Framework)** elemre.
   
    ![A WebJob sablont megjelenítő Új Projekt párbeszédpanel](./media/webjobs-dotnet-deploy-vs/np.png)
2. Kövesse a korábban bemutatott utasításokat, [hogy a konzolalkalmazás-projekt független WebJobs projektté legyen.](#convertnolink)

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>WebJobs új projekt sablon használata webes projekthez kapcsolódó WebJob esetén
1. Kattintson a jobb gombbal a webes projektre a **Megoldáskezelőben**, majd kattintson az**Új Azure WebJob-projekt** **hozzáadása** > parancsra .
   
    ![Új Azure WebJob Project menübejegyzés](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Megjelenik [az Azure WebJob hozzáadása](#configure) párbeszédpanel.
2. Töltse ki az [Azure WebJob hozzáadása](#configure) párbeszédpanelt, majd kattintson az **OK**gombra.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Az Azure WebJob hozzáadása párbeszédpanel
Az **Azure WebJob hozzáadása** párbeszédpanelen megadhatja a WebJob nevét, és futtathatja a webfeladat módbeállítását. 

![Az Azure WebJob hozzáadása párbeszédpanel](./media/webjobs-dotnet-deploy-vs/aaw2.png)

A párbeszédpanel mezői az Azure Portal **WebJob hozzáadása** párbeszédpanelén lévő mezőknek felelnek meg. További információt a [Háttérfeladatok futtatása webfeladatokkal](webjobs-create.md)című témakörben talál.

> [!NOTE]
> * A parancssori telepítésről az [Azure WebJobs parancssori vagy folyamatos kézbesítésének engedélyezése](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)című témakörben talál további információt.
> * Ha telepít egy WebJob, majd úgy dönt, hogy módosítani szeretné a WebJob típusát, és újratelepíteni, törölnie kell a *webjobs-publish-settings.json* fájlt. Ezzel a Visual Studio ismét megjeleníti a közzétételi beállításokat, így módosíthatja a WebJob típusát.
> * Ha telepít egy WebJobot, és később folyamatosan nem folytonosról nem folytonosra módosítja a futtatási módot, vagy fordítva, a Visual Studio új WebJobot hoz létre az Azure-ban az újratelepítéskor. Ha más ütemezési beállításokat is módosít, de a futtatási módot ugyanúgy hagyja, vagy az Ütemezett és az Igény szerinti között vált, a Visual Studio nem új, hanem frissíti a meglévő feladatot.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
Amikor konfigurál egy konzolalkalmazást a WebJobs központi telepítéséhez, a Visual Studio telepíti a [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet csomagot, és az ütemezési információkat egy *webjob-publish-settings.json* fájlban tárolja a WebJobs projekt *projekt tulajdonságai* mappájában. Íme egy példa erre a fájlra:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Ezt a fájlt közvetlenül is szerkesztheti, és a Visual Studio biztosítja az IntelliSense-t. A fájlséma a [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) rendszer tárolja, és ott is megtekinthető.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
Amikor webfeladatokat támogató projektet csatol egy webes projekthez, a Visual Studio a *Webjobs-list.json* fájlban tárolja a WebJobs projekt nevét a webes projekt *Tulajdonságok* mappájában. A lista több WebJobs-projektet is tartalmazhat, ahogy az a következő példában látható:

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

Ezt a fájlt közvetlenül is szerkesztheti, és a Visual Studio biztosítja az IntelliSense-t. A fájlséma a [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) rendszer tárolja, és ott is megtekinthető.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>WebJobs-projekt telepítése
A webprojekthez kapcsolt WebJobs-projekt automatikusan települ a webes projekttel. A webes projektek telepítéséről további információt az **Útmutató útmutatók** > **alkalmazás telepítése** a bal oldali navigációs sávban című témakörben talál.

WebJobs-projekt önálló üzembe helyezéséhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben,** és kattintson a **Közzététel Azure WebJobként...** parancsra. 

![Közzététel Azure WebJob néven](./media/webjobs-dotnet-deploy-vs/paw.png)

Független WebJob esetén megjelenik a webes projektekhez használt **Webes közzététel** varázsló, de kevesebb beállítás sal módosítható.

## <a name="scheduling-a-triggered-webjob"></a>Aktivált WebJobs-feladat ütemezése

A WebJobs egy *settings.job* fájlt használ a WebFeladat futtatásának meghatározásához. Ezzel a fájllal állítsa be a webfeladat végrehajtási ütemezését. A következő példa óránként 9:00 és 17:00 óra között fut:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Ennek a fájlnak a WebJobs mappa gyökerében, a WebJob `wwwroot\app_data\jobs\triggered\{job name}` parancsfájlja mellett kell lennie, például vagy `wwwroot\app_data\jobs\continuous\{job name}`. Amikor webfeladatot telepít a Visual Studióból, jelölje meg a `settings.job` fájltulajdonságokat **másolásként, ha újabb**. 

Amikor [létrehoz egy WebJob az Azure Portalon,](webjobs-create.md)a settings.job fájl jön létre az Ön számára.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON-kifejezések

A WebJobs ugyanazokat a CRON-kifejezéseket használja az ütemezéshez, mint az Azure Functions időzítőeseményindítója. Ha többet szeretne megtudni a CRON-támogatásról, olvassa el az [időzítő triggerének referenciacikkét.](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingjob-reference"></a>setting.job hivatkozás

A WebJobs a következő beállításokat támogatja:

| **Beállítás** | **Típus**  | **Leírás** |
| ----------- | --------- | --------------- |
| `is_in_place` | Összes | Lehetővé teszi, hogy a feladat a helyén fusson anélkül, hogy először átmásolna egy ideiglenes mappába. További információ: [WebJobs munkakönyvtár](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Folyamatos | Csak a WebJobs-ot egyetlen példányon futtassa, ha horizontális annekt. További információ: [Folyamatos feladat beállítása singletonként](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)című témakörben olvashat. |
| `schedule` | Kiváltott | Cron-alapú ütemezés szerint futtassa a WebJob ot. További információért tekintse meg az [időzítő triggerének referenciacikkét.](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) |
| `stopping_wait_time`| Összes | Lehetővé teszi a leállítási viselkedés szabályozását. További információ: [Kecses leállítás](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a WebJobs SDK-ról](webjobs-sdk-how-to.md)
