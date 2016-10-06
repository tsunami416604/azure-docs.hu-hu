<properties
   pageTitle="Biztonsági állapotfigyelés az Azure Security Centerben | Microsoft Azure"
   description="Ez a dokumentum az Azure Security Center figyelési funkcióinak használatába nyújt bevezetést."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>


#Biztonsági állapotfigyelés az Azure Security Centerben
Ez a dokumentum segítséget nyújt az Azure Security Center által nyújtott figyelési funkcióknak a szabályzatoknak való megfelelés ellenőrzésére történő használatába.

##Mi az a biztonsági állapotfigyelés?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. A biztonságfigyelés ezzel szemben proaktív stratégiát jelent, amely az erőforrásokat ellenőrizve azonosítja a vállalati szabványoknak vagy ajánlott eljárásoknak meg nem felelő rendszereket.

##A biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](security-center-policies.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat.  A hálózati konfigurációra vonatkozó adatok azonnal elérhetők, míg a virtuális gépek konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információkra körülbelül egy órát kell várni. A **Resource Security Health** (Erőforrás biztonsági állapota) panelen megtekintheti az erőforrások biztonsági állapotát, valamint az esetleg felmerülő problémákat. A problémák listáját a **Javaslatok** panelen is megtekintheti.

A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [Implementing security recommendations in Azure Security Center](security-center-recommendations.md) (A biztonsági javaslatok alkalmazása az Azure Security Centerben).

A **Resources security health** (Erőforrások biztonsági állapota) csempén is nyomon követheti erőforrásainak biztonsági állapotát. Az alábbi példában több olyan magas és közepes súlyosságú probléma látható, amely intézkedést követel meg. A bekapcsolt biztonsági szabályzatok hatással vannak a megfigyelt vezérlőtípusokra.

![Erőforrások állapota](./media/security-center-monitoring/security-center-monitoring-fig1-new4.png)

Ha a Security Center biztonsági rést (például biztonsági frissítésekkel el nem látott virtuális gépet vagy [hálózati biztonsági csoporttal](../virtual-network/virtual-networks-nsg.md) nem rendelkező alhálózatot) észlel, azt a rendszer itt listázza.

###Virtuális gépek figyelése
A **Resources security health** (Erőforrások biztonsági állapota) csempén található **Virtual machines** (Virtuális gépek) elemre kattintva megnyithatja a **Virtual machines** (Virtuális gépek) panelt, amelyen további információkat talál az előkészítési és megelőzési lépésekről, valamint itt tekintheti meg a Security Center által figyelt virtuális gépek listáját is.

![Hiányzó rendszerfrissítés a virtuális gépen](./media/security-center-monitoring/security-center-monitoring-fig2-ga.png)

- Onboarding steps (Előkészítési lépések)
- Virtual machine recommendations (A virtuális gépre vonatkozó javaslatok)
- Virtual machines (Virtuális gépek)

Az egyes szakaszokban elérhető lehetőségek kiválasztásával további részleteket tudhat meg a probléma megoldásához szükséges lépésekről. Az alábbi szakaszokban részletesen foglalkozunk ezekkel a területekkel.

#### Figyelési javaslatok
Ebben a szakaszban tekintheti meg az adatgyűjtésre beállított virtuális gépeket, valamint aktuális állapotukat. Ha az összes virtuális gépen bekapcsolta az adatgyűjtést, azok készen állnak a Security Center biztonsági szabályzatainak fogadására. Ha ebbe a bejegyzésbe kattint, a **Data collection installation status** (Adatgyűjtés telepítési állapota) panel nyílik meg, amelyen a virtuális gépek nevei, valamint az adatgyűjtés aktuális állapota jelenik meg az **INSTALLATION STATUS** (TELEPÍTÉSI ÁLLAPOT) oszlopban, ahogyan az az alábbi képen is látható.

![Initialization status (Telepítés állapota)](./media/security-center-monitoring/security-center-monitoring-fig3-ga.png)


####Virtual machine recommendations (A virtuális gépre vonatkozó javaslatok)
Ebben a szakaszban az Azure Security Center által megfigyelt egyes [virtuális gépekre vonatkozó javaslatok](security-center-virtual-machine-recommendations.md) szerepelnek. Ahogy az alábbi képen is látható, az első oszlopban maga a javaslat látható, a másodikban azoknak a virtuális gépeknek a száma, amelyekre érvényes a javaslat, a harmadikban pedig a probléma súlyossága.

![Virtuális gépekre vonatkozó javaslatok](./media/security-center-monitoring/security-center-monitoring-fig4-ga.png)

> [AZURE.NOTE] A Networking Health (Hálózatkezelés állapota) panel Network topology (Hálózati topológia) listájában kizárólag a legalább egy nyilvános végponttal rendelkező virtuális gépek jelennek meg.

