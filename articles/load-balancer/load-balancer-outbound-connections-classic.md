---
title: Kimenő kapcsolatok (klasszikus) Azure-ban |} Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan Azure lehetővé teszi, hogy a felhőalapú szolgáltatások nyilvános internetes szolgáltatásokkal kommunikálni.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: kumud
ms.openlocfilehash: f6452d8f88b91fe0cbf144ce951b84ba4cec0047
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="outbound-connections-classic"></a>Kimenő kapcsolatok (klasszikus)

Azure felhasználói telepítés a kimenő kapcsolatot biztosít számos különböző módon. A cikkből megtudhatja, Mik azok a helyzetek, amikor vonatkoznak, leírás és kezelésük módjával.

>[!NOTE]
>Ez a cikk csak klasszikus központi telepítéseket tartalmazza.  Felülvizsgálati [kimenő kapcsolatok](load-balancer-outbound-connections.md) minden erőforrás-kezelő központi telepítési forgatókönyve az Azure-ban.

A központi telepítés az Azure-végpontok kívül a nyilvános IP-címtér az Azure kommunikálhatnak. Amikor egy példányát a nyilvános IP-címtér lévő célra egy kimenő folyam kezdeményez, Azure dinamikusan rendeli a magánhálózati IP-cím egy nyilvános IP-cím. Ez a leképezés létrehozása után a kimenő kezdeményezésű áramlás forgalom is elérhető a magánhálózati IP-címet a folyamat származási helyének.

