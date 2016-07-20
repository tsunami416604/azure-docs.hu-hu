<properties
   pageTitle="Az Azure Security Center használatának első lépései | Microsoft Azure"
   description="Ebből a dokumentumból megismerheti az Azure Security Center biztonsági szabályzatainak konfigurálásához szükséges lépéseket."
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
   ms.date="06/03/2016"
   ms.author="yurid"/>

# Biztonsági szabályzatok beállítása az Azure Security Centerben
Ez a dokumentum végigvezeti az Azure Security Centerben a biztonsági szabályzatok beállításához szükséges lépéseken.

> [AZURE.NOTE] A dokumentumban szereplő információk az Azure Security Center előzetes verziójára vonatkoznak.

## Mi az az Azure Security Center?
Az Azure Security Center magasabb fokú betekintést és szélesebb körű vezérlést tesz lehetővé az Azure-erőforrások fölött, így segít a fenyegetések megelőzésében, észlelésében és kezelésében. A biztonsági megoldások átfogó ökoszisztémájának alkalmazásával az összes előfizetésére vonatkozóan integrált biztonságfelügyeletet és szabályzatkezelést biztosít, és segít felderíteni az egyébként nehezen észlelhető fenyegetéseket.

## Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az egy adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol. Az Azure Security Centerben a vállalat biztonsági igényeinek és az egyes előfizetésekben szereplő alkalmazások típusának vagy az adatok érzékenységének megfelelően határozhatja meg az Azure-előfizetésekre vagy -erőforráscsoportokra vonatkozó szabályzatokat.

Például különböző biztonsági követelmények vonatkozhatnak a fejlesztésben vagy tesztelésben, illetve az éles környezetben használt erőforrásokra. A szabályozott adatokkal (például személyazonosításra alkalmas adatokkal) működő alkalmazások is magasabb szintű biztonságot követelhetnek meg. Az Azure Security Centerben engedélyezett biztonsági szabályzatok biztonsági javaslatokkal szolgálnak, és figyelést biztosítanak, így segítenek Önnek felismerni a potenciális biztonsági réseket és elhárítani a fenyegetéseket.

## Biztonsági szabályzatok beállítása előfizetésekhez

Az egyes előfizetésekhez vagy erőforráscsoportokhoz külön-külön biztonsági szabályzatot állíthat be. A biztonsági szabályzat módosításához az előfizetésben Tulajdonos vagy Közreműködő szerepkörrel kell rendelkeznie. Az Azure Security Center biztonsági szabályzatainak konfigurálásához kövesse az alábbi lépéseket:

1. Kattintson az Azure Security Center irányítópultjának **Security Policy** (Biztonsági szabályzat) csempéjére.

2. A jobb oldalon megnyíló **Security Policy - Define policy per subscription or resource group** (Biztonsági szabályzat – Előfizetésre vagy erőforráscsoportra vonatkozó szabályzat beállítása) panelen válassza ki az előfizetést, amelyhez engedélyezni szeretné a biztonsági szabályzatot. Ha a teljes előfizetés helyett csak egy erőforráscsoporton szeretné engedélyezni a biztonsági szabályzatot, görgessen le a következő szakaszhoz, ahol az erőforráscsoportokra vonatkozó szabályzatokkal kapcsolatban is talál információkat.

    ![A szabályzat definiálása](./media/security-center-policies/security-center-policies-fig01.png)

3. Megnyílik az adott előfizetéshez tartozó **Security policy** (Biztonsági szabályzat) panel, az alábbi képen látható lehetőségekkel vagy hasonlókkal:

    ![Adatgyűjtés engedélyezése](./media/security-center-policies/security-center-policies-fig1-new.png)

4. A **Collect data from virtual machines** (Adatgyűjtés a virtuális gépekről) beállítás értéke legyen **On** (Bekapcsolva). Ezzel engedélyezi a meglévő és az új erőforrásokra vonatkozó automatikus naplógyűjtést. 

    >[AZURE.NOTE] Határozottan javasoljuk, hogy minden egyes előfizetésnél kapcsolja be az adatgyűjtést, mivel így az összes meglévő és új virtuális gép esetében elérhető a biztonsági figyelés. Az adatgyűjtés engedélyezését követően a rendszer telepíti a figyelőügynököt. Ha jelenleg még nem szeretné bekapcsolni az adatgyűjtést ezen a helyen, ezt később is megteheti az állapotot és javaslatokat tartalmazó nézetben. Megadhatja azt is, hogy az egész előfizetésre, vagy csupán az Ön által kiválasztott virtuális gépekre vonatkozóan szeretné bekapcsolni az adatgyűjtést. A támogatott virtuális gépekkel kapcsolatban további információkat talál az [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: GYIK) című cikkben.

