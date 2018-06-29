---
title: Egy Azure IoT központi alkalmazásban eszköz sablon beállítása |} Microsoft Docs
description: Megtudhatja, hogyan állíthat be egy eszköz sablon mérések, beállítások, tulajdonságok, szabályok és egy irányítópultot.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f71d4c7dc94fedfd598ab87c51366ba9fb1f184a
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063035"
---
# <a name="set-up-a-device-template"></a>Eszköz sablon beállítása

Egy eszköz sablon egy tervezetének, jellemzőit és egy eszköz, amely csatlakozik a Microsoft Azure IoT központi alkalmazások típusú viselkedés határozza meg.

A jelentéskészítő például egy IoT-csatlakoztatott ventilátor rendelkező eszköz sablont hozhat létre:

- Hőmérséklet-telemetria mérési

- Ventilátor motor hiba esemény mérési

- Működési állapot mérési ventilátor

- Ventilátor beállítása

- Location tulajdonság

- Szabályok, amelyek az értesítések küldése

- Irányítópultot, amely lehetővé teszi az átfogó képet az eszköz

A eszköz sablonból operátor hozhat létre és valós ventilátor eszközök csatlakoztatása a névvel, mint **ventilátor.-1** és **ventilátor-2**. A ventilátorok rendelkezik mérések, beállítások, tulajdonságok, szabályok és egy irányítópultot, amely a felhasználók az alkalmazás figyelése és kezelése.

> [!NOTE]
> Csak a létrehozói és a rendszergazdák létrehozása, szerkesztése és törlése eszköz sablonok. Bármely felhasználó létrehozhat eszközök a a **eszköz Explorer** meglévő eszköz felügyeleticsomag-sablonok lap.

## <a name="create-a-device-template"></a>Eszköz-sablon létrehozása

1. Lépjen a **alkalmazás jelentéskészítő** lap.

2. Hozzon létre egy üres sablont, válassza ki **eszköz sablon létrehozása**, majd válassza ki **egyéni**.

3. Adja meg az új eszköz-sablon nevét, és válassza ki **létrehozása**.

   ![Eszköz részleteit megjelenítő oldalon a "Hűtő" a sablonnevet](./media/howto-set-up-template/devicedetailspage.png)

4. Épp most a **eszközadatok** új szimulált eszköz oldalán. A szimulált eszköz automatikusan létrejön egy eszköz sablon létrehozásakor. Adatok jelentéseket, és szabályozhatja csakúgy, mint egy valós eszközt.

Egyes lapokon nézze most a **eszközadatok** lap.

## <a name="measurements"></a>Mérések

A méréseket az adatokat, hogy az eszköz származik. Az eszköz lehetőségeinek megfelelő eszköz sablonjára több mérések is hozzáadhat.

- **Telemetria** mérések, amely az eszköz által összegyűjtött numerikus adatpontok. Ezek állandó folyama most jelöli. Példa: hőmérséklet.
- **Esemény** méréseket időpontban adatok, amelyek valamilyen többszörösére az eszközön. Egy súlyossági szintet az esemény fontosságát jelöli. Példa: egy ventilátor motor hiba.
- **Állapot** mérések határoz meg az eszköz vagy az összetevők állapotát egy meghatározott időtartamra vonatkozóan. Például egy ventilátor mód adható meg kellene **operációs** és **leállítva** , a két lehetséges állapota.

### <a name="create-a-telemetry-measurement"></a>A telemetriai adatok mérési létrehozása
Egy új telemetriai mérési hozzáadásához válassza a **+ új mérési** gombra. Válassza ki **Telemetriai** a mérték, írja be, és adja meg a részleteket a a **létrehozása Telemetriai** űrlap.

> [!NOTE]
> Ha egy valódi eszköz csatlakozik, a neve, amely az eszköz jelentés készít a mérés figyelmet fordítania. A név pontosan meg kell egyeznie a **mezőnév** bejegyzés egy mérték.

Hozzáadhat például egy új hőmérséklet telemetriai mérési:

![Hőmérséklet méréséhez adatokkal "Telemetriai létrehozása" képernyő](./media/howto-set-up-template/measurementsform.png)

Miután kiválasztotta a **mentése**, a **hőmérséklet** mérési mérések listájában jelenik meg. A képi megjelenítés gyűjtő, az eszköz hőmérséklete adatok operátor tekintheti meg.

