---
title: Java-WebApp linuxon – Azure teljesítményének figyelése |} A Microsoft Docs
description: Kiterjesztett alkalmazásteljesítmény-figyelés Java webhelyét az összegyűjtött beépülő modul az Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 1da207545fd98c9582aff6798f69f7ed02a02cf0
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53980792"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>összegyűjtött: Linux teljesítmény-mérőszámok az Application insights szolgáltatásban


Megismerheti a Linux rendszer teljesítmény-mérőszámokat a [Application Insights](../../application-insights/app-insights-overview.md), telepítse [összegyűjtött](https://collectd.org/)együtt az Application Insights beépülő modult,. A nyílt forráskódú megoldás különféle rendszererőforrásokat és hálózati statisztikákat gyűjti össze.

Általában fogja használni összegyűjtött, ha már rendelkezik [kialakítva az Application insights segítségével a Javás webszolgáltatások][java]. Biztosít további adatokat annak érdekében, hogy az alkalmazás teljesítményének növelése, vagy a problémák diagnosztizálásához. 

![Mintadiagramok](./media/java-collectd/sample.png)

## <a name="get-your-instrumentation-key"></a>A kialakítási kulcs beszerzése
Az a [Microsoft Azure-portálon](https://portal.azure.com), nyissa meg a [Application Insights](../../application-insights/app-insights-overview.md) erőforrás, ahol azt szeretné, hogy az adatok jelennek meg. (Vagy [hozzon létre egy új erőforrást](../../application-insights/app-insights-create-new-resource.md).)

Készítsen róla egy másolatot a kialakítási kulcs, amely azonosítja az erőforrást.

![Összes tallózása, nyissa meg az erőforrást, majd az Essentials legördülő válassza ki, és a kialakítási kulcs másolása](./media/java-collectd/02-props.png)

## <a name="install-collectd-and-the-plug-in"></a>Összegyűjtött és a beépülő modul telepítése
A Linux rendszerű kiszolgáló gépeken:

1. Telepítés [összegyűjtött](https://collectd.org/) 5.4.0-s vagy újabb.
2. Töltse le a [Application Insights összegyűjtött író beépülő modul](https://aka.ms/aijavasdk). Megjegyzés: a verziószámot.
3. A beépülő modul JAR történő másolás `/usr/share/collectd/java`.
4. Szerkesztés `/etc/collectd/collectd.conf`:
   * Ügyeljen arra, hogy [a Java-beépülő modul](https://collectd.org/wiki/index.php/Plugin:Java) engedélyezve van.
   * Frissítse a JVMArg számára a java.class.path tartalmazza a következő JAR. A letöltött egyeznie, a verziószám frissítéséhez:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Ez a kódrészlet használatával a Rendszerállapotkulcsot az erőforrás hozzáadása:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Íme egy minta konfigurációs fájl részeként:

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

Egyéb konfigurálása [összegyűjtött beépülő modulok](https://collectd.org/wiki/index.php/Table_of_Plugins), amely képes különféle forrásból gyűjthet adatokat, különböző.

Indítsa újra a következők szerint összegyűjtött annak [manuális](https://collectd.org/wiki/index.php/First_steps).

## <a name="view-the-data-in-application-insights"></a>Az adatok megtekintése az Application Insights
Nyissa meg az Application Insights-erőforrás [Metrikaböngésző, és adja hozzá a diagramok][metrics], meg szeretné tekinteni az egyéni kategóriából a metrikák kiválasztásával.

![](./media/java-collectd/result.png)

Alapértelmezés szerint a metrikák, amelyről a metrikák gyűjtött összes gazdagépeken összesítjük. A diagram részletei panelen gazdagépenként, a metrikák megjelenítéséhez kapcsolja be a csoportosítást, és válassza a csoportosítás az összegyűjtött-gazdagép.

## <a name="to-exclude-upload-of-specific-statistics"></a>Az adott statisztika feltöltését kizárása
Alapértelmezés szerint az Application Insights beépülő modul küld az engedélyezett összegyűjtött "read" beépülő modulok által gyűjtött összes adat. 

Adatok kizárása adott beépülő modulok, illetve az adatforrások:

* A konfigurációs fájl szerkesztésével. 
* A `<Plugin ApplicationInsightsWriter>`, ehhez hasonló irányelv sorok hozzáadása:

| Direktiva | Következmény |
| --- | --- |
| `Exclude disk` |Zárja ki által gyűjtött összes adat a `disk` beépülő modul |
| `Exclude disk:read,write` |A nevű adatforrások kizárása `read` és `write` származó a `disk` beépülő modult. |

Külön irányelvek és a egy új sor.

## <a name="problems"></a>Problémákat tapasztal?
*Nem látható adatok a portálon*

* Nyissa meg [keresési] [ diagnostic] megtekintheti, ha a nyers események menüpontjára. Egyes esetekben tovább tartanak a metrikaböngészőben jelennek meg.
* Szüksége lehet [tűzfalkivételeket a kimenő adatok beállítása](../../azure-monitor/app/ip-addresses.md)
* Nyomkövetés engedélyezése a az Application Insights beépülő modullal. Adja hozzá ezt a sort belül `<Plugin ApplicationInsightsWriter>`:
  * `SDKLogger true`
* Nyisson meg egy terminált, és indítsa el az összegyűjtött részletes módban, minden hibajelentés megtekintéséhez:
  * `sudo collectd -f`

## <a name="known-issue"></a>Ismert probléma

Az Application Insights írási beépülő modul nem kompatibilis a bizonyos olvasási beépülő modulokat. Néhány beépülő modulok néha küldése "NaN", ahol az Application Insights beépülő modul vár a lebegőpontos számmá.

Jelenség: Az összegyűjtött napló jeleníti meg a hibákat, amelyek tartalmazzák az "AI:... SyntaxError: Nem várt token N".

Megkerülő megoldás: A probléma írási beépülő modulok által gyűjtött adatok kihagyása. 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../application-insights/app-insights-metrics-explorer.md


