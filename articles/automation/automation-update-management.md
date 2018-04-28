---
title: Frissítse a felügyeleti megoldás az Azure-ban
description: Ebből a cikkből megtudhatja, hogyan használhatja ezt a megoldást Windows és Linux rendszerű számítógépek frissítéseinek kezelésére.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/23/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c4b05044b0894e565ec4136f368314cb22041a7b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="update-management-solution-in-azure"></a>Frissítse a felügyeleti megoldás az Azure-ban

A frissítés-kezelési megoldás az Azure automationben lehetővé teszi a Windows és Linux rendszerű számítógép Azure, a helyszíni környezetben és más szolgáltatók telepítése operációs rendszer frissítéseinek kezelése. Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és felügyelhető a kiszolgálók szükséges frissítéseinek telepítése is.

A virtuális gépek frissítéseinek felügyeletét közvetlenül az [Azure Automation](automation-offering-get-started.md)-fiókjából engedélyezheti.
Ha szeretné megtudni, hogyan engedélyezheti a virtuális gépek frissítéseinek felügyeletét az Automation-fiókjából, akkor tekintse meg a [több virtuális gép frissítéseinek felügyeletét](manage-update-multi.md) ismertető cikket. Engedélyezheti a frissítéskezelés az egyetlen virtuális gépek létrehozását a virtuális gép lap az Azure portálon. Ebben a forgatókönyvben érhető el mindkét [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) és [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) virtuális gépek.

## <a name="solution-overview"></a>Megoldás áttekintése

Számítógépek frissítés használatával felügyelt értékelési és a frissítés központi telepítések elvégzéséhez a következő beállításokat:

* Microsoft Monitoring agent a Windows vagy Linux
* PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
* Automation hibrid runbook-feldolgozó
* Microsoft Update vagy Windows Server Update Services Windows-számítógépekhez

Az alábbi ábrán látható konceptuális ábrázolása viselkedését, és adatfolyam hogyan a megoldás értékelésére, és alkalmazza a biztonsági frissítéseket az összes kapcsolódó, Windows Server és Linux rendszerű számítógépek a munkaterületen.

![Frissítse a felügyeleti folyamatot](media/automation-update-management/update-mgmt-updateworkflow.png)

Miután a számítógép frissítési megfelelőség szempontjából vizsgálatot végez, az ügynök továbbítja a tömeges szolgáltatáshoz. Windows-számítógépeken a rendszer alapértelmezés szerint 12 óránként elvégzi a megfelelőségi vizsgálatot. A vizsgálati ütemezés szerint mellett vizsgálata frissítési megfelelőség szempontjából a kezdeményezett belül 15 perc, ha a Microsoft Monitoring Agent (MMA) újraindítása, rissítés telepítése előtt, és a frissítés telepítése után. Linux-számítógépeken a rendszer alapértelmezés szerint 3 óránként végzi el a megfelelőségi vizsgálatot, valamint az MMA-ügynök újraindítása után 15 percen belül.

A megoldás jelenti, hogy mennyire naprakész a számítógép az alapján, hogy milyen forrást konfigurált a szinkronizáláshoz. Ha a Windows-számítógép a WSUS-nak való jelentésre van konfigurálva, az eredmények eltérőek lehetnek a Microsoft Update által megjelenített adatoktól attól függően, hogy a WSUS mikor szinkronizált utoljára a Microsoft Update-tel. Ez megegyezik a Linux rendszerű számítógépek, jelentést és egy nyilvános tárház egy helyi tárház vannak konfigurálva.

A szoftverfrissítések központi telepítéséhez vagy telepítéséhez létrehozhat egy ütemezett üzembe helyezést a frissítést igénylő számítógépeken. Windows-számítógépek esetében a *Választható* besorolású frissítések nem tartoznak az üzembe helyezés hatálya alá, csak a kötelező frissítések. Az ütemezett telepítési határozza meg, milyen cél számítógépek megkapják a megfelelő frissítéseket, vagy explicit módon adja meg a számítógépeket, vagy jelöljön ki egy [számítógépcsoport](../log-analytics/log-analytics-computer-groups.md) alapuló számítógépek egy adott készletét napló átvizsgálása ki. Emellett meghatároz egy ütemezést egy időszak jóváhagyására és kijelölésére, amelyen belül engedélyezett a frissítések telepítése. A telepítést az Azure Automation runbookjai végzik. A runbookok nem tekinthetők meg, és nem kívánnak semmilyen konfigurálást. Frissítéstelepítés létrehozásakor egy ütemezés jön létre, amely a megadott időben az érintett számítógépekre irányuló frissítési mesterrunbookot indít el. A mesterrunbook minden olyan ügynökön egy gyermekrunbookot indít el, amely elvégzi a szükséges frissítések telepítését.

