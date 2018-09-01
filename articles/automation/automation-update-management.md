---
title: Frissítéskezelési megoldás az Azure-ban
description: Ebből a cikkből megismerheti, hogyan használható az Azure Update Management megoldás a Windows és Linux rendszerű számítógépek frissítéseinek kezelésére.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 08/29/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 79c78da3ba3102f41a1bd623c6844f064699b30a
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382099"
---
# <a name="update-management-solution-in-azure"></a>Frissítéskezelési megoldás az Azure-ban

A frissítéskezelési megoldás az Azure Automation segítségével operációs rendszer frissítéseinek kezelése az Azure-ban, a helyszíni környezetben vagy az egyéb felhőszolgáltatók üzembe helyezett Windows és Linux számítógépek. Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és felügyelhető a kiszolgálók szükséges frissítéseinek telepítése is.

Az Update Management a virtuális gépek közvetlenül az Azure Automation-fiókjából engedélyezheti. Az Update Management engedélyezése a virtuális gépek az Automation-fiókjából kapcsolatban lásd: [több virtuális gép frissítéseinek kezelése](manage-update-multi.md). Az Update Management egyetlen virtuális gép az Azure Portalon a virtuális gép panelről is engedélyezheti. Ebben a forgatókönyvben érhető el a [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) és [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuális gépeket.

## <a name="solution-overview"></a>Megoldás áttekintése

Update Management által felügyelt számítógépekre, hajtsa végre az értékelési és frissítéstelepítések a következő konfigurációk használatával:

* A Microsoft Monitoring Agent (MMA) for Windows vagy Linux rendszeren
* PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
* Automation hibrid runbook-feldolgozó
* A Microsoft Update vagy Windows Server Update Services (WSUS) Windows-számítógépekhez

Az alábbi ábrán látható egy koncepcióvázlaton jelenítik működését és adatfolyamait, hogy az a megoldás hogyan értékeli és alkalmazza a biztonsági frissítéseket az összes csatlakoztatott Windows Server és Linux rendszerű számítógépek egy adott munkaterület:

![Frissítéskezelési folyamatdiagramja](media/automation-update-management/update-mgmt-updateworkflow.png)

Az Update Management segítségével natív módon előkészítheti a gépeket ugyanabban a bérlőben több előfizetésben található. Egy másik bérlőben, be kell vezetnie a gépek kezeléséhez, azokat [nem Azure-gépek](automation-onboard-solutions-from-automation-account.md#onboard-a-non-azure-machine).

Miután a számítógép frissítési megfelelőség szempontjából vizsgálatot végez, az ügynök továbbítja az adatokat tömeges az Azure Log Analyticshez való. A Windows-számítógépen a megfelelőségi vizsgálat 12 óránként történik alapértelmezés szerint.

Az ütemezett vizsgálat mellett a frissítési megfelelőség szempontjából vizsgálatot kezdeményez frissítés telepítése előtt, ha az MMA újraindítása után 15 percen belül és frissítés telepítése után.

Linux rendszerű számítógépen a megfelelőségi vizsgálat három óránként történik alapértelmezés szerint. Az MMA-ügynök újraindítása, ha a megfelelőségi vizsgálatot kezdeményez 15 percen belül.

Jelentések, hogy mennyire naprakész a számítógép az alapján, hogy milyen forrást úgy van konfigurálva, hogy a megoldás szinkronizálni. Ha a Windows-számítógép van konfigurálva, hogy a WSUS, attól függően, ha a WSUS a legutóbbi szinkronizálásra a Microsoft Update, az eredmények eltérőek lehetnek a Microsoft Updates jeleníti meg. Ez megegyezik a Linux rendszerű számítógépek, amelyek a nyilvános adattár helyett helyi adattárnak való jelentésre vannak konfigurálva.

> [!NOTE]
> Hogy a szolgáltatás megfelelően jelentést, az Update Management igényel, bizonyos URL-címeket és portokat engedélyezni kell. Ezekkel a követelményekkel kapcsolatos további tudnivalókért lásd: [hálózati hibrid feldolgozók tervezése](automation-hybrid-runbook-worker.md#network-planning).

A szoftverfrissítések központi telepítéséhez vagy telepítéséhez létrehozhat egy ütemezett üzembe helyezést a frissítést igénylő számítógépeken. Besorolású frissítések *nem kötelező* nem része a Windows-számítógépek esetében az üzembe helyezés hatálya. Csak a szükséges frissítéseket az üzembe helyezés hatálya szerepelnek. 

Az ütemezett telepítés határozza meg, mely célszámítógépek kapni az alkalmazható frissítéseket, vagy explicit módon adja meg a számítógépeket, vagy kiválasztásával egy [számítógépcsoport](../log-analytics/log-analytics-computer-groups.md) , amelyek naplókeresésekkel, a számítógépek adott halmazára alapul. Is megad egy ütemezés jóváhagyására és kijelölésére a frissítések telepítése során, amelyek adott időszakban.

A telepítést az Azure Automation runbookjai végzik. A runbookok nem tekinthetők, és a runbookok nem igényelnek semmilyen konfigurálást. Frissítéstelepítés létrehozásakor a központi telepítési ütemezés, amely a megadott időben az érintett számítógépekre irányuló frissítési mester runbookot elindítja hoz létre. A mester runbook egy gyermek runbookot indít az egyes ügynököket, hajtsa végre a szükséges frissítések telepítését.

A dátum és a frissítés központi telepítésben megadott időpontban a célszámítógépek a központi telepítést végre párhuzamosan. A telepítés előtt történik vizsgálat és ellenőrizze, hogy a frissítések továbbra is szükséges. A WSUS-ügyfélszámítógépek Ha a frissítések WSUS, a nem jóváhagyott a frissítés telepítése sikertelen lesz.

## <a name="clients"></a>Ügyfelek

### <a name="supported-client-types"></a>Támogatott ügyfél típusú

Az alábbi táblázat bemutatja a támogatott operációs rendszerek listáját:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|A Windows Server 2008, Windows Server 2008 R2 RTM    | Támogatja az értékelések csak frissíteni.         |
|A Windows Server 2008 R2 SP1 és újabb verziók     |.NET-keretrendszer 4.5-ös vagy újabb szükség. ([Töltse le a .NET-keretrendszer](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0-s vagy újabb szükség. ([Töltse le a WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 megbízhatóság növelése érdekében ajánlott.  ([Töltse le a WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz. "Yum" besorolásalapú javítás szükséges, amely CentOS nem rendelkezik beépített biztonsági adatokat adja vissza.         |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 14.04 LTS, 16.04 LTS (x86/x64) és      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

### <a name="unsupported-client-types"></a>Nem támogatott ügyfélalkalmazás típusa

Az alábbi táblázat a nem támogatott operációs rendszerek:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows-ügyfél     | Ügyféloldali operációs rendszerek (például Windows 7 és Windows 10-es) nem támogatottak.        |
|A Windows Server 2016 Nano Server     | Nem támogatott.       |

### <a name="client-requirements"></a>Ügyfélkövetelmények

#### <a name="windows"></a>Windows

Windows-ügynökök egy WSUS-kiszolgálóval való kommunikációhoz kell konfigurálni, vagy azok a Microsoft Update hozzáféréssel kell rendelkeznie. A frissítéskezelés a System Center Configuration Managerrel használható. Integrációs forgatókönyvek kapcsolatos további információkért lásd: [integrálása System Center Configuration Managerben az Update Management](oms-solution-updatemgmt-sccmintegration.md#configuration). A [Windows-ügynök](../log-analytics/log-analytics-agent-windows.md) megadása kötelező. Ha Ön egy Azure virtuális gép előkészítése a rendszer automatikusan telepíti az ügynököt.

#### <a name="linux"></a>Linux

A Linux rendszerre a gép hozzáféréssel kell rendelkeznie valamely frissítési tárházhoz. A frissítés tárház lehet nyilvános vagy privát. A TLS 1.1 és TLS 1.2 használatával kommunikálhat az Update Management szükséges. Az Operations Management Suite (OMS) ügynök Linux rendszerekre, amely több Log Analytics-munkaterületnek való jelentésre van konfigurálva a megoldás nem támogatott.

A Linuxhoz készült OMS-ügynök telepítése és a legújabb verzió letöltéséhez kapcsolatos információkért lásd: [Operations Management Suite Linux-ügynök](https://github.com/microsoft/oms-agent-for-linux). A Windows OMS ügynök telepítésével kapcsolatos információkért lásd: [Operations Management Suite ügynök a Windows](../log-analytics/log-analytics-windows-agent.md).

## <a name="permissions"></a>Engedélyek

Hozhat létre, és a frissítés központi telepítések felügyeletéhez szükséges, konkrét engedélyek szükségesek. Ezekkel az engedélyekkel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés - Update Management](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Megoldás-összetevők

A megoldás a következő erőforrásokból áll. Az erőforrások, az Automation-fiók hozzáadása. Azok, vagy közvetlenül csatlakoztatott ügynökök vagy egy Operations Manager csatlakoztatott felügyeleti csoportban.

### <a name="hybrid-worker-groups"></a>Hibridfeldolgozó-csoportok

Miután engedélyezi ezt a megoldást, bármely Windows-számítógép, amely közvetlenül csatlakozik a Log Analytics-munkaterület konfigurálása automatikusan történik, a hibrid Runbook-feldolgozó telepítésben ebben a megoldásban lévő runbookok támogatása érdekében.

Minden Windows-számítógépen, a megoldás által felügyelt szerepel-e a **hibrid feldolgozócsoportok** alakban egy **rendszer hibrid feldolgozócsoport** az Automation-fiókhoz. A megoldások használata az elnevezési konvenciót *gazdanév FQDN_GUID*. Runbookok ezeket a csoportokat nem célozhatja-fiókjában található. Ha megpróbálja meghiúsulnak. Ezek a csoportok célja, hogy csak a felügyeleti megoldást támogatják.

A Windows-számítógépeket adhat hozzá egy hibrid Runbook-feldolgozó csoporthoz az Automation-fiókban az Automation-runbookok támogatása, ha a megoldás és a hibrid Runbook-feldolgozó csoport tagsága is ugyanazt a fiókot használja. Ez a funkció a hibrid Runbook-feldolgozó 7.2.12024.0-s verziójától hozzáadva.

### <a name="management-packs"></a>Felügyeleti csomagok

Ha a System Center Operations Manager felügyeleti csoportban a Log Analytics-munkaterülethez van csatlakoztatva, a következő felügyeleti csomagokat az Operations Manager vannak telepítve. Ezek a felügyeleti csomagok is telepítve lesz a közvetlenül csatlakoztatott Windows-számítógépeken a megoldás hozzáadását követően. Nem kell konfigurálni vagy felügyelni ezeket a felügyeleti csomagokat.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

Hogyan megoldás felügyeleti csomagok frissítéseivel kapcsolatos további információkért lásd: [Operations Manager csatlakoztatása a Log Analytics](../log-analytics/log-analytics-om-agents.md).

> [!NOTE]
> Azon rendszerek esetében az Operations Manager ügynök az Update Management, teljes mértékben felügyeltek lehessen az ügynökét frissíteni kell a Microsoft Monitoring Agent. Az ügynök frissítésével kapcsolatban lásd: [Operations Manager-ügynök frissítése](/system-center/scom/deploy-upgrade-agents).

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Győződjön meg róla, hogy nem Azure-beli gépek előkészítve

Győződjön meg arról, hogy közvetlenül csatlakoztatott gépek kommunikál a Log Analytics, néhány perc múlva futtathatja egyet a következő naplókeresési mintákat.

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

A Windows-számítógépen tekintse át, ellenőrizze az ügynök kapcsolatot a Log Analytics használatával a következő információkat:

1. A Vezérlőpulton nyissa meg a **Microsoft Monitoring Agent**. Az a **Azure Log Analytics** fülön az ügynök a következő üzenettel: **a Microsoft Monitoring Agent sikeresen csatlakozott a Log Analytics**.
2. Nyissa meg a Windows eseménynaplóban. Lépjen a **alkalmazás- és Services Logs\Operations Manager** , és keresse meg az eseményt azonosító 3000 és a forrás-esemény azonosítója 5002 **Service Connector**. Ezek az események azt jelzik, hogy a számítógép regisztrálva van a Log Analytics-munkaterületet, és konfigurációt kap.

Ha az ügynök nem tud kommunikálni a Log Analytics és az ügynök kommunikáljon az internettel egy tűzfalon vagy proxykiszolgálón keresztül van konfigurálva, győződjön meg arról, hogy a tűzfal vagy proxy server megfelelően van-e konfigurálva. Győződjön meg arról, hogy megfelelően van-e konfigurálva a tűzfal vagy proxy-kiszolgáló kezelésével kapcsolatos információkért lásd: [Windows ügynök hálózati konfigurációja](../log-analytics/log-analytics-agent-windows.md) vagy [Linux-ügynök hálózati konfigurációja](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Ha Linux rendszerei való kommunikációhoz proxykiszolgáló beállítva vagy az OMS-átjárót, és máris bevezetése a megoldást, frissítse a *proxy.conf* engedélyeket biztosítson az omiuser csoportnak olvasási engedélyt a fájlhoz a következő használatával parancsok:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Újonnan hozzáadott Linux-ügynökök állapota **frissített** értékelés végrehajtását követően. A folyamat akár hat órát is igénybe vehet.

Ellenőrizheti, hogy az Operations Manager felügyeleti csoport kommunikál a Log Analytics [ellenőrzése az Operations Manager Log Analytics-integráció](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-log-analytics).

## <a name="data-collection"></a>Adatgyűjtés

### <a name="supported-agents"></a>Támogatott ügynökök

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A megoldás begyűjti a Windows ügynököktől a rendszerfrissítésekről szóló információkat, és ezután kezdeményezi a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A megoldás Linux-ügynököktől a rendszerfrissítésekről szóló információkat gyűjti, és ezután kezdeményezi a támogatott disztribúciókon szükséges frissítések telepítését. |
| Az Operations Manager felügyeleti csoportja |Igen |A megoldás információt szerez be a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről.<br/>A Log Analyticshez való közvetlen kapcsolat legyen az Operations Manager-ügynököt nem szükséges. Adatok lesznek továbbítva a felügyeleti csoportból a Log Analytics-munkaterületet. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

Felügyelt Windows-számítógépek esetében naponta kétszer történik vizsgálat. 15 percenként, a Windows API hívása az utolsó frissítésének időpontját, hogy módosultak-e az állapot meghatározására a lekérdezéshez. Ha a állapota megváltozott, a megfelelőségi vizsgálatot kezdeményez. 

Vizsgálat három óránként történik, az egyes által felügyelt Linux-számítógép.

30 perc és a felügyelt számítógépekből származó frissített adatok megjelennek az irányítópulton 6 óráig is eltarthat.

## <a name="viewing-update-assessments"></a>A frissítési felmérések megtekintése

Válassza ki az Automation-fiók **az Update Management** a gépek állapotának megtekintéséhez.

Ez a nézet nyújt azokról a számítógépekről hiányzó frissítéseket, a frissítéstelepítések és az ütemezett frissítéstelepítések. Az a **megfelelőségi oszlop**, láthatja, hogy az utolsó időpont, a gép lett értékelve. Az a **frissítése ÜGYNÖK KÉSZÜLTSÉGE** oszlopban látható, ha az update-ügynök állapotát. Ha probléma van, válassza ki a hivatkozásra kattintva nyissa meg a hibaelhárítási dokumentáció, amelyek segítségével megtudhatja, milyen lépéseket kell tennie a probléma.

Egy Naplókeresés futtatásához, amely a gép, frissítés vagy telepítés kapcsolatos információkat ad vissza, válassza ki a elemet a listában. A **naplóbeli keresés** panel megnyílik a kiválasztott elem egy lekérdezést:

![Frissítéskezelés alapértelmezett nézet](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>Frissítések telepítése

Frissítések értékelni az összes Linux és Windows számítógéphez a munkaterületen, után szükséges frissítések létrehozásával telepíthet egy *frissítéstelepítés*. A frissítéstelepítések egy vagy több számítógép szükséges frissítéseinek ütemezett telepítése. Megadhatja, hogy az érintett foglalandó dátuma és időpontja az üzembe helyezés és a egy számítógép vagy számítógépek csoportja. A számítógépcsoportokkal kapcsolatos további információkért tekintse meg a [Log Analytics számítógépcsoportjaival](../log-analytics/log-analytics-computer-groups.md) kapcsolatos részt.

 Ha számítógépcsoportok szerepelnek a, csoporttagság ütemezés létrehozása idején már csak egyszer lesz kiértékelve. A csoportot érintő későbbi változások nem tükrözi. Ennek megoldásához törölje az ütemezett frissítéstelepítést, és hozza létre újból.

> [!NOTE]
> Windows virtuális gépek alapértelmezés szerint az Azure Marketplace-ről üzembe helyezett beállítása automatikusan frissítéseket kapjanak a Windows Update szolgáltatás. Ez a viselkedés nem változik, ez a megoldás hozzáadásakor, vagy Windows virtuális gépek felvétele a munkaterülethez. Ha ez a megoldás segítségével aktívan frissítések nem kezeli, az alapértelmezett viselkedést (automatikusan alkalmazza a frissítéseket) vonatkozik.

Frissítések alkalmazása folyamatban, az ubuntu rendszeren karbantartási időszakon kívül elkerüléséhez konfigurálja újra az Unattended-Upgrade csomagot az automatikus frissítések letiltásához. A csomag konfigurálásával kapcsolatos további információkért lásd: [az Ubuntu Server útmutatójának automatikus frissítések témakörében](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Érhetők el az Azure piactéren elérhető igény szerinti Red Hat Enterprise Linux (RHEL) rendszerképekből létrehozott virtuális gépek regisztrálva vannak a hozzáférést a [Red Hat frissítési infrastruktúrához (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) , amely az Azure-ban üzemel. Más Linux-disztribúció a következő támogatott módszerek a terjesztési kell frissíteni a terjesztési online fájl adattárból.

## <a name="view-missing-updates"></a>Hiányzó frissítések megtekintése

Válassza ki **hiányzó frissítések** , amely a gépekről hiányoznak frissítések listájának megtekintéséhez. Minden egyes frissítés szerepel a listán, és a választható ki. További információ a frissítést, az operációs rendszer és a egy hivatkozást igénylő gépek száma kapcsolatos információk jelennek meg. A **naplóbeli keresés** panelen a frissítésekkel kapcsolatos további részleteket jeleníti meg.

## <a name="view-update-deployments"></a>Frissítéstelepítések megtekintése

Válassza ki a **Frissítéstelepítések** fülre kattintva megtekintheti a frissítéstelepítések listáját. Válassza ki a központi telepítések valamelyik megnyitásához a táblázatban a **központi telepítés futtatása frissítés** adott frissítéstelepítés panelje.

![Frissítéstelepítési eredmények áttekintése](./media/automation-update-management/update-deployment-run.png)

## <a name="create-or-edit-an-update-deployment"></a>Hozzon létre vagy frissítéstelepítés szerkesztése

Hozzon létre egy új frissítéstelepítést, jelölje be **frissítések központi telepítésének ütemezése**. A **új frissítéstelepítés** panel nyílik meg. Adja meg az értékeket az alábbi táblázatban leírt tulajdonságokkal, és kattintson a **létrehozás**:

| Tulajdonság | Leírás |
| --- | --- |
| Name (Név) |A frissítéstelepítést beazonosító egyedi név. |
|Operációs rendszer| Linux vagy Windows|
| Frissítendő gépek |Válassza ki, mentett keresést, importált csoporthoz, vagy a legördülő listából válassza ki a gépet, és válassza ki az egyes gépek. Ha úgy dönt, **gépek**, jelenik meg a gép készen áll-e a **frissítési ÜGYNÖK KÉSZÜLTSÉGE** oszlop.</br> Számítógépcsoportok létrehozását a Log Analytics különböző módszereivel kapcsolatos további információkért lásd: [Log Analytics számítógépcsoportjaival](../log-analytics/log-analytics-computer-groups.md) |
|Frissítési besorolások|Válassza ki az összes szükséges|
|Kihagyandó frissítések|Adja meg a kihagyandó frissítések. Windows adja meg a KB, a "KB" előtag nélkül. A Linux rendszerre adja meg a csomag nevét, vagy használjon helyettesítő.  |
|Ütemezési beállítások|Válassza ki az időpontot, elindításához, és válassza ki bármelyik egyszer, vagy az ismétlődés ismétlődés|
| Karbantartási időszak |Frissítések beállított percek száma. Az érték lehet nem lehet kisebb, mint 30 perc és legfeljebb 6 óra |
| Vezérlő újraindítása| Azt határozza meg, hogyan újraindítások kell kezelni. Az elérhető lehetőségek:</br>Újraindítás szükség esetén (alapértelmezett beállítás)</br>Mindig induljon újra</br>Soha ne induljon újra</br>Csak újraindítás – frissítések nem lesznek telepítve|

## <a name="update-classifications"></a>Frissítési besorolások

Az alábbi táblázatok sorolják fel a frissítési besorolások az Update Management, minden egyes olyan definícióval.

### <a name="windows"></a>Windows

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Egy frissítés egy adott probléma, amely kritikus, nem biztonsági hiba.        |
|Biztonsági frissítések     | Egy frissítés egy termékspecifikus biztonsági problémára.        |
|Kumulatív frissítések     | Az egyszerű telepítés együtt csomagolt gyorsjavítások összesített csoportja.        |
|Funkciócsomagok     | Termékkiadáson kívül terjesztett új termékfunkciók.        |
|Szervizcsomagok     | Gyorsjavítások alkalmazáshoz összesített csoportja.        |
|Definíciófrissítések     | Vírus vagy egyéb definíciós fájlok frissítése.        |
|Eszközök     | Segédprogramok vagy funkciók teljes egy vagy több feladatot.        |
|Frissítések     | Egy alkalmazás vagy a jelenleg telepített fájl frissítése.        |

### <a name="linux"></a>Linux

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Frissítéseket egy meghatározott problémára vagy egy termékspecifikus biztonsági problémára.         |
|Egyéb frissítések     | Minden egyéb frissítések, amelyek nem kritikus fontosságú jellegűek, vagy biztonsági frissítések nem.        |

A Linux rendszerre, az Update Management tudja megkülönböztetni kritikus és biztonsági frissítések értékelési adatokat, mert a kibővített adatok megjelenítése a felhőben közben a felhőben. A javítás, az Update Management besorolási adatok a gépen elérhető támaszkodik. Ellentétben más terjesztésekről, a CentOS nem rendelkezik ezekkel az információkkal elérhető beépített. Ha CentOS gépekről úgy, hogy a következő parancsot a biztonsági adatokat adja vissza, az Update Management fogja tudni javítás besorolások alapján.

```bash
sudo yum -q --security check-update
```

Jelenleg nem támogatott metódus metódus natív osztályozás – adatok rendelkezésre állását, a CentOS engedélyezése. Jelenleg csak legjobb támogatás, akiknek előfordulhat, hogy engedélyezve van ez a saját áll rendelkezésre.

## <a name="ports"></a>Portok

A következő címekre kifejezetten az Update Management szükségesek. Ezek a címek kommunikációt a 443-as porton keresztül történik.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

A hibrid Runbook-feldolgozó által igényelt portokat kapcsolatos további információkért lásd: [hibrid feldolgozói szerepkör portok](automation-hybrid-runbook-worker.md#hybrid-worker-role).

Javasoljuk, hogy a címek kivételek meghatározásakor. Az IP-címeket, letöltheti a [a Microsoft Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). Ez a fájl-címlistája hetente frissül, és tükrözi a jelenleg üzembe helyezett tartományokat és minden jövőbeni változtatásokról, az IP tartományokat.

## <a name="search-logs"></a>Keresés naplókban

Mellett a részleteit, amelyet az Azure Portalon szemben a naplókban keresést végezhet. Válassza ki a megoldás lapokon **Log Analytics**. A **naplóbeli keresés** panel nyílik meg.

Emellett megismerheti a lekérdezések testreszabásához, vagy különböző ügyfelek és több funkcionáló használják őket: [Log Analytics-értesítés API keresése dokumentációját](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>Mintalekérdezések

Az alábbi szakaszok nyújtanak naplózási mintalekérdezések Ez a megoldás által összegyűjtött frissítési rekordokkal kapcsolatos:

#### <a name="single-azure-vm-assessment-queries-windows"></a>Egyetlen Azure Virtuálisgép-kiértékelés lekérdezések (Windows)

A VMUUID értékét cserélje le a virtuális gép kérdez le a virtuális gép GUID Azonosítóját. A Log Analytics a következő lekérdezés futtatásával használandó VMUUID találhatja meg: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Hiányzó frissítések összegzése

```
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>Hiányzó frissítések listája

```
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

#### <a name="single-azure-vm-assessment-queries-linux"></a>Egyetlen Azure virtuális gépek értékelése lekérdezések (Linux)

Az egyes Linux-disztribúciók egy [bájtsorrend](https://en.wikipedia.org/wiki/Endianness) eltérés a VMUUID értékkel, amely az Azure Resource Manager és a Log Analytics tárolja származik. A következő lekérdezés vagy bájtsorrend egyeztetés ellenőrzi. A VMUUID értékeket cserélje le a GUID-megfelelően a eredményeket adja vissza csökkenő helyiértékű és növekvő bájtsorrendű formátumát. A Log Analytics a következő lekérdezés futtatásával használandó VMUUID találhatja meg: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>Hiányzó frissítések összegzése

```
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>Hiányzó frissítések listája

```
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

#### <a name="multi-vm-assessment-queries"></a>Több virtuális gépre kiterjedő értékelés lekérdezések

##### <a name="computers-summary"></a>Számítógépek összegzése

```
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
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeeedOtherUpdatesCount=sum(computersNeeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>Hiányzó frissítések összegzése

```
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
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>Számítógépek listája

```
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

```
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

## <a name="integrate-with-system-center-configuration-manager"></a>Integrálás a System Center Configuration Managerrel

Ügyfelek, akik befektettek a System Center Configuration Managerben számítógépek, kiszolgálók és mobileszközök kezeléséhez is a teljesítményét és fejlettségét a Configuration Manager szoftverfrissítések kezelését megkönnyítő támaszkodnak. A Configuration Manager a szoftverfrissítés-kezelési (SUM) ciklus részét képezi.

Megtudhatja, hogyan integrálható a felügyeleti megoldás a System Center Configuration Managerrel, tekintse meg a [integrálása System Center Configuration Managerben az Update Management](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patch-linux-machines"></a>Javítás Linux rendszerű gépeken

Az alábbi szakaszok ismertetik a lehetséges problémák Linux javítása.

### <a name="unexpected-os-level-upgrades"></a>Váratlan operációsrendszer-szintű frissítései

Az egyes Linux variantní hodnoty, például a Red Hat Enterprise Linux operációsrendszer-szintű verziófrissítések csomagok keresztül fordulhatnak elő. Ez készülve az Update Management fut, az operációs rendszer verziószámának változik. Mert frissítéskezelési frissítési csomagok, amelyek a rendszergazda a Linux rendszerű számítógépen helyileg használja ugyanazokat a módszereket használ, ez a viselkedés szándékosan így.

Az operációs rendszer verzióját az Update Management futtatások keresztül frissítése elkerüléséhez használja az **kizárási** funkció.

A Red Hat Enterprise Linux a kizárni kívánt nevét redhat – kiadás-server.x86_64.

![Kizárni kívánt Linux-csomagok](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Kritikus fontosságú / biztonsági javításokat nem lesznek alkalmazva

Amikor frissítéseket telepít egy Linux rendszerű gép, kiválaszthatja a frissítési besorolásokat. Ezzel leszűkíti az alkalmazott frissítések körét, amelyek megfelelnek a megadott feltételeknek. A szűrő alkalmazása helyileg a számítógépen a frissítés telepítésekor.

Frissítéskezelési frissítés Adatbővítés végez a felhőben, mert néhány frissítést előfordulhat, hogy a megjelölni az Update Management jelentőségűként biztonsági, annak ellenére, hogy a helyi gép nem rendelkezik az információkat. Ennek eredményeképpen ha Linux rendszerű gép kritikus frissítéseket alkalmazza, előfordulhat frissítéseket, amelyek nincsenek megjelölve jelentőségűként biztonsági, hogy gép és a frissítések nem lesznek alkalmazva.

Azonban az Update Management előfordulhat, hogy továbbra is jelenti, hogy a gép, hogy a megfelelő frissítés további adatait, mert nem megfelelő.

Frissítési besorolás szerint frissítéseinek telepítéséhez nem működik a CentOS beépített. A SUSE kiválasztásával *csak* más frissítéseket, a besorolás vonhat néhány biztonsági frissítések is, ha a biztonsági frissítések telepítve kapcsolódnak a zypper használatával (Csomagkezelő), vagy annak függőségeit először szükség. Ez a zypper használatával korlátozása. Bizonyos esetekben, előfordulhat, hogy kell futtassa újra a frissítés telepítése, ellenőrizze, ellenőrizze a frissítési napló.

## <a name="troubleshoot"></a>Hibaelhárítás

Az Update Management hibaelhárítása kapcsolatban lásd: [az Update Management hibáinak elhárítása](troubleshoot/update-management.md)

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyagban megtudhatja, hogyan kezelheti a frissítéseket a Windows virtuális gépek számára.

> [!div class="nextstepaction"]
> [Az Azure Windows rendszerű virtuális gépek frissítéseinek és javításainak kezelése](automation-tutorial-update-management.md)

* Naplókeresés funkciójával [Log Analytics](../log-analytics/log-analytics-log-searches.md) frissítés részletes adatainak megtekintéséhez.
* [Riasztások létrehozása](../log-analytics/log-analytics-alerts.md) , a számítógépekről hiányzó kritikus frissítések észlelésekor, vagy ha egy számítógép automatikus frissítése letiltott állapotba kerül.
