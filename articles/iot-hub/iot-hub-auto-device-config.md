---
title: Konfigurálhatja és figyelheti az Azure IoT Hub nagy mennyiségű IoT-eszközök |} A Microsoft Docs
description: Azure IoT Hub automatikus konfigurációk használatával több eszközt konfiguráció hozzárendelése
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: 2edde122b109779794bb86752d69a5318edb9235
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "42056860"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-portal"></a>Konfigurálhatja és figyelheti az Azure portal használatával nagy mennyiségű IoT-eszközök

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Az Azure IoT Hub automatikus kezelés automatizálja ismétlődő és összetett feladatok nagy eszköz flották kezelésének azok életciklusának teljes keresztül. Automatikus felügyeleti (MDM) cél az eszközök a hozzájuk tartozó tulajdonságok alapján, a kívánt konfiguráció definiálása és frissítheti az eszközeit, amikor hatókör lépnek az IoT Hub segítségével.  Ez egy automatikus konfigurációs, amely is lehetővé teszi az összegzés befejezését és a megfelelőség, a leíró egyesítése és a ütközések, és megkezdik a szakaszos megközelítéssel konfigurációk használatával történik.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatikus konfigurációk munkahelyi ikereszközök készletét frissítése a kívánt tulajdonságokkal és ikereszköz alapján összefoglaló jelentés által jelentett tulajdonságokként.  Azt mutatja be egy új osztályt és nevű JSON-dokumentumok egy *konfigurációs* amely három részből áll:

* A **feltétel cél** ikereszközök frissítendő hatókörének meghatározása. A célfeltétel device twin címkékre lekérdezésként van megadva és/vagy jelentett tulajdonságokként.

* A **tartalmat** hozzáadva vagy frissítve a célzott eszközök ikerállapotának a kívánt tulajdonságok meghatározása. A tartalom elérési útját a módosítani kívánt tulajdonságok szakasza tartalmaz.

* A **metrikák** határozza meg, például konfigurációs állapotait összesítő számát **sikeres**, **folyamatban lévő**, és **hiba**. Egyéni metrikák megadott eszközön lekérdezések ikereszköz jelentett tulajdonságait.  Rendszermérőszámokat alapértelmezett mérőszámok, amelyek a ikereszköz állapotát, például az ikereszközök szánt száma és a twins, amelyek sikeresen frissítve lett-e a rendszer. 

## <a name="implement-device-twins-to-configure-devices"></a>Eszközök konfigurálása ikereszközök megvalósítása

Automatikus eszközkonfigurációkat állapota a felhő és az eszközök közötti szinkronizálását ikereszközök használata szükséges. Tekintse meg [ikereszközök megismerése és használata az IoT Hub](iot-hub-devguide-device-twins.md) útmutató ikereszközök használatával.

## <a name="identify-devices-using-tags"></a>Címkék használatával eszközök azonosítása

Mielőtt létrehozna egy konfigurációt, mely eszközöket szeretné befolyásolni meg kell adnia. Az Azure IoT Hub eszköz az ikereszköz címkék használatával azonosítja. Minden eszköz rendelkezhet több címkét, és meghatározhatja azokat bármilyen módon, amely logikus a megoldáshoz. Például ha különböző helyeken lévő eszközök kezeléséhez, előfordulhat, hogy hozzá a következő címkék az ikereszközök:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

