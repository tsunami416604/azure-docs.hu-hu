---
title: Automatikus központi telepítési eszközcsoportok – Azure IoT Edge |} A Microsoft Docs
description: Az automatikus központi telepítéseket használ az Azure IoT Edge-eszközök megosztott címkék alapján csoportok kezelése
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb45f2b929c08ce77c83af450726a00dd6af458e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456730"
---
# <a name="understand-iot-edge-automatic-deployments-for-single-devices-or-at-scale"></a>Az automatikus központi IoT Edge egy eszközök vagy ipari méretekben ismertetése

Az eszközök Azure IoT Edge a más típusú IoT hasonló eszközök [életciklusát](../iot-hub/iot-hub-device-management-overview.md) követik:

1. Új IoT Edge-eszközök kiépítése az operációs rendszer és a [IoT Edge futtatókörnyezet](iot-edge-runtime.md)telepítése révén.
2. Konfigurálja az eszközöket [IoT Edge modulok](iot-edge-modules.md)futtatására, majd figyelje az állapotukat. 
3. Végül vonja ki az eszközt, ha cserélni vagy elavulttá válnak.  

A Azure IoT Edge kétféle módon konfigurálhatja a modulokat IoT Edge eszközökön való futtatásra: egyet a fejlesztéshez és gyors ismétlésekhez egyetlen eszközön (ezt a módszert használta a Azure IoT Edge [oktatóanyagokban](tutorial-deploy-function.md)), és az egyiket a IoT Edge eszközök nagy flottáinak kezeléséhez. Mindkét ezek a módszerek érhetők el az Azure Portalon, és programozott módon. Célcsoportok vagy nagy számú eszköz megadásához megadhatja, hogy mely eszközökön szeretné üzembe helyezni a modulokat [címkék](../iot-edge/how-to-deploy-monitor.md#identify-devices-using-tags) használatával az eszközök Twin-ben. Az alábbi lépéseket a központi telepítést, a tags tulajdonságnak keresztül azonosított Washington állam eszközcsoport beszélni. 

Ez a cikk a konfiguráció koncentrál, és figyelési eszközök, flottái szakaszok együttesen a automatikus IoT Edge-telepítések. Az általános telepítési lépések a következők: 

1. Az operátornak határozza meg a központi telepítés a modulokat, valamint a Céleszközök írja le. Minden központi telepítéshez tartozik egy üzembe helyezési jegyzék, amely tükrözi ezt az információt. 
2. Az IoT Hub szolgáltatás az összes megcélzott eszköz konfigurálhatja azokat a kívánt modulok kommunikál. 
3. Az IoT Hub szolgáltatás állapota lekéri az IoT Edge-eszközök, és elérhetővé teszi azokat az operátor.  Egy operátor például akkor láthatja, ha egy peremhálózati eszköz nincs megfelelően konfigurálva, vagy ha egy modul meghibásodik a futtatókörnyezet során. 
4. Bármikor új IoT Edge-eszközök, amelyek megfelelnek a célcsoport-kezelési feltételek a központi telepítés vannak konfigurálva. Például egy olyan központi telepítés, amely a Washington állam összes IoT Edge eszközét célozza meg, automatikusan konfigurál egy új IoT Edge eszközt, miután kiépítik, és hozzáadja őket a Washington állambeli eszköz csoportjához. 
 
Ez a cikk ismerteti az egyes összetevők konfigurálása és figyelése a központi telepítés. A központi telepítések létrehozásának és frissítésének bemutatóját lásd: [IoT Edge modulok üzembe helyezése és figyelése nagy léptékben](how-to-deploy-monitor.md).

## <a name="deployment"></a>Környezet

Az IoT Edge automatikus központi telepítési lemezképek modul futtatása példány célzott az IoT Edge-eszközök IoT Edge rendeli hozzá. Úgy működik, hogy konfigurálása az IoT Edge manifest nasazení megfelelő inicializálási paramétereknek modulok listáját tartalmazza. A központi telepítés egyetlen eszközhöz (az eszköz azonosítója alapján) vagy eszközök egy csoportjára (címkék alapján) is hozzárendelhető. Miután egy IoT Edge eszköz megkapja az üzembe helyezési jegyzéket, letölti és telepíti a tároló lemezképeit a megfelelő tároló-tárházból, és ennek megfelelően konfigurálja azokat. A központi telepítés létrehozása után az operátor figyelheti a központi telepítés állapotát, és ellenőrizheti, hogy megfelelően vannak-e konfigurálva a meglevő eszközök.

Csak az IoT Edge-eszközökön konfigurálható egy központi telepítést. A következő előfeltételeknek kell az eszközön, mielőtt megkaphatja a központi telepítés:

* Az operációs rendszer
* A felügyeleti rendszer, például Moby vagy a Docker
* Az IoT Edge-futtatókörnyezet kiépítése 

### <a name="deployment-manifest"></a>Üzembehelyezési jegyzék

A manifest nasazení egy JSON-dokumentum, amely leírja a modulokat az érintett IoT Edge-eszközökön konfigurálható. Az összes modult, beleértve a szükséges rendszer-modulok (kifejezetten az IoT Edge-ügynök és hub az IoT Edge) konfigurációs metaadatok tartalmazza.  

Az egyes modulok konfigurációs metaadatok tartalmaznak: 

* Verzió 
* Típus 
* Állapot (például fut vagy Leállítva) 
* Újraindítási szabályzat 
* Kép- és tároló-beállításjegyzék
* Bemeneti és kimeneti adatok útvonalai 

Ha a modul rendszerképet egy privát tárolójegyzékben található, az IoT Edge-ügynök tárolja a tárolójegyzék hitelesítő adatainak. 

### <a name="target-condition"></a>Célfeltétel

A cél feltételét a rendszer folyamatosan kiértékeli az üzemelő példány teljes élettartama során. A követelményeknek megfelelő új eszközök megtalálhatók, és a meglévő eszközöket, amelyek többé nem törlődnek. A központi telepítés akkor aktiválódik, ha a szolgáltatás észleli a célként megadott feltétel tejes. 

Például rendelkezik, egy-egy cél feltétel tags.environment = 'prod'. Az üzembe helyezés indíthat, ha nincsenek 10 eszközökre. A modulok telepítése sikeresen megtörtént a 10 ezeket az eszközöket. Összes eszköz 10, 10 sikeres válaszok, 0 meghibásodási válaszok és 0 függőben lévő válaszok formájában jelenik meg az IoT Edge-ügynök állapota. Most már hozzáadhat öt további eszközök tags.environment = 'prod'. A szolgáltatás észleli a változást, és az IoT Edge-ügynök állapota 15 összes eszköz, 10 sikeres válaszok, 0 meghibásodási válaszok és 5 függőben lévő válaszok válik az öt új eszközökre való.

A device twins címkéket vagy deviceId bármely logikai feltétel használatával céleszközeinek kiválasztásához. Ha címkével szeretné feltételt használni, hozzá kell adnia a "címkék":{} szakaszt az eszköz twin (a tulajdonságok között azonos szinten). [További információ a címkékről az eszköz Twin-ben](../iot-hub/iot-hub-devguide-device-twins.md)

Példák a célként megadott feltétel:

* deviceId = "linuxprod1"
* tags.Environment = 'prod'
* tags.Environment = 'prod' AND tags.location = 'westus'
* tags.Environment = 'prod' vagy tags.location = 'westus'
* tags.Operator = "János" AND tags.environment = 'prod' nem deviceId = "linuxprod1"

Íme néhány korlátozza, amikor egy célfeltétel hozhat létre:

* Ikereszköz a címkék vagy deviceId célfeltétel csak készíthet.
* A célfeltétel bármely részének dupla idézőjelek között nem engedélyezett. Használjon aposztrófot.
* Szimpla idézőjelek között a célfeltétel értékeit jelölik. Ezért egy másik egyetlen ajánlattal a szimpla idézőjel kell elkerülésére, ha az eszköz neve része. Ha például egy `operator'sDevice`nevű eszközt szeretne megcélozni, írja be a `deviceId='operator''sDevice'`.
* A megcélzott feltételi értékekben a számok, betűk és a következő karakterek engedélyezettek: `-:.+%_#*?!(),=@;$`.

### <a name="priority"></a>Prioritás

Prioritás határozza meg, akár egy központi telepítési egy céleszköz képest más központi kell alkalmazni. Üzembe helyezési prioritás pozitív egész szám, a nagyobb számot jelzi, hogy a magasabb prioritású. Ha több üzemelő IoT Edge-eszköz vonatkozik, az üzembe helyezés a legmagasabb prioritású vonatkozik.  Az alacsonyabb prioritású központi telepítések nincsenek alkalmazva, és nincsenek egyesítve.  Ha egy eszközhöz két vagy több, azonos prioritású üzemelő példány van rendelve, a rendszer a legutóbb létrehozott központi telepítést (a létrehozási időbélyeg alapján határozza meg) alkalmazza.

### <a name="labels"></a>Címkék 

A címkékhez karakterlánc kulcs/érték párok, amellyel szűrőt és központi telepítések csoportja. Egy üzemelő példány több címkével is rendelkezhet. Címkék megadása nem kötelező, és nincs hatással a tényleges konfiguráció az IoT Edge-eszközök beállítása. 

### <a name="deployment-status"></a>Üzembe helyezés állapota

Központi telepítés meghatározásához, hogy életbe léptette-e sikeresen bármely célzott IoT Edge-eszköz figyelhető.  A célként megadott peremhálózati eszköz a következő állapotok egy vagy több kategóriájában fog megjelenni: 

* A **cél** megjeleníti a központi telepítésre vonatkozó célzott feltételnek megfelelő IoT Edge eszközöket.
* A **tényleges** IoT Edge olyan eszközöket jeleníti meg, amelyeket nem a magasabb prioritású másik telepítés céloz meg.
* A **kifogástalan** állapotú eszközök azokat a IoT Edge eszközöket jeleníti meg, amelyek visszajelentették azokat a szolgáltatást, amelyeken a modulok telepítése sikeresen befejeződött. 
* A nem megfelelő **állapot** azt mutatja, hogy a IoT Edge eszközök visszajelentették a szolgáltatásnak, hogy az egyik vagy a modul telepítése nem sikerült. További vizsgálat céljából a hiba, távoli csatlakozás azokat az eszközöket, és naplófájljainak megtekintéséhez.
* **Ismeretlen** a IoT Edge olyan eszközök megjelenítése, amelyek nem jelentettek semmilyen, a központi telepítésre vonatkozó állapotot. További vizsgálat céljából, megtekintheti az adatok és a napló fájljai.

## <a name="phased-rollout"></a>Fázisokra bontva történő bevezetéséhez 

Egy fázisokra bontva történő bevezetéséhez egy általános eljárásait, amellyel az operátornak telepíti a módosítások egy IoT Edge-eszközök kiterjesszük készletét. A célja az, hogy a parancsban történt használhatatlanná tévő széles skálán kockázatának csökkentése érdekében fokozatosan módosítsa.  

Több lépcsőben vezeti be a következő fázisra és lépéseket hajtja végre: 

1. Hozzon létre egy tesztkörnyezet IoT Edge eszközök kiépítésével, és az eszköz kettős címkéjét (például `tag.environment='test'`). A tesztkörnyezetben tükröznie kell azt a éles környezetet, amelyet a központi telepítés végül megcéloz. 
2. Beleértve a kívánt modulok és konfigurációk központi telepítés létrehozása. A célcsoport-kezelési feltétel a teszt IoT Edge-eszköz környezetre irányul.   
3. Ellenőrizze a tesztelési környezetben új modul konfigurációját.
4. A frissítéstelepítés, hogy egy új címke hozzáadása a célcsoport-kezelési feltétel éles IoT Edge-eszközök egy részhalmazát tartalmazzák. Emellett gondoskodjon arról, hogy a prioritás a központi telepítés magasabb, mint más jelenleg célzott az adott eszközökre való központi telepítések 
5. Győződjön meg arról, hogy az üzembe helyezés sikeres volt-e a megcélzott IoT-eszközökön a központi telepítés állapotának megtekintése.
6. A telepítés összes többi éles IoT Edge-eszközök frissítéséhez.

## <a name="rollback"></a>Visszaállítás

Üzemelő példányok is lesz állítva, ha hibák vagy konfigurációs hibák kap.  Mivel a központi telepítés egy IoT Edge eszköz abszolút moduljának konfigurációját definiálja, egy további központi telepítésnek is ugyanazt az eszközt kell céloznia, ha a cél az összes modul eltávolítása.  

Visszagörgetése hajtsa végre a következő lépéseket: 

1. Győződjön meg arról, hogy egy második üzemelő példány is irányuló eszköz ugyanezek. A visszaállítás az a célja, hogy távolítsa el az összes modult, ha a második üzembe helyezést nem tartalmaznia kell minden olyan modulokat. 
2. Módosítsa vagy távolítsa el az üzemelő példány vissza úgy, hogy az eszközök már nem felelnek meg célcsoport-kezelési visszaállítás kívánt cél feltételkifejezés.
3. Győződjön meg arról, hogy a visszaállítás sikeres volt-e a központi telepítés állapotának megtekintése.
   * A helyzetben előzőre telepítési állapotúnak kell lennie már nem az eszközökhöz, amelyek vissza lett állítva.
   * A második üzembe helyezést most már tartalmazza az eszközök, amelyeket vissza lett állítva a telepítés állapota.


## <a name="next-steps"></a>Következő lépések

* Végigvezeti a központi telepítések létrehozásához, frissítéséhez vagy törléséhez szükséges lépéseket a [IoT Edge modulok nagy léptékű üzembe helyezéséhez és figyeléséhez](how-to-deploy-monitor.md).
* További információ a [IoT Edge futtatókörnyezettel](iot-edge-runtime.md) és a [IoT Edge modulokkal](iot-edge-modules.md)kapcsolatos egyéb IoT Edge fogalmakról.

