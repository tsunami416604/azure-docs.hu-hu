---
title: Csatlakozás és kommunikáció a szolgáltatásokkal az Azure Service Fabricben
description: Ismerje meg, hogyan oldhatja meg, csatlakozhat és kommunikálhat a szolgáltatásokkal a Service Fabric ben.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: e57d169decf482f8b8be1e3b31a07690bc222c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458239"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>Csatlakozás és kommunikáció a szolgáltatásokkal a Service Fabricben
A Service Fabric egy szolgáltatás fut valahol egy Service Fabric-fürt, általában több virtuális gép között elosztva. Áthelyezhető egyik helyről a másikra, akár a szolgáltatás tulajdonosa, vagy automatikusan a Service Fabric. A szolgáltatások nem statikusan kötődnek egy adott géphez vagy címhez.

A Service Fabric-alkalmazás általában számos különböző szolgáltatásokból áll, ahol minden szolgáltatás egy speciális feladatot hajt végre. Ezek a szolgáltatások kommunikálhatnak egymással, hogy teljes funkciót alkossanak, például egy webalkalmazás különböző részeit rendereljék. Vannak olyan ügyfélalkalmazások is, amelyek a szolgáltatásokhoz csatlakoznak és kommunikálnak velük. Ez a dokumentum ismerteti, hogyan lehet beállítani a kommunikációt, és a szolgáltatások között a Service Fabric.

## <a name="bring-your-own-protocol"></a>Hozza magával a saját protokollját
A Service Fabric segít a szolgáltatások életciklusának kezelésében, de nem hoz döntéseket a szolgáltatások használatával kapcsolatban. Ez magában foglalja a kommunikációt is. Amikor a Service Fabric megnyitja a szolgáltatást, ez a szolgáltatás lehetősége a bejövő kérelmek végpontjának beállítására, bármilyen protokoll vagy kommunikációs verem használatával. A szolgáltatás egy normál **IP:port** címet figyel bármely címzési séma, például egy URI használatával. Több szolgáltatáspéldány vagy -kópia is megoszthat egy gazdafolyamatot, amely esetben vagy különböző portokat kell használniuk, vagy portmegosztási mechanizmust kell használniuk, például a Http.sys kernel illesztőprogramját a Windows rendszerben. Mindkét esetben a gazdafolyamat minden szolgáltatáspéldányának vagy replikájának egyedileg címezhetőnek kell lennie.

![szolgáltatásvégpontok][1]

## <a name="service-discovery-and-resolution"></a>Szolgáltatás felderítése és feloldása
Az elosztott rendszerben a szolgáltatások idővel átkerülhetnek egyik gépről a másikra. Ez különböző okok miatt fordulhat elő, beleértve az erőforrás-kiegyenlítést, a frissítéseket, a feladatátvételt vagy a horizontális felskálázást. Ez azt jelenti, hogy a szolgáltatás végpontjai megváltoznak, ahogy a szolgáltatás különböző IP-címekkel rendelkező csomópontokra kerül, és különböző portokon is megnyílhat, ha a szolgáltatás dinamikusan kiválasztott portot használ.

![A szolgáltatások elosztása][7]

A Service Fabric egy felderítési és feloldási szolgáltatást, az elnevezési szolgáltatást biztosít. Az elnevezési szolgáltatás egy olyan táblát tart fenn, amely leképezi az elnevezett szolgáltatáspéldányokat a végpontcímekhez, amelyeket figyelnek. A Service Fabric összes elnevezett szolgáltatáspéldánya egyedi névvel `"fabric:/MyApplication/MyService"`rendelkezik, például URI-ként. A szolgáltatás neve nem változik a szolgáltatás élettartama alatt, csak a végpontcímek, amelyek módosíthatják, amikor a szolgáltatások áthelyezése. Ez hasonló az állandó URL-címekkel rendelkező, de az IP-cím megváltozott webhelyeihez. És hasonlóan a DNS a weben, amely feloldja a webhely URL-címeket IP-címekre, Service Fabric egy regisztrátor, amely leképezi a szolgáltatás nevét a végpont címét.

![szolgáltatásvégpontok][2]

A szolgáltatások megoldása és a szolgáltatásokhoz való csatlakozás a következő lépéseket foglalja egy ciklusban:

* **Feloldás:** A szolgáltatás által közzétett végpont bekésése az elnevezési szolgáltatásból.
* **Csatlakozás**: Csatlakozzon a szolgáltatáshoz az adott végponton használt protokollon keresztül.
* **Újrapróbálkozás**: A csatlakozási kísérlet számos okból sikertelen lehet, például ha a szolgáltatás a végpontcím utolsó feloldása óta költözött. Ebben az esetben az előző feloldási és csatlakozási lépéseket újra meg kell próbálni, és ez a ciklus addig ismétlődik, amíg a kapcsolat sikeres nem lesz.

