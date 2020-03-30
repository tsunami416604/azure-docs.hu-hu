---
title: Azure bejárati ajtó – gyakori kérdések
description: Ez az oldal választ ad az Azure Bejárati ajtóval kapcsolatos gyakori kérdésekre
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
ms.openlocfilehash: 1cfee9749bf2eb30799efb05ac875843bcde6651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372622"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Gyakori kérdések az Azure bejárati ajtajához

Ez a cikk az Azure Bejárati ajtajával kapcsolatos funkciókkal és funkciókkal kapcsolatos gyakori kérdésekre ad választ. Ha nem látja a választ a kérdésére, felveheti velünk a kapcsolatot a következő csatornákon keresztül (növekvő sorrendben):

1. A hozzászólások részben ezt a cikket.
2. [Az Azure Bejárati ajtajának UserVoice .](https://feedback.azure.com/forums/217313-networking?category_id=345025)
3. **Microsoft-támogatás:** Új támogatási kérelem létrehozásához az Azure Portal **súgólapján** kattintson a **Súgó + támogatás** gombra, majd az Új támogatási **kérelem**elemre.

## <a name="general"></a>Általános kérdések

### <a name="what-is-azure-front-door"></a>Mi az Azure Front Door?

Az Azure Front Door egy alkalmazáskézbesítési hálózat (ADN) szolgáltatásként, amely különböző 7-es réteges terheléselosztási lehetőségeket kínál az alkalmazások számára. Dinamikus helygyorsítást (DSA) biztosít a globális terheléselosztásmellett közel valós idejű feladatátvételsel. Ez egy magas rendelkezésre állású és skálázható szolgáltatás, amely teljes mértékben az Azure által felügyelt.

### <a name="what-features-does-azure-front-door-support"></a>Milyen funkciókat támogat az Azure Front Door?

Az Azure Front Door támogatja a dinamikus helygyorsítást (DSA), az SSL-kiszervezést és a végpontok közötti SSL-t, a webalkalmazás-tűzfalat, a cookie-alapú munkamenet-affinitást, az URL-elérési út-alapú útválasztást, az ingyenes tanúsítványokat és a többtartományos kezelést és másokat. A támogatott funkciók teljes listáját az [Azure Bejárati ajtajának áttekintése című témakörben találja.](front-door-overview.md)

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Mi a különbség az Azure Front Door és az Azure Application Gateway között?

Bár mind a Bejárati ajtó, mind az Application Gateway a 7- es réteg (HTTP/HTTPS) terheléselosztók, az elsődleges különbség az, hogy a Bejárati ajtó egy globális szolgáltatás, míg az Application Gateway egy regionális szolgáltatás. Míg a bejárati ajtó képes a különböző méretezési egységek/fürtök/bélyegző egységek közötti különböző régiók közötti terhelési egyensúlyt, az Application Gateway lehetővé teszi a virtuális gépek/tárolók közötti terhelési egyensúlyt, amely a méretezési egységen belül van.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Mikor kell telepíteni egy alkalmazásátjáró mögött bejárati ajtó?

A legfontosabb forgatókönyvek, hogy miért kell használni application gateway mögött bejárati ajtó a következők:

- Bejárati ajtó csak globális szinten hajthat végre elérési útalapú terheléselosztást, de ha a virtuális hálózaton (VNET) belül még jobban szeretné eltölteni a forgalmat, akkor az Application Gateway-t kell használnia.
- Mivel a bejárati ajtó nem működik a virtuális gép/tároló szintjén, így nem tud kapcsolat kiürítése. Azonban az Application Gateway lehetővé teszi, hogy kapcsolat kiürítése. 
- Az AFD mögötti application gateway segítségével 100%-os SSL-kiszervezésérhető el, és csak HTTP-kérelmeket irányíthat a virtuális hálózaton (VNET) belül.
- Bejárati ajtó és az Application Gateway egyaránt támogatja a munkamenet-affinitást. Míg a Bejárati ajtó egy felhasználói munkamenetből származó további forgalmat irányíthat egy adott régióban ugyanarra a fürtre vagy háttérrendszerre, az Application Gateway a forgalmat a fürtön belül ugyanarra a kiszolgálóra irányíthatja.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Üzembe helyezhetjük az Azure Load Balancer-t a bejárati ajtó mögött?

Az Azure Front Door-nak nyilvános VIP-re vagy nyilvánosan elérhető DNS-névre van szüksége a forgalom irányításához. Az Azure Load Balancer üzembe helyezése a bejárati ajtó mögött gyakori használati eset.

### <a name="what-protocols-does-azure-front-door-support"></a>Milyen protokollokat támogat az Azure Bejárati ajtó?

Az Azure Front Door támogatja a HTTP, HTTPS és HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Hogyan támogatja az Azure Bejárati ajtó a HTTP/2-t?

A HTTP/2 protokoll támogatása csak az Azure Bejárati ajtóhoz csatlakozó ügyfelek számára érhető el. A háttérrendszerekhez a háttérkészletben lévő kommunikáció http/1.1 felett van. A HTTP/2 támogatása alapértelmezés szerint engedélyezve van.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Milyen erőforrások at támogatott ma a háttérkészlet részeként?

Háttérkészlet-készletek állhat storage, webapp, Kubernetes-példányok, vagy bármely más egyéni állomásnév, amely nyilvános kapcsolattal rendelkezik. Az Azure Bejárati ajtajának megköveteli, hogy a háttérrendszerek nyilvános IP-cím vagy nyilvánosan feloldható DNS-állomásnév segítségével vannak definiálva. A háttérkészletek tagjai zónákon, régiókon vagy akár az Azure-on kívül is lehetnek, feltéve, hogy nyilvános kapcsolattal rendelkeznek.

### <a name="what-regions-is-the-service-available-in"></a>Milyen régiókban érhető el a szolgáltatás?

Az Azure Front Door egy globális szolgáltatás, és nem kötődik egyetlen adott Azure-régióhoz sem. A bejárati ajtó létrehozásakor csak az erőforráscsoport helyét kell megadnia, amely alapvetően megadja, hogy az erőforráscsoport metaadatait hol tárolja a rendszer. A bejárati ajtó erőforrás maga globális erőforrásként jön létre, és a konfiguráció globálisan telepítve van az összes POP-ban (Jelenléti pont). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Melyek az Azure Bejárati ajtajának POP-helyei?

Azure Front Door has the same list of POP (Point of Presence) locations as Azure CDN from Microsoft. A POP-ok teljes listáját a [Microsoft Azure CDN POP-helyeicímű dokumentumban tájékán tájékadják](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)meg.

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Az Azure Front Door dedikált központi telepítés az alkalmazásomhoz, vagy az ügyfelek között van megosztva?

Az Azure Front Door egy globálisan elosztott több-bérlős szolgáltatás. Így a Bejárati ajtó infrastruktúrája megoszlik az összes ügyfele között. Azonban egy bejárati ajtó profil létrehozásával megadhatja az alkalmazáshoz szükséges konfigurációt, és a bejárati ajtaján végrehajtott módosítások nem befolyásolják a bejárati ajtó más konfigurációit.

### <a name="is-http-https-redirection-supported"></a>A HTTP->HTTPS átirányítása támogatott?

Igen. Valójában az Azure Front Door támogatja a gazdagép, az elérési út és a lekérdezési karakterlánc átirányítása, valamint az URL-átirányítás egy részét. További információ az [URL-átirányításról.](front-door-url-redirect.md) 

### <a name="in-what-order-are-routing-rules-processed"></a>Milyen sorrendben dolgozzák fel az útválasztási szabályokat?

A bejárati ajtó útvonalai nincsenek megrendezve, és a legjobb egyezés alapján egy adott útvonal at választanak ki. További információ [arról, hogy a Bejárati ajtó hogyan egyezteti az útválasztási szabályokra vonatkozó kérelmeket.](front-door-route-matching.md)

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Hogyan zárolhatom a hozzáférést a háttérprogramomhoz csak az Azure bejárati ajtajához?

Az alkalmazás zárolásához csak az adott bejárati ajtóról érkező forgalom fogadásához be kell állítania az IP-axi-címeket a háttérrendszerhez, majd korlátoznia kell a háttérrendszer forgalmat a Bejárati ajtó által küldött "X-Azure-FDID" fejléc konkrét értékére. Ezeket a lépéseket az alábbiak szerint részletezzük:

- Konfigurálja az IP-acling-et a háttérrendszerekszámára, hogy fogadja az Azure Front Door háttér-IP-címteréből és csak az Azure infrastruktúra-szolgáltatásaiból érkező forgalmat. Olvassa el az alábbi IP-adatokat a háttérprogram acling:
 
    - Tekintse az *AzureFrontDoor.Backend* szakaszt az [Azure IP-tartományokban és a szolgáltatáscímkéket](https://www.microsoft.com/download/details.aspx?id=56519) a Bejárati ajtó IPv4-háttérIP-címtartományához, vagy használhatja az *AzureFrontDoor.Backend* szolgáltatáscímkét a [hálózati biztonsági csoportokban](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) vagy az [Azure Firewall szolgáltatáscímkékkel.](https://docs.microsoft.com/azure/firewall/service-tags)
    - A Bejárati ajtó IPv6-háttér-IP-területe a szolgáltatáscímkében lefedett helyen nem szerepel az Azure IP-tartományok JSON-fájljában. **IPv6** Ha explicit IPv6-címtartományt keres, az jelenleg`2a01:111:2050::/44`
    - Az Azure [alapvető infrastrukturális szolgáltatásai](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) virtualizált `168.63.129.16` gazdaIP-címeken keresztül: és`169.254.169.254`

    > [!WARNING]
    > A Front Door háttér-IP-területe később változhat, azonban biztosítani fogjuk, hogy mielőtt ez megtörténne, integrálódtunk volna [az Azure IP-tartományokba és a szolgáltatáscímkékbe.](https://www.microsoft.com/download/details.aspx?id=56519) Azt javasoljuk, hogy a módosítások és frissítések az [Azure IP-tartományok és a szolgáltatáscímkék](https://www.microsoft.com/download/details.aspx?id=56519) előfizetni.

-    Get művelet végrehajtása a bejárati ajtón `2020-01-01` az API-verzióval vagy újabb verzióval. Az API-hívás, `frontdoorID` keresse meg a mezőben. Szűrje a bejárati ajtó által a háttérrendszerre küldött bejövő fejlécet , az **"X-Azure-FDID"** fejlécet a mező `frontdoorID`értékével. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Lehet a anycast IP változás élettartama alatt az én Bejárati ajtó?

A bejárati ajtó előtétes IP-címe általában nem változik, és statikus maradhat a bejárati ajtó élettartama alatt. Erre azonban **nincs garancia.** Kegyeletem, ne vegyen fel semmilyen közvetlen függőséget a vi.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Támogatja az Azure Bejárati ajtó a statikus vagy dedikált IP-ket?

Nem, az Azure Bejárati ajtó jelenleg nem támogatja a statikus vagy dedikált előtér-alapú bármelycast IP-k. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Támogatja az Azure Front Door az x-forwarded-for fejléceket?

Igen, az Azure Front Door támogatja az X-Forwarded-For, X-Forwarded-Host és X-Forwarded-Proto fejlécek. X-Forwarded-For ha a fejléc már jelen volt, majd a Bejárati ajtó hozzáfűzi az ügyfél szoftvercsatorna IP-címéhez. Különben hozzáadja a fejlécet az ügyfélszoftverzet IP-címével értékként. X-Forwarded-Host és X-Forwarded-Proto esetén az érték felülbírálva van.

További információ a [Bejárati ajtó által támogatott HTTP-fejlécekről.](front-door-http-headers-protocol.md)  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Mennyi ideig tart egy Azure bejárati ajtajának üzembe helyezése? A bejárati ajtó ma is működik frissítés kor?

Egy új bejárati ajtó létrehozása, vagy egy meglévő bejárati ajtó frissítései körülbelül 3-5 percet vesz igénybe a globális telepítéshez. Ez azt jelenti, hogy körülbelül 3-5 perc múlva a Bejárati ajtó konfigurációja világszerte az összes POP-unkban lesz telepítve.

Megjegyzés – Az egyéni SSL-tanúsítványfrissítések globális üzembe helyezése körülbelül 30 percet vesz igénybe.

Az útvonalak vagy háttérkészletek stb. A tanúsítványfrissítések szintén atomi atomi atomi atomi atomi atomfrissítések, és nem okoznak kimaradást, kivéve, ha az "AFD Managed" -ről a "Saját tanúsítvány használatára" váltanak, vagy fordítva.


## <a name="configuration"></a>Konfiguráció

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Az Azure Front Door terheléselosztása vagy a virtuális hálózaton belüli forgalom irányítása?

Az Azure Front Door (AFD) nyilvános IP-címet vagy nyilvánosan feloldható DNS-nevet igényel a forgalom irányításához. Így a válasz nem AFD közvetlenül nem irányíthatja a virtuális hálózaton belül, de egy alkalmazásátjáró vagy az Azure load balancer használata között megoldja ezt a forgatókönyvet.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Melyek az Azure Bejárati ajtajának különböző időtúl- és korlátok?

Ismerje meg az Azure Front Door összes dokumentált [időtúl- és korlátját.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)

## <a name="performance"></a>Teljesítmény

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Hogyan támogatja az Azure Front Door a magas rendelkezésre állást és a méretezhetőséget?

Az Azure Front Door egy globálisan elosztott több-bérlős platform, amely hatalmas kapacitással rendelkezik, hogy kielégítse az alkalmazás méretezhetőségi igényeit. A Microsoft globális hálózatának pereméről érkező Front Door globális terheléselosztási képességet biztosít, amely lehetővé teszi, hogy a teljes alkalmazáson vagy akár az egyes mikroszolgáltatásokon keresztül is áttekintsék a régiókat vagy a különböző felhőket.

## <a name="ssl-configuration"></a>SSL-konfiguráció

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Milyen TLS-verziókat támogat az Azure Front Door?

A 2019 szeptembere után létrehozott minden bejárati ajtó profil a TLS 1.2-t használja alapértelmezett minimumként.

A Front Door támogatja a TLS 1.0, 1.1 és 1.2 verzióit. A TLS 1.3 még nem támogatott.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Milyen tanúsítványok támogatottak az Azure Bejárati ajtajában?

Ahhoz, hogy a HTTPS protokoll biztonságosan kézbesítse a tartalmat egy bejárati ajtó egyéni tartományban, választhat, hogy az Azure Front Door által kezelt tanúsítványt használ, vagy saját tanúsítványt használ.
A Bejárati ajtó kezelt opció egy szabványos SSL tanúsítványt tartalmaz a Digicert-en keresztül, és a Front Door key vaultjában tárolja. Ha úgy dönt, hogy saját tanúsítványt használ, akkor egy támogatott hitelesítésszolgáltatótól származó tanúsítványt is bevihet, és lehet szabványos SSL, kiterjesztett érvényesítési tanúsítvány vagy akár helyettesítő tanúsítvány is. Az önaláírt tanúsítványok nem támogatottak. További információ [arról, hogyan engedélyezheti a HTTPS protokollt egyéni tartományban.](https://aka.ms/FrontDoorCustomDomainHTTPS)

### <a name="does-front-door-support-autorotation-of-certificates"></a>Támogatja a Bejárati ajtó a tanúsítványok automatikus rotációját?

A Bejárati ajtó által felügyelt tanúsítvány beállítás esetén a tanúsítványokat a bejárati ajtó automatikusan elforgatja. Ha a Bejárati ajtó által kezelt tanúsítványt használja, és látja, hogy a tanúsítvány lejárati dátuma kevesebb, mint 60 nap múlva van, nyújtson be egy támogatási jegyet.
</br>A saját egyéni SSL-tanúsítvány, autorotation nem támogatott. Hasonlóan ahhoz, ahogyan egy adott egyéni tartományhoz első alkalommal állították be, a Bejárati ajtót a Key Vault megfelelő tanúsítványverziójára kell mutatnia, és biztosítania kell, hogy a Bejárati ajtó egyszerű szolgáltatása továbbra is hozzáférhessen a Key Vaulthoz. Ez a frissített tanúsítvány-bevezetési művelet a Bejárati ajtó által atomi, és nem okoz semmilyen termelési hatást, feltéve, hogy a tulajdonos neve vagy SAN a tanúsítvány nem változik.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Melyek az Azure Front Door által támogatott jelenlegi titkosítási csomagok?

Az Azure Front Door által támogatott jelenlegi titkosítási csomagok a következők:

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

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Konfigurálhatom az SSL-házirendet az SSL protokoll verzióinak vezérlésére?

Az Azure Front Door ban konfigurálhat egy minimális TLS-verziót az egyéni tartomány HTTPS-beállításaiban az Azure Portalon vagy az [Azure REST API-n](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)keresztül. Jelenleg 1.0 és 1.2 között választhat.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Konfigurálhatom úgy a Bejárati ajtót, hogy csak bizonyos rejtjelcsomagokat támogasson?

Nem, a Bejárati ajtó konfigurálása adott rejtjelező csomagokhoz nem támogatott. A hitelesítésszolgáltatótól (például Verisign, Entrust vagy Digicert) azonban beszerezheti saját egyéni SSL-tanúsítványát, és a létrehozáskor a tanúsítványon külön rejtjel-csomagokat is megjelölhet. 

### <a name="does-front-door-support-ocsp-stapling"></a>Támogatja a Bejárati ajtó az OCSP tűzést?

Igen, az OCSP tűzést alapértelmezés szerint a Bejárati ajtó támogatja, és nincs szükség konfigurációra.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Az Azure Front Door is támogatja a háttérrendszer forgalmának újratitkosítását?

Igen, az Azure Front Door támogatja az SSL-kiszervezést, és a végpontok végéig ssl, amely újra titkosítja a forgalmat a háttérszolgáltatás. Valójában, mivel a háttérrendszerhez való csatlakozások nyilvános IP-címén keresztül történnek, ajánlott konfigurálni a bejárati ajtót, hogy https-t használjon továbbítási protokollként.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Támogatja a Bejárati ajtó az önaláírt tanúsítványokat a HTTPS-kapcsolat háttérrendszerén?

Nem, az önaláírt tanúsítványok nem támogatottak a Bejárati ajtóban, és a korlátozás mindkettőre vonatkozik:

1. **Háttérrendszerek:** Nem használhatja az önaláírt tanúsítványokat, ha a forgalmat HTTPS vagy HTTPS állapotmintaként továbbítja, vagy a gyorsítótárat az eredetitől kezdve az útválasztási szabályok hoz a gyorsítótár az engedélyezve van.
2. **Előtér:** Nem használhat önaláírt tanúsítványokat, ha saját egyéni SSL-tanúsítványt használ a HTTPS engedélyezéséhez az egyéni tartományban.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Miért a HTTPS-forgalom a háttér-háttér-tartalékom nem sikerül?

A sikeres HTTPS-kapcsolatok a háttérrendszer, hogy az állapot-mintavételek vagy a kérelmek továbbítása, két oka lehet a HTTPS-forgalom sikertelen lehet:

1. **A tanúsítvány tulajdonosának neve nem egyezik:** Https-kapcsolatok esetén a Bejárati ajtó azt várja, hogy a háttérrendszer egy érvényes hitelesítésszolgáltató tanúsítványát jelenítse meg, amelynek tulajdonosneve megegyezik a háttérrendszer állomásnevével. Ha például a háttérrendszer állomásneve `myapp-centralus.contosonews.net` van beállítva, és a tanúsítvány, amelyet a `myapp-centralus.contosonews.net` háttérrendszer az SSL-kézfogás során nem rendelkezik, sem `*myapp-centralus*.contosonews.net` a tulajdonos nevében, a bejárati ajtó elutasítja a kapcsolatot, és hibát okoz. 
    1. **Megoldás:** Bár megfelelőségi szempontból nem ajánlott, a hibát úgy oldhatja meg, ha letiltja a tanúsítvány tulajdonosának névellenőrzését az elülső ajtóhoz. Ez az Azure Portal beállításai és az API BackendPoolsSettings alatt található.
2. **Háttérszolgáltatás-üzemeltetési tanúsítvány érvénytelen hitelesítésszolgáltatótól:** Csak [érvényes hitelesítésszolgáltatók tanúsítványai](/azure/frontdoor/front-door-troubleshoot-allowed-ca) használhatók a háttérben a Bejárati ajtóval. A belső hitelesítésszolgáltatótól vagy az önaláírt tanúsítványoktól származó tanúsítványok nem engedélyezettek.

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Milyen típusú metrikák és naplók érhetők el az Azure Bejárati ajtajával?

A naplókról és más diagnosztikai képességekről a [Bejárati ajtó metrikáinak és naplóinak figyelése című témakörben talál.](front-door-diagnostics.md)

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Mi az adatmegőrzési szabály a diagnosztikai naplók?

Diagnosztikai naplók áramlását az ügyfelek tárfiók és az ügyfelek beállíthatják az adatmegőrzési házirend alapján a preferencia. Diagnosztikai naplók is elküldhető egy Event Hub vagy az Azure Monitor naplók. További információ: [Azure Front Door Diagnostics](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Hogyan szerezhetem be az Azure Front Door naplózási naplóit?

Naplónaplók érhetők el az Azure bejárati ajtajához. A portálon kattintson a napló panelmenüjének **Tevékenységnapló** parancsára. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Beállíthatok riasztásokat az Azure Bejárati ajtajával?

Igen, az Azure Bejárati ajtaján támogatja a riasztásokat. Riasztások vannak konfigurálva a metrikák. 

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
