---
title: A gépek és az alkalmazások az Azure Security Center védelme |} A Microsoft Docs
description: Ez a dokumentum a Security Center javaslatait, segítő tárgyalja a virtuális gépek és számítógépek és a webalkalmazások és App Service Environment-környezetek védelmét.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: a3bce8d6312dd09a7f10f8d5d2eaebd4e312d95d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200785"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>A gépek és az alkalmazások az Azure Security Center védelme
Azure Security Center elemzi az Azure-erőforrások, a nem Azure-kiszolgálók és a virtuális gépek biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, javaslatok, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán hoz létre. Javaslatok alkalmazása az Azure-erőforrástípus: virtuális gépek (VM) és a számítógépek, alkalmazások, hálózati, SQL, és az identitás- és hozzáférés.

Ez a cikk foglalkozik, javaslatok, amelyek a alkalmazni a gépek és alkalmazások.

## <a name="monitoring-security-health"></a>A biztonsági állapot figyelése
Az erőforrások biztonsági állapotát a figyelemmel kísérheti a **Security Center – áttekintés** irányítópultot. A **erőforrások** szakaszban azonosított problémák számát és a biztonsági állapot egyes erőforrástípusok biztosít.

Megtekintheti az összes hibáit kiválasztásával **javaslatok**. Javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](security-center-recommendations.md).

