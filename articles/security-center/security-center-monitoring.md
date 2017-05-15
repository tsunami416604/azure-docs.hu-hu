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
ms.date: 05/09/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: f7f50c305a5ae6ad7bba4e793f4cb6a0735b80b4
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017


---
# <a name="security-health-monitoring-in-azure-security-center"></a>Biztonsági állapotfigyelés az Azure Security Centerben
Ez a cikk az Azure Security Center figyelési funkcióknak használatához nyújt segítséget a szabályzatoknak való megfelelés ellenőrzése érdekében.

## <a name="what-is-security-health-monitoring"></a>Mi az a biztonsági állapotfigyelés?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. A biztonságfigyelés ezzel szemben proaktív stratégiát jelent, amely az erőforrásokat ellenőrizve azonosítja a vállalati szabványoknak vagy ajánlott eljárásoknak meg nem felelő rendszereket.

## <a name="monitoring-security-health"></a>A biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](security-center-policies.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat. A hálózati konfigurációval kapcsolatos információk azonnal elérhetők. A virtuális gépek konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információkra körülbelül egy órát kell várni. A **Megelőzés** szakasz területén megtekintheti az erőforrások biztonsági állapotát és az esetleges problémákat. A problémák listáját a **Javaslatok** csempén is megtekintheti.

A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](security-center-recommendations.md).

A **Megelőzés** szakaszban is nyomon követheti erőforrásainak biztonsági állapotát. Az alábbi példában azt láthatja, hogy minden egyes erőforrás csempéjén (Számítás, Hálózat, Tárolás és adatok, Alkalmazás) megtalálható az azonosított hibák összes száma.

![A Resources security health (Erőforrások biztonsági állapota) csempe](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)


### <a name="monitor-compute"></a>Számítási tevékenység figyelése
Ha a **Számítás** csempére kattint, a megnyíló **Számítás** panelen három lap jelenik meg:

- **Áttekintés**: megfigyelés és a virtuális gépre vonatkozó javaslatok.
- **Virtuális gépek**: az összes virtuális gép és azok aktuális biztonsági állapotának listája.
- **Felhőszolgáltatások**: a Security Center által figyelt összes webes és feldolgozói szerepkör listája.

![Hiányzó rendszerfrissítések virtuális gépenként](./media/security-center-monitoring/security-center-monitoring-fig1-new002-2017.png)

Minden egyes lap esetében több szakaszt hozhat létre, és az egyes szakaszokban elérhető lehetőségek kiválasztásával további részleteket tudhat meg az adott probléma megoldásához szükséges lépésekről. 

#### <a name="monitoring-recommendations"></a>Figyelési javaslatok
Ebben a szakaszban tekintheti meg az összes adatgyűjtésre beállított virtuális gépet, és azok aktuális állapotát. Ha az összes virtuális gépen bekapcsolta az adatgyűjtést, azok készen állnak a Security Center biztonsági szabályzatainak fogadására. Ha erre a bejegyzésre kattint, megnyílik **A virtuálisgép-ügynök hiányzik vagy nem válaszol** panel. 

![Hiányzó rendszerfrissítések virtuális gépenként](./media/security-center-monitoring/security-center-monitoring-fig1-new003-2017.png)


#### <a name="virtual-machine-recommendations"></a>Virtual machine recommendations (A virtuális gépre vonatkozó javaslatok)
Ebben a szakaszban az Azure Security Center által megfigyelt [virtuális gépekre vonatkozó javaslatokat](security-center-virtual-machine-recommendations.md) olvashat. Az első oszlop a javaslatokat sorolja fel. A második oszlop az adott javaslat által érintett virtuális gépek számát tartalmazza. A harmadik oszlop a probléma súlyosságát mutatja, ahogyan az alábbi képernyőfelvételen látható.

![Virtual machine recommendations (A virtuális gépre vonatkozó javaslatok)](./media/security-center-monitoring/security-center-monitoring-fig1-new004-2017.png)

