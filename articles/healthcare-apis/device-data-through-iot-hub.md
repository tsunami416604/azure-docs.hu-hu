---
title: 'Oktatóanyag: eszközre vonatkozó adatfogadás az Azure IoT Hub'
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti az eszköz adattovábbítását IoT Hubból az Azure API-ba a FHIR-n keresztül a IoT-összekötőn keresztül.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 06/27/2020
ms.author: punagpal
ms.openlocfilehash: d606cd4f5c4b901c060c97d73524997b94eaf225
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446302"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Oktatóanyag: eszközre vonatkozó adatfogadás az Azure IoT Hub

Az IoT-összekötő lehetővé teszi, hogy az orvosi eszközök internetes hálózatáról (IoMT) származó adatokból betöltse az Azure API-t a FHIR-be. Az [IoT-összekötő üzembe helyezése (előzetes verzió) Azure Portal](iot-fhir-portal-quickstart.md) rövid útmutatóval az Azure által felügyelt eszközökre mutat példát, IoT Central a telemetria IoT-összekötőnek való [küldésére](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) . Az IoT-összekötő az Azure IoT Hub használatával kiosztott és felügyelt eszközökkel is használható. Ez az oktatóanyag azt az eljárást ismerteti, amellyel az Azure IoT Hubról a IoT-összekötőhöz csatlakoztathatja és irányíthatja az eszközöket.

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API FHIR-erőforráshoz legalább egy IoT-összekötővel – a [IoT-összekötő (előzetes verzió) üzembe helyezése Azure Portal használatával](iot-fhir-portal-quickstart.md)
- Azure IoT Hub-erőforrás valós vagy szimulált eszközhöz csatlakoztatva – [IoT hub létrehozása a Azure Portal használatával](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Ha Azure IoT Hub szimulált eszköz alkalmazást használ, a különböző támogatott nyelvek és rendszerek közül választhat.

## <a name="get-connection-string-for-iot-connector-preview"></a>IoT-összekötő csatlakozási karakterláncának beolvasása (előzetes verzió)

Az Azure IoT Hub kapcsolati karakterláncot igényel a IoT-összekötővel való biztonságos csatlakozáshoz. Hozzon létre egy új csatlakozási karakterláncot a IoT-összekötőhöz a következő témakörben leírtak szerint: Create a Reporting [String](iot-fhir-portal-quickstart.md#generate-a-connection-string) Őrizze meg ezt a kapcsolódási karakterláncot a következő lépésben való használatra.

Az IoT-összekötő egy Azure Event hub-példányt használ a motorháztető alatt az eszköz üzeneteinek fogadásához. A fent létrehozott kapcsolati sztring alapvetően a kapcsolati sztring ehhez a mögöttes Event hub-hoz.

## <a name="connect-azure-iot-hub-with-the-iot-connector-preview"></a>Az Azure IoT Hub összekötése a IoT-összekötővel (előzetes verzió)

Az Azure IoT Hub támogatja az [üzenet-útválasztás](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) nevű szolgáltatást, amely lehetővé teszi az eszközre vonatkozó adatküldés lehetőségét különböző Azure-szolgáltatások, például az Event hub, a Storage-fiók és a Service Bus számára. Az IoT-összekötő ezt a funkciót használja az Azure IoT Hubról az Event hub-végpontra való csatlakozáshoz és az eszköz adatainak küldéséhez.

> [!NOTE] 
> Jelenleg csak a PowerShell vagy a CLI parancs használható az üzenet- [Útválasztás létrehozásához](https://docs.microsoft.com/azure/iot-hub/tutorial-routing) , mert az IoT-összekötő Event hub-je nem az ügyfél-előfizetésen fut, ezért nem lesz látható a Azure Portalon keresztül. Bár ha a PowerShell vagy a parancssori felület használatával hozzáadja az üzenet-útválasztási objektumokat, azok láthatók lesznek a Azure Portalon, és ott is kezelhetők.

Az üzenetek útválasztásának beállítása két lépésből áll.

### <a name="add-an-endpoint"></a>Végpont hozzáadása
Ez a lépés egy olyan végpontot határoz meg, amelybe a IoT Hub átirányítja az adattovábbítást. Hozza létre ezt a végpontot az [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell [-paranccsal vagy az IOT hub Routing-Endpoint Create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) CLI parancs használatával a beállítás alapján.

Itt látható a végpont létrehozásához a paranccsal használandó paraméterek listája:

|PowerShell-paraméter|CLI-paraméter|Leírás|
|---|---|---|
|ResourceGroupName|resource-group|Az IoT Hub erőforrás erőforráscsoport-neve.|
|Name|hub neve|A IoT Hub erőforrás neve.|
|Végpontneve|végpont neve|Használjon olyan nevet, amelyet hozzá szeretne rendelni a létrehozandó végponthoz.|
|EndpointType|végpont típusa|Annak a végpontnak a típusa, amelyhez IoT Hub csatlakoznia kell. Használja a "EventHub" konstans értékét a PowerShell és a "EventHub" számára a CLI-hez.|
|EndpointResourceGroup|végpont – erőforrás-csoport|Erőforráscsoport neve a IoT-összekötő Azure API-hoz a FHIR-erőforráshoz. Ezt az értéket a FHIR készült Azure API áttekintés lapján érheti el.|
|EndpointSubscriptionId|végpont – előfizetés-azonosító|Az IoT-összekötő Azure API-FHIR-erőforráshoz tartozó előfizetés-azonosítója. Ezt az értéket a FHIR készült Azure API áttekintés lapján érheti el.|
|ConnectionString|connection-string|A IoT-összekötőhöz tartozó kapcsolódási karakterlánc. Használja az előző lépésben beszerzett értéket.|

### <a name="add-a-message-route"></a>Üzenet útvonalának hozzáadása
Ez a lépés egy üzenet útvonalát határozza meg a fent létrehozott végpont használatával. Hozzon létre egy útvonalat az [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell-paranccsal vagy [az IOT hub Route Create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) CLI parancs használatával a beállítás alapján.

Itt látható a végpont létrehozásához a paranccsal használandó paraméterek listája:

|PowerShell-paraméter|CLI-paraméter|Leírás|
|---|---|---|
|ResourceGroupName|g|Az IoT Hub erőforrás erőforráscsoport-neve.|
|Name|hub neve|A IoT Hub erőforrás neve.|
|Végpontneve|végpont neve|A fent létrehozott végpont neve.|
|Z|útvonal neve|A létrehozandó üzenet-útvonalhoz hozzárendelni kívánt név.|
|Forrás|forrás típusa|A végpontnak küldendő adattípusok. Használja a "DeviceMessages" konstans értékét a PowerShell és a "DeviceMessages" számára a CLI-hez.|

## <a name="send-device-message-to-iot-hub"></a>Eszköz üzenet küldése IoT Hub

Az eszköz (valós vagy szimulált) használatával elküldheti az alábbi minta szívfrekvencia-üzenetet az Azure IoT Hubnak. Ez az üzenet a IoT-összekötőre lesz átirányítva, ahol az üzenet egy FHIR megfigyelési erőforrásba lesz átalakítva, és a FHIR Azure API-ba kerül.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Ügyeljen arra, hogy a IoT-összekötővel konfigurált [leképezési sablonoknak](iot-mapping-templates.md) megfelelő üzenetet küldjön.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Eszközbeállítások megtekintése a FHIR készült Azure API-ban

A IoT-összekötő által létrehozott FHIR-megfigyelési erőforrás (oka) t a Poster használatával tekintheti meg a FHIR készült Azure API-ban. Állítsa be a [Poster-t, hogy hozzáférjen az Azure API-hoz a FHIR-hez](access-fhir-postman-tutorial.md) , és `GET` kérést küldjön a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` megfigyelési FHIR-erőforrások megtekintésére a fenti mintában elküldött pulzusszám értékkel.

> [!TIP]
> Győződjön meg arról, hogy a felhasználó megfelelő hozzáféréssel rendelkezik az Azure API-hoz a FHIR adatsíkon. Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](configure-azure-rbac.md) használatával rendelje hozzá a szükséges adatsíkok-szerepköröket.


## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállíthatja, hogy az Azure IoT Hub az IoT-összekötőre irányítsa az eszközöket. Az IoT-összekötővel kapcsolatos további tudnivalókért válasszon a következő lépések közül:

Az IoT-összekötőn belüli adatáramlás különböző szakaszainak megismerése.

>[!div class="nextstepaction"]
>[IoT-összekötő adatfolyama](iot-data-flow.md)

Ismerje meg, hogyan konfigurálhatja az IoT-összekötőt eszköz-és FHIR-leképezési sablonok használatával.

>[!div class="nextstepaction"]
>[IoT-összekötő leképezési sablonjai](iot-mapping-templates.md)

Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.