Szolgáltatási javaslatok teljes listáját a számítási műveletek és az alkalmazás létrehozásáról: [javaslatok](security-center-virtual-machine-protection.md#compute-and-app-recommendations).

A folytatáshoz válasszon ki **számítás és alkalmazások** alatt **erőforrások** vagy a Security Center főmenüjébe.
![Security Center irányítópultja](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>A számítási és az App Services figyelése
A **számítási & alkalmazások**területen a következő lapok találhatók:

- **Áttekintés**: Monitorozás és a Security Center által azonosított javaslatok.
- **Virtuális gépek és számítógépek**: a virtuális gépek és számítógépek listája, valamint mindegyikre az aktuális biztonsági állapota.
- **Felhőszolgáltatások**: a Security Center által figyelt összes webes és feldolgozói szerepkör listája.
- **App Services**: az App Service-környezetek és az egyes alkalmazások aktuális biztonsági állapotának listája.
- **Tárolók (előzetes verzió)** : a IaaS Linux rendszerű gépeken tárolt tárolók listája, valamint a Docker-konfigurációk biztonsági értékelése.
- **Számítási erőforrások (előzetes verzió)** : a számítási erőforrások, például a Service Fabric-fürtök és az Event hubok javaslatainak listája.

A folytatáshoz válassza a **számítási & alkalmazások** az **erőforrás biztonsági higiéniája**alatt lehetőséget.

![Compute](./media/security-center-virtual-machine-recommendations/compute.png)

Minden egyes lap esetében több szakaszt hozhat létre, és az egyes szakaszokban elérhető lehetőségek kiválasztásával további részleteket tudhat meg az adott probléma megoldásához szükséges lépésekről.

### Nem monitorozott virtuális gépek és számítógépek <a name="unmonitored-vms-and-computers"></a>
A virtuális gép vagy a számítógép nem figyelt a Security Center által, ha a gép nem fut a Microsoft Monitoring Agent bővítményt. Előfordulhat, hogy egy gépen már telepítve van egy helyi ügynök, például a OMS Direct ügynök vagy a System Center Operations Manager ügynök. Ilyen ügynökökkel rendelkező gépek eszközként van azonosítva nem figyelt, mert ezek az ügynökök teljes mértékben nem támogatottak a Security Centerben. A Security Center összes funkciójának legteljesebb kihasználása érdekében szükség van a Microsoft Monitoring Agent bővítményre.

A bővítményt a nem monitorozott virtuális gép vagy a számítógépen a már telepített helyi ügynök mellett is telepítheti. A két ügynök konfigurációja legyen megegyező, és ugyanahhoz a munkaterülethez csatlakoztassa őket. Ez lehetővé teszi, hogy a Security Center interakcióba léphessen a Microsoft Monitoring Agent bővítménnyel, és adatokat gyűjtsön. [A virtuálisgép-bővítmény engedélyezésével](../azure-monitor/learn/quick-collect-azurevm.md) foglalkozó témakörben találja a Microsoft Monitoring Agent bővítmény telepítésével kapcsolatos utasításokat.

[A Monitoring Agent állapotproblémái](security-center-troubleshooting-guide.md#mon-agent) szakaszban többet is megtudhat arról, hogy a Security Center miért nem tudja sikeresen monitorozni az automatikus üzembe helyezésre inicializált virtuális gépeket és számítógépeket.

### <a name="recommendations"></a>Javaslatok
Ebben a szakaszban rendelkezik minden virtuális gép és számítógép, webes és feldolgozói szerepkörök, az Azure App Service Web Apps és az Azure App Service-környezet, amely a Security Center figyeli vonatkozó javaslatok szerepelnek. Az első oszlop a javaslatokat sorolja fel. A második oszlop az adott javaslat által érintett erőforrások teljes számát jeleníti meg. A harmadik oszlop a probléma súlyosságát mutatja.

Minden egyes javaslatokra műveleteket hajthat végre kijelölése után. Ha például a **hiányzó rendszerfrissítések**lehetőséget választja, akkor a virtuális gépek és a hiányzó javításokat tartalmazó számítógépek száma, valamint a hiányzó frissítés súlyossága jelenik meg.

**Rendszerfrissítések alkalmazása** rendelkezik grafikus formátumban, a Windows, és egy Linux-kritikus frissítések összegzése. A második részben az alábbi információkat tartalmazó táblázat található:

- **NÉV**: A hiányzó frissítés neve.
- **Virtuális gépek & számítógépek**: A frissítésből hiányzó virtuális gépek és számítógépek száma összesen.
- **FRISSÍTÉS SÚLYOSSÁGA**: Az adott javaslat súlyosságát írja le:

    - **Kritikus**: A biztonsági rés egy értelmes erőforrással (alkalmazás, virtuális gép vagy hálózati biztonsági csoport) van, és figyelmet igényel.
    - **Fontos**: A folyamat elvégzéséhez vagy a biztonsági rések megszüntetéséhez nem kritikus vagy további lépések szükségesek.
    - **Mérsékelt**: A biztonsági rést meg kell oldani, de nincs szükség azonnali beavatkozásra. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)


- **ÁLLAPOT**: A javaslat jelenlegi állapota:

    - **Megnyitás**: A javaslat még nem lett megcímezve.
    - **Folyamatban**: A javaslatot jelenleg az adott erőforrásokra alkalmazza a rendszer, és nincs szükség beavatkozásra.
    - **Megoldott**: Az ajánlás már befejeződött. (A probléma megoldása után a bejegyzés halványan jelenik meg.)

A javaslat részleteinek megtekintéséhez kattintson a hiányzó frissítés nevére a listában.


> [!NOTE]
> Itt a biztonsági javaslatok láthatók, ugyanazok, mint a a **javaslatok** csempére. A javaslatok megoldásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>Virtuális gépek és számítógépek
A virtuális gépek és számítógépek szakasz áttekintheti az összes virtuális gép és számítógép ajánlások. Minden oszlop egy javaslatcsoportot képvisel.

![Javaslatok virtuális gépekhez és számítógépekhez](./media/security-center-virtual-machine-recommendations/vm-computers.png)

A listában szereplő ikon négy típusa van:

![Nem Azure-beli számítógép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Nem Azure-alapú számítógép.

![Az Azure Resource Manager-alapú](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Az Azure Resource Manager-alapú.

![Az Azure klasszikus virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Az Azure klasszikus virtuális gép.


![Virtuális gépek azonosítani a munkaterületről](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Azok a virtuális gépek, amelyek csak a megtekintett előfizetés részét képező munkaterület alapján azonosít a rendszer. Ebbe beletartoznak az előfizetésben szereplő munkaterületre vonatkozó más előfizetések virtuális gépei, valamint Operations Manager közvetlen ügynökkel telepített virtuális gépek és nem rendelkeznek erőforrás-AZONOSÍTÓval.

Az egyes javaslatok alatt megjelenő ikonok segít gyorsan azonosítani a virtuális gép és számítógép hagyni a figyelmet, és a javaslat típusát. A szűrőket használhatja **Erőforrás típusa** és **Súlyosság**szerint is a listában való kereséshez.

Az egyes virtuális gépek biztonsági javaslatainak részletezéséhez kattintson a virtuális gépre.
Itt láthatja a biztonsági adatok a virtuális gép vagy a számítógépen. Alul a javasolt művelet és az egyes problémák súlyossága látható.
![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Felhőszolgáltatások
A Cloud Services esetében egy javaslat jön létre, ha az operációs rendszer verziója elavult.

![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Egy forgatókönyv, amelyben egy javaslat (amely nem áll fenn az előző példában) meg kell kövesse a lépéseket az egyes javaslatokra kattintva frissítheti az operációs rendszer verzióját. Ha egy frissítés elérhetővé válik, a rendszer riasztást jelenít meg (a probléma súlyosságától függően vörös vagy narancssárga riasztást). Ha ezt a riasztást kijelöli a Webrole1 webes (a Windows Servert az IIS-ben automatikusan üzembe helyezett webalkalmazással futtatja) vagy a WorkerRole1 (a Windows Servert az IIS-ben automatikusan telepített webalkalmazással futtatja), akkor a javaslat további részleteket tartalmaz.

Ha előírásszerűbb magyarázatot kíván megtekinteni erről a javaslatról, a **DESCRIPTION** (LEÍRÁS) oszlopban kattintson az **Update OS version** (Operációs rendszer verziójának frissítése) elemre.



![Operációs rendszer verziójának frissítése](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>App Services
A App Service információk megtekintéséhez engedélyeznie kell a App Service az előfizetésében. A szolgáltatás engedélyezésével kapcsolatos útmutatásért lásd: [App Service Azure Security Centerekkel való védelemmel](security-center-app-services.md).
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

   ![Javaslatok App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Válasszon egy javaslatot a javaslat leírásának megtekintéséhez, valamint a nem megfelelő állapotú erőforrások, az egészséges erőforrások és a nem ellenőrzött erőforrások listájához.

   - Az **átadott értékelések** oszlopban az átadott értékelések listája szerepel.  Ezek az értékelések súlyosságát, mindig zöld.

   - Válassza ki a átadott értékelés listájából az értékelés leírását, a nem megfelelő állapotú és kifogástalan állapotú erőforrások listájának és a nem vizsgált erőforrások listáját. Nem megfelelő állapotú erőforrások lapja, de a lista mindig üres lesz, mivel az értékelés átadott.

     ![Szervizelés App Service](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>virtuálisgép-méretezési csoportok
Security Center automatikusan felfedi, hogy vannak-e méretezési készletek, és javasolja, hogy telepítse a Microsoft monitoring agentet ezekre a méretezési csoportokra. 

A Microsoft monitoring Agent telepítése: 

1. Válassza a **figyelési ügynök telepítése virtuálisgép-méretezési csoportra**lehetőséget. A nem figyelt méretezési csoportok listáját kapja meg.
2. Válasszon egy nem kifogástalan állapotú méretezési készletet. Kövesse az utasításokat, és telepítse a figyelési ügynököt egy meglévő feltöltött munkaterületen, vagy hozzon létre egy újat. Ha nincs beállítva, ügyeljen arra, hogy a munkaterület [árképzési szintjét](security-center-pricing.md) állítsa be.

   ![Az MMS telepítése](./media/security-center-virtual-machine-recommendations/install-mms.png)

Új méretezési csoportok beállítása a Microsoft monitoring Agent Automatikus telepítéséhez:
1. Nyissa meg Azure Policy éskattintson a definíciók elemre.
2. Keresse meg a házirendet a **Windows rendszerű virtuálisgép-méretezési csoportok log Analytics ügynökének üzembe helyezéséhez** , és kattintson rá.
3. Kattintson a **Hozzárendelés** gombra.
4. Állítsa be a **hatókör** és **log Analytics munkaterületet** , és kattintson a **hozzárendelés**elemre.

Ha az összes meglévő méretezési csoportot be szeretné állítani a Microsoft monitoring Agent telepítésére, akkor a Azure Policyban lépjen a **szervizelés** elemre, és alkalmazza a meglévő házirendet a meglévő méretezési csoportokra.


## <a name="compute-and-app-recommendations"></a>Számítási és az alkalmazás vonatkozó javaslatok
|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|App Service-ben|20|Webes alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül|Csak HTTPS-kapcsolaton keresztül korlátozza a webes alkalmazások elérésére.|
|App Service-ben|20|Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül|Csak HTTPS-kapcsolaton keresztül korlátozza a Függvényalkalmazások elérésére.|
|App Service-ben|5|A App Services diagnosztikai naplóit engedélyezni kell|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|App Service-ben|10|Távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz|Kapcsolja ki a hibakeresési webes alkalmazásokhoz, ha már nincs rá szüksége. Távoli hibakeresés használatához meg kell nyitni a Függvényalkalmazás bejövő portokat.|
|App Service-ben|10|Távoli hibakeresést ki kell kapcsolni a Függvényalkalmazást|Ha már nincs szüksége rá a függvényalkalmazás hibakeresés kikapcsolását. Távoli hibakeresés használatához meg kell nyitni a Függvényalkalmazás bejövő portokat.|
|App Service-ben|10|Ne engedélyezze az összes ("*") erőforrások hozzáférhetnek az alkalmazáshoz| Ne engedélyezze a WEBSITE_LOAD_CERTIFICATES paraméter beállítása "". A paraméter beállítása "azt jelenti, hogy minden tanúsítvány töltődnek be a webes alkalmazások személyes tanúsítványtárolójába. A minimális jogosultság elvével való visszaéléshez ez is vezethet, mert nem valószínű, hogy a hely összes tanúsítvány futásidőben hozzá kell férnie.|
|App Service-ben|20|A CORS nem teszi lehetővé minden erőforrás eléréséhez a webes alkalmazások|Együttműködhet a webalkalmazás csak a szükséges tartományok engedélyezése. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a webalkalmazáshoz való hozzáférés.|
|App Service-ben|20|A CORS nem teszi lehetővé a Függvényalkalmazás eléréséhez minden erőforrás| Lehetővé teszi a funkció alkalmazását interakcióba csak a szükséges tartományok. Közötti eredetű erőforrások megosztása (CORS) kell nem teszi lehetővé minden tartománynak a függvény-alkalmazás elérésére.|
|Számítási erőforrások (kötegelt)|1|A metrika riasztási szabályait a Batch-fiókokon kell konfigurálni|Metrikaalapú riasztási szabályok konfigurálása a Batch-fiók és a készlet törlése kész események és a készlet törlése Start események típusú metrikák engedélyezése|
|Számítási erőforrások (a service fabric)|10|Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez|Hajtsa végre a Service Fabric ügyfél-hitelesítés csak az Azure Active Directory segítségével.|
|Számítási erőforrások (automation-fiók)|5|Az Automation-fiók változóit titkosítani kell|Automation-fiók változó adategységek titkosítást bizalmas adatok tárolásakor.|
|Számítási erőforrások (Keresés)|5|Diagnosztikai naplók engedélyezésének naplózása a keresési szolgáltatásokhoz|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (a service bus)|5|A Service Bus diagnosztikai naplóit engedélyezni kell|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (stream analytics)|5|A Azure Stream Analytics diagnosztikai naplóit engedélyezni kell|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (kötegelt)|5|A Batch-fiókok a diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (eseményközpontból)|5|Az Event hub diagnosztikai naplóit engedélyezni kell|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (a logic apps)|5|A Logic Apps-diagnosztikai naplók engedélyezése|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|Számítási erőforrások (a service fabric)|15|A Service Fabric EncryptAndSign a ClusterProtectionLevel tulajdonsága|Service Fabric egy elsődleges fürttanúsítvány csomópontok közötti kommunikációt három szintje (nincs, bejelentkezési és EncryptAndSign) védelmet biztosít.  Biztosíthatja, hogy a csomópontok közötti üzenetek titkosítva és digitálisan aláírt védelmi szintjének beállítása. |
|Számítási erőforrások (a service bus)|1|A Service Bus-névtér RootManageSharedAccessKey kivételével minden engedélyezési szabályok törlése |Service Bus-ügyfél ne használjon egy névtér szintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A legalacsonyabb jogosultsági szintű biztonsági modellel való összehangoláshoz hozzáférési házirendeket kell létrehoznia az entitások szintjén a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz lehessen hozzáférést biztosítani.|
|Számítási erőforrások (eseményközpontból)|1|A RootManageSharedAccessKey kivételével az összes engedélyezési szabályt el kell távolítani az Event hub-névtérből|Event Hub-ügyfelek ne használjon egy névtér szintű hozzáférési szabályzatot, amely valamennyi üzenetsorok és témakörök a névtérben hozzáférést biztosít. A legalacsonyabb jogosultsági szintű biztonsági modellel való összehangoláshoz hozzáférési házirendeket kell létrehoznia az entitások szintjén a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz lehessen hozzáférést biztosítani.|
|Számítási erőforrások (eseményközpontból)|5|Az Event hub-entitás engedélyezési szabályait definiálni kell|Azoknak az engedélyezési szabályok alacsonyabb szintű hozzáférést biztosítani az Event Hubs entitáson naplózása.|
|Machine|50|Figyelési ügynök telepítése a gépeken|A Monitoring agent engedélyezni az adatgyűjtést, a frissítések vizsgálata, alapkonfiguráció-keresés és az endpoint protection az összes olyan számítógépen telepíti.|
|Gép|50|Az Automatikus kiépítés és az előfizetésekre vonatkozó adatok gyűjtésének engedélyezése |Automatikus üzembe helyezés és a gépek előfizetéseiben engedélyezni az adatgyűjtést, a frissítések vizsgálata, alapkonfiguráció-keresés és az endpoint protection az összes olyan számítógépen, hozzáadva az előfizetésekhez az adatgyűjtés engedélyezése.|
|Gép|40|A gépek figyelőügynök-állapotproblémáinak megoldása|A védelemhez a Security Center teljes monitorozási ügynök problémák megoldásához a gépeken a hibaelhárítási útmutató utasításait követve| 
|Gép|40|A gépek Endpoint Protection-állapotproblémáinak megoldása|A Security Center teljes védelem, figyelési ügynök problémák megoldásához a gépeken a hibaelhárítási útmutató utasításait követve.|
|Gép|40|Hiányzó ellenőrzési adatok hibaelhárítása a gépeken|Hárítsa el a virtuális gépek és számítógépek hiányzó vizsgálati adatok. Hiányzó vizsgálati adatok, például hiányzó biztonsági értékelések a gépek eredményezi a frissítés vizsgálata, a baseline vizsgálata, és a hiányzó endpoint protection megoldás vizsgálata.|
|Gép|40|A számítógépekre telepíteni kell a rendszerfrissítéseket|Hiányzó rendszerbiztonsági és kritikus frissítések, a Windows és Linux rendszerű virtuális gépek és számítógépek biztonságossá tétele
|Machine|15|Webalkalmazási tűzfal hozzáadása| Telepítse a webalkalmazási tűzfal (WAF) megoldás a webalkalmazások biztonságossá tételéhez. |
|Machine|40|Frissítse az operációs rendszer verzióját felhőszolgáltatási szerepköreihez|Frissítse az operációs rendszer verzióját felhőszolgáltatási szerepköreihez az operációsrendszer-család elérhető legújabb verziójára.|
|Gép|35|A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell|A biztonsági rések támadások elleni védelem érdekében a gépeken biztonsági beállításokkal.|
|Gép|35|A biztonsági rések biztonsági konfigurációban telepíti a tárolókat|Biztonsági rések eltávolítása a telepített Dockerrel rendelkező gépek biztonsági konfigurációjából a támadások elleni védekezés céljából.|
|Gép|25|Adaptív alkalmazásvezérlés engedélyezése|Engedélyezze az alkalmazás vezérlő a vezérlőelem, mely alkalmazások futhatnak az Azure-ban található virtuális gépek. Ez segít felvértezni virtuális gépeit a kártevők ellen. A Security Center gépi tanulási az egyes virtuális gépeken futó alkalmazások elemzése, és segít a alkalmazni, lehetővé teszi a szabályok alkalmazásában. Ez a funkció egyszerűsíti konfigurálásának folyamatán, és fenntartja az alkalmazás lehetővé teszi a szabályokat.|
|Gép|20|Endpoint Protection-megoldás telepítése a gépeken|Végpontvédelmi megoldás telepítse a virtuális gépekhez, a fenyegetések és biztonsági rések elleni védelem érdekében.|
|Gép|20|Indítsa újra a gépeket a rendszerfrissítések alkalmazásához|Indítsa újra a gépeket a rendszerfrissítések alkalmazásához és a számítógépek biztonsági rések elleni védelméhez.|
|Machine|15|A lemezes titkosítást a virtuális gépeken kell alkalmazni|Az Azure Disk Encryption mindkét használatával a Windows és Linux rendszerű virtuális gépek virtuálisgép-lemezek titkosítása. Az Azure Disk Encryption (ADE) használja, az iparági szabványos BitLocker funkcióját Windows és Linux operációsrendszer- és lemeztitkosítás védheti és az adatok biztonságos, és a szervezeti biztonsági és megfelelőségi igazodjunk biztosít DM-Crypt funkcióját az ügyfeleknek az Azure key vault kötelezettségvállalás. Ha a megfelelőségi és biztonsági követelményt megköveteli, hogy a teljes körű a titkosítási kulcsokat, beleértve a rövid élettartamú (helyileg csatlakoztatott ideiglenes) lemez, használja az Azure disk encryption titkosítás használata adatok titkosításához. Azt is megteheti alapértelmezés szerint felügyelt lemezek vannak titkosítása az Azure Storage Service Encryption az Azure-ban a Microsoft által felügyelt kulcsok esetén a titkosítási kulcsok használatával alapértelmezés szerint. Ha ez megfelel a megfelelőségi és biztonsági követelmények, használhatja az alapértelmezett felügyelt lemeztitkosítás az igényeknek.|
|Machine|30|Biztonsági rések felmérését lehetővé tevő megoldás telepítése a virtuális gépeken|Biztonsági rések felmérését lehetővé tevő megoldás telepítése a virtuális gépeken|
|Gép|15|Webalkalmazási tűzfal hozzáadása| Telepítse a webalkalmazási tűzfal (WAF) megoldás a webalkalmazások biztonságossá tételéhez. |
|Gép|30|A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni|Amelynek a sebezhetőség-felmérési 3. fél megoldás üzembe helyezett virtuális gépek folyamatosan értékelni alatt álló alkalmazás és az operációs rendszer biztonsági rések ellen. Minden alkalommal, amikor kivédeni találhatók, ezek érhetők el további információ az ajánlás része.|
|Machine|30|Biztonsági rések felmérését lehetővé tevő megoldás telepítése a virtuális gépeken|Biztonsági rések felmérését lehetővé tevő megoldás telepítése a virtuális gépeken|
|Machine|1|A virtuális gépeket át kell telepíteni az új AzureRM-erőforrásokra|A virtuális gépek Azure Resource Manager használatával olyan biztonsági fejlesztéseket biztosíthat, mint például a következők: erősebb hozzáférés-vezérlés (RBAC), jobb auditálás, erőforrás-kezelő alapú üzembe helyezés és irányítás, felügyelt identitásokhoz való hozzáférés, a Key Vault for Secrets elérése Az Azure AD-alapú hitelesítés és a címkék és erőforráscsoportok támogatása a biztonsági felügyelet megkönnyítésére. |
|Machine|30|A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni|Amelynek a sebezhetőség-felmérési 3. fél megoldás üzembe helyezett virtuális gépek folyamatosan értékelni alatt álló alkalmazás és az operációs rendszer biztonsági rések ellen. Minden alkalommal, amikor kivédeni találhatók, ezek érhetők el további információ az ajánlás része.|
|Virtuális gép méretkészlete |4|A Virtual Machine Scale Sets diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig is megtarthatja őket. Ez lehetővé teszi, hogy vizsgálati célokra újra létrehozza a tevékenységek nyomvonalait. Ez akkor hasznos, ha biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Virtuális gép méretkészlete|35|A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell|Javítsa a biztonsági beállításokat a virtuálisgép-méretezési csoportokban a támadások elleni védelem érdekében. |
|Virtuális gép méretkészlete|5|Az Endpoint Protection állapotával kapcsolatos hibák elhárítása virtuálisgép-méretezési csoportokban|Javítsa ki az Endpoint Protection-állapottal kapcsolatos hibákat a virtuálisgép-méretezési csoportokban a fenyegetések és a biztonsági rések elleni védelem érdekében. |
|Virtuális gép méretkészlete|10|Az Endpoint Protection szolgáltatást virtuális gépekre kell telepíteni|Telepítsen egy Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra a fenyegetések és a biztonsági rések elleni védelem érdekében. |
|Virtuális gép méretkészlete|40|A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell|Telepítse a hiányzó rendszerbiztonsági és kritikus frissítéseket a Windows és a Linux rendszerű virtuálisgép-méretezési csoportok biztonságossá tételéhez. |
 





## <a name="next-steps"></a>További lépések
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:


* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Identitás és hozzáférés az Azure Security Center figyelése](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.

