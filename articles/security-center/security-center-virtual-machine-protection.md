---
title: A gépek és az alkalmazások az Azure Security Center védelme |} A Microsoft Docs
description: Ez a dokumentum a Security Center javaslatait, segítő tárgyalja a virtuális gépek és számítógépek és a webalkalmazások és App Service Environment-környezetek védelmét.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/27/2019
ms.author: monhaber
ms.openlocfilehash: 8dcaa9b98292e66d81daf3d115159b0c0c1124af
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106724"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>A gépek és az alkalmazások az Azure Security Center védelme
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, javaslatok, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán hoz létre. Javaslatok alkalmazása az Azure-erőforrástípus: virtuális gépek (VM) és a számítógépek, alkalmazások, hálózati, SQL, és az identitás- és hozzáférés.

Ez a cikk foglalkozik, javaslatok, amelyek a alkalmazni a gépek és alkalmazások.

## <a name="monitoring-security-health"></a>A biztonsági állapot figyelése
Az erőforrások biztonsági állapotát a figyelemmel kísérheti a **Security Center – áttekintés** irányítópultot. A **erőforrások** szakaszban azonosított problémák számát és a biztonsági állapot egyes erőforrástípusok biztosít.

Megtekintheti az összes hibáit kiválasztásával **javaslatok**. Javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](security-center-recommendations.md).

Szolgáltatási javaslatok teljes listáját a számítási műveletek és az alkalmazás létrehozásáról: [javaslatok](security-center-virtual-machine-recommendations.md).

