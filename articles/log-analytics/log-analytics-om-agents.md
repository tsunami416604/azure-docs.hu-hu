---
title: Az Operations Manager csatlakoztatása a Log Analyticshez | Microsoft Docs
description: Meglévő System Center Operations Manager-befektetései kamatoztatása, és a Log Analytics képességeinek kiterjesztése érdekében az Operations Managert integrálhatja munkaterületével.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 6ed1d9fc810bfa14beed83609d13c1010d479ed9
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622245"
---
# <a name="connect-operations-manager-to-log-analytics"></a>Az Operations Manager csatlakoztatása a Log Analyticshez
A meglévő befektetéseit a [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) és kiterjesztett képességek használata a Log Analytics, az Operations Manager integrálása a Log Analytics-munkaterületre. Így kiaknázhatja a Log Analytics lehetőségeit, miközben továbbra is használhatja az Operations Managert a következőkre:

* Az informatikai szolgáltatások állapotának monitorozása az Operations Managerrel
* Az incidens- és problémakezelést támogató ITSM-megoldásokkal való integráció megőrzése
* Az Operations Managerrel monitorozott helyszíni és a nyilvános felhőn üzemelő IaaS-alapú virtuális gépeken telepített ügynökök életciklusának felügyelete

A System Center Operations Managerrel való integráció növeli szolgáltatásüzemeltetési stratégiái értékét, mivel az Operations Managerből származó adatok gyűjtését, tárolását és elemzését a Log Analytics sebességével és hatékonyságával végzi. A Log Analytics segít összerendelni és azonosítani a problémák és a felmerülő ismétlődő jelenségek hátterében rejlő hibákat, és ezzel támogatja meglévő problémakezelési folyamatait. A keresőmotor a teljesítmény-, esemény- és riasztásadatok vizsgálatában megfigyelhető rugalmassága, valamint az adatok értelmező megjelenítését szolgáló kiterjedt irányítópult- és jelentéskészítési funkcionalitása jól mutatják, hogy a Log Analytics milyen hatékonyan képes kiegészíteni az Operations Manager működését.

Az Operations Manager felügyeleti csoportnak jelentő ügynökök a munkaterületen engedélyezett Log Analytics-adatforrások és -megoldások alapján gyűjtik az adatokat a kiszolgálókról. Az engedélyezett megoldásoktól függően az adatokat vagy egy Operations Manager-felügyeleti kiszolgáló küldi a szolgáltatásnak, vagy az ügynök által felügyelt rendszeren gyűjtött adatok mennyisége miatt közvetlenül az ügynök küldi a Log Analyticsnek. A felügyeleti kiszolgáló az adatokat közvetlenül továbbítja a szolgáltatásnak, és azok soha nem lesznek az éles vagy az adattárház-adatbázisba írva. Ha egy felügyeleti kiszolgáló Log Analytics-kapcsolata megszakad, a kiszolgáló az adatokat helyileg gyorsítótárazza, amíg a kommunikáció helyre nem áll a Log Analyticsszel. Ha a felügyeleti kiszolgáló előre tervezett karbantartások vagy nem tervezett leállás miatt offline állapotba lép, a felügyeleti csoport egy másik felügyeleti kiszolgálója veszi át és folytatja a kapcsolattartást a Log Analyticsszel.  

Az alábbi diagram egy System Center Operations Manager-beli felügyeleti csoport felügyeleti kiszolgálói és ügynökei, illetve a Log Analytics közötti kapcsolatot mutatja be, az irányokat és a portokat is feltüntetve.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Ha az informatikai biztonsági szabályzatok nem engedélyezik a számítógépek a hálózat csatlakozik az internethez, felügyeleti kiszolgálók beállítható úgy, hogy a konfigurációs adatokat fogad és küld attól függően, a megoldások összegyűjtött adatok Log Analytics-átjárón engedélyezve van. További információkat és lépéseket az Operations Manager felügyeleti csoportban való kommunikációhoz a Log Analytics szolgáltatásnak egy Log Analytics-átjárón keresztül konfigurálásával: [számítógépek csatlakoztatása a Log Analytics használata a Log Analytics-átjáró](log-analytics-oms-gateway.md).  

