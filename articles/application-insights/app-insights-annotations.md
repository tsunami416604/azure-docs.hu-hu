---
title: Kiadási jegyzetek az Application Insights |} A Microsoft Docs
description: Központi telepítés hozzáadása, vagy létrehozhatja a mérőszámdiagramok explorer az Application Insights jelölőket.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: 056716e243980f0a8aadc1ff7e9b8776809ad88e
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036397"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Jegyzetek a mérőszám-diagramok, az Application insights szolgáltatásban
A jegyzetek [Metrikaböngésző](app-insights-metrics-explorer.md) diagramok megjelenítése, amelybe telepítette egy új létrehozást, vagy egyéb jelentős esemény történt. Vállalnak könnyen látható-e a módosítások volt-e az alkalmazás teljesítményére gyakorolt hatását. Ezek automatikusan létrehozhatók a [Visual Studio Team Services build rendszer](https://docs.microsoft.com/vsts/pipelines/tasks/). Is létrehozhat, hogy ez a jelző azt tetszés szerint bármilyen eseményre [hozza létre őket a PowerShell](#create-annotations-from-powershell).

![Jegyzetek kiszolgálói válaszidő látható korrelációkereséssel – példa](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Kiadási jegyzetek a VSTS-build

Kiadási jegyzetek érhetők el a felhő alapú build és kiadás tartalmazza a Visual Studio Team Services szolgáltatást. 

### <a name="install-the-annotations-extension-one-time"></a>A jegyzetek bővítmény (egyszer) telepítése
Kiadási jegyzetek lehessen kell telepíteni az egyik rendelkezésre álló számos Team Services bővítmény a Visual Studio-piactéren.

1. Jelentkezzen be a [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) projekt.
2. A Visual Studio-piactéren [a kiadási jegyzetek bővítmény beszerzése](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations), és adja hozzá a Team Services-fiók.

![At leggyakoribb jobb oldalán a Team Services weblapot, nyissa meg a piactéren. Válassza ki a vizuális Team Services és a Build és kiadás, válassza a több megjelenítése.](./media/app-insights-annotations/10.png)

Csak akkor kell elvégeznie egyszer a Visual Studio Team Services-fiókjához. Kiadási jegyzetek mostantól konfigurálható úgy, hogy minden projekt-fiókjában található. 

### <a name="configure-release-annotations"></a>Kiadási jegyzetek konfigurálása

Szeretne egy különálló, minden VSTS-kiadási sablon API-kulcs beszerzése.

1. Jelentkezzen be a [a Microsoft Azure Portal](https://portal.azure.com) , és nyissa meg az Application Insights-erőforrást, amely az alkalmazás figyelésére. (Vagy [hozzon létre egyet most](app-insights-overview.md), ha még nem tette,.)
2. Nyissa meg **API-hozzáférés**, **Application Insights azonosító**.
   
    ![A Portal.Azure.com címen nyissa meg az Application Insights-erőforrást, és kattintson a beállítások. Nyissa meg az API-hozzáférés. Az Alkalmazásazonosító másolása](./media/app-insights-annotations/20.png)

4. Egy külön böngészőablakot nyissa meg a (vagy hozzon létre), amely kezeli a központi telepítések a Visual Studio Team Services a kiadási sablon. 
   
    Adjon hozzá egy feladatot, és válassza ki az Application Insights kiadási jegyzet feladat a menüből.
   
    Illessze be a **alkalmazásazonosító** , az API-hozzáférés panelen fájlból kimásolt.
   
    ![A Visual Studio Team Services kiadási nyissa meg, válassza ki a kiadási definíció, és válassza a Szerkesztés. Feladat hozzáadása gombra, és válassza ki az Application Insights kiadási jegyzet. Illessze be az Application Insights-azonosítót.](./media/app-insights-annotations/30.png)
4. Állítsa be a **apikey tulajdonsággal végzett tesztelése** mezőt egy változó `$(ApiKey)`.

5. Az Azure ablakban hozzon létre egy új API-kulcsot, és igénybe vehet egy példányát.
   
    ![Az API-hozzáférés panelen az Azure ablakban kattintson az API-kulcs létrehozása. Adja meg a megjegyzést, ellenőrizze az írási jegyzetek, és kattintson a kulcs létrehozása. Másolja ki az új kulccsal.](./media/app-insights-annotations/40.png)

6. Kiadási sablon a konfigurációs lap megnyitásához.
   
    Hozzon létre a változó definícióját `ApiKey`.
   
    Illessze be a apikey tulajdonsággal végzett tesztelése változó definícióját az API-kulcsát.
   
    ![A Team Services ablakban válassza ki a konfigurációs lapon, és kattintson a változó hozzáadása. Állítsa be a nevét, apikey tulajdonsággal végzett tesztelése, és az érték, illessze be az imént létrehozott kulcsot, és kattintson a lakat ikonra.](./media/app-insights-annotations/50.png)
7. Végül **mentése** a kiadási definíció.


## <a name="view-annotations"></a>Megjegyzések megjelenítése
Most, amikor a kiadási sablon használatával telepít egy új kiadás, jegyzet küld az Application Insightsba. A jegyzetek a Metrikaböngésző diagram fog megjelenni.

Kattintson bármely jelölőt, nyissa meg a kiadott, beleértve a kérelmező, forráságat vezérlő részleteit a, a kiadási definíció, a környezet és más.

![Kattintson bármely kiadási jelölőt.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Egyéni jegyzetek létrehozása PowerShellben
Jegyzetek (használata a VS Team System) nélkül szeretné valamilyen folyamat is létrehozhat. 


1. Helyi másolatot készít a [Powershell-parancsfájlt a Githubról](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Az alkalmazás Azonosítójának lekéréséhez és API-kulcs létrehozása az API-hozzáférés panelen.

3. Hívja a parancsfájl ehhez hasonló:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Módosítsa a parancsfájlt, például az elmúlt jegyzetek könnyebbé vált a.

## <a name="next-steps"></a>További lépések

* [Munkaelem létrehozása](app-insights-diagnostic-search.md#create-work-item)
* [Automatizálás a PowerShell-lel](app-insights-powershell.md)
