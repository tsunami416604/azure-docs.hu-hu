---
title: Automatikusan skálázó és zónaredundáns Application Gateway v2
description: Ez a cikk bemutatja az Azure Application Standard_v2 és WAF_v2 termékváltozat, amely automatikus skálázás és zónaredundáns funkciókat tartalmaz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 4cd2969f9a56c96af2b2c6db216f6829a080260c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371276"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatikusan skálázó és zónaredundáns Application Gateway v2 

Az Application Gateway és a Web Application Firewall (WAF) Standard_v2 és WAF_v2 termékváltozatban is elérhető. A v2 termékváltozat teljesítménybeli fejlesztéseket kínál, és támogatja a kritikus fontosságú új funkciókat, például az automatikus skálázást, a zónaredundanciát és a statikus VIP-k támogatását. A Standard és a WAF termékváltozat meglévő szolgáltatásai továbbra is támogatottak az új v2 termékváltozatban, néhány kivétellel az [összehasonlító](#differences-with-v1-sku) szakaszban.

Az új v2 termékváltozat a következő fejlesztéseket tartalmazza:

- **Automatikus skálázás:** Az alkalmazásátjáró vagy a WAF-telepítések az automatikus skálázás termékváltozat alatt a forgalmi terhelési minták módosítása alapján fel- és leskálázása. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. Ez a termékváltozat valódi rugalmasságot kínál. A Standard_v2 és WAF_v2 termékváltozatban az Application Gateway rögzített kapacitással (automatikus skálázás letiltva) és automatikus skálázást engedélyezve módban is működhet. A rögzített kapacitás mód konzisztens és kiszámítható munkaterheléssel rendelkező forgatókönyvek esetén hasznos. Az automatikus skálázási mód előnyös azokban az alkalmazásokban, amelyek az alkalmazásforgalom varianciáját látják.
- **Zónaredundancia:** Az Application Gateway vagy a WAF központi telepítése több rendelkezésre állási zónára is kiterjedhet, így nincs szükség külön Application Gateway-példányok kiépítésére minden zónában egy Traffic Manager rel. Választhat egy vagy több zónát, ahol az Application Gateway-példányok vannak telepítve, ami ellenállóbbá teszi a zónahibákkal szemben. Az alkalmazások háttérkészlete hasonlóan elosztható a rendelkezésre állási zónák között.

  A zónaredundancia csak ott érhető el, ahol az Azure Zones elérhető. Más régiókban az összes többi szolgáltatás támogatott. További információ: [Mik a rendelkezésre állási zónák az Azure-ban?](../availability-zones/az-overview.md#services-support-by-region)
- **Statikus VIP**: Az Application Gateway v2 termékváltozat kizárólag a statikus VIP-típust támogatja. Ez biztosítja, hogy az alkalmazásátjáróhoz társított IP-címzés nem változik a központi telepítés életciklusa során, még újraindítás után sem.  Nincs statikus VIP a v1-ben, ezért az alkalmazásátjáró URL-címét kell használnia az alkalmazásnév-továbbítás ip-cím helyett az alkalmazásátjárón keresztül.
- **Fejléc újraírása**: Az Application Gateway lehetővé teszi, hogy http-kérelem- és válaszfejléceket adjon hozzá, távolítson el vagy frissítsen a v2 Termékváltozattal. További információ: [HTTP-fejlécek újraírása az Application Gateway alkalmazással című témakörben.](rewrite-http-headers.md)
- **Key Vault-integráció:** Az Application Gateway v2 támogatja a Key Vaultmal való integrációt a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok esetében. További információt az [SSL-végződtetés key vault-tanúsítványokkal című témakörben talál.](key-vault-certs.md)
- **Azure Kubernetes service ingress controller:** Az Application Gateway v2 ingress controller lehetővé teszi, hogy az Azure Application Gateway az Azure Kubernetes-szolgáltatás (AKS) AKS-fürt néven a be- és be- és azOn imázsaként használható. További információ: [Mi az Application Gateway ingress controller?](ingress-controller-overview.md).
- **Teljesítménybeli fejlesztések:** A v2 termékváltozat akár 5-nél is jobb SSL-kiszervezési teljesítményt kínál, mint a Standard/WAF termékváltozat.
- **Gyorsabb üzembe helyezési és frissítési idő** A v2 termékváltozat a Standard/WAF termékváltozathoz képest gyorsabb üzembe helyezési és frissítési időt biztosít. Ez magában foglalja a WAF konfigurációs módosításait is.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Támogatott régiók

A Standard_v2 és WAF_v2 SKU a következő régiókban érhető el: USA északi középső régiója, USA déli középső régiója, USA nyugati régiója 2, USA keleti régiója, USA keleti régiója 2, USA középső része, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Franciaország Közép-, Egyesült Királyság-Nyugat, Japán-kelet, Nyugat-Japán, Ausztrália Kelet- , Ausztrália Délkeleti, Brazília Déli, Kanada Középső, Kanada Kelet-Ázsia, Korea Központi, Korea Déli, Egyesült Királyság Déli, Közép-India, Nyugat-India, Dél-India.

## <a name="pricing"></a>Díjszabás

A v2 termékváltozat, a díjszabási modell a felhasználás által vezérelt, és már nem kapcsolódik a példányok száma vagy mérete. A v2 Termékváltozat árképzéskét összetevőből áll:

- **Rögzített ár** – Ez óránkénti (vagy részleges óra) ár egy Standard_v2 vagy WAF_v2 Átjáró kiépítése. Kérjük, vegye figyelembe, hogy 0 további minimális példány továbbra is biztosítja a szolgáltatás magas rendelkezésre állását, amely mindig fix áron van.
- **Kapacitás egységára** – Ez egy fogyasztásalapú költség, amelya fix költségen felül kerül felszámításra. A kapacitásegység-töltés óránként vagy részleges óránként is számításra kerül. A kapacitásegységnek három dimenziója van – számítási egység, állandó kapcsolatok és átviteli képesség. A számítási egység a felhasznált processzorkapacitás mértéke. A számítási egységet befolyásoló tényezők a TLS-kapcsolatok/mp, az URL-újraírási számítások és a WAF-szabályok feldolgozása. Az állandó kapcsolat az alkalmazásátjáróhoz egy adott számlázási időközben létrehozott TCP-kapcsolatok mértéke. Átviteli átmenő egy átlagos megabit/mp a rendszer által egy adott számlázási időközben feldolgozott.  A számlázás kapacitásegység szintjén történik a fenntartott példányok száma feletti minden.

Minden kapacitásegység legbénult: 1 számítási egységből vagy 2500 állandó kapcsolatból vagy 2,22 Mb/s átviteli sebességből áll.

Számítási egység irányítása:

- **Standard_v2** – Minden számítási egység körülbelül 50 kapcsolatot képes másodpercenként az RSA 2048 bites TLS-tanúsítvánnyal.
- **WAF_v2** – Minden számítási egység másodpercenként körülbelül 10 egyidejű kérést támogat a forgalom 70–30%-os kombinációjával, a 2 KB GET/POST-nál kevesebb 70%-kal, és magasabb rakoncátlankodik. A WAF teljesítményét jelenleg nem befolyásolja a válasz mérete.

> [!NOTE]
> Minden példány jelenleg körülbelül 10 kapacitásegységet támogat.
> A számítási egység által kezelhető kérések száma különböző feltételektől függ, például a TLS tanúsítványkulcs méretétől, a kulcscsere algoritmusátírását, a fejléc újraírását, valamint a WAF bejövő kérelemmérete esetén. Azt javasoljuk, hogy alkalmazásteszteket hajtson végre a számítási egységenkénti kérelemsebesség meghatározásához. Mind a kapacitásegység, mind a számítási egység a számlázás megkezdése előtt metrikaként lesz elérhető.

Az alábbi táblázat a példaárakat mutatja be, és csak illusztrációs célokat szolgál.

**Árképzés az USA keleti részén:**

|              Termékváltozat neve                             | Fix ár ($/óra)  | Kapacitás egységára ($/CU-óra)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

További díjszabási információkért tekintse meg az [árképzési oldalt.](https://azure.microsoft.com/pricing/details/application-gateway/) 

**1. példa**

Az Application Gateway Standard_v2 automatikus skálázás nélkül van kiépítve öt példány rögzített kapacitású manuális skálázási módban.

Fix ár = 744(óra) * $0.20 = $148.8 <br>
Kapacitásegységek = 744 (óra) * 10 kapacitásegység példányonként * 5 példány * $0.008 kapacitásegységenként óra = $297.6

Teljes ár = $148.8 + $297.6 = $446.4

**2. példa**

Az Application Gateway standard_v2 egy hónapra van kiépítve, minimális nulla példánysal, és ez idő alatt 25 új SSL-kapcsolatot kap/mp, átlagosan 8,88 Mb/s adatátvitelt. Feltételezve, hogy a kapcsolatok rövid életűek, az ár a következő lenne:

Fix ár = 744(óra) * $0.20 = $148.8

Kapacitás egységára = 744(óra) * Max (25/50 számítási egység kapcsolatokhoz/mp, 8,88/2,22 kapacitásegység az átviteli sebességhez) * $0.008 = 744 * 4 * 0,008 = $23.81

Teljes ár = $148.8+23.81 = $172.61

Mint látható, csak négy kapacitásegységért kell fizetnie, nem pedig az egész példányért. 

> [!NOTE]
> A Max függvény egy értékpár legnagyobb értékét adja eredményül.


**3. példa**

Az Application Gateway standard_v2 egy hónapra van kiépítve, legalább öt példányban. Feltételezve, hogy nincs forgalom, és a kapcsolatok rövid életű, az ár lenne:

Fix ár = 744(óra) * $0.20 = $148.8

Kapacitás egységára = 744(óra) * Max (0/50 számítási egység kapcsolatokhoz/mp, 0/2,22 kapacitásegység az átviteli sebességhez) * $0.008 = 744 * 50 * 0.008 = $297.60

Teljes ár = $148.80+297.60 = $446.4

Ebben az esetben az öt példány teljes egészét számlázunk, még akkor is, ha nincs forgalom.

**4. példa**

Az Application Gateway standard_v2 egy hónapra van kiépítve, legalább öt példányban, de ezúttal átlagosan 125 mb/s adatátvitel, és 25 SSL-kapcsolat másodpercenként. Feltételezve, hogy nincs forgalom, és a kapcsolatok rövid életű, az ár lenne:

Fix ár = 744(óra) * $0.20 = $148.8

Kapacitás egységára = 744(óra) * Max (25/50 számítási egység kapcsolatokhoz/mp, 125/2,22 kapacitásegység az átviteli sebességhez) * $0.008 = 744 * 57 * 0,008 = $339.26

Teljes ár = $148.80+339.26 = $488.06

Ebben az esetben a teljes öt példányért, valamint hét kapacitásegységért (ami egy példány 7/10-e) számlázunk.  

**5. példa**

Egy alkalmazásátjáró WAF_v2 egy hónapra van kiépítve. Ez idő alatt 25 új SSL-kapcsolatot kap másodpercenként, átlagosan 8,88 Mb/s adatátvitelt, és másodpercenként 80 kérést küld. Feltételezve, hogy a kapcsolatok rövid életűek, és hogy az alkalmazás számítási egységének számítása 10 RPS-t támogat számítási egységenként, az ön ára a következő:

Fix ár = 744(óra) * $0.36 = $267.84

Kapacitás egységára = 744(óra) * Max (maximális számítási egység(25/50 kapcsolatok/mp, 80/10 WAF RPS), 8,88/2,22 kapacitásegység az átviteli sebességhez) * $0,0144 = 744 * 8 * 0,0144 = $85.71

Teljes ár = $267.84 + $85.71 = $353.55

> [!NOTE]
> A Max függvény egy értékpár legnagyobb értékét adja eredményül.

## <a name="scaling-application-gateway-and-waf-v2"></a>Alkalmazásátjáró és WAF-v2 méretezése

Az Application Gateway és a WAF két módban is beállítható:

- **Automatikus skálázás** – Ha az automatikus skálázás engedélyezve van, az Application Gateway és a WAF v2 SK-ok az alkalmazás forgalmi követelményei alapján fel- és leskálázásra. Ez a mód jobb rugalmasságot biztosít az alkalmazás számára, és szükségtelenné teszi az alkalmazásátjáró méretének vagy példányszámának kitalálását. Ez a mód lehetővé teszi a költségek megtakarítását is, mivel nem követeli meg, hogy az átjáró csúcskapacitáson fusson a várható maximális forgalomterheléshez. Meg kell adnia egy minimális és opcionálisan maximális példányszámot. A minimális kapacitás biztosítja, hogy az Application Gateway és a WAF v2 ne essen a megadott minimális példányszám alá, még forgalom hiányában sem. Minden példány 10 további fenntartott kapacitásegységnek számít. Nulla azt jelenti, nem fenntartott kapacitás, és pusztán automatikus skálázás jellegű. Kérjük, vegye figyelembe, hogy a nulla további minimális példány oka továbbra is biztosítja a szolgáltatás magas rendelkezésre állását, amely mindig fix áron van. Szükség esetén megadhatja a maximális példányszámot is, amely biztosítja, hogy az Application Gateway ne méretezze túl a megadott számú példányt. Továbbra is az átjáró által kiszolgált forgalom mennyiségéért kell fizetnie. A példányok száma 0 és 125 között lehet. A maximális példányszám alapértelmezett értéke 20, ha nincs megadva.
- **Manuális** – Választhatja a Kézi módot is, ahol az átjáró nem méretezhető automatikusan. Ebben a módban, ha nagyobb a forgalom, mint amit az Application Gateway vagy a WAF kezelni tud, az forgalom kiesését eredményezheti. Kézi módban kötelező megadni a példányok számát. A példányok száma 1 és 125 példány között változhat.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Szolgáltatás összehasonlítása v1 Termékváltozat és v2 Termékváltozat

Az alábbi táblázat összehasonlítja az egyes termékváltozatokkal elérhető funkciókat.

|                                                   | v1 Termékváltozat   | v2 Termékváltozat   |
| ------------------------------------------------- | -------- | -------- |
| Automatikus skálázás                                       |          | &#x2713; |
| Zóna redundanciája                                   |          | &#x2713; |
| Statikus VIP                                        |          | &#x2713; |
| Az Azure Kubernetes szolgáltatás (AKS) ingress vezérlője |          | &#x2713; |
| Azure Key Vault-integráció                       |          | &#x2713; |
| HTTP(S) fejlécek újraírása                           |          | &#x2713; |
| URL-alapú útválasztás                                 | &#x2713; | &#x2713; |
| Több hely üzemeltetése                             | &#x2713; | &#x2713; |
| Forgalom átirányítása                               | &#x2713; | &#x2713; |
| Webalkalmazási tűzfal (WAF)                    | &#x2713; | &#x2713; |
| WAF egyéni szabályok                                  |          | &#x2713; |
| Secure Sockets Layer- (SSL-) lezárás            | &#x2713; | &#x2713; |
| Végpontok közötti SSL-titkosítás                         | &#x2713; | &#x2713; |
| Munkamenet-affinitás                                  | &#x2713; | &#x2713; |
| Egyéni hibalapok                                | &#x2713; | &#x2713; |
| WebSocket támogatás                                 | &#x2713; | &#x2713; |
| HTTP/2-támogatás                                    | &#x2713; | &#x2713; |
| Kapcsolatkiürítés                               | &#x2713; | &#x2713; |

> [!NOTE]
> Az automatikus skálázás v2 termékváltozat mostantól támogatja az [alapértelmezett állapotminta](application-gateway-probe-overview.md#default-health-probe) automatikusan figyeli a háttér-készletében lévő összes erőforrás állapotát, és kiemeli azokat a háttértagokat, amelyek nem kifogástalannak minősülnek. Az alapértelmezett állapotminta automatikusan konfigurálva van olyan háttérrendszerekhez, amelyek nem rendelkeznek egyéni mintavételi konfigurációval. További információ: [állapotminta az alkalmazásátjáróban.](application-gateway-probe-overview.md)

## <a name="differences-with-v1-sku"></a>Különbségek a v1 Termékváltozattal

|Különbség|Részletek|
|--|--|
|Hitelesítési tanúsítvány|Nem támogatott.<br>További információt a [Végpontok között SSL áttekintése az Application Gateway segítségével című témakörben talál.](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)|
|Standard_v2 és a Standard Application Gateway keverése ugyanazon az alhálózaton|Nem támogatott|
|Felhasználó által definiált útvonal (UDR) az Application Gateway alhálózaton|Támogatott (adott forgatókönyvek). Előzetesverzióban.<br> A támogatott forgatókönyvekről az [Application Gateway konfigurációjának áttekintése című témakörben olvashat bővebben.](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)|
|NSG bejövő porttartományhoz| - 65200 és 65535 között Standard_v2 termékváltozat esetében<br>- 65503-tól 65534-ig a standard termékváltozatok esetében.<br>További információt a [GYAKORI KÉRDÉSEK című témakörben talál.](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)|
|Teljesítménynaplók az Azure-diagnosztikában|Nem támogatott.<br>Az Azure-metrikákat kell használni.|
|Számlázás|A számlázás a tervek szerint 2019. július 1-jén kezdődik.|
|FIPS mód|Ezek jelenleg nem támogatottak.|
|Csak ILB mód|Ez jelenleg nem támogatott. A nyilvános és az ILB mód együtt támogatott.|
|Netwatcher integráció|Nem támogatott.|
|Azure Security Center – integráció|Még nem érhető el.

## <a name="migrate-from-v1-to-v2"></a>Migrálás az 1-esről a 2-es verzióra

Egy Azure PowerShell-parancsfájl érhető el a PowerShell-galériában, amely segít a v1 Application Gateway/WAF-ről a v2 automatikus skálázási termékváltozatra való áttelepítéshez. Ez a parancsfájl segít a konfiguráció másolása a v1 átjáró. A forgalom migrációja továbbra is az Ön felelőssége. További információ: [Az Azure Application Gateway áttelepítése a v1-ről a 2-es v2-re](migrate-v1-v2.md)című témakörben található.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal](quick-create-portal.md)
- [Automatikus skálázási, zónaredundáns alkalmazásátjáró létrehozása fenntartott virtuális IP-címmel az Azure PowerShell használatával](tutorial-autoscale-ps.md)
- További információ az [Application Gateway alkalmazásról.](overview.md)
- További információ az [Azure Firewall ről.](../firewall/overview.md)
