---
title: Az Azure IoT Central alkalmazáshoz eszköz sablon beállítása |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be egy eszköz sablon mérések, beállítások, tulajdonságainak, szabályok és a egy irányítópultot.
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1306b97ce7fcf2cd429be31bc99d022de53805d6
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259856"
---
# <a name="set-up-a-device-template-new-ui-design"></a>Állítson be egy eszköz sablont (új felhasználói felület tervezése)

Egy eszköz, amely meghatározza a jellemzőit és a egy eszköz, amely csatlakozik az Azure IoT Central alkalmazáshoz típusú viselkedéseit tervrajz áll.

Például egy jelentéskészítő egy IoT-csatlakoztatott ventilátor v: rendelkező eszköz sablon létrehozása

- Hőmérsékleti telemetria mérési

- Ventilátor motor hiba esemény mérési

- Működési állapot mérési ventilátor

- Ventilátor sebesség beállítása

- Location tulajdonság

- Szabályok, amelyek az értesítések küldése

- Irányítópult, amelyen az eszköz átfogó képet

Eszköz sablonból, az operátornak hozhat létre és valódi ventilátor eszközök csatlakoztatása a neveket például **ventilátor.-1** és **ventilátor-2**. Az összes alábbi ventilátorok mérések, beállítások, tulajdonságainak, szabályok és egy irányítópultot, amely az alkalmazás felhasználóinak monitorozni és kezelni a rendelkezik.

> [!NOTE]
> Csak a kapcsolat építői és a rendszergazdák létrehozása, szerkesztése és törlése eszköz sablonok. Minden felhasználó létrehozhat eszközök a a **Device Explorer** meglévő eszközsablonok lapot.

## <a name="create-a-device-template"></a>Egy eszköz-sablon létrehozása

1. Keresse meg a **eszközsablonok** lapot.

2. Hozzon létre egy sablont, első lépésként válassza ki **+ új**.

3. Első lépésként gyorsan a meglévő, előre elkészített sablonok közül választhat. Ellenkező esetben válassza **egyéni**, adjon meg egy nevet, majd kattintson **létrehozás** teljesen új saját sablon létrehozásához.

   ![Eszköz könyvtár](./media/howto-set-up-template/newtemplate.png)

4. Amikor létrehoz egy egyéni sablont, láthatja a **eszközadatok** lap az új eszköz sablon. IoT-központ automatikusan hozza létre a szimulált eszköz, amikor egy eszköz sablont hoz létre. Szimulált eszköz lehetővé teszi a az alkalmazás viselkedésének valós eszköz csatlakoztatása előtt.

Az alábbi szakaszok ismertetik a különböző lapokra a **eszköz sablon** lapot.

## <a name="measurements"></a>Mérések

Mértékek az eszközről az adatokat. Az eszköz a sablon megfelelő az eszköz képességeit több mérések adhat hozzá.

- **Telemetria** mérések, amely az eszköz által összegyűjtött numerikus adatpontok. Ezek még folyama jelöli. Ilyen például a hőmérsékletet.
- **Esemény** mérések időponthoz adatok, amelyek valamit az eszközön a jelentősége. Egy súlyossági szintet az esemény fontossága jelöli. Példa ventilátor motor hiba.
- **Állapot** mérések az eszköz vagy összetevőinek állapotát képviselik egy időszakon belül. Például egy ventilátor mód lehet meghatározni, hogy **operációs** és **leállítva** , a két lehetséges állapota.

### <a name="create-a-telemetry-measurement"></a>Telemetria mérték létrehozása

Egy új telemetriai mérési hozzáadásához válassza **+ új mérték**, válassza a **Telemetriai** a mérték, írja be, és adja meg a részleteket az űrlapon.

