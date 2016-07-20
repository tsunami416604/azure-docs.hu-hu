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
   ms.date="06/06/2016"
   ms.author="yurid"/>

#Biztonsági állapotfigyelés az Azure Security Centerben
Ez a dokumentum segítséget nyújt az Azure Security Center által nyújtott figyelési funkcióknak a szabályzatoknak való megfelelés ellenőrzésére történő használatába.

> [AZURE.NOTE] A dokumentumban szereplő információk az Azure Security Center előzetes verziójára vonatkoznak.

## Mi az az Azure Security Center?
A Security Center magasabb fokú betekintést és szélesebb körű vezérlést tesz lehetővé az Azure-erőforrások fölött, így segít a fenyegetések megelőzésében, észlelésében és kezelésében. A biztonsági megoldások átfogó ökoszisztémájának alkalmazásával az összes előfizetésére vonatkozóan integrált biztonságfelügyeletet és szabályzatkezelést biztosít, és segít felderíteni az egyébként nehezen észlelhető fenyegetéseket.

##Mi az a biztonsági állapotfigyelés?
Ha a „figyelés” szót halljuk, általában arra gondolunk, hogy elővigyázatosan várjuk, hogy egy adott esemény bekövetkezzen, hogy aztán reagálhassunk a szituációra. A biztonságfigyelés ezzel szemben proaktív stratégiát jelent, amely az erőforrásokat ellenőrizve azonosítja a vállalati szabványoknak vagy ajánlott eljárásoknak meg nem felelő rendszereket.

##A biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](security-center-policies.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat.  A hálózati konfigurációra vonatkozó adatok azonnal elérhetők, míg a virtuális gépek konfigurációjára (például a biztonsági frissítések állapotára és az operációs rendszer beállításaira) vonatkozó információkra körülbelül egy órát kell várni. A **Resource Security Health** (Erőforrás biztonsági állapota) panelen megtekintheti az erőforrások biztonsági állapotát, valamint az esetleg felmerülő problémákat. A problémák listáját a **Javaslatok** panelen is megtekintheti.

A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [Implementing security recommendations in Azure Security Center](security-center-recommendations.md) (A biztonsági javaslatok alkalmazása az Azure Security Centerben).

A **Resources security health** (Erőforrások biztonsági állapota) csempén is nyomon követheti erőforrásainak biztonsági állapotát. Az alábbi példában több olyan magas és közepes súlyosságú probléma látható, amely intézkedést követel meg. A bekapcsolt biztonsági szabályzatok hatással vannak a megfigyelt vezérlőtípusokra.

![Erőforrások állapota](./media/security-center-monitoring/security-center-monitoring-fig1-new3.png)

Ha a Security Center biztonsági rést (például biztonsági frissítésekkel el nem látott virtuális gépet vagy [hálózati biztonsági csoporttal](../virtual-network/virtual-networks-nsg.md) nem rendelkező alhálózatot) észlel, azt a rendszer itt listázza.

###Virtuális gépek figyelése
A **Resources security health** (Erőforrások biztonsági állapota) csempén található **Virtual machines** (Virtuális gépek) elemre kattintva megnyithatja a **Virtual machines** (Virtuális gépek) panelt, amelyen további információkat talál az előkészítési és megelőzési lépésekről, valamint itt tekintheti meg a Security Center által figyelt virtuális gépek listáját is.

