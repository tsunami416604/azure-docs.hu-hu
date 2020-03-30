---
title: Az Azure Service Fabric fordított proxyja
description: Használja a Service Fabric fordított proxy ját a mikroszolgáltatásokhoz a fürtön belülről és kívülről történő kommunikációhoz.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 4fa4c6e46dd786b833087f892d995e85b5d2ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282223"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Fordított proxy az Azure Service Fabricben
Az Azure Service Fabric beépített fordított proxy segít a Service Fabric-fürtben futó mikroszolgáltatásoknak a http-végpontokkal rendelkező más szolgáltatások felderítésében és kommunikálásában.

## <a name="microservices-communication-model"></a>Mikroszolgáltatások kommunikációs modellje
A Service Fabric mikroszolgáltatásai a fürt csomópontjainak egy részhalmazán futnak, és különböző okok miatt áttelepíthetők a csomópontok között. Ennek eredményeképpen a mikroszolgáltatások végpontjai dinamikusan változhatnak. A fürt más szolgáltatásainak felderítéséhez és az azokkal való kommunikációhoz a mikroszolgáltatásnak a következő lépéseken kell átmennie:

1. A szolgáltatás helyét az elnevezési szolgáltatáson keresztül oldhatja fel.
2. Csatlakozzon a szolgáltatáshoz.
3. Az előző lépések tördelése egy olyan ciklusban, amely a kapcsolathibákra alkalmazandó szolgáltatásfeloldási és újrapróbálkozási házirendeket valósítja meg

További információt a [Csatlakozás és a szolgáltatásokkal való kommunikáció](service-fabric-connect-and-communicate-with-services.md)című témakörben talál.

### <a name="communicating-by-using-the-reverse-proxy"></a>Kommunikáció a fordított proxy használatával
A fordított proxy olyan szolgáltatás, amely minden csomóponton fut, és kezeli a végpontok felbontását, az automatikus újrapróbálkozást és az ügyfélszolgáltatások nevében történő egyéb csatlakozási hibákat. A fordított proxy beállítható úgy, hogy különböző házirendeket alkalmazzon az ügyfélszolgáltatásokból érkező kérelmek kezelése érdekében. Fordított proxy használatával az ügyfélszolgáltatás bármely ügyféloldali HTTP-kommunikációs kódtárat használhat, és nem igényel speciális felbontást és újrapróbálkozási logikát a szolgáltatásban. 

A fordított proxy egy vagy több végpontot tár fel a helyi csomóponton az ügyfélszolgáltatások számára, amelyet más szolgáltatásoknak küldhetnek.

![Belső kommunikáció][1]

> [!NOTE]
> **Támogatott platformok**
>
> A Service Fabric fordított proxyja jelenleg a következő platformokat támogatja
> * *Windows Cluster*: Windows 8 és újabb vagy Windows Server 2012 és újabb
> * *Linux Cluster*: Reverse Proxy jelenleg nem érhető el linuxos fürtökhöz
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Mikroszolgáltatások elérése a fürtön kívülről
A mikroszolgáltatások alapértelmezett külső kommunikációs modellje egy opt-in modell, ahol minden szolgáltatás nem érhető el közvetlenül a külső ügyfelek. [Az Azure Load Balancer](../load-balancer/load-balancer-overview.md), amely a mikroszolgáltatások és a külső ügyfelek közötti hálózati határ, hálózati címfordítást hajt végre, és továbbítja a külső kérelmeket a belső IP:port végpontok. Ahhoz, hogy egy mikroszolgáltatás végpontja közvetlenül elérhető legyen a külső ügyfelek számára, először konfigurálnia kell a terheléselosztót, hogy továbbítsa a forgalmat a szolgáltatás által a fürtben használt minden egyes portra. Továbbá a legtöbb mikroszolgáltatások, különösen az állapotalapú mikroszolgáltatások, nem élnek a fürt összes csomópontján. A mikroszolgáltatások a feladatátvételi csomópontok között mozoghatnak. Ilyen esetekben a terheléselosztó nem tudja hatékonyan meghatározni a replikák célcsomópontjának helyét, amelyre a forgalmat továbbítania kell.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Mikroszolgáltatások elérése fordított proxyn keresztül a fürtön kívülről
Ahelyett, hogy konfigurálná egy adott szolgáltatás portját a Terheléselosztóban, csak a fordított proxy portját konfigurálhatja a Terheléselosztóban. Ez a konfiguráció lehetővé teszi, hogy a fürtön kívüli ügyfelek további konfiguráció nélkül érik el a fürtön belüli szolgáltatásokat a fordított proxy használatával.

![Külső kommunikáció][0]

