---
title: Rövid útmutató – szimulált eszköz hozzáadása az Azure IoT Central
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy eszköz-sablont, és hogyan adhat hozzá szimulált eszközt a IoT Central alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 25e1742612c6fc8c326f2918a4d69c55a9888c97
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81000440"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Gyors útmutató: szimulált eszköz hozzáadása a IoT Central alkalmazáshoz

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Az eszköz sablonja meghatározza a IoT Central-alkalmazáshoz csatlakozó eszközök képességeit. A képességek közé tartozik az eszköz telemetria, az eszköz tulajdonságai, valamint az eszköz által válaszoló parancsok. Egy eszköz sablonjában egy építő vagy egy operátor valós és szimulált eszközöket is hozzáadhat egy alkalmazáshoz. A szimulált eszközök hasznosak lehetnek a IoT Central alkalmazás működésének teszteléséhez a valódi eszközök csatlakoztatása előtt.

Ebben a rövid útmutatóban hozzá kell adnia egy *MXChip IoT fejlesztői készlet* (fejlesztői készlet) táblához, és létre kell hoznia egy szimulált eszközt. A rövid útmutató elvégzéséhez nincs szükség valódi eszközre, és az eszköz szimulációja is működik. Egy fejlesztői készlet-eszköz:

* Telemetria, például hőmérsékletet küld.
* Az eszközre jellemző tulajdonságokat, például a fényerő szintjét jelenti.
* Válaszol olyan parancsokra, mint a bekapcsolás és a kikapcsolás.
* Az általános eszköz tulajdonságait, például a belső vezérlőprogram verzióját és a sorozatszámot jelenti.

## <a name="prerequisites"></a>Előfeltételek

Fejezze be az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) című rövid útmutatót egy IoT Central-alkalmazás létrehozásához az **egyéni alkalmazás > egyéni alkalmazásspecifikus** sablon használatával.

## <a name="create-a-template"></a>Sablon létrehozása

Építőként az IoT Central alkalmazásban létrehozhat és szerkesztheti az eszközök sablonjait. Miután közzétett egy sablont, szimulált eszközt hozhat, vagy a valódi eszközöket összekapcsolhatja az eszköz sablonjában. A szimulált eszközök lehetővé teszik az alkalmazás működésének tesztelését a valódi eszköz csatlakoztatása előtt.

Ha új sablont szeretne felvenni az alkalmazásba, válassza a bal oldali ablaktábla **eszközök sablonok** lapját.

![Eszközök sablonjai lap](./media/quick-create-simulated-device/device-definitions.png)

Az eszköz-sablon tartalmaz egy eszköz-képességi modellt, amely meghatározza az eszköz által küldött telemetria, az eszköz tulajdonságait, valamint azokat a parancsokat, amelyekre az eszköz válaszol.

### <a name="add-a-device-capability-model"></a>Eszköz-képesség modell hozzáadása

Az eszköz képességeinek modelljét több lehetőség is felveszi a IoT Central alkalmazásba. Létrehozhat egy új modellt, egy modellt importálhat egy fájlból, vagy kiválaszthat egy eszközt az eszköz katalógusból. A IoT Central az *eszköz első* megközelítését is támogatja, ahol automatikusan importál egy modellt egy adattárból, amikor az eszköz először csatlakozik. Ebben a rövid útmutatóban egy eszközt választ ki az eszköz-katalógusból az eszköz képességi modelljének importálásához.

A következő lépések bemutatják, hogyan importálhatja a **MXChip IoT fejlesztői készlet** -eszköz képesség modelljét az eszköz-katalógus használatával. Ezek az eszközök telemetria, például hőmérsékletet küldenek az alkalmazásnak:

1. Új sablon hozzáadásához válassza **+** az **eszközök sablonjai** lapot.

1. A **sablon típusának kiválasztása** lapon görgessen lefelé, amíg meg nem találja a **MXChip IoT fejlesztői készlet** csempét.

1. Válassza ki a **MXChip IoT fejlesztői készlet** csempét, majd kattintson a **Tovább gombra: testreszabás**.

1. Az **Áttekintés** lapon válassza a **Létrehozás**lehetőséget.