> [!NOTE]
> Az eszköz sablon egyeznie kell a megfelelő eszköz kódot ahhoz, hogy a telemetria mérés esetén valós eszköz csatlakoztatva van, az alkalmazás megjelenítendő tulajdonságnevek. Végezze el ugyanezt konfigurálásakor beállításait, az eszköztulajdonságok és a parancsok, továbbra is az alábbi szakaszok az eszköz sablon megadására.

Például egy új hőmérsékleti telemetria mérték is hozzáadhat:

| Megjelenítendő név        | Mezőnév    |  Egység    | Min   |Max|
| --------------------| ------------- |-----------|-------|---|
| Hőmérséklet         | TEMP          |  degC     |  0    |100|

!["A telemetriai adatok létrehozása" űrlapján hőmérséklet mérési adatait](./media/howto-set-up-template/measurementsform.png)

Kiválasztása után **mentése**, a **hőmérséklet** mérési mérések listájában jelenik meg. Egy rövid ideig a szimulált eszközről érkező hőmérsékleti adatok a Vizualizáció látható.

Telemetria megjelenítése, ha az alábbi összesítési lehetőségek közül választhat: Átlagos, minimális, maximális, Sum és száma. **Átlagos** van kiválasztva az alapértelmezett összesítés a diagramra. 

> [!NOTE]
> A telemetriai adatok mérték adattípusa lebegőpontos szám.

### <a name="create-an-event-measurement"></a>Egy esemény-mérték létrehozása

Egy új esemény mérési hozzáadásához válassza **+ új mérték** válassza **esemény** mérési típusaként. Adja meg, hogy a **esemény létrehozása** űrlap.

Adja meg a **megjelenítendő név**, **mezőnév**, és **súlyossági** az esemény részleteit. Súlyosság három szintjei közül választhat: **Hiba**, **figyelmeztetés**, és **információk**.

Például hozzáadhat egy új **ventilátor Motor hiba** esemény.

| Megjelenítendő név        | Mezőnév    |  Alapértelmezett súlyosság |
| --------------------| ------------- |-----------|
| Ventilátormotor-hiba     | fanmotorerror |  Hiba    |

!["Esemény létrehozása" űrlapján ventilátor motor esemény részletei](./media/howto-set-up-template/eventmeasurementsform.png)

Kiválasztása után **mentése**, a **ventilátor Motor hiba** mérési mérések listájában jelenik meg. Egy rövid ideig láthatja a képi megjelenítés, a szimulált eszközről az eseményadatok.

További részletekre kíváncsi egy eseménnyel kapcsolatban, kattintson a diagramra a esemény ikonra:

![A "Ventilátor Motor Error" esemény részletei](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Az esemény mérték adattípusa karakterlánc.

### <a name="create-a-state-measurement"></a>Egy állapot-mérték létrehozása

Új állapot a mérés hozzáadásához válassza a **+ új mérték** gombra, majd **állapot** mérési típusaként. Adja meg, hogy a **létrehozása állapot** űrlap.

Adja meg az adatait **megjelenített név**, **mezőnév**, és **értékek** az állapot. Minden egyes érték is használható, ha az érték jelenik meg, diagramok és táblázatok megjelenített név.

Például hozzáadhat egy új **ventilátor mód** állapota, az eszköz küldhet, két lehetséges értékekkel rendelkező **operációs** és **leállítva**.

| Megjelenítendő név | Mezőnév    |  1. érték   | Megjelenítendő név | 2. érték    |Megjelenítendő név  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Ventilátor mód     | fanmode       |  1         | Üzemel    |     0      | Leállítva      |

![Ventilátor mód részleteit tartalmazó "Szerkesztés állapot" képernyő](./media/howto-set-up-template/statemeasurementsform.png)

Kiválasztása után **mentése**, a **ventilátor mód** állapot mérési mérések listájában jelenik meg. Egy rövid, Önnek pedig a szimulált eszközről az állapot adatok a Vizualizáció látható.

Az eszköz a kis időtartam túl sok adatpont küld, ha az állapot mértéket egy másik Vizualizáció jelenik meg. Válassza ki a diagram összes adatponttal megtekintéséhez időrendi sorrendben meghatározott időszakon belül. Az időtartomány, tekintse meg a diagramon ábrázolt mérését is szűkíthető.

> [!NOTE]
> Az állapot mérték adattípusa karakterlánc.

## <a name="settings"></a>Beállítások

Beállítások egy eszköz vezérlésére. Adja meg a bemeneti adatok az eszköz operátorokat lehetővé teszik. Csempék formájában jelenik meg az eszköz-sablon több beállítást is hozzáadhat a **beállítások** fülre az operátorok használatához. Számos különböző típusú beállításokat is hozzáadhat: szám, szöveg, dátum, be-vagy kikapcsolása, választéklista és szakaszcímke.

Három állapota lehet. Az eszköz ezeket az állapotokat az jelenti.

- **Synced**: Az eszközt, hogy a beállítás értéke megváltozott.

- **Függőben lévő**: Az eszköz jelenleg módosul, a beállítás értéke.

- **Hiba**: Az eszköz hibát adott vissza.

Például hozzáadhat egy új ventilátor sebesség beállítás kiválasztásával **beállítások** , és adjon meg az új **szám** beállítást:

| Megjelenítendő név  | Mezőnév    |  Egység  | Tizedesjegyek |Kezdeti|
| --------------| ------------- |---------| ---------|---- |
| Sebesség ventilátor     | fanSpeed      | RPM     | 2        | 0   |

!["Configure száma" űrlapján beállításai sebességét részletei](./media/howto-set-up-template/settingsform.png)

Kiválasztása után **mentése**, a **ventilátor sebesség** beállítás egy csempe jelenik meg. Az operátornak használhatja a beállítás a **Device Explorer** lapot, és módosítsa az eszköz Ventilátor sebessége.

## <a name="properties"></a>Tulajdonságok

A tulajdonságok akkor az eszközön, például az eszköz helye és sorozatszám társított metaadatokat. Több tulajdonságok hozzáadása a sablonhoz, eszköz, amely a csempék formájában jelenik meg a **tulajdonságok** fülre. Vlastnost rendelkezhet egy típus, például a szám, szöveg, dátum, be-vagy kikapcsolása, eszköztulajdonság, címkét vagy helyet. Az operátor is adja meg a tulajdonságok értékeit, hozzon létre egy eszközt, és ezeket az értékeket bármikor szerkesztheti azokat. Eszköztulajdonságok csak olvashatók, és az eszköz az alkalmazásba küldi. Az operátornak eszköz tulajdonságai nem módosíthatók. Egy valós eszköz csatlakozik, az tulajdonság csempére esetén az alkalmazás a frissítéseket.

Két tulajdonságkategória érhető el:

- _Eszköztulajdonságok_ által jelentett az IoT Central alkalmazáshoz. Eszköztulajdonságok az eszköz által jelentett csak olvasható érték, és frissülnek az alkalmazásban, ha egy valódi eszköz csatlakoztatva van.
- _Alkalmazástulajdonságok_ , amely az alkalmazás tárolódnak, és szerkesztheti az operátor. Az eszköz nem ismeri fel az alkalmazás tulajdonságait.

Például az eszköz utolsó szervizelt dátumának hozzáadhat egy új **dátum** (egy alkalmazás-tulajdonság) tulajdonsága a **tulajdonságok** lapon:

| Megjelenítendő név  | Mezőnév | Kezdeti érték   |
| --------------| -----------|-----------------|
| Utolsó szervizelés      | lastServiced        | 01/29/2019     |

![A "Tulajdonságok" lap "Utolsó Serviced konfigurálása" képernyő](./media/howto-set-up-template/propertiesform.png)

Miután kiválasztotta **mentése**, a legutóbbi dátum szolgálja ki, a csempe jelenik meg az eszköz.

Miután létrehozta a csempét, módosíthatja az alkalmazás tulajdonság értéke az a **Device Explorer**.

### <a name="create-a-location-property-through-azure-maps"></a>Hozzon létre egy hely tulajdonságot az Azure Maps révén

Az Azure IoT Central helyadatok földrajzi összefüggésbe, és bármely olyan utca, házszám szélességi és hosszúsági koordinátáit leképezése. Vagy leképezheti szélességi és hosszúsági koordinátáit. Az Azure Maps ezt a lehetőséget az IoT Central lehetővé teszi.

Hely tulajdonságai két típusú adhat hozzá:

- **A helyen, ahol egy alkalmazás tulajdonság**, amely tárolja az alkalmazást. Az eszköz nem ismeri fel az alkalmazás tulajdonságait.
- **A helyen, ahol egy adott eszköztulajdonság**, amely az eszköz jelenti, hogy az alkalmazás.

#### <a name="add-location-as-an-application-property"></a>Egy alkalmazás tulajdonságként helyének hozzáadása

Egy alkalmazás tulajdonságként a location tulajdonsághoz hozhat létre az IoT-központ alkalmazás az Azure Maps használatával. Ha például az eszköz telepítési címe is hozzáadhat:

1. Keresse meg a **tulajdonságok** fülre.

2. Válassza ki a könyvtárban **hely**.

3. Konfigurálása **megjelenítendő név**, **mezőnév**, és (opcionálisan) **kezdeti érték** helyéhez.

    | Megjelenítendő név  | Mezőnév | Kezdeti érték |
    | --------------| -----------|---------| 
    | Telepítési címe | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   !["A hely konfigurálása" űrlapján hely részletei](./media/howto-set-up-template/locationcloudproperty2.png)

   Van egy hely hozzáadásához két támogatott formátumok:
   - **Hely-címként**
   - **A helyen, ahol koordinátái**

4. Kattintson a **Mentés** gombra. Az operátornak frissítheti a hely értékét a **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Eszköz-tulajdonságként helyének hozzáadása

A location tulajdonság által jelentett tulajdonságként eszköz hozhat létre. Ha például szeretné nyomon követni az eszköz helye:

1. Keresse meg a **tulajdonságok** fülre.

2. Válassza ki **Eszköztulajdonság** a könyvtárból.

3. Konfigurálja a megjelenített nevet és mező nevét, és válassza ki **hely** , adattípus:

    | Megjelenítendő név  | Mezőnév | Adattípus |
    | --------------| -----------|-----------|
    | Eszközhely | deviceLocation | location  |

   > [!NOTE]
   > A mezőneveknek meg kell egyeznie a megfelelő eszköz kód tulajdonságneveket

   !["Az eszköz tulajdonságainak konfigurálása" űrlapján hely részletei](./media/howto-set-up-template/locationdeviceproperty2.png)

A valós eszközöknek a csatlakozás után a helyre, amely egy adott eszköztulajdonság hozzáadott frissül az értéket, az eszköz által küldött. Most, hogy konfigurálta a location tulajdonsághoz, [jeleníthetik meg a helyet, az eszköz irányítópult térképen hozzáadása](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Parancsok

Parancsok segítségével eszköz távoli felügyelete. Ezek lehetővé teszi a kezelők az eszközön lévő parancsok futtatásához. Csempék formájában jelenik meg az eszköz-sablon több parancsot is hozzáadhat a **parancsok** fülre az operátorok használatához. Az eszköz a szerkesztő, mint rugalmasan adhatók meg a parancsok a követelményeinek megfelelően.

Miben különbözik egy parancs egy beállítást?

* **Beállítás**: A beállítás egy-egy eszköz a alkalmazni kívánt konfigurációját. Azt szeretné, hogy az eszköz számára, hogy a konfigurációs maradnak, amíg nem módosítja azt. Például a hőmérsékletet, a feldolgozó beállítja, és azt szeretné, hogy a beállítást, akkor is, ha a feldolgozó újraindítja.

* **A parancs**: Parancsok használatával azonnal parancsot az eszközön távolról futtatni az IoT-központ. Ha egy eszköz nem kapcsolódik, a parancs túllépi az időkorlátot, és nem sikerül. Ha például szeretné eszköz újraindítása.

Például hozzáadhat egy új **Echo** parancsot választva a **parancsok** fülre, majd válassza **+ új parancs**, és írja be az új parancs részletei:

| Megjelenítendő név  | Mezőnév | Alapértelmezett időtúllépés | Adattípus |
| --------------| -----------|---------------- | --------- |
| Echo parancs  | echo       |  30             | szöveg      |

![Echo részleteit tartalmazó "Parancs konfigurálása" képernyő](./media/howto-set-up-template/commandsecho.png)

Kiválasztása után **mentése**, a **Echo** parancs csempeként jelenik meg, és készen áll a használatra a **Device Explorer** amikor a valós eszköz csatlakozik-e. A parancs a mezők nevét meg kell egyeznie a tulajdonságnevek ahhoz, hogy a parancs sikeres futtatásához a megfelelő eszköz kódban.

## <a name="rules"></a>Szabályok

Szabályok kezelők számára az eszközök közel valós idejű figyelését. Szabályok automatikus hajthatók végre műveletek, például az e-mailt küld, amikor a szabály akkor lesz kiváltva. Egy szabály típus érhető el még ma:

- **Telemetria szabály**, amely akkor aktiválódik, ha a kiválasztott eszköz telemetriai átlép egy küszöbértéket. [További tudnivalók a telemetriai adatok szabályok](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Irányítópult

Az irányítópulton, ahol az operátornak lépjen az eszköz adatainak megtekintése:. Mint szerkesztő csempék is hozzáadhat az ezen a lapon, a kezelők számára a megértéséhez, hogy az eszköz működése. Az eszköz sablon több irányítópult-csempék is hozzáadhat. Adja hozzá például a lemezkép, vonaldiagram, sávdiagramot oszlopdiagramra cseréli, fő teljesítménymutató (KPI), beállítások és tulajdonságok irányítópult-csempék számos különböző típusú, és címkézését.

Például hozzáadhat egy **beállításait és tulajdonságait** az aktuális értékek beállítás- és a egy kijelölt kiválasztásával mozaiklapra a **irányítópult** lapra, és a csempét a tárból:

![Részletes beállítások és tulajdonságok "Konfigurálása eszköz részletei" képernyő](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Mostantól Ha egy operátort megtekinti az irányítópult a **Device Explorer**, megjelenik a csempén.

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Az Azure Maps-hely hozzáadása az irányítópult

Ha konfigurálta a location tulajdonsághoz, az eszköz irányítópultján egy térkép segítségével jelenítheti meg a helyet.

1. Keresse meg a **irányítópult** fülre.

1. Az eszköz irányítópultján válassza **térkép** a könyvtárból.

1. Adjon meg egy címet a térképen. Az alábbi példa címe **telepítési hely**. Majd válassza ki a helyet jelölő tulajdonsághoz, amely a korábban konfigurált a **tulajdonságok** fülre. A következő példában **telepítési cím** van kiválasztva.

   ![Cím és a Tulajdonságok részleteit tartalmazó "Konfigurálása a Map" képernyő](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Kattintson a **Mentés** gombra. A szolgáltatástérkép csempe mostantól megjeleníti a kiválasztott hely.

A térkép méretezheti át a kívánt méretre. Mostantól Ha egy operátort megtekinti az irányítópult a **Device Explorer**, minden irányítópult-csempék, hogy konfigurálta, beleértve a helyek térképe láthatók.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT Central alkalmazáshoz egy eszköz-sablon beállítása, hogy a következőket teheti:

> [!div class="nextstepaction"]
> [Hozzon létre egy új eszköz sablon verziója](howto-version-devicetemplate.md)