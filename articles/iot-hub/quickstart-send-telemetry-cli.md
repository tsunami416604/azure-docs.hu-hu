---
title: Telemetria küldése az Azure IoT Hub (CLI) rövid útmutatójának
description: Ez a rövid útmutató bemutatja az IoT Hub új fejlesztőinek, hogyan kezdheti el az Azure CLI segítségével egy IoT-központ létrehozásához, telemetriai adatok küldéséhez és az eszközök és a hub közötti üzenetek megtekintéséhez.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 711e15986265324bbb353fb2b4404cbfeb48dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78851422"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Rövid útmutató: Telemetriai adatok küldése egy eszközről egy IoT hubra, és figyelheti az Azure CLI-vel

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban az Azure CLI segítségével hozzon létre egy IoT Hub és egy szimulált eszköz, küldjön eszköz telemetriai adatokat a hubra, és küldjön egy felhőből az eszközre üzenetet. Az Azure Portal on is vizualizálhatja az eszköz metrikák. Ez egy alapvető munkafolyamat azoknak a fejlesztőknek, akik a CLI-t használják az IoT Hub-alkalmazásokkal való interakcióhoz.

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egyet ingyenesen,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
- Azure CLI. Ebben a rövid útmutatóban futtathatja az összes parancsot az Azure Cloud Shell, egy interaktív CLI rendszerhéj használatával, amely a böngészőben fut. Ha a Cloud Shellt használja, nem kell semmit telepítenie. Ha a CLI-t helyileg szeretné használni, ez a rövid útmutató az Azure CLI 2.0.76-os vagy újabb verzióját igényli. A verzió megkereséséhez futtassa a következő parancsot: az --version. A telepítéshez vagy frissítéshez olvassa [el az Azure CLI telepítése]( /cli/azure/install-azure-cli)című témakört.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Függetlenül attól, hogy a CLI-t helyileg vagy a Cloud Shellben futtatja, tartsa nyitva a portált a böngészőjében.  A rövid útmutató későbbi részében használhatja.

