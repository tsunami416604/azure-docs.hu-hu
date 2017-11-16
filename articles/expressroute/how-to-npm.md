---
title: "Hálózati Teljesítményfigyelő konfigurálása az Azure ExpressRoute-Kapcsolatcsoportok (előzetes verzió) |} Microsoft Docs"
description: "NPM konfigurálása Azure ExpressRoute-Kapcsolatcsoportok. (Előzetes verzió)"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: cherylmc
ms.openlocfilehash: 3ab8029d035c3ba88ddb8a112e27f9054f7c203c
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="configure-network-performance-monitor-for-expressroute-preview"></a>Konfigurálja a hálózati Teljesítményfigyelő az ExpressRoute (előzetes verzió)

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
* USA keleti régiója 
* Délkelet-Ázsia 

## <a name="workflow"></a>Munkafolyamat

Több kiszolgáló figyelési ügynökök telepítve vannak a helyszíni és az Azure-ban. Az ügynökök kommunikálnak egymással, de ne küldjön adatokat, azok TCP kézfogás csomagok küldése. Az ügynökök közötti kommunikáció lehetővé teszi, hogy az Azure-t azoknak a hálózati topológia és a forgalmat több elérési út.

1. Hozzon létre egy NPM munkaterület közül a [támogató régiók](#regions).
2. Telepítse és konfigurálja a szoftverfrissítési ügynökök: 
    * A telepítés figyelését a helyszíni kiszolgálók és az Azure virtuális gépeken futó ügynököket.
    * A beállítások a kiszolgálókon figyelési ügynök engedélyezése a figyelőügynökök való kommunikációhoz. (Nyissa meg a tűzfal portjait, stb.)
3. Konfigurálja a hálózati biztonsági csoport (NSG) szabályokat az ügynök figyelését a helyszíni kommunikálni Azure virtuális gépeken telepített figyelési ügynökökhöz.
4. Engedélyezett az NPM-munkaterület kérelmet.
5. Figyelés beállítása: automatikus felderítése és kezelése a hálózatok láthatók a NPM.

Használata hálózati Teljesítményfigyelő más objektumok, vagy a szolgáltatások figyelésére, és már rendelkezik munkaterület a támogatott régiók egyikéhez sem, ha az 1. lépésben és a 2. lépés kihagyhatja, és a konfiguráció 3. lépés megkezdése.

## <a name="configure"></a>1. lépés: A munkaterület létrehozása

1. Az a [Azure-portálon](https://portal.azure.com), a szolgáltatások listájában keresse a **piactér** "Hálózati Teljesítményfigyelő". Megnyitásához kattintson a vissza a **hálózati Teljesítményfigyelő** lap.

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
6. A a **hálózati Teljesítményfigyelő Üdvözöljük** lapon jelölje be **szintetikus tranzakciók használata TCP**, kattintson a **Submit**. A TCP tranzakciókat csak történő elérhetővé tételéhez és megszünteti a kapcsolatot használják. Nem küld adatokat a TCP-kapcsolatokon keresztül.

  ![Szintetikus tranzakciók TCP](.\media\how-to-npm\6.png)

## <a name="agents"></a>2. lépés: Telepítse és konfigurálja az ügynökök

### <a name="download"></a>2.1: az ügynök telepítési fájl letöltése

1. Az a **hálózati teljesítmény figyelési konfiguráció - TCP-telepítőből** az erőforrás a a **OMS-ügynökök telepítése** területen kattintson az ügynök, amely megfelel a kiszolgáló processzor- és letöltése a a telepítő fájl.

  >[!NOTE]
  >A Linux-ügynök jelenleg nem támogatott az ExpressRoute figyelését.
  >
  >
2. Ezután másolja a **munkaterület azonosítója** és **elsődleges kulcs** a Jegyzettömbbe.
3. Az a **ügynökök konfigurálása** szakaszban, töltse le a Powershell-parancsfájlt. A PowerShell-parancsfájl segítségével nyissa meg a megfelelő tűzfal port a TCP-tranzakciók.

  ![PowerShell-szkript](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: a figyelési ügynök telepítése minden felügyeleti kiszolgálón

1. Futtatás **telepítő** ExpressRoute figyelemmel kísérésére használni kívánt összes kiszolgálón az ügynök telepítéséhez. A figyeléshez használt kiszolgáló lehet egy virtuális Gépet, vagy a helyszíni és internetkapcsolattal kell rendelkeznie. Azure a figyelni kívánt hálózati szegmenshez legalább egy ügynök-hez, és egy ügynököt telepíteni szeretné.
2. Az a **üdvözlő** kattintson **következő**.
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
7. Az a **konfigurálása sikeresen befejeződött** kattintson **Befejezés**.
8. Amikor végzett, a Microsoft Monitoring Agent jelenik meg a Vezérlőpultot. Tekintse át a hiba a konfiguráció, és ellenőrizze, hogy az ügynök csatlakozik-e az Operational Insights (OMS). Csatlakoztatva az OMS-be, amikor az ügynök jeleníti meg a következő üzenet: **a Microsoft Monitoring Agent sikeresen csatlakozott-e a Microsoft Operations Management Suite szolgáltatással**.

### <a name="proxy"></a>2.3: konfigurálja a proxybeállításokat (nem kötelező)

Ha olyan webproxyt használ az Internet eléréséhez, az alábbi lépések segítségével a Microsoft Monitoring Agent proxy beállításainak konfigurálása. Hajtsa végre ezeket a lépéseket minden olyan kiszolgáló. Ha sok kiszolgálót kell konfigurálnia, akkor érdemes lehet parancsfájl használatával automatizálni a folyamatot. Ha igen, tekintse meg a [proxybeállításokat a Microsoft Monitoring Agent egy olyan parancsfájllal](../log-analytics/log-analytics-windows-agents.md#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

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

Az ügynök kiszolgálón nyissa meg egy PowerShell-ablakot rendszergazdai jogosultságokkal. Futtassa a [EnableRules](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) PowerShell parancsfájlt (a korábban letöltött). Ne használjon a paramétereket.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>3. lépés: A hálózati biztonsági csoport szabályainak konfigurálása

A figyelési ügynök kiszolgálók, amelyek az Azure-ban, konfigurálnia kell a hálózati biztonsági csoport (NSG) szabályokat, az TCP-forgalom szintetikus tranzakciók NPM által használt port. Az alapértelmezett port az 8084. Ez lehetővé teszi az Azure virtuális gépen egy helyszíni kommunikálni a figyelési ügynök monitoring Agent szolgáltatásnál.

NSG kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

## <a name="whitelist"></a>4. lépés: Kérelem engedélyezett munkaterület

>[!NOTE]
>Győződjön meg arról, hogy telepítette az ügynököt (a helyszíni kiszolgáló ügynöke és az Azure-kiszolgálóügynök), és ebben a lépésben a PowerShell-parancsfájlt, a folytatás előtt futtatta.
>
>

Előtt ExpressRoute figyelési az szolgáltatásával NPM, kérnie kell rendelkeznie a munkaterület szerepel az engedélyezési listán. [Kattintson ide a lapra, és töltse ki az űrlap](https://aka.ms/npmcohort). (Mutató: érdemes lehet a hivatkozás megnyitásához az egy új ablakot vagy lapot). Az engedélyezett végre üzleti napi egy vagy több. Ha a engedélyezése befejeződött, kapni fog egy e-mailt.

## <a name="setupmonitor"></a>5. lépés: NPM ExpressRoute-figyelés konfigurálása.

>[!WARNING]
>Ne folytassa tovább, amíg a munkaterület már szerepel az engedélyezési listán, és megjelenik egy visszaigazoló e-mailben.
>
>

Miután befejezte a korábbi szakaszokban, és győződjön meg arról, hogy ki lett-e az engedélyezési listán szereplő, állíthat be figyelését.

1. Nyissa meg a hálózati Teljesítményfigyelő áttekintés csempe navigáljon a **összes erőforrás** lapon, majd kattintson az engedélyezési listán szereplő NPM munkaterület a.

  ![npm munkaterület](.\media\how-to-npm\npm.png)
2. Kattintson a **hálózati Teljesítményfigyelő** áttekintés csempe az irányítópulton elindítani. Az irányítópult tartalmaz egy ExpressRoute lap, amely azt mutatja, hogy az ExpressRoute "nem konfigurált állapotba". Kattintson a **szolgáltatás telepítése** a hálózati Teljesítményfigyelő konfigurációs oldal megnyitásához.

  ![szolgáltatás telepítése](.\media\how-to-npm\npm2.png)
3. A konfiguráció lapon keresse meg a bal oldali panelen található az "ExpressRoute-Társviszony" lapon. Kattintson a **most felderítése**.

  ![Felderítése](.\media\how-to-npm\13.png)
4. Felderítési befejezését követően megjelenik szabályok egyedi áramkör és virtuális hálózat nevét. Kezdetben ezek a szabályok le vannak tiltva. A szabályok engedélyezése, majd válassza ki a felügyeleti ügynökök és a küszöbérték.

  ![szabályok](.\media\how-to-npm\14.png)
5. Megkezdéséhez feltöltése az értékek körülbelül 30 – 60 perc várakozás után a szabályok engedélyezése, majd válassza az értékek és a figyelni kívánt ügynökök, nincs és a **ExpressRoute figyelési** csempék elérhető legyen. A figyelési csempék jelenik meg, ha az ExpressRoute-Kapcsolatcsoportok és a kapcsolat erőforrások figyeli NPM.

  ![csempék figyelést](.\media\how-to-npm\15.png)

## <a name="explore"></a>6. lépés: Figyelési csempék megtekintése

### <a name="dashboard"></a>Hálózati Teljesítményfigyelő lap

Az NPM oldal tartalmazó lap az ExpressRoute, ExpressRoute-Kapcsolatcsoportok és társviszony állapotának áttekintése látható.

  ![Irányítópult](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Kapcsolatok listája

Az összes figyelt ExpressRoute-Kapcsolatcsoportok listájának megtekintéséhez kattintson a a **ExpressRoute-Kapcsolatcsoportok** csempére. Válassza ki a kapcsolat, és annak állapotát, csomagvesztés, a sávszélesség-használatot és a késleltetés trend diagramok megtekintése. A diagram olyan interaktív. Kiválaszthatja, hogy egy egyéni időkerete a diagramok ábrázolásához. Az egér területen a diagram nagyítás, és tekintse meg a minden részletre kiterjedő adatpontok húzza.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Adatvesztés, a késés és a teljesítmény alakulását

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
