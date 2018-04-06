---
title: Az Operations Manager csatlakoztatása szolgáltatáshoz |} Microsoft Docs
description: A meglévő befektetések a System Center Operations Manager karbantartása, és kiterjesztett képességek használata Naplóelemzési, integrálható az Operations Manager az OMS-munkaterület.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2017
ms.author: magoedte
ms.openlocfilehash: 6db47c7baa0a345a32d26d56e843acd0204ae50b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="connect-operations-manager-to-log-analytics"></a>Adatforrások csatlakoztatása az Operations Manager szolgáltatáshoz
A meglévő befektetések a System Center Operations Manager karbantartása, és kiterjesztett képességek használata Naplóelemzési, integrálható az Operations Manager az OMS-munkaterület.  Ez lehetővé teszi, miközben továbbra is használja az Operations Manager OMS lehetőségek is használja:

* Továbbra is az Operations Manager az informatikai szolgáltatások állapotának figyelése
* A támogatási incidensek és a probléma felügyeleti ITSM megoldásaival való integráció fenntartása
* A helyszíni és a nyilvános felhő infrastruktúra-szolgáltatási virtuális gépek, amelyek a figyelheti az Operations Manager telepített ügynökök életciklusának kezelését

System Center Operations Manager integrálása értéket ad hozzá a szolgáltatási műveletek stratégia a sebesség és a összegyűjtésének, tárolásának és elemzéséhez az Operations Manager OMS hatékonyságát.  OMS egyeztetéséhez, és a hibák, a problémák azonosítása és felszínre hozza a meglévő probléma felügyeleti folyamat támogatásához az ismétlődések segítségével.   A teljesítmény-, esemény- és riasztási adatokat, a gazdag irányítópultok és az adatok közzétételét ésszerű módszerrel, a jelentéskészítési képességek vizsgálata keresőmotor rugalmasan OMS számos lehetőséget kínál, az Operations Manager complimenting erőssége mutatja be.

Az Operations Manager felügyeleti csoportnak az ügynök adatokat gyűjteni a Log Analyticshez adatforrások és az OMS-előfizetésben engedélyezett megoldások alapján a kiszolgálók.  Attól függően, hogy a megoldás engedélyezte, ezek a megoldások adatait vagy kerülnek közvetlenül egy Operations Manager felügyeleti kiszolgálóról az OMS-webszolgáltatás vagy az ügynök által felügyelt rendszeren gyűjtött adatok mennyisége miatt OMS webszolgáltatás küldött közvetlenül az ügynöktől. A felügyeleti kiszolgáló továbbítja az OMS adatokat közvetlenül a OMS webszolgáltatás; az OperationsManager vagy OperationsManagerDW adatbázist soha nem írás.  A felügyeleti kiszolgáló a OMS webszolgáltatással kapcsolata megszakad, ha azt gyorsítótárazza az adatok helyileg, amíg a kommunikációs helyreáll, az OMS Szolgáltatáshoz.  Ha a felügyeleti kiszolgáló előre tervezett karbantartások vagy nem tervezett leállás miatt offline állapotban, a felügyeleti csoport egy másik felügyeleti kiszolgálóra folytatja OMS kapcsolatot.  

A következő ábra szemlélteti a felügyeleti kiszolgálók és az ügynökök a System Center Operations Manager felügyeleti csoport és az OMS-ben, beleértve a irányát és portok közötti kapcsolat.   

