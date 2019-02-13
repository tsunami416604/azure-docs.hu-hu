---
title: Csatlakozás és a kommunikáció a szolgáltatásokkal az Azure Service Fabricben |} A Microsoft Docs
description: Ismerje meg, hogyan oldja meg, csatlakozzon, és a kommunikáció a szolgáltatásokkal a Service Fabricben.
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
ms.openlocfilehash: f11d680330a43dd49b3c36c864f50b9dc869d172
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211852"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Csatlakozás és a kommunikáció a szolgáltatásokkal a Service Fabricben
A Service Fabric-szolgáltatás fut valahol a Service Fabric-fürt, általában elosztva a több virtuális gép. Azt is áthelyezhető egyetlen helyről a másikra, a szolgáltatás tulajdonosa, vagy automatikusan a Service Fabric. Szolgáltatások statikusan nem kapcsolódnak egy adott géphez vagy a cím.

Service Fabric-alkalmazások általában számos különböző szolgáltatásokat, ahol minden szolgáltatás hajt végre egy specializált feladat tevődik össze. Ezek a szolgáltatások is kommunikálnak egymással egy teljes funkciót, például egy webes alkalmazás különböző részei megjelenítési űrlap. Is találhatók, amely csatlakozik, és a kommunikáció a szolgáltatásokkal ügyfélalkalmazások számára. Ez a dokumentum- és a Service fabric-szolgáltatások közötti kommunikáció beállítását ismerteti.

## <a name="bring-your-own-protocol"></a>A saját protokoll használata
A Service Fabric segítségével felügyelheti a szolgáltatásokat, de azt nem döntéseket hoz a szolgáltatások tegye. Ez magában foglalja a kommunikációt. A szolgáltatás a Service Fabric már meg van nyitva, amikor ez a szolgáltatás lehetőséget, hogy a bejövő kérelmeket, bármilyen kívánt protokoll vagy a kommunikációs verem használata végpont beállítása. A szolgáltatás figyelni fogja a normál **IP:port** használatával bármilyen címzési séma, például egy URI-cím. Több szolgáltatáspéldány vagy replikák megoszthatja a gazdafolyamatokon ebben az esetben sem kell eltérő portok használatára, vagy használjon egy port-megosztási módszerrel, például a http.sys kernel-illesztőprogram a Windows. Mindkét esetben minden szolgáltatáspéldány vagy a replika egy gazdagép folyamatban kell lennie egyedileg címezhetővé válnak.

![Szolgáltatásvégpontok][1]

## <a name="service-discovery-and-resolution"></a>Szolgáltatásészlelés és megoldás szerint
Az elosztott rendszerekben szolgáltatások előfordulhat, hogy áthelyezése egyik gépről egy másik idővel. Ez akkor fordulhat elő, beleértve a terheléselosztást, a frissítések, feladatátvétel vagy horizontális felskálázás erőforrás különböző okok miatt. Ez azt jelenti, hogy szolgáltatásvégpont-címeit módosítani, mert a szolgáltatás különböző IP-címekkel rendelkező csomópontok helyezi át, és a különböző portokat is megnyithatja, ha a szolgáltatás dinamikusan kiválasztott portot használ.

![Terjesztésipont-szolgáltatások][7]

A Service Fabric neve az elnevezési szolgáltatás felderítési és névfeloldási szolgáltatást nyújt. Az elnevezési szolgáltatás fenntart egy táblát, amely leképezi nevű szolgáltatás példányainak, figyeljen a végpont-címekre. A Service Fabric minden elnevezett szolgáltatáspéldányok kiszolgálókként URI-k, például egyedi nevük legyen `"fabric:/MyApplication/MyService"`. A szolgáltatás neve nem változik a szolgáltatás élettartama során, hogy csak a módosíthatja, ha a szolgáltatások végponti címeket. Ez a hasonló webhelyek, amelyek állandó URL-címek, de ha az IP-cím változhat. És a weben, amely a webhely URL-címek feloldása egy olyan IP-címek, DNS-hez hasonló a Service Fabric-regisztráló, amely szolgáltatásnevek végpont-címére van leképezve.

![Szolgáltatásvégpontok][2]

Feloldását és -szolgáltatásokhoz való csatlakozás magában foglalja egy hurokba, és futtassa az alábbi lépéseket:

* **Oldja meg**: A végpont, amely egy szolgáltatás közzétette le az elnevezési szolgáltatásban.
* **Csatlakozás**: Függetlenül az, hogy a végpont használ protokollon keresztül csatlakozni a szolgáltatáshoz.
* **Ismételje meg**: Kapcsolódási kísérlet sikertelenek lehetnek, számtalan esetében például, ha a szolgáltatás át lett helyezve, mivel a legutóbbi végpontcím lett feloldva. Ebben az esetben az előző megoldásához, és megpróbálja újból végrehajtani a lépéseket kell csatlakozni, és ez a ciklus ismétlődik mindaddig, amíg a kapcsolat sikeresen létrejön.

