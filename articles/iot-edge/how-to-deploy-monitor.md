---
title: A modulok figyeli, hogy Azure IoT peremhálózati |} Microsoft Docs
description: A peremhálózati eszközön futó modulok kezelése
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/07/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6d024dfdd661d6bebe7d163b96659d6e169cc5cc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale---preview"></a>Központi telepítése és figyelése IoT peremhálózati modulok léptékű – előzetes

Azure IoT peremhálózati lehetővé teszi analytics áthelyezése a peremhálózaton, és a felhő felületet biztosít, így kezelheti és figyelheti az IoT-peremeszközök anélkül, hogy minden egyes fizikailag eléréséhez. Távolról kezelheti az eszközöket képesség, egyre fontosabb az eszközök internetes hálózatát megoldások nagyobb és összetettebb folyamatosan nőnek. Az üzleti céljaihoz, függetlenül attól, hány eszközt ad hozzá Azure IoT peremhálózati terveztek.

Egyes eszközök kezeléséhez és a modulok telepíteni őket egyenként. Azonban ha azt szeretné, nagy léptékű eszközökre módosításokat, létrehozhat egy **IoT peremhálózati automatikus központi telepítési**, amely az IoT hubon automatikus kezelés része. Központi telepítések dinamikus folyamatok, amelyek lehetővé teszik több modul egyszerre több eszközre telepíteni, állapota és a modulok állapotának nyomon, és szükség esetén módosítása. 

## <a name="identify-devices-using-tags"></a>Címkék használatával eszközök azonosítása

Mielőtt létrehozna egy központi telepítést, akkor lehet megadni, mely eszközöket szeretné befolyásolni. Az Azure IoT peremhálózati azonosítja eszközök **címkék** az eszköz iker a. Minden eszköz lehet több címkét, és meghatározhatja azokat bármilyen módon legjobb megoldást. Például ha egy intelligens épületek egyetemi kezelni, hozzáadhatja a következő címkék eszköz:

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

Eszköz twins és címkékkel kapcsolatos további információkért lásd: [megértése és használja az IoT Hub eszköz twins][lnk-device-twin].

## <a name="create-a-deployment"></a>Hozzon létre telepítést

1. Az a [Azure-portálon][lnk-portal], keresse fel az IoT hub. 
1. Válassza ki **IoT peremhálózati (előzetes verzió)**.
1. Válassza ki **IoT Edge-telepítés hozzáadása**.

A központi telepítés létrehozásához öt lépésből áll. A következő szakaszok segítségével minden egyes ismerteti. 

### <a name="step-1-name-and-label"></a>1. lépés: Nevét és a felirat

1. Adjon meg egy egyedi nevet a központi telepítés. Elkerülendő, a szóközöket és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
1. Adjon hozzá a központi telepítések nyomon követéséhez. Címkék: **neve**, **érték** tartalmaznak, amelyek a központi telepítés ismertetik. Például `HostPlatform, Linux` vagy `Version, 3.0.1`.
1. Válassza ki **következő** lépés két ugrás. 

### <a name="step-2-add-modules-optional"></a>2. lépés:, Vegye fel a modulok (nem kötelező)

A modulok, amely egy központi telepítést adhat hozzá két típusa van. Az egyik ki az Azure-szolgáltatások, például a Tárfiók, vagy a Stream Analytics-alapú modul. A második pedig egy modul ki saját kód alapján. A központi telepítés vagy típus több modul adhat hozzá. 

Ha a központi telepítés létrehozásához engedélyezi nincsenek modulok, meglévő modul eltávolítása az eszközöket. 

>[!NOTE]
>Az Azure Machine Learning és az Azure Functions automatizált Azure szolgáltatás központi telepítése még nem támogatott. A modul egyéni központi telepítés segítségével manuálisan ezekbe a szolgáltatásokba hozzá szeretne adni a telepítéshez. 

Azure Stream Analytics egy modul hozzáadásához kövesse az alábbi lépéseket:
1. Válassza ki **importálási Azure Stream Analytics IoT peremhálózati modul**.
1. A legördülő menükben segítségével válassza ki az Azure szolgáltatáspéldány, amely számára telepíteni kívánja.
1. Válassza ki **mentése** a modul hozzá szeretne adni a telepítéshez. 

