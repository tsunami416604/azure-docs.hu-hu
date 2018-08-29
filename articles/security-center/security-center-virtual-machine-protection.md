---
title: A gépek és az alkalmazások az Azure Security Center védelme |} A Microsoft Docs
description: Ez a dokumentum a Security Center javaslatait, segítő tárgyalja a virtuális gépek és számítógépek és a webalkalmazások és App Service Environment-környezetek védelmét.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7cf5f86d9a2d121ff54c40e27c6bc50847a4dfdf
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43133044"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>A gépek és az alkalmazások az Azure Security Center védelme
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, javaslatok, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán hoz létre. Javaslatok alkalmazása az Azure-erőforrástípus: virtuális gépek (VM) és a számítógépek, alkalmazások, hálózati, SQL, és az identitás- és hozzáférés.

Ez a cikk foglalkozik, javaslatok, amelyek a alkalmazni a gépek és alkalmazások.

## <a name="monitoring-security-health"></a>A biztonsági állapot figyelése
Az erőforrások biztonsági állapotát a figyelemmel kísérheti a **Security Center – áttekintés** irányítópultot. A **erőforrások** szakaszban azonosított problémák számát és a biztonsági állapot egyes erőforrástípusok biztosít.

Megtekintheti az összes hibáit kiválasztásával **javaslatok**. Javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](security-center-recommendations.md).

Szolgáltatási javaslatok teljes listáját a számítási műveletek és az alkalmazás létrehozásáról: [javaslatok](security-center-virtual-machine-recommendations.md).

A folytatáshoz válasszon ki **számítás és alkalmazások** alatt **erőforrások** vagy a Security Center főmenüjébe.
![Security Center irányítópultja][1]

## <a name="monitor-compute-and-app-services"></a>Számítási tevékenység figyelése és az App services
A **számítási**, négy lap található:

- **Áttekintés**: figyelés és javaslatok a Security Center által azonosított.
- **Virtuális gépek és számítógépek**: a virtuális gépek, a számítógépek és az egyes aktuális biztonsági állapotának listája.
- **Cloud Services**: a Security Center által figyelt webes és feldolgozói szerepkörök listáját.
- **App services (előzetes verzió)**: az App service Environment-környezetek és az egyes aktuális biztonsági állapotának listája.
A folytatáshoz válasszon ki **számítás és alkalmazások** alatt **erőforrások** vagy a Security Center főmenüjébe.

![Compute][2]

Minden egyes lap esetében több szakaszt hozhat létre, és az egyes szakaszokban elérhető lehetőségek kiválasztásával további részleteket tudhat meg az adott probléma megoldásához szükséges lépésekről.

### <a name="monitoring-recommendations"></a>Figyelési javaslatok
Ebben a szakaszban a virtuális gépeket és számítógépeket, amelyek az Automatikus kiépítés és azok aktuális állapotát adatgyűjtésre teljes számát jeleníti meg. A példánkban egy javaslat található: **A Monitoring Agent állapotproblémái**. Válassza ki ezt a javaslatot.

![A figyelő állapotával kapcsolatos problémák][3]

Megnyílik **A Monitoring Agent állapotproblémái** panel. Itt szerepelnek azok a virtuális gépek és számítógépek, amelyeket a Security Center nem tud sikeresen monitorozni. Részletes információkért válasszon ki egy virtuális gépet vagy számítógépet. A **FIGYELÉS ÁLLAPOTA** megmutatja az okot, amely miatt a Security Center nem tud monitorozni. A **MONITOROZÁS ÁLLAPOTA** értékeinek, leírásainak és a megoldások lépéseinek listáját a [Security Center hibaelhárítási útmutatójában](security-center-troubleshooting-guide.md) találja.

### Nem monitorozott virtuális gépek és számítógépek <a name="unmonitored-vms-and-computers"></a>
A virtuális gép vagy a számítógép nem figyelt a Security Center által, ha a gép nem fut a Microsoft Monitoring Agent bővítményt. A gépek rendelkezhetnek egy helyi ügynök már telepítve van, például a közvetlen OMS-ügynök vagy az SCOM-ügynök. Ilyen ügynökökkel rendelkező gépek eszközként van azonosítva nem figyelt, mert ezek az ügynökök teljes mértékben nem támogatottak a Security Centerben. A Security Center összes funkciójának legteljesebb kihasználása érdekében szükség van a Microsoft Monitoring Agent bővítményre.