## <a name="launch-the-cloud-shell"></a>A Cloud Shell elindítása
Ebben a szakaszban elindítja az Azure Cloud Shell egy példányát. Ha a CLI-t helyileg használja, ugorjon a [Két CLI-munkamenet előkészítése](#prepare-two-cli-sessions)szakaszra.

A Cloud Shell elindítása:

1. Válassza a **Felhőhéj** gombot az Azure Portal jobb felső menüsorában. 

    ![Az Azure Portal Cloud Shell gombja](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Ha ez az első alkalom, hogy a Cloud Shellt használja, a rendszer kéri, hogy hozzon létre tárolót, amely a Cloud Shell használatához szükséges.  Válasszon ki egy előfizetést egy tárfiók és a Microsoft Azure Files megosztás létrehozásához. 

2. Válassza ki a kívánt CLI-környezetet a **Környezet kiválasztása** legördülő menüben. Ez a rövid útmutató a **Bash** környezetet használja. Az alábbi CLI-parancsok mindegyike a Powershell környezetben is működik. 

    ![CLI-környezet kiválasztása](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Két CLI-munkamenet előkészítése

Ebben a szakaszban két Azure CLI-munkamenetet készít elő. Ha a Cloud Shellt használja, a két munkamenetet külön böngészőlapokon fogja futtatni. Ha helyi CLI-ügyfelet használ, két külön CLI-példányt fog futtatni. Az első munkamenetet szimulált eszközként, a második munkamenetet pedig az üzenetek figyelésére és küldésére fogja használni. Parancs futtatásához válassza a **Másolás** lehetőséget a rövid útmutatóban lévő kódblokk másolásához, illessze be a rendszerhéj-munkamenetbe, és futtassa azt.

Az Azure CLI megköveteli, hogy be kell jelentkeznie az Azure-fiókjába. Az Azure CLI rendszerhéj-munkamenet és az IoT hub közötti minden kommunikáció hitelesítve és titkosítva van. Ennek eredményeképpen ez a rövid útmutató nem igényel további hitelesítést, amelyet egy valódi eszközzel, például egy kapcsolati karakterlánccal használna.

*  Futtassa az [az bővítmény hozzáadása](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) parancsot, és adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a CLI-rendszerhéjhoz. Az IOT-bővítmény hozzáadja az IoT Hub, az IoT Edge és az IoT-eszközlétesítési szolgáltatás (DPS) adott parancsokat az Azure CLI-hez.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Az Azure IOT-bővítmény telepítése után nem kell újra telepítenie a Cloud Shell-munkamenetekben. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Nyisson meg egy második CLI-munkamenetet.  Ha a Cloud Shellt használja, válassza az **Új munkamenet megnyitása**lehetőséget. Ha helyileg használja a CLI-t, nyisson meg egy második példányt. 

    >[!div class="mx-imgBorder"]
    >![Új Cloud Shell-munkamenet megnyitása](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása
Ebben a szakaszban az Azure CLI használatával hozzon létre egy erőforráscsoportot és egy IoT Hub.  Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az IoT Hub központi üzenetközpontként működik az IoT-alkalmazás és az eszközök közötti kétirányú kommunikációhoz. 

> [!TIP]
> Szükség esetén létrehozhat egy Azure-erőforráscsoportot, egy IoT Hubot és más erőforrásokat az [Azure Portal](iot-hub-create-through-portal.md), a [Visual Studio-kód](iot-hub-create-use-iot-toolkit.md)vagy más programozott módszerek használatával.  

1. Erőforráscsoport létrehozásához futtassa az [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) parancsot. A következő parancs létrehoz egy *MyResourceGroup* nevű erőforráscsoportot az *eastus* helyen. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. IoT-központ létrehozásához futtassa az [aziot hub create](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) parancsot. Az IoT-központ létrehozása eltarthat néhány percig. 

    *YourIotHubName*. Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névre. Az IoT-központ nevének globálisan egyedinek kell lennie az Azure-ban. Ez a helyőrző a rövid útmutató többi részében az IoT hub nevének ábrázolására szolgál.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Eszköz létrehozása és figyelése
Ebben a szakaszban egy szimulált eszközt hoz létre az első CLI-munkamenetben. A szimulált eszköz elküldi az eszköz telemetriáját az IoT hub. A második CLI-munkamenetben figyelheti az eseményeket és a telemetriai adatokat, és egy felhőből az eszközre irányuló üzenetet küld a szimulált eszköznek.

Szimulált eszköz létrehozása és indítása:
1. Futtassa az [az iot hub eszközidentitás-létrehozási](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) parancsot az első CLI-munkamenetben. Ez létrehozza a szimulált eszközidentitást. 

    *YourIotHubName*. Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névre. 

    *simDevice*. Ezt a nevet közvetlenül használhatja a szimulált eszközhöz a rövid útmutató többi részében. Tetszés szerint használjon másik nevet. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Futtassa az [az iot eszköz szimulálása](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-simulate) parancsot az első CLI-munkamenetben.  Ezzel elindítja a szimulált eszközt. Az eszköz telemetriai adatokat küld az IoT hub, és üzeneteket fogad tőle.  

    *YourIotHubName*. Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névre. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Eszköz figyelése:
1. A második CLI-munkamenetben futtassa az [az iot hub monitor-events](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) parancsot. Ezzel megkezdi a szimulált eszköz figyelését. A kimenet a szimulált eszköz által az IoT hubnak küldött telemetriai adatokat jeleníti meg.

    *YourIotHubName*. Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névre. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![A Cloud Shell eseményeket figyel](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Miután a második CLI-munkamenetben figyelte a szimulált eszközt, a Ctrl+C billentyűkombinációval állítsa le a figyelést. 

## <a name="use-the-cli-to-send-a-message"></a>Üzenet küldése a CLI segítségével
Ebben a szakaszban a második CLI-munkamenet segítségével küldhet üzenetet a szimulált eszköznek.

1. Az első CLI-munkamenetben ellenőrizze, hogy a szimulált eszköz fut-e. Ha az eszköz leállt, a következő parancsot kell futtatni a indításához:

    *YourIotHubName*. Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névre. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. A második CLI-munkamenetben futtassa az [az iot eszköz c2d-message send](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device/c2d-message?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-c2d-message-send) parancsát. Ez egy felhőből az eszközre irányuló üzenetet küld az IoT hubról a szimulált eszközre. Az üzenet egy karakterláncot és két kulcs-érték párt tartalmaz.  

    *YourIotHubName*. Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névre. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Szükség esetén felhőből az eszközre irányuló üzeneteket küldhet az Azure Portalhasználatával. Ehhez keresse meg az IoT Hub áttekintő lapját, válassza az **IoT-eszközök**lehetőséget, jelölje ki a szimulált eszközt, és válassza az **Üzenet az eszköznek**lehetőséget. 

1. Az első CLI-munkamenetben ellenőrizze, hogy a szimulált eszköz megkapta-e az üzenetet. 

    ![Cloud Shell felhőből az eszközre című üzenet](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Az üzenet megtekintése után zárja be a második CLI-munkamenetet. Tartsa nyitva az első CLI-munkamenetet. Egy későbbi lépésben az erőforrások karbantartására használhatja.

## <a name="view-messaging-metrics-in-the-portal"></a>Üzenetkezelési mutatók megtekintése a portálon
Az Azure Portal lehetővé teszi az IoT Hub és az eszközök minden aspektusának kezelését. Egy tipikus IoT Hub-alkalmazás, amely betöltése telemetriai adatok eszközökről, érdemes lehet az eszközök figyelése vagy metrikák megtekintése az eszköz telemetriai adatok. 

Üzenetkezelési metrikák megjelenítése az Azure Portalon:
1. A portál bal oldali navigációs menüjében válassza az **Összes erőforrás lehetőséget.** Ez felsorolja az összes erőforrást az előfizetésben, beleértve a létrehozott IoT hub. 

1. Válassza ki a létrehozott IoT-központ on a kapcsolatot. A portál megjeleníti a hub áttekintő lapját.

1. Válassza **ki a metrikák** az IoT Hub bal oldali ablaktáblájában. 

    ![Az IoT Hub üzenetkezelési metrikái](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Adja meg az IoT hub nevét a **Scope .**

2. Válassza az *Iot Hub standard metrikákat* a **Metrika névtérben.**

3. Válassza a **Metric (Metrika)** *üzeneteinek teljes számát.* 

4. Vigye az egérmutatót annak az idővonalnak a területe fölé, amelyben az eszköz üzeneteket küldött. Egy adott időpontban az üzenetek teljes száma az ütemterv bal alsó sarkában jelenik meg.

    ![Az Azure IoT Hub-metrikák megtekintése](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Szükség esetén a **Metrika** legördülő menüsegítségével más metrikákat is megjeleníthet a szimulált eszközön. Például a *C2d üzenetkézbesítésbefejeződött* vagy *az Összes eszköz (előzetes verzió)*. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az Ebben a rövid útmutatóban létrehozott Azure-erőforrásokra, az Azure CLI használatával törölheti őket.

Ha folytatja a következő ajánlott cikket, megtarthatja a már létrehozott erőforrásokat, és újra felhasználhatja őket. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

Erőforráscsoport törlése név alapján:
1. Futtassa az [az csoport törlési](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) parancsát. Ezzel eltávolítja az erőforráscsoportot, az IoT Hubot és a létrehozott eszközregisztrációt.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Futtassa az [az csoportlista](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) parancsot az erőforráscsoport törlésének megerősítéséhez.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban az Azure CLI segítségével hozzon létre egy IoT-központot, hozzon létre egy szimulált eszközt, küldjön telemetriai adatokat, figyelje a telemetriai adatokat, küldjön egy felhőből az eszközre üzenetet, és törölje az erőforrásokat. Az Azure Portal segítségével vizualizálja az üzenetkezelési metrikákat az eszközén.

Ha Ön egy eszköz fejlesztője, a javasolt következő lépés az, hogy a telemetriai gyorsindítás, amely az Azure IoT Device SDK c. opcionálisan tekintse meg az egyik elérhető Azure IoT Hub telemetriai rövid útmutató cikkek a preferált nyelven vagy SDK.

> [!div class="nextstepaction"]
> [Rövid útmutató: Telemetriai adatok küldése egy eszközről egy IoT hub (C)](quickstart-send-telemetry-c.md)
