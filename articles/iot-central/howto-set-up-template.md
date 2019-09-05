---
title: Eszköz sablonjának beállítása Azure IoT Central alkalmazásban | Microsoft Docs
description: Megtudhatja, hogyan állíthat be egy eszközt a mérésekkel, a beállításokkal, a tulajdonságokkal, a szabályokkal és az irányítópulttal.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 63c0a04a6d18d6af850b1492d2efa9df9aa65219
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877377"
---
# <a name="set-up-a-device-template"></a>Eszközsablon beállítása

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Az eszköz sablonja egy olyan terv, amely meghatározza egy Azure IoT Central-alkalmazáshoz csatlakozó eszköz típusának jellemzőit és viselkedését.

A Builder például létrehozhat egy eszköz sablont egy csatlakoztatott ventilátorhoz, amely a következő jellemzőkkel rendelkezik:

- Hőmérséklet telemetria mérése
- Hely mérése
- Ventilátoros motor hiba eseményének mérése
- Ventilátor működési állapotának mérése
- Ventilátor sebességének beállítása
- Riasztásokat küldő szabályok
- Irányítópult, amely átfogó képet nyújt az eszközről

Ebből az eszköz sablonból az operátor létrehozhat és összekapcsolhat valódi ventilátoros eszközöket olyan nevekkel, mint például a **ventilátor-1** és a **ventilátor-2**. Ezek a ventilátorok olyan mérésekkel, beállításokkal, tulajdonságokkal, szabályokkal és irányítópulttal rendelkeznek, amelyeket az alkalmazás felhasználói figyelnek és kezelhetnek.

> [!NOTE]
> Csak az építők és a rendszergazdák hozhatnak létre, szerkeszthetnek és törölhetnek eszközöket. Bármely felhasználó létrehozhat eszközöket a **Device Explorer** oldalon a meglévő eszközök sablonjaiból.

## <a name="create-a-device-template"></a>Eszköz sablonjának létrehozása

1. Navigáljon az **eszközök sablonjai** lapra.

2. Sablon létrehozásához először válassza az **+ új**lehetőséget.

3. A gyors kezdéshez válasszon a meglévő, előre elkészített sablonok közül. Ellenkező esetben válassza az **Egyéni**lehetőséget, adjon meg egy nevet, majd kattintson a **Létrehozás** gombra a saját sablon kiépítéséhez a semmiből.

   ![Eszköz sablonjának könyvtára](./media/howto-set-up-template/newtemplate.png)

4. Egyéni sablon létrehozásakor megtekintheti az új sablon **eszköz adatai** lapját. IoT Central automatikusan létrehoz egy szimulált eszközt az eszköz létrehozásakor. A szimulált eszköz lehetővé teszi az alkalmazás működésének tesztelését a valódi eszköz csatlakoztatása előtt.

A következő szakaszok az **eszköz sablonja** oldalon található lapokat ismertetik.

## <a name="measurements"></a>Mérések

A mérések az eszközről származó adatok. Több mérést is hozzáadhat az eszköz sablonhoz az eszköz képességeinek megfelelően.

- A **telemetria** mérések az eszköz által az idő múlásával összegyűjtött numerikus adatpontok. Folyamatos adatfolyamként jelennek meg. Ilyen például a hőmérséklet.
- Az **esemény** -mérések olyan időponthoz mért adatmennyiségek, amelyek az eszközön valamilyen jelentőséget jelentenek. A súlyossági szint az esemény fontosságát jelöli. Ilyen például a ventilátoros motor hibája.
- Az **állapot** -mérések az eszköz vagy annak összetevőinek állapotát jelzik egy adott időszakban. A ventilátor mód például úgy definiálható, mint a két lehetséges állapot .
- A **hely** mérése az eszköz hosszúsági és szélességi koordinátái az adott időszakra vonatkozóan. Egy ventilátor például áthelyezhető az egyik helyről a másikra.

### <a name="create-a-telemetry-measurement"></a>Telemetria-mérés létrehozása

