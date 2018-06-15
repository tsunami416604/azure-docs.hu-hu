---
title: VPN-átjárók Azure hálózati figyelőt hibaelhárítás figyelése |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan diagnosztizálhatja a helyi kapcsolat az Azure Automation- és hálózati figyelőt
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
ms.openlocfilehash: a102916bb0626f5b110fb134a8a25c902cfaefe7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598132"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>VPN-átjárók a hálózati figyelőt hibaelhárításban figyelése

Való mélyebben elemezheti a hálózat teljesítménye szempontjából fontos a megbízható szolgáltatásokat nyújtsanak az ügyfeleinek. Ezért fontos hálózati kimaradás körülmények gyorsan észlelését és csökkenthető a leállás feltétel intézkedéseket. Azure Automation szolgáltatásbeli bevezetése, valamint a feladat futtatása programozott módon runbookok keresztül teszi lehetővé. Egy tökéletes receptet folyamatos és proaktív hálózati figyelés és riasztás végrehajtásához Azure Automation használatával hoz létre.

## <a name="scenario"></a>Forgatókönyv

Az alábbi ábrán a forgatókönyv egy többrétegű alkalmazást, a helyi kapcsolat létrejött, a VPN-átjáró és alagút használatával. Annak biztosítása, a VPN-átjáró működik-e és fut-létfontosságú alkalmazások teljesítménye.

Egy runbook egy parancsfájlt, amely a VPN-alagúton, az erőforrás hibaelhárítási API-val való kapcsolat bújtatási állapotának ellenőrzése kapcsolat állapotának ellenőrzése hozza létre. Ha az állapot nem kifogástalan, egy e-mailek eseményindító rendszergazdák érkezik.

![Példaforgatókönyv][scenario]

Ez a forgatókönyv tartalma:

- Hozzon létre egy runbook meghívása a `Start-AzureRmNetworkWatcherResourceTroubleshooting` parancsmaggal hibaelhárításához kapcsolat állapota
- Ütemezés kapcsolása a forgatókönyvhöz

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv megkezdése előtt rendelkeznie kell a következő előfeltételeknek:

- Egy Azure automation-fiók az Azure-ban. Győződjön meg arról, hogy az automation-fiók a legújabb modul van, és a AzureRM.Network modul is rendelkezik. A AzureRM.Network modul esetén érhető el, a modul gyűjteményben kell adja hozzá az automation-fiók.
- A hitelesítő adatok készletét konfigurálása az Azure Automationben kell rendelkeznie. További tudnivalókért olvassa el [Azure Automation szolgáltatásbeli biztonsági](../automation/automation-security-overview.md)
- Egy érvényes SMTP-kiszolgáló (Office 365, a helyszíni e-mail vagy egy másik) és az Azure Automationben hitelesítő adatokhoz
- A konfigurált virtuális hálózati átjáró az Azure-ban.
- A naplók tárolásához egy létező tárolóval meglévő tárfiókot.

> [!NOTE]
> Az infrastruktúra az előző ábrán kitaláltak illusztrációs célokat szolgálnak, és még nem jöttek létre ebben a cikkben található lépéseket.

### <a name="create-the-runbook"></a>Hozza létre a runbookot

Az első lépés a példa konfigurálására, hogy hozza létre a runbookot. A példa egy futtató fiókot. A futtató fiókokkal kapcsolatos további tudnivalókért keresse fel a [Runbookok hitelesítéséhez az Azure-beli futtató fiók](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>1. lépés

Keresse meg az Azure Automation a [Azure-portálon](https://portal.azure.com) kattintson **Runbookok**

![Automation-fiók – áttekintés][1]

### <a name="step-2"></a>2. lépés

Kattintson a **hozzáadása egy runbook** a runbook létrehozásának megkezdéséhez.

![runbookok panel][2]

### <a name="step-3"></a>3. lépés

A **Gyorslétrehozás**, kattintson a **hozzon létre egy új runbookot** hozza létre a runbookot.

![egy runbook panel hozzáadása][3]

### <a name="step-4"></a>4. lépés

Ebben a lépésben, amelyben tudatjuk a felhasználókkal a runbook nevét, a példában szereplő nevezik **Get-VPNGatewayStatus**. Ez nem lényeges, hogy a runbook egy leíró nevet, és adjon neki egy nevet, amely PowerShell szabványos elnevezési szabályai a következő ajánlott. A runbook típusa ehhez a példához **PowerShell**, a többi beállítást a rendszer grafikus PowerShell-munkafolyamati és grafikus PowerShell-munkafolyamat.

![runbook panel][4]

### <a name="step-5"></a>5. lépés

Ebben a lépésben a runbook jön létre az alábbi példakód biztosít a példában a szükséges összes kódot. Az elemek, amelyek tartalmazzák a kódban \<érték\> kell cserélni az értékeket az előfizetésből.

Kattintson az alábbi kód használata **mentése**

```PowerShell
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

A forgatókönyv mentése, után ütemezés össze kell kapcsolni, hogy automatizálja a runbook elindítása. A folyamat elindításához kattintson **ütemezés**.

![6. lépés][6]

## <a name="link-a-schedule-to-the-runbook"></a>Ütemezés kapcsolása a forgatókönyvhöz

Létre kell hozni egy új ütemezést. Kattintson a **ütemezés kapcsolása a forgatókönyvhöz**.

![7. lépés][7]

### <a name="step-1"></a>1. lépés

Az a **ütemezés** paneljén kattintson **új ütemezés létrehozása**

![8. lépés][8]

### <a name="step-2"></a>2. lépés

Az a **új ütemezés** panel kitöltése során az ütemezési információkat. A beállítható értékei a következők:

- **Név** -ütemezés rövid nevét.
- **Leírás** -ütemezés leírását.
- **Elindul** -e érték dátum, idő és időzóna alkotó az idő az ütemezés eseményindítók kombinációja.
- **Ismétlődés** -Ez az érték határozza meg az ütemezés ismétlési.  Érvényes értékek a következők **egyszer** vagy **ismétlődő**.
- **Ismétlődik minden** -az ismétlődési ütemezés az óra, nap, hét és hónap.
- **Beállíthatja a lejárati idejét** -az érték szabja meg, ha az ütemezés lejárati vagy nem. Megadható **Igen** vagy **nem**. Érvényes dátumot és időpontot meg kell adni, ha az Igen választása vannak.

> [!NOTE]
> Egy runbook minden órában gyakoribb futtatásához rendelkeznie kell, ha egyszerre több ütemezés léteznie kell más időközönként (Ez azt jelenti, 15, 30, az óra 45 perc)

![9. lépés][9]

### <a name="step-3"></a>3. lépés

Kattintson a Mentés menteni az ütemezést a runbookhoz.

![10. lépés][10]

## <a name="next-steps"></a>További lépések

Most, hogy megismerhesse a integrálása az Azure Automation szolgáltatásban hibaelhárítási hálózati figyelőt, megtudhatja, hogyan csomag rögzíti a virtuális gép riasztásokat kiváltó ellátogatva [hozzon létre egy riasztási kiváltott csomagrögzítéssel Azure hálózati figyelőt](network-watcher-alert-triggered-packet-capture.md).

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
