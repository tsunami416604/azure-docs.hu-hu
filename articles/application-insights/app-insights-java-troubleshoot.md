---
title: "Hibaelhárítás az Application Insights Java webes projekt"
description: "Hibaelhárítási útmutató - figyelési élő Java-alkalmazásokhoz az Application insights szolgáltatással."
services: application-insights
documentationcenter: java
author: CFreemanwa
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: bwren
ms.openlocfilehash: ce46a4f561a273dc340b090a5bf0c8932a308722
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Hibaelhárítás, kérdések és válaszok: Application Insights Java-hoz
Kérdések és problémák [Azure Application Insights Java nyelven][java]? Az alábbiakban néhány tipp.

## <a name="build-errors"></a>Build hibák
**Az eclipse-ben, az Application Insights SDK Maven vagy a gradle-lel hozzáadásakor build vagy ellenőrzőösszeg érvényesítési hiba jelenik meg.**

* Ha a függőség <version> elem minta használatával helyettesítő karaktereket is tartalmazó (pl. (Maven) `<version>[1.0,)</version>` vagy (Gradle) `version:'1.0.+'`), próbáljon meg egy adott verziójához helyette például `1.0.2`. Tekintse meg a [kibocsátási megjegyzéseket](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) legújabb verziójának.

## <a name="no-data"></a>Nincs adat
**I Application Insights hozzáadása sikeresen befejeződött, és saját alkalmazás futott, de soha nem láthatta, szeretnék adatokat a portálon.**

