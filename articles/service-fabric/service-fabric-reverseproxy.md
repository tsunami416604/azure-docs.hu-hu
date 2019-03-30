---
title: Az Azure Service Fabric fordított proxyja |} A Microsoft Docs
description: A Service Fabric fordított proxy használata belül és kívül a fürt a mikroszolgáltatások való kommunikációhoz.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 6ce6f1f6559b43a64fb7edd0773a20f8ee0cf8a3
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661921"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Az Azure Service Fabric fordított proxy
Az Azure Service Fabric épített fordított proxy segítségével Service Fabric-fürtön futó mikroszolgáltatásokat felderítése és kommunikálni más szolgáltatásokkal, amelyek http-végpontokat.

## <a name="microservices-communication-model"></a>Mikroszolgáltatás-alapú modellt
A Service Fabricben Mikroszolgáltatásokat fut a fürtben található csomópontok egy része, és különböző okok miatt a csomópontok között telepíthet át. Ennek eredményeképpen a végpontok a mikroszolgáltatásokat dinamikusan módosításához. Fedezze fel, és a fürt más szolgáltatásokkal kommunikálni, mikroszolgáltatás haladjon végig a következő lépéseket:

1. Oldja meg a szolgáltatás helyét az elnevezési szolgáltatáson keresztül.
2. Csatlakozzon a szolgáltatáshoz.
3. Az előző lépések burkolása hurokba került, amely megvalósítja a névfeloldási szolgáltatást, és ismételje meg a házirendek a alkalmazni a csatlakozási hibák

