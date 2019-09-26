---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2498711a5b7e5bce29cd0054ba40257f8f996d43
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266823"
---
### <a name="enable-logging-with-diagnostics-settings"></a>A naplózás engedélyezése a diagnosztikai beállításokkal

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot.

2. Válassza a **diagnosztikai beállítások**lehetőséget.

3. Válassza ki **diagnosztika bekapcsolása**.

   ![Diagnosztika bekapcsolása](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Adjon nevet a diagnosztikai beállításoknak.

5. Válassza ki, hová szeretné elküldeni a naplókat. A három lehetőség bármely kombinációját kiválaszthatja:

   * Archiválás tárfiókba
   * Streamelés eseményközpontba
   * Küldés a Log Analytics szolgáltatásba

6. Válassza ki a figyelni kívánt műveleteket, és engedélyezze a naplók számára a műveleteket. A diagnosztikai beállítások által a következő műveleteket lehet jelenteni:

   * Kapcsolatok
   * Eszköz telemetria
   * Felhőből az eszközre irányuló üzenetek
   * Eszköz-identitási műveletek
   * Fájlfeltöltések
   * Üzenetirányítás
   * A felhőből az eszközre irányuló kettős műveletek
   * Az eszközről a felhőbe irányuló kettős művelet
   * Kettős művelet
   * Feladatok műveletei
   * Közvetlen metódusok  
   * Elosztott nyomkövetés (előzetes verzió)
   * Konfigurációk
   * Eszköz streamek
   * Eszköz metrikái

6. Mentse az új beállításokat. 

Ha be szeretné kapcsolni a diagnosztika beállításait a PowerShell használatával, használja a következő kódot:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Új beállítások érvénybe léptetéséhez körülbelül 10 perc múlva. Ezt követően a naplók a **diagnosztikai beállítások** panel konfigurált archiválási célpontjában jelennek meg. A diagnosztika konfigurálásával kapcsolatos további információkért lásd: [adatok gyűjtése és felhasználása az Azure-erőforrásokból](../articles/azure-monitor/platform/resource-logs-overview.md).