A frissítéstelepítésben megadott helyen és időben a célszámítógépek egyszerre hajtják végre a telepítést. A rendszer először egy vizsgálatot hajt végre, amelynek során ellenőrzi, mely frissítések szükségesek még, és telepíti őket. A WSUS-ügyfélszámítógépek számára, ha a frissítéseket a WSUS, jóvá nem hagyott a frissítés telepítése sikertelen.

## <a name="clients"></a>Ügyfelek

### <a name="supported-client-types"></a>Ügyfelek támogatott típusok

A következő táblázat a támogatott operációs rendszerek listáját jeleníti meg:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Csak értékelés frissítése         |
|Windows Server 2008 R2 SP1 és újabb rendszer     |A Windows PowerShell 4.0-s vagy újabb rendszer szükséges ([töltse le a WMF 4.0-s](https://www.microsoft.com/download/details.aspx?id=40855)).</br> A Windows PowerShell 5.1 ([letöltése WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)) megbízhatóbbak ajánlott.         |
|CentOS 6 (x86/x64) és 7 (x64)      | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Red Hat Enterprise 6 (x86/x64) és 7 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)     | A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.        |
|Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)      |A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.         |

### <a name="unsupported-client-types"></a>Ügyfél nem támogatott típusú

A következő táblázat az operációs rendszereket, amelyek nem támogatottak.

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows-ügyfél     | Ügyféloldali operációs rendszerek (a Windows 7, Windows 10 stb.) nem támogatottak.        |
|Windows Server 2016 Nano Server     | Nem támogatott       |

### <a name="client-requirements"></a>Ügyfélkövetelmények

#### <a name="windows"></a>Windows

