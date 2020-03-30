---
title: 'Azure VPN-átjáró: Riasztások konfigurálása diagnosztikai naplóeseményeken'
description: A VPN-átjáró diagnosztikai naplóeseményein lévő riasztások konfigurálásának lépései
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: b587fa31f1aa42e8dbd5a9d2430c0117891f2091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239790"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Riasztások beállítása a VPN-átjáró diagnosztikai naplóeseményeire vonatkozóan

Ez a cikk segít riasztások at az Azure VPN-átjáró diagnosztikai naplóesemények az Azure Log Analytics használatával riasztások beállítása. 

Az Azure-ban a következő naplók érhetők el:

|***Név*** | ***Leírás*** |
|---        | ---               |
|GatewayDiagnosticLog (Átjáródiagnosztikai napló) | Diagnosztikai naplókat tartalmaz az átjáró konfigurációs eseményeihez, az elsődleges módosításokhoz és a karbantartási eseményekhez |
|TunnelDiagnosticLog (TunnelDiagnosticLog) | Bújtatási állapotváltozási eseményeket tartalmaz. A bújtatási kapcsolódási/kapcsolatbontási események nek van egy összefoglaló oka az állapotváltozásra, ha van ilyen |
|RouteDiagnosticLog (Útvonaldiagnosztikai napló) | Az átjárón előforduló statikus útvonalak és BGP-események változásainak naplózása |
|IKEDiagnosticLog | Az internetes kulcscsere vezérlőüzeneteinek és eseményeinek naplózása az átjárón |
|P2SDiagnosticLog | Az átjárón lévő pont-hely vezérlőüzenetek és események naplózása |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Riasztások beállítása az Azure Portalon

A következő példalépések riasztást hoznak létre egy olyan lekapcsolási eseményhez, amely egy helyek közötti VPN-alagutat foglal magában:


1. Az Azure Portalon keresse meg a **Log Analytics** elemet az **Összes szolgáltatás** csoportban, és válassza a **Log Analytics-munkaterületeket.**

   ![A Log Analytics-munkaterületekre való belépéshez kijelölt beállítások](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Létrehozás")

2. Válassza a **Létrehozás gombot** a **Log Analytics** lapon.

   ![Az Analytics naplózási lapja a Létrehozás gombbal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Válassza ezt:")

3. Válassza **az Új létrehozása lehetőséget,** és töltse ki a részleteket.

   ![A Log Analytics-munkaterület létrehozásának részletei](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Válassza ezt:")

4. Keresse meg a VPN-átjárót a **Monitor** > **diagnosztika beállításai** panelen.

   ![A VPN-átjáró keresésének kiválasztása a Diagnosztikai beállítások ban](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Válassza ezt:")

5. A diagnosztika bekapcsolásához kattintson duplán az átjáróra, majd válassza **a Diagnosztika bekapcsolása**lehetőséget.

   ![A diagnosztika bekapcsolásával kapcsolatos beállítások](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Válassza ezt:")

6. Töltse ki a részleteket, és győződjön meg arról, hogy **a Küldés a Log Analytics** és **tunnelDiagnosticLog** ki vannak jelölve. Válassza ki a 3.

   ![Bejelölve jelölőnégyzetek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Válassza ezt:")

   > [!NOTE]
   > Az adatok kezdetben néhány órába is beletelhetnek.

7. Nyissa meg a virtuális hálózati átjáró erőforrás áttekintését, és válassza a **Figyelés** lapon a **Riasztások** lehetőséget. Ezután hozzon létre egy új riasztási szabályt, vagy szerkesztsen egy meglévő riasztási szabályt.

   ![Új figyelmeztetési szabály létrehozásának kijelölése](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Válassza ezt:")

   ![pontról helyre](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Válassza ezt:")
8. Válassza ki a Log Analytics munkaterületet és az erőforrást.

   ![Kijelölések munkaterülethez és erőforráshoz](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Válassza ezt:")

9. A **Feltétel hozzáadása csoportban**válassza az **Egyéni naplókeresés** lehetőséget jellogikaként.

   ![Egyéni naplókeresés kijelölések](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Válassza ezt:")

10. Adja meg az alábbi lekérdezést a **Keresési lekérdezés** szövegmezőben. Cserélje le az értékeket a <> és timegenerated szükség szerint.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Állítsa a küszöbértéket 0-ra, és válassza **a Kész gombot.**

    ![Lekérdezés bevitele és küszöbérték kiválasztása](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Válassza ezt:")

11. A **Szabály létrehozása** lapon válassza az **Új létrehozása lehetőséget** a **MŰVELETCSOPORTOK** szakaszban. Töltse ki a részleteket, és válassza **az OK gombot.**

    ![Új műveletcsoport részletei](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Válassza ezt:")

12. A **Szabály létrehozása** lapon adja meg a **Műveletek testreszabása** részleteket, és győződjön meg arról, hogy a megfelelő név jelenik meg az **ACTION GROUP NAME** szakaszban. A szabály létrehozásához válassza a **Figyelmeztetési szabály létrehozása** lehetőséget.

    ![Kijelölés szabály létrehozásához](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Válassza ezt:")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Riasztások beállítása a PowerShell használatával

A következő példa lépésekkel hozzon létre egy riasztást egy lekapcsolási esemény, amely magában foglalja a helyek közötti VPN-alagút.

1. Log Analytics-munkaterület létrehozása:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. A VPN-átjáró diagnosztikájának bekapcsolása:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Műveletcsoport létrehozása.

   Ez a kód létrehoz egy műveletcsoportot, amely e-mail értesítést küld a riasztás aktiválásakor:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Riasztási szabály létrehozása egyéni naplókeresés alapján:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>További lépések

A bújtatási metrikákriasztási beállításokkonfigurálása a [VPN-átjáró metrikáihoz](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)című értesítések beállítása című témakörben található.
