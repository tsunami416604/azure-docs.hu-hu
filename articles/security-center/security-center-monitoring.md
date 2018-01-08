---
title: "Biztonságfigyelés az Azure Security Centerben | Microsoft Docs"
description: "Ez a cikk az Azure Security Center figyelési funkcióinak használatába nyújt bevezetést."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/28/2017
ms.author: yurid
ms.openlocfilehash: a14528013b34b912f4f2e1bc07094c7b20a0f63c
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="security-health-monitoring-in-azure-security-center"></a>Biztonsági állapotfigyelés az Azure Security Centerben
Ez a cikk az Azure Security Center figyelési funkcióknak használatához nyújt segítséget a szabályzatoknak való megfelelés ellenőrzése érdekében.

## <a name="what-is-security-health-monitoring"></a>Mi az a biztonsági állapotfigyelés?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. A biztonságfigyelés ezzel szemben proaktív stratégiát jelent, amely az erőforrásokat ellenőrizve azonosítja a vállalati szabványoknak vagy ajánlott eljárásoknak meg nem felelő rendszereket.

## <a name="monitoring-security-health"></a>A biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](security-center-policies.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők. Azon virtuális gépek és számítógépek számától függően, amelyeken az ügynök telepítve van, a virtuális gépek és a számítógép konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információk összegyűjtésére körülbelül egy órát kell várni. A **Megelőzés** szakasz területén megtekintheti az erőforrások biztonsági állapotát és az esetleges problémákat. A problémák listáját a **Javaslatok** csempén is megtekintheti.

A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](security-center-recommendations.md).

A **Megelőzés** szakaszban is nyomon követheti erőforrásainak biztonsági állapotát. Az alábbi példában azt láthatja, hogy minden egyes erőforrás csempéjén (Számítás, Hálózat, Tárolás és adatok, Alkalmazás) megtalálható az azonosított hibák összes száma.

![A Resources security health (Erőforrások biztonsági állapota) csempe](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>Számítási tevékenység figyelése
A **Számítás** csempére kattintva a következő három lap jelenik meg:

- **Áttekintés**: figyelés és javaslatok.
- **Virtuális gépek és számítógépek**: az összes virtuális gép és számítógép, valamint azok aktuális biztonsági állapotának listája.
- **Felhőszolgáltatások**: a Security Center által figyelt összes webes és feldolgozói szerepkör listája.

![Hiányzó rendszerfrissítések virtuális gépenként](./media/security-center-monitoring/security-center-monitoring-fig1-sep2017.png)

Minden egyes lap esetében több szakaszt hozhat létre, és az egyes szakaszokban elérhető lehetőségek kiválasztásával további részleteket tudhat meg az adott probléma megoldásához szükséges lépésekről.

#### <a name="monitoring-recommendations"></a>Figyelési javaslatok
Ebben a szakaszban tekintheti meg az összes automatikus üzembe helyezésre beállított virtuális gépet és számítógépet, valamint azok aktuális állapotát. Ha erre a bejegyzésre kattint, megnyílik **A Monitoring Agent állapotproblémái** panel.

![A Monitoring Agent állapotproblémái](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)

Itt szerepelnek azok a virtuális gépek és számítógépek, amelyeket a Security Center nem tud sikeresen monitorozni. Részletes információkért válasszon ki egy virtuális gépet vagy számítógépet. A **FIGYELÉS ÁLLAPOTA** határozza meg a problémát.

#### <a name="recommendations"></a>Javaslatok
Ebben a szakaszban az Azure Security Center által megfigyelt [virtuális gépekre és számítógépekre vonatkozó javaslatokat](security-center-virtual-machine-recommendations.md) olvashat. Az első oszlop a javaslatokat sorolja fel. A második oszlop az adott javaslat által érintett virtuális gépek és számítógépek számát tartalmazza. A harmadik oszlop a probléma súlyosságát mutatja, ahogyan az alábbi képernyőfelvételen látható:

![Virtual machine recommendations (A virtuális gépre vonatkozó javaslatok)](./media/security-center-monitoring/security-center-monitoring-fig2-sep2017.png)

> [!NOTE]
> A **Hálózatkezelés állapota** panel **Hálózati topológia** listájában kizárólag a legalább egy nyilvános végponttal rendelkező virtuális gépek jelennek meg.
>

Az egyes javaslatokra kattintva különböző műveleteket végezhet el. Ha például a **Hiányzó rendszerfrissítések** elemre kattint, megtekintheti azoknak a virtuális gépeknek és számítógépeknek a listáját, amelyekről frissítések hiányoznak, valamint a hiányzó frissítés súlyosságát, ahogy az alábbi képernyőfelvételen látható:

![Virtuális gépek hiányzó rendszerfrissítései](./media/security-center-monitoring/security-center-monitoring-fig9-sep2017.png)

A **Hiányzó rendszerfrissítések** grafikus formátumban összegzi a kritikus frissítéseket, az egyik részben a Windows, a másik részben pedig a Linux rendszerre vonatkozóan. A második részben az alábbi információkat tartalmazó táblázat található:

* **NÉV** – A hiányzó frissítés neve.
*  **VIRTUÁLIS GÉPEK ÉS SZÁMÍTÓGÉPEK SZÁMA**: Azon virtuális gépek és számítógépek teljes száma, amelyek esetében ez a frissítés hiányzik.
* **STATE** (Állapot): a javaslat aktuális állapota:
  * **Open** (Nyitott): a javaslattal egyelőre még nem foglalkoztak.
  * **In Progress** (Folyamatban): folyamatban van a javaslat alkalmazása az érintett erőforrásokra, további lépésekre nincs szükség.
  * **Resolved** (Megoldott): a javaslat alkalmazása megtörtént. (A probléma megoldása után a bejegyzés halványan jelenik meg.)
* **SEVERITY** (Súlyosság): az adott javaslat súlyosságát határozza meg:
  * **High** (Magas): biztonsági rés található egy fontos erőforrásnál (alkalmazás, virtuális gép, hálózati biztonsági csoport) és beavatkozást igényel.
  * **Medium** (Közepes): nem kritikus vagy kiegészítő lépések elvégzése szükséges egy folyamat befejezéséhez vagy egy biztonsági rés megszüntetéséhez.
  * **Low** (Alacsony): a biztonsági rést be kell tömni, de a probléma nem igényel azonnali beavatkozást. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)

