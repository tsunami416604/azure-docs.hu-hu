---
title: Távoli megfigyelés IoT és az értesítések az Azure Logic Apps |} A Microsoft Docs
description: IoT hőmérséklet figyelése az IoT hubon az Azure Logic Apps használatával, és automatikusan a postaláda az összes észlelt rendellenességek e-mail értesítések küldéséhez.
author: rangv
manager: ''
keywords: IOT, iot-értesítések, monitoring iot hőmérséklet figyelése
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: adda4e948c11f84517b1e8dd01e6cfe42155e1ca
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409441"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT távoli figyelés és értesítések az Azure Logic Apps csatlakoztatása az IoT hub és a postaláda

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Az Azure Logic Apps lehetővé teszi folyamatok automatizálásához lépések sorozataként. Logikai alkalmazás csatlakozhatnak különböző szolgáltatások és protokollok között. Ez egy eseményindítóval kezdődik például "Amikor egy fiókot hozzáadnak", és a műveletek, például a "leküldéses értesítések küldéséhez" egy kombinációját követ. Ez a funkció lehetővé teszi a Logic Apps tökéletes IoT-megoldás az IoT figyelésére, ilyen például a riasztás a rendellenességeket, más használati forgatókönyvek között támogatott.

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti, hogyan hozhat létre egy logikai alkalmazást, amely csatlakozik az IoT hub és a postaláda hőmérséklet-figyelés és értesítések. Amikor a hőmérséklet 30 C fölött van, az ügyfélalkalmazás jelöli meg `temperatureAlert = "true"` küld az IoT hub az üzenetben. Az üzenet aktivál a logikai alkalmazás e-mailben értesítést küldünk.

## <a name="what-you-do"></a>TEENDŐ

* Hozzon létre egy service bus-névtér és üzenetsor hozzáadása.
* A végpont és a egy útválasztási szabály hozzáadása az IoT hubnak.
* Létrehozása, konfigurálása, és a egy logikai alkalmazás teszteléséhez.

## <a name="what-you-need"></a>Mi szükséges

* Az oktatóanyag [beállítani eszközét](iot-hub-raspberry-pi-kit-node-get-started.md) fejeződött be, amely magában foglalja az alábbi követelményeknek:
  * Aktív Azure-előfizetés.
  * Az Azure IoT hub az előfizetéséhez.
  * Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Service bus-névtér létrehozása és a egy üzenetsor hozzáadása

### <a name="create-a-service-bus-namespace"></a>Service bus-névtér létrehozása

1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **erőforrás létrehozása** > **vállalati integráció** > **a Service Bus**.
1. Adja meg az alábbi információkat:

   **Név**: a service bus nevét.

   **A tarifacsomag**: kattintson a **alapszintű** > **kiválasztása**. Az alapszintű csomag is elegendő ehhez az oktatóanyaghoz.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: ugyanazt a helyet, amely az IoT hub használja.
