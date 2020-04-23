---
title: Automatikus eszközkezelés az Azure IoT Hubbal | Microsoft dokumentumok
description: Több IoT-eszköz és -modul kezelése az Azure IoT Hub automatikus konfigurációival
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 276f115f579fbd1ab077722b220a4a0c6c571850
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025067"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatikus IoT-eszköz- és modulkezelés az Azure Portal használatával

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Az Azure IoT Hub automatikus eszközkezelése automatizálja a nagy eszközflották kezelésével kapcsolatos ismétlődő és összetett feladatok at. Az automatikus eszközfelügyelettel megcélozhatja az eszközök készletét a tulajdonságaik alapján, meghatározhatja a kívánt konfigurációt, majd lehetővé teheti az IoT Hub számára, hogy frissítse az eszközöket, amikor azok hatókörbe kerülnek. Ez a frissítés _automatikus eszközkonfigurációval_ vagy _automatikus modulkonfigurációval_történik, amely lehetővé teszi a befejezés és a megfelelőség összegzését, az egyesítés és az ütközések kezelését, valamint a konfigurációk szakaszos megközelítésben történő bevezetését.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Az automatikus eszközkezelés úgy működik, hogy frissíti az ikereszközök vagy a modulikrek készletét a kívánt tulajdonságokkal, és jelentést tesz egy, két jelentett tulajdonságon alapuló összegzést.  Bevezet egy új osztály és JSON dokumentum nevű *konfiguráció,* amely három részből áll:

* A **célfeltétel** határozza meg az eszköz ikrek vagy a modul ikrek frissíteni kell. A célfeltétel két címke és/vagy jelentett tulajdonságok lekérdezéseként van megadva.

* A **céltartalom** határozza meg a kívánt tulajdonságokat kell hozzáadni vagy frissíteni a megcélzott eszköz twins vagy modul twins. A tartalom tartalmazza a módosítani kívánt tulajdonságok szakaszának elérési útját.

* A **mérőszámok határozzák** meg a különböző konfigurációs állapotok , például a **Sikeres**, **a Folyamatban**és a **Hiba**összegzését . Az egyéni metrikák két jelentett tulajdonság lekérdezéseként vannak megadva.  A rendszermetrikák az alapértelmezett metrikák, amelyek az ikerfrissítés állapotát mérik, például a megcélzott ikerikrek számát és a sikeresen frissített ikrek számát.

Az automatikus konfigurációk először röviddel a konfiguráció létrehozása után, majd ötperces időközönként futnak. Metrikák lekérdezések fut minden alkalommal, amikor az automatikus konfiguráció fut.

## <a name="implement-twins"></a>Ikrek megvalósítása

Az automatikus eszközkonfigurációk hoz használ-as eszközök ikerállapot a felhő és az eszközök közötti szinkronizáláshoz.  További információ: [Eszközök ikerállapotának megismerése és használata az IoT hubon](iot-hub-devguide-device-twins.md).

Az automatikus modulkonfigurációk hoz a modul twins a felhő és a modulok közötti állapot szinkronizálásához. További információ: [A modultwins az IoT Hubban.](iot-hub-devguide-module-twins.md)

## <a name="use-tags-to-target-twins"></a>Címkék használata ikrek célzásához

A konfiguráció létrehozása előtt meg kell adnia, hogy mely eszközöket vagy modulokat kívánja befolyásolni. Az Azure IoT Hub azonosítja az eszközöket, és címkéket használ az ikereszközben, és azonosítja a modulokat a modul ikercímkék használatával. Minden eszköz vagy modul több címkével is rendelkezhet, és bármilyen módon meghatározhatja őket, amely a megoldásnak van értelme. Ha például különböző helyeken kezeli az eszközöket, adja hozzá a következő címkéket egy ikereszközhöz:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az IoT hub. 

2. Válassza az **IoT-eszköz konfigurációját.**