A javaslat részleteinek megtekintéséhez kattintson a hiányzó frissítés nevére a listában.

![Adott virtuális gép hiányzó rendszerfrissítései](./media/security-center-monitoring/security-center-monitoring-fig4-sep2017.png)

> [!NOTE]
> Itt ugyanazok a biztonsági javaslatok láthatók, mint a **Javaslatok** lehetőség esetében. A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](security-center-recommendations.md). Ez nem csupán a virtuális gépekre vonatkozik, hanem az összes olyan erőforrásra és számítógépre, amely szerepel az **Erőforrás állapota** csempén.
>

#### <a name="vms--computers-section"></a>Virtuális gépek és számítógépek szakasz
A virtuális gépekre és számítógépekre vonatkozó szakaszban áttekintheti az összes virtuális gépet és számítógépet, valamint a rájuk vonatkozó javaslatokat. Ahogy az alábbi képernyőfelvételen is látható, minden oszlop egy javaslatcsoportot képvisel:

![Az összes virtuális gép és javaslat áttekintése](./media/security-center-monitoring/security-center-monitoring-fig5-sep2017.png)

A listában négyféle ikon szerepel, az alábbiak szerint:

![icon1](./media/security-center-monitoring/security-center-monitoring-icon1.png) Nem Azure-alapú számítógép.

![icon2](./media/security-center-monitoring/security-center-monitoring-icon2.png) Azure Resource Manager-alapú virtuális gép.

![icon3](./media/security-center-monitoring/security-center-monitoring-icon3.png) Klasszikus Azure virtuális gép.

![icon4](./media/security-center-monitoring/security-center-monitoring-icon4.png) Azok a virtuális gépek, amelyek csak a megtekintett előfizetés részét képező munkaterület alapján azonosít a rendszer. Ezek közé tartozhatnak olyan, más előfizetésekből származó virtuális gépek is, amelyek a jelen előfizetéshez tartozó munkaterületnek vannak alárendelve, valamint az SCOM közvetlen ügynök használatával telepített, erőforrás-azonosítóval nem rendelkező virtuális gépek.

Az egyes javaslatok alatt megjelenő ikonok segítenek gyorsan azonosítani az intézkedést igénylő virtuális gépeket és számítógépeket, valamint a javaslat típusát. A **Szűrő** lehetőség használatával kiválaszthatja, hogy mely lehetőségek jelenjenek meg a képernyőn.

![Szűrés](./media/security-center-monitoring/security-center-monitoring-fig6-sep2017.png)

Az előző példában az egyik virtuális gép végpontvédelmével kapcsolatban a rendszer kritikus fontosságú javaslatot tett közzé. Ha további információt szeretne kapni a virtuális gépről, kattintson rá:

![Virtuális gépek biztonsági információi](./media/security-center-monitoring/security-center-monitoring-fig7-sep2017.png)

Itt láthatja a virtuális gépre vagy a számítógépre vonatkozó biztonsági információkat. A panel alján a javasolt műveleteket, valamint az egyes problémák súlyosságát láthatja.

