---
title: fájlbefoglalás
description: fájlbefoglalás
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f3cab4909937bbf5ccb0f72b194b08810c0487c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792020"
---
### <a name="enable-logging-with-diagnostics-settings"></a>A naplózás engedélyezése a diagnosztikai beállításokkal

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.

2. Válassza a **diagnosztikai beállítások**lehetőséget.

3. Kattintson **a diagnosztika bekapcsolása**elemre.

   ![Diagnosztika bekapcsolása](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Adjon nevet a diagnosztikai beállításoknak.

5. Válassza ki, hová szeretné elküldeni a naplókat. A három lehetőség bármely kombinációját kiválaszthatja:

   * Archiválás tárfiókba
   * Streamelés eseményközpontba
   * Küldés a Log Analyticsnek

6. Válassza ki a figyelni kívánt műveleteket, és engedélyezze a naplók számára a műveleteket. A diagnosztikai beállítások által a következő műveleteket lehet jelenteni:

   * Kapcsolatok
   * Eszköz telemetria
   * Felhőből az eszközre irányuló üzenetek
   * Eszköz-identitási műveletek
   * Fájlfeltöltés
   * Üzenetek útválasztása
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

Az új beállítások körülbelül 10 percen belül lépnek érvénybe. Ezt követően a naplók a **diagnosztikai beállítások** panel konfigurált archiválási célpontjában jelennek meg. A diagnosztika konfigurálásával kapcsolatos további információkért lásd: [adatok gyűjtése és felhasználása az Azure-erőforrásokból](../articles/azure-monitor/platform/platform-logs-overview.md).
