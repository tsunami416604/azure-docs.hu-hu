---
title: "Teljesítményfigyelő-megoldás az Azure Naplóelemzés hálózati |} Microsoft Docs"
description: "A hálózati teljesítmény figyelése az Azure Naplóelemzés a hálózatok a közelében real-alapú egyszeri közötti teljesítményének figyeléséhez megkönnyíti a nehezen észlelhető, és keresse meg a hálózati szűk keresztmetszetek."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: banders
ms.openlocfilehash: d5d5ec1b524fa455c8d2231c7c16fd7942f713c4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="network-performance-monitor-solution-in-log-analytics"></a>A hálózati Naplóelemzési Teljesítményfigyelő megoldás

![Hálózati Teljesítményfigyelő szimbólum](./media/log-analytics-network-performance-monitor/npm-symbol.png)

Ez a dokumentum ismerteti a telepítés, és hogy Naplóelemzési, amely segítséget nyújt a hálózatok a közelében real-alapú egyszeri közötti teljesítményének figyeléséhez észleli, és keresse meg a hálózati Teljesítményfigyelő megoldás hogyan hálózati szűk keresztmetszetek. A hálózati Teljesítményfigyelő megoldás segítségével figyelheti az adatvesztéssel és a két hálózatot, alhálózatot vagy a kiszolgálók közötti késés. Hálózati teljesítmény a figyelő észleli, például a forgalom blackholing útválasztási hibák és problémák hagyományos hálózati figyelési módszerek nem észleli a hálózati problémák. Hálózati Teljesítményfigyelő riasztásokat állít elő, és értesíti, amikor a küszöbérték megsérül, a hálózati kapcsolat. Ezeket a küszöbértékeket is ismernie kell automatikusan a rendszer, vagy egyéni riasztási szabályok használatával konfigurálhatók. Hálózati Teljesítményfigyelő időben történő észlelése érdekében a hálózati teljesítményproblémák biztosítja, és egy adott hálózati szegmens vagy az eszköz a probléma forrása localizes.

A megoldás irányítópultja, amely a legutóbbi hálózati állapotával kapcsolatos események, nem megfelelő állapotú hálózati kapcsolatok és alhálózati kapcsolat, amely használata során szembesülnek magas csomag veszteséget és késéseket hálózati összefoglaló információkat jelenít meg a hálózati problémák észlelését. Akkor is részletezés az alhálózati kapcsolat, valamint a csomópontok hivatkozások aktuális állapotának megtekintéséhez a hálózati kapcsolaton. A korábbi trendjét veszteséget és késéseket, a hálózat, az alhálózat és a csomópontok szintjén is megtekintheti. Átmeneti hálózati problémák észlelése korábbi trend diagramok csomag veszteséget és késéseket megtekintésével, és keresse meg a hálózati szűk keresztmetszeteket topológia-térképként. Az interaktív topológia graph lehetővé teszi a Ugrás által Ugrás hálózati útvonalak megjelenítheti, és határozza meg a probléma forrása. Más megoldások, például a naplóban keresse meg a különböző analytics követelmények hozhat létre a hálózati teljesítmény figyelése során gyűjtött adatok alapján egyéni jelentéseket.

A megoldás elsődleges mechanizmusaként szintetikus tranzakciók használatával deríti fel a hálózati hibákat. Ebben az esetben használhatja egy adott hálózati eszköz szállítójával, vagy a modell tekintet nélkül. A helyszíni, a felhőben (IaaS) és a hibrid környezetek között működik. A megoldás automatikusan észleli a hálózati topológia és a hálózat különböző útvonalak.

Tipikus hálózati figyelési termékek összpontosítani a hálózati eszközök (útválasztók, kapcsolók stb.) állapotának figyelését, de nem ad meg hálózati Teljesítményfigyelő viszont két pont közötti hálózati kapcsolat tényleges minőségének betekintést.

### <a name="using-the-solution-standalone"></a>A megoldás önálló használata
Ha szeretné a kritikus fontosságú munkaterhelésekhez közötti hálózati kapcsolat minőségének, hálózatok, adatközpontok vagy office helyek, akkor használhatja a hálózati Teljesítményfigyelő megoldás önmagában közötti kapcsolat állapotának figyeléséhez:

* több adatközpontban vagy office helyre, egy nyilvános vagy privát hálózaton keresztül csatlakozó
* kritikus fontosságú munkaterhelésekhez, üzletági alkalmazásokat futtató
* nyilvános felhőszolgáltatások, például a Microsoft Azure vagy Amazon Web Services (AWS) és a helyszíni hálózatokban, ha IaaS (virtuális) érhető el, és engedélyezi a kommunikációt a konfigurált átjárók rendelkezik helyszíni hálózatokhoz és a felhőalapú hálózatokhoz között
* Azure és a helyszíni hálózat Express Route használatakor

### <a name="using-the-solution-with-other-networking-tools"></a>A segítségével más hálózati eszközök
Ha szeretné figyelni a üzletági alkalmazás, használhatja a hálózati Teljesítményfigyelő megoldás kiegészítő megoldásként más hálózati eszközök. A lassú hálózati lassú alkalmazások vezethet, és hálózati Teljesítményfigyelő segítségével megvizsgálhatja a mögöttes hálózati probléma miatt fellépő alkalmazás teljesítményproblémákat. A megoldás nem igényel a hálózati eszközökhöz való hozzáférést, mert az alkalmazás-rendszergazda nem szükséges egy hálózati csapat nyújt tájékoztatást, hogy a hálózati alkalmazások érinti támaszkodnak.

