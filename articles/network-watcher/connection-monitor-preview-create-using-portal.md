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
ms.openlocfilehash: 4e7067e537ce8fb6faf82198f7863957f79ffb22
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567928"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Csatlakozáskezelő (előzetes verzió) létrehozása a Azure Portal használatával

Megtudhatja, hogyan hozhat létre kapcsolati figyelőt (előzetes verzió) az erőforrások közötti kommunikáció figyeléséhez a Azure Portal használatával. Támogatja a hibrid és az Azure-beli Felhőbeli üzembe helyezést.

## <a name="before-you-begin"></a>Előkészületek 

A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott kapcsolódó figyelőkben a helyszíni és az Azure-beli virtuális gépeket is felveheti forrásként. Ezek a kapcsolati figyelők a végpontokhoz való kapcsolódás figyelésére is használhatók. A végpontok lehetnek az Azure-ban vagy bármely más URL-címen vagy IP-címen.

A Csatlakozáskezelő (előzetes verzió) a következő entitásokat tartalmazza:


* **Csatlakozáskezelő erőforrás** – régióra jellemző Azure-erőforrás. A következő entitások mindegyike egy Csatlakozáskezelő erőforrás tulajdonságait képezi.
* **Endpoint (végpont** ) – a kapcsolati ellenőrzésekben részt vevő forrás vagy cél. Ilyen végpontok például az Azure-beli virtuális gépek, a helyszíni ügynökök, az URL-címek és az IP-címek.
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
1. Az **alapvető beállítások** lapon adja meg a Csatlakozáskezelő adatait:
   * **Csatlakozáskezelő neve** – adja hozzá a Csatlakozáskezelő nevét. Használja az Azure-erőforrások szabványos elnevezési szabályait.
   * **Előfizetés** – válasszon egy előfizetést a kapcsolatok figyelője számára.
   * **Régió** – válasszon régiót a kapcsolatok figyelője számára. Csak az ebben a régióban létrehozott forrás virtuális gépeket lehet kiválasztani.
   * **Munkaterület konfigurációja** – a munkaterület tárolja a figyelési adatait. Használhat egyéni munkaterületet vagy az alapértelmezett munkaterületet is. 
       * Az alapértelmezett munkaterület használatához jelölje be a jelölőnégyzetet. 
       * Egyéni munkaterület kiválasztásához törölje a jelet a jelölőnégyzetből. Ezután válassza ki az előfizetést és a régiót az egyéni munkaterülethez. 
1. A lap alján kattintson a Next ( **Tovább) gombra: csoportok tesztelése**.

   ![A Csatlakozáskezelő alapjai lapját ábrázoló képernyőfelvétel](./media/connection-monitor-2-preview/create-cm-basics.png)

