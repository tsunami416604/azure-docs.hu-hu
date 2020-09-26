---
title: Csatlakozáskezelő előzetes verziójának létrehozása – Azure Portal
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan hozhatja létre a kapcsolódási figyelőt (előzetes verzió) a Azure Portal használatával.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: b783a1434ebf6f8e1ad645d69f9f54a4c9c03e7f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362922"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Csatlakozáskezelő (előzetes verzió) létrehozása a Azure Portal használatával

Megtudhatja, hogyan hozhat létre kapcsolati figyelőt (előzetes verzió) az erőforrások közötti kommunikáció figyeléséhez a Azure Portal használatával. Támogatja a hibrid és az Azure-beli Felhőbeli üzembe helyezést.

## <a name="before-you-begin"></a>Előkészületek 

A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott kapcsolódó figyelőkben a helyszíni és az Azure-beli virtuális gépeket is felveheti forrásként. Ezek a kapcsolati figyelők a végpontokhoz való kapcsolódás figyelésére is használhatók. A végpontok lehetnek az Azure-ban vagy bármely más URL-címen vagy IP-címen.

A Csatlakozáskezelő (előzetes verzió) a következő entitásokat tartalmazza:


* **Csatlakozáskezelő erőforrás** – régióra jellemző Azure-erőforrás. A következő entitások mindegyike egy Csatlakozáskezelő erőforrás tulajdonságait képezi.
* **Endpoint (végpont** ) – a kapcsolati ellenőrzésekben részt vevő forrás vagy cél. Példák a végpontokra 
    * Azure-beli virtuális gépek
    * Azure-virtuális hálózatok
    * Azure-alhálózatok
    * Helyszíni ügynökök
    * Helyszíni alhálózatok
    * Több alhálózatból álló helyszíni egyéni hálózat
    * URL-címek és IP-címek 
