---
title: Kimenő kapcsolatok az Azure-ban (klasszikus)
titlesuffix: Azure Load Balancer
description: Ez a cikk azt ismerteti, hogy az Azure hogyan teszi lehetővé a felhőalapú szolgáltatások számára a nyilvános internetes szolgáltatásokkal való kommunikációt.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: allensu
ms.openlocfilehash: 10af3b4838aae1565bac1d996997c117a74cedbc
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274665"
---
# <a name="outbound-connections-classic"></a>Kimenő kapcsolatok (klasszikus)

Az Azure számos különböző mechanizmuson keresztül biztosít kimenő kapcsolatot az ügyfelek központi telepítéséhez. Ez a cikk a forgatókönyvek, a működésük és a kezelésük módját ismerteti.

>[!NOTE]
>Ez a cikk csak a klasszikus központi telepítéseket ismerteti.  Tekintse át a [kimenő kapcsolatokat](load-balancer-outbound-connections.md) az összes Resource Manager-alapú üzembe helyezési forgatókönyv esetén az Azure-ban.

Az Azure-beli üzemelő példányok az Azure-on kívüli végpontokkal kommunikálhatnak a nyilvános IP-címtartomány használatával. Ha egy példány kimenő folyamatot kezdeményez a nyilvános IP-címtartomány egy céljára, az Azure dinamikusan leképezi a magánhálózati IP-címet egy nyilvános IP-címhez. Ennek a leképezésnek a létrehozása után a kimenő forgalomtól érkező forgalom visszaadott értéke elérheti azt a magánhálózati IP-címet is, ahol a folyamat származik.

