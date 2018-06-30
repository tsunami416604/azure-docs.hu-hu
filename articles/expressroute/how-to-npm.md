---
title: Hálózati Teljesítményfigyelő konfigurálása az Azure ExpressRoute-Kapcsolatcsoportok |} Microsoft Docs
description: Konfigurálja a felhő alapú hálózatfigyelési (NPM) az Azure ExpressRoute-Kapcsolatcsoportok. Ez magában foglalja figyelési ExpressRoute magánhálózati társviszony-létesítés, és a Microsoft társviszony-létesítés alatt.
documentationcenter: na
services: expressroute
author: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2018
ms.author: cherylmc
ms.openlocfilehash: 47f219b7319e4d2bbadf03954f7bd7f6f39da3b4
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128979"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Network Performance Monitor for ExpressRoute konfigurálása

Hálózati teljesítmény figyelése (NPM) egy olyan felhőalapú hálózati felügyeleti megoldás, amely figyeli az Azure-alapú telepítések és (fiókirodákban, stb.) a helyszíni helyek közötti kapcsolatot. NPM Naplóelemzési részét képezi. NPM bővítménye, amely lehetővé teszi a hálózati teljesítmény figyelése a magánhálózati társviszony-létesítés vagy a Microsoft társviszony-létesítés konfigurált ExpressRoute-Kapcsolatcsoportok keresztül ExpressRoute kínál. NPM az ExpressRoute konfigurálásakor hálózati problémák azonosításához, és kiszűri észlelését. A szolgáltatás Azure Government felhő is érhető el.

A következőket teheti:

* Különböző Vnetekhez különböző veszteséget és késéseket figyelése és állíthatók be riasztások

* Összes elérési utat (például a redundáns elérési utak) a hálózat figyelése

* Nehezen replikálni átmeneti és időpontban hálózati probléma elhárításához

* Meghatározásához, hogy egy adott szegmensnek a hálózaton, a teljesítmény csökkenését felelős

* Virtuális hálózatonként átviteli beolvasása (ha van minden egyes virtuális telepített ügynökök)

* Tekintse meg a ExpressRoute rendszerállapot egy előző pont időben

## <a name="workflow"></a>Munkafolyamat

Több kiszolgáló figyelési ügynökök telepítve vannak a helyszíni és az Azure-ban. Az ügynökök kommunikálnak egymással, de ne küldjön adatokat, azok TCP kézfogás csomagok küldése. Az ügynökök közötti kommunikáció lehetővé teszi, hogy az Azure-t azoknak a hálózati topológia és a forgalmat több elérési út.

1. Hozzon létre egy NPM munkaterületet. Ez megegyezik az OMS-munkaterület.
2. Telepítse és konfigurálja a szoftverfrissítési ügynökök: 
    * Figyelési ügynök telepítése a helyszíni kiszolgálók és az Azure virtuális gépek (a magánhálózati társviszony-létesítés).
    * A beállítások a kiszolgálókon figyelési ügynök engedélyezése a figyelőügynökök való kommunikációhoz. (Nyissa meg a tűzfal portjait, stb.)
3. Konfigurálja a hálózati biztonsági csoport (NSG) szabályokat az ügynök figyelését a helyszíni kommunikálni Azure virtuális gépeken telepített figyelési ügynökökhöz.
4. Figyelés beállítása: automatikus felderítése és kezelése a hálózatok láthatók a NPM.

Használata hálózati Teljesítményfigyelő más objektumok, vagy a szolgáltatások figyelésére, és már rendelkezik munkaterület a támogatott régiók egyikéhez sem, ha az 1. lépésben és a 2. lépés kihagyhatja, és a konfiguráció 3. lépés megkezdése.

## <a name="configure"></a>1. lépés: A munkaterület létrehozása

Munkaterület létrehozása, amely rendelkezik az ExpressRoute circuit(s) Vnetek hivatkozásra az előfizetést.

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki az előfizetést, amely rendelkezik a Vnetek társítottak, az ExpressRoute-kapcsolatcsoportot. Ezután keresse meg a szolgáltatások listájában a **piactér** "Hálózati Teljesítményfigyelő". Megnyitásához kattintson a vissza a **hálózati Teljesítményfigyelő** lap.

   >[!NOTE]
   >Hozzon létre egy új munkaterületet, vagy egy meglévő munkaterületen. Ha egy meglévő munkaterület használni kívánt, meg kell győződnie arról, hogy a munkaterület áttelepítése megtörtént-e az új lekérdezési nyelv. [További információ...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](.\media\how-to-npm\3.png)<br><br>