> [!NOTE]
> A **Networking Health** (Hálózatkezelés állapota) panel **Network topology** (Hálózati topológia) listájában kizárólag a legalább egy nyilvános végponttal rendelkező virtuális gépek jelennek meg.
>
>

Az egyes javaslatokra kattintva különböző műveleteket végezhet el. Ha például a **Missing system updates** (Hiányzó rendszerfrissítések) elemre kattint, megnyílik a **Missing system updates** (Hiányzó rendszerfrissítések) panel. Ahogy az alábbi képernyőfelvételen látható, ezen a panelen megtekintheti azoknak a virtuális gépeknek a listáját, amelyekről frissítések hiányoznak, valamint a hiányzó frissítés súlyosságát.

![Virtuális gépek hiányzó rendszerfrissítései](./media/security-center-monitoring/security-center-monitoring-fig5-ga.png)

A **Missing system updates** (Hiányzó rendszerfrissítések) panelen az alábbi információkat tartalmazó táblázat található:

* **VIRTUAL MACHINE** (Virtuális gép): a virtuális gép neve, amelyről frissítések hiányoznak.
* **SYSTEM UPDATES** (Rendszerfrissítések): a hiányzó rendszerfrissítések száma.
* **LAST SCAN TIME** (Utolsó átvizsgálás időpontja): az az időpont, amikor a Security Center legutóbb ellenőrizte a virtuális gépen a frissítéseket.
* **STATE** (Állapot): a javaslat aktuális állapota:
  * **Open** (Nyitott): a javaslattal egyelőre még nem foglalkoztak.
  * **In Progress** (Folyamatban): folyamatban van a javaslat alkalmazása az érintett erőforrásokra, további lépésekre nincs szükség.
  * **Resolved** (Megoldott): a javaslat alkalmazása megtörtént. (A probléma megoldása után a bejegyzés halványan jelenik meg.)
* **SEVERITY** (Súlyosság): az adott javaslat súlyosságát határozza meg:
  * **High** (Magas): biztonsági rés található egy fontos erőforrásnál (alkalmazás, virtuális gép, hálózati biztonsági csoport) és beavatkozást igényel.
  * **Medium** (Közepes): nem kritikus vagy kiegészítő lépések elvégzése szükséges egy folyamat befejezéséhez vagy egy biztonsági rés megszüntetéséhez.
  * **Low** (Alacsony): a biztonsági rést be kell tömni, de a probléma nem igényel azonnali beavatkozást. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)

A javaslat részleteinek megtekintéséhez kattintson a virtuális gép nevére. Az alábbi képernyőfelvételnek megfelelően megnyílik egy új panel, amelyen a kiválasztott virtuális gép frissítéseinek listája látható.

![Adott virtuális gép hiányzó rendszerfrissítései](./media/security-center-monitoring/security-center-monitoring-fig6-ga.png)

> [!NOTE]
> Itt ugyanazok a biztonsági javaslatok láthatók, mint a **Recommendations** (Javaslatok) panelen. A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [A biztonsági javaslatok alkalmazása az Azure Security Centerben](security-center-recommendations.md). Ez nem csupán a virtuális gépekre vonatkozik, hanem az összes olyan erőforrásra, amely szerepel a **Resource Health** (Erőforrás állapota) csempén.
>
>

#### <a name="virtual-machines-section"></a>A Virtual machines (Virtuális gépek) szakasz
A virtuális gépekre vonatkozó szakaszban áttekintheti az összes virtuális gépet, valamint a rájuk vonatkozó javaslatokat. Ahogy az alábbi képernyőfelvételen is látható, minden oszlop egy javaslatcsoportot képvisel:

![Az összes virtuális gép és javaslat áttekintése](./media/security-center-monitoring/security-center-monitoring-fig1-new005-2017.png)

Az egyes javaslatok alatt megjelenő ikonok segítenek gyorsan azonosítani az intézkedést igénylő virtuális gépeket és a javaslat típusát.

