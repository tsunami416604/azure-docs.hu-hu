---
title: DevOps-kiadási folyamat folyamatos figyelése Azure-folyamatokkal és Azure-Application Insightsokkal | Microsoft Docs
description: Útmutatást nyújt a folyamatos figyelés beállításához Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655395"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Folyamatos figyelés hozzáadása a kiadási folyamathoz

Az Azure-folyamatok integrálva vannak az Azure Application Insights-mel, hogy a DevOps-kiadási folyamat folyamatos monitorozását lehetővé tegye a szoftverfejlesztés életciklusa során. 

A folyamatos figyeléssel a kiadási folyamatok Application Insights és más Azure-erőforrásokból is beépíthetnek megfigyelési adatokba. Ha a kiadási folyamat egy Application Insights riasztást észlel, a folyamat a riasztás feloldása előtt elindíthatja vagy visszaállíthatja az üzemelő példányt. Ha az összes ellenőrzés sikeres, a központi telepítések az éles környezetből egészen a tesztelésig automatikusan folytathatják a beavatkozást anélkül, hogy kézi beavatkozásra lenne szükség. 

## <a name="configure-continuous-monitoring"></a>Folyamatos figyelés konfigurálása

1. Az [Azure DevOps](https://dev.azure.com)válassza ki a szervezet és a projekt elemet.
   
1. A projekt lap bal oldali menüjében válassza a **folyamatok** > **kiadások**elemet. 
   
1. Húzza le a nyilat az **új** elem melletti nyílra, és válassza az **új kiadási folyamat**lehetőséget. Ha még nem rendelkezik folyamattal, a megjelenő oldalon válassza az **új folyamat** elemet.
   
1. A **sablon kiválasztása** ablaktáblán keresse meg és válassza ki **Azure app Service központi telepítést folyamatos figyeléssel**, majd válassza az **alkalmaz**lehetőséget. 

   ![Új Azure-folyamatok kiadási folyamata](media/continuous-monitoring/001.png)

1. Az **1. fázis** mezőben válassza ki a hivatkozást a **szakasz feladatainak megtekintéséhez.**

   ![Szakasz feladatainak megtekintése](media/continuous-monitoring/002.png)

1. Az **1. fázis** konfigurációs ablaktábláján hajtsa végre a következő mezőket: 

    | Paraméter        | Érték |
   | ------------- |:-----|
   | **Szakasz neve**      | Adja meg a fázis nevét, vagy hagyja meg az **1. fázisban**. |
   | **Azure-előfizetés** | Legördülő menüből válassza ki a használni kívánt társított Azure-előfizetést.|
   | **App type** (Alkalmazás típusa) | Legördülő menüből válassza ki az alkalmazás típusát. |
   | **App Service neve** | Adja meg a Azure App Service nevét. |
   | **Application Insights erőforráscsoport-neve**    | Legördülő menüből válassza ki a használni kívánt erőforráscsoportot. |
   | **Application Insights erőforrás neve** | Legördülő menüből válassza ki a kiválasztott erőforráscsoport Application Insights erőforrását.

1. Ha menteni szeretné a folyamatot az alapértelmezett riasztási szabály beállításaival, válassza a jobb felső sarokban található **Mentés** az Azure DevOps ablakban lehetőséget. Adjon meg egy leíró megjegyzést, majd kattintson **az OK gombra**.

## <a name="modify-alert-rules"></a>Riasztási szabályok módosítása

A **folyamatos figyelési sablonnal rendelkező Azure app Service üzemelő példánynak** négy riasztási szabálya van: **rendelkezésre állás**, **Sikertelen kérelmek**, **kiszolgáló válaszideje**és **kiszolgálói kivételek**. További szabályokat adhat hozzá, vagy módosíthatja a szabályok beállításait, hogy azok megfeleljenek a szolgáltatási szint igényeinek. 

A riasztási szabály beállításainak módosítása:

1. A kiadási folyamat lap bal oldali ablaktábláján válassza a **Application Insights riasztások konfigurálása**lehetőséget.

1. A **Azure monitor riasztások** panelen válassza a három pontot **...** lehetőséget a **riasztási szabályok**mellett.
   
1. A **riasztási szabályok** párbeszédpanelen válassza ki a riasztási szabály melletti legördülő szimbólumot, például a **rendelkezésre állást**. 
   
1. Módosítsa a **küszöbértéket** és az egyéb beállításokat, hogy megfeleljenek a követelményeinek.
   
   ![Riasztás módosítása](media/continuous-monitoring/003.png)
   
1. Kattintson az **OK gombra**, majd kattintson a jobb felső sarokban található **Mentés** elemre az Azure DevOps ablakban. Adjon meg egy leíró megjegyzést, majd kattintson **az OK gombra**.

## <a name="add-deployment-conditions"></a>Központi telepítési feltételek hozzáadása

Amikor üzembe helyezési kapukat ad hozzá a kiadási folyamathoz, a beállított küszöbértéket meghaladó riasztás megakadályozza a nem kívánt kiadási előléptetést. A riasztás feloldása után az üzemelő példány automatikusan folytatható.

Üzembe helyezési kapuk hozzáadása:

1. A fő folyamat lap **szakaszok**területén válassza ki az **üzembe helyezés előtti feltételeket** vagy a **Telepítés utáni feltételek** szimbólumot attól függően, hogy melyik szakasznak szüksége van egy folyamatos figyelési kapura.
   
   ![Üzembe helyezés előtti feltételek](media/continuous-monitoring/004.png)
   
1. A **központi telepítés előtti feltételek** konfigurációs ablaktáblán állítsa be a **kapuk** beállítást **engedélyezve**értékűre.
   
1. Az **üzembe helyezési kapuk**mellett válassza a **Hozzáadás**lehetőséget.
   
1. Válassza ki a **lekérdezés Azure monitor riasztások** lehetőséget a legördülő menüből. Ez a beállítás lehetővé teszi a Azure Monitor és Application Insights riasztások elérését.
   
   ![Azure Monitor riasztások lekérdezése](media/continuous-monitoring/005.png)
   
1. A **kiértékelési beállítások**szakaszban adja meg azokat az értékeket, amelyeket meg szeretne adni a **Gates újraértékelése** és **a Gates meghibásodása utáni időtúllépési**idő között. 

## <a name="view-release-logs"></a>Kiadási naplók megtekintése

Megtekintheti a telepítési kapu viselkedését és a kiadási naplók egyéb kiadási lépéseit. A naplók megnyitása:

1. Válassza a **kiadások** lehetőséget a folyamat lap bal oldali menüjében. 
   
1. Válasszon ki egy kiadást. 
   
1. A **szakaszok**szakaszban válassza a bármely fázis lehetőséget a kiadás összegzésének megtekintéséhez. 
   
1. A naplók megtekintéséhez válassza a **naplók megtekintése** lehetőséget a kiadás összefoglalójában, válassza a **sikeres** vagy **sikertelen** hiperhivatkozást bármely szakaszban, vagy vigye a kurzort bármely szakaszra, és válassza a **naplók**lehetőséget. 
   
   ![Kiadási naplók megtekintése](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Következő lépések

Az Azure-folyamatokkal kapcsolatos további információkért tekintse meg az [Azure-folyamatok dokumentációját](https://docs.microsoft.com/azure/devops/pipelines).
