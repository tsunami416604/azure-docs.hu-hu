---
title: Automatikusan skálázó és zónaredundáns Application Gateway v2
description: Ez a cikk bemutatja az Azure Application Standard_v2 és WAF_v2 SKU-t, amely magában foglalja az automatikus skálázást és a zóna-redundáns funkciókat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: ab2165cb6b48e58a7adca7093f7ff82d10495066
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196478"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatikusan skálázó és zónaredundáns Application Gateway v2 

A Application Gateway és a webalkalmazási tűzfal (WAF) Standard_v2 és WAF_v2 SKU alatt is elérhető. A v2 SKU teljesítmény-fejlesztéseket biztosít, és támogatja a kritikus fontosságú új funkciókat, például az automatikus skálázást, a zónák redundanciát és a statikus VIP-címek támogatását. A standard és a WAF SKU meglévő szolgáltatásai továbbra is támogatottak lesznek az új v2 SKU-ban, és néhány kivétel szerepel az [összehasonlítás](#differences-from-v1-sku) szakaszban.

Az új v2 SKU a következő fejlesztéseket tartalmazza:

- Automatikus **skálázás**: az automatikus skálázási SKU-ban lévő Application Gateway-vagy WAF-telepítések a forgalmi terhelési minták módosítása alapján vertikális fel-vagy leskálázást is igénybe vehetnek. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. Ez az SKU valódi rugalmasságot biztosít. A Standard_v2 és WAF_v2 SKU-ban a Application Gateway a rögzített kapacitásban (automatikus skálázás letiltva) és az automatikus skálázást engedélyező módban is működhet. A rögzített kapacitás mód olyan helyzetekben hasznos, amelyek konzisztens és kiszámítható számítási feladatokkal rendelkeznek. Az automatikus skálázási mód hasznos olyan alkalmazásokban, amelyek az alkalmazás forgalmában tapasztalnak eltérést.
- **Zóna redundancia**: egy Application Gateway vagy WAF üzemelő példány több Availability Zonesra is terjedhet, így nem kell külön Application Gateway példányt kiépíteni az egyes zónákban Traffic Manager. Kiválaszthat egyetlen zónát vagy több olyan zónát, ahol Application Gateway példányok vannak telepítve, ami rugalmasabb lehet a zóna meghibásodása miatt. Az alkalmazások háttér-készlete hasonló módon terjeszthető a rendelkezésre állási zónák között.

  A zóna redundancia csak akkor érhető el, ha az Azure-zónák elérhetők. Más régiókban a többi funkció is támogatott. További információ: [régiók és Availability Zones az Azure-ban](../availability-zones/az-overview.md)
- **Statikus VIP**: a Application Gateway v2 SKU kizárólag a statikus VIP-típust támogatja. Ez biztosítja, hogy az Application gatewayhez társított virtuális IP-cím ne változzon az üzemelő példány életciklusa alatt, még újraindítás után is.  Nem létezik statikus virtuális IP-cím a v1-ben, ezért az Application Gateway URL-címe helyett a tartománynév-útválasztáshoz használt IP-címet kell használnia az Application Gateway használatával App Services.
- **Fejléc újraírása**: a Application Gateway lehetővé teszi a HTTP-kérések és a válasz-fejlécek hozzáadását, eltávolítását vagy frissítését v2 SKU-val. További információt a HTTP- [fejlécek újraírása a Application Gatewaysal](rewrite-http-headers.md) című témakörben talál.
- **Key Vault integráció**: a Application Gateway v2 támogatja az Key Vault-integrációt a https-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványokhoz. További információ: TLS- [lezárás Key Vault tanúsítványokkal](key-vault-certs.md).
- **Azure Kubernetes-szolgáltatás bejövő adatkezelője**: a Application Gateway v2 beáramló vezérlő lehetővé teszi, hogy az azure-Application Gateway használható legyen a bejövő forgalomként egy Azure Kubernetes szolgáltatás (ak) számára, az úgynevezett AK-fürt. További információ: [Mi az a Application Gateway beáramló vezérlő?](ingress-controller-overview.md).
- **Teljesítménybeli fejlesztések**: a v2 SKU a standard/WAF SKU-hoz képest akár 5x-ös nagyobb TLS-kiszervezési teljesítményt nyújt.
- **Gyorsabb üzembe helyezés és frissítés ideje** A v2 SKU gyorsabb üzembe helyezést és frissítési időt biztosít a standard/WAF SKU-hoz képest. Ez magában foglalja a WAF konfigurációs változásait is.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Támogatott régiók

A Standard_v2 és WAF_v2 SKU a következő régiókban érhető el: az USA északi középső régiója, az USA déli középső régiója, az USA nyugati régiója, az USA nyugati régiója, USA keleti régiója, USA 2. keleti régiója, USA középső régiója, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Kelet-Ázsia Kelet-Ausztrália Egyesült Királyság nyugati régiója Közép-Németország , Egyesült Királyság déli régiója, Közép-India, Nyugat-India, Dél-India.

## <a name="pricing"></a>Díjszabás

A v2 SKU-val a díjszabási modellt a használat vezérli, és a rendszer már nem csatolja a példányszámot vagy a méretet. A v2 SKU díjszabása két összetevőből áll:

- **Rögzített ár** – ez egy Standard_v2 vagy WAF_v2 átjáró kiépítésének óránkénti (vagy részleges órányi) díja. Vegye figyelembe, hogy a 0 további minimális példány továbbra is biztosítja a szolgáltatás magas rendelkezésre állását, amely mindig rögzített díjszabással van ellátva.
- **Kapacitási egység díja** – ez egy fogyasztási alapú költség, amelyet a rögzített költség mellett számítunk fel. A kapacitási egység díját óránként vagy részben is számításba kell venni. A kapacitás egysége – számítási egység, állandó kapcsolatok és átviteli sebesség – három dimenzióval rendelkezik. A számítási egység a felhasznált processzorkapacitás mérőszáma. A számítási egységet befolyásoló tényezők a TLS-kapcsolatok/mp, az URL-újraírási számítások és a WAF-szabályok feldolgozása. Az állandó kapcsolat az Application Gateway számára a megadott számlázási időszakban létesített TCP-kapcsolatok mértéke. Az átviteli sebesség egy adott számlázási időszakban a rendszer által feldolgozott átlagos megabit/mp.  A számlázás a fenntartott példányok számának megfelelő kapacitás egység szintjén történik.

Minden kapacitási egység legfeljebb a következőkből áll: 1 számítási egység, 2500 állandó kapcsolat és 2,22 – Mbps átviteli sebesség.

Számítási egységre vonatkozó útmutató:

- **Standard_v2** – minden számítási egység képes körülbelül 50 kapcsolat másodpercenként az RSA 2048-bites Key TLS-tanúsítvánnyal.
- **WAF_v2** – az egyes számítási egységek körülbelül 10 egyidejű kérést támogatnak másodpercenként a 70-30%-os adatforgalomhoz, 70%-nál kevesebb kérés/post, és annál nagyobb marad. A WAF teljesítményét jelenleg nem érinti a válasz mérete.

> [!NOTE]
> Az egyes példányok jelenleg körülbelül 10 kapacitást képesek támogatni.
> A számítási egységek által kezelhető kérelmek száma különböző feltételektől függ, például a TLS-tanúsítvány kulcsának méretétől, a kulcscsere-algoritmustól, a fejléc újraírásának és a WAF bejövő kérések méretétől függően. Javasoljuk, hogy az alkalmazás-tesztek alapján határozza meg a kérelmek sebességét számítási egységenként. A számlázás elkezdése előtt a kapacitás egység és a számítási egység mérőszámként is elérhetővé válik.

Az alábbi táblázat az árakat mutatja be, és csak illusztrációs célokat szolgál.

**Díjszabás az USA keleti régiójában**:

|              SKU neve                             | Rögzített ár ($/óra)  | Kapacitási egység ára ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0,0080                          |
| WAF_v2                                            |    0.36             | 0,0144                          |

A díjszabással kapcsolatos további információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/application-gateway/). 

