---
title: Az eszköz belső vezérlőprogramjának frissítése az Azure IoT Hubon keresztül | Microsoft Docs
description: Eszközök belsővezérlőprogram-frissítési folyamatának implementálása feladatok és ikereszközök használatával.
services: iot-hub
author: dominicbetts
manager: timlt
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2019
ms.author: dobett
ms.custom: mvc
ms.openlocfilehash: c387a7c10b962cfd27a7563c00ce58f58e97b9d5
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671828"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Oktatóanyag: Egy eszköz belső vezérlőprogram frissítési folyamata megvalósítása

Előfordulhat, hogy frissíteni kell az IoT Hubhoz csatlakoztatott eszközök belső vezérlőprogramját. Például előfordulhat, hogy új funkciókat szeretne hozzáadni a belső vezérlőprogramhoz vagy biztonsági javításokat szeretne alkalmazni. Számos IoT-forgatókönyvben nem célszerű fizikailag megkeresni, majd manuálisan alkalmazni az eszközök belsővezérlőprogram-frissítéseit. Ez az oktatóanyag bemutatja, hogyan indítható el és monitorozható távolról a belsővezérlőprogram-frissítési folyamat a Hubhoz csatlakoztatott háttéralkalmazásból.

A belsővezérlőprogram-frissítési folyamat létrehozásához és monitorozásához az oktatóanyagban szereplő háttéralkalmazás létrehoz egy _konfigurációt_ az IoT Hubban. Az IoT Hub [automatikus eszközkezelése](iot-hub-auto-device-config.md) ezt a konfigurációt használja az _ikereszköz kívánt tulajdonságainak_ frissítéséhez minden hűtőeszközön. A kívánt tulajdonságok adják meg a szükséges belsővezérlőprogram-frissítés részleteit. Amíg a hűtőeszközök a belsővezérlőprogram-frissítési folyamatot futtatják, jelentést küldenek az állapotukról a háttéralkalmazásnak az _ikereszköz jelentett tulajdonságainak_ használatával. A háttéralkalmazás a konfigurációval monitorozza az eszközről küldött jelentett tulajdonságokat, és nyomon követi a belsővezérlőprogram-frissítési folyamatot a befejezésig:

![A belsővezérlőprogram-frissítési folyamat](media/tutorial-firmware-update/Process.png)

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Létrehoz egy IoT Hubot, és hozzáad egy teszteszközt az eszközidentitás-jegyzékhez.
> * Létrehoz egy konfigurációt a belsővezérlőprogram-frissítés meghatározásához.
> * Szimulálja a belsővezérlőprogram-frissítési folyamatot egy eszközön.
> * Állapotfrissítéseket fogad az eszközről a belsővezérlőprogram-frissítés előrehaladtával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutatóban futtatott két mintaalkalmazás a Node.js használatával készült. A fejlesztői gépen szükség lesz a Node.js 4.x.x. vagy újabb változatára.

