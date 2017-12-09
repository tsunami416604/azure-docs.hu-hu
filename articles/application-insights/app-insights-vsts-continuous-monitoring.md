---
title: "Folyamatos figyelés a DevOps kiadási folyamatának VSTS és Azure Application Insights |} Microsoft Docs"
description: "Ez a gyors a folyamatos figyelés az Application Insights beállítása"
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: article
manager: carmonm
ms.openlocfilehash: 5bfbdd0033f966422a84071a694845627827f016
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Folyamatos figyelés felvétele a kiadási folyamat

A Visual Studio Team Services (VSTS) integrálódik az Azure Application Insights folyamatos figyelés a szoftver fejlesztési életciklus során a DevOps kiadási-feldolgozási folyamat engedélyezéséhez. 

VSTS mostantól támogatja a folyamatos figyelését, amelyek segítségével a kiadási folyamatok figyelési adatok az Application insights szolgáltatással és más Azure-erőforrások építhessék be. Az Application Insights riasztást észlelésekor a központi telepítés gated maradjanak, vagy állítható vissza, amíg a riasztás oldódik meg. Ha az ellenőrzés átadni, központi telepítések lépne automatikusan tesztből egészen éles kézi beavatkozás nélkül. 

## <a name="configure-continuous-monitoring"></a>Folyamatos figyelés konfigurálása

1. Válasszon ki egy meglévő VSTS projektet.

2. Vigye **összeállítása, és felszabadíthatja a** > Válassza ki **kiadásokban** > kattintson a **plusz jelre** > **létrehozás kiadás definition** > Keressen **figyelési** > **folyamatos figyelését, az Azure App Service központi telepítése.**

   ![Új VSTS kiadás meghatározása](.\media\app-insights-continuous-monitoring\001.png)

3. Kattintson a **alkalmazni.**

4. A piros felkiáltójel mellett válassza ki a szöveget a kék **környezettel kapcsolatos feladatok megtekintéséhez.**

   ![Környezet feladatok megtekintése](.\media\app-insights-continuous-monitoring\002.png)

   Egy konfigurációs mezőben jelenik meg, használja a következő táblázat a beviteli mezők kitöltéséhez.

    | Paraméter        | Érték |
   | ------------- |:-----|
   | **A környezeti neve**      | A kiadási definition környezet leíró nevet |
   | **Azure-előfizetés** | Legördülő lista bármely Azure-előfizetések a VSTS-fiókhoz kapcsolódó tölti fel|
   | **App Service-neve** | Új értéket kézi bevitele ezt a mezőt, attól függően, hogy más beállításokat szükség lehet. |
   | **Erőforráscsoport**    | Legördülő elérhető erőforráscsoportok tölti fel |
   | **Application Insights-erőforrás neve** | Legördülő lista összes Application Insights-erőforrások, amelyek megfelelnek a korábban kiválasztott erőforráscsoporthoz tölti fel.

5. Válassza ki **konfigurálja az Application Insights-riasztások**

6. A riasztási szabályok alapértelmezett, válassza az **mentése** > adjon meg egy leíró megjegyzés > kattintson **OK**

## <a name="modify-alert-rules"></a>Riasztási szabályok módosítása

1. Az előre meghatározott riasztási beállítások módosításához kattintson a mező **folytatást jelző pontokra...**  jobb oldalán **riasztási szabályok.**

   (Out-of-box négy riasztási szabályok: rendelkezésre állási, a sikertelen kérelmek, a kiszolgáló válaszideje, a kivételek.)

2. Kattintson a Tovább gombra a legördülő lista szimbólum **rendelkezésre állását.**

3. Módosíthatja a rendelkezésre állási **küszöbérték** lehet elégíteni a szolgáltatások szint.

   ![-Riasztás módosítása](.\media\app-insights-continuous-monitoring\003.png)

4. Válassza ki **OK** > **mentése** > adjon meg egy leíró megjegyzés > kattintson **OK gombra.**

## <a name="add-deployment-conditions"></a>Központi telepítés feltételek hozzáadása

1. Kattintson a **csővezeték** > Válassza ki a **előtti** vagy **telepítés utáni feltételek** szimbólum attól függően, hogy a folyamatos figyelési kaput igénylő szakasszal.

   ![Központi telepítés előtti feltételek](.\media\app-insights-continuous-monitoring\004.png)

2. Állítsa be **kapuk** való **engedélyezve** > **jóváhagyási kapuk**> kattintson **hozzáadása.**

3. Válassza ki **Azure figyelő** (Ez a beállítás lehetővé teszi a hozzáférést riasztásokat is az Azure-figyelő és az Application Insights)

    ![Azure Monitor](.\media\app-insights-continuous-monitoring\005.png)

4. Adjon meg egy **kapuk időtúllépés** érték.

5. Adjon meg egy **mintavételi gyakoriság.**

## <a name="deployment-gate-status-logs"></a>Telepítési kapu állapot naplói

Miután hozzáadta a központi telepítés kapuk, az Application Insightsban, ami meghaladja a korábban meghatározott küszöbértéket, riasztást védi a nemkívánatos kiadás előléptetés telepítését. Ha a riasztás fel lett oldva, a központi telepítés automatikusan lépne.

Válassza ezt a viselkedést betartásának, **kiadásokban** > kattintson a jobb gombbal kiadás neve **nyissa meg a** > **naplók.**

![Logs](.\media\app-insights-continuous-monitoring\006.png)

## <a name="next-steps"></a>Következő lépések

További VSTS-Build és tájékozódhat kiadás próbálja ki az alábbiakat [quickstarts.](https://docs.microsoft.com/vsts/build-release/)
