---
title: Update Management megoldás az Azure-ban
description: Ez a cikk azt ismerteti, hogyan használható az Azure Update Management-megoldás a Windows-és Linux-gépek frissítéseinek kezelésére.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: 9bdc8cf97513854cf6a92ffd078febca6302d35c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617412"
---
# <a name="update-management-solution-in-azure"></a>Update Management megoldás az Azure-ban

A Azure Automation **Update Management megoldásával** kezelheti az operációs rendszer frissítéseit az Azure-ban, a helyszíni környezetekben és más felhőalapú környezetekben lévő Windows-és Linux-gépekhez. Gyorsan felbecsülheti az összes ügynökön elérhető frissítések állapotát, és kezelheti a kiszolgálók szükséges frissítéseinek telepítésének folyamatát.

A virtuális gépek (VM-EK) Update Management a következő módszerekkel engedélyezhető:

- Azure Automation- [fiókjából](automation-onboard-solutions-from-automation-account.md) egy vagy több Azure-gép számára, és manuálisan a nem Azure-beli gépekhez.

- Egyetlen Azure-beli virtuális géphez a Azure Portal virtuális gép lapján. Ez a forgatókönyv [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) és [Windows rendszerű](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) virtuális gépek esetében érhető el.

- [Több Azure](manage-update-multi.md) -beli virtuális gép esetén válassza ki őket a Azure Portal Virtual Machines lapján. 

> [!NOTE]
> A **Update Management megoldáshoz** egy log Analytics-munkaterületet kell összekapcsolnia az Automation-fiókkal. A támogatott régiók végleges listáját az [Azure Workspace-hozzárendelések](./how-to/region-mappings.md)című témakörben tekintheti meg. A régió-hozzárendelések nem befolyásolják a virtuális gépek az Automation-fióktól eltérő régióban való felügyeletének képességét.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Az Azure [Resource Manager-sablonok](automation-update-management-deploy-template.md) segítséget nyújtanak a **Update Management megoldás** üzembe helyezéséhez egy új vagy meglévő Automation-fiókra, és log Analytics munkaterületre az előfizetésében.

## <a name="solution-overview"></a>Megoldás áttekintése

A Update Management által felügyelt gépek a következő konfigurációkat használják az értékelés végrehajtásához és a központi telepítések frissítéséhez:

* Windows vagy Linux rendszerhez készült Log Analytics ügynök
* PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
* Automation hibrid runbook-feldolgozó
* Microsoft Update vagy Windows Server Update Services (WSUS) Windows rendszerű gépekhez

A következő ábra azt szemlélteti, hogy a megoldás hogyan vizsgálja és alkalmazza a biztonsági frissítéseket a munkaterületen lévő összes csatlakoztatott Windows Server-és Linux-gépen:

![Update Management folyamat](./media/automation-update-management/update-mgmt-updateworkflow.png)

Az Update Management ahhoz is használható, hogy ugyanabban a bérlőben több előfizetésben készítse elő natív módon a gépeket.

A csomag felszabadítása után 2 – 3 órát vesz igénybe, hogy a javítás megjelenjen a Linux rendszerű gépek értékeléséhez. A Windows rendszerű gépek esetében 12 – 15 órát vesz igénybe, hogy a javítás megjelenjen az értékelés után.

Miután a gép befejezte a frissítések megfelelőségi vizsgálatát, az ügynök tömegesen továbbítja az adatokat Azure Monitor naplókhoz. Windows rendszerű gépen a megfelelőségi vizsgálat alapértelmezés szerint 12 óránként fut.

A vizsgálati ütemterven kívül a frissítés megfelelőségének vizsgálata a Log Analytics ügynök újraindítása után 15 percen belül, a frissítés telepítése előtt és a frissítés telepítése után kezdeményezhető.

Linux rendszerű gépek esetén a megfelelőségi vizsgálat alapértelmezés szerint óránként történik. Ha a Log Analytics ügynök újraindítása megtörtént, a rendszer 15 percen belül kezdeményezi a megfelelőségi vizsgálatot.

