---
title: "Hálózati Teljesítményfigyelő konfigurálása az Azure ExpressRoute-Kapcsolatcsoportok |} Microsoft Docs"
description: "Konfigurálja a felhő alapú hálózatfigyelési az Azure ExpressRoute-Kapcsolatcsoportok."
documentationcenter: na
services: expressroute
author: ajaycode
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: agummadi
ms.openlocfilehash: 4d5bf1550ecd5982e51c0ae8d3917102d2f7c253
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Az ExpressRoute hálózati Teljesítményfigyelő konfigurálása

Hálózati teljesítmény figyelése (NPM) egy olyan felhőalapú hálózati felügyeleti megoldás, amely figyeli az Azure-alapú telepítések és (fiókirodákban, stb.) a helyszíni helyek közötti kapcsolatot. NPM része a Microsoft Operations Management Suite (OMS). NPM most kibővíti az ExpressRoute, amely lehetővé teszi a hálózati teljesítmény figyeléséhez használja a magánhálózati társviszony-létesítés ExpressRoute-Kapcsolatcsoportok keresztül. NPM az ExpressRoute konfigurálásakor hálózati problémák azonosításához, és kiszűri észlelését.

A következőket teheti:

* Különböző Vnetekhez különböző veszteséget és késéseket figyelése és állíthatók be riasztások

* Összes elérési utat (például a redundáns elérési utak) a hálózat figyelése

* Nehezen replikálni átmeneti és időpontban hálózati probléma elhárításához

* Meghatározásához, hogy egy adott szegmensnek a hálózaton, a teljesítmény csökkenését felelős

* Virtuális hálózatonként átviteli beolvasása (ha van minden egyes virtuális telepített ügynökök)

* Tekintse meg a ExpressRoute rendszerállapot egy előző pont időben

## <a name="regions"></a>Régiók

Figyelheti a világ minden részén ExpressRoute-Kapcsolatcsoportok üzemeltetett munkaterület használatával a következő területek közül:

* Nyugat-Európa
* USA nyugati középső régiója
* USA keleti régiója 
* Délkelet-Ázsia 
* Dél-kelet-Ausztrália

## <a name="workflow"></a>munkafolyamat

Több kiszolgáló figyelési ügynökök telepítve vannak a helyszíni és az Azure-ban. Az ügynökök kommunikálnak egymással, de ne küldjön adatokat, azok TCP kézfogás csomagok küldése. Az ügynökök közötti kommunikáció lehetővé teszi, hogy az Azure-t azoknak a hálózati topológia és a forgalmat több elérési út.

