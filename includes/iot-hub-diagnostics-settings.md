---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0039c4eff3571a96cba1ab36136e0a588d78eb75
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50159128"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Naplózás a diagnosztikai beállítások engedélyezése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot.

2. Válassza ki **diagnosztikai beállítások**.

3. Válassza ki **diagnosztika bekapcsolása**.

   ![Diagnosztika bekapcsolása](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Nevezze el a diagnosztikai beállításokat.

5. Válassza ki kívánja küldeni a naplókat. A következő három lehetőség közül választhat:

   * Archiválás tárfiókba
   * Streamelés eseményközpontba
   * Küldés a Log Analyticsnek

6. Válassza ki a figyelni kívánt mely műveletek, és ezekhez a műveletekhez-naplók engedélyezésére. Diagnosztikai beállítások jelenthetik-e a műveletek a következők:

   * Kapcsolatok
   * Eszköztelemetria
   * Felhőből az eszközre irányuló üzenetek
   * Eszközművelet identitás
   * Fájlfeltöltések
   * Üzenetirányítás
   * Felhőalapú ikereszköz műveletek
   * Eszközről a felhőbe – az ikereszköz-műveletek
   * Ikereszköz-műveletek
   * Feladat műveletei
   * Közvetlen metódusok  

6. Az új beállítások mentéséhez. 

Ha kapcsolja be a diagnosztikai beállítások a PowerShell-lel szeretne, használja a következő kódot:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Új beállítások érvénybe léptetéséhez körülbelül 10 perc múlva. Ezt követően naplók jelennek meg a beállított archiválási célzott a **diagnosztikai beállítások** panelen. Konfiguruje se diagnostika kapcsolatos további információkért lásd: [gyűjtése és felhasználása a naplófájlok adatait az azure-erőforrások](../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).