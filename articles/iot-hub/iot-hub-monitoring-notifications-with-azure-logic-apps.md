---
title: Távoli megfigyelés IoT és az értesítések az Azure Logic Apps |} A Microsoft Docs
description: IoT hőmérséklet figyelése az IoT hubon az Azure Logic Apps használatával, és automatikusan a postaláda az összes észlelt rendellenességek e-mail értesítések küldéséhez.
author: robinsh
keywords: IOT, iot-értesítések, monitoring iot hőmérséklet figyelése
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719338"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT távoli figyelés és értesítések az Azure Logic Apps csatlakoztatása az IoT hub és a postaláda

![Végpontok közötti diagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Az Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) munkafolyamatok összehangolására a helyszíni és felhőbeli szolgáltatásokat, egy vagy több vállalatok számára, és különböző protokollok között. Logikai alkalmazás egy eseményindítóval, amely egy vagy több műveletet beépített vezérlőkkel, például a feltételek és iterátorok is előkészített követi kezdődik. Ez rugalmasságot lehetővé teszi a Logic Apps ideális IoT-megoldások figyelési IoT-forgatókönyvek esetén. Például a telemetriai adatokat egy eszközről az IoT Hub-végponton megérkezése kezdeményezheti a logikai alkalmazások munkafolyamataiba adatraktár-az adatok egy Azure Storage-blobba, küldjön e-mailes riasztásokhoz figyelmezteti a adatok rendellenességek észlelését, a technikus meglátogatása ütemezése, ha egy jelentett hiba , és így tovább.

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti, hogyan hozhat létre egy logikai alkalmazást, amely csatlakozik az IoT hub és a postaláda hőmérséklet-figyelés és értesítések.

Az Ügyfélkód az eszközön futó beállítja egy alkalmazás tulajdonság `temperatureAlert`, az összes telemetriai üzenetet, küld az IoT hubhoz. Ha az ügyfélkód a fenti 30 C hőmérséklet észlel, azt állítja be ezt a tulajdonságot `true`; ellenkező esetben azt állítja be a tulajdonság `false`.

Ebben a témakörben, állítsa be az IoT hub az Útválasztás, amelyben üzenetek küldéséhez `temperatureAlert = true` egy Service Bus végpont, és állítsa be a logikai alkalmazást, amely elindítja a Service Bus-végpont érkező üzeneteket, és e-mailben értesítést küld Önnek.

## <a name="what-you-do"></a>TEENDŐ

* Service Bus-névtér létrehozása, és hozzá tud adni egy Service Bus-üzenetsorba.
* Egyéni végpontok és útválasztási szabály hozzáadása az IoT hub, a Service Bus-üzenetsorba egy hőmérséklet riasztást tartalmazó üzenetek továbbítását.
* Létrehozása, konfigurálása és tesztelése a Service Bus-üzenetsorba érkező üzeneteket fogadni, és értesítő e-mailek küldése a kívánt címzettje számára egy logikai alkalmazást.

## <a name="what-you-need"></a>Mi szükséges

