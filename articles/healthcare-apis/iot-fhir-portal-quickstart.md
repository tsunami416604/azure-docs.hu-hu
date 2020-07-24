---
title: 'Rövid útmutató: a IoT-összekötő (előzetes verzió) üzembe helyezése Azure Portal használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe, konfigurálhatja és használhatja a FHIR készült Azure API IoT-összekötő funkcióját a Azure Portal használatával.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: punagpal
ms.openlocfilehash: bc85765666ba3baeae7ec795118f615f6b4b4368
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102191"
---
# <a name="quickstart-deploy-iot-connector-preview-using-azure-portal"></a>Rövid útmutató: a IoT-összekötő (előzetes verzió) üzembe helyezése Azure Portal használatával

Az IoT Connector az Azure API választható funkciója, amely lehetővé teszi az orvosi eszközök internetes FHIR (IoMT) származó adatok betöltését. Az előzetes verzió ideje alatt az IoT Connector szolgáltatás ingyenesen elérhetővé válik. Ennek a rövid útmutatónak a segítségével megtanulhatja a következőket:
- A IoT-összekötő üzembe helyezése és konfigurálása a Azure Portal használatával
- Az IoT-összekötőnek küldött, szimulált eszköz használata
- Az IoT Connector által létrehozott erőforrások megtekintése a FHIR készült Azure API-ban

## <a name="prerequisites"></a>Előfeltételek

