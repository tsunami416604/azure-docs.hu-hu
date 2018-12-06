---
title: A Network Performance Monitor konfigurálása az Azure ExpressRoute-Kapcsolatcsoportok |} A Microsoft Docs
description: Konfigurálja a felhő alapú hálózatfigyelési (NPM) az Azure ExpressRoute-Kapcsolatcsoportok számára. Tartalmazza a figyelés ExpressRoute privát társviszony-létesítés és Microsoft társviszony-létesítésen keresztül.
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
ms.openlocfilehash: 93bafb739c35b81796186dc845f7933a82e1675b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968118"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Network Performance Monitor for ExpressRoute konfigurálása

Network Performance monitort (NPM) egy olyan felhő alapú hálózati figyelő megoldás, amely figyeli az Azure-alapú telepítések és (fiókirodákban, stb.) a helyszíni helyek közötti kapcsolatot. Npm-et a Log Analytics részét képezi. Az NPM kibővíti az expressroute-hoz, amely lehetővé teszi a hálózati teljesítmény nyomon követése az ExpressRoute-Kapcsolatcsoportok privát társviszony-létesítés, vagy a Microsoft társviszony-létesítés használatára konfigurált keresztül. Hálózati problémák azonosítása és kiküszöbölése érdekében észlelését az npm-et az expressroute-hoz való konfigurálásakor. Ez a szolgáltatás az Azure Government felhőben is érhető el.

A következőket teheti:

* Figyelheti a veszteséget és késéseket különböző virtuális hálózatok között, és riasztásokat állíthat be

* A hálózaton (beleértve a redundáns elérési utak) összes útvonalának figyelése

* Átmeneti és időponthoz – hálózati problémák, amelyeket nehéz replikálni

* Segít meghatározni, hogy a hálózat teljesítménycsökkenésért felelős konkrét szegmensét

* Virtuális hálózati sebességet beolvasása (Ha rendelkezik az egyes virtuális hálózatok telepített ügynökök)

* Az ExpressRoute rendszerállapot egy adott múltbeli időpontból megjelenő idő

## <a name="workflow"></a>A munkafolyamat

Monitorozási ügynökök több kiszolgálón vannak telepítve a helyszínen és az Azure-ban. Az ügynökök kommunikálnak egymással, de ne küldjön adatokat, azokat a TCP kézfogás csomagok küldése. Az ügynökök közötti kommunikáció lehetővé teszi, hogy az Azure-bA a hálózati topológia és az elérési út a forgalom is igénybe vehet.

1. Hozzon létre egy NPM-munkaterületet. Ez a Log Analytics-munkaterület megegyezik.
2. Telepítse és konfigurálja a szoftverfrissítési ügynökök: 
    * Monitorozási ügynökök telepítése a helyszíni kiszolgálók és az Azure virtuális gépek (a privát társviszony-létesítés).
    * Beállítások konfigurálása a monitorozási ügynök kiszolgálókon, hogy a monitorozási ügynökök való kommunikációhoz. (Nyissa meg a tűzfal portjait, stb.)
3. Konfigurálja a hálózati biztonsági csoport (NSG) szabályai a monitorozási ügynök kommunikáljon a helyszíni Azure virtuális gépeken telepített monitorozási ügynökök.
4. Figyelés beállítása: automatikus felderítése és kezelése, mely hálózatokat láthatók az npm-et.

Ha már használja a Network Performance Monitor más objektumok vagy szolgáltatásokat, és a támogatott régiók egyikében már rendelkezik munkaterület, 1. lépést és a 2. lépést kihagyhatja, és 3. lépés-a konfigurálás megkezdése.

## <a name="configure"></a>1. lépés: Hozzon létre egy munkaterületet

Hozzon létre egy munkaterületet, amely rendelkezik a virtuális hálózatokat az ExpressRoute-kapcsolatcsoport az előfizetésben.