1. Néhány másodperc elteltével megtekintheti az új eszköz sablonját:

    ![MXChip IoT fejlesztői készlet-eszköz sablonja](./media/quick-create-simulated-device/devkit-template.png)

    A MXChip IoT fejlesztői készlet képesség modellje olyan felületeket tartalmaz, mint például a **mxchip_sensor**, a **Mxchip_settings**és az **eszköz adatai**. A felületek határozzák meg egy MXChip-IoT fejlesztői készlet-eszköz képességeit. A képességek közé tartozik az eszköz által küldött telemetria, az eszköz tulajdonságainak jelentése, valamint az eszköz által válaszoló parancsok.

### <a name="add-cloud-properties"></a>Felhő tulajdonságainak hozzáadása

Az eszközök tartalmazhatnak Felhőbeli tulajdonságokat is. A felhő tulajdonságai csak a IoT Central alkalmazásban érhetők el, és a rendszer soha nem továbbítja, vagy nem fogadja az eszközt.

1. Válassza a **felhő tulajdonságai** lehetőséget, majd a **+ felhő tulajdonságot**. A következő táblázatban található információk segítségével két Felhőbeli tulajdonságot adhat hozzá az eszköz sablonhoz:

    | Megjelenítendő név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | None          | Dátum   |
    | Ügyfél neve     | None          | Sztring |

1. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra:

    ![Felhő tulajdonságai](media/quick-create-simulated-device/cloud-properties.png)

## <a name="views"></a>Nézetek

Építőként testreszabhatja az alkalmazást, hogy az eszközre vonatkozó releváns információkat jelenítsen meg az operátorral. A testreszabások lehetővé teszik, hogy az operátor felügyelje az alkalmazáshoz csatlakoztatott eszközöket. Kétféle nézetet hozhat létre az operátorok számára az eszközök használatához:

* Az eszközök és a felhő tulajdonságainak megtekintésére és szerkesztésére szolgáló űrlapok.
* Irányítópultok az eszközök megjelenítéséhez, beleértve az általuk küldött telemetria is.

### <a name="default-views"></a>Alapértelmezett nézetek

Az alapértelmezett nézetek gyors módszert biztosítanak a fontos eszközök megjelenítésének megkezdéséhez. Az eszköz sablonjaihoz legfeljebb három alapértelmezett nézet hozható létre:

* A **parancsok** nézet lehetővé teszi, hogy az operátor parancsokat küldjön az eszközre.
* Az **Áttekintés** nézet diagramokat és mérőszámokat használ az eszközök telemetria megjelenítéséhez.
* A **Névjegy** nézet megjeleníti az eszköz tulajdonságait.

Válassza ki a **nézetek** csomópontot az eszköz sablonjában. Láthatja, hogy IoT Central a sablon hozzáadásakor **áttekintést** és a **Névjegy** nézetét hozta létre.

Új **felügyelt eszköz** űrlap hozzáadása, amelyet az operátor használhat az eszköz felügyeletére:

1. Válassza ki a **nézetek** csomópontot, majd válassza az **eszköz és a Felhőbeli** adatcsempe szerkesztése lehetőséget egy új nézet hozzáadásához.

1. Módosítsa az űrlap nevét az **eszköz kezelésére**.

1. Válassza ki az **ügyfél nevét** és az **utolsó szolgáltatás dátumának** Felhőbeli tulajdonságait, valamint a **ventilátor sebessége** tulajdonságot. Ezután válassza a **Hozzáadás szakaszt**:

    ![Új űrlap létrehozása](media/quick-create-simulated-device/new-form.png)

1. Az új űrlap mentéséhez válassza a **Mentés** lehetőséget.

## <a name="publish-device-template"></a>Eszköz sablonjának közzététele

Szimulált eszköz létrehozása vagy valódi eszköz csatlakoztatása előtt közzé kell tennie az eszköz sablonját. Habár IoT Central közzétette a sablont, amikor először hozta létre, közzé kell tennie a frissített verziót.

Eszköz sablonjának közzététele:

1. Nyissa meg az eszköz sablonját az **eszközök sablonjai** lapon.

