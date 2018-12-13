---
title: Az Azure Cosmos DB használatával az Azure IoT Hub eszköz kapcsolateseményei ORDER |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan order, és jegyezze fel az eszköz kapcsolati események az Azure IoT Hub az Azure Cosmos DB a legújabb kapcsolati-állapot karbantartásához
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: asrastog
ms.openlocfilehash: daf4a6142c0e30fa7b8534e5598cec39c69079c0
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184049"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Rendelés eszköz az Azure Cosmos DB használatával az Azure IoT Hub kapcsolati események

Az Azure Event Grid segít az esemény-alapú alkalmazásokat fejleszthet, és könnyedén integrálhatja a saját vállalati megoldások az IoT-események. Ez a cikk végigvezeti a telepítés nyomon követését és a legújabb eszköz kapcsolat állapot tárolásához a Cosmos DB-ben használható. Azt a csatlakoztatott eszköz és az eszköz leválasztott esemény elérhető sorszáma használja, és a legfrissebb állapot tárolásához a Cosmos DB-ben. Egy tárolt eljárást, amely egy Cosmos DB-gyűjteményen végrehajtott alkalmazáslogika használni fogjuk.

A feladatütemezési szám hexadecimális szám karakterláncként. Összehasonlítási karakterlánc segítségével azonosíthatja a nagyobb számot. Ha a hexadecimális karakterlánc, a szám egy 256 bites szám lesz. A sorszám szigorúan növekszik, és az utolsó esemény fog rendelkezni, mint az eseményeket a nagyobb szám. Ez akkor hasznos, ha gyakori eszköz kapcsolódik, és bontja a kapcsolatot, és győződjön meg arról, csak az utolsó esemény segítségével alsóbb rétegbeli műveletet indít, mivel az Azure Event Grid nem támogatja az események rendezése.

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nincs fiókja, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

