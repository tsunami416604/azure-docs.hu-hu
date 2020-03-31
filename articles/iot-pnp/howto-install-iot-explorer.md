---
title: Az Azure IoT Explorer telepítése és használata | Microsoft dokumentumok
description: Telepítse az Azure IoT-kezelő eszközt, és használja az IoT Plug and Play preview-eszközök IoT hubhoz csatlakoztatott eszközekkel való interakcióhoz.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159183"
---
# <a name="install-and-use-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése és használata

Az Azure IoT Explorer egy grafikus eszköz az IoT Plug and Play előzetes eszközökkel való interakcióhoz és teszteléshez. Miután telepítette az eszközt a helyi számítógépre, csatlakoztathatja azt egy eszközhöz. Az eszköz segítségével megtekintheti az eszköz által küldött telemetriai adatokat, dolgozhat az eszköz tulajdonságaival és a hívási parancsokkal.

Ez a cikk a következőkhöz nyújt útmutatást:

- Telepítse és konfigurálja az Azure IoT explorer eszközt.
- Az eszközzel kommunikálhat az eszközökkel, és tesztelheti azeszközöket.

## <a name="prerequisites"></a>Előfeltételek

Az Azure IoT Explorer eszköz használatához a következőkre van szükség:

- Egy Azure IoT-központ. Számos módja van egy IoT-központ hozzáadása az Azure-előfizetéshez, például [egy IoT hub létrehozása az Azure CLI használatával.](../iot-hub/iot-hub-create-using-cli.md) Az Azure IoT Explorer eszköz futtatásához az IoT hub kapcsolati karakterláncra van szükség. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
- Az IoT hubon regisztrált eszköz. Az alábbi Azure CLI-paranccsal regisztrálhat egy eszközt. Ügyeljen arra, `{YourIoTHubName}` hogy `{YourDeviceID}` a helyőrzőket és helyőrzőket az értékeivel cserélje le:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése

Nyissa meg az [Azure IoT Explorer kiadásait,](https://github.com/Azure/azure-iot-explorer/releases) és bővítse ki az eszközök listáját a legutóbbi kiadáshoz. Töltse le és telepítse az alkalmazás legújabb verzióját.

## <a name="use-azure-iot-explorer"></a>Az Azure IoT Explorer használata

Egy eszköz höz csatlakoztathatja saját eszközét, vagy használhatja az egyik szimulált eszközünket. A szimulált eszközminta futtatásához kövesse az [alábbi utasításokat.](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples)

### <a name="connect-to-your-hub"></a>Csatlakozás a hubhoz

Az Azure IoT-kezelő első futtatásakor a rendszer kéri az IoT hub kapcsolati karakterláncát. A kapcsolati karakterlánc hozzáadása után válassza a **Csatlakozás lehetőséget.** Az eszköz beállításaival átválthat egy másik IoT-központra a kapcsolati karakterlánc frissítésével.

Az IoT Plug and Play-eszközök modelldefiníciója a nyilvános tárházban, a vállalati tárházban vagy a csatlakoztatott eszközben tárolódik. Alapértelmezés szerint az eszköz megkeresi a modelldefiníciót a nyilvános modelltárházban és a csatlakoztatott eszközben. Hozzáadhat és eltávolíthat forrásokat, vagy beállíthatja a források prioritását a **Beállítások ban:**

Forrás hozzáadása:

1. Nyissa meg a **Beállítások lapot.**
1. Válassza az **Új** lehetőséget, és válassza ki a forrást.
1. Ha a vállalati modell tárház, adja meg a kapcsolati karakterláncot.

Forrás eltávolítása:

1. Nyissa meg a **Beállítások lapot.**
1. Keresse meg az eltávolítani kívánt forrást.
1. Az eltávolításhoz válassza az **X** lehetőséget. A nyilvános modelltárház nem távolítható el, mert a közös felületdefiníciók ebből a tárházból származnak.

A forrásprioritások módosítása:

A modelldefiníciós források egyikét a lista egy másik rangsorolásába húzhatja. Ütközés esetén a magasabb rangsorú definíciós források felülbírálják az alacsonyabb rangsorú forrásokat.

### <a name="view-devices"></a>Eszközök megtekintése

Miután az eszköz csatlakozik az IoT hubhoz, megjeleníti az **Eszközök** listalapot, amely felsorolja az IoT hubregisztrált eszközidentitásokat. A lista bármely bejegyzését kibonthatja, hogy további információkat lásson.

Az **Eszközök** listalapon a következőket teheti:

- Válassza a **Hozzáadás** lehetőséget, ha új eszközt regisztrál a hubbal. Ezután adja meg az eszköz azonosítóját. Az alapértelmezett beállításokkal automatikusan létrehozhathitelesítési kulcsokat, és engedélyezheti a kapcsolatot a központtal.
- Jelöljön ki egy eszközt, majd a **Törlés gombra** az eszközidentitás törléséhez. Tekintse át az eszköz adatait a művelet befejezése előtt, és győződjön meg arról, hogy a megfelelő eszközidentitást teszi le.
- Lekérdezés `capabilityID` és `interfaceID`a szerint. Adja hozzá `capabilityID` `interfaceID` vagy paraméterként az eszközök lekérdezéséhez.

## <a name="interact-with-a-device"></a>Eszköz használata

Az **Eszközök** listalapon jelöljön ki egy értéket az **Eszközazonosító** oszlopban a regisztrált eszköz részletlapjának megtekintéséhez. Minden eszközhöz két rész re van szükség: **Device** and **Digital Twin**.

### <a name="device"></a>Eszköz

Ez a szakasz tartalmazza az **Eszközidentitás**, **Device Twin**, **Telemetriai,** **Közvetlen módszer** és **a felhőből az eszközre üzenet** fülek.

- Megtekintheti és frissítheti az [eszköz identitásadatait](../iot-hub/iot-hub-devguide-identity-registry.md) az **Eszköz identitása** lapon.
- Az eszköz [ikeradatait](../iot-hub/iot-hub-devguide-device-twins.md) az **Eszköz ikerlapján** érheti el.
- Ha egy eszköz csatlakoztatva van, és aktívan adatokat küld, megtekintheti a [telemetriai adatokat](../iot-hub/iot-hub-devguide-messages-read-builtin.md) a **Telemetriai** adatok lapon.
- A **Közvetlen metódus** lapon [közvetlen metódust](../iot-hub/iot-hub-devguide-direct-methods.md) hívhat meg az eszközön.
- A felhőből az [eszközre irányuló üzeneteket](../iot-hub/iot-hub-devguide-messages-c2d.md) a felhőből az **eszközre irányuló üzenetek** lapon küldheti el.

### <a name="digital-twin"></a>Digitális iker

Az eszköz segítségével megtekintheti az eszköz digitális ikerpéldányát. IoT Plug and Play eszköz esetén az eszköz képességmodelljéhez társított összes felület az eszköz ezen szakaszában jelenik meg. Válasszon ki egy felületet a megfelelő [IoT Plug and Play primitívek kibővítéséhez.](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)

### <a name="interface"></a>Felület

A **Felület** lapon megtekintheti a kapcsolat JSON-definícióját.

#### <a name="properties"></a>Tulajdonságok

A **nem írható tulajdonságok** lapon egy kapcsolatban definiált írásvédett tulajdonságok at tekintheti meg. Az **írható tulajdonságok** lapon frissítheti az illesztőfelületen definiált írható tulajdonságokat:

1. Nyissa meg az **Írható tulajdonságok** lapot.
1. Kattintson a frissíteni kívánt tulajdonságra.
1. Adja meg a tulajdonság új értékét.
1. Az eszközre küldendő hasznos adat előnézetének megtekintése.
1. Küldje el a módosítást.

A módosítás elküldése után nyomon követheti a frissítés állapotát: **szinkronizálás,** **sikeres**vagy **hiba**. Amikor a szinkronizálás befejeződött, a tulajdonság új értéke a **Jelentett tulajdonság** oszlopban jelenik meg. Ha a szinkronizálás befejezése előtt más oldalakra navigál, az eszköz a frissítés befejezésekor is értesíti. Az eszköz értesítési központját is használhatja az értesítési előzmények megtekintéséhez.

#### <a name="commands"></a>Parancsok

Ha parancsot szeretne küldeni egy eszközre, lépjen a **Parancsok** lapra:

1. A parancsok listájában bontsa ki az aktiválni kívánt parancsot.
1. Adja meg a parancs szükséges értékeit.
1. Az eszközre küldendő hasznos adat előnézetének megtekintése.
1. Küldje el a parancsot.

#### <a name="telemetry"></a>Telemetria

A kijelölt felület telemetriai adatainak megtekintéséhez lépjen a **Telemetriai** lapra.

## <a name="next-steps"></a>További lépések

Ebben az útmutató cikkben megtanulta, hogyan telepítheti és használhatja az Azure IoT Explorert az IoT Plug and Play-eszközök kel való interakcióhoz. A javasolt következő lépés az [Azure CLI-bővítmény telepítésének és használatának megismerése.](./howto-install-pnp-cli.md)
