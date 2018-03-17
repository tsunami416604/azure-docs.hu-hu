---
title: "Ismerkedés az Azure Automation szolgáltatással"
description: "Ez a cikk áttekintést az Azure Automation szolgáltatás. Azt ellenőrzi, hogy a tervezési és megvalósítási részletek előkészítésekor bevezetése az elérhető az Azure piactérről."
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: dab404178b45828732e137835213046cedaf0d03
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="get-started-with-azure-automation"></a>Ismerkedés az Azure Automation szolgáltatással

Ez a cikk az Azure Automation telepítésével kapcsolatos alapfogalmakat be. Ha az Azure Automation új vagy már ismerik a automation munkafolyamat szoftverek, mint a System Center Orchestrator, megtudhatja hogyan készíti elő és az alaplapi automatizálás. Ez a cikk elolvasása után készen az folyamat automation igényeinek megfelelő runbookok fejlesztése lesz. 


## <a name="automation-architecture-overview"></a>Az Automation-architektúra áttekintése

![Az Azure Automation áttekintése](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation a szoftver, mint egy szolgáltatott szoftverként (SaaS) alkalmazás, amely méretezhető és megbízható több-bérlős-környezetet biztosít, amelyben segítségével runbookokat automatizálására. Azure, a felhőszolgáltatásokra, a kívánt állapot konfigurációs szolgáltatása (DSC) is használhatja, vagy egy helyszíni környezet konfigurációjának kezelése a Windows és Linux rendszerek változik. Bejegyzései szerepelnek az Automation-fiók, beleértve a runbookok, eszközök és futtató fiókok választva egymástól az előfizetésében szereplő más Automation-fiók és más előfizetéseket.  

Az Azure-ban futtatott Runbookok Automation védőfalak hajtja végre. Védőfalak egy platformszolgáltatási virtuális gépként az Azure platformon üzemeltetett. 

Automatizálási védőfalak adja meg a runbook végrehajtása, beleértve a modulok, a tárolási, a memória, a hálózati kommunikáció és a feladat adatfolyamok minden aspektusára a bérlők elkülönítését. Ezt a szerepkört a szolgáltatás kezeli. Nem fér hozzá, vagy a szerepkör kezelése az Azure vagy az Automation-fiókból.         

A központi telepítési és a helyi adatközpontban, illetve más felhőszolgáltatásaival források kezelése automatizálására Automation-fiók létrehozása után, jelölhet ki egy vagy több virtuális gépek futtatásához a [hibrid forgatókönyv-feldolgozó](automation-hybrid-runbook-worker.md) szerepkör. Minden egyes hibrid forgatókönyv-feldolgozó Microsoft felügyeleti ügynököt kell telepíteni és Automation-fiók szükséges. Az ügynök Azure Log Analytics-munkaterülethez kapcsolattal kell rendelkeznie. Log Analytics segítségével a telepítés bootstrap kezelése a Microsoft Management Agent ügynököt, és figyelheti a hibrid forgatókönyv-feldolgozó működését. Azure Automation szolgáltatásbeli hajt végre a runbookok és futtatni szeretne ilyen utasítás kézbesítését.

Telepíthet több hibrid forgatókönyv-feldolgozók. Hibrid forgatókönyv-feldolgozók segítségével magas rendelkezésre állás biztosításához a runbookok és a terheléselosztás runbook-feladatok. Bizonyos esetekben használhat a runbook-feladatok az adott munkaterhelés konkrét vagy környezetben. A hibrid forgatókönyv-feldolgozót a Microsoft Monitoring Agent kezdeményezi a kommunikációt az Automation szolgáltatás a 443-as TCP-porton keresztül. Hibrid forgatókönyv-feldolgozók nincs bejövő tűzfal követelményekkel rendelkezik.  

Érdemes lehet a hibrid forgatókönyv-feldolgozók kezelési feladatok szemben a más gépeket vagy szolgáltatásokat az adott környezetben futó runbook. Adott esetben a runbook valószínűleg is hozzá kell más portok. Ha az IT-biztonsági házirendeknek nem engedélyezi a számítógépek, a hálózat csatlakozik az internethez, tekintse át a [OMS átjáró](../log-analytics/log-analytics-oms-gateway.md). Az Operations Management Suite (OMS) átjáró proxyként viselkedik, a hibrid forgatókönyv-feldolgozót a. Gyűjti a feladat állapotát, és konfigurációs adatokat fogad az Automation-fiók.

A hibrid forgatókönyv-feldolgozók a futó Runbookok a számítógépen futtassa a helyi rendszerfiók környezetében. Amikor felügyeleti műveleteket hajt végre, a helyi számítógépen a Windows biztonsági környezetben ajánlott. Ha azt szeretné, hogy a runbook feladatok futtatását, amelyek túlmutatnak a helyi számítógép erőforrásokon, szükség lehet a biztonságos hitelesítő eszközök az Automation-fiókot ad meg. Biztonságos hitelesítő eszközök elérje a runbookot, és segítségével a külső forrásnak a hitelesítést. Használhat [Credential](automation-credentials.md), [tanúsítvány](automation-certificates.md), és [kapcsolat](automation-connections.md) eszközök a runbookban. Az eszközök használata a parancsmagok, amelyekkel adja meg a hitelesítő adataival őket.

Azure Automation tárolt virtuális gépek DSC-konfigurációk is alkalmazhatja. Egyéb fizikai és virtuális gépek konfigurációi kérhetnek az Automation DSC lekérési kiszolgálójáról. Nem kell telepítenie minden infrastruktúra az Automation DSC lekérési kiszolgálójával való kezelését a helyszíni fizikai vagy virtuális Windows és Linux rendszerek támogatásához. Csak akkor kell minden egyes rendszerből Automation DSC használatával felügyelni kívánt kimenő internet-hozzáféréssel. Kommunikáció az OMS szolgáltatáshoz a 443-as TCP-porton keresztül történik.   

## <a name="prerequisites"></a>Előfeltételek

### <a name="automation-dsc"></a>Automation DSC
Automation DSC segítségével ezek a gépek kezelheti:

* Az Azure virtuális gépek (klasszikus) Windows vagy Linux rendszerű.
* Az Azure virtuális gépek Windows vagy Linux rendszerű.
* Amazon Web Services (AWS) a Windows vagy Linux rendszerű virtuális gépek.
* Fizikai és virtuális Windows rendszerű számítógépek, amelyek a helyszíni vagy felhőben működő Azure vagy az AWS kívül.
* Fizikai és virtuális Linux számítógépeket, amelyek a helyszíni vagy felhőben működő Azure vagy az AWS kívül.

A Windows-alapú gépek a legújabb Windows Management Framework (WMF) 5 rendszerre telepíthető. A Linux-gépek, a legújabb verzióját a [PowerShell DSC Linux-ügynök](https://www.microsoft.com/en-us/download/details.aspx?id=49150) kell telepíteni. A PowerShell DSC ügynök WMF 5 használ Automation folytatott kommunikációhoz. 

### <a name="hybrid-runbook-worker"></a>hibrid runbook-feldolgozó  
Ha kijelöl egy számítógépen, hogy hibrid forgatókönyv-feladatok futtatása, a számítógép a következő előfeltételeknek kell megfelelnie:

* Windows Server 2012 vagy újabb.
* Windows PowerShell 4.0 vagy újabb. A nagyobb megbízhatóságot ajánlott a Windows PowerShell 5.0. Is [az új verzió letöltése](https://www.microsoft.com/download/details.aspx?id=50395) a Microsoft Download Center webhelyről.
* .NET-keretrendszer 4.6.2-es verziójához vagy újabb.
* Legalább két magszámra vonatkozó követelménynek.
* Egy legalább 4 GB RAM-mal.

### <a name="permissions-required-to-create-an-automation-account"></a>Automation-fiók létrehozásához szükséges engedélyek
Az Automation-fiók létrehozása vagy frissítése, és a jelen cikkben ismertetett feladatok végrehajtásához, a következő jogosultságokkal és engedélyekkel kell rendelkeznie:   
 
* Automation-fiók létrehozásához az Azure Active Directory (Azure AD) felhasználói fiókot hozzá kell adni a tulajdonosi szerepkört, a megfelelő engedélyekkel rendelkező szerepkörhöz **Microsoft.Automation** erőforrásokat. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).  
* Az Azure portálon a **Azure Active Directory** > **kezelése** > **App regisztrációk**, ha **App regisztrációk**  értéke **Igen**, az Azure AD-bérlő nem rendszergazdai felhasználók számára is [regisztrálni az Active Directory alkalmazások](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions). Ha **App regisztrációk** értéke **nem**, ezt a műveletet végző felhasználót az Azure AD globális rendszergazdának kell lennie. 

Ha még nem tagja az Active Directory előfizetésre, ahhoz az előfizetéshez globális rendszergazda/coadministrator szerepkör hozzáadásakor, amelyek hozzáadott Active Directory vendégként. Ebben a forgatókönyvben a következő üzenet jelenik meg a **Automation-fiók hozzáadása** lap: "Nincs létrehozásához szükséges engedélyek." 

Ha egy felhasználó hozzáadódik a globális rendszergazda/coadministrator szerepkör először, akkor is távolítsa el azokat az Active Directory előfizetésre, és majd újra vegye fel a teljes Active Directory a felhasználói szerepkörhöz.

Felhasználói szerepkörök ellenőrzése:
1. Az Azure-portálon lépjen a **Azure Active Directory** ablaktáblán.
2. Válassza ki **felhasználók és csoportok**.
3. Válassza ki **minden felhasználó**. 
4. Egy adott felhasználó kiválasztása után válassza ki a **profil**. Értékét a **felhasználótípust** attribútum a profil alapján nem lehet **vendég**.

## <a name="authentication-planning"></a>Hitelesítés tervezése
Az Azure Automationben automatizálhatja a feladatokat, amelyek az Azure, a helyszíni és a felhőszolgáltatásokra erőforrásokon. A szükséges műveletek elvégzéséhez runbookra akkor segítségével biztonságosan hozzáférjenek a erőforrásokhoz engedélyekkel kell rendelkeznie. Az előfizetésen belül szükséges minimális engedélyeket kell rendelkeznie.  

### <a name="what-is-an-automation-account"></a>Mi az az Automation-fiók 
Azure Automation parancsmagokkal erőforrásokon végre minden automatizálási feladatok hitelesítést az Azure-bA az Azure AD identity szervezeti hitelesítő adatok-alapú hitelesítés.  Automation-fiók nem azonos a portálra való konfigurálásához és használatához Azure-erőforrások bejelentkezéshez használt fiók. 

A következő erőforrások találhatók Automation-fiók:

* **Tanúsítványok**. Található, amely runbook vagy DSC-konfiguráció-hitelesítéshez használt tanúsítvány. Tanúsítványok is hozzáadhat.
* **Kapcsolatok**. Egy runbook vagy a DSC-konfiguráció egy külső szolgáltatás vagy alkalmazás való kapcsolódáshoz szükséges hitelesítési és konfigurációs információt tartalmazza.
* **Hitelesítő adatok**. Tartalmaz egy **PSCredential** objektum, amely a biztonsági hitelesítő adatok, például a felhasználónévvel és jelszóval rendelkezik. A hitelesítő adatokra van szükség egy runbook vagy a DSC-konfiguráció hitelesítéséhez.
* **Integrációs modulok**. Automation-fiók mellékelt PowerShell-modulok. A PowerShell-modul segítségével futtassa a parancsmagokat a runbookok és a DSC-konfigurációk.
* **Ütemezések**. Indítsa el, vagy állítsa le a runbookot, egy megadott időpontban, beleértve az ismétlődő gyakoriságot ütemezéseket tartalmazza.
* **Változók**. A runbookot vagy a DSC-konfiguráció által kínált számokat tartalmazhat.
* **A DSC-konfigurációk**. PowerShell-parancsfájlok, amelyek egy operációs rendszer a szolgáltatás vagy a beállítás konfigurálásával vagy egy alkalmazás telepítése a Windows vagy Linux számítógép ismertetik.  
* **Runbookok**. Az automatizálás egy automatikus folyamat végző feladatokhoz Windows PowerShell alapján.    

Automatizálási erőforrások minden Automation-fiókhoz társított egyetlen Azure-régiót. Automation-fiók segítségével, az erőforrások kezelése az előfizetéshez. Ha olyan szabályzatokkal rendelkezik, amelyek az adatok és erőforrások adott régióban való elkülönítését írják elő, hozzon létre Automation-fiókokat különböző régiókban.

Automation-fiók létrehozása az Azure portálon, amikor két hitelesítési entitás automatikusan jönnek létre:

* **A Futtatás mint fiók**. Ezt a fiókot a következő feladatokat hajtja végre:
  - Az Azure AD-hoz létre egy egyszerű szolgáltatást.
  - Létrehoz egy tanúsítványt.
  - A Contributor Role-Based hozzáférés-vezérlés (RBAC), amely runbookok használatával kezeli az Azure Resource Manager erőforrások rendeli.
* **Klasszikus Futtatás mint fiók**. Ez a fiók feltölt egy felügyeleti tanúsítványt. A tanúsítvány runbookok használatával hagyományos erőforrások kezelésére szolgál.

Az RBAC érhető el a Resource Manager engedélyezett műveletek egy Azure AD-felhasználói fiókot, és a Futtatás mint fiók megadását. Az RBAC segítségével is, hogy a szolgáltatás egyszerű hitelesítést. További információt, és a fájlrendszersérülések kezelésének Automation engedélyek egy modelljét súgó, lásd: [szerepköralapú hozzáférés-vezérlés az Azure Automation-cikkben](automation-role-based-access-control.md).  

#### <a name="authentication-methods"></a>Hitelesítési módszerek
A következő táblázat összefoglalja a hitelesítési módszereket, amelyek, amely támogatja az Azure Automation környezetben.

| Módszer | Környezet 
| --- | --- | 
| Azure futtató és klasszikus futtató fiókok |Azure Resource Manager és klasszikus Azure üzemelő példány |  
| Azure AD felhasználói fiók |Azure Resource Manager és klasszikus Azure üzemelő példány |  
| Windows-hitelesítés |Helyi adatközpontban vagy más felhőalapú szolgáltatásokat szolgáltató a hibrid forgatókönyv-feldolgozói szerepkör használatával |  
| Amazon Web Services hitelesítő adatok |Amazon webszolgáltatások |  

A következő cikkekben e környezetek hitelesítés konfigurálása – áttekintés és megvalósításának lépéseit. A cikkek ismertetik a használatával egy meglévő, és egy új fiók használatával, amely akkor fordítsanak az adott környezetben. 
* [Önálló Azure Automation-fiók létrehozása](automation-create-standalone-account.md)
* [Runbookok hitelesítik magukat az Azure klasszikus üzembe helyezési és erőforrás-kezelő](automation-create-aduser-account.md)
* [Az Amazon Web Services Runbookok hitelesítéséhez](automation-config-aws-account.md)
* [Automatizálási futtató fiók frissítéséhez](automation-create-runas-account.md)

Az Azure-beli futtató és a klasszikus futtató fiókokhoz tartozó [frissítése Automation Futtatás mint fiók](automation-create-runas-account.md) ismerteti, hogyan lehet frissíteni a meglévő Automation-fiók a futtató fiókokat a portálról. Azt is bemutatja, hogyan használhatja a Powershellt, ha az Automation-fiók eredetileg nem volt konfigurálva futtató vagy a klasszikus futtató fiókkal. Létrehozhat egy futtató fiókot és egy klasszikus futtató fiókot a vállalati hitelesítésszolgáltatót (CA) által kiadott tanúsítvánnyal. Felülvizsgálati [frissítés Automation Futtatás mint fiók](automation-create-runas-account.md) megtudhatja, hogyan hozhat létre a fiókokat a konfiguráció használatával.     
 
## <a name="network-planning"></a>A hálózat megtervezése
A hibrid forgatókönyv-feldolgozó csatlakozni, és regisztrálhatja az OMS Szolgáltatáshoz a port számát és az URL-címeket ebben a szakaszban ismertetett elérésére kell legyen. Ez az kívül a [portok és a Microsoft Monitoring Agent szükséges URL-címek](../log-analytics/log-analytics-windows-agent.md) OMS Szolgáltatáshoz való kapcsolódáshoz. 

Ha az ügynök és az OMS közötti kommunikáció proxykiszolgálót használ, ellenőrizze, hogy a megfelelő erőforrásokon elérhető. Ha tűzfal használata az internetről való hozzáférésének korlátozásához, konfigurálnia kell a tűzfalat, hogy lehetővé teszik a hozzáférést.

A következő port és URL-címeket a hibrid forgatókönyv-feldolgozói szerepkör Automation folytatott kommunikációhoz szükségesek:

* Port: Csak a TCP 443-as kimenő internet-hozzáférés szükség.
* Global URL: *.azure-automation.net.

Ha egy Automation-fiók, amely egy adott területre van definiálva, korlátozhatja, hogy regionális adatközpontok kommunikációt. A következő táblázat a DNS-rekord mindegyik régióhoz.

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

Régió IP-címek helyett régió nevek listája, töltse le a [Azure Datacenter IP-cím](https://www.microsoft.com/download/details.aspx?id=41653) XML-fájlt a Microsoft Download Center webhelyről. 

> [!NOTE]
> Az Azure Datacenter IP-cím XML-fájl tartalmazza a Microsoft Azure adatközpontjaiban használt IP-címtartományok. Számítás-, SQL és a fájl szerepelnek. 
>
>A frissített fáljra hetente visszaküldi. A fájl az aktuálisan telepített tartománya és az IP-címtartományok jövőbeli módosításait tartalmazza. Új tartományok, amelyek szerepelnek a fájlban legalább egy hétig nem szerepel az adatközpontokban. 
>
> Célszerű minden héten az új XML-fájl letöltésére. Ezt követően frissítse a helyet megfelelő azonosításához az Azure-ban futó szolgáltatásokat. Az Azure ExpressRoute-felhasználók vegye figyelembe, hogy ezt a fájlt a Border Gateway Protocol (BGP) hirdetmény Azure terület minden hónap első hetében frissítéséhez használja. 
> 

## <a name="creating-an-automation-account"></a>Automation-fiók létrehozása

Az alábbi táblázatban bemutat módszerek az Automation-fiók létrehozása az Azure portálon. A táblázat minden egyes típusú üzembe helyezését, és a különbségeket.  

|Módszer | Leírás |
|-------|-------------|
| Válassza ki **Automation & vezérlő** az Azure piactéren | Az Azure piactéren elérhető létrehoz egy Automation-fiók és -OMS-munkaterület csatolt, és ugyanazt az erőforráscsoportot és régióban. Integráció a OMS is Log Analytics segítségével figyelheti és elemezheti a runbook feladat állapotát és a feladat adatfolyamok időbeli előnyeit. Használhatja a speciális funkciókat is Naplóelemzési eszkalálása vagy vizsgálja meg a problémákat. Az elérhető telepíti a **változások követése** és **frissítéskezelés** megoldások, amelyek alapértelmezés szerint engedélyezve vannak. |
| Válassza ki **Automation** a piactéren | Ez a módszer egy Automation-fiók egy új vagy meglévő erőforráscsoportot, amely nem kapcsolódik az OMS-munkaterület hoz létre. A rendelkezésre álló megoldások nem tartoznak bele a **Automation & vezérlő** kínál. Ez a módszer, amely bemutatja az Automation alapkonfigurációt. Útmutató runbookok és a DSC-konfigurációk írni, és a szolgáltatás funkcióinak használata segíthet. |
| Válassza ki **felügyeleti** megoldások | Ha egy **felügyeleti** megoldás, beleértve a [frissítéskezelés](../operations-management-suite/oms-solution-update-management.md), [indítása/leállítása virtuális gépek során munkaidőn kívüli](automation-solution-vm-management.md), vagy [változások követése](../log-analytics/log-analytics-change-tracking.md), a megoldás kéri, hogy válasszon ki egy meglévő Automation-fiókot és az OMS-munkaterület. A megoldást kínál az Automation-fiók és az OMS-munkaterület pedig ez szükséges lenne a megoldás az előfizetésében üzembe létrehozására. |

### <a name="create-an-automation-account-thats-integrated-with-oms"></a>Az OMS-integrációval Automation-fiók létrehozása
Az automatizálás bevezetésében, azt javasoljuk, hogy kiválassza a **Automation & vezérlő** kínál a piactéren. Ezzel a módszerrel automatizálási fiókot hoz létre, és létrejön egy OMS-munkaterület való integráció. Ha ezt a módszert használja, akkor is, hogy az elérhető felügyeleti megoldások telepítésére.  

[Önálló Automation-fiók létrehozása](automation-create-standalone-account.md) végigvezeti a bevezetése az Automation-fiók és az OMS-munkaterület létrehozása a **Automation & vezérlő** kínál. Megismerheti önálló tesztelési Automation-fiók létrehozásához, vagy tekintse meg a szolgáltatás.  

Az Automation-fiók és az OMS-munkaterület létrehozása használatával a **Automation & vezérlő** Piactéri ajánlat:

1. Jelentkezzen be egy olyan fiókkal, amely az előfizetés-Rendszergazdák szerepkör tagja, és az előfizetés egy coadministrator az Azure portálon.
2. Válassza ki **új**.<br><br> ![Válassza az új Azure-portálon](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. Keresse meg **Automation**. A keresési eredmények között, válassza ki a **Automation & vezérlő**.<br><br> ![Jelölje ki az Automation & vezérlés az Azure piactéren](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png).<br>   
4. Tekintse át az elérhető leírását, majd válassza ki **létrehozása**.  
5. A **Automation & vezérlő**, jelölje be **OMS-munkaterület**. A **OMS-munkaterület**, jelölje be az OMS-munkaterület, amely az Azure-előfizetés, amely az Automation-fiókhoz csatolva van. Ha egy OMS-munkaterület nem rendelkezik, válassza ki a **új munkaterület létrehozása**. A **OMS-munkaterület**: 
  1. A **OMS-munkaterület**, adjon meg egy nevet az új munkaterületet.
  2. A **előfizetés**, válasszon ki egy előfizetést kapcsolódik. Ha az alapértelmezés szerinti kiválasztás nem használni kívánt előfizetést, a legördülő listából válassza ki az előfizetést.
  3. A **erőforráscsoport**, hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot.  
  4. A **hely**, válasszon ki egy régiót. További információkért lásd: [Azure Automation is elérhető régiók](https://azure.microsoft.com/regions/services/). Két érhető el megoldások: réteg szabad és csomópontonként (OMS). Ingyenes szint van korlátozva, a megőrzési időtartam és a runbook-feladat futásidejű perc naponta, gyűjtött adatmennyiséget. Az egyes csomópontok (OMS) réteg nem rendelkezik maximális naponta gyűjtött adatok mennyisége.  
  5. Válassza az **Automation-fiók** elemet.  Ha létrehoz egy új OMS-munkaterület, az új OMS-munkaterület társított Automation-fiók is kell létrehoznia. Az Azure-előfizetéssel, erőforráscsoport és terület tartalmazza. 
    1. Válassza ki **Automation-fiók létrehozása**.
    2. A **Automation-fiók**, a a **neve** mezőbe írja be az Automation-fiók nevét.
    Egyéb beállítások automatikusan fel van töltve, a kiválasztott OMS-munkaterület alapján. Ezek a beállítások nem módosíthatók. 
    3. Az ajánlat alapértelmezett hitelesítési módszere egy Azure futtató fiók. Miután kiválasztotta a **OK**, a konfigurációs beállításokat érvényesíti, és az Automation-fiók létrehozása. Nyomon követéséhez a telepítés előrehaladását, a menüben válassza ki a **értesítések**. 
    4. Ellenkező esetben válasszon egy meglévő Automation futtató fiókot. A kiválasztott fiók egy másik OMS-munkaterület már nem csatolható. Ha van, egy értesítés jelenik meg. Ha a fiók már csatolva van egy OMS-munkaterület, jelöljön ki egy másik Automation futtató fiókot, vagy hozzon létre egyet.
    5. Azt követően adja meg vagy válassza ki a szükséges adatokat, válassza ki a **létrehozása**. Az adatokat a rendszer ellenőrzi, és az Automation-fiókot és futtató fiókok létrehozásához szükségesek. Automatikusan visszatér a **OMS-munkaterület** ablaktáblán.  
6. Adja meg vagy válassza ki a szükséges adatokat a után a **OMS-munkaterület** ablaktáblában válassza **létrehozása**.  Az adatokat a rendszer ellenőrzi, és a munkaterület létrehozását. Nyomon követéséhez a telepítés előrehaladását, a menüben válassza ki a **értesítések**. A rendszer visszairányítja a **megoldás hozzáadása** ablaktáblán.  
7. A **Automation & vezérlő** beállításokat, győződjön meg arról, hogy szeretné-e telepíteni az ajánlott előre kiválasztott megoldásokat. Ha megváltoztatja az alapértelmezett beállításokat, a megoldások külön-külön később is telepítheti.  
8. Bevezetési automatizálási és az OMS-munkaterület folytatásához válasszon **létrehozása**. Minden beállítás ellenőrzését, és Azure megpróbálja telepíteni az elérhető az előfizetésében. Ez a folyamat eltarthat néhány másodpercig. Nyomon követéséhez a telepítés előrehaladását, a menüben válassza ki a **értesítések**. 

Miután az elérhető előkészítve, tegye a következő feladatokat:
* Runbookok létrehozásának megkezdéséhez.
* Munka a felügyeleti megoldás, hogy engedélyezve van.
* Központi telepítése egy [hibrid forgatókönyv-feldolgozó](automation-hybrid-runbook-worker.md) szerepkör.
* A munka megkezdéséhez [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics) erőforrásoknak a felhő vagy a helyszíni környezetben által generált adatok gyűjtéséért felelős ügyfélfeladatot.   

## <a name="next-steps"></a>További lépések
* Győződjön meg arról, hogy az új Automation-fiók Azure-erőforrások végezhetnek hitelesítést. További információkért lásd: [teszt Azure Automation futtató fiók hitelesítési](automation-verify-runas-authentication.md).
* Útmutató a szerzői alapismeretek létrehozásának a runbookok és kapcsolódó szempontokat. További információkért lásd: [automatizálási runbook típusokat](automation-runbook-types.md).