5. Ha még nem konfigurálta tárfiókját, elképzelhető, hogy a **Security Policy** (Biztonsági szabályzat) lap megnyitásakor az alábbihoz hasonló figyelmeztetés jelenik meg:

    ![Tároló kiválasztása](./media/security-center-policies/security-center-policies-fig2.png)

6. Ha a figyelmeztetés megjelenik, kattintson erre a lehetőségre, és válassza ki a használni kívánt régiót az alábbi képen látható módon:

    ![Tároló kiválasztása](./media/security-center-policies/security-center-policies-fig3.png)

7. Minden régió esetében, ahol virtuális gépei futnak, válassza ki, hogy mely tárfiók tárolja a virtuális gépekről összegyűjtött adatokat. Így egy földrajzi területen belül tárolhatja az adatokat, ami segít az adatok védelmében, és az egy helyre tartozó adatok közös joghatóság alá helyezésében. Ha eldöntötte, hogy melyik régiót szeretné használni, válassza ki a kívánt régiót, majd a tárfiókot.

8. A **Choose storage accounts** (Tárfiókok kiválasztása) panelen kattintson az **OK** gombra.

    > [AZURE.NOTE] Ha szeretné, akár egy központi tárfiókban is összegyűjtheti a különböző régiókban futó virtuális gépekről származó adatokat. További információk: [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: GYIK).

9. A **Security Policy** (Biztonsági szabályzat) panelen kattintson az **On** (Bekapcsolás) lehetőségre azoknál a biztonsági javaslatoknál, amelyeket az előfizetésnél használni kíván. Kattintson a **Prevention policy** (Megelőzési szabályzat) elemre. Megjelenik a **Security Policy** (Biztonsági szabályzat) panel az alábbi képen látható módon. 

    ![A biztonsági szabályzatok kiválasztása](./media/security-center-policies/security-center-policies-fig1-1-new2.png)

Az alábbi táblázat segít megismerni az egyes lehetőségek működését:

| Szabályzat | Bekapcsolt állapotban |
|----- |-----|
| System Updates (Rendszerfrissítések) | Naponta lekéri az elérhető biztonsági és fontos frissítések listáját a Windows Update-tól vagy a WSUS-tól (attól függően, hogy melyik szolgáltatást használja a virtuális gép), és ennek alapján javasolja a hiányzó frissítések alkalmazását. |
| Baseline Rules (Alapkonfigurációs szabályok) | Naponta elemzi az operációs rendszer azon konfigurációit, amelyek sebezhetővé teszik a virtuális gépet, és a biztonsági rések megszüntetését elősegítő konfigurációmódosításokat javasol. A szabályzat által figyelt konfigurációkkal kapcsolatban lásd a [javasolt alapkonfigurációk listáját](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). |
| Endpoint Protection (Végpontok védelme) | A szabályzat az összes windowsos virtuális gép esetében végpontvédelem beállítását javasolja a vírusok, kémprogramok és más kártékony szoftverek hatékonyabb azonosításához és eltávolításához. 
| Network Security Groups (Hálózati biztonsági csoportok) | A javaslat [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md) (NSG-k) beállítását javasolja, ami megkönnyíti az alhálózatokra és hálózati adapterekre beérkező, illetve ezekről kimenő forgalom szabályozását. Az alhálózatra beállított NSG-ket az összes virtuális géphez tartozó hálózati adapter örökli, kivéve, ha Ön más beállítást ad meg. Az NSG-k konfigurálásának figyelése mellett ez a lehetőség a bejövő biztonsági szabályokat is ellenőrzi, és megállapítja, hogy vannak-e köztük olyanok, amelyek engedélyezik a bejövő forgalmat. |
| Web Application Firewall (Webalkalmazási tűzfal) | Webalkalmazási tűzfal kiépítését javasolja a virtuális gépeken a következő esetekben: [példányszintű nyilvános IP-címet](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP-t) használ, és az ahhoz tartozó NSG bejövő biztonsági szabályait úgy állították be, hogy engedélyezzék a 80-as/443-as port elérését. Elosztott terhelésű IP-címet (virtuális IP-címet) használ, és az ahhoz tartozó terheléselosztási és bejövő NAT-re vonatkozó szabályokat úgy állították be, hogy engedélyezzék a 80-as/443-as port elérését (további információkért lásd: [Azure Resource Manager Support for Load Balancer](../load-balancer/load-balancer-arm.md) (Az Azure Resource Manager támogatása a Terheléselosztó számára). |
| Next Generation Firewall (Új generációs tűzfal) | Ezzel az Azure-ba épített hálózati biztonsági csoportokon túlra is kiterjesztheti a hálózati védelmet. A Security Center felfedezi az üzemelő példányokat, amelyekhez új generációs tűzfal használata javasolt, és engedélyezi a virtuális berendezés kiépítését. |
| SQL Auditing (SQL-naplózás) | Javasolja, hogy a megfelelés, a jobb észlelés és a támadások hatékonyabb kivizsgálásához engedélyezze az Azure SQL Server kiszolgálók és adatbázisok naplózását. |
| SQL Transparent Data Encryption (SQL transzparens adattitkosítás) | Javasolja, hogy engedélyezze az inaktív adatok titkosítását az Azure SQL-adatbázisokra, valamint az azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra vonatkozóan, így hiába jutnak be illetéktelen személyek a rendszerbe, az adatokat nem fogják tudni olvasni. |

11. Ha befejezte a beállítások konfigurálását, kattintson a javaslatokat tartalmazó **Security Policy** (Biztonsági szabályzat) panel **OK** gombjára, majd az eredeti beállításokat tartalmazó **Security Policy** (Biztonsági szabályzat) panel **Save** (Mentés) gombjára.

## Biztonsági szabályzatok beállítása erőforráscsoportokhoz

Ha inkább erőforráscsoportonként szeretné beállítani a biztonsági szabályzatokat, hasonló lépéseket kell követnie, mint az előfizetések esetében. A legfontosabb különbség az, hogy ki kell bontania az előfizetés nevét, és ki kell választania az erőforráscsoportot, amelyet egyedi biztonsági szabályzattal szeretne ellátni:

![Erőforráscsoport kiválasztása](./media/security-center-policies/security-center-policies-fig4.png)

Az erőforráscsoport kiválasztását követően megnyílik a **Security policy** (Biztonsági szabályzat) panel. Az **Inheritance** (Öröklés) funkció alapértelmezés szerint engedélyezve van, ami azt jelenti, hogy az erőforráscsoport az összes biztonsági szabályzatot örökölte az előfizetés szintjéről. Ha az adott erőforráscsoport esetében egyedi biztonsági szabályzatot szeretne használni, módosítsa ezt a beállítást. Válassza a **Unique** (Egyedi) lehetőséget, és végezze el a kívánt módosításokat a **Prevention policy** (Megelőzési szabályzat) résznél.

![Erőforráscsoportra vonatkozó biztonsági szabályzat](./media/security-center-policies/security-center-policies-fig5-new.png)

> [AZURE.NOTE] Abban az esetben, ha ütközés lép fel az előfizetés-szintű és az erőforráscsoport-szintű szabályzat között, az erőforráscsoport-szintű beállítás élvez elsőbbséget.


## Következő lépések

Ebben a dokumentumban megtanulhatta az Azure Security Center biztonsági szabályzatainak konfigurálását. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – Az Azure Security Center tervezésével, valamint a bevezetés során jelentős elemekkel kapcsolatos útmutató.
- [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – A cikkből megismerheti az Azure-erőforrások állapotfigyelésének módját.
- [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben) – Ebből az írásból megtanulhatja a biztonsági riasztások kezelésének és megoldásának módját.
- [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Ez a cikk ismerteti a partnermegoldások biztonsági állapotának figyelését.
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – A szolgáltatás használatára vonatkozó gyakran ismételt kérdések.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=Jun16_HO2-->