3. Válassza **az Eszközkonfiguráció hozzáadása** vagy a **Modulkonfiguráció hozzáadása**lehetőséget.

   ![Eszközkonfiguráció vagy modulkonfiguráció hozzáadása](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

A konfiguráció létrehozásához öt lépésből áll. A következő szakaszok végigvezetik őket. 

### <a name="name-and-label"></a>Név és címke

1. Adjon a konfigurációnak egy egyedi nevet, amely legfeljebb 128 kisbetűs. Kerülje a szóközöket `& ^ [ ] { } \ | " < > /`és a következő érvénytelen karaktereket: .

2. Címkék hozzáadásával nyomon követheti a konfigurációkat. A címkék **a név**, **értékpárok,** amelyek leírják a konfigurációt. Például `HostPlatform, Linux` vagy `Version, 3.0.1`.

3. Válassza a **Tovább** gombot a következő lépésre való ugráshoz. 

### <a name="specify-settings"></a>Beállítások megadása

Ez a szakasz határozza meg a célzott eszköz vagy modul twins beállítandó tartalmat. Minden beállításkészlethez két bemenet tartozik. Az első az ikerelérési út, amely a JSON szakasz elérési útja az iker kívánt tulajdonságokat, amelyek beállítva.  A második az adott szakaszba beszúrandó JSON-tartalom. 

Beállíthatja például az ikerelérési `properties.desired.chiller-water` utat, majd megadhatja a következő JSON-tartalmat: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Az ikerútvonal és -tartalom beállítása](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


Egyéni beállításokat úgy is megadhat, hogy megadja a teljes ikerútvonalat, és zárójelek nélkül adja meg az értéket. Az ikerelérési út `properties.desired.chiller-water.temperature`esetén például `66`állítsa a tartalmat . Ezután hozzon létre egy új ikerbeállítást a nyomás tulajdonsághoz. 

Ha két vagy több konfiguráció ugyanazt a két útvonalat célozza meg, a legmagasabb prioritású konfiguráció tartalma lesz érvényes (a prioritás a 4. lépésben van meghatározva).

Ha el szeretne távolítani egy meglévő tulajdonságot, adja meg a tulajdonság értékét a értékére. `null`

További beállításokat az **Eszköziker-beállítás hozzáadása** vagy **a Moduliker-beállítás hozzáadása**lehetőséget választva adhat meg.

### <a name="specify-metrics-optional"></a>Metrikák megadása (nem kötelező)

Metrikák összegzése a különböző állapotok, amelyek egy eszköz vagy modul jelentheti vissza a konfigurációs tartalom alkalmazása után. Létrehozhat például egy mérőszámot a függőben lévő beállítások változásaihoz, egy hibát, és egy mérőszámot a sikeres beállítások változásaihoz.

Minden konfigurációleg feljebb öt egyéni metrikával rendelkezhet. 

1. Adja meg a **metrikus név nevét.**

2. Adjon meg egy lekérdezést a **Metrikus feltételekhez.**  A lekérdezés az ikereszköz által jelentett tulajdonságokon alapul.  A metrika a lekérdezés által visszaadott sorok számát jelöli.

Például:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

A konfiguráció tanoncát tartalmazó záradékot is megadhat, például: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Ha a konfigurált modulokról szóló jelentéshez `moduleId` mérőszámot készít, válassza a (K) `devices.modules`lehetőséget. Például:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Céleszközök

Használja a címkék et az ikrek, hogy a cél az adott eszközök vagy modulok, amelyek megkapják ezt a konfigurációt. Iker jelentett tulajdonságokat is megcélozhat.

Az automatikus eszközkonfigurációk csak az eszköz ikercímkéit tudják megcélozni, az automatikus modulkonfigurációk pedig csak a modul ikercímkéit. 

Mivel több konfiguráció is megcélozhatja ugyanazt az eszközt vagy modult, minden konfigurációnak prioritási számra van szüksége. Ha bármikor ütközik, a legmagasabb prioritású konfiguráció nyer. 

1. Adjon meg egy pozitív egész szám a konfiguráció **prioritás .** A legmagasabb numerikus érték a legmagasabb prioritásnak számít. Ha két konfiguráció prioritási számmal rendelkezik, akkor a legutóbb létrehozott konfiguráció nyer. 

2. Adja meg a **cél feltételt** annak meghatározásához, hogy mely eszközök vagy modulok lesznek megcélozva ezzel a konfigurációval. A feltétel ikercímkéken vagy ikerjelentett tulajdonságokon alapul, és meg kell egyeznie a kifejezés formátumának. 

   Az automatikus eszközkonfigurációhoz megadhatja, hogy csak a címkét vagy a jelentett tulajdonságot célozza meg. Például `tags.environment='test'` vagy `properties.reported.chillerProperties.model='4000x'`. Megadhatja, `*` hogy az összes eszközt célozza meg. 
   
   Az automatikus modulkonfiguráció, lekérdezés segítségével adja meg a címkéket vagy jelentett tulajdonságokat az IoT hub regisztrált modulok. Például `from devices.modules where tags.environment='test'` vagy `from devices.modules where properties.reported.chillerProperties.model='4000x'`. A helyettesítő karakter nem használható az összes modul célzására. 

3. Válassza a **Tovább** gombot az utolsó lépésre való továbblépéshez.

### <a name="review-configuration"></a>Konfiguráció áttekintése

Tekintse át a konfigurációs adatokat, majd válassza a **Küldés lehetőséget.**

## <a name="monitor-a-configuration"></a>Konfiguráció figyelése

A konfiguráció részleteinek megtekintéséhez és az azt futtató eszközök figyeléséhez kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az IoT hub. 

2. Válassza az **IoT-eszköz konfigurációját.**

3. Vizsgálja meg a konfigurációs listát. Az egyes konfigurációkhoz a következő részleteket tekintheti meg:

   * **ID** - a konfiguráció neve.

   * **Célfeltétel** – a célzott eszközök vagy modulok definiálásához használt lekérdezés.

   * **Prioritás** – a konfigurációhoz rendelt prioritási szám.

   * **Létrehozási idő** – a konfiguráció létrehozásának időbélyegzője. Ez az időbélyeg a kapcsolatok megszakítására szolgál, ha két konfiguráció azonos prioritással rendelkezik. 

   * **Rendszermetrikák** – az IoT Hub által kiszámított metrikák, amelyeket a fejlesztők nem szabhatnak testre. A targeted a célfeltételnek megfelelő ikereszközök számát adja meg. Alkalmazza a konfiguráció által módosított ikereszközök számát, amely részleges módosításokat is tartalmazhat abban az esetben, ha egy külön, magasabb prioritású konfiguráció is módosította. 

   * **Egyéni metrikák** – a fejlesztők által két jelentett tulajdonság lekérdezéseként megadott metrikák.  Konfigurációnként legfeljebb öt egyéni metrika határozható meg. 
   
4. Válassza ki a figyelni kívánt konfigurációt.  

5. Vizsgálja meg a konfiguráció részleteit. A lapok segítségével megtekintheti a konfigurációt kapott eszközök konkrét részleteit.

   * **Célfeltétel** – a célfeltételnek megfelelő eszközök vagy modulok. 

   * **Metrikák** – a rendszer metrikák és egyéni metrikák listája.  Megtekintheti az egyes mutatókhoz megszámlált eszközök vagy modulok listáját, ha a legördülő menüben kiválasztja a metrikát, majd kiválasztja az **Eszközök** megtekintése vagy **a Modulok megtekintése**lehetőséget.

   * **Eszköz iker beállítások** vagy **modul Twin Beállítások** - az iker beállításokat, amelyek a konfiguráció által beállított. 

   * **Konfigurációs címkék** – a konfiguráció leírására használt kulcs-érték párok.  A címkék nincsenek hatással a működésre. 

## <a name="modify-a-configuration"></a>Konfiguráció módosítása

Konfiguráció módosításakor a módosítások azonnal replikálódnak az összes megcélzott eszközre vagy modulra. 

Ha frissíti a célfeltételt, a következő frissítések jelennek meg:

* Ha egy iker nem felel meg a régi célfeltételnek, de megfelel az új célfeltételnek, és ez a konfiguráció az iker számára a legmagasabb prioritás, akkor ez a konfiguráció lesz alkalmazva. 

* Ha egy iker, amely jelenleg ezt a konfigurációt futtatja, már nem felel meg a célfeltételnek, a konfiguráció beállításai törlődnek, és az ikertestvérét a következő legmagasabb prioritású konfiguráció módosítja. 

* Ha egy iker, amely jelenleg ezt a konfigurációt futtatja, már nem felel meg a célfeltételnek, és nem felel meg más konfigurációk célfeltételének, akkor a konfiguráció beállításai törlődnek, és az ikerkapcsolaton nem történik más módosítás. 

Konfiguráció módosításához kövesse az alábbi lépéseket: 

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az IoT hub. 

2. Válassza az **IoT-eszköz konfigurációját.** 

3. Válassza ki a módosítani kívánt konfigurációt. 

4. A következő mezők frissítése: 

   * Célfeltétel 
   * Címkék 
   * Prioritás 
   * Mérőszámok

4. Kattintson a **Mentés** gombra.

5. Kövesse a [Konfiguráció figyelése](#monitor-a-configuration) a konfigurációt című részben leírt lépéseket a változások bevezetésének megtekintéséhez. 

## <a name="delete-a-configuration"></a>Konfiguráció törlése

Amikor töröl egy konfigurációt, minden eszköz twins veszi a következő legmagasabb prioritású konfiguráció. Ha az ikereszközök nem felelnek meg más konfigurációk célfeltételének, akkor a rendszer nem alkalmaz más beállításokat. 

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az IoT hub. 

2. Válassza az **IoT-eszköz konfigurációját.** 

3. A jelölőnégyzet segítségével válassza ki a törölni kívánt konfigurációt. 

4. Válassza a **Törlés** elemet.

5. A kérdés kérni fogja, hogy erősítse meg.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja és figyelheti az IoT-eszközöket nagy méretekben. Az Azure IoT Hub kezeléséről az alábbi hivatkozásokra kattintva olvashat bővebben:

* [IoT Hub-eszközidentitások csoportos kezelése](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)

Az IoT Hub-eszközkiépítési szolgáltatás használatával a nulla érintéses, just-in-time kiépítés engedélyezéséről a következő témakört kell ismertennie: 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
