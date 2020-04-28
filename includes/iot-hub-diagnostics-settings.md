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
ms.openlocfilehash: 3778ec2fac13aee29ce361402a535ca70fd56c33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75750705"
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