A bővítményt a nem monitorozott virtuális gép vagy a számítógépen a már telepített helyi ügynök mellett is telepítheti. A két ügynök konfigurációja legyen megegyező, és ugyanahhoz a munkaterülethez csatlakoztassa őket. Ez lehetővé teszi, hogy a Security Center interakcióba léphessen a Microsoft Monitoring Agent bővítménnyel, és adatokat gyűjtsön. [A virtuálisgép-bővítmény engedélyezésével](../log-analytics/log-analytics-quick-collect-azurevm.md) foglalkozó témakörben találja a Microsoft Monitoring Agent bővítmény telepítésével kapcsolatos utasításokat.

[A Monitoring Agent állapotproblémái](security-center-troubleshooting-guide.md#mon-agent) szakaszban többet is megtudhat arról, hogy a Security Center miért nem tudja sikeresen monitorozni az automatikus üzembe helyezésre inicializált virtuális gépeket és számítógépeket.

### <a name="recommendations"></a>Javaslatok
Ebben a szakaszban rendelkezik minden virtuális gép és számítógép, webes és feldolgozói szerepkörök, az Azure App Service Web Apps és az Azure App Service-környezet, amely a Security Center figyeli vonatkozó javaslatok szerepelnek. Az első oszlop a javaslatokat sorolja fel. A második oszlop az adott javaslat által érintett erőforrások teljes számát jeleníti meg. A harmadik oszlop a probléma súlyosságát mutatja, ahogyan az alábbi képernyőfelvételen látható:

![Javaslatok][4]

Minden egyes javaslatokra műveleteket hajthat végre kijelölése után. Például, ha kiválasztja **hiányzó rendszerfrissítések**, a virtuális gépek számát és számítógépekre, amelyekről hiányoznak a javításokat, és a hiányzó frissítés súlyosságát jelenik meg, az alábbi képernyőképen látható módon:

![Rendszerfrissítések alkalmazása][5]

**Rendszerfrissítések alkalmazása** rendelkezik grafikus formátumban, a Windows, és egy Linux-kritikus frissítések összegzése. A második részben az alábbi információkat tartalmazó táblázat található:

- **NÉV** – A hiányzó frissítés neve.
- **VIRTUÁLIS GÉPEK ÉS SZÁMÍTÓGÉPEK SZÁMA**: Azon virtuális gépek és számítógépek teljes száma, amelyek esetében ez a frissítés hiányzik.
- **FRISSÍTÉS SÚLYOSSÁGA**: azt ismerteti, hogy az adott javaslat súlyosságát:

    - **Kritikus fontosságú**: biztonsági rés fontos erőforrásnál (alkalmazás, virtuális géphez vagy hálózati biztonsági csoport) létezik, és beavatkozást igényel.
    - **Fontos**: nem kritikus vagy kiegészítő lépések szükségesek egy folyamat befejezéséhez vagy egy biztonsági rés megszüntetéséhez.
    - **Mérsékelt**: biztonsági rés kell tömni, de nem igényel azonnali beavatkozást. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)


- **STATE** (Állapot): a javaslat aktuális állapota:

    - **Open** (Nyitott): a javaslattal egyelőre még nem foglalkoztak.
    - **In Progress** (Folyamatban): folyamatban van a javaslat alkalmazása az érintett erőforrásokra, további lépésekre nincs szükség.
    - **Resolved** (Megoldott): a javaslat alkalmazása megtörtént. (A probléma megoldása után a bejegyzés halványan jelenik meg.)

A javaslat részleteinek megtekintéséhez kattintson a hiányzó frissítés nevére a listában.

![Javaslat részletei][6]

> [!NOTE]
> Itt a biztonsági javaslatok láthatók, ugyanazok, mint a a **javaslatok** csempére. Lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](security-center-recommendations.md) javaslatok alkalmazásával kapcsolatban további információt.
>
>

### <a name="vms-and-computers"></a>Virtuális gépek és számítógépek
A virtuális gépek és számítógépek szakasz áttekintheti az összes virtuális gép és számítógép ajánlások. Ahogy az alábbi képernyőfelvételen is látható, minden oszlop egy javaslatcsoportot képvisel:

![Virtuális gép és számítógép vonatkozó javaslatok][7]

A listában szereplő ikon négy típusa van:

![Nem Azure-beli számítógép][8] Nem Azure-alapú számítógép.

![Az Azure Resource Manager-alapú][9] Az Azure Resource Manager-alapú.

![Az Azure klasszikus virtuális gép][10] Az Azure klasszikus virtuális gép.

