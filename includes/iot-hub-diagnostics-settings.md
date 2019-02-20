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
ms.openlocfilehash: 150b800bfa6bfa20f10dbba7e8d55981ecb9df34
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56422803"
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

6. Az új beállítások mentéséhez. 

Ha kapcsolja be a diagnosztikai beállítások a PowerShell-lel szeretne, használja a következő kódot:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Új beállítások érvénybe léptetéséhez körülbelül 10 perc múlva. Ezt követően naplók jelennek meg a beállított archiválási célzott a **diagnosztikai beállítások** panelen. Konfiguruje se diagnostika kapcsolatos további információkért lásd: [gyűjtése és felhasználása a naplófájlok adatait az azure-erőforrások](../articles/azure-monitor/platform/diagnostic-logs-overview.md).