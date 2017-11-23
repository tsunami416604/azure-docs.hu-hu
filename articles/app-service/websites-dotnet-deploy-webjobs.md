---
title: "Fejlesztésekor és telepítésekor használja a Visual Studio - Azure webjobs-feladatok"
description: "Megtudhatja, hogyan fejleszthet és Azure WebJobs telepítése az Azure App Service Visual Studio használatával."
services: app-service
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 8793485f2f1967e8c14ef335f14f81c9b946f974
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Fejlesztésekor és telepítésekor a Visual Studio - Azure App Service segítségével webjobs-feladatok

## <a name="overview"></a>Áttekintés

Ez a témakör ismerteti, hogyan egy konzolalkalmazás projekt telepítése egy webalkalmazást, a Visual Studio [App Service](app-service-web-overview.md) , egy [Azure webjobs-feladat](http://go.microsoft.com/fwlink/?LinkId=390226). A webjobs-feladatok telepítésével kapcsolatos információkat a [Azure-portálon](https://portal.azure.com), lásd: [háttérfeladatok futtatása a webjobs-feladatok](web-sites-create-web-jobs.md).

Ha a Visual Studio telepíti a WebJobs-kompatibilis Konzolalkalmazás projekt, két feladatokat hajtja végre:

* Futásidejű fájlokat másolja a megfelelő mappát a web app alkalmazásban (*App_Data/feladatok/folyamatos* a folyamatos webjobs-feladatok, *App_Data/feladatok/indított* az ütemezett és igény szerinti webjobs-feladatok).
* Beállítja az [Azure ütemezőjének](#scheduler) az adott időpontokban futtatásra ütemezett webjobs-feladatok. (Ez nem szükséges a folyamatos WebJobs.)

A WebJobs-kompatibilis projekt van felvéve a következő elemek:

* A [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-csomagot.
* A [webjobs-feladat közzététele settings.json](#publishsettings) központi telepítés és az ütemezési beállításokat tartalmazó fájl. 

![Mi kerül egy központi telepítésben is webjobs-feladat engedélyezése Konzolalkalmazás bemutató ábra](./media/websites-dotnet-deploy-webjobs/convert.png)

Ezek az elemek hozzáadása egy meglévő Konzolalkalmazás-projektet, vagy egy sablon használatával létrehozhat egy új WebJobs-kompatibilis Konzolalkalmazás-projektet. 

Projekt telepítése webjobs-feladat, önmagában, vagy hivatkozás egy webes projekt, hogy azt automatikusan telepíti, ha a webes projekt telepítése. Projektek csatolásához Visual Studio tartalmazza a WebJobs-kompatibilis projekt nevét a [webjobs-list.json](#webjobslist) a webes projekt fájlban.

![Webjobs-feladat project web project csatolása bemutató ábra](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Előfeltételek

Ha a Visual Studio 2015-öt használ, telepítse a [Azure SDK for .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Ha a Visual Studio 2017 használata esetén telepítse a [Azure fejlesztési munkaterhelés](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a>Webjobs-feladatok meglévő Konzolalkalmazás projekt központi telepítésének engedélyezése

Erre két lehetősége van:

* [Engedélyezze az automatikus központi telepítési egy webes projekt](#convertlink).

  Egy meglévő Konzolalkalmazás-projekt konfigurálása, hogy automatikusan telepíti a webjobs-feladat, amikor egy webes projekt telepítése. Használja ezt a beállítást, ha azt szeretné, a webjobs-feladat futtatása az azonos, a kapcsolódó webalkalmazás futtassa web app alkalmazásban.

* [Központi telepítés nélkül webes projektet engedélyezése](#convertnolink).

  Webjobs-feladat készítéséhez önmagában, a webes projektet Kapcsolódás meglévő Konzolalkalmazás projekt konfigurálása. Használja ezt a beállítást, ha azt szeretné, a webjobs-feladat futtatása a webalkalmazásban önmagában nem fut a webes alkalmazás webes alkalmazásokkal együtt. Érdemes ehhez ahhoz, hogy a lehetővé válik a webjobs-feladat erőforrások, függetlenül a webes alkalmazás erőforrásaihoz.

### <a id="convertlink"></a>WebJobs-automatikus központi telepítési egy webes projekt engedélyezése

1. Kattintson a jobb gombbal a webes projekt **Megoldáskezelőben**, és kattintson a **Hozzáadás** > **Azure webjobs-feladat létező projekt**.
   
    ![Azure webjobs-feladat létező projekt](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    A [adja hozzá a Azure webjobs-feladat](#configure) párbeszédpanel jelenik meg.
2. Az a **projektnevet** legördülő listában válassza a Konzolalkalmazás projekt, amelyet a webjobs-feladat.
   
    ![Válassza ki a projektet az Azure Webjobs hozzáadása párbeszédpanel](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Fejezze be a [adja hozzá a Azure webjobs-feladat](#configure) párbeszédpanel, és kattintson **OK**. 

### <a id="convertnolink"></a>Webjobs-feladatok központi telepítés nélkül webes projektet engedélyezése
1. Kattintson a jobb gombbal a Konzolalkalmazás-projektre a **Megoldáskezelőben**, és kattintson a **Azure webjobs-feladat közzététel...** . 
   
    ![Közzététel az Azure webjobs-feladat](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    A [adja hozzá a Azure webjobs-feladat](#configure) párbeszédpanel jelenik meg, a kiválasztott projekthez a **projektnevet** mezőbe.
2. Fejezze be a [adja hozzá a Azure webjobs-feladat](#configure) párbeszédpanel, és kattintson **OK**.
   
   A **webhely közzététele** varázsló jelenik meg.  Ha nem szeretné, azonnal közzététele, zárja be a varázslót. A megadott beállítások a lesznek mentve, ha meg szeretné [a projekt telepítése](#deploy).

## <a id="create"></a>WebJobs-kompatibilis új projekt létrehozása
WebJobs-kompatibilis új projekt létrehozásához, használhatja a Konzolalkalmazás projekt sablont és WebJobs központi telepítés engedélyezése a [az előző szakaszban](#convert). Alternatív megoldásként használhatja a webjobs-feladatok – új projekt sablont:

* [A webjobs-feladatok – új projekt sablon használata az egy független webjobs-feladat](#createnolink)
  
    A projekt létrehozása, és konfigurálja úgy, hogy telepítsen önmagában, a webjobs-feladat, a webes projekt nincs hivatkozás. Használja ezt a beállítást, ha azt szeretné, a webjobs-feladat futtatása a webalkalmazásban önmagában nem fut a webes alkalmazás webes alkalmazásokkal együtt. Érdemes ehhez ahhoz, hogy a lehetővé válik a webjobs-feladat erőforrások, függetlenül a webes alkalmazás erőforrásaihoz.
* [A webjobs-feladatok – új projekt sablon használata az egy webes projekt kapcsolódó webjobs-feladat](#createlink)
  
    Egy webes projekt megoldáskezelőben telepítésekor automatikusan webjobs-feladat telepítéséhez konfigurált-projekt létrehozása. Használja ezt a beállítást, ha azt szeretné, a webjobs-feladat futtatása az azonos, a kapcsolódó webalkalmazás futtassa web app alkalmazásban.

> [!NOTE]
> A webjobs-feladatok – új projekt sablon automatikusan telepíti a NuGet-csomagok, és magában foglalja a kód *Program.cs* a a [WebJobs SDK](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Ha nem szeretné használni a WebJobs SDK, távolítsa el vagy módosítsa a `host.RunAndBlock` utasítás *Program.cs*.
> 
> 

### <a id="createnolink"></a>A webjobs-feladatok – új projekt sablon használata az egy független webjobs-feladat
1. Kattintson a **fájl** > **új projekt**, majd a **új projekt** párbeszédpanelen kattintson **felhő** > **Azure webjobs-feladat (.NET-keretrendszer)**.
   
    ![Webjobs-feladat sablon új projekt párbeszédpanel](./media/websites-dotnet-deploy-webjobs/np.png)
2. Kövesse az utasításokat a korábbi látható [ellenőrizze a Konzolalkalmazás projekt egy független WebJobs-projekt](#convertnolink).

### <a id="createlink"></a>A webjobs-feladatok – új projekt sablon használata az egy webes projekt kapcsolódó webjobs-feladat
1. Kattintson a jobb gombbal a webes projekt **Megoldáskezelőben**, és kattintson a **Hozzáadás** > **új Azure webjobs-feladat projekt**.
   
    ![Új Azure webjobs-feladat projekt menübejegyzést](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    A [adja hozzá a Azure webjobs-feladat](#configure) párbeszédpanel jelenik meg.
2. Fejezze be a [adja hozzá a Azure webjobs-feladat](#configure) párbeszédpanel, és kattintson **OK**.

## <a id="configure"></a>Az Azure Webjobs hozzáadása párbeszédpanel
A **adja hozzá a Azure webjobs-feladat** párbeszédpanelen lehetővé teszi, hogy adja meg a webjobs-feladat nevét, és futtassa a webjobs-feladat mód beállítása. 

![Azure webjobs-feladat párbeszédpanel hozzáadása](./media/websites-dotnet-deploy-webjobs/aaw2.png)

A mezők ezen a párbeszédpanelen mezőinek felelnek meg a **hozzáadása webjobs-feladat** párbeszédpanel az Azure portálról. További információkért lásd: [háttérfeladatok futtatása a webjobs-feladatok](web-sites-create-web-jobs.md).

> [!NOTE]
> * Parancssori telepítéssel kapcsolatos információkért lásd: [parancssori engedélyezése vagy folyamatos kézbesítését az Azure webjobs-feladatok](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Ha telepíti a webjobs-feladat, majd döntse el, a webjobs-feladat, és helyezze üzembe újra módosításához, lesz szüksége törli a *webjobs-feladatok közzététele settings.json* fájlt. Ezzel biztosítható a Visual Studio jelenjen meg a közzétételi beállításokat, így módosíthatja a webjobs-feladat típusát.
> * Ha telepíti a webjobs-feladat, a futtatási mód később módosítható a folyamatos nem folytonos vagy fordítva, a Visual Studio a újratelepítésekor a új webjobs-feladat az Azure-hoz létre. Ha további ütemezési beállítások módosítására, de hagyja futtatási mód megegyezik, vagy váltás ütemezett és igény szerinti, Visual Studio frissíti a meglévő feladat helyett hozzon létre egy újat.
> 
> 

## <a id="publishsettings"></a>webjobs-feladat közzététele settings.json
Amikor konfigurál egy konzolalkalmazás WebJobs központi telepítés, a Visual Studio telepíti a [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet csomag- és ütemezési információkat tárolja egy *webjobs-feladat közzététele settings.json* fájlt a projektben *tulajdonságok* a WebJobs-projekt mappájából. Íme egy példa, hogy a fájl:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Közvetlenül szerkesztheti a fájlt, és a Visual Studio IntelliSense biztosít. A fájl séma tárolódik [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) nem lehet megtekinteni.  

## <a id="webjobslist"></a>webjobs-list.json
Ha egy WebJobs-kompatibilis projekt webes projektet, a Visual Studio tárolja a webjobs-feladatok projekt nevét egy *webjobs-list.json* fájl a webes projekt *tulajdonságok* mappa. A lista tartalmazhat több WebJobs-projektet, a következő példában látható módon:

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

Közvetlenül szerkesztheti a fájlt, és a Visual Studio IntelliSense biztosít. A fájl séma tárolódik [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) nem lehet megtekinteni.

## <a id="deploy"></a>Webjobs-feladatok projekt telepítése
Webjobs-feladatok projektben lévő nyit meg egy webes projekt automatikusan telepíti a webes projekt. A webes projekt telepítése kapcsolatos információkért lásd: **útmutató útmutató** > **telepítés app** a bal oldali navigációs.

Webjobs-feladatok projekt telepítése önmagában, kattintson a jobb gombbal a projektre a **Megoldáskezelőben** kattintson **Azure webjobs-feladat közzététel...** . 

![Közzététel az Azure webjobs-feladat](./media/websites-dotnet-deploy-webjobs/paw.png)

Egy független webjobs-feladat, ugyanazt a **webhely közzététele** használt webes projektek megjelenik, de kevesebb beállításokkal elérhető módosítása varázsló.