Adja hozzá az egyéni kódot modulként, vagy manuálisan adja hozzá az Azure-szolgáltatások modul, kövesse az alábbi lépéseket:
1. Válassza az **Add IoT Edge module** (IoT Edge-modul hozzáadása) lehetőséget.
1. A modul adjon egy **neve**.
1. Az a **lemezkép URI** mezőbe írja be a Docker-tároló kép a modulhoz. 
1. Adja meg az esetleges **tároló létrehozása beállítások** , amely a tárolóban kell átadni. További információkért lásd: [docker létrehozása][lnk-docker-create].
1. Válassza ki a legördülő menü segítségével egy **indítsa újra a házirend**. A következő lehetőségek közül választhat: 
   * **Mindig** -a modul mindig újraindul, ha bármilyen okból állítja le.
   * **Soha ne** -a modul soha nem indul újra, ha bármilyen okból állítja le.
   * **A sikertelen** -a modul újraindul, ha azt összeomlik, de nem Ha állítja le megfelelően. 
   * **A sérült** -a modul újraindítása, ha összeomlik, vagy a nem kifogástalan állapotú adja vissza. Már minden modul egészségügyi állapot függvény végrehajtásához. 
1. A legördülő menü segítségével válassza ki a **szükséges állapot** modul. A következő lehetőségek közül választhat:
   * **Futó** -Ez az alapértelmezett beállítás. A modul futtató üzembe helyezése után azonnal elindul.
   * **Leállítva** -üzembe helyezése után a modul üresjárat mindaddig, amíg Ön vagy egy másik modul start alapján kell.
1. Válassza ki **engedélyezése** Ha azt szeretné, a címkéket és a kívánt tulajdonságok hozzáadása a modul iker. 
1. Válassza ki **mentése** a modul hozzá szeretne adni a telepítéshez. 

Ha már konfigurált a modulok, válassza ki a **következő** három lépés áthelyezése.

### <a name="step-3-specify-routes-optional"></a>3. lépés: Adja meg az útvonalakat (nem kötelező)

Útvonalak határozza meg, hogyan modulok kommunikálnak egymással a központi telepítés belül. Adja meg a központi telepítés bármely útvonalakat, és válasszon **következő** négy lépésben áthelyezése. 

### <a name="step-4-target-devices"></a>4. lépés: A Céleszközök számára

A tulajdonsággal címkéket az eszközökről, amelyekre a kívánt eszközök, amelyek megkapják a központi telepítéshez. 

Mivel több központi telepítéssel is céloz ugyanarra az eszközre, adjon egyes központi telepítések egy prioritást. Ha valaha is ütközést, a központi telepítés a legmagasabb prioritású wins. Ha két központi telepítések ugyanazt a prioritást, a legtöbb létrehozott egy közelmúltban wins. 

1. Adjon meg egy pozitív egész szám a központi telepítés **prioritás**.
1. Adjon meg egy **céloz feltétel** határozza meg, hogy mely eszközök fog ezzel az üzembe helyezéssel a. A feltétel eszköz iker címkék alapul, és meg kell felelnie a kifejezés formátumban. Például: `tags.environment='test'`. 
1. Válassza ki **következő** áthelyezése az utolsó lépését.

### <a name="step-5-review-template"></a>5. lépés: Felülvizsgálati sablonja

Tekintse át a központi telepítési adatait, majd válasszon **Submit**.

## <a name="monitor-a-deployment"></a>A figyelő a központi telepítés

A központi telepítés részleteinek megtekintéséhez, és figyelje az eszközöket is fut, tegye a következőket:

1. Jelentkezzen be a [Azure-portálon] [ lnk-portal] , és keresse meg az IoT hub. 
1. Válassza ki **IoT peremhálózati (előzetes verzió)**.
1. Válassza ki **IoT peremhálózati központi telepítések**. 

   ![Nézet IoT peremhálózati központi telepítések][1]

1. Vizsgálja meg a központi telepítési lista. Az egyes központi telepítések megtekintheti a következő adatokat:
   * **Azonosító** -a központi telepítés nevét.
   * **Cél feltétel** -megcélzott eszköz definiáló tag.
   * **Prioritás** -a prioritást az üzemelő példányhoz társítva.
   * **Az IoT-Edge ügynök állapota** -eszközöket, amelyek a központi telepítést, és hozzájuk állapotfigyelő állapotok számát. 
   * **A nem megfelelő modulok** -a központi telepítés hibajelentést modulok száma. 
   * **Létrehozás ideje** -a központi telepítés létrehozásakor az időbélyegző. Az időbélyegző szolgál, bontsa ki, amikor két központi telepítések a azonos prioritással rendelkezik. 