## <a name="connecting-to-other-services"></a>Csatlakozás más szolgáltatásokhoz
A fürtön belül egymáshoz csatlakozó szolgáltatások általában közvetlenül hozzáférhetnek más szolgáltatások végpontjaihoz, mivel a fürt csomópontjai ugyanazon a helyi hálózaton vannak. Annak érdekében, hogy könnyebben csatlakozhat a szolgáltatások között, a Service Fabric további szolgáltatásokat biztosít, amelyek a névmeghatározási szolgáltatást használják. DNS-szolgáltatás és fordított proxyszolgáltatás.


### <a name="dns-service"></a>DNS-szolgáltatás
Mivel számos szolgáltatás, különösen a konténeres szolgáltatások, rendelkezhetnek egy meglévő URL-névvel, mivel ezeket a szabványos DNS protokoll (nem pedig az elnevezési szolgáltatás protokoll) használatával tudja feloldani, nagyon kényelmes, különösen az alkalmazások "lift and shift" forgatókönyveiben. Pontosan ezt teszi a DNS-szolgáltatás. Lehetővé teszi a DNS-nevek leképezése egy szolgáltatásnévhez, és így feloldhatja a végpont IP-címeit. 

Ahogy az alábbi ábrán látható, a Service Fabric-fürtben futó DNS-szolgáltatás leképezi a DNS-neveket a szolgáltatásnevekhez, amelyeket aztán az elnevezési szolgáltatás felold, hogy visszaadja a végpontcímeket, amelyekhez csatlakozni szeretne. A szolgáltatás DNS-neve a létrehozás időpontjában áll rendelkezésre. 

![szolgáltatásvégpontok][9]

A DNS-szolgáltatás használatával kapcsolatos további részletekért tekintse meg a [DNS-szolgáltatás az Azure Service Fabric cikkében.](service-fabric-dnsservice.md)

### <a name="reverse-proxy-service"></a>Proxy szolgáltatás sztornírozása
A fordított proxy a fürtben lévő szolgáltatásokat, amelyek http-végpontokat, köztük HTTPS-t is elérhetővé teszik. A fordított proxy nagyban leegyszerűsíti más szolgáltatások és azok metódusai hívását egy adott URI-formátummal, és kezeli a feloldási, csatlakozási, újrapróbálkozási lépéseket, amelyek szükségesek ahhoz, hogy az egyik szolgáltatás kommunikáljon egy másikkal az elnevezési szolgáltatás használatával. Más szóval elrejti az elnevezési szolgáltatást, amikor más szolgáltatásokat hív, mivel ez olyan egyszerű, mint egy URL-cím hívása.

![szolgáltatásvégpontok][10]

A fordított proxyszolgáltatás használatával kapcsolatos további részletekért tekintse meg a [Proxy megfordítása az Azure Service Fabric cikkében.](service-fabric-reverseproxy.md)

## <a name="connections-from-external-clients"></a>Külső ügyfelek kapcsolatai
A fürtön belül egymáshoz csatlakozó szolgáltatások általában közvetlenül hozzáférhetnek más szolgáltatások végpontjaihoz, mivel a fürt csomópontjai ugyanazon a helyi hálózaton vannak. Bizonyos környezetekben azonban előfordulhat, hogy a fürt egy terheléselosztó mögött van, amely a külső bejövő forgalom korlátozott portkészleten keresztül történő átirányítása. Ezekben az esetekben a szolgáltatások továbbra is kommunikálhatnak egymással, és feloldhatják a címeket az elnevezési szolgáltatás használatával, de további lépéseket kell tenni annak érdekében, hogy a külső ügyfelek kapcsolatba léphessenek a szolgáltatásokkal.

## <a name="service-fabric-in-azure"></a>Service Fabric az Azure-ban
Az Azure-beli Service Fabric-fürt egy Azure-terheléselosztó mögé kerül. A fürthöz vezető összes külső forgalomnak át kell haladnia a terheléselosztón. A terheléselosztó automatikusan továbbítja a bejövő forgalmat egy adott porton egy véletlenszerű *csomópontra,* amelynek ugyanaz a portja van nyitva. Az Azure Load Balancer csak a csomópontokon megnyitott portokról tud , nem tud az egyes *szolgáltatások*által megnyitott *portokról.*

![Azure load balancer és service Fabric topológia][3]

Például a **80-as**port külső forgalmának elfogadásához a következő dolgokat kell konfigurálni:

1. Írjon egy szolgáltatást, amely a 80-as porton figyel. Konfigurálja a 80-as portot a szolgáltatás ServiceManifest.xml fájljában, és nyisson meg egy figyelőt a szolgáltatásban, például egy saját üzemeltetésű webkiszolgálót.

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
2. Hozzon létre egy Service Fabric-fürtöt az Azure-ban, és adja meg a **80-as** portot egyéni végpontportként a szolgáltatást üzemeltető csomóponttípushoz. Ha egynél több csomóponttípussal rendelkezik, beállíthat egy *elhelyezési megkötést* a szolgáltatáson, hogy az csak azon a csomóponttípuson futjon, amelyen az egyéni végpontport meg van nyitva.

    ![Port megnyitása csomóponttípuson][4]
3. A fürt létrehozása után konfigurálja az Azure Load Balancer a fürt erőforráscsoport továbbítja a forgalmat a 80-as porton. Fürt létrehozásakor az Azure Portalon keresztül, ez automatikusan be van állítva minden egyéni végpontport, amely konfigurálva volt.

    ![Forgalom továbbítása az Azure Load Balancer-ben][5]
