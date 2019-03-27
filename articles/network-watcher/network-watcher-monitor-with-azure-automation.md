---
title: Hibaelhárítás Azure Network Watcher VPN-átjárók figyelése |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan a az Azure Automation és a Network Watcher helyszíni kapcsolatok diagnosztizálása
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 4995d7ae846652c374a289603f29f88f6f56dfef
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485493"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>A Network Watcher troubleshooting VPN-átjárók figyelése

Részletes információszerzéshez a hálózati teljesítményt, kritikus fontosságú megbízható szolgáltatásokat nyújtsanak az ügyfeleinek. Ezért fontos hálózati kimaradás feltételek gyors észlelése és a szolgáltatáskimaradás feltétel csökkentése érdekében javítási intézkedéseket. Az Azure Automation lehetővé teszi végrehajtása és a egy feladat futtatása programozott módon a runbookok használatával. Azure Automation használatával létrehoz egy tökéletes receptet folyamatos és proaktív hálózati monitorozási és riasztási végrehajtásához.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv az alábbi képen egy többrétegű alkalmazást a helyi kapcsolat jött létre a VPN-átjáró és alagút. Az alkalmazások teljesítményének biztosítása a VPN-átjáró működik és fut fontos.

Egy runbook jön létre egy parancsfájlt, amely a VPN-alagút, kapcsolat bújtatási állapotának ellenőrzése az erőforrás hibaelhárítás API-t használó kapcsolat állapotának ellenőrzése. Ha az állapot nem kifogástalan, egy e-mail-trigger rendszergazdák érkezik.

![Példaforgatókönyv][scenario]

Ebben a forgatókönyvben lesz:

- Hozzon létre egy runbook meghívása a `Start-AzureRmNetworkWatcherResourceTroubleshooting` parancsmagot, hogy a kapcsolat állapotának hibaelhárítása
- Ütemezés összekapcsolása a runbook

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben a Kezdés előtt az alábbi előfeltételekkel kell rendelkeznie:

- Egy Azure automation-fiókot az Azure-ban. Győződjön meg arról, hogy az automation-fiók rendelkezik a legújabb modulok az AzureRM.Network modult is tartalmaz. Az AzureRM.Network modul érhető el a modul katalógus Ha adja hozzá az automation-fiók van szüksége.
- Az Azure Automation konfigurálása hitelesítő adatra kell rendelkeznie. További információ: [Azure Automation biztonsága](../automation/automation-security-overview.md)
- Egy érvényes SMTP-kiszolgáló (Office 365-höz, a helyszíni e-mailek vagy egy másik) és az Azure Automation szolgáltatásban meghatározott hitelesítő adatokkal
- A konfigurált virtuális hálózati átjáró az Azure-ban.
- Egy meglévő tárfiókot a naplók tárolására egy meglévő tárolóval.

> [!NOTE]
> A fejezet az előző képen infrastruktúra illusztrációs célokat szolgálnak, és nem jönnek létre ebben a cikkben található lépéseket.

### <a name="create-the-runbook"></a>A runbook létrehozása

Az első lépés a példa konfigurálása, ha a runbook. Ebben a példában egy futtató fiókot használja. Futtató fiókok kapcsolatos további információkért látogasson el a [Runbookok hitelesítése Azure-beli futtató fiókkal](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>1. lépés

Az Azure Automationt, keresse meg a [az Azure portal](https://portal.azure.com) kattintson **Runbookok**

![Automation-fiókok áttekintése][1]

### <a name="step-2"></a>2. lépés

Kattintson a **forgatókönyv hozzáadása** a runbook létrehozásának megkezdéséhez.

![runbookok panel][2]

### <a name="step-3"></a>3. lépés

A **Gyorslétrehozás**, kattintson a **hozzon létre egy új runbookot** hozza létre a runbookot.

![Adjon hozzá egy runbook panel][3]

### <a name="step-4"></a>4. lépés

Ebben a lépésben azt adja meg a runbook nevét, a példában nevű **Get-VPNGatewayStatus**. Ez nem lényeges, hogy a runbook egy leíró nevet, és javasolt nevezi el a következő PowerShell szabványos elnevezési szabványait. Ebben a példában a runbook típusa **PowerShell**, a többi beállítást a rendszer grafikus, PowerShell-munkafolyamat, és a grafikus PowerShell-munkafolyamat.

![forgatókönyv panel][4]

### <a name="step-5"></a>5. lépés

Ebben a lépésben a runbook létrehozása a következő példakód a példában a szükséges összes kódot biztosít az. Az elemek, amelyek tartalmazzák a kódban \<érték\> kell cserélni az értékeket az előfizetésből.

Kattintson a következő kód használatával **mentése**

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

A runbook a mentés után ütemezés szerint kell társítani, hogy a kezdete a forgatókönyv automatizálása. A folyamat elindításához kattintson **ütemezés**.

![6. lépés][6]

## <a name="link-a-schedule-to-the-runbook"></a>Ütemezés összekapcsolása a runbook

Létre kell hozni egy új ütemezést. Kattintson a **összekapcsolhat egy ütemezést a runbook**.

![7. lépés][7]

### <a name="step-1"></a>1. lépés

Az a **ütemezés** panelen kattintson a **új ütemezés létrehozása**

![8. lépés][8]

### <a name="step-2"></a>2. lépés

Az a **új ütemezés** panelen töltse ki az ütemezési információkat. A megadható értékek a következők:

- **Név** -ütemezés rövid nevét.
- **Leírás** – egy leírást az ütemezés.
- **Elindul** – ezt az értéket a dátum, idő és időzóna alkotó az idő az ütemezési eseményindítókról kombinációját.
- **Ismétlődési** – Ez az érték határozza meg a ütemezése ismétlődésének beolvasása.  Érvényes értékek a következők **egyszer** vagy **ismétlődő**.
- **Ismétlődés minden** -óra, nap, hét vagy hónapok az ütemezés ismétlődési időköze.
- **Beállíthatja a lejárati idejét** -érték határozza meg, ha az ütemezés lejárnak, vagy sem. Állítható **Igen** vagy **nem**. Egy érvényes dátumot és időpontot is, ha az Igen választ adni.

> [!NOTE]
> Egy runbook minden órában gyakoribb futtatásához rendelkeznie kell, ha több ütemezés kell létrehozni, különböző időközönként (azaz, 15, 30, 45 perc minden óra után)

![9. lépés][9]

### <a name="step-3"></a>3. lépés

Kattintson a Mentés gombra az ütemezés a runbookhoz.

![10. lépés][10]

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik egy-egy Network Watcher hibaelhárítása az Azure Automation integrálása, megtudhatja, hogyan aktiválhat a virtuális gép riasztásokat csomagrögzítés funkcionáló [Azure Network WatcherHozzonlétreegyaktiváltriasztáscsomagrögzítés](network-watcher-alert-triggered-packet-capture.md).

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
