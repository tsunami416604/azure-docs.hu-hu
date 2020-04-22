---
title: IoT távoli figyelése és értesítései az Azure Logic Apps alkalmazásokkal | Microsoft dokumentumok
description: Azure Logic Apps az IoT-hőmérséklet figyelése az IoT hubon, és automatikusan e-mail értesítéseket küldeni a postaládájába az észlelt rendellenességek.
author: robinsh
keywords: iot monitoring, iot értesítések, iot hőmérséklet-ellenőrzés
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 2720f9acfa308294b30f9203ba80e3f9b426e1e9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680723"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT-távfigyelés és értesítések az IoT-központés postaláda összekötő Azure Logic Apps alkalmazásokkal

![Végpontok között diagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Az Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) segítségével vezényelheti a munkafolyamatokat a helyszíni és felhőalapú szolgáltatások, egy vagy több vállalat és a különböző protokollok között. A logikai alkalmazás egy eseményindítóval kezdődik, amelyet egy vagy több művelet követ, amelyek beépített vezérlőkkel, például feltételekkel és iterátorokkal szekvenálhatók. Ez a rugalmasság teszi a Logic Apps ideális IoT-megoldást az IoT figyelési forgatókönyvekhez. Például a telemetriai adatok érkezése egy eszközről egy IoT Hub-végponton kezdeményezheti a logikai alkalmazás munkafolyamatok az adatok egy Azure Storage blob, e-mail riasztások küldése, hogy figyelmeztesse az adatanomáliák, ütemezése technikus látogatás, ha egy eszköz hibát jelent, és így tovább.

## <a name="what-you-learn"></a>Ismertetett témák

Megtudhatja, hogyan hozhat létre egy logikai alkalmazást, amely összeköti az IoT hubot és a postaládát a hőmérséklet figyeléséhez és az értesítésekhez.

Az eszközön futó ügyfélkód beállít egy `temperatureAlert`alkalmazástulajdonságot, minden telemetriai üzenet, amelyet az IoT hubnak küld. Ha az ügyfélkód 30 °C feletti hőmérsékletet `true`észlel, ezt a tulajdonságot a ; ellenkező esetben a tulajdonságot a parancsra állítja. `false`

Az IoT hubba érkező üzenetek a következőkhöz hasonlóan néznek ki, a `temperatureAlert` törzsben és az alkalmazás tulajdonságaiban található tulajdonságban található telemetriai adatokkal (a rendszertulajdonságok nem jelennek meg):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Az IoT Hub üzenetformátumáról az [IoT Hub-üzenetek létrehozása és olvasása](iot-hub-devguide-messages-construct.md)című témakörben olvashat bővebben.

Ebben a témakörben az IoT hubon beállítható az `temperatureAlert` útválasztás olyan üzenetek küldéséhez, amelyekben a tulajdonság egy Service `true` Bus-végponthoz kapcsolódik. Ezután beállít egy logikai alkalmazást, amely elindítja a Service Bus végpontra érkező üzeneteket, és e-mail értesítést küld.

## <a name="what-you-do"></a>Mit csinálsz

* Hozzon létre egy Service Bus-névteret, és adjon hozzá egy Service Bus-várólistát.
* Adjon hozzá egy egyéni végpontot és egy útválasztási szabályt az IoT-központhoz a hőmérsékleti riasztást tartalmazó üzenetek nek a Service Bus-várólistába való továbbításához.
* Hozzon létre, konfiguráljon és teszteljen egy logikai alkalmazást a Service Bus-várólistából érkező üzenetek felhasználásához és értesítési e-mailek küldéséhez a kívánt címzettnek.

## <a name="what-you-need"></a>Mi szükséges

