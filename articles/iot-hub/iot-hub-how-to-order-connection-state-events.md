---
title: Eszközkapcsolati események megrendelése fr Azure IoT Hub w/Azure Cosmos DB
description: Ez a cikk bemutatja, hogyan rendelhet és rögzíthet eszközkapcsolati eseményeket az Azure IoT Hubról az Azure Cosmos DB használatával a legújabb kapcsolatállapot fenntartása érdekében
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: 210c2e74305ba99b4ac3a12625d0b7f5fc47ba43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954253"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Eszközkapcsolati események megrendelése az Azure IoT Hubról az Azure Cosmos DB használatával

Az Azure Event Grid segítségével eseményalapú alkalmazásokat hozhat létre, és egyszerűen integrálhatja az IoT-eseményeket az üzleti megoldásokba. Ez a cikk végigvezeti a beállításokat, amely nyomon követheti és tárolhatja a legújabb eszköz kapcsolati állapotát a Cosmos DB-ben. A csatlakoztatott és az eszközkapcsolattal leválasztott eseményekben elérhető sorszámot fogjuk használni, és a legújabb állapotot a Cosmos DB-ben tároljuk. Egy tárolt eljárást fogunk használni, amely egy alkalmazáslogika, amely a Cosmos DB-ben egy gyűjtemény ellen hajtódik végre.

A sorszám egy hexadecimális szám karakterlánc-ábrázolása. A karakterlánc-összehasonlítás segítségével azonosíthatja a nagyobb számot. Ha a karakterláncot hexaszta, akkor a szám 256 bites szám lesz. A sorszám szigorúan növekszik, és a legutóbbi esemény nagyobb számmal fog rendelkezni, mint más események száma. Ez akkor hasznos, ha gyakori eszközcsatlakozik és bontja a kapcsolatot, és biztosítani szeretné, hogy csak a legújabb eseményt használja egy alsóbb rétegbeli művelet elindításához, mivel az Azure Event Grid nem támogatja az események rendezését.

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nem rendelkezik ilyen, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

