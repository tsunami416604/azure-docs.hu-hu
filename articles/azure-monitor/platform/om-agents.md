---
title: Az Operations Manager csatlakoztatása az Azure Monitorhoz | Microsoft dokumentumok
description: Meglévő System Center Operations Manager-befektetései kamatoztatása, és a Log Analytics képességeinek kiterjesztése érdekében az Operations Managert integrálhatja munkaterületével.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 92b6737f48d8d8704f461c9adac92284b323b05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274345"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Az Operations Manager csatlakoztatása az Azure Monitorhoz

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A System Center [Operations Managerbe](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) történő meglévő befektetésének fenntartásához és az Azure Monitor kiterjesztett képességeinek használatához integrálhatja az Operations Managert a Log Analytics-munkaterülettel. Ez lehetővé teszi, hogy kihasználja a naplók lehetőségeit az Azure Monitorban, miközben továbbra is használja az Operations Managert a következőkre:

* Az informatikai szolgáltatások állapotának monitorozása az Operations Managerrel
* Az incidens- és problémakezelést támogató ITSM-megoldásokkal való integráció megőrzése
* Az Operations Managerrel monitorozott helyszíni és a nyilvános felhőn üzemelő IaaS-alapú virtuális gépeken telepített ügynökök életciklusának felügyelete

A System Center Operations Managerrel való integráció az Azure Monitor sebességének és hatékonyságának használatával növeli a szolgáltatási műveletek stratégiáját az Operations Manager naplóadatainak gyűjtésében, tárolásában és elemzésében. Az Azure Monitor naplólekérdezései segítenek a korrelációban, és a meglévő problémakezelési folyamat támogatása érdekében a problémák hibáinak azonosításán és az ismétlődések felületének felismerésén dolgozik. A lekérdezési motor rugalmassága a teljesítmény-, esemény- és riasztási adatok vizsgálatához, gazdag irányítópultokkal és jelentéskészítési képességekkel, hogy ezeket az adatokat értelmes módon elérhetővé tegye, jól mutatja, hogy az Azure Monitor milyen erősen nyújt az Operations Manager tágítását.

Az Operations Manager felügyeleti csoportnak jelentett ügynökök adatokat gyűjtenek a kiszolgálókról a munkaterületen engedélyezett [Log Analytics adatforrások](agent-data-sources.md) és megoldások alapján. Az engedélyezett megoldásoktól függően az adatokat vagy közvetlenül az Operations Manager felügyeleti kiszolgálóról küldi el a szolgáltatásnak, vagy az ügynök által felügyelt rendszeren gyűjtött adatok mennyisége miatt a rendszer közvetlenül az ügynöktől küldi el a Log Analytics-munkaterületre. A felügyeleti kiszolgáló az adatokat közvetlenül továbbítja a szolgáltatásnak, és azok soha nem lesznek az éles vagy az adattárház-adatbázisba írva. Ha egy felügyeleti kiszolgáló elveszíti a kapcsolatot az Azure Monitor, gyorsítótárazza az adatokat helyileg, amíg a kommunikáció helyre áll. Ha a felügyeleti kiszolgáló tervezett karbantartás vagy nem tervezett kimaradás miatt offline állapotban van, a felügyeleti csoport egy másik felügyeleti kiszolgálója folytatja a kapcsolatot az Azure Monitorlal.  

Az alábbi ábrán a felügyeleti kiszolgálók és ügynökök közötti kapcsolat a System Center Operations Manager felügyeleti csoport és az Azure Monitor, beleértve az irányt és a portok.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Ha az informatikai biztonsági házirendek nem teszik lehetővé, hogy a hálózat számítógépei csatlakozzanak az internethez, a felügyeleti kiszolgálók beállíthatók úgy, hogy a Log Analytics átjáróhoz csatlakozzanak a konfigurációs információk fogadásához és az összegyűjtött adatok küldéséhez a megoldásoktól függően Engedélyezve. További információ és az Operations Manager felügyeleti csoport konfigurálása az Azure Monitor nak egy Log Analytics-átjárón keresztül történő kommunikációra című témakörben található: [Számítógépek csatlakoztatása az Azure Monitorhoz a Log Analytics-átjáró használatával című témakörben.](../../azure-monitor/platform/gateway.md)  

