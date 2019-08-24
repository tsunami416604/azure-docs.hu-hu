---
title: Új eszköztípus definiálása az Azure IoT Centralban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre új típusú eszközt az Azure IoT Central alkalmazásban. Megadhatja a típus telemetria, állapotát, tulajdonságait és parancsait.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 877769041d1587c6c1b0f4dbaff51d5a1a7924b5
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997710"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-preview-features"></a>Oktatóanyag: Új eszköz típusának definiálása az Azure IoT Central alkalmazásban (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan definiálhat új eszköztípust egy eszközsablon használatával a Microsoft Azure IoT Central alkalmazásban. Az eszköz-sablonok határozzák meg az eszköz képességeit. A képességek közé tartozik az eszköz telemetria, az eszköz tulajdonságai, valamint az eszköz által válaszoló parancsok.

Ebben az oktatóanyagban egy **környezeti Sensor** -eszköz sablonját hozza létre. Környezeti érzékelő eszköz:

* Telemetria, például hőmérsékletet küld.
* Az eszközre jellemző tulajdonságokat, például a fényerő szintjét jelenti.
* Válaszol olyan parancsokra, mint a bekapcsolás és a kikapcsolás.
* Az általános eszköz tulajdonságait, például a belső vezérlőprogram verzióját és a sorozatszámot jelenti.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy új sablont.
> * Eszköz-képesség modell importálása.
> * Felhő tulajdonságainak létrehozása
> * Adjon meg egy vizualizációt az eszköz telemetria.
> * Tegye közzé az eszköz sablonját.
> * Hozzon létre egy szimulált eszközt az eszköz sablonja számára.
> * Megtekintheti a szimulált eszközt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure IoT Central-alkalmazásra. Ha elvégezte az [Azure IoT Central-alkalmazás létrehozása](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) rövid útmutatót, újból felhasználhatja az abban létrehozott alkalmazást. Egyébként hajtsa végre az alábbi lépéseket egy üres Azure IoT Central-alkalmazás létrehozásához:

1. Lépjen az Azure IoT Central [Alkalmazáskezelő](https://aka.ms/iotcentral) oldalára.

1. Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal, ellenkező esetben írja alá azt Microsoft-fiók használatával:

    ![Lépjen a céges fiókjába](./media/tutorial-define-device-type-pnp/sign-in.png)

1. Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget.

Előzetes funkciókat használó új Azure IoT Central-alkalmazás létrehozása, beleértve a IoT Plug and Play:

1. Válassza a **Próba** elemet. Próbaverziós alkalmazás létrehozásához nincs szükség Azure-előfizetésre.

    A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Válassza az **előnézet alkalmazás**lehetőséget.

1. Választhat egy rövid alkalmazásnevet is, például a **Contoso-légkondicionálók** nevet. Az Azure IoT Central létrehoz egy egyéni URL-előtagot. Ezt az URL-előtagot egy könnyebben megjegyezhető előtagra módosíthatja.

1. Ha próbaverziós alkalmazást hoz létre, meg kell adnia a kapcsolattartási adatait.

1. Kattintson a **Létrehozás** gombra.

    ![Azure IoT Central Alkalmazás létrehozása oldal](./media/tutorial-define-device-type-pnp/iotcentralcreate.png)

    További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Szükség van a **EnvironmentalSensorInline. capabilitymodel. JSON** fájl helyi példányára is, amely tartalmazza a [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) eszköz képességének modelljét. [Innen](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json)töltheti le. Kattintson a jobb gombbal az oldalra, és válassza a **Mentés másként**lehetőséget.

Miután letöltötte a fájlt, nyissa meg egy szövegszerkesztőben, és cserélje le a két példányát `<YOUR_COMPANY_NAME_HERE>` a névre. Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja.

## <a name="create-a-template"></a>Sablon létrehozása

A Builder használatával az alkalmazásban létrehozhat és szerkesztheti az eszközök sablonjait. Miután közzétett egy sablont, létrehozhat egy szimulált eszközt, vagy kapcsolódhat a valódi eszközökhöz, amelyek implementálják az eszköz sablonját. A szimulált eszközök lehetővé teszik az alkalmazás működésének tesztelését a valódi eszköz csatlakoztatása előtt.

Új sablon az alkalmazáshoz való hozzáadásához nyissa meg az **eszközök sablonjai** lapot. Ehhez válassza a bal oldali navigációs menü **eszközök sablonok** lapját.

![Eszközök sablonjai lap](./media/tutorial-define-device-type-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Eszköz-képesség modell hozzáadása

Több lehetőség is rendelkezésre áll az eszköz képességi modell létrehozásához IoT Centralban. Dönthet úgy, hogy létrehoz egy teljesen új egyéni modellt, importál egy fájlból, kiválasztja az eszközt a katalógusból, vagy csatlakoztat egy IoT Plug and Play eszközt egy eszközről az első olyan kapcsolaton keresztül, ahol az eszköz képességi modell közzé lett téve a nyilvános tárházban. Ebben az oktatóanyagban egy eszköz-képesség modellt importál egy fájlból.

A következő lépések bemutatják, hogyan importálhatja a képesség modelljét egy **környezeti érzékelő** eszközre. Ezek az eszközök telemetria, például hőmérsékletet küldenek az alkalmazásnak:

1. Új sablon hozzáadásához válassza az **+ új** lehetőséget az **eszközök sablonjai** lapon.

1. Válassza az **Egyéni** lehetőséget a sablonok listájában.

1. Adja meg a **környezeti érzékelőt** az eszköz sablonjának neveként.

1. Válassza az **importálási képesség modell** lehetőséget, hogy új eszköz-képesség modellt hozzon létre egy JSON-fájlból. Navigáljon ahhoz a mappához, ahová a **EnvironmentalSensorInline. capabilitymodel. JSON** fájlt mentette a helyi gépen. Válassza ki a **EnvironmentalSensorInline. capabilitymodel. JSON** fájlt, majd kattintson a **Megnyitás**gombra. A környezeti érzékelő képesség modellje tartalmazza a **környezeti érzékelőt** és az **eszköz információs** felületét:

    ![Környezeti érzékelő eszköz képességének modellje](./media/tutorial-define-device-type-pnp/newdevicecapabilitymodel.png)

    Ezek a felületek határozzák meg egy **környezeti érzékelő** eszköz képességeit. A képességek közé tartozik az eszköz által küldött telemetria, az eszköz tulajdonságainak jelentése, valamint az eszköz által válaszoló parancsok.

### <a name="add-cloud-properties"></a>Felhő tulajdonságainak hozzáadása

Az eszközök tartalmazhatnak Felhőbeli tulajdonságokat is. A felhő tulajdonságai csak a IoT Central alkalmazásban érhetők el, és a rendszer soha nem továbbítja, vagy nem fogadja az eszközt.

1. Válassza a **felhő tulajdonságai** lehetőséget, majd a **+ felhő tulajdonságot**. A következő táblázatban található információk használatával hozzáadhat egy Felhőbeli tulajdonságot az eszköz sablonhoz.

    | Megjelenítendő név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | Nincsenek          | Date   |
    | Ügyfél neve     | Nincsenek          | Sztring |

1. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra:

    ![Felhő tulajdonságai](media/tutorial-define-device-type-pnp/cloudproperties.png)

### <a name="add-customizations"></a>Testreszabások hozzáadása

Ha módosítania kell egy felületet, vagy IoT Central-specifikus funkciókat kell hozzáadnia egy olyan funkcióhoz, amely nem igényli az eszköz képességeinek megadását, használja a testreszabásokat. A mezőket testreszabhatja, ha a képesség modell Piszkozat vagy közzétett állapotban van. Csak olyan mezőket szabhat testre, amelyek nem bontják le az illesztőfelületek kompatibilitását. Megteheti például a következőt:

- Testreszabhatja egy képesség megjelenített nevét és egységeit.
- Adja meg az alapértelmezett színt, amelyet akkor kell használni, ha az érték megjelenik a diagramon.
- Egy tulajdonság kezdeti, minimális és maximális értékének megadása.

Nem szabhatja testre a képesség nevét vagy a képesség típusát.


## <a name="create-views"></a>Nézetek létrehozása

Építőként testreszabhatja az alkalmazást, hogy a megfelelő információkat jelenítse meg a környezeti érzékelő eszközéről egy operátorra. A testreszabások lehetővé teszik, hogy az operátor felügyelje az alkalmazáshoz kapcsolódó környezeti érzékelők eszközeit. Kétféle nézetet hozhat létre az operátorok számára az eszközök használatához:

* Az eszközök és a felhő tulajdonságainak megtekintésére és szerkesztésére szolgáló űrlapok.
* Irányítópultok az eszközök megjelenítéséhez.

### <a name="generate-default-views"></a>Alapértelmezett nézetek előállítása

Az alapértelmezett nézetek létrehozásával gyorsan megkezdheti a fontos eszköz adatainak megjelenítését. Az eszköz sablonjaihoz legfeljebb három alapértelmezett nézet hozható létre:

* A **parancsok** nézet lehetővé teszi, hogy az operátor parancsokat küldjön az eszközre.
* Az **Áttekintés** nézet diagramokat és mérőszámokat használ az eszközök telemetria megjelenítéséhez.
* A **Névjegy** nézet megjeleníti az eszköz tulajdonságait.

Miután kiválasztotta az **alapértelmezett nézetek létrehozása**lehetőséget, a rendszer automatikusan hozzáadja őket az eszköz-sablon **nézetek** szakaszában.

### <a name="configure-a-view-to-visualize-devices"></a>Nézet konfigurálása eszközök megjelenítéséhez

Az eszközök irányítópultja lehetővé teszi, hogy az operátor diagramok és metrikák használatával megjelenítse az eszközt. Szerkesztőként meghatározhatja, milyen információk jelenjenek meg egy eszköz irányítópultján. Több irányítópultot is megadhat az eszközökhöz. Ha létre szeretne hozni egy irányítópultot a környezeti érzékelők telemetria megjelenítéséhez, válassza a **nézetek** lehetőséget, majd jelenítse meg **az eszközt**:

1. A **Tulajdonságok**területen megjelenik az összes eszköz tulajdonságai, a felhő tulajdonságai, a telemetria és a statikus beállítások. Ezen elemek bármelyikét áthúzhatja a nézetbe. Húzza a **fényerő szintje** tulajdonságot a nézetre. A csempét a fogaskerék ikon használatával konfigurálhatja.

1. Telemetria ábrázoló diagram hozzáadásához válassza a **páratartalom** és **hőmérséklet**lehetőséget, majd kattintson az **összekapcsolás**elemre. Ha más formátumban szeretné megtekinteni a diagramot, például egy tortadiagram vagy oszlopdiagram, akkor a csempe tetején kattintson a **vizualizáció módosítása** gombra.

1. A nézet mentéséhez válassza a **Mentés** lehetőséget:

További csempéket is hozzáadhat, amelyek további tulajdonságokat vagy telemetria értékeket jelenítenek meg. Hozzáadhat statikus szöveget, hivatkozásokat és képeket is. Az irányítópulton lévő csempe áthelyezéséhez vagy átméretezéséhez vigye az egérmutatót a csempére, és húzza a csempét egy új helyre, vagy méretezze át.

### <a name="add-a-device-form"></a>Eszköz űrlapjának hozzáadása

Az eszközök űrlapja lehetővé teszi, hogy az operátor az írható eszköz tulajdonságait és a felhő tulajdonságait szerkessze. Építőként több űrlapot is meghatározhat, és kiválaszthatja, hogy mely eszközök és felhő-tulajdonságok jelenjenek meg az egyes űrlapokon. Az űrlap írásvédett eszközének tulajdonságait is megjelenítheti.

Űrlap létrehozása a környezeti érzékelő tulajdonságainak megtekintéséhez és szerkesztéséhez:

1. Navigáljon a nézetekhez a **környezeti érzékelő** sablonban. Új nézet hozzáadásához válassza az **eszköz és** a Felhőbeli adatcsempe szerkesztése lehetőséget.

1. Adja meg az űrlap neve **környezeti érzékelő tulajdonságait**.

1. Húzza az **ügyfél neve** és az **utolsó szolgáltatás dátumának** Felhőbeli tulajdonságai elemet az űrlap meglévő szakaszára.

1. Válassza ki a **fényerő szintjét** és az **eszköz állapotára** szolgáló eszköz tulajdonságait. Ezután válassza a **Hozzáadás szakaszt**. Szerkessze a szakasz címét, hogy az **érzékelő tulajdonságai**legyenek. Kattintson az **Alkalmaz** gombra.

1. Válassza ki az **eszköz modelljét**, a **szoftver verzióját**, a **gyártót**és a **processzor gyártójának** eszközének tulajdonságait. Ezután válassza a **Hozzáadás szakaszt**. Szerkessze a szakasz címét, hogy az **eszköz tulajdonságai**legyenek. Kattintson az **Alkalmaz** gombra.

1. A nézet mentéséhez válassza a **Mentés** lehetőséget.

## <a name="publish-device-template"></a>Eszköz sablonjának közzététele

Szimulált környezeti érzékelő létrehozása vagy valódi környezeti érzékelő csatlakoztatása előtt közzé kell tennie az eszköz sablonját.

Eszköz sablonjának közzététele:

1. Nyissa meg az eszköz sablonját az **eszközök sablonjai** lapon.

1. Kattintson a **Publish** (Közzététel) elemre.

1. Az **eszközbeállítások közzététele** párbeszédpanelen válassza a **Közzététel**lehetőséget:

    ![Közzétett modell](media/tutorial-define-device-type-pnp/publishedmodel.png)

Egy sablon közzététele után az eszköz megjelenik az **eszközök** lapon és a kezelőben. Egy közzétett eszköz sablonjában nem szerkesztheti az eszköz képességeinek modelljét új verzió létrehozása nélkül. A közzétett eszközön azonban verziószámozás nélkül is frissítheti a felhő tulajdonságait, testreszabásait és nézeteit. A módosítások elvégzése után válassza a **Közzététel** lehetőséget a módosítások elküldéséhez az operátornak.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Új eszközsablon létrehozása
* Eszköz-képesség modell importálása.
* Felhő tulajdonságainak létrehozása
* Testreszabások létrehozása.
* Adjon meg egy vizualizációt az eszköz telemetria.
* Tegye közzé az eszköz sablonját.

Most, hogy létrehozott egy eszközt az Azure IoT Central-alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Eszköz hozzáadása](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
