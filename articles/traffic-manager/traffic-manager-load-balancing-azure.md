---
title: Terheléselosztási szolgáltatás használata az Azure-ban |} A Microsoft Docs
description: 'Ez az oktatóanyag bemutatja, hogyan hozhat létre a forgatókönyv az Azure-terheléselosztó portfólióban használatával: A TRAFFIC Manager, az Application Gateway és a Load Balancert.'
services: traffic-manager
documentationcenter: ''
author: liumichelle
manager: dkays
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: 906e1840f35ab14997c727551b893a0219eb78d8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099018"
---
# <a name="using-load-balancing-services-in-azure"></a>Terheléselosztási szolgáltatás használata az Azure-ban

## <a name="introduction"></a>Bevezetés

A Microsoft Azure több szolgáltatásokat kínál a hálózati forgalom hogyan ossza kezelése és elosztott terhelésű. Külön-külön használja ezeket a szolgáltatásokat, vagy kombinálja a módszereket, hozhat létre az optimális megoldást igényeitől függően.

Ebben az oktatóanyagban azt először határozza meg a felhasználói használati esetek és hogyan el kell végezni robusztusabb és nagy teljesítményű lásd a következő Azure terheléselosztási portfólió használatával: A TRAFFIC Manager, az Application Gateway és a Load Balancert. Ezután biztosítunk részletes útmutató, amely a georedundáns, elosztja a forgalmat a virtuális gépekhez, és segít a központi telepítés létrehozásához a különböző típusú kérelmek kezelése.

Elméleti szinten ezen szolgáltatások mindegyike szerepet játszik az olyan különálló a terheléselosztás hierarchiában.

* **A TRAFFIC Manager** globális DNS terheléselosztást biztosít. Megvizsgálja a beérkező DNS és a egy megfelelően működő végpont, összhangban az útválasztási házirendet az ügyfél ki van választva fűzi hozzá. Útválasztási módszerek lehetőségek közül választhat:
  * Teljesítmény-útválasztási késés szempontjából a legközelebb eső végpont a kérelmező küldéséhez.
  * Az összes forgalmat egy végpontot, más biztonsági végpontokkal rendelkező útválasztási prioritás.
  * Súlyozott ciklikus időszeletelés útválasztás, amely elosztja a forgalmat, amely minden végpont van rendelve a súlyozás alapján.
  * Földrajzi alapú az Útválasztás a felhasználó földrajzi helye alapján végpontjainak a forgalom elosztása.
  * Az alhálózat (IP-címtartomány) a felhasználó alapján alhálózat alapú útválasztást, ossza el a forgalmat a végpontjainak.
  * Többértékű útválasztást, amely lehetővé teszi több végpontjainak IP-címek egyetlen DNS-válasz küldése.

  Az ügyfél közvetlenül a Traffic Manager által visszaadott végponthoz csatlakozik. Az Azure Traffic Manager észleli, ha a végpont nem kifogástalan, és ezután átirányítja a az ügyfelek egy másik megfelelő példány. Tekintse meg [Azure Traffic Manager dokumentációja](traffic-manager-overview.md) további információ a szolgáltatást.
* **Az Application Gateway** alkalmazáskézbesítési vezérlőt (ADC) biztosít a szolgáltatás, amely számos 7. rétegbeli terheléselosztási lehetőséget nyújt alkalmazásának. Lehetővé teszi az ügyfelek optimalizálhatják a webfarmok termelékenységét a processzorigényes SSL-lezárások az Alkalmazásátjáró felé történő kiszervezésével. Egyéb 7. rétegbeli útválasztási lehetőségeket tartalmazza, Ciklikus időszeleteléses elosztását bejövő forgalom, a cookie-alapú munkamenet-affinitást, a URL-cím-alapú útválasztás és a egy application gateway mögött több webhelyet is üzemeltethet. Az Application Gateway konfigurálható egy internetre irányuló átjáró, egy belső átjáró vagy mindkettőt. Application Gateway-példány az Azure teljes körűen felügyelt, méretezhető és magas rendelkezésre állású. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében.
* **Load Balancer** szerves része az Azure SDN-verem, nagy teljesítményű, kis késleltetésű 4. rétegű terheléselosztási szolgáltatásokat nyújtó minden UDP és TCP protokollra. Kezeli a bejövő és kimenő kapcsolatokat. A nyilvános és belső elosztott terhelésű végpontok konfigurálása, és definiáljon szabályokat a bejövő kapcsolatok leképezése háttérbeli készletet destinations TCP- és HTTP-állapotellenőrzéséhez beállítások használatával kezelheti a szolgáltatás rendelkezésre állása.

