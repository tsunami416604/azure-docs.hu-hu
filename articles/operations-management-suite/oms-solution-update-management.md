---
title: "Frissítéskezelési megoldás az OMS-ben | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja ezt a megoldást Windows és Linux rendszerű számítógépek frissítéseinek kezelésére."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: 71322c650b2ee464bab91bf8d4b176f3b2d93949
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="update-management-solution-in-oms"></a>Frissítéskezelési megoldás az OMS-ben

![Frissítéskezelési szimbólum](./media/oms-solution-update-management/update-management-symbol.png)

Az OMS-beli frissítéskezelési megoldással az Azure-ban üzembe helyezett Windows és Linux rendszerű számítógépeken, helyszíni környezetekben vagy más felhőszolgáltatóknál kezelheti az operációs rendszer biztonsági frissítéseit.  Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és felügyelhető a kiszolgálók szükséges frissítéseinek telepítése is.

## <a name="update-management-in-azure-automation"></a>Az Azure Automation frissítéseinek felügyelete

A virtuális gépek frissítéseinek felügyeletét közvetlenül az [Azure Automation](../automation/automation-offering-get-started.md)-fiókjából engedélyezheti.
Ha szeretné megtudni, hogyan engedélyezheti a virtuális gépek frissítéseinek felügyeletét az Automation-fiókjából, akkor tekintse meg a [több virtuális gép frissítéseinek felügyeletét](../automation/manage-update-multi.md) ismertető cikket.


## <a name="solution-overview"></a>Megoldás áttekintése
Az OMS által kezelt számítógépek a következők használatával hajtják végre a felméréseket és frissítik az üzemelő példányokat:

* OMS Windows- vagy Linux-ügynök
* PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
* Automation hibrid runbook-feldolgozó
* Microsoft Update vagy Windows Server Update Services Windows-számítógépekhez

Az alábbi ábrák egy koncepcióvázlaton jelenítik meg annak működését és adatfolyamait, hogy a megoldás hogyan értékeli és alkalmazza a biztonsági frissítéseket egy adott munkaterület minden csatlakoztatott Windows Server- és Linux-számítógépén.    

#### <a name="windows-server"></a>Windows Server
![A Windows Server frissítéskezelési folyamatdiagramja](media/oms-solution-update-management/update-mgmt-windows-updateworkflow.png)

#### <a name="linux"></a>Linux
![A Linux frissítéskezelési folyamatdiagramja](media/oms-solution-update-management/update-mgmt-linux-updateworkflow.png)

Miután a számítógép futtat egy vizsgálatot a frissítés megfelelőségének ellenőrzésére, az OMS-ügynök kötegelve továbbítja az adatokat az OMS-nek. Windows-számítógépeken a rendszer alapértelmezés szerint 12 óránként elvégzi a megfelelőségi vizsgálatot.  Az ütemezett vizsgálatok mellett a rendszer a Microsoft Monitoring Agent (MMA) ügynök újraindítása esetén, továbbá a frissítések telepítése előtt és után is 15 percen belül végrehajtja a frissítésmegfelelőségi vizsgálatot.  Linux-számítógépeken a rendszer alapértelmezés szerint 3 óránként végzi el a megfelelőségi vizsgálatot, valamint az MMA-ügynök újraindítása után 15 percen belül.  

A megfelelőségi adatok ezután a megoldásban foglalt irányítópultokon dolgozhatók fel és összegezhetők, illetve a felhasználó által megadott vagy előre beállított lekérdezésekkel kereshetők.  A megoldás jelenti, hogy mennyire naprakész a számítógép az alapján, hogy milyen forrást konfigurált a szinkronizáláshoz.  Ha a Windows-számítógép a WSUS-nak való jelentésre van konfigurálva, az eredmények eltérőek lehetnek a Microsoft Update által megjelenített adatoktól attól függően, hogy a WSUS mikor szinkronizált utoljára a Microsoft Update-tel.  Ugyanez a helyzet a Linux-számítógépek esetében, amelyek nyilvános adattár helyett helyi adattárnak való jelentésre vannak konfigurálva.   

A szoftverfrissítések központi telepítéséhez vagy telepítéséhez létrehozhat egy ütemezett üzembe helyezést a frissítést igénylő számítógépeken.  Windows-számítógépek esetében a *Választható* besorolású frissítések nem tartoznak az üzembe helyezés hatálya alá, csak a kötelező frissítések.  Az ütemezett telepítés határozza meg, mely célszámítógépek kapják meg az alkalmazható frissítéseket, vagy a számítógépek kifejezett kiválasztásával, vagy egy kiemelt számítógépcsoport naplókeresései alapján összeállított [számítógépcsoport](../log-analytics/log-analytics-computer-groups.md) kiválasztásával.  Emellett meghatároz egy ütemezést egy időszak jóváhagyására és kijelölésére, amelyen belül engedélyezett a frissítések telepítése.  A telepítést az Azure Automation runbookjai végzik.  A runbookok nem tekinthetők meg, és nem kívánnak semmilyen konfigurálást.  Frissítéstelepítés létrehozásakor egy ütemezés jön létre, amely a megadott időben az érintett számítógépekre irányuló frissítési mesterrunbookot indít el.  A mesterrunbook minden olyan ügynökön egy gyermekrunbookot indít el, amely elvégzi a szükséges frissítések telepítését.       

