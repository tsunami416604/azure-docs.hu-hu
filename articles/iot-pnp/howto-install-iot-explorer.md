---
title: Az Azure IoT Explorer telepítése és használata | Microsoft Docs
description: Telepítse az Azure IoT Explorer eszközt, és használja a IoT Plug and Play előnézeti eszközökhöz csatlakoztatott IoT-hubhoz való interakcióhoz.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159183"
---
# <a name="install-and-use-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése és használata

Az Azure IoT Explorer egy grafikus eszköz a IoT Plug and Play előnézeti eszközökhöz való interakcióhoz és teszteléséhez. Miután telepítette az eszközt a helyi gépre, használhatja azt egy eszközhöz való kapcsolódáshoz. Az eszköz segítségével megtekintheti az eszköz által küldött telemetria, az eszköz tulajdonságait és a hívási parancsokat.

Ez a cikk a következőkhöz nyújt útmutatást:

- Telepítse és konfigurálja az Azure IoT Explorer eszközt.
- Használja az eszközt az eszközök használatához és teszteléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az Azure IoT Explorer eszköz használatához a következőkre lesz szüksége:

- Egy Azure IoT hub. Az Azure-előfizetéshez többféleképpen is hozzáadhat egy IoT hubot, például [egy IoT hub létrehozását az Azure CLI használatával](../iot-hub/iot-hub-create-using-cli.md). Az Azure IoT Explorer eszköz futtatásához szüksége lesz az IoT hub kapcsolódási karakterláncára. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
- Az IoT hub-ban regisztrált eszköz. Egy eszköz regisztrálásához használja az alábbi Azure CLI-parancsot. Ügyeljen rá, hogy a `{YourIoTHubName}` és `{YourDeviceID}` a helyőrzőket cserélje le az értékekre:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése

Nyissa meg az [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) kiadásait, és bontsa ki a legújabb kiadáshoz tartozó eszközök listáját. Töltse le és telepítse az alkalmazás legújabb verzióját.

## <a name="use-azure-iot-explorer"></a>Az Azure IoT Explorer használata

Egy eszközhöz csatlakozhat a saját eszközéhez, vagy használhatja a mintául szolgáló szimulált eszközök egyikét is. Kövesse [ezeket az utasításokat](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) a szimulált eszköz mintájának futtatásához.

### <a name="connect-to-your-hub"></a>Kapcsolódás a hubhoz

Az Azure IoT Explorer első futtatásakor a rendszer a IoT hub csatlakozási karakterláncát kéri. A kapcsolati karakterlánc hozzáadása **után válassza a kapcsolat lehetőséget**. Az eszköz beállításai segítségével másik IoT-hubhoz válthat a kapcsolódási karakterlánc frissítésével.

Egy IoT Plug and Play eszköz modell-definícióját a rendszer a nyilvános tárházban, a vállalati tárházban vagy a csatlakoztatott eszközön tárolja. Alapértelmezés szerint az eszköz a nyilvános modell adattárában és a csatlakoztatott eszközön keresi a modell definícióját. Hozzáadhat és eltávolíthat forrásokat, vagy konfigurálhatja a források prioritását a **beállításokban**:

Forrás hozzáadása:

1. Válassza a **Beállítások lehetőséget**.
1. Válassza az **új** lehetőséget, és válassza ki a forrást.
1. Ha hozzáadja a vállalati modell tárházát, adja meg a kapcsolatok karakterláncát.

Forrás eltávolítása:

1. Válassza a **Beállítások lehetőséget**.
1. Keresse meg az eltávolítani kívánt forrást.
1. Válassza az **X** lehetőséget az eltávolításához. A nyilvános modell tárháza nem távolítható el, mert az általános illesztőfelület-definíciók ebből a tárházból származnak.

A forrás prioritásának módosítása:

A modell definíciós forrásainak egyikét áthúzással áthelyezheti egy másik rangsorba a listában. Ha ütközés lép fel, a magasabb prioritású definíciós források felülbírálják az alacsonyabb rangsorú forrásokat.

### <a name="view-devices"></a>Eszközök megtekintése

Miután az eszköz csatlakozik az IoT hubhoz, megjeleníti az **eszközök** listája oldalt, amely felsorolja az IoT hub-ban regisztrált eszköz identitásait. A lista bármely bejegyzését kibontva további információkat tekinthet meg.

Az **eszközök** listája lapon a következőket teheti:

- A **Hozzáadás** gombra kattintva regisztrálhat egy új eszközt a hubhoz. Ezután adja meg az eszköz AZONOSÍTÓját. Az alapértelmezett beállítások használatával automatikusan generálhat hitelesítési kulcsokat, és engedélyezheti a kapcsolódást a hubhoz.
- Válasszon ki egy eszközt, majd válassza a **Törlés** lehetőséget az eszköz identitásának törléséhez. A művelet elvégzése előtt tekintse át az eszköz részleteit, és győződjön meg róla, hogy a megfelelő eszköz identitását törli.
- Lekérdezés: `capabilityID` és `interfaceID`. Adja hozzá a `capabilityID` vagy `interfaceID` az paramétert az eszközök lekérdezéséhez.

## <a name="interact-with-a-device"></a>Eszköz használata

Az **eszközök** listája lapon válasszon ki egy értéket az **eszköz azonosítója** oszlopban a regisztrált eszköz részletes oldalának megtekintéséhez. Minden eszközhöz két rész található: az **eszköz** és a **digitális Twin**.

### <a name="device"></a>Eszköz

Ebben a szakaszban az **eszköz identitása**, az **eszköz Twin**, a **telemetria**, a **Direct metódus** és a **felhőből az eszközre küldött üzenetek** lapok találhatók.

- Az Eszközállapot **lapon** megtekintheti és frissítheti az [eszköz identitásának](../iot-hub/iot-hub-devguide-identity-registry.md) adatait.
- A Device Twin **(eszközök Twin) lapon érheti** el az [eszköz két](../iot-hub/iot-hub-devguide-device-twins.md) adatát.
- Ha egy eszköz csatlakoztatva van, és aktívan küld adatokat, megtekintheti a [telemetria](../iot-hub/iot-hub-devguide-messages-read-builtin.md) a **telemetria** lapon.
- A **közvetlen** metódus lapon hívhat [közvetlen metódust](../iot-hub/iot-hub-devguide-direct-methods.md) az eszközön.
- A felhőből az [eszközre irányuló](../iot-hub/iot-hub-devguide-messages-c2d.md) **üzeneteket a felhőből az eszközre küldött üzenetek** lapon küldheti el.

### <a name="digital-twin"></a>Digitális Twin

Az eszköz az eszköz digitális dupla példányára is használható. Egy IoT Plug and Play eszköz esetében az eszköz képességeinek modelljéhez társított összes interfész megjelenik az eszköz ezen szakaszában. Válasszon egy felületet a megfelelő [IoT Plug and Play primitívek](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)kibontásához.

### <a name="interface"></a>Felület

A **csatoló** lapon megtekintheti az illesztőfelület JSON-definícióját.

#### <a name="properties"></a>Tulajdonságok

A **nem írható tulajdonságok** lapon megtekintheti az illesztőfelületben definiált írásvédett tulajdonságokat. A csatolóban definiált írható tulajdonságokat az **írható tulajdonságok** lapon frissítheti:

1. Nyissa meg az **írható tulajdonságok** lapot.
1. Kattintson a frissíteni kívánt tulajdonságra.
1. Adja meg a tulajdonság új értékét.
1. Tekintse át az eszközre küldendő hasznos adatokat.
1. Küldje el a változást.

A módosítás elküldése után nyomon követheti a frissítési állapotot: **szinkronizálás**, **sikeres**vagy **hiba**. A szinkronizálás befejezésekor megjelenik a tulajdonság új értéke a **jelentett tulajdonság** oszlopban. Ha a szinkronizálás befejeződése előtt más lapokra navigál, az eszköz továbbra is értesíti, ha a frissítés befejeződött. Az értesítési előzmények megjelenítéséhez az eszköz értesítési központját is használhatja.

#### <a name="commands"></a>Parancsok

Ha parancsot szeretne küldeni egy eszközre, lépjen a **parancsok** oldalra:

1. A parancsok listájában bontsa ki az elindítani kívánt parancsot.
1. Adja meg a parancshoz szükséges értékeket.
1. Tekintse át az eszközre küldendő hasznos adatokat.
1. Küldje el a parancsot.

#### <a name="telemetry"></a>Telemetria

A kiválasztott felület telemetria megtekintéséhez lépjen a **telemetria** lapra.

## <a name="next-steps"></a>További lépések

Ebben a útmutatóban megtanulta, hogyan telepítheti és használhatja az Azure IoT Explorer alkalmazást a IoT Plug and Play eszközeivel való interakcióhoz. A következő lépés azt ismerteti, hogyan [telepítheti és használhatja az Azure CLI-bővítményt](./howto-install-pnp-cli.md).
