---
title: Új IoT-eszköztípus definiálása az Azure IoT Centralban | Microsoft dokumentumok
description: Ez a cikk bemutatja, mint építő, hogyan hozhat létre egy új Azure IoT-eszközsablont az Azure IoT Central-alkalmazásban. Megadhatja a típus telemetriai adatait, állapotát, tulajdonságait és parancsait.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 6f93d74653aab78e48e613ddf9252a0876548b95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157670"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Új IoT-eszköztípus definiálása az Azure IoT Central alkalmazásban

Az eszközsablon egy tervezet, amely meghatározza az Azure IoT Central alkalmazáshoz csatlakozó eszköztípusok jellemzőit és viselkedését.

Egy szerkesztő például létrehozhat egy eszközsablont egy csatlakoztatott ventilátorhoz, amely a következő jellemzőkkel rendelkezik:

- Hőmérséklet-telemetria küldése
- Helytulajdonság küldése
- Ventillátorhiba-események küldése
- Rajongói működési állapot küldése
- Írható ventilátorsebesség-tulajdonságot biztosít
- Az eszköz újraindítására irányuló paranccsal
- Átfogó képet ad a készülékről egy irányítópulton keresztül

Ebből az eszközsablonból az operátor valódi ventilátoreszközöket hozhat létre és csatlakoztathat. Mindezek a ventilátorok olyan mértékekkel, tulajdonságokkal és parancsokkal rendelkeznek, amelyeket az operátorok a figyelésükre és kezelésére használnak. Az operátorok az eszköz irányítópultjait és űrlapjait használják a ventilátoreszközökkel való interakcióhoz.

> [!NOTE]
> Csak a szerkesztők és a rendszergazdák hozhatnak létre, szerkeszthetnek és törölhetnek eszközsablonokat. Bármely felhasználó létrehozhat eszközöket az **Eszközök** lapon a meglévő eszközsablonokból.

[Az IoT Plug and Play (előzetes verzió)](../../iot-pnp/overview-iot-plug-and-play.md) lehetővé teszi az IoT Central számára az eszközök integrálását anélkül, hogy bármilyen beágyazott eszközkódot írna. Az IoT Plug and Play (előzetes verzió) középpontjában egy eszközképességi modellséma található, amely ismerteti az eszköz képességeit. Az IoT Central-alkalmazásokban az eszközsablonok ezeket az IoT Plug and Play (előzetes verzió) eszközképességi modelleket használják.

Szerkesztőként számos lehetősége van az eszközsablonok létrehozására:

- Tervezze meg az eszközsablont az IoT Centralban, majd valósítsa meg az eszközképességi modelljét az eszközkódban.
- Importáljon egy eszközképességi modellt az [Azure Certified for IoT eszközkatalógusból.](https://aka.ms/iotdevcat) Ezután adja hozzá az IoT Central alkalmazás igényeinek megfelelő felhőtulajdonságokat, testreszabásokat és irányítópultokat.
- Hozzon létre egy eszközképességi modellt a Visual Studio-kód használatával. Valósítsa meg az eszközkódját a modellből. Manuálisan importálja az eszközképességi modellt az IoT Central alkalmazásba, majd adja hozzá a felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat, amelyeket az IoT Central alkalmazás igényeinek szüksége van.
- Hozzon létre egy eszközképességi modellt a Visual Studio-kód használatával. Valósítsa meg az eszköz kódját a modellből, és csatlakoztassa a valódi eszközt az IoT Central alkalmazáshoz egy eszköz-első kapcsolat használatával. Az IoT Central megkeresi és importálja az eszközképesség-modellt a nyilvános tárházból. Ezután hozzáadhatja az IoT Central-alkalmazás által igényelt felhőtulajdonságokat, testreszabásokat és irányítópultokat az eszközsablonhoz.

## <a name="create-a-device-template-from-the-device-catalog"></a>Eszközsablon létrehozása az eszközkatalógusból

Szerkesztőként gyorsan elkezdheti kiépíteni a megoldást egy IoT Plug and Play (előzetes verzió) tanúsítvánnyal rendelkező eszköz használatával. Tekintse meg a listát az [Azure IoT-eszközkatalógusban.](https://catalog.azureiotsolutions.com/alldevices) Az IoT Central integrálható az eszközkatalógussal, így az alábbi IoT Plug and Play (előzetes verzió) tanúsítvánnyal rendelkező eszközök bármelyikéből importálhat egy eszközképességi modellt. Eszközsablon létrehozása az alábbi eszközök egyikéről az IoT Centralban:

1. Nyissa meg az IT Central alkalmazás **Eszközsablonok** lapját.
1. Válassza a **+ Új**lehetőséget, majd válassza ki az IoT Plug and Play (előzetes verzió) tanúsítvánnyal rendelkező eszközöket a katalógusból. Az IoT Central létrehoz egy eszközsablont ezen eszközképességi modell alapján.
1. Bármilyen felhőtulajdonságot, testreszabást vagy nézetet hozzáadhat az eszközsablonhoz.
1. Válassza **a Közzététel** lehetőséget, ha azt szeretné, hogy a sablon elérhető legyen az operátorok számára az eszközök megtekintéséhez és csatlakoztatásához.

## <a name="create-a-device-template-from-scratch"></a>Teljesen új eszközsablon létrehozása

Az eszközsablon a következőket tartalmazza:

- Az _eszköz képességi modellje,_ amely meghatározza az eszköz által megvalósított telemetriai adatokat, tulajdonságokat és parancsokat. Ezek a képességek egy vagy több felületbe vannak rendezve.
- _Felhőalapú tulajdonságok,_ amelyek meghatározzák az IoT Central alkalmazás által az eszközökről tárolt adatokat. Egy felhőtulajdonság például rögzítheti az eszköz utolsó szervizelésének dátumát. Ezeket az információkat soha nem osztjuk meg az eszközzel.
- A testreszabások lehetővé _teszik,_ hogy a szerkesztő felülbíráljon néhány definíciót az eszközképességi modellben. A szerkesztő például felülbírálhatja egy eszköztulajdonság nevét. A tulajdonságnevek az IoT Central irányítópultjain és űrlapjain jelennek meg.
- _Az irányítópultok és űrlapok_ lehetővé teszik, hogy a szerkesztő olyan felhasználói felületet hozzon létre, amely lehetővé teszi az operátorok számára az alkalmazáshoz csatlakoztatott eszközök figyelését és kezelését.

Eszközsablon létrehozása az IoT Centralban:

1. Nyissa meg az IT Central alkalmazás **Eszközsablonok** lapját.
1. Válassza **a + Új** > **egyéni**lehetőséget .
1. Adja meg a sablon nevét, például **a Környezetérzékelő**t.
1. Nyomja **le az Enter billentyűt.** Az IoT Central egy üres eszközsablont hoz létre.

## <a name="manage-a-device-template"></a>Eszközsablon kezelése

Sablont átnevezhet vagy törölhet a sablon kezdőlapjáról.

Miután hozzáadott egy eszközképességi modellt a sablonhoz, közzéteheti azt. Asablon közzétételéig nem csatlakoztathat egy eszközt ezen sablon alapján, hogy az operátorok megjelenhessenek az **Eszközök** lapon.

## <a name="create-a-capability-model"></a>Képességmodell létrehozása

Eszközképességi modell létrehozásához a következőket teheti:

- Az IoT Central használatával hozzon létre egy teljesen új egyéni modellt.
- Modell importálása JSON-fájlból. Előfordulhat, hogy egy eszközszerkesztő a Visual Studio-kód segítségével készített egy eszközképességi modellt az alkalmazáshoz.
- Válasszon egyet az eszközkatalógusból. Ez a beállítás importálja a gyártó által az eszközhöz közzétett eszközképesség-modellt. Az így importált eszközképesség-modell automatikusan közzétételre kerül.

## <a name="manage-a-capability-model"></a>Képességmodell kezelése

Az eszközképességi modell létrehozása után a következőket teheti:

- Csatolók hozzáadása a modellhez. A modellnek legalább egy felülettel kell rendelkeznie.
- A modell metaadatainak, például azonosítójának, névterjnek és nevének szerkesztése.
- Törölje a modellt.

## <a name="create-an-interface"></a>Kapcsolat létrehozása

Az eszközképességnek legalább egy kapcsolattal kell rendelkeznie. A felület a képességek újrafelhasználható gyűjteménye.

Kapcsolat létrehozása:

1. Nyissa meg az eszköz képességmodelljét, és válassza **a + Interface hozzáadása**lehetőséget.

1. Az **Illesztő kiválasztása** lapon a következőket teheti:

    - Hozzon létre egy teljesen új egyéni felületet.
    - Meglévő felület importálása fájlból. Előfordulhat, hogy egy eszközkészítő a Visual Studio-kódot használta az eszköz felületének megszerzői jogához.
    - Válasszon egyet a szabványos felületek közül, például az **Eszközinformáció** felület közül. A szabványos összeköttetések határozzák meg a sok eszköz közös képességeit. Ezeket a szabványos felületeket az Azure IoT teszi közzé, és nem lehet verziót verzióval vagy szerkesztett.

1. Miután létrehozott egy összeköttetést, válassza az **Identitás szerkesztése lehetőséget** a kapcsolat megjelenítendő nevének módosításához.

1. Ha úgy dönt, hogy teljesen új egyéni felületet hoz létre, hozzáadhatja az eszköz képességeit. Az eszköz képességei telemetriai adatok, tulajdonságok és parancsok.

### <a name="telemetry"></a>Telemetria

A telemetriai adatok az eszközről küldött értékek, általában egy érzékelőből küldött adatfolyam. Előfordulhat például, hogy egy érzékelő jelenti a környezeti hőmérsékletet.

Az alábbi táblázat a telemetriai funkciók konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | Az irányítópultokon és űrlapokon használt telemetriai érték megjelenítendő neve. |
| Név | A mező neve a telemetriai üzenetben. Az IoT Central létrehoz egy értéket ehhez a mezőhöz a megjelenítendő névből, de szükség esetén kiválaszthatja a saját értékét. |
| Képesség típusa | Telemetria. |
| Szemantikai típus | A telemetria szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus kiválasztása határozza meg, hogy a következő mezők közül melyik érhető el. |
| Séma | A telemetriai adattípus, például dupla, karakterlánc vagy vektor. A rendelkezésre álló választási lehetőségeket a szemantikai típus határozza meg. Séma nem érhető el az esemény és az állapot szemantikai típusok. |
| Severity | Csak az esemény szemantikai típushoz érhető el. A végkielégítések **a következők: Hiba**, **Információ**vagy **Figyelmeztetés**. |
| Állami értékek | Csak az állam szemantikai típushoz érhető el. Adja meg a lehetséges állapotértékeket, amelyek mindegyike megjelenítendő névvel, névvel, felsorolási típussal és értékkel rendelkezik. |
| Unit (Egység) | A telemetriai érték egy egysége, például **mph**, **%** vagy ** &deg;C.** |
| Kijelző egység | Az irányítópultokon és űrlapokon használható kijelzőegység. |
| Megjegyzés | A telemetriai funkcióval kapcsolatos megjegyzések. |
| Leírás | A telemetriai képesség leírása. |

### <a name="properties"></a>Tulajdonságok

A tulajdonságok az időponthoz való értékeket jelölik. Egy eszköz például egy tulajdonság segítségével jelentheti az elérni kívánt hőmérsékletet. Az IoT Central írható tulajdonságait beállíthatja.

Az alábbi táblázat egy tulajdonságképesség konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | Az irányítópultokon és űrlapokon használt tulajdonságérték megjelenítendő neve. |
| Név | A tulajdonság neve. Az IoT Central létrehoz egy értéket ehhez a mezőhöz a megjelenítendő névből, de szükség esetén kiválaszthatja a saját értékét. |
| Képesség típusa | Tulajdonság. |
| Szemantikai típus | A tulajdonság szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus kiválasztása határozza meg, hogy a következő mezők közül melyik érhető el. |
| Séma | A tulajdonság adattípusa, például dupla, karakterlánc vagy vektor. A rendelkezésre álló választási lehetőségeket a szemantikai típus határozza meg. Séma nem érhető el az esemény és az állapot szemantikai típusok. |
| Írható | Ha a tulajdonság nem írható, az eszköz jelentheti a tulajdonságértékeket az IoT Centralnak. Ha a tulajdonság írható, az eszköz jelentheti a tulajdonságértékeket az IoT Central nak, és az IoT Central tulajdonságfrissítéseket küldhet az eszközre.
| Severity | Csak az esemény szemantikai típushoz érhető el. A végkielégítések **a következők: Hiba**, **Információ**vagy **Figyelmeztetés**. |
| Állami értékek | Csak az állam szemantikai típushoz érhető el. Adja meg a lehetséges állapotértékeket, amelyek mindegyike megjelenítendő névvel, névvel, felsorolási típussal és értékkel rendelkezik. |
| Unit (Egység) | A tulajdonság értékének egy egysége, például **mph**, **%** vagy ** &deg;C**. |
| Kijelző egység | Az irányítópultokon és űrlapokon használható kijelzőegység. |
| Megjegyzés | A tulajdonsággal kapcsolatos megjegyzések. |
| Leírás | A tulajdonságképesség leírása. |

### <a name="commands"></a>Parancsok

Az IOt Central eszközparancsait hívhatja meg. A parancsok opcionálisan paramétereket adnak át az eszköznek, és választ kapnak az eszköztől. Meghívhat például egy parancsot, hogy 10 másodperc alatt újraindítsa az eszközt.

Az alábbi táblázat a parancsképesség konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | Az irányítópultokon és űrlapokon használt parancs megjelenítendő neve. |
| Név | A parancs neve. Az IoT Central létrehoz egy értéket ehhez a mezőhöz a megjelenítendő névből, de szükség esetén kiválaszthatja a saját értékét. |
| Képesség típusa | Parancs. |
| Parancs | `SynchronousExecutionType`. |
| Megjegyzés | A parancsképességgel kapcsolatos megjegyzések. |
| Leírás | A parancsképesség leírása. |
| Kérés | Ha engedélyezve van, a kérelem paraméterének definíciója, beleértve a következőket: név, megjelenítendő név, séma, egység és megjelenítési egység. |
| Válasz | Ha engedélyezve van, a parancsválasz definíciója, beleértve a következőket: név, megjelenítendő név, séma, egység és megjelenítési egység. |

## <a name="manage-an-interface"></a>Kezelőfelület kezelése

Ha még nem tette közzé a felületet, szerkesztheti a felület által meghatározott képességeket. A kapcsolat közzététele után, ha módosításokat szeretne végrehajtani, létre kell hoznia az eszközsablon új verzióját, és a felületet. A **Testreszabás** szakaszban olyan módosításokat hajthat végre, amelyekhez nincs szükség verziószámozásra, például megjelenítendő neveket vagy egységeket.

A felületet JSON-fájlként is exportálhatja, ha azt egy másik képességmodellben szeretné újra felhasználni.

## <a name="add-cloud-properties"></a>Felhőtulajdonságok hozzáadása

A felhőbeli tulajdonságok használatával az IoT Central ban lévő eszközök adatait tárolhatja. A felhőtulajdonságok at soha nem küldi el a rendszer egy eszközre. A felhőbeli tulajdonságok segítségével például tárolhatja az eszközt telepített ügyfél nevét, vagy az eszköz utolsó szervizdátumát.

Az alábbi táblázat egy felhőtulajdonság konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | Az irányítópultokon és űrlapokon használt felhőtulajdonság-érték megjelenítendő neve. |
| Név | A felhőtulajdonság neve. Az IoT Central létrehoz egy értéket ehhez a mezőhöz a megjelenítendő névből, de szükség esetén kiválaszthatja a saját értékét. |
| Szemantikai típus | A tulajdonság szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus kiválasztása határozza meg, hogy a következő mezők közül melyik érhető el. |
| Séma | A felhőtulajdonság adattípusa, például dupla, karakterlánc vagy vektor. A rendelkezésre álló választási lehetőségeket a szemantikai típus határozza meg. |

## <a name="add-customizations"></a>Testreszabások hozzáadása

Testreszabások használata, ha módosítania kell egy importált felületet, vagy hozzá kell adnia az IoT-középspecifikus funkciókat egy képességhez. Csak olyan mezőket szabhat testre, amelyek nem szakítják meg a felület kompatibilitását. Megteheti például a következőt:

- A funkció megjelenítendő nevének és egységeinek testreszabása.
- Adjon hozzá egy alapértelmezett színt, amelyet akkor használ, ha az érték megjelenik a diagramon.
- Adja meg egy tulajdonság kezdeti, minimális és maximális értékét.

A képesség neve vagy a képességtípusa nem szabható testre. Ha a **Testreszabás** szakaszban nem hajtható végre módosítások, a funkció módosításához módosítania kell az eszközsablont és -felületet.

### <a name="generate-default-views"></a>Alapértelmezett nézetek létrehozása

Az alapértelmezett nézetek létrehozása gyors módja a fontos eszközadatok megjelenítésének. Legfeljebb három alapértelmezett nézet jött létre az eszközsablonhoz:

- **A parancsok** eszközparancsokkal rendelkeznek, és lehetővé teszik a kezelő számára, hogy azokat az eszközre küldje.
- **Az áttekintés** eszköztelemetriával, diagramok és metrikák megjelenítésével jeleníti meg a nézetet.
- **A Körülbelül** alkalmazás az eszközadatokat tartalmazó nézetet biztosít az eszköz tulajdonságok megjelenítésével.

Miután beválasztotta **az Alapértelmezett nézetek létrehozása**lehetőséget, láthatja, hogy az eszközsablon **Nézetek** szakaszában automatikusan hozzáadták őket.

## <a name="add-dashboards"></a>Irányítópultok hozzáadása

Irányítópultok hozzáadása eszközsablonhoz, hogy az operátorok diagramok és mérőszámok használatával vizualizálhassanak egy eszközt. Egy eszközsablonhoz több irányítópult is lehet.

Irányítópult hozzáadása eszközsablonhoz:

1. Nyissa meg az eszközsablont, és válassza **a Nézetek lehetőséget.**
1. Válassza **az Eszköz megjelenítése**lehetőséget.
1. Írja be az irányítópult nevét az **Irányítópult neve mezőbe.**
1. Csempéket adhat az irányítópulthoz a statikus, a tulajdonság, a felhőtulajdonság, a telemetriai adatok és a parancscsempék listájából. Húzza az irányítópulthoz hozzáadni kívánt csempéket.
1. Ha több telemetriai értéket szeretne ábrázolni egyetlen diagramcsempén, jelölje ki a telemetriai értékeket, majd kattintson az **Egyesítés gombra.**
1. Állítsa be a hozzáadott csempéket az adatok megjelenítésének testreszabásához. Ehhez válassza a fogaskerék ikont, vagy válassza a **Konfiguráció módosítása** lehetőséget a diagramcsempén.
1. Rendezze el és méretezze át a csempéket az irányítópulton.
1. Mentse a módosításokat.

### <a name="configure-preview-device-to-view-dashboard"></a>Preview-eszköz konfigurálása az irányítópult megtekintéséhez

Az irányítópult megtekintéséhez és teszteléséhez válassza **az Előnézeti eszköz konfigurálása**lehetőséget. Ez lehetővé teszi, hogy az irányítópultot úgy tekintse meg, ahogy azt az operátor látja a közzététel után. Ezzel a beállítással ellenőrizheti, hogy a nézetek a megfelelő adatokat jelenítik-e meg. Az alábbiak közül választhat:

- Nincs előnézeti eszköz.
- Az eszközsablonhoz konfigurált valódi teszteszköz.
- Az alkalmazás ban lévő eszköz az eszközazonosító használatával.

## <a name="add-forms"></a>Űrlapok hozzáadása

Űrlapok hozzáadása eszközsablonhoz, hogy az operátorok a tulajdonságok megtekintésével és beállításával kezelhessék az eszközt. Az operátorok csak a felhő tulajdonságait és az írható eszköztulajdonságokat szerkeszthetik. Egy eszközsablonhoz több űrlap is lehet.

Űrlap hozzáadása eszközsablonhoz:

1. Nyissa meg az eszközsablont, és válassza **a Nézetek lehetőséget.**
1. Válassza **az Eszköz- és felhőadatok szerkesztése lehetőséget.**
1. Írja be az űrlap nevét az **Űrlap név mezőbe.**
1. Adja meg az űrlap elrendezéséhez használni kívánt oszlopok számát.
1. Adjon tulajdonságokat az űrlap egy meglévő szakaszához, vagy jelölje ki a Tulajdonságok lehetőséget, és válassza **a Szakasz hozzáadása lehetőséget.** Szakaszok használatával csoportosíthatja az űrlap tulajdonságait. Egy szakaszhoz címet is hozzáadhat.
1. Konfigurálja az űrlap egyes tulajdonságait a viselkedés testreszabásához.
1. Rendezze el az űrlap tulajdonságait.
1. Mentse a módosításokat.

## <a name="publish-a-device-template"></a>Eszközsablon közzététele

Az eszközképességi modellt megvalósító eszköz csatlakoztatása előtt közzé kell tennie az eszközsablont.

Az eszközsablon közzététele után csak korlátozott módosításokat hajthat végre az eszközképességi modellen. A felület módosításához új verziót kell [létrehoznia és közzétennie.](./howto-version-device-template.md)

Eszközsablon közzétételéhez nyissa meg az eszközsablont, és válassza a **Közzététel**lehetőséget.

Az eszközsablon közzététele után az operátor megléphet az **Eszközök** lapon, és hozzáadhat valós vagy szimulált eszközöket, amelyek az eszközsablont használják. A módosítások végrehajtásakor folytathatja az eszközsablon módosítását és mentését. Ha ezeket a módosításokat ki szeretné tolni az operátornak, hogy megtekinthesse az **Eszközök** lapon, minden alkalommal válassza **a Közzététel** lehetőséget.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Hozzon létre egy új IoT-eszközsablont.
* Hozzon létre felhőtulajdonságokat.
* Testreszabások létrehozása.
* Vizualizációdefiniálása az eszköz telemetriai adataihoz.
* Tegye közzé az eszközsablont.

Ezután a következőket teheti:

> [!div class="nextstepaction"]
> [Eszköz csatlakoztatása](howto-connect-devkit.md)