A frissítéstelepítésben megadott helyen és időben a célszámítógépek egyszerre hajtják végre a telepítést.  A rendszer először egy vizsgálatot hajt végre, amelynek során ellenőrzi, mely frissítések szükségesek még, és telepíti őket.  Fontos megjegyezni, hogy WSUS-ügyfélszámítógépek esetében sikertelen lesz a frissítéstelepítés, ha a frissítéseket nem hagyja jóvá a WSUS-ben.  Az alkalmazott frissítések eredményeit a rendszer továbbítja az OMS-nek feldolgozásra és összesítésre az irányítópultokon vagy az események keresésével.     

## <a name="prerequisites"></a>Előfeltételek
* A megoldás a frissítések állapotának felmérését Windows Server 2008 vagy újabb, a frissítéstelepítéseket pedig csak Windows Server 2008 R2 SP1 vagy újabb rendszerek esetében támogatja.  A Nano Server nem támogatott.

    > [!NOTE]
    > Frissítések a Windows Server 2008 R2 SP1 rendszerre való telepítésének támogatásához .NET-keretrendszer 4.5 és WMF 5.0 vagy újabb verzió szükséges.
    >  
* Az ügyféloldali Windows operációs rendszerek nem támogatottak.  
* A Windows rendszerű ügynökszámítógépeket vagy a Windows Server Update Services (WSUS) szolgáltatással való kommunikációhoz kell konfigurálni, vagy a Microsoft Update szolgáltatáshoz kell hozzáféréssel rendelkezniük.  

    > [!NOTE]
    > A Windows-ügynök ezzel egyidejűleg nem felügyelhető a System Center Configuration Manager használatával.  
    >
