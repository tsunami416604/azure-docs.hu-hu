---
title: Csatlakozás és az Azure Service Fabric szolgáltatásokkal kommunikálni |} Microsoft Docs
description: Ismerje meg, hogyan oldható fel, csatlakozni, és a Service Fabric szolgáltatásokkal kommunikálni.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 2b6fd2373a9cd0b376a6c8729d5952c5fc48ddf8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205586"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Csatlakozás és a Service Fabric szolgáltatásokkal kommunikálni
A Service Fabric szolgáltatás fut. valahol a Service Fabric-fürt, általában pontjain több virtuális géphez Akkor helyezheti át egyetlen helyről a másikra, a szolgáltatás tulajdonosa, vagy automatikusan a Service Fabric. Szolgáltatások statikusan nem kötődnek, egy adott számítógép vagy a címet.

A Service Fabric-alkalmazás általában sok különböző szolgáltatások, ahol minden szolgáltatás hajt végre egy speciális feladat tevődik össze. Ezeket a szolgáltatásokat is kommunikálhatnak egymással egy teljes funkciót, például egy webes alkalmazás megjelenítési különböző részeinek kialakításához. Van még ügyfélalkalmazások, amelyek csatlakozni és szolgáltatásokkal kommunikálni. Ez a dokumentum ismerteti a és a Service Fabric szolgáltatások közötti kommunikáció beállítása.

A Microsoft Virtual Academy videó azt is ismerteti, amelyek a szolgáltatások közötti kommunikáció: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=iYFCk76yC_6706218965">  
<img src="./media/service-fabric-connect-and-communicate-with-services/CommunicationVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="bring-your-own-protocol"></a>Kapcsolja a saját protokoll
Service Fabric segít a szolgáltatások életciklusának kezelését, de azt nem tesz, a szolgáltatások mire döntéseket. Ez magában foglalja a kommunikációt. A szolgáltatás a Service Fabric már meg van nyitva, ha ez a szolgáltatás lehetőség arra, hogy a bejövő kéréseket, használatával bármilyen kívánt protokollt vagy a kommunikációs verem végpont beállítása. A szolgáltatás figyelni fogja normál **IP:port** cím bármely címzési séma, például egy URI-t használ. Több egy szolgáltatáspéldány vagy replikák megoszthatja a gazdafolyamatokon ebben az esetben azok kell eltérő portok használatára, vagy használjon egy port megosztása mechanizmus, például a http.sys kernel-illesztőprogram a Windows. Mindkét esetben minden szolgáltatáspéldány vagy egy replika kell egyedi módon címezhetők.

![Szolgáltatás-végpontok][1]

## <a name="service-discovery-and-resolution"></a>A szolgáltatásészlelés és megoldás szerint
Elosztott rendszerek esetén szolgáltatások előfordulhat, hogy helyezze át egyik gépről egy másikra adott idő alatt. Ez akkor fordulhat elő, beleértve az erőforrások terhelésének, a frissítések, feladatátvétel vagy kibővített különböző okokból. Ez azt jelenti, szolgáltatás-végpont címére módosítsa a szolgáltatás különböző IP-címekkel rendelkező csomópontok helyezi át, és meg különböző portokon: Ha a szolgáltatás dinamikusan kiválasztott portot használ.

![Szolgáltatások][7]

A Service Fabric biztosít a szolgáltatás nevű felderítési és névfeloldási szolgáltatás. A szolgáltatás megőrzi a táblázat, amely leképezhető nevű szolgáltatáspéldány azok figyelni a végpont-címekre. A Service Fabric összes elnevezett szolgáltatáspéldány képviselt URI-k, mint például egyedi nevük legyen `"fabric:/MyApplication/MyService"`. A szolgáltatás neve nem változtatja meg a szolgáltatás életciklusa alatt, de csak a módosíthatja, ha a szolgáltatások végponti címeket. Ez az megfelel a webhelyek, amelyek állandó URL-címek, de ha az IP-címet módosíthatja. És a weben, amely kiküszöböli a webhely URL-címek IP-címek, DNS-hasonló Service Fabric a regisztráló, amely a végpont címe szolgáltatásnevek van leképezve.

![Szolgáltatás-végpontok][2]

Megoldása és a szolgáltatásokhoz kapcsolódik az alábbi lépésekből ismétlődő:

* **Hárítsa el**: a szolgáltatás, amely a szolgáltatás közzétette a végpont lekérése.
* **Csatlakozás**: függetlenül, hogy a végpont által használt protokoll azt keresztül csatlakozni a szolgáltatáshoz.
* **Próbálja meg újra**: kapcsolódási kísérlet tetszőleges számú okok miatt sikertelenek lehetnek, például ha a szolgáltatás át lett helyezve, mert a legutóbbi végpontcím lett feloldva a. Ebben az esetben az előző oldható fel, és csatlakozzon újra megpróbálja lépéseket kell, és ez a ciklus addig ismétlődik, amíg a kapcsolat nem jár sikerrel.

