---
title: Kimenő kapcsolatok az Azure-ban
titleSuffix: Azure Load Balancer
description: Ez a cikk azt ismerteti, hogyan teszi lehetővé az Azure a virtuális gépek számára a nyilvános internetes szolgáltatásokkal való kommunikációt.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: 4368a025ecc158afa1ee78b8abd86bd6db42ba75
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438665"
---
# <a name="outbound-connections-in-azure"></a>Kimenő kapcsolatok az Azure-ban

Azure Load Balancer különböző mechanizmusokon keresztül biztosítja a kimenő kapcsolatokat. Ez a cikk a forgatókönyveket és azok kezelését ismerteti. Ha a kimenő kapcsolattal kapcsolatos problémát tapasztal egy Azure Load Balanceron keresztül, tekintse meg a [Kimenő kapcsolatok hibaelhárítási útmutatóját](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE]
>Ez a cikk a Resource Manager-alapú üzemelő példányokat ismerteti. A Microsoft a Resource Managert a termelési számítási feladatokhoz ajánlja.

## <a name="terminology"></a>Terminológia

| Időszak | Alkalmazható protokoll (ok) | Részletek|
| ---------|---------| -------|
| Forrás hálózati címfordítás (SNAT) | TCP, UDP | Az Azure-beli üzemelő példányok az Azure-on kívüli végpontokkal kommunikálhatnak a nyilvános IP-címtartomány használatával. Ha egy példány kimenő folyamatot kezdeményez a nyilvános IP-címtartomány egy céljára, az Azure dinamikusan leképezi a magánhálózati IP-címet egy nyilvános IP-címhez. Ennek a leképezésnek a létrehozása után a kimenő forgalomtól érkező forgalom visszaadott értéke elérheti azt a magánhálózati IP-címet is, ahol a folyamat származik. Az Azure a **forrás hálózati címfordítás (SNAT)** használatával hajtja végre ezt a funkciót.|
| Port maszkolása SNAT (PAT)| TCP, UDP |  Ha a magánhálózati IP-címek egyetlen nyilvános IP-cím mögé kerülnek, az Azure a címfordítás **(Pat)** használatával maszkolási/elrejti a magánhálózati IP-címeket. A rendszer ideiglenes portokat használ a PAT számára, és a készlet mérete alapján van [előfoglalva](#preallocatedports) . Ha egy nyilvános Load Balancer erőforrás olyan virtuálisgép-példányokhoz van társítva, amelyek nem rendelkeznek dedikált nyilvános IP-címekkel, minden kimenő kapcsolódási forrás újraírásra kerül. A forrás a virtuális hálózat magánhálózati IP-címéről a terheléselosztó nyilvános IP-címére íródik újra. A nyilvános IP-címtartomány területen a folyamat 5 rekordjának (forrás IP-címe, forrásport, IP-átviteli protokoll, cél IP-címe, célport) egyedinek kell lennie. A maszkolási SNAT TCP-vagy UDP-protokollal is használható. Az ideiglenes portok (SNAT-portok) a magánhálózati forrás IP-címének újraírása után érhetők el, mert több folyamat egyetlen nyilvános IP-címről származik. A SNAT algoritmusú port az UDP és a TCP protokollal eltérő SNAT-portokat foglal le.|
| SNAT-portok| TCP | A SNAT-portok egy adott nyilvános IP-forráscím számára elérhető ideiglenes portok. A rendszer egy SNAT-portot használ egy adott cél IP-címhez, porthoz. Ha több TCP-folyamat ugyanarra a cél IP-címére, portra és protokollra vonatkozik, az egyes TCP-folyamatok egyetlen SNAT-portot használnak. Ez biztosítja, hogy a folyamatok egyediek legyenek, amikor ugyanabból a nyilvános IP-címről származnak, és ugyanarra a cél IP-címére, portra és protokollra mutatnak. Több folyamat, amelyek mindegyike egy másik cél IP-címére, portra és protokollra vonatkozik, egyetlen SNAT-portot kell megosztania. A cél IP-címe, portja és protokollja egyedivé teszi a folyamatokat anélkül, hogy további forrásport szükségesek a nyilvános IP-címtartomány forgalmának megkülönböztetéséhez.|
|SNAT-portok | UDP | Az UDP-SNAT portjait egy másik algoritmus kezeli, mint a TCP SNAT-portok.  A Load Balancer az UDP protokollhoz "Port-korlátozott kúp NAT" néven ismert algoritmust használ.  A rendszer egy SNAT-portot használ minden egyes folyamathoz, a cél IP-címétől, a porttól függetlenül.|
| Fogyási | - | A SNAT-portok erőforrásainak kimerítése esetén a kimenő folyamatok meghiúsulnak, amíg a meglévő folyamatok SNAT-portokat nem szabadítanak fel. Load Balancer visszaállítja a SNAT-portokat, amikor a folyamat lezárult, és [4 perces üresjárati időkorlátot](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) használ a SNAT-portok üresjárati forgalomból való visszaigényléséhez. Az UDP-SNAT portok általában sokkal gyorsabbak, mint a TCP SNAT-portok, a használt algoritmus különbsége miatt. Ezeket a különbségeket figyelembe véve kell megterveznie és méreteznie a tesztet.|
| SNAT-port kiadásának viselkedése | TCP | Ha bármelyik kiszolgáló/ügyfél elküldi a FINACK, a SNAT-port 240 másodperc után lesz felszabadítva. Ha az első látható, a SNAT-port 15 másodperc elteltével fog megjelenni. Ha elérte az üresjárati időtúllépést, a rendszer a portot szabadítja fel.|
| SNAT-port kiadásának viselkedése | UDP |Ha elérte az üresjárati időtúllépést, a rendszer a portot szabadítja fel.|
| SNAT-port újrafelhasználása | TCP, UDP | A portok felszabadítása után a port igény szerint újra felhasználható.  Egy adott forgatókönyv esetében úgy gondolhatja, hogy a SNAT-portok a legalacsonyabb és a legmagasabb rendelkezésre állási sorba kerülnek, és az első elérhető SNAT-portot használja az új kapcsolatokhoz.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Port kiosztási algoritmusa

Az Azure egy algoritmus használatával határozza meg, hogy hány előre lefoglalt SNAT-port áll rendelkezésre a háttérrendszer-készlet mérete alapján a PAT használatakor. A terheléselosztó minden egyes nyilvános IP-címéhez 64 000-as port érhető el SNAT-portként minden IP-átviteli protokollhoz. Az UDP és a TCP számára a SNAT-portok száma azonos, és az IP-átviteli protokolltól függetlenül használatos.  A SNAT-port használata azonban eltérő attól függően, hogy a folyamat UDP vagy TCP. A kimenő folyamatok létrehozásakor a rendszer dinamikusan felhasználja ezeket a portokat (az előfoglalási korlátig), és a folyamat bezárásakor vagy [üresjárati időtúllépés](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) esetén is felszabadítja. A portok csak akkor lesznek felhasználva, ha a folyamatokat egyedivé kell tenni.

#### <a name="dynamic-snat-ports-allocated"></a><a name="snatporttable"></a> Lefoglalt dinamikus SNAT portok

A következő táblázat a SNAT portok előfoglalásait mutatja be a háttérbeli készlet méreteihez:

| Készlet mérete (VM-példányok) | Az előlefoglalt SNAT-portok száma IP-konfiguráció alapján |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Előfordulhat, hogy a háttér-készlet méretének módosítása hatással lehet a meglévő folyamatokra:

- Ha a háttérbeli készlet mérete nő, és a következő szintjére vált, az előlefoglalt SNAT-portok fele a következő nagyobb háttér-készletre való áttérés során visszaigényelve lesz. A visszaigényelt SNAT-porthoz társított folyamatok időtúllépést okoznak, és újra kell létrehozni. Ha új folyamatra van kísérlet, a folyamat azonnal sikeres lesz, amíg az előlefoglalt portok elérhetők lesznek.
- Ha a háttérbeli készlet mérete csökken, és az átmenetek alacsonyabb szinten vannak, a rendelkezésre álló SNAT-portok száma növekszik. Ebben az esetben a meglévő lefoglalt SNAT-portok és a hozzájuk tartozó folyamatok nem érintettek.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>A kimenő kapcsolatok forgatókönyvének áttekintése

| Forgatókönyv | Metódus | IP-protokollok | Description |
|  --- | --- | --- | --- |
|  1. nyilvános IP-címmel rendelkező virtuális gép (Azure Load Balancerval vagy anélkül | SNAT, nem használt port | TCP, UDP, ICMP, ESP | Az Azure a példány hálózati adapterének IP-konfigurációjához hozzárendelt nyilvános IP-címet használja az összes kimenő folyamathoz. A példányhoz minden elérhető ideiglenes port tartozik. Nem számít, hogy a virtuális gép terheléselosztás alatt áll-e. Ez a forgatókönyv elsőbbséget élvez a többiekkel szemben. Egy virtuális géphez hozzárendelt nyilvános IP-cím 1:1-kapcsolat (nem 1: sok), és állapot nélküli 1:1 NAT-ként lett megvalósítva. |
| 2. a virtuális géphez társított nyilvános Load Balancer (nincs nyilvános IP-cím a virtuális gépen/példányon) | SNAT a Load Balancer előtérrel (PAT) rendelkező portokkal | TCP, UDP | Ebben az esetben a Load Balancer erőforrást egy terheléselosztó-szabállyal kell konfigurálni, hogy kapcsolatot hozzon létre a nyilvános IP-frontend és a háttér-készlet között. Ha nem végzi el a szabály konfigurálását, a viselkedés a 3. forgatókönyvben ismertetett módon történik. Nincs szükség ahhoz, hogy a szabály a háttérrendszer munkafolyamati készletében működő figyelővel rendelkezzen a sikeres állapothoz. Amikor a virtuális gép létrehoz egy kimenő folyamatot, az Azure lefordítja a kimenő folyamat magánhálózati IP-címét a nyilvános Load Balancer előtér nyilvános IP-címére a SNAT-on keresztül. A terheléselosztó nyilvános IP-címének ideiglenes portjai a virtuális gép által kezdeményezett egyes folyamatok megkülönböztetésére szolgálnak. A SNAT dinamikusan használja az [előlefoglalt ideiglenes portokat](#preallocatedports) a kimenő folyamatok létrehozásakor. Ebben a kontextusban a SNAT használt ideiglenes portok neve SNAT-portok. Az SNAT-portok előre le vannak foglalva az [alapértelmezett SNAT-portok lefoglalt táblájában](#snatporttable)leírtak szerint. |
| 3. virtuális gép (nincs Load Balancer, nincs nyilvános IP-cím) vagy a virtuális gép alapszintű belső Load Balancerhoz társítva | SNAT a port maszkolásával (PAT) | TCP, UDP | Amikor a virtuális gép létrehoz egy kimenő folyamatot, az Azure lefordítja a kimenő folyamat privát forrás IP-címét egy nyilvános forrás IP-címére. Ez a nyilvános IP-cím **nem konfigurálható**, nem foglalható le, és nem számítja ki az előfizetés nyilvános IP-erőforrásának korlátját. Ha újra telepíti a virtuális gépet vagy a rendelkezésre állási készletet vagy a virtuálisgép-méretezési készletet, akkor ez a nyilvános IP-cím fel lesz szabadítva, és egy új nyilvános IP-címet igényel. Ne használja ezt a forgatókönyvet az IP-címek engedélyezési listájának engedélyezéséhez. Ehelyett használja az 1. vagy a 2. forgatókönyvet, ahol explicit módon deklarálja a kimenő viselkedést. A SNAT-portok a [lefoglalt alapértelmezett SNAT-portok táblázatban](#snatporttable)leírt módon vannak kiosztva.

## <a name="outbound-rules"></a><a name="outboundrules"></a>Kimenő szabályok

 A kimenő szabályok egyszerűvé teszik a nyilvános [standard Load Balancer](load-balancer-standard-overview.md)kimenő hálózati címfordításának konfigurálását.  A kimenő kapcsolat teljes deklaratív vezérlése lehetővé teszi az adott igényeknek megfelelő méretezést és finomhangolást. Ez a szakasz a fent ismertetett 2 (B) forgatókönyvet bontja ki.

![Load Balancer kimenő szabályok](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

A kimenő szabályok használata esetén a Load Balancer használatával megadhatja a kimenő NAT-t a semmiből. A meglévő kimenő NAT működésének méretezését és finomhangolását is elvégezheti.

A kimenő szabályok segítségével a következőket vezérelheti:

- mely virtuális gépeket kell lefordítani a nyilvános IP-címekre.
- a kimenő SNAT portok kiosztása.
- a kimenő fordítását biztosító protokollok.
- a kimenő kapcsolat üresjárati időkorlátja (4-120 perc) milyen időtartamra van használatban.
- meg kell-e küldeni a TCP alaphelyzetbe állítását üresjárati időkorláton
- egyetlen szabállyal rendelkező TCP és UDP átviteli protokollok

### <a name="outbound-rule-definition"></a>Kimenő szabály definíciója

Az összes Load Balancer-szabályhoz hasonlóan a kimenő szabályok ugyanazt a szintaxist követik, mint a terheléselosztás és a bejövő NAT- **szabályok: előtér**-  +  **Paraméterek**  +  **háttér-készlete**. Egy kimenő szabály konfigurálja a kimenő NAT-t a _háttér-készlet által azonosított összes virtuális gép_ számára, hogy a rendszer lefordítsa a _felületet_.  A _Paraméterek_ további részletes szabályozást biztosítanak a kimenő NAT-algoritmushoz képest.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Kimenő NAT méretezése több IP-címmel

A frontend által biztosított további IP-címek további 64 000 ideiglenes portokat biztosítanak a Load Balancer számára SNAT-portokként való használatra. Több IP-címet is használhat a nagy léptékű forgatókönyvek tervezéséhez, és a kimenő szabályok használatával csökkentheti a [SNAT kimerülésének](troubleshoot-outbound-connection.md#snatexhaust) hajlamos mintázatát.  

A [nyilvános IP-előtagot](https://aka.ms/lbpublicipprefix) közvetlenül is használhatja egy kimenő szabállyal.  A nyilvános IP-előtag használatával egyszerűbben méretezhető és egyszerűsített, az Azure-környezetből származó folyamatokat tartalmazó lista is elérhető. A Load Balancer erőforráson belül konfigurálhat egy előtérbeli IP-konfigurációt, amely közvetlenül a nyilvános IP-cím előtagra hivatkozik.  Ez lehetővé teszi a nyilvános IP-előtag Load Balancer kizárólagos felügyeletét, és a kimenő kapcsolatok esetében a rendszer automatikusan a nyilvános IP-előtagon belül található összes nyilvános IP-címet fogja használni a kimenő kapcsolatokhoz.  A nyilvános IP-előtagon belüli összes IP-cím egy további 64 000 ideiglenes portot biztosít IP-címenként, hogy Load Balancer SNAT-portokként legyen használatban.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Kimenő forgalom üresjárati időkorlátja és a TCP-visszaállítás

A kimenő szabályok egy konfigurációs paramétert biztosítanak a kimenő folyamat üresjárati időkorlátjának szabályozására, és az alkalmazás igényeinek megfelelően illeszkednek. A kimenő üresjárati időtúllépések alapértelmezett értéke 4 perc. Megtudhatja, hogyan [konfigurálhatja az üresjárati időkorlátot](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). Load Balancer alapértelmezett viselkedése a folyamat csendes eldobása a kimenő Üresjárati időkorlát elérésekor.  A `enableTCPReset` paraméterrel előre jelezhető az alkalmazások viselkedése, és szabályozható, hogy a kimenő Üresjárati időkorlát időtúllépése esetén a kétirányú TCP alaphelyzetbe állítása (TCP első) legyen-e elküldve. Tekintse át az [Üresjárat időkorlátjának TCP-visszaállítását](https://aka.ms/lbtcpreset) , beleértve a régió rendelkezésre állását.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Kimenő kapcsolat megakadályozása

A terheléselosztási szabályok biztosítják a kimenő NAT automatikus programozását. Bizonyos esetekben azonban a terheléselosztási szabály segítségével letilthatja a kimenő NAT automatikus programozását, hogy lehetővé tegye a viselkedés szabályozását vagy pontosítását.  
Ezt a paramétert kétféleképpen is használhatja:

1. A kimenő SNAT bejövő IP-címének opcionális letiltása a kimenő SNAT letiltásával a terheléselosztási szabályhoz
  
2. A bejövő és kimenő IP-címek kimenő SNAT paramétereinek hangolása egyidejűleg.  Az automatikus kimenő NAT-programozást le kell tiltani ahhoz, hogy a kimenő szabályok szabályozni tudják a vezérlést.  Ha például meg szeretné változtatni egy SNAT-port lefoglalását a bejövő forgalomhoz is, a `disableOutboundSnat` paramétert True értékre kell állítani.  Ha egy kimenő szabályt próbál használni a bejövő IP-cím paramétereinek újradefiniálásához, és nem adta meg a terheléselosztási szabály kimenő NAT-programozását, akkor a kimenő szabályok konfigurálásának művelete sikertelen lesz.

>[!IMPORTANT]
> Ha a paraméter értéke TRUE (igaz), a virtuális gép nem rendelkezik kimenő kapcsolattal, és nem rendelkezik kimenő kapcsolattal.  A virtuális gép vagy az alkalmazás néhány művelete attól függ, hogy a kimenő kapcsolat elérhető-e. Győződjön meg arról, hogy tisztában van a forgatókönyv függőségeivel, és befolyásolta ennek a változásnak a hatását.

Időnként előfordulhat, hogy a virtuális gép számára engedélyezni kell a kimenő folyamat létrehozását. Vagy előfordulhat, hogy meg kell adni egy követelményt, amely azt a célt szolgálhatja, hogy mely célhelyek érhetők el a kimenő folyamatokkal, vagy hogy mely célhelyek kezdhetik meg Ebben az esetben [hálózati biztonsági csoportok](../virtual-network/security-overview.md) használatával felügyelheti a virtuális gép által elérhető célhelyeket. A NSG használatával is kezelheti, hogy mely nyilvános célhelyek indíthatnak bejövő folyamatokat.

Ha NSG alkalmaz egy elosztott terhelésű virtuális gépre, ügyeljen a [szolgáltatás-címkékre](../virtual-network/security-overview.md#service-tags) és az [alapértelmezett biztonsági szabályokra](../virtual-network/security-overview.md#default-security-rules). Gondoskodnia kell arról, hogy a virtuális gép Azure Load Balancertól kapjon állapot-mintavételi kérelmeket.

Ha egy NSG letiltja az állapot-mintavételi kérelmeket a AZURE_LOADBALANCER alapértelmezett címkétől, a virtuális gép állapotának mintavétele meghiúsul, és a virtuális gép meg van jelölve. Load Balancer leállítja az új folyamatok küldését a virtuális gépre.

## <a name="scenarios-with-outbound-rules"></a>Forgatókönyvek kimenő szabályokkal

| # | Forgatókönyv| Részletek|
|---|---|---|
| I | A kifelé irányuló kimenő kapcsolatok egy adott nyilvános IP-címhez| A kifelé irányuló kimenő kapcsolatok esetében egy kimenő szabályt is használhat, hogy a rendszer egy adott nyilvános IP-címről hozzon létre, hogy megkönnyítse az engedélyezési forgatókönyvek használatát.  Ez a forrás nyilvános IP-cím ugyanaz lehet, mint a terheléselosztási szabály által használt terheléselosztási szabályok vagy a nyilvános IP-címek különböző készletei.  1. [Nyilvános IP-előtag](https://aka.ms/lbpublicipprefix) (vagy nyilvános IP-címek létrehozása nyilvános IP-előtagból) 2. Hozzon létre egy nyilvános standard Load Balancer 3. Hozzon létre a 4. használni kívánt nyilvános IP-előtagra (vagy nyilvános IP-címekre) hivatkozó előtérbeli felületet. Használja fel a háttérrendszer-készletet, vagy hozzon létre egy háttér-készletet, és helyezze a virtuális gépeket a nyilvános Load Balancer 5 egyik háttér-készletéből. Konfiguráljon egy kimenő szabályt a nyilvános Load Balancer a virtuális gépek kimenő NAT-hálózatának programba való bekapcsolásához a frontendek használatával. Ha nem szeretné, hogy a terheléselosztási szabályt a kimenő forgalomhoz használja, le kell tiltania a kimenő SNAT a terheléselosztási szabályban.
| II | SNAT-port foglalásának módosítása| A kimenő szabályok használatával beállíthatja az [automatikus SNAT-portok kiosztását a háttérbeli készlet méretétől függően](load-balancer-outbound-connections.md#preallocatedports). Ha például két virtuális gép egyetlen nyilvános IP-címet oszt ki a kimenő NAT-hoz, akkor érdemes lehet megnövelni az alapértelmezett 1024-as portokból lefoglalt SNAT-portok számát, ha SNAT-kimerülést tapasztal. Minden nyilvános IP-cím legfeljebb 64 000 ideiglenes portot tud elősegíteni.  Ha egyetlen nyilvános IP-címmel rendelkező kimenő szabályt konfigurál, a háttér-készletben lévő virtuális gépekhez összesen 64 000 SNAT-portot oszthat ki.  Két virtuális gép esetében legfeljebb 32 000 SNAT-portot lehet lefoglalni egy kimenő szabállyal (2x 32 000 = 64 000). A kimenő szabályok használatával beállíthatja a SNAT portok alapértelmezett kiosztását. Az alapértelmezett SNAT-kiosztásnál több vagy kevesebb lefoglalást biztosít. A kimenő szabályok összes felületének minden nyilvános IP-címe akár 64 000 ideiglenes portot is felhasználhat SNAT-portokként való használatra.  Load Balancer SNAT-portokat foglal le a 8 többszörösében. Ha a 8 értékkel nem osztható értéket ad meg, a rendszer elutasítja a konfigurációs műveletet.  Ha a nyilvános IP-címek száma alapján több SNAT-portot próbál kiosztani, akkor a rendszer elutasítja a konfigurációs műveletet.  Ha például egy virtuális gépenként 10 000 portot foglal le, és a háttérbeli készlet 7 virtuális gépe egyetlen nyilvános IP-címet oszt meg, akkor a rendszer elutasítja a konfigurációt (7 x 10 000 SNAT-port > 64 000 SNAT-portok).  A forgatókönyv engedélyezéséhez több nyilvános IP-címet is hozzáadhat a Kimenő szabály előtérbeli felületéhez. A háttér-készlet méretétől függően visszatérhet az [alapértelmezett SNAT-portra](load-balancer-outbound-connections.md#preallocatedports) a portok számának 0 értékének megadásával. Ebben az esetben az első 50-es virtuálisgép-példány 1024 portot kap, a 51-100-as virtuálisgép-példányok pedig 512-et kapnak, így a [táblázat](#snatporttable)szerint.|
| III| Csak kimenő engedélyezése | Egy nyilvános standard Load Balancer használatával biztosíthatja a kimenő NAT-t a virtuális gépek csoportjai számára. Ebben a forgatókönyvben saját maga is használhat kimenő szabályt anélkül, hogy további szabályokat kellene megadnia.|
| IV | Kimenő NAT csak virtuális gépekhez (nincs bejövő) | Definiáljon egy nyilvános standard Load Balancer, helyezze a virtuális gépeket a háttér-készletbe, és állítson be egy kimenő szabályt a kimenő NAT programba, és a kimenő kapcsolatok a megadott nyilvános IP-címről származnak. A nyilvános IP-előtagot is használhatja, amely leegyszerűsíti a kimenő kapcsolatok forrásának listázását. 1. Hozzon létre egy nyilvános standard Load Balancer. 2. Hozzon létre egy háttér-készletet, és helyezze a virtuális gépeket a nyilvános Load Balancer háttér-készletéből. 3. Konfiguráljon egy kimenő szabályt a nyilvános Load Balanceron a kimenő NAT programhoz a virtuális gépek számára.
| V| Kimenő NAT belső standard Load Balancer-forgatókönyvekhez| Belső standard Load Balancer használata esetén a kimenő NAT nem érhető el, amíg explicit módon be nem jelenti a kimenő kapcsolatot. A kimenő kapcsolatok a belső standard Load Balancer mögött lévő virtuális gépek kimenő kapcsolatának létrehozásához a következő lépésekkel határozható meg: 1. Hozzon létre egy nyilvános standard Load Balancer. 2. Hozzon létre egy háttér-készletet, és helyezze a virtuális gépeket a nyilvános Load Balancer háttér-készletéből a belső Load Balancer mellett. 3. Konfiguráljon egy kimenő szabályt a nyilvános Load Balanceron a kimenő NAT programhoz a virtuális gépek számára. Ennek a forgatókönyvnek a részletes ismertetését [ebben a példában](https://docs.microsoft.com/azure/load-balancer/egress-only)találja. |
| VI | TCP-& UDP-protokollok engedélyezése a kimenő NAT nyilvános standard Load Balancer | Nyilvános standard Load Balancer használata esetén a megadott automatikus kimenő NAT-programozás megfelel a terheléselosztási szabály átviteli protokolljának. 1. Tiltsa le a kimenő SNAT a terheléselosztási szabályban. 2. Konfiguráljon egy kimenő szabályt ugyanarra a Load Balancer. 3. Használja újra a virtuális gépek által már használt háttér-készletet. 4. A Kimenő szabály részeként a "protokoll": "all" érték adható meg. Ha csak a bejövő NAT-szabályok vannak használatban, a rendszer nem biztosít kimenő NAT-szabályokat. 1. Helyezze a virtuális gépeket egy háttérbeli készletbe. 2. Adjon meg egy vagy több előtér-IP-konfigurációt nyilvános IP-cím (ek) vagy nyilvános IP-előtag esetén 3. Konfiguráljon egy kimenő szabályt ugyanarra a Load Balancer. 4. A "protokoll": "all" érték meghatározása a Kimenő szabály részeként |


## <a name="limitations"></a>Korlátozások

- A felhasználható ideiglenes portok maximális száma előtér-IP-címenként 64 000.
- A konfigurálható kimenő Üresjárati időkorlát (4 – 120 perc) tartománya (240 – 7200 másodperc).
- A Load Balancer nem támogatja az ICMP protokollt a kimenő NAT-hoz.
- A kimenő szabályok csak a hálózati adapter elsődleges IP-konfigurációjához alkalmazhatók.  Egy virtuális gép vagy NVA másodlagos IP-címéhez nem hozható létre Kimenő szabály. Több hálózati adapter is támogatott.
- A VNet és más Microsoft-platformokat nem tartalmazó webes feldolgozói szerepkörök csak akkor érhetők el, ha csak belső standard Load Balancer van használatban, mivel a VNet szolgáltatások és egyéb platform-szolgáltatások funkciójának mellékhatása. Ne támaszkodjon erre a mellékhatásra, mert maga a saját szolgáltatás, vagy az alapul szolgáló platform értesítés nélkül változhat. Mindig feltételezni kell, hogy a kimenő kapcsolatot explicit módon kell létrehoznia, ha csak belső standard Load Balancer használata esetén szükséges. A jelen cikkben ismertetett 3. forgatókönyv nem érhető el.

## <a name="next-steps"></a>Következő lépések

- További információ a [standard Load Balancerról](load-balancer-standard-overview.md).
- Tekintse meg a [Azure Load Balancerokkal kapcsolatos gyakori kérdéseket](load-balancer-faqs.md).
- További információ a szabványos nyilvános Load Balancer [kimenő szabályairól](load-balancer-outbound-rules-overview.md) .
- További információ a [Load Balancerról](load-balancer-overview.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
- Ismerje meg az Azure egyéb fontos [hálózati funkcióit](../networking/networking-overview.md) .