Is ha már más hálózati eszközök figyelési fektet, majd a megoldás is kiegészíteni ezekhez az eszközökhöz, mert a hagyományos hálózati figyelési megoldások nem betekintést teljesítménymutatók végpontok közötti hálózati veszteségeket és késéseket hasonlóan.  A hálózati Teljesítményfigyelő megoldás segítségével töltse ki az adott térközét.

## <a name="installing-and-configuring-agents-for-the-solution"></a>A megoldás ügynökök telepítése és konfigurálása
A legfontosabb folyamatok segítségével telepíthet ügynököket a [Log Analyticshez való csatlakozás Windows számítógépek](log-analytics-windows-agent.md) és [csatlakozás az Operations Manager szolgáltatáshoz](log-analytics-om-agents.md).

> [!NOTE]
> Ahhoz, hogy kevés az adat a felderítésére és figyelésére a hálózati erőforrások legalább 2 ügynökök telepítése lesz szüksége. Ellenkező esetben a megoldás marad konfigurálása állapotban telepítése és konfigurálása további ügynökök.
>
>

### <a name="where-to-install-the-agents"></a>Az ügynökök telepítésének helyéről
Ügynökök telepítése előtt fontolja meg a hálózat és a figyelni kívánt milyen részei a hálózati topológia. Azt javasoljuk, hogy az egyes alhálózatokon, amelyek segítségével nyomon követni kívánt egynél több ügynök telepítése. Ez azt jelenti minden alhálózat, amely segítségével nyomon követni kívánt, válassza a kiszolgálók vagy a virtuális gépek két vagy több, és telepítse az ügynököt a őket.

Ha biztos a hálózat topológiáját, telepítse az ügynököt a kritikus fontosságú munkaterhelésekhez, ahol a hálózati teljesítmény figyelni kívánt kiszolgálókon. Például előfordulhat, hogy szeretne nyomon követjük, hogy a webkiszolgáló és az SQL Server rendszert futtató hálózati kapcsolatát. Ebben a példában kellene egy ügynököt telepít mindkét kiszolgálón.

Ügynökök figyelik a hálózati kapcsolat (hivatkozások) állomások nem magukat a gazdagépek között. Ezért ha figyelni szeretné a hálózati kapcsolat, telepítenie kell az ügynökök mindkét végponton, hogy a hivatkozás.

### <a name="configure-agents"></a>Ügynökök konfigurálása

