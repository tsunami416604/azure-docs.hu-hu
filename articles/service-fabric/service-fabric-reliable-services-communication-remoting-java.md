---
title: A Java használatát Azure Service Fabric szolgáltatás távoli eljáráshívási |} Microsoft Docs
description: Service Fabric távoli eljáráshívási lehetővé teszi, hogy az ügyfelek és a szolgáltatások távoli eljáráshívás segítségével Java szolgáltatásokkal kommunikálni.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 3215ee4adf907524626b4919b637ce23b9e0e782
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750180"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>A Java a Reliable Services szolgáltatás távelérése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Szolgáltatások, amelyek egy adott kommunikációs protokollt vagy a veremben, mint például a WebAPI, a Windows Communication Foundation (WCF) vagy más, nem kapcsolódik a Reliable Services keretrendszer lehetővé teszi a távoli eljáráshívás gyorsan és egyszerűen állíthat be a távoli eljáráshívások szolgáltatások.  A cikkből megtudhatja, hogyan állíthat be a Java-szolgáltatásokat a távoli eljáráshívásokat.

## <a name="set-up-remoting-on-a-service"></a>A szolgáltatás távoli eljáráshívást beállítani
A szolgáltatás távoli eljáráshívás beállítása két egyszerű lépésben történik:

1. Hozzon létre egy felület, a szolgáltatás megvalósítása. Ez az interfész határozza meg a módszereket, amelyek a szolgáltatás a távoli eljáráshívás érhetők el. A módszerek kell lennie a feladatot visszaadó aszinkron módszereket. Az illesztőfelületet kell megvalósítania `microsoft.serviceFabric.services.remoting.Service` jelezze, hogy a szolgáltatás távoli eljáráshívási felülettel rendelkezik.
2. A szolgáltatás egy távoli eljáráshívás figyelő használja. Ez egy `CommunicationListener` megvalósítása, amely távoli eljáráshívási képességeket biztosít. `FabricTransportServiceRemotingListener` az alapértelmezett távelérési átviteli protokollal távoli eljáráshívási figyelő létrehozásához használható.

A következő állapotmentes szolgáltatások például egy távoli eljáráshívással működik a "Hello, World" beolvasandó egyetlen metódus közzététele.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Az argumentumok és visszatérési értékének típusa a szolgáltatási felület lehet bármely egyszerű, bonyolult vagy egyéni típusa, de elemnek szerializálhatónak kell lennie.
>
>

## <a name="call-remote-service-methods"></a>Távoli szolgáltatás metódushívások
A szolgáltatás a helyi proxyként metódusok meghívása a szolgáltatás a távoli eljáráshívás verem segítségével történik a `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` osztály. A `ServiceProxyBase` hoz létre egy helyi proxykiszolgáló ugyanazon a felületen, amely a szolgáltatás használatával. A proxybeállítások egyszerűen hívása módszerek illesztőn az távoli.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

A távoli eljáráshívás keretrendszer kivételek, a szolgáltatás, az ügyfél propagálása zajlik. Ezért kivételkezelést logika használatával az ügyfél `ServiceProxyBase` közvetlenül kezelheti a kivételeket, amelyek a szolgáltatás jelez.

## <a name="service-proxy-lifetime"></a>Szolgáltatási Proxy élettartamát
Egy egyszerűsített művelet ServiceProxy létrehozása, hogy létrehozhasson annyi, szükség szerint. Szolgáltatási Proxy példányok felhasználhatók, mindaddig, amíg azok szükségesek. A távoli eljáráshívás kivételt jelez, ha továbbra is felhasználhatja a proxy-példányt. Minden egyes ServiceProxy a hálózaton keresztül üzenetek küldéséhez használt kommunikációs ügyfél tartalmazza. Hogy a távoli hívásokat, miközben belső a rendszer ellenőrzi annak megállapításához, hogy a kommunikáció ügyfél érvényes. Ezen ellenőrzés eredménye alapján, a kommunikáció ügyfél újból létrejön szükség esetén. Ezért, ha kivétel történik, nem kell hozza létre újra `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory Lifetime
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) van olyan adat-előállítóval, amely különböző távelérési kapcsolatok proxy hoz. Ha API-t használja `ServiceProxyBase.create` proxy létrehozására, majd keretrendszer létrehoz egy `FabricServiceProxyFactory`.
Akkor célszerű manuálisan létrehozhat egyet az felül kell bírálni [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory) tulajdonságait.
Gyári során drága. `FabricServiceProxyFactory` kommunikáció az ügyfelek gyorsítótárában megtalálhatók.
Bevált gyakorlat az, hogy a gyorsítótár `FabricServiceProxyFactory` , amíg.

## <a name="remoting-exception-handling"></a>Távoli eljáráshívás kivételkezelést
A távoli kivétel lépett fel az service API rendszer küldi vissza az ügyfél RuntimeException vagy FabricException.

ServiceProxy a szolgáltatás partíció létrejön az összes feladatátvételi kivétel kezelése. Újra feloldja a végpontok feladatátvételi Exceptions(Non-Transient Exceptions) esetén és a megfelelő végponttal hívás újrapróbálja a telepítést. Feladatátvétel kivétel újrapróbálkozások száma: nincs meghatározva.
TransientExceptions, esetén csak újrapróbálkozik a hívást.

Alapértelmezett újrapróbálkozási paraméterei [OperationRetrySettings] által biztosított. (https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) Ezeket az értékeket konfigurálhatja úgy, hogy OperationRetrySettings objektum ServiceProxyFactory konstruktor.

## <a name="next-steps"></a>További lépések
* [A Reliable Services kommunikáció biztonságához](service-fabric-reliable-services-secure-communication-java.md)