További információkért lásd: [csatlakozás és a kommunikáció a szolgáltatásokkal](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Kommunikáció fordított proxy használatával
Fordított proxy egy szolgáltatása, amely minden csomóponton fut, és kezeli az endpoint névfeloldást, automatikus újrapróbálkozás, és más szolgáltatások ügyfél nevében kapcsolati hibák. Fordított proxy konfigurálható különböző szabályzatok alkalmazására, a szolgáltatások ügyfél kéréseit kezeli. Fordított proxy használatával lehetővé teszi, hogy az ügyfélszolgáltatás bármely ügyféloldali HTTP való kommunikációra való kódtárak és nem igényelnek speciális megoldási majd újrapróbálkozási logika, a szolgáltatásban. 

Fordított proxy más szolgáltatásokba irányuló kérelmek küldéséhez használt ügyfél-szolgáltatások helyi csomóponton egy vagy több végpontot tesz közzé.

![Belső kommunikációs][1]

> [!NOTE]
> **A támogatott platformok**
>
> A Service Fabric fordított proxy jelenleg a következő platformokat támogatja
> * *Windows-fürt*: Windows 8 és újabb vagy Windows Server 2012 és újabb verziók
> * *Linux-fürt*: Fordított Proxy nem érhető el jelenleg a Linux-fürtökre
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>A mikroszolgáltatások a fürtön kívülről elérése
Az alapértelmezett külső kommunikációs modellt a mikroszolgáltatásokat egy választható modell, ahol minden szolgáltatás nem érhető el közvetlenül a külső ügyfeleknek. [Az Azure Load Balancer](../load-balancer/load-balancer-overview.md), mikroszolgáltatások és a külső ügyfelek közötti hálózati határok hálózati címfordítás hajt végre, amely továbbítja a külső, belső IP:port-végpontokra irányuló kéréseket. Ahhoz, hogy egy mikroszolgáltatás-végpont el közvetlenül a külső ügyfeleknek, először konfigurálnia kell a terheléselosztó továbbítja a forgalmat a szolgáltatás által használt a fürt minden egyes porthoz. Emellett a legtöbb mikroszolgáltatások, különösen az állapotalapú mikroszolgáltatások, nem élő a fürt összes csomópontján. A mikroszolgáltatások helyezheti át a feladatátvételi csomópontok között. Ezekben az esetekben a Load Balancer hatékonyan nem tudja megállapítani a replikákról, amelyhez továbbítsa a forgalmat a célcsomópont helyét.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Mikroszolgáltatások a fürtön kívülről származó fordított proxyn keresztül éri el
Helyett egy adott szolgáltatás portszámát konfigurálása a Load Balancer, konfigurálhatja a Load Balancer csak a fordított proxy portjával. Ez a konfiguráció lehetővé teszi az ügyfelek számára a fürtön kívülről a fürtön belüli szolgáltatások eléréséhez további konfiguráció nélkül a fordított proxy használatával.

![Külső kommunikáció][0]

> [!WARNING]
> A fordított proxy portjával Load balancerben konfigurálásakor, amely közzétenni egy HTTP-végpontot a fürt összes mikroszolgáltatások a fürtön kívülről címezhető. Ez azt jelenti, hogy rosszindulatú felhasználók által felderíthető lehet-e a mikroszolgáltatás-alapú szinkronban kell lennie a belső. Ez potenciálisan megadja súlyos biztonsági réseket, hogy azokat kihasználnák; Példa:
>
> * Egy rosszindulatú felhasználó egy szolgáltatásmegtagadási támadást indíthatnak ismételten meghívásával egy belső szolgáltatás, amely nem rendelkezik egy eléggé támadási felületét.
> * Egy rosszindulatú felhasználó előfordulhat, hogy helytelenül formázott csomagok továbbítására az egy belső szolgáltatás nem kívánt viselkedést eredményez.
> * Szinkronban kell lennie a belső szolgáltatás nem célja, hogy ki vannak téve a szolgáltatások a fürtön, így is közzéteheti a bizalmas adatokat egy rosszindulatú felhasználó kívül titkos vagy bizalmas információkat adhatnak vissza. 
>
> Ellenőrizze, hogy teljes mértékben tudomásul veszi és csökkentheti a potenciális biztonsági vonatkozásai annak a fürthöz, és azt, mielőtt a fordított proxy portjával nyilvános futó alkalmazások. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>A fordított proxy használatával szolgáltatások címzéshez URI-formátum
A fordított proxy azonosíthatja a szolgáltatás partíció, amelyhez kell továbbítani a beérkező kérelmet egy adott egységes erőforrás-azonosítója (URI) formátumot használja:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** A fordított proxy HTTP vagy HTTPS-forgalom fogadására konfigurálhatók. HTTPS-továbbítást, tekintse meg a [Csatlakozás biztonságos szolgáltatás, amely a fordított proxy](service-fabric-reverseproxy-configure-secure-communication.md) szerint HTTPS a fordított proxy beállítása után.
* **Fürt teljes minősített tartománynevét (FQDN) |} belső IP-címe:** A külső ügyfelek esetében beállíthatja, hogy a fürt tartományával, például mycluster.eastus.cloudapp.azure.com keresztül elérhető legyen a fordított proxy. A fordított proxy alapértelmezés szerint minden csomóponton fut. Belső forgalom esetében a fordított proxy érhető el a localhost vagy bármely belső IP-Címét, például a 10.0.0.1.
* **Port:** Ez az a port, pl. 19081., a fordított proxyhoz megadott.
* **ServiceInstanceName:** Ez az, hogy a telepített szolgáltatáspéldány nélkül elérni próbált teljes nevét a "fabric: /" sémát. Ha például el a *fabric: / myapp/myservice/* szolgáltatást, használja *myapp/myservice*.

    A szolgáltatás-példány nevét a kis-és nagybetűket. Egy eltérő kis-és a szolgáltatás neve az URL-címben rendelkezik, a kérelem a 404 (nem található) sikertelen.
* **Utótag elérési útja:** Ez az a tényleges URL-cím, mint például a *myapi/értékek/hozzáadása/3*, a szolgáltatás, amelyhez csatlakozni kíván.
* **PartitionKey:** Egy particionált szolgáltatás esetén ez a számított partíciókulcs a partíció, amely szeretne elérni. Vegye figyelembe, hogy ez a *nem* a partíció GUID azonosítója. Ez a paraméter nem szükséges a singleton partícióséma használó szolgáltatások.
* **PartitionKind:** Ez az a szolgáltatás partíciósémájának. Ez akkor lehet "Int64Range" vagy "Nevű". Ez a paraméter nem szükséges a singleton partícióséma használó szolgáltatások.
* **ListenerName** a végpontok a szolgáltatás a következő formában vannak {"Végpontok": {"Listener1": "Endpoint1", "Listener2": "Típusú Endpoint2" …}}. Ha a szolgáltatás több végpontot tesz közzé, ez azonosítja a végpont, amelyet az ügyfél kérése továbbítani kell. Ez nem hagyható, ha a szolgáltatás csak egy figyelőt.
* **TargetReplicaSelector** azt határozza meg, hogy a cél replika- vagy be kell jelölni.
  * Ha a célként megadott szolgáltatás állapot-nyilvántartó, a TargetReplicaSelector lehet az alábbi lehetőségek közül:  'PrimaryReplica', "RandomSecondaryReplica" vagy "RandomReplica". Ha ez a paraméter nincs megadva, az alapértelmezett érték "PrimaryReplica".
  * Ha a célszolgáltatás állapot nélküli, fordított proxy választja, a szolgáltatás partíciót továbbítja a kérést egy véletlenszerű példányát.
* **Időkorlát:**  Megadja a fordított proxy a szolgáltatás nevében az ügyfél kérése által létrehozott a HTTP-kérelem időtúllépés. Az alapértelmezett értéke 60 másodperc. Ez egy nem kötelező paraméter.

### <a name="example-usage"></a>Példa a használatra
Példaként nézzük a *fabric: / MyApp/MyService* szolgáltatás, amely megnyitja a HTTP-figyelő a következő URL-címen:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Az alábbiakban az erőforrásokat a szolgáltatáshoz:

* `/index.html`
* `/api/users/<userId>`

Ha a szolgáltatás a singleton particionálási sémát, használja a *PartitionKey* és *PartitionKind* lekérdezési karakterlánc paraméterei nem szükséges, és a szolgáltatás elérhető, az átjáró használatával:

* Külsőleg: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Belső használatra: `http://localhost:19081/MyApp/MyService`

Ha a szolgáltatás egységes Int64 particionálási sémát, használja a *PartitionKey* és *PartitionKind* lekérdezési karakterlánc paraméterei kell használni a szolgáltatás partíciójának elérni:

* Külsőleg: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Belső használatra: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Szeretné elérni, amely a szolgáltatás elérhetővé teszi az erőforrások, egyszerűen helyezze el az erőforrás elérési útja a szolgáltatás neve, az URL-cím után:

* Külsőleg: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Belső használatra: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Az átjáró ezután továbbítja ezeket a kérelmeket a szolgáltatás URL-címe:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Különleges kezelést port megosztási szolgáltatások
A Service Fabric fordított proxy megkísérli újra feloldani egy szolgáltatás-címet, és ismételje meg a kérelmet, ha a szolgáltatás nem érhető el. Általában a szolgáltatás nem érhető el, ha a szolgáltatáspéldány vagy replika át lett helyezve egy másik csomópont, a normál életciklusának. Ha ez történik, a fordított proxy jelzi, hogy a végpont már nem meg van nyitva az eredetileg feloldott címet a hálózati kapcsolati hiba jelenhet meg.

Azonban replikák, illetve szolgáltatáspéldányok megoszthat egy gazdafolyamaton, és előfordulhat, hogy is megoszthatja a port, a http.sys-alapú kiszolgáló által üzemeltetett többek között:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Ebben az esetben valószínű, hogy a webkiszolgáló érhető el a gazdagép-folyamat, és válaszol a kérelmekre, de a feloldott szolgáltatáspéldány vagy a replika már nem érhető el a gazdagépen. Azt jelzi, ebben az esetben az átjáró kap-e a webkiszolgáló HTTP 404-es választ. Így a HTTP 404-es választ rendelkezhet két különböző leírt jelentésekkel bírnak:

- #1. eset: A szolgáltatás címének helyességét, de a felhasználónak a kért erőforrás nem létezik.
- #2. eset: A szolgáltatás címének helytelen, és az erőforrást, a felhasználó azt kérte, előfordulhat, hogy létezik egy másik csomóponton.

Az első esetben egy normál HTTP 404-es, amely számít felhasználói hiba történt. Azonban a második esetben a felhasználó által kért egy erőforrást, amely létezik. A fordított proxy nem tudta megtalálható lesz, mert a szolgáltatás át lett helyezve. A fordított proxy kell újra feloldani a címét, és ismételje meg a kérelmet.

A fordított proxy így megkülönböztetni a két esetben úgy kell. Ahhoz, hogy a különbséget, egy mutatót a kiszolgáló megadása kötelező.

* Alapértelmezés szerint a fordított proxy #2. eset azt feltételezi, és megkísérli feloldani, és adja ki újra a kérelmet.
* Annak jelzésére, és a fordított proxy #1. eset, a szolgáltatás a következő HTTP-válaszfejléc kell visszaadnia:

  `X-ServiceFabric : ResourceNotFound`

A HTTP-válaszfejléc azt jelzi, hogy egy normál HTTP 404-es helyzet, amelyben a kért erőforrás nem létezik, és a fordított proxy nem próbálja meg újra a szolgáltatás címének feloldására.

## <a name="special-handling-for-services-running-in-containers"></a>Különleges kezelést tárolókban futó szolgáltatásokhoz

A tárolókon belül futó szolgáltatások esetében is használhatja a környezeti változó `Fabric_NodeIPOrFQDN` létrehozására a [fordított proxy URL-címe](#uri-format-for-addressing-services-by-using-the-reverse-proxy) hasonlóan az alábbi kódot:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
A helyi fürt `Fabric_NodeIPOrFQDN` alapértelmezés szerint a "localhost" van beállítva. A helyi fürt elindításához a `-UseMachineName` ellenőrizze, hogy a tárolók elérheti a csomóponton futó fordított proxy paramétert. További információkért lásd: [konfigurálja a fejlesztői környezetet, a tárolók debug](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Tárolók Docker Compose belül futó Service Fabric-szolgáltatások igényelnek speciális docker-compose.yml *szakasz portok* http: vagy https: konfiguráció. További információkért lásd: [az Azure Service Fabric üzembe helyezési támogatás a Docker Compose](service-fabric-docker-compose.md).

## <a name="next-steps"></a>További lépések
* [Állítsa be, és a fordított proxy konfigurálása egy fürtön](service-fabric-reverseproxy-setup.md).
* [A fordított proxy-továbbítást a biztonságos HTTP-szolgáltatás beállítása](service-fabric-reverseproxy-configure-secure-communication.md)
* [Fordított proxy események diagnosztizálása](service-fabric-reverse-proxy-diagnostics.md)
* Tekintse meg a szolgáltatások közötti HTTP-kommunikációt egy példát egy [mintaprojektet a Githubon](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [A Reliable Services-táveléréssel kezdeményezett távoli eljáráshívások](service-fabric-reliable-services-communication-remoting.md)
* [A Reliable Services OWIN használó webes API](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikáció a Reliable Services használatával](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