2. A fő alján **hálózati Teljesítményfigyelő** kattintson **létrehozása** megnyitásához **hálózati Teljesítményfigyelő - hozzon létre új megoldás** lap. Kattintson a **OMS-munkaterület - ki kell jelölnie egy munkaterület** munkaterületek lapjának megnyitásához. Kattintson a **+ hozzon létre új munkaterület** munkaterület lapjának megnyitásához.
3. Az a **OMS-munkaterület** lapon jelölje be **hozzon létre új**, majd adja meg a következő beállításokat:

  * OMS-munkaterület - adja meg a munkaterület nevét.
  * Előfizetés – Ha több előfizetéssel, válasszon egyet az új munkaterületet társítani kívánt rendelkezik.
  * Erőforráscsoport - hozzon létre egy erőforráscsoportot, vagy használjon egy meglévőt.
  * Hely - e a hely használatával adja meg az ügynök csatlakozási naplók használt tárfiók helye.
  * A tarifacsomag - válassza ki a tarifacsomagot.
  
    >[!NOTE]
    >Az ExpressRoute-kapcsolatcsoport bárhol a világon lehet. A munkaterület ugyanabban a régióban kell nem tartalmaz.
    >
  
    ![munkaterület](.\media\how-to-npm\4.png)<br><br>
4. Kattintson a **OK** mentéséhez és a beállítások-sablon üzembe helyezése. Amikor érvényesíti a sablont, kattintson a **létrehozása** központi telepítése a munkaterületen.
5. A munkaterület központi telepítése után nyissa meg a **NetworkMonitoring(name)** létrehozott erőforrás. Ellenőrizze a beállításokat, majd kattintson a **megoldás további konfigurálást igényel**.

   ![további beállítások](.\media\how-to-npm\5.png)

## <a name="agents"></a>2. lépés: Telepítse és konfigurálja az ügynökök

### <a name="download"></a>2.1: az ügynök telepítési fájl letöltése