1. Az a [az Azure portal](https://portal.azure.com), keresse fel az IoT hubnak. 

2. Válassza ki **IoT-eszköz konfigurációs**.

3. Válassza ki **konfiguráció hozzáadása**.

Hozzon létre egy konfigurációt öt lépésből áll. A következő szakaszok egyenként végig. 

### <a name="name-and-label"></a>Név és címke

1. Adjon meg egy egyedi nevet, amely legfeljebb 128 kisbetűket a konfiguráció. Kerülje a tárolóhelyek és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.

2. Adja hozzá a címkéket a konfigurációk nyomon követéséhez. Címkék **neve**, **érték** párok, amelyek ismertetik a konfigurációt. Ha például `HostPlatform, Linux` vagy `Version, 3.0.1`.

3. Válassza ki **tovább** áthelyezése a következő lépéssel. 

### <a name="specify-settings"></a>Beállítások megadása

Ebben a szakaszban meghatározza a cél tartalmat a célzott eszközök ikerállapotának kell beállítani. Nincsenek a két bemenet az egyes beállítások. Az első az eszköz ikereszköz útvonal, a JSON szakaszra belül az ikereszköz kívánt tulajdonságait, amely fogja állítani az elérési útját.  A második pedig a szakaszban lévő beszúrandó JSON-tartalmak. Például állítsa be az eszköz Ikereszköz elérési útját és a tartalom a következőhöz:

![Adja meg a Device Twin elérési út és a tartalom](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Egyéni beállítások beállíthatja úgy, hogy a teljes elérési út megadását az eszköz Ikereszköz elérési útját és a tartalom nem zárójelekkel értékét is. Például állítsa be az eszköz Ikereszköz elérési útját `properties.desired.chiller-water.temperature` , és állítsa a tartalom `66`.

Ha két vagy több konfigurációk ugyanazt eszköz az Ikereszköz elérési utat célként, a tartalom a legmagasabb prioritású konfigurációs alkalmazza (4. lépés a prioritás van megadva).

Ha el szeretné távolítani a tulajdonságot, adja meg a tulajdonság értéket `null`.

További beállítások kiválasztásával adhat hozzá **Device Twin beállítás hozzáadása**.

### <a name="specify-metrics-optional"></a>Adja meg a metrikák (nem kötelező)

Metrikák adja meg az eszköz lehet, hogy jelentéseket küldhetnek vissza alkalmazása konfigurációjának tartalma eredményeként állapotait összesítő számát. Például előfordulhat, hogy hozzon létre egy metrikát a függőben lévő beállításokat érintő változások, a egy metrikát a hibákat és a egy metrikát a sikeres beállítások módosításait.

1. Adjon meg egy nevet **metrika neve**.

2. Adjon meg egy lekérdezést a **metrika feltételek**.  A lekérdezés alapul eszköz ikereszköz jelentett tulajdonságait.  A metrika a lekérdezés által visszaadott sorok számát jelöli.

Példa:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

Megadhat egy záradékot, hogy a konfigurációt alkalmazta, például: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

### <a name="target-devices"></a>Céleszközök

A tulajdonság az ikereszközök címkék segítségével jelölje ki a kívánt eszközök, amelyek megkapják ezt a konfigurációt.  Az eszköz eszközök is célként ikereszköz jelentett tulajdonságait.

Mivel több konfiguráció megcélozhatnak ugyanarra az eszközre, adjon az egyes konfigurációkhoz egy prioritást. Minden eddiginél van ütközés, ha a konfiguráció a legmagasabb prioritású wins. 

1. Adja meg egy pozitív egész számot a konfigurációs **prioritású**. A legnagyobb numerikus értéket számít a legmagasabb prioritást. Ha két azonos prioritású számot, azt, amelyik a legtöbb készült nemrégiben wins. 

2. Adjon meg egy **feltétel cél** meghatározni, hogy mely eszközök érinteni fog ebben a konfigurációban. A feltétel device twin címkék alapján vagy az ikereszköz jelentett tulajdonságait, és meg kell egyeznie a kifejezés formátuma. Ha például `tags.environment='test'` vagy `properties.reported.chillerProperties.model='4000x'`. Megadhat `*` minden eszközt megcéloznak a.

3. Válassza ki **tovább** , továbbléphet az utolsó lépés.

### <a name="review-configuration"></a>Konfiguráció áttekintése

Tekintse át a konfigurációs adatokat, majd válassza a **küldés**.

## <a name="monitor-a-configuration"></a>A figyelő a konfigurációs

Egy konfigurációs adatait tekintheti meg, és az azt futtató eszközök figyelését, használja az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), keresse fel az IoT hubnak. 

2. Válassza ki **IoT-eszköz konfigurációs**.

3. Vizsgálja meg a konfigurációs listát. Minden egyes konfiguráció esetében a következő adatokat tekintheti meg:

   * **ID** -konfiguráció neve.

   * **A feltétel cél** – a lekérdezés segítségével határozhatók meg a megcélzott eszközökön.

   * **Prioritás** -a a konfiguráció rendelt prioritás száma.

   * **Létrehozás ideje** – a konfiguráció létrehozásakor időbélyege. Az időbélyegző ties megszüntetése, ha két azonos prioritású szolgál. 

   * **Rendszermérőszámokat** -metrikák, amely az IoT Hub számítják ki és a fejlesztők nem szabható testre. Megcélzott ikereszközök a célként megadott feltételnek megfelelő számát adja meg. Alkalmazza a megadott ikereszközök, hogy módosította a konfigurációt, amelyek magukban foglalhatják a részleges módosítás abban az esetben, ha egy különálló, magasabb prioritású konfigurációs továbbá által végrehajtott módosítások számát. 

   * **Egyéni metrikák** -mérőszámok, amelyek az ikereszköz-lekérdezéseket, fejlesztője által lett megadva jelentett tulajdonságokként.  Legfeljebb öt egyéni metrikákat definiálható konfiguráció. 
   
4. Válassza ki a figyelni kívánt konfiguráció.  

5. Vizsgálja meg a konfigurációs adatait. Lapok használatával megtekintheti a részletes adatait az eszközöket, amelyek a konfigurációt kapott.

   * **A feltétel cél** – az eszközöket, amelyek a célként megadott feltételnek megfelelő. 

   * **Metrikák** -rendszermérőszámot és egyéni mérőszámok listája.  Minden egyes metrika számítanak eszközök listáját megtekintheti a metrika kell választania a listából, és válassza **eszközök megtekintése**.

   * **Ikereszköz eszközbeállítások** – az ikereszköz eszközbeállítások által a konfigurációt. 

   * **Konfigurációs címkék** -konfiguráció leírására szolgáló kulcs-érték párokat.  Címkék semmilyen hatást a funkciót nem. 

## <a name="modify-a-configuration"></a>A konfiguráció módosítása

Ha módosítja a konfigurációt, a módosítások azonnal replikálja az összes megcélzott eszközre. 

A célfeltétel frissít, ha elő a következő frissítéseket:

* Az ikereszközök nem felelt meg a régi célfeltétel, de az új célfeltétel megfelel, és ez a konfiguráció a legmagasabb prioritású az adott ikereszköz, ha ez a konfiguráció az ikereszköz van alkalmazva. 

* Az ikereszközök már nem felel meg a célként megadott feltétel, ha a konfigurációs beállításai a rendszer eltávolítja, és az ikereszköz fog módosította a következő legmagasabb prioritású konfigurációs. 

* Ha már nem ez a konfiguráció jelenleg fut az ikereszközök a célfeltétel megfelel, és minden egyéb konfigurációt cél állapota nem felel meg, a konfigurációs beállításai a eltávolításra kerül, és minden egyéb módosítás kerül sor az ikereszköz. 

A konfiguráció módosításához használja az alábbi lépéseket: 

1. Az a [az Azure portal](https://portal.azure.com), keresse fel az IoT hubnak. 

2. Válassza ki **IoT-eszköz konfigurációs**. 

3. Válassza ki a módosítani kívánt beállításait. 

4. Hajtsa végre a frissítéseket a következő mezőket: 

   * Célfeltétel 
   * Címkék 
   * Prioritás 
   * Mérőszámok

4. Kattintson a **Mentés** gombra.

5. Kövesse a [figyelése a configuration](#monitor-a-configuration) megkezdik a módosítások megtekintéséhez. 

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Amikor egy konfiguráció törléséhez a következő legmagasabb prioritású konfigurációjuk bármely ikereszközök igénybe vehet. Ikereszközök nem felelnek meg a célfeltétel bármely más beállítás, ha nincs más beállítások lesznek alkalmazva. 

1. Az a [az Azure portal](https://portal.azure.com) nyissa meg az IoT hubnak. 

2. Válassza ki **IoT-eszköz konfigurációs**. 

3. A jelölőnégyzet segítségével válassza ki a törölni kívánt beállításait. 

4. Válassza a **Törlés** elemet.

5. Kérdés rákérdez, hogy erősítse meg.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja és figyelheti a nagy mennyiségű IoT-eszközök. Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Az IoT Hub eszközidentitások tömeges kezelése](iot-hub-bulk-identity-mgmt.md)
* [Az IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)

Böngészhet a beavatkozás nélküli, just-in-time kiépítését lehetővé tevő, olvassa el az IoT Hub Device Provisioning Service használatával: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