1. Kattintson a **Create** (Létrehozás) gombra.

   ![A service bus-névtér létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Adjon hozzá egy service bus-üzenetsorba

1. Nyissa meg a service bus-névteret, és kattintson a **+ üzenetsor**.
1. Adja meg a várólista nevét, és kattintson a **létrehozás**.
1. Nyissa meg a service bus-üzenetsorba, és kattintson a **megosztott elérési házirendek** > **+ Hozzáadás**.
1. Adjon meg egy nevet a házirendet, jelölőnégyzet **kezelés**, és kattintson a **létrehozás**.

   ![Az Azure Portalon adjon hozzá egy service bus-üzenetsorba](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Az IoT hubhoz a végpont és a egy útválasztási szabály hozzáadása

### <a name="add-an-endpoint"></a>Végpont hozzáadása

1. Nyissa meg az IoT hubhoz, kattintson a végpontok > + Hozzáadás.
1. Adja meg a következő információkat:

   **Név**: a végpont nevét.

   **Végpont típusa**: válasszon **Service Bus-üzenetsor**.

   **Service Bus-névtér**: válassza ki a létrehozott névtérre.

   **Service Bus-üzenetsorba**: válassza ki a létrehozott sorban.
1. Kattintson az **OK** gombra.

   ![Az Azure Portalon az IoT hub-végpont hozzáadása](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Útválasztási szabály hozzáadása

1. Kattintson az IoT hub **útvonalak** > **+ Hozzáadás**.
1. Adja meg a következő információkat:

   **Név**: az útválasztási szabály neve.

   **Az adatforrás**: válasszon **DeviceMessages**.

   **Végpont**: válassza ki a létrehozott végpont.

   **Lekérdezési karakterlánc**: Adja meg `temperatureAlert = "true"`.
1. Kattintson a **Save** (Mentés) gombra.

   ![Útválasztási szabály hozzáadása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>A logikai alkalmazás létrehozása és konfigurálása

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

1. Az a [az Azure portal](https://portal.azure.com/), kattintson a **erőforrás létrehozása** > **vállalati integráció** > **logikai alkalmazás**.
1. Adja meg a következő információkat:

   **Név**: a logikai alkalmazás nevét.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: ugyanazt a helyet, amely az IoT hub használja.
1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="configure-the-logic-app"></a>A logikai alkalmazás konfigurálása

1. Nyissa meg a logikai alkalmazást, megnyílik a Logic Apps Designerben be.
1. A Logic Apps Designerben kattintson **üres logikai alkalmazás**.

   ![Kezdje egy üres logikai alkalmazás az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Kattintson a **Service Bus**.

   ![Válassza ki a Service Bus – indítsa el a logikai alkalmazás létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Kattintson a **a Service Bus – Ha egy vagy több üzenet érkezik egy üzenetsorban (automatikus lezárás)**.
1. Hozzon létre egy service bus-kapcsolat.
   1. Adja meg a kapcsolat nevét.
   1. Kattintson a service bus-névtér > a service bus-szabályzat > **létrehozás**.

      ![A logikai alkalmazás egy service bus-kapcsolat létrehozása az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Kattintson a **Folytatás** a service bus-kapcsolat létrehozása után.
   1. Válassza ki az Ön által létrehozott üzenetsorba, és adja meg `175` a **maximális üzenetszám**

      ![Adja meg a maximális üzenetszám, a service bus-kapcsolat esetében a logikai alkalmazás](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Kattintson a "Mentés" gombra kattintva mentse a módosításokat.

1. Hozzon létre egy SMTP-szolgáltatás kapcsolatot.
   1. Kattintson a **új lépés** > **művelet hozzáadása**.
   1. Típus `SMTP`, kattintson a **SMTP** a keresési eredményekben szolgáltatásra, és kattintson a **SMTP - E-mail küldése**.

      ![Az SMTP-kapcsolat létrehozása a logikai alkalmazásban, az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Adja meg az SMTP-információit a postaláda, és kattintson a **létrehozás**.

      ![Adja meg az SMTP-kapcsolati adatok a logikai alkalmazásban, az Azure Portalon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Az SMTP-információit az első [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), és [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).
   1. Adja meg az e-mail-címe **a** és **való**, és `High temperature detected` a **tulajdonos** és **törzs**.
   1. Kattintson a **Save** (Mentés) gombra.

A logikai alkalmazás megfelelően működik, mentésekor.

## <a name="test-the-logic-app"></a>A logikai alkalmazás tesztelése

1. Az ügyfélalkalmazás úgy, hogy az eszköz Start [ESP8266 csatlakoztatása Azure IoT hubra](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. A környezeti hőmérséklet körül 30 c meghaladta a SensorTag növelése Ha például világos körül a SensorTag gyertyák színét.
1. A logikai alkalmazás által küldött e-mail-értesítést kell kapnia.

   > [!NOTE]
   > Győződjön meg arról, hogy, az e-mailt küld a feladó identitása szükség lehet az e-mail-szolgáltató.

## <a name="next-steps"></a>További lépések

Sikeresen létrehozott egy logikai alkalmazást, amely csatlakozik az IoT hub és a postaláda hőmérséklet-figyelés és értesítések.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
