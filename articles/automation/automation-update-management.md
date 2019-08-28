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
ms.openlocfilehash: 6f23a1f8e60567e1c2ed89b27f0eb2bab4ca5912
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061813"
---
# <a name="update-management-solution-in-azure"></a>Update Management megoldás az Azure-ban

A Azure Automation Update Management megoldásával kezelheti az operációs rendszer frissítéseit az Azure-ban, a helyszíni környezetekben vagy más felhőalapú szolgáltatókban lévő Windows-és Linux-számítógépeken. Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és felügyelhető a kiszolgálók szükséges frissítéseinek telepítése is.

A virtuális gépeket közvetlenül a Azure Automation-fiókjából engedélyezheti Update Management. Ha szeretné megtudni, hogyan engedélyezheti a virtuális gépek Update Managementét az Automation-fiókjából, tekintse meg a [több virtuális gép frissítéseinek kezelése](manage-update-multi.md)című témakört. A virtuális gép Update Management is engedélyezheti a Azure Portal virtuális gép lapján. Ez a forgatókönyv [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) és [Windows rendszerű](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuális gépek esetén érhető el.

> [!NOTE]
> A Update Management megoldáshoz egy Log Analytics-munkaterületet kell összekapcsolnia az Automation-fiókkal. A támogatott régiók végleges listáját az [Azure Workspace](./how-to/region-mappings.md)-hozzárendelések című témakörben tekintheti meg. A régió-hozzárendelések nem befolyásolják a virtuális gépek az Automation-fióktól eltérő régióban való felügyeletének képességét.

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

A szoftverfrissítések központi telepítéséhez vagy telepítéséhez létrehozhat egy ütemezett üzembe helyezést a frissítést igénylő számítógépeken. A választhatóként besorolt frissítések nem szerepelnek a Windows rendszerű számítógépek központi telepítési hatókörében. A központi telepítési hatókörben csak a szükséges frissítések szerepelnek.

Az ütemezett telepítés meghatározza, hogy mely célszámítógépek kapják meg a megfelelő frissítéseket, vagy a számítógépek explicit módon történő megadásával, vagy egy adott számítógép, illetve egy [Azure-lekérdezés](#azure-machines) naplójának keresésén alapuló [számítógépcsoport](../azure-monitor/platform/computer-groups.md) kiválasztásával. Ez dinamikusan kiválasztja az Azure-beli virtuális gépeket a megadott feltételek alapján. Ezek a csoportok eltérnek a [hatókör](../azure-monitor/insights/solution-targeting.md)-konfigurációtól, amely csak annak meghatározására szolgál, hogy mely gépek kapják meg a megoldást használó felügyeleti csomagokat.

Megadhat egy ütemtervet is a jóváhagyáshoz, és beállíthatja azt az időtartamot, ameddig a frissítések telepíthetők. Ezt az időtartamot karbantartási időszaknak nevezzük. Ha újraindításra van szükség, a karbantartási időszak tíz perce van fenntartva újraindítás esetén, és kiválasztotta a megfelelő újraindítási beállítást. Ha a javítás a vártnál hosszabb időt vesz igénybe, és a karbantartási időszakban kevesebb, mint tíz perc van, a rendszer újraindítást hajt végre.

A telepítést az Azure Automation runbookjai végzik. Ezeket a runbookok nem lehet megtekinteni, és a runbookok nem igényel konfigurálást. Egy frissítés központi telepítésének létrehozásakor a frissítés központi telepítése egy olyan ütemezést hoz létre, amely egy fő frissítési runbook indít el a befoglalt számítógépeken a megadott időpontban. A fő runbook elindít egy gyermek runbook az egyes ügynökökön a szükséges frissítések telepítéséhez.

A frissítés központi telepítésében megadott dátum és idő szerint a célszámítógépek párhuzamosan hajtják végre a telepítést. A telepítés előtt a rendszer ellenőrzi, hogy a frissítések továbbra is szükségesek-e. WSUS-ügyfélszámítógépek esetén, ha a frissítések nem hagyták jóvá a WSUS-ben, a frissítés telepítése sikertelen lesz.

Ha több Log Analytics munkaterületen (több-önkiszolgáló) lévő Update Managementhoz regisztrált gép van, nem támogatott.

## <a name="clients"></a>Ügyfelek

### <a name="supported-client-types"></a>Támogatott ügyfél típusú

A következő táblázat a támogatott operációs rendszerek listáját tartalmazza:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | A csak a frissítési felméréseket támogatja.         |
|Windows Server 2019 (Datacenter/Datacenter Core/standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/standard)<br><br>Windows Server 2012 R2 (Datacenter/standard)<br><br>Windows Server 2008 R2 (RTM és SP1 standard)|A .NET-keretrendszer 4.5.1-es vagy újabb verziója szükséges. ([.NET-keretrendszer letöltése](/dotnet/framework/install/guide-for-developers))<br/> A Windows PowerShell 4,0-es vagy újabb verziójára van szükség. ([WMF 4,0 letöltése](https://www.microsoft.com/download/details.aspx?id=40855))<br/> A fokozott megbízhatóság érdekében ajánlott a Windows PowerShell 5,1.  ([WMF 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz. A besoroláson alapuló javításhoz a "yum" utasítás szükséges ahhoz, hogy a CentOS ne legyen a dobozban. A CentOS besoroláson alapuló javításával kapcsolatos további információkért lásd: [frissítési besorolások Linux](#linux-2) rendszeren          |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 14,04 LTS, 16,04 LTS és 18,04 (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

> [!NOTE]
> Az Azure-beli virtuálisgép-méretezési csoportok a Update Management használatával kezelhetők. A Update Management a példányokon működik, nem az alapképet. A frissítéseket növekményes módon kell ütemeznie, hogy az összes virtuálisgép-példány egyszerre ne legyen frissítve.
> VMSS-csomópontok hozzáadásához kövesse a [nem Azure-beli gép onbaord](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)tartozó lépéseket.

### <a name="unsupported-client-types"></a>Nem támogatott ügyfélalkalmazás típusa

Az alábbi táblázat a nem támogatott operációs rendszerek:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows-ügyfél     | Ügyféloldali operációs rendszerek (például Windows 7 és Windows 10-es) nem támogatottak.        |
|A Windows Server 2016 Nano Server     | Nem támogatott.       |
|Azure Kubernetes szolgáltatási csomópontok | Nem támogatott. Az [Azure Kubernetes Service (ak) által használt Linux-csomópontok biztonsági és kernel-frissítéseinek alkalmazása](../aks/node-updates-kured.md) című részletes javítási folyamat|

### <a name="client-requirements"></a>Ügyfélre vonatkozó követelmények

#### <a name="windows"></a>Windows

A Windows-ügynököket úgy kell konfigurálni, hogy a WSUS-kiszolgálóval kommunikáljanak, vagy hozzá kell férniük Microsoft Updatehoz. A Update Management a System Center Configuration Manager használatával végezheti el. Az integrációs forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [a System Center Configuration Manager integrálása Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration)használatával. A [Windows](../azure-monitor/platform/agent-windows.md) -ügynököt kötelező megadni. Az ügynök automatikusan települ, ha Azure-beli virtuális gépet helyez üzembe.

> [!NOTE]
> Lehetséges, hogy a felhasználó módosíthatja Csoportházirend, hogy a számítógép újraindítása csak a felhasználó által, nem pedig a rendszer által végezhető el. A felügyelt gépek elakadnak, ha Update Management nem rendelkezik jogosultsággal a gép újraindításához a felhasználó manuális beavatkozása nélkül.
>
> További információ: [csoportházirend beállítások konfigurálása az automatikus frissítésekhez](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Linux esetén a gépnek hozzá kell férnie egy frissítési tárházhoz. A frissítési tárház lehet magán vagy nyilvános. A Update Managementhoz való interakcióhoz TLS 1,1 vagy TLS 1,2 szükséges. Ez a megoldás nem támogatja az olyan Log Analytics Linux-ügynököt, amely több Log Analytics munkaterületnek való jelentésre van konfigurálva.

További információ a Linux rendszerhez készült Log Analytics-ügynök telepítéséről és a legújabb verzió letöltéséről: [log Analytics Agent for Linux](https://github.com/microsoft/oms-agent-for-linux). További információ a Windows Log Analytics-ügynök telepítéséről: [Microsoft monitoring Agent for Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Engedélyek

A frissítési központi telepítések létrehozásához és kezeléséhez konkrét engedélyekre van szükség. További információ ezekről az engedélyekről: [szerepköralapú hozzáférés – Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Megoldás-összetevők

A megoldás a következő erőforrásokból áll. A rendszer hozzáadja az erőforrásokat az Automation-fiókhoz. Közvetlenül csatlakoztatott ügynökök vagy Operations Manager csatlakoztatott felügyeleti csoportban vannak.

### <a name="hybrid-worker-groups"></a>Hibridfeldolgozó-csoportok

A megoldás engedélyezése után a Log Analytics munkaterülethez közvetlenül csatlakozó Windows-számítógépek automatikusan hibrid Runbook-feldolgozóként vannak konfigurálva, hogy támogassák a megoldásban foglalt runbookok.

A megoldás által felügyelt Windows-számítógépek a **hibrid munkavégző csoportok** ablaktáblán jelennek meg az Automation-fiókhoz tartozó **rendszer hibrid** feldolgozói csoportként. A megoldások az elnevezési konvenció *hostname FQDN_GUID*használják. Ezek a csoportok nem állíthatók be a runbookok a fiókjában. Ha próbálkozik, sikertelenek lesznek. Ezek a csoportok kizárólag a felügyeleti megoldást támogatják.

A Windows rendszerű számítógépeket felveheti az Automation-fiókjában lévő hibrid Runbook-feldolgozó csoportba az Automation-runbookok támogatásához, ha ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook Worker Group tagsághoz. Ez a funkció a hibrid Runbook-feldolgozó verziójának 7.2.12024.0 lett hozzáadva.

### <a name="management-packs"></a>Felügyeleti csomagok

Ha a System Center Operations Manager felügyeleti csoport egy Log Analytics munkaterülethez csatlakozik, a következő felügyeleti csomagok lesznek telepítve a Operations Managerban. Ezeket a felügyeleti csomagokat a megoldás hozzáadása után közvetlenül csatlakoztatott Windows rendszerű számítógépekre is telepíti a rendszer. Ezeket a felügyeleti csomagokat nem kell konfigurálnia vagy kezelnie.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

> [!NOTE]
> Ha olyan Operations Manager 1807 felügyeleti csoporttal rendelkezik, amelyben a felügyeleti csoport szintjén konfigurált ügynökök vannak társítva egy munkaterülethez, az aktuális megkerülő Áthidaló megoldás az, hogy megjelenjen a IsAutoRegistrationEnabled **igaz** értékre állítása a következőben: **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** szabály.

A megoldás-felügyeleti csomagok frissítésével kapcsolatos további információkért lásd: [Operations Manager Összekötése Azure monitor naplókhoz](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Az Operations Manager-ügynököt tartalmazó rendszerek esetében a Update Management teljes körű felügyeletéhez az ügynököt frissíteni kell a Microsoft monitoring Agentre. Az ügynök frissítésével kapcsolatos további információkért lásd: [Operations Manager-ügynök frissítése](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). A Operations Managert használó környezetek esetén szükséges, hogy System Center Operations Manager 2012 R2-es vagy újabb verzióját futtassa.

## <a name="onboard"></a>Update Management engedélyezése

A rendszerek javításának megkezdéséhez engedélyeznie kell a Update Management megoldást. A gépek bevezetésének számos módja van Update Management. A megoldás bevezetésének ajánlott és támogatott módjai a következők:

* [Virtuális gépről](automation-onboard-solutions-from-vm.md)
* [Több gép tallózása](automation-onboard-solutions-from-browse.md)
* [Az Automation-fiókból](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation runbook](automation-onboard-solutions.md)

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Győződjön meg arról, hogy a nem Azure-beli gépek vannak bekészítve

Annak ellenőrzéséhez, hogy a közvetlenül csatlakoztatott gépek Azure Monitor-naplókkal kommunikálnak-e, néhány perc elteltével futtathatja az alábbi naplók egyikét.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Windows rendszerű számítógépen a következő információkat tekintheti meg az ügynök kapcsolatának ellenőrzéséhez Azure Monitor naplók használatával:

1. A vezérlőpulton nyissa meg a **Microsoft monitoring Agent ügynököt**. Az **Azure log Analytics** lapon az ügynök a következő üzenetet jeleníti meg: **A Microsoft monitoring Agent sikeresen csatlakozott a log Analyticshoz**.
2. Nyissa meg a Windows eseménynaplót. Nyissa meg az **Application and Services Logs\Operations Manager alkalmazást** , és keressen rá a 3000-es azonosítójú eseményre és a 5002-es azonosítójú eseményre. Ezek az események azt jelzik, hogy a számítógép regisztrálva van a Log Analytics munkaterületen, és fogadja a konfigurációt.

Ha az ügynök nem tud kommunikálni Azure Monitor naplókkal, és az ügynök úgy van konfigurálva, hogy tűzfalon vagy proxykiszolgálón keresztül kommunikáljon az internettel, ellenőrizze, hogy a tűzfal vagy a proxykiszolgáló megfelelően van-e konfigurálva. A tűzfal vagy a proxykiszolgáló megfelelő konfigurálásának ellenőrzéséhez tekintse meg a [hálózati konfiguráció Windows](../azure-monitor/platform/agent-windows.md) -ügynökhöz vagy [a Linux-ügynök hálózati konfigurációja](../log-analytics/log-analytics-agent-linux.md)című témakört.

> [!NOTE]
> Ha a linuxos rendszerek proxy vagy Log Analytics átjáróval való kommunikációra vannak konfigurálva, és a megoldás előkészítését végzi, frissítse a *proxy. conf* engedélyeit, hogy a omiuser csoport olvasási engedélyt adjon a fájlhoz a következő parancsok használatával:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Az újonnan hozzáadott Linux-ügynökök a felmérés végrehajtása után frissülő állapotot mutatnak. A folyamat akár hat órát is igénybe vehet.

Annak ellenőrzéséhez, hogy egy Operations Manager felügyeleti csoport kommunikál-e Azure Monitor naplókkal, tekintse meg az [Operations Manager integráció ellenőrzése Azure monitor naplókkal](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)című témakört.

## <a name="data-collection"></a>Adatgyűjtés

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A megoldás adatokat gyűjt a Windows-ügynököktől a rendszerfrissítésekről, majd elindítja a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A megoldás adatokat gyűjt a Linux-ügynököktől a rendszerfrissítésekről, majd kezdeményezi a szükséges frissítések telepítését a támogatott disztribúciók esetében. |
| Az Operations Manager felügyeleti csoportja |Igen |A megoldás információt szerez be a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről.<br/>Nincs szükség közvetlen kapcsolódásra a Operations Manager ügynöktől a Azure Monitor naplókhoz. Adatok lesznek továbbítva a felügyeleti csoportból a Log Analytics-munkaterületet. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

Minden felügyelt Windows-számítógép naponta kétszer végez vizsgálatot. A rendszer 15 percenként lekérdezi a Windows API-t, hogy lekérdezze az utolsó frissítés idejét annak megállapításához, hogy megváltozott-e az állapot. Ha az állapot módosult, a rendszer megfelelőségi vizsgálatot kezdeményez.

Minden egyes felügyelt Linux rendszerű számítógéphez óránként vizsgálatot kell végrehajtani.

A felügyelt számítógépekről származó frissített adatok megjelenítéséhez az irányítópult 30 perc és 6 óra között is eltarthat.

Az átlagos Azure Monitor a Update Management-t használó gépek adatfelhasználását körülbelül 25MB havonta. Ez az érték csak közelítés, és a környezettől függően változhat. Javasoljuk, hogy figyelje a környezetét, és tekintse meg a pontos használatot.

## <a name="viewing-update-assessments"></a>Frissítési felmérések megtekintése

Az Automation-fiókjában válassza a **Update Management** lehetőséget a gépek állapotának megtekintéséhez.

Ez a nézet a gépekre, a hiányzó frissítésekre, a frissítések telepítésére és az ütemezett frissítések telepítésére vonatkozó információkat tartalmaz. A **megfelelőség oszlopban**láthatja, hogy mikor történt a gép utolsó értékelése. A **frissítési** ügynök készültségi oszlopában láthatja, hogy a frissítési ügynök állapota. Ha probléma merül fel, a hivatkozásra kattintva keresse meg a hibaelhárítási dokumentációt, amelyből megtudhatja, hogy milyen lépéseket kell tennie a probléma megoldásához.

Ha olyan naplót szeretne futtatni, amely adatokat ad vissza a gépre, a frissítésre vagy a központi telepítésre vonatkozóan, válassza ki az elemet a listában. Megnyílik a **napló keresése** ablaktábla a kiválasztott elem lekérdezésével:

![Update Management alapértelmezett nézet](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Frissítések telepítése

A frissítések a munkaterületen található összes Linux-és Windows-számítógépen való értékelését követően a szükséges frissítéseket a *frissítés központi telepítésének*létrehozásával is telepítheti. A frissítés központi telepítésének létrehozásához írási hozzáféréssel kell rendelkeznie az Automation-fiókhoz, és írási hozzáférésre van szüksége az üzemelő példányra irányuló összes Azure-beli virtuális géphez. A frissítés központi telepítése egy vagy több számítógép szükséges frissítéseinek ütemezett telepítése. Megadhatja a központi telepítés dátumát és időpontját, valamint a központi telepítés hatókörében szerepeltetni kívánt számítógép vagy számítógép csoportját. További információ a számítógépcsoportokról: [számítógépcsoportok Azure monitor-naplókban](../azure-monitor/platform/computer-groups.md).

Ha számítógép-csoportokat is tartalmaz a frissítés központi telepítéséhez, a csoporttagság csak egyszer lesz kiértékelve, az ütemezett létrehozás időpontjában. A csoport későbbi módosításai nem tükröződnek. A [dinamikus csoportok](#using-dynamic-groups)használatának megkezdéséhez ezeket a csoportokat a rendszer központi telepítési időpontban oldja meg, és az Azure-beli virtuális gépek lekérdezésével vagy nem Azure-beli virtuális gépek mentett keresésével határozza meg.

> [!NOTE]
> Az Azure Marketplace-ről üzembe helyezett Windows-beli virtuális gépek alapértelmezés szerint a Windows Update szolgáltatásból származó automatikus frissítések fogadására vannak beállítva. Ez a viselkedés nem változik, ha hozzáadja ezt a megoldást, vagy Windows rendszerű virtuális gépeket ad hozzá a munkaterülethez. Ha nem kezeli aktívan a frissítéseket a megoldás használatával, a rendszer az alapértelmezett működést (a frissítések automatikus érvénybe lépését) alkalmazza.

Ha el szeretné kerülni, hogy a frissítések ne legyenek alkalmazva az Ubuntu karbantartási időszakán kívül, konfigurálja újra a felügyelet nélküli frissítési csomagot az automatikus frissítések letiltásához. A csomag konfigurálásával kapcsolatos további információkért lásd [az Ubuntu Server útmutatójának automatikus frissítések témakörét](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Az Azure piactéren elérhető, igény szerinti Red Hat Enterprise Linux (RHEL) lemezképből létrehozott virtuális gépek regisztrálva vannak az Azure-ban üzembe helyezett [Red Hat frissítési infrastruktúrához (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) . A terjesztés támogatott módszereinek követésével minden más Linux-disztribúciót frissíteni kell az elosztás online fájljából.

Új frissítés központi telepítésének létrehozásához válassza a **frissítés központi telepítésének ütemezett**beállítást. Megnyílik az **új frissítés központi telepítése** oldal. Adja meg az alábbi táblázatban leírt tulajdonságok értékeit, majd kattintson a **Létrehozás**gombra:

| Tulajdonság | Description |
| --- | --- |
| Name (Név) |A frissítéstelepítést beazonosító egyedi név. |
|Operációs rendszer| Linux vagy Windows|
| Frissítendő csoportok |Azure-gépek esetén az előfizetés, az erőforráscsoportok, a helyszínek és a címkék kombinációja alapján hozzon létre egy lekérdezést az üzembe helyezéshez felvenni kívánt Azure-beli virtuális gépek dinamikus csoportjának létrehozásához. </br></br>Nem Azure-beli gépek esetén válasszon ki egy meglévő mentett keresést, és válasszon ki egy olyan csoportot, amely nem Azure-beli gépeket tartalmaz az üzembe helyezéshez. </br></br>További információ: [dinamikus csoportok](automation-update-management.md#using-dynamic-groups)|
| Frissítendő gépek |Válasszon ki egy mentett keresést, importált csoportot, vagy válasszon gépet a legördülő listából, és válassza az egyes gépek lehetőséget. Ha a **Gépek** lehetőséget választotta, a gép állapota az **ÜGYNÖK KÉSZÜLTSÉGÉNEK FRISSÍTÉSE** oszlopban látható.</br> A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről a következő témakörben talál további információt: [számítógépcsoportok Azure monitor-naplókban](../azure-monitor/platform/computer-groups.md) |
|Frissítési besorolások|Válassza ki az összes szükséges frissítési besorolást|
|Frissítések belefoglalása/kizárása|Ekkor megnyílik a **Belefoglalás/kizárás** oldal. A belefoglalandó vagy kizárandó frissítések külön lapokon jelennek meg. További információ a bevonások kezeléséről: a [beilleszkedési viselkedés](automation-update-management.md#inclusion-behavior) |
|Ütemezési beállítások|Válassza ki a kezdéshez szükséges időt, és válasszon egyszer vagy ismétlődő lehetőséget az ismétlődéshez|
| Parancsfájlok előtti + parancsfájlok utáni|Válassza ki az üzembe helyezés előtt és után futtatandó parancsfájlokat|
| Karbantartási időszak |A frissítések számára beállított percek száma. Az érték nem lehet kevesebb, mint 30 perc, legfeljebb 6 óra |
| Vezérlő újraindítása| Meghatározza az újraindítások kezelését. Az elérhető lehetőségek:</br>Újraindítás szükség esetén (alapértelmezett beállítás)</br>Mindig induljon újra</br>Soha ne induljon újra</br>Csak újraindítás – frissítések nem lesznek telepítve|

A frissítési központi telepítések programozott módon is létrehozhatók. Ha meg szeretné tudni, hogyan hozhat létre frissítési központi telepítést a REST API, tekintse meg a [szoftverfrissítési konfigurációk – létrehozás](/rest/api/automation/softwareupdateconfigurations/create)című témakört. Létezik egy minta runbook is, amely a heti frissítés központi telepítésének létrehozására használható. További információ erről a runbook: [heti frissítési telepítés létrehozása egy vagy több virtuális géphez egy erőforráscsoport esetében](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

### <a name="maintenance-windows"></a>Karbantartási időszakok

A karbantartási időszakok a frissítések telepítésének időtartamát szabályozzák. Karbantartási időszak megadásakor vegye figyelembe a következő adatokat.

* A karbantartási időszakok azt szabályozzák, hogy a rendszer hány frissítést próbáljon telepíteni.
* A Update Management nem állítja le az új frissítések telepítését, ha a karbantartási időszak végére közeledik.
* A Update Management nem szakítja meg a folyamatban lévő frissítéseket, ha a karbantartási időszak túllépve.
* Ha a karbantartási időszakot túllépik a Windowson, gyakran egy szervizcsomag-frissítés miatt sok időt vesz igénybe a telepítés.

### <a name="multi-tenant"></a>Több-bérlős frissítések telepítése

Ha olyan gépekkel rendelkezik, amelyeket egy másik Azure-bérlő jelent a javításhoz szükséges Update Management, akkor a következő megkerülő megoldást kell használnia ahhoz, hogy ütemezve legyen. A [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) parancsmag `-ForUpdate` használatával létrehozhat egy ütemtervet, és használhatja a [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) parancsmagot, és átadhatja a gépeket a másikban a `-NonAzureComputer` (z) paraméter bérlője. A következő példa egy példát mutat be erre:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>Hiányzó frissítések megtekintése

Válassza a **hiányzó frissítések** lehetőséget a gépekből hiányzó frissítések listájának megtekintéséhez. Minden frissítés szerepel a listáján, és kiválasztható. A frissítéshez szükséges gépek számával, az operációs rendszerrel és a további információkra mutató hivatkozással kapcsolatos információk láthatók. A **naplók keresése** panel további részleteket jelenít meg a frissítésekről.

## <a name="view-update-deployments"></a>Frissítések központi telepítésének megtekintése

A meglévő frissítési központi telepítések listájának megtekintéséhez kattintson a **központi telepítések frissítése** fülre. Válassza ki bármelyik frissítési központi telepítést a táblázatban, hogy megnyissa a frissítés központi telepítésének **futtatása** ablaktáblát a központi telepítéshez. A feladatok naplóit legfeljebb 30 napig tároljuk.

![A frissítések telepítésének eredményeinek áttekintése](./media/automation-update-management/update-deployment-run.png)

A REST API frissítésének megtekintéséhez tekintse meg a [szoftverfrissítés-konfiguráció futtatása](/rest/api/automation/softwareupdateconfigurationruns)című témakört.

## <a name="update-classifications"></a>Frissítési besorolások

Az alábbi táblázat a Update Management frissítési besorolásait sorolja fel, az egyes besorolások definíciója szerint.

### <a name="windows"></a>Windows

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Egy adott problémára vonatkozó frissítés, amely kritikus, nem biztonsággal kapcsolatos hibára vonatkozik.        |
|Biztonsági frissítések     | Egy termékre vonatkozó, biztonsággal kapcsolatos probléma frissítése.        |
|Kumulatív frissítések     | Az egyszerű üzembe helyezéshez együtt csomagolt gyorsjavítások összesített készlete.        |
|Funkciócsomagok     | A termék kiadásán kívül terjesztett új termék-szolgáltatások.        |
|Szervizcsomagok     | Egy alkalmazásra alkalmazott gyorsjavítások kumulatív készlete.        |
|A definíciók frissítései     | Vírus-vagy egyéb definíciós fájlok frissítése.        |
|Eszközök     | Olyan segédprogram vagy szolgáltatás, amely egy vagy több feladat elvégzését segíti elő.        |
|Frissítések     | Egy aktuálisan telepített alkalmazás vagy fájl frissítése.        |

### <a name="linux-2"></a>Linux

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Adott probléma vagy termékspecifikus, biztonsággal kapcsolatos probléma frissítései.         |
|Egyéb frissítések     | Minden egyéb olyan frissítés, amely nem kritikus jellegű, vagy nem biztonsági frissítés.        |

A Linux rendszerben a Update Management különbséget tehet a Felhőbeli kritikus és biztonsági frissítések között, miközben a felhőben az adatgazdagítás miatt kiértékeli az értékelést. A javításhoz a Update Management a gépen elérhető besorolási értékekre támaszkodik. A többi disztribúciótól eltérően a CentOS nem rendelkezik ezekkel az információkkal a dobozból. Ha a CentOS-gépek úgy vannak konfigurálva, hogy a következő parancs biztonsági értékeit adja vissza, Update Management a besorolások alapján lesz képes a javításra.

```bash
sudo yum -q --security check-update
```

Jelenleg nincs olyan metódus, amely támogatja a natív besorolást – az adatelérhetőség a CentOS-ben. Jelenleg csak a lehető legjobb támogatást nyújtja azoknak az ügyfeleknek, akik saját maguk engedélyezték ezt.

## <a name="firstparty-predownload"></a>Speciális beállítások

A Update Management Windows Update a Windows-frissítések letöltésére és telepítésére támaszkodik. Ennek eredményeképpen tiszteletben tartjuk a Windows Update által használt beállításokat. Ha a nem Windows rendszerű frissítéseket engedélyező beállításokat használ, akkor a Update Management a frissítéseket is kezeli. Ha szeretné engedélyezni a frissítések letöltését a frissítés központi telepítése előtt, a frissítés központi telepítései gyorsabban és kevésbé valószínű, hogy túllépik a karbantartási időszakot.

### <a name="pre-download-updates"></a>Frissítések előzetes letöltése

A frissítések Csoportházirendban való automatikus letöltésének konfigurálásához beállíthatja az [Automatikus frissítések konfigurálása beállítást](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) **3**értékre. Ezzel letölti a háttérben a szükséges frissítéseket, de nem telepíti őket. Ez Update Management az ütemtervek vezérlésében, de a frissítések letöltésének engedélyezése a Update Management karbantartási időszakon kívül. Ez megakadályozhatja, hogy a **karbantartási** időszak túllépte a hibákat a Update Managementban.

Ezt a PowerShell-lel is beállíthatja, ha egy olyan rendszeren futtatja a következő PowerShell-t, amelyen szeretné automatikusan letölteni a frissítéseket.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>Automatikus telepítés letiltása

Az Azure-beli virtuális gépek alapértelmezés szerint automatikusan telepíthetők a frissítések. Ez azt eredményezheti, hogy a frissítések telepítésének megkezdése előtt telepítenie kell a frissítéseket Update Management. Ezt a viselkedést letilthatja a `NoAutoUpdate` beállításkulcs `1`beállításával. A következő PowerShell-kódrészlet az egyik módját mutatja be.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>Egyéb Microsoft-termékek frissítéseinek engedélyezése

Alapértelmezés szerint a Windows Update csak Windows-frissítéseket biztosít. Ha engedélyezi, **hogy a Windows frissítése során más Microsoft-termékek**is engedélyezzék a frissítéseket, más termékek frissítései is elérhetők, beleértve a SQL Server vagy más, az első féltől származó szoftverek biztonsági javításait is. Ez a beállítás nem konfigurálható Csoportházirend. Futtassa a következő PowerShellt azokon a rendszereken, amelyeken engedélyezni kívánja a más, első féltől származó javítások bekapcsolását, Update Management pedig tiszteletben tartja ezt a beállítást.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a>Harmadik féltől származó javítások Windows rendszeren

A Update Management a helyileg konfigurált frissítési tárházra támaszkodik a támogatott Windows-rendszerek javításához. Ez vagy a WSUS vagy a Windows Update. Az olyan [eszközök](/sccm/sum/tools/updates-publisher
) , mint a System Center Updates Publisher (a frissítések közzétevője) lehetővé teszik egyéni frissítések közzétételét a WSUS szolgáltatásban. Ez a forgatókönyv lehetővé teszi, hogy Update Management a harmadik féltől származó szoftverrel rendelkező frissítési adattárként System Center Configuration Manager használó gépeket. Az Updates Publisher konfigurálásával kapcsolatos további információkért lásd: a [frissítések közzétevő telepítése](/sccm/sum/tools/install-updates-publisher).

## <a name="ports"></a>Hálózati tervezés

A következő címek megadása kifejezetten a Update Management. A címekkel folytatott kommunikáció az 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

Windows rendszerű gépek esetén a Windows Update által igényelt végpontokra is engedélyeznie kell a forgalmat.  A szükséges végpontok frissített listáját a [http/proxy szolgáltatással kapcsolatos problémák](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)között találja. Ha helyi [Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)-kiszolgálóval rendelkezik, engedélyeznie kell a forgalmat a [WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)-kulcsban megadott kiszolgálóra is.

A Red Hat Linux rendszerű gépek esetében tekintse meg a szükséges végpontok számára a [RHUI Content Delivery Servers IP](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) -címei című témakört. Más Linux-disztribúciók esetében tekintse meg a szolgáltatói dokumentációt.

További információ a hibrid Runbook Worker által igényelt portokról: [hibrid feldolgozói szerepkör portjai](automation-hybrid-runbook-worker.md#hybrid-worker-role).

A kivételek meghatározásakor a felsorolt címek használatát javasoljuk. IP-címek esetén letöltheti az [Microsoft Azure adatközpont IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653). A fájl hetente frissül, és tükrözi a jelenleg üzembe helyezett tartományokat és az IP-címtartományok közelgő változásait.

Az Internet-hozzáférés [nélküli számítógépek](../azure-monitor/platform/gateway.md) összekapcsolására vonatkozó utasításokat követve konfigurálja azokat a gépeket, amelyek nem rendelkeznek interneteléréssel.

## <a name="search-logs"></a>Keresés naplókban

A Azure Portalban megadott részletek mellett a naplókon is végezhet keresést. A megoldás oldalain válassza a **log Analytics**lehetőséget. Megnyílik a **napló keresése** panel.

Azt is megtudhatja, hogyan szabhatja testre a lekérdezéseket, és hogyan használhatja őket különböző ügyfelekről, és hogyan teheti őket többek között:  [Log Analytics Search API dokumentációja](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Mintalekérdezések

A következő szakaszokban a megoldás által gyűjtött frissítési rekordokra vonatkozó példákat láthat:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Egyetlen Azure-beli VM-értékelő lekérdezés (Windows)

Cserélje le a VMUUID értéket a lekérdezni kívánt virtuális gép virtuális géphez tartozó GUID azonosítóra. A következő lekérdezés Azure Monitor naplókban való futtatásával megtalálhatja a használni kívánt VMUUID:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Hiányzó frissítések összegzése

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Hiányzó frissítések listája

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>Egyetlen Azure-beli VM-értékelő lekérdezés (Linux)

Egyes Linux-disztribúciók esetében a [endianness](https://en.wikipedia.org/wiki/Endianness) nem egyezik a Azure Resource Manager és a Azure monitor naplókban tárolt VMUUID értékkel. A következő lekérdezés mindkét endianness egyezést keres. Cserélje le a VMUUID értékeket a GUID big-endian és little-endian formátumára, hogy megfelelően visszaadja az eredményeket. A következő lekérdezés Azure Monitor naplókban való futtatásával megtalálhatja a használni kívánt VMUUID:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Hiányzó frissítések összegzése

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Hiányzó frissítések listája

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>Több virtuális gépre kiterjedő értékelési lekérdezések

##### <a name="computers-summary"></a>Számítógépek összegzése

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Hiányzó frissítések összegzése

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="computers-list"></a>Számítógépek listája

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>Hiányzó frissítések listája

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>Dinamikus csoportok használata

A Update Management lehetővé teszi az Azure-beli vagy nem Azure-beli virtuális gépek dinamikus csoportjának megcélzását a frissítések központi telepítéséhez. A rendszer kiértékeli ezeket a csoportokat a központi telepítési időszakban, így nem kell a központi telepítést a gépek hozzáadásához módosítania.

> [!NOTE]
> A frissítés központi telepítésének létrehozásakor megfelelő engedélyekkel kell rendelkeznie. További információ: [Install Updates (frissítések telepítése](#install-updates)).

### <a name="azure-machines"></a>Azure-gépek

Ezeket a csoportokat egy lekérdezés határozza meg, amikor a frissítés központi telepítése megkezdődik, a rendszer kiértékeli a csoport tagjait. A dinamikus csoportok nem működnek a klasszikus virtuális gépekkel. A lekérdezés meghatározásakor a következő elemek együtt használhatók a dinamikus csoport feltöltéséhez.

* Subscription
* Erőforráscsoportok
* Helyek
* Tags

![Csoportok kiválasztása](./media/automation-update-management/select-groups.png)

Egy dinamikus csoport eredményének megtekintéséhez kattintson a **Preview (előnézet** ) gombra. Ez az előzetes verzió megjeleníti a csoporttagság időpontját, ebben a példában a címke **szerepkörrel** rendelkező gépeket keresünk **BackendServer**. Ha további gépek is szerepelnek a címkében, a rendszer hozzáadja azokat az adott csoportra vonatkozó jövőbeli központi telepítésekhez.

![előzetes verziójú csoportok](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>Nem Azure-beli gépek

A nem Azure-beli gépek esetében a mentett keresések, amelyeket számítógépcsoportokként is emlegetik, a dinamikus csoport létrehozásához használatosak. A mentett keresések létrehozásáról további információt a [számítógépcsoport létrehozása](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)című témakörben talál. A csoport létrehozása után kiválaszthatja a mentett keresések listájából. Az **előnézet** gombra kattintva megtekintheti a mentett keresésben lévő számítógépeket.

![Csoportok kiválasztása](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>Integrálás a System Center Configuration Managerrel

A számítógépek, kiszolgálók és mobileszközök kezeléséhez System Center Configuration Manager befektetett ügyfelek is a Configuration Manager erősségét és érettségét használják a szoftverfrissítések kezeléséhez. Configuration Manager a szoftverfrissítés-kezelési (SUM) ciklus részét képezi.

Ha szeretné megtudni, hogyan integrálható a felügyeleti megoldás System Center Configuration Manager használatával, tekintse meg a [System Center Configuration Manager integrálása a Update Management](oms-solution-updatemgmt-sccmintegration.md)használatával című témakört.

## <a name="inclusion-behavior"></a>Integrációs viselkedés

A frissítés befoglalása lehetővé teszi az alkalmazandó frissítések megadását. A csomagban található javítások vagy csomagok telepítve vannak. Ha a javítások vagy csomagok bekerülnek, és egy besorolás van kiválasztva, akkor a befoglalt elemek és a besorolásnak megfelelő elemek is telepítve vannak.

Fontos tudni, hogy a kizárások felülbírálják a belefoglalásokat. Ha például meghatároz egy kizárási szabályt `*`, akkor a rendszer nem telepíti a javításokat és a csomagokat, mivel azok ki vannak zárva. A kizárt javítások továbbra is hiányzóként jelennek meg a gépről. Linux rendszerű gépek esetén, ha csomag van megadva, de a függő csomag ki van zárva, a csomag nincs telepítve.

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

## <a name="remove-a-vm-from-update-management"></a>Virtuális gép eltávolítása Update Management

Virtuális gép eltávolítása Update Managementról:

* A Log Analytics munkaterületen távolítsa el a virtuális gépet a hatókör-konfiguráció `MicrosoftDefaultScopeConfig-Updates`mentett keresésével. A mentett keresések a munkaterület **általános** területén találhatók.
* Távolítsa el a [Microsoft monitoring agentet](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) vagy a [Linux rendszerhez készült log Analytics](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)-ügynököt.

## <a name="next-steps"></a>További lépések

Folytassa az Oktatóanyaggal, amelyből megtudhatja, hogyan kezelheti a Windows rendszerű virtuális gépek frissítéseit.

> [!div class="nextstepaction"]
> [Az Azure Windows rendszerű virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)

* A részletes frissítési információk megtekintéséhez használja a naplóbeli kereséseket [Azure monitor naplókban](../log-analytics/log-analytics-log-searches.md) .
* [Riasztások létrehozása](automation-tutorial-update-management.md#configure-alerts) a központi telepítés állapotának frissítéséhez.

* Ha szeretné megtudni, hogyan kezelheti Update Management a REST API segítségével, tekintse meg a [szoftverfrissítések konfigurációit](/rest/api/automation/softwareupdateconfigurations) ismertető témakört.
* A Update Management hibaelhárításával kapcsolatos további információkért lásd: [hibaelhárítás Update Management](troubleshoot/update-management.md)
