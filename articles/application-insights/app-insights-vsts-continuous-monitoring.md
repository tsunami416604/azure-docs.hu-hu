---
title: Folyamatos figyelés a fejlesztési és üzemeltetési kibocsátási folyamat az Azure DevOps és az Azure Application Insights |} A Microsoft Docs
description: Ez a gyors beállítani a folyamatos figyelés az Application insights segítségével
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 75401614b6892402083af5192b691f00d82c8d05
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50413621"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>A kiadási folyamathoz folyamatos megfigyelés hozzáadása

Az Azure DevOps-szolgáltatásokkal integrálható az Azure Application Insights, hogy a szoftver fejlesztési életciklus során, a DevOps-kiadási folyamatok folyamatos figyelésére. 

Az Azure DevOps-szolgáltatásokkal mostantól támogatja a folyamatos monitorozással, amelyek segítségével a kiadási folyamatokat építhet be az Application Insights és az egyéb Azure-erőforrások figyelési adatait. Az Application Insights riasztást észlelt, amikor a központi telepítés sorompós maradhat, vagy állítható vissza a riasztás megszüntetéséig. Ha minden ellenőrzések sikeres központi telepítések folytassa automatikusan tesztből egészen az éles környezetbe manuális beavatkozás nélkül. 

## <a name="configure-continuous-monitoring"></a>Folyamatos figyelés konfigurálása

1. Válassza ki egy meglévő Azure-fejlesztési és üzemeltetési szolgáltatások projekt.

2. A kurzort **készítése és kiadása** > válassza **kiadásokban** > kattintson a **plusz jelre** > **kiadási definíció létrehozása** > Keresse meg **figyelési** > **az Azure App Service üzembe helyezés, a folyamatos figyelés.**

   ![Új Azure fejlesztési és üzemeltetési szolgáltatások kibocsátási folyamatok](media/app-insights-vsts-continuous-monitoring/001.png)

3. Kattintson a **vonatkoznak.**

4. A piros felkiáltójel mellett jelölje ki a szöveget a kék **környezettel kapcsolatos feladatok megtekintéséhez.**

   ![Környezet feladatok megtekintése](media/app-insights-vsts-continuous-monitoring/002.png)

   Egy konfigurációs képernyő jelenik meg, a következő táblázat segítségével az adatbeviteli mezők kitöltéséhez.

    | Paraméter        | Érték |
   | ------------- |:-----|
   | **Környezet neve**      | A kibocsátási folyamat környezetet leíró neve |
   | **Azure-előfizetés** | Legördülő tölti fel az Azure DevOps-szolgáltatásokkal szervezet kapcsolódó Azure-előfizetést|
   | **App Service neve** | Manuális bejegyzés egy új értéket ebben a mezőben más kijelölések, attól függően szükséges lehet |
   | **Erőforráscsoport**    | Legördülő tölti fel az elérhető erőforráscsoportok |
   | **Application Insights-erőforrás neve** | Minden Application Insights-erőforrások, amelyek megfelelnek a korábban kiválasztott erőforráscsoportba tartozó legördülő tölti fel.

5. Válassza ki **konfigurálása az Application Insights-riasztások**

6. Válassza ki a riasztási szabályok alapértelmezett, **mentése** > adjon meg egy leíró megjegyzés > kattintson **OK**

## <a name="modify-alert-rules"></a>Riasztási szabályok módosítása

1. Az előre meghatározott riasztási beállítások módosításához kattintson a mező **három pont...**  jobbra **riasztási szabályok.**

   (Out-of-box négy riasztási szabályok: rendelkezésre állás, a sikertelen kérelmek, a kiszolgáló válaszideje, a kiszolgálókivételek.)

2. Kattintson a legördülő menü mellett **rendelkezésre állását.**

3. Módosítsa a rendelkezésre állási **küszöbérték** a szolgáltatási szint követelményeinek.

   ![Riasztás módosítása](media/app-insights-vsts-continuous-monitoring/003.png)

4. Válassza ki **OK** > **mentése** > adjon meg egy leíró megjegyzés > kattintson **OK.**

## <a name="add-deployment-conditions"></a>Üzembe helyezés feltételek hozzáadása

1. Kattintson a **folyamat** > Válassza ki a **előtti** vagy **üzembe helyezés utáni feltételek** függően a szakaszban egy folyamatos figyelési kapu igénylő szimbólum.

   ![Központi telepítés előtti feltételek](media/app-insights-vsts-continuous-monitoring/004.png)

2. Állítsa be **kapuk** való **engedélyezve** > **jóváhagyási kapuk**> kattintson **hozzáadása.**

3. Válassza ki **Azure Monitor** (Ez a beállítás lehetővé teszi a hozzáférés riasztásokra mind az Azure Monitor és az Application Insights)

    ![Azure Monitor](media/app-insights-vsts-continuous-monitoring/005.png)

4. Adjon meg egy **kapuk időtúllépési** értéket.

5. Adjon meg egy **mintavételi időköz.**

## <a name="deployment-gate-status-logs"></a>Telepítési kapu állapot naplói

Miután hozzáadta a központi telepítési kapuk, az Application insights szolgáltatásban, ami meghaladja a korábban meghatározott küszöbértéket, riasztást nyújt a nemkívánatos kiadási előléptetés-telepítése. A riasztás fel lett oldva, miután a telepítés automatikusan folytassa.

Tekintse meg ezt a viselkedést, jelölje be **kiadásokban** > kattintson a jobb gombbal kiadás neve **nyissa meg a** > **naplókat.**

![Logs](media/app-insights-vsts-continuous-monitoring/006.png)

## <a name="next-steps"></a>További lépések

További információ az Azure-folyamatok próbálja ki ezeket a további [rövid útmutatók.](https://docs.microsoft.com/azure/devops/pipelines)
