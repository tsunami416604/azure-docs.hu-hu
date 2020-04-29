---
title: A Application Insights kibocsátási megjegyzései | Microsoft Docs
description: Adja hozzá az üzembe helyezést, vagy készítsen jelölőket a metrikák Explorer diagramjaihoz Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 0ad773ca6a7102ac718d43dfbbf6a4f834e681a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010719"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Jegyzetek a metrikus diagramokon Application Insights

A jegyzetek megmutatják, hol telepített egy új buildet vagy más jelentős eseményt. A jegyzetek segítségével egyszerűen megtekintheti, hogy a módosítások hatással voltak-e az alkalmazás teljesítményére. Ezek automatikusan létrehozhatók az Azure- [folyamatok](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Build rendszerével. Megjegyzéseket is létrehozhat, hogy minden olyan eseményt megjelölni, amelyet a PowerShellből hoz létre.

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

1. A **név**mezőben adja `ApiKey`meg az értéket, majd az **érték**alatt illessze be a másolt API-kulcsot az **API-hozzáférés** lapról.
   
   ![Az Azure DevOps-változók lapon válassza a Hozzáadás elemet, nevezze el a ApiKey változót, majd illessze be az API-kulcsot az érték mezőbe.](./media/annotations/7-paste-api-key.png)
   
1. A sablon mentéséhez válassza a fő kiadási sablon ablak **Mentés** elemét.

## <a name="view-annotations"></a>Jegyzetek megtekintése


   > [!NOTE]
   > A kibocsátási megjegyzések jelenleg nem érhetők el a Application Insights metrika ablaktábláján.

Most, amikor a kiadás sablont használja egy új kiadás üzembe helyezéséhez, a rendszer elküld egy jegyzetet Application Insights. A jegyzetek a következő helyszíneken tekinthetők meg:

A használat ablaktáblán lehetősége van a kiadási jegyzetek manuális létrehozására is:

![Képernyőkép – a sávdiagramok száma egy adott időszakon belül megjelenített felhasználói látogatások számával. A kiadási jegyzetek zöld pipaként jelennek meg a diagram felett, amely azt jelzi, hogy a kiadás milyen időpontban történt](./media/annotations/usage-pane.png)

Minden olyan log-alapú munkafüzet-lekérdezésben, amelyben a vizualizáció megjeleníti az időt az x tengely mentén.

![Képernyőfelvétel a munkafüzetek panelről idősorozatos naplózási lekérdezéssel megjelenített megjegyzésekkel](./media/annotations/workbooks-annotations.png)

Ha engedélyezni szeretné a jegyzeteket a munkafüzetben, ugorjon a **Speciális beállítások** elemre, és válassza a **jegyzetek megjelenítése**lehetőséget.

![Képernyőkép a speciális beállítások menüjéről, a következő szavakkal: a beállítás melletti jelölőnégyzetekkel Kiemelt jegyzeteket jelenít meg az engedélyezéshez.](./media/annotations/workbook-show-annotations.png)

A kiadás részleteinek megnyitásához válassza a Megjegyzés jelölőjét, beleértve a kérelmezőt, a verziókövetés ágat, a kiadási folyamatot és a környezetet.

## <a name="create-custom-annotations-from-powershell"></a>Egyéni jegyzetek létrehozása a PowerShellből
A GitHubról származó [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell-szkripttel megjegyzéseket hozhat létre bármely olyan folyamatból, amely az Azure DevOps használata nélkül használható. 

1. Készítse el a [CreateReleaseAnnotation. ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)helyi példányát.
   
1. Az előző eljárás lépéseit követve szerezze be a Application Insights azonosítót, és hozzon létre egy API-kulcsot a Application Insights **API-hozzáférés** lapon.
   
1. Hívja meg a PowerShell-parancsfájlt a következő kóddal, és cserélje le a szögletes zárójeles helyőrzőket az értékekre. A `-releaseProperties` nem kötelező. 
   
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

## <a name="next-steps"></a>További lépések

* [Munkaelemek létrehozása](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatizálás a PowerShell használatával](../../azure-monitor/app/powershell.md)
