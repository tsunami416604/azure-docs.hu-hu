---
title: Az Azure virtuális hálózati nat-kapcsolatának hibái
titleSuffix: Azure Virtual Network
description: A virtuális hálózati hálózati hálózati módszerrel kapcsolatos problémák elhárítása.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 4a273801290a0a5833ebd83983a8b6b0ad856b45
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79408484"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Az Azure virtuális hálózati nat-kapcsolatának hibái

Ez a cikk segít a rendszergazdáknak diagnosztizálni és megoldani a csatlakozási problémákat a virtuális hálózati hálózati címzet használatakor.

## <a name="problems"></a>Problémák

* [SNAT kimerültség](#snat-exhaustion)
* [Az ICMP pingelése sikertelen](#icmp-ping-is-failing)
* [Kapcsolódási hibák](#connectivity-failures)
* [IPv6 együttélés](#ipv6-coexistence)

A problémák megoldásához kövesse az alábbi szakaszlépéseit.

## <a name="resolution"></a>Megoldás:

### <a name="snat-exhaustion"></a>SNAT kimerültség

Egyetlen [NAT átjáró erőforrás](nat-gateway-resource.md) 64 000-től 1 millió egyidejű folyamatig támogatja.  Minden IP-cím 64 000 SNAT-portot biztosít a rendelkezésre álló készlethez. NAT-átjáró-erőforrásonként legfeljebb 16 IP-címet használhat.  A SNAT mechanizmus [itt](nat-gateway-resource.md#source-network-address-translation) részletesebben ismertetjük.

Az SNAT-kimerültség kiváltó oka gyakran a kimenő kapcsolat létrehozásának és kezelésének antimintája.  Alaposan olvassa át ezt a szakaszt.

#### <a name="steps"></a>Lépések

1. Vizsgálja meg, hogy az alkalmazás hogyan hoz létre kimenő kapcsolatot (például kódellenőrzés vagy csomagrögzítés). 
2. Határozza meg, hogy ez a tevékenység várható viselkedés-e, vagy hogy az alkalmazás helytelenül viselkedik-e.  Az Azure Monitor [metrikák használatával](nat-metrics.md) támaszthatja alá a megállapításokat. Használja a "Sikertelen" kategóriát az SNAT-kapcsolatok metrikához.
3. Értékelje, hogy követi-e a megfelelő mintákat.
4. Értékelje ki, hogy az SNAT-port kimerülése mérsékelni kell-e a NAT-átjáró-erőforráshoz rendelt további IP-címekkel.

#### <a name="design-patterns"></a>Tervezési minták

Amikor csak lehetséges, mindig használja ki a kapcsolat újrafelhasználását és a csatlakoztatáskészletezését.  Ezek a minták elkerülik az erőforrások kimerülési problémáit, és kiszámítható viselkedést eredményeznek. Ezeknek a mintáknak a primitívjei számos fejlesztői könyvtárban és keretrendszerben találhatók.

_**Megoldás:**_ Használja a megfelelő mintákat és ajánlott eljárásokat

- Az atomi kérelmek (kapcsolatonként egy kérelem) nem megfelelő tervezési választás. Az ilyen anti-pattern korlátozza a skálát, csökkenti a teljesítményt és csökkenti a megbízhatóságot. Ehelyett használja fel újra a HTTP/S-kapcsolatokat a kapcsolatok és a kapcsolódó SNAT-portok számának csökkentése érdekében. Az alkalmazás léptéke a csökkentett kézfogások, a terhelésés és a kriptográfiai működési költségek miatt javul ni fog a TLS használatakor.
- A DNS számos egyéni folyamatot vezethet be a hangerőn, ha az ügyfél nem gyorsítótárazja a DNS-feloldók eredményét. Használja a gyorsítótárazást.
- UDP-folyamatok (például DNS-keresések) snat-portokat foglalnak le az alapjárati időtúltöltés idejére. Minél hosszabb az tétlen időtúljárat, annál nagyobb a nyomás az SNAT-portokra. Használjon rövid tétlen időoutot (például 4 percet).
- A kapcsolatkötet kialakításához használjon kapcsolatkészleteket.
- Soha ne hagyjon el csendesen egy TCP-folyamatot, és ne hagyatkozzon a TCP időzítőkre a folyamat megtisztításához. Így a köztes rendszerekben és végpontokon lefoglalt állapot marad, és a portok nem érhetők el más kapcsolatok hoz. Ez alkalmazáshibákat és SNAT-kimerültséget okozhat. 
- A TCP-vel kapcsolatos időzítőértékeket nem szabad megváltoztatni a hatás szakértői ismerete nélkül. Bár a TCP helyreáll, az alkalmazás teljesítményét negatívan befolyásolhatja, ha a kapcsolat végpontjai nem egyeznek az elvárásokkal. A vágy, hogy változtatni időzítő általában annak a jele, egy mögöttes tervezési probléma. Tekintse át a következő ajánlásokat.

Gyakran alkalommal SNAT kimerültség is felerősíthető más anti-minták a mögöttes alkalmazás. Tekintse át ezeket a további mintákat és gyakorlati tanácsokat a szolgáltatás méretének és megbízhatóságának javítása érdekében.

- Fontolja meg a szinkron [lekérdezési minták](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) hosszú ideig futó műveletek számára, hogy más műveletek kapcsolati erőforrásait szabadítsa fel.
- A hosszú élettartamú folyamatoknak (például az újrafelhasznált TCP-kapcsolatoknak) tcp keepalives vagy alkalmazásréteg-megtartási élettartamot kell használniuk a köztes rendszerek időtúllépésének elkerülése érdekében. Az alapjárati időtúltöltés növelése végső megoldás, és előfordulhat, hogy nem oldja meg a kiváltó okot. A hosszú időmeghosszabbítás alacsony sebességhibákat okozhat, amikor lejár az idő-túlóra, és késleltetést és szükségtelen hibákat vezethet be.
- Kecses [újrapróbálkozási mintákat](https://docs.microsoft.com/azure/architecture/patterns/retry) kell használni, hogy elkerüljék az agresszív újrapróbálkozások/bursts átmeneti hiba vagy hiba helyreállítása során.
Új TCP-kapcsolat létrehozása minden HTTP-művelethez (más néven "atomi kapcsolatok") egy anti-minta.  Az atomi kapcsolatok megakadályozzák, hogy az alkalmazás jól skálázható és erőforrások at pazarolja.  Mindig több műveletet kell ugyanabba a kapcsolatba mártasz.  Az alkalmazás a tranzakció sebességének és az erőforrásköltségeknek kedvez.  Ha az alkalmazás átviteli réteg titkosítást (például TLS) használ, jelentős költség jár az új kapcsolatok feldolgozásához.  Tekintse át az [Azure Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns/) további ajánlott eljárásminták.

#### <a name="additional-possible-mitigations"></a>További lehetséges megoldások

_**Megoldás:**_ A kimenő kapcsolatokat az alábbiak szerint kell felskálázni:

| Forgatókönyv | Bizonyíték |Kezelés |
|---|---|---|
| A SNAT-portok és az SNAT-portok kimerülése versengést tapasztal a magas használatú időszakokban. | Az Azure Monitor SNAT-kapcsolatok [metrikája](nat-metrics.md) "Sikertelen" kategória átmeneti vagy állandó hibákat és nagy kapcsolati hangerőt jelenít meg.  | Határozza meg, hogy hozzáadhat-e további nyilvános IP-cím-erőforrásokat vagy nyilvános IP-előtag-erőforrásokat. Ez a kiegészítés összesen legfeljebb 16 IP-címet tesz lehetővé a NAT-átjáróhoz. Ez a kiegészítés több készletet biztosít a rendelkezésre álló SNAT-portokhoz (IP-címenként 64 000), és lehetővé teszi a forgatókönyv további méretezését.|
| Már 16 IP-címet adott meg, és még mindig fennáll az SNAT-port kimerülése. | A további IP-cím hozzáadására tett kísérlet sikertelen. A nyilvános IP-címerőforrásokból vagy nyilvános IP-előtag-erőforrásokból származó IP-címek száma meghaladja az összes encikó 16-ot. | Ossza el az alkalmazáskörnyezetet több alhálózat között, és biztosítson egy NAT-átjáró-erőforrást minden alhálózathoz.  Értékelje újra a tervezési mintázat(oka)t, hogy az előző útmutatás alapján [optimalizáljon.](#design-patterns) |

>[!NOTE]
>Fontos megérteni, hogy miért snat kimerültség fordul elő. Győződjön meg arról, hogy a megfelelő mintákat használja a méretezhető és megbízható forgatókönyvekhez.  Ha további SNAT-portokat ad hozzá egy forgatókönyvhöz anélkül, hogy megértenék az igény okát, az a legvégső megoldásnak kell lennie. Ha nem érti, hogy a forgatókönyv miért gyakorol nyomást az SNAT-portkészletre, ha további IP-címek hozzáadásával további SNAT-portokat ad hozzá a készlethez, az csak késlelteti az alkalmazás méretezésével azonos kimerülési hibát.  Lehet, hogy elfedi más hatékonysági és anti-minták.

### <a name="icmp-ping-is-failing"></a>Az ICMP pingelése sikertelen

[A virtuális hálózati hálózati címzés](nat-overview.md) támogatja az IPv4 UDP és TCP protokollokat. IcMP nem támogatott, és várhatóan nem sikerül.  

_**Megoldás:**_ Ehelyett használja a TCP-kapcsolati teszteket (például "TCP ping") és az UDP-specifikus alkalmazásréteg-teszteket a végpontok közötti kapcsolat érvényesítéséhez.

Az alábbi táblázat egy kiindulási pontot használható, amelyhez a tesztek indításához használt eszközök használhatók.

| Operációs rendszer | Általános TCP-kapcsolatteszt | TCP alkalmazásréteg-vizsgálat | UDP |
|---|---|---|---|
| Linux | nc (általános csatlakozási teszt) | göndör (TCP alkalmazási réteg teszt) | alkalmazásspecifikus |
| Windows | [Psping között](https://docs.microsoft.com/sysinternals/downloads/psping) | [PowerShell-meghívás-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | alkalmazásspecifikus |

### <a name="connectivity-failures"></a>Kapcsolódási hibák

Connectivity issues with [Virtual Network NAT](nat-overview.md) can be caused by several different issues:

* a NAT átjáró átmeneti vagy tartós [SNAT-kimerülése,](#snat-exhaustion)
* átmeneti hibák az Azure-infrastruktúrában, 
* átmeneti hibák az Azure és a nyilvános internetes célállomás közötti útvonalon, 
* átmeneti vagy tartós hibák a nyilvános internetes célhelyen.

Az alábbihoz hasonló eszközöket használhat az érvényesítési kapcsolathoz. [Az ICMP pingje nem támogatott.](#icmp-ping-is-failing)

| Operációs rendszer | Általános TCP-kapcsolatteszt | TCP alkalmazásréteg-vizsgálat | UDP |
|---|---|---|---|
| Linux | nc (általános csatlakozási teszt) | göndör (TCP alkalmazási réteg teszt) | alkalmazásspecifikus |
| Windows | [Psping között](https://docs.microsoft.com/sysinternals/downloads/psping) | [PowerShell-meghívás-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | alkalmazásspecifikus |

#### <a name="snat-exhaustion"></a>SNAT kimerültség

Tekintse át a [SNAT-kimerültségről](#snat-exhaustion) szóló részt ebben a cikkben.

#### <a name="azure-infrastructure"></a>Azure-infrastruktúra

Az Azure nagy gonddal figyeli és üzemelteti infrastruktúráját. Átmeneti hibák fordulhatnak elő, nincs garancia arra, hogy az átvitelek veszteségmentesek.  Használjon olyan tervezési mintákat, amelyek lehetővé teszik a SYN újraküldését a TCP-alkalmazásokhoz. Használja a kapcsolat időtúlárait, amelyek elég nagyok ahhoz, hogy lehetővé tegyék a TCP SYN újraközvetítést az elveszett SYN-csomagok által okozott átmeneti hatások csökkentése érdekében.

_**Megoldás:**_

* Ellenőrizze a [SNAT kimerültség](#snat-exhaustion).
* A SYN újraadási viselkedését szabályozó TCP-verem konfigurációs paraméterét RTO[(Újraküldési idő-out) nevezik.](https://tools.ietf.org/html/rfc793) Az RTO-érték állítható, de alapértelmezés szerint általában 1 másodperc vagy magasabb, exponenciális visszalépéssel.  Ha az alkalmazás kapcsolati időtúl-túl rövid (például 1 másodperc), előfordulhat, hogy szórványos kapcsolat időtúlórák.  Növelje az alkalmazáskapcsolat időtúllépését.
* Ha hosszabb, nem várt időkitöltéseket észlel az alapértelmezett alkalmazásviselkedéssel, nyisson meg egy támogatási esetet a további hibaelhárításhoz.

Nem javasoljuk a TCP-kapcsolat időtúllépésének mesterséges csökkentését vagy az RTO-paraméter finomhangolását.

#### <a name="public-internet-transit"></a>Tömegközlekedés

Az átmeneti hibák esélye nő a célhoz vezető hosszabb úttal és több köztes rendszerrel. Az átmeneti hibák várhatóan növekedhetnek az [Azure-infrastruktúrán](#azure-infrastructure)keresztül. 

Kövesse ugyanazt az útmutatást, mint az előző [Azure-infrastruktúra](#azure-infrastructure) szakasz.

#### <a name="internet-endpoint"></a>Internet-végpont

Az előző szakaszok érvényesek, valamint az internet-végpont, amely a kommunikáció jön létre. Egyéb tényezők, amelyek hatással lehetnek a kapcsolat sikerére:

* forgalomirányítás a rendeltetési oldalon, beleértve a
- A céloldal által előírt API-sebességkorlátozás
- Volumetrikus DDoS mérséklések vagy szállítási réteg forgalom alakításában
* tűzfal vagy más összetevő a célhelyen 

Általában csomag rögzítése a forrásnál, és a cél (ha rendelkezésre áll) szükséges meghatározni, hogy mi történik.

_**Megoldás:**_

* Ellenőrizze a [SNAT kimerültség](#snat-exhaustion). 
* Összehasonlításképpen ellenőrizze a végponthoz való kapcsolódást ugyanabban a régióban vagy máshol.  
* Ha nagy volumenű vagy tranzakciós díjú tesztelést hoz létre, vizsgálja meg, hogy a sebesség csökkentése csökkenti-e a hibák előfordulását.
* Ha a sebesség módosítása hatással van a hibák arányára, ellenőrizze, hogy elérte-e az API-sebességkorlátokat vagy a céloldalon lévő egyéb korlátozásokat.
* Ha a vizsgálat nem meggyőző, nyisson meg egy támogatási esetet további hibaelhárításcéljából.

#### <a name="tcp-resets-received"></a>Fogadott TCP-alaphelyzetbe állítás

A NAT-átjáró tcp-visszaállításokat hoz létre a forrás virtuális gépen a folyamatban lévőként fel nem ismert forgalom hoz létre.

Ennek egyik lehetséges oka, hogy a TCP-kapcsolat tétlen időtúllépés miatt megszakadt.  Az tétlen időout 4 perctől 120 percig állítható.

A TCP-alapbeállítások nem a NAT-átjáró erőforrások nyilvános oldalán jönnek létre. A céloldalon a TCP-alaphelyzetbe állítást a forrásvirtuális gép hozza létre, nem a NAT átjáró erőforrás.

_**Megoldás:**_

* Tekintse át [a tervezési mintákra](#design-patterns) vonatkozó ajánlásokat.  
* Szükség esetén nyisson meg egy támogatási esetet további hibaelhárításcéljából.

### <a name="ipv6-coexistence"></a>IPv6 együttélés

[A virtuális hálózati hálózati címcím](nat-overview.md) támogatja az IPv4 UDP- és TCP protokollokat, és [az IPv6-előtaggal rendelkező alhálózaton történő](nat-overview.md#limitations)telepítés nem támogatott.

_**Megoldás:**_ NAT-átjáró telepítése iPv6-előtag nélküli alhálózaton.

A [virtual network nat uservoice](https://aka.ms/natuservoice)-on keresztül jelezheti a további képességek iránti érdeklődést.

## <a name="next-steps"></a>További lépések

* További információ a [virtuális hálózati hálózati hálózati kapcsolatról](nat-overview.md)
* Ismerje meg ab Fry out [NAT átjáró erőforrás](nat-gateway-resource.md)
* Ismerje meg [a NAT-átjáró-erőforrások metrikáit és riasztásait.](nat-metrics.md)
* [Mondja el, mit kell építeni a következő virtuális hálózati NAT a UserVoice](https://aka.ms/natuservoice).