* Egy aktív Azure Cosmos DB SQL API-fiók. Ha még nem hozott létre egyet, olvassa el az [Adatbázis-fiók létrehozása](../cosmos-db/create-sql-api-java.md#create-a-database-account) a forgatókönyvhez című témakört.

* Gyűjtemény az adatbázisban. Lásd: [Gyűjtemény hozzáadása](../cosmos-db/create-sql-api-java.md#add-a-container) a forgatókönyvhez. A gyűjtemény létrehozásakor `/id` használja a partíciókulcsot.

* Egy IoT Hub az Azure-ban. Ha még nem hozott létre központot, [az IoT Hub első lépéseit](iot-hub-csharp-csharp-getstarted.md) ismertető cikkben talál útmutatást.

## <a name="create-a-stored-procedure"></a>Tárolt eljárás létrehozása

Először hozzon létre egy tárolt eljárást, és állítsa be úgy, hogy olyan logikát futtasson, amely összehasonlítja a bejövő események sorozatszámait, és rögzíti az adatbázisban eszközönkénti legutóbbi eseményt.

1. A Cosmos DB SQL API-ban válassza az **Adatkezelő** > **új** > **tárolt eljárás lehetőséget.**

   ![Tárolt eljárás létrehozása](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Írja be a Tárolt eljárásazonosító **legújabbDeviceConnectionState** azonosítóját, és illessze be a következőket a **Tárolt eljárás törzsébe.** Ne feledje, hogy ennek a kódnak felül kell niea a tárolt eljárás törzsében lévő bármely meglévő kód helyett. Ez a kód eszközönként egy sort tart fenn, és a legmagasabb sorszám azonosításával rögzíti az eszközazonosító legutóbbi kapcsolati állapotát.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Mentse a tárolt eljárást:

    ![tárolt eljárás mentése](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Először hozzon létre egy logikai alkalmazást, és adjon hozzá egy Event Grid-triggert, amely a virtuális gép erőforráscsoportját monitorozza.

### <a name="create-a-logic-app-resource"></a>Logikai alkalmazás erőforrás létrehozása

1. Az [Azure Portalon](https://portal.azure.com)válassza a **+Hozzon létre egy erőforrást**, válassza **az Integráció,** majd **a Logikai alkalmazás lehetőséget.**

   ![Logikai alkalmazás létrehozása](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Adjon az előfizetésben egyedi nevet a logikai alkalmazásnak, majd válassza ki ugyanazt az előfizetést, erőforráscsoportot és helyet, ahová az IoT-központ tartozik.

   ![Új logikai alkalmazás](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. A logikai alkalmazás létrehozásához válassza a **Létrehozás** gombot.

   Ezzel létrehozott egy Azure-erőforrást a logikai alkalmazás számára. Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer gyakran használt minták sablonjait jeleníti meg, hogy megkönnyítse az első lépéseket.

   > [!NOTE]
   > A logikai alkalmazás újbóli megkereséséhez és megnyitásához válassza **az Erőforráscsoportok** lehetőséget, és válassza ki az útmutatóhoz használt erőforráscsoportot. Ezután válassza ki az új logikai alkalmazást. Ez megnyitja a Logic App Designer.

4. A Logic App Designer, görgessen jobbra, amíg meg nem jelenik a közös eseményindítók. A **Sablonok csoportban**válassza **a Üres logikai alkalmazás** lehetőséget, hogy a logikai alkalmazást teljesen újjá hozhassa létre.

### <a name="select-a-trigger"></a>Trigger kiválasztása

A trigger a logikai alkalmazást elindító konkrét esemény. Ebben az oktatóanyagban a munkafolyamatot aktiváló trigger HTTP-kapcsolaton keresztül fogad egy kérést.

1. Az összekötők és az eseményindítók keresősávjába írja be a **HTTP értéket,** és nyomja le az Enter billentyűt.

2. Válassza ki a **Kérés – HTTP-kérés fogadásakor** lehetőséget triggerként.

   ![HTTP-kérés trigger kiválasztása](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget.

   ![Séma létrehozásához használjon mintahasznos tananyagot](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Másolja az alábbi JSON-kódmintát a szövegmezőbe, majd kattintson a **Kész** gombra:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Minta Beillesztése JSON hasznos teher](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Egy előugró értesítés jelenhet meg a következő üzenettel: **Ne felejtsen el egy alkalmazás/JSON értékű Content-Type fejlécet beszúrni a kérésbe**. A javaslatot nyugodtan figyelmen kívül hagyhatja, és továbbléphet a következő szakaszra.

### <a name="create-a-condition"></a>Feltétel létrehozása

A logikai alkalmazás munkafolyamatában a feltételek segítenek adott műveletek futtatásában az adott feltétel átadása után. A feltétel teljesülése után a kívánt művelet definiálható. Ebben az oktatóanyagban a feltétel annak ellenőrzése, hogy az eventType eszköz csatlakoztatva van-e vagy nincs-e csatlakoztatva. A művelet az adatbázisban tárolt eljárás végrehajtása lesz.

1. Válassza a **+ Új lépés** **lehetőséget,** majd a Beépített lehetőséget, majd a Feltétel lehetőséget, és válassza **a Feltétel**lehetőséget. Kattintson a **Válasszon egy értéket,** és megjelenik egy mező, amely a dinamikus tartalmat – a kijelölhető mezőket – jeleníti meg. Töltse ki az alábbi mezőket, hogy csak az eszközcsatlakoztatva és az eszközleválasztott események esetén hajtsa végre ezt:

   * Válasszon egy értéket: **eventType** -- válassza ezt a dinamikus tartalom mezőiből, amelyek akkor jelennek meg, amikor erre a mezőre kattint.
   * A "egyenlő" **ends with**
   * Válasszon egy értéket: **nected**.

     ![Kitöltési feltétel](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. A **ha igaz** párbeszédpanelen kattintson a **Művelet hozzáadása gombra.**
  
   ![Művelet hozzáadása, ha igaz](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Keresse meg a Cosmos DB-t, és válassza az **Azure Cosmos DB -Tárolt eljárás végrehajtása**

   ![CosmosDB keresése](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Töltse ki a **cosmosdb-connection** parancsot a **kapcsolatnévhez,** és jelölje ki a bejegyzést a táblában, majd válassza a **Létrehozás gombot.** Megjelenik a **Tárolt eljárás végrehajtása** panel. Adja meg a mezők értékeit:

   **Adatbázis azonosítója**: Todolist

   **Gyűjtemény azonosítója**: Elemek

   **Sproc ID**: LatestDeviceConnectionState

5. Válassza **az Új paraméter hozzáadása**lehetőséget. A megjelenő legördülő menüben jelölje be a **Partíció kulcs** és a tárolt **eljárás paraméterei**jelölőnégyzetet, majd kattintson a képernyő bármely más pontjára; hozzáad egy mezőt a partíciókulcs értékéhez, és egy mezőt a tárolt eljárás paramétereihez.

   ![logikai alkalmazás művelet feltöltése](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Most adja meg a partíció kulcs értékét és paramétereit az alábbiak szerint. Ügyeljen arra, hogy a zárójelek és dupla idézőjelek, ahogy látható. Előfordulhat, hogy a **Dinamikus tartalom hozzáadása** gombra kell kattintania ahhoz, hogy megkapja az itt használható érvényes értékeket.

   ![logikai alkalmazás művelet feltöltése](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Az ablaktábla tetején, ahol a **Következő szöveg szerepel,** a **Kimenet kiválasztása az előző lépésekből**csoportban győződjön meg arról, hogy a **Törzs** ki van jelölve.

   ![logikai alkalmazás feltöltése minden](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Mentse a logikai alkalmazást.

### <a name="copy-the-http-url"></a>A HTTP URL-cím másolása

Mielőtt elhagyja a Logic Apps Designer, másolja az URL-címet, amely a logikai alkalmazás figyel egy eseményindító. Erre az URL-címre az Event Grid konfigurálásához lesz szükség.

1. Kattintással bontsa ki a **HTTP-kérés fogadásakor** triggerkonfigurációs mezőt.

2. Másolja ki a **HTTP POST URL** értékét a mellette lévő másolás gombra kattintva.

   ![A HTTP POST URL másolása](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Mentse ezt az URL-címet, hogy a következő szakaszban hivatkozhasson majd rá.

## <a name="configure-subscription-for-iot-hub-events"></a>Az IoT Hub eseményeire való előfizetés beállítása

Ebben a szakaszban konfiguráljuk az IoT-központot, hogy közzétegye a bekövetkező eseményeket.

1. Az Azure Portalon keresse meg az IoT-központot.

2. Válassza **az Események**lehetőséget.

   ![Az Event Grid-adatok megnyitása](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Válassza a **+ Esemény előfizetés lehetőséget.**

   ![Új esemény-előfizetés létrehozása](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Az **esemény-előfizetés részleteinek kitöltése:** Adjon meg egy leíró nevet, és válassza **az Eseményrács sémáját**.

5. Töltse ki az **Eseménytípusok** mezőket. A legördülő listában válassza csak az **Eszköz csatlakoztatva** és az **Eszköz leválasztva lehetőséget** a menüből. Kattintson a képernyő tetszőleges pontjára a lista bezárásához és a beállítások mentéséhez.

   ![A keresett eseménytípusok beállítása](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. A **Végpont részletei**területen válassza a Végpont típusa **webhookként** lehetőséget, és kattintson a végpont kijelölésére, és illessze be a logikai alkalmazásból másolt URL-címet, és erősítse meg a kijelölést.

   ![Végpont url-címének kijelölése](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Az űrlapnak most az alábbi példához hasonlóan kell kinéznie:

   ![Esemény-előfizetési űrlapminta](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Kattintson a **Létrehozás** gombra az esemény-előfizetés mentéséhez.

## <a name="observe-events"></a>Események megfigyelése

Most, hogy az esemény-előfizetés be van állítva, teszteljük egy eszköz csatlakoztatásával.

### <a name="register-a-device-in-iot-hub"></a>Eszköz regisztrálása az IoT Hubban

1. Az IoT-központban válassza az **IoT-eszközök** lehetőséget.

2. Válassza a **+Hozzáadás** lehetőséget az ablaktábla tetején.

3. Az **Eszközazonosító** mezőben adja meg a `Demo-Device-1` azonosítót.

4. Kattintson a **Mentés** gombra.

5. Több eszközt is hozzáadhat különböző eszközazonosítókkal.

   ![Hubhoz hozzáadott eszközök](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Kattintson ismét a készülékre; most a kapcsolati karakterláncok és kulcsok lesznek kitöltve. Másolja a **Kapcsolat karakterláncot - elsődleges kulcs** későbbi használatra.

   ![Az eszköz ConnectionString karakterlánca](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Raspberry Pi szimulátor indítása

Használjuk a Raspberry Pi webszimulátort az eszközkapcsolat szimulálására.

[Raspberry Pi szimulátor indítása](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Mintaalkalmazás futtatása a Raspberry Pi webes szimulátorán

Ez egy eszközhöz csatlakoztatott eseményt indít el.

1. A kódolási területen cserélje le a helyőrzőt a 15-ös sorban az Azure IoT Hub-eszköz kapcsolati karakterláncával, amelyet az előző szakasz végén mentett.

   ![Beillesztés az eszköz kapcsolati karakterláncában](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Futtassa az alkalmazást a **Futtatás**lehetőség kiválasztásával.

Az alábbi kimenethez hasonló, amely az érzékelő adatait és az IoT hubra küldött üzeneteket jeleníti meg.

   ![Az alkalmazás futtatása](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Kattintson a **Leállítás** gombra a szimulátor leállításához és egy **eszközleválasztott esemény aktiválásához.**

Most már futtatott egy mintaalkalmazást az érzékelőadatok gyűjtéséhez és az IoT-központba való elküldéséhez.

### <a name="observe-events-in-cosmos-db"></a>Események megfigyelése a Cosmos DB-ben

A végrehajtott tárolt eljárás eredményeit a Cosmos DB-dokumentumban láthatja. Így néz ki mindez. Minden sor eszközenként a legújabb eszközkapcsolati állapotot tartalmazza.

   ![Hogyan lehet az eredményt](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure [Portal](https://portal.azure.com)használata helyett az IoT Hub lépéseit az Azure CLI használatával végezheti el. További részletek az Azure CLI-lapok on [hozzon létre egy esemény-előfizetés](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) t és [egy IoT-eszköz létrehozásáról.](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban olyan erőforrásokat használtunk, amelyek költségekkel terhelik az Azure-előfizetését. Ha befejezte az oktatóanyag kipróbálását és az eredmények tesztelését, tiltsa le vagy törölje azokat az erőforrásokat, amelyeket nem szeretne megtartani.

Ha nem szeretné elveszteni a logikai alkalmazásba fektetett munkáját, a törlés helyett csak tiltsa le.

1. Keresse meg a logikai alkalmazást.

2. Az **Áttekintés** panelen válassza a **Törlés** vagy **a Letiltás**lehetőséget.

    Mindegyik előfizetés egy ingyenes IoT-központtal rendelkezhet. Ha ingyenes központot hozott létre az oktatóanyaghoz, azt nem kell törölnie a költségek megelőzéséhez.

3. Keresse meg az IoT-központot.

4. Az **Áttekintés** panelen válassza a **Törlés gombot.**

    Az esemény-előfizetést akkor is érdemes lehet törölnie, ha megtartja az IoT-központot.

5. Az IoT-központban válassza az **Event Grid** lehetőséget.

6. Válassza ki a törölni kívánt esemény-előfizetést.

7. Válassza a **Törlés** elemet.

Ha el szeretne távolítani egy Azure Cosmos DB-fiókot az Azure Portalról, kattintson a jobb gombbal a fiók nevére, és kattintson a **Fiók törlése parancsra.** Tekintse meg az [Azure Cosmos DB-fiók törlésére](https://docs.microsoft.com/azure/cosmos-db/manage-account)vonatkozó részletes utasításokat.

## <a name="next-steps"></a>További lépések

* További információ az [IoT Hub-eseményekre való reagálásról az Event Grid használatával műveletek indításához](../iot-hub/iot-hub-event-grid.md)

* [Próbálja ki az IoT Hub-események oktatóanyagát](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* További információ arról, hogy mit tehetsz még az [Event Griddel](../event-grid/overview.md)