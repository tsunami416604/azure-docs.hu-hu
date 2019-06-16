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
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476172"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Jegyzetek a mérőszám-diagramok, az Application insights szolgáltatásban

A jegyzetek [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md) diagramok megjelenítése, amelybe telepítette egy új létrehozást, vagy egyéb jelentős esemény történt. Vállalnak könnyen látható-e a módosítások volt-e az alkalmazás teljesítményére gyakorolt hatását. Ezek automatikusan létrehozhatók a [Azure fejlesztési és üzemeltetési szolgáltatásokat hozhat létre a rendszer](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Hogy ez a jelző azt minden olyan esemény, például a powershellből létrehozásával is létrehozhat.

> [!NOTE]
> Ez a cikk tükrözi az elavult **klasszikus metrikák élmény**. Jegyzetek rendszer csak a klasszikus tapasztalatok és a jelenleg elérhető  **[munkafüzetek](../../azure-monitor/app/usage-workbooks.md)** . További információ az aktuális metrikák tapasztal, olvassuk [Ez a cikk](../../azure-monitor/platform/metrics-charts.md).

![Jegyzetek – példa](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Kiadási jegyzetek az Azure DevOps-Services-build

Kiadási jegyzetek érhetők el a felhőalapú Azure folyamatok szolgáltatás az Azure DevOps-szolgáltatásokkal.

### <a name="install-the-annotations-extension-one-time"></a>A jegyzetek bővítmény (egyszer) telepítése
Kiadási jegyzetek lehessen kell telepíteni a Visual Studio-piactéren elérhető számos Azure DevOps-szolgáltatásokkal bővítmény.

1. Jelentkezzen be a [Azure DevOps-szolgáltatásokkal](https://azure.microsoft.com/services/devops/) projekt.
2. A Visual Studio-piactéren [a kiadási jegyzetek bővítmény beszerzése](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations), és adja hozzá az Azure DevOps-szolgáltatásokkal szervezet.

![Válassza ki az Azure DevOps-szervezetekben, majd telepítse.](./media/annotations/1-install.png)

Csak akkor kell elvégeznie egyszer Azure DevOps-szolgáltatásokkal szervezete számára. Kiadási jegyzetek is lehet konfigurálni a szervezet minden projekt.

### <a name="configure-release-annotations"></a>Kiadási jegyzetek konfigurálása

Kell külön API-kulcs az egyes Azure DevOps-szolgáltatásokkal a kiadásban sablonok beszerzése.

1. Jelentkezzen be a [Microsoft Azure-portálon](https://portal.azure.com) , és nyissa meg az Application Insights-erőforrást, amely az alkalmazás figyelésére. (Vagy [hozzon létre egyet most](../../azure-monitor/app/app-insights-overview.md), ha még nem tette,.)
2. Nyissa meg a **API-hozzáférés** fülre, és másolja a **Application Insights azonosító**.
   
    ![A Portal.Azure.com címen nyissa meg az Application Insights-erőforrást, és kattintson a beállítások. Nyissa meg az API-hozzáférés. Az Alkalmazásazonosító másolása](./media/annotations/2-app-id.png)

4. Egy külön böngészőablakot nyissa meg a (vagy hozzon létre) a kiadási sablon, amely felügyeli az üzemelő példányok az Azure DevOps-szolgáltatásokkal.
   
    Adjon hozzá egy feladatot, és válassza ki az Application Insights kiadási jegyzet feladat a menüből.

   ![Kattintson a pluszjelre kattintva adja hozzá a feladat, és válassza ki az Application Insights kiadási jegyzet. Illessze be az Application Insights-azonosítót.](./media/annotations/3-add-task.png)

    Illessze be a **Alkalmazásazonosító** , az API-hozzáférés lapon fájlból kimásolt.
   
    ![Illessze be az Application Insights-Azonosítót](./media/annotations/4-paste-app-id.png)

5. Az Azure ablakban hozzon létre egy új API-kulcsot, és igénybe vehet egy példányát.
   
    ![Az API-hozzáférés az Azure ablakban lapon kattintson az API-kulcs létrehozása.](./media/annotations/5-create-api-key.png)

    ![A create API key lapon adja meg a megjegyzést, ellenőrizze az írási jegyzetek, majd kattintson a kulcs létrehozása. Másolja ki az új kulccsal.](./media/annotations/6-create-api-key.png)

6. Kiadási sablon a konfigurációs lap megnyitásához.
   
    Hozzon létre a változó definícióját `ApiKey`.
   
    Illessze be a apikey tulajdonsággal végzett tesztelése változó definícióját az API-kulcsát.
   
    ![Az Azure DevOps-szolgáltatásokkal ablakban válassza ki a változó lapot, és kattintson a Hozzáadás elemre. Állítsa be a nevét, apikey tulajdonsággal végzett tesztelése, és az érték illessze be az Ön hozza létre a kulcsot, és kattintson a lakat ikonra.](./media/annotations/7-paste-api-key.png)
1. Végül **mentése** a kiadási folyamathoz.


## <a name="view-annotations"></a>Megjegyzések megjelenítése
Most, amikor a kiadási sablon használatával telepít egy új kiadás, jegyzet küld az Application Insightsba. A jegyzetek a Metrikaböngésző diagram fog megjelenni.

Kattintson a bármely jelölőt (világos szürke nyíl), nyissa meg a részleteket a kiadást, beleértve a kérelmező, forráságat vezérlő, kiadási folyamatot, a környezet és más.

![Kattintson bármely kiadási jelölőt.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Egyéni jegyzetek létrehozása PowerShellben
Jegyzetek bármely (nélkül az Azure DevOps Services) folyamat során is létrehozhat. 


1. Helyi másolatot készít a [Powershell-parancsfájlt a Githubról](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Az alkalmazás Azonosítójának lekéréséhez és API-kulcs létrehozása az API-hozzáférés lapról.

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

* [Munkaelem létrehozása](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatizálás a PowerShell-lel](../../azure-monitor/app/powershell.md)