* Töltse ki a [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz oktatóanyagait; például [a Raspberry Pi a node.js-szel.](iot-hub-raspberry-pi-kit-node-get-started.md) Ezek a következő követelményekre vonatkoznak:

  * Aktív Azure-előfizetés.
  * Egy Azure IoT hub az előfizetés alatt.
  * Az eszközön futó ügyfélalkalmazás, amely telemetriai üzeneteket küld az Azure IoT hub.

## <a name="create-service-bus-namespace-and-queue"></a>Szolgáltatásbusz-névtér és várólista létrehozása

Hozzon létre egy Service Bus-névteret és üzenetsort. Ebben a témakörben később egy útválasztási szabályt hoz létre az IoT hubban, amely hőmérsékleti riasztást tartalmazó üzeneteket a Service Bus-várólistába irányít, ahol egy logikai alkalmazás felveszi őket, és elindítja azt egy értesítő e-mail küldésére.

### <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása

1. Az [Azure Portalon](https://portal.azure.com/)válassza a + > **Erőforrás-integrációs** > **szolgáltatásbusz** **létrehozása**lehetőséget.

1. A **Névtér létrehozása** ablaktáblán adja meg a következő adatokat:

   **Név**: A szolgáltatásbusz névterének neve. A névtérnek egyedinek kell lennie az Azure-ban.

   **Tarifacsomag**: Válassza az **Alap elemet** a legördülő listából. Az alapszintű szint elegendő ehhez az oktatóanyaghoz.

   **Erőforráscsoport:** Használja ugyanazt az erőforráscsoportot, amelyet az IoT hub használ.

   **Hely:** Használja ugyanazt a helyet, amelyet az IoT hub használ.

   ![Szolgáltatásbusz-névtér létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Kattintson a **Létrehozás** gombra. Várja meg, amíg a központi telepítés befejeződik, mielőtt továbblépne a következő lépésre.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Service Bus-várólista hozzáadása a névtérhez

1. Nyissa meg a Service Bus névteret. A Service Bus névtér hez a legegyszerűbben úgy juthat el, ha az **erőforráspanelről kiválasztja az Erőforráscsoportokat,** kiválasztja az erőforráscsoportot, majd az erőforrások listájából kiválasztja a Service Bus névteret.

1. A **Service Bus névtér ablaktábláján** válassza a **+ Várólista lehetőséget.**

1. Adja meg a várólista nevét, majd válassza a **Létrehozás gombot.** A várólista sikeres létrehozása után a **Várólista létrehozása** ablaktábla bezárul.

   ![Szolgáltatásbusz-várólista hozzáadása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. A **Service Bus névtér ablaktáblájának** **Entitások**területén válassza a **Várólisták**lehetőséget. Nyissa meg a Service Bus várólistát a listából, majd válassza **a Megosztott hozzáférési házirendek** > **+ Hozzáadás**lehetőséget.

1. Adja meg a házirend nevét, jelölje be a **Kezelés**jelölőnégyzetet, és válassza a **Létrehozás lehetőséget.**

   ![Szolgáltatásbusz-várólista-házirend hozzáadása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Egyéni végpont- és útválasztási szabály hozzáadása az IoT-központhoz

Adjon hozzá egy egyéni végpontot a Service Bus-várólistához az IoT hubhoz, és hozzon létre egy üzenet-útválasztási szabályt a hőmérséklet-riasztást tartalmazó üzenetek irányításához, ahol a logikai alkalmazás felveszi őket. Az útválasztási szabály útválasztási lekérdezést használ, `temperatureAlert = "true"`a `temperatureAlert` program az eszközön futó ügyfélkód által beállított alkalmazástulajdonság értéke alapján továbbítja az üzeneteket. További információ: [Üzenet-útválasztási lekérdezés az üzenet tulajdonságai alapján](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Egyéni végpont hozzáadása

1. Nyissa meg az IoT-központot. Az IoT-központhoz való leggyorsabb leválasztás: **erőforráscsoportok** kiválasztása az erőforrás-ablaktáblából, válassza ki az erőforráscsoportot, majd válassza ki az IoT-központot az erőforrások listájából.

1. **Az Üzenetek csoportban**válassza **az Üzenetútválasztás**lehetőséget. Az **Üzenetút-tervezés** ablaktáblán jelölje ki az **Egyéni végpontok** lapot, majd a **+ Hozzáadás lehetőséget.** A legördülő listából válassza a **Service bus queue**lehetőséget.

   ![Végpont hozzáadása az IoT-központhoz az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. A **Szolgáltatásbusz végpontjának hozzáadása** ablaktáblán adja meg a következő adatokat:

   **Végpont neve**: A végpont neve.

   **Service bus névtér**: Válassza ki a létrehozott névteret.

   **Szolgáltatásbusz-várólista:** Válassza ki a létrehozott várólistát.

   ![Végpont hozzáadása az IoT-központhoz az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Kattintson a **Létrehozás** gombra. A végpont sikeres létrehozása után folytassa a következő lépéssel.

### <a name="add-a-routing-rule"></a>Útválasztási szabály hozzáadása

1. Az **Üzenetút-küldés** ablaktáblán válassza az **Útvonalak** lapot, majd a **+ Hozzáadás lehetőséget.**

1. Az **Útvonal hozzáadása** ablaktáblán adja meg a következő adatokat:

   **Név**: Az útválasztási szabály neve.

   **Végpont**: Jelölje ki a létrehozott végpontot.

   **Adatforrás**: Válassza **az Eszköztelemetriai üzenetek lehetőséget.**

   **Útválasztási**lekérdezés `temperatureAlert = "true"`: Írja be a értéket.

   ![Útválasztási szabály hozzáadása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Kattintson a **Mentés** gombra. Bezárhatja az **Üzenet útválasztási** ablaktáblát.

## <a name="create-and-configure-a-logic-app"></a>Logikai alkalmazás létrehozása és konfigurálása

Az előző szakaszban beállítja az IoT hub a hőmérséklet-riasztást tartalmazó üzeneteket a Service Bus-várólistába. Most beállíthat egy logikai alkalmazást a Service Bus-várólista figyelésére, és e-mail értesítést küld, amikor egy üzenet kerül a várólistába.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

1. Válassza az > **Erőforrás-integrációs** > **logikai alkalmazás** **létrehozása**lehetőséget.

1. Adja meg a következő információkat:

   **Név**: A logikai alkalmazás neve.

   **Erőforráscsoport:** Használja ugyanazt az erőforráscsoportot, amelyet az IoT hub használ.

   **Hely:** Használja ugyanazt a helyet, amelyet az IoT hub használ.

   ![Logikai alkalmazás létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Kattintson a **Létrehozás** gombra.

### <a name="configure-the-logic-app-trigger"></a>A logikai alkalmazás eseményindítójának konfigurálása

1. Nyissa meg a logikai alkalmazást. A legegyszerűbb módja annak, hogy a logikai alkalmazás válassza **erőforráscsoportok** az erőforrás ablaktáblából, válassza ki az erőforráscsoportot, majd válassza ki a logikai alkalmazást az erőforrások listájából. Amikor kiválasztja a logikai alkalmazást, megnyílik a Logic Apps Designer.

1. A Logic Apps Designerben görgessen le a **Sablonok** laphoz, és válassza az **Üres logikai alkalmazás lehetőséget.**

   ![Kezdje egy üres logikai alkalmazással az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Válassza az **Összes** lapot, majd a **Service Bus**lehetőséget.

   ![Válassza a Service Bus lehetőséget a logikai alkalmazás létrehozásának megkezdéséhez az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Az **Eseményindítók**csoportban jelölje be a **Ha egy vagy több üzenet érkezik a várólistába (automatikus kiegészítés)** lehetőséget.

   ![Válassza ki a logikai alkalmazás eseményindítóját az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Hozzon létre egy szolgáltatásbusz-kapcsolatot.
   1. Adja meg a kapcsolat nevét, és válassza ki a Service Bus névterét a listából. Megnyílik a következő képernyő.

      ![Szolgáltatásbusz-kapcsolat létrehozása a logikai alkalmazáshoz az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Válassza ki a szolgáltatásbusz-házirendet (RootManageSharedAccessKey). Ezután válassza **a Létrehozás lehetőséget.**

      ![Szolgáltatásbusz-kapcsolat létrehozása a logikai alkalmazáshoz az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. A végső képernyőn a **Várólista neve**területen válassza ki a legördülő menüből létrehozott várólistát. Írja `175` be a **Maximális üzenetszám mezőbe.**

      ![Adja meg a szolgáltatásbusz-kapcsolat maximális üzenetszámát a logikai alkalmazásban](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. A **Save** módosítások mentéséhez válassza a Logic Apps Designer menümentés parancsát.

### <a name="configure-the-logic-app-action"></a>A logikai alkalmazás műveletének konfigurálása

1. SMTP-szolgáltatáskapcsolat létrehozása.

   1. Válassza az **Új lépés** lehetőséget. A **Művelet kiválasztása**csoportban válassza a **Mind** lehetőséget.

   1. Írja `smtp` be a keresőmezőbe, jelölje ki az **SMTP-szolgáltatást** a keresési eredményben, majd válassza az **E-mail küldése**lehetőséget.

      ![SMTP-kapcsolat létrehozása a logikai alkalmazásban az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Adja meg a postaládához szükséges SMTP-adatokat, majd válassza a **Létrehozás gombot.**

      ![SMTP-kapcsolat adatainak megadása a logikai alkalmazásban az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      A [Hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970)a [Gmail](https://support.google.com/a/answer/176600?hl=en)és a [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html)SMTP-adatainak beszerzése.

      > [!NOTE]
      > Előfordulhat, hogy a kapcsolat létrehozásához le kell tiltania a TLS/SSL-t. Ha ez a helyzet, és a kapcsolat létrejötte után újra engedélyezni szeretné a TLS-t, olvassa el a szakasz végén található választható lépést.

   1. Az **Új paraméter hozzáadása** legördülő **Send Email** menüből válassza a **Feladó**, **A,** **Tárgy** és **Törzs**lehetőséget. Kattintson vagy koppintson a képernyő tetszőleges pontjára a kijelölési mező bezárásához.

      ![SMTP-kapcsolat e-mail mezőinek kiválasztása](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Adja meg a **Címzett** és `High temperature detected` a **Címzett,** valamint a **Tárgy** és **a Törzs**e-mail címét. Ha megnyílik a **Dinamikus tartalom hozzáadása a folyamatpárbeszédpanelen használt alkalmazásokból és összekötőkből,** az **Elrejtés gombra** választva zárja be. Ebben az oktatóanyagban nem használ dinamikus tartalmat.

      ![Kitöltő SMTP-kapcsolat e-mail mezői](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Az SMTP-kapcsolat mentéséhez válassza a **Mentés** lehetőséget.

1. (Nem kötelező) Ha az e-mail szolgáltatóval való kapcsolat létesítéséhez le kellett tiltania a TLS-t, és újra engedélyezni szeretné azt, kövesse az alábbi lépéseket:

   1. A **Logikai alkalmazás** ablaktábláján a **Fejlesztői eszközök**csoportban válassza az **API-kapcsolatok lehetőséget.**

   1. Az API-kapcsolatok listájából válassza ki az SMTP-kapcsolatot.

   1. Az **SMTP API-kapcsolat** ablaktáblán az **Általános**csoportban válassza **az API-kapcsolat szerkesztése**lehetőséget.

   1. Az **API-kapcsolat szerkesztése** ablaktáblán válassza az **SSL engedélyezése lehetőséget?** **Save**

      ![SMTP API-kapcsolat szerkesztése a logikai alkalmazásban az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

A logikai alkalmazás most már készen áll a Service Bus-várólistából érkező hőmérsékleti riasztások feldolgozására, és értesítések et küldhet az e-mail fiókba.

## <a name="test-the-logic-app"></a>A logikai alkalmazás tesztelése

1. Indítsa el az ügyfélalkalmazást az eszközön.

1. Ha fizikai eszközt használ, óvatosan hozzon egy hőforrást a hőérzékelő közelébe, amíg a hőmérséklet meg nem haladja a 30 °C-ot. Ha az online szimulátort használja, az ügyfélkód véletlenszerűen 30 °C-ot meghaladó telemetriai üzeneteket fog kiküldeni.

1. Meg kell kezdenie a logikai alkalmazás által küldött e-mail-értesítések fogadását.

   > [!NOTE]
   > Előfordulhat, hogy az e-mail szolgáltatónak ellenőriznie kell a feladó személyazonosságát, hogy megbizonyosodjon arról, hogy Ön küldte az e-mailt.

## <a name="next-steps"></a>További lépések

Sikeresen létrehozott egy logikai alkalmazást, amely összeköti az IoT hubot és a postaládát a hőmérséklet figyeléséhez és az értesítésekhez.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
