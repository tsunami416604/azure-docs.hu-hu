---
title: 'Azure ExpressRoute: NPM konfigurálása áramkörökhöz'
description: Konfigurálja a felhőalapú hálózati figyelést (NPM) az Azure ExpressRoute-áramkörökhöz. Ez magában foglalja az ExpressRoute privát társviszony-létesítés és a Microsoft társviszony-létesítés figyelését.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.openlocfilehash: 54fa3dcbfbbcb3153f81407a9bc9b52511405390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076591"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Network Performance Monitor for ExpressRoute konfigurálása

Ez a cikk segít konfigurálni a Hálózati teljesítményfigyelő bővítményt az ExpressRoute figyelésére. A Network Performance Monitor (NPM) egy felhőalapú hálózatmonitorozási megoldás, amely az Azure felhőkörnyezetek és a helyszíni környezetek (fiókirodák stb.) közötti kapcsolatot monitorozza. Az NPM az Azure Monitor-naplók részét képezi. Az NPM által biztosított ExpressRoute-bővítménnyel monitorozhatja a privát vagy Microsoft-társviszony használatára konfigurált ExpressRoute-kapcsolatcsoportok hálózati teljesítményét. Az NPM ExpressRoute használatára való konfigurálásával azonosíthatja és elkerülheti a hálózati problémákat. Ez a szolgáltatás az Azure Government felhő esetében is elérhető.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A következőket teheti:

* A veszteség és a késés figyelése a különböző virtuális hálózatok között, és riasztások beállítása

* A hálózat összes útvonalának figyelése (beleértve a redundáns elérési utakat is)

* A nehezen replikálható átmeneti és időponthoz elegendő hálózati problémák elhárítása

* A hálózat egy adott szegmensének meghatározásához, amely a csökkent teljesítményért felelős

* Átviteli forgalom virtuális hálózatonként (Ha az ügynökök telepítve vannak az egyes virtuális hálózatokban)

* Az ExpressRoute-rendszer állapotának megtekintése egy korábbi időpontból

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

A figyelési ügynökök több kiszolgálóra vannak telepítve, mind a helyszínen, mind az Azure-ban. Az ügynökök kommunikálnak egymással, de nem küldenek adatokat, TCP kézfogási csomagokat küldenek. Az ügynökök közötti kommunikáció lehetővé teszi az Azure számára, hogy leképezze a hálózati topológiát és a forgalmat igénybe vehet útvonalat.

1. NPM-munkaterület létrehozása. Ez megegyezik a Log Analytics-munkaterülettel.
2. Szoftverügynökök telepítése és konfigurálása. (Ha csak a Microsoft társviszony-létesítést szeretné figyelni, nem kell szoftverügynököket telepítenie és konfigurálnia.): 
    * Felügyeleti ügynökök telepítése a helyszíni kiszolgálókon és az Azure virtuális gépek (a privát társviszony-létesítés).
    * Konfigurálja a figyelési ügynök kiszolgálóinak beállításait, hogy a figyelési ügynökök kommunikálhassanak. (Tűzfalportok megnyitása stb.)
3. Konfigurálja a hálózati biztonsági csoport (NSG) szabályokat, hogy az Azure virtuális gépeken telepített figyelési ügynök kommunikáljon a helyszíni figyelőügynökökkel.
4. Figyelés beállítása: Automatikus felderítése és annak kezelése, hogy mely hálózatok láthatók az NPM-ben.

Ha már használja a Hálózati teljesítményfigyelőt más objektumok vagy szolgáltatások figyelésére, és már rendelkezik munkaterülettel a támogatott régiók egyikében, kihagyhatja az 1.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>1. lépés: Munkaterület létrehozása

Hozzon létre egy munkaterületet az előfizetésben, amely a virtuális hálózatok az ExpressRoute-kapcsolat(ok)ra hivatkozik.