1. Lépjen a **általános beállítások** lapján a **hálózati teljesítmény figyelési konfiguráció** lap az erőforrás. Kattintson az ügynök, amely megfelel a kiszolgáló processzor a a **OMS-ügynökök telepítése** szakaszt, és töltse le a telepítő fájlját.
2. Ezután másolja a **munkaterület azonosítója** és **elsődleges kulcs** a Jegyzettömbbe.
3. Az a **OMS-ügynökök konfigurálása figyelés a TCP protokollt használó** szakaszban, töltse le a Powershell-parancsfájlt. A PowerShell-parancsfájl segítségével nyissa meg a megfelelő tűzfal port a TCP-tranzakciók.

  ![PowerShell-szkript](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: a figyelési ügynök telepítése minden felügyeleti kiszolgálón (az egyes virtuális Hálózatot, amely segítségével nyomon követni kívánt)

Azt javasoljuk, hogy telepítse az ügynököt legalább két redundancia (például a helyszíni Azure Vnetekhez) ExpressRoute-kapcsolat mindkét oldalán. Az ügynököt telepíteni kell a Windows Server (2008 SP1 vagy újabb). Windows asztali operációs rendszer és a Linux operációs rendszert futtató ExpressRoute-Kapcsolatcsoportok megfigyelése nincs támogatva. Az ügynökök telepítéséhez tegye a következőket:
   
  >[!NOTE]
  >Ügynökök leküldött által SCOM (tartalmazza a [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) nem lehet helytől következetesen észleli, ha az Azure üzemeltetése. Azt javasoljuk, hogy nem használja ezeket az ügynököket az Azure Vnetekhez ExpressRoute figyelésére.
  >

1. Futtatás **telepítő** ExpressRoute figyelemmel kísérésére használni kívánt összes kiszolgálón az ügynök telepítéséhez. A figyeléshez használt kiszolgáló lehet egy virtuális Gépet, vagy a helyszínen, és internetkapcsolattal kell rendelkeznie. Azure a figyelni kívánt hálózati szegmenshez legalább egy ügynök-hez, és egy ügynököt telepíteni szeretné.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. Az a **licencfeltételeket** lapon olvassa el a licencfeltételeket, és kattintson a **elfogadom**.
4. Az a **célmappa** lapon módosítani vagy hagyja az alapértelmezett telepítési mappa, és kattintson a **következő**.
5. Az a **ügynök telepítésének beállításai** lap, dönthet úgy, hogy az ügynök csatlakoztatása az Azure Log Analytics vagy az Operations Manager. Vagy a választási lehetőségek üresen hagyhatja, ha az ügynök később konfigurálni szeretné. Miután kiválasztotta a selection(s), kattintson a **következő**.

  * Ha úgy döntött, hogy csatlakozni **Azure Naplóelemzés**, illessze be a **munkaterület azonosítója** és **Munkaterületkulcsot** (elsődleges kulcs), másolja a Jegyzettömbbe az előző szakaszban. Ezután kattintson a **Tovább** gombra.

    ![Azonosítója és kulcsa](.\media\how-to-npm\8.png)
  * Ha úgy döntött, hogy csatlakozni **Operations Manager**, a **felügyeleti csoport konfigurációja** írja be a **felügyeleti csoport neve**, **felügyeleti kiszolgáló** , és a **felügyeleti kiszolgáló portszáma**. Ezután kattintson a **Tovább** gombra.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * Az a **Ügynökműveleti fiók** lapon, vagy válassza ki a **helyi rendszer** fiókot, vagy **tartományi vagy helyi számítógépfiók**. Ezután kattintson a **Tovább** gombra.

    ![Fiók](.\media\how-to-npm\10.png)
6. Az a **telepítésre kész** lapon ellenőrizze a megadott beállításokat, és kattintson a **telepítése**.
7. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.
8. Amikor végzett, a Microsoft Monitoring Agent jelenik meg a Vezérlőpultot. Tekintse át a hiba a konfiguráció, és ellenőrizze, hogy az ügynök csatlakozik-e az Azure Naplóelemzés (OMS). Csatlakozás, az ügynök jeleníti meg a következő üzenet: **a Microsoft Monitoring Agent sikeresen csatlakozott-e a Microsoft Operations Management Suite szolgáltatással**.

9. Ismételje meg ezt az eljárást minden egyes figyelést igénylő virtuális hálózat.

### <a name="proxy"></a>2.3: konfigurálja a proxybeállításokat (nem kötelező)

Ha olyan webproxyt használ az Internet eléréséhez, az alábbi lépések segítségével a Microsoft Monitoring Agent proxy beállításainak konfigurálása. Hajtsa végre ezeket a lépéseket minden olyan kiszolgáló. Ha sok kiszolgálót kell konfigurálnia, akkor érdemes lehet parancsfájl használatával automatizálni a folyamatot. Ha igen, tekintse meg a [proxybeállításokat a Microsoft Monitoring Agent egy olyan parancsfájllal](../log-analytics/log-analytics-windows-agent.md).

A Microsoft Monitoring Agent a Vezérlőpulton Proxybeállítások konfigurálása:

1. Nyissa meg a **vezérlőpultot**.
2. Válassza a **Microsoft Monitoring Agent** lehetőséget.
3. Kattintson a **Proxy Settings** (Proxybeállítások) fülre.
4. Válassza ki **proxykiszolgálót használni** és URL-címét és portszámát, ha szükséges. Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgáló eléréséhez.

  ![proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: ügynök kapcsolat ellenőrzése

Könnyen ellenőrizheti, hogy az ügynökök megfelelően kommunikálnak.

1. Az ügynök figyelését a kiszolgálón nyissa meg a **Vezérlőpult**.
2. Nyissa meg a **Microsoft-Figyelőügynök**.
3. Kattintson a **Azure Naplóelemzés** fülre.
4. Az a **állapot** oszlopban láthatja, hogy az ügynök sikeresen csatlakoztatva Naplóelemzési.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: Nyissa meg a tűzfal portjait a figyelési ügynök kiszolgálókon

A TCP protokollt használ, meg kell nyitnia tűzfalportok győződjön meg arról, hogy a felügyeleti ügynökök is kommunikálni.

A PowerShell-parancsfájl hozza létre a beállításkulcsokat a hálózati teljesítményt figyelő által igényelt is futtathatja. Ezt a parancsfájlt is engedélyezi a figyelési ügynökökhöz TCP-kapcsolatok létrehozása egymás mellett a Windows tűzfal-szabályokat hoz létre. A beállításkulcsok hozta létre a parancsfájl adja meg, hogy a naplófájl a hibakeresési naplókat, és a naplók fájl elérési útját. Az ügynök-kommunikációhoz használt TCP-portot is meghatározza. Ezek a kulcsok értékeit automatikusan a parancsfájl által vannak állítva. Nem kell manuálisan módosítani ezeket a kulcsokat.

Alapértelmezett port 8084 már meg van nyitva. A parancsprogram "portszám" paraméter megadásával egyéni portot is használhat. Azonban ha így tesz, adjon meg ugyanazt a portot, az összes kiszolgálón, amelyiken futtatja a parancsfájlt.

>[!NOTE]
>A "EnableRules" PowerShell-parancsfájl konfigurálása a Windows tűzfal-szabályok csak a kiszolgáló, ahol a parancsfájl futtatása. Ha egy hálózati tűzfalat, meg kell győződnie arról, hogy lehetővé teszi a hálózati teljesítményt figyelő által használt TCP-portot adatforgalmat.
>
>

Az ügynök kiszolgálón nyissa meg egy PowerShell-ablakot rendszergazdai jogosultságokkal. Futtassa a [EnableRules](https://aka.ms/npmpowershellscript) PowerShell parancsfájlt (a korábban letöltött). Ne használjon a paramétereket.

![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>3. lépés: A hálózati biztonsági csoport szabályainak konfigurálása

Az Azure-ban ügynök kiszolgálók figyelése, konfigurálnia kell a hálózati biztonsági csoport (NSG) szabályokat, az TCP-forgalom szintetikus tranzakciók NPM által használt port. Az alapértelmezett port az 8084. Ez lehetővé teszi egy Azure virtuális gépen egy helyszíni kommunikálni a figyelési ügynök monitoring Agent szolgáltatásnál.

NSG kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Győződjön meg arról, hogy telepítette az ügynököt (a helyszíni kiszolgáló ügynöke és az Azure-kiszolgálóügynök), és ebben a lépésben a PowerShell-parancsfájlt, a folytatás előtt futtatta.
>

## <a name="setupmonitor"></a>4. lépés: A társviszony-létesítési kapcsolatok észlelése

1. Nyissa meg a hálózati Teljesítményfigyelő áttekintés csempe navigáljon a **összes erőforrás** lapon, majd kattintson az engedélyezési listán szereplő NPM munkaterület a.

  ![npm munkaterület](.\media\how-to-npm\npm.png)
2. Kattintson a **hálózati Teljesítményfigyelő** áttekintés csempe az irányítópulton elindítani. Az irányítópult tartalmaz egy ExpressRoute lap, amely azt mutatja, hogy az ExpressRoute "nem konfigurált állapotba". Kattintson a **szolgáltatás telepítése** a hálózati Teljesítményfigyelő konfigurációs oldal megnyitásához.

  ![szolgáltatás telepítése](.\media\how-to-npm\npm2.png)
3. A konfiguráció lapon keresse meg a bal oldali panelen található az "ExpressRoute-Társviszony" lapon. Ezután kattintson **felderítése most**.

  ![felderítés](.\media\how-to-npm\13.png)
4. Felderítési befejezését követően a következő elemeket tartalmazó lista jelenik meg:
  * A Microsoft társviszony-létesítési kapcsolatok a az ExpressRoute circuit(s) ehhez az előfizetéshez társított összes.
  * Az előfizetéshez társított összes magánhálózati társviszony-létesítési kapcsolatot a Vnetek-hez.
            
## <a name="configmonitor"></a>5. lépés: A figyelők konfigurálása

Ebben a szakaszban konfigurálni a figyelők. Kövesse a társviszony-létesítés típusú, amely segítségével nyomon követni kívánt: **magánhálózati társviszony-létesítés**, vagy **Microsoft társviszony-létesítés**.

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés

A magánhálózati társviszony-létesítés, ha felderítés befejeződött, megjelenik az szabályainak fog egyedi **áramkör neve** és **VNet neve**. Kezdetben ezek a szabályok le vannak tiltva.

![szabályok](.\media\how-to-npm\14.png)

1. Ellenőrizze a **figyelheti a társviszony** jelölőnégyzetet.
2. Jelölje be a jelölőnégyzetet **engedélyezése állapotának figyelését a társviszony**.
3. Válassza ki a figyelési feltétel. Egyéni küszöbökkel állapotával kapcsolatos események létrehozásához írja be a küszöbértékek állíthatja be. A feltétel értéke a megadott küszöbértéknél, a kijelölt hálózati/alhálózat pár fölé megy, amikor a rendszerállapot esemény jön létre.
4. Kattintson a ON Helyszínen ügynökök **hozzáadása ügynökök** gombra kattintva vegye fel a helyszíni kiszolgálókat, amelyből el kívánja a magánhálózati társviszony-létesítési kapcsolat figyelése. Győződjön meg arról, hogy csak akkor válassza, amely kapcsolódik a Microsoft végpontot, amelyhez a 2. lépéshez szakaszában megadott ügynökök. A helyszíni ügynökök kell tudni érnie a végpont az ExpressRoute-kapcsolatot.
5. A beállítások mentéséhez.
6. Megkezdéséhez feltöltése az értékek körülbelül 30 – 60 perc várakozás után a szabályok engedélyezése, majd válassza az értékek és a figyelni kívánt ügynökök, nincs és a **ExpressRoute figyelési** csempék elérhető legyen.

### <a name="microsoft-peering"></a>Microsoft társviszony-létesítés

A Microsoft társviszony-létesítéshez, kattintson a Microsoft társviszony-létesítési kapcsolatok, amelyet szeretne figyelni, és adja meg a beállításokat.

1. Ellenőrizze a **figyelheti a társviszony** jelölőnégyzetet. 
2. (Választható) A cél Microsoft szolgáltatásvégpont módosíthatja. Alapértelmezés szerint NPM úgy dönt, a Microsoft szolgáltatási végpont céljaként. NPM figyeli a cél-végponthoz ExpressRoute keresztül és a helyszíni kiszolgálók közötti kapcsolatot. 
    * A cél-végponthoz módosításához kattintson a **(Szerkesztés)** hivatkozásra **cél:**, jelöljön ki egy másik Microsoft szolgáltatási cél végpont URL-címek listáját.
      ![Cél szerkesztése](.\media\how-to-npm\edit_target.png)<br>

    * Egy egyéni URL-cím vagy IP-címét is használhatja. Ez a beállítás akkor különösen fontos, ha a Microsoft társviszony-létesítés Azure PaaS szolgáltatásokba, például az Azure Storage, az SQL-adatbázisok és a webhelyeket, amelyeket a érhető el a nyilvános IP-kapcsolatot létesíteni. Ehhez kattintson a hivatkozásra **(egyéni URL-cím vagy IP-címet használja helyette)** alján található az URL-listában, majd írja be a nyilvános végpontot az Azure PaaS szolgáltatás, amely csatlakozik a ExpressRoute Microsoft-társviszony létesítése –.
    ![Egyéni URL-címe](.\media\how-to-npm\custom_url.png)<br>

    * Ha beállításokkal használ, győződjön meg arról, hogy csak a Microsoft szolgáltatási végpont itt van-e kiválasztva. A végpontot kell ExpressRoute kapcsolódik, és elérhető-e a helyszíni-ügynökök által.
3. Jelölje be a jelölőnégyzetet **engedélyezése állapotának figyelését a társviszony**.
4. Válassza ki a figyelési feltétel. Egyéni küszöbökkel állapotával kapcsolatos események létrehozásához írja be a küszöbértékek állíthatja be. A feltétel értéke a megadott küszöbértéknél, a kijelölt hálózati/alhálózat pár fölé megy, amikor a rendszerállapot esemény jön létre.
5. Kattintson a ON Helyszínen ügynökök **hozzáadása ügynökök** gombra kattintva vegye fel a helyszíni kiszolgálókat, amelyből el kívánja a Microsoft társviszony-létesítési kapcsolat figyelése. Győződjön meg arról, hogy csak akkor válassza, amely kapcsolódik a Microsoft végpontok, amely a 2. lépéshez szakaszában megadott ügynökök. A helyszíni ügynökök kell tudni érnie a végpont az ExpressRoute-kapcsolatot.
6. A beállítások mentéséhez.
7. Megkezdéséhez feltöltése az értékek körülbelül 30 – 60 perc várakozás után a szabályok engedélyezése, majd válassza az értékek és a figyelni kívánt ügynökök, nincs és a **ExpressRoute figyelési** csempék elérhető legyen.

## <a name="explore"></a>6. lépés: Figyelési csempék megtekintése

A figyelési csempék jelenik meg, ha az ExpressRoute-Kapcsolatcsoportok és a kapcsolat erőforrások figyeli NPM. A Microsoft Peering csempére a Microsoft Peering kapcsolatok állapotát a részletezéshez kattintson.

![csempék figyelést](.\media\how-to-npm\15.png)

### <a name="dashboard"></a>Hálózati Teljesítményfigyelő lap

Az NPM oldal tartalmazó lap az ExpressRoute, ExpressRoute-Kapcsolatcsoportok és társviszony állapotának áttekintése látható.

![Irányítópult](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Kapcsolatok listája

Megtekintheti az összes figyelt a ExpressRoute-Kapcsolatcsoportok, kattintson a **ExpressRoute-Kapcsolatcsoportok** csempére. Válassza ki a kapcsolat, és annak állapotát, csomagvesztés, a sávszélesség-használatot és a késleltetés trend diagramok megtekintése. A diagram olyan interaktív. Kiválaszthatja, hogy egy egyéni időkerete a diagramok ábrázolásához. Az egér területen a diagram nagyítás, és tekintse meg a minden részletre kiterjedő adatpontok húzza.

![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Adatvesztés, a késés és a teljesítmény alakulását

A sávszélesség, a késés és a veszteség diagram olyan interaktív. Akkor is nagyíthatja diagramokat, minden szakasza egér vezérlők használatával. Megtekintheti a sávszélesség, a késés és a veszteség adatok más időközönkénti kattintva **dátum/idő**, a műveletek gomb bal felső alatt található.

![Trend](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Társviszony listája

Virtuális hálózatok magánhálózati társviszony-létesítés keresztül létesített összes kapcsolat listájának megtekintése, kattintson a **privát Társviszony** csempére az irányítópulton. Itt kiválaszthatja a virtuális hálózati kapcsolat, és annak állapotát, csomagvesztés, a sávszélesség-használatot és a késleltetés trend diagramok megtekintése.

![kör listája](.\media\how-to-npm\peerings.png)

### <a name="nodes"></a>Csomópontok megjelenítése

A helyszíni-csomópontok és a választott társviszony-létesítési ExpressRoute-kapcsolatot az Azure virtuális gépek vagy a Microsoft végpontok közötti összes hivatkozás listájának megtekintése, kattintson a **csomóponti kapcsolat megtekintése**. A hivatkozások állapotát, valamint veszteséget és késéseket, a hozzájuk kapcsolódó trendjét tekintheti meg.

![csomópontok megjelenítése](.\media\how-to-npm\nodes.png)

### <a name="topology"></a>Kör topológia

Kör topológia megtekintéséhez kattintson a **topológia** csempére. Ezzel megnyitná a topológia e nézetében a kiválasztott kör vagy társviszony-létesítés. A topológia ábrája szegmensekhez késést biztosít a hálózaton található. Minden egyes réteg 3 Ugrás a csomópont a diagram jelképezi. Az Ugrás kapcsolatos további részletekért kattintson a hop tárja fel.

A helyszíni útválasztók ugrásainak tartalmazza az alábbi csúszka mozgatásával láthatósági szintjét növelhető **szűrők**. A csúszka sávjának áthelyezése jobbra vagy balra, növekszik vagy csökken a topológia Graph ugrások száma. Minden szegmensben közötti késleltetés akkor látható, amely lehetővé teszi a hálózat nagy késleltetésű szegmensek gyorsabb elkülönítési.

![szűrők](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>A kapcsolat részletes topológia e nézetében

Ebben a nézetben látható a VNet kapcsolatokhoz.
![részletes topológia](.\media\how-to-npm\17.png)