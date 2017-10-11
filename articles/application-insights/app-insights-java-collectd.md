---
title: "Linux - Azure Java web app teljesítményére figyelése |} Microsoft Docs"
description: "Bővített alkalmazásteljesítmény-figyelés a Java-webhely beépülő modul CollectD az Application insights szolgáltatással."
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: bwren
ms.openlocfilehash: 4ea917b068e0242bfb88d7357eca032607a43a3f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd: Linux teljesítménymutatók az Application Insightsban


Linux rendszer teljesítménymutatók a felfedezése [Application Insights](app-insights-overview.md), telepítse [collectd](http://collectd.org/)együtt az Application Insights beépülő modult,. A nyílt forráskódú megoldás különböző rendszer és a hálózati statisztikákat gyűjt.

Általában fogja használni collectd, ha már rendelkezik [a Java webes szolgáltatás az Application insights szolgáltatással tagolva][java]. Ez lehetővé teszi több adat segítséget az alkalmazás a teljesítmény növelése és a problémák diagnosztizálásához. 

![a minta diagramok](./media/app-insights-java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>A rendszerállapot-kulcs beszerzése
Az a [Microsoft Azure-portálon](https://portal.azure.com), nyissa meg a [Application Insights](app-insights-overview.md) erőforrás, ahol azt szeretné, hogy az adatok jelennek meg. (Vagy [hozzon létre egy új erőforrást](app-insights-create-new-resource.md).)

Igénybe vehet a instrumentation kulcs, amely azonosítja az erőforrás egy példányát.

![Keresse meg az összes, nyissa meg az erőforrást, majd az Essentials legördülő válassza ki, és a Instrumentation kulcs másolása](./media/app-insights-java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Collectd és a beépülő modul telepítése
A Linux server gépeken:

1. Telepítés [collectd](http://collectd.org/) 5.4.0 verzió vagy újabb.
2. Töltse le a [Application Insights collectd író beépülő modul](https://aka.ms/aijavasdk). Megjegyzés: a verziószám.
3. A beépülő modul JAR történő másolás `/usr/share/collectd/java`.
4. Szerkesztés `/etc/collectd/collectd.conf`:
   * Győződjön meg arról, hogy [a Java beépülő modul](https://collectd.org/wiki/index.php/Plugin:Java) engedélyezve van.
   * Frissítse a JVMArg a java.class.path számára a következő JAR felvenni. A letöltött egyeznie a verziószám frissítéséhez:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adja hozzá ezt a kódrészletet, az erőforrás Instrumentation kulccsal:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Íme egy példa konfigurációs fájl részeként:

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

Konfigurálja az egyéb [collectd beépülő modulok](https://collectd.org/wiki/index.php/Table_of_Plugins), amelyek különböző adatokat gyűjthet különböző forrásokból származó.

Indítsa újra a következők szerint collectd a [manuális](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Az adatok megtekintése az Application Insightsban
Nyissa meg az Application Insights-erőforrás [Metrikaböngésző, és adja hozzá a diagramok][metrics], a metrikák meg szeretné tekinteni a egyéni kategóriából kiválasztása.

![](./media/app-insights-java-collectd/result.png)

Alapértelmezés szerint a metrikák összesítése, amelyből a metrikák gyűjtött összes állomás számítógépével. A metrikák állomásonként, a diagram részletei panelen megtekintéséhez kapcsolja be a csoportosítás, és válassza a CollectD-állomás szerint kell csoportosítani.

## <a name="to-exclude-upload-of-specific-statistics"></a>Az adott statisztika feltöltése kizárása
Alapértelmezés szerint az Application Insights beépülő modul elküldi az engedélyezett collectd "read" beépülő modulok által gyűjtött összes adat. 

Adatok kizárandó konkrét beépülő modulok, illetve az adatforrások:

* Szerkessze a konfigurációs fájlt. 
* A `<Plugin ApplicationInsightsWriter>`, adja hozzá a direktíva sorokat:

| Irányelv | Következmény |
| --- | --- |
| `Exclude disk` |Zárja ki által gyűjtött összes adat a `disk` beépülő modul |
| `Exclude disk:read,write` |Kizárása a nevű `read` és `write` a a `disk` beépülő modul. |

Külön irányelvek és egy új sor.

## <a name="problems"></a>Problémákat tapasztal?
*Nem szerepel az adatok a portálon*

* Nyissa meg [keresési] [ diagnostic] érkezett-e a nyers események megjelenítéséhez. Egyes esetekben tovább tartanak metrikaböngésző jelennek meg.
* Szükség lehet [tűzfalkivételeket a kimenő adatok beállítása](app-insights-ip-addresses.md)
* Nyomkövetés engedélyezése a az Application Insights beépülő modult. Adja hozzá a sort belül `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Nyissa meg egy terminált, és indítsa el a collectd a kapcsoló ütközik a jelentések megtekintéséhez:
  * `sudo collectd -f`

## <a name="known-issue"></a>Ismert hiba

Az Application Insights írási beépülő modul nem kompatibilis a bizonyos olvasási beépülő modulok. Néhány beépülő modulok néha küldése "NaN", ahol az Application Insights beépülő modul egy lebegőpontos számot vár.

Jelenség: A collectd napló mutatja, amely tartalmazza az "Eszközintelligencia:... SyntaxError: váratlan lexikális elem N ".

Megkerülő megoldás: A probléma írási beépülő modulok által gyűjtött adatok kihagyása. 

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md