## <a name="connecting-to-other-services"></a>Kapcsolódás más szolgáltatásokhoz
Szolgáltatások csatlakozik egymáshoz egy fürtben általában közvetlenül hozzáférhet a végpontok az más szolgáltatások, mert a fürt csomópontjainak azonos helyi hálózatra. Győződjön meg arról, hogy csatlakoztathatók a szolgáltatások közötti, a Service Fabric további szolgáltatásokat, melyek az elnevezési szolgáltatást biztosít. A DNS-szolgáltatás és a egy fordított proxy szolgáltatás.


### <a name="dns-service"></a>DNS-szolgáltatás
Számos szolgáltatás óta különösen tárolóalapú szolgáltatásokat is rendelkezik egy meglévő URL-címet, folyamatban tudja oldani ezeket a szabványos DNS-sel protokoll (helyett az elnevezési szolgáltatásban protokoll) nagyon hasznos, különösen az alkalmazások "átemelése" forgatókönyvek. Ez a pontosan, a DNS-szolgáltatás leírása. Lehetővé teszi, hogy a DNS-név leképezése a szolgáltatás nevét, és ezért a végpont IP-címek feloldása. 

Az alábbi ábrán látható, a DNS-szolgáltatás, a Service Fabric-fürtön futó DNS-nevek szolgáltatásnevek, amely ezután az elnevezési szolgáltatás kapcsolódni a végponthoz címek feloldása rendeli hozzá. A szolgáltatás DNS-nevét a létrehozás időpontjában van megadva. 

![Szolgáltatásvégpontok][9]

A DNS használatával további részletekért lásd: Service [DNS-szolgáltatás az Azure Service Fabric](service-fabric-dnsservice.md) cikk.

### <a name="reverse-proxy-service"></a>Fordított proxy szolgáltatás
A fordított proxy-címek szolgáltatások a fürtben, amely elérhetővé teszi a HTTP-végpontokat, beleértve a HTTPS. A fordított proxy jelentősen leegyszerűsíti a más szolgáltatások és a módszereket hívása által egy adott URI-formátum, és a megoldása kezeli, csatlakozás, próbálkozzon újra egy szolgáltatást egy másik az elnevezési szolgáltatással folytatott kommunikációhoz szükséges lépéseket. Más szóval elrejti az elnevezési szolgáltatásban, más szolgáltatások azáltal, hogy ez egyszerűen hívása egy URL-cím hívásakor.

![Szolgáltatásvégpontok][10]

További információ a fordított proxy szolgáltatás használatával: [fordított proxy az Azure Service Fabric](service-fabric-reverseproxy.md) cikk.

## <a name="connections-from-external-clients"></a>Külső ügyfelek kapcsolódását
Szolgáltatások csatlakozik egymáshoz egy fürtben általában közvetlenül hozzáférhet a végpontok az más szolgáltatások, mert a fürt csomópontjainak azonos helyi hálózatra. Bizonyos környezetekben előfordulhat azonban, a fürt, amely egy korlátozott számú portok külső bejövő forgalmat irányítja egy terheléselosztó mögé. Ezekben az esetekben szolgáltatások továbbra is kommunikálni egymással és az elnevezési szolgáltatás használatával címeket feloldani, de további lépéseket kell tenni, hogy a külső ügyfelek serviceshez való csatlakozáshoz.

## <a name="service-fabric-in-azure"></a>A Service Fabric az Azure-ban
Az Azure Service Fabric-fürtön az Azure Load Balancerhez mögé kerül. A fürt összes külső forgalom meg kell felelnie a terheléselosztón keresztül. A load balancer automatikusan továbbítja a forgalmat, egy véletlenszerű egy adott porton bejövő *csomópont* , amely rendelkezik a nyissa meg a portot. Az Azure Load Balancer csak a nyitott portokkal kapcsolatos tudja a *csomópontok*, nem tudja információk személy által megnyitott portok *szolgáltatások*.

![Az Azure Load Balancer és a Service Fabric-topológia][3]

Például ahhoz, hogy fogadja el a külső forgalmat porton **80-as**, konfigurálni kell az alábbiakat:

