---
title: Mi az Azure Application Gateway?
description: Megtudhatja, hogyan kezelheti az alkalmazása webes forgalmát az Azure Application Gateway segítségével.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 11/23/2019
ms.author: victorh
ms.openlocfilehash: a61b1a44419ac35efa5888de2b5a6e4988dfb512
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422310"
---
# <a name="what-is-azure-application-gateway"></a>Mi az Azure Application Gateway?

Az Azure Application Gateway egy webes forgalomra vonatkozó terheléselosztó, amellyel kezelheti a webalkalmazásai forgalmát. A hagyományos terheléselosztók az átviteli rétegen működnek (OSI 4. réteg – TCP és UDP), és a forrás IP-címe és portja alapján irányítják a forgalmat a célállomás IP-címére és portjára.

![Application Gateway conceptual](media/overview/figure1-720.png)

With Application Gateway, you can make routing decisions based on additional attributes of an HTTP request, such as URI path or host headers. A forgalmat például a bejövő URL-cím alapján irányíthatja. Tehát ha a bejövő URL-cím a `/images` kifejezést tartalmazza, a forgalmat adott, képekre konfigurált kiszolgálókra irányíthatja (azaz egy készletbe). If `/video` is in the URL, that traffic is routed to another pool that's optimized for videos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Ezt a fajta útválasztást alkalmazásrétegbeli (OSI 7. réteg) terheléselosztásnak nevezzük. Az Azure Application Gateway URL-alapú és egyéb útválasztásra is képes.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. If you need high-performance, low-latency, Layer-4 load balancing, see [What is Azure Load Balancer?](../load-balancer/load-balancer-overview.md) If you're looking for global DNS load balancing, see [What is Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Your end-to-end scenarios may benefit from combining these solutions.
>
> For an Azure load-balancing options comparison, see [Overview of load-balancing options in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Az Azure Application Gateway a következő funkciókkal rendelkezik:

## <a name="secure-sockets-layer-ssltls-termination"></a>Secure Sockets Layer (SSL/TLS) termination

Application gateway supports SSL/TLS termination at the gateway, after which traffic typically flows unencrypted to the backend servers. Ez a funkció lehetővé teszi, hogy a webkiszolgálók megszabaduljanak a magas titkosítási és visszafejtési üzemeltetési költségektől. But sometimes unencrypted communication to the servers is not an acceptable option. This can be because of security requirements, compliance requirements, or the application may only accept a secure connection. For these applications, application gateway supports end to end SSL/TLS encryption.

## <a name="autoscaling"></a>Automatikus skálázás

Application Gateway or WAF deployments under Standard_v2 or WAF_v2 SKU support autoscaling and can scale up or down based on changing traffic load patterns. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. For more information about the Application Gateway Standard_v2 and WAF_v2  features, see [Autoscaling v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Zone redundancy

An Application Gateway or WAF deployments under  Standard_v2 or WAF_v2 SKU can span multiple Availability Zones, offering better fault resiliency and removing the need to provision separate Application Gateways in each zone.

## <a name="static-vip"></a>Static VIP

The application gateway VIP on Standard_v2 or WAF_v2 SKU supports static VIP type exclusively. This ensures that the VIP associated with application gateway doesn't change even over the lifetime of the Application Gateway.

## <a name="web-application-firewall"></a>Webalkalmazási tűzfal

Web application firewall (WAF) is a service that provides centralized protection of your web applications from common exploits and vulnerabilities. WAF is based on rules from the [OWASP (Open Web Application Security Project) core rule sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (WAF_v2 only), 3.0, and 2.2.9. 

A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának számos rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

For more information, see [What is Azure Web Application Firewall?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Bejövőforgalom-vezérlő az AKS-hez
Application Gateway Ingress Controller (AGIC) allows you to use Application Gateway as the ingress for an [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) cluster. 

The ingress controller runs as a pod within the AKS cluster and consumes [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) and converts them to an Application Gateway configuration which allows the gateway to load-balance traffic to the Kubernetes pods. The ingress controller only supports Application Gateway V2 SKU. 

For more information, see [Application Gateway Ingress Controller (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>URL-alapú útválasztás

Az URL-alapú útválasztás lehetővé teszi, hogy a kérésben szereplő URL-cím alapján irányítsa a forgalmat a háttér-kiszolgálókészlethez. Az egyik lehetőség az, hogy a különböző típusú tartalmakra vonatkozó kéréseket különböző készletekhez irányítja.

Például `http://contoso.com/video/*` iránti kérelmek VideoServerPoolba, míg a `http://contoso.com/images/*` felé irányuló kérelmek az ImageServerPoolba vannak továbbítva. Ha a kérés egyik elérésiút-kategóriába sem sorolható, a DefaultServerPool az alapértelmezett kiszolgáló.

For more information, see [URL-based routing with Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Több hely üzemeltetése

A többhelyes üzemeltetéssel egynél több webhelyet konfigurálhat ugyanazon az Application Gateway-példányon. This feature allows you to configure a more efficient topology for your deployments by adding up to 100 web sites to one Application Gateway, or 40 for WAF (for optimal performance). Mindegyik webhelyet a saját készletéhez lehet irányítani. Az Application Gateway például a `contoso.com` és a `fabrikam.com` forgalmát is kiszolgálhatja a ContosoServerPool és a FabrikamServerPool kiszolgálókészletekből.

A `http://contoso.com` iránti kérelmek a ContosoServerPoolba, míg a `http://fabrikam.com` felé irányuló kérelmek a FabrikamServerPoolba vannak továbbítva.

Hasonlóképpen, ugyanazon szülőtartomány két altartományát ugyanazon Application Gateway-telepítésről üzemeltetheti. Az altartományok használatának példái között lehet az egyetlen Application Gateway-telepítésen üzemeltetett `http://blog.contoso.com` és `http://app.contoso.com`.

For more information, see [multiple-site hosting with Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Átirányítás

Számos webalkalmazás esetében gyakori eset az automatikus HTTP–HTTPS átirányítás annak érdekében, hogy az alkalmazás és a felhasználói közötti kommunikáció titkosított útvonalon történjen.

In the past, you may have used techniques such as dedicated pool creation whose sole purpose is to redirect requests it receives on HTTP to HTTPS. Az Application Gateway támogatja a forgalom az Application Gateway alapján való átirányításának lehetőségét. Ez leegyszerűsíti az alkalmazáskonfigurációt, optimalizálja az erőforrás-használatot, és új átirányítási forgatókönyveket támogat, például a globális és útvonalalapú átirányítást. Application Gateway redirection support isn't limited to HTTP to HTTPS redirection alone. Ez egy általános átirányítási mechanizmus, így a szabályokkal bármilyen megadott portról és portra átirányíthat. A szolgáltatás a külső webhelyre való átirányítást is támogatja.

Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

- Globális átirányítás portok között a Gatewayen. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
- Útvonalalapú átirányítás. Ez a fajta átirányítás csak a megadott webhelyrészen engedélyezi a HTTP–HTTPS átirányítást, például egy `/cart/*` kifejezéssel jelzett bevásárlókosár részen.
- Átirányítás külső helyre.

For more information, see [redirecting traffic](https://docs.microsoft.com/azure/application-gateway/redirect-overview) with Application Gateway.

## <a name="session-affinity"></a>Munkamenet-affinitás

A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott kiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

## <a name="websocket-and-http2-traffic"></a>Websocket- és HTTP/2-forgalom

Az Application Gateway natív támogatást nyújt a Websocket- és HTTP/2-protokollok számára. Kizárólag WebSocket-támogatásra vonatkozó felhasználói beállítás nem létezik.

A WebSocket és a HTTP/2 protokollok teljes körű duplex kommunikációt tesznek lehetővé egy kiszolgáló és egy ügyfél között egy hosszú ideig futó TCP-kapcsolaton. Ez interaktívabb kommunikációt eredményez a webkiszolgáló és az ügyél között, amely anélkül marad kétirányú, hogy a HTTP-alapú implementációkban kötelező lekérdezésekre lenne szükség. These protocols have low overhead, unlike HTTP, and can reuse the same TCP connection for multiple request/responses resulting in a more efficient resource utilization . Ezek a protokollok a hagyományos, 80-as és 443-as HTTP-portokon működnek.

For more information, see [WebSocket support](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) and [HTTP/2 support](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="connection-draining"></a>Kapcsolatkiürítés

A kapcsolatkiürítéssel zökkenőmentesen végrehajtható a háttérkészlettagok eltávolítása a tervezett szolgáltatásfrissítések során. E beállítás engedélyezése háttérbeli HTTP-beállítással történik, és a szabálylétrehozás keretében az adott háttérkészlet összes tagjára alkalmazható. Once enabled, Application Gateway ensures all de-registering instances of a backend pool do not receive any new request while allowing existing requests to complete within a configured time limit. This applies to both backend instances that are explicitly removed from the backend pool by an API call, and backend instances that are reported as unhealthy as determined by the health probes.

For more information, see the Connection Draining section of [Application Gateway Configuration Overview](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining).

## <a name="custom-error-pages"></a>Egyéni hibalapok

Az Application Gatewayjel testreszabhatók a hibaoldalak. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.

For more information, see [Custom Errors](custom-error.md).

## <a name="rewrite-http-headers"></a>HTTP-fejlécek átírása

HTTP headers allow the client and server to pass additional information with the request or the response. Rewriting these HTTP headers helps you accomplish several important scenarios, such as:

- Adding security-related header fields like HSTS/ X-XSS-Protection.
- Removing response header fields that can reveal sensitive information.
- Stripping port information from X-Forwarded-For headers.

Application Gateway supports the capability to add, remove, or update HTTP request and response headers, while the request and response packets move between the client and back-end pools. It also provides you with the capability to add conditions to ensure the specified headers are rewritten only when certain conditions are met.

For more information, see [Rewrite HTTP headers](rewrite-http-headers.md).

## <a name="sizing"></a>Méretezés

Application Gateway Standard_v2 and WAF_v2 SKU can be configured for autoscaling or fixed size deployments. These SKUs don't offer different instance sizes. For more information on v2 performance and pricing, see [Autoscaling v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing).

The Application Gateway Standard and WAF SKU is currently offered in three sizes: **Small**, **Medium**, and **Large**. A Kicsi méret ideális fejlesztési és tesztelési célokra.

Az Application Gateway korlátainak teljes listáját lásd: [Az Application Gateway szolgáltatási korlátozásai](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

The following table shows an average performance throughput for each application gateway v1 instance with SSL offload enabled:

| Average back-end page response size | Kicsi | Közepes | Nagy |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mb/s |
| 100 KB |35 Mbps |100 Mb/s |200 Mb/s |

> [!NOTE]
> Ezek az értékek az alkalmazásátjáró hozzávetőleges átviteli sebességét jelzik. A tényleges átvitel számos környezeti tényezőtől függ, például az átlagos lapmérettől, a háttérpéldányok helyétől és a lapkiszolgálás feldolgozási időtartamától. A pontos teljesítményszámokhoz saját teszteket kell futtatnia. Ezek az értékek csupán útmutatóul szolgálnak a kapacitástervezéshez.

## <a name="next-steps"></a>Következő lépések

Az igényeitől és a környezetétől függően az Azure Portallal, az Azure PowerShellel vagy az Azure CLI-vel hozhat létre egy Application Gateway-tesztet:

- [Quickstart: Direct web traffic with Azure Application Gateway - Azure portal](quick-create-portal.md)
- [Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure PowerShell](quick-create-powershell.md)
- [Első lépések – A webes forgalom irányítása az Azure Application Gateway szolgáltatással – Azure CLI](quick-create-cli.md)
