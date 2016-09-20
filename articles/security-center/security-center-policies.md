<properties
   pageTitle="Biztonsági szabályzatok beállítása az Azure Security Centerben | Microsoft Azure"
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
   ms.date="08/26/2016"
   ms.author="yurid"/>

# Biztonsági szabályzatok beállítása az Azure Security Centerben
Ez a dokumentum végigvezeti a Security Center biztonsági szabályzatainak beállításához szükséges lépéseken.

## Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az egy adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol. A Security Centerben a vállalat biztonsági igényeinek és az egyes előfizetésekben szereplő alkalmazások típusának vagy az adatok érzékenységének megfelelően határozhatja meg az Azure-előfizetésekre vagy -erőforráscsoportokra vonatkozó szabályzatokat.

Például különböző biztonsági követelmények vonatkozhatnak a fejlesztésben vagy tesztelésben, illetve az éles környezetben használt erőforrásokra. A szabályozott adatokkal (például személyazonosításra alkalmas adatokkal) működő alkalmazások is magasabb szintű biztonságot követelhetnek meg. Az Azure Security Centerben engedélyezett biztonsági szabályzatok biztonsági javaslatokkal szolgálnak és megfigyelést biztosítanak, így segítenek felismerni a potenciális biztonsági réseket és elhárítani a fenyegetéseket. Az [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) cikkből további információkhoz juthat az Önnek legmegfelelőbb lehetőségről.

## Biztonsági szabályzatok beállítása előfizetésekhez

Az egyes előfizetésekhez vagy erőforráscsoportokhoz külön-külön biztonsági szabályzatot állíthat be. A biztonsági szabályzat módosításához az előfizetésben Tulajdonos vagy Közreműködő szerepkörrel kell rendelkeznie. A Security Center biztonsági szabályzatainak konfigurálásához nyissa meg az Azure Portalt, és kövesse az előzőekben megadott lépéseket:

1. A Security Center irányítópultján kattintson a **Policy** (Szabályzat) csempére.

2. A jobb oldalon megnyíló **Security Policy - Define policy per subscription or resource group** (Biztonsági szabályzat – Előfizetésre vagy erőforráscsoportra vonatkozó szabályzat beállítása) panelen válassza ki az előfizetést, amelyhez engedélyezni szeretné a biztonsági szabályzatot. Ha a teljes előfizetés helyett csak egy erőforráscsoporton szeretné engedélyezni a biztonsági szabályzatot, görgessen le a következő szakaszhoz, ahol az erőforráscsoportokra vonatkozó szabályzatokkal kapcsolatban is talál információkat.

    ![A szabályzat definiálása](./media/security-center-policies/security-center-policies-fig1-ga.png)