1. Az a [az Azure portal](https://portal.azure.com), válassza ki az előfizetést, amely rendelkezik a virtuális hálózatok társviszonyba az ExpressRoute-kapcsolatcsoportot. Ezután keresse meg a szolgáltatások listájában a **Marketplace** a "Network Performance Monitor". Megnyitásához kattintson a vissza a **Network Performance Monitor** lapot.

   >[!NOTE]
   >Hozzon létre egy új munkaterületet, vagy egy meglévő munkaterületet használja. Ha szeretne egy meglévő munkaterületet használja, győződjön meg arról, hogy a munkaterület migrálták az új lekérdezési nyelvre. [További információ...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. A fő alján **Network Performance Monitor** lap, kattintson **létrehozás** megnyitásához **Network Performance Monitor – új megoldás létrehozása** lap. Kattintson a **Log Analytics-munkaterület - válassza ki a munkaterület** a munkaterületek lap megnyitásához. Kattintson a **+ létrehozás új munkaterület** nyissa meg a munkaterületen.
3. Az a **Log Analytics-munkaterület** lapon jelölje be **hozzon létre új**, majd adja meg a következő beállításokat:

  * Log Analytics-munkaterület - adja meg a munkaterület nevét.
  * Előfizetés – Ha több előfizetést, válasszon egyet az új munkaterületet társítani szeretné.
  * Erőforráscsoport – hozzon létre egy erőforráscsoportot, vagy használjon egy meglévőt.
  * Adja meg a helyet, az ügynök kapcsolatot naplókhoz használt tárfiók helye – Ez a hely szolgál.
  * Tarifacsomag - válassza ki a tarifacsomagot.
  
    >[!NOTE]
    >Az ExpressRoute-kapcsolatcsoport a világ bárhol lehet. Nem kell ugyanabban a régióban, ahol a munkaterület lehet.
    >
  
    ![munkaterület](./media/how-to-npm/4.png)<br><br>
4. Kattintson a **OK** mentéséhez és a beállítások sablon üzembe helyezéséhez. Ha a sablon érvényesítése, kattintson **létrehozása** való üzembe helyezése a munkaterületen.
5. A munkaterület üzembe helyezését követően navigáljon a **NetworkMonitoring(name)** létrehozott erőforrást. Ellenőrizze a beállításokat, majd kattintson a **megoldás további konfigurálást igényel**.

   ![további beállítások](./media/how-to-npm/5.png)

## <a name="agents"></a>2. lépés: Telepítse és konfigurálja az ügynökök

### <a name="download"></a>2.1-es verzióját: az ügynök telepítési fájl letöltése

1. Nyissa meg a **közös beállítások** lapján a **hálózati Teljesítményfigyelő beállítása** az erőforrás oldalán. Kattintson arra az ügynökre, amely megfelel a kiszolgáló processzor, a **Log Analytics-ügynökök telepítése** szakaszt, és töltse le a telepítőfájlt.
2. Ezután másolja a **munkaterület-Azonosítót** és **elsődleges kulcs** a Jegyzettömbbe.
3. Az a **Log Analytics ügynökök konfigurálása figyelésre TCP protokoll használatával** szakaszban, a Powershell-parancsprogram letöltése. A PowerShell-parancsfájl segítségével nyissa meg a megfelelő tűzfal-, a TCP-tranzakciók.

  ![PowerShell-szkript](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2-es: monitorozási ügynök telepítése minden felügyeleti kiszolgálón (az egyes virtuális hálózat, amely a figyelni kívánt)

Azt javasoljuk, hogy a redundancia biztosítása érdekében (Ha például a helyszínen, az Azure virtuális hálózatok) az ExpressRoute-kapcsolat mindkét oldalán legalább két ügynökök telepítése. Az ügynököt telepíteni kell a Windows Server (2008 SP1 vagy újabb). Windows asztali operációs rendszer és a Linux operációs rendszer használata az ExpressRoute-Kapcsolatcsoportok figyelés nem támogatott. Használja az alábbi lépéseket az ügynökök telepítéséhez:
   
  >[!NOTE]
  >Ügynökök SCOM által leküldött (tartalmazza a [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) nem lehet helyükre következetesen észleli, ha azokat az Azure-ban. Azt javasoljuk, hogy nem használja ezeket az ügynököket az Azure virtuális hálózat ExpressRoute figyelése.
  >

1. Futtatás **telepítő** az ügynök telepítése minden egyes ExpressRoute használni kívánt kiszolgálót. A figyeléshez használt kiszolgáló lehet egy virtuális Gépet, vagy a helyszíni, és az Internet-hozzáféréssel kell rendelkeznie. Legalább egy ügynökkel a helyszínen és a egy ügynök telepítése, amely az Azure-ban figyelni kívánt hálózati szegmenshez kell.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. Az a **licencfeltételek** lapon olvassa el a licencfeltételeket, és kattintson a **elfogadom**.
4. Az a **célmappa** lapon módosíthatja vagy megtartani az alapértelmezett telepítési mappát, és kattintson a **tovább**.
5. Az a **ügynök telepítésének beállításai** lapon lehet váltani, az ügynök csatlakoztatása az Azure Log Analytics vagy az Operations Manager. Vagy üresen hagyhatja, a lehetőségek, ha az ügynök később konfigurálni szeretné. Miután kiválasztotta a selection(s), kattintson a **tovább**.

  * Ha úgy döntött, hogy csatlakozni **Azure Log Analytics**, illessze be a **munkaterület-Azonosítót** és **Munkaterületkulcsot** Jegyzettömbbe az előző szakaszban kimásolt (elsődleges kulcs). Ezután kattintson a **Tovább** gombra.

    ![Azonosítója és kulcsa](./media/how-to-npm/8.png)
  * Ha úgy döntött, hogy csatlakozni **az Operations Manager**, az a **felügyeleticsoport-konfigurációjának** írja be a **felügyeleti csoport neve**, **felügyeleti kiszolgáló** , és a **felügyeleti kiszolgáló portja**. Ezután kattintson a **Tovább** gombra.

    ![Operations Manager](./media/how-to-npm/9.png)
  * Az a **Ügynökműveleti fiók** lapon, válassza a **helyi rendszer** fiókot, vagy **tartományi vagy helyi számítógépfiók**. Ezután kattintson a **Tovább** gombra.

    ![Fiók](./media/how-to-npm/10.png)
6. Az a **telepítésre kész** lapon ellenőrizze a beállításokat, és kattintson a **telepítése**.
7. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.
8. Amikor végzett, a Microsoft Monitoring Agentet a Vezérlőpulton jelenik meg. Áttekintheti a konfigurációt, és ellenőrizze, hogy az ügynök csatlakozik-e az Azure Log Analyticshez. Ha csatlakozik, az ügynök megjeleníti a következő üzenetet: **a Microsoft Monitoring Agent sikeresen csatlakozott a Microsoft Operations Management Suite szolgáltatásban**.

9. Ismételje meg ezt az eljárást minden egyes virtuális hálózat, amely figyelni kell.

### <a name="proxy"></a>2.3: konfigurálja a proxybeállításokat (nem kötelező)

Használatakor egy webalkalmazás-proxyn keresztül csatlakozik az internetre, a következő lépések használatával konfigurálja a proxybeállításokat a Microsoft Monitoring Agent számára. Hajtsa végre ezeket a lépéseket minden olyan kiszolgáló esetén. Ha sok kiszolgálót kell konfigurálnia, akkor érdemes lehet parancsfájl használatával automatizálni a folyamatot. Ha igen, tekintse meg a [proxybeállításokat a Microsoft Monitoring Agent egy olyan parancsfájllal](../log-analytics/log-analytics-windows-agent.md).

A Microsoft Monitoring Agentet a Vezérlőpulton proxybeállításainak konfigurálása:

1. Nyissa meg a **vezérlőpultot**.
2. Válassza a **Microsoft Monitoring Agent** lehetőséget.
3. Kattintson a **Proxy Settings** (Proxybeállítások) fülre.
4. Válassza ki **proxykiszolgálóval** , és írja be az URL-címet és portszámot, amennyiben szükséges. Ha a proxykiszolgáló hitelesítést igényel, írja be a felhasználónevet és jelszót a proxykiszolgáló eléréséhez.

  ![Proxy](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4: Ellenőrizze az ügynök kapcsolatot

Egyszerűen ellenőrizheti az ügynökök kommunikálnak-e.

1. A figyelőügynök kiszolgálóra, nyissa meg a **Vezérlőpult**.
2. Nyissa meg a **a Microsoft Monitoring Agent**.
3. Kattintson a **Azure Log Analytics** fülre.
4. Az a **állapot** oszlopban megtekintheti az, hogy az ügynök sikeresen csatlakoztatva a Log Analytics szolgáltatásba.

  ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5: Nyissa meg a tűzfal portjait a monitorozási ügynök kiszolgálókon

A TCP protokollt használja, meg kell nyitnia tűzfalportok győződjön meg arról, hogy a monitorozási ügynökök közötti kommunikációhoz.

Hozza létre a beállításkulcsokat, amelyek szükségesek a Network Performance Monitor egy PowerShell-szkriptet is futtathatja. Ez a szkript létrehoz a Windows tűzfal-szabályok, hogy az egyes TCP-kapcsolatok létrehozása a figyelőügynökök is. A beállításkulcsok, a szkript által létrehozott adja meg, hogy a naplófájl a hibakeresési naplók, és a naplók fájl elérési útját. Az ügynök kommunikációhoz használt TCP-portot is meghatározza. Ezek a kulcsok értékeit a parancsfájl által automatikusan beállítva. Nem kell manuálisan módosítani ezeket a kulcsokat.

Alapértelmezett port: 8084 nyitja meg. Azáltal, hogy a paraméter "portszám' a parancsfájl egy egyéni portot is használhatja. Ha így tesz, a parancsprogramot futtató összes kiszolgáló ugyanazt a portot kell adnia.

>[!NOTE]
>A "EnableRules" PowerShell-parancsprogram konfigurálja a Windows tűzfal-szabályok csak a kiszolgálón, a szkript futása. Ha a hálózati tűzfal, győződjön meg arról, hogy lehetővé teszi a Network Performance Monitor által használt TCP-port felé irányuló forgalom.
>
>

Az ügynök kiszolgálón nyissa meg egy PowerShell-ablakot rendszergazdai jogosultságokkal. Futtassa a [EnableRules](https://aka.ms/npmpowershellscript) (amely a korábban letöltött) PowerShell-parancsfájlt. Ne használja a paramétereket.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>3. lépés: Konfigurálja a hálózati biztonsági csoport szabályai

Az Azure-ban ügynök kiszolgálók figyelése, konfigurálnia kell a hálózati biztonsági csoport (NSG) szabályai egy portot a szintetikus tranzakciókhoz NPM által használt TCP-forgalom engedélyezéséhez. Az alapértelmezett port: 8084. Ez lehetővé teszi, hogy a monitorozási ügynök kommunikáljon a helyszíni-beli virtuális gépen telepített ügynök figyelése.

NSG-t kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Győződjön meg arról, hogy telepítette az ügynököket (a helyszíni kiszolgáló ügynök és az Azure server agent), és az ebben a lépésben a PowerShell-parancsfájlt, a folytatás előtt futtatnia kell.
>

## <a name="setupmonitor"></a>4. lépés: A társviszony-kapcsolatok felderítése

1. A Network Performance Monitor áttekintése csempe a keresse meg a **összes erőforrás** lapon, majd kattintson az NPM-munkaterület az engedélyezési listához hozzáadni kívánt.

  ![az npm-munkaterület](./media/how-to-npm/npm.png)
2. Kattintson a **Network Performance Monitor** áttekintés csempe az irányítópulton való vizualizációjára. Az irányítópult tartalmaz egy ExpressRoute-oldal, amely azt mutatja, hogy az ExpressRoute "nem konfigurált állapotba". Kattintson a **funkció telepítése** a Network Performance Monitor konfigurációs lap megnyitásához.

  ![funkció telepítése](./media/how-to-npm/npm2.png)
3. A konfigurációs lapon keresse meg az ExpressRoute-Társviszonyok lapon, a bal oldali panelen található. Ezután kattintson **felderítése most**.

  ![felderítés](./media/how-to-npm/13.png)
4. Felderítés befejeződése után a következő elemek egy lista jelenik meg:
  * Az ehhez az előfizetéshez társított ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítési kapcsolások mindegyikét.
  * Az előfizetéshez tartozó összes a privát társviszony-kapcsolatokat, amelyek a virtuális hálózatokhoz csatlakoznak.
            
## <a name="configmonitor"></a>5. lépés: Konfigurálja a figyelők

Ebben a szakaszban konfigurálni a figyelők. Kövesse a lépéseket a társviszony-létesítés típusa, amely a figyelni kívánt: **magánhálózati társviszony-létesítés**, vagy **Microsoft társviszony-létesítés**.

### <a name="private-peering"></a>Magánhálózati társviszony-létesítés

Magánhálózati társviszony-létesítés, a felderítés befejeződése után látni fog vonatkozó szabályok egyedi **kapcsolatcsoport neve** és **virtuális hálózat neve**. Kezdetben ezek a szabályok le vannak tiltva.

![szabályok](./media/how-to-npm/14.png)

1. Ellenőrizze a **társviszony figyelése** jelölőnégyzetet.
2. Jelölje be a **egészségügyi figyelés engedélyezése a társviszony**.
3. Válassza ki a figyelési feltétel. A Szolgáltatásállapot-események létrehozásához írja be a küszöbértékek egyedi küszöbértékeket állíthatja be. Minden alkalommal, amikor a feltétel értéke a megadott küszöbértéknél, a kiválasztott hálózat/alhálózat pár túllépik, állapottal kapcsolatos esemény jön létre.
4. Kattintson a helyszíni ügynökök **ügynökök hozzáadása** gombra kattintva adhat hozzá a helyszíni kiszolgálók, ahonnan a privát társviszony-létesítési kapcsolat figyeléséhez. Ellenőrizze, hogy csak akkor válassza, amely a Microsoft végpontot, amelyhez a 2. lépés a szakaszban megadott kapcsolati ügynökök. A helyi ügynökök elérjék a végpontot, az ExpressRoute-kapcsolat használatával képesnek kell lennie.
5. A beállítások mentéséhez.
6. A szabályok engedélyezése, és kiválaszthatja a kívánt értékeket, és a figyelni kívánt ügynököket, után várjon körülbelül 30 – 60 perc feltöltése megkezdéséhez az értékek nincs és a **az ExpressRoute monitorozása** elérhető csempék.

### <a name="microsoft-peering"></a>Microsoft társviszony-létesítés

A Microsoft társviszony-létesítéshez, kattintson a figyelni kívánt Microsoft társviszony-létesítési kapcsolat, és adja meg a beállításokat.

1. Ellenőrizze a **társviszony figyelése** jelölőnégyzetet. 
2. (Nem kötelező) Módosíthatja a cél Microsoft szolgáltatásvégpontot. Alapértelmezés szerint az NPM és a cél Microsoft szolgáltatásvégpont választja ki. Az NPM figyeli a kapcsolat a helyszíni kiszolgálók és a céloldali végpont expressroute-on keresztül. 
    * A céloldali végpont módosításához kattintson a **(Szerkesztés)** mellett kapcsolni **cél:**, és válassza ki egy másik Microsoft szolgáltatási cél végpont URL-címek listájából.
      ![Cél szerkesztése](./media/how-to-npm/edit_target.png)<br>

    * Egy egyéni URL-cím vagy IP-címet is használhatja. Ez a beállítás akkor különösen fontos, ha a Microsoft társviszony-létesítést úgy, hogy egy kapcsolatot az Azure PaaS-szolgáltatások, például az Azure Storage, SQL Database-adatbázisok és a nyilvános IP-címet felkínált webhelyek. Ehhez kattintson a hivatkozásra **(egyéni URL-cím vagy IP-címet használja helyette)** alján, az URL-CÍMEK listája, majd adja meg az Azure PaaS-szolgáltatás, amely csatlakozik az ExpressRoute a Microsoft társviszony-létesítésen keresztül a nyilvános végponthoz.
    ![Egyéni URL-cím](./media/how-to-npm/custom_url.png)<br>

    * Ha ezeket a választható beállításokat használja, ügyeljen arra, hogy csak a Microsoft-szolgáltatásvégpontot itt van-e kiválasztva. A végpont kell lennie a helyi ügynökök által az expressroute-hoz csatlakoztatott és a érhető el.
3. Jelölje be a **egészségügyi figyelés engedélyezése a társviszony**.
4. Válassza ki a figyelési feltétel. A Szolgáltatásállapot-események létrehozásához írja be a küszöbértékek egyedi küszöbértékeket állíthatja be. Minden alkalommal, amikor a feltétel értéke a megadott küszöbértéknél, a kiválasztott hálózat/alhálózat pár túllépik, állapottal kapcsolatos esemény jön létre.
5. Kattintson a helyszíni ügynökök **ügynökök hozzáadása** gombra kattintva adhat hozzá a helyszíni kiszolgálók, ahonnan a Microsoft társviszony-létesítési kapcsolat figyeléséhez. Ellenőrizze, hogy csak akkor válassza a 2. lépés a szakaszban megadott Microsoft-szolgáltatás végpontokra irányuló rendelkező ügynököknek. A helyi ügynökök elérjék a végpontot, az ExpressRoute-kapcsolat használatával képesnek kell lennie.
6. A beállítások mentéséhez.
7. A szabályok engedélyezése, és kiválaszthatja a kívánt értékeket, és a figyelni kívánt ügynököket, után várjon körülbelül 30 – 60 perc feltöltése megkezdéséhez az értékek nincs és a **az ExpressRoute monitorozása** elérhető csempék.

## <a name="explore"></a>6. lépés: Figyelési csempék megjelenítése

Ha már látja a figyelési csempék, az ExpressRoute-Kapcsolatcsoportok és kapcsolati erőforrás figyeli, az NPM. A Microsoft-Peering kapcsolatok állapotának részletes keresztül a Microsoft Peering csempére kattinthat.

![csempék figyelése](./media/how-to-npm/15.png)

### <a name="dashboard"></a>Hálózati Teljesítményfigyelő lap

Az NPM-lapot tartalmazó lap az expressroute-hoz, amely az ExpressRoute-Kapcsolatcsoportok és a társviszony állapotának áttekintése látható.

![Irányítópult](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>Kapcsolatcsoportok listája

Megtekintheti az összes figyelt a ExpressRoute-Kapcsolatcsoportok, kattintson a **ExpressRoute-Kapcsolatcsoportok** csempére. Válassza ki egy kapcsolatcsoportot, és megtekintheti annak állapotát, trenddiagramok csomagvesztés, a sávszélesség kihasználtságát és a késés. A diagramok használata interaktív. Kiválaszthat egy küldik az ábrázolást a diagramok egyéni időtartományából. A diagram a nagyításra, és tekintse meg a részletes adatokat is húzza az egeret területen.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>Teljesítmény, és az adatveszteség, a késés trendje

A sávszélesség, a késés és a veszteség diagramok használata interaktív. Nagyíthatja ezekbe a diagramokba szakasz egér-vezérlők használatával. Megtekintheti a sávszélesség, a késés és a veszteség adatok más időközönként a kattintva **dátum/idő**, a bal felső sarokban a műveletek gomb alatt található.

![Trend](./media/how-to-npm/16.png)

### <a name="peerings"></a>Társviszony-Létesítéseket listája

Kattintson a virtuális hálózatok privát társviszony-létesítésen keresztül irányuló összes kapcsolatot listájának megtekintése a **privát társviszony létesítése** csempét az irányítópulton. Itt kiválaszthatja a virtuális hálózati kapcsolat, és megtekintheti annak állapotát, trenddiagramok csomagvesztés, a sávszélesség kihasználtságát és a késés.

![kapcsolatcsoport listája](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>Csomópontok megtekintése

A helyszíni és a választott társviszony-létesítési ExpressRoute-kapcsolat Szolgáltatásvégpontok Azure virtuális gépek vagy a Microsoft közötti összes hivatkozást listájának megtekintése, kattintson a **csomóponti hivatkozások megtekintése**. Minden hivatkozás állapotát, valamint a csomagvesztés és a hozzájuk társított késés trendje tekintheti meg.

![Csomópontok megtekintése](./media/how-to-npm/nodes.png)

### <a name="topology"></a>Kapcsolatcsoport topológia

Kapcsolatcsoport topológiájának megtekintéséhez kattintson a **topológia** csempére. Ekkor megjelenik a topológia e nézetében a kiválasztott kapcsolatcsoporthoz vagy a társviszony-létesítés. A topológiadiagramot a minden egyes szegmens a késést biztosít a hálózaton található. 3. rétegbeli ugrásokra egy csomópont a diagram képviseli. Az Ugrás kapcsolatos további részletekért kattintson egy Ugrás a tárja fel.

Növelheti helyszíni útválasztók ugrásainak tartalmazza az alábbi csúszka mozgatásával láthatóság szintjét **szűrők**. A csúszka mozgatása jobbra vagy balra, növekszik és csökken az ugrások számát, a topológia Graph. Minden egyes szegmens a késés akkor látható, amely lehetővé teszi a hálózat nagy késésű szegmensek gyorsabban elkülönítését.

![szűrők](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>A kapcsolatcsoport részletes topológia megtekintése

Ebben a nézetben látható a virtuális hálózatok közötti kapcsolatok.
![részletes topológia](./media/how-to-npm/17.png)