* Végezze el a [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyag vagy az eszköz oktatóanyagokban; például [Raspberry Pi és node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ez érvényes a következő követelményeknek:

  * Aktív Azure-előfizetés.
  * Az Azure IoT hub az előfizetéséhez.
  * Telemetriai üzeneteket küld az Azure IoT hub az eszközén futó ügyfélalkalmazás.

## <a name="create-service-bus-namespace-and-queue"></a>Service Bus-névtér és üzenetsor létrehozása

Hozzon létre egy Service Bus-névteret és üzenetsort. Ez a témakör későbbi részében egy útválasztási szabályt, a Service Bus-üzenetsorba, ahol a logikai alkalmazás által mértékének és aktiválása, hogy értesítő e-mailt küldjön egy hőmérséklet riasztást tartalmazó közvetlen üzeneteket az IoT hub létrehozása.

### <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása

1. Az a [az Azure portal](https://portal.azure.com/)válassza **+ erőforrás létrehozása** > **integrációs** > **a Service Bus**.

1. Az a **névtér létrehozása** panelen adja meg a következő információkat:

   **Név**: A service bus-névtér neve. A névtér Azure egyedinek kell lennie.

   **A tarifacsomag**: Válassza ki **alapszintű** a legördülő listából. Az alapszintű csomag is elegendő ehhez az oktatóanyaghoz.

   **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: Az IoT hub használó ugyanazt a helyet használja.

   ![A service bus-névtér létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Kattintson a **Létrehozás** gombra. Várjon, amíg az üzembe helyezés befejeződik, mielőtt a következő lépéssel.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>A névtér hozzáadása egy Service Bus-üzenetsorba

1. Nyissa meg a Service Bus-névteret. A legegyszerűbben úgy juthat el a Service Bus-névtér az, hogy válasszon **erőforráscsoportok** erőforrás panelen jelölje ki az erőforráscsoportot, majd válassza ki a Service Bus-névtér az erőforrások listájából.

1. Az a **Service Bus-Namespace** ablaktáblán válassza előbb **+ üzenetsor**.

1. Adja meg a várólista nevét, majd **létrehozás**. Ha a várólista sikeresen létrejött, a **üzenetsor létrehozása** ablaktáblát.

   ![Az Azure Portalon adjon hozzá egy service bus-üzenetsorba](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Lépjen vissza a **Service Bus-Namespace** panel alatt **entitások**, jelölje be **üzenetsorok**. Nyissa meg a Service Bus-üzenetsorba a listából, és válassza ki **megosztott elérési házirendek** >  **+ Hozzáadás**.

1. Adjon meg egy nevet a házirendet, jelölőnégyzet **kezelés**, majd válassza ki **létrehozás**.

   ![A service bus-üzenetsor szabályzat hozzáadása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Egyéni végpont és útválasztási szabály hozzáadása az IoT hubhoz

Vegyen fel egy egyéni végpontot a Service Bus-üzenetsorba az IoT hubhoz, és hozzon létre egy üzenetet útválasztási szabályt, amely közvetlen üzeneteket, amelyek egy hőmérséklet riasztást, hogy a végpont tartalmazzák, ahol csak azt követően dolgozza a logikai alkalmazás által. Az útválasztási szabályt használ egy útválasztási lekérdezés `temperatureAlert = "true"`továbbított üzenetek értéke alapján, a `temperatureAlert` az Ügyfélkód az eszközön futó alkalmazás tulajdonsággal. További tudnivalókért lásd: [üzenet tulajdonságai alapján útválasztási lekérdezés üzenet](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Egyéni végpont hozzáadása

1. Nyissa meg az IoT hubnak. A legegyszerűbben úgy juthat el az IoT hub az, hogy válasszon **erőforráscsoportok** erőforrás panelen jelölje ki az erőforráscsoportot, majd válassza ki az IoT hub az erőforrások listájából.

1. A **üzenetkezelés**válassza **üzenet-útválasztása**. Az a **üzenet-útválasztása** panelen válassza a **egyéni végpontok** lapot, majd **+ Hozzáadás**. A legördülő listából válassza ki a **Service bus-üzenetsor**.

   ![Az Azure Portalon az IoT hub-végpont hozzáadása](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Az a **hozzáadása egy service bus-végponttal** panelen adja meg a következőket:

   **Végpont neve**: A végpont neve.

   **Service bus-névtér**: Válassza ki a létrehozott névtérre.

   **Service bus-üzenetsor**: Válassza ki a létrehozott sorban.

   ![Az Azure Portalon az IoT hub-végpont hozzáadása](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Kattintson a **Létrehozás** gombra. A végpont sikeres létrehozása után folytassa a következő lépéssel.

### <a name="add-a-routing-rule"></a>Útválasztási szabály hozzáadása

1. Lépjen vissza a **üzenet-útválasztása** panelen válassza a **útvonalak** lapot, majd **+ Hozzáadás**.

1. Az a **adjon hozzá egy útvonalat** panelen adja meg a következőket:

   **Név**: Az útválasztási szabály neve.

   **Végpont**: Válassza ki a létrehozott végpont.

   **Az adatforrás**: Válassza ki **eszköz Telemetriai üzeneteket**.

   **Útválasztási lekérdezés**: Írja be a `temperatureAlert = "true"` (igen) kifejezést.

   ![Útválasztási szabály hozzáadása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Kattintson a **Mentés** gombra. Zárja be a **üzenet-útválasztása** ablaktáblán.

## <a name="create-and-configure-a-logic-app"></a>A logikai alkalmazás létrehozása és konfigurálása

Az előző szakaszban beállíthatja az IoT hub egy hőmérséklet-értesítés a Service Bus-üzenetsorhoz tartalmazó üzenetek továbbítását. Most már beállította egy logikai alkalmazást, figyelheti a Service Bus-üzenetsorba, és értesítő e-mailt küld, amikor egy üzenet bekerül az üzenetsorba.

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

1. Válassza ki **erőforrás létrehozása** > **integrációs** > **logikai alkalmazás**.

1. Adja meg a következő információkat:

   **Név**: A logikai alkalmazás neve.

   **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: Az IoT hub használó ugyanazt a helyet használja.

   ![Logikai alkalmazás létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Kattintson a **Létrehozás** gombra.

### <a name="configure-the-logic-app-trigger"></a>A logikai alkalmazás eseményindítóját konfigurálása

1. Nyissa meg a logikai alkalmazást. A legegyszerűbben úgy juthat el a logikai alkalmazást, hogy válasszon **erőforráscsoportok** erőforrás panelen jelölje ki az erőforráscsoportot, majd válassza ki a logikai alkalmazás az erőforrások listájából. Amikor kiválasztja a logikai alkalmazást, megnyílik a Logic Apps Designerben.

1. A Logic Apps Designerben görgessen le a **sablonok** válassza **üres logikai alkalmazás**.

   ![Kezdje egy üres logikai alkalmazás az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Válassza ki a **összes** lapot, majd **a Service Bus**.

   ![Válassza ki a Service Bus – indítsa el a logikai alkalmazás létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. A **eseményindítók**válassza **egy vagy több üzenet érkezésekor (automatikus lezárás) üzenetsor**.

   ![Válassza ki az eseményindítót a logikai alkalmazás az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Hozzon létre egy service bus-kapcsolat.
   1. Adjon meg egy csatlakozási nevet, és válassza ki a Service Bus-névtér a listából. A következő képernyőn megnyílik.

      ![A logikai alkalmazás egy service bus-kapcsolat létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Válassza ki a service bus-szabályzat (RootManageSharedAccessKey). Válassza ki **létrehozás**.

      ![A logikai alkalmazás egy service bus-kapcsolat létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. A végső képernyőn a **üzenetsornév**, az üzenetsor, amely létrehozta a legördülő listából válassza ki. Adja meg `175` a **maximális üzenetszám**.

      ![Adja meg a maximális üzenetszám, a service bus-kapcsolat esetében a logikai alkalmazás](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Válassza ki **mentése** a módosítások mentéséhez, a Logic Apps Designer tetején lévő menüben.

### <a name="configure-the-logic-app-action"></a>A logikaialkalmazás-művelet konfigurálása

1. Hozzon létre egy SMTP-szolgáltatás kapcsolatot.

   1. Válassza az **Új lépés** lehetőséget. A **válasszon ki egy műveletet**, jelölje be a **összes** fülre.

   1. Típus `smtp` a keresőmezőbe, válassza ki a **SMTP** a keresési eredményekben szolgáltatásra, és válassza ki **E-mail küldése**.

      ![Az SMTP-kapcsolat létrehozása a logikai alkalmazásban, az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Adja meg az SMTP-információit a postaláda, és válassza ki **létrehozás**.

      ![Adja meg az SMTP-kapcsolati adatok a logikai alkalmazásban, az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Az SMTP-információit az első [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), és [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Szükség lehet tiltani az SSL-kapcsolat létesítésére. Ha újra engedélyezi az SSL, a kapcsolat létrejötte után ez a helyzet, tekintse meg a választható lépés, ez a szakasz végén.

   1. Az a **új paraméter hozzáadása** legördülő a **E-mail küldése** lépésben válassza **a**, **való**, **tulajdonos**és **törzs**. Kattintson vagy koppintson bárhol a kiválasztási mező bezárásához a képernyőn.

      ![SMTP-kapcsolat e-mail mezők kiválasztása](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Adja meg az e-mail-címe **a** és **való**, és `High temperature detected` a **tulajdonos** és **törzs**. Ha a **dinamikus tartalom hozzáadása az alkalmazások és a folyamatban használt összekötőkhöz** párbeszédpanel megjelenik, válassza ki **elrejtése** bezárásához. Ebben az oktatóanyagban nem használja a dinamikus tartalmak.

      ![Kitöltési SMTP kapcsolat e-mail mezők](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Válassza ki **mentése** menteni az SMTP-kapcsolat.

1. (Nem kötelező) Ha a kapcsolat létrehozása az e-mail-szolgáltatóhoz, és újra engedélyezi az SSL letiltásához, kövesse az alábbi lépéseket:

   1. Az a **logikai alkalmazás** panel alatt **Fejlesztőeszközök**, jelölje be **API-kapcsolatok**.

   1. API-kapcsolatok listájában jelölje ki az SMTP-kapcsolat.

   1. Az a **smtp API-kapcsolat** panel alatt **általános**válassza **szerkesztése API-kapcsolat**.

   1. Az a **API-kapcsolat szerkesztése** ablaktáblán válassza **SSL engedélyezése?** , írja be újra az e-mail-fiók jelszavát, és válassza ki **mentése**.

      ![A logikai alkalmazás az Azure Portalon lévő SMTP API-kapcsolat szerkesztése](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

A logikai alkalmazás már készen áll a feldolgozni a Service Bus-üzenetsorba érkező hőmérséklet-riasztások és értesítések küldéséhez az e-mail-fiókjába.

## <a name="test-the-logic-app"></a>A logikai alkalmazás tesztelése

1. Indítsa el az ügyfélalkalmazás az eszközön.

1. Ha egy fizikai eszközt használ, gondosan használata a megadott hőtérképrészlet érzékelő közelében megadott hőtérképrészlet forrás mindaddig, amíg a hőmérséklet meghaladja a 30 fok c Az online szimulátor használata, ha az Ügyfélkód véletlenszerűen kimeneti telemetriai üzeneteket, amelyeknek haladja meg 30.

1. A logikai alkalmazás által küldött e-mail értesítések fogadásának érdemes kezdenie.

   > [!NOTE]
   > Győződjön meg arról, hogy, az e-mailt küld a feladó identitása szükség lehet az e-mail-szolgáltató.

## <a name="next-steps"></a>További lépések

Sikeresen létrehozott egy logikai alkalmazást, amely csatlakozik az IoT hub és a postaláda hőmérséklet-figyelés és értesítések.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