Azure forrás hálózati címfordítás (SNAT) használja ezt a funkciót. Több privát IP-címek vannak színleg mögött egyetlen nyilvános IP-címnek, az Azure használja [címfordítást (PAT) port](#pat) helyettesítő magánhálózati IP-címek számára. Elmúló port a PAT használja, és [előzetesen lefoglalt](#preallocatedports) készlet mérete alapján.

Egyszerre több [kimenő forgatókönyvek](#scenarios). Ezek a forgatókönyvek igény szerint kombinálhatja. Tekintse át alaposan, hogy a képességek, korlátozások és minták tájékozódjon a telepítési modell vonatkoznak és a forgatókönyvet. Tekintse át az útmutató a [forgatókönyvekben kezelése](#snatexhaust).

## <a name="scenarios"></a>Forgatókönyv áttekintése

Azure kimenő kapcsolat klasszikus központi telepítések eléréséhez három különböző módszert biztosít.  Nem minden klasszikus központi telepítések számára elérhető összes három forgatókönyv rendelkezik:

| Forgatókönyv | Módszer | IP-protokollok | Leírás | Webes feldolgozói szerepkör | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Virtuális gép példány szint nyilvános IP-címmel](#ilpip) | SNAT, színleg nem használt port | TCP, UDP, ICMP, ESP | Azure virtuális géphez hozzárendelt nyilvános IP-címet használ. A példány érhető el az összes rövid élettartamú port tartozik. | Nem | Igen |
| [2. nyilvános elosztott terhelésű végpont](#publiclbendpoint) | A port színleg (PAT) a nyilvános végpontnak SNAT | TCP, UDP | Azure több titkos végpont osztanak meg az nyilvános IP-cím nyilvános végpontot. Azure PAT használ a nyilvános végpont elmúló port. | Igen | Igen |
| [3. Önálló virtuális gép ](#defaultsnat) | A port színleg (PAT) SNAT | TCP, UDP | Azure automatikusan meg, míg a nyilvános IP-címnek a SNAT, a nyilvános IP-címet oszt meg a teljes telepítési és a nyilvános végpontot IP-cím elmúló port PAT használ. Ez egy webfarmos tartalék a fenti forgatókönyvek esetén. Nem ajánlott, ha figyelemmel kísérelhetik és vezérelhetik van szüksége. | Igen | Igen |

Ez a kimenő kapcsolat funkció érhető el az Azure Resource Manager üzembe helyezések egy részét.  

Különböző központi telepítésénél a Classic különböző funkcióit rendelkezik:

| Klasszikus üzembe helyezési | A funkció érhető el | 
| --- | --- |
| Virtuális gép | a forgatókönyv [1](#ilpip), [2](#publiclbendpoint), vagy [3](#defaultsnat) |
| Webes feldolgozói szerepkör | csak a forgatókönyv [2](#publiclbendpoint), [3](#defaultsnat) | 

[Megoldás stratégiák](#snatexhaust) is azonos eltéréseket.

A [előzetes lefoglalása elmúló port használt algoritmust](#ephemeralports) a klasszikus üzembe helyezés PAT ugyanúgy Azure Resource Manager erőforrás üzembe helyezések érték.

### <a name="ilpip"></a>1. forgatókönyv: Virtuális gép példány szint nyilvános IP-címmel

Ebben a forgatókönyvben a virtuális Gépnek legyen egy példány szint nyilvános IP (ILPIP) rendelve. Kimenő kapcsolatok illeti nem számít, hogy a virtuális gép elosztott terhelésű végpont rendelkezik, vagy nem. Ebben a forgatókönyvben élvez a többi. Egy ILPIP használata esetén a virtuális Gépet a ILPIP minden kimenő forgalom használ.  

Nyilvános IP-egy virtuális Géphez rendelt egy 1:1 kapcsolat (nem 1:many), és valósul meg állapotmentes 1:1 hálózati címfordítást.  A virtuális Gépnek legyen használható elmúló port, és nem használja a port színleg (PAT).

Ha az alkalmazás számos kimenő forgalom kezdeményez, és SNAT port Erőforrásfogyás tapasztal, érdemes lehet hozzárendelése egy [SNAT megkötések mérséklése ILPIP](#assignilpip). Felülvizsgálati [kezelése SNAT Erőforrásfogyás](#snatexhaust) egészében.

### <a name="publiclbendpoint"></a>2. forgatókönyv: Nyilvános elosztott terhelésű végpont

Ebben a forgatókönyvben a VM vagy a webes feldolgozói szerepkör társítva az elosztott terhelésű végpont egy nyilvános IP-címet. A virtuális gép nem rendelkezik a nyilvános IP-cím hozzárendelve. 

A terheléselosztással rendelkező virtuális Gépet egy kimenő folyam hoz létre, amikor Azure lefordítja a nyilvános IP-cím a nyilvános elosztott terhelésű végpont kimenő folyam titkos forrás IP-címét. Azure SNAT használja ezt a funkciót. Azure is használ [PAT](#pat) a helyettesítő több privát IP-cím, egy nyilvános IP-cím mögött. 

A load balancer nyilvános IP-cím előtér-rövid élettartamú port segítségével különböztetheti meg egymástól a virtuális gép által az egyes adatfolyamok. SNAT dinamikusan használ [előzetesen lefoglalt elmúló port](#preallocatedports) kimenő adatfolyamok létrehozásakor. Ebben a környezetben használt SNAT porttartomány SNAT portok nevezzük.

SNAT portok vannak előzetesen lefoglalt leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakasz. A véges erőforrása is nagyon gyorsan kimerítették fontosságúak. Fontos tudni, hogyan vannak [felhasznált](#pat). Annak megértése, hogyan tervezhet a a fogyasztás és szükség szerint, tekintse át a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

Ha [több nyilvános elosztott terhelésű végpont](load-balancer-multivip.md) létezik, az alábbi nyilvános IP-címek közül bármelyik egy [a kimenő forgalom jelölt](#multivipsnat), és egy véletlenszerűen kiválasztott.  

### <a name="defaultsnat"></a>3. eset: Nem nyilvános IP-cím társított

Ebben a forgatókönyvben a VM vagy webes feldolgozói szerepkör tagja nem egy nyilvános elosztott terhelésű végpont.  És a virtuális gépek esetén nincs hozzárendelve ILPIP-címnek. Ha a virtuális Gépet hoz létre egy kimenő folyam, Azure lefordítja a nyilvános IP-forráscím kimenő folyam titkos forrás IP-címét. A nyilvános IP-cím, a kimenő folyam használt nem konfigurálható, és nem tartoznak bele az előfizetés nyilvános IP-erőforrás korlátját ellen.  Azure automatikusan osztja ki ezt a címet.

Azure használ SNAT port színleg ([PAT](#pat)) Ez a függvény végrehajtásához. Ebben a forgatókönyvben hasonlít [2. forgatókönyv](#lb), azonban nem szabályozhatják a használt IP-cím van. Ez az a tartalék forgatókönyv esetén 1 és 2 nem léteznek. Ez a forgatókönyv nem ajánlott, ha azt szeretné, hogy a kimenő cím vezérelheti. Kimenő kapcsolatok az alkalmazáshoz kritikus részét képezik, ha egy másik helyzet kell választotta.

SNAT portok vannak előzetesen lefoglalt leírtak szerint a [ismertetése SNAT és a PAT](#snat) szakasz.  A virtuális gépek vagy webes feldolgozói szerepkörök megosztása a nyilvános IP-cím száma előzetesen lefoglalt elmúló port számát határozza meg.   Fontos tudni, hogyan vannak [felhasznált](#pat). Annak megértése, hogyan tervezhet a a fogyasztás és szükség szerint, tekintse át a [kezelése SNAT Erőforrásfogyás](#snatexhaust).

## <a name="snat"></a>SNAT és a PAT ismertetése

### <a name="pat"></a>Port színleg SNAT (PAT)

Ha egy központi telepítést hajt végre egy kimenő kapcsolat, minden kimenő kapcsolat forrása van írni. A forrás (a fent leírt forgatókönyvek alapján) telepítéshez társított nyilvános IP-cím a a magánhálózati IP-címtér van újraírja. A nyilvános IP-címtér 5 rekordos a folyamat (forrás IP-cím, forrásport, IP-átviteli protokoll, cél IP-címe, Célport) egyedinek kell lennie.  

Ennek elérése után írja át a magánhálózati IP-forráscím, mert több adatfolyamok egy egyetlen nyilvános IP-címről elmúló port (SNAT portok) segítségével. 

Egy SNAT port felhasznált ne egy egyetlen IP-címre, port és protokoll. Több adatfolyamok ugyanahhoz a cél IP-cím, port és protokoll, az egyes SNAT egyetlen portot használ fel. Ez biztosítja, hogy az adatfolyamok egyedi legyenek-e az azonos nyilvános IP-cím oly módon, és nyissa meg az azonos cél IP-cím, port és protokoll. 

Több adatfolyamok, egy másik cél IP-cím, port és protokoll, hogy mindegyik egyetlen SNAT port megosztása. A cél IP-cím, port és protokoll egyedivé adatfolyamok nincs szükség további Forráshelyek segítségével különböztetheti meg egymástól a nyilvános IP-címtér forgalmának portok.

SNAT port erőforrások elfogytak, amikor a kimenő forgalom sikertelen, amíg a meglévő forgalom kiadási SNAT portok. Terheléselosztó úgy szabadít fel SNAT portok az adatfolyam bezárása, és használja a [4 perces időtúllépést](#idletimeout) az üresjárati folyamok SNAT portok újraigénylési funkcióját.

Feltételeket, amelyek gyakran előfordulhat, hogy SNAT port Erőforrásfogyás mérséklése kombinációját, tekintse át a [kezelése SNAT](#snatexhaust) szakasz.

### <a name="preallocatedports"></a>Rövid élettartamú port előzetes lefoglalás port színleg SNAT (PAT)

Használatakor a háttérkészlet mérete alapján az algoritmus meghatározására száma előzetesen lefoglalt SNAT elérhető portok az Azure által használt port színleg SNAT ([PAT](#pat)). SNAT portjait elmúló port egy adott nyilvános IP-forráscím érhető el.

Azure preallocates SNAT portok Ha példánya van telepítve, hány virtuális gép vagy webes feldolgozói szerepkör-példány osztozik az egy adott nyilvános IP-cím alapján.  Kimenő forgalom létrehozásakor [PAT](#pat) dinamikusan használ fel (de előzetesen lefoglalt), majd felenged ezeket a portokat, a folyamat befejeződésekor vagy [az üresjárati időkorlát](#ideltimeout) fordulhat elő.

Az alábbi táblázat a SNAT port preallocations háttér címkészletet méretű rétege számára:

| Példányok | Előzetesen lefoglalt SNAT portok példányonként |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Ne feledje, hogy a rendelkezésre álló SNAT portok száma nem jelenti azt, közvetlenül az adatfolyamok száma. Egyetlen SNAT port a több célhoz rendelt egyedi felhasználhatók. Portok csak akkor, ha a forgalom egyedivé kell felhasználni. Tervezési és a megoldás útmutatásért tekintse át a részt kapcsolatos [kezelése a kimeríthető erőforrás](#snatexhaust) és a következő szakasz ismerteti [PAT](#pat).

A központi telepítés méretének módosítása hatással lehet a meglévő adatfolyamok némelyike. Ha a háttér-készlet mérete növekszik, és be a következő réteggel átmenetek, a előzetesen lefoglalt SNAT portok felének visszaigényelt vannak a következő nagyobb háttér címkészletet réteg történő áttelepítés során. Folyamatok, amelyek regenerált SNAT portot fog túllépi az időkorlátot, és létre kell hozni. Ha egy új módszer kísérlet történik, a folyamat sikeres lesz azonnal, mindaddig, amíg érhetők el előzetesen lefoglalt portok.

Ha a központi telepítési méret csökkenti, és az alacsonyabb szint közeledik, a rendelkezésre álló SNAT portok száma nő. Ebben az esetben meglévő lefoglalt SNAT portok, és a megfelelő adatfolyamok, nem érintettek.

SNAT port lefoglalását meghatározott IP-protokoll (TCP és UDP külön-külön karbantarthatók) és a következő feltételek kiadott:

### <a name="tcp-snat-port-release"></a>TCP SNAT port kiadás

- Ha mindkét kiszolgáló ügyfél küld FIN/ACK, SNAT port kiadjuk 240 másodperc múlva.
- Ha egy RST látható, SNAT port kiadjuk 15 másodperc után.
- üresjárati időtúllépés el lett érve.

### <a name="udp-snat-port-release"></a>UDP-SNAT port kiadás

- üresjárati időtúllépés el lett érve.

## <a name="problemsolving"></a> A probléma megoldásához 

Ez a szakasz segítséget nyújtanak az SNAT Erőforrásfogyás és a többi olyan forgatókönyvet, amely akkor fordulhat elő, a kimenő kapcsolatok az Azure-ban készült.

### <a name="snatexhaust"></a> SNAT (PAT) port Erőforrásfogyás kezelése
[Elmúló port](#preallocatedports) használt [PAT](#pat) egy kimeríthető erőforrás van, a [nem tartozó nyilvános IP-cím](#defaultsnat) és [nyilvános elosztott terhelésű végpont](#publiclbendpoint).

Ha tudja, hogy sok kimenő TCP vagy UDP-kapcsolata ugyanahhoz a cél IP-cím és port még kezdeményezése, és láthatja a kimenő kapcsolatok sikertelen vagy végigvitelével úgy, hogy Ön skálázását SNAT portok (előzetesen lefoglalt [elmúló portok](#preallocatedports) által használt [PAT](#pat)), több lehetőség közül választhat általános megoldás. Tekintse át ezeket a beállításokat, és határozza meg az elérhető és ajánlott a forgatókönyvéhez. Akkor lehet, hogy egy vagy több segítséget nyújtanak ebben a forgatókönyvben.

Ha problémája van a kimenő kapcsolat viselkedésének megértése, IP-verem statisztika (netstat) is használhatja. Vagy hasznos, és figyelje meg a kapcsolat viselkedés a csomag rögzíti.

#### <a name="connectionreuse"></a>Az alkalmazás kapcsolatok újból módosítása 
Igény szerinti elmúló SNAT újból felhasználja a kapcsolatot az alkalmazás által használt portok csökkentése érdekében. Ez különösen igaz protokollok, például a HTTP/1.1, ahol a kapcsolat újbóli az alapértelmezett beállítás. És egyéb protokollok (például REST) t a HTTP Protokollt használó pedig előnyei. 

Újbóli mindig jobb, mint az egyes, atomi TCP-kapcsolatok az egyes kérelmek. Használja fel további performant, nagyon hatékony TCP tranzakciók eredményez.

#### <a name="connection pooling"></a>Az alkalmazás használatához a kapcsolatkészletezést módosítása
A rendszer az alkalmazásban, ahol kérelmek belső elosztott kapcsolatokat (egyes újból felhasználja a ahol csak lehetséges) készletét kapcsolatkészlet is alkalmazhat. Ez a séma használatban elmúló port száma korlátozza, és a környezetet hoz létre a megfelelő. Ez a séma is növelheti a kapacitásának több egyidejű művelet tételével, amikor egyetlen kapcsolatból blokkolja-e a művelet a válasz.  

Kapcsolatkészletezést előfordulhat, hogy az alkalmazás vagy a konfigurációs beállítások, az alkalmazás fejlesztése segítségével keretében már létezik. A kapcsolat újbóli kapcsolatkészletezést kombinálhatja. A több kérelmek felhasználni a azonos cél IP-cím és port portok rögzített, előre jelezhető száma. A kérelmek is előnyös TCP tranzakciók, így csökkentve a késést és erőforrás-használat hatékony felhasználását. UDP-tranzakciók számára is hasznos, mert a számot, UDP-forgalom kezelésére is pedig kipufogógáz feltételek elkerülése és kezelheti a SNAT port kihasználtságának.

#### <a name="retry logic"></a>Az alkalmazást, hogy az kisebb agresszív újrapróbálkozási logika módosítása
Ha [előzetesen lefoglalt elmúló port](#preallocatedports) használt [PAT](#pat) vannak kimerült vagy alkalmazás hiba történik, túl szigorú vagy találgatásos kényszerített újrapróbálja decay nevű és leállítási logika miatt Erőforrásfogyás fordulhat elő, vagy megőrzése nélkül. Igény szerinti rövid élettartamú port egy kisebb agresszív újrapróbálkozási logika használatával csökkentése érdekében. 

Elmúló port (nem állítható) 4 perces üresjárati időtúllépés rendelkezik. Ha az újrapróbálkozások túl agresszív, az Erőforrásfogyás rendelkezik nincs lehetőség arra, hogy a saját törlődnek. Ezért annak eldöntéséhez, hogy hogyan--és milyen gyakran – az alkalmazás újrapróbálja tranzakciók a Tervező kritikus részét képezi.

#### <a name="assignilpip"></a>Egy példány szintű nyilvános IP-cím hozzárendelése minden virtuális gép
A forgatókönyvhöz a hozzárendelése egy ILPIP változik [példány szint nyilvános IP-cím egy virtuális géphez](#ilpip). A virtuális gép érhetők el az egyes virtuális gépek használt rövid élettartamú a nyilvános IP-portok. (Nem ahol rövid élettartamú port egy nyilvános IP-cím a megfelelő központi telepítési társított virtuális gépek megosztott forgatókönyvek.) Nincsenek kompromisszumot kell figyelembe venni, például a célgyűjtemény nagyszámú egyedi IP-címek engedélyezése.

>[!NOTE] 
>Ez a beállítás nem érhető el webes feldolgozói szerepköröket.

### <a name="idletimeout"></a>Segítségével a Keep Alive csomagok küldését a kimenő üresjárati időtúllépés

Kimenő kapcsolatok 4 perces üresjárati időtúllépés rendelkezik. Ez az időkorlát értéke nem állítható. Azonban használhatja (például TCP Keep Alive csomagok küldését) transport vagy alkalmazásréteg Keep-Alive csomagok küldését az üresjárati folyamat frissítése, és alaphelyzetbe állítja az üresjárati időtúllépés, ha szükséges.  Kérje meg a szállító csomagolt szoftverek ezt támogatja-e és az engedélyezéshez.  Általában csak egy oldalon kell Keep Alive csomagok küldését alaphelyzetbe állítja az üresjárati időkorlát létrehozásához. 

## <a name="discoveroutbound"></a>A nyilvános IP-cím egy virtuális gép használ felderítése
Számos módon határozza meg a nyilvános IP-forráscím egy kimenő kapcsolat. OpenDNS láthatja a nyilvános IP-címet a virtuális gép egy szolgáltatást biztosít. 

Az nslookup parancs használatával a OpenDNS feloldó elküldheti a név myip.opendns.com DNS-lekérdezést. A szolgáltatás az IP-forráscím kérés küldése használt adja vissza. A virtuális gépről a következő lekérdezés futtatásakor a rendszer a választ a nyilvános IP-címet használja ezt a virtuális gépet:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>További lépések

- További információ [terheléselosztó](load-balancer-overview.md) Resource Manager üzembe helyezések szerepel.
- Mód megismerése [kimenő kapcsolat](load-balancer-outbound-connections.md) forgatókönyvek Resource Manager üzembe helyezések érhető el.
