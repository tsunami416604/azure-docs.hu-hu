---
title: Az Azure IoT Central alkalmazáshoz eszköz sablon beállítása |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be egy eszköz sablon mérések, beállítások, tulajdonságainak, szabályok és a egy irányítópultot.
author: viv-liu
ms.author: viviali
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 61bc9da45ac420e5683be1ea3ad253eae9c0ba5a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158941"
---
# <a name="set-up-a-device-template"></a>Eszközsablon beállítása

Egy eszköz, amely meghatározza a jellemzőit és a egy eszköz, amely csatlakozik az Azure IoT Central alkalmazáshoz típusú viselkedéseit tervrajz áll.

Például egy jelentéskészítő egy IoT-csatlakoztatott ventilátor rendelkező eszköz sablont hozhat létre:

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

1. Nyissa meg a **alkalmazás Builder** lapot.

2. Hozzon létre egy üres sablont, válassza a **eszköz-sablon létrehozása**, majd válassza ki **egyéni**.

3. Adjon meg egy nevet (például hűtőszekrények-1) az új eszköz sablon, és válassza ki **létrehozás**.

   !["Hűtőszekrények" a sablon nevét az eszköz részleteit tartalmazó oldalra](./media/howto-set-up-template/devicedetailspage.png)

4. Most már használ a **eszközadatok** új szimulált eszköz lapján. Szimulált eszköz automatikusan létrejön az Ön számára, egy eszköz sablon létrehozásakor. Jelentések adatait és a egy igazi eszközön hasonlóan lehet irányítani.

Most nézzük, az egyes lapokon a **eszközadatok** lapot.

## <a name="measurements"></a>Mérések

Mértékek az eszközről az adatokat. Az eszköz a sablon megfelelő az eszköz képességeit több mérések adhat hozzá.

- **Telemetria** mérések, amely az eszköz által összegyűjtött numerikus adatpontok. Ezek még folyama jelöli. Ilyen például a hőmérsékletet.
- **Esemény** mérések időponthoz adatok, amelyek valamit az eszközön a jelentősége. Egy súlyossági szintet az esemény fontossága jelöli. Példa ventilátor motor hiba.
- **Állapot** mérések az eszköz vagy összetevőinek állapotát képviselik egy időszakon belül. Például egy ventilátor mód lehet meghatározni, hogy **operációs** és **leállítva** , a két lehetséges állapota.

### <a name="create-a-telemetry-measurement"></a>Telemetria mérték létrehozása
Egy új telemetriai mérési hozzáadásához válassza **sablon szerkesztése**, majd kattintson a **+ új mérték** gombra. Válassza ki **Telemetriai** a mérték, írja be, és adja meg, hogy a **Telemetriát hozzon létre** űrlap.

> [!NOTE]
> Az eszköz sablon egyeznie kell a megfelelő eszköz kódot ahhoz, hogy a telemetria mérés esetén valós eszköz csatlakoztatva van, az alkalmazás megjelenítendő tulajdonságnevek. Végezze el ugyanezt konfigurálásakor beállításait, az eszköztulajdonságok és a parancsok, továbbra is az alábbi szakaszok az eszköz sablon megadására.

Például egy új hőmérsékleti telemetria mérték is hozzáadhat:
| Megjelenítendő név        | Mezőnév    |  Egység    | Min   |Max|
| --------------------| ------------- |-----------|-------|---|
| Hőmérséklet         | TEMP          |  degC     |  0    |100|

!["A telemetriai adatok létrehozása" űrlapján hőmérséklet mérési adatait](./media/howto-set-up-template/measurementsform.png)

Miután kiválasztotta **kész**, a **hőmérséklet** mérték megjelenik a listában a mértékek. Egy rövid ideig a szimulált eszközt hozott létre hőmérsékleti adatok a Vizualizáció látható. Eszköz sablont hoz létre, amikor egy szimulált eszköz jön létre a sablonból, amely lehetővé teszi, hogy tesztelje az alkalmazás viselkedését, mielőtt egy fizikai/valós eszköz csatlakoztatva van.


> [!NOTE]
  A telemetriai adatok mérték adattípusa lebegőpontos szám.

