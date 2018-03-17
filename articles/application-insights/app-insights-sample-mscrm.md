---
title: "A Microsoft Dynamics CRM-hez és az Azure Application Insights |} Microsoft Docs"
description: "Telemetriai adatok beszerzése a Microsoft Dynamics CRM Online Application Insights segítségével. Forgatókönyv: a telepítés az első adatok, a képi megjelenítés és exportálása."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: mbullwin
ms.openlocfilehash: c5a651a24fcf5d1fc64922483045c08321a3b89c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Forgatókönyv: Telemetriai engedélyezése a Microsoft Dynamics CRM Online Application Insights segítségével
Ez a cikk bemutatja, hogyan telemetriai adatok beolvasására [Microsoft Dynamics CRM Online](https://www.dynamics.com/) használatával [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Végigvezetjük a teljes folyamat Application Insights-parancsfájl hozzáadása az alkalmazáshoz, adatokat és az adatok vizuális rögzítése.

> [!NOTE]
> [Keresse meg a megoldást](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Az Application Insights hozzáadása új vagy meglévő CRM Online-példányra
Ha figyelni szeretné az alkalmazást, az Application Insights SDK az alkalmazás hozzáadása. Az SDK-t küld a telemetria bekapcsolásával a [Application Insights portál](https://portal.azure.com), amelyen használja a hatékony elemzés és diagnosztikai eszközöket, vagy exportálja az adatokat a tárhelyre.

### <a name="create-an-application-insights-resource-in-azure"></a>Az Application Insights-erőforrás létrehozása az Azure-ban
1. Első [egy fiókot a Microsoft Azure-ban](http://azure.com/pricing). 
2. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és adja hozzá egy új Application Insights-erőforrást. Ez az, ha az adatok feldolgozása és jelenik meg.

    ![Kattintson a +, fejlesztői szolgáltatások, az Application Insights.](./media/app-insights-sample-mscrm/01.png)

    Az alkalmazás típusának válassza az ASP.NET lehetőséget.
3. Az első lépések lap megnyitásához, és nyissa meg a "a figyelő és diagnosztizálhatja az ügyféloldali".

    ![A weblap beszúrásához kódrészletet](./media/app-insights-sample-mscrm/03.png)

**Tartsa nyitva, a kódlapot** közben a következő lépés egy másik böngészőablakban teszi. A kód hamarosan lesz szüksége. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>A Microsoft Dynamics CRM JavaScript webes erőforrás létrehozása
1. Nyissa meg az CRM Online-példány és a bejelentkezési rendszergazdai jogosultságokkal.
2. Nyissa meg a Microsoft Dynamics CRM beállításait, egyéni beállításokat, a rendszer testreszabása

    ![Microsoft Dynamics CRM settings](./media/app-insights-sample-mscrm/00001.png)

    ![Beállítások > Testreszabás](./media/app-insights-sample-mscrm/00002.png)

1. Hozzon létre egy JavaScript-erőforrást.

    ![Az új webes erőforrás párbeszédpanel](./media/app-insights-sample-mscrm/07.png)

    Adjon neki egy névvel, jelölje be **parancsfájl (JScript)** , és nyissa meg a szövegszerkesztőben.

    ![Nyissa meg a szövegszerkesztőben.](./media/app-insights-sample-mscrm/00004.png)
2. Másolja a kódot az Application Insights. Másolása, ügyeljen arra, hogy figyelmen kívül hagyása parancsprogramcímkékben-e. Tekintse meg az alábbi képernyőfelvételen:

    ![A rendszerállapot-kulcs beállítása](./media/app-insights-sample-mscrm/00005.png)

    A kód a instrumentation kulcsot, amely azonosítja az Application insights-erőforrást tartalmazza.
3. Mentse, és tegye közzé.

    ![Mentse és közzététele](./media/app-insights-sample-mscrm/00006.png)

### <a name="instrument-forms"></a>Eszköz űrlapok
1. Microsoft CRM Online-hoz nyissa meg a fiók képernyő

    ![Fiók képernyő](./media/app-insights-sample-mscrm/00007.png)
2. Nyissa meg az űrlap tulajdonságai

    ![Űrlap tulajdonságai](./media/app-insights-sample-mscrm/00008.png)
3. A JavaScript létrehozott webes erőforrás hozzáadása

    ![Hozzáadásra szolgáló menü](./media/app-insights-sample-mscrm/13.png)

4. Mentse, és tegye közzé az űrlapok testreszabásai.

## <a name="metrics-captured"></a>Rögzített metrikák
Most már készen az űrlap a telemetria-rögzítést. Azokat alkalmazni, amikor adatokat küldeni az Application Insights-erőforrást.

Az alábbiakban láthatja adatok minták.

#### <a name="application-health"></a>Alkalmazás állapotának
![Példa lapbetöltési idő](./media/app-insights-sample-mscrm/15.png)

![Példa lap nézetek diagram](./media/app-insights-sample-mscrm/16.png)

Böngésző kivételek:

![Böngésző kivételek diagram](./media/app-insights-sample-mscrm/17.png)

Kattintson a diagram megszerezni a további részletek:

![Kivételek listájáról](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Használat
![Felhasználók, a munkamenetek és az oldalmegtekintéseket](./media/app-insights-sample-mscrm/19.png)

![Munkamenet diagramok](./media/app-insights-sample-mscrm/20.png)

![Böngésző-verziók](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Böngészők
![Lapbetöltési idő bontása](./media/app-insights-sample-mscrm/22.png)

![A böngésző verziója-munkamenetek száma](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Földrajzi hely
![Ország munkamenetek száma](./media/app-insights-sample-mscrm/24.png)

![A munkamenetek és országonként felhasználók](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Belső lapkérelem megtekintése
![Lap összegzésének megtekintése](./media/app-insights-sample-mscrm/26.png)

![Keresés lap eseményeinek megtekintése](./media/app-insights-sample-mscrm/27.png)

![Hasonló Lapmegtekintések](./media/app-insights-sample-mscrm/28.png)

![Lapmegtekintési tulajdonságok](./media/app-insights-sample-mscrm/29.png)

![Egy munkamenet oldal](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Mintakód
[Keresse meg a mintakódot](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Lehetőség van még mélyebb elemzés, ha Ön [exportálja az adatokat a Microsoft Power bi-bA](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>A Microsoft Dynamics CRM megoldást
[Ez a minta megoldás megvalósítása a Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Részletek
* [Mi az Application Insights?](app-insights-overview.md)
* [Az Application Insights webes](app-insights-javascript.md)
* [További mintákat és forgatókönyvek](app-insights-code-samples.md)