Az előző példában az egyik virtuális gép végpontvédelmével kapcsolatban a rendszer kritikus fontosságú javaslatot tett közzé. Ha további információt szeretne kapni a virtuális gépről, kattintson rá. Megnyílik az alábbi képernyőfelvételen látható új panel, amely a kívánt virtuális gépről nyújt áttekintést.

![Virtuális gépek biztonsági információi](./media/security-center-monitoring/security-center-monitoring-fig8-ga.png)

Ezen a panelen találhatók a virtuális gép biztonsági információi. A panel alján a javasolt műveleteket, valamint az egyes problémák súlyosságát láthatja.

#### <a name="cloud-services-section"></a>Cloud Services szakasz
A Cloud Services-szolgáltatások esetében a rendszer abban az esetben hozza létre a javaslatot, ha az operációs rendszer verziója elavult, ahogyan az alábbi képernyőfelvételen látható:

![Felhőszolgáltatások állapotinformációi](./media/security-center-monitoring/security-center-monitoring-fig1-new006-2017.png)

Olyan esetekben, ahol valóban rendelkezik javaslattal (az előző példa nem ilyen esetet mutat be), a javaslatban megadott lépéseket követve frissítheti az operációs rendszer verzióját. Ha egy frissítés elérhetővé válik, a rendszer riasztást jelenít meg (a probléma súlyosságától függően vörös vagy narancssárga riasztást). Ha a WebRole1 webes vagy a WorkerRole1 feldolgozói szerepkör riasztására kattint (mindkettő az IIS-re automatikusan telepített webalkalmazással együtt futtatja a Windows Servert), akkor egy új panel nyílik meg, amelyen további részletek láthatók az adott javaslatról, ahogyan az alábbi képernyőfelvételen is látható:

![Felhőszolgáltatás adatai](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png)

Ha előírásszerűbb magyarázatot kíván megtekinteni erről a javaslatról, a **DESCRIPTION** (LEÍRÁS) oszlopban kattintson az **Update OS version** (Operációs rendszer verziójának frissítése) elemre. Ekkor megnyílik a további részleteket tartalmazó **Update OS version (Preview) (Operációs rendszer verziójának frissítése - előzetes verzió)** panel.

