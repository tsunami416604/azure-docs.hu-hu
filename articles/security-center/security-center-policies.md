---
title: "Biztonsági szabályzatok beállítása az Azure Security Centerben | Microsoft Docs"
description: "Ebből a dokumentumból megismerheti az Azure Security Center biztonsági szabályzatainak konfigurálásához szükséges lépéseket."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: aefec15c72c6cf8389a29b03be70abb4c7f020b9
ms.contentlocale: hu-hu
ms.lasthandoff: 07/19/2017

---
# <a name="set-security-policies-in-azure-security-center"></a>Biztonsági szabályzatok beállítása az Azure Security Centerben
Ez a dokumentum végigvezeti a Security Center biztonsági szabályzatainak beállításához szükséges lépéseken.

>[!NOTE] 
>2017 júniusának elejétől kezdve a Security Center a Microsoft Monitoring Agent használatával gyűjti össze és tárolja az adatokat. További információk: [Az Azure Security Center Platform migrálása](security-center-platform-migration.md). A jelen cikkben található információk a Security Center a Microsoft Monitoring Agentre való váltás után elérhető funkcióit ismertetik.
>

## <a name="what-are-security-policies"></a>Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg azoknak a vezérlőelemeknek a körét, amelyeket a rendszer az egy adott előfizetésen belüli erőforrásokhoz javasol. A Security Centerben a vállalat biztonsági igényeinek és az egyes előfizetésekben szereplő alkalmazások típusának vagy az adatok bizalmasságának megfelelően határozhatja meg az Azure-előfizetésekre vonatkozó szabályzatokat.

Különböző biztonsági követelmények vonatkozhatnak például a fejlesztésben vagy tesztelésben, illetve az éles környezetben használt erőforrásokra. A szabályozott adatokat, például személyazonosításra alkalmas adatokat használó alkalmazások is magasabb szintű biztonságot követelhetnek meg. Az Azure Security Centerben engedélyezett biztonsági szabályzatok biztonsági javaslatokkal szolgálnak, és figyelést biztosítanak, így segítenek Önnek felismerni a potenciális biztonsági réseket és elhárítani a fenyegetéseket. Az [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md) cikkből további információkhoz juthat az Önnek legmegfelelőbb lehetőség meghatározásával kapcsolatban.

## <a name="set-security-policies"></a>Biztonsági házirendek beállítása
Az egyes előfizetésekhez külön-külön biztonsági szabályzatot állíthat be. Biztonsági szabályzat módosításához az előfizetésben tulajdonos vagy közreműködő szerepkörrel kell rendelkeznie. A Security Center biztonsági szabályzatainak konfigurálásához jelentkezzen be az Azure Portalra, és kövesse az alábbiakban megadott lépéseket:

