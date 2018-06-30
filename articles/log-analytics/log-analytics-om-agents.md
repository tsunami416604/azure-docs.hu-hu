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
ms.date: 06/05/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 29ab649f8fe06ae598ff138ff98eb2611ec38e1f
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128877"
---
# <a name="connect-operations-manager-to-log-analytics"></a>Az Operations Manager csatlakoztatása a Log Analyticshez
Meglévő System Center Operations Manager-befektetései kamatoztatása, és a Log Analytics képességeinek kiterjesztése érdekében az Operations Managert integrálhatja Log Analytics-munkaterületével.  Így kiaknázhatja a Log Analytics lehetőségeit, miközben továbbra is használhatja az Operations Managert a következőkre:

* Az informatikai szolgáltatások állapotának monitorozása az Operations Managerrel
* Az incidens- és problémakezelést támogató ITSM-megoldásokkal való integráció megőrzése
* Az Operations Managerrel monitorozott helyszíni és a nyilvános felhőn üzemelő IaaS-alapú virtuális gépeken telepített ügynökök életciklusának felügyelete

A System Center Operations Managerrel való integráció növeli szolgáltatásüzemeltetési stratégiái értékét, mivel az Operations Managerből származó adatok gyűjtését, tárolását és elemzését a Log Analytics sebességével és hatékonyságával végzi.  A Log Analytics segít összerendelni és azonosítani a problémák és a felmerülő ismétlődő jelenségek hátterében rejlő hibákat, és ezzel támogatja meglévő problémakezelési folyamatait.  A keresőmotor a teljesítmény-, esemény- és riasztásadatok vizsgálatában megfigyelhető rugalmassága, valamint az adatok értelmező megjelenítését szolgáló kiterjedt irányítópult- és jelentéskészítési funkcionalitása jól mutatják, hogy a Log Analytics milyen hatékonyan képes kiegészíteni az Operations Manager működését.

Az Operations Manager felügyeleti csoportnak jelentő ügynökök a munkaterületen engedélyezett Log Analytics-adatforrások és -megoldások alapján gyűjtik az adatokat a kiszolgálókról.  Az engedélyezett megoldásoktól függően az adatokat vagy egy Operations Manager-felügyeleti kiszolgáló küldi a szolgáltatásnak, vagy az ügynök által felügyelt rendszeren gyűjtött adatok mennyisége miatt közvetlenül az ügynök küldi a Log Analyticsnek. A felügyeleti kiszolgáló az adatokat közvetlenül továbbítja a szolgáltatásnak, és azok soha nem lesznek az éles vagy az adattárház-adatbázisba írva.  Ha egy felügyeleti kiszolgáló Log Analytics-kapcsolata megszakad, a kiszolgáló az adatokat helyileg gyorsítótárazza, amíg a kommunikáció helyre nem áll a Log Analyticsszel.  Ha a felügyeleti kiszolgáló előre tervezett karbantartások vagy nem tervezett leállás miatt offline állapotba lép, a felügyeleti csoport egy másik felügyeleti kiszolgálója veszi át és folytatja a kapcsolattartást a Log Analyticsszel.  

Az alábbi diagram egy System Center Operations Manager-beli felügyeleti csoport felügyeleti kiszolgálói és ügynökei, illetve a Log Analytics közötti kapcsolatot mutatja be, az irányokat és a portokat is feltüntetve.   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Ha az informatikai biztonsági szabályzatok nem engedélyezik, hogy a hálózat számítógépei kapcsolódjanak az internetre, a felügyeleti kiszolgálók az engedélyezett megoldásoktól függően konfigurálhatóak úgy, hogy az OMS-átjáróhoz kapcsolódjanak a konfigurációs adatok letöltése és a gyűjtött adatok küldése érdekében.  Az Operations Manager felügyeleti csoport a Log Analytics szolgáltatással az OMS-átjárón keresztüli kommunikációjának konfigurációjával kapcsolatos további információért lásd a [számítógépek az OMS-hez az OMS-átjáró használatával történő csatlakoztatását](log-analytics-oms-gateway.md) ismertető témakört.  