![Virtuális gépek azonosítani a munkaterületről][11] Azok a virtuális gépek, amelyek csak a megtekintett előfizetés részét képező munkaterület alapján azonosít a rendszer. Ezek közé tartozhatnak olyan, más előfizetésekből származó virtuális gépek is, amelyek a jelen előfizetéshez tartozó munkaterületnek vannak alárendelve, valamint az SCOM közvetlen ügynök használatával telepített, erőforrás-azonosítóval nem rendelkező virtuális gépek.

Az egyes javaslatok alatt megjelenő ikonok segít gyorsan azonosítani a virtuális gép és számítógép hagyni a figyelmet, és a javaslat típusát. Szűrje segítségével válassza ki a beállítások ezen a képernyőn láthatja.

![Szűrés][12]

Az előző példában egy virtuális gépnél kritikus súlyosságú javaslat az endpoint protection kapcsolatban. További információt szeretne kapni a virtuális gép kiválasztása:

![Kritikus súlyosságú javaslat][13]

Itt láthatja a biztonsági adatok a virtuális gép vagy a számítógépen. A panel alján a javasolt műveleteket, valamint az egyes problémák súlyosságát láthatja.

### <a name="cloud-services"></a>Felhőszolgáltatások
A Cloud Services-szolgáltatások esetében a rendszer abban az esetben hozza létre a javaslatot, ha az operációs rendszer verziója elavult, ahogyan az alábbi képernyőfelvételen látható:

![Felhőszolgáltatások][14]

Egy forgatókönyv, amelyben egy javaslat (amely nem áll fenn az előző példában) meg kell kövesse a lépéseket az egyes javaslatokra kattintva frissítheti az operációs rendszer verzióját. Ha egy frissítés elérhetővé válik, a rendszer riasztást jelenít meg (a probléma súlyosságától függően vörös vagy narancssárga riasztást). Ha bejelöli ezt a riasztást a webrole1 webes (futtatja a Windows Server IIS-re automatikusan telepített webalkalmazással együtt) vagy a WorkerRole1 (futtatja a Windows Server IIS-re automatikusan telepített webalkalmazással együtt) sort, hogy további részleteket erről a javaslatról, ahogyan az a alábbi képernyőfelvételen látható:

![WorkerRole1][15]

Ha előírásszerűbb magyarázatot kíván megtekinteni erről a javaslatról, a **DESCRIPTION** (LEÍRÁS) oszlopban kattintson az **Update OS version** (Operációs rendszer verziójának frissítése) elemre.

![Operációs rendszer verziójának frissítése][16]

### <a name="app-services-preview"></a>App Services (előzetes verzió)

> [!NOTE]
> Monitoring App Service-ben van, és csak a Standard szintű Security Centerben elérhető előzetes verzióban érhető el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
>
>

A **App services**, az App service Environment-környezetek listáját látja, és a Security Center kockázatértékelés alapján állapotösszegzése elvégzett.

![App Services][17]

A listában szereplő ikon három típusa van:

![App services-környezetben][18] App services környezet.

![Webalkalmazás][19] Webes alkalmazás.

![Függvény-alkalmazás][24] Függvény-alkalmazás.

1. Válassza ki a webalkalmazást. Összegzési nézetet megnyílik a három lappal:

  - **Javaslatok**: nem sikerült, a Security Center által végzett alapján.
  - **Sikeres értékelések**: megfelelt a Security Center által végzett listája.
  - **Értékelések nem érhetők el**: értékelések, amelyek nem futhat, mert hiba történt, vagy az ajánlás listája nem releváns, az adott App Service

  A **javaslatok** minden javaslat súlyosságát és a kiválasztott webes alkalmazás a javaslatok listája.

  ![Összefoglalás megtekintése][20]

2. Válasszon ki egy javaslatot, egy leírást az ajánlást, és nem megfelelő erőforrások, a kifogástalan állapotú erőforrások és a nem vizsgált erőforrások listáját.

  ![Javaslat leírása][21]

  A **értékelések átadott** sikeres értékelések listája.  Ezek az értékelések súlyosságát, mindig zöld.

  ![Sikeres értékelések][22]

3. Válassza ki a átadott értékelés listájából az értékelés leírását, a nem megfelelő állapotú és kifogástalan állapotú erőforrások listájának és a nem vizsgált erőforrások listáját. Nem megfelelő állapotú erőforrások lapja, de a lista mindig üres lesz, mivel az értékelés átadott.

    ![Kifogástalan állapotú erőforrások][23]



## <a name="next-steps"></a>További lépések
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:


* [A virtuális gépek ismertetése az Azure Security Center-javaslatok](security-center-virtual-machine-recommendations.md)
* [Identitás és hozzáférés az Azure Security Center figyelése](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
