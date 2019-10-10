---
title: Automatikusan skálázó és zónaredundáns Application Gateway v2
description: Ez a cikk bemutatja az Azure Application Standard_v2 és a WAF_v2 SKU-t, amely magában foglalja az automatikus skálázást és a zóna-redundáns funkciókat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: f58ac4448f50e8e02f2838fef02c9f884f69266b
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177452"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatikusan skálázó és zónaredundáns Application Gateway v2 

A Application Gateway és a webalkalmazási tűzfal (WAF) a Standard_v2 és a WAF_v2 SKU alatt is elérhető. A v2 SKU teljesítmény-fejlesztéseket biztosít, és támogatja a kritikus fontosságú új funkciókat, például az automatikus skálázást, a zónák redundanciát és a statikus VIP-címek támogatását. A standard és a WAF SKU meglévő szolgáltatásai továbbra is támogatottak lesznek az új v2 SKU-ban, és néhány kivétel szerepel az [összehasonlítás](#differences-with-v1-sku) szakaszban.

Az új v2 SKU a következő fejlesztéseket tartalmazza:

- Automatikus **skálázás**: az automatikus skálázási SKU-ban lévő Application Gateway-vagy WAF-telepítések a forgalmi terhelési minták módosítása alapján vertikális fel-vagy leskálázást is igénybe vehetnek. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. Ez az SKU valódi rugalmasságot biztosít. A Standard_v2 és a WAF_v2 SKU-ban a Application Gateway a rögzített kapacitásban (automatikus skálázás letiltva) és az automatikus skálázást engedélyező módban is működhet. A rögzített kapacitás mód olyan helyzetekben hasznos, amelyek konzisztens és kiszámítható számítási feladatokkal rendelkeznek. Az automatikus skálázási mód hasznos olyan alkalmazásokban, amelyek az alkalmazás forgalmában tapasztalnak eltérést.
- **Zóna redundancia**: egy Application Gateway vagy WAF üzemelő példány több Availability Zonesra is terjedhet, így nem kell külön Application Gateway példányt kiépíteni az egyes zónákban Traffic Manager. Kiválaszthat egyetlen zónát vagy több olyan zónát, ahol Application Gateway példányok vannak telepítve, ami rugalmasabb lehet a zóna meghibásodása miatt. Az alkalmazások háttér-készlete hasonló módon terjeszthető a rendelkezésre állási zónák között.

  A zóna redundancia csak akkor érhető el, ha az Azure-zónák elérhetők. Más régiókban a többi funkció is támogatott. További információ: [Mi a Availability Zones az Azure-ban?](../availability-zones/az-overview.md#services-support-by-region)
- **Statikus VIP**: a Application Gateway v2 SKU kizárólag a statikus VIP-típust támogatja. Ez biztosítja, hogy az Application gatewayhez társított virtuális IP-cím ne változzon az üzemelő példány életciklusa alatt, még újraindítás után is.  Nem létezik statikus virtuális IP-cím a v1-ben, ezért az Application Gateway URL-címe helyett a tartománynév-útválasztáshoz használt IP-címet kell használnia az Application Gateway használatával App Services.
- **Fejléc újraírása**: a Application Gateway lehetővé teszi a HTTP-kérések és a válasz-fejlécek hozzáadását, eltávolítását vagy frissítését v2 SKU-val. További információt a HTTP- [fejlécek újraírása a Application Gatewaysal](rewrite-http-headers.md) című témakörben talál.
- **Key Vault Integration (előzetes verzió)** : a Application Gateway v2 támogatja a https-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault (nyilvános előzetes verzió) integrációját. További információ: SSL- [lezárás Key Vault tanúsítványokkal](key-vault-certs.md).
- **Azure Kubernetes Service beáramlási vezérlő (előzetes verzió)** : a Application Gateway v2 beáramló vezérlő lehetővé teszi, hogy az Azure Application Gateway használható legyen a bemenő forgalomként egy Azure Kubernetes szolgáltatás (ak) néven, az AK-fürt. További információkért tekintse meg a [dokumentációs oldalt](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Teljesítménybeli fejlesztések**: a v2 SKU a standard/WAF SKU-hoz képest akár 5x-ös nagyobb SSL-kiszervezési teljesítményt nyújt.
- **Gyorsabb üzembe helyezés és frissítés ideje** A v2 SKU gyorsabb üzembe helyezést és frissítési időt biztosít a standard/WAF SKU-hoz képest. Ez magában foglalja a WAF konfigurációs változásait is.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Támogatott régiók

A Standard_v2 és a WAF_v2 SKU a következő régiókban érhető el: az USA északi középső régiója, az USA déli középső régiója, az USA nyugati régiója, az USA nyugati régiója, az USA keleti régiója, USA 2. keleti régiója, USA középső régiója, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Közép-Németország, Kelet-Ausztrália Egyesült Királyság nyugati régiója , Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Kelet-Ázsia, Korea középső régiója, Dél-Korea, Dél-India, Egyesült Királyság déli régiója, Közép-India, Nyugat-India, Dél-India.

## <a name="pricing"></a>Díjszabás

A v2 SKU-val a díjszabási modellt a használat vezérli, és a rendszer már nem csatolja a példányszámot vagy a méretet. A v2 SKU díjszabása két összetevőből áll:

- **Rögzített ár** – ez egy Standard_v2-vagy WAF_v2-átjáró kiépítésének óránkénti (vagy részleges órás) díja.
- **Kapacitási egység díja** – ez a fogyasztáson alapuló költség, amelyet a rögzített költség mellett számítunk fel. A kapacitásegység díjait is óránkénti vagy részben óránkénti egységekben mérjük. A kapacitásegységek három összetevőből állnak: a számítási egység, az állandó kapcsolatok és az átviteli sebesség. A számítási egység a felhasznált processzorkapacitás mértékegysége. A számítási egységet befolyásoló tényezők a TLS-kapcsolatok/mp, az URL-Újraírási számítások és a WAF-szabályok feldolgozása. Az állandó kapcsolat az Application Gateway számára a megadott számlázási időszakban létesített TCP-kapcsolatok mértéke. Az átviteli sebesség egy adott számlázási időszakban a rendszer által feldolgozott átlagos megabit/mp.

Minden kapacitási egység legfeljebb a következőkből áll: 1 számítási egység, vagy 2500 állandó kapcsolat vagy 2,22 – Mbps átviteli sebesség.

Számítási egységre vonatkozó útmutató:

- **Standard_v2** – minden számítási egység képes körülbelül 50 kapcsolatot másodpercenként az RSA 2048-bites Key TLS-tanúsítvánnyal.
- **WAF_v2** – az egyes számítási egységek körülbelül 10 egyidejű kérést támogatnak másodpercenként 70-30%-os adatforgalomhoz, 70%-os kérelmeknél kevesebb, mint 2 KB-os Get/post és annál nagyobb marad. A WAF teljesítményét jelenleg nem érinti a válasz mérete.

> [!NOTE]
> Az egyes példányok jelenleg körülbelül 10 kapacitást képesek támogatni.
> A számítási egységek által kezelhető kérelmek száma különböző feltételektől függ, például a TLS-tanúsítvány kulcsának méretétől, a kulcscsere-algoritmustól, a fejléc újraírásának és a WAF bejövő kérések méretétől függően. Javasoljuk, hogy az alkalmazás-tesztek alapján határozza meg a kérelmek sebességét számítási egységenként. A számlázás elkezdése előtt a kapacitás egység és a számítási egység mérőszámként is elérhetővé válik.

Az alábbi táblázat az árakat mutatja be, és csak illusztrációs célokat szolgál.

**Díjszabás az USA keleti régiójában**:

|              SKU neve                             | Rögzített ár ($/óra)  | Kapacitási egység ára ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0,0080                          |
| WAF_v2                                            |    0,36             | 0,0144                          |

A díjszabással kapcsolatos további információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/application-gateway/). A számlázás a 2019. július 1-jén kezdődik.

**1. példa**

Az Application Gateway Standard_v2 a manuális skálázási módban automatikus skálázás nélkül, öt példány rögzített kapacitással lett kiépítve.

Rögzített ár = 744 (óra) * $0,20 = $148,8 <br>
Kapacitási egységek = 744 (óra) * 10 kapacitási egység/példány * 5 példány * $0,008/kapacitás egység órája = $297,6

Teljes ár = $148,8 + $297,6 = $446,4

**2. példa**

Egy Application Gateway standard_v2 egy hónapig lett kiépítve, ezért 25 új SSL-kapcsolatot (/s) kap, átlagosan 8,88 MB/s adatátviteli sebesség. Ha a kapcsolatok rövid életűek, az ár a következő:

Rögzített ár = 744 (óra) * $0,20 = $148,8

Kapacitási egység ára = 744 (óra) * Max (25/50 számítási egység kapcsolatokhoz/s, 8.88/2.22 kapacitási egység az átviteli sebességhez) * $0,008 = 744 * 4 * 0,008 = $23,81

Teljes ár = $148.8 + 23.81 = $172,61

> [!NOTE]
> A Max függvény a legnagyobb értéket adja vissza egy értékben.

**3. példa**

Egy adott hónapban egy Application Gateway WAF_v2 van kiépítve. Ebben az időszakban 25 új SSL-kapcsolat/másodperc, átlagosan 8,88 MB/s adatátviteli sebesség és a 80-es kérelem másodpercenkénti száma jelenik meg. Ha a kapcsolatok rövid életűek, és az alkalmazás számítási egységének kiszámítása a számítási egységenként 10 RPS-t támogat, a díj a következő lesz:

Rögzített ár = 744 (óra) * $0,36 = $267,84

Kapacitási egység ára = 744 (óra) * Max (számítási egység Max (25/50 a kapcsolatok/mp, 80/10 WAF RPS), 8.88/2.22 kapacitási egység az átviteli sebességhez) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Teljes ár = $267,84 + $85,71 = $353,55

> [!NOTE]
> A Max függvény a legnagyobb értéket adja vissza egy értékben.

## <a name="scaling-application-gateway-and-waf-v2"></a>A Application Gateway és a WAF v2 méretezése

A Application Gateway és a WAF két módban is konfigurálható:

- Automatikus **skálázás** – ha engedélyezve van az automatikus skálázás, a Application Gateway és a WAF v2 SKU-ra vertikális fel-és leskálázás az alkalmazás-forgalmi követelmények alapján. Ez a mód jobb rugalmasságot biztosít az alkalmazás számára, és nem kell kitalálnia az Application Gateway-méretet vagy a példányszámot. Ez a mód lehetővé teszi a költségmegtakarítást úgy, hogy a várt maximális forgalmi terheléshez nem szükséges, hogy az átjáró kiosztott kapacitással fusson. Meg kell adnia a példányok minimális és opcionális maximális példányszámát. A minimális kapacitás biztosítja, hogy a Application Gateway és a WAF v2 ne érje el a megadott minimális példányszámot, még a forgalom hiányában is. Minden példány 10 további fenntartott kapacitási egységnek számít. 0 nem fenntartott kapacitást jelez, és tisztán automatikus méretezést jelent a természetben. Vegye figyelembe, hogy a 0 további minimális példány továbbra is biztosítja a szolgáltatás magas rendelkezésre állását, amely mindig rögzített díjszabással van ellátva. Megadhatja a példányok maximális számát is, ami garantálja, hogy a Application Gateway a megadott számú példányon túl nem méretezhető. Az átjáró által kiszolgált forgalom mennyiségét továbbra is számlázjuk. A példányok száma 0 és 125 között lehet. A példányok maximális számának alapértelmezett értéke 20, ha nincs megadva. 
- **Manuális** – választhatja azt a manuális módot is, amelyben az átjáró nem rendelkezik Automatikus méretezéssel. Ebben a módban, ha több forgalom van, mint amit a Application Gateway-vagy WAF tud kezelni, a forgalom elvesztését eredményezheti. A manuális mód megadása esetén kötelező megadni a példányszámot. A példányok száma 1 és 125 példány között lehet.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Szolgáltatások összehasonlítása v1 SKU és v2 SKU között

A következő táblázat összehasonlítja az egyes SKU-kal elérhető szolgáltatásokat.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatikus skálázás                                       |          | &#x2713; |
| Zóna redundancia                                   |          | &#x2713; |
| Statikus VIP                                        |          | &#x2713; |
| Azure Kubernetes szolgáltatás (ak) bejövő adatkezelője |          | &#x2713; |
| Azure Key Vault-integráció                       |          | &#x2713; |
| HTTP (S) fejlécek újraírása                           |          | &#x2713; |
| URL-alapú útválasztás                                 | &#x2713; | &#x2713; |
| Több hely üzemeltetése                             | &#x2713; | &#x2713; |
| Forgalom átirányítása                               | &#x2713; | &#x2713; |
| Webalkalmazási tűzfal (WAF)                    | &#x2713; | &#x2713; |
| Secure Sockets Layer- (SSL-) lezárás            | &#x2713; | &#x2713; |
| Végpontok közötti SSL-titkosítás                         | &#x2713; | &#x2713; |
| Munkamenet-affinitás                                  | &#x2713; | &#x2713; |
| Egyéni hibalapok                                | &#x2713; | &#x2713; |
| WebSocket támogatás                                 | &#x2713; | &#x2713; |
| HTTP/2-támogatás                                    | &#x2713; | &#x2713; |
| Kapcsolatkiürítés                               | &#x2713; | &#x2713; |

> [!NOTE]
> Az automatikus skálázási v2 SKU mostantól támogatja az [alapértelmezett állapot](application-gateway-probe-overview.md#default-health-probe) -ellenőrzéseket, hogy automatikusan figyelje a háttér-készlet összes erőforrásának állapotát, és kiemelje azokat a háttérbeli tagokat, amelyek nem megfelelőnek minősülnek. Az alapértelmezett állapot-mintavétel automatikusan konfigurálva van olyan backendekhez, amelyek nem rendelkeznek egyéni mintavételi konfigurációval. További információért lásd: [az Application Gateway Health-szondái](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Különbségek a v1 SKU-val

|Különbség|Részletek|
|--|--|
|Hitelesítési tanúsítvány|Nem támogatott.<br>További információ: [a végpontok közötti SSL áttekintése Application Gateway használatával](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Standard_v2 és standard Application Gatewayek keverése ugyanazon az alhálózaton|Nem támogatott|
|Felhasználó által megadott útvonal (UDR) Application Gateway alhálózaton|Nem támogatott|
|NSG a bejövő porttartomány| -65200 – 65535 a Standard_v2 SKU-hoz<br>-65503 – 65534 a standard SKU-hoz.<br>További információt a [Gyakori kérdések](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)című témakörben talál.|
|Teljesítménynaplók az Azure diagnosticsban|Nem támogatott.<br>Az Azure-metrikákat kell használni.|
|Számlázás|Számlázás ütemezve, hogy 2019. július 1-jén induljon el.|
|FIPS mód|Ezek jelenleg nem támogatottak.|
|Csak ILB mód|Ez jelenleg nem támogatott. A nyilvános és a ILB mód együttes használata támogatott.|
|Netwatcher-integráció|Nem támogatott.|
|Azure Security Center integráció|Még nem érhető el.

## <a name="migrate-from-v1-to-v2"></a>Migrálás az 1-esről a 2-es verzióra

Azure PowerShell parancsfájl a PowerShell-galériában érhető el, hogy segítséget nyújtson a v1 Application Gateway/WAF a v2 automatikus skálázási SKU-ba való átálláshoz. Ez a szkript segítséget nyújt a konfigurációnak a v1-átjáróról történő másolásához. A forgalom áttelepítése továbbra is az Ön felelőssége. További információ: [Azure Application Gateway migrálása v1-ről v2-re](migrate-v1-v2.md).

## <a name="next-steps"></a>Következő lépések

- [Gyors útmutató: webes forgalom közvetlen továbbítása az Azure Application Gateway-Azure Portal](quick-create-portal.md)
- [Hozzon létre egy automatikus skálázást, a Zone redundáns Application Gateway-t egy fenntartott virtuális IP-címmel a Azure PowerShell használatával](tutorial-autoscale-ps.md)
- További információ a [Application Gatewayról](overview.md).
- További információ a [Azure Firewallról](../firewall/overview.md).
