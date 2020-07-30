---
title: Újdonságok IoT Plug and Play előzetes verziójának frissítése | Microsoft Docs
description: Ismerkedjen meg a IoT Plug and Play előzetes verziójának frissítésével kapcsolatos újdonságokkal.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: f212143d90b6a6bf9c8f1f597c276b9ab4100617
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406260"
---
# <a name="iot-plug-and-play-preview-refresh"></a>IoT Plug and Play előzetes verziójának frissítése

Ez a cikk a Plug and Play IoT SDK-k, kódtárak, eszközök és szolgáltatások főbb változásait ismerteti a 2020-es frissítés kiadásában. Az előző IoT Plug and Play előzetes verziójának kiadása 2019 augusztusában volt.

## <a name="digital-twins-definition-language-dtdl"></a>Digitális Twins-definíciós nyelv (DTDL)

Ez a kiadás a [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) támogatását és a [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview)elavulttá teszi.

Az alábbi lista a DTDL v1 és a DTDL v2 közötti főbb különbségeket mutatja be. A DTDL v2-ben:

- A modell azonosítói a helyett előtaggal rendelkeznek `dtmi` `urn` . A digitális kettős modell azonosítói (DTMI) lecserélik a `urn` DTDL v1-ben használt előre rögzített digitális kettős azonosítókat. A verzió már megelőzi a-t `;` . Például, ha korábban már használta, használja a következőt: `urn:CompanyName:Model:1` `dtmi:CompanyName:Model;1` .
- Állítsa a értéket a `@context` `dtmi:dtdl:context;2` helyett `http://azureiot.com/v1/contexts/IoTModel.json` .
- Az eszköz modellezéséhez használja a **CapabilityModel** típus helyett az **illesztőfelület** típusát.
- Az **összetevők** lecserélik a **felületi** példányokat. A **kapcsolatok** és az **összetevők** megadhatók egy **felület**tartalmának részeként.
- Egy **felület** örökölhető egy másik **illesztőfelülettől**.
- A DTDL _bővíthető szemantikai típusok_használatával bővíthető. Ez a bővíthető típusrendszer nagyobb rugalmasságot biztosít, mint a nehezen kódolt szemantikai típusok, például a hőmérséklet és a páratartalom a DTDL v1-ben.
- A **displayUnit** tulajdonság el lett távolítva.
- Nem használhatja a kezdő vagy záró aláhúzást a névben. A névben vezető aláhúzások a rendszer számára vannak fenntartva.

A DTDL v1 használatához az SDK és az Azure IoT Explorer 0,10. x verziójának korábbi verzióját kell használnia. A DTDL v2-mel való együttműködéshez szüksége lesz az SDK és az Azure IoT Explorer 0,11. x legújabb verziójára.

## <a name="no-component-and-multiple-component-implementations"></a>Nincs összetevő és több összetevő-implementáció

Néhány telemetria, parancsot vagy tulajdonságot használó egyszerű eszköz az összetevők használata nélkül egyetlen felületen is ismertethető. A meglévő eszközök IoT válhatnak Plug and Play a **modell azonosítójának** bejelentésével anélkül, hogy a meglévő eszköz implementációja módosul.

Az összetettebb eszközök különböző felületeken telemetria, parancsokat és tulajdonságokat kezelhetnek, így kezelheti a bonyolultságot, és engedélyezheti az eszközökön való használatot. Ezeket az eszközöket frissíteni kell, hogy kövessék a [IoT Plug and Play előnézeti üzenetekben](concepts-convention.md)definiált egyszerű szabályok készletét.

## <a name="registration-and-discovery"></a>Regisztráció és felderítés

Ebben a kiadásban az eszközök bejelentik a **modell azonosítóját** IoT hub minden kapcsolatban. IoT Hub gyorsítótárazza a **modell azonosítóját** , amely lehetővé teszi a háttér-megoldások számára a **modell azonosítójának** lekérését a Device Twin `modelId` tulajdonság használatával. A **modell azonosítóját** a rendszer a digitális Twin-ből is beolvashatja `$metadata.$model` .

## <a name="microsoft-defined-interfaces"></a>Microsoft által definiált felületek

A következő Microsoft által definiált felületek elavultak, és nem jelennek meg az új modell adattárában:

- **urn: azureiot: ModelDiscovery: DigitalTwin: 1**
- **urn: azureiot: ModelDiscovery: ModelInformation: 1**

