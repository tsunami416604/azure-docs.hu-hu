### <a name="enable-logging-with-diagnostics-settings"></a>A diagnosztikai beállításokat a naplózás engedélyezése

1. Jelentkezzen be a [Azure-portálon] [ lnk-portal] , és keresse meg az IoT Hub.
1. Válassza ki **diagnosztikai beállítások**.
1. Válassza ki **a diagnosztika bekapcsolásához**.

   ![Diagnosztika engedélyezése][1]

1. Adjon meg egy nevet a diagnosztikai beállítások.
1. Válasszon hol szeretné elküldeni a naplókat. A következő három beállítás bármilyen kombinációját kiválaszthatja:
   * A tárolási fiók archiválása
   * Az eseményközpontok felé adatfolyam
   * A Naplóelemzési küldése
1. Válassza ki a figyelni kívánt milyen műveletek, és a naplókat, hogy ezek a műveletek engedélyezése. A diagnosztikai beállítások jelenthetik-e a műveletek a következők:
   * Kapcsolatok
   * Telemetriát
   * Felhő-eszközre küldött üzenetek
   * Eszköz identitása műveletek
   * Fájlfeltöltések
   * Üzenet-útválasztás
   * Felhő eszközre iker műveletek
   * Eszköz-felhő iker műveletek
   * A két műveletek
   * Feladat műveletek
   * Közvetlen metódusok  
1. Az új beállítások mentéséhez. 

Ha kapcsolja be a diagnosztikai beállításokat a PowerShell segítségével szeretne, használja a következő kódot:

```
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Új beállítások érvénybe körülbelül 10 perc. Ezt követően naplók jelenik meg a beállított archiválási cél a **diagnosztikai beállítások** panelen. Diagnosztika konfigurálásával kapcsolatos további információkért lásd: [gyűjtése és felhasználása az azure-erőforrások naplóadatait][lnk-diagnostics-settings].

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
