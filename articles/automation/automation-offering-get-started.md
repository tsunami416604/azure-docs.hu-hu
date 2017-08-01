--- 
title: "Bevezetés az Azure Automation használatába | Microsoft Docs"
description: "Ez a cikk a kialakítás és az implementálás részleteinek áttekintésével áttekintést nyújt az Azure Automation szolgáltatásról az Azure Marketplace-ről származó ajánlat bevezetésének előkészítéséhez."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/12/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 7ef31d7d72844c0ed3be0701549e49e26aac9abf
ms.contentlocale: hu-hu
ms.lasthandoff: 07/24/2017

---

# <a name="getting-started-with-azure-automation"></a>Bevezetés az Azure Automation használatába

Ez a kezdeti lépéseket ismertető útmutató az Azure Automation üzembe helyezésével kapcsolatos alapfogalmakat mutatja be. Ha még nem ismeri az Azure Automationt, vagy nincs tapasztalata a System Center Orchestratorhöz hasonló munkafolyamat-automatizálási szoftverekkel, ez az útmutató bemutatja az Automation előkészítését és bevezetését.  Ezt követően már felkészült lesz a folyamatautomatizálási szükségletek támogatására szolgáló runbookfejlesztés megkezdésére. 


## <a name="automation-architecture-overview"></a>Az Automation-architektúra áttekintése

