---
title: Terheléselosztás szolgáltatások használata az Azure-ban |} Microsoft Docs
description: 'Ez az oktatóanyag bemutatja, hogyan hozzon létre egy olyan forgatókönyvet az Azure terheléselosztó portfóliót: Traffic Manager, az alkalmazás átjárót és a terheléselosztó.'
services: traffic-manager
documentationcenter: ''
author: liumichelle
manager: vitinnan
editor: ''
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: 86867a9d6d2c43e6505b1a06672546a017172bfe
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401106"
---
# <a name="using-load-balancing-services-in-azure"></a>Terheléselosztás szolgáltatások használata az Azure-ban

## <a name="introduction"></a>Bevezetés

A Microsoft Azure több kezelése a hálózati adatforgalom elosztása milyen szolgáltatásokat és elosztott terhelésű biztosít. Ezek a szolgáltatások külön-külön használhatja, vagy a módszerek kombinálásával az optimális megoldás kiépítését, igényeitől függően.

Ez az oktatóanyag azt először meg kell határozni vevő használati esetek és hogyan el kell végezni robusztusabb és performant lásd a következő Azure terheléselosztás portfóliót használatával: Traffic Manager, az alkalmazás átjárót és a terheléselosztó. Azt adja meg a központi telepítés, amely földrajzilag redundáns, osztja el a forgalmat a virtuális gépekhez, és segít létrehozásának részletes leírása kezelheti a kérelmek különböző.

Elméleti szinten a terheléselosztás hierarchiában különböző szerepet játszik egyes ezeket a szolgáltatásokat.

* **A TRAFFIC Manager** globális DNS terheléselosztást biztosít. Megvizsgálja a bejövő DNS-kérésekre, és a kifogástalan állapotú végpontok, az ügyfél van kijelölve útválasztási házirend szerinti válaszol. Az útválasztási metódusait lehetőségek közül választhat:
  * A teljesítmény szempontjából várakozási ideje a legközelebbi végpont a kérelmező küldendő útválasztási.
  * Minden más végpontok tartalékként, végpont felé irányuló forgalom közvetlen útválasztás prioritás.
  * Súlyozott ciklikus multiplexelés útválasztási, amely osztja el a forgalmat a végpontok rendelt súlyozás alapján.

  Az ügyfél közvetlenül kapcsolódik az adott végpontra. Az Azure Traffic Manager észleli, ha a végpont nem kifogástalan, és ezután átirányítja a felhasználókat az ügyfelek egy másik megfelelő példányához. Tekintse meg [Azure Traffic Manager dokumentációs](traffic-manager-overview.md) további információt a szolgáltatást.
* **Alkalmazásátjáró** alkalmazás kézbesítési vezérlő (LÉPETT) biztosít az alkalmazás különböző réteg 7 terheléselosztás képességeket nyújtó szolgáltatás. Lehetővé teszi az ügyfelek számára a webkiszolgáló farm termelékenység optimalizálása kiürítésével a CPU-intenzív SSL-lezárást az Alkalmazásátjáró. Más réteg 7 útválasztási lehetőségek közé tartozik a ciklikus multiplexelés, bejövő forgalmat, a munkamenet cookie-alapú kapcsolat, a URL-cím elérési út-alapú útválasztási és a több webhely mögött egyetlen Alkalmazásátjáró lehetőséget. Alkalmazásátjáró konfigurálhat egy internetes átjárót, egy csak belső-átjáró vagy mindkettőt. Alkalmazásátjáró teljesen Azure felügyelt, méretezhető és magas rendelkezésre állású. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében.
* **Terheléselosztó** az Azure SDN-vermet, nagy teljesítményű, alacsony késésű réteg 4 terheléselosztás szolgáltatásokat nyújtó minden UDP- és TCP protokollra szerves része. Bejövő és kimenő kapcsolatok kezeli. Konfigurálja a nyilvános és belső elosztott terhelésű végpont, és háttér-készlet célok TCP- és HTTP állapot-ellenőrzés beállítások segítségével kezelheti a szolgáltatás rendelkezésre állása bejövő kapcsolatok hozzárendelése szabályok meghatározásához.