Új telemetria-mérés hozzáadásához válassza az **+ új mérték**lehetőséget, válassza a **telemetria** lehetőséget a mérték típusaként, és adja meg az űrlap részleteit.

> [!NOTE]
> Az eszköz sablonjának neveinek meg kell egyezniük a megfelelő eszköz kódjában található nevekkel, hogy a telemetria-mérték megjelenjen az alkalmazásban, amikor valódi eszköz csatlakozik. Ha a beállításokat, az eszköz tulajdonságait és a parancsokat konfigurálja, akkor a következő részben ismertetett módon adja meg az eszköz sablonját.

Hozzáadhat például egy új hőmérséklet-telemetria mérést:

| Megjelenítendő név        | Mező neve    |  Mértékegységek    | Min   |Max|
| --------------------| ------------- |-----------|-------|---|
| Hőmérséklet         | ideiglenes          |  degC     |  0    |100|

!["Telemetria létrehozása" űrlap a hőmérséklet-mérés részleteivel](./media/howto-set-up-template/measurementsform.png)

A **Mentés**gombra kattintva a **hőmérséklet** -mérés megjelenik a mértékegységek listájában. Rövid idő alatt a szimulált eszköz hőmérsékleti adatainak vizualizációja látható.

A telemetria megjelenítésekor a következő összesítési lehetőségek közül választhat: Átlagos, minimális, maximális, összeg és darabszám. Az **átlag** érték van kiválasztva az alapértelmezett összesítésként a diagramon.

> [!NOTE]
> A telemetria-mérés adattípusa lebegőpontos szám.

### <a name="create-an-event-measurement"></a>Esemény-mérés létrehozása

Új esemény mértékének hozzáadásához válassza az **+ új mérték** lehetőséget, majd válassza az **esemény** lehetőséget a mérték típusaként. Adja meg a részleteket az **esemény létrehozása** űrlapon.

Adja meg az esemény **megjelenítendő nevét**, a **mező nevét**és a **súlyossági** adatokat. A következő három elérhető súlyossági szint közül választhat: **Hiba**, **Figyelmeztetés**és **információ**.

Hozzáadhat például egy új **ventilátoros motort jelző** eseményt.

| Megjelenítendő név        | Mező neve    |  Alapértelmezett súlyosság |
| --------------------| ------------- |-----------|
| Ventilátormotor-hiba     | fanmotorerror |  Hiba    |

!["Esemény létrehozása" űrlap a ventilátoros motorral kapcsolatos esemény részleteivel](./media/howto-set-up-template/eventmeasurementsform.png)

Miután kiválasztotta a **Save (Mentés**) lehetőséget, a **ventilátor motoros hibák** mérése megjelenik a mértékegységek listájában. Rövid idő alatt megtekintheti az esemény adatainak vizualizációját a szimulált eszközről.

Egy esemény további részleteinek megtekintéséhez válassza ki az esemény ikonját a diagramon:

![A "fan-motor hibája" esemény részletei](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Az esemény mértékének adattípusa karakterlánc.

### <a name="create-a-state-measurement"></a>Állapot mérésének létrehozása

Új állapot mértékének hozzáadásához válassza az **+ új mérték** gombot, és válassza az **állapot** lehetőséget a mérték típusaként. Adja meg a részleteket az **állapot létrehozása** űrlapon.

Adja **meg a megjelenítendő név**, a **mező neve**és az állapot **értékeinek** részleteit. Minden érték rendelkezhet egy megjelenítendő névvel, amelyet akkor fog használni, amikor az érték megjelenik a diagramokban és a táblákban.

Hozzáadhat például egy új **ventilátor mód** állapotot, amely két lehetséges értékkel rendelkezik, amelyeket az eszköz képes elküldeni, **működni** és **leállítani**.

| Megjelenítendő név | Mező neve    |  1\. érték   | Megjelenítendő név | 2\. érték    |Megjelenítendő név  |
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Ventilátor mód     | fanmode       |  1         | Üzemel    |     0      | Leállítva      |

!["Állapot szerkesztése" űrlap a ventilátor mód részleteivel](./media/howto-set-up-template/statemeasurementsform.png)

A **Mentés**gombra kattintva a **ventilátor üzemmód** állapotának mérése megjelenik a mértékegységek listájában. Rövid idő alatt megtekintheti az állapotadatok vizualizációját a szimulált eszközről.

Ha az eszköz túl sok adatpontot küld egy kis időtartamon belül, az állapot mérése egy másik vizualizációval jelenik meg. Válassza ki a diagramot, hogy az adott időszakon belül az összes adatpontot időrendi sorrendben jelenítse meg. Az időtartomány leszűkíthető úgy is, hogy megjelenjen a diagramon ábrázolt mérés.

> [!NOTE]
> Az állapot mérésének adattípusa karakterlánc.

### <a name="create-a-location-measurement"></a>Hely mérésének létrehozása

Új hely mértékének hozzáadásához válassza az **+ új mérték**lehetőséget, válassza a **hely** lehetőséget a mérték típusaként, és adja meg a részleteket a **create mértékegység** űrlapon.

Hozzáadhat például egy új hely telemetria mértékét:

| Megjelenítendő név        | Mező neve    |
| --------------------| ------------- |
| Eszköz helye      |  assetloc     |

!["Hely létrehozása" űrlap a hely mértékének részleteivel](./media/howto-set-up-template/locationmeasurementsform.png)

A Save ( **Mentés**) gombra kattintva a **hely** mérőszáma megjelenik a mértékegységek listájában. Rövid idő alatt megtekintheti a hely adatainak vizualizációját a szimulált eszközről.

A hely megjelenítésekor a következő lehetőségek közül választhat: legutóbbi hely és hely előzményei. A **hely előzményei** csak a kiválasztott időtartományra érvényesek.

A hely mérésének adattípusa egy olyan objektum, amely hosszúságot, szélességet és választható magasságot tartalmaz. A következő kódrészlet a JavaScript-struktúrát mutatja be:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

A valódi eszköz csatlakoztatása után a mértékként hozzáadott hely frissül az eszköz által eljuttatott értékkel. A hely mértékének beállítása után [hozzáadhat egy térképet a hely megjelenítéséhez az eszköz irányítópultján](#add-a-location-measurement-in-the-dashboard).

## <a name="settings"></a>Beállítások

A beállítások vezérlik az eszközt. Lehetővé teszik, hogy az operátorok adatokat adjanak az eszköznek. Több olyan beállítást is hozzáadhat az eszközhöz, amely csempéként jelenik meg az operátorok számára a **Beállítások** lapon. Többféle típusú beállítást is hozzáadhat: szám, szöveg, dátum, váltógomb és szakasz felirata.

A beállítások a három állapot egyike lehet. Az eszköz ezeket az állapotokat jelenti.

- **Szinkronizálva**: Az eszköz a beállítás értékének megfelelően módosult.

- **Függőben**: Az eszköz jelenleg a beállítási értékre változik.

- **Hiba**: Az eszköz hibát adott vissza.

Hozzáadhat például egy új ventilátor sebességét úgy, hogy kiválasztja a **Settings (beállítások** ) lehetőséget, és beírja az új **szám** beállítást:

| Megjelenítendő név  | Mező neve    |  Mértékegységek  | Tizedesek |Kezdeti|
| --------------| ------------- |---------| ---------|---- |
| Ventilátor sebessége     | fanSpeed      | RPM     | 2        | 0   |

!["Szám konfigurálása" űrlap a sebességi beállítások részleteivel](./media/howto-set-up-template/settingsform.png)

A Save ( **Mentés**) gombra kattintva a **ventilátor sebessége** beállítás csempeként jelenik meg. Az operátor a **Device Explorer** lapon található beállítás használatával módosíthatja az eszköz ventilátorának sebességét.

## <a name="properties"></a>properties

A tulajdonságok az eszközhöz társított metaadatok, például rögzített eszköz helye és sorozatszáma. Több tulajdonság hozzáadása a **Tulajdonságok** lapon csempéként megjelenő sablonhoz. Egy tulajdonság típusa például szám, szöveg, dátum, váltógomb, eszköz tulajdonság, címke vagy rögzített hely. Az operátor meghatározza a tulajdonságok értékeit, amikor létrehoznak egy eszközt, és bármikor szerkeszthetik ezeket az értékeket. Az eszköz tulajdonságai csak olvashatók, és az eszközről az alkalmazásba kerülnek. Az operátorok nem változtathatják meg az eszköz tulajdonságait. Amikor egy valódi eszköz csatlakozik, az eszköz tulajdonságlapja frissül az alkalmazásban.

Két tulajdonságkategória érhető el:

- Az eszköz által az IoT Central alkalmazásnak jelentett _Tulajdonságok_ . Az eszköz tulajdonságai az eszköz által jelentett írásvédett értékek, és az alkalmazásban frissülnek, amikor valódi eszköz van csatlakoztatva.
- Az alkalmazásban tárolt és az operátor által szerkeszthető _alkalmazás-tulajdonságok_ . Az alkalmazás tulajdonságai csak az alkalmazásban tárolódnak, és az eszköz soha nem látja őket.

Például hozzáadhatja az eszköz utolsó kiszolgált dátumát új **Date** (alkalmazás) tulajdonságként a **Tulajdonságok** lapon:

| Megjelenítendő név  | Mező neve | Kezdeti érték   |
| --------------| -----------|-----------------|
| Utolsó szervizelés      | lastServiced        | 01/29/2019     |

!["Az utolsó szervizelt" űrlap konfigurálása a "tulajdonságok" lapon](./media/howto-set-up-template/propertiesform.png)

A Save ( **Mentés**) gombra kattintva a rendszer csempeként jeleníti meg az eszköz utolsó kiszolgált dátumát.

Miután létrehozta a csempét, módosíthatja az alkalmazás tulajdonság értékét a **Device Explorerban**.

### <a name="create-a-location-property"></a>Location tulajdonság létrehozása

Földrajzi kontextust biztosíthat az Azure IoT Centralban, és leképezheti a szélességi és hosszúsági koordinátákat, illetve az utca címeit. Azure Maps engedélyezi ezt a funkciót a IoT Centralban.

A hely két típusát adhatja hozzá:

- Az alkalmazásban tárolt **Location (alkalmazás) tulajdonság**. Az alkalmazás tulajdonságai csak az alkalmazásban tárolódnak, és az eszköz soha nem látja őket.
- A **hely eszköz tulajdonságként**, amelyet az eszköz az alkalmazásnak jelent. Az ilyen típusú tulajdonságot a rendszer a legjobb statikus helyen használja.

> [!NOTE]
> A tulajdonságként megadott hely nem rögzíti az előzményeket. Ha előzményekre van szükség, használja a hely mértékét.

#### <a name="add-location-as-an-application-property"></a>Hely hozzáadása alkalmazás-tulajdonságként

A Location (hely) tulajdonságot alkalmazás-tulajdonságként is létrehozhatja a IoT Central alkalmazásban Azure Maps használatával. Felveheti például az eszköz telepítési címe:

1. Navigáljon a **Tulajdonságok** lapra.

2. A könyvtárban válassza a **hely**lehetőséget.

3. A **megjelenítendő név**, a **Mezőnév**és (opcionálisan) **kezdeti érték** konfigurálása a helyhez.

    | Megjelenítendő név  | Mező neve | Kezdeti érték |
    | --------------| -----------|---------|
    | Telepítési címe | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   !["Hely konfigurálása" űrlap a hely részleteivel](./media/howto-set-up-template/locationcloudproperty2.png)

   Két támogatott formátum van a hely hozzáadásához:
   - **Hely címként**
   - **Hely koordinátákként**

4. Kattintson a **Mentés** gombra. Az operátor frissítheti a hely értékét a **Device Explorerban**.

#### <a name="add-location-as-a-device-property"></a>Hely hozzáadása eszköz tulajdonságként

A Location (hely) tulajdonságot az eszköz által jelentett eszköz tulajdonságként is létrehozhatja. Ha például nyomon szeretné követni az eszköz helyét:

1. Navigáljon a **Tulajdonságok** lapra.

2. Válassza ki az **eszköz tulajdonságot** a könyvtárból.

3. Adja meg a megjelenítendő név és a mező nevét, és válassza a **hely** lehetőséget az adattípus mezőben:

    | Megjelenítendő név  | Mező neve | Adattípus |
    | --------------| -----------|-----------|
    | Eszközhely | deviceLocation | location  |

   > [!NOTE]
   > A mezők neveinek meg kell egyezniük a megfelelő eszköz kódjában szereplő tulajdonságok neveivel.

   !["Az eszköz tulajdonságainak konfigurálása" űrlap a hely részleteivel](./media/howto-set-up-template/locationdeviceproperty2.png)

A valódi eszköz csatlakoztatása után a rendszer az eszköz tulajdonságként hozzáadott helyet frissíti az eszköz által eljuttatott értékkel. Miután konfigurálta a Location (hely) tulajdonságot, [hozzáadhat egy térképet a hely megjelenítéséhez az eszköz irányítópultján](#add-a-location-property-in-the-dashboard).

## <a name="commands"></a>Parancsok

A parancsok használatával távolról kezelhetők az eszközök. Lehetővé teszik a kezelők számára parancsok futtatását az eszközön. Több olyan parancsot is hozzáadhat az eszközhöz, amely csempéként jelenik meg az operátorok által használt **parancsok** lapon. Az eszköz előkészítője számára rugalmasan határozhatja meg a parancsokat az igényeinek megfelelően.

Miben különbözik a parancsok egy adott beállítástól?

- **Beállítás**: A beállítás az eszközre alkalmazni kívánt konfiguráció. Azt szeretné, hogy az eszköz mindaddig megőrzi ezt a konfigurációt, amíg meg nem változtatja. Például beállíthatja a fagyasztó hőmérsékletét, és ezt a beállítást a fagyasztó újraindításakor is meg szeretné jeleníteni.

- **Parancs**: Parancsok használatával azonnal futtathat egy parancsot az eszközön a IoT Centralból távolról. Ha egy eszköz nincs csatlakoztatva, a parancs túllépi az időkorlátot, és sikertelen lesz. Például egy eszköz újraindítására van szükség.

Hozzáadhat például egy új **echo** -parancsot a **parancsok** lapon, majd az **+ új parancs**lehetőség kiválasztásával és az új parancs részleteinek megadásával:

| Megjelenítendő név  | Mező neve | Alapértelmezett határidő-túllépés | Adattípus |
| --------------| -----------|---------------- | --------- |
| Echo parancs  | echo       |  30             | text      |

!["Parancs konfigurálása" űrlap az ECHO részleteivel](./media/howto-set-up-template/commandsecho1.png)

A Save ( **Mentés**) gombra kattintva a **echo** parancs csempeként jelenik meg, és készen áll arra, hogy a **Device Explorer** , amikor a valódi eszköz csatlakozik. Ahhoz, hogy a parancsok sikeresen fussanak, a parancs mezőinek meg kell egyezniük a megfelelő eszköz kódjában szereplő névvel.

[Itt található a minta C-eszköz kódjának hivatkozása.](https://github.com/Azure/iot-central-firmware/blob/ad40358906aeb8f2040a822ba5292df866692c16/MXCHIP/mxchip_advanced/src/AzureIOTClient.cpp#L34)

## <a name="rules"></a>Szabályok

A szabályok segítségével a kezelők közel valós időben figyelheti az eszközöket. A szabályok automatikusan kezdeményeznek olyan műveleteket, mint például az e-mailek küldése a szabály indításakor. Jelenleg az egyik szabály elérhető:

- **Telemetria-szabály**, amely akkor aktiválódik, ha a kiválasztott eszköz telemetria átlép egy megadott küszöbértéket. [További információ a telemetria-szabályokról](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Irányítópult

Az irányítópulton megtekintheti az eszközre vonatkozó információkat. Szerkesztőként hozzáadhat csempéket a laphoz, hogy a kezelők megértsék az eszköz viselkedését. Számos típusú irányítópult-csempét (például képet, vonalas diagramot, sávdiagram, fő teljesítménymutatót (KPI), beállításokat és tulajdonságokat, valamint címkét) adhat hozzá.

Hozzáadhat például egy **beállításokat és tulajdonságokat tartalmazó** csempét a beállítások és tulajdonságok aktuális értékeinek megjelenítéséhez az **irányítópult** lap és a tár csempéje segítségével:

!["Az eszköz részleteinek konfigurálása" űrlap a beállítások és a tulajdonságok részleteivel](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Most, amikor egy operátor megtekinti az irányítópultot a **Device Explorerban**, láthatják a csempét.

### <a name="add-a-location-measurement-in-the-dashboard"></a>Hely mértékének megadása az irányítópulton

Ha beállította a hely mértékét, az eszköz irányítópultján megjelenítheti a helyet egy térképen. A hely méréséhez lehetősége van a hely előzményeinek ábrázolására.

1. Navigáljon az **irányítópult** lapra.

1. Az eszköz irányítópultján válassza a **Térkép** lehetőséget a könyvtárból.

1. Adja meg a Térkép címét. A következő példában a cím **eszköz aktuális helye**található. Ezután válassza ki a **mérések** lapon korábban konfigurált hely mértékét. A következő példában az **eszköz helyének** mérése van kiválasztva:

   !["Map konfigurálása" űrlap a cím és a tulajdonságok részleteivel](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Kattintson a **Mentés** gombra. A Térkép csempe most megjeleníti a kiválasztott helyet.

Átméretezheti a Térkép csempéjét. Amikor egy operátor megtekinti az irányítópultot a **Device Explorerban**, az összes konfigurált irányítópult-csempét, beleértve a hely térképét is látható.

### <a name="add-a-location-property-in-the-dashboard"></a>Location tulajdonság hozzáadása az irányítópulton

Ha konfigurált egy Location (hely) tulajdonságot, az eszköz irányítópultján megjelenítheti a helyet egy térképsel.

1. Navigáljon az **irányítópult** lapra.

1. Az eszköz irányítópultján válassza a **Térkép** lehetőséget a könyvtárból.

1. Adja meg a Térkép címét. A következő példában a cím **eszköz aktuális helye**található. Ezután válassza ki a Location (hely) tulajdonságot, amelyet korábban a **Properties (Tulajdonságok** ) lapon konfigurált. A következő példában az **eszköz helyének** mérése van kiválasztva:

   ![Térkép űrlap konfigurálása a cím és a tulajdonságok részleteivel](./media/howto-set-up-template/locationcloudproperty6map.png)

1. Kattintson a **Mentés** gombra. A Térkép csempe most megjeleníti a kiválasztott helyet.

Átméretezheti a Térkép csempéjét. Amikor egy operátor megtekinti az irányítópultot a **Device Explorerban**, az összes konfigurált irányítópult-csempét, beleértve a hely térképét is látható.

Ha többet szeretne megtudni a csempék használatáról az Azure IoT Centralban, tekintse meg az [irányítópult-csempék használatát](howto-use-tiles.md)ismertető témakört.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan állíthatja be az eszköz sablonját az Azure IoT Central alkalmazásban, a következőket teheti:

- [Új sablon-verzió létrehozása](howto-version-device-template.md)
- [MXChip-IoT fejlesztői készlet-eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-devkit.md)
- [Általános ügyfélalkalmazás összekötése az Azure IoT Central-alkalmazással (node. js)](howto-connect-nodejs.md)