#### <a name="cloud-services-section"></a>Cloud Services szakasz
A Cloud Services-szolgáltatások esetében a rendszer abban az esetben hozza létre a javaslatot, ha az operációs rendszer verziója elavult, ahogyan az alábbi képernyőfelvételen látható:

![Felhőszolgáltatások állapotinformációi](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

Olyan esetekben, ahol valóban rendelkezik javaslattal (az előző példa nem ilyen esetet mutat be), a javaslatban megadott lépéseket követve frissítheti az operációs rendszer verzióját. Ha egy frissítés elérhetővé válik, a rendszer riasztást jelenít meg (a probléma súlyosságától függően vörös vagy narancssárga riasztást). Ha a WebRole1 webes vagy a WorkerRole1 feldolgozói szerepkör riasztására kattint (mindkettő az IIS-re automatikusan telepített webalkalmazással együtt futtatja a Windows Servert), akkor további részleteket láthat az adott javaslatról, ahogyan az alábbi képernyőfelvételen is látható:

![Felhőszolgáltatás adatai](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Ha előírásszerűbb magyarázatot kíván megtekinteni erről a javaslatról, a **DESCRIPTION** (LEÍRÁS) oszlopban kattintson az **Update OS version** (Operációs rendszer verziójának frissítése) elemre.

![Felhőszolgáltatásokkal kapcsolatos javaslatok](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>Virtuális hálózatok figyelése
A **Hálózat** csempére kattintva megnyílik az alábbi képernyőképen látható **Hálózat** panel, amelyen további információk érhetők el:

![Hálózat panel](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Hálózatokra vonatkozó javaslatok
A virtuális gépek erőforrás-állapotára vonatkozó információkhoz hasonlóan a képernyő felső részén a problémák összefoglaló listája látható, alább pedig a megfigyelt hálózatok listája.

A hálózati állapotot részletező listában megtekintheti a potenciális biztonsági problémákat, valamint az ezekre vonatkozó [javaslatokat](security-center-network-recommendations.md). Problémát jelenthetnek például a következők:

* Nincs telepítve újgenerációs tűzfal (NGFW)
* Az alhálózatokon nincsenek bekapcsolva a hálózati biztonsági csoportok
* Az virtuális gépeken nincsenek bekapcsolva a hálózati biztonsági csoportok
* Külső hozzáférés korlátozása nyilvános külső végponton keresztül
* Megfelelő állapotú internet felé néző végpontok

Ha egy javaslatra kattint, az alábbi példában látható módon további részleteket tekinthet meg a javaslatról:

![Az adott javaslat részletei a Hálózat panelen](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

Ebben a példában a **Configure Missing Network Security Groups for Subnets** (Hiányzó hálózati biztonsági csoportok konfigurálása az alhálózatok számára) panelen láthatja azoknak az alhálózatoknak és virtuális gépeknek a listáját, amelyekhez nem tartoznak hálózati biztonsági csoportok védelme alá. Ha arra az alhálózatra kattint, amelyre a hálózati biztonsági csoportot szeretné alkalmazni, megnyílik a **Hálózati biztonsági csoport választása** panel. Itt kiválaszthatja az alhálózathoz legmegfelelőbb hálózati biztonsági csoportot, illetve akár új hálózati biztonsági csoportot is létrehozhat.

#### <a name="internet-facing-endpoints-section"></a>Internet facing endpoints (Internet felé néző végpontok) szakasz
Az **Internet facing endpoints** (Internet felé néző végpontok) részben azokat a virtuális gépeket láthatja, amelyeken jelenleg internet felé néző végpont működik, illetve megtekinthető a végpont aktuális állapota is.

![Internet felé néző végpontokkal konfigurált virtuális gépek és a végpontok állapota](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Ez a táblázat tartalmazza a virtuális gépet tartalmazó végpont nevét, az internet felé néző IP-címet, valamint a hálózati biztonsági csoport és az NGFW aktuális súlyossági állapotát. A táblázat elemei súlyosság szerint rendezve jelennek meg:

* Piros (legfelül): a legmagasabb prioritás, azonnali beavatkozást igényel
* Narancssárga: közepes szintű prioritás, a lehető legrövidebb időn belül beavatkozást igényel
* Zöld (utolsó): megfelelő állapot

#### <a name="networking-topology-section"></a>Networking topology (Hálózati topológia) szakasz
A **Networking topology** (Hálózati topológia) rész az erőforrások hierarchikus nézetét mutatja, ahogy az alábbi képernyőfelvételen is látható:

![Az erőforrások hierarchikus nézete a Hálózati topológia szakaszban](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

A táblázat elemei (a virtuális gépek és az alhálózatok) súlyosság szerint rendezve jelennek meg:

* Piros (legfelül): a legmagasabb prioritás, azonnali beavatkozást igényel
* Narancssárga: közepes szintű prioritás, a lehető legrövidebb időn belül beavatkozást igényel
* Zöld (utolsó): megfelelő állapot

A topológia e nézetében az első szinten vannak a [virtuális hálózatok](../virtual-network/virtual-networks-overview.md), a [virtuális hálózati átjárók](/vpn-gateway/vpn-gateway-site-to-site-create.md) és a [ (klasszikus) virtuális hálózatok](/virtual-network/virtual-networks-create-vnet-classic-pportal.md). A második szinthez tartoznak az alhálózatok, míg a harmadik szinten az ezekhez az alhálózatokhoz tartozó virtuális gépek. A jobb oldali oszlopban az adott erőforrásokhoz tartozó hálózati biztonsági csoport aktuális állapota található, ahogyan az alábbi példában is látható:

![A hálózati biztonsági csoport állapota a Networking topology (Hálózati topológia) szakaszban](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

A panel alsó részén a virtuális gépre vonatkozó javaslatok láthatók, a fentiekben leírtakhoz hasonlóan. A javaslatra kattintva további információkat tekinthet meg, illetve alkalmazhatók a szükséges biztonsági rendszabályok/konfigurációk.

### <a name="monitor-storage--data"></a>A Tárolás és adatok figyelése

A **Megelőzés szakasz** **Tárolás és adatok** elemére kattintva megnyithatja az **Adatforrások** panelt, amelyen az SQL és a Storage használatával kapcsolatos javaslatokat olvashat. Ezenfelül általános [javaslatokat](security-center-sql-service-recommendations.md) is talál itt az adatbázis állapotára vonatkozóan. A tárolás titkosításáról további információkat az [Azure-tárfiókok titkosításának engedélyezése az Azure Security Centerben](security-center-enable-encryption-for-storage-account.md) című cikkben találhat.

![Adatforrások](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Az **SQL Recommendations** (Javaslatok az SQL használatával kapcsolatban) területen bármely javaslatra kattintva részletesebb információt kap a probléma megoldásához szükséges további intézkedésről. A következő példában a **Database Auditing & Threat detection on SQL databases** (Naplózás és fenyegetésészlelés az SQL-adatbázisokban) javaslat kibontott nézete látható.

![SQL-javaslat részletei](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Az **Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisokban** panelen a következő információk láthatók:

* Az SQL-adatbázisok listája
* A kiszolgáló, amely tárolja ezeket
* Az, hogy a beállítás a kiszolgálóról öröklődött, vagy konkrétan az adott adatbázisra vonatkozik
* Az aktuális állapot
* A probléma súlyossága

Ha a javaslat megoldása érdekében rákattint az adatbázisra, az alábbi képernyőn látható módon megnyílik az **Naplózás és fenyegetésészlelés** panel.

![Naplózás és fenyegetésészlelés](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

A naplózás engedélyezéséhez módosítsa az **Auditing** (Naplózás) beállítását **ON** (BE) értékre.

### <a name="monitor-applications"></a>Alkalmazások figyelése

Ha az Azure számítási feladatban felfedett webes portokkal (80-as és 443-as TCP-portok) működő (az Azure Resource Managerrel létrehozott) [virtuális gépeken](../azure-resource-manager/resource-manager-deployment-model.md) található alkalmazások futnak, a Security Center képes ezeket is figyelni, azonosítani a potenciális biztonsági problémákat és megoldást javasolni. Az **Alkalmazások** csempére kattintva megnyithatja az **Alkalmazások** panelt, amelynek **Alkalmazásokkal kapcsolatos javaslatok** szakaszában számos javaslat található. Ezenfelül itt látható az alkalmazások gazdagép, IP-cím vagy tartomány szerinti lebontása is, valamint ha van telepítve WAF-megoldás:

![Alkalmazások biztonsági állapota](./media/security-center-monitoring/security-center-monitoring-fig8-sep2017.png)

Ahogy a többi javaslat esetében, az egyes javaslatokra kattintva további információkhoz juthat a problémáról, illetve lehetséges megoldásáról. Az alábbi ábrán szereplő példában a rendszer nem biztonságos webalkalmazásként azonosított egy alkalmazást. Ha kijelöli a nem biztonságosnak ítélt alkalmazást, az alábbi lehetőség érhető el:

![Részletek](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Itt megjelenik az alkalmazáshoz tartozó összes javaslat listája. A **Webalkalmazási tűzfal felvétele** javaslatra kattintva megnyílik a **Webalkalmazási tűzfal felvétele** panel, amelyről külső WAF (webalkalmazási tűzfal) telepítésére van lehetőség, ahogyan az alábbi képernyőfelvételen is látható.

![Az Add Web Application Firewall (Webalkalmazás-tűzfal hozzáadása) párbeszédpanel](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md): Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md): Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
