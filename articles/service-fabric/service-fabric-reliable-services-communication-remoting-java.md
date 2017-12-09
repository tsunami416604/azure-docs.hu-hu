---
title: "Az Azure Service Fabric szolgáltatás távelérése |} Microsoft Docs"
description: "A Service Fabric távoli eljáráshívás lehetővé teszi, hogy az ügyfelek és a szolgáltatások a távoli eljáráshívás segítségével szolgáltatásokkal kommunikálni."
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 51a9c8bd628ef9e65d04a3a4ddbdc127d84d4b54
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="service-remoting-with-reliable-services"></a>A Reliable Services szolgáltatás távelérése
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

A Reliable Services keretrendszer lehetővé teszi a távoli eljáráshívás szolgáltatásokhoz a távoli eljáráshívás beállítása gyorsan és egyszerűen.

## <a name="set-up-remoting-on-a-service"></a>A szolgáltatás távoli eljáráshívást beállítani
A szolgáltatás távoli eljáráshívás beállítása két egyszerű lépésben történik:

1. Hozzon létre egy felület, a szolgáltatás megvalósítása. Ez az interfész határozza meg a módszereket, amelyek a szolgáltatás a távoli eljáráshívás érhetők el. A módszerek kell lennie a feladatot visszaadó aszinkron módszereket. Az illesztőfelületet kell megvalósítania `microsoft.serviceFabric.services.remoting.Service` jelezze, hogy a szolgáltatás távoli eljáráshívási felülettel rendelkezik.
2. A szolgáltatás egy távoli eljáráshívás figyelő használja. Ez egy `CommunicationListener` megvalósítása, amely távoli eljáráshívási képességeket biztosít. `FabricTransportServiceRemotingListener`az alapértelmezett távelérési átviteli protokollal távoli eljáráshívási figyelő létrehozásához használható.

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
Egy egyszerűsített művelet ServiceProxy létrehozása, a felhasználó létrehozhat annyi, szükség szerint. Szolgáltatási Proxy újra felhasználhatók, amíg a felhasználó szükség lenne rá. Felhasználó újra használhatja a azonos proxy kivétel esetén. Minden egyes ServiceProxy a hálózaton keresztül üzenetek küldéséhez használt kommunikációs ügyfél tartalmazza. API meghívása, során tudunk belső ellenőrizze, hogy ha a használt kommunikációs ügyfél esetében érvényes. Az adott eredménye alapján, most újra létrehozzuk a kommunikációs ügyfél. Így a felhasználónak nem kell serviceproxy kivétel esetén hozza létre.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory élettartama
[FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._fabric_service_proxy_factory) van olyan adat-előállítóval, amely különböző távelérési kapcsolatok proxy hoz. Ha API-t használja `ServiceProxyBase.create` proxy létrehozására, majd keretrendszer létrehoz egy `FabricServiceProxyFactory`.
Akkor célszerű manuálisan létrehozhat egyet az felül kell bírálni [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client._service_remoting_client_factory) tulajdonságait.
Gyári során drága. `FabricServiceProxyFactory`kommunikáció az ügyfelek gyorsítótárában megtalálhatók.
Bevált gyakorlat az, hogy a gyorsítótár `FabricServiceProxyFactory` , amíg.

## <a name="remoting-exception-handling"></a>Távoli eljáráshívás kivételkezelést
A távoli kivétel lépett fel az service API rendszer küldi vissza az ügyfél RuntimeException vagy FabricException.

ServiceProxy a szolgáltatás partíció létrejön az összes feladatátvételi kivétel kezelése. Újra feloldja a végpontok feladatátvételi Exceptions(Non-Transient Exceptions) esetén és a megfelelő végponttal hívás újrapróbálja a telepítést. Feladatátvétel kivétel újrapróbálkozások száma: nincs meghatározva.
TransientExceptions, esetén csak újrapróbálkozik a hívást.

Alapértelmezett újrapróbálkozási paraméterei [OperationRetrySettings] által biztosított. (https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client._operation_retry_settings) Felhasználó OperationRetrySettings objektum ServiceProxyFactory konstruktorának való átadásával konfigurálhatja ezeket az értékeket.

## <a name="next-steps"></a>Következő lépések
* [A Reliable Services kommunikáció biztonságához](service-fabric-reliable-services-secure-communication.md)