## <a name="system-requirements"></a>Rendszerkövetelmények
Mielőtt elkezdené, az alábbi adatokat áttekintve ellenőrizze, hogy megfelel-e a szükséges előfeltételeknek.

* A Log Analytics kizárólag a System Center Operations Manager 1801, az Operations Manager 2016, az Operations Manager 2012 SP1 UR6-os vagy újabb, illetve az Operations Manager 2012 R2 UR2-es vagy újabb kiadást támogatja.  A proxytámogatás az Operations Manager 2012 SP1 UR 7-es és az Operations Manager 2012 R2 UR 3-as verziójában jelent meg.
* Minden Operations Manager-ügynöknek meg kell felelnie a minimális támogatási feltételeknek. Bizonyosodjon meg róla, hogy az ügynökök frissítve vannak a minimális szintekre, máskülönben a Windows-ügynökök forgalma meghiúsulhat, és rengeteg hiba kerülhet az Operations Manager-eseménynaplóba.
* Egy Log Analytics-munkaterület.  További információért tekintse át az [Ismerkedés a Log Analytics szolgáltatással](log-analytics-get-started.md) című cikket.

### <a name="network"></a>Network (Hálózat)
Az alábbi lista az Operations Management-ügynök, a felügyeleti kiszolgálók és az Operatív konzol a Log Analyticsszel való kommunikációhoz szükséges proxy- és tűzfal-konfigurációját tartalmazza.  Az egyes összetevők forgalma kifelé, a hálózatból a Log Analytics szolgáltatásra irányul.     

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
|**Operations Manager konzol az OMS-hez**|||  
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

## <a name="connecting-operations-manager-to-log-analytics"></a>Az Operations Manager csatlakoztatása a Log Analyticshez
Az alábbi lépések végrehajtásával konfigurálhatja Operations Manager felügyeleti csoportját, hogy a Log Analytics munkaterületei egyikéhez csatlakozzon.

Ha az Operations Manager felügyeleti csoport most első alkalommal regisztrál Log Analytics-munkaterületre, és a felügyeleti kiszolgálóknak proxyn vagy OMS-átjárókiszolgálón keresztül kell kommunikálnia a szolgáltatással, az Operatív konzolon nem érhető el a felügyeleti csoport proxykonfigurációját megadó beállítás.  A felügyeleti csoportot sikeresen regisztrálni kell a szolgáltatásban ahhoz, hogy ez a lehetőség rendelkezésre álljon.  Az integráció és a felügyeleti csoportba tartozó minden felügyeleti kiszolgáló konfigurálásához Netsh használatával frissítenie kell a rendszerproxy-konfigurációt a rendszeren, amelyiken az Operatív konzolt futtatja.  

1. Nyisson meg egy emelt szintű parancssort.
   a. Ugrás a **Start** és típus **cmd**.
   b. Kattintson a jobb gombbal **parancssor** , és jelölje ki futtató rendszergazda **.
2. Írja be a következő parancsot, majd nyomja le az **Enter** billentyűt:

    `netsh winhttp set proxy <proxy>:<port>`

Miután végrehajtotta a következő lépéseket a Log Analytics integrálására, a `netsh winhttp reset proxy` parancs futtatásával eltávolíthatja a konfigurációt, majd az Operatív konzol **Proxykiszolgáló konfigurálása** beállításával adhatja meg a proxy- vagy OMS-átjárókiszolgálót. 

1. Nyissa meg az Operatív konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. Bontsa ki az Operations Management Suite-csomópontot, és kattintson a **Kapcsolat** elemre.
3. Kattintson a **Register to Operations Management Suite** (Regisztráció az Operations Management Suite-be) hivatkozásra.
4. Az **Operations Management Suite Előkészítési varázsló Hitelesítés lapján** adja meg az OMS-előfizetésével társított rendszergazdai fiók e-mail-címét vagy telefonszámát és jelszavát, és kattintson a **Bejelentkezés** gombra.
5. Miután sikeresen bejelentkezett, az **Operations Management Suite Előkészítési varázsló Munkaterület kiválasztása lapján** ki kell választania a Log Analytics-munkaterületet.  Ha több munkaterülettel is rendelkezik, válassza ki a legördülő listából azt, amelyiket az Operations Manager felügyeleti csoportba regisztrálni szeretne, majd kattintson a **Tovább** gombra.
   
   > [!NOTE]
   > Az Operations Manager egyszerre csak egy Log Analytics-munkaterület használatát támogatja. Az előző munkaterület a Log Analyticsbe regisztrált kapcsolati és a számítógépekre vonatkozó adatai törölve lesznek a Log Analyticsből.
   > 
   > 