1. A Security Center irányítópultján kattintson a **Policy** (Szabályzat) csempére.
2. A megnyíló Security Policy (Biztonsági szabályzat) panelen válassza ki azt az előfizetést, amelyhez engedélyezni kívánja a biztonsági szabályzatot.

    ![A szabályzat definiálása](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. Megnyílik a kiválasztott előfizetéshez tartozó **Security policy** (Biztonsági szabályzat) panel, amelyen néhány beállítás jelenik meg. Ezen a panelen a következő beállítások érhetőek el:

   * **Prevention policy** (Megelőzési szabályzat): Ezzel a beállítással előfizetésenként konfigurálhatók a szabályzatok.  
   * **Email notification** (E-mailes értesítés): Ezzel a beállítással olyan e-mailes értesítés konfigurálható, amelyet a rendszer a nagyon súlyos riasztások napi első előfordulása esetében küld. Az e-mail-beállítások kizárólag előfizetési szabályok esetében konfigurálhatók. Az e-mailes értesítések konfigurálásával kapcsolatos további információkért olvassa el a [Biztonsági kapcsolattartási adatok megadása az Azure Security Centerben](security-center-provide-security-contact-details.md) című cikket.
   * **Pricing tier** (Tarifacsomag): Ez a beállítás a kiválasztott tarifacsomag frissítéséhez használható. Az árképzési beállításokkal kapcsolatban további információkat a [Security Center díjszabását](security-center-pricing.md) ismertető oldalon talál.
4. A **Collect data from virtual machines** (Adatgyűjtés a virtuális gépekről) beállítás értéke legyen **On** (Bekapcsolva). Ezzel engedélyezi a meglévő és az új erőforrásokra vonatkozó automatikus naplógyűjtést a Microsoft Monitoring Agenttel. Ez ugyanaz az ügynök, amelyet az Operations Management Suite és a Log Analytics szolgáltatás is használ. A rendszer az ügynökből összegyűjtött adatokat az Azure-előfizetéséhez társított, meglévő Log Analytics-munkaterületen tárolja, illetve egy új munkaterületen, a virtuális gép földrajzi helyét is figyelembe véve.

5. A **Security Policy** (Biztonsági szabályzat) panelen kattintson a **Prevention policy** (Megelőzési szabályzat) elemre a rendelkezésre álló beállítások megtekintéséhez. Kattintson az **On** (Bekapcsolás) elemre az előfizetés esetében releváns biztonsági javaslatok engedélyezéséhez.

    ![A biztonsági szabályzatok kiválasztása](./media/security-center-policies/security-center-policies-fig4-newUI.png)

Az egyes beállítások megértéséhez használja az alábbi táblázatot:

| Szabályzat | Bekapcsolt állapotban |
| --- | --- |
| System updates (Rendszerfrissítések) |Lekéri az elérhető biztonsági és kritikus frissítések napi listáját a Windows Update vagy a Windows Server Update Services szolgáltatástól. A lekért lista az adott virtuális gép számára beállított szolgáltatástól függ, és javasolja a hiányzó frissítések alkalmazását. Linux rendszerek esetében a szabályzat a disztribúció által biztosított csomagkezelő rendszert használja az elérhető frissítésekkel rendelkező csomagok meghatározásához. Az [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure.md) virtuális gépeitől származó biztonsági és kritikus frissítéseket is keres. |
| OS vulnerabilities (Operációs rendszerek sebezhetőségei) |Naponta elemzi az operációs rendszer beállításait olyan problémák meghatározása érdekében, amelyek a virtuális gépet sebezhetővé tehetik a támadásokkal szemben. A szabályzat a biztonsági rések megszüntetését elősegítő konfigurációmódosításokat is javasol. A szabályzat által figyelt konfigurációkkal kapcsolatban lásd a [javasolt alapkonfigurációk listáját](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (A Windows Server 2016 jelenleg nem részesül teljes mértékű támogatásban.) |
| Endpoint protection (Végpontok védelme) |A szabályzat az összes windowsos virtuális gép esetében végpontvédelem beállítását javasolja a vírusok, kémprogramok és más kártékony szoftverek hatékonyabb azonosításához és eltávolításához. |
| Disk encryption (Lemeztitkosítás) |Nyugalmi állapotban az adatvédelmi teljesítmény javításához ajánlja a lemeztitkosítás engedélyezését az összes virtuális gépen. |
| Network security groups (Hálózati biztonsági csoportok) |[Hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md) konfigurálását ajánlja a be- és kimenő adatforgalom vezérlésére a nyilvános végpontokkal rendelkező virtuális gépek esetében. Az alhálózatra beállított hálózati biztonsági csoportokat az összes virtuális géphez tartozó hálózati adapter örökli, kivéve, ha Ön más beállítást ad meg. A hálózati biztonsági csoportok konfigurálásának figyelése mellett ez a szabályzat a bejövő biztonsági szabályokat is ellenőrzi, és azonosítja azokat a szabályokat, amelyek engedélyezik a bejövő forgalmat. |
| Web application firewall (Webalkalmazási tűzfal) |Webalkalmazási tűzfal kiépítését javasolja a virtuális gépeken, ha az alábbiak valamelyike teljesül: </br></br>A rendszer [példányszintű nyilvános IP](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP)-címet használ, és a kapcsolódó hálózati biztonsági csoportra vonatkozó bejövő biztonsági szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva.</br></br>A rendszer elosztott terhelésű IP-címet használ, és a kapcsolódó terheléselosztás és a hálózati címfordításra (NAT) vonatkozó bejövő szabályok a 80/443-as port elérésének engedélyezésére vannak konfigurálva. (A további információkat az [Azure Resource Manager support for Load Balancer](../load-balancer/load-balancer-arm.md) (Az Azure Resource Manager támogatása a terheléselosztó számára)) című rész tartalmazza. |
| Next generation firewall (Új generációs tűzfal) |Az Azure-ba épített hálózati biztonsági csoportokon túlra is kiterjesztheti a hálózati védelmet. A Security Center felderíti az üzemelő példányokat, amelyekhez új generációs tűzfal használata javasolt, és engedélyezi a virtuális berendezés kiépítését. |
| SQL-naplózás és fenyegetésészlelés |Javasolja, hogy a megfelelés, a jobb fenyegetésészlelés és a támadások hatékonyabb kivizsgálásához engedélyezze az Azure Database-hozzáférések naplózását. |
| SQL-titkosítás |Javasolja, hogy engedélyezze az inaktív adatok titkosítását az Azure SQL Database számára, valamint az azokhoz kapcsolódó biztonsági mentési és tranzakciós naplófájlokra vonatkozóan. Így hiába jutnak be illetéktelen személyek a rendszerbe, az adatokat nem fogják tudni olvasni. |
| Sebezhetőségi felmérés |Javasolja, hogy telepítsen egy biztonsági rések felmérése szolgáló megoldást a virtuális gépére. |
| Storage-titkosítás |Ez a szolgáltatás jelenleg Azure-blobok és -fájlok számára érhető el. Vegye figyelembe, hogy a Storage szolgáltatás titkosításának engedélyezése után csak az új adatok lesznek titkosítva, és a tárfiók meglévő fájljai titkosítatlanok maradnak. |

Miután minden beállítást konfigurált, kattintson a javaslatokat tartalmazó **Security Policy** (Biztonsági szabályzat) panel **OK** gombjára, majd az eredeti beállításokat tartalmazó **Security Policy** (Biztonsági szabályzat) panel **Save** (Mentés) gombjára.

> [!NOTE]
> A tarifacsomag továbbra is érvényes az erőforráscsoportok szintjére. További tudnivalókért tekintse meg az [Árképzést](https://azure.microsoft.com/pricing/details/security-center/) ismertető oldalt.
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