A megoldás azt mutatja be, hogy a gép milyen naprakészen van attól függően, hogy milyen forrásra van konfigurálva a szinkronizáláshoz. Ha a Windows rendszerű gép úgy van konfigurálva, hogy a WSUS szolgáltatásnak jelentsen, attól függően, hogy mikor szinkronizálta az Microsoft Update, az eredmények eltérhetnek attól, amit Microsoft Update mutat. Ez a viselkedés ugyanaz, mint a Linux rendszerű gépeken, amelyek úgy vannak konfigurálva, hogy nyilvános tárház helyett helyi tárházba jelentsenek.

> [!NOTE]
> Ahhoz, hogy a szolgáltatás megfelelően jelentsen, Update Management szükség van bizonyos URL-címekre és portokra. Ha többet szeretne megtudni ezekről a követelményekről, tekintse meg [a hibrid feldolgozók hálózati tervezése](automation-hybrid-runbook-worker.md#network-planning)című témakört.

A frissítéseket egy ütemezett telepítés létrehozásával telepítheti és telepítheti a frissítésekhez szükséges számítógépeken. A *választhatóként* besorolt frissítések nem szerepelnek a Windows rendszerű gépek központi telepítési hatókörében. A központi telepítési hatókörben csak a szükséges frissítések szerepelnek.

Az ütemezett telepítés meghatározza, hogy mely célszámítógépek kapják meg a megfelelő frissítéseket. Ezt az egyes gépek explicit módon történő megadásával, vagy egy olyan [számítógépcsoport](../azure-monitor/platform/computer-groups.md) kiválasztásával hajtja végre, amely egy adott gép (vagy egy olyan [Azure-lekérdezésen](automation-update-management-query-logs.md) alapul, amely dinamikusan kiválasztja az Azure-beli virtuális gépeket a megadott feltételek alapján). Ezek a csoportok eltérnek a [hatókör-konfigurációtól](../azure-monitor/insights/solution-targeting.md), amely csak annak meghatározására szolgál, hogy mely gépek kapják meg a megoldást használó felügyeleti csomagokat.

Megadhat egy ütemtervet is a jóváhagyáshoz, és beállíthatja azt az időtartamot, amely alatt a frissítések telepíthetők. Ezt az időtartamot karbantartási időszaknak nevezzük. A karbantartási időszak 20 perces időszaka újraindítások számára van fenntartva, feltételezve, hogy az egyik szükséges, és a megfelelő újraindítási beállítást választotta. Ha a javítás a vártnál hosszabb időt vesz igénybe, és a karbantartási időszakban kevesebb mint 20 perc van, a rendszer újraindítást hajt végre.

A telepítést az Azure Automation runbookjai végzik. Ezeket a runbookok nem lehet megtekinteni, és nem igényelnek semmilyen konfigurálást. Egy frissítés központi telepítésének létrehozásakor egy ütemezést hoz létre, amely a főfrissítési runbook a befoglalt gépekhez megadott időpontban indítja el. A fő runbook elindít egy gyermek runbook az egyes ügynökökön a szükséges frissítések telepítéséhez.

A központi telepítésben megadott napon és időpontban a célszámítógépeken párhuzamosan hajtják végre a telepítést. A telepítés előtt a rendszer ellenőrzi, hogy a frissítések továbbra is szükségesek-e. WSUS-ügyfélszámítógépek esetén, ha a frissítések nem lettek jóváhagyva a WSUS-ben, a frissítés telepítése sikertelen lesz.

Ha a Update Management több Log Analytics munkaterületen (más néven többhelyű) van regisztrálva a gépen, nem támogatott.

## <a name="clients"></a>Ügyfelek

### <a name="supported-client-types"></a>Támogatott ügyfelek típusai

A következő táblázat felsorolja a frissítési felmérések támogatott operációs rendszereit. A javításhoz hibrid Runbook-feldolgozóra van szükség. A hibrid Runbook-feldolgozói követelményekkel kapcsolatos információkért tekintse meg a [Windows hibrid Runbook-feldolgozók](automation-windows-hrw-install.md) és a [Linux hibrid Runbook](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)-feldolgozó telepítéséhez szükséges telepítési útmutatókat.

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/standard)<br><br>Windows Server 2012 R2 (Datacenter/standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM és SP1 standard)| Update Management csak az adott operációs rendszerre vonatkozó értékeléseket támogatja, a javítások nem támogatottak, mert a Windows Server 2008 R2 nem támogatja a [hibrid Runbook-feldolgozót](automation-windows-hrw-install.md) . |
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáférésre van szükségük egy frissítési tárházhoz. A besoroláson alapuló javításokhoz `yum` olyan biztonsági adatforrásokat kell visszaadnia, amelyeket a CentOS nem tartalmaz a RTM kiadásokban. A CentOS besoroláson alapuló javításával kapcsolatos további információkért lásd: [frissítési besorolások Linux](automation-view-update-assessments.md#linux-2)rendszeren.          |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáférésre van szükségük egy frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáférésre van szükségük egy frissítési tárházhoz.        |
|Ubuntu 14,04 LTS, 16,04 LTS és 18,04 (x86/x64)      |A Linux-ügynököknek hozzáférésre van szükségük egy frissítési tárházhoz.         |

> [!NOTE]
> Az Azure-beli virtuálisgép-méretezési csoportok a Update Management használatával kezelhetők. A Update Management a példányokon működik, nem az alapképre. A frissítéseket növekményes módon kell ütemeznie, hogy a virtuálisgép-példányok ne legyenek egyszerre frissítve.
> A virtuálisgép-méretezési csoportok csomópontjait a [nem Azure-beli számítógép](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)bevezetésének lépéseit követve veheti fel.

### <a name="unsupported-client-types"></a>Nem támogatott ügyfelek típusai

A következő táblázat a nem támogatott operációs rendszereket sorolja fel:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows-ügyfél     | Az ügyféloldali operációs rendszerek (például a Windows 7 és a Windows 10) nem támogatottak.        |
|Windows Server 2016 Nano Server     | Nem támogatott.       |
|Azure Kubernetes szolgáltatási csomópontok | Nem támogatott. Használja a [biztonsági és kernel-frissítések alkalmazása Linux-csomópontokra az Azure Kubernetes szolgáltatásban (ak)](../aks/node-updates-kured.md) című témakörben ismertetett javítási folyamatot|

### <a name="client-requirements"></a>Ügyfélkövetelmények

Az alábbi információk az operációs rendszerre vonatkozó ügyfelek követelményeit ismertetik. További útmutatásért lásd: [hálózati tervezés](#ports).

#### <a name="windows"></a>Windows

A Windows-ügynököket úgy kell konfigurálni, hogy a WSUS-kiszolgálóval kommunikáljanak, vagy hozzáférést igényelnek a Microsoft Updatehoz.

A Update Management a Configuration Manager használatával végezheti el. Az integrációs forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [a Configuration Manager integrálása Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration)használatával. A [log Analytics Windows-ügynököt](../azure-monitor/platform/agent-windows.md) kötelező megadni. Az ügynök automatikusan települ, ha Azure-beli virtuális gépet helyez üzembe.

Alapértelmezés szerint az Azure Marketplace-ről üzembe helyezett Windows-alapú virtuális gépek a Windows Update szolgáltatásból származó automatikus frissítések fogadására vannak beállítva. Ez a viselkedés nem változik, ha hozzáadja ezt a megoldást, vagy Windows rendszerű virtuális gépeket ad hozzá a munkaterülethez. Ha nem kezeli aktívan a frissítéseket a megoldás használatával, a rendszer az alapértelmezett működést (a frissítések automatikus érvénybe lépését) alkalmazza.

> [!NOTE]
> A felhasználók módosíthatják Csoportházirend, hogy a számítógép újraindítását csak a felhasználó hajtsa végre, nem pedig a rendszer. A felügyelt gépek elakadnak abban az esetben, ha Update Management nem rendelkezik jogosultsággal a gép újraindításához a felhasználó manuális beavatkozása nélkül.
>
> További információ: [csoportházirend beállítások konfigurálása az automatikus frissítésekhez](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Linux esetén a számítógépnek hozzáférésre van szüksége egy frissítési tárházhoz. A frissítési tárház lehet magán vagy nyilvános. A Update Managementhoz való interakcióhoz TLS 1,1 vagy TLS 1,2 szükséges. Ez a megoldás nem támogatja az olyan Log Analytics Linux-ügynököt, amely több Log Analytics munkaterületre való jelentésre van konfigurálva. A gépnek a Python 2. x verziójának is telepítve kell lennie.

További információ a Linux rendszerhez készült Log Analytics-ügynök telepítéséről és a legújabb verzió letöltéséről: [log Analytics Agent for Linux](../azure-monitor/platform/agent-linux.md). További információ a Windows rendszerű Log Analytics ügynök telepítéséről: [Windows rendszerű számítógépek Összekötése Azure monitorhoz](../log-analytics/log-analytics-windows-agent.md).

Az Azure piactéren elérhető, igény szerinti Red Hat Enterprise Linux (RHEL) lemezképből létrehozott virtuális gépek regisztrálva vannak az Azure-ban üzembe helyezett [Red Hat frissítési infrastruktúrához (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) . Minden más Linux-disztribúciót a terjesztés támogatott módszereinek használatával kell frissíteni a disztribúció online fájl-tárházában.

## <a name="permissions"></a>Engedélyek

A frissítési központi telepítések létrehozásához és kezeléséhez konkrét engedélyekre van szükség. További információ ezekről az engedélyekről: [szerepköralapú hozzáférés – Update Management](automation-role-based-access-control.md#update-management-permissions).

## <a name="solution-components"></a>Megoldás-összetevők

A megoldás a következő erőforrásokból áll. Ezeket az erőforrásokat a rendszer automatikusan hozzáadja az Automation-fiókhoz a megoldás engedélyezésekor. 

### <a name="hybrid-worker-groups"></a>Hibridfeldolgozó-csoportok

Ha engedélyezi ezt a megoldást, a Log Analytics munkaterülethez közvetlenül csatlakozó Windows-gépeket a rendszer automatikusan hibrid Runbook-feldolgozóként konfigurálja a megoldásban foglalt runbookok támogatásához.

A megoldás által felügyelt minden Windows-gép a hibrid feldolgozói csoportok panelen, az Automation-fiókhoz tartozó rendszer hibrid feldolgozói csoportként jelenik meg. A megoldások az `Hostname FQDN_GUID` elnevezési konvenciót használják. Ezek a csoportok nem állíthatók be a runbookok a fiókjában. Ha próbálkozik, a kísérlet sikertelen lesz. Ezek a csoportok csak ezt a felügyeleti megoldást támogatják.

A Windows rendszerű gépet hozzáadhatja az Automation-fiókjában lévő hibrid Runbook Worker csoporthoz az Automation-runbookok támogatásához, ha ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook-feldolgozói csoport tagságához. Ez a funkció a hibrid Runbook-feldolgozó verziójának 7.2.12024.0 lett hozzáadva.

### <a name="management-packs"></a>Felügyeleti csomagok

Ha a System Center Operations Manager felügyeleti csoport [egy log Analytics munkaterülethez csatlakozik](../azure-monitor/platform/om-agents.md), a következő felügyeleti csomagok lesznek telepítve a Operations Managerban. Ezeket a felügyeleti csomagokat a megoldás hozzáadása után közvetlenül csatlakoztatott Windows rendszerű gépekre is telepíti a rendszer. A felügyeleti csomagokat nem szükséges konfigurálni vagy felügyelni.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

> [!NOTE]
> Ha van olyan Operations Manager 1807 vagy 2019 felügyeleti csoport, amely egy Log Analytics munkaterülethez van csatlakoztatva, és a felügyeleti csoportban a naplózási adatok gyűjtéséhez konfigurált ügynökök vannak konfigurálva `IsAutoRegistrationEnabled` , akkor a paramétert felül kell bírálni a **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** szabályban.

A megoldás-felügyeleti csomagok frissítésével kapcsolatos további információkért lásd: [Operations Manager Összekötése Azure monitor naplókhoz](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Az Operations Manager-ügynököt Update Management által teljes mértékben felügyelt gépek esetében az ügynököt frissíteni kell a Windows Log Analytics ügynökének vagy a Linux Log Analytics ügynökének. Az ügynök frissítésével kapcsolatos további információkért lásd: [Operations Manager-ügynök frissítése](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). A Operations Managert használó környezetekben System Center Operations Manager 2012 R2-es vagy újabb verzióját kell futtatnia.

## <a name="data-collection"></a>Adatgyűjtés

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A megoldás adatokat gyűjt a Windows-ügynököktől a rendszerfrissítésekről, majd elindítja a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A megoldás adatokat gyűjt a Linux-ügynököktől a rendszerfrissítésekről, majd kezdeményezi a szükséges frissítések telepítését a támogatott disztribúciók esetében. |
| Az Operations Manager felügyeleti csoportja |Igen |A megoldás információt szerez be a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről.<br/><br/>Nincs szükség közvetlen kapcsolódásra a Operations Manager ügynöktől a Azure Monitor naplókhoz. Az adatok továbbítása a felügyeleti csoportból a Log Analytics munkaterületre történik. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

Minden felügyelt Windows-gép naponta kétszer végez vizsgálatot. A rendszer 15 percenként lekérdezi a Windows API-t, hogy lekérdezze az utolsó frissítés idejét annak megállapításához, hogy megváltozott-e az állapot. Ha az állapot módosult, a rendszer megfelelőségi vizsgálatot kezdeményez.

Minden egyes felügyelt Linux rendszerű gép óránként vizsgálatot végez.

30 perc és 6 óra között eltarthat, amíg az irányítópult a felügyelt gépekről származó frissített adatok megjelenítésére is képes.

Egy Update Management-t használó gép átlagos adatfelhasználása Azure Monitor-naplók havonta körülbelül 25 megabájt (MB). Ez az érték csak egy közelítés, és változhat a környezettől függően. Javasoljuk, hogy figyelje a környezetét, hogy nyomon követhesse a pontos használatot. További információ az adatfelhasználás elemzéséről: a [használat és a Cost kezelése](../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Hálózattervezés

A következő címek megadása kifejezetten a Update Management. A címekkel folytatott kommunikáció az 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net | *. blob.core.usgovcloudapi.net|
|*.azure-automation.net | *. azure-automation.us|

Windows rendszerű gépek esetén a Windows Update által igényelt végpontokra is engedélyeznie kell a forgalmat. A szükséges végpontok frissített listáját a [http/proxy szolgáltatással kapcsolatos problémák](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)között találja. Ha helyi [Windows Update-kiszolgálóval](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)rendelkezik, engedélyeznie kell a forgalmat a [WSUS-kulcsban](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)megadott kiszolgálóra is.

A Red Hat Linux rendszerű gépek esetében tekintse meg [az IP-címek a RHUI](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) a szükséges végpontokhoz című témakört. Más Linux-disztribúciók esetében tekintse meg a szolgáltatói dokumentációt.

A hibrid Runbook-feldolgozók számára szükséges portokkal kapcsolatos további információkért lásd a [hibrid feldolgozói szerepkör portjait](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Javasoljuk, hogy használja a kivételek meghatározásakor felsorolt címeket. IP-címek esetén letöltheti [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653). A fájl hetente frissül, és az IP-címtartományok aktuálisan telepített tartományait és minden közelgő változását tükrözi.

Az internet-hozzáféréssel nem rendelkező számítógépek konfigurálásához kövesse az [Internet-hozzáférés nélküli számítógépek összekapcsolására](../azure-monitor/platform/gateway.md) vonatkozó utasításokat.

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

### <a name="linux"></a><a name="linux-2"></a>Linux

|Osztályozás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Adott probléma vagy termékspecifikus, biztonsággal kapcsolatos probléma frissítései.         |
|Egyéb frissítések     | Minden egyéb olyan frissítés, amely nem kritikus jellegű, vagy amelyek nem biztonsági frissítések.        |

A Linux rendszerben a Update Management a Felhőbeli adatgazdagítás miatt kiértékelheti a kritikus frissítések és a biztonsági frissítések közötti különbséget a felhőben. A javításhoz a Update Management a gépen elérhető besorolási értékekre támaszkodik. A többi disztribúciótól eltérően a CentOS nem rendelkezik ezekkel az információkkal az RTM verziójában. Ha olyan CentOS-számítógépekkel rendelkezik, amelyek a következő parancs biztonsági értékének visszaadására vannak konfigurálva, a Update Management a besorolások alapján javíthatja a javítást.

```bash
sudo yum -q --security check-update
```

Jelenleg nem támogatott a natív besorolás engedélyezése – az adatelérhetőség a CentOS-ben. Jelenleg csak a lehető legjobb támogatást nyújtja azoknak az ügyfeleknek, akik saját maguk is engedélyezték ezt. 

A Red Hat Enterprise 6-os verziójának frissítéseinek besorolásához telepítenie kell a yum-Security beépülő modult. Red Hat Enterprise Linux 7 rendszeren a beépülő modul már maga is része a yum szolgáltatásnak, nem kell semmit telepítenie. További információkért lásd a Red Hat [Tudásbázis következő cikkét](https://access.redhat.com/solutions/10021).

## <a name="integrate-with-configuration-manager"></a>Integrálás a Configuration Managerrel

A számítógépek, kiszolgálók és mobileszközök kezeléséhez a Microsoft Endpoint Configuration Managerba befektetett ügyfelek is a Configuration Manager erősségét és érettségét használják a szoftverfrissítések kezeléséhez. Configuration Manager a szoftverfrissítés-kezelési (SUM) ciklus részét képezi.

Ha szeretné megtudni, hogyan integrálható a felügyeleti megoldás Configuration Manager használatával, tekintse meg a [Configuration Manager integrálása a Update Management](oms-solution-updatemgmt-sccmintegration.md)használatával című témakört.

### <a name="third-party-updates-on-windows"></a>Harmadik féltől származó frissítések Windows rendszeren

A Update Management a helyileg konfigurált frissítési tárházra támaszkodik a támogatott Windows-rendszerek frissítésére. Ez vagy a WSUS vagy a Windows Update. Az olyan eszközök, mint a [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (a frissítések közzétevője) lehetővé teszik egyéni frissítések importálását és közzétételét a WSUS szolgáltatásban. Ez a forgatókönyv lehetővé teszi, hogy Update Management frissítse a Configuration Managert használó gépeket a harmadik féltől származó szoftverrel rendelkező frissítési adattárként. Az Updates Publisher konfigurálásával kapcsolatos további információkért lásd: a [frissítések közzétevő telepítése](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a><a name="onboard"></a>Update Management engedélyezése

A rendszerek frissítésének megkezdéséhez engedélyeznie kell a Update Management megoldást. A megoldás bevezetésének ajánlott és támogatott módszerei a következők:

- [Virtuális gépről](automation-onboard-solutions-from-vm.md)
- [Több gép tallózása](automation-onboard-solutions-from-browse.md)
- [Az Automation-fiókból](automation-onboard-solutions-from-automation-account.md)
- [Azure Automation runbook](automation-onboard-solutions.md)
- [Azure Resource Manager sablonnal](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>További lépések

A megoldással kapcsolatos gyakori kérdések áttekintéséhez tekintse át a Azure Automation [GYIK](automation-faq.md) című szakaszt.
