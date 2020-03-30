---
title: Szolgáltatás-smoting Java használatával az Azure Service Fabric
description: A Service Fabric távoli eljáráshívással kommunikálhat a Java-szolgáltatásokkal.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: eef63d7a2c8a4b15938dfbffd7db5f9d1b22d426
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426635"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Szolgáltatás-átirányító java-ban megbízható szolgáltatásokkal
> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Olyan szolgáltatások esetében, amelyek nem kötődnek egy adott kommunikációs protokollhoz vagy veremhez, például a WebAPI, a Windows Kommunikációs Alapítvány (WCF) vagy mások esetében, a Megbízható szolgáltatások keretrendszer egy távoli eljáráshívások gyors és egyszerű beállításához biztosít távoli eljáráshívásokat. Szolgáltatások.  Ez a cikk a Java-val írt szolgáltatások távoli eljáráshívásainak beállítását ismerteti.

## <a name="set-up-remoting-on-a-service"></a>Átirányító beállítás szolgáltatáson
A szolgáltatás ramoteltállítása két egyszerű lépésben történik:

1. Hozzon létre egy felületet a szolgáltatás számára. Ez a felület határozza meg azokat a módszereket, amelyek a szolgáltatás távoli eljáráshívásához rendelkezésre állnak. A metódusok kell feladat-visszatérő aszinkron módszerek. Az interfésznek `microsoft.serviceFabric.services.remoting.Service` azt a jelzést kell végrehajtania, hogy a szolgáltatás nak van egy átirányító interfésze.
2. Használjon egy moting figyelőt a szolgáltatásban. Ez egy `CommunicationListener` olyan megvalósítás, amely átirányító képességeket biztosít. `FabricTransportServiceRemotingListener`segítségével létrehozhat egy mutatókapcsolati figyelőt az alapértelmezett átirányítási átviteli protokoll használatával.

Például a következő állapotmentes szolgáltatás egyetlen módszert tesz elérhetővé a "Hello World" távoli eljáráshíváson keresztül.

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
> A szolgáltatásfelület argumentumai és visszatérési típusai bármilyen egyszerű, összetett vagy egyéni típusúak lehetnek, de szerializálhatónak kell lenniük.
>
>

## <a name="call-remote-service-methods"></a>Távoli szolgáltatásmetódusok hívása
A szolgáltatás hívási metódusai a szolgáltatás on the remoting stack `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` használatával történik egy helyi proxy a szolgáltatás az osztályon keresztül. A `ServiceProxyBase` metódus helyi proxyt hoz létre a szolgáltatás által megvalósított felület használatával. Ezzel a proxy, akkor egyszerűen hívja módszerek a felület távolról.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

A kapcsolati átirányító keretrendszer a szolgáltatáshoz kidobott kivételeket propagálja az ügyfélnek. Így a kivételkezelési logika az `ServiceProxyBase` ügyfél használatával közvetlenül kezelni kivételeket, hogy a szolgáltatás dob.

## <a name="service-proxy-lifetime"></a>Szolgáltatásproxy élettartama
ServiceProxy létrehozása egy könnyű művelet, így hozhat létre annyi, amennyire szüksége van. A szolgáltatásproxy-példányok addig használhatók fel, amíg szükség van rájuk. Ha egy távoli eljáráshívás kivételt eredményez, akkor is újra felhasználhatja ugyanazt a proxypéldányt. Minden ServiceProxy tartalmaz egy kommunikációs ügyfél használható üzenetekküldésére a hálózaton keresztül. Távoli hívások meghívása közben a rendszer belső ellenőrzéseket végez annak megállapítására, hogy a kommunikációs ügyfél érvényes-e. Az ellenőrzések eredményei alapján a kommunikációs ügyfél szükség esetén újra létrejön. Ezért ha kivétel történik, nem kell újra `ServiceProxy`létrehoznia.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory élettartama
[A FabricServiceProxyFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) egy olyan gyár, amely proxyt hoz létre a különböző kapcsolatkezelő felületekhez. Ha az `ServiceProxyBase.create` API-t proxy létrehozásához `FabricServiceProxyFactory`használja, a keretrendszer létrehoz egy .
Hasznos, ha manuálisan hoz létre egyet, ha felül kell írnia [a ServiceRemotingClientFactory](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) tulajdonságokat.
A gyár egy drága művelet. `FabricServiceProxyFactory`a kommunikációs ügyfelek gyorsítótárát tartja fenn.
A legjobb gyakorlat `FabricServiceProxyFactory` az, hogy cache, ameddig csak lehetséges.

## <a name="remoting-exception-handling"></a>A kapcsolatonkénti kivétel kezelése
A szolgáltatás API által okozott összes távoli kivétel torkig lesz az ügyfélnek RuntimeException vagy FabricException néven.

A ServiceProxy kezeli az összes feladatátvételi kivételt ahhoz a szolgáltatáspartícióhoz, amelyhez létrehozták. Újra feloldja a végpontokat, ha van feladatátvételi kivétel (nem átmeneti kivételek), és újrapróbálkozik a hívás a megfelelő végponttal. A feladatátvételi kivétel újrapróbálkozásainak száma határozatlan.
Átmenetikivételek esetén csak újrapróbálkozik a hívással.

Az alapértelmezett újrapróbálkozási paramétereket az [OperationRetrySettings határozza](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings)meg.
Ezeket az értékeket úgy konfigurálhatja, hogy átadja az OperationRetrySettings objektumot a ServiceProxyFactory konstruktornak.

## <a name="next-steps"></a>További lépések
* [Kommunikáció biztonságossá tétele megbízható szolgáltatásokhoz](service-fabric-reliable-services-secure-communication-java.md)