## <a name="prerequisites"></a>Előfeltételek 
Mielőtt hozzákezdene, tekintse át az alábbi követelményeknek.

* Csak a log Analytics támogatja a System Center Operations Manager 1807, az Operations Manager 1801-re, az Operations Manager 2016-ra, az Operations Manager 2012 SP1 UR6 vagy újabb, és az Operations Manager 2012 R2 UR2 vagy újabb. A proxytámogatás az Operations Manager 2012 SP1 UR 7-es és az Operations Manager 2012 R2 UR 3-as verziójában jelent meg.
* Minden Operations Manager-ügynöknek meg kell felelnie a minimális támogatási feltételeknek. Győződjön meg arról, hogy ügynök a minimális frissítéskor el, egyéb Windows-ügynök kommunikációja sikertelen és hibák készítése az Operations Manager eseménynaplójában.
* Egy Log Analytics-munkaterület. További információkért tekintse át a [Log Analytics-munkaterület áttekintése](log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) ).
* Végez hitelesítést az Azure-bA egy olyan fiókkal, amely tagja a [Log Analytics-közreműködő szerepkör](log-analytics-manage-access.md#manage-accounts-and-users).  

>[!NOTE]
>Útmutató legutóbbi módosításait az Azure API-k megakadályozza, hogy a felhasználók képes arra, hogy sikeresen az első alkalommal között a felügyeleti csoport és a Log Analytics-integráció konfigurálása. Azok a vásárlóknak, akik már integrálva van a felügyeleti csoport a szolgáltatásban nem érinti, kivéve, ha újra kell konfigurálni a meglévő kapcsolatot.  
>Az Operations Manager egyes verzióihoz kiadása egy új felügyeleti csomagot:  
>* Töltse le a felügyeleti csomag a System Center Operations Manager 1801 [Itt](https://www.microsoft.com/download/details.aspx?id=57173)  
>* Töltse le a felügyeleti csomag a System Center 2016 – Operations Manager [Itt](https://www.microsoft.com/download/details.aspx?id=57172)  
>* A System Center Operations Manager 2012 R2, töltse le a felügyeleti csomag [Itt](https://www.microsoft.com/en-us/download/details.aspx?id=57171)  

### <a name="network"></a>Network (Hálózat)
Az alábbi lista az Operations Management-ügynök, a felügyeleti kiszolgálók és az Operatív konzol a Log Analyticsszel való kommunikációhoz szükséges proxy- és tűzfal-konfigurációját tartalmazza. Az egyes összetevők forgalma kifelé, a hálózatból a Log Analytics szolgáltatásra irányul.   

|Erőforrás | Portszám| HTTP-ellenőrzés kihagyása|  
|---------|------|-----------------------|  
|**Ügynök**|||  
|\*.ods.opinsights.azure.com| 443 |Igen|  
|\*.oms.opinsights.azure.com| 443|Igen|  
|\*.blob.core.windows.net| 443|Igen|  
|\*.azure-automation.net| 443|Igen|  
|**Felügyeleti kiszolgáló**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Igen|  
|\*.ods.opinsights.azure.com| 443| Igen|  
|*.azure-automation.net | 443| Igen|  
|**Operations Manager konzolján a Log Analyticsbe**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 és 443||  
|\*.microsoft.com| 80 és 443||  
|\*.microsoftonline.com| 80 és 443||  
|\*.mms.microsoft.com| 80 és 443||  
|login.windows.net| 80 és 443||  
|portal.loganalytics.io| 80 és 443||
|api.loganalytics.io| 80 és 443||
|docs.loganalytics.io| 80 és 443||  

### <a name="tls-12-protocol"></a>A TLS 1.2 protokoll
A Log Analytics az átvitt adatok biztonságának biztosítása érdekében, Határozottan javasoljuk, hogy használatához legalább az ügynök és a felügyeleti csoport konfigurálása Transport Layer Security (TLS) 1.2-es. Biztonsági rés található régebbi verziói a TLS/Secure Sockets Layer (SSL), és jelenleg továbbra is működnek, hogy a visszamenőleges kompatibilitás, amíg azok **nem ajánlott**. További információkért tekintse át a [biztonságosan a TLS 1.2 használatával az adatok küldésének](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

## <a name="connecting-operations-manager-to-log-analytics"></a>Az Operations Manager csatlakoztatása a Log Analyticshez
Az alábbi lépések végrehajtásával konfigurálhatja Operations Manager felügyeleti csoportját, hogy a Log Analytics munkaterületei egyikéhez csatlakozzon.

Az Operations Manager felügyeleti csoportban a Log Analytics-munkaterületet a kezdeti regisztráció során adhat meg a proxykonfiguráció a felügyeleti csoport nem áll rendelkezésre az operatív konzolon.  A felügyeleti csoportot sikeresen regisztrálni kell a szolgáltatásban ahhoz, hogy ez a lehetőség rendelkezésre álljon.  Ez elkerülhető, hogy frissíteni szeretné a rendszer proxykonfigurációt a Netsh segédprogrammal a rendszer a fut az operatív konzol integrációt és az összes felügyeleti kiszolgáló konfigurálása a felügyeleti csoportban.  

1. Nyisson meg egy emelt szintű parancssort.
   a. Lépjen a **Start** , és írja be **cmd**.
   b. Kattintson a jobb gombbal **parancssor** , és válassza ki futtató rendszergazda **.
1. Írja be a következő parancsot, majd nyomja le az **Enter** billentyűt:

    `netsh winhttp set proxy <proxy>:<port>`

Log Analytics szolgáltatással integrálható a következő lépések elvégzése után eltávolíthatja a konfiguráció futtatása `netsh winhttp reset proxy` , majd a **Configure proxy server** lehetőség az operatív konzolon adja meg a proxy vagy a napló Analytics-átjáró kiszolgáló. 

1. Nyissa meg az Operatív konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Bontsa ki az Operations Management Suite-csomópontot, és kattintson a **Kapcsolat** elemre.
1. Kattintson a **Register to Operations Management Suite** (Regisztráció az Operations Management Suite-be) hivatkozásra.
1. Az a **Operations Magement Suite előkészítési varázslója: hitelesítés** lapon adja meg az e-mail cím vagy telefonszám és jelszót a rendszergazdai fiók, amely az OMS-előfizetés társítva van, és kattintson a  **Jelentkezzen be a**.

   >[!NOTE]
   >OMS most már a Log Analytics nevezik.
   
1. Sikeres hitelesítés után, az a **Operations Management Suite előkészítési varázslója: Munkaterület kiválasztása** lap kéri, válassza ki az Azure-bérlő, az előfizetést és a Log Analytics-munkaterületet. Ha több munkaterülettel is rendelkezik, válassza ki a legördülő listából azt, amelyiket az Operations Manager felügyeleti csoportba regisztrálni szeretne, majd kattintson a **Tovább** gombra.
   
   > [!NOTE]
   > Az Operations Manager egyszerre csak egy Log Analytics-munkaterület használatát támogatja. Az előző munkaterület a Log Analyticsbe regisztrált kapcsolati és a számítógépekre vonatkozó adatai törölve lesznek a Log Analyticsből.
   > 
   > 
1. Az **Operations Management Suite Előkészítési varázsló Összefoglalás lapján** ellenőrizze a beállításokat, és ha rendben találja azokat, kattintson a **Létrehozás** gombra.
1. Az **Operations Management Suite Előkészítési varázsló Befejezés lapján** kattintson a **Bezárás** gombra.

### <a name="add-agent-managed-computers"></a>Ügynök által felügyelt számítógépek hozzáadása
Integrációjának konfigurálása után az a Log Analytics-munkaterülethez, csak a szolgáltatás kapcsolatot létesít, nem a gyűjtött adatokat a felügyeleti csoportnak jelentő ügynököktől. Ez nem fordulhat elő, amíg mely adott ügynök által felügyelt számítógépek gyűjt adatokat a Log Analytics konfigurálása után. A számítógép-objektumokat kiválaszthatja egyenként, vagy kiválaszthat egy Windows számítógép-objektumokat tartalmazó csoportot. Nem választhat olyan csoportot, amely egy másik osztályba tartozó példányokat, például logikai lemezeket vagy SQL-adatbázisokat tartalmaz.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Bontsa ki az Operations Management Suite-csomópontot, és kattintson a **Kapcsolat** elemre.
1. Kattintson a **Számítógép/csoport hozzáadása** hivatkozásra a panel jobb oldalán a Műveletek cím alatt.
1. A **Számítógép keresése** párbeszédpanelen az Operations Manager által megfigyelt számítógépekre vagy csoportokra kereshet. Válassza ki azokat a számítógépeket vagy csoportokat, amelyeket be szeretne vezetni a Log Analyticsbe, és kattintson a **Hozzáadás**, majd az **OK** gombra.

A Felügyelt számítógépek csomópont adatgyűjtésre konfigurált számítógépei és csoportjai az Operations Management Suite-ben az Operatív konzol **Adminisztráció** munkaterületén láthatók. Itt szükség szerint hozzá is adhat és el is távolíthat számítógépeket és csoportokat.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Proxybeállítások konfigurálása az Operatív konzolon
Hajtsa végre az alábbi lépéseket, ha egy belső proxykiszolgáló a felügyeleti csoport és a Log Analytics szolgáltatás között. Ezek a beállítások központilag vannak felügyelve a felügyeleti csoportból, és onnan kiküldve a Log Analytics adatgyűjtési hatókörébe tartozó, ügynök által felügyelt rendszerekre.  Ez hasznosnak bizonyul, ha egyes megoldások megkerülik a felügyeleti kiszolgálót, és közvetlenül a szolgáltatásba küldik az adatokat.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Bontsa ki a Microsoft Operations Management Suite elemet, majd kattintson a **Kapcsolatok** gombra.
1. Az OMS Connection (OMS-kapcsolat) nézetben kattintson a **Configure Proxy Server** (Proxykiszolgáló konfigurálása) lehetőségre.
1. Az **Operational Management Suite varázsló Proxykiszolgáló lapján** válassza a **Use a proxy server to access the Operations Management Suite** (Proxykiszolgáló használata az Operations Management Suite eléréséhez) lehetőséget, majd írja be az URL-címet a portszámmal együtt (például: http://corpproxy:80), majd kattintson a **Befejezés** gombra.

Ha a proxykiszolgáló hitelesítést igényel, hajtsa végre az alábbi lépések végrehajtásával konfigurálhatja a hitelesítő adatokat és beállításokat, amelyeket a felügyelt számítógépekre, amelyek a felügyeleti csoportban a Log Analytics szolgáltatásba jelentések propagálni kell.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. A **RunAs Configuration** (RunAs-konfiguráció) területen válassza a **Profiles** (Profilok) lehetőséget.
1. Nyissa meg a **System Center Advisor Run As Profile Proxy** (System Center Advisor RunAs-profiljának proxyja) profilt.
1. A futtatásiprofil-varázslóban kattintson az Add (Hozzáadás) lehetőségre egy futtató fiók használatához. Létrehozhat egy [futtató fiókot](https://technet.microsoft.com/library/hh321655.aspx), vagy használhat egy meglévő fiókot is. Ennek a fióknak rendelkeznie kell a megfelelő engedélyekkel a proxykiszolgálón való áthaladáshoz.
1. A felügyelni kívánt fiók beállításához válassza az **Egy kijelölt osztály, csoport vagy objektum** lehetőséget, kattintson a **Kiválasztás…** gombra, majd kattintson a **Csoport...** gombra a **Csoport keresése** keresőmező megnyitásához.
1. Keresse meg és válassza ki a **Microsoft System Center Advisor Monitoring Server Group** (Microsoft System Center Advisor monitorozásikiszolgáló-csoport) nevű csoportot. Miután kiválasztotta a csoportot, kattintson az **OK** gombra a **Csoport keresése** keresőmező bezárásához.
1. Az **OK** gombra kattintva zárja be az **Add a Run As account** (Futtató fiók hozzáadása) mezőt.
1. Kattintson a **Mentés** gombra a varázsló befejezéséhez és a módosítások mentéséhez.

Miután a kapcsolat létrejött, és konfigurálta, hogy mely ügynökök gyűjtsenek és jelentsenek adatokat a Log Analyticsnek, a következő konfiguráció lesz a felügyeleti csoportra alkalmazva, nem feltétlenül ebben a sorrendben:

* A **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** futtató fiók létrejön. Ez a fiók a **Microsoft System Center Advisor Run As Profile Blob** (Microsoft System Center Advisor futtatóprofil-blob) futtató blobhoz van társítva, és két osztályt céloz: **Gyűjtési kiszolgáló** és **Operations Manager felügyeleti csoport**.
* Két összekötő jön létre.  Az első összekötő neve **Microsoft.SystemCenter.Advisor.DataConnector**, és automatikusan konfigurálva van egy olyan előfizetéssel, amely a felügyeleti csoport összes osztályának példányain keletkező riasztásokat továbbítja a Log Analyticsnek. A második összekötő **az Advisor Connector**, amelynek feladata a Log Analytics szolgáltatással való kommunikációhoz és az adatok megosztásában.
* A felügyeleti csoportban az adatok gyűjtésére konfigurált ügynökök és csoportok fel lesznek véve a **Microsoft System Center Advisor monitorozásikiszolgáló-csoportba**.

## <a name="management-pack-updates"></a>Felügyeleti csomagok frissítései
A konfiguráció végeztével az Operations Manager felügyeleti csoport kapcsolatot létesít a Log Analytics szolgáltatással. A felügyeleti kiszolgáló szinkronizál a webszolgáltatással, és felügyeleti csomagok formájában megkapja a frissített konfigurációadatokat az Operations Managerrel való integrációra engedélyezett megoldásokra vonatkozóan. Az Operations Manager ellenőrzi a felügyeleti csomagok és az automatikus frissítések letöltése, és importálja őket, ha azok elérhetők. Ezt a működést kifejezetten két szabály vezérli:

* **Microsoft.SystemCenter.Advisor.MPUpdate** – Az alapvető Log Analytics felügyeleti csomagokat frissíti. Alapértelmezés szerint 12 óránként fut.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – A munkaterületen engedélyezett megoldások felügyeleti csomagjait frissíti. Alapértelmezés szerint öt (5) percenként fut.

Automatikus letöltés megakadályozása tiltsa le azokat, vagy módosíthatja annak gyakoriságát, milyen gyakran szinkronizálja a felügyeleti kiszolgáló határozza meg, ha egy új felügyeleti csomag érhető el, és kell letölteni a Log Analytics az alábbi két szabály felül lehet bírálni. A [Szabály vagy figyelő felülbírálása](https://technet.microsoft.com/library/hh212869.aspx) cikkben ismertetett lépések mentén módosíthatja a **Gyakoriság** paraméter másodpercben kifejezett értékét, amely a szinkronizálás ütemezését adja meg, vagy az **Engedélyezve** paraméter módosításával letilthatja a szabályokat. Ezeket a szabálymódosításokat az Operations Manager felügyeleti csoport osztályban lévő minden objektumra alkalmazza.

Folytassa a meglévő vezérlő folyamatának szabályozni a felügyeleti csomag kiadásokban az éles környezetű felügyeleti csoportjában, a szabályok letiltása, és engedélyezheti őket a megadott időpontok engedélyezett a frissítések során. Ha rendelkezik fejlesztési vagy QA-felügyeleti csoporttal környezetében, és az képes kapcsolódni az internetre, konfigurálhatja a felügyeleti csoportot egy Log Analytics-munkaterülettel, amely támogatja ezt a forgatókönyvet. Ez lehetővé teszi, hogy áttekintse és értékelje a Log Analytics-felügyeleti csomagok iteratív kiadásait, még mielőtt kiadná őket üzemi felügyeleti csoportjába.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Váltás Operations Manager csoportról egy új Log Analytics-munkaterületre
1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet, és hozzon létre egy munkaterületet.  
1. Nyissa meg az Operations Manager konzolt az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal, és válassza a **Felügyelet** munkaterületet.
1. Bontsa ki a Log Analytics, és válassza ki **kapcsolatok**.
1. Válassza az **Operation Management Suite újrakonfigurálása** hivatkozást, az ablaktábla közepén.
1. Kövesse a **Log Analytics előkészítési varázslója** , és adja meg az e-mail címet vagy telefonszámot számát és a jelszót a rendszergazdai fiók, amely az új Log Analytics-munkaterület társítva van.
   
   > [!NOTE]
   > Az **Operations Management Suite Előkészítési Varázsló: Munkaterület Kiválasztása** lap azt a meglévő munkaterületet mutatja be, amely használatban van.
   > 
   > 

## <a name="validate-operations-manager-integration-with-log-analytics"></a>Az Operations Manager és a Log Analytics integrációjának ellenőrzése
Több módon is ellenőrizheti a Log Analytics és az Operations Manager integrációjának sikerességét.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Integráció megerősítése az Azure Portalról
1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket.
1. A Log Analytics-munkaterületek listájában válassza ki a vonatkozó munkaterületet.  
1. Válassza a **Speciális beállítások**, a **Csatlakoztatott Források**, majd a **System Center** elemet. 
1. A System Center Operations Manager szakaszában található táblázatban megjelenik a felügyeleti csoport neve, az ügynökök számával együtt, valamint az utolsó adatok beérkezésének állapota.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Integráció megerősítése az Operatív konzolról
1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Kattintson a **Felügyeleti csomagok** elemre, és a **Keresés:** szövegmezőbe írja be az **Advisor** vagy az **Intelligence** kifejezést.
1. Ekkor a keresési eredményekben megjelenik a megfelelő felügyeleti csomag, attól függően, hogy mely megoldások lettek engedélyezve.  Ha például az Alert Management megoldást engedélyezte, akkor a listában a Microsoft System Center Advisor Riasztáskezelés csomag szerepel.
1. A **Monitorozás** nézetből nyissa meg az **Operations Management Suite/Állapot** nézetet.  Jelöljön ki egy felügyeleti kiszolgálót a **Felügyeleti kiszolgáló állapota** ablaktáblán, majd a **Részletes nézet** ablaktáblán erősítse meg, hogy a **Hitelesítési kiszolgálói URI** tulajdonság értéke megfelel-e a Log Analytics-munkaterület azonosítójának.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-log-analytics"></a>A Log Analytics-integráció eltávolítása
Ha már nincs szüksége az Operations Manager felügyeleti csoport és a Log Analytics-munkaterület közötti integrációra, több lépést is el kell végeznie a kapcsolat és a konfiguráció a felügyeleti csoportban való megfelelő eltávolításának érdekében. Az alábbi eljárás során a felügyeleti csoport hivatkozásának törlésével frissíti Log Analytics-munkaterületét, törli a Log Analytics-összekötőket, majd törli azokat a felügyeleti csomagokat, amelyek támogatják az integrációt a szolgáltatással.  

Azokat az Ön által engedélyezett megoldásokhoz tartozó felügyeleti csomagokat, amelyek integráltak az Operations Managerrel, valamint azokat a felügyeleti csomagokat, amelyek támogatják a Log Analytics szolgáltatással való integrációt, nem lehet könnyedén törölni a felügyeleti csoportból. Ennek az az oka, hogy egyes Log Analytics-felügyeleti csoportok más kapcsolódó felügyeleti csomagoktól függnek. Ha törölni szeretné a más felügyeleti csomagoktól függő felügyeleti csomagokat, töltse le a [függőséggel rendelkező felügyeleti csomagok eltávolítására](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) szolgáló szkriptet a TechNet Script Centerből.  

1. Nyissa meg az Operations Manager parancsrendszerhéjat az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal.
   
    > [!WARNING]
    > A folytatás előtt győződjön meg arról, hogy nem rendelkezik olyan egyéni felügyeleti csomagokkal, amelyek nevében szerepel az Advisor vagy az IntelligencePack szó, mert ha vannak ilyenek, azok is törlődni fognak a következő lépés során a felügyeleti csoportból.
    > 

1. A parancsrendszerhéjban írja be a következőt: `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Ezután írja be a következőt: `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Olyan felügyeleti csomagok eltávolításához, amelyek más System Center Advisor felügyeleti csomagoktól függnek, használja a *RecursiveRemove.ps1* szkriptet, amelyet még a TechNet Script Centerből töltött le.  
 
    > [!NOTE]
    > A lépés a PowerShell-lel az Advisor felügyeleti csomagok eltávolítása nem törli automatikusan a Microsoft System Center Advisor belső vagy a Microsoft System Center Advisor felügyeleti csomagokat.  Ne törölje őket.  
    >  

1. Nyissa meg az Operations Manager Operatív konzolját az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal.
1. A **Felügyelet** területen válassza a **Felügyeleti csomagok** csomópontot, majd a **Keresés:** mezőbe gépelje be az **Advisor** szót, és győződjön meg arról, hogy az alábbi felügyeleti csomagok továbbra is importálódnak felügyeleti csoportjába:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Az Azure Portalon kattintson a **beállítások** csempére.
1. Válassza ki **csatlakoztatott források**.
1. A System Center Operations Manager szakaszában a táblázatban megtekintheti a felügyeleti csoport el kívánja távolítani a munkaterület nevét. A **Legutóbbi adatok** oszlop alatt kattintson az **Eltávolítás** elemre.  
   
    > [!NOTE]
    > Az **Eltávolítás** hivatkozás csak 14 nap múlva válik elérhetővé, ha nem észlelhető aktivitás a csatlakoztatott felügyeleti csoportban.  
    > 

1. Ezután megjelenik egy ablak, amely arra kéri, hogy erősítse meg eltávolítási szándékát.  A továbblépéshez kattintson az **Igen** gombra. 

Ha törölni szeretné a két összekötőt (a Microsoft.SystemCenter.Advisor.DataConnectort és az Advisor-összekötőt), mentse számítógépére az alábbi PowerShell-szkriptet, és hajtsa végre az alábbi példák segítségével:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> A számítógépen, amelyről a szkriptet futtatja, ha nem felügyeleti kiszolgáló, telepítve kell lennie az Operations Manager-parancsrendszerhéjnak, a felügyeleti csoport verziójától függően.
> 
> 

```
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

A jövőben, ha azt tervezi, hogy a felügyeleti csoport egy Log Analytics-munkaterülethez való újracsatlakozás, újra importálnia kell a `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` felügyeleticsomag-fájlt. A környezetében telepített System Center Operations Manager verziójától függően a fájlt a következő helyen találja:

* A forrás-adathordozón a `\ManagementPacks` System Center 2016 – Operations Manager mappában vagy felette.
* A felügyeleti csoporton alkalmazott legújabb kumulatív frissítésben. Az Operations Manager 2012 esetében a forrásmappa a ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups`, a 2012 R2 esetében pedig a `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>További lépések
A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](../monitoring/monitoring-solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).