* CentOS 6 (x86/x64) és 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) és 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) és 12 (x64)  
* Ubuntu 12.04 LTS és újabb x86/x64   
    > [!NOTE]  
    > Ahhoz, hogy Ubuntu rendszeren elkerülje a karbantartási időszakon kívüli frissítéstelepítést, konfigurálja újra az Unattended-Upgrade csomagot az automatikus frissítések letiltásához. A konfigurációval kapcsolatos útmutatásért lásd [az Ubuntu Server útmutatójának automatikus frissítésekkel kapcsolatos témakörét](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.  

    > [!NOTE]
    > Ebben a megoldásban nem támogatott az olyan Linuxhoz készült OMS-ügynök, amely több OMS-munkaterületnek való jelentésre van konfigurálva.  
    >

További információért a Linuxhoz készült OMS-ügynök telepítéséről és a legújabb verzió letöltéséről tekintse meg a [Linuxhoz készült Operations Management Suite-ügynökkel](https://github.com/microsoft/oms-agent-for-linux) kapcsolatos részt.  További információért a Windowshoz készült OMS-ügynök telepítésével kapcsolatban tekintse meg a [Windowshoz készült Operations Management Suite-ügynökkel](../log-analytics/log-analytics-windows-agent.md) kapcsolatos részt.  

### <a name="permissions"></a>Engedélyek
A frissítéstelepítések létrehozásához közreműködői szerepkörrel kell rendelkeznie az Automation-fiókjában és Log Analytics-munkaterületén egyaránt.  

## <a name="solution-components"></a>Megoldás-összetevők
Ez a megoldás a következő erőforrásokból áll, amelyek az Automation-fiókjába lesznek felvéve, és ügynökökhöz vagy az Operations Managerhez kapcsolt felügyeleti csoporthoz lesznek közvetlenül hozzákapcsolva.

### <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoportja össze van kapcsolva egy OMS-munkaterülettel, az alábbi felügyeleti csomagok is telepítve lesznek az Operations Managerben.  Ezeket a felügyeleti csomagokat a megoldás hozzáadását követően a rendszer a közvetlenül kapcsolódó Windows rendszerű számítógépekre is telepíti. A felügyeleti csomagokat nem szükséges konfigurálni vagy felügyelni.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../log-analytics/log-analytics-om-agents.md).

### <a name="hybrid-worker-groups"></a>Hibridfeldolgozó-csoportok
Ha engedélyezi ezt a megoldást, az OMS-munkaterülethez közvetlenül kapcsolódó Windows-számítógépeket a rendszer automatikusan hibrid runbook-feldolgozóként konfigurálja a megoldásban lévő runbookok támogatása érdekében.  Ez az egyes, a megoldás által felügyelt Windows rendszerű számítógépek esetében az Automation-fiók Hibrid Runbook-feldolgozócsoportok paneljén lesz látható a *Gazdanév FQDN_GUID* elnevezési konvenciót követve.  Ezeket a csoportokat nem célozhatja meg, ha runbookok vannak a fiókjában, ellenkező esetben sikertelenek lesznek. Ezek a csoportok csak a felügyeleti megoldást támogatják.   

Azonban a Windows rendszerű számítógépek hozzáadhatóak egy hibrid runbook-feldolgozócsoporthoz az Automation-fiókban az Automation-runbookok támogatása érdekében, ha ugyanazon fiókot használja a megoldáshoz és a hibrid runbook-feldolgozócsoporttagsághoz.  Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.  

## <a name="configuration"></a>Konfiguráció
A frissítéskezelési megoldás az OMS-munkaterülethez való hozzáadásához és annak ellenőrzéséhez, hogy az ügynökök készítenek jelentéseket, hajtsa végre az alábbi lépéseket. A munkaterülethez már csatlakoztatott Windows-ügynökök hozzáadása automatikusan, további konfiguráció nélkül történik.

A megoldás a következő módszerekkel helyezhető üzembe:

* Az Azure Portalon az Azure Marketplace Automatizálás és vezérlés ajánlata vagy egy frissítéskezelési megoldás kiválasztásával
* Az OMS-munkaterülete OMS-megoldástárából

Ha rendelkezik összekapcsolt Automation-fiókkal és OMS munkaterülettel egyazon erőforráscsoportban és régióban, az Automatizálás és vezérlés kiválasztása esetén a rendszer ellenőrzi a konfigurációt, majd telepíti a megoldást és konfigurálja azt a két szolgáltatásban.  A frissítéskezelési megoldás az Azure Marketplace-en való kiválasztása ugyanezt eredményezi.  Ha egyik szolgáltatás sincs telepítve az előfizetésében, kövesse az **Új megoldás létrehozása** panelen leírt lépéseket, és erősítse meg, hogy szeretné telepíteni a többi, előzetesen kiválasztott, ajánlott megoldást.  Hozzáadhatja az OMS-munkaterülethez a frissítéskezelési megoldást. Ehhez kövesse a megoldástárban az [OMS-megoldások hozzáadásával](../log-analytics/log-analytics-add-solutions.md) kapcsolatos témakörben leírt eljárást.  

### <a name="confirm-oms-agents-and-operations-manager-management-group-connected-to-oms"></a>OMS-ügynökök és Operations Manager felügyeleti csoport az OMS-sel való kapcsolatának ellenőrzése

Közvetlenül kapcsolódó, Linuxhoz és Windowshoz készült OMS-ügynök az OMS-sel való kommunikációjának ellenőrzéséhez néhány perc után futtathatja a következő naplókeresést:

* Linux – `Type=Heartbeat OSType=Linux | top 500000 | dedup SourceComputerId | Sort Computer | display Table`.  

* Windows – `Type=Heartbeat OSType=Windows | top 500000 | dedup SourceComputerId | Sort Computer | display Table`

Windows rendszerű számítógépen a következők áttekintésével ellenőrizheti az ügynök csatlakozását az OMS-hez:

1.  Nyissa meg a Microsoft Monitoring Agentet a Vezérlőpulton. Az **Azure Log Analytics (OMS)** fülön az ügynök megjeleníti a következő üzenetet: **A Microsoft Monitoring Agent sikeresen csatlakozott a Microsoft Operations Management Suite szolgáltatáshoz**.   
2.  Nyissa meg a Windows Eseménynaplóját, lépjen az **Alkalmazás- és szolgáltatásnaplók\Operations Manager** részhez, és keresse meg a 3000-es, illetve az 5002-es eseményazonosítót a forrás szolgáltatás-összekötőből.  Ezek az események jelzik, hogy a számítógép regisztrálva van az OMS-munkaterületen, és konfigurációt kap.  

Ha az ügynök nem tud kommunikálni az OMS szolgáltatással, és úgy van konfigurálva, hogy tűzfalon vagy proxykiszolgálón keresztül kommunikáljon az internettel, a [ Windows ügynök hálózati konfigurációját](../log-analytics/log-analytics-windows-agent.md) vagy a [Linux ügynök hálózati konfigurációját](../log-analytics/log-analytics-agent-linux.md) bemutató témakör áttekintésével ellenőrizze, hogy a tűzfal vagy a proxykiszolgáló megfelelően van-e konfigurálva.

> [!NOTE]
> Ha Linux rendszerei proxyval vagy OMS-átjáróval való kommunikációra vannak konfigurálva, és ezt a megoldást most vezeti be, a *proxy.conf* engedélyek frissítésével biztosítson az omiuser csoportnak olvasási engedélyt a fájlhoz. Ezt a következő parancsok végrehajtásával teheti meg:  
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`  
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`


Az újonnan hozzáadott Linux-ügynökök **Frissítve** állapotúak, miután a rendszer végrehajt egy elemzést.  A folyamat akár hat órát is igénybe vehet.

Annak ellenőrzéséhez, hogy egy Operations Manager felügyeleti csoport kommunikál-e az OMS-szel, tekintse meg az [Operations Manager és az OMS integrációjának ellenőrzésével](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms) kapcsolatos részt.

## <a name="data-collection"></a>Adatgyűjtés
### <a name="supported-agents"></a>Támogatott ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A megoldás információt szerez be Windows-ügynököktől a rendszerfrissítésekről, és kezdeményezi a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A megoldás információt szerez be Linux-ügynököktől a rendszerfrissítésekről, és kezdeményezi a szükséges frissítések telepítését a támogatott disztribúciókon. |
| Az Operations Manager felügyeleti csoportja |Igen |A megoldás információt szerez be a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről.<br>Ehhez nem szükséges, hogy közvetlen kapcsolat legyen az Operations Manager-ügynök és a Log Analytics között. Az adatok a felügyeleti csoportból az OMS-tárházba lesznek továbbítva. |
| Azure Storage-fiók |Nem |Az Azure Storage nem tartalmaz rendszerfrissítésekkel kapcsolatos információt. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága
Minden felügyelt Windows-számítógép esetében naponta kétszer történik vizsgálat. A rendszer 15 percenként lekérdezi a Windows API utolsó frissítésének időpontját, hogy meghatározza, megváltozott-e az állapot, és ha igen, megfelelőségi vizsgálatot kezdeményez.  Linux-számítógépek esetében a vizsgálat három óránként történik.

30 perctől akár 6 óráig is eltarthat, amíg megjelennek a felügyelt számítógépekből származó frissített adatok az irányítópulton.   

## <a name="using-the-solution"></a>A megoldás használata
A Frissítéskezelési megoldás hozzáadásakor az OMS-munkaterületen a **Frissítéskezelés** csempe felkerül az OMS-irányítópultra. Ez a csempe a környezetben jelenleg elérhető számítógépek számát és grafikus ábrázolását jeleníti meg, valamint a frissítési megfelelőségi állapotukat.<br><br>
![Frissítéskezelés – áttekintő csempe](media/oms-solution-update-management/update-management-summary-tile.png)  


## <a name="viewing-update-assessments"></a>A frissítési felmérések megtekintése
A **Frissítéskezelés** irányítópultjának megnyitásához kattintson a **Frissítéskezelés** csempére.<br><br> ![Frissítéskezelés – áttekintő irányítópult](./media/oms-solution-update-management/update-management-dashboard.png)<br>

Az irányítópult részletes áttekintést kínál a frissítési állapotokról az operációs rendszer típusa és frissítési besorolása alapján kategorizálva – kritikus, biztonsági vagy egyéb (például egy definíciófrissítés). Az aktuális irányítópulton lévő egyes csempék eredményei csak az üzembe helyezésre jóváhagyott frissítéseket tükrözik. Ez a számítógépek szinkronizációs forrásán alapul.   A **Frissítéstelepítések** csempe kijelölésekor a rendszer átirányítja a Frissítéstelepítések oldalra, ahol megtekintheti az ütemezéseket, az aktuálisan futó telepítéseket, a befejezett telepítéseket, vagy ütemezhet új telepítést.  

Futtathat naplókeresést, amely minden bejegyzést visszaad a megfelelő csempére kattintva. Egy adott kategória és előre meghatározott feltételek lekérdezéséhez válasszon a **Gyakori frissítési lekérdezések** oszlop alatt elérhető listából.    

## <a name="installing-updates"></a>Frissítések telepítése
A munkaterületen található összes Linux- és Windows-számítógép frissítésének felmérését követően a szükséges frissítések telepítését egy *frissítéstelepítés* létrehozásával végezheti el.  A frissítéstelepítés egy vagy több számítógép szükséges frissítéseinek ütemezett telepítése.  Ehhez adja meg a telepítés dátumát és idejét, valamint meg kell határoznia a telepítésben érintett számítógépet vagy számítógépcsoportot.  A számítógépcsoportokkal kapcsolatos további információkért tekintse meg a [Log Analytics számítógépcsoportjaival](../log-analytics/log-analytics-computer-groups.md) kapcsolatos részt.  Ha számítógépcsoportok szerepelnek a frissítéstelepítésben, a csoporttagságot csak egyszer, az ütemezés létrehozásakor méri fel a rendszer.  A csoportot érintő későbbi változások nem jelennek meg.  Ennek megoldásához törölje az ütemezett frissítéstelepítést, és hozza ismét létre.

> [!NOTE]
> Az Azure Marketplace-ről üzembe helyezett Windows rendszerű virtuális gépek alapértelmezés szerint úgy vannak beállítva, hogy automatikusan frissítéseket kapjanak a Windows Update szolgáltatástól.  Ez a viselkedés nem változik meg, miután hozzáadja ezt a megoldást vagy Windows rendszerű virtuális gépeket a munkaterületéhez.  Ha nem felügyeli aktívan a frissítéseket ebben a megoldásban, az alapértelmezett viselkedést alkalmazza a rendszer (automatikusan alkalmazza a frissítéseket).  

Az Azure Marketplace-en elérhető, igény szerinti Red Hat Enterprise Linux- (RHEL-) rendszerképekből létrehozott virtuális gépek regisztrálva vannak az Azure-ban üzembe helyezett [Red Hat Update Infrastructure (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) eléréséhez.  Minden más Linux-változatot a disztribúciók online fájltárolójából kell frissíteni, a támogatott eljárásokat követve.  

### <a name="viewing-update-deployments"></a>Frissítéstelepítések megtekintése
Az elérhető frissítéstelepítések listáját a **Frissítéstelepítés** csempére kattintva tekintheti meg.  A lista a frissítések állapota szerinti csoportosításban jelenik meg: **Ütemezett**, **Futó** és **Befejezett**.<br><br> ![Frissítéstelepítési ütemezési lap](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

A frissítéstelepítések tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
| --- | --- |
| Name (Név) |A frissítéstelepítés neve. |
| Ütemezés |Az ütemezés típusa.  Az elérhető lehetőségek: *Egyszer*, *Hetente*, vagy *Havonta*. |
| Kezdési idő |A frissítéstelepítés ütemezett kezdetének dátuma és időpontja. |
| Időtartam |A frissítéstelepítés engedélyezett futási idejének hossza percekben megadva.  Ha ez alatt az idő alatt nem fejeződik be minden frissítés telepítése, akkor a hátramaradó frissítések csak a következő ütemezett frissítéstelepítés során kerülnek telepítésre. |
| Kiszolgálók |A frissítéstelepítés által érintett számítógépek száma.  |
| status |A frissítéstelepítés aktuális állapota.<br><br>Lehetséges értékek:<br>– Nincs elindítva<br>– Fut<br>– Befejeződött |

Egy befejezett frissítéstelepítést kijelölve megjeleníthető a részletes nézetű képernyő, amely az alábbi táblázatban felsorolt oszlopokat tartalmazza.  Ha a frissítéstelepítés még nem indult el, az oszlopok nem tartalmaznak adatot.<br><br> ![Frissítéstelepítési eredmények áttekintése](./media/oms-solution-update-management/update-management-deploymentresults-dashboard.png)

| Oszlop | Leírás |
| --- | --- |
| **Számítógépnézet** | |
| Windows rendszerű számítógépek |A frissítéstelepítésben érintett Windows rendszerű számítógépek számát listázza állapot alapján.  Az állapot nevére kattintva naplókeresést indíthat el, amely megjelenítheti az adott állapotú összes frissítési bejegyzést. |
| Linux rendszerű számítógépek |A frissítéstelepítésben érintett Linux rendszerű számítógépek számát listázza állapot alapján.  Az állapot nevére kattintva naplókeresést indíthat el, amely megjelenítheti az adott állapotú összes frissítési bejegyzést. |
| Számítógép telepítési állapota |A frissítéstelepítéssel érintett számítógépeket listázza a sikeres telepítések százalékarányával. A bejegyzés nevére kattintva keresést indíthat a naplóban, amellyel megjeleníti az összes hiányzó és kritikus frissítést. |
| **Frissítésnézet** | |
| Windows-frissítések |A frissítéstelepítésben érintett Windows-frissítéseket és telepítési állapotukat listázza frissítésenként.  Egy frissítést kiválasztva futtathat egy naplókeresést, amely minden, az adott frissítéssel kapcsolatos frissítési bejegyzést visszaad, vagy kattintson az állapotra egy naplókeresés futtatásához, amely minden, a telepítéssel kapcsolatos frissítési bejegyzést visszaad. |
| Linux-frissítések |A frissítéstelepítésben érintett Linux-frissítéseket és telepítési állapotukat listázza frissítésenként.  Egy frissítést kiválasztva futtathat egy naplókeresést, amely minden, az adott frissítéssel kapcsolatos frissítési bejegyzést visszaad, vagy kattintson az állapotra egy naplókeresés futtatásához, amely minden, a telepítéssel kapcsolatos frissítési bejegyzést visszaad. |

### <a name="creating-an-update-deployment"></a>Frissítéstelepítés létrehozása
Új frissítéstelepítés létrehozásához a képernyő felső részében kattintson a **Hozzáadás** gombra az **Új telepítésfrissítés** lap megnyitásához.  Az alábbi táblázatban szereplő tulajdonságok értékeit kell megadnia.

| Tulajdonság | Leírás |
| --- | --- |
| Name (Név) |A frissítéstelepítést beazonosító egyedi név. |
| Időzóna |A kezdési időpontnál használandó időzóna. |
| Ütemezés típusa | Az ütemezés típusa.  Az elérhető lehetőségek: *Egyszer*, *Hetente*, vagy *Havonta*.  
| Kezdési idő |A frissítéstelepítés indításának dátuma és időpontja. **Megjegyzés:** Ha azonnal szeretné elvégezni a telepítést, a leghamarabb az aktuális időtől számítva 30 perc múlva futtathatja. |
| Időtartam |A frissítéstelepítés engedélyezett futási idejének hossza percekben megadva.  Ha ez alatt az idő alatt nem fejeződik be minden frissítés telepítése, akkor a hátramaradó frissítések csak a következő ütemezett frissítéstelepítés során kerülnek telepítésre. |
| Számítógépek |A frissítéstelepítés által érintett vagy célzott számítógépek vagy számítógépcsoportok nevei.  A legördülő listából válasszon ki egy vagy több elemet. |

<br><br> ![Új frissítéstelepítés lapja](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Időtartomány
A Frissítéskezelési szolgáltatás adatelemzése alapértelmezés szerint a legutóbbi egy nap során felderített csatlakoztatott felügyeleti csoportokra terjed ki.

Az adatokra vonatkozó időtartomány módosításához válassza ki az irányítópult tetején látható **Az adatok időtartománya** elemet. Itt meghatározhatja, hogy milyen időtartományban létrehozott vagy módosított adatok legyenek használva: legutóbbi 7 nap, 1 nap vagy 6 óra. Az **Egyéni** lehetőség választásával egyéni dátumtartományt is megadhat.

## <a name="log-analytics-records"></a>Log Analytics-rekordok
A Frissítéskezelési megoldás két rekordtípust hoz létre az OMS-adattárban.

### <a name="update-records"></a>Update típusú rekordok
Az egyes számítógépekhez szükséges vagy telepített minden egyes frissítéshez egy**Update** (frissítés) típusú rekord készül. Az Update típusú rekordok tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
| --- | --- |
| Típus |*Update* |
| SourceSystem |A frissítés telepítését jóváhagyó forrás.<br>Lehetséges értékek:<br>– Microsoft Update (Microsoft-frissítés)<br>– Windows Update<br>– SCCM<br>– Linux Servers (Linux-kiszolgálók – a csomagkezelőkből lekért információ alapján) |
| Approved |Azt jelzi, hogy a frissítés jóvá lett-e hagyva telepítésre.<br> Linux-kiszolgálók esetén ez nem kötelező, mivel a javításokat nem az OMS kezeli. |
| Kategóriák Windows esetén |A frissítés kategóriája.<br>Lehetséges értékek:<br>– Applications (Alkalmazások)<br>– Critical Updates (Kritikus frissítések)<br>– Definition Updates (Definíciófrissítés)<br>– Feature Packs (Funkciócsomag)<br>– Security Updates (Biztonsági frissítések)<br>– Service Packs (Szervizcsomagok)<br>– Update Rollups (Kumulatív frissítések)<br>– Updates (Frissítések) |
| Kategóriák Linux esetén |A frissítés kategóriája.<br>Lehetséges értékek:<br>– Critical Updates (Kritikus frissítések)<br>– Security Updates (Biztonsági frissítések)<br>– Other Updates (Másféle frissítések) |
| Computer |A számítógép neve. |
| InstallTimeAvailable |Azt jelzi, hogy elérhető-e a telepítés ideje az ugyanezt a frissítést telepítő más ügynököktől. |
| InstallTimePredictionSeconds |Az ugyanezt a frissítést telepítő más ügynököktől származó információ alapján számított várható telepítési idő. |
| KBID |A frissítést ismertető tudásbáziscikk azonosítója. |
| ManagementGroupName |A felügyeleti csoport neve SCOM-ügynökök esetén.  Más ügynökök esetén ez AOI-<workspace ID>. |
| MSRCBulletinID |A frissítést ismertető Microsoft biztonsági közlemény azonosítója. |
| MSRCSeverity |A Microsoft biztonsági közlemény fontossága.<br>Lehetséges értékek:<br>– Critical (Kritikus)<br>– Important (Fontos)<br>– Moderate (Közepes) |
| Optional |Azt jelzi, hogy a frissítés választható-e. |
| Product |A terméke neve, amelyhez a frissítés tartozik.  A **Megtekintés** lehetőségre kattintva a cikket megnyithatja egy böngészőben. |
| PackageSeverity |A frissítés által javított biztonsági rés  súlyossága a Linux-disztribúció szállítója által közölt adatok alapján. |
| PublishDate |A frissítés telepítésének dátuma és időpontja. |
| RebootBehavior |Azt jelzi, hogy a frissítés telepítéséhez szükséges-e újraindítás.<br>Lehetséges értékek:<br>- canrequestreboot (újraindítást igényelhet)<br>- neverreboots (nem igényel újraindítást) |
| RevisionNumber |A frissítés változatszáma. |
| SourceComputerId |A számítógép azonosításához használt globálisan egyedi azonosító (GUID). |
| TimeGenerated |A rekord utolsó frissítésének dátuma és időpontja. |
| Beosztás |A frissítés címe. |
| UpdateID |A frissítés azonosításához használt globálisan egyedi azonosító (GUID). |
| UpdateState |Azt jelzi, hogy a frissítés telepítve van-e ezen a számítógépen.<br>Lehetséges értékek:<br>– Installed – A frissítés telepítve van ezen a számítógépen.<br>– Needed – A frissítés szükséges, de nincs telepítve ezen a számítógépen. |

Ha olyan naplókeresést végez, amely **Update** típusú rekordokat ad vissza, a **Frissítések** nézet kiválasztásával megjeleníthet olyan csempéket, amelyek a keresés eredményeként visszaadott frissítések áttekintését tartalmazzák. A **Hiányzó és alkalmazott frissítések** és a **Szükséges és választható frissítések** csempék bejegyzéseire kattintva az adott típusú frissítésekre korlátozhatja a megjelenítést. Az eredeti rekordokhoz a **Lista** vagy a **Táblázat** nézetre kattintva térhet vissza.<br>

![Naplókeresés, frissítés nézet, Update típusú rekorddal](./media/oms-solution-update-management/update-la-view-updates.png)  

A **Táblázat** nézetben bármely rekord **KBID** tulajdonságára kattintva a vonatkozó tudásbáziscikket megnyithatja egy böngészőben. Ez lehetővé teszi, hogy az egyes frissítésekről egyszerűen jusson további információkhoz.<br>

![Naplókeresés, táblázat nézet csempékkel, frissítések típusú rekorddal](./media/oms-solution-update-management/update-la-view-table.png)

**Lista** nézetben a tudásbáziscikk megnyitásához a KBID tulajdonság melletti **Megtekintés** hivatkozásra kell kattintani.<br>

![Naplókeresés, lista nézet csempékkel, frissítések típusú rekorddal](./media/oms-solution-update-management/update-la-view-list.png)

### <a name="updatesummary-records"></a>UpdateSummary típusú rekordok
Minden Windows-ügynök esetében egy **UpdateSummary** típusú rekord készül. Ez a rekord minden olyan alkalommal frissül, amikor a számítógépen frissítéskeresés történik. Az **UpdateSummary** típusú rekordok tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
| --- | --- |
| Típus |UpdateSummary |
| SourceSystem |OpsManager |
| Computer |A számítógép neve. |
| CriticalUpdatesMissing |A számítógépről hiányzó kritikus frissítések száma. |
| ManagementGroupName |A felügyeleti csoport neve SCOM-ügynökök esetén. Más ügynökök esetén ez AOI-<workspace ID>. |
| NETRuntimeVersion |A számítógépre telepített .NET-futtatórendszer verziója. |
| OldestMissingSecurityUpdateBucket |A számítógépen a leghosszabb ideje hiányzó biztonsági frissítés kiadása óta eltelt időt kategorizáló gyűjtő.<br>Lehetséges értékek:<br>– Older (Régebbi)<br>– 180 days ago (180 napja)<br>– 150 days ago (150 napja)<br>– 120 days ago (120 napja)<br>– 90 days ago (90 napja)<br>– 60 days ago (60 napja)<br>– 30 days ago (30 napja)<br>– Recent (Friss) |
| OldestMissingSecurityUpdateInDays |A számítógépen a leghosszabb ideje hiányzó biztonsági frissítés kiadása óta eltelt napok száma. |
| OsVersion |A számítógépre telepített operációs rendszer verziószáma. |
| OtherUpdatesMissing |A számítógépről hiányzó további frissítések száma. |
| SecurityUpdatesMissing |A számítógépről hiányzó biztonsági frissítések száma. |
| SourceComputerId |A számítógép azonosításához használt globálisan egyedi azonosító (GUID). |
| TimeGenerated |A rekord utolsó frissítésének dátuma és időpontja. |
| TotalUpdatesMissing |A számítógépről hiányzó összes frissítés száma. |
| WindowsUpdateAgentVersion |A számítógépen található Windows Update Agent verziószáma. |
| WindowsUpdateSetting |A fontos frissítések telepítésének módját meghatározó beállítás a számítógépen.<br>Lehetséges értékek:<br>– Disabled (Letiltva)<br>– Notify before installation (Értesítés telepítés előtt)<br>– Scheduled installation (Ütemezett telepítése) |
| WSUSServer |A WSUS-kiszolgálóra mutató URL, ha a számítógép konfigurálásában meg van adva ennek használata. |

## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által összegyűjtött frissítési rekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

| Lekérdezés | Leírás |
| --- | --- |
| Type:Update OSType!=Linux UpdateState=Needed Optional=false Approved!=false &#124; measure count() by Computer |Frissítést igénylő Windows-alapú kiszolgáló-számítógépek |
| Type:Update OSType=Linux UpdateState!="Not needed" &#124; measure count() by Computer |Frissítést igénylő Linux-kiszolgálók | 
| Type=Update UpdateState=Needed Optional=false &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |Minden számítógép, amelyről hiányzik frissítés |
| Type=Update UpdateState=Needed Optional=false Computer="COMPUTER01.contoso.com" &#124; select Computer,Title,KBID,Product,UpdateSeverity,PublishedDate |Egy adott számítógépről hiányzó frissítések (cserélje le az értéket a saját számítógépnevére)|
| Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") |Minden számítógép, amelyről kritikus vagy biztonsági frissítés hiányzik | 
| Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual &#124; Distinct Computer} &#124; Distinct KBID |Hiányzó kritikus vagy biztonsági frissítések olyan számítógépeken, amelyeken kézi frissítés van beállítva |
| Type=Event EventLevelName=error Computer IN {Type=Update (Classification="Security Updates" OR Classification="Critical Updates") UpdateState=Needed Optional=false &#124; Distinct Computer} |Olyan gépek hibaeseményei, amelyeknél kritikus vagy biztonsági szükséges frissítések hiányoznak |
| Type=Update Optional=false Classification="Update Rollups" UpdateState=Needed &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |Minden olyan számítógép, amelynél kumulatív frissítések hiányoznak | 
| Type=Update UpdateState=Needed Optional=false &#124; Egyedi név |Egyedi frissítések minden számítógépnél | 
| Type:UpdateRunProgress InstallationStatus=failed &#124; measure count() by Computer, Title, UpdateRunName |Windows-alapú kiszolgáló-számítógép, amelynek frissítései sikertelenek voltak a frissítés futtatásakor | 
| Type:UpdateRunProgress InstallationStatus=failed &#124; measure count() by Computer, Product, UpdateRunName |Linux-kiszolgáló, amelynek frissítései sikertelenek voltak a frissítés futtatásakor | 
| Type=UpdateSummary &#124; measure count() by WSUSServer |WSUS-t használó számítógépek | 
| Type=UpdateSummary &#124; measure count() by WindowsUpdateSetting |Automatikus frissítési beállítások | 
| Type=UpdateSummary WindowsUpdateSetting=Manual |Azok a számítógépek, amelyeken az automatikus frissítés le van tiltva | 
| Type=Update and OSType=Linux and UpdateState!="Not needed" &#124; measure count() by Computer |Minden olyan Linux-számítógép, amelyhez csomagfrissítés érhető el | 
| Type=Update and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") &#124; measure count() by Computer |Minden olyan Linux-számítógép, amelyhez kritikus vagy biztonsági rést javító csomagfrissítés érhető el | 
| Type=Update and OSType=Linux and UpdateState!="Not needed" |Minden olyan csomag, amelyhez frissítés érhető el | 
| Type=Update  and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") |Minden olyan csomag, amelyhez kritikus vagy biztonsági rést javító frissítés érhető el | 
| Type:UpdateRunProgress &#124; measure Count() by UpdateRunName |Azon frissítéstelepítések listája, amelyekhez módosított számítógépek találhatóak | 
| Type:UpdateRunProgress UpdateRunName="DeploymentName" &#124; measure Count() by Computer |Az ebben a frissítésfuttatásban frissített számítógépek (cserélje le az értéket a saját frissítéstelepítésének nevére) | 
| Type=Update and OSType=Linux and OSName = Ubuntu &#124; measure count() by Computer |Minden olyan Ubuntu-gép, amelyhez bármilyen frissítés érhető el |

## <a name="integrate-with-system-center-configuration-manager"></a>Integrálás a System Center Configuration Managerrel

Azok a felhasználók, akik befektettek a System Center Configuration Manager használatába a számítógépek, kiszolgálók és mobileszközök kezelése érdekében, a teljesítményét és fejlettségét a szoftverfrissítések kezelése során, a szoftverfrissítés-kezelési (SUM) ciklus részeként is kihasználják.

Ha szeretné megtudni, hogyan integrálható az OMS frissítéskezelési megoldás a System Center Configuration Managerrel, tekintse át [a System Center Configuration Manager az OMS frissítéskezelési megoldással történő integrálását](../automation/oms-solution-updatemgmt-sccmintegration.md) ismertető cikket.

## <a name="troubleshooting"></a>Hibaelhárítás

Ebben a szakaszban arról talál információkat, hogyan háríthatja el a frissítéskezelési megoldásban felmerülő hibákat.

### <a name="how-do-i-troubleshoot-onboarding-issues"></a>Hogyan háríthatom el a bevezetési hibákat?
Ha problémák merülnek fel a megoldás vagy virtuális gépek bevezetése során, ellenőrizze az **Application and Services Logs\Operations Manager** (Alkalmazási és szolgáltatási naplók\Operations Manager) eseménynaplóban a 4502-es eseményazonosítóval ellátott eseményeket, valamint a következőt tartalmazó eseményüzeneteket: **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.  Az alábbi táblázat konkrét hibaüzeneteket, illetve egy-egy lehetséges megoldásukat ismerteti.  

| Üzenet | Ok | Megoldás |   
|----------|----------|----------|  
| A gép nem regisztrálható a javításkezelőhöz,<br>A regisztráció kivétel miatt meghiúsult<br>System.InvalidOperationException: {„Üzenet”:„A gép már<br>regisztrálva van egy másik fiókhoz. "} | A gép már be lett vezetve egy másik munkaterületre a frissítéskezeléshez | Végezze el a régi összetevők tisztítását [a hibrid runbook-csoport törlésével](../automation/automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|  
| A gép nem regisztrálható a javításkezelőhöz,<br>A regisztráció kivétel miatt meghiúsult<br>System.Net.Http.HttpRequestException: Hiba történt a kérés küldése során. ---><br>System.Net.WebException: Az alapul szolgáló kapcsolat<br>megszakadt: Váratlan hiba<br>történt a fogadó oldalon. ---> System.ComponentModel.Win32Exception:<br>Az ügyfél és a kiszolgáló nem képes kommunikálni,<br>mert nem rendelkeznek közös algoritmussal | A proxy/átjáró/tűzfal blokkolja a kommunikációt | [A rendszerkövetelmények áttekintése](../automation/automation-offering-get-started.md#network-planning)|  
| A gép nem regisztrálható a javításkezelőhöz,<br>A regisztráció kivétel miatt meghiúsult<br>Newtonsoft.Json.JsonReaderException: Hiba történt a pozitív végtelen érték elemzése közben. | A proxy/átjáró/tűzfal blokkolja a kommunikációt | [A rendszerkövetelmények áttekintése](../automation/automation-offering-get-started.md#network-planning)| 
| Az <wsid>.oms.opinsights.azure.com szolgáltatás által bemutatott tanúsítványt<br>nem a Microsoft szolgáltatásaihoz használt<br>hitelesítésszolgáltató bocsátotta ki. Kérjük, érdeklődjön<br>a hálózati rendszergazdánál, hogy futtat-e olyan proxyt, amely elfogja a<br>TLS/SSL-kommunikációt. |A proxy/átjáró/tűzfal blokkolja a kommunikációt | [A rendszerkövetelmények áttekintése](../automation/automation-offering-get-started.md#network-planning)|  
| A gép nem regisztrálható a javításkezelőhöz,<br>A regisztráció kivétel miatt meghiúsult<br>AgentService.HybridRegistration.<br>PowerShell.Certificates.CertificateCreationException:<br>Nem sikerült önaláírt tanúsítványt létrehozni. ---><br>System.UnauthorizedAccessException: A hozzáférés megtagadva. | Hiba az önaláírt tanúsítvány létrehozásakor | Ellenőrizze, hogy a rendszerfióknak<br>van-e olvasási hozzáférése a következő mappához:<br>**C:\ProgramData\Microsoft\**<br>**Crypto\RSA**|  

### <a name="how-do-i-troubleshoot-update-deployments"></a>Hogyan háríthatom el a frissítéstelepítési hibákat?
Megtekintheti a frissítések telepítéséért felelős runbook eredményeit. Ezek az ezt a megoldást támogató OMS-munkaterülettel összekapcsolt Automation-fiókjának Feladatok paneljén, az ütemezett frissítéstelepítésekben találhatóak.  A **Patch-MicrosoftOMSComputer** runbook egy gyermekrunbook, amely egy megadott, felügyelt számítógépet céloz meg. A részletes stream áttekintése részletes információt jelenít meg erről a telepítésről.  A kimenet megjeleníti, mely szükséges frissítések alkalmazhatóak, valamint mutatja a letöltési állapotot, a telepítési állapotot és további részleteket.<br><br> ![Frissítéstelepítési feladat állapota](media/oms-solution-update-management/update-la-patchrunbook-outputstream.png)<br>

További információért tekintse meg az [Automation runbook-kimeneteivel és -üzeneteivel](../automation/automation-runbook-output-and-messages.md) kapcsolatos részt.   

## <a name="next-steps"></a>További lépések
* A részletes frissítési adatokat a [Log Analytics](../log-analytics/log-analytics-log-searches.md) Naplókeresés funkciójával is megtekintheti.
* [Saját irányítópult létrehozásával](../log-analytics/log-analytics-dashboards.md) megjelenítheti a felügyelt számítógépek frissítési megfelelőségét.
* [Létrehozhat riasztásokat](../log-analytics/log-analytics-alerts.md) a számítógépekről hiányzó kritikus frissítések jelzésére vagy arra az estre, ha egy számítógép automatikus frissítése letiltott állapotba kerül.  
