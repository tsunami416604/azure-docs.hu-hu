---
title: Az eclipse-ben az Azure Service Fabric-alkalmazás hibakeresése |} Microsoft Docs
description: Tovább fejlesztheti megbízhatóságának és teljesítményének a szolgáltatások fejlesztéséhez és a helyi fejlesztési fürtök Hibakeresés az eclipse-ben őket.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: timlt
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 0e9e816fa84816b1b5d12f066dc65aee7b4930f7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Az Eclipse használata Java Service Fabric-alkalmazás hibakeresése
> [!div class="op_single_selector"]
> * [A Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Indítsa el a helyi fejlesztési fürtök lépéseit követve [a Service Fabric fejlesztési környezet létrehozása](service-fabric-get-started-linux.md).

2. A szolgáltatás kívánt debug, úgy, hogy ez a java-folyamat távoli hibakeresési paraméterekkel entryPoint.sh frissítése. Ez a fájl a következő helyen található: ``ApplicationName\ServiceNamePkg\Code\entrypoint.sh``. Ebben a példában a 8001-es port van beállítva a hibakereséshez.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=y -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Az Application Manifest frissítése a példányainak számát vagy a szolgáltatást, amely az írásával a replika száma 1 értékre állításával. Ezzel a beállítással elkerülheti, hogy ütközések alakuljanak ki a hibakereséshez használt portnál. Állapotmentes szolgáltatások esetén például állítsa be az ``InstanceCount="1"`` értéket, állapotalapú szolgáltatások esetén pedig állítsa be a cél és a minimális replikakészlet méretét 1-re a következőképpen: `` TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

4. Az alkalmazás telepítéséhez.

5. Válassza ki az Eclipse IDE **Futtatás Debug konfigurációk -> -> Java-alkalmazások és a kapcsolat tulajdonságai bemeneti** és a tulajdonságok az alábbiak szerint állíthatja:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Állítson be töréspontokat a kívánt pontokon, és az alkalmazás hibakeresése.

Ha az alkalmazás összeomló, is érdemes lehet coredumps engedélyezéséhez. Végrehajtás ``ulimit -c`` a rendszerhéj, és ha 0 értéket adja vissza, majd coredumps nem engedélyezettek. Ahhoz, hogy a korlátlan coredumps, hajtsa végre a következő parancsot: ``ulimit -c unlimited``. Azt is ellenőrizheti a folyamat állapotát a parancs ``ulimit -a``.  Ha szeretne frissíteni a coredump generációs elérési útja, a végrehajtást ``echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern``. 

### <a name="next-steps"></a>További lépések

* [Linux Azure Diagnostics használatával naplógyűjtéshez](service-fabric-diagnostics-how-to-setup-lad.md).
* [Figyelése és diagnosztizálása helyileg szolgáltatások](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