4. Az Azure Load Balancer egy mintavételsegítségével határozza meg, hogy küldjön-e forgalmat egy adott csomópontra. A mintavétel rendszeresen ellenőrzi a végpontot az egyes csomópontokon, hogy megállapítsa, hogy a csomópont válaszol-e vagy sem. Ha a mintavétel nem kap választ egy konfigurált számú idő után, a terheléselosztó leállítja a forgalom küldését az adott csomópontra. Fürt létrehozásakor az Azure Portalon keresztül, a mintavétel automatikusan beállít minden egyéni végpontport, amely konfigurálva volt.

    ![Forgalom továbbítása az Azure Load Balancer-ben][8]

Fontos megjegyezni, hogy az Azure Load Balancer és a mintavétel csak a *csomópontokról*tud, nem a csomópontokon futó *szolgáltatások.* Az Azure Load Balancer mindig küld forgalmat a csomópontok, amelyek válaszolnak a mintavétel, ezért ügyelni kell arra, hogy a szolgáltatások érhetők el a csomópontokon, amelyek képesek reagálni a mintavételre.

## <a name="reliable-services-built-in-communication-api-options"></a>Megbízható szolgáltatások: Beépített kommunikációs API-beállítások
A Reliable Services keretrendszer számos előre elkészített kommunikációs lehetőséggel rendelkezik. A döntés, hogy melyik fog a legjobban működni az Ön számára, a programozási modell, a kommunikációs keretrendszer és a szolgáltatások programozási nyelvének kiválasztásától függ.

* **Nincs konkrét protokoll:**  Ha nem rendelkezik egy adott kommunikációs keretrendszer, de azt szeretné, hogy valamit, és gyorsan fut, majd az ideális választás az Ön számára a [szolgáltatás távoli eljárás,](service-fabric-reliable-services-communication-remoting.md)amely lehetővé teszi az erősen beírt távoli eljárás hívások megbízható szolgáltatások és megbízható szereplők. Ez a legegyszerűbb és leggyorsabb módja a szolgáltatási kommunikáció megkezdésének. A szolgáltatás távoli eljárásátirányító kezeli a szolgáltatáscímek, a kapcsolat, az újrapróbálkozás és a hibakezelés megoldását. Ez c# és Java alkalmazásokhoz is elérhető.
* **HTTP**: A nyelv-független kommunikáció, HTTP nyújt az iparági szabványnak megfelelő választás eszközök kel és HTTP-kiszolgálók elérhető számos különböző nyelven, minden által támogatott Service Fabric. A szolgáltatások bármilyen elérhető HTTP-veremben használhatók, beleértve [a ASP.NET Webes API-t](service-fabric-reliable-services-communication-webapi.md) a C# alkalmazásokhoz. A C# nyelven írt `ICommunicationClient` `ServicePartitionClient` ügyfelek kihasználhatják a és `CommunicationClient` az `FabricServicePartitionClient` osztályokat, míg a Java esetében a és az osztályokat használhatja a [szolgáltatásfeloldáshoz, a HTTP-kapcsolatokhoz és az újrapróbálkozási hurkokhoz.](service-fabric-reliable-services-communication.md)
* **WCF:** Ha a meglévő kódot használó WCF, mint a `WcfCommunicationListener` kommunikációs keretrendszer, `WcfCommunicationClient` `ServicePartitionClient` akkor használhatja a kiszolgálóoldali és osztályok az ügyfél számára. Ez azonban csak a Windows alapú fürtök C# alkalmazásai esetében érhető el. További információt a kommunikációs [verem WCF-alapú implementációjáról](service-fabric-reliable-services-communication-wcf.md)szóló cikkben talál.

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Egyéni protokollok és egyéb kommunikációs keretek használata
A szolgáltatások bármilyen protokollt vagy keretrendszert használhatnak a kommunikációhoz, legyen az egyéni bináris protokoll TCP-szoftvercsatornákon keresztül, vagy [események közvetítése az Azure Event Hubs-on](https://azure.microsoft.com/services/event-hubs/) vagy [az Azure IoT Hubon](https://azure.microsoft.com/services/iot-hub/)keresztül. A Service Fabric olyan kommunikációs API-kat biztosít, amelyekhez csatlakoztathatja a kommunikációs veremeket, miközben a felderítésre és a csatlakozásra irányuló összes munkát öntől absztrakttá teszi. Tekintse meg ezt a cikket a [megbízható szolgáltatás kommunikációs modell](service-fabric-reliable-services-communication.md) további részletekért.

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Megbízható szolgáltatások kommunikációs modellben](service-fabric-reliable-services-communication.md)elérhető fogalmakról és API-król, majd gyorsan megismerkedjen a [szolgáltatás-átirányítóval,](service-fabric-reliable-services-communication-remoting.md) és ismerje meg, hogyan írhat kommunikációs figyelőt a webAPI használatával az [OWIN saját állomásával.](service-fabric-reliable-services-communication-webapi.md)

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