1. Válassza ki a figyelni kívánt telepítést.  
1. Vizsgálja meg a központi telepítés részleteit. Lapok segítségével megtekintheti a részletes adatait az eszközöket, amelyek a központi telepítés érkezett: 
   * **Megcélzott** -a peremhálózati eszköz a célként megadott feltételnek megfelelő. 
   * **Alkalmazott** – a megcélzott peremhálózati eszköz, amely nem a nagyobb prioritású egy másik telepítés által célzott. Ezek azok az eszközöket, amelyek ténylegesen megkapná a központi telepítést. 
   * **Sikeres Reporting** - az alkalmazott peremeszközök küldött vissza a szolgáltatás számára, hogy a modulok üzembe helyezése sikeresen befejeződött. 
   * **Hiba Reporting** – az alkalmazott peremhálózati eszköz, amely jelentett vissza a szolgáltatás, amelyre egy vagy több modulok nem települtek-e sikeresen. A további vizsgálathoz a hiba, szüksége lesz az eszközök távolról csatlakozni és megtekinteni a rendszernapló fájljaiban. 
   * **A nem megfelelő modulok Reporting** – az alkalmazott peremhálózati eszköz, amely jelentette vissza a szolgáltatásnak, hogy egy vagy több modulok üzembe helyezése sikeresen befejeződött, de most jelentik a hibákat. 

## <a name="modify-a-deployment"></a>Módosítsa a központi telepítés

Amikor módosít egy központi telepítést, a módosítások azonnal replikálja az összes megcélzott eszköz. 

Ha frissíti a cél feltétel, a következő frissítéseket fordulhat elő:
* Ha egy eszköz nem felelt meg a régi cél feltétel, de az új cél feltétel és a központi telepítés a legmagasabb prioritású az eszköznek, majd a központi telepítés vonatkozik az eszközt. 
* Jelenleg fut a központi telepítés már nem eszköz megfelel-e a célként megadott feltétel, ha eltávolítja a központi telepítés, és időt vesz igénybe, a következő legmagasabb prioritású üzemelő példányon. 
* Ha a jelenleg futó már nem a központi telepítés eszköz megfelel-e a célként megadott feltétel, és nem felel meg a célként megadott feltétel egyéb telepítések, majd nem módosul az eszközön. Az eszköz továbbra is fennáll, az aktuális modulok aktuális állapotban fut, de nem áll az üzembe helyezés többé. Amennyiben az megfelel-e bármilyen más központi telepítési cél feltétele, eltávolítja a központi telepítés, és az új kiszolgálón időt vesz igénybe. 

A telepítés módosításához tegye a következőket: 

1. Jelentkezzen be a [Azure-portálon] [ lnk-portal] , és keresse meg az IoT hub. 
1. Válassza ki **IoT peremhálózati (előzetes verzió)**.
1. Válassza ki **IoT peremhálózati központi telepítések**. 

   ![Nézet IoT peremhálózati központi telepítések][1]

1. Válassza ki a módosítani kívánt központi telepítés. 
1. Frissítések készítése a következő mezőket: 
   * Cél feltétel 
   * Címkék 
   * Prioritás 
1. Kattintson a **Mentés** gombra.
1. Kövesse a [a központi telepítésének] [ anchor-monitor] bemutató megkezdik a módosításokat. 

## <a name="delete-a-deployment"></a>A központi telepítés törlése

Ha töröl egy központi telepítést, azokat az eszközöket, a következő legmagasabb prioritású telepítésekhez igénybe. Ha az eszközök nem felelnek meg a célként megadott feltétel más központi telepítés, majd a modulok nem törlődnek, amikor a központi telepítést törölték. 

1. Jelentkezzen be a [Azure-portálon] [ lnk-portal] , és keresse meg az IoT hub. 
1. Válassza ki **IoT peremhálózati (előzetes verzió)**.
1. Válassza ki **IoT peremhálózati központi telepítések**. 

   ![Nézet IoT peremhálózati központi telepítések][1]

1. A jelölőnégyzet segítségével válassza ki a törölni kívánt központi telepítését. 
1. Válassza a **Törlés** elemet.
1. A kérdés tájékoztatja, hogy ez a művelet törli a központi telepítés és az összes eszköz korábbi állapotának visszaállításához.  Ez azt jelenti, hogy az alacsonyabb prioritású központi telepítés alkalmazza.  Ha nincs másik üzemelő példány, nincsenek modulok törlődik. Az ügyfelek érdemes alkalmazni, ha szükségük nulla modulok egy központi telepítés létrehozásához, és telepítse azt a azonos eszközökre. Válassza ki **Igen** Ha továbbra is szeretné. 

## <a name="next-steps"></a>További lépések

További információ [modulok telepítése peremeszközök][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
