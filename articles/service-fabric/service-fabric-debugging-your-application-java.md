---
title: Az alkalmazás hibakeresése az Eclipse alkalmazásban
description: Javíthatja a szolgáltatások megbízhatóságát és teljesítményét azáltal, hogy helyi fejlesztési fürtön fejleszti és hibakeresést biztosít az Eclipse alkalmazásban.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614485"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Hibakeresés a Java Service Fabric alkalmazás eclipse használatával
> [!div class="op_single_selector"]
> * [Visual Stúdió/Csharp](service-fabric-debugging-your-application.md) 
> * [Napfogyatkozás/Java](service-fabric-debugging-your-application-java.md)
> 

1. Helyi fejlesztési fürt indítása a [Service Fabric-fejlesztői környezet beállítása](service-fabric-get-started-linux.md)című lépés lépéseit követve.

2. Frissítse entryPoint.sh a hibakeresést kívánt szolgáltatásról, hogy a java folyamatot távoli hibakeresési paraméterekkel indítsa el. Ez a fájl a következő `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`helyen található: . Ebben a példában a 8001-es port van beállítva a hibakereséshez.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Frissítse az alkalmazásjegyzéket úgy, hogy a hibakeresés alatt lévő szolgáltatás példányszámát vagy replikaszámát 1-re állítja. Ezzel a beállítással elkerülheti, hogy ütközések alakuljanak ki a hibakereséshez használt portnál. Állapotmentes szolgáltatások esetén például állítsa be az `InstanceCount="1"` értéket, állapotalapú szolgáltatások esetén pedig állítsa be a cél és a minimális replikakészlet méretét 1-re a következőképpen: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Az alkalmazás üzembe helyezése.

5. Az Eclipse IDE-ben válassza a **Run -> Debug Configurations -> Remote Java application és a bemeneti kapcsolat tulajdonságait, és** állítsa be a tulajdonságokat az alábbiak szerint:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Állítsa be a töréspontokat a kívánt pontokon, és hibakeresés az alkalmazás.

Ha az alkalmazás összeomlik, akkor is érdemes engedélyezni coredumps. Hajtsa végre `ulimit -c` a rendszerhéjban, és ha 0-t ad vissza, akkor a coredumps nincs engedélyezve. A korlátlan coredumps engedélyezéséhez hajtsa végre a következő parancsot: `ulimit -c unlimited`. Az állapotot a paranccsal `ulimit -a`is ellenőrizheti.  Ha frissíteni kívánta a coredump `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`létrehozási útvonalát, hajtsa végre a végrehajtását. 

### <a name="next-steps"></a>További lépések

* [Naplók gyűjtése a Linux Azure Diagnosztika használatával.](service-fabric-diagnostics-how-to-setup-lad.md)
* [A szolgáltatások helyi figyelése és diagnosztizálása.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
