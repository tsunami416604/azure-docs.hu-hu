---
title: Az Azure Service Fabric fordított proxy biztonságos kommunikáció |} A Microsoft Docs
description: Végpontok közötti biztonságos kommunikáció engedélyezése a fordított proxy konfigurálása.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 0558a5647267dda26890ba3a6dc1af326fae94f6
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308163"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Csatlakozzon a fordított proxy egy biztonságos szolgáltatáshoz

Ez a cikk ismerteti a fordított proxy és a szolgáltatások, ezáltal lehetővé téve egy végpontok közötti biztonságos csatorna közötti biztonságos kapcsolatot létesíteni.

Csatlakozás biztonságos szolgáltatások támogatott csak akkor, ha a fordított proxy HTTPS figyelésére van konfigurálva. A dokumentum többi része feltételezi, hogy ez a helyzet.
Tekintse meg [fordított proxy az Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) a fordított proxy konfigurálása a Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>A fordított proxy és a szolgáltatások közötti biztonságos kapcsolat létrehozása 

### <a name="reverse-proxy-authenticating-to-services"></a>Fordított proxy szolgáltatásokhoz való hitelesítéséhez:
A fordított proxy azonosítja magát a tanúsítványt használni, a megadott szolgáltatások ***reverseProxyCertificate*** tulajdonságot a **fürt** [erőforrás típushoz című](../azure-resource-manager/resource-group-authoring-templates.md). Szolgáltatások ellenőrizni a fordított proxy által bemutatott tanúsítványt logikát valósíthat meg. A szolgáltatások konfigurációs beállításokat a konfigurációs csomag megadható az elfogadható ügyfél-tanúsítvány részletei. Ez futásidőben olvashatók és a fordított proxy által bemutatott tanúsítványt érvényesítéséhez használt. Tekintse meg [alkalmazásparaméterek kezelése](service-fabric-manage-multiple-environment-app-configuration.md) fel a konfigurációs beállításokat. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Fordított proxyn keresztül a szolgáltatás által bemutatott tanúsítványt a szolgáltatás identitás ellenőrzése:
Fordított proxy támogatja a kiszolgálói tanúsítvány hitelesítése a szolgáltatások által bemutatott tanúsítványok végrehajtásához a következő házirendek: None, ServiceCommonNameAndIssuer és ServiceCertificateThumbprints.
Szabályzat kiválasztása a fordított proxy használni, adja meg a **ApplicationCertificateValidationPolicy** a a **ApplicationGateway/Http** szakaszba [fabricSettings](service-fabric-cluster-fabric-settings.md).

A következő szakasz ezeket a beállításokat minden konfigurációs részleteket jeleníti meg.

### <a name="service-certificate-validation-options"></a>Tanúsítvány érvényességi Szolgáltatásbeállítások 