* Várjon egy percet, és kattintson a frissítés parancsra. A két diagramot rendszeresen frissíteni magukat, de manuálisan is frissítheti. A frissítési időköz az időtartományt a diagram függ.
* Ellenőrizze, hogy van-e egy instrumentation kulcs van megadva az ApplicationInsights.xml fájlban (a Projekt erőforrások mappa)
* Győződjön meg arról, hogy nincs `<DisableTelemetry>true</DisableTelemetry>` csomópont az XML-fájlban.
* A tűzfal akkor előfordulhat, hogy nyissa meg a 80-as és a kimenő forgalmat dc.services.visualstudio.com 443-as TCP-portot. Tekintse meg a [tűzfalkivételeket teljes listája](app-insights-ip-addresses.md)
* A Microsoft Azure-ban indítsa el a tábla, és nézze meg a szolgáltatástérkép állapotát. Ha néhány riasztási jelzések, várjon, amíg azok vissza kellett volna OK majd zárja be és nyissa meg újra az Application Insights-alkalmazás paneljének.
* Engedélyezze a naplózást az IDE konzolablak hozzáadásával egy `<SDKLogger />` elem a ApplicationInsights.xml fájlban (a erőforrások a projekt), és a végrehajtásával kerüli meg a [hiba] bejegyzések legfelső szintű csomópontja alatt.
* Győződjön meg arról, hogy a megfelelő ApplicationInsights.xml fájl sikeresen betöltötte a Java SDK-t, ha megnézi a konzol kimeneti üzenetek "konfigurációs fájl sikeresen talált" utasítás.
* Ha a konfigurációs fájl nem található, ellenőrizze a kimenet megtekintéséhez, ahol a konfigurációs fájl keres a, és győződjön meg arról, hogy a ApplicationInsights.xml azokat a keresési helyek egyikén található. A szokásos megoldás, mint a konfigurációs fájl elhelyezheti az Application Insights SDK JARs közelében. Például: a Tomcat, ez azt jelentené a WEBALKALMAZÁS-INF/lib mappát.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Adatok megjelenítéséhez használt, de leállt
* Ellenőrizze a [állapot blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Elérte a havi kvótát, az adatokat? Nyissa meg a beállítások/kvóta és az árazás a regisztrációval. Ha igen, frissítse a csomagot, vagy további kapacitást kell fizetnie. Tekintse meg a [séma árképzési](https://azure.microsoft.com/pricing/details/application-insights/).

#### <a name="i-dont-see-all-the-data-im-expecting"></a>I vagyok várt adatok nem látható
* Nyissa meg a kvóták és panel megnyitásához, és ellenőrizze e árképzési [mintavételi](app-insights-sampling.md) működik. (100 %-os átviteli azt jelenti, hogy a mintavételi műveletben nem.) Az Application Insights szolgáltatás csak a azon részét, az alkalmazásból érkező telemetriai adatok fogadásához állítható be. Ez segítséget nyújt tartásához telemetriai adatot a havi kvótán belül. 

## <a name="no-usage-data"></a>Nincs használati adatok
**Kérelmek és válaszidejét, de nincs lapmegtekintés, böngésző vagy felhasználói adatok láthatók.**

Sikeresen állít be az alkalmazás telemetriai adatokat küldhet a kiszolgálóról. Most a következő lépés [telemetriai adatokat küldhet a webböngészőből beállítása a weblapok][usage].

Azt is megteheti Ha az ügyfél az alkalmazásban egy [telefon vagy más eszköz][platforms], ott telemetriai adatokat küldhet. 

Az ügyfél- és telemetria létrehozásához használja a instrumentation ugyanazzal a kulccsal. Az adatok jelennek meg az ugyanazon az Application Insights-erőforrás, és az ügyfél és kiszolgáló események összefüggéseket képes lesz.


## <a name="disabling-telemetry"></a>Telemetria letiltása
**Hogyan letilthatja a telemetriai adatok gyűjtemény?**

A kódban:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Vagy** 

Frissítés ApplicationInsights.xml (a a Projekt erőforrások mappában). Adja hozzá a következő, a legfelső szintű csomópontja alatt:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

XML módszert használ, akkor az alkalmazás újraindítása, ha megváltoztatja az értéket.

## <a name="changing-the-target"></a>A tároló módosítása
**Hogyan lehet módosítani a projekt adatokat küld az Azure erőforrás?**

* [Az új erőforrás instrumentation kulcs lekérése.][java]
* Az Application Insights hozzáadni a projekthez az Azure-eszközkészlet használata az eclipse-ben, ha a webes projekt kattintson a jobb gombbal, válassza ki **Azure**, **konfigurálja az Application Insights**, és módosítsa a kulcsot.
* Ellenkező esetben frissítése a kulcsot a ApplicationInsights.xml a Projekt erőforrások mappában.

## <a name="debug-data-from-the-sdk"></a>A hibakeresési adatokat az SDK-ból

**Hogyan tudhatom meg, hogy az SDK tevékenységeit?**

Mi történik az API-t a kapcsolatos további információk beszerzéséhez vegye fel a `<SDKLogger/>` ApplicationInsights.xml konfigurációs fájljának legfelső szintű csomópontja alatt.

Azt is beállíthatja, hogy a kimeneti fájlba naplózó:

```XML

    <SDKLogger type="FILE">
      <enabled>True</enabled>
      <UniquePrefix>JavaSDKLog</UniquePrefix>
    </SDKLogger>
```

A fájlok alatt található `%temp%\javasdklogs` vagy `java.io.tmpdir` esetén Tomcat kiszolgálót.


## <a name="the-azure-start-screen"></a>Az Azure kezdőképernyőn
**Vagyok megnézi [az Azure-portálon](https://portal.azure.com). Nem a térkép arról, hogy valami kapcsolatos alkalmazásom?**

Azure-kiszolgálók állapotának nem, a világ különböző mutatja.

*Az Azure start tábláról (kezdőképernyő) hogyan található adatok az alkalmazással kapcsolatos?*

Feltéve, hogy [az alkalmazás beállítása az Application Insights][java], kattintson a Tallózás gombra, válassza ki az Application Insights, és válassza ki az alkalmazáshoz létrehozott alkalmazás-erőforrást. A beolvasandó nincs gyorsabb a jövőben, rögzíthető az alkalmazást a start táblán.

## <a name="intranet-servers"></a>Intranetes kiszolgálók
**Képes a kiszolgáló is figyelése intranetről?**

Igen, megadva, a kiszolgáló telemetriai adatokat küldhet az Application Insights portálon találja meg a nyilvános interneten keresztül. 

A tűzfal akkor előfordulhat, hogy nyissa meg a 80-as és a kimenő forgalom dc.services.visualstudio.com és f5.services.visualstudio.com 443-as TCP-portot.

## <a name="data-retention"></a>Adatmegőrzés
**Mennyi ideig adatok őrződnek meg a portálon? Az biztonságos?**

Lásd: [az adatmegőrzés és az adatvédelmi][data].

## <a name="next-steps"></a>Következő lépések
**Az Application Insights saját Java server alkalmazás beállítása I. Mit tehetek?**

* [A weblapok rendelkezésre állásának figyelése][availability]
* [Weblap megfigyeléséhez][usage]
* [Használat követése és az eszköz alkalmazások problémák diagnosztizálása][platforms]
* [Kód írása az alkalmazás használati nyomkövetéséhez][track]
* [Diagnosztikai naplók rögzítése][javalogs]

## <a name="get-help"></a>Segítségkérés
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md