1. A **test groups (tesztelési csoportok** ) lapon válassza a **+ teszt csoport**elemet. A tesztelési csoportok beállításával kapcsolatban lásd: [tesztkörnyezet létrehozása a kapcsolódási figyelőben](#create-test-groups-in-a-connection-monitor). 
1. A lap alján válassza a **Tovább: Áttekintés + létrehozás** elemet a kapcsolódási figyelő áttekintéséhez.

   ![A test groups (tesztelési csoportok) lapot és a tesztelési csoport részleteit tartalmazó panelt ábrázoló képernyőfelvétel](./media/connection-monitor-2-preview/create-tg.png)

1. A **felülvizsgálat + létrehozás** lapon tekintse át az alapszintű információkat és a tesztelési csoportokat a Csatlakozáskezelő létrehozása előtt. Ha módosítania kell a kapcsolódási figyelőt:
   * Az alapszintű részletek szerkesztéséhez válassza a ceruza ikont.
   * Egy tesztelési csoport szerkesztéséhez válassza ki azt.

   > [!NOTE] 
   > A **felülvizsgálat + létrehozás** lapon a Csatlakozáskezelő előzetes verziójának havi díja látható. Jelenleg az **aktuális költség** oszlop nem számít fel díjat. Ha a kapcsolódási figyelő általánosan elérhetővé válik, ebben az oszlopban a havi díj jelenik meg. 
   > 
   > Még a Csatlakozáskezelő előzetes verziójának szakasza is érvényes, Log Analytics betöltési díjat számítunk fel.

1. Amikor készen áll a kapcsolódási figyelő létrehozására, a **felülvizsgálat + létrehozás** lap alján válassza a **Létrehozás**lehetőséget.

   ![Képernyőkép a Csatlakozáskezelőról, amely a felülvizsgálat + létrehozás lapot mutatja](./media/connection-monitor-2-preview/review-create-cm.png)

A Csatlakozáskezelő (előzetes verzió) létrehozza a figyelő erőforrást a háttérben.

## <a name="create-test-groups-in-a-connection-monitor"></a>Tesztelési csoportok létrehozása egy figyelőben

A-figyelő minden egyes tesztelési csoportja olyan forrásokat és célhelyeket tartalmaz, amelyek a hálózati paramétereken keresztül teszteltek. A rendszer a sikertelen ellenőrzések százalékos arányát és a RTT.

A Azure Portal a következő mezők értékeinek megadásával hozzon létre egy tesztelési csoportot a kapcsolódási figyelőben:

* **Tesztelési csoport letiltása** – ezt a mezőt kiválasztva letilthatja az összes olyan forrás és cél figyelését, amelyet a tesztelési csoport meghatároz. Ez a beállítás alapértelmezés szerint nincs bejelölve.
* **Név** – a tesztelési csoport neve.
* **Források** – az Azure-beli virtuális gépeket és a helyszíni gépeket forrásként is megadhatja, ha az ügynökök telepítve vannak rajtuk. A forráshoz tartozó ügynök telepítéséhez tekintse meg a [figyelési ügynökök telepítése](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)című témakört.
   * Az Azure-ügynökök kiválasztásához válassza az **Azure-ügynökök** fület. Itt csak a kapcsolódási figyelő létrehozásakor megadott régióhoz kötött virtuális gépek jelennek meg. Alapértelmezés szerint a virtuális gépek a hozzájuk tartozó előfizetésbe vannak csoportosítva. Ezek a csoportok össze vannak csukva. 
   
       Az előfizetések szintjéről a hierarchiában lévő többi szintre is lebonthatja a részletezést:

      **Előfizetés**  >  **Erőforráscsoportok**  >  **Virtuális hálózatok**  >  **Alhálózatok**  >  **Ügynökökkel rendelkező virtuális gépek**

      A **Group By** mező értékét úgy is megváltoztathatja, hogy bármely más szintről elindítsa a fát. Ha például a virtuális hálózat alapján csoportosítja a csoportosítást, akkor a hierarchiában található ügynökökkel rendelkező virtuális gépek a **virtuális hálózatok**-  >  **alhálózatokat**  >  **ügynökökkel**látják el.

      ![Képernyőfelvétel a kapcsolatkezelő szolgáltatásról, amely a források hozzáadása panelt és az Azure Agents fület jeleníti meg](./media/connection-monitor-2-preview/add-azure-sources.png)

   * A helyszíni ügynökök kiválasztásához válassza a **nem – Azure ügynökök** lapot. Alapértelmezés szerint az ügynökök régiónként vannak csoportosítva. Mindegyik munkaterülethez konfigurálva van a Network Performance Monitor-megoldás. 
   
       Ha Network Performance Monitort kell hozzáadnia a munkaterülethez, szerezze be azt az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)-ről. A Network Performance Monitor hozzáadásával kapcsolatos további információkért lásd: [monitorozási megoldások a Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       A **Csatlakozáskezelő létrehozása** nézetben az **alapok** lapon az alapértelmezett régió van kiválasztva. Ha megváltoztatja a régiót, az ügynököket az új régióban lévő munkaterületekről is kiválaszthatja. A **Group By** mező értékét is módosíthatja alhálózatok szerint csoportosítva.

      ![Képernyőfelvétel a kapcsolatkezelő szolgáltatásról, amely a források hozzáadása panelt és a nem Azure-ügynökök lapot mutatja](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * A kiválasztott Azure-és nem Azure-ügynökök áttekintéséhez lépjen a **felülvizsgálat** lapra.

      ![Képernyőfelvétel a kapcsolatok Figyelőről, a források hozzáadása panel és a felülvizsgálat lap](./media/connection-monitor-2-preview/review-sources.png)

   * Amikor befejezte a források beállítását, a **források hozzáadása** panel alján válassza a **kész**lehetőséget.

* **Célhelyek** – az Azure-beli virtuális gépekkel vagy bármely végponttal (nyilvános IP-címmel, URL-lel vagy FQDN-vel) való kapcsolat figyeléséhez megadhatja őket célhelyként. Egyetlen tesztelési csoportban Azure-beli virtuális gépeket, Office 365 URL-címeket, Dynamics 365 URL-címeket és egyéni végpontokat adhat hozzá.

    * Az Azure-beli virtuális gépek célhelyként való kiválasztásához válassza az Azure-beli **virtuális gépek** fület. Alapértelmezés szerint az Azure-beli virtuális gépek olyan előfizetési hierarchiába vannak csoportosítva, amely ugyanabban a régióban található, mint amelyet a **kapcsolatkezelő létrehozása** nézetben kiválasztott, az **alapok** lapon. Módosíthatja a régiót, és az újonnan kiválasztott régióból is kiválaszthatja az Azure-beli virtuális gépeket. Ezután részletezheti az előfizetések szintjét a hierarchia más szintjeire, például az Azure-ügynökök szintjére.

       ![Képernyőkép a célhelyek hozzáadása panelről, amely az Azure-beli virtuális gépek lapot mutatja](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Képernyőkép a célhelyek hozzáadása panelről, amely az előfizetés szintjét mutatja](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * A végpontok célhelyként való kiválasztásához válassza a **végpontok** fület. A végpontok listája az Office 365 test URL-címeit és a Dynamics 365 teszt URL-címeket tartalmazza, név szerint csoportosítva. Ezen végpontok mellett olyan végpontot is kiválaszthat, amelyet más tesztelési csoportokban hoztak létre ugyanabban a kapcsolódási figyelőben. 
    
        Új végpont hozzáadásához kattintson a jobb felső sarokban található **+ végpontok**elemre. Ezután adja meg a végpont nevét és URL-címét, az IP-címet vagy a teljes tartománynevet.

       ![Képernyőfelvétel: a végpontok célhelyként való hozzáadásának helye a kapcsolódási figyelőben](./media/connection-monitor-2-preview/add-endpoints.png)

    * A kiválasztott Azure-beli virtuális gépek és végpontok áttekintéséhez válassza a **felülvizsgálat** lapot.
    * Amikor befejezte a célhelyek kiválasztását, válassza a **kész**lehetőséget.

* **Tesztelési konfigurációk** – tesztelési konfigurációkat rendelhet a tesztelési csoportokhoz. A Azure Portal tesztelési csoportonként csak egy tesztelési konfigurációt engedélyez, de a ARMClient használatával további műveleteket is felvehet.

    * **Név** – a teszt konfigurációjának neve.
    * **Protokoll** – válassza a TCP, az ICMP vagy a http lehetőséget. A HTTP HTTPS-re való módosításához válassza a **http** protokollt, és válassza a **443** portot.
        * **Hálózati teszt konfigurációjának létrehozása** – ez a jelölőnégyzet csak akkor jelenik meg, ha a **protokoll** mezőben a **http** lehetőséget választja. Jelölje be ezt a jelölőnégyzetet, ha olyan tesztelési konfigurációt szeretne létrehozni, amely ugyanazokat a forrásokat és célhelyeket használja, amelyeket a konfigurációban máshol adott meg. Az újonnan létrehozott teszt-konfiguráció neve `<the name of your test configuration>_networkTestConfig` .
        * **Traceroute letiltása** – ez a mező olyan tesztelési csoportokra vonatkozik, amelyek TCP vagy ICMP protokollal rendelkeznek. Jelölje be ezt a jelölőnégyzetet, ha le szeretné állítani, hogy a források felfedjék a topológiát és a hop-by-hop RTT.
    * **Célport** – a mező testreszabható a választott célport használatával.
    * **Tesztelési gyakoriság** – ezzel a mezővel kiválaszthatja, hogy a források milyen gyakran fognak pingelni a protokollon és a megadott porton. 30 másodpercet, 1 percet, 5 percet, 15 percet vagy 30 percet is kiválaszthat. A források a választott érték alapján próbálják meg a célokhoz való kapcsolódást.  Ha például 30 másodpercet választ, a források legalább egyszer ellenőrizhetik a kapcsolatot a célhoz, 30 másodpercen belül.
    * **Sikerességi küszöb** – a következő hálózati paraméterek küszöbértékeit állíthatja be:
       * Az **ellenőrzések sikertelenek** – megadhatja a sikertelen ellenőrzések százalékos arányát, ha a források a megadott feltételek használatával ellenőrzik a csatlakozást a célhelyekhez. A TCP vagy az ICMP protokoll esetében a sikertelen ellenőrzések százalékaránya a csomagok elvesztésének százalékában is egyenlő lehet. HTTP protokoll esetén ez a mező a nem válaszoló HTTP-kérelmek százalékos arányát jelöli.
       * **Oda-** és bekapcsolási idő – állítsa be a RTT ezredmásodpercben, hogy mennyi ideig tart a források kapcsolódása a célhoz a tesztelési konfiguráción keresztül.
    
       ![Képernyőkép a tesztelési konfiguráció beállításáról a kapcsolódási figyelőben](./media/connection-monitor-2-preview/add-test-config.png)


## <a name="scale-limits"></a>Skálázási korlátok

A hálózati figyelők a következő skálázási korlátokkal rendelkeznek:

* Az előfizetések maximális száma régiónként: 100
* Tesztelési csoportok maximális száma kapcsolatonként: 20
* Maximális források és célhelyek kapcsolatonként: 100
* A tesztelési konfigurációk maximális száma kapcsolatonként: 2 a Azure Portalon keresztül

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan elemezheti a figyelési és riasztási információkat](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Útmutató a [hálózatban felmerülő problémák diagnosztizálásához](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