Ha szeretne használni az ICMP protokoll szintetikus tranzakciók, kell ICMP megbízhatóan használatával a következő tűzfalszabályok engedélyezése:

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow
```


Ha azt tervezi, hogy a TCP protokollt kell nyissa meg a számítógépek számára, győződjön meg arról, hogy az ügynökök kommunikálhatnak tűzfalportjai. Töltse le és futtassa kell a [EnableRules.ps1 PowerShell-parancsfájl](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) egy PowerShell-ablakban rendszergazdai jogosultságokkal rendelkező paraméterek nélkül.

A parancsfájl hoz létre a hálózati teljesítményt figyelő által igényelt beállításkulcsokat, és létrehozza a Windows tűzfal szabályokat az ügynökök számára a TCP-kapcsolatok létrehozása egymás mellett. A beállításkulcsok hozta létre a parancsfájlt is megadhatja, hogy a naplófájl a hibakeresési naplók és a naplók fájl elérési útját. Az ügynök-kommunikációhoz használt TCP-portot is meghatározza. Ezek a kulcsok értékeit automatikusan beállítja a parancsfájl által, így nem kell manuálisan módosítani ezeket a kulcsokat.

A port, megnyílik az 8084. Egyéni portot is használhat a paraméter megadásával `portNumber` parancsfájlt. Azonban ugyanazt a portot kell használni az összes számítógépen ahol a parancsfájl futtatása.

> [!NOTE]
> A EnableRules.ps1 parancsfájl konfigurálja a Windows tűzfal-szabályok csak azon a számítógépen, ahol a parancsfájl futtatása. Ha egy hálózati tűzfalat, meg kell győződnie arról, hogy lehetővé teszi a hálózati teljesítményt figyelő által használt TCP-portot adatforgalmat.
>
>

## <a name="configuring-the-solution"></a>A megoldás konfigurálása
Az alábbi információk segítségével telepítse és konfigurálja a megoldást.

1. A hálózati Teljesítményfigyelő megoldás szerez adatokat futtató Windows Server 2008 SP 1 vagy újabb vagy Windows 7 SP1 vagy újabb, amelyek ugyanazok a követelmények vonatkoznak, a Microsoft Monitoring Agent (MMA). NPM ügynökök is futtathatja a Windows asztali/ügyfél operációs rendszereken (Windows 10, Windows 8.1, Windows 8 és Windows 7).
    >[!NOTE]
    >Az ügynökök a Windows server operációs rendszerek támogatják a protokollokat, szintetikus tranzakció TCP és az ICMP. Azonban az ügyféloldali Windows operációs rendszereken az ügynököket csak támogatja ICMP protokollt szintetikus tranzakció.

2. A hálózati Teljesítményfigyelő megoldás hozzáadása a munkaterület [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).<br><br> ![Hálózati Teljesítményfigyelő szimbólum](./media/log-analytics-network-performance-monitor/npm-symbol.png)  
3. Az OMS-portálon, megjelenik egy új csempe jelenik **hálózati Teljesítményfigyelő** üzenettel *megoldás további konfigurálást igényel*. Kattintson a csempére kattintva navigáljon a **telepítési** fülre, és válassza ki, hogy a hálózat figyelése szintetikus tranzakcióinak használni kívánt protokollt.  Felülvizsgálati [válassza ki a megfelelő protokoll-ICMP vagy TCP](#choose-the-right-protocol-icmp-or-tcp) segítségével válassza ki a megfelelő protokollt a hálózati meghatározáshoz.<br><br> ![a megoldáshoz szükségesek protokoll kiválasztása](media/log-analytics-network-performance-monitor/log-analytics-netmon-perf-welcome.png)<br><br>

4. A protokoll kiválasztása után a rendszer átirányítja a a **OMS áttekintése** lap. A megoldás összesíti az adatokat a hálózatról, amíg a hálózati Teljesítményfigyelő áttekintés csempe megjeleníti a következő üzenet *adatösszesítés folyamatban*.<br><br> ![megoldás az adatok összesítése](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-01.png)<br><br>
5. Miután az adatok gyűjtése és indexelt, az Áttekintés csempe módosítja, és jelzi, hogy végre kell hajtania a további konfigurációs.<br><br> ![megoldás csempe további konfigurációt igényel](media/log-analytics-network-performance-monitor/log-analytics-netmon-tile-status-02.png)<br><br>
6. Kattintson a csempére, majd indítsa el a következő lépésekkel megfelelő megoldás konfigurálását.

### <a name="create-new-networks"></a>Új hálózatok létrehozása
Egy hálózatot a hálózati Teljesítményfigyelőben az alhálózatok logikai tárolója. Hozzon létre egy hálózati egy rövid nevet, és az üzleti logika szerint további alhálózatokat. Például létrehozhat egy hálózati nevű *London* , és adja hozzá az összes alhálózata a londoni adatközpontban, illetve a hálózati nevű *ContosoFrontEnd* , és adja hozzá a Contoso nevű alkalmazás előtérrendszerét kiszolgáló összes alhálózata Ehhez a hálózathoz.
A konfiguráció lapon látni fogja, a hálózati nevű **alapértelmezett** a hálózatok lapon. Ha még nem hozott létre olyan hálózatra, az automatikusan felderített alhálózatok az alapértelmezett hálózati kerülnek.
Hálózatot hoz létre, amikor egy alhálózat hozzáadása, és alhálózaton eltávolítják az alapértelmezett hálózatról. Ha törli a hálózaton, az összes alhálózatot automatikusan visszatér az alapértelmezett hálózati.
Az alapértelmezett hálózati ebből kifolyólag minden olyan alhálózat nem található a felhasználó által definiált hálózati tárolója funkcionál. Nem szerkeszthetők vagy az alapértelmezett hálózat törlése. Mindig marad a rendszerben. Azonban létrehozhat annyi egyéni hálózatok szükség szerint.
A legtöbb esetben a szervezet alhálózatok egynél több hálózati kell összeállítani, és hozzon létre egy vagy több hálózatot az alhálózatok az üzleti logika szerint csoportosíthatja

#### <a name="to-create-a-new-network"></a>Új-hálózat létrehozása
1. Kattintson a **Hozzáadás hálózati** és írja be a hálózati nevét és leírását.
2. Válassza ki egy vagy több alhálózatból, és kattintson a **Hozzáadás**.
3. Kattintson a **mentése** a konfiguráció mentéséhez.<br><br> ![hálózat hozzáadása](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>Várjon, amíg adatösszesítés
Először a konfiguráció mentése után a megoldás elindul, a csomópontok, amelyen telepítve vannak-e az ügynökök közötti hálózati csomag veszteséget és késéseket adatok gyűjtése. Ez a folyamat eltarthat egy ideig, néha 30 perc. Ebben az állapotban során a hálózati teljesítmény figyelése csempe ismertető lapján jeleníti meg a következő üzenet *adatösszesítés folyamat*.

![adatösszesítés folyamatban](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

Amikor az adatok feltöltése, a hálózati teljesítmény figyelése csempe frissítése ábrázoló adatok jelenik meg.

![Hálózati teljesítmény figyelése csempe](./media/log-analytics-network-performance-monitor/npm-tile.png)

Kattintson a csempére a hálózati teljesítményt figyelő irányítópult megtekintéséhez.

![Hálózati teljesítmény figyelő irányítópult](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Alhálózatok figyelési beállításainak szerkesztése
Ha legalább egy ügynök telepítése minden alhálózat szerepel a **alhálózatai** a lap lapján.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Engedélyezheti vagy tilthatja le a figyelést adott alhálózatai
1. Válassza ki, vagy törölje a jelölését a **produkáló alhálózati azonosító** és ellenőrizze, hogy **figyelés használható** kijelölt vagy üres, szükség. Válassza ki, vagy törölje a több alhálózattal. Le van tiltva, az alhálózatok nem, az ügynök frissítve lesz, leállítja a pingelés más ügynökök figyeli.
2. Válassza ki a figyelni kívánt az adott alhálózat az alhálózat kiválasztásával a listából, és a szükséges csomópontok a nem figyelt és a felügyelt csomópontok tartalmazó listák közötti áthelyezése csomópontok.
   Hozzáadhat egy egyéni **leírás** az az alhálózat, tetszés szerint.
3. Kattintson a **mentése** a konfiguráció mentéséhez.<br><br> ![alhálózatának szerkesztése](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Válassza ki a csomópontok figyelése
A telepített ügynökkel rendelkező csomópontok találhatók a **csomópontok** fülre.

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Engedélyezheti vagy tilthatja le a csomópontok figyelése
1. Válassza ki, vagy törölje a figyelése, vagy állítsa le a figyelési kívánt csomópontokat.
2. Kattintson a **figyelés használható**, vagy törölje a jelölést, szükség szerint.
3. Kattintson a **Save** (Mentés) gombra.<br><br> ![csomópont figyelésének engedélyezése](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>Állapotfigyelési szabályainak beállítása
Hálózati Teljesítményfigyelő állapotával kapcsolatos események, amikor a 2 alhálózatok között, vagy 2 hálózatok közötti hálózati kapcsolatot a teljesítmény küszöbértéket megsérül állít elő. Ezeket a küszöbértékeket is ismernie kell automatikusan a rendszer, vagy megadhat egyéni küszöbértékeket.
A rendszer automatikusan létrehoz egy alapértelmezett szabályt, amely olyan állapotesemény, amikor elvesztése vagy bármely két hálózati/alhálózat közötti késés hivatkozásait megszegése hoz létre a rendszer által ismert küszöbértéket. Ez segít a megoldás figyeléséhez a hálózati infrastruktúra, amíg explicit módon nincsenek ellenőrzési szabályok. Ha engedélyezve van az alapértelmezett szabályt, a csomópontok szintetikus tranzakciók küldeni a többi csomópontnak, hogy engedélyezte a figyelésre. Az alapértelmezett szabályt akkor hasznos, esetén kis hálózatokhoz, például olyan forgatókönyvekben, ahol kis számú egy mikroszolgáltatási futtató kiszolgálók, és győződjön meg arról, hogy az összes o a kiszolgálók kapcsolódik szeretné egymással.

>[!NOTE]
>Javasoljuk, hogy letiltja az alapértelmezett szabályt, és hozzon létre egyéni figyelési szabályokat, különösen nagyméretű hálózatokhoz használata nagyszámú csomópontot a figyelés esetén. Ez csökkenti a megoldás és a figyelést a hálózatra rendezheti súgó által generált forgalmat.

Az üzleti logika szerint egyéni ellenőrzési szabályokat létrehozni. Például ha azt szeretné, a hálózati kapcsolat 2 office helyek headquarter teljesítményének figyeléséhez, majd csoportos office hely1 O1 hálózaton lévő összes alhálózata, office hely2 O2 hálózaton lévő összes alhálózata a H. létrehozása hálózati alkalmával a székhelyet mindig az összes alhálózata figyelés a szabályok egy az egyhez O1 és H között a másik O2 és H. között 2


#### <a name="to-create-custom-monitoring-rules"></a>Egyéni ellenőrzési szabályok létrehozása
1. Kattintson a **szabály hozzáadása** a a **figyelő** lapra, és írja be a szabály nevét és leírását.
2. Válassza ki a két hálózati vagy alhálózat hivatkozások figyelése a listából.
3. Először a hálózati legördülő listából válassza ki azt a hálózatot, amelyben az első alhálózat/s érdeklő tartalmazza, és a megfelelő alhálózati legördülő listából válassza ki az alhálózat/s.
   Válassza ki **összes alhálózatai** Ha azt szeretné, hogy a hálózati kapcsolaton minden alhálózatai figyelésére. Hasonlóképpen válassza ki a másik alhálózat/s iránt. És kattinthat **vegye fel kivételhiba** kizárása az adott alhálózat hivatkozások a kijelölés végrehajtott figyelését.
4. [ICMP és a TCP protokoll választhat](#choose-the-right-protocol-icmp-or-tcp) szintetikus tranzakciók hajthatók végre.
5. Ha nem kívánja a kijelölt, majd elemeinek állapotával kapcsolatos események törlése létrehozása **engedélyezése a hivatkozások a szabály által kezelt állapotfigyelés**.
6. Válassza ki a feltételek figyelése.
   Adhatja meg egyéni küszöbökkel állapotát az eseménygenerálás írja be a küszöbértékekhez. A feltétel értéke a megadott küszöbértéknél, a kijelölt hálózati/alhálózat pár fölé megy, amikor a rendszerállapot esemény jön létre.
7. Kattintson a **mentése** a konfiguráció mentéséhez.<br><br> ![Egyéni figyelési szabály létrehozása](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

Figyelési szabály mentése után integrálható, hogy a szabály Riasztáskezelési kattintva **riasztás létrehozása**. Riasztási szabály automatikusan létrejön a keresési lekérdezés és más szükséges paramétereket automatikusan kitöltött-a. Riasztási szabály használatakor is fogadhatja az e-mail alapú értesítések mellett a meglévő riasztást NPM belül. Riasztások is elindítható a runbookok javító műveleteket, vagy azok integrálható a meglévő szolgáltatási felügyeleti megoldások webhookok használatával. Kattinthat **kezelése riasztás** a riasztási beállítások konfigurálása.

### <a name="choose-the-right-protocol-icmp-or-tcp"></a>Válassza ki a megfelelő protokoll-ICMP vagy TCP

Hálózati teljesítmény figyelése (NPM) szintetikus tranzakciók használatával hálózati teljesítménymutatók, például a csomag és a kapcsolat késési kiszámításához. Ez jobb megértése, fontolja meg az NPM ügynök kapcsolódik a hálózati kapcsolat egyik végét. Az NPM ügynök mintavételi csomagokat küld egy második NPM ügynök kapcsolódik a hálózat egy másik végét. A második ügynök válaszok válasz csomagokkal. Ez a folyamat ismétlődik néhány alkalommal. Válaszok és minden válasz fogadásához szükséges idő mérésével az első NPM ügynök kapcsolat késési értékelésére, és az elveszett csomagokat.

A formátum, mérete és ezek a csomagok sorozatát a választott megfigyelési szabályok létrehozásakor protokoll határozza meg. A csomagok protokollon alapuló, a köztes hálózati eszközt (útválasztók, kapcsolók stb.) lehet, hogy másképp feldolgozni ezeket a csomagokat. Következésképpen a protokoll választott hatással van az eredmények pontosságának. És a protokoll választott azt is meghatározza, hogy bármely manuális lépéseket az NPM-megoldás telepítése után kell végrehajtania.

NPM biztosít a szintetikus tranzakciók végrehajtása ICMP és a TCP protokoll közötti választás.
ICMP szintetikus tranzakció szabály létrehozásakor válassza ki, ha az NPM ügynökök ICMP ECHO üzenetek használatával kiszámítja a hálózati késés és a csomagveszteség. ICMP ECHO használja ugyanazt az üzenetet, amely a hagyományos Ping eszköz által küldött. Ha TCP protokollt használja, a NPM ügynökök TCP SZIN csomag elküldése a hálózaton keresztül. Ide kerül a TCP-kézfogás befejezési és majd a RST csomagok használata a kapcsolat eltávolítása.

#### <a name="points-to-consider-before-choosing-the-protocol"></a>A protokoll kiválasztása előtt megfontolandó szempontok
Mielőtt használandó protokollt választja, vegye figyelembe a következőket:

##### <a name="discovering-multiple-network-routes"></a>Több hálózati útvonal felderítése
TCP pontosabb, ha több útvonal felderítéséhez és kevesebb ügynökök minden alhálózatban kell. Például egy vagy két ügynökök TCP használatával felderíthetők az alhálózatok közötti összes redundáns elérési utat. Azonban hogy az ICMP Protokollt használó hasonló eredmény elérése érdekében több ügynökök kell. ICMP, ha rendelkezik segítségével *N* száma több, mint 5 kell két alhálózat között útvonalak*N* ügynökök vagy a forrás-, vagy a cél alhálózaton.

##### <a name="accuracy-of-results"></a>Eredmények pontosságának
Útválasztók és kapcsolók általában alacsonyabb prioritású hozzárendelése ICMP ECHO csomagok TCP csomagok képest. Bizonyos helyzetekben előfordulhat Ha a hálózati eszközök van terhelve, jobban TCP által gyűjtött adatok tükrözi az adatvesztéssel és alkalmazások által tapasztalt késést. Ennek oka az, hogy az alkalmazás forgalom a legtöbb TCP protokollon keresztül zajlik. Ilyen esetekben ICMP TCP képest kevésbé pontos eredményeket nyújt.

##### <a name="firewall-configuration"></a>Tűzfal-konfiguráció
TCP protokollt igényel, hogy a TCP-csomagokat a célport kapnak. Az alapértelmezett port NPM-ügynökök által használt az 8084, de ez az ügynökök konfigurálásakor módosítható. Igen szükség győződjön meg róla, hogy a hálózati tűzfalak vagy az NSG-szabályok (az Azure-ban) forgalmat a porton keresztülhaladó. Szükség győződjön meg arról, hogy azokon a számítógépeken, amelyeken az ügynökök telepítve vannak a helyi tűzfal forgalmat engedélyezi ezt a portot a van konfigurálva.

Tűzfalszabályok konfigurálása a Windows rendszert futtató számítógépeken, de kell manuálisan konfigurálnia a hálózati tűzfal használhatja a PowerShell-parancsfájlokat.

Ezzel szemben ICMP nem működik a portot használja. A legtöbb vállalati környezetben ICMP-forgalmat engedélyezett a tűzfalon keresztül hálózati diagnosztika eszközök, például a Ping eszköz használatát teszik lehetővé. Így ha egy gép másik pingelhető, majd használhatja az ICMP protokoll tűzfal kézi konfigurálása nélkül.

> [!NOTE]
> Néhány tűzfalak blokkolhatják a ICMP, ami azt eredményezheti, hogy a biztonsági információk és az esemény a felügyeleti rendszer jelentős számú eseményt eredményezve újraküldési. Győződjön meg arról, hogy az Ön által nem blokkolva van által a hálózati tűzfal/NSG, ellenkező esetben NPM nem lesz képes a hálózati szegmenst figyelése.  Ebből kifolyólag javasoljuk TCP használhat a figyeléshez. Használjon ICMP forgatókönyvekben hol áll nem használhatják a TCP, például amikor:
> * Windows-alapú ügyfél-csomópontok, mivel TCP nyers sockets nem engedélyezettek a Windows ügyfél használ
> * A hálózati tűzfal/NSG blokkok TCP


#### <a name="how-to-switch-the-protocol"></a>Hogyan lehet váltani a protokoll

Ha a telepítés során ICMP használatát választotta, átválthat TCP bármikor az alapértelmezett szabály figyelési szerkesztésével.

##### <a name="to-edit-the-default-monitoring-rule"></a>Az alapértelmezett figyelési szabály szerkesztése
1.  Navigáljon a **hálózati teljesítmény** > **figyelő** > **konfigurálása** > **figyelő** majd **alapértelmezett szabály**.
2.  Görgessen a **protokoll** szakaszt, és válassza ki a használni kívánt protokollt.
3.  Kattintson a **mentése** a beállítást.

Akkor is, ha az alapértelmezett szabályt egy adott protokollt használ, a másik protokollal létrehozhat új szabályokat. Ha egyes szabályok ICMP, illetve egy másik használja TCP szabályok vegyesen is létrehozhat.


## <a name="data-collection-details"></a>Az gyűjtemény adatait
Hálózati Teljesítményfigyelő TCP SZIN-SYNACK-Nyugtázási kézfogás csomagok TCP van kiválasztva, és az ICMP ECHO ICMP ECHO REPLY használja, amikor az ICMP protokollt van kiválasztva veszteséget és késéseket kapcsolatos információk összegyűjtéséhez. Traceroute is használt topológia lekérése.

A következő táblázat adatgyűjtési módszerek és egyéb adatok gyűjtése hogyan hálózati Teljesítményfigyelő részleteit.

| Platform | Közvetlen ügynök | SCOM-ügynököt | Azure Storage | SCOM szükséges? | Felügyeleti csoport keresztül küldött SCOM ügynök adatok | Gyűjtemény gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP kézfogások/ICMP ECHO üzenetek 5 másodpercentként adatok küldése át 3 percenként |

A megoldás szintetikus tranzakciók használja fel a hálózat állapotát. OMS-ügynököt egymással (attól függően, hogy a protokoll jelölni figyelésre) a hálózati exchange TCP- vagy ICMP Echo a különböző ponton telepíteni. A folyamat során ügynökök üzenetváltási időt és a csomag elvesztését, megtudhatja, ha van ilyen. Minden ügynök rendszeres időközönként is végez a különböző útvonalakat, meg kell vizsgálni a hálózaton található más ügynökök egy nyomkövetési útvonalat. Ezen adatok használatával, az ügynökök is kikövetkeztetni a hálózati késés és a csomag bontásban. A tesztek összes öt másodpercenként, és az adatok szerint van összesítve három perces időtartamra az ügynökök a Naplóelemzés szolgáltatáshoz való feltöltés előtt meg kell ismételni.

> [!NOTE]
> Ügynökök gyakran egymással kommunikálnak, de azok nem hoznak létre nagy mennyiségű hálózati forgalmat a tesztek végrehajtása közben. Ügynökök csak a TCP SZIN-SYNACK-Nyugtázási kézfogás csomagok a veszteséget és késéseket – nincsenek csomagok továbbított adatok meghatározásához támaszkodnak. A folyamat során ügynökök kommunikálnak egymással csak szükség esetén, és az ügynök kommunikációs topológiát arra optimalizálták, hálózati forgalom csökkentése érdekében.
>
>

## <a name="using-the-solution"></a>A megoldás használata
Ez a szakasz ismerteti az irányítópult funkciók és a használatukat.

### <a name="solution-overview-tile"></a>Megoldás áttekintés csempe
Miután engedélyezte a hálózati Teljesítményfigyelő megoldás, a megoldás csempe az OMS áttekintése lapon gyors áttekintést nyújt az a hálózat állapotát. A perecdiagram megfelelő és nem megfelelő állapotú alhálózati kapcsolat számát jeleníti meg. A csempére kattint, a megoldás irányítópultja nyílik meg.

![Hálózati teljesítmény figyelése csempe](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>Hálózati Teljesítményfigyelő megoldás irányítópultja
A **hálózati összefoglaló** panel a hálózatok és a relatív méretük összegzését jeleníti meg. A hálózati kapcsolatokon, az alhálózati hivatkozások és az elérési út teljes száma (az IP-címek ügynökkel két gazdagépek és a közöttük útválasztók ugrásainak áll egy elérési utat) a rendszer megjelenítő csempék követi.

A **felső hálózati állapotával kapcsolatos események** panel a rendszer és az idő a legutóbbi állapotával kapcsolatos események és riasztások listáját tartalmazza a óta módosították, hogy az esemény aktív. A rendszerállapot-esemény vagy riasztás jön létre, amikor csomagvesztés vagy a hálózati vagy alhálózat hivatkozás várakozási ideje meghaladja a küszöbértéket.

A **felső nem megfelelő állapotú hálózati kapcsolatok** panelen lévő nem megfelelő állapotú hálózati kapcsolatok listáját jeleníti meg. Ezek azok a hálózati kapcsolat, amelyek egy vagy több káros állapotesemény azokat a pillanatban.

A **felső alhálózati kapcsolat legtöbb adatvesztés** és **alhálózati kapcsolat és a késleltetés** paneleken megjelenítése a felső alhálózati kapcsolat csomagvesztés és alhálózati kapcsolat rendre felső késés korlátozza. Nagy késleltetésű vagy bizonyos csomagvesztés várható bizonyos hálózati kapcsolatokról. Ilyen kapcsolatok a felső tíz listán, de nem különbözteti meg a nem megfelelő.

A **közös lekérdezések** panel, amely a nyers hálózati figyelési közvetlenül az adatok lehívása keresési lekérdezések készletét tartalmazza. Ezeket a lekérdezéseket kiindulási pontként használható a saját testreszabott jelentéskészítéshez lekérdezések létrehozásáról.

![Hálózati teljesítmény figyelő irányítópult](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>Részletezés az mélysége
A megoldás irányítópultot leásási mélyebb különböző mutató hivatkozások be bármely területekre érdeklő gombra. Például amikor megjelenik egy figyelmeztetés vagy egy nem megfelelő állapotú hálózati kapcsolat megjelenjen az irányítópulton, kattinthat, hogy további vizsgálat. Megnyílik egy oldal, amely tartalmazza az adott hálózati kapcsolat minden alhálózati kapcsolat. Minden alhálózati kapcsolat, a késés és egészségügyi állapotának látni fogja, és megtudhatja, milyen alhálózati kapcsolat gyors okozza a problémát. Ezután kattintson **csomóponti kapcsolat megtekintése** a nem megfelelő állapotú alhálózati kapcsolat csomópont hivatkozások megtekintéséhez. Ezután tekintse meg az egyes csomópontok hivatkozásokat, és a nem megfelelő állapotú csomóponti kapcsolat található.

Kattinthat **nézet topológia** az útvonalakat a forrás és cél csomópontok között a Ugrás-hop topológiája megtekintéséhez. A nem megfelelő útvonalakat vagy ugrások piros, hogy gyorsan azonosíthatja a problémát az a hálózat egy adott részének láthatók.

![részletes adatok](./media/log-analytics-network-performance-monitor/npm-drill.png)

### <a name="network-state-recorder"></a>Hálózati állapotát rögzítő

Minden a nézet jeleníti meg, időben pillanatképet a hálózat állapotát az adott helyen. Alapértelmezés szerint a legutóbbi állapot jelenik meg. Az a lap tetején látható a pont, amelynek az állapot jelenik meg időben. Dönthet úgy, visszamehetnek az időben, és a hálózati állapotfigyelő pillanatképe megtekintéséhez kattintson a sáv a **műveletek**. Választhatja azt is, engedélyezni vagy letiltani a lapok automatikus frissítésének, amíg a legfrissebb állapotának megtekintése.

![hálózati állapota](./media/log-analytics-network-performance-monitor/network-state.png)

#### <a name="trend-charts"></a>Trend diagramok
Minden szinten, hogy részletes, veszteséget és késéseket hálózati kapcsolat trendjét látható. Trend diagramok alhálózat és a csomópont hivatkozások is elérhetők. Módosíthatja a diagramot úgy, hogy a vezérlő tetején található a diagram használatával megrajzolásához időintervallumát.

Trend diagramok egy korábbi szempontjából a hálózati kapcsolaton teljesítményének megjelenítése. Egyes hálózati probléma átmeneti jellegű, és dolgozza fel a hálózat aktuális állapotának megtekintésével csak nehéz lenne. Ez az az oka a problémák gyorsan surface eltűnnek, mielőtt bárki megjegyzések csak olyan időpontra később újra. Ilyen átmeneti probléma is nehézkes lehet alkalmazás-rendszergazdák számára, mert azok gyakran felület ad ki a az alkalmazás válaszideje, még akkor is, ha az összes alkalmazás-összetevők a jelek szerint zökkenőmentes megtévesztő növekedése.

Ha megnézi a trend diagram, ahol a probléma megjelenik egy hirtelen csúcs, hálózati késés és a csomagveszteség könnyen képes észlelni az ilyen típusú problémák.

![Trend diagram](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Ugrás az Ugrás által topológia térkép
Teljesítményfigyelő hálózati útvonalak egy interaktív topológia-térképként két csomópont között a Ugrás-hop topológiája látható. Jelölje ki a csomópont hivatkozást, majd kattintson a topológia térkép megtekintheti **nézet topológia**. Emellett megtekintheti a topológia térkép kattintva **elérési utak** csempére az irányítópulton. Amikor rákattint **elérési utak** az irányítópulton, konfigurálnia kell a forrás és cél csomópontok válasszon a bal oldali panelen, majd kattintson **rajzot** megrajzolásához az útvonalakat a két csomópont között.

A topológia térképen jeleníti meg, hány útvonalak között két csomópont, és mit kell tenni az adatcsomagok elérési utak. Hálózati teljesítmény szűk keresztmetszetek a topológia-térképként piros lesznek megjelölve. Hibás hálózati kapcsolat vagy egy hibás hálózati eszköz is keresse meg a topológia-térképként piros színes elemek felmérésével.

Kattintson a csomópont vagy rámutatáskor felett a topológia-térképként, helyezésekor itt látható a teljes tartománynév és IP-címnek a csomópont tulajdonságait. Kattintson egy Ugrás meg IP-címet. Ha szeretné, adott útvonalak szűrésére összecsukható műveleti ablaktábláján a szűrők segítségével. És a hálózati topológia a csúszkával műveleti ablaktábláján ugrások elrejtésével is egyszerűsíthető. Nagyítás növelésére, de az egér kerekének használatával a topológia térkép ki.

Vegye figyelembe, hogy a topológia látható módon a térkép 3 rétegbeli topológia, és nem tartalmazza 2. rétegbeli eszközök és kapcsolatok.

![Ugrás az Ugrás által topológia térkép](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Hibák megkeresése
Hálózati Teljesítményfigyelő anélkül, hogy a hálózati eszközök a hálózati szűk keresztmetszeteket találja. A hálózatról, és a hálózati grafikonon speciális algoritmusok alkalmazásával összegyűjtött adatokon alapulnak, hálózati Teljesítményfigyelő lehetővé teszi a hálózat részei, amelyek valószínűleg probabilisztikus becslése a probléma forrása.

Ez a megközelítés célszerű határozható meg, hogy a hálózati szűk keresztmetszeteket ugrások való hozzáférés nem érhető el, mert nem igényel a hálózati eszközök, például az útválasztók és kapcsolók összegyűjteni kívánt adatokat. A akkor is ez hasznos, ha az útválasztók ugrásainak két csomópont között nem szerepelnek a rendszergazdai felügyeletet. Például az útválasztók ugrásainak Internetszolgáltató útválasztók lehet.

### <a name="log-analytics-search"></a>A Naplóelemzési keresése
Minden adat, amely grafikusan elérhetővé a hálózati Teljesítményfigyelő irányítópulton keresztül történő és leásási oldalon érhető el natív módon Naplóelemzési keresési. A keresési lekérdezés nyelvének használata a lekérdezést, és egyéni jelentések készítése az adatok exportálása az Excel vagy a Power bi által. A **közös lekérdezések** panelen az irányítópult tartalmaz néhány hasznos lekérdezést, amely kiindulási pontként használható a saját lekérdezések és jelentések létrehozása.

![keresési lekérdezések](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>A rendszerállapot-riasztások alapvető okát
Most, hogy tekintse át a hálózati teljesítmény figyelése, vizsgáljuk meg egy olyan állapotesemény kiváltó okának egyszerű vizsgálatot.

1. Az Áttekintés lap jelenik meg a hálózat állapotának gyors pillanatképe betartásával a **hálózati Teljesítményfigyelő** csempére. Figyelje meg, hogy a figyelt 6 alhálózatai hivatkozásokat, kívül 2 sérült állapotban. Ez garantálja, hogy a vizsgálat. Kattintson a csempére a megoldás irányítópult megtekintéséhez.<br><br> ![Hálózati teljesítmény figyelése csempe](./media/log-analytics-network-performance-monitor/npm-investigation01.png)  
2. Az alábbi példa képen láthatja, hogy nincs olyan állapotesemény egy sérült hálózati kapcsolat. Vizsgálja meg a problémát, majd kattintson a mellett dönt a **DMZ2-DMZ1** hálózati kapcsolat a legfelső szintű a probléma megállapításához.<br><br> ![nem megfelelő állapotú hálózati kapcsolat, példa](./media/log-analytics-network-performance-monitor/npm-investigation02.png)  
3. A részletező lapon látható az alhálózati kapcsolat **DMZ2-DMZ1** hálózati kapcsolat. Láthatja, hogy mindkét az alhálózati kapcsolat a késési elért a küszöbértéket, így a hálózati kapcsolat nem kifogástalan. Az alhálózati kapcsolat a késési trendjeit is megtekinthető. Használhatja az időbeállítást-vezérlés a diagramot úgy, hogy a szükséges időtartomány összpontosítanak. Ha a késés elérte a maximális szakában tekintheti meg. A naplókat a probléma kivizsgálása ebben az időszakban később rá tud keresni. Kattintson a **csomóponti kapcsolat megtekintése** leásási további számára.<br><br> ![nem megfelelő állapotú alhálózati hivatkozások – példa](./media/log-analytics-network-performance-monitor/npm-investigation03.png) 
4. Hasonló az előző lapra, a részletezés az adott alhálózat hivatkozás laplistákhoz le a bennük foglalt csomóponti kapcsolat. Hasonló műveletek végezhetők itt, ahogyan az előző lépésben. Kattintson a **nézet topológia** megtekintéséhez a topológiát a 2-csomópontok között.<br><br> ![nem megfelelő állapotú csomóponti hivatkozások – példa](./media/log-analytics-network-performance-monitor/npm-investigation04.png)  
5. A 2 kijelölt csomópontok közötti összes elérési utat ábrázolási a topológia a térképen. A topológia-térképként két csomópont között útvonalak a Ugrás-hop topológiája jelenítheti meg. Biztosít a hány útvonalak léteznek a két csomópontot, és milyen közötti világossá elérési utak az adatcsomagok tart. Hálózati teljesítmény szűk piros színnel jelöli. Hibás hálózati kapcsolat vagy egy hibás hálózati eszköz is keresse meg a topológia-térképként piros színes elemek felmérésével.<br><br> ![a nem megfelelő topológia nézet – példa](./media/log-analytics-network-performance-monitor/npm-investigation05.png)  
6. Az adatvesztés, a késés és az egyes elérési utakat az ugrások számát tekinthetők át a **művelet** ablaktáblán. A görgetősáv segítségével ezeket a nem megfelelő elérési utak részleteinek megtekintéséhez.  A szűrők segítségével válassza ki a nem megfelelő állapotú Ugrás az elérési utakat, hogy csak a kiválasztott elérési utak topológiájának ábrázolja. Az egér kerekének segítségével nagyítása vagy kicsinyítése a topológia leképezés.

   Az a kép alatt egyértelműen megtekintheti az adott részt a a hálózaton a problémás területek okozza az elérési útja és a piros szín ugrások megtekintésével. Kattintson a csomópont a topológia a térképen tárja fel a csomópont teljesen minősített Tartománynevét, beleértve a tulajdonságok és IP-címet. Az IP-címét a hop kattintva jeleníti meg.<br><br> ![a nem megfelelő topológia – példa az útvonalra részletei](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="provide-feedback"></a>Visszajelzés küldése

- **UserVoice** -ötleteit azt szeretné, hogy működik a hálózati Teljesítményfigyelő szolgáltatások vannak, beküldheti. Látogasson el a [UserVoice lap](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring).
- **Csatlakozás a kohorszok** -még mindig a kohorszok csatlakozás új ügyfelek rendelkező iránt érdeklődik. Része, akkor kell új szolgáltatások korai eléréséhez, és Hálózatfigyelő teljesítményének javítása érdekében. Érdekli való csatlakozás, ha kitöltés kibővített ez [gyors felmérés](https://aka.ms/npmcohort).

## <a name="next-steps"></a>Következő lépések
* [Naplók keresése](log-analytics-log-searches.md) részletes hálózati teljesítmény rekordok megtekintéséhez.
