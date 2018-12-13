---
title: Hibakeresés az Azure Service Fabric-alkalmazások linuxon |} A Microsoft Docs
description: Ismerje meg, hogyan figyelheti és diagnosztizálhatja a Service Fabric-szolgáltatások egy helyi Linux fejlesztői gépen.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 9f0c4789e73659e5965440989c23a8cf673f7cd2
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309161"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>A helyi gép fejlesztési telepítőjének szolgáltatások monitorozása és diagnosztizálása


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Figyelés, észlelni, diagnosztizálása és hibaelhárítása lehetővé teszi a felhasználói élmény minimális megszakadását folytatásához szolgáltatások. A monitoring and diagnostics kritikusak tényleges üzembe helyezett éles környezetben. Szolgáltatások fejlesztése során egy hasonló modell bevezetése biztosítja, hogy a diagnosztikai folyamat működik-e éles környezetben való áthelyezésekor. A Service Fabric megkönnyíti a szolgáltatás-fejlesztők számára a diagnosztikai is zökkenőmentesen együttműködő egygépes helyi fejlesztési beállításokat és a való életből vett éles fürt feladatainak megvalósítása.


## <a name="debugging-service-fabric-java-applications"></a>Service Fabric Java-alkalmazások hibakeresése

Java-alkalmazások [több naplózási keretrendszerekből](http://en.wikipedia.org/wiki/Java_logging_framework) érhetők el. Mivel `java.util.logging` az alapértelmezett beállítás a JRE együtt is használható a [a GitHub-kódpéldák](http://github.com/Azure-Samples/service-fabric-java-getting-started). A következő vitafórum azt ismerteti, hogyan konfigurálhatja a `java.util.logging` keretrendszer.

Java.util.logging használatával irányíthatja át az alkalmazásnaplókat a memória, a kimeneti Stream, a konzol fájlok vagy a sockets. Ezek a beállítások mindegyike esetében nincsenek alapértelmezett kivételkezelők keretében nyújtott. Létrehozhat egy `app.properties` fájlt, hogy az alkalmazás összes naplók átirányítása egy helyi fájlba a fájl-kezelő konfigurálása.

Az alábbi kódrészlet egy példa konfiguráció tartalmazza:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Által mutatott a mappát a `app.properties` fájlnak léteznie kell. Után az `app.properties` jön létre, a belépési pont szkriptje is módosítani kell `entrypoint.sh` a a `<applicationfolder>/<servicePkg>/Code/` mappát a tulajdonság beállítása `java.util.logging.config.file` való `app.propertes` fájlt. A bejegyzés a következő kódrészlethez hasonlóan kell kinéznie:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Ezt a konfigurációt eredményez, egy rotált módon összegyűjtött naplók `/tmp/servicefabric/logs/`. A naplófájl ebben az esetben nevű mysfapp%u.%g.log ahol:
* **%u** egyedi szám, a Java egyidejű folyamatok közötti ütközések feloldása.
* **%g** naplók Elforgatás megkülönböztetésére generáció száma.

Alapértelmezés szerint ha obsluha explicit módon van konfigurálva, a konzol kezelő regisztrálva van. A naplók alapján /var/log/syslog syslog egy tekinthető meg.

További információkért lásd: a [a GitHub-kódpéldák](http://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Service Fabric C#-alkalmazások hibakeresése


A Linux-alapú alkalmazásokat coreclr-nek nyomkövetés több keretrendszerek érhetők el. További információkért lásd: [GitHub: naplózás](http:/github.com/aspnet/logging).  EventSource tisztában van-e a C# fejlesztők számára, mivel a(z) ebben a cikkben EventSource nyomkövetés coreclr-nek minták Linux rendszeren.

Az első lépés az, hogy a naplók írni memória, a kimeneti Stream vagy konzol fájlok közé tartozik a System.Diagnostics.Tracing.  A naplózás használatával EventSource, adja hozzá a következő projektet a project.json:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

Egy egyéni EventListener használatával figyeli a szolgáltatás esemény, és majd ennek megfelelően átirányítja őket a nyomkövetési fájlok. A következő kódrészlet azt mutatja be, egy minta megvalósítását EventSource, és a egy egyéni EventListener naplózás:


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


Az előző kódrészletben exportálja fájlba a naplók `/tmp/MyServiceLog.txt`. Ez a fájlnév megfelelően frissíteni kell. Abban az esetben, ha meg szeretné átirányítani a naplókat, a konzolon, a következő kódrészletet használja a testre szabott EventListener osztályban:

```csharp
public static TextWriter Out = Console.Out;
```

A minták [C# minták](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) EventSource, és a egy egyéni EventListener használatával naplózza az eseményeket egy fájlt.



## <a name="next-steps"></a>További lépések
A diagnosztika az Azure-fürtön az alkalmazás ugyanazt a nyomkövetés kódot az alkalmazáshoz hozzáadott is működik. Tekintse meg ezeket a cikkeket, amelyek tárgyalják az eszközök különböző beállításait, és bemutatják, hogyan állítsa be őket.
* [Azure Diagnostics-naplók összegyűjtése](service-fabric-diagnostics-how-to-setup-lad.md)
