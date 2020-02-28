---
title: A Application Insights kibocsátási megjegyzései | Microsoft Docs
description: Adja hozzá az üzembe helyezést, vagy készítsen jelölőket a metrikák Explorer diagramjaihoz Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666513"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Jegyzetek a metrikus diagramokon Application Insights

[Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md) diagramok megjegyzései megmutatják, hol telepített egy új buildet vagy más jelentős eseményt. A jegyzetek segítségével egyszerűen megtekintheti, hogy a módosítások hatással voltak-e az alkalmazás teljesítményére. Ezek automatikusan létrehozhatók az Azure- [folyamatok](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Build rendszerével. Megjegyzéseket is létrehozhat, hogy minden olyan eseményt megjelölni, amelyet a PowerShellből hoz létre.

> [!NOTE]
> Ez a cikk az elavult **klasszikus metrikai élményt**mutatja be. A jegyzetek csak a klasszikus felhasználói felület és a **[munkafüzetek](../../azure-monitor/app/usage-workbooks.md)** esetében érhetők el. Az aktuális mérőszámokkal kapcsolatos további tudnivalókért tekintse meg az [Azure Metrikaböngésző speciális funkcióit](../../azure-monitor/platform/metrics-charts.md)ismertető témakört.

![Jegyzetek – példa](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Kiadási jegyzetek az Azure-folyamatok létrehozásával

A kibocsátási megjegyzések az Azure DevOps felhőalapú Azure-folyamatok szolgáltatásának egyik funkciója.

### <a name="install-the-annotations-extension-one-time"></a>A jegyzetek kiterjesztésének (egyszeri) telepítése
A kiadási jegyzetek létrehozásához telepítenie kell a Visual Studio Marketplace-en elérhető számos Azure DevOps-bővítmény egyikét.

1. Jelentkezzen be az [Azure DevOps](https://azure.microsoft.com/services/devops/) -projektbe.
   
1. A Visual Studio Marketplace [kiadási jegyzetek bővítmény](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) lapján válassza ki az Azure DevOps-szervezetet, majd válassza a **telepítés** lehetőséget a bővítmény Azure DevOps-szervezethez való hozzáadásához.
   
   ![Válasszon ki egy Azure DevOps-szervezetet, majd válassza a telepítés lehetőséget.](./media/annotations/1-install.png)
   
Csak egyszer kell telepítenie a bővítményt az Azure DevOps-szervezet számára. Mostantól a szervezet bármely projektje számára is konfigurálhatja a kibocsátási megjegyzéseket.

### <a name="configure-release-annotations"></a>Kiadási jegyzetek konfigurálása

Hozzon létre külön API-kulcsot az Azure-folyamatok egyes kiadási sablonjaihoz.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és nyissa meg az alkalmazást figyelő Application Insights-erőforrást. Ha még nem rendelkezik ilyennel, [hozzon létre egy új Application Insights erőforrást](../../azure-monitor/app/app-insights-overview.md).
   
1. Nyissa meg az **API-hozzáférés** lapot, és másolja a **Application Insights azonosítót**.
   
   ![Az API-hozzáférés területen másolja az alkalmazás AZONOSÍTÓját.](./media/annotations/2-app-id.png)

1. Egy külön böngészőablakban nyissa meg vagy hozza létre az Azure-folyamatok üzembe helyezéseit kezelő kiadási sablont.
   
1. Válassza a **feladat hozzáadása**lehetőséget, majd a menüből válassza a **Application Insights kibocsátási megjegyzése** feladatot.
   
   ![Válassza a feladat hozzáadása lehetőséget, és válassza ki Application Insights kiadási jegyzetet.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > A kiadási jegyzet feladata jelenleg csak a Windows-alapú ügynököket támogatja; nem futtatható Linux, macOS vagy más típusú ügynökön.
   
1. Az **alkalmazás azonosítója**alatt illessze be az **API-hozzáférés** LAPRÓL másolt Application Insights azonosítót.
   
   ![Application Insights-azonosító beillesztése](./media/annotations/4-paste-app-id.png)
   
1. Vissza a Application Insights **API-hozzáférés** ablakban válassza az **API-kulcs létrehozása**lehetőséget. 
   
   ![Az API-hozzáférés lapon válassza az API-kulcs létrehozása lehetőséget.](./media/annotations/5-create-api-key.png)
   
1. Az **API-kulcs létrehozása** ablakban írja be a leírást, válassza a **Megjegyzések írása**lehetőséget, majd válassza a **kulcs létrehozása**lehetőséget. Másolja az új kulcsot.
   
   ![Az API-kulcs létrehozása ablakban írja be a leírást, válassza a megjegyzések írása lehetőséget, majd válassza a kulcs létrehozása lehetőséget.](./media/annotations/6-create-api-key.png)
   
1. A kiadási sablon ablak **változók** lapján válassza a **Hozzáadás** lehetőséget az új API-kulcs változó definíciójának létrehozásához.

1. A **név**mezőben adja meg a `ApiKey`értéket, majd az **érték**alatt illessze be az API- **hozzáférés** lapon másolt API-kulcsot.
   
   ![Az Azure DevOps-változók lapon válassza a Hozzáadás elemet, nevezze el a ApiKey változót, majd illessze be az API-kulcsot az érték mezőbe.](./media/annotations/7-paste-api-key.png)
   
1. A sablon mentéséhez válassza a fő kiadási sablon ablak **Mentés** elemét.

## <a name="view-annotations"></a>Jegyzetek megtekintése
Most, amikor a kiadás sablont használja egy új kiadás üzembe helyezéséhez, a rendszer elküld egy jegyzetet Application Insights. A jegyzetek a **Metrikaböngésző**diagramjain jelennek meg.

A kiadás részleteinek megnyitásához válassza a Megjegyzés jelölőjét (világosszürke nyíl), beleértve a kérelmezőt, a forrás-ellenőrzési ágat, a kiadási folyamatot és a környezetet.

![Válassza ki a kiadási jegyzet jelölőjét.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Egyéni jegyzetek létrehozása a PowerShellből
A GitHubról származó [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell-szkripttel megjegyzéseket hozhat létre bármely olyan folyamatból, amely az Azure DevOps használata nélkül használható. 

1. Készítse el a [CreateReleaseAnnotation. ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)helyi példányát.
   
1. Az előző eljárás lépéseit követve szerezze be a Application Insights azonosítót, és hozzon létre egy API-kulcsot a Application Insights **API-hozzáférés** lapon.
   
1. Hívja meg a PowerShell-parancsfájlt a következő kóddal, és cserélje le a szögletes zárójeles helyőrzőket az értékekre. A `-releaseProperties` megadása nem kötelező. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Módosíthatja a parancsfájlt, például a múltbeli jegyzetek létrehozásához.

## <a name="next-steps"></a>Következő lépések

* [Munkaelemek létrehozása](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatizálás a PowerShell-lel](../../azure-monitor/app/powershell.md)
