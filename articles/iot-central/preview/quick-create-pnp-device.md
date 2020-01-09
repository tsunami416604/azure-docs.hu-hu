---
title: Rövid útmutató – szimulált eszköz hozzáadása az Azure IoT Central
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy eszköz-sablont, és hogyan adhat hozzá szimulált eszközt a IoT Central alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 669bdcddc7e4bb0c63250d9b571ff7ef4343ef5c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434829"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application-preview-features"></a>Gyors útmutató: szimulált eszköz hozzáadása a IoT Central alkalmazáshoz (előzetes verziójú funkciók)

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Az eszköz sablonja meghatározza a IoT Central-alkalmazáshoz csatlakozó eszközök képességeit. A képességek közé tartozik az eszköz telemetria, az eszköz tulajdonságai, valamint az eszköz által válaszoló parancsok. Egy eszköz sablonjában egy építő vagy egy operátor valós és szimulált eszközöket is hozzáadhat egy alkalmazáshoz. A szimulált eszközök hasznosak lehetnek a IoT Central alkalmazás működésének teszteléséhez a valódi eszközök csatlakoztatása előtt.

Ebben a rövid útmutatóban egy **környezeti szenzor** -eszköz sablonját hozza létre, és egy szimulált eszközt ad hozzá. Környezeti érzékelő eszköz:

* Telemetria, például hőmérsékletet küld.
* Az eszközre jellemző tulajdonságokat, például a fényerő szintjét jelenti.
* Válaszol olyan parancsokra, mint a bekapcsolás és a kikapcsolás.
* Az általános eszköz tulajdonságait, például a belső vezérlőprogram verzióját és a sorozatszámot jelenti.

## <a name="prerequisites"></a>Előfeltételek

Fejezze be az [Azure IoT Central-alkalmazás létrehozása (előzetes verziójú funkciók)](./quick-deploy-iot-central.md) című rövid útmutatót egy IoT Central alkalmazás létrehozásához az **egyéni alkalmazás > előzetes verziójú alkalmazás** sablonjának használatával.

Szükség van a **EnvironmentalSensorInline. capabilitymodel. JSON** fájl helyi példányára is, amely tartalmazza a [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) eszköz képességének modelljét. [Innen](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json)töltheti le. Kattintson a jobb gombbal az oldalra, és válassza a **Mentés másként**lehetőséget.

A fájl letöltése után nyissa meg egy szövegszerkesztőben, és cserélje le a `<YOUR_COMPANY_NAME_HERE>` két példányát a nevére. Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja.

## <a name="create-a-template"></a>Sablon létrehozása

A Builder használatával az alkalmazásban létrehozhat és szerkesztheti az eszközök sablonjait. Miután közzétett egy sablont, létrehozhat egy szimulált eszközt, vagy kapcsolódhat a valódi eszközökhöz, amelyek implementálják az eszköz sablonját. A szimulált eszközök lehetővé teszik az alkalmazás működésének tesztelését a valódi eszköz csatlakoztatása előtt.

Új sablon az alkalmazáshoz való hozzáadásához nyissa meg az **eszközök sablonjai** lapot. Ehhez válassza a bal oldali ablaktábla **eszközök sablonok** lapját.