## <a name="scenario"></a>Forgatókönyv

A példában egy egyszerű webhely kétféle típusú tartalom látja, hogy használjuk: lemezképek és dinamikusan megjelenített weblapjain. A webhely földrajzilag redundáns kell lennie, és azt a felhasználók a legközelebb (legkisebb mértékű késleltetést) szolgálhat hely hozzájuk. Az alkalmazás fejlesztőjének úgy döntött, hogy minden URL-címek, amelyek megfelelnek a minta/képek / *, amelyek eltérnek a webfarm a többi virtuális gépek dedikált készletből rendelkezésre.

Emellett az alapértelmezett Virtuálisgép-címkészletet a dinamikus tartalom felvegye egy háttér-adatbázis, amely a magas rendelkezésre állású fürt kell. A teljes telepítési Azure Resource Manageren keresztül állítható be.

Traffic Manager, az alkalmazás átjárót és a terheléselosztó használatával lehetővé teszi, hogy a webhely felvétele a céljai elérése:

* **Multi-georedundancia**: egy régió tartozik leáll, ha a Traffic Manager irányítja a forgalmat zökkenőmentesen beavatkozás nélküli legközelebbi régió a az alkalmazás tulajdonosa.
* **Kisebb késleltetést**: mivel a Traffic Manager automatikusan arra utasítja az ügyfél a legközelebbi régiót, az ügyfél kisebb késést biztosít észlel, amikor a kért weblap tartalmát.
* **Független méretezhetősége**: a webes alkalmazás munkaterhelés tartalomtípushoz választják el, mert az alkalmazás tulajdonosa méretezheti a kérelem munkaterhelések egymástól független. Alkalmazásátjáró biztosítja annak biztosítására, hogy a megadott szabályok és az alkalmazás állapotának alapján a megfelelő készletek a forgalom.
* **Belső terheléselosztás**: csak az aktív és a megfelelő végpont-adatbázis az alkalmazás van kitéve, mert terheléselosztó elé a magas rendelkezésre állású fürt. Adatbázis-rendszergazda emellett optimalizálható az alkalmazások és szolgáltatások a fürtön, függetlenül az előtér-alkalmazás az aktív és passzív replikák elosztásával. Terheléselosztó kapcsolatok biztosítja a magas rendelkezésre állású fürthöz, és biztosítja, hogy csak a megfelelő adatbázisok fogadják.

Az alábbi ábra az ebben a forgatókönyvben architektúráját mutatja be:

![Terheléselosztás ábrája architektúrája](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Ebben a példában az Azure által terheléselosztás szolgáltatások sok lehetséges konfiguráció csak egyik. A TRAFFIC Manager, az alkalmazás átjárót és a terheléselosztó is keverhetők, és a terheléselosztás igényeinek legjobban megfeleljen egyik megfelelő. Például ha SSL-kiszervezés, vagy a réteg 7 feldolgozási nincs szükség, Load Balancer az Alkalmazásátjáró helyett használható.

## <a name="setting-up-the-load-balancing-stack"></a>A terheléselosztó-készlet beállítása

### <a name="step-1-create-a-traffic-manager-profile"></a>1. lépés: A Traffic Manager-profil létrehozása

1. Az Azure portálon kattintson **hozzon létre egy erőforrást** > **hálózati** > **Traffic Manager-profil**  >   **Hozzon létre**.
2. Adja meg a következő alapvető adatokat:

  * **Név**: Adjon a Traffic Manager-profil egy DNS-előtag neve.
  * **Útválasztási módszer**: válassza ki a forgalom-útválasztási módszer házirendet. A módszerekkel kapcsolatos további információkért lásd: [a Traffic Manager forgalom-útválasztási módszerei](traffic-manager-routing-methods.md).
  * **Előfizetés**: válassza ki az előfizetést, amely a profil tartalmazza.
  * **Erőforráscsoport**: jelölje be a profil tartalmazó erőforráscsoportot. Egy új vagy meglévő erőforráscsoportot lehet.
  * **Erőforráscsoport helye**: a Traffic Manager szolgáltatás globális, és nem kötött helyre. Azonban meg kell adnia egy régiót a Traffic Manager-profil társított metaadatokat tartalmazó csoport. Ezen a helyen nincs hatással van a profil futásidejű rendelkezésre.

3. Kattintson a **létrehozása** a Traffic Manager-profil létrehozásához.

  !["Létrehozása a Traffic Manager" panel](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>2. lépés: Az alkalmazás-átjárók létrehozása

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **hozzon létre egy erőforrást** > **hálózati** > **Alkalmazásátjáró**.
2. Adja meg az Alkalmazásátjáró a következő alapvető információkat:

  * **Név**: az Alkalmazásátjáró nevét.
  * **Termékváltozat-méretét**: az Alkalmazásátjáró érhető el, a kis, közepes vagy nagy a mérete.
  * **Count példány**: A példányok száma, 2 és 10 közötti értéket.
  * **Erőforráscsoport**: az erőforráscsoport, amely tárolja az Alkalmazásátjáró. Lehet, hogy egy meglévő erőforráscsoportot, vagy egy újat.
  * **Hely**: az alkalmazás átjáró, amely az erőforráscsoportot és ugyanazon a helyen régióját. A hely fontos, mert a virtuális hálózat és a nyilvános IP-cím és az átjáró ugyanazon a helyen kell lennie.
3. Kattintson az **OK** gombra.
4. Adja meg a virtuális hálózati alhálózat, előtér-IP és az Alkalmazásátjáró figyelő konfigurációi. Ebben a forgatókönyvben az előtér-IP-cím van **nyilvános**, amely lehetővé teszi, hogy vehető fel a végpont a Traffic Manager-profil később.
5. A figyelő konfigurálása a következő lehetőségek közül:
    * Ha a HTTP használata esetén nincs szükség konfigurálásához. Kattintson az **OK** gombra.
    * Ha a HTTPS PROTOKOLLT használja, további konfigurációs szükség. Tekintse meg [Alkalmazásátjáró létrehozása](../application-gateway/application-gateway-create-gateway-portal.md), induló 9. lépés. Ha befejezte a konfigurálását, kattintson **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>URL-cím alkalmazás átjárók útválasztás konfigurálása

Ha úgy dönt, hogy a háttér-készlet, az elérési út alapú szabállyal konfigurált Alkalmazásátjáró egy ciklikus multiplexelés mellett a kérelem URL-címének elérési út mintája vesz igénybe. Ebben a forgatókönyvben azt ad hozzá egy elérési utat-alapú szabály olyan URL, amely közvetlen "/images/\*" a kép kiszolgálókészlethez. URL-cím konfigurálásával kapcsolatos további információkat az Alkalmazásátjáró, elérési út-alapú útválasztási hivatkoznak [Alkalmazásátjáró elérési alapú szabály létrehozásához](../application-gateway/application-gateway-create-url-route-portal.md).

![Átjáró webes szintű diagramja](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Az erőforrás-csoportból nyissa meg az Alkalmazásátjáró az előző szakaszban létrehozott példányához.
2. A **beállítások**, jelölje be **háttérkészletek**, majd válassza ki **Hozzáadás** hozzáadása a virtuális gépeket, a webes szintű háttér-készletek társítani kívánt.
3. Adja meg a háttér-címkészlet nevét, és a gép a készletben lévő összes IP-címet. Ebben a forgatókönyvben azt kapcsolódik a virtuális gépek két háttér-kiszolgálófiók rendelkezik.

  ![Alkalmazásátjáró "Add háttérkészlet"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Alatt **beállítások** jelölje ki az Alkalmazásátjáró **szabályok**, és kattintson a **elérési utat** szabály hozzáadása gomb.

  ![Alkalmazás átjáró szabályok "Elérési útja alapján" gombra](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. A szabály konfigurálása a következő információk megadásával.

   Alapvető beállítások:

   + **Név**: a szabály, amely elérhető a portál rövid nevét.
   + **Figyelő**: A figyelő, amely a szabály szolgál.
   + **Alapértelmezett háttérkészlet**: A háttér-készlet alapértelmezett szabály használható.
   + **Alapértelmezett beállítások HTTP**: A HTTP-beállítások az alapértelmezett szabály használható.

   Elérési út-alapú szabályok:

   + **Név**: az elérési út alapú szabály rövid nevét.
   + **Elérési utak**: az elérésiút-szabály, amely forgalmat szolgál.
   + **Háttérkészlet**: Ez a szabály használható a háttér-készlet.
   + **HTTP-beállítása**: Ez a szabály használható a HTTP-beállításokat.

   > [!IMPORTANT]
   > Útvonalak megadása: Érvényes elérési kezdődhet "/". A helyettesítő karakter "\*" végén csak engedélyezett. Érvényes többek között az /xyz, /xyz\*, vagy /xyz/\*.

   ![Application Gateway "Elérési út alapú szabály hozzáadása" panel](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>3. lépés: A Traffic Manager-végpontok átjárók alkalmazás hozzáadása

Ebben a forgatókönyvben a Traffic Manager alkalmazásátjárót (szerint be van állítva, az előző lépésben) különböző régiókban található csatlakozik. Az alkalmazás átjáró van beállítva, a következő lépéssel fogja csatlakoztassa őket a Traffic Manager-profil.

1. Nyissa meg a Traffic Manager-profil. Ehhez keresse meg a erőforráscsoportot, vagy keresse meg a Traffic Manager-profil neve **összes erőforrás**.
2. A bal oldali panelen válassza ki a **végpontok**, és kattintson a **Hozzáadás** a végpont hozzáadásához.

  ![A TRAFFIC Manager végpontok "Hozzáadás" gombra](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Hozzon létre egy végpontot, írja be a következő információkat:

  * **Típus**: válassza ki a végpont terheléselosztásához. Ebben az esetben válassza **Azure-végpont** mert kapcsolódik-ez a korábban konfigurált alkalmazás átjárópéldányokról.
  * **Név**: Adja meg a végpont nevét.
  * **Erőforrás céltípust**: válasszon **nyilvános IP-cím** majd az **célerőforrás**, válassza ki a korábban konfigurált alkalmazás átjáró nyilvános IP-cím.

   ![A TRAFFIC Manager "Végpont hozzáadása"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Most tesztelheti a telepítő a Traffic Manager-profil DNS elérésével (ebben a példában: TrafficManagerScenario.trafficmanager.net). Küldje el újból a kérelmeket, elindítani vagy állítsa le a virtuális gépek és a webkiszolgálók esetében, amelyek különböző régiókban létrejöttek, és a telepítés teszteléséhez a Traffic Manager-profil beállításainak módosítása.

### <a name="step-4-create-a-load-balancer"></a>4. lépés:, Hozzon létre egy adott terheléselosztóhoz

Ebben a forgatókönyvben terheléselosztó osztja el a magas rendelkezésre állású fürt-adatbázishoz a webes réteg érkező kapcsolatokat.

Ha a magas rendelkezésre állási adatbázis fürt által használt SQL Server AlwaysOn, tekintse meg a [konfigurálása egy vagy több mindig a rendelkezésre állási csoport figyelői](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) részletes útmutatásait.

A belső terheléselosztók konfigurálásával kapcsolatos további információkért lásd: [belső terheléselosztót létrehozása az Azure portálon](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **hozzon létre egy erőforrást** > **hálózati** > **terheléselosztó**.
2. Válassza ki a terheléselosztó nevét.
3. Állítsa be a **típus** való **belső**, és válassza ki a megfelelő virtuális hálózati és alhálózati lenniük, hogy a terheléselosztóhoz.
4. A **IP-cím hozzárendelése**, válassza **dinamikus** vagy **statikus**.
5. A **erőforráscsoport**, válassza ki az erőforrást a terheléselosztóhoz.
6. A **hely**, válassza ki a terheléselosztó megfelelő régiót.
7. Kattintson a **létrehozása** a terheléselosztó létrehozásához.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Háttér-adatbázis-rétegből csatlakozni a terheléselosztó

1. Az erőforrás-csoportból a terheléselosztó az előző lépésben létrehozott található.
2. A **beállítások**, kattintson a **háttérkészletek**, és kattintson a **hozzáadása** hozzáadása egy háttér címkészletet.

  ![Terheléselosztó "Add háttérkészlet"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. A háttér-címkészlet nevét, adja meg.
4. Adja hozzá az egyes gépek vagy a rendelkezésre állási készlet a háttér-készlethez.

#### <a name="configure-a-probe"></a>A mintavétel

1. A terheléselosztó a alatt **beállítások**, jelölje be **vizsgálat**, és kattintson a **Hozzáadás** vizsgálatok hozzáadásához.

 ![Terheléselosztó "Add mintavételi"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Adja meg a mintavétel a nevét.
3. Válassza ki a **protokoll** a mintavételhez. -Adatbázis érdemes lehet a HTTP-vizsgálatot, hanem egy TCP-Hálózatfigyelővel. Terheléselosztó mintavételt kapcsolatos további tudnivalókért lásd [megértése load balancer mintavételt](../load-balancer/load-balancer-custom-probe-overview.md).
4. Adja meg a **Port** az adatbázis eléréséhez a mintavétel használható.
5. A **időköz**, adja meg, hogy milyen gyakran mintavételi az alkalmazást.
6. A **sérült küszöbérték**, adja meg a folyamatos mintavételi előforduló hibákat kell a háttér-virtuális gép számára megfelelő állapotúnak számít.
7. Kattintson a **OK** a mintavétel létrehozása.

#### <a name="configure-the-load-balancing-rules"></a>A terheléselosztási szabályok konfigurálása

1. A **beállítások** válassza ki a terheléselosztó **terheléselosztási szabályok**, és kattintson a **Hozzáadás** olyan szabály létrehozására.
2. Adja meg a **neve** a terheléselosztó szabályhoz.
3. Válassza ki a **előtérbeli IP-cím** a terheléselosztó **protokoll**, és **Port**.
4. A **háttérportot**, adja meg a háttér-készlet használandó portot.
5. Válassza ki a **háttérkészlet** és a **mintavételi** alkalmazni a szabályt, hogy az előző lépésben hozott létre.
6. A **munkamenet megőrzését**, válassza ki, hogyan szeretné megőrizni a munkameneteket.
7. A **az üresjárati időkorlát**, adja meg percben, mielőtt üresjárati időkorlátot.
8. A **fix IP-Címek**, válassza **letiltott** vagy **engedélyezve**.
9. A szabály létrehozásához kattintson az **OK** gombra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>5. lépés: Webes rétegbeli virtuális gépek csatlakozni a terheléselosztó

Most azt konfigurálja az IP-cím és a terheléselosztó előtér-port az adatbázis-kapcsolat a webalkalmazás-réteg virtuális gépeken futó alkalmazások. Ezen beállítása csak virtuális gépeken futó alkalmazások. A cél IP-cím és port konfigurálásához tekintse meg az alkalmazás dokumentációját. Az előtér IP-címét az Azure portálon megkereséséhez nyissa meg az előtér-IP-címkészletbe a **terheléselosztó beállításai**.

![Terheléselosztó "Előtér-IP-készlet" navigációs ablaktábla betöltése](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>További lépések

* [A Traffic Manager áttekintése](traffic-manager-overview.md)
* [Átjáró – áttekintés](../application-gateway/application-gateway-introduction.md)
* [Az Azure Load Balancer áttekintése](../load-balancer/load-balancer-overview.md)
