---
title: Kimenő kapcsolatok
titleSuffix: Azure Load Balancer
description: Ez a cikk azt ismerteti, hogyan teszi lehetővé az Azure a virtuális gépek számára a nyilvános internetes szolgáltatásokkal való kommunikációt.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2020
ms.author: allensu
ms.openlocfilehash: d778b3ae0889ea0bf9cc38ca5813ac61fc5fcdbe
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595653"
---
# <a name="outbound-connections"></a>Kimenő kapcsolatok

Azure Load Balancer különböző mechanizmusokon keresztül biztosítja a kimenő kapcsolatokat. Ez a cikk a forgatókönyveket és azok kezelését ismerteti. 


## <a name="scenarios"></a>Forgatókönyvek

* A virtuális gép nyilvános IP-címmel rendelkezik.
* Virtuális gép nyilvános IP-cím nélkül.
* Virtuális gép nyilvános IP-cím nélkül, standard Load Balancer nélkül.

### <a name="virtual-machine-with-public-ip"></a><a name="scenario1"></a>Virtuális gép nyilvános IP-címmel

| Szövetségek | Módszer | IP-protokollok |
| ---------- | ------ | ------------ |
| Nyilvános Load Balancer vagy önálló | [SNAT (forrás hálózati címfordítás)](#snat) </br> A [Pat (port maszkolása)](#pat) nincs használatban. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (biztonsági tartalom beágyazása) |

#### <a name="description"></a>Leírás

Az Azure a példány hálózati adapterének IP-konfigurációjához hozzárendelt nyilvános IP-címet használja az összes kimenő folyamathoz. A példányhoz minden elérhető ideiglenes port tartozik. Nem számít, hogy a virtuális gép terheléselosztás alatt áll-e. Ez a forgatókönyv elsőbbséget élvez a többiekkel szemben. 

Egy virtuális géphez hozzárendelt nyilvános IP-cím 1:1-kapcsolat (nem 1: sok), és állapot nélküli 1:1 NAT-ként lett megvalósítva.

### <a name="virtual-machine-without-public-ip"></a><a name="scenario2"></a>Virtuális gép nyilvános IP-cím nélkül

| Szövetségek | Módszer | IP-protokollok |
| ------------ | ------ | ------------ |
| Nyilvános Load Balancer | A Load Balancer frontend használata a [SNAT](#snat) -hez a [Pat (port maszkolása)](#pat).| TCP </br> UDP |

#### <a name="description"></a>Leírás

A terheléselosztó erőforrása egy terheléselosztó-szabállyal van konfigurálva. Ez a szabály a nyilvános IP-frontend és a háttér-készlet közötti kapcsolat létrehozására szolgál. 

Ha nem hajtja végre ezt a szabályt, a viselkedés a 3. forgatókönyvben leírtak szerint történik. 

Az állapot-mintavétel sikerességéhez nincs szükség figyelőhöz tartozó szabályra.

Amikor egy virtuális gép létrehoz egy kimenő folyamatot, az Azure lefordítja a forrás IP-címet a nyilvános terheléselosztó előtérbeli felületének nyilvános IP-címére. Ezt a fordítást a [SNAT](#snat)-on keresztül teheti meg. 

A terheléselosztó nyilvános IP-címének ideiglenes portjai a virtuális gép által kezdeményezett egyes folyamatok megkülönböztetésére szolgálnak. A SNAT dinamikusan használja az [előlefoglalt ideiglenes portokat](#preallocatedports) a kimenő folyamatok létrehozásakor. 

Ebben a kontextusban a SNAT használt ideiglenes portok neve SNAT-portok. Az SNAT-portok előre le vannak foglalva az [alapértelmezett SNAT-portok kiosztási táblájában](#snatporttable)leírtak szerint.

### <a name="virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a>Virtuális gép nyilvános IP-cím nélkül, standard Load Balancer nélkül

| Szövetségek | Módszer | IP-protokollok |
| ------------ | ------ | ------------ |
|Nincs </br> Alapszintű Load Balancer | [SNAT](#snat) a [port maszkolásával (Pat)](#pat)| TCP </br> UDP | 

#### <a name="description"></a>Leírás

Amikor a virtuális gép létrehoz egy kimenő folyamatot, az Azure lefordítja a forrás IP-címet egy nyilvános forrás IP-címére. Ez a nyilvános IP-cím **nem konfigurálható** , és nem foglalható le. Ez a cím nem számít az előfizetés nyilvános IP-erőforrásának korlátja alapján. 

A nyilvános IP-cím fel lesz szabadítva, és új nyilvános IP-címet igényel, ha újra telepíti a következőket: 

* Virtuális gép
* Rendelkezésre állási csoport
* Virtuálisgép-méretezési csoport  

Ne használja ezt a forgatókönyvet az IP-címek engedélyezési listához való hozzáadásához. Használja az 1. vagy a 2. forgatókönyvet, ahol explicit módon deklarálja a kimenő viselkedést. Az [SNAT](#snat) -portok az [alapértelmezett SNAT-portok kiosztási táblájában](#snatporttable)leírt módon vannak lefoglalva.



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Port kiosztási algoritmusa

Az Azure algoritmus használatával határozza meg, hogy hány előre lefoglalt [SNAT](#snat) -port érhető el. Az algoritmus a háttérbeli készlet méretén alapuló portok számát alapozza. 

A terheléselosztó összes nyilvános IP-címéhez 64 000-as port [SNAT](#snat) -portként érhető el. A rendszer az UDP és a TCP esetében is megegyező számú [SNAT](#snat) -portot rendel hozzá. A portok az IP protokolltól függetlenül lesznek felhasználva. 

A [SNAT](#snat) -port használata eltérő attól függően, hogy a folyamat UDP vagy TCP. 

A portok az előlefoglalt korláttal dinamikusan lesznek felhasználva.  A portok akkor jelennek meg, amikor a folyamat bezáródik vagy üresjárati időtúllépés történik.

További információ az üresjárati időtúllépésekről: [a Azure Load Balancer kimenő kapcsolatainak hibáinak megoldása](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 

A portok csak akkor lesznek felhasználva, ha a folyamatokat egyedivé kell tenni.

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> Dinamikus SNAT portok előfoglalása

A következő táblázat a [SNAT](#snat) portok előfoglalásait mutatja be a háttérbeli készlet méreteihez:

| Készlet mérete (VM-példányok) | Az előlefoglalt SNAT-portok száma IP-konfiguráció alapján |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Előfordulhat, hogy a háttér-készlet méretének módosítása hatással lehet a meglévő folyamatokra:

- A háttérbeli készlet mérete növeli a következő szintet. Az előlefoglalt [SNAT](#snat) -portok fele a következő szintjére való áttérés során visszaigényelve lesz. 

- A visszaigényelt [SNAT](#snat) -portok időtúllépésével és bezárásával kapcsolatos folyamatok. Ezeket a folyamatokat újra kell létrehozni. Ha új folyamatra van kísérlet, a folyamat azonnal sikeres lesz, amíg az előlefoglalt portok elérhetők lesznek.

- Ha a háttér-készlet mérete csökkenti a méretet, és az átmenetek alacsonyabb szinten vannak, a rendelkezésre álló [SNAT](#snat) -portok száma növekszik. A meglévő megadott [SNAT](#snat) -portok és a hozzájuk tartozó folyamatok nem érintettek.

## <a name="outbound-rules"></a><a name="outboundrules"></a>Kimenő szabályok

 A kimenő szabályok lehetővé teszik a nyilvános [standard Load Balancer](load-balancer-standard-overview.md)kimenő hálózati címfordításának konfigurációját.  

> [!NOTE]
> Az **Azure Virtual Network NAT** kimenő kapcsolatot biztosíthat a virtuális hálózatokban található virtuális gépek számára.  További információ: [Mi az az Azure Virtual Network NAT?](../virtual-network/nat-overview.md) .

A kimenő kapcsolat teljes deklaratív vezérlése lehetővé teszi az igényeinek megfelelő méretezést és finomhangolást.

![Terheléselosztó kimenő szabályai](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

A kimenő szabályok használatával a Load Balancer segítségével megadhatja a kimenő NAT-t a semmiből. A meglévő kimenő NAT működésének méretezését és finomhangolását is elvégezheti.

A kimenő szabályok segítségével a következőket vezérelheti:

- Mely virtuális gépeket kell lefordítani a nyilvános IP-címekre.
- A kimenő [SNAT](#snat) portjainak megadnia.
- A kimenő fordítását biztosító protokollok.
- A kimenő kapcsolat üresjárati időkorlátja (4-120 perc) milyen időtartamra van használatban.
- Meg kell-e küldeni a TCP alaphelyzetbe állítását üresjárati időkorláton
- Egyetlen szabállyal rendelkező TCP és UDP átviteli protokollok

### <a name="outbound-rule-definition"></a>Kimenő szabály definíciója

A kimenő szabályok ugyanazt a megszokott szintaxist követik, mint a terheléselosztás és a bejövő NAT- **szabályok: előtér**-  +  **Paraméterek**  +  **háttér-készlete**. Egy kimenő szabály konfigurálja a kimenő NAT-t a _háttér-készlet által azonosított összes virtuális gép_ számára, hogy a rendszer lefordítsa a _felületet_.  A _Paraméterek_ további részletes szabályozást biztosítanak a kimenő NAT-algoritmushoz képest.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Kimenő NAT méretezése több IP-címmel

A frontend által biztosított további IP-címek további 64 000 ideiglenes portokat biztosítanak a terheléselosztó számára SNAT-portokként való használathoz. 

Több IP-cím használata a nagy léptékű forgatókönyvek megtervezéséhez. A kimenő szabályok használatával csökkentheti a [SNAT-kimerültséget](troubleshoot-outbound-connection.md#snatexhaust). 

A [nyilvános IP-előtagot](https://aka.ms/lbpublicipprefix) közvetlenül is használhatja egy kimenő szabállyal. 

A nyilvános IP-előtag növeli az üzemelő példány skálázását. Az előtag hozzáadható az Azure-erőforrásokból származó folyamatok engedélyezési listájához. A Load balancerben konfigurálhatja a előtérbeli IP-konfigurációt a nyilvános IP-cím előtagjaként való hivatkozáshoz.  

A terheléselosztó felügyeli a nyilvános IP-előtagot. A kimenő kapcsolatok esetében a kimeneti szabály automatikusan a nyilvános IP-előtagban található összes nyilvános IP-címet fogja használni. 

A nyilvános IP-előtagon belüli összes IP-cím egy további 64 000 ideiglenes portot biztosít IP-címként a terheléselosztó számára SNAT-portokként való használathoz.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Kimenő forgalom üresjárati időkorlátja és a TCP-visszaállítás

A kimenő szabályok egy konfigurációs paramétert biztosítanak a kimenő folyamat üresjárati időkorlátjának szabályozására, és az alkalmazás igényeinek megfelelően illeszkednek. A kimenő üresjárati időtúllépések alapértelmezett értéke 4 perc. További információ: az [üresjárati időtúllépések konfigurálása](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). 

A terheléselosztó alapértelmezett viselkedése a folyamat csendes eldobása, ha elérte a kimenő üresjárati időkorlátot. A `enableTCPReset` paraméter lehetővé teszi a kiszámítható alkalmazások viselkedését és vezérlését. A paraméter azt határozza meg, hogy a kimenő Üresjárati időkorlát időtúllépése esetén a kétirányú TCP alaphelyzetbe állítás (TCP első) legyen-e. 

Tekintse át az [Üresjárat időkorlátjának TCP-visszaállítását](https://aka.ms/lbtcpreset) , beleértve a régió rendelkezésre állását.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Kimenő kapcsolat megakadályozása

Terheléselosztási szabályok biztosítják a kimenő NAT automatikus programozását. Bizonyos forgatókönyvek esetében a kimenő NAT automatikus programozásának letiltására van szükség a terheléselosztási szabály alapján. A szabály használatával történő letiltás lehetővé teszi a viselkedés szabályozását vagy pontosítását.  

Ezt a paramétert kétféleképpen is használhatja:

1. A kimenő SNAT bejövő IP-címének megakadályozása. Tiltsa le a kimenő SNAT a terheléselosztási szabályban.
  
2. A bejövő és kimenő IP-címek kimenő [SNAT](#snat) paramétereinek hangolása egyidejűleg. Az automatikus kimenő NAT-t le kell tiltani ahhoz, hogy a kimenő szabályok szabályozni tudják a vezérlést. Ha módosítani szeretné egy SNAT-port lefoglalását is, akkor a `disableOutboundSnat` paramétert igaz értékre kell állítani. 

A kimenő szabályok konfigurálásának művelete sikertelen lesz, ha megpróbál újradefiniálni egy bejövő IP-címet.  Először tiltsa le a terheléselosztási szabály kimenő NAT-át.

>[!IMPORTANT]
> Ha a paraméter értéke TRUE (igaz), a virtuális gép nem rendelkezik kimenő kapcsolattal, és nem rendelkezik kimenő kapcsolattal.  A virtuális gép vagy az alkalmazás néhány művelete attól függ, hogy a kimenő kapcsolat elérhető-e. Győződjön meg arról, hogy tisztában van a forgatókönyv függőségeivel, és befolyásolta ennek a változásnak a hatását.

Esetenként nem kívánatos, hogy egy virtuális gép kimenő folyamatot hozzon létre. Előfordulhat, hogy egy olyan követelményt kell kezelnie, hogy mely célhelyek kapják meg a kimenő folyamatokat, vagy hogy mely célhelyek kezdenek bejövő forgalmat. [Hálózati biztonsági csoportok](../virtual-network/security-overview.md) használatával kezelheti a virtuális gép által elnyúló célhelyeket. A NSG használatával felügyelheti, hogy mely nyilvános célhelyek indítják el a bejövő folyamatokat.

Ha NSG alkalmaz egy elosztott terhelésű virtuális gépre, ügyeljen a [szolgáltatás-címkékre](../virtual-network/security-overview.md#service-tags) és az [alapértelmezett biztonsági szabályokra](../virtual-network/security-overview.md#default-security-rules). Győződjön meg arról, hogy a virtuális gép Azure Load Balancertól származó állapot-mintavételi kérelmeket tud fogadni.

Ha egy NSG letiltja az állapot-mintavételi kérelmeket a AZURE_LOADBALANCER alapértelmezett címkétől, a virtuális gép állapotának mintavétele meghiúsul, és a virtuális gép nem érhető el. Load Balancer leállítja az új folyamatok küldését a virtuális gépre.

## <a name="scenarios-with-outbound-rules"></a>Forgatókönyvek kimenő szabályokkal

### <a name="outbound-rules-scenarios"></a>Kimenő szabályok forgatókönyvei

* Konfigurálja a kimenő kapcsolatokat egy adott nyilvános IP-cím vagy előtag-készletbe.
* Módosítsa a [SNAT](#snat) -portok foglalását.
* Csak a kimenő engedélyezése.
* Kimenő NAT csak virtuális gépekhez (nincs bejövő).
* Kimenő NAT a belső standard Load Balancerhez.
* Engedélyezze mindkét TCP-& UDP protokollt a kimenő NAT-hoz egy nyilvános standard Load balancerrel.

### <a name="configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Kimenő kapcsolatok konfigurálása egy adott nyilvános IP-cím vagy előtag-készlethez

#### <a name="details"></a>Részletek

Ezzel a módszerrel a kimenő kapcsolatok a nyilvános IP-címek készletéről származnak. Nyilvános IP-címeket vagy előtagokat adhat hozzá egy engedélyezési vagy megtagadási listához a létrehozásuk alapján.

Ez a nyilvános IP-cím vagy előtag ugyanaz lehet, mint a terheléselosztási szabályok. 

Eltérő nyilvános IP-cím vagy előtag használata egy terheléselosztási szabály által használtnál:  

1. Hozzon létre nyilvános IP-előtagot vagy nyilvános IP-címet.
2. Nyilvános standard Load Balancer létrehozása 
3. Hozzon létre egy, a használni kívánt nyilvános IP-előtagra vagy nyilvános IP-címére hivatkozó előtérbeli felületet. 
4. Háttérbeli készlet újrafelhasználása vagy háttérbeli készlet létrehozása és a virtuális gépek elhelyezése a nyilvános Load Balancer háttér-készletében
5. Konfiguráljon egy kimenő szabályt a nyilvános terheléselosztó számára, hogy engedélyezze a kimenő NAT-t a virtuális gépek számára a frontend használatával. Ha nem szeretné, hogy a terheléselosztási szabályt használja a kimenő forgalomhoz, tiltsa le a kimenő SNAT a terheléselosztási szabályban.

### <a name="modify-snat-port-allocation"></a><a name="scenario2out"></a>[SNAT](#snat) -port foglalásának módosítása

#### <a name="details"></a>Részletek

A kimenő szabályok használatával beállíthatja az [automatikus SNAT-portok kiosztását a háttérbeli készlet méretétől függően](load-balancer-outbound-connections.md#preallocatedports). 

Ha SNAT kimerültséget tapasztal, növelje az alapértelmezett 1024-as számú [SNAT](#snat) -portot. 

A nyilvános IP-címek legfeljebb 64 000 ideiglenes portot vesznek részt. A háttérrendszer-készletben lévő virtuális gépek száma határozza meg az egyes virtuális gépekre terjesztett portok számát. A háttér-készlet egyik virtuális gépe fér hozzá a maximális 64 000-as porthoz. Két virtuális gép esetében legfeljebb 32 000 [SNAT](#snat) -portot lehet megadni kimenő szabállyal (2x 32 000 = 64 000). 

A kimenő szabályok használatával beállíthatja az alapértelmezés szerint megadott SNAT-portokat. Az alapértelmezett [SNAT](#snat) -portok közül több vagy kevesebb is adható meg. A kimenő szabályok egy felületéről érkező összes nyilvános IP-cím akár 64 000 ideiglenes portot is felhasználhat [SNAT](#snat) -portokként való használatra.  

A Load Balancer 8-nál több [SNAT](#snat) -portot ad meg. Ha a 8 értékkel nem osztható értéket ad meg, a rendszer elutasítja a konfigurációs műveletet. 

Ha a nyilvános IP-címek száma alapján több [SNAT](#snat) -portot próbál meg megadni, a rendszer elutasítja a konfigurációs műveletet.

Ha virtuális gépenként 10 000 portot ad meg, és a backend-készletben szereplő hét virtuális gép egyetlen nyilvános IP-címmel rendelkezik, a rendszer elutasítja a konfigurációt. A hét megszorozva a 10 000-as mérettel, amely meghaladja a 64 000-es portot. A forgatókönyv engedélyezéséhez adjon hozzá több nyilvános IP-címet a Kimenő szabály előtérbeli felületéhez. 

A portok számának megadásával visszaállíthatja az [alapértelmezett portok kiosztását](load-balancer-outbound-connections.md#preallocatedports) . Az első 50-es virtuálisgép-példány 1024 portot kap, a 51-100-as virtuálisgép-példányok pedig a maximális példányszámig 512-t kapnak.  További információ a SNAT alapértelmezett kiosztásáról: [SNAT-portok foglalási táblázata](#snatporttable).

### <a name="enable-outbound-only"></a><a name="scenario3out"></a>Csak kimenő engedélyezése

#### <a name="details"></a>Részletek

Egy nyilvános standard Load Balancer használatával biztosíthatja a kimenő NAT-t a virtuális gépek csoportjai számára. Ebben a forgatókönyvben saját maga is használhat kimenő szabályt anélkül, hogy további szabályokat kellene megadnia.

> [!NOTE]
> Az **Azure Virtual Network NAT** a terheléselosztó szükségessége nélkül biztosíthatja a kimenő kapcsolatot a virtuális gépek számára.  További információ: [Mi az az Azure Virtual Network NAT?](../virtual-network/nat-overview.md) .

### <a name="outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Kimenő NAT csak virtuális gépekhez (nincs bejövő)

> [!NOTE]
> Az **Azure Virtual Network NAT** a terheléselosztó szükségessége nélkül biztosíthatja a kimenő kapcsolatot a virtuális gépek számára.  További információ: [Mi az az Azure Virtual Network NAT?](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Részletek

Ehhez a forgatókönyvhöz:

1. Hozzon létre egy nyilvános IP-címet vagy előtagot.
2. Hozzon létre egy nyilvános standard Load balancert. 
3. Hozzon létre egy, a nyilvános IP-címhez vagy a kimenő számára dedikált előtaghoz társított előtérbeli felületet.
4. Hozzon létre egy háttér-készletet a virtuális gépek számára.
5. Helyezze a virtuális gépeket a háttér-készletbe.
6. Konfiguráljon egy kimenő szabályt a kimenő NAT engedélyezéséhez.

A [SNAT](#snat) -portok méretezéséhez használjon előtagot vagy nyilvános IP-címet. Adja hozzá a kimenő kapcsolatok forrását egy engedélyezési vagy megtagadási listához.

### <a name="outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Kimenő NAT belső standard Load Balancerhez

> [!NOTE]
> Az **Azure Virtual Network NAT** a belső standard Load balancert használó virtuális gépek kimenő kapcsolatait is biztosíthatja.  További információ: [Mi az az Azure Virtual Network NAT?](../virtual-network/nat-overview.md) .

#### <a name="details"></a>Részletek

A kimenő kapcsolat nem érhető el a belső standard Load Balancer számára, amíg az explicit módon be nem jelentve. 

További információ: [csak kimenő terheléselosztó konfigurálása](https://docs.microsoft.com/azure/load-balancer/egress-only).


### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>TCP & UDP protokollok engedélyezése a kimenő NAT-hoz egy nyilvános standard Load balancerrel

#### <a name="details"></a>Részletek

Nyilvános standard Load Balancer használata esetén a megadott automatikus kimenő NAT megfelel a terheléselosztási szabály átviteli protokolljának. 

1. Tiltsa le a kimenő [SNAT](#snat) a terheléselosztási szabályban. 
2. Konfiguráljon egy kimenő szabályt ugyanazon a terheléselosztóon.
3. Használja újra a virtuális gépek által már használt háttér-készletet. 
4. A Kimenő szabály részeként a "protokoll": "all" érték adható meg. 

Ha csak a bejövő NAT-szabályok vannak használatban, a rendszer nem biztosít kimenő NAT-szabályokat. 

1. Helyezze a virtuális gépeket egy háttérbeli készletbe.
2. Egy vagy több előtér-IP-konfiguráció definiálása nyilvános IP-cím (ek) vagy nyilvános IP-előtag esetén 
3. Konfiguráljon egy kimenő szabályt ugyanazon a terheléselosztóon. 
4. A "protokoll": "all" érték meghatározása a Kimenő szabály részeként

## <a name="terminology"></a><a name="terms"></a> Terminológiája

### <a name="source-network-address-translation"></a><a name="snat"></a>Forrás hálózati címfordítás

| Alkalmazható protokoll (ok) |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Részletek

Az Azure-beli üzemelő példányok az Azure-on kívüli végpontokkal kommunikálhatnak a nyilvános IP-címtartomány használatával.

Ha egy példány egy nyilvános IP-címmel rendelkező célhelyre indítja a kimenő forgalmat, az Azure dinamikusan leképezi az erőforrás magánhálózati IP-címét egy nyilvános IP-címhez. 

Ennek a leképezésnek a létrehozása után a kimenő forgalom visszaadott értéke eléri azt a magánhálózati IP-címet, ahol a folyamat származik. 

Az Azure a **forrás hálózati címfordítás (SNAT)** használatával hajtja végre ezt a funkciót.

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Port maszkolása SNAT (PAT)

| Alkalmazható protokoll (ok) |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Részletek

Ha a magánhálózati IP-címek egyetlen nyilvános IP-cím mögé tartoznak, az Azure a címfordítás **(Pat)** használatával elrejti a magánhálózati IP-címeket. 

A rendszer ideiglenes portokat használ a PAT számára, és a készlet mérete alapján van [előfoglalva](#preallocatedports) . 

Ha egy nyilvános terheléselosztó nyilvános IP-címek nélküli virtuális gépekhez van társítva, minden kimenő kapcsolódási forrás újraírásra kerül. 

A forrást a rendszer a virtuális hálózat magánhálózati IP-címéről a terheléselosztó nyilvános IP-címére írja át. 

A nyilvános IP-címtartomány területen a folyamat öt rekordjának egyedinek kell lennie:

* Forrás IP-címe
* Forrásport
* IP-átviteli protokoll
* Cél IP-cím
* Célport 

A port maszkolása SNAT TCP-vagy UDP-protokollal is használható. A SNAT-portok a magánhálózati forrás IP-címének újraírása után használatosak, mert több folyamat egyetlen nyilvános IP-címről származik. A SNAT-algoritmust biztosító port az UDP és a TCP protokollal eltérő SNAT-portokat biztosít.

### <a name="snat-ports-tcp"></a>SNAT portok (TCP)

| Alkalmazható protokoll (ok) |
|------------------------|
| TCP |

#### <a name="details"></a>Részletek

A SNAT-portok egy nyilvános IP-forráscím számára elérhető ideiglenes portok. A rendszer egy SNAT-portot használ egy adott cél IP-cím és port esetében. 

Ha több TCP-folyamat ugyanarra a cél IP-címére, portra és protokollra vonatkozik, az egyes TCP-folyamatok egyetlen SNAT-portot használnak. 

Ez a felhasználás biztosítja, hogy a folyamatok egyediek legyenek, amikor ugyanabból a nyilvános IP-címről származnak, és a következőkre utaznak:

* Azonos cél IP-cím
* Port
* Protokoll 

Több folyamat osztozik egyetlen SNAT-porton. 

A cél IP-címe, portja és protokollja egyedivé teszi a folyamatokat anélkül, hogy további forrásport szükségesek a nyilvános IP-címtartomány forgalmának megkülönböztetéséhez.


### <a name="snat-ports-udp"></a>SNAT-portok (UDP)

| Alkalmazható protokoll (ok) |
|------------------------|
| UDP |

#### <a name="details"></a>Részletek

Az UDP-SNAT portjait egy másik algoritmus kezeli, mint a TCP SNAT-portok.  A Load Balancer egy portra korlátozott kúp NAT nevű algoritmust használ UDP-hez.

A rendszer egy SNAT-portot használ minden egyes folyamathoz a cél IP-cím és port esetében.


### <a name="exhaustion"></a><a name="exhaustion"></a>Fogyási

| Alkalmazható protokoll (ok) |
|------------------------|
| N.A. |

#### <a name="details"></a>Részletek

A SNAT-portok erőforrásainak kimerítése esetén a kimenő folyamatok meghiúsulnak, amíg a meglévő folyamatok SNAT-portokat nem szabadítanak fel. A Load Balancer visszaállítja a SNAT-portokat a folyamat bezárásakor.

A terheléselosztó négy perc [üresjárati időkorlátot](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) használ a SNAT-portok visszaigényléséhez.

Az UDP-SNAT portok általában sokkal gyorsabbak, mint a TCP SNAT-portok, mert a használatban lévő algoritmus eltér. Tervezési és méretezési teszt a különbség miatt.

### <a name="snat-port-release-behavior-tcp"></a>SNAT-port kiadásának viselkedése (TCP)

| Alkalmazható protokoll (ok) |
|------------------------|
| TCP |

#### <a name="details"></a>Részletek

Ha egy kiszolgáló vagy ügyfél FINACK küld, a SNAT-portok 240 másodperc után lesznek felszabadítva. Az első megjelenésekor a rendszer 15 másodperc elteltével SNAT portot szabadít fel. Ha elérte az üresjárati időkorlátot, a rendszer felszabadítja a portot.

### <a name="snat-port-release-behavior-udp"></a>SNAT-port kiadási viselkedése (UDP)

| Alkalmazható protokoll (ok) |
|------------------------|
| TCP |

#### <a name="details"></a>Részletek

Ha elérte az üresjárati időtúllépést, a rendszer felszabadítja a portot.

### <a name="snat-port-reuse"></a>SNAT-port újrafelhasználása

| Alkalmazható protokoll (ok) |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>Részletek

A port felszabadítása után a port újra elérhetővé válik. Az SNAT-portok a legalacsonyabb és a legmagasabb rendelkezésre állású sorok egy adott forgatókönyv esetében, és az első elérhető SNAT-portot használja az új kapcsolatokhoz.

## <a name="limitations"></a>Korlátozások

- A felhasználható ideiglenes portok maximális száma előtér-IP-címenként 64 000.
- A konfigurálható kimenő Üresjárati időkorlát (4 – 120 perc) tartománya (240 – 7200 másodperc).
- A Load Balancer nem támogatja az ICMP protokollt a kimenő NAT-hoz.
- A kimenő szabályok csak a hálózati adapter elsődleges IP-konfigurációjához alkalmazhatók.  Nem hozható létre Kimenő szabály a virtuális gép vagy NVA másodlagos IP-címéhez. Több hálózati adapter is támogatott.
- A virtuális hálózat és más Microsoft Platform-szolgáltatások nélküli webes feldolgozói szerepkörök elérhetők a belső standard Load Balancer használata esetén. Ez a hozzáférhetőség a VNet szolgáltatások és egyéb platform-szolgáltatások működésének mellékhatása. Ne támaszkodjon erre a mellékhatásra, mert maga a saját szolgáltatás, vagy az alapul szolgáló platform értesítés nélkül változhat. Mindig tegyük fel, hogy a kimenő kapcsolatot explicit módon kell létrehoznia, ha csak belső standard Load Balancer használata esetén szeretné használni. A jelen cikkben ismertetett 3. forgatókönyv nem érhető el.

## <a name="next-steps"></a>További lépések

Ha a kimenő kapcsolattal kapcsolatos problémákat tapasztal egy Azure Load Balanceron keresztül, tekintse meg a [Kimenő kapcsolatok hibaelhárítási útmutatóját](../load-balancer/troubleshoot-outbound-connection.md).

- További tudnivalók a [standard Load balancerről](load-balancer-standard-overview.md).
- Tekintse meg a [Azure Load Balancerokkal kapcsolatos gyakori kérdéseket](load-balancer-faqs.md).
- További információ a standard nyilvános Load Balancer [kimenő szabályairól](load-balancer-outbound-rules-overview.md) .