Az egyes javaslatokra kattintva különböző műveleteket végezhet el. Ha például a **Missing system updates** (Hiányzó rendszerfrissítések) elemre kattint, megnyílik a **Missing system updates** (Hiányzó rendszerfrissítések) panel. Ahogy az alábbi képen is látható, ezen a panelen megtekintheti azoknak a virtuális gépeknek a listáját, amelyekről frissítések hiányoznak, illetve a hiányzó frissítés súlyosságát.

![Hiányzó rendszerfrissítések](./media/security-center-monitoring/security-center-monitoring-fig5-ga.png)

A **Missing system updates** (Hiányzó rendszerfrissítések) panelen az alábbi információkat tartalmazó táblázat található:

- **VIRTUAL MACHINE** (Virtuális gép): a virtuális gép neve, amelyről frissítések hiányoznak.
- **SYSTEM UPDATES** (Rendszerfrissítések): a hiányzó rendszerfrissítések száma.
- **LAST SCAN TIME** (Utolsó átvizsgálás időpontja): az az időpont, amikor a Security Center legutóbb ellenőrizte a virtuális gépen a frissítéseket.
- **STATE** (Állapot): a javaslat aktuális állapota:
    - **Open** (Nyitott): a javaslattal egyelőre még nem foglalkoztak.
    - **In Progress** (Folyamatban): jelenleg is folyamatban van a javaslat alkalmazása az érintett erőforrásokra, további lépésekre nincs szükség.
    - **Resolved** (Megoldva): a javaslatot már megoldották (ha a problémát már megoldották, a bejegyzés szürkén jelenik meg).
- **SEVERITY** (Súlyosság): az adott javaslat súlyosságát határozza meg:
    - **High** (Magas): a biztonsági rés fontos erőforrásnál (alkalmazás, virtuális gép, hálózati biztonsági csoport) található, ezért beavatkozást igényel.
    - **Medium** (Közepes): nem kritikus vagy kiegészítő lépések elvégzése szükséges egy folyamat befejezéséhez vagy egy biztonsági rés megszüntetéséhez.
    - **Low** (Alacsony): a biztonsági rést be kell tömni, de a probléma nem igényel azonnali beavatkozást. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)

A javaslat részletes adatainak megtekintéséhez kattintson a virtuális gép nevére. Ahogy az alábbi képen is látható, megnyílik a virtuális gépre vonatkozó új panel, amelyen megtalálja a frissítések listáját.

![Hiányzó rendszerfrissítések virtuális gépenként](./media/security-center-monitoring/security-center-monitoring-fig6-ga.png)

> [AZURE.NOTE] Itt ugyanazok a biztonsági javaslatok láthatók, mint a Recommendations (Javaslatok) panelen. A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [Implementing security recommendations in Azure Security Center](security-center-recommendations.md) (A biztonsági javaslatok alkalmazása az Azure Security Centerben). Ez nem csupán a virtuális gépekre vonatkozik, hanem az összes erőforrásra, amely szerepel a Resource Health (Erőforrás állapota) csempén.

####A Virtual machines (Virtuális gépek) szakasz
A virtuális gépekre vonatkozó szakaszban áttekintheti az összes virtuális gépet, valamint a rájuk vonatkozó javaslatokat. Ahogy az alábbi képen is látható, minden oszlop egy javaslatcsoportot képvisel:

![Virtuális gépek](./media/security-center-monitoring/security-center-monitoring-fig7-ga.png)

Az egyes javaslatok alatt megjelenő ikonok segítenek gyorsan azonosítani azokat a virtuális gépeket, amelyekkel foglalkoznia kell, illetve azt, hogy milyen típusú javaslat érkezett.

A fenti példában az egyik virtuális gép végpontvédelmével kapcsolatban a rendszer kritikus fontosságú javaslatot tett közzé. További információkért kattintson a kívánt virtuális gépre. Ahogy az alábbi képen is látható, megjelenik egy új panel, amelyen a virtuális gép adatai láthatók.

![Virtuális gép biztonsági adatai](./media/security-center-monitoring/security-center-monitoring-fig8-ga.png)

A virtuális gép biztonsági információi ezen a panelen találhatók meg. A panel alján a javasolt műveleteket, valamint az egyes problémák súlyosságát láthatja.

#### Felhőszolgáltatások (előzetes verzió) szakasz
A felhőszolgáltatások állapotinformációi a virtuális gép biztonsági állapot csempéjén jelennek meg. A rendszer akkor tesz közzé javaslatot, ha az operációs rendszer nem naprakész, ahogyan az az alábbi képen is látható: 

![Felhőszolgáltatások](./media/security-center-monitoring/security-center-monitoring-fig8-new2.png)