1. Hozzon létre egy NPM munkaterület közül a [támogató régiók](#regions).
2. Telepítse és konfigurálja a szoftverfrissítési ügynökök: 
    * A telepítés figyelését a helyszíni kiszolgálók és az Azure virtuális gépeken futó ügynököket.
    * A beállítások a kiszolgálókon figyelési ügynök engedélyezése a figyelőügynökök való kommunikációhoz. (Nyissa meg a tűzfal portjait, stb.)
3. Konfigurálja a hálózati biztonsági csoport (NSG) szabályokat az ügynök figyelését a helyszíni kommunikálni Azure virtuális gépeken telepített figyelési ügynökökhöz.
4. Figyelés beállítása: automatikus felderítése és kezelése a hálózatok láthatók a NPM.

Használata hálózati Teljesítményfigyelő más objektumok, vagy a szolgáltatások figyelésére, és már rendelkezik munkaterület a támogatott régiók egyikéhez sem, ha az 1. lépésben és a 2. lépés kihagyhatja, és a konfiguráció 3. lépés megkezdése.

## <a name="configure"></a>1. lépés: A munkaterület létrehozása

Munkaterület létrehozása, amely rendelkezik az ExpressRoute circuit(s) Vnetek hivatkozásra az előfizetést.

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki az előfizetést, amely rendelkezik a Vnetek társítottak, az ExpressRoute-kapcsolatcsoportot. Majd keresse meg a szolgáltatások listájában a **piactér** "Hálózati Teljesítményfigyelő". Megnyitásához kattintson a vissza a **hálózati Teljesítményfigyelő** lap.

>[!NOTE]
>Előfordulhat, hogy hozzon létre egy új munkaterületet, vagy egy meglévő munkaterületen.  Ha szeretne egy meglévő munkaterületen, győződjön meg róla, hogy a munkaterület áttelepítése megtörtént-e az új lekérdezési nyelv. [További információ...](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-log-search-upgrade)
>

  ![portal](.\media\how-to-npm\3.png)<br><br>
2. A fő alján **hálózati Teljesítményfigyelő** kattintson **létrehozása** megnyitásához **hálózati Teljesítményfigyelő - hozzon létre új megoldás** lap. Kattintson a **OMS-munkaterület - ki kell jelölnie egy munkaterület** munkaterületek lapjának megnyitásához. Kattintson a **+ hozzon létre új munkaterület** munkaterület lapjának megnyitásához.
3. Az a **OMS-munkaterület** lapon jelölje be **hozzon létre új** és a következő beállításokat:

  * OMS-munkaterület - adja meg a munkaterület nevét.
  * Előfizetés – Ha több előfizetéssel, válasszon egyet az új munkaterületet társítani kívánt rendelkezik.
  * Erőforráscsoport - hozzon létre egy erőforráscsoportot, vagy használjon egy meglévőt.
  * Hely - ki kell választania egy [támogatott régióban](#regions).
  * Tarifacsomagra - válassza az "ingyenes"
  
  >[!NOTE]
  >Az ExpressRoute-kapcsolatcsoport a világon bárhol lehet, és nem rendelkezik a munkaterületen ugyanabban a régióban kell.
  >
  
  ![Munkaterület](.\media\how-to-npm\4.png)<br><br>
4. Kattintson a **OK** mentéséhez és a beállítások-sablon üzembe helyezése. Amikor érvényesíti a sablont, kattintson a **létrehozása** központi telepítése a munkaterületen.
5. A munkaterület központi telepítése után nyissa meg a **NetworkMonitoring(name)** létrehozott erőforrás. Ellenőrizze a beállításokat, majd kattintson a **megoldás további konfigurálást igényel**.

  ![további beállítások](.\media\how-to-npm\5.png)

## <a name="agents"></a>2. lépés: Telepítse és konfigurálja az ügynökök

### <a name="download"></a>2.1: az ügynök telepítési fájl letöltése

1. Lépjen a **általános beállítások** lapján a **hálózati teljesítmény figyelési konfiguráció** lap az erőforrás. Kattintson az ügynök, amely megfelel a kiszolgáló processzor a a **OMS-ügynökök telepítése** szakaszt, és töltse le a telepítő fájlját.

  >[!NOTE]
  >Az ügynököt telepíteni kell a Windows Server (2008 SP1 vagy újabb). Windows asztali operációs rendszer és a Linux operációs rendszert futtató ExpressRoute-Kapcsolatcsoportok megfigyelése nincs támogatva. 
  >
  >
2. Ezután másolja a **munkaterület azonosítója** és **elsődleges kulcs** a Jegyzettömbbe.
3. Az a **OMS-ügynökök konfigurálása figyelés a TCP protokollt használó** szakaszban, töltse le a Powershell-parancsfájlt. A PowerShell-parancsfájl segítségével nyissa meg a megfelelő tűzfal port a TCP-tranzakciók.

  ![PowerShell-szkript](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: a figyelési ügynök telepítése minden felügyeleti kiszolgálón (az egyes virtuális Hálózatot, amely segítségével nyomon követni kívánt)

Azt javasoljuk, hogy telepítse a redundancia érdekében legalább két ügynököket (vagyis a helyszíni Azure Vnetekhez) ExpressRoute-kapcsolat mindkét oldalán. Az ügynökök telepítéséhez tegye a következőket:

1. Futtatás **telepítő** ExpressRoute figyelemmel kísérésére használni kívánt összes kiszolgálón az ügynök telepítéséhez. A figyeléshez használt kiszolgáló lehet egy virtuális Gépet, vagy a helyszíni és internetkapcsolattal kell rendelkeznie. Azure a figyelni kívánt hálózati szegmenshez legalább egy ügynök-hez, és egy ügynököt telepíteni szeretné.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. Az a **licencfeltételeket** lapon olvassa el a licencfeltételeket, és kattintson a **elfogadom**.
4. Az a **célmappa** lapon módosítani vagy hagyja az alapértelmezett telepítési mappa, és kattintson a **következő**.
5. Az a **ügynök telepítésének beállításai** lap, dönthet úgy, hogy az ügynök csatlakoztatása az Azure Naplóelemzés (OMS) vagy az Operations Manager. Vagy a választási lehetőségek üresen hagyhatja, ha az ügynök később konfigurálni szeretné. Miután kiválasztotta a selection(s), kattintson a **következő**.

  * Ha úgy döntött, hogy csatlakozni **Azure Naplóelemzés (OMS)**, illessze be a **munkaterület azonosítója** és **Munkaterületkulcsot** (elsődleges kulcs), másolja a Jegyzettömbbe az előző szakaszban. Ezután kattintson a **Tovább** gombra.

    ![Azonosítója és kulcsa](.\media\how-to-npm\8.png)
  * Ha úgy döntött, hogy csatlakozni **Operations Manager**, a **felügyeleti csoport konfigurációja** írja be a **felügyeleti csoport neve**, **felügyeleti kiszolgáló** , és a **felügyeleti kiszolgáló portszáma**. Ezután kattintson a **Tovább** gombra.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * Az a **Ügynökműveleti fiók** lapon, vagy válassza ki a **helyi rendszer** fiókot, vagy **tartományi vagy helyi számítógépfiók**. Ezután kattintson a **Tovább** gombra.

    ![Fiók](.\media\how-to-npm\10.png)
6. Az a **telepítésre kész** lapon ellenőrizze a megadott beállításokat, és kattintson a **telepítése**.
7. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.
8. Amikor végzett, a Microsoft Monitoring Agent jelenik meg a Vezérlőpultot. Tekintse át a hiba a konfiguráció, és ellenőrizze, hogy az ügynök csatlakozik-e az Operational Insights (OMS). Csatlakoztatva az OMS-be, amikor az ügynök jeleníti meg a következő üzenet: **a Microsoft Monitoring Agent sikeresen csatlakozott-e a Microsoft Operations Management Suite szolgáltatással**.

9. Ismételje meg a figyelést igénylő minden vnet.

### <a name="proxy"></a>2.3: konfigurálja a proxybeállításokat (nem kötelező)

Ha olyan webproxyt használ az Internet eléréséhez, az alábbi lépések segítségével a Microsoft Monitoring Agent proxy beállításainak konfigurálása. Hajtsa végre ezeket a lépéseket minden olyan kiszolgáló. Ha sok kiszolgálót kell konfigurálnia, akkor érdemes lehet parancsfájl használatával automatizálni a folyamatot. Ha igen, tekintse meg a [proxybeállításokat a Microsoft Monitoring Agent egy olyan parancsfájllal](../log-analytics/log-analytics-windows-agent.md).

A Microsoft Monitoring Agent a Vezérlőpulton Proxybeállítások konfigurálása:

1. Nyissa meg a **vezérlőpultot**.
2. Válassza a **Microsoft Monitoring Agent** lehetőséget.
3. Kattintson a **Proxy Settings** (Proxybeállítások) fülre.
4. Válassza ki **proxykiszolgálót használni** és URL-címét és portszámát, ha szükséges. Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgáló eléréséhez.

  ![Proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: ügynök kapcsolat ellenőrzése

Könnyen ellenőrizheti, hogy az ügynökök megfelelően kommunikálnak.

1. Az ügynök figyelését a kiszolgálón nyissa meg a **Vezérlőpult**.
2. Nyissa meg a **Microsoft-Figyelőügynök**.
3. Kattintson a **Azure Naplóelemzés (OMS)** fülre.
4. Az a **állapot** oszlopban láthatja, hogy az ügynök sikeresen csatlakoztatva az Operations Management Suite szolgáltatással.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: Nyissa meg a tűzfal portjait a figyelési ügynök kiszolgálókon

A TCP protokollt használ, meg kell nyitnia tűzfalportok győződjön meg arról, hogy a felügyeleti ügynökök is kommunikálni.

Hozza létre a beállításkulcsokat a hálózati teljesítményt figyelő által igényelt, valamint az ügynökök számára a TCP-kapcsolatok létrehozása egymás mellett figyelés engedélyezése a Windows tűzfal-szabályok létrehozása a PowerShell-parancsfájl futtatása. A beállításkulcsok hozta létre a parancsfájlt is megadhatja, hogy a naplófájl a hibakeresési naplókat, és a naplók fájl elérési útját. Az ügynök-kommunikációhoz használt TCP-portot is meghatározza. Ezek a kulcsok értékeit automatikusan beállítja a parancsfájl által, így nem kell manuálisan módosítani ezeket a kulcsokat.

Alapértelmezett port 8084 már meg van nyitva. A parancsprogram "portszám" paraméter megadásával egyéni portot is használhat. Azonban ha így tesz, adjon meg ugyanazt a portot, az összes kiszolgálón, amelyiken futtatja a parancsfájlt.

>[!NOTE]
>A "EnableRules" PowerShell-parancsfájl konfigurálása a Windows tűzfal-szabályok csak a kiszolgáló, ahol a parancsfájl futtatása. Ha egy hálózati tűzfalat, meg kell győződnie arról, hogy lehetővé teszi a hálózati teljesítményt figyelő által használt TCP-portot adatforgalmat.
>
>

Az ügynök kiszolgálón nyissa meg egy PowerShell-ablakot rendszergazdai jogosultságokkal. Futtassa a [EnableRules](https://aka.ms/npmpowershellscript) PowerShell parancsfájlt (a korábban letöltött). Ne használjon a paramétereket.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>3. lépés: A hálózati biztonsági csoport szabályainak konfigurálása

A figyelési ügynök kiszolgálók, amelyek az Azure-ban, konfigurálnia kell a hálózati biztonsági csoport (NSG) szabályokat, az TCP-forgalom szintetikus tranzakciók NPM által használt port. Az alapértelmezett port az 8084. Ez lehetővé teszi az Azure virtuális gépen egy helyszíni kommunikálni a figyelési ügynök monitoring Agent szolgáltatásnál.

NSG kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Győződjön meg arról, hogy telepítette az ügynököt (a helyszíni kiszolgáló ügynöke és az Azure-kiszolgálóügynök), és ebben a lépésben a PowerShell-parancsfájlt, a folytatás előtt futtatta.
>
>


## <a name="setupmonitor"></a>4. lépés: NPM ExpressRoute-figyelés konfigurálása.

Miután elvégezte a korábbi szakaszokban, állíthat be figyelését.

1. Nyissa meg a hálózati Teljesítményfigyelő áttekintés csempe navigáljon a **összes erőforrás** lapon, majd kattintson az engedélyezési listán szereplő NPM munkaterület a.

  ![npm munkaterület](.\media\how-to-npm\npm.png)
2. Kattintson a **hálózati Teljesítményfigyelő** áttekintés csempe az irányítópulton elindítani. Az irányítópult tartalmaz egy ExpressRoute lap, amely azt mutatja, hogy az ExpressRoute "nem konfigurált állapotba". Kattintson a **szolgáltatás telepítése** a hálózati Teljesítményfigyelő konfigurációs oldal megnyitásához.

  ![szolgáltatás telepítése](.\media\how-to-npm\npm2.png)
3. A konfiguráció lapon keresse meg a bal oldali panelen található az "ExpressRoute-Társviszony" lapon. Kattintson a **most felderítése**.

  ![felderítés](.\media\how-to-npm\13.png)
4. Felderítési befejezését követően megjelenik szabályok egyedi áramkör és virtuális hálózat nevét. Kezdetben ezek a szabályok le vannak tiltva. A szabályok engedélyezése, majd válassza ki a felügyeleti ügynökök és a küszöbérték.

  ![szabályok](.\media\how-to-npm\14.png)
5. Megkezdéséhez feltöltése az értékek körülbelül 30 – 60 perc várakozás után a szabályok engedélyezése, majd válassza az értékek és a figyelni kívánt ügynökök, nincs és a **ExpressRoute figyelési** csempék elérhető legyen. A figyelési csempék jelenik meg, ha az ExpressRoute-Kapcsolatcsoportok és a kapcsolat erőforrások figyeli NPM.

  ![csempék figyelést](.\media\how-to-npm\15.png)

## <a name="explore"></a>5. lépés: Figyelési csempék megtekintése

### <a name="dashboard"></a>Hálózati Teljesítményfigyelő lap

Az NPM oldal tartalmazó lap az ExpressRoute, ExpressRoute-Kapcsolatcsoportok és társviszony állapotának áttekintése látható.

  ![Irányítópult](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Kapcsolatok listája

Az összes figyelt ExpressRoute-Kapcsolatcsoportok listájának megtekintéséhez kattintson a a **ExpressRoute-Kapcsolatcsoportok** csempére. Válassza ki a kapcsolat, és annak állapotát, csomagvesztés, a sávszélesség-használatot és a késleltetés trend diagramok megtekintése. A diagram olyan interaktív. Kiválaszthatja, hogy egy egyéni időkerete a diagramok ábrázolásához. Az egér területen a diagram nagyítás, és tekintse meg a minden részletre kiterjedő adatpontok húzza.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Adatvesztés, a késleltetés és a teljesítmény alakulását

A sávszélesség, a késés és a veszteség diagram olyan interaktív. Akkor is nagyíthatja diagramokat, minden szakasza egér vezérlők használatával. Megtekintheti a sávszélesség, a késés és a veszteség adatok más időközönkénti kattintva **dátum/idő**, a műveletek gomb bal felső alatt található.

![Trend](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Társviszony listája

Kattintson a **privát Társviszony** csempe az irányítópulton lévő összes virtuális hálózatokhoz való csatlakozás listája magánhálózati társviszony-létesítés alatt megjelenik. Itt kiválaszthatja a virtuális hálózati kapcsolat, és annak állapotát, csomagvesztés, a sávszélesség-használatot és a késleltetés trend diagramok megtekintése.

  ![kör listája](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>Kör topológia

Kör topológia megtekintéséhez kattintson a a **topológia** csempére. Ezzel megnyitná a topológia e nézetében a kiválasztott kör vagy társviszony-létesítés. A topológia ábrája szegmensekhez késést biztosít a hálózaton található, és minden 3 rétegbeli Ugrás a csomópont a diagram képviseli. Az Ugrás kapcsolatos további részletekért kattintson a hop tárja fel.
A helyszíni útválasztók ugrásainak tartalmazza az alábbi csúszka mozgatásával láthatósági szintjét növelhető **szűrők**. A csúszka sávjának áthelyezése jobbra vagy balra, növekszik vagy csökken a topológia Graph ugrások száma. Minden szegmensben közötti késleltetés akkor látható, amely lehetővé teszi a hálózat nagy késleltetésű szegmensek gyorsabb elkülönítési.

![szűrők](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>A kapcsolat részletes topológia e nézetében

Ebben a nézetben látható a VNet kapcsolatokhoz.
![részletes topológia](.\media\how-to-npm\17.png)