**1\. példa**

Az Application Gateway Standard_v2 a manuális skálázási módban automatikus skálázás nélkül, öt példány rögzített kapacitással lett kiépítve.

Rögzített ár = 744 (óra) * $0,20 = $148,8 <br>
Kapacitási egységek = 744 (óra) * 10 kapacitási egység/példány * 5 példány * $0,008/kapacitás egység órája = $297,6

Teljes ár = $148,8 + $297,6 = $446,4

**2\. példa**

Egy Application Gateway standard_v2 egy hónapig van kiépítve, minimális példányszámban, és ez idő alatt 25 új TLS-kapcsolat/s, átlagosan 8,88 – Mbps adatátvitelt kap. Ha a kapcsolatok rövid életűek, az ár a következő:

Rögzített ár = 744 (óra) * $0,20 = $148,8

Kapacitási egység ára = 744 (óra) * Max (25/50 számítási egység kapcsolatokhoz/s, 8.88/2.22 kapacitási egység az átviteli sebességhez) * $0,008 = 744 * 4 * 0,008 = $23,81

Teljes ár = $148.8 + 23.81 = $172,61

Amint láthatja, a rendszer csak négy kapacitású egységért számláz, nem pedig a teljes példányért. 

> [!NOTE]
> A Max függvény a legnagyobb értéket adja vissza egy értékben.


