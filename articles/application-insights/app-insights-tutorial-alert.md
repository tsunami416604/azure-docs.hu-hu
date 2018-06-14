---
title: Értesítések küldése az Azure Application Insights |} Microsoft Docs
description: Az oktatóanyag segítséget nyújt küld riasztást, az alkalmazás használatával Azure Application Insights hibáinak válaszként.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 39e2f136e30ebb6dcfc003c435382f3384af1052
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
ms.locfileid: "23947227"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>A figyelő és riasztás az alkalmazás állapotát az Azure Application insights szolgáltatással

Az Azure Application Insights lehetővé teszi az alkalmazás figyelése és küld, riasztást, ha vagy nem érhető el, hibákat tapasztal, vagy a teljesítménnyel kapcsolatos problémák hibaelhárításához használhatók.  Ez az oktatóanyag végigvezeti annak ellenőrzésére, az alkalmazás rendelkezésre állásának folyamatosan létrehozásának folyamatán, és problémákat észlelt különböző típusú riasztások küldése adott válaszként.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Elérhetőségi teszt folyamatosan ellenőrzése a válaszban az alkalmazás létrehozása
> * E-mailt küldjön rendszergazdák probléma esetén
> * Hozzon létre a riasztások teljesítménymutatók alapján 
> * A logikai alkalmazás segítségével összesített telemetriai adatokat küldhet egy ütemezés szerint.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
    - Az Azure-bA egy .NET-alkalmazás központi telepítése és [engedélyezze az Application Insights SDK](app-insights-asp-net.md). 


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Elérhetőségi teszt létrehozása
Az Application Insightsban rendelkezésreállás figyelésére szolgáló tesztek engedélyezi, hogy automatikusan tesztelje az alkalmazás a világ különböző helyekről.   Ebben az oktatóanyagban egy egyszerű tesztelési annak érdekében, hogy rendelkezésre áll-e az alkalmazás kell elvégezni.  A részletes működésének teszteléséhez részletes útmutatást is létrehozhatók. 

1. Válassza ki **Application Insights** és majd jelölje ki az előfizetését.  
1. Válassza ki **rendelkezésre állási** alatt a **vizsgálat** menüre, és kattintson **Hozzáadás teszt**.
 
    ![Elérhetőségi teszt hozzáadása](media/app-insights-tutorial-alert/add-test.png)

2. Adjon meg egy nevet a teszthez, és hagyja meg az alapértelmezett beállításokat.  Ez a kezdőlap, az alkalmazás 5 percenként kér 5 különböző földrajzi helyet. 
3. Válassza ki **riasztások** megnyitásához a **riasztások** ahol meghatározhatja, hogyan reagáljon a teszt sikertelen, a részletek panelen. Típus a mikor küldjön egy e-mail címet a figyelmeztetési feltétel teljesülése esetén.  A címben opcionálisan be az olyan webhook hívására, ha a riasztási feltétel teljesülése esetén.

    ![Teszt létrehozása](media/app-insights-tutorial-alert/create-test.png)
 
4. A teszt panelen adja vissza, és néhány perc múlva jelenítse meg a rendelkezésre állási teszt eredményei.  Kattintson a vizsgálat nevére az egyes helyeken a részletek megtekintéséhez.  A diagram bemutatja a sikeres és a tesztelések időtartama.

    ![Részletei](media/app-insights-tutorial-alert/test-details.png)

5.  Részletezve minden részleteit az a pont, a diagram a kattintva.  Az alábbi példában a sikertelen kérelmek részleteit jeleníti meg.

    ![Tesztelési eredménye](media/app-insights-tutorial-alert/test-result.png)
  
6. A figyelmeztetési feltétel teljesülése esetén, egy hasonló alább e-mail érkezik a megadott cím.

    ![Riasztási mail](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Riasztás létrehozása a metrikák
Mellett értesítések küldését egy elérhetőségi teszt, létrehozhat egy riasztás bármely teljesítménymutatók alatt gyűjtött az alkalmazáshoz.

2. Válassza ki **riasztások** a a **konfigurálása** menü.  Ekkor megnyílik az Azure-riasztások panel.  Előfordulhat, hogy más riasztási szabályok az itt konfigurált más szolgáltatásokhoz.
3. Kattintson a **metrika riasztás hozzáadása**.  Ekkor megnyílik egy új riasztási szabályt létrehozni a panelen.

    ![Metrika riasztások hozzáadása](media/app-insights-tutorial-alert/add-metric-alert.png)

4. Írja be egy **neve** a riasztásra vonatkozó szabály, és válassza ki az alkalmazás számára a legördülő **erőforrás**.
5. Válassza ki a **metrika** fog kapni.  Egy grafikonon jelenik meg, hogy a kérelem értéket az elmúlt 24 óra során.  Ez segítséget nyújt a metrika a feltétel beállítását.

    ![Riasztási szabály hozzáadása](media/app-insights-tutorial-alert/add-alert-01.png)

6. Adjon meg egy **feltétel** és **küszöbérték** a riasztás. Ez az a szám, ahányszor, amelyet a metrika létrejön egy riasztás. 
6. A **keresztül értesíti** ellenőrizze a **E-mail-tulajdonosok, közreműködőknek és olvasóknak** jelölőnégyzetet, hogy egy üzenetet küldeni a felhasználóknak, ha a riasztási feltétel teljesül, és adja hozzá a további címzettek e-mail címét.  Megadhatja egy webhook vagy logikai alkalmazás itt futtatja, ha a feltétel teljesül.  Ezek segítségével esetleg csökkenthető az észlelt probléma vagy 

    ![Riasztási szabály hozzáadása](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Proaktív módon eljuttathatja
Riasztások jönnek létre a szembeni egy meghatározott az alkalmazás azonosított problémát, és általában lefoglalni a kritikus feltételeket azonnali figyelmet igénylő riasztásokat.  Egy ütemezés szerint automatikusan futó logikai alkalmazást az alkalmazásba proaktív adatokat is kap.  Lehet például naponta összefoglaló információt, amely további fel kell mérni a rendszergazdák számára a leveleket.

További részletek a logikai alkalmazás létrehozása az Application insights szolgáltatással: [automatizálhatja az Application Insights dolgozza fel a Logic Apps segítségével](automate-with-logic-apps.md)

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte hogyan riasztást küld a problémák, a következő oktatóanyag megtudhatja, hogyan felhasználók az alkalmazással való interakció elemzése továbblépés.

> [!div class="nextstepaction"]
> [Felhasználók ismertetése](app-insights-tutorial-users.md)