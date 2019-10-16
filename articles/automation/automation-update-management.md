---
title: Update Management megoldás az Azure-ban
description: Ennek a cikknek a segítségével megismerheti, hogyan kezelheti a Windows-és Linux-számítógépek frissítéseit az Azure Update Management megoldás használatával.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1751e8d67f59285d011df33a2d4d1d6d8abcec6a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376050"
---
# <a name="update-management-solution-in-azure"></a>Update Management megoldás az Azure-ban

A Azure Automation Update Management megoldásával kezelheti az operációs rendszer frissítéseit az Azure-ban, a helyszíni környezetekben vagy más felhőalapú szolgáltatókban lévő Windows-és Linux-számítógépeken. Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és felügyelhető a kiszolgálók szükséges frissítéseinek telepítése is.

A virtuális gépeket közvetlenül a Azure Automation-fiókjából engedélyezheti Update Management. Ha szeretné megtudni, hogyan engedélyezheti a virtuális gépek Update Managementét az Automation-fiókjából, tekintse meg a [több virtuális gép frissítéseinek kezelése](manage-update-multi.md)című témakört. A virtuális gép Update Management is engedélyezheti a Azure Portal virtuális gép lapján. Ez a forgatókönyv [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) és [Windows rendszerű](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) virtuális gépek esetén érhető el.

> [!NOTE]
> A Update Management megoldáshoz egy Log Analytics-munkaterületet kell összekapcsolnia az Automation-fiókkal. A támogatott régiók végleges listáját az [Azure Workspace-hozzárendelések](./how-to/region-mappings.md)című témakörben tekintheti meg. A régió-hozzárendelések nem befolyásolják a virtuális gépek az Automation-fióktól eltérő régióban való felügyeletének képességét.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Megoldás áttekintése

A Update Management által felügyelt számítógépek a következő konfigurációkat használják a felmérési és frissítési központi telepítések végrehajtásához:

* Microsoft monitoring Agent (MMA) Windows vagy Linux rendszerhez
* PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
* Automation hibrid runbook-feldolgozó
* Microsoft Update vagy Windows Server Update Services (WSUS) Windows rendszerű számítógépekhez

A következő ábra a viselkedés és az adatfolyam fogalmi nézetét mutatja be, hogy a megoldás hogyan vizsgálja és alkalmazza a biztonsági frissítéseket a munkaterület összes csatlakoztatott Windows Server-és Linux-számítógépén:

![Update Management folyamat](./media/automation-update-management/update-mgmt-updateworkflow.png)

A Update Management a különböző előfizetésekben lévő gépek natív módon történő bevezetésére használható ugyanazon a bérlőn.

A csomag felszabadítása után 2-3 órát vesz igénybe, hogy a javítás megjelenjen a Linux rendszerű gépek értékeléséhez. A Windows rendszerű gépek esetében 12-15 órát vesz igénybe, hogy a javítás megjelenjen az értékelés után.

Miután a számítógép befejezte a frissítések megfelelőségi vizsgálatát, az ügynök tömegesen továbbítja az adatokat Azure Monitor naplókhoz. A Windows rendszerű számítógépeken alapértelmezés szerint 12 óránként futnak a megfelelőségi vizsgálat.

A vizsgálati ütemterven kívül a frissítési megfelelőség vizsgálata az MMA újraindítása után 15 percen belül megkezdődik, a frissítés telepítése előtt és a frissítés telepítése után.

Linux rendszerű számítógépek esetében a megfelelőségi vizsgálat alapértelmezés szerint óránként történik. Az MMA-ügynök újraindításakor a megfelelőségi vizsgálat 15 percen belül megkezdődik.

A megoldás azt mutatja be, hogy a számítógép naprakészen van-e attól függően, hogy milyen forrásra van konfigurálva a szinkronizáláshoz. Ha a Windows rendszerű számítógép úgy van konfigurálva, hogy a WSUS szolgáltatásnak jelentsen, attól függően, hogy mikor szinkronizálta az Microsoft Update, az eredmények eltérhetnek a Microsoft frissítései által megjelenített adatoktól. Ez a viselkedés ugyanaz, mint a Linux rendszerű számítógépek esetében, amelyek úgy vannak konfigurálva, hogy nyilvános tárház helyett helyi tárházba jelentsenek.