![OMS-műveletek-manager-integráció-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Ha az IT-biztonsági házirendeknek nem engedélyezi a számítógépek a hálózat csatlakozik az internethez, felügyeleti kiszolgáló beállítható úgy, hogy az OMS-átjárón, attól függően, hogy a megoldás engedélyezte az összegyűjtött adatok küldésére és fogadására a konfigurációs adatokat.  További információt és az Operations Manager felügyeleti csoport egy OMS-átjárót az OMS szolgáltatáshoz keresztüli kommunikáció konfigurálásával kapcsolatos lépéseket, [számítógépeket csatlakoztatni az OMS Szolgáltatáshoz az OMS-átjáró](log-analytics-oms-gateway.md).  

## <a name="system-requirements"></a>Rendszerkövetelmények
Megkezdése előtt tekintse át az alábbi részleteket megfelel a szükséges előfeltételek ellenőrzése.

* Csak akkor támogatja az Operations Manager 2016-Operations Manager 2012 SP1 UR6 OMS és nagyobb, és az Operations Manager 2012 R2 UR2 és nagyobb.  A proxytámogatás az Operations Manager 2012 SP1 UR 7-es és az Operations Manager 2012 R2 UR 3-as verziójában jelent meg.
* Az összes Operations Manager-ügynökök meg kell felelnie a minimális támogatási követelményeknek. Győződjön meg arról, hogy ügynökök a minimális frissítéskor, ellenkező esetben a Windows-ügynök forgalom sikertelen lehet, hogy és sok hiba előfordulhat, hogy töltse ki az Operations Manager eseménynaplójában.
* Az OMS-előfizetéssel.  További információkért tekintse át [Ismerkedés a Naplóelemzési](log-analytics-get-started.md).

### <a name="network"></a>Network (Hálózat)
Az alábbi lista a proxy és tűzfal konfigurációs adatokat, az Operations Manager-ügynök, felügyeleti kiszolgálók és operatív konzol OMS folytatott kommunikációhoz szükséges információt.  Minden egyes összetevő érkező forgalom értéke kimenő, a hálózatról az OMS szolgáltatáshoz.     

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
|**Operations Manager konzolján az OMS-be**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80-as és 443-as||  
|\*.microsoft.com| 80-as és 443-as||  
|\*.microsoftonline.com| 80-as és 443-as||  
|\*.mms.microsoft.com| 80-as és 443-as||  
|login.windows.net| 80-as és 443-as||  


## <a name="connecting-operations-manager-to-oms"></a>Csatlakozás az Operations Manager az OMS-be
Hajtsa végre az alábbi lépéseket az Operations Manager felügyeleti csoportjának csatlakozni az OMS-munkaterület egyik konfigurálásának lépéseit.

1. Az Operations Manager konzolon válassza a **felügyeleti** munkaterületen.
2. Bontsa ki az Operations Management Suite csomópontot, és kattintson a **kapcsolat**.
3. Kattintson a **Regisztrálás az Operations Management Suite** hivatkozásra.
4. Az a **Operations Management Suite előkészítési varázslója: hitelesítés** lapon, az e-mail címet vagy telefonszámot, és az OMS-előfizetéshez társított rendszergazdai fiók jelszavát, majd kattintson **bejelentkezés**.
5. Ön hitelesítése után vannak sikeresen, a a **Operations Management Suite előkészítési varázslója: Munkaterület kiválasztása** oldal kéri az OMS-munkaterület kiválasztásához.  Ha egynél több munkaterületen, válassza ki a munkaterület regisztrálása az Operations Manager felügyeleti csoportot a legördülő listából, és kattintson a kívánt **következő**.
   
   > [!NOTE]
   > Az Operations Manager csak egyszerre egy OMS-munkaterület támogat. A kapcsolat és a számítógépek az előző munkaterület az OMS Szolgáltatáshoz regisztrált törlődnek az OMS Szolgáltatáshoz.
   > 
   > 
6. Az a **Operations Management Suite előkészítési varázslója: Összegzés** lapon hagyja jóvá a beállításokat, ha azok a helyes-e, kattintson **létrehozása**.
7. Az a **Operations Management Suite előkészítési varázslója: Befejezés** kattintson **Bezárás**.

### <a name="add-agent-managed-computers"></a>Ügynök által felügyelt számítógépek hozzáadása
Integrációjának konfigurálása után az OMS-munkaterület, ez csak OMS kapcsolatot létesít, nincs adatgyűjtés az ügynök a felügyeleti csoportban. Ez nem fordulhat elő, amíg mely adott ügynök által felügyelt számítógépek adatokat gyűjt a Log Analyticshez konfigurálása után. Kiválaszthatja a számítógép-objektumok önállóan, vagy kiválaszthat egy csoportot, amely tartalmazza a Windows számítógép-objektumok. Nem választhat ki egy csoportot, amely egy másik osztály, például logikai lemezek vagy az SQL-adatbázis példányait tartalmazza.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. Bontsa ki az Operations Management Suite csomópontot, és kattintson a **kapcsolat**.
3. Kattintson a **egy számítógépcsoport hozzáadásához** alatt a műveletek a jobb oldali ablaktábla a fejléc.
4. Az a **számítógép keresése** párbeszédpanelen is kereshet számítógépek vagy csoportok az Operations Manager általi megfigyelés alatt. Válassza ki a számítógépek vagy csoportok érheti el a OMS, kattintson **Hozzáadás**, és kattintson a **OK**.

Megtekintheti a számítógépeket és csoportokat szeretné az adatgyűjtést az Operations Management Suite a felügyelt számítógépek csomópontjából konfigurálva a **felügyeleti** munkaterület az operatív konzol.  Itt adja hozzá, vagy távolítsa el a számítógép- és szükség szerint.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Az operatív konzolon OMS proxybeállításainak konfigurálása
Hajtsa végre az alábbi lépéseket, a felügyeleti csoport és az OMS-webszolgáltatás között egy belső proxykiszolgáló esetén.  Ezek a beállítások központilag a felügyeleti csoportból felügyelt és ügynök által felügyelt rendszerek, adatok gyűjtéséhez OMS hatókörében szereplő kiosztva.  Ez az egyes megoldások kihagyása a felügyeleti kiszolgáló és adatokat közvetlenül küldeni az OMS-webszolgáltatás előnyös.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. Bontsa ki az Operations Management Suite, majd a **kapcsolatok**.
3. Az OMS Connection (OMS-kapcsolat) nézetben kattintson a **Configure Proxy Server** (Proxykiszolgáló konfigurálása) lehetőségre.
4. A **Operations Management Suite varázslója: proxykiszolgáló** lapon jelölje be **proxykiszolgáló használata az Operations Management Suite eléréséhez**, és írja be az URL-cím, egy portszám, például http://corpproxy:80 majd **Befejezés**.

Ha a proxykiszolgálóhoz hitelesítés szükséges, hajtsa végre a következő lépésekkel állíthatja be a hitelesítő adatok és beállítások propagálódik az OMS jelent a felügyeleti csoportban lévő felügyelt számítógépeket.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. A **RunAs Configuration** (RunAs-konfiguráció) területen válassza a **Profiles** (Profilok) lehetőséget.
3. Nyissa meg a **System Center Advisor Run As Profile Proxy** (System Center Advisor RunAs-profiljának proxyja) profilt.
4. Az a futtató profil varázsló kattintson a Hozzáadás gombra a Futtatás mint fiók használatára. Létrehozhat egy [Futtatás mint fiók](https://technet.microsoft.com/library/hh321655.aspx) vagy egy meglévő fiókot használjon. Ennek a fióknak rendelkeznie kell a megfelelő engedélyekkel a proxykiszolgálón való áthaladáshoz.
5. Kezelheti a fiók beállításához válassza **egy kijelölt osztály, csoport vagy objektum**, kattintson a **válasszon...** majd **csoporthoz...** lehetőségre a **csoport keresési** mezőbe.
6. Keresse meg és válassza ki **Microsoft System Center Advisor figyelési kiszolgáló csoport**.  Kattintson a **OK** zárja be a csoport kijelölése után a **csoport keresési** mezőbe.
7. Kattintson a **OK** bezárásához a **hozzáadása egy futtató fiókot** mezőbe.
8. Kattintson a **mentése** a varázsló befejezéséhez és a módosítások mentéséhez.

Után létrejön a kapcsolat, és mely ügynökök gyűjt és az OMS-be a jelentés adatait konfigurál, a következő konfiguráció alkalmazása a felügyeleti csoport nem feltétlenül sorrendben:

* A futtató fiók **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** jön létre.  Az a futtató profilhoz társítva **Microsoft System Center Advisor Futtatás mint profil Blob** célzott két osztály - és **gyűjtési kiszolgáló** és **Operations Manager felügyeleti csoport**.
* Két összekötők jönnek létre.  Az első nevű **Microsoft.SystemCenter.Advisor.DataConnector** és a-előfizetést, amely továbbítja az OMS szolgáltatáshoz a felügyeleti csoport összes osztályok példányai generált összes riasztás automatikusan konfigurálja. A második összekötő **az Advisor Connector**, amely felelős az OMS webes szolgáltatással folytatott kommunikáció és az adatok megosztása.
* Az ügynökök és a felügyeleti csoportban szereplő adatok gyűjtéséért felelős ügyfélfeladatot kiválasztott csoportok hozzáadódik a **Microsoft System Center Advisor figyelési kiszolgáló csoport**.

## <a name="management-pack-updates"></a>Felügyeleti csomagok frissítéséhez
Konfiguráció befejezése után az Operations Manager felügyeleti csoport kapcsolatot hoz létre az OMS szolgáltatással.  A felügyeleti kiszolgáló a webszolgáltatás szinkronizálja, és engedélyezte a megoldások, amelyekbe beépül az Operations Manager felügyeleti csomagok formájában fogadják a frissített konfigurációs adatokat.   Az Operations Manager ellenőrzi a felügyeleti csomagok és az automatikus frissítések letöltése, és importálja azokat, ha azok elérhetők.  Két szabály van különösen ami szabályozhatja, hogy ez a viselkedés:

* **Microsoft.SystemCenter.Advisor.MPUpdate** -frissíti az alap OMS felügyeleti csomagokat. Alapértelmezés szerint 12 óránként fut.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** -frissíti a megoldás felügyeleti csomagok engedélyezve van a munkaterületen. Alapértelmezés szerint öt (5) percenként fut.

Ha szeretné felülbírálni az e két szabályok automatikus letöltés tiltása letiltásával őket, vagy módosíthat, milyen gyakran szinkronizálja a felügyeleti kiszolgáló határozzák meg, ha egy új felügyeleti csomag elérhető, és le kell tölteni az OMS gyakoriságát.  Kövesse a lépéseket [egy szabály vagy figyelő felülbírálása](https://technet.microsoft.com/library/hh212869.aspx) módosítani a **gyakoriság** paraméter másodpercben. a szinkronizálási ütemezés módosításához, vagy módosítsa a **engedélyezve** letiltja a szabályok paraméter.  A felülbírálások az Operations Manager felügyeleti csoport osztály összes objektumához célként.

Ha folytatja a meglévő vezérlő folyamatának szabályozása a üzemi felügyeleti csoportban található felügyeleti csomag kiadásokban követően, a szabályok letiltása, és lehetővé teszi az adott időben, amikor a frissítések engedélyezettek. Ha rendelkezik olyan fejlesztési vagy QA felügyeleti csoporthoz a környezetben, és képes kapcsolódni az internetre, konfigurálható, hogy a felügyeleti csoport egy OMS-munkaterület ennek támogatásához.  Ez lehetővé teszi, hogy ellenőrizze és értékelje az OMS-felügyeleti csomagok iteratív kiadásaiban őket az üzemi felügyeleti csoportjába feloldása előtt.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Az Operations Manager csoport váltani egy új OMS-munkaterület
1. Jelentkezzen be az OMS-előfizetés és a munkaterület létrehozása [a Microsoft Operations Management Suite](http://oms.microsoft.com/).
2. Az Operations Manager konzol megnyitásához olyan fiókkal, amely az Operations Manager-Rendszergazdák szerepkör tagja, és válassza ki a **felügyeleti** munkaterületen.
3. Bontsa ki az Operations Management Suite, és válassza **kapcsolatok**.
4. Válassza ki a **művelet Management Suite újrakonfigurálása** hivatkozás a közel-oldalon, ha a panel.
5. Kövesse a **Operations Management Suite előkészítési varázslója** , és adja meg e-mail címét vagy telefonszámát számát és az új OMS-munkaterület társított rendszergazdai fiók jelszavát.
   
   > [!NOTE]
   > A **Operations Management Suite előkészítési varázslója: Munkaterület kiválasztása** lap megadja a már meglévő munkaterület, amely használatban van.
   > 
   > 

## <a name="validate-operations-manager-integration-with-oms"></a>Az Operations Manager integrációja, OMS ellenőrzése
Azt is ellenőrizze, hogy az Operations Manager-integrálást az OMS sikeres néhány különböző módja van.

### <a name="to-confirm-integration-from-the-oms-portal"></a>Integráció az OMS-portálon megerősítéséhez
1. Az OMS-portálon kattintson a **beállítások** csempéje
2. Válassza ki **csatlakoztatott adatforrások**.
3. A tábla a System Center Operations Manager szakaszban meg kell jelennie az ügynökök és az állapot számú felsorolt adatok utolsó fogadásakor felügyeleti csoport neve.
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)
4. Megjegyzés: a **munkaterület azonosítója** érték a bal oldali a beállítások lap.  Az Operations Manager felügyeleti csoportjának az alábbi szemben érvényesíti.  

### <a name="to-confirm-integration-from-the-operations-console"></a>Integráció az operatív konzolról megerősítéséhez
1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
2. Válassza ki **felügyeleti csomagok** és a a **keres:** szöveg írja be a következőt **Advisor** vagy **Eszközintelligencia**.
3. Attól függően, hogy engedélyezte a megoldások tekintse meg a megfelelő felügyeleti csomag szerepel a keresési eredmények között.  Például ha engedélyezte a riasztási felügyeleti megoldás, a felügyeleti csomag Microsoft System Center Advisor Riasztáskezelési szerepel a listában.
4. Az a **figyelés** megtekintéséhez nyissa meg a **Operations Management Suite\Health állapot** nézet.  Jelöljön ki egy felügyeleti csoportban a **felügyeleti kiszolgáló állapota** ablaktáblán, és a a **részletes nézet** ablaktáblán, erősítse meg a tulajdonság értékét **hitelesítési szolgáltatás URI** megegyezik az OMS-munkaterület azonosítója.
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-oms"></a>Távolítsa el az OMS integrációja
Ha már nincs szükség az Operations Manager felügyeleti csoport és az OMS-munkaterület közötti integráció, nincs több lépésre van szükség a kapcsolat és a konfiguráció megfelelően eltávolítani a felügyeleti csoport. Az alábbi eljárást az OMS-munkaterület törölni kell a hivatkozás a felügyeleti csoport frissítése, törölje az OMS-összekötőt, és ezután törölje az OMS támogató felügyeleti csomagok rendelkezik.   

Felügyeleti csomagok megoldásainak engedélyezve van, amelyekbe beépül az Operations Manager és az integráció az OMS szolgáltatással támogatásához szükséges felügyeleti csomagok könnyen nem törölhető a felügyeleti csoportból.  Ennek az az oka az OMS-felügyeleti csomagok némelyike más kapcsolódó felügyeleti csomagoktól tartalmazhat függőségeket.  Törölje a felügyeleti csomagokat függőség rendelkező más felügyeleti csomagoktól függ, töltse le a parancsfájl [függőségekkel rendelkező felügyeleti csomag eltávolítása](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) TechNet Script Center.  

1. Nyissa meg az Operations Manager parancs-rendszerhéja egy olyan fiókkal, amely az Operations Manager-Rendszergazdák szerepkör tagja.
   
    > [!WARNING]
    > Ellenőrizze, nem rendelkezik a neve, a folytatás előtt a word Advisor vagy IntelligencePack bármilyen egyéni felügyeleti csomagok, ellenkező esetben a következő lépések törölje ezeket a felügyeleti csoportból.
    > 

2. A parancs-rendszerhéj parancssorában gépelje be `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. Következő típusa `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. A felügyeleti csomagok fennmaradó, amely más System Center Advisor felügyeleti csomagoktól tartalmazhat függőség eltávolításához használja a parancsfájl *RecursiveRemove.ps1* korábban letöltött a TechNet Script Center.  
 
    > [!NOTE]
    > Ne törölje a Microsoft System Center Advisor vagy a Microsoft System Center Advisor belső felügyeleti csomag.  
    >  

5. Nyissa meg az Operations Manager operatív konzol egy olyan fiókkal, amely az Operations Manager-Rendszergazdák szerepkör tagja.
6. A **felügyeleti**, jelölje be a **felügyeleti csomagok** csomópont és a a **keres:** mezőbe írja be **Advisor** , és ellenőrizze a következő felügyeleti csomagokat továbbra is az Ön felügyeleti csoportjába importált:
   
   * A Microsoft System Center Advisor
   * A Microsoft System Center Advisor belső
7. Az OMS-portálon kattintson a **Beállítások** csempére.
8. Válassza ki **csatlakoztatott adatforrások**.
9. A tábla a System Center Operations Manager szakaszban meg kell jelennie a felügyeleti csoport el szeretné távolítani a munkaterület neve.  Az oszlop alatt **utolsó adatok**, kattintson a **eltávolítása**.  
   
    > [!NOTE]
    > A **eltávolítása** hivatkozás nem lesz elérhető 14 nap elteltével nem észlelhető a csatlakoztatott felügyeleti csoportból tevékenység esetén.  
    > 

10. Megjelenik egy ablak, amelyben meg kell erősítenie, hogy szeretné-e az eltávolítás folytatásához.  Kattintson a **Igen** a folytatáshoz. 

Törölje a két összekötőt - Microsoft.SystemCenter.Advisor.DataConnector és az Advisor Connector, mentse a számítógépre a PowerShell parancsfájl alatt, és hajtsa végre az alábbi példák segítségével:

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> A számítógépen futtatja a parancsfájlt, ha nem a felügyeleti kiszolgáló, az Operations Manager parancshéjat, attól függően, hogy a felügyeleti csoport verziójának telepítve kell rendelkeznie.
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

A jövőben Ha újracsatlakozni a felügyeleti csoport egy OMS-munkaterület tervezi, akkor importálja újra a `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` a felügyeleti csoportra alkalmazza a legújabb kumulatív frissítését a felügyeleticsomag-fájlt.  Ez a fájl a `%ProgramFiles%\Microsoft System Center 2012` vagy a `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` mappát.

## <a name="next-steps"></a>További lépések
Funkciók hozzáadása és az adatgyűjtésre [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).