## <a name="prerequisites"></a>Előfeltételek

A kezdés előtt tekintse át az alábbi követelményeket.

* Az Azure Monitor csak a System Center Operations Manager 2016-os vagy újabb, az Operations Manager 2012 SP1 UR6 vagy újabb, valamint az Operations Manager 2012 R2 UR2 vagy újabb verzióit támogatja. A proxytámogatás az Operations Manager 2012 SP1 UR 7-es és az Operations Manager 2012 R2 UR 3-as verziójában jelent meg.
* A System Center Operations Manager 2016 integrálása az Egyesült Államok kormányzati felhőjével egy frissített Advisor felügyeleti csomagra van szükség, amely et a 2. A System Center Operations Manager 2012 R2 rendszercsomaghoz egy frissített Advisor felügyeleti csomagra van szükség, amely a 3.
* Minden Operations Manager-ügynöknek meg kell felelnie a minimális támogatási feltételeknek. Győződjön meg arról, hogy az ügynökök a minimális frissítés, különben a Windows-ügynök kommunikáció sikertelen lehet, és hibákat generál az Operations Manager eseménynaplójában.
* Egy Log Analytics-munkaterület. További információért tekintse át [a Log Analytics munkaterületének áttekintését.](design-logs-deployment.md) 
* Az Azure-ban egy olyan fiókkal hitelesítheti magát, amely a [Log Analytics közreműködői szerepkör](manage-access.md#manage-access-using-azure-permissions)tagja.

* Támogatott régiók – A System Center Operations Manager csak a következő Azure-régiókat támogatja a Log Analytics-munkaterülethez való csatlakozáshoz:
    - USA nyugati középső régiója
    - Délkelet-Ausztrália
    - Nyugat-Európa
    - USA keleti régiója
    - Délkelet-Ázsia
    - Kelet-Japán
    - Az Egyesült Királyság déli régiója
    - Közép-India
    - Közép-Kanada
    - USA nyugati régiója, 2.

>[!NOTE]
>Az Azure API-k legutóbbi módosításai megakadályozzák, hogy az ügyfelek első alkalommal konfigurálhatják a felügyeleti csoport és az Azure Monitor közötti integrációt. Az on-k, akik már integrálták a felügyeleti csoportot a szolgáltatással, a rendszer csak akkor érinti, ha újra kell konfigurálnia a meglévő kapcsolatot.  
>Új felügyeleti csomag jelent meg az Operations Manager következő verzióihoz:
> - A System Center Operations Manager 2019 esetében ez a felügyeleti csomag a forrásadathordozóhoz tartozik, és egy új felügyeleti csoport telepítése vagy frissítés során települ.
>- Az Operations Manager 1801 felügyeleti csomag az Operations Manager 1807 esetében is alkalmazható.
>- A System Center Operations Manager 1801 esetében töltse le a felügyeleti csomagot [innen.](https://www.microsoft.com/download/details.aspx?id=57173)
>- A System Center 2016 - Operations Manager esetében töltse le a felügyeleti csomagot [innen.](https://www.microsoft.com/download/details.aspx?id=57172)  
>- A System Center Operations Manager 2012 R2 esetében töltse le a felügyeleti csomagot [innen.](https://www.microsoft.com/download/details.aspx?id=57171)  


### <a name="network"></a>Network (Hálózat)

Az alábbi információk az Operations Manager-ügynök, a felügyeleti kiszolgálók és az Operations console azure monitorral való kommunikációjához szükséges proxy- és tűzfalkonfigurációs információkat sorolják fel. Az egyes összetevőkből érkező forgalom kimenő a hálózatról az Azure Monitorra.

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
|**Operations Manager konzol az Azure Monitorhoz**|||  
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

### <a name="tls-12-protocol"></a>TLS 1.2 protokoll

Az Azure Monitorra történő átvitel során az adatok biztonságának biztosítása érdekében javasoljuk, hogy konfigurálja az ügynököt és a felügyeleti csoportot legalább a Transport Layer Security (TLS) 1.2 használatára. A TLS/Secure Sockets Layer (SSL) régebbi verziói sebezhetőnek bizonyultak, és bár jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak.** További információkért tekintse át [az Adatok biztonságos küldése a TLS 1.2 használatával](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)című, című további című információt.

## <a name="connecting-operations-manager-to-azure-monitor"></a>Az Operations Manager csatlakoztatása az Azure Monitorhoz

Az alábbi lépések végrehajtásával konfigurálhatja Operations Manager felügyeleti csoportját, hogy a Log Analytics munkaterületei egyikéhez csatlakozzon.

Az Operations Manager felügyeleti csoport loganalytics-munkaterülettel történő kezdeti regisztrálása során a felügyeleti csoport proxykonfigurációjának megadása nem érhető el az operatív konzolon.  A felügyeleti csoportot sikeresen regisztrálni kell a szolgáltatásban ahhoz, hogy ez a lehetőség rendelkezésre álljon.  A megoldás megoldásához frissítenie kell a rendszerproxy-konfigurációt azon a rendszeren, amelyből az operatív konzolt futtató rendszer az integráció konfigurálásához, valamint a felügyeleti csoport összes felügyeleti kiszolgálója segítségével kell frissítenie.  

1. Nyisson meg egy emelt szintű parancssort.
   a. Nyissa meg a **Start és** a **cmd típust.**
   b. Kattintson a jobb gombbal **a Parancssorra,** és válassza a Futtatás rendszergazdaként** parancsot.
1. Írja be a következő parancsot, majd nyomja le az **Enter** billentyűt:

    `netsh winhttp set proxy <proxy>:<port>`

Miután elvégezte a következő lépéseket az Azure Monitorlal `netsh winhttp reset proxy` való integrációhoz, eltávolíthatja a konfigurációt a futtatással, majd az Operations console **Proxykiszolgáló konfigurálása** beállítással adja meg a proxyt vagy a Log Analytics átjárókiszolgálót.

1. Nyissa meg az Operatív konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Bontsa ki az Operations Management Suite-csomópontot, és kattintson a **Kapcsolat** elemre.
1. Kattintson a **Register to Operations Management Suite** (Regisztráció az Operations Management Suite-be) hivatkozásra.
1. Az **Operations Management Suite Előkészítési varázsló Hitelesítés lapján** adja meg az OMS-előfizetésével társított rendszergazdai fiók e-mail-címét vagy telefonszámát és jelszavát, és kattintson a **Bejelentkezés** gombra.

   >[!NOTE]
   >Az Operations Management Suite neve kilett vonva.

1. A sikeres hitelesítést követően az **Operations Management Suite Bevezetés varázsló: Válassza ki a munkaterület** lapot, a rendszer kéri, hogy válassza ki az Azure-bérlő, előfizetés és a Log Analytics munkaterületet. Ha több munkaterülettel is rendelkezik, válassza ki a legördülő listából azt, amelyiket az Operations Manager felügyeleti csoportba regisztrálni szeretne, majd kattintson a **Tovább** gombra.

   > [!NOTE]
   > Az Operations Manager egyszerre csak egy Log Analytics-munkaterület használatát támogatja. A kapcsolat és a számítógépek, amelyek regisztráltak az Azure Monitor az előző munkaterülettel törlődnek az Azure Monitor.
   >
   >
1. Az **Operations Management Suite Előkészítési varázsló Összefoglalás lapján** ellenőrizze a beállításokat, és ha rendben találja azokat, kattintson a **Létrehozás** gombra.
1. Az **Operations Management Suite Előkészítési varázsló Befejezés lapján** kattintson a **Bezárás** gombra.

### <a name="add-agent-managed-computers"></a>Ügynök által felügyelt számítógépek hozzáadása

A Log Analytics-munkaterülettel való integráció konfigurálása után csak kapcsolatot hoz létre a szolgáltatással, a felügyeleti csoportnak jelentést tevő ügynököktől nem gyűjt adatokat. Ez csak akkor fog megtörténni, ha konfigurálja, hogy mely ügynök által felügyelt számítógépek gyűjtik a naplóadatokat az Azure Monitorhoz. A számítógép-objektumokat kiválaszthatja egyenként, vagy kiválaszthat egy Windows számítógép-objektumokat tartalmazó csoportot. Nem választhat olyan csoportot, amely egy másik osztályba tartozó példányokat, például logikai lemezeket vagy SQL-adatbázisokat tartalmaz.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Bontsa ki az Operations Management Suite-csomópontot, és kattintson a **Kapcsolat** elemre.
1. Kattintson a **Számítógép/csoport hozzáadása** hivatkozásra a panel jobb oldalán a Műveletek cím alatt.
1. A **Számítógép keresése** párbeszédpanelen az Operations Manager által megfigyelt számítógépekre vagy csoportokra kereshet. Válassza ki a számítógépeket vagy csoportokat, beleértve az Operations Manager Management Server alkalmazást az Azure Monitorba, kattintson a **Hozzáadás**gombra, majd kattintson az **OK**gombra.

A Felügyelt számítógépek csomópont adatgyűjtésre konfigurált számítógépei és csoportjai az Operations Management Suite-ben az Operatív konzol **Adminisztráció** munkaterületén láthatók. Itt szükség szerint hozzá is adhat és el is távolíthat számítógépeket és csoportokat.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Proxybeállítások konfigurálása az Operatív konzolon

Hajtsa végre a következő lépéseket, ha egy belső proxykiszolgáló a felügyeleti csoport és az Azure Monitor között. Ezeket a beállításokat központilag kezeli a felügyeleti csoport, és elosztott ügynök által felügyelt rendszerek, amelyek szerepelnek a hatókörben az Azure Monitor naplóadatok gyűjtésére.  Ez hasznosnak bizonyul, ha egyes megoldások megkerülik a felügyeleti kiszolgálót, és közvetlenül a szolgáltatásba küldik az adatokat.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Bontsa ki a Microsoft Operations Management Suite elemet, majd kattintson a **Kapcsolatok** gombra.
1. Az OMS Connection (OMS-kapcsolat) nézetben kattintson a **Configure Proxy Server** (Proxykiszolgáló konfigurálása) lehetőségre.
1. Az **Operational Management Suite varázsló Proxykiszolgáló lapján** válassza a **Use a proxy server to access the Operations Management Suite** (Proxykiszolgáló használata az Operations Management Suite eléréséhez) lehetőséget, majd írja be az URL-címet a portszámmal együtt (például: http://corpproxy:80), majd kattintson a **Befejezés** gombra.

Ha a proxykiszolgáló hitelesítést igényel, hajtsa végre az alábbi lépéseket a hitelesítő adatok és beállítások konfigurálásához, amelyeket propagálni kell az Azure Monitornak a felügyeleti csoportban jelentést készítő felügyelt számítógépekre.

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. A **RunAs Configuration** (RunAs-konfiguráció) területen válassza a **Profiles** (Profilok) lehetőséget.
1. Nyissa meg a **System Center Advisor Run As Profile Proxy** (System Center Advisor RunAs-profiljának proxyja) profilt.
1. A futtatásiprofil-varázslóban kattintson az Add (Hozzáadás) lehetőségre egy futtató fiók használatához. Létrehozhat egy [futtató fiókot](https://technet.microsoft.com/library/hh321655.aspx), vagy használhat egy meglévő fiókot is. Ennek a fióknak rendelkeznie kell a megfelelő engedélyekkel a proxykiszolgálón való áthaladáshoz.
1. A felügyelni kívánt fiók beállításához válassza az **Egy kijelölt osztály, csoport vagy objektum** lehetőséget, kattintson a **Kiválasztás…** gombra, majd kattintson a **Csoport...** gombra a **Csoport keresése** keresőmező megnyitásához.
1. Keresse meg és válassza ki a **Microsoft System Center Advisor Monitoring Server Group** (Microsoft System Center Advisor monitorozásikiszolgáló-csoport) nevű csoportot. Miután kiválasztotta a csoportot, kattintson az **OK** gombra a **Csoport keresése** keresőmező bezárásához.
1. Kattintson az **OK** gombra a Futtatás hozzáadása fiókként mező **bezárásához.**
1. Kattintson a **Mentés** gombra a varázsló befejezéséhez és a módosítások mentéséhez.

A kapcsolat létrehozása után, és konfigurálja, hogy mely ügynökök gyűjtik és jelentik a naplóadatokat az Azure Monitornak, a rendszer a következő konfigurációt alkalmazza a felügyeleti csoportban, nem feltétlenül sorrendben:

* A **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** futtató fiók létrejön. Ez a fiók a **Microsoft System Center Advisor Run As Profile Blob** (Microsoft System Center Advisor futtatóprofil-blob) futtató blobhoz van társítva, és két osztályt céloz: **Gyűjtési kiszolgáló** és **Operations Manager felügyeleti csoport**.
* Két összekötő jön létre.  Az első neve **Microsoft.SystemCenter.Advisor.DataConnector,** és automatikusan konfigurálva van egy előfizetéssel, amely továbbítja a felügyeleti csoport összes osztályának példányaiból generált összes riasztást az Azure Monitornak. A második összekötő az **Advisor Connector,** amely az Azure Monitorlal való kommunikációért és az adatok megosztásáért felelős.
* A felügyeleti csoportban az adatok gyűjtésére konfigurált ügynökök és csoportok fel lesznek véve a **Microsoft System Center Advisor monitorozásikiszolgáló-csoportba**.

## <a name="management-pack-updates"></a>Felügyeleti csomagok frissítései

A konfiguráció befejezése után az Operations Manager felügyeleti csoport kapcsolatot létesít az Azure Monitor. A felügyeleti kiszolgáló szinkronizál a webszolgáltatással, és felügyeleti csomagok formájában megkapja a frissített konfigurációadatokat az Operations Managerrel való integrációra engedélyezett megoldásokra vonatkozóan. Az Operations Manager megkeresi a felügyeleti csomagok frissítéseit, és automatikusan letölti és importálja őket, amikor elérhetővé válik. Ezt a működést kifejezetten két szabály vezérli:

* **Microsoft.SystemCenter.Advisor.MPUpdate** – Frissíti az Azure Monitor alap felügyeleti csomagjait. Alapértelmezés szerint 12 óránként fut.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – A munkaterületen engedélyezett megoldások felügyeleti csomagjait frissíti. Alapértelmezés szerint öt (5) percenként fut.

Ezt a két szabályt felülbírálhatja, hogy letiltsa őket az automatikus letöltés letiltásával, vagy módosíthatja annak gyakoriságát, hogy a felügyeleti kiszolgáló milyen gyakran szinkronizálja az Azure Monitort, és megállapíthatja, hogy elérhető-e új felügyeleti csomag, és le kell-e tölteni. A [Szabály vagy figyelő felülbírálása](https://technet.microsoft.com/library/hh212869.aspx) cikkben ismertetett lépések mentén módosíthatja a **Gyakoriság** paraméter másodpercben kifejezett értékét, amely a szinkronizálás ütemezését adja meg, vagy az **Engedélyezve** paraméter módosításával letilthatja a szabályokat. Ezeket a szabálymódosításokat az Operations Manager felügyeleti csoport osztályban lévő minden objektumra alkalmazza.

Ha továbbra is követni szeretné a felügyeleti csomagok kiadásainak vezérlésére szolgáló meglévő változáskezelési folyamatot az éles környezetkezelési csoportban, letilthatja a szabályokat, és engedélyezheti azokat bizonyos időszakokban, amikor a frissítések engedélyezettek. Ha a környezetében van olyan fejlesztési vagy QA-felügyeleti csoporttal, amely tud kapcsolódni az internetre, konfigurálhatja a felügyeleti csoportot egy Log Analytics-munkaterülettel, amely támogatja ezt a forgatókönyvet. Ez lehetővé teszi, hogy tekintse át és értékelje ki az Azure Monitor felügyeleti csomagok iteratív kiadásait, mielőtt azokat az éles felügyeleti csoportba adná.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Váltás Operations Manager csoportról egy új Log Analytics-munkaterületre

1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .
1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet, és hozzon létre egy munkaterületet.  
1. Nyissa meg az Operations Manager konzolt az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal, és válassza a **Felügyelet** munkaterületet.
1. Bontsa ki a Log Analytics csomópontot, és válassza a **Kapcsolatok**lehetőséget.
1. Válassza az **Operation Management Suite újrakonfigurálása** hivatkozást, az ablaktábla közepén.
1. Kövesse a **Log Analytics bevezetés varázslót,** és adja meg az új Log Analytics-munkaterülethez társított rendszergazdai fiók e-mail címét vagy telefonszámát és jelszavát.

   > [!NOTE]
   > Az **Operations Management Suite Előkészítési Varázsló: Munkaterület Kiválasztása** lap azt a meglévő munkaterületet mutatja be, amely használatban van.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Az Operations Manager integrációjának ellenőrzése az Azure Monitorral

Többféleképpen is ellenőrizheti, hogy az Azure Monitor az Operations Manager-integráció sikeres-e.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Integráció megerősítése az Azure Portalról

1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket.
1. A Log Analytics-munkaterületek listájában válassza ki a vonatkozó munkaterületet.  
1. Válassza a **Speciális beállítások**, a **Csatlakoztatott Források**, majd a **System Center** elemet.
1. A System Center Operations Manager szakaszában található táblázatban megjelenik a felügyeleti csoport neve, az ügynökök számával együtt, valamint az utolsó adatok beérkezésének állapota.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Integráció megerősítése az Operatív konzolról

1. Nyissa meg az Operations Manager-konzolt, és válassza ki az **Administration** (Adminisztráció) munkaterületet.
1. Kattintson a **Felügyeleti csomagok** elemre, és a **Keresés:** szövegmezőbe írja be az **Advisor** vagy az **Intelligence** kifejezést.
1. Ekkor a keresési eredményekben megjelenik a megfelelő felügyeleti csomag, attól függően, hogy mely megoldások lettek engedélyezve.  Ha például az Alert Management megoldást engedélyezte, akkor a listában a Microsoft System Center Advisor Riasztáskezelés csomag szerepel.
1. A **Monitorozás** nézetből nyissa meg az **Operations Management Suite/Állapot** nézetet.  Jelöljön ki egy felügyeleti kiszolgálót a **Felügyeleti kiszolgáló állapota** ablaktáblán, majd a **Részletes nézet** ablaktáblán erősítse meg, hogy a **Hitelesítési kiszolgálói URI** tulajdonság értéke megfelel-e a Log Analytics-munkaterület azonosítójának.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Integráció eltávolítása az Azure Monitorral

Ha már nincs szüksége az Operations Manager felügyeleti csoport és a Log Analytics-munkaterület közötti integrációra, több lépést is el kell végeznie a kapcsolat és a konfiguráció a felügyeleti csoportban való megfelelő eltávolításának érdekében. Az alábbi eljárás a Log Analytics-munkaterület frissítésével a felügyeleti csoport hivatkozásának törlésével, az Azure Monitor-összekötők törlésével, majd a szolgáltatással való integrációt támogató felügyeleti csomagok törlésével.  

Az Operations Managerrel integrálható megoldások felügyeleti csomagjai és az Azure Monitorlal való integráció támogatásához szükséges felügyeleti csomagok nem törölhetők könnyen a felügyeleti csoportból. Ennek az az oka, hogy az Azure Monitor felügyeleti csomagok némelyike más kapcsolódó felügyeleti csomagoktól függ. Ha törölni szeretné a más felügyeleti csomagoktól függő felügyeleti csomagokat, töltse le a [függőséggel rendelkező felügyeleti csomagok eltávolítására](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) szolgáló szkriptet a TechNet Script Centerből.  

1. Nyissa meg az Operations Manager parancsrendszerhéjat az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal.

    > [!WARNING]
    > A folytatás előtt győződjön meg arról, hogy nem rendelkezik olyan egyéni felügyeleti csomagokkal, amelyek nevében szerepel az Advisor vagy az IntelligencePack szó, mert ha vannak ilyenek, azok is törlődni fognak a következő lépés során a felügyeleti csoportból.
    >

1. A parancsrendszerhéjban írja be a következőt: `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Ezután írja be a következőt: `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Olyan felügyeleti csomagok eltávolításához, amelyek más System Center Advisor felügyeleti csomagoktól függnek, használja a *RecursiveRemove.ps1* szkriptet, amelyet még a TechNet Script Centerből töltött le.  

    > [!NOTE]
    > Az Advisor felügyeleti csomagok PowerShell eltávolításához nem törli automatikusan a Microsoft System Center Advisor vagy a Microsoft System Center Advisor belső felügyeleti csomagok eltávolítása.  Ne próbálja meg törölni őket.  
    >  

1. Nyissa meg az Operations Manager Operatív konzolját az Operations Manager-rendszergazdák szerepkörbe tartozó fiókkal.
1. A **Felügyelet** területen válassza a **Felügyeleti csomagok** csomópontot, majd a **Keresés:** mezőbe gépelje be az **Advisor** szót, és győződjön meg arról, hogy az alábbi felügyeleti csomagok továbbra is importálódnak felügyeleti csoportjába:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Az Azure Portalon kattintson a **Beállítások** csempére.
1. Válassza a **Csatlakoztatott források lehetőséget.**
1. A System Center Operations Manager szakasza alatti táblában meg kell jelennie annak a felügyeleti csoportnak a nevében, amelyet el szeretne távolítani a munkaterületről. A **Legutóbbi adatok** oszlop alatt kattintson az **Eltávolítás** elemre.  

    > [!NOTE]
    > Az **Eltávolítás** hivatkozás csak 14 nap múlva válik elérhetővé, ha nem észlelhető aktivitás a csatlakoztatott felügyeleti csoportban.  
    >

1. Ezután megjelenik egy ablak, amely arra kéri, hogy erősítse meg eltávolítási szándékát.  A továbblépéshez kattintson az **Igen** gombra.

Ha törölni szeretné a két összekötőt (a Microsoft.SystemCenter.Advisor.DataConnectort és az Advisor-összekötőt), mentse számítógépére az alábbi PowerShell-szkriptet, és hajtsa végre az alábbi példák segítségével:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> A számítógépen, amelyről a szkriptet futtatja, ha nem felügyeleti kiszolgáló, telepítve kell lennie az Operations Manager-parancsrendszerhéjnak, a felügyeleti csoport verziójától függően.
>
>

```powershell
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

A jövőben, ha azt tervezi, hogy újra csatlakoztatja a felügyeleti csoportot egy `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` Log Analytics-munkaterülethez, újra kell importálnia a felügyeleti csomag fájlt. A környezetében telepített System Center Operations Manager verziójától függően a fájlt a következő helyen találja:

* A forrás-adathordozón a `\ManagementPacks` System Center 2016 – Operations Manager mappában vagy felette.
* A felügyeleti csoporton alkalmazott legújabb kumulatív frissítésben. Az Operations Manager 2012 esetében `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` a forrásmappa, a 2012 `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`R2 esetében pedig a.

## <a name="next-steps"></a>További lépések

A funkciók hozzáadásához és az adatok gyűjtéséhez olvassa el [az Azure Monitor-megoldások hozzáadása a Megoldások galériából című témakört.](../../azure-monitor/insights/solutions.md)