![Eszközök sablonjai lap](./media/quick-create-pnp-device/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Eszköz-képesség modell hozzáadása

Több lehetőség is rendelkezésre áll az eszköz képességi modell létrehozásához IoT Centralban. Dönthet úgy, hogy létrehoz egy teljesen új egyéni modellt, importál egy fájlból, kiválasztja az eszközt a katalógusból, vagy csatlakoztat egy IoT Plug and Play eszközt egy eszközről az első olyan kapcsolaton keresztül, ahol az eszköz képességi modell közzé lett téve a nyilvános tárházban. Ebben az oktatóanyagban egy eszköz-képesség modellt importál egy fájlból.

A következő lépések bemutatják, hogyan importálhatja a képesség modelljét egy **környezeti érzékelő** eszközre. Ezek az eszközök telemetria, például hőmérsékletet küldenek az alkalmazásnak:

1. Új sablon hozzáadásához válassza a **+** lehetőséget az **eszközök sablonjai** lapon.

1. Válassza ki a **IoT eszközt** az egyéni eszközök listájában, majd válassza a **Tovább: testreszabás**, majd a **Tovább: felülvizsgálat**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

1. Adja meg a **környezeti érzékelőt** az eszköz sablonjának neveként.

1. Válassza az **importálási képesség modell** lehetőséget, hogy új eszköz-képesség modellt hozzon létre egy JSON-fájlból. Navigáljon ahhoz a mappához, ahová a **EnvironmentalSensorInline. capabilitymodel. JSON** fájlt mentette a helyi gépen. Válassza ki a **EnvironmentalSensorInline. capabilitymodel. JSON** fájlt, majd kattintson a **Megnyitás**gombra. A környezeti érzékelő képesség modellje tartalmazza a **környezeti érzékelőt** és az **eszköz információs** felületét:

    ![Környezeti érzékelő eszköz képességének modellje](./media/quick-create-pnp-device/newdevicecapabilitymodel.png)

    Ezek a felületek határozzák meg egy **környezeti érzékelő** eszköz képességeit. A képességek közé tartozik az eszköz által küldött telemetria, az eszköz tulajdonságainak jelentése, valamint az eszköz által válaszoló parancsok.

### <a name="add-cloud-properties"></a>Felhő tulajdonságainak hozzáadása

Az eszközök tartalmazhatnak Felhőbeli tulajdonságokat is. A felhő tulajdonságai csak a IoT Central alkalmazásban érhetők el, és a rendszer soha nem továbbítja, vagy nem fogadja az eszközt.

1. Válassza a **felhő tulajdonságai** lehetőséget, majd a **+ felhő tulajdonságot**. A következő táblázatban található információk használatával hozzáadhat egy Felhőbeli tulajdonságot az eszköz sablonhoz.

    | Megjelenítendő név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | None          | Dátum   |
    | Ügyfél neve     | None          | Sztring |

1. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra:

    ![Felhő tulajdonságai](media/quick-create-pnp-device/cloudproperties.png)

## <a name="create-views"></a>Nézetek létrehozása

Építőként testreszabhatja az alkalmazást, hogy a megfelelő információkat jelenítse meg a környezeti érzékelő eszközéről egy operátorra. A testreszabások lehetővé teszik, hogy az operátor felügyelje az alkalmazáshoz kapcsolódó környezeti érzékelők eszközeit. Kétféle nézetet hozhat létre az operátorok számára az eszközök használatához:

* Az eszközök és a felhő tulajdonságainak megtekintésére és szerkesztésére szolgáló űrlapok.
* Irányítópultok az eszközök megjelenítéséhez.

### <a name="generate-default-views"></a>Alapértelmezett nézetek előállítása

Az alapértelmezett nézetek létrehozásával gyorsan megkezdheti a fontos eszköz adatainak megjelenítését. Az eszköz sablonjaihoz legfeljebb három alapértelmezett nézet hozható létre:

* A **parancsok** nézet lehetővé teszi, hogy az operátor parancsokat küldjön az eszközre.
* Az **Áttekintés** nézet diagramokat és mérőszámokat használ az eszközök telemetria megjelenítéséhez.
* A **Névjegy** nézet megjeleníti az eszköz tulajdonságait.

Válassza a **nézetek** lehetőséget, majd az **alapértelmezett nézetek létrehozását**.

### <a name="configure-a-view-to-visualize-devices"></a>Nézet konfigurálása eszközök megjelenítéséhez

Az eszközök irányítópultja lehetővé teszi, hogy az operátor diagramok és metrikák használatával megjelenítse az eszközt. Szerkesztőként meghatározhatja, milyen információk jelenjenek meg egy eszköz irányítópultján. Több irányítópultot is megadhat az eszközökhöz. Ha létre szeretne hozni egy irányítópultot a környezeti érzékelők telemetria megjelenítéséhez, válassza a **nézetek** lehetőséget, majd **jelenítse meg az eszközt**:

1. A **Tulajdonságok**területen megjelenik az összes eszköz tulajdonságai, a felhő tulajdonságai, a telemetria és a statikus beállítások. Ezen elemek bármelyikét áthúzhatja a nézetbe. Húzza a **fényerő szintje** tulajdonságot a nézetre. A csempét a fogaskerék ikon használatával konfigurálhatja.

1. Telemetria ábrázoló diagram hozzáadásához válassza a **páratartalom** és **hőmérséklet**lehetőséget, majd kattintson az **összekapcsolás**elemre. Ha más formátumban szeretné megtekinteni a diagramot, például egy tortadiagram vagy oszlopdiagram, akkor a csempe tetején kattintson a **vizualizáció módosítása** gombra.

1. A nézet mentéséhez válassza a **Mentés** lehetőséget:

További csempéket is hozzáadhat, amelyek további tulajdonságokat vagy telemetria értékeket jelenítenek meg. Hozzáadhat statikus szöveget, hivatkozásokat és képeket is. Az irányítópulton lévő csempe áthelyezéséhez vagy átméretezéséhez vigye az egérmutatót a csempére, és húzza a csempét egy új helyre, vagy méretezze át.

### <a name="add-a-device-form"></a>Eszköz űrlapjának hozzáadása

Az eszközök űrlapja lehetővé teszi, hogy az operátor az írható eszköz tulajdonságait és a felhő tulajdonságait szerkessze. Építőként több űrlapot is meghatározhat, és kiválaszthatja, hogy mely eszközök és felhő-tulajdonságok jelenjenek meg az egyes űrlapokon. Az űrlap írásvédett eszközének tulajdonságait is megjelenítheti.

Űrlap létrehozása a környezeti érzékelő tulajdonságainak megtekintéséhez és szerkesztéséhez:

1. Navigáljon a **nézetekhez** a **környezeti érzékelő** sablonban. Új nézet hozzáadásához válassza az **eszköz és a Felhőbeli** adatcsempe szerkesztése lehetőséget.

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

    ![Közzétett modell](media/quick-create-pnp-device/publishedmodel.png)

Egy sablon közzététele után az eszköz megjelenik az **eszközök** lapon és a kezelőben. Egy közzétett eszköz sablonjában nem szerkesztheti az eszköz képességeinek modelljét új verzió létrehozása nélkül. A közzétett eszközön azonban verziószámozás nélkül is frissítheti a felhő tulajdonságait, testreszabásait és nézeteit. A módosítások elvégzése után válassza a **Közzététel** lehetőséget a módosítások elküldéséhez az operátornak.

## <a name="add-a-simulated-device"></a>Szimulált eszköz hozzáadása

Szimulált eszköznek az alkalmazáshoz való hozzáadásához használja a létrehozott **környezeti érzékelő** eszköz sablonját.

1. Új eszköz hozzáadása operátorként a bal oldali ablaktáblán válassza az **eszközök** lehetőséget. Az **eszközök** lapon az **összes eszköz** és a **környezeti érzékelő** eszköz sablonja látható. Válassza a **környezeti érzékelő**elemet.

1. Szimulált környezeti érzékelő eszköz hozzáadásához válassza az **+ új**lehetőséget. Használja a javasolt **eszköz azonosítóját** , vagy adja meg a saját kisbetűs **eszközének azonosítóját**. Megadhatja az új eszköz nevét is. Váltson a **szimulált** **váltógomb értékre, majd** válassza a **Létrehozás**lehetőséget.

    ![Szimulált eszköz](./media/quick-create-pnp-device/simulated-device.png)

Mostantól a Builder által az eszköz sablonja által létrehozott nézeteket szimulált adatként is használhatja.

## <a name="use-a-simulated-device-to-improve-views"></a>A nézetek fejlesztése szimulált eszköz használatával

Miután létrehozta az új szimulált eszközt, a szerkesztő ezzel az eszközzel folytathatja a fejlesztést és az eszköz sablonra épülő nézeteit.

1. A bal oldali ablaktáblán válassza ki az **eszközök sablonokat** , majd válassza ki a **környezeti érzékelő** sablont.

1. Válassza ki a szerkeszteni kívánt nézeteket, vagy hozzon létre egy új nézetet. Kattintson az **előnézeti eszköz konfigurálása**lehetőségre, majd **válasszon egy futó eszközről**. Itt választhat, hogy nincs-e előnézeti eszköz a teszteléshez konfigurálható valódi eszköz vagy a IoT Centralba felvett meglévő eszköz között.

1. Válassza ki a szimulált eszközt a listában. Ezután válassza az **Alkalmaz** lehetőséget. Most már megtekintheti ugyanazt a szimulált eszközt az eszköz sablon nézetének létrehozási felületén. Ez a nézet diagramok és egyéb vizualizációk esetén hasznos.

    ![Előnézet eszköz konfigurálása](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy **környezeti érzékelőt** tartalmazó eszközt, és hogyan adhat hozzá szimulált eszközt az alkalmazáshoz.

Ha többet szeretne megtudni az alkalmazáshoz csatlakoztatott eszközök monitorozásáról, folytassa a gyors üzembe helyezéssel:

> [!div class="nextstepaction"]
> [Szabályok és műveletek konfigurálása](./quick-configure-rules.md)