Az Azure a forrás hálózati címfordítás (SNAT) használatával hajtja végre ezt a funkciót. Ha egy nyilvános IP-cím mögé több magánhálózati IP-cím is fel van használva, akkor az Azure a címfordítás [(Pat)](#pat) használatával álcázza a magánhálózati IP-címeket. A rendszer ideiglenes portokat használ a PAT [](#preallocatedports) számára, és a készlet mérete alapján van előfoglalva.

Több [kimenő forgatókönyv](#scenarios)is létezik. Ezeket a forgatókönyveket igény szerint kombinálhatja. Alaposan tekintse át ezeket a képességeket, megkötéseket és mintákat, ahogyan azok az üzemi modellre és az alkalmazásokra vonatkoznak. Tekintse át az [ilyen forgatókönyvek kezelésével](#snatexhaust)kapcsolatos útmutatást.

## <a name="scenarios"></a>Forgatókönyv áttekintése

Az Azure három különböző módszert biztosít a kimenő kapcsolatok klasszikus üzembe helyezésének eléréséhez.  Nem minden klasszikus üzemelő példány rendelkezik mind a három forgatókönyvvel:

| Forgatókönyv | Módszer | IP-protokollok | Leírás | Webes feldolgozói szerepkör | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Példány szintű nyilvános IP-címmel rendelkező virtuális gép](#ilpip) | SNAT, nem használt port | TCP, UDP, ICMP, ESP | Az Azure a nyilvános IP-címet hozzárendelő virtuális gépet használja. A példányhoz minden elérhető ideiglenes port tartozik. | Nem | Igen |
| [2. nyilvános terheléselosztási végpont](#publiclbendpoint) | SNAT (PAT) a nyilvános végponthoz | TCP, UDP | Az Azure a nyilvános IP-cím nyilvános végpontját több privát végponttal osztja meg. Az Azure a nyilvános végponton elmúló portokat használ a PAT számára. | Igen | Igen |
| [3. Önálló virtuális gép](#defaultsnat) | SNAT a port maszkolásával (PAT) | TCP, UDP | Az Azure automatikusan kijelöl egy nyilvános IP-címet a SNAT, megosztja ezt a nyilvános IP-címet a teljes telepítéssel, és a nyilvános végpont IP-címének ideiglenes portjait használja a PAT számára. Ez egy tartalék forgatókönyv az előző forgatókönyvekhez. Ha láthatóságra és vezérlésre van szüksége, nem ajánlott. | Igen | Igen |

Ez az Azure-beli Resource Manager-alapú üzemelő példányok számára elérhető kimenő kapcsolódási funkciók részhalmaza.  

A klasszikus különböző központi telepítések különböző funkciókkal rendelkeznek:

| Klasszikus üzembe helyezés | Elérhető funkció | 
| --- | --- |
| Virtuális gép | [1](#ilpip)., [2](#publiclbendpoint). vagy [3](#defaultsnat) . forgatókönyv |
| Webes feldolgozói szerepkör | csak [2](#publiclbendpoint). eset, [3](#defaultsnat) | 

A [kockázatcsökkentő stratégiák](#snatexhaust) is ugyanazok a különbségek.

A klasszikus központi telepítések esetében az ideiglenes portok előfoglalásához használt algoritmus ugyanaz, mint a Azure Resource Manager erőforrás-telepítések esetében.

### <a name="ilpip"></a>1. forgatókönyv: Példány szintű nyilvános IP-címmel rendelkező virtuális gép

Ebben az esetben a virtuális gépnek van hozzárendelve egy példány szintű nyilvános IP-címe (ILPIP). A kimenő kapcsolatok tekintetében nem számít, hogy a virtuális gép rendelkezik-e elosztott terhelésű végponttal vagy sem. Ez a forgatókönyv elsőbbséget élvez a többiekkel szemben. ILPIP használata esetén a virtuális gép az összes kimenő folyamathoz a ILPIP használja.  

Egy virtuális géphez hozzárendelt nyilvános IP-cím 1:1-kapcsolat (nem 1: sok), és állapot nélküli 1:1 NAT-ként lett megvalósítva.  A rendszer nem használja az álcázott portot (PAT), és a virtuális gép számára elérhető minden ideiglenes port.

Ha az alkalmazás sok kimenő folyamatot kezdeményez, és a SNAT-portok kimerülését tapasztalja, érdemes lehet egy [ILPIP rendelni a SNAT-korlátok enyhítéséhez](#assignilpip). Tekintse át a [SNAT-kimerültség kezelését](#snatexhaust) teljes egészében.

### <a name="publiclbendpoint"></a>2. forgatókönyv: Nyilvános terheléselosztási végpont

Ebben az esetben a virtuális gép vagy a webes feldolgozói szerepkör a terheléselosztási végponton keresztül egy nyilvános IP-címhez van társítva. A virtuális gépnek nincs hozzárendelve nyilvános IP-címe. 

Ha a terheléselosztásos virtuális gép kimenő folyamatot hoz létre, az Azure a kimenő folyamat magánhálózati IP-címét a nyilvános terheléselosztási végpont nyilvános IP-címére fordítja le. Az Azure a SNAT használatával hajtja végre ezt a funkciót. Az Azure a [Pat](#pat) használatával több magánhálózati IP-címet is maszkolást használ egy nyilvános IP-cím mögé. 

A terheléselosztó nyilvános IP-címének ideiglenes portjai a virtuális gép által kezdeményezett egyes folyamatok megkülönböztetésére szolgálnak. A SNAT dinamikusan használja az [előlefoglalt ideiglenes portokat](#preallocatedports) a kimenő folyamatok létrehozásakor. Ebben a kontextusban a SNAT használt ideiglenes portok neve SNAT-portok.

Az SNAT-portok a [SNAT és a Pat](#snat) című szakaszban leírtak szerint vannak kiosztva. Egy véges erőforrás, amely kimeríthető. Fontos megérteni a használatuk módját. [](#pat) Ha szeretné megtudni, hogyan tervezze meg ezt a felhasználást, és szükség esetén enyhítse a megoldást, tekintse át a [SNAT-kimerülés kezelését](#snatexhaust)ismertető

Ha [több nyilvános terheléselosztási végpont](load-balancer-multivip.md) létezik, ezek közül a nyilvános IP-címek közül bármelyik a kimenő forgalomra jelölt, az egyik pedig véletlenszerűen van kiválasztva.  

### <a name="defaultsnat"></a>3. forgatókönyv: Nincs hozzárendelve nyilvános IP-cím

Ebben az esetben a virtuális gép vagy a webes feldolgozói szerepkör nem része egy nyilvános terheléselosztási végpontnak.  A virtuális gép esetében azonban nincs hozzárendelve ILPIP-címe. Amikor a virtuális gép létrehoz egy kimenő folyamatot, az Azure lefordítja a kimenő folyamat privát forrás IP-címét egy nyilvános forrás IP-címére. Az ehhez a kimenő folyamathoz használt nyilvános IP-cím nem konfigurálható, és nem számít bele az előfizetés nyilvános IP-erőforrásának korlátba.  Az Azure automatikusan lefoglalja ezt a címeket.

Az Azure a SNAT-t használja a port maszkolásával ([Pat](#pat)) a függvény végrehajtásához. Ez a forgatókönyv hasonló a 2. forgatókönyvhöz, de a használt IP-cím nem szabályozható. Ez egy tartalék forgatókönyv, ha az 1. és a 2. forgatókönyvek nem léteznek. Ezt a forgatókönyvet nem javasoljuk, ha a kimenő címet szeretné szabályozni. Ha a kimenő kapcsolatok az alkalmazás kritikus részét képezik, egy másik forgatókönyvet kell választania.

Az SNAT-portok a [SNAT és a Pat](#snat) című szakaszban leírtak szerint vannak kiosztva.  A nyilvános IP-címet megosztó virtuális gépek vagy webes feldolgozói szerepkörök száma határozza meg az előlefoglalt ideiglenes portok számát.   Fontos megérteni a használatuk módját. [](#pat) Ha szeretné megtudni, hogyan tervezze meg ezt a felhasználást, és szükség esetén enyhítse a megoldást, tekintse át a [SNAT-kimerülés kezelését](#snatexhaust)ismertető

## <a name="snat"></a>A SNAT és a PAT ismertetése

### <a name="pat"></a>Port maszkolása SNAT (PAT)

Amikor egy központi telepítés kimenő kapcsolatokat kezdeményez, minden kimenő kapcsolódási forrás újraírásra kerül. A forrás a magánhálózati IP-címtartomány és a telepítéshez társított nyilvános IP-cím (a fent ismertetett forgatókönyvek alapján) újraírása. A nyilvános IP-címtartomány területen a folyamat 5 rekordjának (forrás IP-címe, forrásport, IP-átviteli protokoll, cél IP-címe, célport) egyedinek kell lennie.  

Az ideiglenes portok (SNAT-portok) a magánhálózati forrás IP-címének újraírása után érhetők el, mert több folyamat egyetlen nyilvános IP-címről származik. 

A rendszer egy SNAT-portot használ egy adott cél IP-cím, port és protokoll számára. Ha több folyamat ugyanarra a cél IP-címére, portra és protokollra vonatkozik, az egyes folyamatok egyetlen SNAT-portot használnak. Ez biztosítja, hogy a folyamatok egyediek legyenek, amikor ugyanabból a nyilvános IP-címről származnak, és ugyanarra a cél IP-címére, portra és protokollra mutatnak. 

Több folyamat, amelyek mindegyike egy másik cél IP-címére, portra és protokollra vonatkozik, egyetlen SNAT-portot kell megosztania. A cél IP-címe, portja és protokollja egyedivé teszi a folyamatokat anélkül, hogy további forrásport szükségesek a nyilvános IP-címtartomány forgalmának megkülönböztetéséhez.

A SNAT-portok erőforrásainak kimerítése esetén a kimenő folyamatok meghiúsulnak, amíg a meglévő folyamatok SNAT-portokat nem szabadítanak fel. Load Balancer visszaállítja a SNAT-portokat, amikor a folyamat lezárult, és [4 perces üresjárati](#idletimeout) időkorlátot használ a SNAT-portok üresjárati forgalomból való visszaigényléséhez.

A SNAT-portok kimerüléséhez gyakran vezető feltételek enyhítéséhez tekintse át a [SNAT kezelése](#snatexhaust) szakaszt.

### <a name="preallocatedports"></a>Ideiglenes port előfoglalása a port maszkolása SNAT (PAT)

Az Azure egy algoritmus használatával határozza meg, hogy hány előre lefoglalt SNAT-port áll rendelkezésre a háttérrendszer-készlet mérete alapján a SNAT ([Pat](#pat)) port használatakor. A SNAT-portok egy adott nyilvános IP-forráscím számára elérhető ideiglenes portok.

Az Azure előzetesen lefoglalja a SNAT-portokat, amikor egy példány üzembe helyezése attól függ, hogy hány virtuális gép vagy webes feldolgozói szerepkör-példány osztozik egy adott nyilvános IP-címen.  A kimenő folyamatok létrehozásakor a [Pat](#pat) dinamikusan felhasználja (az előlefoglalt korlátig), és felszabadítja ezeket a portokat, amikor a folyamat lezárul vagy üresjáratban időtúllépés történik.

A következő táblázat a SNAT portok előfoglalásait mutatja be a háttérbeli készlet méreteihez:

| példányok | Előlefoglalt SNAT-portok száma (példány) |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Ne feledje, hogy az elérhető SNAT-portok száma nem közvetlenül a folyamatok számára van lefordítva. Egyetlen SNAT-port többször is felhasználható több egyedi célhelyre. A portok csak akkor lesznek felhasználva, ha a folyamatokat egyedivé kell tenni. A tervezéssel és a mérsékléssel kapcsolatos útmutatásért tekintse meg a kimerített [erőforrás kezelésével](#snatexhaust) és a [Pat](#pat)leírásával foglalkozó szakaszt.

Előfordulhat, hogy a telepítés méretének módosítása hatással lehet a már meglévő folyamatokra. Ha a háttérbeli készlet mérete nő, és a következő szintjére vált, az előlefoglalt SNAT-portok fele a következő nagyobb háttér-készletre való áttérés során visszaigényelve lesz. A visszaigényelt SNAT-porthoz társított folyamatok időtúllépést okoznak, és újra kell létrehozni. Ha új folyamatra van kísérlet, a folyamat azonnal sikeres lesz, amíg az előlefoglalt portok elérhetők lesznek.

Ha az üzemelő példány mérete csökken, és az áttérés alacsonyabb szintű, a rendelkezésre álló SNAT-portok száma növekszik. Ebben az esetben a meglévő lefoglalt SNAT-portok és a hozzájuk tartozó folyamatok nem érintettek.

Ha a felhőalapú szolgáltatás újratelepítése vagy módosítása megtörténik, az infrastruktúra ideiglenesen jelentheti a háttér-készletet, hogy a tényleges és az Azure-ban a vártnál kisebb SNAT-portok legyenek lefoglalva.  Ez átmenetileg növelheti a SNAT-portok kimerülésének valószínűségét. Végül a készlet mérete a tényleges méretre vált, és az Azure automatikusan megnöveli az előlefoglalt SNAT-portokat a fenti táblázat alapján a várt számra.  Ez a viselkedés tervezési és nem konfigurálható.

A SNAT portok kiosztása IP-átviteli protokoll specifikus (a TCP és az UDP külön van karbantartva), és a következő feltételekkel szabadítható fel:

### <a name="tcp-snat-port-release"></a>TCP SNAT-port kiadása

- Ha mind a kiszolgáló, mind az ügyfél FIN/ACK-t küld, a SNAT port 240 másodperc után lesz felszabadítva.
- Ha az első látható, a SNAT-port 15 másodperc elteltével fog megjelenni.
- a tétlen időtúllépés elérte

### <a name="udp-snat-port-release"></a>UDP SNAT-port kiadása

- a tétlen időtúllépés elérte

## <a name="problemsolving"></a>Problémamegoldás 

Ez a szakasz az Azure-beli kimenő kapcsolatokkal kapcsolatos SNAT-kimerültség és egyéb forgatókönyvek enyhítését segíti elő.

### <a name="snatexhaust"></a>A SNAT (PAT) portjának kimerülésének kezelése
A Pathoz használt [](#pat) [ideiglenes portok](#preallocatedports) kimeríthető erőforrások, amelyeket [nem a nyilvános IP-cím](#defaultsnat) és a [nyilvános elosztott terhelésű végpont](#publiclbendpoint)nem ismertet.

Ha tudja, hogy több kimenő TCP-vagy UDP-kapcsolatra van szüksége ugyanahhoz a cél IP-címhez és porthoz, és megfigyelheti a sikertelen kimenő kapcsolatokat, vagy ha támogatja a SNAT-portok kimerítésének támogatását (az előzetesen lefoglalt [ideiglenes portok](#preallocatedports) a [Pat](#pat)által használt) számos általános kockázatcsökkentő lehetőséggel rendelkezik. Tekintse át ezeket a beállításokat, és döntse el, hogy mi az elérhető és melyik a legmegfelelőbb a forgatókönyvhöz. Lehetséges, hogy egy vagy több segíthet a forgatókönyv kezelésében.

Ha nem sikerül megismerni a kimenő kapcsolatok viselkedését, használhatja az IP-verem statisztikáit (netstat). Vagy hasznos lehet a kapcsolatok viselkedésének figyelésére a csomagok rögzítései használatával.

#### <a name="connectionreuse"></a>Az alkalmazás módosítása a kapcsolatok újrafelhasználásához 
A SNAT használt ideiglenes portok igényét csökkentheti az alkalmazásban található kapcsolatok újrafelhasználásával. Ez különösen az olyan protokollok esetében igaz, mint a HTTP/1.1, ahol az alapértelmezett a kapcsolatok újrafelhasználása. Továbbá a HTTP protokollt használó más protokollok (például a REST) is hasznosak lehetnek. 

Az újrafelhasználás mindig jobb, mint az egyes kérések egyéni, atomi TCP-kapcsolatai. A több teljesítményű, nagyon hatékony TCP-tranzakció eredményeit is újra felhasználhatja.

#### <a name="connection pooling"></a>Az alkalmazás módosítása a kapcsolatok készletezésének használatára
Az alkalmazásban létrehozhat egy kapcsolati készletezési sémát, ahol a kérések belsőleg oszlanak meg egy rögzített kapcsolaton belül (az összes újrafelhasználás, ahol lehetséges). Ez a séma korlátozza a használatban lévő ideiglenes portok számát, és egy előre jelezhető környezetet hoz létre. Ez a séma növelheti a kérések átviteli sebességét azáltal, hogy lehetővé teszi több egyidejű művelet engedélyezését, ha egyetlen kapcsolódás blokkolja a művelet válaszát.  

Előfordulhat, hogy a kapcsolatok készletezése már létezik azon a keretrendszeren belül, amelyet az alkalmazás fejlesztéséhez vagy az alkalmazás konfigurációs beállításaihoz használ. Összekapcsolhatja a kapcsolatok készletezését a kapcsolatok újbóli használatával. A többszörös kérelmek ezután egy rögzített, kiszámítható számú portot használnak ugyanahhoz a cél IP-címhez és porthoz. A kérések kihasználják a TCP-tranzakciók hatékony használatát a késés és az erőforrások kihasználtságának csökkentése érdekében. Az UDP-tranzakciók is hasznosak lehetnek, mivel az UDP-folyamatok számának kezelése a kimerülési feltételek elkerülésével és a SNAT-portok kihasználtságának kezelésével is jár.

#### <a name="retry logic"></a>Az alkalmazás módosítása kevésbé agresszív újrapróbálkozási logika használatára
Ha a [pathoz](#pat) használt, előlefoglalt időszakos [portok](#preallocatedports) kimerülése vagy az alkalmazások meghibásodása esetén az agresszív vagy találgatásos kényszerített újrapróbálkozások a romlás és a leállítási logikája miatt kimerítik vagy megtartják a fáradtságot. Az ideiglenes portok igényét kevésbé agresszív újrapróbálkozási logika használatával csökkentheti. 

Az ideiglenes portok 4 perces üresjárati időkorláttal rendelkeznek (nem állítható be). Ha az újrapróbálkozások túl agresszívek, a kimerültségnek nincs lehetősége a saját törlésére. Ezért figyelembe véve, hogy a--és milyen gyakran – az alkalmazás újrapróbálkozási tranzakciói a terv kritikus részét képezik.

#### <a name="assignilpip"></a>Példány szintjének nyilvános IP-címének társítása minden virtuális géphez
A ILPIP hozzárendelésével a forgatókönyv [egy virtuális gépre való nyilvános IP-](#ilpip)címére változik. Az egyes virtuális gépekhez használt nyilvános IP-címek minden ideiglenes portja elérhető a virtuális gép számára. (Azon forgatókönyvek esetében, amelyekben a nyilvános IP-címek ideiglenes portjai meg vannak osztva a megfelelő telepítéshez társított összes virtuális géppel.) Vannak olyan kompromisszumok, amelyek figyelembe veszik, például a nagy számú egyedi IP-cím engedélyezési lehetséges következményei.

>[!NOTE] 
>Ez a beállítás webes feldolgozói szerepkörök esetén nem érhető el.

### <a name="idletimeout"></a>A kimenő Üresjárati időkorlát alaphelyzetbe állítása a Keepalives használatával

A kimenő kapcsolatok 4 perces üresjárati időkorláttal rendelkeznek. Ez az időkorlát nem állítható be. Azonban a Transport (például a TCP Keepalives) vagy az alkalmazás-réteg Keepalives segítségével frissítheti az üresjárati folyamatokat, és szükség esetén visszaállíthatja ezt az üresjárati időkorlátot.  Érdeklődjön a csomagban található bármely csomagolt szoftver szállítóján, hogy ez támogatott-e, illetve hogyan engedélyezhető.  Általában csak az egyik oldalon kell Keepalives-t előállítani az Üresjárati időkorlát alaphelyzetbe állításához. 

## <a name="discoveroutbound"></a>A virtuális gép által használt nyilvános IP-cím feltárása
A kimenő kapcsolatok nyilvános forrás IP-címének meghatározása számos módon megtörténik. A nyit egy olyan szolgáltatást biztosít, amely a virtuális gép nyilvános IP-címét jeleníti meg. 

Az nslookup parancs használatával DNS-lekérdezést küldhet a myip.opendns.com név számára a nyit feloldójának. A szolgáltatás visszaadja a lekérdezés küldéséhez használt forrás IP-címet. Ha a következő lekérdezést futtatja a virtuális gépről, a válasz az adott virtuális géphez használt nyilvános IP-cím:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>További lépések

- További információ a Resource Manager-alapú üzemelő példányok [Load Balancer](load-balancer-overview.md) használatáról.
- A Resource Manager-alapú üzemelő példányokon elérhető [kimenő kapcsolódási](load-balancer-outbound-connections.md) forgatókönyvekkel kapcsolatos tanulási mód.
