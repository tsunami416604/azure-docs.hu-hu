---
title: 'Oktatóanyag: IoT Visual Alerts minta'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban a Custom Vision egy IoT-eszközzel való használatával ismeri fel és jelentheti a vizuális állapotokat a kamera videocsatornájából.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 9f3802ada79ee87d1a04634f7caac3b1b4286dce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978032"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Oktatóanyag: Egyéni látás használata IoT-eszközzel a vizuális állapotok jelentéséhez

Ez a mintaalkalmazás bemutatja, hogyan használhatja a Custom Vision segítségével a vizuális állapotok észlelésére szolgáló eszközt kamerával. Ezt az észlelési forgatókönyvet ioT-eszközön futtathatja a Custom Vision szolgáltatásból exportált ONNX-modell használatával.

A vizuális állapot a kép tartalmát írja le: egy üres szobát vagy egy szobát emberekkel, egy üres műút vagy egy teherautóval ellátott műút, és így tovább. Az alábbi képen látható, hogy az alkalmazás észleli, ha egy banán vagy egy alma kerül a kamera elé.

![Animáció egy felhasználói felület címkézés gyümölcs a kamera előtt](./media/iot-visual-alerts-tutorial/scoring.gif)

Ez az oktatóanyag a következőket mutatja be:
> [!div class="checklist"]
> * Konfigurálja a mintaalkalmazást saját Egyéni Látás- és IoT Hub-erőforrások használatára.
> * Az alkalmazás segítségével betaníthatja a Custom Vision projektet.
> * Az alkalmazás segítségével valós időben szerezhet új képeket, és elküldtheti az eredményeket az Azure-nak.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Ennek a projektnek **compact** image besorolási projektnek kell lennie, mert később exportáljuk a modellt az ONNX-re.
* Emellett létre kell [hoznia egy IoT Hub-erőforrást](https://ms.portal.azure.com/#create/Microsoft.IotHub) az Azure-ban.
* [Visual Studio 2015-ös vagy újabb verzió](https://www.visualstudio.com/downloads/)
* Opcionálisan egy IoT-eszköz, amelyen A Windows 10 IoT Core 17763-as vagy újabb verziója fut. Az alkalmazást közvetlenül a számítógépről is futtathatja.
   * A Raspberry Pi 2 és 3 esetén a Windows 10-et közvetlenül az IoT Dashboard alkalmazásból állíthatja be. Más eszközök, például a DrangonBoard esetében az [eMMC módszerrel](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices)kell villognia. Ha segítségre van szüksége egy új eszköz beállításához, olvassa el [Az eszköz beállítása a](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) Windows IoT dokumentációjában című témakört.

## <a name="about-the-visual-alerts-app"></a>A Vizuális riasztások alkalmazás

Az IoT Visual Alerts alkalmazás folyamatos ciklusban fut, és szükség szerint négy különböző állapot között vált:

* **Nincs modell:** Nem műveleti állapot. Az alkalmazás folyamatosan alszik egy másodpercig, és ellenőrizze a kamerát.
* **Betanítási képek rögzítése:** Ebben az állapotban az alkalmazás rögzít egy képet, és feltölti azt a cél egyéni látásprojektbe betanítási képként. Az alkalmazás ezután 500 ms-ig alvó állapotban van, és addig megismétli a műveletet, amíg a beállított célképszámot nem rögzíti. Ezután elindítja a custom vision modell betanítását.
* **Várakozás a betanított modellre:** Ebben az állapotban az alkalmazás minden másodpercben meghívja a Custom Vision API-t, hogy ellenőrizze, hogy a célprojekt tartalmaz-e betanított iterációt. Ha talál egyet, letölti a megfelelő ONNX-modellt egy helyi fájlba, és **pontozási** állapotba vált.
* **Pontozás:** Ebben az állapotban az alkalmazás a Windows ML segítségével értékeli ki a kamera egyetlen képkockáját a helyi ONNX modellhez képest. Az eredményül kapott képbesorolás jelenik meg a képernyőn, és üzenetként küldi el az IoT Hub. Az alkalmazás ezután egy másodpercig alvó állapotban van, mielőtt új képet szerezne.

## <a name="understand-the-code-structure"></a>A kódszerkezet ismertetése

A következő fájlok kezelik az alkalmazás fő funkcióit.

| Fájl | Leírás |
|-------------|-------------|
| [Főoldal.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Ez a fájl határozza meg az XAML felhasználói felületét. Ez ad otthont a webkamera-vezérlő, és tartalmazza a címkéket használt állapotfrissítéseket.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Ez a kód szabályozza az XAML felhasználói felületének működését. Ez tartalmazza az állapot gép feldolgozó kódot.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Ez az osztály egy burkoló, amely kezeli a Custom Vision Szolgáltatással való integrációt.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Ez az osztály egy burkoló, amely kezeli a Windows ML-vel való integrációt az ONNX-modell betöltéséhez és a képek pontozásához.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | Ez az osztály egy burkoló, amely kezeli az IoT Hubintegrációt a pontozási eredmények Azure-ba való feltöltéséhez.|

## <a name="set-up-the-visual-alerts-app"></a>A Vizuális riasztások alkalmazás beállítása

Az alábbi lépésekkel leképezheti az IoT Visual Alerts alkalmazást a számítógépen vagy az IoT-eszközön.

1. Klónozza vagy töltse le az [IoTVisualAlerts mintát](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) a GitHubon.
1. Az _IoTVisualAlerts.sln_ megoldás megnyitása a Visual Studióban
1. Integrálja egyéni vision projektjét:
    1. A _CustomVision\CustomVisionServiceWrapper.cs_ parancsfájlban `ApiKey` frissítse a változót a betanítási kulccsal.
    1. Ezután `Endpoint` frissítse a változót a kulcshoz társított végpont URL-címével.
    1. Frissítse `targetCVSProjectGuid` a változót a használni kívánt Egyéni látásprojekt megfelelő azonosítójával. 
1. Az IoT Hub-erőforrás beállítása:
    1. Az _IoTHub\IotHubWrapper.cs_ parancsfájlban `s_connectionString` frissítse a változót az eszköz megfelelő kapcsolati karakterláncával. 
    1. Az Azure Portalon töltse be az IoT Hub-példányt, kattintson az **IoT-eszközök** re **az Intézők**csoportban, válassza ki a céleszközön (vagy hozzon létre egyet, ha szükséges), és keresse meg a kapcsolati karakterláncot az **Elsődleges kapcsolati karakterlánc**csoportban. A karakterlánc tartalmazza az IoT Hub nevét, az eszközazonosítót és a megosztott hozzáférési kulcsot; ez birtokol a `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`következő formátum: .

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ha az alkalmazást a számítógépen futtatja, válassza a **Helyi számítógép** lehetőséget a céleszközhöz a Visual Studióban, és válassza az **x64** vagy **x86** lehetőséget a célplatformhoz. Ezután nyomja le az F5 billentyűt a program futtatásához. Az alkalmazásnak el kell indítania és meg kell jelenítenie a kamera élő közvetítését és egy állapotüzenetet.

Ha arm-processzorral rendelkező IoT-eszközre telepít, az **ARM-ot** kell célplatformként, a **távoli gépet** pedig céleszközként választania. Amikor a rendszer kéri, adja meg az eszköz IP-címét (ugyanazon a hálózaton kell lennie, mint a számítógépének). Az IP-címet az eszköz indítása után a Windows IoT alapértelmezett alkalmazásából szerezheti be, és csatlakoztathatja a hálózathoz. A program futtatásához nyomja le az F5 billentyűt.

Amikor először futtatja az alkalmazást, nem lesz tudomása a vizuális állapotokról. Egy állapotüzenetet jelenít meg, amely szerint nincs elérhető modell. 

## <a name="capture-training-images"></a>Betanítási képek rögzítése

Modell beállításához az alkalmazást a **Betanítási képek rögzítése** állapotba kell helyeznie. Tegye az alábbi lépések egyikét:
* Ha az alkalmazást pc-n futtatja, használja a felhasználói felület jobb felső sarkában található gombot.
* Ha az alkalmazást IoT-eszközön futtatja, `EnterLearningMode` hívja meg a metódust az eszközön az IoT Hubon keresztül. Az Azure Portal IoT Hub menüjében vagy egy olyan eszközzel, mint az [IoT Hub eszközkezelője](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)keresztül hívhatja meg.
 
Amikor az alkalmazás belép a **Rögzítés idomítási képek** állapotába, másodpercenként körülbelül két képet fog rögzíteni, amíg el nem éri a képek célszámát. Alapértelmezés szerint ez 30 kép, de beállíthatja ezt a paramétert a `EnterLearningMode` kívánt szám átadásával az IoT Hub metódus. 

Miközben az alkalmazás képeket készít, meg kell mutatnia a fényképezőgépet az észlelni kívánt vizuális állapotok típusainak (például egy üres szoba, egy szoba emberekkel, egy üres asztal, egy asztal egy játékteherautóval, és így tovább).

## <a name="train-the-custom-vision-model"></a>Az egyéni vision modell betanítása

Miután az alkalmazás befejezte a képek rögzítését, feltölti őket, majd átvált a **Waiting For Betanított modell** állapotra. Ezen a ponton meg kell mennie a [Custom Vision portálon,](https://www.customvision.ai/) és hozzon létre egy modellt az új betanítási képek alapján. A következő animáció egy példát mutat be erre a folyamatra.

![Animáció: címkézés több kép a banán](./media/iot-visual-alerts-tutorial/labeling.gif)

Ha meg szeretné ismételni ezt a folyamatot a saját forgatókönyvével:

1. Jelentkezzen be a [Custom Vision portálra.](http://customvision.ai)
1. Keresse meg a célprojektet, amelynek most már az alkalmazás által feltöltött összes betanítási képpel rendelkeznie kell.
1. Minden azonosítani kívánt vizuális állapothoz jelölje ki a megfelelő képeket, és manuálisan alkalmazza a címkét.
    * Ha például az a célod, hogy különbséget tegyünk egy üres szoba és egy olyan szoba között, amelyben emberek vannak, azt javasoljuk, hogy öt vagy több képet jelöljön meg az emberekkel új osztályként, **a Személyek**és az öt vagy több kép megjelölése nélkül, akik nem rendelkeznek **negatív** címkével. Ez segít a modell különbséget a két állam között.
    * Egy másik példa, ha a cél az, hogy megközelítse, hogy mennyire tele van egy polc, akkor használhat címkéket, például **EmptyShelf**, **PartiallyFullShelf**és **FullShelf**.
1. Ha végzett, válassza a **Vonat** gombot.
1. A betanítás befejezése után az alkalmazás észleli, hogy egy betanított iteráció érhető el. Elindítja a betanított modell EXPORTÁLÁSát az ONNX-re, és letölti azt az eszközre.

## <a name="use-the-trained-model"></a>A betanított modell használata

Miután az alkalmazás letölti a betanított modellt, átvált a **pontozási** állapotba, és folyamatos ciklusban kezdi meg a képek pontozási pontozását a kamerából.

Az alkalmazás minden egyes rögzített képnél megjeleníti a felső címkét a képernyőn. Ha nem ismeri fel a vizuális állapotot, akkor a **Nincs egyezés**. Az alkalmazás ezeket az üzeneteket is elküldi az IoT Hubnak, és ha egy osztály észlel, `detectedClassAlert`az üzenet tartalmazza a címkét, a megbízhatósági pontszámot és a nevű tulajdonságot, amelyet az IoT Hub-ügyfelek használhatnak, akik a tulajdonságok alapján gyors üzenet-útválasztást szeretnek.

Ezenkívül a minta sense [HAT könyvtárat](https://github.com/emmellsoft/RPi.SenseHat) használ, hogy észlelje, ha egy Sense HAT egységgel rendelkező Raspberry Pi-n fut, így kimeneti kijelzőként használhatja, ha az összes kijelzőt pirosra állítja, amikor osztályt észlel, és üres, ha nem észlel semmit.

## <a name="reuse-the-app"></a>Az alkalmazás újrafelhasználása

Ha vissza szeretné állítani az alkalmazást az eredeti állapotába, ezt megteheti a felhasználói felület jobb felső sarkában található gombra `DeleteCurrentModel` kattintva, vagy a metódus meghívásával az IoT Hubon keresztül.

Bármikor megismételheti a betanítási képek feltöltésének lépését a jobb felső felhasználói `EnterLearningMode` felület gombjára kattintva vagy a metódus ismételt felhívásával.

Ha az alkalmazást egy eszközön futtatja, és újra le kell kérnie az IP-címet (például távoli kapcsolatot `GetIpAddress` kell létesítenie a [Windows IoT távoli ügyfélen](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)keresztül), meghívhatja a metódust az IoT Hubon keresztül.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az egyéni vision projektet, ha már nem szeretné karbantartani. A [Custom Vision webhelyen](https://customvision.ai)keresse meg a **Projektek** elemet, és válassza ki az új projekt alatti kukát.

![Képernyőkép: az Új projekt feliratú panel kukásikonnal](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban beállít és futtat egy alkalmazást, amely észleli a vizuális állapotinformációkat egy IoT-eszközön, és elküldi az eredményeket az IoT Hubnak. Ezután vizsgálja meg tovább a forráskódot, vagy hajtsa végre az alábbi javasolt módosítások egyikét.

> [!div class="nextstepaction"]
> [IoTVisualAlerts-minta (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Adjon hozzá egy IoT Hub-metódust, hogy az alkalmazás közvetlenül a Várakozás a betanított modell állapotba **váltson.** Így betaníthatja a modellt olyan képekkel, amelyeket nem maga az eszköz rögzített, majd leküldéses az új modellt a parancsban lévő eszközre.
* Kövesse a [Visualize valós idejű érzékelő adatbemutató](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) ját a Power BI irányítópultjának létrehozásához a minta által küldött IoT Hub-riasztások megjelenítéséhez.
* Kövesse az [IoT távoli figyelési](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) oktatóanyag egy logikai alkalmazás, amely válaszol az IoT Hub riasztások, ha vizuális állapotok észlelése.
