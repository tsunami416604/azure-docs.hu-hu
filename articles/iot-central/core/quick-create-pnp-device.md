---
title: Rövid útmutató – Szimulált eszköz hozzáadása az Azure IoT Centralhoz
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy eszközsablont, és hogyan adhat hozzá egy szimulált eszközt az IoT Central-alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 489bf81388c1bb889756d54957e791282054f0f7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77169582"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Rövid útmutató: Szimulált eszköz hozzáadása az IoT Central alkalmazáshoz

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Az eszközsablon az IoT Central-alkalmazáshoz csatlakozó eszköz képességeit határozza meg. A képességek közé tartozik az eszköz által küldött telemetriai adatok, az eszköz tulajdonságai és az eszköz által adott parancsok. Egy eszközsablonból egy szerkesztő vagy operátor valós és szimulált eszközöket is hozzáadhat egy alkalmazáshoz. A szimulált eszközök hasznosak az IoT Central-alkalmazás viselkedésének teszteléséhez, mielőtt valódi eszközöket csatlakoztatna.

Ebben a rövid útmutatóban hozzáad egy eszközsablont egy *MXChip IoT DevKit (DevKit)* táblához, és létrehoz egy szimulált eszközt. A rövid útmutató végrehajtásához nincs szükség valódi eszközre, az eszköz szimulációjával kell dolgoznia. DevKit-eszköz:

* Telemetriai adatokat, például hőmérsékletet küld.
* Eszközspecifikus tulajdonságokat, például fényerőt jelent.
* Válaszol az olyan parancsokra, mint a be- és kikapcsolás.
* Általános eszköztulajdonságokat jelent, például a belső vezérlőprogram verzióját és a sorozatszámát.

## <a name="prerequisites"></a>Előfeltételek

Végezze el az [Azure IoT Central alkalmazás](./quick-deploy-iot-central.md) létrehozása rövid útmutatót egy IoT Central alkalmazás létrehozásához az egyéni alkalmazás > egyéni **alkalmazássablon** használatával.

## <a name="create-a-template"></a>Sablon létrehozása

Szerkesztőként eszközsablonokat hozhat létre és szerkeszthet az IoT Central alkalmazásban. Az eszközsablon közzététele után szimulált eszközt hozhat létre, vagy valódi eszközöket csatlakoztathat az eszközsablonból. A szimulált eszközök lehetővé teszik az alkalmazás viselkedésének tesztelését, mielőtt valódi eszközt csatlakoztatna.

Ha új eszközsablont szeretne hozzáadni az alkalmazáshoz, válassza az **Eszközsablonok** lapot a bal oldali ablaktáblában.

![Eszközsablonok lap](./media/quick-create-pnp-device/devicedefinitions.png)

Az eszközsablon tartalmaz egy eszközképességi modellt, amely meghatározza az eszköz által küldött telemetriai adatokat, az eszköz tulajdonságait és az eszköz által válaszoló parancsokat.

### <a name="add-a-device-capability-model"></a>Eszközképességi modell hozzáadása

Számos lehetőség van egy eszközképességi modell hozzáadására az IoT Central alkalmazáshoz. Létrehozhat egy teljesen új modellt, importálhat egy modellt egy fájlból, vagy kiválaszthat egy eszközt az eszközkatalógusból. Az IoT Central is támogatja az *eszköz-első* megközelítés, ahol automatikusan importálja a modellt egy tárházból, amikor egy eszköz először csatlakozik. Ebben a rövid útmutatóban az eszközkatalógusból kiválaszt egy eszközt az eszközképességi modell importálásához.

A következő lépések bemutatják, hogyan használhatja az eszközkatalógust egy **MXChip IoT DevKit-eszköz** képességmodelljének importálásához. Ezek az eszközök telemetriai adatokat, például hőmérsékletet küldenek az alkalmazásnak:

1. Új eszközsablon hozzáadásához **+** válassza az **Eszközsablonok** lapon.

1. A **Sablon típusának kiválasztása** lapon görgessen lefelé, amíg meg nem találja az **MXChip IoT DevKit** csempét.

1. Válassza az **MXChip IoT DevKit** csempét, majd a **Tovább: Testreszabás**lehetőséget.

