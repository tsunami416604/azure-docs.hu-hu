---
title: Fejlesztés és üzembe helyezés a Visual Studio használatával – az Azure webjobs-feladatok
description: Megtudhatja, hogyan fejleszthet és helyezhet üzembe az Azure webjobs-feladatok az Azure App Service-ben a Visual Studio használatával.
services: app-service
documentationcenter: ''
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 64fdb6dceb1ca10e68411f95c310fdd9a2e25202
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444131"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Fejlesztés és üzembe helyezés a Visual Studio használatával – az Azure App Service webjobs-feladatok

## <a name="overview"></a>Áttekintés

Ez a témakör ismerteti a Visual Studióval egy Console Application-projektet egy webalkalmazást az üzembe [App Service-ben](app-service-web-overview.md) , egy [Azure webjobs-feladat](http://go.microsoft.com/fwlink/?LinkId=390226). A webjobs használatával kapcsolatos információkat a [az Azure portal](https://portal.azure.com), lásd: [háttérfeladatok futtatása WebJobs-feladatokkal](web-sites-create-web-jobs.md).

Ha a Visual Studio telepíti a WebJobs-kompatibilis Konzolalkalmazás-projektet, két feladatot hajtja végre:

* Futtatási fájlokat másolja a megfelelő mappát a webalkalmazásban (*App_Data/feladatok/folyamatos* a folyamatos webjobs-feladatokat, *App_Data/feladatok/triggered* ütemezett és igény szerinti WebJobs esetében).
* Beállítja a [Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) feladatokat a webjobs-feladatokat, akik adott időpontban futnak. (Ez nem szükséges a folyamatos webjobs-feladatok.)

A WebJobs-kompatibilis projekt hozzáadja a következőkkel rendelkezik:

* A [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-csomagot.
* A [webjobs-feladat közzététele settings.json](#publishsettings) üzembe helyezési és ütemezési beállításait tartalmazó fájl. 

![Mi kerül egy Konzolalkalmazást, Webjobs-feladatként központi telepítés engedélyezése a bemutató ábra.](./media/websites-dotnet-deploy-webjobs/convert.png)

Ezeket az elemeket ad hozzá egy meglévő Konzolalkalmazás-projektet, vagy egy sablon használatával létrehozhat egy új WebJobs-kompatibilis Konzolalkalmazás-projektet. 

Projekt telepítése Webjobs-feladatként önmagában, vagy hivatkozás egy web projektet, hogy automatikusan helyez üzembe, amikor a webes projekt telepítése. Projektek mutató hivatkozást, a Visual Studio tartalmazza a WebJobs-kompatibilis projekt nevét egy [webjobs-list.json](#webjobslist) fájlt a webalkalmazás-projektben.

![Webjobs-feladat project web projektet összekapcsolja bemutató ábra.](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Előfeltételek

Ha a Visual Studio 2015-öt használ, telepítse a [Azure SDK for .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Ha a Visual Studio 2017-et használ, telepítse a [Azure development workload munkafolyamatának](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a> WebJobs üzembe helyezése meglévő Konzolalkalmazás-projektet engedélyezése

Erre két lehetősége van:

* [Engedélyezze az automatikus üzembe helyezés, a webes projektet](#convertlink).

  Állítsa egy meglévő Konzolalkalmazás-projektet, hogy azt automatikusan telepíti a Webjobs-feladatként webes projektet központi telepítésekor. Használja ezt a beállítást, ha meg szeretné futtatni a webjobs-feladat ugyanazt a webalkalmazást, amelyben a kapcsolódó webalkalmazás futtatása.

* [Központi telepítés anélkül, hogy webes projektet engedélyezése](#convertnolink).

  Konfigurálja a Webjobs-feladatként telepítéséhez önmagában, és nincs hivatkozás a webes projektet egy meglévő Konzolalkalmazás-projektet. Használja ezt a beállítást, ha meg szeretné a webjobs-feladat futtatása webalkalmazásban önmagában, az nincs a webalkalmazás-ban futó webalkalmazás. Érdemes ehhez ahhoz, hogy a Webjobs-erőforrások, függetlenül a webalkalmazás-erőforrások skálázása.

### <a id="convertlink"></a> Webes projektet automatikus WebJobs üzembe helyezésének engedélyezése

1. Kattintson a jobb gombbal a webes projektre a **Megoldáskezelőben**, és kattintson a **Hozzáadás** > **meglévő projektet Azure WebJob feladatként**.
   
    ![Meglévő projekt Azure WebJob feladatként](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    A [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel jelenik meg.
2. Az a **projektnév** legördülő listában válassza a Konzolalkalmazást projekthez legyen hozzáadva egy WebJob feladatként.
   
    ![Válassza ki a projektet Azure WebJob hozzáadása párbeszédpanel](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Végezze el a [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel, és kattintson **OK**. 

### <a id="convertnolink"></a> Anélkül, hogy webes projektet a WebJobs üzembe helyezés engedélyezése
1. Kattintson a jobb gombbal a Konzolalkalmazás-projektben **Megoldáskezelőben**, és kattintson a **Publish as Azure WebJob...** . 
   
    ![Közzététel Azure webjobsként](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    A [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel jelenik meg, a kiválasztott projekthez a **projektnév** mezőbe.
2. Végezze el a [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel, és kattintson **OK**.
   
   A **Publish Web** varázsló jelenik meg.  Ha nem szeretné azonnal közzététele, zárja be a varázslót. A beállítások a megadott a lesznek mentve, ha szeretné, hogy [a projekt telepítése](#deploy).

## <a id="create"></a>WebJobs-kompatibilis új projekt létrehozása
WebJobs-kompatibilis az új projekt létrehozásához használja a Konzolalkalmazás-projekt sablont és WebJobs üzembe helyezés engedélyezése, a [az előző szakaszban](#convert). Alternatív megoldásként használhatja a webjobs-feladatok új webesprojekt-sablon:

* [A webjobs-feladatok – új projekt sablont használ egy független webjobs-feladat](#createnolink)
  
    Hozzon létre egy projektet, és konfigurálja úgy, hogy saját maga, és nincs hivatkozás webes projektet a Webjobs-feladatként üzembe helyezése. Használja ezt a beállítást, ha meg szeretné a webjobs-feladat futtatása webalkalmazásban önmagában, az nincs a webalkalmazás-ban futó webalkalmazás. Érdemes ehhez ahhoz, hogy a Webjobs-erőforrások, függetlenül a webalkalmazás-erőforrások skálázása.
* [A webjobs-feladatok új webesprojekt-sablon használata a webjobs-feladat egy webes projekthez társított](#createlink)
  
    Hozzon létre egy projektet, amely konfigurálva van egy webes projektet a megoldásban üzembe helyezésekor Webjobs-feladatként automatikus központi telepítés. Használja ezt a beállítást, ha meg szeretné futtatni a webjobs-feladat ugyanazt a webalkalmazást, amelyben a kapcsolódó webalkalmazás futtatása.

> [!NOTE]
> A webjobs-feladatok új webesprojekt-sablon automatikusan telepíti a NuGet-csomagok és a kód *Program.cs* számára a [WebJobs SDK-val](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Ha nem szeretné a WebJobs SDK-val, távolítsa el vagy módosítsa a `host.RunAndBlock` utasítás *Program.cs*.
> 
> 

### <a id="createnolink"></a> A webjobs-feladatok – új projekt sablont használ egy független webjobs-feladat
1. Kattintson a **fájl** > **új projekt**, majd a **új projekt** párbeszédpanelen kattintson **felhőalapú**  >   **Az Azure WebJob (.NET-keretrendszer)**.
   
    ![Új projekt párbeszédpanel a webjobs-feladat sablon megjelenítése](./media/websites-dotnet-deploy-webjobs/np.png)
2. Kövesse a fentebb látható [győződjön meg arról, a Konzolalkalmazás egy független a WebJobs-projektet a project](#convertnolink).

### <a id="createlink"></a> A webjobs-feladatok új webesprojekt-sablon használata a webjobs-feladat egy webes projekthez társított
1. Kattintson a jobb gombbal a webes projektre a **Megoldáskezelőben**, és kattintson a **Hozzáadás** > **új Azure webjobs-feladat projekt**.
   
    ![Új Azure webjobs-feladat projekt menüpont](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    A [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel jelenik meg.
2. Végezze el a [adja hozzá a az Azure webjobs-feladat](#configure) párbeszédpanel, és kattintson **OK**.

## <a id="configure"></a>Az Azure Webjobs hozzáadása párbeszédpanel
A **adja hozzá a az Azure webjobs-feladat** párbeszédpanel segítségével adja meg a webjobs-feladat neve és mód beállítása a webjobs-feladat futtatása. 

![Adja hozzá az Azure webjobs-feladat párbeszédpanel](./media/websites-dotnet-deploy-webjobs/aaw2.png)

A mezők a mezők ezen a párbeszédpanelen felel meg a **a webjobs-feladat hozzáadása** az Azure Portal párbeszédpanel. További információkért lásd: [háttérfeladatok futtatása WebJobs-feladatokkal](web-sites-create-web-jobs.md).

> [!NOTE]
> * Parancssori telepítéssel kapcsolatos információkért lásd: [parancssori engedélyezése vagy a folyamatos teljesítés az Azure webjobs-feladatok](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Ha a Webjobs üzembe helyezése, és ezután döntse el, a webjobs-feladat és ismételt üzembe helyezése típusát módosítani szeretné, törölni kell a *webjobs közzététele settings.json* fájlt. Ez teszi Visual Studióban jelenjen meg a közzétételi beállítások, így módosíthatja a webjobs-feladat típusát.
> * A Webjobs üzembe helyezése, és később módosítani a futtatási mód folyamatos nem folytonos vagy fordítva, ha a Visual Studio létrehoz egy új webjobs-feladat Azure újbóli telepítése során. Ha további ütemezési beállítások módosítására, de hagyja futtatási mód azonos, vagy váltás ütemezett és igény szerinti, a Visual Studio a meglévő feladat frissítése helyett hozzon létre egy újat.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
Amikor konfigurál egy Konzolalkalmazást WebJobs üzembe helyezéshez, a Visual Studio telepíti a [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet csomag és az ütemezési információkat a tárolók egy *webjobs-feladat közzététele settings.json*  fájlt a projektben *tulajdonságok* mappájában, a WebJobs-projekt. Íme egy példa, hogy a fájl:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Közvetlenül szerkesztheti ezt a fájlt, és a Visual Studio IntelliSense biztosít. A fájl séma tárolt [ http://schemastore.org ](http://schemastore.org/schemas/json/webjob-publish-settings.json) ott lehet megtekinteni.  

## <a id="webjobslist"></a>webjobs-list.json
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

Közvetlenül szerkesztheti ezt a fájlt, és a Visual Studio IntelliSense biztosít. A fájl séma tárolt [ http://schemastore.org ](http://schemastore.org/schemas/json/webjobs-list.json) ott lehet megtekinteni.

## <a id="deploy"></a>A WebJobs-projekt üzembe helyezése
Egy WebJobs-projektet, amelyet összekapcsolt, hogy webes projektet a webes projekt automatikusan üzembe helyezi. További információ a webes projekt telepítése: **útmutató végigvezeti a** > **üzembe helyezés alkalmazás** a bal oldali navigációs.

A webjobs-feladatok projekt önmagában üzembe helyezéséhez kattintson a jobb gombbal a projektre a **Megoldáskezelőben** kattintson **Publish as Azure WebJob...** . 

![Közzététel Azure webjobsként](./media/websites-dotnet-deploy-webjobs/paw.png)

Egy független a webjobs-feladat, ugyanaz a **Publish Web** használt a webes projektek jelenik meg, de kevesebb konfigurációs lehetőség módosítása varázsló.
