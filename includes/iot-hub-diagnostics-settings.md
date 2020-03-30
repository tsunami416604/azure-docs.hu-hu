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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75750705"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Naplózás engedélyezése diagnosztikai beállításokkal

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT-központot.

2. Válassza **a Diagnosztikai beállítások lehetőséget**.

3. Válassza **a Diagnosztika bekapcsolása**lehetőséget.

   ![Diagnosztika bekapcsolása](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Adjon nevet a diagnosztikai beállításoknak.

5. Válassza ki, hogy hová szeretné elküldeni a naplókat. A három lehetőség közül tetszőleges kombinációt választhat:

   * Archiválás tárfiókba
   * Streamelés eseményközpontba
   * Küldés a Log Analyticsnek

6. Válassza ki, hogy mely műveleteket szeretné figyelni, és engedélyezze a naplókat ezekhez a műveletekhez. A diagnosztikai beállítások a következők:

   * Kapcsolatok
   * Eszköztelemetria
   * Felhőből eszközre irányuló üzenetek
   * Eszközidentitás-műveletek
   * Fájlfeltöltések
   * Üzenetek útválasztása
   * Felhőből az eszközre irányuló ikerműveletek
   * Az eszközök közötti ikerműveletek
   * Ikerműveletek
   * Feladatműveletek
   * Közvetlen metódusok  
   * Elosztott nyomkövetés (előzetes verzió)
   * Konfigurációk
   * Eszközadatfolyamok
   * Eszközmérő számok

6. Mentse az új beállításokat. 

Ha be szeretné kapcsolni a diagnosztikai beállításokat a PowerShell használatával, használja a következő kódot:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Az új beállítások körülbelül 10 perc múlva lépnek érvénybe. Ezt követően a naplók megjelennek a diagnosztikai beállítások panelen a konfigurált **archiválási** célban. A diagnosztika konfigurálásáról további információt az [Azure-erőforrások naplóadatainak gyűjtése és felhasználása című témakörben talál.](../articles/azure-monitor/platform/platform-logs-overview.md)