3. Megnyílik az adott előfizetéshez tartozó **Security policy** (Biztonsági házirend) panel, amelyen az alábbi képernyőn láthatóhoz hasonló beállítások jelennek meg:

    ![Adatgyűjtés engedélyezése](./media/security-center-policies/security-center-policies-fig2-ga.png)

    Ezen a panelen a következő beállítások érhetőek el:
    - **Prevention policy** (Megelőzési szabályzat): Ezzel a beállítással előfizetésenként vagy erőforráscsoportonként konfigurálhatók szabályzatok.  
    - **Email notification** (E-mailes értesítés): A rendszer a nagyon súlyos riasztások napi első előfordulása esetében e-mailes értesítést küld. Az e-mail-beállítások kizárólag előfizetési szabályok esetében konfigurálhatóak. Az e-mailes értesítések konfigurálásával kapcsolatos további információkért olvassa el a [Biztonsági kapcsolattartási adatok megadása az Azure Security Centerben](security-center-provide-security-contact-details.md) című cikket. 
    - **Pricing tier** (Tarifacsomag): Ez a beállítás a választott tarifacsomagról történő frissítéshez használható. Az árképzési beállításokkal kapcsolatos további információkat a [Security Center oldalán](https://azure.microsoft.com/pricing/details/security-center/) talál.

    
4.  A **Collect data from virtual machines** (Adatgyűjtés a virtuális gépekről) beállítás értéke legyen **On** (Bekapcsolva). Ezzel engedélyezi a meglévő és az új erőforrásokra vonatkozó automatikus naplógyűjtést. 

    >[AZURE.NOTE] Érdemes minden egyes előfizetés esetében bekapcsolni az adatgyűjtést annak biztosítására, hogy a biztonságfigyelés minden meglévő és új virtuális gép esetében elérhető legyen. Az adatgyűjtés engedélyezését követően a rendszer telepíti a figyelőügynököt. Ha jelenleg még nem szeretné bekapcsolni az adatgyűjtést ezen a helyen, ezt később is megteheti az állapotot és javaslatokat tartalmazó nézetben. Megadhatja azt is, hogy az egész előfizetésre, vagy csupán az Ön által kiválasztott virtuális gépekre vonatkozóan szeretné bekapcsolni az adatgyűjtést. A támogatott virtuális gépekkel kapcsolatban további információkat talál az [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: GYIK) című cikkben.

5. Ha még nem konfigurálta a tárfiókot, a **Security Policy** (Biztonsági szabályzat) panel megnyitásakor egy, az alábbi képernyőn láthatóhoz hasonló figyelmeztetést kaphat:

    ![Tároló kiválasztása](./media/security-center-policies/security-center-policies-fig2.png)

6. Ha ezt a figyelmeztetést látja, kattintson erre a lehetőségre, majd válassza ki a régiót az alábbi képernyőn látható módon:

    ![Tároló kiválasztása](./media/security-center-policies/security-center-policies-fig3-ga.png)

7. Minden régió esetében, ahol virtuális gépei futnak, válassza ki, hogy mely tárfiók tárolja a virtuális gépekről összegyűjtött adatokat. Így egy földrajzi területen belül tárolhatja az adatokat, ami segít az adatok védelmében, és az egy helyre tartozó adatok közös joghatóság alá helyezésében. Ha eldöntötte, hogy melyik régiót szeretné használni, válassza ki a kívánt régiót, majd a tárfiókot.

8. A **Choose storage accounts** (Tárfiókok kiválasztása) panelen kattintson az **OK** gombra.

    > [AZURE.NOTE] Ha szeretné, akár egy központi tárfiókban is összegyűjtheti a különböző régiókban futó virtuális gépekről származó adatokat. További információk: [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: GYIK).

9. A **Security Policy** (Biztonsági szabályzat) panelen kattintson az **On** (Bekapcsolás) lehetőségre azoknál a biztonsági javaslatoknál, amelyeket az előfizetésnél használni kíván. Kattintson a **Prevention policy** (Megelőzési szabályzat) elemre. A következő képernyőn látható módon megnyílik a **Security Policy** (Biztonsági szabályzat) panel: 

    ![A biztonsági szabályzatok kiválasztása](./media/security-center-policies/security-center-policies-fig4-ga.png)

Az egyes beállítások funkciója az alábbi táblázatban szerepel:

| Szabályzat | Bekapcsolt állapotban |
|----- |-----|
| System Updates (Rendszerfrissítések) | Naponta lekéri az elérhető biztonsági és fontos frissítések listáját a Windows Update-től vagy a WSUS-tól (attól függően, hogy melyik szolgáltatást használja a virtuális gép), és ennek alapján javasolja a hiányzó frissítések alkalmazását. A mellékelt csomagkezelő rendszer disztribúciójának használatával keres frissítéseket Linux rendszerekben annak meghatározására, hogy mely csomagokhoz érhetőek el frissítések. A [Cloud Services](./cloud-services/cloud-services-how-to-configure.md) virtuális gépeitől származó biztonsági és kritikus frissítéseket is keres. |
| Operációs rendszerek sebezhetőségei | Naponta elemzi az operációs rendszer azon konfigurációit, amelyek sebezhetővé teszik a virtuális gépet, és a biztonsági rések megszüntetését elősegítő konfigurációmódosításokat javasol. A szabályzat által figyelt konfigurációkkal kapcsolatban lásd a [javasolt alapkonfigurációk listáját](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). |
| Endpoint Protection (Végpontok védelme) | A szabályzat az összes windowsos virtuális gép esetében végpontvédelem beállítását javasolja a vírusok, kémprogramok és más kártékony szoftverek hatékonyabb azonosításához és eltávolításához.|
| Lemeztitkosítás | Nyugalmi állapotban az adatvédelmi teljesítmény javításához ajánlja a lemeztitkosítás engedélyezését az összes virtuális gépen. 
| Network Security Groups (Hálózati biztonsági csoportok) | Ajánlja a [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md) (NSG-k) a be- és kimenő adatforgalom vezérlésére történő konfigurálását a nyilvános végpontokkal rendelkező virtuális gépek irányába. Az alhálózatra beállított NSG-ket az összes virtuális géphez tartozó hálózati adapter örökli, kivéve, ha Ön más beállítást ad meg. Az NSG-k konfigurálásának figyelése mellett ez a lehetőség a bejövő biztonsági szabályokat is ellenőrzi, és megállapítja, hogy vannak-e köztük olyanok, amelyek engedélyezik a bejövő forgalmat. |
| Web Application Firewall (Webalkalmazási tűzfal) | Webalkalmazási tűzfal kiépítését javasolja a virtuális gépeken a következő esetekben: [példányszintű nyilvános IP-címet](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP-t) használ, és az ahhoz tartozó NSG bejövő biztonsági szabályait úgy állították be, hogy engedélyezzék a 80-as/443-as port elérését. Elosztott terhelésű IP-címet (virtuális IP-címet) használ, és az ahhoz tartozó terheléselosztási és bejövő NAT-re vonatkozó szabályokat úgy állították be, hogy engedélyezzék a 80-as/443-as port elérését (további információkért lásd: [Azure Resource Manager Support for Load Balancer](../load-balancer/load-balancer-arm.md) (Az Azure Resource Manager támogatása a Terheléselosztó számára). |
| Next Generation Firewall (Új generációs tűzfal) | Ezzel az Azure-ba épített hálózati biztonsági csoportokon túlra is kiterjesztheti a hálózati védelmet. A Security Center felfedezi az üzemelő példányokat, amelyekhez új generációs tűzfal használata javasolt, és engedélyezi a virtuális berendezés kiépítését. |
| SQL Auditing (SQL-naplózás) | Javasolja, hogy a megfelelés, a jobb észlelés és a támadások hatékonyabb kivizsgálásához engedélyezze az Azure SQL Server kiszolgálók és adatbázisok naplózását. |
| SQL Transparent Data Encryption (SQL transzparens adattitkosítás) | Javasolja, hogy engedélyezze az inaktív adatok titkosítását az Azure SQL-adatbázisokra, valamint az azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra vonatkozóan, így hiába jutnak be illetéktelen személyek a rendszerbe, az adatokat nem fogják tudni olvasni. |
    
Ha befejezte a beállítások konfigurálását, kattintson a javaslatokat tartalmazó **Security Policy** (Biztonsági szabályzat) panel **OK** gombjára, majd az eredeti beállításokat tartalmazó **Security Policy** (Biztonsági szabályzat) panel **Save** (Mentés) gombjára.

## Biztonsági szabályzatok beállítása erőforráscsoportokhoz

Ha inkább erőforráscsoportonként szeretné beállítani a biztonsági szabályzatokat, hasonló lépéseket kell követnie, mint az előfizetések esetében. A legfontosabb különbség az, hogy ki kell bontania az előfizetés nevét, és ki kell választania az erőforráscsoportot, amelyet egyedi biztonsági szabályzattal szeretne ellátni:

![Erőforráscsoport kiválasztása](./media/security-center-policies/security-center-policies-fig5-ga.png)

Az erőforráscsoport kiválasztását követően megnyílik a **Security policy** (Biztonsági szabályzat) panel. Az **Inheritance** (Öröklés) funkció alapértelmezés szerint engedélyezve van, ami azt jelenti, hogy az erőforráscsoport az összes biztonsági szabályzatot örökölte az előfizetés szintjéről. Ha az adott erőforráscsoport esetében egyedi biztonsági szabályzatot szeretne használni, módosítsa ezt a beállítást. Válassza a **Unique** (Egyedi) lehetőséget, és végezze el a kívánt módosításokat a **Prevention policy** (Megelőzési szabályzat) résznél.

![Erőforráscsoportra vonatkozó biztonsági szabályzat](./media/security-center-policies/security-center-policies-fig6-ga.png)

> [AZURE.NOTE] Abban az esetben, ha ütközés lép fel az előfizetés-szintű és az erőforráscsoport-szintű szabályzat között, az erőforráscsoport-szintű beállítás élvez elsőbbséget.


## Lásd még:

Ebben a dokumentumban megtanulhatta az Azure Security Center biztonsági szabályzatainak konfigurálását. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) – A tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
- [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának megfigyeléséhez.
- [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
- [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
- [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center – gyakran ismételt kérdések) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.



<!--HONumber=sep16_HO1-->