1. Az [Azure Portalon](https://portal.azure.com)válassza ki azt az előfizetést, amelynek a VNET-k az ExpressRoute-kapcsolatlétesítési az ExpressRoute-kapcsolatban. Ezután keresse meg a szolgáltatások listáját a **Marketplace-en** a "Hálózati teljesítményfigyelő". A visszatérésnél kattintson ide a **Hálózati teljesítményfigyelő** lap megnyitásához.

   >[!NOTE]
   >Létrehozhat egy új munkaterületet, vagy használhat egy meglévő munkaterületet. Ha meglévő munkaterületet szeretne használni, győződjön meg arról, hogy a munkaterületet áttelepítették az új lekérdezési nyelvre. [További információ...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portál](./media/how-to-npm/3.png)<br><br>
2. A fő Hálózati **teljesítményfigyelő** lap alján kattintson a **Létrehozás** gombra a **Hálózati teljesítményfigyelő – Új megoldás létrehozása** lap megnyitásához. Kattintson **a Log Analytics Workspace (Naplóelemzési munkaterület) elemre – válasszon munkaterületet** a Munkaterületek lap megnyitásához. Kattintson **a + Új munkaterület létrehozása gombra** a Munkaterület lap megnyitásához.
3. A **Log Analytics munkaterület** lapon válassza az **Új létrehozása**lehetőséget, majd adja meg a következő beállításokat:

   * Log Analytics-munkaterület – írja be a munkaterület nevét.
   * Előfizetés – Ha több előfizetéssel rendelkezik, válassza ki azt, amelyet az új munkaterülethez szeretne társítani.
   * Erőforráscsoport – Erőforráscsoport létrehozása vagy meglévő csoport használata.
   * Hely – Ez a hely adja meg az ügynök kapcsolati naplóihoz használt tárfiók helyét.
   * Tarifacsomag – válassza ki a tarifacsomagot.
  
     >[!NOTE]
     >Az ExpressRoute-áramkör a világ bármely pontján lehet. Nem kell ugyanabban a régióban, mint a munkaterület.
     >
  
     ![munkaterület](./media/how-to-npm/4.png)<br><br>
4. A beállítássablon mentéséhez és telepítéséhez kattintson az **OK** gombra. Miután a sablon érvényesíti, kattintson a **Létrehozás** gombra a munkaterület üzembe helyezéséhez.
5. A munkaterület üzembe helyezése után keresse meg a létrehozott **NetworkMonitoring(name)** erőforrást. Érvényesítse a beállításokat, majd kattintson a **Megoldás további konfigurációra van szüksége**gombra.

   ![további konfiguráció](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>2. lépés: Ügynökök telepítése és konfigurálása

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2.1: Az ügynök beállítási fájljának letöltése

1. Nyissa meg az erőforrás **Hálózati teljesítményfigyelő konfigurációja** lapjának **Általános beállítások** lapját. Kattintson a kiszolgáló processzorának megfelelő ügyintézőre a **Log Analytics ügynökök telepítése** szakaszban, és töltse le a telepítőfájlt.
2. Ezután másolja a **munkaterület azonosítót** és **az elsődleges kulcsot** a Jegyzettömbbe.
3. A **Log Analytics-ügynökök konfigurálása a TCP protokoll használatával figyelése** szakaszból töltse le a Powershell-parancsfájlt. A PowerShell-parancsfájl segítségével megnyithatja a tcp-tranzakciók megfelelő tűzfalportját.

   ![PowerShell-szkript](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2.2: Telepítsen egy figyelési ügynököt minden figyelési kiszolgálóra (minden figyelni kívánt virtuális hálózatra)

Javasoljuk, hogy legalább két ügynököt telepítsen az ExpressRoute-kapcsolat mindkét oldalára redundancia (például helyszíni, Azure VNETs) érdekében. Az ügynököt Windows Server (2008 SP1 vagy újabb) kiszolgálóra kell telepíteni. Az ExpressRoute-áramkörök figyelése Windows asztali operációs rendszerrel és Linux operációs rendszerrel nem támogatott. Az ügynökök telepítéséhez kövesse az alábbi lépéseket:
   
  >[!NOTE]
  >Az SCOM (beleértve az [MMA-t](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)is) által leadott ügynökök előfordulhat, hogy nem tudják következetesen észlelni a helyüket, ha az Azure-ban vannak üzemeltetve. Azt javasoljuk, hogy ne használja ezeket az ügynököket az Azure VNETs ExpressRoute figyelésére.
  >

1. Futtassa a **telepítőt,** és telepítse az ügynököt minden olyan kiszolgálóra, amelyet az ExpressRoute figyelésére használni kíván. A figyeléshez használt kiszolgáló lehet virtuális gép vagy helyszíni, és internet-hozzáféréssel kell rendelkeznie. Legalább egy ügynököt telepítenie kell a helyszínen, és minden olyan hálózati szegmensben, amelyet figyelni szeretne az Azure-ban, minden egyes hálózati szegmensben telepítenie kell.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **Licencfeltételek** lapon olvassa el a licencet, majd kattintson az **Elfogadom gombra.**
4. A **Célmappa** lapon módosítsa vagy tartsa meg az alapértelmezett telepítési mappát, majd kattintson a **Tovább**gombra.
5. Az **Ügynök beállítási beállításai** lapon kiválaszthatja, hogy az ügynököt csatlakoztatja-e az Azure Monitor naplóihoz vagy az Operations Managerhez. Vagy üresen hagyhatja a választási lehetőségeket, ha később szeretné konfigurálni az ügynököt. A kijelölés(ek) után kattintson a **Tovább**gombra.

   * Ha úgy döntött, hogy csatlakozik az **Azure Log Analytics,** illessze be a **munkaterület-azonosító** és **a munkaterület kulcs** (elsődleges kulcs), amely et másolt a Jegyzettömb az előző szakaszban. Ezután kattintson a **Tovább gombra.**

     ![Azonosító és kulcs](./media/how-to-npm/8.png)
   * Ha az **Operations Manager**szolgáltatáshoz való csatlakozást választotta, írja be a **Felügyeleti csoport konfigurációja** lapot, írja be a **Felügyeleti csoport nevét**, a Felügyeleti **kiszolgáló**és a Felügyeleti **kiszolgáló portját.** Ezután kattintson a **Tovább gombra.**

     ![Operations Manager](./media/how-to-npm/9.png)
   * Az **Ügynöki műveletfiók** lapon válassza a **Helyi rendszer** fiók vagy a Tartomány vagy a **Helyi számítógépfiók lehetőséget.** Ezután kattintson a **Tovább gombra.**

     ![Fiók](./media/how-to-npm/10.png)
6. A **Felszerelésre való felkészülés** lapon tekintse át a beállításokat, majd kattintson a **Telepítés gombra.**
7. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.
8. Ha elkészült, a Microsoft monitoringügynök megjelenik a Vezérlőpulton. Tekintse át a konfigurációt, és ellenőrizze, hogy az ügynök csatlakozik-e az Azure Monitor naplóihoz. Csatlakoztatva az ügynök a következő üzenetet jeleníti meg: **A Microsoft Monitoring Agent sikeresen csatlakozott a Microsoft Operations Management Suite szolgáltatáshoz.**

9. Ismételje meg ezt az eljárást minden egyes szükséges virtuális hálózatesetén.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2.3: Proxybeállítások konfigurálása (nem kötelező)

Ha webproxyt használ az internet eléréséhez, az alábbi lépésekkel konfigurálhatja a Microsoft Monitoring Agent proxybeállításait. Hajtsa végre ezeket a lépéseket az egyes kiszolgálókon. Ha sok kiszolgálót kell konfigurálnia, akkor érdemes lehet parancsfájl használatával automatizálni a folyamatot. Ha igen, olvassa el [a Microsoft Monitoring Agent proxybeállításainak konfigurálása parancsfájl használatával című témakört.](../log-analytics/log-analytics-windows-agent.md)

A Microsoft monitoringügynök proxybeállításainak konfigurálása a Vezérlőpulthasználatával:

1. Nyissa meg a **Vezérlőpultot**.
2. Válassza a **Microsoft Monitoring Agent** lehetőséget.
3. Kattintson a **Proxy Settings** (Proxybeállítások) fülre.
4. Válassza **a Proxykiszolgáló használata** lehetőséget, és szükség esetén írja be az URL-címet és a portszámát. Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgáló eléréséhez.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2.4: Az ügynök kapcsolatának ellenőrzése

Könnyedén ellenőrizheti, hogy az ügynökök kommunikálnak-e.

1. A figyelőügynökkel rendelkező kiszolgálón nyissa meg a **Vezérlőpultot**.
2. Nyissa meg a **Microsoft monitoring ügynököt**.
3. Kattintson az **Azure Log Analytics** fülre.
4. Az **Állapot** oszlopban látnia kell, hogy az ügynök sikeresen csatlakozott az Azure Monitor naplók.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2.5: Nyissa meg a tűzfalportokat a figyelőügynök-kiszolgálókon

A TCP protokoll használatához meg kell nyitnia a tűzfalportokat, hogy a figyelési ügynökök kommunikálhassanak.

PowerShell-parancsfájl futtatásával létrehozhatja a Hálózati teljesítményfigyelő által igényelt beállításkulcsokat. Ez a parancsfájl a Windows tűzfal szabályait is létrehozza, amelyek lehetővé teszik a figyelési ügynökök számára, hogy TCP-kapcsolatokat hozzanak létre egymással. A parancsfájl által létrehozott beállításkulcsok határozzák meg, hogy naplózzák-e a hibakeresési naplókat, valamint a naplófájl elérési útját. Azt is meghatározza az ügynök TCP port kommunikációs használt. A kulcsok értékeit a parancsfájl automatikusan beállítja. Ne módosítsa manuálisan ezeket a kulcsokat.

A 8084-es port alapértelmezés szerint meg van nyitva. Egyéni portot úgy használhat, hogy megadja a "portNumber" paramétert a parancsfájlnak. Ha azonban így tesz, ugyanazt a portot kell megadnia minden olyan kiszolgálóhoz, amelyen a parancsfájlt futtatja.

>[!NOTE]
>Az "EnableRules" PowerShell-parancsfájl csak azon a kiszolgálón konfigurálja a Windows tűzfal szabályait, amelyen a parancsfájl fut. Ha hálózati tűzfallal rendelkezik, győződjön meg arról, hogy engedélyezi a Hálózati teljesítményfigyelő által használt TCP-portra szánt forgalmat.
>
>

Az ügynökkiszolgálókon nyisson meg egy rendszergazdai jogosultságokkal rendelkező PowerShell-ablakot. Futtassa az [EnableRules](https://aka.ms/npmpowershellscript) PowerShell-parancsfájlt (amelyet korábban letöltött). Ne használjon semmilyen paramétert.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>3. lépés: Hálózati biztonsági csoport szabályainak konfigurálása

Az Azure-ban található ügynökkiszolgálók figyeléséhez konfigurálnia kell a hálózati biztonsági csoport (NSG) szabályait, hogy a TCP-forgalom az NPM által szintetikus tranzakciókhoz használt porton engedélyezze a TCP-forgalmat. Az alapértelmezett port a 8084. Ez lehetővé teszi, hogy egy Azure-virtuális gépre telepített figyelési ügynök kommunikáljon egy helyszíni felügyeleti ügynökkel.

Az NSG-ről további információt a [Hálózati biztonsági csoportok című témakörben talál.](../virtual-network/virtual-networks-create-nsg-arm-portal.md)

>[!NOTE]
>Győződjön meg arról, hogy telepítette az ügynökök (mind a helyszíni kiszolgálóügynök és az Azure-kiszolgálóügynök), és futtassa a PowerShell-parancsfájlt, mielőtt ezzel a lépéssel.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>4. lépés: Társviszony-létesítési kapcsolatok felfedezése

1. Keresse meg a Hálózati teljesítményfigyelő áttekintő csempéjét a **Minden erőforrás** lapon, majd kattintson az engedélyezési npm-munkaterületre.

   ![npm munkaterület](./media/how-to-npm/npm.png)
2. Az irányítópult beállításához kattintson a **Hálózatteljesítmény-figyelő** áttekintő csempéjére. Az irányítópult tartalmaz egy ExpressRoute-lapot, amely azt mutatja, hogy az ExpressRoute "nem konfigurált állapotban" van. Kattintson **a Szolgáltatás beállítása gombra** a Hálózati teljesítményfigyelő konfigurációs lapjának megnyitásához.

   ![szolgáltatás beállítása](./media/how-to-npm/npm2.png)
3. A konfigurációs lapon keresse meg az "ExpressRoute Társviszony-létesítések" lapot, amely a bal oldali panelen található. Ezután kattintson a **Discover Now gombra.**

   ![Felfedezni](./media/how-to-npm/13.png)
4. Amikor a felderítés befejeződik, megjelenik egy lista, amely a következő elemeket tartalmazza:
   * Az ExpressRoute-kapcsolati kapcsolat(ok) összes, ehhez az előfizetéshez társított Microsoft társviszony-létesítési kapcsolati kapcsolata.
   * Az előfizetéshez társított virtuális hálózatokhoz kapcsolódó összes privát társviszony-létesítő kapcsolat.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>5. lépés: Monitorok konfigurálása

Ebben a szakaszban konfigurálja a monitorokat. Kövesse a figyelni kívánt társviszony-létesítés itípusának lépéseit: **privát társviszony-létesítés**vagy **Microsoft-társviszony-létesítés**.

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés

A privát társviszony-létesítés, amikor a felderítés befejeződik, megjelenik az egyedi **körkapcsolati név** és **a virtuális hálózat neve**szabályok . Kezdetben ezek a szabályok le vannak tiltva.

![szabályok](./media/how-to-npm/14.png)

1. Jelölje be a **Társviszony-létesítés figyelése** jelölőnégyzetet.
2. Jelölje be az **Állapotfigyelés engedélyezése jelölőnégyzetet ehhez a társviszony-létesítéshez.**
3. Válassza ki a figyelési feltételeket. Egyéni küszöbértékek állíthat be az állapotesemények létrehozásához a küszöbértékek beírásával. Amikor a feltétel értéke meghaladja a kiválasztott hálózati/alhálózati pár ra vonatkozó kiválasztott küszöbértéket, a rendszer állapoteseményt hoz létre.
4. Kattintson az ON-PREM ügynökök **ügynökök hozzáadása** gombra azoknak a helyszíni kiszolgálóknak a hozzáadásához, amelyekről figyelni szeretné a privát társviszony-létesítési kapcsolatot. Győződjön meg arról, hogy csak olyan ügynököket választ, amelyek a 2. A helyszíni ügynököknek el kell tudniuk érni a végpontot az ExpressRoute-kapcsolat használatával.
5. Mentse a beállításokat.
6. Miután engedélyezte a szabályokat, és kiválasztotta a figyelni kívánt értékeket és ügynököket, körülbelül 30–60 percet kell várnia az értékek feltöltésének megkezdésére, és az **ExpressRoute figyelési** csempék elérhetővé válnak.

### <a name="microsoft-peering"></a>Microsoft társviszony-létesítés

Microsoft-társviszony-létesítés esetén kattintson a figyelni kívánt Microsoft társviszony-létesítési kapcsolat(ok)ra, és konfigurálja a beállításokat.

1. Jelölje be a **Társviszony-létesítés figyelése** jelölőnégyzetet. 
2. (Nem kötelező) Módosíthatja a cél Microsoft szolgáltatás végpont. Alapértelmezés szerint az NPM egy Microsoft-szolgáltatásvégpontot választ célként. Az NPM figyeli a helyszíni kiszolgálók és a célvégpont közötti kapcsolatot az ExpressRoute-on keresztül. 
    * A célvégpont módosításához kattintson a **Cél:**( **szerkesztés)** hivatkozásra, és válasszon másik Microsoft-szolgáltatási célvégpontot az URL-címek listájából.
      ![cél szerkesztése](./media/how-to-npm/edit_target.png)<br>

    * Egyéni URL-címet vagy IP-címet is használhat. Ez a beállítás különösen akkor fontos, ha microsoftos társviszony-létesítést használ az Azure PaaS-szolgáltatásokkal, például az Azure Storage- és SQL-adatbázisokkal és a nyilvános IP-címeken kínált webhelyekkel való kapcsolat létesítéséhez. Ehhez kattintson az URL-címlista alján található hivatkozásra **(Egyéni URL-cím vagy IP-cím használata),** majd adja meg az ExpressRoute-alapú Microsoft-társviszony-létesítésen keresztül csatlakoztatott Azure PaaS-szolgáltatás nyilvános végpontját.
    ![egyéni URL](./media/how-to-npm/custom_url.png)<br>

    * Ha ezeket a választható beállításokat használja, győződjön meg arról, hogy csak a Microsoft szolgáltatásvégpont van kijelölve. A végpontot az ExpressRoute-hoz kell csatlakoztatni, és a helyszíni ügynököknek el kell érniük.
3. Jelölje be az **Állapotfigyelés engedélyezése jelölőnégyzetet ehhez a társviszony-létesítéshez.**
4. Válassza ki a figyelési feltételeket. Egyéni küszöbértékek állíthat be az állapotesemények létrehozásához a küszöbértékek beírásával. Amikor a feltétel értéke meghaladja a kiválasztott hálózati/alhálózati pár ra vonatkozó kiválasztott küszöbértéket, a rendszer állapoteseményt hoz létre.
5. Kattintson az ON-PREM ÜGYNÖKÖK **ügynökök hozzáadása** gombra azoknak a helyszíni kiszolgálóknak a hozzáadásához, amelyekről figyelni szeretné a Microsoft társviszony-létesítési kapcsolatot. Győződjön meg arról, hogy csak olyan ügynököket választ, amelyek a 2. A helyszíni ügynököknek el kell tudniuk érni a végpontot az ExpressRoute-kapcsolat használatával.
6. Mentse a beállításokat.
7. Miután engedélyezte a szabályokat, és kiválasztotta a figyelni kívánt értékeket és ügynököket, körülbelül 30–60 percet kell várnia az értékek feltöltésének megkezdésére, és az **ExpressRoute figyelési** csempék elérhetővé válnak.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>6. lépés: Figyelőcsempék megtekintése

Miután megjelenik a figyelési csempék, az ExpressRoute-áramkörök és a kapcsolati erőforrások az NPM figyeli. A Microsoft társviszony-létesítési csempére kattintva részletezheti a Microsoft társviszony-létesítési kapcsolatok állapotát.

![csempék figyelése](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Hálózati teljesítményfigyelő lap

Az NPM lap tartalmaz egy lapot az ExpressRoute számára, amely áttekintést nyújt az ExpressRoute-áramkörök és társviszony-létesítések állapotáról.

![Irányítópult](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Áramkörök listája

Az összes figyelt ExpressRoute-kapcsolat listájának megtekintéséhez kattintson az **ExpressRoute-áramkörök** csempére. Kiválaszthatja az áramkört, és megtekintheti annak állapotát, a csomagvesztés trenddiagramjait, a sávszélesség-kihasználtságot és a késést. A diagramok interaktívak. A diagramok ábrázolásához egyéni időablakot is kiválaszthat. Az egérmutatót a diagram egy területe fölé húzva nagyíthatja és láthatja a részletes adatpontokat.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>A veszteség, a késés és az átviteli képesség tendenciája

A sávszélesség, a késés és a veszteségdiagramok interaktívak. Az egérvezérlőelemekkel a diagramok bármely szakaszát nagyíthatja. A sávszélesség- és késésadatokat a többi intervallumhoz a Bal felső sarokban található Műveletek gomb alatt található **Dátum/idő**gombra kattintva is megtekintheti.

![Trend](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Társviszony-létesítések listája

A virtuális hálózatokhoz való összes kapcsolat listájának megtekintéséhez a privát társviszony-létesítésen keresztül kattintson a **Privát társviszony-létesítések** csempére az irányítópulton. Itt kiválaszthatja a virtuális hálózati kapcsolatot, és megtekintheti annak állapotát, a csomagvesztés trenddiagramjait, a sávszélesség-kihasználtságot és a késést.

![áramkörlista](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Csomópontok nézet

A helyszíni csomópontok és a kiválasztott ExpressRoute-társviszony-létesítési kapcsolat Azure-beli virtuális gépei/Microsoft-szolgáltatásvégpontjai közötti összes kapcsolat listájának megtekintéséhez kattintson a **Csomópontkapcsolatok megtekintése gombra.** Megtekintheti az egyes hivatkozások állapotát, valamint a hozzájuk társított veszteség és késés trendjét.

![csomópontok nézet](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Áramköri topológia

Az áramköri topológia megtekintéséhez kattintson a **Topológia** csempére. Ezzel a kijelölt áramkör vagy társviszony-létesítés topológia nézetére kerül. A topológiadiagram a hálózat minden szegmensének késését biztosítja. Minden réteg 3 ugrás képviseli a csomópont a diagram. A hopra kattintva további részletek jelennek meg a hopról.

A **szűrők**alatti csúszka mozgatásával növelheti a láthatóság szintjét, hogy a helyszíni ugrásokat is tartalmazza. A csúszka balra vagy jobbra mozgatásával nő/csökken a topológiadiagramon lévő ugrások száma. Az egyes szegmensek késése látható, ami lehetővé teszi a nagy késleltetésű szegmensek gyorsabb elkülönítését a hálózaton.

![szűrők](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Áramkör részletes topológiája

Ebben a nézetben virtuális hálózati kapcsolatok láthatók.
![részletes topológia](./media/how-to-npm/17.png)
