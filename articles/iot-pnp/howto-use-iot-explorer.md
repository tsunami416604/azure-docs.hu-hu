---
title: Az Azure IoT Explorer telepítése és használata | Microsoft Docs
description: Telepítse az Azure IoT Explorer eszközt, és használja az IoT hub-hoz csatlakoztatott IoT Plug and Play előnézeti eszközökhöz való interakcióhoz.
author: rido-min
ms.author: rmpablos
ms.date: 05/06/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 083dcde44e56af34f17d952c46e554b234818f27
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352233"
---
# <a name="install-and-use-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése és használata

Az Azure IoT Explorer egy grafikus eszköz a IoT Plug and Play előnézeti eszközökhöz való interakcióhoz és teszteléséhez. Miután telepítette az eszközt a helyi gépre, használhatja azt egy hubhoz való kapcsolódáshoz. Az eszköz segítségével megtekintheti az eszközök küldésének telemetria, az eszköz tulajdonságait, valamint parancsokat hívhat meg.

Ez a cikk a következőkhöz nyújt útmutatást:

- Telepítse és konfigurálja az Azure IoT Explorer eszközt.
- Használja az eszközt az eszközök használatához és teszteléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az Azure IoT Explorer eszköz használatához a következőkre lesz szüksége:

- Egy Azure IoT hub. Az Azure-előfizetéshez többféleképpen is hozzáadhat egy IoT hubot, például [egy IoT hub létrehozását az Azure CLI használatával](../iot-hub/iot-hub-create-using-cli.md). Az Azure IoT Explorer eszköz futtatásához szüksége lesz az IoT hub kapcsolódási karakterláncára. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.
- Az IoT hub-ban regisztrált eszköz. Egy eszköz regisztrálásához használja az alábbi Azure CLI-parancsot. Ügyeljen rá, hogy a `{YourIoTHubName}` és a helyőrzőket cserélje le az `{YourDeviceID}` értékekre:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése

Nyissa meg az [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) kiadásait, és bontsa ki a legújabb kiadáshoz tartozó eszközök listáját. Töltse le és telepítse az alkalmazás legújabb verzióját.

>[!Important]
>A 0.11.0 IoT Explorer verziója csak a Plug and Play IoT 2020-es verzióját támogatja. Ha az előző előzetes verzióban elérhető funkciókat szeretné használni, az augusztus 2019-as kiadásban, egy 0,10. x verziót kell telepítenie.

## <a name="use-azure-iot-explorer"></a>Az Azure IoT Explorer használata

Egy eszközhöz csatlakozhat a saját eszközéhez, vagy használhatja a mintául szolgáló szimulált eszközök egyikét is. Kövesse [ezeket az utasításokat](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) a szimulált eszköz mintájának futtatásához.

### <a name="connect-to-your-hub"></a>Kapcsolódás a hubhoz

Az Azure IoT Explorer első futtatásakor a rendszer a IoT hub csatlakozási karakterláncát kéri. A kapcsolati karakterlánc hozzáadása **után válassza a kapcsolat lehetőséget**. Az eszköz beállításai segítségével másik IoT-hubhoz válthat a kapcsolódási karakterlánc frissítésével.

Egy IoT Plug and Play eszköz modell-definícióját a rendszer a nyilvános tárházban, a csatlakoztatott eszközön vagy egy helyi mappában tárolja. Alapértelmezés szerint az eszköz a nyilvános tárházban és a csatlakoztatott eszközön keresi a modell definícióját. Hozzáadhat és eltávolíthat forrásokat, vagy konfigurálhatja a források prioritását a **beállításokban**:

Forrás hozzáadása:

1. Ugrás a **Kezdőlap/IoT Plug and Play beállítások menüpontra**
2. Válassza a **Hozzáadás** lehetőséget, majd válassza ki a forrást egy adattárból vagy egy helyi mappából.

Forrás eltávolítása:

1. Ugrás a **Kezdőlap/IoT Plug and Play beállítások menüpontra**
2. Keresse meg az eltávolítani kívánt forrást.
3. Válassza az **X** lehetőséget az eltávolításához.

A forrás prioritásának módosítása:

A modell definíciós forrásainak egyikét áthúzással áthelyezheti egy másik rangsorba a listában. 

### <a name="view-devices"></a>Eszközök megtekintése

Miután az eszköz csatlakozik az IoT hubhoz, megjeleníti az **eszközök** listája oldalt, amely felsorolja az IoT hub-ban regisztrált eszköz identitásait. A listában bármelyik bejegyzés kiválasztásával további információkat tekinthet meg.

Az **eszközök** listája lapon a következőket teheti:

- Az **új** lehetőség kiválasztásával regisztrálhat egy új eszközt a hubhoz. Ezután adja meg az eszköz AZONOSÍTÓját. Az alapértelmezett beállítások használatával automatikusan generálhat hitelesítési kulcsokat, és engedélyezheti a kapcsolódást a hubhoz.
- Válasszon ki egy eszközt, majd válassza a **Törlés** lehetőséget az eszköz identitásának törléséhez. A művelet elvégzése előtt tekintse át az eszköz részleteit, és győződjön meg róla, hogy a megfelelő eszköz identitását törli.

## <a name="interact-with-a-device"></a>Eszköz használata

Az **eszközök** listája lapon válasszon ki egy értéket az **eszköz azonosítója** oszlopban a regisztrált eszköz részletes oldalának megtekintéséhez. Minden eszközhöz két rész található: az **eszköz** és a **digitális Twin**.

### <a name="device"></a>Eszköz

Ebben a szakaszban az **eszköz identitása**, az **eszköz Twin**, a **telemetria**, a **Direct metódus**, a **felhőből az eszközre irányuló üzenet**, a **modul-identitás** lapok találhatók.

- Az Eszközállapot **lapon** megtekintheti és frissítheti az [eszköz identitásának](../iot-hub/iot-hub-devguide-identity-registry.md) adatait.
- A Device Twin **(eszközök Twin) lapon érheti** el az [eszköz két](../iot-hub/iot-hub-devguide-device-twins.md) adatát.
- Ha egy eszköz csatlakoztatva van, és aktívan küld adatokat, megtekintheti a [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) a **telemetria** lapon.
- A **közvetlen** metódus lapon hívhat [közvetlen metódust](../iot-hub/iot-hub-devguide-direct-methods.md) az eszközön.
- A felhőből az [eszközre irányuló](../iot-hub/iot-hub-devguide-messages-c2d.md) **üzeneteket a felhőből az eszközre küldött üzenetek** lapon küldheti el.
- A [modulhoz tartozó Twin](../iot-hub/iot-hub-devguide-module-twins.md) -adatokat is elérheti.

### <a name="iot-plug-and-play-components"></a>IoT Plug and Play-összetevők

Ha az eszköz egy **modell azonosítójának**használatával csatlakozik az hubhoz, az eszköz megjeleníti a **IoT Plug and Play Components** fület, ahol LÁTHATja a **modell azonosítóját**.

Ha a **modell azonosítója** elérhető az egyik konfigurált forrásban – nyilvános tárházban vagy helyi mappában, megjelenik az összetevők listája. Egy összetevő kiválasztásával az elérhető tulajdonságok, parancsok és telemetria láthatók.

Az **összetevő** lapon megtekintheti az írásvédett tulajdonságokat, frissítheti az írható tulajdonságokat, parancsokat hívhat meg, és megtekintheti az összetevő által létrehozott telemetria-üzeneteket.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Összetevők megtekintése az Azure IoT Explorerben":::

#### <a name="properties"></a>Tulajdonságok

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Tulajdonságok megtekintése az Azure IoT Explorerben":::

Megtekintheti a **Tulajdonságok (csak olvasható)** lapon lévő felületen megadott írásvédett tulajdonságokat. A **(z) tulajdonságok (írható)** lapon lévő illesztőfelületben definiált írható tulajdonságok frissíthetők:

1. Lépjen a **Tulajdonságok (írható)** lapra.
1. Kattintson a frissíteni kívánt tulajdonságra.
1. Adja meg a tulajdonság új értékét.
1. Tekintse át az eszközre küldendő hasznos adatokat.
1. Küldje el a változást.

A módosítás elküldése után nyomon követheti a frissítési állapotot: **szinkronizálás**, **sikeres**vagy **hiba**. A szinkronizálás befejezésekor megjelenik a tulajdonság új értéke a **jelentett tulajdonság** oszlopban. Ha a szinkronizálás befejeződése előtt más lapokra navigál, az eszköz továbbra is értesíti, ha a frissítés befejeződött. Az értesítési előzmények megjelenítéséhez az eszköz értesítési központját is használhatja.

#### <a name="commands"></a>Parancsok

Ha parancsot szeretne küldeni egy eszközre, lépjen a **parancsok** lapra:

1. A parancsok listájában bontsa ki az elindítani kívánt parancsot.
1. Adja meg a parancshoz szükséges értékeket.
1. Tekintse át az eszközre küldendő hasznos adatokat.
1. Küldje el a parancsot.

#### <a name="telemetry"></a>Telemetria

A kiválasztott felület telemetria megtekintéséhez nyissa meg a **telemetria** lapot.

## <a name="next-steps"></a>További lépések

Ebben a útmutatóban megtanulta, hogyan telepítheti és használhatja az Azure IoT Explorer alkalmazást a IoT Plug and Play eszközeivel való interakcióhoz. A következő lépés azt ismerteti, hogyan használható az [Azure CLI-IoT Plug and Play parancsai](./howto-use-iot-pnp-cli.md).
