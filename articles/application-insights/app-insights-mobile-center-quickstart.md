---
title: "Az Azure Application Insights gyors üzembe helyezése | Microsoft Docs"
description: "Útmutatás a mobilalkalmazások a figyelés az Application Insights és Mobile Center gyorsan beállítása"
services: application-insights
keywords: 
author: numberbycolors
ms.author: daviste
ms.date: 10/05/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: a8af65a9a9f31200f833a47f811ae50a0a9d2f9b
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="start-analyzing-your-mobile-app-with-mobile-center-and-application-insights"></a>Indítsa el a mobilalkalmazást az mobileszköz-központ és az Application Insights elemzése

A gyors üzembe helyezés végigvezeti Önt az alkalmazáspéldányban Mobile Center Application insights szolgáltatással való kapcsolódás. Az Application insights szolgáltatással lekérdezése, szegmentálhatja, szűrésére és hatékonyabb eszközök érhetők el, mint a telemetriai adatok elemzése a [Analytics](https://docs.microsoft.com/mobile-center/analytics/) Mobile Center szolgáltatás.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Azure-előfizetés.
- Az iOS, Android, Xamarin, univerzális Windows vagy natív reagálni alkalmazással.
 
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="onboard-to-mobile-center"></a>Érheti el a mobil Center

Az Application Insights az a mobilalkalmazás használata előtt kell bevezetésében az alkalmazásnak, hogy [Mobile Center](https://docs.microsoft.com/mobile-center/). Az Application Insights nem telemetriai adatok fogadhatók az a mobilalkalmazás közvetlenül. Ehelyett az alkalmazás egyéni esemény telemetriai adatokat küld Mobile Center. Majd Mobile Center folyamatosan exportálja az egyéni esemény másolatát az Application Insightsban való, az események fogadása.

A bevezetni az alkalmazás minden egyes platformhoz a mobileszköz Center gyors üzembe helyezés a alkalmazás támogatja. Minden egyes platformhoz különálló Mobile Center példányok létrehozása:

* [iOS](https://docs.microsoft.com/mobile-center/sdk/getting-started/ios).
* [Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/android).
* [Xamarin](https://docs.microsoft.com/mobile-center/sdk/getting-started/xamarin).
* [Az univerzális Windows](https://docs.microsoft.com/mobile-center/sdk/getting-started/uwp).
* [Natív reagálni](https://docs.microsoft.com/mobile-center/sdk/getting-started/react-native).

## <a name="track-events-in-your-app"></a>Nyomon követheti az eseményeket az alkalmazásban

Miután az alkalmazás Mobile Center előkészítve, kell módosítani kell a mobileszköz Center SDK használatával egyéni esemény telemetriai adatokat küldhet. Egyéni eseményeket az Application Insights az exportált Mobile Center telemetriai csak ilyen típusú is.

Egyéni események küldése az iOS-alkalmazások, használja a `trackEvent` vagy `trackEvent:withProperties` módszerek a Mobile Center SDK-ban. [További információk az iOS-alkalmazások nyomon követés.](https://docs.microsoft.com/mobile-center/sdk/analytics/ios)

```Swift
MSAnalytics.trackEvent("Video clicked")
```

Egyéni események küldése az Android-alkalmazások, használja a `trackEvent` a Mobile Center SDK metódust. [További információk az Android-alkalmazások nyomon követés.](https://docs.microsoft.com/mobile-center/sdk/analytics/android)

```Java
Analytics.trackEvent("Video clicked")
```

Egyéni események küldése az egyéb alkalmazás platformokról származó, használja a `trackEvent` a Mobile Center SDK-k metódusokon.

Győződjön meg arról, hogy az egyéni események beérkező, keresse fel a **események** lap a **Analytics** Mobile Center című szakasza. Az események megjelennek a, ha azok még az alkalmazásból küldött néhány percig is tarthat.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása

Miután az alkalmazás egyéni események küld, és ezek az események Mobile Center beérkező, hozzon létre egy Mobile Center-típus az Application Insights-erőforrást az Azure portálon kell:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **Új** > **Felügyelet és kezelés** > **Application Insights** elemet.

    ![Application Insights-erőforrás hozzáadása](./media/app-insights-mobile-center-quickstart/add.png)

    A konfiguráció mezőben jelenik meg. Az alábbi táblázat segítségével töltse ki a beviteli mezők.

    | Beállítások        |  Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name (Név)**      | Néhány globális szinten egyedi érték, például a "myApp-iOS" | A figyelt alkalmazást azonosító név |
   | **Alkalmazás típusa** | Mobile központ alkalmazásában | A figyelt alkalmazás típusa |
   | **Erőforráscsoport**     | Egy új erőforráscsoportot, vagy egy meglévő a menüből | Az erőforráscsoport, amelyben az új Application Insights-erőforrás létrehozása |
   | **Hely** | Egy helyet a menüből | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

3. Kattintson a **Create** (Létrehozás) gombra.

Ha az alkalmazás támogatja a több platformot (iOS, Android, stb.), érdemes létrehozni külön Application Insights-erőforrások, egy mindegyik platformon.

## <a name="export-to-application-insights"></a>Az Application Insights exportálása

A új Application Insights-erőforrás a a **áttekintése** lapját a **Essentials** szakasz elején, másolja az ehhez az erőforráshoz instrumentation kulcsot.

Az alkalmazás Mobile Center-példány:

1. Az a **beállítások** kattintson **exportálása**.
2. Válasszon **új exportálása**, válasszon **Application Insights**, majd kattintson a **Testreszabás**.
3. Az Application Insights instrumentation kulcs illessze be a mezőbe.
4. Hozzájárul az használatát, az Azure-előfizetés az Application Insights-erőforrást tartalmazó növelését. Minden egyes Application Insights-erőforrás a havi fogadott adatok első 1 GB szabad. [További tudnivalók az Application Insights árképzési.](https://azure.microsoft.com/pricing/details/application-insights/)

Ne felejtse el az egyes platformokon ismételje meg a folyamatot a alkalmazás támogatja.

Egyszer [exportálása](https://docs.microsoft.com/mobile-center/analytics/export) van beállítva fel, minden egyéni esemény Mobile központ fogadja az Application Insightsban való másolódik. Az Application Insights elérni, így ha azok nem jelennek meg azonnal, várjon egy kicsit előtt további diagnosztizálása az események több percet is igénybe vehet.

Hogy több adatot biztosítson amikor első alkalommal csatlakoztatja, egyéni események Mobile Center legutóbbi 48 órán belül automatikusan exportálása az Application Insights részére.

## <a name="start-monitoring-your-app"></a>Az alkalmazás figyelése

Az Application Insights lekérdezni, szegmentálhatja, szűrheti, és elemezheti az egyéni esemény telemetriai adatokat az alkalmazásokból, a mobil központ elemzőeszközök túl.

1. **Az egyéni esemény telemetriai adatokat lekérdezni.** Az Application Insights az **áttekintése** lapon, válassza ki **Analytics**. 

   ![Az Application Insightsban Analytics gomb](./media/app-insights-mobile-center-quickstart/analytics.png)

   Ekkor megnyílik az Application Insights Analytics portál társított az Application Insights-erőforrást. Az Analytics portál segítségével közvetlenül adatait kéri a Log Analytics lekérdezési nyelv használ, így az alkalmazás és a felhasználók önkényesen összetett kérdéseit teheti fel.
   
   Nyissa meg egy új lapot az Analytics-portálon, majd illessze be a következő lekérdezés. A különböző felhasználók számáról elküldött minden egyéni esemény az alkalmazásból az elmúlt 24 órában, ezek különböző száma szerint rendezett számát adja vissza.

   ```AIQL
   customEvents
   | where timestamp >= ago(24h)
   | summarize dcount(user_Id) by name 
   | order by dcount_user_Id desc 
   ```

   ![Analytics portál](./media/app-insights-mobile-center-quickstart/analytics-portal.png)

   1. Válassza ki a lekérdezés bárhova kattinthat a lekérdezés a szövegszerkesztőben.
   2. Kattintson a **Ugrás** a lekérdezés futtatásához. 

   További információ [Application Insights Analytics](app-insights-analytics.md) és a [Log Analytics lekérdezési nyelv](https://docs.loganalytics.io/docs/Language-Reference).


2. **Szegmentálni, és az egyéni esemény telemetriai szűréséhez.** Az Application Insights az **áttekintése** lapon, válassza ki **felhasználók** a tartalomjegyzékben.

   ![Felhasználó-eszköz ikon](./media/app-insights-mobile-center-quickstart/users-icon.png)

   A felhasználó-eszköz a felhasználók számára az alkalmazás egyes gombokra kattintott, bizonyos képernyők meglátogatott vagy bármilyen más műveletet végre, amely a Mobile Center SDK-val eseményként nyomon követett jeleníti meg. Ha már megtörtént a szegmenseket, és a mobil Center események szűréséhez, a felhasználó eszköze kiváló választás.

   ![Felhasználó-eszköz](./media/app-insights-mobile-center-quickstart/users.png) 

   A használati földrajzi hely például szegmentálja kiválasztása **ország vagy régió** a a **szerint** legördülő menüre.

3. **Vizsgálja meg az alkalmazás átalakítás, megőrzés és navigációs minták.** Az Application Insights az **áttekintése** lapon, válassza ki **felhasználói Forgalomáramlás** a tartalomjegyzékben.

   ![Felhasználó adatfolyamok eszköz](./media/app-insights-mobile-center-quickstart/user-flows.png)

   A felhasználó Forgalomáramlás eszköz visualizes mely eseményeket, a felhasználók küldeni, néhány kiindulási esemény után. Akkor célszerű átfogó képet, hogy a felhasználók hogyan navigálnak az alkalmazás első. Azt is megmutathatja, helyek, ahol sok felhasználó kiköpülődést az alkalmazásból, vagy ismétlődő és újra ugyanazokat a műveleteket.

   Felül felhasználói zajlik az Application Insights rendelkeznek, több más használati elemzőeszközök adott kérdések megválaszolása:

   * **Tölcsérek** átváltási figyelése és elemzésére.
   * **Megőrzési** elemzéséhez, milyen mértékben az alkalmazás megtartja a felhasználók adott idő alatt.
   * **Munkafüzetek** a képi megjelenítések és a szöveg kombinálása egy megosztható jelentésbe.
   * **Cohorts** elnevezésekor és a felhasználók vagy események meghatározott csoportok mentése, így azok könnyen hivatkozható, azonban a más analytics eszközök számára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szeretné, hogy továbbra is az Application Insights használja a mobileszköz-központban, kapcsolja ki az Mobile Center exportálása, és az Application Insights-erőforrás törlése. Ez megakadályozza, hogy többletfizetésre volna szükség, további Application Insights által ehhez az erőforráshoz.

Exportálás Mobile Center kikapcsolása:

1. Lépjen a Mobile Center **beállítások** válassza **exportálása**.
2. Kattintson az Application Insights exportálás törölje, majd kattintson a kívánt **exportálás törlése** a lap alján, majd erősítse meg.

Az Application Insights-erőforrás törlése:

1. Az Azure portál bal oldali menüben kattintson a **erőforráscsoportok** és válassza ki az erőforráscsoportot, amelyben az Application Insights-erőforrás létrehozása történt.
2. Nyissa meg a törölni kívánt Application Insights-erőforrást. Kattintson a **törlése** az erőforrás a felső menüben, majd erősítse meg. Az Application Insights exportált adatok másolatát véglegesen törölni szeretné.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megérteni a felhasználók hogyan használják az alkalmazást](app-insights-usage-overview.md)