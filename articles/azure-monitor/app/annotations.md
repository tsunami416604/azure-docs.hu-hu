---
title: Az Application Insights jegyzeteinek kiadása | Microsoft dokumentumok
description: Üzembe helyezési vagy buildjelölők hozzáadása a metrikák kezelői diagramokhoz az Application Insightsban.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 0ad773ca6a7102ac718d43dfbbf6a4f834e681a0
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010719"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Az Application Insights metrikadiagramjainak jegyzetei

A jegyzetek azt mutatják, hogy hol telepített egy új buildet vagy más jelentős eseményeket. A jegyzetek megkönnyítik annak megtekintését, hogy a módosítások hatással voltak-e az alkalmazás teljesítményére. Ezek automatikusan létre az [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) build rendszer. A PowerShellből való létrehozásukhoz jegyzeteket is létrehozhat, amelyek megjelölhetik a kívánt eseményeket.

## <a name="release-annotations-with-azure-pipelines-build"></a>Az Azure Pipelines buildelésével jegyzetek et ad ki

A kiadási jegyzetek az Azure DevOps felhőalapú Azure Pipelines szolgáltatásának egyik szolgáltatása.

### <a name="install-the-annotations-extension-one-time"></a>A Jegyzetek bővítmény telepítése (egyszer)

Ahhoz, hogy kiadási jegyzeteket hozhat létre, telepítenie kell a Visual Studio Piactéren elérhető számos Azure DevOps-bővítmény egyikét.

