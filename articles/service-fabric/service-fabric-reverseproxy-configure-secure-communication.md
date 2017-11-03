---
title: "Az Azure Service Fabric fordított proxy biztonságos kommunikációs |} Microsoft Docs"
description: "Ahhoz, hogy biztonságos-végpontok közötti kommunikáció fordított proxy konfigurálása."
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 568f9638c59282bcd7d3fae058a1588a889c22dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Csatlakozzon egy biztonságos szolgáltatáshoz a fordított proxy

Ez a cikk azt ismerteti, hogyan közötti a fordított proxy és a szolgáltatások, így egy végpontok közötti biztonságos csatorna biztonságos kapcsolat létrehozásához.

Biztonságos szolgáltatásokhoz való kapcsolódás esetén támogatott csak fordított proxy a figyelést a HTTPS van konfigurálva. A dokumentum többi részén azt feltételezi, hogy ez a helyzet.
Tekintse meg [fordított proxy az Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) a fordított proxy konfigurálása a Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>A fordított proxy és a szolgáltatások közötti biztonságos kapcsolat felépítése 

### <a name="reverse-proxy-authenticating-to-services"></a>Fordított proxy services hitelesítéséhez:
A fordított proxy azonosítja magát a tanúsítványt használni, a megadott szolgáltatások ***reverseProxyCertificate*** tulajdonságot a **fürt** [erőforrás típushoz című rész](../azure-resource-manager/resource-group-authoring-templates.md). Szolgáltatások ellenőrzése a tanúsítvány a fordított proxy logikát is létrehozható. A szolgáltatások konfigurációs beállítások a konfigurációs csomag megadható az elfogadható ügyfél-tanúsítvány részletei. A futási időben olvashatja és a fordított proxyhoz tartozó a tanúsítvány érvényesítéséhez használt. Tekintse meg [alkalmazás paraméterek kezelése](service-fabric-manage-multiple-environment-app-configuration.md) vegye fel a konfigurációs beállításokat. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Fordított proxyn keresztül a szolgáltatás által bemutatott tanúsítványt a szolgáltatás identitás ellenőrzése:
Kiszolgálói tanúsítvány hitelesítése a tanúsítványok, a szolgáltatások által bemutatott végrehajtásához fordított proxy támogatja a következő lehetőségek egyikét: None, ServiceCommonNameAndIssuer és ServiceCertificateThumbprints.
Válassza ki a három paraméterek egyikét, adja meg a **ApplicationCertificateValidationPolicy** alapú/Http elem alatt a Paraméterek szakaszban [fabricSettings](service-fabric-cluster-fabric-settings.md).

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "None"
              }
            ]
          }
        ],
        ...
}
```

A következő szakasz ezeket a beállításokat minden további konfigurációs vonatkozó további információért tekintse meg.

### <a name="service-certificate-validation-options"></a>Szolgáltatásbeállítások tanúsítvány érvényesítése 

- **Nincs**: fordított proxy kihagyja a proxy tanúsítvány ellenőrzése és a biztonságos kapcsolatot. Ez az alapértelmezett viselkedése.
Adja meg a **ApplicationCertificateValidationPolicy** értékű **nincs** alapú/Http elem a Paraméterek szakaszban.

- **ServiceCommonNameAndIssuer**: fordított proxy ellenőrzi a tanúsítvány köznapi nevének és azonnali kibocsátójának ujjlenyomata alapján szolgáltatás által bemutatott tanúsítványt: Adja meg a **ApplicationCertificateValidationPolicy** értékű **ServiceCommonNameAndIssuer** alapú/Http elem a Paraméterek szakaszban.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCommonNameAndIssuer"
              }
            ]
          }
        ],
        ...
}
```

Szolgáltatás köznapi név és a kiállító ujjlenyomatok listájának megadásához adja hozzá a **alapú/Http/ServiceCommonNameAndIssuer** elem alatt fabricSettings, az alább látható módon. A paraméterek tömbelem több tanúsítvány köznapi név és kiállítójának ujjlenyomata párok lehet hozzáadni. 

Ha a végpont fordított proxy csatlakozik, hogy megadja egy tanúsítványt, aki közös nevét és kiállítójának ujjlenyomata megegyezik-e az itt megadott értékek, akkor SSL-csatorna jön létre. A tanúsítvány részleteinél egyező sikertelenség fordított proxy az ügyfélkérés 502 (Hibás átjáró)-állapotkód: sikertelen lesz. A HTTP állapotsora is tartalmazni fog a kifejezés "Érvénytelen SSL-tanúsítvány." 

```json
{
"fabricSettings": [
          ...
          {
             "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
            "parameters": [
              {
                "name": "WinFabric-Test-Certificate-CN1",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
              },
              {
                "name": "WinFabric-Test-Certificate-CN2",
                "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
              }
            ]
          }
        ],
        ...
}
```