**3\. példa**

Egy Application Gateway standard_v2 egy hónapig van kiépítve, amely legalább öt példányból áll. Feltételezve, hogy a forgalom és a kapcsolatok nincsenek rövid életűek, az ár a következő:

Rögzített ár = 744 (óra) * $0,20 = $148,8

Kapacitási egység ára = 744 (óra) * Max (0/50 számítási egység a kapcsolatok/mp, 0/2.22 kapacitási egység az átviteli sebességhez) * $0,008 = 744 * 50 * 0,008 = $297,60

Teljes ár = $148.80 + 297.60 = $446,4

Ebben az esetben az öt példány teljes összegét számítjuk fel, még akkor is, ha nincs forgalom.

**4. példa**

Egy Application Gateway standard_v2 egy hónapig van kiépítve, amely legalább öt példányból áll, de ezúttal átlagosan 125 MB/s adatátviteli sebesség és 25 TLS-kapcsolat másodpercenként. Feltételezve, hogy a forgalom és a kapcsolatok nincsenek rövid életűek, az ár a következő:

Rögzített ár = 744 (óra) * $0,20 = $148,8

Kapacitási egység ára = 744 (óra) * Max (25/50 számítási egység kapcsolatokhoz/mp, 125/2.22 kapacitási egység az átviteli sebességhez) * $0,008 = 744 * 57 * 0,008 = $339,26

Teljes ár = $148.80 + 339.26 = $488,06

Ebben az esetben a teljes öt példányra, valamint hét kapacitási egységre (amely egy példány 7/10) számítunk fel díjat.  

**5. példa**

Egy hónapra kiépített Application Gateway WAF_v2. Ebben az időszakban 25 új TLS-kapcsolat/másodperc, átlagosan 8,88 MB/s adatátviteli sebesség és a 80-es kérelem másodpercenkénti száma jelenik meg. Ha a kapcsolatok rövid életűek, és az alkalmazás számítási egységének kiszámítása a számítási egységenként 10 RPS-t támogat, a díj a következő lesz:

Rögzített ár = 744 (óra) * $0,36 = $267,84