### <a name="create-an-event-measurement"></a>Egy esemény-mérték létrehozása
Egy új esemény mérési hozzáadásához válassza **sablon szerkesztése**, majd kattintson a **+ új mérték** gombra. Válassza ki **esemény** a mérték, írja be, és adja meg, hogy a **esemény létrehozása** űrlap.

Adja meg a **megjelenítendő név**, **mezőnév**, és **súlyossági** az esemény részleteit. Súlyosság három szintjei közül választhat: **hiba**, **figyelmeztetés**, és **információk**.  

Például hozzáadhat egy új **ventilátor Motor hiba** esemény.

| Megjelenítendő név        | Mezőnév    |  Alapértelmezett súlyosság | 
| --------------------| ------------- |-----------|
| Ventilátormotor-hiba     | fanmotorerror |  Hiba    | 

!["Esemény létrehozása" űrlapján ventilátor motor esemény részletei](./media/howto-set-up-template/eventmeasurementsform.png)

Kiválasztása után **kész**, a **ventilátor Motor hiba** mérési mérések listájában jelenik meg. Az operátor az eseményadatokat, hogy az eszköz által küldött, a Vizualizáció látható.

![Esemény mérési diagram](./media/howto-set-up-template/eventmeasurementschart.png)

Az esemény részleteinek megtekintéséhez jelölje ki a diagramon az esemény ikonra.

