---
title: Egy Azure IoT központi alkalmazásban eszköz sablon beállítása |} Microsoft Docs
description: Megtudhatja, hogyan mérések, beállítások, tulajdonságok, szabályok és irányítópult eszköz sablon beállítása.
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0bef8722ee51c916652e6964305f324341052341
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754550"
---
# <a name="set-up-a-device-template"></a>Eszköz sablon beállítása

Egy eszköz sablon egy tervezetének, jellemzőit és egy eszköz, amely csatlakozik a Microsoft Azure IoT központi alkalmazások típusú viselkedés határozza meg.

Például egy jelentéskészítő, amelynek csatlakoztatott IoT-ventilátor eszköz sablont hozhat létre:

- Hőmérséklet-telemetria mérési

- Ventilátor motor hiba esemény mérési

- Működési állapot mérési ventilátor

- Ventilátor beállítása

- Location tulajdonság

- Szabályok, amelyek az értesítések küldése

- Amely lehetővé teszi az eszközzel kapcsolatos nézetet az összes irányítópult

A eszköz sablonból operátor hozhat létre és valós ventilátor eszközök csatlakoztatása a névvel, mint **ventilátor.-1** és **ventilátor-2**. A ventilátorok rendelkezik mérések, a beállításokat és a tulajdonságok, szabályok, és egy irányítópultot, amely a felhasználók az alkalmazás figyelése és kezelése.

> [!NOTE]
> Csak a létrehozói és a rendszergazdák létrehozása, szerkesztése és törlése eszköz sablonok. Bármely felhasználó létrehozhat az eszközök a **eszköz Explorer** meglévő eszköz felügyeleticsomag-sablonok lap.

## <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása

1. Keresse meg a **alkalmazás jelentéskészítő** lap.

2. Hozzon létre egy üres sablont, válassza a **eszköz sablon létrehozása**, és válassza a **egyéni**.

3. Adja meg az új eszköz-sablon nevét, és válassza a **létrehozása**.

    ![Eszköz részleteit megjelenítő oldalra](./media/howto-set-up-template/devicedetailspage.png)

4. Épp most a **eszközadatok** új szimulált eszköz oldalán. A szimulált eszköz automatikusan létrejön egy új eszköz sablon létrehozásakor. Adatok jelentéseket, és szabályozhatja csakúgy, mint egy valós eszközt.

Egyes lapokon nézze most a **eszközadatok** lap.

## <a name="measurements"></a>Mérések

Mérések az adatok az eszközről érkező. Az eszköz lehetőségeinek megfelelő eszköz sablonjára több mérések is hozzáadhat. Telemetriai adatok és az esemény jelenleg támogatott mérések típusú.

- **Telemetria** mérések a numerikus adatpontok az eszköz által összegyűjtött és állandó folyama képviseli. Például hőmérséklet.
- **Esemény** méréseket időpontban adatok jelentő jelentőséggel az eszközön. Események súlyossági hozzájuk társítva az esemény fontosságát jelölő rendelkezik. Például a motor hiba ventilátor
- **Állapot** mérések határoz meg az eszköz vagy az összetevők állapotát egy meghatározott időtartamra vonatkozóan. Például ventilátor módban működő rendelkezőként definiált és leállt, a két lehetséges állapota lehet.

### <a name="create-a-telemetry-measurement"></a>A telemetriai adatok mérési létrehozása
Egy új telemetriai mérési hozzáadásához kattintson a **+ új mérési** , amelyek segítségével választhat, állítsa be a mértékegységet az űrlap megnyitása gombra. Válassza ki **Telemetriai** , és adja meg a részleteket a **létrehozása Telemetriai** űrlap.

> [!NOTE]
> Ha egy valódi eszköz csatlakoztatott, kell fizetnie, amennyit a mérték neve figyelmet az eszköz jelentés készít. A név pontosan meg kell egyeznie a **mezőnév** egy mérték.

Hozzáadhat például egy új hőmérséklet telemetriai mérési:

![Mérések képernyő](./media/howto-set-up-template/measurementsform.png)

A választott **mentése**, a **hőmérséklet** mérési mérések listájában jelenik meg, és egy olyan operátort látja a képi megjelenítés gyűjtő, az eszköz hőmérséklete adatok.

