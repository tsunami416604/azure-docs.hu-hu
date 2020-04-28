---
title: A Java szolgáltatás használata az Azure-ban Service Fabric
description: Service Fabric a távelérés lehetővé teszi, hogy az ügyfelek és szolgáltatások távoli eljáráshívás használatával kommunikáljanak a Java-szolgáltatásokkal.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: eef63d7a2c8a4b15938dfbffd7db5f9d1b22d426
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75426635"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Szolgáltatás-távelérés a javában Reliable Services
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Olyan szolgáltatások esetén, amelyek nem kapcsolódnak egy adott kommunikációs protokollhoz vagy veremhez (például WebAPI, Windows Communication Foundation (WCF) vagy másokhoz), a Reliable Services keretrendszer egy távelérési mechanizmust biztosít, amellyel gyorsan és egyszerűen állíthatja be a távoli eljáráshívás szolgáltatásait.  Ez a cikk azt ismerteti, hogyan állítható be a távoli eljáráshívás a Javával írt szolgáltatásokhoz.

## <a name="set-up-remoting-on-a-service"></a>Távelérés beállítása a szolgáltatáson
Egy szolgáltatás távelérésének beállítása két egyszerű lépésben történik:

1. Hozzon létre egy felületet a szolgáltatás megvalósításához. Ez az interfész határozza meg a szolgáltatásban távoli eljárás hívásához elérhető módszereket. A metódusoknak aszinkron metódusok tevékenység-visszaküldési módszereknek kell lenniük. A csatolónak meg `microsoft.serviceFabric.services.remoting.Service` kell valósítania annak jelzésére, hogy a szolgáltatásnak van-e távelérési felülete.
2. Használjon távelérési figyelőt a szolgáltatásban. Ez egy olyan `CommunicationListener` implementáció, amely távelérési funkciókat biztosít. `FabricTransportServiceRemotingListener`a távelérés-figyelőt az alapértelmezett távelérési átviteli protokoll használatával lehet létrehozni.

A következő állapot nélküli szolgáltatás például egyetlen metódust tesz elérhetővé, amely távoli eljáráshívás esetén ""Helló világ!"alkalmazás" beolvasását teszi lehetővé.

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
> A szolgáltatás felületének argumentumai és visszatérési típusai lehetnek egyszerű, összetett vagy egyéni típusok, de szerializálható kell lenniük.
>
>

## <a name="call-remote-service-methods"></a>Távoli szolgáltatási módszerek hívása
Ha a távelérési verem használatával metódusokat hív meg egy szolgáltatáson, a szolgáltatás a `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` osztályon keresztül helyi proxy használatával végezhető el. A `ServiceProxyBase` metódus egy helyi proxyt hoz létre ugyanazzal a kezelőfelülettel, amelyet a szolgáltatás implementál. Ezzel a proxyval egyszerűen hívhatja a metódusokat az illesztőfelületen távolról.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

A távelérési keretrendszer a szolgáltatásban a-ügyfél felé irányuló kivételeket propagálja. Így a kivételek kezelése a-ügyfélen a `ServiceProxyBase` használatával közvetlenül kezelheti a szolgáltatás által kiváltott kivételeket.

## <a name="service-proxy-lifetime"></a>Szolgáltatási proxy élettartama
A ServiceProxy létrehozása egy könnyű művelet, így tetszőleges számú igényt hozhat létre. A Service proxy példányai a szükséges ideig újra felhasználhatók. Ha egy távoli eljáráshívás kivételt jelez, továbbra is használhatja ugyanazt a proxy-példányt. Minden ServiceProxy tartalmaz egy kommunikációs ügyfelet, amely üzenetek küldésére szolgál a vezetékes hálózaton keresztül. A távoli hívások meghívása során a rendszer belső ellenőrzéseket végez annak meghatározására, hogy a kommunikációs ügyfél érvényes-e. Az ellenőrzések eredményei alapján a kommunikációs ügyfél újból létrejön, ha szükséges. Ezért ha kivétel történik, nem kell újból létrehoznia `ServiceProxy`.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory élettartama
A [FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) egy olyan gyár, amely különböző távelérési felületek számára hoz létre proxyt. Ha API `ServiceProxyBase.create` -t használ a proxy létrehozásához, a Framework `FabricServiceProxyFactory`létrehoz egy-t.
A [ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) tulajdonságainak felülbírálásához hasznos lehet manuálisan létrehozni.
A gyár költséges művelet. `FabricServiceProxyFactory`megőrzi a kommunikációs ügyfelek gyorsítótárát.
Az ajánlott eljárás a gyorsítótárazás `FabricServiceProxyFactory` a lehető leghosszabbra.

## <a name="remoting-exception-handling"></a>Távelérési kivételek kezelését
A Service API által kiváltott összes távoli kivételt visszaküldi az ügyfélnek RuntimeException vagy FabricException.

A ServiceProxy kezeli az összes feladatátvételi kivételt a számára létrehozott szolgáltatási partícióhoz. Újra feloldja a végpontokat, ha vannak feladatátvételi kivételek (nem átmeneti kivételek), és újrapróbálkozik a hívást a megfelelő végponttal. A feladatátvételi kivételek újrapróbálkozások száma határozatlan.
TransientExceptions esetén csak újra próbálkozik a hívással.

Az alapértelmezett újrapróbálkozási paramétereket a [OperationRetrySettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings)biztosítanak.
Ezeket az értékeket úgy is konfigurálhatja, hogy átadja a OperationRetrySettings objektumot a ServiceProxyFactory konstruktornak.

## <a name="next-steps"></a>További lépések
* [A Reliable Services kommunikációjának biztonságossá tétele](service-fabric-reliable-services-secure-communication-java.md)
