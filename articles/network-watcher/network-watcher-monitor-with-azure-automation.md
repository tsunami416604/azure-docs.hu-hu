---
title: VPN-átjárók hibáinak elhárítása és figyelése – Azure Automation
titleSuffix: Azure Network Watcher
description: Ez a cikk bemutatja, hogyan diagnosztizálják a helyszíni kapcsolatot az Azure Automation és a Network Watcher segítségével
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 74c9f44ff5fbbbb50bba1594d371633fd49857eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845042"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>VPN-átjárók figyelése a Hálózatfigyelő hibaelhárításával

A hálózati teljesítmény mélyebb betekintést nyer, ezért megbízható szolgáltatásokat nyújthat az ügyfeleknek. Ezért rendkívül fontos a hálózati kimaradási feltételek gyors észlelése és a kimaradási feltétel csökkentése érdekében korrekciós intézkedések meghozása. Az Azure Automation lehetővé teszi, hogy egy feladatot programozott módon valósítson meg és futtasson runbookokon keresztül. Az Azure Automation használatával tökéletes receptet hoz létre a folyamatos és proaktív hálózati figyeléshez és riasztáshoz.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv az alábbi rendszerkép egy többrétegű alkalmazás, a helyszíni kapcsolat vpn-átjáró és alagút használatával létrehozott. A VPN-átjáró működésének biztosítása kritikus fontosságú az alkalmazások teljesítménye szempontjából.

A runbook egy parancsfájllal jön létre a VPN-alagút kapcsolati állapotának ellenőrzésére, az Erőforrás hibaelhárítás i. segítségével a kapcsolatalagút állapotának ellenőrzéséhez. Ha az állapot nem kifogástalan, a rendszer e-mail-eseményindítót küld a rendszergazdáknak.

![Példaforgatókönyv][scenario]

Ez a forgatókönyv:

- Runbook létrehozása a `Start-AzureRmNetworkWatcherResourceTroubleshooting` parancsmag hívásával a kapcsolat állapotának elhárításához
- Ütemezés csatolása a runbookhoz

## <a name="before-you-begin"></a>Előkészületek

A forgatókönyv megkezdése előtt a következő előfeltételekre van szüksége:

- Azure-automatizálási fiók az Azure-ban. Győződjön meg arról, hogy az automation-fiók rendelkezik a legújabb modulokkal, és az AzureRM.Network modullal is rendelkezik. Az AzureRM.Network modul érhető el a modulkatalógusban, ha hozzá kell adnia az automatizálási fiókhoz.
- Az Azure Automationben konfigurálni kell egy hitelesítő adatokat. További információ az [Azure Automation biztonságáról](../automation/automation-security-overview.md)
- Érvényes SMTP-kiszolgáló (Office 365, helyszíni levelezés vagy más) és az Azure Automationben definiált hitelesítő adatok
- Konfigurált virtuális hálózati átjáró az Azure-ban.
- Egy meglévő tárfiók egy meglévő tárolót a naplók tárolására.

> [!NOTE]
> Az előző képen ábrázolt infrastruktúra illusztrációs célokat szolgál, és nem a jelen cikkben található lépésekkel jön létre.

### <a name="create-the-runbook"></a>A runbook létrehozása

A példa konfigurálásának első lépése a runbook létrehozása. Ez a példa egy run-as fiókot használ. Ha többet szeretne megtudni a run-as fiókokról, látogasson el [a Runbookok hitelesítése az Azure Run As fiókkal](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>1. lépés

Keresse meg az Azure Automation szolgáltatást az [Azure Portalon,](https://portal.azure.com) és kattintson a **Runbookok** elemre

![automatizálási fiók áttekintése][1]

### <a name="step-2"></a>2. lépés

Kattintson **a Runbook hozzáadása** a runbook létrehozási folyamatának elindításához.

![runbooks penge][2]

### <a name="step-3"></a>3. lépés

A **Gyors létrehozás csoportban**kattintson **az Új runbook létrehozása** elemre a runbook létrehozásához.

![runbook panel hozzáadása][3]

### <a name="step-4"></a>4. lépés

Ebben a lépésben nevet adunk a runbooknak, a példában **get-VPNGatewayStatus**néven. Fontos, hogy a runbook egy leíró nevet, és ajánlott, hogy adjon neki egy nevet, amely követi a szabványos PowerShell-elnevezési szabványok. Ebben a példában a Runbook típusa a **PowerShell,** a többi lehetőség a grafikus, a PowerShell-munkafolyamat és a grafikus PowerShell-munkafolyamat.

![runbook penge][4]

### <a name="step-5"></a>5. lépés

Ebben a lépésben a runbook jön létre, a következő kód példa a példa összes szükséges kódot. Az értéket tartalmazó \<kódot\> tartalmazó elemeket le kell cserélni az előfizetésből származó értékekkel.

Használja a következő kódot a **Mentés gombra kattintva**

```powershell
# Set these variables to the proper values for your environment
$o365AutomationCredential = "<Office 365 account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for Office 365 account
$cred = Get-AutomationPSCredential -Name $o365AutomationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>6. lépés

A runbook mentése után egy ütemezést kell csatolni hozzá a runbook indításának automatizálásához. A folyamat elindításához kattintson az **Ütemezés gombra.**

![6. lépés][6]

## <a name="link-a-schedule-to-the-runbook"></a>Ütemezés csatolása a runbookhoz

Új ütemezést kell létrehozni. Kattintson **az Ütemezés csatolása a runbookhoz**elemre.

![7. lépés][7]

### <a name="step-1"></a>1. lépés

Az **Ütemezés** panelen kattintson az **Új ütemezés létrehozása gombra.**

![8. lépés][8]

### <a name="step-2"></a>2. lépés

Az **Új ütemezés** panelen töltse ki az ütemezési adatokat. A beállítható értékek a következő listában találhatók:

- **Név** - Az ütemezés rövid neve.
- **Leírás** - az ütemezés leírása.
- **Indítás –** Ez az érték a dátum, az idő és az időzóna kombinációja, amely az ütemezés eseményindítóinak időpontját teszi ki.
- **Ismétlődés** – Ez az érték határozza meg az ütemezések ismétlődését.  Az érvényes értékek az **Egyszeri** vagy **ismétlődő**értékek.
- **Ismétlődés minden** – Az ütemezés ismétlődési időköze órákban, napokban, hetekben vagy hónapokban.
- **Lejárat beállítása** – Az érték határozza meg, hogy az ütemezés nek le kell-e járnia. Lehet állítani, hogy **Igen** vagy **Nem**. Ha igent választanak, érvényes dátumot és időpontot kell megadni.

> [!NOTE]
> Ha a runbook oka gyakrabban fut, mint minden órában, több ütemezést kell létrehozni különböző időközönként (azaz 15, 30, 45 perccel az óra után)

![9. lépés][9]

### <a name="step-3"></a>3. lépés

Kattintson a Mentés gombra az ütemezés runbookba való mentéséhez.

![10. lépés][10]

## <a name="next-steps"></a>További lépések

Most, hogy már tisztában van azzal, hogyan integrálhatja a Network Watcher hibaelhárítását az Azure Automationbe, ismerje meg, hogyan indíthatja el a csomagrögzítéseket a virtuális gépriasztásain az [Azure Network Watcher által kiváltott csomagrögzítés létrehozása című témakörben.](network-watcher-alert-triggered-packet-capture.md)

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