1. Jelentkezzen be az [Azure DevOps-projektbe.](https://azure.microsoft.com/services/devops/)
   
1. A Visual Studio [Piactér kiadási jegyzetbővítmény-bővítménylapján](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) válassza ki az Azure DevOps-szervezetet, majd a **Telepítés** lehetőséget választva adja hozzá a bővítményt az Azure DevOps-szervezethez.
   
   ![Válasszon ki egy Azure DevOps-szervezetet, majd válassza a Telepítés lehetőséget.](./media/annotations/1-install.png)
   
Csak egyszer kell telepítenie a bővítményt az Azure DevOps-szervezethez. Most már beállíthatja a kiadási jegyzeteket a szervezet bármely projektjéhez.

### <a name="configure-release-annotations"></a>Kiadási jegyzetek konfigurálása

Hozzon létre egy külön API-kulcsot az Azure Pipelines kiadási sablonjaihoz.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és nyissa meg az application insights-erőforrást, amely az alkalmazást figyeli. Vagy ha még nem rendelkezik ilyen, [hozzon létre egy új Application Insights-erőforrást.](../../azure-monitor/app/app-insights-overview.md)
   
1. Nyissa meg az **API Access** lapot, és másolja az **Application Insights-azonosítót.**
   
   ![Az API Access alatt másolja az alkalmazásazonosítót.](./media/annotations/2-app-id.png)

1. Egy külön böngészőablakban nyissa meg vagy hozza létre az Azure-folyamatok központi telepítéseit kezelő kiadási sablont.
   
1. Válassza **a Feladat hozzáadása**lehetőséget, majd a menüből válassza az Application Insights Release **Annotation (Alkalmazáselemzési közlemény)** lehetőséget.
   
   ![Válassza a Feladat hozzáadása lehetőséget, majd az Application Insights release annotation (Alkalmazáselemzési közlemény) lehetőséget.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > A Felszabadítási jegyzet feladat jelenleg csak windows-alapú ügynököket támogat; nem fog futni Linuxon, macOS-en vagy más típusú ügynökökön.
   
1. Az **Alkalmazásazonosító**csoportban illessze be az **API Access** lapról másolt Application Insights-azonosítót.
   
   ![Az Application Insights-azonosító beillesztése](./media/annotations/4-paste-app-id.png)
   
1. Az Application Insights **API Access** ablakában válassza az **API-kulcs létrehozása**lehetőséget. 
   
   ![Az API Access lapon válassza az API-kulcs létrehozása lehetőséget.](./media/annotations/5-create-api-key.png)
   
1. Az **API-kulcs létrehozása** ablakban írjon be egy leírást, válassza **a Jegyzetek írása**lehetőséget, majd a Kulcs létrehozása **lehetőséget.** Másolja az új kulcsot.
   
   ![Az API-kulcs létrehozása ablakban írjon be egy leírást, válassza a Jegyzetek írása lehetőséget, majd válassza a Kulcs létrehozása lehetőséget.](./media/annotations/6-create-api-key.png)
   
1. A kiadási sablon **ablakváltozók** lapján válassza a **Hozzáadás** lehetőséget az új API-kulcs változódefiníciójának létrehozásához.

1. A **Név** `ApiKey`mezőbe írja be a beírt értéket, és az **Érték**csoportban illessze be az **API Access** lapról másolt API-kulcsot.
   
   ![Az Azure DevOps-változók lapon válassza a Hozzáadás, a változó ApiKey nevet, és illessze be az API-kulcsot az Érték csoportban.](./media/annotations/7-paste-api-key.png)
   
1. A sablon mentéséhez válassza a **Mentés** gombot a fő kiadási sablon ablakában.

## <a name="view-annotations"></a>A jegyzetek megtekintése


   > [!NOTE]
   > A kiadási jegyzetek jelenleg nem érhetők el az Application Insights Metrikák ablaktáblájában

Most antól a rendszer a kiadási sablon használatával telepít egy új kiadást, a rendszer egy olyan notnotant küld az Application Insightsnak, amelyet a rendszer a kiadási sablon használatával küld. A jegyzetek a következő helyeken tekinthetők meg:

A használati ablaktábla, ahol manuálisan is létrehozhat kibocsátási jegyzeteket:

![Képernyőkép a sávdiagramról, amelyen a felhasználói látogatások száma egy óra alatt jelenik meg. A kiadási megjegyzések zöld pipaként jelennek meg a diagram felett, jelezve a kiadás időpontjának pillanatát](./media/annotations/usage-pane.png)

Minden olyan naplóalapú munkafüzet-lekérdezésben, ahol a vizualizáció az x tengely mentén jeleníti meg az időt.

![Képernyőkép a munkafüzetek ablaktábláról az idősorozat-naplóalapú lekérdezéssel, amelyen jegyzetek jelennek meg](./media/annotations/workbooks-annotations.png)

A munkafüzetben lévő jegyzetek engedélyezéséhez válassza a **Speciális beállítások lehetőséget,** és válassza **a Jegyzetek megjelenítése**lehetőséget.

![Képernyőkép a Speciális beállítások menüről, amelyen a szavak a beállítást jelzik, és a beállítás mellett egy pipa jelenik meg.](./media/annotations/workbook-show-annotations.png)

Válassza ki bármelyik jegyzetjelölőt a kiadás részleteinek megnyitásához, beleértve a kérelmezőt, a forrásvezérlő ágat, a kiadási folyamatot és a környezetet.

## <a name="create-custom-annotations-from-powershell"></a>Egyéni jegyzetek létrehozása a PowerShellből
A GitHub [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell-parancsfájljával az Azure DevOps használata nélkül hozhat létre jegyzeteket bármely folyamatból. 

1. Készítsen helyi másolatot a [CreateReleaseAnnotation.ps1 programról.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
   
1. Az előző eljárás lépéseivel lejuthat az Application Insights-azonosítóról, és létrehozhat egy API-kulcsot az Application Insights **API Access** lapról.
   
1. Hívja meg a PowerShell-parancsfájlt a következő kóddal, és cserélje le a szögletes helyőrzőket az ön értékeire. A `-releaseProperties` nem kötelező. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Módosíthatja a parancsfájlt, például a múlthoz jegyzeteket hozhat létre.

## <a name="next-steps"></a>További lépések

* [Munkaelemek létrehozása](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatizálás a PowerShell használatával](../../azure-monitor/app/powershell.md)
