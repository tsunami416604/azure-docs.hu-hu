---
title: Felügyeleti megoldás frissítése az Azure-ban
description: Ez a cikk ismerteti, hogyan használhatja az Azure Update Management megoldás a Windows és Linux gépek frissítései.
services: automation
ms.subservice: update-management
ms.date: 02/27/2020
ms.topic: conceptual
ms.openlocfilehash: eaba4bf7760e150f2477ee743c797f94784b8506
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535502"
---
# <a name="update-management-solution-in-azure"></a>Felügyeleti megoldás frissítése az Azure-ban

Az Azure Automation Frissítéskezelési megoldásával kezelheti az operációs rendszer frissítéseit windowsos és Linux os gépeihez az Azure-ban, a helyszíni környezetekben és más felhőalapú környezetekben. Gyorsan felmérheti az összes ügynökgépen elérhető frissítések állapotát, és kezelheti a kiszolgálókhoz szükséges frissítések telepítésének folyamatát.

A virtuális gépekfrissítés-kezelést a következő módszerekkel engedélyezheti:

- Az [Azure Automation-fiók](automation-onboard-solutions-from-automation-account.md) egy vagy több Azure-gépek és manuálisan nem Azure-gépek.

- Egyetlen Azure-virtuális gép az Azure Portalon a virtuális gép lapjáról. Ez a forgatókönyv [Linux és](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) virtuális gépekhez érhető el.

- [Több Azure-beli virtuális gép](manage-update-multi.md) esetén az Azure Portal virtuális **gépek** lapján való kijelölésükhez. 

> [!NOTE]
> Az Update Management megoldás hoz egy Log Analytics-munkaterületet az Automation-fiókhoz. A támogatott régiók végleges listáját az [Azure Workspace-leképezések című témakörben található.](./how-to/region-mappings.md) A régióleképezések nem befolyásolják a virtuális gépek kezelését az Automation-fióktól egy külön régióban.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Elérhető egy Azure [Resource Manager-sablon,](automation-update-management-deploy-template.md) amely lehetővé teszi az Update Management megoldás üzembe helyezését egy új vagy meglévő Automation-fiókra és a Log Analytics-munkaterületre az előfizetésben.

## <a name="solution-overview"></a>Megoldás áttekintése

Az Update Management által felügyelt gépek a következő konfigurációkat használják az értékelés elvégzéséhez és a központi telepítések frissítéséhez:

* Log Analytics-ügynök Windows vagy Linux rendszerhez
* PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
* Automation hibrid runbook-feldolgozó
* Microsoft Update vagy Windows Server Update Services (WSUS) Windows gépekhez

Az alábbi ábra bemutatja, hogyan értékeli és alkalmazza a biztonsági frissítéseket a munkaterület összes csatlakoztatott Windows Server és Linux rendszerű gépére:

![Felügyeleti folyamat frissítése](./media/automation-update-management/update-mgmt-updateworkflow.png)

Az Update Management ahhoz is használható, hogy ugyanabban a bérlőben több előfizetésben készítse elő natív módon a gépeket.

A csomag kiadása után 2-3 órát vesz igénybe, hogy a javítás megjelenjen a Linux gépek hez az értékeléshez. Windows-gépek esetén 12–15 órát vesz igénybe, amíg a javítás megjelenik a javítás kiértékelése után.

Miután egy gép befejezte a frissítésmegfelelőség vizsgálatát, az ügynök továbbítja az adatokat ömlesztve az Azure Monitor naplóiba. Windows rendszerű gépeken a megfelelőségi vizsgálat alapértelmezés szerint 12 óránként fut.

A vizsgálatütemezés mellett a frissítés megfelelőségének vizsgálata a Log Analytics-ügynök újraindítását követő 15 percen belül, a frissítés telepítése előtt és a frissítés telepítése után is megkezdődik.

Linux-gép esetén a megfelelőségi vizsgálat alapértelmezés szerint óránként történik. Ha a Log Analytics-ügynök újraindul, 15 percen belül megfelelőségi vizsgálat indul.