![Mérések diagramhoz](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>Egy esemény mérték létrehozása
Egy új esemény mérési hozzáadásához kattintson a **+ új mérési** , amelyek segítségével választhat, állítsa be a mértékegységet az űrlap megnyitása gombra. Válassza ki **esemény** , és adja meg a részleteket a **esemény létrehozása** űrlap.

Ezen a képernyőn adja meg a **megjelenített név**, **mezőnév**, és a **súlyossági** az esemény. A súlyosság - három szintjei közül választhat **hiba**, **figyelmeztetés**, és **információk**.  

Például hozzáadhat egy új "Ventilátor Motor Error" esemény.

![Esemény mérések képernyő](./media/howto-set-up-template/eventmeasurementsform.png)

A választott **mentése**, a **ventilátor Motor hiba** mérési mérések listájában jelenik meg, és egy olyan operátort látja a képi megjelenítés az küld az eszköz eseményadatok.

![Esemény mérések diagram](./media/howto-set-up-template/eventmeasurementschart.png)

Az eseménnyel kapcsolatos további részletek megtekintéséhez kattintson az esemény ikonra a diagram:

![Esemény mérések részletei](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>Hozzon létre egy állapot mérési
Új állapot a mérés hozzáadásához kattintson a **+ új mérési** , amelyek segítségével választhat, állítsa be a mértékegységet az űrlap megnyitása gombra. Válassza ki **állapot** , és adja meg a részleteket a **létrehozás állapot** űrlap.

Ezen a képernyőn adja meg a **megjelenített név**, **mezőnév**, és a lehetséges **értékek** az állapot. Minden egyes **érték** megjelenített név, fogja használni, amikor az értéket ábrázoló diagramok és táblák is lehet.

Például hozzáadhat egy új "Ventilátor mód" állapotát, amely az eszköz küldő két lehetséges értékkel rendelkezik, **operációs** és **leállítva**.

![Állapot mérések képernyő](./media/howto-set-up-template/statemeasurementsform.png)

Kiválasztása után **mentése**, a **ventilátor mód** állapot mérési mérések listájában jelenik meg, és az üzemeltető látja a képi megjelenítés küld az eszköz állapota adatok.

![Állapot mérések diagram](./media/howto-set-up-template/statemeasurementschart.png)

Abban az esetben, ha az eszköz túl sok adatpontot küld egy rövid időtartam alatt, az állapot mérése más vizualizációval jelenik meg, az alább látható módon. Ha a diagramra kattint, akkor az abba az időszakba eső összes adatpont időrendben jelenik meg. Az időtartományt, tekintse meg a mérték a diagramon ábrázolt is szűkítéséhez.

![Állapot mérések részletei](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>Beállítások

Beállítások egy eszköz vezérlésére. Az alkalmazás arra, hogy az eszközhöz bevitelek operátorok lehetővé teszik. Az eszköz sablon megjelenő mozaikként több beállítást is hozzáadhat a **beállítások** fülre az operátorok használatára. Hat különböző típusú beállításokat is hozzáadhat: szám, dátum, váltása, kivételezési listát, és a szakasz címke.

> [!NOTE]
> Ha egy valódi eszköz csatlakozik, különös a beállítás neve a eszközökről készült jelentések. A név pontosan meg kell egyeznie a **mezőnév** a beállítását.

Beállítások az alábbi három állapotban lehet. Ezeket az állapotokat az eszköz által jelentett.

- **Szinkronizált**: az eszköz megfelelően a beállítás értéke megváltozott.

- **Függőben lévő**: az eszköz jelenleg vált át a beállítás értéke.

- **Hiba**: az eszköz hibát adott vissza.

Például egy új ventilátor sebesség beállítást is hozzáadhat:

![Beállítások képernyő](./media/howto-set-up-template/settingsform.png)

Kiválasztása után **mentése**, a **sebesség ventilátor** beállítás csempe jelenik meg, és készen áll az módosítani szeretné a ventilátor sebességét az eszköz használható.

Miután létrehozott egy új csempe, kipróbálhatja az új beállítással. Először, kapcsolja ki a tervezési módba, a bal felső sarkában a képernyőn.

![Beállításai lapon](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>Tulajdonságok

A rendszer az eszköz metaadatait az eszköz, például az eszköz helyét és sorozatszám társított tulajdonságokat. Az eszköz sablon, amely a csempék jelennek meg több tulajdonságot is hozzáadhat a **tulajdonságok** fülre. Operátor megadhatja az értékeket azokhoz a tulajdonságokhoz, hoznak létre egy új eszközt, és bármikor ezeket az értékeket szerkesztheti azokat. Hat különböző tulajdonságokat is hozzáadhat: szám, dátum, váltása, eszköztulajdonságon, és címke.

Tulajdonságok két típusa van:

- **Eszköztulajdonságok** tulajdonságok a eszköz által jelentett.
- **Alkalmazástulajdonságok** pusztán az alkalmazásban tárolt tulajdonságai. Az eszköz regisztrációját nincsenek saját ismeretei az alkalmazás tulajdonságait.

> [!NOTE]
> Az eszköz tulajdonságok, ha egy valódi eszköz csatlakoztatott, kell fizetnie, amennyit figyelmet a tulajdonság nevét az eszköz jelentés készít. A név pontosan meg kell egyeznie a **mezőnév** tulajdonság. Az alkalmazás tulajdonságait, a mező neve lehet bármilyen, mindaddig, amíg az eszköz sablon egyedi neve.

Például egy új tulajdonságként eszköz helyét is hozzáadhat:

![Tulajdonságok képernyő](./media/howto-set-up-template/propertiesform.png)

Miután kiválasztotta **mentése**, eszköz helyét csempe jelenik meg:

![Tulajdonságok csempéje](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> Után hoz létre egy új csempe, módosíthatja a tulajdonság értékét. Először, kapcsolja ki a tervezési módba, a bal felső sarkában a képernyőn.

### <a name="create-a-location-property-powered-by-azure-maps"></a>Hozzon létre egy Azure Maps technológiával helyet jelölő tulajdonsághoz
Földrajzi környezet rendszerében a hely adatait az Azure IoT-központ, és a szélességi és hosszúsági koordinátáját egy utca, házszám, vagy egyszerűen a szélességi és hosszúsági koordinátákkal hozzárendelését. Ez a funkció az Azure IoT központi Azure leképezésekhez van-e kapcsolva.

A hely tulajdonságai adhat hozzá két típusa van:
- **A helyen, ahol az alkalmazás tulajdonság** pusztán az alkalmazásban kerülnek. Az eszköz regisztrációját nincsenek saját ismeretei az alkalmazás tulajdonságait.
- **Hely eszköz tulajdonságként** az eszköz megjelenik.

#### <a name="adding-location-as-an-application-property"></a>Egy alkalmazás tulajdonság hely hozzáadása 

Létrehozhat egy helyet tulajdonság használatával az Azure alkalmazás tulajdonságként hozzárendeli az Azure IoT központi alkalmazásban. Például az eszköz telepítési címe is hozzáadhat. 

1. Keresse meg az eszköz tulajdonságlap; Győződjön meg arról, tervezési üzemmód engedélyezve van.

   ![Location tulajdonság](./media/howto-set-up-template/locationcloudproperty1.png)

2. A tulajdonság lapon kattintson a helyen.
3. Megjelenített név, mező nevét és a kezdeti érték, a hely konfigurálása a opcionálisan. 

   ![Hely tulajdonság képernyő](./media/howto-set-up-template/locationcloudproperty2.png)

   Nincsenek két támogatott formátumok hely hozzáadásához:
   - **Hely-címként**
   - **Hely-koordináták** 

4.  Kattintson a **Save** (Mentés) gombra. 

    ![A Location tulajdonság mező](./media/howto-set-up-template/locationcloudproperty3.png)

Most egy olyan operátort frissítheti a hely mező űrlap a hely értéke. 

#### <a name="adding-location-as-a-device-property"></a>Eszköz tulajdonságként hely hozzáadása 

Az eszköz által jelentett eszköz tulajdonságként hozhat létre a helyet jelölő tulajdonsághoz.
Például szeretné nyomon követni az eszköz helyét.

1.  Keresse meg az eszköz tulajdonságlap; Győződjön meg arról, tervezési üzemmód engedélyezve van.

2.  Kattintson a könyvtárból eszköz tulajdonság.

    ![A Location tulajdonság mező](./media/howto-set-up-template/locationdeviceproperty1.png)

3.  Konfigurálja a megjelenített név, a mező nevét, és válassza ki a "hely" adattípusú értékként. 

    > [!NOTE]
    > A mezőnév pontosan egyeznie kell a tulajdonság az eszköz nevére jelentéseket. 

    ![A Location tulajdonság mező](./media/howto-set-up-template/locationdeviceproperty2.png)

    ![Hely tulajdonság operátor nézet](./media/howto-set-up-template/locationdeviceproperty2.png)

Most, hogy a location tulajdonság van beállítva, akkor fog tudni vegyen fel egy társítást a helyet, az eszköz irányítópult megjelenítéséhez. Lásd: hogyan [hely hozzáadása Azure térkép irányítópulton](howto-set-up-template.md#add-location-azure-map-in-dashboard).

## <a name="commands"></a>Parancsok

Parancsok segítségével a eszköz távoli felügyelete. Lehetővé teszik az operátorok az alkalmazás azonnal futtassa a parancsokat az eszközön. Az eszköz sablon megjelenő mozaikként több parancs is hozzáadhat a **parancsok** fülre az operátorok használatára. A szerkesztő az eszköz, így rugalmasan megadhatók a parancs a követelményeknek.

Hogyan van egy **parancs** eltér a **beállítás**? 

* : A beállítás egy eszközre alkalmazni kívánt konfigurációs, és azt szeretné, hogy a konfigurálás továbbra is fennáll, addig az eszköz. Például be szeretné állítani a feldolgozó hőmérséklete, és azt szeretné, a beállítás akkor is, ha a feldolgozó újra. 

* Parancs: Használatával parancsok azonnal futtassa a parancsot az eszköz távoli IoTCentral, ha egy eszköz nincs csatlakoztatva, majd a parancs időkorlátja, és a sikertelen. Például szeretné indítsa újra a számítógép újraindításához eszköz.  

Parancsok végrehajtásakor lehet az alábbi három állapotban, attól függően, hogy az eszköz a parancsot kapott. 

Például egy új echo paranccsal adhatja hozzá:

![Parancsok képernyő](./media/howto-set-up-template/commandsecho.png)

Miután kiválasztotta **mentése**, a **Echo** parancs csempe jelenik meg, és készen áll az Echo az eszköz használatával.

Miután létrehozott egy új csempe, kipróbálhatja az új parancsot.

## <a name="rules"></a>Szabályok

Szabályok szereplői eszközök közel valós idejű figyelése. Szabályok automatikus meghívása **műveletek** például egy e-mailek küldéséhez, amikor a szabály akkor váltja ki. Érhető el egy típusú szabályt ma:

- **Telemetria szabály:** telemetriai szabály váltja ki, ha a kijelölt telemetriát keverve használ a megadott küszöbértéket. További információ [telemetriai szabályok](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Irányítópult

Az irányítópult pedig operátor eszközök adatainak megjelenítéséhez hová. A jelentéskészítő, vegyen fel csempéket a ezen a lapon, a kezelők számára az eszköz működésének megértése. Az eszköz sablon több irányítópulton található csempe is hozzáadhat. Hat különböző típusú irányítópulton található csempe is hozzáadhat: kép, diagram, a sávdiagram, a KPI, a beállítások és a tulajdonságok és címkézését.

Például hozzáadhat egy **beállítások és a Tulajdonságok** mozaiklapra a kijelölt beállítások és tulajdonságainak aktuális értékeivel:

![Irányítópult eszköz részletei képernyőn](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

Most operátor megtekinti az irányítópulton, amikor ez a csempe megjeleníti a tulajdonságok és az eszköz beállításait láthatják:

![Irányítópult-csempe](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-location-azure-map-in-dashboard"></a>Hely hozzáadása Azure térkép-irányítópulton

Ha konfigurálta a helyet jelölő tulajdonsághoz hasonlóan a lépéseket [hozzon létre egy Azure Maps technológiával helyet jelölő tulajdonsághoz](howto-set-up-template.md), jelenítheti meg a helyét, a térkép lesz az eszköz irányítópulton jobbra.

1.  Keresse meg az eszköz irányítópult fület; Győződjön meg arról, tervezési üzemmód engedélyezve van.

2.  Az eszköz irányítópultot válassza ki a térkép a könyvtárból. 

    ![Irányítópult helyének Azure hozzárendelése kiválasztása](./media/howto-set-up-template/locationcloudproperty4map.png)

3.  Olyan címet adjon, és válassza ki a helyet jelölő tulajdonsághoz vannak konfigurálva a Eszköztulajdonságon részeként.

    ![Irányítópult helyét Azure leképezés konfigurálása](./media/howto-set-up-template/locationcloudproperty5map.png)

4.  Mentés és a térkép csempe megjelenítése a választott helyen jelenik meg. 

    ![Irányítópult helyét Azure térkép megjelenítése](./media/howto-set-up-template/locationcloudproperty6map.png) 

    A kívánt méretet a térképen átméretezése lehet.

    Most operátor megtekinti az irányítópulton, amikor az összes az irányítópulton található csempe konfigurálta, beleértve a térkép láthatják!

    ![Irányítópult hely Azure térkép irányítópult](./media/howto-set-up-template/locationcloudproperty7map.png) 

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan állíthat be az Azure IoT központi alkalmazás eszköz sablont, ez a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Hozzon létre egy új eszköz sablon verziója](howto-version-devicetemplate.md)
