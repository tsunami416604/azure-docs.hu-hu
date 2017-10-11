---
title: "Az IoT távoli megfigyelési és az Azure Logic Apps értesítések |} Microsoft Docs"
description: "IoT az IoT hub figyelését hőmérséklet Azure Logic Apps használni, és automatikusan az e-mail értesítéseket küldhet a bármely észlelhető rendellenességeket postaládájához."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "figyelés, az iot-értesítések, IOT iot hőmérséklet figyelése"
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 7a611912ae55eb22103539dbba9f1a06aaa543b7
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Az IoT távoli figyelés és az értesítések az Azure Logic Apps csatlakoztatása az IoT-központ és postaláda

![Végpontok közötti diagramja](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Az Azure Logic Apps segítségével lépések sorozataként folyamatok automatizálásához. Logikai alkalmazás különböző szolgáltatások és protokollok keresztül is elérheti. Megkezdi az eseményindító például a "Ha a fiók hozzáadása" és a műveletek, például "a leküldéses értesítések küldését" egyik kombinációja követ. A szolgáltatás elérhetővé teszi a Logic Apps tökéletes megoldás az IoT-megoldás az IoT figyelésére, ilyen például a riasztás a rendellenességek észlelését, más használati forgatókönyvek között marad.

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti az IoT hub és a postaláda hőmérséklet figyelési és értesítések csatlakozó logikai alkalmazás létrehozása. Amikor a hőmérséklet 30 C fölött van, az ügyfélalkalmazás jelöli meg `temperatureAlert = "true"` küld az IoT hub üzenetben. Az üzenet a logikai alkalmazás e-mail értesítés küldése váltja ki.

## <a name="what-you-do"></a>Mit

* Service bus-névtér létrehozása, és egy sor felvétele.
* A végpont és útválasztási szabály hozzáadása az IoT hub.
* Hozzon létre, beállította és ellenőrizte a logikai alkalmazást.

## <a name="what-you-need"></a>Mi szükséges

* Az oktatóanyag [beállítani az eszközét](iot-hub-raspberry-pi-kit-node-get-started.md) fejeződött be, amely hozzá van rendelve az alábbi követelményeknek:
  * Aktív Azure-előfizetés.
  * Az előfizetéshez tartozó Azure IoT hub.
  * Egy ügyfélalkalmazást, amely üzeneteket küld az Azure IoT hub.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Service bus-névtér létrehozása, és egy sor felvétele

### <a name="create-a-service-bus-namespace"></a>Service bus-névtér létrehozása

1. Az a [Azure-portálon](https://portal.azure.com/), kattintson a **új** > **vállalati integrációs** > **Service Bus**.
1. Adja meg a következő információkat:

   **Név**: a service bus nevét.

   **A tarifacsomag**: kattintson a **alapvető** > **válasszon**. Az alapszintű rétegben is elegendő ehhez az oktatóanyaghoz.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: ugyanazt a helyet, amely az IoT hub használja.
1. Kattintson a **Create** (Létrehozás) gombra.

   ![A service bus-névtér létrehozása az Azure portálon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Adja hozzá a service bus-üzenetsorba

1. Nyissa meg a service bus-névtér, és kattintson a **+ várólista**.
1. Adja meg a várólista nevét, és kattintson a **létrehozása**.
1. Nyissa meg a service bus-üzenetsorba, és kattintson a **megosztott elérési házirendek** > **+ Hozzáadás**.
1. Adjon meg egy nevet a házirend, jelölőnégyzet **kezelése**, és kattintson a **létrehozása**.

   ![A service bus-üzenetsorba hozzáadása az Azure-portálon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>A végpont és útválasztási szabály hozzáadása az IoT hub

### <a name="add-an-endpoint"></a>Végpont hozzáadása

1. Nyissa meg az IoT hub, kattintson a végpontok > + Hozzáadás.
1. Adja meg a következő információkat:

   **Név**: a végpont neve.

   **Típusú végpont**: válasszon **Service Bus-üzenetsorba**.

   **Service Bus-névtér**: válassza ki a létrehozott névtér.

   **Service Bus-üzenetsorba**: válassza ki a létrehozott üzenetsorba.
1. Kattintson az **OK** gombra.

   ![A végpont hozzáadása az Azure-portálon az IoT hub](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Útválasztási szabály hozzáadása

1. Kattintson az IoT hub **útvonalak** > **+ Hozzáadás**.
1. Adja meg a következő információkat:

   **Név**: az útválasztási szabály nevét.

   **Az adatforrás**: válasszon **DeviceMessages**.

   **Végpont**: válassza ki a létrehozott végpontot.

   **Lekérdezési karakterlánc**: Adjon meg `temperatureAlert = "true"`.
1. Kattintson a **Save** (Mentés) gombra.

   ![Útválasztási szabály hozzáadása az Azure-portálon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Hozza létre és konfigurálja a logikai alkalmazás

### <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

1. Az a [Azure-portálon](https://portal.azure.com/), kattintson a **új** > **vállalati integrációs** > **logikai alkalmazás**.
1. Adja meg a következő információkat:

   **Név**: a logikai alkalmazás nevét.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: ugyanazt a helyet, amely az IoT hub használja.
1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="configure-the-logic-app"></a>A logikai alkalmazás konfigurálása

1. Nyissa meg a logikai alkalmazást, amely megnyitja a Logic Apps Tervező be.
1. A Logic Apps tervezőben kattintson **üres logikai alkalmazás**.

   ![Indítsa el egy üres logikai alkalmazást az Azure-portálon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Kattintson a **Service Bus**.

   ![Válassza ki a Service Bus számára, hogy a logikai alkalmazás létrehozása az Azure-portálon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Kattintson a **Service Bus – egy vagy több üzenet a várólistában egyszerre várakozó (automatikusan hajthat végre) érkezésekor**.
1. Hozzon létre egy service bus-kapcsolat.
   1. Adja meg a kapcsolat neve.
   1. Kattintson a service bus-névtér > a service bus házirend > **létrehozása**.

      ![Hozzon létre egy service bus kapcsolatot a Logic Apps alkalmazást az Azure-portálon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Kattintson a **Folytatás** a service bus-kapcsolat létrehozása után.
   1. Jelölje ki a létrehozott várólistát, és írja be `175` a **maximális üzenetek száma**

      ![A service bus kapcsolati üzenetek maximális számát adja meg a Logic Apps alkalmazást](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Kattintson a "Mentés" gombra a módosítások mentéséhez.

1. SMTP-szolgáltatás kapcsolat létrehozásához.
   1. Kattintson a **új lépés** > **művelet hozzáadása**.
   1. Típus `SMTP`, kattintson a **SMTP** a keresési eredmény szolgáltatásra, és kattintson a **SMTP - E-mail küldése**.

      ![Az SMTP-kapcsolat létrehozása a Logic Apps alkalmazást az Azure-portálon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Adja meg a postaláda az SMTP-adatokat, és kattintson **létrehozása**.

      ![SMTP-kapcsolati adatok adja meg a Logic Apps alkalmazást az Azure-portálon](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Az SMTP adatait az beszerzése [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en), és [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).
   1. Adja meg az e-mail címet a **a** és **való**, és `High temperature detected` a **tulajdonos** és **törzs**.
   1. Kattintson a **Save** (Mentés) gombra.

A logikai alkalmazás megfelelően működik történő mentésekor.

## <a name="test-the-logic-app"></a>A logikai alkalmazás tesztelése

1. Indítsa el az ügyfélalkalmazás az eszköz üzembe helyezett [ESP8266 csatlakozzon az Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. A környezet hőmérséklet körül fent 30 c kell SensorTag növelése Például világos a gyertyák a SensorTag körül.
1. A logikai alkalmazás által küldött e-mailben értesítést kell kapnia.

   > [!NOTE]
   > Az e-mail-szolgáltató kell gondoskodjon róla, hogy Ön az e-mailt küld a feladónak identitásának ellenőrzéséhez.

## <a name="next-steps"></a>Következő lépések

Sikeresen létrehozta a logikai alkalmazás, amely összeköti az IoT hub és a postaláda hőmérséklet figyelési és értesítések.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
