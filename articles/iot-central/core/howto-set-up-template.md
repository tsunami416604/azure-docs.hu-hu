---
title: Új IoT-eszköz típusának meghatározása az Azure IoT Centralban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre új Azure IoT-sablont az Azure IoT Central alkalmazásban. Megadhatja a típus telemetria, állapotát, tulajdonságait és parancsait.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperfq1
- device-developer
ms.openlocfilehash: aa70c9e5d67c759afe905e9e110d6bcd18555a8c
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90019240"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Új IoT-eszköztípus definiálása az Azure IoT Central-alkalmazásban

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Az eszköz sablonja egy olyan terv, amely meghatározza egy [Azure IoT Central-alkalmazáshoz](concepts-app-templates.md)csatlakozó eszköz típusának jellemzőit és viselkedését.

A Builder például létrehozhat egy eszköz sablont egy csatlakoztatott ventilátorhoz, amely a következő jellemzőkkel rendelkezik:

- Hőmérséklet telemetria küld
- Location tulajdonság küldése
- Ventilátor motoros hibák eseményeinek küldése
- Ventilátor működési állapotának küldése
- Egy írható ventilátoros Speed tulajdonságot biztosít
- Az eszköz újraindítására szolgáló parancsot biztosít
- Általános áttekintést nyújt az eszközről egy irányítópulton keresztül

