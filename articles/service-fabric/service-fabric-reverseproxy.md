---
title: "Az Azure Service Fabric fordított proxy |} Microsoft Docs"
description: "A Service Fabric fordított proxy használata a fürtön kívül és belül a mikroszolgáltatások kommunikál."
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 55b201842503a879725fa77328a72c83fe0bbade
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Az Azure Service Fabric fordított proxy
Azure Service Fabric épített fordított proxy segít a Service Fabric-fürt futó mikroszolgáltatások felderítése és http-végpontokról rendelkező más szolgáltatásokkal kommunikálni.

## <a name="microservices-communication-model"></a>Mikroszolgáltatások kommunikációs modellt
A Service Fabric Mikroszolgáltatások egy olyan részhalmazán, a fürtben található csomópontok futtassa, és számos okból a csomópontok között telepíthet át. Ennek eredményeképpen dinamikusan módosítható a végpontokat a mikroszolgáltatások létrehozására. Fedezze fel, és a fürt más szolgáltatásokkal kommunikálni, mikroszolgáltatási haladjon végig a következő lépéseket:

1. Oldja meg a helyét a elnevezési szolgáltatáson keresztül.
2. Csatlakozni a szolgáltatáshoz.
3. Az előző lépések burkolása szolgáltatás feloldási megvalósító ismétlődő, majd próbálja megismételni a csatlakozási hibák alkalmazni a házirendeket

