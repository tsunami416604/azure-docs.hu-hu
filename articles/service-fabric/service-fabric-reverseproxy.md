---
title: Az Azure Service Fabric fordított proxyja |} A Microsoft Docs
description: A Service Fabric fordított proxy használata belül és kívül a fürt a mikroszolgáltatások való kommunikációhoz.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: bec2e443b920a1f163b7b328197d3688d207ed35
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39309119"
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
> * *Linux-fürt*: fordított Proxy nem érhető el jelenleg a Linux-fürtökre
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>A mikroszolgáltatások a fürtön kívülről elérése
Az alapértelmezett külső kommunikációs modellt a mikroszolgáltatásokat egy választható modell, ahol minden szolgáltatás nem érhető el közvetlenül a külső ügyfeleknek. [Az Azure Load Balancer](../load-balancer/load-balancer-overview.md), mikroszolgáltatások és a külső ügyfelek közötti hálózati határok hálózati címfordítás hajt végre, amely továbbítja a külső, belső IP:port-végpontokra irányuló kéréseket. Ahhoz, hogy egy mikroszolgáltatás-végpont el közvetlenül a külső ügyfeleknek, először konfigurálnia kell a terheléselosztó továbbítja a forgalmat a szolgáltatás által használt a fürt minden egyes porthoz. Emellett a legtöbb mikroszolgáltatások, különösen az állapotalapú mikroszolgáltatások, nem élő a fürt összes csomópontján. A mikroszolgáltatások helyezheti át a feladatátvételi csomópontok között. Ezekben az esetekben a Load Balancer hatékonyan nem tudja megállapítani a replikákról, amelyhez továbbítsa a forgalmat a célcsomópont helyét.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Mikroszolgáltatások a fürtön kívülről származó fordított proxyn keresztül éri el
Helyett egy adott szolgáltatás portszámát konfigurálása a Load Balancer, konfigurálhatja a Load Balancer csak a fordított proxy portjával. Ez a konfiguráció lehetővé teszi az ügyfelek számára a fürtön kívülről a fürtön belüli szolgáltatások eléréséhez további konfiguráció nélkül a fordított proxy használatával.

![Külső kommunikáció][0]

> [!WARNING]
> A fordított proxy portjával Load balancerben konfigurálásakor, amely közzétenni egy HTTP-végpontot a fürt összes mikroszolgáltatások a fürtön kívülről címezhető. Ez azt jelenti, hogy rosszindulatú felhasználók által felderíthető lehet-e a mikroszolgáltatás-alapú szinkronban kell lennie a belső. Ez potenially súlyos biztonsági réseket, hogy azokat kihasználnák; mutat be. Példa:
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

