---
title: Konfigurálhatja és figyelheti az IoT-eszközök Azure IoT hubbal léptékű |} Microsoft Docs
description: Konfiguráció hozzárendelése több eszköz automatikus eszközkonfigurációk Azure IoT-központ segítségével
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: fe5ce960663f39d4f2c87a7bbffa091d327e9559
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632448"
---
# <a name="configure-and-monitor-iot-devices-at-scale---preview"></a>Konfigurálja és az IoT-eszközök léptékű figyelése – előzetes

Automatikus kezelés az Azure IoT Hub automatizálja az ismétlődő és az összetett feladatok nagy eszköz flották kezelését a teljes egészében a életciklusának keresztül. Automatikus felügyeleti (MDM) az eszközök tulajdonságaik alapján célozza, adja meg a kívánt konfiguráció, és lehetővé teszik az IoT-központ frissítése az eszközöket, amikor a hatókör származnak.  Ez egy automatikus eszközkonfiguráció, amely lehetővé teszi, hogy összesítse a befejezése és a megfelelőség, a leíró egyesítése és a ütközések, és fokozatosan konfigurációk szakaszos megközelítéssel használatával történik.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatikus eszköz konfigurációk munkahelyi eszköz twins csoportja frissítésével kívánt tulajdonságokkal és jelentéskészítési eszköz iker alapján összefoglaló jelentett tulajdonságait.  Egy új osztályt és nevű JSON-dokumentum okozna a _konfigurációs_ amelyek három részből áll:

* A **céloz feltétel** eszköz twins frissítendő körét határozza meg. A célként megadott feltétel van megadva eszköz iker címkékre lekérdezésként és/vagy jelentett tulajdonságok.

* A **céloz tartalom** kell hozzáadni, vagy a céleszköz twins frissített kívánt tulajdonságait határozza meg. A tartalom elérési útját a módosítani kívánt tulajdonságok szakasza tartalmazza.

* A **metrikák** határozza meg az összefoglaló számát is különböző konfigurációs állapotnak, mint **sikeres**, **folyamatban lévő**, és **hiba**. Egyéni metrikák eszközön lekérdezések vannak megadva a két jelentett tulajdonságok.  Rendszer-adatok gyűjtése le alapértelmezett metrikák mérő iker frissítési állapot, például az eszköz twins irányuló számát és a sikeresen frissített twins számát. 

> [!Note]
> Előzetes Ez a szolgáltatás nem áll rendelkezésre az IoT-központok USA keleti régiója, USA nyugati régiója, Észak-Európában és Nyugat-Európában régiókban.

## <a name="implement-device-twins-to-configure-devices"></a>Eszközök konfigurálása eszköz twins megvalósítása

Automatikus eszközkonfigurációk eszköz twins a felhő- és eszközök közötti szinkronizálását használata szükséges.  Tekintse meg [megértése és használja az IoT Hub eszköz twins] [ lnk-device-twin] útmutatót twins eszköz használatával.

## <a name="identify-devices-using-tags"></a>Címkék használatával eszközök azonosítása

Mielőtt létrehozna egy konfigurációs, mely eszközöket szeretné befolyásolni kell megadnia. Az Azure IoT Hub címkék használata az eszköz két eszközöket jeleníti meg. Minden eszköz lehet több címkét, és meghatározhatja azokat bármilyen módon legjobb megoldást. Például különböző helyeken lévő eszközök kezeléséhez, ha egy eszköz iker hozzáadhat előfordulhat, hogy a következő címkékkel:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

1. Az a [Azure-portálon][lnk-portal], keresse fel az IoT hub. 
1. Válassza ki **IoT-eszközök konfigurációját (előzetes verzió)**.
1. Válassza ki **konfiguráció hozzáadása**.

A konfiguráció létrehozásához öt lépésből áll. A következő szakaszok segítségével minden egyes ismerteti. 

### <a name="step-1-name-and-label"></a>1. lépés: Nevét és a felirat

1. Adjon meg legfeljebb 128 kisbetűk egy egyedi nevet a konfigurációt. Elkerülendő, a szóközöket és a következő érvénytelen karaktereket: `& ^ [ ] { } \ | " < > /`.
1. Adja hozzá a címkéket a konfigurációk nyomon követéséhez. Címkék: **neve**, **érték** tartalmaznak, amelyek ismertetik a konfigurációt. Például `HostPlatform, Linux` vagy `Version, 3.0.1`.
1. Válassza ki **következő** lépés két ugrás. 