- **Nincs**: fordított proxy kihagyja a proxy tanúsítvány ellenőrzése és a biztonságos kapcsolatot létesít. Ez az az alapértelmezett viselkedést.
Adja meg a **ApplicationCertificateValidationPolicy** értékkel **nincs** a a [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.

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

- **ServiceCommonNameAndIssuer**: fordított proxy ellenőrzi a tanúsítvány köznapi nevéből és azonnali kibocsátói ujjlenyomat alapján a szolgáltatás által bemutatott tanúsítványt: Adja meg a **ApplicationCertificateValidationPolicy** értékkel **ServiceCommonNameAndIssuer** a a [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.

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

   Adja meg a szolgáltatás köznapi név és kibocsátójának ujjlenyomatai listáját, adjon hozzá egy [ **ApplicationGateway/Http/ServiceCommonNameAndIssuer** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) szakaszba **fabricSettings**, ahogy az alábbi. Több tanúsítvány köznapi neve és kiállító ujjlenyomata párt lehet hozzáadni a **paraméterek** tömb. 

   Ha a végpont fordított proxy csatlakozik, hogy megadja egy tanúsítványt, akik gyakori neve és kiállító ujjlenyomata megegyezik az itt megadott értékeket, SSL-csatorna létrejött. 
   A tanúsítvány adatainak megfelelően sikertelenség fordított proxy 502 (Hibás átjáró) állapotkód az ügyfél kérése sikertelen lesz. A HTTP-állapot sort is tartalmazni fog a következő kifejezést: "Érvénytelen SSL-tanúsítvány." 

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

- **ServiceCertificateThumbprints**: fordított proxy ellenőrzi, hogy a proxy tanúsítvány az ujjlenyomat alapján. Dönthet úgy, hogy nyissa meg ezt az útvonalat, amikor a szolgáltatás úgy van konfigurálva, a saját önaláírt tanúsítványok: Adja meg a **ApplicationCertificateValidationPolicy** értékkel **ServiceCertificateThumbprints** a a [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.

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

   Az ujjlenyomatok az is meg egy **ServiceCertificateThumbprints** bejegyzést a **ApplicationGateway/Http** szakaszban. Több ujjlenyomatok az érték mezőbe egy vesszővel tagolt lista formájában adható meg lent látható módon:

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

   Ha a tanúsítvány ujjlenyomatát a konfigurációs bejegyzés szerepel, a fordított proxy sikeres, az SSL-kapcsolatot. Ellenkező esetben lezárja a kapcsolatot, és nem sikerül az ügyfél kérése az 502-es (Hibás átjáró). A HTTP-állapot sort is tartalmazni fog a következő kifejezést: "Érvénytelen SSL-tanúsítvány."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Végpont lemezválasztási logika amikor szolgáltatások biztonságos, valamint a nem védett végpontokat tesznek közzé.
Több végpont szolgáltatás konfigurálása a Service fabric támogatja. További információkért lásd: [erőforrások meghatározása szolgáltatásjegyzékben](service-fabric-service-manifest-resources.md).

Fordított proxy kiválaszt egy, a kérelem alapján továbbítja a végpontok a **ListenerName** lekérdezési paraméter az a [szolgáltatás URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Ha a **ListenerName** paraméter nincs megadva, a fordított proxy minden végpontot a végpontok listából választhatja ki. Attól függően, a végpont konfigurálva a szolgáltatáshoz a kiválasztott végpont lehet egy HTTP vagy HTTPS-végpont. Előfordulhat, forgatókönyvek és a követelmények ahol azt szeretné, hogy a fordított proxy módban egy "csak biztonságos"; azt jelenti nem szeretné a biztonságos fordított proxy nem biztonságos végpontokra irányuló kérések továbbítására. Fordított proxy beállítása csak biztonságos módban, adja meg a **SecureOnlyMode** konfigurációs bejegyzés értékkel **igaz** a a [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.   

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

> [!NOTE]
> Ha a **SecureOnlyMode**, ha egy ügyfél megadott egy **ListenerName** fordított proxy nem sikerül megfelelő HTTP(unsecured) végpont, a kérelem a 404-es (nem található) HTTP-állapotkóddal.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>A fordított proxyn keresztül ügyféltanúsítvány-alapú hitelesítés beállítása
A fordított proxyk SSL-lezárást történik, és az ügyfél tanúsítványt minden adat elveszik. A szolgáltatások ügyféltanúsítvány-alapú hitelesítés végrehajtásához adja meg a **ForwardClientCertificate** beállítását a [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.

1. Amikor **ForwardClientCertificate** értékre van állítva **hamis**, fordított proxy nem kéri az ügyféltanúsítványt az SSL-kézfogás során az ügyféllel.
Ez az az alapértelmezett viselkedést.

2. Amikor **ForwardClientCertificate** értékre van állítva **igaz**, fordított proxy kéréseket az ügyfél-tanúsítványt az SSL-kézfogás során a az ügyfél.
Majd továbbítja az ügyfél egy egyéni HTTP-fejlécben nevű Tanúsítványadatok **X ügyféltanúsítvány**. A fejléc értéke az ügyféltanúsítvány base64-kódolású PEM formátumú karakterlánc. A szolgáltatás is sikeres/sikertelen a kérelem megfelelő állapotkód: a tanúsítvány adatainak ellenőrzése után.
Ha az ügyfél nincs jelen tanúsítvány, fordított proxy egy üres fejlécet továbbítja, és megadásával letiltható a szolgáltatás a helyzet kezeléséhez.

> [!NOTE]
> Fordított proxy puszta továbbító. Bármely az ügyféltanúsítvány érvényesítése nem hajtja végre.


## <a name="next-steps"></a>További lépések
* Tekintse meg [fordított proxy konfigurálása biztonságos serviceshez való csatlakozáshoz](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) az Azure Resource Manager sablonminták konfigurálása védelmét a különböző szolgáltatási tanúsítvány a fordított proxy ellenőrzési lehetőségeit.
* Tekintse meg a szolgáltatások közötti HTTP-kommunikációt egy példát egy [mintaprojektet a Githubon](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [A Reliable Services-táveléréssel kezdeményezett távoli eljáráshívások](service-fabric-reliable-services-communication-remoting.md)
* [A Reliable Services OWIN használó webes API](service-fabric-reliable-services-communication-webapi.md)
* [Fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md)
