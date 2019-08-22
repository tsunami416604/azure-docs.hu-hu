---
title: Az Azure IoT Explorer telepítése és használata | Microsoft Docs
description: Telepítse az Azure IoT Explorer eszközt, és használja a IoT Plug and Play előnézeti eszközökhöz csatlakoztatott IoT-hubhoz való interakcióhoz.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c7d8b946fc587d58f13e16c1d7d806390e712104
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879551"
---
# <a name="install-and-use-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése és használata

Az Azure IoT Explorer egy grafikus eszköz a IoT Plug and Play előnézeti eszközökhöz való interakcióhoz és teszteléséhez. Miután telepítette az eszközt a helyi gépre, használhatja azt egy eszközhöz való kapcsolódáshoz. Az eszköz segítségével megtekintheti az eszköz által küldött telemetria, az eszköz tulajdonságait és a hívási parancsokat.

Ez a cikk bemutatja, hogyan végezheti el a következőket:

- Telepítse és konfigurálja az Azure IoT Explorer eszközt.
- Használja az eszközt az eszközök használatához és teszteléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az Azure IoT Explorer eszköz használatához a következőkre lesz szüksége:

- Egy Azure IoT hub. Az Azure-előfizetéshez többféleképpen is hozzáadhat egy IoT hubot, például [egy IoT hub létrehozását az Azure CLI használatával](../iot-hub/iot-hub-create-using-cli.md). Az Azure IoT Explorer eszköz futtatásához szüksége lesz az IoT hub kapcsolódási karakterláncára. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Az IoT hub-ban regisztrált eszköz. Egy eszköz regisztrálásához használja az alábbi Azure CLI-parancsot. Ügyeljen rá, hogy a `{YourIoTHubName}` és `{YourDeviceID}` a helyőrzőket cserélje le az értékekre:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése

Nyissa meg az [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) kiadásait, és bontsa ki a legújabb kiadáshoz tartozó eszközök listáját. Töltse le és telepítse az alkalmazás legújabb verzióját.

## <a name="use-azure-iot-explorer"></a>Az Azure IoT Explorer használata

Egy eszközhöz csatlakozhat a saját eszközéhez, vagy használhatja a mintául szolgáló szimulált eszközök egyikét is. Kövesse [ezeket az utasításokat](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) a szimulált eszköz mintájának futtatásához.

### <a name="connect-to-your-hub"></a>Kapcsolódás a hubhoz

Az Azure IoT Explorer első futtatásakor a rendszer a IoT hub csatlakozási karakterláncát kéri. A kapcsolati karakterlánc hozzáadása után válassza akapcsolat lehetőséget. Az eszköz beállításai segítségével másik IoT-hubhoz válthat a kapcsolódási karakterlánc frissítésével.

Egy IoT Plug and Play eszköz modell-definícióját a rendszer a nyilvános tárházban, a vállalati tárházban vagy a csatlakoztatott eszközön tárolja. Alapértelmezés szerint az eszköz a nyilvános modell adattárában és a csatlakoztatott eszközön keresi a modell definícióját. Hozzáadhat és eltávolíthat forrásokat, vagy konfigurálhatja a források prioritását a beállításokban:

Forrás hozzáadása:

1. Lépjen a **beállítások**.
1. Válassza az **új** lehetőséget, és válassza ki a forrást.
1. Ha hozzáadja a vállalati modell tárházát, adja meg a kapcsolatok karakterláncát.

Forrás eltávolítása:

1. Lépjen a **beállítások**.
1. Keresse meg az eltávolítani kívánt forrást.
1. Válassza az **X** lehetőséget az eltávolításához. A nyilvános modell tárháza nem távolítható el, mert az általános illesztőfelület-definíciók ebből a tárházból származnak.

A forrás prioritásának módosítása:

A modell definíciós forrásainak egyikét áthúzással áthelyezheti egy másik rangsorba a listában. Ha ütközés lép fel, a magasabb prioritású definíciós források felülbírálják az alacsonyabb rangsorú forrásokat.

### <a name="overview-page"></a>Áttekintő lap

#### <a name="device-overview"></a>Eszközök áttekintése

Miután az eszköz csatlakozik az IoT hubhoz, megjelenik egy áttekintő oldal, amely felsorolja az Azure IoT hub-ban regisztrált összes eszköz identitását. Válasszon ki egy eszközt a további részletek megtekintéséhez.

#### <a name="device-management"></a>Eszközkezelés

- Ha új eszközt szeretne regisztrálni a hubhoz, válassza a **Hozzáadás**lehetőséget. Adja meg az eszköz AZONOSÍTÓját. Az alapértelmezett beállítások használatával automatikusan generálhatja a hitelesítési kulcsokat, és engedélyezheti a kapcsolódást a hubhoz.
- Az eszköz identitásának törléséhez válassza a **Törlés**lehetőséget. A művelet elvégzése előtt tekintse át az eszköz részleteit, és győződjön meg róla, hogy a megfelelő eszköz identitását törli.
- Az eszköz támogatja a `capabilityID` és `interfaceID`a lekérdezését. Adja hozzá a `capabilityID` vagy `interfaceID` az paramétert az eszközök lekérdezéséhez.

## <a name="interact-with-a-device"></a>Eszköz használata

Kattintson duplán az eszközre az Áttekintés lapon a részletek következő szintjének megtekintéséhez. Két részből áll: **Eszköz** -és **digitális Twin**.

### <a name="device"></a>Eszköz

Ez a szakasz tartalmazza az **eszköz identitását**, a **telemetria**és az **eszköz két** lapját.

- Az Eszközállapot lapon megtekintheti és frissítheti az eszköz identitásának adatait.
- Ha egy eszköz csatlakoztatva van, és aktívan küld adatokat, megtekintheti a telemetria a **telemetria** lapon.
- A Device twin (eszközök Twin) lapon érheti el az eszköz két adatát.

### <a name="digital-twin"></a>Digitális Twin

Az eszköz az eszköz digitális dupla példányára is használható. A IoT Plug and Play eszköz esetében ebben a cikkben megjelenik az eszköz-képesség modellhez társított összes interfész. Válasszon egy felületet a megfelelő [IoT Plug and Play primitívek](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)kibontásához.

#### <a name="properties"></a>properties

A **tulajdonságlapon** megtekintheti a kezelőfelületen megadott írásvédett tulajdonságokat. A csatolóban definiált írható tulajdonságokat az **írható tulajdonságok** lapon frissítheti.

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

Ebben a útmutatóban megtanulta, hogyan telepítheti és használhatja az Azure IoT Explorer alkalmazást a IoT Plug and Play eszközeivel való interakcióhoz. Egy javasolt következő lépés a [modellek kezelésének](./howto-manage-models.md)megismerése.