![Mérési diagramhoz](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Egy esemény mérték létrehozása
Egy új esemény mérési hozzáadásához válassza a **+ új mérési** gombra. Válassza ki **esemény** a mérték, írja be, és adja meg a részleteket a a **esemény létrehozása** űrlap.

Adja meg a **megjelenített név**, **mezőnév**, és **súlyossági** az esemény részleteit. A három elérhető súlyossági szintek közül választhat: **hiba**, **figyelmeztetés**, és **információk**.  

Például hozzáadhat egy új **ventilátor Motor hiba** esemény.

![Egy ventilátor motor esemény részleteinek "Esemény létrehozása" képernyő](./media/howto-set-up-template/eventmeasurementsform.png)

Miután kiválasztotta a **mentése**, a **ventilátor Motor hiba** mérési mérések listájában jelenik meg. Az esemény-adatok, amely az eszköz küld a képi megjelenítés operátor tekintheti meg.

![Esemény mérési diagram](./media/howto-set-up-template/eventmeasurementschart.png)

Az esemény részletes adatainak megtekintéséhez jelölje ki az esemény ikonra a diagram:

![A "Ventilátor Motor Error" esemény részletei](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Hozzon létre egy állapot mérési
Új állapot a mérés hozzáadásához válassza a **+ új mérési** gombra. Válassza ki **állapot** a mérték, írja be, és adja meg a részleteket a a **létrehozás állapot** űrlap.

Adja meg a részletes **megjelenített név**, **mezőnév**, és **értékek** az állapot. Az egyes értékek is használandó, ha az érték diagramok és táblák megjelenítendő nevét.

Például hozzáadhat egy új **ventilátor mód** , két lehetséges értéket, amely az eszköz küld, amelynek állapota **operációs** és **leállítva**.

![Ventilátor mód adatokkal "Szerkesztés állapot" képernyő](./media/howto-set-up-template/statemeasurementsform.png)

Miután kiválasztotta a **mentése**, a **ventilátor mód** állapot mérési mérések listájában jelenik meg. Az operátor tekintheti meg a képi megjelenítés küld az eszköz állapota adatok.

![Állapot mérési diagram](./media/howto-set-up-template/statemeasurementschart.png)

Az eszköz túl sok adatpont küld egy kis idő, az állapot mérés az egy másik visual megjelenik az alábbi képernyőfelvételen látható módon. Ha a diagram gombra kattint, a meghatározott időszakon belül adatpontok időrendi sorrendben jelennek meg. Az időtartományt, tekintse meg a mérték a diagramon ábrázolt is szűkítéséhez.

![Az állapotadatok mérési adatokat a "statikus ventilátor mód"](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Beállítások

Beállítások egy eszköz vezérlésére. Az alkalmazás arra, hogy az eszközhöz bevitelek operátorok lehetővé teszik. Az eszköz sablon megjelenő mozaikként több beállítást is hozzáadhat a **beállítások** fülre az operátorok használatára. Hat típusú beállítások is hozzáadhat: szám, dátum, váltása, kivételezési listát, és a szakasz címke.

> [!NOTE]
> Ha egy valódi eszköz csatlakozik, a neve, amely az eszköz jelentés készít beállítás figyelmet fordítania. A név pontosan meg kell egyeznie a **mezőnév** bejegyzés beállítás.

Beállítások az alábbi három állapotban lehet. Az eszköz jelentés készít ezeket az állapotokat.

- **Szinkronizált**: az eszköz megfelelően a beállítás értéke megváltozott.

- **Függőben lévő**: az eszköz jelenleg vált át a beállítás értéke.

- **Hiba**: az eszköz hibát adott vissza.

Például egy új ventilátor sebesség beállítást is hozzáadhat:

![Sebesség beállításai részleteinek "Számának konfigurálása" képernyő](./media/howto-set-up-template/settingsform.png)

Miután kiválasztotta a **mentése**, a **Ventilátor sebessége** beállítás csempe jelenik meg, és az eszköz ventilátor sebességének módosítása használatra kész.

Miután létrehozott egy csempe, kipróbálhatja az új beállítással. Első lépésként kapcsolja ki a tervezési módba, a képernyő jobb felső részén.

![A "Tervezési módba" kapcsolóval csempe "Beállítások" lapon](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Tulajdonságok

A rendszer az eszköz metaadatait az eszköz, például az eszköz helyét és sorozatszám társított tulajdonságokat. Az eszköz sablon megjelenő mozaikként több tulajdonságot is hozzáadhat a **tulajdonságok** fülre. Operátor megadhatja az értékeket azokhoz a tulajdonságokhoz, hoznak létre egy eszközt, és bármikor ezeket az értékeket szerkesztheti azokat. Hat típusú tulajdonságokat is hozzáadhat: szám, dátum, váltása, eszköztulajdonságon, és címke.

Tulajdonságok két kategóriába sorolhatók:

- **Eszköz** tulajdonságait, amely az eszköz jelentés készít.
- **Alkalmazás** pusztán az alkalmazásban tárolt tulajdonságai. Az eszköz nem ismeri fel az alkalmazás tulajdonságait.

> [!NOTE]
> Az eszköz tulajdonságok valós eszköz csatlakozik, amikor nagy figyelmet fordítani az, hogy az eszköz jelentés készít a tulajdonság nevét. A név pontosan meg kell egyeznie a **mezőnév** bejegyzés tulajdonság. Az alkalmazás tulajdonságait, a mező neve lehet bármilyen, mindaddig, amíg az eszköz sablon egyedi neve.

Például egy új tulajdonságként eszköz helyét is hozzáadhat:

![A "Tulajdonságok" lapon "Szöveget konfigurálása" képernyő](./media/howto-set-up-template/propertiesform.png)

Miután kiválasztotta a **mentése**, eszköz helyét csempe jelenik meg:

![Hely csempe](./media/howto-set-up-template/propertiestile.png)

Miután létrehozott egy csempe, módosíthatja a tulajdonság értékét. Első lépésként kapcsolja ki a tervezési módba, a képernyő jobb felső részén.

### <a name="create-a-location-property-through-azure-maps"></a>Hozzon létre egy Azure-leképezések helye tulajdonság
Földrajzi környezet rendszerében a hely adatait az Azure IoT-központ, és bármely olyan utca, házszám szélességi és hosszúsági koordinátáját hozzárendelését. Vagy Önnek egyszerűen térkép szélességi és hosszúsági koordinátákkal is. Az Azure Maps lehetővé teszi, hogy ezt a lehetőséget az IoT központi.

Kétféle típusú hely tulajdonságai is hozzáadhat:
- **Egy alkalmazás tulajdonság helyének**, amely kizárólag az alkalmazás tárolódik. Az eszköz nem ismeri fel az alkalmazás tulajdonságait.
- **Hely eszköz tulajdonságként**, amely az eszköz jelentés készít.

#### <a name="add-location-as-an-application-property"></a>Alkalmazás tulajdonságként hely hozzáadása 
A location tulajdonság egy alkalmazás tulajdonság létrehozásához, ha Azure IoT központi alkalmazásában megtekintését. Például az eszköz telepítési címe is hozzáadhat. 

1. Az a **tulajdonságok** lapra, győződjön meg arról, hogy **tervezési módba** van **a**.

   ![A Tervező mód engedélyezve van a "Tulajdonságok" lapon](./media/howto-set-up-template/locationcloudproperty1.png)

2. Válassza ki a szalagtár **hely**.
3. Konfigurálása **megjelenített név**, **mezőnév**, és (opcionálisan) **kezdeti érték** helyét. 

   !["A hely konfigurálása" űrlap hely adatokkal](./media/howto-set-up-template/locationcloudproperty2.png)

   Nincsenek két támogatott formátumok hely hozzáadásához:
   - **Hely-címként**
   - **Hely-koordináták** 

4. Kattintson a **Mentés** gombra. 

   ![Telepítési címmel hozzáadott helyet jelölő tulajdonsághoz](./media/howto-set-up-template/locationcloudproperty3.png)

Most egy olyan operátort frissítheti a hely mező űrlap a hely értéke. 

#### <a name="add-location-as-a-device-property"></a>Eszköz tulajdonságként hely hozzáadása 

A helyet jelölő tulajdonsághoz, amely az eszköz jelentés készít eszköz tulajdonságként hozhat létre. Például, ha szeretné nyomon követni az eszköz helyét:

1. Az a **tulajdonságok** lapra, győződjön meg arról, hogy **tervezési módba** van **a**.

   ![A Tervező mód engedélyezve van a "Tulajdonságok" lapon](./media/howto-set-up-template/locationdeviceproperty1.png)

2. Válassza ki **Eszköztulajdonságon** a könyvtárból.
3. Konfigurálja a megjelenített név és a mező nevét, majd válassza ki **hely** adattípusaként. 

   > [!NOTE]
   > A mezőnév pontosan egyeznie kell, hogy az eszköz jelentés készít a tulajdonságnevet kell megadni. 

   ![Hely adatokkal "Eszköztulajdonságok konfigurálása" képernyő](./media/howto-set-up-template/locationdeviceproperty2.png)

Most, hogy konfigurálta a helyet jelölő tulajdonsághoz, akkor [vegyen fel egy társítást a helyet, az eszköz irányítópult megjelenítéséhez](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Parancsok

Parancsok segítségével a eszköz távoli felügyelete. Lehetővé teszik az operátorok az alkalmazás azonnal futtassa a parancsokat az eszközön. Az eszköz sablon megjelenő mozaikként több parancs is hozzáadhat a **parancsok** fülre az operátorok használatára. A szerkesztő az eszköz, mint így rugalmasan megadhatók a parancsok a követelményeknek megfelelően.

Miben különbözik a parancs egy beállítást? 

* **Beállítás**: A beállítás a konfigurációkat, amelyek egy eszközre alkalmazni kívánja, és azt szeretné, hogy a konfigurálás továbbra is fennáll, addig az eszköz. Például be szeretné állítani a feldolgozó hőmérséklete, és azt szeretné, hogy a beállítást, akkor is, ha a feldolgozó újraindul. 

* **A parancs**: parancsok segítségével azonnal futtassa a parancsot az eszköz távoli IoT központi. Ha egy eszköz nincs csatlakoztatva, a parancs végrehajtásának időkorlátja, és sikertelen lesz. Például szeretné egy eszköz újraindítása.  

A parancs futtatásakor az alábbi három állapotban, attól függően, hogy az eszköz a parancsot kapott lehet. 

Például hozzáadhat egy új **Echo** parancs:

!["A parancsot konfigurálása" űrlap echo részleteit](./media/howto-set-up-template/commandsecho.png)

Miután kiválasztotta a **mentése**, a **Echo** parancs csempe jelenik meg, és az eszköz echo való használatra kész.

Miután létrehozott egy csempe, kipróbálhatja az új parancsot.

## <a name="rules"></a>Szabályok

Szabályok szereplői eszközök közel valós idejű figyelése. Szabályok automatikus műveleteket végez, például egy e-mailt küld, ha a szabály akkor lesz kiváltva lép működésbe. A szabály egy típusú ma akkor érhető el:

- **Telemetria szabály**, amely akkor váltódik ki, a kijelölt telemetriát ebbe a megadott küszöbértéket. [További információ a telemetriai adatok szabályok](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Irányítópult

Az irányítópult pedig operátor eszközök adatainak megjelenítéséhez hová. Szerkesztő, mert ezen a lapon, a kezelők számára az eszköz működésének megértése hozzáadhatja a csempéket. Az eszköz sablon több irányítópulton található csempe is hozzáadhat. Hat típusú irányítópulton található csempe is hozzáadhat: kép, diagram, a sávdiagram, a KPI, a beállítások és a tulajdonságok és címkézését.

Például hozzáadhat egy **beállítások és a Tulajdonságok** mozaiklapra a kijelölt beállítások és tulajdonságainak aktuális értékeivel:

!["Az eszköz részletes adatainak konfigurálása" képernyő beállításait és a Tulajdonságok adatokkal](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Most operátor megtekinti az irányítópulton, amikor ez a csempe megjeleníti a tulajdonságok és az eszköz beállításait láthatják:

!["Irányítópult" lapon megjelenített beállításokat és a csempe tulajdonságai](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Adja hozzá az Azure-leképezések helye az irányítópulton

Ha konfigurálta a location tulajdonság korábban a [hozzon létre egy Azure-leképezések helye tulajdonság](#create-a-location-property-through-azure-maps), az eszköz irányítópulton térképre használatával jelenítheti meg a helyet.

1. Az a **irányítópult** lapra, győződjön meg arról, hogy **tervezési módba** van **a**.

   ![A Tervező mód engedélyezve van a "Irányítópult" lap](./media/howto-set-up-template/locationcloudproperty4map.png)

2. Az eszköz irányítópultra, válassza ki a **térkép** a könyvtárból. 
3. Olyan címet adjon, és válassza ki a korábban beállított a eszköztulajdonságok részeként helyet jelölő tulajdonsághoz.

   ![Cím és a Tulajdonságok adatokkal "Térkép konfigurálása" képernyő](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Kattintson a **Mentés** gombra. A térkép csempe mostantól megjeleníti a kiválasztott hely. 

   ![Térkép csempe, amely a kijelölt hely](./media/howto-set-up-template/locationcloudproperty6map.png) 

A térkép átméretezheti a kívánt méretre.

Most operátor megtekinti az irányítópulton, amikor konfigurálását, beleértve egy helyének hozzárendelése az összes irányítópult-csempéihez láthatják.

![A az irányítópult csempéi](./media/howto-set-up-template/locationcloudproperty7map.png) 

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT központi alkalmazás eszköz sablont beállításával, akkor a következőket teheti:

> [!div class="nextstepaction"]
> [Hozzon létre egy új eszköz sablon verziója](howto-version-devicetemplate.md)