Kapacitási egység ára = 744 (óra) * Max (számítási egység Max (25/50 a kapcsolatok/mp, 80/10 WAF RPS), 8.88/2.22 kapacitási egység az átviteli sebességhez) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Teljes ár = $267,84 + $85,71 = $353,55

> [!NOTE]
> A Max függvény a legnagyobb értéket adja vissza egy értékben.

## <a name="scaling-application-gateway-and-waf-v2"></a>A Application Gateway és a WAF v2 méretezése

A Application Gateway és a WAF két módban is konfigurálható:

- Automatikus **skálázás** – ha engedélyezve van az automatikus skálázás, a Application Gateway és a WAF v2 SKU-ra vertikális fel-és leskálázás az alkalmazás-forgalmi követelmények alapján. Ez a mód jobb rugalmasságot biztosít az alkalmazás számára, és nem kell kitalálnia az Application Gateway-méretet vagy a példányszámot. Ez a mód lehetővé teszi a költségmegtakarítást úgy, hogy a várt maximális forgalmi terheléshez nem szükséges, hogy az átjáró kiosztott kapacitással fusson. Meg kell adnia a példányok minimális és opcionális maximális példányszámát. A minimális kapacitás biztosítja, hogy a Application Gateway és a WAF v2 ne érje el a megadott minimális példányszámot, még a forgalom hiányában is. Az egyes példányok nagyjából 10 további fenntartott kapacitási egységnek felelnek meg. A nulla nem jelent fenntartott kapacitást, és kizárólag az automatikus skálázást jelenti. Megadhatja a példányok maximális számát is, ami garantálja, hogy a Application Gateway a megadott számú példányon túl nem méretezhető. Az átjáró által kiszolgált forgalom mennyisége csak a számlázás után történik meg. A példányok száma 0 és 125 között lehet. A példányok maximális számának alapértelmezett értéke 20, ha nincs megadva.
- **Manuális** – választhatja azt a manuális módot is, amelyben az átjáró nem rendelkezik Automatikus méretezéssel. Ebben a módban, ha több forgalom van, mint amit a Application Gateway-vagy WAF tud kezelni, a forgalom elvesztését eredményezheti. A manuális mód megadása esetén kötelező megadni a példányszámot. A példányok száma 1 és 125 példány között lehet.

## <a name="autoscaling-and-high-availability"></a>Automatikus skálázás és magas rendelkezésre állás

Az Azure Application Gateway-átjárók mindig egy kiválóan elérhető módon vannak üzembe helyezve. A szolgáltatás több, konfiguráltként létrehozott példányból áll (ha az automatikus skálázás le van tiltva), vagy az alkalmazás terhelése miatt szükséges (ha az automatikus skálázás engedélyezve van). Vegye figyelembe, hogy a felhasználó szemszögéből nem feltétlenül látja el az egyes példányokat, hanem csak az Application Gateway szolgáltatás egészére. Ha egy adott példány problémába ütközik, és a működése leáll, az Azure Application Gateway transzparens módon létrehoz egy új példányt.

Vegye figyelembe, hogy még akkor is, ha az automatikus skálázást nulla minimális példányokkal konfigurálja, a szolgáltatás továbbra is elérhető lesz, ami mindig a rögzített ár részét képezi.

Egy új példány létrehozása azonban hosszabb időt is igénybe vehet (körülbelül hat vagy hét perc). Ezért ha nem szeretné megbirkózni ezzel az állásidővel, beállíthatja a minimális példányszámot (2), ideális esetben a rendelkezésre állási zónák támogatásával. Így a szokásos körülmények között legalább két példányban kell lennie az Azure-Application Gateway belül, így ha az egyik probléma a másikkal próbálkozik, akkor az új példány létrehozásakor megpróbál megbirkózni a forgalommal. Vegye figyelembe, hogy az Azure Application Gateway-példányok körülbelül 10 kapacitást tudnak támogatni, így attól függően, hogy mekkora forgalomra van szüksége, a minimális példány automatikus skálázási beállítását a 2 értéknél nagyobb értékre kell beállítania.

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
| WAF egyéni szabályok                                  |          | &#x2713; |
| Transport Layer Security (TLS)/Secure-szoftvercsatorna (SSL) leállítása            | &#x2713; | &#x2713; |
| Végpontok közötti TLS-titkosítás                         | &#x2713; | &#x2713; |
| Munkamenet-affinitás                                  | &#x2713; | &#x2713; |
| Egyéni hibalapok                                | &#x2713; | &#x2713; |
| WebSocket támogatás                                 | &#x2713; | &#x2713; |
| HTTP/2-támogatás                                    | &#x2713; | &#x2713; |
| Kapcsolatkiürítés                               | &#x2713; | &#x2713; |

