---
title: Terheléselosztási szolgáltatások használata az Azure-ban | Microsoft dokumentumok
description: 'Ez az oktatóanyag bemutatja, hogyan hozhat létre egy forgatókönyvet az Azure terheléselosztási portfólióhasználatával: Traffic Manager, Application Gateway és Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: rohink
ms.openlocfilehash: cccd4a6b0b52608a6a17b73688e18f27088df5b0
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757205"
---
# <a name="using-load-balancing-services-in-azure"></a>Terheléselosztási szolgáltatások használata az Azure-ban

## <a name="introduction"></a>Introduction (Bevezetés)

A Microsoft Azure több szolgáltatást is kínál a hálózati forgalom elosztásának és a terheléselosztásnak a kezelésére. Ezeket a szolgáltatásokat külön-külön is használhatja, vagy az igényeinek megfelelően kombinálhatja a módszereiket az optimális megoldás létrehozásához.

Ebben az oktatóanyagban először meghatározunk egy ügyfélhasználati esetet, és bemutatjuk, hogyan lehet robusztusabbá és hatékonyabbá tenni a következő Azure terheléselosztási portfólió használatával: Traffic Manager, Application Gateway és Load Balancer. Ezután lépésenként idlonyolni kell egy földrajzilag redundáns központi telepítés t, a forgalmat a virtuális gépek között, és segít a különböző típusú kérelmek kezeléséhez.

Fogalmi szinten ezek a szolgáltatások külön szerepet játszanak a terheléselosztási hierarchiában.

* **A Traffic Manager** globális DNS-terheléselosztást biztosít. Megvizsgálja a bejövő DNS-kérelmeket, és kifogástalan végponttal válaszol, az ügyfél által kiválasztott útválasztási házirendnek megfelelően. Az útválasztási módszerek lehetőségei a következők:
  * Teljesítmény-útválasztás a kérelmező nek a legközelebbi végpontra történő elküldéséhez a késés szempontjából.
  * Prioritás-útválasztás az összes forgalmat egy végpontra irányíthatja, más végpontokkal pedig biztonsági mentésként.
  * Súlyozott ciklikus multiplexelés útválasztás, amely elosztja a forgalmat az egyes végpontok hozrendelt súlyozás alapján.
  * Földrajzi alapú útválasztás a forgalom elosztása az alkalmazás végpontjai a felhasználó földrajzi helye alapján.
  * Alhálózati alapú útválasztás a forgalom elosztása az alkalmazás végpontok alapján a felhasználó alhálózat (IP-címtartománya).
  * Többértékű útválasztás, amely lehetővé teszi, hogy egyetlen DNS-válaszban egynél több alkalmazásvégpont IP-címét küldje el.

  Az ügyfél közvetlenül csatlakozik a Traffic Manager által visszaadott végponthoz. Az Azure Traffic Manager észleli, ha egy végpont nem kifogástalan, és majd átirányítja az ügyfelek egy másik kifogástalan állapotú példány. A szolgáltatásról az [Azure Traffic Manager dokumentációjában](traffic-manager-overview.md) olvashat bővebben.
* **Az Application Gateway** szolgáltatásként biztosítja az alkalmazáskézbesítési vezérlőt (ADC), amely különböző Layer 7 terheléselosztási lehetőségeket kínál az alkalmazásszámára. Lehetővé teszi az ügyfelek számára, hogy optimalizálják a webfarm okait a CPU-igényes TLS-végződés nek az alkalmazásátjáróra történő kiszervezésével. A Layer 7 egyéb útválasztási képességei közé tartozik a bejövő forgalom ciklikus multiplexelése, a cookie-alapú munkamenet-affinitás, az URL-útvonal-alapú útválasztás, valamint az egyetlen alkalmazásátjáró mögött több webhely üzemeltetésének lehetősége. Az Application Gateway konfigurálható internetfelé néző átjáróként, csak belső átjáróként vagy a kettő kombinációjaként. Az Application Gateway teljes mértékben felügyelt, méretezhető és magas rendelkezésre állású. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében.
* **A terheléselosztó** az Azure SDN-verem szerves része, amely nagy teljesítményű, alacsony késleltetésű Layer 4 terheléselosztási szolgáltatásokat biztosít az összes UDP- és TCP-protokollhoz. Kezeli a bejövő és kimenő kapcsolatokat. Segítségével nyilvános és belső elosztott terhelésű végpontok konfigurálhatók, valamint szabályok definiálhatók, amelyek a bejövő kapcsolatokat a háttérbeli készletben található célokra irányítják TCP- és HTTP-állapotellenőrzési lehetőségek használatával, a szolgáltatás rendelkezésre állásának felügyeletéhez.

