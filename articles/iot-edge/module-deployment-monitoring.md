---
title: Modulok telepítése az Azure IoT Edge |} A Microsoft Docs
description: Ismerje meg hogyan modulok telepítve, a peremeszközökre
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 754dafc80a435fbb9f2cee080b29d1765ae935e6
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47422470"
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale"></a>Megismerheti az IoT Edge-telepítések egyetlen eszközök vagy ipari méretekben

Hajtsa végre az Azure IoT Edge-eszközök egy [eszközéletciklusra] [ lnk-lifecycle] IoT-eszközök más típusú hasonló:

1. IoT Edge-eszközök kiosztása, amely magában foglalja az operációs rendszer eszköz imaging és telepítése a [IoT Edge-futtatókörnyezet][lnk-runtime].
2. Az eszközök történő futtatásra vannak konfigurálva [IoT Edge-modulok][lnk-modules], és majd Health figyeli. 
3. Végül eszközök eltávolíthatja, ha cserélni vagy elavulttá válnak.  

Az Azure IoT Edge a modulok futtatása az IoT Edge-eszközökön való konfigurálásához kétféle módszert biztosít: egyet a fejlesztési és a egy adott eszközön gyors ismétlésének (Ez a módszer az Azure IoT Edge-ben használt [oktatóanyagok](tutorial-deploy-function.md)), és a egy kezelésére szolgáló nagy flottáknak IoT Edge-eszközökön. Mindkét ezek a módszerek érhetők el az Azure Portalon, és programozott módon. Csoportok és a egy nagy számú célozza, mely eszközöket szeretné telepíteni a modulok használatával lehet megadni [címkék](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor#identify-devices-using-tags) az ikereszközben. Az alábbi lépéseket a központi telepítést, a tags tulajdonságnak keresztül azonosított Washington állam eszközcsoport beszélni. 

Ez a cikk a konfiguráció koncentrál, és figyelési eszközök, flottái szakaszok együttesen a automatikus IoT Edge-telepítések. A telepítési lépések a következők: 

1. Az operátornak határozza meg a központi telepítés a modulokat, valamint a Céleszközök írja le. Minden egyes üzemelő példánynak van a központi telepítési jegyzékfájl, amely tartalmazza ezt az információt. 
2. Az IoT Hub szolgáltatás az összes megcélzott eszköz konfigurálhatja azokat a kívánt modulok kommunikál. 
3. Az IoT Hub szolgáltatás állapota lekéri az IoT Edge-eszközökön, és elérhetővé teszi az üzemeltető figyelése a.  Az operátornak láthatja például, amikor egy Edge-eszköz nem történik meg sikeresen, vagy ha egy modul sikertelen futtatás ideje alatt. 
4. Bármikor új IoT Edge-eszközök, amelyek megfelelnek a célcsoport-kezelési feltételek a központi telepítés vannak konfigurálva. Például a Washington állam minden IoT Edge-eszközök automatikusan célzó központi telepítés egy új IoT Edge-eszköz konfigurálása kiépítve, és hozzáadja a Washington állam eszköz csoporthoz. 
 
Ez a cikk ismerteti az egyes összetevők konfigurálása és figyelése a központi telepítés. Létrehozása és frissítése a központi telepítés leírását lásd: [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok][lnk-howto].

## <a name="deployment"></a>Környezet

Az IoT Edge automatikus központi telepítési lemezképek modul futtatása példány célzott az IoT Edge-eszközök IoT Edge rendeli hozzá. Úgy működik, hogy konfigurálása az IoT Edge manifest nasazení megfelelő inicializálási paramétereknek modulok listáját tartalmazza. Központi telepítés rendelhetők (Eszközazonosító alapján) egy eszközre vagy eszközökre (a címkék alapján). IoT Edge-eszköz megkapja a manifest nasazení, miután, letölti és telepíti a modult tárolórendszerképeket a megfelelő tárolót változásokat, és ennek megfelelően konfigurálja őket. Központi telepítés létrehozása után az operátornak figyelheti a központi telepítési állapotának megtekintéséhez, hogy a megcélzott eszközökön helyesen van-e konfigurálva.

Eszközök kell konfigurálni egy telepítést IoT Edge-eszközökön üzembe helyezhető. A következő előfeltételeknek kell az eszközön, mielőtt megkaphatja a központi telepítés:

* Az operációs rendszer
* A felügyeleti rendszer, például Moby vagy a Docker
* Az IoT Edge-futtatókörnyezet kiépítése 

### <a name="deployment-manifest"></a>Manifest nasazení

A manifest nasazení egy JSON-dokumentum, amely leírja a modulokat az érintett IoT Edge-eszközökön konfigurálható. Az összes modult, beleértve a szükséges rendszer-modulok (kifejezetten az IoT Edge-ügynök és hub az IoT Edge) konfigurációs metaadatok tartalmazza.  

Az egyes modulok konfigurációs metaadatok tartalmaznak: 

* Verzió 
* Típus 
* Állapot (pl. fut vagy Leállítva) 
* Indítsa újra a házirend 
* Kép- és tároló-beállításjegyzék
* Bemeneti és kimeneti adatok útvonalai 

Ha a modul rendszerképet egy privát tárolójegyzékben található, az IoT Edge-ügynök tárolja a tárolójegyzék hitelesítő adatainak. 

### <a name="target-condition"></a>Célfeltétel

A célfeltétel a rendszer folyamatosan értékeli a követelményeknek megfelelő új eszközök, vagy távolítsa el az eszközöket, amelyek többé nem az üzembe helyezés élettartama keresztül. A központi telepítés rendszer aktiválhatók, ha a szolgáltatás észleli a célként megadott feltétel tejes. 

Például rendelkezik, egy-egy cél feltétel tags.environment = 'prod'. Az üzembe helyezés indíthat, ha nincsenek tíz éles eszközt. A modulok telepítése sikeresen megtörtént a tíz ezeket az eszközöket. Összes eszköz 10, 10 sikeres válaszok, 0 meghibásodási válaszok és 0 függőben lévő válaszok formájában jelenik meg az IoT Edge-ügynök állapota. Most már hozzáadhat öt további eszközök tags.environment = 'prod'. A szolgáltatás észleli a változást, és az IoT Edge-ügynök állapota 15 összes eszköz, 10 sikeres válaszok, 0 meghibásodási válaszok és 5 függőben lévő válaszok válik az öt új eszközökre való.

A device twins címkéket vagy deviceId bármely logikai feltétel használatával céleszközeinek kiválasztásához. Ha azt szeretné, a feltétel használata a címkék, hozzá kell "címkék":{} szakasz azokat az ikereszköz az azonos szinten tulajdonságai alapján. [További információ az ikereszközök címkék](../iot-hub/iot-hub-devguide-device-twins.md)

Példák a célként megadott feltétel:

* deviceId = "linuxprod1"
* tags.Environment = 'prod'
* tags.Environment = 'prod' AND tags.location = 'westus'
* tags.Environment = 'prod' vagy tags.location = 'westus'
* tags.Operator = "János" AND tags.environment = 'prod' nem deviceId = "linuxprod1"

Íme néhány korlátozza, amikor egy célfeltétel hozhat létre:

* Ikereszköz a címkék vagy deviceId célfeltétel csak készíthet.
* A célfeltétel bármely részének dupla idézőjelek között nem engedélyezett. Használjon szimpla idézőjelek között.
* Szimpla idézőjelek között a célfeltétel értékeit jelölik. Ezért egy másik egyetlen ajánlattal a szimpla idézőjel kell elkerülésére, ha az eszköz neve része. Például a célfeltétel: operator'sDevice kellene mert deviceId = "operátor" % sDevice ".
* Számok, betűk és a következő karakterek engedélyezettek, a cél feltételértékeket tartalmaznak: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioritás

Prioritás határozza meg, akár egy központi telepítési egy céleszköz képest más központi kell alkalmazni. Üzembe helyezési prioritás pozitív egész szám, a nagyobb számot jelzi, hogy a magasabb prioritású. Ha több üzemelő IoT Edge-eszköz vonatkozik, az üzembe helyezés a legmagasabb prioritású vonatkozik.  Alacsonyabb prioritású üzemelő példányok nem történik meg, és nem azok egyesítésekor.  Ha egy eszköz két vagy több üzemelő példány az azonos prioritású vonatkozik, a legutóbb létrehozott központi telepítés (határozza meg a létrehozás időbélyegzője) vonatkozik rá.

### <a name="labels"></a>Címkék 

A címkékhez karakterlánc kulcs/érték párok, amellyel szűrőt és központi telepítések csoportja. Előfordulhat, hogy a központi telepítés több. Címkék megadása nem kötelező, és nincs hatással a tényleges konfiguráció az IoT Edge-eszközök beállítása. 

### <a name="deployment-status"></a>Üzembe helyezés állapota

Központi telepítés meghatározásához, hogy életbe léptette-e sikeresen bármely célzott IoT Edge-eszköz figyelhető.  A peremhálózati eszköz egy vagy több következő állapot kategória jelenik meg: 

* **Cél** az IoT Edge-eszközöket, amelyek megfelelnek a feltétel célzó központi mutatja.
* **Tényleges** jeleníti meg a célzott IoT Edge, amely nem a nagyobb prioritású egy másik telepítés által célzott eszközök.
* **Kifogástalan állapotú** jeleníti meg az IoT Edge-ben, jelentettek vissza a szolgáltatásnak, hogy a modulok sikeresen telepítették-e. 
* **Nem megfelelő állapotú** jeleníti meg az IoT Edge eszközöket jelentett vissza a szolgáltatásnak, hogy az egyik vagy a modulok nem telepített sikeresen megtörtént. További vizsgálat céljából a hiba, távolról csatlakozhat, amellyel az eszközöket, és naplófájljainak megtekintéséhez.
* **Ismeretlen** az IoT Edge eszközöket, amelyek nem jelentettek bármilyen ehhez a központi telepítéshez tartozó állapot látható. További vizsgálat céljából, megtekintheti az adatok és a napló fájljai.

## <a name="phased-rollout"></a>Fázisokra bontva történő bevezetéséhez 

Egy fázisokra bontva történő bevezetéséhez egy általános eljárásait, amellyel az operátornak telepíti a módosítások egy IoT Edge-eszközök kiterjesszük készletét. A célja az, hogy a parancsban történt használhatatlanná tévő széles skálán kockázatának csökkentése érdekében fokozatosan módosítsa.  

Több lépcsőben vezeti be a következő fázisra és lépéseket hajtja végre: 

1. Hozzon létre egy tesztkörnyezetben az IoT Edge-eszközök kiépítési őket, és például ikereszköz eszközcímke beállítás `tag.environment='test'`. A tesztelési környezetben kell tükrözik az éles környezetben a központi telepítési idővel célközönsége. 
2. Beleértve a kívánt modulok és konfigurációk központi telepítés létrehozása. A célcsoport-kezelési feltétel a teszt IoT Edge-eszköz környezetre irányul.   
3. Ellenőrizze a tesztelési környezetben új modul konfigurációját.
4. A frissítéstelepítés, hogy egy új címke hozzáadása a célcsoport-kezelési feltétel éles IoT Edge-eszközök egy részhalmazát tartalmazzák. Emellett gondoskodjon arról, hogy a prioritás a központi telepítés magasabb, mint más jelenleg célzott az adott eszközökre való központi telepítések 
5. Győződjön meg arról, hogy az üzembe helyezés sikeres volt-e a megcélzott IoT-eszközökön a központi telepítés állapotának megtekintése.
6. A telepítés összes többi éles IoT Edge-eszközök frissítéséhez.

## <a name="rollback"></a>Visszaállítás

Üzemelő példányok is vissza lesz állítva a hibák vagy konfigurációs hibák esetén.  Központi telepítés határozza meg az IoT Edge-eszköz abszolút modul konfigurációját, mert egy további üzembe helyezési is verziókkal kell működnie ugyanarra az eszközre alacsonyabb prioritással akkor is, ha a cél, hogy távolítsa el az összes modult.  

Visszagörgetése hajtsa végre a következő lépéseket: 

1. Győződjön meg arról, hogy egy második üzemelő példány is irányuló eszköz ugyanezek. A visszaállítás az a célja, hogy távolítsa el az összes modult, ha a második üzembe helyezést nem tartalmaznia kell minden olyan modulokat. 
2. Módosítsa vagy távolítsa el az üzemelő példány vissza úgy, hogy az eszközök már nem felelnek meg célcsoport-kezelési visszaállítás kívánt cél feltételkifejezés.
3. Győződjön meg arról, hogy a visszaállítás sikeres volt-e a központi telepítés állapotának megtekintése.
   * A helyzetben előzőre telepítési állapotúnak kell lennie már nem az eszközökhöz, amelyek vissza lett állítva.
   * A második üzembe helyezést most már tartalmazza az eszközök, amelyeket vissza lett állítva a telepítés állapota.


## <a name="next-steps"></a>További lépések

* Lépésről lépésre bemutatjuk létrehozása, frissítése vagy törlése a központi telepítés [üzembe helyezése és figyelése a nagy mennyiségű IoT Edge-modulok][lnk-howto].
* További információ az IoT Edge más fogalmakat, mint a [IoT Edge-futtatókörnyezet] [ lnk-runtime] és [IoT Edge-modulok][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md