1. A **Véleményezés** lapon válassza a **Létrehozás gombot.**

1. Néhány másodperc múlva láthatja az új eszközsablont:

    ![MXChip IoT DevKit eszközsablon](./media/quick-create-pnp-device/devkit-template.png)

    Az MXChip IoT DevKit képességmodell olyan felületeket tartalmaz, mint **a mxchip_sensor,** **a mxchip_settings**és **az Eszközadatok**. Az Interfészek meghatározzák az MXChip IoT DevKit-eszközök képességeit. Képességek közé tartozik az eszköz által küldött telemetriai adatok, az eszköz által küldött tulajdonságok, és a parancsokat az eszköz válaszol.

### <a name="add-cloud-properties"></a>Felhőtulajdonságok hozzáadása

Az eszközsablon felhőalapú tulajdonságokat is tartalmazhat. A felhőtulajdonságok csak az IoT Central alkalmazásban léteznek, és soha nem küldik el vagy fogadják az eszközről.

1. Válassza a **Felhőtulajdonságok,** majd **a + Felhőhozzáadása tulajdonság lehetőséget.** Az alábbi táblázatban található információk segítségével két felhőtulajdonságot adhat hozzá az eszközsablonhoz:

    | Megjelenítendő név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | None          | Dátum   |
    | Vevő neve     | None          | Sztring |

1. A módosítások mentéséhez válassza a **Mentés** lehetőséget:

    ![Felhőtulajdonságai](media/quick-create-pnp-device/cloudproperties.png)

## <a name="views"></a>Nézetek

Szerkesztőként testreszabhatja az alkalmazást, hogy az eszközre vonatkozó releváns információkat jelenítse meg egy operátornak. A testreszabások lehetővé teszik, hogy az üzemeltető kezelje az alkalmazáshoz csatlakoztatott eszközöket. Kétféle nézetet hozhat létre az operátorok számára az eszközökkel való kommunikációhoz:

* Űrlapok az eszköz- és felhőtulajdonságok megtekintéséhez és szerkesztéséhez.
* Irányítópultok az eszközök megjelenítéséhez, beleértve az általuk küldött telemetriai adatokat is.

### <a name="default-views"></a>Alapértelmezett nézetek

Az alapértelmezett nézetek segítségével gyorsan megjelenítheti fontos eszközinformációit. Legfeljebb három alapértelmezett nézet et hozhat létre az eszközsablonhoz:

* A **Parancsok** nézet lehetővé teszi, hogy a kezelő parancsokat küldjön az eszközre.
* Az **Áttekintő** nézet diagramok és metrikák segítségével jeleníti meg az eszköz telemetriai adatai.
* A **Bemutat** nézet megjeleníti az eszköz tulajdonságait.

Jelölje ki a **Nézetek** csomópontot az eszközsablonban. Láthatja, hogy az IoT Central **áttekintést** és **egy Betekintő** nézetet hozott létre a sablon hozzáadásakor.

Új **Eszközkezelő** űrlap hozzáadása, amelyet az operátor az eszköz kezeléséhez használhat:

1. Jelölje ki a **Nézetek** csomópontot, majd az új nézet hozzáadásához válassza a Szerkesztő eszköz és a **felhőalapú adatok** csempét.

1. Módosítsa az űrlap nevét **Az eszköz kezelése**névre.

1. Válassza ki az **Ügyfél nevét** és a végső **szolgáltatás dátuma** felhőtulajdonságokat, valamint a **Fan Speed** tulajdonságot. Ezután válassza **a Hozzáadás szakaszt:**

    ![Új űrlap létrehozása](media/quick-create-pnp-device/new-form.png)

1. Az új űrlap mentéséhez válassza a **Mentés** gombot.

## <a name="publish-device-template"></a>Eszközsablon közzététele

Mielőtt létrehozhatna egy szimulált eszközt, vagy valódi eszközt csatlakoztathatna, közzé kell tennie az eszközsablont. Bár az IoT Central közzétette a sablont, amikor először létrehozta, közzé kell tennie a frissített verziót.

Eszközsablon közzététele:

1. Nyissa meg az eszközsablont az **Eszközsablonok** lapon.