Windows-ügynökök kommunikálni a Windows Server Update Services (WSUS) kiszolgáló, vagy van a Microsoft Update eléréséhez meg kell adni. Frissítéskezelés a System Center Configuration Managerrel használható, ismerje meg, további információ az integrációs feladatokhoz látogasson el a [integrálni System Center Configuration Manager frissítési felügyeleti](oms-solution-updatemgmt-sccmintegration.md#configuration). A [Windows-ügynök](../log-analytics/log-analytics-agent-windows.md) szükséges. Ez az ügynök telepítése automatikusan történik, ha egy Azure virtuális gép.

#### <a name="linux"></a>Linux

A Linux a gép hozzáféréssel kell rendelkeznie egy frissítés tárházba, amely magán- vagy nyilvános lehet. Ezen megoldás nem támogatott Linux úgy konfigurálva, hogy több Naplóelemzési munkaterület jelentés OMS-ügynököt.

Az OMS-ügynök telepítése Linux és a legújabb verzió letöltéséhez kapcsolatos további információkért lásd: [Operations Management Suite-ügynök Linux](https://github.com/microsoft/oms-agent-for-linux). További információért a Windowshoz készült OMS-ügynök telepítésével kapcsolatban tekintse meg a [Windowshoz készült Operations Management Suite-ügynökkel](../log-analytics/log-analytics-windows-agent.md) kapcsolatos részt.

## <a name="permissions"></a>Engedélyek

Hozzon létre, és a frissítés központi telepítések felügyeletéhez szükséges, konkrét engedélyeket kell. Ezek az engedélyek olvashat további [szerepkör alapú hozzáférés - frissítéskezelés](automation-role-based-access-control.md#update-management)

## <a name="solution-components"></a>Megoldás-összetevők

Ez a megoldás a következő erőforrásokból áll, amelyek az Automation-fiókjába lesznek felvéve, és ügynökökhöz vagy az Operations Managerhez kapcsolt felügyeleti csoporthoz lesznek közvetlenül hozzákapcsolva.

### <a name="hybrid-worker-groups"></a>Hibridfeldolgozó-csoportok

Miután engedélyezte a megoldás, a Naplóelemzési munkaterület közvetlenül kapcsolódó minden Windows számítógép automatikusan van beállítva egy hibrid forgatókönyv-feldolgozót a megoldásban a runbookok támogatásához. A megoldás által kezelt Windows számítógépenként szerepel a hibrid dolgozó csoportok lapon alatt az Automation-fiókhoz, a következő elnevezési rendszer hibrid feldolgozócsoport *állomásnév FQDN_GUID*. Ezek a csoportok a runbookok nem tudja megcélozni a fiókjában, egyébként pedig és a sikertelen. Ezek a csoportok csak a felügyeleti megoldást támogatják.

Azonban a Windows rendszerű számítógépek hozzáadhatóak egy hibrid runbook-feldolgozócsoporthoz az Automation-fiókban az Automation-runbookok támogatása érdekében, ha ugyanazon fiókot használja a megoldáshoz és a hibrid runbook-feldolgozócsoporttagsághoz. Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.

### <a name="management-packs"></a>Felügyeleti csomagok

Ha a System Center Operations Manager felügyeleti csoport csatlakoztatva van a Naplóelemzési munkaterület, a következő felügyeleti csomagokat az Operations Manager vannak telepítve. Ezeket a felügyeleti csomagokat a megoldás hozzáadását követően a rendszer a közvetlenül kapcsolódó Windows rendszerű számítógépekre is telepíti. A felügyeleti csomagokat nem szükséges konfigurálni vagy felügyelni.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-non-azure-machines-are-onboarded"></a>Ellenőrizze az Azure-gépek előkészítve

Győződjön meg arról, hogy közvetlenül csatlakoztatott gépek kommunikáló Naplóelemzési, után néhány percet is futtathatja a következő naplófájl-keresési:

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

A Windows-számítógépen a következő ügynök ellenőrzésére a Log Analyticshez tekinthetők át:

1. Nyissa meg a Microsoft Monitoring Agent, a Vezérlőpulton, és az a **Azure Naplóelemzés** lapon, az ügynök jeleníti meg a következő üzenet: **Naplóelemzési sikeresen csatlakozott a Microsoft Monitoring Agent** .
2. Nyissa meg a Windows Eseménynaplóját, lépjen az **Alkalmazás- és szolgáltatásnaplók\Operations Manager** részhez, és keresse meg a 3000-es, illetve az 5002-es eseményazonosítót a forrás szolgáltatás-összekötőből. Ezek az események azt jelzik, a számítógép regisztrálva van a Naplóelemzési munkaterület, és konfigurációs fogadja.

Ha az ügynök nem tud kommunikálni a Naplóelemzési, és a tűzfalon vagy proxykiszolgálón keresztül az internettel történő kommunikációra van konfigurálva, győződjön meg arról, a tűzfal vagy a proxy kiszolgáló megfelelően konfigurálva megtekintésével [hálózati konfigurációja Windows-ügynök](../log-analytics/log-analytics-agent-windows.md) vagy [hálózati konfigurációt a Linux-ügynök](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Ha a Linux rendszerek kommunikálni a proxy vagy az OMS-átjáró vannak konfigurálva, és bevezetési ezt a megoldást, a frissítés a *proxy.conf* engedélyeket a omiuser csoport olvasási jogosultsággal a fájl a következő elvégzésével parancsok: `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Az újonnan hozzáadott Linux-ügynökök **Frissítve** állapotúak, miután a rendszer végrehajt egy elemzést. A folyamat akár hat órát is igénybe vehet.

Ellenőrizheti, hogy az Operations Manager felügyeleti csoport Naplóelemzési kommunikál, [ellenőrzése az Operations Manager integrációja Naplóelemzési](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms).

## <a name="data-collection"></a>Adatgyűjtés

### <a name="supported-agents"></a>Támogatott ügynökök

Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A megoldás információt szerez be Windows-ügynököktől a rendszerfrissítésekről, és kezdeményezi a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A megoldás információt szerez be Linux-ügynököktől a rendszerfrissítésekről, és kezdeményezi a szükséges frissítések telepítését a támogatott disztribúciókon. |
| Az Operations Manager felügyeleti csoportja |Igen |A megoldás információt szerez be a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről.</br>Ehhez nem szükséges, hogy közvetlen kapcsolat legyen az Operations Manager-ügynök és a Log Analytics között. A Naplóelemzési munkaterületet adat továbbítódik a felügyeleti csoportból. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

Minden felügyelt Windows-számítógép esetében naponta kétszer történik vizsgálat. A rendszer 15 percenként lekérdezi a Windows API utolsó frissítésének időpontját, hogy meghatározza, megváltozott-e az állapot, és ha igen, megfelelőségi vizsgálatot kezdeményez. Linux-számítógépek esetében a vizsgálat három óránként történik.

30 perctől akár 6 óráig is eltarthat, amíg megjelennek a felügyelt számítógépekből származó frissített adatok az irányítópulton.

## <a name="viewing-update-assessments"></a>A frissítési felmérések megtekintése

Kattintson a a **frissítéskezelés** a az automation-fiók a gépek állapotának megtekintése.

Ez a nézet a gépek hiányzó frissítések, a központi telepítést és az ütemezett frissítés központi telepítések tájékoztatást nyújt.

Futtathatja a napló keresési, amely információkat ad vissza a gépen, frissítés, vagy a központi telepítés a listában az elem kiválasztásával. Ekkor megnyílik a **naplófájl-keresési** egy lekérdezést a kijelölt lapon.

## <a name="installing-updates"></a>Frissítések telepítése

A munkaterületen található összes Linux- és Windows-számítógép frissítésének felmérését követően a szükséges frissítések telepítését egy *frissítéstelepítés* létrehozásával végezheti el. Egy központi telepítés egy vagy több számítógépet a szükséges frissítések ütemezett telepítés. Ehhez adja meg a telepítés dátumát és idejét, valamint meg kell határoznia a telepítésben érintett számítógépet vagy számítógépcsoportot. A számítógépcsoportokkal kapcsolatos további információkért tekintse meg a [Log Analytics számítógépcsoportjaival](../log-analytics/log-analytics-computer-groups.md) kapcsolatos részt. Számítógépcsoportok szerepel a frissítések központi telepítését, ha csoporttagság ütemezés létrehozása során csak egyszer legyen kiértékelve. A csoportot érintő későbbi változások nem jelennek meg. Ennek megoldásához törölje az ütemezett frissítéstelepítést, és hozza ismét létre.

> [!NOTE]
> Az Azure Marketplace-ről üzembe helyezett Windows rendszerű virtuális gépek alapértelmezés szerint úgy vannak beállítva, hogy automatikusan frissítéseket kapjanak a Windows Update szolgáltatástól. Ez a viselkedés nem változik meg, miután hozzáadja ezt a megoldást vagy Windows rendszerű virtuális gépeket a munkaterületéhez. Ha nem aktív kezelheti a frissítéseket az ebben a megoldásban ez az alapértelmezett viselkedés (automatikus frissítések alkalmazása) vonatkozik.

Ahhoz, hogy Ubuntu rendszeren elkerülje a karbantartási időszakon kívüli frissítéstelepítést, konfigurálja újra az Unattended-Upgrade csomagot az automatikus frissítések letiltásához. A konfigurációval kapcsolatos útmutatásért lásd [az Ubuntu Server útmutatójának automatikus frissítésekkel kapcsolatos témakörét](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Az Azure Marketplace-en elérhető, igény szerinti Red Hat Enterprise Linux- (RHEL-) rendszerképekből létrehozott virtuális gépek regisztrálva vannak az Azure-ban üzembe helyezett [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) eléréséhez. Minden más Linux-változatot a disztribúciók online fájltárolójából kell frissíteni, a támogatott eljárásokat követve.

## <a name="viewing-missing-updates"></a>Hiányzó frissítések megtekintése

Kattintson a **hiányzó frissítések** hiányzik a gépeknek a frissítések listájának megtekintéséhez. Minden egyes frissítés szerepel a listán, és információkat vonatkozó további információt a frissítés, az operációs rendszer és a hivatkozás igénylő gépek számát jeleníti meg. Minden egyes frissítés választható és a **naplófájl-keresési** lap jeleníti meg, és a frissítések további részleteit jeleníti meg.

## <a name="viewing-update-deployments"></a>Frissítéstelepítések megtekintése

Kattintson a **szoftverfrissítés-telepítések** lap választásával tudja megtekinteni a meglévő központi telepítések listáját. Bármelyik frissítés a központi telepítések a tábla kattintva megnyílik a **központi telepítés futtatása frissítés** adott központi telepítési oldal.

![Frissítéstelepítési eredmények áttekintése](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>Frissítéstelepítés létrehozása

Hozzon létre egy új központi telepítésének kattintva a **ütemezés központi telepítésének** gombra, nyissa meg a képernyő tetején a **új központi telepítésének** lap. Az alábbi táblázatban szereplő tulajdonságok értékeket kell megadnia:

| Tulajdonság | Leírás |
| --- | --- |
| Name (Név) |A frissítéstelepítést beazonosító egyedi név. |
|Operációs rendszer| Linux- vagy Windows|
| Gépek frissítése |Válasszon ki egy mentett keresést vagy gép válasszon címet a legördülő listán, és válassza ki az egyes gépek |
|Frissítési besorolások|Válassza ki a szükséges összes frissítési besorolások|
|Frissítések kizárása|Adja meg a "KB" előtag nélkül kizárandó összes KB|
|Ütemezési beállítások|Válassza ki a dátumot, és válassza ki az egyszer, vagy az ismétlődés ismétlődő|
| Karbantartási időszak |Állítsa be a frissítéseket percek számát. Az érték lehet nem lehet kisebb, mint 30 perc és legfeljebb 6 óra |

## <a name="update-classifications"></a>Frissítési besorolások

A következő táblázatok tartalmazzák a frissítési besorolások felügyelete mellett a következő definícióját: minden besorolási listáját.

### <a name="windows"></a>Windows

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Egy frissítés egy adott probléma, amely kritikus, nem biztonsági hiba kezelésére szolgál.        |
|Biztonsági frissítések     | Egy frissítés egy termékspecifikus biztonsági problémára.        |
|Kumulatív frissítések     | Az egyszerű telepítés egy csomagba gyorsjavítások összesített csoportja.        |
|Funkciócsomagok     | Termékkiadáson kívül terjesztett új termékfunkciók.        |
|Szervizcsomagok     | Egy alkalmazás által használt gyorsjavítások összesített csomagja.        |
|Definíciófrissítések     | Vírus- vagy egyéb definíciós fájlok frissítése.        |
|Eszközök     | A segédprogram vagy szolgáltatás, amely egy vagy több feladat végrehajtására.        |
|Frissítések     | Egy alkalmazás vagy a jelenleg telepített fájl frissítése.        |

### <a name="linux"></a>Linux

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Frissítéseket egy meghatározott problémára vagy egy termékspecifikus biztonsági problémára.         |
|Egyéb frissítések     | Minden más frissítéseket, amelyek nem kritikus fontosságú jellege vagy biztonsági frissítések.        |

## <a name="search-logs"></a>Keresési naplókat

A részleteket a portál által biztosított, mellett keresések szemben a naplók végezheti el. Az a **változások követése** megnyitva, kattintson **Naplóelemzési**, ekkor megnyílik a **naplófájl-keresési** lap

### <a name="sample-queries"></a>A lekérdezés

A következő táblázat a megoldás által gyűjtött frissítési rekordok minta napló keres:

| Lekérdezés | Leírás |
| --- | --- |
|Frissítés</br>&#124;Ha UpdateState == "Szükséges" és az opcionális == false</br>&#124;Számítógép, a cím, a KBID, a besorolás, a PublishedDate projekt |Minden számítógép, amelyről hiányzik frissítés</br>Adja hozzá az operációs rendszer korlátozni a következők egyikét:</br>OSType = "Windows"</br>OSType == "Linux" |
| Frissítés</br>&#124;Ha UpdateState == "Szükséges" és az opcionális == false</br>&#124;Ha számítógép == "ContosoVM1.contoso.com"</br>&#124;Számítógép, a cím, a KBID, a termék, a PublishedDate projekt |Egy adott számítógépről hiányzó frissítések (cserélje le az értéket a saját számítógépnevére)|
| Esemény</br>&#124;Ha EventLevelName == "error" és a számítógép ((frissítés &#124; where (besorolási == "Biztonsági frissítések" vagy a besorolási == "Kritikus frissítések")</br>&#124;Ha UpdateState == "Szükséges" és az opcionális == false </br>&#124;különálló számítógép)) |Olyan gépek hibaeseményei, amelyeknél kritikus vagy biztonsági szükséges frissítések hiányoznak |
| Frissítés</br>&#124;Ha UpdateState == "Szükséges" és az opcionális == false</br>&#124;különböző cím |Egyedi frissítések minden számítógépnél |
| UpdateRunProgress</br>&#124;Ha InstallationStatus == "sikertelen" </br>&#124;AggregatedValue összefoglalója count() által számítógép, cím, UpdateRunName = |Futtassa egy frissítés sikertelen rendelkező számítógépek</br>Adja hozzá az operációs rendszer korlátozni a következők egyikét:</br>OSType = "Windows"</br>OSType == "Linux" |
| Frissítés</br>&#124;Ha OSType == "Linux"</br>&#124;Ha UpdateState! = "Nem szükséges" és (besorolási == "Kritikus frissítések" vagy a besorolási == "Biztonsági frissítések")</br>&#124;AggregatedValue összefoglalója = count() számítógépenként |Linux gépeire, amelyeket a csomag frissítés érhető el, amely kritikus vagy biztonsági rést listája |
| UpdateRunProgress</br>&#124;Ha UpdateRunName == "DeploymentName"</br>&#124;AggregatedValue összefoglalója = count() számítógépenként|Az ebben a frissítésfuttatásban frissített számítógépek (cserélje le az értéket a saját frissítéstelepítésének nevére) |

## <a name="integrate-with-system-center-configuration-manager"></a>Integrálás a System Center Configuration Managerrel

Azok a felhasználók, akik befektettek a System Center Configuration Managerbe a számítógépek, kiszolgálók és mobileszközök kezelése érdekében, a teljesítményét és fejlettségét a szoftverfrissítések kezelése során, a szoftverfrissítés-kezelési (SUM) ciklus részeként is kihasználják.

A felügyeleti megoldás integrálása a System Center Configuration Managerrel kapcsolatban a [integrálni System Center Configuration Manager frissítési felügyeleti](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patching-linux-machines"></a>Linux-gépek javítását

Az alábbi szakaszok ismertetik a lehetséges problémákat Linux javítását.

### <a name="package-exclusion"></a>Csomag kizárása

Bizonyos Linux-változatok, például a Red Hat Enterprise Linux, a csomagok keresztül az operációs rendszer szintű frissítések fordulhat elő. Ez vezethet frissítéskezelés fut. Ha megváltoztatja a az operációs rendszer verziószáma. Frissítse a felügyeleti csomagok frissítése, mint a rendszergazda helyileg a Linux rendszerű számítógépen ugyanazokat a módszereket használja, mert ez a viselkedés szándékosan így.

Frissítéskezelés futtatása keresztül operációsrendszer-verzió frissítése elkerüléséhez használja a **kizárási** szolgáltatás.

Red Hat Enterprise Linux, a csomag nevét, hogy kizárja lenne: redhat – kiadás-server.x86_64

![Zárja ki a Linux-csomagok](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>Most nincs alkalmazva biztonsági javítások

Frissítések telepítése a a Linux rendszerű számítógépre, ha frissítési besorolások hajthatók végre. A szűrés frissítéseket, amelyek érvényesek a megadott feltételeknek megfelelő. A szűrő alkalmazása helyileg a számítógépen a frissítés telepítésekor. Mivel felügyeleti frissítése Frissítés dúsító a felhőben hajtja végre, néhány frissítést előfordulhat, hogy lehet megjelölt frissítése felügyeleti biztonsági hatás rendelkezőként, bár a helyi számítógép nem rendelkezik ezt az információt. Ennek eredményeképpen ha kritikus frissítések alkalmazása a Linux-gépek, valószínűleg frissítéseket, amelyek nincsenek megjelölve tartalmazónak címkéz biztonsági hatása az, hogy a gép, és a rendszer nem alkalmazza. Felügyeleti frissítése azonban továbbra is lehetséges, hogy jelentést, hogy a gép, hogy nem kompatibilis, mert a megfelelő frissítésével kapcsolatos további információkat.

Frissítési besorolás frissítések telepítése a openSUSE Linux miatt a különböző javítási modellt, amely nem működnek.

## <a name="troubleshooting"></a>Hibaelhárítás

Ebben a szakaszban arról talál információkat, hogyan háríthatja el a frissítéskezelési megoldásban felmerülő hibákat.

Ha problémák merülnek fel a megoldás vagy virtuális gépek bevezetése során, ellenőrizze az **Application and Services Logs\Operations Manager** (Alkalmazási és szolgáltatási naplók\Operations Manager) eseménynaplóban a 4502-es eseményazonosítóval ellátott eseményeket, valamint a következőt tartalmazó eseményüzeneteket: **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. Az alábbi táblázat konkrét hibaüzeneteket, illetve egy-egy lehetséges megoldásukat ismerteti.

| Üzenet | Ok | Megoldás |
|----------|----------|----------|
| A gép nem regisztrálható a javításkezelőhöz,</br>A regisztráció kivétel miatt meghiúsult</br>System.InvalidOperationException: {„Üzenet”:„A gép már</br>regisztrálva van egy másik fiókhoz. "} | A gép már be lett vezetve egy másik munkaterületre a frissítéskezeléshez | Végezze el a régi összetevők tisztítását [a hibrid runbook-csoport törlésével](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|
| Nem sikerült regisztrálni a gépet a javítások, regisztrációs kivétel miatt sikertelen volt</br>System.Net.Http.HttpRequestException: Hiba történt a kérés küldése során. ---></br>System.Net.WebException: Az alapul szolgáló kapcsolat</br>megszakadt: Váratlan hiba</br>történt a fogadó oldalon. ---> System.ComponentModel.Win32Exception:</br>Az ügyfél és a kiszolgáló nem képes kommunikálni,</br>mert nem rendelkeznek közös algoritmussal | A proxy/átjáró/tűzfal blokkolja a kommunikációt | [A rendszerkövetelmények áttekintése](automation-offering-get-started.md#network-planning)|
| A gép nem regisztrálható a javításkezelőhöz,</br>A regisztráció kivétel miatt meghiúsult</br>Newtonsoft.Json.JsonReaderException: Hiba történt a pozitív végtelen érték elemzése közben. | A proxy/átjáró/tűzfal blokkolja a kommunikációt | [A rendszerkövetelmények áttekintése](automation-offering-get-started.md#network-planning)|
| A szolgáltatás által bemutatott tanúsítványt \<wsid\>. oms.opinsights.azure.com</br>nem a Microsoft szolgáltatásaihoz használt</br>hitelesítésszolgáltató bocsátotta ki. Kapcsolatfelvétel</br>a hálózati rendszergazdánál, hogy futtat-e olyan proxyt, amely elfogja a</br>TLS/SSL-kommunikációt. |A proxy/átjáró/tűzfal blokkolja a kommunikációt | [A rendszerkövetelmények áttekintése](automation-offering-get-started.md#network-planning)|
| A gép nem regisztrálható a javításkezelőhöz,</br>A regisztráció kivétel miatt meghiúsult</br>AgentService.HybridRegistration.</br>PowerShell.Certificates.CertificateCreationException:</br>Nem sikerült önaláírt tanúsítványt létrehozni. ---></br>System.UnauthorizedAccessException: A hozzáférés megtagadva. | Hiba az önaláírt tanúsítvány létrehozásakor | Ellenőrizze, hogy a rendszerfióknak</br>van-e olvasási hozzáférése a következő mappához:</br>**C:\ProgramData\Microsoft\**</br>** Crypto\RSA**|

## <a name="next-steps"></a>További lépések

Az oktatóanyag áttekintésével megismerheti, hogyan kezelheti a frissítéseket a Windows virtuális gépek továbbra is.

> [!div class="nextstepaction"]
> [Frissítések és javítások kezelheti a Windows Azure virtuális gépeken](automation-tutorial-update-management.md)

* A részletes frissítési adatokat a [Log Analytics](../log-analytics/log-analytics-log-searches.md) Naplókeresés funkciójával is megtekintheti.
* [Létrehozhat riasztásokat](../log-analytics/log-analytics-alerts.md) a számítógépekről hiányzó kritikus frissítések jelzésére vagy arra az estre, ha egy számítógép automatikus frissítése letiltott állapotba kerül.