6. Az **Operations Management Suite Előkészítési varázsló Összefoglalás lapján** ellenőrizze a beállításokat, és ha rendben találja azokat, kattintson a **Létrehozás** gombra.
7. Az **Operations Management Suite Előkészítési varázsló Befejezés lapján** kattintson a **Bezárás** gombra.

### <a name="add-agent-managed-computers"></a>Ügynök által felügyelt számítógépek hozzáadása
Miután konfigurálta a Log Analytics-munkaterülettel való integrációt, ez csupán kapcsolatot létesít a szolgáltatással, adatokat nem gyűjt a felügyeleti csoportba jelentő ügynökökről. Ez csak azután kezdődik meg, hogy beállítja, melyik ügynök által felügyelt számítógépek gyűjtsenek adatokat a Log Analytics számára. A számítógép-objektumokat kiválaszthatja egyenként, vagy kiválaszthat egy Windows számítógép-objektumokat tartalmazó csoportot. Nem választhat olyan csoportot, amely egy másik osztályba tartozó példányokat, például logikai lemezeket vagy SQL-adatbázisokat tartalmaz.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. Bontsa ki az Operations Management Suite-csomópontot, és kattintson a **Kapcsolat** elemre.
3. Kattintson a **Számítógép/csoport hozzáadása** hivatkozásra a panel jobb oldalán a Műveletek cím alatt.
4. A **Számítógép keresése** párbeszédpanelen az Operations Manager által megfigyelt számítógépekre vagy csoportokra kereshet. Válassza ki azokat a számítógépeket vagy csoportokat, amelyeket be szeretne vezetni a Log Analyticsbe, és kattintson a **Hozzáadás**, majd az **OK** gombra.