> [!NOTE]
> Az automatikus skálázási v2 SKU mostantól támogatja az [alapértelmezett állapot](application-gateway-probe-overview.md#default-health-probe) -ellenőrzéseket, hogy automatikusan figyelje a háttér-készlet összes erőforrásának állapotát, és kiemelje azokat a háttérbeli tagokat, amelyek nem megfelelőnek minősülnek. Az alapértelmezett állapot-mintavétel automatikusan konfigurálva van olyan backendekhez, amelyek nem rendelkeznek egyéni mintavételi konfigurációval. További információért lásd: [az Application Gateway Health-szondái](application-gateway-probe-overview.md).

## <a name="differences-from-v1-sku"></a>A v1 SKU-tól származó különbségek

Ez a szakasz a v1 SKU-ból eltérő v2 SKU szolgáltatásait és korlátozásait ismerteti.

|Különbség|Részletek|
|--|--|
|Hitelesítési tanúsítvány|Nem támogatott.<br>További információ: [a végpontok és a végpontok közötti TLS áttekintése Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Standard_v2 és standard Application Gateway összekeverése ugyanazon az alhálózaton|Nem támogatott|
|Felhasználó által megadott útvonal (UDR) Application Gateway alhálózaton|Támogatott (konkrét forgatókönyvek). Előzetes verzióban.<br> További információ a támogatott forgatókönyvekről: [Application Gateway konfiguráció áttekintése](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).|
|NSG a bejövő porttartomány| -65200 – 65535 Standard_v2 SKU-hoz<br>-65503 – 65534 a standard SKU-hoz.<br>További információt a [Gyakori kérdések](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)című témakörben talál.|
|Teljesítménynaplók az Azure diagnosticsban|Nem támogatott.<br>Az Azure-metrikákat kell használni.|
|Számlázás|Számlázás ütemezve, hogy 2019. július 1-jén induljon el.|
|FIPS mód|Ezek jelenleg nem támogatottak.|
|Csak ILB mód|Ez jelenleg nem támogatott. A nyilvános és a ILB mód együttes használata támogatott.|
|NET Watcher-integráció|Nem támogatott.|
|Azure Security Center – integráció|Még nem érhető el.

## <a name="migrate-from-v1-to-v2"></a>Migrálás az 1-esről a 2-es verzióra

Azure PowerShell parancsfájl a PowerShell-galériában érhető el, hogy segítséget nyújtson a v1 Application Gateway/WAF a v2 automatikus skálázási SKU-ba való átálláshoz. Ez a szkript segítséget nyújt a konfigurációnak a v1-átjáróról történő másolásához. A forgalom áttelepítése továbbra is az Ön felelőssége. További információ: [Azure Application Gateway migrálása v1-ről v2-re](migrate-v1-v2.md).

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Webes forgalom irányítása az Azure Application Gatewayjel – Azure Portal](quick-create-portal.md)
- [Hozzon létre egy automatikus skálázást, a Zone redundáns Application Gateway-t egy fenntartott virtuális IP-címmel a Azure PowerShell használatával](tutorial-autoscale-ps.md)
- További információ a [Application Gatewayról](overview.md).
- További információ a [Azure Firewallról](../firewall/overview.md).