### <a name="step-2-specify-settings"></a>2. lépés: Adja meg a beállításokat

Ebben a szakaszban adja meg a cél tartalmat a célzott eszközön twins lehet megadni. Nincsenek az egyes beállítások két bemenet. Az első az eszköz kettős elérési úttal, ami belüli lesz beállítva, a két szükséges tulajdonságok JSON szakasz elérési útja.  A második a JSON-e ezen részében beszúrni. Például állítsa be az eszköz kettős elérési útját és a tartalom a következőhöz:

![Az eszköz kettős elérési útját és a tartalom beállítása](./media/iot-hub-auto-device-config/create-configuration-full-browser.png)

Az eszköz kettős elérési útját és a tartalom nem zárójelekkel értéke a teljes elérési út megadásával is beállíthatja egyéni beállítások. Állítsa például a kettős elérési útján `properties.desired.chiller-water.temperature` , és a tartalom: `66`

Ha két vagy több konfigurációk megcélozni a azonos kettős elérési útján, a tartalmat a legmagasabb prioritású konfigurációs érvényes (4. lépés a prioritás van megadva).

Ha el szeretné távolítani egy tulajdonság, adja meg a tulajdonság értéket `null`.

További beállítások kiválasztásával adhat hozzá **eszköz a két beállítás hozzáadása**

### <a name="step-3-specify-metrics-optional"></a>3. lépés: Adja meg a metrikák (nem kötelező)

Metrikák eszköz előfordulhat, hogy jelentéseket küldhetnek vissza konfigurációs tartalom alkalmazása miatt a különböző állapotok összefoglaló számát adja meg. Például létrehozhat egy metrikát függőben lévő módosításait, egy metrika a hibákat és egy metrikát sikeres módosításait.

1. Adjon meg egy nevet **metrika neve**
1. Adjon meg egy lekérdezést a **metrika feltételek**.  A lekérdezés alapú eszközön iker jelentett tulajdonságait.  A metrika a lekérdezés által visszaadott sorok számát jelöli.

Például:`SELECT deviceId FROM devices WHERE properties.reported.chillerWaterSettings.status='pending'`

Megadhat egy záradékot, hogy a konfigurációt alkalmazta, például: `SELECT deviceId FROM devices WHERE configurations.[[yourconfigname]].status='Applied'` többek között a zárójelek.


### <a name="step-4-target-devices"></a>4. lépés: A Céleszközök számára

Az eszköz twins a címkék tulajdonságot használja, amelyekre az adott eszközöket, amelyek megkapják ezt a konfigurációt.  Eszköz-eszközök is célba iker jelentett tulajdonságait.

Mivel több konfigurációt is céloz ugyanarra az eszközre, adjon minden konfigurációs számot. Ha valaha is ütközés esetén az a legmagasabb prioritású wins-konfigurációt. 

1. Egy pozitív egész számot adjon meg a konfigurációs **prioritás**. A legmagasabb prioritású legnagyobb numerikus értéket számít. Ha két konfiguráció ugyanazt a prioritást, a legtöbb létrehozott egy közelmúltban wins. 
1. Adjon meg egy **céloz feltétel** határozza meg, hogy mely eszközök lesz ezzel a konfigurációval való. A feltétel alapján eszköz iker címkék vagy eszköz iker tulajdonságok jelentette, és meg kell felelnie a kifejezés formátumban. Például `tags.environment='test'` vagy `properties.reported.chillerProperties.model='4000x'`. 
1. Válassza ki **következő** áthelyezése az utolsó lépését.

### <a name="step-5-review-configuration"></a>5. lépés: Konfiguráció áttekintése

Ellenőrizze a konfigurációs adatokat, majd válasszon **Submit**.

## <a name="monitor-a-configuration"></a>A figyelő a konfigurációs

Egy konfigurációs részleteit megtekintheti, és figyelheti az eszközök is fut, tegye a következőket:

1. Az a [Azure-portálon][lnk-portal], keresse fel az IoT hub. 
1. Válassza ki **IoT-eszközök konfigurációját (előzetes verzió)**.
1. Vizsgálja meg a konfigurációs listát. Az egyes konfigurációs tekintheti meg a következő adatokat:
   * **Azonosító** -konfiguráció neve.
   * **Cél feltétel** -célzott eszközeiket használt lekérdezés.
   * **Prioritás** -a prioritást, a konfiguráció rendelt.
   * **Létrehozás ideje** – az a konfiguráció létrehozásának időbélyegző. Az időbélyegző szolgál, bontsa ki, amikor két konfigurációban a azonos prioritással rendelkezik. 
   * **Rendszer metrikák** -metrikák IoT-központ számítják ki és fejlesztők nem szabható testre. Megcélzott eszköz twins a célként megadott feltételnek megfelelő számát adja meg. Alkalmazza a megadott eszköz twins, amely módosította a konfigurációt, ami magában foglalhatja a részleges módosítás arra az esetre, ha egy önálló, magasabb prioritású konfigurációs is végrehajtott módosítások számát. 
   * **Egyéni metrikák** -metrikák eszköz iker lekérdezéseket, a fejlesztő megadott jelentett tulajdonságok.  Legfeljebb öt egyéni metrikák definiálható konfigurációs. 
   
1. Válassza ki a figyelni kívánt konfigurációról.  
1. Vizsgálja meg a konfigurációs adatait. Lapok segítségével megtekintheti az eszközöket, amelyek a konfigurációt kapott részletes adatait: 
   * **Cél feltétel** -az eszközöket, amelyek a célként megadott feltételnek megfelelő. 
   * **Metrikák** -rendszer metrikák és egyéni metrikák listáját.  Jelölje ki a metrika a legördülő listán, és jelölje be minden mérőszám számlálási eszközök listáját megtekintheti **nézet eszközök**.
   * **A két eszközbeállítások** – a kettős eszközbeállításokat, amelyek a konfigurációs beállításokat. 
   * **Konfigurációs címkék** -kulcs-érték párok használt konfigurációt.  Címkék befolyásolni funkciót. 

## <a name="modify-a-configuration"></a>A konfiguráció módosítása

A konfiguráció módosításakor a azonnal replikálja a módosításokat az összes megcélzott eszköz. 

Ha frissíti a cél feltétel, a következő frissítéseket fordulhat elő:
* Ha egy eszköz iker nem felelt meg a régi cél feltétel, de az új cél feltétel és a beállítások az adott eszköz iker a legmagasabb prioritású, ez a konfiguráció az eszköz a két van alkalmazva. 
* Ha egy eszköz iker már nem felel meg a célként megadott feltétel, a beállítások a konfigurációs eltávolításra kerül, és az eszköz iker módosítani fogja a következő legmagasabb prioritású virtuális gép konfigurációja. 
* Ha egy eszköz iker már nem ez a konfiguráció jelenleg fut a célként megadott feltétel, és minden egyéb konfigurációt cél állapota nem felel meg, a beállítások a konfigurációs eltávolításra kerül, és nem módosít a kettős kerül sor. 

A konfiguráció módosításához tegye a következőket: 

1. Az a [Azure-portálon][lnk-portal], keresse fel az IoT hub. 
1. Válassza ki **IoT-eszközök konfigurációját (előzetes verzió)**. 
1. Válassza ki a módosítani kívánt konfigurációról. 
1. Frissítések készítése a következő mezőket: 
   * Cél feltétel 
   * Címkék 
   * Prioritás 
   * Mérőszámok
1. Kattintson a **Mentés** gombra.
1. Kövesse a [figyelő a konfigurációs] [horgonyzási-figyelő] bemutató megkezdik a módosításokat. 

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Ha töröl egy konfigurációt, bármely eszköz twins a következő legmagasabb prioritású konfigurációs igénybe. Eszköz twins nem felelnek meg cél más beállításokat, ha nincs más beállítások érvényesek. 

1. Az a [Azure-portálon][lnk-portal], keresse fel az IoT hub. 
1. Válassza ki **IoT-eszközök konfigurációját (előzetes verzió)**. 
1. A jelölőnégyzet segítségével válassza ki a törölni kívánt konfigurációról. 
1. Válassza a **Törlés** elemet.
1. A kérdés kérni fogja annak megerősítéséhez.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan konfigurálhatja és figyelheti a léptékű az IoT-eszközök. Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Az IoT Hub eszköz identitásai tömeges kezelése][lnk-bulkIDs]
* [Az IoT-központ metrikák][lnk-metrics]
* [Figyelési műveletek][lnk-monitor]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

Az IoT Hub eszköz kiépítése szolgáltatás segítségével nulla-érintés engedélyezése megismeréséhez just-in-time kiépítés, lásd: 

* [Az Azure IoT Hub eszköz-üzembehelyezési szolgáltatás][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