A javaslatban megadott lépéseket követve frissítheti az operációs rendszer verzióját. Ha például az egyik webes vagy feldolgozói szerepkör (mindkettő az IIS-re automatikusan telepített webalkalmazással együtt futtatja a Windows Servert) esetében a vörös színű riasztásra kattint, egy új panel nyílik meg, amelyen további részletek láthatóak az adott javaslatról ahogyan az az alábbi képen is látható:

![Felhőszolgáltatás adatai](./media/security-center-monitoring/security-center-monitoring-fig8-new3.png) 

Ha az előírásoknak megfelelőbb magyarázatot kíván megtekinteni erről a javaslatról, a **DESCRIPTION** (LEÍRÁS) oszlopban kattintson az **Update OS version** (Operációs rendszer verziójának frissítése) elemre. Ekkor megnyílik a további részleteket tartalmazó **Operációs rendszer verziójának frissítése (előzetes verzió)** panel.

![Cloud Services – javaslatok](./media/security-center-monitoring/security-center-monitoring-fig8-new4.png)  

### Virtuális hálózatok figyelése
A **Resources security health** (Erőforrások biztonsági állapota) panel **Networking** (Hálózat) csempéjére kattintva megnyithatja az alább látható **Networking** (Hálózat) panelt, amelyen további információk érhetők el:

![Hálózat](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

####Hálózatokra vonatkozó javaslatok

A virtuális gépek erőforrás-állapotára vonatkozó információkhoz hasonlóan a panel felső részén a problémák összefoglaló listája látható, alább pedig a megfigyelt hálózatok listája.

A hálózati állapotot részletező listában megtekintheti a potenciális biztonsági problémákat, valamint az ezekre vonatkozó [javaslatokat](security-center-network-recommendations.md). Problémát jelenthetnek például a következők:

- Nincs telepítve új generációs tűzfal (NGFW)
- Az alhálózatokon nincsenek bekapcsolva a hálózati biztonsági csoportok (NSG-k)
- A virtuális gépeken nincsenek bekapcsolva a hálózati biztonsági csoportok (NSG-k)
- Külső hozzáférés korlátozása nyilvános külső végponton keresztül
- Kielégítő állapotú internet felé néző végpontok

Ha a javaslatokra kattint, új panel nyílik meg, amelyen, ahogy az az alábbi képen is látható, további részletek ismerhetők meg a javaslatról.

![Végpont korlátozása](./media/security-center-monitoring/security-center-monitoring-fig9-ga.png)

A példában a **Configure Missing Network Security Groups for Subnets** (Hiányzó hálózati biztonsági csoportok konfigurálása az alhálózatok számára) panelen láthatja azoknak az alhálózatoknak és virtuális gépeknek a listáját, amelyekhez nem tartozik NSG-védelem. Ha rákattint az alhálózatra, amelyre NSG-t kíván alkalmazni, megnyílik egy újabb panel.

A **Choose network security group** (Hálózati biztonsági csoport választása) panelen kiválaszthatja az alhálózathoz legmegfelelőbb hálózati biztonsági csoportot, illetve akár új hálózati biztonsági csoportot is létrehozhat. 

####Internet facing endpoints (Internet felé néző végpontok) szakasz

Az **Internet facing endpoints** (Internet felé néző végpontok) részben azoknak a virtuális gépeknek a listája látható, amelyeken jelenleg internet felé néző végpont működik, illetve megtekinthető a végpont aktuális állapota is.

![Internet felé néző végpont](./media/security-center-monitoring/security-center-monitoring-fig10-ga.png)

Ez a táblázat tartalmazza a virtuális gépet tartalmazó végpont nevét, az internet felé néző IP-címet, valamint az NSG és az NGFW aktuális súlyossági állapotát. A táblázat elemei súlyosság szerint rendezve jelennek meg, az alábbi sorrendben:
- Piros (legfelül): a legmagasabb prioritás, azonnal foglalkozzon vele 
- Narancssárga: közepes szintű prioritás, a lehető leghamarabb foglalkozzon vele
- Zöld (utolsó): megfelelő állapot

####Networking topology (Hálózati topológia) szakasz

A **Networking topology** (Hálózati topológia) részben az erőforrások hierarchiába rendezett módon tekinthetők meg, ahogy az az alábbi képen is látható:

![Hálózati topológia](./media/security-center-monitoring/security-center-monitoring-fig121-new4.png)

A táblázat elemei (a virtuális gépek és az alhálózatok) súlyosság szerint rendezve jelennek meg, az alábbi sorrendben:
- Piros (legfelül): a legmagasabb prioritás, azonnal foglalkozzon vele 
- Narancssárga: közepes szintű prioritás, a lehető leghamarabb foglalkozzon vele
- Zöld (utolsó): megfelelő állapot

A topológia e nézetében az első szinten vannak a [virtuális hálózatok](../virtual-network/virtual-networks-overview.md), a [virtuális hálózati átjárók](../vpn-gateway/vpn-gateway-site-to-site-create.md) és a [virtuális hálózat (klasszikus)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). A második szinthez tartoznak az alhálózatok, míg a harmadik szinten az ezekhez az alhálózatokhoz kapcsolódó virtuális gépek. A jobb oldali oszlopban az adott erőforrásokhoz tartozó hálózati biztonsági csoport (NSG) aktuális állapota található, ahogyan az az alábbi példában is látható:

![Hálózati fa](./media/security-center-monitoring/security-center-monitoring-fig12-ga.png)

A panel alsó részén a virtuális gépre vonatkozó javaslatok láthatók, a fentiekben leírtakhoz hasonlóan. A javaslatra kattintva további információkat tekinthet meg, illetve alkalmazhatók a szükséges biztonsági rendszabályok/konfigurációk.

###Adatok figyelése
A **Resources security health** (Erőforrások biztonsági állapota) csempe **Data** (Adatok) csempéjére kattintva megnyithatja az **SQL** panelt, amelyen megtekintheti a problémákkal (például a naplózás vagy a transzparens adattitkosítás kikapcsolása) kapcsolatos javaslatokat. Ezenfelül általános [javaslatokat](security-center-sql-service-recommendations.md) is talál itt az adatbázis állapotára vonatkozóan.

![SQL-erőforrás állapota](./media/security-center-monitoring/security-center-monitoring-fig13-ga.png)

A javaslatok bármelyikére kattintva további részleteket tudhat meg a probléma megoldásához szükséges műveletekről. Az alábbi példában a **Database Auditing not enabled** (Adatbázis-naplózás nincs engedélyezve) javaslat kibontott nézete látható.

![SQL-erőforrás állapota](./media/security-center-monitoring/security-center-monitoring-fig14-ga.png)

Az **Enable Auditing on SQL databases** (SQL-adatbázis naplózásának engedélyezése) panelen a következő információk láthatók:

- Az SQL-adatbázisok listája
- A kiszolgáló, amely tárolja ezeket
- Az, hogy a beállítás a kiszolgálóról öröklődött, vagy konkrétan az adott adatbázisra vonatkozik
- Az aktuális állapot
- A probléma súlyossága

Ha a javaslat megoldása érdekében rákattint az adatbázisra, az alábbi képen látható módon megnyílik az **Auditing & Threat detection** (Naplózás és fenyegetésészlelés) panel.

![SQL-erőforrás állapota](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

A naplózás engedélyezéséhez egyszerűen módosítsa az **Auditing** (Naplózás) beállítását **ON** (BE) értékre.

### Alkalmazások figyelése

Ha az Azure számítási feladatban felfedett webes portokkal (80-as és 443-as TCP-portok) működő [erőforrás-kezelő virtuális gépeken](../resource-manager-deployment-model.md) található alkalmazások futnak, a Security Center képes ezeket is figyelni, azonosítani a potenciális biztonsági problémákat, és megoldást javasolni. Az **Applications** (Alkalmazások) csempére kattintva megnyithatja az **Applications** (Alkalmazások) panelt, amelynek megelőzési lépéseket tartalmazó szakaszában számos javaslat található. Ezenfelül itt látható az alkalmazások állomásonkénti/virtuális IP-címenkénti lebontása is.

![Alkalmazások biztonsági állapota](./media/security-center-monitoring/security-center-monitoring-fig16-ga.png)

Ahogy a többi javaslat esetében, itt is az egyes elemekre kattintva további információkhoz juthat a problémáról, illetve lehetséges megoldásáról. Az alábbi képen szereplő példában a rendszer nem biztonságos webalkalmazásként azonosított egy alkalmazást. Ha kijelöli a nem biztonságosnak ítélt alkalmazást, megnyílik egy újabb panel, amelyen az alábbi lehetőségek érhetők el:

![Alkalmazások](./media/security-center-monitoring/security-center-monitoring-fig17-ga.png)

Ezen a panelen az alkalmazáshoz tartozó összes javaslat listája megjelenik. Az **Add a web application firewall** (Webalkalmazás-tűzfal hozzáadása) javaslatra kattintva megnyílik az **Add a Web Application Firewall** (Webalkalmazás-tűzfal hozzáadása) panel, amelyről külső WAF (webalkalmazás-tűzfal) telepítésére van lehetőség, ahogyan az az alábbi képen is látható.

![WAF hozzáadása](./media/security-center-monitoring/security-center-monitoring-fig18-ga.png)

## Lásd még:
Ebben a dokumentumban megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
- [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
- [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Sep16_HO4-->