- **ServiceCertificateThumbprints**: fordított proxy annak ujjlenyomata a proxy szolgáltatástanúsítványban ellenőrzi. Ha szeretné, nyissa meg az útvonal, ha a szolgáltatás úgy van konfigurálva, az önkiszolgáló aláírt tanúsítványok: Adja meg a **ApplicationCertificateValidationPolicy** értékű **ServiceCertificateThumbprints** alapú/Http elem a Paraméterek szakaszban.

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
              {
                "name": "ApplicationCertificateValidationPolicy",
                "value": "ServiceCertificateThumbprints"
              }
            ]
          }
        ],
        ...
}
```

Az ujjlenyomatok is megadhat egy **ServiceCertificateThumbprints** paraméterek bemutató alapú/Http elem. Alább látható módon az érték mezőben vesszővel tagolt lista formájában adható meg több ujjlenyomatok:

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "ServiceCertificateThumbprints",
                "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
              }
            ]
          }
        ],
        ...
}
```

Ha a tanúsítvány ujjlenyomatát a konfigurációs bejegyzés is szerepel, a fordított proxy az SSL-kapcsolat sikeres lesz. Ellenkező esetben megszakítja a kapcsolatot, és nem sikerül az ügyfél kérelem 502-es (Hibás átjáró). A HTTP állapotsora is tartalmazni fog a kifejezés "Érvénytelen SSL-tanúsítvány."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Ha a szolgáltatások elérhetővé tenni a biztonságos, valamint az olyan nem biztonságos végpontok végpont lemezválasztási logika
A Service fabric szolgáltatás több végpontot konfigurálását támogatja. Lásd: [Specify resources in a service manifest](service-fabric-service-manifest-resources.md) (Erőforrások megadása a szolgáltatásjegyzékben).

Fordított proxy kiválaszt egy továbbítja a kérelmet alapján a végpontokat a **ListenerName** lekérdezési paraméter. Ha nincs megadva, kiválaszthatja a tetszőleges végpontot végpontok listájáról. Ez lehet most egy HTTP vagy HTTPS-végponton. Előfordulhat, forgatókönyvek/követelmények működését a "biztonságos mód", azaz a fordított proxyhoz, ahová nem szeretné, hogy a biztonságos fordított proxy kérések nem biztonságos végpontokkal való továbbítására. Ez a megadásával érhető el a **SecureOnlyMode** konfigurációs bejegyzés értékű **igaz** alapú/Http elem a Paraméterek szakaszban.   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> 
> Ha a **SecureOnlyMode**, ha az ügyfél megadott egy **ListenerName** megfelelő HTTP(unsecured) végpont, fordított proxy a kérelem a 404-es (nem található) HTTP-állapotkód: sikertelen.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>A fordított proxyn keresztül ügyféltanúsítvány-alapú hitelesítés beállítása
SSL-lezárást történik meg a fordított proxy, és az ügyfél tanúsítvány minden adat elveszik. Ügyféltanúsítvány-alapú hitelesítés elvégzéséhez a szolgáltatásokat, állítsa be a **ForwardClientCertificate** a Paraméterek szakaszban alapú/Http elem beállítása.

1. Ha **ForwardClientCertificate** értéke **hamis**, fordított proxy nem kéri az ügyféltanúsítványt az SSL-kézfogás során az ügyfél.
Ez az alapértelmezett viselkedése.

2. Ha **ForwardClientCertificate** értéke **igaz**, fordított proxy kérelmek az ügyféltanúsítványt az ügyfél az SSL-kézfogás során.
Majd továbbítja az ügyfél tanúsítványának adatait nevű egyéni HTTP-fejlécben **X ügyféltanúsítvány**. A fejléc értéke az ügyfél tanúsítványáról base64-kódolású PEM formázó karakterlánc. A szolgáltatás képes sikeres/sikertelen a kérelem megfelelő állapotkód: a tanúsítvány adatainak ellenőrzése után.
Ha az ügyfél nincs jelen tanúsítvány, a fordított proxy egy üres fejléc továbbítja, és lehetővé teszik a szolgáltatás kezelése a.

> Fordított proxy puszta továbbító. Bármely az ügyféltanúsítvány érvényesítése nem hajtja végre.


## <a name="next-steps"></a>Következő lépések
* Tekintse meg [fordított proxy konfigurálása a biztonságos szolgáltatásokhoz](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) az Azure Resource Manager sablon minták konfigurálása biztonságos különböző szolgáltatástanúsítvány fordított proxy ellenőrzési beállítások.
* Példa a szolgáltatások közötti HTTP-kommunikációt egy [mintaprojektet a Githubon](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Távoli eljáráshívások a Reliable Services távoli eljáráshívás](service-fabric-reliable-services-communication-remoting.md)
* [Webes API-t használó OWIN Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md)
