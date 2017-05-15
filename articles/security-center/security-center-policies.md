---
title: "Biztonsági szabályzatok beállítása az Azure Security Centerben | Microsoft Docs"
description: "Ebből a dokumentumból megismerheti az Azure Security Center biztonsági szabályzatainak konfigurálásához szükséges lépéseket."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: ec658f4c74d54a11684460c0e634303793480152
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017


---
# <a name="set-security-policies-in-azure-security-center"></a>Biztonsági szabályzatok beállítása az Azure Security Centerben
Ez a dokumentum végigvezeti a Security Center biztonsági szabályzatainak beállításához szükséges lépéseken.

## <a name="what-are-security-policies"></a>Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az egy adott előfizetésen vagy erőforráscsoporton belüli erőforrásokhoz javasol. A Security Centerben a vállalat biztonsági igényeinek és az egyes előfizetésekben szereplő alkalmazások típusának vagy az adatok érzékenységének megfelelően határozhatja meg az Azure-előfizetésekre vagy -erőforráscsoportokra vonatkozó szabályzatokat.

Különböző biztonsági követelmények vonatkozhatnak például a fejlesztésben vagy tesztelésben, illetve az éles környezetben használt erőforrásokra. A szabályozott adatokat, például személyazonosításra alkalmas adatokat használó alkalmazások is magasabb szintű biztonságot követelhetnek meg. Az Azure Security Centerben engedélyezett biztonsági szabályzatok biztonsági javaslatokkal szolgálnak, és figyelést biztosítanak, így segítenek Önnek felismerni a potenciális biztonsági réseket és elhárítani a fenyegetéseket. Az [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) cikkből további információkhoz juthat az Önnek legmegfelelőbb lehetőség meghatározásával kapcsolatban.

## <a name="set-security-policies-for-subscriptions"></a>Biztonsági szabályzatok beállítása előfizetésekhez
Az egyes előfizetésekhez vagy erőforráscsoportokhoz külön-külön biztonsági szabályzatot állíthat be. Biztonsági szabályzat módosításához az előfizetésben tulajdonos vagy közreműködő szerepkörrel kell rendelkeznie. A Security Center biztonsági szabályzatainak konfigurálásához jelentkezzen be az Azure Portalra, és kövesse az alábbiakban megadott lépéseket:

1. A Security Center irányítópultján kattintson a **Policy** (Szabályzat) csempére.
2. A megnyíló **Security Policy - Define policy per subscription or resource group** (Biztonsági szabályzat – Előfizetésre vagy erőforráscsoportra vonatkozó szabályzat beállítása) panelen válassza ki azt az előfizetést, amelyhez engedélyezni szeretné a biztonsági szabályzatot. Ha a teljes előfizetés helyett csak egy erőforráscsoporton szeretné engedélyezni a biztonsági szabályzatot, görgessen le a következő szakaszhoz, ahol az erőforráscsoportokra vonatkozó biztonsági szabályzatok beállításával kapcsolatban is talál információkat.

    ![A szabályzat definiálása](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. Megnyílik a kiválasztott előfizetéshez tartozó **Security policy** (Biztonsági szabályzat) panel, amelyen az alábbi képernyőképen láthatóhoz hasonló beállítások jelennek meg:

    ![Adatgyűjtés engedélyezése](./media/security-center-policies/security-center-policies-fig2-ga.png)

    Ezen a panelen a következő beállítások érhetőek el:

   * **Prevention policy** (Megelőzési szabályzat): Ezzel a beállítással előfizetésenként vagy erőforráscsoportonként konfigurálhatók szabályzatok.  
   * **Email notification** (E-mailes értesítés): Ezzel a beállítással olyan e-mailes értesítés konfigurálható, amelyet a rendszer a nagyon súlyos riasztások napi első előfordulása esetében küld. Az e-mail-beállítások kizárólag előfizetési szabályok esetében konfigurálhatók. Az e-mailes értesítések konfigurálásával kapcsolatos további információkért olvassa el a [Biztonsági kapcsolattartási adatok megadása az Azure Security Centerben](security-center-provide-security-contact-details.md) című cikket.
   * **Pricing tier** (Tarifacsomag): Ez a beállítás a kiválasztott tarifacsomag frissítéséhez használható. Az árképzési beállításokkal kapcsolatban további információkat a [Security Center díjszabását](security-center-pricing.md) ismertető oldalon talál.
4. A **Collect data from virtual machines** (Adatgyűjtés a virtuális gépekről) beállítás értéke legyen **On** (Bekapcsolva). Ezzel engedélyezi a meglévő és az új erőforrásokra vonatkozó automatikus naplógyűjtést.

   > [!NOTE]
   > Azt javasoljuk, hogy minden egyes előfizetés esetében kapcsolja be az adatgyűjtést annak biztosítására, hogy a biztonságfigyelés minden meglévő és új virtuális gép esetében elérhető legyen. Az adatgyűjtés engedélyezését követően a rendszer telepíti a figyelőügynököt. Ha jelenleg még nem szeretné bekapcsolni az adatgyűjtést ezen a helyen, ezt később is megteheti a **Health** (Állapot) és a **Recommendations** (Javaslatok) nézetben. Megadhatja azt is, hogy csak az előfizetésre, vagy az Ön által kiválasztott virtuális gépekre vonatkozóan engedélyezi az adatgyűjtést. A támogatott virtuális gépekkel kapcsolatban további információkat talál az [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: GYIK) című cikkben.
   >
   >
5. Ha még nem konfigurálta a tárfiókot, a **Security Policy** (Biztonsági szabályzat) panel megnyitásakor egy, az alábbi képernyőn láthatóhoz hasonló figyelmeztetést kaphat. Ha nem választ tárfiókot az egyes régiókhoz, a rendszer automatikusan létrehoz egy-egy tárfiókot minden régióhoz.

    ![Tároló kiválasztása](./media/security-center-policies/security-center-policies-fig2.png)
6. Ha ezt a figyelmeztetést látja, kattintson erre a lehetőségre, majd válassza ki a régiót az alábbi képernyőképen látható módon:

    ![Tároló kiválasztása](./media/security-center-policies/security-center-policies-fig3-ga.png)
7. Minden olyan régió esetében, ahol virtuális gépeket futtat, kiválaszthatja, hogy melyik tárfiók tárolja a virtuális gépekről összegyűjtött adatokat. Így egy földrajzi területen belül egyszerűen tárolhatja az adatokat, ami segít az adatok védelmében, és az egy helyre tartozó adatok közös joghatóság alá helyezésében. Miután eldöntötte, hogy melyik régiót szeretné használni, válassza ki a kívánt régiót, majd a tárfiókot.
8. A **Choose storage accounts** (Tárfiókok kiválasztása) panelen kattintson az **OK** gombra.

   > [!NOTE]
   > Ha szeretné, akár egy központi tárfiókban is összegyűjtheti a különböző régiókban futó virtuális gépekről származó adatokat. További információk: [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: GYIK).
   >
   >
9. A **Security Policy** (Biztonsági szabályzat) panelen kattintson az **On** (Bekapcsolás) lehetőségre azoknál a biztonsági javaslatoknál, amelyeket az előfizetésnél használni kíván. Az alábbi képernyőképen látható beállításokhoz hasonló beállítások megtekintéséhez kattintson a **Prevention policy** (Megelőzési szabályzat) elemre:

    ![A biztonsági szabályzatok kiválasztása](./media/security-center-policies/security-center-policies-fig4-newUI.png)

Az egyes beállítások megértéséhez használja az alábbi táblázatot:

| Szabályzat | Bekapcsolt állapotban |
| --- | --- |
| System updates (Rendszerfrissítések) |Lekéri az elérhető biztonsági és kritikus frissítések napi listáját a Windows Update vagy a Windows Server Update Services szolgáltatástól. A lekért lista az adott virtuális gép számára beállított szolgáltatástól függ, és javasolja a hiányzó frissítések alkalmazását. Linux rendszerek esetében a szabályzat a disztribúció által biztosított csomagkezelő rendszert használja az elérhető frissítésekkel rendelkező csomagok meghatározásához. Az [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure.md) virtuális gépeitől származó biztonsági és kritikus frissítéseket is keres. |
| OS vulnerabilities (Operációs rendszerek sebezhetőségei) |Naponta elemzi az operációs rendszer beállításait olyan problémák meghatározása érdekében, amelyek a virtuális gépet sebezhetővé tehetik a támadásokkal szemben. A szabályzat a biztonsági rések megszüntetését elősegítő konfigurációmódosításokat is javasol. A szabályzat által figyelt konfigurációkkal kapcsolatban lásd a [javasolt alapkonfigurációk listáját](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (A Windows Server 2016 jelenleg nem részesül teljes mértékű támogatásban.) |
| Endpoint protection (Végpontok védelme) |A szabályzat az összes windowsos virtuális gép esetében végpontvédelem beállítását javasolja a vírusok, kémprogramok és más kártékony szoftverek hatékonyabb azonosításához és eltávolításához. |
| Disk encryption (Lemeztitkosítás) |Nyugalmi állapotban az adatvédelmi teljesítmény javításához ajánlja a lemeztitkosítás engedélyezését az összes virtuális gépen. |
| Network security groups (Hálózati biztonsági csoportok) |[Hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md) konfigurálását ajánlja a be- és kimenő adatforgalom vezérlésére a nyilvános végpontokkal rendelkező virtuális gépek esetében. Az alhálózatra beállított hálózati biztonsági csoportokat az összes virtuális géphez tartozó hálózati adapter örökli, kivéve, ha Ön más beállítást ad meg. A hálózati biztonsági csoportok konfigurálásának figyelése mellett ez a szabályzat a bejövő biztonsági szabályokat is ellenőrzi, és azonosítja azokat a szabályokat, amelyek engedélyezik a bejövő forgalmat. |
| Web application firewall (Webalkalmazási tűzfal) |Webalkalmazási tűzfal kiépítését javasolja a virtuális gépeken, ha az alábbiak valamelyike teljesül:</br></br>A rendszer [példányszintű nyilvános IP](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP)-címet használ, és a kapcsolódó hálózati biztonsági csoportra vonatkozó bejövő biztonsági szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva.</br></br>A rendszer elosztott terhelésű IP-címet használ, és a kapcsolódó terheléselosztás és a hálózati címfordításra (NAT) vonatkozó bejövő szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva. (A további információkat az [Azure Resource Manager support for Load Balancer](../load-balancer/load-balancer-arm.md) (Az Azure Resource Manager támogatása a terheléselosztó számára)) című rész tartalmazza. |
| Next generation firewall (Új generációs tűzfal) |Az Azure-ba épített hálózati biztonsági csoportokon túlra is kiterjesztheti a hálózati védelmet. A Security Center felderíti az üzemelő példányokat, amelyekhez új generációs tűzfal használata javasolt, és engedélyezi a virtuális berendezés kiépítését. |
| SQL-naplózás és fenyegetésészlelés |Javasolja, hogy a megfelelés, a jobb fenyegetésészlelés és a támadások hatékonyabb kivizsgálásához engedélyezze az Azure Database-hozzáférések naplózását. |
| SQL transparent data encryption (SQL transzparens adattitkosítás) |Javasolja, hogy engedélyezze az inaktív adatok titkosítását az Azure SQL Database számára, valamint az azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra vonatkozóan. Így hiába jutnak be illetéktelen személyek a rendszerbe, az adatokat nem fogják tudni olvasni. |
| Sebezhetőségi felmérés |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| Storage-titkosítás |Ez a szolgáltatás jelenleg Azure-blobok és -fájlok számára érhető el. Vegye figyelembe, hogy a Storage szolgáltatás titkosításának engedélyezése után csak az új adatok lesznek titkosítva, és a tárfiók meglévő fájljai titkosítatlanok maradnak. |

Miután minden beállítást konfigurált, kattintson a javaslatokat tartalmazó **Security Policy** (Biztonsági szabályzat) panel **OK** gombjára, majd az eredeti beállításokat tartalmazó **Security Policy** (Biztonsági szabályzat) panel **Save** (Mentés) gombjára.

## <a name="set-security-policies-for-resource-groups"></a>Biztonsági szabályzatok beállítása erőforráscsoportokhoz
Ha inkább erőforráscsoportonként szeretné beállítani a biztonsági szabályzatokat, hasonló lépéseket kell követnie, mint az előfizetésekre vonatkozó biztonsági szabályzatok beállításánál. A legfontosabb különbség az, hogy ki kell bontania az előfizetés nevét, és ki kell választania azt az erőforráscsoportot, amelyet egyedi biztonsági szabályzattal szeretne ellátni:

![Erőforráscsoport kiválasztása](./media/security-center-policies/security-center-policies-fig5-ga.png)

Az erőforráscsoport kiválasztását követően megnyílik a **Security policy** (Biztonsági szabályzat) panel. Alapértelmezés szerint az **Inheritance** (Öröklés) beállítás engedélyezve van. Ez azt jelenti, hogy ez az erőforráscsoport az összes biztonsági szabályzatot örökli az előfizetés szintjéről. Ha egy adott erőforráscsoport esetében egyedi biztonsági szabályzatot szeretne használni, módosíthatja ezt a beállítást. Ebben az esetben válassza a **Unique** (Egyedi) lehetőséget, és végezze el a kívánt módosításokat a **Prevention policy** (Megelőzési szabályzat) résznél.

![Erőforráscsoportra vonatkozó biztonsági szabályzat](./media/security-center-policies/security-center-policies-fig6-ga.png)

> [!NOTE]
> Abban az esetben, ha ütközés lép fel az előfizetés-szintű és az erőforráscsoport-szintű szabályzat között, az erőforráscsoport-szintű beállítás élvez elsőbbséget.
>
>

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban megtanulhatta az Azure Security Center biztonsági szabályzatainak konfigurálását. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md). A tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md). A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md). A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