Az eszköz sablonja alapján a kezelők valódi ventilátoros eszközöket hozhatnak létre és csatlakozhatnak. Ezek a ventilátorok olyan mérésekkel, tulajdonságokkal és parancsokkal rendelkeznek, amelyeket az operátorok a figyelésre és felügyeletre használnak. A kezelők az eszközök [irányítópultját](#add-dashboards) és űrlapjait használják a ventilátorral való interakcióra. Az eszközök fejlesztői a sablont használják annak megismeréséhez, hogy az eszköz hogyan kommunikál az alkalmazással. További információ: [telemetria, Property és Command hasznos](concepts-telemetry-properties-commands.md)adatok.

> [!NOTE]
> Csak az építők és a rendszergazdák hozhatnak létre, szerkeszthetnek és törölhetnek eszközöket. Bármely felhasználó létrehozhat eszközöket az **eszközök** lapon a meglévő eszközök sablonjaiból.

Egy IoT Central alkalmazásban az eszköz egy eszköz képesség modell segítségével ismerteti az eszközök képességeit. A Builder számos lehetőséget kínál az eszközök sablonjainak létrehozására:

- Tervezze meg IoT Central az eszköz sablonját, majd [implementálja az eszköz képességeinek modelljét az eszköz kódjában](concepts-telemetry-properties-commands.md).
- Eszköz-képesség modell importálása az [Azure Certified for IoT Device Catalog](https://aka.ms/iotdevcat)eszközből. Ezután adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat.
- Hozzon létre egy eszköz-képesség modellt a Visual Studio Code használatával. Implementálja az eszköz kódját a modellből. Manuálisan importálja az eszköz képességeinek modelljét a IoT Central alkalmazásba, majd adja hozzá a IoT Central alkalmazás igényeinek megfelelő Felhőbeli tulajdonságokat, testreszabásokat és irányítópultokat.
- Hozzon létre egy eszköz-képesség modellt a Visual Studio Code használatával. Implementálja az eszköz kódját a modellből, és a valódi eszközt csatlakoztathatja a IoT Central alkalmazáshoz egy eszköz – első kapcsolat használatával. IoT Central megkeresi és importálja az eszköz képességeinek modelljét a nyilvános adattárból. Ezután hozzáadhat bármilyen Felhőbeli tulajdonságot, testreszabást és irányítópultot, amely az IoT Central alkalmazásnak az eszköz sablonját kell használnia.

Az IoT Central alkalmazáshoz a [REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) vagy a [parancssori](howto-manage-iot-central-from-cli.md)felület használatával is hozzáadhat eszközillesztőket.

Egyes [alkalmazás-sablonok](concepts-app-templates.md) már tartalmaznak olyan eszközöket, amelyek hasznosak lehetnek abban az esetben, amikor az alkalmazás sablonja támogatja. Lásd például: [in-store Analytics Architecture](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Eszköz sablonjának létrehozása az eszköz-katalógusból

Építőként gyorsan megkezdheti a megoldás kiépítését egy IoT Plug and Play (előzetes verzió) tanúsítvánnyal rendelkező eszköz használatával. Tekintse meg a listát az [Azure IoT-eszköz katalógusában](https://catalog.azureiotsolutions.com/alldevices). IoT Central integrálható az eszköz-katalógussal, így a IoT Plug and Play (előzetes verzió) tanúsítvánnyal rendelkező eszközökről importálhat egy eszköz-képesség modellt. Eszköz sablonjának létrehozása ezen eszközök egyikéről a IoT Centralban:

1. Nyissa meg az IoT Central alkalmazás **eszköz sablonok** lapját.
1. Válassza az **+ új**lehetőséget, majd válassza ki a katalógusból a IoT Plug and Play (előzetes verzió) tanúsítvánnyal rendelkező eszközöket. IoT Central létrehoz egy sablont ezen eszköz-képesség modell alapján.
1. Bármilyen Felhőbeli tulajdonságot, testreszabást és nézetet hozzáadhat az eszköz sablonhoz.
1. Válassza a **Közzététel** lehetőséget, hogy a sablon elérhető legyen az operátorok számára az eszközök megtekintéséhez és csatlakoztatásához.

## <a name="create-a-device-template-from-scratch"></a>Sablon létrehozása a semmiből

Az eszköz sablonjai A következőket tartalmazzák:

- Az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat meghatározó _eszköz-képességi modell_ . Ezeket a képességeket egy vagy több interfészbe rendezi a rendszer.
- A _felhő tulajdonságai_ , amelyek a IoT Central alkalmazás által az eszközökön tárolt adatokat határozzák meg. Előfordulhat például, hogy egy Felhőbeli tulajdonság rögzíti az eszköz legutóbbi kiszolgálásának dátumát. Ezeket az adatokat soha nem osztja meg az eszközzel.
- A _testreszabások_ lehetővé teszik, hogy a szerkesztő felülbírálja az eszköz képességeinek modellje definícióit. A szerkesztő például felülbírálhatja egy eszköz tulajdonságának a nevét. A tulajdonságok neve IoT Central irányítópultokon és űrlapokon jelenik meg.
- Az _irányítópultok és űrlapok_ lehetővé teszik, hogy a szerkesztő olyan felhasználói felületet hozzon létre, amely lehetővé teszi a kezelők számára az alkalmazáshoz csatlakoztatott eszközök figyelését és kezelését

Eszköz sablonjának létrehozása a IoT Centralban:

1. Nyissa meg az IoT Central alkalmazás **eszköz sablonok** lapját.
1. Válassza az **+ új**  >  **Egyéni**lehetőséget.
1. Adja meg a sablon nevét, például a **környezeti érzékelőt**.
1.  Nyomja le az **Enter** billentyűt. IoT Central létrehoz egy üres sablont.

## <a name="manage-a-device-template"></a>Eszköz sablonjának kezelése

A sablon kezdőlapján átnevezheti vagy törölheti a sablonokat.

Miután hozzáadta az eszköz képességeinek modelljét a sablonhoz, közzéteheti azt. Amíg nem tette közzé a sablont, nem tud csatlakozni az eszközhöz a sablon alapján, hogy az operátorok megjelenjenek az **eszközök** lapon.

## <a name="create-a-capability-model"></a>Képesség modell létrehozása

Eszköz-képesség modell létrehozásához a következőket teheti:

- A IoT Central használatával hozzon létre egy egyéni modellt a semmiből.
- Modell importálása JSON-fájlból. Előfordulhat, hogy egy eszköz-szerkesztő a Visual Studio Code-ot használta az alkalmazáshoz tartozó eszköz-képesség modell létrehozásához.
- Válasszon egy eszközt az eszköz-katalógusból. Ezzel a beállítással importálhatja azt az eszköz-képességi modellt, amelyet a gyártó közzétett az eszközön. Az ehhez hasonló eszköz-képesség modell automatikusan közzé lesz téve.

## <a name="manage-a-capability-model"></a>Képesség modell kezelése

Az eszköz-képesség modell létrehozása után a következőket teheti:

- Felületek hozzáadása a modellhez. A modellnek legalább egy csatolóval kell rendelkeznie.
- Szerkessze a modell metaadatait, például az azonosítót, a névteret és a nevet.
- Törölje a modellt.

## <a name="create-an-interface"></a>Felület létrehozása

Az eszköz képességeinek legalább egy csatolóval kell rendelkezniük. Az illesztőfelület a képességek újrafelhasználható gyűjteménye.

Felület létrehozása:

1. Nyissa meg az eszköz képességeinek modelljét, és válassza a **+ kapcsolat hozzáadása**elemet.

1. A **csatoló kiválasztása** lapon a következőket teheti:

    - Hozzon létre egy egyéni felületet a semmiből.
    - Meglévő illesztőfelület importálása egy fájlból. Előfordulhat, hogy egy eszközön a Visual Studio Code használatával létrehoztak egy felületet az eszközhöz.
    - Válasszon egyet a standard felületek közül, például az **eszköz adatai** felületet. A standard felületek a sok eszközhöz közös képességeket határozzák meg. Ezeket a standard felületeket az Azure IoT teszi közzé, és nem lehet verziószámmal vagy szerkesztéssel ellátott.

1. Miután létrehozta a felületet, az **identitás szerkesztése** elemre kattintva módosíthatja az interfész megjelenítendő nevét.

1. Ha úgy dönt, hogy új egyéni felületet hoz létre, hozzáadhatja az eszköz képességeit. Az eszköz képességei a következők: telemetria, tulajdonságok és parancsok.

### <a name="telemetry"></a>Telemetria

A telemetria az eszközről küldött értékek streamje, jellemzően egy érzékelőből. Egy érzékelő például jelenthetheti a környezeti hőmérsékletet.

A következő táblázat a telemetria képesség konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | Az irányítópultokon és űrlapokon használt telemetria érték megjelenítendő neve. |
| Name | A mező neve a telemetria üzenetben. IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. A mezőnek alfanumerikusnak kell lennie. |
| Képesség típusa | Telemetria. |
| Szemantikai típus | A telemetria szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus megválasztása határozza meg, hogy a következő mezők közül melyek érhetők el. |
| Séma | A telemetria adattípus, például Double, string vagy Vector. Az elérhető beállításokat a szemantikai típus határozza meg. A séma nem érhető el az esemény és az állapot szemantikai típusaihoz. |
| Súlyosság | Csak az esemény szemantikai típusához érhető el. A megszakítások a következők: **hiba**, **információ**vagy **Figyelmeztetés**. |
| Állapot értékei | Csak az állapot szemantikai típusához érhető el. Definiálja a lehetséges állapotinformációkat, amelyek mindegyike megjelenített névvel, névvel, számbavételi típussal és értékkel rendelkezik. |
| Unit (Egység) | A telemetria értékének (például: **mph**, **%** , vagy ** &deg; C**) egysége. |
| Megjelenítési egység | Irányítópultokon és űrlapokon használható megjelenítési egység. |
| Megjegyzés | A telemetria képességgel kapcsolatos megjegyzések. |
| Description | A telemetria képesség leírása. |

### <a name="properties"></a>Tulajdonságok

A tulajdonságok a pont – idő értékeket jelölik. Egy eszköz használhat például egy tulajdonságot a elérni kívánt cél hőmérséklet jelentésére. A IoT Central írható tulajdonságokat adhat meg.

A következő táblázat a tulajdonságok funkciójának konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | Az irányítópultokon és űrlapokon használt tulajdonságérték megjelenítendő neve. |
| Name | A tulajdonság neve. IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. A mezőnek alfanumerikusnak kell lennie. |
| Képesség típusa | Tulajdonság. |
| Szemantikai típus | A tulajdonság szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus megválasztása határozza meg, hogy a következő mezők közül melyek érhetők el. |
| Séma | A tulajdonság adattípusa, például Double, string vagy Vector. Az elérhető beállításokat a szemantikai típus határozza meg. A séma nem érhető el az esemény és az állapot szemantikai típusaihoz. |
| Írható | Ha a tulajdonság nem írható, az eszköz jelentést készíthet IoT Central. Ha a tulajdonság írható, az eszköz jelentést készíthet IoT Central, és IoT Central a tulajdonságok frissítését is elküldheti az eszköznek.
| Súlyosság | Csak az esemény szemantikai típusához érhető el. A megszakítások a következők: **hiba**, **információ**vagy **Figyelmeztetés**. |
| Állapot értékei | Csak az állapot szemantikai típusához érhető el. Definiálja a lehetséges állapotinformációkat, amelyek mindegyike megjelenített névvel, névvel, számbavételi típussal és értékkel rendelkezik. |
| Unit (Egység) | A tulajdonság értékének egysége, például: **mph**, **%** , vagy ** &deg; C**. |
| Megjelenítési egység | Irányítópultokon és űrlapokon használható megjelenítési egység. |
| Megjegyzés | A tulajdonság képességével kapcsolatos megjegyzések. |
| Description | A tulajdonság funkciójának leírása. |

### <a name="commands"></a>Parancsok

A IoT Central eszköz parancsai hívhatók. A parancsok opcionálisan továbbítják a paramétereket az eszköznek, és választ kapnak az eszköztől. Meghívhat például egy parancsot 10 másodperc alatt egy eszköz újraindítására.

A következő táblázat a parancs funkciójának konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | Az irányítópultokon és űrlapokon használt parancs megjelenítendő neve. |
| Name | A parancs neve. IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. A mezőnek alfanumerikusnak kell lennie. |
| Képesség típusa | Parancs. |
| Parancs | `SynchronousExecutionType`. |
| Megjegyzés | A parancs képességével kapcsolatos megjegyzések. |
| Description | A parancs funkciójának leírása. |
| Kérés | Ha engedélyezve van, a kérelem paraméterének definíciója, beleértve a következőket: név, megjelenítendő név, séma, egység és megjelenítési egység. |
| Reagálás | Ha engedélyezve van, a parancs válaszának definíciója, beleértve a következőket: név, megjelenítendő név, séma, egység és megjelenítési egység. |

## <a name="manage-an-interface"></a>Illesztőfelület kezelése

Ha még nem tette közzé a felületet, módosíthatja az illesztőfelület által definiált képességeket. Miután közzétette a felületet, ha módosítani kívánja a módosításokat, létre kell hoznia az eszköz sablonjának új verzióját és az illesztőfelület verzióját. A **Testreszabás** szakaszban olyan módosításokat végezhet, amelyek nem igényelnek verziószámozást, például megjelenítendő neveket vagy egységeket.

Azt is megteheti, hogy a felületet JSON-fájlként exportálja, ha újra szeretné használni egy másik képesség modellben.

## <a name="add-cloud-properties"></a>Felhőtulajdonságok hozzáadása

A felhő tulajdonságai a IoT Central lévő eszközök adatainak tárolására használhatók. A felhő tulajdonságai soha nem továbbítódnak az eszközre. Például a Cloud Properties használatával tárolhatja annak az ügyfélnek a nevét, aki az eszközt telepítette, vagy az eszköz utolsó szolgáltatásának dátuma.

A következő táblázat a Cloud Property konfigurációs beállításait mutatja be:

| Mező | Leírás |
| ----- | ----------- |
| Megjelenítendő név | Az irányítópultokon és űrlapokon használt Cloud Property érték megjelenítendő neve. |
| Name | A felhő tulajdonság neve IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. |
| Szemantikai típus | A tulajdonság szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus megválasztása határozza meg, hogy a következő mezők közül melyek érhetők el. |
| Séma | A Felhőbeli tulajdonság adattípusa, például Double, string vagy Vector. Az elérhető beállításokat a szemantikai típus határozza meg. |

## <a name="add-customizations"></a>Testreszabások hozzáadása

Ha módosítania kell egy importált felületet, vagy IoT Central-specifikus funkciókat kell hozzáadnia egy képességhez, használja a testreszabásokat. Csak olyan mezőket szabhat testre, amelyek nem bontják le az illesztőfelületek kompatibilitását. Megteheti például a következőt:

- Testreszabhatja egy képesség megjelenített nevét és egységeit.
- Adja meg az alapértelmezett színt, amelyet akkor kell használni, ha az érték megjelenik a diagramon.
- Egy tulajdonság kezdeti, minimális és maximális értékének megadása.

Nem szabhatja testre a képesség nevét vagy a képesség típusát. Ha módosítások nem hajthatók végre a **Testreszabás** szakaszban, az eszköz sablonját és felületét kell megadnia a funkció módosításához.

### <a name="generate-default-views"></a>Alapértelmezett nézetek előállítása

Az alapértelmezett nézetek létrehozásával gyorsan megjelenítheti az eszköz fontos adatait. Az eszköz sablonjában legfeljebb három alapértelmezett nézet hozható létre:

- A **parancsok** megjelenítik az eszköz parancsainak nézetét, és lehetővé teszik az operátor számára az eszközre történő küldést.
- Az **Áttekintés** az eszközök telemetria, a diagramok és a metrikák megjelenítését teszi lehetővé.
- A **Névjegy** az eszköz adatainak megtekintését, az eszköz tulajdonságainak megjelenítését ismerteti.

Miután kiválasztotta az **alapértelmezett nézetek létrehozása**lehetőséget, láthatja, hogy az eszköz sablonjának **nézetek** szakaszában automatikusan hozzá lettek adva.

## <a name="add-dashboards"></a>Irányítópultok hozzáadása

Irányítópultokat adhat hozzá egy sablonhoz, hogy az operátorok diagramok és metrikák használatával jelenítsék meg az eszközöket. Az eszközök sablonjaihoz több irányítópult is tartozhat.

Irányítópult hozzáadása egy eszköz sablonhoz:

1. Nyissa meg az eszköz sablonját, és válassza a **nézetek**lehetőséget.
1. Válassza ki **az eszköz megjelenítését**.
1. Adja meg az irányítópult nevét az **irányítópult neve**mezőben.
1. Csempe hozzáadása az irányítópulthoz a statikus, a tulajdonság, a Cloud Property, a telemetria és a Command csempék listájából. Húzza az irányítópultra felvenni kívánt csempéket.
1. Ha több telemetria-értéket szeretne ábrázolni egyetlen diagramon, válassza ki a telemetria-értékeket, majd kattintson az **összevonás**elemre.
1. Konfigurálja az egyes hozzáadott csempéket az adatmegjelenítés testreszabásához. Ezt úgy teheti meg, hogy a fogaskerék ikonra kattint, vagy kiválasztja a **konfiguráció módosítása** elemet a diagram csempén.
1. Rendezze és méretezze át a csempéket az irányítópulton.
1. Mentse a módosításokat.

### <a name="configure-preview-device-to-view-dashboard"></a>Az előnézet eszköz konfigurálása az irányítópult megtekintéséhez

Az irányítópult megtekintéséhez és teszteléséhez válassza az **előnézet eszköz konfigurálása**lehetőséget. Ez lehetővé teszi, hogy megtekintse az irányítópultot, amikor az operátor látja a közzététel után. Ezzel a beállítással ellenőrizheti, hogy a nézetek a megfelelő adatokon jelenjenek-e meg. Az alábbiak közül választhat:

- Nincs előnézeti eszköz.
- Az eszköz sablonja számára konfigurált valós tesztelési eszköz.
- Egy meglévő eszköz az alkalmazásban az eszköz AZONOSÍTÓjának használatával.

## <a name="add-forms"></a>Űrlapok hozzáadása

Űrlapokat adhat hozzá egy eszköz sablonhoz, hogy az operátorok a tulajdonságok megtekintésével és beállításával lehetővé tegyék az eszközök felügyeletét. Az operátorok csak a felhő tulajdonságait és az írható eszköz tulajdonságait módosíthatják. Az eszközök sablonjaihoz több űrlap is tartozhat.

Űrlap hozzáadása egy eszköz sablonhoz:

1. Nyissa meg az eszköz sablonját, és válassza a **nézetek**lehetőséget.
1. Válassza **az eszköz és a Felhőbeli adattárolás szerkesztése**lehetőséget.
1. Adja meg az űrlap nevét az **űrlap nevében**.
1. Válassza ki az űrlap elrendezéséhez használni kívánt oszlopok számát.
1. Adja hozzá a tulajdonságokat egy meglévő szakaszhoz az űrlapon, vagy válassza a tulajdonságok lehetőséget, és válassza a **Hozzáadás szakaszt**. Az űrlapon található tulajdonságok csoportosításához használjon szakaszt. Hozzáadhat egy címet egy szakaszhoz.
1. Konfigurálja az űrlap minden tulajdonságát, hogy testre szabja a viselkedését.
1. Rendezze a tulajdonságokat az űrlapon.
1. Mentse a módosításokat.

## <a name="publish-a-device-template"></a>Eszközsablon közzététele

Az eszköz képességeinek modelljét megvalósító eszköz csatlakoztatása előtt közzé kell tennie az eszköz sablonját.

Miután közzétett egy sablont, csak korlátozott módosításokat végezhet az eszköz képességeinek modelljében. Egy felület módosításához [létre kell hoznia és közzé kell tennie egy új verziót](./howto-version-device-template.md).

Az eszköz közzétételéhez nyissa meg az eszköz sablonját, és válassza a **Közzététel**lehetőséget.

Miután közzétett egy sablont, az operátor megkeresheti az **eszközök** lapot, és hozzáadhat akár valódi, akár szimulált eszközöket, amelyek az eszköz sablonját használják. A módosítások végrehajtása során továbbra is módosíthatja és mentheti az eszköz sablonját. Ha ezeket a módosításokat a kezelőben a **Devices (eszközök** ) lapon szeretné megtekinteni, ki kell választania a **közzétételi** időt.

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, javasolt következő lépésként olvassa el az [eszköz sablon verziószámozását](./howto-version-device-template.md)ismertető témakört.