![Az Azure Automation áttekintése](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Az Azure Automation olyan szolgáltatott szoftverként (SaaS) biztosított alkalmazás, amely megbízható és méretezhető, több-bérlős környezetet nyújt a folyamatok runbookok segítségével történő automatizálásához, illetve a Windows és Linux konfigurációs változásainak kezeléséhez a Célállapot-konfiguráció (DSC) szolgáltatással az Azure-ban, egyéb felhőszolgáltatókon, vagy a helyszíni erőforrásokon. Az Automation-fiókban tárolt entitások, például a runbookok, az adategységek, és a futtató fiókok elkülönülnek az adott előfizetésen és az egyéb előfizetéseken belüli Automation-fiókoktól.  

Az Azure-ban futtatott runbookok Automation-tesztkörnyezetekben futnak, amelyek szolgáltatásként nyújtott platformalapú (PaaS) Azure virtuális gépeken üzemelnek.  Az Automation-tesztkörnyezetek a runbookok futtatásának minden aspektusában biztosítják a bérlők elkülönítését, legyen szó modulokról, tárterületről, memóriáról, hálózati kommunikációról, feladatstreamekről stb. Ezt a szerepkört a szolgáltatás felügyeli, és a vezérlése nem érhető el az Ön Azure- vagy Azure Automation-fiókjából.         

A helyi adatközpontban vagy egyéb felhőszolgáltatásban tárolt erőforrások automatikus üzembe helyezéséhez vagy felügyeletéhez – Automation-fiók létrehozását követően – megadhat egy vagy több olyan gépet, amely a [hibrid runbook-feldolgozó (HRW)](automation-hybrid-runbook-worker.md) szerepkört futtatja.  Minden HRW-hez szükség van egy Log Analytics-munkaterülethez csatlakozó Microsoft Management Agentre és egy Automation-fiókra.  A Log Analytics a telepítés indításához, a Microsoft Management Agent karbantartásához és a HRW funkcióinak monitorozásához használható.  A runbookok kézbesítését és a futtatási utasítást az Azure Automation végzi.

Több HRW-t is üzembe helyezhet a runbook magas rendelkezésre állásának biztosításához, a runbookfeladatok terheléselosztásához, és bizonyos esetekben adott számítási feladatokhoz vagy környezetekhez való kiosztásukhoz.  A Microsoft Monitoring Agent a HRW-n kommunikációt kezdeményez az Automation szolgáltatással a 443-as TCP-porton, bejövő tűzfalra vonatkozó követelmények nélkül.  Ha már van a környezet egyik HRW-jén futó runbookja, és szeretné, hogy a runbook kezelési feladatokat végezzen a környezet más gépekein vagy szolgáltatásain, akkor előfordulhat, hogy más portokhoz is hozzáférést kell biztosítania a runbooknak.  Ha az informatikai biztonsági szabályzatok nem engedélyezik, hogy a hálózat számítógépei kapcsolódjanak az internetre, olvassa el az [OMS-átjáró](../log-analytics/log-analytics-oms-gateway.md) című cikket. Az átjáró proxyként működik a HRW számára, és gyűjti a feladatállapotokat, valamint konfigurációs információkat fogad az Automation-fióktól.

A HRW-n futó runbookok a számítógép helyi rendszerfiókjának kontextusában futnak, ami a helyi Windows-gép adminisztrációs műveleteinek végrehajtásához ajánlott biztonsági kontextus. Ha azt szeretné, hogy a runbook a helyi gépen kívüli erőforrásokon futtasson feladatokat, akkor lehetséges, hogy biztonságos hitelesítési objektumokat kell megadnia az Automation-fiókban, amelyeket elérhet a runbookból és használhat a külső erőforrással való hitelesítéshez. A [Hitelesítő adat](automation-credentials.md), [Tanúsítvány](automation-certificates.md) és [Kapcsolat](automation-connections.md) adategységeket olyan parancsmagokkal használhatja a runbookban, amelyekben hitelesítő adatokat is megadhat a hitelesítéshez.

Az Azure Automationben tárolt DSC-konfigurációk közvetlenül alkalmazhatók az Azure-beli virtuális gépekre. Egyéb fizikai és virtuális gépek az Azure Automation DSC lekérési kiszolgálóról kérhetnek konfigurációkat.  A helyszíni fizikai vagy virtuális Windows és Linux rendszerek konfigurációinak kezeléséhez nincs szükség az Automation DSC lekérési kiszolgálót támogató infrastruktúra üzembe helyezésére, csak arra, hogy az egyes rendszerek kimenő internetkapcsolatát az Automation DSC kezelje, és az OMS szolgáltatással kommunikáció a 443-as TCP porton keresztül történjen.   

## <a name="prerequisites"></a>Előfeltételek

### <a name="automation-dsc"></a>Automation DSC
Az Azure Automation DSC különféle gépek felügyeletéhez használható:

* Windows vagy Linux rendszerű (klasszikus) Azure-beli virtuális gépek
* Windows vagy Linux rendszerű Azure-beli virtuális gépek
* Windows vagy Linux rendszerű Amazon Web Services (AWS) virtuális gépek
* Fizikai/virtuális Windows rendszerű számítógépek a helyszínen, illetve nem Azure- és AWS-alapú felhőben
* Fizikai/virtuális Linux-számítógépek a helyszínen, illetve nem Azure- és AWS-alapú felhőben

Ahhoz, hogy a PowerShell DSC Windows-ügynök kommunikálni tudjon az Azure Automationnel, a WMF 5 legújabb verziója szükséges. Ahhoz, hogy a Linux kommunikálni tudjon az Azure Automationnel, a [PowerShell DSC Linux-ügynök](https://www.microsoft.com/en-us/download/details.aspx?id=49150) legfrissebb verziója szükséges.

### <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó  
A hibrid runbookfeladatok futtatására kijelölt számítógépnek az alábbiakkal kell rendelkeznie:

* Windows Server 2012 vagy újabb
* Windows PowerShell 4.0 vagy újabb.  A megbízhatóság növelése érdekében javasoljuk a Windows PowerShell 5.0 telepítését. A legújabb verziót letöltheti a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=50395)
* Minimum két mag
* Legalább 4 GB RAM

### <a name="permissions-required-to-create-automation-account"></a>Az Automation-fiók létrehozásához szükséges engedélyek
Automation-fiók létrehozásához vagy frissítéséhez az alábbi, a jelen témakör végrehajtásához szükséges jogosultságokkal és engedélyekkel kell rendelkeznie.   
 
* Automation-fiók létrehozásához az AD-felhasználói fiókot egy olyan szerepkörhöz kell hozzáadni, amely a Microsoft.Automation-erőforrások közreműködői szerepkörével egyenértékű engedélyekkel rendelkezik a [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md#contributor-role-permissions) című cikkben ismertetett módon.  
* Az Azure AD-bérlő nem rendszergazda jogosultságú felhasználói abban az esetben végezhetik el az [AD-alkalmazások regisztrálását](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions), ha az Alkalmazásregisztrációk beállítás értéke **Igen**.  Ha az Alkalmazásregisztrációk beállítás értéke **Nem**, ezt a műveletet csak az Azure AD globális rendszergazdái hajthatják végre. 

Ha nem tagja az előfizetéshez tartozó Active Directory-példánynak, mielőtt hozzáadják Önt az előfizetés globális rendszergazdai vagy társadminisztrátori szerepköréhez, vendégként lesz hozzáadva az Active Directoryhoz. Ebben az esetben „Nincs engedélye létrehozni…” figyelmeztető üzenetet kap az **Automation-fiók hozzáadása** panelen. A globális rendszergazdai vagy társadminisztrátori szerepkörhöz hozzáadott felhasználók először eltávolíthatók az előfizetéshez tartozó Active Directory-példányból, majd újra hozzáadhatók, így teljes jogú felhasználók lehetnek az Active Directoryban. Ez a helyzet úgy ellenőrizhető, ha az Azure Portal **Azure Active Directory** panelén a **Felhasználók és csoportok** és a **Minden felhasználó** elemre kattint, majd a konkrét felhasználó kiválasztása után a **Profil** elemet választja. A felhasználók profilja alatti **Felhasználó típusa** attribútum értéke ne legyen **Guest** (vendég).

## <a name="authentication-planning"></a>A hitelesítés tervezése
Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure-beli és helyszíni erőforrásokon, illetve egyéb felhőszolgáltatókon.  Annak érdekében, hogy a forgatókönyv elvégezze a szükséges műveleteket, engedélyekkel kell rendelkeznie az erőforrások biztonságos eléréséhez az előfizetésben szükséges minimális jogokkal.  

### <a name="what-is-an-automation-account"></a>Mi az az Automation-fiók? 
Minden automatizálási feladatot, amelyet az Azure Automation parancsmagjaival hajt végre az erőforrásokon, hitelesíteni kell az Azure Active Directory szervezeti identitáshitelesítésével.  Az Automation-fiók nem azonos az Azure-erőforrások konfigurációjakor és használatakor a portálra való bejelentkezéshez használt fiókkal.  Az egyes fiókokban a következő Automation-erőforrások találhatóak meg:

* **Tanúsítványok** – runbookból vagy DSC-konfigurációból történő hitelesítéshez vagy azok hozzáadásához használt tanúsítványt tartalmaz.
* **Kapcsolatok** – olyan hitelesítési és konfigurációs adatokat tartalmaz, amelyek egy runbook vagy DSC-konfiguráció és egy külső szolgáltatás vagy alkalmazás közötti kapcsolathoz szükségesek.
* **Hitelesítő adatok** – PSCredential objektum, amely olyan biztonsági hitelesítő adatokat tartalmaz, mint a runbookból vagy DSC-konfigurációból történő hitelesítéshez szükséges felhasználónév és jelszó.
* **Integrációs modulok** – Az Azure Automation-fiókok részét képező PowerShell-modulok a parancsmagok runbookokon vagy DSC-konfigurációkon belüli használatához.
* **Ütemezések** – a runbookokat adott időpontban elindító vagy leállító ütemezéseket tartalmaz, beleértve az ismétlődő gyakoriságot is.
* **Változók** – runbookból vagy DSC-konfigurációból elérhető értékeket tartalmaznak.
* **DSC-konfigurációk** – PowerShell-szkriptek, amelyek útmutatás nyújtanak az operációs rendszer egy szolgáltatásának vagy beállításának konfigurálásához, vagy egy alkalmazás Windows vagy Linux rendszerű számítógépre történő telepítéséhez.  
* **Runbookok** – feladatkészletek, amelyek bizonyos automatizált folyamatokat hajtanak végre az Azure Automationben a Windows PowerShell alapján.    

Az Azure-fiókokhoz tartozó Automation-erőforrások egy Azure-régióhoz tartoznak, de az Automation-fiókok képesek az előfizetés összes erőforrását kezelni. Ha olyan szabályzatokkal rendelkezik, amelyek az adatok és erőforrások adott régióban való elkülönítését írják elő, hozzon létre Automation-fiókokat különböző régiókban.

> [!NOTE]
> Az Azure portálon létrehozott Automation-fiókok, valamint a rajtuk tárolt erőforrások nem érhetők el a klasszikus Azure portálról. Ha ezeket fiókokat vagy az erőforrásaikat Windows PowerShellel felügyeli, az Azure Resource Manager modulokat kell használnia.
> 

Amikor létrehoz egy Automation-fiókot az Azure Portalon, automatikusan két hitelesítési entitás jön létre:

* Egy futtató fiók. Ez a fiók létrehoz egy egyszerű szolgáltatást az Azure Active Directory-ban (Azure AD), valamint egy tanúsítványt. Emellett kiosztja a Közreműködő szerepköralapú hozzáférés-vezérlést (RBAC), amely runbookok használatával kezeli a Resource Manager-erőforrásokat.
* Egy klasszikus futtató fiókot. Ez a fiók feltölt egy felügyeleti tanúsítványt, amellyel runbookok használatával kezelheti a klasszikus erőforrásokat.

A szerepköralapú hozzáférés-vezérlés az Azure Resource Managerben érhető el, hogy hozzáférést adjon az engedélyezett műveleteknek egy Azure AD-felhasználói fiókhoz és futtatófiókhoz, és hitelesítse az egyszerű szolgáltatást.  Az Automation-engedélyek kezelésére használt modell fejlesztésére vonatkozó további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md) című cikket.  

#### <a name="authentication-methods"></a>Hitelesítési módszerek
A következő táblázat összefoglalja az Azure Automation által támogatott összes környezet különböző hitelesítési módszereit.

| Módszer | Környezet 
| --- | --- | 
| Azure futtató és klasszikus futtató fiókok |Azure Resource Manager és klasszikus Azure üzemelő példány |  
| Azure AD felhasználói fiók |Azure Resource Manager és klasszikus Azure üzemelő példány |  
| Windows-hitelesítés |Hibrid runbook-feldolgozót használó helyszíni adatközpont vagy egyéb felhőszolgáltató |  
| AWS hitelesítő adatok |Amazon webszolgáltatások |  

Az **Útmutató\Hitelesítés és biztonság** szakaszban támogató cikkeket talál, amelyek áttekintést nyújtanak a témáról, és bemutatják a szóban forgó környezetek hitelesítésének konfigurálásához szükséges implementációs lépéseket az adott környezethez kijelölt meglévő vagy új fiók esetén.  Azure-beli futtató fiók és klasszikus futtató fiók esetében az [Automation futtató fiók frissítésével kapcsolatos](automation-create-runas-account.md) témakör mutatja be, hogyan frissíthető a futtató fiókokat tartalmazó meglévő Automation-fiók a portálról vagy a PowerShell-lel, ha eredetileg nem lett futtató vagy klasszikus futtató fiókkal konfigurálva. Ha egy futtató és egy klasszikus futtató fiókot szeretne létrehozni a vállalati hitelesítésszolgáltató által kibocsátott tanúsítvánnyal, ebből a cikkből betekintést nyerhet a fiókoknak ezen konfigurációval történő létrehozásába.     
 
## <a name="network-planning"></a>Hálózattervezés
Ahhoz, hogy a hibrid runbook-feldolgozó kapcsolódni és regisztrálni tudjon a Microsoft Operations Management Suite (OMS) szolgáltatásban, hozzáféréssel kell rendelkeznie az alább ismertetett portszámokhoz és URL-címekhez.  Ez kiegészítésként szolgál a [Microsoft Monitoring Agent OMS-csatlakozásához szükséges portok és URL-címek](../log-analytics/log-analytics-windows-agents.md#network) listájához. Ha proxykiszolgálót használ az ügynök és az OMS szolgáltatás közötti kommunikációhoz, győződjön meg arról, hogy a megfelelő erőforrások elérhetők. Ha tűzfallal korlátozza az internethez való hozzáférést, akkor a tűzfalat úgy kell beállítani, hogy engedélyezze a hozzáférést.

Az alábbi lista a hibrid runbook-feldolgozó és az Automation szolgáltatás közötti kommunikációhoz szükséges portokat és URL-címeket tartalmazza.

* Port: a kimenő internetkapcsolathoz csak a 443-as TCP port szükséges
* Globális URL: *.azure-automation.net

Ha rendelkezik egy adott régióhoz meghatározott Automation-fiókkal, és szeretné korlátozni a régió adatközpontjával való kommunikációt, az egyes régiók DNS-rekordját az alábbi táblázatban találja.

| **Régió** | **DNS-rekord** |
| --- | --- |
| USA déli középső régiója |scus-jobruntimedata-prod-su1.azure-automation.net |
| USA 2. keleti régiója |eus2-jobruntimedata-prod-su1.azure-automation.net |
| USA nyugati középső régiója | wcus-jobruntimedata-prod-su1.azure-automation.net |
| Nyugat-Európa |we-jobruntimedata-prod-su1.azure-automation.net |
| Észak-Európa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Közép-Kanada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Délkelet-Ázsia |sea-jobruntimedata-prod-su1.azure-automation.net |
| Közép-India |cid-jobruntimedata-prod-su1.azure-automation.net |
| Kelet-Japán |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Délkelet-Ausztrália |ase-jobruntimedata-prod-su1.azure-automation.net |
| Az Egyesült Királyság déli régiója | uks-jobruntimedata-prod-su1.azure-automation.net |
| USA-beli államigazgatás – Virginia | usge-jobruntimedata-prod-su1.azure-automation.us |

Ha a nevek helyet az IP-címek listájára van szüksége, töltse le és tekintse át az [Azure-adatközponti IP-címeket](https://www.microsoft.com/download/details.aspx?id=41653) tartalmazó XML-fájlt a Microsoft letöltőközpontból. 

> [!NOTE]
> Ez a fájl azokat az IP-címtartományokat (beleértve Compute, SQL és Storage tartományokat) tartalmazza, amelyeket a Microsoft Azure adatközpontokban használnak. Hetente közzétesznek egy frissített fájlt, amely bemutatja a jelenleg üzembe helyezett tartományokat és minden, az IP tartományokat érintő közelgő változást. A fájlban megjelenő új tartományokat legalább egy hétig nem használják még az adatközpontok. Töltse le az új XML-fájlt minden héten, és végezze el a szükséges változtatásokat oldalán, hogy helyesen azonosítsa az Azure-ban futó szolgáltatásokat. Az Express Route felhasználóinak feltűnhet, hogy régen ez a fájl frissítette az Azure címterületek BGP-közzétételét minden hónap első hetében. 
> 

## <a name="creating-an-automation-account"></a>Automation-fiók létrehozása

Az Automation-fiók többféleképpen létrehozható az Azure Portalon.  Az alábbi táblázat az üzembe helyezés különböző típusait és a köztük lévő különbségeket ismerteti.  

|Módszer | Leírás |
|-------|-------------|
| Az Automatizálás és vezérlés kiválasztása a Marketplace-en | Olyan ajánlat, amely egy egymáshoz kapcsolódó Automation-fiókot és OMS-munkaterületet hoz létre ugyanabban az erőforráscsoportban és régióban.  Az OMS-integráció révén a runbookfeladatok állapotának és a feladatstreamek időbeli monitorozásához és elemzéséhez, valamint a problémák eszkalálására és kivizsgálására alkalmas speciális szolgáltatások használatához elérhető a Log Analytics szolgáltatás. Az ajánlat az alapértelmezés szerint engedélyezett változáskövetési és frissítéskezelési megoldásokat is üzembe helyezi. |
| Az Automatizálás elem kiválasztása a Marketplace-en | Létrehoz egy Automation-fiókot egy új vagy meglévő erőforráscsoportban. A fiók nem kapcsolódik OMS-munkaterülethez, és nem tartalmazza az Automatizálás és vezérlés ajánlatban elérhető megoldásokat. Ez az alapkonfiguráció bevezetésként szolgál az Automation szolgáltatásba, segít a runbookírás és a DSC-konfigurációk elsajátításában, valamint bemutatja a szolgáltatás képességeinek használatát. |
| Kiválasztott felügyeleti megoldások | Ha kiválaszt egy megoldást – **[Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md)**, **[Virtuális gépek indítása és leállítása munkaidőn kívül](automation-solution-vm-management.md)**, vagy **[Változáskövetés](../log-analytics/log-analytics-change-tracking.md)**, akkor ki kell választania egy meglévő Automation-fiókot és OMS-munkaterületet, vagy a rendszer felkínálja, hogy mindkettőt hozza létre az előfizetésben üzembe helyezni kívánt megoldás szükségletei alapján. |

Ez a témakör bemutatja az Automation-fiók és az OMS-munkaterület létrehozását az Automatizálás és vezérlés ajánlat bevezetésével.  A tesztelésre és a szolgáltatás kipróbálására szolgáló önálló Automation-fiók létrehozásáról tekintse meg a következő cikket: [Önálló Automation-fiók létrehozása](automation-create-standalone-account.md).  

### <a name="create-automation-account-integrated-with-oms"></a>OMS-sel integrált Automation-fiók létrehozása
Az Automation bevezetésének ajánlott módja a Marketplace Automatizálás és vezérlés ajánlatának használata.  Ez létrehoz egy Automation-fiókot, és biztosítja az integrációt egy OMS-munkaterülettel, valamint lehetőséget nyújt az ajánlattal együtt elérhető kezelési megoldások telepítésére.  

1. Jelentkezzen be az Azure Portal webhelyre egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörhöz tartozó csoportnak, és emellett az előfizetés társadminisztrátorának is számít.

2. Kattintson az **Új** lehetőségre.<br><br> ![Válassza az Új lehetőséget az Azure Portalon](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. Keressen rá az **Automatizálás** kifejezésre, majd a találatok listájában válassza az **Automatizálás és vezérlés*** lehetőséget.<br><br> ![Az Automatizálás és vezérlés elem keresése és kiválasztása a Marketplace-en](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   

4. Az ajánlat leírásának elolvasása után kattintson a **Létrehozás** gombra.  

5. Az **Automatizálás és vezérlés** beállításainak paneljén válassza az **OMS-munkaterület** elemet.  Az **OMS-munkaterületek** panelen válassza ki az Automation-fiókot tartalmazó Azure-előfizetéshez kapcsolódó OMS-munkaterületet, vagy hozzon létre egy újat.  Ha nem rendelkezik OMS-munkaterülettel, válassza az **Új munkaterület létrehozása** lehetőséget, és az **OMS-munkaterület** panelen végezze el az alábbiakat: 
   - Adja meg az új **OMS-munkaterület** nevét.
   - A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   - Az **Erőforráscsoport** területen létrehozhat egy erőforráscsoportot, vagy kiválaszthat egy meglévőt.  
   - Válasszon ki egy **helyet**.  Jelenleg csak a következő helyek közül választhat: **Délkelet-Ausztrália**, **USA keleti régiója**, **Délkelet-Ázsia**, **USA nyugati középső régiója** és **Nyugat-Európa**.
   - Válasszon egy tarifacsomagot a **Tarifacsomag** területen.  A megoldás két tarifacsomagban érhető el: ingyenes és csomópontalapú (OMS-) csomagban.  Az ingyenes csomagnál korlátozva van a naponta összegyűjtött adatok mennyisége, a megőrzési időtartam és a runbook-feladatok futásideje (perc).  A csomópontalapú (OMS) csomagnál a naponta összegyűjtött adatok mennyisége nincs korlátozva.  
   - Válassza az **Automation-fiók** elemet.  Új OMS-munkaterület létrehozásakor egy hozzá társított új Automation-fiókot is létre kell hoznia, valamint meg kell adnia az Azure-előfizetését, az erőforráscsoportot és a régiót.  Kiválaszthatja az **Automation-fiók létrehozása** lehetőséget, és az **Automation-fiók** panelen megadhatja a következő adatokat: 
  - A **Név** mezőbe írja be az Automation-fiók nevét.

    A rendszer a kiválasztott OMS-munkaterület alapján automatikusan kitölti az összes többi beállítást, amelyek utána nem módosíthatók.  Az ajánlat alapértelmezett hitelesítési módszere egy Azure futtató fiók.  Miután rákattintott az **OK** gombra, a rendszer érvényesíti a konfigurációs beállításokat, és létrehozza az Automation-fiókot.  Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását. 

    Ellenkező esetben válasszon egy meglévő Automation futtató fiókot.  Nem választhat másik OMS-munkaterülethez már kapcsolódó fiókot, ellenkező esetben a panelen egy figyelmeztető üzenet jelenik meg.  Ha a kiválasztott fiók már össze van kapcsolva a munkaterülettel, egy másik Automation futtató fiókot kell választania, vagy létre kell hoznia egyet.

    A szükséges információk megadása után kattintson a **Létrehozás** elemre.  A rendszer ellenőrzi a megadott adatokat, majd létrehozza az Automation futtató fiókokat.  Ezután automatikusan visszairányítja az **OMS-munkaterület** panelre.  

6. Miután az **OMS-munkaterület** panelen megadta a szükséges adatokat, kattintson a **Létrehozás** gombra.  Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet.  Ezután visszatér a **Megoldás hozzáadása** panelre.  

7. Az **Automatizálás és vezérlés** beállításainak paneljén erősítse meg, hogy telepíteni kívánja az előre kiválasztott megoldásokat. Ha bármelyiket kihagyja, később külön is telepítheti.  

8. Kattintson a **Létrehozás** elemre, és folytassa az eljárást az Automation és az OMS-munkaterület bevezetésével. A rendszer érvényesíti az összes beállítást, majd megpróbálja üzembe helyezni az ajánlatot az előfizetésében.  A folyamat eltarthat néhány másodpercig, az előrehaladását nyomon követheti az **Értesítések** menüpont alatt. 

Az ajánlat bevezetését követően hozzáláthat a runbookok létrehozásához, az engedélyezett felügyeleti megoldások használatához, egy [hibrid runbook-feldolgozó](automation-hybrid-runbook-worker.md) szerepkör üzembe helyezéséhez, vagy a felhőalapú vagy helyszíni környezet erőforrásai által előállított adatok [Log Analytics](https://docs.microsoft.com/azure/log-analytics) segítségével történő gyűjtéséhez.   

## <a name="next-steps"></a>Következő lépések
* Ellenőrizheti, hogy az új Automation-fiók el tudja végezni a hitelesítést az Azure-erőforrásokkal: [Azure Automation futtató fiók hitelesítésének tesztelése](automation-verify-runas-authentication.md).
* A runbookok létrehozásának első lépéseihez először tekintse át a támogatott [Automation-runbooktípusokat](automation-runbook-types.md) és a kapcsolódó szempontokat.