## <a name="connecting-to-other-services"></a>Más szolgáltatásokhoz kapcsolódik
Szolgáltatások csatlakozás egymással a fürtben található általában közvetlenül hozzáférhet az egyéb szolgáltatások végpontok, mert a fürtben található csomópontok a helyi hálózaton. Hogy könnyebb szolgáltatások közötti kapcsolat, a Service Fabric további olyan szolgáltatásokat biztosít, a szolgáltatás használata. A DNS-szolgáltatás és a fordított proxy szolgáltatás.


### <a name="dns-service"></a>DNS-szolgáltatás
Óta számos szolgáltatás főleg indexelése szolgáltatások lehet egy olyan meglévő URL-címet nevének, tudnak majd hárítsa el ezeket a szabványos DNS-sel protokoll (ahelyett, hogy a szolgáltatás protokoll) nagyon kényelmes, különösen az alkalmazás "növekedési és az eltolás mértékét megadó" forgatókönyvek. Ez az pontosan, a DNS-szolgáltatás funkciója. Lehetővé teszi a DNS-nevek hozzárendelése egy szolgáltatásnevet, és ezért a végpont IP-címeket feloldani. 

Ahogy a következő ábrán is látható, a DNS-szolgáltatás, a Service Fabric-fürt futó DNS-nevek szolgáltatásnevek, amely majd szünteti meg a szolgáltatás a végpont címét való kapcsolódáshoz vissza rendeli hozzá. A szolgáltatás DNS-nevét a létrehozás időpontjában valósul meg. 

![Szolgáltatás-végpontok][9]

A DNS használatáról további részletekért lásd: Service [DNS-szolgáltatás az Azure Service Fabric](service-fabric-dnsservice.md) cikk.

### <a name="reverse-proxy-service"></a>Fordított proxy szolgáltatás
A fordított proxy a fürt, amely elérhetővé teszi a HTTP-végpontokról, beleértve a HTTPS szolgáltatások megoldást. A fordított proxy jelentősen leegyszerűsíti az egyéb szolgáltatások és a metódusok meghívása azzal, hogy egy adott URI-formátum, és kezeli a feloldás, csatlakozás, próbálkozzon újra egy szolgáltatás egy másik a szolgáltatás használatakor kommunikációs szükséges lépéseket. Más szóval elrejti a szolgáltatás az Ön azáltal, hogy a lehető legegyszerűbb hívja az egy URL-címet egyéb szolgáltatások hívásakor.

![Szolgáltatás-végpontok][10]

A fordított proxy szolgáltatás használatáról további információ: [fordított proxy az Azure Service Fabric](service-fabric-reverseproxy.md) cikk.

## <a name="connections-from-external-clients"></a>Külső ügyfelek kapcsolódását
Szolgáltatások csatlakozás egymással a fürtben található általában közvetlenül hozzáférhet az egyéb szolgáltatások végpontok, mert a fürtben található csomópontok a helyi hálózaton. Bizonyos környezetekben előfordulhat azonban, fürt, amely a külső érkező forgalmat korlátozott számú portok keresztül továbbítja a terheléselosztó mögött. Ezekben az esetekben szolgáltatások továbbra is kommunikálhatnak egymással és a szolgáltatás használatakor címek feloldására, de további lépést kell tenni a külső ügyfelek szolgáltatásokhoz.

## <a name="service-fabric-in-azure"></a>A Service Fabric az Azure-ban
Az Azure Service Fabric-fürtök az Azure terheléselosztó mögött helyezkedik el. A fürt összes külső forgalmat kell haladnia a terheléselosztó hasonló adataival. A terheléselosztó automatikusan továbbítsa a forgalmat egy véletlenszerű egy adott portot a bejövő *csomópont* , amely rendelkezik a megnyitásához ugyanazt a portot. Az Azure Load Balancer csak ismer portok nyitva a *csomópontok*, portok nyitva személy nem ismer *szolgáltatások*.

![Az Azure Load Balancer és a Service Fabric topológia][3]

Például ahhoz, hogy fogadja el a külső forgalom porton **80**, konfigurálni kell a következőket:

1. Az írási olyan szolgáltatás, amely a 80-as porton figyel. A szolgáltatás ServiceManifest.xml 80-as portot konfigurálja, és nyissa meg a figyelő a szolgáltatásban, például egy önálló üzemeltetett webkiszolgálón.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. A Service Fabric-fürt létrehozása az Azure-ban, és adja meg a port **80** a csomóponttípus a szolgáltatást üzemeltető egyéni végpont-portjaként működik. Ha egynél több csomópont típusa van, beállíthat egy *elhelyezési korlátozás* a szolgáltatás csak fut, amely a megnyitott egyéni végpont-porttal rendelkezik csomóponttípuson biztosításához.

    ![A csomóponttípus port megnyitása][4]