A Node.js-t a [nodejs.org](https://nodejs.org) oldalról töltheti le többféle platformra.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

Töltse le a Node.js-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

## <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása

Az oktatóanyag teljesítéséhez az Azure-előfizetésnek rendelkeznie kell egy olyan IoT Hubbal, amelyhez tartoznia kell egy, az eszközidentitás-jegyzékhez hozzáadott eszköznek. Az eszköz identitásjegyzékében található bejegyzés teszi lehetővé az oktatóanyagban futtatott szimulált eszköz csatlakozását a központhoz.

Ha az előfizetésében még nincs IoT-központ, a következő CLI-szkripttel beállíthat egyet. Ez a szkript a **tutorial-iot-hub** nevet használja az IoT-központhoz, de futtatáskor a nevet le kell cserélni az Ön által megadott egyedi névre. A szkript az erőforráscsoportot és a központot az **USA középső régiójában** hozza létre, de ez módosítható egy Önhöz közelebb eső régióra. A szkript lekéri az IoT Hub szolgáltatás kapcsolati sztringjét, amellyel a mintául szolgáló háttéralkalmazásban csatlakozhat az IoT Hubhoz:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later
az iot hub show-connection-string --hub-name $hubname -o table

```

Ez az oktatóanyag egy **MyFirmwareUpdateDevice** nevű szimulált eszközt használ. Az alábbi szkript hozzáadja ezt az eszközt az eszközidentitás-jegyzékhez, beállít egy címkeértéket, és lekéri a kapcsolati sztringet:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity show-connection-string --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Ha ezeket a parancsokat Windows-parancssorban vagy PowerShell-parancssorban futtatja, tekintse meg az [azure-iot-cli-extension tippek](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips
) oldalt a JSON-sztringek idézésével kapcsolatos tudnivalókért.

## <a name="start-the-firmware-update"></a>A belsővezérlőprogram-frissítés elindítása

Hozzon létre egy [automatikus eszközkezelési konfigurációt](iot-hub-auto-device-config.md#create-a-configuration) a háttéralkalmazásban, hogy elkezdhesse a belsővezérlőprogram-frissítési folyamatot az összes olyan eszközön, amelynek **devicetype** értéke „chiller”. Ez a szakasz a következőket ismerteti:

* Konfiguráció létrehozása háttéralkalmazásból.
* A feladat monitorozása a befejeződéséig.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>A kívánt tulajdonságok használata a belsővezérlőprogram-frissítés elindítására a háttéralkalmazásból

A konfigurációt létrehozó háttéralkalmazásának kódjának megtekintéséhez keresse meg az **iot-hub/Tutorials/FirmwareUpdate** mappát a letöltött Node.js mintaprojektben. Ezután nyissa meg a ServiceClient.js fájlt egy szövegszerkesztőben.

A háttéralkalmazás az alábbi konfigurációt hozza létre:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

A konfiguráció a következő szakaszokat tartalmazza:

* A `content` megadja a belső vezérlőprogram a kiválasztott eszközöknek küldött, kívánt tulajdonságait.
* A(z) `metrics` megadja a belsővezérlőprogram-frissítés állapotát jelentő lekérdezéseket.
* A(z) `targetCondition` kiválasztja a belsővezérlőprogram-frissítést fogadó eszközöket.
* A(z) `priorty` beállítja a konfiguráció más konfigurációkhoz viszonyított relatív prioritását.

A háttéralkalmazás a következő kódot használja a konfiguráció létrehozásához, amellyel megadhatók a kívánt tulajdonságok:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

A konfiguráció létrehozása után az alkalmazás monitorozza a belsővezérlőprogram-frissítést:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

A konfiguráció két mérőszámtípusról küld jelentést:

* Rendszermérőszámok: jelentik, hány eszköz kapja meg a frissítést és hány eszközre lett alkalmazva a frissítés.
* Egyéni mérőszámok: a konfigurációhoz hozzáadott lekérdezések hozzák létre. Ebben az oktatóanyagban a lekérdezések jelentik, hogy hány eszköz található a frissítési folyamat egyes fázisaiban.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Válasz a belső vezérlőprogram frissítési kérelmére az eszközön

A belső vezérlőprogram a háttéralkalmazásból küldött kívánt tulajdonságait kezelő szimulálteszköz-kód megtekintéséhez keresse meg az **iot-hub/Tutorials/FirmwareUpdate** mappát a letöltött Node.js-mintaprojektben. Ezután nyissa meg a SimulatedDevice.js fájlt egy szövegszerkesztőben.

A szimulálteszköz-alkalmazás létrehoz egy kezelőt a **properties.desired.firmware** kívánt tulajdonságok frissítéseihez az ikereszközön. A kezelőben elvégez néhány alapvető ellenőrzést a kívánt tulajdonságokon a frissítési folyamat futtatása előtt:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>A belső vezérlőprogram frissítése

Az **initiateFirmwareUpdateFlow** függvény futtatja a frissítést. Ez a függvény a **waterfall** függvénnyel futtatja a frissítési folyamat fázisait egymás után. Ebben a példában a belsővezérlőprogram-frissítés négy fázisból áll. Az első fázis letölti a rendszerképet, a második egy ellenőrzőösszeg használatával ellenőrzi, a harmadik fázis alkalmazza a rendszerképet, az utolsó pedig újraindítja az eszközt:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

A frissítési folyamat során a szimulált eszköz jelentést készít a folyamatról a jelentett tulajdonságok használatával:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

A következő kódrészlet a letöltési fázis implementálását mutatja be. A letöltési fázis során a szimulált eszköz a jelentett tulajdonságok használatával állapotinformációkat küld a háttéralkalmazásnak:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Minta futtatása

Ebben a szakaszban két mintaalkalmazás futtatásával figyelhető meg, hogyan hozza létre a háttéralkalmazás a konfigurációt a belsővezérlőprogram-frissítési folyamat kezeléséhez a szimulált eszközön.

A szimulált eszköz és a háttéralkalmazások futtatásához szükség lesz az eszköz és a szolgáltatás kapcsolati sztringjére. Ezt a sztringet az oktatóanyag elején, az erőforrások létrehozásánál jegyezte fel.

A szimulálteszköz-alkalmazás futtatásához nyisson meg egy felületet vagy parancssori ablakot, és lépjen a letöltött Node.js-projektben az **iot-hub/Tutorials/FirmwareUpdate** mappához. Ezután futtassa le a következő parancsokat:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

A háttéralkalmazás futtatásához nyisson meg egy újabb kezelőfelületet vagy parancssori ablakot. Keresse meg a letöltött Node.js-projektben az **iot-hub/Tutorials/FirmwareUpdate** mappát. Ezután futtassa le a következő parancsokat:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A következő képernyőkép a szimulálteszköz-alkalmazás kimenetét mutatja, és azt, hogyan válaszol a belső vezérlőprogram kívánt tulajdonságinak frissítésére a háttéralkalmazásból:

![Szimulált eszköz](./media/tutorial-firmware-update/SimulatedDevice.png)

A következő képernyőkép a háttéralkalmazás kimenetét mutatja, és kiemeli, hogyan hozza létre a konfigurációt a belső vezérlőprogram kívánt tulajdonságainak frissítéséhez:

![Háttéralkalmazás](./media/tutorial-firmware-update/BackEnd1.png)

A következő képernyőkép a háttéralkalmazás kimenetét mutatja, és kiemeli, hogyan monitorozza a belsővezérlőprogram-frissítési mérőszámokat a szimulált eszközről:

![Háttéralkalmazás](./media/tutorial-firmware-update/BackEnd2.png)

Az IoT Hub eszközidentitás-jegyzékének késése miatt előfordulhat, hogy nem jelenik meg a háttéralkalmazásnak küldött összes állapotfrissítés. A mérőszámokat a portálon is megtekintheti az IoT Hub **Automatikus eszközkezelés -> IoT-eszköz konfigurációja** szakaszában:

![A konfiguráció megtekintése a portálon](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a következő oktatóanyagot is el kívánja végezni, ne távolítsa el az erőforráscsoportot és az IoT-központot, mert ezeket később újból használhatja.

Ha már nincs szüksége az IoT Hubra, az erőforráscsoporttal együtt törölje a Portalon. Ehhez válassza ki az IoT-központot tartalmazó **tutorial-iot-hub-rg** erőforráscsoportot, majd kattintson a **Törlés** gombra.

Ha szeretné, a CLI-t is használhatja:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan implementálható a csatlakoztatott eszközök belsővezérlőprogram-frissítési folyamata. Folytassa a következő oktatóanyaggal, portal-eszközök Azure IoT Hub és az Azure CLI-parancsok használata az eszköz kapcsolat teszteléséhez.

> [!div class="nextstepaction"]
[Szimulált eszköz használata az IoT Hub-kapcsolat ellenőrzéséhez](tutorial-connectivity.md)