## <a name="scenario"></a>Forgatókönyv

Ebben a példában a forgatókönyvben két típusú tartalmat szolgál egy olyan egyszerű webhely használjuk: képek és dinamikusan megjelenített weblapok. A webhely georedundáns kell lennie, és a felhasználók a legközelebb (legkisebb késéssel) azt kell szolgálnia helyet őket. Az alkalmazás fejlesztőjének úgy döntött, hogy bármely URL-címek, amelyek megfelelnek a minta/képek / * üzemeltetett virtuális gépek, amelyek eltérnek a web farm többi a dedikált készletből.

Emellett az alapértelmezett VM-készletet, a dinamikus tartalmat szolgáltató kommunikáljon egy háttérbeli futó adatbázis magas rendelkezésre állású fürt kell. A teljes üzembe helyezés Azure Resource Manageren keresztül állítható be.

A Traffic Manager, az Application Gateway és a Load Balancer használatával lehetővé teszi, hogy a webhely ezen tervezési célok eléréséhez:

* **Többszörös közötti redundancia**: Egy adott régióban leáll, ha a Traffic Manager átirányítja a forgalmat zökkenőmentesen a beavatkozása nélkül a legközelebbi régió, az alkalmazás tulajdonosa.
* **Kisebb késést**: A Traffic Manager automatikusan átirányítja az ügyfél számára a legközelebbi régió, mert az a felhasználói élményt kisebb késés, a weblap tartalma kérésekor.
* **Független méretezhetősége**: A webes alkalmazás számítási feladatait van elválasztva a tartalom típusa, mivel az alkalmazás tulajdonosa méretezheti a kérelem számítási feladatok egymástól független. Az Application Gateway biztosítja, hogy az adatforgalmat a megadott szabályok és az alkalmazás állapotának alapján a megfelelő tárolókészletekben.
* **Belső terheléselosztás**: Mivel a terheléselosztó a magas rendelkezésre állású fürt előtt, csak aktív és kifogástalan állapotú közzéteszi a végpontot egy adatbázis az alkalmazáshoz. Ezenkívül az adatbázis-rendszergazda képes optimalizálni az a számítási feladatok aktív és passzív replika osztja meg a független az előtér-alkalmazást a fürtön. Load Balancer továbbítja az kapcsolatok a magas rendelkezésre állású fürt, és biztosítja, hogy csak kifogástalan állapotú adatbázisok csatlakozási kérések fogadása.

Az alábbi ábra az ebben a forgatókönyvben architektúráját mutatja be:

![Terheléselosztás ábrája architektúra](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Ebben a példában az csak a terheléselosztási szolgáltatások, Azure által kínált számos lehetséges konfigurációk egyik. A TRAFFIC Manager, az Application Gateway és a Load Balancer lehet vegyes, és a terheléselosztás igényeinek legjobban megfeleljen egyezteti. Például ha SSL alapú kiszervezést, illetve a 7. rétegbeli feldolgozása már nem szükséges, Load Balancer az Application Gateway helyett használható.

## <a name="setting-up-the-load-balancing-stack"></a>A terheléselosztó-verem telepítése

### <a name="step-1-create-a-traffic-manager-profile"></a>1. lépés: Traffic Manager-profil létrehozása

1. Az Azure Portalon kattintson a **erőforrás létrehozása** > **hálózatkezelés** > **Traffic Manager-profil**  >   **Hozzon létre**.
2. Adja meg a következő alapvető információkat:

   * **Név**: Adjon a Traffic Manager-profil DNS-előtagnevet.
   * **Útválasztási módszer**: Válassza ki a forgalom-útválasztási módszer házirendet. A módszerekkel kapcsolatos további információkért lásd: [Traffic Manager forgalom-útválasztási módszerek](traffic-manager-routing-methods.md).
   * **Előfizetés**: Válassza ki az előfizetést, amelyhez a profil tartalmazza.
   * **Erőforráscsoport**: Válassza ki az erőforráscsoportot, amely tartalmazza a profil. Ez lehet egy új vagy meglévő erőforráscsoportot.
   * **Erőforráscsoport helye**: Traffic Manager szolgáltatás globális, és nem egy helyre kötve. Azonban meg kell adnia egy régiót a Traffic Manager-profil társított metaadatokat tartalmazó csoport. Ezen a helyen nem befolyásolja a profil futásidejű rendelkezésre állását.

3. Kattintson a **létrehozás** a Traffic Manager-profil létrehozásához.

   !["A Traffic Manager létrehozása" panel](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>2. lépés: Az application Gateway átjárók létrehozása

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **erőforrás létrehozása** > **hálózatkezelés** > **Application Gateway**.
2. Adja meg az application gateway a következő alapvető információkat:

   * **Név**: Az application gateway neve.
   * **SKU-méret**: Az application Gateway átjáróhoz is rendelkezésre állnak, kicsi, közepes vagy nagy a mérete.
   * **Példányszám**: A példányok száma, 2 és 10 közötti értéket.
   * **Erőforráscsoport**: Az erőforráscsoport, amely az application gateway tárol. Egy meglévő erőforráscsoportot, vagy egy új lehet.
   * **Hely**: Az application gateway, amely megegyezik az erőforráscsoport ugyanazon a helyen a régióban. A hely fontos, mivel a virtuális hálózat és a nyilvános IP-címet az átjáró ugyanazon a helyen kell lennie.
3. Kattintson az **OK** gombra.
4. Adja meg a virtuális hálózat, alhálózat, előtérbeli IP-címet és figyelő konfigurációk az application gateway számára. Ebben a forgatókönyvben az előtér-IP-cím van **nyilvános**, amely lehetővé teszi, hogy lehet hozzáadni, a végpont a Traffic Manager-profil később.
5. Konfigurálja a figyelőt a következő lehetőségek közül:
    * Ha a HTTP használata esetén nincs semmit nem kell konfigurálni. Kattintson az **OK** gombra.
    * Ha a HTTPS PROTOKOLLT használja, további konfigurációs szükség. Tekintse meg [hozzon létre egy application gateway](../application-gateway/application-gateway-create-gateway-portal.md)keretében már 9. lépés. A konfiguráció befejeztével kattintson **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Az application Gateway átjárók URL-útválasztás konfigurálása

Ha úgy dönt, hogy egy háttérkészlet-, az application gateway egy útvonalalapú szabály konfigurálva van a kérelem URL-címének ciklikus multiplexelés mellett egy elérési út mintája vesz igénybe. Ebben a forgatókönyvben egy útvonalalapú szabály bármely URL-címet a közvetlen adunk "/images/\*" a lemezkép kiszolgálókészlethez. További információ az URL-cím konfigurálása az application Gateway-alapú útválasztás tekintse meg [hozzon létre egy application gateway-alapú szabályt](../application-gateway/application-gateway-create-url-route-portal.md).

![Application Gateway webes szintű diagram](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Az erőforráscsoportból nyissa meg az application gateway az előző szakaszban létrehozott példányát.
2. Alatt **beállítások**, jelölje be **háttérkészletek**, majd válassza ki **hozzáadása** szeretne társítani a webes szintű háttér-készletekkel rendelkező virtuális gépek hozzáadásához.
3. Adja meg, hogy a készletben található gépek IP-címek és a háttérkészlet nevét. Ebben a forgatókönyvben a virtuális gépek két háttér-kiszolgálókészletek kapcsolódik.

   ![Az Application Gateway "Háttérkészlet hozzáadása"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. A **beállítások** válassza ki az application Gateway **szabályok**, majd kattintson a **elérésiút-alapú** gombra egy szabály hozzáadásához.

   ![Application Gateway szabályokat "Elérési út alapján" gomb](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. A szabály konfigurálása a következő információk megadásával.

   Alapszintű beállítások:

   + **Név**: A szabály a portálon elérhető, rövid neve.
   + **Figyelő**: A szabály a figyelőt.
   + **Alapértelmezett háttérkészlet**: A háttérbeli készletet, amely az alapértelmezett szabály használható.
   + **Alapértelmezett HTTP-beállítások**: A HTTP-beállítások az alapértelmezett szabály használható.

   -Alapú szabályok:

   + **Név**: Az útvonalalapú szabály rövid neve.
   + **Elérési utak**: Az elérésiút-szabályt, amely továbbítja a forgalmat szolgál.
   + **Háttérkészlet**: Ez a szabály használható a háttér-készletet.
   + **HTTP-beállítás**: A HTTP-beállítások, ez a szabály használható.

   > [!IMPORTANT]
   > Elérési utak: Érvényes útvonalak kell kezdődnie "/". A helyettesítő karakteres "\*" végén csak engedélyezett. Érvényes példák /xyz, /xyz\*, vagy /xyz/\*.

   ![Application Gateway "Útvonalalapú szabály felvétele" panel](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>3. lépés: Adja hozzá az application Gateway-átjárók, a Traffic Manager-végpontok

Ebben a forgatókönyvben a Traffic Manager csatlakoztatva van az application Gateway átjárók (az a fenti lépések szerint), amelyek különböző régiókban találhatók. Most, hogy az application Gateway átjárók vannak konfigurálva, a következő lépés az csatlakoztathatja őket a Traffic Manager-profilra.

1. Nyissa meg a Traffic Manager-profil. Ehhez tekintse meg az erőforráscsoportot, vagy keresse meg a Traffic Manager-profil neve **összes erőforrás**.
2. A bal oldali panelen válassza ki a **végpontok**, és kattintson a **Hozzáadás** végpont hozzáadásának.

   ![A TRAFFIC Manager végpont "Hozzáadás" gombra](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Hozzon létre egy végpont írja be az alábbi adatokat:

   * **Típus**: Válassza ki a végpont terheléselosztásához. Ebben az esetben válassza **Azure-végpont** mert kapcsolódunk az application gateway-példány korábban konfigurált.
   * **Név**: Adja meg a végpont nevét.
   * **Célerőforrás típusa**: Válassza ki **nyilvános IP-cím** majd **Célerőforrásnál**, válassza ki a korábban beállított application Gateway nyilvános IP-cím.

   ![A TRAFFIC Manager "Hozzáadás végpont"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Most tesztelheti a telepítő úgy férjenek hozzá a DNS-ben a Traffic Manager-profil (ebben a példában: TrafficManagerScenario.trafficmanager.net). Kérelem újraküldése, vizualizációjára vagy állítsa le a virtuális gépek és a webkiszolgálók esetében, amelyek különböző régiókban létrehozott, és a telepítés teszteléséhez a Traffic Manager-profil beállításainak módosítása.

### <a name="step-4-create-a-load-balancer"></a>4. lépés: Load Balancer létrehozása

Ebben a forgatókönyvben a terheléselosztó osztja el az adatbázisok magas rendelkezésre állású fürtön belül csatlakozhat a webes szintről.

Ha az adatbázis magas rendelkezésre állású fürt használt SQL Server AlwaysOn, [konfigurálásához legalább egy mindig a rendelkezésre állási csoport figyelője](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) részletes útmutatásait.

Belső terheléselosztó konfigurálásával kapcsolatos további információkért lásd: [belső terheléselosztó létrehozása az Azure Portalon](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **erőforrás létrehozása** > **hálózatkezelés** > **terheléselosztó**.
2. Válassza ki a terheléselosztó nevét.
3. Állítsa be a **típus** való **belső**, és válassza ki a megfelelő virtuális hálózatot és alhálózatot találhatók, hogy a terheléselosztó számára.
4. A **IP-cím hozzárendelése**, ezek közül bármelyikre **dinamikus** vagy **statikus**.
5. A **erőforráscsoport**, válassza ki az erőforráscsoportot, a terheléselosztó számára.
6. A **hely**, válassza ki a megfelelő régiót a terheléselosztóhoz.
7. Kattintson a **létrehozás** a terheléselosztó létrehozásához.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Csatlakozás a terheléselosztó háttér-adatbázis-rétegből

1. Az erőforráscsoportból keresse meg a terheléselosztó, amely az előző lépésben jött létre.
2. A **beállítások**, kattintson a **háttérkészletek**, és kattintson a **Hozzáadás** egy háttérkészlet hozzáadása.

   ![Load Balancer "Háttérkészlet hozzáadása"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Adja meg a háttérkészlet nevét.
4. Adja hozzá az egyes gépek vagy a rendelkezésre állási a háttérkészlethez.

#### <a name="configure-a-probe"></a>A mintavétel konfigurálása

1. A load balancerben alatt **beállítások**, jelölje be **mintavételek**, és kattintson a **Hozzáadás** vizsgálatok hozzáadásához.

   ![Load Balancer "Mintavétel hozzáadása"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Adja meg a mintavétel neve.
3. Válassza ki a **protokoll** mintavétel. Adatbázis esetén érdemes lehet inkább egy TCP-mintavétel, mint a HTTP-mintavétel. Load balancer vizsgálatok kapcsolatos további információkért tekintse meg [megismerése a load balancer vizsgálatok](../load-balancer/load-balancer-custom-probe-overview.md).
4. Adja meg a **Port** készít az adatbázisról a mintavétel elérésekor használni.
5. A **időköz**, adja meg, hogy milyen gyakran kell mintát venni az alkalmazásból.
6. A **nem kifogástalan állapot küszöbértéke**, adja meg a folyamatos mintavételi hibák, amelyek a háttérbeli virtuális gép nem megfelelő állapotú venni a számát.
7. Kattintson a **OK** a mintavétel létrehozása.

#### <a name="configure-the-load-balancing-rules"></a>A terheléselosztási szabályok konfigurálása

1. A **beállítások** válassza ki a terheléselosztó **terheléselosztási szabályok**, és kattintson a **Hozzáadás** olyan szabály létrehozására.
2. Adja meg a **neve** a terheléselosztási szabály.
3. Válassza ki a **előtérbeli IP-cím** a terheléselosztó **protokoll**, és **Port**.
4. A **háttérport**, adja meg a háttérkészlet használandó portot.
5. Válassza ki a **háttérkészlet** és a **mintavételi** a alkalmazni a szabályt az előző lépésekben létrehozott.
6. A **munkamenet megőrzését**, válassza ki, hogyan szeretné megőrizni a munkameneteket.
7. A **üresjárati időtúllépés**, adja meg percben az üresjárati időkorlát lejárta előtt.
8. Alatt **nem fix IP-**, ezek közül bármelyikre **letiltott** vagy **engedélyezve**.
9. A szabály létrehozásához kattintson az **OK** gombra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>5. lépés: Webes szintű virtuális gépek csatlakoztatása a terheléselosztóhoz

Most úgy konfiguráljuk az IP-cím és a terheléselosztó előtérbeli portot az bármilyen adatbázis-kapcsolatok a webes szintű virtuális gépeken futó alkalmazások. Ez a konfiguráció csak a virtuális gépeken futó alkalmazások. A cél IP-cím és port konfigurálása, tekintse meg az alkalmazás dokumentációját. Az Azure Portalon az előtér-IP-cím megkereséséhez nyissa meg az előtérbeli IP-címkészlet a **terheléselosztó beállításai**.

![Load Balancer "Előtérbeli IP-készlet" navigációs ablaktábla](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>További lépések

* [A Traffic Manager áttekintése](traffic-manager-overview.md)
* [Application Gateway áttekintése](../application-gateway/application-gateway-introduction.md)
* [Az Azure Load Balancer áttekintése](../load-balancer/load-balancer-overview.md)