A folytatáshoz válasszon ki **számítás és alkalmazások** alatt **erőforrások** vagy a Security Center főmenüjébe.
![Security Center irányítópultja](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Számítási és az app services monitoringja
A **számítás és alkalmazások**, a következő lap található:

- **Áttekintés**: Monitorozás és a Security Center által azonosított javaslatok.
- **Virtuális gépek és számítógépek**: a virtuális gépek és számítógépek listája, valamint mindegyikre az aktuális biztonsági állapota.
- **Felhőszolgáltatások**: a Security Center által figyelt összes webes és feldolgozói szerepkör listája.
- **App services**: az App service Environment-környezetek és az egyes aktuális biztonsági állapotának listája.
- **Tárolók (előzetes verzió)**: az IaaS Linuxos gépeken és a biztonsági konfigurációk értékelése a saját Docker üzemeltetett tárolók listájában.
- **Számítási erőforrások (előzetes verzió)**: a számítási erőforrások, például a Service Fabric-fürtök és az Event hubs a javaslatok listája.

A folytatáshoz válasszon ki **számítás és alkalmazások** alatt **erőforrás biztonsági higiéniai**.

![Compute](./media/security-center-virtual-machine-recommendations/compute.png)

Minden egyes lap esetében több szakaszt hozhat létre, és az egyes szakaszokban elérhető lehetőségek kiválasztásával további részleteket tudhat meg az adott probléma megoldásához szükséges lépésekről.

### Nem monitorozott virtuális gépek és számítógépek <a name="unmonitored-vms-and-computers"></a>
A virtuális gép vagy a számítógép nem figyelt a Security Center által, ha a gép nem fut a Microsoft Monitoring Agent bővítményt. A gépek rendelkezhetnek egy helyi ügynök már telepítve van, például a közvetlen OMS-ügynök vagy az SCOM-ügynök. Ilyen ügynökökkel rendelkező gépek eszközként van azonosítva nem figyelt, mert ezek az ügynökök teljes mértékben nem támogatottak a Security Centerben. A Security Center összes funkciójának legteljesebb kihasználása érdekében szükség van a Microsoft Monitoring Agent bővítményre.

A bővítményt a nem monitorozott virtuális gép vagy a számítógépen a már telepített helyi ügynök mellett is telepítheti. A két ügynök konfigurációja legyen megegyező, és ugyanahhoz a munkaterülethez csatlakoztassa őket. Ez lehetővé teszi, hogy a Security Center interakcióba léphessen a Microsoft Monitoring Agent bővítménnyel, és adatokat gyűjtsön. [A virtuálisgép-bővítmény engedélyezésével](../azure-monitor/learn/quick-collect-azurevm.md) foglalkozó témakörben találja a Microsoft Monitoring Agent bővítmény telepítésével kapcsolatos utasításokat.

[A Monitoring Agent állapotproblémái](security-center-troubleshooting-guide.md#mon-agent) szakaszban többet is megtudhat arról, hogy a Security Center miért nem tudja sikeresen monitorozni az automatikus üzembe helyezésre inicializált virtuális gépeket és számítógépeket.

### <a name="recommendations"></a>Javaslatok
Ebben a szakaszban rendelkezik minden virtuális gép és számítógép, webes és feldolgozói szerepkörök, az Azure App Service Web Apps és az Azure App Service-környezet, amely a Security Center figyeli vonatkozó javaslatok szerepelnek. Az első oszlop a javaslatokat sorolja fel. A második oszlop az adott javaslat által érintett erőforrások teljes számát jeleníti meg. A harmadik oszlop a probléma súlyosságát mutatja.

Minden egyes javaslatokra műveleteket hajthat végre kijelölése után. Például, ha kiválasztja **hiányzó rendszerfrissítések**, a virtuális gépek számát és számítógépekre, amelyekről hiányoznak a javítások és a hiányzó frissítés súlyosságát jelenik meg.

**Rendszerfrissítések alkalmazása** rendelkezik grafikus formátumban, a Windows, és egy Linux-kritikus frissítések összegzése. A második részben az alábbi információkat tartalmazó táblázat található:

- **NÉV**: A hiányzó frissítés nevére.
- **NO. Virtuális gépek és számítógépek**: Virtuális gépek és számítógépek, amelyekről frissítések hiányoznak a frissítés teljes száma.
- **FRISSÍTÉS SÚLYOSSÁGA**: Ismerteti, hogy az adott javaslat súlyosságát:

    - **Kritikus fontosságú**: Biztonsági rés fontos erőforrásnál (alkalmazás, virtuális géphez vagy hálózati biztonsági csoport) létezik, és beavatkozást igényel.
    - **Fontos**: A nem kritikus vagy kiegészítő lépések szükségesek egy folyamat befejezéséhez vagy egy biztonsági rés megszüntetéséhez.
    - **Mérsékelt**: Biztonsági rés kell tömni, de nem igényel azonnali beavatkozást. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)


- **ÁLLAPOT**: A javaslat aktuális állapota:

    - **Nyissa meg**: Az ajánlás még nem foglalkoztak.
    - **Folyamatban lévő**: Az ajánlás jelenleg érvényesül ezeket az erőforrásokat, és ilyenkor Ön nem szükséges.
    - **Megoldott**: Az ajánlás már befejeződött. (A probléma megoldása után a bejegyzés halványan jelenik meg.)

A javaslat részleteinek megtekintéséhez kattintson a hiányzó frissítés nevére a listában.


> [!NOTE]
> Itt a biztonsági javaslatok láthatók, ugyanazok, mint a a **javaslatok** csempére. Lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](security-center-recommendations.md) javaslatok alkalmazásával kapcsolatban további információt.
>
>

### <a name="vms-and-computers"></a>Virtuális gépek és számítógépek
A virtuális gépek és számítógépek szakasz áttekintheti az összes virtuális gép és számítógép ajánlások. Minden oszlop egy javaslatcsoportot képvisel.

![Javaslatok virtuális gépekhez és számítógépekhez](./media/security-center-virtual-machine-recommendations/vm-computers.png)

A listában szereplő ikon négy típusa van:

![Nem Azure-beli számítógép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Nem Azure-alapú számítógép.

![Az Azure Resource Manager-alapú](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Az Azure Resource Manager-alapú.

![Az Azure klasszikus virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Az Azure klasszikus virtuális gép.


![Virtuális gépek azonosítani a munkaterületről](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Azok a virtuális gépek, amelyek csak a megtekintett előfizetés részét képező munkaterület alapján azonosít a rendszer. Ezek közé tartozhatnak olyan, más előfizetésekből származó virtuális gépek is, amelyek a jelen előfizetéshez tartozó munkaterületnek vannak alárendelve, valamint az SCOM közvetlen ügynök használatával telepített, erőforrás-azonosítóval nem rendelkező virtuális gépek.

Az egyes javaslatok alatt megjelenő ikonok segít gyorsan azonosítani a virtuális gép és számítógép hagyni a figyelmet, és a javaslat típusát. A listában úgy kereshet is használhatja a szűrők **erőforrástípus** , illetve **súlyossági**.

Részletezheti a biztonsági javaslatok láthatók az egyes virtuális Gépekhez, kattintson a virtuális gépen.
Itt láthatja a biztonsági adatok a virtuális gép vagy a számítógépen. A panel alján a javasolt műveleteket, valamint az egyes problémák súlyosságát láthatja.
![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Felhőszolgáltatások
A cloud services esetében javaslatot jön létre, ha az operációs rendszer verziója elavult.

![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Egy forgatókönyv, amelyben egy javaslat (amely nem áll fenn az előző példában) meg kell kövesse a lépéseket az egyes javaslatokra kattintva frissítheti az operációs rendszer verzióját. Ha egy frissítés elérhetővé válik, a rendszer riasztást jelenít meg (a probléma súlyosságától függően vörös vagy narancssárga riasztást). Ha bejelöli ezt a riasztást a webrole1 webes (futtatja a Windows Server IIS-re automatikusan telepített webalkalmazással együtt) vagy a WorkerRole1 (futtatja a Windows Server IIS-re automatikusan telepített webalkalmazással együtt) sorok, erről a javaslatról további részleteket.

Ha előírásszerűbb magyarázatot kíván megtekinteni erről a javaslatról, a **DESCRIPTION** (LEÍRÁS) oszlopban kattintson az **Update OS version** (Operációs rendszer verziójának frissítése) elemre.



![Operációs rendszer verziójának frissítése](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>App Services
Kívánja engedélyezni az App Service az előfizetésében, annak érdekében, hogy az App Service-információk megtekintése. Ez a funkció engedélyezésével kapcsolatos útmutatásért lásd: [védelme App Service-ben az Azure Security Center](security-center-app-services.md).
[!NOTE]
> Monitoring App Service-ben van, és csak a Standard szintű Security Centerben elérhető előzetes verzióban érhető el.


A **App services**, az App service Environment-környezetek listáját látja, és a Security Center kockázatértékelés alapján állapotösszegzése elvégzett.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

A listában szereplő ikon három típusa van:

![App services-környezetben](./media/security-center-virtual-machine-recommendations/ase.png) App services környezet.

![Webalkalmazás](./media/security-center-virtual-machine-recommendations/web-app.png) Webes alkalmazás.

![Függvény-alkalmazás](./media/security-center-virtual-machine-recommendations/function-app.png) Függvény-alkalmazás.

1. Válassza ki a webalkalmazást. Összegzési nézetet megnyílik a három lappal:

  - **Javaslatok**: nem sikerült, a Security Center által végzett alapján.
  - **Sikeres értékelések**: megfelelt a Security Center által végzett listája.
  - **Értékelések nem érhetők el**: értékelések, amelyek nem futhat, mert hiba történt, vagy az ajánlás listája nem releváns, az adott App Service

  A **javaslatok** minden javaslat súlyosságát és a kiválasztott webes alkalmazás a javaslatok listája.

  ![App Services javaslatok](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Válasszon ki egy javaslatot, az ajánlás leírását és a nem megfelelő erőforrások, a kifogástalan állapotú erőforrások és a nem vizsgált erőforrások listájának megtekintéséhez.

 - Alatt a **értékelések átadott** oszlop az sikeres értékelések listája.  Ezek az értékelések súlyosságát, mindig zöld.

 -  Válassza ki a átadott értékelés listájából az értékelés leírását, a nem megfelelő állapotú és kifogástalan állapotú erőforrások listájának és a nem vizsgált erőforrások listáját. Nem megfelelő állapotú erőforrások lapja, de a lista mindig üres lesz, mivel az értékelés átadott.

    ![App Service-ben szervizelés](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)


## <a name="compute-and-app-recommendations"></a>Számítási és az alkalmazás vonatkozó javaslatok
|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|App Service|20|Webes alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül|Csak HTTPS-kapcsolaton keresztül korlátozza a webes alkalmazások elérésére.|
|App Service-ben|20|Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül|Csak HTTPS-kapcsolaton keresztül korlátozza a Függvényalkalmazások elérésére.|
|App Service|5|Az App Service-ben a diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|App Service|10|Távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz|Kapcsolja ki a hibakeresési webes alkalmazásokhoz, ha már nincs rá szüksége. Távoli hibakeresés használatához meg kell nyitni a Függvényalkalmazás bejövő portokat.|
|App Service-ben|10|Távoli hibakeresést ki kell kapcsolni a Függvényalkalmazást|Ha már nincs szüksége rá a függvényalkalmazás hibakeresés kikapcsolását. Távoli hibakeresés használatához meg kell nyitni a Függvényalkalmazás bejövő portokat.|
|App Service-ben|10|Webes alkalmazás IP-korlátozások konfigurálása|Az alkalmazás-hozzáférést IP-címek listájának meghatározását. Az IP-korlátozások használatát megvédheti webalkalmazását a gyakori támadásoktól.|
|App Service-ben|10|Ne engedélyezze az összes ("*") erőforrások hozzáférhetnek az alkalmazáshoz| Ne engedélyezze a WEBSITE_LOAD_CERTIFICATES paraméter beállítása "". A paraméter beállítása "azt jelenti, hogy minden tanúsítvány töltődnek be a webes alkalmazások személyes tanúsítványtárolójába. A minimális jogosultság elvével való visszaéléshez ez is vezethet, mert nem valószínű, hogy a hely összes tanúsítvány futásidőben hozzá kell férnie.|
|App Service|20|A CORS nem teszi lehetővé minden erőforrás eléréséhez a webes alkalmazások|Együttműködhet a webalkalmazás csak a szükséges tartományok engedélyezése. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a webalkalmazáshoz való hozzáférés.|
|App Service-ben|20|A CORS nem teszi lehetővé a Függvényalkalmazás eléréséhez minden erőforrás| Lehetővé teszi a funkció alkalmazását interakcióba csak a szükséges tartományok. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a függvény-alkalmazás elérésére.|
|Számítási erőforrások (kötegelt)|1|Batch-fiókot a metrikaalapú riasztási szabályok konfigurálása|Metrikaalapú riasztási szabályok konfigurálása a Batch-fiók és a készlet törlése kész események és a készlet törlése Start események típusú metrikák engedélyezése|
|Számítási erőforrások (a service fabric)|10|Azure Active Directory használata az ügyfél-hitelesítéshez, a Service Fabricben|Hajtsa végre a Service Fabric ügyfél-hitelesítés csak az Azure Active Directory segítségével.|
|Számítási erőforrások (automation-fiók)|5| Automation-fiók titkosításának engedélyezése|Automation-fiók változó adategységek titkosítást bizalmas adatok tárolásakor.|
|Számítási erőforrások (Load balancer)|5|Load Balancer-diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (Keresés)|5|A keresési szolgáltatás diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (a service bus)|5|A Service Bus-diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (stream analytics)|5|Az Azure Stream Analytics diagnosztikai naplóinak engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (a service fabric)|5|A Service Fabric-diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (kötegelt)|5|A Batch-fiókok a diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (eseményközpontból)|5|Event Hub-diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (a logic apps)|5|A Logic Apps-diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (a service fabric)|15|A Service Fabric EncryptAndSign a ClusterProtectionLevel tulajdonsága|Service Fabric egy elsődleges fürttanúsítvány csomópontok közötti kommunikációt három szintje (nincs, bejelentkezési és EncryptAndSign) védelmet biztosít.  Biztosíthatja, hogy a csomópontok közötti üzenetek titkosítva és digitálisan aláírt védelmi szintjének beállítása. |
|Számítási erőforrások (a service bus)|1|A Service Bus-névtér RootManageSharedAccessKey kivételével minden engedélyezési szabályok törlése |Service Bus-ügyfél ne használjon egy névtér szintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A minimális jogosultságokkal rendelkező igazítása biztonság a modellben kell hozzáférési szabályzatokat hoz létre az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|Számítási erőforrások (eseményközpontból)|1|Eseményközpont-névtérrel távolítsa el RootManageSharedAccessKey kivételével minden engedélyezési szabályok |Event Hub-ügyfelek ne használjon egy névtér szintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A minimális jogosultságokkal rendelkező igazítása biztonság a modellben kell hozzáférési szabályzatokat hoz létre az entitások szintjén, az üzenetsorok és témakörök biztosíthat hozzáférést az adott entitáshoz.|
|Számítási erőforrások (eseményközpontból)|5|Az Event Hubs entitásra az engedélyezési szabályok definiálása|Azoknak az engedélyezési szabályok alacsonyabb szintű hozzáférést biztosítani az Event Hubs entitáson naplózása.|
|Gép|50|Figyelési ügynök telepítése a gépeken|A Monitoring agent engedélyezni az adatgyűjtést, a frissítések vizsgálata, alapkonfiguráció-keresés és az endpoint protection az összes olyan számítógépen telepíti.|
|Gép|50|Az Automatikus kiépítés és az előfizetésekre vonatkozó adatok gyűjtésének engedélyezése |Automatikus üzembe helyezés és a gépek előfizetéseiben engedélyezni az adatgyűjtést, a frissítések vizsgálata, alapkonfiguráció-keresés és az endpoint protection az összes olyan számítógépen, hozzáadva az előfizetésekhez az adatgyűjtés engedélyezése.|
|Gép|40|A gépek figyelőügynök-állapotproblémáinak megoldása|A védelemhez a Security Center teljes monitorozási ügynök problémák megoldásához a gépeken a hibaelhárítási útmutató utasításait követve| 
|Gép|40|A gépek Endpoint Protection-állapotproblémáinak megoldása|A Security Center teljes védelem, figyelési ügynök problémák megoldásához a gépeken a hibaelhárítási útmutató utasításait követve.|
|Gép|40|Hiányzó ellenőrzési adatok hibaelhárítása a gépeken|Hárítsa el a virtuális gépek és számítógépek hiányzó vizsgálati adatok. Hiányzó vizsgálati adatok, például hiányzó biztonsági értékelések a gépek eredményezi a frissítés vizsgálata, a baseline vizsgálata, és a hiányzó endpoint protection megoldás vizsgálata.|
|Gép|40|Rendszerfrissítések telepítése a gépeken|Hiányzó rendszerbiztonsági és kritikus frissítések, a Windows és Linux rendszerű virtuális gépek és számítógépek biztonságossá tétele
|Gép|15|Webalkalmazási tűzfal hozzáadása| Telepítse a webalkalmazási tűzfal (WAF) megoldás a webalkalmazások biztonságossá tételéhez. |
|Gép|40|Frissítse az operációs rendszer verzióját felhőszolgáltatási szerepköreihez|Frissítse az operációs rendszer verzióját felhőszolgáltatási szerepköreihez az operációsrendszer-család elérhető legújabb verziójára.|
|Gép|35|Biztonsági rések javítása a gépek biztonsági konfigurációjában|A biztonsági rések támadások elleni védelem érdekében a gépeken biztonsági beállításokkal. |
|Gép|35|A biztonsági rések biztonsági konfigurációban telepíti a tárolókat|Biztonsági rések eltávolítása a telepített Dockerrel rendelkező gépek biztonsági konfigurációjából a támadások elleni védekezés céljából.|
|Gép|25|Adaptív alkalmazásvezérlés engedélyezése|Engedélyezze az alkalmazás vezérlő a vezérlőelem, mely alkalmazások futhatnak az Azure-ban található virtuális gépek. Ez segít felvértezni virtuális gépeit a kártevők ellen. A Security Center gépi tanulási az egyes virtuális gépeken futó alkalmazások elemzése, és segít a alkalmazni, lehetővé teszi a szabályok alkalmazásában. Ez a funkció egyszerűsíti konfigurálásának folyamatán, és fenntartja az alkalmazás lehetővé teszi a szabályokat.|
|Gép|20|Endpoint Protection-megoldás telepítése a gépeken|Végpontvédelmi megoldás telepítse a virtuális gépekhez, a fenyegetések és biztonsági rések elleni védelem érdekében.|
|Gép|20|Indítsa újra a gépeket a rendszerfrissítések alkalmazásához|Indítsa újra a gépeket a rendszerfrissítések alkalmazásához és a számítógépek biztonsági rések elleni védelméhez.|
|Gép|15|Lemeztitkosítás alkalmazása a virtuális gépeken|Az Azure Disk Encryption mindkét használatával a Windows és Linux rendszerű virtuális gépek virtuálisgép-lemezek titkosítása. Az Azure Disk Encryption (ADE) használja, az iparági szabványos BitLocker funkcióját Windows és Linux operációsrendszer- és lemeztitkosítás védheti és az adatok biztonságos, és a szervezeti biztonsági és megfelelőségi igazodjunk biztosít DM-Crypt funkcióját az ügyfeleknek az Azure key vault kötelezettségvállalás. Ha a megfelelőségi és biztonsági követelményt megköveteli, hogy a teljes körű a titkosítási kulcsokat, beleértve a rövid élettartamú (helyileg csatlakoztatott ideiglenes) lemez, használja az Azure disk encryption titkosítás használata adatok titkosításához. Azt is megteheti alapértelmezés szerint felügyelt lemezek vannak titkosítása az Azure Storage Service Encryption az Azure-ban a Microsoft által felügyelt kulcsok esetén a titkosítási kulcsok használatával alapértelmezés szerint. Ha ez megfelel a megfelelőségi és biztonsági követelmények, használhatja az alapértelmezett felügyelt lemeztitkosítás az igényeknek.|
|Gép|30|Biztonsági rések felmérését lehetővé tevő megoldás telepítése a virtuális gépeken|Biztonsági rések felmérését lehetővé tevő megoldás telepítése a virtuális gépeken|
|Gép|15|Webalkalmazási tűzfal hozzáadása| Telepítse a webalkalmazási tűzfal (WAF) megoldás a webalkalmazások biztonságossá tételéhez. |
|Gép|30|Sebezhetőség-felmérési megoldás használatával a biztonsági rések|Amelynek a sebezhetőség-felmérési 3. fél megoldás üzembe helyezett virtuális gépek folyamatosan értékelni alatt álló alkalmazás és az operációs rendszer biztonsági rések ellen. Minden alkalommal, amikor kivédeni találhatók, ezek érhetők el további információ az ajánlás része.|
|Gép|30|Biztonsági rések felmérését lehetővé tevő megoldás telepítése a virtuális gépeken|Biztonsági rések felmérését lehetővé tevő megoldás telepítése a virtuális gépeken|
|Gép|1|Virtuális gépek áttelepítése az új Azure Resource Manager-erőforrások|A virtuális gépek Azure Resource Manager használatával adja meg például a biztonsági fejlesztések: erősebb hozzáférés-vezérlés (RBAC), a jobb naplózás, a Resource Manager-alapú üzembe helyezés és a cégirányítási, elérését a felügyelt identitások, a titkos kulcsokat, a key vaulthoz való hozzáférés Az Azure AD-alapú hitelesítést és címkék támogatása és -erőforráscsoportok egyszerűbb biztonság kezelése. |
|Gép|30|Sebezhetőség-felmérési megoldás használatával a biztonsági rések|Amelynek a sebezhetőség-felmérési 3. fél megoldás üzembe helyezett virtuális gépek folyamatosan értékelni alatt álló alkalmazás és az operációs rendszer biztonsági rések ellen. Minden alkalommal, amikor kivédeni találhatók, ezek érhetők el további információ az ajánlás része.|

 





## <a name="next-steps"></a>További lépések
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:


* [A virtuális gépek ismertetése az Azure Security Center-javaslatok](security-center-virtual-machine-recommendations.md)
* [Identitás és hozzáférés az Azure Security Center figyelése](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.

