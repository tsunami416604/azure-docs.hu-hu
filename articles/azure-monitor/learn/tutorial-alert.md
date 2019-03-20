---
title: Értesítések küldése az Azure Application Insightsból |} A Microsoft Docs
description: Az oktatóanyag válaszként az Azure Application Insights használatával alkalmazásban szereplő hibák a riasztások küldéséhez.
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 70a11867dded3b7156f6b212ceb4756ee7c287f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079162"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>A figyelő és az Azure Application Insights alkalmazásállapot-riasztás

Az Azure Application Insights lehetővé teszi az alkalmazás figyelése és küld, riasztást, ha vagy nem érhető el, tapasztalt hibák vagy teljesítményproblémák szenvedő.  Ez az oktatóanyag végigvezeti a folyamatos, az alkalmazás rendelkezésre állásának ellenőrzéséhez tesztek létrehozásának folyamatán, és problémákat észlelt különböző típusú riasztások küldése adott válaszként.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A válasz az alkalmazás folyamatos ellenőrzéséhez rendelkezésre állási teszt létrehozása
> * A rendszergazdák üzenetet küldeni, ha probléma merül fel
> * Teljesítmény-mérőszámon alapuló riasztások létrehozása 
> * Logikai alkalmazás használatával az összegzett telemetriai adatok küldése az ütemezés szerint.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - ASP.NET és webfejlesztés
    - Azure-fejlesztés
    - Telepítsen egy .NET-alkalmazást az Azure-hoz, és [engedélyezze az Application Insights SDK](../../azure-monitor/app/asp-net.md)-t. 


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.

## <a name="create-availability-test"></a>Rendelkezésre állási teszt létrehozása
Az Application Insights rendelkezésre állási tesztek automatikusan tesztelheti a világszerte különböző helyekről alkalmazását teszi lehetővé.   Ebben az oktatóanyagban egy egyszerű tesztet annak biztosításához, hogy az alkalmazás elérhető lesz végezhet.  Emellett létrehozhat egy teljes forgatókönyv részletes működésének teszteléséhez. 

1. Válassza ki az **Application Insights** elemet, majd az előfizetését.  
1. Válassza ki **rendelkezésre állási** alatt a **vizsgálat** menüben, majd kattintson **Hozzáadás teszt**.
 
    ![A rendelkezésre állási teszt hozzáadása](media/tutorial-alert/add-test.png)

2. Írja be a teszthez nevét, és hagyja a többi alapértelmezett értéket.  Ez a kezdőlap, az alkalmazás 5 percenként 5 különböző földrajzi helyeken kér. 
3. Válassza ki **riasztások** megnyitásához a **riasztások** panel, ahol megadhatja a részletei hogyan reagáljon, ha a teszt meghiúsul. Írja be az e-mail-címet, küldhet, amikor a riasztási feltételek teljesülésére.  Szükség esetén beírhatja a cím, egy webhook meghívására, ha a riasztási feltételek teljesülésére.

    ![Teszt létrehozása](media/tutorial-alert/create-test.png)
 
4. A teszt panelre való visszatéréshez, és pár percen belül el kell kezdenie a rendelkezésre állási teszt eredményeinek megtekintése.  Kattintson a teszt neve, az egyes helyek a részletek megtekintéséhez.  A pontdiagram jeleníti meg, a sikeres és minden egyes vizsgálat időtartama.

    ![Teszt részletei](media/tutorial-alert/test-details.png)

5.  Részletezhet bármely adott teszt a részletek a pontdiagram a pontjára kattintva.  Az alábbi példa egy sikertelen kérelmek részleteit jeleníti meg.

    ![Teszteredmény](media/tutorial-alert/test-result.png)
  
6. A riasztási feltételek teljesülése esetén, az alábbihoz hasonló e-mail érkezik a megadott címre.

    ![Riasztási e-mail](media/tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>Hozzon létre egy riasztást a metrikák
Mellett küldhetők értesítések a rendelkezésre állási teszt, létrehozhat egy riasztást bármely alatt gyűjtött teljesítmény-mérőszámok az alkalmazáshoz.

1. Válassza ki **riasztások** származó a **konfigurálása** menü.  Ekkor megnyílik az Azure Alerts panel.  Előfordulhat, hogy más riasztási szabályok az itt konfigurált más szolgáltatásokhoz.
1. Kattintson a **metrikariasztás hozzáadása**.  Ekkor megnyílik egy új riasztási szabály létrehozása a panelen.

    ![Metrikariasztás hozzáadása](media/tutorial-alert/add-metric-alert.png)

1. Írja be egy **neve** a riasztáshoz tartozó szabályt, és a legördülő menüből válassza ki az alkalmazását **erőforrás**.
1. Válassza ki a **metrika** mintát.  Gráf az elmúlt 24 óra során ezt a kérelmet értékét jelzi jelenik meg.  Ez a metrika a feltétel beállítása segítséget nyújt.

    ![Riasztási szabály felvétele](media/tutorial-alert/add-alert-01.png)

1. Adjon meg egy **feltétel** és **küszöbérték** a riasztás. Ez a száma, amelyet a metrika egy riasztás hozható létre. 
1. A **értesítés ezen keresztül** ellenőrizze a **E-mail-tulajdonosoknak, közreműködőknek és olvasóknak** jelölőnégyzetet, hogy egy üzenetet küldeni a felhasználóknak, ha a riasztási feltétel teljesül, és adja hozzá az összes további címzett e-mail-címét.  Megadhat egy webhookot vagy egy logikai alkalmazást itt, amelyen a feltétel teljesülése esetén is.  Ezek próbál meg a probléma megoldásához felhasználható vagy 

    ![Riasztási szabály felvétele](media/tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>Proaktív módon küldhet adatokat
Riasztások jönnek létre a szembeni problémák megoldásával az alkalmazás egy adott készletét, és általában fenntartott riasztásokat az azonnali figyelmet igénylő kritikus fontosságú feltételeket.  Logikai alkalmazás használatával, amely egy ütemezés szerint automatikusan futtatja az alkalmazással kapcsolatos információk proaktív módon fogadhat.  Lehet például egy naponta összefoglaló információt, amely további fel kell mérni a rendszergazdák számára küldött e-mail.

További információ a Logic Apps-alkalmazás létrehozása az Application Insights: [automatizálása az Application Insights feldolgozza a Logic Apps használatával](../../azure-monitor/app/automate-with-logic-apps.md)

## <a name="next-steps"></a>További lépések
Most, hogy bemutattuk, hogyan lehet a problémák riasztást, folytassa a következő oktatóanyaggal, elemezhetők a felhasználók hogyan használja az alkalmazását.

> [!div class="nextstepaction"]
> [A felhasználók megismerése](../../azure-monitor/learn/tutorial-users.md)