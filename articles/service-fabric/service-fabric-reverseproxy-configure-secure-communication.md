---
title: Az Azure Service Fabric fordított proxy biztonságos kommunikációja
description: Konfigurálja a fordított proxyt a biztonságos végpontok közötti kommunikáció engedélyezéséhez egy Azure Service Fabric-alkalmazásban.
author: kavyako
ms.topic: conceptual
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 4cfeaf34a39231ffa91ea970a61f66632bae40c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282249"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Csatlakozás biztonságos szolgáltatáshoz a fordított proxyval

Ez a cikk bemutatja, hogyan hozhat létre biztonságos kapcsolatot a fordított proxy és a szolgáltatások között, így lehetővé téve a végpontok közötti biztonságos csatornát. Ha többet szeretne megtudni a fordított proxyról, olvassa [el a Proxy visszafordítása az Azure Service Fabricben](service-fabric-reverseproxy.md)

A biztonságos szolgáltatásokhoz való csatlakozás csak akkor támogatott, ha a fordított proxy https-kapcsolaton történő figyelésre van beállítva. Ez a cikk feltételezi, hogy ez a helyzet.
Tekintse meg [a telepítés fordított proxy az Azure Service Fabric](service-fabric-reverseproxy-setup.md) konfigurálása a fordított proxy a Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Biztonságos kapcsolat létrehozása a fordított proxy és a szolgáltatások között 

