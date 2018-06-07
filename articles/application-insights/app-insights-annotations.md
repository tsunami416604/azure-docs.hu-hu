---
title: A kiadási jegyzetek az Application Insights |} Microsoft Docs
description: Központi telepítés hozzáadása, vagy összeállíthatja a metrikák explorer diagramokat az Application Insightsban való jelölőket.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: a479fa553d64f3820ae8513353484e72b57d30e4
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807798"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Az Application Insightsban metrika diagramok jegyzetek
A jegyzetek [Metrikaböngésző](app-insights-metrics-explorer.md) diagramok megjelenítése, amelyen rendszerbe van állítva egy új buildverziót, vagy más jelentős esemény történt. Akkor könnyen látható, hogy a módosítások volt-e az alkalmazás teljesítményére hatással. Ezek automatikusan létrehozhatók a [Visual Studio Team Services rendszer build](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs). Jegyzeteket, tetszés szerint mindenképpen jelzőt is létrehozhat [hozza létre őket a Powershellből](#create-annotations-from-powershell).

![Kiszolgáló válaszideje látható korrelációban állnak a jegyzetek – példa](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Kiadási jegyzetek a VSTS-build

Kiadási jegyzetek a felhő alapú build szolgáltatása, és felszabadíthatja a Visual Studio Team Services szolgáltatás. 

### <a name="install-the-annotations-extension-one-time"></a>A jegyzetek (egyszer) bővítményének telepítése
Nem fogja tudni kiadási jegyzetek írására, lesz szüksége a Visual Studio piactéren elérhető számos Team Service kiterjesztések telepítéséhez.

1. Jelentkezzen be a [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) projekt.
2. A Visual Studio piactéren [a kiadási jegyzetek bővítmény](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations), és adja hozzá a Team Services-fiókhoz.

![At Team Services weblap, nyissa meg piactér jobb felső. Jelölje ki Visual Team Services és a Build és verziószám, válassza lásd: több.](./media/app-insights-annotations/10.png)

Csak szüksége erre egyszer a Visual Studio Team Services-fiókját. Kiadási jegyzetek a projektre a fiókját most konfigurálhatók. 

### <a name="configure-release-annotations"></a>Kiadási jegyzetek konfigurálása

Minden VSTS kiadási sablon külön API-kulcs csatlakoztatni kell.

1. Jelentkezzen be a [Microsoft Azure portál](https://portal.azure.com) , és nyissa meg az Application Insights-erőforrást, amely az alkalmazás figyeli. (Vagy [létrehozhat egy tárhelyet](app-insights-overview.md), ha még nem tette meg még.)
2. Nyissa meg **API-hozzáférés**, **Application Insights azonosító**.
   
    ![A portal.azure.com nyissa meg az Application Insights-erőforrást, majd válassza a beállítások. Nyissa meg az API-hozzáférést. Másolja át az Alkalmazásazonosítót](./media/app-insights-annotations/20.png)

4. Egy külön böngészőablakban nyissa meg a (vagy hozzon létre) a kiadási sablon, amely a központi telepítések a Visual Studio Team Services kezeli. 
   
    Adjon hozzá egy feladatot, és válassza ki az Application Insights kiadási Megjegyzés feladatot a menüből.
   
    Beillesztés a **alkalmazásazonosító** API-hozzáférés paneljén másolt.
   
    ![A Visual Studio Team Services nyissa meg a kiadási válassza ki a kiadási definícióját, kattintson a Szerkesztés. A feladat hozzáadása gombra, és válassza ki az Application Insights kiadási megjegyzés. Illessze be az Application Insights azonosítóját.](./media/app-insights-annotations/30.png)
4. Állítsa be a **APIKey** mező egy változóhoz `$(ApiKey)`.

5. Az Azure ablakban hozzon létre egy új API-kulcsot, és igénybe vehet egy példányát.
   
    ![Az API-hozzáférés az Azure ablak panelen kattintson az API-kulcs létrehozása. Adja meg a megjegyzést, ellenőrizze az írási jegyzeteket és kattintson a kulcs létrehozása. Az új kulcs másolása.](./media/app-insights-annotations/40.png)

6. Nyissa meg a kiadási sablon konfiguráció lapon.
   
    Hozzon létre egy változó definíciója `ApiKey`.
   
    Illessze be az API-kulcs a ApiKey változó definícióját.
   
    ![A Team Services ablakban jelölje ki a konfiguráció lapon, és kattintson a változó hozzáadása. Állítsa be a nevét, ApiKey és az értéke, illessze be az imént létrehozott kulcs, és kattintson a lakat ikon.](./media/app-insights-annotations/50.png)
7. Végezetül **mentése** kiadás meghatározása.


## <a name="view-annotations"></a>Jegyzetek megtekintése
Most a kiadás sablon telepítése új kiadását használja, a jegyzet az Application insights kapnak. A jegyzetek a Metrikaböngészőben diagramok jelenik meg.

Kattintson a bármely jegyzet jelölő nyissa meg a kiadást, beleértve a kérelmező, forrás vezérlő fiókirodai részletei, meghatározására, a környezet és több kiadási.

![Kattintson a kiadási Megjegyzés jelölő.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Hozzon létre egyéni jegyzetek a Powershellből
Jegyzetek e folyamat (nélkül használja a Visual STUDIO Team System) is létrehozhat. 


1. Helyi másolatot készít a [Powershell-parancsfájl a Githubról](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Az Alkalmazásazonosító beszerzése és API-kulcs létrehozása az API-hozzáférés paneljén.

3. A parancsfájl ilyen hívása:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Nem módosítja a parancsfájlt, például az előző jegyzetek írására.

## <a name="next-steps"></a>További lépések

* [Munkaelemek létrehozása](app-insights-diagnostic-search.md#create-work-item)
* [Automatizálása a PowerShell](app-insights-powershell.md)