![Felhőszolgáltatásokkal kapcsolatos javaslatok](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### <a name="monitor-virtual-networks"></a>Virtuális hálózatok figyelése
A **Hálózat** csempére kattintva megnyílik az alábbi képernyőképen látható **Hálózat** panel, amelyen további információk érhetők el:

![Hálózat panel](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

#### <a name="networking-recommendations"></a>Hálózatokra vonatkozó javaslatok
A virtuális gépek erőforrás-állapotára vonatkozó információkhoz hasonlóan a panel felső részén a problémák összefoglaló listája látható, alább pedig a megfigyelt hálózatok listája.

A hálózati állapotot részletező listában megtekintheti a potenciális biztonsági problémákat, valamint az ezekre vonatkozó [javaslatokat](security-center-network-recommendations.md). Problémát jelenthetnek például a következők:

* Nincs telepítve újgenerációs tűzfal (NGFW)
* Az alhálózatokon nincsenek bekapcsolva a hálózati biztonsági csoportok
* Az virtuális gépeken nincsenek bekapcsolva a hálózati biztonsági csoportok
* Külső hozzáférés korlátozása nyilvános külső végponton keresztül
* Megfelelő állapotú internet felé néző végpontok

Ha egy javaslatra kattint, az alábbi példában látható módon megnyílik egy új panel a javaslat további részleteivel.

![Javaslat részletei a Hálózat-panelen](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

Ebben a példában a **Configure Missing Network Security Groups for Subnets** (Hiányzó hálózati biztonsági csoportok konfigurálása az alhálózatok számára) panelen láthatja azoknak az alhálózatoknak és virtuális gépeknek a listáját, amelyekhez nem tartoznak hálózati biztonsági csoportok védelme alá. Ha arra az alhálózatra kattint, amelyre a hálózati biztonsági csoportot szeretné alkalmazni, megnyílik egy újabb panel.

A **Choose network security group** (Hálózati biztonsági csoport választása) panelen kiválaszthatja az alhálózathoz legmegfelelőbb hálózati biztonsági csoportot, illetve akár új hálózati biztonsági csoportot is létrehozhat.

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

A **Megelőzés** szakasz **Tárolás és adatok** elemére kattintva megnyithatja az **Adatforrások** panelt, amelyen az SQL és a Storage használatával kapcsolatos javaslatokat olvashat. Ezenfelül általános [javaslatokat](security-center-sql-service-recommendations.md) is talál itt az adatbázis állapotára vonatkozóan. A tárolás titkosításáról további információkat az [Azure-tárfiókok titkosításának engedélyezése az Azure Security Centerben](security-center-enable-encryption-for-storage-account.md) című cikkben találhat.

![Adatforrások](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Az **SQL Recommendations** (Javaslatok az SQL használatával kapcsolatban) területen bármely javaslatra kattintva részletesebb információt kap a probléma megoldásához szükséges további intézkedésről. A következő példában a **Database Auditing & Threat detection on SQL databases** (Naplózás és fenyegetésészlelés az SQL-adatbázisokban) javaslat kibontott nézete látható.

![SQL-javaslat részletei](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Az **Enable Auditing & Threat detection on SQL databases** (Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisokon) panelen a következő információk láthatók:

* Az SQL-adatbázisok listája
* A kiszolgáló, amely tárolja ezeket
* Az, hogy a beállítás a kiszolgálóról öröklődött, vagy konkrétan az adott adatbázisra vonatkozik
* Az aktuális állapot
* A probléma súlyossága

Ha a javaslat megoldása érdekében rákattint az adatbázisra, az alábbi képernyőn látható módon megnyílik az **Auditing & Threat detection** (Naplózás és fenyegetésészlelés) panel.

![Naplózás és fenyegetésészlelés panel](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

A naplózás engedélyezéséhez módosítsa az **Auditing** (Naplózás) beállítását **ON** (BE) értékre.

### <a name="monitor-applications"></a>Alkalmazások figyelése

Ha az Azure számítási feladatban felfedett webes portokkal (80-as és 443-as TCP-portok) működő (az Azure Resource Managerrel létrehozott) [virtuális gépeken](../azure-resource-manager/resource-manager-deployment-model.md) található alkalmazások futnak, a Security Center képes ezeket is figyelni, azonosítani a potenciális biztonsági problémákat és megoldást javasolni. Az **Alkalmazások** csempére kattintva megnyithatja az **Alkalmazások** panelt, amelynek **Alkalmazásokkal kapcsolatos javaslatok** szakaszában számos javaslat található. Emellett itt látható az alkalmazások állomásonkénti/virtuális IP-címenkénti lebontása is az alábbi képernyőfelvételnek megfelelően.

![Alkalmazások biztonsági állapota](./media/security-center-monitoring/security-center-monitoring-fig16-ga.png)

Ahogy a többi javaslat esetében, az egyes javaslatokra kattintva további információkhoz juthat a problémáról, illetve lehetséges megoldásáról. Az alábbi ábrán szereplő példában a rendszer nem biztonságos webalkalmazásként azonosított egy alkalmazást. Ha kijelöli a nem biztonságosnak ítélt alkalmazást, megnyílik egy újabb panel, amelyen az alábbi lehetőségek érhetők el:

![Információk a nem biztonságos alkalmazásról](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Ezen a panelen megjelenik az alkalmazáshoz tartozó összes javaslat listája. Az **Add a web application firewall** (Webalkalmazás-tűzfal hozzáadása) javaslatra kattintva megnyílik az **Add a Web Application Firewall** (Webalkalmazás-tűzfal hozzáadása) panel, amelyről külső WAF (webalkalmazás-tűzfal) telepítésére van lehetőség, ahogyan az alábbi képernyőfelvételen is látható.

![Az Add Web Application Firewall (Webalkalmazás-tűzfal hozzáadása) párbeszédpanel](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## <a name="see-also"></a>Lásd még:
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md): Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md): Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

