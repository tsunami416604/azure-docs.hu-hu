---
title: Azure Service Fabric-alkalmazások hibakeresése Linux rendszeren
description: Megtudhatja, hogyan figyelheti és diagnosztizálhatja a Service Fabric-szolgáltatásokat egy helyi linuxos fejlesztői gépen.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d8b5ec2f2190586f5eced5eee112b190a82504c3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75526294"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Szolgáltatások figyelése és diagnosztizálása helyi Linux-alapú gépek fejlesztésének beállításakor


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

A szolgáltatások figyelése, észlelése, diagnosztizálása és hibaelhárítása lehetővé teszi, hogy a szolgáltatások továbbra is minimálisan megzavarják a felhasználói élményt. A monitorozás és a diagnosztika kritikus fontosságú a tényleges üzembe helyezett éles környezetben. A szolgáltatások fejlesztése során hasonló modell bevezetése biztosítja, hogy a diagnosztikai folyamat az éles környezetbe való áttéréskor is működik. A Service Fabric megkönnyíti a szolgáltatás-fejlesztők számára a diagnosztika megvalósítását, amely zökkenőmentesen képes együttműködni mind az egyszámítógépes helyi fejlesztési környezetekben, mind a valós üzemi fürtökön.


## <a name="debugging-service-fabric-java-applications"></a>Java-alkalmazások hibakeresése Service Fabric

Java-alkalmazások esetében [több naplózási keretrendszer](https://en.wikipedia.org/wiki/Java_logging_framework) érhető el. Mivel a `java.util.logging` az alapértelmezett beállítás a JRE-ben, a [kód példákat](https://github.com/Azure-Samples/service-fabric-java-getting-started)is használ a githubban. A következő vitafórum azt ismerteti, hogyan konfigurálhatja a `java.util.logging` keretrendszert.

A Java. util. Logging használatával átirányíthatja az alkalmazás naplóit a memóriára, a kimeneti adatfolyamokra, a konzol fájljaira vagy a szoftvercsatornára. Ezen beállítások esetében a keretrendszerben már vannak alapértelmezett kezelők. `app.properties`-fájl létrehozásával konfigurálhatja az alkalmazás fájlkezelőjét az összes napló helyi fájlba való átirányításához.

A következő kódrészlet egy példa konfigurációt tartalmaz:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

A `app.properties` fájl által mutatott mappának léteznie kell. A `app.properties` fájl létrehozása után módosítania kell a belépési pont parancsfájlját is, `entrypoint.sh` a `<applicationfolder>/<servicePkg>/Code/` mappában, hogy a tulajdonságot `java.util.logging.config.file` `app.properties` fájlra állítsa be. A bejegyzésnek a következő kódrészlethez hasonlóan kell kinéznie:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Ez a konfiguráció a naplókat a következő rotációs módon gyűjti: `/tmp/servicefabric/logs/`. Ebben az esetben a naplófájl neve mysfapp% u .% g. log, ahol:
* a **(z)% u** egy egyedi szám az egyidejű Java-folyamatok közötti ütközések feloldásához.
* a **(z)% g** a létrehozási szám, amely megkülönbözteti a forgó naplók közötti különbséget.

Alapértelmezés szerint, ha nincs beállítva kezelő, a konzol kezelője regisztrálva van. Az egyik a syslog-naplókat a/var/log/syslog. alatt tekintheti meg

További információ: [példák a githubon](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Service Fabric C# alkalmazások hibakeresése


A Linuxon futó CoreCLR-alkalmazások nyomkövetéséhez több keretrendszer is rendelkezésre áll. További információt a [GitHub: naplózás](http:/github.com/aspnet/logging)című témakörben talál.  Mivel a EventSource ismerős a C# fejlesztők számára, "Ez a cikk a CoreCLR-minták EventSource használja a Linuxon.

Az első lépés a System. Diagnostics. tracing belefoglalása, hogy a naplókat a memóriába, a kimeneti adatfolyamba vagy a konzol fájljaiba lehessen írni.  A EventSource használatával történő naplózáshoz adja hozzá a következő projektet a Project. JSON fájlhoz:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Egyéni EventListener is megfigyelheti a szolgáltatási eseményt, majd megfelelően átirányíthatja őket a nyomkövetési fájlokhoz. A következő kódrészlet a EventSource és az egyéni EventListener használatával történő naplózási minta megvalósítását mutatja be:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


Az előző kódrészlet a naplókat `/tmp/MyServiceLog.txt`fájlba írja. A fájl nevét megfelelően frissíteni kell. Ha át szeretné irányítani a naplókat a konzolra, használja a következő kódrészletet a testreszabott EventListener osztályban:

```csharp
public static TextWriter Out = Console.Out;
```

A [ C# minták](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) a EventSource és az egyéni EventListener használatával naplózzák az eseményeket egy fájlba.



## <a name="next-steps"></a>Következő lépések
Az alkalmazáshoz hozzáadott nyomkövetési kód az alkalmazás diagnosztikaával is együttműködik az Azure-fürtön. Tekintse át ezeket a cikkeket, amelyek az eszközök különböző lehetőségeit tárgyalják, és leírják, hogyan kell beállítani őket.
* [Naplók összegyűjtése a Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)
