---
title: "Hozzon létre egy új Azure Application Insights-erőforrást |} Microsoft Docs"
description: "Manuálisan állítsa be az Application Insights egy új élő alkalmazás figyelését."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 9023f3d9ae3ddd4d75b5853a08177cba7718cec1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása
Azure Application Insights az alkalmazással kapcsolatos adatokat jeleníti meg a Microsoft Azure *erőforrás*. Új erőforrás létrehozása része ezért [Application Insights beállítása egy új alkalmazás figyelésére][start]. Sok esetben erőforrás létrehozása automatikusan úgy teheti az IDE. De néhány esetben hoz létre egy erőforrás manuálisan - például számára elkülönített erőforrások fejlesztési és éles az alkalmazás létrehozza.

Az erőforrás létrehozása után annak instrumentation kulcs lekérése, és azt használja az alkalmazás az SDK konfigurálására. Az erőforráskulcs telemetriai adatok az erőforrás hivatkozásokat tartalmaz.

## <a name="sign-up-to-microsoft-azure"></a>Jelentkezzen a Microsoft Azure
Ha még nem kapott egy [Microsoft fiókot, egy letöltése](http://live.com). (Például Outlook.com, OneDrive, Windows Phone vagy XBox Live-szolgáltatást használ, ha már rendelkezik Microsoft-fiók.)

Emellett szükség van egy előfizetés [Microsoft Azure](http://azure.com). Ha a csapat vagy szervezet Azure-előfizetéssel, a tulajdonos adhat hozzá, a Windows Live ID azonosítójával. Most csak felszámított a valóban használt funkciókért. Az alapértelmezett alapszintű csomag lehetővé teszi a kísérleti díjmentesen használható bizonyos mennyiségű.

Előfizetés hozzáférést van, amikor jelentkezzen be az Application Insights részére, [http://portal.azure.com](https://portal.azure.com), és a Live ID bejelentkezési használja.

## <a name="create-an-application-insights-resource"></a>Application Insights-erőforrás létrehozása
Az a [portal.azure.com](https://portal.azure.com), vegyen fel egy Application Insights-erőforrást:

![Kattintson az Új, majd az Application Insights lehetőségre](./media/app-insights-create-new-resource/01-new.png)

* **Az alkalmazástípus** elemnél mi jelenik a áttekintése panel megnyitásához, és a tulajdonságok érhetők el a [metrika explorer][metrics]. Ha nem látja a típusú alkalmazást, válassza az általános.
* **Előfizetés** a fizetési fiók az Azure-ban.
* **Erőforráscsoport** a könnyebb tulajdonságainak kezelésére van, például hozzáférés-vezérlést. Ha már létrehozott más Azure-erőforrások, válassza ki az új erőforrás helyezze ugyanabba a csoportba.
* **Hely** van, ahol azt megőrizni az adatokat.
* **Rögzítés az irányítópulton** helyezi az erőforrás egy gyors elérést csempe az Azure kezdőlapján. Ajánlott.

Ha az alkalmazás létrehozása után egy új panelen nyitja meg. Ezen a panelen, ahol látható teljesítmény- és használati adatokat az alkalmazásra vonatkozó. 

Kattintva visszatérhet, amikor legközelebb bejelentkezik Azure, keresse meg az alkalmazás gyors üzembe helyezési csempe a start táblán (kezdőképernyő). Vagy kattintson a Tallózás gombra, és keresse meg.

## <a name="copy-the-instrumentation-key"></a>A rendszerállapot-kulcs másolása
A instrumentation kulcs azonosítja az erőforrás, amelyet létrehozott. Esetleg szükség lenne rá, hogy biztosítsa az SDK-t a.

![Essentials kattintson, majd a Instrumentation kulcsot, a CTRL + C](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Az alkalmazás az SDK telepítése
Telepítse az Application Insights SDK az alkalmazás. Ezt a lépést az alkalmazás fokozottan függ. 

A rendszerállapot-kulcsot használ konfigurálása [az SDK-t, hogy az alkalmazás telepítése][start].

Az SDK magában foglalja a telemetriai adatokat küldhet anélkül, hogy a kód írása modulban. Nyomon követheti a felhasználói műveletek vagy eseményadatokat részletesen, [API-t használó] [ api] saját telemetriai adatokat küldhet.

## <a name="monitor"></a>Lásd: a telemetriai adatok
Zárja be a gyors üzembe helyezési panel az alkalmazás panel az Azure-portálon való visszatéréshez.

Kattintson a keresés csempe megtekintéséhez [diagnosztikai keresési][diagnostic], ahol az első események jelennek meg. 

Ha több adatot várt, kattintson a **frissítése** néhány másodperc múlva.

## <a name="creating-a-resource-automatically"></a>Erőforrás automatikus létrehozása
Írhat egy [PowerShell-parancsfájl](app-insights-powershell.md) erőforrás automatikus létrehozása.

## <a name="next-steps"></a>Következő lépések
* [Irányítópult létrehozása](app-insights-dashboards.md)
* [Diagnosztikai keresés](app-insights-diagnostic-search.md)
* [Metrikák böngészése](app-insights-metrics-explorer.md)
* [Analytics-lekérdezések](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

