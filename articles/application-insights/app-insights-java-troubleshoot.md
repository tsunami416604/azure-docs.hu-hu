---
title: Hibaelhárítás az Application Insights Java webes projekt
description: Hibaelhárítási útmutató - figyelési élő Java-alkalmazásokhoz az Application insights szolgáltatással.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6b3205603b91077ca2c3226dcb78589de37d15cf
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Hibaelhárítás, kérdések és válaszok: Application Insights Java-hoz
Kérdések és problémák [Azure Application Insights Java nyelven][java]? Az alábbiakban néhány tipp.

## <a name="build-errors"></a>Build hibák
**Az eclipse-ben vagy Intellij Idea, az Application Insights SDK Maven vagy a gradle-lel hozzáadásakor build vagy ellenőrzőösszeg érvényesítési hiba jelenik meg.**

* Ha a függőség <version> elem minta használatával helyettesítő karaktereket is tartalmazó (pl. (Maven) `<version>[2.0,)</version>` vagy (Gradle) `version:'2.0.+'`), próbáljon meg egy adott verziójához helyette például `2.0.1`. Tekintse meg a [kibocsátási megjegyzéseket](https://github.com/Microsoft/ApplicationInsights-Java/releases) legújabb verziójának.

## <a name="no-data"></a>Nincsenek adatok
**I Application Insights hozzáadása sikeresen befejeződött, és saját alkalmazás futott, de soha nem láthatta, szeretnék adatokat a portálon.**

* Várjon egy percet, és kattintson a frissítés parancsra. A két diagramot rendszeresen frissíteni magukat, de manuálisan is frissítheti. A frissítési időköz az időtartományt a diagram függ.
* Ellenőrizze, hogy rendelkezik-e egy instrumentation kulcsot (mappában az erőforrásokat a projekt) ApplicationInsights.xml fájlban meghatározott vagy a környezeti változó konfigurált.
* Győződjön meg arról, hogy nincs `<DisableTelemetry>true</DisableTelemetry>` csomópont az XML-fájlban.
* A tűzfal akkor előfordulhat, hogy nyissa meg a 80-as és a kimenő forgalmat dc.services.visualstudio.com 443-as TCP-portot. Tekintse meg a [tűzfalkivételeket teljes listája](app-insights-ip-addresses.md)
* A Microsoft Azure-ban indítsa el a tábla, és nézze meg a szolgáltatástérkép állapotát. Ha néhány riasztási jelzések, várjon, amíg azok vissza kellett volna OK majd zárja be és nyissa meg újra az Application Insights-alkalmazás paneljének.
* Engedélyezze a naplózást az IDE konzolablak hozzáadásával egy `<SDKLogger />` elem a ApplicationInsights.xml fájlban (a erőforrások a projekt), és a bejegyzéseket végrehajtásával kerüli meg AI a legfelső szintű csomópontjában: információ/figyelmeztetés/hiba bármilyen gyanús naplókhoz.
* Győződjön meg arról, hogy a megfelelő ApplicationInsights.xml fájl sikeresen betöltötte a Java SDK-t, ha megnézi a konzol kimeneti üzenetek "konfigurációs fájl sikeresen talált" utasítás.
* Ha a konfigurációs fájl nem található, ellenőrizze a kimenet megtekintéséhez, ahol a konfigurációs fájl keres a, és győződjön meg arról, hogy a ApplicationInsights.xml azokat a keresési helyek egyikén található. A szokásos megoldás, mint a konfigurációs fájl elhelyezheti az Application Insights SDK JARs közelében. Például: a Tomcat, ez azt jelentené a WEBALKALMAZÁS-INF/osztályok mappát. A fejlesztés során elhelyezheti ApplicationInsights.xml a webes projekt erőforrások mappára.
* Is találhat [GitHub problémák oldal](https://github.com/Microsoft/ApplicationInsights-Java/issues) az ismert problémák az SDK-val.
* Ellenőrizze, hogy az Application Insights mag, webalkalmazás, ügynök és naplózási appenders ugyanazon verzióját használja bármely verziója ütközés problémák elkerülése érdekében.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Adatok megjelenítéséhez használt, de leállt
* Ellenőrizze a [állapot blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Elérte a havi kvótát, az adatokat? Nyissa meg a beállítások/kvóta és az árazás a regisztrációval. Ha igen, frissítse a csomagot, vagy további kapacitást kell fizetnie. Tekintse meg a [séma árképzési](https://azure.microsoft.com/pricing/details/application-insights/).
* Ön nemrég frissítette az SDK? Győződjön meg arról, hogy a projekt könyvtárán belül jelen-e csak egyedi SDK JAR-fájlok kivételével. Nem lehet jelen SDK két különböző verziója.
* Van szüksége, a megfelelő AI erőforrást? Adjon meg kell egyeznie az alkalmazás az erőforráshoz, amennyiben várt telemetriai iKey. Azonos szintűeknek kell lenniük.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>I vagyok várt adatok nem látható
* Nyissa meg a használati és a becsült költség lap és ellenőrzés e [mintavételi](app-insights-sampling.md) működik. (100 %-os átviteli azt jelenti, hogy a mintavételi műveletben nem.) Az Application Insights szolgáltatás csak a azon részét, az alkalmazásból érkező telemetriai adatok fogadásához állítható be. Ez segítséget nyújt tartásához telemetriai adatot a havi kvótán belül. 
* Rendelkezik-e kapcsolva SDK mintavételi? Ha igen, akkor az adatok, a megfelelő esetében kamatláb mintát venni.
* A Java SDK régebbi verzióját futtató? 2.0.1 verziójától kezdve, azt vezettek tartalék tolerancia mechanizmus időszakos hálózati és a háttérkiszolgáló hibák, valamint a helyi meghajtón adatmegőrzés kezelésére.
* Ön első szabályozott miatt túlzott telemetriai? Ha bekapcsolja a naplózási adatokat, jelenik meg a napló "App halmozódni" üzenet. Az aktuális határértéke 32 KB-os telemetriai elemek/mp.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java-ügynök nem rögzíti a függőségi adatokat
* Ön konfigurált Java ügynök következő [Java-ügynök konfigurálása](app-insights-java-agent.md) ?
* Győződjön meg arról, hogy a java-ügynök jar, mind az Eszközintelligencia-Agent.xml fájl ugyanabba a mappába kerülnek.
* Győződjön meg arról, hogy az automatikus-gyűjtése kívánt függőségi automatikus gyűjtemény esetén támogatott. Jelenleg csak támogatott MySQL, MsSQL, Oracle-adatbázis és a Redis Cache-gyorsítótár-függőség gyűjtemény.
* Használ JDK 1.7 vagy 1.8? Jelenleg nem támogatjuk függőségi gyűjtemény JDK 9.

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

**Or** 

Frissítés ApplicationInsights.xml (a a Projekt erőforrások mappában). Adja hozzá a következő, a legfelső szintű csomópontja alatt:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

XML módszert használ, akkor az alkalmazás újraindítása, ha megváltoztatja az értéket.

## <a name="changing-the-target"></a>A tároló módosítása
**Hogyan lehet módosítani a projekt adatokat küld az Azure erőforrás?**

* [Az új erőforrás instrumentation kulcs lekérése.][java]
* Az Application Insights hozzáadni a projekthez az Azure-eszközkészlet használata az eclipse-ben, ha a webes projekt kattintson a jobb gombbal, válassza ki **Azure**, **konfigurálja az Application Insights**, és módosítsa a kulcsot.
* Ha konfigurálta az a Instrumentation kulcs környezeti változó, frissítse a környezeti változó értékének új iKey.
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

## <a name="debug-logging"></a>Hibakeresési naplózás
Az Application Insights használ `org.apache.http`. Ez az Application Insights core JAR-fájlok kivételével a wsrmp belül más helyre `com.microsoft.applicationinsights.core.dependencies.http`. Ez lehetővé teszi, hogy az Application Insights helyzetek kezelésére, ahol különböző verziói azonos `org.apache.http` egy kódbázis szerepel. 

>[!NOTE]
>Ha engedélyezi a hibakeresési webhelyszintű naplózás az összes névtérhez az alkalmazásban, az szembeni szerződéses kötelezettségeket beleértve az összes végrehajtás alatt álló modul által `org.apache.http` előzményfájlként `com.microsoft.applicationinsights.core.dependencies.http`. Az Application Insights nem tudnak szűrést az ilyen hívást, mert a napló hívás kezdeményezése történik az Apache Library alkalmazni. HIBAKERESÉSI webhelyszintű naplózás naplóadatokat jelentős mennyiségű eredményez, és nem javasolt éles példányok.


## <a name="next-steps"></a>További lépések
**Az Application Insights saját Java server alkalmazás beállítása I. Mit tehetek?**

* [A weblapok rendelkezésre állásának figyelése][availability]
* [Weblap megfigyeléséhez][usage]
* [Használat követése és az eszköz alkalmazások problémák diagnosztizálása][platforms]
* [Kód írása az alkalmazás használati nyomkövetéséhez][track]
* [Diagnosztikai naplók rögzítése][javalogs]

## <a name="get-help"></a>Segítségkérés
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* [Probléma fájlt a Githubon](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md