- Aktív Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API FHIR-erőforráshoz – [Az Azure API üzembe helyezése a FHIR-hez a Azure Portal használatával](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Ugrás az Azure API FHIR-erőforráshoz

Nyissa meg a [Azure Portalt](https://portal.azure.com) , és lépjen az **Azure API for FHIR** erőforráshoz, amelyhez létre szeretné hozni az IoT-összekötő szolgáltatást.

[![Azure API FHIR-erőforráshoz](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

A bal oldali navigációs menüben kattintson a **IoT-összekötő (előzetes verzió)** elemre a **bővítmények** szakaszban a IoT- **Összekötők** lap megnyitásához.

[![IoT-összekötő funkció](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-iot-connector-preview"></a>Új IoT-összekötő létrehozása (előzetes verzió)

Kattintson a **Hozzáadás** gombra az **IoT-összekötő létrehozása** lap megnyitásához.

[![IoT-összekötő hozzáadása](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Adja meg az új IoT-összekötő beállításait. Kattintson a **Létrehozás** gombra, és várja meg az IoT-összekötő üzembe helyezését.

> [!NOTE]
> Válassza a **Létrehozás** lehetőséget a **megoldás típusa** legördülő listából a telepítéshez. 

[![IoT-összekötő létrehozása](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Beállítás|Érték|Leírás |
|---|---|---|
|Összekötő neve|Egyedi név|Adjon meg egy nevet az IoT-összekötő azonosításához. Ennek a névnek egyedinek kell lennie egy Azure API-n belül a FHIR-erőforráshoz. A név csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Betűvel vagy számmal kell kezdődnie és végződnie, és a hosszának 3-24 karakter közöttinek kell lennie.|
|Megoldás típusa|Keresés vagy létrehozás|Válassza a **lookup (keresés** ) lehetőséget, ha sávon kívüli folyamattal hozza létre az [eszköz](https://www.hl7.org/fhir/device.html) és a [PÁCIENS](https://www.hl7.org/fhir/patient.html) FHIR erőforrásait a FHIR készült Azure API-ban. Az IoT-összekötő ezeket az erőforrásokat fogja használni, amikor [megfigyelési](https://www.hl7.org/fhir/observation.html) FHIR-erőforrást hoz létre az eszközre vonatkozó információk ábrázolásához. Válassza a **Létrehozás** lehetőséget, ha azt szeretné, hogy a IoT-összekötő a saját Azure API-ban hozzon létre operációs rendszer nélküli eszközt és beteg erőforrásokat az FHIR lévő megfelelő azonosító értékek használatával.|

A telepítés befejezése után az újonnan létrehozott IoT-összekötő megjelenik az **IoT-összekötők** lapon.

[![IoT-összekötő létrehozva](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-iot-connector-preview"></a>IoT-összekötő konfigurálása (előzetes verzió)

Az IoT-összekötőnek két leképezési sablonra van szüksége az eszköz üzeneteinek FHIR-alapú megfigyelési erőforrás (ok) ra való átalakításához: **eszköz-hozzárendelés** és **FHIR-leképezés**. A IoT-összekötő nem teljesen működőképes, amíg fel nem töltődik a leképezések.

[![IoT-összekötő hiányzó leképezései](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

A leképezési sablonok feltöltéséhez kattintson az újonnan telepített IoT-összekötőre, és lépjen a **IoT-összekötő** lapra.

[![IoT-összekötő kattintson](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Eszköz-hozzárendelés

Az eszköz-hozzárendelési sablon egy normalizált sémába alakítja át az eszközöket. Az **IoT-összekötő** lapon kattintson az **eszköz-hozzárendelés konfigurálása** gombra az **eszköz-hozzárendelés** lapra való ugráshoz. 

[![IoT-összekötő kattintson az eszköz-hozzárendelés konfigurálása elemre.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Az **eszköz-hozzárendelés** lapon adja hozzá a következő parancsfájlt a JSON-szerkesztőhöz, és kattintson a **Mentés**gombra.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![IoT-összekötő eszközének leképezése](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)


#### <a name="fhir-mapping"></a>FHIR leképezése

A FHIR-leképezési sablon normalizált üzenetet alakít át egy FHIR-alapú megfigyelési erőforrásra. Az **IoT-összekötő** lapon kattintson a **FHIR-leképezés konfigurálása** gombra, hogy a **FHIR-leképezési** lapra ugorjon.  

[![IoT-összekötő kattintson a FHIR-leképezés konfigurálása elemre.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

A **FHIR-leképezés** lapon adja hozzá a következő parancsfájlt a JSON-szerkesztőhöz, és kattintson a **Mentés**gombra.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![IoT-összekötő FHIR leképezése](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Kapcsolati sztring létrehozása

A IoMT-eszköznek kapcsolati sztringre van szüksége az IoT-összekötőhöz való csatlakozáshoz és üzenetek küldéséhez. Az újonnan telepített IoT-összekötő **IoT-összekötő** lapján válassza az **Ügyfélkapcsolatok kezelése** gombot. 

[![IoT-összekötő kattintson az Ügyfélkapcsolatok kezelése elemre.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

A **kapcsolatok** lapon kattintson a **Hozzáadás** gombra egy új kapcsolat létrehozásához. 

[![IoT-összekötő kapcsolatai](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Adja meg a kapcsolatok rövid nevét az átfedések ablakban, és kattintson a **Létrehozás** gombra.

[![IoT-összekötő új kapcsolatai](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Válassza ki az újonnan létrehozott kapcsolatot a **kapcsolatok** lapon, és másolja az **elsődleges kapcsolati sztring** mező értékét a jobb oldali átfedési ablakból.

[![IoT-összekötő csatlakozási karakterlánca](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

A kapcsolódási karakterlánc megőrzése egy későbbi lépésben való használatra. 

## <a name="connect-your-devices-to-iot"></a>Eszközök csatlakoztatása a IoT-hez

Az Azure a IoT-termékek széles választékát kínálja a IoT-eszközök csatlakoztatásához és kezeléséhez. Az Azure IoT Hub használatával saját megoldását is létrehozhatja, vagy elindíthatja a IoT-alkalmazások kezelése platformot az Azure IoT Central segítségével. Ebben az oktatóanyagban kihasználjuk az Azure IoT Central-t, amely az iparági környezettel foglalkozó megoldási sablonokkal segíti az első lépéseket.

Telepítse a [folyamatos beteg monitorozási alkalmazás sablonját](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template). Ez a sablon két szimulált eszközt tartalmaz, amelyek valós idejű adatfeldolgozást biztosítanak az első lépések megtételéhez: **intelligens vitális javítás** és **intelligens térd zárójel**.

> [!NOTE]
> Ha a valódi eszközök készen állnak, ugyanazt a IoT Central alkalmazást használhatja az [eszközök](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template) bevezetéséhez és az eszköz-szimulátorok cseréjéhez. Az eszköz adatai automatikusan FHIR is áramlanak. 

## <a name="connect-your-iot-data-with-the-iot-connector-preview"></a>Csatlakoztassa a IoT-adatait a IoT-összekötővel (előzetes verzió)
Miután telepítette a IoT Central alkalmazást, a két beépített szimulált eszköz elkezdi a telemetria generálását. Ebben az oktatóanyagban betöltjük a telemetria a *Smart vitals patch* Simulator-ből a FHIR-be a IoT-összekötőn keresztül. Ha a IoT-adatait a IoT-Összekötőbe szeretné exportálni, a [folyamatos adatexportálást IoT Centralon belül szeretnénk beállítani](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export). A folyamatos adatexportálás lapon:
- Válassza az *Azure Event Hubs* exportálás célhelyként.
- Válassza a **Event Hubs névtér** mezőhöz tartozó *kapcsolatok karakterláncának használata* elemet.
- Adja meg a IoT-összekötő azon csatlakozási karakterláncát, amely az előző lépésben lett beolvasva a **Csatlakozási karakterlánc** mezőhöz.
- Tartsa *meg a* **telemetria** beállítást az **exportálandó** adatmezőhöz.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Eszközbeállítások megtekintése a FHIR készült Azure API-ban

A IoT-összekötő által létrehozott FHIR-alapú megfigyelési erőforrás (oka) t a Poster használatával tekintheti meg a FHIR készült Azure API-ban. Állítsa be a [Poster-t, hogy hozzáférjen az Azure API-hoz a FHIR-hez](access-fhir-postman-tutorial.md) , és `GET` kérjen kérelmet a `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` megfigyelési FHIR erőforrásainak a pulzusszám értékkel való megtekintéséhez. 

> [!TIP]
> Győződjön meg arról, hogy a felhasználó megfelelő hozzáféréssel rendelkezik az Azure API-hoz a FHIR adatsíkon. Az [Azure szerepköralapú Access Control](configure-azure-rbac.md) használatával rendelje hozzá a szükséges adatsíkok-szerepköröket.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, törölheti az IoT-összekötő egy példányát a társított erőforráscsoport, vagy a FHIR szolgáltatáshoz társított Azure API vagy a IoT-összekötő példányának eltávolításával. 

Ha közvetlenül el szeretné távolítani egy IoT-összekötő példányát, válassza ki a példányt a **IoT** -összekötők lapról, és lépjen a **IoT-összekötő** lapra, és kattintson a **Törlés** gombra. Ha a rendszer megerősítést kér, válassza az **Igen** lehetőséget. 

[![IoT-összekötő példányának törlése](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezte a IoT Connector szolgáltatást a FHIR-erőforráshoz készült Azure API-ban. Az IoT-összekötővel kapcsolatos további tudnivalókért válasszon a következő lépések közül:

Az IoT-összekötőn belüli adatáramlás különböző szakaszainak megismerése.

>[!div class="nextstepaction"]
>[IoT-összekötő adatfolyama](iot-data-flow.md)

Ismerje meg, hogyan konfigurálhatja az IoT-összekötőt eszköz-és FHIR-leképezési sablonok használatával.

>[!div class="nextstepaction"]
>[IoT-összekötő leképezési sablonjai](iot-mapping-templates.md)

Az FHIR a HL7 bejegyzett védjegye, amelynek felhasználását a HL7 engedélyezte.
