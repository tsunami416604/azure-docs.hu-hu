---
title: Azure bejárati ajtajának szolgáltatás – gyakori kérdések az bejárati ajtajának |} A Microsoft Docs
description: Ez az oldal nyújt Azure bejárati ajtajának szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok
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
ms.openlocfilehash: 256435dfd016ebbd86dbbe49f4abbb346fb1cd19
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407748"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Az Azure bejárati ajtajának Service kapcsolatos gyakori kérdések

Ez a cikk gyakori kérdésekre ad választ Azure bejárati ajtajának Service szolgáltatásairól és funkcióiról. Ha nem látja a választ a kérdésére, lépjen kapcsolatba velünk (a eszkalálásáról rendelésben) a következő csatornákon keresztül:

1. Ez a cikk a megjegyzéseket.
2. [Az Azure bejárati ajtajának szolgáltatás UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **A Microsoft-támogatás:** Hozhat létre egy új támogatási kérelmet az Azure Portalon, az a **súgó** lapon jelölje be a **súgó + támogatás** gombra, és válassza ki **új támogatási kérelem**.

## <a name="general"></a>Általános kérdések

### <a name="what-is-azure-front-door-service"></a>Mi az az Azure Front Door Service?

Azure bejárati ajtajának Service szolgáltatás egy Tartalomkézbesítési hálózat (ADN) szolgáltatás, az alkalmazások különböző 7. rétegbeli terheléselosztási funkciókat kínál. Dinamikuswebhely-gyorsítás (DSA) együtt globális terheléselosztást a közel valós idejű feladatátvételt biztosít. Egy magas rendelkezésre állású és méretezhető szolgáltatás, amely az Azure teljes körűen felügyelt.

### <a name="what-features-does-azure-front-door-service-support"></a>Milyen funkciókat támogatja az Azure bejárati ajtajának Service?

Azure bejárati ajtajának szolgáltatás támogatja a dinamikuswebhely-gyorsítás (DSA), SSL-kiürítés és teljes körű SSL, a webalkalmazási tűzfal, cookie-alapú munkamenet-affinitást, URL-cím-alapú útválasztás, ingyenes tanúsítványok és a több tartomány felügyeleti és mások. Támogatott szolgáltatások teljes listáját lásd: [áttekintése az Azure bejárati ajtajának Service](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Mi a különbség az Azure bejárati ajtajának Service és az Azure Application Gateway között?

Bejárati ajtajának és az Application Gateway vannak réteg 7 (HTTP/HTTPS) terheléselosztók, amíg az elsődleges különbség az, hogy bejárati ajtajának-e egy globális szolgáltatást, mivel az Application Gateway egy regionális szolgáltatás. Bejárati ajtajának között a különböző méretezési egység/fürtök/blokk egységeket régióban is terheléselosztást, míg az Application Gateway lehetővé teszi, hogy a virtuális gépek és tárolók stb., amely a skálázási egységben lévő csoport közötti terheléselosztást.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Ha azt telepítse az Application Gateway mögött bejárati ajtajának?

Miért érdemes az egyik kell használni az Application Gateway mögött bejárati ajtajának azon kulcsfontosságú forgatókönyvek a következők:

- Bejárati ajtajának-alapú terheléselosztási csak a globális szinten, de az egyik szeretné-e forgalom terheléselosztása is a virtuális hálózaton (VNET) belüli betölteni, akkor kell használniuk, az Application Gateway hajthat végre.
- Mivel bejárati ajtajának nem működik a virtuális gép és tároló szintjén, így azt nem hajtható végre kapcsolat kiürítése. Azonban az Application Gateway lehetővé teszi, hogy a kapcsolat kiürítése. 
- Az Application Gateway mögött AFD egy SSL kiszervezheti és irányíthatja a saját virtuális hálózaton (VNET) belüli csak HTTP-kérések 100 %-át érheti el.
- Bejárati ajtajának és az Application Gateway egyaránt támogatja a munkamenet-affinitás. Bejárati ajtajának is forgalmát háttérkiszolgálóra irányítani a felhasználói munkamenet az azonos fürthöz, vagy a háttérrendszer egy adott régióban, amíg az Alkalmazásátjáró közvetlen beosztására is a forgalom ugyanarra a kiszolgálóra a fürtön belül.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Telepítheti a Microsoft Azure Load Balancer mögött bejárati ajtajának?

Azure bejárati ajtajának szolgáltatás kell továbbítani a forgalmat egy nyilvános virtuális IP-cím vagy egy nyilvánosan elérhető DNS-név. Üzembe helyezése az Azure Load Balancerhez bejárati ajtajának mögött, egy közös használati eset.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Milyen protokollokat támogatja az Azure bejárati ajtajának Service?

Az Azure bejárati ajtajának szolgáltatás támogatja a HTTP, HTTPS és HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Hogyan támogatja a HTTP/2 Azure bejárati ajtajának Service?

HTTP/2 protokoll támogatása csak Azure bejárati ajtajának szolgáltatáshoz csatlakozó ügyfelek számára érhető el. A kommunikáció a háttérkészlet háttérrendszereket HTTP/1.1 felett van. Alapértelmezés szerint engedélyezve van a HTTP/2-támogatás.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Milyen erőforrások jelenleg háttérkészlet részeként támogatottak?

Háttérkészletek tárolási, webalkalmazás, Kubernetes-példányok vagy bármely más egyéni állomásnév, amely rendelkezik a nyilvános kapcsolódási állhat. Azure bejárati ajtajának szolgáltatás szükséges a háttérrendszerek vagy nyilvános IP-cím vagy egy nyilvánosan feloldható DNS-állomásnév definiált. Háttérkészletek tagjai lehetnek zónákban, régiók, vagy az Azure-on is kívül, amíg a nyilvános kapcsolódási rendelkeznek.

### <a name="what-regions-is-the-service-available-in"></a>Mely régiókban érhető el a szolgáltatást?

Az Azure bejárati ajtajának Service globális szolgáltatás, és nem kötődnek konkrét Azure-régiókba. Meg kell adnia egy bejárati ajtajának létrehozásakor csak helye az erőforráscsoport helyét, amely alapvetően határozza meg a metaadatok az erőforráscsoport helyét. Bejárati ajtajának erőforrásán jön létre, globális erőforrás, és a konfigurációs van telepítve globálisan a POP (jelenléti pontok). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Mik azok a POP-helyek Azure bejárati ajtajának szolgáltatás?

Az Azure bejárati ajtajának Service ugyanezt a listát (jelenléti pontok) POP-helyek, a Microsoft Azure CDN rendelkezik. A teljes listát a POP könyvtárából tekintse [a Microsoft Azure CDN POP-helyek](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Az Azure bejárati ajtajának Service az alkalmazásomhoz tartozó dedikált üzemelő, megosztott vagy ügyfelek között?

Az Azure bejárati ajtajának szolgáltatás egy olyan globálisan elosztott több-bérlős szolgáltatás. Tehát bejárati ajtajának infrastruktúráját közösen használja az összes ügyfeleinek. Azonban a bejárati ajtajának létrehozásával meghatározhatja az alkalmazás számára szükséges konfigurációs és 

### <a name="is-http-https-redirection-supported"></a>Is HTTP->HTTPS redirection supported?

Bejárati ajtajának jelenleg nem támogatja a átirányítási URL-címe.

### <a name="in-what-order-are-routing-rules-processed"></a>Milyen sorrendben útválasztási szabályok feldolgozása?

A bejárati ajtajának útvonalai nem vannak rendezve, és a egy adott útvonal alapján választja ki a legmegfelelőbb. Tudjon meg többet [hogyan bejárati ajtajának megfelelő kérelem-útválasztási szabály](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>Hogyan tegye I zárolhatja a hozzáférést a háttérrendszer csak Azure bejárati ajtajának Service?

Csak Azure-szolgáltatásból bejárati ajtajának forgalom fogadására a a háttérrendszerek IP ACLing konfigurálhatja. Korlátozhatja az alkalmazás csak a háttérbeli IP-címtér Azure bejárati ajtajának szolgáltatás a bejövő kapcsolatokat fogadjon. Dolgozunk az felé integrálása [Azure IP-címtartományok és Szolgáltatáscímkék](https://www.microsoft.com/download/details.aspx?id=56519) , de most tekintse meg az alábbi IP-címtartományok esetében:
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> A háttérbeli IP-címtér később módosíthatja, azonban, hogy gondoskodik arról, hogy előtt történik, hogy lenne integráltuk az [Azure IP-címtartományok és Szolgáltatáscímkék](https://www.microsoft.com/download/details.aspx?id=56519). Azt javasoljuk, hogy az előfizetett [Azure IP-címtartományok és Szolgáltatáscímkék](https://www.microsoft.com/download/details.aspx?id=56519) a módosításaiért vagy frissítéseiért. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>A csomópontválasztásos IP-címet módosíthatja a bejárati ajtó élettartama során?

Az előtérbeli csomópontválasztásos IP számára a bejárati ajtajának általában nem kell módosítani, és előfordulhat, hogy kezdettől fogva élettartama statikus marad. Vannak azonban **nincs garancia** esetében azonos. Könyvtárából nem lépnek közvetlen függőségek az IP-címen.  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Támogatja az Azure bejárati ajtajának Service statikus vagy dedikált IP-címek?

Nem, az Azure bejárati ajtajának szolgáltatás jelenleg nem támogatja statikus vagy dedikált előtér nem egyedi IP-címek. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure bejárati ajtajának Service támogatja az x-továbbított – a fejlécek?

Igen, Azure bejárati ajtajának Service támogatja az X-továbbított – esetén, X továbbított gazdagép és az X továbbított Proto fejlécek. Ha X – továbbított-a a fejléc már jelen volt, majd a bejárati ajtajának az ügyfél a szoftvercsatorna IP hozzáfűzi azt. Más esetben hozzáadja az ügyfél a szoftvercsatorna IP-Címét a fejléc értékeként. X továbbított állomás és X továbbított protokoll esetében az érték felülbírálja.

Tudjon meg többet a [bejárati ajtajának támogatott HTTP-fejlécek](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Mennyi ideig tart egy Azure bejárati ajtajának szolgáltatás üzembe helyezéséhez? Saját bejárati ajtajának továbbra is működik, ha a frissítés alatt?

Egy új bejárati ajtajának létrehozása vagy egy meglévő bejárati ajtajának frissítéseit globális üzembe helyezési körülbelül 3 – 5 percig tart. Ez azt jelenti, körülbelül 3-5 perc alatt, a bejárati ajtajának konfigurációs globálisan üzembe helyezendő összes a POP között.

Megjegyzés – egyéni SSL-tanúsítvány frissítések nagyjából 30 percet igénybe globálisan üzembe helyezendő.

## <a name="configuration"></a>Konfiguráció

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Képes Azure bejárati ajtajának terheléselosztás és útvonal forgalom egy virtuális hálózaton belül?

Azure bejárati ajtajának (AFD) egy nyilvános IP-cím vagy a forgalom irányítására nyilvánosan feloldható DNS-név szükséges. Tehát van a válasz nem AFD közvetlenül nem hajthat végre útválasztást egy virtuális hálózaton belül, de ebben a forgatókönyvben egy Application Gateway vagy az Azure Load Balancer használatával a kettő között fogja megoldani.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Mik azok a különféle időtúllépések és Azure bejárati ajtajának Service korlátai?

További tudnivalók a dokumentált [időtúllépések és Azure bejárati ajtajának szolgáltatási korlátai](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Teljesítmény

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Hogyan támogatja a Azure bejárati ajtajának szolgáltatás a magas rendelkezésre állás és méretezhetőség?

Azure bejárati ajtajának szolgáltatás az a hatalmas mennyiségű kapacitást gondoskodjon arról, hogy az alkalmazás méretezhetőségi igényei szerint, globálisan elosztott több-bérlős platform. A Microsoft globális hálózat peremén az biztosítja, bejárati ajtajának globális, amely lehetővé teszi, hogy átadja a feladatokat a teljes alkalmazás vagy akár az egyes mikroszolgáltatások régiókban vagy különböző felhők közötti terheléselosztás képességet nyújt.

## <a name="ssl-configuration"></a>SSL-konfigurációja

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Milyen TLS-verziójú Azure bejárati ajtajának Service által támogatott?

Bejárati ajtajának támogatja a TLS 1.0, 1.1 és 1.2-es. A TLS 1.3 még nem támogatott.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Milyen tanúsítványok az Azure bejárati ajtajának Service támogatottak?

Ahhoz, hogy biztonságosan tartalomtovábbításhoz bejárati ajtajának egyéni tartomány HTTPS-protokollt, kiválaszthatja az Azure bejárati ajtajának szolgáltatás által kezelt tanúsítványt használjon, vagy a saját tanúsítványt használjon.
Kezdettől fogva beállítás rendelkezések egy szabványos SSL-tanúsítvány Digicert keresztül felügyelt, és tárolja az előtérben ajtó a Key Vault. Ha úgy dönt, hogy a saját tanúsítványt használ, akkor segítségével készítheti elő egy támogatott Hitelesítésszolgáltatótól származó tanúsítványt, és a egy szabványos SSL, a bővített ellenőrzés tanúsítvány vagy a helyettesítő tanúsítvány is lehet. Önaláírt tanúsítványok használata nem támogatott. Ismerje meg, [HTTPS engedélyezése egyéni tartomány](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Mik azok az aktuális Azure bejárati ajtajának szolgáltatás által támogatott titkosító csomagok?

Az aktuális Azure bejárati ajtajának szolgáltatás által támogatott titkosító csomagok a következők:

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

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure bejárati ajtajának Service is támogatja a forgalmat a háttérbeli újbóli titkosítása?

Igen, Azure bejárati ajtajának Service támogatja az SSL-alapú kiszervezéshez és teljes körű SSL-t, amely újra titkosítja a forgalmat a háttérbeli. Sőt mivel keresztül történjen a kapcsolatokat, a háttérkiszolgáló nyilvános IP-cím, javasoljuk, hogy konfigurálja-e a bejárati ajtajának továbbítási protokoll a HTTPS használatára.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Konfigurálhatja az SSL-szabályzat az SSL-protokollverziók vezérlésére?

Nem, jelenleg nem támogatja a bejárati ajtajának megtagadni konkrét TLS-verziók és nem állíthatja a TLS minimális verziója. 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Konfigurálhatja a bejárati ajtajának csak az adott titkosító csomagok támogatásához?

Nem, adott titkosító csomagok bejárati ajtajának konfigurálása nem támogatott. 

## <a name="diagnostics-and-logging"></a>Diagnosztika és naplózás

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Milyen típusú metrikák és naplók érhetők el Azure bejárati ajtajának szolgáltatással?

A naplók és egyéb diagnosztikai képességeket biztosít további információkért lásd: [metrikák és naplók monitoringjának bejárati ajtajának](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Mi a megőrzési házirend a diagnosztikai naplók?

Diagnosztikai naplók folyamat ügyfelek storage-fiókba, és ügyfelek állíthatja be a megtartási házirend alapján választaniuk. Diagnosztikai naplók is küldhetők Event Hub vagy az Azure Monitor-naplókat. További információkért lásd: [Azure bejárati ajtajának diagnosztika](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Hogyan kaphatok auditnaplók Azure bejárati ajtajának szolgáltatás?

Auditnaplók Azure bejárati ajtajának szolgáltatás érhetők el. Kattintson a portál **tevékenységnapló** menü paneljén látható a bejárati ajtó a napló eléréséhez. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Állítható riasztások Azure bejárati ajtajának szolgáltatással?

Igen, Azure bejárati ajtajának Service támogatják a riasztásokat. Riasztások a metrikák vannak konfigurálva. 

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.