További információkért lásd: [Connect és szolgáltatásokkal kommunikálni](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>A fordított proxy segítségével történő kommunikációhoz
Fordított proxy olyan szolgáltatás, amely minden csomóponton fut, és kezeli a végponti feloldási automatikus újra gombra, és más csatlakozási hibák ügyfélszolgáltatások nevében. Fordított proxy beállítható úgy, hogy különböző házirendeket alkalmazhat, kezeli az ügyfélszolgáltatások érkező kérelmeket. Fordított proxy használata lehetővé teszi az ügyfél szolgáltatás bármely ügyféloldali HTTP-kommunikáció szalagtárak és nem igényelnek különleges megoldás, és ismételje meg a szolgáltatás a logikai. 

Fordított proxy mutatja meg az egyéb szolgáltatásokhoz kérelmek küldéséhez használt szolgáltatásokhoz helyi csomóponton egy vagy több végpontot.

![Belső kommunikációs][1]

> [!NOTE]
> **A támogatott platformok**
>
> Fordított proxy a Service Fabric jelenleg a következő platformokat támogatja
> * *Windows-fürt*: Windows 8 és újabb vagy Windows Server 2012-es és újabb verziók
> * *Linux-fürt*: fordított Proxy már nem érhető el a Linux-fürtök
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>A fürtön kívüli a mikroszolgáltatások elérése
Az alapértelmezett külső kommunikáció modell mikroszolgáltatások létrehozására olyan opt-in modellt, ahol minden szolgáltatás nem érhető el közvetlenül a külső ügyfeleknek. [Az Azure Load Balancer](../load-balancer/load-balancer-overview.md), egy olyan hálózathatárhoz mikroszolgáltatások létrehozására és a külső ügyfelek közötti hálózati címfordítás végez, amely belső IP:port végpontok külső kérelmeket továbbítja. Ahhoz, hogy egy mikroszolgáltatási végpont közvetlenül elérhető külső ügyfelek számára, először konfigurálnia kell a port, amelyet a szolgáltatás használ a fürt forgalmának terheléselosztó. Továbbá a legtöbb mikroszolgáltatások, különösen akkor állapot-nyilvántartó mikroszolgáltatások nem élő a fürt összes csomópontján. A mikroszolgáltatások áthelyezheti a feladatátvételi csomópontjai között. Ebben az esetben a terheléselosztó hatékonyan nem tudja megállapítani a célcsomópont, amelyhez továbbítsa a forgalmat a replikák helyét.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>A fürtön kívüli a fordított proxyn keresztül mikroszolgáltatások elérése
Egy szolgáltatás portja konfigurálására a terheléselosztóhoz, konfigurálhatja csak a fordított proxy portját a terheléselosztó. Ez a konfiguráció lehetővé teszi az ügyfelek számára a fürtön kívüli elérni a fürtben található szolgáltatások további konfiguráció nélkül fordított proxy használatával.

![Külső kommunikáció][0]

> [!WARNING]
> A Load Balancer konfigurálásakor a fordított proxy port HTTP-végponttal visszaállítását a fürt összes mikroszolgáltatások a fürtön kívüli megcímezhető.
>
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>A fordított proxy használatával szolgáltatások címzéshez URI-formátum
A fordított proxyhoz megadott egységes erőforrás-azonosító (URI) formátumban a szolgáltatás partíció, amelyhez a bejövő kérelem továbbítani azonosításához használja:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** a fordított proxy beállítható úgy, hogy HTTP vagy HTTPS-forgalom fogadására. HTTPS-továbbítást, tekintse meg a [csatlakozás a biztonságos szolgáltatást a fordított proxy](service-fabric-reverseproxy-configure-secure-communication.md) a figyelést a HTTPS fordított proxy telepítése után.
* **Fürt teljesen minősített tartománynevét (FQDN) |} belső IP:** külső ügyfelek esetében konfigurálhatja a fordított proxy úgy, hogy a fürt tartományával, például a mycluster.eastus.cloudapp.azure.com keresztül érhető el. Alapértelmezés szerint a fordított proxy minden csomóponton fut. Belső forgalom esetén a fordított proxy elérhető localhost vagy minden csomópont belső IP-, például 10.0.0.1.
* **Port:** portja, például a 19081, amely a fordított proxy lett meghatározva.
* **ServiceInstanceName:** az üzemelő szolgáltatás példányának nélkül elérni próbált teljesen minősített neve a "fabric: /" sémával. Ahhoz például, hogy elérni a *fabric: / myapp/myservice/* szolgáltatást szeretné használni *myapp/myservice*.

    A szolgáltatás példány neve megkülönbözteti a kis-és nagybetűket. Egy másik kis-és a szolgáltatás neve az URL-cím használata azt eredményezi, a kérelem a 404-es (nem található) sikertelen lesz.
* **Utótag elérési út:** Ez az a tényleges URL-címe, mint például az *myapi/értékek/hozzáadása/3*, a szolgáltatás, amelyhez csatlakozni kíván.
* **PartitionKey:** egy particionált szolgáltatást, ez pedig a számított partíciókulcs a partíció az elérni kívánt. Vegye figyelembe, hogy ez *nem* a partíció GUID azonosítója. Ez a paraméter nincs szükség a singleton partícióséma használó szolgáltatások.
* **PartitionKind:** Ez a szolgáltatás partíciós sémája. Ez lehet "Int64Range" vagy "Nevű". Ez a paraméter nincs szükség a singleton partícióséma használó szolgáltatások.
* **ListenerName** a végpontok a szolgáltatástól a következő formában vannak {"Végpont": {"Listener1": "Endpoint1", "Listener2": "Endpoint2" …}}. Ha a szolgáltatás elérhetővé teszi a több végpontot, ez azonosítja a végpontot, amely az ügyfél kérésében továbbítani kell. Ez a szolgáltatás csak egy figyelő e elhagyható.
* **TargetReplicaSelector** azt határozza meg, hogy a cél replika- vagy kell kiválasztani.
  * Ha a célként megadott szolgáltatás állapotalapú, a TargetReplicaSelector a következők egyike lehet: "PrimaryReplica", "RandomSecondaryReplica" vagy "RandomReplica". Ha ez a paraméter nincs megadva, az alapértelmezett érték "PrimaryReplica".
  * Ha a célként megadott szolgáltatás állapotmentes, fordított proxy szerzi véletlenszerű példánya a service partíció továbbítja a kérést.
* **Időtúllépés:** azt határozza meg, hogy a szolgáltatás nevében az ügyfélkérés fordított proxy által létrehozott HTTP-kérelem időtúllépés. Az alapértelmezett értéke 60 másodperc. Ez nem kötelező paraméter.

### <a name="example-usage"></a>Példa használati
Példaként vegyünk a *fabric: / MyApp/MyService* , amely megnyitja a következő URL-cím a HTTP-figyelő szolgáltatás:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Az alábbiakban a erőforrásait a szolgáltatáshoz:

* `/index.html`
* `/api/users/<userId>`

Ha a szolgáltatás használ a singleton particionálási sémát, a *PartitionKey* és *PartitionKind* lekérdezési karakterlánc paraméterek esetén nincs szükség, és a szolgáltatás elérhető az átjáró, mint:

* Külsőleg:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Belső:`http://localhost:19081/MyApp/MyService`

Ha a szolgáltatás egységes Int64 particionálási sémát, használja a *PartitionKey* és *PartitionKind* lekérdezési karakterlánc paramétereket kell használni a szolgáltatás partíció elérésére:

* Külsőleg:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Belső:`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Amely a szolgáltatás elérhetővé teszi az erőforrások eléréséhez egyszerűen helyezze el az erőforrás elérési útja után a szolgáltatás nevét az URL-címe:

* Külsőleg:`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Belső:`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Az átjáró ezután továbbítja ezeket a kéréseket a szolgáltatás URL-címe:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>A port megosztása különleges kezelést szolgáltatások
A Service Fabric fordított proxy próbálja újra feloldani a szolgáltatás címét, és ismételje meg a kérelmet, ha a szolgáltatás nem érhető el. Általában a szolgáltatás nem érhető el, amikor a szolgáltatáspéldány, vagy a replika áthelyezte a normál életciklusának egy másik csomópont. Ha ez történik, a fordított proxy arról, hogy a végpont már nem nyitva eredetileg feloldott címet a hálózati kapcsolódási hiba fordulhat elő.

Azonban replikák és a szolgáltatáspéldány egy gazdafolyamaton megoszthatnak, és előfordulhat, hogy is megoszthat egy portot, ha azt egy http.sys alapú webkiszolgálóhoz, beleértve:

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Ebben az esetben valószínű, hogy a webkiszolgáló nem érhető el a gazdagép-folyamat és válaszol a kérelmekre, de a szolgáltatáspéldány megoldott vagy a replika már nem érhető el a gazdagépen. Ebben az esetben az átjáró kap egy HTTP 404-es választ a webkiszolgálón. HTTP 404-es választ, így két különböző jelentését veheti fel:

- #1. eset: A szolgáltatás címe helyes, de a felhasználó a kért erőforrás nem létezik.
- #2. eset: A szolgáltatás címe nem megfelelő, és az erőforrás, a felhasználó azt kérte, előfordulhat, hogy létezik egy másik csomópontjára.

Az első esetben egy normál HTTP 404-es, amely felhasználói hiba történt. A második esetben azonban a felhasználó kérelmezte a erőforrása, amely létezik. A fordított proxy nem tudott keresse meg, mert a szolgáltatás át lett helyezve. A fordított proxyhoz kell újra feloldani a címet, és próbálkozzon újra a kéréssel.

A fordított proxy így megkülönböztetheti két esetben úgy kell. Ahhoz, hogy a különbség, egy a kiszolgáló megadása szükséges.

* Alapértelmezés szerint a fordított proxy azt feltételezi, hogy a #2. eset, és megkísérli feloldani, és adja ki újra a kérelmet.
* Jelzi a fordított proxykiszolgálóhoz #1. eset, a szolgáltatás a következő HTTP-válaszfejléc kell visszaadnia:

  `X-ServiceFabric : ResourceNotFound`

A HTTP-válaszfejléc azt jelzi, hogy egy normál HTTP 404-es helyzetet, amelyben a kért erőforrás nem létezik, és a fordított proxy nem kísérli meg újra a szolgáltatást címek feloldására.

## <a name="setup-and-configuration"></a>Beállítás és konfiguráció

### <a name="enable-reverse-proxy-via-azure-portal"></a>Azure-portálon fordított proxy engedélyezése

Azure-portált biztosít fordított proxy engedélyezni egy új Service Fabric-fürt létrehozása során.
A **létrehozása a Service Fabric-fürt**, 2. lépés: fürtkonfiguráció esetén a csomópont-konfiguráció típusát, jelölje be a "Fordított proxy engedélyezése" jelölőnégyzetet.
Biztonságos fordított proxy konfigurálása az SSL-tanúsítvány adható meg a 3. lépés: biztonsági, biztonsági beállítások konfigurálása, jelölje be a jelölőnégyzetet "Közé tartozik egy SSL-tanúsítvány a fordított proxyhoz", és írja be a tanúsítvány adatait.

### <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Fordított proxyn keresztül Azure Resource Manager-sablonok engedélyezése

Használhatja a [Azure Resource Manager sablon](service-fabric-cluster-creation-via-arm.md) ahhoz, hogy a fürt a Service Fabric a fordított proxyhoz.

Tekintse meg [beállítása HTTPS fordított Proxy biztonságos fürtben](https://github.com/ChackDan/Service-Fabric/tree/master/ARM Templates/ReverseProxySecureSample#configure-https-reverse-proxy-in-a-secure-cluster) az Azure Resource Manager sablon minták konfigurálása biztonságos fordított proxy tanúsítvány és kezelési tanúsítványok leváltása.

Először a sablon lekérése a fürt, amely számára telepíteni kívánja. A minta-sablonok, vagy hozzon létre egy egyéni erőforrás-kezelő sablont. A fordított proxy engedélyezéséhez, majd az alábbi lépéseket követve:

1. A port megadása a fordított proxy a [paraméterek szakaszban](../azure-resource-manager/resource-group-authoring-templates.md) sablon.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Adja meg a portot a nodetype objektumok mindegyikének a **fürt** [erőforrás típushoz című rész](../azure-resource-manager/resource-group-authoring-templates.md).

    A port a paraméternév megadásához, reverseProxyEndpointPort azonosít.

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
3. Azure fürtön kívül a fordított proxyhoz kielégítéséhez, a portot, amelyen az 1. lépésben megadott Azure Load Balancer szabályainak beállítása.

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
4. A fordított proxy konfigurálása a port SSL-tanúsítványokat, vegye fel a tanúsítványt a ***reverseProxyCertificate*** tulajdonságot a **fürt** [erőforrás típushoz című rész](../resource-group-authoring-templates.md).

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>A fordított proxy tanúsítvány, amely eltér a fürt tanúsítvány támogatása
 Ha a fordított proxy tanúsítvány eltér a tanúsítványt, amely a fürt titkosítja, majd a korábban megadott tanúsítvány legyen telepítve a virtuális gépen, és hozzáadni a hozzáférés-vezérlési lista (ACL), hogy a Service Fabric-e hozzáférési engedélye. Ehhez a **virtualMachineScaleSets** [erőforrás típushoz című rész](../resource-group-authoring-templates.md). A telepítéshez a osProfile tanúsítvány hozzáadása. A sablon bővítmény szakasza frissítheti a tanúsítványt a hozzáférés-vezérlési listában.

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
> Tanúsítványokat, amelyek eltérnek a fürt tanúsítvánnyal ahhoz, hogy egy meglévő fürt fordított proxy használata esetén a fordított proxy tanúsítvány telepítése és frissítése a hozzáférés-vezérlési lista a fürtön, mielőtt engedélyezné a fordított proxy. Fejezze be a [Azure Resource Manager sablon](service-fabric-cluster-creation-via-arm.md) központi telepítéskor az említett beállításokkal korábban a központi telepítés engedélyezése a fordított proxy megkezdése előtt a lépések 1-4.

## <a name="next-steps"></a>További lépések
* Példa a szolgáltatások közötti HTTP-kommunikációt egy [mintaprojektet a Githubon](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [A fordított proxy-továbbítást a biztonságos HTTP-szolgáltatás](service-fabric-reverseproxy-configure-secure-communication.md)
* [Távoli eljáráshívások a Reliable Services távoli eljáráshívás](service-fabric-reliable-services-communication-remoting.md)
* [Webes API-t használó OWIN Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [WCF-kommunikáció Reliable Services használatával](service-fabric-reliable-services-communication-wcf.md)
* A további fordított proxy konfigurációs lehetőségeket, tekintse meg a alapú/Http című [testre szabhatja a Service Fabric-fürt beállítások](service-fabric-cluster-fabric-settings.md).

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