## <a name="scenario"></a>Forgatókönyv

Ebben a példában egy egyszerű webhelyet használunk, amely kétféle tartalmat szolgál ki: képeket és dinamikusan megjelenített weboldalakat. A webhelynek földrajzilag redundánsnak kell lennie, és a legközelebbi (legalacsonyabb késleltetésű) helyről kell kiszolgálnia a felhasználókat. Az alkalmazás fejlesztője úgy döntött, hogy minden URL-t, amely megfelel a minta /images/* szolgálnak ki egy dedikált készlet virtuális gépek, amelyek eltérnek a többi webfarm.

Emellett a dinamikus tartalmat kiszolgáló alapértelmezett virtuálisgép-készletnek egy magas rendelkezésre állású fürtön tárolt háttér-adatbázissal kell beszélnie. A teljes üzembe helyezés az Azure Resource Manageren keresztül van beállítva.

A Traffic Manager, az Application Gateway és a Load Balancer használatával ez a webhely elérheti a következő tervezési célokat:

* **Többföldrajzi redundancia:** Ha egy régió leáll, a Traffic Manager az alkalmazás tulajdonosának beavatkozása nélkül zökkenőmentesen irányítja a forgalmat a legközelebbi régióba.
* **Csökkentett késés:** Mivel a Traffic Manager automatikusan a legközelebbi régióba irányítja az ügyfelet, az ügyfél kisebb késést tapasztal a weblap tartalmának kérésekor.
* **Független méretezhetőség:** Mivel a webalkalmazás számítási feladatai tartalomtípus szerint vannak elválasztva, az alkalmazás tulajdonosa egymástól függetlenül skálázhatja a kérelemszámítási feladatokat. Az Application Gateway biztosítja, hogy a forgalom a megadott szabályok és az alkalmazás állapota alapján a megfelelő készletekhez legyen irányítva.
* **Belső terheléselosztás:** Mivel a terheléselosztó a magas rendelkezésre állású fürt előtt van, csak az adatbázis aktív és kifogástalan végpontja van kitéve az alkalmazásnak. Emellett az adatbázis-rendszergazda optimalizálhatja a számítási feladatok at az aktív és passzív replikák elosztásával a fürtön az előtér-alkalmazástól függetlenül. A terheléselosztó kapcsolatot biztosít a magas rendelkezésre állású fürthöz, és biztosítja, hogy csak kifogástalan állapotú adatbázisok kapjanak csatlakozási kérelmeket.

Az alábbi ábra a forgatókönyv architektúráját mutatja be:

![A terheléselosztási architektúra diagramja](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Ez a példa csak egy a sok lehetséges konfigurációk az Azure által nyújtott terheléselosztási szolgáltatások. A Traffic Manager, az Application Gateway és a Load Balancer keverhető és a terheléselosztási igényeknek leginkább megfelelően illeszthető. Ha például a TLS-kiszervezés vagy a 7.

## <a name="setting-up-the-load-balancing-stack"></a>A terheléselosztási verem beállítása

### <a name="step-1-create-a-traffic-manager-profile"></a>1. lépés: Traffic Manager-profil létrehozása

1. Az Azure Portalon kattintson az **Erőforrás** > **létrehozása hálózati** > **forgalomkezelő profil** > **létrehozása elemre.**
2. Adja meg a következő alapvető információkat:

   * **Név**: Adjon a Traffic Manager-profilnak DNS-előtagnevet.
   * **Útválasztási módszer**: Válassza ki a forgalom-útválasztási metódus házirendet. A módszerekről a [Traffic Manager forgalomútválasztási módszerei – további](traffic-manager-routing-methods.md)információt talál.
   * **Előfizetés**: Válassza ki a profilt tartalmazó előfizetést.
   * **Erőforráscsoport**: Válassza ki a profilt tartalmazó erőforráscsoportot. Ez lehet egy új vagy meglévő erőforráscsoport.
   * **Erőforráscsoport helye**: A Forgalomkezelő szolgáltatás globális, és nem kötődik egy helyhez. Azonban meg kell adnia egy régiót ahhoz a csoporthoz, ahol a Traffic Manager-profilhoz társított metaadatok találhatók. Ez a hely nincs hatással a profil futásidejű rendelkezésre állására.

3. Kattintson a **Létrehozás** gombra a Traffic Manager-profil létrehozásához.

   !["Traffic Manager létrehozása" panel](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>2. lépés: Az alkalmazásátjárók létrehozása

1. Az Azure Portalon a bal oldali ablaktáblában kattintson az Erőforrás > **létrehozása hálózati** > **alkalmazásátjáró** **elemre.**
2. Adja meg a következő alapvető információkat az alkalmazásátjáróról:

   * **Név**: Az alkalmazásátjáró neve.
   * **Termékváltozat mérete:** Az alkalmazásátjáró mérete, amely kicsi, közepes vagy nagy.
   * **Példányok száma**: A példányok száma, egy 2 és 10 között lévő érték.
   * **Erőforráscsoport**: Az alkalmazásátjárót tartalmazó erőforráscsoport. Ez lehet egy meglévő erőforráscsoport vagy egy új.
   * **Hely**: Az alkalmazásátjáró régiója, amely az erőforráscsoporttal azonos helyen található. A hely fontos, mert a virtuális hálózatnak és a nyilvános IP-címnek ugyanazon a helyen kell lennie, mint az átjárónak.
3. Kattintson az **OK** gombra.
4. Adja meg az alkalmazásátjáró virtuális hálózat-, alhálózati, előtér-IP-és figyelőkonfigurációit. Ebben a forgatókönyvben az előtér-IP-cím **nyilvános,** amely lehetővé teszi, hogy később végpontként hozzáadja a Traffic Manager-profilhoz.
5. Konfigurálja a figyelőt az alábbi lehetőségek egyikével:
    * Http használata esetén nincs mit konfigurálni. Kattintson az **OK** gombra.
    * Https használata esetén további konfigurálásra van szükség. Lásd: [Alkalmazásátjáró létrehozása](../application-gateway/application-gateway-create-gateway-portal.md), a 9. Miután befejezte a konfigurációt, kattintson az **OK**gombra.

#### <a name="configure-url-routing-for-application-gateways"></a>Url-útválasztás konfigurálása alkalmazásátjárókhoz

Ha háttérkészletet választ, az elérési úton alapuló szabállyal konfigurált alkalmazásátjáró a ciklikus multiplexeléselosztás mellett a kérelem URL-címének elérési útját veszi fel. Ebben az esetben egy elérési útalapú szabályt adunk hozzá\*a "/images/ " url-címnek a rendszerképkiszolgáló készlethez való irányításához. Az alkalmazásátjáró URL-útvonal-alapú útválasztásának konfigurálásáról az [Alkalmazásátjáró elérési útának létrehozása](../application-gateway/application-gateway-create-url-route-portal.md)című témakörben talál további információt.

![Alkalmazásátjáró webréteg-diagramja](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Az erőforráscsoportból nyissa meg az előző szakaszban létrehozott alkalmazásátjáró példányát.
2. A **Beállítások csoportban**válassza **a Háttérkészletek**lehetőséget, majd a **Hozzáadás** lehetőséget a webes szintű háttérkészletekhez társítani kívánt virtuális gépek hozzáadásához.
3. Adja meg a háttérkészlet nevét és a készletben található gépek összes IP-címét. Ebben a forgatókönyvben két virtuális gépek háttérkiszolgáló-készleteit kapcsoljuk össze.

   ![Alkalmazásátjáró "Háttérkészlet hozzáadása"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Az alkalmazásátjáró **beállításai csoportban** válassza a **Szabályok**lehetőséget, majd a **Görbe alapú** gombra kattintva adjon hozzá egy szabályt.

   ![Alkalmazásátjáró-szabályok "Elérési út alapú" gomb](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Konfigurálja a szabályt a következő információk megadásával.

   Alapvető beállítások:

   + **Név**: A portálon elérhető szabály rövid neve.
   + **Figyelő:** A szabályhoz használt figyelő.
   + **Alapértelmezett háttérkészlet**: Az alapértelmezett szabállyal használandó háttérkészlet.
   + **Alapértelmezett HTTP-beállítások**: Az alapértelmezett szabállyal használandó HTTP-beállítások.

   Elérési útalapú szabályok:

   + **Név**: Az elérési útalapú szabály rövid neve.
   + **Elérési utak**: A forgalom továbbítására használt elérési útszabály.
   + **Háttérkészlet:** Ezzel a szabállyal használandó háttérkészlet.
   + **HTTP-beállítás**: A szabályhoz használandó HTTP-beállítások.

   > [!IMPORTANT]
   > Elérési utak: Az érvényes elérési utaknak "/" kezdetűnek kell kezdődniük. A "\*helyettesítő karakter csak a végén engedélyezett. Érvényes példák: /xyz,\*/xyz ,\*vagy /xyz/ .

   ![Alkalmazásátjáró "Elérési út alapú szabály hozzáadása" panel](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>3. lépés: Alkalmazásátjárók hozzáadása a Traffic Manager-végpontokhoz

Ebben a forgatókönyvben a Traffic Manager különböző régiókban található alkalmazásátjárókhoz csatlakozik (az előző lépésekben konfigurálva). Most, hogy az alkalmazásátjárók konfigurálva vannak, a következő lépés az, hogy csatlakoztassa őket a Traffic Manager-profilhoz.

1. Nyissa meg a Traffic Manager-profilját. Ehhez keresse meg az erőforráscsoportot, vagy keresse meg a Traffic Manager-profil nevét az **Összes erőforrás területen.**
2. A bal oldali ablaktáblában válassza a **Végpontok**lehetőséget, majd a Végpont hozzáadásához kattintson a **Hozzáadás** gombra.

   ![A Traffic Manager "Hozzáadás" végpontjai gomb](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Hozzon létre egy végpontot a következő adatok megadásával:

   * **Típus**: Válassza ki a terheléselosztáshoz kívánt végpont típusát. Ebben a forgatókönyvben válassza ki **az Azure-végpontot,** mert a korábban konfigurált alkalmazásátjáró-példányokhoz kapcsoljuk.
   * **Név**: Adja meg a végpont nevét.
   * **Célerőforrás-típus**: Válassza a **Nyilvános IP-címet,** majd a **Célerőforrás**csoportban válassza ki a korábban konfigurált alkalmazásátjáró nyilvános IP-címét.

   ![Traffic Manager "Végpont hozzáadása"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Most tesztelheti a beállítás elérésével a DNS a Traffic Manager profil (ebben a példában: TrafficManagerScenario.trafficmanager.net). Újraküldheti a kérelmeket, létrehozhatja vagy leválaszthatja a különböző régiókban létrehozott virtuális gépeket és webkiszolgálókat, és módosíthatja a Traffic Manager profilbeállításait a beállítás teszteléséhez.

### <a name="step-4-create-a-load-balancer"></a>4. lépés: Terheléselosztó létrehozása

Ebben a forgatókönyvben a terheléselosztó a webes rétegből a magas rendelkezésre állású fürtön belüli adatbázisok közötti kapcsolatokat osztja el.

Ha a magas rendelkezésre állású adatbázis-fürt az SQL Server AlwaysOn kiszolgálót használja, tekintse meg [egy vagy több Mindig rendelkezésre állási csoportfigyelőkonfigurálása](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) című részletes útmutatást.

A belső terheléselosztó konfigurálásáról további információt [a Belső terheléselosztó létrehozása az Azure Portalon című témakörben talál.](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)

1. Az Azure Portalon a bal oldali ablaktáblában kattintson az **Erőforrás** > **hálózati** > **terheléselosztó**létrehozása elemre.
2. Válasszon nevet a terheléselosztónak.
3. Állítsa a **Típus** mezőt **Belső**értékre, és válassza ki a terheléselosztó számára megfelelő virtuális hálózatot és alhálózatot.
4. Az **IP-címhozzárendelés csoportban**válassza a **Dinamikus** vagy **a Statikus**lehetőséget.
5. Az **Erőforrás csoport**csoportban válassza ki a terheléselosztó erőforráscsoportját.
6. A **Hely csoportban**válassza ki a terheléselosztó megfelelő régióját.
7. A terheléselosztó létrehozásához kattintson a **Létrehozás** gombra.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Háttéradatbázis-réteg csatlakoztatása a terheléselosztóhoz

1. Az erőforráscsoportból keresse meg az előző lépésekben létrehozott terheléselosztót.
2. A **Beállítások csoportban**kattintson **a Háttérszintű készletek**elemre, majd a **Hozzáadás** gombra háttérkészlet hozzáadásához.

   !["Háttérkészlet hozzáadása" terheléselosztó](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Adja meg a háttérkészlet nevét.
4. Adja hozzá az egyes gépeket vagy egy rendelkezésre állási készletet a háttérkészlethez.

#### <a name="configure-a-probe"></a>Mintavétel konfigurálása

1. A terheléselosztó beállítások **területén**válassza a **Mintavételek**lehetőséget, majd a **Hozzáadás** gombra kattintva adjon hozzá egy mintavételt.

   !["Szonda hozzáadása" terheléselosztó](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Adja meg a szonda nevét.
3. Válassza ki a **protokollt** a szondához. Adatbázis esetén előfordulhat, hogy http-mintavétel helyett TCP-mintavételt szeretne. Ha többet szeretne megtudni a terheléselosztó-szondákról, olvassa el [a Terheléselosztó mintavételek megértése](../load-balancer/load-balancer-custom-probe-overview.md).
4. Adja meg a mintavétel eléréséhez használni használt adatbázis **portját.**
5. Az **Intervallum**csoportban adja meg, hogy milyen gyakran vizsgálja meg az alkalmazást.
6. A **nem kifogástalan küszöbérték alatt**adja meg a folyamatos mintavételi hibák számát, amelyeknek a háttér-virtuális gép nem megfelelőnek kell lennie.
7. A mintavétel létrehozásához kattintson az **OK** gombra.

#### <a name="configure-the-load-balancing-rules"></a>A terheléselosztási szabályok konfigurálása

1. A terheléselosztó **beállításai csoportban** válassza a **Terheléselosztási szabályok**lehetőséget, majd a **Hozzáadás** gombra kattintva hozzon létre egy szabályt.
2. Adja meg a terheléselosztási szabály **nevét.**
3. Válassza ki a terheléselosztó, a **Protokoll**és a **Port** **előtér IP-címét.**
4. A **Háttérablak csoportban**adja meg a háttérkészletben használandó portot.
5. Válassza ki a **háttérkészletet** és az előző lépésekben létrehozott **mintavételt** a szabály alkalmazásához.
6. A **Munkamenet-megőrzés csoportban**válassza ki, hogyan szeretné, hogy a munkamenetek megmaradjanak.
7. Az **első járatnyi időtúltöltés**csoportban adja meg, hogy hány perccel az első járatidőtúltöltés előtt.
8. A **Lebegő IP csoportban**válassza a **Letiltott** vagy **az Engedélyezve**lehetőséget.
9. A szabály létrehozásához kattintson az **OK** gombra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>5. lépés: Webes szintű virtuális gépek csatlakoztatása a terheléselosztóhoz

Most konfiguráljuk az IP-cím és a terheléselosztó előtér-portaz alkalmazások, amelyek futnak a webes szintű virtuális gépek bármilyen adatbázis-kapcsolatok. Ez a konfiguráció az ezeken a virtuális gépeken futó alkalmazásokra vonatkozik. A cél IP-címének és portjának konfigurálásához olvassa el az alkalmazás dokumentációját. Az előtér IP-címének megkereséséhez az Azure Portalon nyissa meg az előtér-IP-készletet a **terheléselosztó beállításaiközött.**

!["Előtér IP-készlet" navigációs ablaktábla](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>További lépések

* [A Traffic Manager áttekintése](traffic-manager-overview.md)
* [Az Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md)
* [Az Azure Load Balancer áttekintése](../load-balancer/load-balancer-overview.md)