A megoldás arról számol be, hogy a számítógép mennyire naprakész, az on alapul, hogy milyen forrásból van konfigurálva a szinkronizáláshoz. Ha a Windows-számítógép úgy van beállítva, hogy jelentsen a WSUS-nak, attól függően, hogy a WSUS mikor volt utoljára szinkronizálva a Microsoft Update szolgáltatással, az eredmények eltérhetnek aTtól, amit a Microsoft Update mutat. Ez a viselkedés megegyezik a Linux-gépek, amelyek úgy vannak beállítva, hogy jelentsen egy helyi tárlató helyett egy nyilvános tárlaton.

> [!NOTE]
> A szolgáltatásnak való megfelelő jelentéshez az Update Management bizonyos URL-címek és portok engedélyezését igényli. Ezekről a követelményekről a [Hibrid dolgozók hálózattervezése](automation-hybrid-runbook-worker.md#network-planning).)

A szoftverfrissítéseket egy ütemezett központi telepítés létrehozásával telepítheti és telepítheti azon gépeken, amelyeken frissítéseket kell telepíteni. A *választhatóként* besorolt frissítések nem szerepelnek a Windows-gépek központi telepítési hatókörében. Csak a szükséges frissítések szerepelnek a központi telepítési hatókörben.

Az ütemezett központi telepítés határozza meg, hogy mely célgépek kapják meg a megfelelő frissítéseket. Ezt úgy teszi, hogy explicit módon megad bizonyos gépeket, vagy kiválaszt egy [olyan számítógépcsoportot,](../azure-monitor/platform/computer-groups.md) amely egy adott gépcsoport naplókeresésein alapul (vagy egy [Azure-lekérdezésen,](automation-update-management-query-logs.md) amely dinamikusan választja ki az Azure virtuális gépeket a megadott feltételek alapján). Ezek a csoportok eltérnek [a hatókör konfigurációjától](../azure-monitor/insights/solution-targeting.md), amely csak annak meghatározására szolgál, hogy mely gépek kapják meg a megoldást engedélyező felügyeleti csomagokat.

Megadhatja azt az ütemezést is, amelynek jóváhagyására és beállítására a frissítések telepíthetők. Ezt az időszakot karbantartási időszaknak nevezzük. A karbantartási időszak 20 perces tartománya újraindul, feltételezve, hogy szükség van rá, és kiválasztotta a megfelelő újraindítási lehetőséget. Ha a javítás a vártnál tovább tart, és kevesebb, mint 20 perc van a karbantartási időszakban, újraindítás nem történik meg.

A telepítést az Azure Automation runbookjai végzik. Ezek a runbookok nem tekinthetők meg, és nem igényelnek semmilyen konfigurációt. Frissítési központi telepítés létrehozásakor létrehoz egy ütemezést, amely elindítja a fő frissítési runbook a megadott időpontban a mellékelt gépek. A fő runbook elindítja a gyermek runbook minden ügynök a szükséges frissítések telepítéséhez.

A frissítés központi telepítésében megadott dátum és időpont ban a célgépek párhuzamosan hajtják végre a központi telepítést. A telepítés előtt egy vizsgálat fut, amely ellenőrzi, hogy a frissítések továbbra is szükségesek-e. Wsus ügyfélgépek esetén, ha a frissítések nincsenek jóváhagyva a WSUS szolgáltatásban, a frissítés telepítése sikertelen lesz.

Egy számítógép regisztrálása update Management több Log Analytics-munkaterület (más néven többhoming) nem támogatott.

## <a name="clients"></a>Ügyfelek

### <a name="supported-client-types"></a>Támogatott ügyféltípusok

Az alábbi táblázat a frissítési felmérésekhez támogatott operációs rendszereket sorolja fel. A javításhoz hibrid Runbook-feldolgozó szükséges. A hibrid runbook-feldolgozó követelményeiről a Windows hibrid [runbook-feldolgozó](automation-windows-hrw-install.md) és a [Linux hibrid runbook-feldolgozó](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)telepítésének telepítési útmutatójában talál.

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2019 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016 (Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2(Adatközpont/Standard)<br><br>Windows Server 2012 || 
|Windows Server 2008 R2 (RTM és SP1 szabvány)| Az Update Management csak az operációs rendszer reklényeit támogatja, a javítás nem támogatott, mivel a [Hibrid Runbook Worker](automation-windows-hrw-install.md) nem támogatott a Windows Server 2008 R2 rendszerben. |
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynökök nek hozzáférést kell biztosítaniuk egy frissítési tárházhoz. A besorolásalapú javításhoz olyan biztonsági adatokat kell `yum` visszaadni, amelyekkel a CentOS nem rendelkezik az RTM-kiadásokban. A CentOS besorolás-alapú javításáról a [Besorolások frissítése Linuxon című](automation-view-update-assessments.md#linux-2)témakörben talál további információt.          |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynökök nek hozzáférést kell biztosítaniuk egy frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynökök nek hozzáférést kell biztosítaniuk egy frissítési tárházhoz.        |
|Ubuntu 14.04 LTS, 16.04 LTS és 18.04 (x86/x64)      |A Linux-ügynökök nek hozzáférést kell biztosítaniuk egy frissítési tárházhoz.         |

> [!NOTE]
> Az Azure virtuálisgép-méretezési csoportok az Update Management segítségével kezelhetők. Update Management működik a példányok magukat, és nem az alaplemezképen. A frissítéseket növekményes módon kell ütemezni, hogy ne egyszerre frissüljön a virtuálisgép-példányok frissítése.
> A virtuálisgép-méretezési csoportok csomópontjait hozzáadhatja a [nem Azure-gép alaplapi](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)lépései című területen leírt lépéseket követve.

### <a name="unsupported-client-types"></a>Nem támogatott ügyféltípusok

Az alábbi táblázat a nem támogatott operációs rendszereket sorolja fel:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows-ügyfél     | Az ügyféloperációs rendszerek (például a Windows 7 és a Windows 10) nem támogatottak.        |
|Windows Server 2016 Nano Server     | Nem támogatott.       |
|Az Azure Kubernetes szolgáltatásnodei | Nem támogatott. A [Biztonsági és kernelfrissítések alkalmazása linuxos csomópontokra az Azure Kubernetes szolgáltatásban (AKS)](../aks/node-updates-kured.md) leírt javítási folyamat használata|

### <a name="client-requirements"></a>Ügyfélkövetelmények

Az alábbi információk az operációs rendszerspecifikus ügyfélkövetelményeket ismertetik. További útmutatásért lásd: [Hálózattervezés](#ports).

#### <a name="windows"></a>Windows

A Windows-ügynököket úgy kell konfigurálni, hogy kommunikáljanak a WSUS-kiszolgálóval, vagy hozzá kell férniük a Microsoft Update szolgáltatáshoz.

Az Update Management a Configuration Manager rel is használható. Az integrációs forgatókönyvekről a [Configuration Manager integrálása frissítéskezeléssel (Integrációs kezelő integrálása frissítéskezeléssel)](oms-solution-updatemgmt-sccmintegration.md#configuration)témakörben olvashat bővebben. A [Log Analytics Windows-ügynök](../azure-monitor/platform/agent-windows.md) szükséges. Az ügynök automatikusan települ, ha egy Azure virtuális gép bevezetésre kerül.

Alapértelmezés szerint az Azure Piactérről telepített Windows virtuális gépek automatikus frissítéseket kapnak a Windows Update Szolgáltatástól. Ez a viselkedés nem változik, ha hozzáadja ezt a megoldást, vagy windowsos virtuális gépeket ad hozzá a munkaterülethez. Ha nem kezeli aktívan a frissítéseket ezzel a megoldással, az alapértelmezett viselkedés (a frissítések automatikus alkalmazása esetén) érvényes.

> [!NOTE]
> A felhasználó módosíthatja a csoportházirendet, hogy a számítógép újraindítását csak a felhasználó hajthassa végre, a rendszer ne. A felügyelt gépek elakadhatnak, ha az Update Management nem rendelkezik a számítógép újraindítására vonatkozó jogokkal a felhasználó manuális beavatkozása nélkül.
>
> További információt az [Automatikus frissítések csoportházirend-beállításainak konfigurálása című témakörben talál.](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)

#### <a name="linux"></a>Linux

Linux esetén a gép nek szüksége van egy frissítési tárházelérésére. A frissítési tárház lehet magán- vagy nyilvános. Az Update Management szolgáltatás kezeléséhez a TLS 1.1 vagy TLS 1.2 szükséges. Ezzel a megoldással nem támogatott egy Linux-naplóügynök, amely úgy van beállítva, hogy egynél több Log Analytics-munkaterületnek jelentsen. A gépnek python 2.x-et is telepítenie kell.

A Linux log analytics-ügynök telepítéséről és a legújabb verzió letöltéséről a [Log Analytics-ügynök Linuxhoz](../azure-monitor/platform/agent-linux.md)című témakörben talál további információt. A Windows Log Analytics-ügynök telepítéséről a [Windows-számítógépek csatlakoztatása az Azure Monitorhoz című](../log-analytics/log-analytics-windows-agent.md)témakörben talál további információt.

Az Azure Marketplace-en elérhető, igény szerinti Red Hat Enterprise Linux (RHEL) lemezképekből létrehozott virtuális gépek regisztrálva vannak az Azure-ban üzembe helyezett [Red Hat Update Infrastructure (RHUI)](../virtual-machines/workloads/redhat/redhat-rhui.md) eléréséhez. Minden más Linux-disztribúciót frissíteni kell a disztribúció online fájltárából a disztribúció által támogatott módszerek használatával.

## <a name="permissions"></a>Engedélyek

A frissítési központi telepítések létrehozásához és kezeléséhez speciális engedélyekre van szükség. Ezekről az engedélyekről a [Szerepköralapú hozzáférés – Frissítéskezelés](automation-role-based-access-control.md#update-management-permissions)című témakörben olvashat.

## <a name="solution-components"></a>Megoldás-összetevők

A megoldás a következő erőforrásokból áll. Ezek az erőforrások automatikusan hozzáadódnak az Automation-fiókhoz, amikor engedélyezi a megoldást. 

### <a name="hybrid-worker-groups"></a>Hibridfeldolgozó-csoportok

A megoldás engedélyezése után minden olyan Windows-gép, amely közvetlenül csatlakozik a Log Analytics-munkaterülethez, automatikusan hibrid runbook-feldolgozóként konfigurálva lesz a megoldásban szereplő runbookok támogatásához.

A megoldás által kezelt minden egyes Windows-gép szerepel a **hibrid munkavégző csoportok** ablaktáblán az Automation-fiók **rendszerhibrid munkavégző csoportjaként.** A megoldások a *FQDN_GUID* elnevezési konvenciót használják. Ezek a csoportok nem célozhatók meg runbookokkal a fiókjában. Ha megpróbálja, a kísérlet sikertelen lesz. Ezek a csoportok csak ezt a felügyeleti megoldást támogatják.

A Windows-gépet hozzáadhatja egy hibrid Runbook-feldolgozó csoporthoz az Automation-fiókban az Automation-runbookok támogatásához, ha ugyanazt a fiókot használja mind a megoldáshoz, mind a hibrid Runbook-feldolgozó csoporttagsághoz. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziója része.

### <a name="management-packs"></a>Felügyeleti csomagok

Ha a System Center Operations Manager felügyeleti csoport [egy Log Analytics-munkaterülethez csatlakozik,](../azure-monitor/platform/om-agents.md)a következő felügyeleti csomagok települnek az Operations Manager ben. Ezek a felügyeleti csomagok a megoldás hozzáadása után közvetlenül csatlakoztatott Windows-gépekre is telepítve vannak. A felügyeleti csomagokat nem szükséges konfigurálni vagy felügyelni.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

> [!NOTE]
> Ha egy Operations Manager 1807 vagy 2019 felügyeleti csoport csatlakozik egy Log Analytics-munkaterülethez, amelya felügyeleti csoportban a naplóadatok gyűjtésére konfigurált ügynökökkel rendelkezik, felül kell írnia a következő szabályt az Update Management használatával való kezelésükhöz: Felülbírálja az **IsAutoRegistrationEnabled** paramétert, és **true** értékre van állítva a **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** szabályban.

A megoldáskezelési csomagok frissítéséről az [Operations Manager csatlakoztatása az Azure Monitor naplóihoz](../azure-monitor/platform/om-agents.md)című témakörben talál további információt.

> [!NOTE]
> Az Operations Manger ügynökkel rendelkező gépek esetében az Update Management teljes körű en kell kezelnie az ügynököt a Windows vagy Linux Log Analytics-ügynökre. Az ügynök frissítésének módjáról az [Operations Manager-ügynök frissítése](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)című témakörben olvashat. Az Operations Managert használó környezetekben a System Center Operations Manager 2012 R2 14 vagy újabb műveletkezelőt kell futtatnia.

## <a name="data-collection"></a>Adatgyűjtés

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A megoldás információkat gyűjt a rendszerfrissítésekről a Windows-ügynököktől, majd elindítja a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A megoldás információkat gyűjt a rendszerfrissítésekről a Linux-ügynököktől, majd elindítja a szükséges frissítések telepítését a támogatott disztribúciókon. |
| Az Operations Manager felügyeleti csoportja |Igen |A megoldás információt szerez be a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről.<br/><br/>Nincs szükség közvetlen kapcsolatra az Operations Manager-ügynök és az Azure Monitor-naplók között. Az adatok továbbítása a felügyeleti csoportból a Log Analytics munkaterületre. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

A vizsgálat naponta kétszer minden felügyelt Windows-gépen történik. 15 percenként a Windows API-t kéri kretmora az utolsó frissítési időpontra, hogy megállapítsa, megváltozott-e az állapot. Ha az állapot megváltozott, megfelelőségi vizsgálat indul.

Minden egyes felügyelt Linux-gépóránként végez egy vizsgálatot.

Az irányítópult 30 perc és 6 óra között is megjelenítheti a felügyelt gépek frissített adatait.

Az Azure Monitor naplóinak átlagos adatforgalma az Update Management használatával körülbelül 25 megabájt (MB) havonta. Ez az érték csak közelítés, és a környezettől függően változhat. Javasoljuk, hogy figyelje a környezetet, hogy nyomon követhesse a pontos használatot. Az adathasználat elemzéséről a [Használat és a költség kezelése című](../azure-monitor/platform/manage-cost-storage.md)témakörben talál további információt.

## <a name="network-planning"></a><a name="ports"></a>Hálózattervezés

A következő címek re van szükség kifejezetten a frissítés-kezelés. Az e címekhez való kommunikáció a 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

Windows-gépek esetén engedélyeznie kell a Windows Update által igényelt végpontokra irányuló forgalmat is. A szükséges végpontok frissített listáját a [HTTP/Proxy protokollal kapcsolatos problémák](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)között találja. Ha helyi [Windows Update kiszolgálóval](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)rendelkezik, engedélyeznie kell a [WSUS-kulcsban](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)megadott kiszolgálóra irányuló forgalmat is.

Red Hat Linux-gépek esetén tekintse meg [az RHUI tartalomkézbesítési kiszolgálók IP-kiszolgálóit a](../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) szükséges végpontok ért. Más Linux-disztribúciók esetén tekintse meg a szolgáltató dokumentációját.

A hibrid runbook-feldolgozóhoz szükséges portokról a [Hibrid feldolgozó szerepkör-portok című témakörben](automation-hybrid-runbook-worker.md#hybrid-worker-role)talál további információt.

Azt javasoljuk, hogy a kivételek definiálásakor használja a felsorolt címeket. IP-címek esetén letöltheti a [Microsoft Azure Datacenter IP-tartományait.](https://www.microsoft.com/download/details.aspx?id=41653) Ez a fájl hetente frissül, és tükrözi a jelenleg telepített tartományokat és az IP-tartományok közelgő módosításait.

Kövesse az [Internet-hozzáféréssel nem rendelkező számítógépek csatlakoztatása](../azure-monitor/platform/gateway.md) című részben található utasításokat az internet-hozzáféréssel nem rendelkező gépek konfigurálásához.

## <a name="update-classifications"></a>Frissítési besorolások

Az alábbi táblázatok az Update Management frissítési besorolásait sorolják fel, az egyes besorolásokhoz egy-egy definícióval.

### <a name="windows"></a>Windows

|Osztályozás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Egy adott probléma frissítése, amely egy kritikus, nem biztonsággal kapcsolatos hibát szüntet meg.        |
|Biztonsági frissítések     | Termékspecifikus, biztonsággal kapcsolatos probléma frissítése.        |
|Kumulatív frissítések     | A könnyű telepítés érdekében összecsomagolt gyorsjavítások összesítő készlete.        |
|Funkciócsomagok     | A termékkiadáson kívül terjesztett új termékfunkciók.        |
|Szervizcsomagok     | Az alkalmazásra alkalmazott gyorsjavítások összesítő készlete.        |
|Definíciófrissítések     | Vírus- vagy más definíciós fájlok frissítése.        |
|Eszközök     | Olyan segédprogram vagy szolgáltatás, amely egy vagy több feladat elvégzését segíti.        |
|Frissítések     | Jelenleg telepített alkalmazás vagy fájl frissítése.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Osztályozás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Frissítések egy adott problémára vagy egy termékspecifikus, biztonsággal kapcsolatos problémára.         |
|Egyéb frissítések     | Minden más olyan frissítés, amely nem kritikus jellegű, vagy nem biztonsági frissítés.        |

Linux esetén az Update Management különbséget tud tenni a kritikus frissítések és a biztonsági frissítések között a felhőben, miközben a felhőben az adatok dúsítása miatt értékelési adatokat jelenít meg. A javításhoz az Update Management a számítógépen elérhető besorolási adatokra támaszkodik. Más disztribúciókkal ellentétben a CentOS nem rendelkezik ezekkel az információkkal az RTM verzióban. Ha a CentOS-gépek úgy vannak konfigurálva, hogy biztonsági adatokat adjanak vissza a következő parancshoz, az Update Management a besorolások alapján javíthat.

```bash
sudo yum -q --security check-update
```

Jelenleg nincs támogatott módszer a natív besorolási adatok rendelkezésre állásának engedélyezésére a CentOS rendszeren. Jelenleg csak a legjobb támogatást nyújtjuk azoknak az ügyfeleknek, akik ezt önmagukban is engedélyezték. 

A Red Hat Enterprise 6-os verziójának frissítéseinek besorolásához telepítenie kell a yum-security bővítményt. A Red Hat Enterprise Linux 7-en a plugin már része a yumnak, nincs szükség a telepítésre. További információt a Red Hat [következő tudásbáziscikkében](https://access.redhat.com/solutions/10021)talál.

## <a name="integrate-with-configuration-manager"></a>Integrálás a Configuration Managerrel

Azok az ügyfelek, akik a Microsoft Endpoint Configuration Manager beruházta a számítógépek, kiszolgálók és mobileszközök kezelésére, a Configuration Manager erősségére és érettségére is támaszkodnak, hogy segítsék őket a szoftverfrissítések kezelésében. A Configuration Manager a szoftverfrissítés-kezelési (SUM) ciklus része.

A felügyeleti megoldás konfigurációkezelővel való integrálásáról a [Configuration Manager integrálása frissítéskezeléssel](oms-solution-updatemgmt-sccmintegration.md)című témakörben olvashat.

### <a name="third-party-updates-on-windows"></a>Külső frissítések a Windows rendszeren

Az Update Management a támogatott Windows-rendszerek frissítéséhez a helyileg konfigurált frissítési tárházra támaszkodik. Ez vagy a WSUS vagy a Windows Update. Az olyan eszközök, mint a [System Center Updates Publisher](https://docs.microsoft.com/configmgr/sum/tools/updates-publisher) (Updates Publisher) lehetővé teszik az egyéni frissítések importálását és közzétételét a WSUS szolgáltatással. Ez a forgatókönyv lehetővé teszi, hogy az Update Management frissítse azon gépeket, amelyek a Configuration Managert használják frissítési tárházként külső szoftverekkel. A Frissítések gyártójának konfigurálásáról a [Frissítések közzétevő telepítése című](https://docs.microsoft.com/configmgr/sum/tools/install-updates-publisher)témakörben olvashat.

## <a name="enable-update-management"></a><a name="onboard"></a>Frissítéskezelés engedélyezése

A rendszerek frissítésének megkezdéséhez engedélyeznie kell az Update Management megoldást. A megoldás alaplapozására ajánlott és támogatott módszerek a következők:

- [Virtuális gépről](automation-onboard-solutions-from-vm.md)

- [Több gép böngészéséből](automation-onboard-solutions-from-browse.md)

- [Az Automation-fiókból](automation-onboard-solutions-from-automation-account.md)

- [Egy Azure Automation runbook](automation-onboard-solutions.md)

- [Azure Resource Manager-sablonnal](automation-update-management-deploy-template.md)

## <a name="next-steps"></a>További lépések

Tekintse át az Azure Automation [GYIK-et](automation-faq.md) a megoldással kapcsolatos gyakori kérdések áttekintéséhez.
