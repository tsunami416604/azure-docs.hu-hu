---
title: Application Insights Telemetria a Visual Studio CodeLensben | Microsoft Docs
description: "A CodeLens használatával gyorsan elérheti az Application Insights-kérést és a kivételtelemetriát a Visual Studióban."
services: application-insights
documentationcenter: .net
author: numberbycolors
manager: douge
ms.assetid: 93559e44-23cb-4b9d-8425-60f7f0d0a82c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2016
ms.author: daviste
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a42d55df223c25a26111bbcc05628ff7f4ff7204


---
# <a name="application-insights-telemetry-in-visual-studio-codelens"></a>Application Insights Telemetria a Visual Studio CodeLensben
A telemetriával megjegyzések fűzhetők a webalkalmazás kódjában szereplő metódusokhoz a futásidejű kivételekkel és a kérések válaszidejével kapcsolatban. Ha telepíti a [Visual Studio Application Insightsot](app-insights-overview.md) az alkalmazásban, a telemetria megjelenik a Visual Studio [CodeLensben](https://msdn.microsoft.com/library/dn269218.aspx) – a az egyes függvények felett láthatók a jegyzetek, ahol korábban a függvény hivatkozási helyeinek száma, a legutóbbi szerkesztő személy neve vagy egyéb hasznos információk jelentek meg.

![CodeLens](./media/app-insights-visual-studio-codelens/codelens-overview.png)

> [!NOTE]
> Az Application Insights a CodeLensben a Visual Studio 2015 Update 3 vagy újabb verzióban, illetve a [Developer Analytics Tools bővítmény](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) legújabb verziójában érhető el. A CodeLens elérhető a Visual Studio Enterprise és Professional kiadásaiban.
> 
> 

## <a name="where-to-find-application-insights-data"></a>Az Application Insights-adatok megkeresése
Az Application Insights Telemetriát a webalkalmazás nyilvános kérési metódusainak CodeLens-jelzőiben találja. A CodeLens-jelzők a C#- és a Visual Basic-kódokban a metódus és egyéb deklarációk fölött jelennek meg. Ha az Application Insights-adatok elérhetők egy metódushoz, akkor a kérések és kivételek jelzőit fogja látni, például „100 kérés, 1% meghiúsult” vagy „10 kivétel”. További információkért kattintson a CodeLens-jelzőre. 

> [!TIP]
> Előfordulhat, hogy az Application Insights kérés- és kivételjelzői néhány másodperccel a CodeLens-jelzők után jelennek meg.
> 
> 

## <a name="exceptions-in-codelens"></a>Kivételek a CodeLensben
![TBD](./media/app-insights-visual-studio-codelens/codelens-exceptions.png)

A CodeLens-kivételjelző azt jeleníti meg, hogy az alkalmazás az elmúlt 24 óra alatti 15 leggyakoribb kivétele hányszor fordult elő az adott metódus által kiszolgált kérés feldolgozása közben.

További részletekért kattintson a CodeLens-kivételjelzőre:

* A kivételek számának százalékos változása az elmúlt 24 órából az azt megelőző 24 órához képest
* A **Go to code** (Ugrás a kódra) paranccsal a kivételt kiváltó függvény forráskódjához navigálhat
* A **Search** (Keresés) használatával a kivétel összes előfordulását lekérdezheti az elmúlt 24 órából
* A **Trend** segítségével megtekintheti a kivétel előfordulási trendjének vizualizációját az elmúlt 24 órából
* A **View all exceptions in this app** (Minden kivétel megtekintése az alkalmazásban) paranccsal a kivétel összes előfordulását lekérdezheti az elmúlt 24 órából
* A **Explore exception trends** (Kivételtrendek böngészése) paranccsal megtekintheti az összes kivétel előfordulási trendjeiről készült vizualizációt az elmúlt 24 órából. 

> [!TIP]
> Ha a „0 exceptions” (0 kivétel) üzenetet látja a CodeLensben, de tudja, hogy kell lennie kivételnek, akkor ellenőrizze, hogy a megfelelő Application Insights-erőforrást választotta-e ki a CodeLensben. Másik erőforrás kiválasztásához kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza az **Application Insights > Choose Telemetry Source** (Application Insights > Telemetriaforrás kiválasztása) lehetőséget. A CodeLens csak az alkalmazásban az elmúlt 24 órában leggyakrabban előforduló 15 kivételnél jelenik meg, ezért ha a kivétel nincs a 15 leggyakoribb kivétel között, akkor a „0 exceptions” (0 kivétel) üzenet jelenik meg. Az ASP.NET-nézetek kivételei nem jelennek meg azokon a vezérlőmetódusokon, amelyek az adott nézeteket létrehozták.
> 
> [!TIP]
> Ha a „? exceptions” (? kivétel) üzenet jelenik meg a CodeLensben, akkor Azure-fiókját társítania kell a Visual Studióhoz, vagy lejárt az Azure-fiók hitelesítő adatainak érvényessége. Mindkét esetben kattintson a „? exceptions” (? kivétel) üzenetre, majd válassza az **Add an account...** (Fiók hozzáadása) lehetőséget, és írja be hitelesítő adatait.
> 
> 

## <a name="requests-in-codelens"></a>Kérések a CodeLensben
![TBD](./media/app-insights-visual-studio-codelens/codelens-requests.png)

A CodeLens-kérésjelző azon HTTP-kérések számát mutatja, amelyeket egy metódus kiszolgált az elmúlt 24 órában, valamint a meghiúsult kérések százalékos arányát.

További részletekért kattintson a CodeLens-kérésjelzőre:

* A kérések és meghiúsult kérések számának változása és a változás százalékos aránya, valamint az átlagos válaszidők az elmúlt 24 óra során az azt megelőző 24 órához képest
* A metódus megbízhatósága az elmúlt 24 órában nem meghiúsult kérések százalékos aránya alapján
* A **Search** (Keresés) használatával a kérések és meghiúsult kérések összes előfordulását lekérdezheti az elmúlt 24 órából
* A **Trend** segítségével megtekintheti a kérések, meghiúsult kérések vagy átlagos válaszidők trendjének vizualizációját az elmúlt 24 órából.
* A CodeLens-adatok forrásának módosításához válassza ki az Application Insights-erőforrás nevét a CodeLens részletes nézetének bal felső sarkában.

## <a name="a-namenextanext-steps"></a><a name="next"></a>Következő lépések
|  |  |
| --- | --- |
| **[Az Application Insights használata a Visual Studióban](app-insights-visual-studio.md)**<br/>Telemetriát kereshet, adatokat tekinthet meg a CodeLensben és konfigurálhatja az Application Insights alkalmazást. Mindezt a Visual Studión belül. |![Kattintson a jobb gombbal a projektre, és válassza az Application Insights, Keresés lehetőséget.](./media/app-insights-visual-studio-codelens/34.png) |
| **[További adatok hozzáadása](app-insights-asp-net-more.md)**<br/>Figyelheti a használatot, az elérhetőséget, a függőségeket és a kivételeket. Integrálhatja a nyomkövetéseket naplózási keretrendszerekből. Egyéni telemetriát írhat. |![Visual Studio](./media/app-insights-visual-studio-codelens/64.png) |
| **[Az Application Insights-portál használata](app-insights-dashboards.md)**<br/>Az irányítópultok, a hatékony diagnosztikai és elemző eszközök, riasztások, egy élő függőségi térkép az alkalmazásához, valamint a telemetria exportálása. |![Visual Studio](./media/app-insights-visual-studio-codelens/62.png) |




<!--HONumber=Nov16_HO2-->


