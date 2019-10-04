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
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604546"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Jegyzetek a mérőszám-diagramok, az Application insights szolgáltatásban

A jegyzetek [Metrikaböngésző](../../azure-monitor/app/metrics-explorer.md) diagramok megjelenítése, amelybe telepítette egy új létrehozást, vagy egyéb jelentős események. Jegyzetek megkönnyítik a talál-e a módosítások volt-e az alkalmazás teljesítményére gyakorolt hatását. Ezek automatikusan létrehozhatók a [Azure folyamatok](https://docs.microsoft.com/azure/devops/pipelines/tasks/) rendszert hozhat létre. Hogy ez a jelző azt minden olyan esemény, például a powershellből létrehozásával is létrehozhat.

> [!NOTE]
> Ez a cikk tükrözi az elavult **klasszikus metrikák élmény**. Jegyzetek rendszer csak a klasszikus tapasztalatok és a jelenleg elérhető  **[munkafüzetek](../../azure-monitor/app/usage-workbooks.md)** . Aktuális metrikák funkciókkal kapcsolatos további tudnivalókért lásd: [speciális szolgáltatások az Azure Metrikaböngésző](../../azure-monitor/platform/metrics-charts.md).

![Jegyzetek – példa](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Kiadási jegyzetek az Azure-folyamatokat hozhat létre

Kiadási jegyzetek érhetők el a felhőalapú Azure folyamatok szolgáltatás az Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>A jegyzetek bővítmény (egyszer) telepítése
Kiadási jegyzetek lehessen kell telepíteni a Visual Studio-piactéren elérhető számos Azure DevOps-bővítmény.

1. Jelentkezzen be a [Azure DevOps](https://azure.microsoft.com/services/devops/) projekt.
   
1. A Visual Studio-piactéren a [kiadási jegyzetek bővítmény](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) lapon, majd válassza ki és válassza ki az Azure DevOps-szervezet **telepítése** a bővítmény hozzáadása az Azure DevOps-szervezethez.
   
   ![Válassza ki az Azure DevOps-szervezetekben, és válassza ki a telepítés.](./media/annotations/1-install.png)
   
Csak ki kell telepíteni a bővítményt, az Azure DevOps-szervezetben. Kiadási jegyzetek minden projekt most konfigurálhatja a szervezetben.

### <a name="configure-release-annotations"></a>Kiadási jegyzetek konfigurálása

Hozzon létre egy külön API-kulcs Azure folyamatok a kiadásban sablonok.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és nyissa meg az Application Insights-erőforrást, amely az alkalmazás figyelésére. Ha még nincs fiókja, vagy [hozzon létre egy új Application Insights-erőforrást](../../azure-monitor/app/app-insights-overview.md).
   
1. Nyissa meg a **API-hozzáférés** fülre, és másolja a **Application Insights azonosító**.
   
   ![API-hozzáférés alatt másolja az alkalmazás azonosítóját.](./media/annotations/2-app-id.png)

1. Nyissa meg egy külön böngészőablakot, vagy a kiadási sablon, amely kezeli az Azure-folyamatok üzemelő példányok létrehozása.
   
1. Válassza ki **feladat hozzáadása**, majd válassza ki a **Application Insights kiadási jegyzet** feladat a menüből.
   
   ![Válassza ki a feladat hozzáadása, és válassza ki az Application Insights kiadási jegyzet.](./media/annotations/3-add-task.png)
   
1. A **Alkalmazásazonosító**, illessze be az Application Insights Azonosítót, amelyet másolt a **API-hozzáférés** fülre.
   
   ![Illessze be az Application Insights-Azonosítót](./media/annotations/4-paste-app-id.png)
   
1. Vissza az Application Insights **API-hozzáférés** ablakban válassza **API-kulcs létrehozása**. 
   
   ![Az API-hozzáférés lapon válassza ki az API-kulcs létrehozása.](./media/annotations/5-create-api-key.png)
   
1. Az a **API-kulcs létrehozása** kiválasztása ablakban írja be egy leírást, **jegyzetek írása**, majd válassza ki **kulcs létrehozása**. Másolja ki az új kulccsal.
   
   ![A fő API létrehozása ablakban adjon meg egy leírást, írási jegyzetek, majd válassza ki és kulcs létrehozása.](./media/annotations/6-create-api-key.png)
   
1. Kiadási sablon ablakában a a **változók** lapon jelölje be **Hozzáadás** a változó definícióját az új API-kulcs létrehozása.

1. Alatt **neve**, adja meg `ApiKey`, majd a **érték**, illessze be a kimásolt API-kulcsot a **API-hozzáférés** fülre.
   
   ![Az Azure DevOps változók lapon válassza a Hozzáadás, apikey tulajdonsággal végzett tesztelése, változó neve, és illessze be az API-kulcs értéke alapján.](./media/annotations/7-paste-api-key.png)
   
1. Válassza ki **mentése** a fő kiadási sablon ablak a sablon mentéséhez.

## <a name="view-annotations"></a>Megjegyzések megjelenítése
Ezután, amikor a kiadási sablon használatával telepít egy új kiadás, jegyzet küld az Application Insights. A jegyzetek jelenik meg a diagramok **Metrikaböngésző**.

Válassza ki bármelyik jelölőt (világos szürke nyíl), nyissa meg a kiadás kérelmező, a vezérlő forráságat, a kibocsátási folyamatok és a környezet adatait.

![Válassza ki a kiadási jelölőt.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Egyéni jegyzetek létrehozása PowerShellben
Használhatja a [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) GitHub jegyzetek valamilyen folyamat használata az Azure DevOps nélkül, például a PowerShell-parancsprogramot. 

1. Egy helyi példányának [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Az előző eljárás a lépéseket követve az Application Insights Azonosítójának lekéréséhez és a egy API-kulcs létrehozása az Application insights **API-hozzáférés** fülre.
   
1. Hívja meg a PowerShell-parancsfájlt a következő kódra, a szög zárójeles helyőrzőket cserélje le az értékeket. A `-releaseProperties` megadása nem kötelező. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

A szkript, például az elmúlt jegyzetek módosíthatja.

## <a name="next-steps"></a>További lépések

* [Munkaelem létrehozása](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatizálás a PowerShell-lel](../../azure-monitor/app/powershell.md)