Az új modell adattárában az alábbi felület jelenik meg: `dtmi:azure:DeviceManagement:DeviceInformation;1` elérhető az URL-címen [ https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation ; 1? API-Version = 2020-05 -01-előzetes](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview)verzió.

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

A **DigitalTwinChangeEvents** - [eseményforrás](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) esemény-szerkezete a **JSON-patch** formátum használatára módosult. Ez a változás egy visszamenőleges kompatibilitási támogatás nélküli megszakítási változás.

## <a name="message-routing"></a>Üzenetek útválasztása

A telemetria-üzenetek a következő módosításokat hajtják végre a [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md) -gyűjteményen belül.

Most már tartalmaz egy **DT-DataSchema** tulajdonságot is, amely az eszköz által regisztrált **modell azonosítója** .

A **DT-subject** tulajdonság a telemetria üzenetet küldő összetevőt jelöli.

A **iothub-Interface-Name** tulajdonság elavult.

## <a name="device-and-service-sdks"></a>Eszköz és szolgáltatás SDK-k

Nincs visszamenőleges kompatibilitás az SDK-k korábbi előzetes verzióival. Ha az SDK legújabb előzetes verziójára vált, módosítania kell a kódot.

Az egyezményen alapuló megközelítés esetében nincs szükség külön eszköz ügyféloldali SDK-k kiválasztására. Ebből az előzetes kiadásból a meglévő **DigitalTwinClient** -kódtárak minden nyelven elavultak. Ehelyett a IoT Hub-eszköz ügyféloldali SDK-k frissültek, hogy a **modell azonosítójának**bejelentését is tartalmazzák.

Azok az eszközök, amelyek nem használnak összetevőket, minimális programkód-módosításokat igényelnek – csak bejelentik a **modell azonosítóját**. A több összetevőt használó összetettebb eszközök esetében előfordulhat, hogy az [egyezmények](concepts-convention.md)megvalósításához újrafelhasználható függvények szükségesek. Az eszközök mintái többek között az eszköz implementációjában felhasználható függvények készletét tartalmazzák.

### <a name="service-sdks"></a>Szolgáltatási SDK-k

A Service SDK [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) és [Python](https://github.com/Azure/azure-iot-sdk-python/blob/digitaltwins-preview/azure-iot-hub/README.md)nyelven érhető el.

## <a name="vs-code-extension"></a>VS Code-bővítmény

Az [Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) bővítmény szerzői támogatást biztosít a DTDL V1-hez, a modell adattárának korábbi verziójához való integrációhoz és a kód generálásához.

Ha a VS Code-ban DTDL v2 authoring támogatásra van szüksége, telepítse az új [DTDL-bővítményt](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) a vs Code-ban. A bővítmény nem biztosít integrációt a modell adattárával vagy a kód generálásával. Mostantól [webes felhasználói felületen](https://aka.ms/iotmodelrepo)keresztül végezheti el a modellek kezelését a tárházban.

## <a name="digital-twin-service-side-rest-apis"></a>Digitális dupla szolgáltatási oldali REST API-k

A [Digital Twin szolgáltatás-oldali REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) -k és a hasznos adatok módosultak. A támogatott API-k a következők:

- Digitális iker beolvasása.
- Hívja meg a parancsot.
- A digitális iker frissítése a **JSON-patch** adattartalommal.

Ebben a kiadásban a meglévő REST API-k továbbra is támogatottak.

## <a name="model-repository"></a>Modelladattár

Most már létezik egy, a nyilvános és a RBAC által védett céges modelleket is tartalmazó modell tárház. Minden modell egyedi azonosítóval rendelkezik, és a létrehozása után nem változtathatók meg.

Ebben a kiadásban nem támogatottak az előző kiadásban meglévő vállalati modell adattárházak. Továbbra is használhatja az [Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com/products) webhelyet a régi DTDL v1-modellek kezeléséhez. Ezt a webhelyet azonban már nem használhatja az eszközök regisztrálásához, teszteléséhez és hitelesítéséhez.

Az Azure CLI-hez készült Azure IoT-bővítmény nem támogatja az új modell tárházát. A `az iot pnp` parancsok csak a modell adattárait működnek az előző kiadásból.

## <a name="azure-iot-central"></a>Azure IoT Central

Az Azure IoT Central jelenleg frissítés alatt áll, hogy támogassa a IoT Plug and Play előzetes verziójának frissítését.
