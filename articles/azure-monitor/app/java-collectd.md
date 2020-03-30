---
title: Java webalkalmazás teljesítményének figyelése Linuxon – Azure | Microsoft dokumentumok
description: A Java-webhely kiterjesztett alkalmazásteljesítmény-figyelése az Application Insights Összegyűjthetési beépülő moduljával.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 687f97c305bffdfb408feb314ccded4f93ac574a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660733"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>összegyűjtött: Linux teljesítménymutatók az Application Insightsban


A Linux rendszer teljesítménymutatóinak megismeréséhez [az Application Insightsban](../../azure-monitor/app/app-insights-overview.md)telepítse az [összegyűjtött](https://collectd.org/), az Application Insights beépülő modullal együtt. Ez a nyílt forráskódú megoldás különböző rendszer- és hálózati statisztikákat gyűjt.

Általában akkor fogja használni a gyűjtött, ha már [műszeres a Java webszolgáltatás Application Insights][java]. Több adatot ad, amelyek segítenek az alkalmazás teljesítményének növelésében vagy a problémák diagnosztizálásában. 

## <a name="get-your-instrumentation-key"></a>Szerezd meg a műszerkulcs
A [Microsoft Azure portalon](https://portal.azure.com)nyissa meg az [Application Insights](../../azure-monitor/app/app-insights-overview.md) erőforrást, ahol meg szeretné jelenjenek az adatok. (Vagy [hozzon létre egy új erőforrást.)](../../azure-monitor/app/create-new-resource.md )

Készítsen másolatot a műszerezési kulcsról, amely azonosítja az erőforrást.

![Böngésszen az összes, nyissa meg az erőforrást, majd az Essentials legördülő menüben válassza ki és másolja a Instrumentation key-t](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>Telepítse a begyűjtött és a beépülő modult
Linux szervergépeken:

1. Telepítse az 5.4.0-s vagy újabb [verziót.](https://collectd.org/)
2. Töltse le az [Application Insights összegyűjtött író plugin](https://github.com/microsoft/ApplicationInsights-Java/tree/master/collectd/src/main/java/com/microsoft/applicationinsights/collectd/internal). Jegyezze fel a verziószámot.
3. Másolja a plugin `/usr/share/collectd/java`JAR-ba .
4. Szerkesztés `/etc/collectd/collectd.conf`:
   * Győződjön meg arról, hogy [a Java beépülő modul](https://collectd.org/wiki/index.php/Plugin:Java) engedélyezve van.
   * Frissítse a JVMArg a java.class.path, hogy tartalmazza a következő JAR. Frissítse a verziószámot a letöltött verziószámnak megfelelően:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adja hozzá ezt a kódrészletet az erőforrás instrumentation kulcsával:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

A mintakonfigurációs fájl része:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Konfigurálja más [gyűjtött bővítményeket](https://collectd.org/wiki/index.php/Table_of_Plugins), amelyek különböző adatokat gyűjthetnek különböző forrásokból.

Indítsa újra a [kézikönyv](https://collectd.org/wiki/index.php/First_steps)szerint gyűjtött .

## <a name="view-the-data-in-application-insights"></a>Az adatok megtekintése az Application Insightsban
Az Application Insights-erőforrásban nyissa meg [a metrikákat és adja hozzá a diagramokat,][metrics]és jelölje ki az egyéni kategóriában megtekinteni kívánt mutatókat.

Alapértelmezés szerint a metrikák összesítve vannak az összes olyan gazdagépen, amelyről a metrikákat gyűjtötték. A gazdagépenkénti mutatók megtekintéséhez a Diagram részletei panelen kapcsolja be a csoportosítást, majd válassza a Csoportosítás t.

## <a name="to-exclude-upload-of-specific-statistics"></a>Konkrét statisztikák feltöltésének kizárása
Alapértelmezés szerint az Application Insights beépülő modul elküldi az összes összegyűjtött adatot az összes engedélyezett összegyűjtött "olvasási" bővítmény. 

Adatok kizárása adott bővítményekből vagy adatforrásokból:

* A konfigurációs fájl szerkesztése. 
* A `<Plugin ApplicationInsightsWriter>`alkalmazásban adja hozzá az ehhez hasonló direktívasorokat:

| Irányelv | Hatás |
| --- | --- |
| `Exclude disk` |A bővítmény által `disk` gyűjtött összes adat kizárása |
| `Exclude disk:read,write` |Zárja ki `read` a `write` megnevezett forrásokat és a `disk` bővítményből. |

Külön irányelvek egy új vonallal.

## <a name="problems"></a>Problémákat tapasztal?
*Nem látok adatokat a portálon*

* Nyissa meg a Keresés című [témakört,][diagnostic] és nézze meg, hogy megérkeztek-e a nyers események. Néha hosszabb időt vesz igénybe, hogy megjelenjen a metrikák explorer.
* Előfordulhat, hogy [tűzfalkivételeket kell beállítania a kimenő adatokhoz](../../azure-monitor/app/ip-addresses.md)
* Engedélyezze a nyomkövetést az Application Insights beépülő modulban. Adja hozzá `<Plugin ApplicationInsightsWriter>`ezt a sort a következő be:
  * `SDKLogger true`
* Nyisson meg egy terminált, és kezdje el a gyűjtést részletes módban, hogy láthassa az általa jelentett problémákat:
  * `sudo collectd -f`

## <a name="known-issue"></a>Ismert probléma

Az Application Insights Write beépülő modul nem kompatibilis bizonyos Olvasási beépülő modulokkal. Egyes bővítmények néha "NaN"-t küldenek, ahol az Application Insights bővítmény lebegőpontos számot vár.

Jelenség: A begyűjtött napló olyan hibákat jelenít meg, amelyek tartalmazzák a következőket: "AI: ... Szintaxishiba: Váratlan n token".

Megoldás: A probléma által gyűjtött adatok kizárása Az Írás beépülő modulok. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