* **http (s):** a fordított proxy HTTP vagy HTTPS-forgalom fogadására konfigurálhatók. HTTPS-továbbítást, tekintse meg a [Csatlakozás biztonságos szolgáltatás, amely a fordított proxy](service-fabric-reverseproxy-configure-secure-communication.md) szerint HTTPS a fordított proxy beállítása után.
* **Fürt teljes minősített tartománynevét (FQDN) |} belső IP:** a külső ügyfelek esetében konfigurálhatja a fordított proxy, hogy a fürt tartományával, például mycluster.eastus.cloudapp.azure.com keresztül elérhető legyen. A fordított proxy alapértelmezés szerint minden csomóponton fut. Belső forgalom esetében a fordított proxy érhető el a localhost vagy bármely belső IP-Címét, például a 10.0.0.1.
* **Port:** Ez az a port, pl. 19081., a fordított proxyhoz megadott.
* **ServiceInstanceName:** azt a teljes nevet, a telepített szolgáltatáspéldány nélkül elérni próbált a "fabric: /" sémát. Ha például el a *fabric: / myapp/myservice/* szolgáltatást, használja *myapp/myservice*.

    A szolgáltatás-példány nevét a kis-és nagybetűket. Egy eltérő kis-és a szolgáltatás neve az URL-címben rendelkezik, a kérelem a 404 (nem található) sikertelen.
* **Utótag elérési útja:** Ez az a tényleges URL-cím, mint például a *myapi/értékek/hozzáadása/3*, a szolgáltatás, amelyhez csatlakozni kíván.
* **PartitionKey:** egy particionált szolgáltatáshoz, ez az a számított partíciókulcs a partíció, amely szeretne elérni. Vegye figyelembe, hogy ez a *nem* a partíció GUID azonosítója. Ez a paraméter nem szükséges a singleton partícióséma használó szolgáltatások.
* **PartitionKind:** Ez az a szolgáltatás partíciósémájának. Ez akkor lehet "Int64Range" vagy "Nevű". Ez a paraméter nem szükséges a singleton partícióséma használó szolgáltatások.
* **ListenerName** a végpontok a szolgáltatás a következő formában vannak {"Végpontok": {"Listener1": "Endpoint1", "Listener2": "Típusú Endpoint2" …}}. Ha a szolgáltatás több végpontot tesz közzé, ez azonosítja a végpont, amelyet az ügyfél kérése továbbítani kell. Ez nem hagyható, ha a szolgáltatás csak egy figyelőt.
* **TargetReplicaSelector** azt határozza meg, hogy a cél replika- vagy be kell jelölni.
  * Ha a célként megadott szolgáltatás állapot-nyilvántartó, a TargetReplicaSelector lehetnek a következők egyikét: 'PrimaryReplica', "RandomSecondaryReplica" vagy "RandomReplica". Ha ez a paraméter nincs megadva, az alapértelmezett érték "PrimaryReplica".
  * Ha a célszolgáltatás állapot nélküli, fordított proxy választja, a szolgáltatás partíciót továbbítja a kérést egy véletlenszerű példányát.
* **Időtúllépés:** azt határozza meg a fordított proxy a szolgáltatás nevében az ügyfél kérése által létrehozott a HTTP-kérelem időtúllépés. Az alapértelmezett értéke 60 másodperc. Ez egy nem kötelező paraméter.

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
* [Az ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
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

## <a name="setup-and-configuration"></a>Beállítás és konfiguráció

### <a name="enable-reverse-proxy-via-azure-portal"></a>Azure-portálon fordított proxy engedélyezése

Az Azure portal lehetőséget biztosít, hogy fordított proxy engedélyezése egy új Service Fabric-fürt létrehozása során.
A **létrehozása a Service Fabric-fürt**, 2. lépés: fürtözött konfigurációban a csomóponttípus konfigurációja, az "Enable fordított proxy" jelölőnégyzet bejelölésével.
A biztonságos fordított proxy konfigurálása, SSL-tanúsítvány adható meg a 3. lépés: biztonsági, biztonsági beállítások konfigurálása, jelölje be a "Include egy SSL-tanúsítvány a fordított proxy", és adja meg a tanúsítvány adatait.

### <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok segítségével fordított proxy engedélyezése

Használhatja a [Azure Resource Manager-sablon](service-fabric-cluster-creation-via-arm.md) ahhoz, hogy a Service Fabric fordított proxy a fürt számára.

Tekintse meg [konfigurálása HTTPS fordított Proxy egy biztonságos fürt](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) az Azure Resource Manager konfigurálása biztonságos sablonminták fordított proxy egy tanúsítványt, és kezelési tanúsítványváltás együtt.

Először a sablont kap a fürt, amely számára telepíteni kívánja. A mintasablonokat használja, vagy hozzon létre egy egyéni Resource Manager-sablont. A fordított proxy engedélyezheti ezt követően az alábbi lépések segítségével:

1. A fordított proxy port definiálása a [paraméterek szakaszban](../azure-resource-manager/resource-group-authoring-templates.md) a sablon.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Adja meg a portot a nodetype objektumok mindegyike a **fürt** [erőforrás típushoz című](../azure-resource-manager/resource-group-authoring-templates.md).

    A port a paraméternév megadásához, reverseProxyEndpointPort azonosítja.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Oldja meg a fordított proxy az Azure-fürtön kívül, állítsa be a Azure Load Balancer-szabályok a port, 1. lépésben megadott.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. A port a fordított proxyhoz tartozó SSL-tanúsítványok konfigurálásához adja hozzá a tanúsítványt a ***reverseProxyCertificate*** tulajdonságot a **fürt** [erőforrás típushoz című](../resource-group-authoring-templates.md) .

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>A fordított proxy tanúsítvány, amely eltér a fürttanúsítvány támogatása
 Ha a fordított proxy tanúsítvány eltér a tanúsítványt, amely biztonságossá teszi a fürt, majd a korábban megadott tanúsítvány telepíteni a virtuális gépen és kell hozzáadni a hozzáférés-vezérlési lista (ACL), hogy a Service Fabric hozzá tud férni. Ehhez a **virtualMachineScaleSets** [erőforrás típushoz című](../resource-group-authoring-templates.md). A telepítéshez adja hozzá ezt a tanúsítványt a osProfile. A sablon a bővítmény szakasz frissítheti a hozzáférés-vezérlési tanúsítványt.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Tanúsítványokat, amelyek eltérnek a fürttanúsítvány egy meglévő fürtben a fordított proxy engedélyezése a használatakor a fordított proxy tanúsítvány telepítéséhez, és frissítse az ACL-t a fürtön, a fordított proxy engedélyezése előtt. Végezze el a [Azure Resource Manager-sablon](service-fabric-cluster-creation-via-arm.md) az említett beállításokat használó központi telepítési korábban a fordított proxy engedélyezése a telepítés megkezdése előtt a lépések 1 – 4.

## <a name="next-steps"></a>További lépések
* Tekintse meg a szolgáltatások közötti HTTP-kommunikációt egy példát egy [mintaprojektet a Githubon](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [A fordított proxy-továbbítást a biztonságos HTTP-szolgáltatás](service-fabric-reverseproxy-configure-secure-communication.md)
* [A Reliable Services-táveléréssel kezdeményezett távoli eljáráshívások](service-fabric-reliable-services-communication-remoting.md)
* [A Reliable Services OWIN használó webes API](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikáció a Reliable Services használatával](service-fabric-reliable-services-communication-wcf.md)
* További fordított proxy konfigurációs beállítások, tekintse meg az ApplicationGateway/Http szakasz [testreszabása a Service Fabric-fürt beállítások](service-fabric-cluster-fabric-settings.md).

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
