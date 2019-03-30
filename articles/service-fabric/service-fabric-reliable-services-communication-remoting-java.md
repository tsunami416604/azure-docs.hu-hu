---
title: Szolgáltatás távelérésének lehetővé tétele az Azure Service Fabric Java használatával |} A Microsoft Docs
description: Remoting Service Fabric lehetővé teszi az ügyfelek és a szolgáltatások a távoli eljáráshívás segítségével a Java-szolgáltatásokkal kommunikálni.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 51c8c689bd3fe3e8967bab77e776ad02f9cb59f1
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667966"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Java Reliable Services és a szolgáltatás távelérésének lehetővé tétele
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

A szolgáltatások, amelyek egy adott kommunikációs protokollt vagy a veremben, mint például a WebAPI, a Windows Communication Foundation (WCF) vagy más, nem kapcsolódik a Reliable Services-keretrendszer gyorsan és egyszerűen beállítása a távoli eljáráshívások távoli eljáráshívás mechanizmust biztosít szolgáltatások.  Ez a cikk ismerteti, hogyan állítható be a távoli eljáráshívások írt Java-szolgáltatások.

## <a name="set-up-remoting-on-a-service"></a>Állítsa be a szolgáltatás távelérésének lehetővé tétele
Szolgáltatás távelérésének lehetővé tétele beállítása két egyszerű lépésben történik:

1. Hozzon létre egy kapcsolatot a szolgáltatás megvalósítása. Toto rozhraní definuje módszerek érhetők el a szolgáltatást a távoli eljáráshívás. A módszerek kell lennie, a feladat-adatszolgáltató aszinkron módszereket. A felület musí implementovat `microsoft.serviceFabric.services.remoting.Service` , hogy jelezze, hogy a szolgáltatás távelérésének lehetővé tétele felülettel rendelkezik.
2. A szolgáltatás egy távoli eljáráshívás figyelő használja. Ez egy `CommunicationListener` megvalósítása, amely távoli eljáráshívás képességeket biztosít. `FabricTransportServiceRemotingListener` az alapértelmezett távelérési átviteli protokollal távoli eljáráshívás figyelő létrehozásához használható.

A következő állapotmentes szolgáltatás például a "Hello World" beszerezhet egy távoli eljáráshívási egyetlen metódus közzététele.

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
> Az argumentumok és a szolgáltatás felületén a návratové typy lehet olyan egyszerű, bonyolult vagy egyéni típusokat, de a szerializálható kell lennie.
>
>

## <a name="call-remote-service-methods"></a>A távoli szolgáltatás metódusok meghívása
Metódusok meghívása a szolgáltatás a távoli eljáráshívás stack használatával a helyi proxyn keresztül a szolgáltatás segítségével történik a `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` osztály. A `ServiceProxyBase` módszer létrehoz egy helyi proxykiszolgáló ugyanazt a felületet, amely megvalósítja a szolgáltatás által. A proxyk egyszerűen hívása módszerek kapcsolaton távolról.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

A távoli eljáráshívás keretrendszer tölti ki az ügyfélnek a szolgáltatásban fellépő kivételek. Használatával az ügyfél tehát kivételkezelés logikai `ServiceProxyBase` közvetlenül kezelheti a kivételeket, amelyek a szolgáltatás jelez.

## <a name="service-proxy-lifetime"></a>Szolgáltatási Proxy élettartamát
ServiceProxy létrehozása egy könnyen használható művelet, így annyi igény szerint hozhat létre. Szolgáltatási Proxy példányok felhasználhatók, amennyiben azok szükségesek. A távoli eljáráshívás kivételt jelez, ha továbbra is felhasználhatja a proxy-példányt. Minden egyes ServiceProxy a hálózaton keresztül üzenetek küldéséhez használt kommunikációs ügyfél tartalmazza. Vzdálená volání meghívása, során annak megállapításához, hogy a kommunikáció ügyfél érvényes belső ellenőrzések elvégzése. Ezen ellenőrzés eredménye alapján a szükség esetén újból a a kommunikációt ügyfél. Ezért, ha kivétel történik, akkor nem kell hozza létre újra `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory Lifetime
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) van egy másik távoli eljáráshívás adapterek proxy létrehozó gyári. Ha az API-val `ServiceProxyBase.create` proxy létrehozására, majd keretrendszer létrehoz egy `FabricServiceProxyFactory`.
Akkor hasznos, hozzon létre egyet manuálisan Ha felül kell bírálnia [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) tulajdonságait.
Gyári drága művelet. `FabricServiceProxyFactory` gyorsítótár-ügyfelek kommunikációt kezeli.
Ajánlott eljárás, hogy a gyorsítótár `FabricServiceProxyFactory` , amíg.

## <a name="remoting-exception-handling"></a>Távoli eljáráshívás kivételkezelés
A távoli kivétel szolgáltatás API-ja által okozott RuntimeException vagy FabricException az ügyfélnek érkeznek.

ServiceProxy kezeli a szolgáltatás-partíció létrehozása az összes feladatátvételi kivétel. Ha feladatátvételi Exceptions(Non-Transient Exceptions) újra megoldja a végpontok és a hívást a megfelelő végpontra való próbálkozások. A feladatátvételi kivétel újrapróbálkozások számát kötelező befejezésére való határozatlan idejű.
TransientExceptions, esetén csak újrapróbálkozik a hívást.

Alapértelmezett újrapróbálkozási paraméterek a következők szerint paramétert [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Ezeket az értékeket konfigurálhatja OperationRetrySettings objektum ServiceProxyFactory konstruktorának való átadásával.

## <a name="next-steps"></a>További lépések
* [Kommunikáció a Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
