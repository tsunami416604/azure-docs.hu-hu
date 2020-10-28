---
title: Hibrid gépek összekötése az Azure-ba a PowerShell használatával
description: Ebből a cikkből megtudhatja, hogyan telepítheti az ügynököt, és hogyan csatlakoztatható egy gép az Azure-hoz az Azure arc-kompatibilis kiszolgálók használatával a PowerShell használatával.
ms.date: 10/27/2020
ms.topic: conceptual
ms.openlocfilehash: bb114ec3e279a7ea696d834af8eb7240cb892dc1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891941"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Hibrid gépek összekötése az Azure-ba a PowerShell használatával

A környezetben egy vagy több Windows-vagy Linux-gépen engedélyezheti az Azure arc-kompatibilis kiszolgálókat, ha manuális lépéseket hajt végre. Vagy a [Connect-AzConnectedMachine PowerShell-](/powershell/module/az.connectedmachine/remove-azconnectedmachine) parancsmag használatával letöltheti a csatlakoztatott gépi ügynököt, telepítheti az ügynököt, és regisztrálhatja a gépet az Azure arc segítségével. A parancsmag letölti a Windows Agent Windows Installer csomagot a Microsoft letöltőközpontból, és a Linux-ügynök csomagját a Microsoft Package adattárból.

Ehhez a módszerhez rendszergazdai jogosultságokkal kell rendelkeznie a gépen az ügynök telepítéséhez és konfigurálásához. Linux rendszeren a legfelső szintű fiók, a Windows rendszeren pedig a helyi Rendszergazdák csoport tagja. Ez a folyamat interaktív módon vagy távolról is elvégezhető a Windows Serveren a [PowerShell távelérés](/powershell/scripting/learn/ps101/08-powershell-remoting)használatával.

Mielőtt elkezdené, tekintse át az [előfeltételeket](agent-overview.md#prerequisites) , és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek. A támogatott régiókkal és az egyéb kapcsolódó megfontolásokkal kapcsolatos információkért lásd: [támogatott Azure-régiók](overview.md#supported-regions).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

- Azure PowerShell-t futtató számítógép. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).

Mielőtt a Azure PowerShell használatával kezelhesse a virtuálisgép-bővítményeket az arc-kompatibilis kiszolgálók által felügyelt hibrid kiszolgálón, telepítenie kell a `Az.ConnectedMachine` modult. Futtassa a következő parancsot az ív használatára képes kiszolgálón:

```powershell
Install-Module -Name Az.ConnectedMachine
```

A telepítés befejeztével a következő üzenet jelenik meg:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Az ügynök telepítése és az Azure-hoz való kapcsolódás

1. Nyisson meg egy PowerShell-konzolt emelt szintű jogosultságokkal.

2. Jelentkezzen be az Azure-ba a parancs futtatásával `Connect-AzAccount` .

3. A csatlakoztatott számítógép ügynökének telepítéséhez használja `Connect-AzConnectedMachine` a, a `-Name` `-ResourceGroupName` és a `-Location` paramétereket. A `-SubscriptionId` paraméter használatával felülbírálhatja az alapértelmezett előfizetést a bejelentkezés után létrehozott Azure-környezet eredményeképpen. Futtassa a következő parancsok egyikét:

    * Az Azure-hoz közvetlenül kommunikáló célszámítógépen található csatlakoztatott számítógép-ügynök telepítéséhez futtassa a következőt:

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    * A csatlakoztatott számítógép ügynökének a proxykiszolgálón keresztül kommunikáló célszámítógépen való telepítéséhez futtassa a következőt:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

Ha a telepítés befejezése után az ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. Windows on *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log* és Linux rendszeren a */var/opt/azcmagent/log/himds.log* címen.

## <a name="install-and-connect-using-powershell-remoting"></a>Telepítés és kapcsolat a PowerShell-távelérés használatával

A cél Windows-kiszolgáló vagy-gép Azure arc-kompatibilis kiszolgálókhoz való konfigurálásához hajtsa végre a következő lépéseket. A távoli számítógépen engedélyezni kell a PowerShell távelérését. Használja a `Enable-PSRemoting` parancsmagot a PowerShell távelérésének engedélyezéséhez.

1. Nyisson meg egy PowerShell-konzolt rendszergazdaként.

2. Jelentkezzen be az Azure-ba a parancs futtatásával `Connect-AzAccount` .

3. A csatlakoztatott számítógép ügynökének telepítéséhez használja `Connect-AzConnectedMachine` a, a `-Name` `-ResourceGroupName` és a `-Location` paramétereket. A `-SubscriptionId` paraméter használatával felülbírálhatja az alapértelmezett előfizetést a bejelentkezés után létrehozott Azure-környezet eredményeképpen.

A következő parancs futtatásával telepítheti a csatlakoztatott gépi ügynököt a célszámítógépen, amely közvetlenül tud kommunikálni az Azure-ban:

```azurepowershell
$session = Connect-PSSession -ComputerName myMachineName
Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
```

A következő példa a parancs eredménye:

```azurepowershell
time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941

Name           Location OSName   Status     ProvisioningState
----           -------- ------   ------     -----------------
myMachineName  eastus   windows  Connected  Succeeded
```

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure Arc csatlakozásának ellenőrzése

Miután telepítette az ügynököt, és konfigurálta az Azure arc-kompatibilis kiszolgálókhoz való csatlakozáshoz, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. A gépet megtekintheti az [Azure Portalon](https://portal.azure.com).

![Sikeres kiszolgálói kapcsolatok](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.

* Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/insights/vminsights-enable-policy.md)és sok más további műveletet.

* További információ a [log Analytics-ügynökről](../../azure-monitor/platform/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelésére szolgáló adatokat szeretné összegyűjteni, az Automation-runbookok vagy-szolgáltatásokkal, például a Update Managementekkel, vagy más Azure-szolgáltatások, például a [Azure Security Center](../../security-center/security-center-introduction.md)használatával