> [!NOTE]
> Ahhoz, hogy a szolgáltatás megfelelően jelentsen, Update Management szükség van bizonyos URL-címekre és portokra. Ha többet szeretne megtudni ezekről a követelményekről, tekintse meg [a hibrid feldolgozók hálózati tervezése](automation-hybrid-runbook-worker.md#network-planning)című témakört.

A szoftverfrissítések központi telepítéséhez vagy telepítéséhez létrehozhat egy ütemezett üzembe helyezést a frissítést igénylő számítógépeken. A *választhatóként* besorolt frissítések nem szerepelnek a Windows rendszerű számítógépek központi telepítési hatókörében. A központi telepítési hatókörben csak a szükséges frissítések szerepelnek.

Az ütemezett telepítés meghatározza, hogy mely célszámítógépek kapják meg a megfelelő frissítéseket, vagy a számítógépek explicit módon történő megadásával, vagy egy adott számítógép, illetve egy [Azure-lekérdezés](automation-update-management-query-logs.md) naplójának keresésén alapuló [számítógépcsoport](../azure-monitor/platform/computer-groups.md) kiválasztásával. Ez dinamikusan kiválasztja az Azure-beli virtuális gépeket a megadott feltételek alapján. Ezek a csoportok eltérnek a [hatókör-konfigurációtól](../azure-monitor/insights/solution-targeting.md), amely csak annak meghatározására szolgál, hogy mely gépek kapják meg a megoldást használó felügyeleti csomagokat.

Megadhat egy ütemtervet is a jóváhagyáshoz, és beállíthatja azt az időtartamot, ameddig a frissítések telepíthetők. Ezt az időtartamot karbantartási időszaknak nevezzük. Ha újraindításra van szükség, a karbantartási időszak húsz perce van fenntartva újraindítás esetén, és kiválasztotta a megfelelő újraindítási beállítást. Ha a javítás a vártnál hosszabb időt vesz igénybe, és a karbantartási időszakban kevesebb, mint húsz perc van, a rendszer újraindítást hajt végre.

A telepítést az Azure Automation runbookjai végzik. Ezeket a runbookok nem lehet megtekinteni, és a runbookok nem igényel konfigurálást. Egy frissítés központi telepítésének létrehozásakor a frissítés központi telepítése egy olyan ütemezést hoz létre, amely egy fő frissítési runbook indít el a befoglalt számítógépeken a megadott időpontban. A fő runbook elindít egy gyermek runbook az egyes ügynökökön a szükséges frissítések telepítéséhez.

A frissítés központi telepítésében megadott dátum és idő szerint a célszámítógépek párhuzamosan hajtják végre a telepítést. A telepítés előtt a rendszer ellenőrzi, hogy a frissítések továbbra is szükségesek-e. WSUS-ügyfélszámítógépek esetén, ha a frissítések nem hagyták jóvá a WSUS-ben, a frissítés telepítése sikertelen lesz.

Ha több Log Analytics munkaterületen (több-önkiszolgáló) lévő Update Managementhoz regisztrált gép van, nem támogatott.

## <a name="clients"></a>Ügyfelek

### <a name="supported-client-types"></a>Támogatott ügyfelek típusai

Az alábbi táblázat a frissítési felmérések támogatott operációs rendszereinek listáját tartalmazza. A javításhoz hibrid Runbook-feldolgozóra van szükség. A hibrid Runbook-feldolgozói követelményekkel kapcsolatos információkért tekintse meg a [Windows HRW](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) és a [Linux HRW](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)telepítési útmutatóit.

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/standard)<br><br>Windows Server 2012 R2 (Datacenter/standard)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM és SP1 standard)||
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz. A besoroláson alapuló javításhoz a "yum" utasítás szükséges ahhoz, hogy a CentOS ne legyen a dobozban. A CentOS besoroláson alapuló javításával kapcsolatos további információkért lásd: [frissítési besorolások Linux](#linux-2) rendszeren          |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 14,04 LTS, 16,04 LTS és 18,04 (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

> [!NOTE]
> Az Azure-beli virtuálisgép-méretezési csoportok a Update Management használatával kezelhetők. A Update Management a példányokon működik, nem az alapképet. A frissítéseket növekményes módon kell ütemeznie, hogy az összes virtuálisgép-példány egyszerre ne legyen frissítve.
> VMSS-csomópontok hozzáadásához kövesse a [nem Azure-beli gép](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)bevezetésének lépéseit.

### <a name="unsupported-client-types"></a>Nem támogatott ügyfelek típusai

A következő táblázat a nem támogatott operációs rendszereket sorolja fel:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows-ügyfél     | Az ügyféloldali operációs rendszerek (például a Windows 7 és a Windows 10) nem támogatottak.        |
|Windows Server 2016 Nano Server     | Nem támogatott.       |
|Azure Kubernetes szolgáltatási csomópontok | Nem támogatott. Az [Azure Kubernetes Service (ak) által használt Linux-csomópontok biztonsági és kernel-frissítéseinek alkalmazása](../aks/node-updates-kured.md) című részletes javítási folyamat|

### <a name="client-requirements"></a>Ügyfélre vonatkozó követelmények

Az alábbi információk az operációsrendszer-specifikus ügyfelekre vonatkozó követelményeket ismertetik.  További útmutatásért tekintse át a [hálózati tervezést](#ports) is.

#### <a name="windows"></a>Windows

A Windows-ügynököket úgy kell konfigurálni, hogy a WSUS-kiszolgálóval kommunikáljanak, vagy hozzá kell férniük Microsoft Updatehoz.

A Update Management a System Center Configuration Manager használatával végezheti el. Az integrációs forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [a System Center Configuration Manager integrálása Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration)használatával. A [Windows-ügynököt](../azure-monitor/platform/agent-windows.md) kötelező megadni. Az ügynök automatikusan települ, ha Azure-beli virtuális gépet helyez üzembe.

Az Azure Marketplace-ről üzembe helyezett Windows-beli virtuális gépek alapértelmezés szerint a Windows Update szolgáltatásból származó automatikus frissítések fogadására vannak beállítva. Ez a viselkedés nem változik, ha hozzáadja ezt a megoldást, vagy Windows rendszerű virtuális gépeket ad hozzá a munkaterülethez. Ha nem kezeli aktívan a frissítéseket a megoldás használatával, a rendszer az alapértelmezett működést (a frissítések automatikus érvénybe lépését) alkalmazza.

> [!NOTE]
> Lehetséges, hogy a felhasználó módosíthatja Csoportházirend, hogy a számítógép újraindítása csak a felhasználó által, nem pedig a rendszer által végezhető el. A felügyelt gépek elakadnak, ha Update Management nem rendelkezik jogosultsággal a gép újraindításához a felhasználó manuális beavatkozása nélkül.
>
> További információ: [csoportházirend beállítások konfigurálása az automatikus frissítésekhez](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Linux esetén a gépnek hozzá kell férnie egy frissítési tárházhoz. A frissítési tárház lehet magán vagy nyilvános. A Update Managementhoz való interakcióhoz TLS 1,1 vagy TLS 1,2 szükséges. Ez a megoldás nem támogatja az olyan Log Analytics Linux-ügynököt, amely több Log Analytics munkaterületnek való jelentésre van konfigurálva.  A gépnek a Python 2. x verziójának is telepítve kell lennie.

További információ a Linux rendszerhez készült Log Analytics-ügynök telepítéséről és a legújabb verzió letöltéséről: [log Analytics Agent for Linux](https://github.com/microsoft/oms-agent-for-linux). További információ a Windows Log Analytics-ügynök telepítéséről: [Microsoft monitoring Agent for Windows](../log-analytics/log-analytics-windows-agent.md).

Az Azure piactéren elérhető, igény szerinti Red Hat Enterprise Linux (RHEL) lemezképből létrehozott virtuális gépek regisztrálva vannak az Azure-ban üzembe helyezett [Red Hat frissítési infrastruktúrához (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) . A terjesztés támogatott módszereinek követésével minden más Linux-disztribúciót frissíteni kell az elosztás online fájljából.

## <a name="permissions"></a>Engedélyek

A frissítési központi telepítések létrehozásához és kezeléséhez konkrét engedélyekre van szükség. További információ ezekről az engedélyekről: [szerepköralapú hozzáférés – Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Megoldás-összetevők

A megoldás a következő erőforrásokból áll. A rendszer hozzáadja az erőforrásokat az Automation-fiókhoz. Közvetlenül csatlakoztatott ügynökök vagy Operations Manager csatlakoztatott felügyeleti csoportban vannak.

### <a name="hybrid-worker-groups"></a>Hibridfeldolgozó-csoportok

A megoldás engedélyezése után a Log Analytics munkaterülethez közvetlenül csatlakozó Windows-számítógépek automatikusan hibrid Runbook-feldolgozóként vannak konfigurálva, hogy támogassák a megoldásban foglalt runbookok.

A megoldás által felügyelt Windows-számítógépek a **hibrid munkavégző csoportok** ablaktáblán jelennek meg az Automation-fiókhoz tartozó **rendszer hibrid feldolgozói csoportként** . A megoldások az elnevezési konvenció *hostname FQDN_GUID*használják. Ezek a csoportok nem állíthatók be a runbookok a fiókjában. Ha próbálkozik, sikertelenek lesznek. Ezek a csoportok kizárólag a felügyeleti megoldást támogatják.

A Windows rendszerű számítógépeket felveheti az Automation-fiókjában lévő hibrid Runbook-feldolgozó csoportba az Automation-runbookok támogatásához, ha ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook Worker Group tagsághoz. Ez a funkció a hibrid Runbook-feldolgozó verziójának 7.2.12024.0 lett hozzáadva.

### <a name="management-packs"></a>Felügyeleti csomagok

Ha a System Center Operations Manager felügyeleti csoport egy Log Analytics munkaterülethez csatlakozik, a következő felügyeleti csomagok lesznek telepítve a Operations Managerban. Ezeket a felügyeleti csomagokat a megoldás hozzáadása után közvetlenül csatlakoztatott Windows rendszerű számítógépekre is telepíti a rendszer. Ezeket a felügyeleti csomagokat nem kell konfigurálnia vagy kezelnie.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

> [!NOTE]
> Ha olyan Operations Manager 1807 vagy 2019 felügyeleti csoporttal rendelkezik, amely a felügyeleti csoport szintjén konfigurált ügynökökkel rendelkezik a munkaterülethez való társításhoz, akkor az aktuális megkerülő Áthidaló megoldás a **IsAutoRegistrationEnabled** felülbírálása a **true** értékre a következőre: a **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** szabály.

A megoldás-felügyeleti csomagok frissítésével kapcsolatos további információkért lásd: [Operations Manager Összekötése Azure monitor naplókhoz](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Az Operations Manager-ügynököt tartalmazó rendszerek esetében a Update Management teljes körű felügyeletéhez az ügynököt frissíteni kell a Microsoft monitoring Agentre. Az ügynök frissítésével kapcsolatos további információkért lásd: [Operations Manager-ügynök frissítése](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). A Operations Managert használó környezetek esetén szükséges, hogy System Center Operations Manager 2012 R2-es vagy újabb verzióját futtassa.

## <a name="data-collection"></a>Adatgyűjtés

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A megoldás adatokat gyűjt a Windows-ügynököktől a rendszerfrissítésekről, majd elindítja a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A megoldás adatokat gyűjt a Linux-ügynököktől a rendszerfrissítésekről, majd kezdeményezi a szükséges frissítések telepítését a támogatott disztribúciók esetében. |
| Az Operations Manager felügyeleti csoportja |Igen |A megoldás információt szerez be a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről.<br/>Nincs szükség közvetlen kapcsolódásra a Operations Manager ügynöktől a Azure Monitor naplókhoz. Az adatok továbbítása a felügyeleti csoportból a Log Analytics munkaterületre történik. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

Minden felügyelt Windows-számítógép naponta kétszer végez vizsgálatot. A rendszer 15 percenként lekérdezi a Windows API-t, hogy lekérdezze az utolsó frissítés idejét annak megállapításához, hogy megváltozott-e az állapot. Ha az állapot módosult, a rendszer megfelelőségi vizsgálatot kezdeményez.

Minden egyes felügyelt Linux rendszerű számítógéphez óránként vizsgálatot kell végrehajtani.

A felügyelt számítógépekről származó frissített adatok megjelenítéséhez az irányítópult 30 perc és 6 óra között is eltarthat.

Az átlagos Azure Monitor a Update Management-t használó gépek adatfelhasználását körülbelül 25MB havonta. Ez az érték csak közelítés, és a környezettől függően változhat. Javasoljuk, hogy figyelje a környezetét, és tekintse meg a pontos használatot.

## <a name="ports"></a>Hálózati tervezés

A következő címek megadása kifejezetten a Update Management. A címekkel folytatott kommunikáció az 443-as porton keresztül történik.

|Nyilvános Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

Windows rendszerű gépek esetén a Windows Update által igényelt végpontokra is engedélyeznie kell a forgalmat.  A szükséges végpontok frissített listáját a [http/proxy szolgáltatással kapcsolatos problémák](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)között találja. Ha helyi [Windows Update-kiszolgálóval](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)rendelkezik, engedélyeznie kell a forgalmat a [WSUS-kulcsban](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)megadott kiszolgálóra is.

A Red Hat Linux rendszerű gépek esetében tekintse meg a szükséges végpontok számára a [RHUI Content Delivery Servers IP](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) -címei című témakört. Más Linux-disztribúciók esetében tekintse meg a szolgáltatói dokumentációt.

További információ a hibrid Runbook Worker által igényelt portokról: [hibrid feldolgozói szerepkör portjai](automation-hybrid-runbook-worker.md#hybrid-worker-role).

A kivételek meghatározásakor a felsorolt címek használatát javasoljuk. IP-címek esetén letöltheti az [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653). A fájl hetente frissül, és tükrözi a jelenleg üzembe helyezett tartományokat és az IP-címtartományok közelgő változásait.

Az Internet-hozzáférés [nélküli számítógépek összekapcsolására](../azure-monitor/platform/gateway.md) vonatkozó utasításokat követve konfigurálja azokat a gépeket, amelyek nem rendelkeznek interneteléréssel.

## <a name="view-update-assessments"></a>Frissítési felmérések megtekintése

Az Automation-fiókjában válassza a **Update Management** lehetőséget a gépek állapotának megtekintéséhez.

Ez a nézet a gépekre, a hiányzó frissítésekre, a frissítések telepítésére és az ütemezett frissítések telepítésére vonatkozó információkat tartalmaz. A **megfelelőség oszlopban**láthatja, hogy mikor történt a gép utolsó értékelése. A **frissítési ügynök készültségi** oszlopában láthatja, hogy a frissítési ügynök állapota. Ha probléma merül fel, a hivatkozásra kattintva keresse meg a hibaelhárítási dokumentációt, amelyből megtudhatja, hogy milyen lépéseket kell tennie a probléma megoldásához.

Ha olyan naplót szeretne futtatni, amely adatokat ad vissza a gépre, a frissítésre vagy a központi telepítésre vonatkozóan, válassza ki az elemet a listában. Megnyílik a **napló keresése** ablaktábla a kiválasztott elem lekérdezésével:

![Update Management alapértelmezett nézet](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Hiányzó frissítések megtekintése

Válassza a **hiányzó frissítések** lehetőséget a gépekből hiányzó frissítések listájának megtekintéséhez. Minden frissítés szerepel a listáján, és kiválasztható. A frissítéshez szükséges gépek számával, az operációs rendszerrel és a további információkra mutató hivatkozással kapcsolatos információk láthatók. A **naplók keresése** panel további részleteket jelenít meg a frissítésekről.

![Hiányzó frissítések](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Frissítési besorolások

Az alábbi táblázat a Update Management frissítési besorolásait sorolja fel, az egyes besorolások definíciója szerint.

### <a name="windows"></a>Windows

|Osztályozás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Egy adott problémára vonatkozó frissítés, amely kritikus, nem biztonsággal kapcsolatos hibára vonatkozik.        |
|Biztonsági frissítések     | Egy termékre vonatkozó, biztonsággal kapcsolatos probléma frissítése.        |
|Kumulatív frissítések     | Az egyszerű üzembe helyezéshez együtt csomagolt gyorsjavítások összesített készlete.        |
|Funkciócsomagok     | A termék kiadásán kívül terjesztett új termék-szolgáltatások.        |
|Szervizcsomagok     | Egy alkalmazásra alkalmazott gyorsjavítások kumulatív készlete.        |
|Definíciófrissítések     | Vírus-vagy egyéb definíciós fájlok frissítése.        |
|Eszközök     | Olyan segédprogram vagy szolgáltatás, amely egy vagy több feladat elvégzését segíti elő.        |
|Frissítések     | Egy aktuálisan telepített alkalmazás vagy fájl frissítése.        |

### <a name="linux-2"></a>Linux

|Osztályozás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Adott probléma vagy termékspecifikus, biztonsággal kapcsolatos probléma frissítései.         |
|Egyéb frissítések     | Minden egyéb olyan frissítés, amely nem kritikus jellegű, vagy nem biztonsági frissítés.        |

A Linux rendszerben a Update Management különbséget tehet a Felhőbeli kritikus és biztonsági frissítések között, miközben a felhőben az adatgazdagítás miatt kiértékeli az értékelést. A javításhoz a Update Management a gépen elérhető besorolási értékekre támaszkodik. A többi disztribúciótól eltérően a CentOS nem rendelkezik ezekkel az információkkal a dobozból. Ha a CentOS-gépek úgy vannak konfigurálva, hogy a következő parancs biztonsági értékeit adja vissza, Update Management a besorolások alapján lesz képes a javításra.

```bash
sudo yum -q --security check-update
```

Jelenleg nincs olyan metódus, amely támogatja a natív besorolást – az adatelérhetőség a CentOS-ben. Jelenleg csak a lehető legjobb támogatást nyújtja azoknak az ügyfeleknek, akik saját maguk engedélyezték ezt.

## <a name="integrate-with-system-center-configuration-manager"></a>Integrálás a System Center Configuration Managerrel

A számítógépek, kiszolgálók és mobileszközök kezeléséhez System Center Configuration Manager befektetett ügyfelek is a Configuration Manager erősségét és érettségét használják a szoftverfrissítések kezeléséhez. Configuration Manager a szoftverfrissítés-kezelési (SUM) ciklus részét képezi.

Ha szeretné megtudni, hogyan integrálható a felügyeleti megoldás System Center Configuration Manager használatával, tekintse meg a [System Center Configuration Manager integrálása a Update Management](oms-solution-updatemgmt-sccmintegration.md)használatával című témakört.

### <a name="third-party-patches-on-windows"></a>Harmadik féltől származó javítások Windows rendszeren

A Update Management a helyileg konfigurált frissítési tárházra támaszkodik a támogatott Windows-rendszerek javításához. Ez vagy a WSUS vagy a Windows Update. Az olyan eszközök, mint a [System Center Updates Publisher](/sccm/sum/tools/updates-publisher) (a frissítések közzétevője) lehetővé teszik egyéni frissítések közzétételét a WSUS szolgáltatásban. Ez a forgatókönyv lehetővé teszi, hogy Update Management a harmadik féltől származó szoftverrel rendelkező frissítési adattárként System Center Configuration Manager használó gépeket. Az Updates Publisher konfigurálásával kapcsolatos további információkért lásd: a [frissítések közzétevő telepítése](/sccm/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Linux rendszerű gépek javítása

A következő szakasz ismerteti a Linux-javítások lehetséges problémáit.

### <a name="unexpected-os-level-upgrades"></a>Váratlan operációsrendszer-szintű frissítések

Egyes Linux-változatoknál, például Red Hat Enterprise Linux, az operációsrendszer-szintű frissítések csomagok használatával is előfordulhatnak. Ennek eredményeként előfordulhat, hogy Update Management futtatja az operációs rendszer verziószámát. Mivel a Update Management ugyanazokat a módszereket használja, mint például a Linux rendszerű számítógépeken a rendszergazda által helyileg használt csomagok frissítése, ez a viselkedés szándékos.

Ha el szeretné kerülni az operációs rendszer verziójának Update Management futtatáson keresztüli frissítését, használja a **kizárás** funkciót.

Red Hat Enterprise Linux a kizárni kívánt csomag neve a RedHat-Release-Server. x86_64.

![A Linux rendszerből kizárandó csomagok](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>A kritikus/biztonsági javítások nincsenek alkalmazva

Ha Linux rendszerű gépre telepít frissítéseket, válassza a frissítési besorolások lehetőséget. Ezzel szűri azokat a frissítéseket, amelyeket a rendszer a megadott feltételeknek megfelelő gépre alkalmaz. Ezt a szűrőt a rendszer helyileg alkalmazza a számítógépen a frissítés telepítésekor.

Mivel a Update Management a felhőben frissíti a frissítés bővítését, bizonyos frissítések a Update Managementban is megadhatók, hogy biztonsági hatással legyenek, még akkor is, ha a helyi gép nem rendelkezik ezekkel az információkkal. Ennek eredményeképpen, ha a kritikus frissítéseket egy Linux rendszerű gépre alkalmazza, előfordulhat, hogy olyan frissítések vannak, amelyek nincsenek megjelölve biztonsági hatással a gépen, és a frissítések nincsenek alkalmazva.

A Update Management azonban továbbra is jelenthetik, hogy a gép nem megfelelő, mert további információkkal szolgál a kapcsolódó frissítésről.

A frissítések Update besorolással történő központi telepítése nem működik a CentOS-ből a dobozból. A CentOS-frissítések megfelelő telepítéséhez válassza a minden besorolás lehetőséget a frissítések alkalmazásának biztosításához. A SUSE esetében, ha *csak* az "egyéb frissítések" lehetőséget választja, mert a besorolás bizonyos biztonsági frissítéseket is tartalmazhat, ha a Zypper (csomagkezelő) vagy annak függőségeire vonatkozó biztonsági frissítések szükségesek először. Ez a viselkedés a Zypper korlátozása. Bizonyos esetekben előfordulhat, hogy újra kell futtatnia a frissítés központi telepítését. Az ellenőrzéshez ellenőrizze a frissítési naplót.

### <a name="multi-tenant"></a>Több-bérlős frissítések telepítése

Ha olyan gépekkel rendelkezik, amelyeket egy másik Azure-bérlő jelent a javításhoz szükséges Update Management, akkor a következő megkerülő megoldást kell használnia ahhoz, hogy ütemezve legyen. A [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmagot használhatja a `-ForUpdate` kapcsolóval egy ütemterv létrehozásához, és a [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) parancsmagot használhatja, és átadhatja a másik bérlőben lévő gépeket a `-NonAzureComputer` paraméterhez. A következő példa egy példát mutat be erre:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Update Management engedélyezése

A rendszerek javításának megkezdéséhez engedélyeznie kell a Update Management megoldást. A gépek bevezetésének számos módja van Update Management. A megoldás bevezetésének ajánlott és támogatott módjai a következők:

* [Virtuális gépről](automation-onboard-solutions-from-vm.md)
* [Több gép tallózása](automation-onboard-solutions-from-browse.md)
* [Az Automation-fiókból](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation runbook](automation-onboard-solutions.md)

## <a name="next-steps"></a>Következő lépések

Folytassa az Oktatóanyaggal, amelyből megtudhatja, hogyan kezelheti a Windows rendszerű virtuális gépek frissítéseit.

> [!div class="nextstepaction"]
> [Az Azure Windows rendszerű virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)

* A részletes frissítési információk megtekintéséhez használja a naplóbeli kereséseket [Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md) .
* [Riasztások létrehozása](automation-tutorial-update-management.md#configure-alerts) a központi telepítés állapotának frissítéséhez.

* Ha szeretné megtudni, hogyan kezelheti Update Management a REST API segítségével, tekintse meg a [szoftverfrissítések konfigurációit](/rest/api/automation/softwareupdateconfigurations) ismertető témakört.
* A Update Management hibaelhárításával kapcsolatos további információkért lásd: [hibaelhárítás Update Management](troubleshoot/update-management.md)
