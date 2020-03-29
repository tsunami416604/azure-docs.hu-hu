---
title: Az Azure Service Fabric-alkalmazások hibakeresése Linux alatt
description: Ismerje meg, hogyan figyelheti és diagnosztizálhatja a Service Fabric-szolgáltatásokat egy helyi Linux-fejlesztőgépen.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d8b5ec2f2190586f5eced5eee112b190a82504c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526294"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Szolgáltatások figyelése és diagnosztizálása helyi Linux-gépfejlesztési környezetben


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

A figyelés, észlelés, diagnosztika és hibaelhárítás lehetővé teszi, hogy a szolgáltatások a felhasználói élmény minimális megszakításával folytatódjanak. A figyelés és a diagnosztika kritikus fontosságú a ténylegesüzembe helyezett éles környezetben. Egy hasonló modell elfogadása a szolgáltatások fejlesztése során biztosítja, hogy a diagnosztikai folyamat működik, amikor éles környezetbe költözik. A Service Fabric megkönnyíti a szolgáltatásfejlesztők számára a diagnosztika megvalósítását, amely zökkenőmentesen működik mind az egygépes helyi fejlesztési beállítások, mind a valós éles fürtbeállítások között.


## <a name="debugging-service-fabric-java-applications"></a>Szolgáltatásfabric Java-alkalmazások hibakeresése

Java alkalmazások esetén [több naplózási keretrendszer](https://en.wikipedia.org/wiki/Java_logging_framework) is elérhető. Mivel `java.util.logging` a JRE alapértelmezett beállítása, a [GitHub kódpéldáihoz](https://github.com/Azure-Samples/service-fabric-java-getting-started)is használható. A következő vita a `java.util.logging` keretrendszer konfigurálásának módját ismerteti.

A java.util.logging segítségével az alkalmazásnaplókat átirányíthatja a memóriába, a kimeneti adatfolyamokba, a konzolfájlokba vagy a szoftvercsatornákba. Ezen beállítások mindegyikéhez vannak már a keretrendszerben megadott alapértelmezett kezelők. Létrehozhat egy `app.properties` fájlt, amely úgy konfigurálja a fájlkezelőt, hogy az alkalmazás az összes naplót átirányítsa egy helyi fájlba.

A következő kódrészlet egy példakonfigurációt tartalmaz:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

A `app.properties` fájl által mutatott mappának léteznie kell. A `app.properties` fájl létrehozása után módosítania kell a belépési `<applicationfolder>/<servicePkg>/Code/` pont parancsfájlját `java.util.logging.config.file` is `app.properties` a mappában, `entrypoint.sh` hogy a tulajdonságot fájlba állítsa. A bejegyzésnek a következő kódrészlethez hasonlóan kell kinéznie:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Ez a konfiguráció azt eredményezi, hogy a `/tmp/servicefabric/logs/`naplók at forgó módon gyűjtik össze a rendszeren belül. Ebben az esetben a naplófájl neve mysfapp%u.%g.log, ahol:
* **%u** egy egyedi szám az egyidejű Java-folyamatok közötti ütközések feloldásához.
* **%g** A forgó naplók megkülönböztetésére alkalmas generálási szám.

Alapértelmezés szerint, ha nincs explicit módon konfigurálva kezelő, a konzolkezelő regisztrálva van. A logokat a /var/log/syslog alatt tekintheti meg a syslog.One can view the logs in syslog under /var/log/syslog.

További információt a [GitHub kódpéldáiban](https://github.com/Azure-Samples/service-fabric-java-getting-started)talál.


## <a name="debugging-service-fabric-c-applications"></a>Szolgáltatásfabric C# alkalmazásának hibakeresésé


Több keretrendszer áll rendelkezésre a CoreCLR-alkalmazások linuxos nyomon követéséhez. További információ: [GitHub: logging](http:/github.com/aspnet/logging).  Mivel az EventSource ismerős a C# fejlesztők számára,'ez a cikk az EventSource-ot használja a CoreCLR-minták Linuxon történő nyomon követéséhez.

Az első lépés a System.Diagnostics.Tracing használata, hogy a naplókat memória- vagy kimeneti adatfolyamokba vagy konzolfájlokba írhassa.  Az EventSource használatával történő naplózáshoz adja hozzá a következő projektet a project.json hoz:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Egy egyéni EventListener segítségével figyelheti a szolgáltatásesemény, és majd megfelelően átirányítja őket a fájlok nyomon követésére. A következő kódrészlet az EventSource használatával történő naplózás mintaimplementációját és egy egyéni EventListener-t jeleníti meg:


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


Az előző kódrészlet a naplókat egy `/tmp/MyServiceLog.txt`fájlba adja ki a rendszerben. Ezt a fájlnevet megfelelően frissíteni kell. Abban az esetben, ha a naplókat konzolra szeretné átirányítani, használja a következő kódrészletet a testreszabott EventListener osztályban:

```csharp
public static TextWriter Out = Console.Out;
```

A [c# minták](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) minták at EventSource és egy egyéni EventListener egy fájlba események naplózásához.



## <a name="next-steps"></a>További lépések
Az alkalmazáshoz hozzáadott nyomkövetési kód is együttműködik az alkalmazás egy Azure-fürtön az alkalmazás diagnosztikájával. Tekintse meg ezeket a cikkeket, amelyek ismertetik az eszközök különböző lehetőségeit, és ismertetik, hogyan állíthatja be őket.
* [Naplók gyűjtése az Azure Diagnostics segítségével](service-fabric-diagnostics-how-to-setup-lad.md)
