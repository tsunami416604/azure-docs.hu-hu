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
ms.openlocfilehash: 6f7772eb7f2c500bbb58c391b1bc4b7a73141699
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675755"
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
   * Elosztott nyomkövetést (előzetes verzió)
   * Konfigurációk
   * Eszköz-adatfolyamok
   * Eszközmetrikák

6. Az új beállítások mentéséhez. 

Ha kapcsolja be a diagnosztikai beállítások a PowerShell-lel szeretne, használja a következő kódot:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Új beállítások érvénybe léptetéséhez körülbelül 10 perc múlva. Ezt követően naplók jelennek meg a beállított archiválási célzott a **diagnosztikai beállítások** panelen. Konfiguruje se diagnostika kapcsolatos további információkért lásd: [gyűjtése és felhasználása a naplófájlok adatait az azure-erőforrások](../articles/azure-monitor/platform/diagnostic-logs-overview.md).