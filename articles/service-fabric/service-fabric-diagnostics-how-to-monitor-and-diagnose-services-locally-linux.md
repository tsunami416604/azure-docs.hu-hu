---
title: "A Linux Azure mikroszolgáltatások hibakeresése |} Microsoft Docs"
description: "Megtudhatja, hogyan figyelése és diagnosztizálása a szolgáltatások egy helyi fejlesztési gépen a Microsoft Azure Service Fabric használatával készítettek."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 4bc73f581f4855ebc724df19dd56fab8bf103854
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Figyelése és diagnosztizálása szolgáltatásai a helyi számítógép fejlesztési beállítása


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Figyelés, észlelésére, diagnosztizálása és hibaelhárítási lehetővé teszi a felhasználói élmény minimális megszakadását folytatásához szolgáltatások. Megfigyelési és diagnosztikai kritikusak tényleges telepített éles környezetben. Egy hasonló modell bevezetése során a szolgáltatások fejlesztéséhez biztosítja, hogy a diagnosztikai csővezeték működik-e éles környezetben való áthelyezésekor. A Service Fabric megkönnyíti a szolgáltatás fejlesztők számára, amely zökkenőmentesen használható legyen a helyi fejlesztési egyszámítógépes beállításokat és a valós üzemi fürt beállítások diagnosztika megvalósításához.


## <a name="debugging-service-fabric-java-applications"></a>Service Fabric Java-alkalmazások hibakeresés

Java-alkalmazások [több naplózási keretrendszer](http://en.wikipedia.org/wiki/Java_logging_framework) érhetők el. Mivel a `java.util.logging` az alapértelmezett beállítás a JRE együtt is használható a [github-kódpéldák](http://github.com/Azure-Samples/service-fabric-java-getting-started).  A következő ismertető ismerteti, hogyan konfigurálhatja a `java.util.logging` keretrendszer.

Java.util.logging használatával is átirányítja az alkalmazásnaplók memória, a Kimeneti folyamok, a konzol fájlok vagy a sockets. Mindkét beállítás vonatkoznak-e már szerepel a keretrendszer alapértelmezett kezelők. Létrehozhat egy `app.properties` fájl konfigurálása a fájlkezelő összes napló átirányítása egy helyi fájl az alkalmazáshoz.

A következő kódrészlet egy példa konfiguráció tartalmazza:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

A mappa által hivatkozott a `app.properties` fájlnak léteznie kell. Után a `app.properties` jön létre, a belépési pont parancsfájlt is módosítani kell `entrypoint.sh` a a `<applicationfolder>/<servicePkg>/Code/` mappa a tulajdonság beállítása `java.util.logging.config.file` való `app.propertes` fájlt. A bejegyzés a következő kódrészletet hasonlóan kell kinéznie:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Ez a konfiguráció eredményez, egy forgó módon gyűjtött naplók `/tmp/servicefabric/logs/`. A naplófájl ebben az esetben nevű mysfapp%u.%g.log ahol:
* **%u** egyidejű Java-folyamatai közötti ütközések feloldása egyedi szám.
* **%g** naplók elforgatása megkülönböztetésére generációs száma.

Alapértelmezés szerint ha leíróval explicit módon van konfigurálva, a konzol kezelő regisztrálva van. A naplók /var/log/syslog a syslog egy tekinthető meg.

További információkért lásd: a [github-kódpéldák](http://github.com/Azure-Samples/service-fabric-java-getting-started).  


## <a name="debugging-service-fabric-c-applications"></a>Service Fabric C# alkalmazások hibakeresése


Több elérhetők keretrendszerek CoreCLR alkalmazások Linux nyomkövetés. További információkért lásd: [GitHub: naplózás](http:/github.com/aspnet/logging).  Mivel a EventSource ismerős a C#-fejlesztők számára "Ebben a cikkben az EventSource a nyomkövetés a Linux CoreCLR mintában.

Az első lépés annak System.Diagnostics.Tracing tartalmazza, így a memória, a Kimeneti folyamok vagy a konzol fájlok írhat a naplók.  Az EventSource naplózni, adja hozzá a következő projekt a project.json:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

Egy egyéni eseményfigyelő Visszahívásán segítségével a szolgáltatás esemény figyeli, és ezután megfelelően átirányítja őket a nyomkövetési fájlok. A következő kódrészletet a minta megvalósításának naplózás EventSource és egy egyéni eseményfigyelő Visszahívásán jeleníti meg:


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
         Out.Write(" {0} ",  Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
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


Az előző részlet kimenete egy fájlra a naplók `/tmp/MyServiceLog.txt`. Ezt a fájlnevet megfelelően frissíteni kell. Abban az esetben, ha át szeretné irányítani a naplókat a konzolon, a következő kódrészletet használja a testreszabott eseményfigyelő Visszahívásán osztályban:

```csharp
public static TextWriter Out = Console.Out;
```

A minták [C# minták](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) EventSource és egy egyéni eseményfigyelő Visszahívásán naplózza az eseményeket egy fájl segítségével.



## <a name="next-steps"></a>További lépések
A diagnosztika az alkalmazás egy Azure fürt ugyanazt a nyomkövetés kódot az alkalmazásba felvett is működik. Tekintse meg a cikkek ismertetik az eszközök a különböző lehetőségek közül, és bemutatják, hogyan állíthatja be azokat.
* [Az Azure diagnosztikai naplók gyűjtéséről](service-fabric-diagnostics-how-to-setup-lad.md)