![A "Ventilátor Motor Error" esemény részletei](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
  Az esemény mérték adattípusa karakterlánc.

### <a name="create-a-state-measurement"></a>Egy állapot-mérték létrehozása
Új állapot a mérés hozzáadásához válassza **sablon szerkesztése**, majd kattintson a **+ új mérték** gombra. Válassza ki **állapot** a mérték, írja be, és adja meg, hogy a **létrehozása állapot** űrlap.

Adja meg az adatait **megjelenített név**, **mezőnév**, és **értékek** az állapot. Minden egyes érték is használható, ha az érték jelenik meg, diagramok és táblázatok megjelenített név.

Például hozzáadhat egy új **ventilátor mód** állapota, az eszköz küldhet, két lehetséges értékekkel rendelkező **operációs** és **leállítva**.


| Megjelenítendő név | Mezőnév    |  1. érték   | Megjelenítendő név | 2. érték    |Megjelenítendő név  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Ventilátor mód     | fanmode       |  1         | Üzemel    |     0      | Leállítva      |

![Ventilátor mód részleteit tartalmazó "Szerkesztés állapot" képernyő](./media/howto-set-up-template/statemeasurementsform.png)

Kiválasztása után **kész**, a **ventilátor mód** állapot mérési mérések listájában jelenik meg. Az operátor az állapotadatokat, amelyeket az eszköz által küldött, a Vizualizáció látható.

![Állapot mérési diagram](./media/howto-set-up-template/statemeasurementschart.png)

Az eszköz a kis időtartam túl sok adatpont küld, ha az állapot mérték jelenik meg egy másik vizualizációt, az alábbi képernyőképen látható módon. Ha rákattint a diagram, az adott időtartamon belül minden adatpontok időrendi sorrendben jelennek meg. Az időtartomány, tekintse meg a diagramon ábrázolt mérését is szűkíthető.

> [!NOTE]
  Az állapot mérték adattípusa karakterlánc.

## <a name="settings"></a>Beállítások

Beállítások egy eszköz vezérlésére. Adja meg a bemeneti adatok az eszközön az alkalmazás üzemeltetői lehetővé teszik. Csempék formájában jelenik meg az eszköz-sablon több beállítást is hozzáadhat a **beállítások** fülre az operátorok használatához. Számos különböző típusú beállításokat is hozzáadhat: szám, szöveg, dátum, be-vagy kikapcsolása, választéklista és szakaszcímke. 

Három állapota lehet. Az eszköz ezeket az állapotokat az jelenti.

- **Szinkronizált**: az eszközt, hogy a beállítás értéke megváltozott.

- **Függőben lévő**: az eszköz jelenleg módosul, a beállítás értéke.

- **Hiba**: az eszköz hibát adott vissza.

Például hozzáadhat egy új ventilátor sebesség beállítás kiválasztásával **sablon szerkesztése** , és adjon meg az új **szám** beállítást:

| Megjelenítendő név  | Mezőnév    |  Egység  | Tizedesjegyek |Kezdeti|
| --------------| ------------- |---------| ---------|---- |
| Sebesség ventilátor     | fanSpeed      | RPM     | 2        | 0   |

!["Configure száma" űrlapján beállításai sebességét részletei](./media/howto-set-up-template/settingsform.png)

Kiválasztása után **mentése**, a **ventilátor sebesség** beállítás csempeként jelenik meg, és készen áll a használatra, az eszköz ventilátor sebességének módosítása.

Miután létrehozott egy csempére, válassza ki a **kész** a képernyő jobb felső részén. Miután az alkalmazás össze van kapcsolva a valós eszközöknek, a beállítás értéke módosul szinkronizált.

![A "Tervezési mód" kapcsolóval csempe "Beállítások" lap](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Tulajdonságok

A tulajdonságok akkor, az eszköz metaadatait, amely az eszköz, például az eszköz helye és sorozatszám vannak társítva. Több tulajdonságokat adhat hozzá, csempék formájában jelenik meg az eszköz-sablon a **tulajdonságok** fülre. Számos különböző típusú tulajdonságokat is hozzáadhat: szám, szöveg, dátum, be-vagy kikapcsolása, eszköztulajdonság, felirat és helyét. Az operátor is adja meg a tulajdonságok értékeit, hozzon létre egy eszközt, és ezeket az értékeket bármikor szerkesztheti azokat. Azonban eszköztulajdonságok csak olvashatók, és az alkalmazásba küldi az eszköz, és nem módosítható az operátor. Ha a valós eszköz van csatlakoztatva, az tulajdonság csempére az alkalmazás frissülni fog. 

Két tulajdonságkategória érhető el:

- **Eszköz** által az IoT Central alkalmazásnak jelentett tulajdonságok. Ezek a csak olvasható érték, az eszköz által jelentett, és amikor a valós eszköz csatlakoztatva van, az alkalmazás frissítése. 
- **Alkalmazás** tulajdonságok, amelyek tárolják, csak az alkalmazásban, és szerkesztheti az operátor. Az eszköz nem ismeri fel az alkalmazás tulajdonságait.

Például hozzáadhatja az eszközök helyére egy új **szöveg** tulajdonság (egy alkalmazás-tulajdonság) kiválasztásával **sablon szerkesztése** és az új tulajdonság megadásával:

| Megjelenítendő név  | Mezőnév | Trim szóközöket  | Trim szóközöket  | Kis-és nagybetűk| Minimális hossz | Maximális hossz |
| --------------| -----------|---------| ---------|---- |----|----|
| Hely      | hely        | Ki     |  Ki     | Vegyes  | 0 | 100|

![A "Tulajdonságok" lap "Szöveg konfigurálása" képernyő](./media/howto-set-up-template/propertiesform.png)

Miután kiválasztotta **mentése**, az eszközök helyére egy csempe jelenik meg:

![Hely csempe](./media/howto-set-up-template/propertiestile.png)

Miután létrehozta a csempét, módosíthatja az alkalmazás tulajdonság értéke. Először jelölje ki **kész** a képernyő jobb felső részén.

### <a name="create-a-location-property-through-azure-maps"></a>Hozzon létre egy hely tulajdonságot az Azure Maps révén
Az Azure IoT Central helyadatok földrajzi összefüggésbe, és bármely olyan utca, házszám szélességi és hosszúsági koordinátáit leképezése. Vagy egyszerűen térkép szélességi és hosszúsági koordinátákat is. Az Azure Maps ezt a lehetőséget az IoT Central lehetővé teszi.

Hely tulajdonságai két típusú adhat hozzá:
- **A helyen, ahol egy alkalmazás tulajdonság**, amely csak az alkalmazásban tárolt. Az eszköz nem ismeri fel az alkalmazás tulajdonságait.
- **A helyen, ahol egy adott eszköztulajdonság**, amely az eszköz jelenti, hogy az alkalmazás.

#### <a name="add-location-as-an-application-property"></a>Egy alkalmazás tulajdonságként helyének hozzáadása 
Egy alkalmazás tulajdonságként a location tulajdonsághoz hozhat létre az IoT-központ alkalmazás az Azure Maps használatával. Ha például az eszköz telepítési címe is hozzáadhat. 

1. Az a **tulajdonságok** lapon jelölje be **sablon szerkesztése**.

   ![Tervezési mód engedélyezve van a "Tulajdonságok" lap](./media/howto-set-up-template/locationcloudproperty1.png)

2. Válassza ki a könyvtárban **hely**.
3. Konfigurálása **megjelenítendő név**, **mezőnév**, és (opcionálisan) **kezdeti érték** helyéhez. 

    | Megjelenítendő név  | Mezőnév | Kezdeti érték |
    | --------------| -----------|---------| 
    | Telepítési címe | insta_address | A Microsoft 1 Microsoft Way, Redmond, WA 98052   |

   !["A hely konfigurálása" űrlapján hely részletei](./media/howto-set-up-template/locationcloudproperty2.png)

   Van egy hely hozzáadásához két támogatott formátumok:
   - **Hely-címként**
   - **A helyen, ahol koordinátái** 

4. Válassza ki **mentése** és **kész**. Az operátornak most már frissítheti az a hely értéket a hely mező képernyőn. 

#### <a name="add-location-as-a-device-property"></a>Eszköz-tulajdonságként helyének hozzáadása 

A location tulajdonság által jelentett tulajdonságként eszköz hozhat létre. Ha például szeretné nyomon követni az eszköz helye:

1. Az a **tulajdonságok** lapon jelölje be **sablon szerkesztése**.

   ![Tervezési mód engedélyezve van a "Tulajdonságok" lap](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Válassza ki **Eszköztulajdonság** a könyvtárból.
3. Állítsa be a megjelenítendő név és a mező nevét, és válassza ki **hely** adattípusának. 

    | Megjelenítendő név  | Mezőnév | Adattípus |
    | --------------| -----------|-----------| 
    | Eszközhely | deviceLoc| location  |

   > [!NOTE]
   > A mezőneveknek meg kell egyeznie a megfelelő eszköz kód tulajdonságneveket

   !["Az eszköz tulajdonságainak konfigurálása" űrlapján hely részletei](./media/howto-set-up-template/locationdeviceproperty2.png)

A valós eszközöknek a csatlakozás után a helyre, amely egy adott eszköztulajdonság hozzáadott a értékkel, az eszköz által küldött frissülni fog. A telepítés, a helyre, amely egy alkalmazás tulajdonság hozzáadott címe egy szerkeszthető csempét. Most, hogy konfigurálta a location tulajdonsághoz, [jeleníthetik meg a helyet, az eszköz irányítópult térképen hozzáadása](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Parancsok

Parancsok segítségével eszköz távoli felügyelete. Ezek lehetővé teszi a kezelők az alkalmazás azonnal az eszközön a parancsok futtatásához. Csempék formájában jelenik meg az eszköz-sablon több parancsot is hozzáadhat a **parancsok** fülre az operátorok használatához. Az eszköz a szerkesztő, mint rugalmasan adhatók meg a parancsok a követelményeinek megfelelően.

Miben különbözik egy parancs egy beállítást? 

* **Beállítás**: A beállítás nem egy-egy eszköz a alkalmazni kívánt konfigurációját, és azt szeretné, hogy az eszköz számára, hogy a konfigurációs maradnak, amíg nem módosítja azt. Például a hőmérsékletet, a feldolgozó beállítja, és azt szeretné, hogy a beállítást, akkor is, ha a feldolgozó újraindítja. 

* **A parancs**: parancsok használatával azonnal parancsot az eszközön távolról futtatni az IoT-központ. Ha egy eszköz nem kapcsolódik, a parancs túllépi az időkorlátot, és nem sikerül. Ha például szeretné eszköz újraindítása.  


Például hozzáadhat egy új **Echo** parancsot választva **sablon szerkesztése**, majd kattintson **+ új parancs**, és beírja az új parancsot:

| Megjelenítendő név  | Mezőnév | Alapértelmezett időtúllépés | Adattípus |
| --------------| -----------|---------------- | --------- | 
| Echo parancs  | echo       |  30             | szöveg      |

![Echo részleteit tartalmazó "Parancs konfigurálása" képernyő](./media/howto-set-up-template/commandsecho.png)

Kiválasztása után **mentése** és **kész**, a **Echo** parancs csempeként jelenik meg, és készen áll a használatra az echo az eszközt, miután az valós eszköz csatlakoztatva van. A parancs a mezők nevét meg kell egyeznie a tulajdonságnevek ahhoz, hogy a parancs sikeres futtatásához a megfelelő eszköz kódban.


## <a name="rules"></a>Szabályok

Szabályok kezelők számára az eszközök közel valós idejű figyelését. Szabályok automatikus hajthatók végre műveletek, például az e-mailt küld, amikor a szabály akkor lesz kiváltva. Egy szabály típus érhető el még ma:

- **Telemetria szabály**, amely akkor aktiválódik, ha a kiválasztott eszköz telemetriai átlép egy küszöbértéket. [További tudnivalók a telemetriai adatok szabályok](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Irányítópult

Az irányítópulton, ahol az operátornak lépjen az eszköz adatainak megtekintése:. Mint szerkesztő csempék is hozzáadhat az ezen a lapon, a kezelők számára a megértéséhez, hogy az eszköz működése. Az eszköz sablon több irányítópult-csempék is hozzáadhat. Adja hozzá például a lemezkép, vonaldiagram, sávdiagramot oszlopdiagramra cseréli, fő teljesítménymutató (KPI), beállítások és tulajdonságok irányítópult-csempék számos különböző típusú, és címkézését.

Hozzáadhat például egy **beállításait és tulajdonságait** csempe kiválasztásával az aktuális értékek beállítás- és a egy kijelölt megjeleníthető **sablon szerkesztése** és a csempét a tárból:

![Részletes beállítások és tulajdonságok "Konfigurálása eszköz részletei" képernyő](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Mostantól Ha operátor megtekinti az irányítópultot, meghatározhatják, hogy ez a csempe, amely megjeleníti a tulajdonságokat és beállításokat az eszköz:

![A megjelenített beállítások és tulajdonságok csempe "Az irányítópult" lap](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Az Azure Maps-hely hozzáadása az irányítópult

Ha konfigurálta a location tulajdonsághoz korábban a [hozzon létre egy hely tulajdonságot keresztül az Azure Maps](#create-a-location-property-through-azure-maps), az eszköz irányítópultján egy térkép használatával jelenítheti meg a helyet.

1. Az a **irányítópult** lapon jelölje be **sablon szerkesztése**.

   ![Tervezési mód engedélyezve van az "Irányítópult" lap](./media/howto-set-up-template/locationcloudproperty4map.png)

2. Az eszköz irányítópultján válassza **térkép** a könyvtárból. 
3. Adjon meg egy címet. Az alábbi példa a cím telepítési hely van, majd válassza ki a korábban beállított tulajdonságok lapján helytulajdonságot. Az alábbi példában **telepítési cím** van kiválasztva.

   ![Cím és a Tulajdonságok részleteit tartalmazó "Konfigurálása a Map" képernyő](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Kattintson a **Mentés** gombra. A szolgáltatástérkép csempe mostantól megjeleníti a kiválasztott hely. 

   ![A kiválasztott helyen a szolgáltatástérkép csempe](./media/howto-set-up-template/locationcloudproperty6map.png) 

A térkép méretezheti át a kívánt méretre. Most már az operátornak megtekinti az irányítópultot, amikor megjelenik az összes az irányítópult-csempék, amelyek konfigurálta, többek között a helyek térképe.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT Central alkalmazáshoz egy eszköz-sablon beállítása, hogy a következőket teheti:

> [!div class="nextstepaction"]
> [Hozzon létre egy új eszköz sablon verziója](howto-version-devicetemplate.md)