1. Írjon egy szolgáltatás, amely a 80-as portot figyeli. A szolgáltatás ServiceManifest.xml 80-as portot konfigurálja, és nyissa meg a figyelő a szolgáltatásban, például egy helyi webkiszolgálót.

    ```xml    <Resources> <Endpoints> <Endpoint Name="WebEndpoint" Protocol="http" Port="80" /> </Endpoints> </Resources>
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
2. Service Fabric-fürt létrehozása az Azure-ban, és adja meg a port **80-as** a csomópont-típus, amely üzemelteti a szolgáltatást egyéni végpont-portjaként működik. Ha egynél több csomópont típusa, beállíthat egy *elhelyezési korlátozás* a szolgáltatás csak akkor fog futni, amely rendelkezik az egyéni végponti port megnyitása typu uzlu biztosításához.

    ![Nyisson meg egy portot a csomópont típusa][4]
3. A fürt létrehozása után konfigurálja az Azure Load Balancer továbbítja a forgalmat a 80-as porton a fürt erőforráscsoportban. Az Azure Portalon keresztül a fürt létrehozásakor ez van beállítva automatikusan minden konfigurált egyéni végponti port.

    ![Továbbítsa a forgalmat az Azure Load balancerben][5]
4. Az Azure Load Balancer mintavétel használja-e küldeni a forgalmat egy adott csomópont a meghatározásához. A mintavétel rendszeresen ellenőrzi minden egyes csomóponton meghatározni, hogy a csomópont válaszol-e a végpont. Ha a mintavétel a válasz megérkezése után a konfigurált számú alkalommal sikertelen, a terheléselosztó nem irányít forgalmat küld a csomóponton. Az Azure Portalon keresztül a fürt létrehozásakor a mintavétel van automatikus beállítása minden konfigurált egyéni végponti port.

    ![Továbbítsa a forgalmat az Azure Load balancerben][8]

Fontos megjegyezni, hogy a mintavétel, valamint az Azure Load Balancer csak ismernie a *csomópontok*, nem a *szolgáltatások* a csomópontokon futó. Az Azure Load Balancer mindig akkor küld forgalmat, hogy a mintavétel válaszolni, így ügyelni kell arra szolgáltatások elérhetők a csomópontokon, amelyek képesek reagálni a mintavétel csomópontok.

## <a name="reliable-services-built-in-communication-api-options"></a>A Reliable Services: Beépített kommunikációs API lehetőségek
A Reliable Services-keretrendszert tartalmaz számos előre elkészített kommunikációs beállítások. A döntést arról, hogy mely egyik működhet a legjobban az Ön számára a programozási modellel, a kommunikáció keretrendszer és a programozási nyelv, a szolgáltatások írt típustól függ.

* **Nincs konkrét protokoll:**  Nem kell egy adott választott kommunikációs keretrendszer, de ismerkedhet meg valami fel, és gyorsan, akkor a ideális megoldás a beállítás, az Ön számára [szolgáltatás távelérésének lehetővé tétele](service-fabric-reliable-services-communication-remoting.md), amely lehetővé teszi a távoli eljáráshívás szigorú típusmegadású hívásainak A Reliable Services és Reliable Actors. Ez az első lépések a szolgáltatások közötti kommunikáció a legkönnyebb és leggyorsabb módja. Szolgáltatás távelérésének lehetővé tétele a szolgáltatás-címére, kapcsolat, újrapróbálkozási és hibakezelés feloldása kezeli. Ez a lehetőség is C# és Java-alkalmazások.
* **HTTP**: Nyelvfüggetlen kommunikációhoz HTTP biztosít egy iparági szabványnak megfelelő által választott eszközökkel és a rendelkezésre álló számos különböző nyelveken, Service Fabric által támogatott HTTP-kiszolgáló. Szolgáltatások a érhető el, beleértve a HTTP szoftverkörnyezettől [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md) a C# alkalmazásokat. Ügyfelek nyelven írt C# kihasználhatják a `ICommunicationClient` és `ServicePartitionClient` osztályokat, mivel a Java használata a `CommunicationClient` és `FabricServicePartitionClient` osztályok, [névfeloldási szolgáltatást, HTTP-kapcsolatokat és újrapróbálkozási hurkok](service-fabric-reliable-services-communication.md).
* **WCF**: Ha a meglévő kódot, amely WCF használ a kommunikációs keretrendszer van, akkor is használhatja a `WcfCommunicationListener` a kiszolgálóoldali és `WcfCommunicationClient` és `ServicePartitionClient` osztályok az ügyfél. Ez azonban csak akkor C# alkalmazások a Windows-alapú fürtökhöz. További részletekért lásd: Ez a cikk [WCF-alapú megvalósítását a kommunikációs verem](service-fabric-reliable-services-communication-wcf.md).

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Egyéni protokollok és más kommunikációs keretrendszerek használatával
Szolgáltatások a bármely protokoll vagy keretrendszer közötti kommunikáció, hogy TCP szoftvercsatorna, vagy az események streamelése keresztül felett, egy egyéni bináris protokoll [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) vagy [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/). A Service Fabric biztosítja a kommunikációt API-kat is csatlakoztathatja a kommunikációs verem be, amíg megismerését és elérését a munka kiveszik a véleményét. Ebben a cikkben megtekintheti a [Reliable Services modellt](service-fabric-reliable-services-communication.md) további részletekért.

## <a name="next-steps"></a>További lépések
További információ a fogalmakat és lévő API-kat a [Reliable Services modellt](service-fabric-reliable-services-communication.md), majd gyors használatbavétele [szolgáltatás távelérésének lehetővé tétele](service-fabric-reliable-services-communication-remoting.md) vagy nyissa meg a részletes megtudhatja, hogyan írhat a kommunikáció figyelő használatával [webes API-hoz integrációsmodul OWIN](service-fabric-reliable-services-communication-webapi.md).

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