A Felügyelt számítógépek csomópont adatgyűjtésre konfigurált számítógépei és csoportjai az Operations Management Suite-ben az Operatív konzol **Adminisztráció** munkaterületén láthatók.  Itt szükség szerint hozzá is adhat és el is távolíthat számítógépeket és csoportokat.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Proxybeállítások konfigurálása az Operatív konzolon
Ha a felügyeleti csoport és a Log Analytics szolgáltatás közé egy belső proxykiszolgáló ékelődik, hajtsa végre a következő lépéseket.  Ezek a beállítások központilag vannak felügyelve a felügyeleti csoportból, és onnan kiküldve a Log Analytics adatgyűjtési hatókörébe tartozó, ügynök által felügyelt rendszerekre.  Ez hasznosnak bizonyul, ha egyes megoldások megkerülik a felügyeleti kiszolgálót, és közvetlenül a szolgáltatásba küldik az adatokat.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. Bontsa ki a Microsoft Operations Management Suite elemet, majd kattintson a **Kapcsolatok** gombra.
3. Az OMS Connection (OMS-kapcsolat) nézetben kattintson a **Configure Proxy Server** (Proxykiszolgáló konfigurálása) lehetőségre.
4. Az **Operational Management Suite varázsló Proxykiszolgáló lapján** válassza a **Use a proxy server to access the Operations Management Suite** (Proxykiszolgáló használata az Operations Management Suite eléréséhez) lehetőséget, majd írja be az URL-címet a portszámmal együtt (például: http://corpproxy:80), majd kattintson a **Befejezés** gombra.

Ha a proxykiszolgáló hitelesítést igényel, az alábbi lépések végrehajtásával konfigurálja a hitelesítő adatokat és beállításokat, amelyeket a felügyeleti csoport az OMS-nek jelentő felügyelt számítógépeire propagálni kell.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. A **RunAs Configuration** (RunAs-konfiguráció) területen válassza a **Profiles** (Profilok) lehetőséget.
3. Nyissa meg a **System Center Advisor Run As Profile Proxy** (System Center Advisor RunAs-profiljának proxyja) profilt.
4. A futtatásiprofil-varázslóban kattintson az Add (Hozzáadás) lehetőségre egy futtató fiók használatához. Létrehozhat egy [futtató fiókot](https://technet.microsoft.com/library/hh321655.aspx), vagy használhat egy meglévő fiókot is. Ennek a fióknak rendelkeznie kell a megfelelő engedélyekkel a proxykiszolgálón való áthaladáshoz.
5. A felügyelni kívánt fiók beállításához válassza az **Egy kijelölt osztály, csoport vagy objektum** lehetőséget, kattintson a **Kiválasztás…** gombra, majd kattintson a **Csoport...** gombra a **Csoport keresése** keresőmező megnyitásához.
6. Keresse meg és válassza ki a **Microsoft System Center Advisor Monitoring Server Group** (Microsoft System Center Advisor monitorozásikiszolgáló-csoport) nevű csoportot.  Miután kiválasztotta a csoportot, kattintson az **OK** gombra a **Csoport keresése** keresőmező bezárásához.
7. Az **OK** gombra kattintva zárja be az **Add a Run As account** (Futtató fiók hozzáadása) mezőt.
8. Kattintson a **Mentés** gombra a varázsló befejezéséhez és a módosítások mentéséhez.

Miután a kapcsolat létrejött, és konfigurálta, hogy mely ügynökök gyűjtsenek és jelentsenek adatokat a Log Analyticsnek, a következő konfiguráció lesz a felügyeleti csoportra alkalmazva, nem feltétlenül ebben a sorrendben:

* A **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** futtató fiók létrejön.  Ez a fiók a **Microsoft System Center Advisor Run As Profile Blob** (Microsoft System Center Advisor futtatóprofil-blob) futtató blobhoz van társítva, és két osztályt céloz: **Gyűjtési kiszolgáló** és **Operations Manager felügyeleti csoport**.
* Két összekötő jön létre.  Az első összekötő neve **Microsoft.SystemCenter.Advisor.DataConnector**, és automatikusan konfigurálva van egy olyan előfizetéssel, amely a felügyeleti csoport összes osztályának példányain keletkező riasztásokat továbbítja a Log Analyticsnek. A második összekötő az **Advisor-összekötő**, amely az OMS-webszolgáltatással folytatott kommunikációért és az adatok megosztásáért felelős.
* A felügyeleti csoportban az adatok gyűjtésére konfigurált ügynökök és csoportok fel lesznek véve a **Microsoft System Center Advisor monitorozásikiszolgáló-csoportba**.

## <a name="management-pack-updates"></a>Felügyeleti csomagok frissítései
A konfiguráció végeztével az Operations Manager felügyeleti csoport kapcsolatot létesít a Log Analytics szolgáltatással.  A felügyeleti kiszolgáló szinkronizál a webszolgáltatással, és felügyeleti csomagok formájában megkapja a frissített konfigurációadatokat az Operations Managerrel való integrációra engedélyezett megoldásokra vonatkozóan.   Az Operations Manager ellenőrzi, hogy a felügyeleti csomagoknak vannak-e elérhető frissítései, és ha igen, automatikusan letölti és importálja azokat.  Ezt a működést kifejezetten két szabály vezérli:

* **Microsoft.SystemCenter.Advisor.MPUpdate** – Az alapvető Log Analytics felügyeleti csomagokat frissíti. Alapértelmezés szerint 12 óránként fut.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – A munkaterületen engedélyezett megoldások felügyeleti csomagjait frissíti. Alapértelmezés szerint öt (5) percenként fut.

A két szabály felülírható – a csomagok automatikus letöltésének kikapcsolásához letilthatja őket, vagy módosíthatja, hogy a felügyeleti kiszolgáló milyen gyakran szinkronizáljon az OMS-sel, és ellenőrizze, hogy van-e új elérhető és letöltendő felügyeleti csomag.  A [Szabály vagy figyelő felülbírálása](https://technet.microsoft.com/library/hh212869.aspx) cikkben ismertetett lépések mentén módosíthatja a **Gyakoriság** paraméter másodpercben kifejezett értékét, amely a szinkronizálás ütemezését adja meg, vagy az **Engedélyezve** paraméter módosításával letilthatja a szabályokat.  Ezeket a szabálymódosításokat az Operations Manager felügyeleti csoport osztályban lévő minden objektumra alkalmazza.

Ha továbbra is a meglévő változáskezelési folyamatot kívánja követni az üzemi felügyeleti csoportjában a kiadott felügyeleti csomagok szabályozásához, kikapcsolhatja a szabályokat, majd ismét bekapcsolhatja meghatározott időpontokban, amikor a frissítések engedélyezettek. Ha rendelkezik fejlesztési vagy QA-felügyeleti csoporttal környezetében, és az képes kapcsolódni az internetre, konfigurálhatja a felügyeleti csoportot egy Log Analytics-munkaterülettel, amely támogatja ezt a forgatókönyvet.  Ez lehetővé teszi, hogy áttekintse és értékelje a Log Analytics-felügyeleti csomagok iteratív kiadásait, még mielőtt kiadná őket üzemi felügyeleti csoportjába.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Váltás Operations Manager csoportról egy új Log Analytics-munkaterületre
1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen.
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet, és hozzon létre egy munkaterületet.  
3. Nyissa meg az Operations Manager konzolt az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal, és válassza a **Felügyelet** munkaterületet.
4. Bontsa ki a Microsoft Operations Management Suit elemet, és válassza a **Kapcsolatok** lehetőséget.
5. Válassza az **Operation Management Suite újrakonfigurálása** hivatkozást, az ablaktábla közepén.
6. Kövesse az **Operations Management Suite Előkészítési Varázsló** lépéseit, és adja meg az új Log Analytics-munkaterületéhez kapcsolódó rendszergazdai fiók e-mail-címét vagy telefonszámát és jelszavát.
   
   > [!NOTE]
   > Az **Operations Management Suite Előkészítési Varázsló: Munkaterület Kiválasztása** lap azt a meglévő munkaterületet mutatja be, amely használatban van.
   > 
   > 

## <a name="validate-operations-manager-integration-with-log-analytics"></a>Az Operations Manager és a Log Analytics integrációjának ellenőrzése
Több módon is ellenőrizheti a Log Analytics és az Operations Manager integrációjának sikerességét.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Integráció megerősítése az Azure Portalról
1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket.
2. A Log Analytics-munkaterületek listájában válassza ki a vonatkozó munkaterületet.  
3. Válassza a **Speciális beállítások**, a **Csatlakoztatott Források**, majd a **System Center** elemet. 
4. A System Center Operations Manager szakaszában található táblázatban megjelenik a felügyeleti csoport neve, az ügynökök számával együtt, valamint az utolsó adatok beérkezésének állapota.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Integráció megerősítése az Operatív konzolról
1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. Kattintson a **Felügyeleti csomagok** elemre, és a **Keresés:** szövegmezőbe írja be az **Advisor** vagy az **Intelligence** kifejezést.
3. Ekkor a keresési eredményekben megjelenik a megfelelő felügyeleti csomag, attól függően, hogy mely megoldások lettek engedélyezve.  Ha például az Alert Management megoldást engedélyezte, akkor a listában a Microsoft System Center Advisor Riasztáskezelés csomag szerepel.
4. A **Monitorozás** nézetből nyissa meg az **Operations Management Suite/Állapot** nézetet.  Jelöljön ki egy felügyeleti kiszolgálót a **Felügyeleti kiszolgáló állapota** ablaktáblán, majd a **Részletes nézet** ablaktáblán erősítse meg, hogy a **Hitelesítési kiszolgálói URI** tulajdonság értéke megfelel-e a Log Analytics-munkaterület azonosítójának.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-log-analytics"></a>A Log Analytics-integráció eltávolítása
Ha már nincs szüksége az Operations Manager felügyeleti csoport és a Log Analytics-munkaterület közötti integrációra, több lépést is el kell végeznie a kapcsolat és a konfiguráció a felügyeleti csoportban való megfelelő eltávolításának érdekében. Az alábbi eljárás során a felügyeleti csoport hivatkozásának törlésével frissíti Log Analytics-munkaterületét, törli a Log Analytics-összekötőket, majd törli azokat a felügyeleti csomagokat, amelyek támogatják az integrációt a szolgáltatással.   

Azokat az Ön által engedélyezett megoldásokhoz tartozó felügyeleti csomagokat, amelyek integráltak az Operations Managerrel, valamint azokat a felügyeleti csomagokat, amelyek támogatják a Log Analytics szolgáltatással való integrációt, nem lehet könnyedén törölni a felügyeleti csoportból.  Ennek az az oka, hogy egyes Log Analytics-felügyeleti csoportok más kapcsolódó felügyeleti csomagoktól függnek.  Ha törölni szeretné a más felügyeleti csomagoktól függő felügyeleti csomagokat, töltse le a [függőséggel rendelkező felügyeleti csomagok eltávolítására](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) szolgáló szkriptet a TechNet Script Centerből.  

1. Nyissa meg az Operations Manager parancsrendszerhéjat az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal.
   
    > [!WARNING]
    > A folytatás előtt győződjön meg arról, hogy nem rendelkezik olyan egyéni felügyeleti csomagokkal, amelyek nevében szerepel az Advisor vagy az IntelligencePack szó, mert ha vannak ilyenek, azok is törlődni fognak a következő lépés során a felügyeleti csoportból.
    > 

2. A parancsrendszerhéjban írja be a következőt: `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. Ezután írja be a következőt: `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. Olyan felügyeleti csomagok eltávolításához, amelyek más System Center Advisor felügyeleti csomagoktól függnek, használja a *RecursiveRemove.ps1* szkriptet, amelyet még a TechNet Script Centerből töltött le.  
 
    > [!NOTE]
    > Ne törölje a Microsoft System Center Advisor vagy a Microsoft System Center Advisor Internal felügyeleti csomagokat.  
    >  

5. Nyissa meg az Operations Manager Operatív konzolját az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal.
6. A **Felügyelet** területen válassza a **Felügyeleti csomagok** csomópontot, majd a **Keresés:** mezőbe gépelje be az **Advisor** szót, és győződjön meg arról, hogy az alábbi felügyeleti csomagok továbbra is importálódnak felügyeleti csoportjába:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal
7. Az OMS-portálon kattintson a **Beállítások** csempére.
8. Válassza ki **csatlakoztatott adatforrások**.
9. A tábla a System Center Operations Manager szakaszban meg kell jelennie a felügyeleti csoport el szeretné távolítani a munkaterület neve.  A **Legutóbbi adatok** oszlop alatt kattintson az **Eltávolítás** elemre.  
   
    > [!NOTE]
    > Az **Eltávolítás** hivatkozás csak 14 nap múlva válik elérhetővé, ha nem észlelhető aktivitás a csatlakoztatott felügyeleti csoportban.  
    > 

10. Ezután megjelenik egy ablak, amely arra kéri, hogy erősítse meg eltávolítási szándékát.  A továbblépéshez kattintson az **Igen** gombra. 

Ha törölni szeretné a két összekötőt (a Microsoft.SystemCenter.Advisor.DataConnectort és az Advisor-összekötőt), mentse számítógépére az alábbi PowerShell-szkriptet, és hajtsa végre az alábbi példák segítségével:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
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

Ha a jövőben újra csatlakoztatni szeretné felügyeleti csoportját egy Log Analytics-munkaterülethez, újból importálnia kell a `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` felügyeleticsomag-fájlt.  A környezetében telepített System Center Operations Manager verziójától függően a fájlt a következő helyen találja:

* A forrás-adathordozón a `\ManagementPacks` System Center 2016 – Operations Manager mappában vagy felette.
* A felügyeleti csoporton alkalmazott legújabb kumulatív frissítésben.  Az Operations Manager 2012 esetében a forrásmappa a ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups`, a 2012 R2 esetében pedig a `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>További lépések
A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).