3. A fürt létrehozása után adja meg az Azure Load Balancer a fürterőforrás-csoport továbbítsa a forgalmat a 80-as porton. Az Azure portálon keresztül fürt létrehozása, ha ez be van állítva automatikusan minden konfigurált egyéni végpont-porthoz.

    ![Az Azure Load Balancer a forgalom továbbítására][5]
4. Az Azure Load Balancer egy mintavételt alapján határozza meg, hogy forgalmat küldeni egy adott csomópont-e. A mintavétel rendszeres időközönként ellenőrzi annak meghatározásához, vagy nem válaszol-e a csomópont minden egyes csomóponton a végpont. Ha a mintavételi nem kapott választ a konfigurált számú alkalommal után, a terheléselosztó leállítja a forgalom küldése a csomóponton. Az Azure portálon keresztül a fürt létrehozásakor egy mintavételt automatikusan be van állítva minden konfigurált egyéni végpont-porthoz.

    ![Az Azure Load Balancer a forgalom továbbítására][8]

Fontos megjegyezni, hogy a mintavétel, valamint az Azure Load Balancer csak ismernie a *csomópontok*, nem a *szolgáltatások* a csomópontokon futó. Az Azure Load Balancer mindig a csomópontokra, amelyeket a mintavétel válaszolni, így ügyelni kell arra szolgáltatások érhetők el a csomóponton, amelyre kiterjedhet a mintavétel válaszolni fog forgalmat küldeni.

## <a name="reliable-services-built-in-communication-api-options"></a>Megbízható szolgáltatások: Beépített kommunikációs API-beállítások
Megbízható szolgáltatások keretében számos előre elkészített kommunikációs lehetőségekről fájlmegosztásokba másolhat. A döntést arról, hogy mely egyik működhet a legjobban az Ön a programozási modellel, a kommunikáció keretrendszer és a programozási nyelv, a szolgáltatások írt függ.

* **Nem adott protokoll:** Ha nem rendelkezik egy adott kiválasztott kommunikációs keretrendszer, de szeretne valamit fel, és gyorsan, akkor az ideális beállítás meg van [szolgáltatás távoli eljáráshívás](service-fabric-reliable-services-communication-remoting.md), amely lehetővé teszi, hogy erős típusmegadású távoli eljáráshívásnak Reliable Services és Reliable Actors hívásokat. Ez módja a legegyszerűbben és leggyorsabban Ismerkedés a szolgáltatások közötti kommunikáció. Szolgáltatás távoli eljáráshívási szolgáltatás címek, a kapcsolat, az újra gombra és a hibakezelés feloldását végzi. Ez az a C# és a Java-alkalmazások számára érhető el.
* **HTTP**: nyelvtől független kommunikációhoz HTTP biztosít egy szabványos választott nyelven is elérhető számos különböző, a Service Fabric által támogatott HTTP-kiszolgálók és eszközök. Szolgáltatások bármely érhető el, beleértve a HTTP-verem használható [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) C#-alkalmazások. C# nyelven írt ügyfelek kihasználhatják a `ICommunicationClient` és `ServicePartitionClient` osztályokat, mivel a Java, használja a `CommunicationClient` és `FabricServicePartitionClient` osztályok, [névfeloldási szolgáltatás, a HTTP-kapcsolatoknál és az újrapróbálkozási ciklusok](service-fabric-reliable-services-communication.md).
* **WCF**: Ha rendelkezik meglévő kódot, amely a kommunikációs keretrendszer WCF használ, akkor használhatja a `WcfCommunicationListener` a kiszolgálóoldali és `WcfCommunicationClient` és `ServicePartitionClient` osztályok az ügyfél számára. Ez azonban lehetőség csak a C#-alkalmazást a Windows-alapú fürtök. További részletekért lásd: Ez a cikk [WCF-alapú megvalósítás a kommunikációs verem](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Egyéni protokollok és egyéb kommunikációs keretrendszerek használatával
Szolgáltatások használhatnak minden protokoll vagy keretrendszer kommunikációhoz, hogy egy egyéni bináris protokoll TCP-szoftvercsatornák vagy az esemény streamelését keresztül-e [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) vagy [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). A Service Fabric API-t is csatlakoztathatja a kommunikációs verem, amíg felderítésére, és csatlakozzon a munkát az Ön kiveszik kommunikációt biztosít. Ez a cikk tudnivalók a [megbízható kommunikáció modell](service-fabric-reliable-services-communication.md) további részleteket.

## <a name="next-steps"></a>További lépések
További információ a fogalmakat és elérhető az API-kat a [Reliable Services kommunikációs modellt](service-fabric-reliable-services-communication.md), majd használatának gyors megkezdése [szolgáltatás távoli eljáráshívás](service-fabric-reliable-services-communication-remoting.md) vagy válassza a részletes megtudhatja, hogyan írhat egy kommunikációs figyelővel [Web API-t önálló gazdagép OWIN](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
