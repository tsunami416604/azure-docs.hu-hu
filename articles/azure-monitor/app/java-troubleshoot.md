---
title: A Java-webprojektekhez hibáinak elhárítása az Application Insights
description: Hibaelhárítási útmutató – az Application Insights élő Java-alkalmazások figyelése.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 1b2e1300c200619f5d1177e9761ad948c62c1239
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013808"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Hibaelhárítás, kérdések és válaszok: Application Insights Java-hoz
Kérdések és problémák [Azure Application Insights Java nyelven][java]? Íme néhány tipp.

## <a name="build-errors"></a>Felépítési hibák
**Az eclipse-ben vagy az Intellij Idea, az Application Insights SDK Maven vagy a Gradle-n keresztül való hozzáadásakor build- vagy ellenőrzőösszeg-érvényesítési hiba jelenik meg.**

* Ha a függőségi `<version>` elem van a minta használata helyettesítő karaktereket is tartalmazó (például) (Maven) `<version>[2.0,)</version>` vagy (gradle-t) `version:'2.0.+'`), például Ehelyett adja meg egy adott verziót `2.0.1`. Tekintse meg a [kibocsátási megjegyzések](https://github.com/Microsoft/ApplicationInsights-Java/releases) legújabb verziójának.

## <a name="no-data"></a>Nincs adat
**Szeretnék az Application Insights hozzáadása sikerült, és futtatta az alkalmazás, de soha nem láttam adatok a portálon.**

* Várjon egy percet, és kattintson a frissítés gombra. A diagramok rendszeres időközönként frissülnek, de manuálisan is frissítheti. A frissítési időköz az időtartományt a diagram függ.
* Ellenőrizze, hogy van-e definiálva az ApplicationInsights.xml fájlban (a Projekt erőforrások mappájához) vagy a környezeti változó konfigurált egy kialakítási kulcsot.
* Győződjön meg arról, hogy nincs `<DisableTelemetry>true</DisableTelemetry>` csomópont xml-fájlban.
* A tűzfal akkor előfordulhat, hogy nyissa meg a TCP-port a 80-as és 443-as porton a kimenő forgalmat a dc.services.visualstudio.com címre. Tekintse meg a [tűzfalkivételeket teljes listája](../../azure-monitor/app/ip-addresses.md)
* A Microsoft Azure-ban indítsa el a tábla, tekintse meg a szolgáltatástérkép állapotát. Ha egyes riasztási jelzések, várjon, amíg azok OK vissza majd zárja be és nyissa meg ismét az Application Insights-alkalmazás paneljén.
* Engedélyezze a naplózást a konzolablakban IDE hozzáadásával egy `<SDKLogger />` elem a ApplicationInsights.xml fájl (a a Projekt erőforrások mappájához), és ellenőrizze a mintametódus használ AI bejegyzéseket gyökércsomópontja alatt: ADATOK/figyelmeztetés/hiba bármely gyanús naplók.
* Győződjön meg arról, hogy a megfelelő ApplicationInsights.xml fájl sikeresen betöltötte a Java SDK-ban, a konzol kimeneti üzenetek "konfigurációs fájl sikeresen talált" utasítás esetében.
* Ha a konfigurációs fájl nem található, ellenőrizze a kimeneti üzenetek megtekintéséhez, ahol a konfigurációs fájlt a keres, és győződjön meg arról, hogy az ApplicationInsights.xml azokat a keresési helyek egyikén található. Célravezető, mint a konfigurációs fájlt az Application Insights SDK JARs közel helyezheti. Például: a Tomcat, ez azt jelentené a WEBALKALMAZÁS-INF/osztályok mappát. A fejlesztés során elhelyezheti applicationinsights.xml fájlt a webes projekt erőforrások mappájához.
* Emellett tekintse meg [GitHub hibabejelentő oldalán](https://github.com/Microsoft/ApplicationInsights-Java/issues) az ismert problémák az SDK-val.
* Ellenőrizze, hogy bármely verziójú ütközés problémák elkerülése érdekében ugyanazt a verzióját az Application Insights alapvető, webes, az ügynök és appenders naplózás használatával.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Használt adatokat, de leállt
* Ellenőrizze a [állapot blog](https://blogs.msdn.com/b/applicationinsights-status/).
* Elérte a havi kvóta adatpontok? Nyissa meg a beállítások/kvóta és díjszabás a. Ha igen, váltson magasabb szintű csomagra, vagy további kapacitást kell fizetnie. Tekintse meg a [díjszabási séma](https://azure.microsoft.com/pricing/details/application-insights/).
* Ön nemrég frissítette az SDK-t? Győződjön meg arról, hogy a projekt könyvtárában található telepítve-e csak egyedi SDK JAR-fájlok kivételével. Nem lehet jelen SDK két különböző verzióit.
* Információra van szüksége, a megfelelő AI-erőforrás? Adjon meg kell egyeznie a Rendszerállapotkulcsot az alkalmazás az erőforráshoz, ahol sebességhez telemetriai adatokat. Ezek egyeznie kell.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Felfedezhetem minden adat nem látható
* Nyissa meg a használat és becsült költségek lapon és ellenőrizze hogy [mintavételi](../../azure-monitor/app/sampling.md) van folyamatban. (100 %-os továbbítása azt jelenti, hogy a mintavétel a művelet nem.) Az Application Insights szolgáltatás beállítható csak töredéke alatt az alkalmazásból érkező telemetriai adatok fogadására. Ez lehetővé teszi a telemetria havi kvótán belül.
* SDK mintavételi engedélyezve van? Ha igen, akkor az adatok a alkalmazni típusokhoz adott díjak mintát venni.
* A Java SDK egy régebbi verzióját futtatja? 2.0.1-es verziójával kezdődően bevezettük a tartalék tolerancia mechanizmus, amellyel kezelni az időszakos hálózati és a háttér-hibák, valamint az adatmegőrzést a helyi meghajtókon.
* Vannak, első miatt szabályozza a túl sok telemetriát? Ha bekapcsolja a naplózási adatokat, látni fogja a napló "Alkalmazás szabályozva" üzenet. Az aktuális korlát 32 ezer telemetriai tétel/másodperc.

### <a name="java-agent-cannot-capture-dependency-data"></a>A Java ügynököt nem függőségi adatok rögzítéséhez.
* Konfigurálta már a Java ügynököt a következő [Java-ügynök konfigurálása](java-agent.md) ?
* Ellenőrizze, hogy a java ügynököt jar mind az AI-Agent.xml fájlt ugyanabba a mappába kerülnek.
* Győződjön meg arról, hogy a függőséget próbált automatikus – gyűjtése automatikus gyűjtemény támogatott. Csak jelenleg MySQL, MsSQL, Oracle DB és az Azure Cache Redis függőségi gyűjtemény.
* JDK 1.7-es vagy 1.8-as használunk? Jelenleg nem támogatott függőségi gyűjtemény a JDK-9.

## <a name="no-usage-data"></a>Nem használati adatok
**Kérelmek és válaszok gyorsaságát, de nincs oldal nézet, böngésző adatait, vagy felhasználói adatok láthatók.**

Sikeresen beállította az alkalmazás telemetriát küldjön a kiszolgálóról. Most a következő lépés [telemetriát küldjön a böngészőben a weblapok beállítása][usage].

Azt is megteheti Ha az ügyfél az alkalmazás egy [telefon vagy más eszköz][platforms], onnan telemetriai adatokat küldhet.

Az azonos kialakítási kulcs használatával állítsa be az ügyfél- és telemetriai adatokat. Application Insights-erőforrás jelenik meg az adatokat, és tudnia kell korrelálni események ügyfél és kiszolgáló lesz.


## <a name="disabling-telemetry"></a>Telemetria letiltása
**Hogyan tilthatom eszköztelemetria-gyűjtést?**

A kódban:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**vagy**

Frissítse az applicationinsights.xml fájlt (a a Projekt erőforrások mappájához). Adja hozzá a következő gyökércsomópontja alatt:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Az XML-metódus használatával, akkor az alkalmazás újraindítása, ha módosítja az értéket.

## <a name="changing-the-target"></a>A cél módosítása
**Hogyan válthatok az Azure erőforrás-projektemmel elküldi az adatokat?**

* [Az új erőforrás kialakítási kulcs lekérése.][java]
* Ha hozzáadott Application Insights a projekthez, Eclipse-hez készült Azure-eszközkészlet használatával, kattintson a jobb gombbal a webes projektre, válassza ki **Azure**, **Application Insights konfigurálása**, és módosíthatja a kulcsot.
* Ha konfigurálta az a kialakítási kulcsot környezeti változót, frissítse a környezeti változó értékét az új Rendszerállapotkulcsot.
* Ellenkező esetben frissítse a kulcsot az applicationinsights.xml fájlt a Projekt erőforrások mappájához a.

## <a name="debug-data-from-the-sdk"></a>Az SDK adatokat hibakeresése

**Hogyan tudhatom meg, milyen módon az SDK-t?**

Mi történik az API-t a további adatokat kérhet le, adjon hozzá `<SDKLogger/>` konfigurációs ApplicationInsights.xml fájl gyökércsomópontja alatt.

A kimeneti fájlba naplózó is utasíthatja:

```XML

    <SDKLogger type="FILE">
      <enabled>True</enabled>
      <UniquePrefix>JavaSDKLog</UniquePrefix>
    </SDKLogger>
```

A fájlok területen található `%temp%\javasdklogs` vagy `java.io.tmpdir` Tomcat-kiszolgáló esetén.


## <a name="the-azure-start-screen"></a>Az Azure üdvözlőképernyője
**Keresek [az Azure Portalon](https://portal.azure.com). Nem a térkép tudnivalók valami alkalmazásomat?**

Az Azure-kiszolgálók nem, a világ különböző pontjain mutatja.

*Azure helyezés tábláról (kezdőképernyő) Hogyan találhatom meg adatokat az alkalmazással kapcsolatban?*

Ha [állítsa be az alkalmazást az Application Insights][java], kattintson a Tallózás gombra, válassza az Application Insights, és válassza ki az alkalmazás az alkalmazás számára létrehozott erőforrást. Beolvasni van gyorsabban a jövőben, rögzítheti az alkalmazást a start táblához.

## <a name="intranet-servers"></a>Intranetes kiszolgálók
**A kiszolgáló figyelhetem intranetről?**

Igen, megadva, a kiszolgáló is telemetriát küldjön az Application Insights portálon a nyilvános interneten keresztül.

A tűzfal akkor előfordulhat, hogy nyissa meg a TCP-port a 80-as és 443-as porton a kimenő forgalom dc.services.visualstudio.com és f5.services.visualstudio.com számára.

## <a name="data-retention"></a>Adatmegőrzés
**Mennyi ideig őrzi meg adatok a portálon? A biztonságos?**

Lásd: [adatok megőrzésére és az adatvédelmi][data].

## <a name="debug-logging"></a>Hibakeresési naplózás
Használja az Application Insights `org.apache.http`. Ez az Application Insights alapvető JAR-fájlok kivételével a névtérben lévő más helyre `com.microsoft.applicationinsights.core.dependencies.http`. Ez lehetővé teszi, hogy az Application Insights forgatókönyvek kezeléséhez, ahol különböző verzióit azonos `org.apache.http` egyetlen kódbázis szerepel.

>[!NOTE]
>Ha engedélyezi a hibakeresési webhelyszintű naplózás az összes névtér az alkalmazásban, azt fogja figyelembe többek között az összes futó modulok által `org.apache.http` előzményfájlként `com.microsoft.applicationinsights.core.dependencies.http`. Az Application Insights nem tudja alkalmazni, mert a napló kezdeményezték a Apache Library hívások szűréséhez. HIBAKERESÉSI webhelyszintű naplózás hozzájárulnak a naplóadatok jelentős mennyiségű, és nem javasolt éles példányokhoz.


## <a name="next-steps"></a>További lépések
**Az Application Insights Java-kiszolgáló alkalmazásom beállítását I. Mit tehetek?**

* [A weblapok rendelkezésre állásának figyelése][availability]
* [Weblap-használat monitorozása][usage]
* [Használat nyomon követése, és diagnosztizálhatja a problémákat, az eszköz-alkalmazásokban][platforms]
* [Kód írása az való az alkalmazás használatának nyomon követése][track]
* [Diagnosztikai naplók rögzítése][javalogs]

## <a name="get-help"></a>Segítségkérés
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Fájl egy problémát a Githubon](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

