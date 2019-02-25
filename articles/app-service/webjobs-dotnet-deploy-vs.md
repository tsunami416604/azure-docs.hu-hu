---
title: Fejlesztés és üzembe helyezés a Visual Studio használatával – az Azure webjobs-feladatok
description: Megtudhatja, hogyan fejleszthet és helyezhet üzembe az Azure webjobs-feladatok az Azure App Service-ben a Visual Studio használatával.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ede7e2fe3a2ab4c0dfd4efaea5ec789924968194
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750157"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Fejlesztés és üzembe helyezés a Visual Studio használatával – az Azure App Service webjobs-feladatok

Ez a cikk ismerteti a Visual Studióval egy Console Application-projektet egy webalkalmazást az üzembe [App Service-ben](overview.md) , egy [Azure webjobs-feladat](https://go.microsoft.com/fwlink/?LinkId=390226). A webjobs használatával kapcsolatos információkat a [az Azure portal](https://portal.azure.com), lásd: [háttérfeladatok futtatása WebJobs-feladatokkal](webjobs-create.md).

Egyetlen webalkalmazásban; több WebJobs közzéteheti. Győződjön meg arról, hogy minden egyes webjobs-feladat egy web app alkalmazásban egy egyedi nevet.

Verzió 3.x, a [Azure WebJobs SDK](webjobs-sdk-how-to.md) lehetővé teszi a .NET Core alkalmazásokat, futtató webjobs-feladatok fejlesztése során 2.x verziója támogatja a .NET-keretrendszer verzióját. Az, hogy a webjobs-feladatok projekt telepítése módja különböző .NET Core-projektek és a .NET-keretrendszer azokat.

## <a name="webjobs-as-net-core-console-apps"></a>Webjobs-feladatok, mint a .NET Core-konzolalkalmazások

Ha verziónál 3.x, a webjobs-feladatok létrehozása és közzététele a webjobs-feladatok, mint a .NET Core-konzolalkalmazások. Létrehozása és közzététele a .NET Core-konzolalkalmazást az Azure Webjobs-feladatként részletes utasításokért lásd: [eseményvezérelt háttérbeli feldolgozásra az Azure WebJobs SDK használatának első lépései](webjobs-sdk-get-started.md).

> [!NOTE]
> .NET core WebJobs webes projektek nem lehet csatolva. A webjobs-feladatot a webes alkalmazás üzembe van szüksége, ha [hozzon létre egy .NET-keretrendszer-konzolalkalmazást, webjobs-feladat](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Az Azure App Service üzembe helyezése

App Service-ben a Visual Studióban egy .NET Core webjobs-feladat közzétételét használja az ugyanazon azokat az eszközöket az ASP.NET Core-alkalmazás közzététele.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Webjobs-feladat típusa

Alapértelmezés szerint a webjobs-feladat egy .NET Core console projektfuttatások csak akkor, amikor elindul, vagy igény szerinti közzé. A projekt frissítheti is [futtatása ütemezés szerint](#scheduled-execution) vagy folyamatos Futtatás.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Ütemezett végrehajtás

Amikor közzétesz egy .NET Core-konzolalkalmazást az Azure-bA egy új *settings.job* fájlt adnak hozzá a projekthez. Ez a fájl használata a webjobs-feladat-végrehajtási ütemezés beállításához. További információkért lásd: [aktivált webjobs-feladat ütemezésének](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Folyamatos végrehajtás

A Visual Studio segítségével módosíthatja a webjobs-feladat futtatását, folyamatosan, amikor az AlwaysOn engedélyezve van az Azure-ban.

1. Ha ezt még nem tette meg, [a projekt közzététele az Azure-bA](#deploy-to-azure-app-service).

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. Az a **közzététel** lapra, majd **beállítások**. 

1. Az a **Profilbeállítások** párbeszédpanelen válassza a **folyamatos** a **webjobs-feladat típusa**, és válassza **mentése**.

    ![A webjobs-feladat beállításai párbeszédpanel közzététele](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Válassza ki **közzététel** közzé a webjobs-feladatot a frissített beállításokkal.

## <a name="webjobs-as-net-framework-console-apps"></a>.NET-keretrendszer konzol alkalmazásként webjobs-feladatok  

Ha a Visual Studio telepíti a WebJobs-kompatibilis .NET Framework Konzolalkalmazás-projektet, két feladatot hajtja végre:

* Futtatási fájlokat másolja a megfelelő mappát a webalkalmazásban (*App_Data/feladatok/folyamatos* a folyamatos webjobs-feladatok és *App_Data/feladatok/triggered* ütemezett vagy igény szerinti WebJobs esetében).
* Beállítja a [Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) feladatokat a webjobs-feladatokat, akik adott időpontban futnak. (Ez nem szükséges a folyamatos webjobs-feladatok.)

A WebJobs-kompatibilis projekt hozzáadja a következőkkel rendelkezik:

* A [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-csomagot.
* A [webjobs-feladat közzététele settings.json](#publishsettings) üzembe helyezési és ütemezési beállításait tartalmazó fájl. 

![Mi kerül egy Konzolalkalmazást, Webjobs-feladatként központi telepítés engedélyezése a bemutató ábra.](./media/webjobs-dotnet-deploy-vs/convert.png)

Ezeket az elemeket ad hozzá egy meglévő Konzolalkalmazás-projektet, vagy egy sablon használatával létrehozhat egy új WebJobs-kompatibilis Konzolalkalmazás-projektet. 

Projekt telepítése Webjobs-feladatként önmagában, vagy hivatkozás egy web projektet, hogy automatikusan helyez üzembe, amikor a webes projekt telepítése. Projektek mutató hivatkozást, a Visual Studio tartalmazza a WebJobs-kompatibilis projekt nevét egy [webjobs-list.json](#webjobslist) fájlt a webalkalmazás-projektben.

![Webjobs-feladat project web projektet összekapcsolja bemutató ábra.](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Előfeltételek

Ha a Visual Studio 2015-öt használ, telepítse a [Azure SDK for .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Ha a Visual Studio 2017-et használ, telepítse a [Azure development workload munkafolyamatának](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

### <a id="convert"></a> WebJobs üzembe helyezése meglévő Konzolalkalmazás-projektet engedélyezése

Erre két lehetősége van:

* [Engedélyezze az automatikus üzembe helyezés, a webes projektet](#convertlink).

  Állítsa egy meglévő Konzolalkalmazás-projektet, hogy azt automatikusan telepíti a Webjobs-feladatként webes projektet központi telepítésekor. Használja ezt a beállítást, ha meg szeretné futtatni a webjobs-feladat ugyanazt a webalkalmazást, amelyben a kapcsolódó webalkalmazás futtatása.

* [Központi telepítés anélkül, hogy webes projektet engedélyezése](#convertnolink).

  Konfigurálja a Webjobs-feladatként telepítéséhez önmagában, és nincs hivatkozás a webes projektet egy meglévő Konzolalkalmazás-projektet. Használja ezt a beállítást, ha meg szeretné a webjobs-feladat futtatása webalkalmazásban önmagában, az nincs a webalkalmazás-ban futó webalkalmazás. Érdemes ehhez ahhoz, hogy a Webjobs-erőforrások, függetlenül a webalkalmazás-erőforrások skálázása.

#### <a id="convertlink"></a> Webes projektet automatikus WebJobs üzembe helyezésének engedélyezése

1. Kattintson a jobb gombbal a webes projektre a **Megoldáskezelőben**, és kattintson a **Hozzáadás** > **meglévő projektet Azure WebJob feladatként**.
   
    ![Meglévő projekt Azure WebJob feladatként](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    A [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel jelenik meg.
2. Az a **projektnév** legördülő listában válassza a Konzolalkalmazást projekthez legyen hozzáadva egy WebJob feladatként.
   
    ![Válassza ki a projektet Azure WebJob hozzáadása párbeszédpanel](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Végezze el a [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel, és kattintson **OK**. 

#### <a id="convertnolink"></a> Anélkül, hogy webes projektet a WebJobs üzembe helyezés engedélyezése
1. Kattintson a jobb gombbal a Konzolalkalmazás-projektben **Megoldáskezelőben**, és kattintson a **Publish as Azure WebJob...** . 
   
    ![Közzététel Azure webjobsként](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    A [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel jelenik meg, a kiválasztott projekthez a **projektnév** mezőbe.
2. Végezze el a [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel, és kattintson **OK**.
   
   A **Publish Web** varázsló jelenik meg.  Ha nem szeretné azonnal közzététele, zárja be a varázslót. A beállítások a megadott a lesznek mentve, ha szeretné, hogy [a projekt telepítése](#deploy).

### <a id="create"></a>WebJobs-kompatibilis új projekt létrehozása
WebJobs-kompatibilis az új projekt létrehozásához használja a Konzolalkalmazás-projekt sablont és WebJobs üzembe helyezés engedélyezése, a [az előző szakaszban](#convert). Alternatív megoldásként használhatja a webjobs-feladatok új webesprojekt-sablon:

* [A webjobs-feladatok – új projekt sablont használ egy független webjobs-feladat](#createnolink)
  
    Hozzon létre egy projektet, és konfigurálja úgy, hogy saját maga, és nincs hivatkozás webes projektet a Webjobs-feladatként üzembe helyezése. Használja ezt a beállítást, ha meg szeretné a webjobs-feladat futtatása webalkalmazásban önmagában, az nincs a webalkalmazás-ban futó webalkalmazás. Érdemes ehhez ahhoz, hogy a Webjobs-erőforrások, függetlenül a webalkalmazás-erőforrások skálázása.
* [A webjobs-feladatok új webesprojekt-sablon használata a webjobs-feladat egy webes projekthez társított](#createlink)
  
    Hozzon létre egy projektet, amely konfigurálva van egy webes projektet a megoldásban üzembe helyezésekor Webjobs-feladatként automatikus központi telepítés. Használja ezt a beállítást, ha meg szeretné futtatni a webjobs-feladat ugyanazt a webalkalmazást, amelyben a kapcsolódó webalkalmazás futtatása.

> [!NOTE]
> A webjobs-feladatok új webesprojekt-sablon automatikusan telepíti a NuGet-csomagok és a kód *Program.cs* számára a [WebJobs SDK-val](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Ha nem szeretné a WebJobs SDK-val, távolítsa el vagy módosítsa a `host.RunAndBlock` utasítás *Program.cs*.
> 
> 

#### <a id="createnolink"></a> A webjobs-feladatok – új projekt sablont használ egy független webjobs-feladat
1. Kattintson a **fájl** > **új projekt**, majd a **új projekt** párbeszédpanelen kattintson **felhőalapú**  >   **Az Azure WebJob (.NET-keretrendszer)**.
   
    ![Új projekt párbeszédpanel a webjobs-feladat sablon megjelenítése](./media/webjobs-dotnet-deploy-vs/np.png)
2. Kövesse a fentebb látható [győződjön meg arról, a Konzolalkalmazás egy független a WebJobs-projektet a project](#convertnolink).

#### <a id="createlink"></a> A webjobs-feladatok új webesprojekt-sablon használata a webjobs-feladat egy webes projekthez társított
1. Kattintson a jobb gombbal a webes projektre a **Megoldáskezelőben**, és kattintson a **Hozzáadás** > **új Azure webjobs-feladat projekt**.
   
    ![Új Azure webjobs-feladat projekt menüpont](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    A [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel jelenik meg.
2. Végezze el a [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel, és kattintson **OK**.

### <a id="configure"></a>Az Azure Webjobs hozzáadása párbeszédpanel
A **adja hozzá a az Azure webjobs-feladat** párbeszédpanel segítségével adja meg a webjobs-feladat neve és mód beállítása a webjobs-feladat futtatása. 

![Adja hozzá az Azure webjobs-feladat párbeszédpanel](./media/webjobs-dotnet-deploy-vs/aaw2.png)

A mezők a mezők ezen a párbeszédpanelen felel meg a **a webjobs-feladat hozzáadása** az Azure Portal párbeszédpanel. További információkért lásd: [háttérfeladatok futtatása WebJobs-feladatokkal](webjobs-create.md).

> [!NOTE]
> * Parancssori telepítéssel kapcsolatos információkért lásd: [parancssori engedélyezése vagy a folyamatos teljesítés az Azure webjobs-feladatok](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Ha a Webjobs üzembe helyezése, és ezután döntse el, a webjobs-feladat és ismételt üzembe helyezése típusát módosítani szeretné, törölni kell a *webjobs közzététele settings.json* fájlt. Ez teszi Visual Studióban jelenjen meg a közzétételi beállítások, így módosíthatja a webjobs-feladat típusát.
> * A Webjobs üzembe helyezése, és később módosítani a futtatási mód folyamatos nem folytonos vagy fordítva, ha a Visual Studio létrehoz egy új webjobs-feladat Azure újbóli telepítése során. Ha további ütemezési beállítások módosítására, de hagyja futtatási mód azonos, vagy váltás ütemezett és igény szerinti, a Visual Studio a meglévő feladat frissítése helyett hozzon létre egy újat.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
Amikor konfigurál egy Konzolalkalmazást WebJobs üzembe helyezéshez, a Visual Studio telepíti a [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet csomag és az ütemezési információkat a tárolók egy *webjobs-feladat közzététele settings.json*  fájlt a projektben *tulajdonságok* mappájában, a WebJobs-projekt. Íme egy példa, hogy a fájl:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Közvetlenül szerkesztheti ezt a fájlt, és a Visual Studio IntelliSense biztosít. A fájl séma tárolt [ https://schemastore.org ](https://schemastore.org/schemas/json/webjob-publish-settings.json) ott lehet megtekinteni.  

### <a id="webjobslist"></a>webjobs-list.json
A WebJobs-kompatibilis projekt kapcsol egy web projektet, ha a Visual Studio a webjobs-feladatok projekt nevét tárolja egy *webjobs-list.json* fájlt a webalkalmazás-projektben *tulajdonságok* mappát. A lista tartalmazhatja több WebJobs projekteket, a következő példában látható módon:

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

Közvetlenül szerkesztheti ezt a fájlt, és a Visual Studio IntelliSense biztosít. A fájl séma tárolt [ https://schemastore.org ](https://schemastore.org/schemas/json/webjobs-list.json) ott lehet megtekinteni.

### <a id="deploy"></a>A WebJobs-projekt üzembe helyezése
Egy WebJobs-projektet, amelyet összekapcsolt, hogy webes projektet a webes projekt automatikusan üzembe helyezi. További információ a webes projekt telepítése: **útmutató végigvezeti a** > **üzembe helyezés alkalmazás** a bal oldali navigációs.

A webjobs-feladatok projekt önmagában üzembe helyezéséhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben** kattintson **Publish as Azure WebJob...** . 

![Közzététel Azure webjobsként](./media/webjobs-dotnet-deploy-vs/paw.png)

Egy független a webjobs-feladat, ugyanaz a **Publish Web** használt a webes projektek jelenik meg, de kevesebb konfigurációs lehetőség módosítása varázsló.

## <a name="scheduling-a-triggered-webjob"></a>Aktivált webjobs-feladat ütemezése

Webjobs-feladatok használ egy *settings.job* meghatározza, hogy a webjobs-feladat futtatásakor. Ez a fájl használata a webjobs-feladat-végrehajtási ütemezés beállításához. Az alábbi példa fut minden órában 9: 00 és 17: 00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Ezt a fájlt kell a webjobs-feladatok mappa gyökerében található mentén egymás mellett parancsprogram a webjobs-feladat, mint például `wwwroot\app_data\jobs\triggered\{job name}` vagy `wwwroot\app_data\jobs\continuous\{job name}`. Amikor telepít egy webjobs-feladatot a Visual Studióból, jelölje meg a `settings.job` tulajdonságokat a fájl **másolás, ha újabb**. 

Ha Ön [webjobs-feladat létrehozása az Azure Portalról](webjobs-create.md), a settings.job fájlt hoznak létre az Ön számára.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON-kifejezések

Webjobs-feladatok az azonos CRON-kifejezéseket használ, az Azure Functions időzítő eseményindító ütemezési. CRON-támogatással kapcsolatos további tudnivalókért tekintse meg a [időzítő eseményindító áttekintésével foglalkozó cikkben](../azure-functions/functions-bindings-timer.md#cron-expressions).

### <a name="settingjob-reference"></a>Setting.job referencia

Webjobs-feladatok az alábbi beállításokat támogatja:

| **Beállítás** | **Típus**  | **Leírás** |
| ----------- | --------- | --------------- |
| `is_in_place` | Összes | Lehetővé teszi, hogy a feladat futtatása helyben anélkül, hogy először egy ideiglenes mappába másolni. További tudnivalókért lásd: [WebJobs munkakönyvtár](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Folyamatos | A webjobs-feladatok csak egy példányban, ha horizontálisan felskálázott futtatható. További tudnivalókért lásd: [állítsa be a folyamatos feladat egypéldányosnak](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Triggerrel indított | A webjobs-feladat CRON-alapú ütemezett futtatását. További TUDNIVALÓKÉRT tekintse meg a [időzítő eseményindító áttekintésével foglalkozó cikkben](../azure-functions/functions-bindings-timer.md#cron-expressions). |
| `stopping_wait_time`| Összes | A leállítási viselkedése irányítását teszi lehetővé. További tudnivalókért lásd: [szabályos leállítást](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a WebJobs SDK-val](webjobs-sdk-how-to.md)
