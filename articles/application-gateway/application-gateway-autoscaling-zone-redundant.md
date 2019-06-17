---
title: Automatikus skálázás és zónaredundáns az Application Gateway v2
description: Ez a cikk bemutatja, az Azure Application Standard_v2 és WAF_v2 SKU, az automatikus skálázás és zónaredundáns szolgáltatást tartalmaz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/13/2019
ms.author: victorh
ms.openlocfilehash: 7cf6b4984f3941da3b2cd0e4eada5eb1d87f2b01
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054741"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatikus skálázás és zónaredundáns az Application Gateway v2 

Az Application Gateway és a webalkalmazási tűzfal (WAF) egy Standard_v2 és WAF_v2 Termékváltozat alatt is elérhetők. A v2 szintű Termékváltozatot kínál a nagyobb teljesítmény és támogatást nyújt a kritikus fontosságú új funkciók, például az automatikus skálázás, a zone redudancy és statikus virtuális IP-címek támogatása. A Standard és a WAF Termékváltozat a meglévő szolgáltatások továbbra is támogatottak az új v2 termékváltozatban szereplő néhány kivételtől eltekintve [összehasonlító](#differences-with-v1-sku) szakaszban.

Az új v2 szintű Termékváltozatot a következő fejlesztéseket tartalmazza:

- **Az automatikus skálázás**: Az automatikus skálázás Termékváltozat az Application Gateway vagy WAF üzembe helyezésekre alapuló forgalmi terhelés minták módosítása vagy is méretezhetők. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. Ez a Termékváltozat valódi rugalmasságot nyújt. A Standard_v2 és WAF_v2 Termékváltozat az Application Gateway rögzített kapacitás (az automatikus skálázás le van tiltva) és az automatikus skálázás engedélyezése mellett módban is működhet. Rögzített kapacitás mód hasznos forgatókönyvek egységes és kiszámítható számítási feladatokkal. Az automatikus skálázási mód akkor előnyös az alkalmazások, amelyeket az alkalmazás forgalmának eltérés.
- **A redundancia zóna**: Egy Application Gateway vagy a waf-védelem is kiterjedhetnek több rendelkezésre állási zónában, eltávolítás, minden zónában egy Traffic Manager külön Application Gateway-példány üzembe kell. Választhat egy zóna vagy több zónában, az Application Gateway-példány telepítve vannak, ami lehetővé teszi az rugalmasabb zóna hibáira. A háttérkészlet alkalmazások hasonló módon – szét lehetnek osztva a rendelkezésre állási zónák.

  A Zone redudancy érhető el csak ha Azure-beli zónák rendelkezésre állnak. Más régiókban minden más szolgáltatások támogatottak. További információkért lásd: [Mik a rendelkezésre állási zónák az Azure-ban?](../availability-zones/az-overview.md#services-support-by-region)
- **Statikus virtuális IP-CÍMEK**: Application gateway v2 Termékváltozat támogatja a statikus virtuális IP-cím kizárólag írja be. Ez biztosítja, hogy az application gateway társított virtuális IP-CÍMEK a telepítéshez, akár az újraindítás után élettartama nem változik.
- **Fejléc Újraírási**: Az Application Gateway lehetővé teszi hozzáadása, eltávolítása vagy frissítse a HTTP-kérelmek és válaszfejlécek v2 szintű Termékváltozatot. További információkért lásd: [Újraírási HTTP-fejlécek az Application Gateway segítségével](rewrite-http-headers.md)
- **Key Vault-integráció (előzetes verzió)** : Application Gateway v2 HTTPS-kompatibilis figyelői csatolt kiszolgálói tanúsítványokat a Key Vault integration (a nyilvános előzetes verzió) támogatja. További információkért lásd: [Key Vault tanúsítványokkal rendelkező SSL-lezárást](key-vault-certs.md).
- **Az Azure Kubernetes Service Bejövőforgalom-vezérlőt (előzetes verzió)** : Az Application Gateway v2 Bejövőforgalom-vezérlőjéhez lehetővé teszi, hogy az Azure Application Gateway használható a bejövő forgalom számára az Azure Kubernetes Service (AKS) néven az AKS-fürtöt. További információkért lásd: a [dokumentációs oldalon](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Nagyobb teljesítmény**: A v2 Termékváltozatot kínál legfeljebb 5 X nagyobb SSL kiszervezheti a teljesítmény, mint a Standard és a WAF Termékváltozat a korábban megszokott.
- **Központi telepítési és frissítési gyorsabb** a v2 szintű Termékváltozatot biztosít, mint a korábban megszokott Standard/WAF Termékváltozatban gyorsabb üzembe helyezési és frissítési idő. Ez a WAF konfigurációmódosításokat is tartalmaz.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Támogatott régiók

A Standard_v2 és WAF_v2 Termékváltozat az alábbi régiókban érhető el: USA északi középső RÉGIÓJA, USA déli középső RÉGIÓJA, USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA 2, USA keleti RÉGIÓJA, USA keleti RÉGIÓJA 2, USA középső RÉGIÓJA, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Franciaország, közép-India, Egyesült Királyság nyugati, kelet-japán, Nyugat-japán. További régiók felveszi a jövőben.

## <a name="pricing"></a>Díjszabás

A v2 termékváltozatú a díjszabási modell határozzák meg a felhasználás, és már nincs csatolva a példányok számát és méretét. A v2 a Termékváltozat díjszabása két összetevőből áll:

- **Rögzített ár** – Ez az óránként (vagy a megkezdett órák) Standard_v2 vagy WAF_v2 átjáró kiépítése árát.
- **A kapacitás Egységár** – Ez a fogyasztás alapú költségeit, amelyet az állandó költség mellett díjat számítunk fel. Kapacitásegység díj is számított óránkénti vagy részleges óránként. Kapacitásegység három dimenziókat, – a számítási egység, állandó kapcsolat és átviteli sebességet. A számítási egység a processzor kapacitásának felhasznált mérték. Számítási egység befolyásoló tényezők TLS kapcsolatok/mp-ben, az URL Rewrite számítások és WAF szabály feldolgozása. Állandó kapcsolatot létrehozott TCP-kapcsolatot az application gateway mértékegysége szerepel egy adott elszámolási időszakban. Átviteli sebesség átlagos megabit/mp dolgozza fel a rendszer az egy adott elszámolási időszakban.

Egy kapacitás elszámolási egység legfeljebb áll: 1 számítási egység, vagy állandó 2500-kapcsolatokat, vagy 2.22 MB/s átviteli sebesség.

Számítási egység útmutatást:

- **Standard_v2** – egyes számítási egység körülbelül 50 kapcsolatok másodpercenként az RSA 2048 bites kulcs TLS-tanúsítvány alkalmas állapotban.
- **WAF_v2** – egyes számítási egység támogathat körülbelül 10 egyidejű kérelmek / másodperc, a forgalom 70 %-os 70 – 30 % vegyesen kérelmek kisebb, mint 2 KB GET/közzététele és a fennmaradó magasabb. WAF teljesítményét nem befolyásolja válasz mérete jelenleg.

> [!NOTE]
> Minden példány jelenleg képes támogatni az körülbelül 10 kapacitásegységek.
> Képes kezelni a számítási egység kérések száma attól függ, például a TLS-tanúsítvány kulcsának mérete, a kulcscsere-algoritmus, a fejléc újraírások, és a WAF bejövő kérelem méret esetén különféle feltételek. Azt javasoljuk, hogy a számítási egység kérelmek arányának meghatározása az alkalmazás az egységteszteket végezhet. Kapacitás és a számítási egység lesz elérhető metrikaként számlázási indítása előtt.

Az alábbi táblázatban látható példa az árak, és csak illusztrációs célokat szolgálnak.

**Az USA keleti régiójában díjszabás**:

|              SKU-név                             | Rögzített díj (USD/óra)  | A kapacitás Egységár ($/ CU-óra)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Díjszabási információkért tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/application-gateway/). A számlázás a 2019. július 1. történő futásra van ütemezve.

**1. példa**

Egy Application Gateway Standard_v2 nélkül szeretné kiosztani az automatikus skálázás manuális skálázásához módban a rögzített öt példányok kapacitását.

Rögzített ár = 744(hours) * 0,20 $ $148.8 = <br>
Kapacitásegységek = 744 (óra) * példányonként 10 kapacitás egység * 5-példánnyal * $0.008 kapacitás egység óránként $297.6 =

Teljes ár = $148.8 + 297.6 $ $446.4 =

**2. példa**

Egy Application Gateway standard_v2 ki van építve, egy hónapig, és ebben az időszakban 25 új SSL kapcsolatok másodpercenkénti átlagos 8.88 MB/s adatátviteli kap. Ha kapcsolatok rövid életűek, az ára lesz:

Rögzített ár = 744(hours) * 0,20 $ $148.8 =

Kapacitás egységár = 744(hours) * Max (25/50 számítási egység a kapcsolatok másodpercenként, 8.88/2.22 kapacitásegységet átviteli sebességet) * $0.008 = 744 * 4 * 0.008 $23.81 =

Teljes ár $148 =. 23.81 8 + = $172.61

> [!NOTE]
> A Max függvény értékek párjai a legnagyobb értéket adja vissza.

**3. példa**

Egy Application Gateway WAF_v2 egy hónapig van kiépítve. Ebben az időszakban 25 új SSL kapcsolatok másodpercenkénti átlagos 8.88 MB/s adatátviteli kap, és másodpercenként 80 kérelem does. Feltéve, hogy kapcsolatok rövid életűek, és, hogy a számítási egység számítási az alkalmazás támogatja a 10 RPS számítási egység, az ár lenne:

Rögzített ár = 744(hours) * 0.36 $ $267.84 =

Kapacitás egységár = 744(hours) * Max (számítási egység Max(25/50 for connections/sec, 80/10 WAF RPS), átviteli sebességet 8.88/2.22 kapacitásegységgel) * $0.0144 = 744 * 8 * 0.0144 $85.71 =

Teljes ár = $267.84 + 85.71 $ $353.55 =

> [!NOTE]
> A Max függvény értékek párjai a legnagyobb értéket adja vissza.

## <a name="scaling-application-gateway-and-waf-v2"></a>Az Application Gateway és a WAF v2 méretezése

Az Application Gateway és a WAF konfigurálható a méretezési csoport két módban:

- **Az automatikus skálázás** – és az automatikus skálázás engedélyezve van, az Application Gateway és a WAF v2 termékváltozatok kisebbre vagy nagyobbra méretezhetők a alkalmazás forgalom követelményeknek megfelelően. Ebben a módban az alkalmazás nagyobb rugalmasságot biztosít, és szükségtelenné teszi a dolgát, az application gateway méretét vagy példányszámot. Ebben a módban is lehetővé teszi csökkenthetők a költségek átjárók futtatását a maximális kiosztott kapacitás várható maximális forgalmi terhelés nem igényli. Ügyfeleknek meg kell adnia egy minimális és igény szerint maximális példányszámot. Kapacitásérték-minimumot biztosítja, hogy az Application Gateway és a WAF v2-es nem eshet a megadott, még akkor is érhető el a forgalmat a példányok minimális száma. A minimális kapacitás, még akkor is érhető el a forgalmat, kell fizetnie. Opcionálisan megadhatja a példányok maximális száma, amely biztosítja, hogy az Application Gateway meghaladja a megadott számú példányok nem méretezhető. Számlázunk az átjáró által kiszolgált forgalom mennyiségének fogja folytatni. A példányok számát 125 0 terjedhet. Az alapértelmezett érték a példányok maximális száma 20 Ha nincs megadva.
- **Manuális** – másik lehetőségként kiválaszthatja a manuális módban, ahol az átjáró nem fog az automatikus méretezés. Ebben a módban Mi az Application Gateway vagy WAF képes kezelni, mint a további adatforgalom esetén azt eredményezheti forgalom adatvesztés. A manuális mód példányszám megadása kötelező. Példányok száma 1 eltérőek lehetnek a 125-példányokhoz.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Termékváltozat v1 és v2 szintű Termékváltozatot közötti funkcióinak összehasonlítása

Az alábbi táblázat az egyes Termékváltozat szolgáltatásait hasonlítja össze.

|                                                   | V1 Termékváltozatot   | v2 szintű Termékváltozatot   |
| ------------------------------------------------- | -------- | -------- |
| Automatikus skálázás                                       |          | &#x2713; |
| A Zone redudancy                                   |          | &#x2713; |
| Statikus virtuális IP-cím                                        |          | &#x2713; |
| Az Azure Kubernetes Service (AKS) Bejövőforgalom-vezérlőt |          | &#x2713; |
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
> A Termékváltozat már támogatja az automatikus skálázás v2 [alapértelmezett állapotadat-mintavételek](application-gateway-probe-overview.md#default-health-probe) automatikusan a háttér-készletben található összes erőforrás állapotának figyelésére, és jelölje ki a háttérrendszer a tagokkal, amelyek nem megfelelő állapotú tekinthetők. Az alapértelmezett állapotadat-mintavétel konfigurálása automatikusan történik a háttérrendszerek, amelyek nem rendelkeznek, egyéni mintavétel konfigurációra. További tudnivalókért lásd: [állapotadat-mintavételek az application gatewayben](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Különbségek a v1 Termékváltozatot

|Különbség|Részletek|
|--|--|
|Hitelesítési tanúsítvány|Nem támogatott.<br>További információkért lásd: [az Application Gateway teljes körű SSL áttekintése](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Standard_v2 és a Standard Application Gateway keverve ugyanazon az alhálózaton|Nem támogatott|
|Felhasználó által megadott útvonal (UDR) az Application Gateway-alhálózat|Nem támogatott|
|NSG bejövő port-tartomány| -65200 – 65535 Standard_v2 Termékváltozat<br>-65503 65534 Standard Termékváltozat esetében.<br>További információkért lásd: a [– gyakori kérdések](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Teljesítmény az Azure-beli diagnosztikai naplók|Nem támogatott.<br>Az Azure-metrikák kell használni.|
|Számlázás|A számlázás a tervek szerint indítsa el a 2019. július 1.|
|FIPS-módban|Ezek jelenleg nem támogatottak.|
|ILB mód|Ez jelenleg nem támogatott. Nyilvános és ILB mód együtt használható.|
|Netwatcher integráció|Nem támogatott.|
|Az Azure Security Center-integráció|Még nem érhető el.

## <a name="migrate-from-v1-to-v2"></a>Migrálás az 1-esről a 2-es verzióra

Az Azure PowerShell-parancsfájlt, amelyik segítségére lehet a a v1 az Application Gateway/WAF Termékváltozat v2 automatikus méretezése a PowerShell-galériából érhető el. Ez a szkript segít a v1-átjárón a konfigurációs másolja. Forgalom az áttelepítés akkor továbbra is Ön felelőssége. További információkért lásd: [áttelepítése Azure Application Gateway v1, v2](migrate-v1-v2.md).

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: A közvetlen webes forgalom az Azure Application Gatewayjel – Azure portal](quick-create-portal.md)
- [Az automatikus skálázás, a zóna redundáns az application gateway létrehozása foglalt virtuális IP-címmel rendelkező Azure PowerShell-lel](tutorial-autoscale-ps.md)
- Tudjon meg többet [Application Gateway](overview.md).
- Tudjon meg többet [Azure tűzfal](../firewall/overview.md).
