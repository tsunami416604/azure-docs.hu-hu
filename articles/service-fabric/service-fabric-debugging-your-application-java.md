---
title: Az eclipse-ben az Azure Service Fabric-alkalmazás hibakeresése |} A Microsoft Docs
description: A megbízhatóság és a szolgáltatások teljesítményét javítása fejlesztésekor és hibakeresésekor őket az Eclipse-ben egy helyi fejlesztési fürtön.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 2f00636da2b29e7815569a683fdf51c6a4e3b0e0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880299"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Az Eclipse-szel Java Service Fabric-alkalmazás hibakeresése
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. A lépéseket követve indítsa el a helyi fejlesztési fürt [a Service Fabric fejlesztési környezet beállítása](service-fabric-get-started-linux.md).

2. Frissítse a szeretne végezni, úgy, hogy ez a java-folyamatot a távoli hibakeresési paraméterekkel szolgáltatás entryPoint.sh. Ez a fájl a következő helyen található: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`. Ebben a példában a 8001-es port van beállítva a hibakereséshez.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Az alkalmazásjegyzék frissítéséhez példányszámát vagy replikaszámát a szolgáltatás a replika száma 1-re állításával. Ezzel a beállítással elkerülheti, hogy ütközések alakuljanak ki a hibakereséshez használt portnál. Állapotmentes szolgáltatások esetén például állítsa be az `InstanceCount="1"` értéket, állapotalapú szolgáltatások esetén pedig állítsa be a cél és a minimális replikakészlet méretét 1-re a következőképpen: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Telepítse az alkalmazást.

5. Válassza ki az Eclipse IDE-ben **Futtatás -> konfigurációk hibakeresése -> Távoli Java-alkalmazás és a bemeneti kapcsolat tulajdonságai** állítsa be a tulajdonságokat a következőképpen:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Állítson be töréspontokat a kívánt pontokat, és az alkalmazás hibakereséséhez.

Ha az alkalmazás összeomlik, is érdemes coredumps engedélyezéséhez. Hajtsa végre `ulimit -c` a rendszerhéj, és a 0 értéket adja vissza, akkor a coredumps nincsenek engedélyezve. Korlátlan számú coredumps engedélyezéséhez hajtsa végre a következő parancsot: `ulimit -c unlimited`. Azt is ellenőrizheti az állapotát a parancs `ulimit -a`.  Ha szeretne, frissítse a coredump generációs elérési útját, hajtsa végre a `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`. 

### <a name="next-steps"></a>További lépések

* [Naplók gyűjtése az Linuxos Azure Diagnostics használatával](service-fabric-diagnostics-how-to-setup-lad.md).
* [A szolgáltatások helyi monitorozása és diagnosztizálása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
