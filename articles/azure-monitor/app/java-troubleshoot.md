---
title: Alkalmazáselemzési adatok – problémamegoldás Java webes projektben
description: Hibaelhárítási útmutató – élő Java-alkalmazások figyelése az Application Insights segítségével.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 04e98938bc5dd17816ae873f122073212275a414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657180"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Hibaelhárítás, kérdések és válaszok: Application Insights Java-hoz
Kérdések vagy problémák az [Azure Application Insights Java-ban?][java] Íme néhány tipp.

## <a name="build-errors"></a>Build hibák
**Az Eclipse vagy intellij ötlet, amikor hozzáadja az Application Insights SDK keresztül Maven vagy Gradle, kapok build vagy ellenőrzőösszeg érvényesítési hibákat.**

* Ha a `<version>` függőségi elem helyettesítő karakterekkel (pl. (Maven) `<version>[2.0,)</version>` vagy (Gradle) `version:'2.0.+'`rendelkező mintát használ, `2.0.1`próbáljon meg egy adott verziót megadni a következőhöz hasonlóan: . Tekintse meg a legújabb verzió [kiadási megjegyzéseket.](https://github.com/Microsoft/ApplicationInsights-Java/releases)

## <a name="no-data"></a>Nincs adat
**Sikeresen felvettem az Application Insightsot, és futtattam az alkalmazást, de még soha nem láttam adatokat a portálon.**

* Várjon egy percet, és kattintson a Frissítés gombra. A diagramok rendszeresen frissülnek, de manuálisan is frissíthetők. A frissítési időköz a diagram időtartományáttól függ.
* Ellenőrizze, hogy rendelkezik-e az ApplicationInsights.xml fájlban (a projekt erőforrásmappájában) vagy a Környezeti változóban definiált instrumentation kulccsal.
* Ellenőrizze, hogy `<DisableTelemetry>true</DisableTelemetry>` nincs-e csomópont az XML-fájlban.
* Előfordulhat, hogy a tűzfalon meg kell nyitnia a 80-as és a 443-as TCP-portokat, hogy a kimenő forgalom dc.services.visualstudio.com. A [tűzfalkivételek teljes listájának](../../azure-monitor/app/ip-addresses.md) megtekintése
* A Microsoft Azure start tábláján tekintse meg a szolgáltatás állapotleképezését. Ha vannak riasztási jelzések, várja meg, amíg azok visszatértek az OK gombra, majd zárja be, majd nyissa meg újra az Application Insights alkalmazás panel.
* [A naplózás bekapcsolása:](#debug-data-from-the-sdk) az `<SDKLogger />` ApplicationInsights.xml fájl gyökércsomópontja alatti elem hozzáadásával (a projekt erőforrásmappájában), és ellenőrizze, hogy vannak-e olyan bejegyzések, amelyek et az AI: INFO/WARN/ERROR elemekkel előnéznek a gyanús naplók miatt. 
* Győződjön meg arról, hogy a Java SDK sikeresen betöltötte a megfelelő ApplicationInsights.xml fájlt, és megnézi a konzol kimeneti üzeneteit a "A konfigurációs fájl sikeresen megtalálható" utasítást.
* Ha a konfigurációs fájl nem található, ellenőrizze a kimeneti üzeneteket, hogy hol keresse a konfigurációs fájlt, és győződjön meg arról, hogy az ApplicationInsights.xml fájl az egyik ilyen keresési helyen található. Ökölszabályként a konfigurációs fájlt az Application Insights SDK JARs közelében helyezheti el. Például: a Tomcat ban ez a WEB-INF/classes mappát jelenti. A fejlesztés során az ApplicationInsights.xml fájlt a webes projekt erőforrásmappájába helyezheti.
* Kérjük, tekintse meg a GitHub problémák oldalon az Ismert problémák az SDK.Please also look at [GitHub issues page](https://github.com/Microsoft/ApplicationInsights-Java/issues) for known issues with the SDK.
* Győződjön meg arról, hogy az Application Insights mag, a web, az ügynök és a naplózási appenders ugyanazon verzióját használja a verzióütközési problémák elkerülése érdekében.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Régebben láttam az adatokat, de leállt
* Ellenőrizze az [állapot blog](https://blogs.msdn.com/b/applicationinsights-status/).
* Elérte az adatpontok havi kvótáját? Nyissa meg a Beállítások/Kvóta és az Árképzés lehetőséget. Ha igen, frissítheti a csomagot, vagy fizethet a további kapacitásért. Tekintse meg az [árképzési rendszert](https://azure.microsoft.com/pricing/details/application-insights/).
* Frissítette mostanában az SDK-t? Győződjön meg arról, hogy csak egyedi SDK-üvegek vannak jelen a projektkönyvtárban. Az SDK-nak nem lehet két különböző verziója.
* A megfelelő AI-erőforrást keresi? Kérjük, egyezik az alkalmazás iKey-je az erőforrás, ahol telemetriai adatokat vár. Ugyanolyannak kell lenniük.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Nem látom az összes várt adatot
* Nyissa meg a Használat és a becsült költség lapot, és ellenőrizze, hogy a [mintavétel működik-e.](../../azure-monitor/app/sampling.md) (A 100%-os átvitel azt jelenti, hogy a mintavétel nem működik.) Az Application Insights szolgáltatás beállítható, hogy elfogadja csak egy töredéke a telemetriai, hogy érkezik az alkalmazásból. Ez segít a telemetriahavi kvótáján belül maradni.
* Be van kapcsolva az SDK mintavételezése? Ha igen, az adatok mintavételezése az összes vonatkozó típusra megadott sebességgel történne.
* A Java SDK egy régebbi verzióját futtatja? A 2.0.1-es verziótól kezdve hibatűrő mechanizmust vezettünk be az időszakos hálózati és háttérhibák, valamint a helyi meghajtókon lévő adatmegőrzés kezelésére.
* A túlzott telemetria miatt megfojtják? Ha bekapcsolja az INFO naplózást, megjelenik egy "Az alkalmazás szabályozása" naplóüzenet. A jelenlegi korlát 32k telemetriai elemek/másodperc.

### <a name="java-agent-cannot-capture-dependency-data"></a>A Java-ügynök nem tudja rögzíteni a függőségi adatokat
* Konfigurálta a Java-ügynököt a [Java-ügynök konfigurálása](java-agent.md) szerint?
* Győződjön meg arról, hogy mind a java agent jar, mind az AI-Agent.xml fájl ugyanabba a mappába kerül.
* Győződjön meg arról, hogy az automatikusan gyűjtést megkísérelt függőség támogatja az automatikus gyűjtést. Jelenleg csak a MySQL, az MsSQL, az Oracle DB és az Azure Cache for Redis függőségi gyűjteményt támogatjuk.

## <a name="no-usage-data"></a>Nincsenek használati adatok
**A kérelmekre és a válaszidőkre vonatkozó adatokat látok, de nincs oldalmegtekintés, böngésző vagy felhasználói adat.**

Sikeresen beállította az alkalmazást, hogy telemetriát küldjön a kiszolgálóról. Most a következő lépés az, hogy [állítsa be a weboldalakat, hogy küldjön telemetriai adatokat a böngészőből][usage].

Azt is megteheti, ha az ügyfél egy alkalmazás egy [telefon vagy más eszköz,][platforms]küldhet telemetriai adatokat onnan.

Használja ugyanazt a instrumentation kulcsot az ügyfél és a kiszolgáló telemetria beállításához. Az adatok ugyanabban az Application Insights-erőforrásban jelennek meg, és az ügyfélről és a kiszolgálóról származó eseményekkorrelkapcsolatos.


## <a name="disabling-telemetry"></a>Telemetrialetiltása
**Hogyan tudom letiltani a telemetriai gyűjteményt?**

Kódban:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Vagy**

Frissítse az ApplicationInsights.xml fájlt (a projekt erőforrásmappájában). Adja hozzá a következőket a gyökércsomópont hoz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Az XML-módszerrel újra kell indítani az alkalmazást az érték módosításakor.

## <a name="changing-the-target"></a>A cél módosítása
**Hogyan módosíthatom, hogy a projektem melyik Azure-erőforrásnak küld adatokat?**

* [Az új erőforrás műszerezési kulcsának beszerezni.][java]
* Ha az Azure Toolkit for Eclipse használatával adta hozzá az Application Insights-ot a projekthez, kattintson a jobb gombbal a webes projektre, válassza az **Azure**, **Az Application Insights konfigurálása**lehetőséget, és módosítsa a kulcsot.
* Ha a Instrumentation kulcsot környezeti változóként konfigurálta, frissítse a környezeti változó értékét az új iKey-vel.
* Ellenkező esetben frissítse a kulcsot az ApplicationInsights.xml fájlban a projekt erőforrásmappájában.

## <a name="debug-data-from-the-sdk"></a>Az SDK-ból származó adatok hibakeresése

**Hogyan tudom meg, mit csinál az SDK?**

Ha további információra van kíváncsi az API-ban zajló eseményekről, adja hozzá `<SDKLogger/>` az ApplicationInsights.xml konfigurációs fájl gyökércsomópontja alá.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml fájl

Azt is utasíthatja a naplózóval, hogy adja ki egy fájlba:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Tavaszi boot starter

Ha engedélyezni szeretné az SDK-naplózást a Spring Boot Alkalmazásalkalmazásokkal az Application Insights tavaszi rendszerindítási starter használatával, adja hozzá a következőket a `application.properties` fájlhoz:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

vagy normál hibára nyomtatva:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java ügynök

A JVM-ügynök naplózásának engedélyezése az [AI-Agent.xml fájl frissítéséhez](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Java parancssori tulajdonságok
_A 2.4.0-s verzió óta_

A naplózás engedélyezése parancssori beállításokkal, konfigurációs fájlok módosítása nélkül:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

vagy normál hibára nyomtatva:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Az Azure kezdőképernyője
**Az [Azure portalt](https://portal.azure.com)nézem. Mond valamit a térkép az alkalmazásommal kapcsolatban?**

Nem, az Azure-kiszolgálók állapotát mutatja világszerte.

*Az Azure start tábláján (kezdőképernyőn) hogyan találhatok adatokat az alkalmazásommal kapcsolatban?*

Feltéve, hogy [beállítja az alkalmazást az Application Insights számára,][java]kattintson a Tallózás gombra, válassza az Application Insights lehetőséget, és válassza ki az alkalmazáshoz létrehozott alkalmazás-erőforrást. Ahhoz, hogy a jövőben gyorsabban odaérj, rögzítheted az alkalmazásodat a kezdőtáblára.

## <a name="intranet-servers"></a>Intranetes kiszolgálók
**Figyelhetek egy kiszolgálót az intraneten?**

Igen, feltéve, hogy a kiszolgáló telemetriai adatokat küldhet az Application Insights-portálra a nyilvános interneten keresztül.

Előfordulhat, hogy a tűzfalon meg kell nyitnia a 80-as és a 443-as TCP-portokat a dc.services.visualstudio.com és f5.services.visualstudio.com kimenő forgalom hoz.

## <a name="data-retention"></a>Adatmegőrzés
**Mennyi ideig őrzi meg az adatokat a portálon? Biztonságos?**

Lásd: [Adatmegőrzés és adatvédelem][data].

## <a name="debug-logging"></a>Hibakeresési naplózás
Az Application `org.apache.http`Insights a . Ez az Application Insights alapedényein belül `com.microsoft.applicationinsights.core.dependencies.http`van áthelyezve a névtér alatt. Ez lehetővé teszi, hogy az Application `org.apache.http` Insights olyan forgatókönyveket kezeljen, amelyekben ugyanazon különböző verziói egy kódbázisban léteznek.

>[!NOTE]
>Ha engedélyezi a DEBUG szintnaplózást az alkalmazás összes névterében, akkor azt `org.apache.http` az `com.microsoft.applicationinsights.core.dependencies.http`összes végrehajtó modul tiszteletben tartja, beleértve a névre átnevezett .. Az Application Insights nem fogja tudni alkalmazni a szűrést ezekre a hívásokra, mert a naplóhívást az Apache-függvénytár kezdeményezi. A DEBUG szintnaplózás jelentős mennyiségű naplóadatot eredményez, és nem ajánlott az élő termelési példányok számára.


## <a name="next-steps"></a>További lépések
**Beállítottam az Application Insights alkalmazást a Java-kiszolgálóalkalmazáshoz. Mi mást tehetnék?**

* [A weboldalak elérhetőségének ellenőrzése][availability]
* [Weblaphasználat figyelése][usage]
* [A használati és az eszközalkalmazásokkal kapcsolatos problémák diagnosztizálása][platforms]
* [Az alkalmazás használatának nyomon követésére használt kód írása][track]
* [Diagnosztikai naplók rögzítése][javalogs]

## <a name="get-help"></a>Segítségkérés
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Probléma fájlja a GitHubon](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