* **Teszt konfiguráció** – egy adott protokollra jellemző konfiguráció. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztelési gyakoriságot és az egyéb paramétereket.
* **Test Group (tesztelési csoport** ) – a forrás-végpontokat, a célként megadott végpontokat és a tesztelési konfigurációkat tartalmazó csoport. Egy Csatlakozáskezelő több tesztelési csoportot is tartalmazhat.
* **Test (teszt** ) – a forrás-végpont, a cél végpont és a tesztelési konfiguráció kombinációja. A teszt a legrészletesebben használt szint, amelyen a figyelési adatokat elérhetővé kell tennie. A figyelési adatmennyiség magában foglalja a sikertelen ellenőrzések százalékos arányát és az oda-és visszaút idejét (RTT).

    ![A kapcsolati figyelőt bemutató diagram, a tesztelési csoportok és tesztek közötti kapcsolat meghatározása](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>Létrehozandó lépések

Az alábbi lépéseket követve hozzon létre egy (előzetes verziójú) kapcsolódási figyelőt a Azure Portal használatával:

1. A Azure Portal kezdőlapján válassza a **Network Watcher**lehetőséget.
1. A bal oldalon a **figyelés** szakaszban válassza a **Csatlakozáskezelő (előzetes verzió)** lehetőséget.
1. Megjelenik az összes, a Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott figyelő. A kapcsolódási figyelő klasszikus felhasználói felületén létrehozott kapcsolódási figyelők megtekintéséhez lépjen a **Csatlakozáskezelő** lapra.

    ![A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott kapcsolatok figyelőit bemutató képernyőfelvétel](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. A **Csatlakozáskezelő (előzetes verzió)** irányítópultján, a bal felső sarokban kattintson a **Létrehozás**elemre.

   ![A Csatlakozáskezelő alapjai lapját ábrázoló képernyőfelvétel](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Az **alapvető beállítások** lapon adja meg a Csatlakozáskezelő adatait:
   * **Csatlakozáskezelő neve** – adja hozzá a Csatlakozáskezelő nevét. Használja az Azure-erőforrások szabványos elnevezési szabályait.
   * **Előfizetés** – válasszon egy előfizetést a kapcsolatok figyelője számára.
   * **Régió** – válasszon régiót a kapcsolatok figyelője számára. Csak az ebben a régióban létrehozott forrás virtuális gépeket lehet kiválasztani.
   * **Munkaterület konfigurációja** – a munkaterület tárolja a figyelési adatait. Használhat egyéni munkaterületet vagy az alapértelmezett munkaterületet is. 
       * Az alapértelmezett munkaterület használatához jelölje be a jelölőnégyzetet. 
       * Egyéni munkaterület kiválasztásához törölje a jelet a jelölőnégyzetből. Ezután válassza ki az előfizetést és a régiót az egyéni munkaterülethez. 
1. A lap alján kattintson a Next ( **Tovább) gombra: csoportok tesztelése**.

   ![Képernyőfelvétel: a test Group létrehozása lap a Csatlakozáskezelőben](./media/connection-monitor-2-preview/create-tg.png)

1. Adja hozzá a tesztelési csoportok forrásait, célhelyeit és tesztelési konfigurációit. A tesztelési csoportok beállításával kapcsolatban lásd: [tesztkörnyezet létrehozása a kapcsolódási figyelőben](#create-test-groups-in-a-connection-monitor). 
1. A lap alján kattintson a Next ( **Tovább) gombra: riasztások létrehozása**.

   ![A riasztásokat létrehozó ablaktáblát ábrázoló képernyőfelvétel](./media/connection-monitor-2-preview/create-alert.png)

1. Riasztások létrehozásával kapcsolatban lásd: [riasztások létrehozása a kapcsolódási figyelőben](#create-alerts-in-connection-monitor)
1. A lap alján válassza a **Tovább: felülvizsgálat + létrehozás**elemet.

  ![Képernyőkép a Csatlakozáskezelőról, amely a felülvizsgálat + létrehozás lapot mutatja](./media/connection-monitor-2-preview/review-create-cm.png)

1. A **felülvizsgálat + létrehozás** lapon tekintse át az alapszintű információkat és a tesztelési csoportokat a Csatlakozáskezelő létrehozása előtt. Ha módosítania kell a kapcsolódási figyelőt:
   * Az alapszintű részletek szerkesztéséhez válassza a ceruza ikont.
   * Egy tesztelési csoport szerkesztéséhez válassza ki azt.

   > [!NOTE] 
   > A **felülvizsgálat + létrehozás** lapon a Csatlakozáskezelő előzetes verziójának havi díja látható. Jelenleg az **aktuális költség** oszlop nem számít fel díjat. Ha a kapcsolódási figyelő általánosan elérhetővé válik, ebben az oszlopban a havi díj jelenik meg. 
   > 
   > Még a Csatlakozáskezelő előzetes verziójának szakasza is érvényes, Log Analytics betöltési díjat számítunk fel.

1. Amikor készen áll a kapcsolódási figyelő létrehozására, a **felülvizsgálat + létrehozás** lap alján válassza a **Létrehozás**lehetőséget.

A Csatlakozáskezelő (előzetes verzió) létrehozza a figyelő erőforrást a háttérben.

## <a name="create-test-groups-in-a-connection-monitor"></a>Tesztelési csoportok létrehozása egy figyelőben

A-figyelő minden egyes tesztelési csoportja olyan forrásokat és célhelyeket tartalmaz, amelyek a hálózati paramétereken keresztül teszteltek. A rendszer a sikertelen ellenőrzések százalékos arányát és a RTT.

A Azure Portal a következő mezők értékeinek megadásával hozzon létre egy tesztelési csoportot a kapcsolódási figyelőben:

* **Tesztelési csoport letiltása** – ezt a mezőt kiválasztva letilthatja az összes olyan forrás és cél figyelését, amelyet a tesztelési csoport meghatároz. Ez a beállítás alapértelmezés szerint nincs bejelölve.
* **Név** – a tesztelési csoport neve.
* **Források** – az Azure-beli virtuális gépeket és a helyszíni gépeket forrásként is megadhatja, ha az ügynökök telepítve vannak rajtuk. A forráshoz tartozó ügynök telepítéséhez tekintse meg a [figyelési ügynökök telepítése](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)című témakört.
   * Azure-ügynökök kiválasztásához válassza az **Azure-végpontok** fület. Itt csak a kapcsolódási figyelő létrehozásakor megadott régióhoz kötött virtuális gépek jelennek meg. Alapértelmezés szerint a virtuális gépek a hozzájuk tartozó előfizetésbe vannak csoportosítva. Ezek a csoportok össze vannak csukva. 
   
       Az előfizetések szintjéről a hierarchiában lévő többi szintre is lebonthatja a részletezést:

      **Előfizetés**  >  **Erőforráscsoportok**  >  **Virtuális hálózatok**  >  **Alhálózatok**  >  **Ügynökökkel rendelkező virtuális gépek**

      A **Group By** mező értékét úgy is megváltoztathatja, hogy bármely más szintről elindítsa a fát. Ha például a virtuális hálózat alapján csoportosítja a csoportosítást, akkor a hierarchiában található ügynökökkel rendelkező virtuális gépek a **virtuális hálózatok**-  >  **alhálózatokat**  >  **ügynökökkel**látják el.
       Egy VNET, alhálózat vagy egyetlen virtuális gép kiválasztja a megfelelő erőforrás-azonosítót végpontként. Alapértelmezés szerint a kijelölt VNET vagy alhálózatban lévő összes virtuális gép Network Watcher bővítménnyel részt vesz a figyelésben. A hatókör csökkentése érdekében válassza az egyes alhálózatok/ügynökök lehetőséget, vagy módosítsa a hatókör tulajdonság értékét. 

      ![Képernyőfelvétel a kapcsolatkezelő szolgáltatásról, amely a források hozzáadása panelt és az Azure Agents fület jeleníti meg](./media/connection-monitor-2-preview/add-azure-sources.png)

   * A helyszíni ügynökök kiválasztásához válassza a **nem – Azure ügynökök** lapot. Alapértelmezés szerint az ügynökök régiónként vannak csoportosítva. Mindegyik munkaterülethez konfigurálva van a Network Performance Monitor-megoldás. 
   
       Ha Network Performance Monitort kell hozzáadnia a munkaterülethez, szerezze be azt az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)-ről. A Network Performance Monitor hozzáadásával kapcsolatos további információkért lásd: [monitorozási megoldások a Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       A **Csatlakozáskezelő létrehozása** nézetben az **alapok** lapon az alapértelmezett régió van kiválasztva. Ha megváltoztatja a régiót, az ügynököket az új régióban lévő munkaterületekről is kiválaszthatja.  Kiválaszthat egy vagy több ügynököt vagy alhálózatot. Az alhálózat nézetben kiválaszthatja a figyeléshez megadott IP-címeket. Több alhálózat hozzáadásával egyéni helyszíni hálózat jön létre, nevezetesen: "OnPremises_Network_1". A **Group By** mező értékét is módosíthatja alhálózatok szerint csoportosítva.

      ![Képernyőfelvétel a kapcsolatkezelő szolgáltatásról, amely a források hozzáadása panelt és a nem Azure-ügynökök lapot mutatja](./media/connection-monitor-2-preview/add-non-azure-sources.png)

   * A **legutóbbi végpont** lapon is kiválaszthatja a legutóbb használt végpontokat. 
   
   * Ha befejezte a források beállítását, válassza a **kész**lehetőséget. Az alapszintű tulajdonságokat, például a végpont nevét továbbra is szerkesztheti, ha a végpontra kattint a tesztelési csoport létrehozása nézetben. 

* **Célhelyek** – az Azure-beli virtuális gépekhez, a helyszíni számítógépekhez vagy bármely végponthoz (nyilvános IP-címhez, URL-címekhez vagy teljes tartománynévhez) való csatlakozást a célhelyként való megadásával figyelheti. Egyetlen tesztelési csoportban Azure-beli virtuális gépeket, helyszíni számítógépeket, Office 365 URL-címeket, Dynamics 365 URL-eket és egyéni végpontokat adhat hozzá.

    * Az Azure-beli virtuális gépek célhelyként való kiválasztásához válassza az **Azure-végpontok** fület. Alapértelmezés szerint az Azure-beli virtuális gépek olyan előfizetési hierarchiába vannak csoportosítva, amely ugyanabban a régióban található, mint amelyet a **kapcsolatkezelő létrehozása** nézetben kiválasztott, az **alapok** lapon. Módosíthatja a régiót, és az újonnan kiválasztott régióból is kiválaszthatja az Azure-beli virtuális gépeket. Ezután az előfizetések szintjéről a hierarchia más szintjeire is kibonthat, ugyanúgy, mint a forrás Azure-végpontokhoz.
     A forrás Azure-végpontokhoz hasonló virtuális hálózatok, alhálózatokat vagy önálló virtuális gépeket választhat ki. Egy VNET, alhálózat vagy egyetlen virtuális gép kiválasztja a megfelelő erőforrás-azonosítót végpontként. Alapértelmezés szerint a kijelölt VNET vagy alhálózatban lévő összes virtuális gép Network Watcher bővítménnyel részt vesz a figyelésben. A hatókör csökkentése érdekében válassza az egyes alhálózatok/ügynökök lehetőséget, vagy módosítsa a hatókör tulajdonság értékét. 

       ![Képernyőkép a célhelyek hozzáadása panelről, amely az Azure-beli virtuális gépek lapot mutatja](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Képernyőkép a célhelyek hozzáadása panelről, amely az előfizetés szintjét mutatja](./media/connection-monitor-2-preview/add-azure-dests2.png)
    
    * Ha nem Azure-ügynököket szeretne kiválasztani célhelyként, válassza a nem Azure-beli végpontok fület. Alapértelmezés szerint az ügynökök régiónként vannak csoportosítva. Mindegyik munkaterülethez konfigurálva van a Network Performance Monitor-megoldás. Ha Network Performance Monitort kell hozzáadnia a munkaterülethez, szerezze be azt az Azure Marketplace-ről. A Network Performance Monitor hozzáadásával kapcsolatos további információkért lásd: monitorozási megoldások a Azure Monitor. A Csatlakozáskezelő létrehozása nézetben az alapok lapon az alapértelmezett régió van kiválasztva. Ha megváltoztatja a régiót, az ügynököket az új régióban lévő munkaterületekről is kiválaszthatja. Kiválaszthat egy vagy több ügynököt vagy alhálózatot. Az alhálózat nézetben kiválaszthatja a figyeléshez megadott IP-címeket. Több alhálózat hozzáadásával egyéni helyszíni hálózat jön létre, nevezetesen: "OnPremises_Network_1".  
    
     ![Képernyőkép a nem Azure-beli célhelyek hozzáadása panelről](./media/connection-monitor-2-preview/add-non-azure-dest.png)
    
    * A nyilvános végpontok célhelyként való kiválasztásához válassza a **külső címek** lapot. A végpontok listája az Office 365 test URL-címeit és a Dynamics 365 teszt URL-címeket tartalmazza, név szerint csoportosítva. Ezen végpontok mellett olyan végpontot is kiválaszthat, amelyet más tesztelési csoportokban hoztak létre ugyanabban a kapcsolódási figyelőben. 
    
        Új végpont hozzáadásához kattintson a jobb felső sarokban található **+ végpontok**elemre. Ezután adja meg a végpont nevét és URL-címét, az IP-címet vagy a teljes tartománynevet.

       ![Képernyőfelvétel: a nyilvános végpontok célhelyként való hozzáadása a kapcsolódási figyelőben](./media/connection-monitor-2-preview/add-endpoints.png)

    * A legutóbb használt végpontok kiválasztásához lépjen a **legutóbbi végpontok**   lapra.
    * Amikor befejezte a célhelyek kiválasztását, válassza a **kész**lehetőséget. Az alapszintű tulajdonságokat, például a végpont nevét továbbra is szerkesztheti, ha a végpontra kattint a tesztelési csoport létrehozása nézetben. 

* **Konfiguráció tesztelése** – egy vagy több tesztelési konfigurációt is hozzárendelhet egy tesztelési csoportban. Hozzon létre egy új teszt-konfigurációt az "új konfiguráció" lapon, vagy használjon más tesztelési csoportokban használt tesztelési konfigurációt a "meglévő kiválasztása" lapon.

    * **Név** – a teszt konfigurációjának neve.
    * **Protokoll** – válassza a TCP, az ICMP vagy a http lehetőséget. A HTTP HTTPS-re való módosításához válassza a **http** protokollt, és válassza a **443** portot.
        * **Hálózati teszt konfigurációjának létrehozása** – ez a jelölőnégyzet csak akkor jelenik meg, ha a **protokoll** mezőben a **http** lehetőséget választja. Jelölje be ezt a jelölőnégyzetet, ha olyan tesztelési konfigurációt szeretne létrehozni, amely ugyanazokat a forrásokat és célhelyeket használja, amelyeket a konfigurációban máshol adott meg. Az újonnan létrehozott teszt-konfiguráció neve `<the name of your test configuration>_networkTestConfig` .
        * **Traceroute letiltása** – ez a mező akkor érvényes, ha a protokoll TCP vagy ICMP. Jelölje be ezt a jelölőnégyzetet, ha le szeretné állítani, hogy a források felfedjék a topológiát és a hop-by-hop RTT.
    * **Célport** – a mező testreszabható a választott célport használatával.
        * Port figyelése – ez a mező akkor érvényes, ha a protokoll TCP. Jelölje be ezt a jelölőnégyzetet a kiválasztott TCP-port megnyitásához, ha még nincs megnyitva. 
    * **Tesztelési gyakoriság** – ezzel a mezővel kiválaszthatja, hogy a források milyen gyakran fognak pingelni a protokollon és a megadott porton. 30 másodpercet, 1 percet, 5 percet, 15 percet vagy 30 percet is kiválaszthat. Válassza az egyéni lehetőséget, ha 30 másodperc és 30 perc közötti gyakoriságot szeretne megadni. A források a választott érték alapján próbálják meg a célokhoz való kapcsolódást.  Ha például 30 másodpercet választ, a források legalább egyszer ellenőrizhetik a kapcsolatot a célhoz, 30 másodpercen belül.
    * **Sikerességi küszöb** – a következő hálózati paraméterek küszöbértékeit állíthatja be:
       * Az **ellenőrzések sikertelenek** – megadhatja a sikertelen ellenőrzések százalékos arányát, ha a források a megadott feltételek használatával ellenőrzik a csatlakozást a célhelyekhez. A TCP vagy az ICMP protokoll esetében a sikertelen ellenőrzések százalékaránya a csomagok elvesztésének százalékában is egyenlő lehet. HTTP protokoll esetén ez a mező a nem válaszoló HTTP-kérelmek százalékos arányát jelöli.
       * **Oda-** és bekapcsolási idő – állítsa be a RTT ezredmásodpercben, hogy mennyi ideig tart a források kapcsolódása a célhoz a tesztelési konfiguráción keresztül.
    
       ![Képernyőkép a tesztelési konfiguráció beállításáról a kapcsolódási figyelőben](./media/connection-monitor-2-preview/add-test-config.png)
       
## <a name="create-alerts-in-connection-monitor"></a>Riasztások létrehozása a Csatlakozáskezelőben

Riasztásokat állíthat be a tesztelési konfigurációkban beállított küszöbértékek alapján meghiúsuló teszteken.   

A Azure Portal a riasztások létrehozásához a kapcsolódási figyelőben a következő mezők értékeit kell megadnia: 

- Riasztás létrehozása – a mező kiválasztásával metrikai riasztást hozhat létre Azure Monitor. Ezzel a beállítással engedélyezheti a többi mező szerkesztését. A riasztásokra vonatkozó további díjakat a [riasztások díjszabása](https://azure.microsoft.com/pricing/details/monitor/) alapján számítjuk fel 

- Hatókör – > erőforrás és hatókör – > hierarchia – ezt az alapismeretek lapon megadott értékek alapján előre ki kell tölteni. 

- Feltétel – > a riasztást a "teszt eredménye (előzetes verzió)" metrika alapján hozza létre a rendszer. Ha a kapcsolódási figyelő tesztelési eredménye sikertelen, a riasztási szabály elindul. 

- Műveleti csoport – közvetlenül is megadhatja az e-mail-címet, vagy választhatja a riasztások létrehozását a műveleti csoportok használatával. Ha úgy dönt, hogy közvetlenül adja meg az e-mail-címet, a rendszer létrehoz egy NPM e-mail-ActionGroup nevű műveleti csoportot, és hozzáadja az e-mail-azonosítót az adott műveleti csoporthoz. Ha a műveleti csoportok használata mellett dönt, ki kell választania egy korábban létrehozott műveleti csoportot. Itt megtudhatja, hogyan hozhat létre egy műveleti csoportot. A riasztás sikeres létrehozása után használhatja a riasztások kezelése hivatkozást a riasztások kezeléséhez. 

- Riasztási szabály neve – a Csatlakozáskezelő neve 

- Szabály engedélyezése a létrehozás után – ez a feltétel alapján engedélyezi a riasztási szabályt. Tiltsa le ezt, ha létre szeretné hozni a szabályt, de nem engedélyezi azt. 

    ![A riasztás létrehozásával kapcsolatos képernyőkép a figyelő panelen](./media/connection-monitor-2-preview/create-alert-filled.png)

## <a name="scale-limits"></a>Skálázási korlátok

A hálózati figyelők a következő skálázási korlátokkal rendelkeznek:

* Az előfizetések maximális száma régiónként: 100
* Tesztelési csoportok maximális száma kapcsolatonként: 20
* Maximális források és célhelyek kapcsolatonként: 100
* A tesztelési konfigurációk maximális száma kapcsolatonként: 2 a Azure Portalon keresztül

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan elemezheti a figyelési és riasztási információkat](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Útmutató a [hálózatban felmerülő problémák diagnosztizálásához](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