![Hiányzó rendszerfrissítés a virtuális gépen](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

- Onboarding steps (Előkészítési lépések)
- Virtual machine recommendations (A virtuális gépre vonatkozó javaslatok)
- Virtual machines (Virtuális gépek)

Az egyes szakaszokban elérhető lehetőségek kiválasztásával további részleteket tudhat meg a probléma megoldásához szükséges lépésekről. Az alábbi szakaszokban részletesen foglalkozunk ezekkel a területekkel.

####Onboarding steps (Előkészítési lépések)
Ebben a szakaszban tekintheti meg az adatgyűjtésre beállított virtuális gépeket, valamint aktuális állapotukat. Ha az összes virtuális gépen bekapcsolta az adatgyűjtést, azok készen állnak a Security Center biztonsági szabályzatainak fogadására. Ha az elemre kattint, megnyílik az **Initializing data collection** (Adatgyűjtés inicializálása) panel, ahol megtekintheti a virtuális gépek nevét, az **INSTALLATION STATUS** (Telepítés állapota) oszlopban pedig az adatgyűjtés aktuális állapotát, ahogy az az alábbi képen is látható.

![Initialization status (Telepítés állapota)](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####Virtual machine recommendations (A virtuális gépre vonatkozó javaslatok)
Ebben a szakaszban az Azure Security Center által megfigyelt egyes virtuális gépekre vonatkozó javaslatok szerepelnek. Ahogy az alábbi képen is látható, az első oszlopban maga a javaslat látható, a másodikban azoknak a virtuális gépeknek a száma, amelyekre érvényes a javaslat, a harmadikban pedig a probléma súlyossága.

![Virtuális gépekre vonatkozó javaslatok](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

Az egyes javaslatokra kattintva különböző műveleteket végezhet el. Ha például a **Missing system updates** (Hiányzó rendszerfrissítések) elemre kattint, megnyílik a **Missing system updates** (Hiányzó rendszerfrissítések) panel. Ahogy az alábbi képen is látható, ezen a panelen megtekintheti azoknak a virtuális gépeknek a listáját, amelyekről frissítések hiányoznak, illetve a hiányzó frissítés súlyosságát.

![Hiányzó rendszerfrissítések](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

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

![Hiányzó rendszerfrissítések virtuális gépenként](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] Itt ugyanazok a biztonsági javaslatok láthatók, mint a Recommendations (Javaslatok) panelen. A javaslatok alkalmazásával kapcsolatban további információkat talál a következő cikkben: [Implementing security recommendations in Azure Security Center](security-center-recommendations.md) (A biztonsági javaslatok alkalmazása az Azure Security Centerben). Ez nem csupán a virtuális gépekre vonatkozik, hanem az összes erőforrásra, amely szerepel a Resource Health (Erőforrás állapota) csempén.

####A Virtual machines (Virtuális gépek) szakasz
A virtuális gépekre vonatkozó szakaszban áttekintheti az összes virtuális gépet, valamint a rájuk vonatkozó javaslatokat. Ahogy az alábbi képen is látható, minden oszlop egy javaslatcsoportot képvisel:

![Virtuális gépek](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

Az egyes javaslatok alatt megjelenő ikonok segítenek gyorsan azonosítani azokat a virtuális gépeket, amelyekkel foglalkoznia kell, illetve azt, hogy milyen típusú javaslat érkezett.

A fenti példában egy virtuális gépnél kritikus súlyosságú javaslat érkezett a kártevőirtó programokkal kapcsolatban. További információkért kattintson a kívánt virtuális gépre. Ahogy az alábbi képen is látható, megjelenik egy új panel, amelyen a virtuális gép adatai láthatók.

![Virtuális gép biztonsági adatai](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

A virtuális gép biztonsági információi ezen a panelen találhatók meg. A panel alján a javasolt műveleteket, valamint az egyes problémák súlyosságát láthatja.

###Virtuális hálózatok figyelése
A **Resources security health** (Erőforrások biztonsági állapota) panel **Networking** (Hálózat) csempéjére kattintva megnyithatja az alább látható **Networking** (Hálózat) panelt, amelyen további információk érhetők el:

![Hálózat](./media/security-center-monitoring/security-center-monitoring-fig9-new3.png)

####Hálózatokra vonatkozó javaslatok

A virtuális gépek erőforrás-állapotára vonatkozó információkhoz hasonlóan a panel felső részén a problémák összefoglaló listája látható, alább pedig a megfigyelt hálózatok listája.

A hálózati állapotot részletező listában megtekintheti a potenciális biztonsági problémákat, valamint az ezekre vonatkozó javaslatokat. Problémát jelenthetnek például a következők:

- Nincs telepítve új generációs tűzfal (NGFW)
- Az alhálózatokon nincsenek bekapcsolva a hálózati biztonsági csoportok (NSG-k)
- A virtuális gépeken nincsenek bekapcsolva a hálózati biztonsági csoportok (NSG-k)
- Külső hozzáférés korlátozása nyilvános külső végponton keresztül
- Kielégítő állapotú internet felé néző végpontok

Ha a javaslatokra kattint, új panel nyílik meg, amelyen, ahogy az az alábbi képen is látható, további részletek ismerhetők meg a javaslatról.

![Végpont korlátozása](./media/security-center-monitoring/security-center-monitoring-fig11-new2.png)

A példában a **Configure Missing Network Security Groups for Subnets** (Hiányzó hálózati biztonsági csoportok konfigurálása az alhálózatok számára) panelen láthatja azoknak az alhálózatoknak és virtuális gépeknek a listáját, amelyekhez nem tartozik NSG-védelem. Ha rákattint az alhálózatra, amelyre NSG-t kíván alkalmazni, megnyílik egy újabb panel.

A **Choose network security group** (Hálózati biztonsági csoport választása) panelen kiválaszthatja az alhálózathoz legmegfelelőbb hálózati biztonsági csoportot, illetve akár új hálózati biztonsági csoportot is létrehozhat. 

####Internet facing endpoints (Internet felé néző végpontok) szakasz

Az **Internet facing endpoints** (Internet felé néző végpontok) részben azoknak a virtuális gépeknek a listája látható, amelyeken jelenleg internet felé néző végpont működik, illetve megtekinthető a végpont aktuális állapota is.

![Internet felé néző végpont](./media/security-center-monitoring/security-center-monitoring-fig121-new5.png)

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

A topológia e nézetében az első szinten vannak a [virtuális hálózatok](../virtual-network/virtual-networks-overview.md), a [virtuális hálózati átjárók](../vpn-gateway/vpn-gateway-site-to-site-create.md) és a [virtuális hálózat (klasszikus)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). A második szinthez tartoznak az alhálózatok, míg a harmadik szinten az ezekhez az alhálózatokhoz kapcsolódó virtuális gépek. A jobb oldali oszlopban tekintheti meg az adott erőforrás hálózati biztonsági csoport (NSG) szerinti állapotát. Ha például a VM-CL-W1 jelzésű virtuális gépet választja, a következő jelenik meg:

![Hálózati fa](./media/security-center-monitoring/security-center-monitoring-fig13-new2.png)

A panel alsó részén a virtuális gépre vonatkozó javaslatok láthatók, a fentiekben leírtakhoz hasonlóan. A javaslatra kattintva további információkat tekinthet meg, illetve alkalmazhatók a szükséges biztonsági rendszabályok/konfigurációk.

###SQL-erőforrások figyelése
A **Resources security health** (Erőforrások biztonsági állapota) csempe **SQL** csempéjére kattintva megnyithatja az SQL panelt, amelyen megtekintheti a problémákkal (például a naplózás vagy a transzparens adattitkosítás kikapcsolása) kapcsolatos javaslatokat. Ezenfelül általános javaslatokat is talál itt az adatbázis állapotára vonatkozóan.

![SQL-erőforrás állapota](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

A javaslatok bármelyikére kattintva további részleteket tudhat meg a probléma megoldásához szükséges műveletekről. Az alábbi példában a **Database Auditing not enabled** (Adatbázis-naplózás nincs engedélyezve) javaslat kibontott nézete látható.

![SQL-erőforrás állapota](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

Az **Enable Auditing on SQL databases** (SQL-adatbázis naplózásának engedélyezése) panelen a következő információk láthatók:

- Az SQL-adatbázisok listája
- A kiszolgáló, amely tárolja ezeket
- Az, hogy a beállítás a kiszolgálóról öröklődött, vagy konkrétan az adott adatbázisra vonatkozik
- Az aktuális állapot
- A probléma súlyossága

Ha a javaslat megoldása érdekében rákattint az adatbázisra, az alábbi képen látható módon megnyílik az **Auditing & Threat detection** (Naplózás és fenyegetésészlelés) panel.

![SQL-erőforrás állapota](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

A naplózás bekacsolásához egyszerűen válassza az **Auditing** (Naplózás) beállításnál az **ON** (Be) lehetőséget, majd kattintson a **Save** (Mentés) gombra.

###Alkalmazások figyelése
Ha az Azure számítási feladatban felfedett webes portokkal (80-as és 443-as TCP-portok) működő [erőforrás-kezelő virtuális gépeken](../resource-manager-deployment-model.md) található alkalmazások futnak, a Security Center képes ezeket is figyelni, azonosítani a potenciális biztonsági problémákat, és megoldást javasolni. Az **Applications** (Alkalmazások) csempére kattintva megnyithatja az **Applications** (Alkalmazások) panelt, amelynek megelőzési lépéseket tartalmazó szakaszában számos javaslat található. Ezenfelül itt látható az alkalmazások állomásonkénti/virtuális IP-címenkénti lebontása is.

![Alkalmazások biztonsági állapota](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

Ahogy a többi javaslat esetében, itt is az egyes elemekre kattintva további információkhoz juthat a problémáról, illetve lehetséges megoldásáról. Az alábbi képen szereplő példában a rendszer nem biztonságos webalkalmazásként azonosított egy alkalmazást. Ha kijelöli a nem biztonságosnak ítélt alkalmazást, megnyílik egy újabb panel, amelyen az alábbi lehetőségek érhetők el:

![Alkalmazások](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

Az **Unsecured Web Applications** (Nem biztonságos webalkalmazások) panelen azok a virtuális gépek szerepelnek, amelyeken nem biztonságosnak ítélt alkalmazások futnak. A listában megtalálható a virtuális gép neve, a probléma aktuális helyzete, valamint súlyossága. Ha a webalkalmazásra kattint, megnyílik az **Add a Web Application Firewall** (Webalkalmazási tűzfal hozzáadása) panel, amelyen – ahogy az az alábbi képen is látható – lehetősége nyílik egy külső fejlesztőtől származó WAF (webalkalmazási tűzfal) telepítésére.

![WAF hozzáadása](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## Következő lépések
Ebben a dokumentumban megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Setting security policies in Azure Security Center](security-center-policies.md) (Biztonsági szabályzatok beállítása az Azure Security Centerben) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
- [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben) – Ebből az írásból megtanulhatja a biztonsági riasztások kezelésének és megoldásának módját.
- [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Ez a cikk ismerteti a partnermegoldások biztonsági állapotának figyelését.
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – A szolgáltatás használatára vonatkozó gyakran ismételt kérdések.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Jun16_HO2-->