* Egy aktív Azure Cosmos DB SQL API-fiók. Ha még nem hozta létre az egyik még, [adatbázisfiók létrehozása](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#create-a-database-account) bemutató.

* Az adatbázis-gyűjtemény. Lásd: [gyűjtemény hozzáadása](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-dotnet#add-a-collection) bemutató.

* Egy IoT Hub az Azure-ban. Ha még nem hozott létre központot, [az IoT Hub első lépéseit](../iot-hub/iot-hub-csharp-csharp-getstarted.md) ismertető cikkben talál útmutatást. 

## <a name="create-a-stored-procedure"></a>Tárolt eljárás létrehozása

Először hozzon létre egy tárolt eljárást, és állítsa be, akár egy logika, amely összehasonlítja a bejövő események sorozatszámok és az utolsó esemény eszközönként rögzíti az adatbázis futtatásához.

1. Válassza ki a Cosmos DB SQL API- **adatkezelő** > **elemek** > **új tárolt eljárás**.

   ![Tárolt eljárás létrehozása](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Adja meg a tárolt eljárás Azonosítóját, és illessze be az alábbi "tárolt eljárás törzsének". Vegye figyelembe, hogy ez a kód cserélje le a meglévő kód a tárolt eljárás törzsében. Ez a kód tartja karban a soronként egy Eszközazonosítót, és a legmagasabb sorszám azonosításával adott Eszközazonosító legutóbbi kapcsolat állapotát rögzíti. 

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

3. A tárolt eljárás mentése: 

    ![tárolt eljárás mentése](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Először hozzon létre egy logikai alkalmazást, és adjon hozzá egy Event Grid-triggert, amely a virtuális gép erőforráscsoportját monitorozza. 

### <a name="create-a-logic-app-resource"></a>Logikai alkalmazás erőforrás létrehozása

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Új** > **Integráció** > **Logikai alkalmazás** lehetőséget.

   ![Logikai alkalmazás létrehozása](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Adjon az előfizetésben egyedi nevet a logikai alkalmazásnak, majd válassza ki ugyanazt az előfizetést, erőforráscsoportot és helyet, ahová az IoT-központ tartozik. 

3. Válassza ki **rögzítés az irányítópulton**, majd válassza a **létrehozás**.

   Ezzel létrehozott egy Azure-erőforrást a logikai alkalmazás számára. Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer gyakran használt minták sablonjait jeleníti meg, hogy megkönnyítse az első lépéseket.

   > [!NOTE] 
   > Ha bejelöli **rögzítés az irányítópulton**, a logikai alkalmazás automatikusan megnyílik a Logic Apps Designerben. Ellenkező esetben manuálisan is megkeresheti és megnyithatja a logikai alkalmazást.

4. A Logic App Designerben a **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet, így az alapoktól építheti fel logikai alkalmazását.

### <a name="select-a-trigger"></a>Trigger kiválasztása

A trigger a logikai alkalmazást elindító konkrét esemény. Ebben az oktatóanyagban a munkafolyamatot aktiváló trigger HTTP-kapcsolaton keresztül fogad egy kérést.  

1. Írja be a **HTTP** kifejezést az összekötők és triggerek keresősávjába.

2. Válassza ki a **Kérés – HTTP-kérés fogadásakor** lehetőséget triggerként. 

   ![HTTP-kérés trigger kiválasztása](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. 

   ![A séma létrehozása hasznosadat-minta használatával](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

5. Egy előugró értesítés jelenhet meg a következő üzenettel: **Ne felejtsen el egy alkalmazás/JSON értékű Content-Type fejlécet beszúrni a kérésbe**. A javaslatot nyugodtan figyelmen kívül hagyhatja, és továbbléphet a következő szakaszra. 

### <a name="create-a-condition"></a>Feltétel létrehozása

A logikai alkalmazás munkafolyamatának a feltételek segítenek, hogy adott feltétel elhagyása után, meghatározott műveletek futtatása. Ha a feltétel teljesül, a kívánt művelet lehet definiálni. Ebben az oktatóanyagban a feltételt, hogy ellenőrizze, hogy eventType csatlakoztatott vagy leválasztott eszköz. A művelet lesz, a tárolt eljárás végrehajtására az adatbázisban. 

1. Válassza ki **új lépés** majd **Built-ins** és **feltétel**. 

2. Töltse ki a feltétel, csak végrehajtani a csatlakoztatott eszköz és az eszköz leválasztott események alább látható módon:

   * Válasszon egy értéket: **esemény típusa**
   * Módosítási "egyenlő" való **végződik.**
   * Válasszon egy értéket: **nected**

     ![Töltse ki a feltétel](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

3. Ha a feltétel teljesül, kattintson a **művelet hozzáadása**.
  
   ![Művelet hozzáadása, ha az értéke igaz](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

4. Keresse meg a Cosmos DB, majd kattintson a **Azure Cosmos DB - tárolt eljárás végrehajtása**

   ![Keresés a cosmosdb-hez](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

5. Töltse ki az űrlapot a végrehajtáshoz tárolt értékek válassza ki az adatbázis be kell szereznie. Adja meg a partíciókulcs-értékkel, és a paraméterek alább látható módon. 

   ![Töltse fel a logikaialkalmazás-művelet](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Mentse a logikai alkalmazást. 

### <a name="copy-the-http-url"></a>A HTTP URL-cím másolása

Mielőtt elhagyja a Logic Apps Designer, másolja az URL-cím, amely a logikai alkalmazás az eseményindító figyel. Erre az URL-címre az Event Grid konfigurálásához lesz szükség. 

1. Kattintással bontsa ki a **HTTP-kérés fogadásakor** triggerkonfigurációs mezőt. 

2. Másolja ki a **HTTP POST URL** értékét a mellette lévő másolás gombra kattintva. 

   ![A HTTP POST URL másolása](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Mentse ezt az URL-címet, hogy a következő szakaszban hivatkozhasson majd rá. 

## <a name="configure-subscription-for-iot-hub-events"></a>Az IoT Hub eseményeire való előfizetés beállítása

Ebben a szakaszban konfiguráljuk az IoT-központot, hogy közzétegye a bekövetkező eseményeket. 

1. Az Azure Portalon keresse meg az IoT-központot. 

2. Válassza az **Események** lehetőséget.

   ![Az Event Grid-adatok megnyitása](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Válassza az **Esemény-előfizetés** lehetőséget. 

   ![Új esemény-előfizetés létrehozása](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Hozza létre az esemény-előfizetést a következő értékekkel: 

   * **Esemény típusa**: Törölje a jelet az előfizetés az összes eseménytípusra, és válassza ki **csatlakoztatott eszköz** és **eszköz leválasztott** a menüből.

   * **Végpont részletei**: Válassza ki a végpont típusa szerint **Webhook** és kattintson a válassza végpont, és illessze be az URL-cím a logikai alkalmazás kimásolt és kijelölés megerősítéséhez.

       ![végpont url-jének kiválasztása](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

   * **Esemény-előfizetés részleteit**: Adjon meg egy leíró nevet, és válassza ki **Event Grid séma**.
   Az űrlapot a következő példához hasonlóan néz ki: 

       ![Esemény-előfizetési űrlapminta](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

5. Kattintson a **Létrehozás** gombra az esemény-előfizetés mentéséhez.

## <a name="observe-events"></a>Tekintse át az események

Most, hogy az esemény-előfizetés be van állítva, nézzük tesztet úgy, egy eszközt.

### <a name="register-a-device-in-iot-hub"></a>Eszköz regisztrálása az IoT hubon

1. Az IoT-központban válassza az **IoT-eszközök** lehetőséget. 

2. Válassza a **Hozzáadás** lehetőséget.

3. Az **Eszközazonosító** mezőben adja meg a `Demo-Device-1` azonosítót.

4. Kattintson a **Mentés** gombra. 

5. Különböző eszközazonosítókat használatával több eszközt is hozzáadhat.

   ![Hogyan eredménye](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Másolás a **kapcsolati karakterlánc – elsődleges kulcs** későbbi használatra.

   ![Hogyan eredménye](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Indítsa el a Raspberry Pi-szimulátor

1. A Raspberry Pi-webszimulátor használatával szimulálja az eszköz kapcsolat.

[Indítsa el a Raspberry Pi-szimulátor](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>A mintaalkalmazás futtatása a a Raspberry Pi-webszimulátor

Ezzel megkezdődik egy eszköz csatlakoztatott esemény.

1. A kódolási területen helyőrzőt cserélje le a sort 15 eszköz az Azure IoT Hub kapcsolati karakterláncra.

   ![Hogyan eredménye](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Az alkalmazás futtatásához kattintson a **futtatása**.

Amely az érzékelőktől kapott adatok és az IoT hubnak küldött üzeneteket jeleníti meg a következő kimenetnek kell megjelennie.

   ![Hogyan eredménye](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Kattintson a **leállítása** állítsa le a szimulátor, és a egy **eszköz csatlakozik** esemény.

Érzékelőktől kapott adatok összegyűjti és elküldi azt az IoT hub egy mintaalkalmazást most már futtatta. 

### <a name="observe-events-in-cosmos-db"></a>Tekintse át az események Cosmos DB-ben

Láthatja a végrehajtott tárolt eljárás eredményét a Cosmos DB-dokumentumban. Itt látható, hogyan is néz. Minden egyes sor tartalmazza a legújabb az eszköz kapcsolati állapotát eszközönként.

   ![Hogyan eredménye](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Használata helyett a [az Azure portal](http://portal.azure.com), az IoT Hub lépéseket az Azure CLI használatával is elérheti. További információkért lásd: az Azure CLI-vel oldalak [egy esemény-előfizetés létrehozása](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) és [egy IoT-eszköz létrehozása](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban olyan erőforrásokat használtunk, amelyek költségekkel terhelik az Azure-előfizetését. Miután végzett az oktatóanyag kipróbálásával és az eredmények tesztelésével, mindenképp tiltsa le vagy törölje azokat az erőforrásokat, amelyeket nem kíván megtartani. 

Ha nem szeretné elveszteni a logikai alkalmazásba fektetett munkáját, a törlés helyett csak tiltsa le. 

1. Keresse meg a logikai alkalmazást.

2. Az a **áttekintése** panelen válassza ki **törlése** vagy **letiltása**. 

Mindegyik előfizetés egy ingyenes IoT-központtal rendelkezhet. Ha ingyenes központot hozott létre az oktatóanyaghoz, azt nem kell törölnie a költségek megelőzéséhez.

1. Keresse meg az IoT-központot. 

2. Az a **áttekintése** panelen válassza ki **törlése**. 

Az esemény-előfizetést akkor is érdemes lehet törölnie, ha megtartja az IoT-központot. 

1. Az IoT-központban válassza az **Event Grid** lehetőséget.

2. Válassza ki a törölni kívánt esemény-előfizetést. 

3. Válassza a **Törlés** elemet. 

Távolítsa el az Azure Cosmos DB-fiókot az Azure Portalon, kattintson a jobb gombbal a fiók nevét, és kattintson a **fiók törlése**. Részletes útmutatást lásd: [törlése az Azure Cosmos DB-fiók](https://docs.microsoft.com/azure/cosmos-db/manage-account#delete).

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [reagálás eseményekre az IoT Hub Event Grid használatával a műveletek indítása](../iot-hub/iot-hub-event-grid.md)

* [Próbálja ki az IoT Hub-események oktatóanyag](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Ismerje meg, hogy más mi mindent tehet az [Event Grid](../event-grid/overview.md)


