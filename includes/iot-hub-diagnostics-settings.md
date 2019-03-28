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
ms.openlocfilehash: dd5dc53311c8611a4ca4d174401bba797fe5c4b1
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505744"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Naplózás a diagnosztikai beállítások engedélyezése

[!INCLUDE [updated-for-az](./updated-for-az.md)]

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
   * Elosztott nyomkövetést (előzetes verzió)
   * Konfigurációk
   * Eszköz-adatfolyamok
   * Eszközmetrikák

6. Az új beállítások mentéséhez. 

Ha kapcsolja be a diagnosztikai beállítások a PowerShell-lel szeretne, használja a következő kódot:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Új beállítások érvénybe léptetéséhez körülbelül 10 perc múlva. Ezt követően naplók jelennek meg a beállított archiválási célzott a **diagnosztikai beállítások** panelen. Konfiguruje se diagnostika kapcsolatos további információkért lásd: [gyűjtése és felhasználása a naplófájlok adatait az azure-erőforrások](../articles/azure-monitor/platform/diagnostic-logs-overview.md).
