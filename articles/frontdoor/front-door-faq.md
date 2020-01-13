---
title: Azure bejárati ajtó szolgáltatás – gyakori kérdések
description: Ez a lap az Azure bejárati szolgáltatásával kapcsolatos gyakori kérdésekre ad választ.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: dd315277e6e8f29a103760d605a7da4603190c20
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75908868"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Gyakori kérdések az Azure-előtérben szolgáltatásról

Ez a cikk az Azure bejárati szolgáltatás funkcióit és funkcióit érintő gyakori kérdésekre ad választ. Ha nem látja a választ a kérdésére, felveheti velünk a kapcsolatot a következő csatornákon keresztül (növekvő sorrendben):

1. A cikk megjegyzések szakasza.
2. [Azure bejárati ajtó szolgáltatás UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft ügyfélszolgálata:** Új támogatási kérelem létrehozásához a Azure Portal **Súgó** lapján kattintson a **Súgó + támogatás** gombra, majd válassza az **új támogatási kérelem**lehetőséget.

## <a name="general"></a>Általános

### <a name="what-is-azure-front-door-service"></a>Mi az az Azure Front Door Service?

Az Azure bejárati ajtó szolgáltatás egy alkalmazásszolgáltatási hálózat (ADN) szolgáltatás, amely különböző 7. rétegbeli terheléselosztási funkciókat kínál az alkalmazásai számára. A dinamikus hely gyorsítását (DSA) és a globális terheléselosztást is biztosítja a közel valós idejű feladatátvételsel együtt. Ez egy kiválóan elérhető és méretezhető szolgáltatás, amelyet az Azure teljes körűen kezel.

### <a name="what-features-does-azure-front-door-service-support"></a>Milyen funkciókkal támogatja az Azure bejárati ajtó szolgáltatást?

Az Azure előtér-szolgáltatás támogatja a dinamikus hely gyorsítását (DSA), az SSL-kiszervezést és a végpontok közötti SSL-t, a webalkalmazási tűzfalat, a cookie-alapú munkamenet-affinitást, az URL-elérésiút-alapú útválasztást, az ingyenes tanúsítványokat és több tartományi felügyeletet A támogatott szolgáltatások teljes listájáért lásd: [Az Azure bejárati szolgáltatásának áttekintése](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Mi a különbség az Azure bejárati ajtó szolgáltatás és az Azure Application Gateway között?

Míg a bejárati ajtó és a Application Gateway a 7. rétegbeli (HTTP/HTTPS) terheléselosztó, az elsődleges különbség az, hogy a bejárati ajtó globális szolgáltatás, míg a Application Gateway regionális szolgáltatás. Míg a bejárati ajtó terheléselosztást végez a különböző méretezési egységek/fürtök/Stamp egységek között a régiók között, Application Gateway lehetővé teszi a terheléselosztást a virtuális gépek/tárolók, illetve a skálázási egységen belül.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Mikor érdemes üzembe helyezni egy Application Gateway a bejárati ajtó mögött?

A legfontosabb forgatókönyvek, hogy miért érdemes Application Gateway a bejárati ajtó mögött:

- A bevezető ajtó csak globális szinten hajthat végre elérésiút-alapú terheléselosztást, de ha a virtuális hálózatán (VNET) belül még tovább szeretné terheléselosztási forgalmat végezni, akkor a Application Gatewayt kell használniuk.
- Mivel a bejárati ajtó nem működik a virtuális gépek/tárolók szintjén, így nem tudja elvégezni a kapcsolatok kiürítését. Application Gateway azonban lehetővé teszi a kapcsolatok kiürítését. 
- A AFD mögötti Application Gateway az egyik elérheti a 100%-os SSL-kiszervezést és csak a virtuális hálózaton belüli HTTP-kérelmeket (VNET).
- A bejárati ajtó és a Application Gateway támogatja a munkamenet-affinitást is. Míg a bejárati ajtó egy adott régióban a felhasználói munkamenetből egy adott fürtre vagy háttérre irányíthatja a további forgalmat, Application Gateway a fürtön belüli ugyanazon kiszolgálóra irányíthatja a forgalmat a affinitize.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Üzembe helyezhető Azure Load Balancer a bejárati ajtó mögött?

Az Azure bejárati szolgáltatásának nyilvános VIP-nek vagy nyilvánosan elérhető DNS-névnek kell lennie ahhoz, hogy átirányítsa a forgalmat. A bejárati ajtó mögötti Azure Load Balancer üzembe helyezése gyakori használati eset.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Milyen protokollok támogatják az Azure bejárati ajtó szolgáltatást?

Az Azure bejárati ajtó szolgáltatás a HTTP, a HTTPS és a HTTP/2 használatát támogatja.

### <a name="how-does-azure-front-door-service-support-http2"></a>Hogyan támogatja az Azure bejárati ajtó szolgáltatás a HTTP/2-t?

A HTTP/2 protokoll támogatása csak az Azure bejárati ajtó szolgáltatáshoz csatlakozó ügyfelek számára érhető el. A háttérrendszer-készletben található háttérrendszer-kommunikáció HTTP/1.1-n keresztül történik. A HTTP/2 támogatás alapértelmezés szerint engedélyezve van.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Milyen erőforrások támogatottak ma a háttérrendszer-készlet részeként?

A háttér-készletek a következők lehetnek: Storage, Web App, Kubernetes instances vagy bármely más, nyilvános kapcsolattal rendelkező egyedi állomásnév. Az Azure bejárati szolgáltatása megköveteli, hogy a háttérrendszer egy nyilvános IP-címen vagy egy nyilvánosan feloldható DNS-állomásnévn keresztül legyen definiálva. A háttér-készletek tagjai többek között zónák, régiók vagy akár az Azure-on kívül is lehetnek, amíg nyilvános kapcsolattal rendelkeznek.

### <a name="what-regions-is-the-service-available-in"></a>Milyen régiókban érhető el a szolgáltatás?

Az Azure bejárati ajtó szolgáltatás globális szolgáltatás, és nem kötődik egyetlen adott Azure-régióhoz sem. A bejárati ajtó létrehozásakor csak az erőforráscsoport helyét kell megadnia, amely alapvetően meghatározza, hogy az erőforráscsoport metaadatainak hol lesznek tárolva. Az előtérben lévő erőforrás maga globális erőforrásként jön létre, és a konfiguráció globálisan települ az összes pop-ra (a jelenléti pontra). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Mik az Azure bejárati ajtó szolgáltatásának POP-helyei?

Az Azure-beli bejárati szolgáltatásban a POP (jelenléti pont) helyeinek listája Azure CDN a Microsofttól. A POPs teljes listájáért tekintse meg [Azure CDN pop-helyszíneket a Microsofttól](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Az Azure bejárati ajtó szolgáltatás egy dedikált üzembe helyezés az alkalmazáshoz, vagy megosztották az ügyfelek között?

Az Azure bejárati ajtó szolgáltatás egy globálisan elosztott, több-bérlős szolgáltatás. Így a bejárati ajtó infrastruktúrája az összes ügyfele között meg van osztva. A bejárati ajtó profiljának létrehozásával azonban megadhatja az alkalmazáshoz szükséges konkrét konfigurációt, és a bejárati ajtón végrehajtott módosítások nem érintik a többi bejárati ajtó konfigurációját.

### <a name="is-http-https-redirection-supported"></a>Támogatott-e a HTTP-> HTTPS-átirányítás?

Igen. Valójában az Azure bejárati szolgáltatás támogatja a gazdagép-, útvonal-és lekérdezési karakterláncok átirányítását, valamint az URL-átirányítás részét. További információ az [URL-átirányításról](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>Milyen sorrendben történik az útválasztási szabályok feldolgozása?

Az előtérben lévő útvonalak nincsenek megrendezve, és egy adott útvonal van kiválasztva a legjobb egyezés alapján. További információ arról [, hogyan illeszkedik a bejárati ajtó a kérelmekhez egy útválasztási szabályhoz](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hogyan lezárni a háttérbeli hozzáférést, hogy csak az Azure bejárati ajtót?

Ha úgy szeretné zárolni az alkalmazást, hogy csak a megadott bejárati ajtóról fogadja a forgalmat, be kell állítania az IP ACL-eket a háttérhez, majd az Azure-beli bejárati ajtó által küldött "X-Forwarded-host" fejléchez tartozó elfogadott értékek készletét. Ezeket a lépéseket az alábbiak szerint részletezjük:

- Konfigurálja az IP-hozzáférés a háttérrendszer számára, hogy fogadja a forgalmat az Azure bejárati ajtó háttérbeli IP-címe és az Azure infrastruktúra-szolgáltatásai között. Az [Azure IP-címtartományok és a szolgáltatás-címkék](https://www.microsoft.com/download/details.aspx?id=56519) integrálásával dolgozunk, de most az alábbi módon hivatkozhat az IP-tartományokra:
 
    - A bejárati ajtó **IPv4** -háttér IP-címe: `147.243.0.0/16`
    - Bejárati ajtó **IPv6** -háttér IP-címe: `2a01:111:2050::/44`
    - Az Azure [alapszintű infrastruktúra-szolgáltatásai](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) virtualizált gazdagép IP-címein keresztül: `168.63.129.16` és `169.254.169.254`

    > [!WARNING]
    > Előfordulhat, hogy a bejárati ajtó háttérbeli IP-címe később is változhat, ezért a megjelenő esetekben biztosítani fogjuk, hogy az [Azure IP-címtartományok és a szolgáltatási címkék](https://www.microsoft.com/download/details.aspx?id=56519)integrálva lennének. Javasoljuk, hogy az [Azure IP-címtartományok és a szolgáltatás-címkék](https://www.microsoft.com/download/details.aspx?id=56519) esetében minden módosítást és frissítést előfizessen.

-   Szűrje a bejárati ajtó által küldött "**X-Forwarded-Host**" fejléc értékeit. A fejléc csak a bejárati ajtó konfigurációjában meghatározott összes előtér-gazdagépet tartalmazhat. Valójában még pontosabban csak azok az állomásnevek szerepelnek, amelyekről el szeretné fogadni a forgalmat.
    - Példa – tegyük fel, hogy a bejárati ajtó konfigurációja a következő előtér-gazdagépekkel rendelkezik _`contoso.azurefd.net`_ (A), a _`www.contoso.com`_ (B), a _`api.contoso.com`_ (C) és a _`notifications.contoso.com`_ (D). Tegyük fel, hogy van két háttér X és Y. 
    - Az X háttérrendszer csak az A és B állomásnévből származó forgalmat veszi át. A háttér Y az A, C és D adatforgalomból is elvégezhető.
    - Tehát az X háttérrendszer esetében csak olyan forgalmat kell fogadnia, amelynél a "**X-Forwarded-Host**" fejléc van beállítva _`contoso.azurefd.net`_ vagy _`www.contoso.com`_ . Minden más esetében a háttér-X-nek el kell utasítania a forgalmat.
    - Hasonlóképpen, a háttérbeli Y-ben csak olyan forgalmat kell fogadnia, amelynél a "**X-Forwarded-Host**" fejléc van beállítva _`contoso.azurefd.net`_ , _`api.contoso.com`_ vagy _`notifications.contoso.com`_ . Minden más esetében a háttérbeli Y-nek el kell utasítania a forgalmat.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>A kiválasztó IP-cím változhat a bejárati ajtó élettartama során?

A bejárati ajtóhoz tartozó előtér-alhálózatok IP-címe általában nem változik, és a bejárati ajtó élettartama esetén statikus maradhat. Azonban nincsenek ilyen **garanciák** . Kérjük, ne vegyen fel közvetlen függőségeket az IP-címekre.

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Támogatja az Azure bejárati ajtó szolgáltatás a statikus vagy dedikált IP-címeket?

Nem, az Azure bejárati ajtó szolgáltatás jelenleg nem támogatja a statikus vagy dedikált előtér-IP-címek használatát. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Támogatja az Azure bejárati ajtó szolgáltatás az x-továbbítást a fejlécekhez?

Igen, az Azure bejárati szolgáltatás támogatja az X által továbbított, X által továbbított-gazdagép és az X által továbbított-proto fejléceket. X-továbbítás esetén – ha a fejléc már szerepel, akkor a bejárati ajtó hozzáfűzi az ügyfél szoftvercsatorna IP-címét. Máskülönben hozzáadja a fejlécet az ügyfél szoftvercsatorna IP-címéhez az értékként. Az X által továbbított-gazdagép és az X-továbbított-proto esetében az érték felül van bírálva.

További információ az [előtérben támogatott HTTP-fejlécekről](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Mennyi időt kell igénybe venni egy Azure bejárati ajtó üzembe helyezésére? A bejárati ajtóm továbbra is működik a frissítéskor?

Egy új bejárati ajtó létrehozása vagy egy meglévő bejárati ajtó frissítése körülbelül 3 – 5 percet vesz igénybe globális üzembe helyezés esetén. Ez azt jelenti, hogy körülbelül 3 – 5 percet vesz igénybe a bejárati ajtó konfigurálása globálisan.

Megjegyzés: az egyéni SSL-tanúsítványok frissítései nagyjából 30 percet vesznek igénybe a globális telepítéshez.

## <a name="configuration"></a>Konfiguráció

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Képes az Azure-beli bejárati terheléselosztásra vagy a virtuális hálózaton belüli forgalom irányítására?

Az Azure bejárati ajtajának (AFD) egy nyilvános IP-címet vagy nyilvánosan feloldható DNS-nevet igényel a forgalom irányításához. Így a válasz nem AFD közvetlenül egy virtuális hálózaton belül, de a két Application Gateway vagy Azure Load Balancer használatával megoldja ezt a forgatókönyvet.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Milyen időtúllépések és korlátok vonatkoznak az Azure bejárati ajtó szolgáltatására?

Ismerje meg az [Azure bejárati ajtó szolgáltatásának dokumentált időtúllépéseit és korlátait](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Teljesítmény

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Hogyan támogatja az Azure bejárati ajtó szolgáltatás a magas rendelkezésre állást és a méretezhetőséget?

Az Azure bejárati ajtó szolgáltatás egy globálisan elosztott, több-bérlős platform, amely nagy mennyiségű kapacitással gondoskodik az alkalmazás méretezhetőségi igényeiről. A Microsoft globális hálózatának szélétől kezdve a bejárati ajtó globális terheléselosztási képességet biztosít, amely lehetővé teszi, hogy a teljes alkalmazást, vagy akár az egyes régiókban vagy más felhőkben is egyedi szolgáltatást végezzen.

## <a name="ssl-configuration"></a>SSL-konfiguráció

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Milyen TLS-verziókat támogat az Azure bejárati ajtó szolgáltatása?

A 2019 szeptember után létrehozott összes bejárati profil a TLS 1,2-et használja alapértelmezett minimumként.

A bejárati ajtó támogatja a 1,0, 1,1 és 1,2 TLS-verziókat. A TLS 1,3 még nem támogatott.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Milyen tanúsítványokat támogat az Azure bejárati ajtó szolgáltatás?

Ha engedélyezni szeretné, hogy a HTTPS protokoll biztonságos módon kézbesítse a tartalmat egy bejárati ajtó egyéni tartományán, dönthet úgy, hogy az Azure bejárati szolgáltatás által felügyelt tanúsítványt használ, vagy saját tanúsítványt használ.
A bejárati ajtó által felügyelt beállítás a Digicert-on keresztül szabványos SSL-tanúsítványt, a bejárati ajtó Key Vaultjában tárolja. Ha úgy dönt, hogy saját tanúsítványt használ, akkor a tanúsítványokat egy támogatott HITELESÍTÉSSZOLGÁLTATÓTÓL is elvégezheti, és lehet szabványos SSL-, kiterjesztett ellenőrzési tanúsítvány vagy akár helyettesítő tanúsítvány is. Az önaláírt tanúsítványok nem támogatottak. Megtudhatja [, hogyan engedélyezheti a HTTPS-t egy egyéni tartományhoz](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Támogatja a bejárati ajtó a tanúsítványok autorotációját?

Az előtérben felügyelt tanúsítvány beállításnál a tanúsítványokat a rendszer automatikusan elforgatja a bejárati ajtón. Ha egy bejárati ajtót felügyelt tanúsítványt használ, és azt látja, hogy a tanúsítvány lejárati dátuma kevesebb, mint 60 nap múlva, a támogatási jegyet.
</br>Az egyéni SSL-tanúsítvány esetében az autorotáció nem támogatott. Az adott egyéni tartományhoz való első beállításhoz hasonlóan a Key Vault a megfelelő tanúsítvány-verzióra kell mutatnia, és biztosítania kell, hogy az első ajtóhoz tartozó szolgáltatásnév továbbra is hozzáférhessen a Key Vaulthoz. Ez a tanúsítvány bevezetési művelete a bejárati ajtónál atomi, és nem okoz semmilyen éles hatást, ha a tulajdonos neve vagy a SAN nem változik a tanúsítványban.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Mik az Azure bejárati ajtó szolgáltatás által támogatott jelenlegi titkosítási csomagok?

Az Azure bejárati ajtó szolgáltatás által támogatott jelenlegi titkosítási csomagok a következők:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Támogatja az Azure bejárati ajtó szolgáltatás a háttérbeli forgalom újratitkosítását is?

Igen, az Azure bejárati szolgáltatás támogatja az SSL-alapú kiszervezést és a végpontok közötti SSL-t, amely újra titkosítja a forgalmat a háttérbe. Valójában, mivel a háttérrel létesített kapcsolatok a nyilvános IP-címeken keresztül történnek, javasoljuk, hogy konfigurálja a bejárati ajtót a HTTPS használatára továbbítási protokollként.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Konfigurálható az SSL-szabályzat az SSL protokoll verzióinak szabályozására?

Az Azure- [REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)használatával beállíthatja az Azure-beli bejárati ajtó minimális TLS-verzióját. Jelenleg a 1,0 és a 1,2 közötti választásra van lehetőség.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Beállítható a bejárati ajtó, hogy csak bizonyos titkosítási csomagokat támogasson?

Nem, az adott titkosítási csomagok bejáratának konfigurálása nem támogatott. 

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Milyen típusú metrikák és naplók érhetők el az Azure bejárati ajtó szolgáltatásával?

A naplókról és az egyéb diagnosztikai képességekről további információt a [metrikák és naplók a bejárati ajtóhoz való figyelését](front-door-diagnostics.md)ismertető témakörben talál.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Mi az adatmegőrzési szabályzat a diagnosztikai naplókon?

A diagnosztikai naplók a Customers Storage-fiókba áramlanak, és az ügyfelek megadhatják az adatmegőrzési szabályzatot a preferencia alapján. A diagnosztikai naplókat egy Event hub-vagy Azure Monitor-naplóba is lehet elküldeni. További információ: [Azure bejárati ajtó szolgáltatásának diagnosztikája](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Hogyan naplók beszerzése az Azure bejárati ajtó szolgáltatáshoz?

A naplók elérhetők az Azure bejárati ajtó szolgáltatáshoz. A portálon kattintson a **tevékenység napló** elemre a bejárati ajtó menüjében a napló eléréséhez. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Beállíthat riasztásokat az Azure bejárati ajtó szolgáltatásával?

Igen, az Azure bejárati ajtó szolgáltatás támogatja a riasztásokat. A riasztások a metrikák használatára vannak konfigurálva. 

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