> [!WARNING]
> Amikor konfigurálja a fordított proxy portját a terheléselosztóban, a fürt összes olyan mikroszolgáltatása, amely HTTP-végpontot fed le, a fürtön kívülről címezhető. Ez azt jelenti, hogy a belsőnek szánt mikroszolgáltatások felderíthetők lehetnek egy meghatározott rosszindulatú felhasználó által. Ez potenciálisan kihasználható súlyos sebezhetőségeket jelent; például:
>
> * A rosszindulatú felhasználók szolgáltatásmegtagadási támadást indíthatnak, ha ismételten olyan belső szolgáltatást hívnak meg, amely nem rendelkezik megfelelően megerősített támadási felülettel.
> * A rosszindulatú felhasználók hibásan formázott csomagokat juttathatnak el egy belső szolgáltatásba, ami nem kívánt viselkedést eredményez.
> * A belső nek szánt szolgáltatások olyan személyes vagy bizalmas adatokat adhatnak vissza, amelyek nem a fürtön kívüli szolgáltatásoknak vannak kitéve, így ezeket a bizalmas információkat rosszindulatú felhasználók számára teszik ki. 
>
> Mielőtt a fordított proxyportot nyilvánossá tenné, győződjön meg arról, hogy teljes mértékben tisztában van a fürt és a rajta futó alkalmazások lehetséges biztonsági következményeivel. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>URI-formátum a szolgáltatások címzéséhez a fordított proxy használatával
A fordított proxy egy adott egységes erőforrás-azonosító (URI) formátumot használ annak a szolgáltatáspartíciónak a azonosítására, amelyre a bejövő kérelmet továbbítani kell:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(k):** A fordított proxy beállítható úgy, hogy elfogadja a HTTP- vagy HTTPS-forgalmat. A HTTPS-továbbítás, olvassa el [a Csatlakozás biztonságos szolgáltatáshoz a fordított proxy,](service-fabric-reverseproxy-configure-secure-communication.md) ha már fordított proxy beállítás hallgatni https.For HTTPS forwarding, refer on Connect to a secure service with the reverse proxy once you have reverse proxy setup to listen on HTTPS.
* **Fürt teljesen minősített tartományneve (FQDN) | belső IP:** Külső ügyfelek esetén beállíthatja a fordított proxyt úgy, hogy az elérhető legyen a fürttartományon keresztül, például mycluster.eastus.cloudapp.azure.com. Alapértelmezés szerint a fordított proxy minden csomóponton fut. A belső forgalom esetén a fordított proxy érhető el localhost vagy bármely belső csomópont IP-cím, például 10.0.0.1.
* **Port:** Ez az a port( például 19081), amely a fordított proxyhoz van megadva.
* **ServiceInstanceName:** Ez az üzembe helyezett szolgáltatáspéldány teljesen minősített neve, amelyet a "fabric:/" nélkül próbál elérni. Rendszer. Például a *háló eléréséhez:/myapp/myservice/* service, akkor használja *myapp / myservice*.

    A szolgáltatáspéldány neve a kis- és nagybetűket nem figyelembe vevő. Ha egy másik címkét használ az URL-címben lévő szolgáltatáspéldány nevéhez, a kérelmek a 404-es (nem található) esetén sikertelenek lesznek.
* **Utótag elérési útja:** Ez a tényleges URL-elérési út, például *a myapi/values/add/3,* a szolgáltatás, amelyhez csatlakozni kíván.
* **Partíciókulcs:** Particionált szolgáltatás esetén ez az elérni kívánt partíció számított partíciókulcsa. Ne feledje, hogy ez *nem* a partíció azonosítójának GUID azonosítója. Ez a paraméter nem szükséges az egytónusú partíciósémát használó szolgáltatásokhoz.
* **PartitionKind:** Ez a szolgáltatáspartíció-séma. Ez lehet "Int64Range" vagy "Named". Ez a paraméter nem szükséges az egytónusú partíciósémát használó szolgáltatásokhoz.
* **Figyelőneve** A szolgáltatás végpontjai {"Végpontok":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}. Ha a szolgáltatás több végpontot tesz elérhetővé, ez azonosítja azt a végpontot, amelybe az ügyfélkérelmet továbbítani kell. Ez elhagyható, ha a szolgáltatás csak egy figyelővel rendelkezik.
* **TargetReplicaSelector (CélRepLikátor)** Ez határozza meg, hogyan kell kiválasztani a célreplikát vagy példányt.
  * Ha a célszolgáltatás állapotalapú, a TargetReplicaSelector a következők egyike lehet: 'PrimaryReplica', 'RandomSecondaryReplica', vagy 'RandomReplica'. Ha ez a paraméter nincs megadva, az alapértelmezett érték a "PrimaryReplica".
  * Ha a célszolgáltatás állapot nélküli, a fordított proxy a szolgáltatáspartíció véletlenszerű példányát választja ki a kérelem továbbításához.
* **Időeltetés:**  Ez adja meg a fordított proxy által a szolgáltatásnak az ügyfélkérés nevében létrehozott HTTP-kérelem időmegta. Az alapértelmezett érték 60 másodperc. Ez egy nem kötelező paraméter.

### <a name="example-usage"></a>Példa a használatra
Vegyük például a *fabric:/MyApp/MyService* szolgáltatást, amely http-figyelőt nyit meg a következő URL-címen:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

A szolgáltatás forrásai a következők:

* `/index.html`
* `/api/users/<userId>`

Ha a szolgáltatás a singleton particionálási sémát használja, a *PartitionKey* és a *PartitionKind* lekérdezési karakterlánc-paraméterek nem szükségesek, és a szolgáltatás az átjáró használatával érhető el:

* Külsőleg:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Belsőleg:`http://localhost:19081/MyApp/MyService`

Ha a szolgáltatás az Uniform Int64 particionálási sémát használja, a *PartitionKey* és a *PartitionKind* lekérdezési karakterlánc paramétereit kell használni a szolgáltatás partíciójának eléréséhez:

* Külsőleg:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Belsőleg:`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

A szolgáltatás által elérhetővé tett erőforrások eléréséhez egyszerűen helyezze el az erőforrás elérési útját a szolgáltatás neve után az URL-címben:

* Külsőleg:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Belsőleg:`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Az átjáró ezután továbbítja ezeket a kéréseket a szolgáltatás URL-címére:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>A kikötőmegosztási szolgáltatások különleges kezelése
A Service Fabric fordított proxy megpróbálja feloldani a szolgáltatás címét újra, és próbálja meg újra a kérelmet, ha egy szolgáltatás nem érhető el. Általában, ha egy szolgáltatás nem érhető el, a szolgáltatáspéldány vagy a replika átkerült egy másik csomópontba a szokásos életciklus részeként. Ilyen esetben előfordulhat, hogy a fordított proxy hálózati csatlakozási hibát kap, amely azt jelzi, hogy a végpont már nincs megnyitva az eredetileg feloldott címen.

A replikák vagy a szolgáltatáspéldányok azonban megoszthatják a gazdafolyamatot, és megoszthatják a portot is, ha egy http.sys-alapú webkiszolgáló üzemelteti őket, többek között:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Ebben az esetben valószínű, hogy a webkiszolgáló elérhető a gazdafolyamatban, és válaszol a kérésekre, de a megoldott szolgáltatáspéldány vagy replika már nem érhető el az állomáson. Ebben az esetben az átjáró http 404-es választ kap a webkiszolgálótól. Így egy HTTP 404 válasznak két különböző jelentése lehet:

- #1.
- #2. eset: A szolgáltatás címe helytelen, és a felhasználó által kért erőforrás létezhet egy másik csomóponton.

Az első eset egy normál HTTP 404, amely felhasználói hibának minősül. A második esetben azonban a felhasználó olyan erőforrást kért, amely létezik. A fordított proxy nem tudta megtalálni, mert maga a szolgáltatás költözött. A fordított proxynak újra fel kell oldania a címet, és újra meg kell próbálnia a kérelmet.

A fordított proxy így kell egy módja annak, hogy különbséget tegyenek a két esetben. Ahhoz, hogy ezt a különbséget, egy tipp a szerver van szükség.

* Alapértelmezés szerint a fordított proxy feltételezi a #2, és megpróbálja újra megoldani és kiadni a kérelmet.
* A fordított proxyra #1 megjelöléséhez a szolgáltatásnak a következő HTTP-válaszfejlécet kell visszaadnia:

  `X-ServiceFabric : ResourceNotFound`

Ez a HTTP-válaszfejléc normál HTTP 404-es helyzetet jelez, amelyben a kért erőforrás nem létezik, és a fordított proxy nem próbálja meg újra feloldani a szolgáltatáscímet.

## <a name="special-handling-for-services-running-in-containers"></a>A konténerekben futó szolgáltatások különleges kezelése

A tárolókon belül futó szolgáltatások esetében `Fabric_NodeIPOrFQDN` a környezeti változó segítségével a [fordított proxy URL-címét](#uri-format-for-addressing-services-by-using-the-reverse-proxy) a következő kódnak nevezi:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
A helyi fürt `Fabric_NodeIPOrFQDN` esetében alapértelmezés szerint "localhost" értékre van állítva. Indítsa el a `-UseMachineName` helyi fürtöt a paraméterrel, és győződjön meg arról, hogy a tárolók elérhetik a csomóponton futó fordított proxyt. További információt a [Fejlesztői környezet konfigurálása a tárolók hibakereséséhez](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers)című témakörben talál.

A Docker-compose tárolókon belül futó Service Fabric-szolgáltatásokhoz speciális docker-compose.yml *ports szakaszhttp:* vagy https: konfiguráció szükséges. További információ: [Docker Compose deployment support in Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>További lépések
* [Fordított proxy beállítása és konfigurálása fürtön.](service-fabric-reverseproxy-setup.md)
* [Továbbítás beállítása a HTTP-szolgáltatás védelmére a fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md)
* [Fordított proxy eseményeinek diagnosztizálása](service-fabric-reverse-proxy-diagnostics.md)
* Tekintse meg a http-kommunikáció t a Szolgáltatások közötti kommunikáció egy [minta projekt a GitHubon.](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Távoli eljáráshívások a Megbízható szolgáltatások távoli hívásával](service-fabric-reliable-services-communication-remoting.md)
* [Az OWIN alkalmazást használó webes API a Megbízható szolgáltatásokban](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikáció a Megbízható szolgáltatások használatával](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