1. Válassza **a Közzététel**lehetőséget:

    ![Közzétett modell](media/quick-create-pnp-device/publishedmodel.png)

1. Az **Eszközsablon közzététele az alkalmazásban** párbeszédpanelen válassza a **Közzététel**lehetőséget. 

Az eszközsablon közzététele után az eszközök **lapon** látható. Egy közzétett eszközsablonban nem szerkesztheti az eszközképességi modellt új verzió létrehozása nélkül. A felhőbeli tulajdonságokat, a testreszabásokat és a nézeteket azonban verziószámozás nélkül is módosíthatja egy közzétett eszközsablonban. A módosítások elvégzése után válassza **a Közzététel** lehetőséget, ha a módosításokat ki szeretné adni a szolgáltatónak.

## <a name="add-a-simulated-device"></a>Szimulált eszköz hozzáadása

Ha egy szimulált eszközt szeretne hozzáadni az alkalmazáshoz, használja a létrehozott **MXChip IoT DevKit** eszközsablont.

1. Ha új eszközt szeretne operátorként hozzáadni, válassza az **Eszközök lehetőséget** a bal oldali ablaktáblában. Az **Eszközök** lapon látható **az összes eszköz** és az **MXChip IoT DevKit** eszközsablon. Válassza az **MXChip IoT DevKit lehetőséget.**

1. Szimulált DevKit-eszköz hozzáadásához **+** válassza a lehetőséget. Használja a javasolt **eszközazonosítót,** vagy adja meg saját **kis-eszközazonosítóját.** Megadhatja az új eszköz nevét is. Győződjön meg arról, hogy a **Szimulált** kapcsoló be van **kapcsolva,** majd válassza a **Létrehozás lehetőséget.**

    ![Szimulált eszköz](./media/quick-create-pnp-device/simulated-device.png)

Mostantól a szerkesztő által az eszközsablonhoz létrehozott nézeteket használhatja szimulált adatok használatával:

1. Válassza ki a szimulált eszközt az **Eszközök** lapon.

1. Az **Áttekintő** nézet a szimulált telemetriai adatok nyomtatását jeleníti meg:

    ![Áttekintő nézet](./media/quick-create-pnp-device/simulated-telemetry.png)

1. A **Bemutatkozás** nézet a tulajdonságértékeket jeleníti meg, beleértve a nézethez hozzáadott felhőtulajdonságokat is.

1. A **Parancsok** nézetben parancsokat futtathat, például **villoghat** az eszközön.

1. Az **Eszközök kezelése** nézet az az űrlap, amelyet az üzemeltető számára hozott létre az eszköz kezeléséhez.

## <a name="use-a-simulated-device-to-improve-views"></a>Nézetek javítása szimulált eszközzel

Miután létrehozott egy új szimulált eszközt, a szerkesztő használhatja ezt az eszközt, hogy tovább fejlessze és építsen az eszközsablon nézeteire.

1. Válassza **az Eszközsablonok lehetőséget** a bal oldali ablaktáblában, és válassza az **MXChip IoT DevKit** sablont.

1. Jelölje ki a szerkesztendő nézetek bármelyikét, vagy hozzon létre új nézetet. Válassza **az Előnézeti eszköz konfigurálása**lehetőséget, majd **válassza ki a futó eszközről**lehetőséget. Itt választhatja ki, hogy nincs-e előnézeti eszköz, egy tesztelésre konfigurált valódi eszköz vagy egy meglévő eszköz, amelyet az IoT Centralba adott hozzá.

1. Válassza ki a szimulált eszközt a listában. Ezután válassza az **Alkalmaz** lehetőséget. Most már láthatja ugyanazt a szimulált eszközt az eszközsablon-nézetek létrehozásában. Ez a nézet diagramok és egyéb vizualizációk esetén hasznos.

    ![Preview-eszköz konfigurálása](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy **MXChip IoT DevKit** eszközsablont, és hogyan adhat hozzá egy szimulált eszközt az alkalmazáshoz.

Ha többet szeretne megtudni az alkalmazáshoz csatlakoztatott eszközök figyeléséről, folytassa a rövid útmutatóval:

> [!div class="nextstepaction"]
> [Szabályok és műveletek konfigurálása](./quick-configure-rules.md)