1. **Közzététel**kiválasztása:

    ![Közzétett modell](media/quick-create-simulated-device/published-model.png)

1. Az **eszköz sablonjának közzététele az alkalmazás** párbeszédpanelen válassza a **Közzététel**lehetőséget. 

Miután közzétett egy sablont, az az **eszközök** lapon látható. Egy közzétett eszköz sablonjában nem szerkesztheti az eszköz képességeinek modelljét új verzió létrehozása nélkül. A közzétett eszközön azonban verziószámozás nélkül is frissítheti a felhő tulajdonságait, testreszabásait és nézeteit. A módosítások elvégzése után válassza a **Közzététel** lehetőséget a módosítások elküldéséhez az operátornak.

## <a name="add-a-simulated-device"></a>Szimulált eszköz hozzáadása

Szimulált eszköz az alkalmazáshoz való hozzáadásához használja a létrehozott **MXChip IoT fejlesztői készlet** -sablont.

1. Új eszköz hozzáadása operátorként a bal oldali ablaktáblán válassza az **eszközök** lehetőséget. A **Devices (eszközök** ) lapon az **összes eszköz** és a **MXChip IoT fejlesztői készlet** -sablon látható. Válassza a **MXChip IoT fejlesztői készlet**lehetőséget.

1. Szimulált fejlesztői készlet-eszköz hozzáadásához válassza a **+** elemet. Használja a javasolt **eszköz azonosítóját** , vagy adja meg a saját kisbetűs **eszközének azonosítóját**. Megadhatja az új eszköz nevét is. Győződjön meg arról, hogy a **szimulált** váltógomb be van **kapcsolva** , majd válassza a **Létrehozás**lehetőséget.

    ![Szimulált eszköz](./media/quick-create-simulated-device/simulated-device.png)

Mostantól a Builder által az eszköz sablonja által létrehozott nézeteket szimulált adatként használhatja:

1. Válassza ki a szimulált eszközt az **eszközök** lapon.

1. Az **Áttekintés** nézetben látható a szimulált telemetria ábrázolása:

    ![Áttekintés nézet](./media/quick-create-simulated-device/simulated-telemetry.png)

1. A **Névjegy** nézet a tulajdonságok értékeit jeleníti meg, beleértve a nézethez hozzáadott felhő-tulajdonságokat is.

1. A **parancsok** nézetben parancsokat futtathat, például a **villogást** az eszközön.

1. Az **eszközök kezelése** nézet a kezelő számára az eszköz kezeléséhez létrehozott űrlap.

## <a name="use-a-simulated-device-to-improve-views"></a>A nézetek fejlesztése szimulált eszköz használatával

Miután létrehozta az új szimulált eszközt, a szerkesztő ezzel az eszközzel folytathatja a fejlesztést és az eszköz sablonra épülő nézeteit.

1. A bal oldali ablaktáblán **válassza az** **MXChip IoT fejlesztői készlet** sablont.

1. Válassza ki a szerkeszteni kívánt nézeteket, vagy hozzon létre egy új nézetet. Válassza az **előnézet eszköz konfigurálása**lehetőséget, majd **válasszon egy futó eszközről**. Itt dönthet úgy, hogy nem rendelkezik előnézeti eszközzel, a teszteléshez konfigurált valódi eszközzel vagy a IoT Centralba felvett meglévő eszközzel.

1. Válassza ki a szimulált eszközt a listában. Ezután válassza az **Alkalmaz** lehetőséget. Most már megtekintheti ugyanazt a szimulált eszközt az eszköz sablon nézetének létrehozási felületén. Ez a nézet diagramok és egyéb vizualizációk esetén hasznos.

    ![Előnézet eszköz konfigurálása](./media/quick-create-simulated-device/configure-preview.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy **MXChip IoT fejlesztői készlet** , és hogyan adhat hozzá szimulált eszközt az alkalmazásához.

Ha többet szeretne megtudni az alkalmazáshoz csatlakoztatott eszközök monitorozásáról, folytassa a gyors üzembe helyezéssel:

> [!div class="nextstepaction"]
> [Szabályok és műveletek konfigurálása](./quick-configure-rules.md)