### <a name="reverse-proxy-authenticating-to-services"></a>Fordított proxy hitelesítése szolgáltatásokhoz:
A fordított proxy azonosítja magát a tanúsítványt használó szolgáltatások számára. Az Azure-fürtök esetében a tanúsítvány ***reverseProxyCertificate*** tulajdonsággal van megadva az Erőforrás-kezelő sablon [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [Resource type szakaszában.](../azure-resource-manager/templates/template-syntax.md) Önálló fürtök esetén a tanúsítvány a ClusterConfig.json **Biztonsági** szakaszában található ***ReverseProxyCertificate*** vagy ***ReverseProxyCertificateCommonNames*** tulajdonsággal van megadva. További információ: [Fordított proxy engedélyezése önálló fürtökön](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

A szolgáltatások megvalósíthatják a logikát a fordított proxy által bemutatott tanúsítvány ellenőrzéséhez. A szolgáltatások megadhatják az elfogadott ügyféltanúsítvány adatait konfigurációs beállításokként a konfigurációs csomagban. Ez futásidőben olvasható, és a fordított proxy által bemutatott tanúsítvány érvényesítésére használható. A [konfigurációs](service-fabric-manage-multiple-environment-app-configuration.md) beállítások hozzáadásához olvassa el az Alkalmazásparaméterek kezelése című hivatkozást. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>A szolgáltatás identitását a szolgáltatás által bemutatott tanúsítványon keresztül ellenőrző proxy:
A fordított proxy a következő házirendeket támogatja a szolgáltatások által bemutatott tanúsítványok kiszolgálói tanúsítvány-ellenőrzésének végrehajtásához: Nincs, ServiceCommonNameAndIssuer és ServiceCertificateThumbprints.
A fordított proxy házirendjének kiválasztásához adja meg az **ApplicationCertificateValidationPolicy parancsot** az **ApplicationGateway/Http** [szakaszfabricSettings](service-fabric-cluster-fabric-settings.md)csoportjában.

A következő szakasz az egyes beállítások konfigurációs részleteit mutatja be.

### <a name="service-certificate-validation-options"></a>Szolgáltatási tanúsítványok ellenőrzési beállításai 

- **Nincs**: A fordított proxy kihagyja a proxied service tanúsítvány ellenőrzését, és létrehozza a biztonságos kapcsolatot. Ez az alapértelmezett viselkedés.
Adja meg az **ApplicationCertificateValidationPolicy** értéket **nincs** értékkel az [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.

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

- **ServiceCommonNameAnd Issuer**: A fordított proxy a tanúsítvány köznapi neve és azonnali kibocsátójának ujjlenyomata alapján ellenőrzi a szolgáltatás által bemutatott tanúsítványt: Adja meg az **ApplicationCertificateValidationPolicy** értéket a **ServiceCommonNameAndIssuer** értékkel az [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.

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

   A szolgáltatás köznapi nevének és kiállítói ujjlenyomatainak listájának megadásához adjon hozzá egy [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) szakaszt **a fabricSettings**csoportban, az alábbiak szerint. A **paraméterek** tömbjében több tanúsítvány köznapi neve és kiállítóujjlenyomat-párja is hozzáadható. 

   Ha a végpont fordított proxy csatlakozik bemutatja a tanúsítványt, aki a köznapi neve és a kibocsátó ujjlenyomata megegyezik az itt megadott értékek bármelyikével, SSL-csatorna jön létre. 
   Ha nem egyezteti a tanúsítvány adatait, a fordított proxy meghiúsul az ügyfél kérésének egy 502-es (Bad Gateway) állapotkóddal. A HTTP állapotsor az "Érvénytelen SSL-tanúsítvány" kifejezést is tartalmazza. 

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

- **ServiceCertificateThumbprints**: Reverse proxy ellenőrzi a proxied szolgáltatás tanúsítvány alapján ujjlenyomatát. Ezt az útvonalat akkor is megválaszthatja, ha a szolgáltatások önaláírt tanúsítványokkal vannak konfigurálva: Adja meg az **ApplicationCertificateValidationPolicy** értéket kapta **ServiceCertificateThumbprints** értékkel az [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.

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

   Adja meg az ujjlenyomatokat is egy **ServiceCertificateThumbprints** bejegyzéssel az **ApplicationGateway/Http** szakaszban. Az értékmezőben több ujjlenyomat is megadható vesszővel tagolt listaként, az alábbiak szerint:

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

   Ha a kiszolgálótanúsítvány ujjlenyomata szerepel ebben a konfigurációs bejegyzésben, a fordított proxy váltja az SSL-kapcsolatot. Ellenkező esetben megszakítja a kapcsolatot, és nem felel meg az ügyfél kérésének egy 502-es (Bad Gateway) segítségével. A HTTP állapotsor az "Érvénytelen SSL-tanúsítvány" kifejezést is tartalmazza.

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Végpontkijelölési logika, amikor a szolgáltatások biztonságos és nem biztonságos végpontokat biztosítanak
A service fabric támogatja a szolgáltatás több végpontjának konfigurálását. További információt az [Erőforrások megadása szolgáltatásjegyzékben című témakörben talál.](service-fabric-service-manifest-resources.md)

A névfeloldási proxy kiválasztja az egyik végpontot a kérelem továbbításához a [szolgáltatás URI-jában](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy)lévő **ListenerName** lekérdezési paraméter alapján. Ha a **ListenerName** paraméter nincs megadva, a fordított proxy bármelyik végpontot kiválaszthat a végpontok listájából. A szolgáltatáshoz konfigurált végpontoktól függően a kijelölt végpont lehet HTTP vagy HTTPS végpont. Előfordulhatnak olyan esetek vagy követelmények, amikor azt szeretné, hogy a fordított proxy "csak biztonságos módban" működjön; ez azt, hogy nem szeretné, hogy a biztonságos fordított proxy továbbítsa a kérelmeket a nem biztonságos végpontok. Ha a fordított proxyt csak biztonságos módba szeretné állítani, adja meg a **SecureOnlyMode** konfigurációs **bejegyzést,** amelynek értéke igaz az [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.   

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
> Ha a **SecureOnlyMode**rendszerben működik, ha az ügyfél http(nem biztonságos) végpontnak megfelelő **Figyelőnevet** adott meg, a fordított proxy 404-es (nem található) HTTP-állapotkóddal nem felel meg a kérésnek.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Ügyféltanúsítvány-hitelesítés beállítása a fordított proxyn keresztül
Az SSL-végződés a fordított proxynál történik, és az ügyféltanúsítvány összes adata elvész. Ahhoz, hogy a szolgáltatások végrehajtsák az ügyféltanúsítvány-hitelesítést, adja meg a **ForwardClientCertificate** beállítást az [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) szakaszban.

1. Ha a **ForwardClientCertificate** **értéke hamis,** a fordított proxy nem kéri az ügyféltanúsítványt az ügyféllel folytatott SSL-kézfogás során.
Ez az alapértelmezett viselkedés.

2. Ha a **ForwardClientCertificate** **értéke igaz,** a fordított proxy az ügyféllel folytatott SSL-kézfogás során kéri az ügyfél tanúsítványát.
Ezután továbbítja az ügyféltanúsítvány adatait egy **X-Client-Certificate nevű egyéni HTTP-fejlécben.** A fejlécértéke az ügyfél tanúsítványának base64 kódolású PEM formátumú karakterlánca. A tanúsítvány adatainak vizsgálata után a szolgáltatás sikeres/sikertelen lehet a megfelelő állapotkóddal rendelkező kérelemben.
Ha az ügyfél nem mutat be tanúsítványt, a fordított proxy továbbít egy üres fejlécet, és hagyja, hogy a szolgáltatás kezelje az esetet.

> [!NOTE]
> Fordított proxy egy egyszerű forwarder. Nem hajtja végre az ügyfél tanúsítványának érvényesítését.


## <a name="next-steps"></a>További lépések
* [Fordított proxy beállítása és konfigurálása fürtön.](service-fabric-reverseproxy-setup.md)
* Lásd: [Fordított proxy konfigurálása a biztonságos szolgáltatásokhoz való csatlakozáshoz](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Tekintse meg a http-kommunikáció t a Szolgáltatások közötti kommunikáció egy [minta projekt a GitHubon.](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Távoli eljáráshívások a Megbízható szolgáltatások távoli hívásával](service-fabric-reliable-services-communication-remoting.md)
* [Az OWIN alkalmazást használó webes API a Megbízható szolgáltatásokban](service-fabric-reliable-services-communication-webapi.md)
* [Fürttanúsítványok kezelése](service-fabric-cluster-security-update-certs-azure.md)
